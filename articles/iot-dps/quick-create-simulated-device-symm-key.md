---
title: Szybki start — używanie klucza symetrycznego do inicjowania symulowanego urządzenia do usługi Azure IoT Hub przy użyciu języka C
description: W tym przewodniku Szybki start użyjesz zestawu SDK urządzenia C do utworzenia symulowanego urządzenia, które używa klucza symetrycznego z usługą inicjowania obsługi administracyjnej urządzeń usługi Azure IoT Hub (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 01/14/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 6047051a36459d61bb5f02907dde9e73a70e86ec
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75945214"
---
# <a name="quickstart-provision-a-simulated-device-with-symmetric-keys"></a>Szybki start: aprowizowanie urządzenia symulowanego przy użyciu kluczy symetrycznych

Z tego przewodnika Szybki start dowiesz się, jak utworzyć i uruchomić symulator urządzenia na maszynie deweloperskiej z systemem Windows. To urządzenie symulowane skonfigurujesz pod kątem używania klucza symetrycznego w celu uwierzytelnienia w wystąpieniu usługi Device Provisioning Service i przypisania do centrum IoT. W celu symulowania sekwencji uruchamiania dla tego urządzenia, które inicjuje aprowizowanie, będzie używany przykładowy kod z [zestawu SDK języka C usługi Azure IoT](https://github.com/Azure/azure-iot-sdk-c). Urządzenie zostanie rozpoznane na podstawie indywidualnej rejestracji w wystąpieniu usługi aprowizowania i przypisane do centrum IoT.

Mimo że w tym artykule pokazano inicjowania obsługi administracyjnej z rejestracją indywidualną, można użyć grup rejestracji. Istnieją pewne różnice podczas korzystania z grup rejestracji. Na przykład należy użyć pochodnego klucza urządzenia z unikatowym identyfikatorem rejestracji dla urządzenia. Mimo że grupy rejestracji klucza symetrycznego nie są ograniczone do starszych urządzeń, artykuł [Aprowizowanie starszych urządzeń za pomocą zaświadczenia klucza symetrycznego](how-to-legacy-device-symm-key.md) zawiera przykład grupy rejestracji. Aby uzyskać więcej informacji, zobacz [Rejestrowanie grupy dla zaświadczania klucza symetrycznego](concepts-symmetric-key-attestation.md#group-enrollments).

Jeśli nie znasz procesu automatycznego aprowizowania, zapoznaj się z tematem [Auto-provisioning concepts (Pojęcia związane z automatycznym aprowizowaniem)](concepts-auto-provisioning.md). 

Pamiętaj również, aby przed rozpoczęciem pracy z tym przewodnikiem Szybki start wykonać kroki przedstawione w części [Konfigurowanie usługi IoT Hub Device Provisioning za pomocą witryny Azure Portal](./quick-setup-auto-provision.md). Ten przewodnik Szybki start wymaga utworzonego już wystąpienia usługi Device Provisioning Service.

Ten artykuł został opracowany z myślą o stacjach roboczych z systemem Windows. Jednak opisane procedury można wykonać także w systemie Linux. Aby uzyskać przykład dla systemu Linux, zobacz [Aprowizowanie pod kątem wielu dzierżaw](how-to-provision-multitenant.md).


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Wymagania wstępne

Następujące wymagania wstępne są dla środowiska deweloperskiego systemu Windows. W przypadku systemu Linux lub macOS zobacz odpowiednią sekcję w [przygotowaniu środowiska programistycznego](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) w dokumentacji SDK.

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 z włączonym obciążeniem ["Tworzenie pulpitu z c++".](https://docs.microsoft.com/cpp/?view=vs-2019#pivot=workloads) Obsługiwane są również program visual studio 2015 i visual studio 2017.

* Zainstalowana najnowsza wersja usługi[Git](https://git-scm.com/download/).

<a id="setupdevbox"></a>

## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>Przygotowywanie środowiska deweloperskiego dla zestawu SDK języka C usługi Azure IoT

W tej sekcji przygotujesz środowisko deweloperskie używane do opracowania [zestawu SDK języka C usługi Azure IoT](https://github.com/Azure/azure-iot-sdk-c). 

Zestaw SDK zawiera przykładowy kod dla urządzenia symulowanego. To urządzenie symulowane podejmie próbę aprowizacji podczas sekwencji rozruchu urządzenia.

1. Pobierz [system kompilacji CMake](https://cmake.org/download/).

    Ważne jest, aby wstępnie wymagane składniki (program Visual Studio oraz pakiet roboczy „Programowanie aplikacji klasycznych w języku C++”) były zainstalowane na tym komputerze **przed** uruchomieniem `CMake` instalacji. Gdy wymagania wstępne zostaną spełnione, a pobrane pliki zweryfikowane, zainstaluj system kompilacji CMake.

    Starsze wersje systemu kompilacji CMake nie można wygenerować pliku rozwiązania używanego w tym artykule. Upewnij się, że używasz nowszej wersji CMake.

2. Kliknij **pozycję Znaczniki** i znajdź nazwę tagu najnowszej wersji na [stronie Release sdk C usługi Azure IoT](https://github.com/Azure/azure-iot-sdk-c/releases/latest).

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

## <a name="create-a-device-enrollment-entry-in-the-portal"></a>Tworzenie wpisu rejestracji urządzenia w portalu

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com), wybierz przycisk **Wszystkie zasoby** w menu po lewej stronie i otwórz usługę inicjowania obsługi administracyjnej urządzeń.

2. Wybierz kartę **Zarządzanie rejestracjami,** a następnie wybierz przycisk **Dodaj rejestrację indywidualną** u góry. 

3. W panelu **Dodawanie rejestracji** wprowadź następujące informacje i naciśnij przycisk **Zapisz.**

   - **Mechanizm:** wybierz **Klucz symetryczny** jako *Mechanizm* poświadczania tożsamości.

   - **Automatyczne generowanie kluczy:** Zaznacz to pole wyboru.

   - **Identyfikator rejestracji**: wprowadź identyfikator rejestracji, aby zidentyfikować rejestrację. Użyj tylko małych znaków alfanumerycznych i kresek (-). Na przykład **symm-key-device-007**.

   - **Identyfikator urządzenia usługi IoT Hub:** wprowadź identyfikator urządzenia. Na przykład **device-007**.

     ![Dodawanie indywidualnej rejestracji dla zaświadczenia klucza symetrycznego w portalu](./media/quick-create-simulated-device-symm-key/create-individual-enrollment.png)

4. Po zapisaniu rejestracji **klucz podstawowy** i **klucz pomocniczy** zostaną wygenerowane i dodane do wpisu rejestracji. Rejestracja urządzenia klucza symetrycznego jest wyświetlana jako **symm-key-device-007** w kolumnie *Identyfikator rejestracji* na karcie *Indywidualne rejestracje*. 

    Otwórz rejestrację i skopiuj wartość wygenerowanego **klucza podstawowego**.



<a id="firstbootsequence"></a>

## <a name="simulate-first-boot-sequence-for-the-device"></a>Symulowanie sekwencji pierwszego uruchamiania dla urządzenia

W tej sekcji zaktualizujesz kod przykładowy w celu wysłania sekwencji uruchamiania urządzenia do wystąpienia usługi Device Provisioning Service. Ta sekwencja uruchamiania spowoduje, że urządzenie zostanie rozpoznane i przypisane do centrum IoT Hub połączonego z wystąpieniem usługi Device Provisioning Service.



1. W witrynie Azure portal wybierz kartę **Przegląd** usługi inicjowania obsługi administracyjnej urządzeń i zanotuj wartość **_zakresu identyfikatorów._**

    ![Wyodrębnianie informacji o punkcie końcowym usługi Device Provisioning Service z bloku portalu](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. W programie Visual Studio otwórz plik rozwiązania **azure_iot_sdks.sln**, który został wygenerowany przez uruchomienie narzędzia CMake. Plik rozwiązania powinien znajdować się w następującej lokalizacji:

    ```
    \azure-iot-sdk-c\cmake\azure_iot_sdks.sln
    ```

    Jeśli plik nie został wygenerowany w katalogu cmake, upewnij się, że użyto najnowszej wersji systemu kompilacji CMake.

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

    Usuń znaczniki komentarza dla tego wywołania funkcji i zastąp wartości symboli zastępczych (włącznie z nawiasami ostrymi) wartościami identyfikatora rejestracji i klucza podstawowego.

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("symm-key-device-007", "your primary key here");
    ```
   
    Zapisz plik.

7. Kliknij prawym przyciskiem myszy projekt **prov\_dev\_client\_sample**, a następnie wybierz pozycję **Ustaw jako projekt startowy**. 

8. W menu Programu Visual Studio wybierz **debugowanie** > **start bez debugowania,** aby uruchomić rozwiązanie. W wierszu o przebudowie projektu wybierz opcję **Tak**, aby odbudować projekt przed uruchomieniem.

    Następujące dane wyjściowe to przykład pomyślnego uruchomienia urządzenia symulowanego i połączenia z wystąpieniem usługi aprowizowania w celu przypisania do centrum IoT:

    ```cmd
    Provisioning API Version: 1.2.8

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: 
    test-docs-hub.azure-devices.net, deviceId: device-007    
    Press enter key to exit:
    ```

9. W portalu przejdź do centrum IoT hub, do na które przypisano symulowane urządzenie, i wybierz kartę **Urządzenia IoT.** Po pomyślnym zainicjowaniu obsługi administracyjnej symulowanego koncentratora jego identyfikator urządzenia jest wyświetlany na bloku **Urządzenia IoT** z *włączonym statusem* . **enabled** Może być konieczne naciśnięcie przycisku **Odśwież** u góry. 

    ![Urządzenie jest rejestrowane w centrum IoT](./media/quick-create-simulated-device-symm-key/hub-registration.png) 


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli planujesz kontynuować pracę i eksplorowanie przykładu klienta urządzenia, nie czyścić zasobów utworzonych w tym przewodniku Szybki start. Jeśli nie zamierzasz kontynuować, wykonaj następujące kroki, aby usunąć wszystkie zasoby utworzone przez ten przewodnik Szybki start.

1. Zamknij okno danych wyjściowych przykładu klienta urządzenia na swojej maszynie.
1. Z menu po lewej stronie w witrynie Azure portal wybierz **pozycję Wszystkie zasoby,** a następnie wybierz usługę inicjowania obsługi urządzeń. Otwórz **okno Zarządzaj rejestracjami** dla usługi, a następnie wybierz kartę **Rejestracje indywidualne.** Zaznacz pole wyboru obok *identyfikatora REJESTRACJI* urządzenia zarejestrowanego w tym przewodniku Szybki start, a następnie naciśnij przycisk **Usuń** u góry okienka. 
1. Z menu po lewej stronie w witrynie Azure portal wybierz **pozycję Wszystkie zasoby,** a następnie wybierz centrum IoT Hub. Otwórz **urządzenia IoT** dla centrum, zaznacz pole wyboru obok *identyfikatora URZĄDZENIA* urządzenia zarejestrowanego w tym przewodniku Szybki start, a następnie naciśnij przycisk **Usuń** u góry okienka.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono symulowane urządzenie na komputerze z systemem Windows i zainicjowano go do centrum IoT przy użyciu klucza Symetrycznego za pomocą usługi inicjowania obsługi administracyjnej usługi Azure IoT Hub w portalu. Aby dowiedzieć się, jak zarejestrować urządzenie programowo, przejdź do szybkiego startu w celu uzyskania programowej rejestracji urządzeń X.509. 

> [!div class="nextstepaction"]
> [Szybki start platformy Azure — rejestrowanie urządzeń X.509 w usłudze inicjowania obsługi administracyjnej usługi Azure IoT Hub](quick-enroll-device-x509-java.md)
