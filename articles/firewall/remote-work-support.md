---
title: Obsługa pracy zdalnej w zaporze platformy Azure
description: W tym artykule pokazano, jak Zapora platformy Azure może obsługiwać wymagania dotyczące siły roboczej zdalnej.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: victorh
ms.openlocfilehash: 5abe9344b0512433c48df50335cce5cf1e3e3547
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289643"
---
# <a name="azure-firewall-remote-work-support"></a>Obsługa pracy zdalnej w zaporze platformy Azure

Zapora azure to zarządzana, oparta na chmurze usługa zabezpieczeń sieci, która chroni zasoby sieci wirtualnej platformy Azure. Jest to w pełni stanowa zapora jako usługa z wbudowaną wysoką dostępnością i możliwością nieograniczonego skalowania w chmurze. 

## <a name="firewall-rules"></a>Reguły zapory

Za pomocą Zapory Platformy Azure można zabezpieczyć przychodzący dostęp RDP infrastruktury pulpitu wirtualnego (VDI) do sieci wirtualnej platformy Azure przy użyciu [reguł DNAT](rule-processing.md)zapory azure. Pulpit wirtualny systemu Windows (WVD) nie wymaga otwierania dostępu przychodzącego do sieci wirtualnej. Należy jednak zezwolić na zestaw wychodzących połączeń sieciowych dla maszyn wirtualnych WVD, które działają w sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [Co to jest pulpit wirtualny systemu Windows?](../virtual-desktop/overview.md#requirements)

Ten dostęp wychodzący można skonfigurować przy użyciu reguł aplikacji Zapory platformy Azure. Aby uzyskać więcej informacji, zobacz [Samouczek: Wdrażanie i konfigurowanie Zapory platformy Azure przy użyciu witryny Azure Portal](tutorial-firewall-deploy-portal.md).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [pulpicie wirtualnym systemu Windows](https://docs.microsoft.com/azure/virtual-desktop/).