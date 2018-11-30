---
author: jpconnock
ms.service: cloud-services
ms.topic: include
ms.date: 11/25/2018
ms.author: jeconnoc
ms.openlocfilehash: f23d5246401f23694755e63a51c52da5dbf5ac1b
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2018
ms.locfileid: "52331317"
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
