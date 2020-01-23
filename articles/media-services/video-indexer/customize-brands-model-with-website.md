---
title: Dostosowywanie modeli marek na platformie Azure za pomocą witryny sieci Web Video Indexer
titleSuffix: Azure Media Services
description: W tym artykule pokazano, jak dostosować model marek przy użyciu witryny sieci Web Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 956ca7af055768398392045ecf9b383d2eb1060f
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513902"
---
# <a name="customize-a-brands-model-with-the-video-indexer-website"></a>Dostosowywanie modelu marek przy użyciu witryny sieci Web Video Indexer

Video Indexer obsługuje wykrywanie marki z mowy i tekstu wizualnego podczas indeksowania i ponownego indeksowania zawartości audio i wideo. Funkcja wykrywania marki identyfikuje wzmianki o produktach, usługach i firmach sugerowanych przez bazę danych marek usługi Bing. Na przykład jeśli firma Microsoft jest wymieniona w zawartości wideo lub audio lub jeśli zostanie wyświetlona w tekście wizualnym w filmie wideo, Video Indexer wykrywa ją jako markę zawartości. Niestandardowy model marek pozwala określić, czy Video Indexer będą wykrywać marki z bazy danych marek Bing, wykluczać Niektóre marki (głównie tworząc czarną listę marek), a także dołączać marki, które powinny być częścią modelu to nie może być w bazie danych marek usługi Bing (głównie w przypadku tworzenia białej listy marek).

Aby zapoznać się z szczegółowym omówieniem, zobacz [Omówienie](customize-brands-model-overview.md).

Za pomocą witryny sieci Web Video Indexer można tworzyć, używać i edytować niestandardowe modele marek wykryte w filmie wideo, zgodnie z opisem w tym temacie. Możesz również użyć interfejsu API, zgodnie z opisem w temacie [Dostosowywanie modelu marek przy użyciu interfejsów API](customize-brands-model-with-api.md).

## <a name="edit-the-settings-of-the-brands-model"></a>Edytuj ustawienia modelu marek  

Możesz określić, czy chcesz wykryć marki z bazy danych marek Bing. W tym celu należy edytować ustawienia modelu marki.

1. Przejdź do witryny internetowej [Video Indexer](https://www.videoindexer.ai/) i zaloguj się.
2. Aby dostosować model na koncie, kliknij przycisk **dostosowywania modelu zawartości** w prawym górnym rogu strony.
 
   ![Dostosuj model zawartości](./media/content-model-customization/content-model-customization.png) 
3. Aby edytować marki, wybierz kartę **marki** .

    ![Dostosuj model marek](./media/customize-brand-model/customize-brand-model.png)
4. Zaznacz opcję **Pokaż marki sugerowane przez Bing** , jeśli chcesz, aby w Video Indexer uwzględnić marki sugerowane przez usługę Bing. Pozostaw opcję niezaznaczone, jeśli nie chcesz, aby Video Indexer wykrywać marki sugerowane przez usługę Bing w Twojej zawartości. 

## <a name="include-brands-in-the-model"></a>Uwzględnij marki w modelu

Sekcja **include marek** reprezentuje niestandardowe marki, które mają być używane do Video Indexer do wykrycia, nawet jeśli nie są sugerowane przez usługę Bing.  

### <a name="add-a-brand"></a>Dodaj markę

1. Kliknij pozycję "+ Dodaj markę".

    ![Dostosuj model marek](./media/customize-brand-model/add-brand.png)

    Podaj nazwę (wymagane), kategorię (opcjonalnie), opis (opcjonalnie) i adres URL odwołania (opcjonalnie).
    Pole kategoria ma pomóc w oznaczeniu marki. To pole jest wyświetlane jako *Tagi* marki przy użyciu interfejsów API Video Indexer. Na przykład znak "Azure" może być oznaczony jako "Chmura" lub skategoryzowany.

    Pole adres URL odwołania może być dowolną witryną referencyjną, taką jak link do strony Wikipedia.
2. Kliknij pozycję "Dodaj markę" i zobaczysz, że marka została dodana do listy **Dołącz marki** .

### <a name="edit-a-brand"></a>Edytuj markę

1. Kliknij ikonę ołówka obok znaku, który chcesz edytować.

    Możesz zaktualizować kategorię, opis lub adres URL odwołania marki. Nie można zmienić nazwy marki, ponieważ nazwy marek są unikatowe. Jeśli musisz zmienić nazwę marki, Usuń całą markę (zobacz następną sekcję) i Utwórz nową markę z nową nazwą.
2. Kliknij przycisk **Aktualizuj** , aby zaktualizować markę o nowe informacje.

### <a name="delete-a-brand"></a>Usuń markę

1. Kliknij ikonę kosza obok znaku, który chcesz usunąć.
2. Kliknij pozycję "Usuń", a marka nie będzie już widoczna na liście *Uwzględnij marki* .

## <a name="exclude-brands-from-the-model"></a>Wyklucz marki z modelu

Sekcja **exclude marek** reprezentuje marki, dla których chcesz Video Indexer nie wykrywać.

### <a name="add-a-brand"></a>Dodaj markę

1. Kliknij pozycję "+ Dodaj markę".

    Podaj nazwę (wymagane), kategorię (opcjonalnie).
2. Kliknij pozycję "Dodaj markę" i zobaczysz, że marka została dodana do listy *wykluczone* marki.

### <a name="edit-a-brand"></a>Edytuj markę

1. Kliknij ikonę ołówka obok znaku, który chcesz edytować.

    Można aktualizować tylko kategorię marki. Nie można zmienić nazwy marki, ponieważ nazwy marek są unikatowe. Jeśli musisz zmienić nazwę marki, Usuń całą markę (zobacz następną sekcję) i Utwórz nową markę z nową nazwą.
2. Kliknij przycisk **Aktualizuj** , aby zaktualizować markę o nowe informacje.

### <a name="delete-a-brand"></a>Usuń markę

1. Kliknij ikonę kosza obok znaku, który chcesz usunąć.
2. Kliknij przycisk "Usuń", a marka nie będzie już widoczna na liście *wykluczone marki* .

## <a name="next-steps"></a>Następne kroki

[Dostosowywanie modelu marek przy użyciu interfejsów API](customize-brands-model-with-api.md)
