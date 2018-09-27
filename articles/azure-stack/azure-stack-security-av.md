---
title: Aktualizowanie programu antywirusowego Windows Defender w usłudze Azure Stack
description: Szczegółowe informacje na temat sposobu oprogramowania antywirusowego są aktualne w usłudze Azure Stack
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 09/26/2018
ms.author: patricka
ms.reviewer: fiseraci
ms.openlocfilehash: 59c59705e840d3cdd0d3c5310d84d711cd00c96a
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2018
ms.locfileid: "47394016"
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
