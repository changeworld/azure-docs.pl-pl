---
title: Tworzenie projektów za pomocą edytora indeksatorów wideo
titleSuffix: Azure Media Services
description: W tym temacie pokazano, jak używać edytora indeksatora wideo do tworzenia projektów.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/02/2019
ms.author: juliako
ms.openlocfilehash: 9f16ab34dc9b37806f9c58b22a3f02afe839632e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73839166"
---
# <a name="use-the-video-indexer-editor-to-create-projects"></a>Tworzenie projektów za pomocą edytora indeksatorów wideo

Witryna video Indexer umożliwia wykorzystanie szczegółowych informacji o filmach do: znajdowania odpowiednich treści multimedialnych, lokalizowania interesujących Cię części i wykorzystywania wyników do tworzenia zupełnie nowego projektu. Po utworzeniu projekt może być renderowany i pobierany z video indexer i być używany we własnych aplikacjach do edycji lub przepływach pracy podrzędnych.

Oto kilka scenariuszy, w których ta funkcja może okazać się przydatna: 

* Tworzenie najważniejszych filmów dla zwiastunów.
* Korzystanie ze starych klipów wideo w rzutach wiadomości.
* Tworzenie krótszych treści dla mediów społecznościowych.

W tym artykule pokazano, jak utworzyć projekt od podstaw, a także jak utworzyć projekt z filmu wideo na koncie.

## <a name="create-new-project-and-manage-videos"></a>Tworzenie nowego projektu i zarządzanie filmami

1. Przejdź do witryny internetowej [Video Indexer](https://www.videoindexer.ai/) i zaloguj się.
1. Wybierz kartę **Projekty.** Jeśli wcześniej tworzyłeś projekty, zobaczysz tutaj wszystkie inne projekty.
1. Kliknij **pozycję Utwórz nowy projekt**.  

    ![Nowy projekt](./media/video-indexer-view-edit/new-project.png)
1. Nadaj projektowi nazwę, klikając ikonę ołówka. Zastąp tekst z napisem "Projekt bez tytułu" nazwą projektu i kliknij czek.

    ![Nowy projekt](./media/video-indexer-view-edit/new-project3.png)
    
### <a name="add-videos-to-the-project"></a>Dodawanie filmów do projektu

> [!NOTE]
> Obecnie projekty mogą zawierać tylko filmy indeksowane w tym samym języku. Po wybraniu filmu w jednym języku nie możesz dodać filmów na koncie, które są w innym języku.

1. Dodaj filmy, z którymi chcesz pracować w tym projekcie, wybierając **pozycję Dodaj filmy**.

    Zobaczysz wszystkie filmy na koncie i pole wyszukiwania z napisem "Wyszukaj tekst, słowa kluczowe lub treści wizualne". Aby wyszukać filmy, które mają określoną osobę, etykietę, markę, słowo kluczowe lub wystąpienie w transkrypcji i OCR.
    
    Na przykład na poniższym obrazku szukamy filmów, które wspominają o "GitHub".
    
    ![GitHub](./media/video-indexer-view-edit/github.png)

    Wyniki można dodatkowo filtrować, wybierając pozycję **Filtruj wyniki**. Możesz filtrować filmy, które mają określoną osobę lub określić, że chcesz zobaczyć tylko wyniki wideo, które są w określonym języku lub mają określonego właściciela. <br/> Można również określić zakres kwerendy. Na przykład, jeśli chcesz wyszukać "GitHub" w OCR, wybierz tekst **wizualny**.

    ![Filtr](./media/video-indexer-view-edit/visual-text.png)

    Do zapytania można nakładać wiele filtrów. Użyj **+** / **-** przycisków, aby dodać/usunąć filtry. Użyj **wyczyść filtry,** aby usunąć wszystkie filtry.
1. Aby dodać filmy, zaznacz je, a następnie wybierz pozycję **Dodaj**.
1. Teraz zobaczysz wszystkie wybrane filmy. Są to filmy, z których masz zamiar wybrać klipy do projektu.

    Kolejność filmów można zmienić, przeciągając i upuszczając lub wybierając przycisk menu listy i wybierając **pozycję Przenieś w dół** lub Przenieś w **górę**. Z menu listy będzie można również usunąć film z tego projektu. 

    ![Zmienić](./media/video-indexer-view-edit/rearrange.png)
    
    W dowolnym momencie możesz dodać więcej filmów do tego projektu, wybierając pozycję **Dodaj filmy**. Można również dodać wiele wystąpień tego samego filmu do projektu. Możesz to zrobić, jeśli chcesz pokazać klip z jednego filmu, a następnie klip z innego, a następnie inny klip z pierwszego filmu. 

### <a name="select-clips-to-use-in-your-project"></a>Wybieranie klipów do użycia w projekcie

Jeśli klikniesz strzałkę w dół po prawej stronie każdego filmu, otworzysz statystyki w filmie na podstawie znaczników czasu (klipów wideo). 

1. Wybierz **pozycję Wyświetl statystyki,** aby dostosować statystyki, które chcesz wyświetlić, a które nie chcesz wyświetlać. 

    ![Wyświetl szczegółowe informacje](./media/video-indexer-view-edit/insights.png)
1. Aby utworzyć zapytania dotyczące określonych klipów, użyj pola wyszukiwania z napisem "Szukaj w transkrypcji, tekście wizualnym, osobach i etykietach".
1. Dodaj filtry, aby dokładniej określić szczegóły dotyczące szukanych scen, wybierając **opcję Filtruj**.

    ![Opcje filtru](./media/video-indexer-view-edit/filter-options.png)

    Na przykład możesz chcieć zobaczyć klipy, w których gitHub jest wymieniony, gdy Donovan Brown jest na ekranie. W tym celu należy dodać filtr "include", który ma "Osoby" jako typ wglądu. Następnie należy wpisać "Donovan Brown" w polu wyszukiwania filtru.
    
    ![Uwzględnij](./media/video-indexer-view-edit/include.png)
    
    Jeśli chcesz klipy, w których GitHub jest wymieniony, podczas gdy Donovan Brown _nie_ jest na ekranie, po prostu zmień filtr "include" w filtr "wyklucz" za pomocą listy rozwijanej. 

1. Dodaj klip do projektu, wybierając segment, który chcesz dodać. Możesz usunąć zaznaczenie tego klipu, klikając ponownie segment.
    
    Dodaj wszystkie segmenty filmu, klikając opcję menu listy obok filmu i wybierając **pozycję Zaznacz wszystkie segmenty**. 

    ![Dodaj wszystkie](./media/video-indexer-view-edit/add-all.png)

    Wszystkie zaznaczenia można wyczyścić, wybierając pozycję Wyczyść zaznaczenie.

> [!TIP]
> Podczas wybierania i zamawiania klipów można wyświetlić podgląd wideo w odtwarzaczu po prawej stronie strony. 

![Wersja zapoznawcza](./media/video-indexer-view-edit/preview.png)

Pamiętaj, aby zapisać projekt podczas wprowadzania zmian, wybierając **pozycję Zapisz projekt**. 

### <a name="render-and-download-the-project"></a>Renderowanie i pobieranie projektu

> [!NOTE]
> W przypadku płatnych kont indeksatora wideo renderowanie projektu ma koszty kodowania. Konta próbne indeksatora wideo są ograniczone do 5 godzin renderowania.

1. Po zakończeniu upewnij się, że projekt został zapisany. Teraz można renderować ten projekt. Wybierz **pozycję Renderuj i pobieraj**. 

    ![Zapisz](./media/video-indexer-view-edit/save.png)

    Pojawi się wyskakujące okienko z napisem, że indeksator wideo renderuje plik, a następnie link do pobrania zostanie wysłany na twój adres e-mail. Wybierz pozycję Kontynuuj. 
    
    Zobaczysz również powiadomienie, że projekt jest renderowany na górze strony. Po zakończeniu renderowania zostanie wyświetlone nowe powiadomienie, że projekt został pomyślnie renderowany. Kliknij powiadomienie, aby pobrać projekt. Pobierze projekt w formacie mp4.

    ![Renderowanie gotowe](./media/video-indexer-view-edit/rendering-done.png)

1. Dostęp do zapisanych projektów można uzyskać na karcie **Projekty.** 

    Jeśli wybierzesz ten projekt, zobaczysz wszystkie szczegółowe informacje i oś czasu tego projektu. Jeśli wybierzesz **Edytor wideo,** możesz kontynuować wprowadzanie zmian w tym projekcie. Zmiany obejmują dodawanie lub usuwanie filmów i klipów lub zmianę nazwy projektu.

    ![Edytor wideo](./media/video-indexer-view-edit/video-editor.png)
     
## <a name="create-a-project-from-your-video"></a>Tworzenie projektu na podstawie filmu

Możesz utworzyć nowy projekt bezpośrednio z filmu na swoim koncie. 

1. Przejdź do karty **Biblioteka** w witrynie indeksatora wideo.
1. Otwórz klip wideo, którego chcesz użyć do utworzenia projektu. Na stronie statystyki i osi czasu wybierz przycisk **Edytor wideo.**

    Spowoduje to przejście do tej samej strony, która została użyta do utworzenia nowego projektu. W przeciwieństwie do nowego projektu zobaczysz segmenty szczegółowych informacji o czasie, które zostały wcześniej rozpoczęte edycji.

## <a name="see-also"></a>Zobacz też

[Omówienie usługi Video Indexer](video-indexer-overview.md)

