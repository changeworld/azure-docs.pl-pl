---
title: Wdrażanie zasobów za pomocą programu PowerShell i szablonu
description: Użyj usługi Azure Resource Manager i Azure PowerShell do wdrażania zasobów na platformie Azure. Zasoby są zdefiniowane w szablonie usługi Resource Manager.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: e595aa8f86a24e59c8e00d24ea8e9dcb0875a8f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153271"
---
# <a name="deploy-resources-with-arm-templates-and-azure-powershell"></a>Wdrażanie zasobów za pomocą szablonów ARM i programu Azure PowerShell

Dowiedz się, jak używać szablonów usługi Azure PowerShell z usługą Azure Resource Manager (ARM) do wdrażania zasobów na platformie Azure. Aby uzyskać więcej informacji na temat koncepcji wdrażania rozwiązań platformy Azure i zarządzania nimi, zobacz [omówienie wdrażania szablonów](overview.md).

## <a name="deployment-scope"></a>Zakres wdrożenia

Wdrożenie można kierować do grupy zasobów, subskrypcji, grupy zarządzania lub dzierżawy. W większości przypadków zostaniesz skierowany do grupy zasobów. Aby zastosować zasady i przypisania ról w większym zakresie, użyj subskrypcji, grupy zarządzania lub wdrożeń dzierżawy. Podczas wdrażania w ramach subskrypcji można utworzyć grupę zasobów i wdrożyć do niej zasoby.

W zależności od zakresu wdrożenia należy użyć różnych poleceń.

Aby wdrożyć w **grupie zasobów,** należy użyć [new-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment):

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <path-to-template>
```

Aby wdrożyć w **ramach subskrypcji,** należy użyć funkcji New-AzSubscriptionDeployment:

```azurepowershell
New-AzSubscriptionDeployment -Location <location> -TemplateFile <path-to-template>
```

Aby uzyskać więcej informacji na temat wdrożeń na poziomie subskrypcji, zobacz [Tworzenie grup zasobów i zasobów na poziomie subskrypcji](deploy-to-subscription.md).

Aby wdrożyć je w **grupie zarządzania,** należy użyć programu [New-AzManagementGroupDeployment](/powershell/module/az.resources/New-AzManagementGroupDeployment).

```azurepowershell
New-AzManagementGroupDeployment -Location <location> -TemplateFile <path-to-template>
```

Aby uzyskać więcej informacji na temat wdrożeń na poziomie grupy zarządzania, zobacz [Tworzenie zasobów na poziomie grupy zarządzania](deploy-to-management-group.md).

Aby wdrożyć w **dzierżawie,** należy użyć [new-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment).

```azurepowershell
New-AzTenantDeployment -Location <location> -TemplateFile <path-to-template>
```

Aby uzyskać więcej informacji na temat wdrożeń na poziomie dzierżawy, zobacz [Tworzenie zasobów na poziomie dzierżawy.](deploy-to-tenant.md)

Przykłady w tym artykule używają wdrożeń grup zasobów.

## <a name="prerequisites"></a>Wymagania wstępne

Do wdrożenia potrzebny jest szablon. Jeśli jeszcze go nie masz, pobierz i zapisz [przykładowy szablon](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) z repozytorium szablonów szybki start platformy Azure. Nazwa pliku lokalnego użyta w tym artykule to **c:\MyTemplates\azuredeploy.json**.

Jeśli nie używasz usługi Azure Cloud Shell do wdrażania szablonów, musisz zainstalować program Azure PowerShell i połączyć się z platformą Azure:

- **Zainstaluj polecenia cmdlet programu Azure PowerShell na komputerze lokalnym.** Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z programem Azure PowerShell](/powershell/azure/get-started-azureps).
- **Połącz się z platformą Azure przy użyciu [connect-AZAccount](/powershell/module/az.accounts/connect-azaccount)**. Jeśli masz wiele subskrypcji platformy Azure, może być również konieczne uruchomienie [Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext). Aby uzyskać więcej informacji, zobacz [Używanie wielu subskrypcji platformy Azure](/powershell/azure/manage-subscriptions-azureps).

## <a name="deploy-local-template"></a>Wdrażanie szablonu lokalnego

Poniższy przykład tworzy grupę zasobów i wdraża szablon z komputera lokalnego. Nazwa grupy zasobów może zawierać tylko znaki alfanumeryczne, kropki, podkreślenia, łączniki i nawiasy. Może to być do 90 znaków. To nie może zakończyć się w okresie.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json
```

Wdrożenie może potrwać kilka minut.

## <a name="deploy-remote-template"></a>Wdrażanie szablonu zdalnego

Zamiast przechowywać szablony ARM na komputerze lokalnym, możesz chcieć przechowywać je w lokalizacji zewnętrznej. Szablony można przechowywać w repozytorium kontroli źródła (na przykład GitHub). Można też przechowywać je na koncie magazynu platformy Azure w celu uzyskania dostępu współużytkowego w organizacji.

Aby wdrożyć szablon zewnętrzny, należy użyć parametru **TemplateUri.** Użyj identyfikatora URI w przykładzie, aby wdrożyć przykładowy szablon z usługi GitHub.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

W poprzednim przykładzie wymaga publicznie dostępnego identyfikatora URI dla szablonu, który działa w większości scenariuszy, ponieważ szablon nie powinien zawierać poufnych danych. Jeśli musisz określić poufne dane (takie jak hasło administratora), przekaż tę wartość jako bezpieczny parametr. Jednak jeśli nie chcesz, aby szablon był publicznie dostępny, możesz go chronić, przechowując go w kontenerze magazynu prywatnego. Aby uzyskać informacje dotyczące wdrażania szablonu, który wymaga tokenu sygnatury dostępu współdzielonego (SAS), zobacz [Wdrażanie szablonu prywatnego za pomocą tokenu sygnatury dostępu Współdzielonego.](secure-template-with-sas-token.md) Aby przejść do samouczka, zobacz [Samouczek: Integrowanie usługi Azure Key Vault we wdrażaniu szablonu ARM](template-tutorial-use-key-vault.md).

## <a name="deploy-from-azure-cloud-shell"></a>Wdrażanie z usługi Azure Cloud Shell

Za pomocą usługi [Azure Cloud Shell](https://shell.azure.com) można wdrożyć szablon. Aby wdrożyć szablon zewnętrzny, podaj identyfikator URI szablonu. Aby wdrożyć szablon lokalny, należy najpierw załadować szablon do konta magazynu dla usługi Cloud Shell. Aby przesłać pliki do powłoki, wybierz ikonę menu **Przekaż/Pobierz pliki** z okna powłoki.

Aby otworzyć powłokę chmury, [https://shell.azure.com](https://shell.azure.com)przejdź do , lub wybierz **Try-It** z następującej sekcji kodu:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Aby wkleić kod do powłoki, kliknij prawym przyciskiem myszy wewnątrz powłoki, a następnie wybierz polecenie **Wklej**.

## <a name="pass-parameter-values"></a>Wartości parametrów przebiegu

Aby przekazać wartości parametrów, można użyć parametrów wbudowanych lub pliku parametrów.

### <a name="inline-parameters"></a>Parametry wbudowane

Aby przekazać parametry wbudowane, podaj `New-AzResourceGroupDeployment` nazwy parametru za pomocą polecenia. Na przykład, aby przekazać ciąg i tablicę do szablonu, użyj:

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString "inline string" `
  -exampleArray $arrayParam
```

Można również uzyskać zawartość pliku i podać tę zawartość jako parametr wbudowany.

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString $(Get-Content -Path c:\MyTemplates\stringcontent.txt -Raw) `
  -exampleArray $arrayParam
```

Uzyskanie wartości parametru z pliku jest przydatne, gdy trzeba podać wartości konfiguracji. Na przykład można podać [wartości cloud-init dla maszyny wirtualnej systemu Linux.](../../virtual-machines/linux/using-cloud-init.md)

Jeśli chcesz przekazać w tablicy obiektów, należy utworzyć tabele mieszania w programie PowerShell i dodać je do tablicy. Przekaż tę tablicę jako parametr podczas wdrażania.

```powershell
$hash1 = @{ Name = "firstSubnet"; AddressPrefix = "10.0.0.0/24"}
$hash2 = @{ Name = "secondSubnet"; AddressPrefix = "10.0.1.0/24"}
$subnetArray = $hash1, $hash2
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleArray $subnetArray
```

### <a name="parameter-files"></a>Pliki parametrów

Zamiast przekazywania parametrów jako wartości wbudowanych w skrypcie, może być łatwiej użyć pliku JSON, który zawiera wartości parametrów. Plik parametrów może być plikiem lokalnym lub zewnętrznym z dostępnym identyfikatorem URI.

Aby uzyskać więcej informacji na temat pliku parametrów, zobacz [Tworzenie pliku parametrów Menedżera zasobów](parameter-files.md).

Aby przekazać lokalny plik parametrów, użyj parametru **TemplateParameterFile:**

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

Aby przekazać plik parametrów zewnętrznych, użyj parametru **TemplateParameterUri:**

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json
```

## <a name="test-template-deployments"></a>Testowanie wdrożeń szablonów

Aby przetestować wartości szablonu i parametrów bez faktycznego wdrażania jakichkolwiek zasobów, należy użyć [funkcji Test-AzResourceGroupDeployment](/powershell/module/az.resources/test-azresourcegroupdeployment). 

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType Standard_GRS
```

Jeśli nie zostaną wykryte żadne błędy, polecenie zakończy się bez odpowiedzi. Jeśli zostanie wykryty błąd, polecenie zwraca komunikat o błędzie. Na przykład przekazanie niepoprawnej wartości dla jednostki SKU konta magazynu zwraca następujący błąd:

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType badSku

Code    : InvalidTemplate
Message : Deployment template validation failed: 'The provided value 'badSku' for the template parameter 'storageAccountType'
          at line '15' and column '24' is not valid. The parameter value is not part of the allowed value(s):
          'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.
Details :
```

Jeśli szablon ma błąd składniowy, polecenie zwraca błąd wskazujący, że nie można przeanalizować szablonu. Komunikat wskazuje numer wiersza i położenie błędu analizy.

```powershell
Test-AzResourceGroupDeployment : After parsing a value an unexpected character was encountered: 
  ". Path 'variables', line 31, position 3.
```

## <a name="next-steps"></a>Następne kroki

- Aby przywrócić pomyślne wdrożenie po wyświetleniu błędu, zobacz [Wycofywanie w przypadku błędu do pomyślnego wdrożenia](rollback-on-error.md).
- Aby określić sposób obsługi zasobów, które istnieją w grupie zasobów, ale nie są zdefiniowane w szablonie, zobacz [Tryby wdrażania usługi Azure Resource Manager](deployment-modes.md).
- Aby dowiedzieć się, jak zdefiniować parametry w szablonie, zobacz [Opis struktury i składni szablonów ARM](template-syntax.md).
- Aby uzyskać informacje dotyczące wdrażania szablonu, który wymaga tokenu sygnatury dostępu Współdzielonego, zobacz [Wdrażanie szablonu prywatnego za pomocą tokenu sygnatury dostępu Współdzielonego](secure-template-with-sas-token.md).
