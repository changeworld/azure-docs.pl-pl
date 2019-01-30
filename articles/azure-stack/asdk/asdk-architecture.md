---
title: Usługi Azure Stack Development Kit architektury | Dokumentacja firmy Microsoft
description: Opis architektury usługi Azure Stack Development Kit (ASDK).
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
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 2cee4d75a111f56012782228d8070bb728720aca
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2019
ms.locfileid: "55238349"
---
# <a name="microsoft-azure-stack-development-kit-architecture"></a>Architektura systemu Microsoft Azure Stack Development Kit
Azure Stack Development Kit (ASDK) jest wdrożenia z pojedynczym węzłem usługi Azure Stack. Wszystkie składniki są instalowane na maszynach wirtualnych z systemem jednego komputera-hosta. 

## <a name="logical-architecture-diagram"></a>Diagram architektury logiczne
Poniższy diagram ilustruje logiczną architekturę ASDK i jego składników.

![Architektura ASDK](media/asdk-architecture/image1.png)

## <a name="virtual-machine-roles"></a>Role maszyny wirtualnej
ASDK oferuje przy użyciu następujących maszyn wirtualnych hostowanych na komputerze deweloperskim zestaw hosta usługi:

| Name | Opis |
| ----- | ----- |
| **AzS-ACS01** | Usługi magazynu platformy Azure Stack.|
| **AzS-ADFS01** | Active Directory Federation Services (ADFS).  |
| **AzS-BGPNAT01** | Krawędzi routera i udostępnia możliwości translatora adresów Sieciowych i sieci VPN dla usługi Azure Stack. |
| **AzS-CA01** | Certyfikat usługi urzędu usług roli usługi Azure Stack.|
| **AzS-DC01** | Active Directory, DNS i DHCP usługi dla usługi Microsoft Azure Stack.|
| **AzS-ERCS01** | Konsola odzyskiwania awaryjnego maszyny Wirtualnej. |
| **AzS-GWY01** | Brama brzegowa usługach, takich jak połączenia lokacja lokacja sieci VPN dla sieci dzierżawcy.|
| **AzS-NC01** | Kontroler sieci, która zarządza usługami sieciowymi w usłudze Azure Stack.  |
| **AzS-SLB01** | Równoważenie obciążenia multiplekser usługi w usłudze Azure Stack dla dzierżawcy i usługach infrastruktury Azure Stack.  |
| **AzS-SQL01** | Wewnętrzny magazyn danych dla ról infrastruktury Azure Stack.  |
| **AzS-WAS01** | Usługi Azure Resource Manager i portalu administracyjnego usługi Azure Stack.|
| **AzS-WASP01**| Usługi Azure Resource Manager i portalu użytkowników (dzierżawcy) w usłudze Azure Stack.|
| **AzS-XRP01** | Kontroler zarządzania infrastruktury Microsoft Azure Stack, w tym dostawców zasobów obliczeniowych, sieci i magazynu.|


## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się więcej o podstawowych zadań administracyjnych ASDK](asdk-admin-basics.md)
