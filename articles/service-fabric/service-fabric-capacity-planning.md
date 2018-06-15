---
title: Planowanie wydajności dla aplikacji sieci szkieletowej usług | Dokumentacja firmy Microsoft
description: Opisuje sposób określenia liczby węzłów obliczeniowych wymagane dla aplikacji sieci szkieletowej usług
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
ms.openlocfilehash: ac8abbdbbe9125ea036d837c08e1089aa6d1e55d
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212861"
---
# <a name="capacity-planning-for-service-fabric-applications"></a>Planowanie wydajności dla aplikacji sieci szkieletowej usług
Ten dokument jest przedstawienie sposobu oszacować ilość zasobów (procesorów CPU, pamięci RAM, dysku magazynu), należy uruchomić aplikacji sieci szkieletowej usług Azure. Bardzo często wymagań dotyczących zasobów zmiany w czasie. Tworzenie i testowanie usługi, a następnie wymaga większej ilości zasobów, przejdź do środowiska produkcyjnego i rozwoju aplikacji w popularne zwykle wymagają niewielkiej ilości zasobów. Podczas projektowania aplikacji przemyślenie długoterminowych wymagań i opcji umożliwiających usługi do skalowania, aby spełnić wymagania wysokiej klienta.

 Podczas tworzenia klastra usługi sieć szkieletowa zdecyduj, jakie rodzaje maszynach wirtualnych (VM) tworzą klastra. Każda maszyna wirtualna jest dostarczany z ograniczoną ilością zasobów w formie procesorów CPU (rdzenie i szybkości), przepustowości sieci, pamięci RAM i magazynu danych na dysku. Wraz z rozwojem usługi wraz z upływem czasu, można uaktualnić do maszyn wirtualnych, które oferują większe zasoby i/lub Dodaj więcej maszyn wirtualnych do klastra. Aby zrobić to drugie, użytkownik musi projektowania usługi początkowo, może korzystać z nowych maszyn wirtualnych, które dynamicznie zostaną dodane do klastra.

Niektóre usługi Zarządzanie mały do żadnych danych na maszynach wirtualnych, samodzielnie. W związku z tym planowanie wydajności dla tych usług należy skoncentrować się głównie na wydajność, co oznacza wybranie odpowiednich procesorów CPU (rdzenie i szybkość) maszyn wirtualnych. Ponadto należy rozważyć przepustowość sieci, w tym, jak często występuje transferów sieci oraz jak dużo danych jest przenoszona. Jeśli usługa musi wykonać również wraz ze wzrostem użycia usługi, możesz dodać więcej maszyn wirtualnych do klastra i obciążenia salda żądania sieciowe na wszystkich maszynach wirtualnych.

Dla usług, które zarządzają dużych ilości danych na maszynach wirtualnych planowania pojemności należy skoncentrować się przede wszystkim od rozmiaru. W związku z tym należy rozważyć pojemność pamięci RAM dla maszyny Wirtualnej i magazynu danych na dysku. System zarządzania pamięci wirtualnej w systemie Windows sprawia, że miejsca na dysku wyglądać pamięci RAM do kodu aplikacji. Ponadto środowiska uruchomieniowego platformy Service Fabric zawiera stronicowanie inteligentne zachowania tylko gorących danych w pamięci i przeniesienie zimnych danych na dysku. W związku z tym aplikacjom więcej pamięci niż jest fizycznie dostępna na maszynie Wirtualnej. Po prostu mających więcej pamięci RAM zwiększa wydajność, ponieważ maszyny Wirtualnej można przechowywać więcej miejsca w pamięci RAM. Maszyna wirtualna, którą wybierzesz powinien mieć dysku jest wystarczająco duży, aby przechowywać dane, które mają na maszynie Wirtualnej. Podobnie maszyny Wirtualnej ma wystarczającą ilość pamięci RAM zapewniające wydajność, której wymagasz. Jeśli dane z usługą rozwoju wraz z upływem czasu, można dodać więcej maszyn wirtualnych do klastra i partycji danych na wszystkich maszynach wirtualnych.

## <a name="determine-how-many-nodes-you-need"></a>Określić liczbę węzłów należy
Partycjonowanie usługi umożliwia skalowanie w poziomie danych z usługą. Aby uzyskać więcej informacji na partycje, zobacz [partycjonowania usługi sieć szkieletowa](service-fabric-concepts-partitioning.md). Każda partycja musi mieścić się w jednej maszyny Wirtualnej, ale wiele partycji (małe) można umieścić na jednej maszynie Wirtualnej. Tak mających więcej partycji małych zapewnia większą elastyczność niż w przypadku kilku większych partycji. Zależność jest wiele partycji zwiększa obciążenie sieci szkieletowej usług i nie można wykonać operacje transakcyjne na partycje. Istnieje również więcej potencjalnych ruchu sieciowego, jeśli kodu usługi często wymaga dostępu fragmentów danych, które znajdują się w różnych partycji. Podczas projektowania usługi, należy więc rozważyć tych zalet i wad na efektywnej strategii partycjonowania.

Załóżmy, że aplikacja ma jednej usługi stanowej, która ma rozmiar magazynu, który będzie rosnąć DB_Size GB w roku. Chcesz dodać więcej aplikacji (i partycje) jako występują wzrostu poza tym roku.  Współczynnik replikacji (RF), która określa liczbę replik dla usługi ma wpływ na całkowity DB_Size. Całkowita liczba DB_Size we wszystkich replik jest współczynnik replikacji pomnożona przez DB_Size.  Node_Size reprezentuje dysk miejsca/pamięci RAM na węzeł, który ma być używany dla usługi. Aby uzyskać najlepszą wydajność DB_Size powinny mieścić się w pamięci w ramach klastra, a powinna być wybrana Node_Size, będącą wokół pamięci RAM maszyny wirtualnej. Przydzielając Node_Size, który jest większy niż pojemność pamięci RAM, są zależne stronicowania udostępniane przez środowisko uruchomieniowe usługi sieć szkieletowa usług. W związku z tym wydajności mogą nie być optymalne, jeśli cały danych jest traktowany jako gorących (od tego momentu danych jest stronicowana We/Wy). Jednak w przypadku wielu usług tylko część danych w przypadku gorących jest bardziej ekonomiczne rozwiązanie.

Liczba węzłów wymagane dla maksymalnej wydajności można obliczyć w następujący sposób:

```
Number of Nodes = (DB_Size * RF)/Node_Size

```


## <a name="account-for-growth"></a>Konto dla rozwoju
Można obliczyć liczby węzłów w oparciu DB_Size, oczekiwanego rośnie, oprócz DB_Size, rozpoczęcia z usługi. Następnie wzrostu liczby węzłów, wraz z rozwojem usługi, dzięki czemu nie są przerostu liczba węzłów. Ale liczba partycji powinny być oparte na liczbę węzłów, które są wymagane, gdy używasz usługi na maksymalny wzrost.

Warto mieć niektóre dodatkowe maszyny dostępne w dowolnym momencie, tak aby (na przykład, jeśli kilka maszyn wirtualnych przestaną działać) może obsłużyć ani nagłego nieoczekiwany błąd.  Dodatkowej pojemności należy określić przy użyciu Twojej oczekiwanej wartości szczytowe, jest punkt wyjścia do zarezerwowania kilka dodatkowych maszyn wirtualnych (% 5 – 10 dodatkowych).

Poprzedni zakłada jednej usługi stanowej. Jeśli masz więcej niż jednej usługi stanowej, należy dodać DB_Size skojarzone z innymi usługami w równości. Alternatywnie można obliczyć liczby węzłów osobno dla każdej usługi stanowej.  Usługa może być repliki lub partycji, które nie są zrównoważonym. Należy pamiętać, że partycji może mieć więcej danych niż inne. Aby uzyskać więcej informacji na partycje, zobacz [partycjonowania artykuł na temat najlepszych rozwiązań](service-fabric-concepts-partitioning.md). Jednak poprzedniego równości jest partycji i replik niezależny, ponieważ sieć szkieletowa usług gwarantuje, że repliki są rozproszone między węzłami w optymalny sposób.

## <a name="use-a-spreadsheet-for-cost-calculation"></a>Arkusz kalkulacyjny na użytek obliczenie kosztów
Teraz umieśćmy kilka liczb rzeczywistych w formule. [Arkusza kalkulacyjnego przykład](https://servicefabricsdkstorage.blob.core.windows.net/publicrelease/SF%20VM%20Cost%20calculator-NEW.xlsx) przedstawiono sposób planowania pojemności dla aplikacji, która zawiera trzy typy obiektów danych. Dla każdego obiektu możemy zbliżenie jego rozmiar i obiekty ile oczekujemy ma. Możemy również wybrać liczby replikami chcemy każdego typu obiektu. Arkusz kalkulacyjny obliczy łączna ilość pamięci, które mają być przechowywane w klastrze.

Następnie możemy wprowadź rozmiar maszyny Wirtualnej i miesięcznych kosztów. Zależnie od rozmiaru maszyny Wirtualnej, arkusz kalkulacyjny określa minimalną liczbę partycji musi używanego do dzielenia danych na fizycznie mieści się w węzłach. Może chcesz większej liczby partycji w celu uwzględnienia obliczeń określonych aplikacji i wymaga ruchu sieciowego. Arkusz kalkulacyjny pokazuje liczbę partycji, które są zarządzania obiektami profilu użytkownika wzrosło od 1 do 6.

Teraz oparte na te informacje, arkusz kalkulacyjny pokazuje, czy fizycznie można pobrać wszystkich danych z żądaną partycji i replik w klastrze 26 węzła. Jednak ten klaster będzie gęsto zapewniać, dlatego może być niektóre dodatkowe węzły do awarii węzła i uaktualnień. Arkusz kalkulacyjny pokazuje też, że mających więcej niż 57 węzłów nie określa żadnej dodatkowe wartości, ponieważ miałoby węzłami pusty. Ponownie można przekraczają 57 węzłów mimo to awarii węzła i uaktualnień. Można dostosować arkusza kalkulacyjnego, aby dopasować specyficznych potrzeb Twojej aplikacji.   

![Arkusza kalkulacyjnego w celu obliczenia kosztu][Image1]

## <a name="next-steps"></a>Kolejne kroki
Zapoznaj się z [partycjonowania usługi sieć szkieletowa usług] [ 10] Aby dowiedzieć się więcej na temat partycjonowania usługi.

<!--Image references-->
[Image1]: ./media/SF-Cost.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-concepts-partitioning.md
