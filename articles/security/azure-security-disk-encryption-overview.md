---
title: Co to jest usługa Azure Disk Encryption?
description: Ten artykuł zawiera omówienie usługi Azure Disk Encryption
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 06/05/2019
ms.custom: seodec18
ms.openlocfilehash: 9b00a5262b1e144aa47cd7fd640906225ff4fecd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67068789"
---
# <a name="azure-disk-encryption-overview"></a>Omówienie usługi Azure Disk Encryption

Usługa Azure Disk Encryption ułatwia ochronę i chronić dane zgodnie z wymaganiami bezpieczeństwa i zgodności obowiązującymi w organizacji. Używa ona [funkcji BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) funkcja systemu Windows i [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) funkcji systemu Linux, aby zapewnić szyfrowanie woluminów dysków systemu operacyjnego i danych maszyn wirtualnych platformy Azure (maszyny wirtualne). Jest także zintegrowana z [usługi Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) ułatwiające kontrolę oraz zarządza wpisów tajnych oraz ich i kluczami szyfrowania dysków i zapewnia, że wszystkie dane na dyskach maszyn wirtualnych są szyfrowane, gdy znajduje się w usłudze Azure storage. Maszyny wirtualne systemu Linux i szyfrowania dysku dla Windows Azure jest ogólnie dostępna we wszystkich publicznych regionach platformy Azure i regiony platformy Azure Government dla standardowych maszyn wirtualnych i maszyn wirtualnych przy użyciu usługi Azure Premium Storage. 

Jeśli używasz usługi Azure Security Center, jest alert, jeśli masz maszyny wirtualne, które nie są szyfrowane. Alerty wyświetlane jako o wysokiej ważności i zaleca się zaszyfrowanie tych maszyn wirtualnych.

![Alert szyfrowania dysków Azure Security Center](media/azure-security-disk-encryption/security-center-disk-encryption-fig1.png)

> [!NOTE]
> Zastosowanie niektórych zaleceń zamieszczonych może zwiększyć danych, sieci, lub użycia zasobów obliczeniowych i powodować dodatkowych kosztów licencji lub subskrypcji.


## <a name="encryption-scenarios"></a>Scenariuszy szyfrowania

Za pomocą usługi Azure Disk Encryption adresów organizacyjnych bezpieczeństwa i zgodności z wymaganiami zabezpieczając maszynach wirtualnych platformy Azure w stanie spoczynku przy użyciu standardowych w branży technologii. Można również skonfigurować maszyny wirtualne do rozruchu w obszarze kluczami kontrolowane przez klienta i zasadami (BYOK) i Przeprowadź inspekcję użycia tych kluczy w magazynie kluczy.

Usługa Azure Disk Encryption obsługuje następujących scenariuszy:

* Włączanie i wyłączanie szyfrowania na nowe maszyny wirtualne utworzone na podstawie obsługiwanych obrazów w galerii platformy Azure.
* Włączanie i wyłączanie szyfrowania na istniejących maszynach wirtualnych, które działają na platformie Azure.
* Włączanie i wyłączanie szyfrowania na nowe Windows maszyny wirtualne utworzone na podstawie wstępnie zaszyfrowany dysk VHD i kluczy szyfrowania.
* Włączanie i wyłączanie szyfrowania na skalowania maszyn wirtualnych Windows ustawia.
* Włączanie i wyłączanie szyfrowania danych dysków dla zestawów skalowania maszyn wirtualnych systemu Linux.
* Włączanie i wyłączanie szyfrowania maszyn wirtualnych dysku zarządzanego.
* Aktualizowanie ustawień szyfrowania zaszyfrowanych istniejących w warstwie Premium i inne niż Premium Storage Maszynami wirtualnymi.
* Tworzenie kopii zapasowej i przywracanie zaszyfrowanych maszyn wirtualnych.
* Przenieś swoje własne szyfrowanie (BYOE) i użycia własnych kluczy scenariuszy (BYOK), w których klientom korzystać ze swoich własnych kluczy szyfrowania i przechowywać je w usłudze Azure key vault.

Obsługuje ona również następujące scenariusze dla maszyn wirtualnych, gdy są włączone w systemie Microsoft Azure:

* Integracja z usługą Azure Key Vault.
* [Maszyny wirtualne w warstwie standardowa](https://azure.microsoft.com/pricing/details/virtual-machines/). [Maszyny wirtualne systemu Linux](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) w tych warstwach, musi spełniać wymagania dotyczące minimalnej ilości pamięci, 7 GB. 
* Włączanie szyfrowania na Windows i maszyn wirtualnych systemu Linux, dysku zarządzanego i skalowania maszyn wirtualnych w zestawie z obsługiwanych obrazów w galerii systemu Azure.
* Wyłączenie szyfrowania systemu operacyjnego i danych maszyn wirtualnych w zestawie dysków dla maszyn wirtualnych Windows, skalowania i maszyn wirtualnych dysku zarządzanego.
* Wyłączenie szyfrowania dla dysków z danymi maszyn wirtualnych z systemem Linux i skalowania maszyn wirtualnych w zestawie i maszyn wirtualnych dysku zarządzanego.
* Włączanie szyfrowania na maszynach wirtualnych z systemem Windows systemu operacyjnego klienta.
* Włączanie szyfrowania dla woluminów przy użyciu ścieżki instalacji.
* Włączanie szyfrowania na maszynach wirtualnych systemu Linux, które są skonfigurowane przy użyciu rozkładanie (RAID) przy użyciu mdadm.
* Włączanie szyfrowania na maszynach wirtualnych systemu Linux, używanego przez LVM dla dysków z danymi.
* Włączanie szyfrowania na dyskach systemu operacyjnego maszyny Wirtualnej systemu Linux i danych.

   > [!NOTE]
   > Szyfrowanie dysku systemu operacyjnego dla niektórych dystrybucjach systemu Linux nie jest obsługiwane. Aby uzyskać więcej informacji, zobacz [często zadawane pytania dotyczące usługi Azure dysku szyfrowanie](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) artykułu.
   
* Włączanie szyfrowania na maszynach wirtualnych, które są skonfigurowane przy użyciu funkcji miejsca do magazynowania systemu Windows począwszy od wersji programu Windows Server 2016.
* Tworzenie kopii zapasowej i przywracanie zaszyfrowanych maszyn wirtualnych, zarówno klucz szyfrowania klucza (KEK), jak i bez klucza KEK scenariuszy.

Usługa Azure Disk Encryption nie działa w przypadku następujących scenariuszy, funkcji i technologii:

* Szyfrowanie warstwa podstawowa maszyny Wirtualnej lub maszyny wirtualne utworzone za pośrednictwem klasycznego metody tworzenia maszyny Wirtualnej.
* Wyłączanie szyfrowania dysku systemu operacyjnego lub dysku danych maszyny wirtualnej systemu Linux, gdy jest zaszyfrowany dysk systemu operacyjnego.
* Ustawia szyfrowania dysku systemu operacyjnego dla skalowania maszyn wirtualnych z systemem Linux.
* Szyfrowanie Windows maszyny wirtualne skonfigurowane przy użyciu systemów opartych na oprogramowaniu RAID.
* Szyfrowanie niestandardowych obrazów maszyn wirtualnych systemu Linux.
* Integracja z lokalnym systemem zarządzania kluczami.
* Usługa pliki systemu Azure (udostępnionego systemu plików).
* Sieciowy System plików (NFS).
* Woluminy dynamiczne.

## <a name="encryption-features"></a>Funkcje szyfrowania

Po włączeniu i wdrożeniu usługi Azure Disk Encryption maszyn wirtualnych platformy Azure, można skonfigurować następujące funkcje do włączenia:

* Szyfrowanie woluminu systemu operacyjnego, aby chronić wolumin rozruchowy przechowywanych w magazynie.
* Szyfrowanie woluminów danych, aby chronić woluminy danych przechowywanych w magazynie.
* Wyłączenie szyfrowania dla dysków systemu operacyjnego i danych w przypadku maszyn wirtualnych Windows.
* Wyłączenie szyfrowania danych dysków maszyn wirtualnych systemu Linux (tylko wtedy, gdy nie jest zaszyfrowany dysk systemu operacyjnego).
* Ochrona kluczy szyfrowania i wpisy tajne w ramach subskrypcji usługi Azure Key Vault.
* Raportowanie stanu szyfrowania zaszyfrowanych maszyn wirtualnych.
* Usuwanie ustawień konfiguracji szyfrowania dysków z maszyny Wirtualnej.
* Wykonywanie kopii zapasowych i przywracanie zaszyfrowanych maszyn wirtualnych przy użyciu usługi Azure Backup.

Usługa Azure Disk Encryption dla maszyn wirtualnych dla Windows i Linux obejmują:

* [Rozszerzenie szyfrowania dysku dla Windows](../virtual-machines/extensions/azure-disk-enc-windows.md).
* [Rozszerzenie szyfrowania dysku dla systemu Linux](../virtual-machines/extensions/azure-disk-enc-linux.md).
* [Polecenia cmdlet programu PowerShell dysku szyfrowanie](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-2.2.0).
* [Polecenia cmdlet szyfrowania dysku wiersza polecenia platformy Azure](/cli/azure/vm/encryption?view=azure-cli-latest).
* [Szablony usługi Azure Resource Manager szyfrowania dysku](azure-security-disk-encryption-appendix.md#resource-manager-templates).

Usługa Azure Disk Encryption jest obsługiwana na maszynach wirtualnych z systemem Windows lub systemu operacyjnego Linux. Aby uzyskać więcej informacji na temat obsługiwanych systemów operacyjnych, zobacz [— często zadawane pytania](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport).

> [!NOTE]
> Nie ma dodatkowych opłat do szyfrowania dysków maszyn wirtualnych za pomocą usługi Azure Disk Encryption. Standardowa [cenach usługi Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) ma zastosowanie do magazynu kluczy, który służy do przechowywania kluczy szyfrowania. 

## <a name="encryption-workflow"></a>Szyfrowanie przepływu pracy

Aby włączyć szyfrowanie dysków dla Windows i maszyn wirtualnych systemu Linux, wykonaj następujące czynności:

1. Zgódź się na włączyć szyfrowanie dysku przy użyciu szablonu Menedżera zasobów szyfrowania dysków Azure, poleceń cmdlet programu PowerShell lub interfejsu wiersza polecenia platformy Azure, a następnie określ konfiguracji szyfrowania.

   * W scenariuszu wirtualnego dysku twardego szyfrowane klienta należy przekazać zaszyfrowanego dysku VHD do konta magazynu i materiału klucza szyfrowania do magazynu kluczy. Następnie podaj konfiguracji szyfrowania, aby włączyć szyfrowanie na utworzonej nowej maszynie Wirtualnej.
   * W przypadku nowych maszyn wirtualnych, które są tworzone na podstawie obrazów Galeria obsługiwanych i istniejących maszyn wirtualnych działających na platformie Azure Podaj konfiguracji szyfrowania, aby włączyć szyfrowanie na maszynie Wirtualnej.

1. Udzielanie dostępu do platformy Azure, aby odczytać materiału klucza szyfrowania (klucze szyfrowania funkcją BitLocker dla systemów Windows) i hasło dla systemu Linux z własnego magazynu kluczy, aby włączyć szyfrowanie na maszynie Wirtualnej.

1. Azure aktualizuje modelu usługi maszyny Wirtualnej przy użyciu szyfrowania i Konfiguracja usługi key vault i konfiguruje zaszyfrowanej maszyny Wirtualnej.

   ![Ochrona przed złośliwym kodem zapewniana przez Microsoft na platformie Azure](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

## <a name="decryption-workflow"></a>Odszyfrowywanie przepływu pracy
Aby wyłączyć szyfrowanie dysków dla maszyn wirtualnych, wykonaj następujące kroki ogólne:

1. Wybierz umożliwia wyłączenie szyfrowania (odszyfrowywania) na uruchomionej maszyny Wirtualnej na platformie Azure i określ żądaną konfigurację odszyfrowywania. Można wyłączyć przy użyciu szablonu Menedżera zasobów szyfrowania dysków Azure, poleceń cmdlet programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

   Ten krok powoduje wyłączenie szyfrowania systemu operacyjnego lub ilości danych lub obu tych programów na uruchomioną maszynę Wirtualną Windows. Jak wspomniano w poprzedniej sekcji, wyłączenie szyfrowania dysku systemu operacyjnego dla systemu Linux nie jest obsługiwane. Krok odszyfrowywania jest dozwolona tylko w przypadku dysków z danymi na maszynach wirtualnych z systemem Linux, tak długo, jak dysk systemu operacyjnego nie jest zaszyfrowany.

1. Aktualizacje platformy Azure w modelu usługi maszyny Wirtualnej i maszyny Wirtualnej jest oznaczony jako odszyfrować. Zawartość maszyny Wirtualnej nie są szyfrowane, gdy.

   > [!NOTE]
   > Wyłącz operacja szyfrowania nie powoduje usunięcia magazynu kluczy i materiału klucza szyfrowania (klucze szyfrowania funkcją BitLocker dla systemów Windows) lub hasło dla systemu Linux.
   >
   > Wyłączenie szyfrowania dysku systemu operacyjnego dla systemu Linux nie jest obsługiwane. Krok odszyfrowywania jest dozwolona tylko w przypadku dysków z danymi na maszynach wirtualnych z systemem Linux.
   >
   > Wyłączenie szyfrowania dysku danych dla systemu Linux nie jest obsługiwana, jeśli dysk systemu operacyjnego został zaszyfrowany.


## <a name="encryption-workflow-previous-release"></a>Szyfrowanie przepływu pracy (poprzedniej wersji)

Nowa wersja usługi Azure Disk Encryption eliminuje konieczność zapewnienia parametrem aplikacji usługi Azure Active Directory (Azure AD), aby włączyć szyfrowanie dysku maszyny Wirtualnej. Za pomocą nowej wersji są już wymagane podanie poświadczeń usługi Azure AD podczas wykonywania kroku Włączanie szyfrowania. Wszystkie nowe maszyny wirtualne muszą być szyfrowane bez parametrów aplikacji usługi Azure AD, korzystając z nowej wersji. Maszyny wirtualne, które już zostały zaszyfrowane za pomocą aplikacji usługi Azure AD, parametry są nadal obsługiwane i powinno być kontynuowane przy użyciu składni usługi Azure AD. Aby włączyć szyfrowanie dysków dla Windows i maszyn wirtualnych systemu Linux (poprzedniej wersji), wykonaj następujące czynności:

1. Wybierz scenariusz szyfrowania z scenariuszy, na liście [scenariuszy szyfrowania](#encryption-scenarios) sekcji.

1. Zgódź się na włączyć szyfrowanie dysku przy użyciu szablonu Menedżera zasobów szyfrowania dysków Azure, poleceń cmdlet programu PowerShell lub interfejsu wiersza polecenia platformy Azure, a następnie określ konfiguracji szyfrowania.

   * W scenariuszu wirtualnego dysku twardego szyfrowane klienta należy przekazać zaszyfrowanego dysku VHD do konta magazynu i materiału klucza szyfrowania do magazynu kluczy. Następnie podaj konfiguracji szyfrowania, aby włączyć szyfrowanie na utworzonej nowej maszynie Wirtualnej.
   * W przypadku nowych maszyn wirtualnych, które są tworzone w portalu Marketplace i istniejących maszyn wirtualnych działających na platformie Azure Podaj konfiguracji szyfrowania, aby włączyć szyfrowanie na maszynie Wirtualnej.

1. Udzielanie dostępu do platformy Azure, aby odczytać materiału klucza szyfrowania (klucze szyfrowania funkcją BitLocker dla systemów Windows) i hasło dla systemu Linux z własnego magazynu kluczy, aby włączyć szyfrowanie na maszynie Wirtualnej.

1. Podaj tożsamość aplikacji usługi Azure AD, można zapisać klucz szyfrowania materiału do magazynu kluczy. Ten krok powoduje włączenie szyfrowania na maszynie Wirtualnej w scenariuszach wymienionych w kroku 2.

1. Azure aktualizuje modelu usługi maszyny Wirtualnej przy użyciu szyfrowania i Konfiguracja usługi key vault i konfiguruje zaszyfrowanej maszyny Wirtualnej.


## <a name="terminology"></a>Terminologia
W poniższej tabeli opisano niektóre typowe terminy używane w dokumentacji szyfrowania dysków Azure:

| Terminologia | Definicja |
| --- | --- |
| Azure AD | [Usługi Azure AD](https://azure.microsoft.com/documentation/services/active-directory/) konto jest używane do uwierzytelniania, przechowywania i pobierania wpisów tajnych w magazynie kluczy. |
| W usłudze Azure Key Vault | Key Vault to usługa kryptograficznych, key management, która opiera się na przetwarzaniu standardów FIPS (Federal Information) zweryfikowanych sprzętowych modułach zabezpieczeń. Standardy te pomagają chronić klucze kryptograficzne i wpisy tajne poufnych. Aby uzyskać więcej informacji, zobacz [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentacji. |
| BitLocker |[Funkcja BitLocker](https://technet.microsoft.com/library/hh831713.aspx) to branży Windows woluminu szyfrowania technologia, która służy do włączania szyfrowania dysków na maszynach wirtualnych Windows. |
| KSB | Klucze szyfrowania funkcją BitLocker (klucz szyfrowania bloków) są używane do szyfrowania woluminu rozruchowego systemu operacyjnego i woluminów danych. BEKs są chronione w magazynie kluczy jako wpisy tajne. |
| Interfejs wiersza polecenia platformy Azure | [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) jest zoptymalizowany do zarządzania i administrowania zasobami platformy Azure z poziomu wiersza polecenia.|
| DM-Crypt |[DM-Crypt](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt) jest podsystem opartych na systemie Linux lub przezroczystego szyfrowania dysku, który służy do włączania szyfrowania dysków na maszynach wirtualnych systemu Linux. |
| Klucz szyfrowania klucza (KEK) | Klucz asymetryczny (RSA 2048), można użyć do ochrony lub zawijania wpisu tajnego. Możesz podać sprzętowego modułu zabezpieczeń (HSM)-chronione klucza lub klucza chronionego przez oprogramowanie. Aby uzyskać więcej informacji, zobacz [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentacji. |
| Polecenia cmdlet programu PowerShell | Aby uzyskać więcej informacji, zobacz [poleceń cmdlet programu Azure PowerShell](/powershell/azure/overview). |

## <a name="next-steps"></a>Kolejne kroki

Aby rozpocząć pracę, zobacz [wymagania wstępne dotyczące usługi Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md).

