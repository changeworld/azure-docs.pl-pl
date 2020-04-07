---
title: Monitorowanie punktów końcowych usługi Azure Traffic Manager | Dokumenty firmy Microsoft
description: Ten artykuł ułatwia zrozumienie, w jaki sposób usługa Traffic Manager używa monitorowania punktów końcowych i automatycznego trybu failover punktu końcowego, aby pomóc klientom platformy Azure we wdrażaniu aplikacji o wysokiej dostępności
services: traffic-manager
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2018
ms.author: rohink
ms.openlocfilehash: 61aafbe8cb12e93d72f5efd01155f06fb3ec0c28
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80757261"
---
# <a name="traffic-manager-endpoint-monitoring"></a>Monitorowanie punktu końcowego usługi Traffic Manager

Usługa Azure Traffic Manager zawiera wbudowane monitorowanie punktów końcowych i automatyczną przebót w trybie failover punktu końcowego. Ta funkcja ułatwia dostarczanie aplikacji o wysokiej dostępności, które są odporne na awarie punktu końcowego, w tym błędy regionu platformy Azure.

## <a name="configure-endpoint-monitoring"></a>Konfigurowanie monitorowania punktów końcowych

Aby skonfigurować monitorowanie punktów końcowych, należy określić następujące ustawienia w profilu usługi Traffic Manager:

* **protokołu**. Wybierz protokół HTTP, HTTPS lub TCP jako protokół używany przez program Traffic Manager podczas sondowania punktu końcowego w celu sprawdzenia jego kondycji. Monitorowanie protokołu HTTPS nie sprawdza, czy certyfikat TLS/SSL jest prawidłowy — sprawdza tylko, czy certyfikat jest obecny.
* **Port**. Wybierz port używany dla żądania.
* **Ścieżka**. To ustawienie konfiguracji jest prawidłowe tylko dla protokołów HTTP i HTTPS, dla których wymagane jest określenie ustawienia ścieżki. Podanie tego ustawienia dla protokołu monitorowania TCP powoduje błąd. W przypadku protokołu HTTP i HTTPS podaj ścieżkę względną i nazwę strony sieci Web lub pliku, do który uzyskuje dostęp monitorujący. Ukośnik do przodu (/) jest prawidłowym wpisem dla ścieżki względnej. Ta wartość oznacza, że plik znajduje się w katalogu głównym (domyślnie).
* **Niestandardowe ustawienia nagłówka** To ustawienie konfiguracji ułatwia dodawanie określonych nagłówków HTTP do kontroli kondycji wysyłanych przez Menedżera ruchu do punktów końcowych w profilu. Nagłówki niestandardowe można określić na poziomie profilu, który ma zastosowanie do wszystkich punktów końcowych w tym profilu i / lub na poziomie punktu końcowego mającego zastosowanie tylko do tego punktu końcowego. Nagłówki niestandardowe można użyć do sprawdzania kondycji do punktów końcowych w środowisku wielodostępnym być kierowane poprawnie do miejsca docelowego, określając nagłówek hosta. Tego ustawienia można również użyć, dodając unikatowe nagłówki, które mogą służyć do identyfikowania żądań http(s) pochodzących z usługi Traffic Manager i przetwarza je w inny sposób. Można określić maksymalnie osiem par nagłówków:wartość oddzielonych przecinkiem. Na przykład "header1:value1,header2:value2". 
* **Oczekiwane zakresy kodów stanu** To ustawienie umożliwia określenie wielu zakresów kodu sukcesu w formacie 200-299, 301-301. Jeśli te kody stanu są odbierane jako odpowiedź z punktu końcowego, gdy jest inicjowane sprawdzanie kondycji, usługa Traffic Manager oznacza te punkty końcowe jako w dobrej kondycji. Można określić maksymalnie 8 zakresów kodu stanu. To ustawienie ma zastosowanie tylko do protokołu HTTP i HTTPS oraz do wszystkich punktów końcowych. To ustawienie znajduje się na poziomie profilu usługi Traffic Manager i domyślnie wartość 200 jest zdefiniowana jako kod stanu sukcesu.
* **Interwał sondowania**. Ta wartość określa, jak często punkt końcowy jest sprawdzany pod kątem jego kondycji z agenta sondowania usługi Traffic Manager. W tym miejscu można określić dwie wartości: 30 sekund (normalne sondowanie) i 10 sekund (szybkie sondowanie). Jeśli nie podano żadnych wartości, profil ustawia wartość domyślną 30 sekund. Odwiedź stronę [Cennik menedżera ruchu,](https://azure.microsoft.com/pricing/details/traffic-manager) aby dowiedzieć się więcej o szybkich cenach sondowania.
* **Tolerowana liczba niepowodzeń**. Ta wartość określa, ile błędów agent sondowania usługi Traffic Manager toleruje przed oznaczeniem tego punktu końcowego jako złej kondycji. Jego wartość może wynosić od 0 do 9. Wartość 0 oznacza, że pojedynczy błąd monitorowania może spowodować, że punkt końcowy, które mają być oznaczone jako złej kondycji. Jeśli nie określono żadnej wartości, używa domyślnej wartości 3.
* **Limit czasu sondy**. Ta właściwość określa czas agent sondowania usługi Traffic Manager powinien czekać przed rozważeniem, że sprawdzić błąd, gdy sonda sprawdzania kondycji jest wysyłana do punktu końcowego. Jeśli interwał sondowania jest ustawiony na 30 sekund, można ustawić wartość limitu czasu między 5 i 10 sekund. Jeśli nie określono żadnej wartości, używa domyślnej wartości 10 sekund. Jeśli interwał sondowania jest ustawiony na 10 sekund, można ustawić wartość limitu czasu między 5 i 9 sekund. Jeśli nie określono żadnej wartości limitu czasu, używa domyślnej wartości 9 sekund.

    ![Monitorowanie punktu końcowego usługi Traffic Manager](./media/traffic-manager-monitoring/endpoint-monitoring-settings.png)

    **Rysunek: Monitorowanie punktu końcowego usługi Traffic Manager**

## <a name="how-endpoint-monitoring-works"></a>Jak działa monitorowanie punktów końcowych

Jeśli protokół monitorowania jest ustawiony jako HTTP lub HTTPS, agent sondowania Usługi Traffic Manager sprawia, że żądanie GET do punktu końcowego przy użyciu protokołu, portu i ścieżki względnej podane. Jeśli zostanie wycofana odpowiedź 200-OK lub którakolwiek z odpowiedzi skonfigurowanych w **zakresach kodu \*stanu Oczekiwany**, ten punkt końcowy jest uważany za zdrowy. Jeśli odpowiedź jest inną wartością lub, jeśli nie odebrano odpowiedzi w określonym upływie limitu czasu, agent sondowania usługi Traffic Manager ponownie próbuje zgodnie z ustawieniem Tolerowana liczba błędów (jeśli to ustawienie jest 0). Jeśli liczba kolejnych awarii jest wyższa niż ustawienie Tolerowana liczba błędów, ten punkt końcowy jest oznaczony jako w złej kondycji. 

Jeśli protokół monitorowania to TCP, agent sondujący usługi Traffic Manager inicjuje żądanie połączenia TCP przy użyciu określonego portu. Jeśli punkt końcowy odpowiada na żądanie z odpowiedzią na ustanowienie połączenia, to sprawdzenie kondycji jest oznaczony jako sukces i agent sondowania usługi Traffic Manager resetuje połączenie TCP. Jeśli odpowiedź jest inna wartość lub jeśli odpowiedź nie zostanie odebrana w określonym okresie limitu czasu, agent sondowania usługi Traffic Manager ponownie próbuje zgodnie z ustawieniem Tolerowana liczba błędów (nie są podejmowane ponowne próby, jeśli to ustawienie jest 0). Jeśli liczba kolejnych awarii jest wyższa niż ustawienie Tolerowana liczba błędów, ten punkt końcowy jest oznaczony w złej kondycji.

We wszystkich przypadkach usługa Traffic Manager sonduje z wielu lokalizacji i kolejne określanie awarii odbywa się w każdym regionie. Oznacza to również, że punkty końcowe odbierają sondy kondycji z menedżera ruchu z większą częstotliwością niż ustawienie używane dla interwału sondowania.

>[!NOTE]
>W przypadku protokołu monitorowania HTTP lub HTTPS powszechną praktyką po stronie punktu końcowego jest zaimplementowanie strony niestandardowej w aplikacji — na przykład /health.aspx. Za pomocą tej ścieżki do monitorowania, można wykonać kontrole specyficzne dla aplikacji, takie jak sprawdzanie liczników wydajności lub sprawdzanie dostępności bazy danych. Na podstawie tych niestandardowych kontroli strona zwraca odpowiedni kod stanu HTTP.

Wszystkie punkty końcowe w profilu usługi Traffic Manager współużytkują ustawienia monitorowania. Jeśli chcesz użyć różnych ustawień monitorowania dla różnych punktów końcowych, możesz utworzyć [zagnieżdżone profile usługi Traffic Manager](traffic-manager-nested-profiles.md#example-5-per-endpoint-monitoring-settings).

## <a name="endpoint-and-profile-status"></a>Stan punktu końcowego i profilu

Można włączyć i wyłączyć profile usługi Traffic Manager i punkty końcowe. Jednak zmiana stanu punktu końcowego może również wystąpić w wyniku automatycznych ustawień i procesów usługi Traffic Manager.

### <a name="endpoint-status"></a>Stan punktu końcowego

Można włączyć lub wyłączyć określony punkt końcowy. Podstawowa usługa, która może nadal być w dobrej kondycji, pozostaje nienaruszona. Zmiana stanu punktu końcowego steruje dostępnością punktu końcowego w profilu usługi Traffic Manager. Gdy stan punktu końcowego jest wyłączony, Usługa Traffic Manager nie sprawdza jego kondycji, a punkt końcowy nie jest uwzględniony w odpowiedzi DNS.

### <a name="profile-status"></a>Stan profilu

Za pomocą ustawienia stanu profilu można włączyć lub wyłączyć określony profil. Podczas gdy stan punktu końcowego wpływa na pojedynczy punkt końcowy, stan profilu wpływa na cały profil, w tym wszystkie punkty końcowe. Po wyłączeniu profilu punkty końcowe nie są sprawdzane pod kątem kondycji i żadne punkty końcowe nie są uwzględniane w odpowiedzi DNS. Kod odpowiedzi [NXDOMAIN](https://tools.ietf.org/html/rfc2308) jest zwracany dla kwerendy DNS.

### <a name="endpoint-monitor-status"></a>Stan monitora punktu końcowego

Stan monitora punktu końcowego jest wartością wygenerowaną przez menedżera ruchu, która pokazuje stan punktu końcowego. Nie można zmienić tego ustawienia ręcznie. Stan monitora punktu końcowego jest kombinacją wyników monitorowania punktu końcowego i skonfigurowanego stanu punktu końcowego. Możliwe wartości stanu monitora punktu końcowego są pokazane w poniższej tabeli:

| Stan profilu | Stan punktu końcowego | Stan monitora punktu końcowego | Uwagi |
| --- | --- | --- | --- |
| Disabled (Wyłączony) |Enabled (Włączony) |Nieaktywne |Profil został wyłączony. Chociaż stan punktu końcowego jest Włączone, stan profilu (Wyłączone) ma pierwszeństwo. Punkty końcowe w profilach wyłączonych nie są monitorowane. Kod odpowiedzi NXDOMAIN jest zwracany dla kwerendy DNS. |
| &lt;dowolne&gt; |Disabled (Wyłączony) |Disabled (Wyłączony) |Punkt końcowy został wyłączony. Wyłączone punkty końcowe nie są monitorowane. Punkt końcowy nie jest uwzględniony w odpowiedziach DNS, w związku z tym nie odbiera ruchu. |
| Enabled (Włączony) |Enabled (Włączony) |Online |Punkt końcowy jest monitorowany i jest w dobrej kondycji. Jest on zawarty w odpowiedziach DNS i może odbierać ruch. |
| Enabled (Włączony) |Enabled (Włączony) |Obniżona wydajność |Sprawdzanie kondycji monitorowania punktu końcowego kończy się niepowodzeniem. Punkt końcowy nie jest uwzględniony w odpowiedziach DNS i nie odbiera ruchu. <br>Wyjątek od tego jest, jeśli wszystkie punkty końcowe są zdegradowane, w którym to przypadku wszystkie z nich są uważane za zwrócone w odpowiedzi na kwerendę.</br>|
| Enabled (Włączony) |Enabled (Włączony) |Punkt sprawdzania |Punkt końcowy jest monitorowany, ale wyniki pierwszej sondy nie zostały jeszcze odebrane. CheckingEndpoint jest stan tymczasowy, który zwykle występuje natychmiast po dodaniu lub włączenie punktu końcowego w profilu. Punkt końcowy w tym stanie jest uwzględniony w odpowiedziach DNS i może odbierać ruch. |
| Enabled (Włączony) |Enabled (Włączony) |Zatrzymano |Aplikacja sieci web, która wskazuje punkt końcowy nie jest uruchomiona. Sprawdź ustawienia aplikacji internetowej. Może się to również zdarzyć, jeśli punkt końcowy jest typu zagnieżdżonego punktu końcowego i profil podrzędny jest wyłączony lub jest nieaktywny. <br>Punkt końcowy o stanie Zatrzymane nie jest monitorowany. Nie jest uwzględniony w odpowiedziach DNS i nie odbiera ruchu. Wyjątek od tego jest, jeśli wszystkie punkty końcowe są zdegradowane, w którym to przypadku wszystkie z nich zostaną uznane za zwrócone w odpowiedzi na kwerendę.</br>|

Aby uzyskać szczegółowe informacje o sposobie obliczania stanu monitora punktu końcowego dla zagnieżdżonych punktów końcowych, zobacz [zagnieżdżone profile usługi Traffic Manager](traffic-manager-nested-profiles.md).

>[!NOTE]
> Stan monitora zatrzymanego punktu końcowego może się zdarzyć w usłudze App Service, jeśli aplikacja sieci web nie jest uruchomiona w warstwie Standardowa lub wyższa. Aby uzyskać więcej informacji, zobacz [Integracja usługi Traffic Manager z usługą App Service](/azure/app-service/web-sites-traffic-manager).

### <a name="profile-monitor-status"></a>Stan monitora profilu

Stan monitora profilu jest kombinacją skonfigurowanego stanu profilu i wartości stanu monitora punktu końcowego dla wszystkich punktów końcowych. Możliwe wartości są opisane w poniższej tabeli:

| Stan profilu (skonfigurowany) | Stan monitora punktu końcowego | Stan monitora profilu | Uwagi |
| --- | --- | --- | --- |
| Disabled (Wyłączony) |&lt;profilu&gt; bez zdefiniowanych punktów końcowych. |Disabled (Wyłączony) |Profil został wyłączony. |
| Enabled (Włączony) |Stan co najmniej jednego punktu końcowego jest obniżony. |Obniżona wydajność |Przejrzyj poszczególne wartości stanu punktu końcowego, aby określić, które punkty końcowe wymagają dalszej uwagi. |
| Enabled (Włączony) |Stan co najmniej jednego punktu końcowego jest w trybie online. Żadne punkty końcowe nie mają stanu zdegradowany. |Online |Usługa akceptuje ruch. Nie są wymagane żadne dalsze działania. |
| Enabled (Włączony) |Stan co najmniej jednego punktu końcowego jest CheckingEndpoint. Żadne punkty końcowe nie są w stanie Online lub zdegradowane. |Punkty sprawdzania |Ten stan przejścia występuje, gdy profil, jeśli został utworzony lub włączony. Kondycja punktu końcowego jest sprawdzana po raz pierwszy. |
| Enabled (Włączony) |Stany wszystkich punktów końcowych w profilu są wyłączone lub zatrzymane lub profil nie ma zdefiniowanych punktów końcowych. |Nieaktywne |Żadne punkty końcowe nie są aktywne, ale profil jest nadal włączone. |

## <a name="endpoint-failover-and-recovery"></a>Przewijanie i odzyskiwanie punktu końcowego

Usługa Traffic Manager okresowo sprawdza kondycję każdego punktu końcowego, w tym złej kondycji punktów końcowych. Usługa Traffic Manager wykrywa, kiedy punkt końcowy staje się w dobrej kondycji i przywraca go do obrotu.

Punkt końcowy jest w złej kondycji, gdy wystąpi którykolwiek z następujących zdarzeń:

- Jeśli protokół monitorowania to HTTP lub HTTPS:
    - Odbierana jest odpowiedź inna niż 200 lub odpowiedź, która nie zawiera zakresu stanu określonego w ustawieniu **Zakresy kodów stanu oczekiwany** (w tym inny kod 2xx lub przekierowanie 301/302).
- Jeśli protokołem monitorowania jest protokół TCP: 
    - Odpowiedź inna niż ACK lub SYN-ACK jest odbierana w odpowiedzi na żądanie SYN wysłane przez Traffic Manager w celu podjęcia próby ustanowienia połączenia.
- Limit czasu. 
- Każdy inny problem z połączeniem, w wyniku którego punkt końcowy nie jest osiągalny.

Aby uzyskać więcej informacji na temat rozwiązywania problemów z nieudanymi kontrolami, zobacz [Rozwiązywanie problemów ze stanem awaryjnym w usłudze Azure Traffic Manager](traffic-manager-troubleshooting-degraded.md). 

Oś czasu na poniższym rysunku jest szczegółowy opis procesu monitorowania punktu końcowego usługi Traffic Manager, który ma następujące ustawienia: protokół monitorowania jest HTTP, interwał sondowania wynosi 30 sekund, liczba tolerowanych błędów wynosi 3, wartość limitu czasu wynosi 10 sekund, a czas wygaśnięcia DNS wynosi 30 sekund.

![Sekwencja pracy awaryjnej punktu końcowego i powrotu po awarii usługi Usługi Traffic Manager](./media/traffic-manager-monitoring/timeline.png)

**Rysunek: Sekwencja trybu failover i sekwencji odzyskiwania punktu końcowego usługi usługi trafficpoint**

1. **POBIERZ**. Dla każdego punktu końcowego system monitorowania usługi Traffic Manager wykonuje żądanie GET na ścieżce określonej w ustawieniach monitorowania.
2. **200 OK lub niestandardowy zakres kodu określony ustawienia monitorowania profilu usługi Traffic Manager** . System monitorowania oczekuje, że komunikat o ustawieniach profilu usługi HTTP 200 OK lub niestandardowy zakres kodu określony w usłudze Traffic Manager zostanie zwrócony w ciągu 10 sekund. Po otrzymaniu tej odpowiedzi, rozpoznaje, że usługa jest dostępna.
3. **30 sekund między kontrolami**. Sprawdzanie kondycji punktu końcowego jest powtarzane co 30 sekund.
4. **Usługa niedostępna**. Usługa staje się niedostępna. Traffic Manager nie będzie wiedział, aż do następnej kontroli kondycji.
5. **Próbuje uzyskać dostęp do ścieżki monitorowania**. System monitorowania wykonuje żądanie GET, ale nie odbiera odpowiedzi w okresie limitu czasu 10 sekund (alternatywnie może zostać odebrana odpowiedź nienależący do 200). Następnie próbuje jeszcze trzy razy, w odstępach 30-sekundowych. Jeśli jedna z prób zakończy się pomyślnie, liczba prób zostanie zresetowana.
6. **Stan ustawiony na Obniżona**. Po czwartym z rzędu awarii system monitorowania oznacza stan punktu końcowego niedostępne jako zdegradowane.
7. **Ruch jest kierowany do innych punktów końcowych**. Serwery nazw DNS usługi Traffic Manager są aktualizowane, a Usługa Traffic Manager nie zwraca już punktu końcowego w odpowiedzi na zapytania DNS. Nowe połączenia są kierowane do innych, dostępnych punktów końcowych. Jednak poprzednie odpowiedzi DNS, które zawierają ten punkt końcowy, mogą nadal być buforowane przez cykliczne serwery DNS i klientów DNS. Klienci nadal używają punktu końcowego, dopóki pamięć podręczna DNS nie wygaśnie. Po wygaśnięciu pamięci podręcznej DNS klienci dokonują nowych zapytań DNS i są kierowani do różnych punktów końcowych. Czas trwania pamięci podręcznej jest kontrolowany przez ustawienie czasu wygaśnięcia w profilu usługi Traffic Manager, na przykład 30 sekund.
8. **Kontrole zdrowia są kontynuowane**. Usługa Traffic Manager nadal sprawdza kondycję punktu końcowego, gdy ma stan obniżony. Usługa Traffic Manager wykrywa, kiedy punkt końcowy powróci do stanu zdrowia.
9. **Usługa wraca do trybu online**. Usługa staje się dostępna. Punkt końcowy zachowuje jego stan obniżona w usłudze Traffic Manager, dopóki system monitorowania wykonuje następną kontrolę kondycji.
10. **Ruch do serwisu wznawia**. Usługa Traffic Manager wysyła żądanie GET i odbiera odpowiedź o stanie 200 OK. Usługa powróciła do stanu zdrowego. Serwery nazw usługi Traffic Manager są aktualizowane i zaczynają przekazywać nazwę DNS usługi w odpowiedziach DNS. Ruch powraca do punktu końcowego jako buforowane odpowiedzi DNS, które zwracają inne punkty końcowe wygasają, a istniejące połączenia z innymi punktami końcowymi są zakończone.

    > [!NOTE]
    > Ponieważ usługa Traffic Manager działa na poziomie DNS, nie może wpływać na istniejące połączenia z dowolnym punktem końcowym. Gdy kieruje ruch między punktami końcowymi (przez zmienione ustawienia profilu lub podczas pracy awaryjnej lub powrotu po awarii), usługa Traffic Manager kieruje nowe połączenia do dostępnych punktów końcowych. Jednak inne punkty końcowe mogą nadal odbierać ruch za pośrednictwem istniejących połączeń, dopóki te sesje nie zostaną zakończone. Aby włączyć ruch do drenażu z istniejących połączeń, aplikacje powinny ograniczyć czas trwania sesji używane dla każdego punktu końcowego.

## <a name="traffic-routing-methods"></a>Metody wyznaczania tras ruchu

Gdy punkt końcowy ma stan obniżona, nie jest już zwracany w odpowiedzi na zapytania DNS. Zamiast tego alternatywny punkt końcowy jest wybierany i zwracany. Metoda routingu ruchu skonfigurowana w profilu określa sposób wybierania alternatywnego punktu końcowego.

* **Priorytet**. Punkty końcowe tworzą listę priorytetów. Pierwszy dostępny punkt końcowy na liście jest zawsze zwracany. Jeśli stan punktu końcowego jest obniżona, zwracany jest następny dostępny punkt końcowy.
* **Ważona**. Każdy dostępny punkt końcowy jest wybierany losowo na podstawie przypisanych im wag i wag innych dostępnych punktów końcowych.
* **Wydajność**. Zwracany jest punkt końcowy najbliżej użytkownika końcowego. Jeśli ten punkt końcowy jest niedostępny, usługa Traffic Manager przenosi ruch do punktów końcowych w następnym najbliższym regionie platformy Azure. Alternatywne plany pracy awaryjnej można skonfigurować dla routingu ruchu wydajności przy użyciu [zagnieżdżonych profilów usługi Traffic Manager](traffic-manager-nested-profiles.md#example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region).
* **Geograficzne**. Zwracany jest punkt końcowy mapowany do obsługi lokalizacji geograficznej na podstawie adresu IP żądania kwerendy. Jeśli ten punkt końcowy jest niedostępny, inny punkt końcowy nie zostanie wybrany do pracy awaryjnej, ponieważ lokalizacja geograficzna może być mapowana tylko do jednego punktu końcowego w profilu (więcej szczegółów znajduje się w [często zadawanych pytaniach).](traffic-manager-FAQs.md#traffic-manager-geographic-traffic-routing-method) Najlepszym rozwiązaniem, korzystając z routingu geograficznego, zaleca się klientom używanie zagnieżdżonych profilów usługi Traffic Manager z więcej niż jednym punktem końcowym jako punktami końcowymi profilu.
* **Wartość wielowartościowa** Zwraca się wiele punktów końcowych mapowanych na adresy IPv4/IPv6. Po odebraniu kwerendy dla tego profilu, punkty końcowe w dobrej kondycji są zwracane na podstawie **maksymalnej liczby rekordów w** określonej wartości odpowiedzi. Domyślna liczba odpowiedzi to dwa punkty końcowe.
* **Podsieć** Zwracany jest punkt końcowy mapowany na zestaw zakresów adresów IP. Po odebraniu żądania z tego adresu IP zwracany punkt końcowy jest punktem zamapowanym dla tego adresu IP. 

Aby uzyskać więcej informacji, zobacz [Metody routingu ruchu usługi Traffic Manager](traffic-manager-routing-methods.md).

> [!NOTE]
> Jeden wyjątek od normalnego zachowania routingu ruchu występuje, gdy wszystkie kwalifikujące się punkty końcowe mają stan obniżony. Traffic Manager podejmuje próbę "najlepszego wysiłku" i *odpowiada tak, jakby wszystkie punkty końcowe stanu Zdegradowany są w rzeczywistości w stanie online.* To zachowanie jest korzystne dla alternatywy, która polega na nie zwracaniu żadnego punktu końcowego w odpowiedzi DNS. Wyłączone lub zatrzymane punkty końcowe nie są monitorowane, w związku z tym nie są uważane za kwalifikujące się do ruchu.
>
> Warunek ten jest często spowodowany nieprawidłową konfiguracją usługi, taką jak:
>
> * Lista kontroli dostępu [ACL] blokująca kontrole kondycji menedżera ruchu.
> * Nieprawidłowa konfiguracja portu monitorowania lub protokołu w profilu Menedżera ruchu.
>
> Konsekwencją tego zachowania jest to, że jeśli kontrole kondycji usługi Traffic Manager nie są poprawnie skonfigurowane, może pojawić się z routingu ruchu tak, jakby *Usługa* Traffic Manager działa poprawnie. Jednak w tym przypadku nie może się zdarzyć, że usługa trybu failover punktu końcowego wpływa na ogólną dostępność aplikacji. Ważne jest, aby sprawdzić, czy profil pokazuje stan online, a nie stan zdegradowany. Stan Online wskazuje, że kontrole kondycji usługi Traffic Manager działają zgodnie z oczekiwaniami.

Aby uzyskać więcej informacji na temat rozwiązywania problemów z nieudanymi kontrolami kondycji, zobacz [Rozwiązywanie problemów ze stanem pogorszenia w usłudze Azure Traffic Manager](traffic-manager-troubleshooting-degraded.md).

## <a name="faqs"></a>Często zadawane pytania

* [Czy usługa Traffic Manager jest odporna na awarie regionu platformy Azure?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#is-traffic-manager-resilient-to-azure-region-failures)

* [Jak wybór lokalizacji grupy zasobów wpływa na Menedżera ruchu?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-the-choice-of-resource-group-location-affect-traffic-manager)

* [Jak określić bieżący stan każdego punktu końcowego?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-do-i-determine-the-current-health-of-each-endpoint)

* [Czy mogę monitorować punkty końcowe HTTPS?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-monitor-https-endpoints)

* [Czy podczas dodawania punktu końcowego używam adresu IP lub nazwy DNS?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#do-i-use-an-ip-address-or-a-dns-name-when-adding-an-endpoint)

* [Jakich typów adresów IP można używać podczas dodawania punktu końcowego?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-types-of-ip-addresses-can-i-use-when-adding-an-endpoint)

* [Czy mogę używać różnych typów adresowania punktów końcowych w ramach jednego profilu?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-different-endpoint-addressing-types-within-a-single-profile)

* [Co się stanie, gdy typ rekordu kwerendy przychodzącej różni się od typu rekordu skojarzonego z typem adresowania punktów końcowych?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-happens-when-an-incoming-querys-record-type-is-different-from-the-record-type-associated-with-the-addressing-type-of-the-endpoints)

* [Czy mogę używać profilu z punktami końcowymi adresowanymi przez IPv4 / IPv6 w profilu zagnieżdżonego?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-a-profile-with-ipv4--ipv6-addressed-endpoints-in-a-nested-profile)

* [Zatrzymałem punkt końcowy aplikacji sieci web w moim profilu usługi Traffic Manager, ale nie otrzymuję żadnego ruchu nawet po ponownym uruchomieniu. Jak mogę to naprawić?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#i-stopped-an-web-application-endpoint-in-my-traffic-manager-profile-but-i-am-not-receiving-any-traffic-even-after-i-restarted-it-how-can-i-fix-this)

* [Czy mogę korzystać z Usługi Traffic Manager, nawet jeśli moja aplikacja nie obsługuje protokołu HTTP lub HTTPS?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-traffic-manager-even-if-my-application-does-not-have-support-for-http-or-https)

* [Jakie konkretne odpowiedzi są wymagane od punktu końcowego podczas korzystania z monitorowania TCP?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-specific-responses-are-required-from-the-endpoint-when-using-tcp-monitoring)

* [Jak szybko usługa Traffic Manager odsunie użytkowników od punktu końcowego w złej kondycji?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-fast-does-traffic-manager-move-my-users-away-from-an-unhealthy-endpoint)

* [Jak określić różne ustawienia monitorowania dla różnych punktów końcowych w profilu?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-specify-different-monitoring-settings-for-different-endpoints-in-a-profile)

* [Jak przypisać nagłówki HTTP do kontroli kondycji usługi Traffic Manager do moich punktów końcowych?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-assign-http-headers-to-the-traffic-manager-health-checks-to-my-endpoints)

* [Jakiego nagłówka hosta używają kontrole kondycji punktu końcowego?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-host-header-do-endpoint-health-checks-use)

* [Jakie są adresy IP, z których pochodzą kontrole kondycji?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-the-ip-addresses-from-which-the-health-checks-originate)

* [Ile kontroli kondycji do punktu końcowego można oczekiwać od Traffic Manager?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager)

* [Jak mogę otrzymywać powiadomienia, jeśli jeden z moich punktów końcowych ustępuje?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-get-notified-if-one-of-my-endpoints-goes-down)

## <a name="next-steps"></a>Następne kroki

Dowiedz [się, jak działa Usługa Traffic Manager](traffic-manager-how-it-works.md)

Dowiedz się więcej o [metodach routingu ruchu](traffic-manager-routing-methods.md) obsługiwanych przez usługę Traffic Manager

Dowiedz się, jak [utworzyć profil usługi Traffic Manager](traffic-manager-manage-profiles.md)

[Rozwiązywanie problemów ze stanem awaryjnym](traffic-manager-troubleshooting-degraded.md) w punkcie końcowym programu Traffic Manager
