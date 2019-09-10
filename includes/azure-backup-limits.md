---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: backup
author: rayne-wiselman
ms.service: backup
ms.topic: include
ms.date: 12/07/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 9e9c09c1825f5c8383a708e8bd343146396f878e
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2019
ms.locfileid: "67183631"
---
Poniższe limity mają zastosowanie do usługi Azure Backup.

| **Limit** | **Domyślne** |
| --- | --- |
| Serwery lub maszyny, które mogą być zarejestrowane w magazynie. | System Windows Server/Klient systemu Windows/Data Protection Manager System Center: 50. <br/><br/> Maszyny wirtualne IaaS: 1 000.  |
| Rozmiar źródła danych w magazynie magazynu. |54 400 GB maksymalne. Limit nie dotyczy kopii zapasowej maszyny wirtualnej IaaS. |
| Magazyny kopii zapasowych w ramach subskrypcji platformy Azure. |500 magazynów na region. |
| Zaplanuj codzienne kopie zapasowe. |Serwer/klient systemu Windows: Trzy dni.<br/> System Center DPM: Dwa dni. <br/> Maszyny wirtualne IaaS: Raz dziennie.  |
| Dyski danych dołączone do maszyny wirtualnej platformy Azure na potrzeby tworzenia kopii zapasowych. | 16 |
| Indywidualny dysk danych dołączony do maszyny wirtualnej platformy Azure na potrzeby tworzenia kopii zapasowych.| 4 095 GB|
