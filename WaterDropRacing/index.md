<head>
  <title>Water Drop Racing</title>
</head>
### [Back](..)
# *Water Drop Racing*
<img src="Images\MainMenu.png" width="100%"/>

[Game Jam Page](https://globalgamejam.org/games/2025/title-5)

Worked as **Programmer**    
*2025 January, 17 - 19*  
Language: c#    

---

Won the ***Best Game Design*** award in the [*Global Game Jam Gothenburg*](https://globalgamejam.org/jam-sites/2025/ggj-gothenburg-25) which had the theme *Bubbles*.

I worked on most things but some highlights are:
- The collectible water drops, They combine into a bigger drop when they touch.
- The camera tracking the player and looking ahead of the movement.
- The camera size being dynamic with the size of the player.
- The racing meters showing where all racers are on the window.

<details>
<summary>CameraMovement.cs</summary>
<pre>
<code>
void Start()
    {
        camera = Camera.main;
        Vector3 nextPosition = objectToTrack.transform.position;
        nextPosition.z = transform.position.z;
        transform.position = nextPosition;
    }

    void LateUpdate()
    {
        if (objectToTrack == null)
        {
            Vector3 trackPosition = new(RandomWaterPlacement.Instance.transform.position.x,
                                    RandomWaterPlacement.Instance.transform.position.y,
                                    transform.position.z);
            transform.position = Vector3.MoveTowards(transform.position, trackPosition, 1);
            float nextSize = RandomWaterPlacement.Instance.SpawnArea.width * camera.aspect / 1.70f;
            camera.orthographicSize = Mathf.Lerp(camera.orthographicSize, nextSize, Time.deltaTime * 2);
        }
        else
        {
            TrackObject();
        }
    }

    void TrackObject()
    {
        MoveTowardsTarget();
        DynamicSize();
    }

    void MoveTowardsTarget()
    {
        Vector3 otherRbVelocity = (Vector3)objectToTrack.GetComponent&lt;Rigidbody2D&gt;().velocity;
        Vector3 modifiedOtherRbVelocity;
        modifiedOtherRbVelocity.x = otherRbVelocity.x * directionMultiplier.x;
        modifiedOtherRbVelocity.y = otherRbVelocity.y * directionMultiplier.y;
        modifiedOtherRbVelocity.z = otherRbVelocity.z * directionMultiplier.z;

        Vector3 trackPosition = objectToTrack.transform.position + modifiedOtherRbVelocity;
        Vector3 nextPosition = Vector3.MoveTowards(transform.position, trackPosition, 1);
        nextPosition.y = Mathf.Clamp(nextPosition.y, FinishLine.Instance.transform.position.y, maxCameraPos);
        nextPosition.x = Mathf.Clamp(nextPosition.x, -maxCameraPosX, maxCameraPosX);
        nextPosition.z = transform.position.z;
        transform.position = nextPosition;
    }

    void DynamicSize()
    {
        float nextSize = objectToTrack.transform.localScale.x * sizeMultiplier;
        nextSize = Mathf.Lerp(camera.orthographicSize, nextSize, Time.deltaTime);
        nextSize = Mathf.Clamp(nextSize, 14, 50); //Maybe do this better?

        camera.orthographicSize = nextSize;
    }
</code>
</pre>
</details>

## Screenshots

<table>
  <tr>
    <td><img src="Images\Gameplay.png" /></td>
    <td><img src="Images\Finished.png" /></td>
  </tr>
</table>
