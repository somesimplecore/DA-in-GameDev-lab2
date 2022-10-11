# Интеграция сервиса для получения данных профиля пользователя.
Отчет по лабораторной работе #2 выполнил(а):
- Паханов Александр Александрович
- РИ-300018

Отметка о выполнении заданий (заполняется студентом):

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * | 60 |
| Задание 2 | * | 20 |
| Задание 3 | * | 20 |

знак "*" - задание выполнено; знак "#" - задание не выполнено;

Работу проверили:
- к.т.н., доцент Денисов Д.В.
- к.э.н., доцент Панов М.А.
- ст. преп., Фадеев В.О.

[![N|Solid](https://cldup.com/dTxpPi9lDf.thumb.png)](https://nodesource.com/products/nsolid)

[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

Структура отчета

- Данные о работе: название работы, фио, группа, выполненные задания.
- Цель работы.
- Задание 1.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Задание 2.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Задание 3.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Выводы.
- ✨Magic ✨

## Цель работы
Создание интерактивного приложения и изучение принципов интеграции в него игровых сервисов.

## Задание 1
### По теме видео практических работ 1-5 повторить реализацию игры на Unity.
### Ход работы:
- Создание основных игровых объектов

![](/Pics/z1_1.jpg)

- Реализация передвижения дракона и сбрасывания яиц

```C#
using UnityEngine;

public class EnemyDragon : MonoBehaviour
{
    public GameObject dragonEggPrefab;
    public float speed = 1;
    public float timeBetweenEggDrop = 1f;
    public float leftRightDistance = 10f;
    public float chanceDirection = 0.1f;
    void Start()
    {
        Invoke("DropEgg", 2f);
    }
    
    void DropEgg()
    {
        Vector3 MyVector = new Vector3(0.0f, 5.0f, 0.0f);
        GameObject egg = Instantiate<GameObject>(dragonEggPrefab);
        egg.transform.position = transform.position + MyVector;
        Invoke("DropEgg", timeBetweenEggDrop);
    }

    void Update()
    {
        Vector3 pos = transform.position;
        pos.x += speed * Time.deltaTime;
        transform.position = pos;

        if(pos.x < -leftRightDistance)
        {
            speed = Mathf.Abs(speed);
        }
        else if (pos.x > leftRightDistance)
        {
            speed = -Mathf.Abs(speed);
        }
    }

    private void FixedUpdate()
    {
        if(Random.value < chanceDirection)
        {
            speed *= -1;
        }
    }
}
```

- Добавление платформы и новых материалов

![](/Pics/z1_2.jpg)

- Создание визуальных эффектов

![](/Pics/z1_3.jpg)

Скрипт поведения яйца:
```C#
using UnityEngine;

public class DragonEgg : MonoBehaviour
{
    public static float bottomY = -30f;

    private void OnTriggerEnter(Collider other)
    {
        ParticleSystem ps = GetComponent<ParticleSystem>();
        var em = ps.emission;
        em.enabled = true;

        Renderer rend;
        rend = GetComponent<Renderer>();
        rend.enabled = false;
    }

    void Update()
    {
        if(transform.position.y < bottomY)
        {
            Destroy(this.gameObject);
        }
    }
}
```

Скрипт DragonPicker для генерации щитов:

```C#
using UnityEngine;

public class DragonPicker : MonoBehaviour
{
    public GameObject energyShieldPrefab;
    public int numEnergyShield = 3;
    public float energyShieldBottomY = -6f;
    public float energyShieldRadius = 1.5f;
    void Start()
    {
        for(int i = 1; i <= numEnergyShield; i++)
        {
            GameObject tShieldGo = Instantiate<GameObject>(energyShieldPrefab);
            tShieldGo.transform.position = new Vector3(0, energyShieldBottomY, 0);
            tShieldGo.transform.localScale = new Vector3(1 * i, 1 * i, 1 * i);
        }
    }
}

```

## Задание 2
### В проект, выполненный в предыдущем задании, добавить систему проверки того, что SDK подключен (доступен в режиме онлайн и отвечает на запросы)
### Ход Работы:

Чтобы подключить SDK Яндекс к проекту, нужно добавить в заголовок head HTML-страницы строку следующего вида:
```javascript
<!-- Yandex Games SDK -->
<script src="https://yandex.ru/games/sdk/v2"></script>
```

Далее требуется инициализировать SDK, используя метод init объекта YaGames:

```C#
YaGames
    .init()
    .then(ysdk => {
        console.log('Yandex SDK initialized');
        window.ysdk = ysdk;
    });
```

Создадим у объекта Sphere дочерний объект Cylinder:

![](/Pics/Cylinder1.jpg)

![](/Pics/Cylinder2.jpg)

Видим, что у объекта Cylinder координаты 0 0 3.
Теперь сделаем его самостоятельным:

![](/Pics/Cylinder3.jpg)

![](/Pics/Cylinder4.jpg)

При этом координаты изменились на 0 1,94 3.
Смена координат произошла из-за того, что в начале объект Cylinder был дочерним объектом и имел локальные координаты относительно родителя, а потом, когда стал самостоятельным объектом, его координаты стали мировыми.

#### - Создайте три различных примера работы компонента RigidBody?

Создадим 3 куба, добавим им компонент RigitBody и измениим им настройки:
- у синего куба уберем Use Gravity (по умолчанию включено);
- зеленому кубу добавим Is Kinematic (по умолчанию выключено);
- у желтого куба оставим настройки по умолчанию.

Первоначальное состояние кубов:

![](/Pics/cubes1.jpg)

При запуске сцены:

![](/Pics/cubes2.jpg)

Желтый куб начал падать вниз, т. к. у него работает настройка Use Gravity.
Зеленый и синий кубы остались на месте, т. к. у первого отключена физика (Is Kinematic), а второй не будет двигаться до тех пор, пока на него не будет взаимодействовать какая-то сила, потому что мы отключили для него силу гравитации.

Пробуем столкнуть синий и зеленый кубы, из-за чего первый начал движение, а второй остался на месте:

![](/Pics/cubes3.jpg)



## Задание 3
### Реализуйте на сцене генерацию n кубиков. Число n вводится пользователем после старта сцены.
### Ход Работы:

Сделаем новую сцену, в которой создадим следующие объекты:

![](/Pics/task3.jpg)

![](/Pics/task3_scene.jpg)

Код для генерации кубиков использует префаб куба и хранит ссылку на Input Field. Инспектор объекта CubeSpawner:

![](/Pics/task3_inspector.jpg)

Сам код для спавна кубиков:

```C#
using TMPro;
using UnityEngine;

public class CubesSpawner : MonoBehaviour
{
    public Object CubePrefab;
    public TMP_InputField CubesNum;
    public void SpawnCubes()
    {
        var num = int.Parse(CubesNum.text);
        for (var i = 0; i < num; i++)
            Instantiate(CubePrefab);
    }
}
```

Запускаем сцену и вводим в поле необходимое количество кубиков:

![](/Pics/task3_input.jpg)

В результате видим, что кубы создаются в количестве 12 штук и разлетаются из-за компонента Rigit Body на префабе:

![](/Pics/task3_cubesClones.jpg)

![](/Pics/task3_generated.jpg)


## Выводы

Мы узнали, что Unity - это многофункциональный удобный движок, с большим количеством различных настроек. Мы можем задавать объекту определенные характеристики, по типу физики, цвета, положения в пространстве и т. д. Также можно производить различные манипуляции с окружением с помощью UI и различных скриптов.

| Plugin | README |
| ------ | ------ |
| Dropbox | [plugins/dropbox/README.md][PlDb] |
| GitHub | [plugins/github/README.md][PlGh] |
| Google Drive | [plugins/googledrive/README.md][PlGd] |
| OneDrive | [plugins/onedrive/README.md][PlOd] |
| Medium | [plugins/medium/README.md][PlMe] |
| Google Analytics | [plugins/googleanalytics/README.md][PlGa] |

## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**
