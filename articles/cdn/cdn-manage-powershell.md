---
title: Zarządzać usługą CDN Azure przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzać usługą Azure CDN za pomocą poleceń cmdlet programu Azure PowerShell.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: fb6f57a5-6e26-4847-8fd9-b51fb05a79eb
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2018
ms.author: magattus
ms.openlocfilehash: 44274c2a46ce51e51d3d8f16d96a0b50c43a3aa1
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593706"
---
# <a name="manage-azure-cdn-with-powershell"></a>Zarządzać usługą CDN Azure przy użyciu programu PowerShell
Program PowerShell udostępnia jedną z najbardziej elastyczny metod do zarządzania profilami usługi Azure CDN i punktów końcowych.  Można użyć programu PowerShell interaktywnie lub pisząc skrypty do automatyzacji zadań zarządzania.  W tym samouczku przedstawiono kilka typowych zadań można wykonać za pomocą programu PowerShell do zarządzania profilami usługi Azure CDN i punktów końcowych.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Zarządzanie profilami usługi Azure CDN i punktów końcowych przy użyciu programu PowerShell, musi mieć zainstalowany moduł Azure PowerShell.  Aby dowiedzieć się, jak zainstalować program Azure PowerShell i nawiązać połączenie z platformy Azure za pomocą `Connect-AzAccount` polecenia cmdlet, zobacz [jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/overview).

> [!IMPORTANT]
> Musisz zalogować się przy użyciu `Connect-AzAccount` przed wykonaniem poleceń cmdlet programu Azure PowerShell.
> 
> 

## <a name="listing-the-azure-cdn-cmdlets"></a>Lista poleceń cmdlet usługi Azure CDN
Możesz wyświetlić listę wszystkich poleceń cmdlet usługi Azure CDN, za pomocą `Get-Command` polecenia cmdlet.

```text
PS C:\> Get-Command -Module Az.Cdn

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Get-AzCdnCustomDomain                         2.0.0      Az.Cdn
Cmdlet          Get-AzCdnEndpoint                             2.0.0      Az.Cdn
Cmdlet          Get-AzCdnEndpointNameAvailability             2.0.0      Az.Cdn
Cmdlet          Get-AzCdnOrigin                               2.0.0      Az.Cdn
Cmdlet          Get-AzCdnProfile                              2.0.0      Az.Cdn
Cmdlet          Get-AzCdnProfileSsoUrl                        2.0.0      Az.Cdn
Cmdlet          New-AzCdnCustomDomain                         2.0.0      Az.Cdn
Cmdlet          New-AzCdnEndpoint                             2.0.0      Az.Cdn
Cmdlet          New-AzCdnProfile                              2.0.0      Az.Cdn
Cmdlet          Publish-AzCdnEndpointContent                  2.0.0      Az.Cdn
Cmdlet          Remove-AzCdnCustomDomain                      2.0.0      Az.Cdn
Cmdlet          Remove-AzCdnEndpoint                          2.0.0      Az.Cdn
Cmdlet          Remove-AzCdnProfile                           2.0.0      Az.Cdn
Cmdlet          Set-AzCdnEndpoint                             2.0.0      Az.Cdn
Cmdlet          Set-AzCdnOrigin                               2.0.0      Az.Cdn
Cmdlet          Set-AzCdnProfile                              2.0.0      Az.Cdn
Cmdlet          Start-AzCdnEndpoint                           2.0.0      Az.Cdn
Cmdlet          Stop-AzCdnEndpoint                            2.0.0      Az.Cdn
Cmdlet          Test-AzCdnCustomDomain                        2.0.0      Az.Cdn
Cmdlet          Unpublish-AzCdnEndpointContent                2.0.0      Az.Cdn
```

## <a name="getting-help"></a>Uzyskiwanie pomocy
Możesz uzyskać pomoc przy użyciu dowolnego z tych poleceń cmdlet, za pomocą `Get-Help` polecenia cmdlet.  `Get-Help` Umożliwia użycie i składnię i opcjonalnie zawiera przykłady.

```text
PS C:\> Get-Help Get-AzCdnProfile

NAME
    Get-AzCdnProfile

SYNOPSIS
    Gets an Azure CDN profile.


SYNTAX
    Get-AzCdnProfile [-ProfileName <String>] [-ResourceGroupName <String>] [-InformationAction
    <ActionPreference>] [-InformationVariable <String>] [<CommonParameters>]


DESCRIPTION
    Gets an Azure CDN profile and all related information.


RELATED LINKS

REMARKS
    To see the examples, type: "get-help Get-AzCdnProfile -examples".
    For more information, type: "get-help Get-AzCdnProfile -detailed".
    For technical information, type: "get-help Get-AzCdnProfile -full".

```

## <a name="listing-existing-azure-cdn-profiles"></a>Lista istniejących profilów usługi Azure CDN
`Get-AzCdnProfile` Polecenia cmdlet bez parametrów umożliwia pobranie wszystkich istniejących profilów CDN.

```powershell
Get-AzCdnProfile
```

Te dane wyjściowe mogą być przesyłane potokiem do poleceń cmdlet dla wyliczenia.

```powershell
# Output the name of all profiles on this subscription.
Get-AzCdnProfile | ForEach-Object { Write-Host $_.Name }

# Return only **Azure CDN from Verizon** profiles.
Get-AzCdnProfile | Where-Object { $_.Sku.Name -eq "Standard_Verizon" }
```

Może również zwracać jeden profil, określając profil nazwą i grupą zasobów.

```powershell
Get-AzCdnProfile -ProfileName CdnDemo -ResourceGroupName CdnDemoRG
```

> [!TIP]
> Użytkownik może mieć wiele profilów CDN o takiej samej nazwie, tak długo, jak są one w różnych grupach zasobów.  Pominięcie `ResourceGroupName` parametr zwraca wszystkie profile z takiej samej nazwie.
> 
> 

## <a name="listing-existing-cdn-endpoints"></a>Lista istniejących punktów końcowych usługi CDN
`Get-AzCdnEndpoint` można pobrać punktu końcowego poszczególnych lub wszystkich punktów końcowych dla profilu.  

```powershell
# Get a single endpoint.
Get-AzCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Get all of the endpoints on a given profile. 
Get-AzCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG

# Return all of the endpoints on all of the profiles.
Get-AzCdnProfile | Get-AzCdnEndpoint

# Return all of the endpoints in this subscription that are currently running.
Get-AzCdnProfile | Get-AzCdnEndpoint | Where-Object { $_.ResourceState -eq "Running" }
```

## <a name="creating-cdn-profiles-and-endpoints"></a>Tworzenie profilów CDN i punktów końcowych
`New-AzCdnProfile` i `New-AzCdnEndpoint` są używane do tworzenia profilów usługi CDN i punktów końcowych. Obsługiwane są następujące jednostki SKU:
- Standard_Verizon
- Premium_Verizon
- Custom_Verizon
- Standard_Akamai
- Standard_Microsoft
- Standard_ChinaCdn

```powershell
# Create a new profile
New-AzCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku Standard_Akamai -Location "Central US"

# Create a new endpoint
New-AzCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Location "Central US" -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

# Create a new profile and endpoint (same as above) in one line
New-AzCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku Standard_Akamai -Location "Central US" | New-AzCdnEndpoint -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

```

## <a name="checking-endpoint-name-availability"></a>Trwa sprawdzanie dostępności nazwy punktu końcowego
`Get-AzCdnEndpointNameAvailability` Zwraca obiekt, wskazującą, czy nazwa punktu końcowego jest dostępna.

```powershell
# Retrieve availability
$availability = Get-AzCdnEndpointNameAvailability -EndpointName "cdnposhdoc"

# If available, write a message to the console.
If($availability.NameAvailable) { Write-Host "Yes, that endpoint name is available." }
Else { Write-Host "No, that endpoint name is not available." }
```

## <a name="adding-a-custom-domain"></a>Dodawanie domeny niestandardowej
`New-AzCdnCustomDomain` dodaje niestandardową nazwę domeny do istniejącego punktu końcowego.

> [!IMPORTANT]
> Należy skonfigurować rekord CNAME u swojego dostawcy DNS zgodnie z opisem w [sposób mapowania niestandardowej domeny na punkt końcowy Content Delivery Network (CDN)](cdn-map-content-to-custom-domain.md).  Można przetestować mapowania przed zmodyfikowaniem przy użyciu punktu końcowego `Test-AzCdnCustomDomain`.
> 
> 

```powershell
# Get an existing endpoint
$endpoint = Get-AzCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Check the mapping
$result = Test-AzCdnCustomDomain -CdnEndpoint $endpoint -CustomDomainHostName "cdn.contoso.com"

# Create the custom domain on the endpoint
If($result.CustomDomainValidated){ New-AzCdnCustomDomain -CustomDomainName Contoso -HostName "cdn.contoso.com" -CdnEndpoint $endpoint }
```

## <a name="modifying-an-endpoint"></a>Modyfikowanie punktu końcowego
`Set-AzCdnEndpoint` Modyfikuje istniejący punkt końcowy.

```powershell
# Get an existing endpoint
$endpoint = Get-AzCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Set up content compression
$endpoint.IsCompressionEnabled = $true
$endpoint.ContentTypesToCompress = "text/javascript","text/css","application/json"

# Save the changed endpoint and apply the changes
Set-AzCdnEndpoint -CdnEndpoint $endpoint
```

## <a name="purgingpre-loading-cdn-assets"></a>Przeczyszczanie/wstępnej-loading CDN zasobów
`Unpublish-AzCdnEndpointContent` powoduje usunięcie buforowanych zasobów, podczas gdy `Publish-AzCdnEndpointContent` wstępnie ładuje zasoby w obsługiwanych punktów końcowych.

```powershell
# Purge some assets.
Unpublish-AzCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -PurgeContent "/images/kitten.png","/video/rickroll.mp4"

# Pre-load some assets.
Publish-AzCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -LoadContent "/images/kitten.png","/video/rickroll.mp4"

# Purge everything in /images/ on all endpoints.
Get-AzCdnProfile | Get-AzCdnEndpoint | Unpublish-AzCdnEndpointContent -PurgeContent "/images/*"
```

## <a name="startingstopping-cdn-endpoints"></a>Punkty końcowe uruchamiania/zatrzymywania usługi CDN
`Start-AzCdnEndpoint` i `Stop-AzCdnEndpoint` może służyć do uruchamiania i zatrzymywania poszczególne punkty końcowe lub grupy punktów końcowych.

```powershell
# Stop the cdndocdemo endpoint
Stop-AzCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Stop all endpoints
Get-AzCdnProfile | Get-AzCdnEndpoint | Stop-AzCdnEndpoint

# Start all endpoints
Get-AzCdnProfile | Get-AzCdnEndpoint | Start-AzCdnEndpoint
```

## <a name="deleting-cdn-resources"></a>Usuwanie zasobów w sieci CDN
`Remove-AzCdnProfile` i `Remove-AzCdnEndpoint` może służyć do usunięcia profilów i punktów końcowych.

```powershell
# Remove a single endpoint
Remove-AzCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Remove all the endpoints on a profile and skip confirmation (-Force)
Get-AzCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG | Get-AzCdnEndpoint | Remove-AzCdnEndpoint -Force

# Remove a single profile
Remove-AzCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG
```

## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak zautomatyzować usługę Azure CDN przy użyciu platformy [.NET](cdn-app-dev-net.md) lub [Node.js](cdn-app-dev-node.md).

Aby dowiedzieć się więcej na temat funkcji usługi CDN, zobacz [Omówienie usługi CDN](cdn-overview.md).

