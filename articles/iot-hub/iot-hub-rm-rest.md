---
title: Tworzenie Centrum Azure IoT przy użyciu dostawcy zasobów interfejsu API REST | Dokumentacja firmy Microsoft
description: Jak użyć interfejsu API REST dostawcy zasobów w celu tworzenia Centrum IoT.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 06f05da839ebca2ae53e255acce7f07d1989673c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34635223"
---
# <a name="create-an-iot-hub-using-the-resource-provider-rest-api-net"></a>Tworzenie Centrum IoT przy użyciu dostawcy zasobów interfejsu API REST (.NET)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Można użyć [interfejsu API REST dostawcy zasobów Centrum IoT] [ lnk-rest-api] tworzenie i zarządzanie nimi centra Azure IoT programowo. Ten samouczek pokazuje, jak używać dostawcy zasobów Centrum IoT interfejsu API REST do tworzenia Centrum IoT z programu C#.

> [!NOTE]
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [usługi Azure Resource Manager i Model Klasyczny](../azure-resource-manager/resource-manager-deployment-model.md).  W tym artykule omówiono przy użyciu modelu wdrażania usługi Azure Resource Manager.

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Program Visual Studio 2015 lub Visual Studio 2017.
* Aktywne konto platformy Azure. <br/>Jeśli go nie masz, możesz utworzyć [bezpłatne konto][lnk-free-trial] w zaledwie kilka minut.
* [Azure PowerShell 1.0] [ lnk-powershell-install] lub nowszym.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Przygotowanie projektu programu Visual Studio

1. W programie Visual Studio, utworzyć projekt Visual C# pulpitu systemu Windows klasycznego za pomocą **aplikacji konsoli (.NET Framework)** szablonu projektu. Nazwij projekt **CreateIoTHubREST**.

2. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt, a następnie kliknij przycisk **Zarządzaj pakietami NuGet**.

3. Sprawdź w Menedżerze pakietów NuGet **Uwzględnij wersję wstępną**i na **Przeglądaj** wyszukiwanie strony **Microsoft.Azure.Management.ResourceManager**. Wybierz pakiet, kliknij przycisk **zainstalować**w **Przejrzyj zmiany** kliknij **OK**, następnie kliknij przycisk **akceptuję** do akceptowania licencji.

4. Wyszukaj w Menedżerze pakietów NuGet **Microsoft.IdentityModel.Clients.ActiveDirectory**.  Kliknij przycisk **zainstalować**w **Przejrzyj zmiany** kliknij **OK**, następnie kliknij przycisk **akceptuję** do akceptowania licencji.

5. W pliku Program.cs Zastąp istniejące **przy użyciu** instrukcje następującym kodem:

    ```csharp
    using System;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json;
    using Microsoft.Rest;
    using System.Linq;
    using System.Threading;
    ```

6. W pliku Program.cs Dodaj następujące zmienne statyczne, zastępując symbole zastępcze. Należy zanotować **ApplicationId**, **SubscriptionId**, **TenantId**, i **hasło** we wcześniejszej części tego samouczka. **Nazwa grupy zasobów** to nazwa grupy zasobów, używane podczas tworzenia Centrum IoT. Możesz użyć wstępnie istniejącą lub nową grupę zasobów. **Nazwa centrum IoT** jest nazwą Centrum IoT można utworzyć, takich jak **MyIoTHub**. Nazwa centrum IoT musi być globalnie unikatowa. **Nazwa wdrożenia** jest nazwę wdrożenia, takich jak **Deployment_01**.

    ```csharp
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";

    static string rgName = "{Resource group name}";
    static string iotHubName = "{IoT Hub name including your initials}";
    ```
[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

[!INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="use-the-resource-provider-rest-api-to-create-an-iot-hub"></a>Użyj dostawcy zasobów interfejsu API REST do tworzenia Centrum IoT

Użyj [interfejsu API REST dostawcy zasobów Centrum IoT] [ lnk-rest-api] do tworzenia Centrum IoT w grupie zasobów. Aby wprowadzić zmiany w istniejących Centrum IoT umożliwia także interfejsu API REST dostawcy zasobów.

1. Dodaj następującą metodę do pliku Program.cs:

    ```csharp
    static void CreateIoTHub(string token)
    {

    }
    ```

2. Dodaj następujący kod do **CreateIoTHub** metody. Ten kod tworzy **HttpClient** obiektu o token uwierzytelniania w nagłówkach:

    ```csharp
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    ```

3. Dodaj następujący kod do **CreateIoTHub** metody. Ten kod zawiera opis Centrum IoT, aby utworzyć i generuje reprezentacja JSON. Dla bieżącej listy lokalizacje, które obsługują Centrum IoT [stan Azure][lnk-status]:

    ```csharp
    var description = new
    {
      name = iotHubName,
      location = "East US",
      sku = new
      {
        name = "S1",
        tier = "Standard",
        capacity = 1
      }
    };

    var json = JsonConvert.SerializeObject(description, Formatting.Indented);
    ```

4. Dodaj następujący kod do **CreateIoTHub** metody. Ten kod przesyła żądanie REST na platformie Azure. Następnie kod sprawdza odpowiedź i pobiera adres URL służy do monitorowania stanu zadania wdrażania:

    ```csharp
    var content = new StringContent(JsonConvert.SerializeObject(description), Encoding.UTF8, "application/json");
    var requestUri = string.Format("https://management.azure.com/subscriptions/{0}/resourcegroups/{1}/providers/Microsoft.devices/IotHubs/{2}?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var result = client.PutAsync(requestUri, content).Result;

    if (!result.IsSuccessStatusCode)
    {
      Console.WriteLine("Failed {0}", result.Content.ReadAsStringAsync().Result);
      return;
    }

    var asyncStatusUri = result.Headers.GetValues("Azure-AsyncOperation").First();
    ```

5. Dodaj następujący kod na końcu **CreateIoTHub** metody. Ten kod zawiera **asyncStatusUri** pobrać adresu w poprzednim kroku oczekiwania na ukończenie wdrożenia:

    ```csharp
    string body;
    do
    {
      Thread.Sleep(10000);
      HttpResponseMessage deploymentstatus = client.GetAsync(asyncStatusUri).Result;
      body = deploymentstatus.Content.ReadAsStringAsync().Result;
    } while (body == "{\"status\":\"Running\"}");
    ```

6. Dodaj następujący kod na końcu **CreateIoTHub** metody. Ten kod pobiera klucze Centrum IoT utworzony i wyświetla je do konsoli:

    ```csharp
    var listKeysUri = string.Format("https://management.azure.com/subscriptions/{0}/resourceGroups/{1}/providers/Microsoft.Devices/IotHubs/{2}/IoTHubKeys/listkeys?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var keysresults = client.PostAsync(listKeysUri, null).Result;

    Console.WriteLine("Keys: {0}", keysresults.Content.ReadAsStringAsync().Result);
    ```

## <a name="complete-and-run-the-application"></a>Zakończenie i uruchomić aplikację

Możesz teraz ukończyć aplikacji przez wywołanie metody **CreateIoTHub** metody, aby skompilować i uruchomić go.

1. Dodaj następujący kod na końcu **Main** metody:

    ```csharp
    CreateIoTHub(token.AccessToken);
    Console.ReadLine();
    ```

2. Kliknij przycisk **kompilacji** , a następnie **zbudować rozwiązanie**. Popraw błędy.

3. Kliknij przycisk **debugowania** , a następnie **Rozpocznij debugowanie** do uruchomienia aplikacji. Może upłynąć kilka minut dla uruchamiania wdrożenia.

4. Aby sprawdzić, czy aplikacja dodany nowego centrum IoT, odwiedź stronę [portalu Azure] [ lnk-azure-portal] i wyświetlanie listy zasobów. Można również użyć **Get-AzureRmResource** polecenia cmdlet programu PowerShell.

> [!NOTE]
> Ta przykładowa aplikacja dodaje Centrum IoT standardowe S1 dla której są rozliczane. Gdy skończysz, można usunąć Centrum IoT za pośrednictwem [portalu Azure] [ lnk-azure-portal] lub za pomocą **AzureRmResource Usuń** polecenia cmdlet programu PowerShell po zakończeniu.

## <a name="next-steps"></a>Kolejne kroki
Po wdrożeniu przy użyciu interfejsu API REST dostawcy zasobów Centrum IoT, może zajść potrzeba dalszego zbadania:

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

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
