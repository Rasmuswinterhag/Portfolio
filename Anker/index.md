<head>
  <title>Anker</title>
</head>
### [Back](..)

# *Anker*

Worked as sole developer    
*Summer 2023, January 2025 - february 2025*

---

I started working on this the summer of 2023 since I wanted *[Placid Plastic Duck Simulator](https://store.steampowered.com/app/1999360/Placid_Plastic_Duck_Simulator)* but I was cheap and thought I could make it myself, but worse. So I made it in 2D, then later at Yrgo we were supposed to make a mobile game, so I ressurected this game and upgraded it. In about a month I added
- Cloud Saving
- A shop
- A way to visit other peoples pools and look at their ducks  
- Diffrent types of ducks with diffrent behavoir.

<details>
<summary>Diffrent Duck types</summary>

  This is the BlackHole duck which starts spinning faster and faster until it starts sucking in other ducks until something slows it down.
  <details>
  <summary>Black Hole Duck</summary>
  <pre>
  <code>
  public class BlackHole : MonoBehaviour
  {
      Rigidbody2D rb2d;
      PointEffector2D pEffector;
      CircleCollider2D pEffectorTrigger;
      ParticleSystem particleSys;
      [SerializeField] float spinTrorque = 5;
      [SerializeField] int minSuckSpeed = 360;
      [SerializeField] float suckRepeatTime = 30;

      void Start()
      {
          rb2d = GetComponent&lt;Rigidbody2D&lt;();
          pEffector = GetComponent&lt;PointEffector2D&lt;();
          pEffectorTrigger = GetComponent&lt;CircleCollider2D&lt;();
          particleSys = GetComponent&lt;ParticleSystem&lt;();
  
          InvokeRepeating(nameof(StartSuck), 0, suckRepeatTime);
      }
  
      void StartSuck()
      {
          StartCoroutine(nameof(Suck));
      }
  
      IEnumerator Suck()
      {
          while (rb2d.angularVelocity &lt; minSuckSpeed)
          {
              rb2d.AddTorque(spinTrorque, ForceMode2D.Force);
              yield return new WaitForSeconds(0.1f);
          }
          SuckParticlesEnabled(true);
          yield return new WaitForSeconds(1f);
          while (rb2d.angularVelocity &lt; minSuckSpeed / 3)
          {
              yield return new WaitForSeconds(0.1f);
          }
          SuckParticlesEnabled(false);
      }
  
      void SuckParticlesEnabled(bool value)
      {
          pEffector.enabled = value;
          pEffectorTrigger.enabled = value;
          if (value)
          {
              particleSys.Play();
          }
          else
          {
              particleSys.Stop();
          }
      }
  }
  </code>
  </pre>
  </details>
  
  This is the rocket duck that propells itself forward until it has reached <code>rocketSpeed</code> then stops for a little bit before starting up again.
  <details>
  <summary>Rocket Duck</summary>
  <pre>
  <code>
public class RocketDuck : MonoBehaviour
{
    [SerializeField] float rocketSpeed = 1.5f;
    [SerializeField] float rocketPower = 0.5f;
    [SerializeField] float rocketCooldown = 1f;
    float speed;
    Animator anim;
    Rigidbody2D rb;
    ParticleSystem pSystem;
    float timer;
    AudioSource audioSource;
    bool rocketing = false;

    void Start()
    {
        rb = GetComponent&lt;Rigidbody2D&lt;();
        anim = GetComponent&lt;Animator&lt;();
        pSystem = GetComponent&lt;ParticleSystem&lt;();
        audioSource = GetComponent&lt;AudioSource&lt;();
        timer = rocketCooldown;
    }

    void FixedUpdate()
    {
        timer = timer + Time.fixedDeltaTime;

        speed = rb.velocity.magnitude;
        if (timer &lt; rocketCooldown)
        {
            if (speed &lt; rocketSpeed)
            {
                rb.AddForce(transform.up * rocketPower);
                audioSource.pitch = Random.Range(0.9f, 1.1f);
                RocketStart();
            }
            else
            {
                timer = 0f;
                RocketStop();
            }
        }
    }

    void RocketStart()
    {
        if (rocketing) { return; }
        rocketing = true;
        anim.Play("RocketDuckRocketing");
        pSystem.Play();
        audioSource.Play();
    }

    void RocketStop()
    {
        if (!rocketing) { return; }
        rocketing = false;
        anim.Play("RocketDuckIdle");
        pSystem.Stop();
        audioSource.Stop();
    }
}

  </code>
  </pre>
  </details>
</details>

I also added some additional ducks and I reworked the system for how you get ducks so that instead of only getting every duck once and then not being able to get them again. Now when you have every duck you can start to get duplicates so you can have 2 of each, then 3 of each, etc. etc.

## Screenshots

<table>
  <tr>
    <td><img src="Images\Pool.png" /></td>
  </tr>
  <tr>
    <td><img src="Images\Shop.png" /></td>
  </tr>
  <tr>
    <td><img src="Images\VisitOthers.png" /></td>
  </tr>
</table>
