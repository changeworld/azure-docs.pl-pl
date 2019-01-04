---
title: Zarządzanie zasobami i klucz — QnA Maker
titleSuffix: Azure Cognitive Services
description: Usługa QnA Maker obsługuje dwa rodzaje kluczy, kluczy subskrypcji i klucze punktu końcowego.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 11/26/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 3984ac84271728b9066aba15a582065ea3121c76
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/18/2018
ms.locfileid: "53581763"
---
# <a name="how-to-manage-keys-in-qna-maker"></a>Jak zarządzać kluczami w usługi QnA Maker

Usługa QnA Maker obsługuje dwa rodzaje kluczy **klucze subskrypcji** i **klucze punktu końcowego**.

![Zarządzanie kluczami](../media/qnamaker-how-to-key-management/key-management.png)

1. **Klucze subskrypcji**: Te klucze są używane do dostępu [interfejsami API usługi zarządzania usługi QnA Maker](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff). Te interfejsy API umożliwiają wykonywanie edycji bazy wiedzy.  

2. **Punkt końcowy klucze**: Te klucze są używane do dostępu do punktu końcowego bazy wiedzy knowledge base, można uzyskać odpowiedzi na pytania użytkowników. Zazwyczaj należy użyć tego punktu końcowego czatbot lub kodem aplikacji klienta, który wykorzystuje usługę QnA Maker.
 
## <a name="subscription-keys"></a>Klucze subskrypcji
Można przeglądać i zresetować klucze subskrypcji w witrynie Azure portal, w której utworzono zasób usługi QnA Maker. 
1. Przejdź do zasobu usługi QnA Maker w witrynie Azure portal.

    ![Lista zasobów usługi QnA Maker](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Przejdź do **klucze**.

    ![Klucz subskrypcji](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="endpoint-keys"></a>Klucze punktu końcowego

Klucze punkt końcowy, można zarządzać przy użyciu [portalu narzędzia QnA Maker](https://qnamaker.ai).

1. Zaloguj się do [portalu narzędzia QnA Maker](https://qnamaker.ai), a następnie przejdź do **ustawienia usługi**.

    ![Klucz punktu końcowego](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Umożliwia wyświetlenie lub zresetować klucze.

    ![Menedżer klucza punktu końcowego](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Jeśli uważasz, że zostały naruszone, należy odświeżyć klucze. Może to wymagać odpowiednie zmiany do kodu bot lub aplikacja kliencka.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Tworzenie bazy wiedzy w różnych językach](./language-knowledge-base.md)
