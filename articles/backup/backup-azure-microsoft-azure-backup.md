---
title: Tworzenie kopii zapasowych za pomocą serwera kopii zapasowych platformy Azure
description: W tym artykule dowiesz się, jak przygotować środowisko do ochrony i tworzenia kopii zapasowych obciążeń przy użyciu programu Microsoft Azure Backup Server (MABS).
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: dd506668f9d75523ff7494bccb2979bf0785990d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273412"
---
# <a name="install-and-upgrade-azure-backup-server"></a>Instalowanie i uaktualnianie serwera kopii zapasowych platformy Azure

> [!div class="op_single_selector"]
>
> * [Serwer kopii zapasowych platformy Azure](backup-azure-microsoft-azure-backup.md)
> * [SCDPM (SCDPM)](backup-azure-dpm-introduction.md)
>
>

> Dotyczy: MABS v3. (MABS v2 nie jest już obsługiwany. Jeśli używasz wersji wcześniejszej niż MABS v3, uaktualnij do najnowszej wersji.)

W tym artykule wyjaśniono, jak przygotować środowisko do tworzenia kopii zapasowych obciążeń przy użyciu programu Microsoft Azure Backup Server (MABS). Za pomocą usługi Azure Backup Server można chronić obciążenia aplikacji, takie jak maszyny wirtualne funkcji Hyper-V, program Microsoft SQL Server, sharepoint server, program Microsoft Exchange i klienci systemu Windows z jednej konsoli.

> [!NOTE]
> Usługa Azure Backup Server może teraz chronić maszyny wirtualne VMware i zapewnia ulepszone funkcje zabezpieczeń. Zainstaluj produkt w sposób wyjaśniony w poniższych sekcjach i najnowszym agencie tworzenia kopii zapasowych platformy Azure. Aby dowiedzieć się więcej na temat tworzenia kopii zapasowych serwerów VMware za pomocą usługi Azure Backup Server, zobacz artykuł [Za pomocą usługi Azure Backup Server do tworzenia kopii zapasowych serwera VMware](backup-azure-backup-server-vmware.md). Aby dowiedzieć się więcej o funkcjach zabezpieczeń, zapoznaj się z [dokumentacją funkcji zabezpieczeń kopii zapasowych platformy Azure](backup-azure-security-feature.md).
>
>

Usługa MABS wdrożona na maszynie wirtualnej platformy Azure może wykonać kopię zapasową maszyn wirtualnych na platformie Azure, ale powinny one znajdować się w tej samej domenie, aby włączyć operację tworzenia kopii zapasowej. Proces tworzenia kopii zapasowej maszyny wirtualnej platformy Azure pozostaje taki sam jak tworzenie kopii zapasowych maszyn wirtualnych w środowisku lokalnym, jednak wdrażanie usługi MABS na platformie Azure ma pewne ograniczenia. Aby uzyskać więcej informacji na temat ograniczeń, zobacz [Program DPM jako maszyna wirtualna platformy Azure](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-1807#setup-prerequisites)

> [!NOTE]
> Platforma Azure ma dwa modele wdrażania do tworzenia i pracy z zasobami: [Menedżer zasobów i klasyczny](../azure-resource-manager/management/deployment-models.md). Ten artykuł zawiera informacje i procedury przywracania maszyn wirtualnych wdrożonych przy użyciu modelu Menedżera zasobów.
>
>

Usługa Azure Backup Server dziedziczy większość funkcji tworzenia kopii zapasowych obciążenia z Menedżera ochrony danych (DPM). Ten artykuł zawiera łącza do dokumentacji programu DPM, aby wyjaśnić niektóre funkcje udostępnione. Mimo że usługa Azure Backup Server udostępnia wiele z tych samych funkcji co program DPM, usługa Azure Backup Server nie wykonuje kopii zapasowej na taśmie ani nie integruje się z programem System Center.

## <a name="choose-an-installation-platform"></a>Wybierz platformę instalacyjną

Pierwszym krokiem w kierunku uruchomienia serwera kopii zapasowej platformy Azure jest skonfigurowanie systemu Windows Server. Serwer może znajdować się na platformie Azure lub lokalnie.

### <a name="using-a-server-in-azure"></a>Korzystanie z serwera na platformie Azure

Wybierając serwer do uruchamiania serwera azure backup server, zaleca się rozpocząć od obrazu galerii systemu Windows Server 2016 Datacenter lub Windows Server 2019 Datacenter. W tym artykule [Utwórz pierwszą maszynę wirtualną systemu Windows w portalu Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), zawiera samouczek, aby rozpocząć pracę z zalecaną maszyną wirtualną na platformie Azure, nawet jeśli nigdy wcześniej nie używano platformy Azure. Zalecane minimalne wymagania dla maszyny wirtualnej serwera (VM) powinny być: Standard_A4_v2 z czterema rdzeniami i 8 GB pamięci RAM.

Ochrona obciążeń za pomocą usługi Azure Backup Server ma wiele niuansów. Artykuł [Zainstaluj program DPM jako maszynę wirtualną platformy Azure,](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/jj852163(v=sc.12))pomaga wyjaśnić te niuanse. Przed wdrożeniem komputera przeczytaj ten artykuł całkowicie.

### <a name="using-an-on-premises-server"></a>Korzystanie z serwera lokalnego

Jeśli nie chcesz uruchamiać serwera podstawowego na platformie Azure, możesz uruchomić serwer na maszynie wirtualnej funkcji Hyper-V, maszynie wirtualnej VMware lub hoście fizycznym. Zalecane minimalne wymagania dotyczące sprzętu serwera to dwa rdzenie i 8 GB pamięci RAM. Obsługiwane systemy operacyjne są wymienione w poniższej tabeli:

| System operacyjny | Platforma | SKU |
|:--- | --- |:--- |
| Windows Server 2019 |64-bitowa |Standard, Datacenter, Essentials |
| Windows Server 2016 i najnowsze usługi SPs |64-bitowa |Standard, Datacenter, Essentials  |

Magazyn programu DPM można deduplikować za pomocą deduplikacji systemu Windows Server. Dowiedz się więcej o tym, jak [program DPM i deduplikacja](https://docs.microsoft.com/system-center/dpm/deduplicate-dpm-storage?view=sc-dpm-2019) współpracują ze sobą po wdrożeniu na maszynach wirtualnych z programem Hyper-V.

> [!NOTE]
> Usługa Azure Backup Server jest przeznaczona do uruchamiania na dedykowanym serwerze jednocelowym. Nie można zainstalować usługi Azure Backup Server na:
>
> * Komputer działa jako kontroler domeny
> * Komputer, na którym jest zainstalowana rola serwera aplikacji
> * Komputer, który jest serwerem zarządzania programu System Center Operations Manager
> * Komputer, na którym jest uruchomiony program Exchange Server
> * Komputer, który jest węzłem klastra
>
> Instalowanie serwera kopii zapasowej platformy Azure nie jest obsługiwane w systemie Windows Server Core ani Microsoft Hyper-V Server.

Zawsze dołącz do usługi Azure Backup Server do domeny. Jeśli planujesz przenieść serwer do innej domeny, najpierw zainstaluj usługę Azure Backup Server, a następnie dołącz do serwera w nowej domenie. Przenoszenie istniejącego komputera usługi Azure Backup Server do nowej domeny po wdrożeniu nie jest *obsługiwane*.

Niezależnie od tego, czy wysyłasz dane kopii zapasowej na platformę Azure, czy przechowujesz je lokalnie, serwer kopii zapasowej platformy Azure musi być zarejestrowany w magazynie usług odzyskiwania.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Konfigurowanie replikacji magazynu

Dla opcji replikacji magazynu można wybrać magazynowanie nadmiarowe geograficznie lub lokalnie. Domyślnie magazyny usług odzyskiwania używają magazynu geograficznie nadmiarowego. Jeśli ten magazyn jest magazynem podstawowym, pozostaw opcję magazynu ustawioną na magazyn geograficznie nadmiarowy. Wybierz magazyn lokalnie nadmiarowy, jeśli chcesz skorzystać z tańszej, ale mniej trwałej opcji. Dowiedz się więcej o opcjach magazynu [geograficznie nadmiarowego](../storage/common/storage-redundancy-grs.md) i [lokalnie nadmiarowego](../storage/common/storage-redundancy-lrs.md) w [omówieniu replikacji usługi Azure Storage](../storage/common/storage-redundancy.md).

Aby edytować ustawienia replikacji magazynu:

1. W bloku **Magazyny usług Recovery Services** kliknij nowy magazyn. W sekcji **Ustawienia** kliknij pozycję **Właściwości**.
2. W **obszarze Właściwości**w obszarze **Konfiguracja kopii zapasowej**kliknij pozycję **Aktualizuj**.

3. Wybierz typ replikacji magazynu i kliknij przycisk **Zapisz**.

     ![Ustawianie konfiguracji przechowywania dla nowego magazynu](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

## <a name="software-package"></a>Pakiet oprogramowania

### <a name="downloading-the-software-package"></a>Pobieranie pakietu oprogramowania

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).
2. Jeśli masz już otwarty magazyn usług odzyskiwania, przejdź do kroku 3. Jeśli magazyn usług odzyskiwania nie jest otwarty, ale znajduje się w witrynie Azure portal, w menu głównym kliknij polecenie **Przeglądaj**.

   * Na liście zasobów wpisz **Usługi odzyskiwania**.
   * Po rozpoczęciu pisania zawartość listy będzie filtrowana w oparciu o wpisywane dane. Po wyświetleniu pozycji **Magazyny Usług odzyskiwania** kliknij ją.

     ![Tworzenie magazynu usługi Recovery Services — krok 1](./media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

     Zostanie wyświetlona lista magazynów Usług odzyskiwania.
   * Wybierz magazyn z listy magazynów Usług odzyskiwania.

     Zostanie otwarty pulpit nawigacyjny wybranego magazynu.

     ![Otwarcie bloku magazynu](./media/backup-azure-microsoft-azure-backup/vault-dashboard.png)
3. Blok **Ustawienia** otwiera się domyślnie. Jeśli jest zamknięta, kliknij **ustawienia,** aby otworzyć blok ustawień.

    ![Otwarcie bloku magazynu](./media/backup-azure-microsoft-azure-backup/vault-setting.png)
4. Kliknij **pozycję Kopia zapasowa,** aby otworzyć kreatora Wprowadzenie.

    ![Rozpoczęcie tworzenia kopii zapasowej](./media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

    W otwieranym bloku **Wprowadzenie do tworzenia kopii zapasowych** cele tworzenia kopii **zapasowych** zostaną wybrane automatycznie.

    ![Kopia zapasowa-cele-domyślnie otwarty](./media/backup-azure-microsoft-azure-backup/getting-started.png)

5. W **bloku Cel kopii zapasowej** z menu **Gdzie jest uruchomione obciążenie,** wybierz opcję **Lokalnie**.

    ![lokalnie i obciążeń jako cele](./media/backup-azure-microsoft-azure-backup/backup-goals-azure-backup-server.png)

    Z menu **rozwijanego Co chcesz wykonać kopię zapasową?** wybierz obciążenia, które chcesz chronić za pomocą serwera kopii zapasowych platformy Azure, a następnie kliknij przycisk **OK**.

    Kreator **wprowadzenie do tworzenia kopii zapasowych** przełącza opcję Przygotuj **infrastrukturę,** aby wykonać kopię zapasową obciążeń na platformie Azure.

   > [!NOTE]
   > Jeśli chcesz wykonać tylko kopię zapasową plików i folderów, zalecamy użycie agenta usługi Azure Backup i wskazówki w artykule [Pierwsze spojrzenie: tworzenie kopii zapasowych plików i folderów](backup-try-azure-backup-in-10-mins.md). Jeśli zamierzasz chronić więcej niż pliki i foldery lub planujesz rozszerzyć potrzeby ochrony w przyszłości, wybierz te obciążenia.
   >
   >

    ![Zmiana kreatora wprowadzenie](./media/backup-azure-microsoft-azure-backup/getting-started-prep-infra.png)

6. W otwieranym bloku **Przygotowywanie infrastruktury** kliknij łącza **Pobierz** dla programu Instalowanie serwera kopii zapasowych platformy Azure i poświadczenia magazynu pobierania. Poświadczenia magazynu są używane podczas rejestracji usługi Azure Backup Server w magazynie usług odzyskiwania. Linki można pobrać do Centrum pobierania, gdzie można pobrać pakiet oprogramowania.

    ![Przygotowywanie infrastruktury dla serwera kopii zapasowych platformy Azure](./media/backup-azure-microsoft-azure-backup/azure-backup-server-prep-infra.png)

7. Zaznacz wszystkie pliki i kliknij przycisk **Dalej**. Pobierz wszystkie pliki pochodzące ze strony pobierania kopii zapasowej platformy Microsoft Azure i umieść wszystkie pliki w tym samym folderze.

    ![Centrum pobierania 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    Ponieważ rozmiar pobierania wszystkich plików razem jest > 3G, na 10 Mbps link do pobrania może potrwać do 60 minut, aby zakończyć pobieranie.

### <a name="extracting-the-software-package"></a>Wyodrębnianie pakietu oprogramowania

Po pobraniu wszystkich plików kliknij przycisk **MicrosoftAzureBackupInstaller.exe**. Spowoduje to uruchomienie **Kreatora instalacji kopii zapasowej platformy Microsoft Azure** w celu wyodrębnienia plików instalacyjnych do określonej przez Ciebie lokalizacji. Kontynuuj za pomocą kreatora i kliknij przycisk **Wyodrębnij,** aby rozpocząć proces ekstrakcji.

> [!WARNING]
> Do wyodrębnienia plików instalacyjnych wymagane jest co najmniej 4 GB wolnego miejsca.
>
>

![Kreator instalacji kopii zapasowej platformy Microsoft Azure](./media/backup-azure-microsoft-azure-backup/extract/03.png)

Po zakończeniu procesu wyodrębniania zaznacz to pole wyboru, aby uruchomić świeżo wyodrębniony *program setup.exe,* aby rozpocząć instalowanie programu Microsoft Azure Backup Server i kliknij przycisk **Zakończ.**

### <a name="installing-the-software-package"></a>Instalowanie pakietu oprogramowania

1. Kliknij pozycję **Kopia zapasowa platformy Microsoft Azure,** aby uruchomić kreatora konfiguracji.

    ![Kreator instalacji kopii zapasowej platformy Microsoft Azure](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)
2. Na ekranie powitalnym kliknij przycisk **Dalej.** Spowoduje to przejście do sekcji *Sprawdzanie pod warunkiem wstępnym.* Na tym ekranie kliknij przycisk **Sprawdź,** aby ustalić, czy wymagania wstępne dotyczące sprzętu i oprogramowania dla usługi Azure Backup Server zostały spełnione. Jeśli wszystkie wymagania wstępne zostaną spełnione pomyślnie, zostanie wyświetlony komunikat informujący, że urządzenie spełnia wymagania. Kliknij przycisk **Dalej.**

    ![Serwer kopii zapasowych platformy Azure — sprawdzanie powitalnym i wymagań wstępnych](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)
3. Pakiet instalacyjny serwera kopii zapasowych platformy Azure jest dostarczany w pakiecie z odpowiednimi plikami binarnymi programu SQL Server. Podczas uruchamiania nowej instalacji serwera kopii zapasowych platformy Azure wybierz opcję **Zainstaluj nowe wystąpienie programu SQL Server za pomocą tej Instalatora** i kliknij przycisk **Sprawdź i zainstaluj.** Po pomyślnym zainstalowaniu wymagań wstępnych kliknij przycisk **Dalej**.

    >[!NOTE]
    >Jeśli chcesz użyć własnego serwera SQL, obsługiwane wersje programu SQL Server to SQL Server 2014 z dodatkiem SP1 lub nowszym, 2016 i 2017.  Wszystkie wersje programu SQL Server powinny być standardowe lub 64-bitowe dla przedsiębiorstw.
    >Usługa Azure Backup Server nie będzie działać ze zdalnym wystąpieniem programu SQL Server. Wystąpienie używane przez usługę Azure Backup Server musi być lokalne. Jeśli używasz istniejącego serwera SQL dla mabs, konfiguracja MABS obsługuje tylko użycie *nazwanych wystąpień* programu SQL server.

    ![Serwer kopii zapasowej platformy Azure — sprawdzanie języka SQL](./media/backup-azure-microsoft-azure-backup/sql/01.png)

    Jeśli wystąpi błąd z zaleceniem ponownego uruchomienia komputera, zrób to i kliknij przycisk **Sprawdź ponownie**. Jeśli występują jakiekolwiek problemy z konfiguracją SQL, należy ponownie skonfigurować sql zgodnie z wytycznymi SQL i ponów próbę zainstalowania/uaktualnienia programu MABS przy użyciu istniejącego wystąpienia języka SQL.

   **Konfiguracja ręczna**

   Korzystając z własnego wystąpienia języka SQL, upewnij się, że dodajesz wbudowane\Administratorzy do roli sysadmin do opanowania bazy danych.

    **Konfiguracja SSRS z programem SQL 2017**

    Podczas korzystania z własnego wystąpienia programu SQL 2017 należy ręcznie skonfigurować usługę SSRS. Po skonfigurowaniu SSRS upewnij się, że właściwość *IsInitialized* SSRS jest ustawiona na *True*. Gdy jest to ustawione na True, MABS zakłada, że SSRS jest już skonfigurowany i pominie konfigurację SSRS.

    Użyj następujących wartości dla konfiguracji SSRS:
    * Konto usługi: "Użyj wbudowanego konta" powinna być usługą sieciową
    * Adres URL usługi sieci Web: "Katalog wirtualny" powinien być ReportServer_\<>
    * Baza danych: DatabaseName powinien\<być ReportServer$ SQLInstanceName>
    * Adres URL portalu sieci Web: "Katalog wirtualny" powinien być Reports_\<> SQLInstanceName

    [Dowiedz się więcej](https://docs.microsoft.com/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode?view=sql-server-2017) o konfiguracji SSRS.

    > [!NOTE]
    > Licencjonowanie programu SQL Server używanego jako baza danych dla usługi MABS podlega [warunkom dotyczącym usług online](https://www.microsoft.com/licensing/product-licensing/products) firmy Microsoft (OST). Według OST, SQL Server w pakiecie z MABS może być używany tylko jako baza danych dla MABS.

4. Podaj lokalizację instalacji plików serwera kopii zapasowej platformy Microsoft Azure i kliknij przycisk **Dalej**.

    ![Prereq2 kopii zapasowej platformy Microsoft Azure](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    Lokalizacja zarysowania jest wymagana do utworzenia kopii zapasowej na platformie Azure. Upewnij się, że lokalizacja zarysowania jest co najmniej 5% danych planowanych do utworzenia kopii zapasowej w chmurze. Aby chronić dysk, po zakończeniu instalacji należy skonfigurować oddzielne dyski. Aby uzyskać więcej informacji dotyczących pul magazynowania, zobacz [Konfigurowanie pul magazynowania i magazynu dysków](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh758075(v=sc.12)).
5. Podaj silne hasło dla ograniczonych kont użytkowników lokalnych i kliknij przycisk **Dalej**.

    ![Prereq2 kopii zapasowej platformy Microsoft Azure](./media/backup-azure-microsoft-azure-backup/security-screen.png)
6. O tym, czy chcesz używać *usługi Microsoft Update* do sprawdzania dostępności aktualizacji, a następnie kliknij przycisk **Dalej**.

   > [!NOTE]
   > Zalecamy przekierowanie usługi Windows Update do witryny Microsoft Update, która oferuje zabezpieczenia i ważne aktualizacje dla systemu Windows i innych produktów, takich jak Microsoft Azure Backup Server.
   >
   >

    ![Prereq2 kopii zapasowej platformy Microsoft Azure](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)
7. Przejrzyj *podsumowanie ustawień* i kliknij pozycję **Zainstaluj**.

    ![Prereq2 kopii zapasowej platformy Microsoft Azure](./media/backup-azure-microsoft-azure-backup/summary-screen.png)
8. Instalacja odbywa się etapami. W pierwszej fazie agent usług odzyskiwania platformy Microsoft Azure jest zainstalowany na serwerze. Kreator sprawdza również dostępność połączenia z Internetem. Jeśli łączność z Internetem jest dostępna, można kontynuować instalację, jeśli nie, musisz podać szczegóły serwera proxy, aby połączyć się z Internetem.

    Następnym krokiem jest skonfigurowanie agenta usług odzyskiwania platformy Microsoft Azure. W ramach konfiguracji należy podać poświadczenia magazynu, aby zarejestrować komputer w magazynie usług odzyskiwania. Podasz również hasło do szyfrowania/odszyfrowywania danych wysyłanych między platformą Azure a lokalem. Możesz automatycznie wygenerować hasło lub podać własne minimalne 16-znakowe hasło. Kontynuuj z kreatorem, dopóki agent nie zostanie skonfigurowany.

    ![PreReq2 serwera kopii zapasowych platformy Azure](./media/backup-azure-microsoft-azure-backup/mars/04.png)
9. Po pomyślnym zakończeniu rejestracji serwera kopii zapasowej platformy Microsoft Azure ogólny kreator instalacji przechodzi do instalacji i konfiguracji składników programu SQL Server i usługi Azure Backup Server. Po zakończeniu instalacji składnika programu SQL Server są instalowane składniki serwera kopii zapasowych platformy Azure.

    ![Azure Backup Server](./media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

Po zakończeniu etapu instalacji zostaną również utworzone ikony pulpitu produktu. Wystarczy dwukrotnie kliknąć ikonę, aby uruchomić produkt.

### <a name="add-backup-storage"></a>Dodawanie magazynu kopii zapasowych

Pierwsza kopia zapasowa jest przechowywana w magazynie dołączonym do komputera serwera kopii zapasowych platformy Azure. Aby uzyskać więcej informacji na temat dodawania dysków, zobacz [Konfigurowanie pul magazynowania i magazynu dysków](https://docs.microsoft.com/azure/backup/backup-mabs-add-storage).

> [!NOTE]
> Należy dodać magazyn kopii zapasowych, nawet jeśli planujesz wysyłać dane na platformę Azure. W bieżącej architekturze usługi Azure Backup Server magazyn usługi Azure Backup przechowuje *drugą* kopię danych, podczas gdy magazyn lokalny przechowuje pierwszą (i obowiązkową) kopię zapasową.
>
>

### <a name="install-and-update-the-data-protection-manager-protection-agent"></a>Instalowanie i aktualizowanie agenta ochrony programu Data Protection Manager

Firma MABS korzysta z agenta ochrony Menedżera ochrony danych w centrum systemu. [Poniżej przedstawiono kroki,](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-1807) aby zainstalować agenta ochrony na serwerach ochrony.

W poniższych sekcjach opisano sposób aktualizowania agentów ochrony dla komputerów klienckich.

1. W Konsoli administratora serwera kopii zapasowych wybierz pozycję**Agenci** **zarządzania** > .

2. W okienku wyświetlania wybierz komputery klienckie, dla których chcesz zaktualizować agenta ochrony.

   > [!NOTE]
   > Kolumna **Aktualizacje agenta** wskazuje, kiedy dla każdego chronionego komputera jest dostępna aktualizacja agenta ochrony. W okienku **Akcje** akcja **Aktualizuj** jest dostępna tylko wtedy, gdy wybrany jest komputer chroniony i dostępne są aktualizacje.
   >
   >

3. Aby zainstalować zaktualizowane środki ochrony na wybranych komputerach, w okienku **Akcje** wybierz pozycję **Aktualizuj**.

4. W przypadku komputera klienckiego, który nie jest podłączony do sieci, dopóki komputer nie jest podłączony do sieci, **kolumna Stan agenta** pokazuje stan **Oczekiwania na aktualizację**.

   Po podłączeniu komputera klienckiego do sieci kolumna **Aktualizacje agenta** dla komputera klienckiego pokazuje stan **aktualizacji**.

## <a name="move-mabs-to-a-new-server"></a>Przenoszenie usługi MABS na nowy serwer

Oto kroki, jeśli trzeba przenieść MABS do nowego serwera, zachowując magazyn. Można to zrobić tylko wtedy, gdy wszystkie dane są dostępne w modern backup storage.

  > [!IMPORTANT]
  >
  > * Nowa nazwa serwera musi mieć taką samą nazwę jak oryginalne wystąpienie serwera kopii zapasowej platformy Azure. Nie można zmienić nazwy nowego wystąpienia usługi Azure Backup Server, jeśli chcesz użyć poprzedniej puli magazynu i bazy danych MABS (DPMDB) do zachowania punktów odzyskiwania.
  > * Musisz mieć kopię zapasową bazy danych MABS (DPMDB). Musisz przywrócić bazę danych.

1. W okienku wyświetlania wybierz komputery klienckie, dla których chcesz zaktualizować agenta ochrony.
2. Zamknij oryginalny serwer kopii zapasowej platformy Azure lub zdejmij go z sieci.
3. Zresetuj konto komputera w usłudze Active Directory.
4. Zainstaluj serwer 2016 na nowym komputerze i nazwij go taką samą nazwą komputera jak oryginalny serwer kopii zapasowej platformy Azure.
5. Dołącz do domeny
6. Instalowanie serwera kopii zapasowej platformy Azure w wersji 3 lub nowszej (przenoszenie dysków puli magazynu MABS ze starego serwera i importowanie)
7. Przywróć dpmdb podjęte w kroku 1.
8. Podłącz magazyn z oryginalnego serwera kopii zapasowej do nowego serwera.
9. Z SQL Przywracanie DPMDB
10. Od wiersza polecenia administratora na nowym dysku CD serwera do folderu lokalizacji i pojemnika instalacji kopii zapasowej platformy Microsoft Azure

    Przykład ścieżki: C:\windows\system32>cd "c:\Program Files\Microsoft Azure Backup\DPM\DPM\bin\"

11. Do tworzenia kopii zapasowej platformy Azure, Uruchom program DPMSYNC -SYNC

    Jeśli dodano nowe dyski do puli magazynu programu DPM zamiast przenosić stare, uruchom program DPMSYNC -Reallocatereplica

## <a name="network-connectivity"></a>Łączność sieciowa

Usługa Azure Backup Server wymaga łączności z usługą Azure Backup, aby produkt działał pomyślnie. Aby sprawdzić, czy komputer ma łączność z ```Get-DPMCloudConnection``` platformą Azure, użyj polecenia cmdlet w konsoli programu PowerShell programu Azure Backup Server. Jeśli dane wyjściowe polecenia cmdlet jest true, a następnie łączność istnieje, w przeciwnym razie nie ma łączności.

W tym samym czasie subskrypcja platformy Azure musi być w dobrej kondycji. Aby dowiedzieć się o stanie subskrypcji i zarządzać nią, zaloguj się do [portalu subskrypcji](https://account.windowsazure.com/Subscriptions).

Gdy znasz stan łączności platformy Azure i subskrypcji platformy Azure, możesz użyć poniższej tabeli, aby dowiedzieć się, jaki ma wpływ na oferowaną funkcję tworzenia kopii zapasowej/przywracania.

| Stan łączności | Subskrypcja platformy Azure | Tworzenie kopii zapasowej w systemie Azure | Zapasowy zapas na dysku | Przywracanie z platformy Azure | Przywracanie z dysku |
| --- | --- | --- | --- | --- | --- |
| Połączone |Aktywne |Dozwolone |Dozwolone |Dozwolone |Dozwolone |
| Połączone |Wygasłe |Zatrzymano |Zatrzymano |Dozwolone |Dozwolone |
| Połączone |Anulowanie obsługi administracyjnej |Zatrzymano |Zatrzymano |Zatrzymane i punkty odzyskiwania platformy Azure usunięte |Zatrzymano |
| Utrata łączności > 15 dni |Aktywne |Zatrzymano |Zatrzymano |Dozwolone |Dozwolone |
| Utrata łączności > 15 dni |Wygasłe |Zatrzymano |Zatrzymano |Dozwolone |Dozwolone |
| Utrata łączności > 15 dni |Anulowanie obsługi administracyjnej |Zatrzymano |Zatrzymano |Zatrzymane i punkty odzyskiwania platformy Azure usunięte |Zatrzymano |

### <a name="recovering-from-loss-of-connectivity"></a>Odzyskiwanie po utracie łączności

Jeśli masz zaporę lub serwer proxy, który uniemożliwia dostęp do platformy Azure, musisz zezwolić na następujące adresy domen w profilu zapory/serwera proxy:

* `http://www.msftncsi.com/ncsi.txt`
* \*.Microsoft.com
* \*.WindowsAzure.com
* \*.microsoftonline.com
* \*.windows.net

Jeśli korzystasz z komunikacji równorzędnej firmy Microsoft w programie ExpressRoute, wybierz następujące usługi/regiony:

* Usługa Azure Active Directory (12076:5060)
* Region Platformy Microsoft Azure (zgodnie z lokalizacją magazynu usług odzyskiwania)
* Usługa Azure Storage (zgodnie z lokalizacją magazynu usług odzyskiwania)

Aby uzyskać więcej informacji, odwiedź stronę [Wymagania dotyczące routingu usługi ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-routing).

Po przywróceniu łączności z platformą Azure na komputerze z serwerem kopii zapasowych usługi Azure, operacje, które można wykonać, są określane przez stan subskrypcji platformy Azure. Powyższa tabela zawiera szczegółowe informacje o operacjach dozwolonych, gdy urządzenie jest "Połączone".

### <a name="handling-subscription-states"></a>Obsługa stanów subskrypcji

Istnieje możliwość podjęcia subskrypcji platformy Azure ze stanu *wygasłe* lub *deprowidowane* do stanu *Active.* Ma to jednak pewne konsekwencje dla zachowania produktu, gdy stan nie jest *aktywny:*

* Subskrypcja *nieprzejednana* traci funkcjonalność dla okresu, w który jest usuwany z obsługi administracyjnej. Po włączeniu *Active,* funkcjonalność produktu kopii zapasowej /przywracania jest ożywiona. Dane kopii zapasowej na dysku lokalnym również można pobrać, jeśli był przechowywany z wystarczająco dużym okresem przechowywania. Jednak dane kopii zapasowej na platformie Azure jest nieodwracalnie utracone, gdy subskrypcja wejdzie w stan *Deprowidowane.*
* *Wygasła subskrypcja* traci tylko funkcjonalność, dopóki nie została ponownie *aktywna.* Wszystkie kopie zapasowe zaplanowane na okres, w którego subskrypcja wygasła, nie będą *uruchamiane.*

## <a name="upgrade-mabs"></a>Uaktualnienie mabs

Aby uaktualnić program MABS, należy użyć następujących procedur.

### <a name="upgrade-from-mabs-v2-to-v3"></a>Uaktualnienie z MABS V2 do V3

> [!NOTE]
>
> MABS V2 nie jest warunkiem wstępnym instalacji MABS V3. Można jednak uaktualnić do MABS V3 tylko z MABS V2.

Aby uaktualnić program MABS, należy wykonać następujące czynności:

1. Aby uaktualnić system MABS V2 do mabs v3, należy w razie potrzeby uaktualnić system operacyjny do systemu Windows Server 2016 lub Windows Server 2019.

2. Uaktualnij swój serwer. Kroki są podobne do [instalacji](#install-and-upgrade-azure-backup-server). Jednak w przypadku ustawień SQL otrzymasz opcję uaktualnienia wystąpienia SQL do programu SQL 2017 lub użycia własnego wystąpienia programu SQL Server 2017.

   > [!NOTE]
   >
   > Nie należy kończyć pracy, gdy wystąpienie SQL jest uaktualniane, zamykanie spowoduje odinstalowanie wystąpienia raportowania SQL, a tym samym próba ponownego uaktualnienia usługi MABS zakończy się niepowodzeniem.

   > [!IMPORTANT]
   >
   >  W ramach uaktualnienia programu SQL 2017 możemy wykonać kopię zapasową kluczy szyfrowania SQL i odinstalować usługi raportowania. Po uaktualnieniu serwera SQL usługa raportowania(14.0.6827.4788) jest instalowana & przywracania kluczy szyfrowania.
   >
   > Podczas ręcznego konfigurowania programu SQL 2017 należy zapoznać się *z konfiguracją SSRS z sekcją SQL 2017* w obszarze Instrukcje instalacji.

3. Zaktualizuj agentów ochrony na chronionych serwerach.
4. Kopie zapasowe powinny być kontynuowane bez konieczności ponownego uruchamiania serwerów produkcyjnych.
5. Możesz rozpocząć ochronę swoich danych już teraz. W przypadku uaktualniania do nowoczesnego magazynu kopii zapasowych podczas ochrony można również wybrać woluminy, w których mają być przechowywane kopie zapasowe, i sprawdzić, czy w obszarze aprowizowana przestrzeń. [Dowiedz się więcej](backup-mabs-add-storage.md).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli serwer kopii zapasowej platformy Microsoft Azure ulegnie awarii z błędami podczas fazy instalacji (lub kopii zapasowej lub przywracania), zapoznaj się z tym [dokumentem kodów błędów,](https://support.microsoft.com/kb/3041338) aby uzyskać więcej informacji.
Można również zapoznać się [z często zadawanymi pytaniami dotyczącymi usługi Azure Backup](backup-azure-backup-faq.md)

## <a name="next-steps"></a>Następne kroki

Szczegółowe informacje można uzyskać tutaj na temat [przygotowania środowiska do programu DPM](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-2019). Zawiera również informacje o obsługiwanych konfiguracjach, na których można wdrożyć i używać usługi Azure Backup Server. Do wykonywania różnych operacji można użyć serii [polecenia cmdlet programu PowerShell.](https://docs.microsoft.com/powershell/module/dataprotectionmanager/?view=systemcenter-ps-2016)

Za pomocą tych artykułów można uzyskać głębsze zrozumienie ochrony obciążenia przy użyciu serwera kopii zapasowej platformy Microsoft Azure.

* [Kopia zapasowa programu SQL Server](backup-azure-backup-sql.md)
* [Kopia zapasowa serwera programu SharePoint](backup-azure-backup-sharepoint.md)
* [Alternatywna kopia zapasowa serwera](backup-azure-alternate-dpm-server.md)
