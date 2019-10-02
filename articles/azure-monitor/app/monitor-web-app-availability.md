---
title: Monitorowanie dostępności i czasu odpowiedzi dowolnej witryny sieci Web | Microsoft Docs
description: Skonfiguruj testy sieci Web w Application Insights. Otrzymuj alerty, jeśli witryna internetowa będzie niedostępna lub reaguje powoli.
services: application-insights
author: mrbullwinkle
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/16/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 3c7ba10525dedf213a416d9ce6b55c80539fedd7
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71812206"
---
# <a name="monitor-the-availability-of-any-website"></a>Monitorowanie dostępności dowolnej witryny sieci Web

Po wdrożeniu aplikacji lub witryny sieci Web można skonfigurować testy cykliczne, aby monitorować dostępność i czas odpowiedzi. [Usługa Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) wysyła żądania sieci Web do aplikacji w regularnych odstępach czasu z punktów na całym świecie. Może to być alert, jeśli aplikacja nie odpowiada, lub jeśli reaguje zbyt wolno.

Testy dostępności można skonfigurować dla dowolnego punktu końcowego HTTP lub HTTPS, który jest dostępny z publicznego Internetu. Nie musisz wprowadzać żadnych zmian w testowanej witrynie sieci Web. W rzeczywistości nie musi to być jeszcze posiadana witryna. Możesz przetestować dostępność interfejsu API REST, od którego zależy usługa.

### <a name="types-of-availability-tests"></a>Typy testów dostępności:

Istnieją trzy typy testów dostępności:

* [Test ping adresu URL](#create-a-url-ping-test): prosty test, który można utworzyć w Azure Portal.
* [Wieloetapowy test sieci Web](availability-multistep.md): nagrywanie sekwencji żądań sieci Web, które mogą być odtwarzane w celu przetestowania bardziej złożonych scenariuszy. Wieloetapowe testy sieci Web są tworzone w Visual Studio Enterprise i przekazywane do portalu w celu wykonania.
* [Testy dostępności niestandardowej ścieżki](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability?view=azure-dotnet): Jeśli zdecydujesz się utworzyć niestandardową aplikację do uruchamiania testów dostępności, Metoda `TrackAvailability()` może być używana do wysyłania wyników do Application Insights.

**Można utworzyć maksymalnie 100 testów dostępności dla zasobu Application Insights.**

## <a name="create-an-application-insights-resource"></a>Tworzenie zasobu Application Insights

Aby można było utworzyć test dostępności, należy najpierw utworzyć zasób Application Insights. Jeśli zasób został już utworzony, przejdź do następnej sekcji, aby [utworzyć test ping adresu URL](#create-a-url-ping-test).

W Azure Portal wybierz pozycję **Utwórz zasób** > **Narzędzia deweloperskie** > **Application Insights** i [Utwórz zasób Application Insights](create-new-resource.md).

## <a name="create-a-url-ping-test"></a>Utwórz test ping adresu URL

Nazwa "test ping adresu URL" jest bitem elementu Misnomer. Aby można było wyczyścić Ten test, nie jest możliwe użycie protokołu ICMP (Internet Control Message Protocol) do sprawdzenia dostępności witryny. Zamiast tego używa bardziej zaawansowanych funkcji żądania HTTP, aby sprawdzić, czy punkt końcowy odpowiada. Mierzy również wydajność skojarzoną z tą odpowiedzią i dodaje możliwość ustawienia niestandardowych kryteriów sukcesu, które są połączone z bardziej zaawansowanymi funkcjami, takimi jak analizowanie żądań zależnych i Zezwalanie na ponowne próby.

Aby utworzyć pierwsze żądanie dostępności, Otwórz okienko dostępność i wybierz pozycję **Utwórz test**.

![Wprowadź co najmniej adres URL witryny sieci Web](./media/monitor-web-app-availability/availability-create-test-001.png)

### <a name="create-a-test"></a>Tworzenie testu

|Ustawienie| Wyjaśnienie
|----|----|----|
|**Adres URL** |  Adres URL może być dowolną stroną sieci Web, która ma zostać przetestowana, ale musi być widoczna z publicznej sieci Internet. Adres URL może zawierać ciąg zapytania. Może to na przykład ułatwić wykonywanie bazy danych. Jeśli adres URL jest rozpoznawany jako przekierowanie, przejdziemy do 10 przekierowań.|
|**Analizuj zależne żądania**| Testuje obrazy, skrypty, pliki stylów i inne pliki, które są częścią strony sieci Web w teście. Zarejestrowany czas odpowiedzi obejmuje czas potrzebny do pobrania tych plików. Test kończy się niepowodzeniem, jeśli nie można pomyślnie pobrać któregokolwiek z tych zasobów w ramach limitu czasu dla całego testu. Jeśli opcja nie jest zaznaczona, test wysyła tylko żądanie pliku pod określonym adresem URL. Włączenie tej opcji skutkuje dokładniejszym sprawdzeniem. Test może zakończyć się niepowodzeniem, co może nie być zauważalne podczas ręcznego przeglądania witryny.
|**Włącz ponawianie prób**|gdy test zakończy się niepowodzeniem, zostanie ponowiony po krótkim czasie. Błąd jest zgłaszany tylko wtedy, gdy trzy kolejne próby kończą się niepowodzeniem. Kolejne testy są następnie wykonywane z normalną częstotliwością testu. Ponowna próba jest tymczasowo zawieszona do następnego sukcesu. Ta reguła jest stosowana niezależnie w każdej lokalizacji testowej. **Zalecamy korzystanie z tej opcji**. Średnio około 80% błędów znika po ponowieniu próby.|
|**Częstotliwość testów**| Ustawia częstotliwość uruchamiania testu z każdej lokalizacji testowej. Z domyślną częstotliwością wynoszącą pięć minut i pięć lokalizacji testowych lokacja jest testowana średnio co minutę.|
|**Lokalizacje testów**| Są miejsca, w których serwery wysyłają żądania sieci Web do adresu URL. **Minimalna liczba zalecanych lokalizacji testowych to pięć** , aby upewnić się, że problemy w witrynie sieci Web można odróżnić od problemów z siecią. Można wybrać maksymalnie 16 lokalizacji.

**Jeśli Twój adres URL nie jest widoczny w publicznej sieci Internet, możesz wybrać opcję selektywnego otwierania zapory, aby zezwalać tylko na transakcje testowe za pośrednictwem programu**. Aby dowiedzieć się więcej o wyjątkach zapory dla naszych agentów testów dostępności, zapoznaj się z [przewodnikiem po adresie IP](https://docs.microsoft.com/azure/azure-monitor/app/ip-addresses#availability-tests).

> [!NOTE]
> Zdecydowanie zalecamy testowanie z wielu lokalizacji z **co najmniej pięcioma lokalizacjami**. Ma to na celu uniknięcie fałszywych alarmów, które mogą powodować przejściowe problemy z określoną lokalizacją. Ponadto firma Microsoft ustaliła, że optymalna konfiguracja ma mieć **liczbę lokalizacji testowych równą progowi lokalizacji alertu + 2**.

### <a name="success-criteria"></a>Kryteria sukcesu

|Ustawienie| Wyjaśnienie
|----|----|----|
| **Limit czasu testu** |Zmniejsz tę wartość, aby otrzymywać alerty o powolnych odpowiedziach. Test jest traktowany jako błąd, jeśli odpowiedzi z witryny nie zostały odebrane w tym okresie. W przypadku wybrania pozycji **Analizuj zależne żądania**wszystkie obrazy, pliki stylów, skrypty i inne zasoby zależne muszą zostać odebrane w tym okresie.|
| **Odpowiedź HTTP** | Zwrócony kod stanu, który jest liczony jako powodzenie. 200 to kod, który wskazuje, że została zwrócona zwykła Strona sieci Web.|
| **Dopasowanie zawartości** | Ciąg, na przykład "Witaj!" Sprawdzamy, czy w każdej odpowiedzi występuje dokładne dopasowanie uwzględniające wielkość liter. Musi to być zwykły ciąg, bez symboli wieloznacznych. Nie zapomnij, że jeśli zmieni się zawartość strony, może być konieczne jej zaktualizowanie. **Zgodność zawartości obsługuje tylko znaki angielskie** |

### <a name="alerts"></a>Alerty

|Ustawienie| Wyjaśnienie
|----|----|----|
|**Niemal w czasie rzeczywistym (wersja zapoznawcza)** | Zalecamy korzystanie z alertów niemal w czasie rzeczywistym. Konfigurowanie tego typu alertu odbywa się po utworzeniu testu dostępności.  |
|**Motyw** | Nie zalecamy już używania klasycznych alertów dla nowych testów dostępności.|
|**Próg lokalizacji alertu**|Zalecamy co najmniej 3/5 lokalizacji. Optymalną relację między progiem lokalizacji alertów a liczbą lokalizacji testowych jest **próg lokalizacji alertu** = **Liczba lokalizacji testowych-2 z co najmniej pięcioma lokalizacjami testów.**|

## <a name="see-your-availability-test-results"></a>Zobacz wyniki testu dostępności

Wyniki testu dostępności można wizualizować przy użyciu widoków liniowych i punktowych.

Po kilku minutach kliknij przycisk **Odśwież** , aby zobaczyć wyniki testu.

![Widok wiersza](./media/monitor-web-app-availability/availability-refresh-002.png)

Widok scatterplot pokazuje próbki wyników testu, które mają w nich szczegółową czynność testu diagnostycznego. Aparat testowy przechowuje szczegóły diagnostyczne dla testów, które mają Błędy. W przypadku testów zakończonych powodzeniem informacje diagnostyczne są przechowywane dla podzestawu wykonań. Umieść kursor na dowolnym z zielonych i czerwonych kropek, aby zobaczyć test, nazwę testu i lokalizację.

![Widok wiersza](./media/monitor-web-app-availability/availability-scatter-plot-003.png)

Wybierz konkretny test, lokalizację lub skrócenie okresu, aby zobaczyć więcej wyników w tym okresie. Użyj Eksploratora wyszukiwania, aby wyświetlić wyniki ze wszystkich wykonań, lub użyj zapytań analizy do uruchomienia niestandardowych raportów dotyczących tych danych.

## <a name="inspect-and-edit-tests"></a>Sprawdzanie i edytowanie testów

Aby edytować, tymczasowo wyłączyć lub usunąć test, kliknij wielokropek obok nazwy testu. Propagowanie zmian konfiguracji do wszystkich agentów testowych po wprowadzeniu zmiany może potrwać do 20 minut.

![Wyświetl szczegóły testu. Edytuj i Wyłącz test sieci Web](./media/monitor-web-app-availability/edit.png)

Możesz chcieć wyłączyć testy dostępności lub reguły alertów skojarzonych z nimi podczas przeprowadzania konserwacji usługi.

## <a name="if-you-see-failures"></a>Jeśli widzisz błędy

Kliknij czerwoną kropkę.

![Kliknij czerwoną kropkę](./media/monitor-web-app-availability/open-instance-3.png)

W wyniku testu dostępności można zobaczyć szczegóły transakcji dla wszystkich składników. W tym miejscu możesz:

* Zbadaj odpowiedź odebraną z serwera.
* Diagnozowanie błędów ze skorelowanej telemetrii po stronie serwera podczas przetwarzania testu dostępności zakończonej niepowodzeniem.
* Zarejestruj problem lub element roboczy w usłudze Git lub Azure Boards, aby śledzić problem. Usterka będzie zawierać link do tego zdarzenia.
* Otwórz wynik testu sieci Web w programie Visual Studio.

Więcej informacji na temat kompleksowego środowiska diagnostyki transakcji można znaleźć [tutaj](../../azure-monitor/app/transaction-diagnostics.md).

Kliknij wiersz wyjątku, aby wyświetlić szczegóły wyjątku po stronie serwera, który spowodował niepowodzenie testu dostępności syntetycznej. Możesz również uzyskać [migawkę debugowania](../../azure-monitor/app/snapshot-debugger.md) na potrzeby bardziej zaawansowanej diagnostyki poziomu kodu.

![Diagnostyka po stronie serwera](./media/monitor-web-app-availability/open-instance-4.png)

Oprócz nieprzetworzonych wyników można także wyświetlić dwie metryki dostępności klucza w [Eksplorator metryk](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started):

1. Dostępność: procent testów, które zakończyły się powodzeniem, we wszystkich wykonaniach testów.
2. Czas trwania testu: Średni czas trwania testu dla wszystkich wykonań testów.

## <a name="automation"></a>Automatyzacja

* [Użyj skryptów programu PowerShell, aby automatycznie skonfigurować test dostępności](../../azure-monitor/app/powershell.md#add-an-availability-test) .
* Skonfiguruj [element webhook](../../azure-monitor/platform/alerts-webhooks.md) , który jest wywoływany, gdy zostanie zgłoszony alert.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Dedykowany artykuł dotyczący [rozwiązywania problemów](troubleshoot-availability.md).

## <a name="next-steps"></a>Następne kroki

* [Alerty dostępności](availability-alerts.md)
* [Wieloetapowe testy sieci Web](availability-multistep.md)


