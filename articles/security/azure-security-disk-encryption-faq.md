---
title: Usługa Azure Disk Encryption — często zadawane pytania | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące programu Microsoft Azure dysku szyfrowanie dla Windows i maszyn wirtualnych IaaS z systemem Linux.
services: security
documentationcenter: na
author: mestew
manager: MBaldwin
ms.assetid: 7188da52-5540-421d-bf45-d124dee74979
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/30/2018
ms.author: mstewart
ms.openlocfilehash: 98b8883a5ab0096102ab7daf90b5b2791a6f7e41
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2018
ms.locfileid: "39389579"
---
# <a name="azure-disk-encryption-faq"></a>Usługa Azure Disk Encryption — często zadawane pytania

Ten artykuł zawiera odpowiedzi na często zadawane pytania (FAQ) dotyczących platformy Azure szyfrowania dysku dla Windows i maszyn wirtualnych IaaS z systemem Linux. Aby uzyskać więcej informacji na temat tej usługi, zobacz [Azure szyfrowania dysku dla Windows i maszyn wirtualnych IaaS z systemem Linux](azure-security-disk-encryption-overview.md).

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Gdzie jest usługa Azure Disk Encryption ogólnie dostępna (GA)?

Szyfrowania dysku dla Windows i maszyn wirtualnych systemu Linux IaaS Azure jest ogólnie dostępna we wszystkich publicznych regionach platformy Azure.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Jakie podejrzewać są dostępne za pomocą usługi Azure Disk Encryption?

Azure GA szyfrowania dysku obsługuje szablony usługi Azure Resource Manager, programu Azure PowerShell i wiersza polecenia platformy Azure. Wiele środowisk użytkownika zapewniają elastyczność. Masz trzy różne opcje dotyczące włączania szyfrowania dysku dla maszyn wirtualnych IaaS. Aby uzyskać więcej informacji na temat środowiska użytkownika i dostępne w usłudze Azure Disk Encryption wskazówki krok po kroku, zobacz [Włącz Azure dysku Encryption for Windows](azure-security-disk-encryption-windows.md) i [włączyć usługi Azure Disk Encryption dla systemu Linux](azure-security-disk-encryption-linux.md).

## <a name="how-much-does-azure-disk-encryption-cost"></a>Ile kosztuje usługa Azure Disk Encryption

Nie ma opłat do szyfrowania dysków maszyn wirtualnych za pomocą usługi Azure Disk Encryption, ale są naliczane opłaty związane z użyciem usługi Azure Key Vault. Aby uzyskać więcej informacji na temat kosztów usługi Azure Key Vault, zobacz [cenach usługi Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) strony.


## <a name="which-virtual-machine-tiers-does-azure-disk-encryption-support"></a>Warstwy maszyny wirtualnej, który obsługuje usługi Azure Disk Encryption?

Usługa Azure Disk Encryption jest dostępna na maszynach wirtualnych w warstwie standardowa, w tym [A, D, DS, G, GS i F](https://azure.microsoft.com/pricing/details/virtual-machines/) serię maszyn wirtualnych IaaS. Jest również dostępna dla maszyn wirtualnych dzięki usłudze premium storage. Nie jest dostępna na maszynach wirtualnych w warstwie podstawowa.

## <a name="bkmk_LinuxOSSupport"></a> Jakie są dystrybucje systemu Linux obsługuje usługi Azure Disk Encryption?

Usługa Azure Disk Encryption jest obsługiwana na poniższe dystrybucje serwera systemu Linux i wersji:

| Dystrybucja systemu Linux | Wersja | Typ woluminu obsługiwany w przypadku szyfrowania|
| --- | --- |--- |
| Ubuntu | 16.04-DAILY-LTS | Dysk systemu operacyjnego i danych |
| Ubuntu | 14.04.5-DAILY-LTS | Dysk systemu operacyjnego i danych |
| RHEL | 7.5 | Dysk danych * |
| RHEL | 7.4 | Dysk danych * |
| RHEL | 7.3 | Dysk danych * |
| RHEL | 7.2 | Dysk danych * |
| RHEL | 6.8 | Dysk danych * |
| RHEL | 6.7 | Dysk danych * |
| CentOS | 7.4 | Dysk systemu operacyjnego i danych |
| CentOS | 7.3 | Dysk systemu operacyjnego i danych |
| CentOS | 7.2n | Dysk systemu operacyjnego i danych |
| CentOS | 6.8 | Dysk systemu operacyjnego i danych |
| CentOS | 7.1 | Dysk z danymi |
| CentOS | 7.0 | Dysk z danymi |
| CentOS | 6.7 | Dysk z danymi |
| CentOS | 6.6 | Dysk z danymi |
| CentOS | 6.5 | Dysk z danymi |
| openSUSE | 13.2 | Dysk z danymi |
| SLES | 12 SP1 | Dysk z danymi |
| SLES | Priorytet: 12-z dodatkiem SP1 | Dysk z danymi |
| SLES | HPC 12 | Dysk z danymi |
| SLES | Priorytet: 11 — z dodatkiem SP4 | Dysk z danymi |
| SLES | 11 SP4 | Dysk z danymi |


*__ADE obsługę systemu RHEL dysk z danymi. Bieżąca implementacja parametru ADE działa dla dysku systemu operacyjnego, ale nie jest aktualnie wspólnie obsługiwane. Firma Microsoft i Red Hat nad wspólnie obsługiwanego rozwiązania. W międzyczasie możesz odwoływać się do [usługi Azure Disk Encryption dla systemu Linux](azure-security-disk-encryption-linux.md) artykułu.__

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Jak rozpocząć, za pomocą usługi Azure Disk Encryption?

Aby rozpocząć pracę, przeczytaj [Omówienie usługi Azure Disk Encryption](azure-security-disk-encryption-overview.md).

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Czy można zaszyfrować woluminów rozruchowych i danych przy użyciu usługi Azure Disk Encryption?

Tak, można zaszyfrować woluminy rozruchowe i dane dla Windows i maszyn wirtualnych IaaS z systemem Linux. W przypadku maszyn wirtualnych Windows nie można zaszyfrować dane bez pierwszy szyfrowania woluminu systemu operacyjnego. W przypadku maszyn wirtualnych systemu Linux jest umożliwia szyfrowanie ilości danych bez konieczności najpierw szyfrowania woluminu systemu operacyjnego. Po wolumin systemu operacyjnego został zaszyfrowany dla systemu Linux, wyłączenie szyfrowania na woluminie systemu operacyjnego dla maszyn wirtualnych IaaS z systemem Linux nie jest obsługiwane.

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok-capability"></a>Usługa Azure Disk Encryption zezwala pozwala na używanie własnego klucza (BYOK) możliwości?

Tak, możesz podać własne klucze szyfrowania. Te klucze są chronione w usłudze Azure Key Vault, czyli magazynu kluczy dla usługi Azure Disk Encryption. Aby uzyskać więcej informacji na temat kluczy szyfrowania klucza obsługi scenariuszy, zobacz [wymagania wstępne dotyczące usługi Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md).

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>Czy można używać klucza szyfrowania klucza utworzone przez platformę Azure?

Tak, można użyć usługi Azure Key Vault, aby wygenerować klucz szyfrowania klucza do użycia szyfrowania dysków Azure. Te klucze są chronione w usłudze Azure Key Vault, czyli magazynu kluczy dla usługi Azure Disk Encryption. Aby uzyskać więcej informacji na temat klucz szyfrowania klucza, zobacz [wymagania wstępne dotyczące usługi Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md).

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>Czy można użyć Usługa zarządzania kluczami w środowisku lokalnym lub przez moduł HSM do ochrony kluczy szyfrowania?

Usługa zarządzania kluczami w środowisku lokalnym lub modułu HSM nie można użyć do ochrony kluczy szyfrowania za pomocą usługi Azure Disk Encryption. Usługa Azure Key Vault służy tylko do ochrony kluczy szyfrowania. Aby uzyskać więcej informacji na temat scenariuszy, Obsługa kluczy szyfrowania, zobacz [wymagania wstępne dotyczące usługi Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>Jakie są wymagania wstępne dotyczące konfigurowania usługi Azure Disk Encryption?

Istnieją wymagania wstępne dotyczące usługi Azure Disk Encryption. Zobacz [wymagania wstępne dotyczące usługi Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) artykuł, aby utworzyć aplikację usługi Azure Active Directory, Utwórz nowy magazyn kluczy, lub skonfigurować istniejącego magazynu kluczy, aby uzyskać dostęp do szyfrowania dysku włączyć szyfrowanie i ochrony kluczy tajnych i klucze. Aby uzyskać więcej informacji na temat scenariuszy, Obsługa kluczy szyfrowania, zobacz [Omówienie usługi Azure Disk Encryption](azure-security-disk-encryption-overview.md).

## <a name="where-can-i-get-more-information-on-how-to-use-powershell-for-configuring-azure-disk-encryption"></a>Gdzie można uzyskać więcej informacji na temat konfigurowania usługi Azure Disk Encryption przy użyciu programu PowerShell?

Istnieją pewne wspaniałe artykuły na sposób wykonywania podstawowych zadań usługi Azure Disk Encryption, a także bardziej zaawansowanych scenariuszy. Aby uzyskać podstawowe zadania, zobacz [Eksplorowanie usługi Azure Disk Encryption przy użyciu programu Azure PowerShell — część 1](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/16/explore-azure-disk-encryption-with-azure-powershell/). Aby uzyskać bardziej zaawansowanych scenariuszy, zobacz [Eksplorowanie usługi Azure Disk Encryption przy użyciu programu Azure PowerShell — część 2](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2/).

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Jakiej wersji programu Azure PowerShell obsługuje usługi Azure Disk Encryption?

Najnowszą wersję zestawu SDK programu Azure PowerShell umożliwia skonfigurowanie usługi Azure Disk Encryption. Pobierz najnowszą wersję [programu Azure PowerShell](https://github.com/Azure/azure-powershell/releases). Usługa Azure Disk Encryption jest *nie* obsługiwany przez zestaw SDK platformy Azure w wersji 1.1.0.

> [!NOTE]
> Rozszerzenia w wersji zapoznawczej szyfrowania dysków Azure dla systemu Linux jest przestarzały. Aby uzyskać więcej informacji, zobacz [rozszerzenie Azure wycofano dysku encryption (wersja zapoznawcza) dla maszyn wirtualnych IaaS z systemem Linux](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/12/deprecating-azure-disk-encryption-preview-extension-for-linux-iaas-vms/).

## <a name="can-i-apply-azure-disk-encryption-on-my-custom-linux-image"></a>Czy mogę zastosować szyfrowania dysków Azure na mój niestandardowych obrazów systemu Linux?

Nie można zastosować usługi Azure Disk Encryption z niestandardowych obrazów systemu Linux. Obsługiwane są tylko galerii obrazów systemu Linux dla obsługiwanych dystrybucjach przywołane wcześniej. Niestandardowych obrazów systemu Linux nie są obecnie obsługiwane.

## <a name="can-i-apply-updates-to-a-linux-red-hat-vm-that-uses-the-yum-update"></a>Aktualizacje można stosować do Red Hat Maszynę wirtualną systemu Linux używającej aktualizacji yum?

Tak, możesz przeprowadzić aktualizację lub stosowanie poprawek do maszyny Wirtualnej z Red Hat Linux. Aby uzyskać więcej informacji, zobacz [zastosowanie aktualizacji do zaszyfrowanej IaaS Red Hat Maszynie wirtualnej platformy Azure przy użyciu narzędzia yum aktualizacji](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/13/applying-updates-to-a-encrypted-azure-iaas-red-hat-vm-using-yum-update/).

## <a name="what-is-the-recommended-azure-disk-encryption-workflow-for-linux"></a>Co to jest przepływ pracy szyfrowania dysków Azure zalecane dla systemu Linux?

Aby uzyskać najlepsze wyniki w systemie Linux, zaleca się poniższy przepływ pracy:
* Zacznij od obrazu niezmodyfikowanego galerii podstawowe odpowiadający żądaną dystrybucja systemu operacyjnego i wersji
* Utwórz kopię zapasową zainstalowanych dyskach, które będą szyfrowane.  Pozwala to na odzyskiwanie w przypadku awarii, na przykład jeśli maszyna wirtualna jest ponownie uruchomiony przed ukończeniem szyfrowania.
* Szyfrowanie (może potrwać kilka godzin, a nawet przez kilka dni w zależności od właściwości maszyny wirtualnej i rozmiar wszelkich dołączonych dysków danych)
* Dostosowywanie i dodanie oprogramowania do obrazu zgodnie z potrzebami.

Jeśli ten przepływ pracy nie jest możliwe, opierając się na [szyfrowanie usługi Storage](../storage/common/storage-service-encryption.md) (SSE) magazynowania platformy warstwy konta może być alternatywa pełne szyfrowanie dysków za pomocą dm-crypt.

## <a name="what-is-the-disk-bek-volume-or-mntazurebekdisk"></a>Co to jest dysk "Klucz szyfrowania bloków woluminu" lub "/ mnt/azure_bek_disk"?

"Klucz szyfrowania bloków woluminu" Windows lub "/ mnt/azure_bek_disk" dla systemu Linux jest woluminem danych lokalnych, która bezpiecznie klucze szyfrowania są przechowywane zaszyfrowane IaaS maszyn wirtualnych platformy Azure.
> [!NOTE]
> Nie należy usuwać ani edytować zawartość tego dysku. Odinstalowuje dysku, ponieważ obecności klucza szyfrowania jest wymagane dla wszystkich operacji szyfrowania na maszynie Wirtualnej IaaS.

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Gdzie można zadawać pytania lub opinię?

Możesz zadać pytania lub Wyraź swoją opinię [forum usługi Azure Disk Encryption](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption).

## <a name="next-steps"></a>Kolejne kroki
W tym dokumencie przedstawiono więcej informacji na temat najczęściej zadawane pytania związane z usługi Azure Disk Encryption. Aby uzyskać więcej informacji na temat tej usługi i jego możliwości zobacz następujące artykuły:

- [Zastosuj szyfrowanie dysków w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Szyfrowanie maszyny wirtualnej z platformy Azure](https://docs.microsoft.com/azure/security-center/security-center-disk-encryption)
- [Funkcja szyfrowania nieaktywnych danych platformy Azure](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)
