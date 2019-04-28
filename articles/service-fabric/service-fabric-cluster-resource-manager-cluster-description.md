---
title: Opis klastra Menedżer zasobów klastra | Dokumentacja firmy Microsoft
description: Opisujących klaster usługi Service Fabric, określając domen błędów, uaktualnić domen, — właściwości węzła i wydajność węzłów Menedżer zasobów klastra.
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
ms.openlocfilehash: ff291bda87ca4b2b4055e36989b035cf410b3b0f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60744310"
---
# <a name="describing-a-service-fabric-cluster"></a>Opisujące klaster usługi Service fabric
Menedżer zasobów klastra usługi Service Fabric udostępnia kilka mechanizmów do opisywania klastra. Podczas wykonywania Menedżer zasobów klastra używa tych informacji, aby zapewnić wysoką dostępność usług uruchomionych w klastrze. Wymuszając te reguły istotne, również próbuje zoptymalizować zużycie zasobów w klastrze.

## <a name="key-concepts"></a>Kluczowe pojęcia
Menedżer zasobów klastra obsługuje kilka funkcji, które opisują klastra:

* Domeny błędów
* Uaktualnij domeny
* Właściwości węzła
* Węzeł pojemności

## <a name="fault-domains"></a>Domeny błędów
Domena błędów to dowolny obszar skoordynowanego awarii. Jedna maszyna jest domena błędów (ponieważ jest ona może zakończyć się niepowodzeniem na swój własny dla różnych przyczyn, na wypadek awarii zasilania na awarie dysku zły oprogramowania układowego kart Sieciowych). Maszyny podłączone do tego samego przełącznika Ethernet znajdują się w tej samej domenie błędów, jak maszyn udostępniania jednego źródła zasilania lub w obrębie jednej lokalizacji. Ponieważ fizycznych dla błędów sprzętu nakładają się na siebie, oraz domen błędów są z natury hierarchiczna i są reprezentowane jako identyfikatory URI w usłudze Service Fabric.

Należy pamiętać, że domen błędów są prawidłowo skonfigurowane, ponieważ usługa Service Fabric używa tych informacji do bezpiecznego umieść usług. Usługa Service Fabric nie chcesz umieścić usług w taki sposób, że utraty domenę błędów (przyczyną niepowodzenia niektórych składników) powoduje, że usługi przejść w dół. Na platformie Azure środowisko usługi Service Fabric używa informacji o domenie błędów oferowanych przez środowisko poprawnie skonfigurować węzły w klastrze w Twoim imieniu. Dla usługi Service Fabric autonomicznej domeny błędów są definiowane w czasie, że klaster jest skonfigurowany 

> [!WARNING]
> Należy pamiętać, że domena błędów informacjami w usłudze Service Fabric jest prawidłowo wprowadzony. Na przykład załóżmy, że węzły klastra usługi Service Fabric działają wewnątrz 10 maszyn wirtualnych, działających na pięć hostów fizycznych. W tym przypadku chociaż występują 10 maszyn wirtualnych, są tylko 5 różnych (najwyższy poziom) domeny błędów. Udostępnianie tego samego hosta fizycznego powoduje, że maszyny wirtualne na udostępnianie tej samej domenie błędów głównego, ponieważ maszyny wirtualne wystąpić błąd skoordynowane, jeśli ich fizycznego hosta nie powiedzie się.  
>
> Usługa Service Fabric oczekuje, że domena błędów węzła nie należy zmieniać. Inne mechanizmy zapewniania wysokiej dostępności maszyn wirtualnych, takich jak [maszyn wirtualnych o wysokiej dostępności](https://technet.microsoft.com/library/cc967323.aspx) może powodować konflikty z usługą Service Fabric, ponieważ używają przezroczyste migracji maszyn wirtualnych z jednego hosta na inny. Te mechanizmy zmienić konfigurację lub nie Powiadamiaj uruchomiony kod wewnątrz maszyny Wirtualnej. Jako takie, są one **nieobsługiwane** jako środowiska do uruchamiania usługi Service Fabric klastrów. Usługa Service Fabric powinna być stosowanej technologii tylko o wysokiej dostępności. Mechanizmy, takie jak migracja maszyny Wirtualnej sieci SAN, lub inne osoby nie są konieczne. Jeśli używane w połączeniu z usługą Service Fabric, te mechanizmy _zmniejszyć_ aplikacji dostępność i niezawodność ponieważ wprowadzają dodatkowej złożoności, Dodaj scentralizowane źródła błędu i korzystanie z niezawodności i Strategie dostępności, która wchodzi w konflikt z tymi w usłudze Service Fabric. 
>
>

Na poniższym rysunku, firma Microsoft kolor wszystkich jednostek, które przyczyniają się do domen błędów i wyświetlanie listy wszystkich różnych domen błędów, które powodują. W tym przykładzie mamy centrów danych ("DC"), stojakami ("R") i bloków ("B"). Wielkiego Jeśli każdy blok zawiera więcej niż jednej maszyny wirtualnej, mogą występować innej warstwy w hierarchii domeny błędów.

<center>

![Węzły zorganizowane za pośrednictwem domeny błędów][Image1]
</center>

Podczas wykonywania Menedżer zasobów klastra usługi Service Fabric uwzględnia domenach awarii klastra i plany układów. Są dystrybuowane stanowych replik lub wystąpień bezstanowe dla danej usługi, dzięki czemu są one w oddzielnych domenach błędów. Dystrybucja usługi w domenach błędów gwarantuje, że dostępność usługi nie są zagrożone, gdy domena błędów nie powiodło się na dowolnym poziomie hierarchii.

Menedżer zasobów klastra usługi Service Fabric nie obsługuje liczbę warstw, które istnieją w hierarchii domeny błędów. Jednak próbuje upewnij się, że utraty jednej części hierarchii nie ma wpływu na usługi działające w niej. 

Najlepiej jest tą samą liczbą węzłów na każdym poziomie głębokość hierarchii domeny błędów. W przypadku niezrównoważone w klastrze "drzewa" domen błędów utrudni dla Menedżera zasobów klastra ustalenie najlepszych alokacji usług. Imbalanced układy domen błędów oznaczają, że utratę niektórych domen wpływ na dostępność usług częściej niż innych domen. W rezultacie Menedżer zasobów klastra rozerwana między dwa cele: Chce używać z maszynami w tej domenie "duże", umieszczając usług na nich, i chce umieszcza usług w innych domenach, tak aby utraty domeny nie powoduje problemów. 

Jak wyglądają imbalanced domen? Na poniższym diagramie przedstawiono dwa układy innego klastra. W pierwszym przykładzie węzły są dystrybuowane równomiernie w domenach błędów. W drugim przykładzie jedną domenę błędów ma wiele więcej węzłów niż inne domeny błędów. 

<center>

![Dwa układy innego klastra][Image2]
</center>

Na platformie Azure Wybór domeny błędów zawiera węzeł odbywa się za Ciebie. Jednak w zależności od liczby węzłów, które należy zarezerwować użytkownik może nadal pozostać z domen błędów z kolejnych węzłów w nich niż inne. Załóżmy na przykład masz 5 domen błędów w klastrze, ale aprowizacji siedmiu węzłów dla danego elementu NodeType. W tym przypadku najpierw dwie domeny błędów skończyć z większą liczbę węzłów. Jeśli będziesz kontynuować wdrożyć więcej elementy NodeType za pomocą tylko kilku wystąpień, problem pobiera niższa. Z tego powodu zalecamy, aby liczby węzłów w każdym węźle typ jest wielokrotnością liczby domen błędów.

## <a name="upgrade-domains"></a>Uaktualnij domeny
Domeny uaktualnień to inna funkcja, która pomaga zrozumieć układ klastra Menedżera zasobów usługi Service Fabric klaster. Domen uaktualnienia definiują zestaw węzłów, które są uaktualnienia w tym samym czasie. Domen uaktualnienia pomagają zrozumieć i organizowanie operacje zarządzania, takie jak uaktualnienia Menedżer zasobów klastra.

Domeny uaktualnień są bardzo podobnie jak domen błędów, ale z kilka podstawowych różnic. Najpierw obszarów awarie sprzętowe skoordynowanego Definiowanie domen błędów. Uaktualnienia domen, z drugiej strony, są definiowane przez zasady. Uzyskaj do określania, ile chcesz, zamiast go są definiowane w środowisku. Może mieć dowolną liczbę domen uaktualnienia, jak węzły. Inna różnica między domeny błędów i domenach uaktualniania jest uaktualnienia domen nie są hierarchiczne. Zamiast tego są one bardziej przypominają prostych tagów. 

Na poniższym diagramie przedstawiono, że trzy domeny uaktualnienia są rozkładane na trzy domeny błędów. Zawiera także jedną możliwe umieszczanie dla trzech różnych replik usługi stanowej, gdzie każdy kończy się w różnych domenach błędów i domenach uaktualniania. Ta umieszczania umożliwia utraty domeny błędów w trakcie wykonywania uaktualnienia usługi i jeszcze jedną kopię kodu i danych.  

<center>

![Umieszczanie o domenach błędów i uaktualnień][Image3]
</center>

Istnieją zalety i wady na potrzeby dużej liczby domen uaktualnienia. Więcej domen uaktualnienia oznacza, że każdy krok uaktualniania jest bardziej szczegółowe i dlatego ma wpływ na mniejszą liczbę węzłów lub usługi. W wyniku mniejszą liczbę usług trzeba przenieść w czasie, Przedstawiamy mniej zmian w systemie. Jest to prawdopodobnie poprawy niezawodności, ponieważ jest mniej usługi wpływ na wszelkie problemy, wprowadzonych podczas uaktualniania. Więcej domen uaktualnienia oznacza również, że konieczne buforów mniej dostępne w innych węzłach, aby obsłużyć wpływ uaktualnienia. Na przykład w przypadku domen uaktualnienia pięciu węzłów w każdym obsługi około 20% ruchu. Jeśli potrzebujesz w walce z tej domeny uaktualnienia dla uaktualnienie, aby obciążenie zwykle musi go w innym miejscu. Ponieważ masz czterech pozostałych domen uaktualnienia, każdy jest wystarczająca ilość miejsca do około 5 procent całkowitego ruchu. Więcej domen uaktualnienia oznacza, że potrzebujesz mniej buforu na węzłach w klastrze. Rozważmy na przykład, jeśli zamiast tego masz 10 domen uaktualnienia. W takim przypadku każdy UD będzie tylko obsługiwał około 10% całkowitego ruchu. Po uaktualnieniu opisano za pośrednictwem klastra każdej domeny, tylko musi mieć miejsca dla około 1.1 procent całkowitego ruchu. Ogólnie więcej domen uaktualnienia pozwala na uruchamianie węzłów na zwiększenie użycia, ponieważ muszą mniej rezerwowanie pojemności. To samo jest prawdą w przypadku domen błędów.  

Wadą mających wiele domen uaktualnienia jest uaktualnień zwykle trwa dłużej. Usługa Service Fabric czeka w krótkim czasie po domeny uaktualnienia zostaje ukończone i sprawdza się przed przystąpieniem do uaktualniania kolejny. Te opóźnienia Włącz wykrywanie problemów wprowadzone podczas uaktualnienia, zanim uaktualnianie będzie kontynuowane. Kosztem jest dopuszczalne, ponieważ uniemożliwia zły zmiany wywierania wpływu na zbyt wiele usług w danym momencie.

Zbyt małej liczby domen uaktualnienia ma wiele negatywne skutki uboczne — podczas każdego poszczególne domeny uaktualnienia nie działa i uaktualniana dużą część łączna pojemność jest niedostępny. Na przykład jeśli masz tylko trzy domeny uaktualnienia są tworzone w dół około 1/3 ogólnej usługi lub pojemność klastra w danym momencie. Jednocześnie o tak wiele usług w dół nie jest pożądane, ponieważ musisz mieć wystarczające moce przerobowe w pozostałej części do klastra, aby obsłużyć obciążenie. Obsługa tego buforu oznacza, że podczas normalnego działania tych węzłów są ładowane mniejsza niż przeciwnym. Zwiększa to koszt uruchamiania usługi.

Nie ma żadnych rzeczywistych limitu całkowitą liczbę błędów i domenach uaktualniania w środowisku lub ograniczenia dotyczące sposobu nakładają się. Inaczej mówiąc, istnieje kilka typowych wzorców:

- Domeny błędów i domenach uaktualniania mapowane 1:1
- Jedną domenę uaktualnienia na węzeł (fizyczny lub wirtualny wystąpienie systemu operacyjnego)
- Model "rozłożone" lub "macierzy", gdzie domenach błędów i domenach uaktualniania tworzą macierz zwykle uruchomieniu dół ukośne podziały maszyn

<center>

![Błędów i domeny uaktualnień układów][Image4]
</center>

Ma nie najlepsze odpowiedzi którego układu, aby wybrać, każdy z nich ma kilka zalet i wad. Na przykład 1FD:1UD model jest prosta konfiguracja. Wartość 1 na model węzła do domeny uaktualnienia jest najbardziej przypomina osób, które są używane do. Podczas uaktualniania każdego węzła jest aktualizowany niezależnie. Jest to podobne do jak małe zestawy maszyn zostały uaktualnione ręcznie w przeszłości. 

Najbardziej typowe model jest sama macierzy, gdzie domenami błędów i domenach uaktualniania tworzą tabelę, a węzły są umieszczane uruchamianie wzdłuż przekątnej. Jest to model używany domyślnie w klastrach usługi Service Fabric na platformie Azure. W przypadku klastrów z wieloma węzłami wszystko, co kończy się wyglądająca jak powyższy wzorzec gęstą macierzy.

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>Ograniczenia w domenach błędów i domeny uaktualnienia i wynikowe zachowania
### <a name="default-approach"></a>*Metody domyślne*
Domyślnie Menedżer zasobów klastra temu usługi są równoważone w domenach błędów i domenach uaktualniania. Jest to modelowane jako [ograniczenie](service-fabric-cluster-resource-manager-management-integration.md). Stany ograniczenie domenach błędów i domeny uaktualnienia: "Partycja danej usługi, nigdy nie należy różnica większa niż jeden liczby obiektów usługi (repliki usługi stanowej lub wystąpień o bezstanowa usługa) między dwiema domenami, w tym samym poziomie hierarchii". Załóżmy, że to ograniczenie daje gwarancję "maksymalną różnicę". Ograniczenie domenach błędów i domeny uaktualnienia zapobiega niektórych przenosi lub ustalenia, które naruszają reguły podanej powyżej. 

Przyjrzyjmy się jednym z przykładów. Załóżmy, że istnieje klastra z sześciu węzłów, skonfigurowany z pięcioma domenami błędów i 5 domenami uaktualnienia.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | | |N3 | | |
| **UD3** | | | |N4 | |
| **UD4** | | | | |N5 |

*Konfiguracja 1*

Teraz załóżmy, że możemy utworzyć usługę za pomocą TargetReplicaSetSize (lub usługi bezstanowej wiadomość InstanceCount) do 5. Repliki proponowany N1 N5. W rzeczywistości N6 nigdy nie jest używany niezależnie od tego, jak wiele usług, takich jak to, którą tworzysz. Ale Dlaczego? Spójrzmy na różnicę między bieżący układ i co się stanie, jeśli wybrano N6.

Oto układ, który mamy i łącznej liczby replik na domenach błędów i domeny uaktualnienia:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** | |R2 | | | |1 |
| **UD2** | | |R3 | | |1 |
| **UD3** | | | |R4 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

*Układ 1*


Ten układ jest równoważony pod względem węzłów na domenę błędów i domeny uaktualnienia. Jest ona również równoważony pod względem liczby replik na domenach błędów i domeny uaktualnienia. Każda domena ma taką samą liczbę węzłów i taką samą liczbę replik.

Teraz Przyjrzyjmy się co się stanie, jeśli firma Microsoft była używana N6 zamiast N2. Sposób będzie replik dystrybucji następnie?

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R5 | | | | |1 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |2 |0 |1 |1 |1 |- |

*Układ 2*


Ten układ narusza nasza definicja gwarancji "maksymalną różnicę" ograniczenia domeny błędów. FD0 ma dwie repliki, podczas gdy FD1 ma zero, dzięki czemu różnicę między FD0 i FD1 daje w sumie dwa, która jest większa niż maksymalna różnica w jednej. Ponieważ nastąpiło naruszenie ograniczenia, Menedżer zasobów klastra nie zezwala na to rozwiązanie. Podobnie jeśli możemy pobrane N2 i N6 (zamiast N1 i N2) otrzymamy wynik:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

*Układ 3*


Ten układ jest równoważone w domenach błędów. Jednak teraz go narusza ograniczenie do domeny uaktualnienia ponieważ UD0 ma zero replik, natomiast UD1 ma dwa. W związku z tym ten układ jest również nieprawidłowe i nie będą pobierane przez Menedżer zasobów klastra.

Takie podejście do dystrybucji stanowych replik lub wystąpień o bezstanowa zapewnia najlepsze możliwe odporność na uszkodzenia. W sytuacji, gdy ulegnie awarii jednej domenie, minimalnej liczby replik/wystąpieniach jest utracone. 

Z drugiej strony to podejście może być zbyt rygorystyczne i nie zezwalaj klastrowi na korzystanie z wszystkich zasobów. W przypadku niektórych konfiguracji klastra nie można używać niektórych węzłów. Może to prowadzić do usługi Service Fabric nie wprowadzenie do usługi, wynikiem komunikaty ostrzegawcze. W poprzednim przykładzie niektóre węzły klastra nie może być używana (N6 w danym przykładzie). Nawet jeśli należy dodać węzły do klastra (N7 — N10), wystąpień/replik tylko będzie umieszczony na N1 — N5 ze względu na ograniczenia domenach błędów i domeny uaktualnienia. 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | |N10 |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | |N9 |N5 |

*Konfiguracja 2*


### <a name="alternative-approach"></a>*Informacje o innym podejściu*

Menedżer zasobów klastra obsługuje inna wersja tego ograniczenia domenach błędów i domeny uaktualnienia, która umożliwia umieszczanie podczas nadal z zagwarantowaniem minimalnego poziomu bezpieczeństwa. Alternatywne ograniczenie domenach błędów i domeny uaktualnienia może być wyrażona w następujący sposób: "Partycja danej usługi, dystrybucji repliki w domenach należy upewnić się, że partycja nie występują w niej utraciła kworum". Załóżmy, że to ograniczenie daje gwarancję "kworum bezpieczne". 

> [!NOTE]
>Usługi stanowe definiujemy *utraciła kworum* w sytuacji, gdy większość replik partycji nie działają w tym samym czasie. Na przykład jeśli TargetReplicaSetSize wynosi pięć, zbiór wszystkie trzy repliki reprezentuje kworum. Podobnie jeśli TargetReplicaSetSize 6, cztery repliki są niezbędne dla kworum. W obu przypadkach nie więcej niż dwie repliki mogą być wyłączone w tym samym czasie Jeśli partycji chce, aby kontynuować, działa prawidłowo. Usługi bezstanowej jest coś takiego jak *utraciła kworum* jako bezstanowej usługi będzie działać normalnie, nawet wtedy, gdy w większości przypadków przestaną działać w tym samym czasie. Dzięki temu skupimy się na usług stanowych w pozostałej części tekstu.
>

Wróćmy do poprzedniego przykładu. Za pomocą ograniczenia w wersji "kworum bezpieczne" wszystkie trzy układy danego będą prawidłowe. Jest to spowodowane nawet, jeśli będzie niepowodzenie FD0 w drugim układu lub UD1 w układzie trzeci, partycji, nadal będzie miał kworum (większość jego repliki nadal będzie się). Z tą wersją ograniczenia N6 prawie zawsze może być wykorzystywane.

Podejście "kworum bezpieczne" zapewnia większą elastyczność niż podejście "maksymalną różnicę", ponieważ łatwiej znaleźć dystrybucji repliki, które są prawidłowe w prawie każdym Topologia klastra. Jednak to podejście nie gwarantuje najlepszą odporność na uszkodzenia właściwości, ponieważ niektóre błędy są gorsza niż inne. W najgorszym przypadku wielkości liter większość repliki może być utracone wystąpił błąd w jednej domenie i jednej z replik dodatkowych. Na przykład zamiast konieczności utratę kworum z 5 replik lub wystąpień 3 błędów, możesz teraz stracić głosów z błędami tylko dwa. 

### <a name="adaptive-approach"></a>*Podejście adaptacyjne*
Ponieważ oba podejścia mają zalety i słabe strony, wprowadziliśmy adaptacyjne metody, która łączy tych dwóch strategii.

> [!NOTE]
> Są to domyślne zachowanie, począwszy od usługi Service Fabric w wersji 6.2. 
> 
> Funkcje adaptacyjnego sterowania podejście domyślnie używa logiki "maksymalną różnicę" i przełączniki logiki "kworum bezpieczne" tylko wtedy, gdy jest to konieczne. Menedżer zasobów klastra automatycznie wpadł na strategię, jakiej jest to konieczne, analizując konfiguracji klastra i usług. Dla danej usługi: *W przypadku TargetReplicaSetSize podzielny przez liczbę domen błędów i liczba domen uaktualnienia **i** liczba węzłów jest mniejsza niż lub równe (liczba domen błędów) * (liczba domen uaktualnienia), klastra Menedżer zasobów należy wykorzystać logiki "na podstawie kworum" dla tej usługi.* Mieć na uwadze, że Menedżer zasobów klastra będzie używać tej metody dla usług stanowych i bezstanowych, pomimo utraciła kworum, które nie są istotne w przypadku usług bezstanowych.

Wróć do poprzedniego przykładu i przyjęto założenie, że klaster ma teraz 8 węzłów (klaster nadal jest skonfigurowany z pięcioma domenami błędów i 5 domenami uaktualnienia TargetReplicaSetSize usług hostowanych na będą nadal tego klastra, 5). 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | | |N5 |

*Konfiguracja 3*

Ponieważ wszystkie niezbędne warunki są spełnione, Menedżer zasobów klastra będzie wykorzystywać logiki "kworum na podstawie" podczas dystrybuowania usługi. Umożliwia to użycie N6 — N8. Dystrybucjami możliwości usługi w tym przypadku może wyglądać następująco:

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | | | |0 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |2 |1 |1 |0 |1 |- |

*Układ 4*

W przypadku usługi TargetReplicaSetSize 4 (na przykład), Menedżer zasobów klastra Zwróć uwagę, że zmiana i wznowić działanie przy użyciu logiki "maksymalną różnicę", ponieważ już TargetReplicaSetSize nie dividable według liczby błędów i domenach uaktualniania. W wyniku niektórych przemieszczania replika zostanie przeprowadzona do dystrybucji, pozostałe cztery repliki w węzłach N1 N5 tak, aby logika domeny w domenie awarii i uaktualniania, w wersji "maksymalną różnicę" nie jest naruszona. 

Patrząc Wstecz, czwarty układ i TargetReplicaSetSize pięć. Jeśli N1 zostanie usunięty z klastra, liczba domen uaktualnienia staje się równa 4. Menedżer zasobów klastra uruchamia ponownie, przy użyciu logiki "maksymalną różnicę", zgodnie z liczbą domenami aktualizacji równomiernie nie rozłożyć TargetReplicaSetSize usługi już. W wyniku repliki R1, gdy ponownie kompilowane musi proponowany N4 tak, aby usterek i ograniczenia domeny uaktualnienia nie zostanie naruszona.

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |ND |ND |ND |ND |ND |ND |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | |R1 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

*Układ 5*

## <a name="configuring-fault-and-upgrade-domains"></a>Konfigurowanie błędów i domenach uaktualniania
Definiowanie domen błędów i domenach uaktualniania odbywa się automatycznie w usłudze Service Fabric wdrożeń hostowanych na platformie Azure. Usługa Service Fabric zbiera i używa informacji o środowisku platformy Azure.

Jeśli w przypadku tworzenia własnego klastra (lub do uruchomienia określonego topologii w trakcie programowania) można podać informacje dotyczące domeny błędów i domeny uaktualnienia samodzielnie. W tym przykładzie definiujemy dziewięciu węzłów lokalny klaster projektowy rozciągających się na trzech "centrów danych" (każdy z trzech stojakami). Ten klaster ma również trzy domeny uaktualnienia rozkładane na tych trzech centrów danych. Przykład konfiguracji znajduje się poniżej: 

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

za pomocą ClusterConfig.json dla autonomicznych wdrożeniach

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
> Podczas definiowania klastrów za pomocą usługi Azure Resource Manager, oraz domen błędów i domenach uaktualniania są przypisywane przez platformę Azure. W związku z tym definicja typy węzłów i zestawy skalowania maszyn wirtualnych do szablonu usługi Azure Resource Manager nie ma domeny błędów i domeny uaktualnienia.
>

## <a name="node-properties-and-placement-constraints"></a>Właściwości węzła i ograniczeniami dotyczącymi umieszczania
Czasami (w rzeczywistości w większości przypadków) użytkownik chce, aby upewnić się, że niektóre obciążenia uruchamiane tylko na niektóre rodzaje węzłów w klastrze. Na przykład niektóre obciążenia mogą wymagać procesorów GPU lub dyski SSD, podczas gdy inne nie. Świetny przykład określania wartości docelowej sprzętu do konkretnych obciążeń jest tam prawie wszystkie architektury n warstwowej. Niektóre maszyny służyć jako frontonu lub interfejs API obsługujący strony aplikacji i są widoczne dla klientów lub Internetu. Różnych maszynach, często z innego sprzętu zasobów, wykonuje pracę warstwy obliczenia lub magazyn. Są to zazwyczaj _nie_ bezpośrednio narażonych na klientów lub Internetu. Usługi Service Fabric oczekuje, że istnieją przypadki, gdzie konkretnych obciążeń muszą zostać uruchomione na konfiguracji określonego sprzętu. Na przykład:

* istniejącej aplikacji n warstwowa została "podniesiony i przesunięte" w środowisku usługi Service Fabric
* Obciążenie chce uruchomić na sprzęcie określonych dla wydajności, skalowania lub ze względów bezpieczeństwa izolacji
* Obciążenie powinna zostać odizolowana od innych obciążeń powodów użycie zasad lub zasobów

Aby obsługiwać te różne konfiguracje, Usługa Service Fabric ma najwyższej klasy pojęcie tagi, które mogą być stosowane do węzłów. Tagi te są nazywane **— właściwości węzła**. **Ograniczeniami dotyczącymi umieszczania** są instrukcje dołączone do poszczególnych usług, które wybierz co najmniej jedną właściwość węzła. Ograniczeniami dotyczącymi umieszczania definiują, gdzie należy uruchamiać usług. Zestaw ograniczeń jest otwarty — wszystkie pary klucz/wartość może pracować. 

<center>

![Układ różnych obciążeń klastra][Image5]
</center>

### <a name="built-in-node-properties"></a>Wbudowane — właściwości węzła
Usługa Service Fabric definiuje niektóre domyślne właściwości węzła, które mogą być używane automatycznie bez użytkownika konieczności wcześniejszego definiowania ich. Domyślne właściwości zdefiniowane w każdym węźle to **NodeType** i **NodeName**. Dlatego na przykład można napisać jako ograniczenie umieszczania `"(NodeType == NodeType03)"`. Ogólnie wykryto NodeType, aby być jednym z najbardziej często używanych właściwości. Jest to przydatne, ponieważ odpowiada ona 1:1 z typem maszyny. Każdy typ maszyny odnosi się do typu obciążenia w tradycyjnych aplikacji n warstwowej.

<center>

![Ograniczeniami dotyczącymi umieszczania i — właściwości węzła][Image6]
</center>

## <a name="placement-constraint-and-node-property-syntax"></a>Ograniczenie umieszczania i składni właściwości węzła 
Wartość określona we właściwości węzła może być typu string, bool, lub podpisany długo. Instrukcja na usługę nosi nazwę miejsca *ograniczenie* ponieważ ogranicza, których usługa może działać w klastrze. Ograniczenia mogą być Boolean instrukcji, która operuje na właściwości innego węzła w klastrze. Nieprawidłowa selektory w tych instrukcjach logiczne są:

1) warunkowe sprawdza, czy tworzenie określonej instrukcji

| Oświadczenie | Składnia |
| --- |:---:|
| "jest równe" | "==" |
| "nie jest równe" | "!=" |
| "większe niż" | ">" |
| "większe niż lub równe" | ">=" |
| "poniżej" | "<" |
| "mniejsze niż lub równe" | "<=" |

2) wartość logiczna instrukcji dla operacji grupowania i logiczne

| Oświadczenie | Składnia |
| --- |:---:|
| "i" | "&&" |
| "or" | "&#124;&#124;" |
| "not" | "!" |
| "grupy jako pojedynczą instrukcję" | "()" |

Poniżej przedstawiono kilka przykładów podstawowy warunek ograniczający instrukcji.

  * `"Value >= 5"`
  * `"NodeColor != green"`
  * `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

Tylko węzły, w którym ogólną instrukcji ograniczeń umieszczania daje w wyniku "True" może mieć usługi dla niej. Węzły, które nie mają właściwości zdefiniowane są niezgodne z wszystkich ograniczeń umieszczania zawierający tę właściwość.

Załóżmy, że następujące właściwości węzła zostały zdefiniowane dla typu danego węzła:

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

za pomocą ClusterConfig.json dla autonomicznych wdrożeniach lub Template.json na platformie Azure hostowane klastrów. 

> [!NOTE]
> W szablonie usługi Azure Resource Manager typ węzła jest zazwyczaj sparametryzowanych. To powiadomienie będzie wyglądać "[parameters('vmNodeType1Name')]" zamiast "NodeType01".
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

Program PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceType -Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementConstraint "HasSSD == true && SomeProperty >= 4"
```

Jeśli wszystkie węzły NodeType01 są prawidłowe, możesz również wybrać ten typ węzła, z ograniczeniem "(NodeType == NodeType01)".

Jedną z najfajniejszych rzeczy podczas ograniczeniami dotyczącymi umieszczania usługi to, że mogą być aktualizowane dynamicznie podczas wykonywania. Dlatego jeśli zachodzi potrzeba, należy można przenoszenie usługi w klastrze, dodawać i usuwać wymagania itp. Usługa Service Fabric zajmuje się zapewnienie, że usługa pozostaje aktualne i dostępne, nawet wtedy, gdy tego rodzaju zmiany zostały wprowadzone.

C#:

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

Program PowerShell:

```posh
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

Ograniczeniami dotyczącymi umieszczania są określone dla każdego wystąpienia różnych nazwaną usługę. Aktualizacje zawsze pobierają miejsca (Zastąp) co wcześniej określono.

Definicja klastra definiuje właściwości w węźle. Zmiana właściwości węzła wymaga uaktualnienia konfiguracji klastra. Uaktualnienie — właściwości węzła wymaga każdego dotyczy węzeł, aby po ponownym uruchomieniu raportu jego nowych właściwości. Te uaktualnienia stopniowe są zarządzane przez usługę Service Fabric.

## <a name="describing-and-managing-cluster-resources"></a>Opisu i zarządzanie zasobami klastra
Jedno z najważniejszych zadań dowolnego programu orchestrator jest pomagające w zarządzaniu zużycie zasobów w klastrze. Zarządzanie zasobami klastra może oznaczać kilka różnych rzeczy. Po pierwsze jest zapewnienie maszyny nie są przeciążone. Oznacza to, upewniając się, że maszyny nie są uruchomione większej liczby usług niż ich może obsłużyć. Po drugie jest równoważenia i optymalizacji, które mają kluczowe znaczenie dla wydajne działanie usług. Niektóre węzły były gorąca, a inne są zimnych nie może dopuścić do kosztów ofert usługi poufnych wprowadzenia ani wydajności. Węzły w warstwie gorąca prowadzić do rywalizacji o zasoby i niską wydajnością, a węzły zimnych reprezentują ograniczenia marnowania zasobów i wyższe koszty. 

Usługa Service Fabric reprezentuje zasobów jako `Metrics`. Metryki są logicznych lub fizycznych zasobów, które użytkownik chce opisać w usłudze Service Fabric. Przykłady metryki elementów, takich jak "WorkQueueDepth" lub "MemoryInMb". Aby uzyskać informacji na temat zasobów fizycznych, określające usługi Service Fabric w węzłach, zobacz [nadzór nad zasobami](service-fabric-resource-governance.md). Aby uzyskać informacje na temat konfigurowania niestandardowych metryk i ich zastosowań, zobacz [w tym artykule](service-fabric-cluster-resource-manager-metrics.md)

Metryki różnią się od ograniczenia angażowania i właściwości węzła. Właściwości węzła są statyczne deskryptorów w poszczególnych węzłach. Metryki Opisz zasoby, które mają węzłów i że korzysta z usług uruchamiania w węźle. Właściwość węzła może być "HasSSD" i można ustawić na wartość true lub false. Ilość miejsca dostępnego w tym dyski SSD i jaka jest używane przez usługi będzie metryki, takie jak "DriveSpaceInMb". 

Należy zauważyć, że podobnie jak właściwości węzła, i ograniczeniami dotyczącymi umieszczania Menedżer zasobów klastra usługi Service Fabric nie rozpoznaje znaczenie nazwy metryki. Nazwy metryki są tylko ciągi. Jest dobrą praktyką deklarowanie jednostki jako część nazwy metryk, które tworzysz, gdy może być niejednoznaczna.

## <a name="capacity"></a>Pojemność
Jeśli wyłączysz wszystkich zasobów *równoważenia*, Menedżer zasobów klastra usługi Service Fabric będzie nadal upewnij się, że żaden węzeł zakończenie za pośrednictwem jej pojemność. Zarządzanie przepełnienia pojemności jest możliwe, chyba że klaster jest zapełniony lub obciążenia jest większy niż dowolnego węzła. Pojemność jest kolejnym *ograniczenie* używany Menedżer zasobów klastra, aby zrozumieć, jaka zasobów, gdy węzeł ma. Pozostała pojemność również jest śledzona dla klastra jako całości. Pojemność i użycie na poziomie usługi są wyrażane pod względem metryk. Na przykład metryka może być "ClientConnections" i danego węzła może być pojemności dla "ClientConnections" od 32768. Inne węzły mogą mieć inne limity niektóre usługi uruchomione na który może węzła Załóżmy, że jego obecnie zużywa 32256 metryki "ClientConnections".

Podczas wykonywania Menedżer zasobów klastra śledzi pozostałe pojemności w klastrze, a w węzłach. Aby móc śledzić zmiany pojemności Menedżer zasobów klastra odejmuje użycia każdej z tych usług z pojemności węzła, gdzie usługa jest uruchamiana. Dzięki tym informacjom Menedżer zasobów klastra usługi Service Fabric można ustalić gdzie umieścić lub Przenieś repliki tak, aby węzły nie są kierowane przez pojemność.

<center>

![Węzły klastra i pojemność][Image7]
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

Program PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("ClientConnections,High,1024,0)
```

Możesz zobaczyć pojemności zdefiniowany w manifeście klastra:

ClusterManifest.xml

```xml
    <NodeType Name="NodeType03">
      <Capacities>
        <Capacity Name="ClientConnections" Value="65536"/>
      </Capacities>
    </NodeType>
```

za pomocą ClusterConfig.json dla autonomicznych wdrożeniach lub Template.json na platformie Azure hostowane klastrów. 

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

Zazwyczaj usługa załadować zmiany dynamicznie. Załóżmy, że obciążenie repliki "ClientConnections" zmieniony z 1024, 2048, że węzeł, na którym była uruchomiona na następnie tylko miała 512 wydajność dla tej metryki. Teraz ta replika lub położenia wystąpienia jest nieprawidłowa, ponieważ nie ma wystarczającej ilości miejsca, w tym węźle. Menedżer zasobów klastra ma zaczną działać, a następnie przełączyć węzeł ponownie poniżej pojemności. Zmniejsza to obciążenie w węźle będącym przeciążone, przenosząc przynajmniej jednej z replik lub wystąpień tego węzła do innych węzłów. Podczas przenoszenia replik, Menedżer zasobów klastra próbuje minimalizuje to koszt tych przepływów. Koszt przeniesienia zostało omówione w [w tym artykule](service-fabric-cluster-resource-manager-movement-cost.md) opisano zasady i więcej na temat Menedżer zasobów klastra przez ponowne równoważenie strategie [tutaj](service-fabric-cluster-resource-manager-metrics.md).

## <a name="cluster-capacity"></a>Pojemność klastra
W jaki sposób Menedżer zasobów klastra usługi Service Fabric może zapewnić ogólny klastra jest przepełniony? Dobrze za pomocą dynamicznego obciążenia nie ma wiele jego wykorzystania. Usługi mogą mieć ich wzrost obciążenia, niezależnie od akcje wykonywane przez Menedżer zasobów klastra. W rezultacie klastra przy użyciu już dziś sporo miejsca może być za słaby, gdy staje się sławę jutro. Inaczej mówiąc, istnieją niektóre formanty, które są wbudowanymi Aby uniknąć problemów. Pierwszą rzeczą, jaką możemy zrobić to zapobiega tworzeniu nowych obciążeń, które mogłoby spowodować klastra zapełnił się.

Załóżmy, że Utwórz usługę bezstanową i ma pewne obciążenia skojarzonych z nim. Załóżmy, że usługa dba o metryki "DiskSpaceInMb". Również Załóżmy, że będzie ona korzystać z pięciu jednostek "DiskSpaceInMb" dla każdego wystąpienia usługi. Chcesz utworzyć trzy wystąpienia usługi. Wspaniale! Dlatego oznacza to, czego potrzebujemy, 15 jednostek "DiskSpaceInMb" znajdować się w klastrze, abyśmy nawet mieć możliwość tworzenia tych wystąpień usługi. Menedżer zasobów klastra stale oblicza pojemność i użycie wszystkie metryki, dzięki czemu można określić, pozostałe pojemności w klastrze. Jeśli nie ma wystarczającej ilości miejsca, Menedżer zasobów klastra odrzuca Utwórz wywołania usługi.

Ponieważ wymagane jest jedynie ma być dostępna 15 jednostek, to miejsce może zostać przydzielone wiele różnych sposobów. Na przykład może istnieć pozostałe jednostki pojemności na 15 różnych węzłach lub trzy pozostałe jednostki pojemności w pięciu różnych węzłach. Jeśli Menedżer zasobów klastra można ponownie rozmieścić elementy, więc ma pięć jednostek dostępnych na trzy węzły, umieszcza usługę. Ponowne rozmieszczanie klastra zwykle jest możliwe chyba, że klaster jest prawie pełna lub istniejących usług nie mogą być konsolidowane przyczyny.

## <a name="buffered-capacity"></a>Buforowane pojemności
Pojemność buforowanego jest inna funkcja Menedżer zasobów klastra. Umożliwia ona rezerwacji część ogólnej wydajności węzła. Bufor pojemności jest używana tylko do umieszczenia usług podczas uaktualnień i awarie węzłów. Pojemność buforowanego określono globalnie na metrykę dla wszystkich węzłów. Wartość, którą można wybrać dla rezerwowanie pojemności jest funkcją liczby domenach błędów i uaktualnienia w klastrze. Więcej błędów i domenach uaktualniania oznacza wybrać mniejszą liczbę dla Twojego buforowaną pojemności. Jeśli masz więcej domen, można oczekiwać, że mniejszych ilości klastra będzie dostępny podczas uaktualnienia. Określanie pojemności buforowane tylko sens, jeśli określono również wydajność węzła dla metryki.

Oto przykład sposobu określania buforowanego pojemność:

ClusterManifest.xml

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="SomeMetric" Value="0.15" />
            <Parameter Name="SomeOtherMetric" Value="0.20" />
        </Section>
```

za pomocą ClusterConfig.json dla autonomicznych wdrożeniach lub Template.json na platformie Azure hostowane klastrów:

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
  - Ustawienia pojemności buforowanego
  - Całkowita pojemność
  - Bieżące zużycie
  - czy wszystkie metryki uznaje się za zrównoważone, czy nie
  - Statystyka odchylenie standardowe
  - węzły, które ma największą i najmniejszą obciążenia  
  
Poniżej możemy zobaczyć przykład te dane wyjściowe:

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
* Aby uzyskać informacji na temat architektury i przepływem informacji w ramach Menedżer zasobów klastra, zapoznaj się [w tym artykule](service-fabric-cluster-resource-manager-architecture.md)
* Definiowanie defragmentacji metryk jest jednym ze sposobów Konsolidacja obciążenia w węzłach zamiast rozkładanie go. Aby dowiedzieć się, jak skonfigurować defragmentacji, zapoznaj się [w tym artykule](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
* Zacznij od początku i [zapoznaj się z wprowadzeniem do usługi Service Fabric Menedżer zasobów klastra](service-fabric-cluster-resource-manager-introduction.md)
* Aby dowiedzieć się o zarządza Menedżer zasobów klastra i równoważy obciążenie w klastrze, zapoznaj się z artykułem na [równoważenia obciążenia](service-fabric-cluster-resource-manager-balancing.md)

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png
