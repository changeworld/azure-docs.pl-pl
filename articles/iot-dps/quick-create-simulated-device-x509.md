---
title: Aprowizowanie symulowanego urządzenia X.509 dla usługi Azure IoT Hub przy użyciu języka C | Microsoft Docs
description: Przewodnik Szybki start platformy Azure — tworzenie i aprowizowanie symulowanego urządzenia X.509 za pomocą zestawu SDK języka C dla usługi Azure IoT Hub Device Provisioning
services: iot-dps
keywords: ''
author: dsk-2015
ms.author: dkshir
ms.date: 04/16/2018
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: e5fe9282dd10bd6bdc41c63718a884a92da4d7c6
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="create-and-provision-an-x509-simulated-device-using-c-device-sdk-for-iot-hub-device-provisioning-service"></a>Tworzenie i aprowizowanie symulowanego urządzenia X.509 za pomocą zestawu SDK języka C dla usługi IoT Hub Device Provisioning
[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

Te kroki pokazują, jak zasymulować urządzenie X.509 na maszynie deweloperskiej z systemem operacyjnym Windows OS i użyć przykładowego kodu do połączenia tego symulowanego urządzenia z usługą Device Provisioning Service i Twoim centrum IoT Hub. 

Jeśli nie znasz procesu automatycznego aprowizowania, zapoznaj się również z tematem [Auto-provisioning concepts](concepts-auto-provisioning.md) (Pojęcia związane z automatycznym aprowizowaniem). Pamiętaj również, aby wcześniej wykonać kroki przedstawione w części [Konfigurowanie usługi IoT Hub Device Provisioning za pomocą witryny Azure Portal](./quick-setup-auto-provision.md). 

[!INCLUDE [IoT DPS basic](../../includes/iot-dps-basic.md)]

<a id="setupdevbox"></a>

## <a name="prepare-the-development-environment"></a>Przygotowywanie środowiska deweloperskiego 

1. Upewnij się, że masz na swojej maszynie zainstalowany program Visual Studio 2015 lub [Visual Studio 2017](https://www.visualstudio.com/vs/). Musisz mieć włączony pakiet roboczy [„Programowanie aplikacji klasycznych w języku C++”](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) dla instalacji programu Visual Studio.

2. Pobierz i zainstaluj [system kompilacji CMake](https://cmake.org/download/). Pamiętaj, że program Visual Studio z pakietem roboczym „Programowanie aplikacji klasycznych w języku C++” powinien być zainstalowany na komputerze **przed** zainstalowaniem programu `cmake`. 

3. Upewnij się, że na swojej maszynie masz zainstalowane oprogramowanie `git` i że jest ono dodane do zmiennych środowiskowych dostępnych z okna poleceń. Zobacz stronę z [narzędziami klienckimi Git organizacji Software Freedom Conservancy](https://git-scm.com/download/), aby uzyskać najnowszą wersję narzędzi `git` do zainstalowania, które obejmują powłokę **Git Bash**, czyli aplikację wiersza polecenia, która może służyć do interakcji z lokalnym repozytorium Git. 

4. Otwórz wiersz polecenia lub powłokę Git Bash. Sklonuj repozytorium GitHub dla przykładu kodu symulacji urządzenia:
    
    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```

5. Utwórz folder w swojej lokalnej kopii tego repozytorium GitHub dla procesu kompilacji CMake. 

    ```cmd
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

6. Przykładowy kod używa certyfikatu X.509 w celu dostarczenia poświadczeń za pośrednictwem uwierzytelniania X.509. Uruchom poniższe polecenie, aby skompilować wersję zestawu SDK specyficzną dla platformy klienta deweloperskiego i [mechanizmu zaświadczania](concepts-security.md#attestation-mechanism) (certyfikat X.509). Polecenie to generuje także rozwiązanie programu Visual Studio dla symulowanego urządzenia. 

    ```cmd
    cmake -Duse_prov_client:BOOL=ON ..
    ```
    
    Jeśli program `cmake` nie znajdzie kompilatora języka C++, mogą występować błędy kompilacji podczas uruchamiania powyższego polecenia. Jeśli tak się stanie, spróbuj uruchomić to polecenie w [wierszu polecenia programu Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 


<a id="portalenroll"></a>

## <a name="create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry"></a>Tworzenie certyfikatu urządzenia X.509 z podpisem własnym i wpisu rejestracji indywidualnej

1. Otwórz rozwiązanie wygenerowane w folderze programu *cmake* o nazwie `azure_iot_sdks.sln` i skompiluj je w programie Visual Studio.

2. Kliknij prawym przyciskiem myszy projekt **dice\_device\_enrollment** w folderze **Provision\_Tools**, a następnie wybierz pozycję **Ustaw jako projekt startowy**. Uruchom rozwiązanie. W oknie danych wyjściowych po wyświetleniu monitu wprowadź wartość **i**, aby przeprowadzić rejestrację indywidualną. W oknie danych wyjściowych zostanie wyświetlony lokalnie wygenerowany certyfikat X.509 dla symulowanego urządzenia. Skopiuj do schowka dane wyjściowe rozpoczynające się od wiersza *-----BEGIN CERTIFICATE-----* i kończące się pierwszym wierszem *-----END CERTIFICATE-----*, a następnie upewnij się, że oba te wiersze również zostały skopiowane. Należy pamiętać, że wymagany jest tylko pierwszy certyfikat z okna danych wyjściowych.
 
3. Utwórz plik o nazwie **_X509testcert.pem_** na maszynie z systemem Windows, otwórz go w wybranym edytorze, a następnie skopiuj zawartość schowka do tego pliku. Zapisz plik. 

4. Zaloguj się do witryny Azure Portal, kliknij przycisk **Wszystkie zasoby** w menu po lewej stronie, a następnie otwórz używaną usługę aprowizacji.

5. W bloku podsumowania usługi Device Provisioning Service wybierz pozycję **Zarządzaj rejestracjami**. Wybierz kartę **Indywidualne rejestracje** i kliknij u góry przycisk **Dodaj**. 

6. W panelu **Dodaj rejestrację** wprowadź następujące informacje:
    - Wybierz opcję **X.509** jako *Mechanizm* poświadczania tożsamości.
    - W obszarze *Plik certyfikatu PEM lub CER* kliknij polecenie *Wybierz plik*, aby wybrać plik certyfikatu **X509testcert.pem** utworzony w poprzednich krokach.
    - Opcjonalnie można podać następujące informacje:
      - Wybierz centrum IoT połączone z Twoją usługą aprowizacji.
      - Wprowadź unikatowy identyfikator urządzenia. Nadając nazwę urządzeniu, unikaj korzystania z danych poufnych. 
      - Zaktualizuj pole **Początkowy stan bliźniaczej reprezentacji urządzenia** za pomocą wybranej konfiguracji początkowej dla urządzenia.
    - Gdy skończysz, kliknij przycisk **Zapisz**. 

    [![Dodawanie indywidualnej rejestracji dla zaświadczenia X.509 w portalu](./media/quick-create-simulated-device-x509/individual-enrollment.png)](./media/quick-create-simulated-device-x509/individual-enrollment.png#lightbox)

   Po pomyślnej rejestracji urządzenie X.509 jest wyświetlane jako **riot-device-cert** w kolumnie *Identyfikator rejestracji* na karcie *Indywidualne rejestracje*. 



<a id="firstbootsequence"></a>

## <a name="simulate-first-boot-sequence-for-the-device"></a>Symulowanie sekwencji pierwszego uruchamiania dla urządzenia

1. W witrynie Azure Portal wybierz blok **Przegląd** dla swojej usługi Device Provisioning Service, a następnie zapisz wartość **_Identyfikator zakresu_**.

    ![Wyodrębnianie informacji o punkcie końcowym usługi DPS z bloku portalu](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. W programie Visual Studio na używanej maszynie przejdź do przykładowego projektu o nazwie **prov\_dev\_client\_sample** w folderze **Provision\_Samples**, a następnie otwórz plik **prov\_dev\_client\_sample.c**.

3. Przypisz wartość _Identyfikator zakresu_ do zmiennej `id_scope`. 

    ```c
    static const char* id_scope = "[ID Scope]";
    ```

4. W funkcji **main()** w tym samym pliku upewnij się, że parametr **SECURE_DEVICE_TYPE** ustawiono na wartość X.509.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

   Oznacz jako komentarz lub usuń instrukcję `hsm_type = SECURE_DEVICE_TYPE_TPM;`, jeśli istnieje. 

5. Kliknij prawym przyciskiem myszy projekt **prov\_dev\_client\_sample**, a następnie wybierz pozycję **Ustaw jako projekt startowy**. Uruchom przykład. Zwróć uwagę na komunikaty symulujące uruchamianie urządzenia i łączenie z usługą Device Provisioning Service w celu pobrania informacji z Twojego centrum IoT. Wyszukaj komunikat informujący o pomyślnej rejestracji w centrum: *Informacje o rejestracji otrzymane od usługi: adres_URL_Twojego_centrum!*. Zamknij okno po wyświetleniu monitu.

6. W portalu przejdź do centrum IoT połączonego z usługą aprowizacji, a następnie otwórz blok **Urządzenia IoT**. Po pomyślnej aprowizacji symulowanego urządzenia X.509 w centrum identyfikator urządzenia jest wyświetlany w bloku **Urządzenia IoT** z pozycją *STATUS* (stan) ustawioną na wartość **enabled** (włączone). Należy pamiętać, że konieczne może być kliknięcie przycisku **Odśwież** znajdującego się u góry, jeśli blok został otwarty przed uruchomieniem przykładowej aplikacji urządzenia. 

    ![Urządzenie jest rejestrowane w centrum IoT](./media/quick-create-simulated-device/hub-registration.png) 

    Jeśli zmienisz wartość w polu *Początkowy stan bliźniaczej reprezentacji urządzenia* z domyślnej na inną we wpisie rejestracji dla Twojego urządzenia, może to spowodować pobranie z centrum żądanego stanu reprezentacji bliźniaczej i odpowiednie do niego działanie. Aby uzyskać więcej informacji, zobacz [Opis bliźniaczej reprezentacji urządzenia w usłudze IoT Hub oraz sposoby jej używania](../iot-hub/iot-hub-devguide-device-twins.md).


> [!IMPORTANT]
> Można również przeprowadzić *rejestrację grupową* urządzeń X.509 przez wprowadzenie następujących modyfikacji kroków w tym przewodniku Szybki start:
>    1. Skonfiguruj maszynę z systemem Windows ma potrzeby korzystania z biblioteki **OpenSSL** zamiast z domyślnej biblioteki **SChannel**, wykonując instrukcje z sekcji dotyczącej **gniazd WebSocket** w przewodniku [Set up a Windows development environment](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#windows) (Konfigurowanie środowiska deweloperskiego w systemie Windows). Należy pamiętać, że maszyny z systemem Linux korzystają domyślnie z biblioteki OpenSSL. 
>    2. W kroku 2 sekcji [Tworzenie wpisu rejestracji urządzenia w usłudze Device Provisioning Service](#portalenroll) powyżej wprowadź **g**, aby przeprowadzić rejestrowanie grupowe.
>    3. W krokach 4 i 5 [tej samej sekcji](#portalenroll) wybierz pozycję **Grupy rejestracji** i wprowadź wymagane informacje dla wpisu grupy.  
>

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli planujesz dalszą pracę z przykładem klienta urządzenia i eksplorowanie go, nie czyść zasobów utworzonych w ramach tego przewodnika Szybki start. Jeśli nie planujesz kontynuować pracy, wykonaj poniższe kroki, aby usunąć wszystkie zasoby utworzone w ramach tego przewodnika Szybki start.

1. Zamknij okno danych wyjściowych przykładu klienta urządzenia na swojej maszynie.
1. W witrynie Azure Portal w menu po lewej stronie kliknij pozycję **Wszystkie zasoby**, a następnie wybierz swoją usługę Device Provisioning Service. Otwórz blok **Zarządzanie rejestracjami** dla usługi, a następnie kliknij kartę **Rejestracje indywidualne**. Wybierz *IDENTYFIKATOR REJESTRACJI* urządzenia zarejestrowanego w ramach tego przewodnika Szybki start i kliknij przycisk **Usuń** u góry. 
1. W witrynie Azure Portal w menu po lewej stronie kliknij pozycję **Wszystkie zasoby**, a następnie wybierz swoje centrum IoT. Otwórz blok **Urządzenia IoT** Twojego centrum, wybierz *IDENTYFIKATOR URZĄDZENIA* zarejestrowanego w ramach tego przewodnika Szybki start, a następnie kliknij przycisk **Usuń** u góry.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start na Twojej maszynie z systemem Windows utworzono symulowane urządzenie X.509, które zostało następnie zaaprowizowane do Twojego centrum IoT przy użyciu usługi Azure IoT Hub Device Provisioning w portalu. Aby dowiedzieć się, jak zarejestrować urządzenie X.509 programowo, przejdź do przewodnika Szybki start dotyczącego programowej rejestracji urządzeń X.509. 

> [!div class="nextstepaction"]
> [Przewodnik Szybki start platformy Azure — Rejestrowanie urządzenia X.509 w usłudze Azure IoT Hub Device Provisioning](quick-enroll-device-x509-java.md)
