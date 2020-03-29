---
title: Omówienie dokumentacji analizy kodu zabezpieczeń firmy Microsoft
description: Ten artykuł zawiera omówienie rozszerzenia Analizy kodu zabezpieczeń firmy Microsoft
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
ms.openlocfilehash: 963bc909b69962cded0a50d717e3a653d3d69769
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74851472"
---
# <a name="about-microsoft-security-code-analysis"></a>Analiza kodu zabezpieczeń firmy Microsoft — informacje

Dzięki rozszerzeniu analizy kodu zabezpieczeń firmy Microsoft zespoły mogą dodawać analizę kodu zabezpieczeń do potoków ciągłej integracji i dostarczania usługi Azure DevOps (CI/CD). Ta analiza jest zalecana przez ekspertów z programu [Secure Development Lifecycle (SDL)](https://www.microsoft.com/securityengineering/sdl/practices) firmy Microsoft.

Spójny środowisko użytkownika upraszcza zabezpieczenia, ukrywając złożoność uruchomionych narzędzi. Dzięki dostarczaniu narzędzi opartych na nugetze zespoły nie muszą już zarządzać instalacją lub aktualizacją narzędzi. Dzięki interfejsom wiersza polecenia i podstawowych interfejsów do tworzenia zadań wszyscy użytkownicy mogą mieć tyle kontroli nad narzędziami, ile chcą.

Zespoły mogą również korzystać z zaawansowanych funkcji postprzetworzenia, takich jak:

- Publikowanie dzienników do przechowywania.
- Generowanie zasysanych raportów skoncentrowanych na deweloperach.
- Konfigurowanie podziałów kompilacji na testach regresji.

## <a name="why-should-i-use-microsoft-security-code-analysis"></a>Dlaczego warto korzystać z analizy kodu zabezpieczeń firmy Microsoft?

### <a name="security-simplified"></a>Bezpieczeństwo uproszczone

Dodawanie narzędzi analizy kodu zabezpieczeń firmy Microsoft do potoku devops platformy Azure jest tak proste, jak dodawanie nowych zadań. Dostosuj zadania lub użyj ich domyślnego zachowania. Zadania uruchamiane w ramach potoku azure DevOps i tworzenia dzienników, które szczegółowo wiele rodzajów wyników.

### <a name="clean-builds"></a>Czyste kompilacje

Po rozwiązaniu początkowych problemów zgłoszonych przez narzędzia, można skonfigurować rozszerzenie do podziału kompilacji na nowe problemy.Konfigurowanie ciągłej integracji opiera się na każdym żądaniu ściągnięcia jest łatwe.

### <a name="set-it-and-forget-it"></a>Ustaw go i zapomnij

Domyślnie zadania kompilacji i narzędzia pozostają aktualne. Jeśli istnieje zaktualizowana wersja narzędzia, nie musisz go pobierać i instalować. Rozszerzenie zajmuje się aktualizacją dla Ciebie.

### <a name="under-the-hood"></a>Kulisy

Zadania kompilacji rozszerzenia ukrywają złożoność:
  - Uruchamianie narzędzi analizy statycznej zabezpieczeń.
  - Przetwarzanie wyników z plików dziennika, aby utworzyć raport podsumowania lub przerwać kompilację.

## <a name="microsoft-security-code-analysis-tool-set"></a>Zestaw narzędzi analizy kodu zabezpieczeń firmy Microsoft

Rozszerzenie Analizy kodu zabezpieczeń firmy Microsoft umożliwia łatwe udostępnianie najnowszych wersji ważnych narzędzi analitycznych. Rozszerzenie obejmuje zarówno narzędzia zarządzane przez firmę Microsoft, jak i narzędzia typu open source.

Narzędzia te są automatycznie pobierane do agenta hostowanego w chmurze po użyciu odpowiedniego zadania kompilacji do skonfigurowania i uruchomienia potoku.

W tej sekcji wymieniono zestaw narzędzi, które są obecnie dostępne w rozszerzeniu. Uważaj na dodanie więcej narzędzi. Ponadto wyślij nam swoje sugestie dotyczące narzędzi, które chcesz nam dodać.

### <a name="anti-malware-scanner"></a>Skaner chroniący przed złośliwym oprogramowaniem

Zadanie kompilacji skanera przed złośliwym oprogramowaniem jest teraz zawarte w rozszerzeniu Analizy kodu zabezpieczeń firmy Microsoft. To zadanie musi być uruchomione na agencie kompilacji, który ma już zainstalowany program Windows Defender. Aby uzyskać więcej informacji, zobacz witrynę windows defender w [witrynie sieci Web](https://aka.ms/defender).

### <a name="binskim"></a>BinSkim (Binskim)

BinSkim to lekki skaner przenośny plik wykonywalny (PE), który sprawdza poprawność ustawień kompilatora, ustawień konsolidatora i innych właściwości plików binarnych istotnych dla zabezpieczeń. To zadanie kompilacji udostępnia otokę wiersza polecenia wokół aplikacji konsoli binskim.exe. BinSkim jest narzędziem typu open source. Aby uzyskać więcej informacji, zobacz [BinSkim na GitHub](https://github.com/Microsoft/binskim).

### <a name="credential-scanner"></a>Skaner poświadczeń

Hasła i inne wpisy tajne przechowywane w kodzie źródłowym są poważnym problemem. Skaner poświadczeń jest zastrzeżonym narzędziem do analizy statycznej, które pomaga rozwiązać ten problem. Narzędzie wykrywa poświadczenia, wpisy tajne, certyfikaty i inną zawartość wrażliwą w kodzie źródłowym i danych wyjściowych kompilacji.

### <a name="microsoft-security-risk-detection"></a>Wykrywanie zagrożeń bezpieczeństwa firmy Microsoft

Microsoft Security Risk Detection (MSRD) to usługa oparta na chmurze do testowania rozmytego. Identyfikuje możliwe do wykorzystania błędy bezpieczeństwa w oprogramowaniu. Ta usługa wymaga osobnej subskrypcji i aktywacji. Aby uzyskać więcej informacji, zobacz [Centrum deweloperów MSRD](https://docs.microsoft.com/security-risk-detection/).

### <a name="roslyn-analyzers"></a>Analizatory Roslyn

Roslyn Analyzers to zintegrowane z kompilatorem narzędzie firmy Microsoft do statycznej analizy zarządzanego kodu C# i Visual Basic. Aby uzyskać więcej informacji, zobacz [analizatory oparte na roslyn](https://docs.microsoft.com/dotnet/standard/analyzers/).

### <a name="tslint"></a>TSLint (własn.

TSLint to rozszerzalne narzędzie do analizy statycznej, które sprawdza kod TypeScript pod kątem czytelności, łatwości konserwacji i błędów w funkcjonalności. Jest szeroko wspierany przez nowoczesnych redaktorów i systemy kompilacji. Można go dostosować za pomocą własnych reguł, konfiguracji i formaterów. TSLint jest narzędziem typu open source. Aby uzyskać więcej informacji, zobacz [TSLint na GitHub](https://github.com/palantir/tslint).

## <a name="analysis-and-post-processing-of-results"></a>Analiza i przetwarzanie końcowe wyników

Rozszerzenie Analizy kodu zabezpieczeń firmy Microsoft ma również trzy zadania postprocessing. Te zadania ułatwiają analizowanie wyników znalezionych przez zadania narzędzia zabezpieczeń. Po dodaniu do potoku te zadania zwykle są zgodne ze wszystkimi innymi zadaniami narzędzia.

### <a name="publish-security-analysis-logs"></a>Publikowanie dzienników analizy zabezpieczeń

Zadanie kompilacji Dzienniki analizy zabezpieczeń publikowania zachowuje pliki dziennika narzędzi zabezpieczeń, które są uruchamiane podczas kompilacji. Możesz przeczytać te dzienniki do badania i obserwacji.

Można opublikować pliki dziennika do artefaktów platformy Azure jako plik zip. Można również skopiować je do dostępnego udziału plików z prywatnego agenta kompilacji.

### <a name="security-report"></a>Raport zabezpieczeń

Zadanie kompilacji raportu zabezpieczeń analizuje pliki dziennika. Pliki te są tworzone przez narzędzia zabezpieczeń, które są uruchamiane podczas kompilacji. Zadanie kompilacji następnie tworzy pojedynczy plik raportu podsumowania. Ten plik zawiera wszystkie problemy znalezione przez narzędzia analityczne.

To zadanie można skonfigurować tak, aby raportować wyniki dla określonych narzędzi lub dla wszystkich narzędzi. Można również wybrać poziom problemu do raportu, takich jak tylko błędy lub zarówno błędy i ostrzeżenia.

### <a name="post-analysis-build-break"></a>Po analizie (podział kompilacji)

Za pomocą zadania kompilacji po analizie można wstrzyknąć podział kompilacji, który celowo powoduje niepowodzenie kompilacji. Wstrzyknąć przerwę kompilacji, jeśli jeden lub więcej narzędzi analitycznych zgłosić problemy w kodzie.

Można skonfigurować to zadanie, aby przerwać kompilację w przypadku problemów znalezionych przez określone narzędzia lub wszystkie narzędzia. Można również skonfigurować go na podstawie ważności znalezionych problemów, takich jak błędy lub ostrzeżenia.

>[!NOTE]
>Zgodnie z projektem każde zadanie kompilacji powiedzie się, jeśli zadanie zakończy się pomyślnie. Jest to prawdą, czy narzędzie znajdzie problemy, dzięki czemu kompilacja można uruchomić do zakończenia, umożliwiając wszystkie narzędzia do uruchomienia.

## <a name="next-steps"></a>Następne kroki

Instrukcje dotyczące dołączania i instalowania kodu zabezpieczeń firmy Microsoft można znaleźć w naszym [podręczniku dołączania i instalacji.](security-code-analysis-onboard.md)

Aby uzyskać więcej informacji na temat konfigurowania zadań [kompilacji,](security-code-analysis-customize.md) zobacz nasz przewodnik konfiguracji lub [przewodnik konfiguracji YAML](yaml-configuration.md).

Jeśli masz dalsze pytania dotyczące rozszerzenia i oferowanych narzędzi, sprawdź naszą [stronę FAQ.](security-code-analysis-faq.md)
