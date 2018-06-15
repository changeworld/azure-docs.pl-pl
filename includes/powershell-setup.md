---
services: virtual-machines
title: Konfigurowanie środowiska PowerShell
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
ms.openlocfilehash: 19c704d965ff3e2fc9ac8c5b623aeb386cb0b974
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2018
ms.locfileid: "23886183"
---
## <a name="setting-up-powershell"></a>Konfigurowanie środowiska PowerShell
Przed użyciem programu Azure PowerShell, wykonaj następujące kroki.

### <a name="verify-powershell-versions"></a>Sprawdź wersje programu PowerShell
Przed użyciem programu Windows PowerShell, musi mieć środowiska Windows PowerShell, w wersji 3.0 lub 4.0. Aby znaleźć wersję środowiska Windows PowerShell, wpisz następujące polecenie w wierszu polecenia programu Windows PowerShell.

    $PSVersionTable

Powinny zostać wyświetlone informacje podobne.

    Name                           Value
    ----                           -----
    PSVersion                      3.0
    WSManStackVersion              3.0
    SerializationVersion           1.1.0.1
    CLRVersion                     4.0.30319.18444
    BuildVersion                   6.2.9200.16481
    PSCompatibleVersions           {1.0, 2.0, 3.0}
    PSRemotingProtocolVersion      2.2

Sprawdź, czy wartość **PSVersion** 3.0 lub 4.0. Aby zainstalować zgodną wersję, zobacz [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) lub [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

Należy również zaznaczyć Azure PowerShell w wersji 0.8.0 lub nowszej. Można sprawdzić wersji programu Azure PowerShell, zainstalowanym za pomocą tego polecenia w wierszu polecenia programu PowerShell systemu Azure.

    Get-Module azure | format-table version

Powinny zostać wyświetlone informacje podobne.

    Version
    -------
    0.8.16.1

Aby uzyskać instrukcje i łącza do najnowszej wersji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azureps-cmdlets-docs).

### <a name="set-your-azure-account-and-subscription"></a>Ustawianie konta i subskrypcji platformy Azure
Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz przeprowadzić aktywację Twojej [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub zarejestrować się w celu [bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).

Otwórz wiersz polecenia programu PowerShell systemu Azure i zaloguj się do platformy Azure z tego polecenia.

    Add-AzureAccount

Jeśli masz wiele subskrypcji Azure, można wyświetlić listę subskrypcji platformy Azure za pomocą tego polecenia.

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

Można ustawić bieżącej subskrypcji Azure, uruchamiając następujące polecenia w wierszu polecenia programu PowerShell systemu Azure. Zastąp wszystko w obrębie oferty, w tym < i > znaków z poprawną nazwą.

    $subscr="<SubscriptionName from the display of Get-AzureSubscription>"
    Select-AzureSubscription -SubscriptionName $subscr -Current    

Aby uzyskać więcej informacji na temat subskrypcji platformy Azure i kont, zobacz [porady: nawiązać połączenia z subskrypcją](/powershell/azureps-cmdlets-docs#Connect).

