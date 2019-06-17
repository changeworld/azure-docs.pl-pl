---
title: Wdrażanie zasobów przy użyciu programu PowerShell i szablonu | Dokumentacja firmy Microsoft
description: Użyj usługi Azure Resource Manager i programu Azure PowerShell do wdrażania zasobów platformy Azure. Zasoby są zdefiniowane w szablonie usługi Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tomfitz
ms.openlocfilehash: 63d729f19b0ef20d0e7a716d6857b4627095856b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66476986"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)

Dowiedz się, jak używać programu Azure PowerShell przy użyciu szablonów usługi Resource Manager do wdrażania zasobów na platformie Azure. Aby uzyskać więcej informacji na temat pojęć związanych z wdrażaniem i zarządzaniem nimi Twoich rozwiązań platformy Azure, zobacz [Omówienie usługi Azure Resource Manager](resource-group-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deployment-scope"></a>Zakres wdrożenia

Można wskazać wdrożenia do subskrypcji platformy Azure lub grupy zasobów w ramach subskrypcji. W większości przypadków będzie wskazywać wdrożenie w grupie zasobów. Subskrypcja wdrożeń umożliwiają stosowanie zasad i przypisań ról w ramach subskrypcji. Subskrypcja wdrożeń możesz także użyć do tworzenia grupy zasobów i wdrażania zasobów. W zależności od zakresu wdrożenia możesz używać różnych poleceń.

Aby wdrożyć **grupy zasobów**, użyj [New AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment):

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <path-to-template>
```

Aby wdrożyć **subskrypcji**, użyj [New AzDeployment](/powershell/module/az.resources/new-azdeployment):

```azurepowershell
New-AzDeployment -Location <location> -TemplateFile <path-to-template>
```

Wdrożenia grup zarządzania są obecnie obsługiwane tylko za pośrednictwem interfejsu API REST. Zobacz [wdrażanie zasobów przy użyciu szablonów usługi Resource Manager i interfejsu REST API usługi Resource Manager](resource-group-template-deploy-rest.md).

W przykładach w tym artykule używany wdrożenia grupy zasobów. Aby uzyskać więcej informacji o wdrożeniach subskrypcji, zobacz [tworzenia grupy zasobów i zasobów na poziomie subskrypcji](deploy-to-subscription.md).

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebne do wdrożenia szablonu. Jeśli nie masz jeszcze jeden, Pobierz i Zapisz [przykładowy szablon](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) z repozytorium szablonów szybkiego startu platformy Azure. Nazwa pliku lokalnego, używane w tym artykule jest **c:\MyTemplates\azuredeploy.json**.

Jeśli usługa Azure Cloud shell jest używana do wdrażania szablonów, należy zainstalować program Azure PowerShell i łączenie z platformą Azure:

- **Zainstaluj polecenia cmdlet programu Azure PowerShell na komputerze lokalnym.** Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z programem Azure PowerShell](/powershell/azure/get-started-azureps).
- **Połącz z platformą Azure za pomocą [Connect AZAccount](/powershell/module/az.accounts/connect-azaccount)** . Jeśli masz wiele subskrypcji platformy Azure, być może trzeba będzie również uruchomić [AzContext zestaw](/powershell/module/Az.Accounts/Set-AzContext). Aby uzyskać więcej informacji, zobacz [użycie większej liczby subskrypcji platformy Azure](/powershell/azure/manage-subscriptions-azureps).

## <a name="deploy-local-template"></a>Wdrażanie lokalnego szablonu

Poniższy przykład tworzy grupę zasobów i służy do wdrażania szablonu z maszyny lokalnej. Nazwa grupy zasobów może zawierać tylko znaki alfanumeryczne, kropki, podkreślenia, łączniki i nawiasy. Może być maksymalnie 90 znaków. Nie może kończyć się kropką.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json
```

Wdrożenie może potrwać kilka minut.

## <a name="deploy-remote-template"></a>Wdrażanie szablonu zdalnego

Zamiast przechowywać szablonów usługi Resource Manager na komputerze lokalnym, użytkownik może chcieć przechowywać je w lokalizacji zewnętrznej. Szablony można przechowywać w repozytorium kontroli źródła (na przykład GitHub). Lub można przechowywać na koncie magazynu platformy Azure w celu zapewnienia dostępu współdzielonego w Twojej organizacji.

Aby wdrożyć szablon zewnętrznego, użyj **TemplateUri** parametru. Użyj identyfikatora URI w przykładzie, aby wdrożyć przykładowy szablon z serwisu GitHub.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Poprzedni przykład wymaga publicznie identyfikator URI dla szablonu, który działa w przypadku większości scenariuszy, ponieważ szablon nie powinna zawierać dane poufne. Jeśli musisz określić dane poufne (na przykład hasło administratora), należy przekazać tę wartość jako parametru secure. Jednak jeśli nie chcesz, aby szablon był dostępny publicznie, można go chronić dzięki przechowywaniu go w kontenerze magazynu prywatnego. Aby uzyskać informacji o wdrażaniu szablonu, który wymaga tokenu (SAS) sygnatury dostępu współdzielonego, zobacz [wdrażanie prywatnego szablonu przy użyciu tokenu sygnatury dostępu Współdzielonego](resource-manager-powershell-sas-token.md). Aby wykonać kroki samouczka, zobacz [samouczka: Integracja z usługą Azure Key Vault podczas wdrażania szablonu usługi Resource Manager](./resource-manager-tutorial-use-key-vault.md).

## <a name="deploy-from-azure-cloud-shell"></a>Wdrażanie z usługi Azure Cloud shell

Możesz użyć [usługi Azure Cloud Shell](https://shell.azure.com) do wdrażania szablonu. Aby wdrożyć szablon zewnętrznych, podaj identyfikator URI szablonu. Aby wdrożyć szablon lokalnego, musisz najpierw załadować swój szablon do konta magazynu dla usługi Cloud Shell. Aby przekazać pliki do powłoki, wybierz **przekazywanych/pobieranych plików** ikonę menu z okna powłoki.

Aby otworzyć usłudze Cloud shell, przejdź do [ https://shell.azure.com ](https://shell.azure.com), lub wybierz **Try It** z poniższej sekcji z kodem:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Aby wkleić kod w powłoce, kliknij prawym przyciskiem myszy wewnątrz powłoki, a następnie wybierz pozycję **Wklej**.

## <a name="redeploy-when-deployment-fails"></a>Wdróż ponownie, gdy wdrożenie zakończy się niepowodzeniem

Ta funkcja jest nazywana *wycofywania w przypadku błędu*. Jeśli wdrożenie zakończy się niepowodzeniem, można automatycznie wdrożyć ponownie wcześniej pomyślnego wdrażania z historii wdrożenia. Aby określić ponownego wdrożenia, należy użyć `-RollbackToLastDeployment` lub `-RollBackDeploymentName` parametr w poleceniu wdrożenia. Ta funkcja jest przydatna, jeśli coś znanego, dobrego stanu dla danego wdrożenia infrastruktury, a chcesz przywrócić ten stan. Kilka ograniczeń i ostrzeżenia:

- Ponownego wdrożenia komputera jest uruchamiany, dokładnie tak, jak została poprzednio uruchomiona z tymi samymi parametrami. Nie można zmienić parametry.
- Poprzedniego wdrożenia zostanie uruchomiony z użyciem [w trybie](./deployment-modes.md#complete-mode). Zostaną usunięte wszystkie zasoby, które nie są uwzględnione w poprzednim wdrożeniu, a wszystkie konfiguracje zasobów są ustawione do poprzedniego stanu. Upewnij się, w pełni rozumiesz [tryby wdrażania](./deployment-modes.md).
- Ponownego wdrożenia komputera ma wpływ tylko na zasoby, nie ma wpływu na wszelkie zmiany danych.
- Ta funkcja jest obsługiwana tylko we wdrożeniach grupy zasobów, nie wdrożeń poziomu subskrypcji. Aby uzyskać więcej informacji na temat wdrażania poziomu subskrypcji zobacz [tworzenia grupy zasobów i zasobów na poziomie subskrypcji](./deploy-to-subscription.md).

Aby użyć tej opcji, wdrożeń muszą mieć unikatowe nazwy, dzięki czemu można je zidentyfikować w historii. Jeśli nie masz unikatowe nazwy bieżącego wdrożenia nie powiodło się może spowodować zastąpienie wcześniej pomyślnego wdrożenia w historii. Tej opcji można używać tylko w przypadku wdrożeń poziomu głównego. Wdrożenia z szablonów zagnieżdżonych nie są dostępne dla ponownego wdrażania.

Aby przeprowadzić ponowne wdrożenie ostatniego pomyślnego wdrożenia, należy dodać `-RollbackToLastDeployment` parametr jako flagi.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollbackToLastDeployment
```

Aby przeprowadzić ponowne wdrożenie określonego wdrożenia, należy użyć `-RollBackDeploymentName` parametru i podaj nazwę wdrożenia.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollBackDeploymentName ExampleDeployment01
```

Zakończyły się powodzeniem określonego wdrożenia.

## <a name="pass-parameter-values"></a>Przekazywanie wartości parametrów

Aby przekazać wartości parametrów, można użyć wbudowanego parametrów lub plik parametrów. Powyższych przykładach w tym artykule Pokaż parametry w tekście.

### <a name="inline-parameters"></a>Parametry wbudowane

Do przekazania parametrów wbudowanych, należy podać nazwy parametru z `New-AzResourceGroupDeployment` polecenia. Na przykład aby przekazać ciąg i Tablica do szablonu, należy użyć:

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString "inline string" `
  -exampleArray $arrayParam
```

Możesz również uzyskać zawartość pliku i podaj tę zawartość jako parametr w tekście.

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString $(Get-Content -Path c:\MyTemplates\stringcontent.txt -Raw) `
  -exampleArray $arrayParam
```

Wprowadzenie wartości parametru z pliku jest przydatne, gdy trzeba określić wartości konfiguracji. Na przykład można podać [wartości pakietu cloud-init dla maszyny wirtualnej Linux](../virtual-machines/linux/using-cloud-init.md).

Jeśli musisz przekazać tablicę obiektów, tworzenie tabel skrótów w programie PowerShell, a następnie dodać je do tablicy. Podczas wdrażania, należy przekazać tę tablicę jako parametr.

```powershell
$hash1 = @{ Name = "firstSubnet"; AddressPrefix = "10.0.0.0/24"}
$hash2 = @{ Name = "secondSubnet"; AddressPrefix = "10.0.1.0/24"}
$subnetArray = $hash1, $hash2
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleArray $subnetArray
```


### <a name="parameter-files"></a>Pliki parametrów

Zamiast przekazywania parametrów jako wartości wbudowanych w skrypcie, użytkownik może okazać się łatwiejszy w obsłudze pliku JSON, który zawiera wartości parametrów. Może to być plik parametrów pliku lokalnego lub zewnętrznego pliku za pomocą dostępny identyfikatora URI.

Plik parametrów musi być w następującym formacie:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "storageAccountType": {
         "value": "Standard_GRS"
     }
  }
}
```

Należy zauważyć, że w sekcji parametrów zawiera nazwę parametru, która pasuje do parametrów zdefiniowanych w szablonie (storageAccountType). Plik parametrów zawiera wartość dla parametru. Ta wartość jest automatycznie przekazanych do szablonu podczas wdrażania. Można utworzyć więcej niż jeden plik parametrów i następnie przekazać plik parametrów właściwe dla scenariusza.

Poprzedni przykład skopiuj i zapisz go jako plik o nazwie `storage.parameters.json`.

Aby przekazać plik parametrów lokalnych, należy użyć **TemplateParameterFile** parametru:

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

Aby przekazać plik parametrów zewnętrznego, użyj **TemplateParameterUri** parametru:

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json
```

### <a name="parameter-precedence"></a>Parametr pierwszeństwo

Można użyć wbudowanego parametrów i pliku parametrów lokalnych w tej samej operacji wdrożenia. Na przykład można określić niektóre wartości w pliku parametrów lokalnych i dodać inne wbudowane wartości podczas wdrażania. Jeśli możesz podać wartości parametrów w pliku parametrów lokalnych i wbudowane, pierwszeństwo ma wartość wbudowanej.

Jednak użycie pliku parametrów zewnętrzne, nie można przekazać wartości innych jako wbudowane lub z pliku lokalnego. Po określeniu w pliku parametrów w **TemplateParameterUri** parametru, wszystkie parametry są ignorowane w wierszach. Podaj wszystkie wartości parametrów w pliku zewnętrznym. Jeśli szablon zawiera poufne wartość, która nie może zawierać w pliku parametrów, Dodaj tę wartość do magazynu kluczy lub dynamicznego udostępniania wszystkie wbudowane wartości parametru.

### <a name="parameter-name-conflicts"></a>Nazwa parametru powoduje konflikt

Jeśli szablon zawiera parametr o nazwie identycznej z nazwą jednego z parametrów polecenia programu PowerShell, program PowerShell wyświetli parametr z szablonu z przyrostkowa **FromTemplate**. Na przykład parametr o nazwie **ResourceGroupName** w swojej szablonu jest w konflikcie z **ResourceGroupName** parametru w [New AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) polecenie cmdlet. Zostanie wyświetlony monit podać wartości **ResourceGroupNameFromTemplate**. Ogólnie rzecz biorąc należy unikać tego pomyłek przez nie nazywanie parametrów o takiej samej nazwie jako parametry używane dla operacji wdrożenia.

## <a name="test-template-deployments"></a>Testowanie wdrożenia szablonu

Aby przetestować wartości szablonu oraz parametrów bez faktycznego wdrażania zasobów, użyj [Test-AzureRmResourceGroupDeployment](/powershell/module/az.resources/test-azresourcegroupdeployment). 

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType Standard_GRS
```

Jeśli zostaną wykryte żadne błędy, polecenie zakończy się bez odpowiedzi. Jeśli zostanie wykryty błąd, to polecenie zwraca komunikat o błędzie. Na przykład przekazując niepoprawną wartość dla konta magazynu jednostki SKU i zwraca następujący błąd:

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType badSku

Code    : InvalidTemplate
Message : Deployment template validation failed: 'The provided value 'badSku' for the template parameter 'storageAccountType'
          at line '15' and column '24' is not valid. The parameter value is not part of the allowed value(s):
          'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.
Details :
```

Jeśli szablon zawiera błąd składniowy, polecenie zwraca komunikat o błędzie informujący, że nie można go przeanalizować szablonu. Komunikat wskazuje, numer wiersza i położenie błąd analizy.

```powershell
Test-AzResourceGroupDeployment : After parsing a value an unexpected character was encountered: 
  ". Path 'variables', line 31, position 3.
```

## <a name="next-steps"></a>Kolejne kroki

- Aby bezpiecznie wdrożyć usługę do więcej niż jednym regionie, zobacz [Azure Deployment Manager](deployment-manager-overview.md).
- Aby określić sposób obsługi zasobów, które istnieją w grupie zasobów, ale nie są zdefiniowane w szablonie, zobacz [tryby wdrażania usługi Azure Resource Manager](deployment-modes.md).
- Aby dowiedzieć się, jak zdefiniować parametry w szablonie, zobacz [Omówienie struktury i składni szablonów usługi Azure Resource Manager](resource-group-authoring-templates.md).
- Aby uzyskać informacje o wdrażaniu szablonu, który wymaga tokenu sygnatury dostępu Współdzielonego, zobacz [wdrażanie prywatnego szablonu przy użyciu tokenu sygnatury dostępu Współdzielonego](resource-manager-powershell-sas-token.md).
