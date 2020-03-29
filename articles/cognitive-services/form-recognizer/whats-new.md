---
title: Co nowego w usłudze Rozpoznawanie formularzy?
titleSuffix: Azure Cognitive Services
description: Poznaj najnowsze zmiany w interfejsie API aparatu rozpoznawania formularzy.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: pafarley
ms.openlocfilehash: 7f20244906581dd2869bbc7fcd997d5245540eda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80155175"
---
# <a name="whats-new-in-form-recognizer"></a>Co nowego w usłudze Rozpoznawanie formularzy?

Usługa rozpoznawania formularzy jest aktualizowana na bieżąco. Ten artykuł umożliwia aktualizowanie funkcji, poprawek i aktualizacji dokumentacji.

> [!NOTE]
> Przewodniki szybki start i przewodniki dla aparatu rozpoznawania formularzy zawsze używają najnowszej wersji interfejsu API, chyba że określono.

## <a name="march-2020"></a>Marzec 2020 r. 

### <a name="extraction-enhancements"></a>Ulepszenia ekstrakcji

Ta wersja zawiera ulepszenia ekstrakcji i ulepszenia dokładności, w szczególności możliwość etykietowania i wyodrębniania wielu par klucz/wartość w tym samym wierszu tekstu. 
 
### <a name="form-recognizer-sample-labeling-tool-is-now-open-source"></a>Narzędzie do oznaczania przykładowego aparatu formularza jest teraz open source

Narzędzie do oznaczania przykładowego aparatu rozpoznawania formularzy jest teraz dostępne jako projekt typu open source. Możesz zintegrować go ze swoimi rozwiązaniami i wprowadzić zmiany specyficzne dla klienta, aby spełnić Twoje potrzeby.

Aby uzyskać więcej informacji na temat narzędzia do etykietowania przykładowego aparatu rozpoznawania formularzy, zapoznaj się z dokumentacją dostępną w [usłudze GitHub](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md).

### <a name="labeling-value-types"></a>Typy wartości etykietowania

Typy wartości są teraz dostępne do użycia za pomocą narzędzia do oznaczania przykładowego aparatu formularza. Te typy wartości są obecnie obsługiwane: 

* Ciąg
* Liczba 
* Liczba całkowita
* Data 
* Time

Ten obraz przedstawia, jak wygląda wybór typu wartości w narzędziu do etykietowania przykładowego aparatu rozpoznawania formularzy:

> [!div class="mx-imgBorder"]
> ![Wybór typu wartości za pomocą przykładowego narzędzia do etykietowania](./media/whats-new/formre-value-type.png)

Wyodrębniona tabela jest dostępna w `pageResults`danych wyjściowych JSON w pliku .

### <a name="table-visualization"></a>Wizualizacja tabeli 

Narzędzie do oznaczania aparatów rozpoznawania formularzy wyświetla teraz tabele, które zostały rozpoznane w dokumencie. Dzięki temu można wyświetlić tabele, które zostały rozpoznane i wyodrębnione z dokumentu, przed etykietowaniem i analizą za pomocą narzędzia do etykietowania przykładowego aparatu formularza. Tę funkcję można włączać/wyłączać za pomocą opcji warstw. 

Jest to przykład sposobu rozpoznawanie i wyodrębnianie tabel:

> [!div class="mx-imgBorder"]
> ![Wizualizacja tabeli przy użyciu przykładowego narzędzia do etykietowania](./media/whats-new/formre-table-viz.png)

> [!IMPORTANT]
> Tabele etykietowania nie są obsługiwane. Jeśli tabele nie są rozpoznawane i ekstrakowane automatycznie, można je oznaczyć tylko jako pary klucz/wartość. Podczas etykietowania tabel jako par klucz/wartość, należy oznaczyć każdą komórkę jako wartość.

### <a name="tls-12-enforcement"></a>Wymuszanie protokołu TLS 1.2

* Protokół TLS 1.2 jest teraz wymuszany dla wszystkich żądań HTTP do tej usługi. Aby uzyskać więcej informacji, zobacz [zabezpieczenia usług Azure Cognitive Services](../cognitive-services-security.md).

## <a name="january-2020"></a>Styczeń 2020 r.

W tej wersji wprowadzono aparat rozpoznawania formularzy 2.0 (wersja zapoznawcza). W poniższych sekcjach znajdziesz więcej informacji o nowych funkcjach, ulepszeniach i zmianach. 

### <a name="new-features"></a>Nowe funkcje

* **Model niestandardowy**
  * **Trenuj z etykietami** Teraz można trenować model niestandardowy z danymi oznaczonymi ręcznie. Powoduje to lepszą wydajność modeli i może tworzyć modele, które działają ze złożonymi formularzami lub formularzami zawierającymi wartości bez kluczy.
  * **Asynchroniczne API** Wywołania asynchronii można używać do trenowania i analizowania dużych zestawów danych i plików.
  * **Obsługa plików TIFF** Teraz można trenować z i wyodrębnić dane z dokumentów TIFF.
  * **Ulepszenia dokładności ekstrakcji**

* **Wstępnie utworzony model paragonu**
  * **Kwoty napiwków** Teraz można wyodrębnić kwoty końcówki i inne wartości odręczne.
  * **Wyodrębnianie pozycji** Wartości elementów zamówienia można wyodrębnić z przyjęć.
  * **Wartości ufności** Można wyświetlić zaufanie modelu dla każdej wyodrębnionewarte.
  * **Ulepszenia dokładności ekstrakcji**

* **Wyodrębnianie układu** Teraz można użyć interfejsu API układu, aby wyodrębnić dane tekstowe i dane tabeli z formularzy.

### <a name="custom-model-api-changes"></a>Zmiany interfejsu API modelu niestandardowego

Zmieniono nazwę wszystkich interfejsów API do szkolenia i używania modeli niestandardowych, a niektóre metody synchroniczne są teraz asynchroniczne. Poniżej przedstawiono istotne zmiany:

* Proces szkolenia modelu jest teraz asynchroniczne. Inicjowanie szkolenia za pośrednictwem **/custom/models wywołanie** interfejsu API. To wywołanie zwraca identyfikator operacji, który można przekazać do **niestandardowych/models/{modelID}** do zwrócenia wyników szkolenia.
* Wyodrębnianie klucza/wartości jest teraz inicjowane przez wywołanie **/custom/models/{modelID}/analyze.** To wywołanie zwraca identyfikator operacji, który można przekazać do **niestandardowych/models/{modelID}/analyzeResults/{resultID},** aby zwrócić wyniki wyodrębniania.
* Identyfikatory operacji dla operacji Train znajdują się teraz w nagłówku **Lokalizacja** odpowiedzi HTTP, a nie w nagłówku **Lokalizacja-Lokalizacja.**

### <a name="receipt-api-changes"></a>Zmiany interfejsu API odbioru

Zmieniono nazwę interfejsów API do odczytu wpływów sprzedaży.

* Wyodrębnianie danych odbioru jest teraz inicjowane przez wywołanie interfejsu API **/prebuilt/receipt/analyze.** To wywołanie zwraca identyfikator operacji, który można przekazać do **/prebuilt/receipt/analyzeResults/{resultID}** w celu zwrócenia wyników wyodrębniania.

### <a name="output-format-changes"></a>Zmiany formatu wyjściowego

Odpowiedzi JSON dla wszystkich wywołań interfejsu API mają nowe formaty. Niektóre klucze i wartości zostały dodane, usunięte lub zmieniono ich nazwę. Zobacz przewodniki Szybki start, aby zapoznać się z przykładami bieżących formatów JSON.

## <a name="next-steps"></a>Następne kroki

Ukończ [przewodnik Szybki start,](quickstarts/curl-train-extract.md) aby rozpocząć korzystanie z [interfejsów API aparatu rozpoznawania formularzy.](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)