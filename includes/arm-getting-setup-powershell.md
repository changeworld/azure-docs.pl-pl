---
author: sdwheeler
ms.service: azure-powershell
ms.topic: include
ms.date: 11/09/2018
ms.author: sewhee
ms.openlocfilehash: c5440555c11d98fb89f8594eec1d4b7e74ea8667
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2019
ms.locfileid: "57891343"
---
## <a name="setting-up-powershell-for-resource-manager-templates"></a>Konfigurowanie programu PowerShell dla szablonów usługi Resource Manager
Zanim użyjesz programu Azure PowerShell z usługą Resource Manager, musisz mieć prawo programu Windows PowerShell i wersji programu Azure PowerShell.

### <a name="verify-powershell-versions"></a>Sprawdź wersji programu PowerShell
Sprawdź, czy masz środowisko Windows PowerShell w wersji 3.0 lub 4.0. Aby znaleźć wersję środowiska Windows PowerShell, wpisz następujące polecenie w wierszu polecenia programu Windows PowerShell.

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


Upewnij się, że wartość **PSVersion** jest 3.0 lub 4.0. Jeśli nie, zobacz [Windows Management Framework 3.0](https://www.microsoft.com/download/details.aspx?id=34595) lub [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855).

### <a name="set-your-azure-account-and-subscription"></a>Ustawianie konta i subskrypcji platformy Azure
Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować swoje [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub zarejestrować się w celu [bezpłatna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

Otwórz wiersz polecenia programu Azure PowerShell, a następnie zaloguj się do platformy Azure za pomocą tego polecenia.

    Connect-AzureRmAccount

Jeśli masz wiele subskrypcji platformy Azure, możesz wyświetlić listę subskrypcji platformy Azure za pomocą tego polecenia.

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

Możesz ustawić bieżąca subskrypcja platformy Azure, uruchamiając następujące polecenia w wierszu polecenia programu Azure PowerShell. Zastąp wszystkie elementy w ramach oferty, w tym < a > znaków z właściwą nazwą.

    $subscr="<SubscriptionName from the display of Get-AzureRmSubscription>"
    Select-AzureRmSubscription -SubscriptionName $subscr -Current

Aby uzyskać więcej informacji na temat subskrypcji platformy Azure i kont, zobacz [jak: Połącz z subskrypcją](/powershell/azureps-cmdlets-docs#step-3-connect).
