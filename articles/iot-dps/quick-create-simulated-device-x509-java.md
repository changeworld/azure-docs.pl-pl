---
title: Inicjowanie obsługi symulowanego urządzenia X. 509 na platformie Azure IoT Hub przy użyciu języka Java
description: 'Przewodnik Szybki start platformy Azure: tworzenie i aprowizowanie symulowanego urządzenia X.509 za pomocą zestawu SDK urządzenia języka Java dla usługi IoT Hub Device Provisioning. W tym przewodniku Szybki start używane są rejestracje indywidualne.'
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: java
ms.custom: mvc
ms.openlocfilehash: ec4c276b951e5cda43a4b224f25aac547cd26bec
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229569"
---
# <a name="quickstart-create-and-provision-a-simulated-x509-device-using-java-device-sdk-for-iot-hub-device-provisioning-service"></a>Szybki Start: Tworzenie i Inicjowanie obsługi symulowanego urządzenia X. 509 za pomocą zestawu SDK urządzenia Java dla IoT Hub Device Provisioning Service
[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

Te kroki pokazują, jak zasymulować urządzenie X.509 na maszynie deweloperskiej z systemem operacyjnym Windows OS i użyć przykładowego kodu do połączenia tego symulowanego urządzenia z usługą Device Provisioning Service i Twoim centrum IoT Hub. 

Jeśli nie znasz procesu automatycznego aprowizowania, zapoznaj się również z tematem [Auto-provisioning concepts (Pojęcia związane z automatycznym aprowizowaniem)](concepts-auto-provisioning.md). Pamiętaj również, aby wcześniej wykonać kroki przedstawione w części [Konfigurowanie usługi IoT Hub Device Provisioning za pomocą witryny Azure Portal](./quick-setup-auto-provision.md). 

Usługa Azure IoT Device Provisioning obsługuje dwa typy rejestracji:
- [Grupy rejestracji](concepts-service.md#enrollment-group): służą do rejestrowania wielu pokrewnych urządzeń.
- [Rejestracje indywidualne](concepts-service.md#individual-enrollment): służą do rejestrowania pojedynczych urządzeń.

W tym artykule przedstawiono rejestracje indywidualne.

## <a name="prepare-the-environment"></a>Przygotowywanie środowiska 

1. Upewnij się, że na maszynie zainstalowano środowisko [Java SE Development Kit 8](https://aka.ms/azure-jdks).

2. Pobierz i zainstaluj pakiet [Maven](https://maven.apache.org/install.html).

3. Upewnij się, że na Twojej maszynie jest zainstalowane oprogramowanie Git i że jest ono dodane do zmiennych środowiskowych dostępnych z okna poleceń. Zobacz stronę z [narzędziami klienckimi Git organizacji Software Freedom Conservancy](https://git-scm.com/download/), aby uzyskać najnowszą wersję narzędzi `git` do zainstalowania, które obejmują powłokę **Git Bash**, czyli aplikację wiersza polecenia, która może służyć do interakcji z lokalnym repozytorium Git. 

4. Otwórz wiersz polecenia. Sklonuj repozytorium GitHub dla przykładu kodu symulacji urządzenia:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```
5. Przejdź do katalogu głównego`azure-iot-sdk-`java` i skompiluj projekt, aby pobrać wszystkie niezbędne pakiety.
   
   ```cmd/sh
   cd azure-iot-sdk-java
   mvn install -DskipTests=true
   ```
6. Przejdź do projektu generatora certyfikatu i skompiluj projekt. 

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator
    mvn clean install
    ```

## <a name="create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry"></a>Tworzenie certyfikatu urządzenia X.509 z podpisem własnym i wpisu rejestracji indywidualnej

W tej sekcji zostanie użyty certyfikat z podpisem własnym X.509. Ważne jest, aby pamiętać o następujących kwestiach:

* Certyfikaty z podpisem własnym są przeznaczone tylko do celów testowania i nie powinny być używane w środowisku produkcyjnym.
* Domyślny termin wygaśnięcia certyfikatu z podpisem własnym to jeden rok.

Przykładowy kod z [zestawu Azure IoT SDK dla środowiska Java](https://github.com/Azure/azure-iot-sdk-java.git) zostanie użyty do utworzenia certyfikatu, który będzie używany z indywidualnym wpisem rejestracji dla urządzenia symulowanego.


1. Przy użyciu wiersza polecenia z poprzednich kroków przejdź do folderu `target`, a następnie wykonaj plik jar utworzony w poprzednim kroku.

    ```cmd/sh
    cd target
    java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
    ```

2. Wprowadź **N** w odpowiedzi na pytanie _Czy chcesz wprowadzić nazwę pospolitą_. Skopiuj do schowka dane wyjściowe z sekcji `Client Cert` rozpoczynające się od wiersza *-----BEGIN CERTIFICATE-----* i kończące się wierszem *-----END CERTIFICATE-----* .

   ![Generator indywidualnego certyfikatu](./media/java-quick-create-simulated-device-x509/individual.png)

3. Utwórz plik o nazwie **_X509individual.pem_** na maszynie z systemem Windows, otwórz go w wybranym edytorze, a następnie skopiuj zawartość schowka do tego pliku. Zapisz plik i zamknij edytor.

4. W wierszu polecenia wprowadź **N** w odpowiedzi na pytanie _Czy chcesz wprowadzić kod weryfikacyjny_ i nie zamykaj danych wyjściowych programu, ponieważ będą one potrzebne w dalszej części przewodnika Szybki start. Później skopiuj wartości `Client Cert` i `Client Cert Private Key` do użycia w następnej sekcji.

5. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com), kliknij przycisk **Wszystkie zasoby** w menu po lewej stronie i otwórz swoje wystąpienie usługi Device Provisioning Service.

6. W bloku podsumowania usługi Device Provisioning Service wybierz pozycję **Zarządzaj rejestracjami**. Wybierz kartę **Rejestracje indywidualne** i kliknij przycisk **Dodaj rejestrację indywidualną** u góry strony. 

7. W panelu **Dodawanie rejestracji** wprowadź następujące informacje:
   - Wybierz opcję **X.509** jako *Mechanizm* poświadczania tożsamości.
   - W obszarze *Plik PEM lub CER certyfikatu podstawowego* kliknij opcję *Wybierz plik*, aby wybrać plik certyfikatu **X509individual.pem** utworzony we wcześniejszych krokach.  
   - Opcjonalnie można podać następujące informacje:
     - Wybierz centrum IoT połączone z Twoją usługą aprowizacji.
     - Wprowadź unikatowy identyfikator urządzenia. Nadając nazwę urządzeniu, unikaj korzystania z danych poufnych. 
     - Zaktualizuj pole **Początkowy stan bliźniaczej reprezentacji urządzenia** za pomocą wybranej konfiguracji początkowej dla urządzenia.
     - Gdy skończysz, kliknij przycisk **Zapisz**. 

     [![Dodawanie indywidualnej rejestracji dla zaświadczenia X.509 w portalu](./media/quick-create-simulated-device-x509-csharp/device-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)

     Po pomyślnej rejestracji urządzenie X.509 jest wyświetlane jako **microsoftriotcore** w kolumnie *Identyfikator rejestracji* na karcie *Indywidualne rejestracje*. 



## <a name="simulate-the-device"></a>Symulowanie urządzenia

1. W bloku podsumowania usługi Device Provisioning wybierz pozycję **Przegląd** i zanotuj wartości _Identyfikator zakresu_ oraz _Globalny punkt końcowy usługi aprowizacji_.

    ![Informacje o usłudze](./media/java-quick-create-simulated-device-x509/extract-dps-endpoints.png)

2. Otwórz wiersz polecenia. Przejdź do folderu projektu przykładowego repozytorium zestawów SDK języka Java.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-X509-sample
    ```

3. Wprowadź usługę aprowizacji oraz informacje o tożsamości X.509 w swoim kodzie. Jest to używane podczas automatycznej aprowizacji w celu poświadczenia symulowanego urządzenia przed rejestracją urządzenia:

   - Edytuj plik `/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningX509Sample.java`, aby uwzględnić w nim wspomniane wcześniej wartości _Identyfikator zakresu_ i _Globalny punkt końcowy usługi aprowizacji_. Uwzględnij też wspomniane w poprzedniej sekcji wartości _Certyfikatu klienta_ i _Klucza prywatnego certyfikatu klienta_.

      ```java
      private static final String idScope = "[Your ID scope here]";
      private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
      private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
      private static final String leafPublicPem = "<Your Public PEM Certificate here>";
      private static final String leafPrivateKey = "<Your Private PEM Key here>";
      ```

   - Użyj następującego formatu podczas kopiowania/wklejania certyfikatu i klucza prywatnego:
        
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

4. Skompiluj przykład. Przejdź do folderu `target` i wykonaj utworzony plik jar.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-x509-sample-{version}-with-deps.jar
    ```

5. W witrynie Azure Portal przejdź do centrum IoT Hub połączonego z usługą aprowizacji, a następnie otwórz blok **Device Explorer**. Po pomyślnej aprowizacji symulowanego urządzenia X.509 w centrum identyfikator urządzenia jest wyświetlany w bloku **Eksplorator urządzeń** z pozycją *STATUS* (Stan) ustawioną na wartość **enabled** (włączone).  Jeśli blok został otwarty przed uruchomieniem przykładowej aplikacji urządzenia, konieczne może być kliknięcie przycisku **Odśwież** znajdującego się u góry. 

    ![Urządzenie jest rejestrowane w centrum IoT](./media/java-quick-create-simulated-device-x509/hubregistration.png) 

> [!NOTE]
> Jeśli zmienisz wartość w polu *Początkowy stan bliźniaczej reprezentacji urządzenia* z domyślnej na inną we wpisie rejestracji dla Twojego urządzenia, może to spowodować pobranie z centrum żądanego stanu reprezentacji bliźniaczej i odpowiednie do niego działanie. Aby uzyskać więcej informacji, zobacz [Opis bliźniaczej reprezentacji urządzenia w usłudze IoT Hub oraz sposoby jej używania](../iot-hub/iot-hub-devguide-device-twins.md).
>


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli planujesz dalszą pracę z przykładem klienta urządzenia i eksplorowanie go, nie czyść zasobów utworzonych w ramach tego przewodnika Szybki start. Jeśli nie planujesz kontynuować pracy, wykonaj poniższe kroki, aby usunąć wszystkie zasoby utworzone w ramach tego przewodnika Szybki start.

1. Zamknij okno danych wyjściowych przykładu klienta urządzenia na swojej maszynie.
2. W witrynie Azure Portal w menu po lewej stronie kliknij pozycję **Wszystkie zasoby**, a następnie wybierz swoją usługę Device Provisioning Service. Otwórz blok **Zarządzanie rejestracjami** dla usługi, a następnie kliknij kartę **rejestracje indywidualne** . Wybierz *Identyfikator rejestracji* urządzenia zarejestrowanego w tym przewodniku Szybki Start, a następnie kliknij przycisk **Usuń** u góry. 
3. W witrynie Azure Portal w menu po lewej stronie kliknij pozycję **Wszystkie zasoby**, a następnie wybierz swoje centrum IoT. Otwórz blok **Urządzenia IoT** Twojego centrum, wybierz *IDENTYFIKATOR URZĄDZENIA* zarejestrowanego w ramach tego przewodnika Szybki start, a następnie kliknij przycisk **Usuń** u góry.


## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono symulowane urządzenie X.509 na komputerze z systemem Windows. Skonfigurowano jego rejestrację w usłudze Azure IoT Hub Device Provisioning Service, a następnie automatycznie aprowizowano urządzenie w centrum IoT. Aby dowiedzieć się, jak zarejestrować urządzenie X.509 programowo, przejdź do przewodnika Szybki start dotyczącego programowej rejestracji urządzeń X.509. 

> [!div class="nextstepaction"]
> [Przewodnik Szybki start platformy Azure — Rejestrowanie urządzenia X.509 w usłudze Azure IoT Hub Device Provisioning](quick-enroll-device-x509-java.md)
