---
title: Wdróż Maszynę wirtualną przy użyciu języka C# i szablonu usługi Resource Manager | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać języka C# i szablonu usługi Resource Manager do wdrażania maszyny Wirtualnej platformy Azure.
services: virtual-machines-windows
documentationcenter: ''
author: davidmu1
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: bfba66e8-c923-4df2-900a-0c2643b81240
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: davidmu
ms.openlocfilehash: f2c488a891d1037535f1782da54544ac6af9dd41
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="deploy-an-azure-virtual-machine-using-c-and-a-resource-manager-template"></a>Wdróż maszynę wirtualną platformy Azure przy użyciu języka C# i szablonu usługi Resource Manager
W tym artykule przedstawiono sposób wdrażania szablonu usługi Azure Resource Manager przy użyciu języka C#. Tworzony szablon wdraża jednej maszyny wirtualnej z systemem Windows Server w nowej sieci wirtualnej z pojedynczą podsiecią.

Aby uzyskać szczegółowy opis zasobu maszyny wirtualnej, zobacz [maszyn wirtualnych w szablonie usługi Azure Resource Manager](template-description.md). Aby uzyskać więcej informacji na temat wszystkich zasobów w szablonie, zobacz [Przewodnik po szablonie usługi Azure Resource Manager](../../azure-resource-manager/resource-manager-template-walkthrough.md).

Wykonaj te kroki trwa około 10 minut.

## <a name="create-a-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

W tym kroku należy się upewnić się, że jest zainstalowany program Visual Studio i Utwórz aplikację konsoli używane do wdrażania szablonu.

1. Jeśli nie jest jeszcze zainstalować [programu Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio). Wybierz **tworzenia klasycznych aplikacji .NET** na stronie obciążeń, a następnie kliknij **zainstalować**. Podsumowując, można stwierdzić, że **narzędzi programistycznych platformy .NET Framework 4 4.6** jest automatycznie wybrana. Jeśli zainstalowano program Visual Studio można dodać obciążenia .NET przy użyciu Uruchom okno programu Visual Studio.
2. W programie Visual Studio, kliknij przycisk **pliku** > **nowy** > **projektu**.
3. W **szablony** > **Visual C#**, wybierz pozycję **aplikacji konsoli (.NET Framework)**, wprowadź *myDotnetProject* dla nazwy Projekt, wybierz lokalizację projektu, a następnie kliknij przycisk **OK**.

## <a name="install-the-packages"></a>Instalowanie pakietów

Najprostszym sposobem zainstalowania bibliotek, które muszą zakończyć te kroki są pakiety NuGet. Aby uzyskać bibliotek, które należy w programie Visual Studio, wykonaj następujące kroki:

1. Kliknij przycisk **narzędzia** > **Menedżera pakietów Nuget**, a następnie kliknij przycisk **Konsola Menedżera pakietów**.
2. W konsoli, wpisz następujące polecenia:

    ```
    Install-Package Microsoft.Azure.Management.Fluent
    Install-Package WindowsAzure.Storage
    ```

## <a name="create-the-files"></a>Tworzenie plików

W tym kroku utworzysz pliku szablonu, który wdraża zasobów i pliku parametrów, które dostarcza wartości parametru do szablonu. Możesz również utworzyć pliku autoryzacji, który służy do wykonywania operacji usługi Azure Resource Manager.

### <a name="create-the-template-file"></a>Utwórz plik szablonu

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy *myDotnetProject* > **Dodaj** > **nowy element**, a następnie wybierz **pliku tekstowego** w *elementów Visual C#*. Nadaj nazwę plikowi *CreateVMTemplate.json*, a następnie kliknij przycisk **Dodaj**.
2. Dodaj ten kod JSON do pliku, który został utworzony:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "adminUsername": { "type": "string" },
        "adminPassword": { "type": "securestring" }
      },
      "variables": {
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks','myVNet')]", 
        "subnetRef": "[concat(variables('vnetID'),'/subnets/mySubnet')]", 
      },
      "resources": [
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/publicIPAddresses",
          "name": "myPublicIPAddress",
          "location": "[resourceGroup().location]",
          "properties": {
            "publicIPAllocationMethod": "Dynamic",
            "dnsSettings": {
              "domainNameLabel": "myresourcegroupdns1"
            }
          }
        },
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "myVNet",
          "location": "[resourceGroup().location]",
          "properties": {
            "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
            "subnets": [
              {
                "name": "mySubnet",
                "properties": { "addressPrefix": "10.0.0.0/24" }
              }
            ]
          }
        },
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "myNic",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Network/publicIPAddresses/', 'myPublicIPAddress')]",
            "[resourceId('Microsoft.Network/virtualNetworks/', 'myVNet')]"
          ],
          "properties": {
            "ipConfigurations": [
              {
                "name": "ipconfig1",
                "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "publicIPAddress": { "id": "[resourceId('Microsoft.Network/publicIPAddresses','myPublicIPAddress')]" },
                  "subnet": { "id": "[variables('subnetRef')]" }
                }
              }
            ]
          }
        },
        {
          "apiVersion": "2016-04-30-preview",
          "type": "Microsoft.Compute/virtualMachines",
          "name": "myVM",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Network/networkInterfaces/', 'myNic')]"
          ],
          "properties": {
            "hardwareProfile": { "vmSize": "Standard_DS1" },
            "osProfile": {
              "computerName": "myVM",
              "adminUsername": "[parameters('adminUsername')]",
              "adminPassword": "[parameters('adminPassword')]"
            },
            "storageProfile": {
              "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "2012-R2-Datacenter",
                "version": "latest"
              },
              "osDisk": {
                "name": "myManagedOSDisk",
                "caching": "ReadWrite",
                "createOption": "FromImage"
              }
            },
            "networkProfile": {
              "networkInterfaces": [
                {
                  "id": "[resourceId('Microsoft.Network/networkInterfaces','myNic')]"
                }
              ]
            }
          }
        }
      ]
    }
    ```

3. Zapisz plik CreateVMTemplate.json.

### <a name="create-the-parameters-file"></a>Tworzenie pliku parametrów

Aby określić wartości parametrów zasobów, które są zdefiniowane w szablonie, należy utworzyć plik parametrów zawierający wartości.

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy *myDotnetProject* > **Dodaj** > **nowy element**, a następnie wybierz **pliku tekstowego** w *elementów Visual C#*. Nadaj nazwę plikowi *parameters.JSON następującym kodem*, a następnie kliknij przycisk **Dodaj**.
2. Dodaj ten kod JSON do pliku, który został utworzony:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "adminUserName": { "value": "azureuser" },
        "adminPassword": { "value": "Azure12345678" }
      }
    }
    ```

4. Zapisywanie pliku parameters.JSON następującym kodem.

### <a name="create-the-authorization-file"></a>Tworzenie pliku autoryzacji

Zanim będzie można wdrożyć szablon, upewnij się, że masz dostęp do [nazwy głównej usługi Active Directory](../../resource-group-authenticate-service-principal.md). Z nazwy głównej usługi należy uzyskać token dla uwierzytelniania żądań do usługi Azure Resource Manager. Należy również zarejestrować identyfikator aplikacji, klucz uwierzytelniania i Identyfikatora dzierżawy, które są potrzebne w pliku autoryzacji.

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy *myDotnetProject* > **Dodaj** > **nowy element**, a następnie wybierz **pliku tekstowego** w *elementów Visual C#*. Nadaj nazwę plikowi *azureauth.properties*, a następnie kliknij przycisk **Dodaj**.
2. Dodaj te właściwości autoryzacji:

    ```
    subscription=<subscription-id>
    client=<application-id>
    key=<authentication-key>
    tenant=<tenant-id>
    managementURI=https://management.core.windows.net/
    baseURL=https://management.azure.com/
    authURL=https://login.windows.net/
    graphURL=https://graph.windows.net/
    ```

    Zastąp **&lt;identyfikator subskrypcji&gt;** z identyfikatorem subskrypcji **&lt;identyfikator aplikacji&gt;** z identyfikatorem aplikacji usługi Active Directory **&lt;klucz uwierzytelniania&gt;** z klucz aplikacji i **&lt;identyfikator dzierżawcy&gt;** przy użyciu identyfikatora dzierżawcy.

3. Zapisz plik azureauth.properties.
4. Ustaw zmienną środowiskową systemu Windows o nazwie AZURE_AUTH_LOCATION z pełną ścieżkę do pliku autoryzacji utworzony, na przykład następujące PowerShell, można użyć polecenia:

    ```
    [Environment]::SetEnvironmentVariable("AZURE_AUTH_LOCATION", "C:\Visual Studio 2017\Projects\myDotnetProject\myDotnetProject\azureauth.properties", "User")
    ```
    
## <a name="create-the-management-client"></a>Tworzenie klienta zarządzania

1. Otwórz plik Program.cs w projekcie, który został utworzony, a następnie dodaj te instrukcje do istniejącej deklaracji using u góry pliku:

    ```
    using Microsoft.Azure.Management.Compute.Fluent;
    using Microsoft.Azure.Management.Compute.Fluent.Models;
    using Microsoft.Azure.Management.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

2. Aby utworzyć klient zarządzania, Dodaj ten kod do metody Main:

    ```
    var credentials = SdkContext.AzureCredentialsFactory
        .FromFile(Environment.GetEnvironmentVariable("AZURE_AUTH_LOCATION"));

    var azure = Azure
        .Configure()
        .WithLogLevel(HttpLoggingDelegatingHandler.Level.Basic)
        .Authenticate(credentials)
        .WithDefaultSubscription();
    ```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Aby określić wartości dla aplikacji, należy dodać kodu dla metody Main:

```
var groupName = "myResourceGroup";
var location = Region.USWest;

var resourceGroup = azure.ResourceGroups.Define(groupName)
    .WithRegion(location)
    .Create();
```

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Szablon i parametry są wdrażane z konta magazynu na platformie Azure. W tym kroku możesz utworzyć konto i przekazać pliki. 

Aby utworzyć konto, Dodaj ten kod do metody Main:

```
string storageAccountName = SdkContext.RandomResourceName("st", 10);

Console.WriteLine("Creating storage account...");
var storage = azure.StorageAccounts.Define(storageAccountName)
    .WithRegion(Region.USWest)
    .WithExistingResourceGroup(resourceGroup)
    .Create();

var storageKeys = storage.GetKeys();
string storageConnectionString = "DefaultEndpointsProtocol=https;"
    + "AccountName=" + storage.Name
    + ";AccountKey=" + storageKeys[0].Value
    + ";EndpointSuffix=core.windows.net";

var account = CloudStorageAccount.Parse(storageConnectionString);
var serviceClient = account.CreateCloudBlobClient();

Console.WriteLine("Creating container...");
var container = serviceClient.GetContainerReference("templates");
container.CreateIfNotExistsAsync().Wait();
var containerPermissions = new BlobContainerPermissions()
    { PublicAccess = BlobContainerPublicAccessType.Container };
container.SetPermissionsAsync(containerPermissions).Wait();

Console.WriteLine("Uploading template file...");
var templateblob = container.GetBlockBlobReference("CreateVMTemplate.json");
templateblob.UploadFromFileAsync("..\\..\\CreateVMTemplate.json").Result();

Console.WriteLine("Uploading parameters file...");
var paramblob = container.GetBlockBlobReference("Parameters.json");
paramblob.UploadFromFileAsync("..\\..\\Parameters.json").Result();
```

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Wdrażanie szablonu i parametrów z konta magazynu, który został utworzony. 

Aby wdrożyć szablon, Dodaj ten kod do metody Main:

```
var templatePath = "https://" + storageAccountName + ".blob.core.windows.net/templates/CreateVMTemplate.json";
var paramPath = "https://" + storageAccountName + ".blob.core.windows.net/templates/Parameters.json";
var deployment = azure.Deployments.Define("myDeployment")
    .WithExistingResourceGroup(groupName)
    .WithTemplateLink(templatePath, "1.0.0.0")
    .WithParametersLink(paramPath, "1.0.0.0")
    .WithMode(Microsoft.Azure.Management.ResourceManager.Fluent.Models.DeploymentMode.Incremental)
    .Create();
Console.WriteLine("Press enter to delete the resource group...");
Console.ReadLine();
```

## <a name="delete-the-resources"></a>Usuwanie zasobów

Ponieważ naliczane są opłaty za zasoby używane na platformie Azure, zawsze jest dobrym rozwiązaniem, aby usunąć zasoby, które nie są już potrzebne. Nie trzeba osobno usunąć wszystkie zasoby w grupie zasobów. Usuń grupę zasobów i wszystkie jej zasoby są automatycznie usuwane. 

Aby usunąć grupę zasobów, Dodaj ten kod do metody Main:

```
azure.ResourceGroups.DeleteByName(groupName);
```

## <a name="run-the-application"></a>Uruchamianie aplikacji

Zakończ powinno zająć około pięciu minut dla tej aplikacji konsoli uruchomić zupełnie od początku. 

1. Aby uruchomić aplikację konsoli, kliknij przycisk **Start**.

2. Przed naciśnięciem przycisku **Enter** zacząć usuwanie zasobów może potrwać kilka minut, aby zweryfikować utworzenie zasobów w portalu Azure. Kliknij stan wdrożenia, aby wyświetlić informacje o wdrożeniu.

## <a name="next-steps"></a>Kolejne kroki
* Jeśli wystąpiły problemy dotyczące wdrożenia, następnym krokiem powinno być aby przyjrzeć się [Rozwiąż typowe błędy wdrożenia usługi Azure z usługą Azure Resource Manager](../../resource-manager-common-deployment-errors.md).
* Dowiedz się, jak wdrożyć maszynę wirtualną i jej zasobach pomocnicze, przeglądając [wdrażanie Azure maszyny wirtualnej przy użyciu języka C#](csharp.md).
