---
title: Usługa Azure monitorowania punktu końcowego usługi Traffic Manager | Dokumentacja firmy Microsoft
description: W tym artykule mogą ułatwić zrozumienie, jak usługa Traffic Manager korzysta z monitorowania punktu końcowego i trybu failover punktu końcowego automatycznego klientom platformy Azure, wdrażać aplikacje o wysokiej dostępności
services: traffic-manager
author: KumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2018
ms.author: kumud
ms.openlocfilehash: 083bdf9c5aec640fbbd7757b307ac47178e0b14b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60329925"
---
# <a name="traffic-manager-endpoint-monitoring"></a>Monitorowanie punktu końcowego usługi Traffic Manager

Usługa Azure Traffic Manager zawiera wbudowany punkt końcowy monitorowania i punktu końcowego automatycznego trybu failover. Ta funkcja pomaga dostarczać aplikacje wysokiej dostępności, które są odporne na niepowodzenia punktu końcowego, łącznie z błędami w regionie platformy Azure.

## <a name="configure-endpoint-monitoring"></a>Konfigurowanie, monitorowanie punktu końcowego

Aby skonfigurować monitorowanie punktu końcowego, należy określić następujące ustawienia w profilu usługi Traffic Manager:

* **Protokół**. Wybierz pozycję HTTP, HTTPS lub TCP jako protokół, usługi Traffic Manager korzysta podczas badania punktu końcowego usługi do sprawdzenia jej kondycji. Monitorowanie protokołu HTTPS nie sprawdza, czy dotyczy certyfikatu protokołu SSL — go tylko sprawdza, czy certyfikat jest obecny.
* **Port**. Wybierz port używany dla żądania.
* **Ścieżka**. To ustawienie konfiguracji jest prawidłowy tylko w przypadku protokołów HTTP i HTTPS, do których określenia ścieżki ustawienie jest wymagane. Zapewnienie tego ustawienia dla TCP monitorowania protokołu będzie skutkowało błędem. Dla protokołu HTTP i HTTPS należy podać względną ścieżkę i nazwę strony sieci Web lub pliku, uzyskujący dostęp do monitorowania. Ukośnika (/) jest prawidłowym wpisem ścieżki względnej. Ta wartość oznacza, że plik znajduje się w katalogu głównym (ustawienie domyślne).
* **Ustawienia niestandardowego nagłówka** to ustawienie pomaga możesz dodać określone nagłówki HTTP do prawidłowej konfiguracji sprawdza, czy usługi Traffic Manager wysyła do punktów końcowych przy użyciu profilu. Nagłówki niestandardowe można określić na poziomie profilu, ma być stosowana dla wszystkich punktów końcowych w tym profilu and / or na poziomie punktu końcowego zastosowanie tylko do tego punktu końcowego. Można użyć niestandardowych nagłówków występowania kontrole kondycji do punktów końcowych w środowisku z wieloma dzierżawami kierowane poprawnie do miejsca docelowego, określając nagłówka hosta. Możesz również użyć tego ustawienia, dodając unikatowy nagłówki, które mogą służyć do identyfikowania usługi Traffic Manager pochodzi żądania HTTP (S) i przetwarza je w różny sposób. Można określić maksymalnie osiem seprated pary nagłówka: wartość oddzielonych przecinkami. Na przykład "header1:value1, header2:value2". 
* **Oczekiwany stan kodu zakresów** to ustawienie umożliwia określenie wielu zakresów kodu powodzenia w formacie 200 299, 301 301. Jeśli te kody stanu są otrzymywane jako odpowiedź z punktu końcowego, gdy kontrola kondycji jest inicjowane, usługa Traffic Manager oznacza tymi punktami końcowymi jako w dobrej kondycji. Można określić maksymalnie 8 zakresów kodu stanu. To ustawienie jest stosowane tylko do protokołu HTTP i HTTPS oraz do wszystkich punktów końcowych. To ustawienie znajduje się na poziomie profilu usługi Traffic Manager, i domyślnie wartość 200 jest zdefiniowany jako kod stanu powodzenia.
* **Interwał sondowania**. Ta wartość określa, jak często punkt końcowy jest sprawdzany pod kątem jego kondycję z agentów do sondowania usługi Traffic Manager. Można określić w tym miejscu dwie wartości: 30 sekund (zwykłego sondowania) i 10 sekund (sondowanie szybkie). Jeśli zostaną podane żadne wartości, profil, który ustawia wartości domyślnej równej 30 sekund. Odwiedź stronę [cennik usługi Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager) strony, aby dowiedzieć się więcej o cenach badania szybkie.
* **Tolerowana liczba niepowodzeń**. Ta wartość określa, jak wiele błędów agenta do sondowania usługi Traffic Manager zaakceptować przed oznaczeniem tego punktu końcowego o złej kondycji. Wartość może należeć do zakresu od 0 do 9. Wartość 0 oznacza pojedynczego uszkodzenia monitorowania może spowodować punkt końcowy był oznaczony jako w złej kondycji. Jeśli wartość nie zostanie określona, zostanie użyta wartość domyślna 3.
* **Limit czasu sondy**. Ta właściwość określa ilość czasu, który agent sondowania usługi Traffic Manager powinien odczekać przez biorąc pod uwagę, sprawdź błąd, gdy sonda sprawdzania kondycji są wysyłane do punktu końcowego. Interwał sondowania jest ustawiony na 30 sekund, można ustawić wartość limitu czasu od 5 do 10 sekund. Jeśli nie określono wartości, używa domyślnie wynosi 10 sekund. Interwał sondowania jest ustawiony na 10 sekund, można ustawić wartość limitu czasu od 5 do 9 sekund. Jeśli wartość limitu czasu nie jest określona, zostanie użyta wartość domyślną 9 sekund.

    ![Monitorowanie punktu końcowego usługi Traffic Manager](./media/traffic-manager-monitoring/endpoint-monitoring-settings.png)

    **Rysunek:  Monitorowanie punktu końcowego usługi Traffic Manager**

## <a name="how-endpoint-monitoring-works"></a>Działania monitorowania punktu końcowego

Jeśli protokół monitorowania jest ustawiony jako protokołu HTTP lub HTTPS, agent sondowania usługi Traffic Manager kieruje żądanie GET do punktu końcowego przy użyciu protokołu, portu i ścieżki względnej, biorąc pod uwagę. Jeśli ponownie uzyskuje odpowiedź 200 OK lub dowolne odpowiedzi skonfigurowanych w **oczekiwany kod stanu \*zakresy**, a następnie tego punktu końcowego jest uznawany za dobrej kondycji. Jeśli odpowiedź jest inna wartość, lub, jeżeli odpowiedź nie zostanie odebrana przed upływem limitu czasu określony, Traffic Manager sondowanie agenta ponownie próbuje zgodnie z ustawieniem tolerowana liczba niepowodzeń (ponowne próby są wykonywane tylko jeśli to ustawienie ma wartość 0). Jeśli liczbę kolejnych niepowodzeń jest wyższa niż wartość ustawienia tolerowana liczba niepowodzeń, punkt końcowy jest oznaczony jako w złej kondycji. 

Jeśli protokół monitorowania jest TCP, agent sondowania usługi Traffic Manager inicjuje żądanie połączenia protokołu TCP, przy użyciu określony port. Jeśli punkt końcowy odpowiada na żądanie przy użyciu odpowiedzi do nawiązania połączenia, sprawdzanie kondycji jest oznaczony jako sukcesu i agent sondowania usługi Traffic Manager resetuje połączenie TCP. Jeśli odpowiedź jest inna wartość, czy odpowiedź nie zostanie odebrana w określonym przedziale czasu określony, Traffic Manager sondowanie agenta ponownie próbuje zgodnie z ustawieniem tolerowana liczba niepowodzeń (ponowne próby są dokonywane informacji, jeśli to ustawienie ma wartość 0). Jeśli liczbę kolejnych niepowodzeń jest wyższa niż wartość ustawienia tolerowana liczba niepowodzeń, punkt końcowy jest oznaczona złej kondycji.

We wszystkich przypadkach sondy usługi Traffic Manager, z wielu lokalizacji i oznaczanie kolejne niepowodzenia się dzieje w każdym regionie. Oznacza to, że punkty końcowe odbierają sond kondycji usługi Traffic Manager z częstotliwością wyższa niż wartość ustawienia używane dla interwału sondowania.

>[!NOTE]
>Dla protokołu HTTP lub HTTPS Protokół monitorowania powszechną praktyką stronie punktu końcowego jest implementacja niestandardowa strona w aplikacji — na przykład /health.aspx. Użycie tej ścieżki w celu monitorowania, można wykonywać sprawdzanie specyficzne dla aplikacji, takich jak sprawdzanie liczników wydajności lub sprawdzania dostępności bazy danych. Oparte na tych niestandardowych kontroli, strony zwraca odpowiedni kod stanu HTTP.

Wszystkie punkty końcowe w profilu usługi Traffic Manager udostępniać ustawienia monitorowania. Jeśli musisz używać różnych ustawień monitorowania dla różnych punktów końcowych, możesz utworzyć [zagnieżdżone profile usługi Traffic Manager](traffic-manager-nested-profiles.md#example-5-per-endpoint-monitoring-settings).

## <a name="endpoint-and-profile-status"></a>Stan punktu końcowego i profilu

Można włączyć i wyłączyć profile usługi Traffic Manager i punktów końcowych. Jednak zmiany stanu punktu końcowego również może wystąpić jako wynik usługi Traffic Manager automatyczne ustawienia i procesy.

### <a name="endpoint-status"></a>Stan punktu końcowego

Można włączyć lub wyłączyć określonego punktu końcowego. Usługi podstawowej, która może być w dobrej kondycji, nie wpływa na. Zmiana stanu punktu końcowego steruje dostępność punktu końcowego w profilu usługi Traffic Manager. Po wyłączeniu stanu punktu końcowego usługi Traffic Manager sprawdza jego kondycji, a punkt końcowy jest niedostępna w odpowiedzi DNS.

### <a name="profile-status"></a>Stan profilu

Korzystając z ustawienia stanu profilu, można włączyć lub wyłączyć określonego profilu. Gdy stan punktu końcowego ma wpływ na jednym punkcie końcowym, stan profilu ma wpływ na cały profilu, w tym wszystkie punkty końcowe. Po wyłączeniu profilu punktów końcowych, które nie są sprawdzane pod kątem kondycji i punkty końcowe nie są uwzględnione w odpowiedzi DNS. [NXDOMAIN](https://tools.ietf.org/html/rfc2308) kod odpowiedzi jest zwracany dla zapytań DNS.

### <a name="endpoint-monitor-status"></a>Stan monitora punktu końcowego

Stan monitora punktu końcowego jest wyświetlany jest stan punktu końcowego wartość generowanych przez usługi Traffic Manager. Nie można zmienić to ustawienie ręcznie. Stan monitora punktu końcowego jest kombinacją wyniki monitorowania punktu końcowego i stan skonfigurowanego punktu końcowego. Możliwe wartości stanu monitora punktu końcowego są wyświetlane w poniższej tabeli:

| Stan profilu | Stan punktu końcowego | Stan monitora punktu końcowego | Uwagi |
| --- | --- | --- | --- |
| Disabled (Wyłączony) |Enabled (Włączony) |Nieaktywna |Profil został wyłączony. Mimo, że stan punktu końcowego jest włączone, pierwszeństwo ma stan profilu (wyłączony). Punkty końcowe w profilach wyłączony nie są monitorowane. Zapytania DNS, zwracany jest kod odpowiedź NXDOMAIN. |
| &lt;Wszystkie&gt; |Disabled (Wyłączony) |Disabled (Wyłączony) |Punkt końcowy został wyłączony. Wyłączone punkty końcowe nie są monitorowane. Punkt końcowy nie jest uwzględniony w odpowiedzi DNS, dlatego nie otrzymuje ruchu. |
| Enabled (Włączony) |Enabled (Włączony) |Online |Punkt końcowy jest monitorowana i jest w dobrej kondycji. On jest dołączony do odpowiedzi DNS i mogą odbierać dane. |
| Enabled (Włączony) |Enabled (Włączony) |Obniżono poziom |Kontrole kondycji z monitorowania punktu końcowego kończą się niepowodzeniem. Punkt końcowy nie jest uwzględniony w odpowiedzi DNS i odbierają ruchu. <br>Wyjątkiem jest, jeśli wszystkie punkty końcowe są ograniczone, w którym to przypadku wszystkie z nich są traktowane jako ma zostać zwrócone w odpowiedzi na zapytanie).</br>|
| Enabled (Włączony) |Enabled (Włączony) |CheckingEndpoint |Punkt końcowy jest monitorowana, ale wyniki pierwszą sondę nie zostały jeszcze odebrane. CheckingEndpoint jest to stan tymczasowy, który zwykle występuje natychmiast po dodaniu lub włączanie punktu końcowego w profilu. Punkt końcowy, w tym stanie znajduje się w odpowiedzi DNS i mogą odbierać dane. |
| Enabled (Włączony) |Enabled (Włączony) |Zatrzymano |Chmury usługi lub aplikacji sieci web, która wskazuje punkt końcowy nie jest uruchomiona. Sprawdź ustawienia aplikacji usługi lub sieci web chmury. Przyczyną może również być punkt końcowy jest zagnieżdżony typ punktu końcowego i profilu podrzędnej jest wyłączona lub jest nieaktywna. <br>Punkt końcowy ze stanem zatrzymania nie będzie monitorowana. Go nie jest uwzględniony w odpowiedzi DNS i odbierają ruchu. Wyjątkiem jest, jeśli wszystkie punkty końcowe są negatywny wpływ na dostępność, w którym to przypadku wszystkie z nich będzie uznawany za zwrócone w odpowiedzi na zapytanie.</br>|

Szczegółowe informacje na temat obliczania stan monitora punktu końcowego dla zagnieżdżonych punktów końcowych, zobacz [zagnieżdżone profile usługi Traffic Manager](traffic-manager-nested-profiles.md).

>[!NOTE]
> Stan monitorowanie zatrzymane punktu końcowego może nastąpić w usłudze App Service, jeśli aplikacja sieci web nie działa w ramach warstwy Standard lub wyższym. Aby uzyskać więcej informacji, zobacz [integracji usługi Traffic Manager z usługą App Service](/azure/app-service/web-sites-traffic-manager).

### <a name="profile-monitor-status"></a>Stan monitora profilu

Stan monitora profilu jest kombinacją stanu skonfigurowanego profilu i wartości stanu monitora punktu końcowego dla wszystkich punktów końcowych. W poniższej tabeli opisano możliwe wartości:

| Stan profilu (zgodnie z konfiguracją) | Stan monitora punktu końcowego | Stan monitora profilu | Uwagi |
| --- | --- | --- | --- |
| Disabled (Wyłączony) |&lt;wszelkie&gt; lub profil przy użyciu zdefiniowanych punktów końcowych. |Disabled (Wyłączony) |Profil został wyłączony. |
| Enabled (Włączony) |Stan co najmniej jeden punkt końcowy ma obniżoną wydajność. |Obniżono poziom |Przejrzyj wartości stanu poszczególnych punktu końcowego, aby określić, które punkty końcowe wymagać dalszych działań. |
| Enabled (Włączony) |Stan co najmniej jeden punkt końcowy jest w trybie Online. Brak punktów końcowych musi mieć stan obniżony. |Online |Usługa akceptuje ruchu. Nie są wymagane żadne dalsze działania. |
| Enabled (Włączony) |Stan co najmniej jeden punkt końcowy jest CheckingEndpoint. Punkty końcowe nie są w stanie Online lub obniżony. |CheckingEndpoints |Ten stan przejścia występuje, gdy profil, jeśli utworzone lub włączone. Trwa sprawdzanie kondycji punktu końcowego, po raz pierwszy. |
| Enabled (Włączony) |Stan wszystkich punktów końcowych w profilu są wyłączone lub zatrzymana lub profil, który nie ma zdefiniowanych punktów końcowych. |Nieaktywna |Punkty końcowe nie są aktywne, ale profil, który jest nadal włączony. |

## <a name="endpoint-failover-and-recovery"></a>Tryb failover punktu końcowego i odzyskiwanie

Usługa Traffic Manager okresowo sprawdza kondycję każdego punktu końcowego, w tym punkty końcowe w złej kondycji. Traffic Manager wykryje, gdy punkt końcowy staje się dobrej kondycji, a jego powrót do rotacji.

Punkt końcowy jest w złej kondycji, gdy zachodzi jedno z następujących zdarzeń:
- W przypadku monitorowania protokołu HTTP lub HTTPS:
    - Odpowiedź – 200 lub odpowiedzi, który nie obejmuje zakres stan określony w **oczekiwany stan kodu zakresów** ustawienie zostanie odebrana (w tym kod różnych 2xx lub przekierowania 301/302).
- Jeśli protokół monitorowania jest TCP: 
    - Odebrano odpowiedź niż potwierdzenia lub SYN potwierdzenia w odpowiedzi na żądanie SYNCHRONIZACJI wysyłane przez usługę Traffic Manager próby ustanowienia połączenia.
- Przekroczono limit czasu. 
- Wszystkie inne problem z połączeniem skutkuje punkt końcowy nie jest dostępny.

Aby uzyskać więcej informacji na temat rozwiązywania problemów sprawdzenia zakończone niepowodzeniem, zobacz [stan rozwiązywania problemów negatywny wpływ na dostępność w usłudze Azure Traffic Manager](traffic-manager-troubleshooting-degraded.md). 

Oś czasu na poniższej ilustracji jest szczegółowy opis procesu monitorowania punktu końcowego usługi Traffic Manager, który ma następujące ustawienia: Protokół monitorowania jest protokół HTTP, interwał sondowania wynosi 30 sekund, liczba błędów tolerowana wynosi 3, wartość limitu czasu wynosi 10 sekund i czasu wygaśnięcia DNS to 30 sekund.

![Punkt końcowy usługi Traffic Manager sekwencji trybu failover i powrotu po awarii](./media/traffic-manager-monitoring/timeline.png)

**Rysunek:  Traffic manager punktu końcowego trybu failover i odzyskiwania sekwencji**

1. **GET**. Dla każdego punktu końcowego usługi Traffic Manager, system monitorowania wykonuje żądanie GET na ścieżce określonej w ustawieniach monitorowania.
2. **Zakresu 200 OK lub niestandardowy kod określony profil usługi Traffic Manager ustawienia monitorowania** . System monitorowania oczekuje HTTP 200 OK lub lub kod niestandardowy zakres określony profil usługi Traffic Manager monitoring komunikat Ustawienia, które mają zostać zwrócone w ciągu 10 sekund. Po odebraniu tej odpowiedzi, rozpoznaje, że usługa jest dostępna.
3. **30 sekund między każdym sprawdzeniem**. Sprawdzenie kondycji punktu końcowego jest powtarzany co 30 sekund.
4. **Usługa niedostępna**. Usługa stanie się niedostępna. Usługa Traffic Manager nie będzie wiadomo, aż do następnego sprawdzania kondycji.
5. **Próbuje uzyskać dostęp do ścieżki monitorowania**. System monitorowania wykonuje żądanie GET, ale nie odebrano odpowiedzi na 10 sekund przed upływem limitu czasu (ewentualnie odpowiedzi – 200 mogą pojawić się). Próbuje trzy razy w odstępach 30 sekund. Jeśli jeden maksymalna liczba prób to się powiedzie, liczbę prób jest resetowany.
6. **Stan ustawiony na obniżony**. Po awarii kolejnych czwarty system monitorowania oznacza stan punktu końcowego niedostępny jako obniżony.
7. **Ruch jest kierowane do innych punktów końcowych**. Serwery nazw DNS usługi Traffic Manager są aktualizowane i usługi Traffic Manager zwraca punkt końcowy nie jest już w odpowiedzi na zapytania DNS. Nowe połączenia są kierowane do innych, dostępnych punktów końcowych. Jednak poprzedniej odpowiedzi DNS, które zawierają ten punkt końcowy, może nadal buforowanych przez cykliczne serwery DNS i klientów DNS. Klienci w dalszym ciągu używać punktu końcowego do momentu wygaśnięcia pamięci podręcznej DNS. Jak wygaśnięcia pamięci podręcznej DNS, klienci nowego zapytania DNS są wysyłane i są kierowane do różnych punktów końcowych. Czas trwania pamięci podręcznej jest kontrolowana przez ustawienie czasu wygaśnięcia w profilu usługi Traffic Manager, na przykład 30 sekund.
8. **Kontrole kondycji nadal**. Usługa Traffic Manager w dalszym ciągu sprawdzanie kondycji punktu końcowego w przypadku, gdy ma ono stan obniżony. Traffic Manager wykryje, gdy punkt końcowy powróci do kondycji.
9. **Usługa powróci do trybu online**. Usługa stanie się dostępny. Punkt końcowy zachowuje stan obniżony w usłudze Traffic Manager, dopóki system monitorujący przeprowadza jego następnego sprawdzania kondycji.
10. **Ruch do usługi wznawia**. Traffic Manager wysyła żądanie pobrania i odbiera odpowiedź stanu 200 OK. Usługa zwróciła do stanu dobrej kondycji. Serwery nazw usługi Traffic Manager są aktualizowane i zaczynają przekazywaniu nazwy DNS usługi w odpowiedzi DNS. Ruch zwraca do punktu końcowego buforowane odpowiedzi DNS zwracające inne punkty końcowe wygasa, a jako istniejących połączeń z innych punktów końcowych są kończone.

    > [!NOTE]
    > Ponieważ usługa Traffic Manager działa na poziomie usługi DNS, nie może mieć wpływ istniejących połączeń z dowolnego punktu końcowego. Po jego kieruje ruch między punktami końcowymi (albo za pomocą profilu zmienionych ustawień lub w tryb failover i powrotu po awarii), usługi Traffic Manager określa, że nowe połączenia z platformą dostępnych punktów końcowych. Jednak inne punkty końcowe mogą w dalszym ciągu otrzymywać ruchu za pośrednictwem istniejących połączeń, do czasu jej zakończenia są tymi sesjami. Aby umożliwić ruch opróżnić z istniejącymi połączeniami, aplikacje, należy ograniczyć czas trwania sesji używane z każdego punktu końcowego.

## <a name="traffic-routing-methods"></a>Metody routingu ruchu

Gdy punkt końcowy stan obniżony, już nie jest zwracana w odpowiedzi na zapytania DNS. Zamiast tego alternatywny punkt końcowy jest wybierany i zwrócony. Metody routingu ruchu, skonfigurowanym w profilu określa, jak można wybrać alternatywny punkt końcowy.

* **Priorytet**. Punkty końcowe formularza listy priorytetów. Zawsze zwracany jest pierwszy dostępny punkt końcowy na liście. Jeśli stan punktu końcowego jest obniżona, zwracany jest następnego dostępnego punktu końcowego.
* **Ważona średnia**. Dowolnego dostępnego punktu końcowego jest wybierany losowo na podstawie ich wagi przypisanej i wagi innych dostępnych punktów końcowych.
* **Wydajność**. Punkt końcowy najbliżej użytkownika końcowego jest zwracana. Punkt końcowy jest niedostępny, usługa Traffic Manager przenosi ruchu do punktów końcowych w dalej najbliższego regionu platformy Azure. Plany awaryjne alternatywnych kierowania ruchu wydajności można skonfigurować za pomocą [zagnieżdżone profile usługi Traffic Manager](traffic-manager-nested-profiles.md#example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region).
* **Geograficzne**. Punkt końcowy zamapowany do obsługi lokalizacja geograficzna na podstawie żądania zapytania adresy IP, jest zwracana. Jeśli punkt końcowy jest niedostępny, innego punktu końcowego nie zostanie wybrana przejścia w tryb failover, ponieważ lokalizacji geograficznej można zamapować tylko jeden punkt końcowy w profilu (szczegółowe informacje znajdują się w [— często zadawane pytania](traffic-manager-FAQs.md#traffic-manager-geographic-traffic-routing-method)). Najlepszym rozwiązaniem, korzystając z geograficznego routingu zaleca się klienci mogą korzystać z więcej niż jednym punktem końcowym zagnieżdżone profile usługi Traffic Manager, jako punkty końcowe profilu.
* **Atrybut wielowartościowy elementu** wiele punktów końcowych mapowane na adresy IPv4 i IPv6 są zwracane. Po otrzymaniu kwerendy dla tego profilu dobrej kondycji punktów końcowych są zwracane na podstawie **maksymalną liczbę rekordów w odpowiedzi** wartości, które zostały określone. Domyślna liczba odpowiedzi jest dwa punkty końcowe.
* **Podsieci** zwracany jest mapowane na zestaw zakresów adresów IP punktu końcowego. Po odebraniu żądania z tego adresu IP punktu końcowego zwracany jest ten mapowany dla tego adresu IP. 

Aby uzyskać więcej informacji, zobacz [metody routingu ruchu w usłudze Traffic Manager](traffic-manager-routing-methods.md).

> [!NOTE]
> Jedynym wyjątkiem normalnego zachowania routingu ruchu występuje, gdy wszystkie kwalifikujące się punkty końcowe mają stan obniżonej wydajności. Próba "starań" sprawia, że usługa Traffic Manager i *reaguje tak, jakby wszystkie obniżony stan punktów końcowych, które faktycznie są w stanie online*. To zachowanie jest innym rozwiązaniem byłoby zwraca dowolnego punktu końcowego w odpowiedzi DNS. Wyłączony lub zatrzymany punktów końcowych nie są monitorowane, w związku z tym, nie uważa się kwalifikuje się do ruchu.
>
> Ten stan jest często spowodowane niewłaściwa konfiguracja usługi, takie jak:
>
> * Listy kontroli dostępu [ACL] Blokowanie sprawdzania kondycji usługi Traffic Manager.
> * Niepoprawna konfiguracja monitorowania portu lub protokół w profilu usługi Traffic manager.
>
> Konsekwencją to zachowanie jest, że w przypadku sprawdzenia kondycji usługi Traffic Manager nie są poprawnie skonfigurowane, może się pojawić od ruchu tak, jakby routingu usługi Traffic Manager *jest* działa prawidłowo. Jednak w takim przypadku trybu failover punktu końcowego nie może się zdarzyć, co ma wpływ na dostępność ogólną aplikacji. Należy sprawdzić, czy profil, który przedstawia stan Online, nie stan obniżony. Stan Online wskazuje, czy kontrole kondycji usługi Traffic Manager działają zgodnie z oczekiwaniami.

Aby uzyskać więcej informacji dotyczących rozwiązywania problemów nie powiodło się kontrole kondycji, zobacz [stan rozwiązywania problemów negatywny wpływ na dostępność w usłudze Azure Traffic Manager](traffic-manager-troubleshooting-degraded.md).



## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, [jak działa usługa Traffic Manager](traffic-manager-how-it-works.md)

Dowiedz się więcej o [metody routingu ruchu](traffic-manager-routing-methods.md) obsługiwane przez usługę Traffic Manager

Dowiedz się, jak [Tworzenie profilu usługi Traffic Manager](traffic-manager-manage-profiles.md)

[Rozwiązywanie problemów z stan obniżony](traffic-manager-troubleshooting-degraded.md) na punkt końcowy usługi Traffic Manager
