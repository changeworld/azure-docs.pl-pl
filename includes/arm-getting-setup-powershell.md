## <a name="setting-up-powershell-for-resource-manager-templates"></a>Konfigurowanie środowiska PowerShell dla Menedżera zasobów szablonów
Przed użyciem programu Azure PowerShell z usługą Resource Manager, należy mieć prawo środowiska Windows PowerShell i wersji programu Azure PowerShell.

### <a name="verify-powershell-versions"></a>Sprawdź wersje programu PowerShell
Sprawdź, czy masz środowiska Windows PowerShell w wersji 3.0 lub 4.0. Aby znaleźć wersję środowiska Windows PowerShell, wpisz następujące polecenie w wierszu polecenia programu Windows PowerShell.

    $PSVersionTable

Otrzymasz informacje następującego typu:

    Name                           Value
    ----                           -----
    PSVersion                      3.0
    WSManStackVersion              3.0
    SerializationVersion           1.1.0.1
    CLRVersion                     4.0.30319.18444
    BuildVersion                   6.2.9200.16481
    PSCompatibleVersions           {1.0, 2.0, 3.0}
    PSRemotingProtocolVersion      2.2


Sprawdź, czy wartość **PSVersion** 3.0 lub 4.0. Jeśli nie, zobacz [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) lub [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

### <a name="set-your-azure-account-and-subscription"></a>Ustawianie konta i subskrypcji platformy Azure
Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz przeprowadzić aktywację Twojej [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub zarejestrować się w celu [bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).

Otwórz wiersz polecenia programu PowerShell systemu Azure i zaloguj się na platformie Azure za pomocą tego polecenia.

    Connect-AzureRmAccount

Jeśli masz wiele subskrypcji Azure, można wyświetlić listę subskrypcji platformy Azure za pomocą tego polecenia.

    Get-AzureRmSubscription

Otrzymasz informacje następującego typu:

    SubscriptionId            : fd22919d-eaca-4f2b-841a-e4ac6770g92e
    SubscriptionName          : Visual Studio Ultimate with MSDN
    Environment               : AzureCloud
    SupportedModes            : AzureServiceManagement,AzureResourceManager
    DefaultAccount            : johndoe@contoso.com
    Accounts                  : {johndoe@contoso.com}
    IsDefault                 : True
    IsCurrent                 : True
    CurrentStorageAccountName :
    TenantId                  : 32fa88b4-86f1-419f-93ab-2d7ce016dba7

Można ustawić bieżącej subskrypcji Azure, uruchamiając następujące polecenia w wierszu polecenia programu PowerShell systemu Azure. Zastąp wszystko w obrębie oferty, w tym < i > znaków z poprawną nazwą.

    $subscr="<SubscriptionName from the display of Get-AzureRmSubscription>"
    Select-AzureRmSubscription -SubscriptionName $subscr -Current

Aby uzyskać więcej informacji na temat subskrypcji platformy Azure i kont, zobacz [porady: nawiązać połączenia z subskrypcją](/powershell/azureps-cmdlets-docs#step-3-connect).

