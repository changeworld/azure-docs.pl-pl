---
author: jpconnock
ms.service: cloud-services
ms.topic: include
ms.date: 11/25/2018
ms.author: jeconnoc
ms.openlocfilehash: f23d5246401f23694755e63a51c52da5dbf5ac1b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183573"
---
> [!WARNING]
> Po włączeniu diagnostykę dla istniejącej roli wszystkie rozszerzenia, które zostały skonfigurowane są wyłączone, gdy pakiet jest wdrażany. Należą do nich:
>
> * Microsoft Monitoring Agent diagnostyki
> * Monitorowanie zabezpieczeń platformy Microsoft Azure
> * Usługa firmy Microsoft chroniąca przed złośliwym kodem                 
> * Microsoft Monitoring Agent
> * Agent Profiler usługi firmy Microsoft      
> * Rozszerzenie domeny platformy Azure Windows        
> * Rozszerzenie diagnostyki platformy Azure Windows   
> * Windows Azure rozszerzenia usług pulpitu zdalnego
> * Moduł zbierający Windows Azure
>
> Po wdrożeniu zaktualizowana rola, można zresetować dotyczących rozszerzeń za pośrednictwem witryny Azure portal lub programu PowerShell.
>
