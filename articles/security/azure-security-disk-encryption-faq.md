---
title: Szyfrowanie dysków Azure — często zadawane pytania | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące programu Microsoft Azure dysku szyfrowanie dla systemu Windows i maszyn wirtualnych systemu Linux IaaS.
services: security
documentationcenter: na
author: DevTiw
manager: avibm
editor: barclayn
ms.assetid: 7188da52-5540-421d-bf45-d124dee74979
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/27/2018
ms.author: barclayn
ms.openlocfilehash: 47ccf91a64653c928cc4da01bc98535c97440d37
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32187695"
---
# <a name="azure-disk-encryption-faq"></a>Szyfrowanie dysków Azure — często zadawane pytania

Ten artykuł zawiera odpowiedzi na często zadawane pytania (FAQ) dotyczące Azure dysku szyfrowanie dla systemu Windows i maszyn wirtualnych systemu Linux IaaS. Aby uzyskać więcej informacji na temat tej usługi, zobacz [Azure dysku szyfrowanie dla systemu Windows i maszyn wirtualnych systemu Linux IaaS](https://docs.microsoft.com/azure/security/azure-security-disk-encryption).

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>W przypadku szyfrowania dysków Azure w ogólnodostępnej (GA)?

Azure szyfrowania dysku dla systemu Windows i maszyn wirtualnych systemu Linux IaaS jest ogólnie dostępności we wszystkich regionach publicznej Azure.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Jakie użytkowników są dostępne w przypadku szyfrowania dysków Azure?

Azure GA szyfrowania dysku obsługuje szablonów usługi Azure Resource Manager, programu Azure PowerShell i interfejsu wiersza polecenia Azure. Zapewnia dużą elastyczność. Możliwe są trzy różne umożliwiających szyfrowanie dysku dla maszyn wirtualnych IaaS. Aby uzyskać więcej informacji dotyczących użytkowników i wskazówki krok po kroku, które są dostępne w szyfrowania dysków Azure, zobacz [szyfrowania dysków Azure wdrożeń i środowisk](azure-security-disk-encryption.md#disk-encryption-deployment-scenarios-and-user-experiences).

## <a name="how-much-does-azure-disk-encryption-cost"></a>Ile kosztuje szyfrowania dysków Azure?

Nie jest bezpłatne szyfrowania dysków maszyny Wirtualnej przy użyciu szyfrowania dysków Azure, ale brak opłat skojarzonych z użyciem usługi Azure Key Vault. Aby uzyskać więcej informacji na temat usługi Azure Key Vault kosztów odwoływać się do [cennik usługi Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) strony.

## <a name="which-virtual-machine-tiers-does-azure-disk-encryption-support"></a>Warstwy maszyny wirtualnej, które obsługuje szyfrowanie dysków Azure?

Szyfrowanie dysków Azure jest dostępna na maszynach wirtualnych warstwy standardowa, łącznie z [A, D DS, G, GS i F](https://azure.microsoft.com/pricing/details/virtual-machines/) serii maszyn wirtualnych IaaS. Jest również dostępna dla maszyn wirtualnych z magazyn w warstwie premium. Nie jest dostępne w warstwie podstawowa maszyn wirtualnych.

## <a name="what-linux-distributions-does-azure-disk-encryption-support"></a>Dystrybucje systemu Linux, które obsługuje szyfrowanie dysków Azure?

Szyfrowanie dysków Azure jest obsługiwane w następujących dystrybucje serwera systemu Linux i wersji:

| Dystrybucja systemu Linux | Wersja | Typ woluminu obsługiwany w przypadku szyfrowania|
| --- | --- |--- |
| Ubuntu | 16.04-DAILY-LTS | Dysk systemu operacyjnego i danych |
| Ubuntu | 14.04.5-DAILY-LTS | Dysk systemu operacyjnego i danych |
| RHEL | 7.4 | Dane dysku * |
| RHEL | 7.3 | Dane dysku * |
| RHEL | 7.2 | Dane dysku * |
| RHEL | 6.8 | Dane dysku * |
| RHEL | 6.7 | Dane dysku * |
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
| SLES | Priorytet: 11-SP4 | Dysk z danymi |
| SLES | 11 SP4 | Dysk z danymi |

*__ADE jest obsługiwana przez RHEL dla dysku danych. Bieżąca implementacja ADE działa dla dysku systemu operacyjnego, ale nie jest aktualnie wspólnie obsługiwane. Zarówno firmy Microsoft, jak i Red Hat pracuje nad rozwiązaniem wspólnie obsługiwane. W międzyczasie możesz odwoływać się w dokumencie ADE do szyfrowania dysku systemu operacyjnego Linux [tutaj](https://docs.microsoft.com/azure/security/azure-security-disk-encryption).__

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Jak można uruchomić przy użyciu szyfrowania dysków Azure?

Aby rozpocząć, przeczytaj [Azure dysku szyfrowanie dla systemu Windows i maszyn wirtualnych systemu Linux IaaS](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) oficjalny dokument.

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Można zaszyfrować woluminów zarówno rozruchu i danych za pomocą szyfrowania dysków Azure?

Tak, można zaszyfrować woluminy rozruchowe i danych systemu Windows i maszyn wirtualnych systemu Linux IaaS. Dla maszyn wirtualnych systemu Windows nie można zaszyfrować dane bez pierwszy szyfrowania woluminu systemu operacyjnego. Dla maszyn wirtualnych systemu Linux można zaszyfrować ilość danych, bez konieczności najpierw szyfrowania woluminu systemu operacyjnego. Po zaszyfrowanych woluminu systemu operacyjnego Linux wyłączenie szyfrowania na woluminie systemu operacyjnego dla maszyn wirtualnych systemu Linux IaaS nie jest obsługiwane.

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok-capability"></a>Czy szyfrowania dysków Azure umożliwia własnego klucza (BYOK) możliwość dostosowania?

Tak, możesz podać własne klucze szyfrowania kluczy. Te klucze są chronione w usłudze Azure Key Vault, która jest magazynu kluczy do szyfrowania dysków Azure. Aby uzyskać więcej informacji o kluczach szyfrowania klucza obsługi scenariuszy, zobacz [szyfrowania dysków Azure wdrożeń i środowisk](azure-security-disk-encryption.md#disk-encryption-deployment-scenarios-and-user-experiences).

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>Można użyć klucza szyfrowania klucza utworzone Azure?

Tak, można użyć usługi Azure Key Vault, aby wygenerować klucz szyfrowania do użycia szyfrowania dysków Azure. Te klucze są chronione w usłudze Azure Key Vault, która jest magazynu kluczy do szyfrowania dysków Azure. Aby uzyskać więcej informacji o scenariuszach Obsługa kluczy szyfrowania, zobacz [szyfrowania dysków Azure wdrożeń i środowisk](azure-security-disk-encryption.md#disk-encryption-deployment-scenarios-and-user-experiences).

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>Ochrona kluczy szyfrowania można użyć lokalnej usługi zarządzania kluczami lub sprzętowego modułu zabezpieczeń?

Nie można użyć lokalnej usługi zarządzania kluczami lub modułu HSM do ochrony kluczy szyfrowania z szyfrowania dysków Azure. Usługa Azure Key Vault służy tylko do ochrony kluczy szyfrowania. Aby uzyskać więcej informacji o scenariuszach Obsługa kluczy szyfrowania, zobacz [szyfrowania dysków Azure wdrożeń i środowisk](azure-security-disk-encryption.md#disk-encryption-deployment-scenarios-and-user-experiences).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>Jakie są wymagania wstępne, aby skonfigurować szyfrowanie dysków Azure?

Brak wymagań wstępnych skrypt programu PowerShell. Za pomocą tego skryptu można utworzyć aplikację usługi Azure Active Directory, Utwórz nowy magazyn kluczy lub skonfigurować istniejący magazyn kluczy na dostęp do szyfrowania dysku włączyć szyfrowanie i ochronę kluczy tajnych i kluczy. Aby uzyskać więcej informacji o scenariuszach Obsługa kluczy szyfrowania, zobacz [wymagań wstępnych szyfrowania dysków Azure i wdrożeń i środowisk](azure-security-disk-encryption.md#prerequisites).

## <a name="where-can-i-get-more-information-on-how-to-use-powershell-for-configuring-azure-disk-encryption"></a>Gdzie można uzyskać więcej informacji na temat sposobu konfigurowania szyfrowania dysków Azure przy użyciu programu PowerShell?

Mamy niektóre dużą artykuły, w jaki sposób można wykonywać podstawowe zadania szyfrowania dysków Azure, a także bardziej zaawansowanych scenariuszy. Do podstawowych zadań, zobacz [Eksploruj szyfrowania dysków Azure przy użyciu programu Azure PowerShell — część 1](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/16/explore-azure-disk-encryption-with-azure-powershell/). Aby uzyskać bardziej zaawansowanych scenariuszy, zobacz [Eksploruj szyfrowania dysków Azure przy użyciu programu Azure PowerShell — część 2](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2/).

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Która wersja programu Azure PowerShell obsługuje szyfrowanie dysków Azure?

Użyj najnowszej wersji zestawu SDK programu PowerShell usługi Azure, aby skonfigurować szyfrowanie dysków Azure. Pobierz najnowszą wersję [programu Azure PowerShell](https://github.com/Azure/azure-powershell/releases). Szyfrowanie dysków Azure jest *nie* obsługiwane przez zestaw Azure SDK w wersji 1.1.0.

> [!NOTE]
> Rozszerzenie podglądu szyfrowania dysku systemu Linux platformy Azure jest przestarzały. Aby uzyskać więcej informacji, zobacz [rozszerzenie Podgląd szyfrowania dysku wycofano Azure dla maszyn wirtualnych systemu Linux IaaS](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/12/deprecating-azure-disk-encryption-preview-extension-for-linux-iaas-vms/).

## <a name="can-i-apply-azure-disk-encryption-on-my-custom-linux-image"></a>Moje niestandardowego obrazu systemu Linux można zastosować szyfrowanie dysków Azure?

Nie można zastosować szyfrowanie dysków Azure do niestandardowego obrazu systemu Linux. Firma Microsoft obsługuje tylko obrazy Linux galerii obsługiwanych dystrybucji wcześniej o nazwie. Niestandardowe obrazy systemu Linux nie jest obecnie obsługiwana.

## <a name="can-i-apply-updates-to-a-linux-red-hat-vm-that-uses-the-yum-update"></a>Aktualizacje można stosować do Red Hat Maszynę wirtualną systemu Linux używającej aktualizacji yum?

Tak, można wykonać operacji aktualizacji lub poprawka maszyny Wirtualnej systemu Linux Red Hat. Aby uzyskać więcej informacji, zobacz [stosowania aktualizacji do zaszyfrowanej IaaS Red Hat maszyny Wirtualnej platformy Azure przy użyciu aktualizacji yum](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/13/applying-updates-to-a-encrypted-azure-iaas-red-hat-vm-using-yum-update/).

## <a name="what-is-the-recommended-azure-disk-encryption-workflow-for-linux"></a>Co to jest przepływ pracy szyfrowania dysków Azure zalecany dla systemu Linux?

Poniższy przepływ pracy jest zalecane, aby uzyskać najlepsze rezultaty w systemie Linux:
* Rozpocznij od obrazu niezmodyfikowanego galerii standardowych odpowiadającej żądanej distro systemu operacyjnego i wersji
* Utwórz kopię zapasową zainstalowanych dyskach, które będą szyfrowane.  Umożliwia odzyskiwanie w razie awarii, na przykład jeśli maszyna wirtualna jest ponowny rozruch szyfrowania została ukończona.
* Szyfrowanie (może zająć wiele godzin lub nawet przez kilka dni w zależności od właściwości maszyny wirtualnej i rozmiar wszystkich dysków dołączonych danych)
* Dostosowywanie, a w razie potrzeby dodaj oprogramowania do obrazu.

Jeśli ten przepływ pracy nie jest możliwe, opierając się na [szyfrowanie usługi Magazyn](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) (SSE) w magazynie platformy warstwy konto może być zamiast pełne szyfrowanie dysków za pomocą dm-crypt.

## <a name="what-is-the-disk-bek-volume-or-mntazurebekdisk"></a>Co to jest dysk "Wolumin Bek" lub "/ mnt/azure_bek_disk"?

"Wolumin Bek" dla systemu Windows lub "/ mnt/azure_bek_disk" dla systemu Linux jest woluminem danych lokalnych, służący do bezpiecznego przechowywania kluczy szyfrowania dla zaszyfrowanych IaaS maszynach wirtualnych platformy Azure.
> [!NOTE]
> Nie należy usuwać ani edytować zawartość w tym dysku. Odinstalowuje dysku, ponieważ obecności klucza szyfrowania jest potrzebny dla wszystkich operacji szyfrowania na Maszynie wirtualnej IaaS.

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Gdzie można przejść, aby zadać pytania lub wyrazić swoją opinię?

Można zadawać pytania i wyrazić swoją opinię na [forum szyfrowania dysków Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption).

## <a name="next-steps"></a>Kolejne kroki
W tym dokumencie przedstawiono więcej informacji na temat najczęściej zadawane pytania dotyczące szyfrowania dysków Azure. Aby uzyskać więcej informacji na temat tej usługi i jego możliwości zobacz następujące artykuły:

- [Zastosuj szyfrowanie dysków w Centrum zabezpieczeń Azure](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Szyfrowanie maszyny wirtualnej platformy Azure](https://docs.microsoft.com/azure/security-center/security-center-disk-encryption)
- [Szyfrowanie przechowywanych danych Azure](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)
