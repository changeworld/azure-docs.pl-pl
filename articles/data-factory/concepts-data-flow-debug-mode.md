---
title: Mapowanie tryb debugowania przepływu danych w usłudze Azure Data Factory
description: Uruchomić sesję debugowania interaktywne podczas budowania danych przepływów
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: d86725718217caf7fd1d9dd6d5d67362e5de7270
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147392"
---
# <a name="mapping-data-flow-debug-mode"></a>Tryb debugowania przepływu danych mapowania

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Azure danych fabryki mapowanie przepływu danych w trybie debugowania mogą być włączone za pomocą przycisku "Debug przepływ danych" w górnej części powierzchni projektowej. Podczas projektowania przepływów danych, włączając tryb debugowania pozwala interaktywnie Obejrzyj dane kształtu transformacji, podczas tworzenia i debugowania przepływów danych. Sesja debugowania mogą służyć zarówno w sesjach projektowania przepływu danych, a także podczas wykonywania debugowania potoku przepływu danych.

![Debugowanie przycisk](media/data-flow/debugbutton.png "przycisk debugowania")

## <a name="overview"></a>Omówienie
Gdy tryb debugowania jest włączona, będziesz tworzyć interaktywnie przepływu danych z aktywnego klastra Spark. Sesja zostanie zamknięte po wyłączeniu debugowania w usłudze Azure Data Factory. Należy pamiętać o godzinowe opłaty naliczane przez usługi Azure Databricks w czasie, czy masz włączony sesji debugowania.

W większości przypadków jest dobrą praktyką jest tworzenie przepływu danych w trybie debugowania, tak że można sprawdzać poprawność logiki biznesowej i wyświetlić przekształceń danych, przed opublikowaniem swoją pracę w usłudze Azure Data Factory. Użyj przycisku "Debugowanie" na panelu potoku, aby przetestować przepływ danych w potoku.

> [!NOTE]
> Jasny tryb debugowania jest zielony, na pasku narzędzi usługi Data Factory, Opłata zostanie naliczona stawka przepływ danych debugowania wynosi 8 rdzeni/godz. ogólne obliczeń z 60-minutowego czas wygaśnięcia 

> [!NOTE]
>Podczas pracy w trybie debugowania w przepływ danych ujścia nie będą zapisywane dane przekształcania. Sesji debugowania ma służyć jako kontroler testów dla przekształceń. Ujścia nie są wymagane podczas debugowania i są ignorowane w przepływie danych. Jeśli chcesz przetestować, zapisywanie danych w ujściu usługi wykonania przepływu danych z potoku usługi Azure Data Factory i użyj wykonywania debugowania z potoku.

## <a name="debug-settings"></a>Ustawienia debugowania
Debugowanie ustawienia mogą być edytowane, klikając pozycję "Ustawienia debugowania" na pasku narzędzi Kanwa przepływ danych. Limity i/lub źródła pliku do użytku dla każdego źródła przekształceń w tym miejscu można wybrać. Limity wierszy, w tym ustawieniu są przeznaczone tylko dla bieżącej sesji debugowania. Możesz również wybrać przejściowa usługa połączona ma być używany dla źródła SQL data Warehouse. 

![Ustawienia debugowania](media/data-flow/debug-settings.png "ustawienia debugowania")

## <a name="cluster-status"></a>Stan klastra
Istnieje klastra wskaźnika stanu, w górnej części powierzchni projektu, który będzie zielony, gdy klaster jest gotowy do debugowania. Jeśli klaster jest już bez wyłączania zasilania, zielony wskaźnik będą wyświetlane niemal natychmiast. Jeśli klaster nie został jeszcze uruchomiony, po wprowadzeniu tryb debugowania, będziesz mieć 5 – 7 minut dla klastra aby zwiększać oczekiwania. Wskaźnik będzie pokrętła aż do jego gotowe.

Po zakończeniu Twojej debugowania, wyłącz przełącznika debugowania, aby klaster usługi Azure Databricks można zakończyć i nie będzie naliczana dla działania debugowania.

## <a name="data-preview"></a>Podgląd danych
Za pomocą debugowania na na karcie Podgląd danych będzie światła w górę na dolny panel. Bez trybie debugowania na przepływ danych przedstawia tylko bieżących metadanych i każdej przekształceń na karcie Sprawdź. Podgląd danych będzie kwerendy tylko liczbę wierszy, które zostały ustawione jako swój limit w ustawieniach debugowania. Konieczne może być kliknij przycisk "Pobierz dane" odświeżanie danych w wersji zapoznawczej.

![Podgląd danych](media/data-flow/datapreview.png "danych (wersja zapoznawcza)")

## <a name="data-profiles"></a>Dane profilów
Wybieranie poszczególnych kolumn danych kart w wersji zapoznawczej będzie się pojawiać, wykres na prawej Siatka danych za pomocą szczegółowe dane statystyczne na temat każdego pola. Usługa Azure Data Factory spowoduje, że określenie na podstawie próbkowania danych typ wykresu, do wyświetlenia. Wysokiej kardynalności pola zostaną domyślnie wykresy /NOT NULL podczas dane kategorii i numeryczne, które ma niski Kardynalność będą wyświetlane wykresy słupkowe przedstawiający częstotliwość wartości danych. Zobaczysz również maksymalna liczba/długość długość pola ciągów, wartości minimalnej i maksymalnej w pól liczbowych, standardowe dev, percentyle, liczby i średnia. 

![Statystyki kolumny](media/data-flow/stats.png "statystyk kolumny")

## <a name="next-steps"></a>Kolejne kroki

Po zakończeniu kompilowania i debugowania przepływu danych, [uruchomić go z potoku.](control-flow-execute-data-flow-activity.md)

Podczas testowania potok przy użyciu przepływu danych, należy użyć potoku [debugowania uruchom opcję wykonywania.](iterative-development-debugging.md)
