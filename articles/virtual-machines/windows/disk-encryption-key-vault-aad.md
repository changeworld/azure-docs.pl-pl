---
title: Tworzenie i Konfigurowanie magazynu kluczy dla Azure Disk Encryption za pomocą usługi Azure AD (poprzednia wersja)
description: Ten artykuł zawiera wymagania wstępne dotyczące korzystania z systemu Microsoft Azure Disk Encryption dla maszyn wirtualnych IaaS.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 5a42b1e0dc82b3340bbebe176c71cb1754d00664
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78391511"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption-with-azure-ad-previous-release"></a>Tworzenie i Konfigurowanie magazynu kluczy dla Azure Disk Encryption za pomocą usługi Azure AD (poprzednia wersja)

**Nowa wersja Azure Disk Encryption eliminuje wymóg udostępniania parametru aplikacji usługi Azure AD w celu włączenia szyfrowania dysków maszyny wirtualnej. W nowej wersji nie jest już wymagane podanie poświadczeń usługi Azure AD podczas kroku włączania szyfrowania. Wszystkie nowe maszyny wirtualne muszą być szyfrowane bez parametrów aplikacji usługi Azure AD przy użyciu nowej wersji. Aby wyświetlić instrukcje włączania szyfrowania dysków maszyn wirtualnych przy użyciu nowej wersji, zobacz [Azure Disk Encryption](disk-encryption-overview.md). Maszyny wirtualne, które zostały już zaszyfrowane za pomocą parametrów aplikacji usługi Azure AD, są nadal obsługiwane i powinny być nadal utrzymywane przy użyciu składni usługi AAD.**

Azure Disk Encryption używa Azure Key Vault do kontrolowania kluczy szyfrowania dysków i wpisów tajnych oraz zarządzania nimi.  Aby uzyskać więcej informacji na temat magazynów kluczy, zobacz Wprowadzenie do [Azure Key Vault](../../key-vault/key-vault-get-started.md) i [Zabezpieczanie magazynu kluczy](../../key-vault/key-vault-secure-your-key-vault.md). 

Tworzenie i Konfigurowanie magazynu kluczy do użycia z Azure Disk Encryption z usługą Azure AD (poprzednią wersją) obejmuje trzy kroki:

1. Tworzenie magazynu kluczy. 
2. Konfigurowanie aplikacji usługi Azure AD i jednostkę usługi.
3. Ustawianie zasad dostępu magazynu kluczy dla aplikacji usługi Azure AD.
4. Zaawansowane zasady dostępu magazynu kluczy zestawu.
 
Możesz również, jeśli chcesz, wygenerować lub zaimportować klucz szyfrowania klucza (KEK).

Instrukcje dotyczące sposobu [instalowania narzędzi i łączenia się z platformą Azure](disk-encryption-key-vault.md#install-tools-and-connect-to-azure)można znaleźć w artykule głównym [Tworzenie i Konfigurowanie magazynu kluczy dla Azure Disk Encryption](disk-encryption-key-vault.md) .

> [!Note]
> Kroki opisane w tym artykule są zautomatyzowane w [Azure Disk Encryption skrypt interfejsu wiersza polecenia wymagania wstępne](https://github.com/ejarvi/ade-cli-getting-started) i [Azure Disk Encryption skrypt programu PowerShell dotyczące wymagań wstępnych](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts).


## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy 
Azure Disk Encryption jest zintegrowana z [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) , aby pomóc w kontroli i zarządzaniu kluczami szyfrowania dysków oraz wpisami tajnymi w ramach subskrypcji magazynu kluczy. Można utworzyć magazyn kluczy lub użyć istniejącego dla usługi Azure Disk Encryption. Aby uzyskać więcej informacji na temat magazynów kluczy, zobacz Wprowadzenie do [Azure Key Vault](../../key-vault/key-vault-get-started.md) i [Zabezpieczanie magazynu kluczy](../../key-vault/key-vault-secure-your-key-vault.md). Szablon usługi Resource Manager, programu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure umożliwia tworzenie magazynu kluczy. 


>[!WARNING]
>Upewnij się, że klucze tajne szyfrowania nie przekracza granic regionalnych, usługa Azure Disk Encryption musi znajdować się w tym samym regionie usługi Key Vault i maszyn wirtualnych. Tworzenie i używanie usługi Key Vault, który znajduje się w tym samym regionie co maszyna wirtualna do zaszyfrowania. 


### <a name="create-a-key-vault-with-powershell"></a>Tworzenie magazynu kluczy przy użyciu programu PowerShell

Można utworzyć magazyn kluczy z Azure PowerShell przy użyciu polecenia cmdlet [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault) . Aby uzyskać dodatkowe polecenia cmdlet dla Key Vault, zobacz [AZ. datamagazyn](/powershell/module/az.keyvault/). 

1. Utwórz nową grupę zasobów, w razie konieczności z poleceniem [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup).  Aby wyświetlić listę lokalizacji centrów danych, użyj polecenie [Get-AzLocation](/powershell/module/az.resources/get-azlocation). 
     
     ```azurepowershell-interactive
     # Get-AzLocation 
     New-AzResourceGroup –Name 'MyKeyVaultResourceGroup' –Location 'East US'
     ```

1. Tworzenie nowego magazynu kluczy przy użyciu polecenia [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault)
    
      ```azurepowershell-interactive
     New-AzKeyVault -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -Location 'East US'
     ```

4. Zanotuj **nazwę magazynu**, **nazwę grupy zasobów**, **Identyfikator zasobu**, identyfikator **URI magazynu**i **Identyfikator obiektu** , który jest zwracany do późniejszego użycia podczas szyfrowania dysków. 


### <a name="create-a-key-vault-with-azure-cli"></a>Tworzenie magazynu kluczy za pomocą interfejsu wiersza polecenia platformy Azure
Możesz zarządzać swoim magazynem kluczy za pomocą interfejsu wiersza polecenia platformy Azure, korzystając z poleceń [AZ Key magazyn](/cli/azure/keyvault#commands) . Aby utworzyć magazyn kluczy, użyj [AZ Key magazyn Create](/cli/azure/keyvault#az-keyvault-create).

1. Utwórz nową grupę zasobów, w razie konieczności, za pomocą [AZ Group Create](/cli/azure/group#az-group-create). Aby wyświetlić listę lokalizacji, użyj [AZ Account List-Locations](/cli/azure/account#az-account-list) 
     
     ```azurecli-interactive
     # To list locations: az account list-locations --output table
     az group create -n "MyKeyVaultResourceGroup" -l "East US"
     ```

3. Utwórz nowy magazyn kluczy za pomocą polecenia [AZ Key webcreate](/cli/azure/keyvault#az-keyvault-create).
    
     ```azurecli-interactive
     az keyvault create --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --location "East US"
     ```

4. Zanotuj **nazwę magazynu** (nazwę), **nazwę grupy zasobów**, **Identyfikator zasobu** (identyfikator), **Identyfikator URI magazynu**i **Identyfikator obiektu** , który jest zwracany do użycia później. 

### <a name="create-a-key-vault-with-a-resource-manager-template"></a>Tworzenie magazynu kluczy przy użyciu szablonu Menedżer zasobów

Magazyn kluczy można utworzyć przy użyciu [szablonu Menedżer zasobów](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. W szablonie szybkiego startu platformy Azure kliknij pozycję **Wdróż na platformie Azure**.
2. Wybierz subskrypcję, grupę zasobów, lokalizację grupy zasobów, nazwę Key Vault, identyfikator obiektu, postanowienia prawne i umowę, a następnie kliknij przycisk **Kup**. 


## <a name="set-up-an-azure-ad-app-and-service-principal"></a>Konfigurowanie aplikacji usługi Azure AD i nazwy głównej usługi 
Jeśli potrzebujesz szyfrowania była włączona na uruchomionej maszyny Wirtualnej na platformie Azure, usługi Azure Disk Encryption generuje i zapisuje klucze szyfrowania w magazynie kluczy. Zarządzanie kluczami szyfrowania w magazynie kluczy wymaga uwierzytelniania usługi Azure AD. Utwórz aplikację usługi Azure AD, w tym celu. Do celów uwierzytelniania można użyć uwierzytelniania opartego na kluczu tajnym klienta lub [uwierzytelniania usługi Azure AD opartego na certyfikatach klienta](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).


### <a name="set-up-an-azure-ad-app-and-service-principal-with-azure-powershell"></a>Konfigurowanie aplikacji usługi Azure AD i nazwy głównej usługi przy użyciu Azure PowerShell 
Aby wykonać następujące polecenia, Pobierz [moduł Azure AD PowerShell](/powershell/azure/active-directory/install-adv2)i użyj go. 

1. Użyj polecenia cmdlet [New-AzADApplication](/powershell/module/az.resources/new-azadapplication) programu PowerShell, aby utworzyć aplikację usługi Azure AD. MyApplicationHomePage i MyApplicationUri może być żadnych wartości, którą chcesz.

     ```azurepowershell
     $aadClientSecret = "My AAD client secret"
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force
     $azureAdApplication = New-AzADApplication -DisplayName "My Application Display Name" -HomePage "https://MyApplicationHomePage" -IdentifierUris "https://MyApplicationUri" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId
     ```

3. $AzureAdApplication.ApplicationId jest ClientID usługi AD platformy Azure i $aadClientSecret jest klucz tajny klienta, który będzie później używane do włączenia usługi Azure Disk Encryption. Chroń klucz tajny klienta usługi Azure AD, odpowiednio. Uruchomienie `$azureAdApplication.ApplicationId` będzie zawierać identyfikator aplikacji.


### <a name="set-up-an-azure-ad-app-and-service-principal-with-azure-cli"></a>Konfigurowanie aplikacji usługi Azure AD i jednostki usługi przy użyciu interfejsu wiersza polecenia platformy Azure

Jednostkami usługi można zarządzać za pomocą interfejsu wiersza polecenia platformy Azure, korzystając z poleceń [AZ AD Sp](/cli/azure/ad/sp) . Aby uzyskać więcej informacji, zobacz [Tworzenie jednostki usługi platformy Azure](/cli/azure/create-an-azure-service-principal-azure-cli).

1. Utwórz nową jednostkę usługi.
     
     ```azurecli-interactive
     az ad sp create-for-rbac --name "ServicePrincipalName" --password "My-AAD-client-secret" --skip-assignment 
     ```
3.  Identyfikator aplikacji zwracany jest identyfikatora klienta usługi Azure AD używane w innych poleceniach. Jest to również nazwę SPN, użyjesz az keyvault set-policy. Hasło jest klucz tajny klienta, którego należy później włączyć usługi Azure Disk Encryption. Chroń klucz tajny klienta usługi Azure AD, odpowiednio.
 
### <a name="set-up-an-azure-ad-app-and-service-principal-though-the-azure-portal"></a>Skonfiguruj aplikację usługi Azure AD i nazwę główną usługi, chociaż Azure Portal
Wykonaj kroki z [portalu use, aby utworzyć aplikację Azure Active Directory i nazwę główną usługi, która będzie mogła uzyskać dostęp do zasobów](../../active-directory/develop/howto-create-service-principal-portal.md) artykułu w celu utworzenia aplikacji usługi Azure AD. Poszczególne kroki wymienione poniżej spowoduje przejście bezpośrednio do sekcji artykułu, aby zakończyć. 

1. [Weryfikuj wymagane uprawnienia](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)
2. [Tworzenie aplikacji Azure Active Directory](../../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) 
     - Można użyć dowolnej nazwy i chcesz przy tworzeniu aplikacji adres URL logowania.
3. [Pobierz identyfikator aplikacji i klucz uwierzytelniania](../../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in). 
     - Klucz uwierzytelniania jest kluczem tajnym klienta i jest używany jako AadClientSecret dla elementu Set-AzVMDiskEncryptionExtension. 
        - Klucz uwierzytelniania jest używany przez aplikację jako poświadczeń do logowania do usługi Azure AD. W witrynie Azure portal ten klucz tajny jest nazywany kluczy, ale nie zawiera żadnych relacji do magazynów kluczy. Zabezpiecz ten wpis tajny odpowiednio. 
     - Identyfikator aplikacji będzie później używany jako AadClientId dla właściwości Set-AzVMDiskEncryptionExtension i jako ServicePrincipalName dla elementu Set-AzKeyVaultAccessPolicy. 

## <a name="set-the-key-vault-access-policy-for-the-azure-ad-app"></a>Ustawianie zasad dostępu magazynu kluczy dla aplikacji usługi Azure AD
Do zapisu kluczy tajnych szyfrowania określonej usługi Key Vault, usługa Azure Disk Encryption wymaga Identyfikatora klienta i klucz tajny klienta aplikacji usługi Azure Active Directory, która ma uprawnienia do zapisu kluczy tajnych w usłudze Key Vault. 

> [!NOTE]
> Azure Disk Encryption wymaga skonfigurowania następujących zasad dostępu do aplikacji klienckiej usługi Azure AD: _WrapKey_ i _Ustaw_ uprawnienia.

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-azure-powershell"></a>Ustawianie zasad dostępu magazynu kluczy dla aplikacji usługi Azure AD za pomocą Azure PowerShell
Aplikacja usługi Azure AD wymaga praw dostępu do kluczy lub wpisów tajnych w magazynie. Użyj polecenia cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) , aby udzielić uprawnień do aplikacji przy użyciu identyfikatora klienta (który został wygenerowany podczas rejestrowania aplikacji) jako wartość parametru _– ServicePrincipalName_ . Aby dowiedzieć się więcej, zobacz wpis w blogu [Azure Key Vault — krok](https://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx)po kroku. 

1. Ustawianie zasad dostępu magazynu kluczy dla aplikacji usługi AD za pomocą programu PowerShell.

     ```azurepowershell
     $keyVaultName = 'MySecureVault'
     $aadClientID = 'MyAadAppClientID'
     $KVRGname = 'MyKeyVaultResourceGroup'
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
     ```

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-azure-cli"></a>Ustawianie zasad dostępu magazynu kluczy dla aplikacji usługi Azure AD przy użyciu interfejsu wiersza polecenia platformy Azure
Użyj [azexception Set-Policy](/cli/azure/keyvault#az-keyvault-set-policy) , aby ustawić zasady dostępu. Aby uzyskać więcej informacji, zobacz [zarządzanie Key Vault przy użyciu interfejsu wiersza polecenia 2,0](../../key-vault/key-vault-manage-with-cli2.md#authorizing-an-application-to-use-a-key-or-secret).

Zapewniają utworzonej jednostki usługi, za pośrednictwem dostępu wiersza polecenia platformy Azure, aby uzyskać klucze tajne i zawijania klucze za pomocą następującego polecenia:
 
     ```azurecli-interactive
     az keyvault set-policy --name "MySecureVault" --spn "<spn created with CLI/the Azure AD ClientID>" --key-permissions wrapKey --secret-permissions set
     ```

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-the-portal"></a>Ustawianie zasad dostępu magazynu kluczy dla aplikacji usługi Azure AD przy użyciu portalu

1. Otwórz grupę zasobów za pomocą usługi key vault.
2. Wybierz magazyn kluczy, przejdź do pozycji **zasady dostępu**, a następnie kliknij przycisk **Dodaj nowe**.
3. W obszarze **Wybierz podmiot zabezpieczeń**Wyszukaj utworzoną aplikację usługi Azure AD i wybierz ją. 
4. W przypadku **uprawnień kluczowych**zaznacz pole **Zawijanie klucza** w obszarze **operacje kryptograficzne**.
5. W przypadku uprawnień do wpisów **tajnych**zaznacz pozycję **Ustaw** w obszarze **operacje zarządzania kluczami tajnymi**.
6. Kliknij przycisk **OK** , aby zapisać zasady dostępu. 

![Usługa Azure Key Vault operacje kryptograficzne - Opakuj klucz](../media/disk-encryption/keyvault-portal-fig3.png)

![Ustaw uprawnienia klucza wpisu tajnego z magazynu platformy Azure —](../media/disk-encryption/keyvault-portal-fig3b.png)

## <a name="set-key-vault-advanced-access-policies"></a>Ustawianie zaawansowanych zasad dostępu magazynu kluczy
Platforma Azure wymaga dostępu do kluczy szyfrowania i wpisy tajne w magazynie kluczy, aby udostępnić je do maszyny Wirtualnej do rozruchu i odszyfrowywania woluminów. Włącz szyfrowanie dysków w magazynie kluczy lub wdrożenia zakończy się niepowodzeniem.  

### <a name="set-key-vault-advanced-access-policies-with-azure-powershell"></a>Ustawianie zaawansowanych zasad dostępu magazynu kluczy za pomocą Azure PowerShell
 Aby włączyć szyfrowanie dysków dla magazynu kluczy, należy użyć polecenia cmdlet programu PowerShell magazynu kluczy [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) .

  - **Włącz Key Vault szyfrowania dysku:** Usługa EnabledForDiskEncryption jest wymagana do szyfrowania dysków Azure.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDiskEncryption
     ```

  - **W razie potrzeby włącz Key Vault wdrożenia:** Umożliwia dostawcy zasobów Microsoft. COMPUTE pobieranie wpisów tajnych z tego magazynu kluczy, gdy ten magazyn kluczy jest przywoływany podczas tworzenia zasobu, na przykład podczas tworzenia maszyny wirtualnej.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDeployment
     ```

  - **W razie potrzeby włącz Key Vault wdrożenia szablonów:** Umożliwia Azure Resource Manager pobierania wpisów tajnych z tego magazynu kluczy, gdy do tego magazynu kluczy odwołuje się wdrożenie szablonu.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForTemplateDeployment
     ```

### <a name="set-key-vault-advanced-access-policies-using-the-azure-cli"></a>Ustawianie zaawansowanych zasad dostępu magazynu kluczy za pomocą interfejsu wiersza polecenia platformy Azure
Użyj [AZ Key Update](/cli/azure/keyvault#az-keyvault-update) , aby włączyć szyfrowanie dysków dla magazynu kluczy. 

 - **Włącz Key Vault szyfrowania dysku:** Szyfrowanie włączone dla dysku jest wymagane. 

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **W razie potrzeby włącz Key Vault wdrożenia:** Zezwól Virtual Machines na pobieranie certyfikatów przechowywanych jako wpisy tajne z magazynu.
     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **W razie potrzeby włącz Key Vault wdrożenia szablonów:** Zezwól Menedżer zasobów na pobieranie wpisów tajnych z magazynu.
     ```azurecli-interactive  
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-template-deployment "true"
     ```


### <a name="set-key-vault-advanced-access-policies-through-the-azure-portal"></a>Ustawianie zaawansowanych zasad dostępu magazynu kluczy za pomocą Azure Portal

1. Wybierz swój magazyn kluczy, przejdź do pozycji **zasady dostępu**, a **następnie kliknij, aby wyświetlić zaawansowane zasady dostępu**.
2. Zaznacz pole o nazwie **Włącz dostęp do Azure Disk Encryption na potrzeby szyfrowania woluminów**.
3. Wybierz pozycję **Włącz dostęp do usługi Azure Virtual Machines w celu wdrożenia** i/lub **włącz dostęp do Azure Resource Manager na potrzeby wdrożenia szablonu**, jeśli jest to konieczne. 
4. Kliknij przycisk **Save** (Zapisz).

![Zaawansowane zasady dostępu magazynu kluczy Azure](../media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-optional"></a>Skonfiguruj klucz szyfrowania klucza (opcjonalnie)
Jeśli chcesz użyć klucz szyfrowania klucza (KEK) Aby uzyskać dodatkową warstwę zabezpieczeń dla kluczy szyfrowania, należy dodać KEK do magazynu kluczy. Użyj polecenia cmdlet [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) , aby utworzyć klucz szyfrowania klucza w magazynie kluczy. Można także zaimportować KEK, z usługi zarządzania kluczami w środowisku lokalnym przez moduł HSM. Aby uzyskać więcej informacji, zobacz [dokumentację Key Vault](../../key-vault/key-vault-hsm-protected-keys.md). Jeśli klucz szyfrowania jest określony, usługi Azure Disk Encryption używa tego klucza do opakowania wpisów tajnych szyfrowania przed zapisaniem w usłudze Key Vault. 

* Podczas generowania kluczy należy użyć typu klucza RSA. Azure Disk Encryption nie obsługuje jeszcze kluczy krzywej eliptycznej.

* KEK adresy URL i wpisu tajnego usługi key vault, na których musi być poddany kontroli wersji. Azure wymusza to ograniczenie wersji. Nieprawidłowy klucz tajny i adresy URL KEK zobacz następujące przykłady:

  * Przykładowy prawidłowy tajny adres URL: *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Przykład prawidłowego adresu URL KEK: *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Usługa Azure Disk Encryption nie obsługuje określania numerów portów jako część wpisy tajne usługi key vault i KEK adresów URL. Aby zapoznać się z przykładami adresów URL obsługiwane i nieobsługiwane key vault zobacz następujące przykłady:

  * Nieakceptowalny adres URL magazynu kluczy *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Akceptowalny adres URL magazynu kluczy: *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="set-up-a-key-encryption-key-with-azure-powershell"></a>Skonfiguruj klucz szyfrowania klucza z Azure PowerShell 
Przed użyciem skryptu programu PowerShell, należy zapoznać się z wstępnie wymagane składniki usługi Azure Disk Encryption, aby zrozumieć kroki w skrypcie. Przykładowy skrypt może wymagać zmiany w danym środowisku. Ten skrypt tworzy wszystkie wymagania wstępne usługi Azure Disk Encryption i szyfruje istniejącej maszyny Wirtualnej IaaS, zawijanie klucz szyfrowania dysku przy użyciu klucza szyfrowania. 

 ```powershell
 # Step 1: Create a new resource group and key vault in the same location.
     # Fill in 'MyLocation', 'MyKeyVaultResourceGroup', and 'MySecureVault' with your values.
     # Use Get-AzLocation to get available locations and use the DisplayName.
     # To use an existing resource group, comment out the line for New-AzResourceGroup
     
     $Loc = 'MyLocation';
     $KVRGname = 'MyKeyVaultResourceGroup';
     $KeyVaultName = 'MySecureVault'; 
     New-AzResourceGroup –Name  $KVRGname –Location $Loc;
     New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc;
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname;
     $KeyVaultResourceId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname).ResourceId;
     $diskEncryptionKeyVaultUrl = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname).VaultUri;
     
 # Step 2: Create the AD application and service principal.
     # Fill in 'MyAADClientSecret', "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
     # MyApplicationHomePage and the MyApplicationUri can be any values you wish.
     
     $aadClientSecret =  'MyAADClientSecret';
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force;
     $azureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId;
     $aadClientID = $azureAdApplication.ApplicationId;
     
 #Step 3: Enable the vault for disk encryption and set the access policy for the Azure AD application.
     
     Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption;
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName  $KVRGname;
     
 #Step 4: Create a new key in the key vault with the Add-AzKeyVaultKey cmdlet.
     # Fill in 'MyKeyEncryptionKey' with your value.
     
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName -Destination 'Software';
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     
 #Step 5: Encrypt the disks of an existing IaaS VM
     # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values. 
     
     $VMName = 'MySecureVM';
      $VMRGName = 'MyVirtualMachineResourceGroup';
     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
```

## <a name="certificate-based-authentication-optional"></a>Uwierzytelnianie oparte na certyfikatach (opcjonalnie)
Jeśli chcesz użyć uwierzytelniania certyfikatu, możesz przekazać jeden do magazynu kluczy i wdrożyć ją do klienta. Przed użyciem skryptu programu PowerShell, należy zapoznać się z wstępnie wymagane składniki usługi Azure Disk Encryption, aby zrozumieć kroki w skrypcie. Przykładowy skrypt może wymagać zmiany w danym środowisku.

     
 ```powershell

 # Fill in "MyKeyVaultResourceGroup", "MySecureVault", and 'MyLocation' ('My location' only if needed)

   $KVRGname = 'MyKeyVaultResourceGroup'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption. No need to set 'My location' in this case.

   $Loc = 'MyLocation'
   New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption

   #Setting some variables with the key vault information 
   $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId

   # Create the Azure AD application and associate the certificate with it. 
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
   
   # Upload the pfx file to the key vault. 
   # Fill in "MyAADCert".  

   $KeyVaultSecretName = "MyAADCert"
   $FileContentBytes = get-content $CertPath -Encoding Byte
   $FileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
           $JSONObject = @"
           { 
               "data" : "$filecontentencoded", 
               "dataType" : "pfx", 
               "password" : "$CertPassword" 
           } 
"@

   $JSONObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
   $JSONEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

   #Set the secret and set the key vault policy for -EnabledForDeployment

   $Secret = ConvertTo-SecureString -String $JSONEncoded -AsPlainText -Force
   Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDeployment

   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values.

   $VMName = 'MySecureVM'
   $VMRGName = 'MyVirtualMachineResourceGroup'
   $CertUrl = (Get-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGName).ResourceId
   $VM = Get-AzVM -ResourceGroupName $VMRGName -Name $VMName 
   $VM = Add-AzVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzVM -VM $VM -ResourceGroupName $VMRGName 

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId
 ```

## <a name="certificate-based-authentication-and-a-kek-optional"></a>Uwierzytelnianie oparte na certyfikacie i KEK (opcjonalnie)

Jeśli chcesz użyć uwierzytelniania certyfikatu, a następnie opakuj klucz szyfrowania za pomocą klucza KEK, możesz użyć poniższego skryptu jako przykład. Przed użyciem skryptu programu PowerShell, należy zapoznać się z wszystkie poprzednie wymagania wstępne usługi Azure Disk Encryption, aby zrozumieć kroki w skrypcie. Przykładowy skrypt może wymagać zmiany w danym środowisku.

     
 ```powershell
# Fill in 'MyKeyVaultResourceGroup', 'MySecureVault', and 'MyLocation' (if needed)

   $KVRGname = 'MyKeyVaultResourceGroup'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption.

   $Loc = 'MyLocation'
   New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption

   # Create the Azure AD application and associate the certificate with it.  
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   ## Give access for setting secrets and wraping keys
   Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname

   # Upload the pfx file to the key vault. 
   # Fill in "MyAADCert". 

   $KeyVaultSecretName = "MyAADCert"
   $FileContentBytes = get-content $CertPath -Encoding Byte
   $FileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
           $JSONObject = @"
           { 
               "data" : "$filecontentencoded", 
               "dataType" : "pfx", 
               "password" : "$CertPassword" 
           } 
"@

   $JSONObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
   $JSONEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

   #Set the secret and set the key vault policy for deployment

   $Secret = ConvertTo-SecureString -String $JSONEncoded -AsPlainText -Force
   Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDeployment

   #Setting some variables with the key vault information and generating a KEK 
   # FIll in 'KEKName'
   
   $KEKName ='KEKName'
   $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId
   $KEK = Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $KEKName -Destination "Software"
   $KeyEncryptionKeyUrl = $KEK.Key.kid



   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values.

   $VMName = 'MySecureVM';
   $VMRGName = 'MyVirtualMachineResourceGroup';
   $CertUrl = (Get-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGName).ResourceId
   $VM = Get-AzVM -ResourceGroupName $VMRGName -Name $VMName 
   $VM = Add-AzVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzVM -VM $VM -ResourceGroupName $VMRGName

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId
```

 
## <a name="next-steps"></a>Następne kroki

[Włączanie Azure Disk Encryption z usługą Azure AD na maszynach wirtualnych z systemem Windows (poprzednia wersja)](disk-encryption-windows-aad.md)
