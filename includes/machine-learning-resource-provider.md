---
author: blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/09/2020
ms.author: larryfr
ms.openlocfilehash: 8fd774f8a3a73ceaffa7902b35e1b1dff12ef5af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75893974"
---
Podczas tworzenia obszaru roboczego usługi Azure Machine Learning lub zasobu używanego przez obszar roboczy może pojawić się błąd podobny do następujących komunikatów:

* `No registered resource provider found for location {location}`
* `The subscription is not registered to use namespace {resource-provider-namespace}`

Większość dostawców zasobów są automatycznie rejestrowane, ale nie wszystkie. Jeśli otrzymasz ten komunikat, musisz zarejestrować wspomnianego dostawcę.

Aby uzyskać informacje na temat rejestrowania dostawców zasobów, zobacz [Rozwiązywanie błędów rejestracji dostawcy zasobów](../articles/azure-resource-manager/templates/error-register-resource-provider.md).