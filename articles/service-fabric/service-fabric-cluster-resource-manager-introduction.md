---
title: Wprowadzenie Menedżera zasobów klastra sieci szkieletowej usług
description: Dowiedz się więcej o Menedżerze zasobów klastra sieci szkieletowej usług, sposobie zarządzania aranżacją usług aplikacji.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: da9205f5d95eaf1b4dc655ee727ab8a4fe90893d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75563330"
---
# <a name="introducing-the-service-fabric-cluster-resource-manager"></a>Wprowadzenie menedżera zasobów klastra sieci szkieletowej usług
Tradycyjnie zarządzanie systemami informatycznymi lub usługami online oznaczało poświęcanie określonych maszyn fizycznych lub wirtualnych określonym usługom lub systemom. Usługi zostały zaprojektowane jako warstwy. Będzie warstwa "web" i warstwa "danych" lub "magazynowania". Aplikacje mają warstwę obsługi wiadomości, w której żądania płynęły i wychodzące, a także zestaw maszyn przeznaczonych do buforowania. Każda warstwa lub typ obciążenia miał dedykowane maszyny: baza danych dostała kilka maszyn poświęconych jej, kilka serwerów sieci web. Jeśli określony typ obciążenia spowodował maszyny, na których był uruchomiony zbyt gorąco, a następnie dodano więcej maszyn z tej samej konfiguracji do tej warstwy. Jednak nie wszystkie obciążenia mogą być skalowane w poziomie tak łatwo — szczególnie w warstwie danych, które zazwyczaj można zastąpić maszynami większymi maszynami. Łatwe w obsłudze. Jeśli maszyna nie powiodła się, ta część ogólnej aplikacji działała z mniejszą pojemnością, dopóki nie można było przywrócić komputera. Nadal dość łatwe (jeśli nie koniecznie zabawa).

Teraz jednak zmienił się świat architektury usług i oprogramowania. Jest bardziej powszechne, że aplikacje przyjęły projekt skalowaw wielkości w poziomie. Tworzenie aplikacji z kontenerów lub mikrousług (lub obu) jest wspólne. Teraz, gdy nadal masz tylko kilka maszyn, nie są one uruchomione tylko jedno wystąpienie obciążenia. Mogą nawet działać wiele różnych obciążeń w tym samym czasie. Masz teraz dziesiątki różnych typów usług (żaden z nich nie zużywa pełnej maszyny wartości zasobów), być może setki różnych wystąpień tych usług. Każde nazwane wystąpienie ma co najmniej jedno wystąpienie lub repliki dla wysokiej dostępności (HA). W zależności od rozmiarów tych obciążeń i ich zajętych, możesz znaleźć się na setkach lub tysiącach maszyn. 

Nagłe zarządzanie środowiskiem nie jest tak proste, jak zarządzanie kilkoma maszynami dedykowanymi do pojedynczych typów obciążeń. Twoje serwery są wirtualne i nie mają już nazw (w końcu zmieniłeś nastawienie ze [zwierząt domowych na bydło).](https://www.slideshare.net/randybias/architectures-for-open-and-scalable-clouds/20) Konfiguracja jest mniej o maszynach, a więcej o samych usługach. Sprzęt, który jest przeznaczony do pojedynczego wystąpienia obciążenia jest w dużej mierze przeszłością. Same usługi stały się małymi systemami rozproszonymi, które obejmują wiele mniejszych elementów sprzętu towarowego.

Ponieważ aplikacja nie jest już serią monolitów rozłożonych na kilka warstw, masz teraz wiele innych kombinacji do czynienia. Kto decyduje, jakie typy obciążeń można uruchamiać na jakim sprzęcie lub ile? Które obciążenia działają dobrze na tym samym sprzęcie, a które są w konflikcie? Kiedy maszyna idzie w dół, skąd wiesz, co tam działało na tej maszynie? Kto jest odpowiedzialny za upewnienie się, że obciążenie zacznie działać ponownie? Czy czekasz na powrót maszyny (wirtualnej?), czy twoje obciążenia automatycznie przesunie się w tryb fail over na innych komputerach i będzie działać dalej? Czy konieczna jest interwencja człowieka? Co z uaktualnieniami w tym środowisku?

Jako deweloperzy i operatorzy zajmujący się w tym środowisku będziemy chcieli pomóc w zarządzaniu tą złożonością. Zatrudnianie binge i próbuje ukryć złożoność z ludźmi prawdopodobnie nie jest właściwą odpowiedzią, więc co robimy?

## <a name="introducing-orchestrators"></a>Przedstawiamy koordynatorów
"Orchestrator" to ogólny termin dla oprogramowania, który pomaga administratorom zarządzać tego typu środowiskami. Orchestrators są składniki, które podejmują w żądaniach, takich jak "Chciałbym pięć kopii tej usługi działa w moim środowisku." Starają się, aby środowisko odpowiadało żądanemu stanowi, bez względu na to, co się stanie.

Orchestrators (nie ludzi) są co podjąć działania, gdy komputer ulegnie awarii lub obciążenia kończy się z jakiegoś nieoczekiwanego powodu. Większość orkiestratorów robi więcej niż tylko radzi sobie z porażką. Inne funkcje, które mają, to zarządzanie nowymi wdrożeniami, obsługa uaktualnień i radzenie sobie z zużyciem zasobów i zarządzaniem. Wszystkie orchestrators są zasadniczo o zachowaniu pewnego pożądanego stanu konfiguracji w środowisku. Chcesz być w stanie powiedzieć orkiestrator, co chcesz i mieć to zrobić podnoszenia ciężkich. Aurora na górze Mesos, Docker Datacenter/Docker Swarm, Kubernetes i Service Fabric są wszystkie przykłady orchestrators. Te orchestrators są aktywnie rozwijane w celu zaspokojenia potrzeb rzeczywistych obciążeń w środowiskach produkcyjnych. 

## <a name="orchestration-as-a-service"></a>Aranżacja jako usługa
Menedżer zasobów klastra jest składnikiem systemu, który obsługuje aranżacji w sieci szkieletowej usług. Zadanie Menedżera zasobów klastra jest podzielone na trzy części:

1. Wymuszanie reguł
2. Optymalizacja środowiska
3. Pomoc w innych procesach

### <a name="what-it-isnt"></a>Co to nie jest
W tradycyjnych aplikacjach warstwy N zawsze istnieje [moduł równoważenia obciążenia.](https://en.wikipedia.org/wiki/Load_balancing_(computing)) Zazwyczaj był to moduł równoważenia obciążenia sieciowego (NLB) lub moduł równoważenia obciążenia aplikacji (ALB) w zależności od tego, gdzie znajdował się w stosie sieciowym. Niektóre moduły równoważenia obciążenia są oparte na sprzęcie, takie jak oferta BigIP F5, inne są oparte na oprogramowaniu, takie jak równoważenie obciążenia sieciowego firmy Microsoft. W innych środowiskach może pojawić się coś takiego jak HAProxy, nginx, Istio lub Wysłannik w tej roli. W tych architekturach zadanie równoważenia obciążenia jest zapewnienie obciążeń bezstanowych otrzymują (w przybliżeniu) taką samą ilość pracy. Strategie równoważenia obciążenia były zróżnicowane. Niektóre balanseri wysyłałyby każde inne wywołanie do innego serwera. Inne pod warunkiem sesji przypinanie / lepkość. Bardziej zaawansowane moduły balansujące używają rzeczywistego szacowania obciążenia lub raportowania do kierowania wywołania na podstawie oczekiwanego kosztu i bieżącego obciążenia maszyny.

Balanseri sieciowe lub routery wiadomości starały się zapewnić, że warstwa sieci web/proces robocza pozostała z grubsza zrównoważona. Strategie równoważenia warstwy danych były różne i zależały od mechanizmu przechowywania danych. Równoważenie warstwy danych opierało się na dzieleniu na fragmenty danych, buforowaniu, widokach zarządzanych, procedurach przechowywanych i innych mechanizmach specyficznych dla magazynu.

Chociaż niektóre z tych strategii są interesujące, Menedżer zasobów klastra sieci szkieletowej usług nie jest niczym jak moduł równoważenia obciążenia sieciowego lub pamięć podręczna. Moduł równoważenia obciążenia sieciowego równoważy frontendy, rozmieszczając ruch między frontendami. Menedżer zasobów klastra sieci szkieletowej usług ma inną strategię. Zasadniczo sieci szkieletowej usług przenosi *usługi* do miejsca, w którym mają największy sens, oczekując ruchu lub obciążenia do naśladowania. Na przykład może przenieść usługi do węzłów, które są obecnie zimne, ponieważ usługi, które tam są, nie wykonują dużo pracy. Węzły mogą być zimne, ponieważ usługi, które były obecne zostały usunięte lub przeniesione w innym miejscu. Innym przykładem Menedżer zasobów klastra może również przenieść usługę z komputera. Być może maszyna ma zostać uaktualniona lub jest przeciążony ze względu na skok zużycia przez usługi uruchomione na nim. Alternatywnie wymagania dotyczące zasobów usługi mogły wzrosnąć. W rezultacie nie ma wystarczających zasobów na tym komputerze, aby kontynuować jego uruchamianie. 

Ponieważ Menedżer zasobów klastra jest odpowiedzialny za przenoszenie usług, zawiera inny zestaw funkcji w porównaniu do tego, co można znaleźć w modułu równoważenia obciążenia sieciowego. Dzieje się tak, ponieważ moduły równoważenia obciążenia sieciowego dostarczają ruch sieciowy do miejsca, w którym usługi już są, nawet jeśli ta lokalizacja nie jest idealna do uruchamiania samej usługi. Menedżer zasobów klastra sieci szkieletowej usług wykorzystuje zasadniczo różne strategie w celu zapewnienia, że zasoby w klastrze są efektywnie wykorzystywane.

## <a name="next-steps"></a>Następne kroki
- Aby uzyskać informacje na temat architektury i przepływu informacji w Menedżerze zasobów klastra, zapoznaj się z [tym artykułem](service-fabric-cluster-resource-manager-architecture.md)
- Menedżer zasobów klastra ma wiele opcji opisu klastra. Aby dowiedzieć się więcej o metrykach, zapoznaj się z tym [artykułem opisującym klaster sieci szkieletowej usług](service-fabric-cluster-resource-manager-cluster-description.md)
- Aby uzyskać więcej informacji na temat konfigurowania usług, [dowiedz się więcej o konfigurowaniu usług](service-fabric-cluster-resource-manager-configure-services.md)
- Metryki są jak menedżer zasobów klastra sieci szkieletowej usług zarządza zużyciem i pojemnością w klastrze. Aby dowiedzieć się więcej o danych i jak je skonfigurować, zapoznaj się z [tym artykułem](service-fabric-cluster-resource-manager-metrics.md)
- Menedżer zasobów klastra współpracuje z możliwościami zarządzania sieci szkieletowej usług. Aby dowiedzieć się więcej o tej integracji, przeczytaj [ten artykuł](service-fabric-cluster-resource-manager-management-integration.md)
- Aby dowiedzieć się, jak Menedżer zasobów klastra zarządza i równoważy obciążenie w klastrze, zapoznaj się z artykułem na temat [równoważenia obciążenia](service-fabric-cluster-resource-manager-balancing.md)
