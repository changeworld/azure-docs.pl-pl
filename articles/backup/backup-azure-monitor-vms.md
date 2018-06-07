---
title: Monitoruj alerty kopii zapasowej maszyn wirtualnych platformy Azure
description: Monitorowanie zdarzeń i alertów z zadań tworzenia kopii zapasowej maszyny wirtualnej platformy Azure. Wyślij wiadomość e-mail na podstawie alertów.
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: markgal
ms.openlocfilehash: 3783014738ec4e8f185531773b1259dc63e7f49f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606311"
---
# <a name="monitor-alerts-for-azure-virtual-machine-backups"></a>Monitorowanie alertów związanych z kopiami zapasowymi maszyny wirtualnej platformy Azure
Alerty są odpowiedzi z usługi, czy próg zdarzenia zostały spełnione lub przekroczenia. Uzyskiwanie informacji o tym, gdy rozpoczęcia problemów może być krytyczne zachowaniu niskich kosztów biznesowych. Alerty zwykle nie występują zgodnie z harmonogramem, a więc warto wiedzieć, jak najszybciej po wystąpieniu alerty. Na przykład gdy zadanie tworzenia kopii zapasowej lub przywracania nie powiodło się, alert występuje w ciągu pięciu minut błędu. Na pulpicie nawigacyjnym magazynu w kafelku alerty kopii zapasowej Wyświetla zdarzeń krytycznych i poziom ostrzeżeń. W ustawieniach alerty kopii zapasowej można wyświetlić wszystkie zdarzenia. Ale co zrobić, jeśli alarm występuje, gdy pracujesz na oddzielnych problem? Jeśli nie znasz w przypadku alertu, może to być pomocnicza niedogodności lub może naruszyć bezpieczeństwo danych. Aby upewnić się, że osobom potrafią alertu — Jeśli występuje on, należy skonfigurować usługę, aby wysłać powiadomienia o alertach pocztą e-mail. Aby uzyskać więcej informacji na temat konfigurowania powiadomień e-mail, zobacz [skonfigurować powiadomienia](backup-azure-monitor-vms.md#configure-notifications).

## <a name="how-do-i-find-information-about-the-alerts"></a>Jak znaleźć informacje o alertach?
Aby wyświetlić informacje o zdarzeniu, która wywołała alert, należy otworzyć sekcji alerty kopii zapasowej. Istnieją dwa sposoby otwarta sekcja alerty kopii zapasowej: z alerty kopii zapasowej kafelka na pulpicie nawigacyjnym magazynu lub z sekcji alertów i zdarzeń.

Aby otworzyć blok alerty kopii zapasowej w kafelku alerty kopii zapasowej:

* Na **alerty kopii zapasowej** kafelka na pulpicie nawigacyjnym magazynu, kliknij przycisk **krytyczny** lub **ostrzeżenie** można wyświetlać zdarzenia operacyjne dla tego poziomu ważności.

    ![Kafelek alerty kopii zapasowej](./media/backup-azure-monitor-vms/backup-alerts-tile.png)

Aby otworzyć blok alerty kopii zapasowej z sekcji alerty i zdarzenia:

1. Na pulpicie nawigacyjnym magazynu, kliknij **wszystkie ustawienia**. ![Przycisk wszystkie ustawienia](./media/backup-azure-monitor-vms/all-settings-button.png)
2. Na **ustawienia** bloku, kliknij przycisk **alerty i zdarzenia**. ![Przycisk alertów i zdarzeń](./media/backup-azure-monitor-vms/alerts-and-events-button.png)
3. Na **alerty i zdarzenia** bloku, kliknij przycisk **alerty kopii zapasowej**. ![Przycisk tworzenia kopii zapasowej alertów](./media/backup-azure-monitor-vms/backup-alerts.png)

    **Alerty kopii zapasowej** sekcji otwiera i wyświetla filtrowane alerty.

    ![Kafelek alerty kopii zapasowej](./media/backup-azure-monitor-vms/backup-alerts-critical.png)
4. Aby wyświetlić szczegółowe informacje na temat określonego alertu, z listy zdarzeń, kliknij alert, aby otworzyć jego **szczegóły** sekcji.

    ![Szczegół zdarzenia](./media/backup-azure-monitor-vms/audit-logs-event-detail.png)

    Aby dostosować wyświetlane na liście atrybutów, zobacz [wyświetlić atrybuty dodatkowe zdarzenia](backup-azure-monitor-vms.md#view-additional-event-attributes)

## <a name="configure-notifications"></a>Konfigurowanie powiadomień
 Można skonfigurować usługę do wysyłania powiadomień e-mail dla alertów, które wystąpiły w ciągu ostatnich godzin lub po wystąpieniu określonych typach zdarzeń.

Aby skonfigurować powiadomienia e-mail dla alertów

1. W menu alerty kopii zapasowej, kliknij przycisk **skonfigurować powiadomienia**

    ![Menu alerty kopii zapasowej](./media/backup-azure-monitor-vms/backup-alerts-menu.png)

    Otwiera sekcji konfiguracji powiadomień.

    ![Konfigurowanie powiadomień bloku](./media/backup-azure-monitor-vms/configure-notifications.png)
2. W sekcji Konfigurowanie powiadomień wiadomości E-mail z powiadomieniem, kliknij polecenie **na**.

    Adresatów i ważność okien dialogowych mieć gwiazdką, ponieważ te informacje są wymagane. Podaj co najmniej jeden adres e-mail, a następnie wybierz co najmniej jeden ważność.
3. W **adresatów (Poczta E-mail)** okna dialogowego, wpisz adresy e-mail, na który otrzymywać powiadomienia. Użyj formatu: username@domainname.com. Oddzielaj adresy e-mail średnikami (;).
4. W **powiadamiania** obszaru, wybierz **na Alert** można wysłać powiadomienia, gdy wystąpi określony alert, lub **co godzinę szyfrowanego** do wysłania podsumowanie dla ostatniej godziny.
5. W **ważność** okno dialogowe, wybierz co najmniej jeden poziom, które wyzwalacza powiadomienia e-mail.
6. Kliknij pozycję **Zapisz**.

   ### <a name="what-alert-types-are-available-for-azure-iaas-vm-backup"></a>Jakie typy alertów są dostępne dla kopii zapasowych maszyn wirtualnych IaaS platformy Azure?
   | Poziom alertu | Wysyłania alertów |
   | --- | --- |
   | Krytyczne | niepowodzenia tworzenia kopii zapasowej, niepowodzenia odzyskiwania |
   | Ostrzeżenie | w przypadku zadań tworzenia kopii zapasowej zakończyło się pomyślnie z ostrzeżeniami (na przykład: Niektóre moduły zapisujące nie powiodło się podczas tworzenia migawki) |
   | Informacyjne | obecnie alerty informacyjne nie są dostępne dla kopii zapasowej maszyny Wirtualnej Azure |

### <a name="are-there-situations-where-email-isnt-sent-even-if-notifications-are-configured"></a>Czy występują sytuacje, w których wiadomość e-mail nie jest wysyłana, mimo że powiadomienia zostały skonfigurowane?
Istnieją sytuacje, w którym alert nie są wysyłane, nawet jeśli powiadomienia zostały prawidłowo skonfigurowane. W następujących sytuacjach wiadomości e-mail powiadomienia nie są wysyłane do uniknięcia szumu alertu:

* Jeśli skonfigurowano powiadomienia szyfrowanego co godzinę, a alert jest uruchamiany i rozpoznać w ciągu godziny.
* Zadanie zostało anulowane.
* Zadanie tworzenia kopii zapasowej zostanie wywołany, a następnie nie, a inne zadanie tworzenia kopii zapasowej jest w toku.
* Uruchamia zaplanowane zadanie tworzenia kopii zapasowej dla maszyny Wirtualnej z obsługą usługi Resource Manager, ale maszyna wirtualna już nie istnieje.

## <a name="using-activity-logs-to-get-notifications-for-successful-backups"></a>Aby otrzymywać powiadomienia dla pomyślnie utworzone kopie zapasowe przy użyciu Dzienniki aktywności

Jeśli chcesz zgłaszane po kopii zapasowych zakończą się pomyślnie, można użyć alerty oparte na [Dzienniki aktywności](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit) magazynu.

### <a name="login-into-azure-portal"></a>Zaloguj się do portalu Azure
Zaloguj się do portalu Azure i przejść do odpowiedniego magazynu usług odzyskiwania Azure i kliknij sekcję "Dziennik aktywności" we właściwościach.

### <a name="identify-appropriate-log"></a>Zidentyfikuj odpowiednie dziennika

Zastosuj filtry przedstawiono na rysunku poniżej, aby sprawdzić, czy są odbiera Dzienniki aktywności dla pomyślnie utworzone kopie zapasowe. Zmień zakres czasu odpowiednio do wyświetlania rekordów.

![Dzienniki aktywności](./media/backup-azure-monitor-vms/activity-logs-identify.png)

Po kliknięciu segmentu "JSON", aby uzyskać więcej szczegółowych informacji i go wyświetlać przez kopiowania wklejenie go do edytora tekstu. Powinna zostać wyświetlona szczegółów magazynu i elementów, które uruchamiane dziennik aktywności, tj. element kopii zapasowej.

Następnie kliknij przycisk "Dodaj alert dziennika aktywności" do generowania alertów dotyczących wszystkich tych dzienników.

### <a name="add-activity-log-alert"></a>Dodaj alert dziennika aktywności

Klikając przycisk "Dodaj działanie dziennika alert" wyświetli ekranu w sposób przedstawiony poniżej

![Alert dziennika aktywności](./media/backup-azure-monitor-vms/activity-logs-alerts-successful.png)
    
Subskrypcji i grupy zasobów są używane do przechowywania alertu. Kryteria, zostaną wstępnie wypełnione. Upewnij się, że wszystkie wartości są istotne dla Twojej wymagań.

Pomyślnie utworzone kopie zapasowe, aby uzyskać poziomu jest oznaczony jako "Informacyjny" i stan "Powodzenie".

W przypadku wybrania "Zasób" powyżej, alert zostanie wygenerowany, gdy dzienniki aktywności są rejestrowane dla tego zasobu lub magazynu. Jeśli chcesz reguły mają zastosowanie do wszystkich magazynów, pozostaw "Zasób" powinien być pusty.

### <a name="define-action-on-alert-firing"></a>Zdefiniuj akcji dla uruchamiania alertu

"Grupa akcji" służy do definiowania akcji podczas generowania alertu. Możesz kliknąć opcję "Typ akcji" Aby dowiedzieć się więcej o dostępnych akcji takich/SMS/Integracja z pocztą e-mail z Zarządzanie usługami IT — itp.

![Grupy akcji dziennika działań](./media/backup-azure-monitor-vms/activity-logs-alerts-action-group.png)


Po kliknięciu przycisku OK zostanie wygenerowany alert dziennika aktywności i Dzienniki aktywności kolejnych zarejestrowane dla pomyślnie utworzone kopie zapasowe będą uruchamiane akcji, zgodnie z definicją w grupie akcji.

### <a name="limitations-on-alerts"></a>Ograniczenia dotyczące alertów
Alerty oparte na zdarzeniu obowiązują następujące ograniczenia:

1. Alerty są wyzwalane na wszystkich maszynach wirtualnych w magazynie usług odzyskiwania. Nie można dostosować alert dotyczący podzbiór maszyn wirtualnych w magazynie usług odzyskiwania.
2. Alerty są wysyłane z "alerts-noreply@mail.windowsazure.com". Obecnie nie można zmodyfikować nadawcą wiadomości e-mail.

## <a name="next-steps"></a>Kolejne kroki
Informacje dotyczące ponownego tworzenia maszyny wirtualnej z punktu odzyskiwania, zapoznaj się z [przywracanie maszyn wirtualnych Azure](backup-azure-arm-restore-vms.md).

Jeśli potrzebujesz informacji na temat ochrony maszyn wirtualnych, zobacz [Pierwsze spojrzenie: Utwórz kopię zapasową maszyn wirtualnych do magazynu usług odzyskiwania](backup-azure-vms-first-look-arm.md). 

Dowiedz się więcej o zadaniach zarządzania dla kopii zapasowych maszyn wirtualnych w artykule [kopii zapasowych maszyn wirtualnych Azure zarządzanie](backup-azure-manage-vms.md).
