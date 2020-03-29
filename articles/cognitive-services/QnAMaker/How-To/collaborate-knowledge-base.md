---
title: Współpraca w bazie wiedzy - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker umożliwia wielu osobom współpracę w bazie wiedzy. Ta funkcja jest dostarczana z kontrolą dostępu opartą na rolach platformy Azure.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/03/2020
ms.author: diberry
ms.openlocfilehash: b5adc7ebacde056a141ca3b361b9eb2ea7900a39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75660753"
---
# <a name="collaborate-on-your-knowledge-base"></a>Współpraca nad bazą wiedzy

QnA Maker umożliwia wielu osobom współpracę na wszystkich bazach wiedzy w tym samym zasobie QnA Maker. Ta funkcja jest dostępna z kontrolą [dostępu opartą na rolach](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)platformy Azure.

Wykonaj następujące czynności, aby udostępnić komuś usługę QnA Maker:

1. Zaloguj się do witryny Azure portal i przejdź do zasobu programu QnA Maker.

    ![Lista zasobów programu QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

1. Przejdź do karty **Kontrola dostępu (IAM).**

    ![QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

1. Wybierz pozycję **Dodaj**.

    ![QnA Maker IAM dodaj](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

1. Wybierz **rolę Właściciel** lub **Współautor.** Nie można udzielić dostępu tylko do odczytu za pośrednictwem kontroli dostępu opartej na rolach. Role właściciela i współautora mają uprawnienia dostępu do odczytu i zapisu do usługi QnA Maker.

    ![QnA Maker IAM dodać rolę](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

1. Wprowadź adres e-mail użytkownika i naciśnij przycisk **Zapisz**.

    ![QnA Maker IAM dodać posyłów e-mail](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

Gdy osoba, udostępnianie usługi QnA Maker, loguje się do [portalu QnA Maker](https://qnamaker.ai) mogą zobaczyć wszystkie bazy wiedzy w tej usłudze.

Pamiętaj, że nie można udostępnić określonej bazy wiedzy w usłudze QnA Maker. Jeśli chcesz bardziej szczegółową kontrolę dostępu, należy rozważyć dystrybucję baz wiedzy w różnych usługach QnA Maker.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Testowanie bazy wiedzy](./test-knowledge-base.md)
