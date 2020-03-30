---
title: Automatyzacja wdrażania i wydawania aplikacji mobilnych za pomocą visual studio App Center i usług platformy Azure
description: Dowiedz się więcej o usługach, takich jak Centrum aplikacji, które pomagają skonfigurować potok ciągłego dostarczania dla aplikacji mobilnych.
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: ccfd0fc0450a6fbd34192dce7e375fe2de7f47c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235353"
---
# <a name="automate-the-deployment-and-release-of-your-mobile-applications-with-continuous-delivery-services"></a>Automatyzacja wdrażania i udostępniania aplikacji mobilnych dzięki usługom ciągłego dostarczania

Jako deweloperzy piszesz kod i sprawdzasz go w repozytorium kodu, ale zatwierdzenia zaewidencjonowane w repozytorium mogą nie zawsze być spójne. Gdy wielu deweloperów pracuje nad tym samym projektem, problemy mogą pochodzić z integracji. Zespoły mogą napotkać sytuacje, w których rzeczy nie działają, błędy piętrzą się, a rozwój projektu zostanie opóźniony. Deweloperzy muszą czekać, aż cały kod oprogramowania zostanie zbudowany i przetestowany, aby sprawdzić, czy nie ma błędów, co sprawia, że proces jest powolny i mniej iteracyjny.

Dzięki ciągłemu dostarczaniu można zautomatyzować wdrażanie i wydawanie aplikacji mobilnych. Nie ma znaczenia, czy rozprowadzasz aplikację do grupy testerów lub pracowników firmy (do testów beta), czy do sklepu z aplikacjami (do produkcji). Ciągłe dostarczanie sprawia, że wdrożenia są mniej ryzykowne i zachęcają do szybkich iteracji. Możesz również w sposób ciągły wypuszczać nowe zmiany dla klientów.

## <a name="distribute-application-binaries-to-beta-testers"></a>Dystrybucja plików binarnych aplikacji do testerów wersji beta
Beta testowanie aplikacji mobilnej jest jednym z kluczowych kroków podczas procesu tworzenia aplikacji. Pomaga znaleźć błędy i problemy w aplikacji na wczesnym etapie. Opinie poprawia jakość aplikacji, gdy przygotowujesz go do użycia w produkcji.

Skorzystaj z następujących usług, aby włączyć potok ciągłego dostarczania w aplikacjach mobilnych.

### <a name="visual-studio-app-center"></a>Visual Studio App Center
[Dystrybucja centrum aplikacji](/appcenter/distribution/) to narzędzie dla deweloperów do szybkiego wydawania kompilacji na urządzeniach. Dzięki pełnej wersji portalu instalacji, App Center Distribute to zaawansowane rozwiązanie do dystrybucji beta testera aplikacji. Jest to również wygodna alternatywa dla dystrybucji za pośrednictwem publicznych sklepów z aplikacjami. Deweloperzy mogą jeszcze bardziej zautomatyzować przepływ pracy dystrybucji dzięki kompilacji usługi App Center i integracji publicznych sklepów z aplikacjami.

**Najważniejsze funkcje**
- Rozpowszechniaj aplikację wśród testerów wersji beta i użytkowników i upewnij się, że wszyscy testerzy są w najnowszej wersji aplikacji.
- Powiadamiaj testerów o nowych wersjach bez ponownego przechodzenia przez przepływ pobierania przez testerów.
- Zarządzanie grupami dystrybucyjnymi dla różnych wersji aplikacji.
- Dystrybucja do sklepów: 
    - [Apple](/appcenter/distribution/stores/apple)
    - [Google Play](/appcenter/distribution/stores/googleplay)
    - [Intune](/appcenter/distribution/stores/intune)
- Uzyskaj obsługę platformy dla systemów iOS, Android, macOS, tvOS, Xamarin, React Native, Unity i Cordova.
- Automatycznie zarejestruj urządzenia z systemem iOS w profilu inicjowania obsługi administracyjnej.

**Odwołania**
- [Zarejestruj się w programie Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Wprowadzenie do dystrybucji w centrum aplikacji](/appcenter/build/)

### <a name="azure-pipelines"></a>Azure Pipelines

[Usługa Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) to w pełni funkcjonalna usługa ciągłej integracji (CI) i ciągłego dostarczania (CD), która współpracuje z preferowanym dostawcą git. Usługi Azure Pipelines można wdrożyć w większości głównych usług w chmurze, takich jak usługi platformy Azure. Możesz zacząć od kodu na GitHub, GitHub Enterprise Server, GitLab, Bitbucket Cloud lub Azure Repos. Następnie można zautomatyzować kompilacji, testowania i wdrażania kodu na platformie Microsoft Azure, Google Cloud Platform lub Amazon Web Services (AWS).

**Najważniejsze funkcje**
- **Uproszczone środowisko oparte na zadaniach konfigurowania serwera ciągłej integracji:** Skonfiguruj serwer ciągłej integracji dla aplikacji mobilnych natywnych (Android, iOS i Windows) oraz międzyplatformowych (Xamarin, Cordova i React Native).
- **Dowolny język, platforma i chmura:** Twórz, testuj i wdrażaj aplikacje Node.js, Python, Java, PHP, Ruby, Go, C/C++, C#, Android i iOS. Działaj równolegle w systemach Linux, macOS i Windows. Wdrażanie u dostawców usług w chmurze, takich jak Azure, AWS i Google Cloud Platform. Rozpowszechniaj aplikacje mobilne za pośrednictwem kanałów beta i sklepów z aplikacjami.
- **Obsługa kontenerów natywnych:** Łatwo twórz nowe kontenery i wypychaj je do dowolnego rejestru. Wdrażanie kontenerów do niezależnych hostów lub kubernetes.
- **Zaawansowane przepływy pracy i funkcje:** Łatwe tworzenie łańcuchów kompilacji i wielofazowych kompilacji. Uzyskaj obsługę YAML, integracji testów, bramek wydania, raportowania i innych.
- **Rozszerzalny:** Korzystaj z zakresu zadań kompilacji, testowania i wdrażania utworzonych przez społeczność, które obejmują setki rozszerzeń od Slack do SonarCloud. Można nawet wdrożyć z innych systemów ciągłej integracji, takich jak Jenkins. Haki internetowe i interfejsy API REST mogą pomóc w integracji.
- **Bezpłatne kompilacje hostowane w chmurze:** Kompilacje te są dostępne dla publicznych i prywatnych repozytoriów.
- **Obsługa wdrażania u innych dostawców chmury:** Dostawcy obejmują AWS i Google Cloud Platform.

**Odwołania**
- [Przewodnik Wprowadzenie do usługi Azure Pipelines](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Rozpoczynanie pracy z usługą Azure DevOps](https://app.vsaex.visualstudio.com/signup/)
  
## <a name="distribute-your-application-directly-to-app-stores"></a>Rozpowszechnianie aplikacji bezpośrednio w sklepach z aplikacjami
Gdy aplikacja jest gotowa do użycia w produkcji i chcesz, aby była używana publicznie, musi zostać przesłana do sklepów z aplikacjami, gdzie może zostać pobrana przez klientów. Istnieje wiele sposobów dystrybucji aplikacji bezpośrednio do sklepów z aplikacjami. 

### <a name="visual-studio-app-center"></a>Visual Studio App Center
Dzięki aplikacji App Center Distribute możesz publikować [aplikacje mobilne](/appcenter/distribution/stores/)bezpośrednio w sklepach z aplikacjami. Po aplikacji jest gotowy do pobrania przez użytkowników, można opublikować pliki binarne aplikacji bezpośrednio z portalu Visual Studio App Center. 

Można bezpośrednio dystrybuować do:
- [Apple App Store](/appcenter/distribution/stores/apple)
- [Sklep Google Play](/appcenter/distribution/stores/googleplay)
- [Microsoft Intune](/appcenter/distribution/stores/intune)
    
### <a name="apple-app-store"></a>Apple App Store
W sklepie z aplikacjami opracowanym i utrzymywanym przez firmę Apple użytkownicy mogą przeglądać i pobierać aplikacje opracowane dla urządzeń z systemem iOS, MacOS, WatchOS i tvOS. Deweloperzy muszą przesłać swoje aplikacje na iOS do sklepu Apple App Store do użytku publicznego.

### <a name="google-play"></a>Google Play

Google Play to oficjalny sklep z aplikacjami dla systemu operacyjnego Android, w którym użytkownicy mogą przeglądać i pobierać aplikacje opracowane dla urządzeń z androidem, które są publikowane za pośrednictwem Google.

### <a name="intune"></a>Usługa Intune

[Usługa Microsoft Intune](/intune/app-management) to usługa oparta na chmurze w przestrzeni zarządzania mobilnością w przedsiębiorstwie, która pomaga zwiększyć produktywność pracowników przy jednoczesnym zachowaniu ochrony danych firmowych. Usługa Intune umożliwia:
- zarządzanie urządzeniami przenośnymi i komputerami używanymi przez pracowników do uzyskiwania dostępu do danych firmowych,
- Zarządzaj aplikacjami mobilnymi używanymi przez pracowników.
- Chroń informacje o swojej firmie, kontrolując sposób, w jaki pracownicy uzyskują do nich dostęp i udostępniają je.
- Upewnij się, że urządzenia i aplikacje są zgodne z wymaganiami firmy dotyczącymi zabezpieczeń.
    
## <a name="deploy-updates-directly-to-users-devices"></a>Wdrażanie aktualizacji bezpośrednio na urządzeniach użytkowników

### <a name="codepush"></a>Kod Kodowy
Dzięki [CodePush](/appcenter/distribution/codepush/) w Centrum aplikacji apache Cordova i React Native deweloperzy mogą wdrażać aktualizacje aplikacji mobilnych bezpośrednio na urządzeniach swoich użytkowników. Działa jako centralne repozytorium, które deweloperzy mogą publikować pewne aktualizacje, takie jak JavaScript, HTML, CSS i zmiany obrazu. Następnie aplikacje mogą wysyłać zapytania o aktualizacje z repozytorium przy użyciu dostarczonych skomp. W ten sposób możesz mieć bardziej deterministyczny i bezpośredni model zaangażowania z użytkownikami podczas rozwiązywania błędów lub dodawania małych funkcji. Nie są wymagane, aby odbudować plik binarny lub redystrybuować go za pośrednictwem żadnych publicznych sklepów z aplikacjami.

**Najważniejsze funkcje**
- Deweloperzy Cordova i React Native mogą wdrażać aktualizacje aplikacji mobilnych bezpośrednio na urządzeniach swoich użytkowników bez zwalniania ich w sklepie.
- Przydatne do naprawiania błędów lub dodawania i usuwania małych funkcji, które nie wymagają odbudowania pliku binarnego i redystrybucji za pośrednictwem odpowiednich sklepów.

**Odwołania**
- [Zarejestruj się w programie Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Wprowadzenie do codepush w Centrum aplikacji](/appcenter/distribution/codepush/)
- [KodPush CLI](/appcenter/distribution/codepush/cli)