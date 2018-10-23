---
author: mattbriggs
ms.service: azure-stack
ms.topic: include
ms.date: 10/19/2018
ms.author: mabrigg
ms.openlocfilehash: d23ba90f7cc0c5a03db2ef3eed4a662abe7ff27e
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2018
ms.locfileid: "49650557"
---
W przepływach pracy sprawdzania poprawności **planowania** przetestujesz typowe parametry przepływu pracy na poziomie, które zostały określone podczas tworzenia przepływu pracy (zobacz [wspólnych parametrów przepływów pracy dla usługi Azure Stack weryfikacji jako usługa](../azure-stack-vaas-parameters.md)). Jeśli dowolna z wartości parametrów testu stają się nieprawidłowe, użytkownik musi resupply je, zgodnie z instrukcją w [zmodyfikować parametry przepływu pracy](../azure-stack-vaas-monitor-test.md#change-workflow-parameters).

> [!NOTE]
> Planowanie testów sprawdzania poprawności w istniejącym wystąpieniu utworzy nowe wystąpienie zamiast w starym wystąpieniu w portalu. Dzienniki starej instalacji zostaną zachowane, ale nie są dostępne z poziomu portalu.  
Po pomyślnym zakończeniu testu **harmonogram** akcja zostanie wyłączona.

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](azure-stack-vaas-workflow-step_select-agent.md)]

1. Wybierz **harmonogram** z menu kontekstowego, aby otworzyć wiersz do planowania wystąpieniem testu.

1. Przejrzyj parametry testu, a następnie wybierz pozycję **przesyłania** do zaplanowania testów do wykonania.