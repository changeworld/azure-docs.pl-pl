---
title: Jak przekazywać dokumentu — niestandardowe w usłudze Translator
titleSuffix: Azure Cognitive Services
description: Przy użyciu funkcji przekazywania dokumentów możesz przekazać równoległe dokumentów dla Twojej szkoleniach. Równoległe dokumenty są pary dokumenty, gdy jest ona tłumaczenia, z drugiej strony. Jeden dokument w parze zawiera zdań w języku źródła i innego dokumentu zawiera te zdania przetłumaczony na język docelowy.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: article
ms.openlocfilehash: bb84a3ddfba54c47cdf91aa72889a960722cdbd3
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2018
ms.locfileid: "51627432"
---
# <a name="upload-a-document"></a>Przekaż dokument

W [niestandardowe w usłudze Translator](https://portal.customtranslator.azure.ai), możesz przekazać równoległe dokumentów do uczenia modeli tłumaczenia. [Równoległe dokumenty](what-are-parallel-documents.md) są parami dokumentów, w których jeden jest tłumaczenia, z drugiej strony. Jeden dokument w parze zawiera zdań w języku źródła i innego dokumentu zawiera te zdania przetłumaczony na język docelowy.

## <a name="how-to-upload-document"></a>Jak przekazać dokument?

Z [niestandardowe w usłudze Translator](https://portal.customtranslator.azure.ai) portalu, kliknij kartę "Dokumenty", aby przejść na stronę dokumenty.

![Link do przekazywania dokumentu](media/how-to/how-to-upload-1.png)


1.  Kliknij przycisk Przekaż pliki na stronie dokumenty.

    ![Załaduj stronę dokumentu](media/how-to/how-to-upload-2.png)

2.  W oknie dialogowym wypełnij następujące informacje:

    a.  Typ dokumentu:

    -  Szkolenie: Te dokumenty stosowanych w odniesieniu do zestawu szkoleniowego.
    -  Dostrajania: Te dokumenty stosowanych w odniesieniu do dostosowywania zestawu.
    -  Testowanie: Te dokumenty będą używane do testowania zestawu.
    -  Słownik fraza: Te dokumenty stosowanych w odniesieniu do słownika frazę.
    -  Słownik zdanie: Te dokumenty stosowanych w odniesieniu do słownika zdania

    b.  Pary języków.

    c.  Zastąp dokument, jeśli istnieje: Zaznacz to pole wyboru, jeśli chcesz zastąpić wszelkie istniejące dokumenty o takiej samej nazwie.

    d.  Wypełnij w odpowiedniej sekcji dla równoległego lub kombi danych.

    -  Równoległe dane:
        -  Plik źródłowy: Wybierz język źródłowy plik z komputera lokalnego.
        -  Plik docelowy: Wybierz docelowy język pliku z komputera lokalnego.
        -  Nazwa dokumentu: używana tylko wtedy, gdy masz przekazywania plików równoległych.

    - Pole kombi dane:
        -  Pole kombi plik: Wybierz plik kombi z komputera lokalnego. Plik kombi zawiera zarówno źródłowe i język docelowy zdań. [Konwencje nazewnictwa](document-formats-naming-convention.md) jest ważne w przypadku plików kombi.

    e.  Kliknij przycisk Przekaż

    ![Okno dialogowe dokumentu przekazywanie](media/how-to/how-to-upload-dialog.png)

3.  W tym momencie możemy przetwarzania dokumentów i próby wyodrębnienia zdań. Kliknij pozycję "Wyświetl postęp przekazywania" Aby sprawdzić stan dokumentów, zgodnie z ich przetwarzania.

    ![Przekazywanie w oknie dialogowym przetwarzanie dokumentu](media/how-to/how-to-upload-processing-dialog.png)

4.  Tej strony wyświetli stan oraz wszelkie błędy, dla każdego pliku w ramach przekazywania. Możesz wyświetlić ostatnie stan przekazywania w dowolnym momencie, klikając kartę "Przekaż historię".

    ![Przekaż dokument historię w oknie dialogowym](media/how-to/how-to-upload-document-history.png)


## <a name="view-upload-history"></a>Wyświetl historię przekazywania

Na stronie Historia przekazywania możesz wyświetlić historię wszystkich przekazywania dokumentów, których szczegóły, takie jak typ dokumentu, pary języka przekazać stanu itd.

1. Z [niestandardowe w usłudze Translator](https://portal.customtranslator.azure.ai) portalu, kliknij kartę przekazywanie historii, aby wyświetlić historię.

    ![Przekaż karcie Historia](media/how-to/how-to-upload-history-1.png)

2. Ta strona pokazuje stan wszystkich poprzednich przekazywanie. Wyświetla przekazywania od najnowszych do najstarsza. Każde przekazanie prezentuje nazwy dokumentu, stan przekazywania, Data przekazywania, liczba przekazanych plików, typ przekazany plik i pary języka w pliku.

    ![Historia strona przekazywania](media/how-to/how-to-document-history-2.png)

3. Kliknij dowolny rekord historii przekazywania. Na stronie Szczegóły przekazania historii można wyświetlić przekazane jako część przekazywania, stan przekazanego pliku język pliku i komunikatu o błędzie (jeśli istnieje jakikolwiek błąd w przekazywania).

## <a name="next-steps"></a>Kolejne kroki

- Użyj [strony szczegółów dokumentu](how-to-view-document-details.md) Aby przejrzeć listę wyodrębnione zdań.
- [Sposób trenowania modelu](how-to-train-model.md).
