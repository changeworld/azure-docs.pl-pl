---
title: Szybki start — używanie klucza symetrycznego do inicjowania symulowanego urządzenia do usługi Azure IoT Hub przy użyciu języka Java
description: W tym przewodniku Szybki start użyjesz zestawu SDK urządzenia Java do utworzenia symulowanego urządzenia, które używa klucza symetrycznego z usługą inicjowania obsługi administracyjnej urządzeń usługi Azure IoT Hub (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 01/30/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.custom: mvc
ms.openlocfilehash: aaa1a4423363255536db7d53a1f8f8fa9ba686ff
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76941402"
---
# <a name="quickstart-provision-a-simulated-device-with-symmetric-keys"></a>Szybki start: aprowizowanie urządzenia symulowanego przy użyciu kluczy symetrycznych

Z tego przewodnika Szybki start dowiesz się, jak utworzyć i uruchomić symulator urządzenia na maszynie deweloperskiej z systemem Windows. To symulowane urządzenie skonfiguruje się tak, aby używało klucza symetrycznego do uwierzytelniania za pomocą wystąpienia usługi obsługi administracyjnej (DPS) i zostało przypisane do centrum IoT Hub. Przykładowy kod z [zestawów SDK IoT platformy Microsoft Azure dla języka Java](https://github.com/Azure/azure-iot-sdk-java) będzie używany do symulowania sekwencji rozruchowej dla urządzenia inicjującego inicjowanie obsługi administracyjnej. Urządzenie zostanie rozpoznane na podstawie indywidualnej rejestracji z wystąpieniem usługi DPS i przypisane do centrum IoT hub.

Mimo że w tym artykule pokazano inicjowania obsługi administracyjnej z rejestracją indywidualną, można użyć grup rejestracji. Istnieją pewne różnice podczas korzystania z grup rejestracji. Na przykład należy użyć pochodnego klucza urządzenia z unikatowym identyfikatorem rejestracji dla urządzenia. Mimo że grupy rejestracji klucza symetrycznego nie są ograniczone do starszych urządzeń, artykuł [Aprowizowanie starszych urządzeń za pomocą zaświadczenia klucza symetrycznego](how-to-legacy-device-symm-key.md) zawiera przykład grupy rejestracji. Aby uzyskać więcej informacji, zobacz [Rejestrowanie grupy dla zaświadczania klucza symetrycznego](concepts-symmetric-key-attestation.md#group-enrollments).

Jeśli nie znasz procesu automatycznego aprowizowania, zapoznaj się z tematem [Auto-provisioning concepts (Pojęcia związane z automatycznym aprowizowaniem)](concepts-auto-provisioning.md). 

Pamiętaj również, aby przed rozpoczęciem pracy z tym przewodnikiem Szybki start wykonać kroki przedstawione w części [Konfigurowanie usługi IoT Hub Device Provisioning za pomocą witryny Azure Portal](./quick-setup-auto-provision.md). Ten przewodnik Szybki start wymaga utworzonego już wystąpienia usługi Device Provisioning Service.

Ten artykuł został opracowany z myślą o stacjach roboczych z systemem Windows. Jednak opisane procedury można wykonać także w systemie Linux. Aby uzyskać przykład dla systemu Linux, zobacz [Aprowizowanie pod kątem wielu dzierżaw](how-to-provision-multitenant.md).


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Wymagania wstępne

* Upewnij się, że na komputerze jest zainstalowany [zestaw Java SE Development Kit 8](https://aka.ms/azure-jdks) lub nowszy.

* Pobierz i zainstaluj pakiet [Maven](https://maven.apache.org/install.html).

* Zainstalowana najnowsza wersja usługi[Git](https://git-scm.com/download/).

<a id="setupdevbox"></a>

## <a name="prepare-the-java-sdk-environment"></a>Przygotowanie środowiska java SDK 

1. Upewnij się, że na Twojej maszynie jest zainstalowane oprogramowanie Git i że jest ono dodane do zmiennych środowiskowych dostępnych z okna poleceń. Zobacz stronę z [narzędziami klienckimi Git organizacji Software Freedom Conservancy](https://git-scm.com/download/), aby uzyskać najnowszą wersję narzędzi `git` do zainstalowania, które obejmują powłokę **Git Bash**, czyli aplikację wiersza polecenia, która może służyć do interakcji z lokalnym repozytorium Git. 

2. Otwórz wiersz polecenia. Sklonuj repozytorium GitHub dla przykładu kodu symulacji urządzenia:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```
3. Przejdź do `azure-iot-sdk-java` katalogu głównego i skompiluj projekt, aby pobrać wszystkie potrzebne pakiety.
   
   ```cmd/sh
   cd azure-iot-sdk-java
   mvn install -DskipTests=true
   ```

## <a name="create-a-device-enrollment"></a>Tworzenie rejestracji urządzenia

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com), wybierz przycisk **Wszystkie zasoby** w menu po lewej stronie i otwórz wystąpienie usługi inicjowania obsługi urządzeń (DPS).

2. Wybierz kartę **Zarządzanie rejestracjami,** a następnie wybierz przycisk **Dodaj rejestrację indywidualną** u góry. 

3. W panelu **Dodawanie rejestracji** wprowadź następujące informacje i naciśnij przycisk **Zapisz.**

   - **Mechanizm:** wybierz **Klucz symetryczny** jako *Mechanizm* poświadczania tożsamości.

   - **Automatyczne generowanie kluczy:** Zaznacz to pole wyboru.

   - **Identyfikator rejestracji**: wprowadź identyfikator rejestracji, aby zidentyfikować rejestrację. Użyj tylko małych znaków alfanumerycznych i kresek (-). Na przykład **symm-key-java-device-007**.

   - **Identyfikator urządzenia usługi IoT Hub:** wprowadź identyfikator urządzenia. Na przykład **java-device-007**.

     ![Dodawanie indywidualnej rejestracji dla zaświadczenia klucza symetrycznego w portalu](./media/quick-create-simulated-device-symm-key-java/create-individual-enrollment-java.png)

4. Po zapisaniu rejestracji **klucz podstawowy** i **klucz pomocniczy** zostaną wygenerowane i dodane do wpisu rejestracji. Rejestracja urządzenia klucza symetrycznego jest wyświetlana jako **symm-key-java-device-007** w kolumnie *Identyfikator rejestracji* na karcie *Rejestracje indywidualne.* 

    Otwórz rejestrację i skopiuj wartość wygenerowanego **klucza podstawowego**. Użyjesz tej wartości klucza i **identyfikatora rejestracji** później po zaktualizowaniu kodu Java dla urządzenia.



<a id="firstbootsequence"></a>

## <a name="simulate-device-boot-sequence"></a>Symulowanie sekwencji rozruchu urządzenia

W tej sekcji zaktualizujesz przykładowy kod urządzenia, aby wysłać sekwencję rozruchu urządzenia do wystąpienia DPS. Ta sekwencja rozruchowa spowoduje, że urządzenie zostanie rozpoznane, uwierzytelnione i przypisane do centrum IoT połączonego z wystąpieniem DPS.

1. Z menu Usługa inicjowania obsługi administracyjnej urządzeń wybierz polecenie **Omówienie** i zanotuj globalny punkt końcowy _zakresu identyfikatora_ i _usługi inicjowania obsługi administracyjnej_.

    ![Informacje o usłudze](./media/java-quick-create-simulated-device-x509/extract-dps-endpoints.png)

2. Otwórz przykładowy kod urządzenia Java do edycji. Pełna ścieżka do przykładowego kodu urządzenia to:

    `azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-symmetrickey-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningSymmetricKeySampleSample.java`

   - Dodaj _zakres identyfikatora_ i _globalny punkt końcowy usługi inicjowania obsługi administracyjnej_ wystąpienia DPS. Dołącz również podstawowy klucz symetryczny i identyfikator rejestracji wybrany dla indywidualnej rejestracji. Zapisz zmiany. 

      ```java
        private static final String SCOPE_ID = "[Your scope ID here]";
        private static final String GLOBAL_ENDPOINT = "[Your Provisioning Service Global Endpoint here]";
        private static final String SYMMETRIC_KEY = "[Enter your Symmetric Key here]";
        private static final String REGISTRATION_ID = "[Enter your Registration ID here]";
      ```

3. Otwórz wiersz polecenia do budowania. Przejdź do przykładowego folderu projektu inicjowania obsługi administracyjnej repozytorium java SDK.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-symmetrickey-sample
    ```

4. Skompiluj przykład, a następnie przejdź do folderu, `target` aby wykonać utworzony plik jar.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-symmetrickey-sample-{version}-with-deps.jar
    ```

5. Oczekiwane dane wyjściowe powinny wyglądać podobnie do następującego:

    ```cmd/sh
      Starting...
      Beginning setup.
      Waiting for Provisioning Service to register
      IotHUb Uri : <Your DPS Service Name>.azure-devices.net
      Device ID : java-device-007
      Sending message from device to IoT Hub...
      Press any key to exit...
      Message received! Response status: OK_EMPTY
    ```

6. W witrynie Azure Portal przejdź do centrum IoT Hub połączonego z usługą aprowizacji, a następnie otwórz blok **Device Explorer**. Po pomyślnym zainicjowaniu obsługi administracyjnej symulowanego symetrycznego kluczowego urządzenia do koncentratora jego identyfikator urządzenia pojawia się na bloku **Eksploratora urządzeń** z **włączonym stanem**. *STATUS*  Może być konieczne naciśnięcie przycisku **Odśwież** u góry, jeśli blok został już otwarty przed uruchomieniem przykładowej aplikacji urządzenia. 

    ![Urządzenie jest rejestrowane w centrum IoT](./media/quick-create-simulated-device-symm-key-java/hubregistration-java.png) 

> [!NOTE]
> Jeśli zmienisz wartość w polu *Początkowy stan bliźniaczej reprezentacji urządzenia* z domyślnej na inną we wpisie rejestracji dla Twojego urządzenia, może to spowodować pobranie z centrum żądanego stanu reprezentacji bliźniaczej i odpowiednie do niego działanie. Aby uzyskać więcej informacji, zobacz [Opis bliźniaczej reprezentacji urządzenia w usłudze IoT Hub oraz sposoby jej używania](../iot-hub/iot-hub-devguide-device-twins.md).
>


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli planujesz kontynuować pracę i eksplorowanie przykładu klienta urządzenia, nie czyścić zasobów utworzonych w tym przewodniku Szybki start. Jeśli nie zamierzasz kontynuować, wykonaj następujące kroki, aby usunąć wszystkie zasoby utworzone przez ten przewodnik Szybki start.

1. Zamknij okno danych wyjściowych przykładu klienta urządzenia na swojej maszynie.
1. Z menu po lewej stronie w witrynie Azure portal wybierz **pozycję Wszystkie zasoby,** a następnie wybierz usługę inicjowania obsługi urządzeń. Otwórz **okno Zarządzaj rejestracjami** dla usługi, a następnie wybierz kartę **Rejestracje indywidualne.** Zaznacz pole wyboru obok *identyfikatora REJESTRACJI* urządzenia zarejestrowanego w tym przewodniku Szybki start, a następnie naciśnij przycisk **Usuń** u góry okienka. 
1. Z menu po lewej stronie w witrynie Azure portal wybierz **pozycję Wszystkie zasoby,** a następnie wybierz centrum IoT Hub. Otwórz **urządzenia IoT** dla centrum, zaznacz pole wyboru obok *identyfikatora URZĄDZENIA* urządzenia zarejestrowanego w tym przewodniku Szybki start, a następnie naciśnij przycisk **Usuń** u góry okienka.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono symulowane urządzenie na komputerze z systemem Windows i zainicjowano go do centrum IoT przy użyciu klucza Symetrycznego za pomocą usługi inicjowania obsługi administracyjnej usługi Azure IoT Hub w portalu. Aby dowiedzieć się, jak zarejestrować urządzenie programowo, przejdź do szybkiego startu w celu uzyskania programowej rejestracji urządzeń X.509. 

> [!div class="nextstepaction"]
> [Szybki start platformy Azure — rejestrowanie urządzeń X.509 w usłudze inicjowania obsługi administracyjnej usługi Azure IoT Hub](quick-enroll-device-x509-java.md)
