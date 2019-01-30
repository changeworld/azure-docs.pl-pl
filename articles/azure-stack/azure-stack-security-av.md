---
title: Aktualizowanie programu antywirusowego Windows Defender w usłudze Azure Stack
description: Szczegółowe informacje na temat sposobu oprogramowania antywirusowego są aktualne w usłudze Azure Stack
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 01/14/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 7899f235853991160e4d8b2c2c832d9a77171e83
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2019
ms.locfileid: "55242205"
---
# <a name="update-windows-defender-antivirus-on-azure-stack"></a>Aktualizowanie programu antywirusowego Windows Defender w usłudze Azure Stack

[Program antywirusowy Windows Defender](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10) to rozwiązanie chroniące przed złośliwym kodem, które zapewnia bezpieczeństwo i ochrona przed wirusami. Każdy składnik infrastruktury Azure Stack (hosty funkcji Hyper-V i maszyn wirtualnych) jest chroniony za pomocą programu antywirusowego Windows Defender. Uzyskać aktualną ochronę definicje programu antywirusowego Windows Defender, aparatu i platformy wymagane okresowe aktualizacje. Jak zastosować aktualizacji, zależy od konfiguracji.

## <a name="connected-scenario"></a>Połączone scenariusza

W przypadku ochrony przed złośliwym oprogramowaniem definicji i aparatu aktualizacji usługi Azure Stack [dostawcy zasobów aktualizacji](azure-stack-updates.md#the-update-resource-provider) pobiera definicje ochrony przed złośliwym oprogramowaniem i aktualizacje aparatu kilka razy dziennie. Każdy składnik infrastruktury Azure Stack pobiera aktualizację od dostawcy zasobów aktualizacji i aktualizowane automatycznie.

Aktualizacje platformy ochrony przed złośliwym oprogramowaniem, zastosuj [comiesięcznej aktualizacji usługi Azure Stack](azure-stack-apply-updates.md). Comiesięcznej aktualizacji usługi Azure Stack obejmuje aktualizacje platformy programu antywirusowego Windows Defender na miesiąc.

## <a name="disconnected-scenario"></a>Scenariusz odłączony

 Zastosuj [comiesięcznej aktualizacji usługi Azure Stack](azure-stack-apply-updates.md). Comiesięcznej aktualizacji usługi Azure Stack obejmuje definicje programu antywirusowego Windows Defender, aparatu i aktualizacji platformy w miesiącu.

## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się więcej na temat zabezpieczeń usługi Azure Stack](azure-stack-security-foundations.md)
