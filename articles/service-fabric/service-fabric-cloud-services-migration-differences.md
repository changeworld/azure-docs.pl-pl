---
title: Różnice między usługami w chmurze i sieci szkieletowej usług | Dokumentacja firmy Microsoft
description: Omówienie pojęć dotyczących migracji z usług w chmurze aplikacjom sieci szkieletowej usług.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: 0b87b1d3-88ad-4658-a465-9f05a3376dee
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 35ab4a9bdd66bf3571e7f189191550f88e17cee2
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206486"
---
# <a name="learn-about-the-differences-between-cloud-services-and-service-fabric-before-migrating-applications"></a>Więcej informacji na temat różnic między usługami w chmurze i sieci szkieletowej usług przed przeprowadzeniem migracji aplikacji.
Usługi sieć szkieletowa usług Microsoft Azure to platforma aplikacji nowej generacji chmury dla aplikacji rozproszonych, skalowalnej, bardzo niezawodne. Podaj wiele nowych funkcji do tworzenia pakietów, wdrażanie, uaktualnianie i zarządzania aplikacji rozproszonej chmury. 

Jest to Przewodnik wprowadzający do migracji aplikacji z usługi w chmurze sieci szkieletowej usług. On skupiono się głównie na architektury i projektu różnice między usługami w chmurze i sieci szkieletowej usług.

## <a name="applications-and-infrastructure"></a>Aplikacji i infrastruktury
Główną różnicą między usługami w chmurze i sieci szkieletowej usług jest relacja między maszynami wirtualnymi, obciążeń i aplikacji. W tym miejscu obciążenia jest zdefiniowany jako kod do wykonania określonego zadania lub świadczyć usługi.

* **Usługi w chmurze jest dotyczące wdrażania aplikacji jako maszyn wirtualnych.** Kod jest ściśle powiązane wystąpienie maszyny Wirtualnej, takie jak sieci Web lub roli proces roboczy. Aby wdrożyć obciążenia usług w chmurze jest wdrożenie co najmniej jedno wystąpienie maszyny Wirtualnej z systemem obciążenia. Brak bez spacji, aplikacji i maszyn wirtualnych, a więc nie obowiązuje żadnych formalnych definicja aplikacji. Aplikację można traktować jako zestaw wystąpień sieci Web lub roli proces roboczy w ramach wdrożenia usługi w chmurze lub całego wdrożenia usługi w chmurze. W tym przykładzie aplikacja jest wyświetlana jako zestaw wystąpień roli.

![Aplikacje usług w chmurze i topologię.][1]

* **Sieć szkieletowa usług to dotyczące wdrażania aplikacji na istniejących maszynach wirtualnych lub maszyn z sieci szkieletowej usług systemu Windows lub Linux.** Usługi zostanie zapisany są całkowicie rozdzielonymi z podstawowej infrastruktury, który jest niedostępny pobieranej przez platformę aplikacji sieci szkieletowej usług, aplikację można wdrożyć w wielu środowiskach. Obciążenia w sieci szkieletowej usług jest nazywane "Usługa", a co najmniej jednej usługi są pogrupowane w formalnie zdefiniowane przez aplikację, która działa na platformę aplikacji sieci szkieletowej usług. Wiele aplikacji można wdrażać w jednym klastrze usługi sieć szkieletowa usług.

![Aplikacje usługi Service Fabric i topologię.][2]

Usługi sieć szkieletowa sam jest warstwa platformy aplikacji, która działa w systemie Windows lub Linux, usługi w chmurze jest system wdrażanie zarządzanych Azure maszyny wirtualne z obciążeniami dołączony.
Model aplikacji usługi Service Fabric ma następujące korzyści:

* Czas szybkiego wdrożenia. Tworzenie wystąpień maszyn wirtualnych może zająć dużo czasu. W sieci szkieletowej usług maszyny wirtualne są wdrażane tylko po do utworzenia klastra obsługującego platforma aplikacji sieci szkieletowej usług. Od tego momentu pakietów aplikacji można wdrożyć w klastrze bardzo szybko.
* Hosting o wysokiej gęstości. Usług w chmurze wirtualna roli proces roboczy obsługuje jeden obciążenia. W sieci szkieletowej usług aplikacje są niezależne od maszyn wirtualnych, co oznacza, że można wdrożyć wiele aplikacji dla niewielkiej liczby maszyn wirtualnych, które można obniżyć całkowity koszt w przypadku większych wdrożeń.
* Usługi sieć szkieletowa platformy, mogą uruchamiać dowolne miejsce którego ma maszyny z systemem Windows Server lub Linux, Azure lub lokalnie. Platformy zapewnia warstwę abstrakcji za pośrednictwem podstawowej infrastruktury co aplikacja może być uruchamiane w różnych środowiskach. 
* Zarządzanie aplikacji rozproszonej. Sieć szkieletowa usług to platforma czy nie tylko aplikacje rozproszone hostów, ale również pomaga zarządzać cyklu ich życia, niezależnie od hostingu maszyny Wirtualnej lub maszyny cyklu życia.

## <a name="application-architecture"></a>Architektura aplikacji
Architektura aplikacji usługi w chmurze zwykle obejmuje wiele zależności zewnętrznych usług, takich jak usługi Service Bus, tabel Azure i magazynu obiektów Blob SQL, Redis i inne osoby do zarządzania, stan i dane aplikacji i komunikacji między sieci Web i Role proces roboczy w ramach wdrożenia usługi w chmurze. Przykład kompletna aplikacja usługi w chmurze może wyglądać następująco:  

![Architektura usług w chmurze][9]

Aplikacje usługi sieć szkieletowa można również korzystać z tej samej usług zewnętrznych w kompletna aplikacja. W tym przykładzie architektury usługi w chmurze to najprostsza ścieżka migracji z usług w chmurze sieci szkieletowej usług jest Zamień tylko wdrożenia usługi w chmurze aplikacji usługi Service Fabric, utrzymywanie ogólna architektura takie same. Sieć Web i roli proces roboczy mogą być przenoszone do usługi sieć szkieletowa usług bezstanowych przy minimalnych zmianach w kodzie.

![Architektura usługi Service Fabric po migracji proste][10]

Na tym etapie system będą nadal działać, taki jak poprzednio. Korzystając z funkcji usługi sieć szkieletowa stanowych, stan zewnętrzne Magazyny można internalized, jak stateful usług, jeśli to możliwe. Jest to bardziej skomplikowane niż proste migracji sieci Web i proces roboczy do usługi sieć szkieletowa usług bezstanowych, ponieważ wymaga zapisywania usług niestandardowych, zawierających podobne funkcje do aplikacji zewnętrznych usług jak przed. Dzięki temu zalety: 

* Usuwanie zależności zewnętrzne 
* Połączenie, wdrażania, zarządzania i modele aktualizacji. 

Przykład wynikowy architektura internalizing tych usług może wyglądać następująco:

![Architektura usługi Service Fabric po pełnej migracji][11]

## <a name="communication-and-workflow"></a>Przepływ pracy i komunikacji
Większość aplikacji usługi w chmurze składa się z więcej niż jedną warstwę. Podobnie aplikacji usługi sieć szkieletowa składa się z więcej niż jedna usługa (zwykle wiele usług). Bezpośrednia komunikacja są dwa modele komunikacji typowe i za pośrednictwem trwałego magazynu zewnętrznego.

### <a name="direct-communication"></a>Bezpośrednia komunikacja
Dzięki bezpośrednia komunikacja warstw może komunikować się bezpośrednio za pośrednictwem punktu końcowego udostępnianych przez każdej warstwy. W środowiskach bezstanowych, takich jak usługi w chmurze, to oznacza, że zaznaczenie wystąpienia roli maszyny Wirtualnej, albo losowo lub okrężnego do równoważenia obciążenia i połączenie bezpośrednio z punktu końcowego.

![Bezpośrednia komunikacja usługi w chmurze][5]

 Bezpośrednia komunikacja jest wspólnym modelu komunikacji w sieci szkieletowej usług. Najważniejsza różnica między usługi Service Fabric i usług w chmurze jest tym w usługi w chmurze połączenia z maszyną wirtualną, podczas gdy w sieci szkieletowej usług połączyć się z usługą. Jest to ważna różnica kilka przyczyn:

* Usługi w sieci szkieletowej usług nie są powiązane z maszyn wirtualnych, które hosta. usługi mogą poruszanie się w klastrze, a w rzeczywistości powinny poruszanie się z różnych powodów: zasób równoważenia, pracy awaryjnej uaktualnienia aplikacji i infrastruktury i ograniczeń umieszczania lub obciążenia. Oznacza to, że adres wystąpienie usługi można zmienić w dowolnym momencie. 
* Maszyna wirtualna w sieci szkieletowej usług może obsługiwać wielu usług, a każda z punktami końcowymi unikatowy.

Sieć szkieletowa usług zapewnia mechanizm odnajdywania usługi, wywołuje usługę nazewnictwa, która może być używany do rozpoznania adresy punktów końcowych usług. 

![Bezpośrednia komunikacja sieci szkieletowej usług][6]

### <a name="queues"></a>Kolejki
Typowe mechanizm komunikacji między warstwami w środowiskach bezstanowych, takich jak usługi w chmurze jest na trwałym przechowywanie zadań służbowych z jedną warstwę do innej kolejki magazynu zewnętrznego. Typowy scenariusz obejmuje warstwa sieci web, która wysyła zadania do kolejki Azure lub usługi Service Bus, gdzie wystąpień roli procesu roboczego można usunąć z kolejki i przetwarzania zadań.

![Komunikacja kolejki usług w chmurze][7]

Można można użyć tego samego modelu komunikacji w sieci szkieletowej usług. Może to być przydatne podczas migracji istniejącej aplikacji usługi w chmurze sieci szkieletowej usług. 

![Bezpośrednia komunikacja sieci szkieletowej usług][8]

## <a name="next-steps"></a>Następne kroki
Jest to najprostsza ścieżka migracji z usług w chmurze sieci szkieletowej usług należy zastąpić tylko wdrożenia usługi w chmurze aplikacji usługi Service Fabric, utrzymywanie ogólna Architektura aplikacji około takie same. Artykuł zawiera także przewodnik ułatwiający konwertowanie usługi bezstanowej sieci szkieletowej usług sieci Web lub roli proces roboczy.

* [Proste migracji: konwertowanie usługi bezstanowej sieci szkieletowej usług sieci Web lub roli procesu roboczego](service-fabric-cloud-services-migration-worker-role-stateless-service.md)

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
