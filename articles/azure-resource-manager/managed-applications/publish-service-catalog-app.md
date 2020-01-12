---
title: Publikowanie aplikacji zarządzanej katalogu usług
description: Przedstawia sposób tworzenia aplikacji zarządzanej platformy Azure przeznaczonej dla członków Twojej organizacji.
author: tfitzmac
ms.topic: tutorial
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: e756617a700d258078e84a3fa11c8aceb6f4dd88
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75903263"
---
# <a name="create-and-publish-a-managed-application-definition"></a>Tworzenie i publikowanie definicji aplikacji zarządzanej

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Możesz tworzyć i publikować [aplikacje zarządzane](overview.md) na platformie Azure, przeznaczone dla członków Twojej organizacji. Na przykład dział IT może publikować aplikacje zarządzane zapewniające zgodność z normami obowiązującymi w organizacji. Takie aplikacje zarządzane są dostępne w katalogu usług, a nie w witrynie Azure Marketplace.

Aby opublikować zarządzaną aplikację w katalogu usług platformy Azure, musisz:

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

## <a name="defining-your-create-experience-using-createuidefinitionjson"></a>Definiowanie środowiska tworzenia przy użyciu pliku CreateUiDefinition. JSON

Jako wydawca zdefiniujesz środowisko tworzenia przy użyciu pliku **createUiDefinition. JSON** , który generuje interfejs dla użytkowników tworzących aplikacje zarządzane. Definiujesz sposób, w jaki użytkownicy dostarczają dane wejściowe dla każdego parametru za pomocą [elementów kontrolki](create-uidefinition-elements.md) , takich jak listy rozwijane, pola tekstowe i hasła.

Utwórz plik o nazwie **createUiDefinition. JSON** (w tej nazwie jest rozróżniana wielkość liter)

Dodaj następujący początkowy kod JSON do pliku i Zapisz go.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
   "handler": "Microsoft.Azure.CreateUIDef",
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

Aby dowiedzieć się więcej, zobacz Rozpoczynanie [pracy z usługą CreateUiDefinition](create-uidefinition-overview.md).

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

Następnym krokiem jest wybranie grupy użytkowników lub aplikacji, która będzie zarządzać zasobami w imieniu klienta. Ta grupa użytkowników lub aplikacja ma uprawnienia w zarządzanej grupie zasobów zgodne z przypisaną rolą. Może to być dowolna wbudowana rola w ramach kontroli dostępu opartej na rolach (RBAC), na przykład Właściciel lub Współautor. Możesz także przydzielić uprawnienia do zarządzania zasobami wybranemu użytkownikowi, ale zwykle przydziela się je grupie użytkowników. Jeśli chcesz utworzyć nową grupę użytkowników usługi Active Directory, zobacz [Create a group and add members in Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) (Tworzenie grupy i dodawanie do niej członków w usłudze Azure Active Directory).

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

## <a name="bring-your-own-storage-for-the-managed-application-definition"></a>Przenoszenie własnego magazynu dla definicji aplikacji zarządzanej
Możesz zdecydować się na przechowywanie definicji aplikacji zarządzanej w ramach konta magazynu dostarczonego przez Ciebie podczas tworzenia, aby zapewnić jego lokalizację i dostęp w pełni zarządzany przez użytkownika zgodnie z wymaganiami prawnymi.

> [!NOTE]
> Korzystanie z własnego magazynu jest obsługiwane tylko przy użyciu szablonu ARM lub wdrożenia interfejsu API REST dla definicji aplikacji zarządzanej.

### <a name="select-your-storage-account"></a>Wybierz konto magazynu
Należy [utworzyć konto magazynu](../../storage/common/storage-account-create.md) , aby zawierało definicję aplikacji zarządzanej do użycia z katalogiem usług.

Skopiuj identyfikator zasobu konta magazynu. Będzie on używany później podczas wdrażania definicji.

### <a name="set-the-role-assignment-for-appliance-resource-provider-in-your-storage-account"></a>Ustaw przypisanie roli dla "dostawca zasobów urządzenia" na koncie magazynu
Aby można było wdrożyć definicję aplikacji zarządzanej na koncie magazynu, należy nadać uprawnienia współautora roli **dostawcy zasobów urządzenia** , aby można było napisać pliki definicji do kontenera konta magazynu.

1. W witrynie [Azure Portal](https://portal.azure.com) przejdź do swojego konta magazynu.
1. Wybierz pozycję **Kontrola dostępu (IAM)** , aby wyświetlić ustawienia kontroli dostępu dla konta magazynu. Wybierz kartę **przypisania ról** , aby wyświetlić listę przypisań ról.
1. W oknie **Dodawanie przypisania roli** wybierz rolę **współautor** . 
1. W polu **Przypisz dostęp do** wybierz pozycję **użytkownik, Grupa lub nazwa główna usługi Azure AD**.
1. W obszarze **Wybierz opcję** Wyszukaj rolę **dostawcy zasobów urządzenia** i wybierz ją.
1. Zapisz przypisanie roli.

### <a name="deploy-the-managed-application-definition-with-an-arm-template"></a>Wdróż definicję aplikacji zarządzanej przy użyciu szablonu ARM 

Użyj następującego szablonu usługi ARM, aby wdrożyć spakowaną aplikację zarządzaną jako nową definicję aplikacji zarządzanej w katalogu usług, której pliki definicji są przechowywane i utrzymywane na Twoim koncie magazynu:
   
```json
    {
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        },
        "applicationName": {
            "type": "string",
            "metadata": {
                "description": "Managed Application name"
            }
        },
        "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
        "definitionStorageResourceID": {
            "type": "string",
            "metadata": {
                "description": "Storage account resource ID for where you're storing your definition"
            }
        },
        "_artifactsLocation": {
            "type": "string",
            "metadata": {
                "description": "The base URI where artifacts required by this template are located."
            }
        }
    },
    "variables": {
        "lockLevel": "None",
        "description": "Sample Managed application definition",
        "displayName": "Sample Managed application definition",
        "managedApplicationDefinitionName": "[parameters('applicationName')]",
        "packageFileUri": "[parameters('_artifactsLocation')]",
        "defLocation": "[parameters('definitionStorageResourceID')]",
        "managedResourceGroupId": "[concat(subscription().id,'/resourceGroups/', concat(parameters('applicationName'),'_managed'))]",
        "applicationDefinitionResourceId": "[resourceId('Microsoft.Solutions/applicationDefinitions',variables('managedApplicationDefinitionName'))]"
    },
    "resources": [
        {
            "type": "Microsoft.Solutions/applicationDefinitions",
            "apiVersion": "2019-07-01",
            "name": "[variables('managedApplicationDefinitionName')]",
            "location": "[parameters('location')]",
            "properties": {
                "lockLevel": "[variables('lockLevel')]",
                "description": "[variables('description')]",
                "displayName": "[variables('displayName')]",
                "packageFileUri": "[variables('packageFileUri')]",
                "storageAccountId": "[variables('defLocation')]"
            }
        }
    ],
    "outputs": {}
}
```

Dodaliśmy nową właściwość o nazwie **storageAccountId** do właściwości applicationDefintion i podaj identyfikator konta magazynu, w którym ma być przechowywana definicja, jako wartość:

Można sprawdzić, czy pliki definicji aplikacji są zapisywane na podanym koncie magazynu w kontenerze o nazwie **applicationdefinitions**.

> [!NOTE]
> W celu zwiększenia bezpieczeństwa można utworzyć magazyn definicji zarządzanych aplikacji w [obiekcie blob konta usługi Azure Storage, w którym jest włączone szyfrowanie](../../storage/common/storage-service-encryption.md). Zawartość definicji jest zaszyfrowana przy użyciu opcji szyfrowania konta magazynu. Tylko użytkownicy z uprawnieniami do pliku mogą zobaczyć definicję w katalogu usług.

### <a name="make-sure-users-can-see-your-definition"></a>Upewnij się, że użytkownicy będą mogli zobaczyć definicję

Masz dostęp do definicji aplikacji zarządzanej, ale chcesz mieć pewność, że inni użytkownicy w Twojej organizacji również mają do niej dostęp. Z definicji przyznaj im co najmniej rolę czytelnika. Użytkownicy mogą odziedziczyć ten poziom dostępu z subskrypcji lub grupy zasobów. Aby sprawdzić, kto ma dostęp do definicji, i dodać użytkowników lub grupy, zobacz [Używanie kontroli dostępu opartej na rolach do zarządzania dostępem do zasobów subskrypcji platformy Azure](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Następne kroki

* Aby opublikować aplikację zarządzaną w witrynie Azure Marketplace, zobacz [Aplikacje zarządzane platformy Azure w witrynie Marketplace](publish-marketplace-app.md).
* Aby wdrożyć wystąpienie aplikacji zarządzanej, zobacz [Deploy service catalog app through Azure portal (Wdrażanie aplikacji wykazu usług przy użyciu witryny Azure Portal)](deploy-service-catalog-quickstart.md).
