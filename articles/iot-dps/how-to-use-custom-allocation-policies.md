---
title: Niestandardowe zasady alokacji za pomocą usługi inicjowania obsługi administracyjnej urządzeń usługi Azure IoT Hub
description: Jak używać niestandardowych zasad alokacji za pomocą usługi inicjowania obsługi administracyjnej urządzeń usługi Azure IoT Hub (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 11/14/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 87ffca1957d4ec449753f1966ed05cf3948f5ca2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75453941"
---
# <a name="how-to-use-custom-allocation-policies"></a>Jak korzystać z niestandardowych zasad alokacji

Niestandardowe zasady alokacji zapewnia większą kontrolę nad tym, jak urządzenia są przypisane do centrum IoT hub. Jest to realizowane przy użyciu kodu niestandardowego w [funkcji platformy Azure,](../azure-functions/functions-overview.md) aby przypisać urządzenia do centrum IoT hub. Usługa inicjowania obsługi administracyjnej urządzeń wywołuje kod funkcji platformy Azure, zapewniając wszystkie istotne informacje o urządzeniu i rejestracji. Kod funkcji jest wykonywany i zwraca informacje o koncentratorze IoT używane do inicjowania obsługi administracyjnej urządzenia.

Korzystając z niestandardowych zasad alokacji, można zdefiniować własne zasady alokacji, gdy zasady udostępniane przez usługę inicjowania obsługi administracyjnej urządzeń nie spełniają wymagań scenariusza.

Na przykład może chcesz sprawdzić certyfikat, którego urządzenie używa podczas inicjowania obsługi administracyjnej i przypisać urządzenie do centrum IoT na podstawie właściwości certyfikatu. Lub może masz informacje przechowywane w bazie danych dla urządzeń i trzeba zbadać bazy danych, aby określić, które centrum IoT urządzenie powinno być przypisane do.

W tym artykule przedstawiono niestandardowe zasady alokacji przy użyciu funkcji platformy Azure napisane w języku C#. Two nowych centrów IoT są tworzone reprezentujące *Contoso Toasters Division* i *Contoso Heat Pumps Division*. Urządzenia żądające inicjowania obsługi administracyjnej muszą mieć identyfikator rejestracji z jednym z następujących sufiksów, które mają być akceptowane w celu inicjowania obsługi administracyjnej:

* **-contoso-tstrsd-007**: Dział Tosterów Contoso
* **-contoso-hpsd-088**: Dział pomp ciepła Contoso

Urządzenia zostaną aprowione na podstawie jednego z tych wymaganych przyrostków na identyfikatorze rejestracji. Te urządzenia będą symulowane przy użyciu przykładu inicjowania obsługi administracyjnej zawartego w [zestawie SDK C usługi Azure IoT.](https://github.com/Azure/azure-iot-sdk-c)

W tym artykule wykonaj następujące kroki:

* Użyj interfejsu wiersza polecenia platformy Azure, aby utworzyć dwa centra IoT podziału Contoso **(Dział Tosterów Contoso** i **Dział pomp ciepła Contoso)**
* Tworzenie nowej rejestracji grupy przy użyciu funkcji platformy Azure dla niestandardowych zasad alokacji
* Tworzenie kluczy urządzenia dla dwóch symulacji urządzeń.
* Konfigurowanie środowiska programistycznego dla zestawu SDK usługi Azure IoT C
* Symuluj urządzenia i sprawdź, czy są aprowiowane zgodnie z przykładowym kodem w zasadach alokacji niestandardowej

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Następujące wymagania wstępne są dla środowiska deweloperskiego systemu Windows. W przypadku systemu Linux lub macOS zobacz odpowiednią sekcję w [przygotowaniu środowiska programistycznego](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) w dokumentacji SDK.

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 z włączonym obciążeniem ["Tworzenie pulpitu z c++".](https://docs.microsoft.com/cpp/?view=vs-2019#pivot=workloads) Obsługiwane są również program visual studio 2015 i visual studio 2017.

* Zainstalowana najnowsza wersja usługi[Git](https://git-scm.com/download/).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-provisioning-service-and-two-divisional-iot-hubs"></a>Tworzenie usługi inicjowania obsługi administracyjnej i dwóch wydzielonych centrów IoT

W tej sekcji służy usługę Azure Cloud Shell do utworzenia usługi inicjowania obsługi administracyjnej i dwóch centrów IoT reprezentujących **dział Tosterów Contoso** i **dział Pompy ciepła Contoso.**

> [!TIP]
> Polecenia użyte w tym artykule tworzą usługę inicjowania obsługi administracyjnej i inne zasoby w lokalizacji zachodnie stany USA. Zaleca się utworzenie zasobów w regionie najbliższym, który obsługuje usługę inicjowania obsługi administracyjnej urządzeń. Można wyświetlić listę dostępnych lokalizacji, uruchamiając polecenie `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` lub przechodząc do strony [Stan platformy Azure](https://azure.microsoft.com/status/) i wyszukując usługę „Device Provisioning”. W poleceniach lokalizacje można określić w formacie jednego wyrazu lub wielu wyrazów; na przykład: westus, zachodnie stany USA, zachodnie stany USA itp. W wartości nie jest rozróżniana wielkość liter. Jeśli do określenia lokalizacji używasz formatu wielu słów, ujmij wartość w cudzysłowy, na przykład `-- location "West US"`.
>

1. Użyj usługi Azure Cloud Shell, aby utworzyć grupę zasobów za pomocą polecenia [tworzenia grupy az.](/cli/azure/group#az-group-create) Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

    Poniższy przykład tworzy grupę zasobów o nazwie *contoso-us-resource-group* w regionie *westus.* Zaleca się użycie tej grupy dla wszystkich zasobów utworzonych w tym artykule. Takie podejście ułatwi oczyszczanie po zakończeniu.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location westus
    ```

2. Użyj usługi Azure Cloud Shell, aby utworzyć usługę inicjowania obsługi administracyjnej urządzeń za pomocą polecenia [az iot dps create.](/cli/azure/iot/dps#az-iot-dps-create) Usługa inicjowania obsługi administracyjnej zostanie dodana do *contoso-us-resource-group*.

    Poniższy przykład tworzy usługę inicjowania obsługi administracyjnej o nazwie *contoso-inicjowania obsługi administracyjnej usługi-1098* w lokalizacji *westus.* Należy użyć unikatowej nazwy usługi. Scholij swój własny przyrostek w nazwie usługi zamiast **1098**.

    ```azurecli-interactive 
    az iot dps create --name contoso-provisioning-service-1098 --resource-group contoso-us-resource-group --location westus
    ```

    To polecenie może potrwać kilka minut.

3. Użyj usługi Azure Cloud Shell, aby utworzyć centrum IoT **działu toasterów Contoso** za pomocą polecenia [az iot hub create.](/cli/azure/iot/hub#az-iot-hub-create) Centrum IoT zostanie dodane do *contoso-us-resource-group*.

    Poniższy przykład tworzy centrum IoT o nazwie *contoso-toasters-hub-1098* w lokalizacji *westus.* Należy użyć unikatowej nazwy koncentratora. Schoruj swój własny przyrostek w nazwie koncentratora zamiast **1098**. Przykładowy kod dla niestandardowych `-toasters-` zasad alokacji wymaga w nazwie centrum.

    ```azurecli-interactive 
    az iot hub create --name contoso-toasters-hub-1098 --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    To polecenie może potrwać kilka minut.

4. Użyj usługi Azure Cloud Shell, aby utworzyć centrum IoT **działu pomp ciepła Contoso** za pomocą polecenia [az iot hub create.](/cli/azure/iot/hub#az-iot-hub-create) To centrum IoT zostanie również dodane do *contoso-us-resource-group*.

    Poniższy przykład tworzy centrum IoT o nazwie *contoso-heatpumps-hub-1098* w lokalizacji *westus.* Należy użyć unikatowej nazwy koncentratora. Schoruj swój własny przyrostek w nazwie koncentratora zamiast **1098**. Przykładowy kod dla niestandardowych `-heatpumps-` zasad alokacji wymaga w nazwie centrum.

    ```azurecli-interactive 
    az iot hub create --name contoso-heatpumps-hub-1098 --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    To polecenie może potrwać kilka minut.

## <a name="create-the-custom-allocation-function"></a>Tworzenie funkcji alokacji niestandardowej

W tej sekcji utworzysz funkcję platformy Azure, która implementuje zasady alokacji niestandardowej. Ta funkcja decyduje, który oddział IoT hub urządzenie powinno być zarejestrowane na podstawie tego, czy jego identyfikator rejestracji zawiera ciąg **-contoso-tstrsd-007** lub **-contoso-hpsd-088**. Ustawia również stan początkowy bliźniaczej części urządzenia w zależności od tego, czy urządzenie jest tosterem, czy pompą ciepła.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Na stronie głównej wybierz pozycję **+ Utwórz zasób**.

2. W polu wyszukiwania *Wyszukaj w Marketplace* wpisz "Aplikacja funkcyjna". Z listy rozwijanej wybierz **pozycję Aplikacja funkcji**, a następnie wybierz pozycję **Utwórz**.

3. Na stronie Tworzenie **aplikacji funkcji** na karcie **Podstawy** wprowadź następujące ustawienia dla nowej aplikacji funkcji i wybierz **pozycję Przejrzyj + utwórz:**

    **Grupa zasobów:** Wybierz **contoso-us-resource-group,** aby zachować wszystkie zasoby utworzone w tym artykule razem.

    **Nazwa aplikacji funkcji**: Wprowadź unikatową nazwę aplikacji funkcji. W tym przykładzie **użyto contoso-function-app-1098**.

    **Publikuj**: Sprawdź, czy **kod** jest zaznaczony.

    **Stos środowiska uruchomieniowego:** Wybierz **.NET Core** z listy rozwijanej.

    **Region:** Wybierz ten sam region co grupa zasobów. W tym przykładzie użyto **zachodnie stany USA**.

    > [!NOTE]
    > Domyślnie usługa Application Insights jest włączona. Usługa Application Insights nie jest konieczne dla tego artykułu, ale może pomóc zrozumieć i zbadać wszelkie problemy napotkane z alokacji niestandardowej. Jeśli wolisz, możesz wyłączyć usługę Application Insights, wybierając kartę **Monitorowanie,** a następnie wybierając **pozycję Nie** dla włącz usługę **Application Insights**.

    ![Tworzenie aplikacji funkcji platformy Azure w celu obsługi niestandardowej funkcji alokacji](./media/how-to-use-custom-allocation-policies/create-function-app.png)

4. Na stronie **Podsumowanie** wybierz pozycję **Utwórz,** aby utworzyć aplikację funkcyjnej. Wdrożenie może potrwać kilka minut. Po jego zakończeniu wybierz pozycję **Przejdź do zasobu**.

5. W lewym okienku strony **Przegląd** aplikacji **+** funkcyjnej wybierz obok **pozycji Funkcje,** aby dodać nową funkcję.

    ![Dodawanie funkcji do aplikacji funkcji](./media/how-to-use-custom-allocation-policies/create-function.png)

6. Na stronie **Usługi Azure Functions for .NET — wprowadzenie** do kroku WYBIERZ ŚRODOWISKO **WDRAŻANIA** wybierz kafelek **W portalu,** a następnie wybierz pozycję **Kontynuuj**.

    ![Wybierz środowisko programistyczne portalu](./media/how-to-use-custom-allocation-policies/function-choose-environment.png)

7. Na następnej stronie, w kroku **TWORZENIE FUNKCJI,** wybierz kafelek **Webhook + API,** a następnie wybierz pozycję **Utwórz**. Zostanie utworzona funkcja o nazwie **HttpTrigger1,** a w portalu zostanie wyświetlona zawartość pliku kodu **run.csx.**

8. Wymagane pakiety Referencyjne Nuget. Aby utworzyć początkową bliźniaczą bliźniaczątkę urządzenia, funkcja alokacji niestandardowej używa klas zdefiniowanych w dwóch pakietach Nuget, które muszą zostać załadowane do środowiska hostingowego. Za pomocą usługi Azure Functions pakiety Nuget są przywoły, używając pliku *function.host.* W tym kroku można zapisać i przekazać plik *function.host.*

    1. Skopiuj następujące wiersze do ulubionego edytora i zapisz plik na komputerze jako *function.host*.

        ```xml
        <Project Sdk="Microsoft.NET.Sdk">  
            <PropertyGroup>  
                <TargetFramework>netstandard2.0</TargetFramework>  
            </PropertyGroup>  
            <ItemGroup>  
                <PackageReference Include="Microsoft.Azure.Devices.Provisioning.Service" Version="1.5.0" />  
                <PackageReference Include="Microsoft.Azure.Devices.Shared" Version="1.16.0" />  
            </ItemGroup>  
        </Project>
        ```

    2. W funkcji **HttpTrigger1** rozwiń kartę **Wyświetl pliki** po prawej stronie okna.

        ![Otwórz pliki widoku](./media/how-to-use-custom-allocation-policies/function-open-view-files.png)

    3. Wybierz **pozycję Przekaż**, przejdź do pliku **function.proj** i wybierz pozycję **Otwórz,** aby przekazać plik.

        ![Wybieranie pliku przekazywania](./media/how-to-use-custom-allocation-policies/function-choose-upload-file.png)

9. Zastąp kod funkcji **HttpTrigger1** następującym kodem i **wybierz**zapisz:

    ```csharp
    #r "Newtonsoft.Json"

    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

    using Microsoft.Azure.Devices.Shared;               // For TwinCollection
    using Microsoft.Azure.Devices.Provisioning.Service; // For TwinState

    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        // Get request body
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);

        log.LogInformation("Request.Body:...");
        log.LogInformation(requestBody);

        // Get registration ID of the device
        string regId = data?.deviceRuntimeContext?.registrationId;

        string message = "Uncaught error";
        bool fail = false;
        ResponseObj obj = new ResponseObj();

        if (regId == null)
        {
            message = "Registration ID not provided for the device.";
            log.LogInformation("Registration ID : NULL");
            fail = true;
        }
        else
        {
            string[] hubs = data?.linkedHubs.ToObject<string[]>();

            // Must have hubs selected on the enrollment
            if (hubs == null)
            {
                message = "No hub group defined for the enrollment.";
                log.LogInformation("linkedHubs : NULL");
                fail = true;
            }
            else
            {
                // This is a Contoso Toaster Model 007
                if (regId.Contains("-contoso-tstrsd-007"))
                {
                    //Find the "-toasters-" IoT hub configured on the enrollment
                    foreach(string hubString in hubs)
                    {
                        if (hubString.Contains("-toasters-"))
                            obj.iotHubHostName = hubString;
                    }

                    if (obj.iotHubHostName == null)
                    {
                        message = "No toasters hub found for the enrollment.";
                        log.LogInformation(message);
                        fail = true;
                    }
                    else
                    {
                        // Specify the initial tags for the device.
                        TwinCollection tags = new TwinCollection();
                        tags["deviceType"] = "toaster";

                        // Specify the initial desired properties for the device.
                        TwinCollection properties = new TwinCollection();
                        properties["state"] = "ready";
                        properties["darknessSetting"] = "medium";

                        // Add the initial twin state to the response.
                        TwinState twinState = new TwinState(tags, properties);
                        obj.initialTwin = twinState;
                    }
                }
                // This is a Contoso Heat pump Model 008
                else if (regId.Contains("-contoso-hpsd-088"))
                {
                    //Find the "-heatpumps-" IoT hub configured on the enrollment
                    foreach(string hubString in hubs)
                    {
                        if (hubString.Contains("-heatpumps-"))
                            obj.iotHubHostName = hubString;
                    }

                    if (obj.iotHubHostName == null)
                    {
                        message = "No heat pumps hub found for the enrollment.";
                        log.LogInformation(message);
                        fail = true;
                    }
                    else
                    {
                        // Specify the initial tags for the device.
                        TwinCollection tags = new TwinCollection();
                        tags["deviceType"] = "heatpump";

                        // Specify the initial desired properties for the device.
                        TwinCollection properties = new TwinCollection();
                        properties["state"] = "on";
                        properties["temperatureSetting"] = "65";

                        // Add the initial twin state to the response.
                        TwinState twinState = new TwinState(tags, properties);
                        obj.initialTwin = twinState;
                    }
                }
                // Unrecognized device.
                else
                {
                    fail = true;
                    message = "Unrecognized device registration.";
                    log.LogInformation("Unknown device registration");
                }
            }
        }

        log.LogInformation("\nResponse");
        log.LogInformation((obj.iotHubHostName != null) ? JsonConvert.SerializeObject(obj) : message);

        return (fail)
            ? new BadRequestObjectResult(message) 
            : (ActionResult)new OkObjectResult(obj);
    }

    public class ResponseObj
    {
        public string iotHubHostName {get; set;}
        public TwinState initialTwin {get; set;}
    }
    ```

## <a name="create-the-enrollment"></a>Tworzenie rejestracji

W tej sekcji utworzysz nową grupę rejestracji, która używa niestandardowych zasad alokacji. Dla uproszczenia w tym artykule używa [zaświadczania klucza symetrycznego](concepts-symmetric-key-attestation.md) z rejestracją. Aby uzyskać bezpieczniejsze rozwiązanie, należy rozważyć użycie [zaświadczenia certyfikatu X.509](concepts-security.md#x509-certificates) z łańcuchem zaufania.

1. Nadal w [witrynie Azure portal](https://portal.azure.com), otwórz usługę inicjowania obsługi administracyjnej.

2. Wybierz **pozycję Zarządzaj rejestracjami** w lewym okienku, a następnie wybierz przycisk **Dodaj grupę rejestracji** u góry strony.

3. W **obszarze Dodaj grupę rejestracji**wprowadź następujące informacje i wybierz przycisk **Zapisz.**

    **Nazwa grupy**: Wprowadź **contoso-custom-allocated-devices**.

    **Typ zaświadczenia**: Wybierz **klucz symetryczny**.

    **Automatyczne generowanie kluczy:** to pole wyboru powinno być już zaznaczone.

    **Wybierz sposób przypisywania urządzeń do koncentratorów:** wybierz **opcję Niestandardowa (użyj funkcji platformy Azure).**

    ![Dodawanie niestandardowej grupy rejestracji alokacji dla zaświadczania klucza symetrycznego](./media/how-to-use-custom-allocation-policies/create-custom-allocation-enrollment.png)

4. W **obszarze Dodaj grupę rejestracji**wybierz pozycję **Połącz nowe centrum IoT,** aby połączyć oba nowe centra IoT.

    Wykonaj ten krok dla obu oddziałów IoT hubów.

    **Subskrypcja:** Jeśli masz wiele subskrypcji, wybierz subskrypcję, w której utworzono centra IoT.

    **Centrum IoT:** Wybierz jeden z utworzonych centrów podziału.

    **Zasady dostępu:** Wybierz **iothubowner**.

    ![Łączenie wydzielonych hubów IoT z usługą inicjowania obsługi administracyjnej](./media/how-to-use-custom-allocation-policies/link-divisional-hubs.png)

5. W **programie Dodaj grupę rejestracji**po połączeniu obu oddziałów IoT hubów należy wybrać je jako grupę Centrum IoT dla grupy rejestracji, jak pokazano poniżej:

    ![Tworzenie grupy centrum podziału dla rejestracji](./media/how-to-use-custom-allocation-policies/enrollment-divisional-hub-group.png)

6. W **obszarze Dodaj grupę rejestracji**przewiń w dół do sekcji Wybierz funkcję platformy **Azure** wybierz aplikację Funkcji utworzoną w poprzedniej sekcji. Następnie wybierz utworzoną funkcję i wybierz pozycję Zapisz, aby zapisać grupę rejestracji.

    ![Wybierz funkcję i zapisz grupę rejestracji](./media/how-to-use-custom-allocation-policies/save-enrollment.png)

7. Po zapisaniu rejestracji otwórz ją ponownie i zanotuj **klucz podstawowy**. Należy najpierw zapisać rejestrację, aby wygenerowano klucze. Ten klucz będzie używany do późniejszego generowania unikatowych kluczy urządzeń dla symulowanych urządzeń.

## <a name="derive-unique-device-keys"></a>Wyprowadzanie unikatowych kluczy urządzenia

W tej sekcji utworzysz dwa unikatowe klucze urządzenia. Jeden klucz będzie używany dla symulowanego urządzenia tostera. Drugi klucz będzie używany do symulowanego urządzenia pompy ciepła.

Aby wygenerować klucz urządzenia, należy użyć **klucza podstawowego,** który został wcześniej opisany, aby obliczyć identyfikator rejestracji urządzenia [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) dla każdego urządzenia i przekonwertować wynik na format Base64. Aby uzyskać więcej informacji na temat tworzenia pochodnych kluczy urządzeń z grupami rejestracji, zobacz sekcję rejestracji grupowych [zaświadczania klucza symetrycznego](concepts-symmetric-key-attestation.md).

W tym artykule należy użyć następujących dwóch identyfikatorów rejestracji urządzenia i obliczyć klucz urządzenia dla obu urządzeń. Oba identyfikatory rejestracji mają prawidłowy sufiks do pracy z przykładowym kodem dla niestandardowych zasad alokacji:

* **breakroom499-contoso-tstrsd-007**
* **mainbuilding167-contoso-hpsd-088**

### <a name="linux-workstations"></a>Stacje robocze Linuksa

Jeśli używasz stacji roboczej systemu Linux, możesz użyć openssl do wygenerowania pochodnych kluczy urządzenia, jak pokazano w poniższym przykładzie.

1. Zastąp wartość **keya** **kluczem kluczowym odnotowanym** wcześniej.

    ```bash
    KEY=oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA==

    REG_ID1=breakroom499-contoso-tstrsd-007
    REG_ID2=mainbuilding167-contoso-hpsd-088

    keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
    devkey1=$(echo -n $REG_ID1 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)
    devkey2=$(echo -n $REG_ID2 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)

    echo -e $"\n\n$REG_ID1 : $devkey1\n$REG_ID2 : $devkey2\n\n"
    ```

    ```bash
    breakroom499-contoso-tstrsd-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
    mainbuilding167-contoso-hpsd-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
    ```

### <a name="windows-based-workstations"></a>Stacje robocze z systemem Windows

Jeśli używasz stacji roboczej opartej na systemie Windows, możesz użyć programu PowerShell do wygenerowania klucza urządzenia pochodnego, jak pokazano w poniższym przykładzie.

1. Zastąp wartość **keya** **kluczem kluczowym odnotowanym** wcześniej.

    ```powershell
    $KEY='oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA=='

    $REG_ID1='breakroom499-contoso-tstrsd-007'
    $REG_ID2='mainbuilding167-contoso-hpsd-088'

    $hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
    $hmacsha256.key = [Convert]::FromBase64String($key)
    $sig1 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID1))
    $sig2 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID2))
    $derivedkey1 = [Convert]::ToBase64String($sig1)
    $derivedkey2 = [Convert]::ToBase64String($sig2)

    echo "`n`n$REG_ID1 : $derivedkey1`n$REG_ID2 : $derivedkey2`n`n"
    ```

    ```powershell
    breakroom499-contoso-tstrsd-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
    mainbuilding167-contoso-hpsd-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
    ```

Symulowane urządzenia będą używać pochodnych kluczy urządzenia z każdym identyfikatorem rejestracji do wykonywania zaświadczania klucza symetrycznego.

## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Przygotowywanie środowiska deweloperskiego dla zestawu SDK języka C usługi Azure IoT

W tej sekcji należy przygotować środowisko programistyczne używane do tworzenia [sdk C usługi Azure IoT.](https://github.com/Azure/azure-iot-sdk-c) Zestaw SDK zawiera przykładowy kod symulowanego urządzenia. To urządzenie symulowane podejmie próbę aprowizacji podczas sekwencji rozruchu urządzenia.

Ta sekcja jest zorientowana na stację roboczą opartą na systemie Windows. Na przykład w systemie Linux zobacz konfigurację maszyn wirtualnych w [sekcji Jak aprowizować wielodostępność](how-to-provision-multitenant.md).

1. Pobierz [system kompilacji CMake](https://cmake.org/download/).

    Ważne jest, aby wstępnie wymagane składniki (program Visual Studio oraz pakiet roboczy „Programowanie aplikacji klasycznych w języku C++”) były zainstalowane na tym komputerze **przed** uruchomieniem `CMake` instalacji. Gdy wymagania wstępne zostaną spełnione, a pobrane pliki zweryfikowane, zainstaluj system kompilacji CMake.

2. Znajdź nazwę tagu [dla najnowszej wersji](https://github.com/Azure/azure-iot-sdk-c/releases/latest) SDK.

3. Otwórz wiersz polecenia lub powłokę Git Bash. Uruchom następujące polecenia, aby sklonować najnowszą wersję repozytorium GitHub [SDK usługi Azure IoT C.](https://github.com/Azure/azure-iot-sdk-c) Użyj znacznika znalezionego w poprzednim kroku `-b` jako wartości parametru:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Należy się spodziewać, że ukończenie operacji potrwa kilka minut.

4. Utwórz podkatalog `cmake` w katalogu głównym repozytorium Git, a następnie przejdź do tego folderu. Uruchom następujące polecenia z `azure-iot-sdk-c` katalogu:

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. Uruchom następujące polecenie, które utworzy wersję zestawu SDK specyficzną dla platformy klienta deweloperskiego. Rozwiązanie programu Visual Studio dla symulowanego urządzenia zostanie wygenerowane w katalogu `cmake`. 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

    Jeśli `cmake` nie znajdzie kompilatora Języka C++, może pojawić się błędy kompilacji podczas uruchamiania polecenia. Jeśli tak się stanie, spróbuj uruchomić polecenie w [wierszu polecenia programu Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

    Gdy kompilacja zakończy się powodzeniem, kilka ostatnich wierszy danych wyjściowych będzie wyglądać podobnie do następujących danych wyjściowych:

    ```cmd/sh
    $ cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

## <a name="simulate-the-devices"></a>Symuluj urządzenia

W tej sekcji zaktualizujesz przykładowy inicjowania obsługi administracyjnej o nazwie **prov\_dev\_klienta\_próbki** znajduje się w azure IoT C SDK, który został skonfigurowany wcześniej.

Ten przykładowy kod symuluje sekwencję rozruchu urządzenia, która wysyła żądanie inicjowania obsługi administracyjnej do wystąpienia usługi inicjowania obsługi administracyjnej urządzenia. Sekwencja rozruchowa spowoduje, że urządzenie toster zostanie rozpoznane i przypisane do centrum IoT przy użyciu niestandardowych zasad alokacji.

1. W witrynie Azure Portal wybierz kartę **Przegląd** dla swojej usługi Device Provisioning Service, a następnie zapisz wartość **_Zakres identyfikatorów_**.

    ![Wyodrębnianie informacji o punkcie końcowym usługi Device Provisioning Service z bloku portalu](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. W programie Visual Studio otwórz plik rozwiązania **azure_iot_sdks.sln,** który został wygenerowany przez uruchomienie CMake wcześniej. Plik rozwiązania powinien znajdować się w następującej lokalizacji:

    ```
    azure-iot-sdk-c\cmake\azure_iot_sdks.sln
    ```

3. W oknie *Eksplorator rozwiązań* programu Visual Studio przejdź do folderu **Provision\_Samples**. Rozwiń przykładowy projekt o nazwie **prov\_dev\_client\_sample**. Rozwiń pozycję **Pliki źródłowe**, a następnie otwórz plik **prov\_dev\_client\_sample.c**.

4. Znajdź stałą `id_scope` i zastąp jej wartość wcześniej skopiowaną wartością **Zakres identyfikatorów**. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. Znajdź definicję funkcji `main()` w tym samym pliku. Upewnij się, że zmienna `hsm_type` jest ustawiona na `SECURE_DEVICE_TYPE_SYMMETRIC_KEY`, jak pokazano poniżej:

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

6. Kliknij prawym przyciskiem myszy projekt **prov\_dev\_client\_sample**, a następnie wybierz pozycję **Ustaw jako projekt startowy**.

### <a name="simulate-the-contoso-toaster-device"></a>Symulowanie urządzenia tostera Contoso

1. Aby symulować urządzenie toster, znajdź `prov_dev_set_symmetric_key_info()` wywołanie w **prov\_dev\_\_klienta sample.c,** który jest komentowany.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Odkomentuj wywołanie funkcji i zastąp wartości symbolu zastępczego (w tym nawiasy kątowe) identyfikatorem rejestracji tostera i wygenerowanym wcześniej kluczem urządzenia. Wartość klucza **JC8F96eayuQwwz +PkE7IzjH2lIAjCUnAa61tDigBnSs=** pokazano poniżej jest tylko podany jako przykład.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("breakroom499-contoso-tstrsd-007", "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=");
    ```

    Zapisz plik.

2. W menu Programu Visual Studio wybierz **debugowanie** > **start bez debugowania,** aby uruchomić rozwiązanie. W wierszu o przebudowie projektu wybierz opcję **Tak**, aby odbudować projekt przed uruchomieniem.

    Poniższe dane wyjściowe są przykładem symulowanego urządzenia tostera pomyślnie uruchamiając i łącząc się z wystąpieniem usługi inicjowania obsługi administracyjnej, które ma zostać przypisane do centrum IoT firmy toastów przez niestandardowe zasady alokacji:

    ```cmd
    Provisioning API Version: 1.3.6

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-toasters-hub-1098.azure-devices.net, deviceId: breakroom499-contoso-tstrsd-007

    Press enter key to exit:
    ```

### <a name="simulate-the-contoso-heat-pump-device"></a>Symulacja urządzenia pompy ciepła Contoso

1. Aby symulować urządzenie pompy ciepła, `prov_dev_set_symmetric_key_info()` zaktualizuj wywołanie w **prov\_dev\_\_client sample.c** ponownie za pomocą identyfikatora rejestracji pompy ciepła i klucza urządzenia pochodnego wygenerowanego wcześniej. Wartość klucza **6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=** pokazano poniżej jest również podana tylko jako przykład.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("mainbuilding167-contoso-hpsd-088", "6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=");
    ```

    Zapisz plik.

2. W menu Programu Visual Studio wybierz **debugowanie** > **start bez debugowania,** aby uruchomić rozwiązanie. W wierszu monitu o odbudowanie projektu wybierz opcję **Tak,** aby odbudować projekt przed uruchomieniem.

    Następujące dane wyjściowe są przykładem symulowanego urządzenia pompy ciepła pomyślnie uruchamiając i łącząc się z wystąpieniem usługi inicjowania obsługi administracyjnej, które mają być przypisane do koncentratora IoT pomp ciepła Contoso przez niestandardowe zasady alokacji:

    ```cmd
    Provisioning API Version: 1.3.6

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-heatpumps-hub-1098.azure-devices.net, deviceId: mainbuilding167-contoso-hpsd-088

    Press enter key to exit:
    ```

## <a name="troubleshooting-custom-allocation-policies"></a>Rozwiązywanie problemów z niestandardowymi zasadami alokacji

W poniższej tabeli przedstawiono oczekiwane scenariusze i kody błędów wyników, które mogą zostać otrzymane. Ta tabela ułatwia rozwiązywanie problemów z niestandardowymi błędami zasad alokacji za pomocą funkcji platformy Azure.

| Scenariusz | Wynik rejestracji z usługi inicjowania obsługi administracyjnej | Wyniki sdek inicjowania obsługi administracyjnej |
| -------- | --------------------------------------------- | ------------------------ |
| Element webhook zwraca 200 OK z "iotHubHostName" ustawioną na prawidłową nazwę hosta centrum IoT | Stan wyniku: Przypisano  | SDK zwraca PROV_DEVICE_RESULT_OK wraz z informacjami o koncentratorze |
| Element webhook zwraca 200 OK z 'iotHubHostName' obecny w odpowiedzi, ale ustawiony na pusty ciąg lub null | Stan wyniku: Nie powiodło się<br><br> Kod błędu: CustomAllocationIotHubSpecified (400208) | SDK zwraca PROV_DEVICE_RESULT_HUB_NOT_SPECIFIED |
| Element webhook zwraca 401 Nieautoryzowane | Stan wyniku: Nie powiodło się<br><br>Kod błędu: CustomAllocationUnauthorizedAccess (400209) | SDK zwraca PROV_DEVICE_RESULT_UNAUTHORIZED |
| Rejestracja indywidualna została utworzona w celu wyłączenia urządzenia | Stan wyniku: Wyłączone | SDK zwraca PROV_DEVICE_RESULT_DISABLED |
| Element webhook zwraca kod błędu >= 429 | Aranżacja DPS ponowi próbę kilka razy. Zasady ponawiania próby są obecnie:<br><br>&nbsp;&nbsp;- Liczba ponownych prób: 10<br>&nbsp;&nbsp;- Początkowy interwał: 1s<br>&nbsp;&nbsp;- Przyrost: 9s | SDK zignoruje błąd i prześle kolejny komunikat o stanie w określonym czasie |
| Element webhook zwraca inny kod stanu | Stan wyniku: Nie powiodło się<br><br>Kod błędu: CustomAllocationFailed (400207) | SDK zwraca PROV_DEVICE_RESULT_DEV_AUTH_ERROR |

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli planujesz kontynuować pracę z zasobami utworzonymi w tym artykule, możesz je pozostawić. Jeśli nie zamierzasz nadal używać zasobów, użyj następujących kroków, aby usunąć wszystkie zasoby utworzone w tym artykule, aby uniknąć niepotrzebnych opłat.

W tym miejscu przyjęto założenie, że wszystkie zasoby w tym artykule zostały utworzone zgodnie z instrukcjami w tej samej grupie zasobów o nazwie **contoso-us-resource-group**.

> [!IMPORTANT]
> Usunięcie grupy zasobów jest nieodwracalne. Grupa zasobów oraz wszystkie zawarte w niej zasoby zostaną trwale usunięte. Uważaj, aby nie usunąć przypadkowo niewłaściwych zasobów lub niewłaściwej grupy zasobów. Jeśli usługa IoT Hub została utworzona wewnątrz istniejącej grupy zasobów zawierającej zasoby, które chcesz zachować, zamiast usuwać całą grupę zasobów, usuń tylko zasób usługi IoT Hub.
>

Aby usunąć grupę zasobów według nazwy:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i wybierz pozycję **Grupy zasobów**.

2. W polach tekstowych **Filtruj według nazwy...** wpisz nazwę grupy zasobów zawierającej zasoby, **contoso-us-resource-group**. 

3. Po prawej stronie grupy zasobów na liście wyników wybierz polecenie **...** **Usuń grupę zasobów**.

4. Zostaniesz poproszony o potwierdzenie usunięcia grupy zasobów. Wpisz ponownie nazwę grupy zasobów, aby potwierdzić, a następnie wybierz pozycję **Usuń**. Po krótkim czasie grupa zasobów i wszystkie zawarte w niej zasoby zostaną usunięte.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej Reprovisioning, zobacz [Urządzenia usługi IoT Hub reprovisioning pojęcia](concepts-device-reprovision.md) 
* Aby dowiedzieć się więcej Deprovisioning, zobacz [Jak deprowizować urządzenia, które wcześniej były automatycznie aprowiowane](how-to-unprovision-devices.md) 
