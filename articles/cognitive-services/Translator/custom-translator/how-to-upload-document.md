---
title: Jak przesłać dokument - Custom Translator
titleSuffix: Azure Cognitive Services
description: Funkcja przekazywania dokumentów przesyła do usługi dokumenty równoległe (dwa dokumenty, w których jeden jest źródłem, a drugi jest tłumaczeniem).
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: b2a249a40d8c782d54a12df43d33655f3409753c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647384"
---
# <a name="upload-a-document"></a>Przekazywanie dokumentu

W [uliczka Custom Translator](https://portal.customtranslator.azure.ai)można przesyłać równoległe dokumenty, aby szkolić modele tłumaczeń. [Dokumenty równoległe](what-are-parallel-documents.md) są parami dokumentów, w których jedna jest tłumaczeniem drugiego. Jeden dokument w parze zawiera zdania w języku źródłowym, a drugi zawiera te zdania przetłumaczone na język docelowy.

Przed przekazaniem dokumentów zapoznaj się z [formatami dokumentów i wskazówkami dotyczącymi konwencji nazewnictwa,](document-formats-naming-convention.md) aby upewnić się, że format pliku jest obsługiwany w umykaczu niestandardowym.

## <a name="how-to-upload-document"></a>Jak przesłać dokument?

W portalu [Custom Translator](https://portal.customtranslator.azure.ai) kliknij kartę "Dokumenty", aby przejść do strony dokumenty.

![Link do przekazywania dokumentów](media/how-to/how-to-upload-1.png)


1.  Kliknij przycisk Przekaż pliki na stronie Dokumenty.

    ![Prześlij stronę dokumentu](media/how-to/how-to-upload-2.png)

2.  W oknie dialogowym wypełnij następujące informacje:

    a.  Typ dokumentu:

    -  Szkolenie: Te dokumenty będą używane do zestawu szkoleniowego.
    -  Strojenie: Te dokumenty będą używane do dostrajania zestawu.
    -  Testowanie: Te dokumenty będą używane do testowania zestawu.
    -  Słownik fraz: Te dokumenty będą używane do słownika fraz.
    -  Słownik zdań: te dokumenty będą używane do słownika zdań

    b.  Para językowa

    d.  Zastąp dokument, jeśli istnieje: Zaznacz to pole wyboru, jeśli chcesz zastąpić istniejące dokumenty o tej samej nazwie.

    d.  Wypełnij odpowiednią sekcję dla danych równoległych lub danych kombi.

    -  Dane równoległe:
        -  Plik źródłowy: wybierz plik języka źródłowego z komputera lokalnego.
        -  Plik docelowy: wybierz docelowy plik języka z komputera lokalnego.
        -  Nazwa dokumentu: używana tylko w przypadku przekazywania plików równoległych.

    - Dane kombi:
        -  Plik kombi: wybierz plik kombi z komputera lokalnego. Plik kombi zawiera zarówno zdania w języku źródłowym, jak i docelowym. [Konwencja nazewnictwa](document-formats-naming-convention.md) jest ważna dla plików kombi.

    e.  Kliknij przycisk Przekaż

    ![Okno dialogowe Przekazywanie dokumentu](media/how-to/how-to-upload-dialog.png)

3.  W tym momencie przetwarzamy Twoje dokumenty i próbujemy wyodrębnić zdania. Możesz kliknąć "Wyświetl postęp przekazywania", aby sprawdzić stan swoich dokumentów podczas ich przetwarzania.

    ![Okno dialogowe Przekazywanie przetwarzania dokumentów](media/how-to/how-to-upload-processing-dialog.png)

4.  Na tej stronie zostanie wyświetlony stan i wszelkie błędy dla każdego pliku w ramach przesyłania. Możesz zobaczyć status przesyłania w dowolnym momencie, klikając kartę "Historia przesyłania".

    ![Okno dialogowe Przekazywanie historii dokumentu](media/how-to/how-to-upload-document-history.png)


## <a name="view-upload-history"></a>Wyświetlanie historii przesyłania

Na stronie historii przesyłania możesz przeglądać historię wszystkich szczegółów przesyłania dokumentów, takich jak typ dokumentu, para języków, stan przesyłania itp.

1. W portalu [Translator niestandardowy](https://portal.customtranslator.azure.ai) kliknij kartę Przekaż historię, aby wyświetlić historię.

    ![Karta Historia przekazywania](media/how-to/how-to-upload-history-1.png)

2. Ta strona pokazuje stan wszystkich twoich wcześniejszych przesłanych. Wyświetla przesyłane pliki z najnowszych do najmniej najnowszych. Dla każdego przesłania jest wyświetlana nazwa dokumentu, stan przekazywania, data przekazania, liczba przesłanych plików, typ przesłanego pliku i para językowa pliku.

    ![Strona historii przekazywania](media/how-to/how-to-document-history-2.png)

3. Kliknij dowolny rekord historii przesyłania. Na stronie szczegółów historii przekazywania możesz wyświetlić pliki przesłane w ramach przekazywania, przesłanego stanu pliku, języka pliku i komunikatu o błędzie (jeśli w przekazywaniu występuje błąd).

## <a name="next-steps"></a>Następne kroki

- Strona [szczegółów dokumentu](how-to-view-document-details.md) służy do przeglądania listy wyodrębnionych zdań.
- [Jak wyszkolić model](how-to-train-model.md).
