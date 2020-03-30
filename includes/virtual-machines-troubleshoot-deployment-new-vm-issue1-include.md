---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 2ae72045ae18d84eac2a6d619d94e3a9e49415ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67183229"
---
## <a name="issue-custom-image-provisioning-errors"></a>Problem: Obraz niestandardowy; inicjowania obsługi administracyjnej błędy
Błędy inicjowania obsługi administracyjnej pojawiają się, jeśli przekażesz lub przechwycisz uogólniony obraz maszyny Wirtualnej jako wyspecjalizowany obraz maszyny Wirtualnej lub odwrotnie. Pierwszy spowoduje błąd limitu czasu inicjowania obsługi administracyjnej, a drugi spowoduje błąd inicjowania obsługi administracyjnej. Aby wdrożyć obraz niestandardowy bez błędów, należy upewnić się, że typ obrazu nie zmienia się podczas procesu przechwytywania.

W poniższej tabeli wymieniono możliwe kombinacje obrazów uogólnionych i wyspecjalizowanych, typ błędu, który napotkasz i co należy zrobić, aby naprawić błędy.

