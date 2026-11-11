# Частина 5. UML — діаграма прецедентів і діаграма класів

**Проєкт:** «VNS» — власна навчальна платформа університету (`"VNS" — Custom University Learning Platform`)
**Команда:** «20/80» · **Мова діаграм і сценаріїв:** англійська, як в артефактах Частин 1–4

> Частина 5 складається з двох лабораторних: **ЛР 1. Діаграма варіантів використання** і **ЛР 2. Діаграми класів** (`Samples/Лабораторна робота 1.docx`, `2.docx`).
> Цей README описує `Done/AI`. Папку `Done/Manual` зробила інша частина команди. Обидві версії моделюють ту саму функцію (AI-помічник на сторінці лекції) з тим самим ключовим прецедентом, але AI-версія спирається на Story Map AI-версії Частини 4 і додає реєстрацію та вхід.

## Що вимагає завдання

| Вимога | Де виконано |
|---|---|
| ЛР 1: розглянути **одну функцію** сервісу, UML-модель у draw.io | `1. Use Case Diagram_VNS.drawio`; функція названа в рамці системи |
| ЛР 1: виділити акторів | 2 головних (Student, Lecturer, обидва — нащадки абстрактного Platform user) і 1 другорядний (External LLM API) |
| ЛР 1: виділити ключовий прецедент, його компоненти й виключення | жовтий еліпс із товстою рамкою; компонент через `«include»`, виключення через `«extend»` |
| ЛР 1, рекомендації: успішний сценарій і сценарії всіх виключень | `2. Use Case Scenarios_VNS.docx` |
| ЛР 1: `«extend»` має умову й посилання на точку розширення | підпис `«extend» (точка) [умова]`; точки розширення вписано в базові еліпси |
| ЛР 2: на прецедентному рівні побудувати класи для цієї ж функції | `3. Class Diagram_VNS.drawio` |
| ЛР 2: описати властивості й методи | усі класи мають три секції; порожня секція теж відділена лінією |
| ЛР 2: побудувати відношення між класами | асоціація, узагальнення, агрегація, композиція, залежність, реалізація |

## Артефакти

| № | Файл (`AI/Artifacts/`) | Що це |
|---|---|---|
| 1 | `1. Use Case Diagram_VNS.drawio` (+ `.png`) | діаграма прецедентів; `.drawio` відкривається й редагується в draw.io |
| 2 | `2. Use Case Scenarios_VNS.docx` | актори й цілі, таблиця прецедентів із джерелами, сценарії та виключення; оформлено за вимогами курсу: A4, Times New Roman 14, інтервал 1,5 |
| 3 | `3. Class Diagram_VNS.drawio` (+ `.png`) | діаграма класів |

Шаблонів до Частини 5 у курсі немає, тому папки `FilledTemplates` тут нема.

## Функція: AI-помічник на сторінці лекції

Завдання просить модель однієї функції, а не всієї платформи. Функція: студент реєструється й входить, а тоді користується AI-інструментами на сторінці лекції (конспект, тест для самоперевірки, флеш-картки, питання до AI). Викладач вирішує, які AI-функції ввімкнені, позначає оцінювані лаби й бачить результати тестів.
Ця ж функція піде далі в ЛР 3–8 (Частина 6), бо ті роботи будуються на діаграмах ЛР 1 і ЛР 2.

## Діаграма прецедентів

**Головний принцип:** прецеденти — це дії користувачів зі Story Map Частини 4 (AI-версія), а не внутрішні перевірки системи. Перевірки (чи є доступ, чи знайдено матеріали) описано як кроки сценаріїв і як операції класів.

| Пакет | Прецеденти | Актор | Джерело |
|---|---|---|---|
| Authorization | Sign in with the university email · Register with the student card number (+ `«extend»` «This student card is already registered») | Platform user / Student | Story Map: Get started → Sign in; реєстрація — Part 2 (Manual), Functional 1 і `Ідея_проекту.md`, 5.2 |
| Learning with the AI assistant | *Use AI study tools* (абстрактний) → Get an AI summary of a lecture · Take a self-check test on a topic · Make flashcards from a lecture · **Ask the AI assistant a question about a lecture** | Student | Story Map: Study topics, Ask questions; Make flashcards (Release 2) |
| AI settings of a course | Choose the AI features of a course · Mark a lab as graded · See self-check results by topic | Lecturer | Part 2, Business Rule 1; Story Map: Check understanding (Release 2) |

**Ключовий прецедент** — «Ask the AI assistant a question about a lecture», як у Manual-версії; у Story Map це наліпка «Ask the AI assistant about the course materials».

| Елемент | Що на діаграмі | Джерело |
|---|---|---|
| `«include»` | Get an answer with links to the slides (зв'язаний з External LLM API) | LLM API: «Answer using only the course materials»; Part 2, Functional 6 |
| `«extend»` (graded task check) [graded lab] | Give hints instead of a ready solution | LLM API: «Refuse a full solution of a graded task»; Business Rule 1 |
| `«extend»` (search result) [nothing found] | Show «Not found in the course materials» → далі `«extend»` Ask the group in the «Course Q&A» | «Answer using only the course materials»; wireframe, екран 2 |
| `«extend»` (limit check) [daily limit reached] | Show «Daily AI limit reached» — розширює абстрактний *Use AI study tools*, тобто всі AI-інструменти | Part 2, Assumption 2 |
| `«extend»` (summary lookup) [no saved summary] | Generate a new summary | LLM API: «Generate a lecture summary»; Assumption 2 (кеш) |
| `«constraint»` | {the lecturer decides which AI features are on…}, {for a graded lab the AI never writes a complete solution} | Part 2, Business Rule 1 |

**Узагальнення використано двічі:**
- **між акторами:** Student і Lecturer → абстрактний *Platform user*, тому «Sign in» намальовано один раз;
- **між прецедентами:** чотири AI-інструменти → абстрактний *Use AI study tools*. Денний ліміт перевіряється для всіх них, тому `«extend»` стоїть на батьківському прецеденті.

**Чому External LLM API — другорядний актор.** Він нічого не ініціює, а лише генерує відповідь на запит платформи. У Story Map він так і позначений: «External LLM API · secondary actor · AI service». Лінії йдуть до тих прецедентів, де потрібна генерація: новий конспект, тест, флеш-картки, відповідь.

**Чим AI-версія відрізняється від Manual:** пакет Authorization (реєстрація й вхід), абстрактний актор Platform user, тест і флеш-картки як окремі прецеденти, «See self-check results by topic» для викладача. Адміністратора тут нема, бо в Story Map AI-версії він другорядний актор, а не користувач цієї функції.

## Діаграма класів

Класи поділені на три пакети за лекцією 7: граничні, керуючі й сутності.

| Пакет | Класи | Роль |
|---|---|---|
| Boundary | `LabPage`, `GradingPage`, `Dashboard`, `IFileStorage`, `FileStorageClient`, `RegistryClient` | екрани, робота зі сховищем файлів і зв'язок із реєстром університету |
| Control | `SubmissionController`, `GradingController`, `ProgressService`, `AccessService` | логіка прецедентів |
| Entity | `Course`, `Lab`, `Submission`, `Grade`, `CourseProgress`, `User`, `Student`, `Lecturer`, `Group` | дані предметної області |

| Відношення | Приклад | Чому так |
|---|---|---|
| Композиція | `Course` ◆ `Lab` ◆ `Submission` ◆ `Grade`, `Student` ◆ `CourseProgress` | частина не існує без цілого |
| Агрегація | `Group` ◇ `Student` | студент існує й без групи |
| Узагальнення | `Student`, `Lecturer` → *`User`* `{incomplete, disjoint}` | спільні поля в абстрактному класі; `incomplete`, бо інші ролі платформи (адміністратор, деканат) у цій функції не беруть участі, `disjoint`, бо один акаунт має одну роль |
| Реалізація | `FileStorageClient` ⇢ `IFileStorage` | сховище можна замінити, логіка здачі від цього не зміниться |
| Залежність `«use»` | `SubmissionController` ⇢ `IFileStorage`, `AccessService`; `GradingController` ⇢ `RegistryClient` | контролер лише викликає їхні операції |
| Асоціація | `Submission` «Is sent by» `Student`, `Lecturer` «Teaches» `Course` | кратності на обох кінцях |

- **Абстрактна операція:** *`getRole()`* у `User`, її реалізують нащадки.
- **Похідний атрибут:** `/stillAvailable` у `CourseProgress` рахується з `earned` і `maximum`.
- **Числа з вимог:** `maxPoints = 10` у `Lab`, `100` у `Course`, `maxFileSizeMb = 20`.
- **Бізнес-правило 2** (офіційну оцінку ставить лише викладач) показане асоціацією `Grade` «Is set by» `Lecturer` і тим, що зберігає оцінку лише `GradingController`.

**Чек-лист лекції («Чек-лист правильної UML-моделі»)** перевірено скриптом: імена класів унікальні, у кожного три секції, усі атрибути й операції мають квантор видимості, кожна асоціація має кратність на обох кінцях, композицію й агрегацію розрізнено.

**Правила лекції 7:** актори працюють лише з граничними класами, а операції сутностей викликають лише керуючі класи. Тому для другорядного актора University registry є граничний клас `RegistryClient`: через нього `GradingController` отримує студентів групи для списку здач. Лінії між сутностями — це структура (що з чого складається), а не виклики, тому правилу вони не суперечать.

## Примітки

- **Тема.** ЛР посилаються на таблицю тем у Google Docs, але на 06/11/2026 у ній лише два рядки інших студенток, рядка нашої команди нема. Тому темою лишається проєкт VNS, як у Частинах 1–4. Варто підтвердити це у викладача.
- **Діаграма класів** лишилася з попередньої версії (функція «здача й оцінювання лаби»); на рішення команди переробляли лише діаграму прецедентів.
- **Як зроблено.** Файли `.drawio` створено скриптом за допомогою AI, PNG отримано рендером цих файлів рушієм draw.io.
- **Реєстрація.** У Story Map і Частині 2 AI-версії облікові записи завантажуються з реєстру, окремої реєстрації там нема. Прецедент «Register with the student card number» додано на рішення команди; його джерело — Functional 1 Manual-версії Частини 2 і розділ 5.2 `Ідея_проекту.md`.
- **Тексти інтерфейсу** («Ask», «Daily AI limit reached») для AI-панелі в Частині 4 AI-версії не узгоджено; за DoR їх треба погодити разом з екраном лекції, якого у wireframe нема.
- **Звіт на ВНС** (`Report by part 5`) поки не подано; у завданні на ВНС окремого опису немає.
