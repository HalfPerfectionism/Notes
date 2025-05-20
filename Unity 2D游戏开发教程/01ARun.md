```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class EnemyAI : MonoBehaviour
{
    public enum EnemyMode { careful, serious, rage };
    public enum EnemyState { idle, walk, run, withdraw, 刺击, 前进喷泉, 三角剑, 七星光芒阵 };
    public EnemyState currentState = EnemyState.idle;

    [Header("Settings")]
    public float a0Range = 4.0f;
    public float a1Range = 4.0f;
    public float a2Range = 8.0f;
    public float a3Range = 5.0f;
    public float a0ColdTime = 3f;
    public float a1ColdTime = 5f;
    public float a2ColdTime = 5f;
    public float a3ColdTIme = 10f;

    private float a0LastTime = 0f;
    private float a1LastTime = 0f;
    private float a2LastTime = 0f;
    private float a3LastTime = 0f;

    public float walkSpeed = 0.41f;
    public float runSpeed = 5.0f;
    public float projectileSpeed = 35f;
    public float[] forwardShakeTime;
    public float[] backShakeTime;


    [Header("Reference")]

    public GameObject projectilePrefab;
    public Transform attackPoint;

    private Animator animator;
    private Rigidbody2D rb;
    private Transform player;
    private EnemyAttack ea;

    private float lastTime;
    private bool isAttacking = false;
    private bool canMove = false;
    private bool canFilp = false;

    private void Start()
    {
        animator = GetComponent<Animator>();
        rb = GetComponent<Rigidbody2D>();
        player = GameObject.FindGameObjectWithTag("Player").transform;
        ea = GetComponent<EnemyAttack>();
    }

    private void Update()
    {
        if (player == null) return;

        // 计算与玩家的距离
        float distanceToPlayer = Vector2.Distance(transform.position, player.position);
        Vector2 direction = (player.transform.position - transform.position).normalized;
        print(Mathf.Sign(direction.x) * distanceToPlayer);

        if (!canMove) rb.velocity = Vector2.zero;

        // 根据距离切换状态
        if (!isAttacking)
        {
            if (distanceToPlayer <= a0Range && Input.GetButtonDown("Jump"))
            {

                ChangeState(EnemyState.刺击);
            }
            else
            {
                ChangeState(EnemyState.idle);
            }

        }

        // 执行当前状态的行为
        ExecuteState();
    }

    //切换状态
    void ChangeState(EnemyState newState)
    {
        if (currentState != newState)
        {
            currentState = newState;
        }
    }

    IEnumerator PreformAttack(int x)
    {
        isAttacking = true;
        canMove = false;
        if (x == 0)
        {
            a0LastTime = Time.time;
            //yield return new WaitForSeconds(forwardShakeTime[x]);
            animator.SetTrigger("刺击");

            yield return new WaitForSeconds(backShakeTime[x]);
        }
        else if (x == 1)
        {
            animator.SetTrigger("a2");
            yield return new WaitForSeconds(forwardShakeTime[x]);
            // 获取敌人的当前朝向（左或右）
            float direction = Mathf.Sign(transform.localScale.x);

            // 计算旋转角度（例如：朝左时旋转 180 度）
            Quaternion rotation = Quaternion.Euler(0, direction == 1 ? 0 : 180, 0);

            // 生成时直接应用旋转
            GameObject projectile = Instantiate(
                projectilePrefab,
                attackPoint.position,
                rotation
            );
            projectile.GetComponent<Rigidbody2D>().velocity = new Vector2(direction * projectileSpeed, 0f);
            yield return new WaitForSeconds(backShakeTime[x]);
        }

        isAttacking = false;
        canMove = true;
        yield return null;
    }


    void ExecuteState()
    {
        switch (currentState)
        {
            case EnemyState.idle:
                rb.velocity = Vector2.zero;
                break;


            case EnemyState.walk:
                if (!canMove) return;
                Vector2 direction = (player.transform.position - transform.position).normalized;
                if (direction.x != 0) transform.localScale = new Vector3(Mathf.Sign(direction.x), 1, 1);
                rb.velocity = new Vector2(direction.x * walkSpeed, 0);
                break;

            case EnemyState.刺击:
                if (Time.time < a0LastTime + a0ColdTime) return;
                StartCoroutine(PreformAttack(0));
                break;

            case EnemyState.前进喷泉:
                if (Time.time < a1LastTime + a1ColdTime) return;
                StartCoroutine(PreformAttack(1));
                break;
            case EnemyState.三角剑:
                if (Time.time < a2LastTime + a2ColdTime) return;
                StartCoroutine(PreformAttack(2));
                break;
            case EnemyState.七星光芒阵:
                if (Time.time < a3LastTime + a3ColdTIme) return;
                StartCoroutine(PreformAttack(3));
                break;
        }
    }
}

```

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class EnemyAI : MonoBehaviour
{
    public enum EnemyMode { careful, serious, rage };
    public enum EnemyState { idle, walk, run, withdraw, 刺击, 前进喷泉, 三角剑, 七星光芒阵 };
    public EnemyState currentState = EnemyState.idle;

    [Header("Settings")]
    public float a0Range = 4.0f;
    public float a1Range = 4.0f;
    public float a2Range = 8.0f;
    public float a3Range = 5.0f;
    public float a0ColdTime = 3f;
    public float a1ColdTime = 5f;
    public float a2ColdTime = 5f;
    public float a3ColdTIme = 10f;

    private float a0LastTime = 0f;
    private float a1LastTime = 0f;
    private float a2LastTime = 0f;
    private float a3LastTime = 0f;

    public float walkSpeed = 0.41f;
    public float runSpeed = 5.0f;
    public float projectileSpeed = 35f;
    public float[] forwardShakeTime;
    public float[] backShakeTime;


    [Header("Reference")]

    public GameObject projectilePrefab;
    public Transform attackPoint;

    private Animator animator;
    private Rigidbody2D rb;
    private Transform player;
    private EnemyAttack ea;

    private float lastTime;
    private bool isAttacking = false;
    private bool canMove = true;
    private bool canFilp = true;
    private float distanceToPlayer; //距离玩家距离
    private Vector2 direction;
    private float xSignDirection; //符号化方向
    private int face = 1; // 朝向，正1，反-1；

    private void Start()
    {
        animator = GetComponent<Animator>();
        rb = GetComponent<Rigidbody2D>();
        player = GameObject.FindGameObjectWithTag("Player").transform;
        ea = GetComponent<EnemyAttack>();
    }

    private void Update()
    {
        if (player == null) return;

        // 计算与玩家的距离
        distanceToPlayer = Vector2.Distance(transform.position, player.position);
        direction = (player.transform.position - transform.position).normalized;
        xSignDirection = Mathf.Sign(direction.x);

        //print(Mathf.Sign(direction.x) * distanceToPlayer);

        if (!canMove) rb.velocity = new Vector2(0f, rb.velocity.y);

        // 根据距离切换状态
        if (!isAttacking)
        {
            
            Filp();

            if (distanceToPlayer <= a2Range)
            {

                ChangeState(EnemyState.三角剑);
            }

            else
            {
                ChangeState(EnemyState.idle);
            }

        }

        // 执行当前状态的行为
        ExecuteState();
    }


    //切换状态
    void ChangeState(EnemyState newState)
    {
        if (currentState != newState)
        {
            currentState = newState;
        }
    }

    //技能的具体逻辑
    IEnumerator PreformAttack(int x)
    {
        isAttacking = true;
        //canMove = false;
        if (x == 0)//刺击
        {
            a0LastTime = Time.time;
            animator.SetTrigger("刺击");
            yield return new WaitForSeconds(backShakeTime[x]);
        }
        else if (x == 1)//前进喷泉
        {
            a1LastTime = Time.time;
            canFilp = false;
            animator.SetTrigger("前进喷泉");

            // 初始爆发速度
            float initialSpeed = 20f * face;
            float timer = 0f;
            float duration = 1f; // 1秒内减速到0

            while (timer < duration)
            {
                // 线性减速：初始速度按剩余时间比例衰减
                float currentSpeed = Mathf.Lerp(initialSpeed, 0, timer / duration);
                rb.velocity = new Vector2(currentSpeed, rb.velocity.y);

                timer += Time.deltaTime;
                yield return null; // 每帧更新
            }

            rb.velocity = new Vector2(0, rb.velocity.y); // 确保最终速度为0
            canFilp = true;
        }
        else if(x == 2)
        {
            a2LastTime = Time.time;
            canFilp = false;
            canMove = false;
            animator.SetTrigger("三角剑");

            //飞行物翻转
            Quaternion rotation = Quaternion.Euler(0, face == 1 ? 0 : 180, 0);

            yield return new WaitForSeconds(forwardShakeTime[x]); //前摇
            print(forwardShakeTime[x]);

            GameObject projectile = Instantiate(projectilePrefab, attackPoint.position, rotation);
            projectile.GetComponent<Rigidbody2D>().velocity = new Vector2(face * projectileSpeed, 0f);

            yield return new WaitForSeconds(backShakeTime[x]);
            canFilp = true;
            canMove = true;
        }
        else if(x == 3)
        {
            a3LastTime = Time.time;
            canFilp = false;
            canMove = false;
            animator.SetTrigger("七星光芒阵");
            yield return new WaitForSeconds(backShakeTime[x]);
            canFilp = true;
            canMove = true;
        }
        isAttacking = false;
        canMove = true;
        yield return null;
    }

    //翻转
    void Filp()
    {
        if (canFilp)
        {
            if(xSignDirection < 0)
            {
                face = -1;
                transform.localRotation = Quaternion.Euler(0, 180, 0);
            }
            else
            {
                face = 1;
                transform.localRotation = Quaternion.Euler(0, 0, 0);
            }
        }
    }


    void ExecuteState()
    {
        switch (currentState)
        {
            case EnemyState.idle:
                rb.velocity = Vector2.zero;
                break;


            case EnemyState.walk:
                if (!canMove) return;
                Vector2 direction = (player.transform.position - transform.position).normalized;
                if (direction.x != 0) transform.localScale = new Vector3(Mathf.Sign(direction.x), 1, 1);
                rb.velocity = new Vector2(direction.x * walkSpeed, 0);
                break;

            case EnemyState.刺击:
                if (Time.time < a0LastTime + a0ColdTime) return;
                StartCoroutine(PreformAttack(0));
                break;

            case EnemyState.前进喷泉:
                if (Time.time < a1LastTime + a1ColdTime) return;
                StartCoroutine(PreformAttack(1));
                break;
            case EnemyState.三角剑:
                if (Time.time < a2LastTime + a2ColdTime) return;
                StartCoroutine(PreformAttack(2));
                break;
            case EnemyState.七星光芒阵:
                if (Time.time < a3LastTime + a3ColdTIme) return;
                StartCoroutine(PreformAttack(3));
                break;
        }
    }
}

```

