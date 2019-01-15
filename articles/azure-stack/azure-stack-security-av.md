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
ms.openlocfilehash: d47e11be88c4a8ca453475c35e9e0f02d9b531ff
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2019
ms.locfileid: "54305132"
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
