---
title: Planowanie wydajności aplikacji usługi Service Fabric | Dokumentacja firmy Microsoft
description: Opisuje sposób zidentyfikować liczbę węzłów obliczeń wymagana dla aplikacji usługi Service Fabric
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: markfuss
editor: ''
ms.assetid: 9fa47be0-50a2-4a51-84a5-20992af94bea
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: d7ca566b86ed79aa773d7af2553223c79ed9944a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60342024"
---
# <a name="capacity-planning-for-service-fabric-applications"></a>Planowanie wydajności dla aplikacji usługi Service Fabric
Ten dokument zawiera wskazówki dotyczące szacowanie ilości zasobów (procesorów CPU, pamięci RAM, dysk magazynu), musisz uruchomić aplikacje usługi Azure Service Fabric. Bardzo często wymagań dotyczących zasobów można zmienić wraz z upływem czasu. Opracowywanie i testowanie usługi, a następnie wymaga większej ilości zasobów, możesz wdrożyć gotowe środowisko produkcyjne i wzrostu w popularności aplikacji zwykle wymagają niewielkiej ilości zasobów. Podczas projektowania aplikacji przemyślenie długoterminowych wymagań i opcji, które umożliwiają usłudze skalowanie wysokiego zapotrzebowania.

 Podczas tworzenia klastra usługi Service Fabric, możesz zdecydować, jakie rodzaje maszyn wirtualnych (VM) wchodzących w skład klastra. Każda maszyna wirtualna jest dostarczany z ograniczoną ilość zasobów w postaci procesorów CPU (rdzenie i prędkość), przepustowości sieci, pamięci RAM i magazynu danych na dysku. Wraz ze wzrostem natężenia usługi wraz z upływem czasu, możesz uaktualnić do maszyn wirtualnych, które oferują większe zasoby i/lub dodać więcej maszyn wirtualnych do klastra. W drugim, możesz musi architektury usługi początkowo tak może korzystać z nowych maszyn wirtualnych, które dynamicznie poproś o dodanie do klastra.

Niektóre usługi Zarządzanie mały do żadnych danych na maszynach wirtualnych, samodzielnie. W związku z tym planowanie pojemności dla tych usług należy skoncentrować się głównie na wydajność, co oznacza wybranie odpowiednich procesorów CPU (rdzenie i prędkość) maszyn wirtualnych. Ponadto należy rozważyć przepustowość sieci, w tym, jak często występują transfery w sieci i ile dane są przesyłane. Jeśli usługa musi wykonać również w miarę wzrostu użycia usługi, możesz dodać więcej maszyn wirtualnych do klastra i równoważenia obciążenia żądania sieciowe wszystkich maszyn wirtualnych.

W przypadku usług, które zarządzają dużymi ilościami danych na maszynach wirtualnych planowania pojemności należy skoncentrować się przede wszystkim od rozmiaru. W związku z tym należy rozważyć, pojemności pamięci RAM maszyny Wirtualnej i magazynu danych na dysku. System zarządzania pamięci wirtualnej Windows sprawia, że ilość miejsca na dysku wyglądać pamięci RAM do kodu aplikacji. Ponadto środowisko uruchomieniowe usługi Service Fabric zapewnia stronicowanie inteligentne przechowywanie tylko gorących danych w pamięci i przeniesienie zimnych danych na dysku. Aplikacje mogą używać związku z tym większej ilości pamięci niż jest fizycznie dostępnej na maszynie Wirtualnej. Po prostu mających więcej pamięci RAM zwiększa wydajność, ponieważ maszyny Wirtualnej można przechowywać więcej miejsca w pamięci RAM. Maszyna wirtualna, którą wybierzesz powinien mieć dysku jest wystarczająco duży, aby przechowywać dane, które mają na maszynie Wirtualnej. Podobnie maszyna wirtualna powinna mieć wystarczającą ilość pamięci RAM, aby zapewnić pożądaną wydajność. Jeśli z usługą danych zwiększa się wraz z upływem czasu, możesz dodać więcej maszyn wirtualnych do klastra i rozdzielanie danych na wszystkich maszynach wirtualnych.

## <a name="determine-how-many-nodes-you-need"></a>Ilu węzłów, należy określić
Partycjonowanie usługi umożliwia skalowanie w poziomie usługi danych. Aby uzyskać więcej informacji na temat partycjonowania, zobacz [partycjonowania usługi Service Fabric](service-fabric-concepts-partitioning.md). Każda partycja musi mieścić się w pojedynczej maszyny Wirtualnej, ale można umieścić wiele partycji (małego) na jednej maszynie Wirtualnej. Dlatego istnienie więcej małych partycji zapewnia większą elastyczność niż posiadanie kilku większe partycje. Jest to kompromis, wiele partycji zwiększa obciążenie usługi Service Fabric i nie można wykonać operacji transakcyjnych na partycje. Istnieje również więcej potencjalnych ruchu sieciowego, jeśli kodu usługi często wymaga dostępu do elementów danych, które znajdują się w różnych partycjach. Podczas projektowania usługi, należy rozważyć następujące zalety i wady na efektywnej strategii partycjonowania.

Załóżmy, że Twoja aplikacja ma jednej usługi stanowe o rozmiarze magazynu, których oczekujesz, że zwiększenie DB_Size GB w roku. Chcesz dodać więcej aplikacji (i partycje) jako środowisko rozwoju poza danego roku.  Współczynnik replikacji (RF), która określa liczbę replik dla usługi ma wpływ na całkowity DB_Size. Łączna liczba DB_Size we wszystkich replik jest współczynnik replikacji pomnożona przez DB_Size.  Node_Size reprezentuje dysk miejsca/pamięci RAM na węzeł, który chcesz użyć dla usługi. Aby uzyskać najlepszą wydajność DB_Size powinien się zmieścić w pamięci w klastrze, a Node_Size, wokół pamięci RAM maszyny wirtualnej powinna być wybrana. Przydzielając Node_Size, który jest większy niż pojemność pamięci RAM, polegasz na stronicowania dostarczane przez środowisko uruchomieniowe usługi Service Fabric. W związku z tym, wydajność może nie być optymalne, jeśli cały danych jest traktowany jako gorąca (przywróconych danych jest stronicowanej We/Wy). Jednak dla wielu usług, w których gorąca jest tylko część danych, jest bardziej opłacalna.

Liczba węzłów, wymagane, aby osiągnąć najwyższą wydajność można obliczyć w następujący sposób:

```
Number of Nodes = (DB_Size * RF)/Node_Size

```


## <a name="account-for-growth"></a>Uwzględnianie wzrostu
Można obliczyć liczby węzłów w oparciu DB_Size, których oczekujesz rośnie, oprócz DB_Size, który rozpoczął się w za pomocą usługi. Następnie zwiększać liczbę węzłów wzrostem tak, aby nie są przerostu liczbę węzłów usługi. Ale liczby partycji powinien opierać się na liczbę węzłów, które są wymagane, gdy korzystasz z usługi na maksymalny wzrost.

Należy dobrze poznać niektóre dodatkowe maszyny dostępne w dowolnym momencie, aby można było obsłużyć wszelkie nieoczekiwane zwiększenia lub Niepowodzenie (na przykład, jeśli kilka maszyn wirtualnych są wyłączane).  Dodatkowa pojemność należy określić przy użyciu usługi oczekiwanej wartości graniczne, punkt początkowy polega na zarezerwować kilka dodatkowych maszyn wirtualnych (5 – 10 procent dodatkowych).

Kroku przyjęto założenie, jednej usługi stanowej. Jeśli masz więcej niż jedna usługa stanowa, trzeba dodać DB_Size skojarzone z innymi usługami do równania. Alternatywnie można obliczyć liczby węzłów osobno dla każdej usługi stanowe.  Usługa może być repliki lub partycje, które nie są zrównoważone. Należy pamiętać, że partycji może być więcej danych niż inne. Aby uzyskać więcej informacji na temat partycjonowania, zobacz [partycjonowanie artykuł na temat najlepszych rozwiązań](service-fabric-concepts-partitioning.md). Jednak poprzedni równanie ma partycji i replik niezależny, ponieważ usługa Service Fabric zapewnia, że repliki są rozproszone między węzłami w optymalny sposób.

## <a name="use-a-spreadsheet-for-cost-calculation"></a>Arkusz kalkulacyjny na użytek obliczania kosztu
Teraz umieśćmy kilka liczb rzeczywistych, w formule. [Przykładowy arkusz kalkulacyjny](https://servicefabricsdkstorage.blob.core.windows.net/publicrelease/SF%20VM%20Cost%20calculator-NEW.xlsx) pokazuje, jak zaplanować pojemność dla aplikacji, która zawiera trzy typy obiektów danych. Dla każdego obiektu Firma Microsoft zbliżone do jej rozmiaru i liczby obiektów, że oczekujemy, że masz. Możemy również wybrać jak wiele replik, które chcemy każdego typu obiektu. Arkusz kalkulacyjny obliczy łączną ilość pamięci, które mają być przechowywane w klastrze.

Następnie możemy wprowadzić rozmiar maszyny Wirtualnej, a koszt miesięczny. Zależności od rozmiaru maszyny Wirtualnej, arkusz kalkulacyjny pozwalają określić minimalną liczbę partycji, który musi być podziału danych, aby fizycznie mieści się w węzłach. Może pragną większej liczby partycji w celu uwzględnienia obliczeń określonej aplikacji i wymaga ruchu sieciowego. Arkusz kalkulacyjny pokazuje, że liczba partycji, które są używane do zarządzania obiektami profilu użytkownika został zwiększony od 1 do 6.

Na podstawie wszystkich tych informacji, arkusz kalkulacyjny zawiera obecnie można fizycznie pobrać wszystkie dane z żądanego partycje i repliki w klastrze z 26 węzłem. Jednak ten klaster będzie gęsto zapewniać, dlatego może być pewne dodatkowe węzły, aby pomieścić awarii węzła i uaktualnień. Arkusz kalkulacyjny pokazuje również, że o ponad 57 węzłów zapewnia żadne dodatkowe wartości, ponieważ będzie zawierał węzły pustych. Można ponownie pozycji ponad 57 węzłów mimo to uwzględnić awarii węzła i uaktualnień. Arkusz kalkulacyjny do określonych potrzeb Twojej aplikacji, można dostosować.   

![Arkusz kalkulacyjny do obliczenia kosztów][Image1]

## <a name="next-steps"></a>Kolejne kroki
Zapoznaj się z [usługi partycjonowania usługi Service Fabric] [ 10] Aby dowiedzieć się więcej na temat partycjonowania usługi.

<!--Image references-->
[Image1]: ./media/SF-Cost.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-concepts-partitioning.md
