---
title: Aprowizuj symulowane urządzenie X.509 do usługi Azure IoT Hub przy użyciu języka C
description: W tym przewodniku Szybki start używane są rejestracje indywidualne. W tym przewodniku Szybki start utworzysz i aprowizujesz symulowane urządzenie X.509 przy użyciu zestawu SDK urządzenia C dla usługi azure IoT Hub Device Provisioning Service (DPS).
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: f0c95e495e222cc72f0a6fc432404fcbaa47df65
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241164"
---
# <a name="quickstart-provision-an-x509-simulated-device-using-the-azure-iot-c-sdk"></a>Szybki start: aprowizowanie symulowanego urządzenia X.509 za pomocą zestawu SDK języka C usługi Azure IoT

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

Z tego przewodnika Szybki start dowiesz się, jak utworzyć i uruchomić symulator urządzenia X.509 na maszynie deweloperskiej z systemem Windows. To symulowane urządzenie skonfigurujesz tak, aby przypisać je do centrum IoT Hub za pomocą rejestracji w wystąpieniu usługi Device Provisioning Service. W celu symulowania sekwencji uruchamiania dla tego urządzenia będzie używany przykładowy kod z [zestawu SDK języka C usługi Azure IoT](https://github.com/Azure/azure-iot-sdk-c). Urządzenie zostanie rozpoznane na podstawie rejestracji w usłudze Provisioning Service i przypisane do centrum IoT Hub.

Jeśli nie znasz procesu automatycznego obsługi administracyjnej, zapoznaj się z [pojęciami autoobceptowania.](concepts-auto-provisioning.md) Pamiętaj również, aby przed rozpoczęciem pracy z tym przewodnikiem Szybki start wykonać kroki przedstawione w części [Konfigurowanie usługi IoT Hub Device Provisioning za pomocą witryny Azure Portal](quick-setup-auto-provision.md). 

Usługa Azure IoT Device Provisioning obsługuje dwa typy rejestracji:

* [Grupy rejestracji](concepts-service.md#enrollment-group): służą do rejestrowania wielu pokrewnych urządzeń.
* [Rejestracje indywidualne](concepts-service.md#individual-enrollment): służą do rejestrowania pojedynczych urządzeń.

W tym artykule przedstawiono rejestracje indywidualne.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Następujące wymagania wstępne są dla środowiska deweloperskiego systemu Windows. W przypadku systemu Linux lub macOS zobacz odpowiednią sekcję w [przygotowaniu środowiska programistycznego](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) w dokumentacji SDK.

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 z włączonym obciążeniem ["Tworzenie pulpitu z c++".](https://docs.microsoft.com/cpp/?view=vs-2019#pivot=workloads) Obsługiwane są również program visual studio 2015 i visual studio 2017.

* Zainstalowana najnowsza wersja usługi[Git](https://git-scm.com/download/).

## <a name="prepare-a-development-environment-for-the-azure-iot-c-sdk"></a>Przygotowywanie środowiska deweloperskiego dla zestawu SDK języka C usługi Azure IoT

W tej sekcji przygotujesz środowisko programistyczne używane do tworzenia [zestawu SDK C usługi Azure IoT,](https://github.com/Azure/azure-iot-sdk-c)który zawiera przykładowy kod sekwencji rozruchowej X.509.

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

5. Przykładowy kod używa certyfikatu X.509 w celu dostarczenia poświadczeń za pośrednictwem uwierzytelniania X.509. Uruchom następujące polecenie, aby utworzyć wersję zestawu SDK specyficzną dla platformy deweloperskiej, która zawiera klienta inicjowania obsługi administracyjnej urządzenia. Rozwiązanie programu Visual Studio dla symulowanego `cmake` urządzenia jest generowane w katalogu.

    ```cmd
    cmake -Duse_prov_client:BOOL=ON ..
    ```

    Jeśli program `cmake` nie znajdzie kompilatora języka C++, mogą występować błędy kompilacji podczas uruchamiania powyższego polecenia. Jeśli tak się stanie, spróbuj uruchomić to polecenie w [wierszu polecenia programu Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

    Gdy kompilacja powiedzie się, kilka ostatnich linii wyjściowych wyglądają podobnie do następujących danych wyjściowych:

    ```cmd/sh
    $ cmake -Duse_prov_client:BOOL=ON ..
    -- Building for: Visual Studio 16 2019
    -- The C compiler identification is MSVC 19.23.28107.0
    -- The CXX compiler identification is MSVC 19.23.28107.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: C:/code/azure-iot-sdk-c/cmake
    ```

## <a name="create-a-self-signed-x509-device-certificate"></a>Tworzenie certyfikatu urządzenia X.509 z podpisem własnym

W tej sekcji zostanie użyty certyfikat z podpisem własnym X.509. Ważne jest, aby pamiętać o następujących kwestiach:

* Certyfikaty z podpisem własnym są przeznaczone tylko do celów testowania i nie powinny być używane w środowisku produkcyjnym.
* Domyślny termin wygaśnięcia certyfikatu z podpisem własnym to jeden rok.

Przykładowy kod z zestawu SDK języka C platformy usługi Azure IoT zostanie użyty do utworzenia certyfikatu, który będzie używany z indywidualnym wpisem rejestracji dla urządzenia symulowanego.

1. Uruchom program Visual Studio i otwórz nowy plik rozwiązania o nazwie `azure_iot_sdks.sln`. Ten plik rozwiązania znajduje się w folderze `cmake` utworzonym wcześniej w katalogu głównym repozytorium Git azure-iot-sdk-c.

2. W menu Programu Visual Studio wybierz pozycję **Buduj** > **rozwiązanie kompilacji,** aby utworzyć wszystkie projekty w rozwiązaniu.

3. W oknie programu Visual Studio *Eksplorator rozwiązań* przejdź do folderu **Provision\_Tools**. Kliknij prawym przyciskiem myszy projekt **dice\_device\_enrollment** i wybierz pozycję **Ustaw jako projekt startowy**.

4. W menu Programu Visual Studio wybierz **debugowanie** > **start bez debugowania,** aby uruchomić rozwiązanie. W oknie danych wyjściowych po wyświetleniu monitu wprowadź wartość **i**, aby przeprowadzić rejestrację indywidualną.

    W oknie danych wyjściowych zostanie wyświetlony lokalnie wygenerowany certyfikat X.509 z podpisem własnym dla symulowanego urządzenia. Skopiuj do schowka dane wyjściowe od wiersza **-----BEGIN CERTIFICATE-----** do pierwszego wiersza **-----END CERTIFICATE-----**, włącznie z tymi wierszami. Będziesz potrzebować tylko pierwszego certyfikatu z okna danych wyjściowych.

5. Za pomocą edytora tekstów zapisz certyfikat w nowym pliku o nazwie **_X509testcert.pem_**.

## <a name="create-a-device-enrollment-entry-in-the-portal"></a>Tworzenie wpisu rejestracji urządzenia w portalu

1. Zaloguj się do witryny Azure portal, wybierz przycisk **Wszystkie zasoby** w menu po lewej stronie i otwórz usługę inicjowania obsługi administracyjnej urządzeń.

2. Wybierz kartę **Zarządzanie rejestracjami,** a następnie wybierz przycisk **Dodaj rejestrację indywidualną** u góry.

3. W panelu **Dodawanie rejestracji** wprowadź następujące informacje, a następnie naciśnij przycisk **Zapisz.**

    * **Mechanizm:** wybierz **X.509** jako *Mechanizm * poświadczania tożsamości.
    * **Podstawowy certyfikat .pem lub plik cer:** Wybierz **wybierz plik,** aby wybrać plik certyfikatu X509testcert.pem, utworzony wcześniej.
    * **Identyfikator urządzenia usługi IoT Hub:** wprowadź ciąg **test-docs-cert-device**, aby nadać urządzeniu identyfikator.

      [![Dodawanie indywidualnej rejestracji dla zaświadczenia X.509 w portalu](./media/quick-create-simulated-device-x509/device-enrollment.png)](./media/quick-create-simulated-device-x509/device-enrollment.png#lightbox)

      Po pomyślnej rejestracji urządzenie X.509 jest wyświetlane jako **riot-device-cert** w kolumnie *Identyfikator rejestracji* na karcie *Indywidualne rejestracje*. 

## <a name="simulate-first-boot-sequence-for-the-device"></a>Symulowanie sekwencji pierwszego uruchamiania dla urządzenia

W tej sekcji zaktualizujesz kod przykładowy w celu wysłania sekwencji uruchamiania urządzenia do wystąpienia usługi Device Provisioning Service. Ta sekwencja uruchamiania spowoduje, że urządzenie zostanie rozpoznane i przypisane do centrum IoT Hub połączonego z wystąpieniem usługi Device Provisioning Service.

1. W witrynie Azure portal wybierz kartę **Przegląd** usługi inicjowania obsługi administracyjnej urządzeń i zanotuj wartość **_zakresu identyfikatorów._**

    ![Wyodrębnianie informacji o punkcie końcowym usługi Device Provisioning Service z bloku portalu](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. W oknie *Eksplorator rozwiązań* programu Visual Studio przejdź do folderu **Provision\_Samples**. Rozwiń przykładowy projekt o nazwie **prov\_dev\_client\_sample**. Rozwiń pozycję **Pliki źródłowe**, a następnie otwórz plik **prov\_dev\_client\_sample.c**.

3. Znajdź stałą `id_scope` i zastąp jej wartość wcześniej skopiowaną wartością **Zakres identyfikatorów**. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

4. Znajdź definicję funkcji `main()` w tym samym pliku. Upewnij się, że zmienna `hsm_type` jest ustawiona na `SECURE_DEVICE_TYPE_X509`, a nie `SECURE_DEVICE_TYPE_TPM`, jak pokazano poniżej.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

5. Kliknij prawym przyciskiem myszy projekt **prov\_dev\_client\_sample**, a następnie wybierz pozycję **Ustaw jako projekt startowy**.

6. W menu Programu Visual Studio wybierz **debugowanie** > **start bez debugowania,** aby uruchomić rozwiązanie. W wierszu monitu o odbudowanie projektu wybierz opcję **Tak,** aby odbudować projekt przed uruchomieniem.

    Następujące dane wyjściowe to przykład pomyślnego uruchomienia aprowizowanego urządzenia klienta i połączenia z wystąpieniem usługi Provisioning Service w celu uzyskania informacji na temat centrum IoT Hub oraz rejestracji:

    ```cmd
    Provisioning API Version: 1.2.7

    Registering... Press enter key to interrupt.

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service:
    test-docs-hub.azure-devices.net, deviceId: test-docs-cert-device
    ```

7. W portalu przejdź do centrum IoT hub połączone z usługą inicjowania obsługi administracyjnej i wybierz kartę **Urządzenia IoT.** Po pomyślnym zainicjowaniu obsługi administracyjnej symulowanego urządzenia X.509 do koncentratora jego identyfikator urządzenia pojawia się na bloku **urządzeń IoT** z **włączonym** *statusem* . Może być konieczne naciśnięcie przycisku **Odśwież** u góry. 

    ![Urządzenie jest rejestrowane w centrum IoT](./media/quick-create-simulated-device/hub-registration.png) 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli planujesz kontynuować pracę i eksplorowanie przykładu klienta urządzenia, nie czyścić zasobów utworzonych w tym przewodniku Szybki start. Jeśli nie zamierzasz kontynuować, wykonaj następujące kroki, aby usunąć wszystkie zasoby utworzone przez ten przewodnik Szybki start.

1. Zamknij okno danych wyjściowych przykładu klienta urządzenia na swojej maszynie.
1. Z menu po lewej stronie w witrynie Azure portal wybierz **pozycję Wszystkie zasoby,** a następnie wybierz usługę inicjowania obsługi urządzeń. Otwórz **okno Zarządzaj rejestracjami** dla usługi, a następnie wybierz kartę **Rejestracje indywidualne.** Zaznacz pole wyboru obok *identyfikatora REJESTRACJI* urządzenia zarejestrowanego w tym przewodniku Szybki start, a następnie naciśnij przycisk **Usuń** u góry okienka. 
1. Z menu po lewej stronie w witrynie Azure portal wybierz **pozycję Wszystkie zasoby,** a następnie wybierz centrum IoT Hub. Otwórz **urządzenia IoT** dla centrum, zaznacz pole wyboru obok *identyfikatora URZĄDZENIA* urządzenia zarejestrowanego w tym przewodniku Szybki start, a następnie naciśnij przycisk **Usuń** u góry okienka.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono symulowane urządzenie X.509 na komputerze z systemem Windows i udostępnino go do centrum IoT hub przy użyciu usługi inicjowania obsługi administracyjnej usługi Azure IoT Hub w portalu. Aby dowiedzieć się, jak zarejestrować urządzenie X.509 programowo, przejdź do szybkiego startu w celu uzyskania programowej rejestracji urządzeń X.509. 

> [!div class="nextstepaction"]
> [Szybki start platformy Azure — rejestrowanie urządzeń X.509 w usłudze inicjowania obsługi administracyjnej usługi Azure IoT Hub](quick-enroll-device-x509-java.md)
