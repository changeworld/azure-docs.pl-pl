---
title: Bezpieczny dostęp do magazynu kluczy — usługi Azure Key Vault | Dokumentacja firmy Microsoft
description: Zarządzaj uprawnieniami dostępu do usługi Azure Key Vault, klucze i wpisy tajne. Obejmuje model uwierzytelniania i autoryzacji dla usługi Key Vault i sposób zabezpieczania własnego magazynu kluczy.
services: key-vault
documentationcenter: ''
author: amitbapat
manager: barbkess
tags: azure-resource-manager
ms.assetid: e5b4e083-4a39-4410-8e3a-2832ad6db405
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: ambapat
ms.openlocfilehash: 20c58647b8a6283de4ca2b90c830fe54db927095
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58484190"
---
# <a name="secure-access-to-a-key-vault"></a>Bezpieczny dostęp do magazynu kluczy

Usługa Azure Key Vault to usługa w chmurze, która zabezpiecza klucze szyfrowania i wpisy tajne, takie jak certyfikaty, parametry połączenia i hasła. Ponieważ te dane są poufne i krytyczne dla działania, należy zabezpieczyć dostęp do magazynów kluczy, zezwalając tylko autoryzowane aplikacje i użytkownicy. Ten artykuł zawiera omówienie modelu dostępu do usługi Key Vault. Wyjaśnia uwierzytelnianie i autoryzację, a w tym artykule opisano sposób zabezpieczania dostępu do magazynów kluczy.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="access-model-overview"></a>Omówienie modelu dostępu

Dostęp do magazynu kluczy jest kontrolowany za pośrednictwem dwóch interfejsów: **płaszczyzny zarządzania** i **płaszczyzny danych**. Płaszczyzna zarządzania zarządza się usługi Key Vault sam. Operacje w tej płaszczyzny obejmują tworzenie i usuwanie magazynów kluczy, pobieranie właściwości usługi Key Vault i aktualizowanie zasad dostępu. Płaszczyzna danych jest miejscem służącym do pracy z danymi przechowywanymi w magazynie kluczy. Możesz dodawać, usuwanie i modyfikowanie kluczy, wpisów tajnych i certyfikatów.

Aby uzyskać dostęp do magazynu kluczy, albo płaszczyźnie, wszystkie podmioty wywołujące (użytkownicy lub aplikacje) musi mieć odpowiednie uwierzytelnianie i autoryzacja. Uwierzytelnianie ustala tożsamość elementu wywołującego. Autoryzacja Określa, jakie operacje może wykonywać obiekt wywołujący. 

Obu płaszczyzn używać usługi Azure Active Directory (Azure AD) do uwierzytelniania. Na potrzeby autoryzacji płaszczyzna zarządzania używa kontroli dostępu opartej na rolach (RBAC) i płaszczyzna danych używa zasad dostępu magazynu kluczy.

## <a name="active-directory-authentication"></a>Uwierzytelnianie za pomocą usługi Active Directory

Po utworzeniu magazynu kluczy w subskrypcji platformy Azure ma automatycznie skojarzone z dzierżawą usługi Azure AD subskrypcji. Wszystkie obiekty wywołujące w obu płaszczyzn należy zarejestrować się w tej dzierżawie i uwierzytelnianie dostępu do magazynu kluczy. W obu przypadkach aplikacje mają dostęp do magazynu kluczy na dwa sposoby:

- **Dostęp użytkownika, a także aplikacji**: Aplikacja uzyskuje dostęp do usługi Key Vault w imieniu zalogowanego użytkownika. Przykłady tego typu dostępu programu Azure PowerShell i witryny Azure portal. Dostęp użytkownika jest udzielany na dwa sposoby. Użytkownicy mogą uzyskiwać dostęp do usługi Key Vault z poziomu dowolnej aplikacji lub muszą oni korzystać z określonej aplikacji (nazywane _złożone tożsamości_).
- **Dostęp tylko do aplikacji**: Aplikacja działa jako demon usługi lub w tle zadanie. Tożsamości aplikacji udzielany jest dostęp do magazynu kluczy.

Dla obu typów dostępu do aplikacji uwierzytelnia się za pomocą usługi Azure AD. Aplikacja wykorzystuje dowolne [obsługiwane metody uwierzytelniania](../active-directory/develop/authentication-scenarios.md) na podstawie typu aplikacji. Aplikacja uzyskuje token zasobu na płaszczyźnie, aby udzielić dostępu. Zasób jest punktem końcowym w płaszczyzny zarządzania lub dane w zależności od środowiska platformy Azure. Aplikacja używa tokenu i wysyła żądanie interfejsu API REST do usługi Key Vault. Aby dowiedzieć się więcej, zapoznaj się z [całego przepływu uwierzytelniania](../active-directory/develop/v1-protocols-oauth-code.md).

Model pojedynczy mechanizm uwierzytelniania obu płaszczyzn ma kilka zalet:

- Organizacje mogą kontrolować dostęp centralnie do wszystkich magazynów kluczy w organizacji.
- Jeśli użytkownik pozostawi, natychmiast utraci dostęp do wszystkich magazynów kluczy w organizacji.
- Organizacje mogą dostosowywać uwierzytelnianie za pomocą opcji w usłudze Azure AD, takie jak włączyć uwierzytelnianie wieloskładnikowe w celu zwiększenia poziomu bezpieczeństwa.

## <a name="resource-endpoints"></a>Punktów końcowych zasobów

Aplikacje dostęp do płaszczyzny za pośrednictwem punktów końcowych. Kontrolę dostępu dla dwóch płaszczyzn działać niezależnie. Aby udzielić dostępu aplikacji do używania kluczy w magazynie kluczy, można przyznać dostęp do płaszczyzny danych za pośrednictwem zasad dostępu magazynu kluczy. Aby przyznać dostęp do odczytu użytkowników do usługi Key Vault, właściwości i tagów, ale nie umożliwia uzyskiwania dostępu do danych (kluczy, wpisów tajnych lub certyfikatów), można przyznać dostęp do płaszczyzny zarządzania przy użyciu RBAC.

W poniższej tabeli przedstawiono punktów końcowych zarządzania i płaszczyzny danych.

| Access&nbsp;plane | Punkty końcowe dostępu | Operacje | Dostęp&nbsp;mechanizmy kontroli |
| --- | --- | --- | --- |
| Płaszczyzna zarządzania | **Cały świat:**<br> management.azure.com:443<br><br> **Usługa Azure China 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Wersja platformy Azure dla administracji USA:**<br> management.usgovcloudapi.net:443<br><br> **Niemiecka wersja platformy Azure:**<br> management.microsoftazure.de:443 | Tworzenie, Odczyt, aktualizowanie i usuwanie magazynów kluczy<br><br>Ustawianie zasad dostępu magazynu kluczy<br><br>Ustawianie tagów usługi Key Vault | RBAC usługi Azure Resource Manager |
| Płaszczyzna danych | **Cały świat:**<br> &lt;nazwa_magazynu&gt;.vault.azure.net:443<br><br> **Usługa Azure China 21Vianet:**<br> &lt;nazwa_magazynu&gt;.vault.azure.cn:443<br><br> **Wersja platformy Azure dla administracji USA:**<br> &lt;nazwa_magazynu&gt;.vault.usgovcloudapi.net:443<br><br> **Niemiecka wersja platformy Azure:**<br> &lt;nazwa_magazynu&gt;.vault.microsoftazure.de:443 | Klucze: Odszyfruj, Szyfruj,<br> unwrap, opakowywanie, sprawdź, zaloguj się,<br> Pobierz, lista, aktualizować, tworzyć,<br> Import, usuwania, przywracania kopii zapasowych,<br><br> Wpisy tajne: pobieranie, listy, ustaw, Usuń | Zasady dostępu magazynu kluczy |

## <a name="management-plane-and-rbac"></a>Płaszczyzna zarządzania i RBAC

Na płaszczyźnie zarządzania umożliwia RBAC (rola w oparciu Access Control) autoryzować operacje, które może wykonywać obiekt wywołujący. W modelu RBAC każdej subskrypcji platformy Azure ma wystąpienia usługi Azure AD. Możesz udzielić dostępu użytkownikom, grupom i aplikacjom z tego katalogu. Dostęp jest udzielany do zarządzania zasobami w subskrypcji platformy Azure, które używają modelu wdrażania usługi Azure Resource Manager. Aby udzielić dostępu, należy użyć [witryny Azure portal](https://portal.azure.com/), [wiersza polecenia platformy Azure](../cli-install-nodejs.md), [programu Azure PowerShell](/powershell/azureps-cmdlets-docs), lub [interfejsów API REST usługi Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn906885.aspx).

Tworzenie magazynu kluczy w grupie zasobów i zarządzanie dostępem przy użyciu usługi Azure AD. Zezwolenie użytkownikom na lub grup możliwość zarządzania magazynami kluczy w grupie zasobów. Przypisując odpowiednie role RBAC, można przyznać dostęp na poziomie określonego zakresu. Aby udzielić dostępu użytkownika do zarządzania magazynami kluczy, możesz przypisać wstępnie zdefiniowanej `key vault Contributor` roli do użytkownika o określonym zakresie. Następujące poziomy zakresów można przypisać do roli RBAC:

- **Subskrypcja**: Rola RBAC, przypisana na poziomie subskrypcji ma zastosowanie do wszystkich grup zasobów i zasobów w ramach tej subskrypcji.
- **Grupa zasobów**: Rola RBAC, przypisana na poziomie grupy zasobów ma zastosowanie do wszystkich zasobów w danej grupie zasobów.
- **Określony zasób**: Rola RBAC, przypisane do określonego zasobu ma zastosowanie do tego zasobu. W tym przypadku zasób jest określonego magazynu kluczy.

Istnieje kilka wstępnie zdefiniowanych ról. Jeśli wstępnie zdefiniowaną rolę nie odpowiadają potrzebom, można zdefiniować własne roli. Aby uzyskać więcej informacji, zobacz [RBAC: Built-in roles](../role-based-access-control/built-in-roles.md) (Kontrola dostępu oparta na rolach: role wbudowane).

> [!IMPORTANT]
> Jeśli użytkownik ma `Contributor` uprawnienia do płaszczyzny zarządzania magazynu kluczy, użytkownik może udzielić sobie dostępu do płaszczyzny danych przez ustawienie zasad dostępu magazynu kluczy. Należy ściśle kontrolować, kto ma `Contributor` roli dostęp do Twoich magazynów kluczy. Sprawdź, czy tylko upoważnione osoby mogą uzyskać dostęp i Zarządzanie magazynami kluczy, kluczy, wpisów tajnych i certyfikatów.
>

<a id="data-plane-access-control"></a> 
## <a name="data-plane-and-access-policies"></a>Zasady płaszczyzna i dostęp do danych

Można przyznać dostęp do płaszczyzny danych przez ustawienie zasad dostępu magazynu kluczy dla magazynu kluczy. Aby ustawić zasady dostępu, użytkownika, grupy lub aplikacji musi mieć `Contributor` uprawnienia do płaszczyzny zarządzania dla tego magazynu kluczy.

Można przyznać użytkownika, grupy lub dostęp do aplikacji do wykonywania określonych operacji dotyczących kluczy lub wpisów tajnych w magazynie kluczy. Magazyn kluczy obsługuje maksymalnie 1024 wpisów zasad dostępu dla magazynu kluczy. Aby udzielić dostępu do płaszczyzny danych wielu użytkownikom, Utwórz grupę zabezpieczeń usługi Azure AD i dodawanie użytkowników do tej grupy.

<a id="key-vault-access-policies"></a> Zasady dostępu magazynu kluczy przyznają uprawnienia oddzielnie do kluczy, wpisów tajnych i certyfikatów. Możesz udzielić użytkownikowi dostępu tylko do kluczy, a nie wpisów tajnych. Uprawnienia dostępu do kluczy, wpisów tajnych i certyfikatów są na poziomie magazynu. Zasady dostępu magazynu kluczy nie obsługują szczegółowe, na poziomie obiektu uprawnień, takich jak określony klucz, hasło lub certyfikat. Aby ustawić zasady dostępu magazynu kluczy, użyj [witryny Azure portal](https://portal.azure.com/), [wiersza polecenia platformy Azure](../cli-install-nodejs.md), [programu Azure PowerShell](/powershell/azureps-cmdlets-docs), lub [interfejsy API REST zarządzania magazyn klucz](https://msdn.microsoft.com/library/azure/mt620024.aspx).

> [!IMPORTANT]
> Zasady dostępu magazynu kluczy są stosowane na poziomie magazynu. Jeśli użytkownikowi udzielono uprawnień do tworzenia i usuwania kluczy, można wykonać te operacje na wszystkie klucze, w tym magazynie kluczy.
>

Możesz ograniczyć dostęp do płaszczyzny danych za pomocą [punkty końcowe usługi sieci wirtualnej dla usługi Azure Key Vault](key-vault-overview-vnet-service-endpoints.md). Można skonfigurować [zapory i reguł sieci wirtualnej](key-vault-network-security.md) dla dodatkową warstwę zabezpieczeń.

## <a name="example"></a>Przykład

W tym przykładzie pracujemy nad aplikacji korzystającej z certyfikatu protokołu SSL, usługi Azure Storage do przechowywania danych oraz klucza RSA 2048-bitowych dla operacji podpisywania. Nasza aplikacja działa w maszynie wirtualnej platformy Azure (VM) (lub zestawu skalowania maszyn wirtualnych). Możemy użyć magazynu kluczy do przechowywania wpisów tajnych aplikacji. Firma Microsoft może przechowywać certyfikatu uruchamiania, który jest używany przez aplikację do uwierzytelniania za pomocą usługi Azure AD.

Konieczny jest dostęp do następujących przechowywane klucze i wpisy tajne:
- **Certyfikat SSL**: Używane do obsługi protokołu SSL.
- **Klucz magazynu**: Umożliwia dostęp do konta magazynu.
- **RSA 2048 bitowego klucza**: Używane dla operacji podpisywania.
- **Certyfikatu uruchamiania**: Używany do uwierzytelniania za pomocą usługi Azure AD. Po nadaniu prawa dostępu możemy pobrania klucza magazynu i użycia klucza RSA do podpisywania.

Należy zdefiniować następujące role, aby określić, kto zarządzanie, wdrażanie i inspekcję naszej aplikacji:
- **Zespół ds. zabezpieczeń**: Pracownicy działu IT z biura dyrektora CSO (Dyrektor ds. bezpieczeństwa) lub podobnych współautorów. Zespół ds. zabezpieczeń jest odpowiedzialny za właściwe bezpieczne przechowywanie wpisów tajnych. Wpisy tajne mogą obejmować certyfikaty SSL, klucze RSA do podpisywania, parametry połączenia i klucze konta magazynu.
- **Deweloperów i operatorów**: Pracowników, którzy opracowywanie aplikacji i wdrożyć ją na platformie Azure. Członkowie zespołu nie należą do pracowników działu zabezpieczeń. Nie mają dostępu do poufnych danych, takich jak certyfikaty SSL i klucze RSA. Tylko aplikacji wdrażanych powinien mieć dostęp do poufnych danych.
- **Audytorzy**: Ta rola jest dla współautorów, którzy nie są członkami rozwoju lub pracowników ogólnego działu IT. Przejrzyj ich użycia i obsługi certyfikatów, kluczy i wpisów tajnych w celu zapewnienia zgodności ze standardami zabezpieczeń. 

Istnieje inna rola, który znajduje się poza zakresem naszej aplikacji: subskrypcji (lub grupy zasobów) administratora. Administrator subskrypcji konfiguruje początkowe uprawnienia dostępu dla zespołu ds. zabezpieczeń. Mogą udzielać dostępu do zespołu zabezpieczeń za pomocą grupy zasobów, który zawiera zasoby wymagane przez aplikację.

Potrzebujemy autoryzować następujące operacje nasze role:

**Zespół ds. zabezpieczeń**
- Tworzenie magazynów kluczy.
- Włączenie rejestrowania usługi Key Vault.
- Dodawanie kluczy i wpisów tajnych.
- Tworzenie kopii zapasowych kluczy na potrzeby odzyskiwania po awarii.
- Ustawianie zasad dostępu usługi Key Vault, aby udzielić uprawnień użytkownikom i aplikacjom dla określonych operacji.
- Okresowo przerzucenie kluczy i wpisów tajnych.

**Deweloperów i operatorów**
- Pobieranie odwołań z zespołu zabezpieczeń ładowania początkowego i certyfikatów SSL (odcisków palca), klucza magazynu (identyfikator URI klucza tajnego) i klucz RSA (identyfikator URI klucza) do podpisywania.
- Tworzenie i wdrażanie aplikacji do programowego dostępu do kluczy i wpisów tajnych.

**Audytorzy**
- Przejrzyj dzienniki usługi Key Vault w celu potwierdzenia prawidłowego użycia kluczy i wpisów tajnych i zgodności ze standardami zabezpieczeń danych.

Poniższa tabela podsumowuje uprawnienia dostępu dla naszych ról i aplikacji. 

| Rola | Uprawnienia do płaszczyzny zarządzania | Uprawnienia do płaszczyzny danych |
| --- | --- | --- |
| Zespół ds. zabezpieczeń | Współautor magazynu kluczy | Klucze: tworzenie kopii zapasowej, tworzenia, usuwania, Pobierz, importowanie, listy, Przywracanie<br>Wpisy tajne: wszystkie operacje |
| Deweloperzy i&nbsp;operatorów | Uprawnienia do wdrażania usługi Key Vault<br><br> **Uwaga**: To uprawnienie umożliwia wdrożone maszyny wirtualne można pobrać Wpisy tajne z magazynu kluczy. | Brak |
| Audytorzy | Brak | Klucze: wyświetlanie<br>Wpisy tajne: wyświetlanie<br><br> **Uwaga**: To uprawnienie umożliwia audytorów do atrybutów (tagi, daty aktywacji, daty wygaśnięcia) klucze i wpisy tajne nie są emitowane w dziennikach inspekcji. |
| Aplikacja | Brak | Klucze: podpisywanie<br>Wpisy tajne: pobieranie |

Trzy role zespołu muszą mieć dostęp do innych zasobów oraz uprawnień usługi Key Vault. Aby wdrożyć maszyny wirtualne (lub funkcji Web Apps w usłudze Azure App Service), deweloperów i operatorów potrzeby `Contributor` dostęp do tych typów zasobów. Audytorzy muszą mieć dostęp do konta magazynu, w którym są przechowywane dzienniki magazynu kluczy.

Aby uzyskać więcej informacji na temat sposobu wdrażania certyfikatów programowego dostępu do kluczy i wpisów tajnych, zobacz następujące zasoby:
- Dowiedz się, jak [wdrażanie certyfikatów na maszynach wirtualnych z magazynu kluczy zarządzanych przez klienta](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/) (wpis na blogu).
- Pobierz [przykłady klienta usługi Azure Key Vault](https://www.microsoft.com/download/details.aspx?id=45343). Ta zawartość ilustruje sposób użycia certyfikatu uruchamiania do uwierzytelniania w usłudze Azure AD do dostępu do magazynu kluczy.

Większość uprawnień dostępu można przyznać za pomocą witryny Azure portal. Aby udzielić szczegółowych uprawnień, można użyć programu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure.

Fragmenty kodu programu PowerShell w tej sekcji są tworzone za pomocą następujące założenia:
- Administrator usługi Azure AD utworzył grupy zabezpieczeń, aby przedstawić trzy role: Zespół ds. zabezpieczeń firmy Contoso, Contoso App DevOps i audytorzy aplikacji Contoso. Administrator dodał użytkowników do odpowiednich grup.
- Wszystkie zasoby znajdują się w **ContosoAppRG** grupy zasobów.
- Dzienniki magazynu kluczy są przechowywane w **contosologstorage** konta magazynu. 
- **ContosoKeyVault** usługi key vault i **contosologstorage** konta magazynu znajdują się w tej samej lokalizacji platformy Azure.

Administrator subskrypcji przypisuje `key vault Contributor` i `User Access Administrator` ról do zespołu zabezpieczeń. Te role Zezwalaj na zespół ds. zabezpieczeń Zarządzanie dostępem do innych zasobów i magazynów kluczy, które w **ContosoAppRG** grupy zasobów.

```powershell
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

Zespół ds. zabezpieczeń utworzenie magazynu kluczy i konfiguruje rejestrowanie i uprawnienia dostępu. Aby uzyskać szczegółowe informacje o uprawnieniach zasad dostępu magazynu kluczy, zobacz [klucze usługi Azure Key Vault, klucze tajne i certyfikaty](about-keys-secrets-and-certificates.md).

```powershell
# Create a key vault and enable logging
$sa = Get-AzStorageAccount -ResourceGroup ContosoAppRG -Name contosologstorage
$kv = New-AzKeyVault -Name ContosoKeyVault -ResourceGroup ContosoAppRG -SKU premium -Location 'westus' -EnabledForDeployment
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent

# Set up data plane permissions for the Contoso Security Team role
Set-AzKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -PermissionsToKeys backup,create,delete,get,import,list,restore -PermissionsToSecrets get,list,set,delete,backup,restore,recover,purge

# Set up management plane permissions for the Contoso App DevOps role
# Create the new role from an existing role
$devopsrole = Get-AzRoleDefinition -Name "Virtual Machine Contributor"
$devopsrole.Id = $null
$devopsrole.Name = "Contoso App DevOps"
$devopsrole.Description = "Can deploy VMs that need secrets from a key vault"
$devopsrole.AssignableScopes = @("/subscriptions/<SUBSCRIPTION-GUID>")

# Add permissions for the Contoso App DevOps role so members can deploy VMs with secrets deployed from key vaults
$devopsrole.Actions.Add("Microsoft.KeyVault/vaults/deploy/action")
New-AzRoleDefinition -Role $devopsrole

# Assign the new role to the Contoso App DevOps security group
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso App Devops')[0].Id -RoleDefinitionName "Contoso App Devops" -ResourceGroupName ContosoAppRG

# Set up data plane permissions for the Contoso App Auditors role
Set-AzKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzADGroup -SearchString 'Contoso App Auditors')[0].Id -PermissionsToKeys list -PermissionsToSecrets list
```

Można przypisać tylko do subskrypcji są nasze zdefiniowanych ról niestandardowych gdzie **ContosoAppRG** zostanie utworzona grupa zasobów. Aby użyć roli niestandardowej dla innych projektów w innych subskrypcjach, należy dodać inne subskrypcje do zakresu roli.

Dla naszych pracownicy działu DevOps, przypisanie roli niestandardowej dla usługi key vault `deploy/action` uprawnienie jest ograniczone do grupy zasobów. Tylko maszyny wirtualne utworzone w **ContosoAppRG** grupę zasobów będą miały dostęp do wpisów tajnych (protokołu SSL i certyfikatów bootstrap). Maszyny wirtualne utworzone w innych grupach zasobów przez członka DevOps nie może uzyskać dostępu tych kluczy tajnych, nawet wtedy, gdy maszyna wirtualna ma tajny identyfikatorów URI.

Naszym przykładzie przedstawiono prosty scenariusz. Rzeczywistych scenariuszach może być bardziej skomplikowane. Uprawnienia można dostosować do własnego magazynu kluczy, w zależności od potrzeb. Firma Microsoft zakłada, że zespół ds. zabezpieczeń zapewnia klucza i wpisu tajnego odwołania (identyfikatory URI i odciski palca), które są używane przez pracownicy działu DevOps w swoich aplikacjach. Deweloperów i operatorów nie wymagają żadnych dostęp do płaszczyzny danych. Skupiliśmy się na sposób zabezpieczania własnego magazynu kluczy. Zapewniają podobne uwagi podczas zabezpieczania [maszyn wirtualnych z systemem](https://azure.microsoft.com/services/virtual-machines/security/), [kont magazynu](../storage/common/storage-security-guide.md)i innych zasobów platformy Azure.

> [!NOTE]
> Ten przykład pokazuje, jak dostęp do usługi Key Vault jest zablokowana w środowisku produkcyjnym. Deweloperzy powinni mieć własne subskrypcji lub grupy zasobów z pełnymi uprawnieniami do zarządzania ich magazynów, maszyny wirtualne i konta magazynu, gdzie opracowują aplikacje.

Zalecane jest skonfigurowanie dodatkowych bezpiecznego dostępu do magazynu kluczy przez [Konfigurowanie usługi Key Vault zapory i sieci wirtualne](key-vault-network-security.md).

## <a name="resources"></a>Zasoby

* [Usługa Azure AD RBAC](../role-based-access-control/role-assignments-portal.md)

* [RBAC: Wbudowane role](../role-based-access-control/built-in-roles.md)

* [Omówienie wdrażania usługi Resource Manager oraz wdrażania klasycznego](../azure-resource-manager/resource-manager-deployment-model.md) 

* [Zarządzanie funkcją RBAC przy użyciu programu Azure PowerShell](../role-based-access-control/role-assignments-powershell.md) 

* [Zarządzanie funkcją RBAC przy użyciu interfejsu API REST](../role-based-access-control/role-assignments-rest.md)

* [RBAC na platformie Microsoft Azure](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

    Ta 2015 Microsoft Ignite konferencji wideo omawia zarządzania dostępem i możliwości raportowania w systemie Azure. Analizuje ona również najlepsze rozwiązania dotyczące zabezpieczania dostępu do subskrypcji platformy Azure przy użyciu usługi Azure AD.

* [Autoryzowanie dostępu do aplikacji sieci web przy użyciu protokołu OAuth 2.0 i usługi Azure AD](../active-directory/develop/v1-protocols-oauth-code.md)

* [Zarządzanie usługą Key Vault interfejsów API REST](https://msdn.microsoft.com/library/azure/mt620024.aspx)

    Dokumentacja interfejsów API REST do zarządzania klucz magazynu programowo, włącznie z ustawieniem zasad dostępu magazynu kluczy.

* [Interfejsy API REST usługi Key Vault](https://msdn.microsoft.com/library/azure/dn903609.aspx)

* [Kontrola dostępu do kluczy](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl)
  
* [Kontrola dostępu do kluczy tajnych](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl)
  
* [Ustaw](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) i [Usuń](/powershell/module/az.keyvault/Remove-azKeyVaultAccessPolicy) zasad dostępu magazynu kluczy przy użyciu programu PowerShell.
  
## <a name="next-steps"></a>Kolejne kroki

Konfigurowanie [usługi Key Vault zapory i sieci wirtualne](key-vault-network-security.md).

Samouczek — Wprowadzenie dla administratora, zobacz [co to jest usługa Azure Key Vault?](key-vault-overview.md).

Aby uzyskać więcej informacji na temat rejestrowania użycia usługi Key Vault, zobacz [Funkcja rejestrowania usługi Azure Key Vault](key-vault-logging.md).

Aby uzyskać więcej informacji na temat używania kluczy i wpisów tajnych za pomocą usługi Azure Key Vault, zobacz [o kluczach i wpisach tajnych](https://msdn.microsoft.com/library/azure/dn903623.aspx).

Jeśli masz pytania dotyczące usługi Key Vault, odwiedź stronę [forów](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).
