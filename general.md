# Общие заметки

### Класс Object
[Документация](https://docs.unity3d.com/ru/current/ScriptReference/Object.html)

Базовый класс для всех объектов в Unity.


### Класс Component
[Документация](https://docs.unity3d.com/ru/current/ScriptReference/Component.html)

Базовый класс для всего что содержит в себе любой игровой объект(GameObject)

Порядок наследования: `Component -> Object`

### Класс GameObject
[Документация](https://docs.unity3d.com/ru/current/ScriptReference/GameObject.html)

Базовый класс для всех объектов на сценах Unity.

Порядок наследования: `GameObject -> Object`

### Класс MonoBehavior
[Документация](https://docs.unity3d.com/ru/current/ScriptReference/MonoBehaviour.html)

Базовый класс, от которого наследуются все скрипты.

Порядок наследования: `MonoBehavior -> Behavior -> Component -> Object`

Класс MonoBehavior имеет ряд функций "сообщений", которые вызываются при определенных условиях. Пример функций: ***Start(), Update()***  

![img1](Images/img1.jpg "img1")

Флажок выключающий MonoBehavior в любом скрипте предотвращает выполнение функций:  
***Start(), Awake(), Update(), FixedUpdate(), OnGUI()***

### Наследование в C#
Синтаксис наследование:`public class ChildClass : ParentClass`

### Полиморфизм в С#
* Во время выполнения объекты производного класса могут обрабатываться как объекты базового класса
* Базовые классы могут определять и реализовывать виртуальные методы, а производные классы - переопределять их:
``` C#
// Реализация виртуального метода родительского класса
public class BaseHelloClass {
	public virtual void SayHello(){ // родительский метод SayHello
		Debug.log("Hello");
	}
}
//-----------------------------------------------
// Производный класс может переопределить член базового класса, только если 
// последний будет объявлен виртуальным или абстрактным.

public class ChildHelloClass : BaseHelloClass {
	public override void SayHello(){ // переопределенный метод SayHello
		Debug.log("Hello in child class");
	}
}
```
Внутри производного класса можно получить доступ к методам базового через `base` Пример:
``` C#
public class Base
{
    public virtual void DoWork() {/*...*/ }
}
public class Derived : Base
{
    public override void DoWork()
    {
        //Perform Derived's work here
        //...
        // Call DoWork on base class
        base.DoWork();
    }
}
```

### Интерфейсы в С#
Интерфейсы могут включить в класс поведение из нескольких источников. 
``` C#
interface ISampleInterface
{
    void SampleMethod();
}

public class ISampleClass : ISampleInterface
{
	void SampleMethod(){
		// ...
	}
}
```
Интерфейс содержит только сигнатуры методов, свойств, событий или индексаторов.

## Отладка в Unity

### Debug
Самый простой способ отладки, использовать класс Debug, который выводит сообщения в консоли редактора:
``` C#
Debug.Log("Some text");
Debug.Error("Errore text");
```

### Переопределение метода ToString()
``` C#
public override string ToString(){
	return string.Format ("***Class EnemyOgre*** OgreName:{0} | Health: {1} | 
		Speed : {2} | CurrentAttack : {3} | RecoveryTime: {4}", OgreName, Health, 
		Speed, CurrentAttack, RecoveryTime);
}
```

### Визуальная отладка
Визуальная отладка осуществляется с помощью класса Gizmo и метода MonoBehavior.OnDrawGizmos()
``` C#
public class GizmoCube : MonoBehavior{
	public bool DrawGizmos = true;

	void OnDrawGizmos(){
		if(!DrawGizmos) return;

		Gizmos.color = Color.blue;

		Gizmos.DrawRay(transform.position, transform.forward.normalized * 4.0f);

		Gizmos.color = Color.red;
		Gizmos.DrawWireSphere(transform.position, 4.0f);

		Gizmos.color = Color.white;
	}
}
```

### Регистрация ошибок в текстовый файл
``` C#
public class ExceptionLogger : MonoBehavior{
	// Внутрення ссылка на объект потока записи
	private System.IO.StreamWriter SW;

	// Имя файла регистрации
	public string LogFileName = "log.txt";

	void Start(){
		// Сделать постоянно хранимым в памяти
		DontDestroyOnLoad(gameObject);

		// Создать объект записи в строку
		SW = new System.IO.StreamWriter(Application.persistentDataPath + "/" + LogFileName);

		Debug.Log(Application.persistentDataPath + "/" + LogFileName);
	}

	// Зарегистрировать обработчик исключений
	void OnEnable(){
		Application.RegisterLogCallback(HandleLog);
	}

	// Отключить обработчик исключений
	void OnDisable(){
		Application.RegisterLogCallBack(null);
	}

	// Записать информацию об исключении в файл
	void HandleLog(String logString, string stackTrace, LogType type){
		// Если исключение ошибка, записать в файл
		if(type == LogType.Exception || type == LogType.Error){
			SW.WriteLine.Exception("Logged at: " + System.DateTime.Now.ToString() 
			+ " - Log Desc: " + logString + " - Trace : " + stackTrace + 
			" - Type: " + type.ToString());
		}
	}
	// Вызывается при унижчтожении объекта
	void OnDestroy() {
		// Закрыть файл
		SW.Close();
	}
}
```

### Profiler (профайлер)
[Documentation](https://docs.unity3d.com/ru/520/Manual/Profiler.html)

Профайлер Unity помогает вам оптимизировать вашу игру. Он сообщает вам о том, как много времени тратится в различных областях вашей игры.

## Функции поиска

### Функция GetComponent
[Documentation](https://docs.unity3d.com/ru/current/ScriptReference/GameObject.GetComponent.html)

Функция получающая компонент объекта по его типу.
``` C#
Transform myTrans = GameObject.GetComponent<Transform> ();
```

### Функция GetComponents
[Documentation](https://docs.unity3d.com/ru/current/ScriptReference/GameObject.GetComponents.html)

Функция получает несколько компонентов по типу
``` C#
// Получить все компоненты
private Component[] AllComp = null;

void Start(){
	// Получить список всех компонентов данного объекта
	AllComp = GetComponents<Component> ();
}
```

### Функции Find и FindWithTag
[Documentation Find](https://docs.unity3d.com/ru/current/ScriptReference/GameObject.Find.html)
[Documentation FindWithTag](https://docs.unity3d.com/ru/current/ScriptReference/GameObject.FindWithTag.html)

Поиск объектов в сцене. Первый ищет по имени объекта, второй по тегу. ***Второй способ быстрее!***

### Функция сравнения тегов CompareTag
[Documentation GameObject](https://docs.unity3d.com/ru/current/ScriptReference/GameObject.html)
``` C#
gameObject.CompareTag(Obj2.tag);
```

### Функция сравнения двух объектов GetInstanceID
[Documentation GameObject](https://docs.unity3d.com/ru/current/ScriptReference/GameObject.html)
``` C#
if(obj1.GetInstanceID() == obj2.GetInstanceID()){
	...
}
```

### Расстояние между двумя объектами
[Documentation Vector3.Distance](https://docs.unity3d.com/ru/current/ScriptReference/Vector3.Distance.html)
``` C#
float distance = Vector3.Distance(obj1.transform.position, obj2.transofrm.position)
```

### Поиск объектов определенного типа
***Не использовать в Update()***
``` C#
Collider[] Cols = Object.FindObjectsOfType<Collider>();
```

### Проверка препятствий между объектами
[Documentation Physics.LineCast](https://docs.unity3d.com/ru/current/ScriptReference/Physics.Linecast.html)
``` C#
public LayerMask LM;

void Update(){
	if(!Physics.LineCast(obj1.transform.position, obj2.transform.position, LM)){
		Debug.log("Препятствий нет");
	}
}

// Показывать вспомогательную линию в режиме отладки
void OnDrawGizmos(){
	Gizmos.DrawLine(obj1.transform.position, obj2.transform.position);
}
```

### Присоединение одного объекта к другому с помощью Transform
``` C#
childObj.transform.parent = parentObj.transform;
```

## Работа с кадрами в Unity
Для работы с кадрами имеются три основных вида событий в любом классе MonoBehavior.
- Update()
- FixecUpdate()
- LateUpdate()

[Documentation Physics.LineCast](https://docs.unity3d.com/ru/current/ScriptReference/MonoBehavior.html)

### Update
Вызывается один раз для каждого кадра в каждом активном компоненте каждого активного объекта. Update соответствует понятию кадра в Unity. Используется для событий ввода: клавиатура, мышь. Очередность обработки Update не определена. Нельзя утверждать, что Update Х будет вызван раньше чем Update Y и наоборот.

### FixedUpdate
Не привязано к кадрам. Может вызываться несколько раз в кадре. Вызывается регулярно и нормированно, через фиксированные интервалы времени. Используется для эмуляции физических характеристик объектов(св-ва компонента RigidBody)

### LateUpdate
Вызывается в каждом кадре как и Update, но только после событий Update и FixedUpdate. Используется для изменения положения камеры, т.к. положение объекта уже было изменено в Update

### Start
Вызывается один раз в первом кадре, где игровой объект становится активным.

### Awake
Вызывается один раз, всегда при создании объекта, всегда перед функцией Start

### Два важных правила в работе с кадрами
1. Важно рационально относиться к Update и любым другим регулярным вызываемым событиям, связанным с кадрами. Они должны содержать только самый необходимый код. ***Серьезно уменьшить нагрузку на функции Update поможет событийное программирование.***
2. Движение должно основываться на времени. То есть нужно писать код для реализации движений и изменений так, чтобы вне зависимости от частоты кадров они выглядели одинаково у всех игроков.***Для этого используется переменная Time.deltaTime***  
`transform.localPosition += transform.forward * Speed * Time.deltaTime;`

### Неуничтожаемые объекты
По умолчанию объекты не могут существовать вне сцены, которой они принадлежат, они уничтожаются при смене активной сцены.

Чтобы перенести объект из сцены в сцену, необходимо его сохранять. Одним из способов сохранения является функция DontDestroyOnLoad: `DontDestroyOnLoad(gameObject);`  
При этом также будут сохраняться все дочерние объекты и любые ресурсы которые использует сохраняемый объект. Также может возникнуть проблема дублирования объектов, т.к. данная функция переносит копию объекта.

### Синглтоны и статические переменные
Синглтоны это классы у которых может существовать только один экземпляр. Такие классы используются в Unity для переноса данных из сцены в сцену. Пример реализации синглтона GameManager:
``` C#
public class GameManager : MonoBehavior{
	// Свойство С# дя доступа к экземпляру синглтона
	// Доступно только для чтения - метод set отсутствует
	public static GameManager Instance{
		// вернуть ссылку на экземпляр
		get {
			return instance;
		}
	}

	private static GameManager instance = null;

	// Наибольший результат в игре
	public int HighScore = 0;

	// Признак приостановки игры
	public bool IsPaused = false;

	// Признак поддержки ввода пользователя
	public bool InputAllowed = true;

	// Метод инициализации
	void Awake (){
		// Проверить наличие экземпляра класса
		// Если присутствует - уничтожить текущий экземпляр
		if(instance){
			DestroyImmediate(gameObject);
			return;
		}
		// Сделать актиным и единственным данный экземпляр
		instance = this;

		// Сделать диспетчер игры неуничтожаемым
		DontDestroyOnLoad(gameObject);
	}
}
```

## Событийное программирование в Unity
Используется для значительного увеличения производительности, заместо постоянного использования функции Update

### Два варианта реализации логики врага
1. Через функцию Update :
``` C#
void Update(){
	// Проверить здоровье врага
	if(Health <= 0){
		// Сымитировать смерть
		Die();
		return;
	}
	// Уровень здоровья слишком низкий ?
	if(health <= 20){
		// Уровень низкий, отправиться на поиски аптечки
		RunAndFindHealthRestore();
		return;
	}
	// Проверить патроны
	// Патроны кончились ?
	if(Ammo <= 0){
		// Отправиться на поиски патронов
		SearchMore();
		return;
	}
	// Здоровье в порядке, патроны есть.
	// Игрок в поле зрения ? Если да - выстрелить
	if(HaveLineOfSight){
		FireAtPlayer();
	}
}
```
2. Событийная реализация логики врага : 
``` C#
// Внутренние закрытые переменные: уровень здоровья и количество патронов
private int _health = 100;
private int _ammo = 50;
//--------------------
// Свойства C# для закрытых переменных
public int Health{
	get{
		return _health;
	}
	set{
		// Привести уровень здоровья в диапозоне 0-100
		_health = Mathf.Clamp(value, 0, 100);

		// Убит ?
		if(_health <= 0){
			OnDead();
			return;
		}

		// Проверить уровень здоровья
		// и сгенерировать событие при необходимости
		if(_health <= 20){
			OnHealthLow();
			return;
		}
	}
}
//--------------------
public int Ammo{
	get{
		return _ammo;
	}
	set{
		// Привести боезапас в диапазон 0-50
		_ammo = Mathf.Clamp(value, 0, 50);

		// Проверить отсутствие патронов
		if(_ammo <= 0){
			// Вызвать событие опустошения боезапаса
			OnAmmoExpired();
			return;
		}
	}
}
//--------------------
void Update(){

}

// Событие при низком уровне здоровья
void OnHealthLow(){
	// Реализация отклика на событие
}

// Событие когда враг умирает
void OnDead(){
	// Реализация отклика на событие
}

// Событие когда заканчиваются патроны
void OnAmmoExpired(){
	// Реализация отклика на событие
}
```

### Система управления событиями EventManager
Для подобной системы нам нужен произвольный класс управления EventManager, который позволит объектам получать уведомления о конкретных событиях. Эта система основана на трех основных идеях:

1. *EventListener*: "получатель событий" применимо к любому объекту, который должен получать уведомления о событии, даже если это событие связано с ним самим. Всякий раз, когда объект ожидает, что ему сообщат о событии, он становится получателем.
2. *EventPoster*: объект, который обнаруживает, что произошло событие, после чего он должен уведомить всех получателей, или разместить объявление о событии. Требуется, чтобы объект инициировал события на глобальном уровне.
3. *EventManager*: синглтон, который сохраняется при переходе между уровнями и доступен глобально. Связывает получателей с отправителями. Принимает уведомления о событиях, и сразу же передает их нужным получателям в форме события.

### EventListener(получатель) 
Должен быть зарегистрирован в *EventManager* в качестве получателя одного или более конкретных событий. Необходимо создать интерфейс, из которого мы будем обращаться ко всем объектам получателей.  
Интерфейс в C# является подходящим кандидатом для создания объектов получателей.  
С помощью интерфейса *Listener*, от которого будут произведены все объекты, каждый объект получает возможность стать получателем события.  
Простой интерфейс *Listener* :
``` C#
// Перечисление определяет все возможные события
// Дополнительные события должны добавляться сюда
public enum EVENT_TYPE {
	GAME_INIT,
	GAME_END,
	AMMO_EMPTY,
	HEALTH_CHANGE,
	DEAD
};
//------------------------------------------------
// Интерфейс Listener для реализации в классах получателей событий
public interface IListener{
	// Вызывается, когда возникает событие
	void OnEvent(
		EVENT_TYPE Event_Type, 
		Component Sender, 
		Object Param = null);
}

```

С помощью интерфейса IListener мы можем сделать получателем любой объект, используя только наследование класса, то есть любой объект может объявить себя получателем и получать события:
``` C#
public class MyCustomListener : MonoBehavior, IListener {
	void Start() {}
	void Update() {}

	// Реализовать функцию OnEvent для приема событий
	public void OnEvent(
		EVENT_TYPE Event_type, 
		Component Sender, 
		Object Param = null) {

		//....
	}
}
```

### EventManager(класс диспетчер синглтон)
Обязанностью диспетчера является вызов событий у получателей, когда события действительно происходят. Этот класс, будучи неуничтожаемым синглтоном, будет подключаться к пустому игровому объекту в сцене и непосредственно доступен для всех других объектов через статическое свойство.
``` C#
// Синглтон EventManager для отправки событий получателям
// Работает с реализациями IListener
public class EventManager : MonoBehavior {
	#region свойства C#
	//-----------------
	// Общий доступ к экземпляру
	public static EventManager Instance {
		get {return instance;}
		set{}
	}
	#endregion

	#region переменные
	// Экземпляр диспетчера событий (синглтон)
	private static EventManager instance = null;

	// Массив получателей (все зарегистрировавшиеся объекты)
	private Dictionary<EVENT_TYPE, List<IListener>> Listeners = new Dictionary<EVENT_TYPE, List<IListener>>();
	#endregion
	//-------------
	#region методы
	// Вызывается перед началом работы для инициализации
	void Awake() {
		// Если экземпляр отсутствует, сохранить данный экземпляр
		if (instance == null) {
			instance = this;
			DontDestroyOnLoad(gameObject);
		} else {
			DestroyImmediate(this);
		}
	}
	//-----------------
	/// <summary>
	/// Функция добавления получателя в массив
	/// </summary>
	/// <param name="Event_Type">Событие, ожидаемое получателем</param>
	/// <param name="Listener">Объект, ожидающий события</param>
	public void AddListener(EVENT_TYPE Event_Type, IListener Listener) {
		// Список получателей для данного события
		List<IListener> ListenList = null;

		// Проверить тип события. Если существует - добавить в список
		if(Listeners.TryGetValue(Event_Type, out ListenList)) {
			// Список существует, добавить новый элемент
			ListenList.Add(Listener);
			return;
		}

		// Иначе создать список как ключ словаря
		ListenList = new List<IListener>();
		ListenList.Add(Listener);
		Listeners.Add(Event_Type, ListenList);
	}
	//---------------------
	/// <summary>
	/// Посылает события получателям
	/// </summary>
	/// <param name="Event_Type">Событие для вызова</param>
	/// <param name="Sender">Вызываемый объект</param>
	/// <param name="Param">Необязательный аргумент</param>
	publuc void PostNotification(EVENT_TYPE, Event_Type, Component Sender, Object Param = null) {
		// Послать событие всем получателям
		// Список получателей только для данного события
		List<IListener> ListenList = null;

		// Если получателей нет - выйти
		if(!Listeners.TryGetValue(Event_Type, out ListenList))
			return;

		// Получатели есть. Послать им событие
		for(int i=0; i<ListenList.Count; i++) {
			if(!ListenList[i].Equals(null))
				ListenList[i].OnEvent(Event_Type, Sender, Param);
		}
	}
	//------------------------
	// Удаляет событие из словаря, включая всех получателей
	public void RemoveEvent(EVENT_TYPE Event_Type) {
		// Удалить запись из словаря
		Listeners.Remove(Event_Type);
	}
	//---------------------------------
	// Удаляет все избыточные записи из словаря
	public void RemoveRedundancies() {
		// Создать новый словарь
		Dictionary<EVENT_TYPE, List<IListener>> TmpListeners = new Dictionary<EVENT_TYPE, List<IListener>>();

		// Обойти все записи в словаре
		foreach(LeyValuePair<EVENT_TYPE, List<IListener>> Item in Listeners) {
			// Обойти всех получателей, удалить пустые ссылки
			for(int i = Item.Value.Count-1; i>=0; i--){
				// Если ссылка пустая, удалить элемент
				if(Item.Value[i].Equals(null))
					Item.Value.RemoveAt(i);
			}

			// Если в списке остались элементы, добавить его в словать tmp
			if(Item.Value.Count > 0)
				TmpListeners.Add (Item.Key, Item.Value);
		}

		// Заменить объект Listeners новым словарем
		Listeners = TmpListeners;
	}
	//------------------
	// Вызывается при смене сцены. Очищает словарь
	void OnLeveWasLoaded() {
		RemoveRedundancies ();
	}
	//--------------------
	#endregion
}
```

### Использование EventManager
Для получения уведомлений о событии (любом событии) получатель должен зарегистрироваться в экземпляре синглтона EventManager. Обычно это делается при первой же возможности, например в функции Start. Не используйте для этого функцию Awake, потому что она зарезервирована для внутренней инициализации объектов, а не для операций, которые выходят за пределы текущего объекта, для смены состояний и настройки других объектов.
Регистрация получателя событий:  
``` C#
void Start() {
	// Добавить себя как получателя события изменения уровня здоровья
	EventManager.Instance.AddListener(EVENT_TYPE.HEALTH_CHANGE, this);
}
```
После регистрации получателей одного или более событий объекты могут затем уведомить синглтон EventManager об обнаруженном событии:
``` C#
public int Health {
	get {return _health;}
	set {
		// Привести уровень здоровья в диапозон 0-100
		_health = Mathf.Clamp(value, 0, 100);

		// Послать уведомление об изменении уровня здоровья
		EventManager.Instance.PostNotification(EVENT_TYPE.HEALTH_CHANGE, this, _health);
	}
}
```
Наконец, после отправки уведомления о событии, все подписавшиеся на него получатели будут автоматически уведомлены синглтоном EventManager. В частности, объект EventManager вызовет функцию OnEvent каждого получателя, давая возможность обработать событие и среагировать в случае необходимости:
``` C#
// Вызывается, когда происходит событие
public void OnEvent (
	EVENT_TYPE Event_Type, 
	Component Sender, 
	object Param = null) {
		// Определить тип события
		switch(Event_Type) {
			case EVENT_TYPE.HEALTH_CHANGE:
				OnHealthChange(Sender, (int)Param);
				break;
		}
	}
```

### Директивы #region и #endregion для свертывания кода
Они добавляют в исходный код организацию и структурность, не затрагивая его сущности и не влияя на его выполнение.
``` C#
#region Методы
void someFunc() {
	//...
}
#endregion
```

### Альтернативный способ, основанный на делегировании
Можно прочитать в книге Алон Торн - Искусство создания сценариев в Unity стр. 155

### События класса MonoBehavior
Класс содержит широкий спектр событий, которые вызываются автоматически при определенных условиях. Эти функции, или события, начинаются с префикса On и включают например такие события, как:
* OnGUI
* OnMouseEnter
* OnMouseDown
* OnParticleCollision

**OnMouseEnter**, **OnMouseDown** и **OnMouseExit** регистрируют события не только для мыши, но и для сеносорной панели. Вызов этих событий определяется коллайдером объекта, события вызываются при нахождении курсора мыши в пределах его объекта. Ни одно из событий мыши не срабатывает для объектов, не имеющих коллайдера.

**OnApplicationQuit** посылается всем объектам сцены перед завершением игры, но до уничтожения сцены и ее содержимого. Если игра тестируется в редакторе, событие вызывается при остановке воспроизведения.
* **Важно!** **OnApplicationQuit** не вызывается на устройствах, работающих под управлением iOS, т.к. приложения часто не закрываются, а приостанавливаются, позволяя позже возобновить работу с того же места.

**OnApplicationFocus** передается всем объектам в сцене, когда окно игры теряет фокус, например когда оно деактивируется при переключении на другую программу. Это событие может быть значительным событием для игр, особенно для многопользовательских, где действия и события в общем мире продолжаются, даже если один или несколько игроков не принимают в игре участия.

**OnApplicationPause** является неоднозначным событием, потому что понятие паузеы в Unity четко не определено. При вызове данного метода все действия и события в игре полностью приостанавливаются. В этом состоянии нет течения времени, и ничто не может двигаться. Это событие будет вызываться, только если сброшен флаг Run In Background во вкладке Player Settings в группе Resolution. 
* В iOS событие OnApplicationPause будет вызываться всякий раз, когда приложение сворачивается или переносится на задний план.

Более верно не полагаться на событие OnApplicationPause при реализации собственной паузы. Лучше использовать переменную Time.timeScale или создать более полноценную систему, которая сможет сама выбирать, какие элементы приостанавливать.

### Определение видимости объекта
Самым простым способом определения видимости или невидимости объекта являются два события:
1. OnBecameVisible
2. OnBecameInvisible
Оба автоматически вызываются для любого объекта с компонентом отображения, таким как MeshRenderer или SkinnedMeshRenderer.  
Пример:
``` C#
public class ViewTester : MonoBehaviour {
	void OnBecameVisible() {
		Debug.log ("Became Visible");
	}

	void OnBecameInvisible() {
		Debug.Log("Became Invisible");
	}
}
```
* Видимость здесь означает, что объект попал в поле зрения камеры, но он может быть перекрыт другими объектами, находящимися ближе к камере.
* Событие относится ко всем камерам, а не к конкретной. Тобишь срабатывает на любой камере.
* Обе функции отслеживают также видимость камеры сцены.

#### Видимость для конкретной камеры
Для определения видимости для конкретной камеры используется событие OnWillRenderObject. Это событие вызывается регулярно, один раз в каждом кадре для каждой камеры, для которой виден объект. Проверка заслонения другими объектами не применяется. С помощью свойства Camera.current можно получить ссылку на камеру, для которой объект в настояее время виден.
``` C#
void OnWillRendererObject() {
	Debug.Log (Camera.current.name);
}
```


### Визуализации изображений в 2D играх
В двухмерных играх графика отображается напрямую. Поэтому желательно сохранить ее стандартные размеры, пиксель в пиксель. Такой вид визуализации называется безупречным, потому каждый пиксель в текстуре выводится на экран без изменений. Но реализация его требует особого подхода. 
* Для отображения 1 игровой еденицы в 1 пиксель значение в поле Size во вкладке Camera должно быть равно половине вертикального разрешения дисплея.  
То есть, если игра будет запущена в окне с разрешением 1024х768, поле Size должно содержать значение 364, потому что 768 / 2 = 364.

Если пользователь имеет возможность изменять размер окна игры или разрешение, необходимо обновить размер камеры программно:
``` C#
[RequireComponent(typeof(Camera))]
//--------------------------------
public class OrthoCam : MonoBehaviour {
	// закрытая ссылка на компонент камеры
	private Camera Cam = null;

	// Число пикселей в единице размера Units
	public float PixelsToWorldUnits = 200f;
	//-----------------
	// Этот метод выполняет инициализацию
	void Awake() {
		// Получить ссылку на камеру
		Cam = GetComponent<Camera>();
	}
	//-----------------
	// Вызывается при отображении каждого кадра
	void LateUpdate() {
		// Изменить ортографический размер
		Cam.orthographicSize = Screen.height / 2f / PixelToWorldUnits;
	}
}
```
* Обратите внимание на переменную PixelToWorldUnits, определяющую масштаб ортографического размера в соответствии с полем Pixel To Units импортированной текстуры спрайтов. Это гарантирует, что спрайты отобразятся с правильными размерами при выводе на экран. Это вызвано необходимостью масштабировать все спрайты в соответствии с этим значением для отображения пикселей текстуры в мировые еденицы измерения.

### Вывод изображения с камеры и постобработка
Камеры и обхекты Unity предоставляют широкие возможности управдения отображением сцены. Все связанные с этим вопросы называются постобработкой. Они включают в себя эффекты **размытия**, **регулировки цвета**, эффект **рыбьего глаза** и т.д.

### Реализация плавно сопровождающей камеры
``` C#
public class CamFlow : MonoBehaviour {
	// Цель для преследования
	public Transform Target = null;

	// Ссылка на локальную трансформацию
	private Transform ThisTransform = null;

	// Линейное расстояние до цели (в мировых еденицах измерения)
	public float DistanceFromTarget = 10.0f;

	// Высота камеры над целью
	public float RoationDamp = 4f;

	// Демпфирование позиции
	public float PosDamp = 4f;

	void Awake() {
		// Получить трансформациб для камеры
		ThisTransform = GetComponent<Transform>();
	}
	//-------------
	// Вызывается при отображении каждого кадра
	void LateUpdate() {
		// Получить скорость
		Vector3 Velocity = Vector3.zero;

		// Вычислить угол поворота интерполяцией
		ThisTransform.rotation = Quaternion.Slerp(ThisTransform.rotation,
			Target.rotation, RotationDamp * Time.deltaTime);

		// Вычислить новую позицию
		Vector3.SmoothDamp(ThisTransform.position,
			Target.position, ref Velocity,
			PosDamp * Time.deltaTime);

		// Отдалить от цели
		ThisTranform.position = Dest - ThisTransform.forward * DistanceFromTarget;

		// Вычислить высоту
		ThisTransform.position = new Vector3 (ThisTransform.position.x,
			CamHeight, ThisTransform.position.z);

		// Направление на цель
		ThisTransform.LookAt(Dest);
	}
}
```


``` C#

```