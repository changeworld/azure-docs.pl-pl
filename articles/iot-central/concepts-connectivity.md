---
title: Łączność urządzeń w usłudze Azure IoT Central | Microsoft Docs
description: W tym artykule przedstawiono kluczowe pojęcia związane z łącznością urządzeń w usłudze Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 04/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 1d8b49b17698d3b527ceab88abdb1a37452f7a06
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69638343"
---
# <a name="device-connectivity-in-azure-iot-central"></a>Łączność urządzeń w usłudze Azure IoT Central

W tym artykule przedstawiono kluczowe pojęcia związane z łącznością urządzeń w Microsoft Azure IoT Central.

Usługa Azure IoT Central używa [usługi azure IoT Hub Device Provisioning Service (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps) do zarządzania wszystkimi rejestracjami i połączeniem urządzeń.

Korzystanie z usługi DPS umożliwia:

- IoT Central do obsługi dołączania i łączenia urządzeń w odpowiedniej skali.
- Można generować poświadczenia urządzenia i konfigurować urządzenia w trybie offline bez rejestrowania urządzeń za pomocą interfejsu użytkownika IoT Central.
- Urządzenia, które mają być połączone za pomocą sygnatur dostępu współdzielonego (SAS).
- Urządzenia, które mają być połączone przy użyciu standardowych certyfikatów X. 509.
- Aby zarejestrować urządzenia w IoT Central, należy użyć własnych identyfikatorów urządzeń. Korzystanie z własnych identyfikatorów urządzeń upraszcza integrację z istniejącymi systemami zaplecza.
- Jeden spójny sposób łączenia urządzeń z IoT Central.

W tym artykule opisano następujące cztery przypadki użycia:

1. [Szybkie łączenie jednego urządzenia przy użyciu sygnatury dostępu współdzielonego](#connect-a-single-device)
1. [Łączenie urządzeń na dużą skalę przy użyciu sygnatury dostępu współdzielonego](#connect-devices-at-scale-using-sas)
1. [Łączenie urządzeń na dużą skalę za pomocą certyfikatów X. 509](#connect-devices-using-x509-certificates) jest to zalecane podejście dla środowisk produkcyjnych.
1. [Nawiązywanie połączenia bez wcześniejszego rejestrowania urządzeń](#connect-without-registering-devices)

## <a name="connect-a-single-device"></a>Podłącz pojedyncze urządzenie

Takie podejście jest przydatne w przypadku eksperymentowania z IoT Central lub testowaniem urządzeń. Możesz użyć informacji o połączeniu urządzenia z aplikacji IoT Central, aby wygenerować parametry połączenia dla urządzenia. Aby uzyskać szczegółowe instrukcje, zobacz [temat Generowanie parametrów połączenia urządzenia w celu nawiązania połączenia z aplikacją platformy Azure IoT Central](howto-generate-connection-string.md).

## <a name="connect-devices-at-scale-using-sas"></a>Łączenie urządzeń na dużą skalę przy użyciu sygnatury dostępu współdzielonego

Aby podłączyć urządzenia do IoT Central na dużą skalę przy użyciu sygnatury dostępu współdzielonego, należy zarejestrować i skonfigurować urządzenia:

### <a name="register-devices-in-bulk"></a>Rejestruj urządzenia luzem

Aby zarejestrować dużą liczbę urządzeń w aplikacji IoT Central, należy [zaimportować identyfikatory urządzeń i nazwy](howto-manage-devices.md#import-devices)urządzeń przy użyciu pliku CSV.

Aby pobrać informacje o połączeniu dla zaimportowanych urządzeń, wyeksportuj [plik CSV z aplikacji IoT Central](howto-manage-devices.md#export-devices).

> [!NOTE]
> Aby dowiedzieć się, jak można podłączyć urządzenia bez wcześniejszego rejestrowania ich w IoT Central, zobacz [Connecting bez wcześniejszego rejestrowania urządzeń](#connect-without-registering-devices).

### <a name="set-up-your-devices"></a>Konfigurowanie urządzeń

Użyj informacji o połączeniu z pliku eksportu w kodzie urządzenia, aby umożliwić urządzeniom łączenie i wysyłanie danych do usługi IoT do aplikacji IoT Central. Aby uzyskać więcej informacji na temat łączenia urządzeń, zobacz [następne kroki](#next-steps).

## <a name="connect-devices-using-x509-certificates"></a>Łączenie urządzeń za pomocą certyfikatów X. 509

W środowisku produkcyjnym używanie certyfikatów X. 509 jest zalecanym mechanizmem uwierzytelniania urządzeń dla IoT Central. Aby dowiedzieć się więcej, zobacz [uwierzytelnianie urządzeń za pomocą certyfikatów X. 509 urzędu certyfikacji](../iot-hub/iot-hub-x509ca-overview.md).

Poniższe kroki opisują sposób łączenia urządzeń z IoT Central przy użyciu certyfikatów X. 509:

1. W aplikacji IoT Central _Dodaj i sprawdź pośredni lub główny certyfikat X. 509_ , który jest używany do generowania certyfikatów urządzeń:

    - Przejdź do opcji **administracja > połączenie z urządzeniem > certyfikaty (X. 509)** i Dodaj certyfikat główny lub pośredni X. 509, którego używasz do generowania certyfikatów urządzeń typu liść.

      ![Ustawienia połączenia](media/concepts-connectivity/connection-settings.png)

      W przypadku naruszenia zabezpieczeń lub wymuszenia wygaśnięcia certyfikatu podstawowego Użyj certyfikatu pomocniczego, aby zmniejszyć przestoje. Podczas aktualizowania certyfikatu podstawowego można nadal inicjować obsługę administracyjną urządzeń przy użyciu certyfikatu pomocniczego.

    - Weryfikowanie własności certyfikatu gwarantuje, że obiektu przekazującego certyfikatu ma klucz prywatny certyfikatu. Aby zweryfikować certyfikat:
        - Wybierz przycisk obok **kodu weryfikacyjnego** , aby wygenerować kod.
        - Utwórz certyfikat weryfikacji X. 509 z kodem weryfikacyjnym wygenerowanym w poprzednim kroku. Zapisz certyfikat jako plik CER.
        - Przekaż podpisany certyfikat weryfikacyjny i wybierz pozycję **Weryfikuj**.

          ![Ustawienia połączenia](media/concepts-connectivity/verify-cert.png)

1. Użyj pliku CSV do zaimportowania _i zarejestrowania urządzeń_ w aplikacji IoT Central.

1. _Konfigurowanie urządzeń._ Wygeneruj certyfikaty liścia przy użyciu przekazanego certyfikatu głównego. Użyj **identyfikatora urządzenia** jako wartości CNAME w certyfikatach liścia. Identyfikator urządzenia powinien składać się ze wszystkich małych liter. Następnie zaprogramowanie urządzeń przy użyciu informacji o usłudze aprowizacji. Gdy urządzenie jest włączane po raz pierwszy, pobiera informacje o połączeniu dla aplikacji IoT Central z usługi DPS.

### <a name="further-reference"></a>Dalsze informacje

- Przykładowa implementacja dla [RaspberryPi.](https://aka.ms/iotcentral-docs-Raspi-releases)

- [Przykładowy klient urządzenia w języku C.](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)

### <a name="for-testing-purposes-only"></a>Tylko do celów testowych

Do celów testowych można użyć tych narzędzi do generowania certyfikatów urzędu certyfikacji i certyfikatów urządzeń.

- W przypadku korzystania z urządzenia DevKit to [Narzędzie wiersza polecenia](https://aka.ms/iotcentral-docs-dicetool) generuje certyfikat urzędu certyfikacji, który można dodać do aplikacji IoT Central w celu zweryfikowania certyfikatów.

- Użyj tego [narzędzia wiersza polecenia](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) , aby:
  - Utwórz łańcuch certyfikatów. Wykonaj krok 2 w artykule w witrynie GitHub.
  - Zapisz certyfikaty jako pliki. cer, aby przekazać je do aplikacji IoT Central.
  - Użyj kodu weryfikacyjnego z aplikacji IoT Central w celu wygenerowania certyfikatu weryfikacji. Wykonaj krok 3 w artykule w witrynie GitHub.
  - Utwórz certyfikaty liści dla urządzeń, korzystając z identyfikatorów urządzeń jako parametru do narzędzia. Wykonaj krok 4 w artykule w witrynie GitHub.

## <a name="connect-without-registering-devices"></a>Łączenie bez rejestrowania urządzeń

Kluczowym scenariuszem IoT Central umożliwia producentom OEM zbiorcze wyprodukowanie urządzeń, które mogą nawiązywać połączenia z aplikacją IoT Central bez wcześniejszego zarejestrowania. Producent musi generować odpowiednie poświadczenia i konfigurować urządzenia w fabryce. Gdy urządzenie jest włączane po raz pierwszy, automatycznie łączy się z aplikacją IoT Central. Aby można było wymusić wysyłanie danych, operator IoT Central musi zatwierdzić urządzenie.

Na poniższym diagramie przedstawiono ten przepływ:

![Ustawienia połączenia](media/concepts-connectivity/device-connection-flow1.png)

Poniższe kroki opisują ten proces bardziej szczegółowo. Kroki różnią się nieco w zależności od tego, czy są używane certyfikaty SAS lub X. 509 na potrzeby uwierzytelniania urządzenia:

1. Skonfiguruj ustawienia połączenia:

    - **Certyfikaty X. 509:** [Dodaj i Sprawdź certyfikat główny/pośredni](#connect-devices-using-x509-certificates) i użyj go do wygenerowania certyfikatów urządzeń w następnym kroku.
    - **SYGNATUR** Skopiuj klucz podstawowy. Ten klucz jest grupowym kluczem SAS dla aplikacji IoT Central. Użyj klucza do wygenerowania kluczy sygnatury dostępu współdzielonego urządzenia w następnym kroku.
    ![Sygnatura dostępu współdzielonego ustawień połączenia](media/concepts-connectivity/connection-settings-sas.png)

1. Generowanie poświadczeń urządzenia
    - **Certyfikaty X. 509:** Wygeneruj certyfikaty liści dla urządzeń przy użyciu certyfikatu głównego lub pośredniego dodanego do aplikacji IoT Central. Upewnij się, że w certyfikatach liścia jest używany **Identyfikator urządzenia** z małymi literami jako rekord CNAME. Tylko do celów testowych Użyj tego [narzędzia wiersza polecenia](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) do generowania certyfikatów urządzeń.
    - **SYGNATUR** Za pomocą tego [narzędzia wiersza polecenia](https://www.npmjs.com/package/dps-keygen) można generować klucze SAS urządzeń. Użyj **klucza podstawowego** grupy z poprzedniego kroku. Identyfikator urządzenia musi mieć małe litery.

      Aby zainstalować [Narzędzie Generator kluczy](https://github.com/Azure/dps-keygen), uruchom następujące polecenie:

      ```cmd/sh
      npm i -g dps-keygen
      ```

      Aby wygenerować klucz urządzenia z klucza podstawowego SAS grupy, uruchom następujące polecenie:

      ```cmd/sh
      dps-keygen -mk:<Primary_Key(GroupSAS)> -di:<device_id>
      ```

1. Aby skonfigurować urządzenia, należy wypróbować każde urządzenie z **identyfikatorem zakresu**, **identyfikatorem urządzenia**i **certyfikatem urządzenia X. 509** lub **kluczem sygnatury**dostępu współdzielonego.

1. Następnie włącz urządzenie, aby połączyć się z aplikacją IoT Central. Po przełączeniu na urządzenie najpierw nawiązuje połączenie z usługą DPS w celu pobrania informacji o rejestracji IoT Central.

1. Podłączone urządzenie początkowo jest wyświetlane jako nieskojarzone **urządzenie** na stronie **Device Explorer** . Stan aprowizacji urządzeń jest **zarejestrowany**. **Skojarz** urządzenie z odpowiednim szablonem urządzenia i zatwierdź urządzenie w celu nawiązania połączenia z aplikacją IoT Central. Następnie urządzenie może pobrać parametry połączenia z IoT Hub i rozpocząć wysyłanie danych. Inicjowanie obsługi urządzeń zostało zakończone, a stan aprowizacji jest teraz **zainicjowany**.

## <a name="provisioning-status"></a>Stan obsługi administracyjnej

Gdy rzeczywiste urządzenie nawiązuje połączenie z aplikacją IoT Central, jego stan aprowizacji zmienia się w następujący sposób:

1. Stan aprowizacji urządzeń jest najpierw **zarejestrowany**. Ten stan oznacza, że urządzenie jest tworzone w IoT Central i ma identyfikator urządzenia. Urządzenie jest rejestrowane, gdy:
    - Na stronie **Device Explorer** zostanie dodane nowe rzeczywiste urządzenie.
    - Zestaw urządzeń zostanie dodany przy użyciu opcji **Importuj** na stronie **Device Explorer** .
    - Urządzenie nie zostało zarejestrowane ręcznie na stronie **Device Explorer** , ale połączono z prawidłowymi poświadczeniami i jest widoczne jako nieskojarzone urządzenie na stronie **Device Explorer** .

1. Stan aprowizacji urządzeń zostanie zmieniony na zainicjowany, gdy urządzenie połączone z aplikacją IoT Central z prawidłowymi poświadczeniami ukończy krok aprowizacji. W tym kroku urządzenie pobiera parametry połączenia z IoT Hub. Urządzenie może teraz połączyć się z IoT Hub i rozpocząć wysyłanie danych.

1. Operator może blokować urządzenie. Gdy urządzenie jest zablokowane, nie może wysyłać danych do aplikacji IoT Central. Zablokowane urządzenia mają stan aprowizacji **zablokowany**. Operator musi zresetować urządzenie, aby można było wznowić wysyłanie danych. Gdy operator odblokowuje urządzenie, stan aprowizacji wraca do jego poprzedniej wartości, **zarejestrowany** lub **zainicjowany**.

## <a name="sdk-support"></a>Obsługa zestawu SDK

Zestawy SDK urządzeń platformy Azure oferują najprostszy sposób implementacji kodu urządzenia. Dostępne są następujące zestawy SDK urządzeń:

- [Zestaw SDK usługi Azure IoT dla języka C](https://github.com/azure/azure-iot-sdk-c)
- [Zestaw SDK usługi Azure IoT dla języka Python](https://github.com/azure/azure-iot-sdk-python)
- [Zestaw SDK usługi Azure IoT dla środowiska Node. js](https://github.com/azure/azure-iot-sdk-node)
- [Zestaw SDK usługi Azure IoT dla języka Java](https://github.com/azure/azure-iot-sdk-java)
- [Zestaw Azure IoT SDK dla platformy .NET](https://github.com/azure/azure-iot-sdk-csharp)

Każde urządzenie nawiązuje połączenie przy użyciu unikatowych parametrów połączenia, które identyfikują urządzenie. Urządzenie może połączyć się tylko z usługą IoT Hub, w której jest zarejestrowany. Gdy utworzysz rzeczywiste urządzenie w aplikacji IoT Central platformy Azure, aplikacja generuje informacje potrzebne do skonstruowania parametrów połączenia przy użyciu `dps-keygen`.

### <a name="sdk-features-and-iot-hub-connectivity"></a>Funkcje zestawu SDK i IoT Hub łączności

Cała komunikacja urządzeń z IoT Hub używa następujących opcji łączności IoT Hub:

- [Obsługa komunikatów przesyłanych z urządzeń do chmury](../iot-hub/iot-hub-devguide-messages-d2c.md)
- [Bliźniaczych reprezentacji urządzenia](../iot-hub/iot-hub-devguide-device-twins.md)

Poniższa tabela zawiera podsumowanie sposobu mapowania funkcji usługi Azure IoT Central Device na funkcje IoT Hub:

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| Miarę Telemetria | Obsługa komunikatów przesyłanych z urządzeń do chmury |
| Właściwości urządzenia | Właściwości zgłoszone przez urządzenie |
| Ustawienia | Wymagane i zgłoszone właściwości dotyczące sznurka urządzenia |

Aby dowiedzieć się więcej o korzystaniu z zestawów SDK urządzeń, zobacz jeden z następujących artykułów dla przykładowego kodu:

- [Łączenie ogólnego klienta Node.js z aplikacją usługi Azure IoT Central](howto-connect-nodejs.md)
- [Łączenie urządzenia z systemem Raspberry Pi z aplikacją usługi Azure IoT Central](howto-connect-raspberry-pi-python.md)
- [Podłącz urządzenie z systemem DevDiv Kit do aplikacji IoT Central platformy Azure](howto-connect-devkit.md).

### <a name="protocols"></a>Protokoły

Zestawy SDK urządzeń obsługują następujące protokoły sieciowe do nawiązywania połączenia z usługą IoT Hub:

- MQTT
- AMQP
- HTTPS

Aby uzyskać informacje o tych protokołach różnicowych i wskazówkach dotyczących wybierania tych protokołów, zobacz [Wybieranie protokołu komunikacyjnego](../iot-hub/iot-hub-devguide-protocols.md).

Jeśli urządzenie nie może użyć żadnego z obsługiwanych protokołów, można użyć Azure IoT Edge do konwersji protokołu. IoT Edge obsługuje inne scenariusze analizy w celu odciążenia przetwarzania do krawędzi z aplikacji IoT Central platformy Azure.

## <a name="security"></a>Bezpieczeństwo

Wszystkie dane wymieniane między urządzeniami a IoT Central platformy Azure są szyfrowane. IoT Hub uwierzytelnia każde żądanie z urządzenia, które nawiązuje połączenie z dowolnymi punktami końcowymi IoT Hub z urządzeniem. Aby uniknąć wymiany poświadczeń za pośrednictwem sieci, urządzenie używa podpisanych tokenów do uwierzytelnienia. Aby uzyskać więcej informacji, zobacz [kontrolowanie dostępu do IoT Hub](../iot-hub/iot-hub-devguide-security.md).

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już o łączności urządzeń w usłudze Azure IoT Central, poniżej przedstawiono sugerowane następne kroki:

- [Przygotowywanie i łączenie urządzenia DevKit](howto-connect-devkit.md)
- [Przygotowywanie i łączenie urządzenia Raspberry Pi](howto-connect-raspberry-pi-python.md)
- [Łączenie ogólnego klienta Node.js z aplikacją usługi Azure IoT Central](howto-connect-nodejs.md)
- [ZESTAW SDK JĘZYKA C: Zestaw SDK klienta urządzenia aprowizacji](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)
