##### # 2如何在Unity中实现Player左右移动 2D Movement

![image-20241217164123624](C:\Users\goodboy\AppData\Roaming\Typora\typora-user-images\image-20241217164123624.png)

![image-20241217164512073](C:\Users\goodboy\AppData\Roaming\Typora\typora-user-images\image-20241217164512073.png)、![image-20241217164941140](C:\Users\goodboy\AppData\Roaming\Typora\typora-user-images\image-20241217164941140.png)

给Player添加脚本PlayerController



```c#
public class PlayerController : MonoBehaviour
{
    [SerializeField]
    private float speed = 8;
    private Rigidbody2D rb;
    private Collider2D col;

    void Start()
    {
        rb = GetComponent<Rigidbody2D>(); 
        col = GetComponent<Collider2D>();
    }
    
    //角色移动
    private void Run()
    {
        float moveDir = Input.GetAxisRaw("Horizontal");
        Vector2 playerVel = new Vector2(moveDir * speed, rb.velocity.y);

        rb.velocity = playerVel;
        //更加丝滑的移动
        //rb.MovePosition(rb.position + playerVel * Time.fixedDeltaTime);

     
    }


    void FixedUpdate()
    {
        Run();

    }
}

```

