---
title: Przewodnik dostosowywania zadań analizy kodu zabezpieczeń Microsoft Azure
description: W tym artykule opisano dostosowywanie zadań w rozszerzeniu Microsoft Security code Analysis
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
ms.openlocfilehash: c0d49c3ce06f6fa72daf7aff466ef65e09ced09a
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241810"
---
# <a name="configure-and-customize-the-build-tasks"></a>Konfigurowanie i dostosowywanie zadań kompilacji

W tym artykule szczegółowo opisano opcje konfiguracji dostępne w poszczególnych zadaniach kompilacji. Artykuł rozpoczyna się od zadań związanych z narzędziami do analizy kodu zabezpieczeń. Jest ona zakończona z zadaniami po przetworzeniu.

## <a name="anti-malware-scanner-task"></a>Zadanie skanera chroniącego przed złośliwym oprogramowaniem

>[!NOTE]
> Zadanie kompilacji skanera chroniącego przed złośliwym oprogramowaniem wymaga agenta kompilacji z włączonym usługą Windows Defender. Udostępniony program Visual Studio 2017 i jego nowsze wersje zapewniają taki Agent. Zadanie kompilacji nie zostanie uruchomione w hostowanym agencie programu Visual Studio 2015.
>
> Mimo że nie można zaktualizować podpisów tych agentów, sygnatury powinny zawsze być starsze niż trzy godziny.

Szczegóły konfiguracji zadania są pokazane na poniższym zrzucie ekranu i tekście.

![Konfigurowanie zadania kompilacji skanera chroniącego przed złośliwym oprogramowaniem](./media/security-tools/5-add-anti-malware-task600.png)

W polu listy **Typ** zrzutu ekranu jest zaznaczona wartość **podstawowa** . Wybierz pozycję **niestandardowa** , aby podać argumenty wiersza polecenia, które dostosowują skanowanie.

Usługa Windows Defender pobiera i instaluje podpisy przy użyciu klienta Windows Update. Jeśli aktualizacja podpisu nie powiedzie się w agencie kompilacji, kod błędu **HRESULT** prawdopodobnie będzie pochodził z Windows Update.

Aby uzyskać więcej informacji na temat Windows Update błędów i ich środków zaradczych, zobacz [Windows Update kodów błędów według składnika](https://docs.microsoft.com/windows/deployment/update/windows-update-error-reference) i artykułu TechNet [Windows Update kod błędu agenta](https://social.technet.microsoft.com/wiki/contents/articles/15260.windows-update-agent-error-codes.aspx).

## <a name="binskim-task"></a>BinSkim, zadanie

> [!NOTE]
> Aby można było uruchomić zadanie BinSkim, kompilacja musi spełniać jeden z następujących warunków:
>    - Kompilacja tworzy artefakty binarne z kodu zarządzanego.
>    - Masz zadeklarowane artefakty binarne, które chcesz analizować przy użyciu BinSkim.

Szczegóły konfiguracji zadania są pokazane na poniższym zrzucie ekranu i na liście.

![Konfigurowanie zadania kompilacji BinSkim](./media/security-tools/7-binskim-task-details.png)

- Ustaw konfigurację kompilacji na Debuguj, aby pliki debugowania PDB zostały utworzone. BinSkim używa tych plików do mapowania problemów z wyjściowego plików binarnych z powrotem do kodu źródłowego.
- Aby uniknąć przeszukiwania i tworzenia własnego wiersza polecenia:
     - Na liście **Typ** wybierz pozycję **podstawowa**.
     - Na liście **Funkcja** wybierz pozycję **Analizuj**.
- W obszarze **docelowy**wprowadź jeden lub więcej specyfikatorów dla pliku, katalogu lub wzorca filtru. Te specyfikatory są rozpoznawane jako co najmniej jeden plik binarny do przeanalizowania:
    - Wiele określonych elementów docelowych musi być rozdzielonych średnikami (;).
    - Specyfikator może być pojedynczym plikiem lub zawierać symbole wieloznaczne.
    - Specyfikacje katalogów muszą zawsze kończyć się \\znakiem *.
    - Przykłady:

           *.dll;*.exe
           $(BUILD_STAGINGDIRECTORY)\*
           $(BUILD_STAGINGDIRECTORY)\*.dll;$(BUILD_STAGINGDIRECTORY)\*.exe;

- Jeśli na liście **Typ** wybierzesz pozycję **wiersz polecenia** , musisz uruchomić program binskim. exe:
     - Upewnij się, że pierwsze argumenty programu binskim. exe są **analizowane** przy użyciu co najmniej jednej specyfikacji ścieżki. Każda ścieżka może być pełną ścieżką lub ścieżką względną do katalogu źródłowego.
     - Wiele ścieżek docelowych musi być rozdzielonych spacją.
     - Możesz pominąć opcję **/o** lub **/Output** . Wartość wyjściowa jest dodawana do użytkownika lub zastępowana.
     - Standardowe konfiguracje wiersza polecenia są wyświetlane w następujący sposób.

           analyze $(Build.StagingDirectory)\* --recurse --verbose
           analyze *.dll *.exe --recurse --verbose

          > [!NOTE]
          > Końcowe \\* jest ważne w przypadku określenia katalogów dla elementu docelowego.

Aby uzyskać więcej informacji na temat argumentów wiersza polecenia BinSkim, reguł według identyfikatora lub kodów zakończenia, zobacz [Podręcznik użytkownika BinSkim](https://github.com/Microsoft/binskim/blob/master/docs/UserGuide.md).

## <a name="credential-scanner-task"></a>Zadanie skanera poświadczeń

Szczegóły konfiguracji zadania są pokazane na poniższym zrzucie ekranu i na liście.

![Konfigurowanie zadania kompilacji skanera poświadczeń](./media/security-tools/3-taskdetails.png)

Dostępne opcje to:

  - **Format danych wyjściowych**: Dostępne wartości to **TSV**, **CSV**, **SARIF**i **PREfast**.
  - **Wersja narzędzia**: Zalecamy wybranie opcji **najnowsze**.
  - **Folder skanowania**: Folder repozytorium do przeskanowania.
  - **Typ pliku wyszukiwania**: Opcje lokalizowania pliku wyszukiwania, który jest używany do skanowania.
  - **Plik**pominięć: Plik [JSON](https://json.org/) może pominąć problemy w dzienniku danych wyjściowych. Aby uzyskać więcej informacji o scenariuszach pomijania, zobacz sekcję często zadawanych pytań w tym artykule.
  - **Pełne dane wyjściowe**: Nie wymaga wyjaśnień.
  - **Rozmiar wsadu**: Liczba współbieżnych wątków używanych do uruchamiania skanera poświadczeń. Wartość domyślna to 20. Możliwe wartości mieszczą się w zakresie od 1 do 2 147 483 647.
  - **Limit czasu dopasowania**: Czas (w sekundach) poświęcany na próbę dopasowania wyszukiwania przed opuszczeniem kontroli.
  - **Rozmiar buforu odczytu skanowania plików**: Rozmiar w bajtach buforu używany podczas odczytywania zawartości. Wartość domyślna to 524 288.  
  - **Maksymalna liczba bajtów odczytu z skanowania plików**: Maksymalna liczba bajtów odczytywanych z pliku podczas analizy zawartości. Wartość domyślna to 104 857 600.
  - **Opcje kontrolki** **Uruchom to zadanie:**  >  Określa, kiedy zadanie zostanie uruchomione. Wybierz opcję **warunki niestandardowe** , aby określić bardziej złożone warunki.
  - **Wersja**: Wersja zadania kompilacji w ramach usługi Azure DevOps. Ta opcja nie jest często używana.

## <a name="microsoft-security-risk-detection-task"></a>Zadanie wykrywania zagrożeń zabezpieczeń firmy Microsoft

> [!NOTE]
> Przed rozpoczęciem korzystania z zadania MSRD należy utworzyć i skonfigurować konto z usługą wykrywania zagrożeń zabezpieczeń firmy Microsoft (MSRD). Ta usługa wymaga oddzielnego procesu dołączania. W przeciwieństwie do większości innych zadań w tym rozszerzeniu, to zadanie wymaga oddzielnej subskrypcji z MSRD.
>
> Zapoznaj się z artykułem [wykrywanie zagrożeń zabezpieczeń firmy Microsoft](https://aka.ms/msrddocs) i [wykrywanie zagrożeń zabezpieczeń firmy Microsoft: ](https://docs.microsoft.com/security-risk-detection/how-to/) Instrukcje.

Szczegóły dotyczące konfigurowania tego zadania przedstawiono na poniższej liście. Dla każdego elementu interfejsu użytkownika można umieścić kursor nad tym elementem, aby uzyskać pomoc.

   - **Nazwa punktu końcowego usługi Azure DevOps dla MSRD**: Ogólny typ punktu końcowego usługi Azure DevOps przechowuje adres URL wystąpienia MSRD i token dostępu interfejsu API REST. Jeśli utworzono taki punkt końcowy, możesz go określić w tym miejscu. W przeciwnym razie wybierz link **Zarządzaj** , aby utworzyć i skonfigurować nowy punkt końcowy usługi dla tego zadania MSRD.
   - **Identyfikator konta**: Identyfikator GUID, który można pobrać z adresu URL konta MSRD.
   - **Adresy URL dla plików binarnych**: Rozdzielana średnikami lista publicznie dostępnych adresów URL. Komputer rozmyty używa tych adresów URL do pobierania plików binarnych.
   - **Adresy URL plików inicjatora**: Rozdzielana średnikami lista publicznie dostępnych adresów URL. Komputer rozmyty używa tych adresów URL do pobierania nasion. Określenie tej wartości jest opcjonalne, jeśli pliki inicjatora są pobierane razem z danymi binarnymi.
   - **Typ platformy systemu operacyjnego**: Platforma systemu operacyjnego (OS) maszyn, na których działa zadanie rozmyte. Dostępne wartości to **Windows** i **Linux**.
   - **Wersja systemu Windows/Linux**: Wersja systemu operacyjnego maszyn, na których działa zadanie rozmyte. Wartość domyślną można zastąpić, jeśli na maszynach jest zainstalowana inna wersja systemu operacyjnego.
   - **Skrypt instalacji pakietu**: Skrypt do uruchomienia na maszynie testowej. Ten skrypt instaluje program docelowy test i jego zależności przed przesłaniem zadania rozmytego.
   - **Parametry przesłania zadania**:
       - **Katalog inicjatora**: Ścieżka do katalogu na maszynie rozmytej, która zawiera nasiona.
       - **Rozszerzenie inicjatora**: Rozszerzenie nazwy pliku nasion.
       - **Plik wykonywalny sterownika testowego**: Ścieżka do docelowego pliku wykonywalnego na maszynie rozmytej.
       - **Architektura wykonywalna sterownika testowego**: Docelowa architektura pliku wykonywalnego. Dostępne wartości to **x86** i **amd64**.
       - **Argumenty sterownika testu**: Argumenty wiersza polecenia przekazane do pliku wykonywalnego testu. Argument "% TestFile%", w tym cudzysłowy, jest automatycznie zastępowany pełną ścieżką do pliku docelowego. Ten plik jest analizowany przez sterownik testowy i jest wymagany.
       - **Proces sterownika testowego kończy się po zakończeniu testu**: Zaznacz to pole wyboru, jeśli sterownik testowy ma zostać zakończony po zakończeniu. Wyczyść to pole wyboru, jeśli sterownik testowy należy wymusić zamknięcie.
       - **Maksymalny czas trwania (w sekundach)** : Oszacowanie najdłuższego oczekiwanego czasu oczekiwania przez program docelowy na przeanalizowanie pliku wejściowego. Im dokładniejsze oszacowanie, tym wydajniejsze są uruchomienia aplikacji.
       - **Sterownik testowy może być uruchamiany wielokrotnie**: Zaznacz to pole wyboru, jeśli sterownik testowy może być uruchamiany wielokrotnie bez w zależności od trwałego lub udostępnionego stanu globalnego.
       - **Można zmienić nazwę sterownika testowego**: Zaznacz to pole wyboru, jeśli można zmienić nazwę pliku wykonywalnego sterownika testowego i nadal działać poprawnie.
       - **Aplikacja rozmytego uruchamiania działa jako pojedynczy proces systemu operacyjnego**: Zaznacz to pole wyboru, jeśli sterownik testowy działa w ramach jednego procesu systemu operacyjnego. Wyczyść to pole wyboru, jeśli sterownik testowy duplikuje dodatkowe procesy.

## <a name="roslyn-analyzers-task"></a>Zadanie analizatorów Roslyn

> [!NOTE]
> Aby można było uruchomić zadanie analizatorów Roslyn, kompilacja musi spełniać następujące warunki:
> - Definicja kompilacji zawiera wbudowane zadanie kompilacji MSBuild lub VSBuild do kompilowania C# kodu lub Visual Basic. Zadanie analizatory jest zależne od danych wejściowych i wyjściowych wbudowanego zadania do uruchamiania kompilacji MSBuild z włączonymi analizatorami Roslyn.
> - Agent kompilacji, na którym uruchomiono to zadanie kompilacji, ma zainstalowany program Visual Studio 2017 w wersji 15,5 lub nowszej, tak aby używał kompilatora w wersji 2,6 lub nowszej.

Szczegóły konfiguracji zadania przedstawiono na poniższej liście.

Dostępne opcje to:

- **Zestaw reguł**: Wymagane wartości to **SDL**, **zalecane przez SDL**lub własny niestandardowy zestaw reguł.
- **Wersja analizatorów**: Zalecamy wybranie opcji **najnowsze**.
- **Plik pominięć ostrzeżeń kompilatora**: Plik tekstowy z listą identyfikatorów ostrzeżeń, które są pomijane.
- **Opcje kontrolki** **Uruchom to zadanie:**  >  Określa, kiedy zadanie zostanie uruchomione. Wybierz opcję **warunki niestandardowe** , aby określić bardziej złożone warunki.

> [!NOTE]
> - Analizatory Roslyn są zintegrowane z kompilatorem i mogą być uruchamiane tylko w ramach kompilacji CSC. exe. W związku z tym to zadanie wymaga, aby polecenie kompilatora uruchomione wcześniej w kompilacji zostało powtórzone lub ponownie uruchomione. To odtwarzanie lub uruchamianie jest wykonywane przez przeszukiwanie Visual Studio Team Services (VSTS) dla dzienników zadań kompilacji programu MSBuild.
>
>   Nie istnieje żaden inny sposób, aby zadanie niezawodnie pobrać wiersz polecenia kompilacji MSBuild z definicji kompilacji. Rozważamy Dodawanie dowolnego pola tekstowego, aby umożliwić użytkownikom wprowadzanie ich linii poleceń. Jednak będzie trudno zachować aktualność tych wierszy poleceń i synchronizować ją z główną kompilacją.
>
>   Niestandardowe kompilacje wymagają odtwarzania całego zestawu poleceń, a nie tylko poleceń kompilatora. W takich przypadkach włączenie analizatorów Roslyn nie jest proste ani niezawodne.
>
> - Analizatory Roslyn są zintegrowane z kompilatorem. Do wywołania, analizatory Roslyn wymagają kompilacji.
>
>   To nowe zadanie kompilacji jest implementowane przez ponowne C# Kompilowanie projektów, które zostały już skompilowane. Nowe zadanie używa tylko zadań kompilacji MSBuild i VSBuild w tej samej kompilacji lub definicji kompilacji co oryginalne zadanie. Jednak w takim przypadku nowe zadanie używa ich z włączonymi analizatorami Roslyn.
>
>   Jeśli nowe zadanie jest uruchamiane na tym samym agencie co oryginalne zadanie, dane wyjściowe nowego zadania zastąpią dane wyjściowe oryginalnego zadania w folderze źródła *s* . Chociaż dane wyjściowe kompilacji są takie same, zalecamy uruchomienie programu MSBuild, skopiowanie danych wyjściowych do katalogu przemieszczania artefaktów, a następnie uruchomienie analizatorów Roslyn.

Aby uzyskać dodatkowe zasoby dla Roslyn analizatorów, zapoznaj [się z analizatorami opartymi na Roslyn](https://docs.microsoft.com/dotnet/standard/analyzers/) na Microsoft docs.

Pakiet analizatora zainstalowany i używany przez to zadanie kompilacji można znaleźć na stronie narzędzia NuGet [Microsoft. CodeAnalysis. FxCopAnalyzers](https://www.nuget.org/packages/Microsoft.CodeAnalysis.FxCopAnalyzers).

## <a name="tslint-task"></a>TSLint, zadanie

Aby uzyskać więcej informacji na temat TSLint, przejdź do [repozytorium GitHub TSLint](https://github.com/palantir/tslint).

>[!NOTE] 
>Ponieważ użytkownik może mieć świadomość, Strona główna [repozytorium usługi GitHub TSLint](https://github.com/palantir/tslint) informuje, że TSLint będzie przestarzałe w 2019. Firma Microsoft bada [ESLint](https://github.com/eslint/eslint) jako alternatywne zadanie.

## <a name="publish-security-analysis-logs-task"></a>Zadanie publikowania dzienników analizy zabezpieczeń

Szczegóły konfiguracji zadania są pokazane na poniższym zrzucie ekranu i na liście.

![Konfigurowanie zadania kompilacji dzienników analizy zabezpieczeń](./media/security-tools/9-publish-security-analsis-logs600.png)  

- **Nazwa artefaktu**: Dowolny identyfikator ciągu.
- **Typ artefaktu**: W zależności od dokonanego wyboru można opublikować dzienniki na serwerze usługi Azure DevOps lub w udostępnionym pliku, który jest dostępny dla agenta kompilacji.
- **Narzędzia**: Można wybrać opcję zachowywania dzienników dla określonych narzędzi lub wybrać **wszystkie narzędzia** , aby zachować wszystkie dzienniki.

## <a name="security-report-task"></a>Zadanie raportu zabezpieczeń

Szczegóły konfiguracji raportów zabezpieczeń przedstawiono na poniższym zrzucie ekranu i na liście.

![Konfigurowanie zadania kompilacji raportu zabezpieczeń](./media/security-tools/4-createsecurityanalysisreport600.png)

- **Raporty**: Wybierz dowolną z następujących formatów **konsoli potoku**, **plików TSV**i **plików HTML** . Dla każdego wybranego formatu jest tworzony jeden plik raportu.
- **Narzędzia**: Wybierz Narzędzia w definicji kompilacji, dla których chcesz uzyskać podsumowanie wykrytych problemów. Dla każdego wybranego narzędzia może być dostępna opcja wyboru, czy są widoczne tylko błędy, czy też w raporcie podsumowującym znajdują się błędy i ostrzeżenia.
- **Opcje zaawansowane**: Jeśli nie ma żadnych dzienników dla jednego z wybranych narzędzi, możesz wybrać opcję rejestrowania ostrzeżenia lub błędu. Jeśli rejestrujesz błąd, zadanie zakończy się niepowodzeniem.
- **Folder dzienników bazowych**: Można dostosować podstawowy folder dzienników, w którym mają zostać znalezione dzienniki. Jednak ta opcja nie jest zazwyczaj używana.

## <a name="post-analysis-task"></a>Zadanie po analizie

Szczegóły konfiguracji zadania są pokazane na poniższym zrzucie ekranu i na liście.

![Konfigurowanie zadania kompilacji po analizie](./media/security-tools/a-post-analysis600.png)

- **Narzędzia**: Wybierz Narzędzia w definicji kompilacji, dla których chcesz warunkowo wstrzyknąć przerwę kompilacji. Dla każdego wybranego narzędzia może być dostępna opcja wyboru, czy ma zostać przerwana tylko w przypadku błędów, czy tylko w przypadku błędów i ostrzeżeń.
- **Raport**: Opcjonalnie można napisać wyniki, które powodują przerwanie kompilacji. Wyniki są zapisywane w oknie i pliku dziennika konsoli usługi Azure DevOps.
- **Opcje zaawansowane**: Jeśli nie ma żadnych dzienników dla jednego z wybranych narzędzi, możesz wybrać opcję rejestrowania ostrzeżenia lub błędu. Jeśli rejestrujesz błąd, zadanie zakończy się niepowodzeniem.

## <a name="next-steps"></a>Następne kroki

Jeśli masz więcej pytań dotyczących rozszerzenia analizy kodu zabezpieczeń i oferowanych narzędzi, zapoznaj się z [naszą stroną często zadawanych pytań](security-code-analysis-faq.md).
