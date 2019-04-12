---
title: Jak aprowizować starsze urządzenia z usługi Azure IoT Hub Device Provisioning za pomocą kluczy symetrycznych | Dokumentacja firmy Microsoft
description: Jak aprowizować starsze urządzenia z urządzeniem, wystąpienie usługi aprowizacji za pomocą kluczy symetrycznych
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.openlocfilehash: 248c7977752eaec86121a0dd197e5bff2621ead5
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/11/2019
ms.locfileid: "59500281"
---
# <a name="how-to-provision-legacy-devices-using-symmetric-keys"></a>Jak wykonać aprowizację starsze urządzenia przy użyciu kluczy symetrycznych


Powszechny problem z wielu starszych urządzeń jest często mają tożsamość, która składa się z jednego fragmentu informacji. Informacje o tożsamości jest zwykle adres MAC lub numeru seryjnego. Starsze urządzenia może nie mieć certyfikat, modułu TPM lub innych funkcja zabezpieczeń, który może służyć do bezpiecznego identyfikowania urządzenia. Usługi Device Provisioning dla Centrum IoT hub zawiera zaświadczenie klucza symetrycznego. Zaświadczenie klucza symetrycznego może służyć do identyfikowania urządzenia na podstawie informacje, takie jak adres MAC lub numeru seryjnego.

Jeśli możesz łatwo zainstalować [sprzętowego modułu zabezpieczeń (HSM)](concepts-security.md#hardware-security-module) i certyfikat, a następnie, może być lepszym rozwiązaniem w identyfikacji i aprowizacji urządzeń. Ponieważ takie podejście może umożliwić obejścia aktualizowania kodu wdrożony do wszystkich urządzeń, a nie musiałby klucz tajny, osadzonego w obrazie urządzenia.

W tym artykule założono, ani modułu HSM lub certyfikatu to rentowną opcją. Jednak zakłada się, że masz niektóre metody aktualizacji kodu urządzenia na potrzeby udostępniania tych urządzeń do usługi Device Provisioning. 

W tym artykule założono również, że aktualizacja urządzenia ma miejsce w bezpiecznym środowisku w celu uniemożliwienia nieupoważnionego dostępu do klucza głównego grupy lub klucza pochodnego urządzenia.

Ten artykuł został opracowany z myślą o stacjach roboczych z systemem Windows. Jednak opisane procedury można wykonać także w systemie Linux. Aby uzyskać przykład dla systemu Linux, zobacz [Aprowizowanie pod kątem wielu dzierżaw](how-to-provision-multitenant.md).


## <a name="overview"></a>Omówienie

Identyfikator unikatowy rejestracji zostanie zdefiniowana dla każdego urządzenia, w oparciu o informacje umożliwiające identyfikację tego urządzenia. Na przykład adres MAC lub numeru seryjnego.

Grupy rejestracji, który używa [zaświadczenie klucza symetrycznego](concepts-symmetric-key-attestation.md) zostanie utworzony przy użyciu usługi Device Provisioning Service. Klucz główny grupy będzie znajdować się w grupie rejestracji. Klucza głównego będzie służyć do tworzenia skrótu każdy identyfikator rejestracji, aby wygenerować klucz urządzenia unikatowy dla każdego urządzenia. Urządzenie będzie używało tego klucza pochodnego urządzenia za pomocą jego Identyfikatora rejestracji zaświadczania z usługą Device Provisioning i można przypisać do usługi IoT hub.

Kod urządzenia, które przedstawiono w tym artykule będzie zgodna z tego samego wzorca jako [Szybki Start: Aprowizowanie symulowanego urządzenia przy użyciu kluczy symetrycznych](quick-create-simulated-device-symm-key.md). Ten kod będzie symulowania urządzenia przy użyciu przykładu z [zestawu SDK C usługi IoT Azure](https://github.com/Azure/azure-iot-sdk-c). Potwierdza symulowanego urządzenia z grupy rejestracji zamiast rejestrację indywidualną, jak pokazano w przewodniku Szybki Start.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Wymagania wstępne

* Ukończenie [skonfigurować IoT Hub Device Provisioning Service przy użyciu witryny Azure portal](./quick-setup-auto-provision.md) Szybki Start.
* Program Visual Studio 2015 lub [Visual Studio 2017](https://www.visualstudio.com/vs/) z włączonym pakietem roboczym [„Programowanie aplikacji klasycznych w języku C++”](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/).
* Zainstalowana najnowsza wersja usługi[Git](https://git-scm.com/download/).


## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Przygotowywanie środowiska deweloperskiego dla zestawu SDK języka C usługi Azure IoT

W tej sekcji przygotujesz środowisko deweloperskie używane do opracowania [zestawu SDK języka C usługi Azure IoT](https://github.com/Azure/azure-iot-sdk-c). 

Zestaw SDK zawiera przykładowy kod dla symulowanego urządzenia. To urządzenie symulowane podejmie próbę aprowizacji podczas sekwencji rozruchu urządzenia.

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


## <a name="create-a-symmetric-key-enrollment-group"></a>Utwórz grupę rejestracji klucza symetrycznego

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com), a następnie otwórz swoje wystąpienie usługi Device Provisioning.

2. Wybierz **Zarządzanie rejestracjami** kartę, a następnie kliknij przycisk **Dodaj grupę rejestracji** znajdujący się u góry strony. 

3. Na **Dodaj grupę rejestracji**, wprowadź następujące informacje i kliknij przycisk **Zapisz** przycisku.

   - **Nazwa grupy**: Wprowadź **mylegacydevices**.

   - **Typ zaświadczeń**: Wybierz **klucz symetryczny**.

   - **Automatycznie generuj klucze**: zaznacz to pole wyboru.

   - **Wybierz sposób przypisywania urządzeń do centrów**: Wybierz **statycznie** można przypisać do określonego koncentratora.

   - **Wybierz centra IoT Hub do tej grupy mogą być przypisane do**: Wybierz jeden z koncentratorami.

     ![Dodaj grupę rejestracji dla zaświadczenia klucza symetrycznego](./media/how-to-legacy-device-symm-key/symm-key-enrollment-group.png)

4. Po zapisaniu rejestracji zostanie wygenerowany **klucz podstawowy** i **klucz pomocniczy**. Zostaną one dodane do wpisu rejestracji. Grupa symetrycznego klucza rejestracji jest wyświetlana jako **mylegacydevices** w obszarze *Nazwa grupy* kolumny w *grup rejestracji* kartę. 

    Otwórz rejestrację i skopiuj wartość wygenerowanego **klucza podstawowego**. Ten klucz jest kluczem głównym grupy.


## <a name="choose-a-unique-registration-id-for-the-device"></a>Wybierz identyfikator rejestracji dla urządzenia

Identyfikator rejestracji musi być zdefiniowany do identyfikowania poszczególnych urządzeń. Można użyć adresu MAC, numer seryjny lub żadnych unikatowych informacji z urządzenia. 

W tym przykładzie używamy kombinację adresu MAC i numer seryjny tworzących następujący ciąg identyfikatora rejestracji.

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

Utwórz identyfikator rejestracji dla urządzenia. Prawidłowe znaki to małe znaki alfanumeryczne i kreski ("-").


## <a name="derive-a-device-key"></a>Uzyskania klucza urządzenia 

Aby wygenerować klucz urządzenia, należy użyć klucza głównego grupy do obliczenia [HMAC SHA256](https://wikipedia.org/wiki/HMAC) identyfikatora rejestracji dla urządzenia i przekonwertować wynik w formacie Base64.

Nie dołączaj klucza głównego usługi grupy w kodzie urządzenia.


#### <a name="linux-workstations"></a>Stacje robocze systemu Linux

Jeśli używasz stacja robocza z systemem Linux umożliwia openssl wygenerowanie własnego klucza pochodnego urządzenia, jak pokazano w poniższym przykładzie.

Zastąp wartość **klucz** z **klucz podstawowy** zanotowany wcześniej.

Zastąp wartość **REG_ID** przy użyciu swojego identyfikatora rejestracji.

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

Jeśli używasz stację roboczą z systemem Windows można użyć programu PowerShell do wygenerowanie własnego klucza pochodnego urządzenia, jak pokazano w poniższym przykładzie.

Zastąp wartość **klucz** z **klucz podstawowy** zanotowany wcześniej.

Zastąp wartość **REG_ID** przy użyciu swojego identyfikatora rejestracji.

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


Urządzenie będzie używać klucza pochodnego urządzenia za pomocą Identyfikatora rejestracji symetrycznego zaświadczenie klucza przy użyciu grup rejestracji podczas inicjowania obsługi.



## <a name="create-a-device-image-to-provision"></a>Tworzenie obrazu urządzenia w celu aprowizacji

W tej sekcji spowoduje zaktualizowanie przykładu aprowizacji o nazwie **prov\_dev\_klienta\_przykładowe** znajduje się w zestawie SDK C usługi Azure IoT konfigurowania wcześniej. 

Ten przykładowy kod symuluje sekwencji rozruchu urządzenia, która spowoduje wysłanie żądania aprowizacji wystąpienia usługi Device Provisioning Service. Sekwencji rozruchu spowoduje, że urządzenie zostało rozpoznane i przypisane do Centrum IoT, które zostały skonfigurowane w grupie rejestracji.

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

6. Znajdź wywołanie funkcji `prov_dev_set_symmetric_key_info()` w pliku **prov\_dev\_client\_sample.c**, które jest ujęte w komentarz.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Usuń znaczniki komentarza wywołania funkcji i Zastąp wartości symboli zastępczych (włącznie z nawiasami) z Identyfikatorem rejestracji dla urządzenia i klucz pochodnej urządzenia, który został wygenerowany.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6", "Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=");
    ```
   
    Zapisz plik.

7. Kliknij prawym przyciskiem myszy projekt **prov\_dev\_client\_sample**, a następnie wybierz pozycję **Ustaw jako projekt startowy**. 

8. Z menu programu Visual Studio wybierz pozycję **Debuguj** > **Uruchom bez debugowania**, aby uruchomić rozwiązanie. W monicie o ponowne skompilowanie projektu kliknij przycisk **Tak**, aby ponownie skompilować projekt przed uruchomieniem.

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

9. W portalu przejdź do centrum IoT, do którego przypisano Twoje urządzenie symulowane, a następnie kliknij kartę **Urządzenia IoT**. Po pomyślnej aprowizacji urządzenia symulowanego w centrum identyfikator urządzenia jest wyświetlany w bloku **Urządzenia IoT** z pozycją *STATUS* (stan) ustawioną na wartość **enabled** (włączone). Być może trzeba będzie kliknąć przycisk **Odśwież** u góry strony. 

    ![Urządzenie jest rejestrowane w centrum IoT](./media/how-to-legacy-device-symm-key/hub-registration.png) 



## <a name="security-concerns"></a>Kwestie dotyczące bezpieczeństwa

Należy pamiętać, że spowoduje to pozostawienie klucza pochodnego urządzenia dołączone jako część obrazu, który nie jest najlepszą zalecaną praktyką zabezpieczeń. To jest jednym z powodów Dlaczego bezpieczeństwo i łatwość użytkowania są wady i zalety. 





## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej Reprovisioning, zobacz [reprovisioning pojęcia dotyczące urządzeń usługi IoT Hub](concepts-device-reprovision.md) 
* [Szybki start: Aprowizowanie urządzenia symulowanego przy użyciu kluczy symetrycznych](quick-create-simulated-device-symm-key.md)
* Aby dowiedzieć się więcej anulowania zastrzeżenia, zobacz [jak anulować aprowizację urządzeń, które wcześniej zostały udostępnione do automatycznego](how-to-unprovision-devices.md) 











