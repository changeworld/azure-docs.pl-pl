---
title: Zarządzanie magazynami i serwerami usług Azure Recovery Services
description: W tym artykule dowiesz się, jak monitorować magazyny usług odzyskiwania i zarządzać nimi za pomocą pulpitu nawigacyjnego przegląd usługi odzyskiwania.
ms.topic: conceptual
ms.date: 07/08/2019
ms.openlocfilehash: 1a4d23c157700f42422cfe7ca8fa1c49e2cf128a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131972"
---
# <a name="monitor-and-manage-recovery-services-vaults"></a>Monitorowanie magazynów usługi Recovery Services i zarządzanie nimi

W tym artykule wyjaśniono, jak używać pulpitu **nawigacyjnego Przegląd** usług odzyskiwania do monitorowania magazynów usług odzyskiwania i zarządzania nimi. Po otwarciu magazynu usług odzyskiwania z listy zostanie otwarty pulpit nawigacyjny **Przegląd** dla wybranego przechowalni. Pulpit nawigacyjny zawiera różne szczegóły dotyczące przechowalni. Istnieją *kafelki,* które pokazują: stan alertów krytycznych i ostrzegawczych, w toku i nieudanych zadań tworzenia kopii zapasowych i ilość lokalnie nadmiarowego magazynu (LRS) i magazynu geograficznie nadmiarowego (GRS) używane. Jeśli kopię zapasową maszyn wirtualnych platformy Azure w magazynie [kafelek **Pre-Check Status kopii zapasowej** wyświetla wszystkie elementy krytyczne lub ostrzegawcze](https://docs.microsoft.com/azure/backup/backup-azure-manage-windows-server#backup-pre-check-status). Poniższy obraz przedstawia pulpit nawigacyjny **przeglądu** **dla magazynu Contoso**. Kafelek **Zapasy kopii zapasowej** pokazuje, że w przechowalni jest zarejestrowanych dziewięć elementów.

![pulpit nawigacyjny magazynu usług odzyskiwania](./media/backup-azure-manage-windows-server/rs-vault-blade.png)

Wymagania wstępne dla tego artykułu to: subskrypcja platformy Azure, magazyn usług odzyskiwania i że dla magazynu skonfigurowano co najmniej jeden element kopii zapasowej.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="open-a-recovery-services-vault"></a>Otwieranie magazynu usług odzyskiwania

Aby monitorować alerty lub wyświetlać dane zarządzania przechowalnią usług odzyskiwania, otwórz magazyn.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) przy użyciu subskrypcji platformy Azure.

2. W portalu kliknij pozycję **Wszystkie usługi**.

   ![Otwórz listę magazynów usług odzyskiwania krok 1](./media/backup-azure-manage-windows-server/open-rs-vault-list.png)

3. W oknie dialogowym **Wszystkie usługi** wpisz polecenie **Usługi odzyskiwania**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Po **wyświetleniu** opcji Magazyny usług odzyskiwania kliknij ją, aby otworzyć listę magazynów usług odzyskiwania w ramach subskrypcji.

    ![Tworzenie magazynu usługi Recovery Services — krok 1](./media/backup-azure-manage-windows-server/list-of-rs-vaults.png) <br/>

4. Na liście przechowalni kliknij przechowalnię, aby otworzyć jej pulpit nawigacyjny **Przegląd.**

    ![pulpit nawigacyjny magazynu usług odzyskiwania](./media/backup-azure-manage-windows-server/rs-vault-blade.png) <br/>

    Pulpit nawigacyjny przeglądowy używa kafelków do dostarczania alertów i danych zadań kopii zapasowej.

## <a name="monitor-backup-jobs-and-alerts"></a>Monitorowanie zadań tworzenia kopii zapasowych i alertów

Pulpit nawigacyjny **przegląd** usługi odzyskiwania zawiera kafelki informacji o monitorowaniu i użytkowaniu. Kafelki w sekcji Monitorowanie wyświetlają alerty krytyczne i ostrzeżenia oraz Zadania w toku i Niepowodzenie. Kliknij określony alert lub zadanie, aby otworzyć menu Alerty kopii zapasowej lub Zadania tworzenia kopii zapasowych, filtrowane dla tego zadania lub alertu.

![Tworzenie kopii zapasowych zadań pulpitu nawigacyjnego](./media/backup-azure-manage-windows-server/monitor-dashboard-tiles-warning.png)

Sekcja Monitorowanie zawiera wyniki wstępnie zdefiniowanych **alertów kopii zapasowych** i zapytań **zadań tworzenia kopii zapasowych.** Płytki monitorowania zawierają aktualne informacje na temat:

* Alerty krytyczne i ostrzeżenia dotyczące zadań tworzenia kopii zapasowych (w ciągu ostatnich 24 godzin)
* Wstępnie sprawdź stan maszyn wirtualnych platformy Azure. Aby uzyskać pełne informacje na temat stanu wstępnego sprawdzania, zobacz [Stan wstępnego sprawdzania kopii zapasowej](#backup-pre-check-status).
* Zadania kopii zapasowej w toku i zadania, które nie powiodły się (w ciągu ostatnich 24 godzin).

Kafelki Użycia zapewniają:

* Liczba elementów kopii zapasowej skonfigurowanych dla przechowalni.
* Magazyn platformy Azure (oddzielone LRS i GRS) używane przez magazyn.

Kliknij kafelki (z wyjątkiem magazynu kopii zapasowych), aby otworzyć skojarzone menu. Na powyższym obrazie kafelek Alerty kopii zapasowej zawiera trzy alerty krytyczne. Kliknięcie wiersza Alerty krytyczne na kafelku Alerty kopii zapasowej powoduje otwarcie alertów kopii zapasowej filtrowanych dla alertów o znaczeniu krytycznym.

![Menu alertów kopii zapasowej filtrowane pod kątem alertów krytycznych](./media/backup-azure-manage-windows-server/critical-backup-alerts.png)

Menu Alerty kopii zapasowej na powyższej ilustracji jest filtrowane według: Stan jest aktywny, ważność jest krytyczna, a czas to poprzednie 24 godziny.

### <a name="backup-pre-check-status"></a>Stan sprawdzania wstępnego kopii zapasowej

Sprawdzanie wstępne kopii zapasowej sprawdź konfigurację maszyn wirtualnych pod kątem problemów, które mogą niekorzystnie wpłynąć na kopie zapasowe. Agregują te informacje, dzięki czemu można je wyświetlić bezpośrednio z pulpitu nawigacyjnego magazynu usług odzyskiwania i przedstawić zalecenia dotyczące środków naprawczych w celu zapewnienia pomyślnych kopii zapasowych spójnych plików lub spójnych z aplikacjami. Nie wymagają one infrastruktury i nie mają żadnych dodatkowych kosztów.  

Sprawdzanie wstępne kopii zapasowej są uruchamiane w ramach zaplanowanych operacji tworzenia kopii zapasowych dla maszyn wirtualnych platformy Azure. Kończą się one jednym z następujących państw:

* **Przekazany:** Ten stan wskazuje, że konfiguracja maszyny Wirtualnej powinna prowadzić do pomyślnego tworzenia kopii zapasowych i nie należy podejmować żadnych działań naprawczych.
* **Ostrzeżenie:** Ten stan wskazuje jeden lub więcej problemów w konfiguracji maszyny Wirtualnej, które *mogą* prowadzić do błędów kopii zapasowej. Zawiera *zalecane* kroki w celu zapewnienia pomyślnych kopii zapasowych. Na przykład nie mając zainstalowanego najnowszego agenta maszyny Wirtualnej może spowodować, że kopie zapasowe nie powiedzie się sporadycznie. Taka sytuacja zapewniłaby stan ostrzegawczy.
* **Krytyczne:** Ten stan wskazuje jeden lub więcej krytycznych problemów w konfiguracji maszyny Wirtualnej, które *doprowadzą* do awarii kopii zapasowej i zapewnia *wymagane* kroki w celu zapewnienia pomyślnego tworzenia kopii zapasowych. Na przykład problem z siecią spowodowany przez aktualizację reguł sieciowej sieciowej sieciowej maszyny Wirtualnej spowoduje niepowodzenie kopii zapasowych, ponieważ uniemożliwia maszynie wirtualnej komunikowanie się z usługą Azure Backup. Taka sytuacja zapewniłaby stan krytyczny.

Wykonaj poniższe czynności, aby rozpocząć rozwiązywanie problemów zgłaszanych przez kontroly wstępne kopii zapasowych maszyn wirtualnych w magazynie usług odzyskiwania.

* Wybierz kafelek **Stan wstępnego sprawdzania kopii zapasowej (Maszyny wirtualne platformy Azure)** na pulpicie nawigacyjnym magazynu usług odzyskiwania.
* Wybierz dowolną maszynę wirtualną ze stanem sprawdzania wstępnego kopii zapasowej **krytycznej** lub **ostrzeżenia**. Ta akcja spowoduje otwarcie okienka **szczegółów maszyny Wirtualnej.**
* Wybierz powiadomienie o okienku w górnej części okienka, aby wyświetlić opis problemu z konfiguracją i kroki korygujące.

## <a name="manage-backup-alerts"></a>Zarządzanie alertami kopii zapasowej

Aby uzyskać dostęp do menu Alerty kopii zapasowej, w menu przechowalni usług odzyskiwania kliknij polecenie **Alerty kopii zapasowych**.

![Alerty kopii zapasowej](./media/backup-azure-manage-windows-server/backup-alerts-menu.png)

Raport Alerty kopii zapasowej zawiera listę alertów dla magazynu.

![Alerty kopii zapasowej](./media/backup-azure-manage-windows-server/backup-alerts.png)

### <a name="alerts"></a>Alerty

Na liście Alerty kopii zapasowej są wyświetlane wybrane informacje dotyczące filtrowanych alertów. W menu Alerty kopii zapasowej można filtrować alerty krytyczne lub ostrzeżenia.

| Poziom alertu | Zdarzenia generujące alerty |
| ----------- | ----------- |
| Krytyczny | Alerty krytyczne są otrzymywane, gdy: zadania kopii zapasowej kończą się niepowodzeniem, zadania odzyskiwania kończą się niepowodzeniem i po zatrzymaniu ochrony na serwerze, ale zachowują dane.|
| Ostrzeżenie | Alerty ostrzegawcze są otrzymywane, gdy: Zadania kopii zapasowej wraz z ostrzeżeniami, na przykład gdy mniej niż 100 plików nie jest archiwizowane z powodu problemów z uszkodzeniem lub gdy pomyślnie wykonano kopię zapasową większej niż 1000 000 plików). |
| Informacyjne | obecnie nie są używane żadne alerty informacyjne. |

### <a name="viewing-alert-details"></a>Wyświetlanie szczegółów alertu

Raport Alerty kopii zapasowej śledzi osiem szczegółów dotyczących każdego alertu. Użyj przycisku **Wybierz kolumny,** aby edytować szczegóły w raporcie.

![Alerty kopii zapasowej](./media/backup-azure-manage-windows-server/backup-alerts.png)

Domyślnie wszystkie szczegóły, z wyjątkiem **ostatniego czasu wystąpienia,** są wyświetlane w raporcie.

* Alerty
* Element kopii zapasowej
* Serwer chroniony
* Ważność
* Czas trwania
* Czas tworzenia
* Stan
* Ostatni czas wystąpienia

### <a name="change-the-details-in-alerts-report"></a>Zmienianie szczegółów w raporcie alertów

1. Aby zmienić informacje o raporcie, w menu **Alerty kopii zapasowych** kliknij polecenie **Wybierz kolumny**.

   ![Alerty kopii zapasowej](./media/backup-azure-manage-windows-server/alerts-menu-choose-columns.png)

   Zostanie otwarte menu **Wybierz kolumny.**

2. W menu **Wybierz kolumny** wybierz szczegóły, które mają być wyświetlane w raporcie.

    ![Menu Wybieranie kolumn](./media/backup-azure-manage-windows-server/choose-columns-menu.png)

3. Kliknij **przycisk Gotowe,** aby zapisać zmiany i zamknąć menu Wybierz kolumny.

   Jeśli wprowadzać zmiany, ale nie chcesz zachować zmiany, kliknij przycisk **Reset,** aby przywrócić wybraną konfigurację do ostatniej zapisanej konfiguracji.

### <a name="change-the-filter-in-alerts-report"></a>Zmienianie filtru w raporcie alertów

Użyj menu **Filtr,** aby zmienić ważność, stan, czas rozpoczęcia i godzinę zakończenia alertów.

> [!NOTE]
> Edytowanie filtru Alerty kopii zapasowej nie zmienia alertów krytycznych ani ostrzegawczych na pulpicie nawigacyjnym Przegląd przechowalni.
>  

1. Aby zmienić filtr Alerty kopii zapasowej, w menu Alerty kopii zapasowej kliknij polecenie **Filtruj**.

   ![Wybierz menu filtru](./media/backup-azure-manage-windows-server/alerts-menu-choose-filter.png)

   Zostanie wyświetle menu Filtr.

   ![Wybierz menu filtru](./media/backup-azure-manage-windows-server/filter-alert-menu.png)

2. Edytuj ważność, stan, godzinę rozpoczęcia lub godzinę zakończenia i kliknij przycisk **Gotowe,** aby zapisać zmiany.

## <a name="configuring-notifications-for-alerts"></a>Konfigurowanie powiadomień dla alertów

Skonfiguruj powiadomienia do generowania wiadomości e-mail po wystąpieniu alertu ostrzeżeń lub krytycznych. Alerty e-mail można wysyłać co godzinę lub w przypadku wystąpienia określonego alertu.

   ![Alerty filtrowania](./media/backup-azure-manage-windows-server/configure-notification.png)

Domyślnie powiadomienia e-mail są **włączone**. Kliknij **przycisk Wyłącz,** aby zatrzymać powiadomienia e-mail.

W **formancie Powiadom** wybierz pozycję **Na alert,** jeśli nie chcesz grupowania lub nie ma wielu elementów, które mogą generować alerty. Każdy alert powoduje jedno powiadomienie (ustawienie domyślne), a wiadomość e-mail z rozdzielczością jest wysyłana natychmiast.

Jeśli wybierzesz **hourly digest,** do adresatów zostanie wysłana wiadomość e-mail wyjaśniająca nierozwiązane alerty wygenerowane w ciągu ostatniej godziny. Wiadomość e-mail z rozdzielczością jest wysyłana pod koniec godziny.

Wybierz ważność alertu (Krytyczne lub Ostrzeżenie) używane do generowania wiadomości e-mail. Obecnie nie ma żadnych alertów informacyjnych.

## <a name="manage-backup-items"></a>Zarządzanie elementami kopii zapasowej

Magazyn usług odzyskiwania przechowuje wiele typów danych kopii zapasowej. [Dowiedz się więcej](backup-overview.md#what-can-i-back-up) o tym, co możesz zrobić kopii zapasowej. Aby zarządzać różnymi serwerami, komputerami, bazami danych i obciążeniami, kliknij kafelek **Elementy kopii zapasowej,** aby wyświetlić zawartość przechowalni.

![Kafelek elementów kopii zapasowej](./media/backup-azure-manage-windows-server/backup-items.png)

Zostanie otwarta lista elementów kopii zapasowej uporządkowanych według typu zarządzania kopiami zapasowymi.

![lista elementów kopii zapasowej](./media/backup-azure-manage-windows-server/list-backup-items.png)

Aby eksplorować określony typ chronionego wystąpienia, kliknij element w kolumnie Typ zarządzania kopiami zapasowymi. Na przykład na powyższym obrazie istnieją dwie maszyny wirtualne platformy Azure chronione w tym magazynie. Kliknięcie przycisku **Azure Virtual Machine**powoduje otwarcie listy chronionych maszyn wirtualnych w tym magazynie.

![lista typu kopii zapasowej](./media/backup-azure-manage-windows-server/list-of-protected-virtual-machines.png)

Lista maszyn wirtualnych zawiera przydatne dane: skojarzoną grupę zasobów, [poprzednią wstępnie sprawdzaną kopię zapasową,](https://docs.microsoft.com/azure/backup/backup-azure-manage-windows-server#backup-pre-check-status)stan ostatniej kopii zapasowej i datę ostatniego punktu przywracania. Wielokropek w ostatniej kolumnie otwiera menu, aby wyzwolić typowe zadania. Przydatne dane podane w kolumnach są różne dla każdego typu kopii zapasowej.

![lista typu kopii zapasowej](./media/backup-azure-manage-windows-server/ellipsis-menu.png)

## <a name="manage-backup-jobs"></a>Zarządzanie zadaniami tworzenia kopii zapasowych

Kafelek **Zadania kopii zapasowej** na pulpicie nawigacyjnym magazynu pokazuje liczbę zadań, które są w toku lub nie powiodło się w ciągu ostatnich 24 godzin. Kafelek umożliwia wgląd w menu Zadania kopii zapasowej.

![Elementów kopii zapasowej z ustawień](./media/backup-azure-manage-windows-server/backup-jobs-tile.png)

Aby wyświetlić dodatkowe szczegóły dotyczące zadań, kliknij pozycję **W toku** lub **Nie można** otworzyć menu Zadania kopii zapasowej filtrowane dla tego stanu.

### <a name="backup-jobs-menu"></a>Menu zadań tworzenia kopii zapasowych

W menu **Zadania kopii zapasowej** są wyświetlane informacje o typie zapasu, operacji, stanie, godzinie rozpoczęcia i czasie trwania.  

Aby otworzyć menu Zadania kopii zapasowej, w menu głównym magazynu kliknij polecenie **Zadania kopii zapasowej**.

![Elementów kopii zapasowej z ustawień](./media/backup-azure-manage-windows-server/backup-jobs-menu-item.png)

Zostanie otwarta lista zadań tworzenia kopii zapasowej.

![Elementów kopii zapasowej z ustawień](./media/backup-azure-manage-windows-server/backup-jobs-list.png)

Menu Zadania kopii zapasowej pokazuje stan wszystkich operacji, we wszystkich typach kopii zapasowych, w ciągu ostatnich 24 godzin. Użyj **filtru,** aby zmienić filtry. Filtry są wyjaśnione w poniższych sekcjach.

Aby zmienić filtry:

1. W menu Zadania kopii zapasowej przechowalni kliknij polecenie **Filtruj**.

   ![Elementów kopii zapasowej z ustawień](./media/backup-azure-manage-windows-server/vault-backup-job-menu-filter.png)

    Zostanie otwarte menu Filtr.

   ![Elementów kopii zapasowej z ustawień](./media/backup-azure-manage-windows-server/filter-menu-backup-jobs.png)

2. Wybierz ustawienia filtru i kliknij przycisk **Gotowe**. Filtrowana lista jest odświeżana na podstawie nowych ustawień.

#### <a name="item-type"></a>Typ towaru

Typ elementu jest typem zarządzania kopiami zapasowymi chronionego wystąpienia. Istnieją cztery typy; zobacz poniższą listę. Można wyświetlić wszystkie typy elementów lub jeden typ elementu. Nie można wybrać dwóch lub trzech typów elementów. Dostępne typy elementów to:

* Wszystkie typy towarów
* Maszyna wirtualna platformy Azure
* Pliki i foldery
* Azure Storage
* Obciążenie platformy Azure

#### <a name="operation"></a>Operacja

Można wyświetlić jedną operację lub wszystkie operacje. Nie można wybrać dwie lub trzy operacje. Dostępne operacje to:

* Wszystkie operacje
* Zarejestruj
* Konfigurowanie kopii zapasowych
* Tworzenie kopii zapasowych
* Przywracanie
* Wyłączanie tworzenia kopii zapasowych
* Usuwanie danych kopii zapasowej

#### <a name="status"></a>Stan

Można wyświetlić wszystkie stany lub jeden. Nie można wybrać dwóch lub trzech stanów. Dostępne stany to:

* Cały status
* Zakończone
* W toku
* Niepowodzenie
* Anulowane
* Uzupełniony ostrzeżeniami

#### <a name="start-time"></a>Godzina rozpoczęcia

Dzień i godzina rozpoczęcia kwerendy. Wartość domyślna to okres 24-godzinny.

#### <a name="end-time"></a>Godzina zakończenia

Dzień i godzina zakończenia kwerendy.

### <a name="export-jobs"></a>Oferty pracy: Eksport

Użyj **zadania eksportu,** aby utworzyć arkusz kalkulacyjny zawierający wszystkie informacje o menu Zadania. Arkusz kalkulacyjny zawiera jeden arkusz zawierający podsumowanie wszystkich zadań i poszczególne arkusze dla każdego zadania.

Aby wyeksportować informacje o zadaniach do arkusza kalkulacyjnego, kliknij pozycję **Eksportuj zadania**. Usługa tworzy arkusz kalkulacyjny przy użyciu nazwy przechowalni i daty, ale można zmienić nazwę.

## <a name="monitor-backup-usage"></a>Monitorowanie użycia kopii zapasowej

Kafelek Magazynu kopii zapasowych na pulpicie nawigacyjnym pokazuje magazyn zużywany na platformie Azure. Użycie pamięci masowej jest przewidziane dla:

* Użycie pamięci masowej LRS w chmurze skojarzone z magazynem
* Użycie pamięci masowej WGS w chmurze skojarzone z magazynem

## <a name="troubleshooting-monitoring-issues"></a>Rozwiązywanie problemów z monitorowaniem

**Problem:** Zadania i/lub alerty z agenta usługi Azure Backup nie są wyświetlane w portalu.

**Kroki rozwiązywania problemów:** Proces, ```OBRecoveryServicesManagementAgent```wysyła dane zadania i alertów do usługi Azure Backup. Od czasu do czasu ten proces może utknąć lub zamknąć.

1. Aby sprawdzić, czy proces nie jest uruchomiony, ```OBRecoveryServicesManagementAgent``` otwórz Menedżera **zadań**i sprawdź jest uruchomiony.

2. Jeśli proces nie jest uruchomiony, otwórz **Panel sterowania**i przejrzyj listę usług. Uruchom lub uruchom ponownie **agenta zarządzania usługami odzyskiwania platformy Microsoft Azure**.

    Aby uzyskać więcej informacji, zapoznaj się z dziennikami na:<br/>
   `<AzureBackup_agent_install_folder>\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider*`Na przykład:<br/>
   `C:\Program Files\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider0.errlog`

## <a name="next-steps"></a>Następne kroki

* [Przywracanie systemu Windows Server lub klienta systemu Windows z platformy Azure](backup-azure-restore-windows-server.md)
* Aby dowiedzieć się więcej o usłudze Azure Backup, zobacz [Omówienie tworzenia kopii zapasowych platformy Azure](backup-introduction-to-azure-backup.md)
