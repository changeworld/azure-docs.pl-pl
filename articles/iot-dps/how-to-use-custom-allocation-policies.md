---
title: Jak używać zasad alokacji niestandardowych za pomocą usługi Azure IoT Hub Device Provisioning | Dokumentacja firmy Microsoft
description: Jak używać zasad alokacji niestandardowych za pomocą usługi Azure IoT Hub Device Provisioning Service
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.openlocfilehash: f0eb2f7358e8fb1564275e1de510f302d2eef90b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59500944"
---
# <a name="how-to-use-custom-allocation-policies"></a>Jak używać zasad niestandardowych alokacji


Zasady alokacji niestandardowy zapewnia większą kontrolę nad sposób przypisywania urządzeń do usługi IoT hub. Jest to realizowane za pomocą kodu niestandardowego w [funkcji platformy Azure](../azure-functions/functions-overview.md) przypisywanie urządzeń do Centrum IoT hub. Usługi device provisioning wywołuje kod funkcji platformy Azure zawierającą wszystkie istotne informacje o urządzeniu i rejestracji. Kod funkcji jest wykonywane i zwraca informacje Centrum IoT, które są używane do aprowizacji urządzenia.

Za pomocą zasad niestandardowych alokacji należy zdefiniować własne zasady alokacji gdy zasady udostępniane przez usługi Device Provisioning Service nie spełniają wymagania danego scenariusza.

Na przykład może być możesz zbadać certyfikat którego używa podczas aprowizowania urządzenia i przypisać urządzenia do usługi IoT hub na podstawie właściwości certyfikatu. Może być może informacje są przechowywane w bazie danych dla urządzeń i muszą wykonywać zapytania bazy danych, aby określić, których urządzenia powinny być przypisane do usługi IoT hub.


W tym artykule przedstawiono zasady alokacji niestandardowych przy użyciu funkcji platformy Azure napisane w języku C#. Dwa nowe centra IoT Hub są utworzone reprezentujących *dzielenia tostery Contoso* i *dzielenia pompy cieplne Contoso*. Urządzenia wymagające inicjowania obsługi musi mieć identyfikator rejestracji przy użyciu jednego z następujących sufiksy byli akceptowani inicjowania obsługi administracyjnej:

- **-contoso-tstrsd-007**: Dzielenie tostery firmy Contoso
- **-contoso-hpsd-088**: Dzielenie pompy cieplne firmy Contoso

Urządzenia będą udostępniane na podstawie jednej z tych sufiksów wymagane na identyfikator rejestracji. Te urządzenia będą symulowane, przy użyciu przykładu aprowizacji objęte [zestawu SDK C usługi IoT Azure](https://github.com/Azure/azure-iot-sdk-c). 

W tym artykule należy wykonać następujące czynności:

* Użyj wiersza polecenia platformy Azure, aby utworzyć dwa centra IoT oddziału firmy Contoso (**dzielenia tostery Contoso** i **dzielenia pompy cieplne Contoso**)
* Tworzenie nowej rejestracji grupowej, za pomocą funkcji platformy Azure dla zasad alokacji niestandardowe
* Utwórz klucze urządzeń dla dwóch symulacji urządzenia.
* Konfigurowanie środowiska deweloperskiego dla zestawu SDK C usługi IoT platformy Azure
* Symulowanie urządzenia, które chcesz zobaczyć, że są one aprowizacji zgodnie z przykładowego kodu zasady niestandardowe alokacji


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Ukończenie [skonfigurować IoT Hub Device Provisioning Service przy użyciu witryny Azure portal](./quick-setup-auto-provision.md) Szybki Start.
* Program Visual Studio 2015 lub [Visual Studio 2017](https://www.visualstudio.com/vs/) z włączonym pakietem roboczym [„Programowanie aplikacji klasycznych w języku C++”](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/).
* Zainstalowana najnowsza wersja usługi[Git](https://git-scm.com/download/).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-two-divisional-iot-hubs"></a>Utwórz dwa wydzielonych centra IoT Hub

W tej sekcji użyjesz usługi Azure Cloud Shell, aby utworzyć dwa nowe centra IoT Hub reprezentujący **dzielenia tostery Contoso** i **dzielenia pompy cieplne Contoso**.

1. Użyj usługi Azure Cloud Shell, aby utworzyć grupę zasobów za pomocą [Tworzenie grupy az](/cli/azure/group#az-group-create) polecenia. Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. 

    Poniższy przykład tworzy grupę zasobów o nazwie *contoso-us-resource-group* w *eastus* regionu. Zaleca się użycie tej grupy dla wszystkich zasobów utworzonych w tym artykule. Takie podejście ułatwi oczyszczania po zakończeniu.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location eastus
    ```

2. Użyj usługi Azure Cloud Shell, aby utworzyć **dzielenia tostery Contoso** usługi IoT hub przy użyciu [az iot hub, utworzyć](/cli/azure/iot/hub#az-iot-hub-create) polecenia. Usługa IoT hub zostanie dodany do *contoso-us-resource-group*.

    Poniższy przykład obejmuje tworzenie Centrum IoT o nazwie *contoso tostery hub-1098* w *eastus* lokalizacji. Możesz korzystać z własnej nazwy Centrum unikatowy. Tworzą własne sufiks nazwę centrum **1098**. Przykład kodu dla zasad alokacji niestandardowe wymaga `-toasters-` nazwę koncentratora.

    ```azurecli-interactive 
    az iot hub create --name contoso-toasters-hub-1098 --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    To polecenie może potrwać kilka minut.

3. Użyj usługi Azure Cloud Shell, aby utworzyć **dzielenia pompy cieplne Contoso** usługi IoT hub przy użyciu [az iot hub, utworzyć](/cli/azure/iot/hub#az-iot-hub-create) polecenia. To IoT hub zostanie także dodana do *contoso-us-resource-group*.

    Poniższy przykład obejmuje tworzenie Centrum IoT o nazwie *contoso-heatpumps-hub-1098* w *eastus* lokalizacji. Możesz korzystać z własnej nazwy Centrum unikatowy. Tworzą własne sufiks nazwę centrum **1098**. Przykład kodu dla zasad alokacji niestandardowe wymaga `-heatpumps-` nazwę koncentratora.

    ```azurecli-interactive 
    az iot hub create --name contoso-heatpumps-hub-1098 --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    To polecenie również może potrwać kilka minut.




## <a name="create-the-enrollment"></a>Tworzenie rejestracji

W tej sekcji utworzysz nową grupę rejestracji, która używa zasady niestandardowe alokacji. Dla uproszczenia w tym artykule użyto [zaświadczenie klucza symetrycznego](concepts-symmetric-key-attestation.md) w przypadku rejestracji. Bardziej bezpieczne rozwiązanie, należy wziąć pod uwagę przy użyciu [zaświadczenia certyfikat X.509](concepts-security.md#x509-certificates) przy użyciu łańcucha zaufania.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com), a następnie otwórz swoje wystąpienie usługi Device Provisioning.

2. Wybierz **Zarządzanie rejestracjami** kartę, a następnie kliknij przycisk **Dodaj grupę rejestracji** znajdujący się u góry strony. 

3. Na **Dodaj grupę rejestracji**, wprowadź następujące informacje i kliknij przycisk **Zapisz** przycisku.

    **Nazwa grupy**: Wprowadź **contoso niestandardowe — przydzielone urządzeń**.

    **Typ zaświadczeń**: Wybierz **klucz symetryczny**.

    **Automatycznie generuj klucze**: Już to pole wyboru powinno być zaznaczone.

    **Wybierz sposób przypisywania urządzeń do centrów**: Wybierz **niestandardowe (Użyj funkcji platformy Azure)**.

    ![Dodaj niestandardowe alokacji grupę rejestracji dla zaświadczenia klucza symetrycznego](./media/how-to-use-custom-allocation-policies/create-custom-allocation-enrollment.png)


4. Na **Dodaj grupę rejestracji**, kliknij przycisk **Link nowego centrum IoT hub** połączyć oba nowych oddziałów centrów iot Hub. 

    Ten krok należy wykonać zarówno z działów centra IoT Hub.

    **Subskrypcja**: Jeśli masz wiele subskrypcji, wybierz subskrypcję, w której utworzono wydzielonych centra IoT Hub.

    **Usługa IoT hub**: Wybierz jeden z działów koncentratorów, utworzone.

    **Zasady dostępu**: Wybierz **iothubowner**.

    ![Link wydzielonych centra IoT Hub z usługą aprowizacji](./media/how-to-use-custom-allocation-policies/link-divisional-hubs.png)


5. Na **Dodaj grupę rejestracji**, gdy obie wydzielonych centra IoT Hub zostały połączone, należy wybrać je jako grupy usługi IoT Hub dla grupy rejestracji jak pokazano poniżej:

    ![Utwórz grupę oddziałów Centrum dla rejestracji](./media/how-to-use-custom-allocation-policies/enrollment-divisional-hub-group.png)


6. Na **Dodaj grupę rejestracji**, przewiń w dół do **wybierz funkcję platformy Azure** sekcji, a następnie kliknij przycisk **Tworzenie nowej aplikacji funkcji**.

7. Na **aplikacji funkcji** utworzyć stronę, zostanie otwarta, wprowadź następujące ustawienia dla nowej funkcji i kliknij przycisk **Utwórz**:

    **Nazwa aplikacji**: Wprowadź unikatową nazwę aplikacji funkcji. **Contoso — funkcja aplikacji-1098** jest wyświetlana jako przykład.

    **Grupa zasobów**: Wybierz **Użyj istniejącej** i **contoso-us-resource-group** zachować wszystkie zasoby utworzone w tym artykule ze sobą.

    **Usługa Application Insights**: W tym ćwiczeniu mogą wyłączyć tę funkcję.

    ![Tworzenie aplikacji funkcji](./media/how-to-use-custom-allocation-policies/function-app-create.png)


8. Po powrocie z **Dodaj grupę rejestracji** upewnij się, ponieważ wybrano nową aplikację funkcji. Może być konieczne ponowne Wybierz subskrypcję, aby odświeżyć listę aplikacji funkcji.

    Po wybraniu nowej aplikacji funkcji kliknij **Tworzenie nowej funkcji**.

    ![Tworzenie aplikacji funkcji](./media/how-to-use-custom-allocation-policies/click-create-new-function.png)

    zostanie otwarty w nowej aplikacji funkcji.

9. W aplikacji funkcji Kliknij, aby utworzyć nową funkcję

    ![Tworzenie aplikacji funkcji](./media/how-to-use-custom-allocation-policies/new-function.png)

    Dla nowych funkcji, należy użyć ustawień domyślnych, Utwórz nową **element Webhook i interfejs API** przy użyciu **CSharp** języka. Kliknij przycisk **Utwórz tę funkcję**.

    Spowoduje to utworzenie nowej funkcji języka C# o nazwie **HttpTriggerCSharp1**.

10. Zastąp kod w nowej funkcji języka C# następującym kodem i kliknij przycisk **Zapisz**:    

    ```C#
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


11. Wróć do usługi **Dodaj grupę rejestracji** strony i upewnij się, nowa funkcja jest zaznaczone. Może być konieczne ponowne wybierz aplikację funkcji, aby odświeżyć listę funkcji.

    Po wybraniu nowej funkcji kliknij **Zapisz** można zapisać grupy rejestracji.

    ![Na koniec zapisz grupy rejestracji](./media/how-to-use-custom-allocation-policies/save-enrollment.png)


12. Po zapisaniu rejestrację, otwórz go ponownie i zanotuj **klucz podstawowy**. Musisz zapisać rejestracji, aby najpierw mają klucze generowane. Ten klucz będzie używany do generowania kluczy unikatowych urządzeń symulowanych urządzeń później.


## <a name="derive-unique-device-keys"></a>Tworzenia kluczy unikatowych urządzeń

W tej sekcji utworzysz dwa klucze unikatowe urządzenia. Jeden klucz będzie używany przez urządzenie symulowane tostera. Drugi klucz będzie używany przez urządzenie symulowane pompy ciepła.

Aby wygenerować klucz urządzenia, którego użyjesz **klucza podstawowego** zanotowanej wcześniej do obliczenia [HMAC SHA256](https://wikipedia.org/wiki/HMAC) identyfikatora rejestracji urządzeń dla każdego urządzenia i przekonwertować wynik w formacie Base64. Aby uzyskać więcej informacji na temat tworzenia kluczy pochodnych urządzenia przy użyciu grup rejestracji, zobacz sekcję rejestracji grupy [zaświadczenie klucza symetrycznego](concepts-symmetric-key-attestation.md).

Na przykład w tym artykule Użyj następujących identyfikatorów rejestracji urządzenia dwóch obliczeniowych i klucz urządzenia, dla obu urządzeń. Oba identyfikatory rejestracji ma prawidłowy sufiks do pracy z przykładowym kodem zasady alokacji niestandardowe:

- **breakroom499-contoso-tstrsd-007**
- **mainbuilding167-contoso-hpsd-088**

#### <a name="linux-workstations"></a>Stacje robocze systemu Linux

Jeśli używasz stacja robocza z systemem Linux umożliwia openssl Generowanie kluczy pochodnych urządzeń jak pokazano w poniższym przykładzie.

1. Zastąp wartość **klucz** z **klucz podstawowy** zanotowany wcześniej.

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


#### <a name="windows-based-workstations"></a>Stacje robocze z systemem Windows

Jeśli używasz stację roboczą z systemem Windows można użyć programu PowerShell do wygenerowanie własnego klucza pochodnego urządzenia, jak pokazano w poniższym przykładzie.

1. Zastąp wartość **klucz** z **klucz podstawowy** zanotowany wcześniej.

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


Symulowane urządzenia będzie używać kluczy pochodnych urządzenia przy użyciu każdego Identyfikatora rejestracji zaświadczenie klucza symetrycznego.




## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Przygotowywanie środowiska deweloperskiego dla zestawu SDK języka C usługi Azure IoT

W tej sekcji przygotujesz środowisko deweloperskie używane do opracowania [zestawu SDK języka C usługi Azure IoT](https://github.com/Azure/azure-iot-sdk-c). Zestaw SDK zawiera przykładowy kod dla symulowanego urządzenia. To urządzenie symulowane podejmie próbę aprowizacji podczas sekwencji rozruchu urządzenia.

Ta sekcja dotyczy opracowane głównie pod kątem stację roboczą z systemem Windows. Na przykład Linux, zobacz Konfigurowanie maszyn wirtualnych w [instrukcjami aprowizacji dla wielodostępności](how-to-provision-multitenant.md).

1. Pobierz [system kompilacji CMake](https://cmake.org/download/).

    Ważne jest, aby wstępnie wymagane składniki (program Visual Studio oraz pakiet roboczy „Programowanie aplikacji klasycznych w języku C++”) były zainstalowane na tym komputerze **przed** uruchomieniem `CMake` instalacji. Gdy wymagania wstępne zostaną spełnione, a pobrane pliki zweryfikowane, zainstaluj system kompilacji CMake.

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
    
    Jeśli program `cmake` nie znajdzie kompilatora języka C++, mogą występować błędy kompilacji podczas uruchamiania powyższego polecenia. Jeśli tak się stanie, spróbuj uruchomić to polecenie w [wierszu polecenia programu Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

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




## <a name="simulate-the-devices"></a>Symulowanie urządzenia

W tej sekcji spowoduje zaktualizowanie przykładu aprowizacji o nazwie **prov\_dev\_klienta\_przykładowe** znajduje się w zestawie SDK C usługi Azure IoT konfigurowania wcześniej. 

Ten przykładowy kod symuluje sekwencji rozruchu urządzenia, która spowoduje wysłanie żądania aprowizacji wystąpienia usługi Device Provisioning Service. Sekwencji rozruchu spowoduje, że urządzenie tostera do rozpoznawania i przypisane do usługi IoT hub za pomocą zasad niestandardowych alokacji.

1. W witrynie Azure Portal wybierz kartę **Przegląd** dla swojej usługi Device Provisioning Service, a następnie zapisz wartość **_Zakres identyfikatorów_**.

    ![Wyodrębnianie informacji o punkcie końcowym usługi Device Provisioning Service z bloku portalu](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. W programie Visual Studio, otwórz **azure_iot_sdks.sln** plik rozwiązania, który został wygenerowany po uruchomieniu narzędzia CMake wcześniej. Plik rozwiązania powinien znajdować się w następującej lokalizacji:

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


#### <a name="simulate-the-contoso-toaster-device"></a>Symulowanie urządzenia tostera firmy Contoso

1. Aby zasymulować urządzenie tostera, Znajdź wywołanie `prov_dev_set_symmetric_key_info()` w **prov\_dev\_klienta\_sample.c** który jest ujęty w komentarz.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Usuń znaczniki komentarza wywołania funkcji i Zastąp wartości symboli zastępczych (włącznie z nawiasami) z Identyfikatorem rejestracji tostera i klucz urządzenia pochodnej, wcześniej wygenerowania. Wartość klucza **JC8F96eayuQwwz + PkE7IzjH2lIAjCUnAa61tDigBnSs =** pokazane poniżej tylko jest podawana jako przykład.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("breakroom499-contoso-tstrsd-007", "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=");
    ```
   
    Zapisz plik.

2. Z menu programu Visual Studio wybierz pozycję **Debuguj** > **Uruchom bez debugowania**, aby uruchomić rozwiązanie. W monicie o ponowne skompilowanie projektu kliknij przycisk **Tak**, aby ponownie skompilować projekt przed uruchomieniem.

    Następujące dane wyjściowe jest przykładem urządzenia symulowanego tostera pomyślnie uruchamiania systemu oraz podłączania do aprowizacji wystąpienia usługi ma być przypisane do Centrum IoT tostery przez zasadę alokacji niestandardowe:

    ```cmd
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-toasters-hub-1098.azure-devices.net, deviceId: breakroom499-contoso-tstrsd-007

    Press enter key to exit:
    ```


#### <a name="simulate-the-contoso-heat-pump-device"></a>Symulowanie urządzenia pompy ciepła firmy Contoso

1. Zasymulować urządzenie pompy ciepła, należy zaktualizować wywołanie `prov_dev_set_symmetric_key_info()` w **prov\_dev\_klienta\_sample.c** ponownie z Identyfikatorem rejestracji pompy częstości dostępu i klucz urządzenia pochodnej wygenerowanego wcześniej . Wartość klucza **6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg =** pokazane poniżej tylko jest podawana jako przykład.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("mainbuilding167-contoso-hpsd-088", "6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=");
    ```
   
    Zapisz plik.

2. Z menu programu Visual Studio wybierz pozycję **Debuguj** > **Uruchom bez debugowania**, aby uruchomić rozwiązanie. W monicie o ponowne skompilowanie projektu kliknij przycisk **Tak**, aby ponownie skompilować projekt przed uruchomieniem.

    Następujące dane wyjściowe jest przykładem urządzenia symulowanego pompy ciepła pomyślnie uruchamiania systemu oraz podłączania do aprowizacji wystąpienia usługi ma być przypisane do Centrum IoT pompy cieplne Contoso przez zasadę alokacji niestandardowe:

    ```cmd
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-heatpumps-hub-1098.azure-devices.net, deviceId: mainbuilding167-contoso-hpsd-088

    Press enter key to exit:
    ```


## <a name="troubleshooting-custom-allocation-policies"></a>Rozwiązywanie problemów z zasad alokacji niestandardowe

W poniższej tabeli przedstawiono oczekiwane scenariusze i kody błędów wyniki, które mogą wystąpić. Ta tabela służy do rozwiązywania problemów z błędami zasad alokacji niestandardowych za pomocą usługi Azure Functions.


| Scenariusz | Wynik rejestracji z usługi Provisioning Service | Inicjowanie obsługi administracyjnej wyniki zestawu SDK |
| -------- | --------------------------------------------- | ------------------------ |
| Element webhook zwraca 200 OK "iotHubHostName" Ustaw prawidłową nazwę hosta Centrum IoT | Stan wyniku: Przypisany  | Zestaw SDK zwraca PROV_DEVICE_RESULT_OK wraz z informacjami Centrum |
| Zwraca element webhook, 200 OK "iotHubHostName" obecne w odpowiedzi, ale ustawiony na pusty ciąg lub wartość null | Stan wyniku: Niepowodzenie<br><br> Kod błędu: CustomAllocationIotHubNotSpecified (400208) | Zestaw SDK zwraca PROV_DEVICE_RESULT_HUB_NOT_SPECIFIED |
| Element webhook zwraca 401 Brak autoryzacji | Stan wyniku: Niepowodzenie<br><br>Kod błędu: CustomAllocationUnauthorizedAccess (400209) | Zestaw SDK zwraca PROV_DEVICE_RESULT_UNAUTHORIZED |
| Rejestracja indywidualna została utworzona w celu Wyłącz urządzenie | Stan wyniku: Disabled (Wyłączony) | Zestaw SDK zwraca PROV_DEVICE_RESULT_DISABLED |
| Element webhook zwraca kod błędu: > = 429 | Organizowanie DPS ponowi próbę wiele razy. Zasady ponawiania jest obecnie:<br><br>&nbsp;&nbsp;-Liczba ponownych prób: 10<br>&nbsp;&nbsp;— Interwał początkowa: 1s<br>&nbsp;&nbsp;-Przyrostu: 9S | Zestaw SDK będzie zignorować błąd i przesłać inny komunikat o stanie get w określonym czasie |
| Element webhook zwraca każdy inny kod stanu | Stan wyniku: Niepowodzenie<br><br>Kod błędu: CustomAllocationFailed (400207) | Zestaw SDK zwraca PROV_DEVICE_RESULT_DEV_AUTH_ERROR |


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli planujesz kontynuować pracę z zasobów utworzonych w tym artykule, możesz pozostawić je. Jeśli nie planujesz kontynuować korzystanie z zasobów, użyj następujące kroki, aby usunąć wszystkie zasoby utworzone w tym artykule, aby uniknąć niepotrzebnych opłat.

W tym miejscu założono w nim utworzyć wszystkie zasoby w tym artykule, zgodnie z instrukcjami w tej samej grupie zasobów o nazwie **contoso-us-resource-group**.

> [!IMPORTANT]
> Usunięcie grupy zasobów jest nieodwracalne. Grupa zasobów oraz wszystkie zawarte w niej zasoby zostaną trwale usunięte. Uważaj, aby nie usunąć przypadkowo niewłaściwych zasobów lub grupy zasobów. Jeśli usługa IoT Hub została utworzona wewnątrz istniejącej grupy zasobów zawierającej zasoby, które chcesz zachować, zamiast usuwać całą grupę zasobów, usuń tylko zasób usługi IoT Hub.
>

Aby usunąć grupę zasobów o nazwie:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i kliknij pozycję **Grupy zasobów**.

2. W **Filtruj według nazwy...**  polu tekstowym wpisz nazwę zasobu, grupy, zawierający zasoby, **contoso-us-resource-group**. 

3. Z prawej strony grupy zasobów na liście wyników kliknij pozycję **...**, a następnie kliknij pozycję **Usuń grupę zasobów**.

4. Zobaczysz prośbę o potwierdzenie usunięcia grupy zasobów. Ponownie wpisz nazwę grupy zasobów w celu potwierdzenia, a następnie kliknij pozycję **Usuń**. Po krótkim czasie grupa zasobów i wszystkie zawarte w niej zasoby zostaną usunięte.

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej Reprovisioning, zobacz [reprovisioning pojęcia dotyczące urządzeń usługi IoT Hub](concepts-device-reprovision.md) 
- Aby dowiedzieć się więcej anulowania zastrzeżenia, zobacz [jak anulować aprowizację urządzeń, które wcześniej zostały udostępnione do automatycznego](how-to-unprovision-devices.md) 











