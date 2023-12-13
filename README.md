# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #1 выполнил:
- Климченко Александр Викторович
- РИ-221110
Отметка о выполнении заданий (заполняется студентом):

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * | 60 |
| Задание 2 | * | 40 |

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
- Задание 1.
- Предложите вариант изменения найденных переменных для 10 уровней в игре. Визуализируйте изменение уровня сложности в таблице.
- Задание 2.
- Создайте 10 сцен на Unity с изменяющимся уровнем сложности.
- Выводы.
- ✨Magic ✨

## Цель работы
- Разработать оптимальный баланс для десяти уровней игры Dragon Picker
## Задание 1
### Предложите вариант изменения найденных переменных для 10 уровней в игре. Визуализируйте изменение уровня сложности в таблице.

Для начала балансировки уровней сложности игры необходимо найти переменные, которые отвечают за следующие действия:
- Скорость полета дракона
- Время сбрасывания яиц
- Вероятность смены траектории движения дракона

За скорость полета дракона отвечает переменная: speed - изначально принимает значение 1.
За время сбрасывания яиц отвечает переменная: timeBetweenEggDrops - изначально принимает значение 2f.
За вероятность смены траектории движения дракона отвечает переменная: chanceDirection - изначально принимает значение 0.1f.

Для изменения уровня сложности в игре будем использовать только 2 переменные - это speed и timeBetweenEggDrops. Т.к. высокое изменение шанса смены траектории может привести к тому, что дракон будет летать почти на одном месте не имея возможности доходить до края сцены.
Будем изменять их путем увеличения скорости дракона и уменьшением времени выпадения яиц.

Для удобства балансировки уровней сложности будем пользоваться google-таблицей: https://docs.google.com/spreadsheets/d/1xlOLFoLdL8WmR_Wfr1O2coetuNa7ggM-lUfVjUGEf4o/edit?usp=sharing

Скорость будет принимать значения от 1 до 5,5.

Время выпадения будет изменяться от 2f до 0,8f.

## Задание 2
### Создайте 10 сцен на Unity с изменяющимся уровнем сложности.

Для выполнения данного задания я решил изменять уровень сложности в игре не прибегая к выбору. Скорость перемещения дракона и скорость выпадения яиц меняется в зависимости от набранных очков игроком.

Чтобы реализовать затеянное я добавил в файл EnemyDragon переменную score, которая играет роль условия, при выполнении которого меняется уровень сложности (Для тестирования был установлен шаг в 5 очков). Далее создаем дочерний класс, в котором считываем значения sqore из файла EnergyShield. И в FixedUpdates проверяем условие для увеличения уровня сложности и меняем его при выполнении.

В файле EnergyShield для корректной работы требуется добавить ссылку на класс EnemyDragon и в OnCollisionEnter передавать значение sqore в EnemyDragon.
В ходе работы я столкнулся с проблемой, что у дракона не менялась скорость при набранном количестве очков. Даннуюю проблему я смог решить путем поиска объекта EnemyDragon в сцене и тогда код стал работать без багов.

Далее будет приведен фрагмент кода из файла EnemyDragon с комментариями.

```c#
public int score = 0; // добавляем переменную score
private void FixedUpdate() {
        if (Random.value < chanceDirection){
            speed *= -1;
        }

        if(score == 5){
            speed = 7f; //изменение скорости дракона
            timeBetweenEggDrops = 1.8f; //изменение времени выпадения яиц
        }

        else if(score == 10){
            speed = 10f;
            timeBetweenEggDrops = 1.6f; 
        }

        else if(score == 15){
            speed = 12f;
            timeBetweenEggDrops = 1.4f; 
        }

        else if(score == 20){
            speed = 15f;
            timeBetweenEggDrops = 1.2f; 
        }

        else if(score == 25){
            speed = 18f;
            timeBetweenEggDrops = 1f; 
        }

        else if(score == 30){
            speed = 20f;
            timeBetweenEggDrops = 0.95f; 
        }

        else if(score == 35){
            speed = 22f;
            timeBetweenEggDrops = 0.9f; 
        }

        else if(score == 40){
            speed = 25f;
            timeBetweenEggDrops = 0.85f; 
        }

        else if(score == 45){
            speed = 30f;
            timeBetweenEggDrops = 0.8f; 
        }
        
    }

    public void AddScore(int points){ // метод для добавления очков
        score += points;
    }

```

Так же будут приведены фрагменты кода из файла EnergyShield.

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using TMPro;

public class EnergyShield : MonoBehaviour
{
    public TextMeshProUGUI scoreGT;
    public AudioSource audioSource;
    public EnemyDragon enemyDragon; // добавляем ссылку на объект EnemyDragon

    void Start() {
        GameObject scoreGO = GameObject.Find("Score");
        scoreGT = scoreGO.GetComponent<TextMeshProUGUI>();
        scoreGT.text = "0";
        enemyDragon = FindObjectOfType<EnemyDragon>(); // ищем объект EnemyDragon в сцене
    }

    void Update()
    {
        Vector3 mousePos2D = Input.mousePosition;
        mousePos2D.z = -Camera.main.transform.position.z;
        Vector3 mousePos3D = Camera.main.ScreenToWorldPoint(mousePos2D);
        Vector3 pos = this.transform.position;
        pos.x = mousePos3D.x;
        this.transform.position = pos;
    }

    private void OnCollisionEnter(Collision coll) {
        GameObject Collided = coll.gameObject;
        if (Collided.tag == "Dragon Egg"){
            Destroy(Collided);
        }
        int score = int.Parse(scoreGT.text);
        score += 1;
        scoreGT.text = score.ToString();
        enemyDragon.AddScore(score); // вызываем метод для добавления очков
        audioSource = GetComponent<AudioSource>();
        audioSource.Play();
    }
}

```

## Выводы
По итогам данной лабораторной работы получилось забалансить 10 уровней сложности для игры Dragon Picker.


## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**
