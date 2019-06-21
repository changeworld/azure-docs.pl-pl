---
title: Rozwiązywanie problemów z testów dostępności usługi Azure Application Insights | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów z testów sieci web w usłudze Azure Application Insights. Otrzymywanie alertów, kiedy witryna sieci Web staje się niedostępna lub wolno odpowiada.
services: application-insights
documentationcenter: ''
author: lgayhardt
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.author: lagayhar
ms.openlocfilehash: 87bc87d7d105d581f0143e87044fb0337c0fd7f6
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305097"
---
# <a name="troubleshooting"></a>Rozwiązywanie problemów

Ten artykuł pomoże rozwiązać typowe problemy, które mogą wystąpić, gdy za pomocą funkcji monitorowania dostępności.

## <a name="ssltls-errors"></a>Błędy protokołu SSL/TLS

|Objaw/komunikat o błędzie| Możliwe przyczyny|
|--------|------|
|Nie można utworzyć bezpiecznego kanału SSL/TLS  | Wersja protokołu SSL. Obsługiwane są tylko protokołu TLS 1.0, 1.1 i 1.2. **Protokół SSLv3 nie jest obsługiwane.**
|TLSv1.2 Record Layer: Alert (poziom: Błąd krytyczny, opis: Nieprawidłowy rekord MAC)| Zobacz StackExchange wątku dla [informacji](https://security.stackexchange.com/questions/39844/getting-ssl-alert-write-fatal-bad-record-mac-during-openssl-handshake).
|Adres URL, którego nie ma sieci CDN (Content Delivery Network) | Może to być spowodowane błędną konfiguracją w sieci CDN |  

### <a name="possible-workaround"></a>Możliwym obejściem

* Jeśli adresy URL, na których występuje problem zawsze mają zasoby zależne, zalecane jest wyłączenie **Analizuj zależne żądania** testu sieci web.

## <a name="test-fails-only-from-certain-locations"></a>Test nie powiedzie się tylko z określonych lokalizacji

|Objaw/komunikat o błędzie| Możliwe przyczyny|
|----|---------|
|Próba połączenia nie powiodło się, ponieważ strona połączona nie odpowiedziała poprawnie po określonym czasie  | Agenci testowi w określonych lokalizacjach są blokowane przez zaporę.|
|    |Przekierowywanie niektórych adresów IP odbywa się za pośrednictwem (usługi równoważenia obciążenia, Menedżery geograficznie traffic Manager, Azure Express Route.) 
|    |Jeśli przy użyciu usługi Azure ExpressRoute, istnieją scenariusze, w której można porzuconych pakietów w przypadkach, gdzie [Routing asymetryczny występuje](https://docs.microsoft.com/azure/expressroute/expressroute-asymmetric-routing).|

## <a name="intermittent-test-failure-with-a-protocol-violation-error"></a>Sporadyczne niepowodzenia testu z powodu błędu naruszenia protokołu

|Objaw/komunikat o błędzie| Możliwe przyczyny|
|----|---------|
Protokół naruszenie CR musi występować LF | Dzieje się tak, gdy źle sformułowane nagłówki są wykrywane. W szczególności niektóre nagłówki mogą nie być sygnalizowany znakiem CRLF do wskazania końca wiersza, co narusza specyfikację protokołu HTTP i w związku z tym zakończy się niepowodzeniem walidacji na poziomie żądania internetowego .NET WebRequest.
 || Może to również być spowodowane modułów równoważenia obciążenia lub sieci dostarczania zawartości.

> [!NOTE]
> Adres URL może nie zakończyć się niepowodzeniem w przeglądarkach mających bardziej swobodne reguły walidacji nagłówków HTTP. Zobacz ten wpis w blogu, aby uzyskać szczegółowe wyjaśnienie tego problemu: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

## <a name="common-troubleshooting-questions"></a>Częste pytania dotyczące rozwiązywania problemów

### <a name="site-looks-okay-but-i-see-test-failures-why-is-application-insights-alerting-me"></a>Witryna wygląda prawidłowo, ale występują niepowodzenia testów? Dlaczego usługa Application Insights jest ze mną alertów?

   * Ma test **Analizuj zależne żądania** włączone? Który skutkuje rygorystyczne kontrole zasoby, takie jak skrypty, obrazy itd. Tego rodzaju błędów może nie być widoczne w przeglądarce. Sprawdź wszystkie obrazy, skrypty, arkusze stylów i inne pliki ładowane przez stronę. Jeśli któryś z nich nie powiedzie się, test jest zgłoszony jako nieudany — nawet wtedy, gdy główna strona HTML ładuje się bez problemu. Aby desensitize test, aby awarie tych zasobów, usuń zaznaczenie pola analizy żądania zależne od konfiguracji testu.

   * Aby zmniejszyć ryzyko wystąpienia szumu z blips przejściowe problemy z siecią itp., upewnij się, Włącz ponawianie próby w przypadku niepowodzenia testów, które w konfiguracji. Można również testy z większej liczby lokalizacji i odpowiednio Zarządzanie próg reguły alertu, aby uniknąć niepotrzebnych alertów przez problemy specyficzne dla lokalizacji.

   * Kliknij dowolny czerwonych kropek doświadczeniu dostępności lub jakiekolwiek niepowodzenie dostępności Eksploratora wyszukiwania, aby wyświetlić szczegóły Dlaczego mamy zgłosił błąd. Wynik testu, wraz z skorelowana telemetria po stronie serwera (jeśli jest włączona) powinno pomóc zrozumieć, dlaczego test nie powiódł się. Typowe przyczyny problemy przejściowe problemy z połączeniem sieciowym lub.

   * Czy limit czasu testu? Firma Microsoft przerwać testów po 2 minuty. Jeśli Twoje polecenie ping lub test wieloetapowy trwa dłużej niż 2 minuty, firma Microsoft będzie zgłaszać jako błąd. Rozważ podzielenie testu na wiele migawek, które można wykonać w krótszych czasów trwania.

   * Wszystkie lokalizacje zgłosili awarii lub tylko niektóre z nich? Jeśli tylko niektóre zgłoszone błędy, może to być spowodowane problemy sieciowe/usługa CDN. Ponownie klikając czerwonych kropek powinna pomagać zrozumieć, dlaczego lokalizacji zgłoszone błędy.

### <a name="i-did-not-get-an-email-when-the-alert-triggered-or-resolved-or-both"></a>Nie pobrały I wiadomości e-mail, gdy alertu wyzwolona lub rozwiązane lub oba?

Sprawdź konfigurację alertów klasycznych, aby potwierdzić swój adres e-mail znajduje się bezpośrednio lub listy dystrybucyjnej, które znajdują się na jest skonfigurowany do otrzymywać powiadomienia. Jeśli tak jest, sprawdź konfigurację listy dystrybucji, aby potwierdzić, że może ona odbierać wiadomości e-mail zewnętrznych. Również szybko sprawdzić, jeśli administrator poczty może mieć żadnych zasady skonfigurowane, które mogą być przyczyną tego problemu.

### <a name="i-did-not-receive-the-webhook-notification"></a>Nie odebrał powiadomienie elementów webhook?

Sprawdź aplikację odbieranie powiadomień elementu webhook jest dostępny i pomyślnie przetwarza żądania elementu webhook. Zobacz [to](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) Aby uzyskać więcej informacji.

### <a name="intermittent-test-failure-with-a-protocol-violation-error"></a>Sporadyczne niepowodzenia testu z powodu błędu naruszenia protokołu?

Błąd „Naruszenie protokołu (...) Po CR musi występować LF” oznacza problem związany z serwerem (lub zależnościami). Występuje w przypadku ustawienia nieprawidłowo sformułowanych nagłówków w odpowiedzi. Przyczyną mogą być moduły równoważenia obciążenia lub sieci dostarczania zawartości. W szczególności niektóre nagłówki mogą nie być sygnalizowany znakiem CRLF do wskazania końca wiersza, co narusza specyfikację protokołu HTTP i w związku z tym niepowodzenia walidacji na poziomie żądania internetowego .NET WebRequest. Sprawdź odpowiedź, aby znaleźć nagłówki, które mogą powodować naruszenie.

> [!NOTE]
> Adres URL może nie zakończyć się niepowodzeniem w przeglądarkach mających bardziej swobodne reguły walidacji nagłówków HTTP. Zobacz ten wpis w blogu, aby uzyskać szczegółowe wyjaśnienie tego problemu: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

### <a name="i-dont-see-any-related-server-side-telemetry-to-diagnose-test-failures"></a>Nie widzisz powiązanych telemetrii po stronie serwera do diagnozowania niepowodzeń testów? *

Jeśli usługa Application Insights została skonfigurowana dla aplikacji po stronie serwera, może to być spowodowane trwaniem [próbkowania](../../azure-monitor/app/sampling.md). Wybierz wynik w różnych dostępności.

### <a name="can-i-call-code-from-my-web-test"></a>Czy mogę wywołać kod z mojego testu sieci Web?

Nie. Kroki testu muszą być zawarte w pliku .webtest. Nie można też wywoływać innych testów sieci Web ani używać pętli. Istnieje jednak kilka wtyczek, które mogą być przydatne.


### <a name="is-there-a-difference-between-web-tests-and-availability-tests"></a>Czym różnią się „testy sieci Web” i „testy dostępności”?

Te dwa terminy mogą być używane zamiennie. Testy dostępności to bardziej ogólny termin, który, oprócz wieloetapowych testów witryny, obejmuje testy ping pojedynczego adresu URL.

### <a name="id-like-to-use-availability-tests-on-our-internal-server-that-runs-behind-a-firewall"></a>Chcę użyć testów dostępności na naszym serwerze wewnętrznym działającym za zaporą.

   Istnieją dwa możliwe rozwiązania:

   * Skonfiguruj zaporę, aby zezwolić na żądania przychodzące z [adresów IP naszych agentów testów sieci Web](../../azure-monitor/app/ip-addresses.md).
   * Napisz własny kod do okresowego testowania wewnętrznego serwera. Uruchom kod jako proces w tle na serwerze testowym za zaporą. Proces testowania może wysyłać wyniki do usługi Application Insights za pomocą interfejsu API [TrackAvailability()](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) w podstawowym zestawie SDK. Wymaga to, aby serwer testowy miał dostęp do połączeń wychodzących punktu końcowego pozyskiwania usługi Application Insights, ale stanowi to dużo mniejsze zagrożenie bezpieczeństwa niż alternatywne dopuszczenie żądań przychodzących. Wyniki nie będą widoczne w blokach testów dostępności sieci Web, ale są wyświetlane jako wyniki dostępności w obszarach analizy, wyszukiwania i eksploratora metryk.

### <a name="uploading-a-multi-step-web-test-fails"></a>Przekazywanie wieloetapowego testu sieci web kończy się niepowodzeniem.

Niektóre przyczyny może się to zdarzyć:
   * Limit rozmiaru to 300 KB.
   * Pętle nie są obsługiwane.
   * Odwołania do innych testów sieci Web nie są obsługiwane.
   * Źródła danych nie są obsługiwane.

### <a name="my-multi-step-test-doesnt-complete"></a>Mój test wieloetapowy nie jest wykonywany w całości

Istnieje limit 100 żądań na test. Ponadto test zostanie zatrzymany, jeśli działa dłużej niż dwie minuty.

### <a name="how-can-i-run-a-test-with-client-certificates"></a>Jak uruchomić test z wykorzystaniem certyfikatów klienta?

To nie jest obecnie obsługiwane.

## <a name="who-receives-the-classic-alert-notifications"></a>Kto otrzymuje powiadomienia o alertach (model klasyczny)?

W tej sekcji dotyczą alertów klasycznych i tylko pomoże Ci zoptymalizować swoje powiadomień o alertach, aby upewnić się, że tylko przez adresatów żądaną otrzymywać powiadomienia. Aby dowiedzieć się więcej o różnicach między [alertów klasycznych](../platform/alerts-classic.overview.md)i nowego środowiska alertów odnoszą się do [artykuł z omówieniem alerty](../platform/alerts-overview.md). Do kontrolowania alert powiadomienia w nowych alertów środowiska użyj [grup akcji](../platform/action-groups.md).

* Firma Microsoft zaleca użycie określonych adresatów klasycznego powiadomień o alertach.

* Dla alertów dotyczących błędów z X z Y lokalizacji **zbiorcze/grupę** pole wyboru opcji, jeśli włączona, wysyła do użytkowników przy użyciu ról Administrator/współadministrator.  Zasadniczo _wszystkich_ Administratorzy _subskrypcji_ będą otrzymywać powiadomienia.

* Dla alertów dotyczących metryk dostępności **zbiorcze/grupę** pole wyboru opcji, jeśli włączona, wysyła do użytkowników przy użyciu właściciela, współautora lub czytelnika ról w ramach subskrypcji. W efekcie _wszystkich_ użytkowników z dostępem do subskrypcji zasobu usługi Application Insights znajdują się w zakresie i będą otrzymywać powiadomienia. 

> [!NOTE]
> Jeśli obecnie używasz **zbiorcze/grupę** pole wyboru opcji i go wyłączyć, nie można przywrócić zmianę.

Jeśli chcesz powiadomić użytkowników na podstawie ich ról, należy użyć nowe alerty środowisko/niemal w czasie rzeczywistym. Za pomocą [grup akcji](../platform/action-groups.md), można skonfigurować powiadomienia e-mail do użytkowników z dowolną rolę właściciel/Współautor/reader (nie łączyć ze sobą jako pojedyncza opcja).

## <a name="next-steps"></a>Kolejne kroki

* [Testowanie sieci web z wieloma krokami](availability-multistep.md)
* [Testów ping adresu URL](monitor-web-app-availability.md)
