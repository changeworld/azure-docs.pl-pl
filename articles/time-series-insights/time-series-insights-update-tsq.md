---
title: Danych i wykonywania zapytań usługi Azure Time Series Insights (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Podczas badania danych platformy Azure Time Series Insights (wersja zapoznawcza)
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/04/2018
ms.openlocfilehash: 00ef6eed23d1645320c28123d6670230cdd725c9
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52964921"
---
# <a name="data-querying"></a>Wykonywanie zapytań dotyczących danych

Usługa Azure Time Series Insights (TSI) umożliwia danych zapytań o zdarzeń i metadane przechowywane w środowisku przy użyciu publicznych interfejsów API urządzenia surface. Te interfejsy API są również używane w [Eksplorator usługi TSI](./time-series-insights-update-explorer.md).

Istnieją trzy podstawowe kategorie interfejsu API, które są dostępne w usłudze Azure TSI:

* Środowisko interfejsom API samym środowisku usługa TSI Przesyła zapytania, takie jak listy środowisk obiekt wywołujący ma dostęp do metadanych środowiska itp.

* Włącz interfejsy API (TSM-Q) model zapytania serii czasu służy do tworzenia, odczytu, aktualizacji i usuwania operacji na metadanych w środowisku części modelu szeregów czasowych. Podobnie jak wystąpień typów, hierarchii, itp.

* Czas serii zapytania (TSQ) interfejsy API pozwalają pobierania danych zdarzenia są rejestrowane od dostawcy źródła lub można wykonać operacji przekształcania, łączenie i wykonywania obliczeń na danych szeregów czasowych.

[Język wyrażeń serii czasu](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx) (TSX) jest skuteczna, czwarty, kategorii. Używa on modeli serii czasu (TSM), aby umożliwić skład zaawansowanych obliczeń.

## <a name="azure-time-series-insights-core-apis"></a>Usługa Azure Time Series Insights podstawowe interfejsy API

Poniżej przedstawiono podstawowe interfejsy API, firma Microsoft obsługuje.

![tsq][1]

### <a name="the-environment-apis"></a>Interfejsy API środowiska

Poniżej przedstawiono dostępne interfejsy API w środowisku:

* [Pobieranie interfejsu API środowiska](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environments-api): zwraca listę wszystkich środowisk, czy obiekt wywołujący jest autoryzowany do uzyskania dostępu.
* [Pobieranie interfejsu API dostępność środowiska](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environment-availability-api): zwraca rozkład liczby zdarzeń za pośrednictwem sygnatura czasowa zdarzenia `$ts`. Ten interfejs API pomaga ustalić, czy istnieją zdarzeń sygnaturę czasową, zwracając liczbę zdarzeń, jeśli istnieje.
* [Pobierz interfejs API schematu zdarzeń](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-event-schema-api): zwraca metadane schematu zdarzeń dla podanego zakresu. Ten interfejs API pomaga pobrać wszystkich metadanych/właściwości dostępne w schemacie dla podanego zakresu.

### <a name="time-series-model-query-tsm-q-apis"></a>Interfejsy API (TSM-Q) dla zapytania w modelu serii czasu

Czas serii Model zapytania interfejsami API dostępnymi są następujące:

* [Ustawienia interfejsu API modelu](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api): umożliwia pobieranie i stosowanie poprawek na domyślny typ i nazwa modelu środowiska.
* [Typy interfejsu API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api): włącza CRUD na typy serii czasu oraz ich zmienne skojarzone.
* [Interfejs API hierarchie](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api): włącza CRUD na hierarchie serii czasu i ich skojarzone pole ścieżki.
* [Wystąpienia interfejsu API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api): włącza CRUD na wystąpienia serii czasu i ich pól skojarzonego wystąpienia.

### <a name="the-time-series-query-tsq-apis"></a>Interfejsy API zapytań (TSQ) serii czasu

Czas serii zapytania interfejsami API dostępnymi są następujące:

* [Pobieranie interfejsu API zdarzeń](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-events-api): rozpoczynanie zdarzenia interfejsu API umożliwia wysyłanie kwerend i pobieranie danych usługa TSI Przesyła zdarzeń są rejestrowane w usłudze Azure TSI od dostawcy źródła.

* [Rozpoczynanie serii API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-series-api): umożliwia wysyłanie kwerend i pobieranie danych usługi Azure TSI z przechwytywanych zdarzeń przy użyciu danych zarejestrowanych na potrzeby przesyłu przy użyciu zmiennych definiowanie w tekście modelu lub podana.

    >[!NOTE]
    > Klauzula agregacji jest ignorowane, nawet jeśli określona w modelu lub podano wbudowanego.

  Serii uzyskać interfejs API zwraca TSV (wartości serii godziny, format, w usłudze TSI korzysta z danych wyjściowych JSON z zapytania) dla każdej zmiennej dla każdego przedziału na podstawie udostępnionych **identyfikator serii czasu** i zestawem używanych zmiennych podana.

* [Agreguj API serii](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#aggregate-series-api): umożliwia wysyłanie kwerend i pobieranie usługa TSI Przesyła dane z przechwytywanych zdarzeń przez pobieranie próbek i agregowanie zarejestrowanych danych.

  Łączny API serii zwraca TSV dla każdej zmiennej dla każdego przedziału na podstawie udostępnionych **identyfikator serii czasu** i zestawem używanych zmiennych podana. Łączny API serii osiąga redukcji dzięki wykorzystaniu zmiennych przechowywanych w TSM lub podano wbudowaną, aby agregacji lub przykładowe dane.

  Obsługiwane typy agregacji: `Min`, `Max`, `Sum`, `Count`, `Average`

## <a name="next-steps"></a>Kolejne kroki

Odczyt [magazynu Azure TSI (wersja zapoznawcza) i ruch przychodzący](./time-series-insights-update-storage-ingress.md).

Przeczytaj o [modelowania danych](./time-series-insights-update-tsm.md).

Przeczytaj o [najlepszych rozwiązań, wybierając Identyfikatora serii czasu](./time-series-insights-update-how-to-id.md).

<!-- Images -->
[1]: media/v2-update-tsq/tsq.png
