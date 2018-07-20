# [Dokumentacja usługi Azure Resource Manager](index.md)

# Omówienie
## [Co to jest usługa Resource Manager?](resource-group-overview.md)
## [Wdrażanie za pomocą usługi Resource Manager i klasyczne](resource-manager-deployment-model.md)

# Przewodniki Szybki start
## [Tworzenie szablonów — portal](resource-manager-quickstart-create-templates-use-the-portal.md)
## [Tworzenie szablonów — program VS Code](resource-manager-quickstart-create-templates-use-visual-studio-code.md)
## [Tworzenie szablonów — program Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)

# Pojęcia
## [Dostawcy zasobów i ich typy](resource-manager-supported-services.md)
## [Grupy zarządzania](management-groups-overview.md)
## [Nadzór subskrypcji](/azure/architecture/cloud-adoption-guide/subscription-governance?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
## [Szablony pozwalające zachować spójność w chmurze](templates-cloud-consistency.md)

# Instrukcje
## Tworzenie szablonów
### [Sekcje szablonu](resource-group-authoring-templates.md)
#### [Parametry](resource-manager-templates-parameters.md)
#### [Zmienne](resource-manager-templates-variables.md)
#### [Funkcje](resource-group-authoring-templates.md#functions)
#### [Zasoby](resource-manager-templates-resources.md)
#### [Dane wyjściowe](resource-manager-templates-outputs.md)
### [Linked and nested templates (Połączone i zagnieżdżone szablony)](resource-group-linked-templates.md)
### [Definiowanie zależności między zasobami](resource-group-define-dependencies.md)
### [Tworzenie wielu wystąpień](resource-group-create-multiple.md)
### [Aktualizowanie zasobu](/azure/architecture/building-blocks/extending-templates/update-resource)

## Wdrażanie
### Azure PowerShell
#### [Wdrażanie szablonu](resource-group-template-deploy.md)
#### [Wdrażanie szablonu prywatnego przy użyciu tokenu sygnatury dostępu współdzielonego](resource-manager-powershell-sas-token.md)
#### [Eksportowanie szablonu i ponowne wdrażanie](resource-manager-export-template-powershell.md)
### Interfejs wiersza polecenia platformy Azure
#### [Wdrażanie szablonu](resource-group-template-deploy-cli.md)
#### [Wdrażanie szablonu prywatnego przy użyciu tokenu sygnatury dostępu współdzielonego](resource-manager-cli-sas-token.md)
#### [Eksportowanie szablonu i ponowne wdrażanie](resource-manager-export-template-cli.md)
### Azure Portal
#### [Wdrażanie zasobów](resource-group-template-deploy-portal.md)
#### [Eksportowanie szablonu](resource-manager-export-template.md)
### [Interfejs API REST](resource-group-template-deploy-rest.md)
### [Wiele grup zasobów lub subskrypcji](resource-manager-cross-resource-group-deployment.md)
### [Ciągła integracja z usługą Visual Studio Team Services](../vs-azure-tools-resource-groups-ci-in-vsts.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
### [Przekazywanie bezpiecznych wartości podczas wdrażania](resource-manager-keyvault-parameter.md)

## Zarządzanie
### [Azure PowerShell](powershell-azure-resource-manager.md)
### [Interfejs wiersza polecenia platformy Azure](xplat-cli-azure-resource-manager.md)
### [Azure Portal](resource-group-portal.md)
### [Organizowanie zasobów przy użyciu tagów](resource-group-using-tags.md)
### [Przenoszenie zasobów do nowej grupy lub subskrypcji](resource-group-move-resources.md)
### [Tworzenie subskrypcji EA](programmatically-create-subscription.md)
### [Przyznawanie dostępu w celu tworzenia subskrypcji EA](grant-access-to-create-subscription.md)
### [Tworzenie grupy zarządzania](management-groups-create.md)
### [Zarządzanie grupami zarządzania](management-groups-manage.md)

## Kontrola dostępu
### Tworzenie jednostki usługi
#### [Azure PowerShell](resource-group-authenticate-service-principal.md)
#### [Interfejs wiersza polecenia platformy Azure](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
#### [Azure Portal](resource-group-create-service-principal-portal.md)
### [Interfejs API uwierzytelniania na potrzeby dostępu do subskrypcji](resource-manager-api-authentication.md)
### [Blokowanie zasobów](resource-group-lock-resources.md)

## Inspekcja
### [Wyświetlanie dzienników aktywności](resource-group-audit.md)
### [Wyświetlanie operacji wdrażania](resource-manager-deployment-operations.md)

## Rozwiązywanie problemów
### [Rozwiązywanie błędów związanych z wdrażaniem](resource-manager-common-deployment-errors.md)
### [AccountNameInvalid](resource-manager-storage-account-name-errors.md)
### [InvalidTemplate](resource-manager-invalid-template-errors.md)
### [Problemy z wdrażaniem w systemie Linux](../virtual-machines/linux/troubleshoot-deploy-vm.md)
### [NoRegisteredProviderFound](resource-manager-register-provider-errors.md)
### [NotFound](resource-manager-not-found-errors.md)
### [ParentResourceNotFound](resource-manager-parent-resource-errors.md)
### [Problemy z aprowizowaniem i alokacją w systemie Linux](../virtual-machines/linux/troubleshoot-deployment-new-vm.md)
### [Problemy z aprowizowaniem i alokacją w systemie Windows](../virtual-machines/windows/troubleshoot-deployment-new-vm.md)
### [RequestDisallowedByPolicy](resource-manager-policy-requestdisallowedbypolicy-error.md)
### [ReservedResourceName](resource-manager-reserved-resource-name.md)
### [ResourceQuotaExceeded](resource-manager-quota-errors.md)
### [SkuNotAvailable](resource-manager-sku-not-available-errors.md)
### [Problemy z wdrażaniem w systemie Windows](../virtual-machines/windows/troubleshoot-deploy-vm.md)

# Dokumentacja
## [Format szablonu](/azure/templates/)
## [Funkcje szablonów](resource-group-template-functions.md)
### [Funkcje tablicy i obiektów](resource-group-template-functions-array.md)
### [Funkcje porównania](resource-group-template-functions-comparison.md)
### [Funkcje wdrażania](resource-group-template-functions-deployment.md)
### [Funkcje logiczne](resource-group-template-functions-logical.md)
### [Funkcje liczbowe](resource-group-template-functions-numeric.md)
### [Funkcje zasobów](resource-group-template-functions-resource.md)
### [Funkcje ciągów](resource-group-template-functions-string.md)
## [Program PowerShell](/powershell/module/azurerm.resources)
## [Interfejs wiersza polecenia platformy Azure](/cli/azure/resource)
## [.NET](/dotnet/api/microsoft.azure.management.resourcemanager)
## [Java](/java/api/com.microsoft.azure.management.resources)
## [Python](http://azure-sdk-for-python.readthedocs.io/en/latest/resourcemanagement.html)
## [REST](/rest/api/resources/)

# Zasoby
## [Harmonogram działania dla platformy Azure](https://azure.microsoft.com/roadmap/?category=monitoring-management)
## [Kalkulator cen](https://azure.microsoft.com/pricing/calculator/)
## [Aktualizacje usług](https://azure.microsoft.com/updates/?product=azure-resource-manager)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-resource-manager)
## [Zarządzanie danymi osobowymi](resource-manager-personal-data.md)
## [Ograniczanie żądań](resource-manager-request-limits.md)
## [Śledzenie operacji asynchronicznych](resource-manager-async-operations.md)
## [Wideo](https://azure.microsoft.com/documentation/videos/index/?services=azure-resource-manager)
