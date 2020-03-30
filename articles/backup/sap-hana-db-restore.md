---
title: Przywracanie baz danych SAP HANA na maszynach wirtualnych platformy Azure
description: W tym artykule dowiedz się, jak przywrócić bazy danych SAP HANA, które są uruchomione na maszynach wirtualnych platformy Azure.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 999edba61177758ad9039e81e789efcef99ca1de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74287920"
---
# <a name="restore-sap-hana-databases-on-azure-vms"></a>Przywracanie baz danych SAP HANA na maszynach wirtualnych platformy Azure

W tym artykule opisano sposób przywracania baz danych SAP HANA, które są uruchomione na maszynie wirtualnej platformy Azure (VM), które usługa Azure Backup ma kopię zapasową w magazynie usług odzyskiwania kopii zapasowych platformy Azure. Przywraca może służyć do tworzenia kopii danych dla scenariuszy deweloper/test lub powrócić do poprzedniego stanu.

Aby uzyskać więcej informacji, jak zrobić zamiejscowy program SAP HANA baz danych, zobacz [Tworzenie kopii zapasowych baz danych SAP HANA na maszynach wirtualnych platformy Azure.](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)

## <a name="restore-to-a-point-in-time-or-to-a-recovery-point"></a>Przywracanie do punktu w czasie lub do punktu odzyskiwania

Usługa Azure Backup może przywrócić bazy danych SAP HANA, które są uruchomione na maszynach wirtualnych platformy Azure w następujący sposób:

* Przywróć do określonej daty lub godziny (do drugiej) przy użyciu kopii zapasowych dziennika. Usługa Azure Backup automatycznie określa odpowiednie pełne, różnicowe kopie zapasowe i łańcuch kopii zapasowych dziennika, które są wymagane do przywrócenia na podstawie wybranego czasu.

* Przywróć do określonej pełnej lub różnicowej kopii zapasowej, aby przywrócić do określonego punktu odzyskiwania.

## <a name="prerequisites"></a>Wymagania wstępne

Przed przywróceniem bazy danych należy zwrócić uwagę na następujące kwestie:

* Bazę danych można przywrócić tylko do wystąpienia SAP HANA, które znajduje się w tym samym regionie

* Wystąpienie docelowe musi być zarejestrowane w tym samym magazynie co źródło

* Usługa Azure Backup nie może zidentyfikować dwóch różnych wystąpień SAP HANA na tej samej maszynie wirtualnej. W związku z tym przywracanie danych z jednego wystąpienia do drugiego na tej samej maszynie wirtualnej nie jest możliwe

* Aby upewnić się, że docelowe wystąpienie SAP HANA jest gotowe do przywrócenia, sprawdź jego stan **gotowości do tworzenia kopii zapasowych:**

  * Otwórz magazyn, w którym zarejestrowane jest docelowe wystąpienie SAP HANA

  * Na pulpicie nawigacyjnym przechowalni w obszarze **Wprowadzenie**wybierz pozycję **Kopia zapasowa**

![Tworzenie kopii zapasowej na pulpicie nawigacyjnym przechowalni](media/sap-hana-db-restore/getting-started-backup.png)

* W **obszarze Kopia zapasowa**w obszarze **Co chcesz wykonać kopię zapasową?** wybierz **sap HANA w usłudze Azure VM**

![Wybieranie sap hana w usłudze Azure VM](media/sap-hana-db-restore/sap-hana-backup.png)

* W obszarze **Odkryj db w maszynach wirtualnych** kliknij **zobacz szczegóły**

![Wyświetl szczegóły](media/sap-hana-db-restore/view-details.png)

* Przejrzyj **gotowość do tworzenia kopii zapasowych** docelowej maszyny Wirtualnej

![Serwery chronione](media/sap-hana-db-restore/protected-servers.png)

* Aby dowiedzieć się więcej o typach przywracania, które obsługuje SAP HANA, zapoznaj się z notatką SAP HANA [1642148](https://launchpad.support.sap.com/#/notes/1642148)

## <a name="restore-a-database"></a>Przywracanie bazy danych

* Otwórz magazyn, w którym jest rejestrowana baza danych SAP HANA, która ma zostać przywrócona

* Na pulpicie nawigacyjnym **przechowalni**w obszarze Elementy chronione wybierz pozycję **Zapasy zapasowe**

![Elementy kopii zapasowej](media/sap-hana-db-restore/backup-items.png)

* W **elementach kopii zapasowej**w obszarze Typ **zarządzania kopiami zapasowymi** wybierz **SAP HANA w usłudze Azure VM**

![Typ zarządzania kopiami zapasowymi](media/sap-hana-db-restore/backup-management-type.png)

* Wybierz bazę danych, która ma zostać przywrócona

 ![Baza danych do przywrócenia](media/sap-hana-db-restore/database-to-restore.png)

* Przejrzyj menu bazy danych. Zawiera informacje o kopii zapasowej bazy danych, w tym:

  * Najstarsze i najnowsze punkty przywracania

  * Stan kopii zapasowej dziennika dla ostatnich 24 i 72 godzin dla bazy danych

![Menu Bazy danych](media/sap-hana-db-restore/database-menu.png)

* Wybierz **pozycję Przywróć bazę danych**

* W obszarze **Konfiguraj przywracania**określ, gdzie (lub jak) ma być przywracane dane:

  * **Lokalizacja alternatywna:** Przywróć bazę danych do lokalizacji alternatywnej i zachowaj oryginalną źródłową bazę danych.

  * **Zastąp DB:** Przywróć dane do tego samego wystąpienia SAP HANA jako oryginalnego źródła. Ta opcja zastępuje oryginalną bazę danych.

![Przywróć konfigurację](media/sap-hana-db-restore/restore-configuration.png)

### <a name="restore-to-alternate-location"></a>Przywracanie do lokalizacji alternatywnej

* W menu **Przywróć konfigurację** w obszarze **Gdzie przywrócić**wybierz pozycję **Lokalizacja alternatywna**.

![Przywracanie do lokalizacji alternatywnej](media/sap-hana-db-restore/restore-alternate-location.png)

* Wybierz nazwę hosta SAP HANA i nazwę wystąpienia, do którego chcesz przywrócić bazę danych.
* Sprawdź, czy docelowe wystąpienie SAP HANA jest gotowe do przywrócenia, zapewniając jego **gotowość do tworzenia kopii zapasowych.** Aby uzyskać więcej informacji, zapoznaj się z [sekcją wymagań wstępnych.](#prerequisites)
* W polu **Nazwa przywróconej bazy danych** wprowadź nazwę docelowej bazy danych.

> [!NOTE]
> Przywraca kontener pojedynczej bazy danych (SDC) musi wykonać te [kontrole](backup-azure-sap-hana-database-troubleshoot.md#single-container-database-sdc-restore).

* Jeśli ma to zastosowanie, wybierz opcję **Zastąp, jeśli baza danych o tej samej nazwie już istnieje w wybranym wystąpieniu HANA**.
* Kliknij przycisk **OK**.

![Konfiguracja przywracania — ekran końcowy](media/sap-hana-db-restore/restore-configuration-last.png)

* W **obszarze Wybierz punkt przywracania**wybierz pozycję **Dzienniki (punkt w czasie),** aby [przywrócić do określonego punktu w czasie](#restore-to-a-specific-point-in-time). Lub wybierz **opcję Pełny & Różnicowy,** aby [przywrócić do określonego punktu odzyskiwania](#restore-to-a-specific-recovery-point).

### <a name="restore-and-overwrite"></a>Przywracanie i zastępowanie

* W menu **Przywróć konfigurację** w obszarze **Gdzie przywrócić**wybierz pozycję **Zastąpełnij DB** > **OK**.

![Zastąp bazę danych](media/sap-hana-db-restore/overwrite-db.png)

* W **obszarze Wybierz punkt przywracania**wybierz pozycję **Dzienniki (punkt w czasie),** aby [przywrócić do określonego punktu w czasie](#restore-to-a-specific-point-in-time). Lub wybierz **opcję Pełny & Różnicowy,** aby [przywrócić do określonego punktu odzyskiwania](#restore-to-a-specific-recovery-point).

### <a name="restore-to-a-specific-point-in-time"></a>Przywracanie do określonego punktu w czasie

Jeśli jako typ przywracania **wybrano dzienniki (punkt w czasie),** wykonaj następujące czynności:

* Wybierz punkt odzyskiwania z wykresu dziennika i wybierz **przycisk OK,** aby wybrać punkt przywracania.

![Punkt przywracania](media/sap-hana-db-restore/restore-point.png)

* W menu **Przywracanie** wybierz pozycję **Przywróć**, aby uruchomić zadanie przywracania.

![Wybierz przycisk przywracania](media/sap-hana-db-restore/restore-restore.png)

* Śledź postęp przywracania w obszarze **Powiadomienia** lub śledź go, wybierając **pozycję Przywróć zadania** w menu bazy danych.

![Przywracanie wyzwolić pomyślnie](media/sap-hana-db-restore/restore-triggered.png)

### <a name="restore-to-a-specific-recovery-point"></a>Przywracanie do określonego punktu odzyskiwania

Jeśli jako typ przywracania wybrano opcję **Pełny & różnicowy,** wykonaj następujące czynności:

* Wybierz punkt odzyskiwania z listy i wybierz **przycisk OK,** aby wybrać punkt przywracania.

![Przywracanie określonego punktu odzyskiwania](media/sap-hana-db-restore/specific-recovery-point.png)

* W menu **Przywracanie** wybierz pozycję **Przywróć**, aby uruchomić zadanie przywracania.

![Rozpocznij zadanie przywracania](media/sap-hana-db-restore/restore-specific.png)

* Śledź postęp przywracania w obszarze **Powiadomienia** lub śledź go, wybierając **pozycję Przywróć zadania** w menu bazy danych.

![Przywracanie postępu](media/sap-hana-db-restore/restore-progress.png)

> [!NOTE]
> W wielu kontenerów bazy danych (MDC) przywraca po system DB jest przywracany do wystąpienia docelowego, trzeba ponownie uruchomić skrypt rejestracji wstępnej. Tylko wtedy kolejne przywraca bazy danych dzierżawy zakończy się pomyślnie. Aby dowiedzieć się więcej, zapoznaj się [z temat rozwiązywania problemów — przywracanie MDC](backup-azure-sap-hana-database-troubleshoot.md#multiple-container-database-mdc-restore).

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak](sap-hana-db-manage.md) zarządzać bazami danych SAP HANA, które są archiwizowane przy użyciu usługi Azure Backup
