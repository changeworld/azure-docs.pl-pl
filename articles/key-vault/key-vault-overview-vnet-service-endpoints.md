---
title: Punkty końcowe usługi sieci wirtualnej dla Azure Key Vault-Azure Key Vault | Microsoft Docs
description: Omówienie punktów końcowych usługi sieci wirtualnej dla Key Vault
services: key-vault
author: amitbapat
ms.author: ambapat
manager: rkarlin
ms.date: 01/02/2019
ms.service: key-vault
ms.topic: conceptual
ms.openlocfilehash: e31153a1c490948266714633202d5e59fe66fa3a
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980486"
---
# <a name="virtual-network-service-endpoints-for-azure-key-vault"></a>Punkty końcowe usługi sieci wirtualnej dla Azure Key Vault

Punkty końcowe usługi sieci wirtualnej dla Azure Key Vault umożliwiają ograniczenie dostępu do określonej sieci wirtualnej. Punkty końcowe umożliwiają również ograniczenie dostępu do listy zakresów adresów IPv4 (protokołu internetowego w wersji 4). Każdy użytkownik nawiązujący połączenie z magazynem kluczy spoza tych źródeł nie odmówi dostępu.

Istnieje jeden ważny wyjątek dla tego ograniczenia. Jeśli Użytkownik zezwolił na dostęp do zaufanych usług firmy Microsoft, połączenia z tych usług będą przełączane przez zaporę. Na przykład te usługi obejmują pakiet Office 365 Exchange Online, pakiet Office 365 SharePoint Online, usługa Azure COMPUTE, Azure Resource Manager i Azure Backup. Ci użytkownicy nadal muszą przedstawić prawidłowy token Azure Active Directory i muszą mieć uprawnienia (skonfigurowane jako zasady dostępu), aby wykonać żądaną operację. Aby uzyskać więcej informacji, zobacz [punkty końcowe usługi sieci wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="usage-scenarios"></a>Scenariusze użycia

Można skonfigurować [zapory Key Vault i sieci wirtualne](key-vault-network-security.md) , aby odmówić dostępu do ruchu ze wszystkich sieci (łącznie z ruchem internetowym). Można udzielić dostępu do ruchu z określonych sieci wirtualnych platformy Azure i publicznych zakresów adresów IP, co pozwala na tworzenie bezpiecznej granicy sieci dla aplikacji.

> [!NOTE]
> Key Vault zapory i reguły sieci wirtualnej stosują się tylko do [płaszczyzny danych](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) Key Vault. Nie mają one wpływ na działania dotyczące płaszczyzny sterowania Key Vault (takie jak tworzenie, usuwanie i modyfikowanie operacji, Ustawianie zasad dostępu, Ustawianie zapór i reguł sieci wirtualnej).

Poniżej przedstawiono kilka przykładów użycia punktów końcowych usługi:

* Używasz Key Vault do przechowywania kluczy szyfrowania, wpisów tajnych aplikacji i certyfikatów i chcesz zablokować dostęp do magazynu kluczy z publicznej sieci Internet.
* Chcesz zablokować dostęp do magazynu kluczy, tak aby tylko aplikacja lub krótka lista wystawianych hostów mogła połączyć się z magazynem kluczy.
* Aplikacja działa w sieci wirtualnej platformy Azure, a ta sieć wirtualna jest zablokowana dla całego ruchu przychodzącego i wychodzącego. Aplikacja nadal musi nawiązać połączenie z usługą Key Vault, aby pobrać klucze tajne lub certyfikaty lub użyć kluczy kryptograficznych.

## <a name="configure-key-vault-firewalls-and-virtual-networks"></a>Konfigurowanie zapór Key Vault i sieci wirtualnych

Poniżej przedstawiono kroki wymagane do skonfigurowania zapór i sieci wirtualnych. Te kroki mają zastosowanie, niezależnie od tego, czy używasz programu PowerShell, interfejsu wiersza polecenia platformy Azure, czy Azure Portal.

1. Włącz [rejestrowanie Key Vault](key-vault-logging.md) , aby wyświetlić szczegółowe dzienniki dostępu. Pomaga to w diagnostyce, gdy zapory i reguły sieci wirtualnej uniemożliwiają dostęp do magazynu kluczy. (Ten krok jest opcjonalny, ale zdecydowanie zalecany).
2. Włącz **punkty końcowe usługi dla magazynu kluczy** dla docelowych sieci wirtualnych i podsieci.
3. Ustawianie zapór i reguł sieci wirtualnej dla magazynu kluczy w celu ograniczenia dostępu do tego magazynu kluczy z określonych sieci wirtualnych, podsieci i zakresów adresów IPv4.
4. Jeśli ten magazyn kluczy musi być dostępny dla wszystkich zaufanych usług firmy Microsoft, Włącz opcję zezwalania **zaufanym usługom platformy Azure** na łączenie się z usługą Key Vault.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie zapór Azure Key Vault i sieci wirtualnych](key-vault-network-security.md).

> [!IMPORTANT]
> Po zastosowaniu reguł zapory użytkownicy mogą wykonywać Key Vault operacje [płaszczyzny danych](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) tylko wtedy, gdy ich żądania pochodzą z dozwolonych sieci wirtualnych lub zakresów adresów IPv4. Dotyczy to również uzyskiwania dostępu do Key Vault z Azure Portal. Mimo że użytkownicy mogą przechodzić do magazynu kluczy z Azure Portal, mogą nie być w stanie wyświetlać kluczy, wpisów tajnych ani certyfikatów, jeśli ich maszyny klienckie nie znajdują się na liście dozwolonych. Ma to również wpływ na wybór Key Vault przez inne usługi platformy Azure. Użytkownicy mogą widzieć listę magazynów kluczy, ale nie listy kluczy, jeśli reguły zapory uniemożliwiają ich komputerom klienckim.


> [!NOTE]
> Należy pamiętać o następujących ograniczeniach konfiguracji:
> * Dozwolone są maksymalnie 127 reguły sieci wirtualnej i 127. 
> * Małe zakresy adresów, które używają rozmiarów prefiksu "/31" lub "/32", nie są obsługiwane. Zamiast tego należy skonfigurować te zakresy przy użyciu poszczególnych reguł adresów IP.
> * Reguły sieci IP są dozwolone tylko dla publicznych adresów IP. Zakresy adresów IP zarezerwowane dla sieci prywatnych (zgodnie z definicją w dokumencie RFC 1918) nie są dozwolone w regułach adresów IP. Sieci prywatne obejmują adresy, które zaczynają się od **10.** , **172.16-31**i **192,168.** 
> * W tej chwili są obsługiwane tylko adresy IPv4.

## <a name="trusted-services"></a>Usługi zaufane

Poniżej znajduje się lista zaufanych usług, które mogą uzyskiwać dostęp do magazynu kluczy, jeśli jest włączona opcja **Zezwalaj na zaufane usługi** .

|Usługa zaufana|Obsługiwane scenariusze użycia|
| --- | --- |
|Usługa wdrażania Virtual Machines platformy Azure|[Wdróż certyfikaty na maszynach wirtualnych z poziomu Key Vault zarządzanych przez klienta](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/).|
|Usługa wdrażania szablonów Azure Resource Manager|[Przekazywanie bezpiecznych wartości podczas wdrażania](../azure-resource-manager/templates/key-vault-parameter.md).|
|Azure Disk Encryption usługi szyfrowania woluminów|Zezwalaj na dostęp do klucza funkcji BitLocker (maszyna wirtualna z systemem Windows) lub hasła DM (maszyna wirtualna z systemem Linux) oraz klucz szyfrowania klucza podczas wdrażania maszyn wirtualnych. Umożliwia to [Azure Disk Encryption](../security/fundamentals/encryption-overview.md).|
|Usługa Azure Backup|Zezwalaj na wykonywanie kopii zapasowych i przywracanie odpowiednich kluczy i wpisów tajnych podczas tworzenia kopii zapasowej w usłudze Azure Virtual Machines przy użyciu [Azure Backup](../backup/backup-introduction-to-azure-backup.md).|
|Usługa Exchange Online & SharePoint Online|Zezwalaj na dostęp do klucza klienta dla usługi Azure szyfrowanie usługi Storage przy użyciu [klucza klienta](https://support.office.com/article/Controlling-your-data-in-Office-365-using-Customer-Key-f2cd475a-e592-46cf-80a3-1bfb0fa17697).|
|Azure Information Protection|Zezwalaj na dostęp do klucza dzierżawy dla [Azure Information Protection.](https://docs.microsoft.com/azure/information-protection/what-is-information-protection)|
|Azure App Service|[Wdróż certyfikat aplikacji sieci Web platformy Azure za pomocą Key Vault](https://azure.github.io/AppService/2016/05/24/Deploying-Azure-Web-App-Certificate-through-Key-Vault.html).|
|Baza danych SQL Azure|[Transparent Data Encryption z obsługą Bring Your Own Key Azure SQL Database i magazynem danych](../sql-database/transparent-data-encryption-byok-azure-sql.md?view=sql-server-2017&viewFallbackFrom=azuresqldb-current).|
|Azure Storage|[Szyfrowanie usługi Storage przy użyciu kluczy zarządzanych przez klienta w programie Azure Key Vault](../storage/common/storage-service-encryption-customer-managed-keys.md).|
|Azure Data Lake Store|[Szyfrowanie danych w Azure Data Lake Store](../data-lake-store/data-lake-store-encryption.md) z kluczem zarządzanym przez klienta.|
|Azure Databricks|[Szybka, łatwa i wydajna usługa analizy oparta na Apache Sparkach](../azure-databricks/what-is-azure-databricks.md)|
|Usługa Azure API Management|[Wdrażanie certyfikatów dla domeny niestandardowej z Key Vault przy użyciu pliku MSI](../api-management/api-management-howto-use-managed-service-identity.md#use-the-managed-service-identity-to-access-other-resources)|
|Azure Data Factory|[Pobieranie poświadczeń magazynu danych w Key Vault z Data Factory](https://go.microsoft.com/fwlink/?linkid=2109491)|



> [!NOTE]
> Należy skonfigurować odpowiednie zasady dostępu Key Vault, aby umożliwić odpowiednim usługom uzyskanie dostępu do Key Vault.

## <a name="next-steps"></a>Następne kroki

* [Zabezpieczanie własnego magazynu kluczy](key-vault-secure-your-key-vault.md)
* [Konfigurowanie zapór Azure Key Vault i sieci wirtualnych](key-vault-network-security.md)
