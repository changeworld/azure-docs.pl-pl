---
title: Mapowanie tryb debugowania przepływu danych w usłudze Azure Data Factory
description: Uruchomić sesję debugowania interaktywne podczas budowania danych przepływów
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 2851517aee0176775bde8b58994f0ed20f6ed01d
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56213409"
---
# <a name="azure-data-factory-mapping-data-flow-debug-mode"></a>Mapowanie tryb debugowania przepływu danych w usłudze Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Azure Data Factory mapowania przepływ danych ma tryb debugowania, które mogą być włączone za pomocą przycisku debugowania w górnej części powierzchni projektowej. Podczas projektowania przepływów danych, ustawienie Tryb debugowania pozwala interaktywnie Obejrzyj dane kształtu transformacji, podczas tworzenia i debugowania przepływów danych.

<img src="media/data-flow/debugbutton.png" width="400">

## <a name="overview"></a>Przegląd
Gdy tryb debugowania jest włączona, możesz interaktywnie tworzyć przepływu danych z działającego klastra interaktywne usługi Azure Databricks. Sesja zostanie zamknięte po wyłączeniu debugowania w usłudze Azure Data Factory. Należy pamiętać o godzinowe opłaty naliczane przez usługi Azure Databricks w czasie, czy masz włączony sesji debugowania.

W większości przypadków jest dobrą praktyką jest tworzenie przepływu danych w trybie debugowania, tak że można sprawdzać poprawność logiki biznesowej i wyświetlić przekształceń danych, przed opublikowaniem swoją pracę w usłudze Azure Data Factory.

## <a name="debug-mode-on"></a>Tryb debugowania na
Po przełączeniu w tryb debugowania, zostanie wyświetlony monit z formularzem boczny panel, który będzie żądać wskaż interaktywne klastra usługi Azure Databricks i wybierz opcje dla pobierania próbek źródła. Musisz używania interaktywne klastra z usługi Azure Databricks i wybierz rozmiar próbki z każdej usługi przekształceń źródła lub pobranie pliku tekstowego do użycia dla danych testowych.

<img src="media/data-flow/upload.png" width="400">

> [!NOTE]
>Podczas pracy w trybie debugowania w przepływ danych ujścia nie będą zapisywane dane przekształcania. Sesja debugowania jest przeznaczony do służyć jako test > potrzeby przekształceń. Ujścia nie są wymagane podczas debugowania i są ignorowane w przepływie danych. Jeśli chcesz przetestować, zapisanie danych > w ujściem Twojej wykonania przepływu danych z potoku usługi Azure Data Factory i użyj wykonywania debugowania z potoku.

## <a name="debug-settings"></a>Ustawienia debugowania
Ustawienia debugowania mogą być każde źródło, z przepływu danych będą wyświetlane w panelu po stronie i można również edytować, wybierając pozycję "Ustawienia źródła" na pasku narzędzi projektowania na przepływ danych. Można wybrać, limity i/lub źródła pliku do użytku dla każdego swoją transformację źródła. Możesz również wybrać klastra usługi Databricks, które chcesz użyć do debugowania.

## <a name="cluster-status"></a>Stan klastra
Istnieje klastra wskaźnika stanu, w górnej części powierzchni projektu, który będzie zielony, gdy klaster jest gotowy do debugowania. Jeśli klaster jest już bez wyłączania zasilania, zielony wskaźnik będą wyświetlane niemal natychmiast. Jeśli klaster nie został jeszcze uruchomiony, po wprowadzeniu tryb debugowania, następnie trzeba będzie poczekać 5 – 7 minut dla klastra nad ułatwieniem wdrażania. Będzie żółty światła wskaźnika, aż wszystko będzie gotowe. Gdy klaster jest gotowy do debugowania przepływu danych, będzie zielony światła wskaźnika.

Po zakończeniu z Twojej debugowania, Włącz debugowanie wyłączyć tak, aby klaster usługi Azure Databricks można zakończyć.

<img src="media/data-flow/datapreview.png" width="400">

## <a name="data-preview"></a>Podgląd danych
Za pomocą debugowania na na karcie Podgląd danych będzie światła w górę na dolny panel. Bez trybie debugowania na przepływ danych przedstawia tylko bieżących metadanych i każdej przekształceń na karcie Sprawdź. Podgląd danych będzie kwerendy tylko liczbę wierszy, ustawione jako swój limit w ustawieniach źródła. Konieczne może być kliknij przycisk "Pobierz dane" odświeżanie danych w wersji zapoznawczej.

<img src="media/data-flow/stats.png" width="400">

## <a name="data-profiles"></a>Dane profilów
Wybieranie poszczególnych kolumn kart danych (wersja zapoznawcza) zostanie okno podręczne wykres na prawej Siatka danych za pomocą szczegółowe dane statystyczne na temat każdego pola. Usługa Azure Data Factory spowoduje, że określenie na podstawie próbkowania danych typ wykresu, do wyświetlenia. Wysoka Kardynalność pola zostaną domyślnie NULL / NOT NULL wykresy, podczas gdy dane kategorii i numeryczne, które ma niski Kardynalność będą wyświetlane wykresy słupkowe przedstawiający częstotliwość wartości danych. Pojawi się także maksymalny / długość długość pola ciągów, minimalna / maksymalna liczba wartości w pól liczbowych, standardowe dev, percentyle, liczby i średnia. 

<img src="media/data-flow/chart.png" width="400">
