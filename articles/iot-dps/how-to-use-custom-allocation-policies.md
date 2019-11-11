---
title: Jak używać niestandardowych zasad alokacji przy użyciu usługi Azure IoT Hub Device Provisioning Service | Microsoft Docs
description: Jak używać niestandardowych zasad alokacji przy użyciu usługi Azure IoT Hub Device Provisioning Service
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.openlocfilehash: 11872f8efcebf39edef2f97cd30c225edbe74bb4
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903568"
---
# <a name="how-to-use-custom-allocation-policies"></a>Jak używać niestandardowych zasad alokacji

Niestandardowe zasady alokacji dają większą kontrolę nad sposobem przypisywania urządzeń do centrum IoT Hub. Jest to realizowane za pomocą niestandardowego kodu w [funkcji platformy Azure](../azure-functions/functions-overview.md) w celu przypisywania urządzeń do centrum IoT. Usługa Device Provisioning wywołuje kod funkcji platformy Azure, dostarczając wszystkie istotne informacje o urządzeniu i rejestracji. Kod funkcji jest wykonywany i zwraca informacje o usłudze IoT Hub używane do aprowizacji urządzenia.

Korzystając z niestandardowych zasad alokacji, należy zdefiniować własne zasady alokacji, jeśli zasady udostępniane przez usługę Device Provisioning nie spełniają wymagań danego scenariusza.

Na przykład może być konieczne przeanalizowanie certyfikatu używanego przez urządzenie podczas aprowizacji i przypisanie urządzenia do usługi IoT Hub na podstawie właściwości certyfikatu. Możesz też mieć informacje przechowywane w bazie danych dla urządzeń i muszą wysyłać zapytania do bazy danych, aby określić, które Centrum IoT ma być przypisane do urządzenia.

W C#tym artykule przedstawiono niestandardowe zasady alokacji przy użyciu funkcji platformy Azure, która została zapisywana. Tworzone są dwa nowe centra IoT przedstawiające *Wydział wyskakujących powiadomień firmy Contoso* oraz *dział pomp cieplnych firmy Contoso*. Urządzenia żądające aprowizacji muszą mieć identyfikator rejestracji z jednym z następujących sufiksów, które mają zostać zaakceptowane do aprowizacji:

* **-contoso-tstrsd-007**: Wydział wyskakujących powiadomień contoso
* **-contoso-hpsd-088**: dział pomp cieplnych contoso

Urządzenia będą obsługiwane na podstawie jednego z tych wymaganych sufiksów w IDENTYFIKATORze rejestracji. Te urządzenia będą symulowane przy użyciu przykładu aprowizacji dołączonego do [zestawu SDK języka C usługi Azure IoT](https://github.com/Azure/azure-iot-sdk-c).

W tym artykule wykonaj następujące czynności:

* Korzystanie z interfejsu wiersza polecenia platformy Azure do tworzenia dwóch centrów **IoT działu** **firmy Contoso**
* Tworzenie nowej rejestracji grupowej przy użyciu funkcji platformy Azure dla niestandardowych zasad alokacji
* Utwórz klucze urządzeń dla dwóch symulacji urządzeń.
* Konfigurowanie środowiska deweloperskiego dla zestawu SDK usługi Azure IoT C
* Symuluj urządzenia i sprawdź, czy są one obsługiwane zgodnie z przykładowym kodem w niestandardowych zasadach alokacji

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Zakończenie [konfigurowania IoT Hub Device Provisioning Service przy użyciu Azure Portal](./quick-setup-auto-provision.md) przewodnika Szybki Start.
* [Program Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 lub nowszy z włączonym obciążeniem ["Programowanie aplikacji klasycznych C++"](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) .
* Zainstalowana najnowsza wersja usługi[Git](https://git-scm.com/download/).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-two-divisional-iot-hubs"></a>Utwórz dwa rozdziałowe centra IoT

W tej sekcji użyjesz Azure Cloud Shell, aby utworzyć dwa nowe centra IoT reprezentujące **Wydział wyskakujących powiadomień firmy Contoso** i **dział pomp cieplnych firmy Contoso**.

1. Użyj Azure Cloud Shell, aby utworzyć grupę zasobów za pomocą polecenia [AZ Group Create](/cli/azure/group#az-group-create) . Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

    Poniższy przykład tworzy grupę zasobów o nazwie *contoso-US-Resource-Group* w regionie *wschodnim* . Zaleca się użycie tej grupy dla wszystkich zasobów utworzonych w tym artykule. To podejście ułatwi oczyszczenie po zakończeniu.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location eastus
    ```

2. Użyj Azure Cloud Shell, aby utworzyć **wyskakujące powiadomienia firmy Contoso działu** IoT Hub za pomocą polecenia [AZ IoT Hub Create](/cli/azure/iot/hub#az-iot-hub-create) . Centrum IoT zostanie dodane do *grupy contoso-US-Resource-Group*.

    W poniższym przykładzie utworzono Centrum IoT o nazwie *contoso-wyskakujące-Hub-1098* w lokalizacji *Wschodnie* . Musisz użyć unikatowej nazwy centrum. Utwórz własny sufiks w nazwie centrum zamiast **1098**. Przykładowy kod dla niestandardowych zasad alokacji wymaga `-toasters-` w nazwie centrum.

    ```azurecli-interactive 
    az iot hub create --name contoso-toasters-hub-1098 --resource-group contoso-us-resource-group --location eastus --sku S1
    ```

    Wykonanie tego polecenia może potrwać kilka minut.

3. Użyj Azure Cloud Shell, aby utworzyć **pompy firmy Contoso, działu** IoT Hub za pomocą polecenia [AZ IoT Hub Create](/cli/azure/iot/hub#az-iot-hub-create) . To centrum IoT zostanie również dodane do *grupy contoso-US-Resource-Group*.

    W poniższym przykładzie utworzono Centrum IoT o nazwie *contoso-heatpumps-Hub-1098* w lokalizacji *Wschodnie* . Musisz użyć unikatowej nazwy centrum. Utwórz własny sufiks w nazwie centrum zamiast **1098**. Przykładowy kod dla niestandardowych zasad alokacji wymaga `-heatpumps-` w nazwie centrum.

    ```azurecli-interactive 
    az iot hub create --name contoso-heatpumps-hub-1098 --resource-group contoso-us-resource-group --location eastus --sku S1
    ```

    Wykonanie tego polecenia może potrwać kilka minut.

## <a name="create-the-enrollment"></a>Utwórz rejestrację

W tej sekcji utworzysz nową grupę rejestracji, która używa niestandardowych zasad alokacji. Dla uproszczenia w tym artykule jest stosowane [zaświadczenie klucza symetrycznego](concepts-symmetric-key-attestation.md) z rejestracją. Aby lepiej zabezpieczyć rozwiązanie, należy rozważyć użycie [zaświadczania certyfikatu X. 509](concepts-security.md#x509-certificates) z łańcuchem zaufania.

1. Zaloguj się do [Azure Portal](https://portal.azure.com)i Otwórz wystąpienie usługi Device Provisioning Service.

2. Wybierz kartę **Zarządzanie rejestracjami** , a następnie kliknij przycisk **Dodaj grupę rejestracji** w górnej części strony. 

3. W obszarze **Dodaj grupę rejestracji**Wprowadź poniższe informacje, a następnie kliknij przycisk **Zapisz** .

    **Nazwa grupy**: wprowadź **contoso-Custom-przydzieloną-Devices**.

    **Typ zaświadczania**: Wybierz **klucz symetryczny**.

    **Automatycznie Generuj klucze**: to pole wyboru powinno być już zaznaczone.

    **Wybierz sposób przypisywania urządzeń do centrów**: wybierz pozycję **niestandardowa (Użyj funkcji platformy Azure)** .

    ![Dodaj grupę rejestracji niestandardowej alokacji na potrzeby zaświadczania klucza symetrycznego](./media/how-to-use-custom-allocation-policies/create-custom-allocation-enrollment.png)

4. W obszarze **Dodaj grupę rejestracji**kliknij pozycję **Połącz nowe centrum IoT Hub** , aby połączyć oba nowe centra usługi IoT. 

    Wykonaj ten krok dla obu centrów obsługi IoT.

    **Subskrypcja**: Jeśli masz wiele subskrypcji, wybierz subskrypcję, w której utworzono centra usługi IoT.

    **Centrum IoT**: Wybierz jeden z utworzonych centrów rozdziałowych.

    **Zasady dostępu**: Wybierz **iothubowner**.

    ![Łączenie centrów IoT z usługą aprowizacji](./media/how-to-use-custom-allocation-policies/link-divisional-hubs.png)

5. Na stronie **Dodawanie grupy rejestracji**, gdy oba centra usługi IoT zostały połączone, należy je wybrać jako grupę IoT Hub grupy rejestracji, jak pokazano poniżej:

    ![Tworzenie grupy centrów rozdziałowych na potrzeby rejestracji](./media/how-to-use-custom-allocation-policies/enrollment-divisional-hub-group.png)

6. Na stronie **Dodaj grupę rejestracji**przewiń w dół do sekcji **Wybierz funkcję platformy Azure** , a następnie kliknij pozycję **Utwórz nową aplikację funkcji**.

7. Na **aplikacja funkcji** utworzyć strony, która zostanie otwarta, wprowadź następujące ustawienia nowej funkcji, a następnie kliknij pozycję **Utwórz**:

    **Nazwa aplikacji**: Wprowadź unikatową nazwę aplikacji funkcji. element **contoso-Function-App-1098** jest pokazywany jako przykład.

    **Grupa zasobów**: wybierz opcję **Użyj istniejącej** i **contoso-US-Resource-Group** , aby zachować wszystkie zasoby utworzone w tym artykule.

    **Application Insights**: w tym ćwiczeniu można ją wyłączyć.

    ![Tworzenie aplikacji funkcji](./media/how-to-use-custom-allocation-policies/function-app-create.png)

8. Na stronie **Dodawanie grupy rejestracji** upewnij się, że została wybrana Nowa aplikacja funkcji. Może być konieczne ponownie wybranie subskrypcji w celu odświeżenia listy aplikacji funkcji.

    Po wybraniu nowej aplikacji funkcji kliknij pozycję **Utwórz nową funkcję**.

    ![Tworzenie aplikacji funkcji](./media/how-to-use-custom-allocation-policies/click-create-new-function.png)

    zostanie otwarta nowa aplikacja funkcji.

9. W aplikacji funkcji kliknij przycisk **+** , aby utworzyć nową funkcję

    ![Tworzenie aplikacji funkcji](./media/how-to-use-custom-allocation-policies/new-function.png)

    Dla nowej funkcji Użyj ustawień domyślnych, aby utworzyć nowy **element webhook i interfejs API** przy użyciu języka **CSharp** . Kliknij pozycję **Utwórz tę funkcję**.

    Spowoduje to utworzenie nowej C# funkcji o nazwie **HttpTriggerCSharp1**.

10. Zastąp kod nowej C# funkcji następującym kodem i kliknij przycisk **Zapisz**:

    ```csharp
    #r "Newtonsoft.Json"
    using System.Net;
    using System.Text;
    using Newtonsoft.Json;

    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        // Just some diagnostic logging
        log.Info("C# HTTP trigger function processed a request.");
        log.Info("Request.Content:...");
        log.Info(req.Content.ReadAsStringAsync().Result);

        // Get request body
        dynamic data = await req.Content.ReadAsAsync<object>();

        // Get registration ID of the device
        string regId = data?.deviceRuntimeContext?.registrationId;

        string message = "Uncaught error";
        bool fail = false;
        ResponseObj obj = new ResponseObj();

        if (regId == null)
        {
            message = "Registration ID not provided for the device.";
            log.Info("Registration ID : NULL");
            fail = true;
        }
        else
        {
            string[] hubs = data?.linkedHubs.ToObject<string[]>();

            // Must have hubs selected on the enrollment
            if (hubs == null)
            {
                message = "No hub group defined for the enrollment.";
                log.Info("linkedHubs : NULL");
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
                        log.Info(message);
                        fail = true;
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
                        log.Info(message);
                        fail = true;
                    }
                }
                // Unrecognized device.
                else
                {
                    fail = true;
                    message = "Unrecognized device registration.";
                    log.Info("Unknown device registration");
                }
            }
        }

        return (fail)
            ? req.CreateResponse(HttpStatusCode.BadRequest, message)
            : new HttpResponseMessage(HttpStatusCode.OK)
            {
                Content = new StringContent(JsonConvert.SerializeObject(obj, Formatting.Indented), Encoding.UTF8, "application/json")
            };
    }

    public class DeviceTwinObj
    {
        public string deviceId {get; set;}
    }

    public class ResponseObj
    {
        public string iotHubHostName {get; set;}
        public string IoTHub {get; set;}
        public DeviceTwinObj initialTwin {get; set;}
        public string[] linkedHubs {get; set;}
        public string enrollment {get; set;}
    }
    ```

11. Wróć do strony **Dodawanie grupy rejestracji** i upewnij się, że została wybrana Nowa funkcja. Może być konieczne ponownie wybranie aplikacji funkcji w celu odświeżenia listy funkcji.

    Po wybraniu nowej funkcji kliknij pozycję **Zapisz** , aby zapisać grupę rejestracji.

    ![Na koniec Zapisz grupę rejestracji](./media/how-to-use-custom-allocation-policies/save-enrollment.png)

12. Po zapisaniu rejestracji Otwórz ją ponownie i zanotuj **klucz podstawowy**. Musisz najpierw zapisać rejestrację, aby generować klucze. Ten klucz zostanie użyty do wygenerowania unikatowych kluczy urządzeń dla symulowanych urządzeń.

## <a name="derive-unique-device-keys"></a>Utwórz unikatowe klucze urządzeń

W tej sekcji utworzysz dwa unikatowe klucze urządzeń. Jeden klucz zostanie użyty dla symulowanego urządzenia wyskakującego. Drugi klucz zostanie użyty dla symulowanego urządzenia pompy termicznej.

Aby wygenerować klucz urządzenia, należy użyć wcześniej zanotowanego **klucza podstawowego** do obliczenia wartości [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) identyfikatora rejestracji urządzenia dla każdego urządzenia i przekonwertowania wyniku na format Base64. Aby uzyskać więcej informacji na temat tworzenia pochodnych kluczy urządzeń przy użyciu grup rejestracji, zobacz sekcję rejestracje grupowe [zaświadczania klucza symetrycznego](concepts-symmetric-key-attestation.md).

W przykładzie w tym artykule należy użyć następujących dwóch identyfikatorów rejestracji urządzeń i obliczyć klucz urządzenia dla obu urządzeń. Oba identyfikatory rejestracji mają prawidłowy sufiks do pracy z przykładowym kodem dla niestandardowych zasad alokacji:

* **breakroom499-contoso-tstrsd-007**
* **mainbuilding167-contoso-hpsd-088**

### <a name="linux-workstations"></a>Stacje robocze systemu Linux

Jeśli używasz stacji roboczej z systemem Linux, możesz użyć OpenSSL, aby wygenerować pochodne klucze urządzenia, jak pokazano w poniższym przykładzie.

1. Zastąp wartość **klucza kluczem** **podstawowym** zanotowanym wcześniej.

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

Jeśli używasz stacji roboczej z systemem Windows, możesz użyć programu PowerShell, aby wygenerować pochodny klucz urządzenia, jak pokazano w poniższym przykładzie.

1. Zastąp wartość **klucza kluczem** **podstawowym** zanotowanym wcześniej.

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

Symulowane urządzenia będą używały pochodnych kluczy urządzeń z każdym IDENTYFIKATORem rejestracji w celu przeprowadzenia zaświadczania klucza symetrycznego.

## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Przygotowywanie środowiska deweloperskiego dla zestawu SDK języka C usługi Azure IoT

W tej sekcji przygotowano środowisko programistyczne używane do kompilowania [zestawu SDK języka C usługi Azure IoT](https://github.com/Azure/azure-iot-sdk-c). Zestaw SDK zawiera przykładowy kod dla symulowanego urządzenia. To urządzenie symulowane podejmie próbę aprowizacji podczas sekwencji rozruchu urządzenia.

Ta sekcja jest ukierunkowana na stację roboczą opartą na systemie Windows. Aby zapoznać się z przykładem systemu Linux, zobacz Konfigurowanie maszyn wirtualnych w temacie [jak zapewnić obsługę wielodostępności](how-to-provision-multitenant.md).

1. Pobierz [system kompilacji CMAKE](https://cmake.org/download/).

    Przed rozpoczęciem instalacji `CMake` należy pamiętać, że wymagania wstępne programu Visual Studio (Visual Studio C++i "Programowanie aplikacji klasycznych") są zainstalowane na maszynie. Po sprawdzeniu wymagań wstępnych i zakończeniu pobierania należy zainstalować system kompilacji CMake.

2. Otwórz wiersz polecenia lub powłokę Git Bash. Wykonaj następujące polecenie, aby sklonować repozytorium GitHub zestawu SDK języka C usługi IoT Azure:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```

    Należy się spodziewać, że ukończenie operacji potrwa kilka minut.

3. Utwórz podkatalog `cmake` w katalogu głównym repozytorium Git, a następnie przejdź do tego folderu. 

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. Uruchom następujące polecenie, które utworzy wersję zestawu SDK specyficzną dla platformy klienta deweloperskiego. Rozwiązanie programu Visual Studio dla symulowanego urządzenia zostanie wygenerowane w katalogu `cmake`. 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

    Jeśli `cmake` nie znajdzie C++ kompilatora, podczas uruchamiania polecenia mogą wystąpić błędy kompilacji. Jeśli tak się stanie, spróbuj uruchomić polecenie w [wierszu polecenia programu Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

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

## <a name="simulate-the-devices"></a>Symulowanie urządzeń

W tej sekcji zostanie zaktualizowane przykładowe Inicjowanie obsługi o nazwie **prov\_dev\_client\_Sample** , które znajdują się w wcześniej SKONFIGUROWANYM zestawie SDK języka C usługi Azure IoT.

Ten przykładowy kod symuluje sekwencję rozruchu urządzenia, która wysyła żądanie aprowizacji do wystąpienia usługi Device Provisioning. Sekwencja rozruchu spowoduje, że urządzenie wyskakujące zostanie rozpoznane i przypisane do usługi IoT Hub przy użyciu niestandardowych zasad alokacji.

1. W witrynie Azure Portal wybierz kartę **Przegląd** dla swojej usługi Device Provisioning Service, a następnie zapisz wartość **_Zakres identyfikatorów_** .

    ![Wyodrębnianie informacji o punkcie końcowym usługi Device Provisioning Service z bloku portalu](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. W programie Visual Studio Otwórz plik rozwiązania **azure_iot_sdks. sln** , który został wygenerowany przez uruchomienie CMAKE wcześniej. Plik rozwiązania powinien znajdować się w następującej lokalizacji:

    ```
    \azure-iot-sdk-c\cmake\azure_iot_sdks.sln
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

### <a name="simulate-the-contoso-toaster-device"></a>Symulowanie urządzenia wyskakującego firmy Contoso

1. Aby zasymulować urządzenie wyskakujące, Znajdź wywołanie `prov_dev_set_symmetric_key_info()` w **prov\_dev\_client\_Sample. c** , który jest oznaczony jako komentarz.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Usuń komentarz wywołania funkcji i Zastąp wartości symboli zastępczych (włącznie z nawiasami ostrymi) IDENTYFIKATORem rejestracji wyskakującego powiadomienia i wygenerowanym wcześniej kluczem urządzenia. Wartość klucza **JC8F96eayuQwwz + PkE7IzjH2lIAjCUnAa61tDigBnSs =** pokazano poniżej jest pokazana jako przykład.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("breakroom499-contoso-tstrsd-007", "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=");
    ```

    Zapisz plik.

2. Z menu programu Visual Studio wybierz pozycję **Debuguj** > **Uruchom bez debugowania**, aby uruchomić rozwiązanie. W monicie o ponowne skompilowanie projektu kliknij przycisk **Tak**, aby ponownie skompilować projekt przed uruchomieniem.

    Poniższe dane wyjściowe to przykład symulowanego urządzenia wyskakującego pomyślnie, który przeprowadził rozruch i nawiązać połączenie z wystąpieniem usługi aprowizacji do przypisania do centrum IoT Hub przez niestandardowe zasady alokacji:

    ```cmd
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-toasters-hub-1098.azure-devices.net, deviceId: breakroom499-contoso-tstrsd-007

    Press enter key to exit:
    ```

### <a name="simulate-the-contoso-heat-pump-device"></a>Symulowanie urządzenia pompy termicznej firmy Contoso

1. Aby zasymulować urządzenie pompy cieplnej, zaktualizuj wywołanie `prov_dev_set_symmetric_key_info()` w **prov\_dev\_client\_Sample. c** ponownie z identyfikatorem rejestracji pompy cieplnej i wygenerowanym wcześniej kluczem urządzenia. Wartość klucza **6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg =** pokazana poniżej jest również podana jako przykład.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("mainbuilding167-contoso-hpsd-088", "6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=");
    ```

    Zapisz plik.

2. Z menu programu Visual Studio wybierz pozycję **Debuguj** > **Uruchom bez debugowania**, aby uruchomić rozwiązanie. W wierszu polecenia, aby ponownie skompilować projekt, kliknij przycisk **tak** , aby ponownie skompilować projekt przed uruchomieniem.

    Następujące dane wyjściowe są przykładem urządzenia symulowanej pompy cieplnej pomyślnie rozruchu i nawiązywania połączenia z wystąpieniem usługi aprowizacji, które ma zostać przypisane do centrum IoT firmy Contoso, w ramach niestandardowych zasad alokacji:

    ```cmd
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-heatpumps-hub-1098.azure-devices.net, deviceId: mainbuilding167-contoso-hpsd-088

    Press enter key to exit:
    ```

## <a name="troubleshooting-custom-allocation-policies"></a>Rozwiązywanie problemów z niestandardowymi zasadami alokacji

W poniższej tabeli przedstawiono oczekiwane scenariusze i kody błędów wyników, które mogą zostać wyświetlone. Ta tabela ułatwia rozwiązywanie problemów z niestandardowymi błędami zasad alokacji przy użyciu Azure Functions.

| Scenariusz | Wynik rejestracji z usługi aprowizacji | Wyniki zestawu SDK aprowizacji |
| -------- | --------------------------------------------- | ------------------------ |
| Element webhook zwraca 200 OK z ustawieniem "iotHubHostName" prawidłową nazwą hosta Centrum IoT Hub | Stan wyniku: przypisano  | Zestaw SDK zwraca PROV_DEVICE_RESULT_OK wraz z informacjami o centrum |
| Element webhook zwraca 200 OK z elementem "iotHubHostName" znajdującym się w odpowiedzi, ale ustawia pusty ciąg lub wartość null | Stan wyniku: niepowodzenie<br><br> Kod błędu: CustomAllocationIotHubNotSpecified (400208) | Zestaw SDK zwraca PROV_DEVICE_RESULT_HUB_NOT_SPECIFIED |
| Element webhook zwraca 401 bez autoryzacji | Stan wyniku: niepowodzenie<br><br>Kod błędu: CustomAllocationUnauthorizedAccess (400209) | Zestaw SDK zwraca PROV_DEVICE_RESULT_UNAUTHORIZED |
| Utworzono indywidualną rejestrację w celu wyłączenia urządzenia | Stan wyniku: wyłączone | Zestaw SDK zwraca PROV_DEVICE_RESULT_DISABLED |
| Element webhook zwraca kod błędu > = 429 | Trwa ponawianie przez aranżację DPS. Zasady ponawiania są obecnie następujące:<br><br>&nbsp;&nbsp;— liczba ponownych prób: 10<br>&nbsp;&nbsp;— początkowy interwał: 1S<br>&nbsp;&nbsp;— przyrost: 9 | Zestaw SDK zignoruje błąd i wyśle inny komunikat o stanie w określonym czasie |
| Element webhook zwraca każdy inny kod stanu | Stan wyniku: niepowodzenie<br><br>Kod błędu: CustomAllocationFailed (400207) | Zestaw SDK zwraca PROV_DEVICE_RESULT_DEV_AUTH_ERROR |

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli planujesz kontynuować pracę z zasobami utworzonymi w tym artykule, możesz je pozostawić. Jeśli nie planujesz kontynuowania korzystania z zasobów, wykonaj następujące kroki, aby usunąć wszystkie zasoby utworzone w tym artykule, aby uniknąć niepotrzebnych opłat.

W tym artykule przyjęto założenie, że utworzono wszystkie zasoby z tego artykułu zgodnie z instrukcjami w tej samej grupie zasobów o nazwie **contoso-US-Resource-Group**.

> [!IMPORTANT]
> Usunięcie grupy zasobów jest nieodwracalne. Grupa zasobów oraz wszystkie zawarte w niej zasoby zostaną trwale usunięte. Uważaj, aby nie usunąć przypadkowo niewłaściwych zasobów lub niewłaściwej grupy zasobów. Jeśli usługa IoT Hub została utworzona wewnątrz istniejącej grupy zasobów zawierającej zasoby, które chcesz zachować, zamiast usuwać całą grupę zasobów, usuń tylko zasób usługi IoT Hub.
>

Aby usunąć grupę zasobów według nazwy:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i kliknij pozycję **Grupy zasobów**.

2. W polu tekstowym **Filtruj według nazwy..** . Wpisz nazwę grupy zasobów zawierającej Twoje zasoby, **contoso-US-Resource-Group**. 

3. Z prawej strony grupy zasobów na liście wyników kliknij pozycję **...** , a następnie kliknij pozycję **Usuń grupę zasobów**.

4. Zostanie wyświetlony monit o potwierdzenie usunięcia grupy zasobów. Ponownie wpisz nazwę grupy zasobów w celu potwierdzenia, a następnie kliknij pozycję **Usuń**. Po krótkim czasie grupa zasobów i wszystkie zawarte w niej zasoby zostaną usunięte.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej, zobacz temat [IoT Hub ponowne Inicjowanie obsługi administracyjnej urządzeń](concepts-device-reprovision.md) 
* Aby dowiedzieć się więcej, zobacz [Jak anulować obsługę administracyjną urządzeń, które były wcześniej inicjowane](how-to-unprovision-devices.md) . 
