---
title: Przegląd — usługa Azure Disk Encryption dla maszyn wirtualnych IaaS | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie programu Microsoft Azure Disk Encryption dla maszyn wirtualnych IaaS.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/16/2019
ms.custom: seodec18
ms.openlocfilehash: 66d788aec83e3e57a49b063f2ca80484360f639d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60612046"
---
# <a name="azure-disk-encryption-for-iaas-vms"></a>Usługa Azure Disk Encryption dla maszyn wirtualnych IaaS

Microsoft Azure jest zobowiązana do zapewnienia prywatności danych i niezależność danych. System Azure umożliwia kontrolowanie danych hostowanymi na platformie Azure za pomocą wielu zaawansowanych technologii szyfrowania, kontroli i zarządzanie kluczami szyfrowania i kontroli i inspekcji dostępu do danych. Formant ten zapewnia klientom platformy Azure z możliwością wyboru rozwiązania, który najlepiej zaspokaja ich potrzeby biznesowe. W tym artykule przedstawiono rozwiązanie technologiczne: "Szyfrowania dysku dla Windows i Linux IaaS maszyn wirtualnych (VM)." Technologia ta pomaga chronić i chronić dane zgodnie z wymaganiami co do bezpieczeństwa organizacji i zobowiązaniami w zakresie zgodności. 

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]


## <a name="overview"></a>Przegląd

Usługa Azure Disk Encryption jest możliwość ułatwiające szyfrowanie dysków Windows i maszyn wirtualnych IaaS z systemem Linux. Szyfrowanie dysków wykorzystuje będące standardami branżowymi [funkcji BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) funkcja systemu Windows i [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) funkcji systemu Linux, aby zapewnić szyfrowanie woluminów dysków systemu operacyjnego i danych. To rozwiązanie jest zintegrowana z usługą [usługi Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) ułatwiają kontrolowanie i zarządzaniu wpisami tajnymi i kluczami szyfrowania dysków. To rozwiązanie zapewnia również, że wszystkie dane na dyskach maszyn wirtualnych są szyfrowane, gdy w usłudze Azure storage.

Szyfrowanie dysków dla Windows i maszyn wirtualnych IaaS z systemem Linux jest ogólnie dostępna we wszystkich publicznych regionach platformy Azure i regiony platformy Azure Government dla standardowych maszyn wirtualnych i maszyn wirtualnych przy użyciu usługi Azure Premium Storage. Po zastosowaniu rozwiązania do zarządzania szyfrowanie dysków może spełnić następujące wymagania biznesowe:

* Maszyny wirtualne IaaS są chronione w stanie spoczynku przy użyciu standardowych w branży technologii umożliwiającą organizacyjnych wymagań dotyczących zabezpieczeń i zgodności.
* Rozruchu maszyn wirtualnych IaaS w ramach kontrolowane przez klienta kluczy i zasad. Można przeprowadzać ich inspekcje użycia ich w magazynie kluczy.

Jeśli używasz usługi Azure Security Center, jest alert, jeśli masz maszyny wirtualne, które nie są szyfrowane. Alerty wyświetlane jako o wysokiej ważności i zaleca się zaszyfrowanie tych maszyn wirtualnych.

![Alert szyfrowania dysków Azure Security Center](media/azure-security-disk-encryption/security-center-disk-encryption-fig1.png)

> [!NOTE]
> Zastosowanie niektórych zaleceń zamieszczonych może zwiększyć danych, sieci, lub użycia zasobów obliczeniowych i powodować dodatkowych kosztów licencji lub subskrypcji.


## <a name="encryption-scenarios"></a>Scenariuszy szyfrowania

Rozwiązanie szyfrowanie dysków obsługuje następujących scenariuszy:

* Włącza szyfrowanie na nowe Windows IaaS maszyny wirtualne utworzone na podstawie zaszyfrowane wstępnie wirtualnego dysku twardego i kluczy szyfrowania.
* Włącz szyfrowanie dla nowych maszyn wirtualnych IaaS utworzone na podstawie obsługiwanych obrazów w galerii platformy Azure.
* Włącz szyfrowanie dla istniejących maszyn wirtualnych IaaS uruchamianych na platformie Azure.
* Włącz szyfrowanie dla zestawów skalowania maszyn wirtualnych Windows.
* Włącz szyfrowanie dla dysków z danymi dla zestawów skalowania maszyn wirtualnych systemu Linux.
* Wyłącz szyfrowanie dla maszyn wirtualnych IaaS Windows.
* Wyłącz szyfrowanie dla dysków z danymi dla maszyn wirtualnych IaaS z systemem Linux.
* Wyłącz szyfrowanie dla zestawów skalowania maszyn wirtualnych Windows.
* Wyłącz szyfrowanie dla dysków z danymi dla zestawów skalowania maszyn wirtualnych systemu Linux.
* Włącz szyfrowanie maszyn wirtualnych dysku zarządzanego.
* Zaktualizuj ustawienia szyfrowania zaszyfrowanych istniejących w warstwie Premium i inne niż Premium Storage Maszynami wirtualnymi.
* Tworzenie kopii zapasowej i przywracanie zaszyfrowanych maszyn wirtualnych.

Rozwiązanie obsługuje następujące scenariusze dla maszyn wirtualnych IaaS, jeśli są włączone w systemie Microsoft Azure:

* Integracja z usługą Azure Key Vault.
* W warstwie standardowa maszyny wirtualne: [A, D, DS, G, GS, F i tak dalej, maszyny wirtualne z serii IaaS](https://azure.microsoft.com/pricing/details/virtual-machines/). [Maszyny wirtualne systemu Linux](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) w tych warstwach, musi spełniać wymagania dotyczące minimalnej ilości pamięci, 7 GB.
* Włączanie szyfrowania na Windows i maszyn wirtualnych IaaS z systemem Linux, dysku zarządzanego i skalowania maszyn wirtualnych w zestawie z obsługiwanych obrazów w galerii systemu Azure.
* Wyłącz szyfrowanie dla dysków systemu operacyjnego i danych Windows IaaS VMs, skalowania maszyn wirtualnych w zestawie i maszyn wirtualnych dysku zarządzanego.
* Wyłącz szyfrowanie na dyskach danych, dla systemu Linux IaaS maszyn wirtualnych skalowania maszyn wirtualnych w zestawie i maszyn wirtualnych dysku zarządzanego.
* Włącz szyfrowanie dla maszyn wirtualnych IaaS uruchamianych systemem operacyjnym klienta Windows.
* Włącz szyfrowanie na woluminach dzięki określeniu ścieżki instalacji.
* Włącz szyfrowanie dla maszyn wirtualnych systemu Linux, które są skonfigurowane przy użyciu rozkładanie (RAID) przy użyciu mdadm.
* Włącz szyfrowanie dla maszyn wirtualnych systemu Linux, używanego przez LVM dla dysków z danymi.
* Włącz szyfrowanie dla dysków systemu operacyjnego maszyny Wirtualnej systemu Linux i danych.

   > [!NOTE]
   > Szyfrowanie dysku systemu operacyjnego dla niektórych dystrybucjach systemu Linux nie jest obsługiwane. Aby uzyskać więcej informacji, zobacz [często zadawane pytania dotyczące usługi Azure dysku szyfrowanie](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) artykułu.
   
* Włącz szyfrowanie na maszynach wirtualnych, które są skonfigurowane przy użyciu funkcji miejsca do magazynowania systemu Windows począwszy od wersji programu Windows Server 2016.
* Zaktualizuj ustawienia szyfrowania dla zaszyfrowanego istniejących w warstwie Premium i innych niż - magazynu maszyny Wirtualnej usługi Premium.
* Tworzenie kopii zapasowej i przywracanie zaszyfrowanych maszyn wirtualnych, zarówno klucz szyfrowania klucza (KEK), jak i bez klucza KEK scenariuszy.
* Wszystkie publiczne platformy Azure i Azure dla instytucji rządowych regiony są obsługiwane.

Rozwiązanie nie obsługuje następujących scenariuszy, funkcji i technologii:

* Warstwa podstawowa maszyn wirtualnych IaaS.
* Wyłącz szyfrowanie dla dysku systemu operacyjnego dla maszyn wirtualnych IaaS z systemem Linux.
* Wyłącz szyfrowanie dla dysku danych, gdy dysk systemu operacyjnego jest zaszyfrowany dla maszyn wirtualnych IaaS z systemem Linux.
* Ustawia szyfrowania dysku systemu operacyjnego dla skalowania maszyn wirtualnych z systemem Linux.
* Maszyny wirtualne IaaS, które są tworzone za pomocą klasycznego metodę tworzenia maszyny Wirtualnej.
* Włącz szyfrowanie klientów niestandardowych obrazów na maszynach wirtualnych IaaS z systemem Linux.
* Integracja z systemem zarządzania kluczami w środowisku lokalnym.
* Usługa pliki systemu Azure (udostępnionego systemu plików).
* Sieciowy System plików (NFS).
* Woluminy dynamiczne.
* Windows maszyn wirtualnych, które są skonfigurowane przy użyciu systemów opartych na oprogramowaniu RAID.

## <a name="encryption-features"></a>Funkcje szyfrowania

Po włączeniu i wdrożeniu szyfrowanie dysków maszyn wirtualnych IaaS platformy Azure, w zależności od konfiguracji podane są włączone następujące możliwości:

* Szyfrowanie woluminu systemu operacyjnego, aby chronić wolumin rozruchowy przechowywanych w magazynie.
* Szyfrowanie woluminów danych, aby chronić woluminy danych przechowywanych w magazynie.
* Wyłącz szyfrowanie dla dysków systemu operacyjnego i danych dla maszyn wirtualnych IaaS Windows.
* Wyłącz szyfrowanie dla dysków z danymi dla maszyn wirtualnych IaaS z systemem Linux (tylko wtedy, gdy nie jest zaszyfrowany dysk systemu operacyjnego).
* Chroń klucze szyfrowania i wpisy tajne w ramach subskrypcji usługi Azure Key Vault.
* Raport stanu szyfrowania zaszyfrowanych maszyn wirtualnych IaaS.
* Usuń ustawienia konfiguracji szyfrowania dysków z maszyn wirtualnych IaaS.
* Tworzenie kopii zapasowej i przywracanie zaszyfrowanych maszyn wirtualnych przy użyciu usługi Azure Backup.

Usługa Azure Disk Encryption dla maszyn wirtualnych IaaS rozwiązania Windows i Linux obejmują:

* Rozszerzenie szyfrowania dysku dla Windows.
* Rozszerzenie szyfrowania dysku dla systemu Linux.
* Dysk szyfrowania poleceń cmdlet programu PowerShell.
* Polecenia cmdlet szyfrowania dysku wiersza polecenia platformy Azure.
* Szablony szyfrowania dysku usługi Azure Resource Manager.

Rozwiązanie Azure Disk Encryption jest obsługiwana na maszynach wirtualnych IaaS z uruchamiania Windows lub systemu operacyjnego Linux. Aby uzyskać więcej informacji na temat obsługiwanych systemów operacyjnych, zobacz [wymagania wstępne](azure-security-disk-encryption-prerequisites.md) artykułu.

> [!NOTE]
> Nie ma dodatkowych opłat do szyfrowania dysków maszyn wirtualnych za pomocą usługi Azure Disk Encryption. Standardowa [cenach usługi Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) ma zastosowanie do magazynu kluczy, który służy do przechowywania kluczy szyfrowania. 


## <a name="encryption-workflow"></a>Szyfrowanie przepływu pracy

Aby włączyć szyfrowanie dysków dla Windows i maszyn wirtualnych systemu Linux, wykonaj następujące czynności:

1. Wybierz scenariusz szyfrowania z scenariuszy, na liście [scenariuszy szyfrowania](#encryption-scenarios) sekcji.

1. Zgódź się na włączyć szyfrowanie dysku przy użyciu szablonu Menedżera zasobów szyfrowania dysków Azure, poleceń cmdlet programu PowerShell lub interfejsu wiersza polecenia platformy Azure, a następnie określ konfiguracji szyfrowania.

   * W scenariuszu wirtualnego dysku twardego szyfrowane klienta należy przekazać zaszyfrowanego dysku VHD do konta magazynu i materiału klucza szyfrowania do magazynu kluczy. Następnie podaj konfiguracji szyfrowania, aby włączyć szyfrowanie na utworzonej nowej maszynie Wirtualnej IaaS.
   * W przypadku nowych maszyn wirtualnych, które są tworzone w portalu Marketplace i istniejących maszyn wirtualnych działających na platformie Azure Podaj konfiguracji szyfrowania, aby włączyć szyfrowanie na maszynie Wirtualnej IaaS.

1. Udzielanie dostępu do platformy Azure, aby odczytać materiału klucza szyfrowania (klucze szyfrowania funkcją BitLocker dla systemów Windows) i hasło dla systemu Linux z własnego magazynu kluczy, aby włączyć szyfrowanie na maszynie Wirtualnej IaaS.

1. Azure aktualizuje modelu usługi maszyny Wirtualnej przy użyciu szyfrowania i Konfiguracja usługi key vault i konfiguruje zaszyfrowanej maszyny Wirtualnej.

   ![Ochrona przed złośliwym kodem zapewniana przez Microsoft na platformie Azure](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

## <a name="decryption-workflow"></a>Odszyfrowywanie przepływu pracy
Aby wyłączyć szyfrowanie dysków dla maszyn wirtualnych IaaS, wykonaj następujące kroki ogólne:

1. Wybierz umożliwia wyłączenie szyfrowania (odszyfrowywania) na uruchomionej maszynie Wirtualnej IaaS na platformie Azure i określ żądaną konfigurację odszyfrowywania. Można wyłączyć przy użyciu szablonu Menedżera zasobów szyfrowania dysków Azure, poleceń cmdlet programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

   Ten krok powoduje wyłączenie szyfrowania systemu operacyjnego lub ilości danych lub obu tych programów na uruchomionej maszynie Wirtualnej IaaS Windows. Jak wspomniano w poprzedniej sekcji, wyłączenie szyfrowania dysku systemu operacyjnego dla systemu Linux nie jest obsługiwane. Krok odszyfrowywania jest dozwolona tylko w przypadku dysków z danymi na maszynach wirtualnych z systemem Linux, tak długo, jak dysk systemu operacyjnego nie jest zaszyfrowany.

1. Aktualizacje platformy Azure, model usług maszyn wirtualnych i maszyn wirtualnych IaaS, jest oznaczana odszyfrować. Zawartość maszyny Wirtualnej nie są szyfrowane, gdy.

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

   * W scenariuszu wirtualnego dysku twardego szyfrowane klienta należy przekazać zaszyfrowanego dysku VHD do konta magazynu i materiału klucza szyfrowania do magazynu kluczy. Następnie podaj konfiguracji szyfrowania, aby włączyć szyfrowanie na utworzonej nowej maszynie Wirtualnej IaaS.
   * W przypadku nowych maszyn wirtualnych, które są tworzone w portalu Marketplace i istniejących maszyn wirtualnych działających na platformie Azure Podaj konfiguracji szyfrowania, aby włączyć szyfrowanie na maszynie Wirtualnej IaaS.

1. Udzielanie dostępu do platformy Azure, aby odczytać materiału klucza szyfrowania (klucze szyfrowania funkcją BitLocker dla systemów Windows) i hasło dla systemu Linux z własnego magazynu kluczy, aby włączyć szyfrowanie na maszynie Wirtualnej IaaS.

1. Podaj tożsamość aplikacji usługi Azure AD, można zapisać klucz szyfrowania materiału do magazynu kluczy. Ten krok powoduje włączenie szyfrowania na maszynie Wirtualnej IaaS w scenariuszach wymienionych w kroku 2.

1. Azure aktualizuje modelu usługi maszyny Wirtualnej przy użyciu szyfrowania i Konfiguracja usługi key vault i konfiguruje zaszyfrowanej maszyny Wirtualnej.


## <a name="terminology"></a>Terminologia
W poniższej tabeli opisano niektóre typowe terminy, które są używane w tej technologii:

| Terminologia | Definicja |
| --- | --- |
| Azure AD | [Usługi Azure AD](https://azure.microsoft.com/documentation/services/active-directory/) konto jest używane do uwierzytelniania, przechowywania i pobierania wpisów tajnych w magazynie kluczy. |
| W usłudze Azure Key Vault | Key Vault to usługa kryptograficznych, key management, która opiera się na przetwarzaniu standardów FIPS (Federal Information) zweryfikowanych sprzętowych modułach zabezpieczeń. Standardy te pomagają chronić klucze kryptograficzne i wpisy tajne poufnych. Aby uzyskać więcej informacji, zobacz [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentacji. |
| BitLocker |[Funkcja BitLocker](https://technet.microsoft.com/library/hh831713.aspx) to branży Windows woluminu szyfrowania technologia, która służy do włączania szyfrowania dysków na maszynach wirtualnych IaaS Windows. |
| KSB | Klucze szyfrowania funkcją BitLocker (klucz szyfrowania bloków) są używane do szyfrowania woluminu rozruchowego systemu operacyjnego i woluminów danych. BEKs są chronione w magazynie kluczy jako wpisy tajne. |
| Interfejs wiersza polecenia platformy Azure | [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) jest zoptymalizowany do zarządzania i administrowania zasobami platformy Azure z poziomu wiersza polecenia.|
| DM-Crypt |[DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) jest podsystem opartych na systemie Linux lub przezroczystego szyfrowania dysku, który służy do włączania szyfrowania dysków na maszynach wirtualnych IaaS z systemem Linux. |
| KEK | Klucz szyfrowania klucza (KEK) to klucz asymetryczny (RSA 2048), który służy do ochrony lub zawijania wpisu tajnego. Możesz podać sprzętowego modułu zabezpieczeń (HSM)-chronione klucza lub klucza chronionego przez oprogramowanie. Aby uzyskać więcej informacji, zobacz [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentacji. |
| Polecenia cmdlet programu PowerShell | Aby uzyskać więcej informacji, zobacz [poleceń cmdlet programu Azure PowerShell](/powershell/azure/overview). |

## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"]
> [Wymagania wstępne usługi Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md)
