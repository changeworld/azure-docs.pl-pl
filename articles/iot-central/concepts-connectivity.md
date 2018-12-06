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
ms.openlocfilehash: 7e90fb6bcfa1bfab59177cbc6c717fefc163a67a
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52960100"
---
# <a name="device-connectivity-in-azure-iot-central"></a>Łączność urządzeń w usłudze Azure IoT Central

W tym artykule przedstawiono podstawowe pojęcia dotyczące łączności między urządzeniami w programie Microsoft Azure IoT Central.

Używa usługi Azure IoT Central [Azure IoT Hub Device Provisioning service (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps), umożliwiając IoT Central do obsługi dołączania i łączenia urządzeń na dużą skalę.

-   Klienci mogą teraz Generowanie poświadczenia urządzenia i skonfigurować urządzenia w trybie offline, bez konieczności najpierw zarejestrować urządzenia w IoT Central
-   IoT Central obsługuje połączenie z urządzeniem z branży zalecane X509 certyfikatu na podstawie łączności, przy jednoczesnym dalszym pomocy technicznej i usprawnić połączenia sygnatury dostępu współdzielonego (SAS)
-   Klienci IoT Central teraz mogą przenieść swoje własne identyfikatory urządzenia, aby zarejestrować urządzenia w IoT Central, włączanie prostą integrację z istniejącymi systemami zaplecza biura
-   Jest spójny sposób połączyć urządzenia IoT Central 

>[!NOTE]
>IoT Central używa usługi Azure IoT Device Provisioning service (DPS) poniżej dla wszystkich rejestracji urządzeń i połączeń, [Dowiedz się więcej o usłudze DPS](https://docs.microsoft.com/azure/iot-dps/about-iot-dps).

Oparte na Twoje użycie przypadków postępuj zgodnie z instrukcjami, aby połączyć urządzenia IoT Central
1. [Szybkie łączenie pojedynczego urządzenia (przy użyciu sygnatury dostępu współdzielonego)](#connect-a-single-device)
1. [Łączenie urządzeń na dużą skalę przy użyciu sygnatury dostępu współdzielonego (SAS)](#connect-devices-at-scale-using-shared-access-signatures)
1. [Łączenie urządzeń na dużą skalę przy użyciu X509 certyfikaty](#connect-devices-using-x509-certificates) **zalecane w przypadku obciążeń produkcyjnych**
1. [Połączenia bez pierwszej rejestracji urządzenia](#connect-without-first-registering-devices) 


>[!NOTE]
>Oto globalnego punktu końcowego dla urządzeń nawiązać połączenie i aprowizować **global.azure urządzeń provisioning.net**.

## <a name="connect-a-single-device"></a>Łączenie pojedynczego urządzenia
Łączenie pojedynczego urządzenia z IoT Central przy użyciu sygnatury dostępu Współdzielonego jest proste i trwa tylko kilka kroków. 
1. Dodaj **rzeczywistego urządzenia** z Device Explorer kliknij **+ nowy > rzeczywistych** dodać rzeczywistego urządzenia.
    * Wprowadź identyfikator urządzenia **<span style="color:Red">(powinno wskazywać na małe litery)</span>** lub sugerowane identyfikator urządzenia.
    * Wprowadź nazwę urządzenia, lub użyć sugerowanej nazwy   
    ![Dodaj urządzenie](media/concepts-connectivity/add-device.png)
1. Pobierz szczegóły połączenia, takich jak **identyfikator zakresu, identyfikator urządzenia i podstawowego klucza** dla dodanego urządzenia, klikając **Connect** na stronie urządzenia.
    * **[Identyfikator zakresu](https://docs.microsoft.com/azure/iot-dps/concepts-device#id-scope)**  jest IoT Central aplikacji i jest generowany przez punkty dystrybucji używane do zapewnienia Unikatowy identyfikator urządzenia w aplikacji.
    * **Identyfikator urządzenia** jest urządzenie Unikatowy identyfikator na aplikację, urządzenie musi wysyłać identyfikator urządzenia jako część wywołania rejestracji.   
    * **Klucz podstawowy** tokenu sygnatury dostępu Współdzielonego jest generowany w programie IoT Central dla tego konkretnego urządzenia. 
    ![Szczegóły połączenia](media/concepts-connectivity/device-connect.PNG)
1. Użyj następujące szczegóły połączenia **tożsamości urządzenia, nazwę urządzenia i klucz podstawowy urządzenia** urządzenia kod w celu aprowizowania i podłącz urządzenie i wykonywanych danych przepływać przez natychmiastowe. Jeśli używasz urządzenia zestawu deweloperskiego postępuj zgodnie z, [szczegółowymi instrukcjami](howto-connect-devkit.md#add-a-real-device), Rozpocznij od sekcji **Przygotuj urządzenie Mxchip**.   

    Poniżej przedstawiono odwołania do innych języków, których możesz chcieć użyć.

    *   **Języka C:** są przy użyciu języka C, należy wykonać [tego klienta urządzenia przykładowe C](https://github.com/Azure/azure-iot-sdk-c/blob/dps_symm_key/provisioning_client/devdoc/using_provisioning_client.md) połączenia z urządzeniem próbki. Użyj następujących ustawień w próbce.   

         ```
         hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
         
         static const char* const SYMMETRIC_KEY_VALUE = "Enter Primary Symmetric key here";

         static const char* const REGISTRATION_NAME = "Enter Device Id here";
        ```

    *   **Node.js:** chcąc używać narzędzia Node.js [instrukcje krok po kroku w tym miejscu użyć](tutorial-add-device.md#prepare-the-client-code), Rozpocznij od sekcji **przygotować kod klienta**.



## <a name="connect-devices-at-scale-using-shared-access-signatures"></a>Łączenie urządzeń na dużą skalę za pomocą sygnatur dostępu współdzielonego

Aby połączyć urządzeń na dużą skalę z usługą IoT Central, przy użyciu sygnatury dostępu Współdzielonego, istnieją dwa kroki związane 
1. **Rejestrowanie urządzeń** przez zaimportowanie ich do IoT Central przy użyciu udostępnionych woluminów Klastra plików i eksportowanie urządzeń przy użyciu szczegółów połączenia urządzeń można używać do łączenia urządzeń
1. **Konfiguracja urządzenia** urządzenia jest zaprogramowane przy użyciu szczegółów połączenia ( **identyfikator zakresu, identyfikator urządzenia i podstawowego klucza**), dzięki czemu może wywołać usługę aprowizacji, aby uzyskać jego połączenia info/IoT przypisania aplikacji centralnej, gdy jest ona włączone.

>[!NOTE]
>Zaawansowana opcja jest również dostępna w przypadku, gdy można podłączyć urządzenia bez konieczności najpierw zarejestrować urządzenia w IoT Central [Dowiedz się więcej tutaj](https://docs.microsoft.com/azure/iot-dps/about-iot-dps).

**Rejestrowanie urządzeń**

Do łączenia z dużą liczbą urządzeń do aplikacji usługi Azure IoT Central oferty zbiorczo importowania urządzeń przy użyciu pliku CSV. 

Wymagania dotyczące pliku CSV: plik CSV powinien mieć następujące kolumny (i nagłówki)
1.  IOTC_DeviceID  **<span style="color:Red">(powinno wskazywać na małe litery)</span>**
1.  IOTC_DeviceName (opcjonalnie)



Importuj urządzenia, aby zarejestrować je w aplikacji
1.  Wybierz **Explorer** w menu nawigacji po lewej stronie.
1.  W lewym panelu wybierz szablon urządzenia, dla którego chcesz zbiorczo utworzyć urządzenia. 
1.  Kliknij przycisk **importu**, wybierz plik CSV, który zawiera listę identyfikatorów urządzeń do zaimportowania.
Ten plik CSV powinien zawierać następujące kolumny (i nagłówki)
    *   IOTC_DeviceID  **<span style="color:Red">(powinno wskazywać na małe litery)</span>**
    *   IOTC_DeviceName (opcjonalnie)
1.  Po zakończeniu importowania w siatce urządzeń wyświetlany jest komunikat o powodzeniu.

Eksportowanie urządzeń, aby uzyskać informacje dotyczące połączenia eksportu jest tworzona w pliku CSV przy użyciu identyfikatora urządzenia, nazwę urządzenia i klucz urządzenia. Za pomocą te szczegóły, aby połączyć urządzenie z IoT Central.
Aby zbiorczo eksportu urządzenia z poziomu aplikacji:
1.  Wybierz **Explorer** w menu nawigacji po lewej stronie.
1.  Wybierz urządzenia, które chcesz wyeksportować, a następnie kliknij przycisk **wyeksportować** akcji.
1.  Po zakończeniu eksportu, komunikat o powodzeniu jest wyświetlany wraz z linkiem do pobrania wygenerowany plik.
1.  Kliknij komunikat o powodzeniu, aby pobrać plik do lokalnego folderu na dysku.
1.  Wyeksportowany plik CSV będzie zawierał następujące informacje kolumn: **identyfikator urządzenia, nazwę urządzenia, klucze podstawowe i pomocnicze urządzeń i podstawowy/pomocniczy odciski palców certyfikatu**
    *   IOTC_DEVICEID
    *   IOTC_DEVICENAME
    *   IOTC_SASKEY_PRIMARY
    *   IOTC_SASKEY_SECONDARY
    *   IOTC_X509THUMBPRINT_PRIMARY 
    *   IOTC_X509THUMBPRINT_SECONDARY


**Konfiguracja urządzenia**

Użyj następujące szczegóły połączenia **tożsamości urządzenia (IOTC_DEVICEID), urządzenia klucza podstawowego (IOTC_SASKEY_PRIMARY) i identyfikator zakresu** urządzenia kod w celu aprowizowania i podłącz urządzenie. Jeśli jeszcze tego nie zrobiono, Pobierz **identyfikator zakresu** z aplikacji IoT Central **Administracja > połączenie z urządzeniem > Identyfikator zakresu**.
Jeśli używasz **zestawu deweloperskiego** urządzeniu łączenie wykonaj [szczegółowe instrukcje](howto-connect-devkit.md#add-a-real-device), Rozpocznij od sekcji **Przygotuj urządzenie Mxchip**.   

Poniżej przedstawiono odwołania do innych języków, których możesz chcieć użyć.

   *   **Języka C:** korzystania z C postępuj zgodnie z [tego klienta urządzenia przykładowe C](https://github.com/Azure/azure-iot-sdk-c/blob/dps_symm_key/provisioning_client/devdoc/using_provisioning_client.md) połączenia z urządzeniem próbki. Użyj następujących ustawień w próbce.   
         ```
         hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;

         static const char* const SYMMETRIC_KEY_VALUE = "Enter Primary Symmetric key here";
         static const char* const REGISTRATION_NAME = "Enter Device Id here";
        ```
    * **Node.js:** chcąc używać narzędzia Node.js [instrukcje krok po kroku w tym miejscu użyć](tutorial-add-device.md#prepare-the-client-code), Rozpocznij od sekcji **przygotować kod klienta**.


## <a name="connect-devices-using-x509-certificates"></a>Połącz urządzenia przy użyciu X509 certyfikatów

Za pomocą X.509 certyfikaty jako mechanizmu zaświadczania jest to doskonały sposób na skalowanie **produkcji** i uprościć Inicjowanie obsługi administracyjnej urządzeń. Certyfikaty X.509 zwykle są rozmieszczone w łańcuchu certyfikatów, w którym każdy z certyfikatów w łańcuchu jest podpisany przy użyciu klucza prywatnego z następnym wyższym certyfikat i tak dalej, kończenie certyfikatu głównego z podpisem własnym zaufania. W ten sposób ustanawiany delegowanego łańcucha zaufania od certyfikatu głównego, wygenerowanego przez zaufany główny urząd certyfikacji (CA) w dół za pomocą każdego pośredniego urzędu certyfikacji do certyfikatu "liścia" jednostek końcowych, które są zainstalowane na urządzeniu. Aby dowiedzieć się więcej, zobacz [uwierzytelnianie urządzeń przy użyciu certyfikatu X.509 urzędu certyfikacji](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview). 

Aby połączyć urządzenia IoT Central przy użyciu X509 certyfikaty są zaangażowani trzy kluczowe kroki 
1. **Konfigurowanie ustawień połączenia** w aplikacji IoT Central, upewniając się, dodając/X509 główny/pośredniego certyfikatu służącego do generowania certyfikatów urządzeń.  Istnieją dwa kroki, aby skonfigurować ustawienia połączenia na potrzeby X509 certyfikatów.  

    *   **Dodaj X509 certyfikatu głównego lub pośredniego** używaną do generowania certyfikatów urządzeń liścia. Przejdź do pozycji Administracja > połączenie z urządzeniem > Certyfikaty. 
    
        ![Ustawienia połączenia](media/concepts-connectivity/connection-settings.PNG)
    *   **Certyfikat weryfikacji:** weryfikowanie własności certyfikatu gwarantuje, że przekazujesz certyfikatu jest w posiadaniu klucza prywatnego certyfikatu. Aby zweryfikować certyfikat
        *  Generuj kod weryfikacyjny, kliknij przycisk obok pola Kod weryfikacji, aby wygenerować kod weryfikacyjny. 
        *  Utwórz certyfikat X.509 weryfikacji z kodem weryfikacyjnym, Zapisz certyfikat jako plik cer. 
        *  Przekazywanie certyfikatu podpisanego weryfikacji i sprawdź, kliknij przycisk.

        ![Ustawienia połączenia](media/concepts-connectivity/verify-cert.png)
    *   **Certyfikat pomocniczy:** podczas cyklu życia rozwiązania IoT, musisz wdrożyć certyfikaty. Dwa z głównych powodów stopniowe certyfikatów będzie naruszenia zabezpieczeń i wygaśnięcia certyfikatu. Pomocnicze certyfikaty są używane do zredukować przestoje w przypadku urządzeń próby aprowizacji, podczas gdy aktualizujesz podstawowego certyfikatu.

    **WYŁĄCZNIE DO CELÓW TESTOWANIA** 
    
    Poniżej przedstawiono niektóre narzędzia wiersza polecenia narzędzia używane do generowania certyfikatów urzędu certyfikacji i certyfikatów urządzeń.

    * Jeśli używasz zestawu deweloperskiego w tym miejscu jest [narzędzia wiersza polecenia](https://aka.ms/iotcentral-docs-dicetool) do generowania urzędu certyfikacji certyfikatów dodać go do swojej aplikacji IoT Central i Sprawdź certyfikaty. 

    *   Użyj tego [narzędzia wiersza polecenia](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) do
        * Tworzenie łańcucha certyfikatów (wykonaj krok 2 w dokumentacji usługi GitHub). 
         Zapisz certyfikatów jako pliki cer i Przekaż do IoT Central (podstawowy).   
        * Uzyskać kod weryfikacyjny z aplikacji IoT Central, wygeneruj certyfikat (wykonaj krok 3 w dokumentacji usługi GitHub) i przekazywania, aby sprawdzić. 
        * Tworzenie certyfikatów liścia urządzeń z systemem identyfikator jako parametr do narzędzia (wykonaj krok 4). Zapisz certyfikat i używać go na swoim urządzeniu.     

1. **Rejestrowanie urządzeń** przez zaimportowanie ich do IoT Central, przy użyciu pliku CSV.

1. **Konfiguracja urządzenia** : generowanie certyfikatów liścia, przy użyciu certyfikatu głównego przekazany. Upewnij się, że używasz **identyfikator urządzenia** jako rekord CNAME w liścia certyfikaty i znajduje się w **małą**. Oto [narzędzia wiersza polecenia](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) do generowania certyfikatów liścia/urządzenia dla **wyłącznie do celów TESTOWYCH**.

    Program urządzenia przy użyciu inicjowania obsługi informacji o usłudze Włączanie go, aby jego szczegóły połączenia i IoT Central po przełączeniu potrzeby przypisywania aplikacji.    

    **Dalsze referene** 
    *   Przykładowe zastosowanie dla [RaspberryPi.](https://aka.ms/iotcentral-docs-Raspi-releases)  

    *   [Przykładowy klient urządzenia w C.](https://github.com/Azure/azure-iot-sdk-c/blob/dps_symm_key/provisioning_client/devdoc/using_provisioning_client.md)

>[!NOTE]
>Użyj **identyfikator urządzenia** jako rekord cname, podczas generowania certyfikatów liścia dla urządzeń.

>[!NOTE]
>**Identyfikator urządzenia** powinny być małymi literami 
 
## <a name="connect-without-first-registering-devices"></a>Połączenia bez pierwszej rejestracji urządzenia
Jest jednym z kluczowych scenariuszy, które IoT Central umożliwia generowanie poświadczenia dla producentów OEM do urządzeń pamięci masowej produkcji i skonfigurować je w fabryce bez konieczności najpierw zarejestrować je w IoT Central. Gdy urządzenia są włączone i nawiązać połączenie z IoT Central operator zatwierdza urządzenia, aby nawiązać połączenie aplikacji IoT Central.

Poniżej przedstawiono przepływ do łączenia urządzeń za pomocą tej funkcji

![Ustawienia połączenia](media/concepts-connectivity/device-connection-flow.PNG)


Postępuj zgodnie z instrukcjami na podstawie wybranego schematu uwierzytelniania urządzeń (X509/SAS)

1. **Ustawienia połączenia** 
    * **X509 certyfikaty:** [Dodaj i Zweryfikuj certyfikat główny/pośredni](#connect-devices-using-x509-certificates) i używać go w celu wygenerowania certyfikatów urządzeń w następnym kroku.
    * **Sygnatury dostępu Współdzielonego:** skopiuj klucz podstawowy (ten klucz jest kluczem sygnatury dostępu Współdzielonego grupy dla tej aplikacji IoT Central) i użyć go do wygenerowania kluczy sygnatury dostępu Współdzielonego urządzenia w następnym kroku. 
![Ustawienia połączenia sygnatury dostępu Współdzielonego](media/concepts-connectivity/connection-settings-sas.png)

1. **Generuj poświadczenia urządzenia** 
    *   **Certyfikaty X509:** generowanie certyfikatów liścia urządzenia przy użyciu certyfikatu głównego/pośredni zostały dodane do tej aplikacji. Upewnij się, że używasz **identyfikator urządzenia** jako rekord cname w certyfikatach liścia i  **<span style="color:Red">(powinno wskazywać na małe litery)</span>**. Oto [narzędzia wiersza polecenia](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) do generowania certyfikatów liścia/urządzenia do testowania.
    *   **Sygnatury dostępu Współdzielonego** klucze sygnatur dostępu Współdzielonego urządzenia mogą być generowane za pomocą tego [narzędzia wiersza polecenia](https://www.npmjs.com/package/dps-keygen). Użyj klucza podstawowego sygnatury dostępu Współdzielonego (klucza SAS grupy) z poprzedniego kroku. Upewnij się, identyfikator urządzenia  **<span style="color:Red">znajduje się w małych liter</span>**.

        Użyj poniższych instrukcji, aby wygenerować klucz sygnatury dostępu Współdzielonego urządzenia           

        ```
        npm i -g dps-keygen
        ```
    
        **Użycie**
                        
        ```
        dps-keygen <Primary_Key(GroupSAS)> <device_id>
        ```

1. **Konfiguracja urządzenia** 
    
     Flash urządzenia przy użyciu **identyfikator zakresu, identyfikator urządzenia klucza SAS/certyfikatu urządzenia** i włączyć na urządzeniu, aby nawiązać połączenie z aplikacji IoT Central.

1. **Połącz urządzenie z IoT Central:** po przełączeniu urządzenia z usługi DPS/IoT Central w celu rejestracji.

1. **Skojarz urządzenia do szablonu:** podłączonego urządzenia będą wyświetlane w obszarze **urządzenia nieskojarzone** w **Device Explorer**. Na urządzeniu, stan aprowizacji **zarejestrowanej**. **Skojarz** urządzenia do szablonu odpowiedniego urządzenia i zatwierdzić urządzenia, aby nawiązać połączenie aplikacji IoT Central. Urządzenie pobiera szczegóły połączeń aplikacji IoT Central, nawiązuje połączenie, a rozpoczyna wysyłanie danych. Provioning urządzenie jest teraz gotowy i *stan aprowizacji* przechodzi **Aprowizowana**.

## <a name="device-provisioning-status"></a>Stan aprowizacji urządzenia
Istnieją pewne czynności związane gdy rzeczywistego urządzenia jest podłączony do usługi Azure IoT Central 
1. **Zarejestrowane**: urządzenie jest pierwszym **zarejestrowanej**, co oznacza, urządzenie zostanie utworzony w IoT Central i zawiera identyfikator urządzenia dla urządzenia.
Urządzenie jest Registeretd po  
    *   Nowe rzeczywistego urządzenia są dodawane na **Explorer**
    *   Zbiór urządzeń zostanie dodany przy użyciu **importu** na **Explorer**
    *   Urządzenie nie zostało zarejestrowane, ale połączy się z prawidłowymi poświadczeniami i jest widoczny w obszarze **nieskojarzonych** urządzeń. 

    We wszystkich powyższych przypadkach *stan aprowizacji* jest **zarejestrowane**

1. **Zainicjowano obsługę administracyjną**: następnym krokiem jest, kiedy urządzenie łączy się z prawidłowymi poświadczeniami IoT Central ukończeniu kroku inicjowania obsługi administracyjnej (tworząc urządzenia w usłudze IoT Hub). Następnie zwraca ciąg połączenia z urządzeniem, aby nawiązać połączenie i rozpocząć wysyłanie danych. Urządzenie *stan aprowizacji* teraz zmienia **zarejestrowanej** do **Aprowizowana**.

1.  **Zablokowane**: operator może zablokować urządzenie, gdy urządzenie zostanie zablokowane nie mogą wysyłać dane do IoT Central, a musi być resetowany. Urządzenia, które są blokowane mają *stan aprowizacji* z **zablokowane**. Operator może również odblokować urządzenie. Po odblokowaniu urządzenia *stan aprowizacji* wróć do jego poprzedniego *stan aprowizacji* (zarejestrowane lub Aprowizowane). 

## <a name="getting-device-connection-string"></a>Pobieranie parametrów połączenia urządzenia
Możesz uzyskać parametry połączenia Centrum Iot hub urządzenia do usługi Azure IoT Hub wykonując następujące kroki 
1. Pobierz szczegóły połączenia, takich jak **identyfikator zakresu, identyfikator urządzenia, klucz podstawowy urządzenia** ze strony urządzenia (stało się na stronie urządzenia > kliknij przycisk Połącz) 

    ![Szczegóły połączenia](media/concepts-connectivity/device-connect.PNG)

1. Pobierz parametry połączenia urządzenia przy użyciu narzędzia wiersza commnd poniżej.
    Użyj poniższych instrukcji, aby uzyskać parametry połączenia urządzenia  

    ```cmd/sh
    npm i -g dps-keygen
    ```
    **Użycie**

    Aby utworzyć parametry połączenia, należy znaleźć pliku binarnego w bin / folder
    ```cmd/sh
    dps_cstr <scope_id> <device_id> <Primary Key(for device)>
    ```
    Dowiedz się więcej o [narzędzie keygen usługi dps w tym miejscu.](https://www.npmjs.com/package/dps-keygen)

## <a name="sdk-support"></a>Obsługa zestawu SDK

Oferty zestawy SDK urządzeń Azure najłatwiej można zaimplementować kod na urządzeniach, które nawiązuje połączenie z aplikacją usługi Azure IoT Central. Dostępne są następujące zestawy SDK urządzeń:

- [Usługa Azure IoT SDK dla języka C](https://github.com/azure/azure-iot-sdk-c)
- [Usługa Azure IoT SDK dla języka Python](https://github.com/azure/azure-iot-sdk-python)
- [Usługa Azure IoT SDK dla środowiska Node.js](https://github.com/azure/azure-iot-sdk-node)
- [Usługa Azure IoT SDK dla języka Java](https://github.com/azure/azure-iot-sdk-java)
- [Usługa Azure IoT SDK dla platformy .NET](https://github.com/azure/azure-iot-sdk-csharp)

Każde urządzenie łączy, przy użyciu parametrów połączenia unikatowy, która identyfikuje urządzenia. Urządzenia mogą łączyć się tylko z Centrum IoT, w którym jest zarejestrowany. Podczas tworzenia rzeczywistego urządzenia w aplikacji usługi Azure IoT Central, aplikacja generuje ciąg połączenia do użycia.

## <a name="sdk-features-and-iot-hub-connectivity"></a>Funkcje zestawu SDK i łączność usługi IoT Hub

Cała komunikacja urządzenia z usługą IoT Hub korzysta z następujących opcji łączności usługi IoT Hub:

- [Komunikaty z urządzenia do chmury](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)
- [Bliźniacze reprezentacje urządzeń](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins)

W poniższej tabeli przedstawiono sposób mapowania funkcji usługi Azure IoT Central urządzenia do funkcji usługi IoT Hub:

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| Miary: Telemetrii | Komunikaty z urządzenia do chmury |
| Właściwości urządzenia | Zgłoszonych właściwości bliźniaka urządzenia |
| Ustawienia | Bliźniacza reprezentacja urządzenia żądanego i zgłaszanych właściwości |

Aby dowiedzieć się więcej o korzystaniu z zestawów SDK urządzeń typu, zobacz jeden z następujących artykułów przykładowy kod:

- [Łączenie ogólnego klienta Node.js z aplikacją usługi Azure IoT Central](howto-connect-nodejs.md)
- [Łączenie urządzenia Raspberry Pi z aplikacją usługi Azure IoT Central](howto-connect-raspberry-pi-python.md)
- [Podłącz urządzenie kit DevDiv do aplikacji usługi Azure IoT Central](howto-connect-devkit.md).


## <a name="protocols"></a>Protokoły

Zestawy SDK urządzeń obsługuje następujące protokoły sieciowe do łączenia się z Centrum IoT:

- MQTT
- AMQP
- HTTPS

Uzyskać informacji o tych protokołów różnicy i wskazówki na temat wybierania jednej, zobacz [wybór protokołu komunikacyjnego](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-protocols).

Jeśli urządzenie nie może użyć żadnych protokołów obsługiwanych, można użyć usługi Azure IoT Edge do protokołu konwersji. IoT Edge obsługuje inne scenariusze analizy na krawędzi Odciążanie przetwarzania do krawędzi z aplikacji usługi Azure IoT Central.

## <a name="security"></a>Bezpieczeństwo

Wszystkie dane wymieniane między urządzeniami i usługi Azure IoT Central są szyfrowane. IoT Hub uwierzytelnia się każde żądanie z urządzenia, który nawiązuje połączenie z dowolną z punktów końcowych usługi IoT Hub przeznaczonych dla urządzenia. Aby uniknąć wymiany poświadczeń przewodowo, urządzenie używa podpisanych tokenów do uwierzytelniania. Aby uzyskać więcej informacji znajduje się pozycja [kontrolować dostęp do usługi IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security).

> [!NOTE]
> Obecnie urządzeń łączących się z usługi Azure IoT Central musi używać tokenów sygnatur dostępu Współdzielonego. Certyfikaty X.509 nie są obsługiwane w przypadku urządzeń łączących się z usługi Azure IoT Central.

## <a name="next-steps"></a>Kolejne kroki

Skoro wiesz już o łączności między urządzeniami w usłudze Azure IoT Central, Oto zalecane kolejne kroki:

- [Przygotuj i podłącz urządzenie Mxchip](howto-connect-devkit.md)
- [Przygotowywanie i łączenie urządzenia Raspberry Pi](howto-connect-raspberry-pi-python.md)
- [Łączenie ogólnego klienta Node.js z aplikacją usługi Azure IoT Central](howto-connect-nodejs.md)
