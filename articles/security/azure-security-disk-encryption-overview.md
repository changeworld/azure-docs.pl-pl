---
title: Usługa Azure Disk Encryption dla maszyn wirtualnych IaaS w — omówienie | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie programu Microsoft Azure Disk Encryption dla maszyn wirtualnych IaaS.
services: security
documentationcenter: na
author: mestew
manager: MBaldwin
ms.assetid: 30324adb-e24c-433a-9214-2d1465cbdf45
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/30/2018
ms.author: mstewart
ms.openlocfilehash: 0e81a48c1215e8590f90c42aee0861e6fda3db8e
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2018
ms.locfileid: "39391851"
---
# <a name="azure-disk-encryption-for-iaas-vms"></a>Usługa Azure Disk Encryption dla maszyn wirtualnych IaaS 
Platforma Microsoft Azure jest zobowiązana do zapewnienia Twojej prywatności danych, niezależność danych, a umożliwiając kontroli platformy Azure hostowanej danych za pomocą wielu zaawansowanych technologii szyfrowania, kontroli i zarządzać kluczami szyfrowania, a kontrola i inspekcja dostępu do danych. Formant ten zapewnia klientom platformy Azure swobodę wyboru rozwiązania, który najlepiej zaspokaja ich potrzeby biznesowe. W tym artykule przedstawiono rozwiązanie technologiczne "Dysku szyfrowanie dla Windows i Linux IaaS maszyn wirtualnych platformy Azure", aby chronić i chronić dane zgodnie z wymaganiami co do bezpieczeństwa organizacji i zobowiązaniami w zakresie zgodności. 

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]


## <a name="overview"></a>Przegląd
Azure dysku Encryption (ADE) to funkcja umożliwiająca szyfrowanie dysków maszyn wirtualnych IaaS systemu Linux i Windows. ADE wykorzystuje będące standardami branżowymi [funkcji BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) funkcja systemu Windows i [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) funkcji systemu Linux, aby zapewnić szyfrowanie woluminów dysków systemu operacyjnego i danych. To rozwiązanie jest zintegrowana z usługą [usługi Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) ułatwiają kontrolowanie i zarządzaniu wpisami tajnymi i kluczami szyfrowania dysków. Rozwiązanie zapewnia również, że wszystkie dane na dyskach maszyn wirtualnych są szyfrowane, gdy w usłudze Azure storage.

Usługa Azure disk encryption for Windows i maszyn wirtualnych IaaS z systemem Linux jest **ogólnie** we wszystkich publicznych regionach platformy Azure i regiony AzureGov dla standardowych maszyn wirtualnych i maszyn wirtualnych dzięki usłudze premium storage. Po zastosowaniu rozwiązania usługi Azure Disk Encryption-management może spełnić następujące wymagania biznesowe:

* Maszyny wirtualne IaaS są chronione za pomocą standardowych w branży technologii zaspakajanie organizacyjnych bezpieczeństwa i zgodności z wymaganiami.
* Przeprowadzać ich inspekcje rozruchu maszyn wirtualnych IaaS, w obszarze klucze kontrolowane przez klienta i zasady, a ich użycie w magazynie kluczy.

> [!NOTE]
> Zastosowanie niektórych zaleceń zamieszczonych może zwiększyć danych, sieci lub użycia zasobów obliczeniowych, wynikiem dodatkowych kosztów licencji lub subskrypcji.


## <a name="encryption-scenarios"></a>Scenariuszy szyfrowania
Rozwiązanie Azure Disk Encryption obsługuje następujących scenariuszy:

* Włącza szyfrowanie na nowe Windows IaaS maszyny wirtualne utworzone na podstawie zaszyfrowane wstępnie wirtualnego dysku twardego i kluczy szyfrowania 
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
    * [Maszyny wirtualne systemu Linux](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) w tych warstwach, musi spełniać wymagania dotyczące minimalnej ilości pamięci, 7 GB
* Włącz szyfrowanie dla Windows i maszyn wirtualnych IaaS z systemem Linux i maszyn wirtualnych dysku zarządzanego przy użyciu obsługiwanych obrazów w galerii platformy Azure
* Wyłącz szyfrowanie dla dysków systemu operacyjnego i danych dla maszyn wirtualnych IaaS Windows i maszyn wirtualnych dysku zarządzanego
* Wyłącz szyfrowanie dla dysków z danymi dla maszyn wirtualnych IaaS z systemem Linux i maszyn wirtualnych dysku zarządzanego
* Włącz szyfrowanie dla maszyn wirtualnych IaaS z systemem operacyjnym Windows klienta
* Włącz szyfrowanie dla woluminów przy użyciu ścieżki instalacji
* Włącz szyfrowanie dla maszyn wirtualnych systemu Linux skonfigurowany przy użyciu dysku przy użyciu mdadm rozkładanie (RAID)
* Włącz szyfrowanie dla maszyn wirtualnych systemu Linux przy użyciu LVM dla dysków z danymi
* Włącz szyfrowanie dla systemu operacyjnego maszyny Wirtualnej systemu Linux i dysków z danymi 
* Włącz szyfrowanie dla Windows maszyny wirtualne skonfigurowane przy użyciu funkcji miejsca do magazynowania
* Aktualizowanie ustawień szyfrowania zaszyfrowanych istniejących w warstwie premium i innych niż premium magazyn maszyny Wirtualnej
* Kopia zapasowa i przywracanie zaszyfrowanych maszyn wirtualnych, zarówno dla klucza KEK nie i scenariuszy KEK (KEK - klucz szyfrowania klucza)
* Wszystkie publiczne platformy Azure i AzureGov regiony są obsługiwane

Rozwiązanie nie obsługuje następujących scenariuszy, funkcji i technologii:

* Warstwa podstawowa maszyn wirtualnych IaaS
* Wyłączenie szyfrowania na dysku systemu operacyjnego dla maszyn wirtualnych IaaS z systemem Linux
* Wyłączenie szyfrowania na dysku danych, jeśli dysk systemu operacyjnego został zaszyfrowany dla maszyn wirtualnych Iaas z systemem Linux
* Maszyny wirtualne IaaS, które są tworzone za pomocą klasycznego metodę tworzenia maszyny Wirtualnej
* Włącz szyfrowanie dla systemu Linux maszyn wirtualnych IaaS obrazów niestandardowych klienta nie jest obsługiwana.
* Integracja z usługą zarządzania kluczami w środowisku lokalnym
* Usługa pliki systemu Azure (udostępnionego systemu plików), Network File System (NFS), dynamiczne woluminy i maszyn wirtualnych Windows, które są skonfigurowane przy użyciu systemów programowej macierzy RAID

## <a name="encryption-features"></a>Funkcje szyfrowania
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

* Rozszerzenie szyfrowania dysku dla Windows.
* Rozszerzenie szyfrowania dysku dla systemu Linux.
* Szyfrowanie dysków, poleceń cmdlet programu PowerShell.
* Szyfrowanie dysków apletów poleceń interfejsu wiersza polecenia platformy Azure (CLI).
* Szablony usługi Azure Resource Manager szyfrowania dysku.

Rozwiązanie Azure Disk Encryption jest obsługiwana na maszynach wirtualnych IaaS z uruchomionym systemem Windows lub systemu operacyjnego Linux. Aby uzyskać więcej informacji na temat obsługiwanych systemów operacyjnych, zobacz [wymagania wstępne](azure-security-disk-encryption-prerequisites.md) artykułu.

> [!NOTE]
> Nie ma dodatkowych opłat do szyfrowania dysków maszyn wirtualnych za pomocą usługi Azure Disk Encryption. Standardowa [cenach usługi Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) ma zastosowanie do magazynu kluczy, używany do przechowywania kluczy szyfrowania. 

## <a name="encryption-workflow"></a>Szyfrowanie przepływu pracy
Aby włączyć szyfrowanie dysków dla Windows i maszyn wirtualnych systemu Linux, wykonaj następujące czynności:

1. Wybierz scenariusz szyfrowania spośród powyższych scenariuszy szyfrowania.
2. Zgódź się na włączenie szyfrowania przy użyciu szablonu Menedżera zasobów szyfrowania dysków Azure, poleceń cmdlet programu PowerShell lub interfejsu wiersza polecenia na dysku, a następnie określ konfiguracji szyfrowania.

   * W scenariuszu wirtualnego dysku twardego szyfrowane klienta należy przekazać zaszyfrowanego dysku VHD do konta magazynu i materiału klucza szyfrowania do magazynu kluczy. Następnie podaj konfiguracji szyfrowania, aby włączyć szyfrowanie na utworzonej nowej maszynie Wirtualnej IaaS.
   * W przypadku nowych maszyn wirtualnych, które są tworzone w portalu Marketplace i istniejących maszyn wirtualnych, które zostały już uruchomione na platformie Azure Podaj konfiguracji szyfrowania, aby włączyć szyfrowanie na maszynie Wirtualnej IaaS.

3. Udzielanie dostępu do platformy Azure, aby odczytać materiału klucza szyfrowania (klucze szyfrowania funkcją BitLocker dla systemów Windows) i hasło dla systemu Linux z własnego magazynu kluczy, aby włączyć szyfrowanie na maszynie Wirtualnej IaaS.

4. Podaj tożsamość aplikacji usługi Azure Active Directory (Azure AD), aby zapisać klucz szyfrowania materiału do magazynu kluczy. Ten krok umożliwia szyfrowanie na maszynie Wirtualnej IaaS w scenariuszach wymienionych w kroku 2.

5. Azure aktualizuje modelu usługi maszyny Wirtualnej przy użyciu szyfrowania i Konfiguracja usługi key vault i konfiguruje zaszyfrowanej maszyny Wirtualnej.

 ![Ochrona przed złośliwym kodem zapewniana przez Microsoft na platformie Azure](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

## <a name="decryption-workflow"></a>Odszyfrowywanie przepływu pracy
Aby wyłączyć szyfrowanie dysków dla maszyn wirtualnych IaaS, wykonaj następujące kroki ogólne:

1. Wybierz umożliwia wyłączenie szyfrowania (odszyfrowywania) na uruchomionej maszynie Wirtualnej IaaS na platformie Azure i określ żądaną konfigurację odszyfrowywania. Można wyłączyć przy użyciu szablonu Menedżera zasobów szyfrowania dysków Azure, poleceń cmdlet programu PowerShell lub wiersza polecenia platformy Azure.

 Ten krok powoduje wyłączenie szyfrowania systemu operacyjnego lub ilości danych lub obu tych programów na uruchomionej maszynie Wirtualnej IaaS Windows. Jednakże jak wspomniano w poprzedniej sekcji, wyłączenie szyfrowania dysku systemu operacyjnego dla systemu Linux nie jest obsługiwana. Krok odszyfrowywanie jest dozwolony tylko dla dysków z danymi na maszynach wirtualnych z systemem Linux, tak długo, jak dysk systemu operacyjnego nie jest zaszyfrowany.
2. Azure aktualizuje model usług maszyn wirtualnych i maszyn wirtualnych IaaS jest oznaczony odszyfrowany. Zawartość maszyny Wirtualnej nie są szyfrowane, gdy.

> [!NOTE]
> Operacja disable szyfrowania nie powoduje usunięcia magazynu kluczy i materiału klucza szyfrowania (klucze szyfrowania funkcją BitLocker dla systemów Windows) lub hasło dla systemu Linux.
 > Wyłączenie szyfrowania dysku systemu operacyjnego dla systemu Linux nie jest obsługiwane. Krok odszyfrowywania jest dozwolona tylko w przypadku dysków z danymi na maszynach wirtualnych z systemem Linux.
Wyłączenie szyfrowania dysku danych dla systemu Linux nie jest obsługiwana, jeśli dysk systemu operacyjnego został zaszyfrowany.

## <a name="terminology"></a>Terminologia
Aby poznać niektóre typowe terminy używane przez tę technologię, skorzystaj z poniższej tabeli terminologii:

| Terminologia | Definicja |
| --- | --- |
| Azure AD | Usługa Azure AD jest [usługi Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/). Konto usługi Azure AD jest używany do uwierzytelniania, przechowywania i pobierania wpisów tajnych w magazynie kluczy. |
| W usłudze Azure Key Vault | Key Vault to usługa kryptograficznych, key management, która jest oparta na przetwarzanie standardów FIPS (Federal Information) zweryfikowane sprzętowych modułów zabezpieczeń, które zapewni ochronę klucze kryptograficzne i wpisy tajne poufnych. Aby uzyskać więcej informacji, zobacz [usługi Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentacji. |
| BitLocker |[Funkcja BitLocker](https://technet.microsoft.com/library/hh831713.aspx) to branży Windows woluminu szyfrowania technologia, która służy do włączania szyfrowania dysków na maszynach wirtualnych IaaS Windows. |
| KSB | Klucze szyfrowania funkcją BitLocker są używane do szyfrowania woluminu rozruchowego systemu operacyjnego i woluminów danych. Klucze funkcji BitLocker są chronione w magazynie kluczy jako wpisy tajne. |
| Interfejs wiersza polecenia | Zobacz [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).|
| DM-Crypt |[DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) jest podsystem opartych na systemie Linux lub przezroczystego szyfrowania dysku, który służy do włączania szyfrowania dysków na maszynach wirtualnych IaaS z systemem Linux. |
| KEK | Klucz szyfrowania klucza to klucz asymetryczny (RSA 2048), który służy do ochrony lub zawijania wpisu tajnego. Możesz podać sprzętowego modułu zabezpieczeń (HSM)-chronione klucza lub klucza chronionego przez oprogramowanie. Aby uzyskać więcej informacji, zobacz [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentacji. |
| PS poleceń cmdlet | Zobacz [poleceń cmdlet programu Azure PowerShell](/powershell/azure/overview). |

## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"]
> [Wymagań wstępnych szyfrowania dysków Azure](azure-security-disk-encryption-prerequisites.md)
