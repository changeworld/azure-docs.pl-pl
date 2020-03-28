---
title: Samouczek - niestandardowe akcje & zasobami
description: W tym samouczku opisano sposób tworzenia aplikacji zarządzanej platformy Azure za pomocą dostawcy niestandardowego platformy Azure.
ms.topic: tutorial
ms.author: lazinnat
author: lazinnat
ms.date: 06/20/2019
ms.openlocfilehash: c3750da6bd76c8cb3908fbdc71ba676f09d77def
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75650079"
---
# <a name="tutorial-create-managed-application-with-custom-actions-and-resources"></a>Samouczek: Tworzenie aplikacji zarządzanej przy niestandardowych akcjach i zasobach

W tym samouczku utworzysz własną aplikację zarządzana z niestandardowymi akcjami i zasobami. Aplikacja zarządzana będzie zawierać akcję `Overview` niestandardową na stronie, niestandardowy typ `Table of Content` zasobu wyświetlany jako oddzielny element menu i niestandardową akcję kontekstu na niestandardowej stronie zasobu.

Ten samouczek zawiera następujące kroki:

> [!div class="checklist"]
> * Plik definicji interfejsu użytkownika autora do tworzenia wystąpienia aplikacji zarządzanej
> * Szablon wdrożenia autora z [dostawcą niestandardowym platformy Azure,](../custom-providers/overview.md)kontem usługi Azure Storage i funkcją platformy Azure
> * Artefakt definicji widoku autora z akcjami i zasobami niestandardowymi
> * Wdrażanie definicji aplikacji zarządzanej
> * Wdrażanie wystąpienia aplikacji zarządzanej
> * Wykonywanie akcji niestandardowych i tworzenie zasobów niestandardowych

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, musisz wiedzieć:

* Jak [utworzyć i opublikować definicję aplikacji zarządzanej](publish-service-catalog-app.md).
* Jak [wdrożyć aplikację wykazu usług za pośrednictwem witryny Azure portal](deploy-service-catalog-quickstart.md).
* Jak [utworzyć interfejs użytkownika portalu Azure dla zarządzanej aplikacji](create-uidefinition-overview.md).
* Wyświetl możliwości [artefaktu definicji.](concepts-view-definition.md)
* Możliwości [dostawcy niestandardowego platformy Azure.](../custom-providers/overview.md)

## <a name="user-interface-definition"></a>Definicja interfejsu użytkownika

W tym samouczku utworzysz aplikację zarządzaną, a jej zarządzana grupa zasobów będzie zawierać wystąpienie dostawcy niestandardowego, konto magazynu i funkcję. Funkcja platformy Azure używana w tym przykładzie implementuje interfejs API, który obsługuje operacje dostawcy niestandardowego dla akcji i zasobów. Konto usługi Azure Storage jest używane jako podstawowy magazyn dla zasobów dostawcy niestandardowego.

Definicja interfejsu użytkownika do tworzenia wystąpienia `funcname` `storagename` aplikacji zarządzanej zawiera i elementy wejściowe. Nazwa konta magazynu i nazwa funkcji muszą być unikatowe globalnie. Domyślnie pliki funkcyjne będą wdrażane z [przykładowego pakietu funkcji,](https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip)ale można je zmienić, dodając element wejściowy dla łącza pakietu w *pliku createUIDefinition.json*:

```json
{
  "name": "funcname",
  "type": "Microsoft.Common.TextBox",
  "label": "Name of the function to be created",
  "toolTip": "Name of the function to be created",
  "visible": true,
  "constraints": {
    "required": true
  }
},
{
  "name": "storagename",
  "type": "Microsoft.Common.TextBox",
  "label": "Name of the storage to be created",
  "toolTip": "Name of the storage to be created",
  "visible": true,
  "constraints": {
    "required": true
  }
},
{
  "name": "zipFileBlobUri",
  "type": "Microsoft.Common.TextBox",
  "defaultValue": "https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip/functionpackage.zip",
  "label": "The Uri to the uploaded function zip file",
  "toolTip": "The Uri to the uploaded function zip file",
  "visible": true
}
```

i dane wyjściowe w *createUIDefinition.json*:

```json
  "funcname": "[steps('applicationSettings').funcname]",
  "storageName": "[steps('applicationSettings').storagename]",
  "zipFileBlobUri": "[steps('applicationSettings').zipFileBlobUri]"
```

Kompletny *createUIDefinition.json* próbki można znaleźć w [Odwołuje: Artefakty elementów interfejsu użytkownika](reference-createuidefinition-artifact.md).

## <a name="template-with-custom-provider"></a>Szablon z dostawcą niestandardowym

Aby utworzyć wystąpienie aplikacji zarządzanej z dostawcą niestandardowym, należy zdefiniować niestandardowy zasób dostawcy o nazwie **publicznej** i wpisać **microsoft.CustomProviders/resourceProviders** w **mainTemplate.json**. W tym zasobie można zdefiniować typy zasobów i akcje dla usługi. Aby wdrożyć usługi Azure Function i instancji konta usługi Azure Storage, należy zdefiniować zasoby typu `Microsoft.Web/sites` i `Microsoft.Storage/storageAccounts` odpowiednio.

W tym samouczku `users` utworzysz `ping` jeden typ `users/contextAction` zasobu, akcję niestandardową i `users` akcję niestandardową, która zostanie wykonana w kontekście zasobu niestandardowego. Dla każdego typu zasobu i akcji należy podać punkt końcowy wskazujący funkcję o nazwie podanej w [pliku createUIDefinition.json](#user-interface-definition). Określ **typ marszruty** jako `Proxy,Cache` typy zasobów i `Proxy` akcje:

```json
{
  "apiVersion": "[variables('customrpApiversion')]",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "name": "[variables('customProviderName')]",
  "location": "[parameters('location')]",
  "properties": {
    "actions": [
      {
        "name": "ping",
        "routingType": "Proxy",
        "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
      },
      {
        "name": "users/contextAction",
        "routingType": "Proxy",
        "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
      }
    ],
    "resourceTypes": [
      {
        "name": "users",
        "routingType": "Proxy,Cache",
        "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
      }
    ]
  },
  "dependsOn": [
    "[concat('Microsoft.Web/sites/',parameters('funcname'))]"
  ]
}
```

Kompletny *przykład mainTemplate.json* można znaleźć w [reference: artefakt szablonu wdrożenia](reference-main-template-artifact.md).

## <a name="view-definition-artifact"></a>Wyświetlanie artefaktu definicji

Aby zdefiniować interfejs użytkownika, który zawiera akcje niestandardowe i zasoby niestandardowe w aplikacji zarządzanej, należy autor **viewDefinition.json** artefakt. Aby uzyskać więcej informacji na temat artefaktu definicji widoku, zobacz [Wyświetlanie artefaktu definicji w aplikacjach zarządzanych platformy Azure](concepts-view-definition.md).

W tym samouczku można zdefiniować:
* Strona *Przegląd* z przyciskiem paska `TestAction` narzędzi reprezentująca akcję niestandardową z podstawowym wprowadzaniem tekstu.
* Strona *Użytkownicy* reprezentująca niestandardowy typ `users`zasobu .
* Akcja `users/contextAction` zasobu niestandardowego na stronie *Użytkownicy,* która będzie `users`wykonywana w kontekście niestandardowego zasobu typu .

W poniższym przykładzie przedstawiono konfigurację widoku dla strony "Przegląd":

```json
{
    "kind": "Overview",
    "properties": {
      "header": "Welcome to your Demo Azure Managed Application",
      "description": "This Managed application with Custom Provider is for demo purposes only.",
      "commands": [{
          "displayName": "Ping Action",
          "path": "/customping",
          "icon": "LaunchCurrent"
      }]
    }
  }
```

Poniższy przykład zawiera konfigurację strony zasobów "Użytkownicy" z niestandardową akcją zasobów:

```json
{
    "kind": "CustomResources",
    "properties": {
      "displayName": "Users",
      "version": "1.0.0.0",
      "resourceType": "users",
      "createUIDefinition": {
      },
      "commands": [{
        "displayName": "Custom Context Action",
        "path": "users/contextAction",
        "icon": "Start"
      }],
      "columns": [
        { "key": "properties.FullName", "displayName": "Full Name" },
        { "key": "properties.Location", "displayName": "Location", "optional": true }
      ]
    }
  }
```

Kompletny *przykład viewDefinition.json* można znaleźć w [punkcie Reference: View definition artifact](reference-view-definition-artifact.md).

## <a name="managed-application-definition"></a>Definicja aplikacji zarządzanej

Pakiet następujących artefaktów aplikacji zarządzanych do archiwum zip i przekazać go do magazynu:

* tworzenieUiDedefdefition.json
* mainTemplate.json
* plik viewDefinition.json

Wszystkie pliki muszą być na poziomie głównym. Pakiet z artefaktami mogą być przechowywane w dowolnym magazynie, na przykład gitHub obiektu blob lub konta usługi Azure Storage obiektu blob. Oto skrypt do przekazania pakietu aplikacji na konto magazynu: 

```powershell
$resourceGroup="appResourcesGroup"
$storageName="mystorageaccount$RANDOM"

# Sign in to your Azure subscription
Connect-AzAccount
# Create resource group for managed application definition and application package
New-AzResourceGroup -Name $resourceGroup -Location eastus

# Create storage account for a package with application artifacts
$storageAccount=New-AzStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageName `
  -SkuName Standard_LRS `
  -Location eastus `
$ctx=$storageAccount.Context

# Create storage container and upload zip to blob
New-AzStorageContainer -Name appcontainer -Context $ctx -Permission blob
Set-AzStorageBlobContent `
  -File "path_to_your_zip_package" `
  -Container appcontainer `
  -Blob app.zip `
  -Context $ctx 

# Get blob absolute uri
$blobUri=(Get-AzureStorageBlob -Container appcontainer -Blob app.zip -Context $ctx).ICloudBlob.uri.AbsoluteUri
```

Uruchom skrypt interfejsu wiersza polecenia platformy Azure poniżej lub wykonaj kroki opisane w witrynie Azure portal, aby wdrożyć definicję aplikacji zarządzanej w wykazie usług:

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azurecli-interactive)

```azurecli-interactive
resourceGroup="appResourcesGroup"
# Select subscription and create resource group (if you have not created yet)
az account set --subscription <subscriptionID>
az group create --name $resourceGroup --location eastus

# Get object ID of your identity
userid=$(az ad user show --upn-or-object-id example@contoso.org --query objectId --output tsv)
# Get role definition ID for the Owner role
roleid=$(az role definition list --name Owner --query [].name --output tsv)

# Create managed application definition resource
az managedapp definition create \
  --name "ManagedUsersAppDefinition" \
  --location "eastus" \
  --resource-group $resourceGroup \
  --lock-level ReadOnly \
  --display-name "Managed users app definition" \
  --description "Managed application with Azure Custom Provider" \
  --authorizations "$userid:$roleid" \
  --package-file-uri "path to your app.zip package"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. W witrynie Azure portal wybierz pozycję **Wszystkie usługi**. Na liście zasobów wpisz i wybierz pozycję **Centrum aplikacji zarządzanych**.
2. W **Centrum aplikacji zarządzanych**wybierz **pozycję Definicja aplikacji wykazu usług** i kliknij przycisk **Dodaj**. 
    
    ![Dodawanie katalogu usług](./media/tutorial-create-managed-app-with-custom-provider/service-catalog-managed-application.png)

3. Podaj wartości tworzenia definicji katalogu usług:

    * Podaj unikatową **nazwę** definicji katalogu usług, **nazwy wyświetlanej** i *opisu*(opcjonalnie).
    * Wybierz **subskrypcję,** **grupę zasobów**i **lokalizację, w** której zostanie utworzona definicja aplikacji. Można użyć tej samej grupy zasobów, która jest używana dla pakietu zip lub utworzyć nową grupę zasobów.
    * W przypadku **uri pliku pakietu**podaj ścieżkę do pliku zip utworzonego w poprzednim kroku.

    ![Podaj wartości](./media/tutorial-create-managed-app-with-custom-provider/add-service-catalog-managed-application.png)

4. Po dojście do sekcji Poziom uwierzytelniania i blokady wybierz pozycję **Dodaj autoryzację**.

    ![Dodaj autoryzację](./media/tutorial-create-managed-app-with-custom-provider/add-authorization.png)

5. Wybierz grupę usługi Azure Active Directory, aby zarządzać zasobami, i wybierz **przycisk OK**.

   ![Dodaj grupę autoryzacji](./media/tutorial-create-managed-app-with-custom-provider/add-auth-group.png)

6. Po podaniu wszystkich wartości wybierz pozycję **Utwórz**.

   ![Tworzenie definicji aplikacji zarządzanej](./media/tutorial-create-managed-app-with-custom-provider/create-service-catalog-definition.png)

---

## <a name="managed-application-instance"></a>Wystąpienie aplikacji zarządzanej

Po wdrożeniu definicji aplikacji zarządzanej uruchom poniższy skrypt lub wykonaj kroki opisane w witrynie Azure Portal, aby wdrożyć wystąpienie aplikacji zarządzanej u niestandardowego dostawcy:

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azurecli-interactive)

```azurecli-interactive
appResourcesGroup="appResourcesGroup"
applicationGroup="usersApplicationGroup"

# Create resource group for managed application instance
az group create --name $applicationGroup --location eastus

# Get ID of managed application definition
appid=$(az managedapp definition show --name ManagedUsersAppDefinition --resource-group $appResourcesGroup --query id --output tsv)

# Create the managed application
az managedapp create \
  --name ManagedUsersApp \
  --location "eastus" \
  --kind "Servicecatalog" \
  --resource-group $applicationGroup \
  --managedapp-definition-id $appid \
  --managed-rg-id "managedResourcesGroup" \
  --parameters "{\"funcname\": {\"value\": \"managedusersappfunction\"}, \"storageName\": {\"value\": \"managedusersappstorage\"}}"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. W witrynie Azure portal wybierz pozycję **Wszystkie usługi**. Na liście zasobów wpisz i wybierz pozycję **Centrum aplikacji zarządzanych**.
2. W **Centrum aplikacji zarządzanych**wybierz pozycję **Service Catalog applications** and click **Add**. 

    ![Dodawanie aplikacji zarządzanej](./media/tutorial-create-managed-app-with-custom-provider/add-managed-application.png)

3. Na **stronie Aplikacje katalogu usług** wpisz nazwę wyświetlaną definicji katalogu usług w polu wyszukiwania. Zaznacz definicję utworzoną w poprzednim kroku i kliknij przycisk **Utwórz**.

    ![Wybieranie katalogu usług](./media/tutorial-create-managed-app-with-custom-provider/select-service-catalog-definition.png)

4. Podaj wartości tworzenia wystąpienia aplikacji zarządzanej z definicji wykazu usług:

    * Wybierz **subskrypcję,** **grupę zasobów**i **lokalizację, w** której zostanie utworzone wystąpienie aplikacji.
    * Podaj unikatową nazwę funkcji platformy Azure i nazwę konta usługi Azure Storage.

    ![Ustawienia aplikacji](./media/tutorial-create-managed-app-with-custom-provider/application-settings.png)

5. Po zakończeniu sprawdzania poprawności kliknij przycisk **OK,** aby wdrożyć wystąpienie aplikacji zarządzanej. 
    
    ![Wdrażanie aplikacji zarządzanej](./media/tutorial-create-managed-app-with-custom-provider/deploy-managed-application.png)

---

## <a name="custom-actions-and-resources"></a>Akcje i zasoby niestandardowe

Po wdrożeniu wystąpienia aplikacji wykazu usług masz dwie nowe grupy zasobów. Pierwsza grupa `applicationGroup` zasobów zawiera wystąpienie aplikacji zarządzanej, druga grupa `managedResourceGroup` zasobów przechowuje zasoby dla aplikacji zarządzanej, w tym **niestandardowego dostawcy**.

![Grupy zasobów aplikacji](./media/tutorial-create-managed-app-with-custom-provider/application-resource-groups.png)

Możesz przejść do wystąpienia aplikacji zarządzanej i wykonać **akcję niestandardową** na stronie "Przegląd", utworzyć **niestandardowy** zasób użytkowników na stronie "Użytkownicy" i uruchomić **niestandardową akcję kontekstu** dla zasobu niestandardowego.

* Przejdź do strony "Przegląd" i kliknij przycisk "Ping Action":

![Wykonywanie akcji niestandardowej](./media/tutorial-create-managed-app-with-custom-provider/perform-custom-action.png)

* Przejdź do strony "Użytkownicy" i kliknij przycisk "Dodaj". Podaj dane wejściowe do tworzenia zasobu i prześlij formularz:

![Tworzenie zasobu niestandardowego](./media/tutorial-create-managed-app-with-custom-provider/create-custom-resource.png)

* Przejdź do strony "Użytkownicy", wybierz zasób "Użytkownicy" i kliknij "Niestandardowa akcja kontekstowa":

![Tworzenie zasobu niestandardowego](./media/tutorial-create-managed-app-with-custom-provider/perform-custom-resource-action.png)

[!INCLUDE [clean-up-section-portal](../../../includes/clean-up-section-portal.md)]

## <a name="looking-for-help"></a>Szukam pomocy

Jeśli masz pytania dotyczące aplikacji zarządzanych platformy Azure, spróbuj zadać pytanie w sprawie [Przepełnienie stosu](https://stackoverflow.com/questions/tagged/azure-managedapps). Podobne pytanie mogło już zostać zadane i udzielono odpowiedzi, więc sprawdź najpierw przed wysłaniem. Dodaj tag, `azure-managedapps` aby uzyskać szybką odpowiedź!

## <a name="next-steps"></a>Następne kroki

Aby opublikować aplikację zarządzaną w witrynie Azure Marketplace, zobacz [Aplikacje zarządzane platformy Azure w witrynie Marketplace](publish-marketplace-app.md).

Dowiedz się więcej o [dostawcach niestandardowych platformy Azure](../custom-providers/overview.md).
