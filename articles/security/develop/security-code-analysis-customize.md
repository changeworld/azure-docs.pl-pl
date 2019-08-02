---
title: Przewodnik dostosowywania zadań analizy kodu zabezpieczeń Microsoft Azure
description: Ten artykuł zawiera informacje o dostosowywaniu zadań w rozszerzeniu analizy kodu zabezpieczeń
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
ms.openlocfilehash: ab219b71eb8cd6f6172b7d02a639301c67811b49
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68718355"
---
# <a name="how-to-configure--customize-the-build-tasks"></a>Instrukcje: Konfigurowanie & dostosowywania zadań kompilacji

Na tej stronie szczegółowo opisano opcje konfiguracji dostępne w poszczególnych zadaniach kompilacji, począwszy od zadań związanych z narzędziami do analizy kodu zabezpieczającego, a następnie zadaniami po przetworzeniu.

## <a name="anti-malware-scanner-task"></a>Zadanie skanera chroniącego przed złośliwym oprogramowaniem

> [!NOTE]
> Zadanie kompilacji chroniącej przed złośliwym oprogramowaniem wymaga agenta kompilacji z włączonym usługą Windows Defender, który ma wartość true w "hostowanej program VS2017" i nowszych agentach kompilacji. (Nie zostanie uruchomiony na starszym agencie "Hosted"/"programu VS2015") Nie można zaktualizować podpisów tych agentów, ale sygnatura powinna być zawsze stosunkowo aktualna, krótsza niż 3 godziny.

Zrzut ekranu i szczegółowe informacje dotyczące konfigurowania poniżej.

![Dostosowywanie zadania kompilacji skanera chroniącego przed złośliwym oprogramowaniem](./media/security-tools/5-add-anti-malware-task600.png) 

- Ustawienia typu = **podstawowe**
- Z typem = **Custom**, można podać argumenty wiersza polecenia w celu dostosowania skanowania.

Usługa Windows Defender pobiera i instaluje podpisy przy użyciu klienta Windows Update. Jeśli aktualizacja podpisu nie powiedzie się w agencie kompilacji, kod błędu HRESULT prawdopodobnie będzie pochodził z Windows Update. Aby uzyskać więcej informacji na temat Windows Update błędów i środków zaradczych, odwiedź [Tę stronę](https://docs.microsoft.com/windows/deployment/update/windows-update-error-reference) i tę [stronę w witrynie TechNet](https://social.technet.microsoft.com/wiki/contents/articles/15260.windows-update-agent-error-codes.aspx)

## <a name="binskim-task"></a>BinSkim, zadanie

> [!NOTE]
> Jako warunek wstępny do uruchomienia zadania BinSkim, kompilacja powinna spełniać jeden z poniższych warunków.
>    - Kompilacja tworzy artefakty binarne z kodu zarządzanego.
>    - Masz przekazane artefakty binarne, które chcesz analizować przy użyciu BinSkim.

Zrzut ekranu i szczegółowe informacje dotyczące konfigurowania poniżej. 

![Konfiguracja BinSkim](./media/security-tools/7-binskim-task-details.png)  
1. Ustaw konfigurację kompilacji na Debuguj, aby generować pliki debugowania * **. pdb** . Są one używane przez BinSkim do mapowania problemów znalezionych w danych binarnych wyjściowych z powrotem do kodu źródłowego. 
2. Wybierz typ = **podstawowa** & funkcja = **Analizuj** , aby uniknąć przeszukiwania i tworzenia własnego wiersza polecenia. 
3. **Target** — jeden lub więcej specyfikatorów do pliku, katalogu lub wzorca filtru, który jest rozpoznawany jako jeden lub więcej plików binarnych do przeanalizowania. 
    - Wiele elementów docelowych powinno być oddzielone **średnikami (;)** . 
    - Może być pojedynczym plikiem lub zawierać symbole wieloznaczne.
    - Katalogi zawsze powinny kończyć się na\*
    - Przykłady:

           *.dll;*.exe
           $(BUILD_STAGINGDIRECTORY)\*
           $(BUILD_STAGINGDIRECTORY)\*.dll;$(BUILD_STAGINGDIRECTORY)\*.exe;
4. Jeśli wybierzesz typ = **wiersz polecenia**, 
     - Upewnij się, że pierwszy argument programu **BinSkim. exe** jest **analizowany** przy użyciu pełnych ścieżek lub ścieżek względem katalogu źródłowego.
     - W przypadku danych wejściowych w **wierszu polecenia** wiele elementów docelowych powinno być oddzielone spacją.
     - Można pominąć parametr pliku **/o** lub **/Output** ; zostanie dodany dla Ciebie lub zastąpiony. 
     - **Standardowa konfiguracja wiersza polecenia** 

           analyze $(Build.StagingDirectory)\* --recurse --verbose
           analyze *.dll *.exe --recurse --verbose
          > [!NOTE]
          > Na końcu \* należy określić katalog lub katalogi dla elementu docelowego. 

Aby uzyskać więcej informacji na temat BinSkim o argumentach wiersza polecenia, regułach według identyfikatorów lub kodów zakończenia, odwiedź [Podręcznik użytkownika BinSkim](https://github.com/Microsoft/binskim/blob/master/docs/UserGuide.md)

## <a name="credential-scanner-task"></a>Zadanie skanera poświadczeń
Zrzut ekranu i szczegółowe informacje dotyczące konfigurowania poniżej.
 
![Dostosowanie skanera poświadczeń](./media/security-tools/3-taskdetails.png)

Dostępne opcje to: 
  - **Format danych wyjściowych** — TSV/CSV/SARIF/PREfast
  - **Wersja narzędzia** Rekomendowane Ostatnia
  - **Folder skanowania** — folder w repozytorium do skanowania
  - **Typ pliku wyszukiwania** — opcje pozwalające zlokalizować plik wyszukiwania używany do skanowania.
  - **Plik** pominięć — plik [JSON](https://json.org/) może służyć do pomijania problemów w dzienniku wyjściowym (więcej szczegółów w sekcji Resources). 
  - **Pełne dane wyjściowe** — wyjaśnienie własne 
  - **Rozmiar wsadu** — liczba współbieżnych wątków używanych do równoległego uruchamiania skanerów poświadczeń. Wartością domyślną jest 20 (wartość musi należeć do zakresu od 1 do 2147483647).
  - **Limit czasu dopasowania** — ilość czasu, przez który należy podjąć próbę dopasowania wyszukiwania przed opuszczeniem kontroli. 
  - **Rozmiar buforu odczytu skanowania plików** — rozmiar buforu podczas odczytywania zawartości w bajtach. (Wartość domyślna to 524288) 
  - **Maksymalna liczba bajtów odczytu plików** , które można odczytać z danego pliku podczas analizy zawartości. (Wartość domyślna to 104857600) 
  - **Uruchom to zadanie** (w obszarze **Opcje sterowania**) — określa, kiedy zadanie powinno zostać uruchomione. Wybierz pozycję "warunki niestandardowe", aby określić bardziej złożone warunki. 
  - Wersja zadania kompilacji w ramach usługi Azure DevOps. Nierzadko używane. 

## <a name="microsoft-security-risk-detection-task"></a>Zadanie wykrywania zagrożeń zabezpieczeń firmy Microsoft
> [!NOTE]
> Musisz utworzyć i skonfigurować konto z usługą wykrywania ryzyka jako warunek wstępny, aby móc korzystać z tego zadania. Ta usługa wymaga oddzielnego procesu dołączania; nie jest to "plug-and-Play" jak większość innych zadań w tym rozszerzeniu. Zapoznaj się z artykułem [wykrywanie zagrożeń zabezpieczeń firmy Microsoft](https://aka.ms/msrddocs) i [wykrywanie zagrożeń zabezpieczeń firmy Microsoft: ](https://docs.microsoft.com/security-risk-detection/how-to/) Instrukcje.

Szczegóły dotyczące konfigurowania poniżej.

Wprowadź wymagane dane; Każda opcja ma pomoc dotyczącą tekstu aktywowanego.
   - **Nazwa punktu końcowego usługi Azure DevOps dla MSRD**: Jeśli utworzono ogólny typ punktu końcowego usługi Azure DevOps do przechowywania adresu URL wystąpienia MSRD (dołączany do) i token dostępu interfejsu API REST, można wybrać ten punkt końcowy usługi. W przeciwnym razie kliknij link Zarządzaj, aby utworzyć i skonfigurować nowy punkt końcowy usługi dla tego zadania MSRD. 
   - **Identyfikator konta**: Jest to identyfikator GUID, który można pobrać z adresu URL konta MSRD.
   - **Adresy URL dla plików binarnych**: Rozdzielana średnikami lista publicznie dostępnych adresów URL (do użycia przez rozmytego komputera w celu pobrania plików binarnych).
   - **Adresy URL plików inicjatora**: Rozdzielana średnikami lista publicznie dostępnych adresów URL (do użycia przez rozmytego komputera w celu pobrania nasion). To pole jest opcjonalne, jeśli pliki inicjatora są pobierane razem z danymi binarnymi.
   - **Typ platformy systemu operacyjnego**: Typ platformy systemu operacyjnego (Windows lub Linux) maszyn, na których ma zostać uruchomione rozmyte zadanie.
   - **Wersja systemu Windows/Linux**: Wersja systemu operacyjnego maszyn, na których będzie uruchamiane zadanie rozmyte. Wartość domyślną można zastąpić, jeśli na maszynach jest zainstalowana inna wersja systemu operacyjnego.
   - **Skrypt instalacji pakietu**: Podaj skrypt do uruchomienia na maszynie testowej, aby zainstalować program docelowy test i jego zależności przed przesłaniem zadania rozmytego.
   - **Parametry przesłania zadania**:
       - **Katalog inicjatora**: Ścieżka do katalogu na maszynie rozmytej zawierającej nasiona.
       - **Rozszerzenie inicjatora**: Rozszerzenie pliku dla nasion
       - **Plik wykonywalny sterownika testowego**: Ścieżka do docelowego pliku wykonywalnego na maszynie rozmytej.
       - **Architektura wykonywalna sterownika testowego**: Docelowa architektura pliku wykonywalnego (x86 lub amd64).
       - **Argumenty sterownika testu**: Argumenty wiersza polecenia przekazane do docelowego pliku wykonywalnego testu. Należy zauważyć, że symbol "% TestFile%", wraz z podwójnymi cudzysłowami, zostanie automatycznie zastąpiony pełną ścieżką do pliku docelowego, oczekiwano przeanalizowania sterownika testowego i jest to wymagane.
       - **Proces sterownika testowego kończy się po zakończeniu testu**: Zaznacz, aby zakończyć testowanie sterownika po zakończeniu; Usuń zaznaczenie, jeśli sterownika testowego należy wymusić zamknięcie.
       - **Maksymalny czas trwania (w sekundach)** : Należy oszacować najdłuższy oczekiwany czas, który jest wymagany, aby program docelowy przeanalizować plik wejściowy. Im dokładniejsze oszacowanie, tym bardziej wydajne jest uruchamianie rozmyte.
       - **Sterownik testowy może być uruchamiany wielokrotnie**: Sprawdź, czy sterownik testowy może być uruchamiany wielokrotnie, bez zależnie od utrwalonego/udostępnionego stanu globalnego.
       - **Można zmienić nazwę sterownika testowego**: Sprawdź, czy można zmienić nazwę pliku wykonywalnego sterownika testowego i czy nadal działa poprawnie.
       - **Aplikacja rozmytego uruchamiania działa jako pojedynczy proces systemu operacyjnego**: Sprawdź, czy sterownik testowy działa w ramach jednego procesu systemu operacyjnego; Nie sprawdzaj, czy sterownik testowy duplikuje dodatkowe procesy.


## <a name="roslyn-analyzers-task"></a>Zadanie analizatorów Roslyn
> [!NOTE]
> Jako warunek wstępny do uruchomienia zadania analizatora Roslyn, kompilacja powinna spełniać następujące warunki.
>  - Definicja kompilacji zawiera wbudowane zadanie kompilacji MSBuild lub VSBuild do kompilowania C# kodu (lub VB). To zadanie polega na wejściu i wyjściu tego konkretnego zadania kompilacji w celu ponownego uruchomienia kompilacji MSBuild z włączonymi analizatorami Roslyn.
>  - Agent kompilacji, na którym uruchomiono to zadanie kompilacji, ma zainstalowany program Visual Studio 2017 v 15.5 lub nowszy (kompilator w wersji 2.6. x).
>

Szczegóły dotyczące konfigurowania poniżej.

Dostępne opcje to: 
- **Zestaw reguł** — wymagany jest obiekt SDL, zalecany lub można użyć niestandardowego zestawu reguł.
- **Wersja analizatorów** Rekomendowane Ostatnia
- **Plik pominięć ostrzeżeń kompilatora** — plik tekstowy z listą identyfikatorów ostrzeżeń, które powinny być pomijane. 
- **Uruchom to zadanie** (w obszarze **Opcje sterowania**) — określa, kiedy zadanie powinno zostać uruchomione. Wybierz pozycję "**warunki niestandardowe**", aby określić bardziej złożone warunki. 

> [!NOTE]
> - Analizatory Roslyn są zintegrowane z kompilatorem i mogą być uruchamiane tylko w ramach kompilacji CSC. exe. W związku z tym zadanie wymaga ponownego odtwarzania/ponownego uruchomienia polecenia kompilatora, które zostało uruchomione wcześniej w kompilacji. W tym celu należy wykonać zapytania o VSTS dla dzienników zadań kompilacji MSBuild (nie istnieje żaden inny sposób, aby zadanie nie umożliwiało niezawodnie uzyskać wiersza polecenia kompilacji MSBuild z definicji kompilacji. Rozważmy dodanie dowolnego pola tekstowego, aby umożliwić użytkownikom wprowadzanie ich linii poleceń , ale będzie trudno zachować te aktualne dane i zsynchronizować je z główną kompilacją). Niestandardowe kompilacje wymagają odtwarzania całego zestawu poleceń, a nie tylko poleceń kompilatora i nie są proste/niezawodne, aby umożliwić analizatory Roslyn w tych przypadkach. 
> - Analizatory Roslyn są zintegrowane z kompilatorem i wymagają wywołania kompilacji. To zadanie kompilacji jest implementowane przez ponowne C# Kompilowanie projektów, które zostały już skompilowane przy użyciu tylko zadania kompilacji MSBuild/VSBuild, w tej samej definicji kompilacji/kompilacji, ale w tym przypadku z włączonymi analizatorami. Jeśli zadanie kompilacji jest uruchamiane na tym samym agencie co oryginalne zadanie kompilacji, dane wyjściowe oryginalnego zadania kompilacji MSBuild/VSBuild zostaną zastąpione w folderze "źródła" przez dane wyjściowe tego zadania kompilacji. Dane wyjściowe kompilacji będą takie same, ale zaleca się uruchomienie programu MSBuild, skopiowanie danych wyjściowych do katalogu przemieszczania artefaktów, a następnie uruchomienie Roslyn.
>

Aby uzyskać dodatkowe zasoby dla analizatorów Roslyn, sprawdź zadania [analizatory Roslyn na Microsoft docs](https://docs.microsoft.com/dotnet/standard/analyzers/)

Pakiet analizatora instalowany i używany przez to zadanie kompilacji można znaleźć [tutaj](https://www.nuget.org/packages/Microsoft.CodeAnalysis.FxCopAnalyzers) 

## <a name="tslint-task"></a>TSLint, zadanie

Aby uzyskać więcej informacji na temat TSLint, odwiedź stronę [repozytorium GitHub TSLint](https://github.com/palantir/tslint)
>[!NOTE] 
>W miarę istnienia tego procesu TSLint będzie przestarzałe w 2019 (Źródło: [TSLint repozytorium GitHub](https://github.com/palantir/tslint)) Zespół aktualnie bada [ESLint](https://github.com/eslint/eslint) jako alternatywę i tworzy nowe zadanie dla ESLintu w planie.

## <a name="publish-security-analysis-logs-task"></a>Zadanie publikowania dzienników analizy zabezpieczeń
Zrzut ekranu i szczegółowe informacje dotyczące konfigurowania poniżej.

![Dostosowywanie analizy zabezpieczeń publikacji](./media/security-tools/9-publish-security-analsis-logs600.png)  

- **Nazwa artefaktu** — może to być dowolny identyfikator ciągu
- **Typ artefaktu** — możesz publikować dzienniki na serwerze Azure-DevOps lub w udziale plików, który jest dostępny dla agenta kompilacji. 
- **Narzędzia** — możesz wybrać opcję zachowywania dzienników dla pojedynczych lub konkretnych narzędzi lub wybrać **wszystkie narzędzia** , aby zachować wszystkie dzienniki. 

## <a name="security-report-task"></a>Zadanie raportu zabezpieczeń
Zrzut ekranu i szczegółowe informacje dotyczące konfigurowania poniżej.  
![Konfiguracja po zakończeniu analizy](./media/security-tools/4-createsecurityanalysisreport600.png) 
- **Raporty** — wybierz pliki raportów do utworzenia; jeden zostanie utworzony w każdej **konsoli**formatu, **TSV**i/lub **HTML** 
- **Narzędzia** — wybierz narzędzia w definicji kompilacji, dla którego chcesz uzyskać podsumowanie wykrytych problemów. Dla każdego wybranego narzędzia może być dostępna opcja wyboru, czy chcesz zobaczyć tylko błędy, czy też błędy i ostrzeżenia w raporcie. 
- **Opcje zaawansowane** — można wybrać opcję rejestrowania ostrzeżenia lub błędu (i niepowodzenia zadania) na wypadek braku dzienników dla jednego z wybranych narzędzi.
Można dostosować podstawowy folder dzienników, w którym można znaleźć dzienniki, ale nie jest to typowy scenariusz. 

## <a name="post-analysis-task"></a>Zadanie po analizie
Zrzut ekranu i szczegółowe informacje dotyczące konfigurowania poniżej.

![Dostosowywanie po analizie](./media/security-tools/a-post-analysis600.png) 
- **Narzędzia** — wybierz narzędzia w definicji kompilacji, dla którego chcesz wprowadzić podział kompilacji na podstawie jego wyników. Dla każdego wybranego narzędzia może być dostępna opcja wyboru, czy chcesz przerwać tylko błędy, czy też błędy i ostrzeżenia. 
- **Raport** — możesz opcjonalnie napisać Znalezione wyniki i spowodować przerwanie kompilacji w oknie konsoli usługi Azure DevOps i w pliku dziennika. 
- **Opcje zaawansowane** — można wybrać opcję rejestrowania ostrzeżenia lub błędu (i niepowodzenia zadania) na wypadek braku dzienników dla jednego z wybranych narzędzi.

## <a name="next-steps"></a>Następne kroki

Jeśli masz więcej pytań na temat rozszerzenia i oferowanych narzędzi, zapoznaj się z [naszą stroną często zadawanych pytań.](security-code-analysis-faq.md)


