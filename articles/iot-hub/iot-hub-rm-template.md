---
title: Tworzenie usługi Azure IoT Hub przy użyciu szablonu (.NET) | Dokumentacja firmy Microsoft
description: Jak utworzyć Centrum IoT Hub przy użyciu programu C# za pomocą szablonu usługi Azure Resource Manager.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/08/2017
ms.openlocfilehash: 84446090da2feaee3005b4ef90ace77b468a3f1a
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2019
ms.locfileid: "59357378"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-net"></a>Tworzenie Centrum IoT przy użyciu szablonu usługi Azure Resource Manager (.NET)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Usługi Azure Resource Manager służy do tworzenia i zarządzania usługi Azure IoT hubs programowo. W tym samouczku dowiesz się, jak utworzyć Centrum IoT hub z programu C# za pomocą szablonu usługi Azure Resource Manager.

> [!NOTE]
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi:  [Usługa Azure Resource Manager i Model Klasyczny](../azure-resource-manager/resource-manager-deployment-model.md).  W tym artykule opisano, przy użyciu modelu wdrażania usługi Azure Resource Manager.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Program Visual Studio 2015 lub Visual Studio 2017.
* Aktywne konto platformy Azure. <br/>Jeśli go nie masz, możesz utworzyć [bezpłatne konto][lnk-free-trial] w zaledwie kilka minut.
* [Konta usługi Azure Storage] [ lnk-storage-account] której możesz przechowywać pliki szablonu usługi Azure Resource Manager.
* [Azure PowerShell 1.0] [ lnk-powershell-install] lub nowszej.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Przygotowanie projektu programu Visual Studio

1. W programie Visual Studio Utwórz projekt Visual C# Windows Classic Desktop za pomocą **Aplikacja konsoli (.NET Framework)** szablonu projektu. Nadaj projektowi nazwę **CreateIoTHub**.

2. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy nad projektem, a następnie kliknij przycisk **Zarządzaj pakietami NuGet**.

3. Sprawdź w Menedżerze pakietów NuGet **Uwzględnij wersję wstępną**, a następnie na **Przeglądaj** strony wyszukiwanie **Microsoft.Azure.Management.ResourceManager**. Wybierz pakiet, kliknij przycisk **zainstalować**w **przeglądanie zmian** kliknij **OK**, następnie kliknij przycisk **akceptuję** zaakceptowania licencji.

4. W Menedżerze pakietów NuGet, wyszukaj **Microsoft.IdentityModel.Clients.ActiveDirectory**.  Kliknij przycisk **zainstalować**w **przeglądanie zmian** kliknij **OK**, następnie kliknij przycisk **akceptuję** Aby zaakceptować licencję.

5. W pliku Program.cs Zastąp istniejące **przy użyciu** instrukcji z następującym kodem:

    ```csharp
    using System;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```

6. W pliku Program.cs Dodaj następujące zmienne statyczne, zastępując wartość symbolu zastępczego. Należy zanotować **ApplicationId**, **SubscriptionId**, **TenantId**, i **hasło** wcześniej w tym samouczku. **Nazwa konta magazynu Azure** jest nazwa konta usługi Azure Storage, w którym są przechowywane pliki szablonu usługi Azure Resource Manager. **Nazwa grupy zasobów** to nazwa grupy zasobów, możesz użyć podczas tworzenia Centrum IoT. Nazwa może zawierać wstępnie istniejącej lub nowej grupy zasobów. **Nazwa wdrożenia** jest nazwa wdrożenia, takich jak **Deployment_01**.

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

## <a name="submit-a-template-to-create-an-iot-hub"></a>Przesyłanie szablonu do utworzenia Centrum IoT

Tworzenie Centrum IoT w grupie zasobów przy użyciu pliku szablonu oraz parametrów JSON. Można również użyć szablonu usługi Azure Resource Manager do wprowadzania zmian w istniejącym Centrum IoT.

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt, kliknij przycisk **Dodaj**, a następnie kliknij przycisk **nowy element**. Dodaj plik JSON o nazwie **template.json** do projektu.

2. Aby dodać standardowa usługi IoT hub do **wschodnie stany USA** regionu, Zastąp zawartość **template.json** przy użyciu następującej definicji zasobu. Aby uzyskać bieżącą listę regionów, które obsługują usługi IoT Hub, zobacz [stanu platformy Azure][lnk-status]:

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

3. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt, kliknij przycisk **Dodaj**, a następnie kliknij przycisk **nowy element**. Dodaj plik JSON o nazwie **parameters.json** do projektu.

4. Zastąp zawartość **parameters.json** z następującymi informacjami parametr, który określa nazwę nowego centrum IoT, takich jak **{inicjały} mynewiothub**. Tak powinien on zawierać Twoje nazwisko lub inicjały musi być globalnie unikatowa nazwa Centrum IoT:

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

5. W **Eksploratora serwera**, a następnie nawiązać połączenie z subskrypcją platformy Azure i w ramach konta usługi Azure Storage Utwórz kontener o nazwie **szablony**. W **właściwości** panelu, ustaw **publicznego dostępu do odczytu** uprawnienia dla **szablony** kontener **obiektu Blob**.

6. W **Eksploratora serwera**, kliknij prawym przyciskiem myszy **szablony** kontenera, a następnie kliknij przycisk **kontenera obiektów Blob widoku**. Kliknij przycisk **przekazywanie obiektu Blob** przycisku, wybierz dwa pliki **parameters.json** i **templates.json**, a następnie kliknij przycisk **Otwórz** do przekazania Pliki JSON **szablony** kontenera. Adresy URL obiektów blob zawierający dane JSON to:

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

8. Dodaj następujący kod do **CreateIoTHub** metody do przesyłania plików szablonu oraz parametrów do usługi Azure Resource Manager:

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

9. Dodaj następujący kod do **CreateIoTHub** metoda, która wyświetla stan i klucze służące do nowego centrum IoT:

    ```csharp
    string state = createResponse.Properties.ProvisioningState;
    Console.WriteLine("Deployment state: {0}", state);

    if (state != "Succeeded")
    {
      Console.WriteLine("Failed to create iothub");
    }
    Console.WriteLine(createResponse.Properties.Outputs);
    ```

## <a name="complete-and-run-the-application"></a>Ukończenie, a następnie uruchom aplikację

Możesz teraz ukończyć aplikacji, wywołując **CreateIoTHub** metoda przed Kompiluj i uruchom go.

1. Dodaj następujący kod na końcu **Main** metody:

    ```csharp
    CreateIoTHub(client);
    Console.ReadLine();
    ```

2. Kliknij przycisk **kompilacji** i następnie **Kompiluj rozwiązanie**. Usuń wszelkie błędy.

3. Kliknij przycisk **debugowania** i następnie **Rozpocznij debugowanie** do uruchomienia aplikacji. Może upłynąć kilka minut, zanim uruchamiania wdrożenia.

4. Aby sprawdzić, aplikacja dodaje nowe Centrum IoT hub, odwiedź stronę [witryny Azure portal] [ lnk-azure-portal] i wyświetlanie listy zasobów. Można również użyć **Get AzResource** polecenia cmdlet programu PowerShell.

> [!NOTE]
> Ta przykładowa aplikacja dodaje S1 standardowa usługi IoT Hub dla którego stosowana jest stawka. Możesz usunąć Centrum IoT hub za pośrednictwem [witryny Azure portal] [ lnk-azure-portal] lub za pomocą **AzResource Usuń** polecenia cmdlet programu PowerShell po zakończeniu.

## <a name="next-steps"></a>Kolejne kroki
Po wdrożeniu usługi IoT hub przy użyciu szablonu usługi Azure Resource Manager z programem C#, warto dokładniej:

* Przeczytaj o możliwości [interfejs API REST dostawcy zasobów usługi IoT Hub][lnk-rest-api].
* Odczyt [Omówienie usługi Azure Resource Manager] [ lnk-azure-rm-overview] Aby dowiedzieć się więcej o możliwościach usługi Azure Resource Manager.
* Składnię JSON i właściwości do użycia w szablonach, zobacz [typów zasobów Microsoft.Devices](/azure/templates/microsoft.devices/iothub-allversions).

Aby dowiedzieć się więcej na temat tworzenia usługi IoT Hub, zobacz następujące artykuły:

* [Wprowadzenie do zestawu SDK języka C][lnk-c-sdk]
* [Zestawy SDK Azure IoT][lnk-sdks]

Aby bliżej zapoznać się z możliwościami usługi IoT Hub, zobacz:

* [Wdrażanie rozwiązań SI na urządzeniach brzegowych przy użyciu usługi Azure IoT Edge][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: /powershell/azure/install-Az-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md
[lnk-storage-account]:../storage/common/storage-create-storage-account.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
