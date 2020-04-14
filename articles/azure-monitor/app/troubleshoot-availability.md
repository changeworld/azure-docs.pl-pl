---
title: Rozwiązywanie problemów z testami dostępności usługi Azure Application Insights
description: Rozwiązywanie problemów z testami sieci Web w usłudze Azure Application Insights. Otrzymywanie alertów, kiedy witryna sieci Web staje się niedostępna lub wolno odpowiada.
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 09/19/2019
ms.reviewer: sdash
ms.openlocfilehash: 94b00a36445b0f4284caba218f6416db726611eb
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255451"
---
# <a name="troubleshooting"></a>Rozwiązywanie problemów

Ten artykuł pomoże Ci rozwiązać typowe problemy, które mogą wystąpić podczas korzystania z monitorowania dostępności.

## <a name="ssltls-errors"></a>Błędy SSL/TLS

|Komunikat o symptomach/błędzie| Możliwe przyczyny|
|--------|------|
|Nie można utworzyć bezpiecznego kanału SSL/TLS  | wersja SSL. Obsługiwane są tylko protokołu TLS 1.0, 1.1 i 1.2. **SSLv3 nie jest obsługiwany.**
|Warstwa rekordu TLSv1.2: Alert (poziom: Krytyczny, Opis: Zły rekord MAC)| Aby [uzyskać więcej informacji,](https://security.stackexchange.com/questions/39844/getting-ssl-alert-write-fatal-bad-record-mac-during-openssl-handshake)zobacz Wątek StackExchange .
|Adres URL, który nie działa, jest siecią CDN (Content Delivery Network) | Może to być spowodowane błędną konfiguracją sieci CDN |  

### <a name="possible-workaround"></a>Możliwe obejście

* Jeśli adresy URL, które występują problem są zawsze do zasobów **zależnych,** zaleca się wyłączenie analizy zależne żądania dla testu sieci web.

## <a name="test-fails-only-from-certain-locations"></a>Test kończy się niepowodzeniem tylko z niektórych lokalizacji

|Komunikat o symptomach/błędzie| Możliwe przyczyny|
|----|---------|
|Próba połączenia nie powiodła się, ponieważ połączona strona nie odpowiedziała prawidłowo po pewnym czasie  | Agenci testowi w niektórych lokalizacjach są blokowani przez zaporę.|
|    |Nakreślenie niektórych adresów IP odbywa się za pośrednictwem (moduły równoważenia obciążenia, menedżerowie ruchu geograficznego, trasa Azure Express). 
|    |W przypadku korzystania z usługi Azure ExpressRoute istnieją scenariusze, w których pakiety mogą być porzucone w przypadkach, gdy [występuje routing asymetryczny.](https://docs.microsoft.com/azure/expressroute/expressroute-asymmetric-routing)|

## <a name="test-failure-with-a-protocol-violation-error"></a>Błąd testu z błędem naruszenia protokołu

|Komunikat o symptomach/błędzie| Możliwe przyczyny| Możliwe uchwały |
|----|---------|-----|
|Serwer popełnił naruszenie protokołu. Section=ResponseHeader Detail=CR musi następować LF | Dzieje się tak, gdy wykryto nieprawidłowo sformułowane nagłówki. W szczególności niektóre nagłówki mogą nie używać CRLF do wskazywania końca wiersza, co narusza specyfikację HTTP. Usługa Application Insights wymusza tę specyfikację HTTP i kończy się niepowodzeniem odpowiedzi z nieprawidłowo sformułowanych nagłówków.| a. Skontaktuj się z dostawcą hosta witryny sieci Web / dostawcą CDN, aby naprawić wadliwe serwery. <br> b. W przypadku, gdy nieudane żądania są zasobami (np. plikami stylów, obrazami, skryptami), można rozważyć wyłączenie analizowania żądań zależnych. Pamiętaj, że jeśli to zrobisz, utracisz możliwość monitorowania dostępności tych plików).

> [!NOTE]
> Adres URL nie może zakończyć się niepowodzeniem w przeglądarkach, które mają zrelaksowaną sprawdzanie poprawności nagłówków HTTP. Zobacz ten wpis w blogu, aby uzyskać szczegółowe wyjaśnienie tego problemu: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

## <a name="common-troubleshooting-questions"></a>Typowe pytania dotyczące rozwiązywania problemów

### <a name="site-looks-okay-but-i-see-test-failures-why-is-application-insights-alerting-me"></a>Witryna wygląda prawidłowo, ale występują niepowodzenia testów Dlaczego usługa Application Insights ostrzega mnie?

   * Czy test ma włączone **żądania zależne od analizy?** Powoduje to ścisłe sprawdzenie zasobów, takich jak skrypty, obrazy itp. Tego typu błędy mogą nie być zauważalne w przeglądarce. Sprawdź wszystkie obrazy, skrypty, arkusze stylów i inne pliki ładowane przez stronę. Jeśli którykolwiek z nich nie powiedzie się, test jest zgłaszany jako nie powiódł się, nawet jeśli główna strona HTML ładuje się bez problemu. Aby odczuć test na takie błędy zasobów, po prostu odznacz żądania zależne analizowania z konfiguracji testu.

   * Aby zmniejszyć prawdopodobieństwo szumu z przejściowych blips sieci itp., upewnij się, włącz ponownych prób dla konfiguracji błędów testowych jest zaznaczone. Można również przetestować z większej liczby lokalizacji i odpowiednio zarządzać progiem reguły alertu, aby zapobiec problemom specyficznym dla lokalizacji powodującym nieuzasadnione alerty.

   * Kliknij dowolną czerwoną kropkę z środowiska dostępności lub awarii dostępności z Eksploratora wyszukiwania, aby zobaczyć szczegóły, dlaczego zgłosiliśmy błąd. Wynik testu wraz ze skorelowaną telemetrią po stronie serwera (jeśli jest włączona) powinien pomóc zrozumieć, dlaczego test nie powiódł się. Typowe przyczyny problemów przejściowych to problemy z siecią lub połączeniem.

   * Czy limit czasu testu? Przerywamy testy po 2 minutach. Jeśli ping lub test wieloetapowy trwa dłużej niż 2 minuty, zgłosimy to jako błąd. Należy rozważyć podział testu na wiele z nich, które można wykonać w krótszym czasie trwania.

   * Czy wszystkie lokalizacje zgłaszały awarię, czy tylko niektóre z nich? Jeśli tylko niektóre zgłoszone błędy, może to być spowodowane problemami z siecią/siecią CDN. Ponownie, kliknięcie na czerwone kropki powinno pomóc zrozumieć, dlaczego lokalizacja zgłosiła błędy.

### <a name="i-did-not-get-an-email-when-the-alert-triggered-or-resolved-or-both"></a>Nie otrzymałem wiadomości e-mail po wyzwoleniu alertu, rozwiązaniu lub obu?

Sprawdź klasyczną konfigurację alertów, aby potwierdzić, że wiadomość e-mail znajduje się bezpośrednio na liście, lub lista dystrybucyjna, na której się znajdujesz, jest skonfigurowana do odbierania powiadomień. Jeśli tak jest, sprawdź konfigurację listy dystrybucyjnej, aby potwierdzić, że może odbierać zewnętrzne wiadomości e-mail. Sprawdź również, czy administrator poczty może mieć skonfigurowane zasady, które mogą powodować ten problem.

### <a name="i-did-not-receive-the-webhook-notification"></a>Nie otrzymałem powiadomienia webhook?

Sprawdź, czy aplikacja odbierająca powiadomienie elementu webhook jest dostępna i pomyślnie przetwarza żądania elementu webhook. Zobacz [to,](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) aby uzyskać więcej informacji.

### <a name="intermittent-test-failure-with-a-protocol-violation-error"></a>Sporadyczne niepowodzenia testu z błędem naruszenia protokołu

Błąd „Naruszenie protokołu (...) Po CR musi występować LF” oznacza problem związany z serwerem (lub zależnościami). Występuje w przypadku ustawienia nieprawidłowo sformułowanych nagłówków w odpowiedzi. Przyczyną mogą być moduły równoważenia obciążenia lub sieci dostarczania zawartości. W szczególności niektóre nagłówki mogą nie używać CRLF do wskazywania końca wiersza, co narusza specyfikację HTTP i w związku z tym nie można sprawdzania poprawności na poziomie .NET WebRequest. Sprawdź odpowiedź na nagłówki punktowe, które mogą naruszać.

> [!NOTE]
> Adres URL nie może zakończyć się niepowodzeniem w przeglądarkach, które mają zrelaksowaną sprawdzanie poprawności nagłówków HTTP. Zobacz ten wpis w blogu, aby uzyskać szczegółowe wyjaśnienie tego problemu: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

### <a name="i-dont-see-any-related-server-side-telemetry-to-diagnose-test-failures"></a>Nie widzę żadnych powiązanych danych telemetrycznych po stronie serwera do diagnozowania błędów testów?*

Jeśli usługa Application Insights została skonfigurowana dla aplikacji po stronie serwera, może to być spowodowane trwaniem [próbkowania](../../azure-monitor/app/sampling.md). Wybierz inny wynik dostępności.

### <a name="can-i-call-code-from-my-web-test"></a>Czy mogę wywołać kod z mojego testu sieci Web?

Nie. Kroki testu muszą być zawarte w pliku .webtest. Nie można też wywoływać innych testów sieci Web ani używać pętli. Istnieje jednak kilka wtyczek, które mogą być przydatne.


### <a name="is-there-a-difference-between-web-tests-and-availability-tests"></a>Czym różnią się „testy sieci Web” i „testy dostępności”?

Te dwa terminy mogą być używane zamiennie. Testy dostępności to bardziej ogólny termin, który, oprócz wieloetapowych testów witryny, obejmuje testy ping pojedynczego adresu URL.

### <a name="id-like-to-use-availability-tests-on-our-internal-server-that-runs-behind-a-firewall"></a>Chcę użyć testów dostępności na naszym serwerze wewnętrznym działającym za zaporą.

   Istnieją dwa możliwe rozwiązania:

   * Skonfiguruj zaporę, aby zezwolić na żądania przychodzące z [adresów IP naszych agentów testów sieci Web](../../azure-monitor/app/ip-addresses.md).
   * Napisz własny kod do okresowego testowania wewnętrznego serwera. Uruchom kod jako proces w tle na serwerze testowym za zaporą. Proces testowania może wysyłać wyniki do usługi Application Insights za pomocą interfejsu API [TrackAvailability()](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) w podstawowym zestawie SDK. Wymaga to, aby serwer testowy miał dostęp do połączeń wychodzących punktu końcowego pozyskiwania usługi Application Insights, ale stanowi to dużo mniejsze zagrożenie bezpieczeństwa niż alternatywne dopuszczenie żądań przychodzących. Wyniki pojawią się w blokach testów sieci web dostępności, chociaż środowisko zostanie nieco uproszczone od tego, co jest dostępne dla testów utworzonych za pośrednictwem portalu. Niestandardowe testy dostępności będą również wyświetlane jako wyniki dostępności w Analytics, Search i Metrics.

### <a name="uploading-a-multi-step-web-test-fails"></a>Przekazywanie wieloetapowego testu sieci web kończy się niepowodzeniem.

Niektóre powody, dla których może się to zdarzyć:
   * Limit rozmiaru to 300 KB.
   * Pętle nie są obsługiwane.
   * Odwołania do innych testów sieci Web nie są obsługiwane.
   * Źródła danych nie są obsługiwane.

### <a name="my-multi-step-test-doesnt-complete"></a>Mój test wieloetapowy nie jest wykonywany w całości

Istnieje limit 100 żądań na test. Ponadto test jest zatrzymany, jeśli działa dłużej niż dwie minuty.

### <a name="how-can-i-run-a-test-with-client-certificates"></a>Jak uruchomić test z wykorzystaniem certyfikatów klienta?

Obecnie nie jest to obsługiwane.

## <a name="who-receives-the-classic-alert-notifications"></a>Kto otrzymuje (klasyczne) powiadomienia o alertach?

Ta sekcja dotyczy tylko alertów klasycznych i pomoże ci zoptymalizować powiadomienia o alertach, aby upewnić się, że tylko pożądani odbiorcy otrzymują powiadomienia. Aby dowiedzieć się więcej o różnicy między [klasycznymi alertami](../platform/alerts-classic.overview.md)a nowym działaniem alertów, zapoznaj się z [omówieniem alertów w artykule](../platform/alerts-overview.md). Aby kontrolować powiadomienia o alertach w nowych alertach, użyj [grup akcji](../platform/action-groups.md).

* Zalecamy używanie określonych adresatów do klasycznych powiadomień o alertach.

* W przypadku alertów o awariach z lokalizacji X poza lokalizacjami Y opcja pola wyboru **zbiorczo/grupowa,** jeśli jest włączona, wysyła do użytkowników z rolami administratora/współadministratora.  Zasadniczo _wszyscy_ administratorzy _subskrypcji_ otrzymają powiadomienia.

* W przypadku alertów dotyczących dostępności metryki opcja pola wyboru **zbiorczo/grupowa,** jeśli jest włączona, wysyła do użytkowników z rolami właściciela, współautora lub czytelnika w subskrypcji. W efekcie _wszyscy_ użytkownicy z dostępem do subskrypcji zasobów usługi Application Insights są w zakresie i będą otrzymywać powiadomienia. 

> [!NOTE]
> Jeśli obecnie używasz opcji pola wyboru **zbiorczo/grupowego** i wyłączysz ją, nie będzie można przywrócić zmiany.

Użyj nowego środowiska alertów/alertów w czasie zbliżonym do rzeczywistego, jeśli chcesz powiadomić użytkowników na podstawie ich ról. W [przypadku grup akcji](../platform/action-groups.md)można skonfigurować powiadomienia e-mail dla użytkowników z dowolną rolą współautora/właściciela/czytelnika (nie łączoną razem jako pojedyncza opcja).

## <a name="next-steps"></a>Następne kroki

* [Wieloetapowe testowanie stron internetowych](availability-multistep.md)
* [Testy ping adresu URL](monitor-web-app-availability.md)
