---
title: Dostosowywanie modelu marki za pomocą witryny video Indexer
titleSuffix: Azure Media Services
description: Dowiedz się, jak dostosować model marki za pomocą witryny wideo indeksatora.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 81df3897dff13823e4b97e10bc91d3a22b0e1b0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128041"
---
# <a name="customize-a-brands-model-with-the-video-indexer-website"></a>Dostosowywanie modelu marki za pomocą witryny video Indexer

Indeksator wideo obsługuje wykrywanie marki z mowy i tekstu wizualnego podczas indeksowania i ponownego indeksowania zawartości wideo i audio. Funkcja wykrywania marki identyfikuje wzmianki o produktach, usługach i firmach sugerowane przez bazę danych marek Bing. Na przykład jeśli firma Microsoft jest wymieniona w zawartości wideo lub audio lub jeśli jest wyświetlana w tekście wizualnym w filmie wideo, indeksator wideo wykrywa go jako markę w zawartości.

Niestandardowy model marki umożliwia:

- wybierz, jeśli chcesz, aby indeksator wideo wykrywał marki z bazy danych marek Bing.
- wybierz, jeśli chcesz, aby indeksator wideo wykluczał niektóre marki z wykrywania (zasadniczo tworząc listę odrzucających marek).
- wybierz, jeśli chcesz, aby indeksator wideo zawierał marki, które powinny być częścią twojego modelu, które mogą nie znajdować się w bazie danych marek Bing (zasadniczo tworząc listę akceptowań marek).

Aby uzyskać szczegółowe informacje, zobacz ten [przegląd](customize-brands-model-overview.md).

Za pomocą witryny video Indexer można tworzyć, używać i edytować niestandardowe modele marek wykryte w klipie wideo, zgodnie z opisem w tym temacie. Można również użyć interfejsu API, zgodnie z opisem w [modelu Dostosuj marki przy użyciu interfejsów API](customize-brands-model-with-api.md).

## <a name="edit-brands-model-settings"></a>Edytuj ustawienia modelu Marki

Masz możliwość, aby ustawić, czy chcesz, aby marki z bazy danych marek Bing mają być wykrywane. Aby ustawić tę opcję, musisz edytować ustawienia modelu marki. Wykonaj następujące kroki:

1. Przejdź do witryny [indeksatora](https://www.videoindexer.ai/) wideo i zaloguj się.
2. Aby dostosować model na koncie, wybierz przycisk **Dostosowywanie modelu zawartości** w prawym górnym rogu strony.

   ![Dostosowywanie modelu zawartości w indeksatorze wideo](./media/content-model-customization/content-model-customization.png)

3. Aby edytować marki, wybierz kartę **Marki.**

    ![Dostosowywanie modelu marek w indeksatorze wideo](./media/customize-brand-model/customize-brand-model.png)

4. Zaznacz opcję **Pokaż marki sugerowane przez Bing,** jeśli chcesz, aby indeksator wideo wykrywał marki sugerowane przez Bing — pozostaw tę opcję niezaznaczone, jeśli tego nie zrobisz.

## <a name="include-brands-in-the-model"></a>Uwzględnij marki w modelu

Sekcja **Uwzględnij marki** reprezentuje niestandardowe marki, które chcesz, aby indeksator wideo wykrywał, nawet jeśli nie są one sugerowane przez bing.  

### <a name="add-a-brand-to-include-list"></a>Dodawanie marki w celu uwzględnienia listy

1. Wybierz **+ Dodaj markę**.

    ![Dostosowywanie modelu marek w indeksatorze wideo](./media/customize-brand-model/add-brand.png)

    Podaj nazwę (wymaganą), kategorię (opcjonalnie), opis (opcjonalnie) i adres URL odwołania (opcjonalnie).
    Pole kategorii ma na celu pomóc Ci oznaczyć marki. To pole jest wyświetlane jako tagi marki podczas korzystania z *interfejsów* API indeksatora wideo. Na przykład marki "Azure" można oznaczyć lub sklasyfikować jako "Chmura".

    Pole adresu URL odniesienia może być dowolną stroną referencyjną dla marki (np. link do jej strony w Wikipedii).

2. Wybierz **pozycję Dodaj markę,** a zobaczysz, że marka została dodana do listy **Dołącz marki.**

### <a name="edit-a-brand-on-the-include-list"></a>Edytowanie marki na liście dołączania

1. Wybierz ikonę ołówka obok marki, którą chcesz edytować.

    Możesz zaktualizować kategorię, opis lub adres URL odwołania marki. Nie można zmienić nazwy marki, ponieważ nazwy marek są unikatowe. Jeśli chcesz zmienić nazwę marki, usuń całą markę (patrz następna sekcja) i utwórz nową markę o nowej nazwie.

2. Wybierz przycisk **Aktualizuj,** aby zaktualizować markę o nowe informacje.

### <a name="delete-a-brand-on-the-include-list"></a>Usuwanie marki na liście dołączania

1. Wybierz ikonę kosza obok marki, którą chcesz usunąć.
2. Wybierz **pozycję Usuń,** a marka nie będzie już wyświetlana na liście *Dołącz marki.*

## <a name="exclude-brands-from-the-model"></a>Wykluczanie marek z modelu

Sekcja **Wyklucz marki** reprezentuje marki, których nie chcesz wykrywać indeksatora wideo.

### <a name="add-a-brand-to-exclude-list"></a>Dodawanie marki w celu wykluczenia listy

1. Wybierz **+ Dodaj markę.**

    Podaj nazwę (wymaganą), kategorię (opcjonalnie).

2. Wybierz **pozycję Dodaj markę,** a zobaczysz, że marka została dodana do listy *Wyklucz marki.*

### <a name="edit-a-brand-on-the-exclude-list"></a>Edytowanie marki na liście wykluczeń

1. Wybierz ikonę ołówka obok marki, którą chcesz edytować.

    Możesz zaktualizować tylko kategorię marki. Nie można zmienić nazwy marki, ponieważ nazwy marek są unikatowe. Jeśli chcesz zmienić nazwę marki, usuń całą markę (patrz następna sekcja) i utwórz nową markę o nowej nazwie.

2. Wybierz przycisk **Aktualizuj,** aby zaktualizować markę o nowe informacje.

### <a name="delete-a-brand-on-the-exclude-list"></a>Usuwanie marki na liście wykluczeń

1. Wybierz ikonę kosza obok marki, którą chcesz usunąć.
2. Wybierz **pozycję Usuń,** a marka nie będzie już wyświetlana na liście *Wyklucz marki.*

## <a name="next-steps"></a>Następne kroki

[Dostosowywanie modelu marki przy użyciu interfejsów API](customize-brands-model-with-api.md)
