---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 02/14/2020
ms.topic: include
ms.author: diberry
ms.openlocfilehash: 155c88ec4766391f70701b17038b915c399d8b0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77371708"
---
## <a name="sign-in-to-luis-portal"></a>Logowanie się do portalu usługi LUIS

Nowy użytkownik usługi LUIS musi wykonać tę procedurę:

1. Zaloguj się do [portalu usługi LUIS (wersja zapoznawcza),](https://preview.luis.ai)wybierz swój kraj i zaakceptuj warunki użytkowania. Jeśli zamiast tego zostanie wyświetlony **mój plik usługi Moje aplikacje,** zasób usługi LUIS już istnieje i należy przejść do przodu, aby utworzyć aplikację.

1. Wybierz **pozycję Utwórz zasób platformy Azure,** a następnie wybierz pozycję **Utwórz zasób autorski, do który chcesz przeprowadzić migrację aplikacji.**

    ![Wybieranie typu zasobu tworzenia opisu języka](../media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Wypełnij szczegóły zasobu.

    ![Tworzenie zasobu autora](../media/migrate-authoring-key/choose-authoring-resource-form.png)

    Podczas **tworzenia nowego zasobu autora**podaj następujące informacje:

    * **Nazwa zasobu** — wybrana nazwa niestandardowa, używana jako część adresu URL zapytań dotyczących tworzenia i przewidywania punktów końcowych.
    * **Dzierżawa** — dzierżawa, z nią skojarzona jest subskrypcja platformy Azure.
    * **Nazwa subskrypcji** — subskrypcja, która będzie naliczona za zasób.
    * **Grupa zasobów** — niestandardowa nazwa grupy zasobów, którą wybierzesz lub utworzysz. Grupy zasobów umożliwiają grupowanie zasobów platformy Azure w celu uzyskania dostępu i zarządzania.
    * **Lokalizacja** — wybór lokalizacji jest oparty na wyborze **grupy zasobów.**
    * **Warstwa cenowa** — warstwa cenowa określa maksymalną transakcję na sekundę i miesiąc.

1. Zostanie wyświetlone podsumowanie zasobu, który ma zostać utworzony. Wybierz **pozycję Dalej**.

    ![Tworzenie zasobu autora](../media/sign-in/sign-in-confirm-key-selection.png)

1. Potwierdź, wybierając przycisk **Kontynuuj**.

    ![Tworzenie zasobu autora](../media/sign-in/sign-in-confirm-continue.png)