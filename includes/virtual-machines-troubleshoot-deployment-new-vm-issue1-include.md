---
author: rockboyfor
ms.service: virtual-machines
ms.topic: include
origin.date: 10/26/2018
ms.date: 11/26/2018
ms.author: v-yeche
ms.openlocfilehash: 2ae72045ae18d84eac2a6d619d94e3a9e49415ae
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123846"
---
## <a name="issue-custom-image-provisioning-errors"></a>Problem: Niestandardowy obraz; błędy inicjowania obsługi administracyjnej
Błędy inicjowania obsługi administracyjnej wystąpić, jeśli przekazywanie lub przechwytywania obrazu uogólnionej maszyny Wirtualnej jako obrazu wyspecjalizowanej maszyny Wirtualnej lub na odwrót. Pierwsza spowoduje błąd limitu czasu inicjowania obsługi administracyjnej i jego spowoduje błąd inicjowania obsługi administracyjnej. Aby wdrożyć niestandardowy obraz bez błędów, upewnij się, że typ obrazu nie zmienia się podczas procesu przechwytywania.

Poniższa tabela zawiera listę możliwych kombinacji uogólniony, wyspecjalizowana obrazów i typ błędu, które można napotkać, co należy zrobić, aby naprawić błędy.

<!--Update_Description: wording update, update link-->