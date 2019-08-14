---
title: Zarządzanie zasobami i klucz — QnA Maker
titleSuffix: Azure Cognitive Services
description: Usługa QnA Maker obsługuje dwa rodzaje kluczy, kluczy subskrypcji i klucze punktu końcowego.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b0b82f37b76a6bca6d84a05bc48b7ae9986fb76b
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967682"
---
# <a name="how-to-manage-keys-in-qna-maker"></a>Jak zarządzać kluczami w usługi QnA Maker

Usługa QnA Maker obsługuje dwa rodzaje kluczy **klucze subskrypcji** i **klucze punktu końcowego**.

![Zarządzanie kluczami](../media/qnamaker-how-to-key-management/key-management.png)

1. **Klucze subskrypcji**: Te klucze są używane w celu uzyskania dostępu do [interfejsów API usługi zarządzania QNA Maker](https://go.microsoft.com/fwlink/?linkid=2092179). Te interfejsy API umożliwiają przeprowadzenie edycji bazy wiedzy.  

2. **Klucze punktów końcowych**: Te klucze są używane do uzyskiwania dostępu do punktu końcowego bazy wiedzy w celu uzyskania odpowiedzi na pytanie użytkownika. Zazwyczaj ten punkt końcowy jest używany w bot rozmowy lub kodzie aplikacji klienta, który korzysta z usługi QnA Maker.
 
## <a name="subscription-keys"></a>Klucze subskrypcji
Można przeglądać i zresetować klucze subskrypcji w witrynie Azure portal, w której utworzono zasób usługi QnA Maker. 
1. Przejdź do zasobu usługi QnA Maker w witrynie Azure portal.

    ![Lista zasobów usługi QnA Maker](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Przejdź do **klucze**.

    ![Klucz subskrypcji](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="endpoint-keys"></a>Klucze punktu końcowego

Klucze punkt końcowy, można zarządzać przy użyciu [portalu narzędzia QnA Maker](https://qnamaker.ai).

1. Zaloguj się do [portalu QNA Maker](https://qnamaker.ai), przejdź do swojego profilu, a następnie kliknij pozycję **Ustawienia usługi**.

    ![Klucz punktu końcowego](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Umożliwia wyświetlenie lub zresetować klucze.

    ![Menedżer klucza punktu końcowego](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Jeśli uważasz, że zostały naruszone, należy odświeżyć klucze. Może to wymagać odpowiednich zmian w aplikacji klienckiej lub kodzie bot.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie bazy wiedzy w różnych językach](./language-knowledge-base.md)
