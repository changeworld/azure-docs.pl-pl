---
title: Azure monitorowania punktów końcowych usługi Traffic Manager | Dokumentacja firmy Microsoft
description: W tym artykule może pomóc Ci zrozumieć, jak Menedżera ruchu korzysta z monitorowania punktów końcowych i trybu failover punktu końcowego automatycznego ułatwiających klientom Azure wdrażać aplikacje wysokiej dostępności
services: traffic-manager
documentationcenter: ''
author: kumudd
manager: timlt
editor: ''
ms.assetid: fff25ac3-d13a-4af9-8916-7c72e3d64bc7
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/22/2017
ms.author: kumud
ms.openlocfilehash: 6d07bd333e4d1663e37a840975cde2d9c73cec9c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="traffic-manager-endpoint-monitoring"></a>Monitorowanie punktu końcowego Menedżera ruchu

Usługi Azure Traffic Manager obejmuje monitorowanie wbudowanym punktem końcowym i punktu końcowego automatycznej pracy awaryjnej. Ta funkcja pomaga dostarczania aplikacji wysokiej dostępności, które są odporne na błędy punktu końcowego, w tym awarii region platformy Azure.

## <a name="configure-endpoint-monitoring"></a>Konfigurowanie monitorowania punktów końcowych

Aby Skonfiguruj punkt końcowy monitorowania, należy określić następujące ustawienia w profilu usługi Traffic Manager:

* **Protokół**. Wybierz pozycję HTTP, HTTPS lub TCP jako protokół usługi Traffic Manager używany podczas badania punktu końcowego w celu sprawdzenia jej kondycji. Monitorowanie HTTPS nie sprawdza, czy obowiązuje certyfikat SSL — go tylko sprawdza, czy certyfikat jest obecny.
* **Port**. Wybierz port używany dla żądania.
* **Ścieżka**. To ustawienie konfiguracji jest prawidłowy tylko w przypadku protokołów HTTP i HTTPS, w których Ścieżka określająca ustawienie jest wymagane. Zapewnia to ustawienie dla monitorowania powoduje błąd protokołu TCP. Dla protokołu HTTP i HTTPS należy podać względną ścieżkę i nazwę strony sieci Web lub plik, którego monitorowanie ma dostęp. Ukośnika (/) jest prawidłowym wpisem dla ścieżki względnej. Ta wartość oznacza, że plik znajduje się w katalogu głównym (ustawienie domyślne).
* **Interwał sondowania**. Ta wartość określa, jak często punkt końcowy jest sprawdzany pod kątem kondycji od agenta sondowania Menedżera ruchu. Można określić w tym miejscu dwóch wartości: 30 sekund (zwykłego sondowania) i 10 sekund (fast sondowanie). Jeśli wartości nie są dostarczane, profil ustawia domyślną wartość 30 sekund. Odwiedź stronę [cennik usługi Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager) strony, aby dowiedzieć się więcej o cenach sondowania szybkie.
* **Liczba błędów dopuszczalne**. Ta wartość określa, ile błędów sondowania agentem menedżera ruchu zaakceptować przed oznaczeniem określonego punktu końcowego swój stan jako niezdrowy. Wartość może należeć do zakresu od 0 do 9. Wartość 0 oznacza pojedynczego uszkodzenia monitorowania może spowodować tego punktu końcowego może być oznaczony jako w złej kondycji. Jeśli nie określono wartości, używa domyślna wartość 3.
* **Limit czasu monitorowania**. Ta właściwość określa ilość czasu, który agenta menedżera ruchu sondowania powinien zaczekać na uwzględnieniu który Sprawdź awarii, gdy sonda sprawdzania kondycji są wysyłane do punktu końcowego. Interwał sondowania wynosi 30 sekund, można ustawić wartość limitu czasu od 5 do 10 sekund. Jeśli nie określono wartości, używa wartość domyślną równą 10 sekund. Interwał sondowania wynosi 10 sekund, można ustawić wartość limitu czasu od 5 do 9 sekund. Jeśli wartość limitu czasu nie zostanie określona, używa wartość domyślną 9 sekund.

![Monitorowanie punktu końcowego Menedżera ruchu](./media/traffic-manager-monitoring/endpoint-monitoring-settings.png)

**Rysunek 1: Monitorowanie punktu końcowego Menedżera ruchu**

## <a name="how-endpoint-monitoring-works"></a>Jak działa monitorowania punktów końcowych

Jeśli protokół monitorowania jest ustawiony jako protokołu HTTP lub HTTPS, agent sondowania Menedżera ruchu zgłasza żądanie GET przy użyciu protokołu, portu i ścieżki względnej danego punktu końcowego. Jeśli ponownie pobiera odpowiedź 200 OK, a następnie tego punktu końcowego jest uznawany za dobrej kondycji. Jeśli odpowiedź ma inną wartość lub, jeśli odpowiedź nie zostanie odebrana przed upływem limitu czasu określona, przyjmowana Traffic Manager sondowanie agenta ponownie próbuje zgodnie z ustawieniem dopuszczalne liczbę błędów (ponowne próby są wykonywane informacji, jeśli to ustawienie ma wartość 0). Jeśli liczbę kolejnych niepowodzeń jest wyższa niż wartość ustawienia dopuszczalne liczbę błędów, tego punktu końcowego jest oznaczona jako w złej kondycji. 

Jeśli monitorowania protokołu TCP, agent sondowania Menedżera ruchu inicjuje żądanie połączenia TCP za pomocą określony port. Jeśli punkt końcowy odpowiada na żądanie za pomocą odpowiedzi do nawiązania połączenia, sprawdzanie kondycji jest oznaczony jako sukcesu i agenta sondowania Traffic Manager resetuje połączenie TCP. Jeśli odpowiedź ma inną wartość lub odpowiedź nie zostanie odebrana przed upływem limitu czasu określonego, Traffic Manager sondowanie agenta ponownie próbuje zgodnie z ustawieniem dopuszczalne liczbę błędów (ponowne próby będą Jeśli to ustawienie ma wartość 0). Jeśli liczbę kolejnych niepowodzeń jest wyższa niż wartość ustawienia dopuszczalne liczbę błędów, ten punkt końcowy jest oznaczona złej kondycji.

We wszystkich przypadkach sondy Menedżera ruchu z wielu lokalizacji i oznaczanie niepowodzenie kolejnych się stanie w każdym regionie. Oznacza to również, czy punkty końcowe otrzymują sondy kondycji z Menedżera ruchu z częstotliwością wyższa niż wartość ustawienia używane dla interwału sondowania.

>[!NOTE]
>Dla protokołu HTTP lub HTTPS Protokół monitorowania popularną praktyką po stronie punktu końcowego jest wprowadzenie niestandardowej strony w aplikacji — na przykład /health.aspx. Przy użyciu tej ścieżki do monitorowania, można wykonać testy specyficzne dla aplikacji, takich jak sprawdzanie liczników wydajności lub Sprawdzanie dostępności bazy danych. W oparciu o te niestandardowe kontroli, strony zwraca odpowiedni kod stanu HTTP.

Wszystkie punkty końcowe w profilu usługi Traffic Manager udostępnić ustawienia monitorowania. Jeśli musisz używać różnych ustawień monitorowania dla różnych punktów końcowych, możesz utworzyć [zagnieżdżonych profilów usługi Traffic Manager](traffic-manager-nested-profiles.md#example-5-per-endpoint-monitoring-settings).

## <a name="endpoint-and-profile-status"></a>Stan punktu końcowego i profilu

Można włączyć i wyłączyć profilów usługi Traffic Manager i punktów końcowych. Jednak zmiany w stan punktu końcowego również mogą wystąpić jako wynik Traffic Manager automatycznego ustawienia i procesów.

### <a name="endpoint-status"></a>Stan punktu końcowego

Można włączyć lub wyłączyć określonego punktu końcowego. Usługi podstawowej, która może nadal być dobrej kondycji, nie mają wpływu. Zmiana stanu punktu końcowego określa dostępność punktu końcowego profilu Menedżera ruchu. Po wyłączeniu stan punktu końcowego Menedżera ruchu nie sprawdza jego kondycji, a punkt końcowy nie jest dołączany do odpowiedzi DNS.

### <a name="profile-status"></a>Stan profilu

Przy użyciu ustawienia stanu profilu, można włączyć lub wyłączyć określonego profilu. Gdy stan punktu końcowego ma wpływ na jeden punkt końcowy, stan profilu wpływa na całą profil, w tym wszystkie punkty końcowe. Po wyłączeniu profilu punktów końcowych, które nie są sprawdzane pod kątem kondycji i punkty końcowe nie są uwzględnione w odpowiedzi DNS. [NXDOMAIN](https://tools.ietf.org/html/rfc2308) kod odpowiedzi jest zwracany dla zapytania DNS.

### <a name="endpoint-monitor-status"></a>Stan monitora punktu końcowego

Stan monitora punktu końcowego jest generowanych przez Menedżera ruchu wartość, która wyświetla stan punktu końcowego. Nie można zmienić tego ustawienia ręcznie. Monitoruje stan punktu końcowego jest kombinacją wyniki monitorowania punktów końcowych i stan skonfigurowanego punktu końcowego. Możliwe wartości stanu monitora punktu końcowego są przedstawione w poniższej tabeli:

| Stan profilu | Stan punktu końcowego | Stan monitora punktu końcowego | Uwagi |
| --- | --- | --- | --- |
| Disabled (Wyłączony) |Enabled (Włączony) |Nieaktywna |Profil został wyłączony. Mimo że stan punktu końcowego jest włączona, pierwszeństwo ma stan profilu (wyłączone). Punkty końcowe w profilach wyłączone nie są monitorowane. NXDOMAIN kod odpowiedzi jest zwracany dla zapytania DNS. |
| &lt;wszystkie&gt; |Disabled (Wyłączony) |Disabled (Wyłączony) |Punkt końcowy został wyłączony. Wyłączone punkty końcowe nie są monitorowane. Punkt końcowy nie jest uwzględniony w odpowiedzi DNS, w związku z tym nie odbieranie ruchu. |
| Enabled (Włączony) |Enabled (Włączony) |Online |Punkt końcowy jest monitorowane i jest w dobrej kondycji. Jest dołączony do odpowiedzi DNS, a mogą odbierać dane. |
| Enabled (Włączony) |Enabled (Włączony) |Obniżono poziom |Monitorowania kontroli kondycji punktu końcowego kończą się niepowodzeniem. Punkt końcowy nie jest uwzględniony w odpowiedzi DNS i nie odbiera ruch. <br>Wyjątkiem jest Jeśli wszystkie punkty końcowe są ograniczone, w którym to przypadku wszystkie z nich są traktowane jako ma zostać zwrócona w odpowiedzi na zapytanie).</br>|
| Enabled (Włączony) |Enabled (Włączony) |CheckingEndpoint |Punkt końcowy jest monitorowana, ale wyniki pierwsze badanie nie zostały jeszcze odebrane. CheckingEndpoint jest to stan przejściowy, który zazwyczaj występuje natychmiast po dodaniu lub włączanie punktu końcowego w profilu. Punkt końcowy w tym stanie jest dołączony do odpowiedzi DNS i mogą odbierać dane. |
| Enabled (Włączony) |Enabled (Włączony) |Zatrzymano |Chmury usługi lub aplikacji sieci web, która wskazuje punkt końcowy nie jest uruchomiona. Sprawdź ustawienia aplikacji usługi lub aplikacji internetowej chmury. Może to również nastąpić, jeśli punkt końcowy jest zagnieżdżony typ punktu końcowego i profilu podrzędnych jest wyłączony lub jest nieaktywna. <br>Punkt końcowy ze stanem zatrzymania nie jest monitorowany. Nie jest uwzględniony w odpowiedzi DNS, a nie odbiera ruch. Wyjątkiem jest, jeśli wszystkie punkty końcowe są ograniczone, w którym to przypadku wszystkie z nich będą uznawane za ma zostać zwrócona w odpowiedzi na kwerendę.</br>|

Szczegółowe informacje na temat obliczania stan monitora punktu końcowego dla zagnieżdżonej punktów końcowych, zobacz [zagnieżdżonych profilów usługi Traffic Manager](traffic-manager-nested-profiles.md).

>[!NOTE]
> Stan monitora zatrzymać punktu końcowego możliwe w usłudze aplikacji aplikacji sieci web nie działa w warstwie standardowej lub nowszym. Aby uzyskać więcej informacji, zobacz [integracji Menedżera ruchu z usługi aplikacji](/azure/app-service/web-sites-traffic-manager).

### <a name="profile-monitor-status"></a>Stan monitora profilu

Stan monitora profilu jest kombinacją stan skonfigurowanego profilu i punktu końcowego wartości stanu monitora dla wszystkich punktów końcowych. Możliwe wartości zostały opisane w poniższej tabeli:

| Stan profilu (zgodnie z konfiguracją) | Stan monitora punktu końcowego | Stan monitora profilu | Uwagi |
| --- | --- | --- | --- |
| Disabled (Wyłączony) |&lt;wszelkie&gt; lub profil z określonych punktów końcowych. |Disabled (Wyłączony) |Profil został wyłączony. |
| Enabled (Włączony) |Stan co najmniej jeden punkt końcowy jest znacznie mniej wydajna. |Obniżono poziom |Przejrzyj wartości stanu poszczególnych punktu końcowego, aby ustalić, które punkty końcowe wymagać dalszych działań. |
| Enabled (Włączony) |Stan co najmniej jeden punkt końcowy jest w trybie Online. Punkty końcowe nie będą miały stan obniżony. |Online |Usługa akceptuje ruch. Nie są wymagane żadne dalsze działania. |
| Enabled (Włączony) |Stan co najmniej jeden punkt końcowy jest CheckingEndpoint. Punkty końcowe nie są w stanie Online lub obniżony. |CheckingEndpoints |Ten stan przejścia występuje, gdy profilu, jeśli utworzone lub włączone. Kondycja punktu końcowego jest sprawdzany po raz pierwszy. |
| Enabled (Włączony) |Stany wszystkie punkty końcowe w profilu są wyłączone lub zatrzymana lub profil nie ma zdefiniowanych punktów końcowych. |Nieaktywna |Punkty końcowe nie są aktywne, ale profil jest nadal włączony. |

## <a name="endpoint-failover-and-recovery"></a>Punkt końcowy trybu failover i odzyskiwania

Menedżer ruchu okresowo sprawdza kondycję każdego punktu końcowego, w tym zła punktów końcowych. Menedżer ruchu wykrywa punkt końcowy staje się dobrej kondycji, a jego powrót do obrotu.

Punkt końcowy jest zła, gdy wystąpienia któregokolwiek z następujących zdarzeń:
- W przypadku monitorowania protokołu HTTP lub HTTPS:
    - Odebrano odpowiedź z systemem innym niż 200 (w tym kod różnych 2xx lub przekierowanie 301/302).
- Jeśli protokół monitorowania jest TCP: 
    - Odebrano odpowiedź niż potwierdzenia lub SYN potwierdzenia w odpowiedzi na żądania SYNCHRONIZACJI wysyłane przez usługę Traffic Manager próby ustanowienia połączenia.
- Limit czasu. 
- Wszystkie inne problem z połączeniem spowodować, że punkt końcowy nie są dostępne.

Aby uzyskać więcej informacji dotyczących rozwiązywania problemów nie powiodło się sprawdzenie, zobacz [stan rozwiązywania problemów ze spadkiem w usłudze Azure Traffic Manager](traffic-manager-troubleshooting-degraded.md). 

Następujące oś czasu na rysunku 2 jest szczegółowy opis procesu monitorowania punktu końcowego Menedżera ruchu, który ma następujące ustawienia: jest monitorowanie protokołu HTTP, interwał sondowania wynosi 30 sekund, liczba niepowodzeń tolerowaną to 3, wartość limitu czasu wynosi 10 sekund i czas wygaśnięcia DNS to 30 sekund.

![Punkt końcowy Menedżera ruchu sekwencji trybu failover i powrotu po awarii](./media/traffic-manager-monitoring/timeline.png)

**Rysunek 2: Ruchu Menedżer punktu końcowego trybu failover i odzyskiwania sekwencji**

1. **GET**. Dla każdego punktu końcowego Menedżera ruchu monitorowania systemu wykonuje żądanie GET w ścieżce określonej w ustawieniach monitorowania.
2. **200 OK**. System monitorowania oczekuje komunikat HTTP 200 OK ma zostać zwrócona w ciągu 10 sekund. Po otrzymaniu tej odpowiedzi rozpoznaje, że usługa jest dostępna.
3. **30 sekund między każdym sprawdzeniem**. Sprawdzenie kondycji punktu końcowego jest powtarzany co 30 sekund.
4. **Usługa jest niedostępna**. Usługa jest niedostępny. Menedżer ruchu nie będzie wiedzieć, do czasu następnego sprawdzania kondycji.
5. **Próbuje uzyskać dostęp do ścieżki monitorowania**. System monitorowania wykonuje żądanie GET, ale nie otrzymał odpowiedzi przed upływem limitu czasu 10 sekund (alternatywnie odpowiedzi z systemem innym niż 200 mogą pojawić się). Próbuje trzy razy, w odstępach 30 sekund. Jeśli jeden z wpisów zakończy się pomyślnie, liczbę prób jest resetowany.
6. **Stan ustawiony na obniżony**. Po czwartym niepowodzeniu kolejnych monitorowania systemu oznacza stan punktu końcowego niedostępny jako obniżony.
7. **Ruch jest kierowane do innych punktów końcowych**. Serwery nazw DNS Menedżera ruchu są aktualizowane i Menedżera ruchu już zwraca punkt końcowy w odpowiedzi na zapytania DNS. Nowe połączenia są kierowane do innych, dostępnych punktów końcowych. Jednak poprzedniej odpowiedzi DNS, które zawierają ten punkt końcowy nadal można buforować cykliczne serwery DNS i klientów DNS. Klienci w dalszym ciągu korzystać z punktu końcowego do momentu wygaśnięcia pamięci podręcznej DNS. Zgodnie z pamięci podręcznej DNS wygaśnie, klienci utworzyć nowego zapytania DNS i są kierowane do różnych punktów końcowych. Czas trwania pamięci podręcznej jest kontrolowana przez ustawienie Czas wygaśnięcia w profilu usługi Traffic Manager, na przykład 30 sekund.
8. **Kontrole kondycji kontynuować**. Menedżer ruchu w dalszym ciągu Sprawdź kondycję punktu końcowego, gdy ma stan obniżony. Menedżer ruchu wykrywa zwraca punkt końcowy do kondycji.
9. **Usługa wraca do trybu online**. Usługa stanie się dostępny. Punkt końcowy zachowuje jego stan obniżony w usłudze Traffic Manager do momentu jego następnego sprawdzania kondycji wykonuje system monitorowania.
10. **Ruch do usługi wznawia**. Traffic Manager wysyła żądanie GET i odbiera odpowiedź 200 OK stanu. Usługa powrócił do stanu dobrej kondycji. Serwery nazw Menedżera ruchu są aktualizowane i rozpoczęciem przekazywaniu nazwę DNS usługi w odpowiedzi DNS. Ruch zwraca do punktu końcowego buforowane odpowiedzi DNS zwracające innych punktów końcowych wygasa, a jako istniejących połączeń z innych punktów końcowych są zamykane.

    > [!NOTE]
    > Ponieważ usługi Traffic Manager działa na poziomie DNS, nie może mieć wpływ istniejących połączeń z dowolnego punktu końcowego. Po jego kieruje ruch między punktami końcowymi (albo przez ustawienia profilu zmienione albo podczas pracy awaryjnej lub powrotu po awarii), usługi Traffic Manager kieruje nowych połączeń z dostępnych punktów końcowych. Jednak inne punkty końcowe mogą w dalszym ciągu odbieranie ruchu za pośrednictwem istniejących połączeń, dopóki nie kończą się tymi sesjami. Aby umożliwić ruch do opróżnienia z istniejących połączeń, aplikacji, należy ograniczyć czas trwania sesji z każdego punktu końcowego.

## <a name="traffic-routing-methods"></a>Metody routingu ruchu

Punkt końcowy ma stan obniżony, jest już zwracany w odpowiedzi na zapytania DNS. Zamiast tego alternatywny punkt końcowy jest wybrane i zwracane. Metoda routingu ruchu skonfigurowane w profilu określa, jak wybrano alternatywny punktu końcowego.

* **Priorytet**. Punkty końcowe tworzą listy priorytetów. Zwracany jest zawsze pierwszego dostępnego punktu końcowego na liście. Jeśli stan punktu końcowego jest ograniczone, zwracany jest następnego dostępnego punktu końcowego.
* **Ważone**. Wszystkie dostępne punktu końcowego jest wybierany losowo na podstawie ich wagi przypisanej i wagi innych dostępnych punktów końcowych.
* **Wydajność**. Zwracany jest punktem końcowym najbliżej użytkownika końcowego. Jeśli ten punkt końcowy jest niedostępny, Menedżera ruchu przenosi ruch do punktów końcowych w następnym najbliższy region platformy Azure. Plany alternatywnych trybu failover dla routingu ruchu wydajności można skonfigurować za pomocą [zagnieżdżonych profilów usługi Traffic Manager](traffic-manager-nested-profiles.md#example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region).
* **Geograficzne**. Zwracany jest mapowane do obsługi lokalizacji geograficznej, na podstawie zapytania żądania punktu końcowego adresami IP. Jeśli ten punkt końcowy jest niedostępny, inny punkt końcowy nie zostanie wybrany do trybu failover, ponieważ lokalizacji geograficznej, mogą być mapowane tylko na jeden punkt końcowy w profilu (szczegółowe informacje znajdują się w [— często zadawane pytania](traffic-manager-FAQs.md#traffic-manager-geographic-traffic-routing-method)). Najlepszym rozwiązaniem, przy użyciu routingu geograficznego firma Microsoft zaleca klientom korzystanie z więcej niż jeden punkt końcowy zagnieżdżonych profilów usługi Traffic Manager, jako punkty końcowe profilu.

Aby uzyskać więcej informacji, zobacz [metody routingu ruchu Menedżera ruchu](traffic-manager-routing-methods.md).

> [!NOTE]
> Jedynym wyjątkiem normalne zachowanie routingu ruchu występuje, gdy wszystkie kwalifikujące się punkty końcowe mają obniżeniem stanu. Sprawia, że Menedżera ruchu "optymalnego" podejmować i *odpowiada tak, jakby wszystkie punkty końcowe stan obniżony faktycznie są w stanie online*. To zachowanie jest inny byłoby nie zwrócił żadnych punktów końcowych w odpowiedzi DNS. Wyłączony lub zatrzymany punkty końcowe nie są monitorowane, w związku z tym nie uważa się kwalifikujące się do ruchu sieciowego.
>
> Ten stan jest często spowodowane niewłaściwa konfiguracja usługi, takie jak:
>
> * Sprawdza, czy lista kontroli dostępu [ACL] blokowaniem kondycji usługi Traffic Manager.
> * Niepoprawna konfiguracja monitorowania portu lub protokół w profilu Menedżera ruchu.
>
> Konsekwencją to zachowanie jest czy kontroli kondycji usługi Traffic Manager nie są poprawnie skonfigurowane, mogą być wyświetlane do od ruchu routingu jako menedżera ruchu *jest* działa prawidłowo. Jednak w takim przypadku trybu failover punktu końcowego nie może się zdarzyć, co ma wpływ na dostępność aplikacji ogólnej. Należy sprawdzić, czy profil pokazuje stan Online, nie stan obniżony. Stan Online wskazuje, że sprawdzenia kondycji usługi Traffic Manager działają zgodnie z oczekiwaniami.

Aby uzyskać więcej informacji na temat rozwiązywania problemów nie przeszły pomyślnie sprawdzania kondycji, zobacz [stan rozwiązywania problemów ze spadkiem w usłudze Azure Traffic Manager](traffic-manager-troubleshooting-degraded.md).



## <a name="next-steps"></a>Kolejne kroki

Dowiedz się [działania Menedżera ruchu](traffic-manager-how-traffic-manager-works.md)

Dowiedz się więcej o [metody routingu ruchu](traffic-manager-routing-methods.md) obsługiwane przez Menedżera ruchu

Dowiedz się, jak [Tworzenie profilu Menedżera ruchu](traffic-manager-manage-profiles.md)

[Rozwiązywanie problemów z obniżony stan](traffic-manager-troubleshooting-degraded.md) na punkt końcowy Menedżera ruchu
