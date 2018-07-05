---
title: Usługa Azure Disk Encryption for Windows i maszyn wirtualnych IaaS z systemem Linux | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie Microsoft Azure dysku Encryption for Windows i maszyn wirtualnych IaaS z systemem Linux.
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
ms.author: devtiw
ms.openlocfilehash: f350716d0ca906376f3eadce9e117694ff14515c
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/04/2018
ms.locfileid: "35756400"
---
# <a name="azure-disk-encryption-for-windows-and-linux-iaas-vms"></a>Usługa Azure Disk Encryption for Windows i maszyn wirtualnych IaaS z systemem Linux
Platforma Microsoft Azure jest zdecydowanie wszelkich starań, aby można było zapewnienie prywatności danych, niezależność danych i umożliwia sterowanie subskrypcji platformy Azure hostowanej danych za pomocą wielu nowoczesnych technologii szyfrowania, kontrolowania i zarządzać kluczami szyfrowania, kontrola i inspekcja dostępu do danych. Klienci platformy Azure zapewnia elastyczność wyboru rozwiązania, który najlepiej zaspokaja ich potrzeby biznesowe. W tym dokumencie możemy przedstawiono nowe rozwiązanie technologiczne "Usługa Azure Disk Encryption for Windows i Linux maszyn wirtualnych IaaS w" Aby chronić i chronić dane zgodnie z wymaganiami co do bezpieczeństwa organizacji i zobowiązaniami w zakresie zgodności. Ten dokument zawiera szczegółowe wskazówki dotyczące sposobu używania funkcji szyfrowania dysków Azure w tym obsługiwane scenariusze i użytkownik napotka.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]


## <a name="overview"></a>Przegląd
Usługa Azure Disk Encryption to nowa funkcja ułatwiająca szyfrowanie dysków maszyn wirtualnych IaaS systemu Linux i Windows. Usługa Azure Disk Encryption korzysta z branżowymi [funkcji BitLocker](https://technet.microsoft.com/library/cc732774.aspx) funkcja systemu Windows i [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) funkcji systemu Linux, aby zapewnić szyfrowanie woluminów systemu operacyjnego i dysków z danymi. To rozwiązanie jest zintegrowana z usługą [usługi Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) ułatwiają kontrolowanie i zarządzanie kluczami szyfrowania dysku i wpisami tajnymi w ramach subskrypcji usługi key vault. Rozwiązanie zapewnia również, że wszystkie dane na dyskach maszyn wirtualnych są szyfrowane, gdy w usłudze Azure storage.

Usługa Azure disk encryption for Windows i maszyn wirtualnych IaaS z systemem Linux jest teraz w **ogólnie** we wszystkich publicznych regionach platformy Azure i regiony AzureGov dla standardowych maszyn wirtualnych i maszyn wirtualnych dzięki usłudze premium storage.

> [!NOTE]
> Zastosowanie niektórych zaleceń zamieszczonych może zwiększyć danych, sieci lub użycia zasobów obliczeniowych, wynikiem dodatkowych kosztów licencji lub subskrypcji.


### <a name="encryption-scenarios"></a>Scenariuszy szyfrowania
Rozwiązanie Azure Disk Encryption obsługuje następujących scenariuszy:

* Włącz szyfrowanie dla nowych maszyn wirtualnych IaaS utworzone na podstawie wstępnie zaszyfrowany dysk VHD i klucze szyfrowania
* Włącz szyfrowanie dla nowych maszyn wirtualnych IaaS utworzone na podstawie obsługiwanych obrazów w galerii platformy Azure
* Włącz szyfrowanie dla istniejących maszyn wirtualnych IaaS działających na platformie Azure
* Wyłącz szyfrowanie dla systemu Windows IaaS VMs
* Wyłącz szyfrowanie dla dysków z danymi dla maszyn wirtualnych IaaS z systemem Linux
* Włącz szyfrowanie maszyn wirtualnych dysku zarządzanego
* Aktualizowanie ustawień szyfrowania zaszyfrowanych istniejących w warstwie premium i innych niż premium magazyn maszyny Wirtualnej
* Kopia zapasowa i przywracanie zaszyfrowanych maszyn wirtualnych

Rozwiązanie obsługuje następujące scenariusze dla maszyn wirtualnych IaaS, gdy są one włączone w systemie Microsoft Azure:

* Integracja z usługą Azure Key Vault
* Maszyny wirtualne w warstwie standardowa: [A, D, DS, G, GS, F i tak dalej serię maszyn wirtualnych IaaS](https://azure.microsoft.com/pricing/details/virtual-machines/)
* Włącz szyfrowanie dla Windows i maszyn wirtualnych IaaS z systemem Linux i maszyn wirtualnych dysku zarządzanego przy użyciu obsługiwanych obrazów w galerii platformy Azure
* Wyłącz szyfrowanie dla dysków systemu operacyjnego i danych dla maszyn wirtualnych IaaS Windows i maszyn wirtualnych dysku zarządzanego
* Wyłącz szyfrowanie dla dysków z danymi dla maszyn wirtualnych IaaS z systemem Linux i maszyn wirtualnych dysku zarządzanego
* Włącz szyfrowanie dla maszyn wirtualnych IaaS z systemem operacyjnym Windows klienta
* Włącz szyfrowanie dla woluminów przy użyciu ścieżki instalacji
* Włącz szyfrowanie dla maszyn wirtualnych systemu Linux skonfigurowany przy użyciu dysku przy użyciu mdadm rozkładanie (RAID)
* Włącz szyfrowanie dla maszyn wirtualnych systemu Linux przy użyciu LVM dla dysków z danymi
* Włącz szyfrowanie dla systemu Linux LVM 7.3 dla systemu operacyjnego i dysków z danymi 
* Włącz szyfrowanie dla Windows maszyny wirtualne skonfigurowane przy użyciu funkcji miejsca do magazynowania
* Aktualizowanie ustawień szyfrowania zaszyfrowanych istniejących w warstwie premium i innych niż premium magazyn maszyny Wirtualnej
* Kopia zapasowa i przywracanie zaszyfrowanych maszyn wirtualnych, zarówno dla klucza KEK nie i scenariuszy KEK (KEK - klucz szyfrowania klucza)
* Wszystkie publiczne platformy Azure i AzureGov regiony są obsługiwane

Rozwiązanie nie obsługuje następujących scenariuszy, funkcji i technologii:

* Warstwa podstawowa maszyn wirtualnych IaaS
* Wyłączenie szyfrowania na dysku systemu operacyjnego dla maszyn wirtualnych IaaS z systemem Linux
* Wyłączenie szyfrowania na dysku danych, jeśli dysk systemu operacyjnego został zaszyfrowany dla maszyn wirtualnych Iaas z systemem Linux
* Maszyny wirtualne IaaS, które są tworzone za pomocą klasycznego metodę tworzenia maszyny Wirtualnej
* Włącz szyfrowanie dla Windows i maszyn wirtualnych IaaS z systemem Linux, niestandardowe obrazy klienta nie jest obsługiwana.
* Integracja z usługą zarządzania kluczami w środowisku lokalnym
* Usługa pliki systemu Azure (udostępnionego systemu plików), Network File System (NFS), dynamiczne woluminy i maszyn wirtualnych Windows, które są skonfigurowane przy użyciu systemów programowej macierzy RAID

### <a name="encryption-features"></a>Funkcje szyfrowania
Po włączeniu i wdrożeniu usługi Azure Disk Encryption maszyn wirtualnych IaaS platformy Azure, następujące funkcje są włączone, w zależności od konfiguracji podano:

* Szyfrowanie woluminu systemu operacyjnego, aby chronić wolumin rozruchowy w stanie spoczynku w usłudze storage
* Szyfrowanie woluminów danych, aby chronić woluminy danych w spoczynku w usłudze storage
* Wyłączenie szyfrowania dla dysków systemu operacyjnego i danych dla maszyn wirtualnych IaaS Windows
* Wyłączenie szyfrowania danych dyski dla maszyn wirtualnych IaaS z systemem Linux (tylko wtedy, gdy nie jest zaszyfrowane dla dysku systemu operacyjnego)
* Ochrona kluczy szyfrowania i wpisy tajne w ramach subskrypcji usługi key vault
* Raportowanie stanu szyfrowania zaszyfrowanych maszyn wirtualnych IaaS
* Usuwanie ustawień konfiguracji szyfrowania dysku od maszyny wirtualnej IaaS
* Kopia zapasowa i przywracanie zaszyfrowanych maszyn wirtualnych przy użyciu usługi Azure Backup

Usługa Azure Disk Encryption dla maszyn wirtualnych IaaS rozwiązania Windows i Linux obejmują:

* Rozszerzenie szyfrowanie dysków dla Windows.
* Rozszerzenie szyfrowania dysku dla systemu Linux.
* Szyfrowanie dysków poleceń cmdlet programu PowerShell.
* Szyfrowania dysków apletów poleceń interfejsu wiersza polecenia platformy Azure (CLI).
* Szyfrowanie dysków szablonów usługi Azure Resource Manager.

Rozwiązanie Azure Disk Encryption jest obsługiwana na maszynach wirtualnych IaaS z uruchomionym systemem Windows lub systemu operacyjnego Linux. Aby uzyskać więcej informacji o obsługiwanych systemach operacyjnych zobacz sekcję "Wymagania wstępne".

> [!NOTE]
> Nie ma dodatkowych opłat do szyfrowania dysków maszyn wirtualnych za pomocą usługi Azure Disk Encryption.

### <a name="value-proposition"></a>Opis korzyści
Po zastosowaniu rozwiązania usługi Azure Disk Encryption-management może spełnić następujące wymagania biznesowe:

* Maszyny wirtualne IaaS są chronione w stanie spoczynku, ponieważ używasz standardowych w branży technologii umożliwiającą organizacyjnych wymagań dotyczących zabezpieczeń i zgodności.
* Przeprowadzać ich inspekcje rozruchu maszyn wirtualnych IaaS, w obszarze klucze kontrolowane przez klienta i zasady, a ich użycie w magazynie kluczy.

### <a name="encryption-workflow"></a>Szyfrowanie przepływu pracy
Aby włączyć szyfrowanie dysków dla Windows i maszyn wirtualnych systemu Linux, wykonaj następujące czynności:

1. Wybierz scenariusz szyfrowania spośród powyższych scenariuszy szyfrowania.
2. Zgódź się na włączenie szyfrowania przy użyciu szablonu Menedżera zasobów szyfrowania dysków Azure, poleceń cmdlet programu PowerShell lub interfejsu wiersza polecenia na dysku, a następnie określ konfiguracji szyfrowania.

   * W scenariuszu wirtualnego dysku twardego szyfrowane klienta należy przekazać zaszyfrowanego dysku VHD do konta magazynu i materiału klucza szyfrowania do magazynu kluczy. Następnie podaj konfiguracji szyfrowania, aby włączyć szyfrowanie na utworzonej nowej maszynie Wirtualnej IaaS.
   * W przypadku nowych maszyn wirtualnych, które są tworzone w portalu Marketplace i istniejących maszyn wirtualnych, które zostały już uruchomione na platformie Azure Podaj konfiguracji szyfrowania, aby włączyć szyfrowanie na maszynie Wirtualnej IaaS.

3. Udzielanie dostępu do platformy Azure, aby odczytać materiału klucza szyfrowania (klucze szyfrowania funkcją BitLocker dla systemów Windows) i hasło dla systemu Linux z własnego magazynu kluczy, aby włączyć szyfrowanie na maszynie Wirtualnej IaaS.

4. Podaj tożsamość aplikacji usługi Azure Active Directory (Azure AD), aby zapisać klucz szyfrowania materiału do magazynu kluczy. Ten krok umożliwia szyfrowanie na maszynie Wirtualnej IaaS w scenariuszach wymienionych w kroku 2.

5. Azure aktualizuje modelu usługi maszyny Wirtualnej przy użyciu szyfrowania i Konfiguracja usługi key vault i konfiguruje zaszyfrowanej maszyny Wirtualnej.

 ![Ochrona przed złośliwym kodem zapewniana przez Microsoft na platformie Azure](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

### <a name="decryption-workflow"></a>Odszyfrowywanie przepływu pracy
Aby wyłączyć szyfrowanie dysków dla maszyn wirtualnych IaaS, wykonaj następujące kroki ogólne:

1. Wybierz umożliwia wyłączenie szyfrowania (odszyfrowywania) na uruchomionej maszynie Wirtualnej IaaS na platformie Azure przy użyciu szablonu Menedżera zasobów szyfrowania dysków Azure lub poleceń cmdlet programu PowerShell, a następnie określ żądaną konfigurację odszyfrowywania.

 Ten krok powoduje wyłączenie szyfrowania systemu operacyjnego lub ilości danych lub obu tych programów na uruchomionej maszynie Wirtualnej IaaS Windows. Jednakże jak wspomniano w poprzedniej sekcji, wyłączenie szyfrowania dysku systemu operacyjnego dla systemu Linux nie jest obsługiwana. Krok odszyfrowywanie jest dozwolony tylko dla dysków z danymi na maszynach wirtualnych z systemem Linux, tak długo, jak dysk systemu operacyjnego nie jest zaszyfrowany.
2. Azure aktualizuje model usług maszyn wirtualnych i maszyn wirtualnych IaaS jest oznaczony odszyfrowany. Zawartość maszyny Wirtualnej nie są szyfrowane, gdy.

> [!NOTE]
> Operacja disable szyfrowania nie powoduje usunięcia magazynu kluczy i materiału klucza szyfrowania (klucze szyfrowania funkcją BitLocker dla systemów Windows) lub hasło dla systemu Linux.
 > Wyłączenie szyfrowania dysku systemu operacyjnego dla systemu Linux nie jest obsługiwane. Krok odszyfrowywania jest dozwolona tylko w przypadku dysków z danymi na maszynach wirtualnych z systemem Linux.
Wyłączenie szyfrowania dysku danych dla systemu Linux nie jest obsługiwana, jeśli dysk systemu operacyjnego został zaszyfrowany.

## <a name="prerequisites"></a>Wymagania wstępne
Przed włączeniem usługi Azure Disk Encryption na maszynach wirtualnych IaaS platformy Azure dla obsługiwanych scenariuszach, które zostały omówione w sekcji "Omówienie", zobacz następujące wymagania wstępne:

* Musi mieć prawidłową aktywną subskrypcją platformy Azure do tworzenia zasobów na platformie Azure w obsługiwanych regionach.
* Usługa Azure Disk Encryption jest obsługiwane w następujących wersjach systemu Windows Server: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 i Windows Server 2016.
* Usługa Azure Disk Encryption jest obsługiwana w następujących wersjach klienta Windows: Windows 10 i Windows 8 klienta.

> [!NOTE]
> Dla systemu Windows Server 2008 R2 musisz mieć zainstalowany przed włączeniem szyfrowania na platformie Azure program .NET Framework 4.5. Można zainstalować z witryny Windows Update, instalując opcjonalną aktualizację programu Microsoft .NET Framework 4.5.2 systemów Windows Server 2008 R2 x64 64 ([KB2901983](https://support.microsoft.com/kb/2901983)).

* Usługa Azure Disk Encryption jest tylko obsługiwana w określonych w galerii platformy Azure są dystrybucje systemu Linux serwera i wersje.  Lista aktualnie obsługiwanych wersji można znaleźć na stronie [często zadawane pytania dotyczące usługi Azure dysku szyfrowanie](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-faq).

* Usługa Azure Disk Encryption wymaga, że maszyny wirtualne i usługi key vault, na których znajdują się w tym samym regionie platformy Azure i subskrypcji.

> [!NOTE]
> Konfigurowanie zasobów w oddzielnych regionach powoduje awarię w włączenie funkcji usługi Azure Disk Encryption.

* Aby skonfigurować i skonfigurować magazyn kluczy dla usługi Azure Disk Encryption, zobacz sekcję **ustaw i konfigurowaniu magazynu kluczy dla usługi Azure Disk Encryption** w *wymagania wstępne* dalszej części tego artykułu.
* Instalowanie i konfigurowanie aplikacji usługi Azure AD w usłudze Azure Active directory dla usługi Azure Disk Encryption, sekcji **Konfigurowanie aplikacji usługi Azure AD w usłudze Azure Active Directory** w *wymagania wstępne* sekcji w tym artykule.
* Do ustawiania i konfigurowania zasad dostępu magazynu kluczy dla aplikacji Azure AD, zobacz sekcję **Konfigurowanie zasad dostępu magazynu kluczy dla aplikacji Azure AD** w *wymagania wstępne* dalszej części tego artykułu.
* Aby przygotować wirtualny dysk twardy zaszyfrowane wstępnie Windows, zobacz sekcję **przygotowanie wirtualnego dysku twardego Windows zaszyfrowane wstępnie** w *dodatku*.
* Aby przygotować zaszyfrowane wstępnie wirtualnego dysku twardego systemu Linux, zobacz sekcję **przygotowanie wirtualnego dysku twardego systemu Linux zaszyfrowane wstępnie** w *dodatku*.
* Platforma Azure wymaga dostępu do kluczy szyfrowania i wpisy tajne w magazynie kluczy, aby udostępnić je do maszyny wirtualnej podczas uruchamiania i odszyfrowuje wolumin systemu operacyjnego maszyny wirtualnej. Aby udzielić uprawnień do platformy Azure, należy ustawić **EnabledForDiskEncryption** właściwość w magazynie kluczy. Aby uzyskać więcej informacji, zobacz **zestawu i konfigurowaniu magazynu kluczy dla usługi Azure Disk Encryption** w dodatku.
* KEK adresy URL i wpisu tajnego usługi key vault, na których musi być poddany kontroli wersji. Azure wymusza to ograniczenie wersji. Nieprawidłowy klucz tajny i adresy URL KEK zobacz następujące przykłady:

  * Przykład prawidłowy adres URL wpisu tajnego:   *https://contosovault.vault.azure.net/secrets/BitLockerEncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Przykład prawidłowego adresu URL klucza KEK:   *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Usługa Azure Disk Encryption nie obsługuje określania numerów portów jako część wpisy tajne usługi key vault i KEK adresów URL. Aby zapoznać się z przykładami adresów URL obsługiwane i nieobsługiwane key vault zobacz następujące tematy:

  * Adres URL magazynu kluczy można zaakceptować  *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Adres URL dopuszczalne magazynu kluczy:   *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Aby włączyć usługi Azure Disk Encryption funkcji maszyn wirtualnych IaaS musi spełniać następujące wymagania dotyczące konfiguracji punktu końcowego sieci:
  * Aby uzyskać tokenu, połączyć się z magazynem kluczy, maszyn wirtualnych IaaS musi być w stanie połączyć się z punktem końcowym usługi Azure Active Directory, \[login.microsoftonline.com\].
  * Można zapisać klucze szyfrowania do magazynu kluczy, maszyn wirtualnych IaaS musi być możliwe nawiązanie połączenia z punktu końcowego magazynu kluczy.
  * Maszyn wirtualnych IaaS musi być możliwe nawiązanie połączenia z punktu końcowego usługi Azure storage, który hostuje repozytorium rozszerzenie platformy Azure i konto magazynu platformy Azure, w którym przechowywane są pliki wirtualnego dysku twardego.

  > [!NOTE]
  > Zasady zabezpieczeń ogranicza dostęp do Internetu z maszyn wirtualnych platformy Azure, przypadku rozwiązać poprzedni identyfikator URI i skonfigurowania określonych Reguła zezwalająca na łączności wychodzącej do adresów IP.
  >
  >Aby skonfigurować i dostęp do usługi Azure Key Vault za zaporą (zapora)https://docs.microsoft.com/azure/key-vault/key-vault-access-behind-firewall)

* Najnowszą wersję programu Azure PowerShell SDK umożliwia skonfigurowanie usługi Azure Disk Encryption. Pobierz najnowszą wersję [wersji programu Azure PowerShell](https://github.com/Azure/azure-powershell/releases)

 > [!NOTE]
  > Usługa Azure Disk Encryption nie jest obsługiwana w [Azure PowerShell SDK w wersji 1.1.0](https://github.com/Azure/azure-powershell/releases/tag/v1.1.0-January2016). Jeśli otrzymujesz błąd związany z przy użyciu programu Azure PowerShell 1.1.0, zobacz [Azure dysku szyfrowanie błędów związanych z programu Azure PowerShell 1.1.0](http://blogs.msdn.com/b/azuresecurity/archive/2016/02/10/azure-disk-encryption-error-related-to-azure-powershell-1-1-0.aspx).

* Aby uruchomić dowolne polecenie wiersza polecenia platformy Azure i skojarzyć je z subskrypcją platformy Azure, należy najpierw zainstalować interfejs wiersza polecenia platformy Azure:
  * Aby zainstalować interfejs wiersza polecenia platformy Azure i skojarzyć je z subskrypcją platformy Azure, zobacz [jak instalowanie i Konfigurowanie interfejsu wiersza polecenia Azure](../cli-install-nodejs.md).
  * Aby użyć wiersza polecenia platformy Azure dla systemów Mac, Linux i Windows za pomocą usługi Azure Resource Manager, zobacz [polecenia wiersza polecenia platformy Azure w trybie usługi Resource Manager](../virtual-machines/azure-cli-arm-commands.md).

* W przypadku szyfrowania dysku zarządzanego, jest obowiązkowa wymagań wstępnych do wykonania migawki dysku zarządzanego lub kopii zapasowej dysku poza usługi Azure Disk Encryption przed włączeniem szyfrowania.  Bez kopii zapasowej na miejscu wszelkie nieoczekiwany błąd podczas szyfrowania może spowodować dysku i maszyny Wirtualnej niedostępny bez opcji odzyskiwania.  Polecenia Set-AzureRmVMDiskEncryptionExtension nie jest obecnie wykonywanie kopii zapasowych dysków zarządzanych i zgłosi błąd, jeśli użyty dla dysku zarządzanego, o ile nie określono parametru - skipVmBackup.  Ten parametr jest niebezpieczne używać, chyba że kopia zapasowa została już dokonana poza usługi Azure Disk Encryption.   Jeśli nie określono parametru - skipVmBackup, polecenia cmdlet nie będzie wykonać kopię zapasową dysku zarządzanego przed szyfrowania.  Z tego powodu uważa się obowiązkowe warunek wstępny do upewnić się, że kopii zapasowej dysku zarządzanego maszyny Wirtualnej jest w miejscu przed włączeniem usługi Azure Disk Encryption w przypadku, gdy później wymagane jest odzyskiwanie.  
> [!NOTE]
 > Parametru - skipVmBackup nigdy nie należy używać, chyba że migawki lub kopie zapasowe dokonano już poza usługi Azure Disk Encryption. 

* Rozwiązanie Azure Disk Encryption dla maszyn wirtualnych IaaS Windows korzysta z zewnętrznego ochrony klucza funkcji BitLocker. Dla domeny dołączonym do maszyn wirtualnych, nie należy wypychać żadnych zasad grupy, które wymuszają modułu TPM funkcje ochrony kluczy. Aby uzyskać informacje o zasadach grupy "Zezwalaj na funkcję BitLocker bez zgodnego modułu TPM", zobacz [dokumentacja zasad grupy funkcji BitLocker](https://technet.microsoft.com/library/ee706521).
* Zasady funkcji BitLocker na maszynach wirtualnych przyłączonych do domeny za pomocą zasad grupy niestandardowe musi zawierać następujące ustawienie: `Configure user storage of bitlocker recovery information -> Allow 256-bit recovery key` usługi Azure Disk Encryption zakończy się niepowodzeniem, jeśli ustawienia zasad grupy niestandardowe do używania funkcji Bitlocker są niezgodne. Na komputerach, które nie miały prawidłowe zasady może być wymagane ustawienia, zastosowanie nowych zasad, wymuszając nowe zasady w celu aktualizacji (/ Force gpupdate.exe) i ponowne uruchomienie.  
* Aby utworzyć aplikację usługi Azure AD, tworzenie magazynu kluczy, lub skonfigurować istniejącego magazynu kluczy i włączenia szyfrowania, zobacz [wymagań wstępnych skrypt programu PowerShell usługi Azure Disk Encryption](https://github.com/Azure/azure-powershell/blob/master/src/ResourceManager/Compute/Commands.Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1).
* Aby skonfigurować wymagania wstępne dotyczące szyfrowania dysków za pomocą wiersza polecenia platformy Azure, zobacz [tego skryptu powłoki systemowej](https://github.com/ejarvi/ade-cli-getting-started).
* Aby użyć usługi Azure Backup do kopii zapasowej i przywracanie zaszyfrowanych maszyn wirtualnych, gdy szyfrowanie jest włączone za pomocą usługi Azure Disk Encryption, szyfrowania maszyn wirtualnych przy użyciu konfiguracji klucza usługi Azure Disk Encryption. Usługa Backup obsługuje maszyny wirtualne, które są szyfrowane za pomocą klucza KEK nie lub KEK konfiguracji. Zobacz [jak kopii zapasowej i przywracanie zaszyfrowanych maszyn wirtualnych przy użyciu szyfrowania usługi Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).

* Podczas szyfrowania woluminu systemu operacyjnego Linux, należy pamiętać, że ponowne uruchomienie maszyny Wirtualnej jest obecnie wymagane na końcu procesu. Można to zrobić za pośrednictwem portalu, programu powershell lub interfejsu wiersza polecenia.   Aby śledzić postępy, szyfrowania, okresowo sondować komunikat o stanie zwracany przez Get-AzureRmVMDiskEncryptionStatus https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus.  Po zakończeniu szyfrowania komunikatu o stanie zwróconym przez to polecenie wskazuje to. Na przykład "komunikat dotyczący postępu: dysk systemu operacyjnego zostały pomyślnie zaszyfrowane, wykonaj ponowny rozruch maszyny Wirtualnej" na tym etapie maszyna wirtualna może być uruchomiona ponownie oraz używane.  

* Usługa Azure Disk Encryption dla systemu Linux wymaga dysków danych, aby mieć zainstalowany system plików w systemie Linux przed szyfrowania

* Rekursywnie instalowane dane, które dyski nie są obsługiwane przez usługi Azure Disk Encryption dla systemu Linux. Na przykład, jeśli system docelowy został zainstalowany dysk, na pasku/foo/następnie drugiego na /foo/bar/baz, szyfrowanie /foo/bar/baz powiedzie się, ale szyfrowania paska/foo/zakończy się niepowodzeniem. 

* Usługa Azure Disk Encryption jest obsługiwana tylko na obrazy galerii systemu Azure, obsługiwana, które spełniają wymagania wstępne wymienione wyżej. Niestandardowe obrazy klienta nie są obsługiwane z powodu zachowania procesu, które mogą występować na tych obrazów i schematy niestandardowe partycji. Co więcej nawet obrazu z galerii na podstawie maszyny Wirtualnej, która początkowo zostały spełnione wymagania wstępne zostały zmodyfikowane po utworzeniu mogą być niezgodne.  W tym powodem, sugerowane procedury do szyfrowania maszyny Wirtualnej z systemem Linux jest rozpocząć od czystego galerii, szyfrowanie maszyny Wirtualnej i następnie dodać niestandardowe oprogramowanie i wszystkie dane do maszyny Wirtualnej, zgodnie z potrzebami.  

* Usługa Azure Disk Encryption i wolumin lokalnych danych — klucz szyfrowania bloków woluminu dla Windows i wolumin/mnt/azure_bek_disk maszyn wirtualnych IaaS systemu Linux w celu bezpiecznego przechowywania klucza szyfrowania. Nie należy usuwać ani edytować zawartość tego dysku. Odinstalowuje dysku, ponieważ obecności klucza szyfrowania jest wymagane dla wszystkich operacji szyfrowania na maszynie Wirtualnej IaaS. Plik README, objęte wolumin zawiera dodatkowe szczegóły.

#### <a name="set-up-the-azure-ad-application-in-azure-active-directory"></a>Konfigurowanie aplikacji usługi Azure AD w usłudze Azure Active Directory
Jeśli potrzebujesz szyfrowania była włączona na uruchomionej maszyny Wirtualnej na platformie Azure, usługi Azure Disk Encryption generuje i zapisuje klucze szyfrowania w magazynie kluczy. Zarządzanie kluczami szyfrowania w magazynie kluczy wymaga uwierzytelniania usługi Azure AD.

W tym celu należy utworzyć aplikację usługi Azure AD. Szczegółowy opis kroków można znaleźć rejestrowania aplikacji w sekcji "Uzyskiwanie tożsamości dla aplikacji" wpis w blogu [usługi Azure Key Vault — krok po kroku](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx). Ten wpis zawiera również wiele przykładów przydatne do instalowania i konfigurowania magazynu kluczy. Na potrzeby uwierzytelniania można użyć uwierzytelniania opartego na klucz tajny klienta lub uwierzytelnianie klienta oparte na certyfikatach usługi Azure AD.

#### <a name="client-secret-based-authentication-for-azure-ad"></a>Uwierzytelnianie oparte na klucz tajny klienta dla usługi Azure AD
W kolejnych sekcjach mogą być pomocne w konfigurowaniu uwierzytelnianie oparte na klucz tajny klienta dla usługi Azure AD.

##### <a name="create-an-azure-ad-application-by-using-azure-powershell"></a>Tworzenie aplikacji usługi Azure AD przy użyciu programu Azure PowerShell
Aby utworzyć aplikację usługi Azure AD, użyj następującego polecenia cmdlet programu PowerShell:

    $aadClientSecret = "yourSecret"
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -Password $aadClientSecret
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

> [!NOTE]
> $azureAdApplication.ApplicationId jest ClientID usługi AD platformy Azure i $aadClientSecret jest klucz tajny klienta, którego należy później włączyć usługi Azure Disk Encryption. Chroń klucz tajny klienta usługi Azure AD, odpowiednio.

##### <a name="setting-up-the-azure-ad-client-id-and-secret-from-the-azure-portal"></a>Konfigurowanie usługi Azure AD, Identyfikatora klienta i klucz tajny w witrynie Azure portal
Można również ustawić zapasowej klucz tajny i identyfikator klienta usługi Azure AD przy użyciu witryny Azure Portal. Aby wykonać to zadanie, wykonaj następujące czynności:

1. Wybierz **wszystkie usługi > Usługa Azure Active Directory**

 ![Usługa Azure Disk Encryption](./media/azure-security-disk-encryption/aad-service.png)

2. Wybierz **rejestracji aplikacji > Rejestrowanie nowej aplikacji**

 ![Usługa Azure Disk Encryption](./media/azure-security-disk-encryption/aad-app-registration.png)

3. Podaj wymagane informacje, a następnie utwórz aplikację:

 ![Usługa Azure Disk Encryption](./media/azure-security-disk-encryption/aad-create-app.png)

4. Wybierz nowo utworzoną aplikację, aby wyświetlić jego właściwości, w tym identyfikator aplikacji.  Aby utworzyć klucz dla aplikacji, wybierz **Ustawienia > klucze**, Dodaj opis i wygaśnięcia klucza i kliknij przycisk **Zapisz**

 ![Usługa Azure Disk Encryption](./media/azure-security-disk-encryption/aad-create-pw.png)

5. Skopiuj wygenerowany wartość wpisu tajnego i chronić go odpowiednio.

 ![Usługa Azure Disk Encryption](./media/azure-security-disk-encryption/aad-save-pw.png)


##### <a name="use-an-existing-application"></a>Użyj istniejącej aplikacji
Aby wykonać następujące polecenia, uzyskania i używania [modułu Azure AD PowerShell](https://technet.microsoft.com/library/jj151815.aspx).

> [!NOTE]
> Następujące polecenia, musi być uruchomione z nowe okno programu PowerShell. Nie należy używać programu Azure PowerShell lub w oknie usługi Azure Resource Manager do wykonywania poleceń. Zalecamy takie podejście, ponieważ tych poleceń cmdlet znajdują się w moduł MSOnline lub programu PowerShell usługi Azure AD.

    $clientSecret = ‘<yourAadClientSecret>’
    $aadClientID = '<Client ID of your Azure AD application>'
    connect-msolservice
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type password -Value $clientSecret

#### <a name="certificate-based-authentication-for-azure-ad"></a>Uwierzytelnianie oparte na certyfikatach dla usługi Azure AD
> [!NOTE]
> Uwierzytelnianie usługi Azure AD na podstawie certyfikatu nie jest obecnie obsługiwane na maszynach wirtualnych z systemem Linux.

W kolejnych sekcjach pokazano, jak skonfigurować uwierzytelnianie oparte na certyfikatach w usłudze Azure AD.

##### <a name="create-an-azure-ad-application"></a>Utwórz aplikację usługi Azure AD
Aby utworzyć aplikację usługi Azure AD, wykonaj następujące polecenia cmdlet programu PowerShell:

> [!NOTE]
> Zastąp następujące `yourpassword` ciąg znaków z bezpieczne hasło i zabezpieczenia hasło.

    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate("C:\certificates\examplecert.pfx", "yourpassword")
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -KeyValue $keyValue -KeyType AsymmetricX509Cert
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

Po zakończeniu tego kroku, Przekaż plik PFX do magazynu kluczy, a następnie włącz zasady dostępu wymagane do wdrożenia tego certyfikatu do maszyny Wirtualnej.

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

Po zakończeniu tego kroku, Przekaż plik PFX do magazynu kluczy, a następnie włącz zasady dostępu, które jest potrzebne do wdrażania certyfikatu do maszyny Wirtualnej.

##### <a name="upload-a-pfx-file-to-your-key-vault"></a>Przekaż plik PFX do magazynu kluczy
Aby uzyskać szczegółowe wyjaśnienie tego procesu, zobacz [oficjalny Blog platformy Azure klucz magazynu zespołu](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx). Następujące polecenia cmdlet programu PowerShell są jednak wszystko, czego potrzebujesz do zadania. Pamiętaj wykonać je z poziomu konsoli programu Azure PowerShell.

> [!NOTE]
> Zastąp następujące `yourpassword` ciąg znaków z bezpieczne hasło i zabezpieczenia hasło.

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

##### <a name="deploy-a-certificate-in-your-key-vault-to-an-existing-vm"></a>Wdróż certyfikat w magazynie kluczy na istniejącej maszyny Wirtualnej
Po zakończeniu przekazywania plik PFX, należy wdrożyć certyfikat w magazynie kluczy na istniejącej maszyny Wirtualnej przy użyciu następujących:
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

#### <a name="set-up-the-key-vault-access-policy-for-the-azure-ad-application"></a>Konfigurowanie zasad dostępu magazynu kluczy dla aplikacji Azure AD
Aplikacja usługi Azure AD wymaga praw dostępu do kluczy lub wpisów tajnych w magazynie. Użyj [ `Set-AzureKeyVaultAccessPolicy` ](/powershell/module/azure/set-azurekeyvaultaccesspolicy?view=azuresmps-3.7.0) polecenia cmdlet, aby udzielić uprawnień do aplikacji przy użyciu Identyfikatora klienta, (który został wygenerowany, gdy aplikacja została zarejestrowana) jako _— ServicePrincipalName_ wartość parametru. Aby dowiedzieć się więcej, zobacz wpis w blogu [usługi Azure Key Vault — krok po kroku](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx). Oto przykład sposobu wykonania tego zadania za pomocą programu PowerShell:

    $keyVaultName = '<yourKeyVaultName>'
    $aadClientID = '<yourAadAppClientID>'
    $rgname = '<yourResourceGroup>'
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $rgname

> [!NOTE]
> Usługa Azure Disk Encryption, musisz skonfigurować następujące zasady dostępu do aplikacji klienta usługi Azure AD: _WrapKey_ i _ustaw_ uprawnienia.

## <a name="terminology"></a>Terminologia
Aby poznać niektóre typowe terminy używane przez tę technologię, skorzystaj z poniższej tabeli terminologii:

| Terminologia | Definicja |
| --- | --- |
| Azure AD | Usługa Azure AD jest [usługi Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/). Konto usługi Azure AD jest wymaganiem wstępnym dla uwierzytelniania, przechowywania i pobierania wpisów tajnych w magazynie kluczy. |
| W usłudze Azure Key Vault | Key Vault to usługa kryptograficznych, key management, która opiera się na zweryfikowane Federal Information Processing Standards FIPS sprzętowych modułów zabezpieczeń, które pomagają chronić klucze kryptograficzne i wpisy tajne poufnych. Aby uzyskać więcej informacji, zobacz [usługi Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentacji. |
| ARM | Azure Resource Manager |
| BitLocker |[Funkcja BitLocker](https://technet.microsoft.com/library/hh831713.aspx) to branży Windows woluminu szyfrowania technologia, która służy do włączania szyfrowania dysków na maszynach wirtualnych IaaS Windows. |
| KSB | Klucze szyfrowania funkcją BitLocker są używane do szyfrowania woluminu rozruchowego systemu operacyjnego i woluminów danych. Klucze funkcji BitLocker są chronione w magazynie kluczy jako wpisy tajne. |
| Interfejs wiersza polecenia | Zobacz [interfejsu wiersza polecenia platformy Azure](../cli-install-nodejs.md). |
| DM-Crypt |[DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) jest podsystem opartych na systemie Linux lub przezroczystego szyfrowania dysku, który służy do włączania szyfrowania dysków na maszynach wirtualnych IaaS z systemem Linux. |
| KEK | Klucz szyfrowania klucza to klucz asymetryczny (RSA 2048), który służy do ochrony lub zawijania wpisu tajnego. Możesz podać zabezpieczeń sprzętowych modułów (HSM)-chronione klucza lub klucza chronionego przez oprogramowanie. Aby uzyskać więcej informacji, zobacz [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentacji. |
| PS poleceń cmdlet | Zobacz [poleceń cmdlet programu Azure PowerShell](/powershell/azure/overview). |

### <a name="set-up-and-configure-your-key-vault-for-azure-disk-encryption"></a>Instalowanie i konfigurowanie własnego magazynu kluczy dla usługi Azure Disk Encryption
Usługa Azure Disk Encryption ułatwia zabezpieczenie szyfrowania dysków kluczy i wpisów tajnych w magazynie kluczy. Aby skonfigurować magazyn kluczy dla usługi Azure Disk Encryption, wykonaj kroki w następujących sekcjach.

#### <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy
Aby utworzyć magazyn kluczy, użyj jednej z następujących opcji:

* ["101-Key-Vault-Create" Resource Manager template](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)
* [Polecenia cmdlet usługi key vault z programu PowerShell platformy Azure](/powershell/module/azurerm.keyvault/#key_vault)
* Azure Resource Manager
* Jak [zabezpieczanie własnego magazynu kluczy](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)

> [!NOTE]
> Jeśli magazyn kluczy ma już skonfigurowane dla Twojej subskrypcji, przejdź do następnej sekcji.

![W usłudze Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig1.png)

#### <a name="set-up-a-key-encryption-key-optional"></a>Konfigurowanie klucza szyfrowania (opcjonalnie)
Jeśli chcesz użyć klucza KEK dla dodatkową warstwę zabezpieczeń dla kluczy szyfrowania funkcją BitLocker, należy dodać KEK do magazynu kluczy. Użyj [ `Add-AzureKeyVaultKey` ](/powershell/module/azurerm.keyvault/add-azurermkeyvaultkey) polecenia cmdlet, aby utworzyć klucz szyfrowania klucza w magazynie kluczy. Można także zaimportować KEK, z usługi zarządzania kluczami w środowisku lokalnym przez moduł HSM. Aby uzyskać więcej informacji, zobacz [Key Vault dokumentacji](https://azure.microsoft.com/documentation/services/key-vault/).

    Add-AzureKeyVaultKey [-VaultName] <string> [-Name] <string> -Destination <string> {HSM | Software}

Można dodać klucza KEK, przechodząc do usługi Azure Resource Manager lub za pomocą interfejsu usługi key vault.

![W usłudze Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig2.png)

#### <a name="set-key-vault-permissions"></a>Ustawianie uprawnień usługi key vault
Platforma Azure wymaga dostępu do kluczy szyfrowania i wpisy tajne w magazynie kluczy, aby udostępnić je do maszyny Wirtualnej do rozruchu i odszyfrowywania woluminów. Aby udzielić uprawnień do korzystania z platformy Azure, należy ustawić **EnabledForDiskEncryption** właściwości klucza magazynu za pomocą polecenia cmdlet programu PowerShell usługi key vault:

    Set-AzureRmKeyVaultAccessPolicy -VaultName <yourVaultName> -ResourceGroupName <yourResourceGroup> -EnabledForDiskEncryption

Można również ustawić **EnabledForDiskEncryption** właściwości, odwiedzając [Eksploratora zasobów Azure](https://resources.azure.com).

Jak wspomniano wcześniej, należy ustawić **EnabledForDiskEncryption** właściwość w magazynie kluczy. W przeciwnym razie wdrożenie zakończy się niepowodzeniem.

Skonfigurowaniem zasad dostępu dla aplikacji usługi Azure AD przy użyciu interfejsu usługi key vault, jak pokazano poniżej:

![W usłudze Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig3.png)

![W usłudze Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig3b.png)

Na **zaawansowane zasady dostępu** i upewnij się, że Twój magazyn kluczy jest włączone dla usługi Azure Disk Encryption:

![Usługa Azure key vault](./media/azure-security-disk-encryption/keyvault-portal-fig4.png)

## <a name="disk-encryption-deployment-scenarios-and-user-experiences"></a>Scenariusze wdrażania szyfrowania dysku i środowisk pracy użytkownika
Można włączyć wiele scenariuszy szyfrowania dysku i kroki zależą od scenariusza. W poniższych częściach omówiono scenariusze, które bardziej szczegółowo.

### <a name="enable-encryption-on-new-iaas-vms-that-are-created-from-the-marketplace"></a>Włącz szyfrowanie dla nowych maszyn wirtualnych IaaS, które są tworzone w witrynie Marketplace
Można włączyć szyfrowania dysków na nowej maszynie Wirtualnej Windows IaaS z rynku na platformie Azure przy użyciu [szablonu usługi Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image).

1. W szablonie szybki start platformy Azure, kliknij polecenie **Wdróż na platformie Azure**, wprowadź konfiguracji szyfrowania na **parametry** bloku, a następnie kliknij **OK**.

2. Wybierz subskrypcję, grupy zasobów, lokalizację grupy zasobów, postanowienia prawne i umowę, a następnie kliknij przycisk **Utwórz** Aby włączyć szyfrowanie na utworzonej nowej maszynie Wirtualnej IaaS.

> [!NOTE]
> Ten szablon umożliwia utworzenie nowego zaszyfrowanych Windows maszynę Wirtualną, która używa obrazu z galerii systemu Windows Server 2012.

Można włączyć szyfrowania dysków na nową IaaS Red Hat Linux 7.2 maszynę Wirtualną za pomocą macierzy RAID-0 200 GB przy użyciu tej [szablonu usługi Resource Manager](https://aka.ms/fde-rhel). Po wdrożeniu szablonu, należy sprawdzić stan szyfrowania maszyny Wirtualnej za pomocą `Get-AzureRmVmDiskEncryptionStatus` polecenia cmdlet, zgodnie z opisem w [szyfrowania dysku systemu operacyjnego na uruchomionej maszynie Wirtualnej systemu Linux](#encrypting-os-drive-on-a-running-linux-vm). Gdy komputer zwraca stan _VMRestartPending_, uruchom ponownie maszynę Wirtualną.

Poniższa tabela zawiera listę parametrów szablonu usługi Resource Manager dla nowych maszyn wirtualnych w scenariuszu witryny Marketplace przy użyciu Identyfikatora klienta usługi Azure AD:

| Parametr | Opis |
| --- | --- |
| adminUserName | Nazwa użytkownika administratora dla maszyny wirtualnej. |
| adminPassword | Hasło użytkownika administratora dla maszyny wirtualnej. |
| newStorageAccountName | Nazwa konta magazynu do przechowywania systemu operacyjnego i danych wirtualnych dysków twardych. |
| vmSize | Rozmiar maszyny Wirtualnej. Obecnie są obsługiwane tylko standardowe A, D i G serii. |
| virtualNetworkName | Nazwa sieci wirtualnej, która karty Sieciowej maszyny Wirtualnej powinny należeć do. |
| subnetName | Nazwa podsieci w sieci wirtualnej, która karty Sieciowej maszyny Wirtualnej powinny należeć do. |
| AADClientID | Identyfikator klienta aplikacji usługi Azure AD, które ma uprawnienia do zapisu kluczy tajnych w magazynie kluczy. |
| AADClientSecret | Klucz tajny klienta aplikacji usługi Azure AD, która ma uprawnienia do zapisu kluczy tajnych w magazynie kluczy. |
| keyVaultURL | Adres URL klucza funkcji BitLocker, należy przekazać do magazyn kluczy. Możesz pobrać go za pomocą polecenia cmdlet `(Get-AzureRmKeyVault -VaultName,-ResourceGroupName ).VaultURI`. |
| keyEncryptionKeyURL | Adres URL klucza szyfrowania klucza, który jest używany do szyfrowania wygenerowanego klucza funkcji BitLocker (opcjonalnie). |
| keyVaultResourceGroup | Grupa zasobów magazynu kluczy. |
| vmName | Nazwa maszyny Wirtualnej, można wykonać w operacji szyfrowania. |

> [!NOTE]
> _KeyEncryptionKeyURL_ jest parametrem opcjonalnym. Przenoszenie własnych KEK do dalszych zabezpieczenie klucza szyfrowania danych (klucz tajny hasło) w magazynie kluczy.

### <a name="enable-encryption-on-new-iaas-vms-that-are-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Włącz szyfrowanie dla nowych maszyn wirtualnych IaaS, które są tworzone na podstawie szyfrowane klienta wirtualnego dysku twardego i klucze szyfrowania
W tym scenariuszu można włączyć szyfrowanie przy użyciu szablonu usługi Resource Manager, poleceń cmdlet programu PowerShell lub interfejsu wiersza polecenia. W poniższych sekcjach opisano szczegółowo szablonu usługi Resource Manager i interfejsu wiersza polecenia.

Postępuj zgodnie z instrukcjami z jednego z tych sekcji do przygotowania obrazów zaszyfrowane wstępnie, których można użyć na platformie Azure. Po utworzeniu obrazu, do tworzenia zaszyfrowanych maszyn wirtualnych platformy Azure za pomocą kroki opisane w następnej sekcji.

* [Przygotowywanie wirtualnego dysku twardego Windows zaszyfrowane wstępnie](#preparing-a-pre-encrypted-windows-vhd)
* [Przygotowywanie wirtualnego dysku twardego systemu Linux zaszyfrowane wstępnie](#preparing-a-pre-encrypted-linux-vhd)

#### <a name="using-the-resource-manager-template"></a>Przy użyciu szablonu usługi Resource Manager
Można włączyć szyfrowanie dysków na zaszyfrowane wirtualnego dysku twardego za pomocą [szablonu usługi Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-pre-encrypted-vm).

1. W szablonie szybki start platformy Azure, kliknij polecenie **Wdróż na platformie Azure**, wprowadź konfiguracji szyfrowania na **parametry** bloku, a następnie kliknij **OK**.

2. Wybierz subskrypcję, grupy zasobów, lokalizację grupy zasobów, postanowienia prawne i umowę, a następnie kliknij przycisk **Utwórz** Aby włączyć szyfrowanie na nowej maszynie Wirtualnej IaaS.

Poniższa tabela zawiera listę parametrów szablonu usługi Resource Manager dla zaszyfrowanych wirtualnego dysku twardego:

| Parametr | Opis |
| --- | --- |
| newStorageAccountName | Nazwa konta magazynu do przechowywania wirtualnego dysku twardego systemu operacyjnego zaszyfrowane. To konto magazynu powinny już został utworzony w tej samej grupie zasobów i tej samej lokalizacji co maszyna wirtualna. |
| osVhdUri | Identyfikator URI wirtualnego dysku twardego systemu operacyjnego z konta magazynu. |
| osType | Typ produktu systemu operacyjnego (Windows/Linux). |
| virtualNetworkName | Nazwa sieci wirtualnej, która karty Sieciowej maszyny Wirtualnej powinny należeć do. Nazwa powinna już została utworzona w tej samej grupie zasobów i tej samej lokalizacji co maszyna wirtualna. |
| subnetName | Nazwa podsieci w sieci wirtualnej, której karty Sieciowej maszyny Wirtualnej powinna należeć. |
| vmSize | Rozmiar maszyny Wirtualnej. Obecnie są obsługiwane tylko standardowe A, D i G serii. |
| keyVaultResourceID | Identyfikator zasobu, która identyfikuje zasób magazynu kluczy w usłudze Azure Resource Manager. Możesz pobrać go przy użyciu polecenia cmdlet programu PowerShell `(Get-AzureRmKeyVault -VaultName &lt;yourKeyVaultName&gt; -ResourceGroupName &lt;yourResourceGroupName&gt;).ResourceId`. |
| keyVaultSecretUrl | Adres URL klucza szyfrowania dysku, który jest skonfigurowany w usłudze key vault. |
| keyVaultKekUrl | Adres URL klucza szyfrowania do szyfrowania klucza wygenerowanego szyfrowania dysków. |
| vmName | Nazwa maszyny Wirtualnej IaaS. |

#### <a name="using-powershell-cmdlets"></a>Za pomocą poleceń cmdlet programu PowerShell
Można włączyć szyfrowanie dysków na zaszyfrowane wirtualnego dysku twardego za pomocą polecenia cmdlet programu PowerShell [ `Set-AzureRmVMOSDisk` ](/powershell/module/azurerm.compute/set-azurermvmosdisk).  

#### <a name="using-cli-commands"></a>Przy użyciu interfejsu wiersza polecenia
Aby włączyć szyfrowanie dysków dla tego scenariusza za pomocą interfejsu wiersza polecenia, wykonaj następujące czynności:

1. Ustawianie zasad dostępu w magazynie kluczy:

   * Ustaw **EnabledForDiskEncryption** flagi:

    `azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
   * Ustaw uprawnienia do aplikacji usługi Azure AD do zapisu kluczy tajnych w magazynie kluczy:

    `azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys '["wrapKey"]' --perms-to-secrets '["set"]'`

2. Aby włączyć szyfrowanie na istniejące lub uruchomionej maszyny Wirtualnej, wpisz:

 `azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId> --volume-type [All|OS|Data]`

3. Pobierz stan szyfrowania:

 `azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json`

4. Aby włączyć szyfrowanie na nową maszynę Wirtualną z zaszyfrowanego wirtualnego dysku twardego, należy użyć poniższych parametrów za pomocą `azure vm create` polecenia:

 ```
   * disk-encryption-key-vault-id <disk-encryption-key-vault-id>
   * disk-encryption-key-url <disk-encryption-key-url>
   * key-encryption-key-vault-id <key-encryption-key-vault-id>
   * key-encryption-key-url <key-encryption-key-url>
 ```

### <a name="enable-encryption-on-existing-or-running-iaas-windows-vm-in-azure"></a>Włącz szyfrowanie dla istniejących lub działających maszyn wirtualnych Windows IaaS na platformie Azure
W tym scenariuszu można włączyć szyfrowanie przy użyciu szablonu usługi Resource Manager, poleceń cmdlet programu PowerShell lub interfejsu wiersza polecenia. Poniższe sekcje opisano szczegółowo ją włączyć za pomocą szablonu usługi Resource Manager i interfejsu wiersza polecenia.

#### <a name="using-the-resource-manager-template"></a>Przy użyciu szablonu usługi Resource Manager
Można włączyć szyfrowanie dysków dla istniejących lub uruchomionych maszyn wirtualnych Windows IaaS na platformie Azure przy użyciu [szablonu usługi Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm).

1. W szablonie szybki start platformy Azure, kliknij polecenie **Wdróż na platformie Azure**, wprowadź konfiguracji szyfrowania na **parametry** bloku, a następnie kliknij **OK**.

2. Wybierz subskrypcję, grupy zasobów, lokalizację grupy zasobów, postanowienia prawne i umowę, a następnie kliknij przycisk **Utwórz** Aby włączyć szyfrowanie na istniejące lub uruchomionej maszyny Wirtualnej IaaS.

Poniższa tabela zawiera listę parametrów szablonu usługi Resource Manager dla istniejących lub uruchomione maszyny wirtualne korzystające z usługi Azure AD identyfikator klienta:

| Parametr | Opis |
| --- | --- |
| AADClientID | Identyfikator klienta aplikacji usługi Azure AD, które ma uprawnienia do zapisu kluczy tajnych w magazynie kluczy. |
| AADClientSecret | Klucz tajny klienta aplikacji usługi Azure AD, która ma uprawnienia do zapisu kluczy tajnych w magazynie kluczy. |
| keyVaultName | Nazwa klucza funkcji BitLocker, należy przekazać do magazyn kluczy. Możesz pobrać go za pomocą polecenia cmdlet `(Get-AzureRmKeyVault -ResourceGroupName <yourResourceGroupName>). Vaultname`. |
|  keyEncryptionKeyURL | Adres URL klucza szyfrowania klucza, który jest używany do szyfrowania wygenerowanego klucza funkcji BitLocker. Ten parametr jest opcjonalny w przypadku wybrania **nokek** na liście rozwijanej UseExistingKek. Jeśli wybierzesz **kek** na liście rozwijanej UseExistingKek należy wprowadzić _keyEncryptionKeyURL_ wartości. |
| volumeType | Typ operacji szyfrowania odbywa się na wolumin. Prawidłowe wartości to _OS_, _danych_, i _wszystkich_. |
| sequenceVersion | Wersja sekwencji operacji funkcji BitLocker. Za każdym razem, gdy operacja szyfrowania dysku jest wykonywana na tej samej maszyny Wirtualnej, należy zwiększyć numer wersji. |
| vmName | Nazwa maszyny Wirtualnej, można wykonać w operacji szyfrowania. |

> [!NOTE]
> _KeyEncryptionKeyURL_ jest parametrem opcjonalnym. Własne KEK może doprowadzić do dodatkowe zabezpieczenie klucza szyfrowania danych (wpis tajny szyfrowania funkcją BitLocker) w magazynie kluczy.

#### <a name="using-powershell-cmdlets"></a>Za pomocą poleceń cmdlet programu PowerShell
Aby uzyskać informacje na temat włączania szyfrowania przy użyciu usługi Azure Disk Encryption przy użyciu poleceń cmdlet programu PowerShell, zobacz wpisy w blogu [Eksplorowanie usługi Azure Disk Encryption przy użyciu programu Azure PowerShell — część 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) i [Eksplorowanie usługi Azure Disk Encryption za pomocą programu Azure PowerShell — część 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx).

#### <a name="using-cli-commands"></a>Przy użyciu interfejsu wiersza polecenia
Aby włączyć szyfrowanie na istniejące lub działających maszyn wirtualnych Windows IaaS na platformie Azure, przy użyciu interfejsu wiersza polecenia, wykonaj następujące czynności:

1. Aby ustawić zasady dostępu w usłudze key vault:
   * Ustaw **EnabledForDiskEncryption** flagi:

    `azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
   * Ustaw uprawnienia do aplikacji usługi Azure AD do zapisu kluczy tajnych w magazynie kluczy:

    `azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys '["wrapKey"]' --perms-to-secrets '["set"]'`
2. Aby włączyć szyfrowanie na istniejące lub uruchomionej maszyny Wirtualnej:

 `azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId> --volume-type [All|OS|Data]`
3. Aby wyświetlić stan szyfrowania:

 `azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json`
4. Aby włączyć szyfrowanie na nową maszynę Wirtualną z zaszyfrowanego wirtualnego dysku twardego, należy użyć poniższych parametrów za pomocą `azure vm create` polecenia:

 ```
   * disk-encryption-key-vault-id <disk-encryption-key-vault-id>
   * disk-encryption-key-url <disk-encryption-key-url>
   * key-encryption-key-vault-id <key-encryption-key-vault-id>
   * key-encryption-key-url <key-encryption-key-url>
 ```

### <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm-in-azure"></a>Włącz szyfrowanie dla istniejących lub uruchamianie systemu Linux maszyny Wirtualnej IaaS na platformie Azure
Można włączyć szyfrowania dysków na istniejące lub uruchamianie systemu Linux maszyny Wirtualnej IaaS na platformie Azure przy użyciu [szablonu usługi Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

1. Kliknij przycisk **Wdróż na platformie Azure** na szablonu szybkiego startu platformy Azure, wprowadź konfiguracji szyfrowania na **parametry** bloku, a następnie kliknij **OK**.

2. Wybierz subskrypcję, grupy zasobów, lokalizację grupy zasobów, postanowienia prawne i umowę, a następnie kliknij przycisk **Utwórz** Aby włączyć szyfrowanie na istniejące lub uruchomionej maszyny Wirtualnej IaaS.

Poniższa tabela zawiera listę parametrów szablonu usługi Resource Manager dla istniejących lub uruchomione maszyny wirtualne korzystające z usługi Azure AD identyfikator klienta:

| Parametr | Opis |
| --- | --- |
| AADClientID | Identyfikator klienta aplikacji usługi Azure AD, które ma uprawnienia do zapisu kluczy tajnych w magazynie kluczy. |
| AADClientSecret | Klucz tajny klienta aplikacji usługi Azure AD, która ma uprawnienia do zapisu kluczy tajnych w magazynie kluczy. |
| keyVaultName | Nazwa klucza funkcji BitLocker, należy przekazać do magazyn kluczy. Możesz pobrać go za pomocą polecenia cmdlet `(Get-AzureRmKeyVault -ResourceGroupName <yourResourceGroupName>). Vaultname`. |
|  keyEncryptionKeyURL | Adres URL klucza szyfrowania klucza, który jest używany do szyfrowania wygenerowanego klucza funkcji BitLocker. Ten parametr jest opcjonalny w przypadku wybrania **nokek** na liście rozwijanej UseExistingKek. Jeśli wybierzesz **kek** na liście rozwijanej UseExistingKek należy wprowadzić _keyEncryptionKeyURL_ wartości. |
| volumeType | Typ operacji szyfrowania odbywa się na wolumin. Prawidłowe wartości obsługiwanych _OS_ lub _wszystkich_ (zobacz obsługiwane dystrybucje systemu Linux i ich wersje dla systemu operacyjnego i dysków z danymi w sekcji prerequisiteis wcześniej). |
| sequenceVersion | Wersja sekwencji operacji funkcji BitLocker. Za każdym razem, gdy operacja szyfrowania dysku jest wykonywana na tej samej maszyny Wirtualnej, należy zwiększyć numer wersji. |
| vmName | Nazwa maszyny Wirtualnej, można wykonać w operacji szyfrowania. |
| Hasło | Wpisz silne hasło klucza szyfrowania danych. |

> [!NOTE]
> _KeyEncryptionKeyURL_ jest parametrem opcjonalnym. Przenoszenie własnych KEK do dalszych zabezpieczenie klucza szyfrowania danych (klucz tajny hasło) w magazynie kluczy.

#### <a name="cli-commands"></a>Polecenia interfejsu wiersza polecenia
Można włączyć szyfrowanie dysków na zaszyfrowane wirtualnego dysku twardego przy instalowaniu i używaniu [interfejsu wiersza polecenia](../cli-install-nodejs.md). Aby włączyć szyfrowanie na istniejące lub uruchomionych maszyn wirtualnych systemu Linux IaaS na platformie Azure przy użyciu interfejsu wiersza polecenia, wykonaj następujące czynności:

1. Ustawianie zasad dostępu w usłudze key vault:

 * Ustaw **EnabledForDiskEncryption** flagi:

    `azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
 * Ustaw uprawnienia do aplikacji usługi Azure AD do zapisu kluczy tajnych w magazynie kluczy:

    `azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys '["wrapKey"]' --perms-to-secrets '["set"]'`

2. Aby włączyć szyfrowanie na istniejące lub uruchomionej maszyny Wirtualnej:

 `azure vm enable-disk-encryption --resource-group <resourceGroupName> --name <vmName> --aad-client-id <aadClientId> --aad-client-secret <aadClientSecret> --disk-encryption-key-vault-url <keyVaultURL> --disk-encryption-key-vault-id <keyVaultResourceId> --volume-type [All|OS|Data]`

3. Pobierz stan szyfrowania:

 `azure vm show-disk-encryption-status --resource-group <resourceGroupName> --name <vmName> --json`

4. Aby włączyć szyfrowanie na nową maszynę Wirtualną z zaszyfrowanego wirtualnego dysku twardego, należy użyć poniższych parametrów za pomocą `azure vm create` polecenia:
 ```
   * disk-encryption-key-vault-id <disk-encryption-key-vault-id>
   * disk-encryption-key-url <disk-encryption-key-url>
   * key-encryption-key-vault-id <key-encryption-key-vault-id>
   * key-encryption-key-url <key-encryption-key-url>
 ```

### <a name="get-the-encryption-status-of-an-encrypted-iaas-vm"></a>Pobierz stan szyfrowania zaszyfrowanych maszyn wirtualnych IaaS
Przy użyciu usługi Azure Resource Manager, można uzyskać stanu szyfrowania [poleceń cmdlet programu PowerShell](/powershell/azure/overview), lub interfejsu wiersza polecenia. W następujących sekcjach opisano sposób użycia witryny Azure Portal oraz interfejsu wiersza polecenia można pobrać stanu szyfrowania.

#### <a name="get-the-encryption-status-of-an-encrypted-windows-vm-by-using-azure-resource-manager"></a>Pobierz stan szyfrowania zaszyfrowanych maszyn wirtualnych Windows za pomocą usługi Azure Resource Manager
Można uzyskać stanu szyfrowania maszyn wirtualnych IaaS z usługi Azure Resource Manager, wykonując następujące czynności:

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com/), a następnie kliknij przycisk **maszyn wirtualnych** w okienku po lewej stronie, aby wyświetlić widok podsumowania maszyn wirtualnych w ramach subskrypcji. Można filtrować widok maszyn wirtualnych po wybraniu nazwy subskrypcji w **subskrypcji** listy rozwijanej.

2. W górnej części **maszyn wirtualnych** kliknij **kolumn**.

3. Na **kolumny wybierz** bloku wybierz **szyfrowanie dysków**, a następnie kliknij przycisk **aktualizacji**. Powinien zostać wyświetlony kolumny szyfrowania dysków, przedstawiający stan szyfrowania _włączone_ lub _nie włączono_ dla każdej maszyny Wirtualnej, jak pokazano na poniższej ilustracji:

 ![Ochrona przed złośliwym kodem zapewniana przez Microsoft na platformie Azure](./media/azure-security-disk-encryption/disk-encryption-fig2.png)

#### <a name="get-the-encryption-status-of-an-encrypted-windowslinux-iaas-vm-by-using-the-disk-encryption-powershell-cmdlet"></a>Pobierz stan szyfrowania zaszyfrowanych maszyn wirtualnych IaaS (Windows/Linux) za pomocą polecenia cmdlet programu PowerShell szyfrowanie dysków
Można pobrać stanu szyfrowania maszyn wirtualnych IaaS z polecenia cmdlet programu PowerShell szyfrowanie dysków `Get-AzureRmVMDiskEncryptionStatus`. Aby uzyskać ustawienia szyfrowania dla maszyny Wirtualnej, wprowadź następujące informacje:

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

Wartości ustawienia OSVolumeEncrypted i DataVolumesEncrypted są ustawiane na _zaszyfrowane_, który wskazuje, że zarówno woluminy są zaszyfrowane przy użyciu usługi Azure Disk Encryption. Aby uzyskać informacje na temat włączania szyfrowania przy użyciu usługi Azure Disk Encryption przy użyciu poleceń cmdlet programu PowerShell, zobacz wpisy w blogu [Eksplorowanie usługi Azure Disk Encryption przy użyciu programu Azure PowerShell — część 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) i [Eksplorowanie usługi Azure Disk Encryption za pomocą programu Azure PowerShell — część 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx).

> [!NOTE]
> Na maszynach wirtualnych systemu Linux, zajmuje trzy do czterech minut `Get-AzureRmVMDiskEncryptionStatus` polecenia cmdlet w celu raportowania stanu szyfrowania.

#### <a name="get-the-encryption-status-of-the-iaas-vm-from-the-disk-encryption-cli-command"></a>Uzyskiwanie stanu szyfrowania maszyn wirtualnych IaaS szyfrowanie dysków polecenia interfejsu wiersza polecenia
Stan szyfrowania maszyn wirtualnych IaaS można uzyskać za pomocą polecenia interfejsu wiersza polecenia szyfrowanie dysków `azure vm show-disk-encryption-status`. Aby uzyskać ustawienia szyfrowania dla maszyny Wirtualnej, wprowadź sesję wiersza polecenia platformy Azure:

    azure vm show-disk-encryption-status --resource-group <yourResourceGroupName> --name <yourVMName> --json  

#### <a name="disable-encryption-on-running-windows-iaas-vm"></a>Wyłącz szyfrowanie dla uruchomionej maszyny Wirtualnej IaaS Windows
Można wyłączyć szyfrowania na bieżące Windows lub maszyny Wirtualnej systemu Linux IaaS przy użyciu szablonu Menedżera zasobów szyfrowania dysków Azure lub poleceń cmdlet programu PowerShell i określić konfigurację odszyfrowywania.

##### <a name="windows-vm"></a>Maszyna wirtualna z systemem Windows
Krok wyłączenie szyfrowania wyłącza funkcję szyfrowania systemu operacyjnego, ilości danych lub obu tych programów na uruchomionej maszynie Wirtualnej IaaS Windows. Nie można wyłączyć wolumin systemu operacyjnego i pozostaw ilość danych zaszyfrowanych. Podczas wykonywania kroku wyłączenie szyfrowania klasycznego modelu wdrażania platformy aktualizuje model usług maszyn wirtualnych i maszyn wirtualnych IaaS Windows jest oznaczony odszyfrowany. Zawartość maszyny Wirtualnej nie są szyfrowane, gdy. Odszyfrowywanie nie powoduje usunięcia magazynu kluczy i materiału klucza szyfrowania (klucze szyfrowania funkcją BitLocker for Windows i hasło dla systemu Linux).

##### <a name="linux-vm"></a>Maszyna wirtualna z systemem Linux
Krok wyłączenie szyfrowania wyłącza funkcję szyfrowania woluminu danych na uruchomionej maszynie Wirtualnej IaaS systemu Linux. Ten krok tylko wtedy, gdy nie jest zaszyfrowany dysk systemu operacyjnego.

> [!NOTE]
> Wyłączenie szyfrowania na dysku systemu operacyjnego nie jest dozwolona na maszynach wirtualnych z systemem Linux.

##### <a name="disable-encryption-on-an-existing-or-running-iaas-vm"></a>Wyłącz szyfrowanie na Maszynie wirtualnej IaaS istniejących lub nie działają
Można wyłączyć szyfrowania dysków na uruchamianie maszyn wirtualnych IaaS Windows za pomocą [szablonu usługi Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm).

1. Na szablonu szybkiego startu platformy Azure, kliknij przycisk **Wdróż na platformie Azure**, wprowadź konfiguracji odszyfrowywania na **parametry** bloku, a następnie kliknij **OK**.

2. Wybierz subskrypcję, grupy zasobów, lokalizację grupy zasobów, postanowienia prawne i umowę, a następnie kliknij przycisk **Utwórz** Aby włączyć szyfrowanie na utworzonej nowej maszynie Wirtualnej IaaS.

W przypadku maszyn wirtualnych systemu Linux można wyłączyć szyfrowania za pomocą [Wyłącz szyfrowanie dla uruchomionej maszyny Wirtualnej systemu Linux](https://aka.ms/decrypt-linuxvm) szablonu.

Poniższa tabela zawiera listę parametrów szablonu usługi Resource Manager dla wyłączenie szyfrowania na uruchomionej maszynie Wirtualnej IaaS:

| Parametr | Opis |
| --- | --- |
| vmName | Nazwa maszyny Wirtualnej, można wykonać w operacji szyfrowania.
| volumeType | Typ operacji odszyfrowywania odbywa się na wolumin. Prawidłowe wartości to _OS_, _danych_, i _wszystkich_. Nie można wyłączyć szyfrowania na uruchomienie wolumin systemu operacyjnego Windows IaaS VM/rozruchowy bez wyłączenie szyfrowania na _danych_ woluminu. Należy również zauważyć, że wyłączenie szyfrowania na dysku systemu operacyjnego nie jest dozwolona na maszynach wirtualnych z systemem Linux. |
| sequenceVersion | Wersja sekwencji operacji funkcji BitLocker. Za każdym razem, gdy operacja odszyfrowywania dysku odbywa się na tej samej maszyny Wirtualnej, należy zwiększyć numer wersji. |

##### <a name="disable-encryption-on-an-existing-or-running-iaas-vm"></a>Wyłącz szyfrowanie na Maszynie wirtualnej IaaS istniejących lub nie działają
Aby wyłączyć szyfrowanie na Maszynie wirtualnej IaaS istniejących lub jest uruchomiony przy użyciu polecenia cmdlet programu PowerShell, zobacz [ `Disable-AzureRmVMDiskEncryption` ](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption). To polecenie cmdlet obsługuje zarówno Windows, jak i maszyny wirtualne systemu Linux. Aby wyłączyć szyfrowanie, instaluje rozszerzenie na maszynie wirtualnej. Jeśli _nazwa_ parametr nie jest określony, rozszerzenie z domyślną nazwą _AzureDiskEncryption for Windows VMs_ zostanie utworzony.

Na maszynach wirtualnych systemu Linux rozszerzenia AzureDiskEncryptionForLinux jest używany.

> [!NOTE]
> To polecenie cmdlet ponowny rozruch maszyny wirtualnej.

### <a name="enable-encryption-on-pre-encrypted-iaas-vm-with-azure-managed-disk"></a>Włącz szyfrowanie dla wstępnie zaszyfrowanych maszyn wirtualnych IaaS z dysków zarządzanych platformy Azure
Szablon ARM dysków zarządzanych platformy Azure umożliwia tworzenie zaszyfrowanej maszyny Wirtualnej na podstawie wirtualnego dysku twardego zaszyfrowane wstępnie przy użyciu szablonu ARM, znajdujący się w   
[Tworzenie nowego dysku zarządzanego zaszyfrowanych z obiektu blob wirtualnego dysku twardego i magazynowania zaszyfrowane wstępnie] (https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)

### <a name="enable-encryption-on-a-new-linux-iaas-vm-with-azure-managed-disk"></a>Włącz szyfrowanie dla nowej maszyny Wirtualnej IaaS systemu Linux, za pomocą usługi Azure Managed Disk
Szablon ARM dysków zarządzanych platformy Azure umożliwia tworzenie nowych zaszyfrowanych IaaS Maszynę wirtualną systemu Linux przy użyciu szablonu ARM, znajdujący się w   
[Wdrożenia systemu RHEL 7.2 z pełne szyfrowanie dysków] (https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-full-disk-encrypted-rhel)

### <a name="enable-encryption-on-a-new-windows-iaas-vm-with-azure-managed-disk"></a>Włącz szyfrowanie dla nowej maszyny Wirtualnej IaaS Windows, za pomocą usługi Azure Managed Disk
 Szablon ARM dysków zarządzanych platformy Azure umożliwia tworzenie nowych zaszyfrowanych IaaS Maszynę wirtualną systemu Linux przy użyciu szablonu ARM, znajdujący się w   
 [Tworzenie nowej zaszyfrowanych Windows IaaS zarządzanych dysków maszyny Wirtualnej na podstawie obrazu z galerii] (https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image-managed-disks)

  > [!NOTE]
  >Jest to konieczne do migawki i/lub kopii zapasowej dysku zarządzanego na podstawie wystąpienia maszyny Wirtualnej poza i przed włączeniem usługi Azure Disk Encryption.  Migawkę dysku zarządzanego, może zostać pobrany z portalu lub można użyć usługi Azure Backup.  Tworzenie kopii zapasowych upewnij się, że opcja odzyskiwania możliwe w przypadku dowolnego nieoczekiwany błąd podczas szyfrowania.  Po nawiązaniu kopii zapasowej polecenia cmdlet polecenia Set-AzureRmVMDiskEncryptionExtension może służyć do szyfrowania dysków zarządzanych, określając parametr - skipVmBackup.  To polecenie zakończy się niepowodzeniem dla dysku zarządzanego na podstawie maszyny Wirtualnej, dopóki nie wykonano kopii zapasowej, a ten parametr został określony.    
 
### <a name="update-encryption-settings-of-an-existing-encrypted-non-premium-vm"></a>Aktualizowanie ustawień szyfrowania istniejącej maszyny wirtualnej zaszyfrowanych innych niż premium
  Używasz istniejące interfejsy obsługą szyfrowania dysków Azure dotyczące uruchamiania maszyny Wirtualnej [polecenia cmdlet PS, szablonów interfejsu wiersza polecenia lub ARM], można zaktualizować ustawień szyfrowania, takimi jak usługi AAD identyfikator/klucz tajny klienta, klucza szyfrowania [KEK], klucz szyfrowania funkcją BitLocker for Windows maszyny Wirtualnej lub hasło dla maszyny Wirtualnej systemu Linux itp. Aktualizacja ustawienia szyfrowania jest obsługiwane dla warstwy premium, jak i maszyny wirtualne magazynu w warstwie innej niż premium.

## <a name="appendix"></a>Dodatek
### <a name="connect-to-your-subscription"></a>Nawiązywanie połączenia z subskrypcją
Przed kontynuowaniem przejrzyj *wymagania wstępne* sekcję w tym artykule. Po upewnieniu się, że zostały spełnione wszystkie wymagania wstępne, połącz się z subskrypcją, wykonując następujące czynności:

1. Uruchom sesję programu Azure PowerShell i zaloguj się do konta platformy Azure za pomocą następującego polecenia:

    `Connect-AzureRmAccount`

2. Jeśli masz wiele subskrypcji i chcesz określić, należy użyć, wpisz następujące polecenie, aby zobaczyć subskrypcje dla konta:

    `Get-AzureRmSubscription`

3. Aby określić subskrypcję, której chcesz użyć, wpisz:

    `Select-AzureRmSubscription -SubscriptionName <Yoursubscriptionname>`

4. Aby sprawdzić, czy subskrypcja skonfigurowane jest poprawna, wpisz:

    `Get-AzureRmSubscription`

5. Aby upewnić się, że są zainstalowane polecenia cmdlet usługi Azure Disk Encryption, wpisz:

    `Get-command *diskencryption*`

6. Następujące dane wyjściowe potwierdzają instalacji programu PowerShell szyfrowania dysków Azure:

```
    PS C:\Windows\System32\WindowsPowerShell\v1.0> get-command *diskencryption*
    CommandType  Name                                         Source                                                             
    Cmdlet       Get-AzureRmVMDiskEncryptionStatus            AzureRM.Compute                                                    
    Cmdlet       Disable-AzureRmVMDiskEncryption              AzureRM.Compute                                                    
    Cmdlet       Set-AzureRmVMDiskEncryptionExtension         AzureRM.Compute                                                     
```

### <a name="prepare-a-pre-encrypted-windows-vhd"></a>Przygotowywanie wirtualnego dysku twardego Windows zaszyfrowane wstępnie
W kolejnych sekcjach są niezbędne do przygotowania zaszyfrowane wstępnie wirtualnego dysku twardego Windows do wdrożenia jako zaszyfrowanego dysku VHD w modelu IaaS platformy Azure. Skorzystaj z informacji do przygotowania i rozruchu świeże Windows maszyny Wirtualnej (VHD) w usłudze Azure Site Recovery lub na platformie Azure.

#### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>Aktualizacja zasad grupy, aby umożliwić bez modułu TPM do ochrony systemu operacyjnego
Ustawienia zasad grupy funkcji BitLocker **szyfrowania dysków funkcją BitLocker**, które znajdują się w obszarze **lokalne zasady komputera** > **konfiguracji komputera**  >  **Szablony administracyjne** > **składników Windows**. Zmień to ustawienie, aby **dyski systemu operacyjnego** > **wymagają dodatkowego uwierzytelniania przy uruchamianiu** > **Zezwalaj na funkcję BitLocker bez zgodnego modułu TPM**, jak pokazano na poniższej ilustracji:

![Ochrona przed złośliwym kodem zapewniana przez Microsoft na platformie Azure](./media/azure-security-disk-encryption/disk-encryption-fig8.png)

#### <a name="install-bitlocker-feature-components"></a>Zainstaluj składniki funkcji BitLocker
Dla systemu Windows Server 2012 i nowszych należy użyć następującego polecenia:

    dism /online /Enable-Feature /all /FeatureName:BitLocker /quiet /norestart

Dla systemu Windows Server 2008 R2 należy użyć następującego polecenia:

    ServerManagerCmd -install BitLockers

#### <a name="prepare-the-os-volume-for-bitlocker-by-using-bdehdcfg"></a>Przygotowanie woluminu systemu operacyjnego do używania funkcji BitLocker przy użyciu `bdehdcfg`
Aby skompresować partycji systemu operacyjnego i przygotowuje komputer do używania funkcji BitLocker, wykonaj następujące polecenie:

    bdehdcfg -target c: shrink -quiet

#### <a name="protect-the-os-volume-by-using-bitlocker"></a>Ochrona woluminu systemu operacyjnego za pomocą funkcji BitLocker
Użyj [ `manage-bde` ](https://technet.microsoft.com/library/ff829849.aspx) polecenie, aby włączyć szyfrowanie na wolumin rozruchowy przy użyciu zewnętrznego ochrony klucza. Również umieścić klucz zewnętrzny (plik .bek), na zewnętrznym dysku lub woluminie. Szyfrowanie jest włączone na wolumin systemowy/rozruchowy po następnym ponownym uruchomieniu.

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

> [!NOTE]
> Przygotowywanie maszyny Wirtualnej przy użyciu oddzielnych danych/zasobów wirtualnego dysku twardego w celu uzyskania klucza zewnętrznego za pomocą funkcji BitLocker.

#### <a name="encrypting-an-os-drive-on-a-running-linux-vm"></a>Szyfrowanie dysku systemu operacyjnego na uruchomionej maszynie Wirtualnej systemu Linux

##### <a name="prerequisites-for-os-disk-encryption"></a>Wymagania wstępne dotyczące szyfrowania dysku systemu operacyjnego

* Maszyna wirtualna musi używać dystrybucji, które są zgodne z szyfrowania dysku systemu operacyjnego zgodnie z zaleceniami z [Azure dysku szyfrowania — często zadawane pytania](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-faq#what-linux-distributions-does-azure-disk-encryption-support) 
* Maszyna wirtualna musi zostać utworzona z obrazu z witryny Marketplace usługi Azure Resource Manager.
* Maszyna wirtualna platformy Azure z co najmniej 4 GB pamięci RAM (zalecany rozmiar to 7 GB).
* (Dla systemu RHEL i CentOS) Wyłącz SELinux. Aby wyłączyć SELinux, zobacz "4.4.2. Wyłączanie SELinux"w [Przewodnik administratora i użytkownika SELinux](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux) na maszynie Wirtualnej.
* Po wyłączeniu SELinux ponowny rozruch maszyny Wirtualnej co najmniej raz.

##### <a name="steps"></a>Kroki
1. Tworzenie maszyny Wirtualnej przy użyciu jednej z dystrybucji określonej wcześniej.

 CentOS 7.2 szyfrowania dysku systemu operacyjnego jest obsługiwane za pośrednictwem niestandardowy obraz. Aby użyć tego obrazu, "7.2n" jako jednostkę SKU można określić podczas tworzenia maszyny Wirtualnej:
 ```
    Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"
 ```
2. Konfigurowanie maszyny Wirtualnej, zgodnie z potrzebami. Jeśli zamierzasz do szyfrowania wszystkich (systemu operacyjnego i danych) dysków, dyski z danymi musi być określona i instalacja z/etc/fstab.

 > [!NOTE]
 > Użyj identyfikatora UUID =... do określenia danych dysków w/etc/fstab zamiast określania nazwy urządzenia bloku (na przykład/dev/sdb1). Podczas szyfrowania, kolejność zmiany dyski na maszynie Wirtualnej. Jeśli maszyna wirtualna opiera się na określonej kolejności zablokować urządzenia, nie będzie można go zainstalować je po zaszyfrowaniu.

3. Wyloguj się z sesji SSH.

4. Aby zaszyfrować systemu operacyjnego, należy określić volumeType jako **wszystkich** lub **systemu operacyjnego** po użytkownik [włączyć szyfrowanie](#enable-encryption-on-existing-or-running-iaas-linux-vm-in-azure).

 > [!NOTE]
 > Wszystkie procesy przestrzeń użytkownika, które nie są uruchomione jako `systemd` usług powinny skasowane z `SIGKILL`. Uruchom ponownie maszynę Wirtualną. Po włączeniu szyfrowania dysku systemu operacyjnego na uruchomionej maszyny Wirtualnej, należy zaplanować przestój maszyny Wirtualnej.

5. Okresowo monitorować postęp szyfrowania zgodnie z instrukcjami podanymi w [następnej sekcji](#monitoring-os-encryption-progress).

6. Po Get-AzureRmVmDiskEncryptionStatus pokazuje "VMRestartPending", należy ponownie uruchomić maszynę Wirtualną, logując się do niego albo za pomocą witryny portal, programu PowerShell lub interfejsu wiersza polecenia.
    ```
    C:\> Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, reboot the VM
    ```
Przed ponownym uruchomieniu, zaleca się zapisanie [diagnostykę rozruchu](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/) maszyny wirtualnej.

#### <a name="monitoring-os-encryption-progress"></a>Monitorowanie postępu szyfrowania systemu operacyjnego
Możesz monitorować postęp szyfrowania systemu operacyjnego na trzy sposoby:

* Użyj `Get-AzureRmVmDiskEncryptionStatus` polecenia cmdlet i sprawdź pole komunikat dotyczący postępu:
    ```
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started
    ```
 Po maszyny Wirtualnej osiągnie "Do szyfrowania dysku systemu operacyjnego", trwa około 40 – 50 minut na usługę Premium storage kopii maszyny Wirtualnej.

 Z powodu [wystawiać #388](https://github.com/Azure/WALinuxAgent/issues/388) w WALinuxAgent, `OsVolumeEncrypted` i `DataVolumesEncrypted` są wyświetlane jako `Unknown` w niektórych dystrybucjach. Za pomocą WALinuxAgent w wersji 2.1.5 i nowszy, ten problem jest rozwiązany automatycznie. Jeśli widzisz `Unknown` w danych wyjściowych, można sprawdzić stanu szyfrowania dysków za pomocą Eksploratora zasobów Azure.

 Przejdź do [Eksploratora zasobów Azure](https://resources.azure.com/), a następnie rozwiń tę hierarchię w panelu wyboru po lewej stronie:

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

 W InstanceView przewiń w dół, aby wyświetlić stan szyfrowania dysków.

 ![Widok wystąpienia maszyny Wirtualnej](./media/azure-security-disk-encryption/vm-instanceview.png)

* Przyjrzyj się [diagnostykę rozruchu](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/). Komunikaty z rozszerzeniem ADE powinien być poprzedzony `[AzureDiskEncryption]`.

* Zaloguj się do maszyny Wirtualnej za pośrednictwem protokołu SSH i uzyskać dziennika rozszerzeń, od:

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

 Zaleca się, czy użytkownik nie zalogować się do maszyny Wirtualnej podczas szyfrowania systemu operacyjnego jest w toku. Skopiuj dzienniki tylko wtedy, gdy te dwie metody nie powiodło się.

#### <a name="prepare-a-pre-encrypted-linux-vhd"></a>Przygotowywanie wirtualnego dysku twardego systemu Linux zaszyfrowane wstępnie
##### <a name="ubuntu-16"></a>Ubuntu 16
Konfigurowanie szyfrowania podczas instalacji dystrybucji, wykonując następujące czynności:

1. Wybierz **Konfigurowanie woluminów zaszyfrowanych** po partycji dysków.

 ![Instalator systemu Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2. Tworzenie dysku rozruchowego oddzielne nie muszą być szyfrowane. Szyfrowanie dysku głównego.

 ![Instalator systemu Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3. Podaj hasło. Jest to hasło, które przekazujesz do magazynu kluczy.

 ![Instalator systemu Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4. Zakończ partycjonowania.

 ![Instalator systemu Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. Podczas rozruchu maszyny Wirtualnej i monit o podanie hasła, należy użyć hasło, które podano w kroku 3.

 ![Instalator systemu Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6. Przygotowywanie maszyny Wirtualnej do przekazywania na platformie Azure przy użyciu [w instrukcjach](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-ubuntu/). Nie należy uruchamiać w ostatnim kroku (anulowanie aprowizacji maszyny Wirtualnej) jeszcze.

Konfigurowanie szyfrowania do współpracy z platformą Azure, wykonując następujące czynności:

1. Utwórz plik w obszarze /usr/local/sbin/azure_crypt_key.sh, z zawartością w poniższym skrypcie. Należy zwrócić uwagę na KeyFileName, ponieważ jest to nazwa pliku hasło używane przez platformę Azure.

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

2. Zmienianie konfiguracji crypt w */etc/crypttab*. Powinny wyglądać następująco:
 ```
    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh
    ```

3. Jeśli edytujesz *azure_crypt_key.sh* w Windows i skopiowany na systemie Linux Uruchom `dos2unix /usr/local/sbin/azure_crypt_key.sh`.

4. Dodaj uprawnienia pliku wykonywalnego do skryptu:
 ```
    chmod +x /usr/local/sbin/azure_crypt_key.sh
 ```
5. Edytuj */etc/initramfs-tools/modules* , dodając wiersze:
 ```
    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1
```
6. Uruchom `update-initramfs -u -k all` initramfs, aby zaktualizować `keyscript` zaczęły obowiązywać.

7. Teraz można anulować aprowizację maszyny Wirtualnej.

 ![Instalator systemu Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig6.png)

8. Przejdź do kolejnego etapu i [przekazywanie wirtualnego dysku twardego](#upload-encrypted-vhd-to-an-azure-storage-account) na platformie Azure.

##### <a name="opensuse-132"></a>openSUSE 13.2
Aby skonfigurować szyfrowanie podczas instalacji dystrybucji, wykonaj następujące czynności:
1. Podczas dzielenia dysków na partycje wybrać **szyfrowanie woluminu grupy**, a następnie wprowadź hasło. Jest to hasło, który zostanie przekazany do magazynu kluczy.

 ![openSUSE 13.2 Setup](./media/azure-security-disk-encryption/opensuse-encrypt-fig1.png)

2. Rozruch maszyny Wirtualnej, przy użyciu hasła.

 ![openSUSE 13.2 Setup](./media/azure-security-disk-encryption/opensuse-encrypt-fig2.png)

3. Przygotowywanie maszyny Wirtualnej do przekazywania na platformie Azure, postępując zgodnie z instrukcjami wyświetlanymi w [przygotowywanie maszyny wirtualnej systemu SLES lub openSUSE dla platformy Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-suse-create-upload-vhd/#prepare-opensuse-131). Nie należy uruchamiać w ostatnim kroku (anulowanie aprowizacji maszyny Wirtualnej) jeszcze.

Aby skonfigurować szyfrowanie w celu współpracy z platformą Azure, wykonaj następujące czynności:
1. Edytuj /etc/dracut.conf i Dodaj następujący wiersz:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```
2. Komentarz następujące wiersze do końca /usr/lib/dracut/modules.d/90crypt/module-setup.sh pliku:
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
I Zamień wszystkie wystąpienia klasy:
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
5. Uruchom `/usr/sbin/dracut -f -v` można zaktualizować initrd.

6. Teraz można anulować aprowizację maszyny Wirtualnej i [przekazywanie wirtualnego dysku twardego](#upload-encrypted-vhd-to-an-azure-storage-account) na platformie Azure.

##### <a name="centos-7"></a>CentOS 7
Aby skonfigurować szyfrowanie podczas instalacji dystrybucji, wykonaj następujące czynności:
1. Wybierz **szyfrowanie danych** po partycji dysków.

 ![Instalator centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig1.png)

2. Upewnij się, że **Szyfruj** jest zaznaczone dla partycji katalogu głównego.

 ![Instalator centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig2.png)

3. Podaj hasło. Jest to hasło, który zostanie przekazany do magazynu kluczy.

 ![Instalator centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig3.png)

4. Podczas rozruchu maszyny Wirtualnej i monit o podanie hasła, należy użyć hasło, które podano w kroku 3.

 ![Instalator centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig4.png)

5. Przygotowywanie maszyny Wirtualnej do przekazywania na platformie Azure przy użyciu instrukcji "CentOS 7.0 +" w [przygotowywanie maszyny wirtualnej z systemem CentOS dla platformy Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-centos/#centos-70). Nie należy uruchamiać w ostatnim kroku (anulowanie aprowizacji maszyny Wirtualnej) jeszcze.

6. Teraz można anulować aprowizację maszyny Wirtualnej i [przekazywanie wirtualnego dysku twardego](#upload-encrypted-vhd-to-an-azure-storage-account) na platformie Azure.

Aby skonfigurować szyfrowanie w celu współpracy z platformą Azure, wykonaj następujące czynności:

1. Edytuj /etc/dracut.conf i Dodaj następujący wiersz:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```

2. Komentarz następujące wiersze do końca /usr/lib/dracut/modules.d/90crypt/module-setup.sh pliku:
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
I Zamień wszystkie wystąpienia klasy:
```
    if [ -z "$DRACUT_SYSTEMD" ]; then
```
na
```
    if [ 1 ]; then
```
4. Edytuj /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh i Dołącz podany kod po "# Otwórz LUKS urządzenie":
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
5. Uruchom "/ usr/sbin/dracut - f - v" można zaktualizować initrd.

![Instalator centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig5.png)

### <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Przekazywanie zaszyfrowanego dysku VHD do konta usługi Azure storage
Po włączeniu funkcji BitLocker szyfrowania lub szyfrowania DM-Crypt lokalny wirtualny dysk twardy zaszyfrowanych wymaga do przekazania do swojego konta magazynu.

    Add-AzureRmVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]

### <a name="upload-the-disk-encryption-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>Przekaż klucz tajny szyfrowania dysku dla wstępnie zaszyfrowanej maszyny Wirtualnej do magazynu kluczy
Klucz tajny szyfrowania dysku, który został uzyskany wcześniej należy przekazać w formie wpisu tajnego w magazynie kluczy. Magazyn kluczy wymaga szyfrowania dysku i włączonymi uprawnieniami dla klienta usługi Azure AD.

    $AadClientId = "YourAADClientId"
    $AadClientSecret = "YourAADClientSecret"

    $key vault = New-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

    Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
    Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption


#### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>Wpis tajny szyfrowania dysku nie jest szyfrowana za pomocą klucza KEK
Aby skonfigurować wpisu tajnego w magazynie kluczy, użyj [Set-AzureKeyVaultSecret](/powershell/module/azurerm.keyvault/set-azurekeyvaultsecret). Jeśli masz maszynę wirtualną Windows, plik klucza szyfrowania bloków jest zakodowany jako ciąg w formacie base64 i następnie przekazywane do usługi key vault przy użyciu `Set-AzureKeyVaultSecret` polecenia cmdlet. Dla systemu Linux hasło jest zakodowany jako ciąg w formacie base64, a następnie przekazywane do magazynu kluczy. Ponadto upewnij się, że następujące znaczniki są ustawione podczas tworzenia klucza tajnego w magazynie kluczy.

    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $secretName = [guid]::NewGuid().ToString()
    $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
    $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

    $secret = Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
    $secretUrl = $secret.Id

Użyj `$secretUrl` w następnym kroku zapoznać [dołączenie dysku systemu operacyjnego bez użycia klucza KEK](#without-using-a-kek).

#### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>Wpis tajny szyfrowania dysku zaszyfrowane przy użyciu klucza KEK
Przed przekazaniem wpisu tajnego do magazynu kluczy, można opcjonalnie zaszyfrować przy użyciu klucza szyfrowania. Użyj zawijania [API](https://msdn.microsoft.com/library/azure/dn878066.aspx) najpierw szyfrowania klucza tajnego przy użyciu klucza szyfrowania. Dane wyjściowe tej operacji zawijania to ciąg zakodowany w adresie URL base64, możesz następnie przekazać jako wpis tajny przy użyciu [ `Set-AzureKeyVaultSecret` ](/powershell/module/azurerm.keyvault/set-azurekeyvaultsecret) polecenia cmdlet.

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

Użyj `$KeyEncryptionKey` i `$secretUrl` w następnym kroku zapoznać [dołączenie dysku systemu operacyjnego przy użyciu klucza KEK](#using-a-kek).

### <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>Określ adres URL wpisu tajnego po dołączeniu dysku systemu operacyjnego
#### <a name="without-using-a-kek"></a>Bez użycia klucza KEK
Podczas dołączania dysku systemu operacyjnego, musisz przekazać `$secretUrl`. Adres URL został wygenerowany w sekcji "nie jest szyfrowana za pomocą klucza KEK tajny szyfrowania dysku".

    Set-AzureRmVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl

#### <a name="using-a-kek"></a>Za pomocą klucza KEK
Po dołączeniu dysku systemu operacyjnego, należy przekazać `$KeyEncryptionKey` i `$secretUrl`. Adres URL został wygenerowany w sekcji "nie jest szyfrowana za pomocą klucza KEK tajny szyfrowania dysku".

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
[Zapoznaj się z usługi Azure Disk Encryption przy użyciu programu Azure PowerShell — część 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/16/explore-azure-disk-encryption-with-azure-powershell.aspx?wa=wsignin1.0)  
[Zapoznaj się z usługi Azure Disk Encryption przy użyciu programu Azure PowerShell — część 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx)
