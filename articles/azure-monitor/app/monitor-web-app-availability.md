---
title: Monitorowanie dostępności i czasu odpowiedzi dowolnej witryny sieci Web | Microsoft Docs
description: Konfigurowanie testów sieci Web w usłudze Application Insights. Otrzymywanie alertów, kiedy witryna sieci Web staje się niedostępna lub wolno odpowiada.
ms.topic: conceptual
ms.date: 09/16/2019
ms.reviewer: sdash
ms.openlocfilehash: 61358051a8ddc32bc01ec5e231f4c28ebfa18ee0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670036"
---
# <a name="monitor-the-availability-of-any-website"></a>Monitorowanie dostępności dowolnej strony internetowej

Po wdrożeniu aplikacji internetowej/witryny sieci Web można skonfigurować testy cykliczne w celu monitorowania dostępności i czasu reakcji. Usługa [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) wysyła żądania sieci Web do aplikacji w regularnych odstępach czasu z punktów na całym świecie. Może ostrzegać, jeśli aplikacja nie odpowiada lub jeśli reaguje zbyt wolno.

Testy dostępności możesz skonfigurować dla dowolnego punktu końcowego protokołów HTTP lub HTTPS, który jest dostępny za pośrednictwem publicznej sieci Internet. Nie musisz wprowadzać żadnych zmian w testującej witrynie. W rzeczywistości nie musi to być nawet strona, którą posiadasz. Można przetestować dostępność interfejsu API REST, od którego zależy usługa.

### <a name="types-of-availability-tests"></a>Rodzaje testów dostępności:

Istnieją trzy typy testów dostępności:

* [Test ping adresu URL](#create-a-url-ping-test): prosty test, który można utworzyć w portalu Azure.
* [Wieloetapowy test internetowy:](availability-multistep.md)nagrywanie sekwencji żądań sieci web, które można odtwarzać w celu przetestowania bardziej złożonych scenariuszy. Wieloetapowe testy sieci web są tworzone w programie Visual Studio Enterprise i przekazywane do portalu do wykonania.
* [Testy dostępności ścieżki niestandardowej:](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability?view=azure-dotnet)Jeśli zdecydujesz się utworzyć niestandardową `TrackAvailability()` aplikację do uruchamiania testów dostępności, metoda może służyć do wysyłania wyników do usługi Application Insights.

**Można utworzyć maksymalnie 100 testów dostępności dla zasobów usługi Application Insights.**

## <a name="create-an-application-insights-resource"></a>Tworzenie zasobu usługi Application Insights

Aby utworzyć test dostępności, należy najpierw utworzyć zasób usługi Application Insights. Jeśli zasób został już utworzony, przejdź do następnej sekcji, aby [utworzyć test ping adresu URL](#create-a-url-ping-test).

W witrynie Azure Portal wybierz pozycję **Utwórz zasób—** > **usługa** > **Application Insights** i [utwórz zasób usługi Application Insights](create-new-resource.md).

## <a name="create-a-url-ping-test"></a>Tworzenie testu ping adresu URL

Nazwa "test ping adresu URL" jest trochę mylące. Aby było jasne, ten test nie korzysta z protokołu ICMP (Internet Control Message Protocol) w celu sprawdzenia dostępności witryny. Zamiast tego używa bardziej zaawansowanych funkcji żądania HTTP, aby sprawdzić, czy punkt końcowy odpowiada. Mierzy również wydajność skojarzoną z tą odpowiedzią i dodaje możliwość ustawiania niestandardowych kryteriów sukcesu w połączeniu z bardziej zaawansowanymi funkcjami, takimi jak analizowanie żądań zależnych i zezwalaniem na ponownych prób.

Aby utworzyć pierwsze żądanie dostępności, otwórz okienko Dostępność i wybierz pozycję **Utwórz test**.

![Podaj przynajmniej adres URL swojej witryny sieci Web](./media/monitor-web-app-availability/availability-create-test-001.png)

### <a name="create-a-test"></a>Tworzenie testu

|Ustawienie| Wyjaśnienie
|----|----|----|
|**Adres URL** |  Adres URL może odnosić się do dowolnej strony sieci Web, którą chcesz przetestować, ale musi być widoczny w publicznym Internecie. Adres URL może zawierać ciąg zapytania. Możesz więc np. szybko sprawdzić działanie bazy danych. Jeśli adres URL jest rozpoznawany jako przekierowanie, zostanie prześledzonych maksymalnie 10 przekierowań.|
|**Analizuj żądania zależne**| Test żąda obrazów, skryptów, plików stylów i innych plików, które są częścią testowej strony sieci Web. Rejestrowany czas odpowiedzi obejmuje czas poświęcony na pobieranie tych plików. Test zakończy się niepowodzeniem, jeśli którykolwiek z tych zasobów nie można pomyślnie pobrać w limit czasu dla całego testu. Jeśli pole opcji nie zostanie zaznaczone, test zażąda tylko pliku pod podanym adresem URL. Włączenie tej opcji powoduje bardziej rygorystyczne sprawdzanie. Test może zakończyć się niepowodzeniem w przypadkach, które mogą nie być zauważalne podczas ręcznego przeglądania witryny.
|**Włączanie ponownych prób**|gdy test zakończy się niepowodzeniem, jest ponawiany po krótkim odstępie czasu. Błąd jest zgłaszany dopiero wtedy, gdy trzy kolejne próby się nie powiodą. Kolejne testy są następnie wykonywane ze zwykłą częstotliwością. Ponawianie prób jest tymczasowo wstrzymane do czasu następnego sukcesu. Ta reguła jest stosowana niezależnie w każdej lokalizacji testu. **Zalecamy tę opcję**. Średnio około 80% błędów znika po ponowieniu testu.|
|**Częstotliwość badania**| Ustawia, jak często test jest uruchamiany z każdej lokalizacji testowej. Przy domyślnej częstotliwości równej 5 minut i 5 lokalizacjach testu witryna będzie testowana średnio co minutę.|
|**Lokalizacje testowe**| Czy miejsca, z których nasze serwery wysyłają żądania internetowe do Twojego adresu URL. **Nasza minimalna liczba zalecanych lokalizacji testowych wynosi pięć,** aby zapewnić, że można odróżnić problemy w witrynie od problemów z siecią. Wybrać można maksymalnie 16 lokalizacji.

**Jeśli adres URL nie jest widoczny z publicznego Internetu, możesz wybrać selektywne otwarcie zapory, aby zezwolić tylko na transakcje testowe za pośrednictwem**programu . Aby dowiedzieć się więcej o wyjątkach zapory dla naszych agentów testowych dostępności, zapoznaj się z [przewodnikiem po adresach IP](https://docs.microsoft.com/azure/azure-monitor/app/ip-addresses#availability-tests).

> [!NOTE]
> Zdecydowanie zalecamy testowanie z wielu lokalizacji z **co najmniej pięcioma lokalizacjami.** Ma to na celu zapobieganie fałszywym alarmom, które mogą wynikać z przejściowych problemów z określoną lokalizacją. Ponadto odkryliśmy, że optymalna konfiguracja ma mieć **liczbę lokalizacji testowych na równi z progiem lokalizacji alertu + 2**.

### <a name="success-criteria"></a>Kryteria sukcesu

|Ustawienie| Wyjaśnienie
|----|----|----|
| **Limit czasu testowania** |Zmniejsz tę wartość, aby otrzymywać alerty o powolnych odpowiedziach. Test jest uznawany za błąd, jeśli w tym okresie nie odebrano odpowiedzi z witryny. W przypadku wybrania opcji **Analizuj zależne żądania** wszystkie obrazy, pliki stylów, skrypty i inne zasoby zależne muszą zostać odebrane w tym okresie.|
| **Odpowiedź HTTP** | Zwrócony kod stanu, który jest liczony jako sukces. Kod 200 oznacza, że została zwrócona normalna strona sieci Web.|
| **Dopasowanie zawartości** | Ciąg, jak "Witamy!" Sprawdzamy, czy w każdej odpowiedzi występuje dokładna zgodność pod względem wielkości liter. Musi to być zwykły ciąg znaków bez symboli wieloznacznych. Pamiętaj, że w razie zmiany zawartości strony może być konieczne zaktualizowanie tego ciągu. **Tylko znaki angielskie są obsługiwane przez dopasowanie zawartości** |

### <a name="alerts"></a>Alerty

|Ustawienie| Wyjaśnienie
|----|----|----|
|**W czasie zbliżonym do rzeczywistego (wersja zapoznawcza)** | Zalecamy korzystanie z alertów w czasie rzeczywistym. Konfigurowanie tego typu alertów odbywa się po utworzeniu testu dostępności.  |
|**Wdrożenie klasyczne** | Nie zaleca się już używania klasycznych alertów dla nowych testów dostępności.|
|**Próg lokalizacji alertu**|Zalecamy co najmniej 3/5 lokalizacji. Optymalna relacja między wartością progową lokalizacji alertu a liczbą lokalizacji testowych jest progowa liczba **lokalizacji** = **alertów lokalizacji testowych — 2, z co najmniej pięcioma lokalizacjami testowymi.**|

## <a name="see-your-availability-test-results"></a>Wyświetlanie wyników testów dostępności

Wyniki testów dostępności można wizualizować zarówno za pomocą widoków wykresu liniowego, jak i punktowego.

Po kilku minutach kliknij przycisk **Odśwież,** aby wyświetlić wyniki testów.

![Widok wiersza](./media/monitor-web-app-availability/availability-refresh-002.png)

Widok scatterplot pokazuje próbki wyników badań, które mają szczegóły testu diagnostycznego w nich. Aparat testowy przechowuje szczegółowe informacje diagnostyczne dla testów z błędami. W przypadku udanych testów szczegółowe informacje diagnostyczne są przechowywane dla podzbioru wykonań. Umieść wskaźnik myszy na dowolnej zielonej/czerwonej kropki, aby zobaczyć test, nazwę testu i lokalizację.

![Widok wiersza](./media/monitor-web-app-availability/availability-scatter-plot-003.png)

Wybierz określony test, lokalizację lub skróć okres czasu, aby zobaczyć więcej wyników w okolicy interesującego okresu czasu. Użyj Eksploratora wyszukiwania, aby zobaczyć wyniki z wszystkich wykonań, lub użyj zapytań analitycznych w celu uruchomienia niestandardowych raportów dla tych danych.

## <a name="inspect-and-edit-tests"></a> Sprawdzanie i edytowanie testów

Aby edytować, tymczasowo wyłączyć lub usunąć test kliknij wielokropek obok nazwy testu. Propagowanie zmian konfiguracji do wszystkich agentów testowych po dokonaniu zmiany może potrwać do 20 minut.

![Wyświetl szczegóły testu. Edytowanie i wyłączanie testu sieci Web](./media/monitor-web-app-availability/edit.png)

Podczas przeprowadzania konserwacji swojej usługi możesz wyłączyć testy dostępności lub skojarzone z nimi reguły alertów.

## <a name="if-you-see-failures"></a>Jeśli widzisz błędy

Kliknij czerwoną kropkę.

![Kliknij czerwoną kropkę](./media/monitor-web-app-availability/open-instance-3.png)

Z wyniku testu dostępności można wyświetlić szczegóły transakcji we wszystkich składnikach. Tutaj możesz:

* Zbadać odpowiedź odebraną z serwera.
* Diagnozowanie awarii za pomocą skorelowanych danych telemetrycznych po stronie serwera zebranych podczas przetwarzania testu dostępności nie powiodło się.
* Zarejestruj problem lub element roboczy w usłudze Git lub Azure Boards, aby śledzić problem. Błąd będzie zawierać link do tego zdarzenia.
* Otworzyć wynik testu sieci Web w programie Visual Studio.

Dowiedz się więcej o diagnostyce transakcji od końca do końca [tutaj](../../azure-monitor/app/transaction-diagnostics.md).

Kliknij wiersz wyjątku, aby wyświetlić szczegóły wyjątku po stronie serwera, który spowodował niepowodzenie testu dostępności syntetycznych. Można również uzyskać [migawkę debugowania](../../azure-monitor/app/snapshot-debugger.md) dla bogatszych diagnostyki na poziomie kodu.

![Diagnostyka po stronie serwera](./media/monitor-web-app-availability/open-instance-4.png)

Oprócz wyników pierwotnych można również wyświetlić dwa kluczowe dane dostępności w [Eksploratorze metryk:](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)

1. Dostępność: procent testów, które zostały pomyślnie zakończone, dla wszystkich wykonań testów.
2. Czas trwania testu: średni czas trwania testu dla wszystkich wykonań testów.

## <a name="automation"></a>Automatyzacja

* Automatyczne [konfigurowanie testów dostępności za pomocą skryptów środowiska PowerShell](../../azure-monitor/app/powershell.md#add-an-availability-test).
* Konfigurowanie [elementu webhook](../../azure-monitor/platform/alerts-webhooks.md) który jest wywoływany przy zgłaszaniu alertu.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Dedykowany [artykuł dotyczący rozwiązywania problemów](troubleshoot-availability.md).

## <a name="next-steps"></a>Następne kroki

* [Alerty o dostępności](availability-alerts.md)
* [Wieloetapowe testy sieci Web](availability-multistep.md)


