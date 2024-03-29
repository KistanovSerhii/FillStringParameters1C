##### <a name="pageup"></a>

## Заполнить параметры строки данными ссылки / Fill String Parameters 1C
   
     1. Входные данные: строка с параметрами и ссылка
     2. Выходные данные: строка где параметры заполнены значениями ссылки

Обработка позволяет заполнить параметры в тексте данными ссылки. 
(есть возможность вывода табличных частей, а также задать форматирование параметра)

---
[Демонстрация YouTube которая поможет вам внедрить и использовать](https://youtu.be/8HTAnS-b22U)

---
***видео готовиться и будет позже***:
[Ссылка на видео демонстрацию похожего функционала БСП](http://example.com)

__Область применения и конкретные кейсы__:

1. Формирование шаблона в справочнике "ДоговорыКонтрагентов" в 1С:Бухгалтерия:
Задача заключалась в предоставлении бухгалтеру возможности
создавать шаблоны в справочнике "ДоговорыКонтрагентов".
Эти шаблоны программно применяются при создании платежного поручения,
определяя назначение платежа согласно содержанию шаблона договора.

2. Уведомление МОЛ о передаче оборудования в нетиповой конфигурации "Инвентаризация компьютеров"
Задача была реализовать почтовое уведомление материально ответственное лицо о передаче оборудования.
В силу отсутствия в конфигурации "БСП", использование данной обработки стало необходимым инструментом
для формирования текста письма для МОЛ.

# Разделы

+ [Рекомендую использовать БСП если это возможно](#step0); ➕
+ [Краткое описание вызова](#step1);  🟣
+ [Пример задачи](#step2); 🔘
+ [Внедрение](#step3); 🪚
+ [Если хотите вызвать без использования БСП](#step4); ➕
+ [ВАЖНО! Если используете расширение](#add0); 🔘

##### <a name="step0"></a> Рекомендую использовать БСП если это возможно [(начало)](#pageup);

Используйте БСП всегда когда это возможно. Подобный механизм БСП называется "Шаблоны сообщений"
и в документации находится "Библиотека стандартных подсистем 3.1.9. Документация"/"Глава 5. Пользовательская документация"/"5.67 Шаблоны сообщений"

      Реализация БСП предназначена для формирования sms сообщения, электронного письма с вложениями печатных форм и документов и т.д.

##### <a name="step1"></a> Краткое описание вызова [(начало)](#pageup);

```
	внешняяОбработкаОбъект = Обработки.ЗаполнитьПараметрыСтрокиДаннымиСсылки.Создать();
		
	ПараметрыЗаполнения = внешняяОбработкаОбъект.ПараметрыЗаполненияШаблона();
	ПараметрыЗаполнения.ИсточникДанных	= Ссылка;
	ПараметрыЗаполнения.СтрокаШаблон	= ШаблонСсылки;
	ПараметрыЗаполнения.ОчиститьПустые 	= Истина;
	
	текстПисьма = внешняяОбработкаОбъект.ПодставитьПараметрыШаблонизированнойСтроки(ПараметрыЗаполнения);

```

##### <a name="step2"></a> Пример задачи [(начало)](#pageup);

Необходимо сформировать текст по шаблону для уведомления о проведении документа увольнение сотрудника
и отправить письмо в отдел безопасности и информационных технологий.

##### <a name="step3"></a> Внедрение [(начало)](#pageup);

1. (если есть БСП) Переходим в модуль обработки, функция "НаборОбъектовИнтерактивногоВызова" и добавляем целевые объекты
2. (необязательно) Переходим в метаданные целевого объекта и добавляем реквизит "ШаблонСсылки" тип Строка неограниченной длины
   (это позволит передавать текущий шаблон из ссылки в обработку и переносить обратно)
3. Добаить обработку в конфигурацию (подключить интерактивно если есть БСП или через конфигуратор)

***Используем программно:***

```
#Область ЗаполнитьПараметрыСтрокиДаннымиСсылки

	#Область ПолучениеВнешнейОбработки_ЗаполнитьПараметрыСтрокиДаннымиСсылки
	   внешняяОбработкаИмя    = "ЗаполнитьПараметрыСтрокиДаннымиСсылки";
	   внешняяОбработкаСсылка = Справочники.ДополнительныеОтчетыИОбработки.НайтиПоРеквизиту("ИмяОбъекта", внешняяОбработкаИмя);
	   внешняяОбработкаОбъект = ДополнительныеОтчетыИОбработки.ОбъектВнешнейОбработки(внешняяОбработкаСсылка);
	#КонецОбласти

      	ПараметрыЗаполнения = внешняяОбработкаОбъект.ПараметрыЗаполненияШаблона();
      	ПараметрыЗаполнения.ИсточникДанных	= Ссылка;
	ПараметрыЗаполнения.СтрокаШаблон	= ШаблонСсылки;
	ПараметрыЗаполнения.ОчиститьПустые = Истина;

	#Область Выполнение_ЗаполнитьПараметрыСтрокиДаннымиСсылки
	   текстПисьма = внешняяОбработкаОбъект.ПодставитьПараметрыШаблонизированнойСтроки(ПараметрыЗаполнения);
	#КонецОбласти

#КонецОбласти
```

***Дополнительные Параметры***
1. для ТЧ: {ВсегоСтрок}, {МаксСтрок=5}
2. Форматирование для значений всех параметров: `ДФ=dd.MM.yyyy`, `ЧДЦ=2`
   пишутся слитно сразу после параметра, пример: [Дата]`ДФ=dd.MM.yyyy` , {Количество}`ЧДЦ=2`

***Дополнительный набор значений источника***
1. В модуле обработки есть функция ___ДополнительныеЗначенияОбщие___ которая возвращает доп. значения
2. В модуле менеджера объекта можно определить функцию ___ДополнительныеЗначенияШаблона___ которая должна возвращать массив структур доп. значений

> Все доп. значения которые получаются от ссылки выносите в менеджер объекта так как это его ответственность предоставлять данные от себя, а общие (не зависящие от конкретной ссылки) выноси в модуль обработки.

```
#Область ДополнительныеЗначенияШаблона

// Функция - Дополнительные Значения Шаблона
//
// Параметры:
//	ИсточникСсылка - Ссылка - на источник данных для возможности получить знч (Можно не передавать)
//
// Возвращаемое значение:
//  Массив ИЗ Структура ИЗ
//	*Имя - Строка - Имя Параметра в шаблонизированной строке
//	*Знч - Значение которое будет подставлено в наблонизированную строку
//	*Формат - Строка - Результат конструктора форматной строки (пример: "ДФ=dd.MM.yyyy")
//
Функция ДополнительныеЗначенияШаблона(ИсточникСсылка) Экспорт
	
	_параметры = Новый Массив;
	
	_параметры.Добавить(
		Новый Структура("Имя,Знч,Формат"
			, "ДатаСоздания"
			, ИсточникСсылка.Дата
			, ""
		)
	);	
	
	Возврат _параметры;
	
КонецФункции

#КонецОбласти

```

##### <a name="step4"></a> Если хотите вызвать без использования БСП [(начало)](#pageup);

![image](https://github.com/KistanovSerhii/FillStringParameters1C/assets/28355711/e16b15d5-d61f-4a4d-b84f-1c615d53310e)
Можно добавить внешнюю обработку в ветку конфигурации к типу "Обработки" и использовать как:
внешняяОбработкаОбъект = Обработки.ЗаполнитьПараметрыСтрокиДаннымиСсылки.Создать();

***Тогда полный код будет таким:***

```
#Область ЗаполнитьПараметрыСтрокиДаннымиСсылки

	#Область ПолучениеВнешнейОбработки_ЗаполнитьПараметрыСтрокиДаннымиСсылки
	   внешняяОбработкаОбъект = Обработки.ЗаполнитьПараметрыСтрокиДаннымиСсылки.Создать();
	#КонецОбласти

      	ПараметрыЗаполнения = внешняяОбработкаОбъект.ПараметрыЗаполненияШаблона();
      	ПараметрыЗаполнения.ИсточникДанных	= Ссылка;
	ПараметрыЗаполнения.СтрокаШаблон	= ШаблонСсылки;
	ПараметрыЗаполнения.ОчиститьПустые = Истина;

	#Область Выполнение_ЗаполнитьПараметрыСтрокиДаннымиСсылки
	   текстПисьма = внешняяОбработкаОбъект.ПодставитьПараметрыШаблонизированнойСтроки(ПараметрыЗаполнения);
	#КонецОбласти

#КонецОбласти
```

# ВАЖНО:

##### <a name="add0"></a> ВАЖНО [(начало)](#pageup); 

 🔘 При использовании через расширение - обязательно снимите флаг "Безопасный режим, имя профиля безопасности"
![image](https://github.com/KistanovSerhii/ElemetsOfFormDynamicP/assets/28355711/7a0d51e4-fb60-4885-857a-61993c5aa62b)
 
