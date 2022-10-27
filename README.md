# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #3 выполнил(а):
- Оглоблин Глеб Александрович
- РИ210941
Отметка о выполнении заданий (заполняется студентом):

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * | 60 |
| Задание 2 | * | 20 |
| Задание 3 | # | 20 |

знак "*" - задание выполнено; знак "#" - задание не выполнено;

Работу проверили:
- к.т.н., доцент Денисов Д.В.
- к.э.н., доцент Панов М.А.
- ст. преп., Фадеев В.О.

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

## Цель работы
познакомиться с программными средствами для создания системы машинного обучения и ее интеграции в Unity.

## Задание 1
### Реализовать систему машинного обучения в связке Python - Google-Sheets – Unity
-	Создал новый пустой 3D проект на Unity.
- добавил в проект ML Agent
- в Anaconda Prompt скачал mlagents 0.28.0 и torch 1.7.1
- создал на сцене плоскость, куб, сферу, простой C# скрипт-файл RollerAgent и подключил его к сфере
![image](https://user-images.githubusercontent.com/79518116/198325825-d96e1ac5-b285-4b85-ad0b-3fa46ab63ba3.png)

- RollerAgent.cs 
```C#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Unity.MLAgents;
using Unity.MLAgents.Sensors;
using Unity.MLAgents.Actuators;

public class RollerAgent : Agent
{
    Rigidbody rBody;
    // Start is called before the first frame update
    void Start()
    {
        rBody = GetComponent<Rigidbody>();
    }

    public Transform Target;
    public override void OnEpisodeBegin()
    {
        if (this.transform.localPosition.y < 0)
        {
            this.rBody.angularVelocity = Vector3.zero;
            this.rBody.velocity = Vector3.zero;
            this.transform.localPosition = new Vector3(0, 0.5f, 0);
        }

        Target.localPosition = new Vector3(Random.value * 8-4, 0.5f, Random.value * 8-4);
    }
    public override void CollectObservations(VectorSensor sensor)
    {
        sensor.AddObservation(Target.localPosition);
        sensor.AddObservation(this.transform.localPosition);
        sensor.AddObservation(rBody.velocity.x);
        sensor.AddObservation(rBody.velocity.z);
    }
    public float forceMultiplier = 10;
    public override void OnActionReceived(ActionBuffers actionBuffers)
    {
        Vector3 controlSignal = Vector3.zero;
        controlSignal.x = actionBuffers.ContinuousActions[0];
        controlSignal.z = actionBuffers.ContinuousActions[1];
        rBody.AddForce(controlSignal * forceMultiplier);

        float distanceToTarget = Vector3.Distance(this.transform.localPosition, Target.localPosition);

        if(distanceToTarget < 1.42f)
        {
            SetReward(1.0f);
            EndEpisode();
        }
        else if (this.transform.localPosition.y < 0)
        {
            EndEpisode();
        }
    }
}
```
- Сфере добавил компоненты Rigidbody, Decision Requester, Behavior Parameters и настроил их
![image](https://user-images.githubusercontent.com/79518116/198326037-6e98f728-ab7b-494b-8073-ea2d51076328.png)

- В корень проекта добавил файл конфигурации нейронной сети
```yaml
behaviors:
  RollerBall:
    trainer_type: ppo
    hyperparameters:
      batch_size: 10
      buffer_size: 100
      learning_rate: 3.0e-4
      beta: 5.0e-4
      epsilon: 0.2
      lambd: 0.99
      num_epoch: 3
      learning_rate_schedule: linear
    network_settings:
      normalize: false
      hidden_units: 128
      num_layers: 2
    reward_signals:
      extrinsic:
        gamma: 0.99
        strength: 1.0
    max_steps: 500000
    time_horizon: 64
    summary_freq: 10000
```
-	Запустил работу ml-агента
![image](https://user-images.githubusercontent.com/79518116/198325459-003d7af3-48cd-4a52-950e-beb81d8ac981.png)

- сделал 3, 9, 27 копий модели Плоскость-Сфера-Куб, запустил симуляцию сцены
![2022-10-27-18-35-38](https://user-images.githubusercontent.com/79518116/198326504-65d05a9e-56ba-4831-9417-9ea6f7a0c883.gif)

![2022-10-27-18-51-09](https://user-images.githubusercontent.com/79518116/198326736-db1cf1ac-8dc2-450c-9272-38dfb9a2c013.gif)


![2022-10-27-19-36-47](https://user-images.githubusercontent.com/79518116/198326787-d5f6fd1d-32fe-4f75-b2c4-9a460b8ad9fb.gif)

![image](https://user-images.githubusercontent.com/79518116/198327376-5291606d-f9a8-4da9-8377-5dfafc1d93e1.png)

-

## Задание 2 
### Реализовать запись в Google-таблицу набора данных, полученных с помощью линейной регрессии из лабораторной работы № 1

## Задание 3
### Самостоятельно разработать сценарий воспроизведения звукового сопровождения в Unity в зависимости от изменения считанных данных в задании 2


## Выводы

- В ходе лабораторной работы мы научились загружать в таблицу данные с помощью Python и выгружать их и использовать с помощью C# и Unity

## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**
