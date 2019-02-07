---
author: mattbriggs
ms.service: azure-stack
ms.topic: include
ms.date: 11/26/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/26/2018
ms.openlocfilehash: 5cd64b806392162fd3bee14ddaf607385ac05264
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/06/2019
ms.locfileid: "55807152"
---
W przepływach pracy sprawdzania poprawności **planowania** przetestujesz typowe parametry przepływu pracy na poziomie, które zostały określone podczas tworzenia przepływu pracy (zobacz [wspólnych parametrów przepływów pracy dla usługi Azure Stack weryfikacji jako usługa](../azure-stack-vaas-parameters.md)). Jeśli dowolna z wartości parametrów testu stają się nieprawidłowe, użytkownik musi resupply je, zgodnie z instrukcją w [zmodyfikować parametry przepływu pracy](../azure-stack-vaas-monitor-test.md#change-workflow-parameters).

> [!NOTE]
> Planowanie testów sprawdzania poprawności w istniejącym wystąpieniu utworzy nowe wystąpienie zamiast w starym wystąpieniu w portalu. Dzienniki starej instalacji zostaną zachowane, ale nie są dostępne z poziomu portalu.  
Po pomyślnym zakończeniu testu **harmonogram** akcja zostanie wyłączona.

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](azure-stack-vaas-workflow-step_select-agent.md)]

1. Wybierz **harmonogram** z menu kontekstowego, aby otworzyć wiersz do planowania wystąpieniem testu.

1. Przejrzyj parametry testu, a następnie wybierz pozycję **przesyłania** do zaplanowania testów do wykonania.