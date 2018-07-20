---
title: Jak używać usługi Azure IoT Hub Device Provisioning Service automatycznej aprowizacji można zarejestrować zestawu deweloperskiego IoT Mxchip z usługą IoT Hub | Dokumentacja firmy Microsoft
description: Jak używać usługi Azure IoT Hub Device Provisioning Service automatycznej aprowizacji można zarejestrować zestawu deweloperskiego IoT Mxchip z usługą IoT Hub.
author: liydu
ms.author: liydu
ms.date: 04/04/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: jeffya
ms.openlocfilehash: 300bde27f956b449d1e0e73f7efb54a13df27b0c
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2018
ms.locfileid: "39145669"
---
# <a name="use-azure-iot-hub-device-provisioning-service-auto-provisioning-to-register-the-mxchip-iot-devkit-with-iot-hub"></a>Użyj usługi Azure IoT Hub Device Provisioning Service automatycznej aprowizacji można zarejestrować zestawu deweloperskiego IoT Mxchip z usługą IoT Hub

W tym artykule opisano sposób użycia usługi Azure IoT Hub Device Provisioning Service [automatycznej aprowizacji](concepts-auto-provisioning.md), można zarejestrować zestawu deweloperskiego IoT Mxchip z usługą Azure IoT Hub. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

* Skonfiguruj globalny punkt końcowy usługi device provisioning na urządzeniu.
* Użyj Unikatowy klucz tajny (domenami aktualizacji), aby wygenerować certyfikat X.509.
* Rejestrowanie poszczególnych urządzeń.
* Sprawdź, czy urządzenie jest zarejestrowane.

[Zestawu deweloperskiego IoT Mxchip](https://aka.ms/iot-devkit) to w jednym z platformą arduino za pomocą zaawansowanych urządzeń peryferyjnych i czujniki. Możesz tworzyć dla niej za pomocą [rozszerzenie programu Visual Studio Code dla Arduino](https://aka.ms/arduino). Mxchip jest dostarczany z rosnącej [katalogu projektów](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) przeprowadzenie rozwiązania Internetu rzeczy (IoT) prototypu, które korzystają z usług platformy Azure.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Wykonanie czynności opisanych w tym samouczku, należy najpierw wykonać następujące zadania:

* Przygotuj swoje Mxchip wykonując kroki opisane w [połączyć DevKit az3166 usługi IoT dla usługi Azure IoT Hub w chmurze](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).
* Przeprowadź uaktualnienie do najnowszego oprogramowania układowego (1.3.0 lub nowszej) z [Mxchip aktualizacji oprogramowania układowego](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/) samouczka.
* Tworzenie i łączenie Centrum IoT Hub z urządzeniem inicjowania obsługi administracyjnej wystąpienia usługi, wykonując kroki opisane w [Konfigurowanie IoT Hub Device Provisioning Service, za pomocą witryny Azure portal](/azure/iot-dps/quick-setup-auto-provision).

## <a name="build-and-deploy-auto-provisioning-registration-software-to-the-device"></a>Tworzenie i wdrażanie automatycznej aprowizacji oprogramowanie do rejestracji na urządzeniu

Aby podłączyć Mxchip do utworzonego wystąpienia usługi aprowizacji urządzeń:

1. W witrynie Azure portal wybierz **Przegląd** urządzenia usługę aprowizacji i notatki w dół w okienku **globalny punkt końcowy urządzenia** i **zakres identyfikatorów** wartości.
  ![Usługa DPS globalny punkt końcowy i zakres identyfikatorów](./media/how-to-connect-mxchip-iot-devkit/dps-global-endpoint.png)

2. Upewnij się, że masz `git` zainstalowana na tym komputerze i że jest ona dodawana do zmiennych środowiskowych dostępnych z okna poleceń. Zobacz [narzędziami klienckimi Git Software Freedom Conservancy](https://git-scm.com/download/) mieć zainstalowaną najnowszą wersję.

3. Otwórz wiersz polecenia. Sklonuj repozytorium GitHub dla usługi przykładowego kodu do aprowizacji urządzeń:
  ```bash
  git clone https://github.com/DevKitExamples/DevKitDPS.git
  ```

4. Otwórz program Visual Studio Code, podłącz Mxchip do komputera, a następnie otwórz folder, który zawiera kod, który zostało sklonowane.

5. Otwórz **DevKitDPS.ino**. Znajdź i Zamień `[Global Device Endpoint]` i `[ID Scope]` wartościami zanotowanymi w dół.
  ![Punkt końcowy usługi DPS](./media/how-to-connect-mxchip-iot-devkit/endpoint.png) można pozostawić **identyfikator** puste. Aplikacja wygeneruje go dla Ciebie na podstawie wersji adresu i oprogramowanie układowe MAC. Chcesz dostosować identyfikator rejestracji, należy użyć tylko alfanumeryczne, małe litery i łącznik kombinacje dłuższą niż 128 znaków. Aby uzyskać więcej informacji, zobacz [Zarządzanie rejestracjami urządzeń przy użyciu witryny Azure portal](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments).

6. Użyj szybkiego otwierania w programie VS Code (Windows: `Ctrl+P`, z systemem macOS: `Cmd+P`) i typ *zadań przekazywania urządzenia* tworzenie i przekazywanie kodu do Mxchip.

7. W oknie danych wyjściowych pokazuje, czy zadanie zakończyło się pomyślnie.

## <a name="save-a-unique-device-secret-on-an-stsafe-security-chip"></a>Zapisz klucz tajny unikatowych urządzeń w układzie zabezpieczeń STSAFE

Automatyczne inicjowanie obsługi administracyjnej można skonfigurować na urządzeniu, w oparciu o urządzenia [mechanizmu zaświadczania](concepts-security.md#attestation-mechanism). Używa zestawu deweloperskiego IoT Mxchip [aparat kompozycji tożsamości urządzenia](https://trustedcomputinggroup.org/wp-content/uploads/Foundational-Trust-for-IOT-and-Resource-Constrained-Devices.pdf) z [Trusted Computing Group](https://trustedcomputinggroup.org). A *urządzenia Unikatowy klucz tajny* (domenami aktualizacji) zapisane w usłudze security STSAFE mikroukład na Mxchip służy do generowania urządzenia, jego unikatowy [certyfikat X.509](concepts-security.md#x509-certificates). Certyfikat jest używany w dalszej części procesu rejestracji na urządzeniu usługi aprowizacji i podczas rejestracji w czasie wykonywania.

Typowe urządzenie Unikatowy klucz tajny jest ciąg 64 znaków, jak pokazano w następującym przykładzie:

```
19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eae
```

Ten ciąg został podzielony na pary znaków, które są używane do obliczania zabezpieczeń. Poprzedni przykład domenami aktualizacji nie zostanie rozwiązany do: `0x19`, `0xe2`, `0x5a`, `0x25`, `0x9d`, `0x0c`, `0x2b`, `0xe0`, `0x3a`, `0x02`, `0xd4`, `0x16` , `0xc0`, `0x5c`, `0x48`, `0xcc`, `0xd0`, `0xcc`, `0x7d`, `0x17`, `0x43`, `0x45`, `0x8a`, `0xae`, `0x1c`, `0xb4`, `0x88`, `0xb0`, `0x74`, `0x99`, `0x3e`, `0xae`.

Aby zapisać wpis tajny unikatowych urządzeń w Mxchip:

1. Otwórz monitor szeregowe przy użyciu narzędzia, takiego jak Putty. Zobacz [Użyj trybu konfiguracji](https://microsoft.github.io/azure-iot-developer-kit/docs/use-configuration-mode/) Aby uzyskać szczegółowe informacje.

2. Za pomocą Mxchip podłączone do komputera, naciśnij i przytrzymaj **A** przycisk, a następnie naciśnij i zwolnij **resetowania** przycisk, aby przejść do trybu konfiguracji. Na ekranie przedstawiono identyfikator Mxchip i konfiguracji.

3. Pobierania próbki domenami aktualizacji ciągu i Zmień co najmniej jeden znak na innych wartości z zakresu od `0` i `f` dla własnego domenami aktualizacji.

4. W oknie Monitora serial wpisz *set_dps_uds [your_own_uds_value]* i naciśnij klawisz Enter.
  > [!NOTE]
  > Na przykład jeśli ustawisz własne domenami aktualizacji, zmieniając ostatnich dwóch znaków do `f`, należy wprowadzić polecenie następująco: `set_dps_uds 19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eff`.

5. Bez konieczności zamykania okna monitora szeregowe, naciśnij klawisz **resetowania** przycisk na Mxchip.

6. Zanotuj **adres MAC Mxchip** i **wersja oprogramowania układowego Mxchip** wartości.
  ![Wersja oprogramowania układowego](./media/how-to-connect-mxchip-iot-devkit/firmware-version.png)

## <a name="generate-an-x509-certificate"></a>Generowanie certyfikatu X.509

### <a name="windows"></a>Windows

1. Otwórz Eksplorator plików i przejdź do folderu, który zawiera inicjowania obsługi usługi przykładowy kod, który zostało wcześniej sklonowane urządzeń. W **.kompilacja** folderu, Znajdź i kopiowanie **DPS.ino.bin** i **DPS.ino.map**.
  ![Wygenerowane pliki](./media/how-to-connect-mxchip-iot-devkit/generated-files.png)
  > [!NOTE]
  > Jeśli zmienisz `built.path` konfiguracji Arduino do innego folderu, musisz znaleźć tych plików w folderze, który został skonfigurowany.

2. Wklej te dwa pliki do **narzędzia** folderu na tym samym poziomie za pomocą **.kompilacja** folderu.

3. Uruchom **dps_cert_gen.exe**. Postępuj zgodnie z monitami, aby wprowadzić swoje **domenami aktualizacji**, **adres MAC** dla Mxchip i **wersja oprogramowania układowego** do wygenerowania certyfikatu X.509.
  ![Run dps-cert-gen.exe](./media/how-to-connect-mxchip-iot-devkit/dps-cert-gen.png)

4. Po wygenerowaniu certyfikatu X.509, **PEM** certyfikatu jest zapisywany w tym samym folderze.

## <a name="create-a-device-enrollment-entry-in-the-device-provisioning-service"></a>Tworzenie wpisu rejestracji urządzenia w usługi device provisioning

1. W witrynie Azure portal przejdź do swojego wystąpienia usługi Device Provisioning. Wybierz **Zarządzanie rejestracjami**, a następnie wybierz pozycję **rejestracje indywidualne** kartę. ![Rejestracje indywidualne](./media/how-to-connect-mxchip-iot-devkit/individual-enrollments.png)

2. Wybierz pozycję **Dodaj**.

3. Na panelu "Dodaj rejestrację":
   - Wybierz **X.509** w obszarze **mechanizm**
   - Kliknij przycisk "Wybierz plik" w obszarze **plik PEM lub cer certyfikatu podstawowego**
   - w oknie dialogowym otwierania pliku, przejdź do, a następnie przekaż **PEM** certyfikatu wygenerowane przed chwilą
   - Pozostaw resztę jako domyślne i kliknij przycisk **Zapisz**

   ![Przekazywanie certyfikatu](./media/how-to-connect-mxchip-iot-devkit/upload-cert.png)

  > [!NOTE]
  > W przypadku błędu oraz tym komunikatem:
  >
  > `{"message":"BadRequest:{\r\n \"errorCode\": 400004,\r\n \"trackingId\": \"1b82d826-ccb4-4e54-91d3-0b25daee8974\",\r\n \"message\": \"The certificate is not a valid base64 string value\",\r\n \"timestampUtc\": \"2018-05-09T13:52:42.7122256Z\"\r\n}"}`
  >
  > Otwórz plik certyfikatu **PEM** jako tekst (Otwórz za pomocą Notatnika lub dowolnego edytora tekstów) i Usuń wiersze:
  >
  > `"-----BEGIN CERTIFICATE-----"` i `"-----END CERTIFICATE-----"`.
  >


## <a name="start-the-devkit"></a>Rozpocznij Mxchip

1. Otwórz program VS Code i szeregowe monitora.

2. Naciśnij klawisz **resetowania** przycisk na Twoje Mxchip.

Początek Mxchip rejestracji zostanie wyświetlony z Twoją usługą aprowizacji urządzenia.

![Wyjście z kodu programu VS](./media/how-to-connect-mxchip-iot-devkit/vscode-output.png)

## <a name="verify-that-the-devkit-is-registered-with-azure-iot-hub"></a>Sprawdź, czy Mxchip jest zarejestrowana przy użyciu usługi Azure IoT Hub

Po uruchomieniu urządzenia, zostaną wykonane następujące czynności:

1. Urządzenie wysyła żądanie rejestracji do swojej usługi device provisioning.
2. Usługi device provisioning przesyła żądanie rejestracji, które odpowiada urządzenie.
3. Po pomyślnej rejestracji usługi device provisioning wysyła identyfikator URI centrum IoT, identyfikator urządzenia i zaszyfrowany klucz do urządzenia.
4. Aplikacja kliencka usługi IoT Hub na urządzeniu łączy się z Centrum.
5. Po pomyślnym nawiązaniu połączenia z koncentratorem zobaczysz urządzenia są wyświetlane w Device Explorer Centrum IoT.
  ![Liczba zarejestrowanych urządzeń](./media/how-to-connect-mxchip-iot-devkit/device-registered.png)

## <a name="change-the-device-id"></a>Zmiana Identyfikatora urządzenia

Identyfikator urządzenia domyślne, które są zarejestrowane w usłudze Azure IoT Hub jest *az3166 usługi*. Jeśli chcesz zmodyfikować identyfikator, postępuj zgodnie z instrukcjami [dostosować identyfikator urządzenia](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/).

## <a name="problems-and-feedback"></a>Problemy i opinie

Jeśli napotkasz problemy, zapoznaj się mxchip Iot DevKit [— często zadawane pytania](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/), lub skontaktowanie się z następujących kanałów pomocy technicznej:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Witryna Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono rejestrowanie urządzenia do usługi device provisioning przy użyciu aparat kompozycji tożsamości urządzenia, tak aby urządzenia automatycznie zarejestrować się w usłudze Azure IoT Hub. 

Podsumowanie pokazaliśmy ci, jak:

> [!div class="checklist"]
> * Skonfiguruj globalny punkt końcowy usługi device provisioning na urządzeniu.
> * Użyj Unikatowy klucz tajny, aby wygenerować certyfikat X.509.
> * Rejestrowanie poszczególnych urządzeń.
> * Sprawdź, czy urządzenie jest zarejestrowane.

Dowiedz się, jak [tworzenie i aprowizowanie symulowanego urządzenia](./quick-create-simulated-device.md).

