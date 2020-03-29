---
title: Wdrażanie centrów powiadomień i zarządzanie nimi przy użyciu programu PowerShell
description: Jak tworzyć centra powiadomień i zarządzać nimi przy użyciu programu PowerShell for Automation
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 7c58f2c8-0399-42bc-9e1e-a7f073426451
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 863fdb445cce41f0fe4cbee63a3d6198c0a79339
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76264648"
---
# <a name="deploy-and-manage-notification-hubs-using-powershell"></a>Wdrażanie centrów powiadomień i zarządzanie nimi przy użyciu programu PowerShell

## <a name="overview"></a>Omówienie

W tym artykule pokazano, jak używać narzędzia Tworzenie i zarządzanie usługą Azure Notification Hubs przy użyciu programu PowerShell. W tym artykule przedstawiono następujące typowe zadania automatyzacji.

- Tworzenie centrum powiadomień
- Ustawianie poświadczeń

Jeśli konieczne jest również utworzenie nowej przestrzeni nazw magistrali usług dla centrów powiadomień, zobacz [Zarządzanie usługą Service Bus za pomocą programu PowerShell](../service-bus-messaging/service-bus-powershell-how-to-provision.md).

Zarządzanie centrum powiadomień nie jest obsługiwany bezpośrednio przez polecenia cmdlet dołączone do programu Azure PowerShell. Najlepszym rozwiązaniem programu PowerShell jest odwołanie się do zestawu Microsoft.Azure.NotificationHubs.dll. Zestaw jest dystrybuowany z [pakietem NuGet centrów powiadomień platformy Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Platforma Azure to platforma oparta na subskrypcji. Aby uzyskać więcej informacji na temat uzyskiwania subskrypcji, zobacz [Opcje zakupu,] [Oferty członków]lub [Bezpłatna wersja próbna].
- Komputer z programem Azure PowerShell. Aby uzyskać instrukcje, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell].
- Ogólne zrozumienie skryptów programu PowerShell, pakietów NuGet i programu .NET Framework.

## <a name="including-a-reference-to-the-net-assembly-for-service-bus"></a>W tym odwołanie do zestawu .NET dla usługi Service Bus

Zarządzanie usługami Azure Notification Hubs nie jest jeszcze dołączony do poleceń cmdlet programu PowerShell w programie Azure PowerShell. Aby aprowizować centra powiadomień, można użyć klienta platformy .NET podanego w [pakiecie NuGet centrów powiadomień platformy Microsoft Azure.](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)

Najpierw upewnij się, że skrypt można zlokalizować **microsoft.Azure.NotificationHubs.dll** zestawu, który jest zainstalowany jako pakiet NuGet w projekcie programu Visual Studio. Aby być elastycznym, skrypt wykonuje następujące kroki:

1. Określa ścieżkę, w której został wywołany.
2. Przechodzi przez ścieżkę, aż `packages`znajdzie folder o nazwie . Ten folder jest tworzony podczas instalowania pakietów NuGet dla projektów programu Visual Studio.
3. Rekursywnie `packages` przeszukuje folder `Microsoft.Azure.NotificationHubs.dll`w poszukiwaniu zestawu o nazwie .
4. Odwołuje się do zestawu, tak aby typy były dostępne do późniejszego użycia.

Oto jak te kroki są implementowane w skrypcie programu PowerShell:

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

## <a name="create-the-namespacemanager-class"></a>Tworzenie `NamespaceManager` klasy

Aby aprowizować centra powiadomień, należy utworzyć wystąpienie [namespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager?view=azure-dotnet) klasy z SDK.

Polecenia cmdlet [Get-AzureSBAuthorizationRule] dołączonego do programu Azure PowerShell można użyć do pobrania reguły autoryzacji używanej do dostarczania ciągu połączenia. Odwołanie do `NamespaceManager` wystąpienia jest przechowywane `$NamespaceManager` w zmiennej. `$NamespaceManager`służy do aprowizowania centrum powiadomień.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager=[Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```

## <a name="provisioning-a-new-notification-hub"></a>Inicjowanie obsługi administracyjnej nowego Centrum powiadomień

Aby aprowizować nowe centrum powiadomień, użyj [interfejsu API platformy .NET dla centrów powiadomień].

W tej części skryptu można skonfigurować cztery zmienne lokalne.

1. `$Namespace`: Ustaw tę nazwę na nazwę obszaru nazw, w którym chcesz utworzyć centrum powiadomień.
2. `$Path`: Ustaw tę ścieżkę na nazwę nowego centrum powiadomień.  Na przykład "MyHub".
3. `$WnsPackageSid`: Ustaw tę ustaw pakiet SID dla aplikacji Windows z [Centrum deweloperów systemu Windows](https://developer.microsoft.com/en-us/windows).
4. `$WnsSecretkey`: Ustaw ten klucz tajny aplikacji systemu Windows z [Centrum deweloperów systemu Windows](https://developer.microsoft.com/en-us/windows).

Zmienne te są używane do łączenia się z obszarem nazw i tworzenia nowego Centrum powiadomień skonfigurowanego do obsługi powiadomień usług powiadomień systemu Windows (WNS) z poświadczeniami WNS dla aplikacji systemu Windows. Aby uzyskać informacje na temat uzyskiwania pakietu SID i klucz tajny zobacz [Wprowadzenie do centrum powiadomień](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) samouczek.

- Fragment kodu skryptu `NamespaceManager` używa obiektu, aby sprawdzić, czy Centrum `$Path` powiadomień zidentyfikowane przez istnieje.
- Jeśli nie istnieje, skrypt `NotificationHubDescription` tworzy z poświadczeniami WNS `NamespaceManager` i `CreateNotificationHub` przekazuje go do metody klasy.

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

- [Zarządzanie magistralą usług za pomocą programu PowerShell](../service-bus-messaging/service-bus-powershell-how-to-provision.md)
- [Jak utworzyć kolejki, tematy i subskrypcje usługi Service Bus przy użyciu skryptu programu PowerShell](https://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [Jak utworzyć obszar nazw magistrali usług i Centrum zdarzeń przy użyciu skryptu programu PowerShell](https://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Niektóre gotowe skrypty są również dostępne do pobrania:

- [Skrypty programu PowerShell magistrali usług](https://code.msdn.microsoft.com/windowsazure/Service-Bus-PowerShell-a46b7059)

[Opcje zakupu]: https://azure.microsoft.com/pricing/purchase-options/
[Oferty członków]: https://azure.microsoft.com/pricing/member-offers/
[Bezpłatna wersja próbna]: https://azure.microsoft.com/pricing/free-trial/
[Instalowanie i konfigurowanie programu Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Interfejs API platformy .NET dla centrów powiadomień]: https://docs.microsoft.com/dotnet/api/overview/azure/notification-hubs?view=azure-dotnet
[Get-AzureSBNamespace]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azuresbnamespace
[New-AzureSBNamespace]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azuresbnamespace
[Get-AzureSBAuthorizationRule]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azuresbauthorizationrule
