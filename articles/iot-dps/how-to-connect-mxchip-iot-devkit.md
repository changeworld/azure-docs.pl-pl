---
title: Jak połączyć się z usługą Azure IoT Hub urządzenia inicjowania obsługi administracyjnej przy użyciu zestaw deweloperski IoT MXChip | Dokumentacja firmy Microsoft
description: Jak połączyć się z usługą Azure IoT Hub urządzenia inicjowania obsługi administracyjnej przy użyciu zestaw deweloperski IoT MXChip
services: iot-dps
keywords: ''
author: liydu
ms.author: liydu
ms.date: 02/20/2018
ms.topic: article
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 502f22a39622e9a8341e1daca8c9899fd8b7d7d1
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="connect-the-mxchip-iot-devkit-to-the-azure-iot-hub-device-provisioning-service"></a>Połącz zestaw deweloperski IoT MXChip do inicjowania obsługi usługi urządzeń Centrum IoT Azure

W tym artykule opisano sposób konfigurowania zestaw deweloperski IoT MXChip, aby była automatycznego rejestrowania w usłudze Azure IoT Hub za pomocą usługi Azure IoT urządzenia inicjowania obsługi usługi. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

* Skonfiguruj punkt końcowy globalnej urządzenia świadczenie usługi na urządzeniu.
* Unikatowy klucz tajny (UDS) umożliwia generowanie certyfikatu X.509.
* Rejestrowanie urządzenia indywidualnych.
* Sprawdź, czy urządzenie jest zarejestrowane.

[Zestaw deweloperski IoT MXChip](https://aka.ms/iot-devkit) jest tablicy Arduino zgodnego w jednym z czujników i sformatowanego urządzenia peryferyjne. Można utworzyć dla niego przy użyciu [rozszerzenia Visual Studio Code Arduino](https://aka.ms/arduino). Zestaw deweloperski, który jest dostarczany z rosnącym [katalogu projektów](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) prowadzące własnych rozwiązań Internetu rzeczy (IoT) prototypu, które korzystają z usług Azure.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby wykonać kroki opisane w tym samouczku, należy wykonać następujące zadania:

* Przygotowanie Twojego zestaw deweloperski, wykonując kroki opisane w [połączyć AZ3166 zestaw deweloperski IoT z Centrum IoT Azure w chmurze](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).
* Uaktualnij do najnowszego oprogramowania układowego (1.3.0 lub nowszym) z [zestaw deweloperski aktualizacji oprogramowania układowego](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/) samouczka.
* Utwórz i Połącz z urządzeniem inicjowania obsługi administracyjnej wystąpienie usługi, wykonując kroki opisane w Centrum IoT [Konfigurowanie IoT Hub urządzenia inicjowania obsługi usługi, z portalu Azure](https://docs.microsoft.com/en-us/azure/iot-dps/quick-setup-auto-provision).

## <a name="set-up-the-device-provisioning-service-configuration-on-the-device"></a>Konfigurowanie inicjowania obsługi konfiguracji usługi na urządzeniu urządzeń

Aby podłączyć zestaw deweloperski do inicjowania obsługi utworzone wystąpienie usługi urządzeń:

1. W portalu Azure wybierz **omówienie** okienko urządzenia inicjowania obsługi usługi i zanotuj, dół **punktu końcowego globalnej urządzenia** i **identyfikator zakresu** wartości.
  ![Punktu dystrybucji globalne punktu końcowego i zakres Identyfikatora](./media/how-to-connect-mxchip-iot-devkit/dps-global-endpoint.png)

2. Upewnij się, że masz `git` na komputerze jest zainstalowany i czy jest ona dodawana do zmiennych środowiskowych dostępny do okna poleceń. Zobacz [narzędzia klienta Git oprogramowania swobodę zakresie ochrony jego](https://git-scm.com/download/) mieć zainstalowaną najnowszą wersję.

3. Otwórz wiersz polecenia. Klonowanie repozytorium GitHub dla inicjowania obsługi usługi przykładowy kod urządzeń:
  ```bash
  git clone https://github.com/DevKitExamples/DevKitDPS.git
  ```

4. Otwórz program Visual Studio Code i łączenie się z komputerem zestaw deweloperski, a następnie otwórz folder, który zawiera kod, który można sklonować.

5. Otwórz **DevKitDPS.ino**. Znajdź i Zamień `[Global Device Endpoint]` i `[ID Scope]` z wartościami zapisany w dół.
  ![Punkt końcowy punktu dystrybucji](./media/how-to-connect-mxchip-iot-devkit/endpoint.png) można pozostawić **registrationId** puste. Aplikacja generuje go na podstawie wersji MAC adres i oprogramowania układowego. Jeśli chcesz dostosować identyfikator rejestracji, należy użyć tylko, alfanumeryczne, małe litery i łącznika kombinacji z maksymalnie 128 znaków. Aby uzyskać więcej informacji, zobacz [Zarządzanie rejestracji urządzenia z portalu Azure](https://docs.microsoft.com/en-us/azure/iot-dps/how-to-manage-enrollments).

6. Użyj szybkiego otwarte w kodzie VS (Windows: `Ctrl+P`, macOS: `Cmd+P`) i typ *zadań przekazywania urządzenia* kompilacji i przekazać kod do zestaw deweloperski.

7. W oknie danych wyjściowych pokazuje, czy zadanie zakończyła się powodzeniem.

## <a name="save-a-unique-device-secret-on-an-stsafe-security-chip"></a>Zapisz hasło unikatowych urządzeń w układzie zabezpieczeń STSAFE

Urządzenie usługi inicjowania obsługi administracyjnej można skonfigurować na urządzeniu, na podstawie jego [sprzętowego modułu zabezpieczeń](https://azure.microsoft.com/en-us/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/). Używa zestaw deweloperski IoT MXChip [aparat kompozycji tożsamości urządzenia](https://trustedcomputinggroup.org/wp-content/uploads/Foundational-Trust-for-IOT-and-Resource-Constrained-Devices.pdf) z [Trusted Computing Group](https://trustedcomputinggroup.org). A *Unikatowy klucz tajny* (UDS) zapisywane w zabezpieczeń STSAFE mikroukładu na zestaw deweloperski służy do generowania urządzenia na unikatowy [X.509](https://docs.microsoft.com/en-us/azure/iot-dps/tutorial-set-up-device#select-a-hardware-security-module) certyfikatu. Certyfikat można później dla procesu rejestracji na urządzeniu, inicjowania obsługi usługi.

Typowe urządzenie Unikatowy klucz tajny jest ciągiem 64 znaków, jak pokazano w następującym przykładzie:

```
19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eae
```

Każdego z dwóch znaków jest używany jako wartość szesnastkowy w obliczeniach zabezpieczeń. Powyższego przykładu UDS jest rozwiązywana: `0x19`, `0xe2`, `0x5a`, `0x25`, `0x9d`, `0x0c`, `0x2b`, `0xe0`, `0x3a`, `0x02`, `0xd4`, `0x16` , `0xc0`, `0x5c`, `0x48`, `0xcc`, `0xd0`, `0xcc`, `0x7d`, `0x17`, `0x43`, `0x45`, `0x8a`, `0xae`, `0x1c`, `0xb4`, `0x88`, `0xb0`, `0x74`, `0x99`, `0x3e`, `0xae`.

Aby zapisać klucz tajny unikatowych urządzeń na zestaw deweloperski:

1. Otwórz monitor szeregowe przy użyciu narzędzia, takiego jak Putty. Zobacz [Użyj trybu konfiguracji](https://microsoft.github.io/azure-iot-developer-kit/docs/use-configuration-mode/) szczegółowe informacje.

2. Z zestaw deweloperski podłączone do komputera, przytrzymaj **A** przycisk, a następnie naciśnij i zwolnij **zresetować** przycisk, aby przejść do trybu konfiguracji. Na ekranie zostaną wyświetlone identyfikator zestaw deweloperski i konfiguracji.

3. Pobrać próbkę UDS ciąg i zmienić jeden lub więcej znaków innych wartości między `0` i `f` własne UDS.

4. W oknie Monitora serial wpisz *set_dps_uds [your_own_uds_value]* i wybierz Enter.
  > [!NOTE]
  > Na przykład jeśli ustawisz własne UDS zmieniając ostatnie dwa znaki `f`, należy wprowadzić polecenie następująco: set_dps_uds 19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eff.

5. Bez zamykania okna monitora szeregowych, naciśnij klawisz **zresetować** przycisk na zestaw deweloperski.

6. Należy zanotować **adres MAC zestaw deweloperski** i **wersja oprogramowania układowego zestaw deweloperski** wartości.
  ![Wersja oprogramowania układowego](./media/how-to-connect-mxchip-iot-devkit/firmware-version.png)

## <a name="generate-an-x509-certificate"></a>Generuj certyfikat X.509

### <a name="windows"></a>Windows

1. Otwórz Eksplorator plików i przejdź do folderu, który zawiera inicjowania obsługi usługi przykładowy kod, który można sklonować starszych urządzeń. W **.kompilacja** folderu, Znajdź i kopiowania **DPS.ino.bin** i **DPS.ino.map** w folderze, który zawiera kod.
  ![Wygenerowane pliki](./media/how-to-connect-mxchip-iot-devkit/generated-files.png)
  > [!NOTE]
  > Jeśli zmienisz `built.path` konfiguracji Arduino do innego folderu, należy znaleźć tych plików w folderze, który został skonfigurowany.

2. Wklej tych plików do **narzędzia** folderu na tym samym poziomie z **.kompilacja** folderu.

3. Run **dps_cert_gen.exe**. Postępuj zgodnie z monitami o wprowadzenie Twojej **UDS**, **adres MAC** dla zestaw deweloperski i **wersja oprogramowania układowego** można wygenerować certyfikatu X.509.
  ![Run dps-cert-gen.exe](./media/how-to-connect-mxchip-iot-devkit/dps-cert-gen.png)

4. Po wygenerowaniu certyfikatu X.509, **PEM** certyfikat zostanie zapisana w tym samym folderze.

## <a name="create-a-device-enrollment-entry-in-the-device-provisioning-service"></a>Tworzenie wpisu rejestracji urządzenia w urządzeniu usługi inicjowania obsługi administracyjnej

1. W portalu Azure przejdź do inicjowania obsługi usługi. Wybierz **Zarządzanie rejestracji**, a następnie wybierz **poszczególnych rejestracji** kartę. ![Poszczególne rejestracji](./media/how-to-connect-mxchip-iot-devkit/individual-enrollments.png)

2. Wybierz pozycję **Dodaj**.

3. W **mechanizmu**, wybierz pozycję **X.509**.
  ![Przekazywanie certyfikatu](./media/how-to-connect-mxchip-iot-devkit/upload-cert.png)

4. W **plik PEM lub .cer certyfikatu**, Przekaż **PEM** wygenerowany certyfikat.

5. Pozostaw domyślne, a następnie wybierz pozostałe **zapisać**.

## <a name="start-the-devkit"></a>Uruchom zestaw deweloperski

1. Otwórz kodzie VS i serial monitora.

2. Naciśnij klawisz **zresetować** przycisk na Twoje zestaw deweloperski.

Możesz sprawdzić zestaw deweloperski rozpoczęcia rejestracji usłudze inicjowania obsługi urządzeń.

![Dane wyjściowe programu VS kodu](./media/how-to-connect-mxchip-iot-devkit/vscode-output.png)

## <a name="verify-that-the-devkit-is-registered-with-azure-iot-hub"></a>Sprawdź, czy zestaw deweloperski jest zarejestrowana z Centrum IoT Azure

Po uruchomieniu urządzenia, zostaną wykonane następujące czynności:

1. Urządzenie wysyła żądanie rejestracji na urządzeniu inicjowania obsługi usługi.
2. Urządzenia świadczenie usługi przesyła żądanie rejestracji, na które odpowiada urządzenia.
3. Na pomyślną rejestrację urządzenia świadczenie usługi wysyła identyfikator URI centrum IoT, identyfikator urządzenia i zaszyfrowanego klucza do urządzenia.
4. Aplikacja kliencka Centrum IoT na urządzeniu łączy do Centrum.
5. Na udane połączenie z koncentratorem zobacz temat urządzenia są wyświetlane w Eksploratorze urządzenia Centrum IoT.
  ![Zarejestrowane urządzenia](./media/how-to-connect-mxchip-iot-devkit/device-registered.png)

## <a name="change-the-device-id"></a>Zmień identyfikator urządzenia

Identyfikator urządzenia domyślna zarejestrowana w usłudze Azure IoT Hub jest *AZ3166*. Jeśli chcesz zmodyfikować identyfikator, postępuj zgodnie z instrukcjami [dostosować identyfikator urządzenia](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/).

## <a name="problems-and-feedback"></a>Problemy i opinie

Jeśli wystąpią problemy, zapoznaj się zestaw deweloperski Iot [— często zadawane pytania](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/), lub dotrzeć do następujących kanałów pomocy technicznej:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Witryna Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono zarejestrować urządzenie do urządzenia, świadczenie usługi przy użyciu aparatu kompozycji tożsamości urządzenia, tak aby automatycznie zarejestrować urządzenie z Centrum IoT Azure. 

Podsumowując, przedstawiono sposób:

> [!div class="checklist"]
> * Skonfiguruj punkt końcowy globalnej urządzenia świadczenie usługi na urządzeniu.
> * Klucz tajny unikatowych urządzeń umożliwia generowanie certyfikatu X.509.
> * Rejestrowanie urządzenia indywidualnych.
> * Sprawdź, czy urządzenie jest zarejestrowane.

Dowiedz się, jak [tworzenia i udostępniania symulowane urządzenie](./quick-create-simulated-device.md).

