---
ms.assetid: ''
title: Magazyn kluczy punkty końcowe usługi sieci Wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Omówienie punktów końcowych usługi sieci wirtualnej dla usługi Key Vault
services: key-vault
author: amitbapat
ms.author: ambapat
manager: mbaldwin
ms.date: 08/31/2018
ms.service: key-vault
ms.workload: identity
ms.topic: conceptual
ms.openlocfilehash: 9b0600a7afb07600116440461037c7abcb9236de
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/18/2018
ms.locfileid: "49404328"
---
# <a name="virtual-network-service-endpoints-for-azure-key-vault"></a>Punkty końcowe usługi sieci wirtualnej dla usługi Azure Key Vault

Wirtualne sieci punktów końcowych usługi dla usługi Key Vault umożliwiają ograniczanie dostępu do określonej sieci wirtualnej i/lub listę zakresów adresów IPv4 (Internet Protocol wersja 4). Dowolny obiekt wywołujący nawiązywania połączenia z usługi key vault z spoza tych źródeł będzie otrzymywał odmowę dostępu. Jeśli została wybrana do klienta umożliwia "Microsoft zaufane usługi", takich jak Office 365 Exchange Online, usługi Office 365 SharePoint Online, usługi Azure compute, usługi Azure Resource Manager, itp. kopia zapasowa Azure, połączenia z tych usług umożliwi przez zaporę. Oczywiście takie obiekty wywołujące nadal należy przedstawić prawidłowy token usługi AAD i musi mieć uprawnienia (skonfigurowana jako zasady dostępu) do wykonania żądanej operacji. Przeczytaj więcej szczegółowych informacji technicznych o [punkty końcowe usługi sieci wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="usage-scenarios"></a>Scenariusze użycia

Można skonfigurować [usługi Key Vault zapory i sieci wirtualne](key-vault-network-security.md) Aby odmówić dostępu do ruchu, ze wszystkich sieci (w tym ruch internetowy) domyślnie. Można można udzielić dostępu do ruchu z określonych sieciach wirtualnych platformy Azure i/lub publicznej sieci internet, adres IP zakresu, co pozwala na tworzenie bezpiecznej granicy sieci dla aplikacji.

> [!NOTE]
> Usługa Key Vault zapory i reguł sieci wirtualnej mają zastosowanie tylko do magazynu kluczy [płaszczyzny danych](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control). Operacje warstwy kontroli usługi Key Vault (np. usługi key vault Utwórz, Usuń, Modyfikuj operacji ustawiania zasad dostępu, ustawienia zapory i reguł sieci wirtualnej) nie ma wpływu zapory i reguł sieci wirtualnej.

Na przykład:
* Jeśli używasz usługi Key Vault do przechowywania kluczy szyfrowania, wpisy tajne aplikacji, certyfikatów i chcesz zablokować dostęp do magazynu kluczy z publicznej sieci internet.
* Aby zablokować dostęp do magazynu kluczy, aby tylko aplikacji lub krótką listę wyznaczone hosty mogą łączyć się z własnego magazynu kluczy
* Masz aplikację działającą w usłudze Azure virtual network (VNET) i tej sieci Wirtualnej jest zablokowana dla całego ruchu przychodzącego i wychodzącego. Aplikacja nadal musi połączyć się z magazynu kluczy, aby pobrać klucze tajne i certyfikaty albo użyj kluczy kryptograficznych.

## <a name="configure-key-vault-firewalls-and-virtual-networks"></a>Konfigurowanie usługi Key Vault zapory i sieci wirtualne

Poniżej przedstawiono kroki wymagane do skonfigurowania zapory i sieci wirtualne. Te kroki nadal ten sam, niezależnie od tego, jakie interfejsu (portal Azure PowerShell, interfejsu wiersza polecenia) będzie używany do konfigurowania zapory i reguł sieci wirtualnej.
1. Opcjonalne, ale zdecydowanie zalecane: Włącz [rejestrowania usługi key vault](key-vault-logging.md) aby zobaczyć szczegółowe uzyskiwanie dostępu do dzienników. Pomoże to w diagnostyce podczas zapory i reguł sieci wirtualnej uniemożliwić dostęp do magazynu kluczy.
2. Włączyć "punkty końcowe usługi dla magazynu kluczy" do docelowej sieci wirtualnych i podsieci
3. Ustawienie zapory i reguł sieci wirtualnej dla usługi key vault w celu ograniczenia dostępu do tego magazynu kluczy z określonej sieci wirtualnych, podsieci i zakresy adresów IPv4.
4. Jeśli tego magazynu kluczy musi być dostępny dla dowolnego zaufanych usług firmy Microsoft, należy włączyć opcję, aby umożliwić "Zaufanych usług platformy Azure" do łączenia z usługą key vault.

Zapoznaj się [skonfigurować Azure Key Vault zapory i sieci wirtualne](key-vault-network-security.md) szczegółowe instrukcje krok po kroku.

> [!IMPORTANT]
> Gdy reguły zapory są stosowane, wszystkie usługi Key Vault [płaszczyzny danych](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) operacje mogą być wykonywane, gdy obiekt wywołujący żądania pochodzą z dozwolonych sieci wirtualnych lub zakres adresów IPV4. Dotyczy to również do uzyskiwania dostępu do magazynu kluczy z witryny Azure portal. Gdy użytkownik może przeglądarki do magazynu kluczy z witryny Azure portal, ich może nie móc listę kluczy/wpisów tajnych/certyfikatów Jeśli swoim komputerze klienckim nie ma na liście dozwolonych. Dotyczy to również selektor klucza — magazynu przez inne usługi platformy Azure. Użytkownicy mogą mieć możliwości wyświetlić listę magazynów kluczy, ale nie listy kluczy, jeśli reguły zapory na swoim komputerze klienckim.


> [!NOTE]
> * Dozwolone są maksymalna 127 reguł sieci Wirtualnej i 127 reguły protokołu IPv4. 
> * Zakresy adresów małe, za pomocą "/ 31" lub "/ 32" prefiks rozmiary nie są obsługiwane. Tych zakresów powinny być skonfigurowane przy użyciu reguły dotyczące poszczególnych adresów IP.
> * Reguły sieciowych adresów IP są dozwolone tylko dla publicznych adresów IP. Zakresy adresów IP zarezerwowanych dla sieci prywatnych (zgodnie z definicją w dokumencie RFC 1918) są niedozwolone w regułach adresów IP. Prywatne sieci obejmują adresów rozpoczynających się * zapis 10.* *, * 172.16. **, i * 192.168. **. 
> * W tej chwili obsługiwane są tylko adresy IPv4.

## <a name="trusted-services"></a>Zaufanych usług
Oto lista zaufanych usług, które mogą uzyskać dostęp do magazynu kluczy, gdy jest włączona opcja "Zezwalaj na zaufane usługi".

|Zaufaną usługę|Scenariusze użycia|
| --- | --- |
|Usługa wdrażania Azure Virtual Machines|[Wdrażanie certyfikatów na maszynach wirtualnych z zarządzanego przez klienta usługi Key Vault](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/)|
|Usługa wdrażania szablonów Azure Resource Manager|[Przekazywanie bezpiecznych wartości podczas wdrażania](../azure-resource-manager/resource-manager-keyvault-parameter.md)|
|Usługa szyfrowania woluminów Azure Disk Encryption|Zezwalaj na dostęp do klucza funkcji BitLocker (systemu Windows Windows VM) lub DM hasło (maszyny Wirtualnej systemu Linux), a także klucz szyfrowania klucza podczas wdrażania maszyny Wirtualnej, aby umożliwić [usługi Azure Disk Encryption](../security/azure-security-disk-encryption.md)|
|Azure Backup|Zezwalaj na kopie zapasowe i przywracanie odpowiednich kluczy i wpisów tajnych podczas tworzenia kopii zapasowej maszyny Wirtualnej platformy Azure, za pomocą [kopia zapasowa Azure](../backup/backup-introduction-to-azure-backup.md)|
|Usługa Exchange Online i SharePoint Online|Zezwalaj na dostęp do klucz klienta dla usługi szyfrowania przy użyciu [klucz klienta](https://support.office.com/article/Controlling-your-data-in-Office-365-using-Customer-Key-f2cd475a-e592-46cf-80a3-1bfb0fa17697).|
|Azure Information Protection|Zezwalaj na dostęp do klucza dzierżawy do [usługi Azure Information Protection.](https://docs.microsoft.com/azure/information-protection/what-is-information-protection)|
|App Services|[Wdrażanie certyfikatu aplikacji sieci Web platformy Azure za pośrednictwem usługi Key Vault](https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/)|
|Azure SQL|[Transparent Data Encryption z obsługą Bring Your Own Key dla usługi Azure SQL Database i Data Warehouse](../sql-database/transparent-data-encryption-byok-azure-sql.md?view=sql-server-2017&viewFallbackFrom=azuresqldb-current)|
|Azure Storage|[Szyfrowanie usługi Storage przy użyciu kluczy zarządzanych przez klienta w usłudze Azure Key Vault](../storage/common/storage-service-encryption-customer-managed-keys.md)|
|Azure Data Lake Store|[Szyfrowanie danych w usłudze Azure Data Lake Store](../data-lake-store/data-lake-store-encryption.md) klientów zarządzanych klucza|



> [!NOTE]
> Zasady dostępu odpowiedniego magazynu kluczy musi być równa Zezwalaj usługom odpowiednie uzyskać dostęp do magazynu kluczy.

## <a name="next-steps"></a>Kolejne kroki

* [Zabezpieczanie własnego magazynu kluczy](key-vault-secure-your-key-vault.md)
* [Konfigurowanie zapór usługi Azure Key Vault i sieciami wirtualnymi](key-vault-network-security.md)