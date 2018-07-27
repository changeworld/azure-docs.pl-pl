---
title: Aktualizowanie usługi Azure Backup centralnej raportowania pakietu zawartości
description: Informacje o aktualizacjach pakietem zawartości usługi Azure Backup w usłudze Power BI
services: backup
documentationcenter: ''
author: adigan
manager: shivamg
editor: ''
ms.assetid: 59df5bec-d959-457d-8731-7b20f7f1013e
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: adigan;
ms.openlocfilehash: 07774234849d96b9a44678b68ca7b13b6b4830f9
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2018
ms.locfileid: "39265820"
---
# <a name="updating-azure-backup-central-reporting-content-pack"></a>Aktualizowanie usługi Azure Backup centralnej raportowania pakietu zawartości 

[Usługa Azure Backup pakietu zawartości](https://docs.microsoft.com/azure/backup/backup-azure-configure-reports#view-reports-in-power-bi) może służyć do centralnie wyświetlić raporty dotyczące kopii zapasowych. Pakiet zawartości jest regularnie aktualizowana, aby dodać więcej funkcji i rozwiązuje usterki. W tym artykule poprowadzi Cię przez kroki, aby zaktualizować pakiet zawartości, opóźnienia, aktualizacji i aktualizacje, które zostały wprowadzone wraz z upływem czasu.

## <a name="how-to-get-updates-to-the-content-pack"></a>Jak uzyskać aktualizacje z pakietem zawartości

### <a name="to-get-the-updated-content-pack"></a>Aby uzyskać zaktualizowany pakiet zawartości
Swoją kopię pakietu zawartości zostaną zaktualizowani automatycznie, jeśli nie zostały wprowadzone zmiany do niego. W przypadku, gdy pakiet zawartości został zmieniony, otrzymasz powiadomienie w usłudze Power Bi i wiadomość e-mail z powiadomieniem o takie same. Można pobrać zaktualizowany pakiet zawartości zgodnie z Twojej wygody. 

### <a name="to-delay-the-update"></a>Aby opóźnić aktualizacji
Najlepszym rozwiązaniem jest zaimportowanie pakietu zawartości do [niestandardowego obszaru roboczego](https://youtu.be/26zyOtyHPJM?t=1m57s). Masz teraz możliwość edytowania raportów.
Jak wspomniano powyżej, jeśli pakiet zawartości zostanie zmienione, zostanie wyświetlone powiadomienie w usłudze Power BI. Można później pobrać pakiet zawartości. 

## <a name="coming-soon"></a>Wkrótce
   
Usługa Azure Backup pakiet zawartości jest aktualizowana do obsługi obciążeń więcej jako bazy danych SQL w usłudze Kopia zapasowa maszyn wirtualnych IaaS i SC programu DPM, oprócz bieżącej obsługi MAB i kopii zapasowych maszyn wirtualnych platformy Azure. Oznacza to, że wkrótce będzie mogła wyświetlać i analizować dane wszystkie dane kopii zapasowej w jednej centralnej lokalizacji. [Raportów można także dostosowywać](https://youtu.be/26zyOtyHPJM) odpowiednio do potrzeb swojej organizacji.

Aby wprowadzić bardziej opisową raportów na potrzeby różnych obciążeń, zestaw raportów wstępnie skonfigurowany przy użyciu pakietu zawartości usługi Azure Backup został zmieniony. Osiągającego w nadchodzących zestaw raportów jest dostępny tutaj:

### <a name="summary"></a>Podsumowanie
   
![Podsumowanie](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-Summary.png)

### <a name="billing"></a>Rozliczenia

![Rozliczenia](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-Billing.png)

### <a name="compliance"></a>Zgodność

![Zgodność](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-Compliance.png)

### <a name="storage"></a>Magazyn

![Magazyn](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-Storage.png)

### <a name="backup-items"></a>Elementy kopii zapasowej
![BackupItems](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-BackupItem.png)

### <a name="alerts"></a>Alerty

![Alerty](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-Alerts.png)

### <a name="jobs"></a>Zadania

![Zadania](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-Jobs.png)
    

## <a name="next-steps"></a>Następne kroki:

* [Udostępnianie raportów w Twojej organizacji](https://youtu.be/26zyOtyHPJM)
* [Usługa Azure Backup — często zadawane pytania](backup-azure-backup-faq.md)
