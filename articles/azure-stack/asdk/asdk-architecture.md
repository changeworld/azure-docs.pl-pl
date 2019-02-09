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
ms.date: 01/21/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 9157acc7517aea56f087a3dbff0fe7114f8b4c87
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2019
ms.locfileid: "55958809"
---
# <a name="microsoft-azure-stack-development-kit-architecture"></a>Architektura systemu Microsoft Azure Stack Development Kit
Azure Stack Development Kit (ASDK) jest wdrożenia z pojedynczym węzłem usługi Azure Stack, działającego na komputerze hosta. Krawędź compontents routingu są instalowane na komputerze hosta, aby zapewnić funkcje usługi translatora adresów Sieciowych i sieci VPN dla usługi Azure Stack. Role infrastruktury w usłudze Azure Stack, uruchom na warstwie komputera fizycznego hosta funkcji Hyper-V.


## <a name="virtual-machine-roles"></a>Role maszyny wirtualnej
ASDK oferuje przy użyciu następujących maszyn wirtualnych hostowanych na komputerze deweloperskim zestaw hosta usługi:

| Name (Nazwa) | Opis |
| ----- | ----- |
| **AzS-ACS01** | Usługi magazynu platformy Azure Stack.|
| **AzS-ADFS01** | Active Directory Federation Services (ADFS).  |
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
