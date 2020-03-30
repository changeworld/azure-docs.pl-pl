---
title: Dostosowywanie zadań analizy kodu zabezpieczeń firmy Microsoft
titleSuffix: Azure
description: W tym artykule opisano dostosowywanie zadań w rozszerzeniu Analizy kodu zabezpieczeń firmy Microsoft
author: vharindra
manager: sukhans
ms.author: terrylan
ms.date: 07/31/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 6cdf892651407defc21f359a8e3b326b4af63b62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77499988"
---
# <a name="configure-and-customize-the-build-tasks"></a>Konfigurowanie i dostosowywanie zadań kompilacji

W tym artykule opisano szczegółowo opcje konfiguracji dostępne w każdym z zadań kompilacji. Artykuł rozpoczyna się od zadań dla narzędzi do analizy kodu zabezpieczeń. Kończy się zadaniami przetwarzania końcowego.

## <a name="anti-malware-scanner-task"></a>Zadanie skanera zapobiega złośliwego oprogramowania

>[!NOTE]
> Zadanie kompilacji skanera przed złośliwym oprogramowaniem wymaga agenta kompilacji z włączoną funkcją Usługi Windows Defender. Hostowane visual studio 2017 i później zapewnić takiego agenta. Zadanie kompilacji nie zostanie uruchomione w programie Visual Studio 2015 hostowanego agenta.
>
> Mimo że podpisów nie można zaktualizować na tych agentów, podpisy powinny być zawsze mniej niż trzy godziny.

Szczegóły konfiguracji zadania są wyświetlane na poniższym zrzucie ekranu i tekście.

![Konfigurowanie zadania kompilacji skanera przed złośliwym oprogramowaniem](./media/security-tools/5-add-anti-malware-task600.png)

W polu listy **Typ** zrzutu ekranu jest zaznaczona opcja **Podstawowe.** Wybierz **opcję Niestandardowe,** aby podać argumenty wiersza polecenia, które dostosowują skanowanie.

Usługa Windows Defender używa klienta usługi Windows Update do pobierania i instalowania podpisów. Jeśli aktualizacja podpisu nie powiedzie się w agencie kompilacji, kod błędu **HRESULT** prawdopodobnie pochodzi z witryny Windows Update.

Aby uzyskać więcej informacji na temat błędów usługi Windows Update i ich łagodzenia, zobacz [Kody błędów usługi Windows Update według składnika](https://docs.microsoft.com/windows/deployment/update/windows-update-error-reference) i artykuł TechNet [Agent windows update — Kody błędów](https://social.technet.microsoft.com/wiki/contents/articles/15260.windows-update-agent-error-codes.aspx).

Aby uzyskać informacje na temat konfiguracji YAML dla tego zadania, sprawdź nasze [opcje YAML anti-malware](yaml-configuration.md#anti-malware-scanner-task)

## <a name="binskim-task"></a>Zadanie BinSkim

> [!NOTE]
> Przed uruchomieniem zadania BinSkim kompilacja musi spełniać jeden z następujących warunków:
>  - Kompilacja produkuje artefakty binarne z kodu zarządzanego.
>  - Masz artefakty binarne zatwierdzone, które chcesz analizować za pomocą BinSkim.

Szczegóły konfiguracji zadania są wyświetlane na poniższym zrzucie ekranu i na liście.

![Konfigurowanie zadania kompilacji BinSkim](./media/security-tools/7-binskim-task-details.png)

- Ustaw konfigurację kompilacji na Debugowanie, tak aby pliki debugowania pdb były produkowane. BinSkim używa tych plików do mapowania problemów w plikach binarnych danych z powrotem do kodu źródłowego.
- Aby uniknąć badania i tworzenia własnego wiersza polecenia:
     - Na liście **Typ** wybierz pozycję **Podstawowe**.
     - Na liście **Funkcja** wybierz pozycję **Analizuj**.
- W **polu Docelowy**wprowadź co najmniej jeden specyfikator pliku, katalogu lub wzorca filtru. Te specyfikatory rozpoznają jeden lub więcej plików binarnych do analizy:
    - Wiele określonych obiektów docelowych musi być oddzielonych średnikiem (;).
    - Specyfikator może być pojedynczym plikiem lub zawierać symbole wieloznaczne.
    - Specyfikacje katalogów muszą \\zawsze kończyć się na *.
    - Przykłady:

           *.dll;*.exe
           $(BUILD_STAGINGDIRECTORY)\*
           $(BUILD_STAGINGDIRECTORY)\*.dll;$(BUILD_STAGINGDIRECTORY)\*.exe;

- Jeśli na liście **Typ** zostanie wybrana **wartość Wiersz polecenia,** należy uruchomić plik binskim.exe:
     - Upewnij się, że pierwsze argumenty binskim.exe są **analizą czasownika,** po której następuje co najmniej jedna specyfikacja ścieżki. Każda ścieżka może być pełną ścieżką lub ścieżką względem katalogu źródłowego.
     - Wiele ścieżek docelowych musi być oddzielonych spacją.
     - Można pominąć **/o** lub **/output** opcji. Wartość wyjściowa jest dodawana dla Ciebie lub zastąpiona.
     - Standardowe konfiguracje wiersza polecenia są wyświetlane w następujący sposób.

           analyze $(Build.StagingDirectory)\* --recurse --verbose
           analyze *.dll *.exe --recurse --verbose

          > [!NOTE]
          > Końcowe \\* jest ważne, jeśli określisz katalogi dla obiektu docelowego.

Aby uzyskać więcej informacji na temat argumentów wiersza polecenia BinSkim, reguł według identyfikatora lub kodów zakończenia, zobacz [Podręcznik użytkownika BinSkim](https://github.com/Microsoft/binskim/blob/master/docs/UserGuide.md).

Aby uzyskać informacje o konfiguracji YAML dla tego zadania, sprawdź nasze [opcje BinSkim YAML](yaml-configuration.md#binskim-task)

## <a name="credential-scanner-task"></a>Zadanie Skaner poświadczeń

Szczegóły konfiguracji zadania są wyświetlane na poniższym zrzucie ekranu i na liście.

![Konfigurowanie zadania kompilacji skanera poświadczeń](./media/security-tools/3-taskdetails.png)

Dostępne opcje to:

  - **Format wyjścia**: Dostępne wartości to **TSV**, **CSV**, **SARIF**i **PREfast**.
  - **Wersja narzędzia**: Zalecamy **wybranie**opcji Najnowsze .
  - **Folder skanowania**: Folder repozytorium do skanowania.
  - **Typ pliku wyszukiwarki:** Opcje lokalizowania pliku wyszukiwania, który jest używany do skanowania.
  - **Plik tłumienia:** Plik [JSON](https://json.org/) może pomijać problemy w dzienniku danych wyjściowych. Aby uzyskać więcej informacji na temat scenariuszy pomijania, zobacz sekcję często zadawane pytania w tym artykule.
  - **Pełne wyjście**: Oczywiste.
  - **Rozmiar partii:** Liczba równoczesnych wątków używanych do uruchamiania skanera poświadczeń. Wartość domyślna to 20. Możliwe wartości wahają się od 1 do 2 147 483 647.
  - **Limit czasu dopasowania:** czas w sekundach, aby poświęcić na próbę dopasowania wyszukiwarki przed porzuceniem czeku.
  - **Rozmiar buforu odczytu skanowania plików:** rozmiar w bajtach buforu używanego podczas odczytu zawartości. Wartość domyślna to 524 288.  
  - **Maksymalna liczba bajtów odczytu skanowania plików:** Maksymalna liczba bajtów odczytywanych z pliku podczas analizy zawartości. Wartość domyślna to 104 857 600.
  - **Opcje sterowania** > **Uruchom to zadanie:** Określa, kiedy zadanie zostanie uruchomione. Wybierz **niestandardowe warunki,** aby określić bardziej złożone warunki.
  - **Wersja:** Wersja zadania kompilacji w ramach usługi Azure DevOps. Ta opcja nie jest często używana.

Aby uzyskać informacje o konfiguracji YAML dla tego zadania, sprawdź nasze [opcje YAML skanera poświadczeń](yaml-configuration.md#credential-scanner-task)

## <a name="microsoft-security-risk-detection-task"></a>Zadanie wykrywania zagrożeń bezpieczeństwa firmy Microsoft

> [!NOTE]
> Przed użyciem zadania MSRD należy utworzyć i skonfigurować konto za pomocą usługi Microsoft Security Risk Detection (MSRD). Ta usługa wymaga oddzielnego procesu dołączania. W przeciwieństwie do większości innych zadań w tym rozszerzeniu, to zadanie wymaga oddzielnej subskrypcji z MSRD.
>
> Instrukcje można znaleźć w [witrynie Microsoft Security Risk Detection](https://aka.ms/msrddocs) and Microsoft Security Risk [Detection: How To.](https://docs.microsoft.com/security-risk-detection/how-to/)

Szczegóły dotyczące konfigurowania tego zadania są wyświetlane na poniższej liście. Dla każdego elementu interfejsu użytkownika można najechać kursorem na ten element, aby uzyskać pomoc.

   - **Nazwa punktu końcowego usługi Azure DevOps dla msrd:** ogólny typ punktu końcowego usługi Azure DevOps przechowuje wbudowany adres URL wystąpienia MSRD i token dostępu interfejsu API REST. Jeśli utworzono taki punkt końcowy, można określić go w tym miejscu. W przeciwnym razie wybierz **łącze Zarządzaj,** aby utworzyć i skonfigurować nowy punkt końcowy usługi dla tego zadania MSRD.
   - **Identyfikator konta:** identyfikator GUID, który można pobrać z adresu URL konta MSRD.
   - **Adresy URL do plików binarnych:** lista publicznie dostępnych adresów URL rozdzielonych średnikami. Fuzzing machine używa tych adresów URL do pobrania plików binarnych.
   - **Adresy URL plików źródłowych:** lista publicznie dostępnych adresów URL rozdzielonych średnikami. Fuzzing machine używa tych adresów URL, aby pobrać nasiona. Określenie tej wartości jest opcjonalne, jeśli pliki źródłowe są pobierane razem z plikami binarnymi.
   - **Typ platformy systemu operacyjnego:** Platforma systemu operacyjnego (OS) maszyn, które uruchamiają zadanie rozmycie. Dostępne wartości to **Windows** i **Linux**.
   - **Windows Edition / Linux Edition**: Edycja systemu operacyjnego maszyn, które działają rozmycie pracy. Wartość domyślną można zastąpić, jeśli na komputerach jest inna wersja systemu operacyjnego.
   - **Skrypt instalacji pakietu:** Skrypt do uruchomienia na komputerze testowym. Ten skrypt instaluje testowy program docelowy i jego zależności przed przesłaniem zadania rozmytego.
   - **Parametry składania zadań**:
       - **Katalog źródłowy:** Ścieżka do katalogu na fuzzing maszyny, która zawiera nasiona.
       - **Rozszerzenie nasion**: Rozszerzenie nazwy pliku nasion.
       - **Plik wykonywalny sterownika testowego:** Ścieżka do docelowego pliku wykonywalnego na fuzzing machine.
       - **Architektura wykonywalna sterownika testowego**: architektura docelowego pliku wykonywalnego. Dostępne wartości to **x86** i **amd64**.
       - **Argumenty sterownika testu:** Argumenty wiersza polecenia przekazywane do testowego pliku wykonywalnego. Argument "%testfile",łącznie ze znakami notowania, jest automatycznie zastępowany pełną ścieżką do pliku docelowego. Ten plik jest analizowany przez sterownik testowy i jest wymagany.
       - **Proces sterownika testowego kończy się po zakończeniu testu:** Zaznacz to pole wyboru, jeśli sterownik testowy ma zostać zakończony po zakończeniu. Wyczyść go, jeśli kierowca testowy musi być przymusowo zamknięty.
       - **Maksymalny czas trwania (w sekundach)**: Oszacowanie najdłuższego, racjonalnie oczekiwanego czasu, który program docelowy wymaga przeanalizowania pliku wejściowego. Im dokładniejsze oszacowanie, tym bardziej efektywnie działa aplikacja rozmywająca.
       - **Sterownik testowy można uruchomić wielokrotnie:** Zaznacz to pole wyboru, jeśli sterownik testowy może działać wielokrotnie bez zależności od trwałego lub udostępnionego stanu globalnego.
       - **Można zmienić nazwę sterownika testowego:** Zaznacz to pole wyboru, jeśli można zmienić nazwę pliku wykonywalnego sterownika testowego i nadal działać poprawnie.
       - **Aplikacja rozmyta działa jako pojedynczy proces systemu operacyjnego:** Zaznacz to pole wyboru, jeśli sterownik testowy działa w ramach jednego procesu systemu operacyjnego. Wyczyść go, jeśli sterownik testowy zduplikuje dodatkowe procesy.

Aby uzyskać informacje o konfiguracji YAML dla tego zadania, zapoznaj się z [naszymi opcjami YAML wykrywania zagrożeń zabezpieczeń](yaml-configuration.md#microsoft-security-risk-detection-task) firmy Microsoft

## <a name="roslyn-analyzers-task"></a>Zadanie analizatorów Roslyn

> [!NOTE]
> Przed uruchomieniem zadania Analizatory Roslyn, kompilacja musi spełniać następujące warunki:
>
> - Definicja kompilacji zawiera wbudowane zadanie kompilacji MSBuild lub VSBuild do skompilowania kodu języka C# lub Visual Basic. Zadanie analizatorów opiera się na danych wejściowych i wyjściowych zadania wbudowanego do uruchomienia kompilacji MSBuild z włączonymi analizatorami Roslyn.
> - Agent kompilacji z uruchomionym tym zadaniem kompilacji ma zainstalowany program Visual Studio 2017 w wersji 15.5 lub nowszej, dzięki czemu używa kompilatora w wersji 2.6 lub nowszej.

Szczegóły konfiguracji zadania są wyświetlane na poniższej liście i w notatce.

Dostępne opcje to:

- **Zestaw reguł:** Wartości są **wymagane SDL,** **SDL Zalecane**lub własny zestaw reguł niestandardowych.
- **Wersja analizatorów**: Zalecamy **wybranie opcji Najnowsze**.
- **Plik tłumienia ostrzeżeń kompilatora:** Plik tekstowy z listą identyfikatorów ostrzeżeń, które są pomijane.
- **Opcje sterowania** > **Uruchom to zadanie:** Określa, kiedy zadanie zostanie uruchomione. Wybierz **opcję Warunki niestandardowe,** aby określić bardziej złożone warunki.

> [!NOTE]
>
> - Analizatory Roslyn są zintegrowane z kompilatorem i mogą być uruchamiane tylko jako część kompilacji csc.exe. W związku z tym to zadanie wymaga kompilatora polecenia, który został uruchomiony wcześniej w kompilacji do odtworzenia lub uruchomić ponownie. Ta powtórka lub uruchomienie odbywa się przez wykonywanie zapytań programu Visual Studio Team Services (VSTS) dla dzienników zadań kompilacji MSBuild.
>
>   Nie ma innego sposobu, aby zadanie niezawodnie uzyskać wiersz polecenia kompilacji MSBuild z definicji kompilacji. Rozważyliśmy dodanie dowolnych pól tekstowych, aby umożliwić użytkownikom wprowadzanie wierszy poleceń. Ale wtedy trudno byłoby utrzymać te linie poleceń na bieżąco i zsynchronizowane z główną kompilacją.
>
>   Kompilacje niestandardowe wymagają odtwarzania całego zestawu poleceń, a nie tylko poleceń kompilatora. W takich przypadkach włączenie analizatorów Roslyn nie jest trywialne ani niezawodne.
>
> - Analizatory Roslyn są zintegrowane z kompilatorem. Do wywołania Analizatory Roslyn wymagają kompilacji.
>
>   To nowe zadanie kompilacji jest implementowane przez ponowne komppilowanie projektów języka C#, które zostały już utworzone. Nowe zadanie używa tylko zadań kompilacji MSBuild i VSBuild w tej samej definicji kompilacji lub kompilacji, co oryginalne zadanie. Jednak w tym przypadku nowe zadanie używa ich z roslyn analizatory włączone.
>
>   Jeśli nowe zadanie działa na tym samym agencie co oryginalne zadanie, dane wyjściowe nowego zadania zastępują dane wyjściowe oryginalnego zadania w folderze źródła *s.* Mimo że dane wyjściowe kompilacji jest taka sama, zaleca się uruchomienie MSBuild, skopiowanie danych wyjściowych do katalogu przemieszczania artefaktów, a następnie uruchomienie analizatorów Roslyn.

Aby uzyskać dodatkowe zasoby dla zadania Analizatory Roslyn, zapoznaj się z [analizatory oparte](https://docs.microsoft.com/dotnet/standard/analyzers/) na Roslyn w microsoft docs.

Pakiet analizatora zainstalowany i używany przez to zadanie kompilacji można znaleźć na stronie NuGet [Microsoft.CodeAnalysis.FxCopAnalyzers](https://www.nuget.org/packages/Microsoft.CodeAnalysis.FxCopAnalyzers).

Aby uzyskać informacje na temat konfiguracji YAML dla tego zadania, sprawdź nasze [opcje Analizatorów Roslyn YAML](yaml-configuration.md#roslyn-analyzers-task)

## <a name="tslint-task"></a>Zadanie TSLint

Aby uzyskać więcej informacji na temat TSLint, przejdź do [repozytorium TSLint GitHub](https://github.com/palantir/tslint).

>[!NOTE] 
>Jak być może wiesz, [TSLint GitHub repozytorium](https://github.com/palantir/tslint) strona główna mówi, że TSLint zostanie przestarzały kiedyś w 2019. Firma Microsoft bada [ESLint](https://github.com/eslint/eslint) jako zadanie alternatywne.

Aby uzyskać informacje o konfiguracji YAML dla tego zadania, zapoznaj się z [naszymi opcjami TSLint YAML](yaml-configuration.md#tslint-task)

## <a name="publish-security-analysis-logs-task"></a>Publikowanie zadań Dzienniki analizy zabezpieczeń

Szczegóły konfiguracji zadania są wyświetlane na poniższym zrzucie ekranu i na liście.

![Konfigurowanie zadania kompilacji Dzienniki analizy zabezpieczeń publikowania](./media/security-tools/9-publish-security-analsis-logs600.png)  

- **Nazwa artefaktu:** Dowolny identyfikator ciągu.
- **Typ artefaktu:** W zależności od wybranego wyboru można publikować dzienniki na serwerze DevOps platformy Azure lub w pliku udostępnionym, który jest dostępny dla agenta kompilacji.
- **Narzędzia:** Można zachować dzienniki dla określonych narzędzi lub wybrać **wszystkie narzędzia,** aby zachować wszystkie dzienniki.

Aby uzyskać informacje o konfiguracji YAML dla tego zadania, sprawdź nasze [opcje publikowania dzienników zabezpieczeń YAML](yaml-configuration.md#publish-security-analysis-logs-task)

## <a name="security-report-task"></a>Zadanie Raportu zabezpieczeń

Szczegóły konfiguracji raportu zabezpieczeń są wyświetlane na poniższym zrzucie ekranu i na liście.

![Konfigurowanie zadania kompilacji raportu zabezpieczeń](./media/security-tools/4-createsecurityanalysisreport600.png)

- **Raporty:** Wybierz dowolny z **formatów konsoli potoku,** **pliku TSV**i **pliku HTML.** Dla każdego wybranego formatu tworzony jest jeden plik raportu.
- **Narzędzia:** Wybierz narzędzia w definicji kompilacji, dla których chcesz podsumować wykryte problemy. Dla każdego wybranego narzędzia może istnieć opcja wyboru, czy są widoczne tylko błędy, czy są wyświetlane zarówno błędy, jak i ostrzeżenia w raporcie podsumowania.
- **Opcje zaawansowane:** Jeśli nie ma żadnych dzienników dla jednego z wybranych narzędzi, możesz zarejestrować ostrzeżenie lub błąd. Jeśli zarejestrujesz błąd, zadanie zakończy się niepowodzeniem.
- **Podstawowy folder dzienników:** Można dostosować folder dzienników podstawowych, w którym znajdują się dzienniki. Ale ta opcja zazwyczaj nie jest używana.

Aby uzyskać informacje o konfiguracji YAML dla tego zadania, zapoznaj się z naszymi [opcjami YAML raportu zabezpieczeń](yaml-configuration.md#security-report-task)

## <a name="post-analysis-task"></a>Zadanie po analizie

Szczegóły konfiguracji zadania są wyświetlane na poniższym zrzucie ekranu i na liście.

![Konfigurowanie zadania kompilacji po analizie](./media/security-tools/a-post-analysis600.png)

- **Narzędzia:** Wybierz narzędzia w definicji kompilacji, dla których chcesz warunkowo wstrzyknąć przerwę kompilacji. Dla każdego wybranego narzędzia może istnieć opcja, aby wybrać, czy chcesz przerwać tylko błędy, czy tylko na błędach i ostrzeżeniach.
- **Raport:** Opcjonalnie można zapisać wyniki, które są przyczyną podziału kompilacji. Wyniki są zapisywane w oknie konsoli i pliku dziennika konsoli Azure DevOps.
- **Opcje zaawansowane:** Jeśli nie ma żadnych dzienników dla jednego z wybranych narzędzi, możesz zarejestrować ostrzeżenie lub błąd. Jeśli zarejestrujesz błąd, zadanie zakończy się niepowodzeniem.

Aby uzyskać informacje o konfiguracji YAML dla tego zadania, zapoznaj się [z naszymi opcjami YAML analizy postu](yaml-configuration.md#post-analysis-task)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat konfiguracji opartej na YAML, zapoznaj się z naszym [przewodnikiem konfiguracji YAML](yaml-configuration.md).

Jeśli masz dalsze pytania dotyczące rozszerzenia analizy kodu bezpieczeństwa i oferowanych narzędzi, zapoznaj się z [naszą stroną FAQ](security-code-analysis-faq.md).
