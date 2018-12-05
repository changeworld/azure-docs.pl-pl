---
title: Zabezpieczanie własnego magazynu kluczy platformy Azure | Dokumentacja firmy Microsoft
description: Zarządzaj uprawnieniami dostępu do usługi Azure Key Vault, klucze i wpisy tajne. Obejmuje model uwierzytelniania i autoryzacji dla usługi Key Vault i sposób zabezpieczania własnego magazynu kluczy.
services: key-vault
documentationcenter: ''
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: e5b4e083-4a39-4410-8e3a-2832ad6db405
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: ambapat
ms.openlocfilehash: 67f24bbccdd2dcf5cca09e09557d7ebebd0a5c2d
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2018
ms.locfileid: "52891082"
---
# <a name="secure-your-key-vault"></a>Zabezpieczanie własnego magazynu kluczy
Usługa Azure Key Vault to usługa w chmurze, która zabezpiecza klucze szyfrowania i wpisy tajne (takie jak certyfikaty, parametry połączenia i hasła). Ponieważ te dane są poufne i krytyczne dla działania, należy zabezpieczyć dostęp do Twoich magazynów kluczy, dzięki czemu tylko autoryzowane aplikacje i użytkowników. Ten artykuł zawiera omówienie modelu dostępu do usługi Key Vault. Wyjaśnia uwierzytelnianie i autoryzację, a w tym artykule opisano sposób zabezpieczania dostępu.

## <a name="overview"></a>Przegląd
Dostęp do magazynu kluczy jest kontrolowany za pośrednictwem dwóch oddzielnych interfejsów: płaszczyzny zarządzania i płaszczyzny danych. 
**Płaszczyzna zarządzania** zajmuje się zarządzanie magazynem, na przykład — tworzenie magazynu, aktualizowanie magazynu, usuwanie magazynu. 
**Płaszczyzna danych** ofertami przy użyciu kluczy tajnych w magazynie, który jest utworzony, aktualizowanie, usuwanie i odczytu wpisu tajnego w magazynie. W przypadku obu płaszczyzn odpowiednie uwierzytelnianie i autoryzacja są wymagane zanim obiekt wywołujący (użytkownik lub aplikacja) mogą uzyskać dostęp do magazynu kluczy. Uwierzytelnianie ustala tożsamość elementu wywołującego, podczas gdy autoryzacja określa operacje, które może wykonywać obiekt wywołujący.

Na potrzeby uwierzytelniania płaszczyzna zarządzania i płaszczyzna danych używają usługi Azure Active Directory. Na potrzeby autoryzacji płaszczyzna zarządzania używa kontroli dostępu opartej na rolach (RBAC), natomiast płaszczyzna danych używa zasad dostępu magazynu kluczy.

Oto krótki przegląd omówionych tematów:

[Uwierzytelnianie przy użyciu usługi Azure Active Directory](#authentication-using-azure-active-directory) — w tej sekcji opisano, jak obiekt wywołujący uwierzytelnia się za pomocą usługi Azure Active Directory w celu uzyskania dostępu do magazynu kluczy za pośrednictwem płaszczyzny zarządzania i płaszczyzny danych. 

W przypadku uwierzytelniania obu płaszczyzn usługa Azure Active Directory (Azure AD). Na potrzeby autoryzacji płaszczyzna zarządzania używa kontroli dostępu opartej na rolach (RBAC), natomiast płaszczyzna danych używa zasad dostępu magazynu kluczy.

## <a name="authenticate-by-using-azure-active-directory"></a>Uwierzytelnianie przy użyciu usługi Azure Active Directory
Po utworzeniu magazynu kluczy w subskrypcji platformy Azure ma automatycznie skojarzone z dzierżawą usługi Azure AD dla subskrypcji. Wszystkie obiekty wywołujące muszą być zarejestrowane w tej dzierżawie i muszą zostać uwierzytelnione na dostęp do magazynu kluczy. To wymaganie dotyczy zarówno płaszczyzny i dane dostępu do płaszczyzny zarządzania. W obu przypadkach aplikacja może uzyskiwać dostęp do usługi Key Vault na dwa sposoby:

* **dostęp użytkownika i aplikacji**: używany z aplikacji uzyskujących dostęp do usługi Key Vault w imieniu zalogowanego użytkownika. Program Azure PowerShell i witryny Azure portal to przykłady tego typu dostępu. Istnieją dwa sposoby udzielania dostępu użytkownikom: 
  - Dostęp do usługi Key Vault z poziomu dowolnej aplikacji.
  - Dostęp do usługi Key Vault tylko wtedy, gdy korzystają z określonej aplikacji (nazywane złożone tożsamości).

* **dostęp tylko do aplikacji**: używany z aplikacjami, które działały jako demon usługi lub zadania w tle. Tożsamości aplikacji udzielany jest dostęp do magazynu kluczy.

W przypadku obu typów aplikacji Aplikacja uwierzytelnia się za pomocą usługi Azure AD przy użyciu dowolnej z [obsługiwanych metod uwierzytelniania](../active-directory/develop/authentication-scenarios.md)i uzyskuje token. Metodę uwierzytelniania używaną zależy od typu aplikacji. Następnie aplikacja używa tego tokenu i wysyła żądanie interfejsu API REST do usługi Key Vault. Żądania płaszczyzny zarządzania są przesyłane za pośrednictwem punktu końcowego usługi Azure Resource Manager. Podczas uzyskiwania dostępu do płaszczyzny danych, aplikacja komunikuje się bezpośrednio do punktu końcowego usługi Key Vault. Aby uzyskać więcej informacji, zobacz [całego przepływu uwierzytelniania](../active-directory/develop/v1-protocols-oauth-code.md). 

Uzyskuje dostęp do nazwy zasobu, dla której aplikacja żąda tokenu zależy które warstwy aplikacji. Nazwa zasobu jest punkt końcowy płaszczyzny zarządzania lub punkt końcowy płaszczyzny danych, w zależności od środowiska platformy Azure. Aby uzyskać więcej informacji zobacz tabelę w dalszej części tego artykułu.

Jeden pojedynczy mechanizm uwierzytelniania obu płaszczyzn ma kilka zalet:

* Organizacje mogą centralnie kontrolować dostęp do wszystkich magazynów kluczy w organizacji.
* Jeśli użytkownik pozostawi, użytkownik utraci natychmiast dostęp do wszystkich magazynów kluczy w organizacji.
* Organizacje mogą dostosowywać uwierzytelnianie za pomocą opcji w usłudze Azure AD (na przykład włączyć uwierzytelnianie wieloskładnikowe bezpieczeństwo).

## <a name="the-management-plane-and-the-data-plane"></a>Płaszczyzna zarządzania i płaszczyzna danych
Płaszczyzna zarządzania umożliwia zarządzanie usługi Key Vault, sam. Dotyczy to również operacji, takich jak zarządzanie atrybutów i ustawienie zasady dostępu do płaszczyzny danych. Płaszczyzna danych umożliwia dodawanie, usuwanie, modyfikowanie i używanie kluczy, wpisów tajnych i certyfikatów przechowywanych w usłudze Key Vault.

Dostęp do interfejsów płaszczyzny płaszczyzna i danych zarządzania za pośrednictwem różnych punktów końcowych, wymienione w poniższej tabeli. Druga kolumna w tabeli opisano nazwy DNS dla tych punktów końcowych w różnych środowiskach Azure. W trzeciej kolumnie opisano operacje, które można wykonywać na danej płaszczyźnie dostępu. Każda płaszczyzna dostępu ma również swój własny mechanizm kontroli dostępu. Kontrola dostępu do płaszczyzny zarządzania można ustawić za pomocą kontroli dostępu opartej na rolach usługi Azure Resource Manager (RBAC). Kontrola dostępu do płaszczyzny danych można ustawić za pomocą zasad dostępu magazynu kluczy.

| Płaszczyzna dostępu | Punkty końcowe dostępu | Operacje | Mechanizm kontroli dostępu |
| --- | --- | --- | --- |
| Płaszczyzna zarządzania |**Cały świat:**<br> management.azure.com:443<br><br> **Usługa Azure China 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Wersja platformy Azure dla administracji USA:**<br> management.usgovcloudapi.net:443<br><br> **Niemiecka wersja platformy Azure:**<br> management.microsoftazure.de:443 |Tworzenie/odczyt/aktualizowanie/usuwanie magazynu kluczy <br> Ustawienie zasad dostępu do usługi Key Vault<br>Ustawianie tagów dla usługi Key Vault |RBAC usługi Azure Resource Manager |
| Płaszczyzna danych |**Cały świat:**<br> &lt;nazwa_magazynu&gt;.vault.azure.net:443<br><br> **Usługa Azure China 21Vianet:**<br> &lt;nazwa_magazynu&gt;.vault.azure.cn:443<br><br> **Wersja platformy Azure dla administracji USA:**<br> &lt;nazwa_magazynu&gt;.vault.usgovcloudapi.net:443<br><br> **Niemiecka wersja platformy Azure:**<br> &lt;nazwa_magazynu&gt;.vault.microsoftazure.de:443 |Klucze: odszyfrować, Encrypt, UnwrapKey, WrapKey, upewnij się, logowania, Get, listy, aktualizacji, tworzenia, importowania, Delete, kopii zapasowej, przywracania<br><br> Dla wpisów tajnych: Pobierz, Lista, Ustaw, Usuń |Zasady dostępu magazynu kluczy |

Zarządzanie płaszczyzna i danych płaszczyzna kontroli dostępu działać niezależnie. Na przykład jeśli użytkownik chce udzielić aplikacji dostępu do używania kluczy w magazynie kluczy, wystarczy przyznać dostęp do płaszczyzny danych. Możesz udzielić dostępu za pomocą zasad dostępu magazynu kluczy. Z drugiej strony użytkownika, który można odczytać właściwości usługi Key Vault i tagi, ale nie dostęp do danych (kluczy, wpisów tajnych lub certyfikatów), wymaga dostępu do płaszczyzny zarządzania. Możesz udzielić dostępu, przypisując dostęp do odczytu dla użytkownika przy użyciu RBAC.

## <a name="management-plane-access-control"></a>Kontrola dostępu do płaszczyzny zarządzania
Płaszczyzna zarządzania składa się z operacji, które wpływają na samym magazynem kluczy, takie jak:

- Tworzenie lub usuwanie magazynu kluczy.
- Pobieranie listy magazynów w ramach subskrypcji.
- Pobieranie właściwości usługi Key Vault (na przykład jednostki SKU i tagi).
- Ustawianie zasad dostępu magazynu kluczy, które kontrolki użytkownika i aplikacji dostępu do kluczy i wpisów tajnych.

Kontrola dostępu do płaszczyzny zarządzania korzysta z funkcji RBAC.  

### <a name="role-based-access-control-rbac"></a>Kontrola dostępu oparta na rolach (RBAC)
Każda subskrypcja platformy Azure znajduje się wystąpienie usługi Azure AD. Możesz udzielić dostępu użytkownikom, grupom i aplikacjom z tego katalogu do zarządzania zasobami w subskrypcji platformy Azure, które używają modelu wdrażania usługi Azure Resource Manager. Ten typ kontroli dostępu jest nazywane RBAC. Do zarządzania tym dostępem można użyć witryny [Azure Portal](https://portal.azure.com/), [narzędzi interfejsu wiersza polecenia platformy Azure](../cli-install-nodejs.md), [programu PowerShell](/powershell/azureps-cmdlets-docs) lub [interfejsów API REST usługi Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn906885.aspx).

Tworzenie magazynu kluczy w grupie zasobów i kontrolować dostęp do płaszczyzny zarządzania za pomocą usługi Azure AD. Na przykład można przyznać użytkownikom lub grupie możliwość zarządzania magazynami kluczy w grupie zasobów.

Przypisując odpowiednie role RBAC mogą przyznawać dostęp użytkownikom, grupom i aplikacjom, które w określonym zakresie. Na przykład aby udzielić dostępu użytkownika do zarządzania magazynami kluczy, przypisaniu wstępnie zdefiniowaną rolę Współautor magazynu klucz do danego użytkownika, w określonym zakresie. Zakres w tym przypadku będzie subskrypcją, grupą zasobów lub określonego magazynu kluczy. Rola przypisana na poziomie subskrypcji ma zastosowanie do wszystkich grup zasobów i zasobów w ramach tej subskrypcji. Rola przypisana na poziomie grupy zasobów ma zastosowanie do wszystkich zasobów w danej grupie zasobów. Rola przypisana dla określonego zasobu ma zastosowanie do tego zasobu. Istnieje kilka wstępnie zdefiniowanych ról (zobacz [RBAC: wbudowane role](../role-based-access-control/built-in-roles.md)). Jeśli wstępnie zdefiniowaną rolę nie odpowiadają potrzebom, można zdefiniować własne roli.

> [!IMPORTANT]
> Należy pamiętać, że jeśli użytkownik ma uprawnienia do płaszczyzny zarządzania usługi Key Vault, może udzielić sobie Współautor dostęp do płaszczyzny danych przez ustawienie zasad dostępu magazynu kluczy. Dlatego należy ściśle kontrolować, kto ma dostęp współautora do magazynów kluczy. Sprawdź, czy tylko upoważnione osoby mogą uzyskać dostęp i Zarządzanie magazynami kluczy, kluczy, wpisów tajnych i certyfikatów.
> 
> 

## <a name="data-plane-access-control"></a>Kontrola dostępu do płaszczyzny danych
Operacje płaszczyzny danych usługi Key Vault dotyczą przechowywanych obiektów, takich jak klucze, wpisy tajne i certyfikaty. Kluczowe operacje obejmują tworzenie, importowanie, aktualizowanie, lista, tworzenie kopii zapasowej i przywracanie kluczy. Operacje kryptograficzne znak, sprawdź, szyfrowania, odszyfrowywania, opakowywanie, Odkodowywanie, ustawić tagi i ustawić innych atrybutów kluczy. Podobnie, operacje na klucze tajne to między innymi get, set, listy, Usuń.

Można przyznać dostęp do płaszczyzny danych przez ustawienie zasad dostępu magazynu kluczy. Użytkownika, grupy lub aplikacji musi mieć uprawnienia współautora do płaszczyzny zarządzania dla magazynu kluczy można było ustawić zasady dostępu dla tego magazynu kluczy. Można przyznać użytkownika, grupy lub dostęp do aplikacji do wykonywania określonych operacji dotyczących kluczy lub wpisów tajnych w magazynie kluczy. Magazyn kluczy obsługuje maksymalnie 1024 wpisów zasad dostępu magazynu kluczy. Aby udzielić dostępu do płaszczyzny danych wielu użytkownikom, Utwórz grupę zabezpieczeń usługi Azure AD i dodawanie użytkowników do tej grupy.

### <a name="key-vault-access-policies"></a>Zasady dostępu magazynu kluczy
Zasady dostępu magazynu kluczy przyznają oddzielnie uprawnienia do kluczy, wpisów tajnych i certyfikatów. Na przykład można udzielić użytkownikowi dostępu tylko do kluczy i nie uprawnień do wpisów tajnych. Uprawnienia do dostępu do kluczy, wpisów tajnych lub certyfikatów są na poziomie magazynu. Zasady dostępu magazynu kluczy nie obsługuje uprawnienia szczegółowe, na poziomie obiektu, takie jak określony klucz, hasło lub certyfikat. Możesz użyć [witryny Azure portal](https://portal.azure.com/), [narzędzia wiersza polecenia platformy Azure](../cli-install-nodejs.md), [PowerShell](/powershell/azureps-cmdlets-docs), lub [interfejsy API REST zarządzania Key Vault](https://msdn.microsoft.com/library/azure/mt620024.aspx) można ustawić zasady dostępu dla magazynu kluczy.

> [!IMPORTANT]
> Zasady dostępu magazynu kluczy są stosowane na poziomie magazynu. Na przykład jeśli użytkownikowi udzielono uprawnień do tworzenia i usuwania kluczy, będzie on mógł wykonywać te operacje na wszystkich kluczach w tym magazynie kluczy.

Oprócz używania zasad dostępu, można ograniczyć dostęp do płaszczyzny danych przy użyciu [punkty końcowe usługi sieci wirtualnej dla usługi Azure Key Vault](key-vault-overview-vnet-service-endpoints.md). Możesz skonfigurować [zapory i reguł sieci wirtualnej](key-vault-network-security.md) dla dodatkową warstwę zabezpieczeń.

## <a name="example"></a>Przykład
Załóżmy, że projektujesz aplikację, która używa certyfikatu dla protokołu SSL, usługi Azure Storage do przechowywania danych i klucza szyfrowania RSA 2048-bitowego dla operacji podpisywania. Załóżmy, że ta aplikacja jest uruchomiona w maszynie wirtualnej platformy Azure (lub zestawu skalowania maszyn wirtualnych). Używanie magazynu kluczy do przechowywania wszystkich wpisów tajnych aplikacji i przechowywania certyfikatu uruchamiania, używanych przez aplikację do uwierzytelniania za pomocą usługi Azure AD.

Poniżej przedstawiono podsumowanie typów kluczy i kluczy tajnych przechowywanych:

* **Certyfikat SSL**: używane do obsługi protokołu SSL.
* **Klucz magazynu**: używany do uzyskiwania dostępu do konta magazynu.
* **RSA 2048-bitowy klucz**: używane dla operacji podpisywania.
* **Certyfikatu uruchamiania**: używany do uwierzytelniania za pomocą usługi Azure AD. Po nadaniu prawa dostępu można pobrać klucza magazynu i użycia klucza RSA do podpisywania.

Teraz określmy osoby, które są wdrażanie i zarządzanie inspekcji tej aplikacji. W tym przykładzie użyjemy trzech ról.

* **Zespół ds. zabezpieczeń**: pracownicy działu zwykle IT z biura dyrektora CSO (Dyrektor ds. bezpieczeństwa) lub równoważnej. Ten zespół jest odpowiedzialny za właściwe bezpieczne przechowywanie wpisów tajnych. Przykłady obejmują certyfikaty SSL, klucze RSA używane do podpisywania, parametry połączenia i klucze konta magazynu.
* **Deweloperzy/Operatorzy**: spamerzy opracowywanie aplikacji, a następnie wdrożyć go na platformie Azure. Zazwyczaj nie są częścią zespołu ds. zabezpieczeń i dlatego nie powinny mieć dostępu do poufnych danych, takich jak certyfikaty SSL i klucze RSA. Tylko wdrażana przez nich aplikacja powinna mieć dostęp do tych obiektów.
* **Audytorzy**: zazwyczaj inny zestaw osób, odizolowany od deweloperów i pracowników ogólnego działu IT. Ich zadaniem jest przeglądanie użycia i obsługi certyfikatów, kluczy i wpisów tajnych w celu zapewnienia zgodności ze standardami zabezpieczeń. 

Istnieje jeszcze jedna rola, która znajduje się poza zakresem tej aplikacji, ale odpowiedni w tym miejscu mają być wymienione. Ta rola jest subskrypcji (lub grupy zasobów) administratora. Administrator subskrypcji konfiguruje początkowe uprawnienia dostępu dla zespołu ds. zabezpieczeń. Administrator subskrypcji udziela dostępu do zespołu ds. zabezpieczeń, za pomocą grupy zasobów, która zawiera zasoby wymagane przez tę aplikację.

Teraz zobaczmy, jakie akcje wykonują poszczególne role w kontekście tej aplikacji.

* **Zespół ds. zabezpieczeń**
  * Tworzy magazynów kluczy.
  * Włącza funkcję rejestrowania usługi Key Vault.
  * Dodaje kluczy/wpisów tajnych.
  * Tworzy kopii zapasowych kluczy na potrzeby odzyskiwania po awarii.
  * Ustawia zasady dostępu do usługi Key Vault, aby udzielić uprawnień użytkownikom i aplikacjom do wykonywania określonych operacji.
  * Okresowo przedstawia kluczy/wpisów tajnych.
* **Deweloperzy/operatorzy**
  * Pobieranie odwołań do ładowania początkowego i certyfikatów SSL (odcisków palca), klucza magazynu (identyfikatora URI wpisu tajnego) i klucza podpisywania (identyfikator URI klucza) od zespołu zabezpieczeń.
  * Tworzenie i wdrażanie aplikacji, który uzyskuje dostęp do kluczy i wpisów tajnych programowo.
* **Audytorzy**
  * Przeglądanie dzienników użycia w celu potwierdzenia prawidłowego użycia kluczy/wpisów tajnych i zgodności ze standardami zabezpieczeń danych.

Teraz zobaczmy, jakie uprawnienia są wymagane dla poszczególnych ról i aplikacji, aby wykonywać przydzielone im zadania. 

| Rola użytkownika | Uprawnienia do płaszczyzny zarządzania | Uprawnienia do płaszczyzny danych |
| --- | --- | --- |
| Zespół ds. zabezpieczeń |Współautor magazynu kluczy |Klucze: wykonywanie kopii zapasowej, tworzenie, usuwanie, pobieranie, importowanie, wyświetlanie, przywracanie <br> Wpisy tajne: wszystkie |
| Deweloperzy/Operatorzy |Uprawnienia, do wdrażania usługi Key Vault, aby maszyny wirtualne, które są wdrażanie mogły pobierać wpisy tajne z magazynu kluczy. |Brak |
| Audytorzy |Brak |Klucze: wyświetlanie<br>Wpisy tajne: wyświetlanie |
| Aplikacja |Brak |Klucze: podpisywanie<br>Wpisy tajne: pobieranie |

> [!NOTE]
> Audytorzy musi uprawnienia listy kluczy i wpisów tajnych, aby móc dokonywać inspekcji atrybutów kluczy i wpisów tajnych, które nie są emitowane w dziennikach. Te atrybuty zawierają tagów, aktywacji i daty wygaśnięcia.
> 
> 

Oprócz uprawnień usługi Key Vault wszystkie trzy role potrzebują również dostępu do innych zasobów. Na przykład aby można było wdrożyć maszyny wirtualne (lub funkcji Web Apps w usłudze Azure App Service), deweloperów i operatorów również potrzebne prawa dostępu współautora do tych typów zasobów. Audytorzy muszą mieć dostęp do konta magazynu, w którym są przechowywane dzienniki magazynu kluczy.

Ponieważ ten artykuł koncentruje się na zabezpieczaniu dostępu do magazynu kluczy, firma Microsoft pokazują tylko pojęć odnoszących się do tego tematu. Szczegóły dotyczące wdrażania certyfikatów i uzyskiwanie dostępu do kluczy i wpisów tajnych programowo są omówione w innym miejscu. Na wystąpienie:

- Wdrażanie certyfikatów przechowywanych w usłudze Key Vault do maszyn wirtualnych zostało omówione w [wdrażanie certyfikatów na maszynach wirtualnych z zarządzanego przez klienta usługi Key Vault](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/) (wpis na blogu).
- [Pobierania próbek klienta usługi Azure Key Vault](https://www.microsoft.com/download/details.aspx?id=45343) ilustruje sposób użycia certyfikatu uruchamiania do uwierzytelniania w usłudze Azure AD do dostępu do magazynu kluczy.

Większość uprawnień dostępu można przyznać za pomocą witryny Azure portal. Celu przyznania szczegółowych uprawnień, może być konieczne użycie programu Azure PowerShell lub interfejsu wiersza polecenia, aby osiągnąć oczekiwany rezultat. 

W poniższych fragmentach kodu programu PowerShell przyjęto następujące założenia:

* Administrator usługi Azure AD utworzył grupy zabezpieczeń, które reprezentują trzy role (zespół ds. zabezpieczeń firmy Contoso, Contoso App Devops i audytorzy aplikacji Contoso). Administrator dodał również użytkowników do grup, do których należą.
* **ContosoAppRG** to grupa zasobów, w której znajdują się wszystkie zasoby. **contosologstorage** to miejsce, w którym przechowywane są dzienniki. 
* Magazyn kluczy **ContosoKeyVault**, a konto magazynu używane dla dzienników usługi Key Vault **contosologstorage**, musi znajdować się w tej samej lokalizacji platformy Azure.

Najpierw administrator subskrypcji przypisuje `key vault Contributor` i `User Access Administrator` ról do zespołu zabezpieczeń. Te role Zezwalaj na zespół ds. zabezpieczeń Zarządzanie dostępem do innych zasobów i Zarządzanie magazynami kluczy, w grupie zasobów ContosoAppRG.

```
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

Poniższy skrypt pokazuje, jak zespół ds. zabezpieczeń można utworzyć magazyn kluczy i konfigurowanie rejestrowania i uprawnienia dostępu. Aby uzyskać szczegółowe informacje o uprawnieniach zasad dostępu magazynu kluczy, zobacz [klucze, wpisy tajne i certyfikaty usługi Azure Key Vault](about-keys-secrets-and-certificates.md).

```
# Create key vault and enable logging
$sa = Get-AzureRmStorageAccount -ResourceGroup ContosoAppRG -Name contosologstorage
$kv = New-AzureRmKeyVault -Name ContosoKeyVault -ResourceGroup ContosoAppRG -SKU premium -Location 'westus' -EnabledForDeployment
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent

# Data plane permissions for Security team
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -PermissionsToKeys backup,create,delete,get,import,list,restore -PermissionsToSecrets get,list,set,delete,backup,restore,recover,purge

# Management plane permissions for Dev/ops
# Create a new role from an existing role
$devopsrole = Get-AzureRmRoleDefinition -Name "Virtual Machine Contributor"
$devopsrole.Id = $null
$devopsrole.Name = "Contoso App Devops"
$devopsrole.Description = "Can deploy VMs that need secrets from key vault"
$devopsrole.AssignableScopes = @("/subscriptions/<SUBSCRIPTION-GUID>")

# Add permission for dev/ops so they can deploy VMs that have secrets deployed from key vaults
$devopsrole.Actions.Add("Microsoft.KeyVault/vaults/deploy/action")
New-AzureRmRoleDefinition -Role $devopsrole

# Assign this newly defined role to Dev ops security group
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso App Devops')[0].Id -RoleDefinitionName "Contoso App Devops" -ResourceGroupName ContosoAppRG

# Data plane permissions for Auditors
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso App Auditors')[0].Id -PermissionsToKeys list -PermissionsToSecrets list
```

Zdefiniowaną rolę niestandardową jest tylko można przypisać do subskrypcji gdzie `ContosoAppRG` zostanie utworzona grupa zasobów. Jeśli te same role niestandardowe będą używane dla innych projektów w innych subskrypcjach, jej zakresu może być dodane więcej subskrypcji.

Przypisanie roli niestandardowej dla deweloperów i operatorów uprawnienia "Wdrażanie/Akcja" obejmuje grupę zasobów. Dzięki temu tylko maszyny wirtualne utworzone w grupie zasobów `ContosoAppRG` na dostęp do wpisy tajne (certyfikat SSL i certyfikat uruchamiania). Maszyny wirtualne utworzone w innej grupie zasobów przez deweloperów i operatorów członek zespołu nie mają dostępu do tych kluczy tajnych, nawet jeśli mają one wpisu tajnego identyfikatorów URI.

Ten przykład pokazuje Prosty scenariusz. Rzeczywiste scenariusze mogą być bardziej skomplikowane i uprawnień można dostosować do własnego magazynu kluczy, w zależności od potrzeb. W tym przykładzie przyjęto założenie, że zespół ds. zabezpieczeń zawiera klucza i wpisu tajnego odwołania (identyfikatory URI i odciski palca), które deweloperów i operatorów musi odwoływać się w swoich aplikacjach. Deweloperów i operatorów nie wymagają żadnych dostęp do płaszczyzny danych. Ten przykład koncentruje się na zabezpieczaniu magazynu kluczy. Należy nadać kontu podobne uwagi, aby zabezpieczyć [maszyn wirtualnych z systemem](https://azure.microsoft.com/services/virtual-machines/security/), [kont magazynu](../storage/common/storage-security-guide.md)i innych zasobów platformy Azure.

> [!NOTE]
> Ten przykład pokazuje, w jaki sposób dostępu do magazynu kluczy będzie zabezpieczany w środowisku produkcyjnym. Deweloperzy powinni mieć swoją własną subskrypcję lub grupę zasobów gdzie mają pełne uprawnienia do zarządzania ich magazynów, maszyny wirtualne i konta magazynu gdzie opracowują aplikacje.

Zdecydowanie zaleca się bardziej szczegółowo bezpiecznego dostępu do magazynu kluczy przez [Konfigurowanie usługi Key Vault zapory i sieci wirtualne](key-vault-network-security.md).

## <a name="resources"></a>Zasoby
* [Kontroli dostępu opartej na rolach w usłudze Azure Active Directory](../role-based-access-control/role-assignments-portal.md)
  
* [Kontrola dostępu oparta na rolach (RBAC): wbudowane role](../role-based-access-control/built-in-roles.md)
  
* [Omówienie wdrażania przy użyciu usługi Resource Manager oraz wdrażania klasycznego](../azure-resource-manager/resource-manager-deployment-model.md)
  
* [Zarządzanie kontrolą dostępu opartej na rolach przy użyciu programu Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)
  
* [Zarządzanie kontrolą dostępu opartej na rolach za pomocą interfejsu API REST](../role-based-access-control/role-assignments-rest.md)
  
* [Kontrola dostępu oparta na rolach dla usługi Microsoft Azure z konferencji Ignite](https://channel9.msdn.com/events/Ignite/2015/BRK2707)
  
  Ta 2015 Microsoft Ignite konferencji wideo omawia zarządzania dostępem i możliwości raportowania w systemie Azure. Analizuje ona również najlepsze rozwiązania dotyczące zabezpieczania dostępu do subskrypcji platformy Azure przy użyciu usługi Azure AD.
* [Autoryzowanie dostępu do aplikacji sieci web przy użyciu protokołu OAuth 2.0 i usługi Azure AD](../active-directory/develop/v1-protocols-oauth-code.md)
  
* [Zarządzanie usługą Key Vault interfejsów API REST](https://msdn.microsoft.com/library/azure/mt620024.aspx)
  
  Ten dokument jest odwołanie do interfejsów API REST do zarządzania magazynem kluczy programowo, włącznie z ustawieniem zasad dostępu magazynu kluczy.
* [Interfejsy API REST usługi Key Vault](https://msdn.microsoft.com/library/azure/dn903609.aspx)
  
* [Kontrola dostępu do kluczy](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl)
  
* [Kontrola dostępu do kluczy tajnych](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl)
  
* [Ustaw](https://docs.microsoft.com/powershell/module/azurerm.keyvault/Set-AzureRmKeyVaultAccessPolicy) i [Usuń](https://docs.microsoft.com/powershell/module/azurerm.keyvault/Remove-AzureRmKeyVaultAccessPolicy) zasad dostępu magazynu kluczy przy użyciu programu PowerShell
  
## <a name="next-steps"></a>Kolejne kroki
[Konfigurowanie zapór i sieci wirtualnych usługi Key Vault](key-vault-network-security.md)

Aby zapoznać się z samouczkiem wprowadzającym dla administratora, zobacz [Wprowadzenie do usługi Azure Key Vault](key-vault-get-started.md).

Aby uzyskać więcej informacji na temat rejestrowania użycia usługi Key Vault, zobacz [Funkcja rejestrowania usługi Azure Key Vault](key-vault-logging.md).

Aby uzyskać więcej informacji na temat używania kluczy i wpisów tajnych za pomocą usługi Azure Key Vault, zobacz [o kluczach i wpisach tajnych](https://msdn.microsoft.com/library/azure/dn903623.aspx).

Jeśli masz pytania dotyczące usługi Key Vault, odwiedź stronę [forów](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).

