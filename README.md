# Анализ соответствия стратегий социально-экономического развития регионов методическим рекомендациям Минэкономразвития России при помощи глубоких нейронных сетей

_Введение_. 
Стратегия социально-экономического развития региона является ключевым документом, определяющим целеполагание на долгосрочный период. Грамотно разработанная стратегия обеспечивает эффективное распределение ограниченных ресурсов и сфокусированное решение проблем, способствует реализации инвестиционных проектов, снижает дифференциацию уровня жизни населения, направляет инфраструктурное развитие.

Новый виток разработки стратегий начался в связи с принятием Федерального закона от 28.06.2014 № 172-ФЗ «О стратегическом планировании в Российской Федерации» (далее – _Закон № 172-ФЗ_). На основе верхнеуровневых правил, содержащихся в Законе, регионы стали разрабатывать собственные стратегии.

Однако через 3 года вышел Приказ Минэкономразвития России от 23.03.2017 № 132 «Об утверждении Методических рекомендаций по разработке и корректировке стратегии социально-экономического развития субъекта Российской Федерации и плана мероприятий по ее реализации» (далее – _Методрекомендации_), устанавливающий требования к единообразному содержанию региональных стратегий. При этом стратегии не в полной мере приводились в соответствие с указанными Методрекомендациями. Поэтому отсутствует единый методологический подход к пониманию сущности и базовых элементов региональных стратегий [1].

_Цель исследования_ – разработать программное обеспечение для выявления уровня соответствия региональных стратегий требованиям Методрекомендаций к разделам и Закону № 172-ФЗ на основе использования глубоких нейронных сетей.

_Задачи исследования_:
1. Собрать и подготовить датасет из региональных стратегий
2. Подобрать оптимальную модель для обработки русскоязычных юридических текстов
3. Выбрать метрики для оценки качества работы моделей
4. Дообучить выбранную модель на основе собранного датасета
5. Интерпретировать результат сопоставления стратегий и Методрекомендаций

## Данные
### Источники данных:
1. сайты субъектов РФ;
2. [ГАС «Управление»](https://gasu.gov.ru/stratdocuments?docLevel=2&docType=105&docType=406);
3. СПС «КонсультантПлюс: Регионы» (доступен в библиотеках, торренте);
4. [сайт Минэкономразвития](https://economy.gov.ru/material/directions/regionalnoe_razvitie/strategicheskoe_planirovanie_prostranstvennogo_razvitiya/strategii_socialno_ekonomicheskogo_razvitiya_subektov_rf/).

### [Обработка данных](https://github.com/DDubrovina/Strategy_analysis/blob/main/Code/%D0%9E%D0%B1%D1%80%D0%B0%D0%B1%D0%BE%D1%82%D0%BA%D0%B0%20%D0%B4%D0%B0%D0%BD%D0%BD%D1%8B%D1%85.ipynb):
1. Скачать файлы в едином формате, например, docx.
2. Привести заголовки к одному формату и удалить разрывы строк.
3. Формирование оглавления внутри файла. Также предлагалось использовать скрипт для этого, но в связи с разными подходами регионов к формированию наименования разделов («Раздел 1», «I»,  «СЦ-1», «Глава 1» и др.) использование одного форматирования не является корректрным.
4. Перевод наименования файлов на транслит.
5. Конвертация docx в txt.
6. Чистка документа от лишних символов и фраз.

### Датасет:
1. [Оглавления стратегий в формате csv](https://github.com/DDubrovina/Strategy_analysis/tree/main/Dataset/tables);
2. [Стратегии социально-экономического развития субъектов РФ в формате docx](https://drive.google.com/drive/folders/1A5vJgGFxsaFxPyATsJCG8ewWcE69brDc);
3. [Стратегии социально-экономического развития субъектов РФ в формате txt](https://drive.google.com/drive/folders/1Wa53YLbnCP0qZ41lefDaccj8447RLhsn);

### Дополнительные файлы:
1. ["Эталонное" оглавление](https://github.com/DDubrovina/Strategy_analysis/blob/main/Dataset/Etalon.csv), составленное на основе Закона № 172-ФЗ и Методрекомендаций.
2. [Промты](https://github.com/DDubrovina/Strategy_analysis/blob/main/Dataset/Promt.csv), составленные с учетом "эталонного" оглавления, в формате: Есть ли в <данном тексте> следующая информация: <>.

### [Размеченные данные](https://github.com/DDubrovina/Strategy_analysis/tree/main/Dataset/markup)
Обучение (16) - тест (2) - валидация (2)

## Методология
Для достижения целей настоящего исследования решались две задачи: 
1) классификация – сопоставление «эталонного» оглавления, составленного на основе требований Методрекомендаций и Закона № 172-ФЗ, с положениями содержания стратегий;
2) генерация – формат «вопрос-ответ» о наличии определенных пунктов. 

### Bert-подобные модели и работа с оглавлением для решения задачи классификации
#### Модели
1. ruBert-base - 768
2. ruRoberta-large - 1024
3. rubert-tiny - 312
4. rubert-tiny2 - 312

#### Метрика - косинусовая мера 
(КАК ВСТАВИТЬ ФОРМУЛУ)

#### Выбор оптимальной модели (ВСТАВИТЬ РИСУНКИ)
Каждой строке (пункту оглавления) соответствует 18 косинусных мер сравнения с «эталонным» оглавлением (табл. 1). В одних случаях максимальное значение верно указывает на соответствующий «эталонный» раздел, в других – нет. Рассчитав точность, мы получили медиану в 16% (рис. 1), что выше случайной, но не намного. Причиной такой низкой точности в том числе является то, что при разметке возник класс -1 в тех случаях, когда похожего пункта в «эталонном» оглавлении не существует, т.к. субъекты вправе самостоятельно расширять содержание стратегий. Однако после подсчета точности только по значимым пунктам было получено медианное значение в 77% (рис. 2). Каждый раз rubert-tiny2 показывает лучший результат. Вследствие этого для базового решения было решено «отсечь» класс -1, т.е. выбрать граничное значение косинусной меры, при котором данная строка будет считаться незначимой.

Выбираем по модели tiny2, всё, что выше минимального значения косинусной меры по значимым классам (нижний край “уса” у самого правого ящика), оставляем для анализа, имея ошибку на части комбинаций (часть верхнего  “уса” tiny2_min от его верхней части до уровня низа “уса” tiny2_max).

### Модели кодировщик-декодировщик и работа с основным текстом
На основе Open LLM Leaderboard была выбрана опенсорсная модель TheBloke/OpenBuddy-Llama2-13B-v11.1-GPTQ, которая работает на хорошем уровне с русским языком и показывает наиболее оптимальные результаты, не сильно хуже по многим тестам, например, чем 70B.

Благодаря созданному оглавлению проходимся в цикле по всем документам, бьем их на части между пунктами оглавления и к каждому такому пункту задаем 18 вопросов, соответствующих «эталонному» содержанию. По размеченным документам проверяем совпадение (т.е. 2 запуска, в одном из которых считается точность). Алгоритм обработки стратегий в виде псевдокода:
Создать словарь_соответствий, такой, что названию региона соответствует текст стратегии и таблица оглавления.
По стратегии в списке_стратегий:
    По оглавлению_стратегии в стратегии (из словаря_соответствий):
    По эталонному_вопросу в списке_промтов:
    	Инференс модели OpenBuddy-Llama2-13B-v11.1-GPTQ
    	Сохранение ответов
    Проверка совпадений (при наличии разметки).
