---
title: 'Usługa Azure Backup: Monitorowanie usługi Azure Backup chronionych obciążeń'
description: Monitorowanie obciążeń usługi Azure Backup przy użyciu witryny Azure portal
services: backup
author: pvrk
manager: shivamg
keywords: Usługa Azure Backup; Alerty;
ms.service: backup
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: pullabhk
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
ms.openlocfilehash: 2513f2e7a2e82a541fa5638b70d0543192c84765
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2019
ms.locfileid: "56885354"
---
# <a name="monitoring-azure-backup-workloads"></a>Monitorowanie obciążeń usługi Azure Backup

Usługa Azure Backup udostępnia wiele rozwiązania tworzenia kopii zapasowych na podstawie kopii zapasowej topologii wymagań i infrastruktury (vs lokalnej platformy Azure). Wszelkich kopii zapasowych przez użytkownika/administratora powinien zostać wyświetlony, co się dzieje na wszystkich rozwiązań i powinien otrzymywać ważnych scenariuszy. Ten artykuł szczegółowo opisuje funkcje monitorowania i powiadomień, udostępniony przez usługę Azure Backup.

## <a name="backup-jobs-in-recovery-services-vault"></a>Zadania tworzenia kopii zapasowych w magazynie usługi Recovery Services

Usługa Azure Backup udostępnia wbudowane monitorowania i alertów w przypadku obciążeń chronionych przez usługę Azure Backup. W obszarze Ustawienia magazynu usługi Recovery Services sekcję "Monitorowanie" udostępnia wbudowane zadania i alerty.

![RS magazynu, wbudowane monitorowanie](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltmonitoring.png)

Zadania są generowane, gdy wykonywane są operacje, takie jak skonfigurowanie kopii zapasowej, operacji tworzenia kopii zapasowej, operacji przywracania, Usuń kopii zapasowej itp.
Zadania z następujących rozwiązań usługi Azure Backup są wyświetlane w tym miejscu.

- Kopie zapasowe maszyn wirtualnych platformy Azure
- Kopie zapasowe plików platformy Azure
- Tworzenie kopii zapasowych obciążenie platformy Azure, takimi jak SQL
- Agent usługi Azure Backup (MAB)

Zadania z System Center Data Protection Manager (SC DPM), Microsoft Azure Backup Server (MABS) nie są wyświetlane w tym miejscu.

> [!NOTE]
> W odniesieniu do obciążeń platformy Azure, takich jak tworzenie kopii zapasowych SQL, w ramach maszyn wirtualnych platformy Azure liczbę zadań kopii zapasowej są ogromne. Na przykład można uruchomić tworzenie kopii zapasowych dzienników, aby co 15 minut. W związku z tym w przypadku takich obciążeń bazy danych operacji tylko użytkownika wyzwalane są wyświetlane. Zaplanowane operacje tworzenia kopii zapasowej nie są wyświetlane.

## <a name="backup-alerts-in-recovery-services-vault"></a>Alerty kopii zapasowej w magazynie usługi Recovery Services

Alerty są głównie scenariusze, w której klient potrzebuje do otrzymywania powiadomień, tak, aby mogli podjąć odpowiednią akcję. W sekcji "Kopia zapasowa alerty" przedstawia alerty wygenerowane przez usługę Azure Backup. Te alerty są zdefiniowane przez usługę, a użytkownik nie może niestandardowe utworzyć żadnych alertów. Poniższe scenariusze są definiowane przez usługę jako dla scenariuszy

- Niepowodzenia wykonywania kopii zapasowej/przywracania
- Kopii zapasowej zakończyło się pomyślnie z ostrzeżeniami
- Zatrzymaj ochronę za pomocą zachowanych danych/Zatrzymaj ochronę za pomocą usuwania danych

Istnieje kilka wyjątków, gdy alert nie jest zgłaszany w przypadku niepowodzenia.

- Użytkownik anulował jawnie uruchomionego zadania
- Zadanie nie powiedzie się, ponieważ inne zadanie kopii zapasowej jest w toku (rób do działania w tym miejscu, ponieważ mamy po prostu poczekaj na zakończenie poprzedniego zadania)
- Zadania tworzenia kopii zapasowej maszyny Wirtualnej nie powiedzie się, ponieważ maszyna wirtualna platformy Azure utworzona kopia zapasowa już istnieje.

Powyższe wyjątki zostały zaprojektowane z warunkiem, że wynik tych operacji (przede wszystkim użytkownika wyzwalane) pojawia się natychmiast na klientach portal/PS/interfejsu wiersza polecenia. W związku z tym użytkownik jest od razu wiedzieć i nie wymaga powiadomienie.

Alerty z następujących rozwiązań usługi Azure Backup są wyświetlane w tym miejscu.

- Kopie zapasowe maszyn wirtualnych platformy Azure
- Kopie zapasowe plików platformy Azure
- Tworzenie kopii zapasowych obciążenie platformy Azure, takimi jak SQL
- Agent usługi Azure Backup (MAB)

> [!NOTE]
> Alerty z System Center Data Protection Manager (SC DPM), Microsoft Azure Backup Server (MABS) nie są wyświetlane w tym miejscu.

Oparte na ważność alertu, alerty można zdefiniować trzy typy:

- **Krytyczne:** W zasadzie jakiekolwiek niepowodzenie tworzenia kopii zapasowej lub odzyskiwania (zaplanowane lub wyzwalane przez użytkownika) może doprowadzić do generowania alertu i będzie wyświetlany jako alert krytyczny. Również destrukcyjne operacje, takie jak usuwanie kopii zapasowej również generować alerty krytyczne.
- **Ostrzeżenie**: Jeśli operacji tworzenia kopii zapasowej zakończyła się pomyślnie, ale z kilku ostrzeżenia są wyświetlane jako alerty ostrzegawcze.
- **Informacyjny**: Obecnie nie informacyjny alert jest generowany przez usługę Azure Backup.

### <a name="notification-for-backup-alerts"></a>Powiadomienia dla alertów kopii zapasowej

> [!NOTE]
> Konfiguracja powiadomienia może odbywać się wyłącznie za pośrednictwem witryny Azure Portal. Obsługa interfejsu wiersza polecenia/PS/REST API/usługi Azure Resource Manager szablonu nie jest jeszcze dostępna.

Po aktywowaniu alertu, klient musi Otrzymuj powiadomienia. Usługa Azure Backup udostępnia mechanizm wbudowanych powiadomień pocztą e-mail. Można określić adresy e-mail poszczególnych lub listy dystrybucyjne, aby otrzymywać powiadomienia, gdy alert zostanie wygenerowany. Możesz również wybrać, czy otrzymywania powiadomień o każdym alercie indywidualnych lub je pogrupować w podsumowanie godzinowe, a następnie otrzymaj powiadomienie.

![Vault RS wbudowane powiadomienia e-mail](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

Po skonfigurowaniu powiadomień, otrzymasz Witaj/wprowadzającą wiadomość e-mail, który potwierdza, że kopia zapasowa Azure wysyłać wiadomości e-mail na te adresy, gdy zostanie zgłoszony alert. Jeśli ustawiono częstotliwość podsumowanie godzinowe oraz alert był wyświetlany i rozwiązywany w ciągu godziny, nie będzie częścią nadchodzących podsumowanie godzinowe.

> [!NOTE]
> Jeśli wykonywana jest operacją destrukcyjną, takie jak "Zatrzymaj ochronę za pomocą usuwania danych", zostanie zgłoszony alert, a następnie zostanie wysłana wiadomość e-mail do właścicieli subskrypcji, Administratorzy i współadministratorzy, nawet jeśli nie skonfigurowano powiadomienia dla magazynu RS.

## <a name="next-steps"></a>Kolejne kroki

[Monitorowanie kopii zapasowych obciążeń platformy Azure przy użyciu usługi Azure Monitor](backup-azure-monitoring-use-azuremonitor.md)