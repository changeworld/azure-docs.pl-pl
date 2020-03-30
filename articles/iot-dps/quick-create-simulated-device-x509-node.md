---
title: Aprowizuj symulowane urządzenie X.509 do usługi Azure IoT Hub przy użyciu node.js
description: Tworzenie i inicjowanie obsługi administracyjnej symulowanego urządzenia X.509 przy użyciu zestawu SDK urządzenia node.js dla usługi inicjowania obsługi administracyjnej usługi azure IoT Hub (DPS). Ten przewodnik Szybki start korzysta z rejestracji indywidualnych.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: nodejs
ms.custom: mvc
ms.openlocfilehash: 746f4adbf616f95c21874d7c1c48881f88c38d34
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77605408"
---
# <a name="quickstart-create-and-provision-an-x509-simulated-device-using-nodejs-device-sdk-for-iot-hub-device-provisioning-service"></a>Szybki start: tworzenie i aprowizowanie symulowanego urządzenia X.509 przy użyciu sdk urządzenia Node.js dla usługi inicjowania obsługi administracyjnej usługi ioT Hub

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

W tym przewodniku Szybki start utworzysz symulowane urządzenie X.509 na komputerze z systemem Windows. Przykładowy kod Node.js urządzenia służy do łączenia tego symulowanego urządzenia z centrum IoT przy użyciu indywidualnej rejestracji w usłudze inicjowania obsługi administracyjnej (DPS).

## <a name="prerequisites"></a>Wymagania wstępne

- Przegląd [koncepcji automatycznego inicjowania obsługi administracyjnej](concepts-auto-provisioning.md).
- Zakończenie [konfigurowania usługi inicjowania obsługi administracyjnej urządzeń w centrum IoT za pomocą witryny Azure portal](./quick-setup-auto-provision.md).
- Konto platformy Azure z aktywną subskrypcją. [Utwórz jeden za darmo](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Node.js v4.0+](https://nodejs.org).
- [Git](https://git-scm.com/download/).
- [OpenSSL](https://www.openssl.org/).

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>Przygotowywanie środowiska 

1. Zanim przejdziesz dalej, wykonaj kroki przedstawione w artykule [Konfigurowanie usługi IoT Hub Device Provisioning w witrynie Azure Portal](./quick-setup-auto-provision.md).

2. Upewnij się, że na komputerze jest zainstalowane [środowisko Node.js w wersji 4.0 lub nowszej](https://nodejs.org).

3. Upewnij [się,](https://git-scm.com/download/) że Git jest zainstalowany na komputerze i jest dodawany do zmiennych środowiskowych dostępnych w oknie poleceń. 

4. Upewnij się, że na Twojej maszynie jest zainstalowana biblioteka [OpenSSL](https://www.openssl.org/) i że jest ona dodana do zmiennych środowiskowych dostępnych z okna poleceń. Ta biblioteka może zostać skompilowana i zainstalowana ze źródła lub pobrana i zainstalowana od [innego podmiotu](https://wiki.openssl.org/index.php/Binaries), takiego jak [ten](https://sourceforge.net/projects/openssl/). 

    > [!NOTE]
    > Jeśli masz już utworzony certyfikat _główny_, _pośredni_ i/lub _liścia_ X.509, możesz pominąć ten krok i wszystkie następne czynności dotyczące generowania certyfikatów.
    >

## <a name="create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry"></a>Tworzenie certyfikatu urządzenia X.509 z podpisem własnym i wpisu rejestracji indywidualnej

W tej sekcji zostanie użyty certyfikat z podpisem własnym X.509. Ważne jest, aby pamiętać o następujących kwestiach:

* Certyfikaty z podpisem własnym są przeznaczone tylko do celów testowania i nie powinny być używane w środowisku produkcyjnym.
* Domyślny termin wygaśnięcia certyfikatu z podpisem własnym to jeden rok.

Przykładowy kod z [zestawu Azure IoT SDK dla środowiska Node.js](https://github.com/Azure/azure-iot-sdk-node.git) zostanie użyty do utworzenia certyfikatu, który będzie używany z indywidualnym wpisem rejestracji dla urządzenia symulowanego.

Usługa Azure IoT Device Provisioning obsługuje dwa typy rejestracji:

- [Grupy rejestracji](concepts-service.md#enrollment-group): służą do rejestrowania wielu pokrewnych urządzeń.
- [Rejestracje indywidualne:](concepts-service.md#individual-enrollment)służy do rejestrowania jednego urządzenia.

W tym artykule przedstawiono rejestracje indywidualne.

1. Otwórz wiersz polecenia. Sklonuj repozytorium GitHub, aby uzyskać przykłady kodu:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-node.git --recursive
    ```

2. Przejdź do skryptu generatora certyfikatu i skompiluj projekt. 

    ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    npm install
    ```

3. Utwórz certyfikat X.509 _liścia_, uruchamiając skrypt z użyciem Twojego parametru _certificate-name_. Nazwa pospolita certyfikatu liścia staje się [identyfikatorem rejestracji](https://docs.microsoft.com/azure/iot-dps/concepts-device#registration-id), dlatego używaj tylko małych znaków alfanumerycznych i łączników.

    ```cmd/sh
    node create_test_cert.js device {certificate-name}
    ```

4. Zaloguj się do [witryny Azure portal](https://portal.azure.com), wybierz przycisk **Wszystkie zasoby** w menu po lewej stronie i otwórz wystąpienie usługi inicjowania obsługi administracyjnej urządzenia.

5. Z menu Usługa inicjowania obsługi administracyjnej urządzeń wybierz polecenie **Zarządzaj rejestracjami**. Wybierz kartę **Rejestracje indywidualne** i wybierz przycisk **Dodaj rejestrację indywidualną** u góry. 

6. W panelu **Dodawanie rejestracji** wprowadź następujące informacje:
   - Wybierz opcję **X.509** jako *Mechanizm* poświadczania tożsamości.
   - W obszarze *Certyfikat podstawowy .pem lub cer file*wybierz *plik,* aby wybrać plik **certyfikatu {nazwa certyfikatu}_cert.pem** utworzony w poprzednich krokach.  
   - Opcjonalnie można podać następujące informacje:
     - Wybierz centrum IoT połączone z Twoją usługą aprowizacji.
     - Wprowadź unikatowy identyfikator urządzenia. Nadając nazwę urządzeniu, unikaj korzystania z danych poufnych. 
     - Zaktualizuj pole **Początkowy stan bliźniaczej reprezentacji urządzenia** za pomocą wybranej konfiguracji początkowej dla urządzenia.
     - Po zakończeniu naciśnij przycisk **Zapisz.** 

     [![Dodawanie indywidualnej rejestracji dla zaświadczenia X.509 w portalu](./media/quick-create-simulated-device-x509-node/device-enrollment.png)](./media/quick-create-simulated-device-x509-node/device-enrollment.png#lightbox)

     Po pomyślnej rejestracji urządzenie X.509 jest wyświetlane jako **{certificatename}** w kolumnie *Identyfikator rejestracji* na karcie *Rejestracje indywidualne.*

## <a name="simulate-the-device"></a>Symulowanie urządzenia

[Zestaw SDK urządzenia środowiska Node.js usługi Azure IoT Hub](https://github.com/Azure/azure-iot-sdk-node) zapewnia prosty sposób symulowania urządzenia. Aby przeczytać więcej, zobacz [Device concepts (Pojęcia dotyczące urządzeń)](https://docs.microsoft.com/azure/iot-dps/concepts-device).

1. W witrynie Azure portal wybierz **blok Przegląd** usługi inicjowania obsługi administracyjnej urządzeń i zanotuj wartości **_punktu końcowego urządzenia GLobal_** i **_zakresu identyfikatorów._**

    ![Wyodrębnianie informacji o punkcie końcowym usługi Device Provisioning Service z bloku portalu](./media/quick-create-simulated-device-x509-node/extract-dps-endpoints.png) 

2. Skopiuj _certyfikat_ i _klucz_ do przykładowego folderu.

    ```cmd/sh
    copy .\{certificate-name}_cert.pem ..\device\samples\{certificate-name}_cert.pem
    copy .\{certificate-name}_key.pem ..\device\samples\{certificate-name}_key.pem
    ```

3. Przejdź do skryptu testowego urządzenia i skompiluj projekt. 

    ```cmd/sh
    cd ..\device\samples
    npm install
    ```

4. Przeprowadź edycję pliku **register\_x509.js**. Po wprowadzeniu poniższych zmian zapisz plik.
    - Zastąp wartość `provisioning host`**_globalnym punktem końcowym urządzenia_** zanotowanym w **kroku 1** powyżej.
    - Zamień `id scope` zakres **_identyfikatora_** opisany w **kroku 1** powyżej. 
    - Zamień `registration id` identyfikator **_rejestracji odnotowany_** w poprzedniej sekcji.
    - Zastąp wartości `cert filename` i `key filename` plikami skopiowanymi w **kroku 2** powyżej. 

5. Uruchom skrypt i sprawdź, czy urządzenie zostało pomyślnie aprowizowane.

    ```cmd/sh
    node register_x509.js
    ```   

6. W portalu przejdź do centrum IoT hub połączone z usługą inicjowania obsługi administracyjnej i otwórz blok **urządzeń IoT.** Po pomyślnym zainicjowaniu obsługi administracyjnej symulowanego urządzenia X.509 do koncentratora jego identyfikator urządzenia pojawia się na bloku **urządzeń IoT** z **włączonym** *statusem* . Może być konieczne naciśnięcie przycisku **Odśwież** u góry, jeśli blok został już otwarty przed uruchomieniem przykładowej aplikacji urządzenia. 

    ![Urządzenie jest rejestrowane w centrum IoT](./media/quick-create-simulated-device-x509-node/hubregistration.png) 

    Jeśli zmienisz wartość w polu *Początkowy stan bliźniaczej reprezentacji urządzenia* z domyślnej na inną we wpisie rejestracji dla Twojego urządzenia, może to spowodować pobranie z centrum żądanego stanu reprezentacji bliźniaczej i odpowiednie do niego działanie. Aby uzyskać więcej informacji, zobacz [Opis bliźniaczej reprezentacji urządzenia w usłudze IoT Hub oraz sposoby jej używania](../iot-hub/iot-hub-devguide-device-twins.md).


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli planujesz kontynuować pracę i eksplorowanie przykładu klienta urządzenia, nie czyścić zasobów utworzonych w tym przewodniku Szybki start. Jeśli nie zamierzasz kontynuować, wykonaj następujące kroki, aby usunąć wszystkie zasoby utworzone przez ten przewodnik Szybki start.

1. Zamknij okno danych wyjściowych przykładu klienta urządzenia na swojej maszynie.
2. Z menu po lewej stronie w witrynie Azure portal wybierz **pozycję Wszystkie zasoby,** a następnie wybierz usługę inicjowania obsługi urządzeń. Otwórz bloku **Zarządzaj rejestracjami** dla usługi, a następnie wybierz kartę **Rejestracje indywidualne.** Zaznacz pole wyboru obok *identyfikatora REJESTRACJI* urządzenia zarejestrowanego w tym przewodniku Szybki start, a następnie naciśnij przycisk **Usuń** u góry okienka. 
3. Z menu po lewej stronie w witrynie Azure portal wybierz **pozycję Wszystkie zasoby,** a następnie wybierz centrum IoT Hub. Otwórz blok **Urządzenia IoT** dla koncentratora, zaznacz pole wyboru obok *identyfikatora URZĄDZENIA* urządzenia zarejestrowanego w tym przewodniku Szybki start, a następnie naciśnij przycisk **Usuń** u góry okienka.


## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono symulowane urządzenie X.509 i zainicjowano jego obsługę w centrum IoT hub przy użyciu usługi inicjowania obsługi administracyjnej usługi Azure IoT Hub w portalu. Aby dowiedzieć się, jak zarejestrować urządzenie X.509 programowo, przejdź do szybkiego startu w celu uzyskania programowej rejestracji urządzeń X.509. 

> [!div class="nextstepaction"]
> [Szybki start platformy Azure — rejestrowanie urządzeń X.509 w usłudze inicjowania obsługi administracyjnej usługi Azure IoT Hub](quick-enroll-device-x509-node.md)
