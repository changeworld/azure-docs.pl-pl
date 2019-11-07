---
title: Informacje o metrykach dla chmury wiosennej platformy Azure
description: Dowiedz się, jak przejrzeć metryki w chmurze wiosennej platformy Azure
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: 952dfc1c707df3b7fa61443ea6ea18630352f0dc
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607806"
---
# <a name="metrics-for-azure-spring-cloud"></a>Metryki dla chmury wiosennej platformy Azure

Eksplorator metryk Azure Monitor jest składnikiem Microsoft Azure Portal, który umożliwia Kreślenie wykresów, wizualnie skorelowane trendy oraz badanie skoków i wartości DIP w metrykach. Użyj Eksploratora metryk, aby zbadać kondycję i wykorzystanie zasobów. W chmurze Azure wiosną oferujemy dwie opcje wyświetlania metryk: wykresy na stronie **Przegląd aplikacji** oraz na stronie metryki poziomu usług.

## <a name="application-overview-page"></a>Strona przeglądu aplikacji

Na stronie **przeglądu aplikacji** każdej aplikacji jest prezentowany wykres metryk, który umożliwia wykonywanie szybkiego sprawdzania stanu aplikacji.  Przejdź do strony usługi w chmurze ze sprężyną Azure i wybierz pozycję **pulpit nawigacyjny aplikacji**, a następnie wybierz aplikację z listy.  

Firma Microsoft udostępnia 5 wykresów z metrykami zaktualizowanymi co minutę dla następujących:

* **Błędy serwera http**: liczba błędów dla żądań HTTP do aplikacji.
* **Dane w**: bajty odebrane przez aplikację.
* **Dane wychodzące**: Bajty wysłane do aplikacji.
* **Żądania**: żądania odebrane przez aplikację.
* **Średni czas odpowiedzi**: Średni czas odpowiedzi z aplikacji.

Możesz wybrać zakres czasu dla wykresu z przedziału od 1 godziny do 7 dni.

## <a name="service-level-metric-queries"></a>Zapytania o metryki na poziomie usług

Chmura sprężynowa platformy Azure umożliwia monitorowanie różnych metryk aplikacji. Zapoznaj się z [przewodnikiem, aby rozpocząć pracę](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started) z Azure monitor metrykami, aby dowiedzieć się więcej na temat tej usługi.

Aby przejrzeć dane metryk, wybierz metrykę, **agregację**i zakres czasu.  Poniższe pojęcia zostały omówione poniżej.

### <a name="aggregation"></a>Agregacja 

Platforma Azure sonduje i aktualizuje metryki co minutę. Platforma Azure oferuje trzy sposoby agregowania danych w wybranym okresie:

* **Łącznie**: Sumuj wszystkie metryki jako docelowe dane wyjściowe.
* **Średnia**: Użyj średniej wartości w okresie jako docelowy wynik.
* **Max/min**: Użyj wartości max/min w okresie jako docelowego wyjścia.

### <a name="time-range"></a>Przedział czasu

Wybierz domyślny zakres czasu lub Zdefiniuj własny.

### <a name="modifying-the-granularity-of-your-metric-query"></a>Modyfikowanie stopnia szczegółowości zapytania metryki

Domyślnie platforma Azure agreguje metryki dla wszystkich aplikacji usługi chmurowej platformy Azure. Aby przejrzeć metryki na poziomie aplikacji lub wystąpienia, użyj funkcji Filter.  
Wybierz pozycję **Dodaj filtr**, ustaw właściwość na wartość **App** i wybierz aplikację docelową, którą chcesz monitorować. Opcjonalnie użyj opcji **Zastosuj dzielenie** , aby narysować oddzielne linie dla każdej aplikacji na wykresie.

>[!TIP]
> Możesz tworzyć własne wykresy na stronie metryk i przypinać je do **pulpitu nawigacyjnego**. Zacznij od zmiany nazwy wykresu.  Następnie wybierz pozycję **Przypnij do pulpitu nawigacyjnego w prawym górnym rogu**. Teraz możesz sprawdzić swoją aplikację na **pulpicie nawigacyjnym**portalu.