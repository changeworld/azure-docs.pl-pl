---
title: Tworzenie rozwiązań za pomocą szablonów programu Visual Studio — usługi Azure Batch | Dokumentacja firmy Microsoft
description: Dowiedz się, jak szablony projektów Visual Studio pomoże Ci zaimplementować i uruchomić obciążenia wymagające wielu obliczeń w usłudze Azure Batch.
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 5e041ae2-25af-4882-a79e-3aa63c4bfb20
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 085bfa582b676f34a02e4c1c5ae7e69c49e5cb4e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60550076"
---
# <a name="use-visual-studio-project-templates-to-jump-start-batch-solutions"></a>Szybko Rozpocznij korzystanie z rozwiązań usługi Batch przy użyciu szablonów projektu programu Visual Studio

**Menedżer zadań** i **szablony zadań procesora Visual Studio** dla usługi Batch zapewniają kod, aby pomóc Ci do wdrażania i uruchamiania obciążeń intensywnie korzystających z obliczeń w usłudze Batch z najmniejszą ilością nakładu pracy. Ten dokument zawiera opis tych szablonów i zawiera wskazówki dotyczące sposobu ich używania.

> [!IMPORTANT]
> W tym artykule omówiono tylko informacje dotyczące tych dwóch szablonów i przyjęto założenie, że czytelnik zna usługi Batch i kluczowych pojęć odnoszących się do niego: pule obliczeniowe węzłów, zadań i zadań, zadania Menedżer zadania, zmienne środowiskowe i inne istotne informacje. Więcej informacji można znaleźć [podstawy usługi Azure Batch](batch-technical-overview.md) i [omówienie funkcji usługi Batch dla deweloperów](batch-api-basics.md).
> 
> 

## <a name="high-level-overview"></a>Ogólne omówienie
Szablony Menedżera zadań i zadań procesora może służyć do utworzenia dwóch składników przydatne:

* Zadanie Menedżera zadań, które implementuje rozdzielacz zadania, które można podzielić zadanie na wiele podzadań, które mogą działać niezależnie, równolegle.
* Procesor zadania, który może służyć do wykonywania przetwarzania wstępnego i przetwarzanie końcowe wokół aplikacji wiersza polecenia.

Na przykład w przypadku renderowania filmu rozdzielacz zadania będzie przekształcając zadania pojedynczego filmu setek lub tysięcy osobne zadania, które może przetwarzać oddzielnie poszczególnych klatek. Odpowiednio procesor zadania powodowałoby wywołanie aplikacji do renderowania i wszystkie procesy zależne, które są wymagane do renderowania każdej ramki, a także wykonywać żadnych dodatkowych akcji (na przykład kopiowanie renderowaną ramkę do lokalizacji magazynu).

> [!NOTE]
> Szablony Menedżera zadań i zadań procesora są niezależne od siebie nawzajem, więc możesz używać zarówno lub tylko jeden z nich w zależności od wymagań zadania obliczeniowe, a także od preferencji.
> 
> 

Jak pokazano na poniższym diagramie, zadanie obliczeniowe, które korzysta z tych szablonów będzie przejście przez trzy etapy:

1. Kod klienta (np. aplikacji, usługi sieci web itp.) przesyła zadanie usługi Batch na platformie Azure, określenie zgodnie z jego zadanie Menedżera zadań programu Menedżer zadań.
2. Usługa partia zadań uruchamia zadanie podrzędne Menedżera zadań w węźle obliczeniowym i podziału zadanie uruchamia określoną liczbę zadań procesora zadania, na, jako wiele węzłów obliczeniowych zgodnie z wymaganiami, na podstawie parametrów i specyfikacje w kodzie rozdzielacz zadania.
3. Zadania procesora zadania są niezależne uruchamiane równolegle, aby przetwarzać dane wejściowe i generować dane wyjściowe.

![Diagram przedstawiający, jak kod klienta wchodzi w interakcję z usługą Batch][diagram01]

## <a name="prerequisites"></a>Wymagania wstępne
Aby użyć szablonów usługi Batch, potrzebne następujące elementy:

* Komputer z programu Visual Studio 2015. Szablony usługi Batch są obecnie obsługiwane tylko w przypadku programu Visual Studio 2015.
* Szablony usługi Batch, które są dostępne z [galerii Visual Studio] [ vs_gallery] jako rozszerzenia programu Visual Studio. Istnieją dwa sposoby pobrania szablonów:
  
  * Zainstaluj szablonów przy użyciu **rozszerzenia i aktualizacje** okno dialogowe w programie Visual Studio (Aby uzyskać więcej informacji, zobacz [Znajdowanie i przy użyciu rozszerzenia programu Visual Studio][vs_find_use_ext]). W **rozszerzenia i aktualizacje** okna dialogowego pole, wyszukiwanie i pobieranie dwa następujące rozszerzenia:
    
    * Usługa Azure Batch Menedżer zadań z rozdzielaczem zadania
    * Azure Batch Task Processor
  * Pobieranie z galerii online dla programu Visual Studio: [Szablony projektów pakietu Microsoft Azure Batch][vs_gallery_templates]
* Jeśli planujesz używać [pakiety aplikacji](batch-application-packages.md) funkcji do wdrożenia Menedżer zadań i węzłów obliczeniowych procesor zadania usługi Batch, należy połączyć konto usługi storage z kontem usługi Batch.

## <a name="preparation"></a>Przygotowanie
Zalecamy utworzenie rozwiązania, które mogą zawierać Menedżera zadań, a także procesor zadania, ponieważ ten może ułatwić umożliwiające udostępnianie kodu między Menedżer zadań i zadań procesora programów. Aby utworzyć to rozwiązanie, wykonaj następujące kroki:

1. Otwórz program Visual Studio i wybierz **pliku** > **New** > **projektu**.
2. W obszarze **szablony**, rozwiń węzeł **inne typy projektów**, kliknij przycisk **Visual Studio Solutions**, a następnie wybierz pozycję **puste rozwiązanie**.
3. Wpisz nazwę, która opisuje przeznaczenie tego rozwiązania (np. "LitwareBatchTaskPrograms") i aplikacji.
4. Aby utworzyć nowe rozwiązanie, kliknij przycisk **OK**.

## <a name="job-manager-template"></a>Szablon Menedżera zadań
Szablon Menedżera zadań pomoże Ci zaimplementować zadanie Menedżera zadań, które można wykonać następujące czynności:

* Zadania można podzielić na wiele podzadań.
* Przesyłanie zadań do uruchomienia w usłudze Batch.

> [!NOTE]
> Aby uzyskać więcej informacji na temat zadań Menedżera zobacz [omówienie funkcji usługi Batch dla deweloperów](batch-api-basics.md#job-manager-task).
> 
> 

### <a name="create-a-job-manager-using-the-template"></a>Tworzenie przy użyciu szablonu Menedżera zadań
Aby dodać Menedżera zadań do rozwiązania, który został utworzony wcześniej, wykonaj następujące kroki:

1. Otwórz istniejące rozwiązanie w programie Visual Studio.
2. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy rozwiązanie, kliknij przycisk **Dodaj** > **nowy projekt**.
3. W obszarze **Visual C#**, kliknij przycisk **chmury**, a następnie kliknij przycisk **Menedżera zadania usługi Batch Azure z rozdzielaczem zadania**.
4. Wpisz nazwę, opis aplikacji, która identyfikuje ten projekt jako Menedżer zadań (np. "LitwareJobManager").
5. Aby utworzyć projekt, kliknij przycisk **OK**.
6. Na koniec Skompiluj projekt, aby wymusić programu Visual Studio można załadować wszystkich pakietów NuGet odwołania i sprawdź, czy projekt jest prawidłowa, przed rozpoczęciem modyfikowania go.

### <a name="job-manager-template-files-and-their-purpose"></a>Pliki szablonów Menedżera zadań i ich przeznaczenia
Podczas tworzenia projektu przy użyciu szablonu Menedżera zadań, generuje trzy grupy plików kodu:

* Plik główny program (Program.cs). Zawiera punktu wejścia programu, a obsługa wyjątków najwyższego poziomu. Zwykle nie należy modyfikować tego.
* Katalogu struktury. Zawiera pliki, które są odpowiedzialne za "standardowy" pracy w programie Menedżer zadania — rozpakowywania parametrów, dodawania zadania do zadania usługi Batch, itp. Zwykle nie należy modyfikować tych plików.
* Plik rozdzielacz zadania (JobSplitter.cs). Jest to, gdzie należy umieścić logikę specyficzne dla aplikacji do dzielenia zadania do zadania.

Oczywiście możesz dodać dodatkowe pliki wymagane do obsługi kodu rozdzielacz zadania, w zależności od złożoności zadania dzielenie logiki.

Szablon generuje również standardowe pliki projektów .NET plik .csproj app.config, packages.config, np.

Pozostałej części tej sekcji opisano różne pliki i ich struktury kodu i wyjaśniono, jak działa każdej klasy.

![Visual Studio Eksplorator rozwiązań Menedżer zadań szablon rozwiązania][solution_explorer01]

**Pliki Framework**

* `Configuration.cs`: Hermetyzuje ładowanie danych konfiguracji zadania, takie jak szczegóły konta usługi Batch, poświadczenia konta połączonego magazynu, zadania i informacji o zadaniu i parametrów zadania. Umożliwia także dostęp do zmiennych środowiskowych zdefiniowanych przez usługi Batch (Zobacz ustawienia środowiska dla podzadań w dokumentacji usługi Batch) za pośrednictwem klasy Configuration.EnvironmentVariable.
* `IConfiguration.cs`: Przenosi implementacji klasy konfiguracji, aby można było test jednostkowy usługi rozdzielacz zadania za pomocą obiektu konfiguracji fałszywych lub makiety.
* `JobManager.cs`: Organizuje składniki programu Menedżer zadań. Jest odpowiedzialny za inicjowanie rozdzielacz zadania, wywoływanie rozdzielacz zadania i wysyła zadania zwrócony przez rozdzielacz zadania osoba przesyłająca zadanie.
* `JobManagerException.cs`: Reprezentuje błąd, który wymaga Menedżer zadań zakończyć. JobManagerException jest używany do opakowywania błędy 'oczekiwany', gdzie można podać szczegółowe informacje diagnostyczne jako część rozwiązania.
* `TaskSubmitter.cs`: Ta klasa jest odpowiedzialni za dodawanie zadań zwrócony przez rozdzielacz zadania wsadowego. Agregacje klasy JobManager sekwencji zadań w partie wydajne, ale czas dodania do zadania, następnie wywołuje TaskSubmitter.SubmitTasks na wątku w tle dla każdej partii.

**Splitter — zadanie**

`JobSplitter.cs`: Ta klasa zawiera logikę specyficzną dla aplikacji do dzielenia zadania do zadania. Struktura wywołuje metodę JobSplitter.Split sekwencji zadań, które dodaje do zadania, ponieważ metoda ta zwraca je uzyskać. Jest to klasa, gdzie należy wstrzyknąć logikę zadania. Implementuje metody Split w celu zwrócenia sekwencji wystąpień CloudTask reprezentujący zadania, do których mają być partycji zadania.

**Standardowe pliki projektu wiersza polecenia platformy .NET**

* `App.config`: Standardowa pliku konfiguracyjnego aplikacji .NET.
* `Packages.config`: Standardowy plik zależności pakietu NuGet.
* `Program.cs`: Zawiera punktu wejścia programu, a obsługa wyjątków najwyższego poziomu.

### <a name="implementing-the-job-splitter"></a>Implementowanie Splitter — zadanie
Po otwarciu projektu szablonu Menedżera zadań, projekt będzie miał plik JobSplitter.cs domyślnie. Można zaimplementować logikę podziału zadań w obciążenia przy użyciu Split() metoda wyświetlane poniżej:

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
> W sekcji adnotacjami w `Split()` metodą jest tylko część kod szablonu Menedżera zadań, które jest przeznaczone do można zmodyfikować przez dodanie logiki do podziału zadań do różnych zadań. Jeśli chcesz zmodyfikować innej części szablonu, należy upewnić się, czy zapoznaniu z działaniem usługi Batch i wypróbować niektóre z [przykłady kodu dla usługi Batch][github_samples].
> 
> 

Twoja implementacja Split() ma dostęp do:

* Parametry zadania za pośrednictwem `_parameters` pola.
* Obiekt CloudJob reprezentujący zadanie, za pośrednictwem `_job` pola.
* Obiekt CloudTask reprezentujący zadanie Menedżera zadań, za pośrednictwem `_jobManagerTask` pola.

Twoje `Split()` wdrożenia nie trzeba bezpośrednio Dodawanie podzadań do zadania. Zamiast tego kod powinien zwrócić sekwencji CloudTask obiektów, a te zostaną dodane do zadania automatycznie przez klasy framework, które wywołują rozdzielacz zadania. Często jest używanie języka C# firmy iterator (`yield return`) funkcji do zaimplementowania rozdzielaczy zadania, ponieważ pozwala ono zadania rozpocząć uruchamianie tak szybko, jak to możliwe, zamiast czekać, aż dla wszystkich zadań, które mają być obliczane.

**Niepowodzenie zadania z rozdzielacza**

Jeśli rozdzielacz swoje zadania wystąpi błąd, powinno być:

* Zakończ sekwencję przy użyciu języka C# `yield break` instrukcji, w których przypadku Menedżer zadań będą traktowane jako kończy się pomyślnie; lub
* Zgłoś wyjątek, w którym przypadku, gdy Menedżer zadań będą traktowane jako nie powiodła się i może ponowione w zależności od tego, jak klient skonfigurował go).

W obu przypadkach będzie może uruchamiać wszystkie zadania, które już zwracane przez zadanie rozdzielacza i dodawane do zadania usługi Batch. Jeśli nie chcesz, aby tak się stało, następnie można wykonać następujące akcje:

* Zakończenie zadania przed powrotem z podziału pracy
* Formułowanie kolekcji całego zadania przed zwróceniem (czyli zwracają `ICollection<CloudTask>` lub `IList<CloudTask>` a nie Implementowanie swoje rozdzielacz zadania za pomocą iteratora C#)
* Użyj zależności zadań podrzędnych, aby wprowadzić wszystkie zadania, które są zależne od pomyślnego zakończenia Menedżer zadań

**Zadanie Menedżera prób**

Jeśli Menedżer zadań nie powiedzie się, może zostać powtórzone, przez usługę Batch, w zależności od ustawienia ponawiania prób klienta. Ogólnie rzecz biorąc jest to bezpieczne, ponieważ struktura dodaje zadania podrzędne do zadania, ignoruje wszelkie zadania, które już istnieją. Jednak jeśli obliczanie zadania jest kosztowne, może nie chcesz ponosić kosztów ponownego obliczenia zadania, które zostały już dodane do zadania; z drugiej strony Jeśli ponownie uruchomić, nie jest gwarantowane do wygenerowania tego samego zadania identyfikatorów następnie przycisk Ignoruj, duplikaty"zachowanie będzie nie zaczną działać. W takich przypadkach należy projektować podziału swoje zadania w taki sposób, aby wykryć pracy, która została już wykonana i powtarzaj go, na przykład, wykonując CloudJob.ListTasks przed rozpoczęciem tymczasowo wstrzymać zadania.

### <a name="exit-codes-and-exceptions-in-the-job-manager-template"></a>Kody zakończenia i wyjątków w szablonie Menedżera zadań
Kody zakończenia i wyjątki zapewniają mechanizm do określenia wynik działania programu, a może pomóc zidentyfikować wszelkie problemy z wykonywaniem program. Szablon Menedżera zadań implementuje kody zakończenia i wyjątków opisanych w tej sekcji.

Zadanie Menedżera zadań, który jest implementowany przy użyciu szablonu Menedżera zadań może zwrócić trzy kody zakończenia możliwe:

| Kod | Opis |
| --- | --- |
| 0 |Menedżer zadań została ukończona pomyślnie. Kod rozdzielacz zadanie zakończyło się, a wszystkie zadania zostały dodane do zadania. |
| 1 |Zadanie podrzędne Menedżera zadań nie powiodło się z powodu wyjątku w 'oczekiwany' części programu. Wyjątek został przetłumaczony na JobManagerException zawierających informacje diagnostyczne i, jeśli jest to możliwe, sugestie dotyczące rozwiązywania błędu. |
| 2 |Zadanie podrzędne Menedżera zadań nie powiodło się z powodu wyjątku "Nieoczekiwany". Wyjątek został zalogowany do wyjścia standardowego. Jednak Menedżer zadań nie mógł dodać żadnych dodatkowych informacji diagnostycznych lub korygowania. |

W przypadku niepowodzenia zadania Menedżer zadania niektóre zadania mogą nadal zostały dodane do usługi przed wystąpieniem błędu. Te zadania będą uruchamiane w zwykły sposób. Omówienie tej ścieżki kodu, zobacz "Niepowodzenie rozdzielacz zadania" powyżej.

Wszystkie informacje, które są zwracane przez wyjątki są zapisywane w plikach stdout.txt i stderr.txt. Aby uzyskać więcej informacji, zobacz [obsługę błędów](batch-api-basics.md#error-handling).

### <a name="client-considerations"></a>Uwagi dotyczące klientów
W tej sekcji opisano niektóre wymagania dotyczące wdrażania klienta podczas wywoływania Menedżera zadań na podstawie tego szablonu. Zobacz [sposób przekazywania parametrów i zmiennych środowiskowych z poziomu kodu klienta](#pass-environment-settings) szczegółowe informacje na temat przekazywania parametrów i ustawienia środowiska.

**Wymagane poświadczenia**

Aby dodać zadania do zadania usługi Azure Batch, zadanie podrzędne Menedżera zadań wymaga adres URL konta usługi Azure Batch i klucz. Należy przekazać je w zmiennych środowiskowych o nazwie YOUR_BATCH_URL i YOUR_BATCH_KEY. Możesz ustawić te Menedżer zadań ustawienia środowiska dla zadań. Na przykład w języku C# klienta:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    new EnvironmentSetting("YOUR_BATCH_URL", "https://account.region.batch.azure.com"),
    new EnvironmentSetting("YOUR_BATCH_KEY", "{your_base64_encoded_account_key}"),
};
```
**Poświadczenia magazynu**

Zazwyczaj klient nie trzeba podać poświadczenia konta połączonego magazynu, aby zadanie podrzędne Menedżera zadań, ponieważ nie trzeba jawnie dostęp na połączonym koncie magazynu () większości menedżerów zadania i (b) na połączonym koncie magazynu znajduje się często do wszystkich zadań jako wspólne ustawienia środowiska dla zadania. Jeśli są one udostępniane na połączonym koncie magazynu za pośrednictwem wspólne ustawienia środowiska, a Menedżer zadania wymaga dostępu do połączonego magazynu, następnie należy określić poświadczenia magazynu połączone w następujący sposób:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    /* other environment settings */
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

**Ustawienia zadania Menedżer zadania**

Klienta należy ustawić Menedżer zadań *killJobOnCompletion* flaga **false**.

Zazwyczaj jest bezpieczne dla klienta ustawić *runExclusive* do **false**.

Którego powinien używać klient *resourceFiles* lub *applicationPackageReferences* kolekcji, aby zadanie było Menedżera pliku wykonywalnego (i jej wymagany plik dll) wdrożone w węźle obliczeniowym.

Domyślnie Menedżer zadań nie zostanie podjęta, jeśli zakończy się niepowodzeniem. W zależności od logiki Menedżera zadań można włączyć ponownych prób przy użyciu klienta *ograniczenia*/*maxTaskRetryCount*.

**Ustawienia zadania**

Jeśli rozdzielacz zadania emituje zadania z zależnościami, klient ustaw wartość true usesTaskDependencies zadania.

W modelu Splitter — zadanie jest niczym niezwykłym, klienci chcą Dodawanie podzadań do zadania podniesienia rozdzielacz zadanie tworzy. Klient w związku z tym należy zwykle ustawiana zadania *onAllTasksComplete* do **terminatejob**.

## <a name="task-processor-template"></a>Szablon procesor zadania
Szablon zadań procesora pomoże Ci zaimplementować procesor zadania, które można wykonać następujące czynności:

* Skonfiguruj informacje wymagane przez każde zadanie podrzędne usługi Batch do uruchomienia.
* Uruchom wszystkie akcje wymagane przez każde zadanie podrzędne usługi Batch.
* Zapisz dane wyjściowe zadania w magazynie trwałym.

Mimo że procesor zadania nie jest wymagany do uruchamiania zadań podrzędnych w usłudze Batch, zaletą przy użyciu procesora zadania jest zapewnia otoki, aby zaimplementować wszystkie akcje do wykonania zadania w jednej lokalizacji. Na przykład, jeśli musisz uruchomić kilka aplikacji w kontekście każdego zadania lub jeśli chcesz skopiować danych do magazynu trwałego po zakończeniu każdego zadania.

Akcje wykonywane przez procesor zadania może być proste lub złożone i jak najwięcej lub few, zgodnie z wymogami danego obciążenia. Ponadto poprzez implementację wszystkich akcji zadania w jednym procesorze zadanie, można łatwo zaktualizować lub dodać akcje na podstawie zmian do aplikacji lub wymagań dotyczących obciążenia. Jednak w niektórych przypadkach procesor zadanie może nie być optymalne rozwiązanie dla Twojego wdrożenia może zwiększyć złożoność niepotrzebne, na przykład podczas uruchamiania zadania, które można szybko uruchomić przy użyciu prostego wiersza polecenia.

### <a name="create-a-task-processor-using-the-template"></a>Utworzyć procesor zadania przy użyciu szablonu
Aby dodać procesor zadania do rozwiązania, który został utworzony wcześniej, wykonaj następujące kroki:

1. Otwórz istniejące rozwiązanie w programie Visual Studio.
2. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy rozwiązanie, kliknij przycisk **Dodaj**, a następnie kliknij przycisk **nowy projekt**.
3. W obszarze **Visual C#**, kliknij przycisk **chmury**, a następnie kliknij przycisk **procesor zadania usługi Batch Azure**.
4. Wpisz nazwę, opis aplikacji, która identyfikuje ten projekt jako procesor zadania (np. "LitwareTaskProcessor").
5. Aby utworzyć projekt, kliknij przycisk **OK**.
6. Na koniec Skompiluj projekt, aby wymusić programu Visual Studio można załadować wszystkich pakietów NuGet odwołania i sprawdź, czy projekt jest prawidłowa, przed rozpoczęciem modyfikowania go.

### <a name="task-processor-template-files-and-their-purpose"></a>Pliki szablonów procesora zadań i ich przeznaczenie
Podczas tworzenia projektu przy użyciu szablonu procesora zadanie generuje trzy grupy plików kodu:

* Plik główny program (Program.cs). Zawiera punktu wejścia programu, a obsługa wyjątków najwyższego poziomu. Zwykle nie należy modyfikować tego.
* Katalogu struktury. Zawiera pliki, które są odpowiedzialne za "standardowy" pracy w programie Menedżer zadania — rozpakowywania parametrów, dodawania zadania do zadania usługi Batch, itp. Zwykle nie należy modyfikować tych plików.
* Plik procesor zadania (TaskProcessor.cs). Jest to, gdzie należy umieścić logikę specyficzne dla aplikacji do wykonywania zadania (zazwyczaj wywołując do istniejącego pliku wykonywalnego). Kod wstępnej i przetwarzania końcowego, takie jak pobieranie dodatkowych danych lub przekazywanie plików wyników również miejsce.

Oczywiście możesz dodać dodatkowe pliki wymagane do obsługi zadań procesora kodu, w zależności od złożoności zadania dzielenie logiki.

Szablon generuje również standardowe pliki projektów .NET plik .csproj app.config, packages.config, np.

Pozostałej części tej sekcji opisano różne pliki i ich struktury kodu i wyjaśniono, jak działa każdej klasy.

![Visual Studio Eksplorator rozwiązań rozwiązanie szablonu zadań procesora][solution_explorer02]

**Pliki Framework**

* `Configuration.cs`: Hermetyzuje ładowanie danych konfiguracji zadania, takie jak szczegóły konta usługi Batch, poświadczenia konta połączonego magazynu, zadania i informacji o zadaniu i parametrów zadania. Umożliwia także dostęp do zmiennych środowiskowych zdefiniowanych przez usługi Batch (Zobacz ustawienia środowiska dla podzadań w dokumentacji usługi Batch) za pośrednictwem klasy Configuration.EnvironmentVariable.
* `IConfiguration.cs`: Przenosi implementacji klasy konfiguracji, aby można było test jednostkowy usługi rozdzielacz zadania za pomocą obiektu konfiguracji fałszywych lub makiety.
* `TaskProcessorException.cs`: Reprezentuje błąd, który wymaga Menedżer zadań zakończyć. TaskProcessorException jest używany do opakowywania błędy 'oczekiwany', gdzie można podać szczegółowe informacje diagnostyczne jako część rozwiązania.

**Procesor zadania**

* `TaskProcessor.cs`: Uruchamia zadanie. Struktura wywołuje metodę TaskProcessor.Run. Jest to klasa, gdzie należy wstrzyknąć logikę specyficzną dla aplikacji, które zadania. Zaimplementuj metodę wykonywania, aby:
  * Analizowanie i sprawdzanie poprawności parametrów zadania
  * Wiersz polecenia dla programu zewnętrznego, który chcesz wywołać redagowania
  * Rejestrować informacje diagnostyczne, które mogą wymagać dla celów debugowania
  * Uruchom proces, korzystając z tego wiersza polecenia
  * Zaczekaj na zakończenie procesu
  * Przechwyć kod zakończenia procesu, aby określić, czy zakończyła się powodzeniem lub nie powiodło się
  * Zapisz wszystkie pliki danych wyjściowych, które mają być przechowywane w magazynie trwałym

**Standardowe pliki projektu wiersza polecenia platformy .NET**

* `App.config`: Standardowa pliku konfiguracyjnego aplikacji .NET.
* `Packages.config`: Standardowy plik zależności pakietu NuGet.
* `Program.cs`: Zawiera punktu wejścia programu, a obsługa wyjątków najwyższego poziomu.

## <a name="implementing-the-task-processor"></a>Implementowanie procesora zadań
Po otwarciu projektu szablonu zadań procesora, projekt będzie miał plik TaskProcessor.cs domyślnie. Można zaimplementować logikę wykonywania zadań w obciążenia przy użyciu metody Run() pokazano poniżej:

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
> Sekcja adnotacjami metody Run() jest tylko część kodu szablonu zadań procesora, który jest przeznaczony dla Ciebie zmodyfikować przez dodanie logiki wykonywania zadań w obciążenia. Jeśli chcesz zmodyfikować innej części szablonu, należy najpierw zapoznać się z działaniem usługi Batch, zapoznaniu się z dokumentacją usługi Batch i wypróbowanie kilka przykładów kodu usługi Batch.
> 
> 

Metoda Run() jest odpowiedzialna za uruchamianie wiersza polecenia od jednego lub więcej procesów, oczekiwanie na ukończenie, wszystkie procesu zapisywania wyników, a na koniec zwraca kod zakończenia. Metoda Run() polega na tym, gdzie wdrożyć logikę przetwarzania dla zadań podrzędnych. Framework procesora zadanie wywołuje metodę Run() dla Ciebie; nie musisz wywołać ją samodzielnie.

Twoja implementacja Run() ma dostęp do:

* Parametry zadania za pośrednictwem `_parameters` pola.
* Identyfikatory zadań i zadań, za pośrednictwem `_jobId` i `_taskId` pola.
* Konfiguracja zadania za pośrednictwem `_configuration` pola.

**Niepowodzenie zadania**

W przypadku awarii wyjście z metody Run(), zgłaszając wyjątek, ale spowoduje to, że najwyższego poziomu obsługi wyjątków w formancie kod zakończenia zadania. Jeśli wymagane jest sterowanie kod zakończenia tak, aby odróżnić różnego rodzaju błąd, na przykład w celach diagnostycznych lub ponieważ niektóre trybów awarii należy zakończyć zadanie, a inne nie powinien, następnie należy zakończysz metoda Run(), zwracając różna od zera Kod zakończenia. Ten staje się kod zakończenia zadania.

### <a name="exit-codes-and-exceptions-in-the-task-processor-template"></a>Kody zakończenia i wyjątków w szablonie zadania procesora
Kody zakończenia i wyjątki zapewniają mechanizm do określenia wynik działania programu, a może pomóc zidentyfikować wszelkie problemy z wykonywaniem program. Szablon zadań procesora implementuje kody zakończenia i wyjątków opisanych w tej sekcji.

Zadania zadania procesora, który jest implementowany przy użyciu szablonu zadań procesora może zwrócić trzy kody zakończenia możliwe:

| Kod | Opis |
| --- | --- |
| [Process.ExitCode][process_exitcode] |Procesor zadanie zakończyło się. Należy zauważyć, że nie oznacza to, że program wywołano zakończyła się pomyślnie — tylko że procesora zadań pomyślnie wywołany i wykonać wszelkie przetwarzanie końcowe bez wyjątków. Znaczenie kod zakończenia jest zależna od wywołany program — zwykle kod zakończenia 0 oznacza, że program zakończyło się pomyślnie i inny kod zakończenia oznacza, że program nie powiodło się. |
| 1 |Procesor zadania nie powiodło się z powodu wyjątku w 'oczekiwany' części programu. Wyjątek został przetłumaczony na `TaskProcessorException` zawierających informacje diagnostyczne i, jeśli jest to możliwe, sugestie dotyczące rozwiązywania błędu. |
| 2 |Procesor zadania nie powiodło się z powodu wyjątku "Nieoczekiwany". Wyjątek został zalogowany do wyjścia standardowego, ale procesor zadań nie mógł dodać żadnych dodatkowych informacji diagnostycznych lub korygowania. |

> [!NOTE]
> Jeśli program, który można wywołać używa kody zakończenia 1 i 2, aby wskazać błąd tryby, wówczas używanie kody zakończenia 1 i 2 dla zadań procesora błędów jest niejednoznaczna. Możesz zmienić one zadania procesora, aby kody zakończenia charakterystyczne poprzez edycję przypadków wyjątek w pliku Program.cs.
> 
> 

Wszystkie informacje, które są zwracane przez wyjątki są zapisywane w plikach stdout.txt i stderr.txt. Aby uzyskać więcej informacji zobacz temat obsługę błędów, w dokumentacji usługi Batch.

### <a name="client-considerations"></a>Uwagi dotyczące klientów
**Poświadczenia magazynu**

Jeśli procesor zadanie używa usługi Azure blob storage do utrwalenia danych wyjściowych, na przykład za pomocą biblioteki pomocnika konwencje pliku, a następnie wymagany jest dostęp do *albo* poświadczeń konta magazynu w chmurze *lub* obiektu blob adres URL kontenera, który zawiera sygnatury dostępu współdzielonego (SAS). Szablon obsługuje dostarczanie poświadczeń za pośrednictwem wspólnych zmiennych środowiskowych. Klienta można przekazać poświadczenia magazynu w następujący sposób:

```csharp
job.CommonEnvironmentSettings = new [] {
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

Konto magazynu jest dostępna w klasie TaskProcessor za pośrednictwem `_configuration.StorageAccount` właściwości.

Jeśli wolisz używać adresu URL kontenera przy użyciu sygnatury dostępu Współdzielonego, można również przekazać to za pomocą ustawień środowiska typowe zadania, ale szablonu procesora zadanie nie zawiera wbudowaną obsługę to.

**Konfiguracja magazynu**

Zaleca się, że klienta lub zadanie podrzędne Menedżera zadań tworzyć wszystkie kontenery, wymagane przez zadania przed dodaniem zadania do zadania. Jest to konieczne, jeśli używasz adresu URL kontenera przy użyciu sygnatury dostępu Współdzielonego jako takie adresu URL nie ma uprawnień do utworzenia kontenera. Zaleca się nawet wtedy, gdy przekazywane poświadczenia konta magazynu, ponieważ zapisuje każde zadanie konieczności CloudBlobContainer.CreateIfNotExistsAsync w kontenerze.

## <a name="pass-parameters-and-environment-variables"></a>Przekaż parametry i zmienne środowiskowe
### <a name="pass-environment-settings"></a>Ustawienia środowiska — dostęp próbny
Klient może przekazywać informacje do zadanie podrzędne Menedżera zadań w formie ustawienia środowiska. Te informacje można następnie używane przez zadanie podrzędne Menedżera zadań, podczas generowania zadanie zadania procesora, które będą uruchamiane w ramach zadania obliczeniowe. Przykładami informacji, które można przekazać jako ustawienia środowiska są:

* Klucze konta magazynu nazwy i konta
* Adres URL konta usługi Batch
* Klucz konta usługi Batch

Usługa Batch ma prosty mechanizm do przekazania ustawienia środowiska do zadanie podrzędne Menedżera zadań przy użyciu `EnvironmentSettings` właściwość [Microsoft.Azure.Batch.JobManagerTask][net_jobmanagertask].

Na przykład, aby uzyskać `BatchClient` wystąpienia dla konta usługi Batch, można przekazać jako zmienne środowiskowe z klienta kodu adresu URL i poświadczeń klucza wspólnego dla konta usługi Batch. Podobnie Aby uzyskać dostęp do konta magazynu, który jest połączony z kontem usługi Batch, można przekazać nazwę konta magazynu i klucza konta magazynu jako zmienne środowiskowe.

### <a name="pass-parameters-to-the-job-manager-template"></a>Parametry są przekazywane do szablonu Menedżera zadań
W wielu przypadkach jest przydatne w celu przekazania parametrów dla zadania do zadania Menedżera zadań, do kontrolowania zadania rozdzielenie procesu lub aby skonfigurować zadania dla zadania. Można to zrobić, przekazując plik JSON o nazwie parameters.json jako plik zasobów dla zadania podrzędnego menedżera zadań. Parametry można następnie udostępniane w `JobSplitter._parameters` pola w szablonie Menedżera zadań.

> [!NOTE]
> Parametr wbudowany program obsługi wspiera tylko słowniki ciąg ciąg. Jeśli chcesz przekazać złożonych wartości JSON jako wartości parametrów, należy przekazać je jako ciągi i je przeanalizować w podziału pracy lub zmodyfikować struktury `Configuration.GetJobParameters` metody.
> 
> 

### <a name="pass-parameters-to-the-task-processor-template"></a>Parametry są przekazywane do szablonu zadania procesora
Parametry można też przekazać do poszczególnych zadań implementowane przy użyciu szablonu zadań procesora. Podobnie jak przy użyciu szablonu Menedżera zadań, zadań procesora szablon wyszukuje plik zasobów o nazwie

parameters.JSON, a jeśli go znaleziono ładuje go jako słownik parametrów. Istnieje kilka opcji dotyczących sposobu przekazania parametrów do zadań procesora zadań:

* Ponowne użycie parametrów zadania JSON. Ta funkcja działa dobrze w przypadku, gdy tylko parametry są pokazane zadań (na przykład renderowania wysokość i szerokość). Aby to zrobić, tworząc CloudTask w rozdzielacz zadania, należy dodać odwołanie do obiektu pliku parameters.json zasobów z ResourceFiles zadanie Menedżera (`JobSplitter._jobManagerTask.ResourceFiles`) CloudTask ResourceFiles kolekcji.
* Generowanie i Przekaż dokument parameters.json specyficzne dla zadania w ramach wykonywania podziału zadań i odwoływać się do tego obiektu blob w kolekcji pliki zasobów zadania podrzędnego. Jest to konieczne, jeśli różne zadania mają różne parametry. Przykładem może być scenariusz renderowania 3W, gdzie do indeksu ramki jest przekazywany do zadań jako parametr.

> [!NOTE]
> Parametr wbudowany program obsługi wspiera tylko słowniki ciąg ciąg. Jeśli chcesz przekazać złożonych wartości JSON jako wartości parametrów, należy przekazać je jako ciągi i analizować je w procesorze zadanie lub zmodyfikować struktury `Configuration.GetTaskParameters` metody.
> 
> 

## <a name="next-steps"></a>Kolejne kroki
### <a name="persist-job-and-task-output-to-azure-storage"></a>Utrwalanie danych wyjściowych zadań i zadań w usłudze Azure Storage
Inne przydatne narzędzie podczas tworzenia rozwiązań usługi Batch jest [Konwencji plików usługi Batch Azure][nuget_package]. Ta biblioteka klas programu .NET (obecnie w wersji zapoznawczej) w aplikacjach platformy .NET usługi Batch umożliwia łatwe przechowywanie i pobieranie danych wyjściowych zadania, do i z usługi Azure Storage. [Utrwalanie danych wyjściowych zadań i zadań usługi Azure Batch](batch-task-output.md) zawiera pełne omówienie biblioteki i sposób jej użycia.


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
