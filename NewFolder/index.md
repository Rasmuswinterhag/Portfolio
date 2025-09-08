<head>
  <title>NewFolder</title>
</head>
### [Back](..)
# *New Folder*

<img src="Images\Banner.png" width="100%"/>

[Itch Page](https://yrgo-game-creator.itch.io/new-folder)  

Worked as **Programmer**  
*2024 november - 2025 June*   
Language: c#    
Nominated to [Swedish Game Awards 2025](https://www.swedishgameawards.se/kopia-på-nominees-2024)

---

I worked on a little bit of everything but some highlights are: 
- The mediaplayer - I built almost the whole thing  
- The Glitch system - Built the whole thing, I built it to be easy to use for the developers
- Animated images -  I modified the normal Image prefab to add animations when you first open them, but not again after  
- Pulling keys from images - I modified the image prefab to make you able to pull keys from it

The glitch system was designed so you could easily customize it as a developer.
<details>
<summary>The glitch system viewed from unity</summary>
I wanted to make it so only the relevant values showed up for what you had selected, but unfortunatly i couldnt figure it out when it was in an array. So I settled for this.
<img src="Images\GlitchHandler.png" width="100%"/>
</details>
<details>
<summary>The glitch events in code</summary>
<pre>
<code>
[Serializable]
public class GlitchEvents
{
    public enum Trigger
    {
        FolderUnlocked,
        FolderOpend,
        WindowOpened,
        WindowClosed,
        SoundPlayed,
        iconBinned,
        ShutDown,
        EmailOpened,
        PinOpened,
        ImageAssembled,
        DoorKnocked
    }

    public enum Function
    {
        OpenWindow,
        SpamWindows,
        MoveWindow,
        StealWindow,
        ScatterIcons,
        ActivatePhysics,
        CreateFolder,
        WindowShake,
        ScreenGlitch,
        DebugLog
    }

    public enum ScatterType
    {
        DESKTOP,
        TRASHCAN,
        OPENED
    }

    [Tooltip("Make negative for unlimited uses")] public int triggerCount;
    [Tooltip("if the list is empty the glitch wont play a sound")] public AudioClip[] glitchSounds;

    [Header("----------------Trigger Settings----------------")]
    public Trigger trigger;

    [Header("Folder unlocked | Folder Opend | iconBinned | PinOpend")]
    public string iconToCheck;

    [Header("Window Opened | Window Closed | iconBinned | PinOpend")]
    public WindowData dataToCheckFor;

    [Header("Window Opened | PinOpend")] 
    public Icon.IconType iconType;

    [Header("Sound Played")]
    public AudioClip soundClip;

    [Header("Email")]
    public int emailId;


    [Header("----------------Function Settings----------------")]
    public Function function;

    [Header("Open Window")]
    public GameObject windowToOpen;
    public WindowData windowData;
    public Vector2 spawnPosition;

    [Header("Window Spam")]
    public int spamCount;
    public GameObject[] windowsToSpam;
    public WindowData[] windowSpamData;
    public float delayBetweenWindows = 0.1f;

    [Header("Move Window")]
    public float mouseSpeed = 10f;
    [Tooltip("Index of window to move, lower is higher up")] public int windowToTarget = 0;
    public Vector2 targetPosition;

    [Header("Window To Steal")]
    [Tooltip("Index of window to steal, lower is higher up")] public int windowToSteal = 0;

    [Header("Scatter Icons")]
    [Tooltip("What folder whill scatter all of its and its subfolders icons")] public ScatterType folderToScatter;

    [Header("Time for scatter, screen glitch and physics to activate")]
    public float totalGlitchTime;

    [Header("Create Folder")]
    public Vector2 folderPosition;
    public string folderName;

    [Header("WindowShake")]
    public int windowToShake;
    public float shakeIntensity;
    public float timeToShake = 1;

    [Header("Debug Log")]
    public string log;
}
</code>
</pre>
and here is what is using the <code>GlitchEvents</code> to trigger the <code>GlitchFunctions</code>
<pre>
<code>
GlitchEvents TriggerChecker(GlitchEvents.Trigger type, WindowData windowData = null, string GUID = null, int id = 0, Icon icon = null, Icon.IconType iconType = 0)
    {
        foreach (GlitchEvents item in glitchEvents)
        {
            if (type != item.trigger || item.triggerCount == 0) { continue; }

            switch (type)
            {
                case GlitchEvents.Trigger.FolderUnlocked:
                    if (item.iconToCheck == GUID)
                    {
                        glitchEventsToDecrease.Add(item);
                        FunctionStarter(item);
                    }
                    break;
                case GlitchEvents.Trigger.FolderOpend:
                    if (item.iconToCheck == GUID)
                    {
                        glitchEventsToDecrease.Add(item);
                        FunctionStarter(item, icon);
                    }
                    break;
                case GlitchEvents.Trigger.WindowOpened:
                    if (item.dataToCheckFor == windowData || item.iconType == iconType)
                    {
                        glitchEventsToDecrease.Add(item);
                        FunctionStarter(item);
                    }
                    break;
                case GlitchEvents.Trigger.WindowClosed:
                    if (item.dataToCheckFor == windowData)
                    {
                        glitchEventsToDecrease.Add(item);
                        FunctionStarter(item);
                    }
                    break;
                case GlitchEvents.Trigger.SoundPlayed:
                    if (CheckForSound(item.soundClip))
                    {
                        glitchEventsToDecrease.Add(item);
                        FunctionStarter(item);
                    }
                    break;
                case GlitchEvents.Trigger.iconBinned:
                    if ((item.iconToCheck == GUID || item.dataToCheckFor == windowData) && (item.iconType == iconType || item.iconType == Icon.IconType.Missing))
                    {
                        glitchEventsToDecrease.Add(item);
                        FunctionStarter(item);
                    }
                    break;
                case GlitchEvents.Trigger.ShutDown:
                    glitchEventsToDecrease.Add(item);
                    FunctionStarter(item);
                    break;
                case GlitchEvents.Trigger.EmailOpened:
                    if (item.emailId == id)
                    {
                        glitchEventsToDecrease.Add(item);
                        FunctionStarter(item);
                    }
                    break;
                case GlitchEvents.Trigger.PinOpened:
                    if (item.iconToCheck == GUID || item.dataToCheckFor == windowData || item.iconType == iconType)
                    {
                        glitchEventsToDecrease.Add(item);
                        FunctionStarter(item);
                    }
                    break;
                case GlitchEvents.Trigger.ImageAssembled:
                    glitchEventsToDecrease.Add(item);
                    FunctionStarter(item);
                    break;
                case GlitchEvents.Trigger.DoorKnocked:
                    glitchEventsToDecrease.Add(item);
                    FunctionStarter(item);
                    break;
            }

        }
        DecreaseUses();
        return null;
    }
</code>
</pre>
</details>

## Screenshots

<table>
  <tr>
    <td ><img src="Images\Mediaplayer.png"/></td>
     <td ><img src="Images\RightClick.png"/></td>
  </tr>
</table>
