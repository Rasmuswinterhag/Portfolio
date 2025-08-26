### [Back](..)
# *Regauge*

<img src="Images\Banner.png" width="100%"/>

[Itch Page](https://yrgo-game-creator.itch.io/regauge)  

Worked as **Programmer**  
*2025 March - 2025 July*

---

In pre-production we had almost an entierly diffrent game idea where you would have a 1v1 stratergy game where you would control your bots and plan your moves in advance almost like "coding" your bots. Then they would execute their "code" all at the same time. When we playtested that idea it worked on paper and thought it was fun but we relized that we couldnt hide your intructions from the other person since it was going to be a PvP couch game, both would all see the same screen. By talking with our teachers and between ourselves in the team it eventually evolved into what it is now.
<img src="Images\Playtest.jpg" width="50%"/>

We started this project by making a prototype, Where we tried to finish the game (code wise) in one week with a bunch of bad code. Then when we were done after that week we threw it all away to start over from the beginning now knowing the direction we want to take the game better and also how some of the systems should work. It helped quite a bit, especially in the begining since thats when we were mosty loose on the concept.

Something im proud of accomplishing is how the main menu is handled. It has diffrent screens and each screen has its own camra angle.

<details>
<summary>Structs.cs</summary>

```csharp
[Serializable]
public struct TwoTransforms
{
    public Transform from;
    public Transform to;

    public Vector3 Direction()
    {
        return to.position - from.position;
    }
}

[Serializable]
public struct Screen
{
    public GameObject gameObject;
    public TwoTransforms cameraAngle;
    public GameObject firstSelectedUIElement;

    public void SetActive(bool value)
    {
        gameObject.SetActive(value);
    }
}
```
</details>

<details>
<summary>MainMenuCamera.cs (excerpt)</summary>

```csharp
    void Start()
    {
        poseCamera = Camera.main.GetComponent<PoseCamera>();
        eventSystem = FindObjectOfType<EventSystem>();

        foreach (Screen screen in screens)
        {
            screensDictionary.Add(screen.gameObject, screen);
        }

        Show(startScreen);
    }

    public void Show(GameObject keyToGoTo)
    {
        lastScreen = CurrenScreen().gameObject;
        foreach (var dictionary in screensDictionary)
        {
            dictionary.Value.SetActive(false);
        }

        Screen objectToActivate = screensDictionary[keyToGoTo];

        objectToActivate.SetActive(true);
        poseCamera.ChangeCameraTo(objectToActivate.cameraAngle);
        eventSystem.SetSelectedGameObject(objectToActivate.firstSelectedUIElement);
    }

    Screen CurrenScreen()
    {
        Screen activeScreen = new();
        foreach (Screen screen in screensDictionary.Values)
        {
            if (screen.gameObject.activeSelf) { activeScreen = screen; break; }
        }

        return activeScreen;
    }

```
</details>

<details>
<summary>PoseCamera.cs</summary>

```csharp

    [SerializeField] float moveSpeed = 5;
    [SerializeField] float rotationSpeed = 5;

    TwoTransforms target;

    public void ChangeCameraTo(TwoTransforms cameraAngle)
    {
        target = cameraAngle;
    }

    public void SetCameraTo(TwoTransforms cameraAngle)
    {
        ChangeCameraTo(cameraAngle);

        transform.position = target.from.position;
        transform.rotation = Quaternion.Euler(target.Direction());
    }

    void Update()
    {
        if (target.from == null || target.to == null) { return; }
        transform.position = Vector3.Lerp(transform.position, target.from.position, Time.deltaTime * moveSpeed);
        transform.rotation = Quaternion.Lerp(transform.rotation, Quaternion.LookRotation(target.Direction()), Time.deltaTime * rotationSpeed);
    }

```
</details>

It works by having each screen in Unity be connected to a position through the ``Screen`` object which also keeps track of the position and rotation of the camera via GameObjects placed in the scene so you can easily change the cameras positions as a developer. Then just lerping the cameras position and rotation towards the ``target``.

## Screenshots

<table>
  <tr>
    <td><img src="Images\SpinnyPlate.gif" /></td>
    <td><img src="Images\4PlayerGameplay.gif" /></td>
  </tr>
</table>
