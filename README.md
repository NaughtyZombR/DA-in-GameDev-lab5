# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #5 выполнил(а):
- Романов Вадим Юрьевич
- РИ210950
Отметка о выполнении заданий (заполняется студентом):

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * | 60 |
| Задание 2 | * | 20 |
| Задание 3 | * | 20 |  

[ну допустим пусть останется 3 задания...]

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
Интеграция экономической системы в проект Unity и обучение ML-Agent.


## Задание 1
### Измените параметры файла. yaml-агента и определите какие параметры и как влияют на обучение модели.
Ход работы:
- Открыл предоставленный проект в Unity и переподключил ML Agents и ML Agents Extensions через Package Manager. С помощью Anaconda Prompt активировал виртуальное пространство и запустил обучение ML-агента.

![Screenshot_4](https://user-images.githubusercontent.com/58142149/204998493-ec238944-6e86-4313-9374-a8f6a546fde5.png)


- После обучения ML-Агента установил в виртуальную среду TensorFlow и открыл TensorBoard, с результатами обучения.

![Screenshot_5](https://user-images.githubusercontent.com/58142149/204998640-330470ca-a01b-4de1-af40-b71e48ea7003.png)

- Стандартными параметрами `Economic.yaml`:

```yaml
behaviors:
  Economic:
    trainer_type: ppo
    hyperparameters:
      batch_size: 1024
      buffer_size: 10240
      learning_rate: 3.0e-4
      learning_rate_schedule: linear
      beta: 1.0e-2
      epsilon: 0.2
      lambd: 0.95
      num_epoch: 3      
    network_settings:
      normalize: false
      hidden_units: 128
      num_layers: 2
    reward_signals:
      extrinsic:
        gamma: 0.99
        strength: 1.0
    checkpoint_interval: 500000
    max_steps: 750000
    time_horizon: 64
    summary_freq: 5000
    self_play:
      save_steps: 20000
      team_change: 100000
      swap_steps: 10000
      play_against_latest_model_ratio: 0.5
      window: 10
```

- По графикам видно, что общее вознаграждение `Cumulative Reward` растёт, потеря значения `Value Loss`, наоборот, уменьшается.

![image](https://user-images.githubusercontent.com/58142149/205001195-d8e8b780-63a8-4fc0-bf63-a26afdf9a11e.png)

_____
__Какие параметры и как влияют на обучение модели:__


### Learning_rate

- `learning_rate` - коэффициент скорости обучения, скорость оптимизации или обучения. Скорость обучения определяет величину изменений, которую оптимайзер может внести за один раз. Нужно подбирать оптимальное значение: если взять слишком большое - модель будет быстрее обучаться, но и шаги станут больше и модель может застрять на месте; если взять слишком маленькое - модель будет заметно дольше обучаться и точно так же может застревать на месте.
___значение следует уменьшать, если обучение нестабильно, а вознаграждение не увеличивается постоянно___
Попробовал увеличить его до `1.0e-4` и обучить модель:

![image](https://user-images.githubusercontent.com/58142149/205082971-6ac7ee77-86e0-4603-9ec9-ced5fd1ea898.png)

- Как видно из скрина, из-за того, что значение увеличилось, сеть "застопарилась" и даже пошла в обратном направлении  
- Уменьшить 'lr' до `1.0e-5` и обучил модель, вот реузльтаты:

![image](https://user-images.githubusercontent.com/58142149/205083223-295d43f3-8db1-4d48-ba76-7ff7022f9fca.png)

График стал чуть лучше, вознаграждение возрастает, хоть и довольно медленно

### Beta
- `beta` - это сила энтропийной регуляризации, которая делает политику агента «более случайной». Это гарантирует, что агенты должным образом исследуют пространство действия во время обучения. Увеличение этого параметра обеспечит выполнение большего количества случайных действий. Параметр должен быть скорректирован таким образом, чтобы Entropy медленно уменьшалась вместе с увеличением вознаграждения. Если энтропия падает слишком медленно, нужно уменьшить beta.

Прошлое обучение

![image](https://user-images.githubusercontent.com/58142149/205084507-e04e55d0-b8f9-488f-aaea-616477ba1bb2.png)

- Изменил на `1.0e-4`

результаты обучения:
![image](https://user-images.githubusercontent.com/58142149/205085080-8f3935db-f1c0-4aff-9cb0-9b049b98fa75.png)

- Вознаграждение за обучение достигло своего пика и перестало расти. При этом `Entropy` начала медленно уменьшаться

![image](https://user-images.githubusercontent.com/58142149/205085663-7e205efd-4304-45f1-8ae9-a15554bb0255.png)


### Epsilon

- `epsilon` влияется на скорость развития политики во время обучения. Соответствует допустимому порогу расхождения между старой и новой политикой при обновлении градиентного спуска. Установка небольшого значения этого параметра приведет к более стабильным обновлениям, но также замедлит процесс обучения.

При увеличении до 0.5 награда оставалась неизменной, число слишком велико. А при маленьком значении, таком как 0.1 - график пришёл в изначальный вид.

### Num_epoch
- `num_epoch` - это параметр, отвечающий за количество проходов через буфер опыта при выполнении оптимизации градиентного спуска. При увеличении этого параметра график начинал вести себя нестабильно, вознаграждение росло гораздо медленее:

![image](https://user-images.githubusercontent.com/58142149/205086912-ba414b14-e868-4014-9b5f-ed6896979756.png)

### Lambd
- `lambd` - этот параметр влияет на то, насколько агент полагается на свою текущую оценку стоимости при вычислении обновленной оценки стоимости. Низкие значения соответствуют большему полаганию на собственную оценку. 

Увеличил до `0.8` Вознаграждение монотонно возрастало, пока не достигло своего максимального значения

![image](https://user-images.githubusercontent.com/58142149/205087203-980cd048-84db-47fd-bb07-2a30da8346cd.png)

____

## Задание 2

- `Cumulative reward` - cреднее вознаграждение за эпизод. Имеет небольшие изменения. В лучшем случае, должно последовательно увеличиваться с течением времени.
- `Episode Length` - средняя продолжительность эпизода в среде для агентов.
- `Policy Loss` - этот график определяет величину изменения политики со временем. Должен стремиться вниз, показывая, что политика всё лучше принимает решения.
- `Value Loss` - это средняя потеря функции значения. Будет увеличиваться по мере увеличения вознаграждения, а после становления стабильного награждения должно уменьшаться.
- `Beta` - график, показывающий изменение силы энтропийной регуляризации, которая делает политику агента «более случайной».
- `Entropy` - соответствует тому, насколько случайны решения, показывает величину исследования агента. Должно последовательно уменьшаться с течением обучения.
- `Learning Rate` - соответствует скорости обучения, будет постепенно уменьшаться с течением времени.
- `Extrinsic Reward` - соответствует среднему совокупному вознаграждению, полученному от окружающей среды за эпизод.
- `Value Estimate` - это среднее значение, посещённое всеми состояниями агента. Чтобы отражать увеличение знаний агента, это значение должно расти, а затем стабилизироваться.

____

## Выводы
- Узнал как можно интегрировать экономическую систему в проект Unity
- Изучил параметры конфигурации модели и их виляние на обучение с помощью анализа графиков в TensorBoard.

## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**

