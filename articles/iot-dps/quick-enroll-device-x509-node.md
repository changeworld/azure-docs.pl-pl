---
title: Rejestrowanie urządzenia X.509 w usłudze Azure Device Provisioning przy użyciu środowiska Node.js | Microsoft Docs
description: Przewodnik Szybki start platformy Azure — Rejestrowanie urządzenia X.509 w usłudze Azure IoT Hub Device Provisioning przy użyciu zestawu SDK środowiska Node.js
services: iot-dps
keywords: ''
author: bryanla
ms.author: v-jamebr
ms.date: 12/21/2017
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 0d255cd3a076b892f0732766e0084f78a7859aa4
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="enroll-x509-devices-to-iot-hub-device-provisioning-service-using-nodejs-service-sdk"></a>Rejestrowanie urządzenia X.509 w usłudze IoT Hub Device Provisioning przy użyciu zestawu SDK środowiska Node.js

[!INCLUDE [iot-dps-selector-quick-enroll-device-x509](../../includes/iot-dps-selector-quick-enroll-device-x509.md)]


W tych krokach pokazano, jak utworzyć programowo grupę rejestracji dla pośredniego lub głównego certyfikatu X.509 urzędu certyfikacji za pomocą [zestawu SDK środowiska Node.js](https://github.com/Azure/azure-iot-sdk-node) i przykładu środowiska Node.js. Mimo iż ta procedura działa zarówno na maszynach z systemem Windows, jak i Linux, w tym artykule używana jest maszyna deweloperska z systemem Windows.
 

## <a name="prerequisites"></a>Wymagania wstępne

- Pamiętaj, aby wykonać kroki przedstawione w sekcji [Konfigurowanie usługi IoT Hub Device Provisioning za pomocą witryny Azure Portal](./quick-setup-auto-provision.md). 

 
- Upewnij się, że na komputerze jest zainstalowane [środowisko Node.js w wersji 4.0 lub nowszej](https://nodejs.org).


- Potrzebny będzie plik pem zawierający pośredni lub główny certyfikat X.509 urzędu certyfikacji, który został przesłany do usługi aprowizacji i przez nią zweryfikowany. **Zestaw SDK c usługi IoT** zawiera narzędzia, które mogą ułatwić tworzenie łańcucha certyfikatów X.509, przekazywanie certyfikatu głównego lub pośredniego z łańcucha oraz wykonywanie dowodu posiadania w usłudze w celu weryfikacji certyfikatu. Aby użyć tych narzędzi, sklonuj [zestaw SDK c usługi IoT](https://github.com/Azure/azure-iot-sdk-c) i wykonaj na swojej maszynie kroki opisane w artykule [azure-iot-sdk-c\tools\CACertificates\CACertificateOverview.md](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).

## <a name="create-the-enrollment-group-sample"></a>Tworzenie przykładowej grupy rejestracji 

 
1. Z poziomu okna polecenia w folderze roboczym uruchom następujące polecenie:
  
     ```cmd\sh
     npm install azure-iot-provisioning-service
     ```  

2. Za pomocą edytora tekstu utwórz plik **create_enrollment_group.js** w folderze roboczym. Dodaj następujący kod do pliku i zapisz go:

    ```
    'use strict';
    var fs = require('fs');

    var provisioningServiceClient = require('azure-iot-provisioning-service').ProvisioningServiceClient;

    var serviceClient = provisioningServiceClient.fromConnectionString(process.argv[2]);

    var enrollment = {
      enrollmentGroupId: 'first',
      attestation: {
        type: 'x509',
        x509: {
          signingCertificates: {
            primary: {
              certificate: fs.readFileSync(process.argv[3], 'utf-8').toString()
            }
          }
        }
      },
      provisioningStatus: 'disabled'
    };

    serviceClient.createOrUpdateEnrollmentGroup(enrollment, function(err, enrollmentResponse) {
      if (err) {
        console.log('error creating the group enrollment: ' + err);
      } else {
        console.log("enrollment record returned: " + JSON.stringify(enrollmentResponse, null, 2));
        enrollmentResponse.provisioningStatus = 'enabled';
        serviceClient.createOrUpdateEnrollmentGroup(enrollmentResponse, function(err, enrollmentResponse) {
          if (err) {
            console.log('error updating the group enrollment: ' + err);
          } else {
            console.log("updated enrollment record returned: " + JSON.stringify(enrollmentResponse, null, 2));
          }
        });
      }
    });
    ````

## <a name="run-the-enrollment-group-sample"></a>Uruchamianie przykładowej grupy rejestracji
 
1. Do uruchomienia przykładu będą potrzebne parametry połączenia usługi aprowizacji. 
    1. Zaloguj się w witrynie Azure Portal, kliknij przycisk **Wszystkie zasoby** w menu po lewej stronie i otwórz swoją usługę Device Provisioning Service. 
    2. Kliknij pozycję **Zasady dostępu współużytkowanego**, a następnie kliknij odpowiednie zasady dostępu, aby otworzyć ich właściwości. W oknie **Zasady dostępu** skopiuj i zanotuj parametry połączenia klucza podstawowego. 

    ![Pobieranie parametrów połączenia usługi aprowizacji z portalu](./media/quick-enroll-device-x509-node/get-service-connection-string.png) 


3. Jak określono w [Wymaganiach wstępnych](#prerequisites), potrzebny będzie także plik pem zawierający pośredni lub główny certyfikat X.509 urzędu certyfikacji, który został wcześniej przesłany do usługi aprowizacji i przez nią zweryfikowany. Aby sprawdzić, czy certyfikat został przekazany i zweryfikowany, kliknij pozycję **Certyfikaty** na stronie podsumowania usługi Device Provisioning w witrynie Azure Portal. Znajdź certyfikat, którego chcesz używać na potrzeby rejestracji grupowej, i upewnij się, że jego stan ma wartość *zweryfikowany*.

    ![Zweryfikowany certyfikat w portalu](./media/quick-enroll-device-x509-node/verify-certificate.png) 

1. Aby utworzyć grupę rejestracji dla certyfikatu, uruchom następujące polecenie (z uwzględnieniem cudzysłowów wokół argumentów polecenia):
 
     ```cmd\sh
     node create_enrollment_group.js "<the connection string for your provisioning service>" "<your certificate's .pem file>"
     ```
 
3. Po pomyślnym utworzeniu nowej grupy rejestracji w oknie polecenia zostaną wyświetlone jej właściwości.

    ![Właściwości rejestracji w danych wyjściowych polecenia](./media/quick-enroll-device-x509-node/sample-output.png) 

4. Upewnij się, że grupa rejestracji została utworzona. W witrynie Azure Portal w bloku podsumowania usługi Device Provisioning wybierz pozycję **Zarządzaj rejestracjami**. Wybierz kartę **Grupy rejestracji** i upewnij się, że obecny jest nowy wpis rejestracji (*pierwszy*).

    ![Właściwości rejestracji w portalu](./media/quick-enroll-device-x509-node/verify-enrollment-portal.png) 
 
## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Jeśli planujesz eksplorować przykłady środowiska Node.js, nie usuwaj zasobów utworzonych w tym przewodniku Szybki start. Jeśli nie planujesz kontynuować pracy, wykonaj poniższe kroki, aby usunąć wszystkie zasoby platformy Azure utworzone w ramach tego przewodnika Szybki start.
 
1. Zamknij okno danych wyjściowych przykładu środowiska Node.js na swojej maszynie.
2. Przejdź do usługi Device Provisioning w witrynie Azure Portal, kliknij pozycję **Zarządzanie rejestracjami**, a następnie wybierz kartę **Grupy rejestracji**. Wybierz *Identyfikator rejestracji* wpisu rejestracji utworzonego w ramach tego przewodnika Szybki start i kliknij przycisk **Usuń** w górnej części bloku.  
3. Z poziomu usługi Device Provisioning kliknij pozycję **Certyfikaty**, kliknij certyfikat przekazany w ramach tego przewodnika Szybki start, a następnie kliknij przycisk **Usuń** w górnej części okna **Szczegóły certyfikatu**.  
 
## <a name="next-steps"></a>Następne kroki
W tym przewodniku Szybki start utworzono rejestrację grupową dla pośredniego lub głównego certyfikatu X.509 urzędu certyfikacji przy użyciu usługi Azure IoT Hub Device Provisioning. Aby uzyskać dokładne informacje na temat aprowizowania urządzeń, przejdź do samouczka poświęconego konfiguracji usługi Device Provisioning Service w witrynie Azure portal. 
 
> [!div class="nextstepaction"]
> [Samouczki dla usługi Azure IoT Hub Device Provisioning Service](./tutorial-set-up-cloud.md)