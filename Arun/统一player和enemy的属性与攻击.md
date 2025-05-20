```c#

public class Attack : MonoBehaviour
{

    [Header("数值")]
    public int damage;
    public float attackRange;
    public float attackRate; // 攻击频率


    private void OnTriggerStay2D(Collider2D collision)
    {


        //加个？如果没有就返回null
        collision.GetComponent<Character>()?.TakeDamage(this);
    }

}

```



```c#


public class Character : MonoBehaviour
{
    [Header("数值")]
    public int currentHealth;
    public int maxHealth = 3000;
    public float invulnearableDuration; //无敌
    public float invulnearableCounter;
    public bool invulnearable;

    //气泡
    public GameObject floatPoint;


    void Start()
    {
        currentHealth = maxHealth;
    }

    private void Update()
    {
        if (invulnearable)
        {
            invulnearableCounter -= Time.deltaTime;
            if(invulnearableCounter <= 0)
            {
                invulnearable = false;
                invulnearableCounter = 0f;
            }
        }
    }

    public void TakeDamage(Attack attacker)
    {
        if (invulnearable) return;
        currentHealth -= attacker.damage;
        TriggerInvulnearable();

        float yVal = Random.Range(-.5f, .5f), xVal = Random.Range(-0.5f, 0.6f);
        Vector3 floatPos = new Vector3(transform.position.x + xVal, transform.position.y + yVal, transform.position.z);
        GameObject gb = Instantiate(floatPoint, floatPos, Quaternion.identity);
        gb.transform.GetChild(0).GetComponent<TextMesh>().text = attacker.damage.ToString();
        if (currentHealth <= 0)
        {
            currentHealth = 0;
            Destroy(gameObject);
        }
    }


    //触发无敌
    private void TriggerInvulnearable()
    {
        if (!invulnearable)
        {
            invulnearable = true;
            invulnearableCounter = invulnearableDuration;
        }
    }

}

```

