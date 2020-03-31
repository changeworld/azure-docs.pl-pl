---
title: Tworzenie centrum Usługi Azure IoT przy użyciu interfejsu API REST dostawcy zasobów | Dokumenty firmy Microsoft
description: Dowiedz się, jak programowo używać interfejsu API REST dostawcy zasobów języka C# do tworzenia centrum IoT hub i zarządzania nim.
author: robinsh
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/08/2017
ms.openlocfilehash: c4cb230c9f0b56e3ff9d81e0d85134a7f192e6e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75429175"
---
# <a name="create-an-iot-hub-using-the-resource-provider-rest-api-net"></a>Tworzenie centrum IoT przy użyciu interfejsu API REST dostawcy zasobów (.NET)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Za pomocą [interfejsu API REST dostawcy zasobów usługi IoT Hub](https://docs.microsoft.com/rest/api/iothub/iothubresource) można tworzyć centra Usługi Azure IoT i zarządzać nimi programowo. W tym samouczku pokazano, jak używać interfejsu API REST dostawcy zasobów usługi IoT Hub do tworzenia centrum IoT hub z programu Języka C#.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Program Visual Studio.

* Aktywne konto platformy Azure. Jeśli go nie masz, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut.

* [Program Azure PowerShell 1.0](https://docs.microsoft.com/powershell/azure/install-Az-ps) lub nowsza.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Przygotowywanie projektu programu Visual Studio

1. W programie Visual Studio utwórz projekt klasycznego pulpitu systemu Windows w języku Visual C# przy użyciu szablonu projektu **aplikacji konsoli (NET Framework).** Nazwij projekt **CreateIoTHubREST**.

2. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt, a następnie kliknij polecenie **Zarządzaj pakietami NuGet**.

3. W Menedżerze pakietów NuGet zaznacz pole **wyboru Uwzględnij wydanie wstępne**i na stronie Przeglądaj wyszukiwanie w **witrynie** **Microsoft.Azure.Management.ResourceManager**. Wybierz pakiet, kliknij pozycję **Zainstaluj**, w **obszarze Przejrzyj zmiany** kliknij przycisk **OK**, a następnie kliknij pozycję **Akceptuję,** aby zaakceptować licencje.

4. W Menedżerze pakietów NuGet wyszukaj hasło **Microsoft.IdentityModel.Clients.ActiveDirectory**.  Kliknij **pozycję Zainstaluj**, w **ciemiękaj zmiany** kliknij przycisk **OK**, a następnie kliknij pozycję **Akceptuję,** aby zaakceptować licencję.

5. W Program.cs zastąp istniejące **instrukcje using** następującym kodem:

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

6. W Program.cs dodaj następujące zmienne statyczne zastępujące wartości zastępcze. Zanotowano **applicationid**, **SubscriptionId**, **TenantId**i **Hasło** wcześniej w tym samouczku. **Nazwa grupy zasobów** to nazwa grupy zasobów używanej podczas tworzenia centrum IoT Hub. Można użyć istniejącej lub nowej grupy zasobów. **Nazwa Centrum IoT** to nazwa utworzonego centrum IoT Hub, takiego jak **MyIoTHub**. Nazwa centrum IoT musi być unikatowa globalnie. **Nazwa wdrożenia** to nazwa wdrożenia, na przykład **Deployment_01**.

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

## <a name="use-the-resource-provider-rest-api-to-create-an-iot-hub"></a>Tworzenie centrum IoT za pomocą interfejsu API REST dostawcy zasobów

Użyj [interfejsu API REST dostawcy zasobów usługi IoT Hub,](https://docs.microsoft.com/rest/api/iothub/iothubresource) aby utworzyć centrum IoT w grupie zasobów. Można również użyć interfejsu API REST dostawcy zasobów, aby wprowadzić zmiany w istniejącym centrum IoT Hub.

1. Dodaj następującą metodę do Program.cs:

    ```csharp
    static void CreateIoTHub(string token)
    {

    }
    ```

2. Dodaj następujący kod do **metody CreateIoTHub.** Ten kod tworzy **obiekt HttpClient** z tokenem uwierzytelniania w nagłówkach:

    ```csharp
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    ```

3. Dodaj następujący kod do **metody CreateIoTHub.** Ten kod opisuje Centrum IoT do tworzenia i generuje reprezentację JSON. Aby uzyskać bieżącą listę lokalizacji obsługujących usługę IoT Hub, zobacz [Stan platformy Azure:](https://azure.microsoft.com/status/)

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

4. Dodaj następujący kod do **metody CreateIoTHub.** Ten kod przesyła żądanie REST na platformie Azure. Następnie kod sprawdza odpowiedź i pobiera adres URL, którego można użyć do monitorowania stanu zadania wdrażania:

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

5. Dodaj następujący kod na końcu **CreateIoTHub** metody. Ten kod używa adresu **asyncStatusUri** pobranego w poprzednim kroku, aby poczekać na zakończenie wdrożenia:

    ```csharp
    string body;
    do
    {
      Thread.Sleep(10000);
      HttpResponseMessage deploymentstatus = client.GetAsync(asyncStatusUri).Result;
      body = deploymentstatus.Content.ReadAsStringAsync().Result;
    } while (body == "{\"status\":\"Running\"}");
    ```

6. Dodaj następujący kod na końcu **CreateIoTHub** metody. Ten kod pobiera klucze utworzonego centrum IoT hub i drukuje je na konsoli:

    ```csharp
    var listKeysUri = string.Format("https://management.azure.com/subscriptions/{0}/resourceGroups/{1}/providers/Microsoft.Devices/IotHubs/{2}/IoTHubKeys/listkeys?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var keysresults = client.PostAsync(listKeysUri, null).Result;

    Console.WriteLine("Keys: {0}", keysresults.Content.ReadAsStringAsync().Result);
    ```

## <a name="complete-and-run-the-application"></a>Uzupełnij i uruchom aplikację

Teraz można ukończyć aplikację, wywołując **CreateIoTHub** metody przed utworzeniem i uruchomieniem go.

1. Dodaj następujący kod na końcu **Main** metody:

    ```csharp
    CreateIoTHub(token.AccessToken);
    Console.ReadLine();
    ```

2. Kliknij **pozycję Kompilacja,** a następnie **pozycję Zbuduj rozwiązanie**. Popraw wszelkie błędy.

3. Kliknij **przycisk Debugowanie,** a następnie **uruchom debugowanie,** aby uruchomić aplikację. Uruchomienie wdrożenia może potrwać kilka minut.

4. Aby sprawdzić, czy aplikacja dodała nowe centrum IoT, odwiedź [witrynę Azure portal](https://portal.azure.com/) i wyświetl listę zasobów. Alternatywnie należy użyć polecenia cmdlet **programu Get-AzResource** programu PowerShell.

> [!NOTE]
> W tym przykładzie aplikacja dodaje S1 Standard IoT Hub, dla którego są rozliczane. Po zakończeniu można usunąć Centrum IoT za pośrednictwem [witryny Azure portal](https://portal.azure.com/) lub przy użyciu polecenia cmdlet **Programu PowerShell Remove-AzResource** po zakończeniu.

## <a name="next-steps"></a>Następne kroki

Teraz wdrożono centrum IoT przy użyciu interfejsu API REST dostawcy zasobów, warto zbadać dalej:

* Przeczytaj o możliwościach [interfejsu API REST dostawcy zasobów usługi IoT Hub](https://docs.microsoft.com/rest/api/iothub/iothubresource).

* Przeczytaj [omówienie usługi Azure Resource Manager,](../azure-resource-manager/management/overview.md) aby dowiedzieć się więcej o możliwościach usługi Azure Resource Manager.

Aby dowiedzieć się więcej na temat tworzenia usługi IoT Hub, zobacz następujące artykuły:

* [Wprowadzenie do C SDK](iot-hub-device-sdk-c-intro.md)

* [Zestawy SDK usługi Azure IoT](iot-hub-devguide-sdks.md)

Aby dokładniej zbadać możliwości usługi IoT Hub, zobacz:

* [Wdrażanie rozwiązań SI na urządzeniach brzegowych przy użyciu usługi Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)