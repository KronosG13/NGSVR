# NGSVR Roadmap

This roadmap outlines the planned development path for NGSVR. Milestones are intentionally scoped to be achievable and demonstrable — each one should produce something you can see and show.

Scope and priorities will shift as contributors join and community feedback comes in. This is a living document.

---

## Milestone 1 — Proof of Life
> *Goal: Prove that multiplayer social VR presence is achievable in UE5.*

- [ ] UE5 project setup with OpenXR / SteamVR support
- [ ] Basic VR pawn (head + hands tracked)
- [ ] Desktop mode (non-VR) support
- [ ] Two or more players in a shared space (listen server)
- [ ] Player position/rotation replicated over network
- [ ] Spatial voice via Vivox
- [ ] Basic placeholder avatar (capsule or mannequin)
- [ ] Public GitHub repo + Discord open

**Target:** Demonstrable two-player VR session with voice. Postable demo video.

---

## Milestone 2 — You Look Like Someone
> *Goal: Replace placeholder avatars with real user avatars.*

- [ ] Runtime VRM avatar loading (local player)
- [ ] Avatar IK — 3-point (head + hands)
- [ ] Avatar IK — 6-point (+ hips + feet, FBT)
- [ ] Avatars visible and replicated to other players
- [ ] Basic avatar selection / loading UI
- [ ] SlimeVR / SteamVR tracker input mapped to IK bones

**Target:** Walk into a room as your own avatar with full body tracking.

---

## Milestone 3 — It Feels Social
> *Goal: Add the social layer that makes it a platform, not just a tech demo.*

- [ ] Instance system (create, list, join rooms)
- [ ] Player name tags
- [ ] Text chat (local / instance)
- [ ] Basic friend / player list
- [ ] Mute / block controls
- [ ] Basic UI framework (menus, HUD)

**Target:** Strangers can find each other, join the same room, and have a conversation.

---

## Milestone 4 — Expressive Avatars
> *Goal: Add face and eye tracking for supported hardware.*

- [ ] OpenXR face tracking extension integration
- [ ] Eye tracking (gaze, blink)
- [ ] Lower face tracking (jaw, lips, cheeks)
- [ ] VRM blend shape mapping for face tracking inputs
- [ ] Replicated face expressions to other players

**Target:** Quest Pro / VIVE Focus / Pico 4 Pro users have fully expressive avatars.

---

## Milestone 5 — User Worlds
> *Goal: Let the community build and share spaces.*

- [ ] Runtime world/map loading (glTF/GLB format)
- [ ] World hosting / joining system
- [ ] Basic world safety sandboxing
- [ ] World portal / travel system
- [ ] Community world repository (basic)

**Target:** Load a community-created world and explore it with friends.

---

## Post-POC / Future
> *These are ideas on the horizon, not commitments.*

- Avatar safety features (fallback avatars, shields)
- World scripting (safe, sandboxed)
- Economy / creator support layer
- Linux support (via Proton investigation)
- Dedicated server infrastructure
- Mobile spectator mode
- SDK / tooling for world and avatar creators
- Moderation tooling for instance hosts

---

*Last updated: 2026*
