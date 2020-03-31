---
title: Aprowizuj starsze urządzenia przy użyciu kluczy symetrycznych — usługa inicjowania obsługi administracyjnej urządzeń usługi Azure IoT Hub
description: Jak używać kluczy symetrycznych do aprowizowania starszych urządzeń za pomocą wystąpienia usługi aprowizacji urządzeń (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.openlocfilehash: 4d1a92f3ebf32d2270eb77ec9c79fe860ba090e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75434717"
---
# <a name="how-to-provision-legacy-devices-using-symmetric-keys"></a>Jak aprowizować starsze urządzenia przy użyciu kluczy symetrycznych

Częstym problemem wielu starszych urządzeń jest to, że często mają tożsamość, która składa się z jednej informacji. Te informacje o tożsamości są zwykle adresem MAC lub numerem seryjnym. Starsze urządzenia mogą nie mieć certyfikatu, modułu TPM ani żadnej innej funkcji zabezpieczeń, która może służyć do bezpiecznej identyfikacji urządzenia. Usługa inicjowania obsługi administracyjnej urządzeń dla usługi IoT hub zawiera zaświadczanie klucza symetrycznego. Zaświadczenie klucza symetrycznego może służyć do identyfikacji urządzenia na podstawie informacji, takich jak adres MAC lub numer seryjny.

Jeśli można łatwo zainstalować [sprzętowy moduł zabezpieczeń (HSM)](concepts-security.md#hardware-security-module) i certyfikat, może to być lepsze podejście do identyfikowania i inicjowania obsługi administracyjnej urządzeń. Ponieważ takie podejście może pozwolić na ominięcie aktualizacji kodu wdrożonego na wszystkich urządzeniach i nie będzie mieć klucza tajnego osadzone w obrazie urządzenia.

W tym artykule przyjęto założenie, że ani moduł HSM, ani certyfikat nie jest realną opcją. Zakłada się jednak, że masz jakąś metodę aktualizowania kodu urządzenia, aby użyć usługi inicjowania obsługi administracyjnej urządzeń do udostępniania tych urządzeń. 

W tym artykule przyjęto również założenie, że aktualizacja urządzenia odbywa się w bezpiecznym środowisku, aby zapobiec nieautoryzowanemu dostępowi do klucza grupy głównej lub pochodnego klucza urządzenia.

Ten artykuł został opracowany z myślą o stacjach roboczych z systemem Windows. Jednak opisane procedury można wykonać także w systemie Linux. Aby uzyskać przykład dla systemu Linux, zobacz [Aprowizowanie pod kątem wielu dzierżaw](how-to-provision-multitenant.md).

> [!NOTE]
> Przykład użyty w tym artykule jest napisany w języku C. Istnieje również [urządzenia języka C# inicjowania obsługi administracyjnej próbki klucza symetrycznego](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/SymmetricKeySample) dostępne. Aby użyć tego przykładu, pobierz lub sklonuj repozytorium [azure-iot-samples-csharp](https://github.com/Azure-Samples/azure-iot-samples-csharp) i postępuj zgodnie z instrukcjami w wierszu w przykładowym kodzie. Można wykonać instrukcje w tym artykule, aby utworzyć grupę rejestracji klucza symetrycznego za pomocą portalu i znaleźć zakres identyfikatora i grupy rejestracji klucze podstawowe i pomocnicze potrzebne do uruchomienia próbki. Można również utworzyć pojedyncze rejestracje przy użyciu przykładu.

## <a name="overview"></a>Omówienie

Unikatowy identyfikator rejestracji zostanie zdefiniowany dla każdego urządzenia na podstawie informacji identyfikujących to urządzenie. Na przykład adres MAC lub numer seryjny.

Grupa rejestracji, która używa [zaświadczania klucza symetrycznego,](concepts-symmetric-key-attestation.md) zostanie utworzona za pomocą usługi inicjowania obsługi administracyjnej urządzeń. Grupa rejestracji będzie zawierać klucz główny grupy. Ten klucz główny będzie używany do mieszania każdego unikatowego identyfikatora rejestracji w celu uzyskania unikatowego klucza urządzenia dla każdego urządzenia. Urządzenie użyje tego pochodnego klucza urządzenia z unikatowym identyfikatorem rejestracji, aby potwierdzić usługę inicjowania obsługi administracyjnej urządzeń i zostać przypisane do centrum IoT hub.

Kod urządzenia zademonstrowany w tym artykule będzie zgodny z tym samym wzorcem co [przewodnik Szybki start: Aprowizuj symulowane urządzenie za pomocą kluczy symetrycznych](quick-create-simulated-device-symm-key.md). Kod symuluje urządzenie przy użyciu przykładu z [SDK C usługi Azure IoT.](https://github.com/Azure/azure-iot-sdk-c) Symulowane urządzenie będzie testowane z grupą rejestracji zamiast rejestracji indywidualnej, jak pokazano w przewodniku Szybki start.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Wymagania wstępne

* Zakończenie [konfigurowania usługi inicjowania obsługi administracyjnej urządzeń w centrum IoT za pomocą przewodnika](./quick-setup-auto-provision.md) Szybki start w portalu Azure.

Następujące wymagania wstępne są dla środowiska deweloperskiego systemu Windows. W przypadku systemu Linux lub macOS zobacz odpowiednią sekcję w [przygotowaniu środowiska programistycznego](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) w dokumentacji SDK.

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 z włączonym obciążeniem ["Tworzenie pulpitu z c++".](https://docs.microsoft.com/cpp/?view=vs-2019#pivot=workloads) Obsługiwane są również program visual studio 2015 i visual studio 2017.

* Zainstalowana najnowsza wersja usługi[Git](https://git-scm.com/download/).

## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Przygotowywanie środowiska deweloperskiego dla zestawu SDK języka C usługi Azure IoT

W tej sekcji przygotujesz środowisko deweloperskie używane do opracowania [zestawu SDK języka C usługi Azure IoT](https://github.com/Azure/azure-iot-sdk-c). 

Zestaw SDK zawiera przykładowy kod symulowanego urządzenia. To urządzenie symulowane podejmie próbę aprowizacji podczas sekwencji rozruchu urządzenia.

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


## <a name="create-a-symmetric-key-enrollment-group"></a>Tworzenie grupy rejestracji klucza symetrycznego

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i otwórz wystąpienie usługi inicjowania obsługi administracyjnej urządzeń.

2. Wybierz kartę **Zarządzanie rejestracjami,** a następnie kliknij przycisk **Dodaj grupę rejestracji** u góry strony. 

3. W **programie Dodaj grupę rejestracji**wprowadź następujące informacje i kliknij przycisk **Zapisz.**

   - **Nazwa grupy**: Wprowadź **mylegacydevices**.

   - **Typ zaświadczenia**: Wybierz **klucz symetryczny**.

   - **Automatycznie generuj klucze**: zaznacz to pole wyboru.

   - **Wybierz sposób przypisywania urządzeń do koncentratorów:** Wybierz **konfigurację statyczną,** aby można było przypisać je do określonego koncentratora.

   - **Wybierz centra IoT, do które można przypisać tę grupę:** Wybierz jeden z centrów.

     ![Dodawanie grupy rejestracji dla zaświadczania klucza symetrycznego](./media/how-to-legacy-device-symm-key/symm-key-enrollment-group.png)

4. Po zapisaniu rejestracji zostanie wygenerowany **klucz podstawowy** i **klucz pomocniczy**. Zostaną one dodane do wpisu rejestracji. Grupa rejestracji klucza symetrycznego jest wyświetlana jako **urządzenia mylegacydevices** w kolumnie *Nazwa grupy* na karcie *Grupy rejestracji.* 

    Otwórz rejestrację i skopiuj wartość wygenerowanego **klucza podstawowego**. Ten klucz jest kluczem grupy głównej.


## <a name="choose-a-unique-registration-id-for-the-device"></a>Wybierz unikatowy identyfikator rejestracyjny urządzenia

Unikatowy identyfikator rejestracji musi być zdefiniowany w celu zidentyfikowania każdego urządzenia. Z urządzenia można użyć adresu MAC, numeru seryjnego lub unikatowych informacji. 

W tym przykładzie używamy kombinacji adresu MAC i numeru seryjnego tworzącego następujący ciąg dla identyfikatora rejestracji.

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

Utwórz unikatowy identyfikator rejestracyjny urządzenia. Prawidłowe znaki to małe litery alfanumeryczne i myślnik ('-').


## <a name="derive-a-device-key"></a>Wyprowadzanie klucza urządzenia 

Aby wygenerować klucz urządzenia, użyj głównego klucza grupy, aby obliczyć [identyfikator HMAC-SHA256](https://wikipedia.org/wiki/HMAC) unikatowego identyfikatora rejestracji urządzenia i przekonwertować wynik na format Base64.

Nie należy dołączać klucza głównego grupy do kodu urządzenia.


#### <a name="linux-workstations"></a>Stacje robocze Linuksa

Jeśli używasz stacji roboczej systemu Linux, można użyć openssl do wygenerowania klucza urządzenia pochodnego, jak pokazano w poniższym przykładzie.

Zastąp wartość **keya** **kluczem kluczowym odnotowanym** wcześniej.

Zastąp wartość **REG_ID** identyfikatorem rejestracyjnym.

```bash
KEY=8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw==
REG_ID=sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```


#### <a name="windows-based-workstations"></a>Stacje robocze z systemem Windows

Jeśli używasz stacji roboczej opartej na systemie Windows, można użyć programu PowerShell do wygenerowania klucza urządzenia pochodnego, jak pokazano w poniższym przykładzie.

Zastąp wartość **keya** **kluczem kluczowym odnotowanym** wcześniej.

Zastąp wartość **REG_ID** identyfikatorem rejestracyjnym.

```powershell
$KEY='8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw=='
$REG_ID='sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

```powershell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```


Urządzenie użyje pochodnego klucza urządzenia z unikatowym identyfikatorem rejestracji do wykonywania zaświadczania klucza symetrycznego z grupą rejestracji podczas inicjowania obsługi administracyjnej.



## <a name="create-a-device-image-to-provision"></a>Tworzenie obrazu urządzenia w celu aprowizowania

W tej sekcji zaktualizujesz przykładowy inicjowania obsługi administracyjnej o nazwie **prov\_dev\_klienta\_próbki** znajduje się w azure IoT C SDK, który został skonfigurowany wcześniej. 

Ten przykładowy kod symuluje sekwencję rozruchu urządzenia, która wysyła żądanie inicjowania obsługi administracyjnej do wystąpienia usługi inicjowania obsługi administracyjnej urządzenia. Sekwencja rozruchowa spowoduje, że urządzenie zostanie rozpoznane i przypisane do centrum IoT, które zostało skonfigurowane w grupie rejestracji.

1. W witrynie Azure Portal wybierz kartę **Przegląd** dla swojej usługi Device Provisioning Service, a następnie zapisz wartość **_Zakres identyfikatorów_**.

    ![Wyodrębnianie informacji o punkcie końcowym usługi Device Provisioning Service z bloku portalu](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. W programie Visual Studio otwórz plik rozwiązania **azure_iot_sdks.sln,** który został wygenerowany przez uruchomienie CMake wcześniej. Plik rozwiązania powinien znajdować się w następującej lokalizacji:

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

6. Znajdź wywołanie funkcji `prov_dev_set_symmetric_key_info()` w pliku **prov\_dev\_client\_sample.c**, które jest ujęte w komentarz.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Odkomentuj wywołanie funkcji i zastąp wartości symbolu zastępczego (w tym nawiasy kątowe) unikatowym identyfikatorem rejestracji urządzenia i wygenerowanym kluczem urządzenia pochodnym.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6", "Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=");
    ```
   
    Zapisz plik.

7. Kliknij prawym przyciskiem myszy projekt **prov\_dev\_client\_sample**, a następnie wybierz pozycję **Ustaw jako projekt startowy**. 

8. W menu Programu Visual Studio wybierz **debugowanie** > **start bez debugowania,** aby uruchomić rozwiązanie. W monicie o ponowne skompilowanie projektu kliknij przycisk **Tak**, aby ponownie skompilować projekt przed uruchomieniem.

    Następujące dane wyjściowe to przykład pomyślnego uruchomienia urządzenia symulowanego i połączenia z wystąpieniem usługi aprowizowania w celu przypisania do centrum IoT:

    ```cmd
    Provisioning API Version: 1.2.8

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: 
    test-docs-hub.azure-devices.net, deviceId: sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

    Press enter key to exit:
    ```

9. W portalu przejdź do centrum IoT hub, do na które przypisano symulowane urządzenie, i kliknij kartę **Urządzenia IoT.** Po pomyślnym zainicjowaniu obsługi administracyjnej symulowanego koncentratora jego identyfikator urządzenia jest wyświetlany na bloku **Urządzenia IoT** z *włączonym statusem* . **enabled** Być może trzeba będzie kliknąć przycisk **Odśwież** u góry strony. 

    ![Urządzenie jest rejestrowane w centrum IoT](./media/how-to-legacy-device-symm-key/hub-registration.png) 



## <a name="security-concerns"></a>Kwestie bezpieczeństwa

Należy pamiętać, że pozostawia to pochodny klucz urządzenia dołączony jako część obrazu, co nie jest zalecaną najlepszą praktyką w zakresie zabezpieczeń. Jest to jeden z powodów, dla których bezpieczeństwo i łatwość użycia są kompromisami. 





## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej Reprovisioning, zobacz [Urządzenia usługi IoT Hub reprovisioning pojęcia](concepts-device-reprovision.md) 
* [Szybki start: aprowizowanie urządzenia symulowanego przy użyciu kluczy symetrycznych](quick-create-simulated-device-symm-key.md)
* Aby dowiedzieć się więcej Deprovisioning, zobacz [Jak anulować aprovision urządzeń, które wcześniej były automatycznie aprowizacji](how-to-unprovision-devices.md) 











