# 🎴 Elemental Runes 3D

**A high-performance, physics-driven 3D Card Battle & Strategy Engine built in Unity 6.**


  Key Features •  Architecture •  Turn Cycle •  Setup •  Controls •  Project Structure

---

> [!NOTE]
> **Elemental Runes 3D** combines traditional meld/set-evaluation mechanics with dynamic 3D card fan physics, realistic deck height stacking, and automated turn-phase state machines.

---

## ⚡ Key Features

| Feature | Description |
| :--- | :--- |
| 📐 **Dynamic 3D Hand Arc** | Cards fan out smoothly around a 3D pivot point with parabolic Y-height curves, rotational angles, and micro Z-offsets to eliminate Z-fighting. |
| 🎛️ **Turn Phase System** | Automated state machine handling **Draw Phase**, **Meld Phase**, and **Discard Phase** transitions between Player and AI turns. |
| 🎴 **Physics Deck Stacking** | Physical 3D representation of the 60-card deck and discard pile. Stack height adjusts dynamically as cards are drawn or discarded. |
| 🧠 **Decoupled Meld Evaluator** | `RamiEvaluator.cs` provides pure static logic validation for runs, sets, and elemental power calculations without scene dependencies. |
| 🖱️ **Raycast Drag & Drop** | World-space 3D card manipulation featuring hover-elevation, plane-restricted dragging, drop-zone detection, and smooth snap-back animations. |

---

## 🏗️ Game Architecture

The system follows a modular, decoupled architecture using Singleton Controllers and ScriptableObjects.

```
                  ┌────────────────────────┐
                  │     GameManager.cs     │
                  │ (Turn & Phase Logic)   │
                  └───────────┬────────────┘
                              │
       ┌──────────────────────┼──────────────────────┐
       ▼                      ▼                      ▼
┌──────────────┐      ┌──────────────┐      ┌──────────────┐
│ DeckManager  │      │HandController│      │   EnemyAI    │
│  (60 Cards)  │      │ (3D Hand Arc)│      │ (Bot Phase)  │
└──────┬───────┘      └──────┬───────┘      └──────────────┘
       │                     │
       ▼                     ▼
┌──────────────┐      ┌──────────────┐      ┌──────────────┐
│  CardData    │◄─────┤CardDragHandler─────►│RamiEvaluator │
│(ScriptableObj)      │ (3D Physics) │      │(Static Logic)│
└──────────────┘      └──────────────┘      └──────────────┘
```

> [!TIP]
> **Script Responsibility Overview:**
> * `DeckManager.cs`: Handles Fisher-Yates deck shuffling, physical 3D stack generation, and discard refilling.
> * `HandController.cs`: Controls hand layout, spacing curves, and arc angles in world space.
> * `CardDragHandler.cs`: Manages mouse raycasting, hover elevation, and drop collisions against `PlayArea` surfaces.
> * `RamiEvaluator.cs`: Evaluates legal melds and combination power totals.

---

## 🔄 Turn State Machine

```
               [ Game Start ]
                     │
                     ▼
             ┌───────────────┐
             │ Player Turn   │
             ├───────────────┤
             │ 1. Draw       │◄─── (Draw 1 card from deck stack)
             │ 2. Meld       │◄─── (Play valid combinations on table)
             │ 3. Discard    │◄─── (Drop 1 card to discard pile)
             └───────┬───────┘
                     │
                     ▼
             ┌───────────────┐
             │ Enemy Turn    │
             ├───────────────┤
             │ 1. Simulated  │◄─── (Thinking delay: 1.5s)
             │ 2. Draw/Play  │◄─── (AI draws & executes move)
             │ 3. Pass       │
             └───────┬───────┘
                     │
                     └───► [ Next Round ]
```

---

## 🎮 Controls & Interaction

| Input | Action | Behavior |
| :--- | :--- | :--- |
| **Mouse Hover** | Focus Card | Lifts card vertically and tilts it slightly closer to camera view. |
| **Click & Drag** | Move Card | Projects card along a 3D camera plane following cursor position. |
| **Drop on Table Area** | Play Meld | Validates meld via `RamiEvaluator` and locks card flat on `TablePlayArea`. |
| **Drop on Discard Pile**| End Phase | Sends card face-up to `Discard_Position` stack and advances turn to `EnemyAI`. |
| **Release Anywhere Else**| Cancel Drag | Smoothly interpolates card back into its designated slot in hand. |

---

## 🚀 Installation & Setup

> [!IMPORTANT]
> Requires **Unity 6 (6000.4.1f1)** or newer with **TextMeshPro** package installed.

1. **Clone the Repository:**
   ```bash
   git clone https://github.com/your-username/elemental-runes-3d.git
   ```

2. **Open Project in Unity:**
   * Launch **Unity Hub** $\rightarrow$ Click **Add** $\rightarrow$ Select project directory.
   * Open scene located at `Assets/Scenes/SampleScene.unity`.

3. **Configure Scene References:**
   * Locate the `GameManager` object in the Hierarchy.
   * Ensure `DeckSpawnTransform`, `DiscardPileTransform`, and `PlayerHandCenter` transforms are assigned in the Inspector.
   * Assign all 60 `CardData` ScriptableObjects into `DeckManager.allCardsDataList`.

4. **Run Game:**
   * Press **Play** in Editor.

---

## 📂 Project Directory Structure

```text
Assets/
├── 📁 CardsData/                 # 60 ScriptableObject card assets
│   ├── Elemental_Fire_01.asset
│   └── Elemental_Water_01.asset
├── 📁 Prefabs/                   # 3D Card Prefab with Colliders & Canvas UI
│   └── CardPrefab.prefab
├── 📁 Scripts/
│   ├── 📁 Controllers/           # Hand layout and mouse input
│   │   ├── HandController.cs
│   │   └── CardDragHandler.cs
│   ├── 📁 Logic/                 # Rule evaluation engine
│   │   └── RamiEvaluator.cs
│   └── 📁 Managers/              # Global game controllers
│       ├── DeckManager.cs
│       └── GameManager.cs
└── 📁 Scenes/
    └── SampleScene.unity
```

---

## 🛠️ Configuration Parameters

```csharp
// HandController.cs Configuration Highlights
public float cardSpacing = 1.2f;       // Horizontal gap between adjacent cards
public float arcHeight = 0.3f;         // Parabolic arch intensity
public float cardRotationAngle = 5.0f; // Angular tilt per hand index
public float depthOffset = -0.01f;     // Micro Z-depth step to fix Z-fighting
```

---

## 📋 Roadmap & Future Improvements

- [x] **Core Hand Mechanics:** 3D parabolic card fanning and Z-depth stacking.
- [x] **Deck & Discard Visuals:** Dynamic stack height rendering.
- [x] **Turn State Machine:** Player and Enemy AI turn cycles.
- [ ] **DOTween Integration:** Replace linear lerps with spring/smooth-damp easing curves.
- [ ] **Multiplayer Network Support:** Mirror / Unity Netcode for GameObjects backend setup.
- [ ] **Audio & FX System:** Card slide, draw, and place SFX triggering.

---

## 📄 License

Distributed under the **MIT License**. See `LICENSE` for details.
