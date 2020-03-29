---
title: Różnice między usługami w chmurze a siecią szkieletową usług
description: Omówienie koncepcyjne migracji aplikacji z usług w chmurze do sieci szkieletowej usług.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 283ad2c63bb59771dab7881522e737f773ab1705
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75463368"
---
# <a name="learn-about-the-differences-between-cloud-services-and-service-fabric-before-migrating-applications"></a>Dowiedz się więcej o różnicach między usługami w chmurze a siecią szkieletową usług przed migracją aplikacji.
Witryna sieci szkieletowej usług Microsoft Azure to platforma aplikacji w chmurze nowej generacji dla wysoce skalowalnych i wysoce niezawodnych aplikacji rozproszonych. Wprowadza wiele nowych funkcji do pakowania, wdrażania, uaktualniania i zarządzania rozproszonymi aplikacjami w chmurze. 

Jest to przewodnik wprowadzający do migracji aplikacji z usług w chmurze do sieci szkieletowej usług. Koncentruje się przede wszystkim na różnice architektury i projektowania między usługami w chmurze i sieci szkieletowej usług.

## <a name="applications-and-infrastructure"></a>Aplikacje i infrastruktura
Zasadniczą różnicą między usługami w chmurze a siecią szkieletową usług jest relacja między maszynami wirtualnymi, obciążeniami i aplikacjami. Obciążenie w tym miejscu jest zdefiniowany jako kod, który piszesz do wykonania określonego zadania lub świadczenia usługi.

* **Usługi w chmurze polega na wdrażaniu aplikacji jako maszyn wirtualnych.** Zapisywany kod jest ściśle połączony z wystąpieniem maszyny Wirtualnej, takim jak rola sieci Web lub Proces roboczy. Aby wdrożyć obciążenie w usługach w chmurze jest wdrożenie jednego lub więcej wystąpień maszyn wirtualnych, które uruchamiają obciążenie. Nie istnieje separacja aplikacji i maszyn wirtualnych, a więc nie istnieje żadna formalna definicja aplikacji. Aplikację można traktować jako zestaw wystąpień roli sieci Web lub procesu roboczego we wdrożeniu usług w chmurze lub jako całe wdrożenie usług w chmurze. W tym przykładzie aplikacja jest wyświetlana jako zestaw wystąpień roli.

![Aplikacje i topologia usług w chmurze][1]

* **Sieci szkieletowej usług jest o wdrażaniu aplikacji do istniejących maszyn wirtualnych lub maszyn z systemem sieci szkieletowej usług w systemie Windows lub Linux.** Usługi, które piszesz są całkowicie oddzielone od podstawowej infrastruktury, która jest pobierana od platformy aplikacji sieci szkieletowej usług, dzięki czemu aplikacja może być wdrożona w wielu środowiskach. Obciążenie w sieci szkieletowej usług jest nazywany "usługa", a co najmniej jedna usługa są pogrupowane w formalnie zdefiniowanej aplikacji, która działa na platformie aplikacji sieci szkieletowej usług. Wiele aplikacji można wdrożyć w jednym klastrze sieci szkieletowej usług.

![Aplikacje sieci szkieletowej usług i topologia][2]

Sama sieci szkieletowej usług jest warstwą platformy aplikacji, która działa w systemie Windows lub Linux, podczas gdy usługi w chmurze to system wdrażania maszyn wirtualnych zarządzanych przez platformę Azure z dołączonymi obciążeniami.
Model aplikacji sieci szkieletowej usług ma wiele zalet:

* Krótki czas wdrażania. Tworzenie wystąpień maszyn wirtualnych może być czasochłonne. W sieci szkieletowej usług maszyny wirtualne są wdrażane tylko raz, tworząc klaster obsługujący platformę aplikacji sieci szkieletowej usług. Od tego momentu pakiety aplikacji można wdrożyć w klastrze bardzo szybko.
* Hosting o dużej gęstości. W usługach w chmurze maszyna wirtualna roli procesu roboczego obsługuje jedno obciążenie. W sieci szkieletowej usług aplikacje są oddzielone od maszyn wirtualnych, które je uruchamiają, co oznacza, że można wdrożyć dużą liczbę aplikacji na niewielkiej liczbie maszyn wirtualnych, co może obniżyć ogólny koszt większych wdrożeń.
* Platforma sieci szkieletowej usług może działać w dowolnym miejscu, w dowolnym miejscu, w tym komputerach z systemem Windows Server lub Linux, niezależnie od tego, czy jest to platforma Azure, czy lokalna. Platforma zapewnia warstwę abstrakcji nad podstawową infrastrukturą, dzięki czemu aplikacja może działać w różnych środowiskach. 
* Zarządzanie aplikacjami rozproszonymi. Sieci szkieletowej usług jest platformą, która nie tylko obsługuje aplikacje rozproszone, ale także pomaga zarządzać ich cyklu życia niezależnie od hostingu maszyny wirtualnej lub cyklu życia komputera.

## <a name="application-architecture"></a>Architektura aplikacji
Architektura aplikacji usługi w chmurze zwykle obejmuje wiele zależności usług zewnętrznych, takich jak Service Bus, Azure Table i Blob Storage, SQL, Redis i innych do zarządzania stanem i danymi aplikacji i komunikacji między siecią Web i Role procesu roboczego we wdrożeniu usług w chmurze. Przykład pełnej aplikacji usług w chmurze może wyglądać następująco:  

![Architektura usług w chmurze][9]

Aplikacje sieci szkieletowej usług można również wybrać do korzystania z tych samych usług zewnętrznych w pełnej aplikacji. W tym przykładzie architektura usług w chmurze najprostszą ścieżką migracji z usług w chmurze do sieci szkieletowej usług jest zastąpienie tylko wdrożenia usług w chmurze aplikacją sieci szkieletowej usług, utrzymując ogólną architekturę na tym samym poziomie. Role sieci Web i procesu roboczego mogą być przenoszone do usług bezstanowych sieci szkieletowej usług z minimalnymi zmianami kodu.

![Architektura sieci szkieletowej usług po prostej migracji][10]

Na tym etapie system powinien nadal działać tak samo jak poprzednio. Korzystając z funkcji stanowych sieci szkieletowej usług, zewnętrzne magazyny stanu mogą być internalizowane jako usługi stanowe, jeśli ma to zastosowanie. Jest to bardziej związane niż prosta migracja ról sieci Web i ról procesu roboczego do usług bezstanowych sieci szkieletowej usług, ponieważ wymaga pisania usług niestandardowych, które zapewniają równoważne funkcje aplikacji, jak wcześniej usługi zewnętrzne. Korzyści z tego obejmują: 

* Usuwanie zależności zewnętrznych 
* Ujednolicenie modeli wdrażania, zarządzania i uaktualniania. 

Przykładowa architektura internalizacji tych usług może wyglądać następująco:

![Architektura sieci szkieletowej usług po pełnej migracji][11]

## <a name="communication-and-workflow"></a>Komunikacja i przepływ pracy
Większość aplikacji usługi w chmurze składa się z więcej niż jednej warstwy. Podobnie aplikacja sieci szkieletowej usług składa się z więcej niż jednej usługi (zazwyczaj wiele usług). Dwa typowe modele komunikacji to komunikacja bezpośrednia i zewnętrzna trwała pamięć masowa.

### <a name="direct-communication"></a>Komunikacja bezpośrednia
Dzięki bezpośredniej komunikacji warstwy mogą komunikować się bezpośrednio za pośrednictwem punktu końcowego udostępnianego przez każdą warstwę. W środowiskach bezstanowych, takich jak usługi w chmurze, oznacza to wybranie wystąpienia roli maszyny Wirtualnej, losowo lub okrężne, aby zrównoważyć obciążenie i bezpośrednie połączenie z jego punktem końcowym.

![Bezpośrednia komunikacja usług w chmurze][5]

 Komunikacja bezpośrednia jest typowym modelem komunikacji w sieci szkieletowej usług. Kluczową różnicą między siecią szkieletową usług a usługami w chmurze jest to, że w usługach w chmurze łączysz się z maszyną wirtualną, podczas gdy w sieci szkieletowej usług łączysz się z usługą. Jest to ważne rozróżnienie z kilku powodów:

* Usługi w sieci szkieletowej usług nie są powiązane z maszynami wirtualnymi, które je hostują; usługi mogą poruszać się w klastrze, a w rzeczywistości oczekuje się, że będą się poruszać z różnych powodów: równoważenia zasobów, pracy awaryjnej, uaktualnień aplikacji i infrastruktury oraz ograniczeń umieszczania lub obciążenia. Oznacza to, że adres wystąpienia usługi może ulec zmianie w dowolnym momencie. 
* Maszyna wirtualna w sieci szkieletowej usług może obsługiwać wiele usług, z których każda ma unikatowe punkty końcowe.

Sieci szkieletowej usług udostępnia mechanizm odnajdowania usług, o nazwie Usługa nazewnictwa, który może służyć do rozpoznawania adresów końcowych usług. 

![Bezpośrednia komunikacja sieci szkieletowej usług][6]

### <a name="queues"></a>Kolejki
Typowy mechanizm komunikacji między warstwami w środowiskach bezstanowych, takich jak usługi w chmurze, służy do używania zewnętrznej kolejki magazynu do trwałego przechowywania zadań roboczych z jednej warstwy do drugiej. Typowym scenariuszem jest warstwa sieci web, która wysyła zadania do kolejki platformy Azure lub usługi Service Bus, gdzie wystąpienia roli procesu roboczego mogą usuwać w kolejce i przetwarzać zadania.

![Komunikacja kolejki usług w chmurze][7]

Ten sam model komunikacji może służyć w sieci szkieletowej usług. Może to być przydatne podczas migracji istniejącej aplikacji usług w chmurze do sieci szkieletowej usług. 

![Bezpośrednia komunikacja sieci szkieletowej usług][8]

## <a name="parity"></a>Parity
[Usługi w chmurze są podobne do sieci szkieletowej usług pod względem stopnia kontroli w porównaniu z łatwością użycia, ale teraz jest to starsza usługa, a sieci szkieletowej usług jest zalecane do nowego rozwoju;](https://docs.microsoft.com/azure/app-service/overview-compare) poniżej przedstawiono porównanie interfejsu API:


| **Interfejs API usługi w chmurze** | **Interfejs API sieci szkieletowej usług** | **Uwagi** |
| --- | --- | --- |
| RolaInstance.GetID | FabricRuntime.GetNodeContext.NodeId lub . Nazwa węzła | Identyfikator jest właściwością NodeName |
| RolaInstance.GetFaultDomain | FabricClient.QueryManager.GetNodeList | Filtruj na NodeName i używaj właściwości FD |
| RolaInstance.GetUpgradeDomain | FabricClient.QueryManager.GetNodeList | Filtruj na NodeName i użyj właściwości Upgrade |
| RoleInstance.GetInstanceEndpoints | FabricRuntime.GetActivationContext lub nazewnictwo (ResolveService) | CodePackageActivationContext, który jest dostarczany zarówno przez FabricRuntime.GetActivationContext, jak i w replikach za pośrednictwem ServiceInitializationParameters.CodePackageActivationContext pod warunkiem, że podczas . Zainicjować |
| RoleEnvironment.GetRoles | FabricClient.QueryManager.GetNodeList | Jeśli chcesz wykonać ten sam rodzaj filtrowania według typu, możesz pobrać listę typów węzłów z manifestu klastra za pośrednictwem FabricClient.ClusterManager.GetClusterManifest i pobrać stamtąd typy ról/węzłów. |
| RoleEnvironment.GetIsDostępne | Connect-WindowsFabricCluster lub utworzyć FabricRuntime wskazał na określony węzeł | * |
| Źródło usługi RoleEnvironment.GetLocalResource | CodePackageActivationContext.Log/Temp/Work | * |
| RoleEnvironment.GetCurrentRoleInstance | CodePackageActivationContext.Log/Temp/Work | * |
| Ścieżka LocalResource.GetRootPath | CodePackageActivationContext.Log/Temp/Work | * |
| Role.GetInstances | FabricClient.QueryManager.GetNodeList lub ResolveService | * |
| Program RoleInstanceEndpoint.GetIPEndpoint | FabricRuntime.GetActivationContext lub nazewnictwo (ResolveService) | * |

## <a name="next-steps"></a>Następne kroki
Najprostszą ścieżką migracji z usług w chmurze do sieci szkieletowej usług jest zastąpienie tylko wdrożenia usług w chmurze aplikacją sieci szkieletowej usług, zachowując ogólną architekturę aplikacji w przybliżeniu taką samą. Poniższy artykuł zawiera przewodnik, aby pomóc przekonwertować rolę sieci Web lub pracownika do usługi bezstanowej sieci szkieletowej usług.

* [Prosta migracja: konwertowanie roli sieci Web lub procesu roboczego na usługę bezstanową sieci szkieletowej usług](service-fabric-cloud-services-migration-worker-role-stateless-service.md)

<!--Image references-->
[1]: ./media/service-fabric-cloud-services-migration-differences/topology-cloud-services.png
[2]: ./media/service-fabric-cloud-services-migration-differences/topology-service-fabric.png
[5]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-direct.png
[6]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-direct.png
[7]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-queues.png
[8]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-queues.png
[9]: ./media/service-fabric-cloud-services-migration-differences/cloud-services-architecture.png
[10]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-simple.png
[11]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-full.png
