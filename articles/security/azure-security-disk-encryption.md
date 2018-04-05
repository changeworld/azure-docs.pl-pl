---
title: Szyfrowanie dysków Azure dla systemu Windows i maszyn wirtualnych systemu Linux IaaS | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie programu Microsoft Azure dysku szyfrowanie dla systemu Windows i maszyn wirtualnych systemu Linux IaaS.
services: security
documentationcenter: na
author: DevTiw
manager: avibm
editor: barclayn
ms.assetid: d3fac8bb-4829-405e-8701-fa7229fb1725
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/13/2018
ms.author: devtiw;ejarvi;mayank88mahajan;vermashi;sudhakarareddyevuri;aravindthoram
ms.openlocfilehash: 5219ebc22e56ad4b5cdfc125f7fa5882c61adb9f
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="azure-disk-encryption-for-windows-and-linux-iaas-vms"></a>Szyfrowanie dysków Azure dla systemu Windows i maszyn wirtualnych systemu Linux IaaS
Microsoft Azure jest silnie zobowiązane do zapewnienia prywatności danych, suwerenności danych i umożliwia sterowanie platformy Azure hostowanej danych za pomocą wielu zaawansowanych technologii szyfrowania, sterowania i zarządzania kluczami szyfrowania, inspekcji i kontroli dostępu do danych. Klienci Azure zapewnia elastyczność wyboru rozwiązania, które będzie najlepiej odpowiadać ich potrzeb biznesowych. W tym dokumencie firma Microsoft podstawowe informacje na temat nowego rozwiązania technologii "Szyfrowania dysków Azure dla systemu Windows i Linux IaaS maszyny Wirtualnej na" Aby chronić i ochrony danych w celu spełnienia organizacji bezpieczeństwa i zgodności zobowiązań. Papieru zapewnia napotka szczegółowe wskazówki dotyczące sposobu używania funkcji szyfrowania dysków Azure w tym obsługiwane scenariusze i użytkownika.

> [!NOTE]
> Zastosowanie niektórych zaleceń zamieszczonych może zwiększyć danych, sieci i użycia zasobów obliczeniowych, co powoduje dodatkowych kosztów licencji lub subskrypcji.

## <a name="overview"></a>Przegląd
Szyfrowanie dysków Azure jest nową możliwość, która pomaga szyfrowania dysków maszyny wirtualnej systemu Windows i Linux IaaS. Szyfrowanie dysków Azure korzysta ze standardu branżowego [funkcji BitLocker](https://technet.microsoft.com/library/cc732774.aspx) funkcji systemu Windows i [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) funkcji systemu Linux w celu zapewnienia szyfrowania woluminów systemu operacyjnego i dysków z danymi. Rozwiązanie jest zintegrowany z [usługi Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) ułatwiają kontrolowanie i zarządzanie nimi klucze szyfrowania dysku i kluczy tajnych w magazynie kluczy subskrypcji. Rozwiązanie zapewnia również, że wszystkie dane na dyskach maszyny wirtualnej są szyfrowane, gdy w magazynie Azure.

Szyfrowanie dysków Azure dla systemu Windows i maszyn wirtualnych systemu Linux IaaS, jest teraz w **ogólnej dostępności** we wszystkich regionach publicznej platformy Azure i regiony AzureGov standardowe maszyn wirtualnych i maszyn wirtualnych z magazyn w warstwie premium.

### <a name="encryption-scenarios"></a>Scenariusze szyfrowania
Rozwiązanie szyfrowania dysków Azure obsługuje następujących scenariuszy:

* Włącz szyfrowanie dla nowych maszyn wirtualnych IaaS utworzone na podstawie wstępnie zaszyfrowany dysk VHD i kluczy szyfrowania
* Włącz szyfrowanie dla nowych maszyn wirtualnych IaaS utworzone z obrazów obsługiwane galerii Azure
* Włącz szyfrowanie dla istniejących maszyn wirtualnych IaaS działające na platformie Azure
* Wyłącz szyfrowanie na maszynach wirtualnych IaaS systemu Windows
* Wyłącz szyfrowanie dysków danych dla maszyn wirtualnych systemu Linux IaaS
* Włącz szyfrowanie dysków zarządzanych maszyn wirtualnych
* Aktualizowanie ustawień szyfrowania istniejących premium zaszyfrowane i inne niż premium magazynu maszyny Wirtualnej
* Kopia zapasowa i przywracanie zaszyfrowanych maszyny wirtualne

Rozwiązanie obsługuje następujące scenariusze dla maszyn wirtualnych IaaS, jeśli są włączone w systemie Microsoft Azure:

* Integracja z usługi Azure Key Vault
* Maszyny wirtualne warstwy standardowa: [A, D DS, G, GS, F i itp szeregów maszyn wirtualnych IaaS](https://azure.microsoft.com/pricing/details/virtual-machines/)
* Włącz szyfrowanie dla systemu Windows i maszyn wirtualnych systemu Linux IaaS i maszyn wirtualnych dysków zarządzanych z obsługiwanych obrazów w galerii Azure
* Wyłącz szyfrowanie dysków systemu operacyjnego i danych dla maszyn wirtualnych IaaS systemu Windows i dysków zarządzanych maszyn wirtualnych
* Wyłącz szyfrowanie dysków danych dla maszyn wirtualnych systemu Linux IaaS i dysków zarządzanych maszyn wirtualnych
* Włącz szyfrowanie dla maszyn wirtualnych IaaS systemem kliencki system operacyjny Windows
* Włącz szyfrowanie dla woluminów przy użyciu ścieżki instalacji
* Włącz szyfrowanie dla maszyn wirtualnych systemu Linux skonfigurowany z dysku przy użyciu mdadm zastosowanie rozkładania (RAID)
* Włącz szyfrowanie dla maszyn wirtualnych systemu Linux przy użyciu LVM dla dysków z danymi
* Włącz szyfrowanie dla systemu Linux LVM 7.3 dla systemu operacyjnego i dysków z danymi 
* Włącz szyfrowanie dla maszyn wirtualnych systemu Windows skonfigurowane z funkcją miejsca do magazynowania
* Aktualizowanie ustawień szyfrowania istniejących premium zaszyfrowane i inne niż premium magazynu maszyny Wirtualnej
* Kopia zapasowa i przywracanie zaszyfrowanych maszyn wirtualnych, dla KEK nie i scenariuszy KEK (KEK - klucza szyfrowania kluczy)
* Wszystkie publiczne Azure i AzureGov regiony są obsługiwane.

Rozwiązanie nie obsługuje następujące scenariusze, funkcje i technologie:

* Maszyny wirtualne IaaS warstwa podstawowa
* Wyłączenie szyfrowania na dysku systemu operacyjnego dla maszyn wirtualnych systemu Linux IaaS
* Wyłączenie szyfrowania na dysku danych, jeśli dysk systemu operacyjnego jest szyfrowany dla maszyn wirtualnych systemu Linux Iaas
* Maszyny wirtualne IaaS, które są tworzone za pomocą klasycznego metodę tworzenia maszyny Wirtualnej
* Włącz szyfrowanie dla systemu Windows i maszyn wirtualnych systemu Linux IaaS niestandardowych obrazów klienta nie jest obsługiwana.
* Integracja z lokalnej usługi zarządzania kluczami
* Usługa pliki Azure (udostępnionego systemu plików), sieciowego systemu plików (NFS), dynamiczne woluminy i maszyn wirtualnych systemu Windows, które są skonfigurowane przy użyciu systemów opartych na oprogramowaniu RAID

### <a name="encryption-features"></a>Funkcji szyfrowania
Po włączeniu i wdrożeniu szyfrowania dysków Azure dla maszyn wirtualnych IaaS platformy Azure, następujące funkcje są włączone, w zależności od Podana konfiguracja:

* Szyfrowania woluminu systemu operacyjnego, aby chronić wolumin rozruchowy magazynowane w magazynie
* Szyfrowanie ilości danych, aby chronić woluminy danych przechowywanych w magazynie
* Wyłączenie szyfrowania dysków systemu operacyjnego i danych dla maszyn wirtualnych IaaS systemu Windows
* Wyłączenie szyfrowania danych dyski dla maszyn wirtualnych systemu Linux IaaS (tylko wtedy, gdy nie jest zaszyfrowany to dysk systemu operacyjnego)
* Ochrona kluczy szyfrowania i kluczy tajnych w magazynie kluczy subskrypcji
* Raportowanie stanu szyfrowania zaszyfrowanej maszyn wirtualnych IaaS
* Usuwanie ustawień konfiguracji szyfrowania dysku od maszyny wirtualnej IaaS
* Kopia zapasowa i przywracanie zaszyfrowanych maszyn wirtualnych za pomocą usługi Kopia zapasowa Azure

Szyfrowanie dysków Azure maszyny Wirtualne IaaS dla systemu Windows i Linux rozwiązanie obejmuje:

* Rozszerzenie szyfrowania dysku dla systemu Windows.
* Rozszerzenie szyfrowania dysku dla systemu Linux.
* Szyfrowanie dysków poleceń cmdlet programu PowerShell.
* Dysk szyfrowania poleceń cmdlet Azure interfejsu wiersza polecenia (CLI).
* Szyfrowanie dysków szablonów usługi Azure Resource Manager.

Rozwiązanie szyfrowania dysków Azure jest obsługiwane na maszynach wirtualnych IaaS, które korzystają z systemu Windows lub systemu operacyjnego Linux. Aby uzyskać więcej informacji o obsługiwanych systemach operacyjnych zobacz sekcję "Wymagania wstępne".

> [!NOTE]
> Brak bez dodatkowych opłat szyfrowanie dysków maszyny Wirtualnej przy użyciu szyfrowania dysków Azure.

### <a name="value-proposition"></a>Opis korzyści
Po zastosowaniu rozwiązania zarządzania szyfrowania dysków Azure mogą spełniać następujące wymagania biznesowe:

* Maszyny wirtualne IaaS są zabezpieczone magazynowane, ponieważ można użyć technologii szyfrowania standardowych do organizacji wymagania dotyczące zabezpieczeń i zgodności.
* Maszyny wirtualne IaaS rozruchu w obszarze klucze kontrolowane przez klienta i zasad, a można inspekcji ich użycia w magazynie kluczy.

### <a name="encryption-workflow"></a>Szyfrowanie przepływu pracy
Aby włączyć szyfrowanie dysku dla systemu Windows i maszyn wirtualnych systemu Linux, wykonaj następujące czynności:

1. Wybierz scenariusz szyfrowania spośród powyższych scenariuszy szyfrowania.
2. Zgódź się na włączenie szyfrowania za pomocą Menedżera zasobów szyfrowania dysków Azure szablonu, poleceń cmdlet programu PowerShell lub polecenia interfejsu wiersza polecenia na dysku, a następnie określ konfiguracji szyfrowania.

   * W scenariuszu wirtualnego dysku twardego szyfrowane klienta przekazać zaszyfrowanego dysku VHD do konta magazynu i materiału klucza szyfrowania magazynu kluczy. Następnie podaj w konfiguracji szyfrowania, aby włączyć szyfrowanie na nowej maszyny Wirtualnej IaaS.
   * W przypadku nowych maszyn wirtualnych, które są tworzone z witryny Marketplace, a istniejące maszyny wirtualne, które zostały już uruchomione na platformie Azure Podaj konfiguracji szyfrowania, aby włączyć szyfrowanie na Maszynie wirtualnej IaaS.

3. Udziel dostępu do platformy Azure do odczytu z magazynu kluczy, aby włączyć szyfrowanie na Maszynie wirtualnej IaaS materiału klucza szyfrowania (klucze szyfrowania funkcji BitLocker dla systemów Windows) i hasło dla systemu Linux.

4. Podaj tożsamość aplikacji usługi Azure Active Directory (Azure AD) można zapisać klucza szyfrowania materiały do magazynu kluczy. Dzięki temu włącza szyfrowanie na maszynie Wirtualnej IaaS w scenariuszach wspomnianego w kroku 2.

5. Azure aktualizuje modelu usług maszyny Wirtualnej przy użyciu szyfrowania i konfiguracji magazynu kluczy i konfiguruje zaszyfrowanych maszyny Wirtualnej.

 ![Ochrona przed złośliwym kodem zapewniana przez Microsoft na platformie Azure](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

### <a name="decryption-workflow"></a>Odszyfrowywanie przepływu pracy
Aby wyłączyć szyfrowanie dysku dla maszyn wirtualnych IaaS, wykonaj następujące czynności ogólne:

1. Wybierz umożliwia wyłączenie szyfrowania (odszyfrowywania) na uruchomionych maszyn wirtualnych IaaS na platformie Azure za pośrednictwem szablon Menedżera zasobów szyfrowania dysków Azure lub poleceń cmdlet programu PowerShell i określić konfigurację odszyfrowywania.

 Ten krok powoduje wyłączenie szyfrowania systemu operacyjnego ilość danych i/lub na Maszynie wirtualnej uruchomionej IaaS systemu Windows. Jednakże jak wspomniano w poprzedniej sekcji, wyłączenie szyfrowania dysku systemu operacyjnego Linux nie jest obsługiwane. Krok odszyfrowywania jest dozwolone tylko w przypadku dysków z danymi na maszynach wirtualnych systemu Linux, tak długo, jak dysk systemu operacyjnego nie jest zaszyfrowany.
2. Azure aktualizacji modelu usług maszyny Wirtualnej, a IaaS maszyny Wirtualnej jest oznaczony jako odszyfrowany. Zawartość maszyny Wirtualnej nie są szyfrowane, gdy.

> [!NOTE]
> Operacja disable szyfrowania nie powoduje usunięcia magazynu kluczy i materiału klucza szyfrowania (klucze szyfrowania funkcji BitLocker dla systemów Windows) lub hasło dla systemu Linux.
 > Wyłączenie szyfrowania dysku systemu operacyjnego Linux nie jest obsługiwane. Krok odszyfrowywania jest dozwolone tylko w przypadku dysków z danymi na maszynach wirtualnych systemu Linux.
Wyłączenie szyfrowania dysku danych dla systemu Linux nie jest obsługiwane, jeśli dysk systemu operacyjnego jest zaszyfrowany.

## <a name="prerequisites"></a>Wymagania wstępne
Przed włączeniem szyfrowania dysków Azure na maszynach wirtualnych Azure IaaS dla obsługiwane scenariusze, które zostały omówione w sekcji "Przegląd", zobacz następujące wymagania wstępne:

* Musi mieć prawidłową aktywną subskrypcją platformy Azure na tworzenie zasobów Azure w obsługiwanych regionów.
* Szyfrowanie dysków Azure jest obsługiwane w następujących wersjach systemu Windows Server: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 i Windows Server 2016.
* Szyfrowanie dysków Azure jest obsługiwane w następujących wersjach klientów systemu Windows: Windows 10 i Windows 8 klienta.

> [!NOTE]
> Dla systemu Windows Server 2008 R2 musisz mieć zainstalowany przed włączeniem szyfrowania na platformie Azure programu .NET Framework 4.5. Można zainstalować z witryny Windows Update, instalując opcjonalną aktualizację programu Microsoft .NET Framework 4.5.2 dla systemów opartych na x64 systemu Windows Server 2008 R2 ([KB2901983](https://support.microsoft.com/kb/2901983)).

* Szyfrowanie dysków Azure jest tylko obsługiwana w określonych galerii Azure na podstawie dystrybucje systemu Linux serwera i wersje.  Aby uzyskać listę aktualnie obsługiwanych wersji, zapoznaj się [FAQ szyfrowania dysków Azure](https://docs.microsoft.com/en-us/azure/security/azure-security-disk-encryption-faq).

* Szyfrowanie dysków Azure wymaga, aby magazyn kluczy i maszyny wirtualne znajdowały się w tym samym regionie Azure i subskrypcji.

> [!NOTE]
> Konfigurowanie zasobów w oddzielnych regionach powoduje to niepowodzenie w włączenie funkcji szyfrowania dysków Azure.

* Do instalowania i konfigurowania magazynu kluczy do szyfrowania dysków Azure, zobacz sekcję **ustawiony w górę i konfigurowania magazynu kluczy szyfrowania dysków Azure** w *wymagania wstępne* sekcji tego artykułu.
* Do instalowania i konfigurowania aplikacji usługi Azure AD w usłudze Azure Active directory szyfrowanie dysków Azure, zobacz sekcję **Konfigurowanie aplikacji usługi Azure AD w usłudze Azure Active Directory** w *wymagania wstępne* sekcji w tym artykule.
* Do instalowania i konfigurowania zasad dostępu do magazynu kluczy dla aplikacji usługi Azure AD, zobacz sekcję **skonfigurować zasady dostępu do magazynu kluczy dla aplikacji usługi Azure AD** w *wymagania wstępne* sekcji tego artykułu.
* Aby przygotować wstępnie zaszyfrowanego dysku VHD systemu Windows, zobacz sekcję **przygotować wstępnie zaszyfrowanego dysku VHD systemu Windows** w *dodatku*.
* Aby przygotować wstępnie zaszyfrowanego dysku VHD systemu Linux, zobacz sekcję **przygotować wstępnie zaszyfrowanego dysku VHD systemu Linux** w *dodatku*.
* Platforma Azure wymaga dostępu do kluczy szyfrowania lub kluczy tajnych w magazynie kluczy, aby były dostępne dla maszyny wirtualnej podczas rozruchu i odszyfrowuje wolumin systemu operacyjnego maszyny wirtualnej. Aby udzielić uprawnień do platformy Azure, należy ustawić **EnabledForDiskEncryption** właściwość w magazynie kluczy. Aby uzyskać więcej informacji, zobacz **ustawiony w górę i konfigurowania magazynu kluczy szyfrowania dysków Azure** w dodatku.
* Klucz tajny magazynu kluczy, a adresy URL KEK musi być numerów wersji. Azure wymusza to ograniczenie wersji. Nieprawidłowy klucz tajny i KEK adresów URL zobacz następujące przykłady:

  * Przykład prawidłowego adresu URL tajny:   *https://contosovault.vault.azure.net/secrets/BitLockerEncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Przykład prawidłowy adres URL KEK:   *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Szyfrowanie dysków Azure nie obsługuje określania numery portów, jako część adresy URL KEK i kluczy tajnych w magazynie kluczy. Przykłady adresów URL magazynu kluczy obsługiwanych i nieobsługiwanych zobacz następujące tematy:

  * Adres URL nie do przyjęcia magazynu kluczy  *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Adres URL dopuszczalne magazynu kluczy:   *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Aby włączyć szyfrowanie dysków Azure funkcji, maszyny wirtualne IaaS musi spełniać następujące wymagania dotyczące konfiguracji punktu końcowego sieci:
  * Aby uzyskać token, aby połączyć się z magazynu kluczy, musi być można nawiązać połączenia z punktem końcowym usługi Azure Active Directory, maszyn wirtualnych IaaS \[login.microsoftonline.com\].
  * Do zapisu kluczy szyfrowania magazynu kluczy, maszyn wirtualnych IaaS musi mieć możliwość połączenia z punktem końcowym magazynu kluczy.
  * Maszyn wirtualnych IaaS musi mieć możliwość nawiązania połączenia usługi Azure storage punktu końcowego, który obsługuje repozytorium rozszerzenie Azure i konto magazynu platformy Azure obsługującym pliki wirtualnego dysku twardego.

  > [!NOTE]
  > Jeśli zasady zabezpieczeń ogranicza dostęp do Internetu z maszyn wirtualnych platformy Azure, można rozwiązać poprzedni identyfikator URI i skonfigurować określona Reguła zezwalająca na łączności wychodzącego adresy IP.
  >
  >Konfigurowanie i dostępu do usługi Azure Key Vault za (zaporyhttps://docs.microsoft.com/azure/key-vault/key-vault-access-behind-firewall)

* Użyj najnowszej wersji zestawu SDK programu PowerShell Azure w wersji, aby skonfigurować szyfrowanie dysków Azure. Pobierz najnowszą wersję [wersji programu Azure PowerShell](https://github.com/Azure/azure-powershell/releases)

 > [!NOTE]
  > Szyfrowanie dysków Azure nie jest obsługiwana w [zestawu SDK usługi Azure PowerShell w wersji 1.1.0](https://github.com/Azure/azure-powershell/releases/tag/v1.1.0-January2016). Jeśli wyświetlany błąd związany z przy użyciu programu Azure PowerShell 1.1.0, zobacz [Azure dysku szyfrowania błędów związanych z programu Azure PowerShell 1.1.0](http://blogs.msdn.com/b/azuresecurity/archive/2016/02/10/azure-disk-encryption-error-related-to-azure-powershell-1-1-0.aspx).

* Aby uruchomić polecenia wiersza polecenia platformy Azure i skojarzyć go z subskrypcją platformy Azure, należy najpierw zainstalować wiersza polecenia platformy Azure:
  * Aby zainstalować wiersza polecenia platformy Azure i skojarzyć go z subskrypcją platformy Azure, zobacz [jak instalowanie i Konfigurowanie interfejsu wiersza polecenia Azure](../cli-install-nodejs.md).
  * Aby użyć wiersza polecenia platformy Azure dla komputerów Mac, Linux i Windows za pomocą Menedżera zasobów Azure, zobacz [polecenia wiersza polecenia platformy Azure w trybie Menedżera zasobów](../virtual-machines/azure-cli-arm-commands.md).

* Podczas szyfrowania dysków zarządzanych, jest wymagane wstępnie wymaganego do wykonania migawki dysków zarządzanych lub kopii zapasowej dysku poza szyfrowania dysków Azure przed włączeniem szyfrowania.  Bez kopii zapasowej w miejscu wszelkie nieoczekiwany błąd podczas szyfrowania może spowodować dysku i wirtualna niedostępna bez opcji odzyskiwania.  Zestaw AzureRmVMDiskEncryptionExtension nie jest obecnie kopii dysków zarządzanych i zostanie błąd, jeśli użyty przed dysków zarządzanych, chyba że został określony parametr - skipVmBackup.  Ten parametr jest niebezpieczne używać, chyba że już wykonano kopię zapasową poza szyfrowania dysków Azure.   Jeśli nie określono parametru - skipVmBackup, polecenie cmdlet nie dokona kopii zapasowej dysków zarządzanych przed szyfrowania.  Z tego powodu uważa się wymagane wstępnie wymagana do upewnij się, że kopia zapasowa dysków zarządzanych się, że maszyna wirtualna ma miejsce przed włączeniem szyfrowania dysków Azure w przypadku odzyskiwania później jest potrzebny.  
> [!NOTE]
 > Parametr - skipVmBackup nigdy nie powinien być używany, chyba że migawka lub kopia zapasowa została już dokonana poza szyfrowania dysków Azure. 

* Rozwiązania szyfrowania dysków Azure używa zewnętrznego ochrony klucza funkcji BitLocker dla maszyn wirtualnych IaaS systemu Windows. Dla domeny dołączonego do maszyn wirtualnych, nie należy wypchnąć żadnych zasad grupy, które wymuszania funkcji ochrony Moduł TPM. Aby uzyskać informacje dotyczące zasad grupy "Zezwalaj na funkcję BitLocker bez zgodny moduł TPM", zobacz [dokumentacja zasad grupy funkcji BitLocker](https://technet.microsoft.com/library/ee706521).
* Zasady funkcji BitLocker na maszynach wirtualnych przyłączony do domeny z zasad grupy niestandardowe musi zawierać następujące ustawienie: `Configure user storage of bitlocker recovery information -> Allow 256-bit recovery key` szyfrowania dysków Azure zakończy się niepowodzeniem, jeśli ustawienia zasad niestandardowych grup do używania funkcji Bitlocker są niezgodne. Na komputerach, które nie ma prawidłowe zasady może być wymagane ustawienia, stosowanie nowych zasad, wymuszanie nowych zasad w celu aktualizacji (gpupdate.exe/Force) i ponowne uruchomienie.  
* Aby utworzyć aplikację usługi Azure AD, utworzyć magazyn kluczy, lub skonfigurować istniejący magazyn kluczy i włączenia szyfrowania, zobacz [skrypt programu PowerShell wymagań wstępnych szyfrowania dysków Azure](https://github.com/Azure/azure-powershell/blob/master/src/ResourceManager/Compute/Commands.Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1).
* Aby skonfigurować wymagań wstępnych szyfrowania dysków za pomocą interfejsu wiersza polecenia Azure, zobacz [ten skrypt Bash](https://github.com/ejarvi/ade-cli-getting-started).
* Aby korzystać z usługi Kopia zapasowa Azure kopii zapasowej i przywracanie zaszyfrowanych maszyn wirtualnych, po włączeniu szyfrowania z szyfrowania dysków Azure, szyfrowania maszyn wirtualnych przy użyciu konfiguracji klucza szyfrowania dysków Azure. Usługa kopii zapasowej obsługuje maszyny wirtualne, które są szyfrowane za pomocą KEK nie lub KEK konfiguracji. Zobacz [jak wykonać kopię zapasową i przywrócić szyfrowane maszyn wirtualnych przy użyciu szyfrowania usługi Kopia zapasowa Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).

* Podczas szyfrowania woluminu systemu operacyjnego Linux, należy pamiętać, że ponowne uruchomienie maszyny Wirtualnej jest obecnie wymagane na koniec procesu. Można to zrobić za pomocą portalu, programu powershell lub interfejsu wiersza polecenia.   Aby śledzić postęp szyfrowania, okresowo sondować komunikatu o stanie zwracanych przez Get-AzureRmVMDiskEncryptionStatus https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus.  Po zakończeniu szyfrowania komunikatu o stanie zwracanych przez to polecenie będzie tę informację. Na przykład "postępu: dysk systemu operacyjnego zostały pomyślnie zaszyfrowane, wykonaj ponowny rozruch maszyny Wirtualnej" w tym punkcie maszyny Wirtualnej może być uruchomiona ponownie oraz używane.  

* Azure szyfrowanie dysku dla systemu Linux wymaga dysków z danymi ma zainstalowany system plików w systemie Linux przed szyfrowania

* Rekursywnie zainstalowanego dane, które dyski nie są obsługiwane przez szyfrowanie dysków Azure dla systemu Linux. Na przykład, jeśli system docelowy ma zainstalowany dysk paska/foo/i następnie w /foo/bar/baz, szyfrowanie /foo/bar/baz powiedzie się, ale szyfrowania paska/foo/zakończy się niepowodzeniem. 

* Szyfrowanie dysków Azure jest obsługiwana tylko w przypadku obrazów obsługiwane galerii Azure, które spełniają wymagania wstępne wymienione wyżej. Niestandardowe obrazy klienta nie są obsługiwane z powodu schemat partycji niestandardowych i zachowania procesu, które mogą występować w tych obrazów. Co więcej, nawet galerii opartej na obrazie maszyny Wirtualnej, która początkowo spełnione wymagania wstępne zostały zmienione po utworzeniu mogą być niezgodne.  W tym powodem, sugerowane procedury szyfrowania Maszynę wirtualną systemu Linux jest uruchomić z czystą galerii, szyfrowanie maszyny Wirtualnej, a następnie dodaj niestandardowe oprogramowania lub dane do maszyny Wirtualnej, zgodnie z potrzebami.  

* Szyfrowanie dysków Azure i ilość danych lokalnych - wolumin Bek dla systemu Windows i/mnt/azure_bek_disk dla maszyn wirtualnych IaaS systemu Linux w celu bezpiecznego przechowywania klucza szyfrowania. Nie należy usuwać ani edytować zawartość w tym dysku. Odinstalowuje dysku, ponieważ obecności klucza szyfrowania jest potrzebny dla wszystkich operacji szyfrowania na Maszynie wirtualnej IaaS. Plik README zawarte w woluminie zawiera dodatkowe szczegóły.

#### <a name="set-up-the-azure-ad-application-in-azure-active-directory"></a>Konfigurowanie aplikacji usługi Azure AD w usłudze Azure Active Directory
Jeśli potrzebujesz szyfrowanie można włączyć na maszynie Wirtualnej uruchomionej w usłudze Azure szyfrowania dysków Azure generuje i zapisuje klucze szyfrowania magazynu kluczy. Zarządzanie kluczy szyfrowania w magazynie kluczy, wymaga uwierzytelniania usługi Azure AD.

W tym celu należy utworzyć aplikację usługi Azure AD. Szczegółowy opis kroków można znaleźć rejestrowania aplikacji w sekcji "Pobierz tożsamości dla aplikacji" w blogu [usługi Azure Key Vault - krok po kroku](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx). Ten wpis zawiera również liczbę przykłady przydatne do instalowania i konfigurowania magazynu kluczy. Na potrzeby uwierzytelniania można użyć uwierzytelniania opartego na klucz tajny klienta i uwierzytelnianie klienta oparte na certyfikatach usługi Azure AD.

#### <a name="client-secret-based-authentication-for-azure-ad"></a>Uwierzytelnianie oparte na protokole klucz tajny klienta dla usługi Azure AD
W kolejnych sekcjach ułatwiają konfigurowanie uwierzytelniania opartego na klucz tajny klienta dla usługi Azure AD.

##### <a name="create-an-azure-ad-application-by-using-azure-powershell"></a>Tworzenie aplikacji usługi Azure AD przy użyciu programu Azure PowerShell
Aby utworzyć aplikację usługi Azure AD, należy użyć następującego polecenia cmdlet programu PowerShell:

    $aadClientSecret = "yourSecret"
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -Password $aadClientSecret
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

> [!NOTE]
> $azureAdApplication.ApplicationId jest Azure AD ClientID i $aadClientSecret ze klucza tajnego klienta należy używać później można włączyć szyfrowania dysków Azure. Odpowiednio Chroń klucz tajny klienta usługi Azure AD.

##### <a name="setting-up-the-azure-ad-client-id-and-secret-from-the-azure-portal"></a>Konfigurowanie usługi Azure AD identyfikator klienta i klucz tajny z portalu Azure
Można również ustawić zapasowej identyfikator klienta usługi Azure AD i klucz tajny przy użyciu portalu Azure. Aby wykonać to zadanie, wykonaj następujące czynności:

1. Wybierz **wszystkie usługi > Azure Active Directory**

 ![Usługa Azure Disk Encryption](./media/azure-security-disk-encryption/aad-service.png)

2. Wybierz **rejestracji aplikacji > nowej rejestracji aplikacji**

 ![Usługa Azure Disk Encryption](./media/azure-security-disk-encryption/aad-app-registration.png)

3. Podaj wymagane informacje i utworzyć aplikację:

 ![Usługa Azure Disk Encryption](./media/azure-security-disk-encryption/aad-create-app.png)

4. Wybierz nowo utworzonej aplikacji, aby wyświetlić jego właściwości, w tym identyfikator aplikacji.  Aby utworzyć klucz dla aplikacji, wybierz **Ustawienia > klucze**, Dodaj opis i wygaśnięcia klucza i kliknij przycisk **Zapisz**

 ![Usługa Azure Disk Encryption](./media/azure-security-disk-encryption/aad-create-pw.png)

5. Skopiuj wygenerowany wartość tajna i odpowiednio go chronić.

 ![Usługa Azure Disk Encryption](./media/azure-security-disk-encryption/aad-save-pw.png)


##### <a name="use-an-existing-application"></a>Użyj istniejącej aplikacji
Do wykonania poniższych poleceń, uzyskiwał i wykorzystywał [modułu Azure AD PowerShell](https://technet.microsoft.com/library/jj151815.aspx).

> [!NOTE]
> Należy wykonać następujące polecenia z nowe okno programu PowerShell. Nie należy używać programu Azure PowerShell lub w oknie usługi Azure Resource Manager można wykonać polecenia. Firma Microsoft zaleca takie podejście, ponieważ te polecenia cmdlet w MSOnline module lub Azure AD PowerShell.

    $clientSecret = ‘<yourAadClientSecret>’
    $aadClientID = '<Client ID of your Azure AD application>'
    connect-msolservice
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type password -Value $clientSecret

#### <a name="certificate-based-authentication-for-azure-ad"></a>Uwierzytelnianie oparte na certyfikatach dla usługi Azure AD
> [!NOTE]
> Uwierzytelnianie oparte na certyfikatach AD Azure aktualnie nie jest obsługiwane na maszynach wirtualnych systemu Linux.

W kolejnych sekcjach pokazano, jak skonfigurować uwierzytelnianie oparte na certyfikatach dla usługi Azure AD.

##### <a name="create-an-azure-ad-application"></a>Tworzenie aplikacji usługi Azure AD
Aby utworzyć aplikację usługi Azure AD, wykonaj następujące polecenia cmdlet programu PowerShell:

> [!NOTE]
> Zastąp następujące `yourpassword` ciągu bezpiecznego hasła i ochrony hasłem.

    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate("C:\certificates\examplecert.pfx", "yourpassword")
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -KeyValue $keyValue -KeyType AsymmetricX509Cert
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

Po zakończeniu tego kroku, Przekaż plik PFX do magazynu kluczy i włącz zasady dostępu, wymagane do wdrożenia tego certyfikatu do maszyny Wirtualnej.

##### <a name="use-an-existing-azure-ad-application"></a>Użyj istniejącej aplikacji usługi Azure AD
W przypadku konfigurowania uwierzytelniania opartego na certyfikatach dla istniejącej aplikacji, użyj polecenia cmdlet programu PowerShell pokazano poniżej. Pamiętaj wykonać je z nowe okno programu PowerShell.

    $certLocalPath = 'C:\certs\myaadapp.cer'
    $aadClientID = '<Client ID of your Azure AD application>'
    connect-msolservice
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate
    $cer.Import($certLocalPath)
    $binCert = $cer.GetRawCertData()
    $credValue = [System.Convert]::ToBase64String($binCert);
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type asymmetric -Value $credValue -Usage verify

Po zakończeniu tego kroku, Przekaż plik PFX do magazynu kluczy i włącz zasady dostępu, które ma potrzebne do wdrożenia certyfikatów do maszyny Wirtualnej.

##### <a name="upload-a-pfx-file-to-your-key-vault"></a>Przekaż plik PFX do magazynu kluczy
Aby uzyskać szczegółowy opis tego procesu, zobacz [oficjalny Azure klucza magazynu Blog zespołu](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx). Następujące polecenia cmdlet programu PowerShell są jednak potrzebne dla zadania. Należy je wykonać z konsoli programu Azure PowerShell.

> [!NOTE]
> Zastąp następujące `yourpassword` ciągu bezpiecznego hasła i ochrony hasłem.

    $certLocalPath = 'C:\certs\myaadapp.pfx'
    $certPassword = "yourpassword"
    $resourceGroupName = ‘yourResourceGroup’
    $keyVaultName = ‘yourKeyVaultName’
    $keyVaultSecretName = ‘yourAadCertSecretName’

    $fileContentBytes = get-content $certLocalPath -Encoding Byte
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

    $jsonObject = @"
    {
    "data": "$filecontentencoded",
    "dataType" :"pfx",
    "password": "$certPassword"
    }
    "@

    $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
    $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

    Switch-AzureMode -Name AzureResourceManager
    $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
    Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName -SecretValue $secret
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ResourceGroupName $resourceGroupName –EnabledForDeployment

##### <a name="deploy-a-certificate-in-your-key-vault-to-an-existing-vm"></a>Wdróż certyfikat w magazynie kluczy, istniejącej maszyny Wirtualnej
Po zakończeniu przekazywania plik PFX, Wdróż certyfikat w magazynie kluczy istniejącej maszyny Wirtualnej z następujących czynności:
 ```
    $resourceGroupName = ‘yourResourceGroup’
    $keyVaultName = ‘yourKeyVaultName’
    $keyVaultSecretName = ‘yourAadCertSecretName’
    $vmName = ‘yourVMName’
    $certUrl = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName).Id
    $sourceVaultId = (Get-AzureRmKeyVault -VaultName $keyVaultName -ResourceGroupName $resourceGroupName).ResourceId
    $vm = Get-AzureRmVM -ResourceGroupName $resourceGroupName -Name $vmName
    $vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore "My" -CertificateUrl $certUrl
    Update-AzureRmVM -VM $vm  -ResourceGroupName $resourceGroupName
 ```

#### <a name="set-up-the-key-vault-access-policy-for-the-azure-ad-application"></a>Konfigurowanie zasad dostępu do magazynu kluczy dla aplikacji usługi Azure AD
Aplikacja Azure AD wymaga praw dostępu do kluczy lub kluczy tajnych w magazynie. Użyj [ `Set-AzureKeyVaultAccessPolicy` ](/powershell/module/azure/set-azurekeyvaultaccesspolicy?view=azuresmps-3.7.0) polecenia cmdlet, aby udzielić uprawnień do aplikacji, przy użyciu Identyfikatora klienta (który został wygenerowany, gdy aplikacja została zarejestrowana) jako _— ServicePrincipalName_ wartość parametru. Aby dowiedzieć się więcej, zobacz wpis w blogu [usługi Azure Key Vault - krok po kroku](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx). Oto przykład sposobu wykonania tego zadania za pomocą programu PowerShell:

    $keyVaultName = '<yourKeyVaultName>'
    $aadClientID = '<yourAadAppClientID>'
    $rgname = '<yourResourceGroup>'
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $rgname

> [!NOTE]
> Szyfrowanie dysków Azure, musisz skonfigurować następujące zasady dostępu do aplikacji klienta usługi Azure AD: _WrapKey_ i _ustawić_ uprawnienia.

## <a name="terminology"></a>Terminologia
Aby poznać niektóre typowe terminy używane przez tę technologię, skorzystaj z poniższej tabeli terminologią:

| Terminologia | Definicja |
| --- | --- |
| Azure AD | Usługa Azure AD [usługi Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/). Konto usługi Azure AD jest wymagane do uwierzytelniania, przechowywania i pobierania kluczy tajnych w magazynie kluczy. |
| W usłudze Azure Key Vault | Key Vault jest usługą zarządzania kryptograficznych, klucza opartego na zweryfikowane FIPS Federal Information Processing standardów sprzętowych modułów zabezpieczeń, które pomagają w ochronie z kluczy kryptograficznych i kluczy tajnych poufnych. Aby uzyskać więcej informacji, zobacz [Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentacji. |
| ARM | Azure Resource Manager |
| BitLocker |[Funkcja BitLocker](https://technet.microsoft.com/library/hh831713.aspx) jest branży Windows woluminu szyfrowania technologia, która jest używana do włączenia szyfrowania dysków na maszynach wirtualnych IaaS systemu Windows. |
| KSB | Klucze szyfrowania funkcji BitLocker są używane do szyfrowania woluminu rozruchowego systemu operacyjnego i woluminów danych. Klucze funkcji BitLocker są chronione w magazynie kluczy jako kluczy tajnych. |
| Interfejs wiersza polecenia | Zobacz [interfejsu wiersza polecenia platformy Azure](../cli-install-nodejs.md). |
| DM-Crypt |[DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) podsystem opartych na systemie Linux lub przezroczystego szyfrowania dysku, który jest używany do Włącz szyfrowanie dysku dla maszyn wirtualnych systemu Linux IaaS. |
| KEK | Klucz szyfrowania jest klucza asymetrycznego (RSA 2048), który służy do ochrony lub opakuj klucz tajny. Możesz podać zabezpieczeń sprzętowych modułów (HSM)-chronione klucza lub klucza chronionego przez oprogramowanie. Aby uzyskać więcej informacji, zobacz [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentacji. |
| PS polecenia cmdlet. | Zobacz [poleceń cmdlet programu Azure PowerShell](/powershell/azure/overview). |

### <a name="set-up-and-configure-your-key-vault-for-azure-disk-encryption"></a>Instalowanie i Konfigurowanie magazynu kluczy do szyfrowania dysków Azure
Szyfrowanie dysków Azure ułatwia zabezpieczenie szyfrowanie dysków kluczy i kluczy tajnych w magazynie kluczy. Aby skonfigurować szyfrowanie dysków Azure magazynu kluczy, wykonać kroki opisane w poniższych sekcjach.

#### <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy
Aby utworzyć magazyn kluczy, użyj jednej z następujących opcji:

* ["101-Key-Vault-Create" Resource Manager template](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)
* [Polecenia cmdlet magazynu kluczy Azure programu PowerShell](/powershell/module/azurerm.keyvault/#key_vault)
* Azure Resource Manager
* Jak [bezpiecznego magazynu kluczy](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)

> [!NOTE]
> Jeśli magazyn kluczy zostały już skonfigurowane dla Twojej subskrypcji, przejdź do następnej sekcji.

![W usłudze Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig1.png)

#### <a name="set-up-a-key-encryption-key-optional"></a>Konfigurowanie klucza szyfrowania (opcjonalnie)
Jeśli chcesz użyć KEK dla dodatkową warstwę zabezpieczeń dla kluczy szyfrowania funkcją BitLocker, należy dodać KEK do magazynu kluczy. Użyj [ `Add-AzureKeyVaultKey` ](/powershell/module/azurerm.keyvault/add-azurermkeyvaultkey) polecenia cmdlet, aby utworzyć klucz szyfrowania kluczy w magazynie kluczy. Można także zaimportować KEK z lokalnymi Zarządzanie klucza HSM. Aby uzyskać więcej informacji, zobacz [klucza magazynu dokumentacji](https://azure.microsoft.com/documentation/services/key-vault/).

    Add-AzureKeyVaultKey [-VaultName] <string> [-Name] <string> -Destination <string> {HSM | Software}

Można dodać klucza KEK, przechodząc do usługi Azure Resource Manager lub przy użyciu interfejsu magazynu kluczy.

![W usłudze Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig2.png)

#### <a name="set-key-vault-permissions"></a>Ustaw uprawnienia magazyn kluczy
Platforma Azure wymaga dostępu do kluczy szyfrowania lub kluczy tajnych w magazynie kluczy, aby udostępnić je do maszyny Wirtualnej do rozruchu i odszyfrowywania woluminów. Aby udzielić uprawnień do platformy Azure, należy ustawić **EnabledForDiskEncryption** właściwości klucza magazynu za pomocą polecenia cmdlet programu PowerShell magazynu kluczy:

    Set-AzureRmKeyVaultAccessPolicy -VaultName <yourVaultName> -ResourceGroupName <yourResourceGroup> -EnabledForDiskEncryption

Można również ustawić **EnabledForDiskEncryption** właściwości odwiedzając [Eksploratora zasobów Azure](https://resources.azure.com).

Jak wspomniano wcześniej, musisz ustawić **EnabledForDiskEncryption** właściwości magazynu kluczy. W przeciwnym razie wdrażanie zakończy się niepowodzeniem.

Jak pokazano poniżej, można skonfigurować zasady dostępu dla aplikacji usługi Azure AD z interfejsu magazynu kluczy:

![W usłudze Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig3.png)

![W usłudze Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig3b.png)

Na **zasady dostępu zaawansowane** i upewnij się, że magazynu kluczy jest włączona obsługa szyfrowania dysków Azure:

![Usługi Azure key vault](./media/azure-security-disk-encryption/keyvault-portal-fig4.png)

## <a name="disk-encryption-deployment-scenarios-and-user-experiences"></a>Scenariusze wdrażania szyfrowanie dysków oraz możliwości użytkowników.
Aby umożliwić wiele scenariuszy szyfrowania dysków i kroki mogą się różnić w zależności od scenariusza. W poniższych częściach omówiono scenariusze większej liczby szczegółów.

### <a name="enable-encryption-on-new-iaas-vms-that-are-created-from-the-marketplace"></a>Włącz szyfrowanie dla nowych maszyn wirtualnych IaaS utworzony z witryny Marketplace
Można włączyć szyfrowanie dysków na nowej maszyny Wirtualnej systemu Windows IaaS z witryny Marketplace na platformie Azure przy użyciu [szablonu usługi Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image).

1. W szablonie szybki start Azure, kliknij polecenie **wdrażanie na platformie Azure**, wprowadź konfiguracji szyfrowania **parametry** bloku, a następnie kliknij przycisk **OK**.

2. Wybierz subskrypcję, grupy zasobów, lokalizacja grupy zasobów, postanowienia prawne i umowę, a następnie kliknij przycisk **Utwórz** Aby włączyć szyfrowanie na nowej maszyny Wirtualnej IaaS.

> [!NOTE]
> Ten szablon umożliwia tworzenie nowych zaszyfrowanych Windows maszynę Wirtualną, która używa obrazu galerii systemu Windows Server 2012.

Można włączyć szyfrowanie dysków na nowe IaaS RedHat Linux 7.2 maszynę Wirtualną za pomocą macierzy RAID-0 200 GB, za pomocą tego [szablonu usługi Resource Manager](https://aka.ms/fde-rhel). Po wdrożeniu szablonu, sprawdź stan szyfrowania maszyny Wirtualnej za pomocą `Get-AzureRmVmDiskEncryptionStatus` polecenia cmdlet, zgodnie z opisem w [OS szyfrowania dysków na uruchomionej maszyny Wirtualnej systemu Linux](#encrypting-os-drive-on-a-running-linux-vm). Gdy komputer zwraca stan _VMRestartPending_, uruchom ponownie maszynę Wirtualną.

W poniższej tabeli wymieniono parametrów szablonu usługi Resource Manager dla nowych maszyn wirtualnych z scenariusza Marketplace przy użyciu Identyfikatora klienta usługi Azure AD:

| Parametr | Opis |
| --- | --- |
| adminUserName | Nazwa użytkownika administratora dla maszyny wirtualnej. |
| adminPassword | Hasło użytkownika administratora dla maszyny wirtualnej. |
| newStorageAccountName | Nazwa konta magazynu do przechowywania systemu operacyjnego i danych wirtualne dyski twarde. |
| vmSize | Rozmiar maszyny wirtualnej. Obecnie obsługiwane są tylko standardowe A, D i G serii. |
| virtualNetworkName | Nazwa sieci wirtualnej, której maszyna wirtualna karta sieciowa powinna należeć. |
| subnetName | Nazwa podsieci w sieci wirtualnej, której maszyna wirtualna karta sieciowa powinna należeć. |
| AADClientID | Identyfikator klienta aplikacji usługi Azure AD, które ma uprawnienia do zapisu kluczy tajnych magazynu kluczy. |
| AADClientSecret | Klucz tajny klienta aplikacji usługi Azure AD, które ma uprawnienia do zapisu kluczy tajnych magazynu kluczy. |
| keyVaultURL | Adres URL magazynu kluczy, który powinien być przekazywane klucza funkcji BitLocker. Możesz pobrać go za pomocą polecenia cmdlet `(Get-AzureRmKeyVault -VaultName,-ResourceGroupName ).VaultURI`. |
| keyEncryptionKeyURL | Adres URL klucza szyfrowania klucza, który jest używany do szyfrowania klucza funkcji BitLocker wygenerowanego (opcjonalnie). |
| keyVaultResourceGroup | Grupa zasobów magazynu kluczy. |
| vmName | Nazwa maszyny Wirtualnej, można wykonać w operacji szyfrowania. |

> [!NOTE]
> _KeyEncryptionKeyURL_ jest parametrem opcjonalnym. Będzie można przełączyć własne KEK do dalszego zabezpieczenie klucza szyfrowania danych (klucz tajny hasło) w magazynie kluczy.

### <a name="enable-encryption-on-new-iaas-vms-that-are-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Włącz szyfrowanie dla nowych maszyn wirtualnych IaaS, które są tworzone na podstawie klienta systemu szyfrowania plików VHD i kluczy szyfrowania
W tym scenariuszu można włączyć szyfrowanie przy użyciu szablonu usługi Resource Manager, poleceń cmdlet programu PowerShell lub polecenia interfejsu wiersza polecenia. W poniższych sekcjach opisano szczegółowo szablonu usługi Resource Manager i polecenia interfejsu wiersza polecenia.

Postępuj zgodnie z instrukcjami z jednego z tych sekcji przygotowania zaszyfrowane wstępnie obrazy, których można użyć w systemie Azure. Po utworzeniu obrazu służy kroki opisane w następnej sekcji można utworzyć zaszyfrowanego maszyny Wirtualnej platformy Azure.

* [Przygotowanie wstępnie zaszyfrowanego dysku VHD systemu Windows](#preparing-a-pre-encrypted-windows-vhd)
* [Przygotowanie wstępnie zaszyfrowanego dysku VHD systemu Linux](#preparing-a-pre-encrypted-linux-vhd)

#### <a name="using-the-resource-manager-template"></a>Przy użyciu szablonu usługi Resource Manager
Można włączyć szyfrowanie dysków na zaszyfrowany dysk VHD za pomocą [szablonu usługi Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-pre-encrypted-vm).

1. W szablonie szybki start Azure, kliknij polecenie **wdrażanie na platformie Azure**, wprowadź konfiguracji szyfrowania **parametry** bloku, a następnie kliknij przycisk **OK**.

2. Wybierz subskrypcję, grupy zasobów, lokalizacja grupy zasobów, postanowienia prawne i umowę, a następnie kliknij przycisk **Utwórz** Aby włączyć szyfrowanie na nowej maszyny Wirtualnej IaaS.

W poniższej tabeli wymieniono parametrów szablonu usługi Resource Manager z zaszyfrowanego dysku VHD:

| Parametr | Opis |
| --- | --- |
| newStorageAccountName | Nazwa konta magazynu do przechowywania zaszyfrowanego dysku VHD systemu operacyjnego. To konto magazynu należy już został utworzony w tej samej grupie zasobów i tej samej lokalizacji co maszyny Wirtualnej. |
| osVhdUri | Identyfikator URI dysku VHD systemu operacyjnego z konta magazynu. |
| osType | Typ produktu systemu operacyjnego (Windows/Linux). |
| virtualNetworkName | Nazwa sieci wirtualnej, której maszyna wirtualna karta sieciowa powinna należeć. Nazwa powinna już został utworzony w tej samej grupie zasobów i tej samej lokalizacji co maszyny Wirtualnej. |
| subnetName | Nazwa podsieci w sieci wirtualnej, której maszyna wirtualna karta sieciowa powinna należeć. |
| vmSize | Rozmiar maszyny wirtualnej. Obecnie obsługiwane są tylko standardowe A, D i G serii. |
| keyVaultResourceID | Identyfikator zasobu, który identyfikuje zasobu magazynu kluczy Azure Resource Manager. Możesz pobrać go za pomocą polecenia cmdlet programu PowerShell `(Get-AzureRmKeyVault -VaultName &lt;yourKeyVaultName&gt; -ResourceGroupName &lt;yourResourceGroupName&gt;).ResourceId`. |
| keyVaultSecretUrl | Adres URL klucza szyfrowania dysków, który został skonfigurowany w magazynie kluczy. |
| keyVaultKekUrl | Adres URL klucz szyfrowania kluczy do szyfrowania klucza wygenerowanego szyfrowanie dysków. |
| vmName | Nazwa maszyny Wirtualnej IaaS. |

#### <a name="using-powershell-cmdlets"></a>Za pomocą poleceń cmdlet programu PowerShell
Można włączyć szyfrowanie dysków na zaszyfrowany dysk VHD za pomocą polecenia cmdlet programu PowerShell [ `Set-AzureRmVMOSDisk` ](/powershell/module/azurerm.compute/set-azurermvmosdisk).  

#### <a name="using-cli-commands"></a>Za pomocą polecenia interfejsu wiersza polecenia
Aby włączyć szyfrowanie dysku dla tego scenariusza za pomocą polecenia interfejsu wiersza polecenia, wykonaj następujące czynności:

1. Ustawianie zasad dostępu w magazynie kluczy:

   * Ustaw **EnabledForDiskEncryption** flagi:

    `azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
   * Ustaw uprawnienia do aplikacji usługi Azure AD do zapisu kluczy tajnych magazynu kluczy:

    `azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys '["wrapKey"]' --perms-to-secrets '["set"]'`

2. Aby włączyć szyfrowanie na istniejących lub uruchomionej maszyny Wirtualnej, wpisz:

 `azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId> --volume-type [All|OS|Data]`

3. Pobierz stan szyfrowania:

 `azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json`

4. Aby włączyć szyfrowanie na nowej maszyny Wirtualnej z zaszyfrowany dysk VHD, należy użyć poniższych parametrów z `azure vm create` polecenia:

 ```
   * disk-encryption-key-vault-id <disk-encryption-key-vault-id>
   * disk-encryption-key-url <disk-encryption-key-url>
   * key-encryption-key-vault-id <key-encryption-key-vault-id>
   * key-encryption-key-url <key-encryption-key-url>
 ```

### <a name="enable-encryption-on-existing-or-running-iaas-windows-vm-in-azure"></a>Włącz szyfrowanie dla istniejących lub uruchomione maszyny Wirtualnej systemu Windows IaaS na platformie Azure
W tym scenariuszu można włączyć szyfrowanie przy użyciu szablonu usługi Resource Manager, poleceń cmdlet programu PowerShell lub polecenia interfejsu wiersza polecenia. W poniższych sekcjach opisano szczegółowo, jak je włączyć za pomocą szablonu usługi Resource Manager i polecenia interfejsu wiersza polecenia.

#### <a name="using-the-resource-manager-template"></a>Przy użyciu szablonu usługi Resource Manager
Można włączyć szyfrowanie dysku na istniejących lub uruchomione maszyny wirtualne IaaS systemu Windows na platformie Azure przy użyciu [szablonu usługi Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm).

1. W szablonie szybki start Azure, kliknij polecenie **wdrażanie na platformie Azure**, wprowadź konfiguracji szyfrowania **parametry** bloku, a następnie kliknij przycisk **OK**.

2. Wybierz subskrypcję, grupy zasobów, lokalizacja grupy zasobów, postanowienia prawne i umowę, a następnie kliknij przycisk **Utwórz** Aby włączyć szyfrowanie na Maszynie wirtualnej IaaS istniejących lub nie działają.

Poniższa tabela zawiera listę parametrów szablonu usługi Resource Manager dla istniejących lub działających maszyn wirtualnych, które mają identyfikator klienta usługi Azure AD:

| Parametr | Opis |
| --- | --- |
| AADClientID | Identyfikator klienta aplikacji usługi Azure AD, które ma uprawnienia do zapisu kluczy tajnych w magazynie kluczy. |
| AADClientSecret | Klucz tajny klienta aplikacji usługi Azure AD, które ma uprawnienia do zapisu kluczy tajnych w magazynie kluczy. |
| keyVaultName | Nazwa magazynu kluczy, który powinien być przekazywane klucza funkcji BitLocker. Możesz pobrać go za pomocą polecenia cmdlet `(Get-AzureRmKeyVault -ResourceGroupName <yourResourceGroupName>). Vaultname`. |
|  keyEncryptionKeyURL | Adres URL klucza szyfrowania klucza, który jest używany do szyfrowania wygenerowanego klucza funkcji BitLocker. Ten parametr jest opcjonalny w przypadku wybrania **nokek** na liście rozwijanej UseExistingKek. W przypadku wybrania **kek** na liście rozwijanej UseExistingKek należy wprowadzić _keyEncryptionKeyURL_ wartości. |
| volumeType | Typ operacji szyfrowania jest wykonywana na wolumin. Prawidłowe wartości to _OS_, _danych_, i _wszystkich_. |
| sequenceVersion | Wersja sekwencji operacji funkcji BitLocker. Zwiększ numer wersji, za każdym razem, gdy operacja szyfrowania dysków jest wykonywana na tej samej maszyny Wirtualnej. |
| vmName | Nazwa maszyny Wirtualnej, można wykonać w operacji szyfrowania. |

> [!NOTE]
> _KeyEncryptionKeyURL_ jest parametrem opcjonalnym. Własne KEK może doprowadzić do dalszych zabezpieczenie klucza szyfrowania danych (funkcji BitLocker Szyfrowanie klucz tajny) w magazynie kluczy.

#### <a name="using-powershell-cmdlets"></a>Za pomocą poleceń cmdlet programu PowerShell
Aby uzyskać informacje na temat włączania szyfrowania z szyfrowania dysków Azure za pomocą poleceń cmdlet programu PowerShell, zobacz wpisy blogu [Eksploruj szyfrowania dysków Azure przy użyciu programu Azure PowerShell — część 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) i [Eksploruj szyfrowania dysków Azure przy użyciu programu Azure PowerShell — część 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx).

#### <a name="using-cli-commands"></a>Za pomocą polecenia interfejsu wiersza polecenia
Aby włączyć szyfrowanie na istniejących lub uruchomione maszyny Wirtualnej systemu Windows IaaS na platformie Azure przy użyciu polecenia interfejsu wiersza polecenia, wykonaj następujące czynności:

1. Aby ustawić zasady dostępu w magazynie kluczy:
   * Ustaw **EnabledForDiskEncryption** flagi:

    `azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
   * Ustaw uprawnienia do aplikacji usługi Azure AD do zapisu kluczy tajnych magazynu kluczy:

    `azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys '["wrapKey"]' --perms-to-secrets '["set"]'`
2. Aby włączyć szyfrowanie na istniejących lub uruchomionej maszyny Wirtualnej:

 `azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId> --volume-type [All|OS|Data]`
3. Można pobrać stanu szyfrowania:

 `azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json`
4. Aby włączyć szyfrowanie na nowej maszyny Wirtualnej z zaszyfrowany dysk VHD, należy użyć poniższych parametrów z `azure vm create` polecenia:

 ```
   * disk-encryption-key-vault-id <disk-encryption-key-vault-id>
   * disk-encryption-key-url <disk-encryption-key-url>
   * key-encryption-key-vault-id <key-encryption-key-vault-id>
   * key-encryption-key-url <key-encryption-key-url>
 ```

### <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm-in-azure"></a>Włącz szyfrowanie dla istniejących lub nie działają IaaS maszyny Wirtualnej systemu Linux na platformie Azure
Można włączyć szyfrowanie dysków na istniejące lub nie działają IaaS maszyny Wirtualnej systemu Linux na platformie Azure przy użyciu [szablonu usługi Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

1. Kliknij przycisk **wdrażanie na platformie Azure** na szablonie szybki start Azure, wprowadź konfiguracji szyfrowania na **parametry** bloku, a następnie kliknij przycisk **OK**.

2. Wybierz subskrypcję, grupy zasobów, lokalizacja grupy zasobów, postanowienia prawne i umowę, a następnie kliknij przycisk **Utwórz** Aby włączyć szyfrowanie na Maszynie wirtualnej IaaS istniejących lub nie działają.

Poniższa tabela zawiera listę parametrów szablonu usługi Resource Manager dla istniejących lub działających maszyn wirtualnych, które mają identyfikator klienta usługi Azure AD:

| Parametr | Opis |
| --- | --- |
| AADClientID | Identyfikator klienta aplikacji usługi Azure AD, które ma uprawnienia do zapisu kluczy tajnych w magazynie kluczy. |
| AADClientSecret | Klucz tajny klienta aplikacji usługi Azure AD, które ma uprawnienia do zapisu kluczy tajnych magazynu kluczy. |
| keyVaultName | Nazwa magazynu kluczy, który powinien być przekazywane klucza funkcji BitLocker. Możesz pobrać go za pomocą polecenia cmdlet `(Get-AzureRmKeyVault -ResourceGroupName <yourResourceGroupName>). Vaultname`. |
|  keyEncryptionKeyURL | Adres URL klucza szyfrowania klucza, który jest używany do szyfrowania wygenerowanego klucza funkcji BitLocker. Ten parametr jest opcjonalny w przypadku wybrania **nokek** na liście rozwijanej UseExistingKek. W przypadku wybrania **kek** na liście rozwijanej UseExistingKek należy wprowadzić _keyEncryptionKeyURL_ wartości. |
| volumeType | Typ operacji szyfrowania jest wykonywana na wolumin. Nieprawidłowa obsługiwane wartości to _OS_ lub _wszystkie_ (zobacz obsługiwanych dystrybucjach systemu Linux i ich wersje systemu operacyjnego i dysków z danymi w sekcji prerequisiteis wcześniej). |
| sequenceVersion | Wersja sekwencji operacji funkcji BitLocker. Zwiększ numer wersji, za każdym razem, gdy operacja szyfrowania dysków jest wykonywana na tej samej maszyny Wirtualnej. |
| vmName | Nazwa maszyny Wirtualnej, można wykonać w operacji szyfrowania. |
| passPhrase | Wpisz silne hasło klucza szyfrowania danych. |

> [!NOTE]
> _KeyEncryptionKeyURL_ jest parametrem opcjonalnym. Będzie można przełączyć własne KEK do dalszego zabezpieczenie klucza szyfrowania danych (klucz tajny hasło) w magazynie kluczy.

#### <a name="cli-commands"></a>Polecenia interfejsu wiersza polecenia
Można włączyć szyfrowanie dysków na zaszyfrowany dysk VHD przez zainstalowanie i używanie [polecenia interfejsu wiersza polecenia](../cli-install-nodejs.md). Aby włączyć szyfrowanie na istniejących lub działających maszyn wirtualnych systemu Linux IaaS na platformie Azure przy użyciu polecenia interfejsu wiersza polecenia, wykonaj następujące czynności:

1. Ustawianie zasad dostępu w magazynie kluczy:

 * Ustaw **EnabledForDiskEncryption** flagi:

    `azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
 * Ustaw uprawnienia do aplikacji usługi Azure AD do zapisu kluczy tajnych magazynu kluczy:

    `azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys '["wrapKey"]' --perms-to-secrets '["set"]'`

2. Aby włączyć szyfrowanie na istniejących lub uruchomionej maszyny Wirtualnej:

 `azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId> --volume-type [All|OS|Data]`

3. Pobierz stan szyfrowania:

 `azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json`

4. Aby włączyć szyfrowanie na nowej maszyny Wirtualnej z zaszyfrowany dysk VHD, należy użyć poniższych parametrów z `azure vm create` polecenia:
 ```
   * disk-encryption-key-vault-id <disk-encryption-key-vault-id>
   * disk-encryption-key-url <disk-encryption-key-url>
   * key-encryption-key-vault-id <key-encryption-key-vault-id>
   * key-encryption-key-url <key-encryption-key-url>
 ```

### <a name="get-the-encryption-status-of-an-encrypted-iaas-vm"></a>Pobieranie stanu szyfrowania zaszyfrowanej maszyn wirtualnych IaaS
Przy użyciu usługi Azure Resource Manager można uzyskać stanu szyfrowania [poleceń cmdlet programu PowerShell](/powershell/azure/overview), lub polecenia interfejsu wiersza polecenia. W poniższych sekcjach opisano sposób korzystania z portalu Azure i poleceń interfejsu wiersza polecenia można pobrać stanu szyfrowania.

#### <a name="get-the-encryption-status-of-an-encrypted-windows-vm-by-using-azure-resource-manager"></a>Pobieranie stanu szyfrowania zaszyfrowanej maszyny Wirtualnej systemu Windows za pomocą usługi Azure Resource Manager
Z usługi Azure Resource Manager można uzyskać stanu szyfrowania maszyn wirtualnych IaaS w następujący sposób:

1. Zaloguj się do [Azure Portal](https://portal.azure.com/), a następnie kliknij przycisk **maszyn wirtualnych** w okienku po lewej stronie, aby wyświetlić widok podsumowania maszyn wirtualnych w ramach subskrypcji. Można filtrować widok maszyn wirtualnych, wybierając Nazwa subskrypcji w **subskrypcji** listy rozwijanej.

2. W górnej części **maszyn wirtualnych** kliknij przycisk **kolumn**.

3. Na **kolumny wybierz** bloku, wybierz opcję **szyfrowanie dysków**, a następnie kliknij przycisk **aktualizacji**. Powinny pojawić się kolumna szyfrowanie dysków przedstawiający stan szyfrowania _włączone_ lub _nie włączono_ dla każdej maszyny Wirtualnej, jak pokazano na poniższej ilustracji:

 ![Ochrona przed złośliwym kodem zapewniana przez Microsoft na platformie Azure](./media/azure-security-disk-encryption/disk-encryption-fig2.png)

#### <a name="get-the-encryption-status-of-an-encrypted-windowslinux-iaas-vm-by-using-the-disk-encryption-powershell-cmdlet"></a>Pobieranie stanu szyfrowania zaszyfrowanej maszyn wirtualnych IaaS (system Windows/Linux) za pomocą polecenia cmdlet programu PowerShell szyfrowania dysków
Polecenia cmdlet programu PowerShell szyfrowanie dysków można uzyskać stanu szyfrowania maszyn wirtualnych IaaS `Get-AzureRmVMDiskEncryptionStatus`. Aby uzyskać ustawienia szyfrowania dla maszyny Wirtualnej, wprowadź następujące informacje:

    C:\> Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : NotEncrypted
    DataVolumesEncrypted       : Encrypted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a

Możesz sprawdzić dane wyjściowe _Get-AzureRmVMDiskEncryptionStatus_ do szyfrowania klucza adresów URL.

    C:\> $status = Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName
    e $VMName -ExtensionName $ExtensionName
    C:\> $status.OsVolumeEncryptionSettings

    DiskEncryptionKey                                                 KeyEncryptionKey                                               Enabled
    -----------------                                                 ----------------                                               -------
    Microsoft.Azure.Management.Compute.Models.KeyVaultSecretReference Microsoft.Azure.Management.Compute.Models.KeyVaultKeyReference    True


    C:\> $status.OsVolumeEncryptionSettings.DiskEncryptionKey.SecretUrl
    https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a
    C:\> $status.OsVolumeEncryptionSettings.DiskEncryptionKey

    SecretUrl                                                                                                               SourceVault
    ---------                                                                                                               -----------
    https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a Microsoft.Azure.Management....

Wartości ustawienia OSVolumeEncrypted i DataVolumesEncrypted są ustawiane na _zaszyfrowane_, który wskazuje, że oba woluminy są szyfrowane za pomocą szyfrowania dysków Azure. Aby uzyskać informacje na temat włączania szyfrowania z szyfrowania dysków Azure za pomocą poleceń cmdlet programu PowerShell, zobacz wpisy blogu [Eksploruj szyfrowania dysków Azure przy użyciu programu Azure PowerShell — część 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) i [Eksploruj szyfrowania dysków Azure przy użyciu programu Azure PowerShell — część 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx).

> [!NOTE]
> Na maszynach wirtualnych systemu Linux, zajmuje trzech do czterech minut `Get-AzureRmVMDiskEncryptionStatus` polecenia cmdlet do raportowania stanu szyfrowania.

#### <a name="get-the-encryption-status-of-the-iaas-vm-from-the-disk-encryption-cli-command"></a>Pobieranie stanu szyfrowania maszyn wirtualnych IaaS szyfrowanie dysków polecenia interfejsu wiersza polecenia
Przy użyciu polecenia interfejsu wiersza polecenia szyfrowanie dysków można uzyskać stanu szyfrowania maszyn wirtualnych IaaS `azure vm show-disk-encryption-status`. Aby uzyskać ustawienia szyfrowania dla maszyny Wirtualnej, wprowadź sesję wiersza polecenia platformy Azure:

    azure vm show-disk-encryption-status --resource-group <yourResourceGroupName> --name <yourVMName> --json  

#### <a name="disable-encryption-on-running-windows-iaas-vm"></a>Wyłącz szyfrowanie uruchamiania maszyny Wirtualnej systemu Windows IaaS
Można wyłączyć szyfrowania na uruchomionej maszyny Wirtualnej systemu Linux IaaS lub systemu Windows za pomocą szablonu Menedżera zasobów szyfrowania dysków Azure lub poleceń cmdlet programu PowerShell i określić konfigurację odszyfrowywania.

##### <a name="windows-vm"></a>Maszyna wirtualna z systemem Windows
Wyłącz szyfrowanie krok wyłącza funkcję szyfrowania systemu operacyjnego i/lub ilość danych na Maszynie wirtualnej uruchomionej IaaS systemu Windows. Nie można wyłączyć woluminu systemu operacyjnego i pozostawić ilość danych zaszyfrowanych. Po wykonaniu kroku Wyłącz szyfrowanie Azure klasycznym modelu wdrażania aktualizacji modelu usług maszyny Wirtualnej i maszyn wirtualnych IaaS systemu Windows jest oznaczony jako odszyfrowany. Zawartość maszyny Wirtualnej nie są szyfrowane, gdy. Odszyfrowywanie nie powoduje usunięcia magazynu kluczy i materiału klucza szyfrowania (klucze szyfrowania funkcji BitLocker dla systemów Windows i hasło dla systemu Linux).

##### <a name="linux-vm"></a>Linux VM
Wyłącz szyfrowanie krok wyłącza szyfrowanie danych woluminu na Maszynie wirtualnej uruchomionej IaaS systemu Linux. Ten krok działa tylko, jeśli nie jest zaszyfrowany dysk systemu operacyjnego.

> [!NOTE]
> Wyłączenie szyfrowania na dysku systemu operacyjnego nie jest dozwolona na maszynach wirtualnych systemu Linux.

##### <a name="disable-encryption-on-an-existing-or-running-iaas-vm"></a>Wyłącz szyfrowanie na maszynie Wirtualnej IaaS istniejących lub nie działają
Można wyłączyć szyfrowania dysków na uruchomionych maszyn wirtualnych IaaS systemu Windows za pomocą [szablonu usługi Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm).

1. W szablonie szybki start Azure, kliknij polecenie **wdrażanie na platformie Azure**, wprowadź konfiguracji odszyfrowywania **parametry** bloku, a następnie kliknij przycisk **OK**.

2. Wybierz subskrypcję, grupy zasobów, lokalizacja grupy zasobów, postanowienia prawne i umowę, a następnie kliknij przycisk **Utwórz** Aby włączyć szyfrowanie na nowej maszyny Wirtualnej IaaS.

Dla maszyn wirtualnych systemu Linux, można wyłączyć szyfrowania za pomocą [wyłączyć szyfrowania na uruchomionej maszyny Wirtualnej systemu Linux](https://aka.ms/decrypt-linuxvm) szablonu.

Poniższa tabela zawiera listę parametrów szablonu usługi Resource Manager wyłączenie szyfrowania na uruchomionej maszynie Wirtualnej IaaS:

| Parametr | Opis |
| --- | --- |
| vmName | Nazwa maszyny Wirtualnej, można wykonać w operacji szyfrowania.
| volumeType | Typ operacji odszyfrowywania odbywa się na wolumin. Prawidłowe wartości to _OS_, _danych_, i _wszystkich_. Nie można wyłączyć szyfrowania na uruchomienie woluminu systemu operacyjnego maszyny Wirtualnej systemu Windows IaaS/rozruchowego bez wyłączenie szyfrowania na _danych_ woluminu. Należy również zauważyć, że wyłączenie szyfrowania na dysku systemu operacyjnego nie jest dozwolona na maszynach wirtualnych systemu Linux. |
| sequenceVersion | Wersja sekwencji operacji funkcji BitLocker. Zwiększ numer wersji, za każdym razem, gdy operacja odszyfrowywania dysku jest wykonywana na tej samej maszyny Wirtualnej. |

##### <a name="disable-encryption-on-an-existing-or-running-iaas-vm"></a>Wyłącz szyfrowanie na maszynie Wirtualnej IaaS istniejących lub nie działają
Aby wyłączyć szyfrowania na maszynie Wirtualnej IaaS istniejących lub jest uruchomiony przy użyciu polecenia cmdlet programu PowerShell, zobacz [ `Disable-AzureRmVMDiskEncryption` ](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption). To polecenie cmdlet obsługuje zarówno systemu Windows, jak i maszyn wirtualnych systemu Linux. Aby wyłączyć szyfrowanie, instaluje rozszerzenia na maszynie wirtualnej. Jeśli _nazwa_ parametr nie zostanie określony, rozszerzenie o nazwie domyślnej _AzureDiskEncryption za dla maszyn wirtualnych systemu Windows_ jest tworzony.

Na maszynach wirtualnych systemu Linux rozszerzenie AzureDiskEncryptionForLinux jest używane.

> [!NOTE]
> To polecenie cmdlet ponowny rozruch maszyny wirtualnej.

### <a name="enable-encryption-on-pre-encrypted-iaas-vm-with-azure-managed-disk"></a>Włącz szyfrowanie dla zaszyfrowane wstępnie IaaS maszynę Wirtualną za pomocą dysku zarządzanego Azure
Szablon Azure ARM zarządzane dysku służy do tworzenia zaszyfrowanego maszyny Wirtualnej z dysku VHD zaszyfrowane wstępnie przy użyciu szablonu ARM znajduje się w   
[Utwórz nowe zaszyfrowanych dysków zarządzanych z zaszyfrowane wstępnie obiektu blob dysku VHD/magazynu] (https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)

### <a name="enable-encryption-on-a-new-linux-iaas-vm-with-azure-managed-disk"></a>Włącz szyfrowanie dla nowej Linux IaaS maszyny Wirtualnej z dyskiem zarządzane Azure
Szablon Azure ARM zarządzane dysku służy do tworzenia nowych zaszyfrowanych IaaS Maszynę wirtualną systemu Linux przy użyciu szablonu ARM znajduje się w   
[Wdrażanie RHEL 7.2 z pełne szyfrowanie dysków] (https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-full-disk-encrypted-rhel)

### <a name="enable-encryption-on-a-new-windows-iaas-vm-with-azure-managed-disk"></a>Włącz szyfrowanie dla nowej maszyny Wirtualnej IaaS systemu Windows z dysku zarządzanego Azure
 Szablon Azure ARM zarządzane dysku służy do tworzenia nowych zaszyfrowanych IaaS Maszynę wirtualną systemu Linux przy użyciu szablonu ARM znajduje się w   
 [Tworzenie nowych zaszyfrowanych systemu Windows IaaS zarządzane dysku maszyny Wirtualnej z obrazu galerii] (https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image-managed-disks)

  > [!NOTE]
  >Jest to konieczne do migawki i/lub na dysku zarządzanego kopia zapasowa, wystąpienie maszyny Wirtualnej poza i przed włączeniem szyfrowania dysków Azure.  Migawki dysków zarządzanych może zostać pobrany z portalu, lub kopia zapasowa Azure można używać.  Kopie zapasowe upewnij się, że opcja odzyskiwania jest możliwe w przypadku dowolnego nieoczekiwany błąd podczas szyfrowania.  Po utworzeniu kopii zapasowej polecenia cmdlet Set-AzureRmVMDiskEncryptionExtension może być używany do szyfrowania dysków zarządzanych, określając parametr - skipVmBackup.  To polecenie zakończy się niepowodzeniem przed dysków zarządzanych, na podstawie maszyny Wirtualnej, dopóki nie wykonano kopii zapasowej i podano tego parametru.    
 
### <a name="update-encryption-settings-of-an-existing-encrypted-non-premium-vm"></a>Aktualizowanie ustawień szyfrowania z istniejącej maszyny Wirtualnej z systemem innym niż premium zaszyfrowane
  Użyj istniejących interfejsów szyfrowania obsługiwane dysku platformy Azure do uruchamiania maszyny Wirtualnej [PS poleceń cmdlet, interfejsu wiersza polecenia lub ARM szablonów] można zaktualizować ustawień szyfrowania AAD klient identyfikator/klucz tajny, klucz szyfrowania klucza [KEK], klucz szyfrowania funkcją BitLocker dla maszyny Wirtualnej systemu Windows lub hasło dla maszyny Wirtualnej systemu Linux itp. Ustawienie szyfrowania aktualizacji jest obsługiwana zarówno — wersja premium, jak i inne niż premium magazynu maszyn wirtualnych.

## <a name="appendix"></a>Dodatek
### <a name="connect-to-your-subscription"></a>Nawiązywanie połączenia z subskrypcją
Przed kontynuowaniem należy przejrzeć *wymagania wstępne* w tym artykule. Po upewnieniu się, że zostały spełnione wszystkie wymagania wstępne, podłącz do subskrypcji, w następujący sposób:

1. Uruchom sesję programu PowerShell Azure i zaloguj się do konta platformy Azure przy użyciu następującego polecenia:

    `Login-AzureRmAccount`

2. Jeśli masz wiele subskrypcji i chcesz określić, należy użyć, wpisz następujące polecenie, aby zobaczyć subskrypcje dla swojego konta:

    `Get-AzureRmSubscription`

3. Aby określić subskrypcję, której chcesz użyć, wpisz:

    `Select-AzureRmSubscription -SubscriptionName <Yoursubscriptionname>`

4. Aby sprawdzić, czy skonfigurowane subskrypcja jest prawidłowa, wpisz:

    `Get-AzureRmSubscription`

5. Aby upewnić się, że są zainstalowane polecenia cmdlet szyfrowania dysków Azure, wpisz:

    `Get-command *diskencryption*`

6. Następujące dane wyjściowe stanowią potwierdzenie instalacji programu PowerShell szyfrowania dysków Azure:

```
    PS C:\Windows\System32\WindowsPowerShell\v1.0> get-command *diskencryption*
    CommandType  Name                                         Source                                                             
    Cmdlet       Get-AzureRmVMDiskEncryptionStatus            AzureRM.Compute                                                    
    Cmdlet       Disable-AzureRmVMDiskEncryption              AzureRM.Compute                                                    
    Cmdlet       Set-AzureRmVMDiskEncryptionExtension         AzureRM.Compute                                                     
```

### <a name="prepare-a-pre-encrypted-windows-vhd"></a>Przygotowanie wstępnie zaszyfrowanego dysku VHD systemu Windows
W kolejnych sekcjach są niezbędne do przygotowania wstępnie zaszyfrowanego dysku VHD systemu Windows do wdrożenia jako zaszyfrowanego dysku VHD w programie Azure IaaS. Skorzystaj z informacji do przygotowania i rozruch świeże systemu Windows maszyny Wirtualnej (VHD) dla usługi Azure Site Recovery lub Azure.

#### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>Aktualizacja zasad grupy, aby umożliwić bez modułu TPM do ochrony systemu operacyjnego
Skonfiguruj ustawienia zasad grupy funkcji BitLocker **szyfrowania dysków funkcją BitLocker**, które znajdują się w obszarze **lokalnych zasad komputera** > **Konfiguracja komputera**  >  **Szablony administracyjne** > **składniki systemu Windows**. To ustawienie, aby zmienić **dyski z systemem operacyjnym** > **wymaga dodatkowego uwierzytelniania przy uruchamianiu** > **Zezwalaj na funkcję BitLocker bez zgodny moduł TPM**, jak pokazano na poniższej ilustracji:

![Ochrona przed złośliwym kodem zapewniana przez Microsoft na platformie Azure](./media/azure-security-disk-encryption/disk-encryption-fig8.png)

#### <a name="install-bitlocker-feature-components"></a>Zainstaluj składniki funkcji BitLocker
W systemie Windows Server 2012 i nowszych Użyj następującego polecenia:

    dism /online /Enable-Feature /all /FeatureName:BitLocker /quiet /norestart

Dla systemu Windows Server 2008 R2 należy użyć następującego polecenia:

    ServerManagerCmd -install BitLockers

#### <a name="prepare-the-os-volume-for-bitlocker-by-using-bdehdcfg"></a>Przygotowanie woluminu systemu operacyjnego dla funkcji BitLocker przy użyciu `bdehdcfg`
Aby skompresować partycji systemu operacyjnego i przygotowuje komputer do używania funkcji BitLocker, uruchom następujące polecenie:

    bdehdcfg -target c: shrink -quiet

#### <a name="protect-the-os-volume-by-using-bitlocker"></a>Ochrona woluminu systemu operacyjnego za pomocą funkcji BitLocker
Użyj [ `manage-bde` ](https://technet.microsoft.com/library/ff829849.aspx) polecenie, aby włączyć szyfrowanie na wolumin rozruchowy przy użyciu zewnętrznego funkcji ochrony klucza. Również umieścić klucz zewnętrzny (plik .bek), na zewnętrznym dysku lub woluminie. Szyfrowanie jest włączone na wolumin systemowy/rozruchowy po kolejnym rozruchu.

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

> [!NOTE]
> Przygotowanie maszyny Wirtualnej z oddzielnych danych/zasobów wirtualnego dysku twardego do pobierania klucza zewnętrznego za pomocą funkcji BitLocker.

#### <a name="encrypting-an-os-drive-on-a-running-linux-vm"></a>Szyfrowanie dysku systemu operacyjnego na uruchomionej maszyny Wirtualnej systemu Linux

##### <a name="prerequisites-for-os-disk-encryption"></a>Wymagania wstępne dotyczące szyfrowania dysku systemu operacyjnego

* Maszyna wirtualna musi być przy użyciu rozkładu zgodne z szyfrowania dysku systemu operacyjnego wymienionych w [FAQ szyfrowania dysków Azure](https://docs.microsoft.com/en-us/azure/security/azure-security-disk-encryption-faq#what-linux-distributions-does-azure-disk-encryption-support) 
* Maszyna wirtualna musi zostać utworzony z obrazu witryny Marketplace usługi Azure Resource Manager.
* Maszyna wirtualna platformy Azure z co najmniej 4 GB pamięci RAM (zalecany rozmiar to 7 GB).
* (W przypadku RHEL i CentOS) Wyłącz SELinux. To disable SELinux, see "4.4.2. Wyłączanie SELinux"w [Przewodnik administratora i użytkownika SELinux](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux) na maszynie Wirtualnej.
* Po wyłączeniu SELinux co najmniej raz ponowny rozruch maszyny Wirtualnej.

##### <a name="steps"></a>Kroki
1. Utwórz maszynę Wirtualną za pomocą jednego z dystrybucji określony wcześniej.

 7.2 CentOS szyfrowania dysku systemu operacyjnego jest obsługiwane za pomocą specjalnego obrazu. Aby użyć tego obrazu, należy określić "7.2n" jako jednostka SKU, podczas tworzenia maszyny Wirtualnej:
 ```
    Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"
 ```
2. Konfigurowanie maszyny Wirtualnej zgodnie z potrzebami. Jeśli zamierzasz do szyfrowania wszystkich (systemu operacyjnego i danych), dyski muszą być /etc/fstab określonego i instalację z dysków z danymi.

 > [!NOTE]
 > Użyj identyfikatora UUID =... Aby określić dyski danych w fstab/etc/zamiast określania nazwy urządzenia bloku (na przykład sdb1/dev /). Podczas szyfrowania kolejność dysków zmienia się na maszynie Wirtualnej. Jeśli maszyna wirtualna opiera się na określonej kolejności bloku urządzeń, nie będzie można zainstalować je po szyfrowania.

3. Wyloguj sesji SSH.

4. Aby zaszyfrować systemu operacyjnego, należy określić volumeType jako **wszystkie** lub **systemu operacyjnego** podczas możesz [włączyć szyfrowanie](#enable-encryption-on-existing-or-running-iaas-linux-vm-in-azure).

 > [!NOTE]
 > Wszystkie procesy przestrzeń użytkownika, które nie są uruchomione jako `systemd` usługi powinny skasowane z `SIGKILL`. Ponowny rozruch maszyny Wirtualnej. Po włączeniu szyfrowania dysku systemu operacyjnego na uruchomionej maszynie Wirtualnej, należy zaplanować przestój maszyny Wirtualnej.

5. Okresowo monitorować postęp szyfrowania zgodnie z instrukcjami podanymi w [następnej sekcji](#monitoring-os-encryption-progress).

6. Po Get AzureRmVmDiskEncryptionStatus zawiera "VMRestartPending", należy ponownie uruchomić maszyny Wirtualnej, logując się do niego lub za pomocą portalu, programu PowerShell lub interfejsu wiersza polecenia.
    ```
    C:\> Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, reboot the VM
    ```
Przed ponownym, zaleca się zapisanie [diagnostyki rozruchu](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/) maszyny wirtualnej.

#### <a name="monitoring-os-encryption-progress"></a>Monitorowanie postępu szyfrowania systemu operacyjnego
Możesz monitorować postęp szyfrowania systemu operacyjnego na trzy sposoby:

* Użyj `Get-AzureRmVmDiskEncryptionStatus` polecenia cmdlet i przejrzyj pole postępu:
    ```
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started
    ```
 Po maszyny Wirtualnej osiągnie "Rozpoczęto szyfrowania dysku systemu operacyjnego", trwa około 40 do 50 minut na magazyn Premium kopii maszyny Wirtualnej.

 Z powodu [wystawiać #388](https://github.com/Azure/WALinuxAgent/issues/388) w WALinuxAgent, `OsVolumeEncrypted` i `DataVolumesEncrypted` wyświetlane jako `Unknown` w niektórych dystrybucji. Z WALinuxAgent wersji 2.1.5 i później, ten problem zostanie rozwiązany automatycznie. Jeśli widzisz `Unknown` w danych wyjściowych, można sprawdzić stan szyfrowanie dysków za pomocą Eksploratora zasobów Azure.

 Przejdź do [Eksploratora zasobów Azure](https://resources.azure.com/), a następnie rozwiń węzeł tej hierarchii w panelu wyboru po lewej stronie:

 ~~~~
 |-- subscriptions
     |-- [Your subscription]
          |-- resourceGroups
               |-- [Your resource group]
                    |-- providers
                         |-- Microsoft.Compute
                              |-- virtualMachines
                                   |-- [Your virtual machine]
                                        |-- InstanceView
~~~~                

 W InstanceView przewiń w dół aby zobaczyć stan szyfrowania dysków.

 ![Widok wystąpienia maszyny Wirtualnej](./media/azure-security-disk-encryption/vm-instanceview.png)

* Przyjrzyj się [diagnostyki rozruchu](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/). Komunikaty z rozszerzeniem ADE powinien być poprzedzony `[AzureDiskEncryption]`.

* Zaloguj się do maszyny Wirtualnej za pośrednictwem SSH i Pobierz dziennik rozszerzenia z:

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

 Zaleca się, że nie logowania się do maszyny Wirtualnej w trakcie szyfrowania systemu operacyjnego. Skopiuj dzienniki tylko wtedy, gdy te dwie metody nie powiodło się.

#### <a name="prepare-a-pre-encrypted-linux-vhd"></a>Przygotowanie wstępnie zaszyfrowanego dysku VHD systemu Linux
##### <a name="ubuntu-16"></a>Ubuntu 16
Skonfigurować szyfrowanie podczas instalacji dystrybucji, wykonując następujące czynności:

1. Wybierz **skonfigurować zaszyfrowanych woluminach** po partycji dysków.

 ![Ubuntu 16.04 Setup](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2. Utwórz dysk rozruchowy oddzielne, nie muszą być szyfrowane. Szyfrowanie dysku głównym.

 ![Ubuntu 16.04 Setup](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3. Należy podać hasło. Jest to hasło, które można przekazać do magazynu kluczy.

 ![Ubuntu 16.04 Setup](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4. Zakończ partycjonowania.

 ![Ubuntu 16.04 Setup](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. Po rozruchu maszyny Wirtualnej i są wyświetlane pytanie o hasło, należy używać hasło podane w kroku 3.

 ![Ubuntu 16.04 Setup](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6. Przygotowywanie maszyny Wirtualnej do przekazywania do platformy Azure przy użyciu [tych instrukcji](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-ubuntu/). Nie należy uruchamiać w ostatnim kroku (anulowania obsługi maszyny Wirtualnej) jeszcze.

Konfigurowanie szyfrowania do pracy z platformy Azure, wykonując następujące czynności:

1. Utwórz plik w obszarze /usr/local/sbin/azure_crypt_key.sh, z zawartością w poniższym skrypcie. Należy zwrócić uwagę KeyFileName, ponieważ jest to nazwa pliku hasło używane przez platformę Azure.

    ```
    #!/bin/sh
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint
    modprobe vfat >/dev/null 2>&1
    modprobe ntfs >/dev/null 2>&1
    sleep 2
    OPENED=0
    cd /sys/block
    for DEV in sd*; do

        echo "> Trying device: $DEV ..." >&2
        mount -t vfat -r /dev/${DEV}1 $MountPoint >/dev/null||
        mount -t ntfs -r /dev/${DEV}1 $MountPoint >/dev/null
        if [ -f $MountPoint/$KeyFileName ]; then
                cat $MountPoint/$KeyFileName
                umount $MountPoint 2>/dev/null
                OPENED=1
                break
        fi
        umount $MountPoint 2>/dev/null
    done

      if [ $OPENED -eq 0 ]; then
        echo "FAILED to find suitable passphrase file ..." >&2
        echo -n "Try to enter your password: " >&2
        read -s -r A </dev/console
        echo -n "$A"
     else
        echo "Success loading keyfile!" >&2
    fi
```

2. Zmienianie konfiguracji kryptograficznego w */etc/crypttab*. Powinny wyglądać następująco:
 ```
    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh
    ```

3. Jeśli edytujesz *azure_crypt_key.sh* w systemie Windows i zostanie skopiowana do systemu Linux, uruchom `dos2unix /usr/local/sbin/azure_crypt_key.sh`.

4. Dodaj uprawnienia pliku wykonywalnego do skryptu:
 ```
    chmod +x /usr/local/sbin/azure_crypt_key.sh
 ```
5. Edytuj */etc/initramfs-tools/modules* przez dołączenie wiersze:
 ```
    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1
```
6. Uruchom `update-initramfs -u -k all` initramfs, aby zaktualizować `keyscript` zostały zastosowane.

7. Teraz można anulowanie zastrzeżenia maszyny Wirtualnej.

 ![Ubuntu 16.04 Setup](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig6.png)

8. Przejdź do następnego kroku i [przekazać dysk VHD](#upload-encrypted-vhd-to-an-azure-storage-account) na platformie Azure.

##### <a name="opensuse-132"></a>openSUSE 13.2
Aby skonfigurować szyfrowanie podczas instalacji dystrybucji, wykonaj następujące czynności:
1. Gdy partycji dysków, wybierz **szyfrowania woluminu grupy**, a następnie wprowadź hasło. Jest to hasło, który trzeba będzie przekazać do magazynu kluczy.

 ![openSUSE 13.2 Setup](./media/azure-security-disk-encryption/opensuse-encrypt-fig1.png)

2. Uruchom maszynę Wirtualną przy użyciu hasła.

 ![openSUSE 13.2 Setup](./media/azure-security-disk-encryption/opensuse-encrypt-fig2.png)

3. Przygotowywanie maszyny Wirtualnej do przekazywania do platformy Azure zgodnie z instrukcjami w [przygotowanie SLES lub openSUSE maszyny wirtualnej na platformie Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-suse-create-upload-vhd/#prepare-opensuse-131). Nie należy uruchamiać w ostatnim kroku (anulowania obsługi maszyny Wirtualnej) jeszcze.

Aby skonfigurować szyfrowanie do pracy z platformą Azure, wykonaj następujące czynności:
1. Edytuj /etc/dracut.conf i Dodaj następujący wiersz:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```
2. Komentarz te wiersze do końca /usr/lib/dracut/modules.d/90crypt/module-setup.sh pliku:
 ```
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
 ```

3. Dołącz następujący wiersz na początku /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh pliku:
 ```
    DRACUT_SYSTEMD=0
 ```
I zmień wszystkich wystąpień:
 ```
    if [ -z "$DRACUT_SYSTEMD" ]; then
 ```
na:
```
    if [ 1 ]; then
```
4. Edytuj /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh i dołącza je do "# Otwórz LUKS urządzenie":

    ```
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```
5. Uruchom `/usr/sbin/dracut -f -v` initrd aktualizacji.

6. Teraz można anulowanie zastrzeżenia maszyny Wirtualnej i [przekazać dysk VHD](#upload-encrypted-vhd-to-an-azure-storage-account) na platformie Azure.

##### <a name="centos-7"></a>CentOS 7
Aby skonfigurować szyfrowanie podczas instalacji dystrybucji, wykonaj następujące czynności:
1. Wybierz **szyfrowania danych** po partycji dysków.

 ![Instalator centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig1.png)

2. Upewnij się, że **Szyfruj** jest zaznaczone dla partycji katalogu głównego.

 ![Instalator centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig2.png)

3. Należy podać hasło. Jest to hasło, które możesz przekazać do magazynu kluczy.

 ![Instalator centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig3.png)

4. Po rozruchu maszyny Wirtualnej i są wyświetlane pytanie o hasło, należy używać hasło podane w kroku 3.

 ![Instalator centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig4.png)

5. Przygotowywanie maszyny Wirtualnej do przekazywania na platformie Azure przy użyciu instrukcji "CentOS 7.0 +" w [przygotowanie maszyny wirtualnej CentOS, oparty na platformie Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-centos/#centos-70). Nie należy uruchamiać w ostatnim kroku (anulowania obsługi maszyny Wirtualnej) jeszcze.

6. Teraz można anulowanie zastrzeżenia maszyny Wirtualnej i [przekazać dysk VHD](#upload-encrypted-vhd-to-an-azure-storage-account) na platformie Azure.

Aby skonfigurować szyfrowanie do pracy z platformą Azure, wykonaj następujące czynności:

1. Edytuj /etc/dracut.conf i Dodaj następujący wiersz:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```

2. Komentarz te wiersze do końca /usr/lib/dracut/modules.d/90crypt/module-setup.sh pliku:
```
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
```

3. Dołącz następujący wiersz na początku /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh pliku:
```
    DRACUT_SYSTEMD=0
```
I zmień wszystkich wystąpień:
```
    if [ -z "$DRACUT_SYSTEMD" ]; then
```
na
```
    if [ 1 ]; then
```
4. Edytuj /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh i Dołącz po "# Otwórz LUKS urządzenie":
    ```
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```    
5. Uruchom "/ usr/sbin/dracut - f - v" initrd aktualizacji.

![Instalator centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig5.png)

### <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Przekazanie zaszyfrowanego dysku VHD do konta magazynu platformy Azure
Po włączeniu szyfrowania funkcją BitLocker lub szyfrowania DM-Crypt lokalnego zaszyfrowanego dysku VHD musi można przekazać do konta magazynu.

    Add-AzureRmVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]

### <a name="upload-the-disk-encryption-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>Przekaż hasło szyfrowania dysków dla maszyny Wirtualnej zaszyfrowane wstępnie do magazynu kluczy
Klucz tajny szyfrowanie dysków, które zostały uzyskane musi wcześniej przekazany jako klucza tajnego w magazynie kluczy. Magazyn kluczy musi mieć uprawnienia, włączona dla klienta usługi Azure AD i szyfrowanie dysków.

    $AadClientId = "YourAADClientId"
    $AadClientSecret = "YourAADClientSecret"

    $key vault = New-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

    Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
    Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption


#### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>Hasło szyfrowania dysku nie jest zaszyfrowany przy KEK
Aby skonfigurować klucza tajnego w magazynie kluczy, należy użyć [AzureKeyVaultSecret zestawu](/powershell/module/azurerm.keyvault/set-azurekeyvaultsecret). Jeśli masz maszyny wirtualnej systemu Windows, plik bek jest zakodowany jako ciąg base64 i następnie przekazywane do przy użyciu magazynu kluczy `Set-AzureKeyVaultSecret` polecenia cmdlet. Dla systemu Linux hasło jest zakodowany jako ciąg base64, a następnie przekazać do magazynu kluczy. Ponadto upewnij się, że następujące znaczniki są ustawione podczas tworzenia klucza tajnego w magazynie kluczy.

    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $secretName = [guid]::NewGuid().ToString()
    $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
    $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

    $secret = Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
    $secretUrl = $secret.Id

Użyj `$secretUrl` w następnym kroku dla [dołączanie dysku systemu operacyjnego bez użycia KEK](#without-using-a-kek).

#### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>Hasło szyfrowania dysku zaszyfrowane za pomocą KEK
Przed przekazaniem klucza tajnego do magazynu kluczy, można opcjonalnie zaszyfrować przy użyciu klucza szyfrowania. Użyj zawijania [interfejsu API](https://msdn.microsoft.com/library/azure/dn878066.aspx) najpierw szyfrowania klucza tajnego za pomocą klucza szyfrowania. Wynik tej operacji zawijania jest base64 ciągu zakodowane w adresie URL, który można następnie przekazać jako klucz tajny przy użyciu [ `Set-AzureKeyVaultSecret` ](/powershell/module/azurerm.keyvault/set-azurekeyvaultsecret) polecenia cmdlet.

    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzureKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzureKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id

Użyj `$KeyEncryptionKey` i `$secretUrl` w następnym kroku dla [dołączanie dysku systemu operacyjnego przy użyciu KEK](#using-a-kek).

### <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>Określ adres URL tajne, po podłączeniu dysku systemu operacyjnego
#### <a name="without-using-a-kek"></a>Bez użycia KEK
Podczas dołączania dysk systemu operacyjnego, musisz przekazać `$secretUrl`. Adres URL został wygenerowany w sekcji "nie jest zaszyfrowany przy KEK tajny szyfrowanie dysków".

    Set-AzureRmVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl

#### <a name="using-a-kek"></a>Przy użyciu KEK
Po podłączeniu dysku systemu operacyjnego, należy przekazać `$KeyEncryptionKey` i `$secretUrl`. Adres URL został wygenerowany w sekcji "nie jest zaszyfrowany przy KEK tajny szyfrowanie dysków".

    Set-AzureRmVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id

## <a name="for-more-information"></a>Więcej informacji
[Eksploruj szyfrowania dysków Azure przy użyciu programu Azure PowerShell — część 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/16/explore-azure-disk-encryption-with-azure-powershell.aspx?wa=wsignin1.0)  
[Eksploruj szyfrowania dysków Azure przy użyciu programu Azure PowerShell — część 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx)
