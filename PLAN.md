# AR Card Battle: project plan (Sharon & Ohad, Make:ID)

## 1. Concept
Physical character cards come alive on the phone. Scanning a card makes its character pop out as a pixel/voxel 3D figure that roams the table. Two players share **one phone** (pass-and-play): each owns a card, and they fight in alternating turns. Players affect the fight with their hand and with real objects. **The Chelsea card holds a duo**, the big fox and the little fox, who pop out together. Once a character is out, the card becomes its live status panel (3 hearts + power meter). On KO the character returns to the card.

## 2. Tech approach (recommended)
| Need | Approach |
|---|---|
| Engine | **Unity + AR Foundation** (ARKit on iOS, ARCore on Android), one codebase |
| Card scanning | AR Foundation **Image Tracking** with a reference image library (one image per card). Cards need high-contrast, non-symmetric art; the current cards are good candidates. Vuforia is a fallback if tracking proves weak. |
| Characters on the table | Plane detection + anchors, so characters walk off the card onto the surface |
| Card UI after pop-out | World-space canvas locked to the tracked card image: 3 hearts + power bar, replacing the art (overlay mask over the card print) |
| Arm raise = "FIGHT" | Simplest reliable: **ML hand/body detection** (e.g. MediaPipe Hands/Pose via a Unity plugin) and a trigger when the hand crosses an upward threshold between the characters. Fallback: tap/swipe up. |
| Objects as obstacles | AR **depth/occlusion** (ARKit LiDAR meshing or ARCore Depth API) to detect anything new between fighters, then NavMesh rebuild so characters path around it. Fallback: small printed "obstacle cards" tracked as images. |
| Characters | Voxel models (MagicaVoxel → Blender for rigging → Unity). Matches the "pixelated" look and keeps poly count low. |
| KO effect | Shader dissolve (pixel-by-pixel fade) + translucent ghost copy rising with particles, then the card art returns |

## 3. Gameplay flow
1. **Scan card 1** → character pops out (bounce), sniffs and looks around (idle-explore loop).
2. **Scan card 2** → second character (or the fox duo) pops out and scares the first. The first one jumps and shakes, and both walk to battle positions facing each other.
3. **Arm raise** → player's arm enters the frame between them; when it rises, "FIGHT!" appears and fades (racing-flag moment).
4. **Turn-based fight (shared phone)** → a "Player 1 / Player 2" banner shows whose turn it is. The active player taps normal / special / duck, then hands the phone over. On the Chelsea side the player also picks **which fox acts** (big = strong, little = quick). Damage and power update on the cards.
5. **Intervention** → player places real objects between them. Characters must path around obstacles to reach the rival, so obstacles cost them time/turns.
6. **KO** → loser dissolves Mortal Kombat style, the ghost rises and fades, and the character reappears printed on its card.

## 4. Moves
| Character | Normal | Special (uses power) | Duck / evade |
|---|---|---|---|
| **Mark** (croc, Heal card, 7★, ATK 5, Heal +4, Shield 3) | Bite | Tail whip | Dives under the "surface" and stalks, only eyes visible |
| **Chelsea** (big fox, Attack card, 5★, ATK 14, Shield 3) | Sound waves | Fire breath | Fast run + jump |
| **Little fox** (unevolved, same card as Chelsea) | Small sound waves | Sparks (weak fire) | Very quick dodge (highest evade chance) |

Duo balance: the two foxes share the Chelsea card's 3 hearts and power meter, so 2 bodies don't mean double health. The little fox's moves are weaker but cheaper and harder to hit. Other suggested hooks: special costs a full power bar; ducking blocks one hit but skips the attack; Mark's Heal +4 = restore a heart (or part of one). Card stats (★, ATK, Shield) map directly to damage and defense.

## 5. Card UI (after pop-out)
- 3 hearts at the top of the card area, emptying left to right
- Power meter bar that fills each turn and on dodges, and empties on a special
- The card's existing color stripe (green / yellow) kept as frame so the card stays recognizable
- Flashes red on hit, glows when the special is ready

**Shared-phone UX:** big turn banner, move buttons at thumb height, a short pause between turns so the phone can change hands without losing tracking.

## 6. Risks
| Risk | Mitigation |
|---|---|
| Image tracking is lost when hands/objects cover the card | Anchor characters to the plane, not to the card, after pop-out |
| Arm-raise detection is unreliable in bad light | Tap fallback; test early |
| Real-object avoidance is the hardest feature (needs depth) | Start with obstacle cards; real objects only on LiDAR phones |
| Rigging voxel characters takes time | Few, punchy animations; squash/stretch over complex rigs |
| Device split (iOS/Android) | Pick one test phone first |
| Phone handover shakes the camera and breaks the scene | Characters anchored to the table plane; brief relocalize pause at turn start |

## 7. Milestones (two people, estimates in work hours)
| # | Milestone | Hours |
|---|---|---|
| 1 | Final character art lock + voxel models (Mark + fox duo) | 10–14 |
| 2 | Unity AR setup, scan one card → static model appears | 6–8 |
| 3 | Rig + animations: pop-out, idle/sniff, 3 moves each, hit, KO (×3) | 18–25 |
| 4 | Two-card scene: scare reaction, battle positions | 5–7 |
| 5 | Turn system, hearts/power UI on cards | 8–10 |
| 6 | FIGHT trigger (hand detection + tap fallback) | 6–10 |
| 7 | Obstacles (obstacle cards first, depth later if time allows) | 8–15 |
| 8 | KO dissolve + ghost + return to card | 4–6 |
| 9 | Sound, VFX polish, playtest with 3–5 users | 8–10 |
| 10 | Presentation: video capture, process board | 6–8 |
| | **Total** | **≈ 80–115 h** (≈ 5–7 weeks at ~8 h/week each) |

Keep a 1-week buffer before critique. A working **vertical slice (milestones 1–5, one character pair)** is the safest thing to show at a mid-critique.

## 8. 3D characters
`characters-3d.html` (same folder) is a voxel preview of Mark and the fox duo (both on the Chelsea card) built from the card colors, with orbit, idle animation and a pop-out-of-card replay. It works as a style reference for the final MagicaVoxel models.
