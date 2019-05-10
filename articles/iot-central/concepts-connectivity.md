---
title: Łączność urządzeń w usłudze Azure IoT Central | Dokumentacja firmy Microsoft
description: W tym artykule przedstawiono podstawowe pojęcia dotyczące łączności między urządzeniami w usłudze Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 04/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: f11d1c6b727799f2cc67c6f5012dc4d117556d2e
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65467807"
---
# <a name="device-connectivity-in-azure-iot-central"></a>Łączność urządzeń w usłudze Azure IoT Central

W tym artykule przedstawiono podstawowe pojęcia dotyczące łączności między urządzeniami w programie Microsoft Azure IoT Central.

Używa usługi Azure IoT Central [Azure IoT Hub Device Provisioning service (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps) do zarządzania wszystkich rejestracji urządzenia i połączenia.

Korzystanie z punktu dystrybucji umożliwia:

- IoT Central do obsługi dołączania i łączenia urządzeń na dużą skalę.
- Generowanie urządzenia poświadczeń i skonfigurować urządzenia w trybie offline, bez konieczności rejestrowania urządzeń za pośrednictwem IoT Central interfejsu użytkownika.
- Urządzenia, aby nawiązać połączenie przy użyciu udostępnionych access signatures (SAS).
- Urządzenia nawiązywanie połączeń za pomocą certyfikatów X.509 będące standardami branżowymi.
- Możesz użyć własnego identyfikatory urządzeń, aby zarejestrować urządzenia w IoT Central. Używanie własnych identyfikatory urządzeń upraszcza integrację z istniejącymi systemami zaplecza biura.
- Pojedynczy i spójny sposób połączyć urządzenia IoT Central.

W tym artykule opisano następujące cztery zastosowań:

1. [Szybkie łączenie pojedynczego urządzenia przy użyciu sygnatury dostępu Współdzielonego](#connect-a-single-device)
1. [Łączenie urządzeń na dużą skalę przy użyciu sygnatury dostępu Współdzielonego](#connect-devices-at-scale-using-sas)
1. [Łączenie urządzeń na dużą skalę za pomocą certyfikatów X.509](#connect-devices-using-x509-certificates) jest to zalecane podejście do środowisk produkcyjnych.
1. [Połączenia bez pierwszej rejestracji urządzenia](#connect-without-registering-devices)

## <a name="connect-a-single-device"></a>Łączenie pojedynczego urządzenia

Takie podejście jest przydatne podczas testowania urządzeń lub eksperymentowanie z IoT Central. Informacje o połączeniu urządzenia z poziomu aplikacji IoT Central umożliwia generowanie parametry połączenia dla urządzenia. Aby uzyskać szczegółowe instrukcje, zobacz [sposób generowania parametrów połączenia urządzenia, aby nawiązać połączenie z aplikacją usługi Azure IoT Central](howto-generate-connection-string.md).

## <a name="connect-devices-at-scale-using-sas"></a>Łączenie urządzeń na dużą skalę przy użyciu sygnatury dostępu Współdzielonego

Aby połączyć urządzenia IoT Central w dużej skali przy użyciu sygnatury dostępu Współdzielonego, należy zarejestrować, a następnie skonfigurować te urządzenia:

### <a name="register-devices-in-bulk"></a>Rejestrowanie urządzeń w trybie zbiorczym

Aby zarejestrować dużą liczbę urządzeń z aplikacją IoT Central, przy użyciu pliku CSV do [Importuj identyfikatory urządzeń i nazwy urządzenia](howto-manage-devices.md#import-devices).

Aby pobrać informacje o połączeniu dla zaimportowanych urządzeń [wyeksportować plik CSV z aplikacji IoT Central](howto-manage-devices.md#export-devices).

> [!NOTE]
> Aby dowiedzieć się, jak można podłączyć urządzenia bez rejestrowania ich pierwszym w IoT Central, zobacz [Połącz bez pierwszej rejestracji urządzenia](#connect-without-registering-devices).

### <a name="set-up-your-devices"></a>Konfigurowanie urządzeń

Użyj informacji o połączeniu z pliku eksportu w kodzie urządzenia, aby włączyć urządzenia do nawiązywania połączeń i wysyłania danych do IoT do Twojej aplikacji IoT Central. Aby uzyskać więcej informacji na temat łączenia urządzeń, zobacz [następne kroki](#next-steps).

## <a name="connect-devices-using-x509-certificates"></a>Połącz urządzenia przy użyciu certyfikatów X.509

W środowisku produkcyjnym za pomocą certyfikatów X.509 jest mechanizm uwierzytelniania zalecane urządzenia usługi IoT Central. Aby dowiedzieć się więcej, zobacz [uwierzytelnianie urządzeń przy użyciu certyfikatu X.509 urzędu certyfikacji](../iot-hub/iot-hub-x509ca-overview.md).

W poniższych krokach opisano, jak połączyć urządzenia IoT Central, za pomocą certyfikatów X.509:

1. W aplikacji IoT Central _Dodaj i sprawdź pośredniego lub głównego certyfikatu X.509_ używane do generowania certyfikatów urządzeń:

    - Przejdź do **Administracja > połączenie z urządzeniem > Certyfikaty (X.509)** i Dodaj X.509 certyfikatu głównego lub pośredniego używane do generowania certyfikatów urządzeń liścia.

      ![Ustawienia połączenia](media/concepts-connectivity/connection-settings.png)

      W przypadku naruszenia zabezpieczeń lub Twój podstawowy certyfikat ma ustawioną datę ważności, należy użyć certyfikatu pomocniczego można ograniczyć przestoje. Można kontynuować do aprowizacji urządzeń przy użyciu certyfikatu pomocniczego podczas aktualizacji certyfikatu podstawowego.

    - Weryfikowanie własności certyfikatu gwarantuje, że przekazujesz certyfikat ma klucz prywatny certyfikatu. Aby zweryfikować certyfikat:
        - Kliknij przycisk Dalej, aby **kod weryfikacyjny** do generowania kodu.
        - Utwórz certyfikat X.509 weryfikacji z kodem weryfikacyjnym, który został wygenerowany w poprzednim kroku. Zapisz certyfikat jako plik cer.
        - Przekaż certyfikat weryfikacji podpisem, a następnie wybierz pozycję **Sprawdź**.

          ![Ustawienia połączenia](media/concepts-connectivity/verify-cert.png)

1. Przy użyciu pliku CSV do _importowania i rejestrowanie urządzeń_ w aplikacji IoT Central.

1. _Konfigurowanie urządzeń._ Generowanie certyfikatów liścia, przy użyciu certyfikatu głównego przekazany. Użyj **identyfikator urządzenia** jako wartość rekordu CNAME w certyfikatach typu liść. Identyfikator urządzenia powinny zawierać tylko małe litery. Zaprogramuj urządzeń za pomocą funkcji udostępniania informacji o usłudze. Gdy urządzenie jest włączone w pierwszym, pobiera jego informacje o połączeniu usługi IoT Central aplikacji z punktu dystrybucji.

### <a name="further-reference"></a>Dalszych odwołań

- Przykładowe zastosowanie dla [RaspberryPi.](https://aka.ms/iotcentral-docs-Raspi-releases)

- [Przykładowy klient urządzenia w C.](https://github.com/Azure/azure-iot-sdk-c/blob/dps_symm_key/provisioning_client/devdoc/using_provisioning_client.md)

### <a name="for-testing-purposes-only"></a>Tylko do celów testowych

Tylko do celów testowych, można użyć tych narzędzi do generowania certyfikatów urzędu certyfikacji i certyfikatów urządzeń.

- Jeśli używasz urządzenia Mxchip to [narzędzia wiersza polecenia](https://aka.ms/iotcentral-docs-dicetool) generuje certyfikat urzędu certyfikacji, można dodać do aplikacji IoT Central, aby zweryfikować certyfikaty.

- Użyj tego [narzędzia wiersza polecenia](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) do:
  - Tworzenie łańcucha certyfikatów. Wykonaj krok 2 w artykule w witrynie GitHub.
  - Zapisz certyfikaty jako pliki cer do przekazania do aplikacji IoT Central.
  - Użyj kodu weryfikacyjnego z aplikacji IoT Central, aby wygenerować certyfikat weryfikacji. Wykonaj krok 3 w artykule w witrynie GitHub.
  - Tworzenie certyfikatów liścia urządzeń przy użyciu identyfikatory urządzeń jako parametr do narzędzia. Wykonaj krok 4 w artykule w witrynie GitHub.

## <a name="connect-without-registering-devices"></a>Połączenia bez rejestracji urządzeń

Kluczowy scenariusz IoT Central umożliwia jest dla producentów OEM do produkcji zbiorcze urządzeń łączących się z aplikacją IoT Central, bez uprzedniego jest zarejestrowany. Producent należy wygenerować odpowiednie poświadczenia i skonfigurować urządzenia w fabryce. Gdy urządzenie włączy po raz pierwszy, jego automatycznie łączy się z aplikacji IoT Central. Operator IoT Central musi zatwierdzić urządzenia, zanim firma stat wysłanie danych jest to możliwe.

Poniższy diagram przedstawia ten przepływ:

![Ustawienia połączenia](media/concepts-connectivity/device-connection-flow1.png)

W poniższych krokach opisano ten proces bardziej szczegółowo. Kroki różnią się nieco w zależności od tego, czy używasz sygnatury dostępu Współdzielonego lub X.509 certyfikatów do uwierzytelniania urządzeń:

1. Konfigurowanie ustawień połączenia:

    - **Certyfikaty X.509:** [Dodanie i zweryfikowanie certyfikatu głównego/pośredni](#connect-devices-using-x509-certificates) i używać go w celu wygenerowania certyfikatów urządzeń w następnym kroku.
    - **SAS:** Skopiuj klucz podstawowy. Ten klucz jest kluczem sygnatury dostępu Współdzielonego grupy aplikacji IoT Central. Użyj klucza do wygenerowania kluczy sygnatury dostępu Współdzielonego urządzenia w następnym kroku.
    ![Ustawienia połączenia sygnatury dostępu Współdzielonego](media/concepts-connectivity/connection-settings-sas.png)

1. Generuj poświadczenia urządzenia
    - **Certyfikaty X.509:** Generowanie certyfikatów liścia urządzenia przy użyciu certyfikatu głównego lub pośredniego dodanych do aplikacji IoT Central. Należy upewnić się, jak małe **identyfikator urządzenia** jako rekord CNAME w certyfikatach typu liść. W przypadku do celów testowych, to użycie [narzędzia wiersza polecenia](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) do generowania certyfikatów urządzeń.
    - **SAS:** Użyj tego [narzędzia wiersza polecenia](https://www.npmjs.com/package/dps-keygen) do wygenerowania kluczy sygnatury dostępu Współdzielonego urządzenia. Użyj grupy **klucz podstawowy** z poprzedniego kroku. Identyfikator urządzenia musi mieć małe.

      Aby zainstalować [klucza narzędzie generatora](https://github.com/Azure/dps-keygen), uruchom następujące polecenie:

      ```cmd/sh
      npm i -g dps-keygen
      ```

      Aby wygenerować klucz urządzenia z grupy klucza podstawowego sygnatury dostępu Współdzielonego, uruchom następujące polecenie:

      ```cmd/sh
      dps-keygen -mk:<Primary_Key(GroupSAS)> -di:<device_id>
      ```

1. Aby skonfigurować urządzenie, flash każdego urządzenia przy użyciu **identyfikator zakresu**, **identyfikator urządzenia**, i **certyfikatu urządzenia X.509** lub **klucza sygnatury dostępu Współdzielonego**.

1. Następnie Włącz urządzenie do nawiązania połączenia z aplikacją IoT Central. Po przełączeniu na urządzeniu, najpierw łączy do punktu dystrybucji do pobrania swoje informacje rejestracyjne IoT Central.

1. Podłączone urządzenie początkowo jest wyświetlany jako **urządzenia nieskojarzone** na **Device Explorer** strony. Na urządzeniu, stan aprowizacji **zarejestrowanej**. **Skojarz** urządzenia do szablonu odpowiedniego urządzenia i zatwierdzić urządzenia podłączyć się do Twojej aplikacji IoT Central. Urządzenie można pobrać parametry połączenia z Centrum IoT i rozpocząć wysyłanie danych. Inicjowanie obsługi administracyjnej urządzeń zostaje zakończony, a stan aprowizacji jest teraz **Aprowizowana**.

## <a name="provisioning-status"></a>Stan obsługi administracyjnej

Gdy rzeczywiste urządzenie łączy się z aplikacji IoT Central, jej udostępniania stan zmienia się w następujący sposób:

1. Stan aprowizacji urządzeń jest pierwszy **zarejestrowanej**. Ten stan oznacza, że urządzenie jest tworzony w IoT Central i zawiera identyfikator urządzenia. Urządzenie zostało zarejestrowane po:
    - Nowe rzeczywistego urządzenia są dodawane na **Device Explorer** strony.
    - Zbiór urządzeń, zostanie dodany przy użyciu **importu** na **Device Explorer** strony.
    - Urządzenie nie zostało zarejestrowane ręcznie na **Device Explorer** strony, ale połączenie z prawidłowymi poświadczeniami i jest widoczny jako **Unassociated** urządzeniu **Device Explorer**strony.

1. Stan aprowizacji urządzeń zmieni się na **Aprowizowana** po zakończeniu kroku inicjowania obsługi administracyjnej przez urządzenia, które są podłączone do aplikacji IoT Central za pomocą prawidłowych poświadczeń. W tym kroku urządzenie pobiera parametry połączenia z usługi IoT Hub. Urządzenie może teraz połączyć się z Centrum IoT i rozpocząć wysyłanie danych.

1. Operator może zablokować urządzenie. Gdy urządzenie jest zablokowane, nie jest w stanie wysyłać dane do aplikacji IoT Central. Zablokowane urządzenia mają stan inicjowania obsługi administracyjnej **zablokowane**. Operator musi zresetować urządzenie, zanim można wznowić, wysyłają dane. Gdy operator odblokowuje urządzenie powraca do poprzedniej wartości, stanu aprowizacji **zarejestrowanej** lub **Aprowizowana**.

## <a name="sdk-support"></a>Obsługa zestawu SDK

Oferty zestawy SDK urządzeń Azure najłatwiej można zaimplementować kodu urządzenia. Dostępne są następujące zestawy SDK urządzeń:

- [Usługa Azure IoT SDK dla języka C](https://github.com/azure/azure-iot-sdk-c)
- [Usługa Azure IoT SDK dla języka Python](https://github.com/azure/azure-iot-sdk-python)
- [Usługa Azure IoT SDK dla środowiska Node.js](https://github.com/azure/azure-iot-sdk-node)
- [Usługa Azure IoT SDK dla języka Java](https://github.com/azure/azure-iot-sdk-java)
- [Usługa Azure IoT SDK dla platformy .NET](https://github.com/azure/azure-iot-sdk-csharp)

Każde urządzenie łączy, przy użyciu parametrów połączenia unikatowy, która identyfikuje urządzenia. Urządzenia mogą łączyć się tylko z Centrum IoT, w którym jest zarejestrowany. Podczas tworzenia rzeczywistego urządzenia w aplikacji usługi Azure IoT Central, aplikacja generuje informacje potrzebne do utworzenia, parametry połączenia za pomocą `dps-keygen`.

### <a name="sdk-features-and-iot-hub-connectivity"></a>Funkcje zestawu SDK i łączność usługi IoT Hub

Cała komunikacja urządzenia z usługą IoT Hub korzysta z następujących opcji łączności usługi IoT Hub:

- [Komunikaty z urządzenia do chmury](../iot-hub/iot-hub-devguide-messages-d2c.md)
- [Bliźniacze reprezentacje urządzeń](../iot-hub/iot-hub-devguide-device-twins.md)

W poniższej tabeli przedstawiono sposób mapowania funkcji usługi Azure IoT Central urządzenia do funkcji usługi IoT Hub:

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| Pomiar: Telemetria | Komunikaty z urządzenia do chmury |
| Właściwości urządzenia | Zgłoszonych właściwości bliźniaka urządzenia |
| Ustawienia | Bliźniacza reprezentacja urządzenia żądanego i zgłaszanych właściwości |

Aby dowiedzieć się więcej o korzystaniu z zestawów SDK urządzeń typu, zobacz jeden z następujących artykułów przykładowy kod:

- [Łączenie ogólnego klienta Node.js z aplikacją usługi Azure IoT Central](howto-connect-nodejs.md)
- [Łączenie urządzenia Raspberry Pi z aplikacją usługi Azure IoT Central](howto-connect-raspberry-pi-python.md)
- [Podłącz urządzenie kit DevDiv do aplikacji usługi Azure IoT Central](howto-connect-devkit.md).

### <a name="protocols"></a>Protokoły

Zestawy SDK urządzeń obsługuje następujące protokoły sieciowe do łączenia się z Centrum IoT:

- MQTT
- AMQP
- HTTPS

Uzyskać informacji o tych protokołów różnicy i wskazówki na temat wybierania jednej, zobacz [wybór protokołu komunikacyjnego](../iot-hub/iot-hub-devguide-protocols.md).

Jeśli urządzenie nie może użyć żadnych protokołów obsługiwanych, można użyć usługi Azure IoT Edge do protokołu konwersji. IoT Edge obsługuje inne scenariusze analizy na krawędzi Odciążanie przetwarzania do krawędzi z aplikacji usługi Azure IoT Central.

## <a name="security"></a>Bezpieczeństwo

Wszystkie dane wymieniane między urządzeniami i usługi Azure IoT Central są szyfrowane. IoT Hub uwierzytelnia się każde żądanie z urządzenia, który nawiązuje połączenie z dowolną z punktów końcowych usługi IoT Hub przeznaczonych dla urządzenia. Aby uniknąć wymiany poświadczeń przewodowo, urządzenie używa podpisanych tokenów do uwierzytelniania. Aby uzyskać więcej informacji znajduje się pozycja [kontrolować dostęp do usługi IoT Hub](../iot-hub/iot-hub-devguide-security.md).

## <a name="next-steps"></a>Kolejne kroki

Skoro wiesz o łączności między urządzeniami w usłudze Azure IoT Central, Oto zalecane kolejne kroki:

- [Przygotuj i podłącz urządzenie Mxchip](howto-connect-devkit.md)
- [Przygotowywanie i łączenie urządzenia Raspberry Pi](howto-connect-raspberry-pi-python.md)
- [Łączenie ogólnego klienta Node.js z aplikacją usługi Azure IoT Central](howto-connect-nodejs.md)
- [ZESTAW SDK C: Aprowizacja urządzenia klienckiego zestawu SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)
