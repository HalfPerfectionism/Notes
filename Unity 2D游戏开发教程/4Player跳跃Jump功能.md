### Player跳跃Jump功能



![image-20241219175649630](C:\Users\goodboy\AppData\Roaming\Typora\typora-user-images\image-20241219175649630.png)

```c#
public class PlayerController : MonoBehaviour
{
    ...
    [SerializeField]
    private float jumpSpeed = 2; //跳跃速度
    private BoxCollider2D feetColllider; //脚脚的碰撞体
    

    void Start()
    {
		...
        feetColllider = GetComponent<BoxCollider2D>();
    }

    //跳跃
    private void Jump()
    {
        if (Input.GetButtonDown("Jump"))
        {
            if (feetColllider.IsTouchingLayers(LayerMask.GetMask("Ground")))
            {
                Vector2 jumpVel = new Vector2(0f, jumpSpeed);
                rb.velocity = Vector2.up * jumpVel;
            }
        }
    }

    void Update()
    {
        Run();
        Filp();
        Jump();
    }
}

```

