---
title: Wprowadzenie do Menedżera zasobów klastra usługi Service Fabric | Dokumentacja firmy Microsoft
description: Wprowadzenie do usługi Service Fabric Menedżer zasobów klastra.
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: cfab735b-923d-4246-a2a8-220d4f4e0c64
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: e9b1cc8b66be36a0a77118f4de672c9411433ba5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60743665"
---
# <a name="introducing-the-service-fabric-cluster-resource-manager"></a>Wprowadzenie do Menedżer zasobów klastra usługi Service Fabric
Tradycyjnie Zarządzanie systemów informatycznych lub usługi online polegało na dedykowanym określonych fizycznych lub maszyn wirtualnych do tych określonych usług lub systemów. Usługi zostały zaprojektowana jako warstwy. Może to być warstwy "Internet" i "dane" lub "Magazyn" warstwy. Aplikacje będą mieć warstwa obsługi komunikatów, gdzie żądania przepływ wewnątrz i na zewnątrz, a także zestaw maszyn dedykowanego dla usługi pamięć podręczna. Każdą warstwę lub typu obciążenia ma określonych maszyn do niego w wersji dedykowanej: kilka maszyn w wersji dedykowanej, serwery sieci web w kilka stało się bazy danych. Określonego typu obciążenie spowodowane maszyn, które było do uruchomić zbyt gorąca, a następnie dodać więcej maszyn w tej samej konfiguracji dla tej warstwy. Jednak nie wszystkie obciążenia może być skalowana w poziomie tak łatwe — szczególnie z warstwą danych zazwyczaj spowodowałoby zastąpienie maszyn z większych maszyn. Łatwe. Maszyna nie powiodło się, część cała aplikacja był uruchamiany niższe osiągnięto maksymalną dopóki komputer może zostać przywrócona. Nadal dość proste (o ile nie zawsze przyjemne).

Teraz jednak świata architektury i oprogramowaniu został zmieniony. Jest to bardziej powszechne, czy aplikacje zostały przyjęte projektowania skalowalnego w poziomie. Tworzenie aplikacji za pomocą kontenerów lub mikrousług (lub obie) jest powszechne. Teraz gdy nadal masz tylko kilka maszyny, nie działają pojedynczego wystąpienia obciążenia. One może jeszcze działać wiele różnych obciążeń w tym samym czasie. Masz teraz dziesiątki różnego rodzaju usług (Brak wykorzystywanie pełnego maszyny, przez które zasoby), być może setki różnych wystąpień tych usług. Każde wystąpienie nazwane ma jedną lub więcej wystąpień lub replik dla wysokiej dostępności (HA). W zależności od wielkości tych obciążeń i ich obciążenia są może okazać się samodzielnie z setek lub tysięcy maszyn. 

Nagle zarządzania środowiskiem nie jest tak proste, jak zarządzanie komputerami kilka przeznaczonych dla pojedynczych typów obciążeń. Serwery wirtualne oraz nie będzie już nazwy (nastąpiło przełączenie mindsets z [zwierząt domowych, aby bydła](https://www.slideshare.net/randybias/architectures-for-open-and-scalable-clouds/20) po wszystkich). Konfiguracja jest mniej o maszyn i więcej informacji na temat uwierzytelnienia usługi. Sprzęt, który jest przeznaczony do pojedynczego wystąpienia, obciążenia jest głównie Historia. Uwierzytelnienia usługi stały się małych systemów rozproszonych, obejmujących wiele mniejsze kawałki standardowego sprzętu.

Ponieważ aplikacja nie jest już szereg monolitycznych projektów rozmieszczone w kilku warstwach, masz teraz liczbę kombinacji więcej radzenia sobie z. Kto decyduje, co typów obciążeń, można uruchomić na sprzęcie, który, lub ile? Obciążeń, które działają na tym samym sprzęcie, a które powodują konflikt? Maszyna przechodzi, w jaki sposób w dół wiedzieć, co zostało uruchomione są na nim na danym komputerze? Kto jest odpowiedzialny za zagwarantowanie, że obciążenie ponownym uruchomieniu? Poczekać maszyny (wirtualny?), aby wrócić do lub obciążeń automatyczne przełączenie do innych maszyn i Zachowaj uruchomiony? Jest interwencji człowieka, wymagane? Jak wygląda uaktualnienia, w tym środowisku?

Jako deweloperów i operatorów w tym środowisku My zamierzamy chcesz uzyskać pomoc w zarządzaniu tę złożoność. Zatrudniania binge i podjęcie próby ukrycie złożoności osobom prawdopodobnie nie jest prawidłowa odpowiedź, więc co możemy zrobić?

## <a name="introducing-orchestrators"></a>Wprowadzenie do koordynatorów
"Orchestrator" jest ogólnym terminem dla fragmentu oprogramowanie, które umożliwia administratorom zarządzanie tych typów środowisk. Koordynatorów są składniki, które przyjmują w żądaniach, takie jak "Chcę ta usługa zostanie uruchomiona w środowisku Moje pięć kopii." Użytkownik próbuje wprowadzić środowisko zgodne żądanego stanu, niezależnie od tego, co się stanie.

Koordynatorów (nie ludzi) to, co podejmowania działań, gdy maszyna nie powiedzie się lub kończy się obciążenie przyczyny nieoczekiwanego. Większość koordynatorów więcej niż tylko zajmuje się awarii. Inne funkcje, które mają w zarządzanym nowych wdrożeń obsługi uaktualnienia i dotyczących zużycia zasobów i zarządzanie. Wszystkich koordynatorów zasadniczo są związane z konserwacją niektóre żądanego stanu konfiguracji w środowisku. Należy ustalić koordynatora, co a jego pozostaw trudne zadania. Aurora na podstawie Mesos, rozwiązania Docker Datacenter/Docker Swarm, Kubernetes i usługi Service Fabric należą do nich koordynatorów. Te koordynatorów aktywnie opracowywane są do potrzeb obciążeń rzeczywistych w środowiskach produkcyjnych. 

## <a name="orchestration-as-a-service"></a>Organizowanie jako usługa
Menedżer zasobów klastra jest składnikiem systemu, który zajmuje się organizowaniem w usłudze Service Fabric. Menedżer zasobów klastra zadania został podzielony na trzy części:

1. Wymuszanie reguł
2. Optymalizowanie środowiska
3. Pomoc w jak najlepszym z innymi procesami

### <a name="what-it-isnt"></a>Co to jest
W tradycyjnych N warstwy aplikacji ma zawsze [modułu równoważenia obciążenia](https://en.wikipedia.org/wiki/Load_balancing_(computing)). Zazwyczaj jest to moduł równoważenia obciążenia sieciowego (NLB) lub aplikacji obciążenia równoważenia (ALB) w zależności od tego, gdzie Sob w stosu sieciowego. Niektóre moduły równoważenia obciążenia sprzętowej jak firmy F5 BigIP oferty, inne są programowej takich jak Microsoft przez równoważenie obciążenia Sieciowego. W innych środowiskach można napotkać coś takich jak HAProxy, nginx, Istio lub usługa Envoy w tej roli. W tych architektur zadania równoważenia obciążenia jest upewnij się, że bezstanowe (około) otrzymywać tej samej ilości pracy. Strategie dotyczące równoważenia obciążenia zróżnicowane. Niektóre moduły równoważenia wysyłane każde wywołanie innego na innym serwerze. Inne podany przypinanie/lepkości sesji. Bardziej zaawansowane równoważenia umożliwia szacowania rzeczywistego obciążenia lub raportowania kierowanie wywołań, na podstawie jego oczekiwane koszty i bieżące obciążenie maszyny.

Równoważenia sieciowego czy routery komunikat próbował upewnij się, że warstwa sieci web/proces roboczy pozostaje około o zrównoważonym obciążeniu. Strategie równoważenia warstwy danych były różne i zależne na mechanizmie magazynu danych. Równoważenie warstwy danych opiera się na dzielenie danych na fragmenty, buforowanie, zarządzanych widoki, procedury składowane i innych mechanizmów specyficzne dla magazynu.

Mimo że niektóre z tych strategii interesujące, Menedżer zasobów klastra usługi Service Fabric nie jest żadnych takich jak moduły równoważenia obciążenia sieciowego lub pamięci podręcznej. Moduł równoważenia obciążenia sieciowego równoważy frontonów przez rozłożenie ruchu na frontonów. Menedżer zasobów klastra usługi Service Fabric ma innych strategii. Zasadniczo, przenosi usługi Service Fabric *usług* gdzie sens najbardziej, oczekiwano ruchu lub załadować do wykonania. Na przykład: to przeniesienie usług do węzłów, które są aktualnie zimne, ponieważ usługi, które są nie robią dużo pracy. Węzły mogą być zimne, ponieważ usługi, które były obecne zostały usunięte lub przeniesione w innym miejscu. Inny przykład Menedżer zasobów klastra można również przenosić usługi od maszyny. Być może jest maszyny zostaną uaktualnione lub jest przeciążony ze względu na wzrost użycia przez usługi działające na nim. Alternatywnie wymagania dotyczące zasobów usługi może wzrosnąć. W rezultacie nie ma wystarczających zasobów na tej maszynie, nadal z niego korzystać. 

Ponieważ Menedżer zasobów klastra jest odpowiedzialny za przesuwanie usług Buduj wokół, zawiera zestaw funkcji w porównaniu do będzie pozyskane w moduł równoważenia obciążenia sieciowego. Jest to spowodowane usługą równoważenia obciążenia sieci dostarczania ruchu sieciowego do, w którym usługi już są, nawet jeśli w tej lokalizacji nie jest idealnym rozwiązaniem do uruchamiania usługi. Menedżer zasobów klastra usługi Service Fabric wykorzystuje całkowicie różne strategie za zapewnienie, że zasoby w klastrze wydajne są wykorzystywane.

## <a name="next-steps"></a>Kolejne kroki
- Aby uzyskać informacji na temat architektury i przepływem informacji w ramach Menedżer zasobów klastra, zapoznaj się [w tym artykule](service-fabric-cluster-resource-manager-architecture.md)
- Menedżer zasobów klastra ma wiele opcji do opisywania klastra. Aby dowiedzieć się więcej o metrykach, obejrzyj ten artykuł w witrynie [opisujące klaster usługi Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)
- Aby uzyskać więcej informacji na temat konfigurowania usług [Dowiedz się więcej na temat konfigurowania usługi](service-fabric-cluster-resource-manager-configure-services.md)
- Metryki są, jak Menedżer zasobów usługi Service Fabric klaster zarządza użycia i pojemności w klastrze. Aby dowiedzieć się więcej na temat metryk i sposobach ich konfigurowania, zapoznaj się [w tym artykule](service-fabric-cluster-resource-manager-metrics.md)
- Menedżer zasobów klastra współpracuje z funkcji zarządzania usługi Service Fabric. Aby dowiedzieć się więcej na temat tej integracji, przeczytaj [w tym artykule](service-fabric-cluster-resource-manager-management-integration.md)
- Aby dowiedzieć się o zarządza Menedżer zasobów klastra i równoważy obciążenie w klastrze, zapoznaj się z artykułem na [równoważenia obciążenia](service-fabric-cluster-resource-manager-balancing.md)
