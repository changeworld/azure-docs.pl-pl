---
title: Enroll TPM device to Azure Device Provisioning Service using Node.js
description: Quickstart - Enroll TPM device to Azure IoT Hub Device Provisioning Service using Node.js service SDK. W tym przewodniku Szybki start używane są rejestracje indywidualne.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: nodejs
ms.custom: mvc
ms.openlocfilehash: d76ed4a9ecf0f5442c6b8b80c900e7edfc114cc7
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228607"
---
# <a name="quickstart-enroll-tpm-device-to-iot-hub-device-provisioning-service-using-nodejs-service-sdk"></a>Quickstart: Enroll TPM device to IoT Hub Device Provisioning Service using Node.js service SDK

[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]


W tych instrukcjach pokazano, jak programowo utworzyć rejestrację indywidualną dla urządzenia TPM w usłudze Azure IoT Hub Device Provisioning przy użyciu [zestawu SDK usługi Node.js](https://github.com/Azure/azure-iot-sdk-node) i przykładowej aplikacji Node.js. Opcjonalnie można zarejestrować symulowane urządzenie TPM w usłudze aprowizacji przy użyciu tego wpisu rejestracji indywidualnej. Mimo że ta procedura działa zarówno na maszynach z systemem Windows, jak i Linux, na potrzeby tego artykułu zostanie użyta maszyna deweloperska z systemem Windows.

## <a name="prerequisites"></a>Wymagania wstępne

- Pamiętaj, aby wcześniej wykonać kroki przedstawione w części [Konfigurowanie usługi IoT Hub Device Provisioning za pomocą witryny Azure Portal](./quick-setup-auto-provision.md). 
-  Upewnij się, że na komputerze jest zainstalowane [środowisko Node.js w wersji 4.0 lub nowszej](https://nodejs.org).
- Jeśli chcesz zarejestrować symulowane urządzenie na końcu tego przewodnika Szybki start, postępuj zgodnie z instrukcjami z sekcji [Tworzenie i aprowizowanie symulowanego urządzenia](quick-create-simulated-device.md) do kroku, w którym uzyskujesz klucz poręczenia dla urządzenia. Zanotuj klucz poręczenia. Zostanie on użyty w dalszej części tego przewodnika Szybki start. **Nie wykonuj kroków tworzenia rejestracji indywidualnej przy użyciu witryny Azure Portal.**
 
## <a name="create-the-individual-enrollment-sample"></a>Tworzenie przykładowej rejestracji indywidualnej 

 
1. Z poziomu okna polecenia w folderze roboczym uruchom następujące polecenie:
  
    ```cmd\sh
    npm install azure-iot-provisioning-service
    ```  

2. Za pomocą edytora tekstu utwórz plik **create_individual_enrollment.js** w folderze roboczym. Dodaj następujący kod do pliku i zapisz go:

    ```
    'use strict';

    var provisioningServiceClient = require('azure-iot-provisioning-service').ProvisioningServiceClient;

    var serviceClient = provisioningServiceClient.fromConnectionString(process.argv[2]);
    var endorsementKey = process.argv[3];

    var enrollment = {
      registrationId: 'first',
      attestation: {
        type: 'tpm',
        tpm: {
          endorsementKey: endorsementKey
        }
      }
    };

    serviceClient.createOrUpdateIndividualEnrollment(enrollment, function(err, enrollmentResponse) {
      if (err) {
        console.log('error creating the individual enrollment: ' + err);
      } else {
        console.log("enrollment record returned: " + JSON.stringify(enrollmentResponse, null, 2));
      }
    });
    ```

## <a name="run-the-individual-enrollment-sample"></a>Uruchamianie przykładowej rejestracji indywidualnej
  
1. Do uruchomienia przykładu będą potrzebne parametry połączenia usługi aprowizacji. 
    1. Zaloguj się w witrynie Azure Portal, kliknij przycisk **Wszystkie zasoby** w menu po lewej stronie i otwórz swoją usługę Device Provisioning. 
    2. Kliknij pozycję **Zasady dostępu współużytkowanego**, a następnie kliknij odpowiednie zasady dostępu, aby otworzyć ich właściwości. W oknie **Zasady dostępu** skopiuj i zanotuj parametry połączenia klucza podstawowego. 

       ![Pobieranie parametrów połączenia usługi aprowizacji z portalu](./media/quick-enroll-device-tpm-node/get-service-connection-string.png) 


2. Wymagany jest również klucz poręczenia dla danego urządzenia. Jeśli w celu utworzenia symulowanego urządzenia TPM wykonano kroki z sekcji [Tworzenie i aprowizowanie symulowanego urządzenia](quick-create-simulated-device.md) przewodnika Szybki start, użyj klucza utworzonego dla tego urządzenia. W przeciwnym razie, aby utworzyć przykładową rejestrację indywidualną, można użyć następującego klucza poręczenia dostarczonego razem z zestawem SDK:

    ```
    AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAxsj2gUScTk1UjuioeTlfGYZrrimExB+bScH75adUMRIi2UOMxG1kw4y+9RW/IVoMl4e620VxZad0ARX2gUqVjYO7KPVt3dyKhZS3dkcvfBisBhP1XH9B33VqHG9SHnbnQXdBUaCgKAfxome8UmBKfe+naTsE5fkvjb/do3/dD6l4sGBwFCnKRdln4XpM03zLpoHFao8zOwt8l/uP3qUIxmCYv9A7m69Ms+5/pCkTu/rK4mRDsfhZ0QLfbzVI6zQFOKF/rwsfBtFeWlWtcuJMKlXdD8TXWElTzgh7JS4qhFzreL0c1mI0GCj+Aws0usZh7dLIVPnlgZcBhgy1SSDQMQ==
    ```

3. Aby utworzyć rejestrację indywidualną dla urządzenia TPM, uruchom następujące polecenie (z uwzględnieniem cudzysłowów wokół argumentów polecenia):
 
     ```cmd\sh
     node create_individual_enrollment.js "<the connection string for your provisioning service>" "<endorsement key>"
     ```
 
3. Po pomyślnym utworzeniu nowej rejestracji indywidualnej w oknie polecenia zostaną wyświetlone jej właściwości.

    ![Właściwości rejestracji w danych wyjściowych polecenia](./media/quick-enroll-device-tpm-node/output.png) 

4. Sprawdź, czy rejestracja indywidualna została utworzona. W witrynie Azure Portal w bloku podsumowania usługi Device Provisioning wybierz pozycję **Zarządzaj rejestracjami**. Wybierz kartę **Rejestracje indywidualne** i kliknij nowy wpis rejestracji (*pierwszy*), aby zweryfikować klucz poręczenia i inne właściwości wpisu.

    ![Właściwości rejestracji w portalu](./media/quick-enroll-device-tpm-node/verify-enrollment-portal.png) 
 
Jeśli po utworzeniu rejestracji indywidualnej dla urządzenia TPM chcesz zarejestrować symulowane urządzenie, możesz wykonać pozostałe kroki z sekcji [Tworzenie i aprowizowanie symulowanego urządzenia](quick-create-simulated-device.md). Pamiętaj, aby pominąć kroki tworzenia rejestracji indywidualnej przy użyciu witryny Azure Portal w tym przewodniku Szybki start.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Jeśli planujesz eksplorować przykłady środowiska Node.js, nie usuwaj zasobów utworzonych w tym przewodniku Szybki start. Jeśli nie planujesz kontynuować pracy, wykonaj poniższe kroki, aby usunąć wszystkie zasoby utworzone w ramach tego przewodnika Szybki start.

1. Zamknij okno danych wyjściowych przykładu środowiska Node.js na swojej maszynie.
1. Jeśli utworzono symulowane urządzenie TPM, zamknij okno symulatora modułu TPM.
2. Navigate to your Device Provisioning service in the Azure portal, click **Manage enrollments**, and then select the **Individual Enrollments** tab. Select the *Registration ID* for the enrollment entry you created using this Quickstart, and click the **Delete** button at the top of the blade. 
 
## <a name="next-steps"></a>Następne kroki
W tym przewodniku Szybki start utworzono programowo wpis rejestracji indywidualnej dla urządzenia TPM oraz opcjonalnie na komputerze utworzono symulowane urządzenie TPM, które zaaprowizowano do Twojego centrum IoT przy użyciu usługi Azure IoT Hub Device Provisioning. Aby uzyskać dokładne informacje na temat aprowizowania urządzeń, przejdź do samouczka poświęconego konfiguracji usługi Device Provisioning Service w witrynie Azure portal. 
 
> [!div class="nextstepaction"]
> [Samouczki dla usługi Azure IoT Hub Device Provisioning Service](./tutorial-set-up-cloud.md)