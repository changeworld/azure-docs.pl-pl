---
title: Uzyskaj odpowiedź domyślną - QnA Maker
description: Odpowiedź domyślna jest zwracana, gdy nie ma dopasowania do pytania. Można zmienić domyślną odpowiedź ze standardowej odpowiedzi domyślnej.
ms.topic: conceptual
ms.date: 01/10/2020
ms.openlocfilehash: fae5c38fd64435a3fae56862bad04e000916e88b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76843279"
---
# <a name="set-default-answer-for-a-knowledge-base"></a>Ustawianie domyślnej odpowiedzi dla bazy wiedzy

Odpowiedź domyślna jest zwracana, gdy nie ma dopasowania do pytania. Można zmienić domyślną odpowiedź ze standardowej odpowiedzi domyślnej.

## <a name="change-default-answer"></a>Zmienianie odpowiedzi domyślnej

1. Przejdź do [witryny Azure portal](https://portal.azure.com) i przejdź do grupy zasobów, która reprezentuje utworzoną usługę QnA Maker.

2. Kliknij, aby otworzyć **usługę app service**.

    ![W witrynie Azure portal uzyskaj dostęp do usługi aplikacji dla programu QnA Maker](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Kliknij **ustawienia aplikacji** i edytuj pole **DefaultAnswer** do żądanej odpowiedzi domyślnej. Kliknij przycisk **Zapisz**.

    ![Wybierz pozycję Ustawienia aplikacji, a następnie edytuj domyślną opcjęAswer dla programu QnA Maker](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Ponowne uruchamianie usługi aplikacji

    ![Po zmianie defaultanswer uruchom ponownie usługę aplikacji QnA Maker](../media/qnamaker-faq/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>Następne kroki

* [Tworzenie bota za pomocą aplikacji QnA Maker i LUIS](../tutorials/integrate-qnamaker-luis.md)