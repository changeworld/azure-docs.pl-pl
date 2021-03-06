---
title: Rejestrowanie urządzenia modułu TPM w usłudze inicjowania obsługi administracyjnej urządzeń platformy Azure przy użyciu języka Java
description: Szybki start — rejestrowanie urządzenia TPM w usłudze inicjowania obsługi administracyjnej usługi usługi Azure IoT Hub (DPS) przy użyciu zestawu SDK usługi Java. W tym przewodniku Szybki start używane są rejestracje indywidualne.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: java
ms.custom: mvc
ms.openlocfilehash: c199d5be4c103c80a6fcc126af70f48367909f64
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241696"
---
# <a name="quickstart-enroll-tpm-device-to-iot-hub-device-provisioning-service-using-java-service-sdk"></a>Szybki start: rejestrowanie urządzenia TPM w usłudze inicjowania obsługi administracyjnej urządzeń usługi IoT Hub przy użyciu sdk usługi Java

[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]

W tym przewodniku Szybki start programowo utwórz indywidualną rejestrację dla symulowanego urządzenia TPM w usłudze inicjowania obsługi administracyjnej urządzeń usługi Azure IoT Hub przy użyciu zestawu SDK usługi Java za pomocą przykładowej aplikacji Java.

## <a name="prerequisites"></a>Wymagania wstępne

- Zakończenie [konfigurowania usługi inicjowania obsługi administracyjnej urządzeń usługi Usługi obsługi urządzeń usługi IoT Hub za pomocą witryny Azure portal](./quick-setup-auto-provision.md).
- Zakończenie [odczytu kluczy kryptograficznych z urządzenia TPM](quick-create-simulated-device.md#simulatetpm).
- Konto platformy Azure z aktywną subskrypcją. [Utwórz jeden za darmo](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Zestaw java se development 8](https://aka.ms/azure-jdks). Ten szybki start instaluje [sdk usługi Java](https://azure.github.io/azure-iot-sdk-java/service/) poniżej. Działa zarówno na Windows i Linux. Ten przewodnik Szybki start korzysta z systemu Windows.
- [Maven 3](https://maven.apache.org/download.cgi).
- [Git](https://git-scm.com/download/).

<a id="setupdevbox"></a>

## <a name="prepare-the-development-environment"></a>Przygotowywanie środowiska deweloperskiego 

1. Upewnij się, że na maszynie zainstalowano środowisko [Java SE Development Kit 8](https://aka.ms/azure-jdks). 

2. Skonfiguruj zmienne środowiskowe instalacji języka Java. Zmienna `PATH` powinna zawierać pełną ścieżkę do katalogu *jdk1.8.x\bin*. Jeśli jest to pierwsza instalacja języka Java na komputerze, utwórz nową zmienną środowiskową o nazwie `JAVA_HOME` i ustaw ją na pełną ścieżkę do katalogu *jdk1.8.x*. Na komputerze z systemem Windows ten katalog znajduje się w folderze *C:\\Program Files\\Java\\*, a zmienne środowiskowe można tworzyć lub edytować po wyszukaniu frazy **Edytuj zmienne środowiskowe systemu** w **Panelu sterowania** komputera z systemem Windows. 

   Aby sprawdzić poprawność skonfigurowania języka Java na komputerze, możesz uruchomić następujące polecenie w oknie polecenia:

    ```cmd\sh
    java -version
    ```

3. Pobierz pakiet [Maven 3](https://maven.apache.org/download.cgi) i wyodrębnij go na swoim komputerze. 

4. Zmodyfikuj zmienną środowiskową `PATH`, aby wskazywała na folder *apache-maven-3.x.x\\bin* znajdujący się w folderze, do którego wyodrębniono pakiet Maven. Poprawność zainstalowania pakietu Maven można potwierdzić, uruchamiając to polecenie w oknie polecenia:

    ```cmd\sh
    mvn --version
    ```

5. Upewnij się, że na swojej maszynie masz zainstalowane oprogramowanie [git](https://git-scm.com/download/) i że jest ono dodane do zmiennej środowiskowej `PATH`. 


<a id="javasample"></a>

## <a name="download-and-modify-the-java-sample-code"></a>Pobieranie i modyfikowanie przykładowego kodu Java

W tej sekcji przedstawiono sposób dodawania szczegółów aprowizacji urządzenia TPM do przykładowego kodu. 

1. Otwórz wiersz polecenia. Sklonuj przykład kodu kodu rejestracji urządzenia repozytorium GitHub przy użyciu zestawu [SDK usługi Java:](https://azure.github.io/azure-iot-sdk-java/service/)
    
    ```cmd\sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

2. W pobranym kodzie źródłowym przejdź do folderu przykładu **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-sample_**. Otwórz plik **_/src/main/java/samples/com/microsoft/azure/sdk/iot/ServiceEnrollmentSample.java_** w dowolnym edytorze i dodaj następujące szczegółowe informacje:

   1. Dodaj ciąg `[Provisioning Connection String]` dla usługi aprowizacji z portalu, jako pokazano poniżej:
       1. Przejdź do usługi inicjowania obsługi administracyjnej w [witrynie Azure portal](https://portal.azure.com). 
       2. Otwórz **Zasady dostępu współużytkowanego** i wybierz zasadę, która ma uprawnienie *EnrollmentWrite*.
       3. Skopiuj **Parametry połączenia klucza podstawowego**. 

           ![Pobieranie parametrów połączenia aprowizacji z portalu](./media/quick-enroll-device-tpm-java/provisioning-string.png)  

       4. W przykładowym pliku kodu **_ServiceEnrollmentSample.java_** zastąp ciąg `[Provisioning Connection String]`**parametrami połączenia klucza podstawowego**.
    
           ```Java
           private static final String PROVISIONING_CONNECTION_STRING = "[Provisioning Connection String]";
           ```

   2. Dodaj szczegóły urządzenia TPM:
       1. Pobierz *identyfikator rejestracji* i *klucz poręczenia modułu TPM* na potrzeby symulacji urządzenia TPM, wykonując kroki prowadzące do sekcji [Symulowanie urządzenia TPM](quick-create-simulated-device.md#simulatetpm).
       2. Użyj **_identyfikatora rejestracji_** i **_klucza poręczenia_** z danych wyjściowych poprzedniego kroku, aby zastąpić ciągi `[RegistrationId]` i `[TPM Endorsement Key]` w przykładowym pliku kodu **_ServiceEnrollmentSample.java_**:
        
           ```Java
           private static final String REGISTRATION_ID = "[RegistrationId]";
           private static final String TPM_ENDORSEMENT_KEY = "[TPM Endorsement Key]";
           ```

   3. Opcjonalnie możesz skonfigurować usługę aprowizacji za pośrednictwem przykładowego kodu:
      - Aby dodać tę konfigurację do przykładu, wykonaj następujące kroki:
        1. Przejdź do centrum IoT połączonego z usługą aprowizacji w witrynie [Azure Portal](https://portal.azure.com). Otwórz kartę **Przegląd** centrum i skopiuj wartość pola **Nazwa hosta**. Przypisz tę **nazwę hosta** do parametru *IOTHUB_HOST_NAME*.
            ```Java
            private static final String IOTHUB_HOST_NAME = "[Host name].azure-devices.net";
            ```
        2. Przypisz przyjazną nazwę do parametru *DEVICE_ID* i zachowaj dla parametru *PROVISIONING_STATUS* wartość domyślną *ENABLED*. 
    
      - LUB, jeśli nie chcesz konfigurować usługi aprowizacji, oznacz jako komentarze bądź usuń następujące instrukcje w pliku _ServiceEnrollmentSample.java_:
          ```Java
          // The following parameters are optional. Remove it if you don't need.
          individualEnrollment.setDeviceId(DEVICE_ID);
          individualEnrollment.setIotHubHostName(IOTHUB_HOST_NAME);
          individualEnrollment.setProvisioningStatus(PROVISIONING_STATUS);
          ```

   4. Przejrzyj przykładowy kod. Tworzy on, aktualizuje i usuwa rejestrację indywidualną urządzenia TPM oraz wykonuje względem niej zapytanie. Aby sprawdzić poprawność rejestracji w portalu, tymczasowo oznacz jako komentarz następujące wiersze kodu na końcu pliku _ServiceEnrollmentSample.java_:
    
       ```Java
       // *********************************** Delete info of individualEnrollment ************************************
       System.out.println("\nDelete the individualEnrollment...");
       provisioningServiceClient.deleteIndividualEnrollment(REGISTRATION_ID);
       ```

   5. Zapisz plik _ServiceEnrollmentSample.java_.

<a id="runjavasample"></a>

## <a name="build-and-run-the-java-sample-code"></a>Kompilowanie i uruchamianie przykładowego kodu Java

1. Otwórz okno polecenia i przejdź do folderu **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-sample_**.

2. Skompiluj przykładowy kod za pomocą tego polecenia:

    ```cmd\sh
    mvn install -DskipTests
    ```

   To polecenie pobiera pakiet [`com.microsoft.azure.sdk.iot.provisioning.service`](https://www.mvnrepository.com/artifact/com.microsoft.azure.sdk.iot.provisioning/provisioning-service-client) Maven na komputer. Ten pakiet zawiera pliki binarne dla [zestawu Java Service SDK](https://azure.github.io/azure-iot-sdk-java/service/), który należy utworzyć przykładowy kod. 

3. Uruchom przykładowy kod przy użyciu tych poleceń w oknie polecenia:

    ```cmd\sh
    cd target
    java -jar ./service-enrollment-sample-{version}-with-deps.jar
    ```

4. Sprawdź poprawność rejestracji w oknie danych wyjściowych. 

5. Przejdź do usługi aprowizacji w witrynie Azure Portal. Wybierz **pozycję Zarządzaj rejestracjami**i wybierz kartę *Registration ID* **Rejestracje indywidualne.** 

    ![Potwierdzanie poprawności rejestracji urządzenia TPM w portalu](./media/quick-enroll-device-tpm-java/verify-tpm-enrollment.png)  

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Jeśli planujesz eksplorować przykład usługi Java, nie czyścić zasobów utworzonych w tym przewodniku Szybki start. Jeśli nie zamierzasz kontynuować, wykonaj następujące kroki, aby usunąć wszystkie zasoby utworzone przez ten przewodnik Szybki start.

1. Zamknij okno danych wyjściowych przykładowej usługi Java na swojej maszynie.
1. Zamknij okno symulatora modułu TPM, które mogło zostać utworzone do symulacji urządzenia TPM.
1. Przejdź do usługi inicjowania obsługi urządzeń w portalu Azure, wybierz pozycję **Zarządzaj rejestracjami**, a następnie wybierz kartę **Rejestracje indywidualne.** Zaznacz pole wyboru obok *identyfikatora rejestracji* dla wpisu rejestracji utworzonego przy użyciu tego przewodnika Szybki start, a następnie naciśnij przycisk **Usuń** u góry okienka.

## <a name="next-steps"></a>Następne kroki
W tym przewodniku Szybki start zarejestrowano symulowane urządzenie TPM do usługi inicjowania obsługi administracyjnej urządzeń. Aby uzyskać dokładne informacje na temat aprowizowania urządzeń, przejdź do samouczka poświęconego konfiguracji usługi Device Provisioning Service w witrynie Azure portal. 

> [!div class="nextstepaction"]
> [Samouczki dla usługi Azure IoT Hub Device Provisioning Service](./tutorial-set-up-cloud.md)
