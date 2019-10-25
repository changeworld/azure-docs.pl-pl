---
title: Automatyzuj wdrażanie i wydawanie aplikacji mobilnych za pomocą usług Visual Studio App Center i Azure
description: Dowiedz się więcej na temat usług, takich jak App Center, które pomagają skonfigurować potok ciągłego dostarczania dla aplikacji mobilnych.
author: elamalani
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 3b8305d02345abe98b674a8ed61626eaf6b5528d
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795603"
---
# <a name="automate-the-deployment-and-release-of-your-mobile-applications-with-continuous-delivery-services"></a>Automatyzuj wdrażanie i wydawanie aplikacji mobilnych za pomocą usług ciągłego dostarczania

Deweloperzy piszą kod i ewidencjonują go w repozytorium kodu, ale zatwierdzenia, które zaewidencjonowano w repozytorium, mogą nie być zawsze spójne. Dzięki wielu deweloperom pracującym nad tym samym projektem, problemy są rozwiązane z integracją, a zespół mógłby działać w sytuacjach, gdy nie działają, błędy piling się, a rozwój projektu jest opóźniony. Deweloperzy muszą czekać, aż cały kod oprogramowania zostanie skompilowany i przetestowany w celu sprawdzenia pod kątem błędów i spowoduje powolne działanie procesu.

Dzięki **ciągłemu dostarczaniu**można zautomatyzować wdrażanie i wydawanie aplikacji mobilnych, bez względu na to, czy aplikacja jest dystrybuowana do grupy testerów, czy pracowników firmy (do testowania wersji beta) czy sklepu z aplikacjami (w przypadku produkcji). Sprawia, że wdrożenia są mniej ryzykowne, zachęca do szybkiego iteracji i pozwala na ciągłą publikację nowych zmian w klientach.

## <a name="distribute-application-binaries-to-beta-testers"></a>Dystrybuowanie plików binarnych aplikacji do testerów wersji beta
Testowanie wersji beta aplikacji mobilnej jest jednym z kluczowych kroków w procesie tworzenia aplikacji. Pozwala ona na wczesne Znajdowanie usterek i problemów w aplikacji oraz przesyłanie opinii, które zwiększają jakość aplikacji, dzięki czemu są gotowe do użycia w środowisku produkcyjnym.

Użyj następujących usług, aby włączyć potok ciągłego dostarczania w aplikacjach mobilnych.

### <a name="visual-studio-app-center"></a>Centrum aplikacji programu Visual Studio
[Dystrybucja App Center](/appcenter/distribution/) to narzędzie dla deweloperów umożliwiające szybkie wydanie kompilacji na urządzeniach użytkowników końcowych. W przypadku korzystania z pełnego portalu instalacji dystrybucja nie jest tylko zaawansowanym rozwiązaniem do dystrybucji testera aplikacji beta, ale również wygodną alternatywą do dystrybucji w publicznych sklepach z aplikacjami. Deweloperzy mogą jeszcze bardziej zautomatyzować przepływ pracy dystrybucji, App Center kompilacje i publiczne integracje magazynu aplikacji.

**Najważniejsze funkcje**
- **Rozpowszechnij aplikację do testerów wersji beta i użytkowników** i upewnij się, że wszystkie Testery znajdują się w najnowszej wersji aplikacji.
- **Powiadamiaj testerów o nowych wersjach** bez testerów, którzy przechodzą ponownie przez przepływ pobierania.
- **Zarządzaj grupami dystrybucji** dla różnych wersji aplikacji.
- **Dystrybucja do sklepów** 
    - [Klienckim](/appcenter/distribution/stores/apple)
    - [Google Play](/appcenter/distribution/stores/googleplay)
    - [Usługa](/appcenter/distribution/stores/intune)
- **Obsługa platform** — iOS, Android, MacOS, systemu tvOS, Xamarin, system reaguje na natywny, Unity, Cordova.
- Automatyczne rejestrowanie urządzeń z systemem iOS w Twoim profilu aprowizacji.

**Wołują**
- [Zarejestruj się w usłudze App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Wprowadzenie do dystrybucji App Center](/appcenter/build/)

### <a name="azure-pipelines"></a>Azure Pipelines

[Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) to w pełni oferowana usługa ciągłej integracji (ci) i ciągłe dostarczanie (CD), które współdziałają z preferowanym dostawcą usługi git i można wdrożyć w większości głównych usług w chmurze, w tym usług platformy Azure. Możesz zacząć od kodu w usłudze GitHub, w witrynie GitHub Enterprise Server, GitLab, BitBucket Cloud lub Azure Repos. Następnie można zautomatyzować kompilowanie, testowanie i wdrażanie kodu w celu Microsoft Azure, Google Cloud Platform lub Amazon Web Services.

**Najważniejsze funkcje**
- Uproszczone środowisko oparte na zadaniach służące do konfigurowania serwera CI dla **natywnych (Android, iOS i Windows) i międzyplatformowych aplikacji mobilnych (Xamarin, Cordova i reagują na natywną)** .
- **Dowolny język, platforma i** aplikacje w chmurze — kompilowanie, testowanie i wdrażanie aplikacji node. js, Python, Java, php, Ruby, go,C++C C#/,, Android i iOS. Uruchamiaj zadania równolegle w systemach Linux, macOS i Windows. Wdrażaj do dostawców chmury, takich jak Azure, AWS i GCP. Dystrybuuj aplikacje mobilne za poorednictwem kanałów beta i sklepów z aplikacjami.
- **Obsługa kontenerów natywnych** — tworzenie nowych kontenerów z łatwością i wypychanie ich do dowolnego rejestru. Wdróż kontenery na niezależnych hostach lub Kubernetes.
- **Zaawansowane przepływy pracy i funkcje** — proste tworzenie łańcucha kompilacji i wielofazowych kompilacji. Obsługa YAML, integracja z testowaniem, bramy wydań, raportowanie i inne informacje.
- **Rozszerzalne** — korzystaj z wielu zadań kompilowania, testowania i wdrażania utworzonych przez społeczność — setki rozszerzeń od zapasu do SonarCloud. Można nawet wdrożyć z innych systemów CI, takich jak Jenkins. Elementy webhook i interfejsy API REST umożliwiają integrację
- **Bezpłatne kompilacje hostowane w chmurze** dla repozytoriów publicznych i prywatnych.
- **Obsługuje wdrażanie do innych dostawców chmury** , takich jak AWS, GCP itp.

**Wołują**
- [Wprowadzenie do przewodnika Azure Pipelines](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Rozpoczynanie pracy z usługą Azure DevOps](https://app.vsaex.visualstudio.com/signup/) 
  
## <a name="distribute-your-application-directly-to-app-stores"></a>Dystrybuuj swoją aplikację bezpośrednio do sklepów z aplikacjami
Gdy aplikacja będzie gotowa do użycia w środowisku produkcyjnym i chcesz ją używać publicznie, należy ją przesłać do sklepów z aplikacjami, w których mogą być pobierane przez klientów. Istnieje wiele sposobów dystrybuowania aplikacji bezpośrednio do sklepów z aplikacjami. 

### <a name="visual-studio-app-center"></a>Centrum aplikacji programu Visual Studio
Usługa [App Center distribute](/appcenter/distribution/stores/) Service umożliwia publikowanie aplikacji mobilnych bezpośrednio w sklepach z aplikacjami. Gdy aplikacja będzie gotowa do pobrania przez użytkowników końcowych, można opublikować pliki binarne aplikacji bezpośrednio z poziomu portalu App Center.  

Możesz bezpośrednio dystrybuować do:
- [Sklep Apple App Store](/appcenter/distribution/stores/apple)
- [Sklep Google Play](/appcenter/distribution/stores/googleplay)
- [Microsoft Intune](/appcenter/distribution/stores/intune)
    
### <a name="apple-app-store"></a>Sklep Apple App Store
Sklep z aplikacjami opracowany i utrzymywany przez firmę Apple umożliwia użytkownikom przeglądanie i pobieranie aplikacji opracowanych na urządzeniach z systemami iOS, MacOS, systemu watchOS i systemu tvOS. Deweloperzy muszą przesłać swoje aplikacje dla systemu iOS do sklepu Apple Store do użytku publicznego.

### <a name="google-play"></a>Google Play

Google Play to oficjalny sklep z aplikacjami dla systemu Android, który umożliwia użytkownikom przeglądanie i pobieranie aplikacji opracowanych dla urządzeń z systemem Android i opublikowanych za pomocą usługi Google.

### <a name="intune"></a>Intune

[Microsoft Intune](/intune/app-management) to usługa oparta na chmurze w obszarze zarządzania mobilnością w przedsiębiorstwie (EMM), która pomaga zwiększyć produktywność pracowników przy zachowaniu ochrony danych firmowych. Usługa Intune umożliwia 
- Zarządzaj urządzeniami przenośnymi i komputerami używanymi przez pracowników do uzyskiwania dostępu do danych firmowych.
- Zarządzanie aplikacjami mobilnymi używanymi przez pracowników.
- Ochrona informacji o firmie przez kontrolowanie sposobu, w jaki pracownicy uzyskują do nich dostęp i udostępniają ją.
- Upewnij się, że urządzenia i aplikacje są zgodne z wymaganiami firmy dotyczącymi bezpieczeństwa.
    
## <a name="deploy-updates-directly-to-users-devices"></a>Wdrażaj aktualizacje bezpośrednio na urządzeniach użytkowników

### <a name="codepush"></a>Usługi codepush
Usługa [usługi codepush](/appcenter/distribution/codepush/) w App Center umożliwia Apache Cordova i reaguje natywnym deweloperom na wdrażanie aktualizacji aplikacji mobilnych bezpośrednio na urządzeniach użytkowników. Działa ona jako centralne repozytorium, które deweloperzy mogą publikować pewne aktualizacje (na przykład w przypadku zmian w języku JS, HTML, CSS i obrazów). Następnie aplikacje mogą wysyłać zapytania o aktualizacje z repozytorium za pomocą dostarczonych zestawów SDK klienta. Dzięki temu można mieć bardziej deterministyczny i bezpośredni model zaangażowania z użytkownikami końcowymi, podczas adresowania usterek lub dodawania małych funkcji bez konieczności ponownego kompilowania pliku binarnego lub rozpowszechniania go ponownie za pośrednictwem jakichkolwiek publicznych sklepów z aplikacjami.

**Najważniejsze funkcje**
- Umożliwia oprogramowaniu Cordova i reagowanie na natywnych deweloperów w celu wdrażania aktualizacji aplikacji mobilnych bezpośrednio na urządzeniach użytkowników bez ich zwolnienia w sklepie.
- Przydatne w przypadku naprawiania usterek lub dodawania/usuwania małych funkcji, które nie wymagają ponownego kompilowania danych binarnych i rozpowszechniania w odpowiednich magazynach.

**Wołują**
- [Zarejestruj się w usłudze App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Wprowadzenie do wypychania kodu w App Center](/appcenter/distribution/codepush/)
- [Interfejs wiersza polecenia usługi codepush](/appcenter/distribution/codepush/cli)