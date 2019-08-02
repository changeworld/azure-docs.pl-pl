---
title: Przegląd dokumentacji dotyczącej analizy kodu zabezpieczeń Microsoft Azure
description: Ten artykuł zawiera Omówienie rozszerzenia analizy kodu zabezpieczeń
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
ms.openlocfilehash: 283d63bafc583f2ac9da3294644aaebd17d7c950
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68718290"
---
# <a name="about-microsoft-security-code-analysis"></a>Informacje o analizie kodu zabezpieczeń firmy Microsoft

**Rozszerzenie Microsoft Security code Analysis** umożliwia zespołom bezproblemową integrację analizy kodu zabezpieczeń z potokami ciągłej integracji/ciągłego [wdrażania (DevOps)](https://www.microsoft.com/securityengineering/sdl/practices) platformy Azure zgodnie z zaleceniami firmy Microsoft. Zabezpieczenia są uproszczone za pomocą spójnego środowiska użytkownika, które stanowi streszczenie złożoności uruchamiania różnych narzędzi. W przypadku dostarczania narzędzi przez pakiet NuGet narzędzia zespoły nie muszą już zarządzać instalowaniem ani aktualizacją narzędzi. Korzystając z wiersza polecenia i podstawowych interfejsów zadań kompilacji, wszyscy użytkownicy z narzędzia świadome Gurus do codziennych deweloperów mogą mieć możliwie najmniejszą lub większą kontrolę nad narzędziami. Zespoły mogą również korzystać z zaawansowanych funkcji przetwarzania końcowego, takich jak dzienniki publikowania na potrzeby przechowywania, generujące raporty ukierunkowane dla deweloperów, które & konfigurowania podziałów kompilacji na regresje.

## <a name="why-microsoft-security-code-analysis"></a>Dlaczego Analiza kodu zabezpieczeń firmy Microsoft

### <a name="security-simplified"></a>Uproszczone zabezpieczenia

Dodawanie narzędzi do analizy kodu zabezpieczeń do potoku usługi Azure DevOps jest tak proste jak dodanie nowych zadań. Dostosuj je lub przejdź do ustawień domyślnych. Zadania są uruchamiane jako część potoku DevOps i generują dzienniki zawierające szczegółowe informacje dotyczące wszystkich rodzajów wyników.

### <a name="clean-builds"></a>Czyste kompilacje

Po rozwiązaniu problemów początkowych raportowanych przez narzędzia można skonfigurować rozszerzenie, aby przerwać kompilacje w nowych problemach. Skonfigurowanie kompilacji ciągłej integracji dla każdego żądania ściągnięcia nigdy nie było jeszcze proste!

### <a name="set-it-and-forget-it"></a>Ustaw ją i zapomnij

Zadania kompilacji i narzędzia można skonfigurować tak, aby były aktualne (i domyślnie). Jeśli jest dostępna zaktualizowana wersja narzędzia, nie trzeba jej pobierać i instalować. to rozszerzenie wymaga tego. 

>>>
### <a name="under-the-hood"></a>Pod okapem

Zadania kompilacji rozszerzenia analizy kodu zabezpieczeń firmy Microsoft abstrakcyjne złożoność:
  - Uruchamianie narzędzi statycznej analizy zabezpieczeń i
  - Przetwarzanie wyników z plików dziennika w celu utworzenia raportu podsumowującego lub przerwania kompilacji.
>>>

## <a name="security-code-analysis-toolset"></a>Zestaw narzędzi do analizy kodu zabezpieczeń

Rozszerzenie Microsoft Security code Analysis jest łatwo dostępne dla Ciebie, czyli najnowsze wersje ważnych narzędzi analitycznych. Rozszerzenie obejmuje narzędzia wewnętrzne i open source firmy Microsoft. Narzędzia są pobierane automatycznie w agencie hostowanym w chmurze po skonfigurowaniu & uruchamiania potoku przy użyciu odpowiedniego zadania kompilacji. Poniżej znajduje się zestaw narzędzi, które są obecnie dostępne w rozszerzeniu. Bądź na bieżąco i przesyłaj nam swoje sugestie dotyczące narzędzi, które mogą zostać dodane.

### <a name="anti-malware-scanner"></a>Skaner chroniący przed złośliwym oprogramowaniem

Zadanie kompilacji skanera chroniącego przed złośliwym oprogramowaniem jest teraz dołączone do rozszerzenia Microsoft Security code Analysis. Musi być uruchomiony na agencie kompilacji, na którym jest już zainstalowany program Windows Defender. Aby uzyskać więcej informacji, odwiedź [witrynę sieci Web usługi Defender](https://aka.ms/defender) 

### <a name="binskim"></a>BinSkim

BinSkim jest przenośnym skanerem plików wykonywalnych (PE), który weryfikuje ustawienia kompilatora/konsolidatora i inne cechy binarne odpowiednie dla zabezpieczeń. Zadanie kompilacji udostępnia otokę wiersza polecenia dla aplikacji BinSkim. exe. BinSkim to narzędzie Open Source i aby uzyskać więcej informacji, odwiedź stronę [BinSkim w witrynie GitHub](https://github.com/Microsoft/binskim)

### <a name="credential-scanner"></a>Skaner poświadczeń

Hasła i inne wpisy tajne przechowywane w kodzie źródłowym są obecnie istotnym problemem. Skaner poświadczeń to zastrzeżone narzędzie do statycznej analizy, które wykrywa poświadczenia, wpisy tajne, certyfikaty i inną poufną zawartość w kodzie źródłowym i w danych wyjściowych kompilacji.

### <a name="microsoft-security-risk-detection"></a>Wykrywanie zagrożeń zabezpieczeń firmy Microsoft

Wykrywanie zagrożeń bezpieczeństwa to wyjątkowa usługa testowania rozmytego oparta na chmurze w celu identyfikowania luk w zabezpieczeniach w oprogramowaniu. Ta usługa wymaga oddzielnego procesu dołączania. Aby uzyskać więcej informacji, odwiedź stronę [MSRD on docs.Microsoft.com](https://docs.microsoft.com/security-risk-detection/)

### <a name="roslyn-analyzers"></a>Analizatory Roslyn

Narzędzie do analizy statycznej zintegrowane z kompilatorem firmy Microsoft służąceC# do analizowania kodu zarządzanego (i VB). Aby uzyskać więcej informacji, odwiedź stronę [analizatory Roslyn na docs.Microsoft.com](https://docs.microsoft.com/dotnet/standard/analyzers/)

### <a name="tslint"></a>TSLint

TSLint to rozszerzalne narzędzie do analizy statycznej, które sprawdza kod języka TypeScript pod kątem czytelności, łatwość konserwacji i błędów funkcji. Jest ona szeroko obsługiwana w nowoczesnych edytorach i systemach kompilacji i można ją dostosować przy użyciu własnych reguł lint, konfiguracji i programu formatującego. TSLint to narzędzie Open Source i aby uzyskać więcej informacji, odwiedź stronę [TSLint w witrynie GitHub](https://github.com/palantir/tslint)

## <a name="analysis-and-post-processing-of-results"></a>Analiza i przetwarzanie końcowe wyników

Rozszerzenie Microsoft Security code Analysis ma także trzy przydatne zadania po przetworzeniu, które ułatwiają przetwarzanie i analizowanie wyników znalezionych przez zadania narzędzi zabezpieczeń. Są one zazwyczaj dodawane do potoku po zakończeniu wszystkich zadań narzędzi.

### <a name="publish-security-analysis-logs"></a>Publikowanie dzienników analizy zabezpieczeń
Zadanie kompilowania dzienników analizy zabezpieczeń zachowuje pliki dziennika narzędzi zabezpieczeń uruchamianych podczas kompilacji w celu zbadania i monitowania.

Mogą one być publikowane w artefaktach serwera platformy Azure (jako plik zip) lub są kopiowane do dostępnego udziału plików z prywatnego agenta kompilacji.

### <a name="security-report"></a>Raport o zabezpieczeniach
Zadanie kompilacji raportu zabezpieczeń analizuje pliki dziennika utworzone przez narzędzia zabezpieczeń uruchamiane podczas kompilacji i tworzy pojedynczy plik raportu podsumowującego ze wszystkimi problemami znalezionymi przez narzędzia do analizy.

Zadanie można skonfigurować do raportowania wyników dla określonych narzędzi lub dla wszystkich narzędzi, a także wybrać poziom problemów (błędy lub błędy i ostrzeżenia), które powinny zostać zgłoszone.

### <a name="post-analysis-build-break"></a>Po analizie (przerwanie kompilacji)
Zadanie kompilacji po analizie umożliwia klientowi wprowadzanie przerwania kompilacji i niepowodzenie kompilacji w przypadku, gdy jedna z dodatkowych narzędzi analitycznych raportuje wyniki lub problemy w kodzie.

Zadanie można skonfigurować tak, aby przerwać kompilację w poszukiwaniu problemów znalezionych przez określone narzędzia lub dla wszystkich narzędzi, a także na podstawie ważności znalezionych problemów (błędów lub ostrzeżeń).

>[!NOTE]
>Indywidualne zadania kompilacji będą się powieść, przez zaprojektowanie, dopóki narzędzie zakończy się pomyślnie, niezależnie od tego, czy istnieją jakieś wyniki, czy nie, aby kompilacja mogła zostać uruchomiona, umożliwiając ukończenie wszystkich narzędzi.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać instrukcje dotyczące dołączania i instalowania analizy kodu zabezpieczeń, zapoznaj się z naszym przewodnikiem dotyczącym dołączania [i instalacji](security-code-analysis-onboard.md)

Aby uzyskać więcej informacji na temat konfigurowania zadań kompilacji, zobacz nasze [Przewodnik po konfiguracji](security-code-analysis-customize.md)

Jeśli masz więcej pytań na temat rozszerzenia i oferowanych narzędzi, zapoznaj się z [naszą stroną często zadawanych pytań.](security-code-analysis-faq.md)