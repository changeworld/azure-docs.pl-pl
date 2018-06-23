---
title: Współpraca nad wiedzy - kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Jak współpracować nad Maker — strona główna bazy wiedzy
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: e18d656236276595fc5186a6656349bf28974ead
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348585"
---
# <a name="collaborate-on-your-knowledge-base"></a>Współpracuj nad bazy wiedzy

Maker — strona główna umożliwia wielu użytkownikom współpracę w bazie wiedzy. Ta funkcja jest udostępniana z platformy Azure [kontroli dostępu opartej na rolach](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-control-configure). 

Wykonaj poniższe kroki, aby udostępnić innemu użytkownikowi Maker — strona główna usługi:

1. Zaloguj się do portalu Azure i przejdź do zasobu Maker — strona główna.

    ![Lista zasobów Maker — strona główna](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

2. Przejdź do **kontroli dostępu (IAM)** kartę.

    ![Maker — strona główna mapy IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

3. Wybierz pozycję **Dodaj**.

    ![Dodaj IAM Maker — strona główna](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

4. Wybierz **właściciela** lub **współautora** roli.

    ![Dodawanie IAM Maker — strona główna roli](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

5. Wprowadź adres e-mail, który chcesz udostępnić, a następnie naciśnij klawisz Zapisz.

    ![IAM Maker pytań Dodaj adres e-mail](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

Teraz, gdy osoby udostępniane usługę Maker — strona główna przy, logują się [Maker — strona główna portalu](https://qnamaker.ai) widzą wszystkie bazy wiedzy w tej usłudze.

Należy pamiętać, że nie można udostępnić określonej bazy wiedzy w usłudze Maker — strona główna. Jeśli chcesz większą kontrolę dostępu, należy wziąć pod uwagę dystrybucja z bazy wiedzy w różnych usługach Maker — strona główna.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Testowanie bazy wiedzy](./test-knowledge-base.md)
