Задача - улучшение Baseline

Описание моделей, вариантов экспериментов и результы метрик.

Модель 1: Наивная модель
На этапе постороения "наивной" модели пробовала добавить свои данные из Progect_6, но это ничего не дало, отказалась от использовния своих данных в дальнейшем.
Изменила признак model. В model соединила brand и первые буквенные обозначения из model_info. Так получается меньше пропусков и модель работает лучше.
MAPE - 21.33%
Модель 2: CatBoostRegressor
Небольшое улучшение дало укрупнение color. Некоторые цвета типа: фиолетовый, желтый, золотистый переименованы в "редкий цвет".
На boxplot виден характер выбросов в mileage. Такие данные заменять на среднее не правильно, для авто это значимый показатель. Разметила на две категории с нормальным пробогом и слишком большим.
Аналогично разметила на две группы по показателю "пробег за год". Это тоже хороший показатель при покупке авто.
Пустое значение в "объеме двигателя" заменила на среднее значение по двигателям - 2.8.
Количественные признаки логарифмируются.
Перепробовала разные виды scaler (StandartScaler, RobustScaler, Normalizer), улучшения не получила. Оставила, как есть MinMaxScaler.
Модель дала хорошее улучшение метрики.
MAPE - 11.13%
Модель 3: GradientBoostingRegressor
Построила модель градиентного бустинга: Взяла эту модель, так как в прошлой своей работе Progect_6 она была лучшей. Данные схожи, поэтому решила использовать ее и здесь.
Результат хороший, но по сравнению с catboost чуть ниже.
MAPE - 11.89%
Blend
Комбинация catboost и gradientboosting моделей в лоб улучшения метрики не дает, но на Kaggle улучшение есть. Поэтому эту модель оставляю.
MAPE - 11.32%.
Модель 4: Simple Dense NN
Результат хуже предыдущего. дальше не используем.
MAPE - 15.89%
Model 5: NLP + Multiple Inputs
В модели использовала предобработку текста.
Пробовала использовать stemming для обработки текстов, но результат не понравился. Убрала этот вариант.
Составлен pattern с символами, не несущими информации, но сильно засоряющими текст. При помощи метода re.sub текст очищен от этих символов.
При помощи метода pymorphy2.MorphAnalyzer, morph.normal_forms текст скорректирован.
Это все дало уменьшение размерa словаря tokenize.word_index с 39 тыс. до 31 тыс. слов.
Результат лучше, чем просто нейронная сеть, но чуть хуже, чем catboost.
MAPE - 12.08%
Blend
Пробуем комбинацию моделей catboost, gradientboosting и NLP
Лучший результат.
Одинаковую метрику дают как комбинация catboost и NLP, так и всех трех. Оставляю оба варианта.
MAPE - 10.80%
Kaggle - 11.61031
Model 6: Добавление картинок
Удалось: загрузить картинки, поизменять картинки, перегнать их в цифру, запустить модель. Модель работала слишком долго и на каком-то этапе работа остановилась. Результата нет. Выводы по данной модели: не буду даже пытаться ее использовать из-за неоправданно высоких временных затратах. На своем компе из-за скачка электроэнергии произошел сброс, на Kaggle есть временное ограничение, да и неинтересно сутками ждать очередной итерации.

Выводы:
Лучший результат получился комбинацией моделей catboost и NLP.
Наибольший вклад в построение оптимальной модели предстаказания цены вносят простые методы catboost и gradientboosting. Кроме лучших показателей MAPE они еще и быстродейственны и позволяют за ограниченное время сделать больше экспериментов с предобработкой, позволяя искать варианты для улучшений.
Добавление NLP в лоб улучшения не дает. Но в комбинации с предидущими моделями позволяет улучшить метрику. Удалось получить метрику 10.80%.
Добавление CV убивает всякую мотивацию использовать CV из-за невозможности получить результат в какие-то понятные строки. Слишком дорогостоящий в плане временных затрат метод. В этой задаче я его отбросила.
Если пытаться оценить вклад разных методов в улучшение метрики MAPE, то конечно можно проследить динамику MAPE: от "наивной модели" до простой модели catboost - идет существенное увеличение c 21.33% до примерно 12.5%. За счет дополнительной предобработки удалось улучшить MAPE с 12.5% до 11.13%. NLP дало дополнительное улучшение с 11.13% до 10.80%. Т.е. всего 0.31%.
Применение NLP тоже затратно по времени. Поэтому нужно взвешивать целесообразность и затраты. В данном случае, если мы пытаемся предсказать стоимость автомобиля в премиум-сегменте, то с точки зрения бизнес-задачи получение этих 0.31% скорее всего и не нужно.
Комментарии это отдельная большая тема, могут быть и как в известной сказке про продажу коровы: "А много ль корова дает молока? - Не выдоешь за день, устанет рука!" Подразумевается, что молока то и нет, но зато как звучит. Здесь нужно владеть навыками, кроме приведения слов текста к начальной форме и очищения от мусора, вычленять существенные слова, несущие в себе смысл. Я пыталась просматривать объявления и что-то выбрать эмперически, типа кожа, люк, ксенон, легкосплавные диски, но эти характеристики и так учтены в модификации моделей.
Итоговый результат на kaggle - 11.61 (55 место).
