---
title: Wdrażanie usługi Notification Hubs i zarządzanie nią przy użyciu programu PowerShell
description: Jak tworzyć i zarządzać nimi przy użyciu programu PowerShell do automatyzacji w usłudze Notification Hubs
services: notification-hubs
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 7c58f2c8-0399-42bc-9e1e-a7f073426451
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 4dbbaeea736dd46478ad9992201ea28bd7bfc2ba
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61457842"
---
# <a name="deploy-and-manage-notification-hubs-using-powershell"></a>Wdrażanie i zarządzanie nimi przy użyciu programu PowerShell w usłudze notification hubs

## <a name="overview"></a>Omówienie

W tym artykule przedstawiono sposób tworzenia i zarządzania usługi Azure Notification Hubs przy użyciu programu PowerShell. Następujące typowe zadania automatyzacji są wyświetlane w tym artykule.

- Tworzenie centrum powiadomień
- Ustaw poświadczenia

Jeśli musisz także utworzyć nowej przestrzeni nazw magistrali usług, do swojej usługi notification hubs, zobacz [zarządzania usługi Service Bus przy użyciu programu PowerShell](../service-bus-messaging/service-bus-powershell-how-to-provision.md).

Zarządzanie centra powiadomień nie jest obsługiwane bezpośrednio przez polecenia cmdlet, dołączone do programu Azure PowerShell. Najlepszym rozwiązaniem z poziomu programu PowerShell jest odwołać się do zestawu Microsoft.Azure.NotificationHubs.dll. Zestaw jest rozpowszechniana z [pakiet NuGet platformy Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Azure to platforma z subskrypcji. Aby uzyskać więcej informacji na temat uzyskiwania subskrypcji, zobacz [opcje zakupu], [oferty dla subskrybentów], lub [Bezpłatna wersja próbna].
- Komputer z programem Azure PowerShell. Aby uzyskać instrukcje, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell].
- Ogólna wiedza o skryptów programu PowerShell, pakietów NuGet i .NET Framework.

## <a name="including-a-reference-to-the-net-assembly-for-service-bus"></a>Uwzględnienie odwołania do zestawu .NET dla usługi Service Bus

Zarządzanie usługi Azure Notification Hubs nie jest jeszcze dołączone do poleceń cmdlet programu PowerShell w programie Azure PowerShell. Aby udostępnić usługę notification hubs, można użyć klienta platformy .NET w [pakiet NuGet platformy Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Najpierw upewnij się, można znaleźć skryptu **Microsoft.Azure.NotificationHubs.dll** zestawu, który jest instalowany jako pakiet NuGet w projekcie programu Visual Studio. Aby elastyczne skrypt wykonuje następujące czynności:

1. Określa ścieżkę, w którym została wywołana.
2. Przechodzi przez ścieżkę, aż znajdzie folderem o nazwie `packages`. Ten folder jest tworzony podczas instalowania pakietów NuGet dla projektów programu Visual Studio.
3. Rekursywnie wyszukuje `packages` folder zestaw o nazwie `Microsoft.Azure.NotificationHubs.dll`.
4. Odwołuje się do zestawu, tak aby typy były dostępne do późniejszego użycia.

Poniżej przedstawiono, jak te kroki są implementowane w skrypcie programu PowerShell:

``` powershell
try
{
    # WARNING: Make sure to reference the latest version of Microsoft.Azure.NotificationHubs.dll
    Write-Output "Adding the [Microsoft.Azure.NotificationHubs.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.Azure.NotificationHubs.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Output "The [Microsoft.Azure.NotificationHubs.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error("Could not add the Microsoft.Azure.NotificationHubs.dll assembly to the script. Make sure you build the solution before running the provisioning script.")
}
```

## <a name="create-the-namespacemanager-class"></a>Utwórz `NamespaceManager` klasy

Aby udostępnić usługę Notification Hubs, Utwórz wystąpienie obiektu [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.namespacemanager.aspx) klasy z zestawu SDK.

Możesz użyć [Get-AzureSBAuthorizationRule] polecenia cmdlet dołączone do programu Azure PowerShell można pobrać reguły autoryzacji, która służy do zapewnienia parametry połączenia. Odwołanie do `NamespaceManager` wystąpienie jest przechowywane w `$NamespaceManager` zmiennej. `$NamespaceManager` Służy do obsługi administracyjnej Centrum powiadomień.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager=[Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```

## <a name="provisioning-a-new-notification-hub"></a>Aprowizacja nowego centrum powiadomień

Aby zainicjować obsługę nowego centrum powiadomień, należy użyć [interfejsu API .NET usługi Notification Hubs].

Ta część skrypt służy do konfigurowania cztery zmienne lokalne.

1. `$Namespace`: Wartość nazwy obszaru nazw, gdzie chcesz utworzyć Centrum powiadomień.
2. `$Path`: Ustaw tę ścieżkę do nazwy nowego centrum powiadomień.  Na przykład "MyHub".
3. `$WnsPackageSid`: Ustaw tę opcję na identyfikator SID pakietu dla aplikacji Windows z [Centrum deweloperów Windows](https://developer.microsoft.com/en-us/windows).
4. `$WnsSecretkey`: Ustaw tę wartość na klucz tajny dla aplikacji Windows za pomocą [Centrum deweloperów Windows](https://developer.microsoft.com/en-us/windows).

Te zmienne są używane do nawiązywania połączeń do przestrzeni nazw i utworzyć nowe Centrum powiadomień konfigurowane do obsługi powiadomień usługi powiadomień Windows (WNS) przy użyciu poświadczeń usługi WNS dla aplikacji Windows. Aby uzyskać informacje dotyczące uzyskiwania pakietu identyfikator SID i wpisu tajnego klucza, zobacz temat [wprowadzenie do usługi Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) samouczka.

- Fragment kodu skryptu używa `NamespaceManager` obiektu, aby sprawdzić, w przypadku Centrum powiadomień jest identyfikowany przez `$Path` istnieje.
- Jeśli nie istnieje, skrypt tworzy `NotificationHubDescription` za pomocą usługi WNS poświadczeń i przekazuje go do `NamespaceManager` klasy `CreateNotificationHub` metody.

``` powershell
$Namespace = "<Enter your namespace>"
$Path  = "<Enter a name for your notification hub>"
$WnsPackageSid = "<your package sid>"
$WnsSecretkey = "<enter your secret key>"

$WnsCredential = New-Object -TypeName Microsoft.Azure.NotificationHubs.WnsCredential -ArgumentList $WnsPackageSid,$WnsSecretkey

# Query the namespace
$CurrentNamespace = Get-AzureSBNamespace -Name $Namespace

# Check if the namespace already exists
if ($CurrentNamespace)
{
    Write-Output "The namespace [$Namespace] in the [$($CurrentNamespace.Region)] region was found."

    # Create the NamespaceManager object used to create a new notification hub
    $sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
    Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
    $NamespaceManager = [Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
    Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."

    # Check to see if the Notification Hub already exists
    if ($NamespaceManager.NotificationHubExists($Path))
    {
        Write-Output "The [$Path] notification hub already exists in the [$Namespace] namespace."  
    }
    else
    {
        Write-Output "Creating the [$Path] notification hub in the [$Namespace] namespace."
        $NHDescription = New-Object -TypeName Microsoft.Azure.NotificationHubs.NotificationHubDescription -ArgumentList $Path;
        $NHDescription.WnsCredential = $WnsCredential;
        $NamespaceManager.CreateNotificationHub($NHDescription);
        Write-Output "The [$Path] notification hub was created in the [$Namespace] namespace."
    }
}
else
{
    Write-Host "The [$Namespace] namespace does not exist."
}
```

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Zarządzanie usługą Service Bus za pomocą programu PowerShell](../service-bus-messaging/service-bus-powershell-how-to-provision.md)
- [Tworzenie kolejek, tematów i subskrypcji przy użyciu skryptu programu PowerShell usługi Service Bus](https://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [Jak utworzyć Namespace magistrali usług i Centrum zdarzeń za pomocą skryptu programu PowerShell](https://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Kilka gotowych skryptów są również dostępne do pobrania:

- [Skrypty programu PowerShell usługi Service Bus](https://code.msdn.microsoft.com/windowsazure/Service-Bus-PowerShell-a46b7059)

[Opcje zakupu]: https://azure.microsoft.com/pricing/purchase-options/
[Oferty dla subskrybentów]: https://azure.microsoft.com/pricing/member-offers/
[Bezpłatna wersja próbna]: https://azure.microsoft.com/pricing/free-trial/
[Instalowanie i konfigurowanie programu Azure PowerShell]: /powershell/azureps-cmdlets-docs
[interfejsu API .NET usługi Notification Hubs]: https://docs.microsoft.com/dotnet/api/overview/azure/notification-hubs?view=azure-dotnet
[Get-AzureSBNamespace]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azuresbnamespace
[New-AzureSBNamespace]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azuresbnamespace
[Get-AzureSBAuthorizationRule]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azuresbauthorizationrule
