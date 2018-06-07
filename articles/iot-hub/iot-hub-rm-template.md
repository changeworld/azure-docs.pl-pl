---
title: Tworzenie Centrum IoT Azure przy użyciu szablonu (.NET) | Dokumentacja firmy Microsoft
description: Jak szablon Menedżera zasobów Azure umożliwia tworzenie Centrum IoT z programem C#.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 1a64749b7218fccfdad6b6eeebfac39a44aa0522
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34635546"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-net"></a>Tworzenie Centrum IoT przy użyciu szablonu usługi Azure Resource Manager (.NET)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Tworzenie i zarządzanie nimi centra Azure IoT programowo, można użyć usługi Azure Resource Manager. Ten samouczek pokazuje, jak używać szablonu usługi Azure Resource Manager do tworzenia Centrum IoT z programu C#.

> [!NOTE]
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [usługi Azure Resource Manager i Model Klasyczny](../azure-resource-manager/resource-manager-deployment-model.md).  W tym artykule omówiono przy użyciu modelu wdrażania usługi Azure Resource Manager.

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Program Visual Studio 2015 lub Visual Studio 2017.
* Aktywne konto platformy Azure. <br/>Jeśli go nie masz, możesz utworzyć [bezpłatne konto][lnk-free-trial] w zaledwie kilka minut.
* [Konta magazynu Azure] [ lnk-storage-account] przechowywania plików szablonu usługi Azure Resource Manager.
* [Azure PowerShell 1.0] [ lnk-powershell-install] lub nowszym.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Przygotowanie projektu programu Visual Studio

1. W programie Visual Studio, utworzyć projekt Visual C# pulpitu systemu Windows klasycznego za pomocą **aplikacji konsoli (.NET Framework)** szablonu projektu. Nazwij projekt **CreateIoTHub**.

2. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt, a następnie kliknij przycisk **Zarządzaj pakietami NuGet**.

3. Sprawdź w Menedżerze pakietów NuGet **Uwzględnij wersję wstępną**i na **Przeglądaj** wyszukiwanie strony **Microsoft.Azure.Management.ResourceManager**. Wybierz pakiet, kliknij przycisk **zainstalować**w **Przejrzyj zmiany** kliknij **OK**, następnie kliknij przycisk **akceptuję** do akceptowania licencji.

4. Wyszukaj w Menedżerze pakietów NuGet **Microsoft.IdentityModel.Clients.ActiveDirectory**.  Kliknij przycisk **zainstalować**w **Przejrzyj zmiany** kliknij **OK**, następnie kliknij przycisk **akceptuję** do akceptowania licencji.

5. W pliku Program.cs Zastąp istniejące **przy użyciu** instrukcje następującym kodem:

    ```csharp
    using System;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```

6. W pliku Program.cs Dodaj następujące zmienne statyczne, zastępując symbole zastępcze. Należy zanotować **ApplicationId**, **SubscriptionId**, **TenantId**, i **hasło** we wcześniejszej części tego samouczka. **Nazwa konta usługi Azure Storage** nazwę konta usługi Azure Storage służy do przechowywania plików szablonu usługi Azure Resource Manager. **Nazwa grupy zasobów** to nazwa grupy zasobów, używane podczas tworzenia Centrum IoT. Nazwa może być wstępnie istniejącej lub nowej grupy zasobów. **Nazwa wdrożenia** jest nazwę wdrożenia, takich jak **Deployment_01**.

    ```csharp
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    static string storageAddress = "https://{Your storage account name}.blob.core.windows.net";
    static string rgName = "{Resource group name}";
    static string deploymentName = "{Deployment name}";
    ```

[!INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="submit-a-template-to-create-an-iot-hub"></a>Przesyłanie szablonu do tworzenia Centrum IoT

Plik JSON szablonu i parametr umożliwia utworzenie Centrum IoT w grupie zasobów. Aby wprowadzić zmiany w istniejących Centrum IoT umożliwia także szablonu usługi Azure Resource Manager.

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt, kliknij przycisk **Dodaj**, a następnie kliknij przycisk **nowy element**. Dodaj plik JSON o nazwie **template.json** do projektu.

2. Można dodać standardowej Centrum IoT na **wschodnie stany USA** regionu, Zastąp zawartość **template.json** z następującą definicję zasobu. Dla bieżącą listę regionów, które obsługują Centrum IoT [stan Azure][lnk-status]:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": {
          "type": "string"
        }
      },
      "resources": [
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs",
        "name": "[parameters('hubName')]",
        "location": "East US",
        "sku": {
          "name": "S1",
          "tier": "Standard",
          "capacity": 1
        },
        "properties": {
          "location": "East US"
        }
      }
      ],
      "outputs": {
        "hubKeys": {
          "value": "[listKeys(resourceId('Microsoft.Devices/IotHubs', parameters('hubName')), '2016-02-03')]",
          "type": "object"
        }
      }
    }
    ```

3. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt, kliknij przycisk **Dodaj**, a następnie kliknij przycisk **nowy element**. Dodaj plik JSON o nazwie **parameters.JSON następującym kodem** do projektu.

4. Zastąp zawartość **parameters.JSON następującym kodem** z następującymi informacjami parametr, który określa nazwę nowego centrum IoT, takich jak **{inicjały} mynewiothub**. Nazwa centrum IoT musi być globalnie unikatowa, powinien on zawierać nazwa lub inicjały:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": { "value": "mynewiothub" }
      }
    }
    ```
  [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

5. W **Eksploratora serwera**nawiązać połączenia z subskrypcją platformy Azure i na koncie magazynu Azure należy utworzyć kontener o nazwie **szablony**. W **właściwości** panelu, ustaw **publiczny dostęp do odczytu** uprawnienia dla **szablony** kontener, aby **obiektu Blob**.

6. W **Eksploratora serwera**, kliknij prawym przyciskiem myszy **szablony** kontenera, a następnie kliknij przycisk **kontenera obiektów Blob widoku**. Kliknij przycisk **przekazania obiektu Blob** przycisku, wybierz dwa pliki **parameters.JSON następującym kodem** i **templates.json**, a następnie kliknij przycisk **Otwórz** do przekazania Pliki JSON do **szablony** kontenera. Adresy URL obiektów blob zawierający dane JSON są:

    ```csharp
    https://{Your storage account name}.blob.core.windows.net/templates/parameters.json
    https://{Your storage account name}.blob.core.windows.net/templates/template.json
    ```
7. Dodaj następującą metodę do pliku Program.cs:

    ```csharp
    static void CreateIoTHub(ResourceManagementClient client)
    {

    }
    ```

8. Dodaj następujący kod do **CreateIoTHub** metody do przesyłania plików szablonu i parametru do usługi Azure Resource Manager:

    ```csharp
    var createResponse = client.Deployments.CreateOrUpdate(
        rgName,
        deploymentName,
        new Deployment()
        {
          Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            TemplateLink = new TemplateLink
            {
              Uri = storageAddress + "/templates/template.json"
            },
            ParametersLink = new ParametersLink
            {
              Uri = storageAddress + "/templates/parameters.json"
            }
          }
        });
    ```

9. Dodaj następujący kod do **CreateIoTHub** metodę, która wyświetla stan i klucze dla nowego centrum IoT:

    ```csharp
    string state = createResponse.Properties.ProvisioningState;
    Console.WriteLine("Deployment state: {0}", state);

    if (state != "Succeeded")
    {
      Console.WriteLine("Failed to create iothub");
    }
    Console.WriteLine(createResponse.Properties.Outputs);
    ```

## <a name="complete-and-run-the-application"></a>Zakończenie i uruchomić aplikację

Możesz teraz ukończyć aplikacji przez wywołanie metody **CreateIoTHub** metody, aby skompilować i uruchomić go.

1. Dodaj następujący kod na końcu **Main** metody:

    ```csharp
    CreateIoTHub(client);
    Console.ReadLine();
    ```

2. Kliknij przycisk **kompilacji** , a następnie **zbudować rozwiązanie**. Popraw błędy.

3. Kliknij przycisk **debugowania** , a następnie **Rozpocznij debugowanie** do uruchomienia aplikacji. Może upłynąć kilka minut dla uruchamiania wdrożenia.

4. Aby sprawdzić aplikacji dodane nowe Centrum IoT, odwiedź stronę [portalu Azure] [ lnk-azure-portal] i wyświetlanie listy zasobów. Można również użyć **Get-AzureRmResource** polecenia cmdlet programu PowerShell.

> [!NOTE]
> Ta przykładowa aplikacja dodaje Centrum IoT standardowe S1 dla której są rozliczane. Można usunąć Centrum IoT za pośrednictwem [portalu Azure] [ lnk-azure-portal] lub za pomocą **AzureRmResource Usuń** polecenia cmdlet programu PowerShell po zakończeniu.

## <a name="next-steps"></a>Kolejne kroki
Po wdrożeniu Centrum IoT przy użyciu szablonu usługi Azure Resource Manager z programem C#, może zajść potrzeba dalszego zbadania:

* Przeczytaj informacje o możliwości [interfejsu API REST dostawcy zasobów Centrum IoT][lnk-rest-api].
* Odczyt [Omówienie usługi Azure Resource Manager] [ lnk-azure-rm-overview] Aby dowiedzieć się więcej o możliwościach usługi Azure Resource Manager.

Aby dowiedzieć się więcej o tworzeniu aplikacji Centrum IoT, zobacz następujące artykuły:

* [Wprowadzenie do zestawu SDK C][lnk-c-sdk]
* [Zestawy SDK Azure IoT][lnk-sdks]

Aby dokładniej analizować możliwości Centrum IoT, zobacz:

* [Wdrażanie rozwiązań SI na urządzeniach brzegowych przy użyciu usługi Azure IoT Edge][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md
[lnk-storage-account]:../storage/common/storage-create-storage-account.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
