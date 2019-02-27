---
title: Łączność urządzeń w usłudze Azure IoT Central | Dokumentacja firmy Microsoft
description: W tym artykule przedstawiono podstawowe pojęcia dotyczące łączności między urządzeniami w usłudze Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: edffc6677609537d8a07aeae45a57c5e88449099
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2019
ms.locfileid: "56882666"
---
# <a name="device-connectivity-in-azure-iot-central"></a>Łączność urządzeń w usłudze Azure IoT Central

W tym artykule przedstawiono podstawowe pojęcia, które odnoszą się do łączności między urządzeniami w programie Microsoft Azure IoT Central.

Używa usługi Azure IoT Central [Azure IoT Hub Device Provisioning Service](https://docs.microsoft.com/azure/iot-dps/about-iot-dps) zarejestrowania się i połącz urządzeń na dużą skalę.

Przy użyciu usługi Device Provisioning Service:

- Klienci mogą teraz Generowanie poświadczenia urządzenia i konfigurowanie urządzenia w trybie offline, bez konieczności najpierw zarejestrować urządzenia w usłudze Azure IoT Central.
- Usługa Azure IoT Central obsługuje połączenie z urządzeniem za pomocą certyfikatów X.509 przerywając do obsługi i usprawnić połączenia przy użyciu sygnatury dostępu współdzielonego.
- Klienci usługi Azure IoT Central teraz można użyć własnych identyfikatory urządzeń do zarejestrowania urządzenia w usłudze Azure IoT Central, która umożliwia prostą integrację z istniejącymi systemami zaplecza biura.
- Istnieje jeden spójny sposób połączyć urządzenia z usługi Azure IoT Central.

>[!NOTE]
>Usługa Azure IoT Central używa IoT Hub Device Provisioning Service dla wszystkich rejestracji urządzeń i połączeń. [Dowiedz się więcej](https://docs.microsoft.com/azure/iot-dps/about-iot-dps).

Oparte na danego przypadku użycia, wykonaj odpowiednie instrukcje, aby podłączyć urządzenia do usługi Azure IoT Central:

- [Łączenie pojedynczego urządzenia szybko (przy użyciu sygnatury dostępu współdzielonego)](#connect-a-single-device)
- [Łączenie urządzeń na dużą skalę przy użyciu sygnatury dostępu współdzielonego](#connect-devices-at-scale-using-shared-access-signatures)
- [Łączenie urządzeń na dużą skalę przy użyciu certyfikatów X.509](#connect-devices-using-x509-certificates) (zalecane dla obciążeń produkcyjnych)
- [Połączenia bez pierwszej rejestracji urządzenia](#connect-without-first-registering-devices) 

>[!NOTE]
>Globalny punkt końcowy dla urządzeń w celu połączenia się i aprowizacji jest **global.azure urządzeń provisioning.net**.

## <a name="connect-a-single-device"></a>Łączenie pojedynczego urządzenia

Aby nawiązać połączenie na jednym urządzeniu usługi Azure IoT Central przy użyciu sygnatury dostępu współdzielonego:

1. Dodaj **rzeczywistego urządzenia** z **Device Explorer**. Wybierz **+ nowy** > **rzeczywistych** dodać rzeczywistego urządzenia.
    - Wprowadź **identyfikator urządzenia** (powinno wskazywać na małe litery) lub identyfikator urządzenia sugerowany.
    - Wprowadź **nazwy urządzenia** lub użyć sugerowanej nazwy.

    ![Dodaj urządzenie](media/concepts-connectivity/add-device.png)

1. Uzyskaj informacje dotyczące połączenia, takich jak **identyfikator zakresu**, **identyfikator urządzenia**, i **klucza podstawowego**, dla dodane urządzenie, wybierając **Connect** na Strona urządzenia.

    - **[Identyfikator zakresu](https://docs.microsoft.com/azure/iot-dps/concepts-device#id-scope)**  odbywa się dla aplikacji usługi Azure IoT Central i jest generowany przez usługę Device Provisioning. Ten identyfikator służy do zapewnienia Unikatowy identyfikator urządzenia w aplikacji.
    - **Identyfikator urządzenia** jest unikatowy identyfikator urządzenia dla aplikacji. Urządzenie musi wysłać Identyfikatora urządzenia jako część wywołania rejestracji.
    - **Klucz podstawowy** token sygnatury dostępu współdzielonego jest generowany w programie Azure IoT Central dla tego konkretnego urządzenia.
 
    ![Szczegóły połączenia](media/concepts-connectivity/device-connect.png)

1. Szczegóły połączenia **tożsamości urządzenia**, **nazwa urządzenia**, a urządzenia **klucz podstawowy** urządzenia kod w celu aprowizowania i podłącz urządzenie i wykonywanych Przepływ danych za pośrednictwem natychmiast. Jeśli używasz urządzenia zestawu deweloperskiego IoT Mxchip (Mxchip), postępuj zgodnie z [instrukcje krok po kroku](howto-connect-devkit-experimental.md#add-a-real-device), poczynając od sekcję "Przygotowanie urządzenie Mxchip."

    Poniżej znajdują się odwołania do innych języków, których możesz chcieć użyć.

    - **Język C:** Postępuj zgodnie z [tego klienta urządzenia przykładowe C](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md) połączenia z urządzeniem próbki. W przykładzie, użyj następujących ustawień:

         ```c
         hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;

         ## Enter the Device Id and Symmetric keys 
         prov_dev_set_symmetric_key_info("<Device Id>", "<Enter Primary Symmetric key here>");
        ```

    - **Node.js:**  Postępuj zgodnie z [instrukcje krok po kroku](tutorial-add-device-experimental.md#prepare-the-client-code), poczynając od sekcję "Przygotowanie kodu klienta."

## <a name="connect-devices-at-scale-by-using-shared-access-signatures"></a>Łączenie urządzeń na dużą skalę przy użyciu sygnatury dostępu współdzielonego

Aby połączyć z urządzeń na dużą skalę za pomocą usługi Azure IoT Central przy użyciu sygnatury dostępu współdzielonego, istnieją dwa ogólne kroki:

1. **Rejestracja urządzenia:** Rejestrowanie urządzeń przez zaimportowanie ich do usługi Azure IoT Central, przy użyciu pliku CSV. Następnie użyj **wyeksportować** akcji, aby wyeksportować swoje urządzenia i uzyskać ich szczegóły połączenia urządzenia.
1. **Konfiguracja urządzenia:** Program urządzenia przy użyciu szczegółów połączenia (**identyfikator zakresu**, **identyfikator urządzenia**, i **klucz podstawowy**). Po włączeniu każde urządzenie wywoła usługi Device Provisioning Service można pobrać jego informacje o połączeniu lub przypisania aplikacji usługi Azure IoT Central.

>[!NOTE]
>Zaawansowana opcja jest również dostępna podłączenia urządzenia bez konieczności najpierw zarejestrować urządzenia w usłudze Azure IoT Central. [Dowiedz się więcej](https://docs.microsoft.com/azure/iot-dps/about-iot-dps).

### <a name="device-registration"></a>Rejestracja urządzenia

Aby połączyć się aplikacji z dużą liczbą urządzeń, usługi Azure IoT Central obsługuje, zbiorczym importowaniem urządzeń przy użyciu pliku CSV. 

1. Importuj urządzenia i zarejestrować je w aplikacji:

   1. Wybierz **Device Explorer** w menu po lewej stronie.
   1. Na lewym panelu, wybierz szablon urządzenia, dla której chcesz zbiorczo utworzyć urządzenia. 
   1. Wybierz **importu**, a następnie wybierz plik CSV, który zawiera listę identyfikatorów urządzeń do zaimportowania. Ten plik CSV powinien zawierać następujące kolumny (i nagłówków):

       - IOTC_DeviceID (powinno wskazywać na małe litery)
       - IOTC_DeviceName (opcjonalnie)

   1. Po zakończeniu importowania w siatce urządzenia wyświetlany jest komunikat o powodzeniu.

1. Eksportowanie urządzeń w celu pobrania ich szczegóły połączenia:

   **Wyeksportować** akcja powoduje utworzenie pliku CSV z Identyfikatorem urządzenia, nazwę urządzenia i klucze urządzeń. Te szczegóły służą do podłączenia urządzenia do usługi Azure IoT Central. Eksport zbiorcze urządzeń z poziomu aplikacji:

   1. Wybierz **Device Explorer** w menu po lewej stronie.
   1. Wybierz urządzenia, które chcesz wyeksportować, a następnie wybierz pozycję **wyeksportować** akcji.
   1. Po zakończeniu eksportu komunikat o powodzeniu jest wyświetlany wraz z linkiem do pobrania wygenerowany plik.
   1. Kliknij komunikat o powodzeniu, aby pobrać plik do lokalnego folderu na dysku.
   1. Wyeksportowany plik CSV będzie zawierał następujące kolumny, które zawierają informacje dotyczące połączenia **identyfikator urządzenia**, **nazwy urządzenia**, **urządzenia podstawowe lub pomocnicze klucze**i  **Podstawowy lub pomocniczy odcisk palca certyfikatu,**:

       - IOTC_DEVICEID
       - IOTC_DEVICENAME
       - IOTC_SASKEY_PRIMARY
       - IOTC_SASKEY_SECONDARY
       - IOTC_X509THUMBPRINT_PRIMARY
       - IOTC_X509THUMBPRINT_SECONDARY

### <a name="device-setup"></a>Konfiguracja urządzenia

 Aby aprowizowania i podłącz urządzenie, użyj szczegółów połączenia **tożsamości urządzenia (IOTC_DEVICEID)**, **urządzenia klucza podstawowego (IOTC_SASKEY_PRIMARY)**, i **identyfikator zakresu** w kodu urządzenia. Jeśli jeszcze tego nie zrobiono, Pobierz **identyfikator zakresu** z poziomu aplikacji usługi Azure IoT Central, wybierając **administracji** > **połączenie z urządzeniem**  >  **Identyfikator zakresu**.

Jeśli łączysz się z urządzeniem Mxchip, postępuj zgodnie z [instrukcje krok po kroku](howto-connect-devkit-experimental.md#add-a-real-device), poczynając od sekcję "Przygotowanie urządzenie Mxchip."

Poniżej znajdują się odwołania do innych języków, których możesz chcieć użyć.

- **Język C:** Postępuj zgodnie z [tego klienta urządzenia przykładowe C](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md) połączenia z urządzeniem próbki. W przykładzie, użyj następujących ustawień:

     ```c
     hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;

     ## Enter the Device Id and Symmetric keys 
     prov_dev_set_symmetric_key_info("<Device Id>", "<Enter Primary Symmetric key here>");
    ```

- **Node.js:** Postępuj zgodnie z [instrukcje krok po kroku](tutorial-add-device-experimental.md#prepare-the-client-code), poczynając od sekcję "Przygotowanie kodu klienta."

## <a name="connect-devices-by-using-x509-certificates"></a>Połącz urządzenia przy użyciu certyfikatów X.509

Za pomocą certyfikatów X.509 jako mechanizmu zaświadczania jest to doskonały sposób na skalowanie produkcji i uprościć Inicjowanie obsługi administracyjnej urządzeń. Certyfikaty X.509 zwykle są rozmieszczone w łańcuchu certyfikatów w relacji zaufania, w którym każdy z certyfikatów w łańcuchu jest podpisany przy użyciu klucza prywatnego w następnym wyższym certyfikatu i tak dalej, kończące się na certyfikat główny z podpisem własnym. Ta struktura ustanawia delegowanego łańcucha zaufania od certyfikatu głównego, który jest generowany przez zaufanego certyfikatu głównego urzędu certyfikacji w dół za pomocą każdego pośredniego urzędu certyfikacji do certyfikatu "liścia" jednostek końcowych, który jest zainstalowany na urządzeniu. Aby dowiedzieć się więcej, zobacz [uwierzytelnianie urządzeń przy użyciu certyfikatów X.509 urzędu certyfikacji](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview). 

Aby podłączyć urządzenia do usługi Azure IoT Central, za pomocą certyfikatów X.509, istnieją trzy kluczowe kroki związane:
 
1. **Konfigurowanie ustawień połączenia** w aplikacji usługi Azure IoT Central, dodając lub sprawdzania X.509 głównego lub pośredniego certyfikatu, który jest używany do generowania certyfikatów urządzeń. Istnieją trzy kroki, aby skonfigurować ustawienia połączenia dla certyfikatów X.509:  

    - **Dodaj X.509 certyfikatu głównego lub pośredniego** używanego do generowania certyfikatów urządzeń liścia. Przejdź do **administracji** > **połączenie z urządzeniem** > **certyfikaty**. 
    
       ![Ustawienia połączenia](media/concepts-connectivity/connection-settings.png)

    - **Sprawdź certyfikat.** Weryfikowanie własności certyfikatu gwarantuje, że przekazujesz certyfikat ma klucz prywatny certyfikatu. Aby zweryfikować certyfikat:

        - Generuj kod weryfikacyjny. Kliknij przycisk Dalej, aby **kod weryfikacyjny** w celu wygenerowania tego kodu. 
        - Utwórz certyfikat X.509 weryfikacji z kodem weryfikacyjnym. Zapisz certyfikat jako plik cer. 
        - Przekaż certyfikat weryfikacji podpisem, a następnie wybierz pozycję **Sprawdź**.

        ![Ustawienia połączenia](media/concepts-connectivity/verify-cert.png)

    - **Dodania dodatkowego certyfikatu.** Podczas cyklu życia rozwiązania IoT należy wdrożyć certyfikaty. Dwa z głównych powodów stopniowe certyfikaty są naruszeń zabezpieczeń i wygaśnięcia certyfikatu. Pomocnicze certyfikaty zredukować przestoje w przypadku urządzeń, które są próby aprowizacji, podczas gdy aktualizujesz podstawowego certyfikatu.

      **Tylko do celów testowych:**
    
      Poniżej przedstawiono niektóre narzędzia wiersza polecenia narzędzia, których można użyć w celu wygenerowania certyfikatów urzędu certyfikacji i certyfikatów urządzeń.

      - Jeśli używasz urządzenia Mxchip, Oto [narzędzia wiersza polecenia](https://aka.ms/iotcentral-docs-dicetool) do generowania certyfikatów urzędu certyfikacji. Dodaj go do aplikacji usługi Azure IoT Central i Sprawdź certyfikaty. 

      - Użyj [to narzędzie wiersza polecenia](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) do:

          - Tworzenie łańcucha certyfikatów (wykonaj: krok 2 w dokumentacji usługi GitHub). Zapisz certyfikaty jako pliki cer i przekazać je do usługi Azure IoT Central (jako podstawowe certyfikaty).
          - Uzyskać kod weryfikacyjny z aplikacji usługi Azure IoT Central, wygeneruj certyfikat (wykonaj krok 3 dokumentację GitHub) i przekaż certyfikat w celu zweryfikowania. 
          - Tworzenie certyfikatów liścia z Identyfikatorem urządzenia, jako parametr do narzędzia (wykonaj krok 4). Zapisz certyfikat i używać go na swoim urządzeniu.

1. **Rejestrowanie urządzeń** przez zaimportowanie ich do usługi Azure IoT Central, przy użyciu pliku CSV.

1. **Konfiguracja urządzenia**: Generowanie certyfikatów liścia za pomocą certyfikatu głównego przekazany. Upewnij się, użyj Identyfikatora urządzenia jako rekord CNAME w certyfikatach liścia i upewnij się, że jest on w małe litery. Oto [narzędzia wiersza polecenia](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) do generowania certyfikatów liścia/urządzenia dla **testowanie wyłącznie do celów**.

1. **Program urządzenia przy użyciu informacji o usłudze aprowizacji**, dzięki czemu może uzyskać jego szczegóły połączenia i przypisania aplikacji usługi Azure IoT Central, gdy jest włączone.

    Więcej informacji można znaleźć w tych artykułach:

    - [Przykładowe zastosowanie dla urządzenia Raspberry Pi](https://aka.ms/iotcentral-docs-Raspi-releases)  

    - [Klient urządzenia przykładowy w języku C](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)

## <a name="connect-without-first-registering-devices"></a>Połączenia bez pierwszej rejestracji urządzenia

Jednym z kluczowych scenariuszy, które obsługuje usługi Azure IoT Central jest dla producentów OEM urządzeń pamięci masowej — producent urządzenia, Generuj poświadczenia i skonfigurować urządzenia w fabryce bez konieczności najpierw zarejestrować urządzenia w usłudze Azure IoT Central. Po urządzenia są włączone i spróbuje połączyć się z usługi Azure IoT Central, operator zatwierdza urządzeniom na łączenie z aplikacji usługi Azure IoT Central.

Poniżej przedstawiono przepływ do łączenia urządzeń za pomocą tej funkcji:

![Ustawienia połączenia](media/concepts-connectivity/device-connection-flow.png)

Wykonaj kroki na podstawie wybranego schematu uwierzytelniania urządzeń (certyfikatów X.509 lub sygnatury dostępu współdzielonego):

1. **Konfigurowanie lub pobieranie ustawień połączenia:**

    - **Certyfikaty X.509:** [Dodanie i zweryfikowanie certyfikatu głównego lub pośredniego](#connect-devices-using-x509-certificates) i używać go w celu wygenerowania certyfikatów urządzeń w następnym kroku.
    - **Sygnatury dostępu współdzielonego:** Skopiuj klucz podstawowy (ten klucz jest klucz sygnatury dostępu współdzielonego grupy dla tej aplikacji usługi Azure IoT Central) i używać go do wygenerowania kluczy sygnatury dostępu urządzeń udostępnionych w następnym kroku.

       ![Ustawienia połączenia dla sygnatury dostępu współdzielonego](media/concepts-connectivity/connection-settings-sas.png)

1. **Generuj poświadczenia urządzenia:**

    - **Certyfikaty X.509:** Generowanie certyfikatów liścia dla urządzeń, za pomocą głównego lub pośredniego certyfikatu, które zostały dodane do tej aplikacji. Upewnij się, użyj Identyfikatora urządzenia jako rekord CNAME w certyfikatach liścia i upewnij się, że jest za małe litery. Oto [narzędzia wiersza polecenia](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) do generowania certyfikatów liścia/urządzenia do testowania.
    - **Sygnatury dostępu współdzielonego:** Aby wygenerować klucze sygnatur dostępu urządzenia udostępnionego, użyj tego [narzędzia wiersza polecenia](https://www.npmjs.com/package/dps-keygen). Użyj klucza podpisu dostępu współdzielonego głównej (klucz sygnatury dostępu współdzielonego grupy) z poprzedniego kroku. Upewnij się, urządzenie, którego identyfikator jest małymi literami.

        Aby uzyskać parametry połączenia urządzenia, użyj następującego polecenia: 

        ```
        npm i -g dps-keygen
        ```
    
        Użyj następującego polecenia, aby wygenerować klucz sygnatury dostępu współdzielonego urządzenia:
                        
        ```
        dps-keygen <Primary_Key(GroupSAS)> <device_id>
        ```

1. **Konfigurowanie urządzeń:** Flash każdego urządzenia przy użyciu **identyfikator zakresu**, **identyfikator urządzenia**, i **klucza SAS/certyfikatu urządzenia**, a następnie Włącz urządzenie, aby podłączyć ją do aplikacji usługi Azure IoT Central.

1. **Podłącz urządzenia do usługi Azure IoT Central:** Po urządzenia są włączone, łączą się z urządzeniem inicjowania obsługi usługi/usługi Azure IoT Central do rejestracji.

1. **Skojarz urządzenia do szablonu:** Połączone urządzenia wyświetlane w obszarze **nieskojarzonych urządzeń** w **Device Explorer**. Stan aprowizacji urządzenia jest **zarejestrowanej**. **Skojarz** urządzeń do szablonu odpowiedniego urządzenia i zatwierdzić urządzeniom na łączenie z aplikacji usługi Azure IoT Central. Urządzenia, Pobierz szczegóły połączenia dla aplikacji usługi Azure IoT Central, a następnie połączyć i rozpocząć wysyłanie danych. Inicjowanie obsługi administracyjnej urządzeń jest zakończony, a **stan aprowizacji** urządzeń przechodzi **Aprowizowana**.

## <a name="device-provisioning-status"></a>Stan aprowizacji urządzenia

Gdy rzeczywistego urządzenia jest podłączony do usługi Azure IoT Central, wykonywane są następujące kroki:

1. **Zarejestrowany**: Najpierw zarejestrowaniu urządzenia, co oznacza, urządzenie zostanie utworzony w usłudze Azure IoT Central i ma identyfikator urządzenia dla urządzenia. Urządzenie zostało zarejestrowane po:

    * Nowe rzeczywistego urządzenia po dodaniu **Device Explorer**
    * Zbiór urządzeń, zostanie dodany przy użyciu **importu** w **Device Explorer**
    * Urządzenia, który nie został zarejestrowany połączy się z prawidłowymi poświadczeniami i jest widoczna w obszarze **nieskojarzonych urządzeń**

    We wszystkich powyższych przypadkach **stan aprowizacji** jest **zarejestrowanej**.

1. **Zainicjowano obsługę administracyjną**: Kiedy urządzenie łączy się z prawidłowymi poświadczeniami, usługi Azure IoT Central ukończeniu kroku inicjowania obsługi administracyjnej (tworząc urządzenia w usłudze IoT Hub). Azure IoT Central następnie zwraca parametry połączenia na urządzeniu, dzięki czemu można połączyć i Rozpocznij wysyłanie danych. Urządzenia **stan aprowizacji** włącza z **zarejestrowanej** do **Aprowizowana**.

1. **Zablokowane**: Operator może zablokować urządzenie. Po zablokowaniu urządzenia nie jest w stanie wysyłać dane do usługi Azure IoT Central i będą musiały być resetowany. Urządzenia, które są blokowane mają **stan aprowizacji** z **zablokowane**. Operator może również odblokować urządzenie. Po jego ma odblokowany, urządzenia **stan aprowizacji** powraca do stanu poprzedniego (**zarejestrowanej** lub **Aprowizowana**). 

## <a name="get-the-device-connection-string"></a>Pobieranie parametrów połączenia urządzenia

Parametry połączenia urządzenia usługi Iot Hub dla usługi Azure IoT Hub można uzyskać, wykonując kroki opisane poniżej:

1. Pobierz szczegóły połączenia urządzenia, takie jak **identyfikator zakresu**, **identyfikator urządzenia**, i **klucza podstawowego** z **połączenie z urządzeniem** strony. Aby uzyskać szczegółowe informacje, przejdź do **urządzenia** strony i wybierz **Connect**. 

    ![Szczegóły połączenia](media/concepts-connectivity/device-connect.png)

1. Pobieranie parametrów połączenia urządzenia przy użyciu narzędzia wiersza polecenia usługi dps-keygen. Aby uzyskać parametry połączenia urządzenia, użyj następującego polecenia:  

    ```cmd/sh
    npm i -g dps-keygen
    ```

    Aby utworzyć parametry połączenia, Znajdź plik binarny, w obszarze *bin /* folderu:

    ```cmd/sh
    dps_cstr <scope_id> <device_id> <Primary Key(for device)>
    ```

    [Dowiedz się więcej o narzędziu dps-keygen](https://www.npmjs.com/package/dps-keygen).

## <a name="sdk-support"></a>Obsługa zestawu SDK

Azure IoT SDKs oferują najłatwiejszy sposób na przy użyciu kodu na urządzeniach z systemem łączących się z aplikacją usługi Azure IoT Central. Dostępne są następujące zestawy SDK:

- [Usługa Azure IoT SDK dla języka C](https://github.com/azure/azure-iot-sdk-c)
- [Usługa Azure IoT SDK dla języka Python](https://github.com/azure/azure-iot-sdk-python)
- [Usługa Azure IoT SDK dla środowiska Node.js](https://github.com/azure/azure-iot-sdk-node)
- [Usługa Azure IoT SDK dla języka Java](https://github.com/azure/azure-iot-sdk-java)
- [Usługa Azure IoT SDK dla platformy .NET](https://github.com/azure/azure-iot-sdk-csharp)

Każde urządzenie łączy się przy użyciu ciągu unikatowego połączenia, który identyfikuje urządzenie. Urządzenie może połączyć się tylko do Centrum IoT, w którym jest zarejestrowany. Podczas tworzenia rzeczywistego urządzenia w aplikacji usługi Azure IoT Central, aplikacja generuje ciąg połączenia do użycia.

## <a name="sdk-features-and-iot-hub-connectivity"></a>Funkcje zestawu SDK i łączność usługi IoT Hub

Cała komunikacja urządzenia z usługą IoT Hub korzysta z następujących opcji łączności usługi IoT Hub:

- [Komunikaty z urządzenia do chmury](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)
- [Bliźniacze reprezentacje urządzeń](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins)

W poniższej tabeli przedstawiono sposób mapowania funkcji usługi Azure IoT Central urządzenia do funkcji usługi IoT Hub:

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| Pomiar: Telemetria | Komunikaty z urządzenia do chmury |
| Właściwości urządzenia | Zgłoszonych właściwości bliźniaka urządzenia |
| Ustawienia | Bliźniacza reprezentacja urządzenia żądanego i zgłaszanych właściwości |

Aby dowiedzieć się więcej o korzystaniu z zestawami SDK Azure IoT, zobacz następujące artykuły dla przykładowego kodu:

- [Łączenie ogólnego klienta Node.js z aplikacją usługi Azure IoT Central](howto-connect-nodejs-experimental.md)
- [Łączenie urządzenia Raspberry Pi z aplikacją usługi Azure IoT Central](howto-connect-raspberry-pi-python.md)
- [Podłącz urządzenie zestawu deweloperskiego IoT Mxchip z aplikacją usługi Azure IoT Central](howto-connect-devkit-experimental.md)


## <a name="protocols"></a>Protokoły

Azure IoT SDKs obsługuje następujące protokoły sieciowe do łączenia się z Centrum IoT:

- MQTT
- AMQP
- HTTPS

Aby uzyskać informacji na temat tych protokołów i wskazówki na temat wybierania jednej, zobacz [wybór protokołu komunikacyjnego](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-protocols).

Jeśli urządzenie nie może użyć żadnych protokołów obsługiwanych, można użyć usługi Azure IoT Edge do protokołu konwersji. IoT Edge obsługuje inne scenariusze analizy na krawędzi Odciążanie przetwarzania do krawędzi z aplikacji usługi Azure IoT Central.

## <a name="security"></a>Bezpieczeństwo

Wszystkie dane wymieniane między urządzeniami i aplikację usługi Azure IoT Central są szyfrowane. IoT Hub uwierzytelnia się każde żądanie z urządzenia, który nawiązuje połączenie z dowolną z punktów końcowych usługi IoT Hub przeznaczonych dla urządzenia. Aby uniknąć wymiany poświadczeń przewodowo, urządzenie używa podpisanych tokenów do uwierzytelniania. Aby uzyskać więcej informacji, zobacz [Kontrola dostępu do centrum IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security).

## <a name="next-steps"></a>Kolejne kroki

- [Przygotuj i podłącz urządzenie z zestawu deweloperskiego IoT Mxchip](howto-connect-devkit-experimental.md)
- [Przygotowanie i Połącz z urządzeniem Raspberry Pi](howto-connect-raspberry-pi-python.md)
- [Łączenie ogólnego klienta Node.js z aplikacją usługi Azure IoT Central](howto-connect-nodejs-experimental.md)
