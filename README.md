# Fortress Fight: UI and Screen Flow

Menus, pause system and loading screen for **Fortress Fight**, a 2D C++ game made by a team of five
(Software Engineering Project 2, Singapore Institute of Technology, Jan–Mar 2026). I was the UI/UX Lead.

**Stack:** C++ · Alpha Engine (DigiPen's C++ game framework) · Visual Studio · Git

> The team code lives in a private school repository. This page explains what I built and what I learned.

<img width="797" height="449" alt="image" src="https://github.com/user-attachments/assets/3e00bd80-3633-47bc-be55-3492fdcb5219" />
<img width="798" height="452" alt="image" src="https://github.com/user-attachments/assets/e01eb1f1-7aa8-4889-bf9a-a69a4c53c553" />
<img width="793" height="444" alt="image" src="https://github.com/user-attachments/assets/d93e54e1-8f25-4fbf-8133-141534d6a549" />

## The problem
The game started as a single level with no way in or out. It needed a main menu, settings, credits, a pause menu and loading screens, and a clean way to move between them, while four teammates kept changing gameplay code at the same time.

## The constraints
- **A fixed framework.** Alpha Engine gives you drawing, input and timing, but no UI system, no scene manager and no built-in animation.
- **A fixed deadline.** One trimester, with milestones along the way.
- **Shared code.** The game loop and the level code belonged to the whole team, so my screens had to plug in without breaking anyone else's work.

## What I built
- **A screen manager.** Every screen (main menu, settings, credits, levels) provides the same six functions: Load, Initialize, Update, Draw, Free and Unload. The manager swaps between them using function pointers, so adding a new screen doesn't touch the main loop.
- **Five screens:** main menu, settings, pause, credits and an animated loading screen.
- **A pause system.** Esc pauses only during gameplay. It sets the frame time step (delta time) to zero, which freezes the game, while still drawing the level under a dimmed overlay with Resume, Settings and Back to Menu buttons.
- **An animated loading screen.** A progress bar, animated dots and a 6-frame running character. Alpha Engine has no animation support, so I built a quad mesh whose texture (UV) coordinates cover exactly one frame of the image strip, and shifted the texture by one frame at a time.
- **A settings screen** with a draggable volume slider and ±10% buttons, connected to the game's audio manager.

## What I tested
I playtested every route between screens (menu → level → pause → settings → back) after each change, and with teammates and playtesters before each milestone.

## What failed, and what I changed
| What went wrong | Why | What I changed |
|---|---|---|
| Pressing Esc to pause also quit the game | Esc was still wired to "quit" in the main loop | Removed the global Esc-to-quit, so Esc means pause during gameplay |
| The pause menu opened and closed instantly | Both the pause menu and the screen manager checked for Esc in the same frame | Moved the Esc check into one place, the screen manager |
| Clicking Resume fired a shot | The same mouse click reached the game the moment it unpaused | Added a 0.15 s input cooldown after Resume |
| The loading animation froze | It used the game's time step, which is zero while paused | Used the real frame time for the loading screen |
| Opening Settings from pause lost the pause state | Settings is a separate screen, so the level restarted without pausing | Added a "return to pause" flag so the pause menu reopens after Settings |
| Quitting showed a loading screen first | Quit went through the normal screen transition | Quit now skips the transition and closes straight away |

## What happened
- All five screens shipped in the final build, and teammates added new screens (such as Settings and a second level) by plugging into the screen manager without changing the main loop.
- Pausing, resuming and visiting Settings mid-game work without losing the player's progress.
