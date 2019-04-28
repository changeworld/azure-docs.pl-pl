---
title: Instalowanie usługi Azure Backup Server w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Użyj usługi Azure Backup Server do ochrony lub tworzenia kopii zapasowych obciążeń w usłudze Azure Stack.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: raynew
ms.openlocfilehash: d3a2ffdedda7f541fb1a3f37a8b40bc7af3dcb57
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60852143"
---
# <a name="install-azure-backup-server-on-azure-stack"></a>Instalowanie składnika Azure Backup Server w usłudze Azure Stack

W tym artykule opisano sposób instalowania usługi Azure Backup Server w usłudze Azure Stack. Za pomocą usługi Azure Backup Server można chronić infrastrukturę jako obciążeń usługi (IaaS), takie jak maszyny wirtualne działające w usłudze Azure Stack. Korzyść z używania usługi Azure Backup Server do ochrony obciążeń jest wszystkich ochrony obciążenia można zarządzać z poziomu pojedynczej konsoli.

> [!NOTE]
> Aby dowiedzieć się o funkcje zabezpieczeń, zapoznaj się [dokumentacji funkcji zabezpieczeń usługi Azure Backup](backup-azure-security-feature.md).
>

## <a name="azure-backup-server-protection-matrix"></a>Macierz ochrony usługi Azure Backup Server
Usługa Azure Backup Server chroni następujące obciążenia maszyny wirtualnej usługi Azure Stack.

| Chronione źródło danych | Ochrona i odzyskiwanie |
| --------------------- | ----------------------- |
| Windows Server o częściowej lub półroczny kanał - Enterprise/Datacenter/Standard | Woluminy, pliki, foldery |
| Windows Server 2016 — Datacenter/Enterprise/Standard | Woluminy, pliki, foldery |
| Windows Server 2012 R2 - Datacenter/Enterprise/Standard | Woluminy, pliki, foldery |
| Windows Server 2012 — Enterprise-Datacenter/Standard | Woluminy, pliki, foldery |
| Windows Server 2008 R2 - Datacenter/Enterprise/Standard | Woluminy, pliki, foldery |
| SQL Server 2016 | Database (Baza danych) |
| SQL Server 2014 | Database (Baza danych) |
| SQL Server 2012 SP1 | Database (Baza danych) |
| Program SharePoint 2016 | Farma, baza danych, serwera sieci Web, serwer sieci web |
| SharePoint 2013 | Farma, baza danych, serwera sieci Web, serwer sieci web |
| SharePoint 2010 | Farma, baza danych, serwera sieci Web, serwer sieci web |

## <a name="prerequisites-for-the-azure-backup-server-environment"></a>Wymagania wstępne dotyczące środowiska usługi Azure Backup Server

Podczas instalowania usługi Azure Backup Server w środowisku usługi Azure Stack, należy wziąć pod uwagę zalecenia przedstawione w tej sekcji. Instalator usługi Azure Backup Server sprawdza, czy w środowisku zawierającym niezbędnych wymagań wstępnych, ale można zaoszczędzić sporo czasu od przygotowania przed zainstalowaniem.

### <a name="determining-size-of-virtual-machine"></a>Określenie rozmiaru maszyny wirtualnej
Aby uruchomić usługi Azure Backup Server na maszynie wirtualnej usługi Azure Stack, należy użyć rozmiaru A2 lub większym. Aby uzyskać pomoc w wyborze rozmiar maszyny wirtualnej, należy pobrać [kalkulatora rozmiaru maszyny Wirtualnej platformy Azure Stack](https://www.microsoft.com/download/details.aspx?id=56832).

### <a name="virtual-networks-on-azure-stack-virtual-machines"></a>Sieci wirtualne na maszynach wirtualnych usługi Azure Stack
Wszystkie maszyny wirtualne używane w obciążeniu usługi Azure Stack musi należeć do tej samej sieci wirtualnej platformy Azure i subskrypcji platformy Azure.

### <a name="azure-backup-server-vm-performance"></a>Azure Backup Server na maszynie Wirtualnej wydajności
Jeśli udostępniony innym maszynom wirtualnym, rozmiar i limity operacje We/Wy — wpływ wydajności maszyny Wirtualnej usługi Azure Backup Server konto magazynu. Z tego powodu należy użyć oddzielnego konta magazynu dla maszyny wirtualnej usługi Azure Backup Server. Agent usługi Azure Backup, uruchomiony na serwerze usługi Azure Backup wymaga tymczasowego magazynu dla:
- własnego użytku (lokalizacja pamięci podręcznej),
- danych przywróconych z chmury (lokalny obszar przemieszczania)

### <a name="configuring-azure-backup-temporary-disk-storage"></a>Konfigurowanie usługi Azure Backup magazyn na tymczasowym dysku
Każda maszyna wirtualna usługi Azure Stack jest dostarczany z magazyn na tymczasowym dysku, który jest dostępny dla użytkownika jako wolumin `D:\`. Lokalny obszar przemieszczania wymagany przez usługę Azure Backup można skonfigurować na `D:\`, a lokalizację pamięci podręcznej można umieścić na `C:\`. W ten sposób nie magazynu musi można używać poza na dyskach danych dołączonych do maszyny wirtualnej usługi Azure Backup Server.

### <a name="storing-backup-data-on-local-disk-and-in-azure"></a>Przechowywania danych kopii zapasowej na dysku lokalnym i na platformie Azure
Usługa Azure Backup Server przechowuje dane kopii zapasowych na dyskach platformy Azure dołączonych do maszyny wirtualnej, na potrzeby odzyskiwania operacyjnego. Gdy dyski i miejsca do magazynowania są dołączone do maszyny wirtualnej, serwer usługi Azure Backup zarządza magazynu. Ilość miejsca w magazynie danych kopii zapasowej zależy od liczby i rozmiaru dysków dołączonych do każdego [maszyny wirtualnej usługi Azure Stack](/azure-stack/user/azure-stack-storage-overview). Każdego rozmiaru maszyny Wirtualnej platformy Azure Stack jest maksymalna liczba dysków, które mogą być dołączone do maszyny wirtualnej. Na przykład A2 to cztery dyski. A3 jest ośmiu dysków. A4 jest 16 dysków. Ponownie rozmiaru i liczby dysków określa puli łączna liczba magazynów kopii zapasowych.

> [!IMPORTANT]
> Należy **nie** przechowywanie danych odzyskiwania operacyjnego (kopia zapasowa) na dyskach dołączonych do serwera usługi Azure Backup więcej niż pięć dni.
>

Przechowywanie kopii zapasowej danych na platformie Azure zmniejsza infrastruktury kopii zapasowych w usłudze Azure Stack. Jeśli dane są starsze niż pięć dni, powinny być przechowywane na platformie Azure.

Aby przechowywać dane kopii zapasowej na platformie Azure, należy utworzyć lub użyć magazynu usług Recovery Services. Podczas przygotowywania do tworzenia kopii zapasowych obciążeń serwera Azure Backup, możesz [skonfigurować magazyn usługi Recovery Services](backup-azure-microsoft-azure-backup.md#create-a-recovery-services-vault). Po każdym uruchomieniu zadania tworzenia kopii zapasowej, punkt odzyskiwania jest tworzony w magazynie. Każdy magazyn usługi Recovery Services zawiera maksymalnie 9999 punktów odzyskiwania. W zależności od liczby punktów odzyskiwania utworzonych i jak długo są przechowywane przez wiele lat można przechowywać dane kopii zapasowej. Na przykład można utworzyć miesięczne punktów odzyskiwania i przechowywać je przez okres pięciu lat.
 
### <a name="scaling-deployment"></a>Skalowanie wdrożenia
Jeśli chcesz skalować wdrożenie, masz następujące opcje:
  - Skalowanie w górę — Zwiększ rozmiar maszyny wirtualnej usługi Azure Backup Server z serii D-series i zwiększyć Magazyn lokalny [zgodnie z instrukcjami maszyny wirtualnej usługi Azure Stack](/azure-stack/user/azure-stack-manage-vm-disks).
  - Obsługa danych — wysyłanie starszych danych do platformy Azure i zachowywanie tylko najnowszych danych w magazynie dołączonym do serwera usługi Azure Backup.
  - Skalowanie w poziomie — dodawanie kolejnych serwerów kopii zapasowej platformy Azure w celu włączenia ochrony obciążeń.

### <a name="net-framework"></a>.NET Framework

.NET framework 3.5 z dodatkiem SP1 lub nowszym należy zainstalować na maszynie wirtualnej.

### <a name="joining-a-domain"></a>Przyłączanie do domeny

Maszyna wirtualna usługi Azure Backup Server muszą być przyłączone do domeny. Użytkownik domeny z uprawnieniami administratora, należy zainstalować usługi Azure Backup Server na maszynie wirtualnej.

## <a name="using-an-iaas-vm-in-azure-stack"></a>W usłudze Azure Stack przy użyciu maszyn wirtualnych IaaS

Podczas wybierania serwera usługi Azure Backup Server, należy uruchomić z obrazem galerii systemu Windows Server 2012 R2 Datacenter lub Windows Server 2016 Datacenter. Artykuł [Utwórz pierwszą maszynę wirtualną Windows w witrynie Azure portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), oferuje samouczek Wprowadzenie do maszyn wirtualnych zalecane. Zalecane minimalne wymagania dotyczące serwera maszyny wirtualnej (VM) powinny być następujące: A2 warstwie podstawowa przy użyciu dwóch rdzeni oraz 3,5 GB pamięci RAM.

Ochrona obciążenia za pomocą usługi Azure Backup Server ma wiele niuanse. Artykuł [Instalowanie programu DPM jako maszynę wirtualną platformy Azure](https://technet.microsoft.com/library/jj852163.aspx), wyjaśniających te różnice. Przed wdrożeniem na maszynie, przeczytaj ten artykuł całkowicie.

> [!NOTE]
> Usługa Azure Backup Server jest przeznaczony do działania na dedykowanym, wyznaczonym do tego celu maszyny wirtualnej. Serwer usługi Azure Backup nie można zainstalować na:
> - Komputer działa jako kontroler domeny
> - Komputer, na którym jest zainstalowana rola serwera aplikacji
> - Komputer, na którym jest uruchomiony program Exchange Server
> - Komputer, który jest węzłem klastra

Zawsze Dołącz do usługi Azure Backup Server do domeny. Jeśli musisz przenieść usługi Azure Backup Server do innej domeny, najpierw zainstaluj serwer usługi Azure Backup, a następnie przyłączyć ją do nowej domeny. Po wdrożeniu serwera usługi Azure Backup nie można jej przenieść do nowej domeny.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Konfigurowanie replikacji magazynu

Opcji replikacji magazynu usługi Recovery Services vault pozwala wybrać magazyn geograficznie nadmiarowy i Magazyn lokalnie nadmiarowy. Domyślnie magazynów usługi Recovery Services używają magazynu geograficznie nadmiarowego. Jeśli ten magazyn jest Twój podstawowy magazyn, pozostaw opcję magazynu ustawioną na magazyn geograficznie nadmiarowy. Wybierz magazyn lokalnie nadmiarowy, jeśli chcesz, aby opcja tańsze, które są mniej trwałe. Więcej informacji o opcjach magazynu [geograficznie nadmiarowego](../storage/common/storage-redundancy-grs.md) i [lokalnie nadmiarowego](../storage/common/storage-redundancy-lrs.md) można znaleźć w temacie [Azure Storage replication overview](../storage/common/storage-redundancy.md) (Omówienie replikacji usługi Azure Storage).

Aby edytować ustawienia replikacji magazynu:

1. Wybierz swój magazyn, aby otworzyć menu Ustawienia i pulpitu nawigacyjnego magazynu. Jeśli **ustawienia** menu nie zostanie otwarty, kliknij przycisk **wszystkie ustawienia** na pulpicie nawigacyjnym magazynu.
2. Na **ustawienia** menu, kliknij przycisk **infrastruktura zapasowa** > **konfiguracji kopii zapasowej** otworzyć **konfiguracji kopii zapasowej**menu. Na **konfiguracji kopii zapasowej** menu, wybierz opcję replikacji swojego magazynu.

    ![Lista magazynów kopii zapasowych](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

## <a name="download-azure-backup-server-installer"></a>Pobierz Instalatora usługi Azure Backup Server

Istnieją dwa sposoby, aby pobrać Instalatora serwera usługi Azure Backup. Możesz pobrać Instalatora serwera usługi Azure Backup z [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=55269). Można również pobrać Instalatora serwera usługi Azure Backup, zgodnie z konfigurowanym w magazynie usługi Recovery Services. W poniższych krokach objaśniono instrukcje pobierania Instalatora z poziomu portalu Azure podczas konfigurowania magazynu usługi Recovery Services.

1. Z maszyny wirtualnej usługi Azure Stack [Zaloguj się do subskrypcji platformy Azure w witrynie Azure portal](https://portal.azure.com/).
2. W menu po lewej stronie wybierz **wszystkich usług**.

    ![Wybierz opcję wszystkie usługi, w menu głównym](./media/backup-mabs-install-azure-stack/click-all-services.png)

3. W **wszystkich usług** okno dialogowe, typ *usługi Recovery Services*. Po rozpoczęciu wpisywania, dane wejściowe filtrowanie listy zasobów. Gdy zostanie wyświetlony, wybierz **Magazyny usługi Recovery Services**.

    ![Wpisz wszystkie okna dialogowego usługi Recovery Services](./media/backup-mabs-install-azure-stack/all-services.png)

    Zostanie wyświetlona lista magazynów usługi Recovery Services w ramach subskrypcji.

4. Z listy magazynów usługi Recovery Services wybierz swój magazyn, aby otworzyć jego pulpit nawigacyjny.

    ![Wpisz wszystkie okna dialogowego usługi Recovery Services](./media/backup-mabs-install-azure-stack/rs-vault-dashboard.png)

5. W menu wprowadzenie do magazynu usługi kliknij **kopii zapasowej** aby otworzyć Kreatora wprowadzenie.

    ![Wprowadzenie do kopii zapasowej](./media/backup-mabs-install-azure-stack/getting-started-backup.png)

    Zostanie otwarte menu kopii zapasowej.

    ![Backup-goals-default-opened](./media/backup-mabs-install-azure-stack/getting-started-menu.png)

6. W menu kopii zapasowej z **gdzie jest uruchomione Twoje obciążenie** menu, wybierz opcję **On-premises**. Z **jakich elementów chcesz utworzyć kopię zapasową?** rozwijanego menu, wybierz opcję obciążeń, którą chcesz chronić, za pomocą serwera usługi Azure Backup. Jeśli nie masz pewności, które obciążenia pracą, aby wybrać, wybierz opcję **maszyn wirtualnych funkcji Hyper-V** a następnie kliknij przycisk **Przygotuj infrastrukturę**.

    ![w lokalnych i obciążeń, gdy cele](./media/backup-mabs-install-azure-stack/getting-started-menu-onprem-hyperv.png)

    **Przygotowanie infrastruktury** zostanie otwarte menu.

7. W **przygotowanie infrastruktury** menu, kliknij przycisk **Pobierz** aby otworzyć stronę sieci web do pobierania plików instalacyjnych serwera usługi Azure Backup.

    ![Getting Started kreatora zmiany](./media/backup-mabs-install-azure-stack/prepare-infrastructure.png)

    Otwiera stronę internetową firmy Microsoft, który jest hostem plików do pobrania dla usługi Azure Backup Server.

8. Na stronie pobierania serwera usługi Microsoft Azure Backup, wybierz język, a następnie kliknij przycisk **Pobierz**.

    ![Zostanie otwarte Centrum pobierania](./media/backup-mabs-install-azure-stack/mabs-download-center-page.png)

9. Instalator serwera usługi Azure Backup składa się z ośmiu plików — Instalatora i siedem plików bin. Sprawdź **nazwy pliku** wybierz wszystkie wymagane pliki, a następnie kliknij przycisk **dalej**. Pobierz wszystkie pliki w tym samym folderze.

    ![1 Centrum pobierania](./media/backup-mabs-install-azure-stack/download-center-selected-files.png)

    Pobieranie rozmiar wszystkich plików instalacji jest większy niż 3 GB. Podczas pobierania 10 MB/s łącza, pobierając wszystkie pliki instalacyjne może potrwać do 60 minut. Pobierz pliki do lokalizacji pobierania.

## <a name="extract-azure-backup-server-install-files"></a>Wyodrębnij pliki instalacji usługi Azure Backup Server

Po pobraniu wszystkich plików do maszyny wirtualnej usługi Azure Stack, przejdź do lokalizacji pobierania. Pierwsza faza instalacji z serwera usługi Azure Backup jest aby wyodrębnić pliki.

![1 Centrum pobierania](./media/backup-mabs-install-azure-stack/download-mabs-installer.png)

1. Aby rozpocząć instalację, z listy pobranych plików, kliknij przycisk **MicrosoftAzureBackupserverInstaller.exe**.

    > [!WARNING]
    > Co najmniej 4GB wolnego miejsca na dysku jest wymagana do wyodrębnienia plików instalacyjnych.
    >

2. W Kreatorze usługi Azure Backup Server kliknij **dalej** aby kontynuować.

    ![Kreator tworzenia kopii zapasowych Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wiz-1.png)

3. Wybierz ścieżkę dla plików usługi Azure Backup Server, a następnie kliknij przycisk **dalej**.

   ![Kreator tworzenia kopii zapasowych Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-select-destination-1.png)

4. Sprawdź lokalizację wyodrębniania, a następnie kliknij przycisk **wyodrębnić**.

   ![Kreator tworzenia kopii zapasowych Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-extract-2.png)

5. Kreator wyodrębnia pliki i przygotowuje proces instalacji.

   ![Kreator tworzenia kopii zapasowych Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-install-3.png)

6. Po zakończeniu procesu wyodrębniania, kliknij przycisk **Zakończ**. Domyślnie **wykonania setup.exe** jest zaznaczone. Po kliknięciu **Zakończ**, Setup.exe instaluje program Microsoft Azure Backup Server do określonej lokalizacji.

   ![Kreator tworzenia kopii zapasowych Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-finish-4.png)

## <a name="install-the-software-package"></a>Zainstaluj pakiet oprogramowania

W poprzednim kroku kliknięto przycisk **Zakończ** aby zakończyć fazy wyodrębniania, a następnie uruchomić Kreatora konfiguracji serwera usługi Azure Backup.

![Kreator tworzenia kopii zapasowych Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5.png)

Usługa Azure Backup Server udostępnia kod za pomocą programu Data Protection Manager. Zostanie wyświetlony odwołania do programu Data Protection Manager i programu DPM, w oknie Instalatora usługi Azure Backup Server. Chociaż usługi Azure Backup Server i Data Protection Manager są oddzielne produkty, te produkty są ściśle powiązane.

1. Aby uruchomić Kreatora instalacji, kliknij **serwera usługi Microsoft Azure Backup**.

   ![Kreator tworzenia kopii zapasowych Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5b.png)

2. Na **powitalnej** ekranu, kliknij przycisk **dalej**.

    ![Sprawdź, usługa Azure Backup Server — wprowadzenie i wymagania wstępne](./media/backup-mabs-install-azure-stack/mabs-install-wizard-setup-6.png)

3. Na **funkcji sprawdzania wymagań wstępnych** ekranu, kliknij przycisk **Sprawdź** ustalenie, jeśli są spełnione wymagania wstępne sprzętu i oprogramowania dla usługi Azure Backup Server.

    ![Sprawdź, usługa Azure Backup Server — wprowadzenie i wymagania wstępne](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-7.png)

    Jeśli w środowisku zawierającym wymagania wstępne, zostanie wyświetlony komunikat wskazujący, czy maszyna spełnia wymagania. Kliknij przycisk **Dalej**.  

    ![Usługa Azure Backup Server — Sprawdzanie wymagań wstępnych Zakończono pomyślnie](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-passed-8.png)

    Jeśli środowisko nie spełnia niezbędnych wymagań wstępnych, zostanie określone problemy. Wymagania wstępne, które nie zostały spełnione są również wyszczególnione w DpmSetup.log. Naprawić błędy wymagań wstępnych, a następnie uruchom **Sprawdź ponownie**. Nie można kontynuować instalacji, dopóki nie są spełnione wszystkie wymagania wstępne.

    ![Usługa Azure Backup Server — wymagania wstępne instalacji, które nie zostały spełnione](./media/backup-mabs-install-azure-stack/installation-errors.png)

4. Serwer usługi Microsoft Azure Backup wymaga programu SQL Server. Pakiet instalacyjny usługi Azure Backup Server oferujemy wraz z pakietem odpowiednie pliki binarne programu SQL Server. Jeśli użytkownik chce użyć instalacji SQL, można. Jednak zalecane to umożliwić Instalatora, Dodaj nowe wystąpienie programu SQL Server. Aby upewnić się, Twoje rozwiązanie ze środowiskiem, kliknij przycisk **Sprawdź i zainstaluj**.

   > [!NOTE]
   > Usługa Azure Backup Server nie będzie działać przy użyciu zdalnego wystąpienia programu SQL Server. Wystąpienie używane przez usługi Azure Backup Server musi być lokalny.
   >

    ![Sprawdź, usługa Azure Backup Server — wprowadzenie i wymagania wstępne](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-install-9.png)

    Po sprawdzeniu, jeśli maszyna wirtualna ma niezbędne wymagania wstępne dotyczące instalacji serwera usługi Azure Backup, kliknij przycisk **dalej**.

    ![Sprawdź, usługa Azure Backup Server — wprowadzenie i wymagania wstępne](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-ready-10.png)

    Jeśli wystąpi awaria zgodnie z zaleceniami ponowne uruchomienie komputera, uruchom ponownie maszynę. Po ponownym uruchomieniu komputera, uruchom ponownie Instalatora, a kiedy można uzyskać dostęp do **ustawień serwera SQL** ekranu, kliknij przycisk **Sprawdź ponownie**.

5. W **ustawienia instalacji**, określ lokalizację instalacji plików serwera kopia zapasowa Microsoft Azure i kliknij przycisk **dalej**.

    ![PreReq2 kopii zapasowych Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-settings-11.png)

    Pliki tymczasowe lokalizacja jest wymagana do utworzenia kopii zapasowej na platformie Azure. Upewnij się, że rozmiar lokalizację tymczasową jest równoważne z co najmniej 5% danych, zaplanowane do wykonania kopii zapasowej na platformie Azure. Do ochrony dysku oddzielne dyski należy skonfigurować po ukończeniu instalacji. Aby uzyskać więcej informacji na temat pul magazynów, zobacz [skonfiguruj pule magazynów i Magazyn dyskowy](https://technet.microsoft.com/library/hh758075.aspx).

6. Na **ustawienia zabezpieczeń** ekranu, podaj silne hasło dla kont użytkowników lokalnych z ograniczeniami i kliknij przycisk **dalej**.

    ![PreReq2 kopii zapasowych Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-security-12.png)

7. Na **zgłoszenie zgody na uczestnictwo w aktualizacji Microsoft** ekranu, wybierz, czy chcesz użyć *Microsoft Update* sprawdzać dostępność aktualizacji, a następnie kliknij przycisk **dalej**.

   > [!NOTE]
   > Firma Microsoft zaleca posiadanie Windows Update, przekierowanie do usługi Microsoft Update oferuje bezpieczeństwo i ważne aktualizacje systemu Windows i innych produktów, takich jak serwer usługi Microsoft Azure Backup.
   >

    ![PreReq2 kopii zapasowych Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-update-13.png)

8. Przegląd *Podsumowanie ustawień* i kliknij przycisk **zainstalować**.

    ![PreReq2 kopii zapasowych Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-summary-14.png)

    Po zakończeniu instalowania usługi Azure Backup Server Instalator natychmiast uruchamia Instalatora agenta usługi Microsoft Azure Recovery Services.

9. Otwiera Instalatora agenta usług odzyskiwania Microsoft Azure i sprawdza, czy połączenie z Internetem. Jeśli połączenie z Internetem jest dostępny, kontynuuj instalację. Jeśli nie ma łączności, podaj szczegóły serwera proxy, aby nawiązać połączenie z Internetem. Po określeniu ustawienia serwera proxy, kliknij przycisk **dalej**.

    ![PreReq2 kopii zapasowych Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-proxy-15.png)

10. Aby zainstalować agenta usługi Microsoft Azure Recovery Services, kliknij przycisk **zainstalować**.

    ![Usługa Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-mars-agent-16.png)

    Agent usług Microsoft Azure Recovery Services, jest określana skrótem agenta usługi Azure Backup umożliwia skonfigurowanie serwera Azure Backup do magazynu usługi Recovery Services. Po skonfigurowaniu serwera usługi Azure Backup będzie zawsze tworzyć kopie zapasowe danych w tym samym magazynie usługi Recovery Services.

11. Gdy agent usług Microsoft Azure Recovery Services kończy instalację, kliknij przycisk **dalej** można uruchomić do następnej fazy: rejestrowanie usługi Azure Backup Server przy użyciu magazynu usługi Recovery Services.

    ![Usługa Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-complete-16.png)

    Instalator uruchamia **kreatora rejestrowania serwera**.

12. Przełącz się do subskrypcji platformy Azure i magazynu usługi Recovery Services. W **Przygotuj infrastrukturę** menu, kliknij przycisk **Pobierz** pobierania poświadczeń magazynu. Jeśli **Pobierz** przycisk w kroku 2 nie jest aktywne, wybierz opcję **już pobrano lub używana najnowsza Instalacja serwera usługi Azure Backup** Aby uaktywnić przycisk. Poświadczenia magazynu zostaną pobrane do lokalizacji, w którym są przechowywane pliki do pobrania. Należy pamiętać o tej lokalizacji, ponieważ należy ona do kolejnego kroku.

    ![Usługa Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/download-mars-credentials-17.png)

13. W **identyfikacji magazynu** menu, kliknij przycisk **Przeglądaj** można znaleźć poświadczenia magazynu usługi Recovery Services.

    ![Usługa Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-vault-id-18.png)

    W **Wybierz poświadczenia magazynu** okno dialogowe, przejdź do lokalizacji pobierania wybierz poświadczenia magazynu, a następnie kliknij przycisk **Otwórz**.

    Ścieżka do poświadczeń pojawia się w menu magazynu identyfikacji. Kliknij przycisk **dalej** aby przejść do ustawienia szyfrowania.

14. W **ustawienie szyfrowania** okna dialogowego, podaj hasło do szyfrowania kopii zapasowych i lokalizację, aby zapisać hasło, a następnie kliknij przycisk **dalej**.

    ![Usługa Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-encryption-19.png)

    Możesz podać własne hasło lub użyj generatora hasło, aby utworzyć go dla Ciebie. Hasło to należy do Ciebie, a firma Microsoft nie jest w stanie zapisać ani zarządzać tego hasła. Do przygotowania do awarii, należy zapisać swoje hasło w dostępnej lokalizacji.

    Po kliknięciu **dalej**, serwera usługi Azure Backup jest zarejestrowany w magazynie usługi Recovery Services. Instalator kontynuuje Instalowanie programu SQL Server i usługi Azure Backup Server.

    ![Usługa Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-still-installing-20.png)

15. Po zakończeniu działania Instalatora, stan pokazuje, czy całe oprogramowanie został pomyślnie zainstalowany.

    ![Usługa Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-done-22.png)

    Po ukończeniu instalacji konsoli serwera usługi Azure Backup i Azure Backup Server PowerShell ikony są tworzone na pulpicie serwera.

## <a name="add-backup-storage"></a>Dodaj magazyn kopii zapasowych

Pierwszy kopia zapasowa jest przechowywana w magazynie dołączonym do komputera serwera usługi Azure Backup. Aby uzyskać więcej informacji na temat dodawania dysków, zobacz [Dodaj Modern Backup storage](https://docs.microsoft.com/system-center/dpm/add-storage?view=sc-dpm-1801).

> [!NOTE]
> Musisz dodać magazyn kopii zapasowych, nawet jeśli zamierzasz wysłać danych na platformie Azure. W ramach usługi Azure Backup Server architektury magazynu usług odzyskiwania przechowuje *drugi* kopię danych podczas Magazyn lokalny zawiera pierwszą (i obowiązkowa) kopii zapasowej.
>
>

## <a name="network-connectivity"></a>Połączenie sieciowe

Usługa Azure Backup Server wymaga połączenia z usługą Azure Backup dla produktu działało poprawnie. Aby sprawdzić, czy komputer ma łączność z platformy Azure, należy użyć ```Get-DPMCloudConnection``` polecenia cmdlet w konsoli programu PowerShell serwera kopii zapasowych Azure. Jeśli dane wyjściowe polecenia cmdlet ma wartość TRUE, a następnie połączenie istnieje inne nie ma łączności.

W tym samym czasie subskrypcji platformy Azure musi być w dobrej kondycji. Aby dowiedzieć się, stan subskrypcji i zarządzać nią, zaloguj się do [portalu subskrypcji](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

Po sprawdzeniu stanu łączność platformy Azure i subskrypcji platformy Azure, można użyć w poniższej tabeli, aby dowiedzieć się o wpływ na funkcje wykonywania kopii zapasowej i przywracania oferowane.

| Stan łączności | Subskrypcja platformy Azure | Tworzenie kopii zapasowej na platformie Azure | Tworzenie kopii zapasowej dysku | Przywracanie z platformy Azure | Przywracanie z dysku |
| --- | --- | --- | --- | --- | --- |
| Połączono |Aktywne |Dopuszczeni |Dopuszczeni |Dopuszczeni |Dopuszczeni |
| Połączono |Wygaśnięcie |Zatrzymano |Zatrzymano |Dopuszczeni |Dopuszczeni |
| Połączono |Anulowanie aprowizacji |Zatrzymano |Zatrzymano |Punkty odzyskiwania zatrzymane, a usługa Azure usunięte |Zatrzymano |
| Utraty łączności > 15 dni |Aktywne |Zatrzymano |Zatrzymano |Dopuszczeni |Dopuszczeni |
| Utraty łączności > 15 dni |Wygaśnięcie |Zatrzymano |Zatrzymano |Dopuszczeni |Dopuszczeni |
| Utraty łączności > 15 dni |Anulowanie aprowizacji |Zatrzymano |Zatrzymano |Punkty odzyskiwania zatrzymane, a usługa Azure usunięte |Zatrzymano |

### <a name="recovering-from-loss-of-connectivity"></a>Odzyskiwanie z utraty łączności

Jeśli zapora lub serwer proxy uniemożliwia dostęp do platformy Azure, umieścić na liście dozwolonych następujące domeny adresów w profilu serwera proxy i zapory:

- `http://www.msftncsi.com/ncsi.txt`
- \*.Microsoft.com
- \*.WindowsAzure.com
- \*.microsoftonline.com
- \*.windows.net

Po przywróceniu łączności na platformę Azure do usługi Azure Backup Server, stan subskrypcji platformy Azure określa operacje, które mogą być wykonywane. Gdy serwer jest **połączono**, skorzystać z tabeli w [połączenia sieciowego](backup-mabs-install-azure-stack.md#network-connectivity) Aby wyświetlić dostępne operacje.

### <a name="handling-subscription-states"></a>Obsługa stany subskrypcji

Istnieje możliwość zmienić subskrypcję platformy Azure z *wygasłe* lub *cofnięcia aprowizacji* do stanu *Active* stanu. Gdy stan subskrypcji nie jest *Active*:

- Gdy subskrypcja jest *cofnięcia aprowizacji*, utraty funkcjonalności. Przywracanie subskrypcja *Active*, revives funkcji wykonywania kopii zapasowej i przywracania. Jeśli dane kopii zapasowej na dysku lokalnym została zachowana z okresem przechowywania wystarczająco duże, można pobrać danych kopii zapasowej. Jednak dane kopii zapasowej na platformie Azure jest nieodwracalnie w momencie wejścia subskrypcji *cofnięcia aprowizacji* stanu.
- Gdy subskrypcja jest *wygasłe*, utraty funkcjonalności. Zaplanowane kopie zapasowe nie są uruchamiane, gdy subskrypcja jest *wygasłe*.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli serwer usługi Microsoft Azure Backup kończy się niepowodzeniem z błędami w fazie konfiguracji (lub kopii zapasowej lub przywracania), zobacz [dokumentu kody błędów](https://support.microsoft.com/kb/3041338).
Może również dotyczyć [kopia zapasowa Azure związane z — często zadawane pytania](backup-azure-backup-faq.md)

## <a name="next-steps"></a>Kolejne kroki

Artykuł [przygotowywanie środowiska dla programu DPM](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1801), zawiera informacje dotyczące obsługiwanych konfiguracji serwera usługi Azure Backup.

Można użyć następujące artykuły, aby lepiej zrozumieć ochronę obciążeń za pomocą serwera usługi Microsoft Azure Backup.

- [Kopia zapasowa programu SQL Server](https://docs.microsoft.com/azure/backup/backup-mabs-sql-azure-stack)
- [Kopia zapasowa programu SharePoint server](https://docs.microsoft.com/azure/backup/backup-mabs-sharepoint-azure-stack)
- [Kopia zapasowa serwera alternatywnego](backup-azure-alternate-dpm-server.md)
