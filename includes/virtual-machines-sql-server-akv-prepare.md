## <a name="prepare-for-akv-integration"></a>Przygotowanie do Integracja
Integracja magazynu kluczy Azure umożliwia skonfigurowanie maszyną Wirtualną programu SQL Server, istnieje kilka wymagań wstępnych: 

1. [Instalowanie programu Azure Powershell](#install-azure-powershell)
2. [Tworzenie usługi Azure Active Directory](#create-an-azure-active-directory)
3. [Tworzenie magazynu kluczy](#create-a-key-vault)

W poniższych sekcjach opisano te warunki wstępne i informacje, które należy zebrać później uruchomić polecenia cmdlet programu PowerShell.

### <a name="install-azure-powershell"></a>Instalowanie programu Azure PowerShell
Upewnij się, że zainstalowano najnowszy zestaw SDK usługi Azure PowerShell. Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azureps-cmdlets-docs).

### <a name="register-an-application-in-your-azure-active-directory"></a>Rejestrowanie aplikacji w usłudze Azure Active Directory
Najpierw musisz mieć [usługi Azure Active Directory](https://azure.microsoft.com/trial/get-started-active-directory/) (AAD) w ramach subskrypcji. Między wiele korzyści dzięki temu można udzielić uprawnienia do magazynu kluczy dla niektórych użytkowników i aplikacje.

Następnie należy zarejestrować aplikację przy użyciu usługi AAD. Zapewni to konto nazwy głównej usługi, które ma dostęp do magazynu kluczy, które będą potrzebne maszyny Wirtualnej. W artykule usługi Azure Key Vault możesz znaleźć następujące kroki w [zarejestrować aplikację w usłudze Azure Active Directory](../articles/key-vault/key-vault-get-started.md#register) sekcji, lub można zobaczyć kroki z zrzuty ekranu w **uzyskać tożsamości dla sekcji aplikacji**  z [ten wpis w blogu](http://blogs.technet.com/b/kv/archive/2015/01/09/azure-key-vault-step-by-step.aspx). Przed wykonaniem tych kroków, należy pamiętać, że należy zebrać następujące informacje w tej rejestracji, który będzie później potrzebny po włączeniu integracji magazynu kluczy Azure na maszynie Wirtualnej SQL.

* Po dodaniu aplikacji Znajdź **identyfikator aplikacji** na **aplikacji zarejestrowanej** bloku.   
    Identyfikator aplikacji jest przypisany później do **$spName** parametr (nazwę główną usługi) w skrypt programu PowerShell, aby włączyć integracji magazynu kluczy Azure. 
* Podczas tych czynności podczas tworzenia klucza, skopiować klucza tajnego klucza pokazany na poniższym zrzucie ekranu. Przypisano później do tego klucza klucz tajny **$spSecret** parametr (klucz tajny nazwy głównej usługi) w skrypcie programu PowerShell.  

* Identyfikator i klucz tajny będzie można również używać do tworzenia poświadczeń w programie SQL Server. 

* Musisz autoryzować tego nowego Identyfikatora klienta, są następujące uprawnienia dostępu: **szyfrowania**, **odszyfrować**, **wrapKey**, **unwrapKey**, **znak**, i **Sprawdź**. Jest to zrobić za pomocą [Set-AzureRmKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/mt603625.aspx) polecenia cmdlet. Aby uzyskać więcej informacji, zobacz [zezwolić aplikacji na używanie klucza lub klucza tajnego](../articles/key-vault/key-vault-get-started.md#authorize).

### <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy
Aby można było używać usługi Azure Key Vault do przechowywania kluczy, który będzie używany do szyfrowania maszyny Wirtualnej, potrzebujesz dostępu do magazynu kluczy. Jeśli magazynu kluczy nie zostały już skonfigurowane, utwórz go, wykonując kroki opisane w [wprowadzenie do korzystania z usługi Azure Key Vault](../articles/key-vault/key-vault-get-started.md) tematu. Przed wykonaniem tych kroków, należy pamiętać, że jest pewne informacje, które należy zebrać podczas tego — konfiguracja który będzie później potrzebny po włączeniu integracji magazynu kluczy Azure na maszynie Wirtualnej SQL.

    New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'

Po wyświetleniu okna Tworzenie krok magazynu kluczy, należy pamiętać, zwracana **vaultUri** właściwość, która jest adres URL magazynu kluczy. W przykładzie przedstawionym w tym kroku przedstawiony poniżej, nazwa magazynu kluczy jest ContosoKeyVault, w związku z tym magazynu kluczy adres URL może być https://contosokeyvault.vault.azure.net/.

Adres URL magazynu kluczy jest przypisany później do **$akvURL** parametru w skrypt programu PowerShell, aby włączyć integracji magazynu kluczy Azure.

Po utworzeniu magazynu kluczy, należy dodać klucz do magazynu kluczy, ten klucz będzie określonej podczas utworzymy klucza asymetrycznego utworzyć później w programie SQL Server.
