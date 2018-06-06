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
ms.openlocfilehash: c79ca93138961e294f03e283466dd66250472dae
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34801742"
---
# <a name="install-azure-backup-server-on-azure-stack"></a>Zainstaluj serwer kopii zapasowej systemu Azure w stosie Azure

W tym artykule opisano sposób instalowania serwera kopii zapasowej Azure na stosie Azure. Serwer kopii zapasowej Azure umożliwia ochronę obciążeń aplikacji uruchomionych w stosie Azure z poziomu pojedynczej konsoli.

> [!NOTE]
> Aby dowiedzieć się więcej na temat funkcji zabezpieczeń, zapoznaj się [dokumentacji funkcji Kopia zapasowa Azure zabezpieczeń](backup-azure-security-feature.md).
>

Można również chronić infrastrukturę jako obciążeń usługę (IaaS), takich jak maszyny wirtualne na platformie Azure.

Pierwszy krok w kierunku uruchamianie serwera kopii zapasowej Azure i przeprowadzanie jest aby skonfigurować maszynę wirtualną w stosie Azure.

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

Po utworzeniu magazynu usług odzyskiwania, użyj menu wprowadzenie w magazynie usług odzyskiwania pobrać Instalatora serwer kopii zapasowej Azure do maszyny wirtualnej Azure stosu. Poniższe kroki miejsce w Twojej subskrypcji platformy Azure.

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

    Ponieważ rozmiar pobierania wszystkich plików jest > sieci 3G, przy 10-MB/s Pobierz link, który może potrwać do 60 minut do pobrania zakończyć. Pliki zostaną pobrane do lokalizacji pobierania.

## <a name="extract-azure-backup-server-install-files"></a>Wyodrębnij pliki instalacji serwera usługi Kopia zapasowa Azure

Po pobraniu wszystkich plików z maszyną wirtualną, przejdź do lokalizacji pobierania.

![Pobierz center 1](./media/backup-mabs-install-azure-stack/download-mabs-installer.png)

1. Aby rozpocząć instalację, z listy pobranych plików, kliknij przycisk **MicrosoftAzureBackupserverInstaller.exe**.

    > [!WARNING]
    > Co najmniej 4GB wolnego miejsca jest wymagane do wyodrębnienia plików instalacyjnych.
    >

2. W Instalatorze serwer kopii zapasowej Azure kliknij **dalej** Aby uruchomić kreatora.

    ![Kreator tworzenia kopii zapasowej Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wiz-1.png)

3. Wybierz lokalizację zainstalować serwer kopii zapasowej Azure i kliknij przycisk **dalej**.

   ![Kreator tworzenia kopii zapasowej Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-select-destination-1.png)

4. Sprawdź lokalizację instalacji, a następnie kliknij przycisk **wyodrębnić**.

   ![Kreator tworzenia kopii zapasowej Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-extract-2.png)

5. Instalator wyodrębnia pliki i przygotowuje proces instalacji.

   ![Kreator tworzenia kopii zapasowej Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-install-3.png)

6. Po zakończeniu procesu wyodrębniania, kliknij przycisk **Zakończ** można uruchomić *setup.exe*. Setup.exe instaluje serwer kopii zapasowej Microsoft Azure.

   ![Kreator tworzenia kopii zapasowej Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-finish-4.png)

## <a name="install-the-software-package"></a>Zainstaluj pakiet oprogramowania

W poprzednim kroku kliknięto **Zakończ** aby zakończyć fazy wyodrębniania, uruchom Kreatora instalacji serwera usługi Kopia zapasowa Azure.

![Kreator tworzenia kopii zapasowej Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5.png)

Serwer kopii zapasowej systemu Azure udostępnia kodu z programu Data Protection Manager. Zostanie wyświetlone odwołania do programu Data Protection Manager i programu DPM w Instalatorze serwer kopii zapasowej Azure. Jeśli serwer usługi Kopia zapasowa Azure i programu Data Protection Manager są oddzielne produkty, odwołania lub narzędzia, których Data Protection Manager lub programu DPM, dotyczą serwer kopii zapasowej Azure.

1. Aby uruchomić Kreatora instalacji, kliknij przycisk **kopia zapasowa Microsoft Azure**.

   ![Kreator tworzenia kopii zapasowej Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5b.png)

2. Na ekranie powitalnym kliknij przycisk **dalej**.

    ![Sprawdź serwera kopii zapasowej Azure — Zapraszamy i wymagania wstępne](./media/backup-mabs-install-azure-stack/mabs-install-wizard-setup-6.png)

3. Na *wymagań wstępnych sprawdza* kliknij **Sprawdź** ustalenie, jeśli zostały spełnione wymagania wstępne sprzętu i oprogramowania dla serwera usługi Kopia zapasowa Azure.

    ![Sprawdź serwera kopii zapasowej Azure — Zapraszamy i wymagania wstępne](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-7.png)

    Jeśli środowisko zawiera wymagania wstępne, pojawi się komunikat wskazujący, że komputer spełnia wymagania. Kliknij przycisk **Dalej**.

    ![Serwera kopii zapasowej Azure — Sprawdzanie wymagań wstępnych Zakończono pomyślnie](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-passed-8.png)

4. Serwer kopii zapasowej Microsoft Azure wymaga programu SQL Server. Pakiet instalacyjny serwera usługi Kopia zapasowa Azure zawiera powiązane potrzebne, jeśli nie chcesz korzystać z własnych SQL odpowiednie pliki binarne programu SQL Server. Wybór zalecane jest, aby dodać nowe wystąpienie programu SQL Server Instalator. Aby zapewnić używanie środowiska programu SQL Server, kliknij przycisk **Sprawdź i zainstaluj**.

   > [!NOTE]
   > Serwer kopii zapasowej systemu Azure nie będzie działać przy użyciu zdalnego wystąpienia programu SQL Server. Wystąpienie używane przez serwer kopii zapasowej Azure musi być kontem lokalnym.
   >

    ![Sprawdź serwera kopii zapasowej Azure — Zapraszamy i wymagania wstępne](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-install-9.png)

    Po sprawdzeniu, czy komputer ma niezbędne wymagania wstępne dotyczące instalacji serwera usługi Kopia zapasowa Azure, kliknij **dalej**.

    ![Sprawdź serwera kopii zapasowej Azure — Zapraszamy i wymagania wstępne](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-ready-10.png)

    Jeśli wystąpi błąd zgodnie z zaleceniami, aby ponownie uruchomić komputer, to zrobić, kliknij przycisk Uruchom ponownie Instalatora, w tym ekranie **Sprawdź ponownie**.

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

### <a name="add-backup-storage"></a>Dodawanie magazynu kopii zapasowej

Pierwszej kopii zapasowej jest przechowywany w magazynie dołączonym do serwera kopii zapasowej Azure. Aby uzyskać więcej informacji na temat dodawania dysków, zobacz [Konfigurowanie pul magazynów i dysku magazynu](https://technet.microsoft.com/library/hh758075.aspx).

> [!NOTE]
> Należy dodać magazyn kopii zapasowych, nawet wtedy, gdy planujesz do wysyłania danych do platformy Azure. W bieżącym architektury serwera kopii zapasowej Azure przechowuje magazynu kopii zapasowych Azure *drugi* kopię danych podczas Magazyn lokalny zawiera pierwszy (i obowiązkowe) kopii zapasowej.
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

Artykuł [przygotowywanie środowiska dla programu DPM](https://technet.microsoft.com/library/hh758176.aspx), zawiera informacje o obsługiwanych konfiguracjach serwera usługi Kopia zapasowa Azure.

Aby lepiej zrozumieć ochrony obciążenia w programie Microsoft Azure Utwórz kopię zapasową serwera można skorzystaj z poniższych artykułów.

- [Kopia zapasowa programu SQL Server](backup-azure-backup-sql.md)
- [Kopia zapasowa programu SharePoint server](backup-azure-backup-sharepoint.md)
- [Alternatywny serwer kopii zapasowej](backup-azure-alternate-dpm-server.md)
