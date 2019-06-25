---
title: Monitorowanie dostępności i czasu odpowiedzi dowolnej witryny sieci Web | Microsoft Docs
description: Konfigurowanie testów sieci Web w usłudze Application Insights. Otrzymywanie alertów, kiedy witryna sieci Web staje się niedostępna lub wolno odpowiada.
services: application-insights
author: mrbullwinkle
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: abe55007aa8a8719d6b6f1659e00a089a2e28771
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303795"
---
# <a name="monitor-the-availability-of-any-website"></a>Monitorowanie dostępności dowolnej witrynie sieci Web

Po wdrożeniu usługi sieci web aplikacji/witryny sieci Web, skonfigurowaniem cyklicznego testów monitorowania dostępności i czasu odpowiedzi. Usługa [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) wysyła żądania sieci Web do aplikacji w regularnych odstępach czasu z punktów na całym świecie. Go może generować alerty, jeśli aplikacja nie odpowiada lub zbyt wolno odpowiada.

Testy dostępności możesz skonfigurować dla dowolnego punktu końcowego protokołów HTTP lub HTTPS, który jest dostępny za pośrednictwem publicznej sieci Internet. Nie trzeba wprowadzać zmian w witrynie sieci Web, którą testujesz. W rzeczywistości nie nawet musi on być lokacji, których jesteś właścicielem. Można sprawdzić dostępność interfejsu API REST, który zależy od usługi.

### <a name="types-of-availability-tests"></a>Rodzaje testów dostępności:

Istnieją trzy rodzaje testów dostępności:

* [Test ping adresu URL](#create-a-url-ping-test): prosty test, który można utworzyć w portalu Azure.
* [Wieloetapowy test sieci web](availability-multistep.md): Rejestrowanie sekwencję żądania sieci web, które mogą być odtwarzane do testowania bardziej złożonych scenariuszy. Wieloetapowe testy sieci web są tworzone w programie Visual Studio Enterprise i przekazywane do portalu w celu wykonania.
* [Testy dostępności niestandardowe śledzenie](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability?view=azure-dotnet): `TrackAvailability()` Metody umożliwiają tworzenie testów dostępności niestandardowych.

**Można utworzyć maksymalnie 100 testów dostępności na zasób usługi Application Insights.**

## <a name="create-an-application-insights-resource"></a>Tworzenie zasobu usługi Application Insights

Aby utworzyć test dostępności, najpierw musisz utworzyć zasób usługi Application Insights. Jeśli już utworzono zasób, przejdź do następnej sekcji, aby [tworzenie testu Ping adresu URL](#create-a-url-ping-test).

W witrynie Azure portal, wybierz **Utwórz zasób** > **narzędzi deweloperskich** > **usługi Application Insights** i [tworzenie Zasób usługi Application Insights](create-new-resource.md).

## <a name="create-a-url-ping-test"></a>Tworzenie testu ping adresu URL

Nazwa "testu ping adresu URL" jest nieco misnomer. Aby być niejasne, ten test nie robi każde użycie protokołu ICMP (Internet Control Message Protocol) do sprawdzania dostępności witryny. Zamiast tego używa bardziej zaawansowanych funkcji żądania HTTP, aby sprawdzić, czy punkt końcowy odpowiada. Także pomiaru wydajności skojarzone z tym odpowiedzi i dodaje możliwość ustawiania kryteria sukcesu niestandardowe, w połączeniu z bardziej zaawansowane funkcje, takie jak analizy zależne żądania i umożliwiając ponownych prób.

Aby utworzyć pierwszego żądania dostępności, otwórz okienko dostępności, a następnie wybierz **utworzyć Test**.

![Podaj przynajmniej adres URL swojej witryny sieci Web](./media/monitor-web-app-availability/availability-create-test-001.png)

### <a name="create-a-test"></a>Tworzenie testu

|Ustawienie| Wyjaśnienie
|----|----|----|
|**Adres URL** |  Adres URL może być dowolnej strony sieci web, którą chcesz przetestować, ale musi być widoczny z publicznej sieci internet. Adres URL może zawierać ciąg zapytania. Możesz więc np. szybko sprawdzić działanie bazy danych. Jeśli adres URL jest rozpoznawany jako przekierowanie, zostanie prześledzonych maksymalnie 10 przekierowań.|
|**Analizuj zależne żądania**| Test zażąda obrazów, skryptów, plików stylów i inne pliki, które są częścią testowanej strony sieci web. Rejestrowany czas odpowiedzi obejmuje czas poświęcony na pobieranie tych plików. Test kończy się niepowodzeniem, jeśli dowolny z tych zasobów nie uda się pobrać przed upływem limitu czasu dla całego testu. Jeśli pole opcji nie zostanie zaznaczone, test zażąda tylko pliku pod podanym adresem URL. Włączenie tej opcji spowoduje w bardziej rygorystyczne wyboru. Test może nie działać w sytuacjach, które mogą nie być widoczne podczas przeglądania ręcznie w witrynie.
|**Włącz ponawianie próby**|gdy test zakończy się niepowodzeniem, zostanie ponowiony po krótkim czasie. Błąd jest zgłaszany dopiero wtedy, gdy trzy kolejne próby się nie powiodą. Kolejne testy są następnie wykonywane ze zwykłą częstotliwością. Ponawianie prób jest tymczasowo wstrzymane do czasu następnego sukcesu. Ta reguła jest stosowana niezależnie w każdej lokalizacji testu. **Wybranie tej opcji zalecane**. Średnio około 80% błędów znika po ponowieniu testu.|
|**Częstotliwość testów**| Określa, jak często wykonywany jest test w poszczególnych lokalizacjach testowych. Przy domyślnej częstotliwości równej 5 minut i 5 lokalizacjach testu witryna będzie testowana średnio co minutę.|
|**Lokalizacje testu**| Są to miejsca, z których nasze serwery wysyłają żądania sieci web do adresu URL. **Nasze minimalna liczba lokalizacji testowych zalecane jest pięć** aby upewnić się, że móc odróżnić problemy z witryną od problemów z siecią. Wybrać można maksymalnie 16 lokalizacji.

**Jeśli adres URL nie jest widoczny z publicznej sieci internet, można selektywnie otwierania zapory internetowej dozwolone są tylko transakcje testu za pomocą**. Aby dowiedzieć się więcej na temat wyjątków zapory dla naszych agentów testów dostępności, zapoznaj się z [przewodnik adresu IP](https://docs.microsoft.com/azure/azure-monitor/app/ip-addresses#availability-tests).

> [!NOTE]
> Zdecydowanie zaleca się testowania z wielu lokalizacji za pomocą **co najmniej pięciu lokalizacjach**. Ten parametr zapobiega fałszywych alarmów, które mogą wynikać z przejściowych problemów z określonej lokalizacji. Ponadto znaleźliśmy optymalną konfigurację ma mieć **liczba lokalizacji testowych równa próg lokalizacji alertu + 2**.

### <a name="success-criteria"></a>Kryteria powodzenia

|Ustawienie| Wyjaśnienie
|----|----|----|
| **Limit czasu testu** |Zmniejsz tę wartość, aby otrzymywać alerty o powolnych odpowiedziach. Test jest uznawany za błąd, jeśli w tym okresie nie odebrano odpowiedzi z witryny. W przypadku wybrania opcji **Analizuj zależne żądania** wszystkie obrazy, pliki stylów, skrypty i inne zasoby zależne muszą zostać odebrane w tym okresie.|
| **Odpowiedź HTTP** | Zwrócony kod stanu, który jest uznawany za sukces. Kod 200 oznacza, że została zwrócona normalna strona sieci Web.|
| **Dopasowania zawartości** | Ciąg, np. "Witaj!" Sprawdzamy, czy w każdej odpowiedzi występuje dokładna zgodność pod względem wielkości liter. Musi to być zwykły ciąg znaków bez symboli wieloznacznych. Pamiętaj, że w razie zmiany zawartości strony może być konieczne zaktualizowanie tego ciągu. **Obsługiwane są wyłącznie znaki alfabetu angielskiego z dopasowania zawartości** |

### <a name="alerts"></a>Alerty

|Ustawienie| Wyjaśnienie
|----|----|----|
|**Niemal w czasie rzeczywistym (wersja zapoznawcza)** | Firma Microsoft zaleca korzystanie z alertów w czasie zbliżonym do rzeczywistego. Konfigurowanie tego rodzaju alert odbywa się po utworzeniu testu dostępności.  |
|**Wdrożenie klasyczne** | Nie zaleca się przy użyciu alertów klasycznych dla nowych testów dostępności.|
|**Próg lokalizacji alertu**|Zalecamy co najmniej 3 na dobę, 5 lokalizacjach. Jest optymalne relacji między próg lokalizacji alertu i liczba lokalizacji testowych **próg lokalizacji alertu** = **liczba lokalizacji testowych - 2, co najmniej 5 lokalizacjach testu.**|

## <a name="see-your-availability-test-results"></a>Wyświetlanie wyników testów dostępności

Mogą być wizualizowane wyników testów dostępności za pomocą widoków Wykres punktowy i wiersza.

Po kilku minutach kliknij **Odśwież** aby zobaczyć wyniki testu.

![Widok wiersza](./media/monitor-web-app-availability/availability-refresh-002.png)

Widok Wykres punktowy przedstawia przykłady wyników testów, które mają szczegóły diagnostycznego kroku testu w nich. Aparat testowy przechowuje szczegółowe informacje diagnostyczne dla testów z błędami. W przypadku udanych testów szczegółowe informacje diagnostyczne są przechowywane dla podzbioru wykonań. Umieść kursor nad zielonych/czerwonych kropkach, aby wyświetlić test, test nazwa i lokalizacja.

![Widok wiersza](./media/monitor-web-app-availability/availability-scatter-plot-003.png)

Wybierz określony test, lokalizację lub skróć okres czasu, aby zobaczyć więcej wyników w okolicy interesującego okresu czasu. Użyj Eksploratora wyszukiwania, aby zobaczyć wyniki z wszystkich wykonań, lub użyj zapytań analitycznych w celu uruchomienia niestandardowych raportów dla tych danych.

## <a name="inspect-and-edit-tests"></a>Sprawdzanie i edytowanie testów

Aby edytować, aby tymczasowo wyłączyć lub usunąć test kliknij wielokropek obok nazwy testu. Może potrwać do 20 minut, zanim zmiany w konfiguracji po dokonaniu zmiany są propagowane do wszystkich agentów testowych.

![Pokaż szczegóły testu. Edytuj i wyłączanie testu sieci web](./media/monitor-web-app-availability/edit.png)

Podczas przeprowadzania konserwacji swojej usługi możesz wyłączyć testy dostępności lub skojarzone z nimi reguły alertów.

## <a name="if-you-see-failures"></a>Jeśli widzisz błędy

Kliknij czerwoną kropkę.

![Kliknij czerwoną kropkę](./media/monitor-web-app-availability/open-instance-3.png)

W wyniku testu dostępności zobaczysz szczegółów transakcji dotyczące wszystkich składników. W tym miejscu możesz wykonywać następujące czynności:

* Zbadać odpowiedź odebraną z serwera.
* Diagnozowanie błędów przy użyciu skorelowanej telemetrii po stronie serwera, zebranych podczas przetwarzania testu dostępności nie powiodło się.
* Zaloguj się problem lub element roboczy w repozytorium Git lub tablice platformy Azure w celu prześledzenia problemu. Błąd będzie zawierać link do tego zdarzenia.
* Otworzyć wynik testu sieci Web w programie Visual Studio.

Dowiedz się więcej o diagnostyce transakcji typu end to end środowisko [tutaj](../../azure-monitor/app/transaction-diagnostics.md).

Kliknij wiersz wyjątek, aby wyświetlić szczegóły dotyczące wyjątków po stronie serwera, który spowodował niepowodzenie testu dostępności syntetycznych. Możesz też pobrać [migawkę debugowania](../../azure-monitor/app/snapshot-debugger.md) dla bogatszych diagnostyki z poziomu kodu.

![Diagnostyka po stronie serwera](./media/monitor-web-app-availability/open-instance-4.png)

Oprócz nieprzetworzonych wyników można również wyświetlić dwa kluczowe metryki dostępności w [Eksploratora metryk](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started):

1. Dostępność: Procent testów, które zakończyły się pomyślnie dla wszystkich wykonań testów.
2. Czas trwania testu: Średni czas trwania testu dla wszystkich wykonań testów.

## <a name="automation"></a>Automatyzacja

* Automatyczne [konfigurowanie testów dostępności za pomocą skryptów środowiska PowerShell](../../azure-monitor/app/powershell.md#add-an-availability-test).
* Konfigurowanie [elementu webhook](../../azure-monitor/platform/alerts-webhooks.md) który jest wywoływany przy zgłaszaniu alertu.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

W wersji dedykowanej [artykule dotyczącym rozwiązywania problemów](troubleshoot-availability.md).

## <a name="next-steps"></a>Kolejne kroki

* [Alerty dostępności](availability-alerts.md)
* [Wieloetapowy test witryny](availability-multistep.md)


