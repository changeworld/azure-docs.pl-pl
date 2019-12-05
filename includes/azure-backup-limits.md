---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: backup
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 12/07/2018
ms.author: dacurwin
ms.custom: include file
ms.openlocfilehash: ca6c57b139b195b94dc7171336cfcc34d18b1872
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74829014"
---
Poniższe limity mają zastosowanie do usługi Azure Backup.

| **Limit** | **Domyślne** |
| --- | --- |
| Serwery lub maszyny, które mogą być zarejestrowane w magazynie. | System Windows Server/Klient systemu Windows/Data Protection Manager System Center: 50. <br/><br/> Maszyny wirtualne IaaS: 1 000.  |
| Rozmiar źródła danych w magazynie magazynu. |54 400 GB maksymalne. Limit nie dotyczy kopii zapasowej maszyny wirtualnej IaaS. |
| Magazyny kopii zapasowych w ramach subskrypcji platformy Azure. |500 magazynów na region. |
| Zaplanuj codzienne kopie zapasowe. |System Windows Server/Klient: trzy dni.<br/> System Center DPM: dwa dni. <br/> Maszyny wirtualne IaaS: raz dziennie.  |
| Dyski danych dołączone do maszyny wirtualnej platformy Azure na potrzeby tworzenia kopii zapasowych. | 16 |
| Indywidualny dysk danych dołączony do maszyny wirtualnej platformy Azure na potrzeby tworzenia kopii zapasowych.| 32 TB|
