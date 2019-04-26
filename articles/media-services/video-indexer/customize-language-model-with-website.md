---
title: Użyć Video Indexer witryny sieci Web, aby dostosować model języka - Azure
titlesuffix: Azure Media Services
description: W tym artykule pokazano, jak dostosować model języka, za pomocą indeksatora wideo witryny sieci Web.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: anzaman
ms.openlocfilehash: 7b81b53c03104023823bef75beb4ac6077feede7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60555437"
---
# <a name="customize-a-language-model-with-the-video-indexer-website"></a>Dostosuj model języka, za pomocą witryny sieci Web indeksatora wideo

Usługa Video Indexer umożliwia tworzenie niestandardowych modeli językowych, aby dostosować rozpoznawania mowy, przekazując adaptacji tekstu, a mianowicie tekstu z domeny słownictwo, którego chcesz aparatu, aby dostosować je do. Po użytkownik nauczenia modelu, nowych słów, które pojawiają się w tekście adaptacji zostanie rozpoznana. 

Aby uzyskać szczegółowe omówienie i najlepsze rozwiązania dotyczące niestandardowych modeli językowych, zobacz [dostosować model języka, za pomocą indeksatora wideo](customize-language-model-overview.md).

Video Indexer witryny sieci Web służy do tworzenia i edytowania niestandardowych modeli językowych ze swojego konta, zgodnie z opisem w tym temacie. Możesz również użyć interfejsu API, zgodnie z opisem w [Dostosowywanie języka modelu przy użyciu interfejsów API](customize-language-model-with-api.md).

## <a name="create-a-language-model"></a>Tworzenie modelu języka

1. Przejdź do witryny internetowej [Video Indexer](https://www.videoindexer.ai/) i zaloguj się.
2. Aby dostosować model na Twoim koncie, wybierz polecenie **zawartości Dostosowywanie modelu** przycisk w prawym górnym rogu strony.

   ![Dostosowywanie modelu zawartości](./media/content-model-customization/content-model-customization.png)

3. Wybierz **języka** kartę.

    Zobaczysz listę obsługiwanych języków. 

    ![Dostosuj model języka](./media/customize-language-model/customize-language-model.png)

4. W obszarze język, który ma, kliknij **modelu Dodaj**.
5. Wpisz nazwę dla modelu językowego i naciśnij klawisz enter.

    Umożliwia utworzenie modelu i udostępnia opcję przekazania plików tekstowych w modelu.
6. Aby dodać plik tekstowy, kliknij **Dodaj plik**. Spowoduje to otwarcie Eksploratora plików.

7. Przejdź do, a następnie wybierz plik tekstowy. Model języka, można dodać wiele plików.

    Można również dodać plik tekstowy, klikając **...**  znajdujący się po prawej stronie modelu językowego i wybierając **Dodaj plik**.
8. Po zakończeniu przekazywania plików tekstowych, kliknij zielony **Train** opcji.

    ![Train model języka](./media/customize-language-model/train-model.png)

Szkolenie może potrwać kilka minut. Po zakończeniu szkolenia **Trained** obok modelu. Można w wersji zapoznawczej, Pobierz i usunięcie pliku z modelu.

![Model języka przeszkolonych](./media/customize-language-model/preview-model.png)

### <a name="using-a-language-model-on-a-new-video"></a>Za pomocą modelu języka na nowy film wideo

Aby użyć Twojego modelu językowego na nowy film wideo, wykonaj jedną z następujących czynności:

* Kliknij pozycję **przekazywanie** przycisku w górnej części strony 

    ![Upload](./media/customize-language-model/upload.png)
* Usuń z pliku audio lub wideo w okręgu lub Przeglądaj w poszukiwaniu pliku

    ![Upload](./media/customize-language-model/upload2.png)

Zapewni to możliwość dokonania wyboru **języka źródłowego wideo**. Kliknij listę rozwijaną i wybierz model języka, który został utworzony z listy. Powinien on Załóżmy, że język Twojego modelu językowego i nazwę, która udostępniła je w nawiasach.

Kliknij przycisk **przekazywanie** opcję w dolnej części strony i nowy film wideo będzie indeksowany przy użyciu Twojego modelu językowego.

### <a name="using-a-language-model-to-reindex"></a>Ponowna indeksacja przy użyciu modelu języka

Aby użyć Twojego modelu językowego do ponownego poindeksowania danych wideo w kolekcji, przejdź do usługi **konta wideo** na [Video Indexer](https://www.videoindexer.ai/) strony głównej i umieść kursor na nazwie film wideo, który ma być ponowna indeksacja.

Zostanie wyświetlony opcje edytowania filmu wideo, usunięcie wideo i ponowna indeksacja filmu wideo. Kliknij opcję ponownego poindeksowania danych filmu wideo.

![Ponowna indeksacja](./media/customize-language-model/reindex1.png)

Daje to możliwość dokonania wyboru **języka źródłowego wideo** indeksowania wideo. Kliknij listę rozwijaną i wybierz model języka, który został utworzony z listy. Powinien on Załóżmy, że język Twojego modelu językowego i nazwę, która udostępniła je w nawiasach.

![Ponowna indeksacja](./media/customize-language-model/reindex.png)

Kliknij przycisk **ponownie poindeksuj** przycisk, a Twój film wideo będzie można ponownie indeksowane za pomocą modelu językowego.

## <a name="edit-a-language-model"></a>Edytuj model języka

Można edytować model języka, zmiana jego nazwy, dodawanie plików do niego i usuwanie plików z niego.

Jeśli dodasz lub Usuń pliki z modelu językowego trzeba będzie ponownie nauczenia modelu, klikając przycisk w kolorze zielonym **szkolenie** opcji.

### <a name="rename-the-language-model"></a>Zmień nazwę modelu językowego

Nazwa modelu językowego można zmienić, klikając **...**  po prawej stronie język modelu i wybierając polecenie **Zmień nazwę**. 

Wpisz nową nazwę i kliknij przycisk Wprowadź.

### <a name="add-files"></a>Dodaj pliki

Aby dodać plik tekstowy, kliknij **Dodaj plik**. Spowoduje to otwarcie Eksploratora plików.

Przejdź do, a następnie wybierz plik tekstowy. Model języka, można dodać wiele plików.

Można również dodać plik tekstowy, klikając **...**  znajdujący się po prawej stronie modelu językowego i wybierając **Dodaj plik**.

### <a name="delete-files"></a>Usuwanie plików

Aby usunąć plik z modelu języka, kliknij przycisk **...**  przycisk po prawej stronie pliku tekstowego, a następnie wybierz pozycję **Usuń**. Spowoduje to przejście nowe okno informujące o tym, że usunięcie nie można cofnąć. Kliknij przycisk **Usuń** opcji w nowym oknie.

Ta akcja usuwa plik całkowicie z modelu językowego.

## <a name="delete-a-language-model"></a>Usuń model języka

Aby usunąć model języka z Twojego konta, kliknij przycisk **...**  przycisk po prawej stronie modelu językowego i wybierz **Usuń**.

Spowoduje to przejście nowe okno informujące o tym, że usunięcie nie można cofnąć. Kliknij przycisk **Usuń** opcji w nowym oknie.

Ta akcja usuwa model języka całkowicie z Twojego konta. Wszelkie film wideo, który używał usunięto model języka spowoduje zachowanie tego samego indeksu, do momentu ponownego indeksowania wideo. Jeśli ponownie poindeksuj wideo, można przypisać nowego modelu języka filmu wideo. W przeciwnym razie usługa Video Indexer użyje jej domyślny model ponownego indeksowania filmu wideo. 

## <a name="customize-language-models-by-correcting-transcripts"></a>Dostosowywanie modeli językowych, poprawiając transkrypcji

Usługa Video Indexer obsługuje automatyczne dostosowywanie języka modeli na podstawie użytkowników poprawki rzeczywiste dokonać transkrypcje plików wideo.

1. Aby wprowadzić korekty do zapisu, otwórz film wideo, który chcesz edytować konto przetwarzania filmów wideo. Wybierz **osi czasu** kartę.

    ![Dostosuj model języka](./media/customize-language-model/timeline.png)
1. Kliknij ikonę ołówka, aby edytować transkrypcję dla Twojego transkrypcji. 

    ![Dostosuj model języka](./media/customize-language-model/edits.png)

    Usługa Video Indexer przechwytuje wszystkie wiersze, które są usuwane przez Ciebie w transkrypcji wideo i automatycznie dodaje je do pliku tekstowego o nazwie "od transkrypcji zmiany". Te zmiany są używane do ponownego trenowania określonych języka użytego do indeksu w tym wideo. 
    
    Jeśli nie określono modelu języka podczas indeksowania w tym wideo, wszystkie zmiany dla tego wideo będą przechowywane w domyślnego języka modelu o nazwie dostosowania w ramach wykryty język filmu wideo. 
    
    W przypadku, gdy wprowadzono wiele zmian tej samej linii, tylko ostatnia wersja poprawiony wiersza będzie służyć do aktualizacji modelu językowego.  
    
    > [!NOTE]
    > Tylko tekstowy korekty są używane do dostosowywania. Oznacza to, że poprawki, które nie wymagają rzeczywistymi słowami (na przykład znaków interpunkcyjnych lub miejsca do magazynowania) nie są uwzględniane. 
    
1. Zostanie wyświetlony poprawki transkrypcji pojawiają się w karcie języka strony dostosowania modelu zawartości.

    ![Dostosuj model języka](./media/customize-language-model/customize.png)

   Aby zobaczyć plik "od zmiany transkrypcji" dla każdego z modeli językowych, kliknij go, aby go otworzyć. 

    ![Z edycji transkrypcji](./media/customize-language-model/from-transcript-edits.png)

## <a name="next-steps"></a>Kolejne kroki

[Dostosuj model języka, za pomocą interfejsów API](customize-language-model-with-api.md)
