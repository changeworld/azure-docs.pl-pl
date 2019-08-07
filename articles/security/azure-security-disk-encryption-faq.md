---
title: Często zadawane pytania — usługa Azure Disk Encryption dla maszyn wirtualnych IaaS | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące programu Microsoft Azure dysku szyfrowanie dla Windows i maszyn wirtualnych IaaS z systemem Linux.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 06/05/2019
ms.custom: seodec18
ms.openlocfilehash: 4f2a34e63a870814c8d2a3ffe24c60083c9d7bb2
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/05/2019
ms.locfileid: "68781103"
---
# <a name="azure-disk-encryption-for-iaas-vms-faq"></a>Azure Disk Encryption dla maszyn wirtualnych IaaS — często zadawane pytania

Ten artykuł zawiera odpowiedzi na często zadawane pytania (FAQ) dotyczących platformy Azure szyfrowania dysku dla Windows i maszyn wirtualnych IaaS z systemem Linux. Aby uzyskać więcej informacji na temat tej usługi, zobacz [Azure szyfrowania dysku dla Windows i maszyn wirtualnych IaaS z systemem Linux](azure-security-disk-encryption-overview.md).

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Gdzie jest usługa Azure Disk Encryption ogólnie dostępna (GA)?

Szyfrowania dysku dla Windows i maszyn wirtualnych systemu Linux IaaS Azure jest ogólnie dostępna we wszystkich publicznych regionach platformy Azure.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Jakie podejrzewać są dostępne za pomocą usługi Azure Disk Encryption?

Azure GA szyfrowania dysku obsługuje szablony usługi Azure Resource Manager, programu Azure PowerShell i wiersza polecenia platformy Azure. Procesy, przez innego użytkownika zapewniają elastyczność. Masz trzy różne opcje dotyczące włączania szyfrowania dysku dla maszyn wirtualnych IaaS. Aby uzyskać więcej informacji na temat środowiska użytkownika i dostępne w usłudze Azure Disk Encryption wskazówki krok po kroku, zobacz [Włącz Azure dysku Encryption for Windows](azure-security-disk-encryption-windows.md) i [włączyć usługi Azure Disk Encryption dla systemu Linux](azure-security-disk-encryption-linux.md).

## <a name="how-much-does-azure-disk-encryption-cost"></a>Ile kosztuje usługa Azure Disk Encryption

Nie ma opłat do szyfrowania dysków maszyn wirtualnych za pomocą usługi Azure Disk Encryption, ale są naliczane opłaty związane z użyciem usługi Azure Key Vault. Aby uzyskać więcej informacji na temat kosztów usługi Azure Key Vault, zobacz [cenach usługi Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) strony.

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Jak rozpocząć, za pomocą usługi Azure Disk Encryption?

Aby rozpocząć pracę, przeczytaj [Omówienie usługi Azure Disk Encryption](azure-security-disk-encryption-overview.md).

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>Jakie rozmiary maszyn wirtualnych i systemów operacyjnych Azure Disk Encryption obsługiwać?

W artykule dotyczącym [wymagań wstępnych Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) wymieniono [rozmiary maszyn wirtualnych](azure-security-disk-encryption-prerequisites.md#supported-vm-sizes) i [systemy operacyjne maszyn wirtualnych](azure-security-disk-encryption-prerequisites.md#supported-operating-systems) obsługujące Azure Disk Encryption.

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Czy można zaszyfrować woluminów rozruchowych i danych przy użyciu usługi Azure Disk Encryption?

Tak, można zaszyfrować woluminy rozruchowe i dane dla Windows i maszyn wirtualnych IaaS z systemem Linux. W przypadku maszyn wirtualnych Windows nie można zaszyfrować dane bez pierwszy szyfrowania woluminu systemu operacyjnego. W przypadku maszyn wirtualnych systemu Linux jest umożliwia szyfrowanie ilości danych bez konieczności najpierw szyfrowania woluminu systemu operacyjnego. Po wolumin systemu operacyjnego został zaszyfrowany dla systemu Linux, wyłączenie szyfrowania na woluminie systemu operacyjnego dla maszyn wirtualnych IaaS z systemem Linux nie jest obsługiwany. W przypadku maszyn wirtualnych z systemem Linux w zestawie skalowania można zaszyfrować tylko wolumin danych.

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>Czy mogę zaszyfrować wolumin odinstalowany z Azure Disk Encryption?

Nie, Azure Disk Encryption szyfruje tylko woluminy zainstalowane.

## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>Jak mogę obrócić wpisy tajne lub klucze szyfrowania?

Aby obrócić wpisy tajne, po prostu wywołaj to samo polecenie, które zostało użyte wcześniej do włączenia szyfrowania dysku, określając inny Key Vault. Aby obrócić klucz szyfrowania klucza, wywołaj to samo polecenie, które zostało użyte pierwotnie, aby włączyć szyfrowanie dysków, określając nowe szyfrowanie klucza. 

>[!WARNING]
> - Jeśli wcześniej była używana [Azure Disk Encryption z aplikacją usługi Azure AD](azure-security-disk-encryption-prerequisites-aad.md) przez określenie poświadczeń usługi Azure AD do zaszyfrowania tej maszyny wirtualnej, należy użyć tej opcji w celu ZASZYFROWANIA maszyny wirtualnej. Nie można użyć [Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) dla tej zaszyfrowanej maszyny wirtualnej, ponieważ nie jest to obsługiwany scenariusz, co oznacza, że przełączanie z aplikacji usługi AAD dla tej zaszyfrowanej maszyny wirtualnej nie jest jeszcze obsługiwane.

## <a name="how-do-i-add-or-remove-a-key-encryption-key-if-i-didnt-originally-use-one"></a>Jak mogę dodać lub usunąć klucz szyfrowania klucza, jeśli nie był pierwotnie używany?

Aby dodać klucz szyfrowania klucza, wywołaj polecenie Włącz ponownie, przekazując klucz szyfrowania klucza. Aby usunąć klucz szyfrowania klucza, wywołaj polecenie Włącz ponownie bez parametru klucza szyfrowania klucza.

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok"></a>Czy usługa Azure Disk Encryption umożliwia używanie własnego klucza (BYOK)?

Tak, możesz podać własne klucze szyfrowania. Te klucze są chronione w usłudze Azure Key Vault, czyli magazynu kluczy dla usługi Azure Disk Encryption. Aby uzyskać więcej informacji na temat kluczy szyfrowania klucza obsługi scenariuszy, zobacz [wymagania wstępne dotyczące usługi Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md).

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>Czy można używać klucza szyfrowania klucza utworzone przez platformę Azure?

Tak, można użyć usługi Azure Key Vault, aby wygenerować klucz szyfrowania klucza do użycia szyfrowania dysków Azure. Te klucze są chronione w usłudze Azure Key Vault, czyli magazynu kluczy dla usługi Azure Disk Encryption. Aby uzyskać więcej informacji na temat klucz szyfrowania klucza, zobacz [wymagania wstępne dotyczące usługi Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md).

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>Czy można użyć Usługa zarządzania kluczami w środowisku lokalnym lub przez moduł HSM do ochrony kluczy szyfrowania?

Usługa zarządzania kluczami w środowisku lokalnym lub modułu HSM nie można użyć do ochrony kluczy szyfrowania za pomocą usługi Azure Disk Encryption. Usługa Azure Key Vault służy tylko do ochrony kluczy szyfrowania. Aby uzyskać więcej informacji na temat scenariuszy, Obsługa kluczy szyfrowania, zobacz [wymagania wstępne dotyczące usługi Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>Jakie są wymagania wstępne dotyczące konfigurowania usługi Azure Disk Encryption?

Istnieją wymagania wstępne dotyczące usługi Azure Disk Encryption. Zobacz [wymagania wstępne dotyczące usługi Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) artykuł, aby utworzyć nowy magazyn kluczy, lub skonfiguruj istniejącego magazynu kluczy, aby uzyskać dostęp do szyfrowania dysku włączyć szyfrowanie i ochrona kluczy tajnych i kluczy. Aby uzyskać więcej informacji na temat scenariuszy, Obsługa kluczy szyfrowania, zobacz [Omówienie usługi Azure Disk Encryption](azure-security-disk-encryption-overview.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption-with-an-azure-ad-app-previous-release"></a>Jakie są wymagania wstępne dotyczące konfigurowania usługi Azure Disk Encryption przy użyciu aplikacji usługi Azure AD (poprzedniej wersji)?

Istnieją wymagania wstępne dotyczące usługi Azure Disk Encryption. Zobacz [wymagania wstępne dotyczące usługi Azure Disk Encryption](azure-security-disk-encryption-prerequisites-aad.md) artykuł, aby utworzyć aplikację usługi Azure Active Directory, Utwórz nowy magazyn kluczy, lub skonfigurować istniejącego magazynu kluczy, aby uzyskać dostęp do szyfrowania dysku włączyć szyfrowanie i ochrony kluczy tajnych i klucze. Aby uzyskać więcej informacji na temat scenariuszy, Obsługa kluczy szyfrowania, zobacz [Omówienie usługi Azure Disk Encryption](azure-security-disk-encryption-overview.md).

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>Usługa Azure Disk Encryption korzysta aplikacja usługi Azure AD (poprzedniej wersji), w dalszym ciągu obsługiwany?
Tak. Szyfrowanie dysków za pomocą aplikacji usługi Azure AD jest nadal obsługiwane. Jednak podczas szyfrowania nowych maszyn wirtualnych zaleca się, że używasz nowej metody, a nie za pomocą aplikacji usługi Azure AD. 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>Czy można migrować maszyny wirtualne, które zostały zaszyfrowane za pomocą aplikacji usługi Azure AD, do szyfrowania bez aplikacji usługi Azure AD?
  Obecnie nie ma ścieżki migracji bezpośrednie dla maszyn, które zostały zaszyfrowane za pomocą aplikacji usługi Azure AD, do szyfrowania bez aplikacji usługi Azure AD. Ponadto nie ma bezpośrednią ścieżkę z szyfrowania bez aplikacji usługi Azure AD do szyfrowania za pomocą aplikacji usługi AD. 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Jakiej wersji programu Azure PowerShell obsługuje usługi Azure Disk Encryption?

Najnowszą wersję zestawu SDK programu Azure PowerShell umożliwia skonfigurowanie usługi Azure Disk Encryption. Pobierz najnowszą wersję [programu Azure PowerShell](https://github.com/Azure/azure-powershell/releases). Usługa Azure Disk Encryption jest *nie* obsługiwany przez zestaw SDK platformy Azure w wersji 1.1.0.

> [!NOTE]
> Rozszerzenie "Microsoft. OSTCExtension. AzureDiskEncryptionForLinux" systemu Linux Azure Disk Encryption Preview jest przestarzałe. To rozszerzenie zostało opublikowane na potrzeby wersji zapoznawczej usługi Azure Disk Encryption. Nie należy używać wersji zapoznawczej rozszerzenia w testowaniu lub wdrożeniu produkcyjnym.

> W przypadku scenariuszy wdrażania, takich jak Azure Resource Manager (ARM), w przypadku których istnieje potrzeba wdrożenia rozszerzenia Azure Disk Encryption dla maszyny wirtualnej z systemem Linux, aby włączyć szyfrowanie na maszynie wirtualnej z systemem Linux IaaS, należy użyć rozszerzenia obsługiwanego w środowisku produkcyjnym usługi Azure Disk Encryption. Microsoft. Azure. Security. AzureDiskEncryptionForLinux ".

## <a name="can-i-apply-azure-disk-encryption-on-my-custom-linux-image"></a>Czy mogę zastosować szyfrowania dysków Azure na mój niestandardowych obrazów systemu Linux?

Nie można zastosować usługi Azure Disk Encryption z niestandardowych obrazów systemu Linux. Obsługiwane są tylko galerii obrazów systemu Linux dla obsługiwanych dystrybucjach przywołane wcześniej. Niestandardowych obrazów systemu Linux nie są obecnie obsługiwane.

## <a name="can-i-apply-updates-to-a-linux-red-hat-vm-that-uses-the-yum-update"></a>Aktualizacje można stosować do Red Hat Maszynę wirtualną systemu Linux używającej aktualizacji yum?

Tak, możesz wykonać aktualizację yum na maszynie wirtualnej z systemem Red Hat Linux.  Aby uzyskać więcej informacji, zobacz [Zarządzanie pakietami systemu Linux za zaporą](azure-security-disk-encryption-tsg.md#linux-package-management-behind-a-firewall).

## <a name="what-is-the-recommended-azure-disk-encryption-workflow-for-linux"></a>Co to jest przepływ pracy szyfrowania dysków Azure zalecane dla systemu Linux?

Aby uzyskać najlepsze wyniki w systemie Linux, zaleca się poniższy przepływ pracy:
* Zacznij od obrazu niezmodyfikowanego galerii podstawowe odpowiadający potrzebne dystrybucja systemu operacyjnego i wersji
* Utwórz kopię zapasową zainstalowanych dyskach, które będą szyfrowane.  Ponownie nawet umożliwia to odzyskiwanie w przypadku awarii, na przykład jeśli maszyna wirtualna jest ponownie uruchomiony przed ukończeniem szyfrowania.
* Szyfrowanie (może potrwać kilka godzin, a nawet przez kilka dni w zależności od właściwości maszyny Wirtualnej i rozmiar wszelkich dołączonych dysków danych)
* Dostosowywanie i dodanie oprogramowania do obrazu zgodnie z potrzebami.

Jeśli ten przepływ pracy nie jest możliwe, opierając się na [szyfrowanie usługi Storage](../storage/common/storage-service-encryption.md) (SSE) magazynowania platformy warstwy konta może być alternatywa pełne szyfrowanie dysków za pomocą dm-crypt.

## <a name="what-is-the-disk-bek-volume-or-mntazure_bek_disk"></a>Co to jest dysk "Klucz szyfrowania bloków woluminu" lub "/ mnt/azure_bek_disk"?

"Klucz szyfrowania bloków woluminu" Windows lub "/ mnt/azure_bek_disk" dla systemu Linux jest woluminem danych lokalnych, która bezpiecznie klucze szyfrowania są przechowywane zaszyfrowane IaaS maszyn wirtualnych platformy Azure.
> [!NOTE]
> Nie należy usuwać ani edytować zawartość tego dysku. Odinstalowuje dysku, ponieważ obecności klucza szyfrowania jest wymagane dla wszystkich operacji szyfrowania na maszynie Wirtualnej IaaS.


## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Metodę szyfrowania używa usługi Azure Disk Encryption?

W systemie Windows, ADE używa metody szyfrowania funkcji BitLocker AES256 (AES256WithDiffuser w wersjach starszych niż Windows Server 2012). W systemie Linux w programie ADE jest stosowane szyfrowanie domyślnie AES-XTS-plain64 z użyciem klucza głównego woluminu 256-bitowego.

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>Czy w przypadku użycia EncryptFormatAll i określić wszystkie typy woluminu, jej spowoduje usunięcie danych na dyskach danych, które już zaszyfrowane?
Nie, dane nie będą usuwane z dysków danych, które już są szyfrowane przy użyciu usługi Azure Disk Encryption. Podobnie jak EncryptFormatAll nie został ponownie zaszyfrować dysk systemu operacyjnego, go nie będzie ponownie zaszyfrować dysk już zaszyfrowanych danych. Aby uzyskać więcej informacji, zobacz [kryteria EncryptFormatAll](azure-security-disk-encryption-linux.md#bkmk_EFACriteria).        

## <a name="is-xfs-filesystem-supported"></a>Czy system plików XFS jest obsługiwany?
Woluminy XFS są obsługiwane na potrzeby szyfrowania dysków danych tylko z EncryptFormatAll. Spowoduje to przeformatowanie woluminu, wymazanie wcześniej istniejących danych. Aby uzyskać więcej informacji, zobacz [kryteria EncryptFormatAll](azure-security-disk-encryption-linux.md#bkmk_EFACriteria).

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>Czy można utworzyć kopię zapasową i przywrócić zaszyfrowaną maszynę wirtualną? 

Azure Backup udostępnia mechanizm tworzenia kopii zapasowych i przywracania zaszyfrowanej maszyny wirtualnej w ramach tej samej subskrypcji i regionu.  Aby uzyskać instrukcje, zobacz [wykonywanie kopii zapasowej i Przywracanie zaszyfrowanych maszyn wirtualnych przy użyciu Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).  Przywracanie zaszyfrowanej maszyny wirtualnej do innego regionu nie jest obecnie obsługiwane.  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Gdzie można zadawać pytania lub opinię?

Możesz zadać pytania lub Wyraź swoją opinię [forum usługi Azure Disk Encryption](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption).

## <a name="next-steps"></a>Kolejne kroki
W tym dokumencie przedstawiono więcej informacji na temat najczęściej zadawane pytania związane z usługi Azure Disk Encryption. Aby uzyskać więcej informacji na temat tej usługi zobacz następujące artykuły:

- [Omówienie szyfrowania dysków Azure](azure-security-disk-encryption-overview.md)
- [Zastosuj szyfrowanie dysków w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Funkcja szyfrowania nieaktywnych danych platformy Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
