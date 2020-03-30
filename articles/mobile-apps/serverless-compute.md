---
title: Tworzenie zaplecza bezserwerowej aplikacji mobilnej za pomocą usługi Azure Functions i innych usług
description: Dowiedz się więcej o usługach obliczeniowych używanych do tworzenia stałych, bezserwerowych aplikacji mobilnych zaplecza.
author: codemillmatt
ms.service: vs-appcenter
ms.assetid: 444f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: d6a2bbc984a8808d2cd8a9b292e55d80f43f2e9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240143"
---
# <a name="build-mobile-back-end-components-with-compute-services"></a>Tworzenie mobilnych komponentów zaplecza z usługami obliczeniowymi
Każda aplikacja mobilna potrzebuje zaplecza odpowiedzialnego za przechowywanie danych, logikę biznesową i bezpieczeństwo. Zarządzanie infrastrukturą do hostowania i wykonywania kodu zaplecza wymaga rozmiaru, aprowizowania i skalowania wielu serwerów. Należy również zarządzać aktualizacjami systemu operacyjnego i sprzętem i stosować poprawki zabezpieczeń. Następnie należy monitorować wszystkie te składniki infrastruktury pod kątem wydajności, dostępności i odporności na uszkodzenia. 

Architektura bezserwerowa jest przydatna dla tego typu scenariusza, ponieważ nie masz serwerów do zarządzania i nie ma systemu operacyjnego lub powiązanych aktualizacji oprogramowania lub sprzętu do zarządzania. Architektura bezserwerowa pozwala zaoszczędzić czas i koszty programistów, co oznacza szybszy czas na rynek i skupienie energii na tworzeniu aplikacji.

## <a name="benefits-of-compute"></a>Korzyści z obliczeń
- Abstrakcja serwerów oznacza, że nie musisz się martwić o hosting, łatanie i bezpieczeństwo, co pozwala skupić się wyłącznie na kodzie.
- Natychmiastowe i wydajne skalowanie zapewnia, że zasoby są aprowiowane automatycznie lub na żądanie w dowolnej skali.
- Wysoka dostępność i odporność na uszkodzenia.
- Mikroliterowanie gwarantuje, że naliczane są tylko wtedy, gdy kod jest faktycznie uruchomiony.
- Kod jest uruchamiany w chmurze napisanej w wybranym języku.

Skorzystaj z następujących usług, aby włączyć możliwości obliczeniowe bezserwerowe w aplikacjach mobilnych.

## <a name="azure-functions"></a>Azure Functions
[Usługa Azure Functions](https://azure.microsoft.com/services/functions/) to środowisko obliczeniowe oparte na zdarzeniach, którego można użyć do wykonania kodu napisanego w wybranym języku programowania, bez martwienia się o serwery. Nie musisz zarządzać aplikacją ani infrastrukturą, aby ją uruchomić. Funkcje skalują się na żądanie i płacisz tylko za czas, w której uruchamia się kod. Funkcje platformy Azure to świetny sposób na zaimplementowanie interfejsu API dla aplikacji mobilnej. Są łatwe do wdrożenia i konserwacji i są dostępne za pośrednictwem protokołu HTTP.

**Najważniejsze funkcje**
- Oparte na zdarzeniach i skalowalne, gdzie można użyć wyzwalaczy i powiązań do definiowania, kiedy funkcja jest wywoływana i do jakich danych łączy.
- Przynieś własne zależności, ponieważ funkcja functions obsługuje NuGet i NPM, dzięki czemu można użyć ulubionych bibliotek.
- Zintegrowane zabezpieczenia, dzięki czemu można chronić funkcje wyzwalane przez protokół HTTP za pomocą dostawców OAuth, takich jak Usługa Azure Active Directory, Facebook, Google, Twitter i Konto Microsoft.
- Uproszczona integracja z różnymi [usługami](/azure/azure-functions/functions-overview) i oprogramowaniem platformy Azure jako usługą (SaaS).
- Elastyczne tworzenie, dzięki czemu można kodować swoje funkcje bezpośrednio w witrynie Azure portal lub skonfigurować ciągłą integrację i wdrożyć kod za pośrednictwem Usługi GitHub, Usługi Azure DevOps i inne obsługiwane narzędzia programistyczne.
- Środowisko wykonawcze funkcji jest open source i dostępne w [usłudze GitHub](https://github.com/azure/azure-webjobs-sdk-script).
- Ulepszone środowisko programistyczne, w którym można kodować, testować i debugować lokalnie przy użyciu preferowanego edytora lub łatwego w użyciu interfejsu internetowego z monitorowaniem za pomocą zintegrowanych narzędzi i wbudowanych funkcji DevOps.
- Różnorodność języków programowania i opcji hostingu do tworzenia, takich jak C#, Node.js, Java, JavaScript lub Python.
- Model cen płatności za użycie oznacza, że płacisz tylko za czas spędzony na uruchomieniu kodu.

**Odwołania**
- [Portal Azure](https://portal.azure.com)
- [Dokumentacja usługi Azure Functions](/azure/azure-functions/)
- [Azure Functions — przewodnik dla deweloperów](/azure/azure-functions/functions-reference)
- [Szybki start](/azure/azure-functions/functions-create-first-function-vs-code)
- [Próbki](/samples/browse/?products=azure-functions&languages=csharp)

## <a name="azure-app-service"></a>Azure App Service
Dzięki [usłudze Azure App Service](https://azure.microsoft.com/services/app-service/)możesz tworzyć i hostować aplikacje internetowe i interfejsy API RESTful w wybranym języku programowania bez zarządzania infrastrukturą. Oferuje skalowanie automatyczne i wysoką dostępność, obsługuje zarówno system Windows, jak i Linux oraz umożliwia automatyczne wdrażanie z usługi GitHub, Azure DevOps lub dowolnego repozytorium Git.

**Najważniejsze funkcje**
- Obsługa wielu języków i ram dla ASP.NET, ASP.NET Core, Java, Ruby, Node.js, PHP lub Python. Można również uruchamiać program PowerShell oraz inne skrypty lub pliki wykonywalne jako usługi w tle.
- Optymalizacja devops poprzez ciągłą integrację i wdrażanie za pomocą usługi Azure DevOps, GitHub, BitBucket, Docker Hub lub Azure Container Registry. Zarządzaj aplikacjami w usłudze App Service za pomocą programu Azure PowerShell lub międzyplatformowego interfejsu wiersza polecenia.
- Skala globalna z wysoką dostępnością do skalowania w górę lub w poziomie ręcznie lub automatycznie.
- Połączenia z platformami SaaS i danymi lokalnymi do wyboru z ponad 50 łączników dla systemów korporacyjnych, takich jak SAP, usługi SaaS, takie jak Salesforce, i usługi internetowe, takie jak Facebook. Dostęp do danych lokalnych przy użyciu połączeń hybrydowych i sieci wirtualnych platformy Azure.
- Usługa Azure App Service jest zgodna z normami ISO, SOC i PCI. Uwierzytelnij użytkowników za pomocą usługi Azure Active Directory lub logowania się do mediów społecznościowych, takich jak Google, Facebook, Twitter i Microsoft. Utwórz ograniczenia adresów IP i zarządzaj tożsamościami usługi.
- Szablony aplikacji do wyboru z obszernej listy szablonów aplikacji w witrynie Azure Marketplace, takich jak WordPress, Joomla i Drupal.
- Integracja programu Visual Studio z dedykowanymi narzędziami w programie Visual Studio usprawnia pracę tworzenia, wdrażania i debugowania.

**Odwołania**
- [Portal Azure](https://portal.azure.com/)
- [Dokumentacja usługi Azure App Service](/azure/app-service/)
- [Szybki start](/azure/app-service/app-service-web-get-started-dotnet)
- [Próbki](/azure/app-service/samples-cli)
- [Samouczki](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service
Usługa [Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/) zarządza hostowanym środowiskiem Kubernetes. Usługa AKS umożliwia szybkie i łatwe wdrażanie konteneryzowanych aplikacji i zarządzanie nimi bez specjalistycznej wiedzy z zakresu aranżacji kontenerów. Eliminuje to również obciążenie bieżącej eksploatacji i konserwacji. AKS apowiony, uaktualnienia i skaluje zasoby na żądanie, bez przekładania aplikacji w tryb offline.

**Najważniejsze funkcje**
- Łatwo migruj istniejące aplikacje do kontenerów i uruchamiaj w ramach systemu AKS.
- Uprość wdrażanie i zarządzanie aplikacjami opartymi na mikrousługach.
- Zabezpiecz DevOps dla AKS, aby osiągnąć równowagę między szybkością a bezpieczeństwem i dostarczać kod szybciej na dużą skalę.
- Skalowanie z łatwością przy użyciu AKS i Azure Container Wystąpień do inicjowania obsługi administracyjnej wewnątrz wystąpień kontenera, które rozpoczynają się w sekundach.
- Wdrażanie urządzeń IoT i zarządzanie nimi na żądanie.
- Trenuj modele uczenia maszynowego z wykorzystaniem narzędzi, takich jak TensorFlow i KubeFlow.

**Odwołania**
- [Portal Azure](https://portal.azure.com/)
- [Dokumentacja usługi Azure Kubernetes](/azure/aks/)
- [Szybki start](/azure/aks/kubernetes-walkthrough-portal)
- [Samouczki](/azure/aks/tutorial-kubernetes-prepare-app)

## <a name="azure-container-instances"></a>Azure Container Instances
[Wystąpienia kontenera platformy Azure](https://azure.microsoft.com/services/container-instances/) to doskonałe rozwiązanie dla każdego scenariusza, który może działać w kontenerach izolowanych, takich jak proste aplikacje, automatyzacja zadań i zadania kompilacji. Szybkie tworzenie aplikacji bez zarządzania maszynami wirtualnymi.

**Najważniejsze funkcje**
- Szybkie czasy uruchamiania jako wystąpienia kontenerów można uruchomić kontenery na platformie Azure w ciągu kilku sekund, bez konieczności aprowizowania i zarządzania maszynami wirtualnymi.
- Publiczna łączność IP i niestandardowa nazwa DNS.
- Zabezpieczenia na poziomie funkcji Hypervisor, które gwarantuje, że aplikacja jest tak izolowane w kontenerze, jak to byłoby w maszynie wirtualnej.
- Niestandardowe rozmiary dla optymalnego wykorzystania, umożliwiając dokładne specyfikacje rdzeni procesora i pamięci. Opłaty są naliczane zgodnie z zapotrzebowaniem i za sekundę, dlatego można skutecznie dostosować wydatki w oparciu o potrzeby.
- Magazyn trwały do pobierania i utrwalania stanu. Wystąpienia kontenera oferuje bezpośrednie instalowanie udziałów usługi Azure Files.
- Kontenery systemu Linux i Windows zaplanowane z tym samym interfejsem API.

**Odwołania**
- [Portal Azure](https://portal.azure.com/)
- [Dokumentacja wystąpień kontenerów platformy Azure](/azure/container-instances/)
- [Szybki start](/azure/container-instances/container-instances-quickstart-portal)
- [Próbki](https://azure.microsoft.com/resources/samples/?sort=0&term=aci)
- [Samouczki](/azure/container-instances/container-instances-tutorial-prepare-app)