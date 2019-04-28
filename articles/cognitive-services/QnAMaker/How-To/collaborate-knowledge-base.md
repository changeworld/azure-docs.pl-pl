---
title: Współpraca w bazie wiedzy knowledge base — Qna Maker
titleSuffix: Azure Cognitive Services
description: Usługa QnA Maker umożliwia wielu użytkownikom współpracę nad wiedzy. Ta funkcja jest dostarczany z kontroli dostępu.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/14/2019
ms.author: tulasim
ms.openlocfilehash: ca754f197a46fc41b6f1b432611a2177ec0afafa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61374863"
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
