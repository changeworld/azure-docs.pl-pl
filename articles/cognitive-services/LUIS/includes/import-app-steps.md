---
title: Importowanie kroków aplikacji
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/30/2020
ms.author: diberry
ms.openlocfilehash: b7b8befa0f5871b65b9b5621bfb99c659bf07235
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422752"
---
1. W portalu usługi LUIS w [wersji zapoznawczej](https://preview.luis.ai)na stronie **Moje aplikacje** wybierz pozycję **+ Nowa aplikacja do konwersacji**, a następnie **importuj jako JSON**. Znajdź zapisany plik JSON z poprzedniego kroku. Nie musisz zmieniać nazwy aplikacji. Wybierz **gotowe**

1. W sekcji **Zarządzanie** na karcie **Wersje** `0.1` wybierz wersję, a następnie wybierz pozycję **Klonuj,** aby sklonować `ml-entity`wersję, a następnie nadaj jej nową 10-znakową nazwę , a następnie wybierz pozycję **Gotowe,** aby zakończyć proces klonowania. Ponieważ nazwa wersji jest używana jako część trasy adresu URL, nie może ona zawierać żadnych znaków, które są nieprawidłowe w adresie URL.

    > [!TIP]
    > Klonowanie do nowej wersji jest najlepszym rozwiązaniem przed zmodyfikowaniem aplikacji. Po zakończeniu ze zmianą wersji wyeksportuj wersję (jako plik .json lub .lu) i sprawdź plik do systemu kontroli źródła.

1. Wybierz **build** następnie **intencje,** aby zobaczyć intencje, główne bloki konstrukcyjne aplikacji usługi LUIS.

    ![Zmień ze strony Wersje na stronę Intencje.](../media/tutorial-machine-learned-entity/new-version-imported-app.png)