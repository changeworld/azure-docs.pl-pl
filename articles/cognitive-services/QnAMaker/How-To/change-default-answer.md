---
title: Pobierz domyślną odpowiedź — QnA Maker
description: Odpowiedź domyślna jest zwracana, gdy nie ma dopasowania do pytania. Możesz chcieć zmienić domyślną odpowiedź ze standardowego domyślnej odpowiedzi.
ms.topic: conceptual
ms.date: 01/10/2020
ms.openlocfilehash: fae5c38fd64435a3fae56862bad04e000916e88b
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843279"
---
# <a name="set-default-answer-for-a-knowledge-base"></a>Ustawianie domyślnej odpowiedzi dla bazy wiedzy

Odpowiedź domyślna jest zwracana, gdy nie ma dopasowania do pytania. Możesz chcieć zmienić domyślną odpowiedź ze standardowego domyślnej odpowiedzi.

## <a name="change-default-answer"></a>Zmień domyślny odpowiedzi

1. Przejdź do [witryny Azure portal](https://portal.azure.com) i przejdź do grupy zasobów, która reprezentuje utworzonej usługi QnA Maker.

2. Kliknij, aby otworzyć **usługi App Service**.

    ![W witrynie Azure portal dostęp do usługi App service dotyczące usługi QnA Maker](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Kliknij pozycję **ustawienia aplikacji** i edytować **DefaultAnswer** pole odpowiedź domyślną. Kliknij pozycję **Zapisz**.

    ![Wybierz ustawienia aplikacji, a następnie edytuj DefaultAnswer dotyczące usługi QnA Maker](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Uruchom ponownie usługi App service

    ![Po zmianie DefaultAnswer, uruchom ponownie usługi QnA Maker usługi App Service](../media/qnamaker-faq/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>Następne kroki

* [Tworzenie bot przy użyciu QnA Maker i LUIS](../tutorials/integrate-qnamaker-luis.md)