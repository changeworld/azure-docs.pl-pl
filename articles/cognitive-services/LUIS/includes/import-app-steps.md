---
title: Importuj kroki aplikacji
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 12/03/2019
ms.author: diberry
ms.openlocfilehash: c0253360c66ef6fd995f65e8a83ba5adcdf19543
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806421"
---
1. W [portalu Luis w wersji zapoznawczej](https://preview.luis.ai)na stronie **Moje aplikacje** wybierz opcję **Importuj**, a następnie **zaimportuj jako plik JSON**. Znajdź zapisany plik JSON z poprzedniego kroku. Nie musisz zmieniać nazwy aplikacji. Wybierz pozycję **gotowe**

1. W sekcji **Zarządzanie** na karcie **wersje** wybierz wersję, a następnie wybierz pozycję **Klonuj** , aby sklonować wersję, i nadaj jej nową nazwę 10 znaków, a następnie wybierz pozycję **gotowe** , aby zakończyć proces klonowania. Ponieważ nazwa wersji jest używana jako część trasy adresu URL, nie może ona zawierać żadnych znaków, które są nieprawidłowe w adresie URL.

    > [!TIP]
    > Klonowanie wersji do nowej wersji jest najlepszym rozwiązaniem Przed zmodyfikowaniem aplikacji. Po zakończeniu wersji wyeksportuj wersję (plik JSON lub Lu) i sprawdź plik w systemie kontroli źródła.

1. Wybierz opcję Kompiluj **, a następnie zapoznaj się z** intencjami, głównymi blokami konstrukcyjnymi aplikacji Luis.

    ![Przejdź do strony wersje na stronie intencje.](../media/tutorial-machine-learned-entity/new-version-imported-app.png)