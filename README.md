# Free Fall

A short Unreal Engine 5 level made for CA1 (game development). The player walks through a cargo plane, triggers a cutscene, picks one of two jump points, and then skydives after a falling bag. Catching the bag plays the ending cutscene.

The project is Blueprint only. There is no `Source` folder, so there is nothing to compile.

## Requirements

- Unreal Engine 5.6 (`EngineAssociation` in `Zenith_Demo.uproject`)
- Windows with a DirectX 12 capable GPU. The config sets DX12 as the default RHI, shader model 6 and ray tracing on, with Lumen (dynamic GI and reflections) and virtual shadow maps.
- Plugins listed in the project file: Modeling Tools Editor Mode (editor only) and Movie Render Pipeline.
- Disk space: the working tree is about 3.7 GB and the `.git` folder about 2.8 GB. See "Repo contents" below for why.

## Opening it

1. Clone the repo.
2. Double-click `Zenith_Demo.uproject`, or open it from the Epic Games Launcher / Unreal Project Browser. Let it compile shaders on first open, which takes a while.
3. Open `Content/Free_Fall.umap` and press Play.

Step 3 is not optional. `Config/DefaultEngine.ini` still has `GameDefaultMap=/Engine/Maps/Templates/OpenWorld`, so the project does not start in Free_Fall by itself, and a packaged build would load the wrong map until that setting is changed. The global game mode is `BP_ThirdPersonGameMode`.

## How the level plays

Summary of how the level is set up, based on the assets and config in the repo.

1. Walk into a trigger volume to start the opening cutscene (`Cutscenes/Opening/LS_OpeningCutscene`). There is also a cargo bay sequence (`LS_Cargo_bay`) and NPCs (`NPC/BP_NPC`) with five voice line assets.
2. After the cutscene a UMG widget (`UI/WBP_SpawnChoice`) pauses the game and asks for "Spawn Location A" or "Spawn Location B".
3. The character is moved to the chosen spot and goes into a skydiving state: the movement component is set to Flying with gravity still on. `bIsSkydiving` on `BP_ThirdPersonCharacter` switches between ground and air logic, and the animation blueprint reads it too (`Flying_Anim` is in `Animation/New`).
4. Steer with mouse look and WASD. Looking down and holding W dives faster. `UI/WBP_Altitude` shows the character's Z position.
5. `Backpack_goal/BP_Bag` is a physics-simulated mesh that falls from the sky. Overlapping it plays `LS_ending`, disables input and shows a "Level Complete!" text widget from a UMG track in the sequence.
6. Looping music is started from the level blueprint when the opening cutscene finishes.

Input uses Enhanced Input (`Content/Input`): `IA_Move`, `IA_Look`, `IA_MouseLook`, `IA_Jump`, `IA_Sprint`, `IA_Interact` and `IA_SkipCutscene`, with `IMC_Default` and `IMC_MouseLook` as the mapping contexts. `DefaultInput.ini` also has legacy Jump (Space, gamepad bottom face button) and Interact (E) mappings. The exact keys for sprint and skipping cutscenes are set inside the mapping context assets, so they aren't listed here.

## Project structure

Only the folders that are mine or that matter are listed.

- `Content/Free_Fall.umap`: the main level. `__ExternalActors__` and `__ExternalObjects__` are the One File Per Actor data for it and the other maps.
- `Content/Blueprints`: `BP_MyGameMode`, `BP_SWAT_Character`, `BP_OnInteract`, `CS_Turbulence` (camera shake, going by the prefix)
- `Content/ThirdPerson/Blueprints`: character, player controller and game mode from the Third Person template, which carry the skydiving logic
- `Content/Cutscenes`: Level Sequences for the opening, cargo bay and ending
- `Content/UI`: spawn choice, altitude and interaction prompt widgets, plus the Bebas Neue font
- `Content/Input`: Enhanced Input actions and mapping contexts
- `Content/NPC`, `Content/Backpack_goal`, `Content/Niagara` (`NS_WingVortex`, used for the plane contrails and wing vortex effects), `Content/Audio`
- `Content/Animation`: retargeted and imported animations, `ABP_SWAT` animation blueprint, an IK retargeter
- `Content/C17_Environment` and `Content/Models`: the C-17 interior and exterior, imported from glTF/FBX, plus parachute, military bag and missile tank props

Third-party content that came in as packs or from Fab: `UltraDynamicSky` (with its own demo map), `QuantumCharacter` (with its own demo map), `FreeAnimationLibrary` (with its own demo map), `Fab` (Megascans surface, a S.W.A.T. operator, an FSB operator, an asphalt texture and a steel plate material), and the Epic template folders `Characters/Mannequins` and `LevelPrototyping`. The pack demo maps are still in the repo: `UltraDynamicSky/Maps/DemoMap`, `QuantumCharacter/Map/Map`, `FreeAnimationLibrary/Demo/Map/DemoMap` and `ThirdPerson/Lvl_ThirdPerson`.

## History

14 commits by AdamPandey, 2025-10-11 to 2025-10-24. Roughly: sky and lighting (V1), C-17 environment (V2), character and animation setup (V3), cutscenes and NPC voice lines (V4), audio and the bag (V5), Niagara contrails (V6), animation blueprint clean-up (V7), then V8.0 as the final version and V8.1 for the readme. The commit messages note which versions had errors on clone. V8.0 is the one to use.

## Repo contents and known problems

- There is no `.gitignore`. `Saved/` (2,079 files), `Intermediate/` (7 files) and `DerivedDataCache/` (6 files) are all committed. `Saved` holds autosaves, crash dumps (`Saved/Crashes`, including a minidump), logs, a browser webcache, shader debug info, `.tmp` files and rendered cutscene frames (`Saved/MovieRenders`). Together with the content packs this accounts for the 2.8 GB `.git` size (Saved alone is about 1 GB on disk). Adding a `.gitignore` for those three folders (plus `Binaries/` and `Build/`) and removing them from the index would be the first cleanup.
- There is an empty file named `git` in the repo root, committed by accident.
- Large binaries are stored as plain git objects. There are no Git LFS rules in `.gitattributes` (it only has `* text=auto`).
- The audio folder contains files named after a film theme track and a C-17 takeoff recording. Those are third-party recordings, so check the licensing before redistributing the project or a build.
- The default map setting described above still points at the OpenWorld template.
- No packaged build is included.
