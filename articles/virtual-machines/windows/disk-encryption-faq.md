---
title: Często zadawane pytania — Azure Disk Encryption dla maszyn wirtualnych z systemem Windows
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące Microsoft Azure szyfrowania dysków dla maszyn wirtualnych z systemem Windows IaaS.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 11/01/2019
ms.custom: seodec18
ms.openlocfilehash: ea2a66a6b012664a9596a02ea32c1a0b677ee3ea
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384265"
---
# <a name="azure-disk-encryption-for-windows-vms-faq"></a>Azure Disk Encryption dla maszyn wirtualnych z systemem Windows — często zadawane pytania

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące Azure Disk Encryption maszyn wirtualnych z systemem Windows. Aby uzyskać więcej informacji na temat tej usługi, zobacz [Azure Disk Encryption przegląd](disk-encryption-overview.md).

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Gdzie jest usługa Azure Disk Encryption ogólnie dostępna (GA)?

Azure Disk Encryption jest ogólnie dostępna we wszystkich regionach publicznych platformy Azure.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Jakie podejrzewać są dostępne za pomocą usługi Azure Disk Encryption?

Azure GA szyfrowania dysku obsługuje szablony usługi Azure Resource Manager, programu Azure PowerShell i wiersza polecenia platformy Azure. Procesy, przez innego użytkownika zapewniają elastyczność. Dostępne są trzy różne opcje włączania szyfrowania dysków dla maszyn wirtualnych. Aby uzyskać więcej informacji na temat środowiska użytkownika i wskazówki krok po kroku dostępne w Azure Disk Encryption, zobacz [Azure Disk Encryption scenariusze dla systemu Windows](disk-encryption-windows.md).

## <a name="how-much-does-azure-disk-encryption-cost"></a>Ile kosztuje usługa Azure Disk Encryption

Nie jest naliczana opłata za szyfrowanie dysków maszyn wirtualnych za pomocą Azure Disk Encryption, ale naliczane są opłaty związane z użyciem Azure Key Vault. Aby uzyskać więcej informacji na temat Azure Key Vault kosztów, zobacz stronę [cennika Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) .

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Jak rozpocząć, za pomocą usługi Azure Disk Encryption?

Aby rozpocząć, zapoznaj się z [omówieniem Azure Disk Encryption](disk-encryption-overview.md).

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>Jakie rozmiary maszyn wirtualnych i systemów operacyjnych Azure Disk Encryption obsługiwać?

W artykule [przegląd Azure Disk Encryption](disk-encryption-overview.md) przedstawiono [rozmiary maszyn wirtualnych](disk-encryption-overview.md#supported-vm-sizes) i [systemy operacyjne maszyn wirtualnych](disk-encryption-overview.md#supported-operating-systems) obsługujące Azure Disk Encryption.

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Czy można zaszyfrować woluminów rozruchowych i danych przy użyciu usługi Azure Disk Encryption?

Można zaszyfrować woluminy rozruchowe i dane, ale nie można szyfrować danych bez wcześniejszego szyfrowania woluminu systemu operacyjnego.

Po zaszyfrowaniu woluminu systemu operacyjnego wyłączenie szyfrowania na woluminie systemu operacyjnego nie jest obsługiwane.

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>Czy mogę zaszyfrować wolumin odinstalowany z Azure Disk Encryption?

Nie, Azure Disk Encryption szyfruje tylko woluminy zainstalowane.

## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>Jak mogę obrócić wpisy tajne lub klucze szyfrowania?

Aby obrócić wpisy tajne, po prostu wywołaj to samo polecenie, które zostało użyte wcześniej do włączenia szyfrowania dysku, określając inny Key Vault. Aby obrócić klucz szyfrowania klucza, wywołaj to samo polecenie, które zostało użyte pierwotnie, aby włączyć szyfrowanie dysków, określając nowe szyfrowanie klucza. 

>[!WARNING]
> - Jeśli wcześniej była używana [Azure Disk Encryption z aplikacją usługi Azure AD](disk-encryption-windows-aad.md) przez określenie poświadczeń usługi Azure AD do zaszyfrowania tej maszyny wirtualnej, należy użyć tej opcji w celu ZASZYFROWANIA maszyny wirtualnej. Nie można użyć Azure Disk Encryption dla tej zaszyfrowanej maszyny wirtualnej, ponieważ nie jest to obsługiwany scenariusz, co oznacza, że przełączanie z aplikacji usługi AAD dla tej zaszyfrowanej maszyny wirtualnej nie jest jeszcze obsługiwane.

## <a name="how-do-i-add-or-remove-a-key-encryption-key-if-i-didnt-originally-use-one"></a>Jak mogę dodać lub usunąć klucz szyfrowania klucza, jeśli nie był pierwotnie używany?

Aby dodać klucz szyfrowania klucza, wywołaj polecenie Włącz ponownie, przekazując klucz szyfrowania klucza. Aby usunąć klucz szyfrowania klucza, wywołaj polecenie Włącz ponownie bez parametru klucza szyfrowania klucza.

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok"></a>Czy usługa Azure Disk Encryption umożliwia używanie własnego klucza (BYOK)?

Tak, możesz podać własne klucze szyfrowania. Te klucze są chronione w usłudze Azure Key Vault, czyli magazynu kluczy dla usługi Azure Disk Encryption. Aby uzyskać więcej informacji na temat scenariuszy obsługi kluczy szyfrowania kluczy, zobacz [Tworzenie i Konfigurowanie magazynu kluczy dla Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>Czy można używać klucza szyfrowania klucza utworzone przez platformę Azure?

Tak, można użyć usługi Azure Key Vault, aby wygenerować klucz szyfrowania klucza do użycia szyfrowania dysków Azure. Te klucze są chronione w usłudze Azure Key Vault, czyli magazynu kluczy dla usługi Azure Disk Encryption. Aby uzyskać więcej informacji o kluczu szyfrowania kluczy, zobacz [Tworzenie i Konfigurowanie magazynu kluczy dla Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>Czy można użyć Usługa zarządzania kluczami w środowisku lokalnym lub przez moduł HSM do ochrony kluczy szyfrowania?

Usługa zarządzania kluczami w środowisku lokalnym lub modułu HSM nie można użyć do ochrony kluczy szyfrowania za pomocą usługi Azure Disk Encryption. Usługa Azure Key Vault służy tylko do ochrony kluczy szyfrowania. Aby uzyskać więcej informacji na temat scenariuszy obsługi klucza szyfrowania kluczy, zobacz [Tworzenie i Konfigurowanie magazynu kluczy dla Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>Jakie są wymagania wstępne dotyczące konfigurowania usługi Azure Disk Encryption?

Istnieją wymagania wstępne dotyczące usługi Azure Disk Encryption. Zapoznaj się z artykułem [Tworzenie i Konfigurowanie magazynu kluczy na potrzeby Azure Disk Encryption](disk-encryption-key-vault.md) artykułu w celu utworzenia nowego magazynu kluczy lub skonfigurowania istniejącego magazynu kluczy na potrzeby dostępu do szyfrowania dysków w celu włączenia szyfrowania i zabezpieczenia kluczy tajnych oraz klucza. Aby uzyskać więcej informacji na temat scenariuszy obsługi klucza szyfrowania kluczy, zobacz [Tworzenie i Konfigurowanie magazynu kluczy dla Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption-with-an-azure-ad-app-previous-release"></a>Jakie są wymagania wstępne dotyczące konfigurowania usługi Azure Disk Encryption przy użyciu aplikacji usługi Azure AD (poprzedniej wersji)?

Istnieją wymagania wstępne dotyczące usługi Azure Disk Encryption. Aby utworzyć aplikację Azure Active Directory, należy zapoznać się z tematem [Azure Disk Encryption with Azure AD](disk-encryption-windows-aad.md) . w tym celu należy utworzyć nowy magazyn kluczy lub skonfigurować istniejący magazyn kluczy na potrzeby dostępu do szyfrowania dysku w celu włączenia szyfrowania i zabezpieczenia kluczy tajnych. Aby uzyskać więcej informacji na temat scenariuszy obsługi klucza szyfrowania kluczy, zobacz [Tworzenie i Konfigurowanie magazynu kluczy dla Azure Disk Encryption za pomocą usługi Azure AD](disk-encryption-key-vault-aad.md).

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>Usługa Azure Disk Encryption korzysta aplikacja usługi Azure AD (poprzedniej wersji), w dalszym ciągu obsługiwany?
Tak. Szyfrowanie dysków za pomocą aplikacji usługi Azure AD jest nadal obsługiwane. Jednak podczas szyfrowania nowych maszyn wirtualnych zaleca się, że używasz nowej metody, a nie za pomocą aplikacji usługi Azure AD. 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>Czy można migrować maszyny wirtualne, które zostały zaszyfrowane za pomocą aplikacji usługi Azure AD, do szyfrowania bez aplikacji usługi Azure AD?
  Obecnie nie ma ścieżki migracji bezpośrednie dla maszyn, które zostały zaszyfrowane za pomocą aplikacji usługi Azure AD, do szyfrowania bez aplikacji usługi Azure AD. Ponadto nie ma bezpośrednią ścieżkę z szyfrowania bez aplikacji usługi Azure AD do szyfrowania za pomocą aplikacji usługi AD. 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Jakiej wersji programu Azure PowerShell obsługuje usługi Azure Disk Encryption?

Najnowszą wersję zestawu SDK programu Azure PowerShell umożliwia skonfigurowanie usługi Azure Disk Encryption. Pobierz najnowszą wersję [Azure PowerShell](https://github.com/Azure/azure-powershell/releases). Azure Disk Encryption *nie* jest obsługiwana przez zestaw Azure SDK w wersji 1.1.0.

## <a name="what-is-the-disk-bek-volume-or-mntazure_bek_disk"></a>Co to jest dysk "Klucz szyfrowania bloków woluminu" lub "/ mnt/azure_bek_disk"?

"Wolumin klucz szyfrowania bloków" to wolumin danych lokalnych, który bezpiecznie przechowuje klucze szyfrowania zaszyfrowanych maszyn wirtualnych platformy Azure.

> [!NOTE]
> Nie należy usuwać ani edytować zawartość tego dysku. Odinstalowuje dysku, ponieważ obecności klucza szyfrowania jest wymagane dla wszystkich operacji szyfrowania na maszynie Wirtualnej IaaS.

## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Metodę szyfrowania używa usługi Azure Disk Encryption?

Azure Disk Encryption wybiera metodę szyfrowania w funkcji BitLocker na podstawie wersji systemu Windows w następujący sposób:

| Wersje systemu Windows                 | Wersja | Metoda szyfrowania        |
|----------------------------------|--------|--------------------------|
| Windows Server 2012, Windows 10 lub nowszy  | > = 1511 |XTS-AES 256 bit           |
| Windows Server 2012, Windows 8, 8,1, 10 | < 1511 |Bit AES 256 *              |
| 2008R2 systemu Windows Server            |        |Bit AES 256 z rozpraszaniem |

\* AES 256 bit with Dyfuzorer nie jest obsługiwany w systemie Windows 2012 i nowszych.

Aby określić wersję systemu operacyjnego Windows, uruchom narzędzie "winver" na maszynie wirtualnej.

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>Czy w przypadku użycia EncryptFormatAll i określić wszystkie typy woluminu, jej spowoduje usunięcie danych na dyskach danych, które już zaszyfrowane?
Nie, dane nie będą usuwane z dysków danych, które już są szyfrowane przy użyciu usługi Azure Disk Encryption. Podobnie jak EncryptFormatAll nie został ponownie zaszyfrować dysk systemu operacyjnego, go nie będzie ponownie zaszyfrować dysk już zaszyfrowanych danych. 

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>Czy można utworzyć kopię zapasową i przywrócić zaszyfrowaną maszynę wirtualną? 

Azure Backup udostępnia mechanizm tworzenia kopii zapasowych i przywracania zaszyfrowanej maszyny wirtualnej w ramach tej samej subskrypcji i regionu.  Aby uzyskać instrukcje, zobacz [wykonywanie kopii zapasowej i Przywracanie zaszyfrowanych maszyn wirtualnych przy użyciu Azure Backup](../../backup/backup-azure-vms-encryption.md).  Przywracanie zaszyfrowanej maszyny wirtualnej do innego regionu nie jest obecnie obsługiwane.  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Gdzie można zadawać pytania lub opinię?

Możesz zadawać pytania lub przekazać Opinie na [forum Azure Disk Encryption](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption).

## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono więcej informacji na temat najczęściej zadawane pytania związane z usługi Azure Disk Encryption. Aby uzyskać więcej informacji na temat tej usługi zobacz następujące artykuły:

- [Omówienie usługi Azure Disk Encryption](disk-encryption-overview.md)
- [Zastosuj szyfrowanie dysków w Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Szyfrowanie danych platformy Azure w spoczynku](../../security/fundamentals/encryption-atrest.md)
