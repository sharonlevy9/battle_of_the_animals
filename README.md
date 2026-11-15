# Battle of the Animals

Mobile AR card-battle prototype (Make:ID). Two players share one phone; Mark the crocodile fights Chelsea and the little fox.

- `index.html` is the playable game (card scanning is simulated by tapping).
- `characters-3d.html` is the voxel character viewer.
- `PLAN.md` is the project plan.
- `cards-targets.mind` is the compiled MindAR image-tracking file for the two cards, for the real camera version.

## Camera mode (iPhone / Android)
Open the GitHub Pages link over https in Safari, press "Use my camera", and allow the camera.

- Lay the two cards flat on a table, side by side, with about two card-widths between them. Keep the table above the cards in view and clear of objects.
- Cards are found with MindAR image tracking and scan automatically.
- Raise your hand between the characters (MediaPipe hand landmarks) to start the fight. The on-screen button is a fallback.
- The fight is automatic and happens between the two cards. Everyday objects you put on the table (cups, bottles, books, phones, toys and so on, 80 common kinds, from a MediaPipe object detector) are picked up and the characters path-find around them. A status line at the bottom shows which cards, hand and objects are currently detected.
