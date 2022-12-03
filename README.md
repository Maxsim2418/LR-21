# LR-21[LR-21.zip](https://github.com/Maxsim2418/LR-21/files/10146059/LR-21.zip)

Борнеман М.А

ЭВТ-70

Игровой движок:Unity

Лабораторная работа №21

Тема: разработка игрового проекта 2 CARS

Цель: приобрести навыки в разработке игрового проекта 2 CARS

Ход работы

1.	Выполнение работы

Создание игрового проекта 2 CARS 

1.	В созданный пустой объект Bg добавил компонент Sprite Renderer, куда добавил спрайт BackGround. У Main Camera выставил Size =9 и разрешение 480x800. Настройки объекта Blue Car: добавил спрайт g1264_2. Добавил объектам компонент скрипта Car, в котором выставил Xpos-X = 4, Y=1.12. Скопировал объекта Blue Car, переименовав его в Orange Car, заменив спрайта и включив Blue Car.

![image](https://user-images.githubusercontent.com/119674602/205433409-e1fcf97b-cc91-4aa9-a353-15636cd0462a.png)

Рис. 21.1 Inspector объектов Sprite

2.	 Листинг Car.cs

using UnityEngine;

public class Car : MonoBehaviour
{
    public bool FirstLaneBlueCar, FirstLaneOrangeCar;
    public bool BlueCar;

    public Vector2 Xpos;

    // Update is called once per frame
    void Update()
    {
        if (Input.GetKeyDown("left"))
        {
            LeftButtonPressed();
        }
        if (Input.GetKeyDown("right"))
        {
            RightButtonPressed();
        }
        if (BlueCar)
        {
            if (FirstLaneBlueCar)
            {
                transform.position = Vector3.Lerp(transform.position, new Vector3(-Xpos.y, transform.position.y, 0), .1f);
            }
            else
            {
                transform.position = Vector3.Lerp(transform.position, new Vector3(-Xpos.x, transform.position.y, 0), .1f);

            }
        }
        else
        {
            if (FirstLaneOrangeCar)
            {
                transform.position = Vector3.Lerp(transform.position, new Vector3(Xpos.y, transform.position.y, 0), .1f);
            }
            else
            {
                transform.position = Vector3.Lerp(transform.position, new Vector3(Xpos.x, transform.position.y, 0), .1f);

            }
        }

    }
    public void LeftButtonPressed()
    {
        if (FirstLaneBlueCar) { FirstLaneBlueCar = false; } else { FirstLaneBlueCar = true; }
    }
    public void RightButtonPressed()
    {
        if (FirstLaneOrangeCar) { FirstLaneOrangeCar = false; } else { FirstLaneOrangeCar = true; }
    }

    public void OnTriggerEnter2D(Collider2D collision)
    {
        if (collision.tag == "Circle")
        {
            //give Points
            FindObjectOfType<GameController>().AddScore();
            Destroy(collision.gameObject);
        }
        if (collision.tag == "Cube")
        {
            //gameOver
            FindObjectOfType<GameController>().GameOver();
            Debug.Log("playerGO");
        }
    }
}

3.	Листинг CircleSquaerGO.cs

using UnityEngine;

public class CircleSquareGO : MonoBehaviour
{
    int speed;
    Rigidbody2D rgbd;
    // Start is called before the first frame update
    void Start()
    {
        speed = 10;
        rgbd = GetComponent<Rigidbody2D>();
        rgbd.velocity = new Vector2(0, -speed);
    }
}

4.	В пустом объекте Circle Blue создал объект через 2D Object – Sprites, куда добавил спрайт Blue_circle. Скопировал его, переименовав в Squaer Blue и заменив спрайт на Blue_cube. Таким же образом сделал игровые объекты другого типа. Объектам Circle Blue и Circle Orange добавил компонент Box Collider 2D. Также добавил к ним компонент скрипта CircleSquaerGO и компонент RigidBody 2D, выставив Body Tipe – Kinematic,  Collision Detection – Contonious и включив Is Trigger.

![image](https://user-images.githubusercontent.com/119674602/205433426-d415456c-e9dd-4683-a578-503e3f7ff05d.png)

Рис. 21.2 Inspector объекта Squaer Orange

5.	Создал префабы игровых объектов в папке Prefabs. Создал тэг Circle, который прикрепил к объектам Circle Blue и Circle Orange, и тэг Cube, который прикрепил к объектам Squaer Blue и Squaer Orange. Создал пустой объект GameController, к которому прикрепил компонент скрипта GameController. Настройки EdgeCollider: выставил Y=-9.5; добавил к нему компонент Box Collider 2D, включив Is Trigger, выставил Size – X=14.

![image](https://user-images.githubusercontent.com/119674602/205433440-85286708-3886-4482-906c-2267f106a55c.png)

Рис. 21.3 Inspector объекта GameController и объекта EdgeCollider

6.	Листинг GameController.cs

using System.Collections;
using UnityEngine.UI;
using UnityEngine;
using UnityEngine.SceneManagement;

public class GameController : MonoBehaviour
{   //Circle And Square GO
    public GameObject[] BlueGO;
    public GameObject[] OrangeGO;
    //To instantiate squate and circle
    public float startWait;
    public float spawnWait;
    //which GO to Instantiate
    GameObject Blue, Orange;

    //spawn point spawn randomly X pos
    float[] XPosition = new float[2] { 1.5f, 4.6f };
    bool GameOverBool;

    int Score;
    public Text ScoreText;

    public GameObject GameOverCanvas;
    // Start is called before the first frame update
    void Start()
    {
        GameOverBool = false;
        Time.timeScale = 1;
        GameOverCanvas.SetActive(false);
    }

    IEnumerator SpawnObjects()
    {
        yield return new WaitForSeconds(startWait);
        while (true)
        {
            for (int i = 0; i < 50; i++)
            {
                //choosing Xpos
                float OrangeXpos = XPosition[Random.Range(0, XPosition.Length)];
                //setting position
                Vector3 OrangePos = new Vector3(OrangeXpos, 10, 0);
                //choosing between square or circle
                Orange = OrangeGO[Random.Range(0, OrangeGO.Length)] as GameObject;
                //Instantiate now
                Instantiate(Orange, OrangePos, Quaternion.identity);
                yield return new WaitForSeconds(spawnWait);

                //choosing Xpos
                float BlueXpos = -XPosition[Random.Range(0, XPosition.Length)];
                //setting position
                Vector3 BluePos = new Vector3(BlueXpos, 10, 0);
                //choosing between square or circle
                Blue = BlueGO[Random.Range(0, BlueGO.Length)] as GameObject;
                //Instantiate now
                Instantiate(Blue, BluePos, Quaternion.identity);
                yield return new WaitForSeconds(spawnWait);
            }
           
        }
    }
    public void StartGame()
    {   
        StartCoroutine(SpawnObjects());
    }

    public void GameOver()
    {
        GameOverBool = true;
        Time.timeScale = 0;
        GameOverCanvas.SetActive(true);
    }

    public void AddScore()
    {
        Score += 1;
        ScoreText.text = Score.ToString();
    }
    public void Restart()
    {
        SceneManager.LoadScene(SceneManager.GetActiveScene().buildIndex);
    }
}

7.	Листинг скрипта EdgeCollider
using UnityEngine;

public class EdgeCollider : MonoBehaviour
{
    public void OnTriggerEnter2D(Collider2D collision)
    {
        if (collision.tag == "Circle")
        {
            //gameOver
            FindObjectOfType<GameController>().GameOver();
            Debug.Log("GameOver");
        }
        else if (collision.tag == "Cube")
        {
            //destroy go
            Destroy(collision.gameObject);
        }

    }
}

8.	Создал объект StartGameCanvas:  выставил Canvas Scaler – Scale with screen size и выстваил разрешение 480х800. Создал объект Button: прикрепил спрайт g1264_0 и нажал Set Native Size. Добавил объект GameController и StartGameCanvas в On Click, выставил GameController – StartGame и gameObject - SetActive. Создал объект panel, у которого выставил черный цвет.

![image](https://user-images.githubusercontent.com/119674602/205433450-70e9183f-4a11-472d-b6aa-fc09dd6ea2ba.png)

Рис. 21.4 Inspector объектов StartGameCanvas

9.	Создал объект GameCanvas:  в компоненте выставил Canvas Scaler – Scale with screen size и выстваил разрешение 480х800. Создал дочерний объект LeftHalfButton: выставил Height=800 Width=240 и в Anchor Presets – привязку центра к середине левой стороны. Скопировал его и изменил под правую сторону. Выкрутил у кнопок макс. прозрачность. В On Click добавил объект Blue Car, выставил Car – LeftHalfButton.Pressed и для RightHalfButton добавил объект Orange Car, выставил Car –RightHalfButton.Pressed.

![image](https://user-images.githubusercontent.com/119674602/205433465-c52000cd-1ad0-49a4-844f-ed485451789d.png)
![image](https://user-images.githubusercontent.com/119674602/205433473-b3592a94-45d4-429c-9fdd-3bc0abb7b4d2.png)

Рис. 21.4 Inspector объектов LeftHalfButton и RightHalfButton

10.	Создал объект ScoreCanvas. Создал дочерний объект Text: выставил Width=625 Height=250, выравнивание текста посередине, в окне Anchor Presets выставил  привязку центра к середине верхней стороны, Font Size = 140, добавил компонент Outline, где выставил Effect Distance X=2 и Y=-2. 

![image](https://user-images.githubusercontent.com/119674602/205433499-7e2ba07c-47bd-4401-bc24-0596e33654d7.png)

Рис. 21.4 Inspector объекта Text

11.	Создал GameOverCanvas. Создал дочерний объект Text: установил Width=685 Height=130, выравнивание текста посередине, Font Size = 85, в окне Anchor Presets выставил  привязку центра к середине верхней стороны. Создал объект Button:  выставил Width=645 Height=90, Font Size = 65, цвет шрифта белый и выкрутил макс. прозрачность; в компонент On Click добавил объект GameController, выставив GameController-Restart.


Рис. 21.4 Inspector объекта Text

12.	Вывод

В ходе проделанной работы были приобретены навыки в разработке игрового проекта 2 CARS.
