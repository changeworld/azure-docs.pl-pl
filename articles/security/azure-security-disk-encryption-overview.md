---
title: Co to jest Azure Disk Encryption?
description: Ten artykuł zawiera omówienie Azure Disk Encryption
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 09/05/2019
ms.custom: seodec18
ms.openlocfilehash: 7855b1048826a9146e206226ae7326661e4609cb
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70764397"
---
# <a name="azure-disk-encryption-overview"></a>Przegląd Azure Disk Encryption

Azure Disk Encryption pomaga chronić dane i zabezpieczać je w celu spełnienia wymagań dotyczących zabezpieczeń i zgodności w organizacji. Używa funkcji [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) systemu Windows i funkcji [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) w systemie Linux, aby zapewnić szyfrowanie woluminów dla systemu operacyjnego i dysków danych maszyn wirtualnych platformy Azure. Jest ona również zintegrowana z [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) , aby pomóc Ci kontrolować klucze szyfrowania dysków i wpisy tajne oraz zarządzać nimi, a także zagwarantować, że wszystkie dane na dyskach maszyn wirtualnych są szyfrowane w stanie spoczynku w usłudze Azure Storage. Azure Disk Encryption dla maszyn wirtualnych z systemami Windows i Linux jest ogólnie dostępna we wszystkich regionach publicznych platformy Azure i Azure Government regionach dla standardowych maszyn wirtualnych i maszyn wirtualnych za pomocą usługi Azure Premium Storage. 

Jeśli używasz usługi Azure Security Center, jest alert, jeśli masz maszyny wirtualne, które nie są szyfrowane. Alerty wyświetlane jako o wysokiej ważności i zaleca się zaszyfrowanie tych maszyn wirtualnych.

![Alert szyfrowania dysków Azure Security Center](media/azure-security-disk-encryption/security-center-disk-encryption-fig1.png)

> [!NOTE]
> Zastosowanie niektórych zaleceń zamieszczonych może zwiększyć danych, sieci, lub użycia zasobów obliczeniowych i powodować dodatkowych kosztów licencji lub subskrypcji.


## <a name="encryption-scenarios"></a>Scenariuszy szyfrowania

Za pomocą Azure Disk Encryption można rozwiązać wymagania dotyczące zabezpieczeń i zgodności organizacji, zabezpieczając maszyny wirtualne platformy Azure w stanie spoczynku przy użyciu standardowej technologii szyfrowania w branży. Możesz również skonfigurować maszyny wirtualne do rozruchu w obszarze klucze i zasady kontrolowane przez klienta (BYOK) i Przeprowadź inspekcję użycia tych kluczy w magazynie kluczy.

Azure Disk Encryption obsługuje następujące scenariusze klientów:

* Włączanie i wyłączanie szyfrowania dla nowych maszyn wirtualnych utworzonych na podstawie obsługiwanych obrazów galerii platformy Azure.
* Włączanie i wyłączanie szyfrowania na istniejących maszynach wirtualnych działających na platformie Azure.
* Włączanie i wyłączanie szyfrowania na nowych maszynach wirtualnych z systemem Windows utworzonych na podstawie wstępnie zaszyfrowanego dysku VHD i kluczy szyfrowania.
* Włączanie i wyłączanie szyfrowania w zestawach skalowania maszyn wirtualnych z systemem Windows.
* Włączanie i wyłączanie szyfrowania na dyskach danych dla zestawów skalowania maszyn wirtualnych z systemem Linux.
* Włączanie i wyłączanie szyfrowania maszyn wirtualnych z dyskami zarządzanymi.
* Aktualizowanie ustawień szyfrowania istniejącej zaszyfrowanej maszyny wirtualnej w warstwie Premium i innej niż Premium Storage.
* Tworzenie kopii zapasowych i Przywracanie zaszyfrowanych maszyn wirtualnych.
* Korzystaj z własnego szyfrowania (BYOE) i dostarczaj własne scenariusze (BYOK), w których klienci używają własnych kluczy szyfrowania i przechowują je w magazynie kluczy platformy Azure.

Program obsługuje również następujące scenariusze dotyczące maszyn wirtualnych, gdy są one włączone w Microsoft Azure:

* Integracja z usługą Azure Key Vault.
* [Maszyny wirtualne w warstwie Standardowa](https://azure.microsoft.com/pricing/details/virtual-machines/) , które spełniają [wymagania dotyczące minimalnej ilości pamięci](azure-security-disk-encryption-prerequisites.md#supported-vm-sizes). 
* Włączanie szyfrowania na maszynach wirtualnych z systemem Windows i Linux, na dysku zarządzanym i w zestawach skalowania z obsługiwanych obrazów galerii platformy Azure.
* Wyłączanie szyfrowania na dyskach systemu operacyjnego i danych dla maszyn wirtualnych z systemem Windows, maszyn wirtualnych zestawu skalowania i maszyn wirtualnych z dyskami zarządzanymi.
* Wyłączanie szyfrowania na dyskach danych dla maszyn wirtualnych z systemem Linux, maszyn wirtualnych zestawu skalowania i maszyn wirtualnych z dyskami zarządzanymi.
* Włączanie szyfrowania na maszynach wirtualnych, na których działa klient systemu operacyjnego Windows.
* Włączanie szyfrowania woluminów ze ścieżkami instalacji.
* Włączanie szyfrowania na maszynach wirtualnych z systemem Linux, które są skonfigurowane przy użyciu funkcji rozkładania dysku (RAID) za pomocą mdadm.
* Włączanie szyfrowania na maszynach wirtualnych z systemem Linux, które używają LVM dla dysków danych.
* Włączanie szyfrowania w systemie operacyjnym i dyskach danych maszyny wirtualnej z systemem Linux.

   > [!NOTE]
   > Szyfrowanie dysku systemu operacyjnego dla niektórych dystrybucjach systemu Linux nie jest obsługiwane. Aby uzyskać więcej informacji, zobacz [obsługiwane systemy operacyjne Azure Disk Encryption: System](azure-security-disk-encryption-prerequisites.md#linux)Linux.
   
* Włączanie szyfrowania na maszynach wirtualnych skonfigurowanych przy użyciu funkcji miejsca do magazynowania systemu Windows począwszy od systemu Windows Server 2016. Bezpośrednie miejsca do magazynowania (S2D) nie jest jeszcze obsługiwana.
* Twórz i przywracaj kopie zapasowe zaszyfrowanych maszyn wirtualnych zarówno w przypadku usługi Key Encryption Key (KEK), jak i innych niż KEK.

Azure Disk Encryption nie działa w następujących scenariuszach, funkcjach i technologiach:

* Szyfrowanie maszyny wirtualnej warstwy podstawowej lub maszyn wirtualnych utworzonych za pomocą metody tworzenia klasycznej maszyny wirtualnej.
* Wyłączanie szyfrowania na dysku systemu operacyjnego lub dysku danych maszyny wirtualnej z systemem Linux podczas szyfrowania dysku systemu operacyjnego.
* Szyfrowanie dysku systemu operacyjnego dla zestawów skalowania maszyn wirtualnych z systemem Linux.
* Szyfrowanie maszyn wirtualnych z systemem Windows skonfigurowanych z oprogramowaniem opartym na oprogramowaniu RAID.
* Szyfrowanie obrazów niestandardowych na maszynach wirtualnych z systemem Linux.
* Integracja z lokalnym systemem zarządzania kluczami.
* Usługa pliki systemu Azure (udostępnionego systemu plików).
* Sieciowy System plików (NFS).
* Woluminy dynamiczne.
* Kontenery systemu Windows Server, które tworzą woluminy dynamiczne dla każdego kontenera.
* Tymczasowe dyski systemu operacyjnego.
* Szyfrowanie współużytkowanych/rozproszonych systemów plików, takich jak (ale nie ograniczone do): System plików DFS, GFS, DRDB, CephFS itd.

## <a name="encryption-features"></a>Funkcje szyfrowania

Po włączeniu i wdrożeniu Azure Disk Encryption dla maszyn wirtualnych platformy Azure można skonfigurować następujące funkcje do włączenia:

* Szyfrowanie woluminu systemu operacyjnego w celu ochrony woluminu rozruchowego przechowywanego w magazynie.
* Szyfrowanie woluminów danych w celu ochrony woluminów danych przechowywanych w magazynie.
* Wyłączanie szyfrowania na dyskach systemu operacyjnego i danych dla maszyn wirtualnych z systemem Windows.
* Wyłączanie szyfrowania na dyskach danych dla maszyn wirtualnych z systemem Linux (tylko wtedy, gdy dysk systemu operacyjnego nie jest szyfrowany).
* Ochrona kluczy szyfrowania i wpisów tajnych w subskrypcji Azure Key Vault.
* Raportowanie stanu szyfrowania zaszyfrowanej maszyny wirtualnej.
* Usuwanie ustawień konfiguracji szyfrowania dysku z maszyny wirtualnej.
* Tworzenie kopii zapasowych i Przywracanie zaszyfrowanych maszyn wirtualnych przy użyciu usługi Azure Backup.

Azure Disk Encryption dla maszyn wirtualnych dla systemów Windows i Linux obejmują:

* [Rozszerzenie szyfrowania dysku dla systemu Windows](../virtual-machines/extensions/azure-disk-enc-windows.md).
* [Rozszerzenie szyfrowania dysku dla systemu Linux](../virtual-machines/extensions/azure-disk-enc-linux.md).
* [Polecenia cmdlet szyfrowania dysku programu PowerShell](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-2.2.0).
* [Polecenia cmdlet szyfrowania dysku interfejsu wiersza polecenia platformy Azure](/cli/azure/vm/encryption?view=azure-cli-latest).
* [Szablony szyfrowania dysków Azure Resource Manager](azure-security-disk-encryption-appendix.md#resource-manager-templates).

> [!NOTE]
> Nie ma dodatkowych opłat do szyfrowania dysków maszyn wirtualnych za pomocą usługi Azure Disk Encryption. Standardowa [cenach usługi Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) ma zastosowanie do magazynu kluczy, który służy do przechowywania kluczy szyfrowania. 

## <a name="encryption-workflow"></a>Szyfrowanie przepływu pracy

Aby włączyć szyfrowanie dysków dla Windows i maszyn wirtualnych systemu Linux, wykonaj następujące czynności:

1. Zgódź się na włączyć szyfrowanie dysku przy użyciu szablonu Menedżera zasobów szyfrowania dysków Azure, poleceń cmdlet programu PowerShell lub interfejsu wiersza polecenia platformy Azure, a następnie określ konfiguracji szyfrowania.

   * W scenariuszu wirtualnego dysku twardego szyfrowane klienta należy przekazać zaszyfrowanego dysku VHD do konta magazynu i materiału klucza szyfrowania do magazynu kluczy. Następnie podaj konfigurację szyfrowania, aby włączyć szyfrowanie na nowej maszynie wirtualnej.
   * W przypadku nowych maszyn wirtualnych utworzonych na podstawie obsługiwanych obrazów galerii i istniejących maszyn wirtualnych, które zostały już uruchomione na platformie Azure, podaj konfigurację szyfrowania, aby włączyć szyfrowanie na maszynie wirtualnej.

1. Przyznaj dostęp do platformy Azure w celu odczytania materiału klucza szyfrowania (klucze szyfrowania funkcji BitLocker dla systemów Windows i hasła dla systemu Linux) z magazynu kluczy, aby włączyć szyfrowanie na maszynie wirtualnej.

1. Azure aktualizuje modelu usługi maszyny Wirtualnej przy użyciu szyfrowania i Konfiguracja usługi key vault i konfiguruje zaszyfrowanej maszyny Wirtualnej.

   ![Ochrona przed złośliwym kodem zapewniana przez Microsoft na platformie Azure](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

## <a name="decryption-workflow"></a>Odszyfrowywanie przepływu pracy
Aby wyłączyć szyfrowanie dysków dla maszyn wirtualnych, wykonaj następujące czynności wysokiego poziomu:

1. Wybierz, aby wyłączyć szyfrowanie (odszyfrowywanie) na uruchomionej maszynie wirtualnej na platformie Azure i określić konfigurację odszyfrowywania. Można wyłączyć przy użyciu szablonu Menedżera zasobów szyfrowania dysków Azure, poleceń cmdlet programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

   Ten krok powoduje wyłączenie szyfrowania systemu operacyjnego lub woluminu danych albo zarówno z działającej maszyny wirtualnej z systemem Windows. Jak wspomniano w poprzedniej sekcji, wyłączenie szyfrowania dysku systemu operacyjnego dla systemu Linux nie jest obsługiwane. Krok odszyfrowywania jest dozwolona tylko w przypadku dysków z danymi na maszynach wirtualnych z systemem Linux, tak długo, jak dysk systemu operacyjnego nie jest zaszyfrowany.

1. Platforma Azure aktualizuje model usługi maszyny wirtualnej, a maszyna wirtualna jest oznaczona jako odszyfrowana. Zawartość maszyny Wirtualnej nie są szyfrowane, gdy.

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

   * W scenariuszu wirtualnego dysku twardego szyfrowane klienta należy przekazać zaszyfrowanego dysku VHD do konta magazynu i materiału klucza szyfrowania do magazynu kluczy. Następnie podaj konfigurację szyfrowania, aby włączyć szyfrowanie na nowej maszynie wirtualnej.
   * W przypadku nowych maszyn wirtualnych utworzonych na podstawie portalu Marketplace i istniejących maszyn wirtualnych, które zostały już uruchomione na platformie Azure, podaj konfigurację szyfrowania, aby włączyć szyfrowanie na maszynie wirtualnej.

1. Przyznaj dostęp do platformy Azure w celu odczytania materiału klucza szyfrowania (klucze szyfrowania funkcji BitLocker dla systemów Windows i hasła dla systemu Linux) z magazynu kluczy, aby włączyć szyfrowanie na maszynie wirtualnej.

1. Podaj tożsamość aplikacji usługi Azure AD, można zapisać klucz szyfrowania materiału do magazynu kluczy. Ten krok włącza szyfrowanie na maszynie wirtualnej w scenariuszach wymienionych w kroku 2.

1. Azure aktualizuje modelu usługi maszyny Wirtualnej przy użyciu szyfrowania i Konfiguracja usługi key vault i konfiguruje zaszyfrowanej maszyny Wirtualnej.


## <a name="terminology"></a>Terminologia
Poniższa tabela zawiera definicje typowych terminów używanych w dokumentacji usługi Azure Disk Encryption:

| Terminologia | Definicja |
| --- | --- |
| Azure AD | [Usługi Azure AD](https://azure.microsoft.com/documentation/services/active-directory/) konto jest używane do uwierzytelniania, przechowywania i pobierania wpisów tajnych w magazynie kluczy. |
| W usłudze Azure Key Vault | Key Vault to usługa kryptograficznych, key management, która opiera się na przetwarzaniu standardów FIPS (Federal Information) zweryfikowanych sprzętowych modułach zabezpieczeń. Standardy te pomagają chronić klucze kryptograficzne i wpisy tajne poufnych. Aby uzyskać więcej informacji, zobacz [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentacji. |
| BitLocker |[Funkcja BitLocker](https://technet.microsoft.com/library/hh831713.aspx) jest rozpoznawaną w branży technologią szyfrowania woluminów systemu Windows, która służy do włączania szyfrowania dysków na maszynach wirtualnych z systemem Windows. |
| KSB | Klucze szyfrowania funkcją BitLocker (klucz szyfrowania bloków) są używane do szyfrowania woluminu rozruchowego systemu operacyjnego i woluminów danych. BEKs są chronione w magazynie kluczy jako wpisy tajne. |
| Interfejs wiersza polecenia platformy Azure | [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) jest zoptymalizowany do zarządzania i administrowania zasobami platformy Azure z poziomu wiersza polecenia.|
| DM-Crypt |[Dm-crypt](https://gitlab.com/cryptsetup/cryptsetup/wikis/DMCrypt) to oparty na systemie Linux, przezroczysty podsystem szyfrowania dysków używany do włączania szyfrowania dysków na maszynach wirtualnych z systemem Linux. |
| Klucz szyfrowania klucza (KEK) | Klucz asymetryczny (RSA 2048), którego można użyć do ochrony lub zawijania klucza tajnego. Możesz podać sprzętowego modułu zabezpieczeń (HSM)-chronione klucza lub klucza chronionego przez oprogramowanie. Aby uzyskać więcej informacji, zobacz [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentacji. |
| Polecenia cmdlet programu PowerShell | Aby uzyskać więcej informacji, zobacz [poleceń cmdlet programu Azure PowerShell](/powershell/azure/overview). |

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć, zobacz [wymagania wstępne dotyczące Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md).

