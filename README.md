# StreamingLevelBug
Demonstrates issue travelling to streaming level with world composition in PIE

To Repro (UE4.26):
1. Open ProjectSettings > Maps & Modes
2. Make sure Default GameMode is set to GameMode_BP
3. Make sure Player Controller Class is set to Test_PC and Default Pawn Class is set to ThirdPersonCharacter
4. Open the ThirdPersonExampleMap
5. Go to Editor Preferences > Play
6. Set Play Net Mode to Client
7. Check "Launch Separate Server"
8. Make sure "Run Under One Process" is unchecked
9. Return to the map and hit play with 1 client. A PIE window and dedicated server will launch and connect
10. Press Z and wait a few seconds
11. Observer character falling in place
12. Exit, then play again with 2 clients
13. Wait a few seconds and observer second client properly loads into level but first does not


The issue is in
UE_4.26\Engine\Source\Runtime\Engine\Private\UnrealEngine.cpp
 line 12926
 
 `else if (WorldContext.PIEInstance != -1 && ((Pending == nullptr) || (Pending->GetDemoNetDriver() != nullptr)))`
 
 I believe the line should be 
 
 `else if (WorldContext.PIEInstance != -1 && ((Pending == nullptr) || (Pending->GetNetDriver() != nullptr) || (Pending->GetDemoNetDriver() != nullptr)))`
 
 Doing that in a source build of the engine works
