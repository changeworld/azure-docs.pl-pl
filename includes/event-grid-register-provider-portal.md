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
ms.openlocfilehash: c3677e7897498aa06d7bd547988ad4dc0326f39b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67183320"
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