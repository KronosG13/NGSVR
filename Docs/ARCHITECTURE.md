# NGSVR Architecture

This document describes the high-level architecture of NGSVR. It is a living document - as systems are built and decisions are made, this will be updated to reflect the actual state of the codebase.

> ⚠️ This is the intended architecture for the POC phase. Not all systems described here exist yet.

---

## Overview

NGSVR is built on Unreal Engine 5 and follows a client-server multiplayer model using Epic Online Services (EOS) for session management and networking. The application runs in two modes - Desktop and VR - with the VR layer handled entirely through OpenXR, making it headset-agnostic.

```
┌─────────────────────────────────────────────────────────┐
│                        NGSVR Client                     │
│                                                         │
│  ┌─────────────┐   ┌──────────────┐   ┌─────────────┐  │
│  │  VR / Input │   │ Avatar System│   │ World System │  │
│  │   Layer     │   │  (IK + VRM)  │   │ (glTF/GLB)  │  │
│  └──────┬──────┘   └──────┬───────┘   └──────┬──────┘  │
│         │                 │                   │         │
│  ┌──────▼─────────────────▼───────────────────▼──────┐  │
│  │                   Game Layer (UE5)                 │  │
│  │        GameMode / GameState / PlayerController     │  │
│  └──────────────────────────┬─────────────────────── ┘  │
│                             │                           │
│  ┌──────────────────────────▼────────────────────────┐  │
│  │              Networking Layer (EOS + UE Replication│  │
│  └──────────────────────────┬─────────────────────── ┘  │
│                             │                           │
│  ┌──────────────┐  ┌────────▼────────┐                  │
│  │  Vivox Voice │  │  EOS Sessions   │                  │
│  └──────────────┘  └─────────────────┘                  │
└─────────────────────────────────────────────────────────┘
```

---

## Core Systems

### 1. VR / Input Layer

Handles all hardware input and abstracts it from the rest of the engine.

- **Runtime:** OpenXR (via UE5's built-in OpenXR plugin)
- **Supported runtimes:** SteamVR, Meta Air Link, Virtual Desktop
- **Input sources:**
  - HMD pose (head position + rotation)
  - Controller poses (left/right hand)
  - SteamVR tracker poses (hips, feet, elbows - FBT)
  - OpenXR face tracking extension (jaw, lips, cheeks, eyes - for supported HMDs)
- **Desktop fallback:** Mouse + keyboard with a free-look camera pawn

The input layer feeds directly into the Avatar System's IK solver each tick.

---

### 2. Avatar System

Responsible for loading, displaying, and animating user avatars at runtime.

**Avatar format:** VRM (open standard, widely supported in the VTuber and social VR space)

**Pipeline:**

```
User selects VRM file
        ↓
Runtime VRM importer (third-party UE5 plugin)
        ↓
Skeleton mapped to NGSVR rig definition
        ↓
IK Rig (UE5 IK Retargeter) applied
        ↓
Avatar rendered locally + replicated to other clients
```

**IK Modes:**
- **3-point IK** - Head + hands (minimum, works with any headset)
- **6-point IK** - Head + hands + hips + feet (requires FBT trackers)
- **Face tracking** - Blend shapes driven by OpenXR face tracking extension data

**Replication:** Avatar transform (head, hands, trackers) and blend shape weights are replicated to all players in the instance. Remote avatars are driven by replicated data, not local IK.

---

### 3. World System

Handles loading and presenting user-created environments at runtime.

**World format:** glTF 2.0 / GLB

- Worlds are loaded as runtime assets - not cooked into the game
- A base persistent level is always loaded (handles game logic, UI, networking)
- User worlds are streamed in as additive levels or as runtime-spawned static meshes
- Collision, lighting, and basic interaction are derived from the glTF scene data

**World sources (planned):**
- Local file (for testing and self-hosted instances)
- Community world repository (future)

**Safety considerations:**
- No arbitrary code execution from world files
- Physics and interaction are controlled by the NGSVR game layer, not world content
- World content is geometry + materials only

---

### 4. Networking Layer

NGSVR uses UE5's built-in replication system over Epic Online Services (EOS).

**Session types:**
- **Listen server** - One player hosts, others connect. Used for early POC and small groups.
- **Dedicated server** - Planned for later. Allows self-hosted instances with no host player required.

**What is replicated:**
- Player pawn transforms (head, hands, tracker positions)
- Avatar blend shape weights (face tracking, visemes)
- Player metadata (display name, avatar ID)
- Instance state (player list, world info)

**NAT traversal:** Handled by EOS, which includes relay fallback for difficult network configurations.

---

### 5. Voice - Vivox

Spatial voice is handled by Vivox, which is integrated into UE5 via the Online Subsystem Vivox plugin.

- **Positional audio** - Voice volume and direction based on in-world position
- **Instance-scoped** - Voice channels are tied to the current instance
- **Mute/block** - Per-player mute controls planned for M3

Vivox is free for UE5 projects up to a generous usage threshold, making it suitable for a community-run open source project.

---

## Folder Structure (Unreal Project)

```
Source/NGSVR/
├── Core/
│   ├── NGSVRGameMode.cpp/.h
│   ├── NGSVRGameState.cpp/.h
│   └── NGSVRPlayerController.cpp/.h
├── Avatar/
│   ├── AvatarComponent.cpp/.h       ← Manages local avatar state
│   ├── AvatarReplicator.cpp/.h      ← Handles network replication
│   └── IKSolverComponent.cpp/.h     ← 3pt / 6pt IK logic
├── VR/
│   ├── VRPawn.cpp/.h                ← Main VR player pawn
│   ├── DesktopPawn.cpp/.h           ← Non-VR fallback pawn
│   └── TrackerInputComponent.cpp/.h ← SteamVR tracker mapping
├── World/
│   ├── WorldLoader.cpp/.h           ← Runtime glTF world loading
│   └── WorldManager.cpp/.h          ← World streaming / lifecycle
├── Network/
│   ├── EOSManager.cpp/.h            ← EOS session management
│   └── InstanceManager.cpp/.h       ← Create / join / leave instances
└── UI/
    ├── MainMenuWidget.cpp/.h
    └── HUDWidget.cpp/.h
```

---

## Key Third-Party Dependencies

| Dependency | Purpose | License |
|---|---|---|
| Epic Online Services (EOS) | Sessions, NAT, networking | Free (Epic) |
| Vivox | Spatial voice | Free tier (Epic) |
| VRM4U or similar | Runtime VRM loading in UE5 | MIT / Open |
| glTF runtime importer | Runtime world loading | Open source |
| OpenXR (UE5 plugin) | VR hardware abstraction | Built-in |
| SteamVR | Desktop VR runtime | Free (Valve) |

---

## Design Principles

**Runtime loading over cooked content.** Avatars and worlds are loaded at runtime from open formats. Nothing is baked into the shipped binary that users did not put there themselves.

**OpenXR first.** No vendor-specific SDK calls. If a feature requires a vendor extension (e.g. Meta face tracking), it is wrapped behind an interface so other implementations can be added later.

**Replication is authoritative on the server.** Client-side prediction is not in scope for the POC. The server is the source of truth for all player positions and instance state.

**Blueprints for prototyping, C++ for systems.** Early POC code may live in Blueprints. Stable systems get ported to C++ before being considered done.

---

## What Is Not In Scope (POC Phase)

- Dedicated server infrastructure
- World scripting or interactables
- Economy or creator monetization
- Linux / Mac support
- Standalone VR device support (Quest native, PSVR, etc.)
- Mobile spectator mode
- Anti-cheat

---

*Last updated: 2026*
