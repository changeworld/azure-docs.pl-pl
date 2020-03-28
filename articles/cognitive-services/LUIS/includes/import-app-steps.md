---
title: Importowanie kroków aplikacji
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 12/03/2019
ms.author: diberry
ms.openlocfilehash: b0d1735df7a3ac36e20af771939aec6b8013f2df
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74840820"
---
1. W portalu usługi LUIS w [wersji zapoznawczej](https://preview.luis.ai)na stronie **Moje aplikacje** wybierz pozycję **Importuj**, a następnie **importuj jako JSON**. Znajdź zapisany plik JSON z poprzedniego kroku. Nie musisz zmieniać nazwy aplikacji. Wybierz **gotowe**

1. W sekcji **Zarządzanie** na karcie **Wersje** wybierz wersję, a następnie wybierz pozycję **Klonuj,** aby sklonować wersję, a następnie nadaj jej nową 10-znakową nazwę, a następnie wybierz pozycję **Gotowe,** aby zakończyć proces klonowania. Ponieważ nazwa wersji jest używana jako część trasy adresu URL, nie może ona zawierać żadnych znaków, które są nieprawidłowe w adresie URL.

    > [!TIP]
    > Klonowanie do nowej wersji jest najlepszym rozwiązaniem przed zmodyfikowaniem aplikacji. Po zakończeniu wersji wyeksportuj wersję (jako plik .json lub .lu) i sprawdź plik do systemu kontroli źródła.

1. Wybierz **build** następnie **intencje,** aby zobaczyć intencje, główne bloki konstrukcyjne aplikacji usługi LUIS.

    ![Zmień ze strony Wersje na stronę Intencje.](../media/tutorial-machine-learned-entity/new-version-imported-app.png)