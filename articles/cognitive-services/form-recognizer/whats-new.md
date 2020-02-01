---
title: Co nowego w usłudze Rozpoznawanie formularzy?
titleSuffix: Azure Cognitive Services
description: Zapoznaj się z najnowszymi zmianami w interfejsie API aparatu rozpoznawania formularzy.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: pafarley
ms.openlocfilehash: 2109d25d3962063c711dcab491855d9ebf1cf694
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901888"
---
# <a name="whats-new-in-form-recognizer"></a>Co nowego w usłudze Rozpoznawanie formularzy?

W tym artykule przedstawiono najważniejsze zmiany, które są dostępne w nowych wersjach interfejsu API rozpoznawania formularzy.

> [!NOTE]
> Przewodniki Szybki Start i prowadnice w tym dokumencie zawsze używają najnowszej wersji interfejsu API, chyba że określisz inaczej.

## <a name="form-recognizer-20-preview"></a>Aparat rozpoznawania formularzy 2,0 (wersja zapoznawcza)

### <a name="new-features"></a>Nowe funkcje

* **Model niestandardowy**
  * **Uczenie z etykietami** Teraz można nauczyć model niestandardowy z ręcznie oznaczonymi danymi. Prowadzi to do lepszego wykonywania modeli i może generować modele, które współpracują z złożonymi formularzami lub formularzami zawierającymi wartości bez kluczy.
  * **Asynchroniczny interfejs API** Za pomocą asynchronicznych wywołań interfejsu API można uczenie się i analizować duże zestawy danych i pliki.
  * **Obsługa plików TIFF** Teraz można nauczyć się i wyodrębnić dane z dokumentów TIFF.
  * **Ulepszenia dokładności wyodrębniania**

* **Wstępnie utworzony model przyjęcia**
  * **Kwoty Porada** Teraz można wyodrębnić kwoty etykiet i inne odręczne wartości.
  * **Wyodrębnianie elementu wiersza** Można wyodrębnić wartości elementów wiersza z przyjęć.
  * **Wartości zaufania** Można wyświetlić zaufanie modelu dla każdej wyodrębnionej wartości.
  * **Ulepszenia dokładności wyodrębniania**

* **Wyodrębnianie układu** Teraz można używać interfejsu API układu do wyodrębniania danych tekstowych i danych tabeli z formularzy.

### <a name="custom-model-api-changes"></a>Zmiany modelu niestandardowego interfejsu API

Wszystkie interfejsy API do szkolenia i korzystania z modeli niestandardowych zostały zmienione, a niektóre metody synchroniczne są teraz asynchroniczne. Poniżej przedstawiono istotne zmiany:

* Proces uczenia modelu jest teraz asynchroniczny. Możesz inicjować szkolenia za pomocą wywołania interfejsu API **/Custom/models** . To wywołanie zwraca identyfikator operacji, który można przekazać do **niestandardowych/modeli/{modelID}** w celu zwrócenia wyników szkoleniowych.
* Wyodrębnianie klucza/wartości jest teraz inicjowane przez wywołanie interfejsu API **/Custom/models/{modelID}/analyze** . To wywołanie zwraca identyfikator operacji, który można przekazać do **niestandardowych/modeli/{modelID}/analyzeResults/{identyfikatora resultid}** , aby zwrócić wyniki wyodrębniania.
* Identyfikatory operacji dla operacji pouczenia znajdują się teraz w nagłówku **lokalizacji** odpowiedzi HTTP, a nie w nagłówku **lokalizacji operacji** .

### <a name="receipt-api-changes"></a>Zmiany interfejsu API paragonów

Nazwy interfejsów API do odczytywania przyjęć sprzedaży zostały zmienione.

* Wyodrębnianie danych przyjęcia jest teraz inicjowane przez wywołanie interfejsu API **/prebuilt/receipt/analyze** . To wywołanie zwraca identyfikator operacji, który można przekazać do **/prebuilt/receipt/analyzeResults/{resultID}** w celu zwrócenia wyników wyodrębniania.

### <a name="output-format-changes"></a>Zmiany formatu danych wyjściowych

Odpowiedzi JSON dla wszystkich wywołań interfejsu API mają nowe formaty. Niektóre klucze i wartości zostały dodane, usunięte lub zmieniono ich nazwy. Przykłady bieżących formatów JSON można znaleźć w przewodnikach Szybki Start.

## <a name="next-steps"></a>Następne kroki

Ukończ [Przewodnik Szybki Start](quickstarts/curl-train-extract.md) , aby rozpocząć pracę z [interfejsami API aparatu rozpoznawania formularzy](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm).