# Classes

Per-class notes live in [`classes/`](classes/).

- `0x009AA224`  **CSWGuiMainCharGen::vftable**: Seems to be the class for character creation
- `0x0099C460`  **CResGFF::vftable**: Generic File Format (GFF) loader used to parse and provide access to resources like UTC, UTI, ARE, etc
- `0x0098B5CC`  **Gob::vftable**: Base game object class used to represent in-world entities. Contains a wide range of virtual functions for lifecycle management, serialization, rendering, and asset loading. 
- `0x009a7a74`  **CSWGuiInGameAreaTransition::vftable**: Gui for area transition display during loading screen
- `0x0099493c`  **CSWSModule::vftable::vftable**: Module class
- `0x00992398`  **CServerExoApp::vftable**: The virtual function table for the internal game server. This class manages the world simulation "heartbeat," background task scheduling via the Microsoft Concurrency Runtime, and serves as the primary owner of the world logic that receives and processes network packets from the client.
