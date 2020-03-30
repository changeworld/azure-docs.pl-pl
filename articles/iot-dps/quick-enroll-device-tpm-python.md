---
title: Rejestrowanie urządzenia modułu TPM w usłudze inicjowania obsługi administracyjnej urządzeń platformy Azure przy użyciu języka Python
description: Szybki start — rejestrowanie urządzenia TPM w usłudze inicjowania obsługi administracyjnej usługi usługi Azure IoT Hub (DPS) przy użyciu zestawu SDK usługi inicjowania obsługi administracyjnej języka Python. W tym przewodniku Szybki start używane są rejestracje indywidualne.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: python
ms.custom: mvc
ms.openlocfilehash: c5fe0a577ead9d8c6408d4268d21465a7b762b6d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77920624"
---
# <a name="quickstart-enroll-tpm-device-to-iot-hub-device-provisioning-service-using-python-provisioning-service-sdk"></a>Szybki start: rejestrowanie urządzenia TPM w usłudze inicjowania obsługi administracyjnej urządzeń usługi Usługi IoT Hub przy użyciu usługi inicjowania obsługi administracyjnej języka Python

[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]

W tym przewodniku Szybki start programowo utwórz indywidualną rejestrację dla urządzenia TPM w usłudze inicjowania obsługi administracyjnej urządzeń usługi Azure IoT Hub przy użyciu zestawu SDK usługi inicjowania obsługi administracyjnej języka Python za pomocą przykładowej aplikacji języka Python.

## <a name="prerequisites"></a>Wymagania wstępne

- Zakończenie [konfigurowania usługi inicjowania obsługi administracyjnej urządzeń usługi Usługi obsługi urządzeń usługi IoT Hub za pomocą witryny Azure portal](./quick-setup-auto-provision.md).
- Konto platformy Azure z aktywną subskrypcją. [Utwórz jeden za darmo](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Python 2.x lub 3.x](https://www.python.org/downloads/). Ten szybki start instaluje [sdk usługi inicjowania obsługi administracyjnej języka Python](https://github.com/Azure/azure-iot-sdk-python/tree/v1-deprecated/provisioning_service_client) poniżej.
- [Pip](https://pip.pypa.io/en/stable/installing/), jeśli nie jest dołączony do dystrybucji Pythona.
- Klucz poparcia. Użyj kroków w [Tworzenie i aprowizowania symulowanego urządzenia](quick-create-simulated-device.md) lub użyj klucza poręczenia dostarczonego z zestawu SDK, opisanego poniżej.

> [!IMPORTANT]
> Ten artykuł dotyczy tylko przestarzałego SDK języka Python w języku Python. Klienci urządzeń i usług usługi usługi inicjowania obsługi urządzeń usługi IoT Hub nie są jeszcze dostępne w wersji 2. Zespół jest obecnie ciężko pracuje, aby doprowadzić V2 do parytetu funkcji.

<a id="prepareenvironment"></a>

## <a name="prepare-the-environment"></a>Przygotowywanie środowiska 

1. Pobierz i zainstaluj środowisko [Python 2.x lub 3.x](https://www.python.org/downloads/). Upewnij się, że używasz 32-bitowej lub 64-bitowej instalacji zgodnie z wymaganiami konfiguracji. Po wyświetleniu monitu podczas instalacji upewnij się, że język Python został dodany do zmiennych środowiskowych specyficznych dla platformy. 

1. W przypadku [sdk usługi inicjowania obsługi administracyjnej języka Python](https://github.com/Azure/azure-iot-sdk-python/tree/v1-deprecated/provisioning_service_client)wybierz jedną z następujących opcji:

    - Utwórz i skompiluj **zestaw SDK języka Python usługi Azure IoT**. Postępuj zgodnie z [tymi instrukcjami](https://github.com/Azure/azure-iot-sdk-python/blob/v1-deprecated/doc/python-devbox-setup.md), aby skompilować pakiety języka Python. Jeśli używasz systemu operacyjnego Windows, zainstaluj także [Pakiet redystrybucyjny języka Visual C++](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) umożliwiający korzystanie z natywnych bibliotek DLL języka Python.

    - [Zainstaluj lub uaktualnij program *pip*, czyli system zarządzania pakietami języka Python](https://pip.pypa.io/en/stable/installing/), i zainstaluj pakiet za pomocą następującego polecenia:

        ```cmd/sh
        pip install azure-iothub-provisioningserviceclient
        ```

1. Wymagany jest klucz poręczenia dla danego urządzenia. Jeśli w celu utworzenia symulowanego urządzenia TPM wykonano kroki z sekcji [Tworzenie i aprowizowanie symulowanego urządzenia](quick-create-simulated-device.md) przewodnika Szybki start, użyj klucza utworzonego dla tego urządzenia. W przeciwnym razie możesz użyć następującego klucza poręczenia dostarczonego razem z zestawem SDK:

    ```
    AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAtW6MOyCu/Nih47atIIoZtlYkhLeCTiSrtRN3q6hqgOllA979No4BOcDWF90OyzJvjQknMfXS/Dx/IJIBnORgCg1YX/j4EEtO7Ase29Xd63HjvG8M94+u2XINu79rkTxeueqW7gPeRZQPnl1xYmqawYcyzJS6GKWKdoIdS+UWu6bJr58V3xwvOQI4NibXKD7htvz07jLItWTFhsWnTdZbJ7PnmfCa2vbRH/9pZIow+CcAL9mNTNNN4FdzYwapNVO+6SY/W4XU0Q+dLMCKYarqVNH5GzAWDfKT8nKzg69yQejJM8oeUWag/8odWOfbszA+iFjw3wVNrA5n8grUieRkPQ==
    ```


## <a name="modify-the-python-sample-code"></a>Modyfikowanie przykładowego kodu w języku Python

W tej sekcji przedstawiono sposób dodawania szczegółów aprowizacji urządzenia TPM do przykładowego kodu. 

1. Przy użyciu edytora tekstów utwórz nowy plik **TpmEnrollment.py**.

1. Dodaj następujące instrukcje `import` i zmienne na początku pliku **TpmEnrollment.py**. Następnie zastąp element `dpsConnectionString` parametrami połączenia zawartymi w **zasadach dostępu współużytkowanego** usługi **Device Provisioning Service** w witrynie **Azure Portal**. Zastąp element `endorsementKey` wartością zanotowaną wcześniej podczas [przygotowywania środowiska](quick-enroll-device-tpm-python.md#prepareenvironment). Na koniec utwórz unikatowy identyfikator `registrationid` i upewnij się, że zawiera on wyłącznie małe litery, cyfry i łączniki.  
   
    ```python
    from provisioningserviceclient import ProvisioningServiceClient
    from provisioningserviceclient.models import IndividualEnrollment, AttestationMechanism

    CONNECTION_STRING = "{dpsConnectionString}"

    ENDORSEMENT_KEY = "{endorsementKey}"

    REGISTRATION_ID = "{registrationid}"
    ```

1. Dodaj następującą funkcję i wywołanie funkcji w celu zaimplementowania tworzenia rejestracji grupowej:
   
    ```python
    def main():
        print ( "Starting individual enrollment..." )

        psc = ProvisioningServiceClient.create_from_connection_string(CONNECTION_STRING)

        att = AttestationMechanism.create_with_tpm(ENDORSEMENT_KEY)
        ie = IndividualEnrollment.create(REGISTRATION_ID, att)

        ie = psc.create_or_update(ie)
    
        print ( "Individual enrollment successful." )
    
    if __name__ == '__main__':
        main()
    ```

1. Zapisz i zamknij plik **TpmEnrollment.py**.
 

## <a name="run-the-sample-tpm-enrollment"></a>Uruchamianie przykładowej rejestracji urządzenia TPM

1. Otwórz wiersz polecenia i uruchom skrypt.

    ```cmd/sh
    python TpmEnrollment.py
    ```

1. Sprawdź poprawność rejestracji w danych wyjściowych.

1. Przejdź do usługi aprowizacji w witrynie Azure Portal. Wybierz pozycję **Zarządzanie rejestracjami**. Zauważ, że urządzenie TPM jest wyświetlane na karcie **Indywidualne rejestracje** z nazwą `registrationid` utworzoną wcześniej. 

    ![Potwierdzanie poprawności rejestracji urządzenia TPM w portalu](./media/quick-enroll-device-tpm-python/1.png)  


## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Jeśli planujesz eksplorować przykład usługi Java, nie czyścić zasobów utworzonych w tym przewodniku Szybki start. Jeśli nie zamierzasz kontynuować, wykonaj następujące kroki, aby usunąć wszystkie zasoby utworzone przez ten przewodnik Szybki start.

1. Zamknij okno danych wyjściowych przykładu w języku Python na swojej maszynie.
1. Jeśli utworzono symulowane urządzenie TPM, zamknij okno symulatora modułu TPM.
1. Przejdź do usługi inicjowania obsługi urządzeń w portalu Azure, wybierz pozycję **Zarządzaj rejestracjami**, a następnie wybierz kartę **Rejestracje indywidualne.** Zaznacz pole wyboru obok *identyfikatora rejestracji* dla wpisu rejestracji utworzonego przy użyciu tego przewodnika Szybki start, a następnie naciśnij przycisk **Usuń** u góry okienka.


## <a name="next-steps"></a>Następne kroki
W tym przewodniku Szybki start programowo utworzono indywidualny wpis rejestracji dla urządzenia TPM i opcjonalnie utworzono symulowane urządzenie modułu TPM na komputerze i udostępniono go do centrum IoT przy użyciu usługi inicjowania obsługi administracyjnej urządzeń usługi Azure IoT Hub. Aby uzyskać dokładne informacje na temat aprowizowania urządzeń, przejdź do samouczka poświęconego konfiguracji usługi Device Provisioning Service w witrynie Azure portal.

> [!div class="nextstepaction"]
> [Samouczki dla usługi Azure IoT Hub Device Provisioning Service](./tutorial-set-up-cloud.md)
