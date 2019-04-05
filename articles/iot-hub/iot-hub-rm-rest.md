---
title: Tworzenie usługi Azure IoT hub przy użyciu interfejsu API REST dostawcy zasobów | Dokumentacja firmy Microsoft
description: Jak używać interfejsu API REST dostawcy zasobów w celu utworzenia Centrum IoT Hub.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/08/2017
ms.openlocfilehash: b24c8fa76e5bb3f8cdf7d246f7f7aa0f1a62258a
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2019
ms.locfileid: "59051516"
---
# <a name="create-an-iot-hub-using-the-resource-provider-rest-api-net"></a>Tworzenie Centrum IoT przy użyciu dostawcy zasobów interfejsu API REST (.NET)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Możesz użyć [interfejs API REST dostawcy zasobów usługi IoT Hub] [ lnk-rest-api] do tworzenia i zarządzania usługi Azure IoT hubs programowo. W tym samouczku dowiesz się, jak tworzenie Centrum IoT z poziomu programu C# za pomocą interfejsu API REST dostawcy zasobów usługi IoT Hub.

> [!NOTE]
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi:  [Usługa Azure Resource Manager i Model Klasyczny](../azure-resource-manager/resource-manager-deployment-model.md).  W tym artykule opisano, przy użyciu modelu wdrażania usługi Azure Resource Manager.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Program Visual Studio 2015 lub Visual Studio 2017.
* Aktywne konto platformy Azure. <br/>Jeśli go nie masz, możesz utworzyć [bezpłatne konto][lnk-free-trial] w zaledwie kilka minut.
* [Azure PowerShell 1.0] [ lnk-powershell-install] lub nowszej.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Przygotowanie projektu programu Visual Studio

1. W programie Visual Studio Utwórz projekt Visual C# Windows Classic Desktop za pomocą **Aplikacja konsoli (.NET Framework)** szablonu projektu. Nadaj projektowi nazwę **CreateIoTHubREST**.

2. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy nad projektem, a następnie kliknij przycisk **Zarządzaj pakietami NuGet**.

3. Sprawdź w Menedżerze pakietów NuGet **Uwzględnij wersję wstępną**, a następnie na **Przeglądaj** strony wyszukiwanie **Microsoft.Azure.Management.ResourceManager**. Wybierz pakiet, kliknij przycisk **zainstalować**w **przeglądanie zmian** kliknij **OK**, następnie kliknij przycisk **akceptuję** zaakceptowania licencji.

4. W Menedżerze pakietów NuGet, wyszukaj **Microsoft.IdentityModel.Clients.ActiveDirectory**.  Kliknij przycisk **zainstalować**w **przeglądanie zmian** kliknij **OK**, następnie kliknij przycisk **akceptuję** Aby zaakceptować licencję.

5. W pliku Program.cs Zastąp istniejące **przy użyciu** instrukcji z następującym kodem:

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

6. W pliku Program.cs Dodaj następujące zmienne statyczne, zastępując wartość symbolu zastępczego. Należy zanotować **ApplicationId**, **SubscriptionId**, **TenantId**, i **hasło** wcześniej w tym samouczku. **Nazwa grupy zasobów** to nazwa grupy zasobów, możesz użyć podczas tworzenia Centrum IoT. Można użyć istniejącej lub nowej grupy zasobów. **Nazwa centrum IoT Hub** jest nazwą Centrum IoT, które tworzysz, takich jak **MyIoTHub**. Nazwa centrum IoT Hub musi być globalnie unikatowa. **Nazwa wdrożenia** jest nazwa wdrożenia, takich jak **Deployment_01**.

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

## <a name="use-the-resource-provider-rest-api-to-create-an-iot-hub"></a>Interfejs API REST dostawcy zasobów umożliwia tworzenie Centrum IoT

Użyj [interfejs API REST dostawcy zasobów usługi IoT Hub] [ lnk-rest-api] do utworzenia Centrum IoT w grupie zasobów. Interfejs API REST dostawcy zasobów można również użyć do wprowadzania zmian w istniejącym Centrum IoT.

1. Dodaj następującą metodę do pliku Program.cs:

    ```csharp
    static void CreateIoTHub(string token)
    {

    }
    ```

2. Dodaj następujący kod do **CreateIoTHub** metody. Ten kod tworzy **HttpClient** obiektu przy użyciu tokenu uwierzytelniania w nagłówkach:

    ```csharp
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    ```

3. Dodaj następujący kod do **CreateIoTHub** metody. Ten kod w tym artykule opisano tworzenie Centrum IoT i generuje reprezentacji JSON. Dla bieżącej listy lokalizacje, które obsługują usługi IoT Hub, zobacz [stanu platformy Azure][lnk-status]:

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

4. Dodaj następujący kod do **CreateIoTHub** metody. Ten kod przesyła żądania REST na platformie Azure. Kod następnie sprawdza odpowiedź i pobiera adres URL, można użyć, aby monitorować stan zadania wdrażania:

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

5. Dodaj następujący kod na końcu **CreateIoTHub** metody. Ten kod używa **asyncStatusUri** pobrać adresu w poprzednim kroku, aby czekać na zakończenie wdrożenia:

    ```csharp
    string body;
    do
    {
      Thread.Sleep(10000);
      HttpResponseMessage deploymentstatus = client.GetAsync(asyncStatusUri).Result;
      body = deploymentstatus.Content.ReadAsStringAsync().Result;
    } while (body == "{\"status\":\"Running\"}");
    ```

6. Dodaj następujący kod na końcu **CreateIoTHub** metody. Ten kod pobiera klucze został utworzony i wyświetla je w konsoli Centrum IoT:

    ```csharp
    var listKeysUri = string.Format("https://management.azure.com/subscriptions/{0}/resourceGroups/{1}/providers/Microsoft.Devices/IotHubs/{2}/IoTHubKeys/listkeys?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var keysresults = client.PostAsync(listKeysUri, null).Result;

    Console.WriteLine("Keys: {0}", keysresults.Content.ReadAsStringAsync().Result);
    ```

## <a name="complete-and-run-the-application"></a>Ukończenie, a następnie uruchom aplikację

Możesz teraz ukończyć aplikacji, wywołując **CreateIoTHub** metoda przed Kompiluj i uruchom go.

1. Dodaj następujący kod na końcu **Main** metody:

    ```csharp
    CreateIoTHub(token.AccessToken);
    Console.ReadLine();
    ```

2. Kliknij przycisk **kompilacji** i następnie **Kompiluj rozwiązanie**. Usuń wszelkie błędy.

3. Kliknij przycisk **debugowania** i następnie **Rozpocznij debugowanie** do uruchomienia aplikacji. Może upłynąć kilka minut, zanim uruchamiania wdrożenia.

4. Aby sprawdzić, czy aplikacja dodane nowe Centrum IoT hub, odwiedź stronę [witryny Azure portal] [ lnk-azure-portal] i wyświetlanie listy zasobów. Można również użyć **Get AzResource** polecenia cmdlet programu PowerShell.

> [!NOTE]
> Ta przykładowa aplikacja dodaje S1 standardowa usługi IoT Hub dla którego stosowana jest stawka. Gdy skończysz, możesz usunąć Centrum IoT hub za pomocą [witryny Azure portal] [ lnk-azure-portal] lub za pomocą **AzResource Usuń** polecenia cmdlet programu PowerShell po zakończeniu.

## <a name="next-steps"></a>Kolejne kroki
Po wdrożeniu usługi IoT hub przy użyciu interfejsu API REST dostawcy zasobów, warto dokładniej:

* Przeczytaj o możliwości [interfejs API REST dostawcy zasobów usługi IoT Hub][lnk-rest-api].
* Odczyt [Omówienie usługi Azure Resource Manager] [ lnk-azure-rm-overview] Aby dowiedzieć się więcej o możliwościach usługi Azure Resource Manager.

Aby dowiedzieć się więcej na temat tworzenia usługi IoT Hub, zobacz następujące artykuły:

* [Wprowadzenie do zestawu SDK języka C][lnk-c-sdk]
* [Zestawy SDK Azure IoT][lnk-sdks]

Aby bliżej zapoznać się z możliwościami usługi IoT Hub, zobacz:

* [Wdrażanie rozwiązań SI na urządzeniach brzegowych przy użyciu usługi Azure IoT Edge][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/install-Az-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
