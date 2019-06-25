---
title: Opisywanie klastra przy użyciu usługi Resource Manager dla klastra | Dokumentacja firmy Microsoft
description: Opisywanie klastra usługi Service Fabric, określając domen błędów, domen uaktualnienia, — właściwości węzła i wydajność węzła dla klastra usługi Resource Manager.
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: 55f8ab37-9399-4c9a-9e6c-d2d859de6766
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 22ccb21a208bbe8e825bff9f7602bfca05990816
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67271648"
---
# <a name="describe-a-service-fabric-cluster-by-using-cluster-resource-manager"></a>Opisywanie klastra usługi Service Fabric przy użyciu usługi Resource Manager dla klastra
Funkcja Menedżer zasobów klastra usługi Azure Service Fabric udostępnia kilka mechanizmów do opisywania klastra:

* Domeny błędów
* Uaktualnij domeny
* Właściwości węzła
* Węzeł pojemności

Podczas wykonywania Menedżer zasobów klastra używa tych informacji, aby zapewnić wysoką dostępność usług uruchomionych w klastrze. Wymuszając te ważne reguły, podejmuje także zoptymalizować zużycie zasobów w klastrze.

## <a name="fault-domains"></a>Domeny błędów
Domena błędów to dowolny obszar skoordynowanego awarii. Jedna maszyna jest domeny błędów. Mogą spowodować niepowodzenie na swój własny z różnych powodów, od mocy błędy dostaw na awarie dysku zły oprogramowania układowego kart Sieciowych. 

Maszyny podłączone do tego samego przełącznika Ethernet znajdują się w tej samej domenie błędów. Dlatego są maszyn, które korzystają z jednego źródła zasilania lub w obrębie jednej lokalizacji. 

Ponieważ jest to naturalny na awarie sprzętu nakładanie się domen błędów są założenia hierarchiczne. Są one reprezentowane jako identyfikatory URI w usłudze Service Fabric.

Należy pamiętać, że domen błędów są prawidłowo skonfigurowane, ponieważ usługa Service Fabric używa tych informacji do bezpiecznego umieść usług. Usługa Service Fabric nie chcesz umieścić usług w taki sposób, że utraty domenę błędów (przyczyną niepowodzenia niektórych składników) powoduje, że usługi przejść w dół. 

W środowisku platformy Azure Usługa Service Fabric używa informacji o domenie błędów oferowanych przez środowisko poprawnie skonfigurować węzły w klastrze w Twoim imieniu. Wystąpienia autonomicznego usługi Service Fabric domeny błędów są definiowane w czasie, że klaster został skonfigurowany. 

> [!WARNING]
> Należy pamiętać, że informacje o domenie błędów, które zostały dostarczone do usługi Service Fabric jest prawidłowo wprowadzony. Na przykład załóżmy, że węzły klastra usługi Service Fabric działają wewnątrz 10 maszyn wirtualnych, na 5 hostach fizycznych. W tym przypadku chociaż występują 10 maszyn wirtualnych, są tylko 5 różnych (najwyższy poziom) domeny błędów. Udostępnianie tego samego hosta fizycznego powoduje, że maszyny wirtualne na udostępnianie tej samej domenie błędów głównego ponieważ maszyny wirtualne wystąpić błąd skoordynowane, jeśli ich fizycznego hosta nie powiedzie się.  
>
> Usługa Service Fabric oczekuje, że domena błędów węzła nie należy zmieniać. Inne mechanizmy zapewniania wysokiej dostępności maszyn wirtualnych, takich jak [maszyn wirtualnych o wysokiej dostępności](https://technet.microsoft.com/library/cc967323.aspx), mogą powodować konflikty z usługą Service Fabric. Te mechanizmy użyj przezroczystego migracji maszyn wirtualnych z jednego hosta na inny. Nie one ponownie skonfigurować lub powiadomić uruchomiony kod wewnątrz maszyny Wirtualnej. Jako takie, są one *nieobsługiwane* jako środowiska do uruchamiania usługi Service Fabric klastrów. 
>
> Usługa Service Fabric powinna być stosowanej technologii tylko o wysokiej dostępności. Mechanizmy, takie jak live migracji maszyny Wirtualnej i sieci SAN nie jest konieczne. Jeśli te mechanizmy są używane w połączeniu z usługą Service Fabric, ich _zmniejszyć_ aplikacji dostępność i niezawodność. Przyczyną jest, że ich wprowadzenie dodatkowej złożoności, Dodaj scentralizowane źródła błędu i użyj strategii niezawodności i dostępności, która wchodzi w konflikt z tymi w usłudze Service Fabric. 
>
>

Na poniższym rysunku firma Microsoft kolor wszystkich jednostek, które przyczyniają się do domen błędów i wyświetlanie listy wszystkich domen, które powodują w różnych domenach błędów. W tym przykładzie mamy centrów danych ("DC"), stojakami ("R") i bloków ("B"). Jeśli każdy blok zawiera więcej niż jedną maszynę wirtualną, może być innej warstwy w hierarchii domeny błędów.

<center>

![Węzły zorganizowane za pośrednictwem domeny błędów][Image1]
</center>

Podczas wykonywania Menedżer zasobów klastra usługi w sieci szkieletowej uwzględnia domenach awarii klastra i plany układów. Stanowych replik lub wystąpień bezstanowej usługi są dystrybuowane, aby były one w oddzielnych domenach błędów. Dystrybucja usługi w domenach błędów gwarantuje, że dostępność usługi nie są naruszone, gdy domena błędów nie powiodło się na dowolnym poziomie hierarchii.

Menedżer zasobów klastra nie obsługuje liczbę warstw, które istnieją w hierarchii domeny błędów. Próbuje upewnij się, że utraty jednej części hierarchii nie ma wpływu na usługi działające w niej. 

Najlepiej jest taką samą liczbę węzłów, na każdym poziomie głębokość w hierarchii domeny błędów. Jeśli "drzewa" domen błędów jest niezrównoważone w klastrze, jest trudniejsze dla klastra usługi Resource Manager ustalenie najlepszych alokacji usług. Układy domeny błędów imbalanced oznacza, że utratę niektórych domen ma wpływ na dostępność usług częściej niż innych domen. W rezultacie Menedżer zasobów klastra rozerwana między dwa cele: 

* Chce używać z maszynami w tej domenie "duże", umieszczając usług na nich. 
* Chce umieszcza usług w innych domenach, tak aby utraty domeny nie powoduje problemów. 

Jak wyglądają imbalanced domen? Na poniższym diagramie przedstawiono dwa układy innego klastra. W pierwszym przykładzie węzły są dystrybuowane równomiernie w domenach błędów. W drugim przykładzie jedną domenę błędów ma wiele więcej węzłów niż inne domeny błędów. 

<center>

![Dwa układy innego klastra][Image2]
</center>

Na platformie Azure Wybór domeny błędów, które zawiera węzeł odbywa się za Ciebie. Jednak w zależności od liczby węzłów, które należy zarezerwować, można nadal na końcu domen błędów, które mają większą liczbę węzłów w nich niż w innych. 

Załóżmy na przykład masz 5 domen błędów w klastrze, ale aprowizacji siedmiu węzłów dla typu węzła (**NodeType**). W tym przypadku pierwsze dwie domeny błędów skończyć z większą liczbę węzłów. Jeśli będziesz kontynuować wdrożyć ich więcej **NodeType** wystąpień przy użyciu jedynie kilku wystąpień, problem pobiera niższa. Z tego powodu zaleca się, że liczba węzłów na każdy typ węzła jest wielokrotnością liczby domen błędów.

## <a name="upgrade-domains"></a>Uaktualnij domeny
Uaktualnianie domeny są innego funkcja, która ułatwia usługi Service Fabric Menedżer zasobów klastra zrozumieć układ klastra. Domen uaktualnienia definiują zestaw węzłów, które są uaktualnienia w tym samym czasie. Domen uaktualnienia pomóc Menedżer zasobów klastra, zrozumieć i organizowanie operacje zarządzania, takie jak uaktualnienia.

Domeny uaktualnień są bardzo podobnie jak domen błędów, ale z kilka podstawowych różnic. Najpierw obszarów awarie sprzętowe skoordynowanego Definiowanie domen błędów. Uaktualnienia domen, z drugiej strony, są definiowane przez zasady. Uzyskaj do określania, ile chcesz, zamiast automatycznego środowiska dyktowanie numer. Może mieć dowolną liczbę domen uaktualnienia, podobnie jak węzły. Inna różnica między domenami błędów i uaktualnień jest domen uaktualnienia nie są hierarchiczne. Zamiast tego są one bardziej przypominają prostych tagów. 

Na poniższym diagramie przedstawiono trzy domeny uaktualnienia rozkładane na trzy domeny błędów. Zawiera także jedną możliwe umieszczanie dla trzech różnych replik usługi stanowej, gdzie każdy kończy się w różnych domenach błędów i domenach uaktualniania. Ta umieszczania umożliwia utraty domeny błędów w trakcie wykonywania uaktualnienia usługi i jeszcze jedną kopię kodu i danych.  

<center>

![Umieszczanie za pomocą oraz domen błędów i uaktualnianie][Image3]
</center>

Istnieją zalety i wady na potrzeby dużej liczby domen uaktualnienia. Więcej domen uaktualnienia oznaczają, że każdy krok uaktualniania jest bardziej szczegółowe i wpływa na mniejszą liczbę węzłów lub usługi. Mniejszą liczbę usług trzeba przenieść w czasie, wprowadzenie do mniej zmian w systemie. Jest to prawdopodobnie poprawy niezawodności, ponieważ jest mniejszy usługi dotyczy problem z wprowadzonych podczas uaktualniania. Więcej domen uaktualnienia to także oznaczać konieczność buforów mniej dostępne w innych węzłach, aby obsłużyć wpływ uaktualnienia. 

Na przykład jeśli masz pięć domen uaktualnienia, węzły w każdym obsługi około 20 procent w ruchu. Jeśli potrzebujesz w walce z tej domeny uaktualnień dla uaktualnienie, aby obciążenie zwykle musi go w innym miejscu. Ponieważ czterech pozostałych domen uaktualnienia, każdy jest wystarczająca ilość miejsca do około 5 procent całkowitego ruchu. Więcej domen uaktualnienia oznacza konieczność mniej buforu na węzłach w klastrze. 

Należy rozważyć, czy istniały odpowiednie 10 domen uaktualnienia zamiast tego. W takim przypadku każdej z domen tego czy obsługa tylko około 10 procent całkowitego ruchu. Po uaktualnieniu opisano za pośrednictwem klastra każdej domeny, musi mieć miejsca dla tylko o 1.1 procent całkowitego ruchu. Więcej domen uaktualnienia ogólnie umożliwiają uruchamianie węzłów na zwiększenie użycia, ponieważ wymaga mniej rezerwowanie pojemności. To samo jest prawdą w przypadku domen błędów.  

Wadą mających wiele domen uaktualnienia jest uaktualnień zwykle trwa dłużej. Usługa Service Fabric czeka przez krótki okres, po domeny uaktualnienia zostało zakończone i sprawdza się przed przystąpieniem do uaktualniania kolejny. Te opóźnienia Włącz wykrywanie problemów wprowadzone podczas uaktualnienia, zanim uaktualnianie będzie kontynuowane. Kosztem jest dopuszczalne, ponieważ uniemożliwia zły zmiany wywierania wpływu na zbyt wiele usług w danym momencie.

Obecność zbyt małej liczby domen uaktualnienia ma wiele negatywne skutki uboczne. Każda domena uaktualnienia jest rozwijane i Trwa uaktualnianie dużą część łączna pojemność jest niedostępny. Na przykład jeśli masz tylko dla trzech domen uaktualnienia, tworzenia w dół o jedną trzecią ogólnej usługi lub pojemność klastra w danym momencie. Jednocześnie o tak wiele usług w dół nie jest pożądane, ponieważ wymaga wystarczającej pojemności w pozostałej części klastra do obsługi obciążenia. Z zachowaniem tego buforu oznacza, że podczas normalnego działania, te węzły są ładowane mniejsza niż przeciwnym. Zwiększa to koszt uruchamiania usługi.

Nie ma żadnych rzeczywistych limitu całkowitą liczbę domen błędów lub uaktualnienie w środowisku lub ograniczenia dotyczące sposobu nakładają się. Jednak istnieją wspólne wzorce:

- Domeny błędów i domeny uaktualnień mapowane 1:1
- Jedną domenę uaktualnienia na węzeł (fizyczny lub wirtualny wystąpienie systemu operacyjnego)
- Model "rozłożone" lub "macierzy", gdzie domenach błędów i domenach uaktualniania tworzą macierz zwykle uruchomieniu dół ukośne podziały maszyn

<center>

![Układy w domenach błędów i uaktualnień][Image4]
</center>

Nie ma żadnych najlepszą odpowiedź, dla którego układu wybrać. Każdy ma zalety i wady. Na przykład 1FD:1UD model jest prosta konfiguracja. Model jedną domenę uaktualnienia na węzeł modelu jest najbardziej takich jak osoby, które są używane do. Podczas uaktualniania każdy węzeł jest aktualizowany niezależnie. Jest to podobne do jak małe zestawy maszyn zostały uaktualnione ręcznie w przeszłości.

Najbardziej typowe model jest sama macierzy, gdzie domenach błędów i domenach uaktualniania tworzą tabelę, a węzły są umieszczane uruchamianie wzdłuż przekątnej. Jest to model używany domyślnie w klastrach usługi Service Fabric na platformie Azure. W przypadku klastrów z wieloma węzłami wszystko, co kończy się wyglądająca jak wzorzec gęstą macierzy.

> [!NOTE]
> Klastry usługi Service Fabric hostowane na platformie Azure nie obsługują zmiany strategii domyślnej. Tylko klastry autonomiczne oferują tego dostosowania.
>

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>Ograniczenia domen domenach błędów i uaktualnień i wynikowe zachowania
### <a name="default-approach"></a>Metody domyślne
Domyślnie Menedżer zasobów klastra temu usługi są równoważone w domenach błędów i uaktualnień. Jest to modelowane jako [ograniczenie](service-fabric-cluster-resource-manager-management-integration.md). Ograniczenie dla stanów domen domenach błędów i uaktualnień: "Partycja danej usługi, nigdy nie należy różnica większa niż jeden liczby obiektów usługi (repliki usługi stanowej lub wystąpień o bezstanowa usługa) między dwiema domenami, w tym samym poziomie hierarchii."

Załóżmy, że to ograniczenie daje gwarancję "maksymalną różnicę". Ograniczenie dla domenach błędów i uaktualnień uniemożliwia niektórych przenosi lub ustalenia, które naruszają zasady.

Na przykład załóżmy, że istnieje klastra z sześciu węzłów, skonfigurowany z pięcioma domenami błędów i 5 domenami uaktualniania.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | | |N3 | | |
| **UD3** | | | |N4 | |
| **UD4** | | | | |N5 |

Teraz załóżmy, że możemy utworzyć usługę za pomocą **TargetReplicaSetSize** (lub usługi bezstanowej **InstanceCount**) wartość 5. Repliki proponowany N1 N5. W rzeczywistości N6 nigdy nie jest używany niezależnie od tego, jak wiele usług, takich jak to, którą tworzysz. Ale Dlaczego? Spójrzmy na różnicę między bieżący układ i co się stanie, jeśli wybrano N6.

Oto układ, który mamy i łącznej liczby replik dla domeny w domenach błędów i uaktualnień:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** | |R2 | | | |1 |
| **UD2** | | |R3 | | |1 |
| **UD3** | | | |R4 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

Ten układ jest równoważony pod względem węzłów na domenę błędów i domeny uaktualnień. Jest ona również równoważony pod względem liczby replik dla domeny w domenach błędów i uaktualnień. Każda domena ma taką samą liczbę węzłów i taką samą liczbę replik.

Teraz Przyjrzyjmy się co się stanie, jeśli firma Microsoft była używana N6 zamiast N2. Sposób będzie replik dystrybucji następnie?

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R5 | | | | |1 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |2 |0 |1 |1 |1 |- |

Ten układ narusza nasza definicja gwarancji "maksymalną różnicę" ograniczenia domen błędów. FD0 ma dwie repliki FD1 ma wartość zero. Różnica między FD0 i FD1 jest daje w sumie dwa, która jest większa niż maksymalna różnica jednego. Ponieważ nastąpiło naruszenie ograniczenia, Menedżer zasobów klastra nie zezwala na to rozwiązanie. Podobnie jeśli firma Microsoft pobrane, N2 i N6 (zamiast N1 i N2), otrzymamy wynik:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

Ten układ jest równoważone w domenach błędów. Ale teraz narusza ograniczenie domeny uaktualnienia, ponieważ UD0 ma zero repliki i UD1 ma dwa. Ten układ również jest nieprawidłowy i nie będą pobierane przez Menedżer zasobów klastra.

Takie podejście do dystrybucji stanowych replik lub wystąpień o bezstanowa zapewnia najlepsze możliwe odporność na uszkodzenia. Jeśli w jednej domenie ulegnie awarii, minimalnej liczby wystąpień/replik zostanie utracony. 

Z drugiej strony to podejście może być zbyt rygorystyczne i nie zezwalaj klastrowi na korzystanie z wszystkich zasobów. W przypadku niektórych konfiguracji klastra nie można używać niektórych węzłów. Może to spowodować Service Fabric, aby nie umieszczać swoje usługi, komunikaty ostrzegawcze. W poprzednim przykładzie, niektóre węzły klastra nie może być używana (N6 w przykładzie). Nawet jeśli węzły są dodawane do tego klastra (N7 N10), wystąpień/replik zostają umieszczone tylko na N1 — N5 ze względu na ograniczenia w domenach błędów i uaktualnień. 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | |N10 |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | |N9 |N5 |



### <a name="alternative-approach"></a>Informacje o innym podejściu

Menedżer zasobów klastra obsługuje inna wersja tego ograniczenia dla domenach błędów i uaktualnień. Umożliwia ona umieszczania podczas nadal z zagwarantowaniem minimalnego poziomu bezpieczeństwa. Alternatywne ograniczenie może być wyrażona w następujący sposób: "Partycja danej usługi, dystrybucji repliki w domenach należy upewnić się, że partycja nie występują w niej utraciła kworum." Załóżmy, że to ograniczenie daje gwarancję "kworum bezpieczne". 

> [!NOTE]
> Usługi stanowe definiujemy *utraciła kworum* w sytuacji, gdy większość replik partycji nie działają w tym samym czasie. Na przykład jeśli **TargetReplicaSetSize** wynosi pięć, zbiór wszystkie trzy repliki reprezentuje kworum. Podobnie jeśli **TargetReplicaSetSize** to sześć, cztery repliki są niezbędne dla kworum. W obu przypadkach nie więcej niż dwie repliki mogą być wyłączone w tym samym czasie Jeśli partycji chce, aby kontynuować, działa prawidłowo. 
>
> Usługi bezstanowej jest coś takiego jak *utraciła kworum*. Usług bezstanowych w dalszym ciągu działać normalnie, nawet wtedy, gdy w większości przypadków przestaną działać w tym samym czasie. Więc skupimy się na usług stanowych w dalszej części tego artykułu.
>

Wróćmy do poprzedniego przykładu. Za pomocą wersji "kworum bezpieczne" ograniczenia wszystkie trzy układy będą prawidłowe. Nawet jeśli FD0 nie powiodło się w drugim układu lub UD1 nie powiodło się w układzie trzeci, partycji nadal będzie miał kworum. (Większość replik nadal będzie się.) Z tą wersją ograniczenia N6 prawie zawsze może być wykorzystywany.

Podejście "kworum bezpieczne" zapewnia większą elastyczność niż metoda "maksymalną różnicę". Przyczyną jest to, że łatwiej dystrybucje repliki, które są ważne w prawie każdym Topologia klastra. Jednak to podejście nie gwarantuje najlepszą odporność na uszkodzenia właściwości, ponieważ niektóre błędy są gorsza niż inne. 

W najgorszym przypadku wielkości liter większość repliki może być utracone wystąpił błąd w jednej domenie i jednej z replik dodatkowych. Na przykład zamiast konieczności utratę kworum z pięciu replik lub wystąpień awarii trzech, mogą teraz utracić głosów z błędami tylko dwa. 

### <a name="adaptive-approach"></a>Podejście adaptacyjne
Ponieważ oba podejścia mają zalety i słabe strony, wprowadziliśmy adaptacyjne metody, która łączy tych dwóch strategii.

> [!NOTE]
> Jest to domyślne zachowanie, począwszy od usługi Service Fabric w wersji 6.2. 
> 
> Funkcje adaptacyjnego sterowania podejście domyślnie używa logiki "maksymalną różnicę" i przełączniki logiki "kworum bezpieczne" tylko wtedy, gdy jest to konieczne. Menedżer zasobów klastra automatycznie wpadł na strategię, jakiej jest to konieczne, analizując konfiguracji klastra i usług.
> 
> Menedżer zasobów klastra należy korzystać z logiką "na podstawie kworum" dla usługi oba te warunki są spełnione:
>
> * **TargetReplicaSetSize** dla usługi jest podzielny przez liczbę domen błędów i liczba domen uaktualnienia.
> * Liczba węzłów jest mniejsza niż lub równa liczbie domen błędów pomnożona przez liczbę domen uaktualnienia.
>
> Pamiętać, że Menedżer zasobów klastra będzie używać tej metody dla usług stanowych i bezstanowych, mimo że utraciła kworum nie ma znaczenia w przypadku usług bezstanowych.

Wróć do poprzedniego przykładu i przyjęto założenie, że klaster ma teraz osiem węzłów. Nadal trwa konfigurowanie klastra z pięcioma domenami błędów i 5 domenami uaktualniania, a **TargetReplicaSetSize** usługi hostowane w tym klastrze pozostaje pięć. 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | | |N5 |

Ponieważ wszystkie niezbędne warunki są spełnione, Menedżer zasobów klastra użyje logiki "kworum na podstawie" podczas dystrybuowania usługi. Umożliwia to użycie N6 N8. Dystrybucjami możliwości usługi w tym przypadku może wyglądać następująco:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | | | |0 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |2 |1 |1 |0 |1 |- |

Jeśli Twoja usługa **TargetReplicaSetSize** wartość jest ograniczona do czterech (na przykład), Menedżer zasobów klastra zauważą tej zmiany. Zostanie wznowiona z wykorzystaniem logiki "maksymalną różnicę", ponieważ **TargetReplicaSetSize** nie jest już dividable według liczby domen błędów i domenach uaktualniania. W wyniku niektórych przemieszczania repliki miało miejsce do dystrybucji pozostałe cztery repliki w węzłach N1 N5. Dzięki temu logika domeny domeny i uaktualnianie błędów w wersji "maksymalną różnicę" nie jest naruszona. 

W poprzednim układzie Jeśli **TargetReplicaSetSize** wartość jest pięć i N1 zostanie usunięty z klastra, liczba domen uaktualnienia staje się równa 4. Ponownie uruchamia przy użyciu logiki "maksymalną różnicę", ponieważ liczba domen uaktualnienia nie równomiernie rozłożyć usługi Menedżer zasobów klastra **TargetReplicaSetSize** już wartość. W rezultacie replika R1, gdy ponownie kompilowane ma powiązana z N4, tak aby nie nastąpiło naruszenie ograniczenia dla domeny w domenach błędów i uaktualnień.

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |ND |ND |ND |ND |ND |ND |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | |R1 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

## <a name="configuring-fault-and-upgrade-domains"></a>Konfigurowanie domen w domenach błędów i uaktualnień
W przypadku wdrożeń hostowanych na platformie Azure Service Fabric w domenach błędów i domeny uaktualnień są definiowane automatycznie. Usługa Service Fabric zbiera i używa informacji o środowisku platformy Azure.

Tworzenia własnego klastra (lub do uruchomienia określonego topologii w trakcie programowania) można podać domenę błędów i informacje o domenie uaktualnienia, samodzielnie. W tym przykładzie definiujemy węzła dziewięć lokalny klaster projektowy rozciągających się na trzech centrów danych (każdy z trzech stojakami). Ten klaster ma również trzy domeny uaktualnienia rozkładane na tych trzech centrów danych. Poniżej przedstawiono przykład konfiguracji w ClusterManifest.xml:

```xml
  <Infrastructure>
    <!-- IsScaleMin indicates that this cluster runs on one box/one single server -->
    <WindowsServer IsScaleMin="true">
      <NodeList>
        <Node NodeName="Node01" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType01" FaultDomain="fd:/DC01/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node02" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType02" FaultDomain="fd:/DC01/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node03" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType03" FaultDomain="fd:/DC01/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node04" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType04" FaultDomain="fd:/DC02/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node05" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType05" FaultDomain="fd:/DC02/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node06" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType06" FaultDomain="fd:/DC02/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node07" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType07" FaultDomain="fd:/DC03/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node08" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType08" FaultDomain="fd:/DC03/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node09" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType09" FaultDomain="fd:/DC03/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
      </NodeList>
    </WindowsServer>
  </Infrastructure>
```

W tym przykładzie użyto ClusterConfig.json dla autonomicznych wdrożeniach:

```json
"nodes": [
  {
    "nodeName": "vm1",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm2",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm3",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD3"
  },
  {
    "nodeName": "vm4",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm5",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm6",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD3"
  },
  {
    "nodeName": "vm7",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm8",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm9",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD3"
  }
],
```

> [!NOTE]
> Podczas definiowania klastrów za pomocą usługi Azure Resource Manager, Azure przypisuje domeny błędów i uaktualnień. Dlatego ustawia definicji typów węzłów i skalowania maszyn wirtualnych szablonu usługi Azure Resource Manager nie zawierają informacje dotyczące domeny błędów i domeny uaktualnień.
>

## <a name="node-properties-and-placement-constraints"></a>Właściwości węzła i ograniczeniami dotyczącymi umieszczania
Czasami (w rzeczywistości w większości przypadków) należy upewnić się, że niektóre obciążenia uruchamiane tylko na niektóre rodzaje węzłów w klastrze. Na przykład niektóre obciążenia mogą wymagać procesorów GPU lub dyski SSD i inne osoby nie mogą. 

Świetny przykład określania wartości docelowej sprzętu do konkretnych obciążeń jest prawie wszystkie architektury n warstwowej. Niektóre maszyny służyć jako frontonu lub strony aplikacji obsługujących interfejsu API i są widoczne dla klientów lub Internetu. Różnych maszynach, często z innego sprzętu zasobów, wykonuje pracę warstwy obliczenia lub magazyn. Są to zazwyczaj _nie_ bezpośrednio narażonych na klientów lub Internetu. 

Usługi Service Fabric oczekuje, że w niektórych przypadkach konkretnych obciążeń może być konieczne uruchomienie w konfiguracjach określonego sprzętu. Na przykład:

* Istniejącej aplikacji n warstwowa została "podniesiony i przesunięte" w środowisku usługi Service Fabric.
* Obciążenia muszą być uruchamiane w sprzętu ze względów wydajności, skalowania lub zabezpieczeń izolacji.
* Obciążenie powinna zostać odizolowana od innych obciążeń powodów użycie zasad lub zasobu.

Aby obsługiwać te różne konfiguracje, Usługa Service Fabric zawiera tagi, które można zastosować do węzłów. Tagi te są nazywane *— właściwości węzła*. *Ograniczeniami dotyczącymi umieszczania* są instrukcje dołączone do poszczególnych usług, wybranymi dla co najmniej jedną właściwość węzła. Ograniczeniami dotyczącymi umieszczania definiują, gdzie należy uruchamiać usług. Zestaw ograniczeń jest otwarty. Podjąć wszystkie pary klucz/wartość. 

<center>

![Różne obciążenia dla układu klastra][Image5]
</center>

### <a name="built-in-node-properties"></a>Właściwości wbudowanego węzła
Usługa Service Fabric definiuje niektóre domyślne właściwości węzła, które mogą służyć automatycznie, dzięki czemu nie trzeba zdefiniować je. Domyślne właściwości zdefiniowane w każdym węźle to **NodeType** i **NodeName**. 

Na przykład można napisać jako ograniczenie umieszczania `"(NodeType == NodeType03)"`. **Element NodeType** jest często używanych właściwości. Jest to przydatne, ponieważ odpowiada ona 1:1 z typem maszyny. Każdy typ maszyny odnosi się do typu obciążenia w tradycyjnych aplikacji n warstwowej.

<center>

![Ograniczeniami dotyczącymi umieszczania i — właściwości węzła][Image6]
</center>

## <a name="placement-constraints-and-node-property-syntax"></a>Ograniczeniami dotyczącymi umieszczania i składni właściwości węzła 
Wartość określona we właściwości węzła może być ciągiem, wartość logiczna, lub podpisany długo. Instrukcja na usługę nosi nazwę miejsca *ograniczenie* ponieważ ogranicza, których usługa może działać w klastrze. Ograniczenia mogą być Boolean instrukcja, która operuje na właściwości węzła w klastrze. Nieprawidłowa selektory w tych instrukcjach logiczne są:

* Warunkowe sprawdza, czy tworzenie określonej instrukcji:

  | Instrukcja | Składnia |
  | --- |:---:|
  | "jest równe" | "==" |
  | "nie jest równe" | "!=" |
  | "większe niż" | ">" |
  | "większe niż lub równe" | ">=" |
  | "poniżej" | "<" |
  | "mniejsze niż lub równe" | "<=" |

* Wartość logiczna instrukcje dla operacji grupowania i logiczne:

  | Instrukcja | Składnia |
  | --- |:---:|
  | "i" | "&&" |
  | "or" | "&#124;&#124;" |
  | "not" | "!" |
  | "grupy jako pojedynczą instrukcję" | "()" |

Poniżej przedstawiono kilka przykładów podstawowy warunek ograniczający instrukcji:

  * `"Value >= 5"`
  * `"NodeColor != green"`
  * `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

Tylko węzły, w którym ogólną instrukcji ograniczeń umieszczania daje w wyniku "True" może mieć usługi dla niej. Węzły, które nie mają zdefiniowaną właściwość nie jest zgodny wszystkich ograniczeń umieszczania, który zawiera właściwość.

Załóżmy, że następujące właściwości węzła zostały zdefiniowane dla typu węzła w ClusterManifest.xml:

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasSSD" Value="true"/>
        <Property Name="NodeColor" Value="green"/>
        <Property Name="SomeProperty" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

Poniższy przykład pokazuje właściwości węzła zdefiniowane za pomocą ClusterConfig.json autonomicznych wdrożeniach lub Template.json klastrów hostowanymi na platformie Azure. 

> [!NOTE]
> W szablonie usługi Azure Resource Manager typ węzła jest zazwyczaj sparametryzowanych. To powiadomienie będzie wyglądać `"[parameters('vmNodeType1Name')]"` zamiast NodeType01.
>

```json
"nodeTypes": [
    {
        "name": "NodeType01",
        "placementProperties": {
            "HasSSD": "true",
            "NodeColor": "green",
            "SomeProperty": "5"
        },
    }
],
```

Można utworzyć usługi umieszczania *ograniczenia* dla usługi w następujący sposób:

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
serviceDescription.PlacementConstraints = "(HasSSD == true && SomeProperty >= 4)";
// Add other required ServiceDescription fields
//...
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

```PowerShell
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceType -Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementConstraint "HasSSD == true && SomeProperty >= 4"
```

Jeśli wszystkie węzły NodeType01 są prawidłowe, możesz również wybrać ten typ węzła, z ograniczeniem `"(NodeType == NodeType01)"`.

Ograniczeniami dotyczącymi umieszczania usługi może być aktualizowany dynamicznie w czasie wykonywania. Jeśli zachodzi potrzeba, można przenoszenie usługi w klastrze, dodać i usunąć wymagania i tak dalej. Usługi Service Fabric zapewnia, że usługa pozostaje aktualne i dostępne, nawet wtedy, gdy tego rodzaju zmiany zostały wprowadzone.

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

```PowerShell
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

Ograniczeniami dotyczącymi umieszczania są określone dla każdego wystąpienia nazwaną usługę. Aktualizacje zawsze pobierają miejsca (Zastąp) co wcześniej określono.

Definicja klastra definiuje właściwości w węźle. Zmiana właściwości węzła wymaga uaktualnienia w konfiguracji klastra. Uaktualnienie — właściwości węzła wymaga każdego dotyczy węzeł, aby po ponownym uruchomieniu raportu jego nowych właściwości. Usługa Service Fabric zarządza te stopniowe.

## <a name="describing-and-managing-cluster-resources"></a>Opisu i zarządzanie zasobami klastra
Jedno z najważniejszych zadań dowolnego programu orchestrator jest pomagające w zarządzaniu zużycie zasobów w klastrze. Zarządzanie zasobami klastra może oznaczać kilka różnych rzeczy. 

Po pierwsze jest zapewnienie maszyny nie są przeciążone. Oznacza to, upewniając się, że maszyny nie są uruchomione większej liczby usług niż ich może obsłużyć. 

Po drugie istnieje równoważenia i optymalizacji, które mają kluczowe znaczenie dla wydajne działanie usług. Niektóre węzły były gorąca, a inne są zimnych nie może dopuścić do ofert usług ekonomiczne lub wrażliwego na wydajność. Węzły w warstwie gorąca prowadzić do rywalizacji o zasoby i niską wydajnością. Węzły zimnych reprezentują ograniczenia marnowania zasobów i wyższe koszty. 

Usługa Service Fabric reprezentuje zasobów jako *metryki*. Metryki są logicznych lub fizycznych zasobów, które użytkownik chce opisać w usłudze Service Fabric. Metryki przykłady "WorkQueueDepth" lub "MemoryInMb." Aby uzyskać informacji na temat zasobów fizycznych, określające usługi Service Fabric w węzłach, zobacz [nadzór nad zasobami](service-fabric-resource-governance.md). Aby uzyskać informacje na temat konfigurowania niestandardowych metryk i ich zastosowań, zobacz [w tym artykule](service-fabric-cluster-resource-manager-metrics.md).

Metryki różnią się od właściwości węzła i ograniczeniami dotyczącymi umieszczania. Właściwości węzła są statyczne deskryptorów w poszczególnych węzłach. Metryki Opisz zasoby, które mają węzłów i że korzysta z usług podczas uruchamiania w węźle. Właściwość węzła może być **HasSSD** i może być ustawiony na wartość true lub false. Ilość miejsca dostępnego w tym dyski SSD i jaka jest używane przez usługi będzie metryki, takie jak "DriveSpaceInMb." 

Podobnie jak właściwości węzła, i ograniczeniami dotyczącymi umieszczania Menedżer zasobów klastra usługi w sieci szkieletowej nie zrozumieć, jakie nazwy średniej metryki. Nazwy metryki są tylko ciągi. Jest dobrą praktyką deklarowanie jednostki jako część nazwy metryk, które tworzysz, gdy mogą być niejednoznaczne.

## <a name="capacity"></a>Pojemność
Jeśli wyłączysz wszystkich zasobów *równoważenia*, Menedżer zasobów klastra usługi w sieci szkieletowej będą nadal upewnij się, że żaden węzeł nie przechodzi przez jej pojemność. Zarządzanie przepełnienia pojemności jest możliwe, chyba że klaster jest zapełniony lub obciążenia jest większy niż dowolnego węzła. Pojemność jest kolejnym *ograniczenie* używany Menedżer zasobów klastra, aby zrozumieć, jaka zasobów, gdy węzeł ma. Pozostała pojemność również jest śledzona dla klastra jako całości. 

Pojemność i użycie na poziomie usługi są wyrażane pod względem metryk. Na przykład metryka może być "ClientConnections" i węzeł może być pojemności dla "ClientConnections" z 32 768. Inne węzły mogą mieć inne ograniczenia. Usługi uruchomione na tym węźle można Załóżmy, że obecnie zużywa 32,256 metryki "ClientConnections."

Podczas wykonywania Menedżer zasobów klastra śledzi pozostałe pojemności w klastrze, a w węzłach. Aby śledzić wydajność, Menedżer zasobów klastra odejmuje użycia każdej z tych usług przy użyciu pojemności węzła, gdzie usługa jest uruchamiana. Dzięki tym informacjom Menedżer zasobów klastra można ustalić gdzie umieścić lub Przenieś repliki tak, aby węzły nie są kierowane przez pojemność.

<center>

![Węzły klastra i pojemność][Image7]
</center>

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
ServiceLoadMetricDescription metric = new ServiceLoadMetricDescription();
metric.Name = "ClientConnections";
metric.PrimaryDefaultLoad = 1024;
metric.SecondaryDefaultLoad = 0;
metric.Weight = ServiceLoadMetricWeight.High;
serviceDescription.Metrics.Add(metric);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

```PowerShell
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("ClientConnections,High,1024,0)
```

Możesz zobaczyć pojemności zdefiniowany w manifeście klastra. Oto przykład ClusterManifest.xml:

```xml
    <NodeType Name="NodeType03">
      <Capacities>
        <Capacity Name="ClientConnections" Value="65536"/>
      </Capacities>
    </NodeType>
```

Oto przykład pojemności zdefiniowanych za pomocą ClusterConfig.json autonomicznych wdrożeniach lub Template.json, w przypadku klastrów hostowanymi na platformie Azure: 

```json
"nodeTypes": [
    {
        "name": "NodeType03",
        "capacities": {
            "ClientConnections": "65536",
        }
    }
],
```

Usługa ładowania często zmieniają się dynamicznie. Załóżmy, że obciążenie repliki "ClientConnections" zmieniła się z 1024 do 2048. Węzeł, który był uruchomiony na następnie ma pojemność tylko 512 pozostała w przypadku tej metryki. Teraz ta replika lub położenia wystąpienia jest nieprawidłowa, ponieważ nie ma wystarczającej ilości miejsca, w tym węźle. Menedżer zasobów klastra musi przełączyć węzeł ponownie poniżej pojemności. Zmniejsza to obciążenie w węźle będącym przeciążone, przenosząc przynajmniej jednej z replik lub wystąpień tego węzła do innych węzłów. 

Menedżer zasobów klastra próbuje zminimalizować koszty ruchu repliki. Możesz dowiedzieć się więcej [koszt przeniesienia](service-fabric-cluster-resource-manager-movement-cost.md) i o [ponowne równoważenie strategie i reguły](service-fabric-cluster-resource-manager-metrics.md).

## <a name="cluster-capacity"></a>Pojemność klastra
Jak Menedżer zasobów klastra usługi Service Fabric zachować ogólną klastra jest przepełniony? Za pomocą dynamicznego obciążenia nie istnieje wiele, co można zrobić. Usługi mogą mieć ich wzrost obciążenia, niezależnie od akcji, które przyjmuje Menedżer zasobów klastra. W rezultacie klastra przy użyciu już dziś sporo miejsca może być za słaby, jeśli istnieje skok jutro. 

Formanty w programie Menedżer zasobów klastra zapobiec problemów. Pierwszą rzeczą, jaką można zrobić to zapobiega tworzeniu nowych obciążeń, które mogłoby spowodować klastra zapełnił się.

Załóżmy, że Utwórz usługę bezstanową i ma pewne obciążenia skojarzonych z nim. Usługa dba o metryki "DiskSpaceInMb". Usługa zużyje pięciu jednostek "DiskSpaceInMb" dla każdego wystąpienia usługi. Chcesz utworzyć trzy wystąpienia usługi. Oznacza to, że potrzebujesz 15 jednostek "DiskSpaceInMb" znajdować się w klastrze nawet tworzenie wystąpień tych usług.

Menedżer zasobów klastra stale oblicza pojemność i użycie wszystkie metryki, dzięki czemu można określić, pozostałe pojemności w klastrze. Jeśli nie ma wystarczającej ilości miejsca, Menedżer zasobów klastra odrzuca wywołanie, aby utworzyć usługę.

Ponieważ wymagane jest tylko 15 jednostki będą dostępne, można przydzielić miejsce na wiele różnych sposobów. Na przykład może istnieć pozostałe jednostki pojemności na 15 różnych węzłach lub trzy pozostałe jednostki pojemności w pięciu różnych węzłach. Jeśli Menedżer zasobów klastra można zmienić kolejność rzeczy, dzięki czemu na trzy węzły są dostępne pięciu jednostek, umieszcza usługę. Ponowne rozmieszczanie klastra zwykle jest możliwe chyba, że klaster jest prawie pełna lub istniejących usług nie mogą być konsolidowane przyczyny.

## <a name="buffered-capacity"></a>Buforowane pojemności
Pojemność buforowanego jest inna funkcja Menedżer zasobów klastra. Umożliwia ona rezerwacji część ogólnej wydajności węzła. Ten bufor pojemności jest używany tylko po to, aby umieścić usług podczas uaktualnień i awarie węzłów. 

Pojemność buforowanego określono globalnie na metrykę dla wszystkich węzłów. Wartość, która wybierz dla rezerwowanie pojemności jest funkcją liczby domen domenach błędów i uaktualnień, które masz w klastrze. Więcej domen w domenach błędów i uaktualnień oznacza to, czy możesz wybrać mniejszą liczbę dla Twojego buforowaną pojemności. Jeśli masz więcej domen, można oczekiwać, że mniejszych ilości klastra będzie dostępny podczas uaktualnienia. Określanie buforowanego pojemność ma sens tylko wtedy, gdy określono również wydajność węzła dla metryki.

Oto przykład sposobu określania buforowanego pojemności w ClusterManifest.xml:

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="SomeMetric" Value="0.15" />
            <Parameter Name="SomeOtherMetric" Value="0.20" />
        </Section>
```

Oto przykład sposobu określania buforowanego pojemności za pośrednictwem ClusterConfig.json dla autonomicznych wdrożeniach lub Template.json hostowanymi na platformie Azure klastrów:

```json
"fabricSettings": [
  {
    "name": "NodeBufferPercentage",
    "parameters": [
      {
          "name": "SomeMetric",
          "value": "0.15"
      },
      {
          "name": "SomeOtherMetric",
          "value": "0.20"
      }
    ]
  }
]
```

Tworzenie nowych usług zakończy się niepowodzeniem, gdy klaster jest buforowana pojemności dla metryki. Zapobieganie tworzeniu nowych usług, aby zachować bufor gwarantuje, że uaktualnienia i błędy nie powodują węzły za pojemność. Pojemność buforowanego jest opcjonalne, ale jest zalecane w przypadku dowolnego klastra, który definiuje wydajności dla metryki.

Menedżer zasobów klastra udostępnia te informacje obciążenia. Dla każdego metryki informacje te obejmują: 
- Ustawienia pojemności buforowanego.
- Całkowita pojemność.
- Bieżące użycie.
- Czy wszystkie metryki uznaje się za równoważone, czy nie.
- Statystyka odchylenia standardowego.
- Węzły, które ma największą i najmniejszą obciążenia.  
  
Poniższy kod przedstawia przykład te dane wyjściowe:

```PowerShell
PS C:\Users\user> Get-ServiceFabricClusterLoadInformation
LastBalancingStartTimeUtc : 9/1/2016 12:54:59 AM
LastBalancingEndTimeUtc   : 9/1/2016 12:54:59 AM
LoadMetricInformation     :
                            LoadMetricName        : Metric1
                            IsBalancedBefore      : False
                            IsBalancedAfter       : False
                            DeviationBefore       : 0.192450089729875
                            DeviationAfter        : 0.192450089729875
                            BalancingThreshold    : 1
                            Action                : NoActionNeeded
                            ActivityThreshold     : 0
                            ClusterCapacity       : 189
                            ClusterLoad           : 45
                            ClusterRemainingCapacity : 144
                            NodeBufferPercentage  : 10
                            ClusterBufferedCapacity : 170
                            ClusterRemainingBufferedCapacity : 125
                            ClusterCapacityViolation : False
                            MinNodeLoadValue      : 0
                            MinNodeLoadNodeId     : 3ea71e8e01f4b0999b121abcbf27d74d
                            MaxNodeLoadValue      : 15
                            MaxNodeLoadNodeId     : 2cc648b6770be1bc9824fa995d5b68b1
```

## <a name="next-steps"></a>Kolejne kroki
* Aby uzyskać informacji na temat architektury i przepływem informacji w ramach klastra usługi Resource Manager, zobacz [omówienie architektury Menedżer zasobów klastra](service-fabric-cluster-resource-manager-architecture.md).
* Definiowanie defragmentacji metryk jest jednym ze sposobów Konsolidacja obciążenia w węzłach zamiast rozkładanie go. Aby dowiedzieć się, jak skonfigurować defragmentacji, zobacz [defragmentacji metryk i obciążenia w usłudze Service Fabric](service-fabric-cluster-resource-manager-defragmentation-metrics.md).
* Zacznij od początku i [zapoznaj się z wprowadzeniem do usługi Service Fabric Menedżer zasobów klastra](service-fabric-cluster-resource-manager-introduction.md).
* Aby dowiedzieć się, jak Menedżer zasobów klastra zarządza i równoważy obciążenie w klastrze, zobacz [równoważenie klastra usługi Service Fabric](service-fabric-cluster-resource-manager-balancing.md).

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png
