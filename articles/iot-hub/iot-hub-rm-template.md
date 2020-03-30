---
title: Tworzenie centrum Usługi Azure IoT hub przy użyciu szablonu (.NET) | Dokumenty firmy Microsoft
description: Jak użyć szablonu usługi Azure Resource Manager do utworzenia Centrum IoT z programem Języka C#.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/08/2017
ms.openlocfilehash: 02e814a9da320d688fe57edf3a3fe0640b8f5a47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75976735"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-net"></a>Tworzenie centrum IoT przy użyciu szablonu usługi Azure Resource Manager (.NET)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Usługi Azure Resource Manager można używać do programowego tworzenia centrów Usługi Azure IoT i zarządzania nimi. W tym samouczku pokazano, jak używać szablonu usługi Azure Resource Manager do tworzenia centrum IoT hub z programu Języka C#.

> [!NOTE]
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Usługi Azure Resource Manager i classic.](../azure-resource-manager/management/deployment-models.md)  W tym artykule opisano przy użyciu modelu wdrażania usługi Azure Resource Manager.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Program Visual Studio.
* Aktywne konto platformy Azure. <br/>Jeśli go nie masz, możesz utworzyć [bezpłatne konto][lnk-free-trial] w zaledwie kilka minut.
* [Konto usługi Azure Storage,][lnk-storage-account] na którym można przechowywać pliki szablonów usługi Azure Resource Manager.
* [Program Azure PowerShell 1.0][lnk-powershell-install] lub nowsza.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Przygotowywanie projektu programu Visual Studio

1. W programie Visual Studio utwórz projekt klasycznego pulpitu systemu Windows w języku Visual C# przy użyciu szablonu projektu **aplikacji konsoli (NET Framework).** Nazwij projekt **CreateIoTHub**.

2. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt, a następnie kliknij polecenie **Zarządzaj pakietami NuGet**.

3. W Menedżerze pakietów NuGet zaznacz pole **wyboru Uwzględnij wydanie wstępne**i na stronie Przeglądaj wyszukiwanie w **witrynie** **Microsoft.Azure.Management.ResourceManager**. Wybierz pakiet, kliknij pozycję **Zainstaluj**, w **obszarze Przejrzyj zmiany** kliknij przycisk **OK**, a następnie kliknij pozycję **Akceptuję,** aby zaakceptować licencje.

4. W Menedżerze pakietów NuGet wyszukaj hasło **Microsoft.IdentityModel.Clients.ActiveDirectory**.  Kliknij **pozycję Zainstaluj**, w **ciemiękaj zmiany** kliknij przycisk **OK**, a następnie kliknij pozycję **Akceptuję,** aby zaakceptować licencję.

5. W Program.cs zastąp istniejące **instrukcje using** następującym kodem:

    ```csharp
    using System;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```

6. W Program.cs dodaj następujące zmienne statyczne zastępujące wartości zastępcze. Zanotowano **applicationid**, **SubscriptionId**, **TenantId**i **Hasło** wcześniej w tym samouczku. **Nazwa konta usługi Azure Storage** to nazwa konta usługi Azure Storage, na którym przechowujesz pliki szablonów usługi Azure Resource Manager. **Nazwa grupy zasobów** to nazwa grupy zasobów używanej podczas tworzenia centrum IoT Hub. Nazwa może być wcześniej istniejącą lub nową grupą zasobów. **Nazwa wdrożenia** to nazwa wdrożenia, na przykład **Deployment_01**.

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

## <a name="submit-a-template-to-create-an-iot-hub"></a>Prześlij szablon, aby utworzyć centrum IoT

Użyj szablonu JSON i pliku parametrów, aby utworzyć centrum IoT w grupie zasobów. Można również użyć szablonu usługi Azure Resource Manager, aby wprowadzić zmiany w istniejącym centrum IoT Hub.

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt, kliknij polecenie **Dodaj**, a następnie kliknij polecenie **Nowy element**. Dodaj plik JSON o nazwie **template.json** do projektu.

2. Aby dodać standardowy centrum IoT hub do regionu **wschodnich stanów USA,** zastąp zawartość **pliku template.json** następującą definicją zasobu. Aby uzyskać bieżącą listę regionów obsługujących centrum IoT Hub, zobacz [Stan platformy Azure:][lnk-status]

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

3. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt, kliknij polecenie **Dodaj**, a następnie kliknij polecenie **Nowy element**. Dodaj plik JSON o nazwie **parameters.json** do projektu.

4. Zastąp zawartość **pliku parameters.json** następującymi informacjami o parametrach, które ustawiają nazwę nowego centrum IoT, takiego jak **{your initials}mynewiothub**. Nazwa centrum IoT musi być unikatowa globalnie, więc powinna zawierać twoje imię i nazwisko lub inicjały:

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

5. W **Eksploratorze serwera**połącz się z subskrypcją platformy Azure, a na koncie usługi Azure Storage utwórz kontener o nazwie **szablony**. W panelu **Właściwości** ustaw uprawnienia **dostępu do odczytu publicznego** dla kontenera **szablonów** na **Obiekt Blob**.

6. W **Eksploratorze serwera**kliknij prawym przyciskiem myszy kontener **szablonów,** a następnie kliknij polecenie **Wyświetl kontener obiektów blob**. Kliknij przycisk **Przekaż obiekt blob,** wybierz dwa pliki, **parameters.json** i **templates.json**, a następnie kliknij przycisk **Otwórz,** aby przekazać pliki JSON do kontenera **szablonów.** Adresy URL obiektów blob zawierających dane JSON są następujące:

    ```csharp
    https://{Your storage account name}.blob.core.windows.net/templates/parameters.json
    https://{Your storage account name}.blob.core.windows.net/templates/template.json
    ```
7. Dodaj następującą metodę do Program.cs:

    ```csharp
    static void CreateIoTHub(ResourceManagementClient client)
    {

    }
    ```

8. Dodaj następujący kod do metody **CreateIoTHub,** aby przesłać pliki szablonów i parametrów do usługi Azure Resource Manager:

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

9. Dodaj następujący kod do metody **CreateIoTHub,** która wyświetla stan i klucze dla nowego centrum IoT Hub:

    ```csharp
    string state = createResponse.Properties.ProvisioningState;
    Console.WriteLine("Deployment state: {0}", state);

    if (state != "Succeeded")
    {
      Console.WriteLine("Failed to create iothub");
    }
    Console.WriteLine(createResponse.Properties.Outputs);
    ```

## <a name="complete-and-run-the-application"></a>Uzupełnij i uruchom aplikację

Teraz można ukończyć aplikację, wywołując **CreateIoTHub** metody przed utworzeniem i uruchomieniem go.

1. Dodaj następujący kod na końcu **Main** metody:

    ```csharp
    CreateIoTHub(client);
    Console.ReadLine();
    ```

2. Kliknij **pozycję Kompilacja,** a następnie **pozycję Zbuduj rozwiązanie**. Popraw wszelkie błędy.

3. Kliknij **przycisk Debugowanie,** a następnie **uruchom debugowanie,** aby uruchomić aplikację. Uruchomienie wdrożenia może potrwać kilka minut.

4. Aby zweryfikować, że aplikacja dodała nowe centrum IoT, odwiedź [witrynę Azure portal][lnk-azure-portal] i wyświetl listę zasobów. Alternatywnie należy użyć polecenia cmdlet **programu Get-AzResource** programu PowerShell.

> [!NOTE]
> W tym przykładzie aplikacja dodaje S1 Standard IoT Hub, dla którego są rozliczane. Centrum IoT można usunąć za pośrednictwem [witryny Azure portal][lnk-azure-portal] lub przy użyciu polecenia cmdlet **programu PowerShell remove-AzResource** po zakończeniu.

## <a name="next-steps"></a>Następne kroki
Teraz wdrożono centrum IoT przy użyciu szablonu Usługi Azure Resource Manager z programem Języka C#, możesz chcieć zbadać dalej:

* Przeczytaj o możliwościach [interfejsu API REST dostawcy zasobów usługi IoT Hub][lnk-rest-api].
* Przeczytaj [omówienie usługi Azure Resource Manager,][lnk-azure-rm-overview] aby dowiedzieć się więcej o możliwościach usługi Azure Resource Manager.
* Aby uzyskać składnię JSON i właściwości używane w szablonach, zobacz [Typy zasobów Microsoft.Devices](/azure/templates/microsoft.devices/iothub-allversions).

Aby dowiedzieć się więcej na temat tworzenia usługi IoT Hub, zobacz następujące artykuły:

* [Wprowadzenie do C SDK][lnk-c-sdk]
* [Zestawy SDK usługi Azure IoT][lnk-sdks]

Aby dokładniej zbadać możliwości usługi IoT Hub, zobacz:

* [Wdrażanie rozwiązań SI na urządzeniach brzegowych przy użyciu usługi Azure IoT Edge][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: /powershell/azure/install-Az-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/management/overview.md
[lnk-storage-account]:../storage/common/storage-create-storage-account.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
