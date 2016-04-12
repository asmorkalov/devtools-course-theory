# Автоматизация

Сергей Носов (Itseez)

11 Октября 2014

<!-- TODO
  - Вставить картинку-эпиграф
  - Примеры
    - Составная команда
    - Bash
    - Python: csv, matplotlib (print pdf, excel, twitter)

  - Человеческий фактор в автоматизации (пример vCount)
  - Использовать материал из книг Power Unix Tools
  - Может быть показать генерацию Excel и картинок на Python
-->

# Содержание

  - Введение
  - Автоматизация в деятельности программиста
    - Общие понятия
    - Bash
    - Python
  - Демо

# Автоматизация человеческой деятельности

  - Биологическая
  - Механическая
  - Электро-механическая
  - Компьютерная / информационная

> Есть мнение, что всё, что _теоретически_ может быть автоматизировано, может
> быть автоматизировано _практически_.

# Выгоды автоматизации

  - Мощность / повышение уровня абстракции
    - Фокус на основной задаче
    - Меньшее количество переключений контекста
  - Надежность
    - Отсутствие человеческого фактора
  - Скорость
  - Дешевизна
    - Особенно при многократном запуске
  - Освобождение времени

> Главное: концентрация на том, что __действительно__ важно.

# Пример: Слепая печать

  - Обладает всеми описанными выгодами
  - Важнейший навык человека, работающего за компьютером
    - Почта, чат
    - Код
    - Статьи, документы

# Автоматизация в деятельности программиста

  - Сбор и обработка данных
    - Составление отчетов/графиков, анализ и обработка больших массивов данных
  - Тестирование
    - Изменения ничего не сломали, анализ производительности
  - Администрирование
    - Регулярные процедуры (бэкап), сложное конфигурирование
  - Собственно автоматизация
    - Построение дистрибутивов, развертывание

# UNIX - OS для разработчиков

  - Огромное количество утилит на любой случай жизни
    - find, cron, tar, sed, etc.
  - Вместе со скриптовыми языками (Bash, Perl, Python, etc)
    предоставляет широчайшие возможности для автоматизации

_Замечание: У любителей Windows нет поводов для расстройств!_

# Пример: Составные команды - философия

  Doug McIlroy

  - This is the Unix philosophy:
    - Write programs that do one thing and do it well.
    - Write programs to work together.
    - Write programs to handle text streams, because that is a universal
      interface.

# Пример: Составные команды

Общая схема:
```bash
        program1 | program2 | program3
```

Занимаемое место в директории:
```bash
du -sh * | sort -hr
```

Удаление пробелов в конце строки и замена tab'ов на 4 пробела:
```bash
cp main.cpp main.cpp.bak && cat main.cpp.bak | sed 's/\t/    /g' | sed 's/\s*$//' > main.cpp
```

Количество просмотров видео на YouTube:
```bash
curl "https://gdata.youtube.com/feeds/api/videos/M9ycFnR_V4I?v=2&alt=json" | \
    sed 's/\(.*\)"viewCount":"\([^"]*\)"\(.*\)/\2/'
```

# Пример: bash

  - "Родной" скриптовый язык для большинства современных систем.
  - Хороший инструмент для использования средств операционной системы.
  - Не очень хороший инструмент для решения прикладных задач.

# Пример: bash — добавление ведущих нулей

```bash
    #!/usr/bin/env bash

    ext=jpg
    for fn in $(ls *.$ext -1)
    do
        i=${fn%.*} # strip extension
        mv $fn $(printf "%04d.$ext" $i) # add leading zeros
    done
```

# Пример: python

  - Один из наиболее популярных скриптовых языков
  - Является вторым родным для большинства систем
  - Применяется для широчайшего круга прикладных задач
  - Обладает большим количеством библиотек
  - Достаточно выразителен и внешне красив

# Пример: python — анализ данных

  В файле находится информация о количестве просмотров ролика с YouTube в
  следующем формате:

    Fri 10 Oct 2014 07:37:10 AM UTC
    9524

    Fri 11 Oct 2014 07:36:48 AM UTC
    16234

    Fri 12 Oct 2014 07:36:56 AM UTC
    25890

  - На первой строке — дата и время
  - На второй — количество просмотров на указанное время
  - Третья строка — пустая и служит для разделения записей

# Пример: python — чтение данных

```python
    with open(sys.argv[1], "r") as f:
        lines = [str.strip(line) for line in f.readlines()]
        dates = []
        views = []
        for i in range(0, len(lines), 3):
            date = " ".join(str.split(lines[i])[1:4])
            dates.append(datetime.datetime.strptime(date, "%d %b %Y"))
            views.append(int(lines[i+1]))
```

# Пример: python — запись в .csv

```python
    with open('data.csv', 'w') as f:
        csv.writer(f, delimiter=',').writerows(zip(dates, views))
```

# Пример: python — построение графиков

```python
    plt.gca().xaxis.set_major_formatter(mdates.DateFormatter('%Y-%m-%d'))
    plt.gca().xaxis.set_major_locator(mdates.WeekdayLocator())
    plt.plot(dates, views)
    plt.show()
```

# Пример на автоматизацию

## Контекст

  - Вы занимаетесь сбором тестовых данных для компании,\
    разрабатывающей ПО для предсказания популярности видео-роликов про котов.
  - Входными данными для программы-классификатора являются:
    - _черно-белые кадры_ ролика
    - разрешением _320х240_
    - сохраненные покадрово в формате _JPEG_
  - Программе обучения требуется также знать:
    - _дату загрузки_ видео
    - _количество просмотров_ на начало каждого дня

# Простор для автоматизации

  - Что является неавтоматизируемой частью работы?
    - Выбор роликов про котов
  - Что является автоматизируемой частью работы?
    - Всё остальное

# Как должен выглядеть рабочий процесс?

  - В течение рабочего дня вы просматриваете ролики на YouTube в поисках
    интересных роликов про котов.
  - Подходящие ролики скачиваются, обрабатываются и добавляются в базу — одним
    нажатием кнопки.
  - В начале каждого дня для каждого ролика в базу добавляется текущая
    информация о количестве просмотров, без вашего участия.

# Ключевые моменты

  - Компьютерные системы имеют огромнейшие возможности для автоматизации — ими
    нужно овладеть и пользоваться.
  - Автоматизированные шаги становятся вашими инструментами, используя которые
    вы можете решать более сложные задачи быстрее.

> Главное: деятельность профессионала заключается в поиске автоматизируемых
> частей и, собственно, их автоматизации.

# Контрольные вопросы

  1. Какие преимущества дает автоматизация
  2. Типичные классы задач на автоматизацию (в работе программиста)
  3. Философия UNIX
  4. Преимущества UNIX при автоматизации
  5. Суть деятельности профессионального программиста

# Спасибо за внимание!

Вопросы?