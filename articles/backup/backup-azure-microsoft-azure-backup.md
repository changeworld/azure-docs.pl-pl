---
title: Serwer kopii zapasowej Azure umożliwia tworzenie kopii zapasowej obciążeń na platformie Azure
description: Serwer kopii zapasowej Azure umożliwia ochrony lub utworzyć kopię zapasową obciążeń do portalu Azure.
services: backup
author: PVRK
manager: shivamg
keywords: Serwer kopii zapasowej systemu Azure; chronić obciążenia; Tworzenie kopii zapasowej obciążeń
ms.service: backup
ms.topic: conceptual
ms.date: 5/14/2018
ms.author: adigan
ms.openlocfilehash: 13b3a5f8530c9f91ca23072c320513b8107df061
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34605811"
---
# <a name="preparing-to-back-up-workloads-using-azure-backup-server"></a>Przygotowywanie do tworzenia kopii zapasowych obciążeń przy użyciu usługi Azure Backup Server
> [!div class="op_single_selector"]
> * [Azure Backup Server](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
>
>

W tym artykule opisano sposób przygotowania środowiska do tworzenia kopii zapasowych obciążeń przy użyciu serwera usługi Kopia zapasowa Azure. Serwer kopii zapasowej Azure umożliwia ochronę obciążeń aplikacji, takich jak maszyn wirtualnych funkcji Hyper-V, programu Microsoft SQL Server, SharePoint Server, Microsoft Exchange i klientów systemu Windows z poziomu pojedynczej konsoli.

> [!NOTE]
> Serwer kopii zapasowej systemu Azure mogą teraz chronić maszyny wirtualne VMware i zapewnia lepsze zabezpieczenia możliwości. Zainstaluj produkt zgodnie z objaśnieniem w sekcjach poniżej; zastosowanie aktualizacji 1 i najnowsza wersja agenta kopii zapasowej Azure. Aby dowiedzieć się więcej o tworzeniu kopii zapasowych serwerów VMware serwer kopii zapasowej Azure, zapoznaj się z artykułem [serwer kopii zapasowej Azure używany do tworzenia kopii zapasowej serwera VMware](backup-azure-backup-server-vmware.md). Aby dowiedzieć się więcej na temat funkcji zabezpieczeń, zapoznaj się [dokumentacji funkcji zabezpieczeń kopii zapasowej platformy Azure](backup-azure-security-feature.md).
>
>

Można również chronić infrastrukturę jako obciążeń usługę (IaaS), takich jak maszyny wirtualne na platformie Azure.

> [!NOTE]
> Platforma Azure ma dwa modele wdrażania związane z tworzeniem i pracą z zasobami: [Resource Manager i Model Klasyczny](../azure-resource-manager/resource-manager-deployment-model.md). Ten artykuł zawiera informacje i procedury dotyczące przywracania maszyn wirtualnych wdrożonych przy użyciu modelu Resource Manager.
>
>

Serwer kopii zapasowej systemu Azure dziedziczy wiele funkcji tworzenia kopii zapasowej obciążeń z programu Data Protection Manager (DPM). Ten artykuł zawiera łącza do dokumentacji programu DPM wyjaśnienie niektórych funkcji udostępnionych. Jednak serwer kopia zapasowa Azure udostępnia wiele funkcji programu DPM. Serwer kopii zapasowej systemu Azure nie obsługuje tworzenia kopii na taśmie, ani nie jest zintegrowana z programem System Center.

## <a name="choose-an-installation-platform"></a>Wybierz platformy instalacji
Pierwszy krok w kierunku uruchamianie serwera kopii zapasowej Azure i przeprowadzanie jest można skonfigurować konta systemu Windows Server. Serwer może być w Azure lub lokalnie.

### <a name="using-a-server-in-azure"></a>Przy użyciu serwera w systemie Azure
W przypadku wybierania serwera do uruchamiania serwera usługi Kopia zapasowa Azure, zalecane jest start z obrazem galerii systemu Windows Server 2012 R2 Datacenter lub Windows Server 2016 Datacenter. Artykuł [tworzenie pierwszej maszyny wirtualnej systemu Windows w portalu Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), zapewnia samouczek na temat rozpoczynania pracy z zalecanych maszyny wirtualnej platformy Azure, nawet jeśli nie znasz Azure przed. Zalecane minimalne wymagania dotyczące serwera maszyny wirtualnej (VM) powinny być: A2 standardowo dwa rdzenie i 3.5 GB pamięci RAM.

Ochrona obciążenia za pomocą serwera usługi Kopia zapasowa Azure ma wiele wszystkie szczegóły. Artykuł [zainstalować program DPM jako maszyny wirtualnej platformy Azure](https://technet.microsoft.com/library/jj852163.aspx), pomaga opisano te wszystkie szczegóły. Przed wdrożeniem komputera należy całkowicie odczytane w tym artykule.

### <a name="using-an-on-premises-server"></a>Za pomocą lokalnego serwera
Jeśli nie chcesz uruchomić serwera podstawowego na platformie Azure, można uruchomić serwera na maszynie Wirtualnej funkcji Hyper-V, maszyny Wirtualnej VMware lub hosta fizycznego. Zalecane minimalne wymagania dotyczące sprzętu serwera są dwa rdzenie i 4 GB pamięci RAM. W poniższej tabeli przedstawiono obsługiwane systemy operacyjne:

| System operacyjny | Platforma | SKU |
|:--- | --- |:--- |
| Windows Server 2016 i najnowszych SPs |64-bitowa |Standard, Datacenter, Essentials (MABS v2 lub nowszej) |
| Windows Server 2012 R2 i najnowsze dodatki Service Pack |64-bitowa |Standard, Datacenter, Foundation |
| Windows Server 2012 i najnowsze dodatki Service Pack |64-bitowa |Datacenter, Foundation, Standard |
| Windows Storage Server 2012 R2 i najnowsze dodatki Service Pack |64-bitowa |Standard, Workgroup |
| Windows Storage Server 2012 i najnowsze dodatki Service Pack |64-bitowa |Standard, Workgroup |

Użytkownik może deduplikacja magazynu programu DPM, za pomocą deduplikacji serwera systemu Windows. Dowiedz się więcej na temat [programu DPM i deduplikacji](https://technet.microsoft.com/library/dn891438.aspx) współpracują ze sobą po wdrożeniu na maszynach wirtualnych funkcji Hyper-V.

> [!NOTE]
> Serwer kopii zapasowej systemu Azure zaprojektowanymi do działania na dedykowanym serwerze jednozadaniowych. Nie można zainstalować serwer kopii zapasowej Azure na:
> - Komputer działa jako kontroler domeny
> - Komputer, na którym jest zainstalowana rola serwera aplikacji
> - Komputer, który jest serwerem zarządzania programu System Center Operations Manager
> - Komputer, na którym jest uruchomiony program Exchange Server
> - Komputer, który jest węzłem klastra

Zawsze przyłączyć serwer kopii zapasowej Azure do domeny. Jeśli zamierzasz przenieść serwer do innej domeny, najpierw zainstaluj serwer kopii zapasowej Azure, a następnie dołączyć serwer do nowej domeny. Przeniesienie istniejącej maszyny serwera kopii zapasowej Azure do nowej domeny, po wdrożenia *nieobsługiwane*.

Wyślij dane kopii zapasowej na platformie Azure lub lokalnie Utrzymaj serwera kopii zapasowej Azure musi być zarejestrowany w magazynie usług odzyskiwania.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Konfigurowanie replikacji magazynu
Dla opcji replikacji magazynu można wybrać magazynowanie nadmiarowe geograficznie lub lokalnie. Domyślnie Magazyny usług odzyskiwania używają magazynu geograficznie nadmiarowego. Jeśli ten magazyn jest podstawowy magazyn, pozostaw tę opcję i magazynu do magazynu geograficznie nadmiarowego. Wybierz magazyn lokalnie nadmiarowy, jeśli chcesz skorzystać z tańszej, ale mniej trwałej opcji. Więcej informacji o opcjach magazynu [geograficznie nadmiarowego](../storage/common/storage-redundancy-grs.md) i [lokalnie nadmiarowego](../storage/common/storage-redundancy-lrs.md) można znaleźć w temacie [Azure Storage replication overview](../storage/common/storage-redundancy.md) (Omówienie replikacji usługi Azure Storage).

Aby edytować ustawienia replikacji magazynu:

1. Wybierz magazyn do otwarcia pulpitu nawigacyjnego magazynu i menu Ustawienia. Jeśli **ustawienia** nie zostanie otwarte menu, kliknij przycisk **wszystkie ustawienia** na pulpicie nawigacyjnym magazynu.
2. Na **ustawienia** menu, kliknij przycisk **infrastruktura kopii zapasowej** > **konfiguracji kopii zapasowej** otworzyć **konfiguracji kopii zapasowej**bloku. Na **konfiguracji kopii zapasowej** menu, wybierz opcję replikacji magazynu dla magazynu.

    ![Lista magazynów kopii zapasowych](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

    Po wybraniu opcji magazynu dla swojego magazynu możesz skojarzyć z nim maszynę wirtualną. Aby rozpocząć kojarzenie, należy odnaleźć i zarejestrować maszyny wirtualne Azure.

## <a name="software-package"></a>Pakiet oprogramowania
### <a name="downloading-the-software-package"></a>Pobieranie pakietu oprogramowania
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Jeśli masz już Otwórz magazyn usług odzyskiwania, przejdź do kroku 3. Jeśli nie masz otwarte magazyn usług odzyskiwania, ale znajdują się w portalu Azure, w menu głównym kliknij **Przeglądaj**.

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

    W **wprowadzenie do korzystania z kopii zapasowej** bloku, który zostanie otwarty, **celów tworzenia kopii zapasowej** zostanie wybrana automatycznie.

    ![Kopia zapasowa cele — domyślna otwieranych](./media/backup-azure-microsoft-azure-backup/getting-started.png)

5. W **cel kopii zapasowej** bloku z **gdzie działa Twoje obciążenie** menu, wybierz opcję **lokalnymi**.

    ![lokalne i obciążeń, gdy cele](./media/backup-azure-microsoft-azure-backup/backup-goals-azure-backup-server.png)

    Z **co chcesz utworzyć kopię zapasową?** menu rozwijanego wybierz obciążeń chcesz chronić za pomocą serwera kopii zapasowej Azure, a następnie kliknij **OK**.

    **Wprowadzenie do korzystania z kopii zapasowej** przełączniki kreatora **przygotowanie infrastruktury** opcję, aby utworzyć kopię zapasową obciążeń na platformie Azure.

   > [!NOTE]
   > Jeśli chcesz utworzyć kopię zapasową plików i folderów, zaleca się przy użyciu agenta usługi Kopia zapasowa Azure i postępując zgodnie ze wskazówkami w artykule [Pierwsze spojrzenie: tworzenie kopii zapasowej plików i folderów](backup-try-azure-backup-in-10-mins.md). Jeśli zamierzasz chronić więcej niż pliki i foldery lub zamierzasz rozwinąć na potrzeby ochrony w przyszłości, wybierz tych obciążeń.
   >
   >

    ![Wprowadzenie zmian kreatora wprowadzenie](./media/backup-azure-microsoft-azure-backup/getting-started-prep-infra.png)

6. W **przygotowanie infrastruktury** bloku, który zostanie otwarty, kliknij polecenie **Pobierz** łącza, aby zainstalować serwer kopii zapasowej Azure i pobierania poświadczeń magazynu. Korzystanie z poświadczeń magazynu podczas rejestracji serwera usługi Kopia zapasowa Azure w magazynie usług odzyskiwania. Łącza prowadzą do Centrum pobierania, gdzie można pobrać pakietu oprogramowania.

    ![Przygotowanie infrastruktury dla serwera usługi Kopia zapasowa Azure](./media/backup-azure-microsoft-azure-backup/azure-backup-server-prep-infra.png)

7. Zaznacz wszystkie pliki, a następnie kliknij przycisk **dalej**. Pobieranie wszystkich plików przychodzących na stronie pobierania kopia zapasowa Microsoft Azure i umieść wszystkie pliki w tym samym folderze.

    ![Pobierz center 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    Ponieważ rozmiar pobierania wszystkich plików jednocześnie > sieci 3G, na 10 MB/s pobrać link, który może potrwać do 60 minut do pobrania zakończyć.

### <a name="extracting-the-software-package"></a>Wyodrębnianie pakietu oprogramowania
Po pobraniu wszystkich plików, kliknij przycisk **MicrosoftAzureBackupInstaller.exe**. Spowoduje to uruchomienie **Kreatora instalacji programu Microsoft Azure Backup** do wyodrębnienia plików instalacyjnych do lokalizacji określonej przez użytkownika. Kontynuuj pracę kreatora i kliknij przycisk **wyodrębnić** przycisk, aby rozpocząć proces wyodrębniania.

> [!WARNING]
> Co najmniej 4GB wolnego miejsca jest wymagane do wyodrębnienia plików instalacyjnych.
>
>

![Kreator tworzenia kopii zapasowej Microsoft Azure](./media/backup-azure-microsoft-azure-backup/extract/03.png)

Po zakończeniu procesu wyodrębniania pole wyboru, aby uruchomić świeżo wyodrębnionego *setup.exe* aby rozpocząć instalowanie serwer kopii zapasowej Microsoft Azure i kliknij pozycję **Zakończ** przycisku.

### <a name="installing-the-software-package"></a>Instalowanie pakietu oprogramowania
1. Kliknij przycisk **kopia zapasowa Microsoft Azure** można uruchomić Kreatora instalacji.

    ![Kreator tworzenia kopii zapasowej Microsoft Azure](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)
2. Na ekranie powitalnym kliknij przycisk **dalej** przycisku. Powoduje to przejście do *wymagań wstępnych sprawdza* sekcji. Na tym ekranie kliknij **Sprawdź** ustalenie, jeśli zostały spełnione wymagania wstępne sprzętu i oprogramowania dla serwera usługi Kopia zapasowa Azure. Jeśli pomyślnie zostały spełnione wszystkie wymagania wstępne, pojawi się komunikat wskazujący, że komputer spełnia wymagania. Polecenie **dalej** przycisku.

    ![Sprawdź serwera kopii zapasowej Azure — Zapraszamy i wymagania wstępne](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)
3. Serwer kopii zapasowej Microsoft Azure wymaga programu SQL Server Standard. Ponadto serwer kopii zapasowej Azure pakiet instalacyjny zawiera powiązane potrzebne, jeśli nie chcesz, aby korzystać z własnych SQL odpowiednie pliki binarne programu SQL Server. Przy uruchamianiu nową instalację serwera usługi Kopia zapasowa Azure, należy wybrać opcję **zainstalować nowe wystąpienie programu SQL Server w przypadku takiej konfiguracji** i kliknij przycisk **Sprawdź i zainstaluj** przycisku. Po pomyślnym zainstalowaniu wymagań wstępnych kliknij przycisk **dalej**.

    ![Serwera kopii zapasowej Azure — sprawdzenie programu SQL Server](./media/backup-azure-microsoft-azure-backup/sql/01.png)

    Jeśli wystąpi błąd zgodnie z zaleceniami ponowne uruchomienie komputera, to zrobić i kliknij przycisk **Sprawdź ponownie**.

   > [!NOTE]
   > Serwer kopii zapasowej systemu Azure nie będzie działać przy użyciu zdalnego wystąpienia programu SQL Server. Wystąpienie używane przez serwer kopii zapasowej Azure musi być kontem lokalnym.
   >
   >
4. Podaj lokalizację instalacji plików serwera kopia zapasowa Microsoft Azure i kliknij przycisk **dalej**.

    ![PreReq2 kopia zapasowa Microsoft Azure](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    Pliki tymczasowe lokalizacji jest wymagana do kopii zapasowej Azure. Upewnij się, że pliki tymczasowe lokalizacja jest co najmniej 5% danych planowanych do wykonania kopii zapasowej do chmury. Do ochrony dysku oddzielnych dyskach należy skonfigurować po zakończeniu instalacji. Aby uzyskać więcej informacji na temat pul magazynów, zobacz [Konfigurowanie pul magazynów i dysku magazynu](https://technet.microsoft.com/library/hh758075.aspx).
5. Podaj silne hasło dla kont użytkowników lokalnych z ograniczeniami, a następnie kliknij przycisk **dalej**.

    ![PreReq2 kopia zapasowa Microsoft Azure](./media/backup-azure-microsoft-azure-backup/security-screen.png)
6. Wybierz, czy chcesz użyć *Microsoft Update* Wyszukaj aktualizacje, a następnie kliknij przycisk **dalej**.

   > [!NOTE]
   > Zaleca się o przekierowania do Microsoft Update zapewnia bezpieczeństwo i ważne aktualizacje systemu Windows i innych produktów, takich jak Microsoft Azure Utwórz kopię zapasową serwera usługi Windows Update.
   >
   >

    ![PreReq2 kopia zapasowa Microsoft Azure](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)
7. Przegląd *Podsumowanie ustawień* i kliknij przycisk **zainstalować**.

    ![PreReq2 kopia zapasowa Microsoft Azure](./media/backup-azure-microsoft-azure-backup/summary-screen.png)
8. Instalacja odbywa się w fazach. W pierwszej fazie agenta usług odzyskiwania Microsoft Azure jest zainstalowany na serwerze. Kreator sprawdza również połączenie z Internetem. Jeśli dostępny jest połączenie z Internetem można kontynuować instalacji, jeśli nie, należy podać szczegóły serwera proxy, aby nawiązać połączenie z Internetem.

    Następnym krokiem jest skonfigurowanie agenta usług odzyskiwania Microsoft Azure. W ramach konfiguracji należy podać swoje poświadczenia magazynu, aby zarejestrować komputer w magazynie usług odzyskiwania. Zostanie również podać hasło do szyfrowania/odszyfrowywania danych przesyłanych między Azure i lokalnej. Można automatycznie wygenerować hasło lub podać własne minimalna hasło 16 znaków. Kontynuować pracę kreatora, dopóki agent został skonfigurowany.

    ![Azure PreReq2 Server kopii zapasowej](./media/backup-azure-microsoft-azure-backup/mars/04.png)
9. Po pomyślnym zakończeniu rejestracji serwera usługi Kopia zapasowa Microsoft Azure, Kreatora konfiguracji ogólnej przechodzą do instalacji i konfiguracji programu SQL Server i składniki serwera kopii zapasowej Azure. Po zakończeniu instalacji składników programu SQL Server, są zainstalowane składniki serwera kopii zapasowej Azure.

    ![Azure Backup Server](./media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

Po ukończeniu kroku instalacji produktu ikony pulpitu będzie utworzono również. Wystarczy dwukrotnie kliknąć ikonę, aby uruchomić produktu.

### <a name="add-backup-storage"></a>Dodawanie magazynu kopii zapasowej
Pierwszej kopii zapasowej jest przechowywany w magazynie dołączonym do serwera kopii zapasowej Azure. Aby uzyskać więcej informacji na temat dodawania dysków, zobacz [Konfigurowanie pul magazynów i dysku magazynu](https://technet.microsoft.com/library/hh758075.aspx).

> [!NOTE]
> Należy dodać magazyn kopii zapasowych, nawet wtedy, gdy planujesz do wysyłania danych do platformy Azure. W bieżącym architektury serwera kopii zapasowej Azure przechowuje magazynu kopii zapasowych Azure *drugi* kopię danych podczas Magazyn lokalny zawiera pierwszy (i obowiązkowe) kopii zapasowej.
>
>

## <a name="network-connectivity"></a>Połączenie sieciowe
Serwer kopii zapasowej systemu Azure wymaga połączenia z usługą kopia zapasowa Azure produktu działało poprawnie. Aby zweryfikować, czy komputer ma łączność z platformy Azure, użyj ```Get-DPMCloudConnection``` polecenia cmdlet w konsoli programu PowerShell serwera kopii zapasowej Azure. Jeśli dane wyjściowe polecenia cmdlet ma wartość PRAWDA, a następnie istnieje połączenie, #else nie ma żadnej łączności.

W tym samym czasie subskrypcji platformy Azure musi być w dobrej kondycji. Aby sprawdzić stan subskrypcji i zarządzania nim, zaloguj się do [portal subskrypcji](https://account.windowsazure.com/Subscriptions).

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
Jeśli zapora lub serwer proxy, który uniemożliwia dostęp do platformy Azure, musisz dozwolonych następujące adresy domeny w profilu zapory/serwera proxy:

* www.msftncsi.com
* \*.Microsoft.com
* \*.WindowsAzure.com
* \*.microsoftonline.com
* \*.windows.net

Gdy łączność Azure została przywrócona do komputera serwera usługi Kopia zapasowa Azure, operacje, które mogą być wykonywane są określane przez stan subskrypcji platformy Azure. Powyżej tabela zawiera szczegółowe informacje o operacjach dozwolone, gdy komputer jest "połączony".

### <a name="handling-subscription-states"></a>Obsługa stany subskrypcji
Jest to możliwe, należy subskrypcji platformy Azure z *wygasłe* lub *Deprovisioned* stan *Active* stanu. Jednak ma wpływ na niektóre na zachowanie produktu podczas stan nie jest *Active*:

* A *Deprovisioned* subskrypcji utraci funkcje dla okresu, w którym jest ona anulowana. Na włączanie *Active*, funkcje produktu wykonywania kopii zapasowej i przywracania jest przywrócona. Można również pobrać dane kopii zapasowej na dysku lokalnym Jeśli znajdowały się od okresu przechowywania wystarczająco duża. Jednak dane kopii zapasowej na platformie Azure po nieodwracalnie wprowadza subskrypcji *Deprovisioned* stanu.
* *Wygasłe* subskrypcji tylko utraci funkcje dla dopóki umożliwiono jego *Active* ponownie. Wszelkich kopii zapasowych zaplanowanych dla okresu subskrypcji została *wygasłe* nie zostaną uruchomione.

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Jeśli kopia zapasowa Microsoft Azure serwera zakończy się niepowodzeniem z błędami fazy instalacji (lub kopii zapasowej lub przywracania), zapoznaj się to [dokumentu kody błędów](https://support.microsoft.com/kb/3041338) Aby uzyskać więcej informacji.
Można także odwoływać się do [— często zadawane pytania dotyczące usługi Kopia zapasowa Azure](backup-azure-backup-faq.md)

## <a name="next-steps"></a>Kolejne kroki
Aby wyświetlić szczegółowe informacje dotyczące [przygotowywanie środowiska dla programu DPM](https://technet.microsoft.com/library/hh758176.aspx) w witrynie Microsoft TechNet. Zawiera informacje o obsługiwanych konfiguracjach, na których można wdrożyć i używany serwer kopii zapasowej Azure.

Skorzystaj z tych artykułów, aby lepiej zrozumieć ochrony obciążenia przy użyciu serwera kopia zapasowa Microsoft Azure.

* [Kopia zapasowa programu SQL Server](backup-azure-backup-sql.md)
* [Kopia zapasowa programu SharePoint server](backup-azure-backup-sharepoint.md)
* [Alternatywny serwer kopii zapasowej](backup-azure-alternate-dpm-server.md)
