---
title: Aprowizowanie symulowanego urządzenia X.509 dla usługi Azure IoT Hub przy użyciu języka Java | Microsoft Docs
description: Przewodnik Szybki start platformy Azure — tworzenie i aprowizowanie symulowanego urządzenia X.509 za pomocą zestawu SDK urządzenia Java dla usługi IoT Hub Device Provisioning
services: iot-dps
keywords: ''
author: msebolt
ms.author: v-masebo
ms.date: 12/21/2017
ms.topic: quickstart
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: java
ms.custom: mvc
ms.openlocfilehash: 6ff10b982579c8c457c632055e654fefc05858a5
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2018
---
# <a name="create-and-provision-a-simulated-x509-device-using-java-device-sdk-for-iot-hub-device-provisioning-service"></a>Tworzenie i aprowizowanie symulowanego urządzenia X.509 za pomocą zestawu SDK urządzenia Java dla usługi IoT Hub Device Provisioning
[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

Te kroki pokazują, jak zasymulować urządzenie X.509 na maszynie deweloperskiej z systemem operacyjnym Windows OS i użyć przykładowego kodu do połączenia tego symulowanego urządzenia z usługą Device Provisioning Service i Twoim centrum IoT Hub. 

Pamiętaj, aby wcześniej wykonać kroki przedstawione w części [Konfigurowanie usługi IoT Hub Device Provisioning za pomocą witryny Azure Portal](./quick-setup-auto-provision.md).


## <a name="prepare-the-environment"></a>Przygotowywanie środowiska 

1. Upewnij się, że na maszynie zainstalowano środowisko [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

1. Pobierz i zainstaluj pakiet [Maven](https://maven.apache.org/install.html).

1. Upewnij się, że na swojej maszynie masz zainstalowane oprogramowanie `git` i że jest ono dodane do zmiennych środowiskowych dostępnych z okna poleceń. Zobacz stronę z [narzędziami klienckimi Git organizacji Software Freedom Conservancy](https://git-scm.com/download/), aby uzyskać najnowszą wersję narzędzi `git` do zainstalowania, które obejmują powłokę **Git Bash**, czyli aplikację wiersza polecenia, która może służyć do interakcji z lokalnym repozytorium Git. 

1. Otwórz wiersz polecenia. Sklonuj repozytorium GitHub dla przykładu kodu symulacji urządzenia:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```
1. Przejdź do katalogu głównego azure-iot-sdk-java i skompiluj projekt, aby pobrać wszystkie niezbędne pakiety.
   
   ```cmd/sh
   cd azure-iot-sdk-java
   mvn install -DskipTests=true
   ```
1. Przejdź do projektu generatora certyfikatu i skompiluj projekt. 

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator
    mvn clean install
    ```

1. Przejdź do folderu docelowego i wykonaj utworzony plik jar.

    ```cmd/sh
    cd target
    java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
    ```

1. Utwórz informacje o rejestracji w sposób zgodny z konfiguracją:

    - **Rejestracja indywidualna**:

        1. Wprowadź **N** w odpowiedzi na pytanie _Czy chcesz wprowadzić nazwę pospolitą_. Skopiuj do schowka dane wyjściowe z sekcji `Client Cert` rozpoczynające się od wiersza *-----BEGIN CERTIFICATE-----* i kończące się wierszem *-----END CERTIFICATE-----*.

            ![Generator indywidualnego certyfikatu](./media/java-quick-create-simulated-device-x509/individual.png)

        1. Utwórz plik o nazwie **_X509individual.pem_** na maszynie z systemem Windows, otwórz go w wybranym edytorze, a następnie skopiuj zawartość schowka do tego pliku. Zapisz plik.

        1. Wprowadź **N** w odpowiedzi na pytanie _Czy chcesz wprowadzić kod weryfikacyjny_ i nie zamykaj danych wyjściowych programu, ponieważ będą one potrzebne w dalszej części przewodnika Szybki start. Zanotuj wartości _Certyfikat klienta_ i _Klucz prywatny certyfikatu klienta_.
    
    - **Grupy rejestracji**:

        1. Wprowadź **N** w odpowiedzi na pytanie _Czy chcesz wprowadzić nazwę pospolitą_. Skopiuj do schowka dane wyjściowe z sekcji `Root Cert` rozpoczynające się od wiersza *-----BEGIN CERTIFICATE-----* i kończące się wierszem *-----END CERTIFICATE-----*.

            ![Generator certyfikatu grupy](./media/java-quick-create-simulated-device-x509/group.png)

        1. Utwórz plik o nazwie **_X509group.pem_** na maszynie z systemem Windows, otwórz go w wybranym edytorze, a następnie skopiuj zawartość schowka do tego pliku. Zapisz plik.

        1. Wprowadź **Y** w odpowiedzi na pytanie _Czy chcesz wprowadzić kod weryfikacyjny_ i nie zamykaj programu, ponieważ będzie on używany w dalszej części przewodnika Szybki start. Zanotuj wartości _Certyfikat klienta_, _Klucz prywatny certyfikatu klienta_, _Certyfikat osoby podpisującej_ i _Certyfikat główny_.

        > [!NOTE]
        > Wartość `Root Cert` powyżej ma zastosowanie tylko do certyfikatów utworzonych w danych wyjściowych konsoli i nie może służyć do podpisywania dodatkowych certyfikatów klienta. Jeśli potrzebujesz bardziej niezawodnego zestawu certyfikatów testowania, zobacz [Managing CA Certificates Sample (Przykład zarządzania certyfikatami urzędu certyfikacji)](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).
        >

## <a name="create-a-device-enrollment-entry"></a>Tworzenie wpisu rejestracji urządzenia

1. Zaloguj się do witryny Azure Portal, kliknij przycisk **Wszystkie zasoby** w menu po lewej stronie, a następnie otwórz używaną usługę aprowizacji.

1. Wprowadź informacje o rejestracji w sposób zgodny z konfiguracją:

    - **Rejestracja indywidualna**: 

        1. W bloku podsumowania usługi Device Provisioning Service wybierz pozycję **Zarządzaj rejestracjami**. Wybierz kartę **Indywidualne rejestracje** i kliknij u góry przycisk **Dodaj**. 

        1. W obszarze **Dodaj wpis listy rejestracji** wprowadź następujące informacje:
            - Wybierz opcję **X.509** jako *Mechanizm* poświadczania tożsamości.
            - W obszarze *Plik certyfikatu PEM lub CER* wybierz plik certyfikatu **_X509individual.pem_** utworzony w poprzednich krokach za pomocą widżetu *Eksplorator plików*.
            - Opcjonalnie można podać następujące informacje:
                - Wybierz centrum IoT połączone z Twoją usługą aprowizacji.
                - Wprowadź unikatowy identyfikator urządzenia. Nadając nazwę urządzeniu, unikaj korzystania z danych poufnych. 
                - Zaktualizuj pole **Początkowy stan bliźniaczej reprezentacji urządzenia** za pomocą wybranej konfiguracji początkowej dla urządzenia.
            - Gdy skończysz, kliknij przycisk **Zapisz**. 

        ![Wprowadzanie informacji o rejestracji urządzenia X.509 w bloku portalu](./media/java-quick-create-simulated-device-x509/enter-device-enrollment.png)  

       Po pomyślnej rejestracji urządzenie X.509 jest wyświetlane jako **microsoftriotcore** w kolumnie *Identyfikator rejestracji* na karcie *Indywidualne rejestracje*. 

    - **Grupy rejestracji**: 

        1. W bloku podsumowania usługi Device Provisioning wybierz pozycję **Certyfikaty** i kliknij przycisk **Dodaj** na górze.

        1. W obszarze **Dodawanie certyfikatu** wprowadź następujące informacje:
            - Wprowadź unikatową nazwę certyfikatu.
            - Wybierz utworzony wcześniej plik **_X509group.pem_**.
            - Gdy skończysz, kliknij przycisk **Zapisz**.

        ![Dodawanie certyfikatu](./media/java-quick-create-simulated-device-x509/add-certificate.png)

        1. Wybierz nowo utworzony certyfikat:
            - Kliknij pozycję **Generuj kod weryfikacyjny**. Skopiuj wygenerowany kod.
            - Wprowadź _kod weryfikacyjny_ lub kliknij prawym przyciskiem myszy, aby wkleić go w oknie działającego generatora _provisioning-x509-cert-generator_.  Naciśnij klawisz **Enter**.
            - Skopiuj do schowka dane wyjściowe z sekcji `Verification Cert` rozpoczynające się od wiersza *-----BEGIN CERTIFICATE-----* i kończące się wierszem *-----END CERTIFICATE-----*.
            
                ![Generator sprawdzania poprawności](./media/java-quick-create-simulated-device-x509/validation-generator.png)

            - Utwórz plik o nazwie **_X509validation.pem_** na maszynie z systemem Windows, otwórz go w wybranym edytorze, a następnie skopiuj zawartość schowka do tego pliku. Zapisz plik.
            - Wybierz plik **_X509validation.pem_** w witrynie Azure Portal. Kliknij przycisk **Weryfikuj**.

            ![Sprawdzanie poprawności certyfikatu](./media/java-quick-create-simulated-device-x509/validate-certificate.png)

        1. Wybierz pozycję **Zarządzanie rejestracjami**. Wybierz kartę **Grupy rejestracji** i kliknij u góry przycisk **Dodaj**.
            - Wprowadź unikatową nazwę grupy.
            - Wybierz utworzoną wcześniej unikatową nazwę certyfikatu.
            - Opcjonalnie można podać następujące informacje:
                - Wybierz centrum IoT połączone z Twoją usługą aprowizacji.
                - Zaktualizuj pole **Początkowy stan bliźniaczej reprezentacji urządzenia** za pomocą wybranej konfiguracji początkowej dla urządzenia.

        ![Wprowadzanie informacji o rejestracji grupy urządzeń X.509 w bloku portalu](./media/java-quick-create-simulated-device-x509/enter-group-enrollment.png)

        Po pomyślnej rejestracji grupa urządzeń X.509 jest wyświetlana w kolumnie *Nazwa grupy* na karcie *Grupy rejestracji*.


## <a name="simulate-the-device"></a>Symulowanie urządzenia

1. W bloku podsumowania usługi Device Provisioning wybierz pozycję **Przegląd** i zanotuj _Identyfikator zakresu_ oraz _Globalny punkt końcowy usługi aprowizacji_.

    ![Informacje o usłudze](./media/java-quick-create-simulated-device-x509/extract-dps-endpoints.png)

1. Otwórz wiersz polecenia. Przejdź do folderu przykładowego projektu.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-X509-sample
    ```

1. Wprowadź informacje o rejestracji w sposób zgodny z konfiguracją:

    - **Rejestracja indywidualna**: 

        1. Edytuj plik `/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningX509Sample.java`, aby uwzględnić w nim wspomniane wcześniej wartości _Identyfikatora zakresu_ i _Globalnego punktu końcowego usługi aprowizacji_. Uwzględnij też wspomniane wcześniej wartości _Certyfikatu klienta_ i _Klucza prywatnego certyfikatu klienta_.

            ```java
            private static final String idScope = "[Your ID scope here]";
            private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
            private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
            private static final String leafPublicPem = "<Your Public PEM Certificate here>";
            private static final String leafPrivateKey = "<Your Private PEM Key here>";
            ```

            - Aby uwzględnić wartości certyfikatu i klucza, użyj następującego formatu:
            
                ```java
                private static final String leafPublicPem = "-----BEGIN CERTIFICATE-----\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "+XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "-----END CERTIFICATE-----\n";
                private static final String leafPrivateKey = "-----BEGIN PRIVATE KEY-----\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXX\n" +
                    "-----END PRIVATE KEY-----\n";
                ```

    - **Grupy rejestracji**: 

        1. Postępuj zgodnie ze znajdującymi się powyżej instrukcjami dotyczącymi **rejestracji indywidualnej**.

        1. Dodaj następujące wiersze kodu na początku funkcji `main`.
        
            ```java
            String intermediatePem = "<Your Signer Certificate here>";          
            String rootPem = "<Your Root Certificate here>";
                
            signerCertificates.add(intermediatePem);
            signerCertificates.add(rootPem);
            ```
    
            - Aby uwzględnić wartości certyfikatów, użyj następującego formatu:
        
                ```java
                String intermediatePem = "-----BEGIN CERTIFICATE-----\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "+XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "-----END CERTIFICATE-----\n";
                String rootPem = "-----BEGIN CERTIFICATE-----\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "+XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "-----END CERTIFICATE-----\n";
                ```

1. Skompiluj przykład. Przejdź do folderu docelowego i wykonaj utworzony plik jar.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-x509-sample-{version}-with-deps.jar
    ```

1. W portalu przejdź do centrum IoT Hub połączonego z usługą aprowizacji, a następnie otwórz blok **Device Explorer**. Po pomyślnej aprowizacji symulowanego urządzenia X.509 w centrum identyfikator urządzenia jest wyświetlany w bloku **Device Explorer** z pozycją *STATUS* (stan) ustawioną na wartość **enabled** (włączone). Należy pamiętać, że konieczne może być kliknięcie przycisku **Odśwież** znajdującego się u góry, jeśli blok został otwarty przed uruchomieniem przykładowej aplikacji urządzenia. 

    ![Urządzenie jest rejestrowane w centrum IoT](./media/java-quick-create-simulated-device-x509/hub-registration.png) 

> [!NOTE]
> Jeśli zmienisz wartość w polu *Początkowy stan bliźniaczej reprezentacji urządzenia* z domyślnej na inną we wpisie rejestracji dla Twojego urządzenia, może to spowodować pobranie z centrum żądanego stanu reprezentacji bliźniaczej i odpowiednie do niego działanie. Aby uzyskać więcej informacji, zobacz [Opis bliźniaczej reprezentacji urządzenia w usłudze IoT Hub oraz sposoby jej używania](../iot-hub/iot-hub-devguide-device-twins.md).
>


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli planujesz dalszą pracę z przykładem klienta urządzenia i eksplorowanie go, nie czyść zasobów utworzonych w ramach tego przewodnika Szybki start. Jeśli nie planujesz kontynuować pracy, wykonaj poniższe kroki, aby usunąć wszystkie zasoby utworzone w ramach tego przewodnika Szybki start.

1. Zamknij okno danych wyjściowych przykładu klienta urządzenia na swojej maszynie.
1. W witrynie Azure Portal w menu po lewej stronie kliknij pozycję **Wszystkie zasoby**, a następnie wybierz swoją usługę Device Provisioning Service. Otwórz blok **Zarządzanie rejestracjami** dla usługi, a następnie kliknij kartę **Rejestracje indywidualne**. Wybierz *IDENTYFIKATOR REJESTRACJI* urządzenia zarejestrowanego w ramach tego przewodnika Szybki start i kliknij przycisk **Usuń** u góry. 
1. W witrynie Azure Portal w menu po lewej stronie kliknij pozycję **Wszystkie zasoby**, a następnie wybierz swoje centrum IoT. Otwórz blok **Urządzenia IoT** Twojego centrum, wybierz *IDENTYFIKATOR URZĄDZENIA* zarejestrowanego w ramach tego przewodnika Szybki start, a następnie kliknij przycisk **Usuń** u góry.


## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start na Twojej maszynie z systemem Windows utworzono symulowane urządzenie X.509, które zostało następnie zaaprowizowane do Twojego centrum IoT przy użyciu usługi Azure IoT Hub Device Provisioning w portalu. Aby dowiedzieć się, jak zarejestrować urządzenie X.509 programowo, przejdź do przewodnika Szybki start dotyczącego programowej rejestracji urządzeń X.509. 

> [!div class="nextstepaction"]
> [Przewodnik Szybki start platformy Azure — Rejestrowanie urządzenia X.509 w usłudze Azure IoT Hub Device Provisioning](quick-enroll-device-x509-java.md)
