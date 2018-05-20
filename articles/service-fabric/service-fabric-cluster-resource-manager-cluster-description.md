---
title: Opis elementu klastra Menedżera zasobów klastra | Dokumentacja firmy Microsoft
description: Określając domen błędów, uaktualnienia domen, właściwości węzła i pojemności węzła dla Menedżera zasobów klastra, opisujący klastra sieci szkieletowej usług.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 55f8ab37-9399-4c9a-9e6c-d2d859de6766
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 13ee238580d645f3e727090bc0e0275b36bdb225
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="describing-a-service-fabric-cluster"></a>Opisujące klastra sieci szkieletowej usług
Usługa sieci szkieletowej klastra Resource Manager zapewnia kilka mechanizmów opisujące klastra. W czasie wykonywania Menedżer zasobów klastra używa tych informacji, aby zapewnić wysoką dostępność usługi działające w klastrze. Wymuszając te reguły ważne również próbuje optymalizacji zużycia zasobów w klastrze.

## <a name="key-concepts"></a>Kluczowe pojęcia
Menedżer zasobów klastra obsługuje kilka funkcji, które opisują klastra:

* Domen błędów
* Domen uaktualnienia
* Właściwości węzła
* Możliwości węzła

## <a name="fault-domains"></a>Domeny błędów
Domeny błędów jest części skoordynowanego awarii. Jedna maszyna jest domeny błędów (od momentu jego może zakończyć się niepowodzeniem na własną dla różnych przyczyn, na wypadek awarii zasilania na awarie dysku zły oprogramowania układowego kart Sieciowych). Urządzenia podłączone do tego samego przełącznika Ethernet znajdują się w tej samej domenie błędów maszyn udostępniania jednego źródła zasilania lub w pojedynczej lokalizacji. Ponieważ jest to fizyczne dla błędów sprzętu nakładanie się domen błędów są z założenia hierarchii i są reprezentowane jako identyfikatory URI w sieci szkieletowej usług.

Należy pamiętać, że domen błędów są poprawnie skonfigurowane od sieci szkieletowej usług używa tych informacji do bezpiecznie umieść usług. Sieć szkieletowa usług nie chcesz umieścić usługi w taki sposób, że utraty domeny błędów (spowodowane niepowodzeniem niektórych składnika) powoduje, że usługi przejść w dół. Na platformie Azure środowisko sieci szkieletowej usług korzysta z domeny błędów informacji dostarczonych przez środowisko Aby poprawnie skonfigurować węzły w klastrze w Twoim imieniu. Dla usługi sieci szkieletowej autonomicznej domen błędów są zdefiniowane w czasie, że klaster jest skonfigurowany 

> [!WARNING]
> Jest ważne, że informacje domeny błędów dostarczone do usługi sieci szkieletowej są prawidłowe. Na przykład załóżmy, że węzły klastra usługi sieć szkieletowa działają wewnątrz 10 maszyn wirtualnych na hostach fizycznych pięć. W takim przypadku chociaż występują 10 maszyn wirtualnych, są tylko 5 różnych (najwyższego poziomu) domeny błędów. Udostępnianie tego samego hosta fizycznego powoduje, że maszyny wirtualne na współużytkowanie tej samej domenie błędów głównego, ponieważ maszyn wirtualnych występuje błąd skoordynowany sposób, jeśli ich hosta fizycznego zakończy się niepowodzeniem.  
>
> Sieć szkieletowa usług oczekuje domeny błędów węzła nie należy zmieniać. Inne mechanizmy zapewniania wysokiej dostępności maszyn wirtualnych, takie jak [maszyn wirtualnych wysokiej dostępności](https://technet.microsoft.com/library/cc967323.aspx) może powodować konflikty z sieci szkieletowej usług, ponieważ używają one przezroczysty migracji maszyn wirtualnych z jednego hosta na inny. Te mechanizmy ponowne konfigurowanie lub nie Powiadamiaj uruchomiony kod w ramach maszyny Wirtualnej. Tak, są one **nieobsługiwane** jako środowiska do uruchamiania usługi sieć szkieletowa klastrów. Usługa sieci szkieletowej powinna być stosowanej technologii tylko wysokiej dostępności. Mechanizmy, takie jak migracja maszyny Wirtualnej sieci SAN, lub inne osoby nie są konieczne. Jeśli używany w połączeniu z usługi Service Fabric tych mechanizmów _zmniejszyć_ aplikacji dostępność i niezawodność, ponieważ ich wprowadzenie dodatkowej złożoności, Dodaj scentralizowane źródła błędu i wykorzystania niezawodność i Strategie dostępności, które powodują konflikt z aliasami w sieci szkieletowej usług. 
>
>

Na poniższym rysunku możemy kolor wszystkich jednostek, które przyczyniają się do domen błędów i wyświetlić listę wszystkich różnych domen błędów wynikających. W tym przykładzie mamy centrów danych ("DC"), stojakami ("R") i bloki ("B"). Powrotne Jeśli każdy blok zawiera więcej niż jednej maszyny wirtualnej, mogą występować innej warstwy w hierarchii domeny błędów.

<center>
![Węzły zorganizowane za pośrednictwem domen błędów][Image1]
</center>

W czasie wykonywania Menedżer zasobów klastra sieci szkieletowej usług uwzględnia domen błędów w klastrze i planów układów. Są dystrybuowane repliki stanowego lub bezstanowego wystąpień dla danej usługi, dzięki czemu są one w oddzielnych domen błędów. Dystrybucję usługę domen błędów gwarantuje, że dostępność usługi nie zostanie naruszony, gdy domeny błędów nie powiodło się na dowolnym poziomie hierarchii.

Menedżer zasobów klastra usługi sieć szkieletowa nie szczególną uwagę liczbę warstw w hierarchii domeny błędów. Jednak próbuje upewnij się, że utraty jednej części hierarchii bez wpływu na usługi uruchomione w nim. 

Najlepiej ma taką samą liczbę węzłów na każdym poziomie głębokość hierarchii domeny błędów. W przypadku równowagi w klastrze "drzewa" domen błędów utrudni dla Menedżera zasobów klastra do ustalenia najlepsze alokacji usług. Układy domen błędów imbalanced oznaczają, że utratę niektórych domen wpływ dostępności usług więcej niż innych domen. W związku z tym Menedżer zasobów klastra jest podarte między dwa cele: chce używać na komputerach w domenie "duże" przez umieszczenie na nich usługi i chce umieścić usług w innych domenach, dzięki czemu utrata domeny nie powoduje problemów. 

Jak wyglądają imbalanced domen? Na poniższym diagramie zostanie przedstawiony dwóch układów innego klastra. W pierwszym przykładzie węzły są równomiernie rozłożone na domen błędów. W drugim przykładzie jednej domenie błędów ma wiele więcej węzłów niż innych domen błędów. 

<center>
![Dwa układów innego klastra][Image2]
</center>

Na platformie Azure Wybór domeny błędów zawiera węzeł odbywa się. Jednak w zależności od liczby węzłów, które należy udostępnić można nadal na końcu domen błędów z większą liczbę węzłów w je od innych. Na przykład, że masz pięć domen błędów w klastrze, ale udostępniać siedmiu węzłów dla danego NodeType. W takim przypadku dwóch pierwszych domen błędów końcowa więcej węzłów. Jeśli będziesz kontynuować wdrażanie więcej elementów NodeType z tylko kilka wystąpień, pogarsza się wraz z pobiera problem. Z tego powodu ma zalecane liczby węzłów w każdym węźle typ jest wielokrotnością liczby domen błędów.

## <a name="upgrade-domains"></a>Domen uaktualnienia
Domeny uaktualnienia są innego funkcja, która pomaga zapoznać się z układem klastra Menedżera zasobów usługi sieć szkieletowa klastra. Domen uaktualnienia Zdefiniuj zestaw węzłów, które zostaną uaktualnione w tym samym czasie. Domen uaktualnienia Pomoc Menedżera zasobów klastra, zrozumieć i organizowania operacje zarządzania, takie jak uaktualnienia.

Domen uaktualnienia są znacznie, takich jak domen błędów, jednak z kilku podstawowych różnic. Najpierw obszarów awarie sprzętowe skoordynowanego Definiowanie domen błędów. Domen uaktualnienia z drugiej strony są definiowane przez zasady. Pobierz zdecydować, ile, zamiast go dyktowanie przez środowisko. Może mieć dowolną liczbę domen uaktualnienia, jak węzłów. Inna różnica między domenach awarii i uaktualniania domen jest uaktualnienia domen nie są hierarchicznej. Zamiast tego są one bardziej przypominają prostych tagów. 

Poniższy diagram przedstawia trzy domeny uaktualnienia są rozkładane na trzy domen błędów. Przedstawia on także jeden możliwe umieszczania dla trzech różnych repliki usługi stanowej, gdzie każdy kończy się w różnych usterek i domen uaktualnienia. Tego rozmieszczenia umożliwia utraty domeny błędów w trakcie wykonywania uaktualnienia usługi oraz jeszcze jedna kopia kodu i danych.  

<center>
![Umieszczanie o domenach awarii i uaktualniania][Image3]
</center>

Brak zalet i wad mającej dużej liczby domen uaktualnienia. Więcej domen uaktualnienia oznacza, że każdy krok uaktualniania jest bardziej szczegółowe i w związku z tym wpływa na mniejszą liczbę węzłów lub usług. W związku z tym usług mniej trzeba przenieść jednocześnie, wprowadzenie mniej zmian w systemie. Jest to prawdopodobnie do poprawy niezawodności, ponieważ jest mniejsza usługi wpływ jakikolwiek problem wprowadzone podczas uaktualnienia. Więcej domen uaktualnienia również oznacza, że konieczne buforu mniej dostępnych w innych węzłach wpływ uaktualnienia obsługi. Na przykład jeśli masz pięć domen uaktualnienia, węzły w każdym są Obsługa około 20% ruchu. Jeśli trzeba wyłączyć tej domeny uaktualnienia do uaktualnienia, aby obciążenie zwykle należy go w innym miejscu. Ponieważ masz cztery pozostałe domeny uaktualnienia, każdy musi mieć miejsce na około 5% łącznie dla ruchu. Więcej domen uaktualnienia oznacza, że należy mniej buforu na węzłach w klastrze. Rozważmy na przykład, jeśli zamiast tego masz 10 domen uaktualnienia. W takim przypadku każdy UD czy tylko obsługiwał około 10% całkowitej ruchu. Podczas uaktualniania kroki do klastra, każdej domeny, tylko musi wystarczająca ilość miejsca do około 1.1% łącznie dla ruchu. Zazwyczaj więcej domen uaktualnienia pozwala na uruchamianie węzły na zwiększenie użycia, ponieważ należy pojemności mniejszej zastrzeżone. To samo dotyczy dla domen błędów.  

Wadą podwyższonego posiadanie wielu domen uaktualnienia jest uaktualnień często trwać dłużej. Sieć szkieletowa usług oczekuje w krótkim czasie po zakończeniu uaktualnienia domeny i sprawdza przed rozpoczęciem uaktualniania do następnej. Te opóźnienia włączyć wykrywanie problemów wprowadzone podczas uaktualnienia przed uaktualnianie będzie kontynuowane. Zależności jest dopuszczalne, ponieważ uniemożliwia nieprawidłowych zmian w czasie wpływających na zbyt dużej ilości usługi.

Za mało domen uaktualnienia ma wiele ujemna efekty uboczne — podczas każdej poszczególne domeny uaktualnienia nie działa i uaktualniany duża część ogólną wydajność jest niedostępny. Na przykład jeśli masz tylko trzy domeny uaktualnienia są tworzone w dół około 1/3 ogólnej usługi lub wydajność klastra naraz. Jednocześnie o tak wiele usług w dół jest pożądane, ponieważ muszą mieć możliwości wystarczające w pozostałej części klastra do obsługi obciążenia. Obsługa tego buforu oznacza, że podczas normalnego działania te węzły są załadowane mniejsze niż w przeciwnym razie. Zwiększa to koszt usługi.

Nie ma żadnego rzeczywistych limitu całkowitą liczbę błędów lub domen uaktualnienia w środowisku lub ograniczeń jak zachodziły na siebie. Inaczej mówiąc, istnieje kilka typowych wzorców:

- Domena awarii oraz domen uaktualnienia mapowane 1:1
- Jedną domenę uaktualnienia na węzeł (fizycznych lub wirtualnych wystąpienie systemu operacyjnego)
- Model "rozłożone" lub "macierzy", gdzie domenach awarii i uaktualniania domeny tworzą macierzy z komputerów z systemem zwykle dół ukośne podziały

<center>
![Błąd i układy domena uaktualnienia][Image4]
</center>

Ma nie najlepiej odpowiedzieć wyboru układu, aby wybrać, każdy ma kilka zalet i wad. Na przykład 1FD:1UD model jest prosta konfiguracja. Wartość 1 na modelu węzła do domeny uaktualnienia jest najbliższe osób, które są używane do. Podczas uaktualniania każdego węzła jest aktualizowana niezależnie. Efekt jest podobny do jak małe zestawy maszyn zostały uaktualnione ręcznie w przeszłości. 

Najbardziej typowe model jest macierzy FD/UD, gdzie FDs i UDs tworzą tabeli i węzły są umieszczane uruchamianie wzdłuż przekątnej. Jest to model używany domyślnie w klastrach usługi sieć szkieletowa na platformie Azure. W przypadku klastrów z węzłami wiele wszystko, co kończy się wyszukiwanie like wzór dense macierzy powyżej.

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>Ograniczenia usterek i domeny uaktualnienia i efekty
### <a name="default-approach"></a>*Podejście domyślne*
Domyślnie Menedżer zasobów klastra zachowuje równoważone usterek i domen uaktualnienia usługi. Jest to modelowane jako [ograniczenia](service-fabric-cluster-resource-manager-management-integration.md). Stany ograniczenia usterek i domeny uaktualnienia: "dla partycji danej usługi, nigdy nie należy różnicy większej niż jeden liczby obiektów usługi (repliki usługi stanowej lub wystąpienia usługi bezstanowej) między dwoma domenami na tym samym poziomie w hierarchii". Załóżmy, że to ograniczenie zapewnia gwarancję "maksymalnego różnica". Ograniczenie usterek i domeny uaktualnienia zapobiega niektórych przenosi i ustalenia, które naruszają zasady podanych powyżej. 

Oto przykład. Załóżmy, że mamy klastra z sześciu węzłów skonfigurowany z pięciu domen błędów i pięć domen uaktualnienia.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | | |N3 | | |
| **UD3** | | | |N4 | |
| **UD4** | | | | |N5 |

*Konfiguracja 1*

Teraz załóżmy, że firma Microsoft Tworzenie usługi z TargetReplicaSetSize (lub dla usługi bezstanowej jako wartość InstanceCount) pięć. Ziemia replik na N1 N5. W rzeczywistości N6 nie jest nigdy używane niezależnie od tego, ile usługi, takie jak ta, którą utworzysz. Ale Dlaczego? Przyjrzyjmy się różnica między bieżący układ i co się stanie, jeśli wybrano opcję N6.

Poniżej przedstawiono układu dotarliśmy i całkowitej liczby replik na usterek i domeny uaktualnienia:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** | |R2 | | | |1 |
| **UD2** | | |R3 | | |1 |
| **UD3** | | | |R4 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

*Układ 1*


Ten układ jest równoważony pod względem węzłów na domenie błędów i domeny uaktualnienia. Jest on również równoważony pod względem liczby replik na usterek i domeny uaktualnienia. Każda domena ma taką samą liczbę węzłów i taką samą liczbę replik.

Teraz Przyjrzyjmy się co się stanie, jeśli ma użyliśmy N6 zamiast N2. Jak będzie replik rozdzielona następnie?

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R5 | | | | |1 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |2 |0 |1 |1 |1 |- |

*Układ 2*


Ten układ narusza naszych definicji gwarancji "maksymalnego różnica" ograniczenia domeny błędów. FD0 ma dwie repliki podczas FD1 ma wartość zero, co różnica między FD0 i FD1 sumę dwóch, która jest większa niż maksymalna różnica jednego. Ponieważ nastąpiło naruszenie ograniczenia, Menedżer zasobów klastra nie zezwala na to rozmieszczenie. Podobnie jeśli mamy pobrane N2 i N6 (zamiast N1, jak i N2) możemy uzyskać:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

*Układ 3*


Ten układ jest równoważony pod względem domen błędów. Jednak obecnie go narusza ograniczenie domeny uaktualnienia ponieważ UD0 nie zero replik UD1 ma dwa. W związku z tym ten układ również jest nieprawidłowy i nie można pobrać przez Menedżera zasobów klastra.

Takie podejście do dystrybucji replik stanowego lub bezstanowego wystąpień zapewnia najlepsze możliwe odporność na uszkodzenia. W przypadku systemowi jedną domenę, minimalnej liczby replikami/wystąpień jest utracone. 

Z drugiej strony takie podejście może być zbyt rygorystyczne i nie zezwalaj klastrowi na korzystanie z wszystkich zasobów. W przypadku niektórych konfiguracji klastra nie można używać niektórych węzłów. Może to prowadzić do sieci szkieletowej usług nie wprowadzenie do usług, co powoduje komunikaty ostrzegawcze. W poprzednim przykładzie niektóre węzły klastra nie może być używane (N6 w danym przykładzie). Nawet jeśli do tego klastra (N7 — N10) należy dodać węzłów, repliki/wystąpień będzie można umieścić tylko w N1 — N5 ze względu na ograniczenia usterek i domeny uaktualnienia. 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | |N10 |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | |N9 |N5 |

*Konfiguracja 2*


### <a name="alternative-approach"></a>*Informacje o innym podejściu*

Menedżer zasobów klastra obsługuje inna wersja tego ograniczenia awarii i uaktualniania domeny, dzięki czemu umieszczania nadal zapewniając minimalnego poziomu bezpieczeństwa. Alternatywne ograniczenia usterek i domeny uaktualnienia może być wyrażona w następujący sposób: "Dla partycji danej usługi dystrybucji repliki między domenami powinien upewnij się, że partycji problem występuje w przypadku utraty kworum". Załóżmy, że to ograniczenie zapewnia gwarancję "bezpieczne kworum". 

> [!NOTE]
>Dla usługi stanowej, definiujemy *utraty kworum* w sytuacji, gdy większość replik partycji działają w tym samym czasie. Na przykład jeśli TargetReplicaSetSize jest pięć, zbiór wszystkie trzy repliki reprezentuje kworum. Podobnie jeśli wartość TargetReplicaSetSize 6, czterech replik są niezbędne dla kworum. W obu przypadkach nie więcej niż dwóch replik można być wyłączone w tym samym czasie Jeśli partycji chce nadal działa prawidłowo. Dla usługi bezstanowej, nie istnieje coś takiego jak *utraty kworum* jako conitnue usług bezstanowych do functionate normalnie, nawet jeśli w większości przypadków przestaną działać jednocześnie. W związku z tym firma Microsoft będzie skupić się na stanowych usług w pozostałej części tekstu.
>

Przejdź wstecz do poprzedniego przykładu. W wersji "bezpiecznej kworum" ograniczenia wszystkie trzy układy danego będzie ważny. Jest to spowodowane nawet wtedy, gdy będzie błąd FD0 w drugim układu lub UD1 w układzie trzeci, partycji nadal będzie miał kworum (większość jego replik nadal będą się). Z tą wersją ograniczenia N6 prawie zawsze może zostać użyte.

Podejście "kworum bezpieczne" zapewniają większą elastyczność niż metoda "maksymalnego różnica" jako łatwiej znaleźć dystrybucje replik, które są prawidłowe w praktycznie każdej Topologia klastra. Jednak takie podejście nie może zagwarantować błędów najlepsze cechy tolerancji ponieważ niektóre błędy są gorsza niż inne. W scenariusz większość replik mogą zostać utracone z błędem w jednej domenie i jednej replice dodatkowe. Na przykład zamiast konieczności utratę kworum z 5 repliki lub wystąpienia błędów 3, można teraz utratę większości z błędami dwóch. 

### <a name="adaptive-approach"></a>*Podejście adaptacyjną*
Ponieważ obu podejść mocne i słabe strony, dodaliśmy adaptacyjną metody, która łączy tych dwóch strategii.

> [!NOTE]
>Są to domyślne zachowanie, począwszy od usługi sieć szkieletowa wersję 6.2. 
>
Podejście adaptacyjną domyślnie używa logiki "maksymalnego różnica" i przełączniki logiki "kworum bezpieczne" tylko wtedy, gdy jest to konieczne. Menedżer zasobów klastra automatycznie liczb dotyczących strategii, która jest niezbędne, analizując konfiguracji klastra i usług. Dla danej usługi: *przypadku podzielna przez liczbę domen błędów i liczba domen uaktualnienia TargetReplicaSetSize **i** liczba węzłów jest mniejsza niż lub równa (liczba domen błędów) * ( Liczba domen uaktualnienia), należy korzystać z Menedżera zasobów klastra logiki "na podstawie kworum" dla tej usługi.* Przy tym pamiętać, że Menedżera zasobów klastra będzie używać tej metody zarówno bezstanowych i stanowych usług, mimo utraty kworum nie są istotne dla usług bezstanowych.

Wróć do poprzedniego przykładu i założono, że klaster ma teraz 8 węzłów (klaster nadal jest skonfigurowany z pięciu domen błędów i pięć domen uaktualnienia i TargetReplicaSetSize usług hostowanych na tym pozostaje klastra, pięć). 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | | |N5 |

*Konfiguracja 3*

Ponieważ wszystkie niezbędne warunki są spełnione, Menedżer zasobów klastra korzystają usługi dystrybucji logikę "na podstawie kworum". Dzięki temu obciążenie N6 — N8. Jeden dystrybucji usługę w tym przypadku może wyglądać jak:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | | | |0 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |2 |1 |1 |0 |1 |- |

*Układ 4*

W przypadku usługi TargetReplicaSetSize cztery (na przykład), Menedżer zasobów klastra należy zauważyć, że zmiany i wznowić przy użyciu logiki "maksymalnego różnica", ponieważ nie TargetReplicaSetSize dividable przez liczbę FDs UDs już. W związku z tym niektóre przeniesień repliki zostanie przeprowadzona w celu dystrybucji pozostałych czterech replik w węzłach N1 N5 tak, aby nie naruszenia wersja "maksymalnego różnica" Logika domeny w domenie awarii i uaktualniania. 

Wyszukiwania wstecz na czwarte układ i TargetReplicaSetSize pięć. Jeśli N1 zostanie usunięty z klastra, liczba domen uaktualnienia staje się 4. Menedżer zasobów klastra uruchamia ponownie, liczba UDs równomiernie nie dzielenia TargetReplicaSetSize usługi już przy użyciu logiki "maksymalnego różnica". W związku z tym repliki R1, podczas tworzenia ponownie, musi trafić na N4 tak, aby nie naruszenia usterek i ograniczenia domeny uaktualnienia.

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |ND |ND |ND |ND |ND |ND |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | |R1 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

*Układ 5*

## <a name="configuring-fault-and-upgrade-domains"></a>Konfigurowanie usterek i domen uaktualnienia
Definiowanie domen błędów i uaktualnienia domeny odbywa się automatycznie w Azure obsługiwane wdrożenia usługi sieć szkieletowa usług. Sieć szkieletowa usług przejmuje i używa informacji o środowisku z platformy Azure.

Jeśli tworzenia własnego klastra (lub chcesz uruchomić określonego topologii w rozwoju), możesz podać informacje o domenie błędów i domeny uaktualnienia samodzielnie. W tym przykładzie definiujemy dziewięć węzła lokalnego klastra projektowego obejmującej trzy "centrów danych" (każdy z trzech stojakami). Ten klaster ma również trzy domeny uaktualnienia rozłożone na tych trzech centrów danych. Przykład konfiguracji znajduje się poniżej: 

ClusterManifest.xml

```xml
  <Infrastructure>
    <!-- IsScaleMin indicates that this cluster runs on one-box /one single server -->
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

za pomocą pliku ClusterConfig.json we wdrożeniach autonomicznych

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
> Podczas definiowania klastrów za pomocą usługi Azure Resource Manager, domenach awarii i uaktualniania domeny są przypisywane przez platformę Azure. W związku z tym definicji typów węzłów i zestawy skalowania maszyny wirtualnej w szablonie usługi Azure Resource Manager nie obejmuje domena awarii lub uaktualnienia domeny.
>

## <a name="node-properties-and-placement-constraints"></a>Właściwości węzła i ograniczenia dotyczące umieszczania
Czasami (w rzeczywistości w większości przypadków) możesz zacząć aby mieć pewność, że niektórych obciążeń uruchomić tylko na pewnych typów węzłów w klastrze. Na przykład niektóre obciążenie może wymagać GPU lub dyski SSD, a niektóre nie. Jest bardzo przykład docelowych sprzętu do określonego obciążenia jest niemal każdego tam n warstwowa architektura. Niektóre maszyny frontonu lub obsługująca strony aplikacji interfejsu API i są widoczne dla klientów lub Internetu. Różnych maszynach, często z zasoby sprzętowe wykonuje pracę warstwy obliczeniowej i magazynu. Są to zazwyczaj _nie_ bezpośrednio widoczne dla klientów lub Internetu. Sieć szkieletowa usług oczekuje, że istnieją przypadki, w którym określonego obciążenia musi być uruchomiony na konfiguracje konkretnego sprzętu. Na przykład:

* istniejącą aplikację n warstwowa został "unosiło i przesunięte" w środowisku sieci szkieletowej usług
* Obciążenie chce uruchamiać na temat sprzętu wydajności, skala lub ze względów bezpieczeństwa izolacji
* Obciążenia powinna zostać odizolowana od innych obciążeń powodów przez zasady lub zasobów

Aby zapewnić obsługę tego rodzaju konfiguracje, sieci szkieletowej usług ma pierwszej klasie pojęcie tagi, które można zastosować do węzłów. Tagi te są nazywane **— właściwości węzła**. **Ograniczenia dotyczące umieszczania** są instrukcje dołączone do poszczególnych usług, które wybrać dla co najmniej jednej właściwości węzła. Ograniczenia dotyczące umieszczania zdefiniować, gdzie mają być uruchamiane usługi. Zestaw warunków ograniczających jest otwarty — wszystkie pary klucz/wartość można pracować. 

<center>
![Układ różnych obciążeń klastra][Image5]
</center>

### <a name="built-in-node-properties"></a>Wbudowane właściwości węzła
Sieć szkieletowa usług definiuje właściwości węzła niektóre domyślne, które mogą być używane automatycznie bez konieczności definiowania ich użytkownika. Domyślne właściwości zdefiniowane w każdym węźle to **NodeType** i **NodeName**. Tak na przykład można zapisać jako ograniczenia umieszczania `"(NodeType == NodeType03)"`. Ogólnie rzecz biorąc znaleźliśmy NodeType jest jednym z najbardziej powszechnie używanych właściwości. Jest przydatne, ponieważ odpowiada ona 1:1 z typem maszyny. Każdy typ maszyny odpowiada rodzaju obciążenia w tradycyjny n poziomowej aplikacji.

<center>
![Ograniczenia dotyczące umieszczania i właściwości węzła][Image6]
</center>

## <a name="placement-constraint-and-node-property-syntax"></a>Ograniczenia umieszczania i składni właściwości węzła 
Wartości określonej we właściwości węzła może być typu string, bool, lub podpisany za długo. Instrukcji w usłudze jest nazywany położenia *ograniczenia* ponieważ ogranicza, gdzie usługa może działać w klastrze. Ograniczenie mogą być żadnych instrukcji logiczna, która działa we właściwościach innego węzła w klastrze. Nieprawidłowa selektory w tych instrukcjach logiczne są:

1) warunkowe sprawdza, czy tworzenie konkretnego instrukcje

| Oświadczenie | Składnia |
| --- |:---:|
| "równe" | "==" |
| "nie równa się" | "!=" |
| "większe niż" | ">" |
| "większe lub równe" | ">=" |
| "mniejsze niż" | "<" |
| "mniejsze niż lub równe" | "<=" |

2) wartość logiczna instrukcje dla operacji grupowania i logicznych

| Oświadczenie | Składnia |
| --- |:---:|
| "i" | "&&" |
| "lub" | "&#124;&#124;" |
| "nie" | "!" |
| "grupy jako pojedynczej instrukcji" | "()" |

Oto kilka przykładów podstawowy warunek ograniczający instrukcji.

  * `"Value >= 5"`
  * `"NodeColor != green"`
  * `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

Tylko węzły gdzie ogólną instrukcji ograniczenia umieszczania ocenia się na "True" może mieć usługi dla niej. Węzły, które nie mają właściwość zdefiniowaną są niezgodne z wszystkich ograniczeń umieszczania zawierającą tej właściwości.

Załóżmy, że następujące właściwości węzła zostały zdefiniowane dla typu węzła:

ClusterManifest.xml

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasSSD" Value="true"/>
        <Property Name="NodeColor" Value="green"/>
        <Property Name="SomeProperty" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

za pomocą pliku ClusterConfig.json dla autonomicznych wdrożeniach lub Template.json dla platformy Azure hostowanej klastrów. 

> [!NOTE]
> W szablonie usługi Azure Resource Manager jest zwykle parametry typu węzła. Jego wygląd "[parameters('vmNodeType1Name')]" zamiast "NodeType01".
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

Można utworzyć usługi umieszczania *ograniczenia* usług, takich jak następujące:

C#

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
serviceDescription.PlacementConstraints = "(HasSSD == true && SomeProperty >= 4)";
// add other required servicedescription fields
//...
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Środowiska PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceType -Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementConstraint "HasSSD == true && SomeProperty >= 4"
```

Jeśli wszystkie węzły NodeType01 są prawidłowe, możesz też wybrać ten typ węzła z ograniczeniem "(NodeType == NodeType01)".

Jednym z elementów chłodnych o ograniczenia umieszczania usług jest czy mogą być aktualizowane dynamicznie w czasie wykonywania. Dlatego jeśli zajdzie potrzeba, można przenosić usługi w klastrze, dodawać i usuwać wymagania, np. Sieć szkieletowa usług odpowiada on za zapewnienie, że usługa jest aktualne i dostępne nawet wtedy, gdy tego rodzaju zmiany zostały wprowadzone.

C#:

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

Środowiska PowerShell:

```posh
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

Ograniczenia dotyczące umieszczania są określone dla każdego wystąpienia usługi o nazwie innej. Aktualizacje zawsze pobierają miejsca (Zastąp) co został wcześniej określony.

Definicja klastra definiuje właściwości w węźle. Zmiana właściwości węzła wymaga uaktualnienia konfiguracji klastra. Uaktualnianie węzła właściwości wymaga każdy węzeł dotyczy ponowne uruchomienie w celu jego właściwości nowych raportów. Te uaktualnienia stopniowego są zarządzane przez sieć szkieletowa usług.

## <a name="describing-and-managing-cluster-resources"></a>Opisu i zarządzanie zasobami klastra
Jedną z najważniejszych zadań żadnych orchestrator jest pomagające w zarządzaniu zużycie zasobów w klastrze. Zarządzanie zasobami klastra może oznaczać kilka różnych rzeczy. Po pierwsze jest zapewnienie maszyny nie są przeciążone. Oznacza to, upewniając się, że maszyny nie są uruchomione usługi więcej niż ich może obsłużyć. Po drugie istnieje równoważenia i optymalizacji, co jest szczególnie ważne wydajne działanie usługi. Niektóre węzły jest gorących, podczas gdy inne dotyczą zimnych nie może dopuścić do kosztu wprowadzenia ani wydajności poufnych oferty żądań. Węzły gorących prowadzić do rywalizacji i pogorszenie wydajności i zimnych węzły reprezentują nieużywanego zasobów i wyższe koszty. 

Sieć szkieletowa usług reprezentuje zasoby jako `Metrics`. Metryka jest logiczną lub fizyczną zasobów, do opisywania sieci szkieletowej usług. Przykłady metryki to np. "WorkQueueDepth" lub "MemoryInMb". Informacje o zasoby fizyczne, rządzących sieci szkieletowej usług w węzłach, zobacz [ładu zasobów](service-fabric-resource-governance.md). Aby uzyskać informacje na temat konfigurowania niestandardowych metryk i ich zastosowań, zobacz [w tym artykule](service-fabric-cluster-resource-manager-metrics.md)

Metryki różnią się od ograniczenia rozmieszczanie i właściwości węzła. Właściwości węzła są statyczne deskryptorów w poszczególnych węzłach. Metryki opisano zasoby, które mają węzłów i że korzystać z usług uruchamianych w węźle. Właściwość węzła może być "HasSSD" i mógł zostać ustawiony na wartość true lub false. Ilość dostępnego miejsca na tym dysków SSD i jaka jest używane przez usługi będą metrykę, takie jak "DriveSpaceInMb". 

Należy pamiętać, że podobnie jak ograniczenia dotyczące umieszczania i właściwości węzła Menedżera zasobów klastra sieci szkieletowej usług nie rozpoznaje nazwy metryki oznacza. Nazwy metryki są tylko ciągi. Jest dobrym rozwiązaniem, aby zadeklarować jednostki jako część nazwy metryki, które są tworzone podczas może być niejednoznaczna.

## <a name="capacity"></a>Pojemność
Jeśli wyłączysz wszystkich zasobów *równoważenia*, Menedżer zasobów klastra usługi sieć szkieletowa będzie nadal upewnij się, że żaden węzeł nie zakończył za pośrednictwem jego pojemność. Zarządzanie przepełnienia pojemności jest możliwe, chyba że klaster jest zapełniony lub obciążenia jest większy niż dowolnego węzła. Pojemność jest inny *ograniczenia* używany Menedżera zasobów klastra, aby zrozumieć, jaka część zasobów, a węzeł ma. Pozostała pojemność jest również śledzona klastra jako całość. Zarówno wydajność i zużycie na poziomie usługi są wyrażone w metryki. Tak na przykład metryka może być "ClientConnections" i podany węzeł może mieć pojemność "ClientConnections" z 32768. Inne węzły mogą mieć inne ograniczenia niektórych usługa jest uruchomiona w tym może węzła powiedz go obecnie zajmuje 32256 metryki "ClientConnections".

W czasie wykonywania Menedżer zasobów klastra śledzi pozostałe zasoby w klastrze, w węzłach. Aby móc śledzić zmiany wydajności Menedżera zasobów klastra odejmuje użycia poszczególnych usług z pojemność węzła, w którym jest uruchomiona usługa. Dzięki tym informacjom Menedżera zasobów klastra sieci szkieletowej usług można dowiedzieć się, gdzie umieścić lub przenieść replik, tak aby węzłów nie przekazywane pojemności.

<center>
![Węzły klastra i pojemności][Image7]
</center>

C#:

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

Środowiska PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("ClientConnections,High,1024,0)
```

Możesz sprawdzić możliwości zdefiniowane w manifeście klastra:

ClusterManifest.xml

```xml
    <NodeType Name="NodeType03">
      <Capacities>
        <Capacity Name="ClientConnections" Value="65536"/>
      </Capacities>
    </NodeType>
```

za pomocą pliku ClusterConfig.json dla autonomicznych wdrożeniach lub Template.json dla platformy Azure hostowanej klastrów. 

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

Zazwyczaj usługa załadować zmiany dynamicznie. Na przykład obciążenie repliki "ClientConnections" zmienione od 1024 do 2048, że węzeł, na którym był uruchomiony na następnie miał tylko 512 pozostałej dla tej metryki. Teraz ta replika lub położenia wystąpienia jest nieprawidłowa, ponieważ nie ma wystarczającej ilości miejsca, w tym węźle. Menedżer zasobów klastra ma zaczną działać, a następnie Pobierz węzeł niż pojemność. Zmniejsza obciążenie na węźle, który znajduje się nad pojemności przenosząc przynajmniej jednej repliki lub wystąpień tego węzła do innych węzłów. Podczas przenoszenia replik, Menedżer zasobów klastra próbuje minimalizuje to koszt tych przepływów. Koszt przeniesienia została szczegółowo opisana w [w tym artykule](service-fabric-cluster-resource-manager-movement-cost.md) więcej na temat Menedżera zasobów klastra na ponowne równoważenie strategie i opisano reguły [tutaj](service-fabric-cluster-resource-manager-metrics.md).

## <a name="cluster-capacity"></a>Pojemność klastra
W jaki sposób usługi Menedżer zasobów klastra sieci szkieletowej zachować ogólną klastra jest zapełniony? Również z dynamicznego obciążenia nie istnieje wiele go wykonać. Usługi mogą mieć ich kolekcji obciążenia niezależnie od akcje wykonywane przez Menedżera zasobów klastra. W związku z tym klastra za pomocą szerokie możliwości rozbudowy dzisiaj może być za słaby, gdy stanie się Słynne jutro. Inaczej mówiąc, brak niektórych formantów, które są rozszerzania w, aby uniknąć problemów. W pierwszej kolejności możemy to zapobiega tworzeniu nowych obciążeń, które mogłyby spowodować klastra zapełnił się.

Powiedz tworzenia usługi bezstanowej, a ma niektóre obciążenia skojarzone z nim. Załóżmy, że usługa dba o metryki "DiskSpaceInMb". Również Załóżmy, że chce korzystać z pięciu jednostek "DiskSpaceInMb" dla każdego wystąpienia usługi. Chcesz utworzyć trzy wystąpienia usługi. Wspaniale! Tak to oznacza, że potrzebujemy 15 jednostek "DiskSpaceInMb" znajdować się w klastrze, abyśmy się nawet można utworzyć wystąpień tych usług. Menedżer zasobów klastra stale oblicza przepustowości i zużycia wszystkie metryki, dzięki czemu można określić pozostałe zasoby w klastrze. Jeśli nie ma wystarczającej ilości miejsca, Menedżer zasobów klastra odrzuca Utwórz wywołania usługi.

Ponieważ wymagane jest tylko być dostępne 15 jednostki, ta przestrzeń może zostać przydzielony wiele różnych sposobów. Przykładowo może istnieć pozostałe jednostki pojemności w różnych węzłach 15 lub trzy pozostałe jednostki pojemności w pięciu różnych węzłach. Jeśli Menedżer zasobów klastra można rozmieszczanie rzeczy, więc na trzy węzły jest dostępne jednostki pięć, umieszcza usługę. Zmienianie rozmieszczenia klastra jest zwykle możliwe, chyba że klaster jest prawie pełna lub istniejących usług nie mogą być konsolidowane jakiegoś powodu.

## <a name="buffered-capacity"></a>Buforowany pojemności
Pojemność buforowany jest inna funkcja Menedżera zasobów klastra. Umożliwia on rezerwacji część ogólnej wydajności węzła. Bufor pojemności jest używana tylko do umieszczenia usługi podczas uaktualniania i awarii węzła. Globalny określony buforowanego pojemności na metryki dla wszystkich węzłów. Wartość, którą wybierz zastrzeżone pojemności jest funkcją liczby awarii i domen uaktualnienia w klastrze. Więcej awarii i uaktualniania domeny oznacza, że z buforowanego pojemności może wybrać mniejszą liczbę. Jeśli masz więcej domen, może spodziewać się mniej będzie dostępny podczas uaktualniania i awarii klastra. Określanie buforowane pojemności ma sens tylko jeśli określono również pojemność węzła dla metryki.

Oto przykład sposobu określania buforowanego pojemności:

ClusterManifest.xml

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="SomeMetric" Value="0.15" />
            <Parameter Name="SomeOtherMetric" Value="0.20" />
        </Section>
```

za pomocą pliku ClusterConfig.json dla autonomicznych wdrożeniach lub Template.json dla platformy Azure hostowanej klastrów:

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

Tworzenie nowej usługi nie powiedzie się, gdy klaster będzie poza buforowanego wydajności dla metryki. Uniemożliwia tworzenie nowych usług, aby zachować buforu gwarantuje, że błędy i uaktualnienia nie powodują węzłów przekroczy pojemności. Pojemność buforowany jest opcjonalne, ale jest zalecane w przypadku dowolnego klastra, który definiuje wydajności dla metryki.

Menedżer zasobów klastra uwidacznia informacje dotyczące tego obciążenia. Dla każdego metryki informacje te obejmują: 
  - Ustawienia pojemności buforowany
  - łączna pojemność
  - Bieżące użycie
  - Określa, czy jest uznawany za wszystkie metryki równoważenia lub nie
  - Statystyka odchylenie standardowe
  - węzły, które mają najbardziej i najmniej obciążenia  
  
Poniżej przedstawiono przykład te dane wyjściowe:

```posh
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
* Aby uzyskać informacje dotyczące przepływu architektury i informacji w ramach Menedżera zasobów klastra, zapoznaj się [w tym artykule ](service-fabric-cluster-resource-manager-architecture.md)
* Definiowanie metryki defragmentacji jest jednym ze sposobów skonsolidować węzłów zamiast go rozkładanie obciążenia. Aby dowiedzieć się, jak skonfigurować defragmentacji, zajrzyj do [w tym artykule](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
* Rozpocznij od początku i [wprowadzenie do usługi sieci szkieletowej klastra Menedżera zasobów](service-fabric-cluster-resource-manager-introduction.md)
* Aby dowiedzieć się o jak Menedżer zasobów klastra zarządza i równoważy obciążenie w klastrze, zobacz artykuł na [równoważenia obciążenia](service-fabric-cluster-resource-manager-balancing.md)

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png
