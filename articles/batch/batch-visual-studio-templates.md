---
title: Tworzenie rozwiązań partii z szablony programu Visual Studio — Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak szablony projektu Visual Studio może pomóc w implementacji i uruchamiania obciążeń obliczeniowych w partii zadań Azure.
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 5e041ae2-25af-4882-a79e-3aa63c4bfb20
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5241c62e8b423b20477fc72c87303daf3d4ab43c
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="use-visual-studio-project-templates-to-jump-start-batch-solutions"></a>Szablony projektu Visual Studio umożliwia szybkie rozpoczęcie tworzenia rozwiązań partii

**Menedżer zadań** i **szablony Visual Studio procesora zadań** dla partii Podaj kod, aby ułatwić do wdrażania i uruchamiania obciążeń obliczeniowych w partii o minimalnej liczbie wysiłku. Ten dokument zawiera opis tych szablonów i zawiera wskazówki dotyczące sposobu ich używania.

> [!IMPORTANT]
> W tym artykule opisano tylko informacje dotyczące tych dwóch szablonów i przyjęto założenie, że czytelnik zna usługa partia zadań i podstawowe pojęcia związane z jego: pule, obliczeniowe węzłów, zadań i zadań, zadania Menedżer zadania, zmienne środowiskowe i inne istotne informacje. Więcej informacji można znaleźć [partii podstawy usługi Azure](batch-technical-overview.md), [Przegląd funkcji partii dla deweloperów](batch-api-basics.md), i [wprowadzenie do biblioteki partii zadań Azure dla platformy .NET](batch-dotnet-get-started.md).
> 
> 

## <a name="high-level-overview"></a>Ogólne omówienie
Menedżer zadań i zadań procesora szablony mogą służyć do tworzenia dwa składniki przydatne:

* Zadanie Menedżer zadania, które implementuje podziału zadania, które można podzielić zadania na wielu zadań, które można uruchomić osobno, równolegle.
* Procesor zadania, który może służyć do wykonywania przetwarzanie wstępne i przetwarzanie końcowe w pobliżu wiersza polecenia aplikacji.

Na przykład w przypadku renderowania film, podziału zadania czy przekształcić zadania pojedynczego filmu setek lub tysięcy oddzielnych zadań, które będzie przetwarzać osobno poszczególne ramki. Odpowiednio procesora zadań powodowałoby wywołanie aplikacji renderowania i wszystkie procesy zależne, które są wymagane do renderowania każdego ramki, a także wszelkie dodatkowe akcje (na przykład kopiowanie renderowanych ramki do lokalizacji magazynu).

> [!NOTE]
> Menedżer zadań i zadań procesora szablony są niezależne od siebie, więc można użyć zarówno lub tylko jeden z nich, w zależności od wymagań zadania obliczeniowych i na swoje preferencje.
> 
> 

Jak pokazano na poniższym diagramie, zadanie obliczeniowe, które korzysta z tych szablonów będzie przejście przez trzech etapów:

1. Kod klienta (np. aplikacji, usługi sieci web, itp.) przesyła zadania na platformie Azure, określając jako jego zadania Menedżera zadań programu Menedżer zadania usługa partia zadań.
2. Usługa partia zadań uruchamia zadanie Menedżer zadania w węźle obliczeń i podziału zadania uruchamia określoną liczbę zadań procesora zadań, w jak wiele węzły obliczeniowe zgodnie z potrzebami, na podstawie parametrów i specyfikacje w kodzie podziału zadania.
3. Zadanie zadania procesora niezależnie, Uruchom równolegle do przetwarzania danych wejściowych oraz do generowania danych wyjściowych.

![Diagram przedstawiający współdziałania kodu klienta z usługi partia zadań][diagram01]

## <a name="prerequisites"></a>Wymagania wstępne
Aby użyć szablonów partii, potrzebne następujące elementy:

* Komputer z programu Visual Studio 2015. Szablony usługi partia zadań są obecnie obsługiwane tylko dla programu Visual Studio 2015.
* Szablony partii, które są dostępne z [galerii programu Visual Studio] [ vs_gallery] jako rozszerzenia programu Visual Studio. Istnieją dwa sposoby pobrania szablonów:
  
  * Zainstalowanie szablonów przy użyciu **rozszerzenia i aktualizacje** okno dialogowe w programie Visual Studio (Aby uzyskać więcej informacji, zobacz [Znajdowanie i rozszerzenia przy użyciu programu Visual Studio][vs_find_use_ext]). W **rozszerzenia i aktualizacje** okno dialogowe pola, wyszukiwanie i pobieranie dwa rozszerzenia:
    
    * Azure Menedżer zadania wsadowego z rozdzielaczem zadania
    * Procesor zadania usługa partia zadań Azure
  * Pobieranie z galerii online dla programu Visual Studio: [szablony projektów programu Microsoft Azure partii][vs_gallery_templates]
* Jeśli planujesz używać [pakietów aplikacji](batch-application-packages.md) funkcji, aby wdrożyć Menedżer zadań i węzłach obliczeniowych partii zadań procesor, musisz połączyć konto magazynu do konta partii zadań.

## <a name="preparation"></a>Przygotowanie
Zaleca się tworzenia rozwiązania, które mogą zawierać Menedżera zadań, a także zadań procesora, ponieważ to ułatwia udostępnianie kodu między Menedżer zadań i zadań procesora programy. Aby utworzyć to rozwiązanie, wykonaj następujące kroki:

1. Otwórz program Visual Studio i wybierz **pliku** > **nowy** > **projektu**.
2. W obszarze **szablony**, rozwiń węzeł **inne typy projektów**, kliknij przycisk **rozwiązań programu Visual Studio**, a następnie wybierz **puste rozwiązanie**.
3. Wpisz nazwę, która opisuje cel to rozwiązanie (np. "LitwareBatchTaskPrograms") i aplikacji.
4. Aby utworzyć nowe rozwiązanie, kliknij przycisk **OK**.

## <a name="job-manager-template"></a>Menedżer zadań szablonu
Szablon Menedżer zadań ułatwia wdrożenie zadanie Menedżer zadania, które można wykonywać następujące czynności:

* Podziel zadania na wielu zadań.
* Przesyłanie zadań do uruchomienia w partii.

> [!NOTE]
> Aby uzyskać więcej informacji na temat zadania Menedżera zadań, zobacz [Przegląd funkcji partii dla deweloperów](batch-api-basics.md#job-manager-task).
> 
> 

### <a name="create-a-job-manager-using-the-template"></a>Utwórz Menedżer zadania przy użyciu szablonu
Aby dodać Menedżer zadań do rozwiązania, który został utworzony wcześniej, wykonaj następujące kroki:

1. Otwórz istniejącego rozwiązania programu Visual Studio.
2. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy rozwiązanie, kliknij przycisk **Dodaj** > **nowy projekt**.
3. W obszarze **Visual C#**, kliknij przycisk **chmury**, a następnie kliknij przycisk **Menedżer zadań wsadowych Azure z rozdzielaczem zadania**.
4. Wpisz nazwę opisującą aplikacji i identyfikuje tego projektu jako Menedżer zadań (np. "LitwareJobManager").
5. Aby utworzyć projekt, kliknij przycisk **OK**.
6. Na koniec Skompiluj projekt, aby wymusić Visual Studio można załadować wszystkich pakietów NuGet przywoływanego i sprawdzić, czy projekt jest prawidłowa, przed rozpoczęciem modyfikowania jej.

### <a name="job-manager-template-files-and-their-purpose"></a>Pliki szablonów Menedżer zadań i ich przeznaczenie
Podczas tworzenia projektu za pomocą szablonu Menedżer zadań generuje trzech grup plików kodu:

* Plik programu głównego (Program.cs). Zawiera punktu wejścia programu, a obsługa wyjątków najwyższego poziomu. Zwykle nie należy modyfikować.
* Katalog Framework. Zawiera pliki odpowiedzialny za "standardowy" pracy przez program Menedżer zadania — rozpakowywania parametrów dodawania zadania do zadania wsadowe, itp. Zwykle nie należy modyfikować tych plików.
* Plik podziału zadania (JobSplitter.cs). Jest to, gdzie należy umieścić specyficzne dla aplikacji logiki do dzielenia zadania do zadania.

Oczywiście można dodać dodatkowe pliki wymagane do obsługi kodu podziału zadania, w zależności od złożoności zadania dzielenia logiki.

Szablon generuje również standardowe pliki projektu .NET pliku .csproj app.config, packages.config, np.

Pozostałej części tej sekcji opisano różne pliki i ich struktury kodu i opisano, co oznacza każdej klasy.

![Visual Studio Solution Explorer przedstawiający Menedżer zadań szablon rozwiązania][solution_explorer01]

**Pliki Framework**

* `Configuration.cs`: Hermetyzuje ładowanie danych konfiguracji zadania, takie jak szczegóły konta wsadowego, poświadczenia konta magazynu połączone, zadania i informacje o zadaniu i parametrów zadania. Umożliwia także dostęp do zmiennych środowiskowych zdefiniowane partii (Zobacz ustawienia środowiska dla zadań, w dokumentacji partii) za pomocą klasy Configuration.EnvironmentVariable.
* `IConfiguration.cs`: Abstracts Implementacja klasy konfiguracji, tak aby można było testu jednostkowego z podziału zadania przy użyciu obiektu fałszywych lub zasymulować konfiguracji.
* `JobManager.cs`: Organizuje składniki programu Menedżer zadania. Jest odpowiedzialny za inicjowanie podziału zadania, wywoływania podziału zadania, a podczas wysyłania zadań zwracane przez podziału zadań do przesyłający zadania.
* `JobManagerException.cs`: Reprezentuje błąd, który wymaga Menedżer zadań zakończyć. JobManagerException jest stosowanego do opakowywania błędy "Oczekiwano", gdy określone informacje diagnostyczne można podać jako część rozwiązania.
* `TaskSubmitter.cs`: Ta klasa jest odpowiedzialny do dodawania zadań zwracane przez podziału zadań do wykonywania zadania wsadowego. Agreguje klasy JobManager sekwencji zadań w partie wydajne, ale odpowiednim dodania do zadania, następnie wywołuje TaskSubmitter.SubmitTasks wątku w tle dla każdej partii.

**Zadanie podziału**

`JobSplitter.cs`: Ta klasa zawiera specyficzne dla aplikacji logiki do dzielenia zadania do zadania. Platformę wywołuje metodę JobSplitter.Split uzyskanie sekwencję zadań, takich jak metoda zwraca je dodaje do zadania. Jest to klasa, gdzie będzie wstrzyknąć logiki zadania. Implementuje metody Split do zwrócenia sekwencję wystąpień CloudTask reprezentujący zadania, do których chcesz partycji zadania.

**Standardowe pliki projektu wiersza polecenia platformy .NET**

* `App.config`: Standardowego pliku konfiguracji aplikacji .NET.
* `Packages.config`: Standardowy plik zależności pakietu NuGet.
* `Program.cs`: Zawiera punktu wejścia programu, a obsługa wyjątków najwyższego poziomu.

### <a name="implementing-the-job-splitter"></a>Implementowanie podziału zadania
Po otwarciu projektu szablonu Menedżer zadań projektu mają plik JobSplitter.cs domyślnie. Logikę podziału zadań można wdrożyć w obciążenie, przy użyciu Pokaż metody Split() poniżej:

```csharp
/// <summary>
/// Gets the tasks into which to split the job. This is where you inject
/// your application-specific logic for decomposing the job into tasks.
///
/// The job manager framework invokes the Split method for you; you need
/// only to implement it, not to call it yourself. Typically, your
/// implementation should return tasks lazily, for example using a C#
/// iterator and the "yield return" statement; this allows tasks to be added
/// and to start running while splitting is still in progress.
/// </summary>
/// <returns>The tasks to be added to the job. Tasks are added automatically
/// by the job manager framework as they are returned by this method.</returns>
public IEnumerable<CloudTask> Split()
{
    // Your code for the split logic goes here.
    int startFrame = Convert.ToInt32(_parameters["StartFrame"]);
    int endFrame = Convert.ToInt32(_parameters["EndFrame"]);

    for (int i = startFrame; i <= endFrame; i++)
    {
        yield return new CloudTask("myTask" + i, "cmd /c dir");
    }
}
```

> [!NOTE]
> Sekcji adnotacjami `Split()` metoda jest tylko część Menedżer zadań kod szablonu, który jest przeznaczony do modyfikowania przez dodanie logiki podziału zadań do różnych zadań. Jeśli chcesz zmodyfikować innej sekcji szablonu, sprawdź, czy są zapoznaniu z działania partii i wypróbować kilka [partii przykłady kodu][github_samples].
> 
> 

Implementacji Split() ma dostęp do:

* Parametrów zadania przy użyciu `_parameters` pola.
* Obiekt CloudJob reprezentujący zadania, za pośrednictwem `_job` pola.
* Obiekt CloudTask reprezentujący zadania Menedżer zadania, za pośrednictwem `_jobManagerTask` pola.

Twoje `Split()` implementacji nie trzeba dodawać zadania bezpośrednio do zadania. Zamiast tego kod powinien zwrócić sekwencji CloudTask obiektów, a te zostaną dodane do zadania automatycznie przez klasy framework, które wywołują podziału zadania. Często jest używanie języka C# w iterator (`yield return`) funkcji do zaimplementowania rozdzielaczy zadania, ponieważ dzięki temu zadań w celu uruchomienia uruchomiona tak szybko, jak to możliwe, zamiast czekać, aż dla wszystkich zadań, które mają być obliczane.

**Niepowodzenie zadania podziału**

Jeśli Twoje podziału zadań wystąpi błąd, powinno być:

* Zakończenie sekwencji przy użyciu języka C# `yield break` instrukcji, w których przypadku Menedżer zadań będą traktowane jako pomyślnie; lub
* Zgłoś wyjątek, w którym to przypadku Menedżer zadań będą traktowane jako nieudane i może podjęta w zależności od tego, jak klient skonfigurował go).

W obu przypadkach będzie może uruchamiać wszystkie zadania już zwrócony przez podziału zadań i dodać do zadania wsadowego. Jeśli nie chcesz, aby tak się stało, następnie możesz:

* Zakończ zadanie przed powrotem z podziału zadania
* Sformułować kolekcji całe zadanie przed zwróceniem (oznacza to, zwróć `ICollection<CloudTask>` lub `IList<CloudTask>` zamiast wykonania programu podziału zadania przy użyciu iteratora C#)
* Użyj zależności zadań, aby wprowadzić wszystkie zadania, które są zależne od pomyślnego zakończenia Menedżer zadań

**Ponowne próby Menedżer zadania**

Jeśli Menedżer zadań nie powiedzie się, może zostać powtórzone, usługa partii w zależności od ustawienia ponawiania klienta. Ogólnie rzecz biorąc jest bezpieczne, ponieważ w ramach dodaje zadania do zadania, ignoruje wszystkie zadania, które już istnieją. Jednak jeśli obliczanie zadania jest kosztowna, nie możesz pociągnąć za sobą koszt ponownego obliczenia zadania, które już zostały dodane do zadania; z drugiej strony Jeśli ponowne uruchomienie nie jest gwarantowana do wygenerowania tego samego zadania identyfikatorów następnie zachowanie "Ignoruj duplikaty" zostanie nie zaczną działać. W takich przypadkach należy projektować w Twojej podziału zadania do pracy, która została już wykonana i powtarzaj go, na przykład, wykonując CloudJob.ListTasks przed rozpoczęciem umożliwiające uzyskanie zadania wykrywania.

### <a name="exit-codes-and-exceptions-in-the-job-manager-template"></a>Kody zakończenia i wyjątków w szablonie Menedżer zadań
Kody zakończenia i wyjątków udostępniają mechanizm określić wyniki działania programu, a pomagają zidentyfikować wszelkie problemy z wykonywanie programu. Menedżer zadań szablonu implementuje kody wyjścia i wyjątków opisanych w tej sekcji.

Zadanie Menedżer zadania, które jest implementowana przy użyciu szablonu Menedżer zadań mogą zwracać trzy kody zakończenia możliwe:

| Kod | Opis |
| --- | --- |
| 0 |Menedżer zadań została ukończona pomyślnie. Uruchomiono kodu podziału zadania do wykonania, a wszystkie zadania zostały dodane do zadania. |
| 1 |Zadanie Menedżer zadania nie powiodło się z powodu wyjątku "Oczekiwano" części programu. Wyjątek przetłumaczony na JobManagerException informacji diagnostycznych i, jeśli to możliwe, sugestie dotyczące rozwiązywania awarii. |
| 2 |Zadanie Menedżer zadania nie powiodło się z powodu wyjątku "nieoczekiwane". Wyjątek został zapisany w dzienniku na wyjście standardowe, ale Menedżer zadań nie mógł dodać żadnych dodatkowych informacji diagnostycznych lub korygowania. |

W przypadku niepowodzenia zadania Menedżer zadania niektóre zadania mogą nadal zostały dodane do usługi przed wystąpieniem błędu. Te zadania będą uruchamiane jako standardowe. Omówienie tę ścieżkę kodu w temacie "Niepowodzenie podziału zadania" powyżej.

Wszystkie informacje zwracane przez wyjątki są zapisywane w plikach stdout.txt i stderr.txt. Aby uzyskać więcej informacji, zobacz [obsługi błędu](batch-api-basics.md#error-handling).

### <a name="client-considerations"></a>Uwagi dotyczące klientów
W tej sekcji opisano niektóre wymagania dotyczące wdrażania klienta podczas wywoływania Menedżer zadań, na podstawie tego szablonu. Zobacz [sposób przekazywania parametrów i zmiennych środowiskowych z kodu klienta](#pass-environment-settings) szczegółowe informacje na temat przekazywania parametry i ustawienia środowiska.

**Wymagane poświadczenia**

Aby można było dodać zadania do zadania partii zadań Azure, zadanie Menedżer zadania wymaga swój adres URL konta partii zadań Azure i klucz. Należy podać je w zmiennych środowiskowych o nazwie YOUR_BATCH_URL i YOUR_BATCH_KEY. Można ustawić w Menedżerze zadań ustawienia środowiska zadań. Na przykład w języku C# klienta:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    new EnvironmentSetting("YOUR_BATCH_URL", "https://account.region.batch.azure.com"),
    new EnvironmentSetting("YOUR_BATCH_KEY", "{your_base64_encoded_account_key}"),
};
```
**Poświadczenia magazynu**

Zazwyczaj klienta nie musi podać poświadczenia konta magazynu połączone, do zadania Menedżer zadania, ponieważ () najbardziej menedżerów zadania nie trzeba jawnie uzyskać dostęp do konta magazynu połączone i (b połączonym koncie magazynu jest często dostępne do wszystkich zadań jako wspólne ustawienia środowiska dla zadania. Jeśli nie udostępniasz kont magazynu połączone za pomocą typowych ustawień środowiska, a Menedżer zadania wymaga dostępu do magazynu połączone, następnie należy określić poświadczenia magazynu połączone w następujący sposób:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    /* other environment settings */
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

**Ustawienia zadania Menedżer zadania**

Klienta należy ustawić Menedżer zadań *killJobOnCompletion* flaga **false**.

Jest zwykle bezpieczne dla klienta ustawić *runExclusive* do **false**.

Powinien używać klient *resourceFiles* lub *applicationPackageReferences* kolekcji, aby zadanie było Menedżera pliku wykonywalnego (i jego wymaganych bibliotek DLL) wdrożone w węźle obliczeń.

Domyślnie Menedżer zadań nie zostanie podjęta, jeśli działanie nie powiodło się. W zależności od logiki Menedżera zadań można włączyć ponownych prób za pomocą klienta *ograniczenia*/*maxTaskRetryCount*.

**Ustawienia zadania**

Jeśli rozdzielacz zadania emituje zadania z zależnościami, klient ustaw wartość true usesTaskDependencies zadania.

W modelu podziału zadania jest rzadko klientom chcesz dodać zadania do zadań poza tworzy podziału zadania. Klienta w związku z tym należy zwykle ustawić zadanie *onAllTasksComplete* do **terminatejob**.

## <a name="task-processor-template"></a>Szablon zadania
Szablon zadań procesora ułatwia wdrożenie procesor zadania, które można wykonywać następujące czynności:

* Skonfiguruj informacje wymagane przez poszczególne zadania wsadowego do uruchomienia.
* Wykonanie wszystkich akcji wymagane przez poszczególne zadania wsadowego.
* Zapisz dane wyjściowe zadania do magazynu trwałego.

Mimo że procesor zadania nie jest wymagany do uruchamiania zadań w partii, zaletą przy użyciu procesora zadań jest zapewnia otoki do wykonania wszystkich zadań wykonywania akcji w jednej lokalizacji. Na przykład czy należy wykonać kilka aplikacji w kontekście każdego zadania, czy należy skopiować dane do magazynu trwałego po zakończeniu poszczególnych zadań.

Akcje wykonywane przez procesor zadań może być jako prosty lub złożonych, a wiele lub kilku, co jest wymagane przez obciążenie. Ponadto implementując wszystkie akcje zadania w jednym procesorze zadań, można łatwo zaktualizować lub dodać akcje na podstawie zmian w aplikacji lub wymagań obciążenia. Jednak w niektórych przypadkach procesor zadania może nie być optymalne rozwiązanie dla implementacji można dodać niepotrzebnych złożoności, na przykład podczas uruchamiania zadania, które można szybko uruchomić z prostego wiersza polecenia.

### <a name="create-a-task-processor-using-the-template"></a>Tworzenie zadań procesora przy użyciu szablonu
Aby dodać procesora zadań do rozwiązania, który został utworzony wcześniej, wykonaj następujące kroki:

1. Otwórz istniejącego rozwiązania programu Visual Studio.
2. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy rozwiązanie, kliknij przycisk **Dodaj**, a następnie kliknij przycisk **nowy projekt**.
3. W obszarze **Visual C#**, kliknij przycisk **chmury**, a następnie kliknij przycisk **Azure partii zadań procesora**.
4. Wpisz nazwę, opis aplikacji, która identyfikuje ten projekt jako zadanie procesora (np. "LitwareTaskProcessor").
5. Aby utworzyć projekt, kliknij przycisk **OK**.
6. Na koniec Skompiluj projekt, aby wymusić Visual Studio można załadować wszystkich pakietów NuGet przywoływanego i sprawdzić, czy projekt jest prawidłowa, przed rozpoczęciem modyfikowania jej.

### <a name="task-processor-template-files-and-their-purpose"></a>Pliki szablonów zadań procesora i ich przeznaczenie
Podczas tworzenia projektu za pomocą szablonu procesora zadań generuje trzech grup plików kodu:

* Plik programu głównego (Program.cs). Zawiera punktu wejścia programu, a obsługa wyjątków najwyższego poziomu. Zwykle nie należy modyfikować.
* Katalog Framework. Zawiera pliki odpowiedzialny za "standardowy" pracy przez program Menedżer zadania — rozpakowywania parametrów dodawania zadania do zadania wsadowe, itp. Zwykle nie należy modyfikować tych plików.
* Plik procesora zadań (TaskProcessor.cs). Jest to, gdzie należy umieścić specyficzne dla aplikacji logiki wykonywania zadania (zazwyczaj wywołując do istniejącego pliku wykonywalnego). Wstępne i przetwarzanie końcowe kodu, na przykład pobranie dodatkowych danych lub przekazywania plików wyników również tu jest.

Oczywiście można dodać dodatkowe pliki wymagane do obsługi zadań procesora kodu, w zależności od złożoności zadania dzielenia logiki.

Szablon generuje również standardowe pliki projektu .NET pliku .csproj app.config, packages.config, np.

Pozostałej części tej sekcji opisano różne pliki i ich struktury kodu i opisano, co oznacza każdej klasy.

![Visual Studio Solution Explorer przedstawiający rozwiązanie szablonu zadań procesora][solution_explorer02]

**Pliki Framework**

* `Configuration.cs`: Hermetyzuje ładowanie danych konfiguracji zadania, takie jak szczegóły konta wsadowego, poświadczenia konta magazynu połączone, zadania i informacje o zadaniu i parametrów zadania. Umożliwia także dostęp do zmiennych środowiskowych zdefiniowane partii (Zobacz ustawienia środowiska dla zadań, w dokumentacji partii) za pomocą klasy Configuration.EnvironmentVariable.
* `IConfiguration.cs`: Abstracts Implementacja klasy konfiguracji, tak aby można było testu jednostkowego z podziału zadania przy użyciu obiektu fałszywych lub zasymulować konfiguracji.
* `TaskProcessorException.cs`: Reprezentuje błąd, który wymaga Menedżer zadań zakończyć. TaskProcessorException jest stosowanego do opakowywania błędy "Oczekiwano", gdy określone informacje diagnostyczne można podać jako część rozwiązania.

**Procesor zadania**

* `TaskProcessor.cs`: Uruchamia zadanie. Platformę wywołuje metodę TaskProcessor.Run. Jest to klasa, gdzie będzie wstrzyknąć specyficzne dla aplikacji logiki zadania. Zaimplementuj metodę Uruchom, aby:
  * Analizowanie i sprawdź poprawność parametrów zadania
  * Redagowanie zewnętrzny program, który chcesz wywołać wiersz polecenia
  * Zaloguj się żadnych informacji diagnostycznych, które mogą wymagać na potrzeby debugowania
  * Rozpocznij proces przy użyciu tego wiersza polecenia
  * Poczekaj na zakończenie procesu
  * Przechwyć kod zakończenia procesu, aby ustalić, czy zakończyło się pomyślnie, lub nie powiodło się
  * Zapisz wszystkie pliki danych wyjściowych, które chcesz zachować do magazynu trwałego

**Standardowe pliki projektu wiersza polecenia platformy .NET**

* `App.config`: Standardowego pliku konfiguracji aplikacji .NET.
* `Packages.config`: Standardowy plik zależności pakietu NuGet.
* `Program.cs`: Zawiera punktu wejścia programu, a obsługa wyjątków najwyższego poziomu.

## <a name="implementing-the-task-processor"></a>Implementowanie zadań procesora
Po otwarciu projektu szablonu zadań procesora projektu mają plik TaskProcessor.cs domyślnie. Można zaimplementować logiki wykonywania zadań w obciążenia przy użyciu metody Run() pokazano poniżej:

```csharp
/// <summary>
/// Runs the task processing logic. This is where you inject
/// your application-specific logic for decomposing the job into tasks.
///
/// The task processor framework invokes the Run method for you; you need
/// only to implement it, not to call it yourself. Typically, your
/// implementation will execute an external program (from resource files or
/// an application package), check the exit code of that program and
/// save output files to persistent storage.
/// </summary>
public async Task<int> Run()

{
    try
    {
        //Your code for the task processor goes here.
        var command = $"compare {_parameters["Frame1"]} {_parameters["Frame2"]} compare.gif";
        using (var process = Process.Start($"cmd /c {command}"))
        {
            process.WaitForExit();
            var taskOutputStorage = new TaskOutputStorage(
            _configuration.StorageAccount,
            _configuration.JobId,
            _configuration.TaskId
            );
            await taskOutputStorage.SaveAsync(
            TaskOutputKind.TaskOutput,
            @"..\stdout.txt",
            @"stdout.txt"
            );
            return process.ExitCode;
        }
    }
    catch (Exception ex)
    {
        throw new TaskProcessorException(
        $"{ex.GetType().Name} exception in run task processor: {ex.Message}",
        ex
        );
    }
}
```
> [!NOTE]
> Sekcji adnotacjami w metodzie Run() jest tylko część zadań procesora kod szablonu, który służy do modyfikowania przez dodanie logiki wykonywania zadań w obciążenie. Jeśli chcesz zmodyfikować innej sekcji szablonu, należy najpierw zapoznać się z działania partii przez zapoznaniu się z dokumentacją partii i wypróbować kilka przykładów kodu partii.
> 
> 

Metody Run() jest odpowiedzialna za uruchamianie wiersza polecenia, co najmniej jednego procesu, oczekiwanie na ukończenie, wszystkie procesu uruchamiania zapisywania wyników, a na koniec zwróciło kod zakończenia. Metoda Run() jest, gdzie implementują logikę przetwarzania dla zadań. Zadanie framework procesora wywołuje metodę Run() dla Ciebie; nie trzeba wywołać ją samodzielnie.

Implementacji Run() ma dostęp do:

* Parametry zadania za pomocą `_parameters` pola.
* Identyfikatory poleceń i zadań, za pomocą `_jobId` i `_taskId` pól.
* Konfiguracja zadania za pomocą `_configuration` pola.

**Niepowodzenie zadania**

W razie awarii wyjściu metody Run() zgłoszeniu wyjątku, ale to pozostawia obsługi najwyższego poziomu wyjątek w formancie kodu zakończenia zadania. Jeśli chcesz kontrolować kod zakończenia tak, aby odróżnić różnego rodzaju błąd, na przykład w celach diagnostycznych albo niektóre rodzaje uszkodzenia należy zakończyć zadania i inne osoby nie powinien należy zakończyć metody Run() zwracając kod wyjścia równy zero. Staje się on kodu zakończenia zadania.

### <a name="exit-codes-and-exceptions-in-the-task-processor-template"></a>Kody zakończenia i wyjątków w szablonie zadań procesora
Kody zakończenia i wyjątków udostępniają mechanizm określić wyniki działania programu, a ich pomocne w identyfikacji problemów z wykonywanie programu. Szablon procesora zadanie implementuje kody wyjścia i wyjątków opisanych w tej sekcji.

Zadanie procesora zadań, które jest implementowana przy użyciu szablonu zadań procesora mogą zwracać trzy kody zakończenia możliwe:

| Kod | Opis |
| --- | --- |
| [Process.ExitCode][process_exitcode] |Uruchomiono procesora zadań do wykonania. Należy pamiętać, że nie oznacza to, możesz wywołać program pomyślnego — tylko że procesor zadań pomyślnie wywołany i wykonać wszelkie przetwarzania końcowego bez wyjątków. Znaczenie kod zakończenia jest zależna od wywołany program — zwykle kod zakończenia 0 oznacza programu zakończyło się pomyślnie i inny kod zakończenia oznacza, że program nie powiodło się. |
| 1 |Procesor zadania nie powiodło się w części "Oczekiwano" programu. Wyjątek przetłumaczony na `TaskProcessorException` informacji diagnostycznych i, jeśli to możliwe, sugestie dotyczące rozwiązywania awarii. |
| 2 |Procesor zadania nie powiodło się z powodu wyjątku "nieoczekiwane". Wyjątek został zapisany w dzienniku na wyjście standardowe, ale procesora zadań nie mógł dodać żadnych dodatkowych informacji diagnostycznych lub korygowania. |

> [!NOTE]
> Jeśli program, który wywołuje korzysta z kodów zakończenia 1 i 2 wskazująca tryby określony błąd, następnie przy użyciu kodów zakończenia 1 i 2 dla błędów procesor zadania jest niejednoznaczna. Kody błędów procesora te zadania można zmienić na kody wyjścia charakterystyczne, edytując przypadków wyjątek w pliku Program.cs.
> 
> 

Wszystkie informacje zwracane przez wyjątki są zapisywane w plikach stdout.txt i stderr.txt. Aby uzyskać więcej informacji zobacz błąd obsługi, w dokumentacji usługi partia zadań.

### <a name="client-considerations"></a>Uwagi dotyczące klientów
**Poświadczenia magazynu**

Jeśli procesor zadań korzysta z magazynu obiektów blob platformy Azure do utrwalenia danych wyjściowych, na przykład za pomocą biblioteki pomocnika konwencje pliku, a następnie będzie potrzebował dostępu do *albo* poświadczeń konta magazynu w chmurze *lub* adresu URL kontenera obiektu blob zawierającego sygnatury dostępu współdzielonego (SAS). Szablon obsługuje podawania poświadczeń za pomocą wspólnych zmiennych środowiskowych. Klient może przekazać poświadczeń magazynu w następujący sposób:

```csharp
job.CommonEnvironmentSettings = new [] {
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

Konto magazynu jest dostępna w klasie TaskProcessor za pośrednictwem `_configuration.StorageAccount` właściwości.

Jeśli wolisz używać adresu URL kontenera z sygnatury dostępu Współdzielonego, można również przekazać to za pomocą ustawienia środowiska typowe zadania, ale zadań procesora szablon nie zawiera wbudowaną obsługę to.

**Konfiguracja magazynu**

Zaleca się, że zadanie Menedżer klienta lub zadania utworzyć kontenerach wymagane przez zadania przed dodaniem zadań dla zadania. Jest to konieczne, korzystając z sygnatury dostępu Współdzielonego adresu URL kontenera, w związku adresu URL nie ma uprawnienia do tworzenia kontenera. Zaleca się nawet wtedy, gdy przekazujesz poświadczeń konta magazynu, ponieważ zapisuje każdego zadania konieczności wywołać CloudBlobContainer.CreateIfNotExistsAsync w kontenerze.

## <a name="pass-parameters-and-environment-variables"></a>Przekaż parametry i zmienne środowiskowe
### <a name="pass-environment-settings"></a>Przekaż ustawienia środowiska
Klient może przekazać informacje do zadania Menedżer zadania w formularzu Ustawienia środowiska. Te informacje można następnie używane przez zadanie Menedżer zadania, podczas generowania zadanie zadania procesora, których będzie uruchamiany jako część zadania obliczeń. Przykładami informacji, które można przekazać jako ustawienia środowiska są:

* Klucze konta magazynu nazwy i konta
* Adres URL konta usługi partia zadań
* Klucz konta usługi partia zadań

Usługa partia zadań ma prosty mechanizm przekazywania ustawienia środowiska do zadania Menedżer zadania przy użyciu `EnvironmentSettings` właściwości w [Microsoft.Azure.Batch.JobManagerTask][net_jobmanagertask].

Na przykład, aby pobrać `BatchClient` wystąpienia dla konta usługi partia zadań, można przekazać jako adres URL i udostępnione klucza poświadczenia dla konta wsadowego kodu zmienne środowiskowe z klienta. Podobnie Aby uzyskać dostęp do konta magazynu, który jest połączony z kontem usługi partia zadań, można przekazać nazwę konta magazynu i klucza konta magazynu jako zmienne środowiskowe.

### <a name="pass-parameters-to-the-job-manager-template"></a>Przekazania parametrów do szablonu Menedżer zadań
W wielu przypadkach jest przydatne do przekazania parametrów dla zadania do zadania Menedżer zadania, do kontrolowania podzielić proces zadania lub Konfigurowanie zadań dla zadania. Można to zrobić, przekazując plik JSON o nazwie parameters.JSON następującym kodem jako plik zasobów dla zadania Menedżer zadania. Parametry mogą następnie pełnić dostępne w `JobSplitter._parameters` w szablonie Menedżer zadań.

> [!NOTE]
> Parametr wbudowany program obsługi wspiera tylko słowniki ciąg ciąg. Jeśli chcesz przekazać wartości złożonych JSON jako wartości parametrów, należy przekazać je jako ciągi i analizować je w podziału zadania lub modyfikuje framework `Configuration.GetJobParameters` metody.
> 
> 

### <a name="pass-parameters-to-the-task-processor-template"></a>Przekazania parametrów do szablonu zadania procesora
Można również przekazania parametrów do poszczególnych zadań wdrożone przy użyciu szablonu zadań procesora. Podobnie jak przy użyciu szablonu Menedżer zadania zadań procesora szablon wyszukuje plik zasobów o nazwie

parameters.JSON następującym kodem, a jeśli go znaleziono ładuje go jako słownik parametrów. Istnieje kilka opcji dotyczących sposobu przekazania parametrów do zadań procesora zadań:

* Ponowne użycie parametrów zadania JSON. Działa to dobrze, jeśli tylko parametry są pokazane na poziomie zadania (na przykład renderowania wysokość i szerokość). Aby to zrobić, tworząc CloudTask w podziału zadania, Dodaj odwołanie do obiektu pliku parameters.JSON następującym kodem zasobów z zadania Menedżer zadania ResourceFiles (`JobSplitter._jobManagerTask.ResourceFiles`) CloudTask ResourceFiles kolekcji.
* Generowanie przekazania dokumentu parameters.JSON następującym kodem poszczególnych zadań w ramach wykonywania podziału zadania i odwołanie do tego obiektu blob w kolekcji plików zasobów zadania. Jest to konieczne, jeśli różne zadania mają różne parametry. Przykładem może być scenariusza renderowania 3W, gdzie do indeksu ramki jest przekazany do zadania jako parametr.

> [!NOTE]
> Parametr wbudowany program obsługi wspiera tylko słowniki ciąg ciąg. Jeśli chcesz przekazać wartości złożonych JSON jako wartości parametrów, należy przekazać je jako ciągi i analizować je w procesorze zadań lub modyfikuje framework `Configuration.GetTaskParameters` metody.
> 
> 

## <a name="next-steps"></a>Kolejne kroki
### <a name="persist-job-and-task-output-to-azure-storage"></a>Utrwalanie i zadań dane wyjściowe do magazynu Azure
Kolejnym narzędziem pomocne w partii opracowywanie rozwiązań jest [konwencje pliku wsadowego Azure][nuget_package]. Ta biblioteka klas programu .NET (obecnie w wersji zapoznawczej) w aplikacjach partiami platformy .NET umożliwia łatwe przechowywanie i pobieranie danych wyjściowych zadania, do i z usługi Azure Storage. [Utrwalić danych wyjściowych i zadań partii zadań Azure](batch-task-output.md) zawiera pełne omówienie biblioteki i jej użycia.

### <a name="batch-forum"></a>Forum usługi partia zadań
[Forum usługi partia zadań Azure] [ forum] w witrynie MSDN jest doskonałym miejscem do omówienia partii i zadać pytania dotyczące usługi. HEAD na za pośrednictwem przydatne Posty "trwałe" i opublikuj swoje pytania powstałych podczas tworzenia własnych rozwiązań partii.

[forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
[net_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobmanagertask.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[process_exitcode]: https://msdn.microsoft.com/library/system.diagnostics.process.exitcode.aspx
[vs_gallery]: https://visualstudiogallery.msdn.microsoft.com/
[vs_gallery_templates]: https://go.microsoft.com/fwlink/?linkid=820714
[vs_find_use_ext]: https://msdn.microsoft.com/library/dd293638.aspx

[diagram01]: ./media/batch-visual-studio-templates/diagram01.png
[solution_explorer01]: ./media/batch-visual-studio-templates/solution_explorer01.png
[solution_explorer02]: ./media/batch-visual-studio-templates/solution_explorer02.png
