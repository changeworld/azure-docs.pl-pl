---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/29/2019
ms.author: glenga
ms.openlocfilehash: f16852cdc18053a3a8e375dc6f14e39bb069afef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "72329612"
---
Usługa Azure Functions umożliwia łączenie usług platformy Azure i innych zasobów z funkcjami bez konieczności pisania własnego kodu integracji. Te *powiązania*, które reprezentują zarówno dane wejściowe, jak i wyjściowe, są zadeklarowane w definicji funkcji. Dane z powiązań są podawane do funkcji jako parametry. *Wyzwalacz* jest specjalnym typem powiązania wejściowego. Chociaż funkcja ma tylko jeden wyzwalacz, może mieć wiele powiązań wejściowych i wyjściowych. Aby dowiedzieć się więcej, zobacz [Koncepcje wyzwalaczy i powiązań usług Azure Functions](../articles/azure-functions/functions-triggers-bindings.md).