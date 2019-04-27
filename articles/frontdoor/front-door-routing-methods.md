---
title: Usługa Azure drzwiami frontowymi — metody routingu ruchu | Dokumentacja firmy Microsoft
description: Ten artykuł pomoże Ci zrozumieć metody routingu ruchu innego, używany przy wejściu
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: bd1278db43ba31ed78f13a826a330e16c3bc8d57
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736228"
---
# <a name="front-door-routing-methods"></a>Metody routingu drzwi

Usługa Azure drzwiami frontowymi obsługuje różne metody routingu ruchu, aby określić, jak kierować ruch HTTP/HTTPS do różnych punktów końcowych usługi. Dla każdego żądania klienta osiągnięcia drzwiami frontowymi skonfigurowana metoda routingu zostanie zastosowany na upewnij się, że żądania są przekazywane do najlepszych wystąpienia zaplecza. 

Dostępne są cztery główne pojęcia do kierowania ruchu w drzwi wejściowe:

* **[Opóźnienie](#latency):** Routing oparty na opóźnienie gwarantuje, że żądania są wysyłane do najniższego zaplecza opóźnienie dopuszczalne w zakresie poufności. Po prostu żądań użytkownika są wysyłane do zestawu "najbliższy" zapleczy w odniesieniu do opóźnienia sieci.
* **[Priorytet](#priority):** Jeśli chcesz użyć podstawowa usługa zaplecza dla całego ruchu, a wykonywanie kopii zapasowych w przypadku, gdy główna osoba kontaktowa lub kopii zapasowej zaplecza są niedostępne, można przypisać priorytety do różnych zaplecza.
* **[Ważona średnia](#weighted):** Jeśli chcesz dystrybuować ruch w zestawie zaplecza, równomiernie lub według wagi współczynniki można przypisać wagi do różnych zaplecza.
* **Koligacja sesji:** Możesz skonfigurować sesję, którą koligacji dla hostów serwera sieci Web lub domen, jeśli chcesz, że kolejne żądania od użytkownika, są wysyłane do tego samego zaplecza, tak długo, jak sesja użytkownika jest nadal aktywne i wystąpienia wewnętrznej bazy danych nadal zgłasza dobrej kondycji oparte na sond kondycji. 

Wszystkie konfiguracje usługi Front Door obejmują monitorowanie kondycji zaplecza i zautomatyzowane, natychmiastowe przechodzenie do trybu failover w skali globalnej. Aby uzyskać więcej informacji, zobacz [drzwiami frontowymi wewnętrznej bazy danych monitorowania](front-door-health-probes.md). Twoje drzwiami frontowymi można skonfigurować do pracy, albo na podstawie jednej metody routingu i w zależności od aplikacji wymaga służy wielu lub wszystkich tych metod routingu w połączeniu Tworzenie optymalnych topologię routingu.

## <a name = "latency"></a>Najniższe opóźnienia na podstawie routing ruchu

Wdrażanie zapleczy w co najmniej dwóch lokalizacjach na całym świecie może zwiększyć szybkość reakcji z wielu aplikacji, kierując ruch do lokalizacji, która jest najbliżej, użytkownikom końcowym. Domyślną metodę routingu ruchu dla danej konfiguracji drzwiami frontowymi przekazuje żądania od użytkowników końcowych do najbliższego serwera zaplecza ze środowiska drzwiami frontowymi, który odebrał żądanie. W połączeniu z architekturą emisji dowolnej usługi Azure Service drzwiami frontowymi takie podejście zapewnia, że każdy z użytkowników końcowych get maksymalną wydajność spersonalizowane na podstawie ich lokalizacji.

"Najbliższy" wewnętrznej bazy danych nie jest koniecznie najbliższego mierzony geograficznej odległości. Zamiast tego drzwiami frontowymi określa najbliższego zaplecza aplikacji przez dokonywanie pomiarów opóźnienia sieci. Przeczytaj więcej na temat [architekturę routingu drzwiami frontowymi](front-door-routing-architecture.md). 

Poniżej przedstawiono ogólny przepływ decyzyjny:

| Dostępne zaplecza | Priorytet | Czas oczekiwania na sygnał (oparte na sondę kondycji) | Wagi |
|-------------| ----------- | ----------- | ----------- |
| Po pierwsze, zaznacz wszystkie zapleczy, które są włączone i zwracane w dobrej kondycji (200 OK) dla sondy kondycji. Załóżmy, że, istnieje sześć zaplecza, A, B, C, D, E i f., a wśród nich C jest w złej kondycji i E jest wyłączona. Dlatego listę dostępnych zapleczy to A, B, D i F.  | Następnie zaplecza o najwyższym priorytecie wśród nich dostępne są zaznaczone. Załóżmy, że wewnętrznej bazy danych A, B i D mają priorytet 1 i zaplecza F o priorytecie 2. Aby wybrane zapleczy będzie A, B i D.| Wybierz zaplecza z zakresem opóźnienia (co najmniej opóźnienia & czułości opóźnienia w ms określone). Powiedz, jeśli jest 15 ms, B jest 30 ms i D jest 60 ms od środowiska drzwiami frontowymi, gdzie żądania Rozważaliśmy i czułość opóźnienia jest 30 ms, a następnie najniższe opóźnienia puli składa się z wewnętrznej bazy danych, A i B, ponieważ D jest wyższy niż 30 ms od najbliższej wewnętrznej bazy danych, który jest A. | Na koniec drzwiami frontowymi będzie działanie okrężne ruch między końcowego wybranej puli zaplecza w stosunku wagi określony. Powiedz, jeśli ma wewnętrznej bazy danych A wadze 5 i wewnętrznej bazy danych B ma wagę 8, a następnie ruch zostanie przekazany w stosunku 5:8 między zapleczem A i B. |

>[!NOTE]
> Domyślnie właściwość czułości opóźnienia jest ustawiona na 0 ms, oznacza to, zawsze przesyła żądanie do najszybszych dostępnych wewnętrznej bazy danych.


## <a name = "priority"></a>Oparte na priorytetach routingu ruchu

Często organizacja chce zapewniają niezawodność swoich usług, wdrażając co najmniej jedna usługa tworzenia kopii zapasowych w przypadku, gdy ich podstawowa usługa ulegnie awarii. Ta topologia w branży, jest również określany jako aktywnego/w gotowości, lub aktywny/pasywny topologia wdrażania. Metody routingu ruchu "Priority" umożliwia klientom platformy Azure można łatwo zaimplementować ten wzorzec pracy awaryjnej.

Domyślne drzwiami frontowymi zawiera listę zaplecza w taki sam priorytet. Domyślnie drzwiami frontowymi wysyła ruch tylko do zaplecza o najwyższym priorytecie (najmniejsza wartość priorytetu) oznacza to, że podstawowy zestaw zaplecza. Jeśli podstawowy zaplecza nie są dostępne, drzwiami frontowymi kieruje ruchem do pomocniczego zestawu zaplecza (drugą najniższą wartość priorytetu). Jeśli podstawowe i pomocnicze zaplecza nie są dostępne, ruch jest przesyłany do innego i tak dalej. Dostępność usługi wewnętrznej bazy danych zależy od skonfigurowanego stanu (włączone lub wyłączone), a następnie sondy stanu kondycji trwającą wewnętrznej bazy danych, zgodnie z ustaleniami kondycji.

### <a name="configuring-priority-for-backends"></a>Konfigurowanie priorytetu dla zaplecza

Każda z zaplecza w puli zaplecza w ramach konfiguracji drzwiami frontowymi ma właściwość o nazwie "Priority", który może być liczbą z przedziału od 1 do 5. Usługa Azure Service drzwiami frontowymi możesz skonfigurować priorytet zaplecza jawnie przy użyciu tej właściwości dla poszczególnych pól zaplecza. Ta właściwość jest wartością z zakresu od 1 do 5. Niższe wartości reprezentują wyższy priorytet. Zaplecza mogą udostępniać wartości priorytetu.

## <a name = "weighted"></a>Metody routingu ruchu ważonego
"Ważona" metody routingu ruchu pozwala równomierne rozłożenie ruchu sieciowego lub użyć wstępnie zdefiniowanych wagi.

W ważona metody routingu ruchu należy przypisać wagi do poszczególnych pól zaplecza w konfiguracji drzwiami frontowymi puli zaplecza. Waga jest liczbą całkowitą z zakresu od 1 do 1000. Ten parametr używa domyślna grubość "50".

Wśród listę dostępnych zaplecza w obrębie czułości akceptowane opóźnienia (jak określono) ruch pobiera dystrybuowane w mechanizm działanie okrężne współczynnika wagi określony. Czułość czas oczekiwania jest równa 0 milisekund, następnie właściwość ta nie zostaną uwzględnione, chyba że istnieją dwa zaplecza przy użyciu tego samego opóźnienia sieci. 

Metoda ważona zapewnia pewne przydatne w scenariuszach:

* **Uaktualnienie stopniowe aplikacji**: Przydzielanie procent ruchu sieciowego, aby kierować do nowego zaplecza i stopniowo zwiększać ruchu, wraz z upływem czasu, co par innych zapleczy przełoży się.
* **Migracja aplikacji na platformie Azure**: Utwórz pulę zaplecza przy użyciu zaplecza aplikacji zarówno dla platformy Azure, jak i zewnętrznych. Dostosuj wagę zaplecza preferowanie nowego zaplecza. Stopniowo skonfigurowaniem to począwszy od konieczności nowych zapleczy, które są wyłączone, następnie przypisując im wag najniższy powoli zwiększenie poziomów, w którym przyjmują większość ruchu. Następnie na końcu wyłączenie mniej preferowanych zaplecza i usunięcie ich z puli.  
* **Rozszerzanie możliwości chmury za dodatkową pojemność**: Szybko rozbudować wdrożenia lokalnego do chmury, ustawiając dla niego za wejściu. Jeśli potrzebujesz dodatkowej pojemności w chmurze, należy dodać lub włączyć więcej zaplecza i określić, jaka część ruchu jest przesyłany do poszczególnych pól zaplecza.

## <a name = "affinity"></a>Koligacja sesji
Domyślnie bez koligacji sesji drzwiami frontowymi przekazuje żądania pochodzące z tego samego klienta do różnych zaplecza na podstawie obciążenia równoważenia szczególnie, jak zmienić opóźnienia do różnych zaplecza, lub jeśli różne żądania z tego samego użytkownika gruntów w innym środowisku wejściu. Jednak w przypadku niektórych aplikacji stanowych lub pewnych innych scenariuszy preferowane jest kierowanie kolejnych żądań pochodzących od danego użytkownika do tego samego zaplecza, które przetworzyło początkowe żądanie. Funkcja koligacji sesji na podstawie plików cookie jest przydatna, gdy chcesz utrzymać sesję użytkownika w ramach jednego zaplecza. Za pomocą plików cookie drzwiami frontowymi zarządzane, Azure drzwiami frontowymi Service można kierować dalszy ruch z sesji użytkownika do tej samej wewnętrznej bazy danych do przetwarzania, tak długo, jak wewnętrznej bazy danych jest w dobrej kondycji i sesji użytkownika nie wygasło. 

Koligację sesji można włączyć na poziomie hosta frontonu dla każdej skonfigurowanej domeny (lub poddomeny). Po jej włączeniu usługa Front Door dodaje plik cookie do sesji użytkownika. Funkcja koligacji sesji na podstawie plików cookie umożliwia usłudze Front Door identyfikowanie różnych użytkowników, nawet korzystających z tego samego adresu IP, co z kolei umożliwia bardziej równomierne rozłożenia ruchu na różne zaplecza.

Czas życia pliku cookie jest taki sam jak sesji użytkownika, ponieważ usługa Front Door aktualnie obsługuje tylko pliki cookie sesji. 

> [!NOTE]
> Publiczne serwery proxy może kolidować z koligacją sesji. Jest to spowodowane ustanowienia sesji wymaga drzwiami frontowymi dodać plik cookie koligacji sesji na potrzeby odpowiedzi, której nie można przeprowadzić, jeśli odpowiedź jest podlega buforowaniu, ponieważ mogłoby zakłócać, pliki cookie innych klientów żądających tego samego zasobu. Aby zabezpieczyć się przed tym, koligacja sesji będzie **nie** ustanawiane, jeśli wewnętrznej bazy danych to jest podejmowana próba, wysyła odpowiedź podlega buforowaniu. Jeśli sesja została ustanowiona, nie ma znaczenia, jeśli odpowiedź z wewnętrznej bazy danych jest podlega buforowaniu, na.
> Koligacja sesji zostanie nawiązane w następujących okolicznościach **chyba że** odpowiedź ma kod stanu HTTP 304:
> - Odpowiedź ma określone wartości ustawione dla ```Cache-Control``` nagłówka, które uniemożliwiają buforowania, np. "prywatny" lub nie-store ".
> - Odpowiedź zawiera ```Authorization``` nagłówek, który nie wygasł.
> - Odpowiedź ma kod stanu HTTP 302.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).
