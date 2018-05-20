---
title: Wprowadzenie do usługi Menedżer zasobów klastra sieci szkieletowej | Dokumentacja firmy Microsoft
description: Wprowadzenie do usługi sieci szkieletowej klastra Menedżera zasobów.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: cfab735b-923d-4246-a2a8-220d4f4e0c64
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: f25a422385abfcdb7020eb7477c0ae2ee55cd8fb
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="introducing-the-service-fabric-cluster-resource-manager"></a>Wprowadzenie do Menedżera zasobów klastra sieci szkieletowej usług
Tradycyjnie zarządzania systemów informatycznych lub usługi online przeznaczone przypisywanie określonych fizycznych lub maszynach wirtualnych do tych określonych usług lub systemów. Usługi zostały zaprojektowane pod jako warstw. Może to być warstwy "web" i "dane" lub "Magazyn" warstwy. Aplikacje byłyby warstwy obsługi wiadomości, gdzie żądania przepływ i wylogowanie, a także zestaw maszyn dedykowane do buforowania. Każdej warstwa lub typ obciążenia miała określone maszyny do niego w wersji dedykowanej: kilka maszyn w wersji dedykowanej, serwery sieci web w kilku otrzymano bazy danych. Jeśli konkretny typ obciążenie spowodowane maszyny, służący do Uruchom zbyt gorących więcej komputerów w tej samej konfiguracji jest dodawana do tej warstwy. Jednak nie wszystkie obciążenia może być skalowana w poziomie tak łatwe — szczególnie z warstwą danych zwykle spowodowałoby zastąpienie maszyn o większych maszyn. Łatwe. Maszyna nie powiodło się, część ogólnej aplikacji był uruchamiany na mniejsze zdolności dopóki komputer może zostać przywrócona. Nadal dość proste (o ile nie zawsze przyjemne).

Teraz jednak zmienił world architektury usługi i oprogramowania. Jest bardziej popularne, że aplikacje wdrożyły projektowania skalowalnego w poziomie. Kompilowanie aplikacji za pomocą kontenerów lub mikrousług (lub obie) jest często. Teraz gdy nadal obowiązują tylko kilka maszyny, nich nie uruchomiony tylko jedno wystąpienie obciążenia. One może jeszcze działać wiele różnych obciążeń w tym samym czasie. Masz teraz wielu różnych typów usług (Brak korzystających z pełnej maszyny, przez które zasobów), być może setki różnych wystąpień tych usług. Każde wystąpienie nazwane ma jedną lub więcej wystąpień lub repliki dla wysokiej dostępności (HA). W zależności od wielkości tych obciążeń i obciążenia są one może okazać się samodzielnie z setkami lub tysiącami maszyn. 

Nie jest tak proste, jak zarządzanie komputerami kilka przeznaczonych do jednego rodzaju obciążenia nagle zarządzanie środowiskiem. Serwery wirtualne i już nie mieć nazwy (przełączono mindsets z [zwierząt domowych, aby bydła](http://www.slideshare.net/randybias/architectures-for-open-and-scalable-clouds/20) po wszystkich). Konfiguracja jest mniejsza o maszyn i więcej informacji na temat uwierzytelnienia usługi. Sprzęt, który jest przeznaczony do pojedynczego wystąpienia obciążenia to przede wszystkim Historia. Uwierzytelnienia usługi stały się małych systemów rozproszonych, obejmującej wiele mniejsze fragmenty sprzęcie masowym.

Ponieważ aplikacja nie jest już serię monoliths rozmieszczenie do kilku warstw, jest teraz dostępna do postępowania w przypadku wielu kombinacji więcej. Kto decyduje o tym, jakie rodzaje obciążeń można uruchamiać na jaki sprzęt, lub liczbę? Obciążeń, które działają na tym samym sprzęcie, a które są w konflikcie? Komputer przechodzi w dół jak sprawdzić, co w była uruchomiona na tym komputerze? Kto jest odpowiedzialny za zapewnienie, że obciążenie uruchamiana ponownie? Należy czekać maszyny (wirtualnej)?, aby wrócić do czy obciążeń automatycznie niepowodzeniem lub przez inne maszyny i kontynuuj działanie? Jest wymagane udziału człowieka? Co uaktualnień w tym środowisku?

Deweloperzy i operatory w tym środowisku chcemy wspomóc zarządzanie tym złożoności. Zatrudniania binge i próby neutralizują złożoność osobom prawdopodobnie nie jest prawidłowa odpowiedź, więc co możemy zrobić?

## <a name="introducing-orchestrators"></a>Wprowadzenie do orchestrators
"Orchestrator" to ogólny termin dla oprogramowania, które ułatwia administratorom zarządzanie środowiskach tego typu. Orchestrators są składnikami, które zająć w żądań, takich jak "Chcę pięć kopii tej usługi uruchomione w środowisku Mój." Próby wprowadzić środowisko odpowiada pożądanego stanu, niezależnie od tego, co się stanie.

Orchestrators (nie ludzi) są co podejmowania działań, gdy wystąpi awaria maszyny lub obciążenia kończy jakiegoś powodu nieoczekiwany. Większość orchestrators więcej niż tylko biznesowych w radzeniu sobie z błędem. Inne funkcje, które mają zarządzanym nowych wdrożeń, Obsługa uaktualniania i dotyczących zużycia zasobów i zarządzania. Wszystkie orchestrators są zasadniczo związane z konserwacją niektórych żądanego stanu konfiguracji w środowisku. Chcesz będzie wiadomo orchestrator, co ma i jego czy lifting ciężki. Aurora u góry Mesos, Docker Datacenter/Docker Swarm, Kubernetes i sieci szkieletowej usług należą do nich orchestrators. Te orchestrators są aktywnie rozwijane do potrzeb rzeczywistych obciążeń w środowisku produkcyjnym. 

## <a name="orchestration-as-a-service"></a>Orchestration jako usługa
Menedżer zasobów klastra to składnik systemu, który obsługuje aranżacji w sieci szkieletowej usług. Menedżer zasobów klastra zadania dzieli się na trzy części:

1. Wymuszanie zasad
2. Optymalizowanie środowiska
3. Ułatwienia z innymi procesami

Aby zobaczyć, jak Menedżer zasobów klastra działa, obejrzyj następujące wideo Microsoft Virtual Academy: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=d4tka66yC_5706218965">
<img src="./media/service-fabric-cluster-resource-manager-introduction/ConceptsAndDemoVid.png" WIDTH="360" HEIGHT="244">
</a></center>

### <a name="what-it-isnt"></a>Co to jest
Tradycyjny N warstwy aplikacji ma zawsze [modułu równoważenia obciążenia](https://en.wikipedia.org/wiki/Load_balancing_(computing)). Zazwyczaj jest to usługi równoważenia obciążenia sieciowego (NLB) lub aplikacji obciążenia równoważenia długopłetwy (ALB) w zależności od tego, gdzie NAS w stos sieciowy. Niektóre moduły równoważenia obciążenia są związane ze sprzętem jak firmy F5 BigIP oferty, inne są programowa takich jak Microsoft do równoważenia obciążenia Sieciowego. W innych środowiskach możesz zobaczyć coś jak HAProxy, nginx, Istio lub wysłannika w tej roli. W tych architektury zadanie równoważenia obciążenia jest upewnij się, że bezstanowe (około) otrzymywać samo pracy. Strategie równoważenia obciążenia zróżnicowane. Niektóre moduły równoważenia wyśle każdego innego wywołania do innego serwera. Udostępniane innym sesji przypinanie/lepkości. Bardziej zaawansowane równoważenia umożliwia oszacowania rzeczywiste obciążenie lub raportowania kierować połączenia na podstawie oczekiwanego kosztów i bieżące obciążenie maszyny.

Moduły równoważenia sieciowego czy routery komunikat próbował upewnij się, że warstwa sieci web/proces roboczy pozostaje około zrównoważonym. Strategie równoważenia warstwy danych były różne i zależnych na mechanizmie magazynu danych. Równoważenie warstwy danych zależał od dzielenia na fragmenty danych, buforowanie zarządzanych widoki, procedury składowane i innych mechanizmów specyficzne dla magazynu.

Niektóre z tych strategii są interesujące, Menedżer zasobów klastra sieci szkieletowej usług nie jest niczego takich jak usługa równoważenia obciążenia sieciowego lub pamięci podręcznej. Usługa równoważenia obciążenia sieciowego równoważy frontends przez rozłożenie frontends ruchu. Menedżer zasobów klastra sieci szkieletowej usług ma inną strategii. Zasadniczo, Service Fabric przenosi *usług* gdzie sensu najbardziej, oczekiwano ruchu lub obciążenia, które należy wykonać. Na przykład może ją przenieść usług węzłów, które są aktualnie zimnych, ponieważ usługi, które są nie robią dużo pracy. Węzły mogą być zimnych, ponieważ usługi, które były obecne zostały usunięte lub przeniesione w innym miejscu. Inny przykład Menedżera zasobów klastra można również przenosić usługi od komputera. Możliwe, że komputer jest zostaną uaktualnione, lub jest przeciążony z powodu kolekcji zużycie przez usługi działające na nim. Alernatively, wymagania dotyczące zasobów usługi może wzrosnąć. W związku z tym nie ma wystarczających zasobów na tym komputerze, aby kontynuować wykonywanie go. 

Menedżer zasobów klastra jest odpowiedzialny za przeniesienie usługom, zawiera zestaw różnych funkcji w porównaniu do czy pozyskane w Usługa równoważenia obciążenia sieciowego. Jest to spowodowane moduły równoważenia obciążenia sieciowego dostarczania ruchu sieciowego do, w którym usługi już są, nawet jeśli w tej lokalizacji nie jest idealnym rozwiązaniem do uruchamiania samej usługi. Menedżer zasobów klastra sieci szkieletowej usług zostają różni strategii za zapewnienie, że zasoby w klastrze są wydajnie wykorzystywane.

## <a name="next-steps"></a>Kolejne kroki
- Aby uzyskać informacje dotyczące przepływu architektury i informacji w ramach Menedżera zasobów klastra, zapoznaj się [w tym artykule ](service-fabric-cluster-resource-manager-architecture.md)
- Menedżer zasobów klastra ma wiele opcji opisujące klastra. Aby dowiedzieć się więcej na temat metryki, zapoznaj się w tym artykule na [opisujące klastra sieci szkieletowej usług](service-fabric-cluster-resource-manager-cluster-description.md)
- Aby uzyskać więcej informacji na temat konfigurowania usługi [informacje na temat konfigurowania usługi](service-fabric-cluster-resource-manager-configure-services.md)(service-fabric-cluster-resource-manager-configure-services.md)
- Metryki są zarządzaniu Menedżer zasobów klastra sieci szkieletowej usług konsumenckich i pojemności w klastrze. Aby dowiedzieć się więcej o metryki i sposobach ich konfigurowania wyewidencjonowanie [w tym artykule](service-fabric-cluster-resource-manager-metrics.md)
- Menedżer zasobów klastra współpracuje z możliwości zarządzania usługi sieć szkieletowa. Aby dowiedzieć się więcej na temat tej integracji, przeczytaj [w tym artykule](service-fabric-cluster-resource-manager-management-integration.md)
- Aby dowiedzieć się o jak Menedżer zasobów klastra zarządza i równoważy obciążenie w klastrze, zobacz artykuł na [równoważenia obciążenia](service-fabric-cluster-resource-manager-balancing.md)
