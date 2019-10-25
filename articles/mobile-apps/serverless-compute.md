---
title: Kompiluj bezserwerowe zaplecza aplikacji mobilnej za pomocą Azure Functions i innych usług
description: Więcej informacji na temat usług obliczeniowych umożliwiających tworzenie pełnych, bezserwerowych zaplecza aplikacji mobilnych.
author: elamalani
manager: elamalani
ms.service: vs-appcenter
ms.assetid: 444f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: b8378be675b86ada10b8442c40a54b635ee115a9
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795863"
---
# <a name="build-mobile-backend-components-with-compute-services"></a>Tworzenie składników zaplecza mobilnego za pomocą usług obliczeniowych
Każda aplikacja mobilna musi być zapleczem odpowiedzialnym za przechowywanie danych, logikę biznesową i bezpieczeństwo. Zarządzanie infrastrukturą w celu hostowania i wykonywania kodu zaplecza wymaga zmiany rozmiaru, aprowizacji i skalowania wielu serwerów, zarządzania aktualizacjami systemu operacyjnego i związanych z nimi sprzętu, stosowania poprawek zabezpieczeń, a następnie monitorowania wszystkich tych składników infrastruktury w celu uzyskania wydajności, dostępność i odporność na uszkodzenia. Jest to przydatne w przypadku, gdy deweloperzy nie mają serwerów do zarządzania, nie systemu operacyjnego ani pokrewnych aktualizacji oprogramowania/sprzętu do zarządzania. Pozwala to zaoszczędzić dużo czasu i kosztów dla deweloperów, co oznacza skrócenie czasu wprowadzenia na rynek i skoncentrowanie się na tworzeniu aplikacji.

## <a name="benefits-of-compute"></a>Zalety obliczeń
- Abstrakcja serwerów: nie trzeba martwić się o hosting, stosowanie poprawek i zabezpieczeń, co umożliwia deweloperom skoncentrowanie się na kodzie.
- Szybkie i wydajne skalowanie zapewnia, że zasoby są obsługiwane automatycznie lub na żądanie, na dowolną skalę, której potrzebujesz.
- Wysoka dostępność i odporność na uszkodzenia.
- Mikro rozliczenia gwarantuje, że opłaty są naliczane tylko wtedy, gdy kod jest rzeczywiście uruchomiony.
- Napisz kod, który jest uruchamiany w chmurze w wybranym języku.

Poniższe usługi umożliwiają włączenie funkcji obliczeniowych bezserwerowych w aplikacjach mobilnych.

## <a name="azure-functions"></a>Stan usługi Funkcje Azure
[Azure Functions](https://azure.microsoft.com/services/functions/) to oparte na zdarzeniach środowisko obliczeniowe, które umożliwia wykonywanie kodu pisanego w wybranym języku programowania bez obaw o serwery. Deweloperzy nie muszą zarządzać aplikacją ani infrastrukturą, aby ją uruchamiać. Funkcje skalowania na żądanie i płacisz tylko za czas działania kodu. Azure Functions to doskonały sposób implementacji interfejsu API dla aplikacji mobilnej, ponieważ są one łatwe do wdrożenia i konserwacji oraz dostępne za pośrednictwem protokołu HTTP.

**Najważniejsze funkcje**
- **Sterowane zdarzeniami i skalowalne,** w których deweloperzy mogą używać **wyzwalaczy i powiązań** , aby zdefiniować, kiedy wywoływana jest funkcja i do jakie dane są połączone.
- **Korzystaj z własnych zależności** — środowisko Functions obsługuje rozwiązania NuGet i NPM, dzięki czemu można używać ulubionych bibliotek.
- **Zintegrowane zabezpieczenia** umożliwiają ochronę funkcji wyzwalanych przez protokół HTTP z dostawcami OAuth, takimi jak Azure Active Directory, Facebook, Google, Twitter i konto Microsoft.
- **Uproszczona integracja** z różnymi [usługami platformy Azure](/azure/azure-functions/functions-overview#integrations) i ofertami oprogramowanie jako usługa (SaaS).
- **Elastyczne programowanie umożliwia tworzenie** kodu funkcji bezpośrednio w portalu lub Konfigurowanie ciągłej integracji i wdrażanie kodu za pomocą usługi GitHub, Azure DevOps Services i innych obsługiwanych narzędzi programistycznych.
- Środowisko uruchomieniowe funkcji jest **otwarte** i dostępne w serwisie [GitHub](https://github.com/azure/azure-webjobs-sdk-script).
-  **Udoskonalone środowisko programistyczne** , w którym deweloperzy mogą wykonywać kod, testować i debugować lokalnie przy użyciu preferowanego edytora lub łatwego w użyciu interfejsu internetowego z monitorowaniem za pomocą zintegrowanych narzędzi i wbudowanych funkcji DevOps.
- **Różne języki programowania i opcje hostingu** — Programowanie przy użyciu C#, Node. js, Java, JavaScript lub Python.
- **Model cenowy płatności za użycie** — płać tylko za czas działania kodu.

**Wołują**
- [Azure Portal](https://portal.azure.com)
- [Łączoną](/azure/azure-functions/)
- [Przewodnik dla deweloperów Azure Functions](/azure/azure-functions/functions-reference)
- [Przewodniki Szybki start](/azure/azure-functions/functions-create-first-function-vs-code)
- [Przykłady](/samples/browse/?products=azure-functions&languages=csharp)

## <a name="app-service"></a>App Service
[Azure App Service](https://azure.microsoft.com/services/app-service/) Azure App Service umożliwia tworzenie i hostowanie aplikacji sieci Web oraz RESTful interfejsów API w wybranym języku programowania bez zarządzania infrastrukturą. Oferuje automatyczne skalowanie i wysoką dostępność, obsługuje systemy Windows i Linux, a także umożliwia używanie wdrożeń zautomatyzowanych z usług GitHub i Azure DevOps oraz dowolnych repozytoriów Git.

**Najważniejsze funkcje**
- Obsługa **wielu języków i struktur** dla ASP.NET, ASP.NET Core, Java, Ruby, Node. js, php lub Python. Program PowerShell oraz inne skrypty lub pliki wykonywalne można również uruchamiać jako usługi w tle.
- **Optymalizacja DevOps** — Skonfiguruj ciągłą integrację i wdrażanie za pomocą usługi Azure DevOps, GitHub, BitBucket, Docker Hub lub Azure Container Registry. Zarządzanie aplikacjami w App Service przy użyciu Azure PowerShell lub międzyplatformowego interfejsu wiersza polecenia (CLI).
- **Globalna skala o wysokiej dostępności** do ręcznego lub automatycznego skalowania w górę lub w dół.
- **Połączenia z platformami SaaS i danymi lokalnymi** do wyboru spośród ponad 50 łączników dla systemów korporacyjnych (takich jak SAP), SaaS Services (takich jak Salesforce) i usług internetowych (takich jak Facebook). Dostęp do danych lokalnych przy użyciu Połączenia hybrydowe i sieci wirtualnych platformy Azure.
- **Zabezpieczenia i zgodność** — Azure App Service są zgodne ze standardami ISO, SOC i PCI. Uwierzytelniaj użytkowników przy użyciu Azure Active Directory lub logowania do sieci społecznościowej (Google, Facebook, Twitter i Microsoft). Utwórz ograniczenia adresów IP i Zarządzaj tożsamościami usług.
- **Szablony aplikacji** do wyboru z obszernej listy szablonów aplikacji w portalu Azure Marketplace, takich jak WordPress, Joomla i Drupal.
- **Integracja programu Visual Studio** z dedykowanymi narzędziami w programie Visual Studio usprawnia tworzenie, wdrażanie i debugowanie.

**Wołują**
- [Azure Portal](https://portal.azure.com/)
- [Łączoną](/azure/app-service/)
- [Przewodniki Szybki start](/azure/app-service/app-service-web-get-started-dotnet)
- [Przykłady](/azure/app-service/samples-cli)
- [Samouczki](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)

## <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)
Usługa [Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/) zarządza hostowanym środowiskiem Kubernetes, dzięki czemu można szybko i łatwo wdrażać konteneryzowane aplikacje i zarządzać nimi bez specjalistycznej wiedzy z zakresu aranżacji kontenerów. Eliminuje to również uciążliwości związane z bieżącą obsługą i konserwacją dzięki aprowizowaniu, aktualizowaniu i skalowaniu zasobów na żądanie bez przełączania aplikacji do trybu offline. 

**Najważniejsze funkcje**
- **Łatwo Migruj istniejące aplikacje** do kontenerów i uruchamiaj je w AKS.
- **Uprość wdrażanie** aplikacji opartych na mikrousługach i zarządzanie nimi.
- **Zabezpiecz DevOps dla AKS** , aby osiągnąć balans między szybkością i zabezpieczeniami i szybciej dostarczać kod na dużą skalę.
- **Skaluj przy użyciu AKS i ACI** , aby udostępnić zasobniki w ACI, które zaczynają się w ciągu sekund.
- **Wdrażanie i zarządzanie urządzeniami IoT** na żądanie.
- Uczenie **modelu uczenia maszynowego** przy użyciu narzędzi, takich jak TensorFlow i KubeFlow.

**Wołują**
- [Azure Portal](https://portal.azure.com/)
- [Łączoną](/azure/aks/)
- [Przewodniki Szybki start](/azure/aks/kubernetes-walkthrough-portal)
- [Samouczki](/azure/aks/tutorial-kubernetes-prepare-app)

## <a name="azure-container-instances"></a>Azure Container Instances
[Azure Container Instances (ACI)](https://azure.microsoft.com/services/container-instances/) to doskonałe rozwiązanie, które może działać w kontenerach izolowanych, w tym prostych aplikacjach, automatyzacji zadań i zadaniach kompilacji. Szybkie opracowywanie aplikacji bez konieczności zarządzania maszynami wirtualnymi.

**Najważniejsze funkcje**
- **Czasy szybkiego uruchamiania** jako ACI mogą rozpoczynać się na platformie Azure w ciągu kilku sekund bez konieczności aprowizacji maszyn wirtualnych i zarządzania nimi.
- **Publiczna łączność IP i niestandardowa nazwa DNS**.
- **Zabezpieczenia na poziomie funkcji hypervisor** , które gwarantują, że aplikacja jest izolowana w kontenerze, tak jak w przypadku maszyny wirtualnej.
- **Niestandardowe rozmiary** umożliwiające optymalne wykorzystanie przez umożliwienie dokładnej specyfikacji rdzeni procesora i pamięci. Opłaty są naliczane zgodnie z zapotrzebowaniem i za sekundę, dlatego można skutecznie dostosować wydatki w oparciu o potrzeby.
- **Magazyn trwały** do pobrania i trwałego stanu ACI oferuje bezpośrednie instalowanie udziałów Azure Files.
- **Kontenery systemu Linux i Windows** zaplanowano przy użyciu tego samego interfejsu API.

**Wołują**
- [Azure Portal](https://portal.azure.com/)
- [Łączoną](/azure/container-instances/)
- [Przewodniki Szybki start](/azure/container-instances/container-instances-quickstart-portal)
- [Przykłady](https://azure.microsoft.com/resources/samples/?sort=0&term=aci)
- [Samouczki](/azure/container-instances/container-instances-tutorial-prepare-app)