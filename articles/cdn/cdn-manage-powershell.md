---
title: Zarządzanie Azure CDN przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać poleceń cmdlet programu Azure PowerShell do zarządzania usługi Azure CDN.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: fb6f57a5-6e26-4847-8fd9-b51fb05a79eb
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 5634ecdec04f023d9eb901c4ad0fb21b13bcfdc1
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="manage-azure-cdn-with-powershell"></a>Zarządzanie Azure CDN przy użyciu programu PowerShell
PowerShell zawiera jedną z najbardziej elastycznym metod do zarządzania profilami usługi Azure CDN i punktów końcowych.  Można użyć programu PowerShell interakcyjnego lub pisanie skryptów do automatyzacji zadań zarządzania.  W tym samouczku przedstawiono kilka najbardziej typowych zadań, można wykonać za pomocą programu PowerShell do zarządzania profilami usługi Azure CDN i punktów końcowych.

## <a name="prerequisites"></a>Wymagania wstępne
Zarządzanie profilami usługi Azure CDN i punktów końcowych przy użyciu programu PowerShell, musi mieć zainstalowany moduł Azure PowerShell.  Aby dowiedzieć się, jak zainstalować program Azure PowerShell i nawiązania połączenia platformy Azure przy użyciu `Connect-AzureRmAccount` polecenia cmdlet, zobacz [jak instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).

> [!IMPORTANT]
> Należy zalogować się przy użyciu `Connect-AzureRmAccount` przed wykonaniem poleceń cmdlet programu Azure PowerShell.
> 
> 

## <a name="listing-the-azure-cdn-cmdlets"></a>Wyświetlanie listy poleceń cmdlet usługi Azure CDN
Możesz wyświetlić listę wszystkich poleceń cmdlet usługi Azure CDN przy użyciu `Get-Command` polecenia cmdlet.

```text
PS C:\> Get-Command -Module AzureRM.Cdn

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Get-AzureRmCdnCustomDomain                         2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnEndpoint                             2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnEndpointNameAvailability             2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnOrigin                               2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnProfile                              2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnProfileSsoUrl                        2.0.0      AzureRm.Cdn
Cmdlet          New-AzureRmCdnCustomDomain                         2.0.0      AzureRm.Cdn
Cmdlet          New-AzureRmCdnEndpoint                             2.0.0      AzureRm.Cdn
Cmdlet          New-AzureRmCdnProfile                              2.0.0      AzureRm.Cdn
Cmdlet          Publish-AzureRmCdnEndpointContent                  2.0.0      AzureRm.Cdn
Cmdlet          Remove-AzureRmCdnCustomDomain                      2.0.0      AzureRm.Cdn
Cmdlet          Remove-AzureRmCdnEndpoint                          2.0.0      AzureRm.Cdn
Cmdlet          Remove-AzureRmCdnProfile                           2.0.0      AzureRm.Cdn
Cmdlet          Set-AzureRmCdnEndpoint                             2.0.0      AzureRm.Cdn
Cmdlet          Set-AzureRmCdnOrigin                               2.0.0      AzureRm.Cdn
Cmdlet          Set-AzureRmCdnProfile                              2.0.0      AzureRm.Cdn
Cmdlet          Start-AzureRmCdnEndpoint                           2.0.0      AzureRm.Cdn
Cmdlet          Stop-AzureRmCdnEndpoint                            2.0.0      AzureRm.Cdn
Cmdlet          Test-AzureRmCdnCustomDomain                        2.0.0      AzureRm.Cdn
Cmdlet          Unpublish-AzureRmCdnEndpointContent                2.0.0      AzureRm.Cdn
```

## <a name="getting-help"></a>Uzyskiwanie pomocy
Pomoc można uzyskać za pomocą dowolnego z tych poleceń cmdlet, za pomocą `Get-Help` polecenia cmdlet.  `Get-Help` Umożliwia użycie i składni oraz opcjonalnie przedstawiono przykłady.

```text
PS C:\> Get-Help Get-AzureRmCdnProfile

NAME
    Get-AzureRmCdnProfile

SYNOPSIS
    Gets an Azure CDN profile.


SYNTAX
    Get-AzureRmCdnProfile [-ProfileName <String>] [-ResourceGroupName <String>] [-InformationAction
    <ActionPreference>] [-InformationVariable <String>] [<CommonParameters>]


DESCRIPTION
    Gets an Azure CDN profile and all related information.


RELATED LINKS

REMARKS
    To see the examples, type: "get-help Get-AzureRmCdnProfile -examples".
    For more information, type: "get-help Get-AzureRmCdnProfile -detailed".
    For technical information, type: "get-help Get-AzureRmCdnProfile -full".

```

## <a name="listing-existing-azure-cdn-profiles"></a>Lista istniejących profilów usługi Azure CDN
`Get-AzureRmCdnProfile` Polecenie cmdlet bez parametrów pobiera wszystkich istniejących profilów CDN.

```powershell
Get-AzureRmCdnProfile
```

Te dane wyjściowe mogą być przetwarzane potokowo do polecenia cmdlet dla wyliczenia.

```powershell
# Output the name of all profiles on this subscription.
Get-AzureRmCdnProfile | ForEach-Object { Write-Host $_.Name }

# Return only **Azure CDN from Verizon** profiles.
Get-AzureRmCdnProfile | Where-Object { $_.Sku.Name -eq "StandardVerizon" }
```

Można także wrócić jeden profil, określając nazwę i zasobów grupy profilów.

```powershell
Get-AzureRmCdnProfile -ProfileName CdnDemo -ResourceGroupName CdnDemoRG
```

> [!TIP]
> Użytkownik może mieć wiele profilów CDN o takiej samej nazwie, tak długo, jak znajdują się w różnych grupach zasobów.  Pominięcie `ResourceGroupName` parametr zwraca wszystkie profile o pasującej nazwie.
> 
> 

## <a name="listing-existing-cdn-endpoints"></a>Lista istniejących punktów końcowych usługi CDN
`Get-AzureRmCdnEndpoint` można pobrać punktu końcowego poszczególnych lub wszystkich punktów końcowych dla profilu.  

```powershell
# Get a single endpoint.
Get-AzureRmCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Get all of the endpoints on a given profile. 
Get-AzureRmCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG

# Return all of the endpoints on all of the profiles.
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint

# Return all of the endpoints in this subscription that are currently running.
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Where-Object { $_.ResourceState -eq "Running" }
```

## <a name="creating-cdn-profiles-and-endpoints"></a>Tworzenie profilów sieci CDN i punkty końcowe
`New-AzureRmCdnProfile` i `New-AzureRmCdnEndpoint` są używane do tworzenia profilów sieci CDN i punktów końcowych.

```powershell
# Create a new profile
New-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku StandardAkamai -Location "Central US"

# Create a new endpoint
New-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Location "Central US" -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

# Create a new profile and endpoint (same as above) in one line
New-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku StandardAkamai -Location "Central US" | New-AzureRmCdnEndpoint -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

```

## <a name="checking-endpoint-name-availability"></a>Sprawdzanie dostępności nazwy punktu końcowego
`Get-AzureRmCdnEndpointNameAvailability` Zwraca obiekt wskazującą, czy nazwa punktu końcowego jest dostępna.

```powershell
# Retrieve availability
$availability = Get-AzureRmCdnEndpointNameAvailability -EndpointName "cdnposhdoc"

# If available, write a message to the console.
If($availability.NameAvailable) { Write-Host "Yes, that endpoint name is available." }
Else { Write-Host "No, that endpoint name is not available." }
```

## <a name="adding-a-custom-domain"></a>Dodawanie domeny niestandardowej
`New-AzureRmCdnCustomDomain` dodaje niestandardową nazwę domeny do istniejącego punktu końcowego.

> [!IMPORTANT]
> Należy zdefiniować CNAME przy pomocy dostawcy usługi DNS zgodnie z opisem w [jak zamapować niestandardową domenę na punkt końcowy sieci dostarczania zawartości (CDN)](cdn-map-content-to-custom-domain.md).  Można przetestować mapowania przed zmodyfikowaniem przy użyciu punktu końcowego `Test-AzureRmCdnCustomDomain`.
> 
> 

```powershell
# Get an existing endpoint
$endpoint = Get-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Check the mapping
$result = Test-AzureRmCdnCustomDomain -CdnEndpoint $endpoint -CustomDomainHostName "cdn.contoso.com"

# Create the custom domain on the endpoint
If($result.CustomDomainValidated){ New-AzureRmCdnCustomDomain -CustomDomainName Contoso -HostName "cdn.contoso.com" -CdnEndpoint $endpoint }
```

## <a name="modifying-an-endpoint"></a>Modyfikowanie punktu końcowego
`Set-AzureRmCdnEndpoint` Modyfikuje istniejący punkt końcowy.

```powershell
# Get an existing endpoint
$endpoint = Get-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Set up content compression
$endpoint.IsCompressionEnabled = $true
$endpoint.ContentTypesToCompress = "text/javascript","text/css","application/json"

# Save the changed endpoint and apply the changes
Set-AzureRmCdnEndpoint -CdnEndpoint $endpoint
```

## <a name="purgingpre-loading-cdn-assets"></a>Przeczyszczanie/wstępnie przygotowany-loading CDN zasoby
`Unpublish-AzureRmCdnEndpointContent` powoduje usunięcie buforowanych zasobów, podczas gdy `Publish-AzureRmCdnEndpointContent` wstępnie ładuje zasoby na obsługiwanych punktów końcowych.

```powershell
# Purge some assets.
Unpublish-AzureRmCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -PurgeContent "/images/kitten.png","/video/rickroll.mp4"

# Pre-load some assets.
Publish-AzureRmCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -LoadContent "/images/kitten.png","/video/rickroll.mp4"

# Purge everything in /images/ on all endpoints.
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Unpublish-AzureRmCdnEndpointContent -PurgeContent "/images/*"
```

## <a name="startingstopping-cdn-endpoints"></a>Punktów końcowych uruchamiania/zatrzymywania usługi CDN
`Start-AzureRmCdnEndpoint` i `Stop-AzureRmCdnEndpoint` może służyć do uruchamiania i zatrzymywania poszczególnych punktów końcowych lub grupy punktów końcowych.

```powershell
# Stop the cdndocdemo endpoint
Stop-AzureRmCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Stop all endpoints
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Stop-AzureRmCdnEndpoint

# Start all endpoints
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Start-AzureRmCdnEndpoint
```

## <a name="deleting-cdn-resources"></a>Usuwanie zasobów w sieci CDN
`Remove-AzureRmCdnProfile` i `Remove-AzureRmCdnEndpoint` może służyć do usunięcia profilów i punktów końcowych.

```powershell
# Remove a single endpoint
Remove-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Remove all the endpoints on a profile and skip confirmation (-Force)
Get-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG | Get-AzureRmCdnEndpoint | Remove-AzureRmCdnEndpoint -Force

# Remove a single profile
Remove-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG
```

## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak zautomatyzować usługę Azure CDN przy użyciu platformy [.NET](cdn-app-dev-net.md) lub [Node.js](cdn-app-dev-node.md).

Aby dowiedzieć się więcej o funkcjach usługi CDN, zobacz [Omówienie usługi CDN](cdn-overview.md).

