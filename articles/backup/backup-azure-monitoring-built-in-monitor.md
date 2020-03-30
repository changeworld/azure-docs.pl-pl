---
title: Monitorowanie obciążeń chronionych za pomocą usługi Azure Backup
description: W tym artykule zapoznaj się z możliwościami monitorowania i powiadamiania dla obciążeń usługi Azure Backup przy użyciu witryny Azure portal.
ms.topic: conceptual
ms.date: 03/05/2019
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
ms.openlocfilehash: de5a82f5ad1d8113b27c07484f2f08f4cf97c759
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294938"
---
# <a name="monitoring-azure-backup-workloads"></a>Monitorowanie obciążeń kopii zapasowej platformy Azure

Usługa Azure Backup udostępnia wiele rozwiązań do tworzenia kopii zapasowych opartych na wymaganiach dotyczących tworzenia kopii zapasowych i topologii infrastruktury (lokalnie i platformy Azure). Każdy użytkownik kopii zapasowej lub administrator powinien zobaczyć, co dzieje się we wszystkich rozwiązaniach i oczekuje się, że zostanie powiadomiony w ważnych scenariuszach. W tym artykule opisano funkcje monitorowania i powiadamiania udostępniane przez usługę Azure Backup.

## <a name="backup-jobs-in-recovery-services-vault"></a>Zadania tworzenia kopii zapasowych w magazynie usług odzyskiwania

Usługa Azure Backup zapewnia wbudowane funkcje monitorowania i alertów dla obciążeń chronionych przez usługę Azure Backup. W ustawieniach magazynu usług odzyskiwania sekcja **Monitorowanie** zawiera wbudowane zadania i alerty.

![Wbudowany monitoring skarbca RS](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltmonitoring.png)

Zadania są generowane podczas wykonywania operacji, takich jak konfigurowanie kopii zapasowej, tworzenia kopii zapasowych, przywracania, usuwania kopii zapasowej i tak dalej.

W tym miejscu przedstawiono zadania z następujących rozwiązań usługi Azure Backup:

- Kopia zapasowa maszyny wirtualnej platformy Azure
- Kopia zapasowa pliku platformy Azure
- Tworzenie kopii zapasowej obciążenia platformy Azure, takie jak SQL i SAP HANA
- Agent usługi Azure Backup (MAB)

Zadania z Programu System Center Data Protection Manager (SC-DPM), Microsoft Azure Backup Server (MABS) NIE są wyświetlane.

> [!NOTE]
> Obciążenia platformy Azure, takie jak kopie zapasowe SQL i SAP HANA w maszynach wirtualnych platformy Azure, mają ogromną liczbę zadań tworzenia kopii zapasowych. Na przykład kopie zapasowe dziennika można uruchomić co 15 minut. W związku z tym dla takich obciążeń bazy danych wyświetlane są tylko operacje wyzwalane przez użytkownika. Zaplanowane operacje tworzenia kopii zapasowych NIE są wyświetlane.

## <a name="backup-alerts-in-recovery-services-vault"></a>Alerty kopii zapasowych w magazynie usług odzyskiwania

Alerty to przede wszystkim scenariusze, w których użytkownicy są powiadamiani, aby mogli podjąć odpowiednie działania. Sekcja **Alerty kopii zapasowej** zawiera alerty generowane przez usługę Azure Backup. Te alerty są definiowane przez usługę, a użytkownik nie może tworzyć żadnych alertów.

### <a name="alert-scenarios"></a>Scenariusze alertów

Następujące scenariusze są definiowane przez usługę jako alerty scenariuszy.

- Niepowodzenia wykonywania kopii zapasowej/przywracania
- Wykonanie kopii zapasowej zakończyło się pomyślnie z ostrzeżeniami dla agenta usługi Azure Backup (MAB)
- Zatrzymaj ochronę dzięki ochronie danych/Ochronie przed zatrzymaniem dzięki usuwaniu danych

### <a name="alerts-from-the-following-azure-backup-solutions-are-shown-here"></a>Alerty z następujących rozwiązań usługi Azure Backup są wyświetlane tutaj

- Kopie zapasowe maszyn wirtualnych platformy Azure
- Kopie zapasowe plików platformy Azure
- Kopie zapasowe obciążenia platformy Azure, takie jak SQL, SAP HANA
- Agent usługi Azure Backup (MAB)

> [!NOTE]
> Alerty z Programu System Center Data Protection Manager (SC-DPM), Microsoft Azure Backup Server (MABS) NIE są wyświetlane w tym miejscu.

### <a name="consolidated-alerts"></a>Alerty skonsolidowane

W przypadku rozwiązań do tworzenia kopii zapasowych obciążenia platformy Azure, takich jak SQL i SAP HANA, kopie zapasowe dziennika mogą być generowane bardzo często (do co 15 minut zgodnie z zasadami). Jest więc również możliwe, że błędy kopii zapasowej dziennika są również bardzo częste (do co 15 minut). W tym scenariuszu użytkownik końcowy zostanie przeciążony, jeśli alert jest wywoływany dla każdego wystąpienia awarii. Tak więc alert jest wysyłany dla pierwszego wystąpienia i jeśli kolejne błędy są z powodu tej samej głównej przyczyny, a następnie dalsze alerty nie są generowane. Pierwszy alert jest aktualizowany z liczbą błędów. Ale jeśli alert jest inaktywowany przez użytkownika, następne wystąpienie wyzwoli inny alert i będzie traktowany jako pierwszy alert dla tego wystąpienia. W ten sposób usługa Azure Backup wykonuje konsolidację alertów dla kopii zapasowych SQL i SAP HANA.

### <a name="exceptions-when-an-alert-is-not-raised"></a>Wyjątki, gdy alert nie jest zgłaszany

Istnieje kilka wyjątków, gdy alert nie jest zgłaszany w przypadku błędu. Oto one:

- Użytkownik jawnie anulował uruchomione zadanie
- Zadanie nie powiedzie się, ponieważ inne zadanie tworzenia kopii zapasowej jest w toku (nic do działania tutaj, ponieważ musimy tylko czekać na poprzednie zadanie, aby zakończyć)
- Zadanie tworzenia kopii zapasowej maszyny Wirtualnej kończy się niepowodzeniem, ponieważ kopia zapasowa maszyny wirtualnej platformy Azure już nie istnieje
- [Alerty skonsolidowane](#consolidated-alerts)

Powyższe wyjątki są zaprojektowane ze zrozumieniem, że wynik tych operacji (głównie wyzwalane przez użytkownika) pojawia się natychmiast na klientach portalu/PS/CLI. Dlatego użytkownik jest natychmiast świadomy i nie potrzebuje powiadomienia.

### <a name="alert-types"></a>Typy alertów

Na podstawie ważności alertów alerty można zdefiniować w trzech typach:

- **Krytyczne:** Co do zasady każda awaria kopii zapasowej lub odzyskiwania (zaplanowane lub wywołane przez użytkownika) prowadzi do generowania alertu i będzie wyświetlana jako alert krytyczny, a także destrukcyjne operacje, takie jak usuwanie kopii zapasowej.
- **Ostrzeżenie:** Jeśli operacja tworzenia kopii zapasowej zakończy się pomyślnie, ale z kilkoma ostrzeżeniami, są one wyświetlane jako alerty ostrzegawcze.
- **Informacje:** Na dzień dzisiejszy usługa Azure Backup nie generuje żadnego alertu informacyjnego.

## <a name="notification-for-backup-alerts"></a>Powiadomienie o alertach dotyczących kopii zapasowej

> [!NOTE]
> Konfigurację powiadomień można wykonać tylko za pośrednictwem usługi Azure Portal. Obsługa interfejsu API/REST/usługi Azure Resource Manager nie jest obsługiwana.

Po uruchomieniu alertu użytkownicy są powiadamiani. Usługa Azure Backup udostępnia wbudowany mechanizm powiadamiania za pośrednictwem poczty e-mail. Można określić poszczególne adresy e-mail lub listy dystrybucyjne, które mają być powiadamiane podczas generowania alertu. Można również wybrać, czy otrzymywać powiadomienia dla każdego pojedynczego alertu lub grupować je w podsumowaniu co godzinę, a następnie otrzymywać powiadomienia.

![Wbudowane powiadomienie e-mail o wbudowanym urzeszeniu usługi RS Vault](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

Po skonfigurowaniu powiadomienia otrzymasz powitalną lub wprowadzającą wiadomość e-mail. Potwierdza to, że usługa Azure Backup może wysyłać wiadomości e-mail na te adresy po wyświetleniu alertu.<br>

Jeśli częstotliwość została ustawiona na podsumowanie godzinowe i alert został podniesiony i rozwiązany w ciągu godziny, nie będzie częścią nadchodzącego podsumowania godzinowego.

> [!NOTE]
>
> - Jeśli wykonywana jest destrukcyjna operacja, taka jak **ochrona zatrzymania z usuniętymi danymi,** zostanie wyświetlony alert i zostanie wysłana wiadomość e-mail do właścicieli subskrypcji, administratorów i współadministratorów, nawet jeśli powiadomienia NIE są skonfigurowane dla magazynu odzyskiwania usługi.
> - Aby skonfigurować powiadomienie dla pomyślnych zadań, użyj [usługi Log Analytics](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-workspace).

## <a name="inactivating-alerts"></a>Inaktywowanie alertów

Aby dezaktywować/rozwiązać aktywny alert, możesz kliknąć element listy odpowiadający alertowi, który chcesz dezaktywować. Spowoduje to otwarcie ekranu, na który wyświetlane są szczegółowe informacje o wpisie, a u góry przycisk "Inactivate". Kliknięcie tego przycisku spowoduje zmianę stanu alertu na "Nieaktywny". Możesz również dezaktywować alert, klikając prawym przyciskiem myszy element listy odpowiadający tego wpisowi i wybierając opcję "Dezaktywuj".

![Dezaktywacja alertów w magazynie RS](media/backup-azure-monitoring-laworkspace/vault-alert-inactivation.png)

## <a name="next-steps"></a>Następne kroki

[Monitorowanie obciążeń kopii zapasowych platformy Azure przy użyciu usługi Azure Monitor](backup-azure-monitoring-use-azuremonitor.md)
