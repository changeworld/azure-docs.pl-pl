---
title: Architektura zestawu programowanie stosu Azure | Dokumentacja firmy Microsoft
description: Opis architektury Azure stosu Development Kit (ASDK).
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 68da3ac0eb135f5956dfea76e186d9c57beea79c
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/17/2018
ms.locfileid: "29975867"
---
# <a name="microsoft-azure-stack-development-kit-architecture"></a>Architektura Microsoft Azure stosu Development Kit
Azure stosu Development Kit (ASDK) to wdrożenie jednowęzłowej stosu Azure. Wszystkie składniki są instalowane na maszynach wirtualnych uruchomionych na maszynie jednego hosta. 

## <a name="logical-architecture-diagram"></a>Diagram architektury logiczne
Na poniższym diagramie przedstawiono architektura logiczna ASDK i jego składniki.

![Architektura ASDK](media/asdk-architecture/image1.png)

## <a name="virtual-machine-roles"></a>Role maszyny wirtualnej
ASDK oferuje usługi przy użyciu następujących maszyn wirtualnych hostowanych na komputerze hosta development kit:

| Name (Nazwa) | Opis |
| ----- | ----- |
| **AzS-ACS01** | Usługi magazynu Azure stosu.|
| **AzS-ADFS01** | Usługi federacyjne Active Directory (AD FS).  |
| **AzS-BGPNAT01** | Krawędzi router i zapewnia możliwości translatora adresów Sieciowych i sieci VPN Azure stosu. |
| **AzS-CA01** | Urząd usług Azure stosu roli usług certyfikatów.|
| **AzS-DC01** | Active Directory, DNS i DHCP usługi Microsoft Azure stosu.|
| **AzS-ERCS01** | Konsola awaryjnego odzyskiwania maszyny Wirtualnej. |
| **AzS-GWY01** | Brama brzegowa usług takich jak połączenia sieci VPN lokacja lokacja dla sieci dzierżawcy.|
| **AzS-NC01** | Kontroler sieci, który zarządza usługi sieciowe Azure stosu.  |
| **AzS-SLB01** | Równoważenia obciążenia multiplekser usług Azure stosu dla zarówno dzierżawców, jak i usługi infrastruktury platformy Azure stosu.  |
| **AzS-SQL01** | Dane wewnętrzne przechowywania dla ról infrastruktury Azure stosu.  |
| **AzS-WAS01** | Portal administracyjny stosu platformy Azure i usługi Azure Resource Manager.|
| **AzS-WASP01**| Portalu użytkownika (dzierżawcy) stosu Azure i usługi Azure Resource Manager.|
| **AzS-XRP01** | Kontroler zarządzania infrastruktury dla programu Microsoft Azure stosu, w tym dostawców zasobów obliczeniowych, sieci i magazynu.|


## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się więcej o podstawowych zadań administracyjnych ASDK](asdk-admin-basics.md)
