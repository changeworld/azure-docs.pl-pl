---
title: Punkty końcowe usługi sieci wirtualnej dla usługi Azure Key Vault — usługi Azure Key Vault | Dokumentacja firmy Microsoft
description: Omówienie punktów końcowych usługi sieci wirtualnej dla usługi Key Vault
services: key-vault
author: amitbapat
ms.author: ambapat
manager: barbkess
ms.date: 01/02/2019
ms.service: key-vault
ms.topic: conceptual
ms.openlocfilehash: d003e851dcfa77b18a67450946a71e4a13ce495c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64700970"
---
# <a name="virtual-network-service-endpoints-for-azure-key-vault"></a>Punkty końcowe usługi sieci wirtualnej dla usługi Azure Key Vault

Punkty końcowe usługi sieci wirtualnej dla usługi Azure Key Vault umożliwiają ograniczenie dostępu do określonej sieci wirtualnej. Punkty końcowe umożliwiają również ograniczyć dostęp do listy zakresów adresów IPv4 (internet protocol w wersji 4). Odmowa dostępu dla każdego użytkownika, na nawiązywanie połączeń z usługi key vault z spoza tych źródeł.

Istnieje jeden ważny wyjątek z tego ograniczenia. Jeśli użytkownik ma wyrażeniu zgody na uczestnictwo w umożliwia zaufanych usług firmy Microsoft, połączenia z tych usług są Poinformuj przez zaporę. Na przykład usługi te to Office 365 Exchange Online, usługi Office 365 SharePoint Online, Azure compute, usługi Azure Resource Manager i usługi Azure Backup. Takie użytkownicy nadal muszą przedstawić prawidłowy token w usłudze Azure Active Directory, a musi uprawnień (skonfigurowana jako zasady dostępu) do wykonania żądanej operacji. Aby uzyskać więcej informacji, zobacz [punkty końcowe usługi sieci wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="usage-scenarios"></a>Scenariusze użycia

Można skonfigurować [usługi Key Vault zapory i sieci wirtualne](key-vault-network-security.md) Aby odmówić dostępu do ruchu, ze wszystkich sieci (w tym ruch z Internetu) domyślnie. Możesz udzielić dostępu do ruchu z określonych sieciach wirtualnych platformy Azure i publicznej sieci internet, adres IP zakresu, co pozwala na tworzenie bezpiecznej granicy sieci dla aplikacji.

> [!NOTE]
> Usługa Key Vault zapory i reguł sieci wirtualnej mają zastosowanie tylko do [płaszczyzny danych](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) usługi Key Vault. Operacje warstwy kontroli usługi Key Vault (takie jak tworzenie, usuwanie i modyfikowanie operacje, ustawianie zasad dostępu, ustawienia zapory i reguł sieci wirtualnej) nie ma wpływu zapory i reguł sieci wirtualnej.

Poniżej przedstawiono kilka przykładów zastosowania punktów końcowych usługi:

* Korzystania z usługi Key Vault do przechowywania kluczy szyfrowania, wpisów tajnych aplikacji i certyfikaty, a chcesz zablokować dostęp do magazynu kluczy z publicznego Internetu.
* Chcesz zablokować dostęp do magazynu kluczy, aby tylko aplikacji lub krótką listę wyznaczone hosty mogą się łączyć do magazynu kluczy.
* Masz aplikację działającą w usłudze Azure virtual network, a ta sieć wirtualna jest zablokowana dla całego ruchu przychodzącego i wychodzącego. Aplikacja nadal musi połączyć się z usługi Key Vault, aby pobrać klucze tajne i certyfikaty, lub użyj kluczy kryptograficznych.

## <a name="configure-key-vault-firewalls-and-virtual-networks"></a>Konfigurowanie usługi Key Vault zapory i sieci wirtualne

Poniżej przedstawiono kroki wymagane do skonfigurowania zapory i sieci wirtualne. Te kroki mają zastosowanie, czy używasz programu PowerShell, interfejsu wiersza polecenia platformy Azure lub witryny Azure portal.

1. Włącz [rejestrowania usługi Key Vault](key-vault-logging.md) aby zobaczyć szczegółowe uzyskiwanie dostępu do dzienników. Pomaga to w diagnostyce podczas zapory i reguł sieci wirtualnej uniemożliwić dostęp do magazynu kluczy. (Ten krok jest opcjonalny, ale zdecydowanie zalecany).
2. Włącz **punkty końcowe dla magazynu kluczy usługi** dla docelowych, sieciami wirtualnymi i podsieciami.
3. Ustawienie zapory i reguł sieci wirtualnej dla usługi key vault w celu ograniczenia dostępu do tego magazynu kluczy z określonej sieci wirtualnej, podsieci i zakresy adresów IPv4.
4. Jeśli tego magazynu kluczy musi być dostępny dla dowolnego zaufanych usług firmy Microsoft, włącz opcję, aby umożliwić **zaufanych usług Azure** nawiązać połączenia z usługi Key Vault.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie usługi Azure Key Vault zapory i sieci wirtualne](key-vault-network-security.md).

> [!IMPORTANT]
> Po reguły zapory są stosowane, użytkownicy usługi Key Vault można wykonać tylko [płaszczyzny danych](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) operacje podczas ich żądania pochodzą z dozwolone sieci wirtualnych lub zakresy adresów IPv4. Dotyczy to również do uzyskiwania dostępu do usługi Key Vault w witrynie Azure portal. Mimo że użytkownicy mogą przeglądać do magazynu kluczy w witrynie Azure portal, ich może nie móc Wyświetl listę kluczy, wpisów tajnych lub certyfikatów Jeśli swoim komputerze klienckim nie ma na liście dozwolonych. Dotyczy to również selektor klucza magazynu przez inne usługi platformy Azure. Użytkowników można wyświetlić listę magazynów kluczy, ale nie listy kluczy, jeśli reguły zapory na swoim komputerze klienckim.


> [!NOTE]
> Należy pamiętać o następujących ograniczeniach konfiguracji:
> * Dozwolone są maksymalnie 127 reguł sieci wirtualnej i 127 reguły protokołu IPv4. 
> * Używające zakresy adresów małych "/ 31" lub "/ 32" prefiks rozmiary nie są obsługiwane. Zamiast tego należy skonfigurować te zakresy, za pomocą poszczególne reguły dotyczące adresów IP.
> * Reguły sieciowych adresów IP są dozwolone tylko dla publicznych adresów IP. Zakresy adresów IP zarezerwowanych dla sieci prywatnych (zgodnie z definicją w dokumencie RFC 1918) są niedozwolone w regułach adresów IP. Prywatne sieci obejmują adresów rozpoczynających się **10.**, **172.16 31**, i **192.168.**. 
> * W tej chwili obsługiwane są tylko adresy IPv4.

## <a name="trusted-services"></a>Zaufanych usług

Oto lista zaufanych usług, które mogą uzyskać dostęp do magazynu kluczy, jeśli **Zezwalaj usługom zaufanych** opcja jest włączona.

|Zaufaną usługę|Scenariusze użycia|
| --- | --- |
|Usługa wdrażania Azure Virtual Machines|[Wdrażanie certyfikatów na maszynach wirtualnych z zarządzanego przez klienta usługi Key Vault](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/).|
|Usługa wdrażania szablonów Azure Resource Manager|[Przekazywanie bezpiecznych wartości podczas wdrażania](../azure-resource-manager/resource-manager-keyvault-parameter.md).|
|Usługa szyfrowania woluminów Azure Disk Encryption|Zezwalaj na dostęp do klucza funkcji BitLocker (systemu Windows Windows VM) lub DM hasło (maszyny Wirtualnej systemu Linux) i klucza szyfrowania podczas wdrażania maszyn wirtualnych. Dzięki temu [usługi Azure Disk Encryption](../security/azure-security-disk-encryption.md).|
|Azure Backup|Zezwalaj na kopia zapasowa i przywracanie odpowiednich kluczy i wpisów tajnych podczas tworzenia kopii zapasowej maszyn wirtualnych platformy Azure, za pomocą [kopia zapasowa Azure](../backup/backup-introduction-to-azure-backup.md).|
|Usługa Exchange Online i SharePoint Online|Zezwalaj na dostęp do klucza klienta szyfrowanie usługi Azure Storage za pomocą [klucz klienta](https://support.office.com/article/Controlling-your-data-in-Office-365-using-Customer-Key-f2cd475a-e592-46cf-80a3-1bfb0fa17697).|
|Azure Information Protection|Zezwalaj na dostęp do klucza dzierżawy do [usługi Azure Information Protection.](https://docs.microsoft.com/azure/information-protection/what-is-information-protection)|
|Azure App Service|[Wdróż certyfikat aplikacji internetowej platformy Azure za pośrednictwem usługi Key Vault](https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/).|
|Azure SQL Database|[Transparent Data Encryption z obsługą Bring Your Own Key dla usługi Azure SQL Database i Data Warehouse](../sql-database/transparent-data-encryption-byok-azure-sql.md?view=sql-server-2017&viewFallbackFrom=azuresqldb-current).|
|Azure Storage|[Szyfrowanie usługi Storage przy użyciu kluczy zarządzanych przez klienta w usłudze Azure Key Vault](../storage/common/storage-service-encryption-customer-managed-keys.md).|
|Azure Data Lake Store|[Szyfrowanie danych w usłudze Azure Data Lake Store](../data-lake-store/data-lake-store-encryption.md) za pomocą klucza zarządzanego przez klienta.|
|Usługa Azure databricks|[Szybkie, łatwe i współpracy platformy Apache Spark — usługi analizy](../azure-databricks/what-is-azure-databricks.md)|



> [!NOTE]
> Należy skonfigurować odpowiednie zasady dostępu magazynu kluczy do odpowiednich usług uzyskać dostęp do usługi Key Vault.

## <a name="next-steps"></a>Kolejne kroki

* [Zabezpieczanie własnego magazynu kluczy](key-vault-secure-your-key-vault.md)
* [Konfigurowanie zapór usługi Azure Key Vault i sieciami wirtualnymi](key-vault-network-security.md)
