---
title: Rejestrowanie urządzeń X.509 w usłudze Azure Device Provisioning przy użyciu języka Python | Microsoft Docs
description: Przewodnik Szybki start platformy Azure — rejestrowanie urządzeń X.509 w usłudze Azure IoT Hub Device Provisioning przy użyciu zestawu SDK usługi aprowizacji języka Python
services: iot-dps
keywords: ''
author: bryanla
ms.author: v-masebo
ms.date: 01/25/2018
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: a5aef91b4af5668f1b34f029ccd443c9eed01e34
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="enroll-x509-devices-to-iot-hub-device-provisioning-service-using-python-provisioning-service-sdk"></a>Rejestrowanie urządzeń X.509 w usłudze IoT Hub Device Provisioning przy użyciu zestawu SDK usługi aprowizacji języka Python
[!INCLUDE [iot-dps-selector-quick-enroll-device-x509](../../includes/iot-dps-selector-quick-enroll-device-x509.md)]

W tych instrukcjach pokazano, jak programowo zarejestrować grupę symulowanych urządzeń X.509 w usłudze Azure IoT Hub Device Provisioning przy użyciu [zestawu SDK usługi aprowizacji języka Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/provisioning_service_client) i przykładowej aplikacji w języku Python. Mimo że zestaw SDK usługi Java działa zarówno na komputerach z systemami Windows, jak i Linux, w tym artykule w celu zaprezentowania procesu rejestracji użyto komputera deweloperskiego z systemem Windows.

Pamiętaj, aby wcześniej [skonfigurować usługę IoT Hub Device Provisioning za pomocą witryny Azure Portal](./quick-setup-auto-provision.md).

> [!NOTE]
> Ten przewodnik szybkiego startu obsługuje jedynie **grupę rejestracji**. Opracowywanie **indywidualnego rejestrowania** za pomocą _zestawu SDK usługi aprowizacji języka Python_ jest w toku.
> 

<a id="prepareenvironment"></a>

## <a name="prepare-the-environment"></a>Przygotowywanie środowiska 

1. Pobierz i zainstaluj środowisko [Python 2.x lub 3.x](https://www.python.org/downloads/). Upewnij się, że używasz 32-bitowej lub 64-bitowej instalacji zgodnie z wymaganiami konfiguracji. Po wyświetleniu monitu podczas instalacji upewnij się, że język Python został dodany do zmiennych środowiskowych specyficznych dla platformy. 

1. Wybierz jedną z następujących opcji:

    - Utwórz i skompiluj **zestaw SDK języka Python usługi Azure IoT**. Postępuj zgodnie z [tymi instrukcjami](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md), aby skompilować pakiety języka Python. Jeśli używasz systemu operacyjnego Windows, zainstaluj także [Pakiet redystrybucyjny języka Visual C++](http://www.microsoft.com/download/confirmation.aspx?id=48145) umożliwiający korzystanie z natywnych bibliotek DLL języka Python.

    - [Zainstaluj lub uaktualnij program *pip*, czyli system zarządzania pakietami języka Python](https://pip.pypa.io/en/stable/installing/), i zainstaluj pakiet za pomocą następującego polecenia:

        ```cmd/sh
        pip install azure-iothub-provisioningserviceclient
        ```

1. Potrzebny będzie plik pem zawierający pośredni lub główny certyfikat X.509 urzędu certyfikacji, który został przesłany do usługi aprowizacji i przez nią zweryfikowany. **Zestaw SDK języka C usługi Azure IoT** zawiera narzędzia, które mogą ułatwić tworzenie łańcucha certyfikatów X.509, przekazywanie certyfikatu głównego lub pośredniego z tego łańcucha oraz wykonywanie operacji dowodu posiadania w usłudze w celu weryfikacji certyfikatu. Aby użyć tych narzędzi, sklonuj [zestaw SDK C usługi IoT](https://github.com/Azure/azure-iot-sdk-c) i wykonaj na swojej maszynie kroki opisane w artykule [azure-iot-sdk-c\tools\CACertificates\CACertificateOverview.md](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).


## <a name="modify-the-python-sample-code"></a>Modyfikowanie przykładowego kodu w języku Python

W tej sekcji przedstawiono sposób dodawania szczegółów aprowizacji urządzenia X.509 do przykładowego kodu. 

1. Przy użyciu edytora tekstów utwórz nowy plik **EnrollmentGroup.py**.

1. Dodaj następujące instrukcje `import` i zmienne na początku pliku **EnrollmentGroup.py**. Następnie zastąp element `dpsConnectionString` parametrami połączenia zawartymi w **zasadach dostępu współużytkowanego** usługi **Device Provisioning Service** w witrynie **Azure Portal**. Zastąp symbol zastępczy certyfikatu certyfikatem utworzonym wcześniej w kroku [Przygotowywanie środowiska](quick-enroll-device-x509-python.md#prepareenvironment). Na koniec utwórz unikatowy identyfikator `registrationid` i upewnij się, że zawiera on wyłącznie małe litery, cyfry i łączniki.  
   
    ```python
    from provisioningserviceclient import ProvisioningServiceClient
    from provisioningserviceclient.models import EnrollmentGroup, AttestationMechanism

    CONNECTION_STRING = "{dpsConnectionString}"

    SIGNING_CERT = """-----BEGIN CERTIFICATE-----
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    -----END CERTIFICATE-----"""

    GROUP_ID = "{registrationid}"
    ```

1. Dodaj następującą funkcję i wywołanie funkcji w celu zaimplementowania tworzenia rejestracji grupowej:
   
    ```python
    def main():
        print ( "Initiating enrollment group creation..." )

        psc = ProvisioningServiceClient.create_from_connection_string(CONNECTION_STRING)
        att = AttestationMechanism.create_with_x509_signing_certs(SIGNING_CERT)
        eg = EnrollmentGroup.create(GROUP_ID, att)

        eg = psc.create_or_update(eg)
    
        print ( "Enrollment group created." )

    if __name__ == '__main__':
        main()
    ```

1. Zapisz i zamknij plik **EnrollmentGroup.py**.
 

## <a name="run-the-sample-group-enrollment"></a>Uruchamianie przykładowej rejestracji grupowej

1. Otwórz wiersz polecenia i uruchom skrypt.

    ```cmd/sh
    python EnrollmentGroup.py
    ```

1. Sprawdź poprawność rejestracji w danych wyjściowych.

1. Przejdź do usługi aprowizacji w witrynie Azure Portal. Kliknij pozycję **Zarządzanie rejestracjami**. Zauważ, że grupa urządzeń X.509 jest wyświetlana na karcie **Grupy rejestracji** z nazwą `registrationid` utworzoną wcześniej. 

    ![Potwierdzanie poprawności rejestracji urządzenia X.509 w portalu](./media/quick-enroll-device-x509-python/1.png)  


## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Jeśli planujesz eksplorować przykładową usługę Java, nie usuwaj zasobów utworzonych w tym przewodniku Szybki start. Jeśli nie planujesz kontynuować pracy, wykonaj poniższe kroki, aby usunąć wszystkie zasoby utworzone w ramach tego przewodnika Szybki start.

1. Zamknij okno danych wyjściowych przykładowej usługi Java na swojej maszynie.
1. Zamknij okno _Generator certyfikatów X509_ na swojej maszynie.
1. Przejdź do usługi Device Provisioning w witrynie Azure Portal, kliknij pozycję **Zarządzanie rejestracjami**, a następnie wybierz kartę **Grupy rejestracji**. Wybierz *NAZWĘ GRUPY* urządzeń X.509 zarejestrowanych w ramach tego przewodnika Szybki start i kliknij przycisk **Usuń** u góry bloku.  


## <a name="next-steps"></a>Następne kroki
W tym przewodniku Szybki start zarejestrowano symulowaną grupę urządzeń X.509 w usłudze Device Provisioning. Aby uzyskać dokładne informacje na temat aprowizowania urządzeń, przejdź do samouczka poświęconego konfiguracji usługi Device Provisioning Service w witrynie Azure portal. 

> [!div class="nextstepaction"]
> [Samouczki dla usługi Azure IoT Hub Device Provisioning Service](./tutorial-set-up-cloud.md)
