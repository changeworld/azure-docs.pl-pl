---
title: Dostosowywanie modeli języków za pomocą witryny sieci Web Video Indexer — Azure
titleSuffix: Azure Media Services
description: W tym artykule pokazano, jak dostosować model języka za pomocą witryny sieci Web Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 329da39914ef957d3a5376ba59e0c7103ad6a5dd
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513919"
---
# <a name="customize-a-language-model-with-the-video-indexer-website"></a>Dostosowywanie modelu języka za pomocą witryny sieci Web Video Indexer

Video Indexer umożliwia tworzenie niestandardowych modeli języka w celu dostosowania rozpoznawania mowy przez przekazywanie tekstu adaptacji, czyli tekstu z domeny, do którego słownika ma być dołączany aparat. Po przeprowadzeniu szkolenia modelu zostaną rozpoznane nowe wyrazy pojawiające się w tekście adaptacyjnym. 

Aby zapoznać się z szczegółowym omówieniem i najlepszymi rozwiązaniami dotyczącymi niestandardowych modeli języków, zobacz [Dostosowywanie modelu języka za pomocą Video Indexer](customize-language-model-overview.md).

Za pomocą witryny sieci Web Video Indexer można tworzyć i edytować niestandardowe modele języka na koncie, zgodnie z opisem w tym temacie. Możesz również użyć interfejsu API, zgodnie z opisem w temacie [Dostosowywanie modelu języka za pomocą interfejsów API](customize-language-model-with-api.md).

## <a name="create-a-language-model"></a>Tworzenie modelu języka

1. Przejdź do witryny internetowej [Video Indexer](https://www.videoindexer.ai/) i zaloguj się.
2. Aby dostosować model na koncie, kliknij przycisk **dostosowywania modelu zawartości** w prawym górnym rogu strony.

   ![Dostosuj model zawartości](./media/content-model-customization/content-model-customization.png)

3. Wybierz kartę **Język** .

    Zostanie wyświetlona lista obsługiwanych języków. 

    ![Dostosuj model języka](./media/customize-language-model/customize-language-model.png)

4. W obszarze język, który chcesz, kliknij pozycję **Dodaj model**.
5. Wpisz nazwę modelu języka i naciśnij klawisz ENTER.

    Spowoduje to utworzenie modelu i umożliwi przekazanie plików tekstowych do modelu.
6. Aby dodać plik tekstowy, kliknij przycisk **Dodaj plik**. Spowoduje to otwarcie Eksploratora plików.

7. Przejdź do i wybierz plik tekstowy. Można dodać wiele plików tekstowych do modelu języka.

    Możesz również dodać plik tekstowy, klikając przycisk **...** po prawej stronie modelu języka i wybierając pozycję **Dodaj plik**.
8. Po zakończeniu przekazywania plików tekstowych kliknij opcję zielony **pociąg** .

    ![Model języka uczenia](./media/customize-language-model/train-model.png)

Proces uczenia może potrwać kilka minut. Po zakończeniu szkolenia zobaczysz **przeszkolony** obok modelu. Możesz wyświetlić podgląd, pobrać i usunąć plik z modelu.

![Model przeszkolonego języka](./media/customize-language-model/preview-model.png)

### <a name="using-a-language-model-on-a-new-video"></a>Używanie modelu języka na nowym filmie wideo

Aby użyć modelu języka na nowym filmie wideo, wykonaj jedną z następujących czynności:

* Kliknij przycisk **Przekaż** w górnej części strony. 

    ![Przekazywanie](./media/customize-language-model/upload.png)
* Porzuć plik audio lub wideo w okręgu lub Wyszukaj plik

    ![Przekazywanie](./media/customize-language-model/upload2.png)

Umożliwi to wybranie **języka źródła wideo**. Kliknij listę rozwijaną i wybierz model języka, który został utworzony na podstawie listy. Powinien on wypowiedzieć język modelu języka i nazwę nadaną w nawiasach.

Kliknij opcję **Przekaż** w dolnej części strony, a nowe wideo zostanie indeksowane przy użyciu modelu języka.

### <a name="using-a-language-model-to-reindex"></a>Używanie modelu języka do ponownego indeksowania

Aby użyć modelu języka do ponownego indeksowania wideo w kolekcji, przejdź do swoich **filmów wideo** na stronie głównej [Video Indexer](https://www.videoindexer.ai/) i umieść kursor nad nazwą filmu wideo, który chcesz ponownie zindeksować.

Zobaczysz opcje edycji wideo, usuwania wideo i ponownego indeksowania wideo. Kliknij opcję, aby ponownie zindeksować wideo.

![Ponowna indeksacja](./media/customize-language-model/reindex1.png)

Dzięki temu można wybrać **język źródła wideo** do ponownego indeksowania wideo. Kliknij listę rozwijaną i wybierz model języka, który został utworzony na podstawie listy. Powinien on wypowiedzieć język modelu języka i nazwę nadaną w nawiasach.

![Ponowna indeksacja](./media/customize-language-model/reindex.png)

Kliknij przycisk **ponownego indeksowania** , a Twoje wideo zostanie ponownie indeksowane przy użyciu modelu języka.

## <a name="edit-a-language-model"></a>Edytowanie modelu języka

Można edytować model języka, zmieniając jego nazwę, dodając do niego pliki i usuwając z niego pliki.

W przypadku dodania lub usunięcia plików z modelu języka należy ponownie przeprowadzić uczenie modelu przez kliknięcie opcji zielony **pociąg** .

### <a name="rename-the-language-model"></a>Zmień nazwę modelu języka

Nazwę modelu języka można zmienić, klikając pozycję **...** po prawej stronie modelu języka i wybierając pozycję **Zmień nazwę**. 

Wpisz nową nazwę i naciśnij klawisz ENTER.

### <a name="add-files"></a>Dodaj pliki

Aby dodać plik tekstowy, kliknij przycisk **Dodaj plik**. Spowoduje to otwarcie Eksploratora plików.

Przejdź do i wybierz plik tekstowy. Można dodać wiele plików tekstowych do modelu języka.

Możesz również dodać plik tekstowy, klikając przycisk **...** po prawej stronie modelu języka i wybierając pozycję **Dodaj plik**.

### <a name="delete-files"></a>Usuwanie plików

Aby usunąć plik z modelu języka, kliknij przycisk **...** po prawej stronie pliku tekstowego, a następnie wybierz pozycję **Usuń**. Spowoduje to wyświetlenie nowego okna z informacją o tym, że nie można cofnąć operacji usuwania. Kliknij opcję **Usuń** w nowym oknie.

Ta akcja powoduje usunięcie pliku w całości z modelu języka.

## <a name="delete-a-language-model"></a>Usuwanie modelu języka

Aby usunąć model języka z konta, kliknij przycisk **...** po prawej stronie modelu języka, a następnie wybierz pozycję **Usuń**.

Spowoduje to wyświetlenie nowego okna z informacją o tym, że nie można cofnąć operacji usuwania. Kliknij opcję **Usuń** w nowym oknie.

Ta akcja spowoduje całkowite usunięcie modelu języka z Twojego konta. Wszystkie filmy wideo, które były używane w modelu języka, będą zachować ten sam indeks do momentu ponownego indeksowania wideo. Jeśli ponownie indeksujesz wideo, możesz przypisać nowy model języka do wideo. W przeciwnym razie Video Indexer będzie używać domyślnego modelu do ponownego indeksowania wideo. 

## <a name="customize-language-models-by-correcting-transcripts"></a>Dostosowywanie modeli języków przez skorygowanie transkrypcji

Video Indexer obsługuje automatyczne dostosowywanie modeli języka na podstawie rzeczywistych poprawek, które użytkownicy wprowadzili do transkrypcji filmów wideo.

1. Aby wprowadzić poprawki do transkrypcji, Otwórz film wideo, który chcesz edytować z poziomu wideo na koncie. Wybierz kartę **oś czasu** .

    ![Dostosuj model języka](./media/customize-language-model/timeline.png)
1. Kliknij ikonę ołówka, aby edytować transkrypcję transkrypcji. 

    ![Dostosuj model języka](./media/customize-language-model/edits.png)

    Video Indexer przechwytuje wszystkie wiersze, które są poprawione przez użytkownika w transkrypcji wideo, i automatycznie dodaje je do pliku tekstowego o nazwie "z edycji transkrypcji". Te zmiany są używane do ponownego uczenia określonego modelu języka, który został użyty do indeksowania tego wideo. 
    
    Jeśli nie określisz modelu języka podczas indeksowania tego wideo, wszystkie zmiany tego wideo będą przechowywane w domyślnym modelu języka o nazwie dostosowania konta w wykrytym języku wideo. 
    
    W przypadku dokonania wielokrotnych zmian w tym samym wierszu do zaktualizowania modelu języka zostanie użyta tylko Ostatnia wersja poprawionego wiersza.  
    
    > [!NOTE]
    > Do dostosowania są używane tylko poprawki tekstowe. Oznacza to, że poprawki, które nie zawierają rzeczywistych wyrazów (na przykład znaki interpunkcyjne lub spacje) nie są uwzględniane. 
    
1. Pojawią się poprawki transkrypcji są wyświetlane na karcie język na stronie dostosowywania modelu zawartości.

    ![Dostosuj model języka](./media/customize-language-model/customize.png)

   Aby przyjrzeć się plikowi "from transkrypcji edycji" dla każdego z modeli języka, kliknij go, aby go otworzyć. 

    ![Z edycji transkrypcji](./media/customize-language-model/from-transcript-edits.png)

## <a name="next-steps"></a>Następne kroki

[Dostosowywanie modelu języka przy użyciu interfejsów API](customize-language-model-with-api.md)
