---
title: Planowanie wdrażania klastrów usługi Azure Service Fabric | Dokumentacja firmy Microsoft
description: Więcej informacji na temat planowania i przygotowania do wdrożenia klastra usługi Service Fabric na platformie Azure w środowisku produkcyjnym.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: aljo
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/20/2019
ms.author: ryanwi
ms.openlocfilehash: d29f9af4201eee41720fe60cc9b7a21abc7fc63d
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58450065"
---
# <a name="plan-and-prepare-for-a-cluster-deployment"></a>Planowanie i przygotowanie do wdrożenia klastra

Planowanie i przygotowanie do wdrożenia klastra produkcyjnego jest bardzo ważne.  Istnieje wiele czynników, które należy wziąć pod uwagę.  Ten artykuł zawiera opis kroków przygotowania wdrożenia klastra.

## <a name="read-the-best-practices-information"></a>Odczytywanie informacji o najlepszych rozwiązań
Aby zarządzać aplikacjami usługi Azure Service Fabric i klastrami pomyślnie, istnieją operacje, zdecydowanie zalecamy należy wykonać, aby zoptymalizować niezawodności w środowisku produkcyjnym.  Aby uzyskać więcej informacji, przeczytaj [najważniejsze wskazówki dotyczące aplikacji usługi Service Fabric i klastra](service-fabric-best-practices-overview.md).

## <a name="select-the-os-for-the-cluster"></a>Wybierz system operacyjny klastra
Usługa Service Fabric umożliwia tworzenie klastrów usługi Service Fabric na wszystkich maszynach wirtualnych lub komputerach z systemem Windows Server lub Linux.  Przed wdrożeniem klastra, musisz wybrać system operacyjny:  Windows lub Linux.  Każdy węzeł w klastrze (maszyna wirtualna) działa ten sam system operacyjny, nie można mieszać Windows i maszyn wirtualnych systemu Linux, które znajdują się w tym samym klastrze.

## <a name="capacity-planning"></a>Planowanie pojemności
Dla wszystkich wdrożeń produkcyjnych planowania pojemności jest ważnym krokiem. Poniżej przedstawiono niektóre zagadnienia, które należy uwzględnić w ramach tego procesu.

* Początkowa liczba typów węzłów klastra 
* Właściwości każdego typu węzła (rozmiar, liczbę wystąpień podstawowy, połączenie z Internetem, liczba maszyn wirtualnych itd.)
* Charakterystyka niezawodności i trwałości klastra

### <a name="select-the-initial-number-of-node-types"></a>Wybierz początkowa liczba typów węzłów
Najpierw należy ustalić, co się dzieje służący do klastra, którą tworzysz. Jakiego rodzaju aplikacje, które planujesz wdrożyć do tego klastra? Aplikacja ma wiele usług i czy któryś z nich muszą być publiczne lub połączone z Internetem? Usługi (wchodzące w skład aplikacji), czy mają potrzeb różnych infrastruktury, takich jak więcej pamięci RAM lub cykle procesora CPU wyższa? Klaster usługi Service Fabric może zawierać więcej niż jeden typ węzła: typ węzła podstawowego i jeden lub więcej typów węzłów innych niż podstawowe. Każdy typ węzła jest mapowany do zestawu skalowania maszyn wirtualnych. Następnie każdy typ węzła może być niezależnie skalowany w górę lub w dół oraz może mieć różne zestawy otwartych portów i różne metryki pojemności. [Właściwości węzła i ograniczeniami dotyczącymi umieszczania] [ placementconstraints] można skonfigurować, aby ograniczyć określone usługi do typów określonego węzła.  Aby uzyskać więcej informacji, przeczytaj [liczba typów węzłów klastra musi zaczynać](service-fabric-cluster-capacity.md#the-number-of-node-types-your-cluster-needs-to-start-out-with).

### <a name="select-node-properties-for-each-node-type"></a>Wybierz właściwości węzła dla każdego typu węzła
Typy węzłów definiują jednostki SKU maszyny Wirtualnej, liczbę i właściwości maszyn wirtualnych w zestawie skalowania skojarzone.

Minimalny rozmiar maszyn wirtualnych dla każdego typu węzła jest określany przez [warstwa trwałości] [ durability] wybierz dla typu węzła.

Minimalna liczba maszyn wirtualnych dla typu węzła podstawowego zależy od [warstwy niezawodności] [ reliability] wybierzesz.

Zobacz minimalnych zaleceń dotyczących [typy węzła podstawowego](service-fabric-cluster-capacity.md#primary-node-type---capacity-guidance), [są to obciążenia stanowe na typy węzłów innych niż podstawowe](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateful-workloads), i [bezstanowych obciążeń w ramach typów węzłów innych niż podstawowe](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateless-workloads). 

Żadnych innych niż minimalna liczba węzłów powinna być oparta na liczbie replik aplikacji/usługi, które mają być uruchamiane w tego typu węzła.  [Planowanie wydajności dla aplikacji usługi Service Fabric](service-fabric-capacity-planning.md) pomaga oszacować zasoby, których potrzebujesz do uruchomienia aplikacji. Można zawsze skalowanie klastra w górę lub dół później, aby dopasować do zmiany obciążenia aplikacji. 

### <a name="select-the-durability-and-reliability-levels-for-the-cluster"></a>Wybierz poziomy trwałości i niezawodności klastra
Warstwa trwałości jest używany do wskazania systemowi uprawnienia, których maszyn wirtualnych z podstawową infrastrukturą platformy Azure. W typie podstawowym węzłem to uprawnienie pozwala usłudze Service Fabric wstrzymać wszelkie wyświetlania żądania z poziomu infrastruktury maszyny Wirtualnej (na przykład ponowne uruchomienie maszyny Wirtualnej, odtworzenia z obrazu maszyny Wirtualnej lub migracji maszyny Wirtualnej) mieć wpływ na wymagania dotyczące kworum dla usług systemu i usługi stanowej. Typy węzłów innych niż podstawowe to uprawnienie umożliwia usługi Service Fabric można wstrzymać maszyny Wirtualnej infrastruktury poziomu żądań (na przykład ponowne uruchomienie maszyny Wirtualnej, odtworzenia z obrazu maszyny Wirtualnej i migracji maszyn wirtualnych), które wpływają na wymagania dotyczące kworum dla usług stanowych.  Aby uzyskać korzyści wynikające z różnymi poziomami i zalecenia dotyczące poziomu, który i kiedy, zobacz [charakterystyki trwałości klastra][durability].

Aby ustawić liczbę replik usług systemowych, które mają być uruchamiane w tym klastrze na podstawowy typ węzła jest używana warstwa niezawodności. Zwiększenie liczby replik, bardziej niezawodne usługi systemowe znajdują się w klastrze.  Aby uzyskać korzyści wynikające z różnymi poziomami i zalecenia dotyczące poziomu, który i kiedy, zobacz [charakterystyka niezawodności klastra][reliability]. 

## <a name="enable-reverse-proxy-andor-dns"></a>Włącz zwrotny serwer proxy i/lub DNS
Łączenie ze sobą ogólnie wewnątrz klastra usługi można uzyskać dostęp do punktów końcowych w innych usługach ponieważ węzłów w klastrze znajdują się w tej samej sieci lokalnej. Aby ułatwić połączenia między usługami, Usługa Service Fabric udostępnia dodatkowe usługi: A [usługi DNS](service-fabric-dnsservice.md) i [odwrotnego serwera proxy usługi](service-fabric-reverseproxy.md).  Obie te usługi można włączyć w przypadku wdrażania klastra.

Od wielu usług zwłaszcza konteneryzowanych usług może mieć nazwę istniejącego adresu URL, możliwość je rozwiązać przy użyciu standardowego systemu DNS protokołu (zamiast protokołu nazewnictwa Service) jest wygodne, szczególnie w przypadku scenariuszy aplikacji "metodą lift and shift". Jest to dokładnie, jak działa usługa DNS. Umożliwia mapowanie nazw DNS na nazwę usługi i dlatego rozpoznawać adresy IP punktów końcowych.

Zwrotny serwer proxy dotyczy usług w klastrze, które uwidaczniają punkty końcowe HTTP (w tym protokołu HTTPS). Zwrotny serwer proxy znacznie upraszcza wywoływania innych usług, podając w określonym formacie identyfikatora URI.  Zwrotny serwer proxy obsługuje także rozpoznania, łączenie i ponów próbę wykonania kroków wymaganych do jednej usługi do komunikowania się z inną.

## <a name="prepare-for-disaster-recovery"></a>Przygotowywanie do odzyskiwania po awarii
Kluczową częścią dostarczania wysokiej dostępności jest zapewnienie, że usługi mogą przetrwać różne rodzaje błędów. Jest to szczególnie ważne w przypadku awarii, znajdujących się niezaplanowane i poza Twoją kontrolą. [Przygotowywanie do odzyskiwania awaryjnego](service-fabric-disaster-recovery.md) opisano niektóre typowe trybów awarii, które mogłyby zostać awarii, jeśli nie modelowane i zarządzane prawidłowo. Omówiono w nim również środki zaradcze i akcje do wykonania w przypadku awarii mimo to się stało.

## <a name="production-readiness-checklist"></a>Lista kontrolna gotowości do produkcji
Jest gotowy do zastąpienia jest przesyłany ruch produkcyjny aplikacji i klastra? Przed wdrożeniem klastra do środowiska produkcyjnego, wykonaj [Lista kontrolna gotowości produkcji](service-fabric-production-readiness-checklist.md). Zachowaj aplikacji i klastra sprawnego dzięki pracy przez elementy na tej liście kontrolnej. Zdecydowanie zaleca się te elementy powinny zostać zaznaczone przed przejściem do środowiska produkcyjnego.

## <a name="next-steps"></a>Kolejne kroki
* [Tworzenie klastra usługi Service Fabric z systemem Windows](service-fabric-best-practices-overview.md)
* [Tworzenie klastra usługi Service Fabric z systemem Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md)

[placementconstraints]: service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints
[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster