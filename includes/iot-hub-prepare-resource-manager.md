## <a name="prepare-to-authenticate-azure-resource-manager-requests"></a>Przygotowanie do uwierzytelniania żądań usługi Azure Resource Manager
Musi uwierzytelniać wszystkie operacje, które należy wykonać na zasobów przy użyciu [usługi Azure Resource Manager] [ lnk-authenticate-arm] z usługi Azure Active Directory (AD). Najprostszym sposobem skonfigurowania tego jest za pomocą programu PowerShell lub wiersza polecenia platformy Azure.

Zainstaluj [poleceń cmdlet programu Azure PowerShell] [ lnk-powershell-install] przed kontynuowaniem.

Poniższe kroki pokazują, jak skonfigurować uwierzytelnianie hasła dla aplikacji usługi AD przy użyciu programu PowerShell. Te polecenia można wykonać w standardowej sesji programu PowerShell.

1. Zaloguj się do subskrypcji platformy Azure przy użyciu następującego polecenia:

    ```powershell
    Connect-AzureRmAccount
    ```

1. Jeśli masz wiele subskrypcji Azure, logowanie do platformy Azure udziela dostępu do subskrypcji platformy Azure skojarzone z poświadczeniami użytkownika. Aby wyświetlić listę dostępnych przy użyciu subskrypcji platformy Azure, użyj następującego polecenia:

    ```powershell
    Get-AzureRMSubscription
    ```

    Użyj następującego polecenia, aby wybrać subskrypcję, która ma być używany do uruchamiania polecenia do zarządzania Centrum IoT. Można użyć nazwy subskrypcji lub identyfikatora z danych wyjściowych poprzedniego polecenia:

    ```powershell
    Select-AzureRMSubscription `
        -SubscriptionName "{your subscription name}"
    ```

2. Zanotuj Twojej **TenantId** i **SubscriptionId**. Należy je później.
3. Utwórz nową aplikację usługi Azure Active Directory przy użyciu następujące polecenie, zastępując symbole zastępcze:
   
   * **{Nazwa wyświetlana}:** nazwę wyświetlaną dla aplikacji, takich jak **MySampleApp**
   * **{Adres URL strony głównej}:** adres URL strony głównej aplikacji takich jak **http://mysampleapp/home**. Ten adres URL nie musi wskazywać rzeczywistych aplikacji.
   * **{Identyfikator aplikacji}:** Unikatowy identyfikator **http://mysampleapp**. Ten adres URL nie musi wskazywać rzeczywistych aplikacji.
   * **{Hasło}:** hasła używanego do uwierzytelniania z aplikacji.
     
     ```powershell
     $SecurePassword=ConvertTo-SecureString {password} –asplaintext –force
     New-AzureRmADApplication -DisplayName {Display name} -HomePage {Home page URL} -IdentifierUris {Application identifier} -Password $SecurePassword
     ```
4. Zanotuj **ApplicationId** aplikacji został utworzony. Należy to później.
5. Utwórz nową główną nazwę usługi, za pomocą następującego polecenia, zastępując **{MyApplicationId}** z **ApplicationId** w poprzednim kroku:
   
    ```powershell
    New-AzureRmADServicePrincipal -ApplicationId {MyApplicationId}
    ```
6. Konfigurowanie przypisania roli za pomocą następującego polecenia, zastępując **{MyApplicationId}** z Twojej **ApplicationId**.
   
    ```powershell
    New-AzureRmRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName {MyApplicationId}
    ```

Tworzenie aplikacji usługi Azure AD, która pozwala na uwierzytelnianie z niestandardowych aplikacji C# zostało zakończone. W dalszej części tego samouczka niezbędne są następujące wartości:

* Dla identyfikatora dzierżawcy
* SubscriptionId
* ApplicationId
* Hasło

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
