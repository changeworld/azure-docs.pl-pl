---
title: Umożliwia dostosowywanie modelu marek — Azure Video Indexer witryny sieci Web
titlesuffix: Azure Media Services
description: W tym artykule pokazano, jak dostosować model marek przy użyciu witryny sieci Web Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 721fde63aeae8704761b3c21f489dcad77cb89e3
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65799610"
---
# <a name="customize-a-brands-model-with-the-video-indexer-website"></a>Dostosuj model marki, za pomocą witryny sieci Web indeksatora wideo

Usługa Video Indexer obsługuje wykrywanie marki mowy i tekstu podczas indeksowania i indeksowanie zawartości audio i wideo. Funkcja wykrywania marki identyfikuje wzmianek o produktach, usługach i firm zaproponowana przez Bing marek w bazie danych. Na przykład Microsoft jest wymieniony w zawartości wideo lub audio lub zostanie ona wyświetlona w tekście visual w filmach wideo, Video Indexer wykrywa ją jako marki w zawartości. Niestandardowy model marek służy do wybierania czy Video Indexer będzie wykrywanie marek z Bing bazy danych z marek i wykluczyć pewne marek miałyby wykryto (zasadniczo tworzy czarnej listy marek) i zawierać znaki firmowe, które powinny być częścią modelu może być w bazie danych marek Bing (zasadniczo tworzy białą listę marek).

Aby uzyskać szczegółowym omówieniem, zobacz [Przegląd](customize-brands-model-overview.md).

Video Indexer witryny sieci Web służy do tworzenia, użycia i Edytuj niestandardowe modele marek wykryte w filmach wideo, zgodnie z opisem w tym temacie. Możesz również użyć interfejsu API, zgodnie z opisem w [marek Dostosowywanie modelu za pomocą interfejsów API](customize-brands-model-with-api.md).

## <a name="edit-the-settings-of-the-brands-model"></a>Edytuj ustawienia modelu marki  

Masz możliwość ustawienia lub nie chcesz, aby marek z bazy danych marek Bing, aby zostało wykryte. W tym celu należy edytować ustawienia modelu marek.

1. Przejdź do witryny internetowej [Video Indexer](https://www.videoindexer.ai/) i zaloguj się.
2. Aby dostosować model na Twoim koncie, wybierz polecenie **zawartości Dostosowywanie modelu** przycisk w prawym górnym rogu strony.
 
   ![Dostosowywanie modelu zawartości](./media/content-model-customization/content-model-customization.png) 
3. Aby edytować marki, wybierz **marek** kartę.

    ![Dostosuj model marki](./media/customize-brand-model/customize-brand-model.png)
4. Sprawdź **Pokaż sugerowane przez usługę Bing marek** opcji, jeśli chcesz indeksatora wideo uwzględnić marek sugerowane przez usługę Bing. Pozostaw niezaznaczone tę opcję, jeśli nie chcesz, aby indeksatora wideo, aby wykryć marek sugerowane przez usługę Bing we własnych Treściach. 

## <a name="include-brands-in-the-model"></a>Obejmują marek w modelu

**Obejmują marek** sekcja reprezentuje marek niestandardowych, które indeksatora wideo wykryć, nawet jeśli ich nie są sugerowane przez usługę Bing.  

### <a name="add-a-brand"></a>Dodaj na marki

1. Kliknij przycisk "+ Dodaj marki".

    ![Dostosuj model marki](./media/customize-brand-model/add-brand.png)

    Podaj nazwę (wymagane), kategoria (opcjonalnie) opis (opcjonalnie) i odwoływać się do adresu URL (opcjonalnie).
    Pole Kategoria jest przeznaczona do pomagają tag Twoich marek. W tym polu jest wyświetlany jako marki *tagi* podczas korzystania z interfejsów API indeksatora wideo. Na przykład marki "Azure" można oznakowane lub kategorii "Chmura".

    Odwołanie do pola Adres URL może być witryny sieci Web dowolnego odwołania na marki, np. łącze do strony Wikipedii.
2. Kliknij pozycję "Dodaj marki", a zobaczysz, że produkt został dodany do **obejmują marek** listy.

### <a name="edit-a-brand"></a>Edytuj marki

1. Kliknij ikonę ołówka obok markę, którą chcesz edytować.

    Kategoria, opis i adres odwołania URL witryn zbiorczych można aktualizować. Nie można zmienić nazwy witryn zbiorczych, ponieważ nazwy marek są unikatowe. Jeśli musisz zmienić nazwę, usuń cały marki (patrz następny rozdział) i tworzenie nowych witryn zbiorczych z nową nazwą.
2. Kliknij przycisk **aktualizacji** przycisk, aby zaktualizować produkt o nowe informacje.

### <a name="delete-a-brand"></a>Usuń marki

1. Kliknij ikonę Kosza obok markę, którą chcesz usunąć.
2. Kliknij przycisk "Usuń" oraz marki pojawią się już w swojej *obejmują marek* listy.

## <a name="exclude-brands-from-the-model"></a>Wyklucz marek z modelu

**Wykluczyć marek** sekcja reprezentuje marki, które indeksatora wideo nie wykrywać.

### <a name="add-a-brand"></a>Dodaj na marki

1. Kliknij przycisk "+ Dodaj marki".

    Podaj nazwę (wymagane) i category (opcjonalnie).
2. Kliknij pozycję "Dodaj marki", a zobaczysz, że produkt został dodany do *wykluczyć marek* listy.

### <a name="edit-a-brand"></a>Edytuj marki

1. Kliknij ikonę ołówka obok markę, którą chcesz edytować.

    Kategoria witryn zbiorczych można aktualizować tylko. Nie można zmienić nazwy witryn zbiorczych, ponieważ nazwy marek są unikatowe. Jeśli musisz zmienić nazwę, usuń cały marki (patrz następny rozdział) i tworzenie nowych witryn zbiorczych z nową nazwą.
2. Kliknij przycisk **aktualizacji** przycisk, aby zaktualizować produkt o nowe informacje.

### <a name="delete-a-brand"></a>Usuń marki

1. Kliknij ikonę Kosza obok markę, którą chcesz usunąć.
2. Kliknij przycisk "Usuń" oraz marki pojawią się już w swojej *wykluczyć marek* listy.

## <a name="next-steps"></a>Kolejne kroki

[Dostosuj model marek przy użyciu interfejsów API](customize-brands-model-with-api.md)
