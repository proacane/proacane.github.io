# 介绍
项目内容选自Unity官方Creator Kit - Beginner Code：https://assetstore.unity.com/packages/templates/tutorials/creator-kit-beginner-code-urp-151986

Unity官方API文档：[Unity - Scripting API: MonoBehaviour](https://docs.unity3d.com/2022.3/Documentation/ScriptReference/MonoBehaviour.html)

# 使用VS2022作为代码编辑器

Edit-Preferences中的External Tools 中设置编辑器，先用VS，以后再试着用用Rider

![image-20241112150246833](C:\Users\Aruceid\AppData\Roaming\Typora\typora-user-images\image-20241112150246833.png)

# 变量

在unity中，创建脚本scrpit实际上就是自定义新的component类型，可以附加到游戏对象GameObject上；C#脚本中的变量也可以看作这个Component类型中的属性；设置为Public就会暴露在 inspector 中

创建并使用变量：

1. 新建一个 scene，命名为 MyScene，放入 Assets/Scenes 下
2. 在新的 scene 中，新建一个 empty gameobject(空游戏对象)命名为 Player
3. 为 Player 对象新增脚本组件，放入 Assets/Scripts 下，命名为 MainPlayer；在其中打印日志
4. 将上一步生成的脚本作为Component应用到 Player 中；运行测试即可发现在控制台中打印的日志

![image-20241112152540164](C:\Users\Aruceid\AppData\Roaming\Typora\typora-user-images\image-20241112152540164.png)

![image-20241112152548624](C:\Users\Aruceid\AppData\Roaming\Typora\typora-user-images\image-20241112152548624.png)

在 Assets/Creator Kit - Beginner Code/Scripts/Tutorial/SpawnerSample.cs 脚本文件中，添加新的变量 radius，用来设置存放距离，该脚本用来生成指定对象

```c#
using UnityEngine;
using CreatorKitCode;

public class SpawnerSample : MonoBehaviour
{
    public GameObject ObjectToSpawn;

    // 距离
    public short radius;
    void Start()
    {
        int angle = 15;
        // 生成物体的位置，初始值为当前物体的位置
        Vector3 spawnPosition = transform.position;
        // Quaternion.Euler方法是进行旋转，这里代表单位向量(1,0,0)绕y轴转15°
        Vector3 direction = Quaternion.Euler(0, angle, 0) * Vector3.right;
        
        spawnPosition = transform.position + direction * radius;
        // 生成物体
        Instantiate(ObjectToSpawn, spawnPosition, Quaternion.identity);

        angle = 55;
        direction = Quaternion.Euler(0, angle, 0) * Vector3.right;
        spawnPosition = transform.position + direction * radius;
        Instantiate(ObjectToSpawn, spawnPosition, Quaternion.identity);

        angle = 95;
        direction = Quaternion.Euler(0, angle, 0) * Vector3.right;
        spawnPosition = transform.position + direction * radius;
        Instantiate(ObjectToSpawn, spawnPosition, Quaternion.identity);
    }
}
```

# 方法

```c#
public class SpawnerSample : MonoBehaviour
{
    public GameObject ObjectToSpawn;

    // 距离
    public short radius;
    void Start()
    {
        int angle = 15;
        for (; angle < 360; angle +=30) {
            GenNewObject(ref angle);
        }
    }

    private void GenNewObject(ref int angle)
    {
        Vector3 direction = Quaternion.Euler(0, angle, 0) * Vector3.right;
        Vector3 spawnPosition = transform.position + direction * radius;
        Instantiate(ObjectToSpawn, spawnPosition, Quaternion.identity);
    }
}
```

# 类

C#中，继承自 MonoBehaviour 的组件类，都会被自动实例化

C#中自定义的类作为成员，需要在类上添加`[System.Serializable]`；类的属性要自动暴露在Unity的Inspector中，必须要Public权限；其它权限的属性需要添加`[UnityEngine.SerializeField]`

```c#
public class MainPlayer : MonoBehaviour
{
    public string myName;
    // cat的属性会出现在Inspector中
    public Cat cat;
    // Start is called before the first frame update
    void Start()
    {
        Debug.Log("My name is " + myName);
    }

    // Update is called once per frame
    void Update() 
    {
        
    }
}

[System.Serializable]
public class Cat
{
    public string name;
    public string age; 
}
```

# 封装与继承

权限修饰符：

- public：可在任何位置访问
- internal：只能在同一项目中访问
- protected：只有该类对象及其子类对象可以访问
- protected internal：同一项目中该类及其子类可以访问
- private：只有自己的类中可以访问

默认权限：

- 类、接口、结构体、枚举的默认权限是 internal；
- 类中成员默认为 private
- 接口成员默认 public
- 命名空间、枚举类型成员默认 public

在unity中，public 成员可以在 Inspector 中直接操作；其它需要加`[UnityEngine.SerializeField]`；子类中可以使用`base`关键字代表基类

```c#
//父类 Monster.cs
using System;
[Serializable]
public class Monster
{
    // 名字
    public string name;
    // 年龄
    public int age;
    // 毛色
    public string color;
    // 生命
    public int hp;

    public Monster() { }
    public Monster(string name, int age, string color, int hp)
    {
        this.name = name;
        this.age = age;
        this.color = color;
        this.hp = hp;
    }
}

// 派生类 Phoenix.cs
using System;
[Serializable]
public class Phoenix:Monster
{
    private bool isReborn=false;

    // 全参构造函数
    public Phoenix(string name, int age, string color, int hp,bool isReborn) : base(name, age, color, hp)
    {
        this.isReborn = isReborn;
    }
    //无参空构造方法
    public Phoenix()
    {
    }

    void Reborn() {
        this.hp = 1000;
        this.isReborn = true;
    }
}
```

