---
title: Planowanie pojemności aplikacji sieci szkieletowej usług
description: W tym artykule opisano sposób identyfikowania liczby węzłów obliczeniowych wymaganych dla aplikacji sieci szkieletowej usług
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: cd5a5c55ff873e4891ac63361d0c4a0b56d70109
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75377212"
---
# <a name="capacity-planning-for-service-fabric-applications"></a>Planowanie pojemności dla aplikacji sieci szkieletowej usług
W tym dokumencie opisano, jak oszacować ilość zasobów (procesory, pamięci RAM, magazyn dysku) potrzebnych do uruchomienia aplikacji sieci szkieletowej usług Azure. Często wymagania dotyczące zasobów zmieniają się wraz z czasem. Zazwyczaj wymaga się kilku zasobów podczas opracowywania/testowania usługi, a następnie wymagają więcej zasobów w miarę przejścia do produkcji i aplikacji rośnie popularność. Podczas projektowania aplikacji, przemyśleć długoterminowe wymagania i dokonać wyboru, które umożliwiają usługi skalowania w celu zaspokojenia wysokiego zapotrzebowania klientów.

 Podczas tworzenia klastra sieci szkieletowej usług, można zdecydować, jakie rodzaje maszyn wirtualnych (VM) tworzą klastra. Każda maszyna wirtualna jest wyposażona w ograniczoną ilość zasobów w postaci procesorów (rdzeni i szybkości), przepustowości sieci, pamięci RAM i pamięci masowej na dysku. W miarę wzrostu usługi w czasie można uaktualnić do maszyn wirtualnych, które oferują większe zasoby i/lub dodać więcej maszyn wirtualnych do klastra. Aby to zrobić, należy najpierw zaprojektować usługę, aby mogła korzystać z nowych maszyn wirtualnych, które są dynamicznie dodawane do klastra.

Niektóre usługi zarządzają niewiele lub nie ma danych na samych maszynach wirtualnych. W związku z tym planowanie pojemności dla tych usług powinny koncentrować się przede wszystkim na wydajności, co oznacza wybór odpowiednich procesorów (rdzeni i szybkość) maszyn wirtualnych. Ponadto należy wziąć pod uwagę przepustowość sieci, w tym, jak często występują transfery sieciowe i ile danych jest przesyłanych. Jeśli usługa musi działać dobrze, a zwiększa użycie usługi, można dodać więcej maszyn wirtualnych do klastra i równoważyć obciążenia żądań sieciowych we wszystkich maszynach wirtualnych.

W przypadku usług, które zarządzają dużą ilością danych na maszynach wirtualnych, planowanie pojemności powinno koncentrować się przede wszystkim na rozmiarze. W związku z tym należy dokładnie rozważyć pojemność pamięci RAM maszyny Wirtualnej i magazynu dysku. System zarządzania pamięcią wirtualną w systemie Windows sprawia, że miejsce na dysku wygląda jak pamięć RAM do kodu aplikacji. Ponadto środowisko uruchomieniowe sieci szkieletowej usług zapewnia inteligentne stronicowanie, przechowując tylko gorące dane w pamięci i przenosząc zimne dane na dysk. Aplikacje mogą zatem używać więcej pamięci niż jest fizycznie dostępne na maszynie Wirtualnej. Posiadanie większej ilości pamięci RAM po prostu zwiększa wydajność, ponieważ maszyna wirtualna może przechowywać więcej pamięci masowej na dysku RAM. Wybrana maszyna wirtualna powinna mieć dysk wystarczająco duży, aby przechowywać dane, które chcesz na maszynie wirtualnej. Podobnie maszyna wirtualna powinna mieć wystarczającą ilość pamięci RAM, aby zapewnić wydajność, której potrzebujesz. Jeśli dane usługi rośnie wraz z czasem, można dodać więcej maszyn wirtualnych do klastra i partycjonować dane na wszystkich maszynach wirtualnych.

## <a name="determine-how-many-nodes-you-need"></a>Określanie liczby potrzebnych węzłów
Partycjonowanie usługi umożliwia skalowanie w poziomie danych usługi. Aby uzyskać więcej informacji na temat partycjonowania, zobacz [Partycjonowanie sieci szkieletowej usług](service-fabric-concepts-partitioning.md). Każda partycja musi zmieścić się w jednej maszynie wirtualnej, ale wiele (małych) partycji można umieścić na jednej maszynie wirtualnej. Tak więc posiadanie większej liczby małych partycji zapewnia większą elastyczność niż posiadanie kilku większych partycji. Kompromis polega na tym, że posiadanie wielu partycji zwiększa obciążenie sieci szkieletowej usług i nie można wykonywać operacji ujednoliconych partycji. Istnieje również większy potencjalny ruch sieciowy, jeśli kod usługi często musi uzyskiwać dostęp do fragmentów danych, które żyją w różnych partycjach. Podczas projektowania usługi, należy dokładnie rozważyć te plusy i minusy, aby osiągnąć skuteczną strategię partycjonowania.

Załóżmy, że aplikacja ma jedną usługę stanową, która ma rozmiar sklepu, który ma wzrosnąć do DB_Size GB w ciągu roku. Jesteś gotów dodać więcej aplikacji (i partycji), jak wystąpić wzrost po tym roku.  Współczynnik replikacji (RF), który określa liczbę replik dla usługi wpływa na całkowitą DB_Size. Całkowita DB_Size we wszystkich replikach jest współczynnik replikacji pomnożona przez DB_Size.  Node_Size reprezentuje miejsce na dysku/pamięć RAM na węzeł, którego chcesz użyć w usłudze. Aby uzyskać najlepszą wydajność, DB_Size powinny pasować do pamięci w klastrze i należy wybrać Node_Size, który znajduje się wokół pamięci RAM maszyny Wirtualnej. Przydzielając Node_Size, który jest większy niż pojemność pamięci RAM, polegasz na stronicowaniu dostarczonym przez środowisko uruchomieniowe sieci szkieletowej usług. W związku z tym wydajność może nie być optymalna, jeśli całe dane są uważane za gorące (od tego czasu dane są stronicowane). Jednak w przypadku wielu usług, w których tylko ułamek danych jest gorący, jest bardziej opłacalny.

Liczbę węzłów wymaganych do maksymalnej wydajności można obliczyć w następujący sposób:

```
Number of Nodes = (DB_Size * RF)/Node_Size

```


## <a name="account-for-growth"></a>Uwzględnianie wzrostu
Można obliczyć liczbę węzłów na podstawie DB_Size, które oczekujesz, że usługa wzrośnie do, oprócz DB_Size, które rozpoczęły się od. Następnie zwiększ liczbę węzłów w miarę wzrostu usługi, aby nie nadmiernie inicjować obsługi administracyjnej liczby węzłów. Ale liczba partycji powinna być oparta na liczbie węzłów, które są potrzebne podczas uruchamiania usługi przy maksymalnym wzroście.

Dobrze jest mieć kilka dodatkowych maszyn dostępnych w dowolnym momencie, dzięki czemu można obsłużyć wszelkie nieoczekiwane skoki lub awarii (na przykład, jeśli kilka maszyn wirtualnych zejść w dół).  Podczas gdy dodatkowa pojemność powinna być określona przy użyciu oczekiwanych skoków, punktem wyjścia jest zarezerwować kilka dodatkowych maszyn wirtualnych (dodatkowe 5-10 procent).

Poprzednia przyjmuje jedną usługę stanową. Jeśli masz więcej niż jedną usługę stanową, musisz dodać DB_Size skojarzone z innymi usługami do równania. Alternatywnie można obliczyć liczbę węzłów oddzielnie dla każdej usługi stanowej.  Usługa może mieć repliki lub partycje, które nie są zrównoważone. Należy pamiętać, że partycje mogą również mieć więcej danych niż inne. Aby uzyskać więcej informacji na temat partycjonowania, zobacz [artykuł partycjonowanie na temat najlepszych rozwiązań](service-fabric-concepts-partitioning.md). Jednak poprzednie równanie jest partycji i repliki niezależnego odzgostyw, ponieważ sieci szkieletowej usług zapewnia, że repliki są rozłożone między węzłami w sposób zoptymalizowany.

## <a name="use-a-spreadsheet-for-cost-calculation"></a>Obliczanie kosztów za pomocą arkusza kalkulacyjnego
Teraz umieśćmy kilka prawdziwych liczb w formule. [Przykładowy arkusz kalkulacyjny](https://github.com/Azure/service-fabric/raw/master/docs_resources/SF_VM_Cost_calculator-NEW.xlsx) pokazuje, jak zaplanować pojemność aplikacji zawierającej trzy typy obiektów danych. Dla każdego obiektu przybliżamy jego rozmiar i liczbę obiektów, których oczekujemy. Możemy również wybrać, ile replik chcemy każdego typu obiektu. Arkusz kalkulacyjny oblicza całkowitą ilość pamięci, która ma być przechowywana w klastrze.

Następnie wprowadzamy rozmiar maszyny Wirtualnej i koszt miesięczny. Na podstawie rozmiaru maszyny Wirtualnej arkusz kalkulacyjny informuje o minimalnej liczbie partycji, których należy użyć do podzielenia danych, aby fizycznie zmieścić się w węzłach. Można chcieć większą liczbę partycji, aby dostosować się do określonych obliczeń aplikacji i potrzeb ruchu sieciowego. Arkusz kalkulacyjny pokazuje liczbę partycji, które zarządzają obiektami profilu użytkownika wzrosła z jednego do sześciu.

Teraz, na podstawie wszystkich tych informacji, arkusz kalkulacyjny pokazuje, że można fizycznie uzyskać wszystkie dane z żądanych partycji i replik w klastrze 26 węzłów. Jednak ten klaster będzie gęsto zapakowane, więc może chcesz kilka dodatkowych węzłów, aby pomieścić błędy węzłów i uaktualnień. Arkusz kalkulacyjny pokazuje również, że posiadanie więcej niż 57 węzłów nie zapewnia żadnej dodatkowej wartości, ponieważ mają puste węzły. Ponownie, może chcesz przejść powyżej 57 węzłów tak, aby pomieścić błędy węzłów i uaktualnień. Możesz dostosować arkusz kalkulacyjny, aby dopasować go do konkretnych potrzeb aplikacji.   

![Arkusz kalkulacyjny do obliczania kosztów][Image1]

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z [usługami partycjonowania sieci szkieletowej usług,][10] aby dowiedzieć się więcej na temat partycjonowania usługi.

<!--Image references-->
[Image1]: ./media/SF-Cost.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-concepts-partitioning.md
