---
title: Monitorowanie alertów kopii zapasowych maszyn wirtualnych platformy Azure
description: Monitorowanie zdarzeń i alertów z zadania tworzenia kopii zapasowej maszyny wirtualnej platformy Azure. Wyślij wiadomość e-mail na podstawie alertów.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: raynew
ms.openlocfilehash: c5c3f498f62d6399534333f8166a514a10f044c5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60464432"
---
# <a name="monitor-alerts-for-azure-virtual-machine-backups"></a>Monitorowanie alertów związanych z kopiami zapasowymi maszyny wirtualnej platformy Azure

Alerty są odpowiedzi z usługi, że ma osiągnięciu lub przekroczenia próg zdarzenia. Wiedząc, podczas rozpoczęcia problemów może okazać się niezbędne do niskich kosztów działalności. Alerty zwykle nie występują zgodnie z harmonogramem, a zatem warto wiedzieć, jak najszybciej po alerty występują. Na przykład gdy zadanie tworzenia kopii zapasowej lub przywracania zakończy się niepowodzeniem, pojawia się alert w ciągu pięciu minut błędu. Na pulpicie nawigacyjnym magazynu na kafelku alerty kopii zapasowej Wyświetla zdarzenia krytyczne i poziom ostrzeżeń. W ustawieniach alerty kopii zapasowej możesz wyświetlić wszystkie zdarzenia. Ale co zrobić, jeśli alarm występuje, gdy użytkownik pracuje na oddzielnych problem? Jeśli nie znasz sytuacji alert pomocnicza niedogodności może to być lub może naruszyć bezpieczeństwo danych. Aby upewnić się, że właściwych osób świadomość alertu — po jego wystąpieniu, należy skonfigurować usługę, aby wysyłać powiadomienia o alertach za pośrednictwem poczty e-mail. Aby uzyskać więcej informacji na temat konfigurowania powiadomień e-mail, zobacz [Konfigurowanie powiadomień](backup-azure-monitor-vms.md#configure-notifications).

## <a name="how-do-i-find-information-about-the-alerts"></a>Jak znaleźć informacje o alertach?

Aby wyświetlić informacje o zdarzeniu, który wygenerował alert, należy otworzyć w sekcji alerty kopii zapasowej. Istnieją dwa sposoby otwierania sekcja alerty kopii zapasowej: z alertami wykonywania kopii zapasowej kafelka na pulpicie nawigacyjnym magazynu lub w sekcji alerty i zdarzenia.

Aby otworzyć blok kafelka alerty kopii zapasowej na kafelku alerty kopii zapasowej:

* Na **alerty kopii zapasowej** kafelków na pulpicie nawigacyjnym magazynu, kliknij przycisk **krytyczny** lub **ostrzeżenie** można wyświetlać zdarzenia operacyjne tego poziomu ważności.

    ![Kafelek alerty kopii zapasowej](./media/backup-azure-monitor-vms/backup-alerts-tile.png)

Aby otworzyć blok kopia zapasowa alerty z sekcji alerty i zdarzenia:

1. Na pulpicie nawigacyjnym magazynu kliknij **wszystkie ustawienia**. ![Przycisk wszystkie ustawienia](./media/backup-azure-monitor-vms/all-settings-button.png)
2. Na **ustawienia** bloku kliknij **alerty i zdarzenia**. ![Alerty i zdarzenia przycisku](./media/backup-azure-monitor-vms/alerts-and-events-button.png)
3. Na **alerty i zdarzenia** bloku kliknij **alerty kopii zapasowej**. ![Przycisk tworzenia kopii zapasowej alertów](./media/backup-azure-monitor-vms/backup-alerts.png)

    **Alerty kopii zapasowej** sekcji otwiera i wyświetla alerty filtrowane.

    ![Kafelek alerty kopii zapasowej](./media/backup-azure-monitor-vms/backup-alerts-critical.png)
4. Aby wyświetlić szczegółowe informacje na temat konkretnego alertu z listy zdarzeń, kliknij alert, aby otworzyć jego **szczegóły** sekcji.

    ![Szczegół zdarzenia](./media/backup-azure-monitor-vms/audit-logs-event-detail.png)

    Aby dostosować atrybuty wyświetlane na liście, zobacz [wyświetlić atrybuty dodatkowe zdarzenia](backup-azure-monitor-vms.md)

## <a name="configure-notifications"></a>Konfigurowanie powiadomień

 Można skonfigurować usługę, aby wysyłała powiadomienia e-mail dla alertów, które wystąpiły w ciągu ostatniej godziny, lub gdy wystąpią określone typy zdarzeń.

Aby skonfigurować powiadomienia e-mail o alertach

1. W menu alerty kopii zapasowej kliknij **skonfigurowanie powiadomień**

    ![Menu alerty kopii zapasowej](./media/backup-azure-monitor-vms/backup-alerts-menu.png)

    Zostanie otwarta sekcja powiadomienia Konfiguruj.

    ![Konfigurowanie powiadomień bloku](./media/backup-azure-monitor-vms/configure-notifications.png)
2. W sekcji powiadomienia Konfigurowanie powiadomień E-mail kliknij **na**.

    Odbiorcy i ważności okien dialogowych mają gwiazdkę obok nich, ponieważ te informacje są wymagane. Podaj co najmniej jeden adres e-mail, a następnie wybierz co najmniej jedną ważność.
3. W **adresaci (E-mail)** okno dialogowe, wpisz adresy e-mail, na który otrzymywać powiadomienia. Użyj formatu: username@domainname.com. Poszczególne adresy e-mail średnikami (;).
4. W **powiadamiania** obszaru, wybierz **na Alert** można wysłać powiadomienia, gdy wystąpi określony alert, lub **podsumowanie godzinowe** do wysłania podsumowanie dla ostatniej godziny.
5. W **ważność** okno dialogowe, wybierz co najmniej jeden poziomy, które ma wyzwalać powiadomienie e-mail.
6. Kliknij pozycję **Zapisz**.

### <a name="what-alert-types-are-available-for-azure-iaas-vm-backup"></a>Jakie typy alertów są dostępne dla maszyn wirtualnych IaaS platformy Azure

   | Poziom alertu | Wysyłania alertów |
   | --- | --- |
   | Krytyczny | Niepowodzenie kopii zapasowej, niepowodzenia odzyskiwania |
   | Ostrzeżenie | dla zadania tworzenia kopii zapasowej zakończyło się pomyślnie z ostrzeżeniami (na przykład: Niektóre moduły zapisujące nie powiodło się podczas tworzenia migawki) |
   | Informacyjne | obecnie nie alerty informacyjne są dostępne dla kopii zapasowej maszyny Wirtualnej platformy Azure |

### <a name="situations-where-email-isnt-sent-even-if-notifications-are-configured"></a>Sytuacje, gdzie poczty e-mail nie jest wysyłana, mimo że powiadomienia zostały skonfigurowane

Istnieją sytuacje, gdy alert nie zostanie wysłany, mimo że powiadomienia zostały prawidłowo skonfigurowane. W następujących sytuacjach, w wiadomości e-mail powiadomienia nie są wysyłane do uniknij szumu spowodowanego alertu:

* Jeśli powiadomień skonfigurowano ustawienie podsumowanie godzinowe oraz alert jest wyświetlany i rozwiązywany w ciągu godziny.
* Zadanie zostało anulowane.
* Zadanie tworzenia kopii zapasowej jest wyzwalane, a następnie kończy się niepowodzeniem, a inne zadanie kopii zapasowej jest w toku.
* Uruchamia zaplanowane zadanie tworzenia kopii zapasowej dla maszyny Wirtualnej z obsługą usługi Resource Manager, ale maszyna wirtualna już nie istnieje.

## <a name="using-activity-logs-to-get-notifications-for-successful-backups"></a>Otrzymuj powiadomienia dla pomyślnie tworzyć kopie zapasowe przy użyciu dzienników aktywności

> [!NOTE]
> Został przeniesiony do nowego modelu programu Przekazywanie dzienników aktywności usługi Azure Backup na magazynów usługi Recovery Services. Niestety to ma wpływ na generowanie dzienników aktywności w chmurach suwerennych platformy Azure. Jeśli użytkownicy suwerennych w chmurze platformy Azure utworzona lub skonfigurowany alerty od dzienników aktywności za pośrednictwem usługi Azure Monitor, jak wspomniano wcześniej w tym miejscu, nie będą one wyzwolone. W takiej sytuacji firma Microsoft zaleca takich użytkownikom korzystanie z ustawień diagnostycznych i obszar roboczy LA lub [raportowania rozwiązania usługi Power BI](backup-azure-configure-reports.md) uzyskać istotne informacje. Ponadto w przypadku wszystkich publicznych regionach platformy Azure, jeśli użytkownika jest zbieranie dzienników aktywności usługi odzyskiwania do obszaru roboczego analizy dzienników, jak wspomniano wcześniej [tutaj](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity), te dzienniki również nie było wyświetlane.

Jeśli chcesz otrzymać powiadomienie po pomyślnym kopie zapasowe, można użyć alerty oparte na [dzienników aktywności](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit) magazynu.

### <a name="login-into-azure-portal"></a>Zaloguj się do witryny Azure portal

Zaloguj się do witryny Azure portal i przejść do odpowiedniego magazynu Azure Recovery Services i kliknij sekcję "Dziennik aktywności" we właściwościach.

### <a name="identify-appropriate-log"></a>Określenie odpowiedniego dziennika

Zastosuj filtry pokazano na poniższej ilustracji, aby sprawdzić, czy otrzymujesz dzienników aktywności dla pomyślnie tworzyć kopie zapasowe. Odpowiednio zmienić zakres czasu, aby wyświetlić rekordy.

![Dzienniki aktywności](./media/backup-azure-monitor-vms/activity-logs-identify.png)

Po kliknięciu segmentu "JSON", aby uzyskać więcej szczegółów i go wyświetlać przez kopiowanie wklejanie go do edytora tekstów. Powinna zostać wyświetlona szczegóły magazynu i elementów, która wywołała dziennik aktywności, czyli element kopii zapasowej.

Następnie kliknij przycisk "Dodaj alert dziennika aktywności" do generowania alertów dla wszystkich tych dzienników.

### <a name="add-activity-log-alert"></a>Dodaj alert dziennika aktywności

Klikając pozycję "Dodaj alert dziennika aktywności" przedstawia ekranu pokazany poniżej

![Alert dziennika aktywności](./media/backup-azure-monitor-vms/activity-logs-alerts-successful.png) subskrypcję i grupę zasobów, które są używane do przechowywania alertu. Kryteria zostaną wstępnie wypełnione. Upewnij się, że wszystkie wartości są istotne dla Twojej wymagań.

Dla pomyślnie tworzyć kopie zapasowe "Poziom" jest oznaczony jako "Informacyjne" i stan jako "Powodzenie".

Jeśli wybierzesz opcję "Zasób" powyżej, alert zostanie wygenerowany, gdy dzienniki aktywności są rejestrowane dla tego zasobu lub magazynu. Jeśli chcesz, aby reguła mogła być stosowana do wszystkich magazynów, należy pozostawić "Zasób" powinien być pusty.

### <a name="define-action-on-alert-firing"></a>Zdefiniuj akcję na uruchomieniu którego alertu

Użyj "grupy akcji" do zdefiniowania akcji podczas generowania alertu. Możesz kliknąć "Akcji typu", aby dowiedzieć się więcej o dostępnych akcji takich/SMS/Integracja z pocztą e-mail za pomocą narzędzia ITSM itd.

![Grupy akcji dziennika aktywności](./media/backup-azure-monitor-vms/activity-logs-alerts-action-group.png)

Po kliknięciu przycisku OK spowoduje wygenerowanie alertu dziennika aktywności i dzienników kolejne działania rejestrowane dla pomyślnie tworzyć kopie zapasowe będą uruchamiane akcji, zgodnie z definicją w grupy akcji.

### <a name="limitations-on-alerts"></a>Ograniczenia dotyczące alertów

Alerty oparte na zdarzeniach podlegają następującym ograniczeniom:

1. Alerty są wyzwalane na wszystkich maszynach wirtualnych w magazynie usługi Recovery Services. Nie można dostosować alert dla podzbioru maszyn wirtualnych w magazynie usługi Recovery Services.
2. Alerty są wysyłane z "alerts-noreply@mail.windowsazure.com". Obecnie nie można zmodyfikować nadawcy wiadomości e-mail.

## <a name="next-steps"></a>Kolejne kroki

Informacje na temat ponownego tworzenia maszyny wirtualnej z punktu odzyskiwania, zapoznaj się z [przywracania maszyn wirtualnych platformy Azure](backup-azure-arm-restore-vms.md).

Aby uzyskać informacje na temat ochrony maszyn wirtualnych, zobacz [Pierwsze spojrzenie: Tworzenie kopii zapasowych maszyn wirtualnych w magazynie usługi Recovery Services](backup-azure-vms-first-look-arm.md).

Dowiedz się więcej o zadaniach zarządzania dla kopii zapasowych maszyn wirtualnych w artykule [kopii zapasowych maszyn wirtualnych Azure zarządzać](backup-azure-manage-vms.md).
