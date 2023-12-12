# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #2 выполнил(а):
- Ильясова Лейсан Рамилевна
- РИ220936
Отметка о выполнении заданий (заполняется студентом):

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * |  |
| Задание 2 | * |  |
| Задание 3 | * |  |

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
- ✨Magic ✨

## Цель работы
Связать google-таблицы и Unity с использованием Python. 

## Задание 1
### Выберите одну из компьютерных игр, приведите скриншот её геймплея и краткое описание концепта игры. Выберите одну из игровых переменных в игре (ресурсы, внутри игровая валюта, здоровье персонажей и т.д.), опишите её роль в игре, условия изменения / появления и диапазон допустимых значений.
Ход работы:
1. Dead by daylight - многопользовательская ассимметричная (4x1) игра в жанре ужасов, где один игрок берет роль Убийцы и охотится на остальных, Выживших. Их задача - в процессе игры завести пять генераторов, открыть и сбежать в ворота, не дав себя убить. Выжившие не могут навредить Убийце (только ослепить/оглушить на пару секунд), а вот Убийца вполне может убить их несколькими способоами.
2. В Dbd есть своя игровая валюта - Очки Крови, которые используются в магазине, Кровавой Паутине. За них можно покупать различные предметы (аптечки, фонарики, жертвоприношения на выбор карты), которые позже можно взять с собой в матч. Набирются они за выполнение Ежедневных ритуалов или достижения во время матчей (ну, плюс за первое вхождение в игру и прохождение обучения).

![Workshop2](https://github.com/Vedro12/workshop1/assets/127394413/a65173f3-f77f-494c-a5fd-1bed7ec41dc1)


## Задание 2
### С помощью скрипта на языке Python заполните google-таблицу данными, описывающими выбранную игровую переменную в выбранной игре (в качестве таких переменных может выступать игровая валюта, ресурсы, здоровье и т.д.). Средствами google-sheets визуализируйте данные в google-таблице (постройте график, диаграмму и пр.) для наглядного представления выбранной игровой величины.
Ход работы:
1. Настроим доступ к google-таблице по api и напишем код генерации данных с помощью Python. В первом столбце таблицы останется номер генерации, во втором будет количество имеющихся Очков Крови, в третьем - уровень редкости предмета.

| Число | Редкость предмета | Стоимость |
| ------ | ------ | ------ |
| 0 | Нет | 0 |
| 1 | Обычный | 2000 |
| 2 | Необычный | 2500 |
| 3 | Редкий | 3250 |
| 4 | Очень редкий | 4000 |
| 5 | Ультра редкий | 5000 |

```py


import gspread
import numpy as np
gc = gspread.service_account(filename='unityds-404412-c6bf99400c74.json')
sh = gc.open("UnityDS")
price = np.random.randint(0, 5001, 11)
mon = list(range(1,11))
i = 0
while i <= len(mon):
    i += 1
    if i == 0:
        continue
    else:
        bloodpoints = price[i-1]
        item = 0
        if bloodpoints < 2000:
            item = 0
        else: 
            if bloodpoints < 2500:
                item = 1
            else: 
                if bloodpoints < 3250:
                    item = 2
                else: 
                    if bloodpoints < 4000:
                        item = 3
                    else: 
                        if bloodpoints < 5000:
                            item = 4
                        else: 
                            if bloodpoints == 5000:
                                item = 5
        bloodpoints = str(bloodpoints)
        item = str(item)
        sh.sheet1.update(('A' + str(i)), str(i))
        sh.sheet1.update(('B' + str(i)), str(bloodpoints))
        sh.sheet1.update(('C' + str(i)), str(item))
        print(item)



```

2.Передадим данные в таблицу.

![Снимок экрана (527)](https://github.com/Vedro12/workshop1/assets/127394413/6dbd1faf-8726-4550-b1cc-d6663e9e3692)
![Снимок экрана (528)](https://github.com/Vedro12/workshop1/assets/127394413/9797ec76-b558-4151-8b9c-2ed43156e04b)


## Задание 3
### Настройте на сцене Unity воспроизведение звуковых файлов, описывающих динамику изменения выбранной переменной. Например, если выбрано здоровье главного персонажа вы можете выводить сообщения, связанные с его состоянием.
```py

using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Networking;
using SimpleJSON;

public class NewBehaviourScript : MonoBehaviour
{
    public AudioClip goodSpeak;
    public AudioClip normalSpeak;
    public AudioClip badSpeak;
    private AudioSource selectAudio;
    private Dictionary<string, float> dataSet = new Dictionary<string, float>();
    private bool statusStart = false;
    private int i = 1;

    // Start is called before the first frame update
    void Start()
    {
        StartCoroutine(GoogleSheets());
    }

    // Update is called once per frame
    void Update()
    {
        if (dataSet.Count == 0) return;
        if (dataSet["Mon_" + i.ToString()] == 0 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioGood());
            Debug.Log(dataSet["Mon_" + i.ToString()] + " Ничего");
        }

        if (dataSet["Mon_" + i.ToString()] == 1 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioNormal());
            Debug.Log(dataSet["Mon_" + i.ToString()] + " Обычный");
        }

        if (dataSet["Mon_" + i.ToString()] == 2 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioBad());
            Debug.Log(dataSet["Mon_" + i.ToString()] + " Необычный");
        }

        if (dataSet["Mon_" + i.ToString()] == 3 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioBad());
            Debug.Log(dataSet["Mon_" + i.ToString()] + " Редкий");
        }

        if (dataSet["Mon_" + i.ToString()] == 4 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioBad());
            Debug.Log(dataSet["Mon_" + i.ToString()] + " Очень редкий");
        }

        if (dataSet["Mon_" + i.ToString()] == 5 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioBad());
            Debug.Log(dataSet["Mon_" + i.ToString()] + " Ультра редкий");
        }
    }

    IEnumerator GoogleSheets()
    {
        UnityWebRequest curentResp = UnityWebRequest.Get("https://sheets.googleapis.com/v4/spreadsheets/1tUJxM6zZwRuuPkY3p4C4LrTGUM4uDkDSJcPRxKERbQA/values/Лист1?key=AIzaSyCc9IiT3mLkzQEtI-X74romSEE69AckDHY");
        yield return curentResp.SendWebRequest();
        string rawResp = curentResp.downloadHandler.text;
        var rawJson = JSON.Parse(rawResp);
        foreach (var itemRawJson in rawJson["values"])
        {
            var parseJson = JSON.Parse(itemRawJson.ToString());
            var selectRow = parseJson[0].AsStringList;
            dataSet.Add(("Mon_" + selectRow[0]), float.Parse(selectRow[2]));
        }
    }

    IEnumerator PlaySelectAudioGood()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = goodSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(3);
        statusStart = false;
        i++;
    }
    IEnumerator PlaySelectAudioNormal()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = normalSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(3);
        statusStart = false;
        i++;
    }
    IEnumerator PlaySelectAudioBad()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = badSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(4);
        statusStart = false;
        i++;
    }
}

```

![Снимок экрана (529)](https://github.com/Vedro12/workshop1/assets/127394413/7210a962-0aef-4392-8a21-a0edd6a0eea9)
![Снимок экрана (530)](https://github.com/Vedro12/workshop1/assets/127394413/21bef859-de17-4414-bb36-9f252f751e6e)


## Выводы
В процессе выполнения лабораторной работы мы научилис заполнять google-таблицы с помощью Python, а также передавать данные из этой таблицы в Unity.


| Plugin | README |
| ------ | ------ |
| GitHub | [plugins/github/README.md][PlGh] |
| Google Drive | [plugins/googledrive/README.md][PlGd] |

## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**
