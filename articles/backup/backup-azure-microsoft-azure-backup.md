---
title: Użyj usługi Azure Backup Server do tworzenia kopii zapasowych obciążeń na platformie Azure
description: Użyj usługi Azure Backup Server do ochrony lub utworzyć kopię zapasową obciążeń do witryny Azure portal.
services: backup
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: kasinh
ms.openlocfilehash: 43793f1cc105bda7a50371f8fffd4ff787f6e300
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204434"
---
# <a name="install-and-upgrade-azure-backup-server"></a>Instalowanie i uaktualnianie usługi Azure Backup Server
> [!div class="op_single_selector"]
> * [Azure Backup Server](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
>
>

W tym artykule opisano sposób przygotowania środowiska do tworzenia kopii zapasowych obciążeń przy użyciu programu Microsoft Azure Backup Server (MABS). Za pomocą usługi Azure Backup Server można chronić obciążeń aplikacji, takich jak maszyny wirtualne funkcji Hyper-V, programu Microsoft SQL Server, SharePoint Server, Microsoft Exchange i klienci Windows z poziomu pojedynczej konsoli.

> [!NOTE]
> Usługa Azure Backup Server, mogą teraz chronić maszyny wirtualne VMware i udostępnia możliwości lepsze zabezpieczenia. Zainstaluj produkt zgodnie z opisem w sekcji poniżej oraz najnowszą wersję agenta usługi Azure Backup. Aby dowiedzieć się więcej o tworzeniu kopii zapasowych serwerów VMware za pomocą usługi Azure Backup Server, zapoznaj się z artykułem [użycia usługi Azure Backup Server do utworzenia kopii zapasowej serwera VMware](backup-azure-backup-server-vmware.md). Aby dowiedzieć się o funkcje zabezpieczeń, zapoznaj się [zabezpieczeń usługi Azure backup oferuje dokumentacji](backup-azure-security-feature.md).
>
>

Serwera usługi Mab wdrożone w Maszynie wirtualnej platformy Azure można tworzyć kopie zapasowe maszyn wirtualnych na platformie Azure, ale powinny one znajdować się w tej samej domenie, aby umożliwić operację tworzenia kopii zapasowej. Proces tworzenia kopii maszyn wirtualnych platformy Azure pozostaje taki sam jak wykonywanie kopii zapasowych maszyn wirtualnych w środowisku lokalnym, jednak wdrażania serwera usługi Mab na platformie Azure ma pewne ograniczenia. Aby uzyskać więcej informacji na temat ograniczeń zobacz [Program DPM jako maszynę wirtualną platformy Azure](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-1807#setup-prerequisites)

> [!NOTE]
> Platforma Azure oferuje dwa modele wdrażania związane z tworzeniem i pracą z zasobami: [model wdrażania przy użyciu usługi Resource Manager i model klasyczny](../azure-resource-manager/resource-manager-deployment-model.md). Ten artykuł zawiera informacje i procedury w celu przywrócenia maszyn wirtualnych wdrożonych przy użyciu modelu usługi Resource Manager.
>
>

Usługa Azure Backup Server dziedziczy, większość funkcji tworzenia kopii zapasowych obciążeń z programu Data Protection Manager (DPM). Ten artykuł zawiera łącza do dokumentacji programu DPM, aby wyjaśnić, niektóre funkcje udostępnione. Chociaż serwer usługi Azure Backup udostępnia wiele funkcji programu DPM, serwer usługi Azure Backup nie obsługuje tworzenie kopii zapasowej na taśmie, ani nie jest zintegrowana z programem System Center.

## <a name="choose-an-installation-platform"></a>Wybierz platformę instalacji
Pierwszy krok w kierunku usługi Azure Backup Server i przeprowadzanie jest skonfigurowania serwera z systemem Windows. Serwer może być na platformie Azure lub lokalnie.

### <a name="using-a-server-in-azure"></a>Za pomocą serwera na platformie Azure
Podczas wybierania serwera do uruchamiania usługi Azure Backup Server, zaleca się, że rozpoczynać image z galerie systemu Windows Server 2012 R2 Datacenter, Windows Server 2016 Datacenter lub Windows Server 2019 Datacenter. Artykuł [Utwórz pierwszą maszynę wirtualną Windows w witrynie Azure portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), zawiera samouczek dla klientów zaczynających się od zalecanych maszyny wirtualnej na platformie Azure, nawet jeśli nie znasz platformy Azure przed. Zalecane minimalne wymagania dotyczące serwera maszyny wirtualnej (VM) powinny być następujące: Maszyna wirtualna Standard_A4_v2 cztery rdzeni i 8 GB pamięci RAM.

Ochrona obciążenia za pomocą usługi Azure Backup Server ma wiele niuanse. Artykuł [Instalowanie programu DPM jako maszynę wirtualną platformy Azure](https://technet.microsoft.com/library/jj852163.aspx), wyjaśniających te różnice. Przed wdrożeniem na maszynie, przeczytaj ten artykuł całkowicie.

### <a name="using-an-on-premises-server"></a>Przy użyciu serwera lokalnego
Jeśli chcesz uruchomić serwer bazowy na platformie Azure, można uruchomić serwera na Maszynę wirtualną funkcji Hyper-V, maszyny Wirtualnej VMware lub hosta fizycznego. Zalecane minimalne wymagania dotyczące sprzętu serwera są dwa rdzenie i 8 GB pamięci RAM. W poniższej tabeli przedstawiono obsługiwane systemy operacyjne:

| System operacyjny | Platforma | SKU |
|:--- | --- |:--- |
| Windows Server 2019 |64-bitowa |Wersje Standard i Datacenter, Essentials (serwera usługi Mab w wersji 3 i nowszych) |
| Windows Server 2016 i najnowsze dodatki Service Pack |64-bitowa |Wersje Standard i Datacenter, Essentials (serwera usługi Mab w wersji 2 i nowsze) |
| Windows Server 2012 R2 i najnowsze dodatki Service Pack |64-bitowa |Standard, Datacenter, Foundation |
| Windows Storage Server 2012 R2 i najnowsze dodatki Service Pack |64-bitowa |Standard, Workgroup |
| Windows Storage Server 2012 i najnowsze dodatki Service Pack |64-bitowa |Standard, Workgroup |

Można deduplikacja magazynu programu DPM przy użyciu funkcji deduplikacji Server systemu Windows. Dowiedz się więcej o tym, jak [programu DPM i deduplikacja](https://technet.microsoft.com/library/dn891438.aspx) współpracują ze sobą, podczas wdrażania na maszynach wirtualnych funkcji Hyper-V.

> [!NOTE]
> Usługa Azure Backup Server jest przeznaczony do działania na dedykowanym, wyznaczonym do tego celu serwerze. Serwer usługi Azure Backup nie można zainstalować na:
> - Komputer działa jako kontroler domeny
> - Komputer, na którym jest zainstalowana rola serwera aplikacji
> - Komputer, który jest serwerem zarządzania programu System Center Operations Manager
> - Komputer, na którym jest uruchomiony program Exchange Server
> - Komputer, który jest węzłem klastra

Zawsze Dołącz do usługi Azure Backup Server do domeny. Jeśli zamierzasz przenieść serwer do innej domeny, najpierw zainstaluj serwer usługi Azure Backup, a następnie dołączyć serwer do nowej domeny. Przeniesienie istniejącej maszyny usługi Azure Backup Server do nowej domeny, po zakończeniu wdrożenia *nieobsługiwane*.

Czy wysyłania danych kopii zapasowej na platformie Azure lub przechowywać je lokalnie serwer usługi Azure Backup musi być zarejestrowany przy użyciu magazynu usługi Recovery Services.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Konfigurowanie replikacji magazynu
Dla opcji replikacji magazynu można wybrać magazynowanie nadmiarowe geograficznie lub lokalnie. Domyślnie magazynów usługi Recovery Services używają magazynu geograficznie nadmiarowego. Jeśli ten magazyn jest Twój podstawowy magazyn, pozostaw opcję magazynu ustawioną na magazyn geograficznie nadmiarowy. Wybierz magazyn lokalnie nadmiarowy, jeśli chcesz skorzystać z tańszej, ale mniej trwałej opcji. Więcej informacji o opcjach magazynu [geograficznie nadmiarowego](../storage/common/storage-redundancy-grs.md) i [lokalnie nadmiarowego](../storage/common/storage-redundancy-lrs.md) można znaleźć w temacie [Azure Storage replication overview](../storage/common/storage-redundancy.md) (Omówienie replikacji usługi Azure Storage).

Aby edytować ustawienia replikacji magazynu:

1. Wybierz swój magazyn, aby otworzyć menu Ustawienia i pulpitu nawigacyjnego magazynu. Jeśli **ustawienia** menu nie zostanie otwarty, kliknij przycisk **wszystkie ustawienia** na pulpicie nawigacyjnym magazynu.
2. Na **ustawienia** menu, kliknij przycisk **infrastruktura zapasowa** > **konfiguracji kopii zapasowej** otworzyć **konfiguracji kopii zapasowej**bloku. Na **konfiguracji kopii zapasowej** menu, wybierz opcję replikacji swojego magazynu.

    ![Lista magazynów kopii zapasowych](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

    Po wybraniu opcji magazynu dla swojego magazynu możesz skojarzyć z nim maszynę wirtualną. Aby rozpocząć kojarzenie, należy odnaleźć i zarejestrować maszyny wirtualne Azure.

## <a name="software-package"></a>Pakiet oprogramowania
### <a name="downloading-the-software-package"></a>Pobieranie pakietu oprogramowania
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Jeśli masz już Otwórz magazyn usługi Recovery Services, przejdź do kroku 3. Jeśli nie masz Otwórz magazyn usługi Recovery Services, ale znajdują się w witrynie Azure portal, w menu głównym kliknij **Przeglądaj**.

   * Na liście zasobów wpisz **Usługi odzyskiwania**.
   * Po rozpoczęciu pisania zawartość listy będzie filtrowana w oparciu o wpisywane dane. Po wyświetleniu pozycji **Magazyny Usług odzyskiwania** kliknij ją.

     ![Tworzenie magazynu usługi Recovery Services — krok 1](./media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

     Zostanie wyświetlona lista magazynów Usług odzyskiwania.
   * Wybierz magazyn z listy magazynów Usług odzyskiwania.

     Zostanie otwarty pulpit nawigacyjny wybranego magazynu.

     ![Otwarcie bloku magazynu](./media/backup-azure-microsoft-azure-backup/vault-dashboard.png)
3. **Ustawienia** zostanie otwarty blok domyślnie. Jeśli jest zamknięty, kliknij polecenie **ustawienia** aby otworzyć blok ustawień.

    ![Otwarcie bloku magazynu](./media/backup-azure-microsoft-azure-backup/vault-setting.png)
4. Kliknij przycisk **kopii zapasowej** aby otworzyć Kreatora wprowadzenie.

    ![Wprowadzenie do kopii zapasowej](./media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

    W **wprowadzenie do kopii zapasowej** otwartym bloku **celów kopii zapasowej** zostanie wybrany automatycznie.

    ![Backup-goals-default-opened](./media/backup-azure-microsoft-azure-backup/getting-started.png)

5. W **cel kopii zapasowej** bloku z **gdzie jest uruchomione Twoje obciążenie** menu, wybierz opcję **On-premises**.

    ![w lokalnych i obciążeń, gdy cele](./media/backup-azure-microsoft-azure-backup/backup-goals-azure-backup-server.png)

    Z **jakich elementów chcesz utworzyć kopię zapasową?** menu rozwijanego wybierz obciążeń, które chcesz chronić za pomocą serwera usługi Azure Backup, a następnie kliknij **OK**.

    **Wprowadzenie do kopii zapasowej** kreatora przełączników **przygotowanie infrastruktury** możliwość tworzenia kopii zapasowych obciążeń na platformie Azure.

   > [!NOTE]
   > Jeśli chcesz utworzyć kopię zapasową plików i folderów, zaleca się przy użyciu agenta usługi Kopia zapasowa Azure i postępując zgodnie ze wskazówkami w artykule [Pierwsze spojrzenie: tworzenie kopii zapasowej plików i folderów](backup-try-azure-backup-in-10-mins.md). Jeśli zamierzasz ochrony więcej niż pliki i foldery lub zamierzasz rozwiń na potrzeby ochrony w przyszłości, wybierz tych obciążeń.
   >
   >

    ![Getting Started kreatora zmiany](./media/backup-azure-microsoft-azure-backup/getting-started-prep-infra.png)

6. W **przygotowanie infrastruktury** bloku, który zostanie otwarty, kliknij polecenie **Pobierz** łącza, aby zainstalować serwer usługi Azure Backup i pobierania poświadczeń magazynu. Użyj poświadczeń magazynu, podczas rejestracji usługi Azure Backup Server do magazynu usługi recovery services. Łącza umożliwiają dostęp do Centrum pobierania, gdzie można pobrać pakietu oprogramowania.

    ![Przygotowanie infrastruktury usługi Azure Backup Server](./media/backup-azure-microsoft-azure-backup/azure-backup-server-prep-infra.png)

7. Zaznacz wszystkie pliki, a następnie kliknij przycisk **dalej**. Pobierz wszystkie pliki, które są dostępne ze strony pobierania kopia zapasowa Microsoft Azure i umieścić wszystkie pliki w tym samym folderze.

    ![1 Centrum pobierania](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    Ponieważ rozmiar pobierania wszystkich plików jednocześnie > 3G, na 10 MB/s, Pobierz łącze, które może potrwać do 60 minut na zakończenie pobierania.

### <a name="extracting-the-software-package"></a>Trwa wyodrębnianie pakietu oprogramowania
Po pobraniu wszystkich plików, kliknij przycisk **MicrosoftAzureBackupInstaller.exe**. Spowoduje to uruchomienie **Kreatora instalacji programu Microsoft Azure Backup** wyodrębnić pliki konfiguracji do lokalizacji określonej przez użytkownika. Kontynuuj pracę kreatora, a następnie kliknij polecenie **wyodrębnić** przycisk, aby rozpocząć proces wyodrębniania.

> [!WARNING]
> Co najmniej 4 GB wolnego miejsca na dysku jest wymagana do wyodrębnienia plików instalacyjnych.
>
>

![Kreator tworzenia kopii zapasowych Microsoft Azure](./media/backup-azure-microsoft-azure-backup/extract/03.png)

Po zakończeniu procesu wyodrębniania pole wyboru, aby uruchomić świeżo wyodrębnione *setup.exe* rozpocząć instalację serwera usługi Microsoft Azure Backup, a następnie kliknij **Zakończ** przycisku.

### <a name="installing-the-software-package"></a>Instalowanie pakietu oprogramowania
1. Kliknij przycisk **kopia zapasowa Microsoft Azure** można uruchomić Kreatora instalacji.

    ![Kreator tworzenia kopii zapasowych Microsoft Azure](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)
2. Na ekranie powitalnym kliknij **dalej** przycisku. Spowoduje to przejście do *funkcji sprawdzania wymagań wstępnych* sekcji. Na tym ekranie kliknij **Sprawdź** ustalenie, jeśli są spełnione wymagania wstępne sprzętu i oprogramowania dla usługi Azure Backup Server. Jeśli wszystkie wymagania wstępne są spełnione pomyślnie, zostanie wyświetlony komunikat wskazujący, czy maszyna spełnia wymagania. Kliknij pozycję **dalej** przycisku.

    ![Sprawdź, usługa Azure Backup Server — wprowadzenie i wymagania wstępne](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)
3. Serwer usługi Microsoft Azure Backup wymaga programu SQL Server Enterprise. Ponadto pakiet instalacyjny usługi Azure Backup Server ma powiązane odpowiednie pliki binarne programu SQL Server, wymagane, jeśli nie chcesz używać własnych SQL. Przy uruchamianiu nową instalację serwera usługi Azure Backup, należy wybrać opcję **zainstalować nowe wystąpienie programu SQL Server za pomocą tego Instalatora** i kliknij przycisk **Sprawdź i zainstaluj** przycisku. Po pomyślnym zainstalowaniu wymagań wstępnych kliknij **dalej**.

    ![Usługa Azure Backup Server — sprawdzenie programu SQL Server](./media/backup-azure-microsoft-azure-backup/sql/01.png)

    Jeśli wystąpi błąd z zaleceniem, aby ponownie uruchomić komputer, w tym celu i kliknij **Sprawdź ponownie**. W przypadku problemów dotyczących konfiguracji programu SQL Zmień konfigurację SQL zgodnie z wytycznymi SQL i ponów próbę, aby instalacja/Aktualizacja serwera usługi Mab przy użyciu istniejącego wystąpienia programu SQL Server.

   > [!NOTE]
   > Usługa Azure Backup Server nie będzie działać przy użyciu zdalnego wystąpienia programu SQL Server. Wystąpienie używane przez serwer usługi Azure Backup musi określać elementy lokalne. W przypadku korzystania z istniejącego serwera SQL dla serwera usługi Mab Instalatora serwera usługi Mab obsługuje wyłącznie korzystanie z *nazwanego wystąpienia* programu SQL server.

   **Konfiguracja ręczna**

   Gdy używasz własnego wystąpienia programu SQL Server, upewnij się, że dodasz BUILTIN\Administratorzy do roli administratora systemu do bazy danych master.

    **Konfiguracja usługi SSRS z programem SQL 2017**

    Korzystając z własnego wystąpienia programu SQL 2017, musisz ręcznie skonfigurować usługi SSRS. Po przeprowadzeniu konfiguracji usługi SSRS, upewnij się, że *IsInitialized* usług SSRS zostaje ustalona *True*. Gdy ta jest ustawiona na wartość True, serwera usługi Mab założono, że usługi SSRS jest już skonfigurowany i pozwoli na pominięcie konfiguracji usługi SSRS.

    Konfiguracji usługi SSRS, należy użyć następujących wartości:

        - Service Account: ‘Use built-in account’ should be Network Service
        - Web Service URL: ‘Virtual Directory’ should be ReportServer_<SQLInstanceName>
        - Database: DatabaseName should be ReportServer$<SQLInstanceName>
        - Web Portal URL: ‘Virtual Directory’ should be Reports_<SQLInstanceName>

    [Dowiedz się więcej](https://docs.microsoft.com/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode?view=sql-server-2017) o konfiguracji usługi SSRS.

4. Podaj lokalizację instalacji plików serwera kopia zapasowa Microsoft Azure, a następnie kliknij przycisk **dalej**.

    ![PreReq2 kopii zapasowych Microsoft Azure](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    Pliki tymczasowe lokalizacja jest wymagana do tworzenia kopii zapasowej na platformie Azure. Upewnij się, że lokalizacja tymczasowa jest co najmniej 5% danych, zaplanowane do wykonania kopii zapasowej w chmurze. Do ochrony dysku oddzielne dyski należy skonfigurować po ukończeniu instalacji. Aby uzyskać więcej informacji na temat pul magazynów, zobacz [skonfiguruj pule magazynów i Magazyn dyskowy](https://technet.microsoft.com/library/hh758075.aspx).
5. Podaj silne hasło dla kont użytkowników lokalnych z ograniczeniami, a następnie kliknij przycisk **dalej**.

    ![PreReq2 kopii zapasowych Microsoft Azure](./media/backup-azure-microsoft-azure-backup/security-screen.png)
6. Wybierz, czy chcesz użyć *Microsoft Update* sprawdzać dostępność aktualizacji, a następnie kliknij przycisk **dalej**.

   > [!NOTE]
   > Firma Microsoft zaleca posiadanie Windows Update, przekierowanie do usługi Microsoft Update oferuje bezpieczeństwo i ważne aktualizacje systemu Windows i innych produktów, takich jak serwer usługi Microsoft Azure Backup.
   >
   >

    ![PreReq2 kopii zapasowych Microsoft Azure](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)
7. Przegląd *Podsumowanie ustawień* i kliknij przycisk **zainstalować**.

    ![PreReq2 kopii zapasowych Microsoft Azure](./media/backup-azure-microsoft-azure-backup/summary-screen.png)
8. Instalacja odbywa się etapami. W pierwszej fazie agenta usługi Microsoft Azure Recovery Services jest zainstalowany na serwerze. Kreator sprawdza również połączenie z Internetem. Jeśli dostępny jest połączenie z Internetem możesz kontynuować instalację, jeśli nie, konieczne będzie podanie szczegółów serwera proxy, aby nawiązać połączenie z Internetem.

    Następnym krokiem jest skonfigurowanie agenta usługi Microsoft Azure Recovery Services. W ramach konfiguracji będzie musiał podać swoje poświadczenia magazynu, aby zarejestrować maszynę w magazynie usług odzyskiwania. Należy również podać hasło do szyfrowania/odszyfrowywania danych przesyłanych między platformą Azure i lokalnym. Możesz automatycznie wygenerować hasło lub Podaj własne minimalne hasło 16 znaków. Kontynuować pracę w kreatorze, dopóki agent został skonfigurowany.

    ![Usługa Azure Backup Server PreReq2](./media/backup-azure-microsoft-azure-backup/mars/04.png)
9. Po pomyślnym ukończeniu rejestracji serwera usługi Microsoft Azure Backup Kreatora konfiguracji ogólnej przechodzą do instalacji i konfiguracji programu SQL Server i składniki usługi Azure Backup Server. Po zakończeniu instalacji składników programu SQL Server są instalowane składniki usługi Azure Backup Server.

    ![Azure Backup Server](./media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

Po ukończeniu kroku instalacji produktu ikony pulpitu będzie utworzono również. Po prostu dwukrotnie kliknąć ikonę aby można było uruchomić produkt.

### <a name="add-backup-storage"></a>Dodaj magazyn kopii zapasowych
Pierwszy kopia zapasowa jest przechowywana w magazynie dołączonym do komputera serwera usługi Azure Backup. Aby uzyskać więcej informacji na temat dodawania dysków, zobacz [skonfiguruj pule magazynów i Magazyn dyskowy](https://docs.microsoft.com/azure/backup/backup-mabs-add-storage).

> [!NOTE]
> Musisz dodać magazyn kopii zapasowych, nawet jeśli zamierzasz wysłać danych na platformie Azure. W ramach bieżącego architektury usługi Azure Backup Server w magazynie usługi Kopia zapasowa Azure przechowuje *drugi* kopię danych podczas Magazyn lokalny zawiera pierwszą (i obowiązkowa) kopii zapasowej.
>
>

### <a name="install-and-update-the-data-protection-manager-protection-agent"></a>Instalowanie i aktualizowanie agenta ochrony programu Data Protection Manager

Agenta ochrony programu System Center Data Protection Manager korzysta z serwera usługi MAB. [Poniżej przedstawiono kroki](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-1807) do zainstalowania agenta ochrony na serwerach ochrony.

Poniżej opisano sposób aktualizacji agentów ochrony na komputerach klienckich.

1. W konsoli administratora serwera kopii zapasowych wybierz **zarządzania** > **agentów**.

2. W okienku wyświetlania wybierz komputery klienckie, dla których chcesz zaktualizować agenta ochrony.

   > [!NOTE]
   > **Aktualizacji agenta** kolumna wskazuje, kiedy aktualizacji agenta ochrony jest dostępna dla każdego chronionego komputera. W **akcje** okienku **aktualizacji** akcja jest dostępna tylko wtedy, gdy komputer chroniony jest zaznaczony i są dostępne aktualizacje.
   >
   >

3. Aby zainstalować zaktualizowanych agentów ochrony na wybranych komputerach w **akcje** okienku wybierz **aktualizacji**.

4. Na komputerze klienckim, który nie jest podłączony do sieci, dopóki komputer jest połączony z siecią **stan agenta** kolumna pokazuje stan **oczekująca aktualizacja**.

   Po komputer kliencki jest połączony z siecią **aktualizacji agenta** kolumny dla komputera klienckiego wskazuje stan **aktualizowanie**.

## <a name="move-mabs-to-a-new-server"></a>Przenoszenie serwera usługi Mab nowy serwer

Poniżej przedstawiono kroki, jeśli musisz przenieść serwera usługi Mab na nowy serwer przy zachowaniu magazynu. Można to zrobić tylko wtedy, gdy wszystkie dane na Modern Backup Storage.


  > [!IMPORTANT]
  > - Nowa nazwa serwera musi być taką samą nazwę jak oryginalne wystąpienie usługi Azure Backup Server. Nie można zmienić nazwy nowego wystąpienia usługi Azure Backup Server, jeśli chcesz użyć poprzedniej puli magazynu i bazy danych programu Data Protection Manager do przechowywania punktów odzyskiwania.
  > - Musi mieć kopię zapasową bazy danych programu Data Protection Manager. Należy przywrócić bazę danych.

1. W okienku wyświetlania wybierz komputery klienckie, dla których chcesz zaktualizować agenta ochrony.
2. Zamknij oryginalny Azure Utwórz kopię zapasową serwera lub zwiększyły podczas transmisji.
3. Resetuj konta komputera w usłudze active directory.
4. Zainstaluj Server 2016 na nowej maszyny i nadaj jej nazwę na taką samą nazwę komputera, jak oryginalny serwer usługi Azure Backup.
5. Przyłączenia do domeny
6. Instalowanie usługi Azure Backup server w wersji 2 lub nowszej (Przenoszenie programu DPM dyski puli magazynów ze starego serwera i import)
7. Przywróć bazę danych DPMDB wykonywane w kroku 1.
8. Dołącz magazyn z oryginalnego serwera kopii zapasowej do nowego serwera.
9. Z bazy danych SQL należy przywrócić bazy danych DPMDB
10. Z poziomu wiersza polecenia administratora na nowy serwer dysk cd, kopia zapasowa Microsoft Azure Zainstaluj lokalizacji i otworzyć folder bin

    Przykład ścieżki: C:\windows\system32>cd "c:\Program Files\Microsoft Azure Backup\DPM\DPM\bin\"

11. Do usługi Azure backup, wykonaj polecenie DPMSYNC-SYNC

    Po dodaniu nowych dysków do puli magazynów programu DPM, zamiast przenoszenia stare, a następnie uruchom polecenie DPMSYNC - Reallocatereplica

## <a name="network-connectivity"></a>Połączenie sieciowe
Usługa Azure Backup Server wymaga połączenia z usługą Azure Backup dla produktu działało poprawnie. Aby sprawdzić, czy komputer ma łączność z platformy Azure, należy użyć ```Get-DPMCloudConnection``` polecenia cmdlet w konsoli programu PowerShell serwera kopii zapasowych Azure. Jeśli dane wyjściowe polecenia cmdlet ma wartość TRUE, a następnie istnieje połączenie, przeciwnym razie nie ma łączności.

W tym samym czasie subskrypcji platformy Azure musi być w dobrej kondycji. Aby dowiedzieć się, stan subskrypcji i zarządzać nią, zaloguj się do [portalu subskrypcji](https://account.windowsazure.com/Subscriptions).

Po sprawdzeniu stanu łączność platformy Azure i subskrypcji platformy Azure, można użyć w poniższej tabeli, aby dowiedzieć się o wpływ na funkcje wykonywania kopii zapasowej i przywracania oferowane.

| Stan łączności | Subskrypcja platformy Azure | Tworzenie kopii zapasowej na platformie Azure | Tworzenie kopii zapasowej dysku | Przywracanie z platformy Azure | Przywracanie z dysku |
| --- | --- | --- | --- | --- | --- |
| Połączono |Aktywne |Dozwolone |Dozwolone |Dozwolone |Dozwolone |
| Połączono |Wygaśnięcie |Zatrzymano |Zatrzymano |Dozwolone |Dozwolone |
| Połączono |Anulowanie aprowizacji |Zatrzymano |Zatrzymano |Punkty odzyskiwania zatrzymane, a usługa Azure usunięte |Zatrzymano |
| Utraty łączności > 15 dni |Aktywne |Zatrzymano |Zatrzymano |Dozwolone |Dozwolone |
| Utraty łączności > 15 dni |Wygaśnięcie |Zatrzymano |Zatrzymano |Dozwolone |Dozwolone |
| Utraty łączności > 15 dni |Anulowanie aprowizacji |Zatrzymano |Zatrzymano |Punkty odzyskiwania zatrzymane, a usługa Azure usunięte |Zatrzymano |

### <a name="recovering-from-loss-of-connectivity"></a>Odzyskiwanie z utraty łączności
Jeśli zapora lub serwer proxy, który uniemożliwia dostęp do platformy Azure, musisz zezwolić na następujące adresy domain w profilu serwera proxy i zapory:

* `http://www.msftncsi.com/ncsi.txt`
* \*.Microsoft.com
* \*.WindowsAzure.com
* \*. microsoftonline.com
* \*. windows.net

Gdy łączność na platformie Azure została przywrócona do komputera serwera usługi Azure Backup, operacje, które mogą być wykonywane są określane przez stan subskrypcji platformy Azure. Powyższej tabeli zawiera informacje o operacjach dozwolona komputera to "Połączono".

### <a name="handling-subscription-states"></a>Obsługa stany subskrypcji
Jest to możliwe, należy subskrypcji platformy Azure z *wygasłe* lub *cofnięcia aprowizacji* do stanu *Active* stanu. Jednak to ma pewne skutki zachowania produktu podczas, gdy stan jest *Active*:

* A *cofnięcia aprowizacji* subskrypcji utraci funkcje w okresie, który jest anulowanie aprowizacji. Na włączanie *Active*, funkcje produktu/przywracania kopii zapasowej jest przywrócona. Dane kopii zapasowej na dysku lokalnym, również mogą być pobierane, jeśli została zachowana z okresem przechowywania wystarczająco duży. Jednak dane kopii zapasowej na platformie Azure jest nieodwracalnie w momencie wejścia subskrypcji *cofnięcia aprowizacji* stanu.
* *Wygasłe* subskrypcji tylko traci funkcji, dopóki nie została wprowadzona *Active* ponownie. Wszelkich kopii zapasowych zaplanowanych dla okresu subskrypcja została *wygasłe* nie będzie działać.

## <a name="upgrade-mabs"></a>Uaktualnianie serwera usługi Mab
Użyj poniższych procedur, aby uaktualnić serwera usługi MAB.

### <a name="upgrade-from-mabs-v2-to-v3"></a>Uaktualnienie z serwera usługi Mab V2 do V3

> [!NOTE]
>
> Serwera usługi Mab w wersji 2 nie jest wymagane w przypadku instalowania serwera usługi Mab w wersji 3. Możesz jednak uaktualnienie do serwera usługi Mab w wersji 3 tylko z serwera usługi Mab w wersji 2.

Aby uaktualnić serwera usługi Mab, wykonaj następujące kroki:

1. Uaktualnienie z serwera usługi Mab V2 do V3 serwera usługi Mab, należy uaktualnić system operacyjny do systemu Windows Server 2016 lub Windows Server 2019 w razie potrzeby.

2. Uaktualnij serwer. Kroki są podobne do [instalacji](#install-and-upgrade-azure-backup-server). Jednak dla ustawienia programu SQL, otrzymasz opcję Uaktualnij wystąpienie programu SQL do SQL 2017 lub użyć wystąpienia programu SQL server 2017.

   > [!NOTE]
   >
   > Nie zamykaj wystąpienia programu SQL jest uaktualniany, kończenie odinstaluje wystąpienie funkcji raportowania programu SQL i dlatego próby ponownego uaktualnienia serwera usługi Mab zakończy się niepowodzeniem.

   Ważne zagadnienia, które należy zwrócić uwagę:

   > [!IMPORTANT]
   >
   >  W ramach uaktualnienia programu SQL 2017 możemy utworzyć kopię zapasową klucze szyfrowania programu SQL i odinstalowania usług raportowania. Po uaktualnieniu serwera SQL service(14.0.6827.4788) raportowania jest zainstalowana i klucze szyfrowania zostaną przywrócone.
   >
   > Podczas ręcznego konfigurowania SQL 2017, zobacz *konfiguracji usługi SSRS z programem SQL 2017* sekcji zgodnie z instrukcjami instalacji.

3. Aktualizowanie agentów ochrony na serwerach chronionych.
4. Tworzenie kopii zapasowych powinno być kontynuowane bez konieczności ponownego uruchomienia serwerów produkcyjnych.
5. Możesz rozpocząć teraz ochrony danych. Jeśli uaktualniasz do Modern Backup Storage, przy jednoczesnej ochronie, również można woluminów, które chcesz przechowywać kopie zapasowe i sprawdź, czy w obszarze aprowizowane miejsce. [Dowiedz się więcej](backup-mabs-add-storage.md).

> [!NOTE]
>
> Jeśli uaktualniasz z serwera usługi Mab V1 na V2, upewnij się, że system operacyjny Windows Server 2016 lub Windows Server 2012 R2. Aby skorzystać z nowych funkcji, takich jak System Center 2016 danych ochrony Menedżera Modern Backup Storage, należy zainstalować kopii zapasowej serwera w wersji 2 w systemie Windows Server 2016. Zanim uaktualnisz do lub zainstalować kopii zapasowej serwera w wersji 2, przeczytaj o [wymagania wstępne instalacji](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-1807#setup-prerequisites) odpowiednie dla serwera usługi MAB.

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Jeśli serwer usługi Microsoft Azure Backup kończy się niepowodzeniem z błędami w fazie konfiguracji (lub kopii zapasowej lub przywracania), zapoznaj się z tym [dokumentu kody błędów](https://support.microsoft.com/kb/3041338) Aby uzyskać więcej informacji.
Może również dotyczyć [kopia zapasowa Azure związane z — często zadawane pytania](backup-azure-backup-faq.md)

## <a name="next-steps"></a>Kolejne kroki
Można uzyskać szczegółowe informacje [przygotowywanie środowiska dla programu DPM](https://technet.microsoft.com/library/hh758176.aspx) w witrynie Microsoft TechNet. Zawiera informacje o obsługiwanych konfiguracjach, na których serwer usługi Azure Backup można wdrożyć i używane. Można używać szeregu [polecenia cmdlet programu PowerShell](https://docs.microsoft.com/powershell/module/dataprotectionmanager/?view=systemcenter-ps-2016) do wykonywania różnych operacji.

Skorzystaj z tych artykułów, aby lepiej zrozumieć ochrony obciążenia w programie Microsoft Azure Backup server.

* [Kopia zapasowa programu SQL Server](backup-azure-backup-sql.md)
* [Kopia zapasowa programu SharePoint server](backup-azure-backup-sharepoint.md)
* [Kopia zapasowa serwera alternatywnego](backup-azure-alternate-dpm-server.md)
