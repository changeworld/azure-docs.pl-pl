---
title: Zarządzanie zasobami i klucz — QnA Maker
titleSuffix: Azure Cognitive Services
description: Usługa QnA Maker obsługuje dwa rodzaje kluczy, kluczy subskrypcji i klucze punktu końcowego.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/04/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 458d587c7ac73f7c8dacdceae3c9f923263533b3
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65792543"
---
# <a name="how-to-manage-keys-in-qna-maker"></a>Jak zarządzać kluczami w usługi QnA Maker

Usługa QnA Maker obsługuje dwa rodzaje kluczy **klucze subskrypcji** i **klucze punktu końcowego**.

![Zarządzanie kluczami](../media/qnamaker-how-to-key-management/key-management.png)

1. **Klucze subskrypcji**: Te klucze są używane do dostępu [interfejsami API usługi zarządzania usługi QnA Maker](https://go.microsoft.com/fwlink/?linkid=2092179). Te interfejsy API umożliwiają wykonywanie edycji bazy wiedzy.  

2. **Punkt końcowy klucze**: Te klucze są używane do dostępu do punktu końcowego bazy wiedzy knowledge base, można uzyskać odpowiedzi na pytania użytkowników. Zazwyczaj należy użyć tego punktu końcowego czatbot lub kodem aplikacji klienta, który wykorzystuje usługę QnA Maker.
 
## <a name="subscription-keys"></a>Klucze subskrypcji
Można przeglądać i zresetować klucze subskrypcji w witrynie Azure portal, w której utworzono zasób usługi QnA Maker. 
1. Przejdź do zasobu usługi QnA Maker w witrynie Azure portal.

    ![Lista zasobów usługi QnA Maker](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Przejdź do **klucze**.

    ![Klucz subskrypcji](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="endpoint-keys"></a>Klucze punktu końcowego

Klucze punkt końcowy, można zarządzać przy użyciu [portalu narzędzia QnA Maker](https://qnamaker.ai).

1. Zaloguj się do [portalu narzędzia QnA Maker](https://qnamaker.ai), przejdź do swojego profilu, a następnie kliknij **ustawienia usługi**.

    ![Klucz punktu końcowego](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Umożliwia wyświetlenie lub zresetować klucze.

    ![Menedżer klucza punktu końcowego](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Jeśli uważasz, że zostały naruszone, należy odświeżyć klucze. Może to wymagać odpowiednie zmiany do kodu bot lub aplikacja kliencka.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Tworzenie bazy wiedzy w różnych językach](./language-knowledge-base.md)
