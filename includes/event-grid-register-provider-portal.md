---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 07/05/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 108bf3f50b863428a63e745c1dac470d4caaf623
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/06/2018
ms.locfileid: "37869818"
---
## <a name="enable-event-grid-resource-provider"></a>Włączanie dostawcy zasobów usługi Event Grid

Jeśli usługa Event Grid nie była wcześniej używana w subskrypcji platformy Azure, może być konieczne zarejestrowanie dostawcy zasobów usługi Event Grid.

W witrynie Azure Portal:

1. Wybierz pozycję **Subskrypcje**.
1. Wybierz subskrypcję, której używasz dla usługi Event Grid.
1. W obszarze **Ustawienia** wybierz opcję **Dostawcy zasobów**.
1. Znajdź dostawcę **Microsoft.EventGrid**.
1. Jeśli nie jest on zarejestrowany, wybierz pozycję **Zarejestruj**. 

Ukończenie rejestracji może chwilę potrwać. Wybierz pozycję **Odśwież**, aby zaktualizować stan. Gdy **Stan** będzie miał wartość **Zarejestrowano**, możesz kontynuować.