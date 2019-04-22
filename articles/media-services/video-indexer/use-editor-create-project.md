---
title: Tworzenie projektów za pomocą edytora indeksatora wideo
titlesuffix: Azure Media Services
description: W tym temacie przedstawiono sposób tworzenia projektów za pomocą edytora Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 04/02/2019
ms.author: juliako
ms.openlocfilehash: a9d6396cab560a201b98497e787af4b6c7c2dabb
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58896652"
---
# <a name="use-the-video-indexer-editor-to-create-projects"></a>Tworzenie projektów za pomocą edytora indeksatora wideo

Witryny indeksatora wideo umożliwia pogłębione analizy plików wideo do: znajdowanie zawartości multimedialnej prawo, Znajdź części interesuje i użyj wyników, aby utworzyć zupełnie nowy projekt. Po utworzeniu projektu może być renderowana i pobierane z Video Indexer i być używane w własnych edycji aplikacji lub podrzędnego przepływów pracy.

Są sytuacje, w którym przydatnych tej funkcji: 

* Tworzenie film prezentuje dla przyczepy.
* Przy użyciu starego klipy wideo w wiadomości rzutowania.
* Tworzenie zawartości krótszy związanych z mediami społecznościowymi.

W tym artykule przedstawiono, jak utworzyć projekt od podstaw, a także jak utworzyć projekt z filmu wideo w ramach Twojego konta.

## <a name="create-new-project-and-manage-videos"></a>Utwórz nowy projekt wideo oraz zarządzanie nimi

1. Przejdź do witryny internetowej [Video Indexer](https://www.videoindexer.ai/) i zaloguj się.
1. Wybierz **projektów** kartę. Jeśli utworzono projekty zanim zobaczysz wszystkich innych projektach, w tym miejscu.
1. Kliknij przycisk **Tworzenie nowego projektu**.  

    ![Nowy projekt](./media/video-indexer-view-edit/new-project.png)
1. Nadaj projektowi nazwę, klikając ikonę ołówka. Zamień tekst, który jest wyświetlany komunikat "Bez nazwy projektu" na nazwę projektu, a następnie kliknij pozycję wyboru.

    ![Nowy projekt](./media/video-indexer-view-edit/new-project3.png)
    
### <a name="add-videos-to-the-project"></a>Dodawanie wideo do projektu

> [!NOTE]
> Obecnie projektów może zawierać tylko filmy wideo indeksowane w tym samym języku. Po wybraniu wideo w jednym języku, nie można dodać wideo na swoim koncie, które znajdują się w innym języku.

1. Dodawanie filmów wideo, które chcesz pracować w tym projekcie, wybierając **dodawać filmy wideo**.

    Widoczne będą wszystkie filmy wideo dotyczące koncie i pola wyszukiwania, który jest wyświetlany komunikat "Wyszukaj tekst, słowa kluczowe lub zawartości wizualnej". Aby wyszukać filmy wideo, które mają określona osoba, etykiety, markę, słowo kluczowe lub wystąpienia w transkrypcji i optyczne rozpoznawanie znaków.
    
    Na przykład na poniższej ilustracji, firma Microsoft szuka plików wideo, w których występuje "GitHub".
    
    ![GitHub](./media/video-indexer-view-edit/github.png)

    Wyniki można dalej filtrować, wybierając **filtrowanie wyników**. Można filtrować, aby wyświetlić wideo, które mają określone osoby w nich lub aby określić, czy chcesz wyświetlić wyniki wideo, które są w języku niektórych lub ma określonego właściciela. <br/> Można również określić zakres kwerendy. Na przykład jeśli chcesz wyszukać "GitHub" optyczne rozpoznawanie znaków, wybierz pozycję **tekstu**.

    ![Filtr](./media/video-indexer-view-edit/visual-text.png)

    W warstwie wiele filtrów do zapytania. Użyj **+** / **-** przycisków, aby dodawać i usuwać filtry. Użyj **Wyczyść filtry** Aby usunąć wszystkie filtry.
1. Aby dodawać filmy wideo, zaznacz je, a następnie wybierz **Dodaj**.
1. Teraz będą widoczne wszystkie filmy wideo, który został wybrany. Są to wideo, z których ma być wybierz klipy w projekcie.

    Można zmienić kolejność filmy wideo, przeciągając i upuszczając lub przycisku menu listy a zaznaczając **Przenieś w dół** lub **Przenieś w górę**. Z menu listy również będzie można usunąć wideo z tego projektu. 

    ![Rozmieszczanie](./media/video-indexer-view-edit/rearrange.png)
    
    Masz opcję, aby dodać więcej filmów wideo do tego projektu w dowolnym momencie, wybierając **dodawać filmy wideo**. Wiele wystąpień tego samego film wideo można również dodać do projektu. Można to zrobić, jeśli chcesz pokazać klip z jednego wideo, a następnie klipu z innego i następnie inny klip z pierwszego filmu wideo. 

### <a name="select-clips-to-use-in-your-project"></a>Wybierz klipy do wykorzystywania w projekcie

Po kliknięciu strzałki w dół po prawej stronie każdego filmu wideo będzie otwierają szczegółowych informacji w trakcie filmu wideo, oparte na sygnatury czasowe (klipy wideo). 

1. Wybierz **Wyświetl szczegółowe dane** dostosować informacje, które chcesz znaleźć oraz tych, które nie chcesz wyświetlić. 

    ![Wyświetl szczegółowe informacje](./media/video-indexer-view-edit/insights.png)
1. Aby utworzyć zapytania dotyczące określonych klipy wideo, użyj pola wyszukiwania, informujący, że "ciąg Wyszukiwanie transkrypcji, tekstu, osoby i etykiety".
1. Dodaj filtry, aby bardziej szczegółowo określić szczegóły, w jaki scen, którego szukasz, wybierając **opcje filtru**.

    ![Opcje filtru](./media/video-indexer-view-edit/filter-options.png)

    Na przykład można wyświetlić klipy gdzie GitHub jest wymieniony w trakcie Donovan Brown na ekranie. W tym celu należy dodać filtr "include", która ma "Ludzie" jako typ szczegółowe informacje. Następnie należy wpisać "Donovan Brown" w polu wyszukiwania dla filtru.
    
    ![Dołącz](./media/video-indexer-view-edit/include.png)
    
    Jeśli chcesz, klipy wideo, gdzie GitHub jest wymieniony w trakcie Donovan Brown _nie_ na ekranie, należy po prostu zmienić filtru "include" do filtru "Wyklucz", korzystając z listy rozwijanej. 

1. Dodaj klip do projektu, wybierając pozycję segment, który chcesz dodać. Możesz usunąć zaznaczenie ten klip, klikając na segmencie ponownie.
    
    Dodaj wszystkie segmenty filmu wideo, klikając opcję menu listy obok wideo i wybierając opcję **wybierz wszystkie segmenty**. 

    ![Dodaj wszystkie](./media/video-indexer-view-edit/add-all.png)

    Możesz wyczyścić wszystkie zaznaczenia, wybierając pozycję Wyczyść zaznaczenie.

> [!TIP]
> Jak zaznaczania i kolejność klipów, możesz wyświetlić podgląd wideo w odtwarzaczu w prawej części strony. 

![Wersja zapoznawcza](./media/video-indexer-view-edit/preview.png)

Pamiętaj, aby zapisać projekt, wprowadzając zmiany, wybierając **Zapisz projekt**. 

### <a name="render-and-download-the-project"></a>Renderowanie i pobrać projekt

> [!NOTE]
> Video Indexer płatnych konta renderowanie projekt ma kodowanie kosztów. Wideo kontami próbnymi indeksatora są ograniczone do 5 godzin renderowania.

1. Gdy wszystko będzie gotowe, upewnij się, że projekt został zapisany. Teraz można renderować tego projektu. Wybierz **renderowania i Pobierz**. 

    ![Zapisz](./media/video-indexer-view-edit/save.png)

    Będą dostępne okno podręczne informujące o tym, że Video indexer będzie renderowany pliku i link pobierania będzie wysyłania pocztą e-mail. Wybierz pozycję Kontynuuj. 
    
    Zobaczysz również powiadomienie, że projekt jest renderowany na górze strony. Po zakończeniu są renderowane, będzie wyświetlane nowe powiadomienie, który pomyślnie renderowany projektu. Kliknij powiadomienie, aby wczytać projekt. Pobierze projektu w formacie mp4.

    ![Renderowanie gotowe](./media/video-indexer-view-edit/rendering-done.png)

1. Możesz uzyskać dostęp zapisanych projektów z **projektów** kartę. 

    Wybranie tego projektu, zobaczysz wszystkie szczegółowe informacje i osi czasu w tym projekcie. Jeśli wybierzesz **edytor wideo**, możesz kontynuować wprowadzanie zmian do tego projektu. Zmiany obejmują dodawanie lub usuwanie, materiały wideo i klipów i zmiana nazwy projektu.

    ![Edytor wideo](./media/video-indexer-view-edit/video-editor.png)
     
## <a name="create-a-project-from-your-video"></a>Tworzenie projektu z filmu wideo

Można utworzyć nowego projektu bezpośrednio z filmu wideo w ramach Twojego konta. 

1. Przejdź do **biblioteki** kartę indeksatora wideo w witrynie sieci Web.
1. Otwórz film wideo, który chcesz użyć do utworzenia projektu. Na stronie oś czasu i szczegółowe informacje, wybierz **edytor wideo** przycisku.

    Spowoduje to przejście do tej samej strony, który został użyty do utworzenia nowego projektu. W przeciwieństwie do nowego projektu zostanie wyświetlony segmentów insights oznaczony sygnaturą czasową filmu wideo, który zostało uruchomione, edytowanie wcześniej.

## <a name="see-also"></a>Zobacz także

[Omówienie usługi Video Indexer](video-indexer-overview.md)

