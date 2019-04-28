---
services: virtual-machines
title: Konfigurowanie programu PowerShell
author: JoeDavies-MSFT
solutions: ''
manager: timlt
editor: tysonn
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 05/12/2015
ms.author: rasquill
ms.openlocfilehash: b96e8e6e31817f6d261f41dbf3b3047dd49c29ba
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485394"
---
## <a name="setting-up-powershell"></a>Konfigurowanie programu PowerShell
Zanim użyjesz programu Azure PowerShell, wykonaj następujące kroki.

### <a name="verify-powershell-versions"></a>Sprawdź wersji programu PowerShell
Zanim będzie można użyć programu Windows PowerShell, musi mieć programu Windows PowerShell, w wersji 3.0 lub 4.0. Aby znaleźć wersję środowiska Windows PowerShell, wpisz następujące polecenie w wierszu polecenia programu Windows PowerShell.

    $PSVersionTable

Powinien zostać wyświetlony podobny do poniższego.

    Name                           Value
    ----                           -----
    PSVersion                      3.0
    WSManStackVersion              3.0
    SerializationVersion           1.1.0.1
    CLRVersion                     4.0.30319.18444
    BuildVersion                   6.2.9200.16481
    PSCompatibleVersions           {1.0, 2.0, 3.0}
    PSRemotingProtocolVersion      2.2

Upewnij się, że wartość **PSVersion** jest 3.0 lub 4.0. Aby zainstalować zgodną wersję, zobacz [Windows Management Framework 3.0](https://www.microsoft.com/download/details.aspx?id=34595) lub [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855).

Powinien również mieć Azure PowerShell w wersji 0.8.0 lub nowszej. Aby sprawdzić wersję programu Azure PowerShell, który został zainstalowany przy użyciu następującego polecenia w wierszu polecenia programu Azure PowerShell.

    Get-Module azure | format-table version

Powinien zostać wyświetlony podobny do poniższego.

    Version
    -------
    0.8.16.1

Aby uzyskać instrukcje i łącza do najnowszej wersji, zobacz [jak instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azureps-cmdlets-docs).

### <a name="set-your-azure-account-and-subscription"></a>Ustawianie konta i subskrypcji platformy Azure
Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować swoje [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub zarejestrować się w celu [bezpłatna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

Otwórz wiersz polecenia programu Azure PowerShell, a następnie zaloguj się do platformy Azure za pomocą tego polecenia.

    Add-AzureAccount

Jeśli masz wiele subskrypcji platformy Azure, możesz wyświetlić listę subskrypcji platformy Azure za pomocą tego polecenia.

    Get-AzureSubscription

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

    $subscr="<SubscriptionName from the display of Get-AzureSubscription>"
    Select-AzureSubscription -SubscriptionName $subscr -Current    

Aby uzyskać więcej informacji na temat subskrypcji platformy Azure i kont, zobacz [jak: Połącz z subskrypcją](/powershell/azureps-cmdlets-docs#Connect).

