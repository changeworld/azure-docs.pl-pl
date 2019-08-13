---
title: Współpraca z bazą wiedzy — QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker umożliwia wielu osobom współpracę w bazie wiedzy. Ta funkcja jest dostępna z Access Control opartych na rolach platformy Azure.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/14/2019
ms.author: diberry
ms.openlocfilehash: 9c5398ff7cb31698db3d4a798b6a082f9e74b99b
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/12/2019
ms.locfileid: "68955131"
---
# <a name="collaborate-on-your-knowledge-base"></a>Współpracuj nad bazą wiedzy

QnA Maker umożliwia wielu osobom współpracę w bazie wiedzy. Ta funkcja jest dostępna z [Access Control opartych na rolach](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)platformy Azure. 

Wykonaj następujące kroki, aby udostępnić usługę QnA Maker komuś:

1. Zaloguj się do Azure Portal i przejdź do zasobu QnA Maker.

    ![Lista zasobów usługi QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

2. Przejdź do karty **Access Control (IAM)** .

    ![QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

3. Wybierz pozycję **Dodaj**.

    ![Dodawanie QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

4. Wybierz **właściciela** lub rolę **współautor** . Nie można udzielić dostępu tylko do odczytu za pośrednictwem Access Control opartych na rolach. Rola właściciela i współautora ma prawo dostępu do odczytu i zapisu do usługi QnA Maker.

    ![QnA Maker Dodaj rolę w usłudze IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

5. Wprowadź adres e-mail, który chcesz udostępnić, a następnie naciśnij przycisk Zapisz.

    ![QnA Maker Dodaj adres e-mail w usłudze IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

Teraz, gdy osoba, do której udostępniono usługę QnA Maker, loguje się do [portalu QNA Maker](https://qnamaker.ai) mogą zobaczyć wszystkie bazy wiedzy w tej usłudze.

Należy pamiętać, że nie można udostępnić konkretnej bazy wiedzy w usłudze QnA Maker. Jeśli potrzebujesz bardziej szczegółowej kontroli dostępu, rozważ dystrybuowanie baz wiedzy w różnych usługach QnA Maker.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Testowanie bazy wiedzy](./test-knowledge-base.md)
