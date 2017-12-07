## Задание 2

### Кртакое описание задания

 1. Считать данные из `training.csv`. Проверить является ли ряд стационарным в широком смысле. Это можно сделать двумя способами: 
    1. Провести визуальную оценку, отрисовав ряд и скользящую статистику(среднее, стандартное отклонение). Построить график на котором будет отображен сам ряд и различные скользящие статистики.
    2. Провести тест Дики - Фуллера.
    Сделать выводы из полученных результатов. Оценить достоверность статистики. 
  3. Разложить временной ряд на тренд, сезональность, остаток в соответствии с аддитивной, мультипликативной моделями. Визуализировать их, оценить стационарность получившихся рядов, сделать выводы. 
  4. Проверить является ли временной ряд интегрированным порядка `k`. Если является, применить к нему модель ARIMA, подобрав необходимые параметры с помощью функции автокорреляции и функции частичной автокорреляции. Выбор параметров обосновать. Отобрать несколько моделей. Предсказать значения для тестовой выборки. Визуализировать их, посчитать `r2 score` для каждой из моделей. Произвести отбор наилучшей модели с помощью информационного критерия Акаике. Провести анализ получившихся результатов. 
  
Дополнительные баллы: соблюдение PEP8, использование для визуализации библиотек bokeh или seaborn.

### Теоритическая часть

  #### Основные определения
  Временной ряд - совокупность наблюдений экономической величины в различные моменты времени. Будем рассматривать временной ряд как выборку из последовательности случайных величин Х(t), t - принимает целочисленные значения от 1 до Т.
  Будем говорить, что процесс стационарен в широком смысле, если случайный процесс таков, что у него мат. ожидание и дисперсия существуют и не зависят от времени, а автокорреляционная функция зависит только от разности значений (t1-t2). 
  Рассмотрим приращения X(t) - X(t-1). Назовем его первой разностью, которую можно рассматривать как другой временной ряд. Таким образом, если мы возьмем первую разность нестационарного ряда, то в результате можем получить стацонарный ряд. Если полученный ряд все же не будет стационарным, то рассмотрим вторую разность первоначального ряда и проверим его на стационарность и т.д. Колличество таких итерация назовем к и будем говорить, что начальный ряд является интегрированным порядка к.
  
  Далее введем определения тренда(T), сезонности(S) и остатков(R).
  Тренд - основная тенденция изменения временного ряда.
  
  Сезонность - периодически колебания, наблюдаемые на временных рядах. Сезонность характерна для экономических временных рядов, реже она встречается в научных данных.
  
  Остатки - это компонента временного ряда, остающаяся после удаления тренда и сезонности.
  
  Временной ряд имеет единичный корень, или порядок интеграции один, если его первые разности образуют стационарный ряд.
  
  #### Модели разложения временного ряда.
 
  Аддитивная модель: Y = T + S + R
  
  Мультипликативная модель: Y = T * S * R
  
  Для рассматриваемого временного ряда эффективнее использовать аддитивную модель: для такой модели более ярко выраженный тренд, меньше разброс сезонность и остатков.
  
  #### Тест Дики-Фуллера
  Тест Дики-Фуллера является средством проверки стационарности временного ряда.
  Нулевая гипотеза H0: g = 0 (существует единичный корень, ряд нестационарный).
  Альтернативная гипотеза: H1: g < 0 (единичного корня нет, ряд стационарный).
  Отвергаем H0 на N процентном (N=1%, 5%, 10%) уровне значимости.
  
  *Тест Дики-Фуллера* используется для проверки ряда на стационарность. Он проверяет ряд на наличие так называемых единичных корней.
  
  Временной ряд имеет *единичный корень* (хотя бы один), если его первые разности образуют стационарный ряд.

  y(t) ~ I(1), т.е. Δy(t)=y(t)-y(t-1) ~ I(0), где Δ - разностный оператор, I(j) - означает, что ряд является интегрированным порядка j, I(0) - ряд стационарен. Определение интегрированности порядка k дается далее.

  Вообще говоря, тест Дики-Фуллера проверяет значение коэффициента `a` в авторегрессионном уравнении 1-го порядка. Оно имеет вид:

  y(t) = a * y(t-1) + ε(t), ε(t) - ошибка
* a=1   => есть единичные корни => стационарности нет
* |a|<1 => нет единичных корней => есть стационарность
* |a|>1 => не свойственно для временных рядов, которые встречаются в реальной жизни - требуется более сложный анализ

Преобразуем уравнение:

y(t) = a * y(t-1) + ε(t) => y(t) - y(t-1) = a * y(t-1) - y(t-1) + ε(t) => Δy(t) = (a-1) * y(t-1) + ε(t) => Δy(t) = b * y(t-1) + ε(t), где b=a-1 - для удобства

* Основная гипотеза: H0: b=0 - процесс нестационарен
* Альтернативаная гипотеза: H1: b<0 - процесс стационарен

Cравниваем найденное с помощью функции `sm.tsa.adfuller(ts)` `p-value` - достоверность статистики с `critical_values` - критическими значениями с уровнем значимости 5%:
* `p-value` > `critical_values` => единичный корень есть и ряд не стационарен
* Иначе стационарен

Достоверность статистики (`p-value`) - мера уверенности в "истинности" результата. Чем p-value меньше, тем больше доверия. Как только p-value становится больше опредленного параметра - `critical_values`, то мы понимаем, что доверять нельзя и говорим, что единичные корни есть.

Уровень значимости показывает (обычно в процентном выражении) степень отклонения от гипотезы. Грубо говоря, если наша достоверность превысила, скажем, 5% уровень значимости, то гипотеза отвергается => процесс нестационарен.
  
  #### ARIMA и SARIMAX
  
  *ARIMA (англ. autoregressive integrated moving average)*- интегрированная модель авторегрессии — скользящего среднего — модель и методология анализа временных рядов. 
  
  *Авторегрессионная (AR-) модель (англ. autoregressive model)* — модель временных рядов, в которой значения временного ряда в данный момент линейно зависят от предыдущих значений этого же ряда.
  
  *Модель скозящего среднего (MA, moving average model)* - модель, в которой моделируемый уровень временного ряда можно представить как линейную функцию прошлых ошибок, т.е. разностей между прошлыми фактическими и теоретическими уровнями.
  
  `ARIMA(p, d, q) = AR(p) + MA(q) + I(d)` , где I(k) - интегрируемый ряд порядка k
  
  
    
