---
title: Aktualizacja usługi Azure Backup centralnej raportowania pakietu zawartości
description: Informacje o aktualizacjach zawartości usługi Kopia zapasowa Azure pack w usłudze Power BI
services: backup
documentationcenter: ''
author: adigan
manager: shivamg
editor: ''
ms.assetid: 59df5bec-d959-457d-8731-7b20f7f1013e
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/24/2018
ms.author: adigan
ms.openlocfilehash: 65ab497b2467846a324d10f8487ab384a01441ee
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52968889"
---
# <a name="update-the-azure-backup-central-reporting-content-pack"></a>Aktualizacja usługi Azure Backup centralnej raportowania pakietu zawartości 

[Pakietu zawartości usługi Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-configure-reports#view-reports-in-power-bi) można wyświetlać raporty dotyczące funkcji Kopia zapasowa centralnej. Pakiet zawartości jest regularnie aktualizowana można dodać więcej funkcji i naprawiaj usterki. W tym artykule wyjaśniono, jak zaktualizować pakiet zawartości. On również informacje o sposobie odroczenie aktualizacji i sprawdzić aktualizacje wprowadzone wraz z upływem czasu.

## <a name="get-updates-to-the-content-pack"></a>Otrzymuj aktualizacje z pakietem zawartości

### <a name="get-the-updated-content-pack"></a>Pobierz zaktualizowany pakiet zawartości
Swoją kopię pakietu zawartości nie zostały wprowadzone żadne zmiany, aktualizuje automatycznie. Po zmianie pakietu zawartości, otrzymasz powiadomienie w usłudze Power BI i wiadomość e-mail z powiadomieniem. Można wybrać pobrać zaktualizowany pakiet zawartości w Twojej wygody. 

### <a name="postpone-the-update"></a>Odroczenie aktualizacji
Najlepszym rozwiązaniem jest zaimportowanie pakietu zawartości do [niestandardowego obszaru roboczego](https://youtu.be/26zyOtyHPJM?t=1m57s). Raporty można teraz edytować.
Jak wspomniano wcześniej Jeśli zmieni pakiet zawartości, zostanie wyświetlone powiadomienie w usłudze Power BI. Można później pobrać pakiet zawartości. 

## <a name="coming-soon"></a>Wkrótce
   
Pakiet zawartości usługi Azure Backup jest zaktualizowana do obsługi obciążeń więcej. Obciążenia obejmują usługi Azure SQL Database dla kopii zapasowych maszyn wirtualnych IaaS i System Center Data Protection Manager. Ta funkcja dodaje do bieżącej obsługi usługi Azure backup i kopii zapasowych maszyn wirtualnych platformy Azure. Ta obsługa oznacza, że można wyświetlać i analizować wszystkie dane kopii zapasowej w jednej centralnej lokalizacji. [Można również dostosować raporty](https://youtu.be/26zyOtyHPJM) odpowiednio do potrzeb swojej organizacji.

Zmiany są wstępnie skonfigurowane raporty, które pochodzą z pakietem zawartości usługi Azure Backup. Zmiany raporty bardziej opisową na potrzeby różnych obciążeń. Osiągającego w nadchodzących zestaw raportów znajduje się tutaj.

### <a name="summary"></a>Podsumowanie
   
![Podsumowanie](./media/backup-azure-central-reporting/AzBackup-Central-Reporting-Summary.png)

### <a name="billing"></a>Rozliczenia

![Rozliczenia](./media/backup-azure-central-reporting/AzBackup-Central-Reporting-Billing.png)

### <a name="compliance"></a>Zgodność

![Zgodność](./media/backup-azure-central-reporting/AzBackup-Central-Reporting-Compliance.png)

### <a name="storage"></a>Magazyn

![Magazyn](./media/backup-azure-central-reporting/AzBackup-Central-Reporting-Storage.png)

### <a name="backup-items"></a>Elementy kopii zapasowej
![Elementy kopii zapasowej](./media/backup-azure-central-reporting/AzBackup-Central-Reporting-BackupItem.png)

### <a name="alerts"></a>Alerty

![Alerty](./media/backup-azure-central-reporting/AzBackup-Central-Reporting-Alerts.png)

### <a name="jobs"></a>Stanowiska

![Stanowiska](./media/backup-azure-central-reporting/AzBackup-Central-Reporting-Jobs.png)
    

## <a name="next-steps"></a>Kolejne kroki

* [Udostępnianie raportów w całej organizacji](https://youtu.be/26zyOtyHPJM)
* [Usługa Azure Backup — często zadawane pytania](backup-azure-backup-faq.md)
