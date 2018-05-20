---
title: Tworzenie i publikowanie aplikacji zarządzanych katalogu usługi Azure | Dokumentacja firmy Microsoft
description: Przedstawia sposób tworzenia aplikacji zarządzanej platformy Azure przeznaczonej dla członków Twojej organizacji.
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/15/2018
ms.author: tomfitz
ms.openlocfilehash: 57821e9c7ed1ca04aa7442f089268c5e89a017c3
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="publish-a-managed-application-for-internal-consumption"></a>Publikowanie aplikacji zarządzanych na użytek wewnętrzny

Możesz utworzyć i opublikować Azure [zarządzane aplikacje](overview.md) które są przeznaczone do członków organizacji. Na przykład dział INFORMATYCZNY może opublikować zarządzanych aplikacji, które zapewnić zgodność ze standardami w organizacji. Te aplikacje zarządzane są dostępne za pośrednictwem katalogu usług, a nie witrynę Azure marketplace.

Aby opublikować zarządzaną aplikację katalogu usług, należy:

* Utworzyć szablon określający zasoby, aby wdrożyć aplikację.
* Elementy interfejsu użytkownika portalu pozwalają zdefiniować, kiedy wdrażanie zarządzanych aplikacji.
* Utwórz pakiet ZIP, który zawiera pliki wymagane szablonu.
* Zdecyduj, które użytkownika, grupy lub aplikacja potrzebuje dostępu do grupy zasobów w subskrypcji użytkownika.
* Tworzenie definicji zarządzaną aplikację, która wskazuje pakietu ZIP i żąda dostępu do tożsamości.

W tym artykule zarządzanych aplikacji zawiera konta magazynu. Jest ona zilustrować kroki publikowania aplikacji zarządzanej. Zakończenie przykłady można znaleźć [przykładowe projekty dla platformy Azure zarządzanych aplikacji](sample-projects.md).

## <a name="create-the-resource-template"></a>Tworzenie szablonu zasobów

Definicja każdej zarządzanej aplikacji zawiera plik o nazwie **mainTemplate.json**. W, można zdefiniować zasobów platformy Azure, aby udostępnić. Szablon nie różni się niczym od zwykłego szablonu usługi Resource Manager.

Utwórz plik o nazwie **mainTemplate.json**. Nazwa jest rozróżniana wielkość liter.

Dodaj następujący kod JSON do pliku. Definiuje parametry w celu utworzenia konta magazynu i określa właściwości dla konta magazynu.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountNamePrefix": {
            "type": "string"
        },
        "storageAccountType": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "storageAccountName": "[concat(parameters('storageAccountNamePrefix'), uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            "apiVersion": "2016-01-01",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('storageAccountType')]"
            },
            "kind": "Storage",
            "properties": {}
        }
    ],
    "outputs": {
        "storageEndpoint": {
            "type": "string",
            "value": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
        }
    }
}
```

Zapisz plik mainTemplate.json.

## <a name="create-the-user-interface-definition"></a>Tworzenie definicji interfejsu użytkownika

Azure portal używa **createUiDefinition.json** plik do generowania interfejsu użytkownika dla użytkowników, którzy tworzenie zarządzanych aplikacji. Zdefiniuj, jak użytkownicy podawanie danych wejściowych dla każdego parametru. Można użyć opcji, takie jak listy rozwijanej, pole tekstowe, pole hasła i inne dane wejściowe narzędzia. Aby dowiedzieć się, jak utworzyć plik definicji interfejsu użytkownika dla aplikacji zarządzanej, zobacz [Rozpoczynanie pracy z plikiem CreateUiDefinition](create-uidefinition-overview.md).

Utwórz plik o nazwie **createUiDefinition.json**. Nazwa jest rozróżniana wielkość liter.

Dodaj następujący kod JSON do pliku.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Compute.MultiVm",
    "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "storageConfig",
                "label": "Storage settings",
                "subLabel": {
                    "preValidation": "Configure the infrastructure settings",
                    "postValidation": "Done"
                },
                "bladeTitle": "Storage settings",
                "elements": [
                    {
                        "name": "storageAccounts",
                        "type": "Microsoft.Storage.MultiStorageAccountCombo",
                        "label": {
                            "prefix": "Storage account name prefix",
                            "type": "Storage account type"
                        },
                        "defaultValue": {
                            "type": "Standard_LRS"
                        },
                        "constraints": {
                            "allowedTypes": [
                                "Premium_LRS",
                                "Standard_LRS",
                                "Standard_GRS"
                            ]
                        }
                    }
                ]
            }
        ],
        "outputs": {
            "storageAccountNamePrefix": "[steps('storageConfig').storageAccounts.prefix]",
            "storageAccountType": "[steps('storageConfig').storageAccounts.type]",
            "location": "[location()]"
        }
    }
}
```

Zapisz plik createUiDefinition.json.

## <a name="package-the-files"></a>Pliki pakietu

Dodaj oba pliki do pliku zip o nazwie app.zip. Dwa pliki muszą być na poziomie głównym pliku zip. Jeśli je umieścić w folderze, zostanie wyświetlony błąd podczas tworzenia definicji aplikacji zarządzanych, stwierdzający, że nie są wymagane pliki. 

Przekaż pakiet do dostępnej lokalizacji, z którym mogą być używane. 

```powershell
New-AzureRmResourceGroup -Name storageGroup -Location eastus
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName storageGroup `
  -Name "mystorageaccount" `
  -Location eastus `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context

New-AzureStorageContainer -Name appcontainer -Context $ctx -Permission blob

Set-AzureStorageBlobContent -File "D:\myapplications\app.zip" `
  -Container appcontainer `
  -Blob "app.zip" `
  -Context $ctx 
```

## <a name="create-the-managed-application-definition"></a>Tworzenie definicji aplikacji zarządzanej

### <a name="create-an-azure-active-directory-user-group-or-application"></a>Tworzenie grupy użytkowników usługi Azure Active Directory lub aplikacji

Następnym krokiem jest, aby wybrać grupy użytkowników lub aplikacji do zarządzania zasobami w imieniu klienta. Tej grupy użytkowników lub aplikacji ma uprawnienia do grupy zasobów zarządzanych w zależności od roli, która jest przypisana. Rola może być żadnych wbudowanych ról kontroli dostępu opartej na rolach (RBAC) jak właściciela lub współautora. Można także zezwolić użytkownikowi do zarządzania zasobami, ale zazwyczaj przypisać to uprawnienie do grupy użytkowników. Aby utworzyć nową grupę użytkowników usługi Active Directory, zobacz [Utwórz grupy i Dodaj elementy członkowskie w usłudze Azure Active Directory](../active-directory/active-directory-groups-create-azure-portal.md).

Potrzebny jest identyfikator obiektu grupy użytkowników na potrzeby zarządzania zasobami. 

```powershell
$groupID=(Get-AzureRmADGroup -DisplayName mygroup).Id
```

### <a name="get-the-role-definition-id"></a>Pobierz identyfikator definicji roli

Następnie należy identyfikator definicji roli roli wbudowanych RBAC chcesz udzielić dostępu dla użytkownika, grupy użytkowników lub aplikacji. Zwykle można użyć roli właściciela lub współautora lub czytelnika. W następującym poleceniu przedstawiono sposób uzyskiwania identyfikatora definicji dla roli Właściciel:

```powershell
$ownerID=(Get-AzureRmRoleDefinition -Name Owner).Id
```

### <a name="create-the-managed-application-definition"></a>Tworzenie definicji aplikacji zarządzanej

Jeśli grupy zasobów nie jest już ma do przechowywania definicji aplikacji zarządzanych, utwórz ją teraz:

```powershell
New-AzureRmResourceGroup -Name appDefinitionGroup -Location westcentralus
```

Teraz utwórz zasób definicji aplikacji zarządzanej.

```powershell
$blob = Get-AzureStorageBlob -Container appcontainer -Blob app.zip -Context $ctx

New-AzureRmManagedApplicationDefinition `
  -Name "ManagedStorage" `
  -Location "westcentralus" `
  -ResourceGroupName appDefinitionGroup `
  -LockLevel ReadOnly `
  -DisplayName "Managed Storage Account" `
  -Description "Managed Azure Storage Account" `
  -Authorization "${groupID}:$ownerID" `
  -PackageFileUri $blob.ICloudBlob.StorageUri.PrimaryUri.AbsoluteUri
```

## <a name="create-the-managed-application"></a>Tworzenie aplikacji zarządzanych

Można wdrożyć aplikacji zarządzanych za pomocą portalu, programu PowerShell lub wiersza polecenia platformy Azure.

### <a name="powershell"></a>PowerShell

Po pierwsze umożliwia wdrażanie zarządzanych aplikacji za pomocą programu PowerShell.

```powershell
# Create resource group
New-AzureRmResourceGroup -Name applicationGroup -Location westcentralus

# Get ID of managed application definition
$appid=(Get-AzureRmManagedApplicationDefinition -ResourceGroupName appDefinitionGroup -Name ManagedStorage).ManagedApplicationDefinitionId

# Create the managed application
New-AzureRmManagedApplication `
  -Name storageApp `
  -Location westcentralus `
  -Kind ServiceCatalog `
  -ResourceGroupName applicationGroup `
  -ManagedApplicationDefinitionId $appid `
  -ManagedResourceGroupName "InfrastructureGroup" `
  -Parameter "{`"storageAccountNamePrefix`": {`"value`": `"demostorage`"}, `"storageAccountType`": {`"value`": `"Standard_LRS`"}}"
```

Istnieje teraz zarządzanych aplikacji i infrastruktury zarządzanych w ramach subskrypcji.

### <a name="portal"></a>Portal

Teraz umożliwia wdrażanie zarządzanych aplikacji za pomocą portalu. Zostanie wyświetlony interfejs użytkownika utworzonego w pakiecie.

1. Przejdź do portalu Azure. Wybierz **+ Utwórz zasób** i wyszukaj **katalogu usług**.

   ![Katalog usługi wyszukiwania](./media/publish-service-catalog-app/create-new.png)

1. Wybierz **katalogu usług zarządzanych aplikacji**.

   ![Wybierz katalog usług](./media/publish-service-catalog-app/select-service-catalog-managed-app.png)

1. Wybierz pozycję **Utwórz**.

   ![Wybierz opcję tworzenia](./media/publish-service-catalog-app/select-create.png)

1. Znajdź aplikację, którą ma zostać utworzona z listy dostępnych rozwiązań i zaznacz go. Wybierz pozycję **Utwórz**.

   ![Znajdź zarządzanej aplikacji](./media/publish-service-catalog-app/find-application.png)

1. Podaj podstawowe informacje wymagane do zarządzanych aplikacji. Określ subskrypcję i Nowa grupa zasobów zawiera zarządzanej aplikacji. Wybierz **zachodnie centralnej nam** dla lokalizacji. Po zakończeniu wybierz polecenie **Zamknij**.

   ![Podaj parametry zarządzanej aplikacji](./media/publish-service-catalog-app/add-basics.png)

1. Podaj wartości, które są specyficzne dla zasobów w zarządzanej aplikacji. Po zakończeniu wybierz polecenie **Zamknij**.

   ![Podaj parametry zasobu](./media/publish-service-catalog-app/add-storage-settings.png)

1. Szablon weryfikuje podanych wartości. Jeśli weryfikacja zakończy się powodzeniem, wybierz **OK** do wdrożenia.

   ![Sprawdź poprawność zarządzanej aplikacji](./media/publish-service-catalog-app/view-summary.png)

Po zakończeniu wdrożenia zarządzanych aplikacji istnieje w grupie zasobów o nazwie applicationGroup. Konto magazynu istnieje w grupie zasobów o nazwie applicationGroup plus wartość skrótu ciągu.

## <a name="next-steps"></a>Kolejne kroki

* Zobacz artykuł [Omówienie aplikacji zarządzanych](overview.md) zawierający wprowadzenie do aplikacji zarządzanych.
* Na przykład projektów, zobacz [przykładowe projekty dla platformy Azure zarządzanych aplikacji](sample-projects.md).
* Aby dowiedzieć się, jak utworzyć plik definicji interfejsu użytkownika dla aplikacji zarządzanej, zobacz [Rozpoczynanie pracy z plikiem CreateUiDefinition](create-uidefinition-overview.md).
