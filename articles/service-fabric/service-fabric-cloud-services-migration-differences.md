---
title: Różnice między usługami w chmurze i usługi Service Fabric | Dokumentacja firmy Microsoft
description: Omówienie pojęć dotyczących migracji aplikacji z usług Cloud Services w usłudze Service Fabric.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 0b87b1d3-88ad-4658-a465-9f05a3376dee
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 8b486e617389e1611dfebf3d347d2d64df088593
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/28/2019
ms.locfileid: "66258650"
---
# <a name="learn-about-the-differences-between-cloud-services-and-service-fabric-before-migrating-applications"></a>Dowiedz się więcej o różnicach między usługami w chmurze i usługi Service Fabric przed przeprowadzeniem migracji aplikacji.
Microsoft Azure Service Fabric to platforma aplikacji chmurze następnej generacji dla aplikacji rozproszonych, wysoce skalowalną i wysoce niezawodne. Wprowadza wiele nowych funkcji do tworzenia pakietów, wdrażanie, uaktualnianie i zarządzania aplikacji rozproszonych w chmurze. 

Jest to Przewodnik wprowadzający do migrowania aplikacji z usług Cloud Services w usłudze Service Fabric. On koncentruje się głównie na architektury i projektowania różnice między usługami w chmurze i usługi Service Fabric.

## <a name="applications-and-infrastructure"></a>Infrastruktura i aplikacje
Podstawowa różnica między usługami w chmurze i usługi Service Fabric jest relacja między maszynami wirtualnymi, obciążeń i aplikacji. W tym miejscu obciążenie jest zdefiniowany jako kod, który można zapisać do wykonywania określonych zadań lub świadczenia usługi.

* **Usługi w chmurze dotyczy wdrażania aplikacji jako maszyny wirtualne.** Utworzony kod jest ściśle powiązany do wystąpienia maszyny Wirtualnej, takie jak sieci Web lub roli procesu roboczego. Aby wdrożyć obciążenia w usługach w chmurze jest wdrożenie wystąpienia maszyn wirtualnych, które są uruchamiane pakiety robocze. Istnieje nie rozdzielenie maszyn wirtualnych i aplikacji, a więc jest nie formalna definicja aplikacji. Aplikację można traktować jako zestaw wystąpień w sieci Web lub roli procesu roboczego w ramach wdrożenia usług Cloud Services lub całego wdrożenia usług w chmurze. W tym przykładzie aplikacja jest wyświetlana jako zestaw wystąpień roli.

![Aplikacje usług w chmurze i topologii][1]

* **Usługa Service Fabric jest dotyczące wdrażania aplikacji na istniejących maszynach wirtualnych lub maszyn z systemem usługi Service Fabric w systemie Windows lub Linux.** Usługi, które piszesz są całkowicie odłączony od podstawowej infrastruktury, która jest natychmiast wyodrębniony przez platformę aplikacji usługi Service Fabric, aby można było wdrożyć aplikację w wielu środowiskach. Obciążenia w usłudze Service Fabric jest nazywana "Usługa", a co najmniej jednej usługi są zgrupowane w formalnie zdefiniowanych przez aplikację działającą na platformie aplikacji usługi Service Fabric. Wiele aplikacji można wdrożyć w jednym klastrze usługi Service Fabric.

![Aplikacje usługi Service Fabric i topologii][2]

Usługa Service Fabric, sama jest warstwa platform aplikacji, która działa w systemie Windows lub Linux, usług w chmurze jest system wdrażania usługi Azure managed maszyn wirtualnych z obciążeniami dołączone.
Model aplikacji usługi Service Fabric ma szereg zalet:

* Szybkie wdrażanie razy. Tworzenie wystąpień maszyn wirtualnych może zająć dużo czasu. W usłudze Service Fabric maszyny wirtualne są wdrażane tylko wtedy, gdy do utworzenia klastra, która hostuje platformy aplikacji usługi Service Fabric. Od tego momentu pakietów aplikacji można wdrożyć w klastrze bardzo szybko.
* Hosting o dużej gęstości. W usługach w chmurze maszyny Wirtualnej roli proces roboczy hostuje jednego obciążenia. W usłudze Service Fabric aplikacje są niezależne od maszyn wirtualnych z systemem, co oznacza, że można wdrożyć dużą liczbę aplikacji do niewielkiej liczby maszyn wirtualnych, co pozwala zmniejszyć całkowity koszt w przypadku większych wdrożeń.
* Usługa Service Fabric, którą platformy można uruchamiać wszędzie, ma maszyny systemu Windows Server lub Linux, czy jest ono platformy Azure lub lokalnie. Ta platforma udostępnia warstwę abstrakcji za pośrednictwem podstawowej infrastruktury, aby uruchomić aplikację w różnych środowiskach. 
* Zarządzanie aplikacji rozproszonej. Usługa Service Fabric to platforma, nie tylko aplikacje rozproszone hosty, ale również ułatwia zarządzanie ich cyklem życia, niezależnie od hostingu maszyn wirtualnych lub cyklu życia maszyny.

## <a name="application-architecture"></a>Architektura aplikacji
Architektura aplikacji usługi w chmurze zwykle obejmuje wiele zależności usług zewnętrznych, takich jak usługi Service Bus, Azure Table i usługi Blob Storage, SQL, Redis i inne do zarządzania, stan i dane aplikacji i komunikacji między sieci Web i Role procesów roboczych w ramach wdrożenia usług w chmurze. Przykładem kompletnej aplikacji usługi w chmurze może wyglądać następująco:  

![Architektura usług w chmurze][9]

Aplikacje usługi Service Fabric można również użyć tych samych usług zewnętrznych w kompletnej aplikacji. Korzystając z tego przykładu architektury usługi w chmurze, to najprostsza ścieżka migracji z usług Cloud Services do usługi Service Fabric jest zamienić tylko wdrożenia usług w chmurze za pomocą aplikacji usługi Service Fabric, utrzymywanie ogólna architektura, taka sama. Ról sieć Web i proces roboczy mogą być przenoszone do usługi bezstanowej usługi Service Fabric przy minimalnych zmianach w kodzie.

![Architektura usługi Service Fabric po migracji prosty][10]

Na tym etapie powinny nadal działać tak samo jak przed systemu. Korzystając z funkcji stanowej usługi Service Fabric, magazyny stanu zewnętrznego można internalized, usług stanowych, jeśli ma to zastosowanie. Jest to bardziej skomplikowane niż proste migrację sieci Web i ról procesów roboczych do usługi bezstanowej usługi Service Fabric, ponieważ wymaga pisania własnych usług, które zapewniają podobne funkcje do aplikacji, jak poprzednio usług zewnętrznych. Takie postępowanie przynosi korzyści: 

* Usuwanie zależności zewnętrzne 
* Widać, wdrażania, zarządzania i modele aktualizacji. 

Przykład wynikowy architekturę internalizing tych usług może wyglądać następująco:

![Architektura usługi Service Fabric po pełnej migracji][11]

## <a name="communication-and-workflow"></a>Komunikację i przepływ pracy
Większość aplikacji usługi w chmurze składają się z więcej niż jedną warstwę. Podobnie aplikacji usługi Service Fabric składa się z więcej niż jednej usługi (zazwyczaj wielu usług). Dwie typowe modele komunikacji są bezpośrednią komunikację przy użyciu zewnętrznego magazynu trwałego i.

### <a name="direct-communication"></a>Bezpośrednia komunikacja
Bezpośrednia komunikacja warstw może komunikować się bezpośrednio za pośrednictwem punktu końcowego uwidocznionego przez poszczególne warstwy. W środowiskach bezstanowych, takich jak usługi w chmurze, to oznacza, że wybranie wystąpienia roli maszyny Wirtualnej, albo losowo lub działanie okrężne do Równoważenie obciążenia i nawiązywania połączenia z punktem końcowym bezpośrednio.

![Bezpośrednia komunikacja usług w chmurze][5]

 Bezpośrednia komunikacja jest Wspólny model komunikacji w usłudze Service Fabric. Główną różnicą między usługi Service Fabric i Cloud Services jest tego w usługi w chmurze możesz połączyć z maszyną wirtualną, natomiast w usłudze Service Fabric możesz połączyć się z usługą. Jest to ważna różnica kilka przyczyn:

* Usługi w usłudze Service Fabric nie są powiązane z maszyn wirtualnych, które Hostuj je; usługi może poruszać się w klastrze, a w rzeczywistości oczekuje się poruszać się z różnych powodów: Równoważenia zasobów, pracy awaryjnej, uaktualnienia aplikacji i infrastruktury i ograniczeń umieszczania lub obciążenia. Oznacza to, że adres wystąpienie usługi można zmienić w dowolnym momencie. 
* Maszynę wirtualną w usłudze Service Fabric umożliwia hostowanie wielu usług, a każda z punktami końcowymi unikatowy.

Usługa Service Fabric udostępnia mechanizm wykrywania usługi o nazwie usługi nazewnictwa, która może służyć do rozpoznawania adresów punktu końcowego usługi. 

![Bezpośrednia komunikacja usługi Service Fabric][6]

### <a name="queues"></a>Kolejki
Typowe mechanizm komunikacji między warstwami w środowiskach bezstanowych, takich jak usługi w chmurze jest trwale przechowywane zadań służbowych z jednej warstwy do innego za pomocą kolejki magazynu zewnętrznego. Typowym scenariuszem jest warstwy sieci web, która wysyła zadania do kolejki platformy Azure lub usługi Service Bus, gdzie wystąpień roli procesu roboczego mogą usuwania z kolejki i przetwarzać zadania.

![Komunikacja kolejki usług w chmurze][7]

Tego samego modelu komunikacji może służyć w usłudze Service Fabric. Może to być przydatne podczas migrowania istniejących aplikacji usługi w chmurze w usłudze Service Fabric. 

![Bezpośrednia komunikacja usługi Service Fabric][8]

## <a name="parity"></a>Parzystość
[Cloud Services jest podobna do usługi Service Fabric w zakresie stopnia kontroli i łatwości użycia, lecz teraz jest to starsza usługa i usługi Service Fabric jest zalecane w przypadku nowych wdrożeń](https://docs.microsoft.com/azure/app-service/overview-compare); Oto porównanie interfejsu API:


| **Interfejs API usługi w chmurze** | **Usługa Service Fabric interfejsu API** | **Uwagi** |
| --- | --- | --- |
| RoleInstance.GetID | FabricRuntime.GetNodeContext.NodeId lub. NodeName | Identyfikator jest właściwością nazwa węzła |
| RoleInstance.GetFaultDomain | FabricClient.QueryManager.GetNodeList | Filtrowanie według NodeName i użyć właściwości FD |
| RoleInstance.GetUpgradeDomain | FabricClient.QueryManager.GetNodeList | Filtrowanie według NodeName i użyj właściwości uaktualnienia |
| RoleInstance.GetInstanceEndpoints | FabricRuntime.GetActivationContext lub nazewnictwa (ResolveService) | CodePackageActivationContext, która jest dostępna zarówno FabricRuntime.GetActivationContext, jak i w obrębie repliki za pośrednictwem ServiceInitializationParameters.CodePackageActivationContext oferowane w trakcie. Inicjowanie |
| RoleEnvironment.GetRoles | FabricClient.QueryManager.GetNodeList | Jeśli chcesz zrobić tego samego sortowania, filtrowania według typu, który można pobrać listy typów węzłów z klastra manifestu za pośrednictwem FabricClient.ClusterManager.GetClusterManifest i Pobierz typy ról/węzłów, w tym miejscu. |
| RoleEnvironment.GetIsAvailable | Połącz WindowsFabricCluster lub Utwórz FabricRuntime wskazywanej do określonego węzła | * |
| RoleEnvironment.GetLocalResource | CodePackageActivationContext.Log/Temp/Work | * |
| RoleEnvironment.GetCurrentRoleInstance | CodePackageActivationContext.Log/Temp/Work | * |
| LocalResource.GetRootPath | CodePackageActivationContext.Log/Temp/Work | * |
| Role.GetInstances | FabricClient.QueryManager.GetNodeList lub ResolveService | * |
| RoleInstanceEndpoint.GetIPEndpoint | FabricRuntime.GetActivationContext lub nazewnictwa (ResolveService) | * |

## <a name="next-steps"></a>Następne kroki
Jest to najprostsza ścieżka migracji z usług Cloud Services do usługi Service Fabric zastąpić tylko wdrożenie usługi w chmurze za pomocą aplikacji usługi Service Fabric, utrzymywanie ogólna Architektura aplikacji mniej więcej taka sama. Poniższy artykuł zawiera wskazówki ułatwiające przekonwertować bezstanowej usługi Service Fabric w sieci Web lub roli procesu roboczego.

* [Proste migracji: konwertowanie sieci Web lub roli procesu roboczego usługi bezstanowej usługi Service Fabric](service-fabric-cloud-services-migration-worker-role-stateless-service.md)

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
