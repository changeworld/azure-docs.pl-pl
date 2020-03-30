---
title: Opis klastra przy użyciu Menedżera zasobów klastra
description: Opis klastra sieci szkieletowej usług określając domeny błędów, domeny uaktualnienia, właściwości węzłów i pojemności węzłów dla Menedżera zasobów klastra.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 7142e3f9aaa25e7ba327194c04ad6a9b5f4e3ad1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258774"
---
# <a name="describe-a-service-fabric-cluster-by-using-cluster-resource-manager"></a>Opis klastra sieci szkieletowej usług przy użyciu Menedżera zasobów klastra
Funkcja Menedżera zasobów klastra usługi Azure Service Fabric udostępnia kilka mechanizmów opisujących klaster:

* Domeny błędów
* Uaktualnianie domen
* Właściwości węzła
* Pojemność węzłów

W czasie wykonywania Menedżer zasobów klastra używa tych informacji, aby zapewnić wysoką dostępność usług uruchomionych w klastrze. Podczas wymuszania tych ważnych reguł, stara się również zoptymalizować zużycie zasobów w klastrze.

## <a name="fault-domains"></a>Domeny błędów
Domena błędów to dowolny obszar skoordynowanej awarii. Pojedynczy komputer jest domeną błędów. Może się nie powieść z różnych powodów, od awarii zasilania do awarii dysku do złego oprogramowania sieciowego. 

Maszyny podłączone do tego samego przełącznika Ethernet znajdują się w tej samej domenie błędów. Podobnie jak maszyny, które mają jedno źródło energii lub w jednym miejscu. 

Ponieważ jest to naturalne, że błędy sprzętowe nakładają się na siebie, domeny błędów są z natury hierarchiczne. Są one reprezentowane jako identyfikatory URI w sieci szkieletowej usług.

Ważne jest, aby domeny błędów były poprawnie skonfigurowane, ponieważ sieci szkieletowej usług używa tych informacji do bezpiecznego umieszczania usług. Sieci szkieletowej usług nie chce umieścić usługi takie, że utrata domeny błędów (spowodowane przez awarię niektórych składników) powoduje, że usługa upaść. 

W środowisku platformy Azure sieci szkieletowej usług używa informacji o domenie błędów dostarczonych przez środowisko, aby poprawnie skonfigurować węzły w klastrze w Twoim imieniu. W przypadku autonomicznych wystąpień sieci szkieletowej usług domeny błędów są definiowane w momencie konfigurowania klastra. 

> [!WARNING]
> Ważne jest, aby informacje o domenie błędów dostarczane do sieci szkieletowej usług były dokładne. Załóżmy na przykład, że węzły klastra sieci szkieletowej usług są uruchomione wewnątrz 10 maszyn wirtualnych, działających na 5 hostach fizycznych. W takim przypadku, mimo że istnieje 10 maszyn wirtualnych, istnieje tylko 5 różnych (najwyższego poziomu) domen błędów. Udostępnianie tego samego hosta fizycznego powoduje, że maszyny wirtualne współużytkują tę samą domenę błędów głównych, ponieważ maszyny wirtualne doświadczają skoordynowanego niepowodzenia, jeśli ich host fizyczny nie powiedzie się.  
>
> Sieci szkieletowej usług oczekuje domeny błędów węzła nie zmienić. Inne mechanizmy zapewniające wysoką dostępność maszyn wirtualnych, takich jak [maszyny wirtualne typu HA,](https://technet.microsoft.com/library/cc967323.aspx)mogą powodować konflikty z siecią szkieletową usług. Mechanizmy te używają przezroczystej migracji maszyn wirtualnych z jednego hosta do drugiego. Nie konfigurują ponownie ani nie powiadamiają uruchomionego kodu wewnątrz maszyny Wirtualnej. W związku z tym nie są one *obsługiwane* jako środowiska do uruchamiania klastrów sieci szkieletowej usług. 
>
> Usługa Fabric powinna być jedyną zastosowaną technologią o wysokiej dostępności. Mechanizmy takie jak migracja maszyn wirtualnych na żywo i nazwy SAN nie są konieczne. Jeśli te mechanizmy są używane w połączeniu z sieci szkieletowej usług, _zmniejszają_ dostępność aplikacji i niezawodność. Powodem jest to, że wprowadzają dodatkową złożoność, dodać scentralizowane źródła awarii i używać strategii niezawodności i dostępności, które są w konflikcie z tymi w sieci szkieletowej usług. 
>
>

Na poniższej grafice pokolorujemy wszystkie jednostki, które przyczyniają się do domen błędów i wymieniamy wszystkie różne domeny błędów, które wynikają. W tym przykładzie mamy centra danych ("DC"), stojaki ("R") i ostrzy ("B"). Jeśli każdy blok zawiera więcej niż jedną maszynę wirtualną, może istnieć inna warstwa w hierarchii domeny błędów.

<center>

![Węzły zorganizowane za pośrednictwem domen błędów][Image1]
</center>

W czasie wykonywania Menedżer zasobów klastra sieci szkieletowej usług uwzględnia domeny błędów w układach klastra i planów. Repliki stanowe lub wystąpienia bezstanowe dla usługi są dystrybuowane, więc znajdują się w oddzielnych domenach błędów. Dystrybucja usługi między domenami błędów zapewnia, że dostępność usługi nie jest zagrożona, gdy domena błędów ulegnie awarii na dowolnym poziomie hierarchii.

Menedżer zasobów klastra nie obchodzi, ile warstw znajduje się w hierarchii domen błędów. Stara się upewnić, że utrata jednej części hierarchii nie wpływa na usługi uruchomione w nim. 

Najlepiej, jeśli ta sama liczba węzłów znajduje się na każdym poziomie głębokości w hierarchii domeny błędów. Jeśli "drzewo" domen błędów jest niezrównoważony w klastrze, jest trudniejsze dla Menedżera zasobów klastra, aby dowiedzieć się najlepszy przydział usług. Nierówne układy domen błędów oznaczają, że utrata niektórych domen wpływa na dostępność usług bardziej niż w innych domenach. W rezultacie Menedżer zasobów klastra jest rozdarty między dwoma celami: 

* Chce korzystać z maszyn w tej "ciężkiej" domenie, umieszczając na nich usługi. 
* Chce umieścić usługi w innych domenach, aby utrata domeny nie powodowała problemów. 

Jak wyglądają niezrównoważone domeny? Na poniższym diagramie przedstawiono dwa różne układy klastra. W pierwszym przykładzie węzły są rozłożone równomiernie między domenami błędów. W drugim przykładzie jedna domena błędów ma o wiele więcej węzłów niż inne domeny błędów. 

<center>

![Dwa różne układy klastra][Image2]
</center>

Na platformie Azure wybór domeny błędów zawiera węzeł jest zarządzany dla Ciebie. Jednak w zależności od liczby węzłów, które aprowizować, nadal można skończyć z domen błędów, które mają więcej węzłów w nich niż w innych. 

Załóżmy na przykład, że w klastrze jest pięć domen błędów, ale aprowizować siedem węzłów dla typu węzła **(NodeType).** W takim przypadku pierwsze dwie domeny błędów kończą się większą liczbą węzłami. Jeśli nadal wdrażać więcej **NodeType** wystąpień tylko kilka wystąpień, problem się pogorszy. Z tego powodu zaleca się, że liczba węzłów w każdym typie węzła jest wielokrotnością liczby domen błędów.

## <a name="upgrade-domains"></a>Uaktualnianie domen
Domeny uaktualniania to kolejna funkcja, która pomaga Menedżerowi zasobów klastra sieci szkieletowej usług zrozumieć układ klastra. Domeny uaktualniania definiują zestawy węzłów, które są uaktualniane w tym samym czasie. Domeny uaktualniania pomagają Menedżerowi zasobów klastra zrozumieć i zorganizować operacje zarządzania, takie jak uaktualnienia.

Domeny uaktualnienia są bardzo podobne do domen błędów, ale z kilkoma kluczowymi różnicami. Po pierwsze obszary skoordynowanych awarii sprzętu definiują domeny błędów. Domeny uaktualnienia, z drugiej strony, są definiowane przez zasady. Możesz zdecydować, ile chcesz, zamiast pozwolić środowisku dyktować numer. Możesz mieć tyle domen uaktualnienia, ile węzłów. Inną różnicą między domenami błędów a domenami uaktualnień jest to, że domeny uaktualnienia nie są hierarchiczne. Zamiast tego są bardziej jak prosty tag. 

Na poniższym diagramie przedstawiono trzy domeny uaktualnienia rozłożone na trzy domeny błędów. Pokazuje również jedno możliwe umieszczenie dla trzech różnych replik usługi stanowej, gdzie każdy kończy się w różnych domen błędów i uaktualnienia. To miejsce docelowe umożliwia utratę domeny błędów w trakcie uaktualniania usługi i nadal ma jedną kopię kodu i danych.  

<center>

![Umieszczanie z domenami błędów i uaktualnień][Image3]
</center>

Istnieją plusy i minusy posiadania dużej liczby domen uaktualnienia. Więcej domen uaktualnienia oznacza, że każdy krok uaktualnienia jest bardziej szczegółowy i wpływa na mniejszą liczbę węzłów lub usług. Mniej usług musi się przemieszczać naraz, wprowadzając mniej zmian w systemie. Ma to tendencję do zwiększenia niezawodności, ponieważ na mniejszą część usługi ma wpływ każdy problem wprowadzony podczas uaktualniania. Więcej domen uaktualnienia oznacza również, że potrzebujesz mniej dostępnego buforu w innych węzłach, aby obsłużyć wpływ uaktualnienia. 

Na przykład jeśli masz pięć domen uaktualnienia, węzły w każdym z nich obsługują około 20 procent ruchu. Jeśli musisz zdjąć tę domenę uaktualnienia dla uaktualnienia, to obciążenie zwykle musi iść gdzieś. Ponieważ masz cztery pozostałe domeny uaktualnienia, każda musi mieć miejsce dla około 5 procent całkowitego ruchu. Więcej domen uaktualnienia oznacza, że potrzebujesz mniej buforu w węzłach w klastrze. 

Zastanów się, czy zamiast tego masz 10 domen uaktualnienia. W takim przypadku każda domena uaktualnienia będzie obsługiwać tylko około 10 procent całkowitego ruchu. Podczas uaktualniania kroki za pośrednictwem klastra, każda domena musiałaby mieć miejsce tylko dla około 1,1 procent całkowitego ruchu. Więcej domen uaktualnienia zazwyczaj pozwalają na uruchamianie węzłów przy wyższym wykorzystaniu, ponieważ potrzebujesz mniej pojemności zarezerwowanej. To samo dotyczy domen błędów.  

Wadą posiadania wielu domen uaktualnienia jest to, że uaktualnienia zwykle trwać dłużej. Sieci szkieletowej usług czeka krótki okres po zakończeniu domeny uaktualnienia i wykonuje kontrole przed rozpoczęciem uaktualniania następnego. Te opóźnienia umożliwiają wykrywanie problemów wprowadzonych przez uaktualnienie przed kontynuowaniem uaktualnienia. Kompromis jest dopuszczalne, ponieważ zapobiega złe zmiany wpływające na zbyt wiele usługi w czasie.

Obecność zbyt mało domen uaktualnienia ma wiele negatywnych skutków ubocznych. Podczas gdy każda domena uaktualnienia jest wyłączona i uaktualniona, duża część ogólnej pojemności jest niedostępna. Na przykład jeśli masz tylko trzy domeny uaktualnienia, stajesz w dół około jednej trzeciej ogólnej pojemności usługi lub klastra naraz. Mając tak dużo usługi w dół na raz nie jest pożądane, ponieważ potrzebujesz wystarczającej pojemności w pozostałej części klastra do obsługi obciążenia. Utrzymanie tego buforu oznacza, że podczas normalnej pracy te węzły są mniej obciążone niż w przeciwnym razie. Zwiększa to koszt uruchomienia usługi.

Nie ma rzeczywistego limitu całkowitej liczby błędów lub uaktualnienia domen w środowisku lub ograniczeń dotyczących ich nakładania się. Ale istnieją wspólne wzorce:

- Domeny błędów i domeny uaktualnień mapowane 1:1
- Jedna domena uaktualnienia na węzeł (fizyczne lub wirtualne wystąpienie systemu operacyjnego)
- Model "rozłożony" lub "macierzowy", w którym domeny błędów i domeny uaktualnienia tworzą macierz z maszynami zwykle biegnącymi w dół przekątnych

<center>

![Układy domen błędów i uaktualnień][Image4]
</center>

Nie ma najlepszej odpowiedzi na to, który układ wybrać. Każdy ma plusy i minusy. Na przykład model 1FD:1UD jest prosty w konfiguracji. Model jednej domeny uaktualnienia na model węzła jest najbardziej podobny do tego, do czego ludzie są przyzwyczajeni. Podczas uaktualniania każdy węzeł jest aktualizowany niezależnie. Jest to podobne do tego, jak małe zestawy maszyn były uaktualniane ręcznie w przeszłości.

Najczęstszym modelem jest macierz FD/UD, w której domeny błędów i domeny uaktualnienia tworzą tabelę, a węzły są umieszczane wzdłuż przekątnej. Jest to model używany domyślnie w klastrach sieci szkieletowej usług na platformie Azure. W przypadku klastrów z wieloma węzłami wszystko wygląda jak wzorzec macierzy gęstej.

> [!NOTE]
> Klastry sieci szkieletowej usług hostowane na platformie Azure nie obsługują zmiany domyślnej strategii. Tylko autonomiczne klastry oferują to dostosowanie.
>

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>Błędy i uaktualnianie ograniczeń domeny i wynikowe zachowanie
### <a name="default-approach"></a>Podejście domyślne
Domyślnie Menedżer zasobów klastra utrzymuje usługi zrównoważone między domenami błędów i uaktualniania. Jest to modelowane jako [ograniczenie](service-fabric-cluster-resource-manager-management-integration.md). Ograniczenie dla domen błędów i uaktualnień stwierdza: "Dla danej partycji usługi, nigdy nie powinno być różnica większa niż jeden w liczbie obiektów usługi (wystąpienia usługi bezstanowej lub repliki usługi stanowej) między dowolnymi dwiema domenami na tej samej hierarchii".

Załóżmy, że to ograniczenie zapewnia gwarancję "maksymalnej różnicy". Ograniczenie dotyczące domen błędów i uaktualnień zapobiega niektórym ruchom lub rozwiązaniom, które naruszają regułę.

Załóżmy na przykład, że mamy klaster z sześcioma węzłami, skonfigurowany z pięcioma domenami błędów i pięcioma domenami uaktualnienia.

|  | Fd0 (fd0) | FD1 (fd1) | FD2 (własnach) | FD3 (własnach) | FD4 (fd4) |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0 (ud0)** |N1 (w 1. | | | | |
| **UD1** |N6 (n6) |N2 (w 2. | | | |
| **Ud2** | | |N3 | | |
| **Ud3** | | | |N4 ( N4 ) | |
| **UD4 (ud4)** | | | | |N5 (w 199 |

Teraz załóżmy, że tworzymy usługę z **TargetReplicaSetSize** (lub dla usługi bezstanowej, **InstanceCount**) wartość pięciu. Repliki lądują na N1-N5. W rzeczywistości N6 nigdy nie jest używany bez względu na to, ile usług, takich jak ten, który tworzysz. Ale dlaczego? Przyjrzyjmy się różnicy między bieżącym układem a tym, co by się stało, gdyby wybrano N6.

Oto układ, który otrzymaliśmy i całkowita liczba replik na usterkę i domenę uaktualnienia:

|  | Fd0 (fd0) | FD1 (fd1) | FD2 (własnach) | FD3 (własnach) | FD4 (fd4) | UDTotal ( UDTotal ) |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0 (ud0)** |R1 | | | | |1 |
| **UD1** | |R2 | | | |1 |
| **Ud2** | | |R3 | | |1 |
| **Ud3** | | | |R4 ( R4 ) | |1 |
| **UD4 (ud4)** | | | | |R5 |1 |
| **FDTotal ( FDTotal )** |1 |1 |1 |1 |1 |- |

Ten układ jest zrównoważony pod względem węzłów na domenę błędów i domeny uaktualnienia. Jest również zrównoważony pod względem liczby replik na usterkę i domeny uaktualnienia. Każda domena ma taką samą liczbę węzłów i taką samą liczbę replik.

Spójrzmy na to, co by się stało, gdybyśmy używali N6 zamiast N2. W jaki sposób repliki będą dystrybuowane wtedy?

|  | Fd0 (fd0) | FD1 (fd1) | FD2 (własnach) | FD3 (własnach) | FD4 (fd4) | UDTotal ( UDTotal ) |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0 (ud0)** |R1 | | | | |1 |
| **UD1** |R5 | | | | |1 |
| **Ud2** | | |R2 | | |1 |
| **Ud3** | | | |R3 | |1 |
| **UD4 (ud4)** | | | | |R4 ( R4 ) |1 |
| **FDTotal ( FDTotal )** |2 |0 |1 |1 |1 |- |

Ten układ narusza naszą definicję gwarancji "maksymalnej różnicy" dla ograniczenia domeny błędów. FD0 ma dwie repliki, podczas gdy FD1 ma zero. Różnica między FD0 i FD1 wynosi w sumie dwa, co jest większe niż maksymalna różnica jednego. Ponieważ ograniczenie zostało naruszone, Menedżer zasobów klastra nie zezwala na to rozwiązanie. Podobnie, gdybyśmy wybrali N2 i N6 (zamiast N1 i N2), otrzymamy:

|  | Fd0 (fd0) | FD1 (fd1) | FD2 (własnach) | FD3 (własnach) | FD4 (fd4) | UDTotal ( UDTotal ) |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0 (ud0)** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **Ud2** | | |R2 | | |1 |
| **Ud3** | | | |R3 | |1 |
| **UD4 (ud4)** | | | | |R4 ( R4 ) |1 |
| **FDTotal ( FDTotal )** |1 |1 |1 |1 |1 |- |

Ten układ jest zrównoważony pod względem domen błędów. Ale teraz jest to naruszenie ograniczenia domeny uaktualnienia, ponieważ UD0 ma zero replik i UD1 ma dwa. Ten układ jest również nieprawidłowy i nie zostanie pobrany przez Menedżera zasobów klastra.

Takie podejście do dystrybucji replik stanowych lub wystąpień bezstanowych zapewnia najlepszą możliwą odporność na uszkodzenia. Jeśli jedna domena uginie się, minimalna liczba replik/wystąpień zostanie utracona. 

Z drugiej strony to podejście może być zbyt rygorystyczne i nie zezwalać klastra na wykorzystanie wszystkich zasobów. W przypadku niektórych konfiguracji klastra nie można używać niektórych węzłów. Może to spowodować, że sieci szkieletowej usług nie umieścić usługi, w wyniku komunikatów ostrzegawczych. W poprzednim przykładzie nie można użyć niektórych węzłów klastra (N6 w przykładzie). Nawet jeśli dodano węzły do tego klastra (N7-N10), repliki/wystąpienia będą umieszczane tylko na N1–N5 z powodu ograniczeń dotyczących domen błędów i uaktualnień. 

|  | Fd0 (fd0) | FD1 (fd1) | FD2 (własnach) | FD3 (własnach) | FD4 (fd4) |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0 (ud0)** |N1 (w 1. | | | |N10 ( N10 ) |
| **UD1** |N6 (n6) |N2 (w 2. | | | |
| **Ud2** | |N7 (n7) |N3 | | |
| **Ud3** | | |N8 (w 199 |N4 ( N4 ) | |
| **UD4 (ud4)** | | | |N9 (n9) |N5 (w 199 |



### <a name="alternative-approach"></a>Podejście alternatywne

Menedżer zasobów klastra obsługuje inną wersję ograniczenia dla domen błędów i uaktualnień. Pozwala na umieszczenie przy jednoczesnym zagwarantowaniu minimalnego poziomu bezpieczeństwa. Alternatywne ograniczenie można podać w następujący sposób: "Dla danej partycji usługi dystrybucji repliki między domenami należy upewnić się, że partycja nie cierpi utraty kworum." Załóżmy, że to ograniczenie zapewnia gwarancję "bezpieczne kworum". 

> [!NOTE]
> Dla usługi stanowej definiujemy *utratę kworum* w sytuacji, gdy większość replik partycji są w dół w tym samym czasie. Na przykład jeśli **TargetReplicaSetSize** jest pięć, zestaw trzech replik reprezentuje kworum. Podobnie jeśli **TargetReplicaSetSize** jest sześć, cztery repliki są niezbędne dla kworum. W obu przypadkach nie więcej niż dwie repliki mogą być w dół w tym samym czasie, jeśli partycja chce nadal działać normalnie. 
>
> W przypadku usługi bezstanowej nie ma czegoś takiego jak *utrata kworum.* Usługi bezstanowe nadal działają normalnie, nawet jeśli większość wystąpień ustępuje w tym samym czasie. Dlatego skupimy się na usługach stanowych w dalszej części tego artykułu.
>

Wróćmy do poprzedniego przykładu. W przypadku "bezpiecznej kworum" ograniczenia wszystkie trzy układy będą prawidłowe. Nawet jeśli FD0 nie powiodło się w drugim układzie lub UD1 nie powiodło się w trzecim układzie, partycja nadal będzie mieć kworum. (Większość replik nadal będzie się znajdować). Z tej wersji ograniczenia, N6 prawie zawsze mogą być wykorzystywane.

Podejście "kworum bezpieczne" zapewnia większą elastyczność niż podejście "maksymalna różnica". Powodem jest to, że łatwiej jest znaleźć dystrybucje replik, które są prawidłowe w prawie każdej topologii klastra. Jednak to podejście nie może zagwarantować najlepsze właściwości tolerancji na uszkodzenia, ponieważ niektóre awarie są gorsze niż inne. 

W najgorszym przypadku większość replik może zostać utracona z powodu awarii jednej domeny i jednej dodatkowej repliki. Na przykład zamiast trzech błędów wymaganych do utraty kworum z pięciu replik lub wystąpień, można teraz stracić większość tylko z dwóch błędów. 

### <a name="adaptive-approach"></a>Podejście adaptacyjne
Ponieważ oba podejścia mają mocne i słabe strony, wprowadziliśmy podejście adaptacyjne, które łączy te dwie strategie.

> [!NOTE]
> Jest to domyślne zachowanie, począwszy od sieci szkieletowej usług w wersji 6.2. 
> 
> Adaptacyjne podejście domyślnie używa logiki "maksymalna różnica" i przełącza się na logikę "kworum bezpieczne" tylko wtedy, gdy jest to konieczne. Menedżer zasobów klastra automatycznie określa, która strategia jest niezbędna, analizując sposób konfigurowania klastra i usług.
> 
> Menedżer zasobów klastra powinien używać logiki "opartej na kworum" dla usługi, które spełniają oba te warunki:
>
> * **TargetReplicaSetSize** dla usługi jest równomiernie podzielny przez liczbę domen błędów i liczbę domen uaktualnienia.
> * Liczba węzłów jest mniejsza lub równa liczbie domen błędów pomnożonej przez liczbę domen uaktualnienia.
>
> Należy pamiętać, że Menedżer zasobów klastra użyje tego podejścia zarówno dla usług bezstanowych, jak i stanowych, nawet jeśli utrata kworum nie jest istotna dla usług bezstanowych.

Wróćmy do poprzedniego przykładu i załóżmy, że klaster ma teraz osiem węzłów. Klaster jest nadal skonfigurowany z pięciu domen błędów i pięć domen uaktualnienia i **TargetReplicaSetSize** wartość usługi hostowane w tym klastrze pozostaje pięć. 

|  | Fd0 (fd0) | FD1 (fd1) | FD2 (własnach) | FD3 (własnach) | FD4 (fd4) |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0 (ud0)** |N1 (w 1. | | | | |
| **UD1** |N6 (n6) |N2 (w 2. | | | |
| **Ud2** | |N7 (n7) |N3 | | |
| **Ud3** | | |N8 (w 199 |N4 ( N4 ) | |
| **UD4 (ud4)** | | | | |N5 (w 199 |

Ponieważ wszystkie niezbędne warunki są spełnione, Menedżer zasobów klastra użyje logiki "opartej na kworum" w dystrybucji usługi. Umożliwia to korzystanie z N6-N8. Jedna z możliwych dystrybucji usług w tym przypadku może wyglądać następująco:

|  | Fd0 (fd0) | FD1 (fd1) | FD2 (własnach) | FD3 (własnach) | FD4 (fd4) | UDTotal ( UDTotal ) |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0 (ud0)** |R1 | | | | |1 |
| **UD1** |R2 | | | | |1 |
| **Ud2** | |R3 |R4 ( R4 ) | | |2 |
| **Ud3** | | | | | |0 |
| **UD4 (ud4)** | | | | |R5 |1 |
| **FDTotal ( FDTotal )** |2 |1 |1 |0 |1 |- |

Jeśli wartość **TargetReplicaSetSize** usługi zostanie zmniejszona do czterech (na przykład), Menedżer zasobów klastra zauważy tę zmianę. Zostanie wznowione przy użyciu logiki "maksymalna różnica", ponieważ **TargetReplicaSetSize** nie jest podzielony przez liczbę domen błędów i domen uaktualnienia już. W rezultacie niektóre ruchy repliki wystąpią do dystrybucji pozostałych czterech replik w węzłach N1-N5. W ten sposób nie narusza się wersji domeny błędów i logiki domeny uaktualnienia "maksymalna różnica". 

W poprzednim układzie, jeśli **Wartość TargetReplicaSetSize** wynosi pięć, a N1 zostanie usunięty z klastra, liczba domen uaktualnienia staje się równa czterem. Ponownie Menedżer zasobów klastra rozpoczyna korzystanie z logiki "maksymalna różnica", ponieważ liczba domen uaktualnienia nie dzieli już wartości **TargetReplicaSetSize** usługi. W rezultacie replika R1, po ponownym zbudowaniu, musi wylądować na N4, aby ograniczenie dotyczące domeny błędów i uaktualnienia nie zostało naruszone.

|  | Fd0 (fd0) | FD1 (fd1) | FD2 (własnach) | FD3 (własnach) | FD4 (fd4) | UDTotal ( UDTotal ) |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0 (ud0)** |Nie dotyczy |Nie dotyczy |Nie dotyczy |Nie dotyczy |Nie dotyczy |Nie dotyczy |
| **UD1** |R2 | | | | |1 |
| **Ud2** | |R3 |R4 ( R4 ) | | |2 |
| **Ud3** | | | |R1 | |1 |
| **UD4 (ud4)** | | | | |R5 |1 |
| **FDTotal ( FDTotal )** |1 |1 |1 |1 |1 |- |

## <a name="configuring-fault-and-upgrade-domains"></a>Konfigurowanie domen błędów i uaktualnień
W wdrożeniach sieci szkieletowej usług hostowanych na platformie Azure domeny błędów i domeny uaktualnień są definiowane automatycznie. Usługa Service Fabric pobiera i używa informacji o środowisku z platformy Azure.

Jeśli tworzysz własny klaster (lub chcesz uruchomić określoną topologię w rozwoju), możesz samodzielnie podać domenę błędów i uaktualnić informacje o domenie. W tym przykładzie definiujemy klaster lokalnego rozwoju dziewięciu węzłów, który obejmuje trzy centra danych (każdy z trzema stojakami). Ten klaster ma również trzy domeny uaktualnienia rozłożone na te trzy centra danych. Oto przykład konfiguracji w pliku ClusterManifest.xml:

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

W tym przykładzie użyto clusterconfig.json dla wdrożeń autonomicznych:

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
> Podczas definiowania klastrów za pośrednictwem usługi Azure Resource Manager platforma Azure przypisuje domeny błędów i domeny uaktualniania. Tak więc definicja typów węzłów i zestawów skalowania maszyny wirtualnej w szablonie usługi Azure Resource Manager nie zawiera informacji o domenie błędów lub domenie uaktualnienia.
>

## <a name="node-properties-and-placement-constraints"></a>Właściwości węzła i ograniczenia umieszczania
Czasami (w rzeczywistości przez większość czasu) należy upewnić się, że niektóre obciążenia są uruchamiane tylko na niektórych typach węzłów w klastrze. Na przykład niektóre obciążenia mogą wymagać procesorów GPU lub dysków SSD, a inne nie. 

Doskonałym przykładem kierowania sprzętu do określonych obciążeń jest prawie każda architektura n-warstwowa. Niektóre maszyny służą jako frontonu lub api obsługujących stronie aplikacji i są narażone na klientów lub Internetu. Różne maszyny, często z różnymi zasobami sprzętowymi, obsługują pracę warstw obliczeniowych lub magazynowych. Zazwyczaj _nie_ są one bezpośrednio narażone na kontakt z klientami lub internetem. 

Sieci szkieletowej usług oczekuje, że w niektórych przypadkach określone obciążenia mogą być konieczne do uruchomienia w określonych konfiguracjach sprzętu. Przykład:

* Istniejąca aplikacja n-warstwowa została "podniesiona i przesunięta" do środowiska sieci szkieletowej usług.
* Obciążenie musi być uruchamiane na określonym sprzęcie ze względu na wydajność, skalę lub izolację zabezpieczeń.
* Obciążenie powinno być odizolowane od innych obciążeń ze względów zużycia zasad lub zasobów.

Aby obsługiwać tego rodzaju konfiguracje, sieci szkieletowej usług zawiera tagi, które można zastosować do węzłów. Tagi te są nazywane *właściwościami węzła*. *Ograniczenia umieszczania* są instrukcjami dołączonymi do poszczególnych usług wybranych dla jednej lub więcej właściwości węzła. Ograniczenia umieszczania określają, gdzie usługi powinny być uruchamiane. Zestaw ograniczeń jest rozszerzalny. Każda para kluczy/wartości może działać. 

<center>

![Różne obciążenia układu klastra][Image5]
</center>

### <a name="built-in-node-properties"></a>Wbudowane właściwości węzła
Sieci szkieletowej usług definiuje niektóre domyślne właściwości węzła, które mogą być używane automatycznie, dzięki czemu nie trzeba ich definiować. Domyślne właściwości zdefiniowane w każdym węźle to **NodeType** i **NodeName**. 

Na przykład można zapisać ograniczenie `"(NodeType == NodeType03)"`umieszczania jako . **NodeType** jest powszechnie używaną właściwością. Jest to przydatne, ponieważ odpowiada 1:1 z typem maszyny. Każdy typ komputera odpowiada typowi obciążenia w tradycyjnej aplikacji n-warstwowej.

<center>

![Ograniczenia umieszczania i właściwości węzła][Image6]
</center>

## <a name="placement-constraints-and-node-property-syntax"></a>Ograniczenia umieszczania i składnia właściwości węzła 
Wartość określona we właściwości węzła może być ciągiem, wartością logiczną lub podpisaną długą. Instrukcja w usłudze jest nazywany *ograniczeniem* umieszczania, ponieważ ogranicza miejsce, w którym usługa może działać w klastrze. Ograniczeniem może być dowolna instrukcja logiczna, która działa na właściwościach węzła w klastrze. Prawidłowe selektory w tych instrukcjach logicznych to:

* Kontrole warunkowe tworzenia określonych instrukcji:

  | Instrukcja | Składnia |
  | --- |:---:|
  | "równa" | "==" |
  | "nie równa się" | "!=" |
  | "większa niż" | ">" |
  | "większa lub równa" | ">=" |
  | "mniej niż" | "<" |
  | "mniej niż lub równe" | "<=" |

* Instrukcje logiczne dla grupowania i operacji logicznych:

  | Instrukcja | Składnia |
  | --- |:---:|
  | "i" | "&&" |
  | "lub" | "&#124;&#124;" |
  | "nie" | "!" |
  | "grupa jako pojedyncze oświadczenie" | "()" |

Oto kilka przykładów podstawowych instrukcji ograniczeń:

  * `"Value >= 5"`
  * `"NodeColor != green"`
  * `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

Tylko węzły, w których ogólna instrukcja ograniczenia umieszczania jest oceniana na "True", mogą mieć na niej usługę. Węzły, które nie mają zdefiniowanej właściwości, nie pasują do żadnego ograniczenia umieszczania zawierającego właściwość.

Załóżmy, że następujące właściwości węzła zostały zdefiniowane dla typu węzła w pliku ClusterManifest.xml:

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasSSD" Value="true"/>
        <Property Name="NodeColor" Value="green"/>
        <Property Name="SomeProperty" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

W poniższym przykładzie przedstawiono właściwości węzła zdefiniowane za pośrednictwem programu ClusterConfig.json dla wdrożeń autonomicznych lub pliku Template.json dla klastrów hostowanych na platformie Azure. 

> [!NOTE]
> W szablonie usługi Azure Resource Manager typ węzła jest zwykle sparametryzowany. To będzie `"[parameters('vmNodeType1Name')]"` wyglądać, a nie NodeType01.
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

*Ograniczenia* umieszczania usług dla usługi można utworzyć w następujący sposób:

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

Jeśli wszystkie węzły NodeType01 są prawidłowe, można również `"(NodeType == NodeType01)"`wybrać ten typ węzła z ograniczeniem .

Ograniczenia umieszczania usługi mogą być aktualizowane dynamicznie w czasie wykonywania. W razie potrzeby można przenieść usługę w klastrze, dodać i usunąć wymagania itd. Sieć szkieletowa usług zapewnia, że usługa pozostaje w górę i dostępne nawet po wprowadzeniu tego typu zmian.

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

```PowerShell
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

Ograniczenia umieszczania są określone dla każdego wystąpienia usługi o nazwie. Aktualizacje zawsze zastępują to, co zostało wcześniej określone.

Definicja klastra definiuje właściwości w węźle. Zmiana właściwości węzła wymaga uaktualnienia do konfiguracji klastra. Uaktualnianie właściwości węzła wymaga każdego węzła, którego dotyczy problem, aby ponownie uruchomić, aby zgłosić jego nowe właściwości. Usługa Fabric zarządza tymi uaktualnieniami stopniowymi.

## <a name="describing-and-managing-cluster-resources"></a>Opisywanie zasobów klastra i zarządzanie nimi
Jednym z najważniejszych zadań dowolnego koordynatora jest pomoc w zarządzaniu zużyciem zasobów w klastrze. Zarządzanie zasobami klastra może oznaczać kilka różnych rzeczy. 

Po pierwsze, nie ma zapewnienie, że maszyny nie są przeciążone. Oznacza to upewnienie się, że maszyny nie są uruchomione więcej usług, niż mogą obsłużyć. 

Po drugie, istnieje równoważenie i optymalizacja, które mają kluczowe znaczenie dla efektywnego uruchamiania usług. Ekonomiczne lub zależne od wydajności oferty usług nie mogą pozwolić, aby niektóre węzły były gorące, podczas gdy inne są zimne. Gorące węzły prowadzą do rywalizacji o zasoby i niskiej wydajności. Zimne węzły reprezentują zmarnowane zasoby i zwiększone koszty. 

Usługa Sieci szkieletowej reprezentuje zasoby jako *metryki*. Metryki to dowolny zasób logiczny lub fizyczny, który chcesz opisać w sieci szkieletowej usług. Przykładami metryk są "WorkQueueDepth" lub "MemoryInMb". Aby uzyskać informacje na temat zasobów fizycznych, którymi sieci szkieletowej usług może zarządzać w węzłach, zobacz [Zarządzanie zasobami](service-fabric-resource-governance.md). Aby uzyskać informacje na temat domyślnych metryk używanych przez Menedżera zasobów klastra i sposobu konfigurowania metryk niestandardowych, zobacz [ten artykuł](service-fabric-cluster-resource-manager-metrics.md).

Metryki różnią się od ograniczeń umieszczania i właściwości węzła. Właściwości węzła są statycznymi deskryptorami samych węzłów. Metryki opisują zasoby, które mają węzły i że usługi zużywają podczas uruchamiania w węźle. Właściwość węzła może być **HasSSD** i może być ustawiona na true lub false. Ilość miejsca dostępnego na tym dysku SSD i ile jest zużywana przez usługi będzie metryka jak "DriveSpaceInMb". 

Podobnie jak w przypadku ograniczeń umieszczania i właściwości węzłów, Menedżer zasobów klastra sieci szkieletowej usług nie rozumie, co oznaczają nazwy metryk. Nazwy metryki to tylko ciągi. Dobrą praktyką jest deklarowanie jednostek jako części nazw metryk, które można utworzyć, gdy mogą być niejednoznaczne.

## <a name="capacity"></a>Pojemność
Jeśli wyłączysz wszystkie *równoważenie*zasobów, Menedżer zasobów klastra sieci szkieletowej usług nadal zapewnia, że żaden węzeł nie przejdzie przez jego pojemność. Zarządzanie przekroczeniem pojemności jest możliwe, chyba że klaster jest zbyt pełny lub obciążenie jest większe niż jakikolwiek węzeł. Pojemność to kolejne *ograniczenie* używane przez Menedżera zasobów klastra do zrozumienia, jaka część zasobu ma węzeł. Pozostała pojemność jest również śledzona dla klastra jako całości. 

Zarówno pojemność, jak i zużycie na poziomie usług są wyrażone w kategoriach metryk. Na przykład metryka może być "ClientConnections" i węzeł może mieć pojemność dla "ClientConnections" 32,768. Inne węzły mogą mieć inne limity. Usługa uruchomiona w tym węźle może powiedzieć, że obecnie zużywa 32 256 metryki "ClientConnections".

W czasie wykonywania Menedżer zasobów klastra śledzi pozostałą pojemność w klastrze i w węzłach. Aby śledzić pojemność, Menedżer zasobów klastra odejmuje użycie każdej usługi z pojemności węzła, w którym usługa jest uruchamiana. Dzięki tym informacjom Menedżer zasobów klastra może dowiedzieć się, gdzie umieścić lub przenieść repliki, aby węzły nie przekroczyły pojemności.

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

Pojemności zdefiniowane w manifeście klastra są widoczne. Oto przykład clustermanifest.xml:

```xml
    <NodeType Name="NodeType03">
      <Capacities>
        <Capacity Name="ClientConnections" Value="65536"/>
      </Capacities>
    </NodeType>
```

Oto przykład pojemności zdefiniowanych za pośrednictwem clusterconfig.json dla wdrożeń autonomicznych lub Template.json dla klastrów hostowanych na platformie Azure: 

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

Obciążenie usługi często zmienia się dynamicznie. Załóżmy, że obciążenie repliki "ClientConnections" uległo zmianie z 1024 na 2048. Węzeł, który był uruchomiony na następnie miał pojemność tylko 512 pozostałych dla tej metryki. Teraz, że replika lub rozmieszczenie wystąpienia jest nieprawidłowy, ponieważ nie ma wystarczającej ilości miejsca w tym węźle. Menedżer zasobów klastra musi odzyskać węzeł poniżej pojemności. Zmniejsza obciążenie węzła, który jest ponad pojemność, przenosząc jedną lub więcej replik lub wystąpień z tego węzła do innych węzłów. 

Menedżer zasobów klastra próbuje zminimalizować koszt przenoszenia replik. Możesz dowiedzieć się więcej o [kosztach ruchu](service-fabric-cluster-resource-manager-movement-cost.md) i o [strategiach i regułach przywracania równowagi.](service-fabric-cluster-resource-manager-metrics.md)

## <a name="cluster-capacity"></a>Pojemność klastra
W jaki sposób Menedżer zasobów klastra sieci szkieletowej usług utrzymuje zbyt pełny klaster? Przy dynamicznym obciążeniu niewiele można zrobić. Usługi mogą mieć swój skok obciążenia niezależnie od akcji, które wykonuje Menedżer zasobów klastra. W rezultacie, twój klaster z dużą ilością headroom dziś może być słaby, jeśli nie ma skok jutro. 

Formanty w Menedżerze zasobów klastra pomagają zapobiegać problemom. Pierwszą rzeczą, którą można zrobić, to zapobiec tworzeniu nowych obciążeń, które mogłyby spowodować, że klaster stanie się pełny.

Załóżmy, że tworzysz usługę bezstanową i ma z nią pewne obciążenie. Usługa dba o metryki "DiskSpaceInMb". Usługa będzie zużywać pięć jednostek "DiskSpaceInMb" dla każdego wystąpienia usługi. Chcesz utworzyć trzy wystąpienia usługi. Oznacza to, że potrzebujesz 15 jednostek "DiskSpaceInMb", aby być obecne w klastrze, aby nawet utworzyć te wystąpienia usługi.

Menedżer zasobów klastra stale oblicza pojemność i zużycie każdej metryki, dzięki czemu może określić pozostałą pojemność w klastrze. Jeśli nie ma wystarczającej ilości miejsca, Menedżer zasobów klastra odrzuca wywołanie utworzenia usługi.

Ponieważ wymaganie jest tylko, że 15 jednostek będą dostępne, można przydzielić to miejsce na wiele różnych sposobów. Na przykład może istnieć jedna pozostała jednostka pojemności na 15 różnych węzłach lub trzy pozostałe jednostki pojemności w pięciu różnych węzłach. Jeśli Menedżer zasobów klastra może zmienić kolejność czynności, tak aby w trzech węzłach było dostępnych pięć jednostek, usługa jest umieszczana. Zmiana rozmieszczenia klastra jest zwykle możliwe, chyba że klaster jest prawie pełny lub istniejące usługi nie mogą być skonsolidowane z jakiegoś powodu.

## <a name="buffered-capacity"></a>Pojemność buforowana
Pojemność buforowana jest kolejną funkcją Menedżera zasobów klastra. Umożliwia rezerwację pewnej części ogólnej pojemności węzła. Ten bufor pojemności jest używany tylko do umieszczania usług podczas uaktualniania i awarii węzłów. 

Pojemność buforowana jest określona globalnie dla metryki dla wszystkich węzłów. Wartość pobrana dla pojemności zarezerwowanej jest funkcją liczby domen błędów i uaktualnień, które masz w klastrze. Więcej domen błędów i uaktualnień oznacza, że można wybrać mniejszą liczbę dla buforowanych pojemności. Jeśli masz więcej domen, można oczekiwać, że mniejsze ilości klastra będą niedostępne podczas uaktualniania i awarii. Określanie buforowane pojemności ma sens tylko wtedy, gdy określono również pojemność węzła dla metryki.

Oto przykład określania buforowanych pojemności w pliku ClusterManifest.xml:

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="SomeMetric" Value="0.15" />
            <Parameter Name="SomeOtherMetric" Value="0.20" />
        </Section>
```

Oto przykład określania buforowanych pojemności za pośrednictwem programu ClusterConfig.json dla wdrożeń autonomicznych lub pliku Template.json dla klastrów hostowanych na platformie Azure:

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

Tworzenie nowych usług kończy się niepowodzeniem, gdy klaster jest poza buforowane zdolności dla metryki. Zapobieganie tworzeniu nowych usług w celu zachowania buforu gwarantuje, że uaktualnienia i awarie nie powodują węzłów, aby przejść przez pojemność. Pojemność buforowana jest opcjonalna, ale zaleca się ją w dowolnym klastrze, który definiuje pojemność metryki.

Menedżer zasobów klastra udostępnia te informacje o obciążeniu. Dla każdej metryki informacje te obejmują: 
- Buforowane ustawienia pojemności.
- Całkowita pojemność.
- Zużycie prądu.
- Czy każda metryka jest uważana za zrównoważoną, czy nie.
- Statystyki dotyczące odchylenia standardowego.
- Węzły, które mają największe i najmniejsze obciążenie.  
  
Poniższy kod przedstawia przykład tego wyjścia:

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

## <a name="next-steps"></a>Następne kroki
* Aby uzyskać informacje na temat architektury i przepływu informacji w Menedżerze zasobów klastra, zobacz [Omówienie architektury Menedżera zasobów klastra](service-fabric-cluster-resource-manager-architecture.md).
* Definiowanie metryk defragmentacji jest jednym ze sposobów konsolidacji obciążenia w węzłach zamiast rozmieszczenia go. Aby dowiedzieć się, jak skonfigurować defragmentację, zobacz [Defragmentacja metryk i obciążenie w sieci szkieletowej usług](service-fabric-cluster-resource-manager-defragmentation-metrics.md).
* Zacznij od początku i [uzyskaj wprowadzenie do Menedżera zasobów klastra sieci szkieletowej usług](service-fabric-cluster-resource-manager-introduction.md).
* Aby dowiedzieć się, jak Menedżer zasobów klastra zarządza i równoważy obciążenie w klastrze, zobacz [Równoważenie klastra sieci szkieletowej usług](service-fabric-cluster-resource-manager-balancing.md).

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png
