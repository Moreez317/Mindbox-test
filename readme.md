# Тестовое задание для Mindbox

## Пройдемся по заданию:

> *у нас мультизональный кластер (три зоны), в котором пять нод*

Вроде все понятно

> *приложение требует около 5-10 секунд для инициализации*

Окей, добавим readiness пробу что бы точно быть уверенным, что поды поднялись и в нее добавим `initialDelaySeconds: 5`

> *по результатам нагрузочного теста известно, что 4 пода справляются с пиковой нагрузкой*

Запомним

> *на первые запросы приложению требуется значительно больше ресурсов CPU, в дальнейшем потребление ровное в районе 0.1 CPU. По памяти всегда “ровно” в районе 128M memory*

В `resource request` нашего деплоя зададим:
```
cpu: 0.1
memory: 128Mi
```
Так же это необходимо для HPA, но о нем ниже

> *приложение имеет дневной цикл по нагрузке – ночью запросов на порядки меньше, пик – днём*

Уже сложнее, гуглим и находим [решение](https://github.com/dignajar/another-autoscaler) этого вопроса 
```
another-autoscaler/start-time: "00 9 * * *"
another-autoscaler/start-replicas: "3"
another-autoscaler/stop-time: "00 21 * * *"
another-autoscaler/stop-replicas: "1" 
```
> *хотим максимально отказоустойчивый deployment*

А вот тут мои полномочия все...

> *хотим минимального потребления ресурсов от этого deployment’а*

Ясно, создадим `HorizontalPodAutoscaler`, который в случае высокой нагрузки будет добавлять поды (максимум 4, минимум 1), а в случае низкой - наоборот убивать лишние, так же ночью будем уменьшать количество запущенных реплик (двумя пунктами выше описывается этот процесс)

---

*В целом тестовое задание стало результатом 3-4 часов гуглинга и курения манов (ранее меня с кубернетесом роднил только один параграф в курсовой, которую я писал), так что этот опыт уже был интересен, рад был бы стажироваться в вашей компании :)*
