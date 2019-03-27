---
title: Usługa Azure skalowanie klastra usługi Service Fabric | Dokumentacja firmy Microsoft
description: Dowiedz się o skalowanie klastrów usługi Azure Service Fabric w lub się i górę lub w dół.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: aljo
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/13/2018
ms.author: aljo
ms.openlocfilehash: 5caae0a354ba0d68a9c089d2604acee96aa0128b
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58499832"
---
# <a name="scaling-azure-service-fabric-clusters"></a>Klastry skalowania usługi Azure Service Fabric
Klaster usługi Service Fabric to zbiór połączonych z siecią maszyn wirtualnych lub fizycznych, w których mikrousługi są wdrażania i zarządzania nimi. Komputer lub maszynę Wirtualną, która jest częścią klastra, jest nazywana węzłem. Klastry mogą zawierać potencjalnie tysiącach węzłów. Po utworzeniu klastra usługi Service Fabric można skalować klastra w poziomie (zmienić liczbę węzłów), czy w pionie (zmienić zasoby węzłów).  Możesz skalować klastra w dowolnym momencie, nawet gdy działają obciążenia w klastrze.  Jak jest skalowana w klastrze, aplikacje będą skalowane automatycznie również.

Dlaczego skalowanie klastra? Zmień zapotrzebowania aplikacji wraz z upływem czasu.  Może być konieczne zwiększenie zasobów klastra aplikacji zwiększenia obciążenia lub sieci natężenia ruchu lub zmniejszanie zasobów klastra, gdy zapotrzebowanie maleje.

## <a name="scaling-in-and-out-or-horizontal-scaling"></a>Skalowanie do wewnątrz i na zewnątrz lub skalowanie w poziomie
Umożliwia zmianę liczby węzłów w klastrze.  Gdy nowe węzły dołączą do klastra, [Menedżer zasobów klastra](service-fabric-cluster-resource-manager-introduction.md) przenosi usług do nich, co zmniejsza obciążenie istniejących węzłów.  Jeśli klaster nie jest efektywne wykorzystanie zasobów, można także zmniejszyć liczbę węzłów.  Jako węzły klastra należy pozostawić, usługi odejścia od tych węzłów i obciążenia w pozostałych węzłach.  Zmniejszenie liczby węzłów w klastrze, działające na platformie Azure może zaoszczędzić pieniądze, ponieważ płacisz za liczbę maszyn wirtualnych możesz użycia i nie obciążenie na tych maszynach wirtualnych.  

- Zalety: Nieskończone możliwości skalowania, teoretycznie.  Jeśli aplikacja jest przeznaczona dla skalowalność, nieograniczone możliwości wzrostu można włączyć poprzez dodanie kolejnych węzłów.  Narzędzia w środowiskach chmur ułatwia dodawanie lub usuwanie węzłów, dzięki czemu można łatwo dostosować pojemność i zapłacisz tylko za wykorzystane zasoby.  
- Wady: Aplikacje muszą być [przeznaczoną do skalowania](service-fabric-concepts-scalability.md).  Bazy danych aplikacji i trwałości mogą wymagać dodatkowej pracy architektury do skalowania, jak również.  [Elementy Reliable collections](service-fabric-reliable-services-reliable-collections.md) w stanowej usługi Service Fabric, jednak znacznie ułatwić skalowanie danych aplikacji.

Zestawy skalowania maszyn wirtualnych to zasób obliczeniowy systemu Azure, która umożliwia wdrażanie i zarządzanie kolekcją maszyn wirtualnych jako zestawu. Każdy typ węzła, który jest zdefiniowany w klastrze platformy Azure jest [konfigurowany jako zestaw skalowania oddzielnych](service-fabric-cluster-nodetypes.md). Każdy typ węzła może następnie być skalowana w lub się niezależnie od siebie, mają różne zestawy otwartych portów i może mieć różne metryki pojemności. 

Podczas skalowania w klastrze platformy Azure, należy przestrzegać następujących wytycznych:
- Typy węzła podstawowego obciążeń produkcyjnych zawsze powinien mieć co najmniej pięć węzłów.
- typy węzłów innych niż podstawowe, obciążeń stanowych produkcyjnych zawsze powinien mieć co najmniej pięć węzłów.
- typy węzłów innych niż podstawowe, obciążeń produkcyjnych o bezstanowa zawsze powinien mieć co najmniej dwa węzły.
- Dowolny typ węzła [poziom trwałości](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) Gold i Silver zawsze powinien mieć co najmniej pięć węzłów.
- Usuń losowe wystąpień maszyny Wirtualnej/węzły z typu węzła, nie zawsze używaj skalowania zestawu skalowania maszyn wirtualnych szczegółów funkcji. Usuwanie losowe wystąpień maszyn wirtualnych może niekorzystnie wpłynąć na możliwość systemów prawidłowo równoważenia obciążenia.
- Jeśli przy użyciu reguł skalowania automatycznego, należy ustawić zasady tak, aby skalowania w (usuwania wystąpień maszyn wirtualnych) odbywa się jednym węźle naraz. Skalowanie w dół więcej niż jedno wystąpienie w danym momencie nie jest bezpieczne.

Ponieważ typy węzłów usługi Service Fabric w klastrze składają się z zestawów skalowania maszyn wirtualnych na zapleczu, użytkownik może [Konfigurowanie reguł skalowania automatycznego lub ręczne skalowanie](service-fabric-cluster-scale-up-down.md) zestawu skalowania maszyn typu/wirtualnego każdego węzła.

### <a name="programmatic-scaling"></a>Programowe skalowania
W wielu scenariuszach [skalowania klastra, ręcznie lub za pomocą reguł automatycznego skalowania](service-fabric-cluster-scale-up-down.md) są dobre rozwiązania. Dla bardziej zaawansowanych scenariuszy jednak mogą nie być odpowiednie. Wady na te metody obejmują:

- Ręczne skalowanie wymaga Zaloguj się i jawne żądanie operacji skalowania. Operacje skalowania są często wymagane lub w czasie nieprzewidywalne, to podejście może nie być dobrym rozwiązaniem.
- Gdy reguł skalowania automatycznego usuwania wystąpienia zestawu skalowania maszyn wirtualnych, ich są automatycznie usuwane wiedzę na temat tego węzła ze skojarzonego klastra usługi Service Fabric, chyba że typ węzła ma poziom niezawodności na poziomie Silver lub Gold. Ponieważ reguł skalowania automatycznego działa na dużą skalę, Ustaw poziom (a nie na poziomie usługi Service Fabric), reguł skalowania automatycznego można usunąć węzłów usługi Service Fabric, bez zamykania bezpiecznie. Usunięcie węzła prosta zostaną pozostawione "ghost" stanu węzła usługi Service Fabric po operacji skalowania na zewnątrz. Aby okresowo oczyszczać stan usunięto węzła w klastrze usługi Service Fabric należałoby osobę (lub usługi).
- Typ węzła, z poziomu niezawodności, Gold i Silver automatycznie oczyszcza usuniętych węzłów, więc jest wymagane nie dodatkowe oczyszczanie.
- Mimo że istnieją [wiele metryk](../azure-monitor/platform/autoscale-common-metrics.md) obsługiwany przez reguły skalowania automatycznego, nadal jest ograniczony zestaw. Jeśli dany scenariusz wymaga skalowanie w oparciu o niektóre metryki nieuwzględnione w tym zestawie, w reguł automatycznego skalowania może nie być dobrym rozwiązaniem.

Jak powinna wynosić skalowania usługi Service Fabric, zależy od danego scenariusza. Jeśli skalowanie jest niczym niezwykłym, możliwość dodawania lub usuwania węzłów ręcznie jest prawdopodobnie wystarczający. W przypadku bardziej złożonych scenariuszy reguł skalowania automatycznego i zestawy SDK udostępnia możliwość skalowania programowo oferują zaawansowane alternatyw.

Interfejsy API usługi Azure istnieje, które umożliwiają aplikacji, aby programowo pracować z maszyną wirtualną zestawach skalowania i klastry usługi Service Fabric. Jeśli istniejące opcje automatycznego skalowania nie działa dla danego scenariusza, te interfejsy API umożliwiają zaimplementować logikę niestandardową skalowania. 

Jedno z podejść do wdrażania tej "wprowadzone od głównej" funkcji automatycznego skalowania jest można dodać nowej usługi bezstanowej do aplikacji usługi Service Fabric w celu zarządzania operacjami skalowania. Tworzenie własnych skalowanie usługi zapewnia najwyższy stopień kontroli i dostosowywalności za pośrednictwem aplikacji na skalowanie. Może to być przydatne w scenariuszach wymagających precyzyjną kontrolę nad tym, kiedy lub jak aplikacja skaluje się wewnątrz lub na zewnątrz. Jednak ta kontrolka jest powiązana z zależnościami, złożoności kodu. Użycie tej metody oznacza, że trzeba własnych skalowania kod, który jest trywialny. W ramach usługi `RunAsync` metody zestawu wyzwalaczy można określić, czy skalowanie jest wymagane (w tym Sprawdzanie parametrów, takich jak rozmiar maksymalny klastra i skalowania cooldowns).   

Interfejs API umożliwiający interakcje zestawu skalowania maszyn wirtualnych, (zarówno jak, aby sprawdzić bieżącą liczbę wystąpień maszyn wirtualnych, a następnie zmodyfikować go) jest [fluent biblioteki usługi Azure Compute zarządzania](https://www.nuget.org/packages/Microsoft.Azure.Management.Compute.Fluent/). Biblioteki fluent obliczeń oferuje łatwe w użyciu interfejsu API na potrzeby interakcji z zestawami skalowania maszyn wirtualnych.  Do interakcji z samego klastra usługi Service Fabric, użyj [System.Fabric.FabricClient](/dotnet/api/system.fabric.fabricclient).

Skalowanie kod nie musi uruchomić jako usługę w klastrze, aby skalować, mimo że. Zarówno `IAzure` i `FabricClient` można zdalne łączenie się z skojarzonych z nimi zasobów platformy Azure, dzięki czemu skalowania usługi można łatwo aplikacji konsoli lub usługa Windows działających z poza aplikacji usługi Service Fabric.

Oparte na tych ograniczeń, możesz chcieć [wdrożenie bardziej dostosowane automatycznego skalowania modeli](service-fabric-cluster-programmatic-scaling.md).

## <a name="scaling-up-and-down-or-vertical-scaling"></a>Skalowanie w górę i w dół lub skalowanie w pionie 
Zmienia zasobów (procesor CPU, pamięcią lub magazynem) z węzłów w klastrze.
- Zalety: Architektura oprogramowania i aplikacji pozostaje taka sama.
- Wady: Ograniczone skalowania, ponieważ ma ograniczenie ile może zwiększyć zasoby na poszczególnych węzłach. Czas przestoju, ponieważ będą potrzebne do podejmowania maszyn fizycznych lub wirtualnych w trybie offline w celu Dodawanie lub usuwanie zasobów.

Zestawy skalowania maszyn wirtualnych to zasób obliczeniowy systemu Azure, która umożliwia wdrażanie i zarządzanie kolekcją maszyn wirtualnych jako zestawu. Każdy typ węzła, który jest zdefiniowany w klastrze platformy Azure jest [konfigurowany jako zestaw skalowania oddzielnych](service-fabric-cluster-nodetypes.md). Każdy typ węzła może następnie być zarządzany oddzielnie.  Typ węzła skalowanie w górę lub w dół obejmuje Zmiana jednostki SKU wystąpień maszyn wirtualnych w zestawie skalowania. 

> [!WARNING]
> Firma Microsoft zaleca, aby nie zmieniać jednostki SKU maszyny Wirtualnej typu węzeł/zestaw skalowania, chyba że jest uruchomiona na [srebrny trwałości lub nowszej](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster). Zmiana rozmiaru jednostki SKU maszyny Wirtualnej jest operacją destrukcyjne danych w miejscu infrastruktury. Bez niektóre możliwość opóźnienia lub monitora, ta zmiana jest możliwe, że operacja może spowodować utratę danych w przypadku usług stanowych lub powodować inne nieprzewidziane problemy operacyjne, nawet w przypadku obciążeń bezstanowych. 
>

Podczas skalowania w klastrze platformy Azure, należy przestrzegać następujących wytycznych:
- W przypadku skalowania w dół typu węzła podstawowego użytkownik powinien nigdy nie przeskalujesz ją w dół do więcej niż co [warstwy niezawodności](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) umożliwia.

Proces skalowania typ węzła w górę lub w dół różni się w zależności od tego, czy jest to typ węzła innego niż podstawowy lub podstawowego.

### <a name="scaling-non-primary-node-types"></a>Skalowanie typy węzłów innych niż podstawowe
Tworzenie nowego typu węzła z zasobami, które są potrzebne.  Zaktualizuj ograniczenia dotyczące umieszczania uruchomionych usług w celu uwzględnienia nowego typu węzła.  Stopniowo (po jednym naraz), zmniejszyć liczbę wystąpień stare liczba wystąpień typów węzłów na zero, tak aby niezawodności klastra nie ma wpływu.  Usługi stopniowo zostaną zmigrowane do nowego typu węzła, ponieważ stary typ węzła jest zamknięty.

### <a name="scaling-the-primary-node-type"></a>Skalowanie podstawowy typ węzła
Zaleca się, że nie należy zmieniać jednostki SKU maszyny Wirtualnej typu węzła podstawowego. Jeśli potrzebujesz większej pojemności klastra, zaleca się dodanie większej liczby wystąpień. 

Jeśli, nie jest to możliwe, należy utworzyć nowy klaster i [Przywróć stan aplikacji](service-fabric-reliable-services-backup-restore.md) (jeśli dotyczy) ze starego klastra. Nie należy przywrócić wszystkie stanu usługi systemu, zostaną ponownie utworzone podczas wdrażania aplikacji do nowego klastra. Jeśli po prostu systemem aplikacji bezstanowych klastra, a następnie wszystko, co możesz zrobić to wdrażanie aplikacji do nowego klastra, masz nic nie można przywrócić. Jeśli zdecydujesz się przejść nieobsługiwany trasy i chcesz zmienić jednostki SKU maszyny Wirtualnej, następnie modyfikacje upewnij skalowania maszyn wirtualnych Ustaw definicję modelu w celu odzwierciedlenia nowej jednostki SKU. Jeśli klaster zawiera tylko jeden węzeł typu, upewnij się, że wszystkie aplikacje stanowe będą odpowiadać na wszystkich [obsługi zdarzeń cyklu życia repliki](service-fabric-reliable-services-lifecycle.md) (np. replika w kompilacji jest zablokowana) w odpowiednim czasie, który repliki usługi ponownie skompiluj czas trwania wynosi mniej niż pięciu minut (poziom trwałości Silver). 

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [skalowalności aplikacji](service-fabric-concepts-scalability.md).
* [Skalowanie klastra usługi Azure wewnątrz lub na zewnątrz](service-fabric-tutorial-scale-cluster.md).
* [Skalowanie klastra usługi Azure programowo](service-fabric-cluster-programmatic-scaling.md) przy użyciu fluent Azure compute zestawu SDK.
* [Skalowanie klastra autonomicznego wewnątrz lub na zewnątrz](service-fabric-cluster-windows-server-add-remove-nodes.md).

