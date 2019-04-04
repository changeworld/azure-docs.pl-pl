---
title: Jak używać usługi Azure IoT Hub Device Provisioning Service automatycznej aprowizacji można zarejestrować zestawu deweloperskiego IoT Mxchip z usługą IoT Hub | Dokumentacja firmy Microsoft
description: Jak używać usługi Azure IoT Hub Device Provisioning Service automatycznej aprowizacji można zarejestrować zestawu deweloperskiego IoT Mxchip z usługą IoT Hub.
author: liydu
ms.author: liydu
ms.date: 12/18/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: jeffya
ms.openlocfilehash: 80e4895e0b276e701a6d7f10d8fc67649db0f188
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58904495"
---
# <a name="use-azure-iot-hub-device-provisioning-service-auto-provisioning-to-register-the-mxchip-iot-devkit-with-iot-hub"></a>Użyj usługi Azure IoT Hub Device Provisioning Service automatycznej aprowizacji można zarejestrować zestawu deweloperskiego IoT Mxchip z usługą IoT Hub

W tym artykule opisano sposób użycia usługi Azure IoT Hub Device Provisioning Service [automatycznej aprowizacji](concepts-auto-provisioning.md), można zarejestrować zestawu deweloperskiego IoT Mxchip z usługą Azure IoT Hub. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

* Skonfiguruj globalny punkt końcowy usługi Device Provisioning na urządzeniu.
* Użyj Unikatowy klucz tajny (domenami aktualizacji), aby wygenerować certyfikat X.509.
* Rejestrowanie poszczególnych urządzeń.
* Sprawdź, czy urządzenie jest zarejestrowane.

[Zestawu deweloperskiego IoT Mxchip](https://aka.ms/iot-devkit) to w jednym z platformą arduino za pomocą zaawansowanych urządzeń peryferyjnych i czujniki. Możesz tworzyć w ramach [Workbench urządzenia IoT Azure](https://aka.ms/iot-workbench) lub [narzędzia IoT Azure](https://aka.ms/azure-iot-tools) pakiet rozszerzenia programu Visual Studio Code. Mxchip jest dostarczany z rosnącej [katalogu projektów](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) przeprowadzenie rozwiązania Internetu rzeczy (IoT) prototypu, które korzystają z usług platformy Azure.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Wykonanie czynności opisanych w tym samouczku, należy najpierw wykonać następujące zadania:

* Konfigurowanie usługi Mxchip Wi-Fi i przygotowywanie środowiska projektowego, wykonując kroki opisane w [połączyć DevKit az3166 usługi IoT dla usługi Azure IoT Hub w chmurze](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).
* Przeprowadź uaktualnienie do najnowszego oprogramowania układowego (1.3.0 lub nowszej) z [Mxchip aktualizacji oprogramowania układowego](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/) samouczka.
* Utwórz i Połącz z wystąpieniem usługi Device Provisioning Centrum IoT Hub, wykonując kroki opisane w [Konfigurowanie IoT Hub Device Provisioning Service, za pomocą witryny Azure portal](/azure/iot-dps/quick-setup-auto-provision).

## <a name="open-sample-project"></a>Otwórz przykładowy projekt

1. Upewnij się, jest Twoja mxchip IoT DevKit **niepołączony** do komputera. Najpierw uruchom program VS Code, a następnie podłącz Mxchip do komputera.

1. Kliknij przycisk `F1` , aby otworzyć paletę poleceń, wpisz i wybierz **Workbench urządzenia IoT platformy Azure: Otwórz przykłady...** . Następnie wybierz pozycję **mxchip IoT DevKit** jako tablica.

1. Na stronie Przykłady aplikacji Workbench IoT, Znajdź **rejestracji urządzeń w usłudze DPS** i kliknij przycisk **Otwórz przykładowy**. Następnie wybiera ścieżki domyślnej, aby pobrać przykładowy kod.
    ![Otwórz przykładowy](media/how-to-connect-mxchip-iot-devkit/open-sample.png)

## <a name="save-a-unique-device-secret-on-device-security-storage"></a>Zapisz Unikatowy klucz tajny urządzenia w magazynie zabezpieczeń urządzenia

Automatyczne inicjowanie obsługi administracyjnej można skonfigurować na urządzeniu, w oparciu o urządzenia [mechanizmu zaświadczania](concepts-security.md#attestation-mechanism). Używa zestawu deweloperskiego IoT Mxchip [aparat kompozycji tożsamości urządzenia](https://trustedcomputinggroup.org/wp-content/uploads/Foundational-Trust-for-IOT-and-Resource-Constrained-Devices.pdf) z [Trusted Computing Group](https://trustedcomputinggroup.org). A **Unikatowy klucz tajny urządzenia** (domenami aktualizacji) zapisywane w mikroukład zabezpieczeń STSAFE ([STSAFE A100](https://microsoft.github.io/azure-iot-developer-kit/docs/understand-security-chip/)) na Mxchip służy do generowania urządzenia, jego unikatowy [certyfikat X.509](concepts-security.md#x509-certificates). Certyfikat jest używany w dalszej części procesu rejestracji w usłudze Device Provisioning i podczas rejestracji w czasie wykonywania.

Typowe domenami aktualizacji jest ciąg 64 znaków, jak pokazano w następującym przykładzie:

```
19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eae
```

Aby zapisać domenami aktualizacji na Mxchip:

1. W programie VS Code kliknij na pasku stanu, aby wybrać COM port do Mxchip.
  ![Wybierz Port COM](media/how-to-connect-mxchip-iot-devkit/select-com.png)

1. Na Mxchip, przytrzymaj wciśnięty **przycisk A**, wypychania i wersji **resetowania** przycisk, a następnie zwolnij **przycisk A**. Twoje Mxchip przechodzi w tryb konfiguracji.

1. Kliknij przycisk `F1` , aby otworzyć paletę poleceń, wpisz i wybierz **Workbench urządzenia IoT platformy Azure: Konfigurowanie ustawień urządzenia... > Parametry unikatowe urządzenia konfiguracji (domenami aktualizacji)**.
  ![Konfigurowanie domenami aktualizacji](media/how-to-connect-mxchip-iot-devkit/config-uds.png)

1. Zanotuj wygenerowany ciąg domenami aktualizacji. Należy go w celu wygenerowania certyfikatu X.509. Następnie naciśnij klawisz `Enter`.
  ![Skopiuj domenami aktualizacji](media/how-to-connect-mxchip-iot-devkit/copy-uds.png)

1. Upewnij się, powiadomienia, że domenami aktualizacji został skonfigurowany na STSAFE pomyślnie.
  ![Konfigurowanie Powodzenie domenami aktualizacji](media/how-to-connect-mxchip-iot-devkit/config-uds-success.png)

> [!NOTE]
> Alternatywnie można skonfigurować domenami aktualizacji za pośrednictwem portu szeregowego, za pomocą narzędzia takiego jak Putty. Postępuj zgodnie z [Użyj trybu konfiguracji](https://microsoft.github.io/azure-iot-developer-kit/docs/use-configuration-mode/) Aby to zrobić.

## <a name="update-the-global-device-endpoint-and-id-scope"></a>Aktualizowanie globalny punkt końcowy urządzenia i identyfikator zakresu

W kodzie urządzenia należy określić [Device provisioning punktu końcowego](/azure/iot-dps/concepts-service#device-provisioning-endpoint) i zakres identyfikatorów w celu zapewnienia izolacji dzierżawy.

1. W witrynie Azure portal wybierz **Przegląd** okienku swojej usługi Device Provisioning i Zapisz **globalny punkt końcowy urządzenia** i **zakres identyfikatorów** wartości.
  ![Inicjowanie obsługi administracyjnej globalny punkt końcowy usługi i zakres identyfikatorów urządzeń](media/how-to-connect-mxchip-iot-devkit/dps-global-endpoint.png)

1. Otwórz **DeKitDPS.ino**. Znajdź i Zamień `[Global Device Endpoint]` i `[ID Scope]` wartościami zanotowanymi w dół.
  ![Punkt końcowy usługi aprowizacji urządzeń](media/how-to-connect-mxchip-iot-devkit/endpoint.png)

1. Wypełnij `registrationId` zmiennej w kodzie. Tylko alfanumeryczne i małych liter, kombinacja łącznik dłuższą niż 128 znaków jest dozwolone. Również zauważyć w dół do wartości.
  ![Identyfikator rejestracji](media/how-to-connect-mxchip-iot-devkit/registration-id.png)

1. Kliknij przycisk `F1`wpisz i wybierz **Workbench urządzenia IoT platformy Azure: Przekazywanie kodu urządzenia**. Uruchamia kompilowania i przekazywanie kodu do Mxchip.
  ![Przekazywanie do urządzenia](media/how-to-connect-mxchip-iot-devkit/device-upload.png)

## <a name="generate-x509-certificate"></a>Generowanie certyfikatu X.509

[Mechanizmu zaświadczania](/azure/iot-dps/concepts-device#attestation-mechanism) używane przez ten przykład jest to certyfikat X.509. Należy użyć narzędzia do jego wygenerowania.

> [!NOTE]
> Generator certyfikatów X.509 obsługuje tylko Windows teraz.

1. W programie VS Code kliknij `F1`wpisz i wybierz **Otwórz nowy Terminal** do Otwórz okno terminalu.

1. Uruchom `dps_cert_gen.exe` w `tool` folderu.

1. Określ lokalizację plików binarnych skompilowanych jako `..\.build\DevKitDPS`. Następnie wklej **domenami aktualizacji** i **identyfikator** zanotowanymi w dół. 
  ![Generowanie X.509](media/how-to-connect-mxchip-iot-devkit/gen-x509.png)

1. A `.pem` generuje certyfikat X.509, w tym samym folderze.
  ![Plik X.509](media/how-to-connect-mxchip-iot-devkit/pem-file.png)

## <a name="create-a-device-enrollment-entry"></a>Tworzenie wpisu rejestracji urządzenia

1. W witrynie Azure portal Otwórz swoją usługę aprowizacji urządzeń, przejdź do zarządzania rejestracjami sekcji, a następnie kliknij przycisk **Dodaj rejestrację indywidualną**.
  ![Dodaj rejestrację indywidualną](media/how-to-connect-mxchip-iot-devkit/add-enrollment.png)

1. Kliknij ikonę pliku **plik PEM lub cer certyfikatu podstawowego** do przekazania `.pem` wygenerowany plik.
  ![Przekaż PEM](media/how-to-connect-mxchip-iot-devkit/upload-pem.png)

## <a name="verify-the-devkit-is-registered-with-azure-iot-hub"></a>Sprawdź, czy Mxchip jest zarejestrowane w usłudze Azure IoT Hub

Naciśnij klawisz **resetowania** przycisk na Twoje Mxchip. Powinien zostać wyświetlony **połączone usługi DPS!** na ekranie Mxchip. Po ponownym uruchomieniu urządzenia, zostaną wykonane następujące czynności:

1. Urządzenie wysyła żądanie rejestracji do usługi Device Provisioning.
1. Usługi Device Provisioning service wysyła z powrotem wezwanie rejestracji, które odpowiada urządzenie.
1. Po pomyślnej rejestracji usługi Device Provisioning service wysyła identyfikator URI centrum IoT, identyfikator urządzenia i zaszyfrowany klucz do urządzenia.
1. Aplikacja kliencka usługi IoT Hub na urządzeniu łączy się z Centrum.
1. Po pomyślnym nawiązaniu połączenia z koncentratorem zobaczysz urządzenia są wyświetlane w Device Explorer Centrum IoT.
  ![Zarejestrowano urządzenie](./media/how-to-connect-mxchip-iot-devkit/device-registered.png)

## <a name="problems-and-feedback"></a>Problemy i opinie

Jeśli napotkasz problemy, zapoznaj się mxchip Iot DevKit [— często zadawane pytania](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/), lub skontaktowanie się z następujących kanałów pomocy technicznej:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono rejestrowanie urządzeń do usługi Device Provisioning przy użyciu aparat kompozycji tożsamości urządzenia, tak, aby automatycznie zarejestrować urządzenie z usługą Azure IoT Hub. 

Podsumowanie pokazaliśmy ci, jak:

> [!div class="checklist"]
> * Skonfiguruj globalny punkt końcowy usługi Device Provisioning na urządzeniu.
> * Użyj Unikatowy klucz tajny, aby wygenerować certyfikat X.509.
> * Rejestrowanie poszczególnych urządzeń.
> * Sprawdź, czy urządzenie jest zarejestrowane.

Dowiedz się, jak [tworzenie i aprowizowanie symulowanego urządzenia](./quick-create-simulated-device.md).

