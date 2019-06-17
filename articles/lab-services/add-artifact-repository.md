---
title: Dodawanie repozytorium artefaktów do laboratorium Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodać repozytorium artefaktów do laboratorium w usłudze Azure DevTest labs.
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2019
ms.author: spelluru
ms.openlocfilehash: 7ff036fbdf3ae9360bed8d728b9bec3a1937b70a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66808246"
---
# <a name="add-an-artifact-repository-to-your-lab-in-devtest-labs"></a>Dodawanie repozytorium artefaktów do laboratorium w usłudze DevTest Labs
DevTest Labs umożliwia określenie artefaktu ma zostać dodany do maszyny Wirtualnej w czasie tworzenia maszyny Wirtualnej lub po utworzeniu maszyny Wirtualnej. Ten artefakt może być narzędziem lub aplikacji, którą chcesz zainstalować na maszynie Wirtualnej. Artefakty są zdefiniowane w pliku JSON załadowane z repozytorium GitHub lub DevOps Git platformy Azure. 

[Publiczne repozytorium artefaktów](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts), obsługiwane przez usługi DevTest Labs, udostępnia wiele popularnych narzędzi dla systemów Windows i Linux. Łącze do tego repozytorium jest automatycznie dodawane do środowiska laboratoryjnego. Repozytorium artefaktów można utworzyć przy użyciu określonego narzędzia, które nie są dostępne w publiczne repozytorium artefaktów. Aby dowiedzieć się więcej na temat tworzenia niestandardowych artefaktów, zobacz [Tworzenie niestandardowych artefaktów](devtest-lab-artifact-author.md).

Ten artykuł zawiera informacje na temat dodawania niestandardowego artefaktu repozytorium przy użyciu witryny Azure portal, szablonów usługi Azure Resource Management i Azure PowerShell. Aby zautomatyzować Dodawanie repozytorium artefaktów do laboratorium, pisząc skrypty programu PowerShell lub interfejsu wiersza polecenia. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne
Aby dodać repozytorium do środowiska laboratoryjnego, należy najpierw Pobierz informacje o kluczu ze swojego repozytorium. W poniższych sekcjach opisano, jak można pobrać informacji wymaganych w przypadku repozytoriów hostowanych na **GitHub** lub **DevOps platformy Azure**.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>Pobierz adres URL klonowania repozytorium GitHub i pat token

1. Przejdź do strony głównej w repozytorium GitHub zawierającego artefaktu lub definicje szablonów usługi Resource Manager.
2. Wybierz przycisk **Clone or download** (Sklonuj lub pobierz).
3. Aby skopiować adres URL do Schowka, zaznacz **adres url klonowania HTTPS** przycisku. Zapisz adres URL do późniejszego użycia.
4. W prawym górnym rogu usługi GitHub wybierz obraz profilu, a następnie wybierz **ustawienia**.
5. W **ustawienia osobiste** menu po lewej stronie, wybierz opcję **ustawienia dewelopera**.
6. Wybierz **osobiste tokeny dostępu** w menu po lewej stronie.
7. Wybierz **Wygeneruj nowy token**.
8. Na **nowe osobisty token dostępu** w obszarze **opis tokenu**, wprowadź opis. Zaakceptuj domyślne elementy w obszarze **wybierz zakresy**, a następnie wybierz pozycję **Generuj Token**.
9. Zapisz wygenerowany token. Przy użyciu tokenu później.
10. Zamknij usługi GitHub.   

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>Uzyskaj klonowanie repozytoriów platformy Azure, adres URL i osobistego tokenu dostępu
1. Przejdź do strony głównej zespołu kolekcji (na przykład https://contoso-web-team.visualstudio.com), a następnie wybierz swój projekt.
2. Na stronie głównej projektu wybierz **kodu**.
3. Aby wyświetlić adres URL klonowania w projekcie **kodu** wybierz opcję **klonowania**.
4. Zapisz adres URL. Adres URL można użyć później.
5. Aby utworzyć osobisty token dostępu, w menu rozwijanego konta użytkownika, wybierz **Mój profil**.
6. Na stronie informacje o profilu, wybierz **zabezpieczeń**.
7. Na **zabezpieczeń** zaznacz **Dodaj**.
8. Na **utworzyć osobisty token dostępu** strony:
   1. Wprowadź **opis** dla tokenu.
   2. W **wygasa w** listy wybierz **180 dni**.
   3. W **kont** listy wybierz **wszystkich dostępnych kont**.
   4. Wybierz **wszystkie zakresy** opcji.
   5. Wybierz **Utwórz Token**.
9. Nowy token, który pojawia się w **osobistych tokenów dostępu** listy. Wybierz **kopiowania tokenu**, a następnie zapisz wartość tokenu w celu późniejszego użycia.
10. W dalszym ciągu połączenia środowiska laboratoryjnego do sekcji repozytorium.

## <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal
Ta sekcja zawiera kroki, aby dodać repozytorium artefaktów do laboratorium w witrynie Azure portal. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz **więcej usług**, a następnie wybierz pozycję **DevTest Labs** z listy usług.
3. Zaznacz na liście laboratoriów środowiska laboratoryjnego. 
4. Wybierz **konfiguracji i zasad** w menu po lewej stronie.
5. Wybierz **repozytoriów** w obszarze **zasobów zewnętrznych** sekcji, w menu po lewej stronie.
6. Wybierz **+ Dodaj** na pasku narzędzi.

    ![Przycisk Dodaj repozytorium](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. Na **repozytoriów** określ następujące informacje:
   1. **Nazwa**. Wprowadź nazwę repozytorium.
   2. **Adres Url klonowania Git**. Wprowadź adres URL klonowania Git HTTPS, które wcześniej zostały skopiowane z usługi GitHub lub usługom DevOps platformy Azure.
   3. **Gałąź**. Aby uzyskać definicji, wprowadź gałęzi.
   4. **Osobisty Token dostępu**. Wprowadź osobisty token dostępu uzyskany wcześniej w usłudze GitHub lub usługom DevOps platformy Azure.
   5. **Ścieżka folderu**. Wprowadź co najmniej jedną ścieżkę folderu względem adres URL klonowania, który zawiera Twoje artefaktu lub definicje szablonów usługi Resource Manager. Po określeniu podkatalogu, upewnij się, że zawierają ukośnika w ścieżce folderu.

        ![Obszar repozytoriów](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. Wybierz pozycję **Zapisz**.

## <a name="use-azure-resource-manager-template"></a>Korzystanie z szablonu usługi Azure Resource Manager
Szablony usługi Azure Resource Management (Azure Resource Manager) są plikami JSON, które opisują zasoby na platformie Azure, która ma zostać utworzona. Aby uzyskać więcej informacji na temat tych szablonów, zobacz [tworzenia usługi Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

Ta sekcja zawiera kroki, aby dodać repozytorium artefaktów do laboratorium przy użyciu szablonu usługi Azure Resource Manager.  Ten szablon tworzy laboratorium, jeśli jeszcze nie istnieje. 

### <a name="template"></a>Szablon
Przykładowy szablon używane w tym artykule zbiera następujące informacje za pomocą parametrów. Większość parametry mają domyślnych ustawień inteligentnych, ale istnieje kilka wartości, które musi być określona. Należy określić nazwę laboratorium, identyfikator URI repozytorium artefaktów i token zabezpieczający, aby znaleźć repozytorium. 

- Nazwa laboratorium.
- Nazwa wyświetlana dla repozytorium artefaktów w usłudze DevTest Labs interfejsu użytkownika (UI). Wartość domyślna to: `Team Repository`.
- Identyfikator URI do repozytorium (przykład: `https://github.com/<myteam>/<nameofrepo>.git` lub `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"`.
- Gałąź w repozytorium, które zawiera artefakty. Wartość domyślna to: `master`.
- Nazwa folderu, który zawiera artefakty. Wartość domyślna to: `/Artifacts`.
- Typ repozytorium. Dozwolone wartości to `VsoGit` lub `GitHub`.
- Token dostępu dla repozytorium. 

    ```json
    {
    
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "labName": {
                "type": "string"
            },
            "artifactRepositoryDisplayName": {
                "type": "string",
                "defaultValue": "Team Repository"
            },
            "artifactRepoUri": {
                "type": "string"
            },
            "artifactRepoBranch": {
                "type": "string",
                "defaultValue": "master"
            },
            "artifactRepoFolder": {
                "type": "string",
                "defaultValue": "/Artifacts"
            },
            "artifactRepoType": {
                "type": "string",
                "allowedValues": ["VsoGit", "GitHub"]
            },
            "artifactRepoSecurityToken": {
                "type": "securestring"
            }
        },
        "variables": {
            "artifactRepositoryName": "[concat('Repo-', uniqueString(subscription().subscriptionId))]"
        },
        "resources": [{
                "apiVersion": "2016-05-15",
                "type": "Microsoft.DevTestLab/labs",
                "name": "[parameters('labName')]",
                "location": "[resourceGroup().location]",
                "resources": [
                    {
                        "apiVersion": "2016-05-15",
                        "name": "[variables('artifactRepositoryName')]",
                        "type": "artifactSources",
                        "dependsOn": [
                            "[resourceId('Microsoft.DevTestLab/labs', parameters('labName'))]"
                        ],
                        "properties": {
                            "uri": "[parameters('artifactRepoUri')]",
                            "folderPath": "[parameters('artifactRepoFolder')]",
                            "branchRef": "[parameters('artifactRepoBranch')]",
                            "displayName": "[parameters('artifactRepositoryDisplayName')]",
                            "securityToken": "[parameters('artifactRepoSecurityToken')]",
                            "sourceType": "[parameters('artifactRepoType')]",
                            "status": "Enabled"
                        }
                    }
                ]
            }
        ]
    }
    ```


### <a name="deploy-the-template"></a>Wdrożenie szablonu
Istnieje kilka sposobów wdrażania szablonu na platformie Azure i zasobów utworzony, jeśli nie istnieje lub aktualizowane, jeśli istnieje. Aby uzyskać szczegółowe informacje zobacz następujące artykuły:

- [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](../azure-resource-manager/resource-group-template-deploy.md)
- [Deploy resources with Resource Manager templates and Azure CLI (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i interfejsu wiersza polecenia platformy Azure)](../azure-resource-manager/resource-group-template-deploy-cli.md)
- [Deploy resources with Resource Manager templates and Azure portal (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i witryny Azure Portal)](../azure-resource-manager/resource-group-template-deploy-portal.md)
- [Deploy resources with Resource Manager templates and Resource Manager REST API (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i interfejsu API REST usługi Resource Manager)](../azure-resource-manager/resource-group-template-deploy-rest.md)

Rozpocznijmy i zobacz, jak wdrożyć szablon w programie PowerShell. Polecenia cmdlet używane do wdrożenia szablonu są zależne od kontekstu, więc bieżącej dzierżawy i bieżącej subskrypcji są używane. Użyj [AzContext zestaw](/powershell/module/az.accounts/set-azcontext) przed przystąpieniem do wdrażania szablonu, jeśli to konieczne, aby zmienić kontekst.

Najpierw utwórz grupę zasobów za pomocą [New AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Jeśli grupa zasobów, których chcesz użyć już istnieje, Pomiń ten krok.

```powershell
New-AzResourceGroup -Name MyLabResourceGroup1 -Location westus
```

Następnie utwórz wdrożenie do grupy zasobów za pomocą [New AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). To polecenie cmdlet ma zastosowanie zmian zasobów na platformie Azure. Kilka wdrożenia zasobów można nawiązać dowolnej danej grupy zasobów. Jeśli wdrażasz kilka razy w tej samej grupie zasobów, upewnij się, że nazwa każdego wdrożenia jest unikatowa.

```powershell
New-AzResourceGroupDeployment `
    -Name MyLabResourceGroup-Deployment1 `
    -ResourceGroupName MyLabResourceGroup1 `
    -TemplateFile azuredeploy.json `
    -TemplateParameterFile azuredeploy.parameters.json
```

Po pomyślnym uruchomieniu AzResourceGroupDeployment nowy, polecenie generuje ważne informacje, takie jak stan aprowizacji (powinna być powiodło się) i jakiekolwiek dane wyjściowe dla szablonu.
 
## <a name="use-azure-powershell"></a>Korzystanie z programu Azure PowerShell 
Ta sekcja zawiera przykładowy skrypt programu PowerShell, który może służyć do Dodawanie repozytorium artefaktów do laboratorium. Jeśli nie masz programu Azure PowerShell, zobacz [jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/overview?view=azps-1.2.0) szczegółowe instrukcje go zainstalować.

### <a name="full-script"></a>Pełny skrypt
Oto pełny skryptu, między innymi niektóre komunikaty pełne i komentarzy:

**New-DevTestLabArtifactRepository.ps1**:

```powershell

<#

.SYNOPSIS
This script creates a new custom repository and adds it to an existing DevTest Lab.

.PARAMETER LabName
The name of the lab.

.PARAMETER LabResourceGroupName
The name of the resource group that contains the lab. 

.PARAMETER ArtifactRepositoryName
Name for the new artifact repository.
Script creates a random name for the respository if it is not specified.

.PARAMETER ArtifactRepositoryDisplayName
Display name for the artifact repository.
This is the name that shows up in the Azure portal (https://portal.azure.com) when viewing all the artifact repositories for a lab.

.PARAMETER RepositoryUri
Uri to the repository.

.PARAMETER RepositoryBranch
Branch in which artifact files can be found. Defaults to 'master'.

.PARAMETER FolderPath
Folder under which artifacts can be found. Defaults to '/Artifacts'

.PARAMETER PersonalAccessToken
Security token for access to GitHub or VSOGit repository.
See https://azure.microsoft.com/documentation/articles/devtest-lab-add-artifact-repo/ for instructions to get personal access token

.PARAMETER SourceType
Whether artifact is VSOGit or GitHub repository.

.EXAMPLE
Set-AzContext -SubscriptionId 11111111-1111-1111-1111-111111111111
.\New-DevTestLabArtifactRepository.ps1 -LabName "mydevtestlab" -LabResourceGroupName "mydtlrg" -ArtifactRepositoryName "MyTeam Repository" -RepositoryUri "https://github.com/<myteam>/<nameofrepo>.git" -PersonalAccessToken "1111...." -SourceType "GitHub"

.NOTES
Script uses the current Az context. To set the context, use the Set-AzContext cmdlet

#>

 
[CmdletBinding()]
Param(

    [Parameter(Mandatory=$true)]
    $LabName,

    [Parameter(Mandatory=$true)]
    $LabResourceGroupName,
    $ArtifactRepositoryName,
    $ArtifactRepositoryDisplayName  = 'Team Artifact Repository',

    [Parameter(Mandatory=$true)]
    $RepositoryUri,
    $RepositoryBranch = 'master',
    $FolderPath = '/Artifacts',
    
    [Parameter(Mandatory=$true)]
    $PersonalAccessToken ,
    
    [Parameter(Mandatory=$true)]
    [ValidateSet('VsoGit', 'GitHub')]
    $SourceType
)


#Set artifact repository internal name,
# if not set by user.

if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}

# Sign in to Azure
Connect-AzAccount


#Get Lab Resource
$LabResource = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceName $LabName -ResourceGroupName $LabResourceGroupName

Write-Verbose "Lab Name: $($LabResource.Name)"
Write-Verbose "Lab Resource Group Name: $($LabResource.ResourceGroupName)"
Write-Verbose "Lab Resource Location: $($LabResource.Location)"

Write-Verbose "Artifact Repository Internal Name: $ArtifactRepositoryName"

#Prepare properties object for call to New-AzResource
$propertiesObject = @{
    uri = $RepositoryUri;
    folderPath = $FolderPath;
    branchRef = $RepositoryBranch;
    displayName = $ArtifactRepositoryDisplayName;
    securityToken = $PersonalAccessToken;
    sourceType = $SourceType;
    status = 'Enabled'
}

Write-Verbose @"Properties to be passed to New-AzResource:$($propertiesObject | Out-String)"@

#Resource will be added to current subscription.
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
Write-Verbose "Az ResourceType: $resourcetype"
Write-Verbose "Az ResourceName: $resourceName"
 
Write-Verbose "Creating artifact repository '$ArtifactRepositoryDisplayName'..."
$result = New-AzResource -Location $LabResource.Location -ResourceGroupName $LabResource.ResourceGroupName -properties $propertiesObject -ResourceType $resourcetype -ResourceName $resourceName -ApiVersion 2016-05-15 -Force


#Alternate implementation:
# Use resourceId rather than resourcetype and resourcename parameters.
# Using resourceId allows you to specify the $SubscriptionId rather than using the
# subscription id of Get-AzContext.
#$resourceId = "/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
#$result = New-AzResource -properties $propertiesObject -ResourceId $resourceId -ApiVersion 2016-05-15 -Force


# Check the result
if ($result.Properties.ProvisioningState -eq "Succeeded") {
    Write-Verbose ("Successfully added artifact repository source '$ArtifactRepositoryDisplayName'")
}
else {
    Write-Error ("Error adding artifact repository source '$ArtifactRepositoryDisplayName'")
}

#Return the newly created resource so it may be used in subsequent scripts
return $result
```

### <a name="run-the-powershell-script"></a>Uruchom skrypt programu PowerShell
Poniższy przykład pokazuje, jak uruchomić skrypt: 

```powershell
Set-AzContext -SubscriptionId <Your Azure subscription ID>

.\New-DevTestLabArtifactRepository.ps1 -LabName "mydevtestlab" -LabResourceGroupName "mydtlrg" -ArtifactRepositoryName "MyTeam Repository" -RepositoryUri "https://github.com/<myteam>/<nameofrepo>.git" -PersonalAccessToken "1111...." -SourceType "GitHub"
```


### <a name="parameters"></a>Parametry
Przykładowy skrypt programu PowerShell w tym artykule przyjmuje następujące parametry:

| Parametr | Opis | 
| --------- | ----------- | 
| LabName | Nazwa laboratorium. |
| ArtifactRepositoryName | Nazwa dla nowego repozytorium artefaktów. Jeśli nie jest określona, skrypt tworzy losową nazwę repozytorium. |
| ArtifactRepositoryDisplayName | Nazwa wyświetlana dla repozytorium artefaktów. Jest to nazwa, która pojawia się w witrynie Azure portal (https://portal.azure.com) podczas przeglądania wszystkich repozytoriów artefaktów laboratorium. |
| RepositoryUri | Identyfikator URI do repozytorium. Przykłady: `https://github.com/<myteam>/<nameofrepo>.git` lub `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"`.| 
| RepositoryBranch | Gałąź w artefakcie, które znajdują się pliki. Wartość domyślna to "master". | 
| FolderPath | Folder, w którym można znaleźć artefaktów. Wartość domyślna to ' / artefaktów |
| PersonalAccessToken | Token zabezpieczający do uzyskiwania dostępu do repozytorium GitHub lub VSOGit. Zobacz sekcję wymagań wstępnych, aby uzyskać instrukcje uzyskać token pat |
| sourceType | Czy artefakt znajduje się repozytorium VSOGit lub GitHub. |

Samo repozytorium należy wewnętrzną nazwę do identyfikacji, który jest inny, nazwę wyświetlaną, która jest widoczna w witrynie Azure portal. Nie widzisz wewnętrzna nazwa w witrynie Azure portal, ale zostanie wyświetlony, za pomocą interfejsów API REST platformy Azure lub programu Azure PowerShell. Skrypt zawiera nazwy, jeśli nie został określony przez użytkownika naszego skryptu.

```powershell
#Set artifact repository name, if not set by user
if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}
```

### <a name="powershell-commands-used-in-the-script"></a>Polecenia programu PowerShell, używane w skrypcie

| Polecenia programu PowerShell | Uwagi |
| ------------------ | ----- |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | To polecenie jest używane do uzyskania szczegółowych informacji o laboratorium, takie jak jego lokalizacji. |
| [Nowe AzResource](/powershell/module/az.resources/new-azresource) | Nie ma żadnych określonego polecenia umożliwiające dodawanie repozytoriów artefaktu. Ogólny [New AzResource](/powershell/module/az.resources/new-azresource) polecenia cmdlet jest zadanie. To polecenie cmdlet musi albo **ResourceId** lub **ResourceName** i **ResourceType** parę jest znany typ zasobu do utworzenia. Ten przykładowy skrypt używa pary typ zasobu i nazwę zasobu. <br/><br/>Należy zauważyć, że w przypadku tworzenia źródło repozytorium artefaktów w tej samej lokalizacji i w ramach tej samej grupie zasobów laboratorium.|

Skrypt ten dodaje nowy zasób do bieżącej subskrypcji. Użyj [Get AzContext](/powershell/module/az.accounts/get-azcontext) Aby wyświetlić te informacje. Użyj [AzContext zestaw](/powershell/module/az.accounts/set-azcontext) można ustawić bieżącego dzierżawę i subskrypcję.

Najlepszym sposobem, aby odnaleźć nazwy zasobów i informacji o typie zasobu jest użycie [interfejsów API REST usługi Azure Test Drive](https://azure.github.io/projects/apis/) witryny sieci Web. Zapoznaj się z [DevTest Labs — 2016-05-15](https://aka.ms/dtlrestapis) dostawcy, aby wyświetlić dostępne interfejsy API REST dla dostawcy usługi DevTest Labs. Użytkownicy skryptu następujący identyfikator zasobu. 

```powershell
"/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
```
 
Typ zasobu jest, że wszystkie elementy na liście po "providers" w identyfikatorze URI, z wyjątkiem wymienionych w nawiasach klamrowych. Nazwa zasobu jest wszystko, co jest widoczne w nawiasach klamrowych. Jeśli więcej niż jeden element nazwy zasobów, oddziel każdy element ukośnika jako wykonaliśmy. 

```powershell
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
```


## <a name="next-steps"></a>Kolejne kroki
- [Określ obowiązkowe artefaktów dla laboratorium w usłudze Azure DevTest Labs](devtest-lab-mandatory-artifacts.md)
- [Tworzenie niestandardowych artefaktów dla swojej maszyny wirtualnej w usłudze DevTest Labs](devtest-lab-artifact-author.md)
- [Diagnozowanie błędów artefaktów w środowisku laboratoryjnym](devtest-lab-troubleshoot-artifact-failure.md)

