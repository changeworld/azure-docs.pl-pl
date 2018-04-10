---
title: Aprowizacja urządzenia przy użyciu usługi Azure IoT Hub Device Provisioning | Microsoft Docs
description: Aprowizacja urządzenia w jednym centrum IoT przy użyciu usługi Azure IoT Hub Device Provisioning
services: iot-dps
keywords: ''
author: dsk-2015
ms.author: dkshir
ms.date: 03/28/2018
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 4d98ce103bed7f9d14eb45422b70ceca1328afaa
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="provision-the-device-to-an-iot-hub-using-the-azure-iot-hub-device-provisioning-service"></a>Aprowizacja urządzenia w centrum IoT przy użyciu usługi Azure IoT Hub Device Provisioning

W poprzednim samouczku omówiono sposób konfigurowania urządzenia do nawiązywania połączenia z usługą Device Provisioning. W tym samouczku dowiesz się, jak używać tej usługi do aprowizacji urządzenia w jednym centrum IoT za pomocą **_list rejestracji_**. Ten samouczek przedstawia sposób wykonania następujących czynności:

> [!div class="checklist"]
> * Rejestrowanie urządzenia
> * Uruchamianie urządzenia
> * Sprawdzanie, czy urządzenie zostało zarejestrowane

## <a name="prerequisites"></a>Wymagania wstępne

Przed kontynuowaniem upewnij się, że urządzenie zostało skonfigurowane zgodnie z zaleceniami z samouczka [Konfigurowanie urządzenia do aprowizacji przy użyciu usługi Azure IoT Hub Device Provisioning](./tutorial-set-up-device.md).

Jeśli nie znasz procesu automatycznego aprowizowania, przed kontynuowaniem zapoznaj się z tematem [Auto-provisioning concepts](concepts-auto-provisioning.md) (Pojęcia związane z automatycznym aprowizowaniem).

<a id="enrolldevice"></a>
## <a name="enroll-the-device"></a>Rejestrowanie urządzenia

Ten krok obejmuje dodawanie unikatowych artefaktów zabezpieczeń urządzenia do usługi Device Provisioning. Te artefakty zabezpieczeń bazują na [mechanizmie zaświadczania](concepts-device.md#attestation-mechanism) urządzenia w następujący sposób:

- W przypadku urządzeń opartych na modułach TPM wymagane są następujące elementy:
    - *Klucz poręczenia* unikatowy dla każdego modułu TPM lub symulacji uzyskany od producenta modułu TPM.  Aby uzyskać więcej informacji, zobacz [Understand TPM Endorsement Key](https://technet.microsoft.com/library/cc770443.aspx) (Informacje o kluczu poręczenia modułu TPM).
    - *Identyfikator rejestracji* używany do jednoznacznego identyfikowania urządzenia w zakresie/przestrzeni nazw. Ten identyfikator może, ale nie musi być taki sam jak identyfikator urządzenia. Identyfikator jest wymagany dla każdego urządzenia. W przypadku urządzeń opartych na modułach TPM identyfikator rejestracji może pochodzić od samego modułu TPM, na przykład może to być skrót SHA-256 klucza poręczenia modułu TPM.

    ![Informacje o rejestracji dla modułu TPM w portalu](./media/tutorial-provision-device-to-hub/tpm-device-enrollment.png)

- W przypadku urządzeń opartych na standardzie X.509 wymagane są następujące elementy:
    - [Certyfikat wystawiony dla modułu lub symulacji X.509](https://msdn.microsoft.com/library/windows/desktop/bb540819.aspx) w postaci pliku *PEM* lub *CER*. W przypadku rejestracji indywidualnej należy użyć *certyfikatu osoby podpisującej* na urządzenia dla systemu X.509, natomiast w przypadku grup rejestracji należy użyć *certyfikatu głównego*. 

    ![Informacje o rejestracji dla systemu X.509 w portalu](./media/tutorial-provision-device-to-hub/x509-device-enrollment.png)

Istnieją dwa sposoby rejestrowania urządzenia w usłudze Device Provisioning:

- **Grupy rejestracji** — jest to reprezentacja grupy urządzeń, które współużytkują specyficzny mechanizm zaświadczania. Firma Microsoft zaleca używanie grupy rejestracji w przypadku dużej liczby urządzeń, które współużytkują pożądaną konfigurację początkową, lub urządzeń przeznaczonych dla tej samej dzierżawy.

    ![Grupy rejestracji dla systemu X.509 w portalu](./media/tutorial-provision-device-to-hub/x509-enrollment-groups.png)

- **Rejestracje indywidualne** — jest to reprezentacja wpisu dla pojedynczego urządzenia, które może zostać zarejestrowane w usłudze Device Provisioning. Rejestracje indywidualne mogą używać certyfikatów x509 lub tokenów SAS (w rzeczywistym lub wirtualnym module TPM) jako mechanizmów zaświadczania. Firma Microsoft zaleca używanie rejestracji indywidualnych w przypadku urządzeń, które wymagają unikatowej konfiguracji początkowej, i urządzeń, które jako mechanizmu zaświadczania mogą używać tylko tokenów SAS za pośrednictwem modułu TPM lub wirtualnego modułu TPM. W przypadku rejestracji indywidualnych można określić identyfikatory urządzeń wymaganego centrum IoT.

Teraz zarejestrujesz urządzenie w wystąpieniu usługi Device Provisioning, korzystając z wymaganych artefaktów zabezpieczeń opartych na mechanizmie zaświadczania urządzenia: 

1. Zaloguj się w witrynie Azure Portal, kliknij przycisk **Wszystkie zasoby** w menu po lewej stronie i otwórz swoją usługę Device Provisioning.

2. W bloku podsumowania usługi Device Provisioning Service wybierz pozycję **Zarządzaj rejestracjami**. W zależności od konfiguracji urządzenia wybierz kartę **Rejestracje indywidualne** lub **Grupy rejestracji**. Kliknij przycisk **Dodaj** na górze. Wybierz opcję **TPM** lub **X.509** jako *Mechanizm* poświadczania tożsamości i wprowadź odpowiednie artefakty zabezpieczeń, jak omówiono wcześniej. Możesz wprowadzić nowy **Identyfikator urządzenia usługi IoT Hub**. Gdy skończysz, kliknij przycisk **Zapisz**. 

3. Po pomyślnym zarejestrowaniu urządzenia powinno ono być widoczne w portalu w następujący sposób:

    ![Pomyślna rejestracja urządzenia TPM w portalu](./media/tutorial-provision-device-to-hub/tpm-enrollment-success.png)

Po rejestracji usługa aprowizacji czeka, aż urządzenie się uruchomi, i łączy się z nim w późniejszym czasie. Podczas pierwszego uruchomienia urządzenia biblioteka zestawu SDK klienta wchodzi w interakcje z modułem w celu wyodrębnienia z urządzenia artefaktów zabezpieczeń i weryfikuje rejestrację przy użyciu usługi Device Provisioning. 

## <a name="start-the-device"></a>Uruchamianie urządzenia

Na tym etapie następujące konfiguracje są gotowe do rejestracji urządzenia:

1. Urządzenie (lub grupa urządzeń) zostało zarejestrowane w usłudze Device Provisioning i 
2. Urządzenie jest gotowe ze skonfigurowanym mechanizmem zaświadczania, do którego można uzyskać dostęp za pośrednictwem aplikacji przy użyciu zestawu SDK klienta usługi Device Provisioning.

Uruchom urządzenie, aby umożliwić aplikacji klienckiej rozpoczęcie rejestracji w usłudze Device Provisioning.  

## <a name="verify-the-device-is-registered"></a>Sprawdzanie, czy urządzenie zostało zarejestrowane

Po uruchomieniu urządzenia powinny zostać wykonane następujące działania. Więcej szczegółowych informacji można uzyskać, analizując przykładową aplikację symulatora modułu TPM [dps_client_sample](https://github.com/Azure/azure-iot-device-auth/blob/master/dps_client/samples/dps_client_sample/dps_client_sample.c). 

1. Urządzenie wysyła żądanie rejestracji do usługi Device Provisioning.
2. W przypadku urządzeń z modułem TPM usługa Device Provisioning przesyła z powrotem wezwanie do rejestracji, na które odpowiada urządzenie. 
3. Po pomyślnej rejestracji usługa Device Provisioning przesyła z powrotem do urządzenia identyfikator URI centrum IoT, identyfikator urządzenia i zaszyfrowany klucz. 
4. Następnie aplikacja kliencka usługi IoT Hub na urządzeniu łączy się z centrum. 
5. Po pomyślnym nawiązaniu połączenia z centrum urządzenie powinno pojawić się w narzędziu **Device Explorer** w centrum IoT. 

    ![Pomyślne połączenie z centrum w portalu](./media/tutorial-provision-device-to-hub/hub-connect-success.png)

## <a name="next-steps"></a>Następne kroki
W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Rejestrowanie urządzenia
> * Uruchamianie urządzenia
> * Sprawdzanie, czy urządzenie zostało zarejestrowane

Przejdź do następnego samouczka, aby dowiedzieć się, jak aprowizować wiele urządzeń w obrębie centrów ze zrównoważonym obciążeniem. 

> [!div class="nextstepaction"]
> [Aprowizuj urządzenia w centrach IoT Hub ze zrównoważonym obciążeniem](./tutorial-provision-multiple-hubs.md)
