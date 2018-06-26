---
title: Zainstaluj serwer kopii zapasowej systemu Azure w stosie Azure | Dokumentacja firmy Microsoft
description: Serwer kopii zapasowej Azure umożliwia ochronę lub tworzenie kopii zapasowej obciążeń w stosie usługi Azure.
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
keywords: Serwer kopii zapasowej systemu Azure; chronić obciążenia; Tworzenie kopii zapasowej obciążeń
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 6/5/2018
ms.author: markgal
ms.openlocfilehash: c9dd6a1818b0afeb5e577724568a8254a70c8228
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36753357"
---
# <a name="install-azure-backup-server-on-azure-stack"></a>Instalowanie składnika Azure Backup Server w usłudze Azure Stack

W tym artykule opisano sposób instalowania serwera kopii zapasowej Azure na stosie Azure. Serwer kopii zapasowej Azure umożliwia ochronę infrastruktury jako obciążenia usługa (IaaS), na przykład maszyn wirtualnych działających w stosie Azure. Zaletą używania serwera usługi Kopia zapasowa Azure w celu ochrony obciążeń jest wszystkich ochrony obciążenia można zarządzać z poziomu pojedynczej konsoli.

> [!NOTE]
> Aby dowiedzieć się więcej na temat funkcji zabezpieczeń, zapoznaj się [dokumentacji funkcji Kopia zapasowa Azure zabezpieczeń](backup-azure-security-feature.md).
>

## <a name="azure-backup-server-protection-matrix"></a>Macierz ochrony usługi Azure Backup Server
Serwer kopii zapasowej systemu Azure chroni następujące obciążenia maszyny wirtualnej Azure stosu.

| Chronionego źródła danych | Ochrona i odzyskiwanie |
| --------------------- | ----------------------- |
| Windows Server częściowej roczna kanału - Enterprise/Datacenter/Standard | Woluminy, pliki, foldery |
| Windows Server 2016 - Enterprise/Datacenter/Standard | Woluminy, pliki, foldery |
| Windows Server 2012 R2 - Enterprise/Datacenter/Standard | Woluminy, pliki, foldery |
| Windows Server 2012 — Entprise/Datacenter/Standard | Woluminy, pliki, foldery |
| Windows Server 2008 R2 - Enterprise/Datacenter/Standard | Woluminy, pliki, foldery |
| SQL Server 2016 | Database (Baza danych) |
| SQL Server 2014 | Database (Baza danych) |
| SQL Server 2012 z dodatkiem SP1 | Database (Baza danych) |
| Program SharePoint 2016 | Farma, baza danych, serwera sieci Web, serwer sieci web |
| SharePoint 2013 | Farma, baza danych, serwera sieci Web, serwer sieci web |
| SharePoint 2010 | Farma, baza danych, serwera sieci Web, serwer sieci web |

## <a name="prerequisites-for-the-azure-backup-server-environment"></a>Wymagania wstępne dotyczące środowiska Azure kopii zapasowej serwera

Podczas instalowania serwera usługi Kopia zapasowa Azure w środowisku Azure stosu, należy wziąć pod uwagę zalecenia przedstawione w tej sekcji. Instalator serwera usługi Kopia zapasowa Azure sprawdza środowiska pod kątem wymagania wstępne, że można skrócić czas od przygotowania, przed zainstalowaniem.

### <a name="determining-size-of-virtual-machine"></a>Ustalanie rozmiaru maszyny wirtualnej
Aby uruchomić serwera usługi Kopia zapasowa Azure na maszynie wirtualnej platformy Azure stosu, użyj rozmiaru A2 lub większym. Aby uzyskać pomoc przy wyborze rozmiaru maszyny wirtualnej, należy pobrać [kalkulatora rozmiaru maszyny Wirtualnej Azure stosu](https://www.microsoft.com/download/details.aspx?id=56832).

### <a name="virtual-networks-on-azure-stack-virtual-machines"></a>Sieci wirtualne na maszynach wirtualnych Azure stosu
Wszystkie maszyny wirtualne używane w stos Azure obciążenia muszą należeć do tej samej sieci wirtualnej platformy Azure i subskrypcję Azure.

### <a name="azure-backup-server-vm-performance"></a>Wydajność maszyny Wirtualnej serwera kopii zapasowej platformy Azure
Jeśli udostępniony innym maszynom wirtualnym, Magazyn konta, rozmiar i IOPS limity wpływu na wydajność maszyny Wirtualnej serwera kopii zapasowej Azure. Z tego powodu należy użyć oddzielnego konta magazynu dla maszyny wirtualnej Azure Utwórz kopię zapasową serwera. Agent usługi Kopia zapasowa Azure uruchomionych na serwerze kopii zapasowej Azure wymaga tymczasowego magazynu dla:
- własnego użytku (lokalizacja pamięci podręcznej),
- danych przywróconych z chmury (lokalny obszar przemieszczania)

### <a name="configuring-azure-backup-temporary-disk-storage"></a>Konfigurowanie magazynu dysku tymczasowym kopia zapasowa Azure
Każda maszyna wirtualna Azure stosu jest dostarczany z magazynu na dysku tymczasowym, który jest dostępny dla użytkownika jako wolumin `D:\`. Lokalny obszar przemieszczania wymagany dla usługi Kopia zapasowa Azure można skonfigurować w celu znajdują się w `D:\`, i lokalizację pamięci podręcznej można umieścić na `C:\`. W ten sposób magazynu nie trzeba używać przeciwną na dyskach danych dołączonych do maszyny wirtualnej serwera usługi Kopia zapasowa Azure.

### <a name="storing-backup-data-on-local-disk-and-in-azure"></a>Przechowywanie danych kopii zapasowej na dysku lokalnym oraz na platformie Azure
Serwer kopii zapasowej systemu Azure przechowuje dane kopii zapasowej na dyskach platformy Azure dołączonych do maszyny wirtualnej dla operacyjnych dotyczących odzyskiwania. Gdy dyski i miejsca do magazynowania są dołączone do maszyny wirtualnej, serwer kopii zapasowej Azure zarządza magazynu. Ilość miejsca w magazynie danych kopii zapasowych zależy od liczby i rozmiaru dysków dołączonych do każdego [maszyny wirtualnej Azure stosu](../azure-stack/user/azure-stack-storage-overview.md). Każdego rozmiaru maszyny Wirtualnej Azure stosu ma maksymalną liczbę dysków, które może zostać dołączony do maszyny wirtualnej. Na przykład A2 jest cztery dyski. A3 jest ośmiu dysków. A4 jest 16 dysków. Ponownie rozmiaru i liczby dysków określa puli całkowita magazynu kopii zapasowych.

> [!IMPORTANT]
> Należy **nie** zachowania danych operacyjnych dotyczących odzyskiwania (kopia zapasowa) na dyskach dołączone do serwera kopii zapasowej Azure przez więcej niż pięć dni.
>

Przechowywania danych kopii zapasowej na platformie Azure zmniejsza infrastruktury kopii zapasowych na stosie Azure. Jeśli dane są więcej niż pięć dni, powinny być przechowywane na platformie Azure.

Aby przechowywać dane kopii zapasowej na platformie Azure, należy utworzyć lub użyć magazynu usług odzyskiwania. Podczas przygotowywania do tworzenia kopii zapasowych obciążeń serwera usługi Kopia zapasowa Azure możesz [skonfigurować magazyn usług odzyskiwania](backup-azure-microsoft-azure-backup.md#create-a-recovery-services-vault). Po skonfigurowaniu każdym uruchomieniu zadania tworzenia kopii zapasowej, punkt odzyskiwania jest tworzony w magazynie. Każdy magazyn usług odzyskiwania zawiera punkty odzyskiwania do 9999. W zależności od liczby punktów odzyskiwania utworzonych i jak długo są przechowywane przez wiele lat można zachować dane kopii zapasowej. Na przykład można utworzyć miesięczne punktów odzyskiwania i zachować je do pięciu lat.
 
### <a name="scaling-deployment"></a>Skalowania wdrożenia
Jeśli chcesz skalowania wdrożenia, dostępne są następujące opcje:
  - Skalowanie w górę — zwiększenie rozmiaru maszyny wirtualnej serwera usługi Kopia zapasowa Azure z serii D serii i zwiększyć Magazyn lokalny [zgodnie z instrukcjami maszyny wirtualnej Azure stosu](../azure-stack/user/azure-stack-manage-vm-disks.md).
  - Korzystaj — wysyłanie starszych danych do platformy Azure i zachowywanie tylko najnowszych danych w magazynie dołączonym do serwera kopii zapasowej Azure.
  - Skalowanie w poziomie — dodawanie kolejnych serwerów kopia zapasowa Azure w celu włączenia ochrony obciążeń.

### <a name="net-framework"></a>.NET Framework

.NET framework 3.5 z dodatkiem SP1 lub nowszym należy zainstalować na maszynie wirtualnej.

### <a name="joining-a-domain"></a>Przyłączanie do domeny

Maszyna wirtualna serwera usługi Kopia zapasowa Azure musi należeć do domeny. Użytkownik z uprawnieniami administratora domeny, należy zainstalować serwer kopii zapasowej Azure na maszynie wirtualnej.

## <a name="using-an-iaas-vm-in-azure-stack"></a>Przy użyciu maszyn wirtualnych IaaS platformy Azure stosu

Podczas wybierania serwera dla serwera usługi Kopia zapasowa Azure, należy uruchomić z obrazem systemu Windows Server 2012 R2 Datacenter lub Windows Server 2016 Datacenter galerii. Artykuł [tworzenie pierwszej maszyny wirtualnej systemu Windows w portalu Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), zawiera samouczek na temat rozpoczynania pracy z zalecanych maszyny wirtualnej. Zalecane minimalne wymagania dotyczące serwera maszyny wirtualnej (VM) powinny być: A2 standardowe dwa rdzenie i 3.5 GB pamięci RAM.

Ochrona obciążenia za pomocą serwera usługi Kopia zapasowa Azure ma wiele wszystkie szczegóły. Artykuł [zainstalować program DPM jako maszyny wirtualnej platformy Azure](https://technet.microsoft.com/library/jj852163.aspx), pomaga opisano te wszystkie szczegóły. Przed wdrożeniem komputera należy całkowicie odczytane w tym artykule.

> [!NOTE]
> Serwer kopii zapasowej systemu Azure jest przeznaczony do uruchamiania na dedykowanym, jednozadaniowych maszyny wirtualnej. Nie można zainstalować serwer kopii zapasowej Azure na:
> - Komputer działa jako kontroler domeny
> - Komputer, na którym jest zainstalowana rola serwera aplikacji
> - Komputer, na którym jest uruchomiony program Exchange Server
> - Komputer, który jest węzłem klastra

Zawsze przyłączyć serwer kopii zapasowej Azure do domeny. Jeśli musisz przenieść serwer kopii zapasowej Azure do innej domeny, najpierw zainstaluj serwer kopii zapasowej Azure, a następnie dołączyć ją do nowej domeny. Po wdrożyć serwer kopii zapasowej Azure, nie można jej przenieść do nowej domeny.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Konfigurowanie replikacji magazynu

Dla opcji replikacji magazynu magazyn usług odzyskiwania umożliwia wybór między magazynu geograficznie nadmiarowego i lokalnie nadmiarowego magazynu. Domyślnie Magazyny usług odzyskiwania używają magazynu geograficznie nadmiarowego. Jeśli ten magazyn jest podstawowy magazyn, pozostaw tę opcję i magazynu do magazynu geograficznie nadmiarowego. Wybierz magazyn lokalnie nadmiarowy, jeśli chcesz, aby opcja tańsze, które są mniej trwałe. Więcej informacji o opcjach magazynu [geograficznie nadmiarowego](../storage/common/storage-redundancy-grs.md) i [lokalnie nadmiarowego](../storage/common/storage-redundancy-lrs.md) można znaleźć w temacie [Azure Storage replication overview](../storage/common/storage-redundancy.md) (Omówienie replikacji usługi Azure Storage).

Aby edytować ustawienia replikacji magazynu:

1. Wybierz magazyn do otwarcia pulpitu nawigacyjnego magazynu i menu Ustawienia. Jeśli **ustawienia** nie zostanie otwarte menu, kliknij przycisk **wszystkie ustawienia** na pulpicie nawigacyjnym magazynu.
2. Na **ustawienia** menu, kliknij przycisk **infrastruktura kopii zapasowej** > **konfiguracji kopii zapasowej** otworzyć **konfiguracji kopii zapasowej**menu. Na **konfiguracji kopii zapasowej** menu, wybierz opcję replikacji magazynu dla magazynu.

    ![Lista magazynów kopii zapasowych](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

## <a name="download-azure-backup-server-installer"></a>Pobierz Instalatora serwera usługi Kopia zapasowa Azure

Istnieją dwa sposoby pobrać Instalatora serwer kopii zapasowej Azure. Możesz pobrać Instalator serwer kopii zapasowej Azure z [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=55269). Możesz również pobrać Instalatora serwer kopii zapasowej Azure jako konfigurowania magazynu usług odzyskiwania. W poniższych krokach objaśniono do pobierania Instalatora z portalu Azure podczas konfigurowania magazynu usług odzyskiwania.

1. Z maszyny wirtualnej Azure stosu [Zaloguj się do subskrypcji platformy Azure w portalu Azure](https://portal.azure.com/).
2. W menu po lewej stronie wybierz **wszystkie usługi**.

    ![Wybierz opcję wszystkie usługi, w menu głównym](./media/backup-mabs-install-azure-stack/click-all-services.png)

3. W **wszystkie usługi** okno dialogowe, typ *usług odzyskiwania*. Po rozpoczęciu wpisywania, dane wejściowe filtruje listę zasobów. Gdy zostanie wyświetlony, wybierz **Magazyny usług odzyskiwania**.

    ![Typ usługi odzyskiwania w oknie dialogowym wszystkie usługi](./media/backup-mabs-install-azure-stack/all-services.png)

    Zostanie wyświetlona lista magazynów usług odzyskiwania w ramach subskrypcji.

4. Z listy magazynów usług odzyskiwania wybierz Twojego magazynu, aby otworzyć jego pulpitu nawigacyjnego.

    ![Typ usługi odzyskiwania w oknie dialogowym wszystkie usługi](./media/backup-mabs-install-azure-stack/rs-vault-dashboard.png)

5. W menu wprowadzenie magazynu, kliknij przycisk **kopii zapasowej** aby otworzyć Kreatora wprowadzenie.

    ![Wprowadzenie do kopii zapasowej](./media/backup-mabs-install-azure-stack/getting-started-backup.png)

    Zostanie otwarte menu kopii zapasowej.

    ![Kopia zapasowa cele — domyślna otwieranych](./media/backup-mabs-install-azure-stack/getting-started-menu.png)

6. W menu kopii zapasowej z **gdzie działa Twoje obciążenie** menu, wybierz opcję **lokalnymi**. Z **co chcesz utworzyć kopię zapasową?** listy rozwijanej wybierz polecenie obciążeń chcesz chronić, za pomocą serwera kopii zapasowej Azure. Jeśli nie masz pewności, który obciążeń, aby wybrać, wybierz **maszyn wirtualnych funkcji Hyper-V** , a następnie kliknij przycisk **przygotowanie infrastruktury**.

    ![lokalne i obciążeń, gdy cele](./media/backup-mabs-install-azure-stack/getting-started-menu-onprem-hyperv.png)

    **Przygotowanie infrastruktury** zostanie otwarte menu.

7. W **przygotowanie infrastruktury** menu, kliknij przycisk **Pobierz** aby otworzyć stronę sieci web do pobierania plików instalacyjnych serwera kopii zapasowej Azure.

    ![Wprowadzenie zmian kreatora wprowadzenie](./media/backup-mabs-install-azure-stack/prepare-infrastructure.png)

    Zostanie otwarta strona sieci web firmy Microsoft, hostującego pliki do pobrania serwer kopii zapasowej Azure.

8. Na stronie pobierania serwer kopii zapasowej Microsoft Azure, wybierz język, a następnie kliknij przycisk **Pobierz**.

    ![Otwiera Centrum pobierania](./media/backup-mabs-install-azure-stack/mabs-download-center-page.png)

9. Instalator serwera usługi Kopia zapasowa Azure składa się z ośmiu plików — Instalatora i siedmiu pliki bin. Sprawdź **nazwę pliku** wybierz wszystkie wymagane pliki, a następnie kliknij przycisk **dalej**. Pobierz wszystkie pliki w tym samym folderze.

    ![Pobierz center 1](./media/backup-mabs-install-azure-stack/download-center-selected-files.png)

    Rozmiar pobierania wszystkich plików instalacji jest większy niż 3 GB. Podczas pobierania 10 MB/s łącza pobierania wszystkich plików instalacji może potrwać do 60 minut. Pobierz pliki do lokalizacji pobierania.

## <a name="extract-azure-backup-server-install-files"></a>Wyodrębnij pliki instalacji serwera usługi Kopia zapasowa Azure

Po pobraniu wszystkich plików z maszyną wirtualną Azure stosu, przejdź do lokalizacji pobierania. Pierwszą fazę instalacji serwera usługi Kopia zapasowa Azure jest aby wyodrębnić pliki.

![Pobierz center 1](./media/backup-mabs-install-azure-stack/download-mabs-installer.png)

1. Aby rozpocząć instalację, z listy pobranych plików, kliknij przycisk **MicrosoftAzureBackupserverInstaller.exe**.

    > [!WARNING]
    > Co najmniej 4GB wolnego miejsca jest wymagane do wyodrębnienia plików instalacyjnych.
    >

2. W Kreatorze serwer kopii zapasowej Azure kliknij **dalej** aby kontynuować.

    ![Kreator tworzenia kopii zapasowej Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wiz-1.png)

3. Wybierz ścieżkę do plików Azure Utwórz kopię zapasową serwera, a następnie kliknij przycisk **dalej**.

   ![Kreator tworzenia kopii zapasowej Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-select-destination-1.png)

4. Sprawdź lokalizację wyodrębniania, a następnie kliknij przycisk **wyodrębnić**.

   ![Kreator tworzenia kopii zapasowej Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-extract-2.png)

5. Kreator wyodrębnia pliki i przygotowuje proces instalacji.

   ![Kreator tworzenia kopii zapasowej Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-install-3.png)

6. Po zakończeniu procesu wyodrębniania, kliknij przycisk **Zakończ**. Domyślnie **wykonania setup.exe** jest zaznaczone. Po kliknięciu **Zakończ**, Setup.exe instaluje serwer kopii zapasowej Microsoft Azure w określonej lokalizacji.

   ![Kreator tworzenia kopii zapasowej Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-finish-4.png)

## <a name="install-the-software-package"></a>Zainstaluj pakiet oprogramowania

W poprzednim kroku kliknięto **Zakończ** aby zakończyć fazy wyodrębniania, uruchom Kreatora instalacji serwera usługi Kopia zapasowa Azure.

![Kreator tworzenia kopii zapasowej Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5.png)

Serwer kopii zapasowej systemu Azure udostępnia kodu z programu Data Protection Manager. Zostanie wyświetlone odwołania do programu Data Protection Manager i programu DPM w Instalatorze serwer kopii zapasowej Azure. Jeśli serwer usługi Kopia zapasowa Azure i programu Data Protection Manager są oddzielne produkty, te produkty są ściśle powiązane.

1. Aby uruchomić Kreatora instalacji, kliknij przycisk **serwer kopii zapasowej Microsoft Azure**.

   ![Kreator tworzenia kopii zapasowej Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5b.png)

2. Na **powitalnej** kliknij **dalej**.

    ![Sprawdź serwera kopii zapasowej Azure — Zapraszamy i wymagania wstępne](./media/backup-mabs-install-azure-stack/mabs-install-wizard-setup-6.png)

3. Na **wymagań wstępnych sprawdza** kliknij **Sprawdź** ustalenie, jeśli zostały spełnione wymagania wstępne sprzętu i oprogramowania dla serwera usługi Kopia zapasowa Azure.

    ![Sprawdź serwera kopii zapasowej Azure — Zapraszamy i wymagania wstępne](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-7.png)

    Jeśli środowisko zawiera wymagania wstępne, pojawi się komunikat wskazujący, że komputer spełnia wymagania. Kliknij przycisk **Dalej**.  

    ![Serwera kopii zapasowej Azure — Sprawdzanie wymagań wstępnych Zakończono pomyślnie](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-passed-8.png)

    Jeśli w danym środowisku nie spełnia wymagania wstępne, zostanie określone problemy. Wymagania wstępne, które nie zostały spełnione są również wyświetlane w DpmSetup.log. Naprawić błędy wymagań wstępnych, a następnie uruchom **Sprawdź ponownie**. Nie można kontynuować instalacji, dopóki wszystkie wymagania wstępne są spełnione.

    ![Serwera kopii zapasowej Azure — nie zostały spełnione wymagania wstępne instalacji](./media/backup-mabs-install-azure-stack/installation-errors.png)

4. Serwer kopii zapasowej Microsoft Azure wymaga programu SQL Server. Pakiet instalacyjny serwera usługi Kopia zapasowa Azure jest powiązane z odpowiednie pliki binarne programu SQL Server. Jeśli chcesz użyć instalacji SQL, możesz. Jednak zalecanym wyborem jest let Instalatora, Dodaj nowe wystąpienie programu SQL Server. Aby upewnić się, Twoje rozwiązanie ze środowiskiem, kliknij przycisk **Sprawdź i zainstaluj**.

   > [!NOTE]
   > Serwer kopii zapasowej systemu Azure nie będzie działać przy użyciu zdalnego wystąpienia programu SQL Server. Wystąpienie używane przez serwer kopii zapasowej Azure musi być kontem lokalnym.
   >

    ![Sprawdź serwera kopii zapasowej Azure — Zapraszamy i wymagania wstępne](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-install-9.png)

    Po sprawdzeniu, czy maszyna wirtualna ma niezbędne wymagania wstępne dotyczące instalacji serwera usługi Kopia zapasowa Azure, kliknij **dalej**.

    ![Sprawdź serwera kopii zapasowej Azure — Zapraszamy i wymagania wstępne](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-ready-10.png)

    Jeśli wystąpi błąd zgodnie z zaleceniami ponowne uruchomienie komputera, a następnie uruchom ponownie komputer. Po ponownym uruchomieniu komputera, uruchom ponownie Instalatora, a po przejściu do **ustawienia programu SQL** ekranu, kliknij przycisk **Sprawdź ponownie**.

5. W **ustawienia instalacji**, podaj lokalizację instalacji plików serwera kopia zapasowa Microsoft Azure i kliknij przycisk **dalej**.

    ![PreReq2 kopia zapasowa Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-settings-11.png)

    Pliki tymczasowe lokalizacji jest wymagany do tworzenia kopii Azure. Upewnij się, że rozmiar pliki tymczasowe lokalizacji jest co najmniej 5% danych planowanych do wykonania kopii zapasowej Azure. Do ochrony dysku oddzielnych dyskach należy skonfigurować po zakończeniu instalacji. Aby uzyskać więcej informacji na temat pul magazynów, zobacz [Konfigurowanie pul magazynów i dysku magazynu](https://technet.microsoft.com/library/hh758075.aspx).

6. Na **ustawienia zabezpieczeń** ekranu, podaj silne hasło dla kont użytkowników lokalnych z ograniczeniami i kliknij przycisk **dalej**.

    ![PreReq2 kopia zapasowa Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-security-12.png)

7. Na **opcjonalnych w aktualizacji firmy Microsoft** ekranu, wybierz, czy chcesz użyć *Microsoft Update* Wyszukaj aktualizacje, a następnie kliknij przycisk **dalej**.

   > [!NOTE]
   > Zaleca się o przekierowania do Microsoft Update zapewnia bezpieczeństwo i ważne aktualizacje systemu Windows i innych produktów, takich jak Microsoft Azure Utwórz kopię zapasową serwera usługi Windows Update.
   >

    ![PreReq2 kopia zapasowa Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-update-13.png)

8. Przegląd *Podsumowanie ustawień* i kliknij przycisk **zainstalować**.

    ![PreReq2 kopia zapasowa Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-summary-14.png)

    Po zakończeniu instalacji serwer kopii zapasowej Azure Instalator uruchamia natychmiast Instalator agenta usług odzyskiwania Microsoft Azure.

9. Zostanie otwarta Instalator agenta usług odzyskiwania Microsoft Azure i sprawdza, czy połączenie z Internetem. Jeśli połączenie z Internetem jest dostępny, kontynuuj instalację. Jeśli nie ma łączności, podaj szczegóły serwera proxy, aby nawiązać połączenie z Internetem. Po określeniu ustawienia serwera proxy, kliknij przycisk **dalej**.

    ![PreReq2 kopia zapasowa Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-proxy-15.png)

10. Aby zainstalować agenta usług odzyskiwania Microsoft Azure, kliknij przycisk **zainstalować**.

    ![PreReq2 serwer kopii zapasowej systemu Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-mars-agent-16.png)

    Agent usług odzyskiwania Microsoft Azure, nazywany również agenta usługi Kopia zapasowa Azure umożliwia skonfigurowanie serwera kopii zapasowej Azure w magazynie usług odzyskiwania. Po skonfigurowaniu serwera kopii zapasowej Azure zawsze zostanie utworzona kopia zapasowa danych w tym samym magazynie usług odzyskiwania.

11. Po zakończeniu instalacji agenta usług odzyskiwania Microsoft Azure kliknij **dalej** uruchomić kolejną fazą: rejestrowanie serwera kopii zapasowej Azure w magazynie usług odzyskiwania.

    ![PreReq2 serwer kopii zapasowej systemu Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-complete-16.png)

    Instalator uruchamia **kreatora rejestrowania serwera**.

12. Przełącz się do subskrypcji platformy Azure i magazynu usług odzyskiwania. W **przygotowanie infrastruktury** menu, kliknij przycisk **Pobierz** pobrać poświadczenia magazynu. Jeśli **Pobierz** przycisk w kroku 2 nie jest aktywne, wybierz opcję **już pobrane lub przy użyciu najnowszych instalacji serwera usługi Kopia zapasowa Azure** Aby uaktywnić przycisk. Pobierz poświadczenia magazynu do lokalizacji, w którym są przechowywane pliki do pobrania. Należy pamiętać o tej lokalizacji, ponieważ będzie on potrzebny do następnego kroku.

    ![PreReq2 serwer kopii zapasowej systemu Azure](./media/backup-mabs-install-azure-stack/download-mars-credentials-17.png)

13. W **identyfikacji magazynu** menu, kliknij przycisk **Przeglądaj** można znaleźć poświadczenia magazynu usług odzyskiwania.

    ![PreReq2 serwer kopii zapasowej systemu Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-vault-id-18.png)

    W **Wybierz poświadczenia magazynu** okno dialogowe, przejdź do lokalizacji pobierania, wybierz poświadczenia magazynu, a następnie kliknij przycisk **Otwórz**.

    Ścieżka do poświadczeń pojawia się w menu magazynu identyfikacji. Kliknij przycisk **dalej** aby przejść do ustawienie szyfrowania.

14. W **ustawienie szyfrowania** okna dialogowego, podaj hasło do szyfrowania kopii zapasowych i lokalizację do przechowywania hasło, a następnie kliknij przycisk **dalej**.

    ![PreReq2 serwer kopii zapasowej systemu Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-encryption-19.png)

    Możesz podać własne hasło lub użyj generatora hasło, aby utworzyć. Hasło jest Twój i firma Microsoft nie jest w stanie zapisać i zarządzać tego hasła. Aby przygotować się do awarii, Zapisz hasło w dostępnej lokalizacji.

    Po kliknięciu **dalej**, Utwórz kopię zapasową serwera Azure jest zarejestrowana w magazynie usług odzyskiwania. Instalator kontynuuje Instalowanie programu SQL Server i serwer kopii zapasowej Azure.

    ![PreReq2 serwer kopii zapasowej systemu Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-still-installing-20.png)

15. Po zakończeniu działania Instalatora, stan pokazuje, że całe oprogramowanie został pomyślnie zainstalowany.

    ![PreReq2 serwer kopii zapasowej systemu Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-done-22.png)

    Po ukończeniu instalacji konsoli serwera usługi Kopia zapasowa Azure i ikony programu PowerShell serwera kopii zapasowej Azure są tworzone na pulpicie serwera.

## <a name="add-backup-storage"></a>Dodawanie magazynu kopii zapasowej

Pierwszej kopii zapasowej jest przechowywany w magazynie dołączonym do serwera kopii zapasowej Azure. Aby uzyskać więcej informacji na temat dodawania dysków, zobacz [magazynu Dodaj nowoczesnych kopii zapasowej](https://docs.microsoft.com/en-us/system-center/dpm/add-storage?view=sc-dpm-1801).

> [!NOTE]
> Należy dodać magazyn kopii zapasowych, nawet wtedy, gdy planujesz do wysyłania danych do platformy Azure. W architekturze serwera kopii zapasowej Azure magazyn usług odzyskiwania i blokad *drugi* kopię danych podczas Magazyn lokalny zawiera pierwszy (i obowiązkowe) kopii zapasowej.
>
>

## <a name="network-connectivity"></a>Połączenie sieciowe

Serwer kopii zapasowej systemu Azure wymaga połączenia z usługą kopia zapasowa Azure produktu działało poprawnie. Aby zweryfikować, czy komputer ma łączność z platformy Azure, użyj ```Get-DPMCloudConnection``` polecenia cmdlet w konsoli programu PowerShell serwera kopii zapasowej Azure. Jeśli dane wyjściowe polecenia cmdlet, ma wartość TRUE, a następnie istnieje połączenie, #else nie ma żadnej łączności.

W tym samym czasie subskrypcji platformy Azure musi być w dobrej kondycji. Aby sprawdzić stan subskrypcji i zarządzania nim, zaloguj się do [portal subskrypcji](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

Znając stanu łączności Azure i subskrypcji Azure, można użyć w poniższej tabeli, aby sprawdzić wpływ na funkcje wykonywania kopii zapasowej i przywracania.

| Stan łączności | Subskrypcja platformy Azure | Tworzenie kopii zapasowych Azure | Kopia zapasowa na dysku | Przywróć z platformy Azure | Przywróć z dysku |
| --- | --- | --- | --- | --- | --- |
| Połączono |Aktywne |Dopuszczeni |Dopuszczeni |Dopuszczeni |Dopuszczeni |
| Połączono |Wygasły |Zatrzymano |Zatrzymano |Dopuszczeni |Dopuszczeni |
| Połączono |Anulowana |Zatrzymano |Zatrzymano |Punkty odzyskiwania zatrzymane, a Azure usunięte |Zatrzymano |
| Utratą połączenia > 15 dni |Aktywne |Zatrzymano |Zatrzymano |Dopuszczeni |Dopuszczeni |
| Utratą połączenia > 15 dni |Wygasły |Zatrzymano |Zatrzymano |Dopuszczeni |Dopuszczeni |
| Utratą połączenia > 15 dni |Anulowana |Zatrzymano |Zatrzymano |Punkty odzyskiwania zatrzymane, a Azure usunięte |Zatrzymano |

### <a name="recovering-from-loss-of-connectivity"></a>Odzyskiwanie z utraty połączenia

Jeśli zapora lub serwer proxy uniemożliwia dostęp do platformy Azure, dozwolonych następującej domeny adresów w profilu zapory/serwera proxy:

- www.msftncsi.com
- \*.Microsoft.com
- \*.WindowsAzure.com
- \*.microsoftonline.com
- \*.windows.net

Po przywróceniu łączności do platformy Azure na serwerze kopii zapasowej Azure, stan subskrypcji platformy Azure określa operacje, które mogą być wykonywane. Gdy serwer jest **połączony**, skorzystaj z tabeli w [połączeń sieciowych](backup-mabs-install-azure-stack.md#network-connectivity) Aby wyświetlić dostępne operacje.

### <a name="handling-subscription-states"></a>Obsługa stany subskrypcji

Istnieje możliwość zmiany subskrypcji platformy Azure z *wygasłe* lub *Deprovisioned* stan *Active* stanu. Gdy stan subskrypcji nie jest *Active*:

- Gdy subskrypcja jest *Deprovisioned*, utraty funkcji. Przywracanie subskrypcja *Active*, revives funkcji wykonywania kopii zapasowej i przywracania. Jeśli dane kopii zapasowej na dysku lokalnym została zachowana okres przechowywania wystarczająco duże, gdy można pobrać danych kopii zapasowej. Jednak dane kopii zapasowej na platformie Azure po nieodwracalnie wprowadza subskrypcji *Deprovisioned* stanu.
- Gdy subskrypcja jest *wygasłe*, utraty funkcji. Zaplanowane kopie zapasowe nie są uruchamiane, gdy subskrypcja jest *wygasłe*.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli kopia zapasowa Microsoft Azure serwera zakończy się niepowodzeniem z błędami fazy instalacji (lub kopii zapasowej lub przywracania), zobacz [dokumentu kody błędów](https://support.microsoft.com/kb/3041338).
Można także odwoływać się do [— często zadawane pytania dotyczące usługi Kopia zapasowa Azure](backup-azure-backup-faq.md)

## <a name="next-steps"></a>Kolejne kroki

Artykuł [przygotowywanie środowiska dla programu DPM](https://docs.microsoft.com/en-us/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1801), zawiera informacje o obsługiwanych konfiguracjach serwera usługi Kopia zapasowa Azure.

Aby lepiej zrozumieć ochrony obciążenia w programie Microsoft Azure Utwórz kopię zapasową serwera można skorzystaj z poniższych artykułów.

- [Kopia zapasowa programu SQL Server](https://docs.microsoft.com/en-us/azure/backup/backup-mabs-sql-azure-stack)
- [Kopia zapasowa programu SharePoint server](https://docs.microsoft.com/en-us/azure/backup/backup-mabs-sharepoint-azure-stack)
- [Alternatywny serwer kopii zapasowej](backup-azure-alternate-dpm-server.md)
