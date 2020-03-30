---
title: Dostosowywanie modelu języka za pomocą witryny video indexer
titleSuffix: Azure Media Services
description: Dowiedz się, jak dostosować model języka za pomocą witryny indeksatora wideo.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 8917a3ac302d18337d79bffce69bad108667b4d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128080"
---
# <a name="customize-a-language-model-with-the-video-indexer-website"></a>Dostosowywanie modelu języka za pomocą witryny video Indexer

Indeksator wideo umożliwia tworzenie niestandardowych modeli języka w celu dostosowania rozpoznawania mowy przez przesłanie tekstu adaptacyjnego, a mianowicie tekstu z domeny, do którego słownictwa chcesz dostosować aparat. Po przeszkoleniu modelu zostaną rozpoznane nowe słowa pojawiające się w tekście adaptacowym.

Aby uzyskać szczegółowe omówienie i najlepsze wskazówki dotyczące niestandardowych modeli języka, zobacz [Dostosowywanie modelu języka za pomocą indeksatora wideo](customize-language-model-overview.md).

Za pomocą witryny indeksatora wideo można tworzyć i edytować niestandardowe modele języka na koncie, zgodnie z opisem w tym temacie. Można również użyć interfejsu API, zgodnie z opisem w [dostosuj model języka przy użyciu interfejsów API](customize-language-model-with-api.md).

## <a name="create-a-language-model"></a>Tworzenie modelu języka

1. Przejdź do witryny [indeksatora](https://www.videoindexer.ai/) wideo i zaloguj się.
2. Aby dostosować model na koncie, wybierz przycisk **Dostosowywanie modelu zawartości** w prawym górnym rogu strony.

   ![Dostosowywanie modelu zawartości w indeksatorze wideo](./media/content-model-customization/content-model-customization.png)

3. Wybierz kartę **Język.**

    Zobaczysz listę obsługiwanych języków.

    ![Lista modeli języków w indeksatorze wideo](./media/customize-language-model/customize-language-model.png)

4. W wybranym języku wybierz pozycję **Dodaj model**.
5. Wpisz nazwę modelu języka i naciśnij enter.

    Ten krok tworzy model i daje możliwość przekazywania plików tekstowych do modelu.

6. Aby dodać plik tekstowy, wybierz pozycję **Dodaj plik**. Otworzy się Eksplorator plików.

7. Przejdź do pliku tekstowego i zaznacz go. Do modelu języka można dodać wiele plików tekstowych.

    Plik tekstowy można również dodać, zaznaczając przycisk **...** po prawej stronie modelu języka i wybierając **pozycję Dodaj plik**.

8. Po zakończeniu przesyłania plików tekstowych wybierz zieloną opcję **Pociąg.**

    ![Trenuj model języka w indeksatorze wideo](./media/customize-language-model/train-model.png)

Proces szkolenia może potrwać kilka minut. Po zakończeniu szkolenia zostanie wyświetlony **Trained** obok modelu. Można wyświetlić podgląd, pobrać i usunąć plik z modelu.

![Model wyszkolonego języka w indeksatorze wideo](./media/customize-language-model/preview-model.png)

### <a name="using-a-language-model-on-a-new-video"></a>Używanie modelu języka w nowym filmie

Aby użyć modelu języka w nowym klipie wideo, wykonaj jedną z następujących czynności:

* Wybierz przycisk **Przekaż** u góry strony.

    ![Przycisk Przekazywanie Indeksator wideo](./media/customize-language-model/upload.png)

* Upuść plik audio lub wideo do okręgu lub wyszukaj plik.

    ![Prześlij indeksator wideo pliku multimedialnego](./media/customize-language-model/upload2.png)

Masz możliwość wybrania języka **źródłowego wideo**. Wybierz listę rozwijaną i wybierz model języka utworzony z listy. Należy podać język modelu języka i nazwę, która została nadana w nawiasach.

Wybierz opcję **Przekaż** u dołu strony, a nowy film zostanie zindeksowany przy użyciu modelu języka.

### <a name="using-a-language-model-to-reindex"></a>Ponowne indeksowanie przy użyciu modelu języka

Aby użyć modelu języka do ponownego indeksowania filmu w kolekcji, przejdź do **filmów z konta** na stronie głównej [indeksatora wideo](https://www.videoindexer.ai/) i umieść wskaźnik myszy na nazwie filmu, który chcesz ponownie wyeksja.

Zobaczysz opcje edytowania filmu, usuwania filmu i ponownego indeksowania filmu. Wybierz opcję ponownego indeksacji filmu.

![Reindex z indeksatorem wideo](./media/customize-language-model/reindex1.png)

Możesz wybrać **język źródłowy wideo,** w który chcesz ponownie wyeksliwać film. Wybierz listę rozwijaną i wybierz model języka utworzony z listy. Należy podać język modelu języka i nazwę, którą nadano mu w nawiasach.

![Wybieranie języka źródła wideo — ponowne indeksację wideo za pomocą indeksatora wideo](./media/customize-language-model/reindex.png)

Wybierz przycisk **Ponownie indeksuj,** a film zostanie ponownie zindeksowany przy użyciu modelu języka.

## <a name="edit-a-language-model"></a>Edytowanie modelu języka

Model języka można edytować, zmieniając jego nazwę, dodając do niego pliki i usuwając z niego pliki.

Jeśli dodasz lub usuniesz pliki z modelu języka, musisz ponownie trenować model, wybierając zieloną opcję **Pociąg.**

### <a name="rename-the-language-model"></a>Zmienianie nazwy modelu języka

Nazwę modelu języka można zmienić, wybierając przycisk wielokropek**po**prawej stronie modelu języka i wybierając opcję **Zmień nazwę**.

Wpisz nową nazwę i naciśnij enter.

### <a name="add-files"></a>Dodawanie plików

Aby dodać plik tekstowy, wybierz pozycję **Dodaj plik**. Otworzy się Eksplorator plików.

Przejdź do pliku tekstowego i zaznacz go. Do modelu języka można dodać wiele plików tekstowych.

Plik tekstowy można również dodać, zaznaczając przycisk wielokropek**po**prawej stronie modelu języka i wybierając pozycję **Dodaj plik**.

### <a name="delete-files"></a>Usuwanie plików

Aby usunąć plik z modelu języka, zaznacz przycisk wielokropek**po**prawej stronie pliku tekstowego i wybierz pozycję **Usuń**. Pojawi się nowe okno z informacją, że usunięcia nie można cofnąć. Wybierz opcję **Usuń** w nowym oknie.

Ta akcja usuwa plik całkowicie z modelu języka.

## <a name="delete-a-language-model"></a>Usuwanie modelu języka

Aby usunąć model języka z konta, wybierz przycisk wielokropek**po**prawej stronie modelu języka i wybierz pozycję **Usuń**.

Pojawi się nowe okno z informacją, że usunięcia nie można cofnąć. Wybierz opcję **Usuń** w nowym oknie.

Ta akcja powoduje całkowite usunięcie modelu języka z konta. Każdy film, który był przy użyciu usuniętego modelu języka będzie zachować ten sam indeks, dopóki nie ponownie zindeksuj wideo. Jeśli ponownie zeksusz film, możesz przypisać nowy model języka do filmu. W przeciwnym razie indeksator wideo użyje swojego domyślnego modelu, aby ponownie wyeksliować wideo.

## <a name="customize-language-models-by-correcting-transcripts"></a>Dostosowywanie modeli językowych przez poprawianie transkrypcji

Indeksator wideo obsługuje automatyczne dostosowywanie modeli języka na podstawie rzeczywistych poprawek, które użytkownicy dokonują w transkrypcji swoich filmów.

1. Aby wprowadzić poprawki do transkrypcji, otwórz film, który chcesz edytować z filmów na koncie. Wybierz kartę **Oś czasu.**

    ![Dostosowywanie karty osi czasu modelu języka — indeksator wideo](./media/customize-language-model/timeline.png)

1. Wybierz ikonę ołówka, aby edytować transkrypcję transkrypcji.

    ![Dostosowywanie transkrypcji edycji modelu języka — indeksator wideo](./media/customize-language-model/edits.png)

    Indeksator wideo przechwytuje wszystkie wiersze, które są korygowane przez Ciebie w transkrypcji wideo i dodaje je automatycznie do pliku tekstowego o nazwie "Od edycji transkrypcji". Te zmiany są używane do ponownego przeszkolenia określonego modelu języka, który został użyty do indeksowania tego klipu wideo.
    
    Jeśli podczas indeksowania tego filmu nie określono modelu języka, wszystkie zmiany w tym filmie zostaną zapisane w domyślnym modelu języka o nazwie "Adaptacje konta" w wykrytym języku filmu.
    
    W przypadku, gdy wprowadzono wiele zmian w tym samym wierszu, tylko ostatnia wersja poprawionego wiersza będzie używana do aktualizacji modelu języka.  
    
    > [!NOTE]
    > Tylko korekty tekstowe są używane do dostosowywania. Poprawki, które nie obejmują rzeczywistych słów (na przykład znaków interpunkcyjnych lub spacji) nie są uwzględniane.
    
1. Poprawki transkrypcji pojawią się na karcie Język na stronie Dostosowywanie modelu zawartości.

    ![Dostosowywanie modelu języka — indeksator wideo](./media/customize-language-model/customize.png)

   Aby sprawdzić plik "Od edycji transkrypcji" dla każdego z modeli języka, wybierz go, aby go otworzyć.

    ![Od edycji transkrypcji — indeksator wideo](./media/customize-language-model/from-transcript-edits.png)

## <a name="next-steps"></a>Następne kroki

[Dostosowywanie modelu języka przy użyciu interfejsów API](customize-language-model-with-api.md)
