---
title: Klucz Zarządzanie - kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Jak zarządzanie kluczami Maker — strona główna
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: b402187f4949dac34fa476648c81b980ba3efc96
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348669"
---
# <a name="key-management"></a>Zarządzanie kluczami

Usługi — strona główna Maker zajmuje się dwa rodzaje kluczy, **klucze subskrypcji** i **punktu końcowego klucze**.

![Zarządzanie kluczami](../media/qnamaker-how-to-key-management/key-management.png)

1. **Klucze subskrypcji**: klucze te są używane do dostępu [Maker — strona główna usługa zarządzania interfejsów API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff). Te interfejsy API umożliwiają wykonywanie różnych operacji CRUD na bazy wiedzy.  

2. **Punkt końcowy kluczy**: klucze te umożliwiają dostęp do punktu końcowego wiedzy można uzyskać odpowiedzi na pytanie użytkownika. Zazwyczaj będzie używać tego punktu końcowego w kodzie bot/aplikacji rozmów, który wykorzystuje usługę Maker — strona główna.
 
## <a name="subscription-keys"></a>Klucze subskrypcji
Można wyświetlać i zresetować Twoje klucze subskrypcji z portalu Azure, w którym utworzono zasób Maker — strona główna. 
1. Przejdź do zasobu Maker — strona główna w portalu Azure.

    ![Lista zasobów Maker — strona główna](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Przejdź do **klucze**.

    ![Klucz subskrypcji](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="endpoint-keys"></a>Klucze punktu końcowego

Klucze punktu końcowego można zarządzać za pomocą [Maker — strona główna portalu](https://qnamaker.ai).

1. Zaloguj się do [Maker — strona główna portalu](https://qnamaker.ai), a następnie przejdź do **zarządzanie kluczami**.

    ![klucz punktu końcowego](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Wyświetl lub zresetować Twoje klucze.

    ![Menedżer klucza punktu końcowego](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Odśwież klucze, jeśli uważasz, że zostały one złamane. Może to wymagać odpowiednie zmiany w kodzie aplikacji/Bot.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Utwórz bazę wiedzy w innym języku](./language-knowledge-base.md)
