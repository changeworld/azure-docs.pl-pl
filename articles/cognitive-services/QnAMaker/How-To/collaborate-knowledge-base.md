---
title: Współpraca w bazie wiedzy knowledge base — QnA Maker
titleSuffix: Azure Cognitive Services
description: Usługa QnA Maker umożliwia wielu użytkownikom współpracę nad wiedzy. Ta funkcja jest dostarczany z kontroli dostępu.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/14/2019
ms.author: diberry
ms.openlocfilehash: 757b791f632e6d7ecb15a0a40384061f0658a6ff
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447510"
---
# <a name="collaborate-on-your-knowledge-base"></a>Współpracuj nad bazy wiedzy

Usługa QnA Maker umożliwia wielu użytkownikom współpracę nad wiedzy. Ta funkcja jest dostarczana z platformą Azure [kontroli dostępu opartej na rolach](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure). 

Wykonaj poniższe kroki, aby udostępnić innemu użytkownikowi usługi QnA Maker:

1. Zaloguj się do witryny Azure portal i przejdź do zasobu usługi QnA Maker.

    ![Lista zasobów usługi QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

2. Przejdź do **kontrola dostępu (IAM)** kartę.

    ![Usługa QnA Maker zarządzania tożsamościami i Dostępem](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

3. Wybierz pozycję **Dodaj**.

    ![Dodaj zarządzania tożsamościami i Dostępem do usługi QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

4. Wybierz **właściciela** lub **Współautor** roli. Nie można udzielić dostępu tylko do odczytu za pomocą kontroli dostępu opartej na rolach. Właściciel i współautor rola ma dostęp do odczytu i zapisu bezpośrednio do usługi QnA Maker.

    ![Usługa QnA Maker IAM Dodaj rolę](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

5. Wprowadź adres e-mail, które chcesz udostępniać, a następnie naciśnij przycisk Zapisz.

    ![Usługa QnA Maker IAM Dodaj adres e-mail](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

Teraz gdy osoba udostępniony usługi QnA Maker, loguje się do [portalu narzędzia QnA Maker](https://qnamaker.ai) mogą przeglądać wszystkie baz wiedzy w tej usłudze.

Należy pamiętać, że nie możesz udostępnić określonym wiedzy, w ramach usługi QnA Maker. Jeśli chcesz bardziej szczegółową kontrolę dostępu, należy wziąć pod uwagę dystrybucję Twojej baz wiedzy w różne usługi QnA Maker.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Testowanie bazy wiedzy](./test-knowledge-base.md)
