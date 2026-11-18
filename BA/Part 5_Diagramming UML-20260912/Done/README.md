# Частина 5. UML — діаграма прецедентів і діаграма класів

**Проєкт:** «VNS» — власна навчальна платформа університету (`"VNS" — Custom University Learning Platform`)
**Команда:** «20/80» · **Мова діаграм і сценаріїв:** англійська, як в артефактах Частин 1–4

> Частина 5 складається з двох лабораторних: **ЛР 1. Діаграма варіантів використання** і **ЛР 2. Діаграми класів** (`Samples/Лабораторна робота 1.docx`, `2.docx`).
> Цей README описує `Done/AI`. Папку `Done/Manual` зробила інша частина команди. Обидві версії моделюють ту саму функцію (AI-помічник на сторінці лекції) з тим самим ключовим прецедентом, але AI-версія спирається на Story Map AI-версії Частини 4 і додає вхід у систему.

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
| Authorization | Sign in with the university email (+ `«extend»` (password status) [first login] «Set my own password at first login») | Platform user | Story Map: Get started → Sign in; Part 2, Functional 1 |
| Learning with the AI assistant | *Use AI study tools* (абстрактний) → Get an AI summary of a lecture · Take a self-check test on a topic · Make flashcards from a lecture · **Ask the AI assistant a question about a lecture** | Student | Story Map: Study topics, Ask questions; Make flashcards (Release 2) |
| AI settings of a course | Choose the AI features of a course · Mark a lab as graded · See self-check results by topic | Lecturer | Part 2, Business Rule 1; Story Map: Check understanding (Release 2) |

**Ключовий прецедент** — «Ask the AI assistant a question about a lecture», як у Manual-версії; у Story Map це наліпка «Ask the AI assistant about the course materials».

| Елемент | Що на діаграмі | Джерело |
|---|---|---|
| `«include»` | Get an answer with links to the slides (зв'язаний з External LLM API) | LLM API: «Answer using only the course materials»; Part 2, Functional 6 |
| `«extend»` (graded task check) [graded lab] | Give hints instead of a ready solution | LLM API: «Refuse a full solution of a graded task»; Business Rule 1 |
| `«extend»` (search result) [nothing found] | Show «Not found in the course materials» | LLM API: «Answer using only the course materials» |
| `«extend»` (limit check) [daily limit reached] | Show «Daily AI limit reached» — розширює абстрактний *Use AI study tools*, тобто всі AI-інструменти | Part 2, Assumption 2 |
| `«extend»` (summary lookup) [no saved summary] | Generate a new summary | LLM API: «Generate a lecture summary»; Assumption 2 (кеш) |
| `«constraint»` | {the lecturer decides which AI features are on…}, {for a graded lab the AI never writes a complete solution} | Part 2, Business Rule 1 |

**Узагальнення використано двічі:**
- **між акторами:** Student і Lecturer → абстрактний *Platform user*, тому «Sign in» намальовано один раз;
- **між прецедентами:** чотири AI-інструменти → абстрактний *Use AI study tools*. Денний ліміт перевіряється для всіх них, тому `«extend»` стоїть на батьківському прецеденті.

**Чому External LLM API — другорядний актор.** Він нічого не ініціює, а лише генерує відповідь на запит платформи. У Story Map він так і позначений: «External LLM API · secondary actor · AI service». Лінії йдуть до тих прецедентів, де потрібна генерація: новий конспект, тест, флеш-картки, відповідь.

**Чим AI-версія відрізняється від Manual:** пакет Authorization (вхід і зміна пароля при першому вході), абстрактний актор Platform user, тест і флеш-картки як окремі прецеденти, «See self-check results by topic» для викладача. Адміністратора тут нема, бо в Story Map AI-версії він другорядний актор, а не користувач цієї функції.

## Діаграма класів

Класи поділені на три пакети за лекцією 7: граничні, керуючі й сутності. Діаграма описує AI-інструменти функції: питання до AI, конспект, тест із картками, налаштування курсу та вхід.

| Пакет | Класи | Роль |
|---|---|---|
| Boundary | `SignInPage`, `AiPanel`, `SummaryTab`, `StudyToolsTab`, `CourseAiSettingsPage`, `ILlmClient`, `LlmGateway` | екрани AI-інструментів і зв'язок із зовнішнім LLM API |
| Control | `AuthController`, `QuestionService`, `GradedTaskGuard`, `SlideSearch`, `SummaryService`, `StudyToolsService`, `CourseAiSettingsController` | логіка прецедентів UC-1…UC-10 |
| Entity | `Course`, `Lecture`, `Slide`, `SlideLink`, `Lab`, `AiSettings`, `DailyUsage`, `AiResult` (абстрактний) → `Answer`, `Summary`, `SelfCheckTest`, `FlashcardSet`; `TestAttempt`, `User` (абстрактний) → `Student`, `Lecturer` | дані предметної області |

| Прецедент | Класи |
|---|---|
| UC-1 питання до AI | `AiPanel` → `QuestionService` → `DailyUsage`, `GradedTaskGuard` (→ `Lab`), `SlideSearch` (→ `Slide`), `ILlmClient`; створює `Answer` із `SlideLink` |
| UC-4 конспект | `SummaryTab` → `SummaryService` → `Lecture.getSavedSummary()`, `ILlmClient`; створює `Summary` |
| UC-5, UC-6 тест і картки | `StudyToolsTab` → `StudyToolsService` → `ILlmClient`; `SelfCheckTest`, `FlashcardSet`, `TestAttempt` |
| UC-7 вхід | `SignInPage` → `AuthController` → `User` |
| UC-8, UC-9, UC-10 | `CourseAiSettingsPage` → `CourseAiSettingsController` → `AiSettings`, `Lab.markAsGraded()`, `TestAttempt` |

| Відношення | Приклад | Чому так |
|---|---|---|
| Композиція | `Course` ◆ `Lecture` ◆ `Slide`, `Course` ◆ `Lab`, `Course` ◆ `AiSettings`, `AiResult` ◆ `SlideLink` | частина не існує без цілого |
| Узагальнення | `Answer`, `Summary`, `SelfCheckTest`, `FlashcardSet` → *`AiResult`* `{complete, disjoint}`; `Student`, `Lecturer` → *`User`* `{incomplete, disjoint}` | спільні поля й помітка «AI-generated» в одному класі |
| Реалізація | `LlmGateway` ⇢ `ILlmClient` | постачальника LLM можна замінити |
| Залежність `«use»` | `QuestionService` ⇢ `GradedTaskGuard`, `SlideSearch`, `ILlmClient`; `SummaryService`, `StudyToolsService` ⇢ `ILlmClient` | сервіс лише викликає їхні операції |
| Асоціація | `Lecture` «Has saved» `Summary` (1 до 0..1), `SlideLink` «Points to» `Slide`, `Student` «Takes» `TestAttempt`, `Student` «Has» `DailyUsage` | кратності на обох кінцях |

- **Абстрактні операції:** *`show()`* в `AiResult`, *`getRole()`* в `User`.
- **Похідний атрибут:** `/remaining` у `DailyUsage` рахується з `limit` і `requests`.
- **Правила з вимог:** усі AI-функції вимкнені, доки викладач їх не ввімкне (`AiSettings`), для оцінюваної лаби AI дає лише підказки (`Lab.graded`, `Answer.isHintOnly`), кожен результат має помітку «AI-generated» (`AiResult.mark`).
- **Зв'язок із Частиною 6:** об'єкти й операції в діаграмах Частини 6 (AI) взято з цієї діаграми: `process`, `remaining`, `count`, `isGradedTask`, `isGraded`, `find`, `getText`, `generate`, `showAnswer`, `showMessage`.

**Правила лекції 7:** актори працюють лише з граничними класами, а операції сутностей викликають лише керуючі класи. Зовнішній LLM API доступний лише через граничний клас `LlmGateway`.

## Примітки

- **Тема.** ЛР посилаються на таблицю тем у Google Docs, але на 06/11/2026 у ній лише два рядки інших студенток, рядка нашої команди нема. Тому темою лишається проєкт VNS, як у Частинах 1–4. Варто підтвердити це у викладача.
- **Діаграма класів** 18/11/2026 переписана під AI-інструменти (раніше в ній була функція «здача й оцінювання лаби»), щоб узгодити її з діаграмою прецедентів і діаграмами Частини 6.
- **Як зроблено.** Файли `.drawio` створено скриптом за допомогою AI, PNG діаграми прецедентів отримано рендером рушієм draw.io; `.drawio` і PNG діаграми класів згенеровано одним скриптом (PNG не експортовано з draw.io, `.drawio` лише перевірено як XML).
- **Вхід без окремої реєстрації.** У Частині 2 AI-версії облікові записи завантажуються з реєстру університету (Assumption 1: «without student registration»), а вхід — за корпоративною поштою й паролем; початковий пароль (дата народження) треба змінити при першому вході (Functional 1). Тому замість реєстрації на діаграмі «Sign in with the university email» з розширенням «Set my own password at first login», як у Story Map.
- **Тексти інтерфейсу** («Ask», «Daily AI limit reached») для AI-панелі в Частині 4 AI-версії не узгоджено; за DoR їх треба погодити разом з екраном лекції, якого у wireframe нема.
- **Звіт на ВНС** (`Report by part 5`) поки не подано; у завданні на ВНС окремого опису немає.
