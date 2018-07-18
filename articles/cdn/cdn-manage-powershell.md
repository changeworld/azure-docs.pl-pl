---
title: Zarządzać usługą CDN Azure przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzać usługą Azure CDN za pomocą poleceń cmdlet programu Azure PowerShell.
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
ms.date: 07/17/2018
ms.author: mazha
ms.openlocfilehash: 15feb7b1d2873bc3f088eaad78079df2e063d73b
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2018
ms.locfileid: "39114076"
---
# <a name="manage-azure-cdn-with-powershell"></a>Zarządzać usługą CDN Azure przy użyciu programu PowerShell
Program PowerShell udostępnia jedną z najbardziej elastyczny metod do zarządzania profilami usługi Azure CDN i punktów końcowych.  Można użyć programu PowerShell interaktywnie lub pisząc skrypty do automatyzacji zadań zarządzania.  W tym samouczku przedstawiono kilka typowych zadań można wykonać za pomocą programu PowerShell do zarządzania profilami usługi Azure CDN i punktów końcowych.

## <a name="prerequisites"></a>Wymagania wstępne
Zarządzanie profilami usługi Azure CDN i punktów końcowych przy użyciu programu PowerShell, musi mieć zainstalowany moduł Azure PowerShell.  Aby dowiedzieć się, jak zainstalować program Azure PowerShell i nawiązać połączenie z platformy Azure za pomocą `Connect-AzureRmAccount` polecenia cmdlet, zobacz [jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/overview).

> [!IMPORTANT]
> Musisz zalogować się przy użyciu `Connect-AzureRmAccount` przed wykonaniem poleceń cmdlet programu Azure PowerShell.
> 
> 

## <a name="listing-the-azure-cdn-cmdlets"></a>Lista poleceń cmdlet usługi Azure CDN
Możesz wyświetlić listę wszystkich poleceń cmdlet usługi Azure CDN, za pomocą `Get-Command` polecenia cmdlet.

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
Możesz uzyskać pomoc przy użyciu dowolnego z tych poleceń cmdlet, za pomocą `Get-Help` polecenia cmdlet.  `Get-Help` Umożliwia użycie i składnię i opcjonalnie zawiera przykłady.

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
`Get-AzureRmCdnProfile` Polecenia cmdlet bez parametrów umożliwia pobranie wszystkich istniejących profilów CDN.

```powershell
Get-AzureRmCdnProfile
```

Te dane wyjściowe mogą być przesyłane potokiem do poleceń cmdlet dla wyliczenia.

```powershell
# Output the name of all profiles on this subscription.
Get-AzureRmCdnProfile | ForEach-Object { Write-Host $_.Name }

# Return only **Azure CDN from Verizon** profiles.
Get-AzureRmCdnProfile | Where-Object { $_.Sku.Name -eq "Standard_Verizon" }
```

Może również zwracać jeden profil, określając profil nazwą i grupą zasobów.

```powershell
Get-AzureRmCdnProfile -ProfileName CdnDemo -ResourceGroupName CdnDemoRG
```

> [!TIP]
> Użytkownik może mieć wiele profilów CDN o takiej samej nazwie, tak długo, jak są one w różnych grupach zasobów.  Pominięcie `ResourceGroupName` parametr zwraca wszystkie profile z takiej samej nazwie.
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

## <a name="creating-cdn-profiles-and-endpoints"></a>Tworzenie profilów CDN i punktów końcowych
`New-AzureRmCdnProfile` i `New-AzureRmCdnEndpoint` są używane do tworzenia profilów usługi CDN i punktów końcowych. Obsługiwane są następujące jednostki SKU:
- Standard_Verizon
- Premium_Verizon
- Custom_Verizon
- Standard_Akamai
- Standard_ChinaCdn

> [!NOTE]
> Jednostka SKU Standard_Microsoft nie jest obsługiwana, gdy znajduje się w wersji zapoznawczej.

```powershell
# Create a new profile
New-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku Standard_Akamai -Location "Central US"

# Create a new endpoint
New-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Location "Central US" -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

# Create a new profile and endpoint (same as above) in one line
New-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku Standard_Akamai -Location "Central US" | New-AzureRmCdnEndpoint -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

```

## <a name="checking-endpoint-name-availability"></a>Trwa sprawdzanie dostępności nazwy punktu końcowego
`Get-AzureRmCdnEndpointNameAvailability` Zwraca obiekt, wskazującą, czy nazwa punktu końcowego jest dostępna.

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
> Należy skonfigurować rekord CNAME u swojego dostawcy DNS zgodnie z opisem w [sposób mapowania niestandardowej domeny na punkt końcowy Content Delivery Network (CDN)](cdn-map-content-to-custom-domain.md).  Można przetestować mapowania przed zmodyfikowaniem przy użyciu punktu końcowego `Test-AzureRmCdnCustomDomain`.
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

## <a name="purgingpre-loading-cdn-assets"></a>Przeczyszczanie/wstępnej-loading CDN zasobów
`Unpublish-AzureRmCdnEndpointContent` powoduje usunięcie buforowanych zasobów, podczas gdy `Publish-AzureRmCdnEndpointContent` wstępnie ładuje zasoby w obsługiwanych punktów końcowych.

```powershell
# Purge some assets.
Unpublish-AzureRmCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -PurgeContent "/images/kitten.png","/video/rickroll.mp4"

# Pre-load some assets.
Publish-AzureRmCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -LoadContent "/images/kitten.png","/video/rickroll.mp4"

# Purge everything in /images/ on all endpoints.
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Unpublish-AzureRmCdnEndpointContent -PurgeContent "/images/*"
```

## <a name="startingstopping-cdn-endpoints"></a>Punkty końcowe uruchamiania/zatrzymywania usługi CDN
`Start-AzureRmCdnEndpoint` i `Stop-AzureRmCdnEndpoint` może służyć do uruchamiania i zatrzymywania poszczególne punkty końcowe lub grupy punktów końcowych.

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

Aby dowiedzieć się więcej na temat funkcji usługi CDN, zobacz [Omówienie usługi CDN](cdn-overview.md).

