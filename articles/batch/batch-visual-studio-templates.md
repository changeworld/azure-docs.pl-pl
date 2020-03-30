---
title: Tworzenie rozwiązań za pomocą szablonów programu Visual Studio — usługa Azure Batch | Dokumenty firmy Microsoft
description: Dowiedz się, jak szablony projektów programu Visual Studio mogą pomóc w zaimplementowanie i uruchomienie obciążeń intensywnie korzystających z obliczeń w usłudze Azure Batch.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 5e041ae2-25af-4882-a79e-3aa63c4bfb20
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: a71dbd1b38ff58ccf1eb7a4d50daad5b24922e2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022753"
---
# <a name="use-visual-studio-project-templates-to-jump-start-batch-solutions"></a>Szybkie uruchamianie rozwiązań wsadowych za pomocą szablonów projektów programu Visual Studio

**Szablony Programu** Visual Studio Menedżera zadań i **procesor zadań** dla usługi Batch zawierają kod ułatwiający implementowanie i uruchamianie obciążeń intensywnie korzystających z obliczeń w usłudze Batch przy jak najmniejszym wysiłku. W tym dokumencie opisano te szablony i przedstawiono wskazówki dotyczące sposobu ich używania.

> [!IMPORTANT]
> W tym artykule omówiono tylko informacje mające zastosowanie do tych dwóch szablonów i założono, że użytkownik jest zaznajomiony z usługą wsadowej i kluczowymi pojęciami z nią związanymi: pule, węzły obliczeniowe, zadania i zadania, zadania menedżera zadań, zmienne środowiskowe i inne istotne Informacji. Więcej informacji można znaleźć w [omówienie](batch-technical-overview.md) funkcji Podstawowe usługi Azure Batch i [Batch dla deweloperów](batch-api-basics.md).
> 
> 

## <a name="high-level-overview"></a>Ogólne omówienie
Szablony Menedżera zadań i procesor zadań mogą być używane do tworzenia dwóch przydatnych składników:

* Zadanie menedżera zadań, które implementuje rozdzielacz zadań, który może podzielić zadanie na wiele zadań, które można uruchomić niezależnie, równolegle.
* Procesor zadań, który może służyć do przetwarzania wstępnego i przetwarzania końcowego wokół wiersza polecenia aplikacji.

Na przykład w scenariuszu renderowania filmu rozdzielacz zadań zamieniłby pojedyncze zadanie filmu w setki lub tysiące oddzielnych zadań, które przetwarzałyby poszczególne klatki oddzielnie. Odpowiednio procesor zadań wywoła aplikację renderowania i wszystkie procesy zależne, które są potrzebne do renderowania każdej ramki, a także wykonać dodatkowe akcje (na przykład kopiowanie renderowanego ramki do lokalizacji magazynu).

> [!NOTE]
> Szablony Menedżera zadań i procesor zadań są od siebie niezależne, więc można użyć obu lub tylko jednego z nich, w zależności od wymagań zadania obliczeniowego i preferencji.
> 
> 

Jak pokazano na poniższym diagramie, zadanie obliczeniowe, które używa tych szablonów, przejdzie przez trzy etapy:

1. Kod klienta (np. aplikacja, usługa sieci web itp.) przesyła zadanie do usługi Batch na platformie Azure, określając jako zadanie menedżera zadań program menedżera zadań.
2. Usługa Batch uruchamia zadanie menedżera zadań w węźle obliczeniowym, a rozdzielacz zadań uruchamia określoną liczbę zadań procesora zadań, na tyle węzłów obliczeniowych, ile jest wymagane, na podstawie parametrów i specyfikacji w kodzie rozdzielacza zadań.
3. Zadania procesora zadań są uruchamiane niezależnie, równolegle, aby przetwarzać dane wejściowe i generować dane wyjściowe.

![Diagram przedstawiający interakcję kodu klienta z usługą Batch][diagram01]

## <a name="prerequisites"></a>Wymagania wstępne
Aby korzystać z szablonów usługi Batch, potrzebne będą następujące elementy:

* Zainstalowany komputer z programem Visual Studio 2015. Szablony wsadowe są obecnie obsługiwane tylko dla programu Visual Studio 2015.
* Szablony usługi Batch, które są dostępne w [galerii programu Visual Studio][vs_gallery] jako rozszerzenia programu Visual Studio. Szablony można uzyskać na dwa sposoby:
  
  * Instalowanie szablonów przy użyciu okna dialogowego **Rozszerzenia i aktualizacje** w programie Visual Studio (aby uzyskać więcej informacji, zobacz [Znajdowanie i używanie rozszerzeń programu Visual Studio][vs_find_use_ext]). W oknie dialogowym **Rozszerzenia i aktualizacje** wyszukaj i pobierz następujące dwa rozszerzenia:
    
    * Menedżer zadań wsadowych platformy Azure z rozdzielaczem zadań
    * Procesor zadań wsadowych platformy Azure
  * Pobierz szablony z galerii online programu Visual Studio: [Szablony projektów wsadowych platformy Microsoft Azure][vs_gallery_templates]
* Jeśli planujesz użyć funkcji [Pakiety aplikacji](batch-application-packages.md) do wdrożenia menedżera zadań i procesora zadań w węzłach obliczeniowych batch, musisz połączyć konto magazynu z kontem usługi Batch.

## <a name="preparation"></a>Przygotowywanie
Zalecamy utworzenie rozwiązania, które może zawierać menedżera zadań, a także procesor zadań, ponieważ może to ułatwić udostępnianie kodu między menedżerem zadań a programami procesora zadań. Aby utworzyć to rozwiązanie, wykonaj następujące kroki:

1. Otwórz program Visual Studio i wybierz **pozycję Plik** > **nowego** > **projektu**.
2. W obszarze **Szablony**rozwiń węzeł **Inne typy projektów**kliknij pozycję **Rozwiązania programu Visual Studio**, a następnie wybierz pozycję Puste **rozwiązanie**.
3. Wpisz nazwę opisującą aplikację i cel tego rozwiązania (np.
4. Aby utworzyć nowe rozwiązanie, kliknij przycisk **OK**.

## <a name="job-manager-template"></a>Szablon Menedżera zadań
Szablon Menedżera zadań ułatwia zaimplementowanie zadania menedżera zadań, które może wykonywać następujące akcje:

* Podziel zadanie na wiele zadań.
* Prześlij te zadania do uruchomienia w uerce.

> [!NOTE]
> Aby uzyskać więcej informacji na temat zadań menedżera zadań, zobacz [Omówienie funkcji usługi Batch dla deweloperów](batch-api-basics.md#job-manager-task).
> 
> 

### <a name="create-a-job-manager-using-the-template"></a>Tworzenie Menedżera zadań przy użyciu szablonu
Aby dodać menedżera zadań do rozwiązania utworzonego wcześniej, wykonaj następujące kroki:

1. Otwórz istniejące rozwiązanie w programie Visual Studio.
2. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy rozwiązanie, kliknij polecenie **Dodaj** > **nowy projekt**.
3. W **obszarze Visual C#** kliknij pozycję **Chmura**, a następnie kliknij pozycję **Azure Batch Job Manager with Job Splitter**.
4. Wpisz nazwę opisującą aplikację i identyfikuje ten projekt jako menedżera zadań (np.
5. Aby utworzyć projekt, kliknij przycisk **OK**.
6. Na koniec skompiluj projekt, aby wymusić visual studio załadować wszystkie pakiety NuGet odwołuje i sprawdzić, czy projekt jest prawidłowy przed rozpoczęciem modyfikowania go.

### <a name="job-manager-template-files-and-their-purpose"></a>Pliki szablonów menedżera zadań i ich przeznaczenie
Podczas tworzenia projektu przy użyciu szablonu Menedżer zadań generuje trzy grupy plików kodu:

* Główny plik programu (Program.cs). Zawiera punkt wejścia programu i obsługę wyjątków najwyższego poziomu. Zwykle nie należy tego modyfikować.
* Katalog Framework. Zawiera pliki odpowiedzialne za pracę "standardową" wykonaną przez program menedżera zadań – rozpakowywanie parametrów, dodawanie zadań do zadania Batch itp. Zwykle nie należy modyfikować tych plików.
* Plik rozdzielacza zadań (JobSplitter.cs). W tym miejscu można umieścić logikę specyficzne dla aplikacji do dzielenia zadania na zadania.

Oczywiście można dodać dodatkowe pliki zgodnie z wymaganiami do obsługi kodu podziału zadań, w zależności od złożoności logiki podziału zadań.

Szablon generuje również standardowe pliki projektu .NET, takie jak plik csproj, app.config, packages.config itp.

W dalszej części tej sekcji opisano różne pliki i ich strukturę kodu i wyjaśniono, co robi każda klasa.

![Eksplorator rozwiązań programu Visual Studio przedstawiający rozwiązanie szablonu Menedżera zadań][solution_explorer01]

**Pliki ramowe**

* `Configuration.cs`: Hermetyzuje ładowanie danych konfiguracji zadania, takich jak szczegóły konta usługi Batch, poświadczenia połączonego konta magazynu, informacje o zadaniu i zadaniu oraz parametry zadania. Zapewnia również dostęp do zmiennych środowiskowych zdefiniowanych przez partię (zobacz ustawienia środowiska dla zadań w dokumentacji usługi Batch) za pośrednictwem klasy Configuration.EnvironmentVariable.
* `IConfiguration.cs`: Abstrakcje implementacji Configuration klasy, dzięki czemu można jednostki testowania podziału zadania przy użyciu fałszywego lub makiety obiektu konfiguracji.
* `JobManager.cs`: Aranżuje składniki programu menedżera zadań. Jest odpowiedzialny za inicjowanie rozdzielacza zadań, wywoływanie rozdzielacza zadań i wysyłanie zadań zwróconych przez rozdzielacz zadań do osoby przesyłającej zadania.
* `JobManagerException.cs`: Reprezentuje błąd, który wymaga zakończenia menedżera zadań. JobManagerException służy do zawijania błędów "oczekiwane", gdzie określone informacje diagnostyczne mogą być dostarczane w ramach zakończenia.
* `TaskSubmitter.cs`: Ta klasa jest odpowiedzialna za dodawanie zadań zwróconych przez rozdzielacz zadań do zadania Batch. JobManager Klasy agreguje sekwencję zadań w partiach dla wydajnego, ale terminowe dodanie do zadania, a następnie wywołuje TaskSubmitter.SubmitTasks w wątku w tle dla każdej partii.

**Rozdzielacz zadań**

`JobSplitter.cs`: Ta klasa zawiera logikę specyficzne dla aplikacji do dzielenia zadania na zadania. Struktura wywołuje JobSplitter.Split metody, aby uzyskać sekwencję zadań, które dodaje do zadania, jak metoda zwraca je. Jest to klasa, w której można wstrzyknąć logikę swojej pracy. Zaimplementuj metodę Podziału, aby zwrócić sekwencję wystąpień CloudTask reprezentujących zadania, do których chcesz podzielić zadanie.

**Standardowe pliki projektu wiersza polecenia .NET**

* `App.config`: Standardowy plik konfiguracyjny aplikacji .NET.
* `Packages.config`: Standardowy plik zależności pakietu NuGet.
* `Program.cs`: Zawiera punkt wejścia programu i obsługę wyjątków najwyższego poziomu.

### <a name="implementing-the-job-splitter"></a>Implementowanie rozdzielacza zadań
Po otwarciu projektu szablonu Menedżera zadań projekt będzie domyślnie otwierany JobSplitter.cs pliku. Logikę podziału dla zadań w obciążeniu można zaimplementować przy użyciu metody Split() poniżej:

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
> Sekcja z adnotacjami w metodzie jest jedyną `Split()` sekcją kodu szablonu Menedżera zadań, który jest przeznaczony do modyfikowania przez dodanie logiki, aby podzielić zadania na różne zadania. Jeśli chcesz zmodyfikować inną sekcję szablonu, upewnij się, że zapoznasz się z działaniem usługi Batch i wypróbuj kilka [przykładów kodu usługi Batch.][github_samples]
> 
> 

Implementacja Split() ma dostęp do:

* Parametry zadania, za `_parameters` pośrednictwem pola.
* CloudJob obiektu reprezentującego zadanie, `_job` za pośrednictwem pola.
* CloudTask obiektu reprezentującego zadanie menedżera zadań, za pośrednictwem `_jobManagerTask` pola.

Implementacja `Split()` nie musi bezpośrednio dodawać zadań do zadania. Zamiast tego kod powinien zwracać sekwencję CloudTask obiektów, a te zostaną dodane do zadania automatycznie przez klasy struktury, które wywołują rozdzielacz zadań. Często używa się funkcji iteratora języka`yield return`C#( ) do implementowania rozdzielaczy zadań, ponieważ umożliwia to uruchamianie zadań tak szybko, jak to możliwe, zamiast czekać na obliczenie wszystkich zadań.

**Niepowodzenie rozdzielacza zadań**

Jeśli rozdzielacz zadań napotka błąd, powinien on:

* Zakończ sekwencję przy użyciu `yield break` instrukcji C#, w którym to przypadku menedżer zadań będzie traktowany jako udany; Lub
* Zgłosić wyjątek, w którym to przypadku menedżer zadań będą traktowane jako nie powiodło się i może być ponowiony w zależności od tego, jak klient skonfigurował go).

W obu przypadkach wszystkie zadania już zwrócone przez rozdzielacz zadań i dodane do zadania Batch będą kwalifikowalne do uruchomienia. Jeśli nie chcesz, aby tak się stało, możesz:

* Zakończenie zadania przed powrotem z rozdzielacza zadań
* Sformułowanie całej kolekcji zadań przed zwróceniem go `ICollection<CloudTask>` `IList<CloudTask>` (oznacza to, że zwraca lub zamiast implementowania rozdzielacza zadań przy użyciu iteratora języka C#)
* Użyj zależności zadań, aby wszystkie zadania były zależne od pomyślnego ukończenia menedżera zadań

**Menedżer zadań ponownych prób**

Jeśli menedżer zadań nie powiedzie się, może zostać ponowiony przez usługę Batch w zależności od ustawień ponawiania próby klienta. Ogólnie rzecz biorąc jest to bezpieczne, ponieważ gdy struktura dodaje zadania do zadania, ignoruje wszystkie zadania, które już istnieją. Jednak jeśli obliczanie zadań jest kosztowne, możesz nie chcieć ponieść kosztu ponownego obliczania zadań, które zostały już dodane do zadania; z drugiej strony, jeśli ponowne uruchomienie nie jest gwarantowane do generowania tych samych identyfikatorów zadań, a następnie zachowanie "ignoruj duplikaty" nie będzie kopać w. W takich przypadkach należy zaprojektować rozdzielacz zadań, aby wykryć pracę, która została już wykonana i nie powtarzać go, na przykład wykonując CloudJob.ListTasks przed rozpoczęciem wykonywania zadań.

### <a name="exit-codes-and-exceptions-in-the-job-manager-template"></a>Kody zakończenia i wyjątki w szablonie Menedżer zadań
Kody zakończenia i wyjątki zapewniają mechanizm do określenia wyniku uruchomienia programu i mogą pomóc zidentyfikować wszelkie problemy z wykonywaniem programu. Szablon Menedżera zadań implementuje kody zakończenia i wyjątki opisane w tej sekcji.

Zadanie menedżera zadań zaimplementowane za pomocą szablonu Menedżer zadań może zwrócić trzy możliwe kody zakończenia:

| Code | Opis |
| --- | --- |
| 0 |Menedżer zadań zakończył pomyślnie. Kod podziału zadań został uruchomiony do ukończenia, a wszystkie zadania zostały dodane do zadania. |
| 1 |Zadanie menedżera zadań nie powiodło się z wyjątkiem w "oczekiwanej" części programu. Wyjątek został przetłumaczony na JobManagerException z informacjami diagnostycznymi i, jeśli to możliwe, sugestie dotyczące rozwiązywania błędu. |
| 2 |Zadanie menedżera zadań nie powiodło się z "nieoczekiwanym" wyjątkiem. Wyjątek został zarejestrowany w standardowych danych wyjściowych, ale menedżer zadań nie mógł dodać żadnych dodatkowych informacji diagnostycznych lub korygowania. |

W przypadku niepowodzenia zadania menedżera zadań niektóre zadania mogły zostać dodane do usługi przed wystąpieniem błędu. Te zadania będą działać normalnie. Zobacz "Błąd podziału zadań" powyżej do dyskusji na temat tej ścieżki kodu.

Wszystkie informacje zwracane przez wyjątki są zapisywane w plikach stdout.txt i stderr.txt. Aby uzyskać więcej informacji, zobacz [Obsługa błędów](batch-api-basics.md#error-handling).

### <a name="client-considerations"></a>Uwagi klientów
W tej sekcji opisano niektóre wymagania dotyczące implementacji klienta podczas wywoływania menedżera zadań na podstawie tego szablonu. Zobacz [Jak przekazać parametry i zmienne środowiskowe z kodu klienta, aby](#pass-environment-settings) uzyskać szczegółowe informacje na temat przekazywania parametrów i ustawień środowiska.

**Poświadczenia obowiązkowe**

Aby dodać zadania do zadania usługi Azure Batch, zadanie menedżera zadań wymaga adresu URL konta usługi Azure Batch i klucza. Należy przekazać je w zmiennych środowiskowych o nazwie YOUR_BATCH_URL i YOUR_BATCH_KEY. Można je ustawić w ustawieniach środowiska zadań Menedżera zadań. Na przykład w kliencie języka C#:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    new EnvironmentSetting("YOUR_BATCH_URL", "https://account.region.batch.azure.com"),
    new EnvironmentSetting("YOUR_BATCH_KEY", "{your_base64_encoded_account_key}"),
};
```
**Poświadczenia magazynu**

Zazwyczaj klient nie musi podawać poświadczeń połączonego konta magazynu do zadania menedżera zadań, ponieważ (a) większość menedżerów zadań nie musi jawnie uzyskiwać dostępu do połączonego konta magazynu, a (b) połączone konto magazynu jest często dostarczane do wszystkich zadań jako wspólnego ustawienia środowiska dla zadania. Jeśli nie podajesz połączonego konta magazynu za pośrednictwem typowych ustawień środowiska, a menedżer zadań wymaga dostępu do połączonego magazynu, należy podać połączone poświadczenia magazynu w następujący sposób:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    /* other environment settings */
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

**Ustawienia zadań menedżera zadań**

Klient powinien ustawić *killJobOnCompletion* flagę menedżera zadań na **false**.

Zazwyczaj jest to bezpieczne dla klienta, aby ustawić *runExclusive* do **false**.

Klient powinien używać *resourceFiles* lub *applicationPackageReferences* kolekcji, aby plik wykonywalny menedżera zadań (i jego wymagane biblioteki DLL) wdrożony w węźle obliczeniowym.

Domyślnie menedżer zadań nie zostanie ponowiony, jeśli zakończy się niepowodzeniem. W zależności od logiki menedżera zadań klient może chcieć włączyć ponownych prób za pomocą *ograniczeń*/*maxTaskRetryCount*.

**Ustawienia zadania**

Jeśli rozdzielacz zadań emituje zadania z zależnościami, klient musi ustawić użycie zadaniaZaważania na wartość true.

W modelu podziału zadań jest nietypowe dla klientów, aby chcieć dodać zadania do zadań ponad to, co tworzy rozdzielacz zadań. W związku z tym klient powinien normalnie ustawić zadanie *onAllTasksComplete* **do terminatejob**.

## <a name="task-processor-template"></a>Szablon procesora zadań
Szablon procesora zadań ułatwia zaimplementowanie procesora zadań, który może wykonywać następujące czynności:

* Skonfiguruj informacje wymagane przez każde zadanie usługi Batch do uruchomienia.
* Uruchom wszystkie akcje wymagane przez każde zadanie wsadowe.
* Zapisz dane wyjściowe zadań w magazynie trwałym.

Chociaż procesor zadań nie jest wymagany do uruchamiania zadań w uczynku, kluczową zaletą korzystania z procesora zadań jest to, że udostępnia otokę do zaimplementowania wszystkich akcji wykonywania zadań w jednej lokalizacji. Na przykład jeśli trzeba uruchomić kilka aplikacji w kontekście każdego zadania lub jeśli trzeba skopiować dane do magazynu trwałego po wykonaniu każdego zadania.

Akcje wykonywane przez procesor zadań mogą być tak proste lub złożone, a nawet tyle lub nieliczne, zgodnie z wymaganiami obciążenia. Ponadto implementując wszystkie akcje zadań w jednym procesorze zadań, można łatwo aktualizować lub dodawać akcje na podstawie zmian w aplikacjach lub wymaganiach dotyczących obciążenia. Jednak w niektórych przypadkach procesor zadań może nie być optymalnym rozwiązaniem dla implementacji, ponieważ może dodać niepotrzebną złożoność, na przykład podczas uruchamiania zadań, które można szybko uruchomić z prostego wiersza polecenia.

### <a name="create-a-task-processor-using-the-template"></a>Tworzenie procesora zadań przy użyciu szablonu
Aby dodać procesor zadań do utworzonego wcześniej rozwiązania, wykonaj następujące kroki:

1. Otwórz istniejące rozwiązanie w programie Visual Studio.
2. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy rozwiązanie, kliknij polecenie **Dodaj**, a następnie kliknij polecenie **Nowy projekt**.
3. W obszarze **Visual C#** kliknij pozycję **Chmura**, a następnie kliknij pozycję **Procesor zadań wsadowych platformy Azure**.
4. Wpisz nazwę opisującą aplikację i identyfikuje ten projekt jako procesor zadań (np.
5. Aby utworzyć projekt, kliknij przycisk **OK**.
6. Na koniec skompiluj projekt, aby wymusić visual studio załadować wszystkie pakiety NuGet odwołuje i sprawdzić, czy projekt jest prawidłowy przed rozpoczęciem modyfikowania go.

### <a name="task-processor-template-files-and-their-purpose"></a>Pliki szablonów procesora zadań i ich przeznaczenie
Podczas tworzenia projektu przy użyciu szablonu procesora zadań generuje trzy grupy plików kodu:

* Główny plik programu (Program.cs). Zawiera punkt wejścia programu i obsługę wyjątków najwyższego poziomu. Zwykle nie należy tego modyfikować.
* Katalog Framework. Zawiera pliki odpowiedzialne za pracę "standardową" wykonaną przez program menedżera zadań – rozpakowywanie parametrów, dodawanie zadań do zadania Batch itp. Zwykle nie należy modyfikować tych plików.
* Plik procesora zadań (TaskProcessor.cs). W tym miejscu można umieścić logikę specyficzne dla aplikacji do wykonywania zadania (zazwyczaj przez wywołanie istniejącego pliku wykonywalnego). Kod przed i po processingu, taki jak pobieranie dodatkowych danych lub przesyłanie plików wyników, również idzie tutaj.

Oczywiście można dodać dodatkowe pliki zgodnie z wymaganiami do obsługi kodu procesora zadań, w zależności od złożoności logiki podziału zadań.

Szablon generuje również standardowe pliki projektu .NET, takie jak plik csproj, app.config, packages.config itp.

W dalszej części tej sekcji opisano różne pliki i ich strukturę kodu i wyjaśniono, co robi każda klasa.

![Eksplorator rozwiązań programu Visual Studio przedstawiający rozwiązanie szablonu procesora zadań][solution_explorer02]

**Pliki ramowe**

* `Configuration.cs`: Hermetyzuje ładowanie danych konfiguracji zadania, takich jak szczegóły konta usługi Batch, poświadczenia połączonego konta magazynu, informacje o zadaniu i zadaniu oraz parametry zadania. Zapewnia również dostęp do zmiennych środowiskowych zdefiniowanych przez partię (zobacz ustawienia środowiska dla zadań w dokumentacji usługi Batch) za pośrednictwem klasy Configuration.EnvironmentVariable.
* `IConfiguration.cs`: Abstrakcje implementacji Configuration klasy, dzięki czemu można jednostki testowania podziału zadania przy użyciu fałszywego lub makiety obiektu konfiguracji.
* `TaskProcessorException.cs`: Reprezentuje błąd, który wymaga zakończenia menedżera zadań. TaskProcessorException służy do zawijania błędów "oczekiwane", gdzie określone informacje diagnostyczne mogą być dostarczane w ramach zakończenia.

**Procesor zadań**

* `TaskProcessor.cs`: Uruchamia zadanie. Struktura wywołuje TaskProcessor.Run metody. Jest to klasa, w której można wstrzyknąć logiki specyficzne dla aplikacji zadania. Zaimplementuj run metody do:
  * Analizuj i sprawdzaj poprawność parametrów zadania
  * Skomponowanie wiersza polecenia dla dowolnego programu zewnętrznego, który ma zostać wywołany
  * Rejestrowanie wszelkich informacji diagnostycznych, które mogą być wymagane do celów debugowania
  * Rozpoczynanie procesu przy użyciu tego wiersza polecenia
  * Poczekaj na zakończenie procesu
  * Przechwyć kod zakończenia procesu, aby ustalić, czy zakończył się pomyślnie, czy nie
  * Zapisywanie wszystkich plików wyjściowych, które chcesz zachować w pamięci trwałej

**Standardowe pliki projektu wiersza polecenia .NET**

* `App.config`: Standardowy plik konfiguracyjny aplikacji .NET.
* `Packages.config`: Standardowy plik zależności pakietu NuGet.
* `Program.cs`: Zawiera punkt wejścia programu i obsługę wyjątków najwyższego poziomu.

## <a name="implementing-the-task-processor"></a>Implementowanie procesora zadań
Po otwarciu projektu szablonu procesora zadań projekt będzie domyślnie otwierany TaskProcessor.cs pliku. Logikę uruchamiania zadań w obciążeniu można zaimplementować przy użyciu metody Run() przedstawionej poniżej:

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
> Sekcja z adnotacjami w metodzie Run() jest jedyną sekcją kodu szablonu procesora zadań, który jest przeznaczony do modyfikowania przez dodanie logiki uruchamiania dla zadań w obciążeniu. Jeśli chcesz zmodyfikować inną sekcję szablonu, najpierw zapoznaj się z tym, jak działa usługa Batch, przeglądając dokumentację usługi Batch i wypróbując kilka przykładów kodu usługi Batch.
> 
> 

Run() Metoda jest odpowiedzialny za uruchomienie wiersza polecenia, uruchomienie jednego lub więcej procesów, oczekiwanie na zakończenie całego procesu, zapisywanie wyników i na koniec powrót z kodem zakończenia. Run() Metoda jest, gdzie można zaimplementować logikę przetwarzania dla zadań. Struktura procesora zadań wywołuje metodę Run() dla Ciebie; nie musisz nazywać tego sam.

Implementacja Run() ma dostęp do:

* Parametry zadania za `_parameters` pośrednictwem pola.
* Zadania i identyfikatory zadań, `_jobId` za `_taskId` pośrednictwem pól i.
* Konfiguracja zadania za `_configuration` pośrednictwem pola.

**Błąd zadania**

W przypadku awarii można zamknąć Run() metody, zgłaszając wyjątek, ale pozostawia to najwyższego poziomu obsługi wyjątków w kontroli kodu zakończenia zadania. Jeśli musisz kontrolować kod zakończenia, aby można było odróżnić różne typy błędów, na przykład do celów diagnostycznych lub ponieważ niektóre tryby awarii powinny zakończyć zadanie, a inne nie, należy zakończyć metodę Run(), zwracając metodę inną niż zero kod zakończenia. Staje się to kod zakończenia zadania.

### <a name="exit-codes-and-exceptions-in-the-task-processor-template"></a>Kody zakończenia i wyjątki w szablonie Procesor zadań
Kody zakończenia i wyjątki zapewniają mechanizm do określenia wyniku uruchomienia programu i mogą pomóc zidentyfikować wszelkie problemy z wykonaniem programu. Szablon Procesor zadań implementuje kody zakończenia i wyjątki opisane w tej sekcji.

Zadanie procesora zadań zaimplementowane za pomocą szablonu Procesor zadań może zwrócić trzy możliwe kody zakończenia:

| Code | Opis |
| --- | --- |
| [Process.ExitCode][process_exitcode] |Procesor zadań został ukończony. Należy zauważyć, że nie oznacza to, że wywoływany program zakończył się pomyślnie — tylko, że procesor zadań wywołał go pomyślnie i wykonał wszelkie post-processing bez wyjątków. Znaczenie kodu zakończenia zależy od wywoływanego programu — zazwyczaj kod zakończenia 0 oznacza, że program zakończył się pomyślnie, a każdy inny kod zakończenia oznacza, że program nie powiódł się. |
| 1 |Procesor zadań nie powiódł się z wyjątkiem w "oczekiwanej" części programu. Wyjątek został przetłumaczony `TaskProcessorException` na z informacjami diagnostycznymi i, w miarę możliwości, sugestie dotyczące rozwiązania błędu. |
| 2 |Procesor zadań nie powiódł się z "nieoczekiwanym" wyjątkiem. Wyjątek został zarejestrowany w standardowym wyjściu, ale procesor zadań nie mógł dodać żadnych dodatkowych informacji diagnostycznych lub korygowania. |

> [!NOTE]
> Jeśli wywoływany program używa kodów zakończenia 1 i 2 do wskazania określonych trybów awarii, użycie kodów zakończenia 1 i 2 dla błędów procesora zadań jest niejednoznaczne. Te kody błędów procesora zadań można zmienić na charakterystyczne kody zakończenia, edytując przypadki wyjątków w pliku Program.cs.
> 
> 

Wszystkie informacje zwracane przez wyjątki są zapisywane w plikach stdout.txt i stderr.txt. Aby uzyskać więcej informacji, zobacz obsługa błędów w dokumentacji usługi Batch.

### <a name="client-considerations"></a>Uwagi klientów
**Poświadczenia magazynu**

Jeśli procesor zadań używa magazynu obiektów blob platformy Azure do utrwalania danych wyjściowych, na przykład przy użyciu biblioteki pomocnika konwencji plików, następnie wymaga dostępu do poświadczeń konta magazynu w *chmurze* *lub* adresu URL kontenera obiektu blob, który zawiera sygnaturę dostępu współdzielonego (SAS). Szablon zawiera obsługę dostarczania poświadczeń za pośrednictwem wspólnych zmiennych środowiskowych. Klient może przekazać poświadczenia magazynu w następujący sposób:

```csharp
job.CommonEnvironmentSettings = new [] {
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

Konto magazynu jest następnie dostępne w Klasie TaskProcessor za pośrednictwem `_configuration.StorageAccount` właściwości.

Jeśli wolisz używać adresu URL kontenera z sygnaturą sygnatury dostępu Współdzielonego, możesz również przekazać to za pomocą wspólnego ustawienia środowiska zadania, ale szablon procesora zadań nie zawiera obecnie wbudowanej obsługi tej funkcji.

**Konfiguracja pamięci masowej**

Zaleca się, aby zadanie klienta lub menedżera zadań utworzyło wszystkie kontenery wymagane przez zadania przed dodaniem zadań do zadania. Jest to obowiązkowe, jeśli używasz adresu URL kontenera z sygnaturą współdzieloną, ponieważ taki adres URL nie zawiera uprawnień do tworzenia kontenera. Zalecane jest, nawet jeśli przekazać poświadczenia konta magazynu, ponieważ zapisuje każde zadanie konieczności wywołania CloudBlobContainer.CreateIfNotExistsAsync w kontenerze.

## <a name="pass-parameters-and-environment-variables"></a>Parametry przebiegu i zmienne środowiskowe
### <a name="pass-environment-settings"></a>Przekaż ustawienia środowiska
Klient może przekazywać informacje do zadania menedżera zadań w postaci ustawień środowiska. Te informacje mogą być następnie używane przez zadanie menedżera zadań podczas generowania zadań procesora zadań, które będą uruchamiane jako część zadania obliczeniowego. Przykłady informacji, które można przekazać jako ustawienia środowiska są:

* Nazwa konta magazynu i klucze konta
* Adres URL konta wsadowego
* Klucz konta wsadowego

Usługa Batch ma prosty mechanizm przekazywania ustawień środowiska do `EnvironmentSettings` zadania menedżera zadań przy użyciu właściwości w [witrynie Microsoft.Azure.Batch.JobManagerTask][net_jobmanagertask].

Na przykład, aby `BatchClient` uzyskać wystąpienie dla konta usługi Batch, można przekazać jako zmienne środowiskowe z kodu klienta adres URL i poświadczenia klucza udostępnionego dla konta usługi Batch. Podobnie, aby uzyskać dostęp do konta magazynu, który jest połączony z kontem usługi Batch, można przekazać nazwę konta magazynu i klucz konta magazynu jako zmienne środowiskowe.

### <a name="pass-parameters-to-the-job-manager-template"></a>Przekazywanie parametrów do szablonu Menedżera zadań
W wielu przypadkach warto przekazać parametry na zadanie do zadania menedżera zadań, aby kontrolować proces dzielenia zadań lub skonfigurować zadania dla zadania. Można to zrobić, przekazując plik JSON o nazwie parameters.json jako plik zasobów dla zadania menedżera zadań. Parametry mogą być następnie `JobSplitter._parameters` dostępne w polu w szablonie Menedżer zadań.

> [!NOTE]
> Wbudowany program obsługi parametrów obsługuje tylko słowniki ciąg-ciąg. Jeśli chcesz przekazać złożone wartości JSON jako wartości parametrów, należy przekazać je jako ciągi i przeanalizować je `Configuration.GetJobParameters` w rozdzielaczu zadań lub zmodyfikować metodę struktury.
> 
> 

### <a name="pass-parameters-to-the-task-processor-template"></a>Przekazywanie parametrów do szablonu procesora zadań
Parametry można również przekazywać do poszczególnych zadań zaimplementowanych przy użyciu szablonu Procesor zadań. Podobnie jak w przypadku szablonu menedżera zadań, szablon procesora zadań wyszukuje plik zasobu o nazwie

parameters.json, a jeśli się okaże, ładuje go jako słownik parametrów. Istnieje kilka opcji przekazywania parametrów do zadań procesora zadań:

* Ponowne użycie parametrów zadania JSON. Działa to dobrze, jeśli tylko parametry są te dla całej pracy (na przykład wysokość renderowania i szerokość). Aby to zrobić, podczas tworzenia CloudTask w rozdzielaczu zadań, należy dodać odwołanie do obiektu pliku zasobów parameters.json z zadania Menedżera zadań ResourceFiles (`JobSplitter._jobManagerTask.ResourceFiles`) do kolekcji ResourceFiles cloudtask.
* Generowanie i przekazywanie dokumentu specyficznego dla zadania.json jako część wykonywania rozdzielacza zadań i odwoływanie się do tego obiektu blob w kolekcji plików zasobów zadania. Jest to konieczne, jeśli różne zadania mają różne parametry. Przykładem może być scenariusz renderowania 3D, w którym indeks ramki jest przekazywany do zadania jako parametr.

> [!NOTE]
> Wbudowany program obsługi parametrów obsługuje tylko słowniki ciąg-ciąg. Jeśli chcesz przekazać złożone wartości JSON jako wartości parametrów, należy przekazać je jako ciągi i przeanalizować je `Configuration.GetTaskParameters` w procesorze zadań lub zmodyfikować metodę struktury.
> 
> 

## <a name="next-steps"></a>Następne kroki
### <a name="persist-job-and-task-output-to-azure-storage"></a>Utrwalanie danych wyjściowych zadania i zadania w usłudze Azure Storage
Innym pomocnym narzędziem w opracowywaniu rozwiązania wsadowego jest [konwencje plików wsadowych platformy Azure.][nuget_package] Użyj tej biblioteki klas .NET (obecnie w wersji zapoznawczej) w aplikacjach usługi Batch .NET, aby łatwo przechowywać i pobierać dane wyjściowe zadań do i z usługi Azure Storage. [Persist Azure Batch zadanie i dane wyjściowe zadania](batch-task-output.md) zawiera pełną dyskusję na temat biblioteki i jej użycia.


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
