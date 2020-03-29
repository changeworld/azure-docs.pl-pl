---
title: Jak zarejestrować zestaw DevKit MXChip IoT DevKit w Usłudze IoT Hub w usłudze Azure IoT Hub | Dokumenty firmy Microsoft
description: Jak używać automatycznej inicjowania obsługi administracyjnej usługi inicjowania obsługi administracyjnej usługi dps (Azure IoT Hub Device Provisioning), aby zarejestrować zestaw DevKit MXChip IoT w centrum IoT Hub.
author: liydu
ms.author: liydu
ms.date: 06/25/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: jeffya
ms.openlocfilehash: f05e92f0452b1cfff23e2094354203fd7eaea48b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975656"
---
# <a name="use-azure-iot-hub-device-provisioning-service-auto-provisioning-to-register-the-mxchip-iot-devkit-with-iot-hub"></a>Automatyczne inicjowanie obsługi administracyjnej usługi aprowizacji urządzeń usługi Azure IoT Hub w celu zarejestrowania zestawu DevKit MXChip IoT w centrum IoT Hub

W tym artykule opisano sposób korzystania z [automatycznego inicjowania obsługi administracyjnej usługi inicjowania obsługi administracyjnej](concepts-auto-provisioning.md)usługi azure IoT Hub, aby zarejestrować zestaw DevKit MXChip IoT w usłudze Azure IoT Hub. Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

* Skonfiguruj globalny punkt końcowy usługi inicjowania obsługi administracyjnej urządzeń na urządzeniu.
* Użyj unikatowego klucza tajnego urządzenia (UDS) do wygenerowania certyfikatu X.509.
* Zarejestruj pojedyncze urządzenie.
* Sprawdź, czy urządzenie jest zarejestrowane.

[MXChip IoT DevKit](https://aka.ms/iot-devkit) to płyta kompatybilna z Arduino i wyposażona w bogate urządzenia peryferyjne i czujniki. Można go opracować przy użyciu [usługi Azure IoT Device Workbench](https://aka.ms/iot-workbench) lub pakietu rozszerzeń [narzędzi Azure IoT w](https://aka.ms/azure-iot-tools) programie Visual Studio Code. DevKit zawiera rosnący [katalog projektów,](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) aby poprowadzić prototyp rozwiązań Internetu rzeczy (IoT), które korzystają z usług platformy Azure.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby wykonać kroki opisane w tym samouczku, wykonaj następujące czynności:

* Skonfiguruj wi-fi devkit i przygotuj środowisko programistyczne, wykonując kroki sekcji "Przygotowanie środowiska programistycznego" w [obszarze Podłącz IoT DevKit AZ3166 do usługi Azure IoT Hub w chmurze.](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started#prepare-the-development-environment)
* Uaktualnij oprogramowanie układowe (1.3.0 lub nowsze) za pomocą [samouczka aktualizacji oprogramowania sprzętowego DevKit.](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/)
* Utwórz i połącz Centrum IoT z wystąpieniem usługi inicjowania obsługi administracyjnej urządzeń, wykonując kroki opisane w [konfigurowaniu usługi inicjowania obsługi administracyjnej urządzeń w centrum IoT za pomocą witryny Azure portal](/azure/iot-dps/quick-setup-auto-provision).

## <a name="open-sample-project"></a>Otwórz przykładowy projekt

1. Upewnij się, że zestaw IoT DevKit nie jest **podłączony** do komputera. Najpierw uruchom program VS Code, a następnie podłącz devkit do komputera.

1. Kliknij, `F1` aby otworzyć paletę poleceń, wpisz i wybierz pozycję Azure **IoT Device Workbench: Open Examples...**. Następnie wybierz **IoT DevKit** jako tablicę.

1. Na stronie Przykłady prac ioT znajdź pozycję **Rejestracja urządzenia w dps** i kliknij przycisk **Otwórz próbkę**. Następnie wybiera domyślną ścieżkę, aby pobrać przykładowy kod.
    ![Otwórz próbkę](media/how-to-connect-mxchip-iot-devkit/open-sample.png)

## <a name="save-a-unique-device-secret-on-device-security-storage"></a>Zapisywanie unikatowego klucza tajnego urządzenia w pamięci zabezpieczającej urządzenia

Automatyczne inicjowanie obsługi administracyjnej można skonfigurować na urządzeniu na podstawie [mechanizmu zaświadczania](concepts-security.md#attestation-mechanism)urządzenia. MXChip IoT DevKit używa [aparatu składu tożsamości urządzenia](https://trustedcomputinggroup.org/wp-content/uploads/Foundational-Trust-for-IOT-and-Resource-Constrained-Devices.pdf) z [zaufanej grupy obliczeniowej](https://trustedcomputinggroup.org). **Unikatowy klucz tajny urządzenia** (UDS) zapisany w układzie zabezpieczeń STSAFE[(STSAFE-A100)](https://microsoft.github.io/azure-iot-developer-kit/docs/understand-security-chip/)w devkit służy do generowania unikalnego [certyfikatu X.509](concepts-security.md#x509-certificates)urządzenia. Certyfikat jest używany później dla procesu rejestracji w usłudze inicjowania obsługi administracyjnej urządzeń i podczas rejestracji w czasie wykonywania.

Typowy UDS jest ciągiem 64 znaków, jak widać w poniższym przykładzie:

```
19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eae
```

Aby zapisać UDS w DevKit:

1. W programie VS Code kliknij pasek stanu, aby wybrać port COM dla zestawu DevKit.
  ![Wybierz port COM](media/how-to-connect-mxchip-iot-devkit/select-com.png)

1. Na DevKit przytrzymaj **przycisk A**, naciśnij i zwolnij przycisk **resetowania,** a następnie zwolnij **przycisk A**. DevKit wchodzi w tryb konfiguracji.

1. Kliknij, `F1` aby otworzyć paletę poleceń, wpisz i wybierz pozycję Azure **IoT Device Workbench: Configure Device Settings... > Config Unique Device String (UDS)**.
  ![Konfigurowanie uds](media/how-to-connect-mxchip-iot-devkit/config-uds.png)

1. Zanotuj wygenerowany ciąg UDS. Będzie potrzebny do wygenerowania certyfikatu X.509. Następnie `Enter`naciśnij klawisz .
  ![Kopiowanie UDS](media/how-to-connect-mxchip-iot-devkit/copy-uds.png)

1. Potwierdź z powiadomienia, że usługa UDS została pomyślnie skonfigurowana w uwieraniu STSAFE.
  ![Konfigurowanie sukcesu UDS](media/how-to-connect-mxchip-iot-devkit/config-uds-success.png)

> [!NOTE]
> Alternatywnie można skonfigurować UDS za pomocą portu szeregowego za pomocą narzędzi, takich jak Putty. Postępuj [zgodnie z trybem konfiguracji.](https://microsoft.github.io/azure-iot-developer-kit/docs/use-configuration-mode/)

## <a name="update-the-global-device-endpoint-and-id-scope"></a>Aktualizowanie globalnego punktu końcowego i zakresu identyfikatora urządzenia

W kodzie urządzenia należy określić [punkt końcowy inicjowania obsługi administracyjnej urządzenia](/azure/iot-dps/concepts-service#device-provisioning-endpoint) i zakres identyfikatora, aby zapewnić izolację dzierżawy.

1. W witrynie Azure portal wybierz **okienko Przegląd** usługi inicjowania obsługi administracyjnej urządzeń i zanotuj wartości **punktu końcowego urządzenia globalnego** i zakresu **identyfikatorów.**
  ![Globalny punkt końcowy i zakres identyfikatora usługi inicjowania obsługi administracyjnej urządzeń](media/how-to-connect-mxchip-iot-devkit/dps-global-endpoint.png)

1. Otwórz **plik DevKitDPS.ino**. Znajdź i `[Global Device Endpoint]` `[ID Scope]` zastąp i wartości, które właśnie zanotowałeś.
  ![Punkt końcowy usługi inicjowania obsługi administracyjnej urządzeń](media/how-to-connect-mxchip-iot-devkit/endpoint.png)

1. Wypełnij `registrationId` zmienną w kodzie. Dozwolona jest tylko kombinacja alfanumeryczna, małych liter i łączników z maksymalnie 128 znakami. Również zauważyć w dół wartość.
  ![Identyfikator rejestracji](media/how-to-connect-mxchip-iot-devkit/registration-id.png)

1. Kliknij `F1`, wpisz i wybierz pozycję **Azure IoT Device Workbench: Przekaż kod urządzenia**. Rozpoczyna kompilowanie i przekazywanie kodu do DevKit.
  ![Przekazywanie do urządzenia](media/how-to-connect-mxchip-iot-devkit/device-upload.png)

## <a name="generate-x509-certificate"></a>Generowanie certyfikatu X.509

Mechanizm [zaświadczania](/azure/iot-dps/concepts-device#attestation-mechanism) używany przez ten przykład jest certyfikat X.509. Aby go wygenerować, musisz użyć narzędzia.

1. W programie VS `F1`Code kliknij przycisk , wpisz i wybierz pozycję **Otwórz nowy terminal,** aby otworzyć okno terminala.

1. Uruchom `dps_cert_gen.exe` `tool` w folderze.

1. Określ skompilowaną `..\.build\DevKitDPS`lokalizację pliku binarnego jako . Następnie wklej **UDS** i **registrationId** właśnie zauważyć w dół. 
  ![Generowanie X.509](media/how-to-connect-mxchip-iot-devkit/gen-x509.png)

1. Certyfikat `.pem` X.509 generuje w tym samym folderze.
  ![Plik X.509](media/how-to-connect-mxchip-iot-devkit/pem-file.png)

## <a name="create-a-device-enrollment-entry"></a>Tworzenie wpisu rejestracji urządzenia

1. W witrynie Azure portal otwórz usługę udostępniania urządzeń, przejdź do sekcji Zarządzanie rejestracjami i kliknij pozycję **Dodaj rejestrację indywidualną**.
  ![Dodawanie rejestracji indywidualnej](media/how-to-connect-mxchip-iot-devkit/add-enrollment.png)

1. Kliknij ikonę pliku obok pozycji **Certyfikat podstawowy .pem lub plik cer,** aby przekazać wygenerowany `.pem` plik.
  ![Prześlij .pem](media/how-to-connect-mxchip-iot-devkit/upload-pem.png)

## <a name="verify-the-devkit-is-registered-with-azure-iot-hub"></a>Sprawdź, czy DevKit jest zarejestrowany w usłudze Azure IoT Hub

Naciśnij przycisk **Resetuj** na devkit. Powinieneś zobaczyć **DPS Connected!** na ekranie DevKit. Po ponownym uruchomieniu urządzenia podejmowane są następujące czynności:

1. Urządzenie wysyła żądanie rejestracji do usługi Device Provisioning.
1. Usługa inicjowania obsługi administracyjnej urządzeń odsyła wyzwanie rejestracji, na które urządzenie odpowiada.
1. Po pomyślnej rejestracji usługa inicjowania obsługi administracyjnej urządzeń wysyła identyfikator URI usługi IoT Hub, identyfikator urządzenia i zaszyfrowany klucz z powrotem do urządzenia.
1. Aplikacja kliencka Usługi IoT Hub na urządzeniu łączy się z koncentratorem.
1. Po pomyślnym połączeniu z koncentratorem zostanie wyświetlone urządzenie wyświetlane w Eksploratorze urządzeń usługi IoT Hub.
  ![Zarejestrowaną](./media/how-to-connect-mxchip-iot-devkit/device-registered.png)

## <a name="problems-and-feedback"></a>Problemy i opinie

Jeśli wystąpią problemy, zapoznaj się z [często zadawanymi pytaniami](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)dotyczącymi programu Iot DevKit lub skontaktuj się z następującymi kanałami, aby uzyskać pomoc techniczną:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Następne kroki

W tym samouczku można zarejestrować urządzenie bezpiecznie do usługi inicjowania obsługi administracyjnej urządzeń przy użyciu aparatu składu tożsamości urządzenia, dzięki czemu urządzenie może automatycznie zarejestrować się w usłudze Azure IoT Hub. 

Podsumowując, dowiedziałeś się, jak:

> [!div class="checklist"]
> * Skonfiguruj globalny punkt końcowy usługi inicjowania obsługi administracyjnej urządzeń na urządzeniu.
> * Użyj unikatowego klucza tajnego urządzenia, aby wygenerować certyfikat X.509.
> * Zarejestruj pojedyncze urządzenie.
> * Sprawdź, czy urządzenie jest zarejestrowane.

Dowiedz się, jak [utworzyć i aprowizować symulowane urządzenie](./quick-create-simulated-device.md).

