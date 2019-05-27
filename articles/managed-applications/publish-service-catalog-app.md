---
title: Tworzenie i publikowanie aplikacji zarządzanej katalogu usług platformy Azure | Microsoft Docs
description: Przedstawia sposób tworzenia aplikacji zarządzanej platformy Azure przeznaczonej dla członków Twojej organizacji.
services: managed-applications
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: dc86943924cd0c47c465e9d3bac4ca91b73a3ff5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66171549"
---
# <a name="create-and-publish-a-managed-application-definition"></a>Tworzenie i publikowanie definicji aplikacji zarządzanej

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Możesz tworzyć i publikować [aplikacje zarządzane](overview.md) na platformie Azure, przeznaczone dla członków Twojej organizacji. Na przykład dział IT może publikować aplikacje zarządzane zapewniające zgodność z normami obowiązującymi w organizacji. Takie aplikacje zarządzane są dostępne w katalogu usług, a nie w witrynie Azure Marketplace.

Aby opublikować aplikację zarządzaną w katalogu usług, należy wykonać następujące czynności:

* Utwórz szablon określający zasoby wdrażane wraz z aplikacją zarządzaną.
* Zdefiniuj elementy interfejsu użytkownika portalu, stosowane podczas wdrażania aplikacji zarządzanej.
* Utwórz pakiet zip zawierający wymagane pliki szablonu.
* Wybierz użytkowników, grupy lub aplikacje, dla których jest wymagany dostęp do grupy zasobów w ramach subskrypcji użytkownika.
* Utwórz definicję aplikacji zarządzanej, wskazującą pakiet zip i zawierającą żądanie dostępu dla określonej tożsamości.

W tym artykule opisano aplikację zarządzaną, która zawiera tylko konto magazynu. Celem artykułu jest przedstawienie kroków publikowania aplikacji zarządzanej. Kompletne przykłady znajdziesz w temacie [Sample projects for Azure managed applications](sample-projects.md) (Przykładowe projekty aplikacji zarządzanych platformy Azure).

Przykłady w języku PowerShell w tym artykule wymagają programu Azure PowerShell w wersji 6.2 lub nowszej. W razie potrzeby [zaktualizuj swoją wersję](/powershell/azure/install-Az-ps).

## <a name="create-the-resource-template"></a>Tworzenie szablonu zasobów

Każda definicja aplikacji zarządzanej zawiera plik o nazwie **mainTemplate.json**. W tym pliku należy zdefiniować zasoby platformy Azure, które zostaną wdrożone. Szablon nie różni się niczym od zwykłego szablonu usługi Resource Manager.

Utwórz plik o nazwie **mainTemplate.json**. W nazwie jest rozróżniana wielkość liter.

Dodaj do tego pliku następujący kod JSON. Definiuje on parametry wymagane podczas tworzenia konta magazynu oraz określa właściwości tego konta magazynu.

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

W witrynie Azure Portal interfejs użytkownika dla użytkowników, którzy tworzą aplikację zarządzaną, jest generowany przy użyciu pliku **createUiDefinition.json**. Możesz zdefiniować sposób wprowadzania danych wejściowych dla poszczególnych parametrów przez użytkowników. Dostępne opcje to między innymi lista rozwijana, pole tekstowe, pole hasła i inne narzędzia wprowadzania. Aby dowiedzieć się, jak utworzyć plik definicji interfejsu użytkownika dla aplikacji zarządzanej, zobacz [Rozpoczynanie pracy z plikiem CreateUiDefinition](create-uidefinition-overview.md).

Utwórz plik o nazwie **createUiDefinition.json**. W nazwie jest rozróżniana wielkość liter.

Dodaj do pliku następujący kod JSON.

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

## <a name="package-the-files"></a>Pakowanie plików

Dodaj oba pliki do pliku zip o nazwie app.zip. Oba pliki muszą znajdować się na poziomie głównym pliku zip. Jeśli zostaną umieszczone w folderze, podczas tworzenia definicji aplikacji zarządzanej otrzymasz komunikat o błędzie, informujący o braku wymaganych plików. 

Przekaż pakiet do dostępnej lokalizacji, w której może być używany. 

```powershell
New-AzResourceGroup -Name storageGroup -Location eastus
$storageAccount = New-AzStorageAccount -ResourceGroupName storageGroup `
  -Name "mystorageaccount" `
  -Location eastus `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context

New-AzStorageContainer -Name appcontainer -Context $ctx -Permission blob

Set-AzStorageBlobContent -File "D:\myapplications\app.zip" `
  -Container appcontainer `
  -Blob "app.zip" `
  -Context $ctx 
```

## <a name="create-the-managed-application-definition"></a>Tworzenie definicji aplikacji zarządzanej

### <a name="create-an-azure-active-directory-user-group-or-application"></a>Tworzenie grupy użytkowników aplikacji usługi Azure Active Directory

Następnym krokiem jest wybranie grupy użytkowników lub aplikacji, która będzie zarządzać zasobami w imieniu klienta. Ta grupa użytkowników lub aplikacja ma uprawnienia w zarządzanej grupie zasobów zgodne z przypisaną rolą. Może to być dowolna wbudowana rola w ramach kontroli dostępu opartej na rolach (RBAC), na przykład Właściciel lub Współautor. Możesz także przydzielić uprawnienia do zarządzania zasobami wybranemu użytkownikowi, ale zwykle przydziela się je grupie użytkowników. Jeśli chcesz utworzyć nową grupę użytkowników usługi Active Directory, zobacz [Create a group and add members in Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) (Tworzenie grupy i dodawanie do niej członków w usłudze Azure Active Directory).

Aby umożliwić zarządzanie zasobami, potrzebujesz identyfikatora obiektu grupy użytkowników. 

```powershell
$groupID=(Get-AzADGroup -DisplayName mygroup).Id
```

### <a name="get-the-role-definition-id"></a>Uzyskiwanie identyfikatora definicji roli

Następnie potrzebny będzie identyfikator definicji roli wbudowanej RBAC, do której chcesz udzielić dostępu użytkownikowi, grupie użytkowników lub aplikacji. Najczęściej używana jest rola Właściciel, Współautor lub Czytelnik. W następującym poleceniu przedstawiono sposób uzyskiwania identyfikatora definicji dla roli Właściciel:

```powershell
$ownerID=(Get-AzRoleDefinition -Name Owner).Id
```

### <a name="create-the-managed-application-definition"></a>Tworzenie definicji aplikacji zarządzanej

Jeśli nie istnieje jeszcze grupa zasobów do przechowywania definicji aplikacji zarządzanej, utwórz ją:

```powershell
New-AzResourceGroup -Name appDefinitionGroup -Location westcentralus
```

Teraz utwórz zasób definicji aplikacji zarządzanej.

```powershell
$blob = Get-AzStorageBlob -Container appcontainer -Blob app.zip -Context $ctx

New-AzManagedApplicationDefinition `
  -Name "ManagedStorage" `
  -Location "westcentralus" `
  -ResourceGroupName appDefinitionGroup `
  -LockLevel ReadOnly `
  -DisplayName "Managed Storage Account" `
  -Description "Managed Azure Storage Account" `
  -Authorization "${groupID}:$ownerID" `
  -PackageFileUri $blob.ICloudBlob.StorageUri.PrimaryUri.AbsoluteUri
```

### <a name="make-sure-users-can-see-your-definition"></a>Upewnij się, że użytkownicy będą mogli zobaczyć definicję

Masz dostęp do definicji aplikacji zarządzanej, ale chcesz mieć pewność, że inni użytkownicy w Twojej organizacji również mają do niej dostęp. Z definicji przyznaj im co najmniej rolę czytelnika. Użytkownicy mogą odziedziczyć ten poziom dostępu z subskrypcji lub grupy zasobów. Aby sprawdzić, kto ma dostęp do definicji, i dodać użytkowników lub grupy, zobacz [Używanie kontroli dostępu opartej na rolach do zarządzania dostępem do zasobów subskrypcji platformy Azure](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Kolejne kroki

* Aby opublikować aplikację zarządzaną w witrynie Azure Marketplace, zobacz [Aplikacje zarządzane platformy Azure w witrynie Marketplace](publish-marketplace-app.md).
* Aby wdrożyć wystąpienie aplikacji zarządzanej, zobacz [Deploy service catalog app through Azure portal (Wdrażanie aplikacji wykazu usług przy użyciu witryny Azure Portal)](deploy-service-catalog-quickstart.md).
