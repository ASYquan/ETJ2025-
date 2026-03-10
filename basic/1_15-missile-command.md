# 1.15: Missile Command

## Description

A Unity game you're supposed to win. The game is rigged so you can't.

## Solution

Ran the game first to understand it. Missiles come in, you try to shoot them down, you always lose. The "Fail" screen comes up and there's no flag anywhere.

Unity games compile their C# game logic into a DLL at `<game>_Data/Managed/Assembly-CSharp.dll`. Opened it with dnSpy:

```
Game_Data/Managed/Assembly-CSharp.dll
```

Searched for "Fail" and "Win" in the class browser. Found the game manager class that handles the end-of-game state. The function that triggered on losing looked something like:

```csharp
void OnGameOver() {
    SceneManager.LoadScene("Fail");
}
```

In dnSpy I right-clicked the method and chose "Edit Method (C#)". Changed it to:

```csharp
void OnGameOver() {
    SceneManager.LoadScene("Win");
}
```

Compiled the change and saved the DLL. Launched the game again, intentionally let everything through, and instead of the fail screen the win scene loaded with the flag displayed.

Flag: `6ea6124fbda3e823e23038ea707e030c`

You don't need to win the game if you can rewrite the rules.
