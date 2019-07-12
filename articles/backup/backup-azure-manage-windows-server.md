---
title: Zarządzanie magazynami usługi Azure Recovery Services i serwerami
description: Zarządzaj zadania i alerty w magazynie usługi Azure Recovery Services.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: raynew
ms.openlocfilehash: b447290a6910d144703bb796290908d0fc21b924
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705135"
---
# <a name="monitor-and-manage-recovery-services-vaults"></a>Monitorowanie magazynów usługi Recovery Services i zarządzanie nimi

W tym artykule wyjaśniono, jak używać magazynu usługi Recovery Services **Przegląd** pulpit nawigacyjny, aby monitorować i zarządzać nimi Magazyny usługi Recovery Services. Po otwarciu magazyn usługi Recovery Services z listy, **Przegląd** zostanie otwarty pulpit nawigacyjny wybranego magazynu. Pulpit nawigacyjny zawiera szczegółowe informacje o magazynie. Istnieją *Kafelki* przedstawiające: stan krytyczny i alerty ostrzegawcze w toku i zakończonych niepowodzeniem zadań tworzenia kopii zapasowej i ilości magazyn lokalnie nadmiarowy (LRS) i Magazyn geograficznie nadmiarowy (GRS) używane. Jeśli tworzysz kopię zapasową maszyn wirtualnych platformy Azure w magazynie [ **Stan wstępnego sprawdzania kopii zapasowej** Kafelek Wyświetla wszystkie elementy krytyczna lub poważna](https://azure.microsoft.com/blog/azure-vm-backup-pre-checks/). Poniższa ilustracja **Przegląd** pulpit nawigacyjny dla **magazynu firmy Contoso**. **Elementy kopii zapasowej** kafelkowi są zarejestrowane w magazynie dziewięć elementów.

![pulpitu nawigacyjnego magazynu usług odzyskiwania](./media/backup-azure-manage-windows-server/rs-vault-blade.png)

Wymagania wstępne w tym artykule: subskrypcji platformy Azure, magazyn usługi Recovery Services i czy nie jest co najmniej jeden element kopii zapasowej skonfigurowane dla magazynu.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]


## <a name="open-a-recovery-services-vault"></a>Otwórz magazyn usługi Recovery Services

Monitorowanie alertów lub przeglądać dane zarządzania dotyczące magazynu usługi Recovery Services, należy otworzyć Magazyn.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) przy użyciu subskrypcji platformy Azure.

2. W portalu, kliknij przycisk **wszystkich usług**.

   ![Otwieranie listy magazynów usługi Recovery Services — krok 1](./media/backup-azure-manage-windows-server/open-rs-vault-list.png)

3. W **wszystkich usług** okno dialogowe, typ **usługi Recovery Services**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Gdy **Magazyny usługi Recovery Services** opcja jest wyświetlana, kliknij go, aby otworzyć listę magazynów usługi Recovery Services w ramach subskrypcji.

    ![Tworzenie magazynu usługi Recovery Services — krok 1](./media/backup-azure-manage-windows-server/list-of-rs-vaults.png) <br/>

4. Z listy magazynów, kliknij pozycję Magazyn, aby otworzyć jego **Przegląd** pulpitu nawigacyjnego.

    ![pulpitu nawigacyjnego magazynu usług odzyskiwania](./media/backup-azure-manage-windows-server/rs-vault-blade.png) <br/>

    Pulpit nawigacyjny przeglądu używa Kafelki, aby zapewnić alerty i zadania tworzenia kopii zapasowej danych.

## <a name="monitor-backup-jobs-and-alerts"></a>Monitorowanie zadań tworzenia kopii zapasowych i alerty

Magazyn usługi Recovery Services **Przegląd** pulpit nawigacyjny zawiera Kafelki, aby uzyskać informacje dotyczące monitorowania i użycia. Kafelki w alerty ostrzegawcze i wyświetlania sekcji monitorowanie krytycznych i w trakcie wykonywania i zadania nie powiodło się. Kliknij dany alert lub zadanie, aby otworzyć menu kopia zapasowa alerty lub zadania tworzenia kopii zapasowej przefiltrowana pod kątem tego zadania lub alertu.

![Zadania tworzenia kopii zapasowej pulpitu nawigacyjnego](./media/backup-azure-manage-windows-server/monitor-dashboard-tiles-warning.png)

W sekcji monitorowanie przedstawiono wyniki opcji wstępnie zdefiniowanych **alerty kopii zapasowej** i **zadania tworzenia kopii zapasowej** zapytania. Kafelki monitorowanie zawierają aktualne informacje na temat:

* Alerty krytyczne i ostrzeżenia dla zadania tworzenia kopii zapasowej (w ciągu ostatnich 24 godzin)
* Zobacz stan wstępnego sprawdzania maszyn wirtualnych platformy Azure — Aby uzyskać pełne informacje na temat Stan wstępnego sprawdzania [blogu kopii zapasowej na stan wstępnego sprawdzania kopii zapasowej](https://azure.microsoft.com/blog/azure-vm-backup-pre-checks/).
* Zadania tworzenia kopii zapasowej w toku i zadań, które nie powiodło się (ostatnie 24 godziny).

Kafelki użycia zapewniają:

* Liczba elementów kopii zapasowej skonfigurowanych dla magazynu.
* Usługi Azure storage (oddzielone LRS i GRS) zużywanym przez Magazyn.

Aby otworzyć menu skojarzone, kliknij przycisk kafelków (z wyjątkiem magazyn kopii zapasowych). Na powyższej ilustracji kafelku alerty kopii zapasowej zawiera trzy alerty krytyczne. Klikając wiersz alertów krytycznych, w kafelku alerty kopii zapasowej, zostanie otwarte alerty kopii zapasowej przefiltrowana pod kątem alertów krytycznych.

![Menu alerty kopii zapasowej przefiltrowana pod kątem alertów krytycznych](./media/backup-azure-manage-windows-server/critical-backup-alerts.png)

Menu alerty kopii zapasowej na ilustracji powyżej są filtrowane według: Stan jest aktywny, ważność, jest krytyczny, a czas ostatnich 24 godzin.

## <a name="manage-backup-alerts"></a>Zarządzanie alertami wykonywania kopii zapasowej

Aby uzyskać dostęp do menu alerty kopii zapasowej, w menu magazynu usługi Recovery Services, kliknij przycisk **alerty kopii zapasowej**.

![Alerty kopii zapasowej](./media/backup-azure-manage-windows-server/backup-alerts-menu.png)

Raport o alertach kopii zapasowej wyświetla alerty dla magazynu.

![Alerty kopii zapasowej](./media/backup-azure-manage-windows-server/backup-alerts.png)

### <a name="alerts"></a>Alerty

Na liście alertów kopii zapasowej Wyświetla wybrane informacje dla filtrowanego alertów. W menu alerty kopii zapasowej można filtrować alerty krytycznych i ostrzeżeń.

| Poziom alertu | Zdarzenia, które generują alerty |
| ----------- | ----------- |
| Krytyczny | Pojawi się krytyczne alerty, gdy: Zadania tworzenia kopii zapasowych zakończą się niepowodzeniem, zadania odzyskiwania zakończą się niepowodzeniem, a po zatrzymaniu ochrony na serwerze, ale zachować dane.|
| Ostrzeżenie | Ostrzeżenia są wyświetlane alerty, gdy: Zadania tworzenia kopii zapasowej zakończenie z ostrzeżeniami, na przykład gdy mniej niż 100 nie kopię zapasową plików z powodu problemów z uszkodzeniem lub jeśli wartość jest większa niż 1 000 000 pliki są pomyślnie utworzyła kopię zapasową). |
| Informacyjne | obecnie nie alerty informacyjne są używane. |

### <a name="viewing-alert-details"></a>Wyświetlanie szczegółów alertów

Raport o alertach kopii zapasowej będzie śledził osiem szczegółowe informacje o każdym alercie. Użyj **wybierz kolumny** przycisk, aby edytować informacje w raporcie.

![Alerty kopii zapasowej](./media/backup-azure-manage-windows-server/backup-alerts.png)

Domyślnie wszystkie szczegółowe informacje, z wyjątkiem **godzina ostatniego wystąpienia**, są wyświetlane w raporcie.

* Alerty
* Element kopii zapasowej
* Chroniony serwer
* severity
* Duration
* Godzina utworzenia
* Stan
* Godzina ostatniego wystąpienia

### <a name="change-the-details-in-alerts-report"></a>Zmień dane w raporcie alertów

1. Aby zmienić informacji w raporcie w **alerty kopii zapasowej** menu, kliknij przycisk **wybierz kolumny**.

   ![Alerty kopii zapasowej](./media/backup-azure-manage-windows-server/alerts-menu-choose-columns.png)

   **Wybierz kolumny** zostanie otwarte menu.

2. W **wybierz kolumny** menu, wybierz szczegóły, o których mają być wyświetlane w raporcie.

    ![Wybierz opcję menu kolumn](./media/backup-azure-manage-windows-server/choose-columns-menu.png)

3. Kliknij przycisk **gotowe** Aby zapisać zmiany i zamknąć menu Wybierz kolumny.

   Jeśli zmiany, ale nie chcesz zachować zmiany, kliknij przycisk **resetowania** zwracają wybrane ostatni zapisany konfiguracji.

### <a name="change-the-filter-in-alerts-report"></a>Zmień filtr w raporcie alertów

Użyj **filtru** menu, aby zmienić ważność, stan, czas rozpoczęcia i czas zakończenia dla alertów.

> [!NOTE]
> Alerty kopii zapasowej do edycji filtru nie zmienia się krytyczne lub ostrzegawcze alerty w magazynie pulpit nawigacyjny przeglądu.
>  

1. Aby zmienić ustawienie filtru alerty kopii zapasowej, w menu alerty kopii zapasowej, kliknij przycisk **filtru**.

   ![Wybierz opcję menu filtrowania](./media/backup-azure-manage-windows-server/alerts-menu-choose-filter.png)

   Zostanie wyświetlone menu filtru.

   ![Wybierz opcję menu filtrowania](./media/backup-azure-manage-windows-server/filter-alert-menu.png)

2. Edytuj ważność, stan, czas rozpoczęcia lub godzina zakończenia, a następnie kliknij przycisk **gotowe** Aby zapisać zmiany.

## <a name="configuring-notifications-for-alerts"></a>Konfigurowanie powiadomień o alertach

Konfigurowanie powiadomień do generowania wiadomości e-mail, gdy występuje alert o ostrzeżenie lub krytyczny. Możesz wysyłać wiadomości e-mail dla alertów co godzinę lub gdy wystąpi dany alert.

   ![Filtrowanie alertów](./media/backup-azure-manage-windows-server/configure-notification.png)

Domyślnie powiadomienia E-mail są **na**. Kliknij przycisk **poza** Aby wyłączyć powiadomienia e-mail.

Na **powiadamiania** sterowania, wybierz **na alertach** Jeśli nie chcesz, aby grupowania lub nie ma wiele elementów, które mogą generować alerty. Każdy alert skutkuje jedno powiadomienie (ustawienie domyślne) i natychmiast zostanie wysłana wiadomość e-mail z rozwiązania.

Jeśli wybierzesz **podsumowanie godzinowe**, zostanie wysłana wiadomość e-mail do adresatów wyjaśniających nierozwiązane alerty wygenerowane w ciągu ostatniej godziny. Rozpoznawanie pocztą e-mail zostanie wysłane na koniec godziny.

Wybierz ważność alertu (krytyczny lub ostrzegawczy) używany do generowania wiadomości e-mail. Aktualnie nie istnieją żadne alerty dotyczące informacji.

## <a name="manage-backup-items"></a>Zarządzanie elementy kopii zapasowej

Magazyn usługi Recovery Services zawiera wiele typów danych kopii zapasowej. [Dowiedz się więcej](backup-overview.md#what-can-i-back-up) o czym można utworzyć kopię. Aby zarządzać różnych serwerów, komputerów, baz danych i obciążeń, kliknij **elementy kopii zapasowej** Kafelek, aby wyświetlić zawartość magazynu.

![Kafelek elementów kopii zapasowych](./media/backup-azure-manage-windows-server/backup-items.png)

Na liście elementów kopii zapasowej, uporządkowane według typu zarządzania kopiami zapasowymi, zostanie otwarty.

![Lista elementów kopii zapasowych](./media/backup-azure-manage-windows-server/list-backup-items.png)

Aby zapoznać się z określonego typu chronionego wystąpienia, kliknij element w kolumnie Typ zarządzania kopiami zapasowymi. Na przykład na powyższym obrazie, istnieją dwie maszyny wirtualne platformy Azure chronione w tym magazynie. Klikając **maszyny wirtualnej platformy Azure**, otwiera listę chronionych maszyn wirtualnych w tym magazynie.

![Lista Typ kopii zapasowej](./media/backup-azure-manage-windows-server/list-of-protected-virtual-machines.png)

Listę maszyn wirtualnych ma użyteczne dane: skojarzonej grupy zasobów, poprzednie [wstępnego sprawdzania kopii zapasowej](https://azure.microsoft.com/blog/azure-vm-backup-pre-checks/), stan ostatniej kopii zapasowej i daty punktu przywracania. Wielokropek w ostatniej kolumnie otwiera menu aby wyzwolić typowych zadań. Przydatne dane dostarczone w kolumnach, różni się dla poszczególnych typów kopii zapasowej.

![Lista Typ kopii zapasowej](./media/backup-azure-manage-windows-server/ellipsis-menu.png)

## <a name="manage-backup-jobs"></a>Zarządzanie zadaniami tworzenia kopii zapasowych

**Zadania tworzenia kopii zapasowej** Kafelek na pulpicie nawigacyjnym magazynu pokazuje liczbę zadań, które są w toku lub nie powiodło się w ciągu ostatnich 24 godzin. Kafelek zawiera programistyczne w menu zadania tworzenia kopii zapasowej.

![Elementy kopii zapasowej z ustawień](./media/backup-azure-manage-windows-server/backup-jobs-tile.png)

Aby wyświetlić dodatkowe szczegóły dotyczące zadania, kliknij **w toku** lub **niepowodzenie** aby otworzyć menu zadania tworzenia kopii zapasowej przefiltrowana pod kątem tego stanu.

### <a name="backup-jobs-menu"></a>Menu zadania tworzenia kopii zapasowej

**Zadania tworzenia kopii zapasowej** menu przedstawia informacje na temat elementu typu, działania, stan, czas rozpoczęcia i czas trwania.  

Aby otworzyć menu zadania tworzenia kopii zapasowej dla magazynu głównego menu, kliknij **zadania tworzenia kopii zapasowej**.

![Elementy kopii zapasowej z ustawień](./media/backup-azure-manage-windows-server/backup-jobs-menu-item.png)

Zostanie otwarta lista zadania tworzenia kopii zapasowej.

![Elementy kopii zapasowej z ustawień](./media/backup-azure-manage-windows-server/backup-jobs-list.png)

W menu zadania tworzenia kopii zapasowych zawiera stan dla wszystkich operacji na wszystkich typach kopii zapasowych, w ciągu ostatnich 24 godzin. Użyj **filtru** zmienić filtry. Filtry są wyjaśnione w poniższych sekcjach.

Aby zmienić filtry:

1. W menu zadania tworzenia kopii zapasowej magazynu, kliknij przycisk **filtru**.

   ![Elementy kopii zapasowej z ustawień](./media/backup-azure-manage-windows-server/vault-backup-job-menu-filter.png)

    Zostanie otwarte menu filtru.

   ![Elementy kopii zapasowej z ustawień](./media/backup-azure-manage-windows-server/filter-menu-backup-jobs.png)

2. Wybierz ustawienia filtru, a następnie kliknij przycisk **gotowe**. Odświeżanie listy filtrowane na podstawie nowych ustawień.

#### <a name="item-type"></a>Typ elementu

Typ elementu to typ zarządzania kopiami zapasowymi chronionego wystąpienia. Istnieją cztery typy; Przejrzyj następującą listę. Możesz wyświetlić wszystkie typy elementów lub jednym typem elementu. Nie można wybrać dwie lub trzy typy elementów. Dostępne typy elementów są:

* Wszystkie typy elementów
* Maszyna wirtualna platformy Azure
* Pliki i foldery
* Azure Storage
* Obciążenie platformy Azure

#### <a name="operation"></a>Operacja

Możesz wyświetlić jedną operację lub wszystkie operacje. Nie można wybrać dwie lub trzy operacje. Dostępne operacje są:

* Wszystkie operacje
* Zarejestruj subskrypcję
* Konfigurowanie kopii zapasowych
* Backup
* Przywracanie
* Wyłącz kopię zapasową
* Usuwanie danych kopii zapasowej

#### <a name="status"></a>Stan

Można wyświetlić stan wszystkich lub jednego. Nie można wybrać dwie lub trzy stany. Dostępne są następujące stany:

* Wszystkie stany
* Ukończone
* W toku
* Niepowodzenie
* Anulowane
* Została zakończona z ostrzeżeniami

#### <a name="start-time"></a>Godzina rozpoczęcia

Dzień i godzinę rozpoczęcia zapytania. Wartość domyślna to 24-godzinnego okresu.

#### <a name="end-time"></a>Godzina zakończenia

Datę i godzinę zakończenia kwerendy.

### <a name="export-jobs"></a>Eksportuj zadania

Użyj **Eksportuj zadania** Aby utworzyć arkusz kalkulacyjny zawierający wszystkie informacje w menu zadań. Arkusz kalkulacyjny zawiera jeden arkusz, który zawiera podsumowanie wszystkich zadań i pojedynczymi arkuszami dla każdego zadania.

Aby wyeksportować informacje zadania do arkusza kalkulacyjnego, kliknij przycisk **Eksportuj zadania**. Usługa tworzy arkusz kalkulacyjny, przy użyciu nazwy magazynu i daty, ale można zmienić nazwę.

## <a name="monitor-backup-usage"></a>Monitorowanie użycia kopii zapasowej

Magazyn kopii zapasowych kafelka na pulpicie nawigacyjnym będą wyświetlane magazynu używane na platformie Azure. Użycie magazynu znajduje się na:

* Użycie magazynu LRS skojarzonego z magazynem w chmurze
* Użycie magazynu GRS skojarzonego z magazynem w chmurze


## <a name="troubleshooting-monitoring-issues"></a>Rozwiązywanie problemów monitorowania

**Problem:** Zadania i/lub alerty z agentem usługi Azure Backup nie są wyświetlane w portalu.

**Kroki rozwiązywania problemów:** Proces, ```OBRecoveryServicesManagementAgent```, wysyła dane zadania i alerty dla usługi Azure Backup. Czasami ten proces może zostać wstrzymana lub zamknięcia systemu.

1. Aby sprawdzić, proces nie jest uruchomiony, otwórz **Menedżera zadań**i sprawdź ```OBRecoveryServicesManagementAgent``` jest uruchomiona.

2. Jeśli proces nie jest uruchomiony, otwórz **Panelu sterowania**, a następnie przejdź na liście usług. Uruchom lub uruchom ponownie **agenta zarządzania Microsoft Azure Recovery Services**.

    Aby uzyskać więcej informacji Przeglądaj dzienniki w lokalizacji:<br/>
   `<AzureBackup_agent_install_folder>\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider*` Na przykład:<br/>
   `C:\Program Files\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider0.errlog`

## <a name="next-steps"></a>Następne kroki
* [Przywracanie systemu Windows Server lub klienta Windows z platformy Azure](backup-azure-restore-windows-server.md)
* Aby dowiedzieć się więcej o usłudze Azure Backup, zobacz [Azure Backup — omówienie](backup-introduction-to-azure-backup.md)
* Odwiedź stronę [Forum usługi Azure Backup](https://go.microsoft.com/fwlink/p/?LinkId=290933)
