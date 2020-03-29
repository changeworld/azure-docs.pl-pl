---
title: Tworzenie zasobu czytnika immersyjnego
titleSuffix: Azure Cognitive Services
description: W tym artykule pokazano, jak utworzyć nowy zasób programu Immersive Reader z niestandardową poddomeną, a następnie skonfigurować usługę Azure AD w dzierżawie platformy Azure.
services: cognitive-services
author: rwaller
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: rwaller
ms.openlocfilehash: 41efe4592c65ae3cdd85ce1b212554e50691905a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78330723"
---
# <a name="create-an-immersive-reader-resource-and-configure-azure-active-directory-authentication"></a>Tworzenie zasobu programu Immersive Reader i konfigurowanie uwierzytelniania usługi Azure Active Directory

W tym artykule udostępniamy skrypt, który utworzy zasób programu Immersive Reader i skonfiguruje uwierzytelnianie usługi Azure Active Directory (Azure AD). Za każdym razem, gdy tworzony jest zasób programu Immersive Reader, czy to za pomocą tego skryptu, czy w portalu, musi być również skonfigurowany z uprawnieniami usługi Azure AD. Ten skrypt ci w tym pomoże.

Skrypt został zaprojektowany do tworzenia i konfigurowania wszystkich niezbędnych immersive reader i zasobów usługi Azure AD dla Ciebie wszystkich w jednym kroku. Jednak można również skonfigurować uwierzytelnianie usługi Azure AD dla istniejącego zasobu programu Immersive Reader, jeśli na przykład, zdarzy ci się już utworzyć jeden w witrynie Azure portal.

W przypadku niektórych klientów może być konieczne utworzenie wielu zasobów programu Immersive Reader, dla rozwoju i produkcji lub być może dla wielu różnych regionów, w których usługa jest wdrażana. W takich przypadkach można wrócić i użyć skryptu wiele razy, aby utworzyć różne zasoby programu Immersive Reader i uzyskać je skonfigurowane przy użyciu uprawnień usługi Azure AD.

Skrypt został zaprojektowany tak, aby był elastyczny. Najpierw będzie szukać istniejących zasobów Immersive Reader i usługi Azure AD w ramach subskrypcji i utworzyć je tylko w razie potrzeby, jeśli jeszcze nie istnieją. Jeśli po raz pierwszy tworzysz zasób Immersive Reader, skrypt zrobi wszystko, czego potrzebujesz. Jeśli chcesz go używać tylko do skonfigurowania usługi Azure AD dla istniejącego zasobu immersive reader, który został utworzony w portalu, zrobi to również. Może być również używany do tworzenia i konfigurowania wielu zasobów immersive reader.

## <a name="set-up-powershell-environment"></a>Konfigurowanie środowiska programu PowerShell

1. Zacznij od otwarcia powłoki [chmury azure](https://docs.microsoft.com/azure/cloud-shell/overview). Upewnij się, że powłoka chmury jest ustawiona na program PowerShell w lewym górnym rogu listy rozwijanej lub wpisując `pwsh`.

1. Skopiuj i wklej następujący fragment kodu do powłoki.

    ```azurepowershell-interactive
    function Create-ImmersiveReaderResource(
        [Parameter(Mandatory=$true, Position=0)] [String] $SubscriptionName,
        [Parameter(Mandatory=$true)] [String] $ResourceName,
        [Parameter(Mandatory=$true)] [String] $ResourceSubdomain,
        [Parameter(Mandatory=$true)] [String] $ResourceSKU,
        [Parameter(Mandatory=$true)] [String] $ResourceLocation,
        [Parameter(Mandatory=$true)] [String] $ResourceGroupName,
        [Parameter(Mandatory=$true)] [String] $ResourceGroupLocation,
        [Parameter(Mandatory=$true)] [String] $AADAppDisplayName="ImmersiveReaderAAD",
        [Parameter(Mandatory=$true)] [String] $AADAppIdentifierUri,
        [Parameter(Mandatory=$true)] [String] $AADAppClientSecret
    )
    {
        $unused = ''
        if (-not [System.Uri]::TryCreate($AADAppIdentifierUri, [System.UriKind]::Absolute, [ref] $unused)) {
            throw "Error: AADAppIdentifierUri must be a valid URI"
        }

        Write-Host "Setting the active subscription to '$SubscriptionName'"
        $subscriptionExists = Get-AzSubscription -SubscriptionName $SubscriptionName
        if (-not $subscriptionExists) {
            throw "Error: Subscription does not exist"
        }
        az account set --subscription $SubscriptionName

        $resourceGroupExists = az group exists --name $ResourceGroupName
        if ($resourceGroupExists -eq "false") {
            Write-Host "Resource group does not exist. Creating resource group"
            $groupResult = az group create --name $ResourceGroupName --location $ResourceGroupLocation
            if (-not $groupResult) {
                throw "Error: Failed to create resource group"
            }
            Write-Host "Resource group created successfully"
        }

        # Create an Immersive Reader resource if it doesn't already exist
        $resourceId = az cognitiveservices account show --resource-group $ResourceGroupName --name $ResourceName --query "id" -o tsv
        if (-not $resourceId) {
            Write-Host "Creating the new Immersive Reader resource '$ResourceName' (SKU '$ResourceSKU') in '$ResourceLocation' with subdomain '$ResourceSubdomain'"
            $resourceId = az cognitiveservices account create `
                            --name $ResourceName `
                            --resource-group $ResourceGroupName `
                            --kind ImmersiveReader `
                            --sku $ResourceSKU `
                            --location $ResourceLocation `
                            --custom-domain $ResourceSubdomain `
                            --query "id" `
                            -o tsv

            if (-not $resourceId) {
                throw "Error: Failed to create Immersive Reader resource"
            }
            Write-Host "Immersive Reader resource created successfully"
        }

        # Create an Azure Active Directory app if it doesn't already exist
        $clientId = az ad app show --id $AADAppIdentifierUri --query "appId" -o tsv
        if (-not $clientId) {
            Write-Host "Creating new Azure Active Directory app"
            $clientId = az ad app create --password $AADAppClientSecret --display-name $AADAppDisplayName --identifier-uris $AADAppIdentifierUri --query "appId" -o tsv

            if (-not $clientId) {
                throw "Error: Failed to create Azure Active Directory app"
            }
            Write-Host "Azure Active Directory app created successfully"
        }

        # Create a service principal if it doesn't already exist
        $principalId = az ad sp show --id $AADAppIdentifierUri --query "objectId" -o tsv
        if (-not $principalId) {
            Write-Host "Creating new service principal"
            az ad sp create --id $clientId | Out-Null
            $principalId = az ad sp show --id $AADAppIdentifierUri --query "objectId" -o tsv

            if (-not $principalId) {
                throw "Error: Failed to create new service principal"
            }
            Write-Host "New service principal created successfully"
        }

        # Sleep for 5 seconds to allow the new service principal to propagate
        Write-Host "Sleeping for 5 seconds"
        Start-Sleep -Seconds 5

        Write-Host "Granting service principal access to the newly created Immersive Reader resource"
        $accessResult = az role assignment create --assignee $principalId --scope $resourceId --role "Cognitive Services User"
        if (-not $accessResult) {
            throw "Error: Failed to grant service principal access"
        }
        Write-Host "Service principal access granted successfully"

        # Grab the tenant ID, which is needed when obtaining an Azure AD token
        $tenantId = az account show --query "tenantId" -o tsv

        # Collect the information needed to obtain an Azure AD token into one object
        $result = @{}
        $result.TenantId = $tenantId
        $result.ClientId = $clientId
        $result.ClientSecret = $AADAppClientSecret
        $result.Subdomain = $ResourceSubdomain

        Write-Host "Success! " -ForegroundColor Green -NoNewline
        Write-Host "Save the following JSON object to a text file for future reference:"
        Write-Output (ConvertTo-Json $result)
    }
    ```

1. Uruchom funkcję, `Create-ImmersiveReaderResource`podając odpowiednie parametry.

    ```azurepowershell-interactive
    Create-ImmersiveReaderResource
      -SubscriptionName '<SUBSCRIPTION_NAME>' `
      -ResourceName '<RESOURCE_NAME>' `
      -ResourceSubdomain '<RESOURCE_SUBDOMAIN>' `
      -ResourceSKU '<RESOURCE_SKU>' `
      -ResourceLocation '<RESOURCE_LOCATION>' `
      -ResourceGroupName '<RESOURCE_GROUP_NAME>' `
      -ResourceGroupLocation '<RESOURCE_GROUP_LOCATION>' `
      -AADAppDisplayName '<AAD_APP_DISPLAY_NAME>' `
      -AADAppIdentifierUri '<AAD_APP_IDENTIFIER_URI>' `
      -AADAppClientSecret '<AAD_APP_CLIENT_SECRET>'
    ```

    | Parametr | Komentarze |
    | --- | --- |
    | SubscriptionName |Nazwa subskrypcji platformy Azure do użycia w zasobie programu Immersive Reader. Aby utworzyć zasób, musisz mieć subskrypcję. |
    | ResourceName |  Musi być alfanumeryczny i może zawierać '-', o ile '-' nie jest pierwszym lub ostatnim znakiem. Długość nie może przekraczać 63 znaków.|
    | ResourceSubdomena |Niestandardowa poddomena jest potrzebna dla zasobu Immersive Reader. Poddomena jest używana przez SDK podczas wywoływania usługi Immersive Reader w celu uruchomienia czytnika. Poddomena musi być unikatowa globalnie. Poddomena musi być alfanumeryczna i może zawierać '-', o ile '-' nie jest pierwszym lub ostatnim znakiem. Długość nie może przekraczać 63 znaków. Ten parametr jest opcjonalny, jeśli zasób już istnieje. |
    | ResourceSKU |Opcje: `S0`. Odwiedź naszą [stronę cennika usług Cognitive Services,](https://azure.microsoft.com/pricing/details/cognitive-services/immersive-reader/) aby dowiedzieć się więcej o każdej dostępnej jednostce SKU. Ten parametr jest opcjonalny, jeśli zasób już istnieje. |
    | ResourceLocation |Opcje: `eastus` `eastus2`, `southcentralus` `westus`, `westus2` `australiaeast`, `southeastasia` `centralindia`, `japaneast` `northeurope`, `uksouth` `westeurope`, , , , , , . Ten parametr jest opcjonalny, jeśli zasób już istnieje. |
    | ResourceGroupName |Zasoby są tworzone w grupach zasobów w ramach subskrypcji. Podaj nazwę istniejącej grupy zasobów. Jeśli grupa zasobów jeszcze nie istnieje, zostanie utworzona nowa grupa o tej nazwie. |
    | ResourceGroupLocation (Alokacja grupy zasobów) |Jeśli grupa zasobów nie istnieje, należy podać lokalizację, w której chcesz utworzyć grupę. Aby znaleźć listę lokalizacji, `az account list-locations`uruchom plik . Użyj właściwości *name* (bez spacji) zwracanego wyniku. Ten parametr jest opcjonalny, jeśli grupa zasobów już istnieje. |
    | Nazwa AADAppDisplayName |Nazwa wyświetlana aplikacji usługi Azure Active Directory. Jeśli nie zostanie znaleziona istniejąca aplikacja usługi Azure AD, zostanie utworzona nowa aplikacja o tej nazwie. Ten parametr jest opcjonalny, jeśli aplikacja usługi Azure AD już istnieje. |
    | AADAppIdentifierUri |Identyfikator URI dla aplikacji usługi Azure AD. Jeśli nie zostanie znaleziona istniejąca aplikacja usługi Azure AD, zostanie utworzona nowa aplikacja z tym identyfikatorem URI. Na przykład `https://immersivereaderaad-mycompany`. |
    | AADAppClientSecret (Protokół z okiem na akces |Utworzone hasło, które będzie używane później do uwierzytelniania podczas uzyskiwania tokenu w celu uruchomienia programu Immersive Reader. Hasło musi mieć co najmniej 16 znaków, zawierać co najmniej 1 znak specjalny i zawierać co najmniej 1 znak numeryczny. |

1. Skopiuj dane wyjściowe JSON do pliku tekstowego do późniejszego użycia. Dane wyjściowe powinny wyglądać podobnie do poniższych.

    ```json
    {
      "TenantId": "...",
      "ClientId": "...",
      "ClientSecret": "...",
      "Subdomain": "..."
    }
    ```

## <a name="next-steps"></a>Następne kroki

* Wyświetl [przewodnik Szybki start w pliku Node.js,](./quickstart-nodejs.md) aby zobaczyć, co jeszcze można zrobić z modułem Immersive Reader SDK przy użyciu pliku Node.js
* Zobacz [samouczek Pythona,](./tutorial-python.md) aby zobaczyć, co jeszcze możesz zrobić z zestawu Immersive Reader SDK przy użyciu języka Python
* Zobacz [samouczek Swift,](./tutorial-ios-picture-immersive-reader.md) aby zobaczyć, co jeszcze możesz zrobić z zestawu Immersive Reader SDK za pomocą programu Swift
* Poznaj [immersyjny moduł SDK czytnika](https://github.com/microsoft/immersive-reader-sdk) i [immersyjny moduł SDK czytnika](./reference.md)




