---
title: Zarządzanie zasobami i klucz — QnA Maker
titleSuffix: Azure Cognitive Services
description: Usługa QnA Maker obsługuje dwa rodzaje kluczy, kluczy subskrypcji i klucze punktu końcowego.
services: cognitive-services
author: nstulasi
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: saneppal
ms.openlocfilehash: 9134819fc4610daadb617e123d861673e8cbfd32
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/13/2018
ms.locfileid: "45543446"
---
# <a name="key-management"></a>Zarządzanie kluczami

Usługa QnA Maker obsługuje dwa rodzaje kluczy **klucze subskrypcji** i **klucze punktu końcowego**.

![Zarządzanie kluczami](../media/qnamaker-how-to-key-management/key-management.png)

1. **Klucze subskrypcji**: te klucze są używane do dostępu [interfejsami API usługi zarządzania usługi QnA Maker](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff). Te interfejsy API umożliwiają wykonywanie różnych operacji CRUD na wiedzy.  

2. **Punkt końcowy klucze**: te klucze są używane do dostępu do punktu końcowego bazy wiedzy knowledge base, można uzyskać odpowiedzi na pytania użytkowników. Zazwyczaj należy użyć tego punktu końcowego w kodzie aplikacji/bota rozmowy, który wykorzystuje usługę QnA Maker.
 
## <a name="subscription-keys"></a>Klucze subskrypcji
Można przeglądać i zresetować klucze subskrypcji w witrynie Azure portal, w której utworzono zasób usługi QnA Maker. 
1. Przejdź do zasobu usługi QnA Maker w witrynie Azure portal.

    ![Lista zasobów usługi QnA Maker](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Przejdź do **klucze**.

    ![Klucz subskrypcji](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="endpoint-keys"></a>Klucze punktu końcowego

Klucze punkt końcowy, można zarządzać przy użyciu [portalu narzędzia QnA Maker](https://qnamaker.ai).

1. Zaloguj się do [portalu narzędzia QnA Maker](https://qnamaker.ai), a następnie przejdź do **zarządzanie kluczami**.

    ![Klucz punktu końcowego](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Umożliwia wyświetlenie lub zresetować klucze.

    ![Menedżer klucza punktu końcowego](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Jeśli uważasz, że zostały naruszone, należy odświeżyć klucze. Może to wymagać odpowiednie zmiany w kodzie aplikacji/Bota.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Tworzenie bazy wiedzy w różnych językach](./language-knowledge-base.md)
