---
title: Metody routingu ruchu w usłudze Azure Traffic Manager — | Dokumentacja firmy Microsoft
description: Ten artykuł ułatwia zrozumienie metod routingu ruchu innego, używane przez usługę Traffic Manager
services: traffic-manager
documentationcenter: ''
author: KumudD
manager: timlt
editor: ''
ms.assetid: db1efbf6-6762-4c7a-ac99-675d4eeb54d0
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2017
ms.author: kumud
ms.openlocfilehash: 03f1cc3a34fa8a472dcab9654b65cc97b8473993
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2018
ms.locfileid: "39398621"
---
# <a name="traffic-manager-routing-methods"></a>Metody routingu w usłudze Traffic Manager

Usługa Azure Traffic Manager obsługuje cztery metody kierowania ruchu, aby określić, jak kierować ruchem sieciowym do różnych punktów końcowych usługi. Usługa Traffic Manager dotyczy każdego zapytania DNS, który odbierze metody routingu ruchu. Metody routingu ruchu określa, w którym punktem końcowym zwracany w odpowiedzi DNS.

Dostępne są cztery metody kierowania ruchu w usłudze Traffic Manager:

* **[Priorytet](#priority):** wybierz **priorytet** Jeśli chcesz używać punktu końcowego usługi podstawowej dla całego ruchu i wykonywanie kopii zapasowych w przypadku podstawowej lub kopii zapasowej punkty końcowe są niedostępne.
* **[Ważona](#weighted):** wybierz **ważona** Jeśli chcesz rozproszyć ruch między zestawem punktów końcowych, albo równomiernie lub według wagi, które można zdefiniować.
* **[Wydajność](#performance):** wybierz **wydajności** gdy mają punkty końcowe w różnych lokalizacjach geograficznych i chcesz, aby używać punktu końcowego "najbliższy" pod kątem najniższych opóźnieniem sieci użytkowników końcowych.
* **[Geograficzne](#geographic):** wybierz **geograficzne** tak, aby użytkownicy są kierowane do określonych punktów końcowych (Azure zewnętrzne i zagnieżdżone) oparte na lokalizacji geograficznej, do której pochodzi swoje zapytanie DNS. Umożliwia to klientom usługi Traffic Manager umożliwia realizację scenariuszy, w których jest ważna, wiedząc, regionu geograficznego użytkownika i routing, ich oparta na. Przykłady obejmują zgodnych z zleceń niezależność danych, lokalizacja środowiska użytkownika & zawartości i pomiaru ruchu z różnych regionów.

Wszystkie profile usługi Traffic Manager obejmują monitorowanie kondycji punktu końcowego i punktu końcowego automatycznego trybu failover. Aby uzyskać więcej informacji, zobacz [monitorowanie punktu końcowego usługi Traffic Manager](traffic-manager-monitoring.md). Jeden profil usługi Traffic Manager można użyć tylko jedna metoda routingu ruchu. Metody routingu ruchu innego można wybrać dla swojego profilu w dowolnym momencie. Zmiany są stosowane w ciągu jednej minuty, a jest naliczany bez przestojów. Metody routingu ruchu mogą być połączone za pomocą zagnieżdżone profile usługi Traffic Manager. Zagnieżdżanie umożliwia zaawansowane i elastyczne konfiguracje routing ruchu procesów, które spełnienia wymagań większych i złożonych aplikacji. Aby uzyskać więcej informacji, zobacz [zagnieżdżone profile usługi Traffic Manager](traffic-manager-nested-profiles.md).

## <a name = "priority"></a>Metody routingu ruchu na priorytecie

Często organizacja chce zapewniają niezawodność swoich usług, wdrażając co najmniej jedna usługa tworzenia kopii zapasowych w przypadku, gdy ich podstawowa usługa ulegnie awarii. Metody routingu ruchu "Priority" umożliwia klientom platformy Azure można łatwo zaimplementować ten wzorzec pracy awaryjnej.

![Usługa Azure Traffic Manager "Priority" metody routingu ruchu][1]

Profil usługi Traffic Manager zawiera priorytetową listą punktów końcowych usługi. Domyślnie usługa Traffic Manager wysyła cały ruch do podstawowego punktu końcowego (najwyższy priorytet). Jeśli podstawowy punkt końcowy jest niedostępny, usługa Traffic Manager kieruje ruchem do drugiego punktu końcowego. Jeśli zarówno podstawowe i pomocnicze punktów końcowych, które nie są dostępne, ruch jest przesyłany do innego i tak dalej. Dostępność punktu końcowego jest oparta na skonfigurowany stan (włączone lub wyłączone) i monitorowania ciągłego punktu końcowego.

### <a name="configuring-endpoints"></a>Konfigurowanie punktów końcowych

Za pomocą usługi Azure Resource Manager, możesz skonfigurować priorytet punktu końcowego jawnie przy użyciu właściwości "priority" dla każdego punktu końcowego. Ta właściwość jest wartością z zakresu od 1 do 1000. Niższe wartości reprezentują wyższy priorytet. Punkty końcowe nie mogą mieć wartości priorytetu. Ustawienie właściwości jest opcjonalne. Gdy argument jest pominięty, jest używany domyślny priorytet w kolejności punktu końcowego.

##<a name = "weighted"></a>Metody routingu ruchu ważonego
"Ważona" metody routingu ruchu pozwala równomierne rozłożenie ruchu sieciowego lub użyć wstępnie zdefiniowanych wagi.

![Usługa Azure Traffic Manager "Ważona" metody rozsyłania ruchu][2]

W ważona metody routingu ruchu należy przypisać wagi do każdego punktu końcowego w konfiguracji profilu usługi Traffic Manager. Waga jest liczbą całkowitą z zakresu od 1 do 1000. Ten parametr jest opcjonalny. W przypadku pominięcia Menedżery Traffic Manager korzysta z domyślnego wagę "1". Wyższa waga, tym wyższy priorytet.

Dla każdego odebrane zapytania DNS Traffic Manager losowo wybiera dostępnego punktu końcowego. Prawdopodobieństwo, że jest wybranie punktu końcowego jest oparty na przypisane wszystkie dostępne punkty końcowe wagi. Przy użyciu tymi samymi wagami dla wszystkich punktów końcowych skutkuje Dystrybucja ruchu nawet. Za pomocą wag wyższe lub niższe w określonych punktach końcowych powoduje, że te punkty końcowe częściej lub rzadziej zwracana w odpowiedzi DNS.

Metoda ważona zapewnia pewne przydatne w scenariuszach:

* Uaktualnienie stopniowe aplikacji: przydzielić procent ruchu sieciowego, aby kierować do nowego punktu końcowego, a następnie stopniowo zwiększać ruchu, wraz z upływem czasu do 100%.
* Migracja aplikacji na platformie Azure: Tworzenie profilu za pomocą zarówno dla platformy Azure, jak i zewnętrznych punktów końcowych. Dostosuj wagę punktów końcowych preferowanie nowe punkty końcowe.
* Rozszerzanie możliwości chmury za dodatkową pojemność: szybko rozbudować wdrożenia lokalnego do chmury, ustawiając dla niego za profilu usługi Traffic Manager. Jeśli potrzebujesz dodatkowej pojemności w chmurze, należy dodać lub włączyć dodatkowe punkty końcowe i określić, jaka część ruchu jest przesyłany do każdego punktu końcowego.

Portal Azure Resource Manager obsługuje konfigurację następujących routingu ważonego ruchu.  Można skonfigurować przy użyciu usługi Resource Manager wersje programu Azure PowerShell, interfejsu wiersza polecenia i interfejsów API REST wagi.

Jest ważne dowiedzieć się, że odpowiedzi DNS są buforowane przez klientów i przez cykliczne serwery DNS, używanych przez klientów do rozpoznawania nazw DNS. Tej buforowanie może mieć wpływ na dystrybucje ważona ruchu. W przypadku dużej liczby klientów i cykliczne serwery DNS Dystrybucja ruchu działa zgodnie z oczekiwaniami. Jednak gdy liczba klientów lub cykliczne serwery DNS jest mała, buforowanie może znacznie pochylanie Dystrybucja ruchu.

Typowe przypadki użycia obejmują:

* Środowisk programowania i testowania
* Komunikacja aplikacji do aplikacji
* Aplikacje mające celu wąskie użytkownika-podstawową, które współdzielą wspólna infrastruktura DNS rekursywnego (na przykład pracownicy firmy, łącząc się za pośrednictwem serwera proxy)

Efekty buforowania te DNS są wspólne dla wszystkich ruchu opartego na protokole DNS systemów routingu, nie tylko usługi Azure Traffic Manager. W niektórych przypadkach jawnie wyczyszczenie pamięci podręcznej DNS może dostarczyć obejście tego problemu. W innych przypadkach może być bardziej odpowiednie alternatywną metodę routingu ruchu.

## <a name = "performance"></a>Metody routingu ruchu wydajności

Wdrażanie punktów końcowych w co najmniej dwóch lokalizacjach na całym świecie może zwiększyć szybkość reakcji wiele aplikacji routing ruchu do lokalizacji, która jest najbliżej, do Ciebie. Metody routingu ruchu "Wydajność" zapewnia tę funkcję.

![Usługa Azure Traffic Manager "Wydajność" metody routingu ruchu][3]

"Najbliższy" punkt końcowy nie jest koniecznie najbliższego mierzony geograficznej odległości. Zamiast tego metody routingu ruchu "Wydajność" Określa najbliższego punktu końcowego, mierząc opóźnienia sieci. Usługa Traffic Manager obsługuje Internet opóźnienie tabeli, aby śledzić czas obustronnej konwersji między zakresów adresów IP i każdego centrum danych platformy Azure.

Usługa Traffic Manager wyszukuje źródłowy adres IP przychodzące żądanie DNS w tabeli opóźnienie Internet. Usługa Traffic Manager wybierze dostępnego punktu końcowego w centrum danych platformy Azure, które ma najniższe opóźnienie dla tego zakresu adresów IP, a następnie zwraca punkt końcowy w odpowiedzi DNS.

Jak wyjaśniono w [jak działa usługa Traffic Manager](traffic-manager-how-it-works.md), usługa Traffic Manager nie otrzyma zapytania DNS bezpośrednio od klientów. Przeciwnie zapytania DNS pochodzą z rekursywnych usług DNS, klienci są skonfigurowane do używania. W związku z tym, adres IP używany do określenia punktu końcowego "najbliższy" nie jest adres IP klienta, ale jest to adres IP z rekursywnych usług DNS. W praktyce ten adres IP jest dobrym serwera proxy dla klienta.


Usługa Traffic Manager regularnie aktualizuje tabeli opóźnienie Internet, aby uwzględnić zmiany w globalnych Internet i nowych regionów systemu Azure. Jednakże wydajność aplikacji zależy w czasie rzeczywistym wahania obciążenia w Internecie. Routing ruchu wydajności nie monitoruje obciążenia w punkcie końcowym danej usługi. Jednak jeśli punktu końcowego będą niedostępne, usługa Traffic Manager nie ma go w odpowiedzi na zapytania DNS.


Informacje, które należy zwrócić uwagę:

* Jeśli Twój profil zawiera wiele punktów końcowych w tym samym regionie platformy Azure, następnie usługi Traffic Manager rozkłada ruch równomiernie na dostępnych punktów końcowych w tym regionie. Jeśli wolisz dystrybucji różnego ruchu w obrębie regionu, możesz użyć [zagnieżdżone profile usługi Traffic Manager](traffic-manager-nested-profiles.md).
* Wszystkich włączonych punktów końcowych w najbliższego regionu platformy Azure są ograniczone, usługa Traffic Manager przenosi ruchu do punktów końcowych w dalej najbliższego regionu platformy Azure. Aby zdefiniować sekwencję preferowanych trybu failover, należy użyć [zagnieżdżone profile usługi Traffic Manager](traffic-manager-nested-profiles.md).
* Korzystając z metody routingu ruchu wydajności z zewnętrznych punktów końcowych lub zagnieżdżone punktów końcowych, należy określić lokalizację tych punktów końcowych. Wybierz region platformy Azure, która jest najbliżej danego wdrożenia. Te lokalizacje są obsługiwane przez tabelę opóźnienie Internet wartości.
* Algorytmu, który wybiera punkt końcowy jest deterministyczna. Powtarzanych zapytań DNS z tego samego klienta są kierowane do tego samego punktu końcowego. Zazwyczaj używają różnych cykliczne serwery DNS podczas podróży. Klient może być przesłane do innego punktu końcowego. Routing może mieć wpływ również przez aktualizacje tabeli opóźnienie Internet. W związku z tym metody routingu ruchu wydajności nie gwarantuje, że klient jest zawsze kierowane do tego samego punktu końcowego.
* Po zmianie tabeli opóźnienie Internetu, można zauważyć, że niektóre klienty są kierowane do innego punktu końcowego. Ta zmiana routingu jest bardziej precyzyjne operacje oparte na bieżących danych opóźnienia. Aktualizacje te są niezbędne do utrzymania dokładności routing ruchu wydajności, zgodnie z Internetu stale ewoluuje.

## <a name = "geographic"></a>Metody geograficznego routingu ruchu

Profile usługi Traffic Manager można skonfigurować do użycia metody routingu geograficznego, dzięki czemu użytkownicy są kierowane do określonych punktów końcowych (Azure, zewnętrzne i zagnieżdżone) oparte na lokalizacji geograficznej, do której pochodzi swoje zapytanie DNS. Umożliwia to klientom usługi Traffic Manager umożliwia realizację scenariuszy, w których jest ważna, wiedząc, regionu geograficznego użytkownika i routing, ich oparta na. Przykłady obejmują zgodnych z zleceń niezależność danych, lokalizacja środowiska użytkownika & zawartości i pomiaru ruchu z różnych regionów.
Po skonfigurowaniu profilu geograficznego routingu każdy punkt końcowy skojarzony profil musi mieć zestaw regionów geograficznych, które do niej przypisany. Region geograficzny może znajdować się na następujących poziomach szczegółowości 
- World — dowolny region
- Regionalne grupowanie — na przykład Afryki, Bliski Wschód, Australia/Pacyfik itp. 
- Kraj/Region — na przykład Irlandia, Peru, SRA Hongkong itp. 
- Stan/prowincję/Województwo — na przykład autostrady dla USA, Australia-Queensland, Alberta Kanada itp. (Uwaga: ten poziom szczegółowości jest obsługiwana tylko dla stanów / prowincji w Australii, Kanadzie, Zjednoczonym Królestwie i USA).

Po przypisaniu punktu końcowego regionu lub zestawie regionów wszystkie żądania z tych regionów kierowana tylko do określonego punktu końcowego. Usługi Traffic Manager używa adres IP źródła zapytania DNS, aby określić region, z którego użytkownik jest badanie od — zwykle jest to adres IP lokalnego rozpoznawania nazw DNS, wykonywania zapytań w imieniu użytkownika.  

![Usługa Azure Traffic Manager "" routing ruchu metody geograficznego](./media/traffic-manager-routing-methods/geographic.png)

Usługa Traffic Manager odczytuje adres IP źródła zapytania DNS i decyduje, który region geograficzny, jest ona pochodzące z. Go następnie sprawdza, czy punkt końcowy, który ma ten region geograficzny do niej przyporządkowany. To wyszukiwanie rozpoczyna się od najniższego poziomu szczegółowości (stan/prowincję/Województwo gdzie jest obsługiwany, inne na poziomie Kraj/Region) i przechodzi do najwyższego poziomu, który **World**. Pierwsze dopasowanie znalezione za pomocą tego przechodzenie jest wyznaczony jako punkt końcowy do zwrócenia w odpowiedzi na zapytanie. Gdy zostanie zwrócona dopasowania z punktem końcowym typ zagnieżdżony punkt końcowy w ramach tego profilu podrzędnych, oparte na jego metody routingu. Poniższe punkty mają zastosowanie do tego zachowania:

- Region geograficzny można zamapować tylko jeden punkt końcowy w profilu usługi Traffic Manager w przypadku typu routingu geograficznego routingu. Dzięki temu routingu użytkowników jest deterministyczna, a klientów można włączyć scenariusze, które wymagają jednoznaczną granicach geograficznych.
- Jeśli w regionie użytkownika jest mapowanie geograficzne w dwóch różnych punktów końcowych, Traffic Manager wybierze punkt końcowy z najniższy poziom szczegółowości i nie należy wziąć pod uwagę kierowania żądań z tego regionu do innego punktu końcowego. Na przykład należy wziąć pod uwagę typ profilu geograficznego routingu z dwoma punktami końcowymi - Punk końcowy 1 i Endpoint2. Punk końcowy 1 jest skonfigurowany do odbierania ruchu z Irlandii, a Endpoint2 jest skonfigurowany do odbierania ruchu z Europy. Jeśli żądanie pochodzi z Irlandii, zawsze jest kierowany do Punk końcowy 1.
- Ponieważ regionu można zamapować tylko jeden punkt końcowy, usługa Traffic Manager zwraca niezależnie od tego, czy punkt końcowy jest w dobrej kondycji lub też nie.

    >[!IMPORTANT]
    >Zdecydowanie zaleca się, że klienci korzystający z metody routingu geograficznego skojarzyć go z punktów końcowych typu zagnieżdżone, do których ma profile podrzędnych zawierające co najmniej dwa punkty końcowe w ramach każdej.
- Jeśli zostanie znalezione dopasowanie punktu końcowego, a punkt końcowy w **zatrzymane** stanu usługi Traffic Manager zwraca odpowiedź NODATA. W tym przypadku nie dalsze wyszukiwań wykonano wyższego rzędu w hierarchii z regionów geograficznych. To zachowanie ma również zastosowanie dla typów zagnieżdżonych punktu końcowego, gdy profil podrzędnych znajduje się w **zatrzymane** lub **wyłączone** stanu.
- Jeśli punkt końcowy jest wyświetlany **wyłączone** stan, nie będą uwzględniane w regionie zgodny proces. To zachowanie ma również zastosowanie dla typów zagnieżdżonych punktu końcowego, gdy punkt końcowy jest **wyłączone** stanu.
- Jeśli zapytania pochodzą regionie geograficznym, który nie jest przyporządkowany w tym profilu, usługa Traffic Manager zwraca odpowiedź NODATA. Dlatego zalecane jest, aby klienci używali geograficznego routingu z jednym punktem końcowym, najlepiej typu zagnieżdżone z co najmniej dwa punkty końcowe w ramach profilu podrzędnych, z regionem **World** do niej przypisany. Dzięki temu, czy adresy IP, które nie są mapowane do regionu są obsługiwane.

Jak wyjaśniono w [jak działa usługa Traffic Manager](traffic-manager-how-it-works.md), usługa Traffic Manager nie otrzyma zapytania DNS bezpośrednio od klientów. Przeciwnie zapytania DNS pochodzą z rekursywnych usług DNS, klienci są skonfigurowane do używania. W związku z tym, adres IP używany do określenia region nie jest adres IP klienta, ale jest to adres IP z rekursywnych usług DNS. W praktyce ten adres IP jest dobrym serwera proxy dla klienta.


## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak tworzyć aplikacje o wysokiej dostępności przy użyciu [monitorowania punktu końcowego usługi Traffic Manager](traffic-manager-monitoring.md)

Dowiedz się, jak [Tworzenie profilu usługi Traffic Manager](traffic-manager-create-profile.md)

<!--Image references-->
[1]: ./media/traffic-manager-routing-methods/priority.png
[2]: ./media/traffic-manager-routing-methods/weighted.png
[3]: ./media/traffic-manager-routing-methods/performance.png



