---
title: Symulacja urządzenia w rozwiązaniu do monitorowania zdalnego — Azure | Dokumentacja firmy Microsoft
description: Ten samouczek pokazuje, jak za pomocą usług symulator urządzenia zdalnego akcelerator rozwiązań do monitorowania.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/15/2018
ms.topic: conceptual
ms.openlocfilehash: 9c0c1ba9dd343baa453f10ad82c0cc8b8e69da7b
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/07/2018
ms.locfileid: "39596158"
---
# <a name="create-a-new-simulated-device"></a>Utwórz nowe urządzenie symulowane

W tym samouczku dowiesz się, jak dostosować mikrousług symulator urządzenia w akceleratora rozwiązania monitorowania zdalnego. Pokazywanie możliwości symulator urządzeń, dwa scenariusze w tym samouczku jest używana w aplikacji IoT firmy Contoso.

Poniższy klip wideo zawiera omówienie opcji dostosowywania mikrousług symulator urządzenia:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/How-to-customize-the-Remote-Monitoring-Preconfigured-Solution-for-Azure-IoT/Player]

W przypadku pierwszego scenariusza firma Contoso chce przetestować nowe urządzenie inteligentne ikony żarówki. Aby wykonać testy, należy utworzyć nowe urządzenie symulowane o następującej charakterystyce:

*Właściwości*

| Name (Nazwa)                     | Wartości                      |
| ------------------------ | --------------------------- |
| Kolor                    | White, Red, Blue            |
| Jasność               | 0 do 100                    |
| Szacowany pozostały okres | Odliczanie z 10 000 godzin |

*Dane telemetryczne*

W poniższej tabeli przedstawiono dane ikonę żarówki raportów w chmurze jako strumień danych:

| Name (Nazwa)   | Wartości      |
| ------ | ----------- |
| Stan | "włączone" "wyłączone" |
| Temperatura | Stopnie F |
| online | wartość true, false |

> [!NOTE]
> **Online** wartość jest obowiązkowa w przypadku wszystkich symulowanych typów.

*Metody*

W poniższej tabeli przedstawiono akcje, które obsługuje nowe urządzenie:

| Name (Nazwa)        |
| ----------- |
| Przełącz się   |
| Wyłącz  |

*Stan początkowy*

W poniższej tabeli przedstawiono początkowy stan urządzenia:

| Name (Nazwa)                     | Wartości |
| ------------------------ | -------|
| Kolor początkowy            | Biały  |
| Jasność początkowej       | 75     |
| Początkowe pozostały okres   | 10 000 |
| Stan początkowy telemetrii | "włączone"   |
| Temperatura początkowej danych telemetrycznych | 200   |

W drugim scenariuszu, możesz dodać nowy typ danych telemetrycznych do firmy Contoso istniejącej **Chłodnica** urządzenia.

Ten samouczek pokazuje, jak za pomocą usług symulator urządzenia akceleratora rozwiązania monitorowania zdalnego:

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

>[!div class="checklist"]
> * Tworzenie nowego typu urządzenia
> * Symulowanie urządzenia niestandardowe zachowanie
> * Dodawanie nowego typu urządzenia do pulpitu nawigacyjnego
> * Wyślij niestandardowych danych telemetrycznych z istniejącego typu urządzenia

W poniższym wideo pokazano krok po kroku łączyć urządzenia symulowanego i rzeczywiste rozwiązania do zdalnego monitorowania:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Part-38-Customizing-Azure-IoT-Suite-solution-and-connect-a-real-device/Player]

## <a name="prerequisites"></a>Wymagania wstępne

Aby skorzystać z tego samouczka, potrzebne są:

* Wdrożone wystąpienie rozwiązania do zdalnego monitorowania w ramach subskrypcji platformy Azure. Jeśli nie zostały wdrożone rozwiązanie monitorowania zdalnego, należy wykonać [wdrażanie akceleratora rozwiązania monitorowania zdalnego](../iot-accelerators/quickstart-remote-monitoring-deploy.md) samouczka.

* Program Visual Studio 2017. Jeśli nie masz zainstalowanego programu Visual Studio 2017, możesz pobrać bezpłatną [programu Visual Studio Community](https://www.visualstudio.com/free-developer-offers/) edition.

* [Eksplorator chmury dla programu Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVS15Preview) rozszerzenia programu Visual Studio.

* Konto na [usługi Docker Hub](https://hub.docker.com/). Możesz zarejestrować bezpłatnie rozpocząć pracę.

* [Git](https://git-scm.com/downloads) zainstalowane na komputerze stacjonarnym.

## <a name="prepare-your-development-environment"></a>Przygotowywanie środowiska projektowego

Wykonaj następujące zadania w celu przygotowania środowiska deweloperskiego do dodawania nowych symulowanych urządzeń do rozwiązania do zdalnego monitorowania:

### <a name="configure-ssh-access-to-the-solution-virtual-machine-in-azure"></a>Konfigurowanie dostępu SSH z maszyną wirtualną rozwiązanie na platformie Azure

Podczas tworzenia rozwiązania do zdalnego monitorowania w [www.azureiotsolutions.com](https://www.azureiotsolutions.com), wybraną nazwę rozwiązania. Nazwa rozwiązania staje się nazwa grupy zasobów platformy Azure, który zawiera różne wdrożone zasoby, których używa rozwiązania. Następujące polecenia, użyj grupy zasobów o nazwie **Contoso-01-**, należy zastąpić **Contoso-01** nazwą grupy zasobów.

Następujące polecenia, użyj `az` polecenia [interfejsu wiersza polecenia platformy Azure w wersji 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest). Możesz zainstalować interfejs wiersza polecenia platformy Azure w wersji 2.0 na komputerze deweloperskim lub użyć [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) w [witryny Azure portal](http://portal.azure.com). Interfejs wiersza polecenia platformy Azure w wersji 2.0 jest wstępnie instalowane w usłudze Cloud Shell.

1. Aby zweryfikować nazwy grupy zasobów, która zawiera zasoby zdalne monitorowanie, uruchom następujące polecenie:

    ```sh
    az group list | grep "name"
    ```

    To polecenie wyświetla listę wszystkich grup zasobów w ramach subskrypcji. Lista powinna zawierać grupę zasobów o takiej samej nazwie jako rozwiązania do zdalnego monitorowania.

1. Aby zasób grupy domyślnej grupy używane przy kolejnych poleceniach, uruchom następujące polecenie, używając Nazwa grupy zasobów zamiast **Contoso-01-**:

    ```sh
    az configure --defaults group=Contoso-01
    ```

1. Aby wyświetlić listę zasobów w grupie zasobów, uruchom następujące polecenie:

    ```sh
    az resource list -o table
    ```

    Zanotuj nazwy maszyny wirtualnej i sieciowej grupy zabezpieczeń. Użyjesz tych wartości w kolejnych krokach.

1. Aby włączyć dostęp SSH z maszyną wirtualną, uruchom następujące polecenie, używając nazwy sieciowej grupy zabezpieczeń z poprzedniego kroku:

    ```sh
    az network nsg rule create --name SSH --nsg-name YOUR-NETWORK-SECURITY-GROUP --priority 101 --destination-port-ranges 22 --access Allow --protocol TCP
    ```

    Aby wyświetlić listę reguł ruchu przychodzącego dla sieci, uruchom następujące polecenie:

    ```sh
    az network nsg rule list --nsg-name YOUR-NETWORK-SECURITY-GROUP -o table
    ```

    Dostęp SSH należy włączyć tylko podczas projektowania i testowania. Po włączeniu protokołu SSH, [należy wyłączyć je ponownie tak szybko jak to możliwe](../security/azure-security-network-security-best-practices.md#disable-rdpssh-access-to-azure-virtual-machines)

1. Aby zmienić hasło, które znasz hasło maszyny wirtualnej, uruchom następujące polecenie. Użyj nazwy maszyny wirtualnej, która zanotowanymi wcześniej i hasła wybranych przez użytkownika:

    ```sh
    az vm user update --name YOUR-VM-NAME --username azureuser --password YOUR-PASSWORD
    ```
1. Aby znaleźć adres IP swojej maszyny wirtualnej, użyj następującego polecenia i zanotuj publiczny adres IP:

    ```sh
    az vm list-ip-addresses --name YOUR-VM-NAME
    ```

1. Można teraz nawiązać połączenie z maszyną wirtualną za pomocą protokołu SSH. `ssh` Polecenia jest wstępnie instalowane w usłudze Cloud Shell. Użyj publicznego adresu IP z poprzedniego kroku i po wyświetleniu monitu hasło skonfigurowane dla maszyny wirtualnej:

    ```sh
    ssh azureuser@public-ip-address
    ```

    Teraz masz dostęp do powłoki maszyny wirtualnej, która uruchamia kontenery platformy Docker w rozwiązaniu monitorowania zdalnego. Aby wyświetlić uruchomione kontenery, użyj następującego polecenia:

    ```sh
    docker ps
    ```

### <a name="find-the-service-connection-strings"></a>Znajdź parametry połączenia z usługą

W tym samouczku możesz pracować z rozwiązania Visual Studio, który nawiązuje połączenie z usługi Cosmos DB i Centrum IoT Hub rozwiązania. Poniższe kroki pokazują, w jednym ze sposobów, aby znaleźć wartości ciągów, które są potrzebne połączenie:

1. Aby znaleźć parametrów połączenia usługi Cosmos DB, uruchom następujące polecenie w sesji SSH podłączona do maszyny wirtualnej:

    ```sh
    sudo grep STORAGEADAPTER_DOCUMENTDB /app/env-vars
    ```

    Zanotuj parametry połączenia. Będzie potrzebna w dalszej części samouczka.

1. Aby znaleźć parametry połączenia Centrum IoT, uruchom następujące polecenie w sesji SSH podłączona do maszyny wirtualnej:

    ```sh
    sudo grep IOTHUB_CONNSTRING /app/env-vars
    ```

    Zanotuj parametry połączenia. Będzie potrzebna w dalszej części samouczka.

> [!NOTE]
> Możesz również znaleźć te parametry połączenia w witrynie Azure portal lub za pomocą `az` polecenia.

### <a name="stop-the-device-simulation-service-in-the-virtual-machine"></a>Zatrzymaj usługę symulacji urządzenia na maszynie wirtualnej

Po zmodyfikowaniu usługi symulacji urządzeń możesz uruchomić go lokalnie, aby przetestować zmiany. Przed uruchomieniem usługi symulacji urządzeń lokalnie, należy zatrzymać wystąpienie działający na maszynie wirtualnej w następujący sposób:

1. Aby znaleźć **identyfikator kontenera** z **urządzeń symulacji dotnet** usługi, uruchom następujące polecenie w sesji SSH podłączona do maszyny wirtualnej:

    ```sh
    docker ps
    ```

    Zanotuj identyfikator kontenera **urządzeń symulacji dotnet** usługi.

1. Aby zatrzymać **urządzeń symulacji dotnet** kontener, uruchom następujące polecenie:

    ```sh
    docker stop container-id-from-previous-step
    ```

### <a name="clone-the-github-repositories"></a>Klonowanie repozytoriów GitHub

W tym samouczku możesz pracować **symulacji urządzenia** i **adapter magazynu** projektów programu Visual Studio. Można sklonować repozytoriów kodu źródłowego z repozytorium GitHub. Wykonaj tę czynność na lokalnej maszynie do programowania, w którym masz zainstalowany program Visual Studio:

1. Otwórz wiersz polecenia i przejdź do folderu, w którym chcesz zapisać kopię **symulacji urządzenia** i **adapter magazynu** repozytoriów GitHub.

1. Klonowanie wersji .NET **symulacji urządzenia** repozytorium, uruchom następujące polecenie:

    ```cmd
    git clone https://github.com/Azure/device-simulation-dotnet.git
    ```

    Usługa symulacji urządzeń w rozwiązaniu monitorowania zdalnego umożliwia wprowadzenia zmian do typów wbudowanych symulowanego urządzenia i tworzenia nowych symulowanych typów urządzeń. Typy urządzeń niestandardowych umożliwia testowanie zachowania rozwiązania do zdalnego monitorowania, przed nawiązaniem połączenia z urządzeniami fizycznymi.

1. Klonowanie wersji .NET **adapter magazynu** repozytorium, uruchom następujące polecenie:

    ```cmd
    git clone https://github.com/Azure/pcs-storage-adapter-dotnet.git
    ```

    Usługa symulacji urządzeń korzysta z usługi adapter magazynu połączyć się z usługą Cosmos DB na platformie Azure. Rozwiązania do zdalnego monitorowania przechowuje dane konfiguracji symulowane urządzenie w bazie danych Cosmos DB.

### <a name="run-the-storage-adapter-service-locally"></a>Uruchom usługę adapter magazynu lokalnie

Usługa symulacji urządzeń korzysta z usługi adapter magazynu nawiązać połączenia z bazą danych Cosmos DB rozwiązania. Jeśli usługa symulacji urządzenia jest uruchamiany lokalnie, należy także uruchomić usługę karty magazyn lokalnie. Poniższe kroki pokazują, jak uruchomić usługi storage karty z programu Visual Studio:

1. W programie Visual Studio, otwórz **komputerów storage-adapter.sln** plik rozwiązania w lokalnym klonie z **adapter magazynu** repozytorium.

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **WebService** projektu, wybierz **właściwości**, a następnie wybierz **debugowania**.

1. W **zmienne środowiskowe** sekcji, edytowanie wartości **komputerów\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING** zmienną połączenie usługi Cosmos DB ciąg, który zanotowanymi wcześniej. Następnie zapisz zmiany.

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **WebService** projektu, wybierz **debugowania**, a następnie wybierz **Uruchom nowe wystąpienie**.

1. Usługa zacznie działać lokalnie i otwiera `http://localhost:9022/v1/status` w domyślnej przeglądarce. Upewnij się, że **stan** wartość "OK: aktywności i dobrze."

1. Pozostaw usługa adapter magazynu, działa lokalnie, ukończenie tego samouczka.

Masz teraz wszystko, co w miejscu i wszystko będzie gotowe do uruchomienia, dodanie nowego typu symulowanego urządzenia do rozwiązania do zdalnego monitorowania.

## <a name="create-a-simulated-device-type"></a>Tworzenie typu symulowanego urządzenia

Najprostszym sposobem utworzenia nowego typu urządzenia w usłudze symulacji urządzenia to na skopiowaniu i zmodyfikowaniu istniejącego typu. Poniższe kroki pokazują sposób kopiowania wbudowane **Chłodnica** urządzeniu, aby utworzyć nową **żarówki** urządzenia:

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **WebService** projektu, wybierz **właściwości**, a następnie wybierz **debugowania**.

1. W **zmienne środowiskowe** sekcji, edytowanie wartości **komputerów\_IOTHUB\_CONNSTRING** zmiennej jako parametry połączenia Centrum IoT Hub zanotowanymi wcześniej. Następnie zapisz zmiany.

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **symulacji urządzenia** rozwiązań i wybierz polecenie **Ustaw projekty startowe**. Wybierz **pojedynczy projekt startowy** i wybierz **WebService**. Następnie kliknij przycisk **OK**.

1. Dla każdego typu urządzenia jest plik modelu w formacie JSON i skojarzone skrypty w **danych/Services/devicemodels** folderu. W Eksploratorze rozwiązań, skopiuj **Chłodnica** pliki, aby utworzyć **żarówki** plików, jak pokazano w poniższej tabeli:

    | Element źródłowy                      | Element docelowy                   |
    | --------------------------- | ----------------------------- |
    | chiller-01.json             | lightbulb-01.json             |
    | scripts/chiller-01-state.js | scripts/lightbulb-01-state.js |
    | skrypty/ponowne uruchomienie komputera method.js    | skrypty/SwitchOn-method.js    |

### <a name="define-the-characteristics-of-the-new-device-type"></a>Zdefiniuj właściwości nowego typu urządzenia

**01.json żarówki** plik definiuje właściwości typu, takich jak dane telemetryczne generuje i obsługuje metody. Następujące kroki aktualizacji **01.json żarówki** plik, aby zdefiniować **żarówki** urządzenia:

1. W **01.json żarówki** pliku, zaktualizuj metadane urządzenia, jak pokazano w poniższym fragmencie kodu:

    ```json
    "SchemaVersion": "1.0.0",
    "Id": "lightbulb-01",
    "Version": "0.0.1",
    "Name": "Lightbulb",
    "Description": "Smart lightbulb device.",
    "Protocol": "MQTT",
    ```

1. W **01.json żarówki** plików, zaktualizuj definicję symulacji, jak pokazano w poniższym fragmencie kodu:

    ```json
    "Simulation": {
      "InitialState": {
        "online": true,
        "temperature": 200.0,
        "temperature_unit": "F",
        "status": "on"
      },
      "Interval": "00:00:20",
      "Scripts": [
        {
          "Type": "javascript",
          "Path": "lightbulb-01-state.js"
        }
      ]
    },
    ```

1. W **01.json żarówki** pliku, aktualizowanie właściwości typu urządzenia, jak pokazano w poniższym fragmencie kodu:

    ```json
    "Properties": {
      "Type": "Lightbulb",
      "Color": "White",
      "Brightness": 75,
      "EstimatedRemainingLife": 10000
    },
    ```

1. W **01.json żarówki** plików, aktualizowanie definicji danych telemetrycznych typu urządzenia, jak pokazano w poniższym fragmencie kodu:

    ```json
    "Telemetry": [
      {
        "Interval": "00:00:20",
        "MessageTemplate": "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\",\"status\":\"${status}\"}",
        "MessageSchema": {
          "Name": "lightbulb-status;v1",
          "Format": "JSON",
          "Fields": {
            "temperature": "double",
            "temperature_unit": "text",
            "status": "text"
          }
        }
      }
    ],
    ```

1. W **01.json żarówki** plików, aktualizowanie metod typu urządzenia, jak pokazano w poniższym fragmencie kodu:

    ```json
    "CloudToDeviceMethods": {
      "SwitchOn": {
        "Type": "javascript",
        "Path": "SwitchOn-method.js"
      },
      "SwitchOff": {
        "Type": "javascript",
        "Path": "SwitchOff-method.js"
      }
    }
    ```

1. Zapisz **01.json żarówki** pliku.

### <a name="simulate-custom-device-behavior"></a>Symulowanie urządzenia niestandardowe zachowanie

**Skryptów/żarówki-01-state.js** plik definiuje zachowanie symulacji **żarówki** typu. Następujące kroki aktualizacji **skryptów/żarówki-01-state.js** plik, aby zdefiniować zachowanie **żarówki** urządzenia:

1. Edytuj definicję w stanie **skryptów/żarówki-01-state.js** pliku, jak pokazano w poniższym fragmencie kodu:

    ```js
    // Default state
    var state = {
      online: true,
      temperature: 200.0,
      temperature_unit: "F",
      status: "on"
    };
    ```

1. Dodaj **Przerzuć** działanie po **różnią się** funkcji przy użyciu następujących definicji:

    ```js
    /**
    * Simple formula that sometimes flips the status of the lightbulb
    */
    function flip(value) {
      if (Math.random() < 0.2) {
        return (value == "on") ? "off" : "on"
      }
      return value;
    }
    ```

1. Edytuj **głównego** funkcję, aby zaimplementować to zachowanie, jak pokazano w poniższym fragmencie kodu:

    ```js
    function main(context, previousState, previousProperties) {

        // Restore the global device properties and the global state before
        // generating the new telemetry, so that the telemetry can apply changes
        // using the previous function state.
        restoreSimulation(previousState, previousProperties);

        state.temperature = vary(200, 5, 150, 250);

        // Make this flip every so often
        state.status = flip(state.status);

        updateState(state);

        return state;
    }
    ```

1. Zapisz **skryptów/żarówki-01-state.js** pliku.

**Skryptów/SwitchOn-method.js** pliku implementuje **przełącznika na** method in Class metoda **żarówki** urządzenia. Następujące kroki aktualizacji **skryptów/SwitchOn-method.js** pliku:

1. Edytuj definicję w stanie **skryptów/SwitchOn-method.js** pliku, jak pokazano w poniższym fragmencie kodu:

    ```js
    var state = {
       status: "on"
    };
    ```

1. Aby przełączyć się na ikonę żarówki, należy edytować **głównego** funkcji w następujący sposób:

    ```js
    function main(context, previousState) {
        log("Executing lightbulb Switch On method.");
        state.status = "on";
        updateState(state);
    }
    ```

1. Zapisz **skryptów/SwitchOn-method.js** pliku.

1. Utwórz kopię **skryptów/SwitchOn-method.js** pliku o nazwie **skryptów/SwitchOff-method.js**.

1. Aby wyłączyć ikonę żarówki, należy edytować **głównego** działać w **skryptów/SwitchOff-method.js** pliku w następujący sposób:

    ```js
    function main(context, previousState) {
        log("Executing lightbulb Switch Off method.");
        state.status = "off";
        updateState(state);
    }
    ```

1. Zapisz **skryptów/SwitchOff-method.js** pliku.

1. W Eksploratorze rozwiązań wybierz każdego z czterech nowe pliki z osobna. W **właściwości** okna dla każdego pliku, upewnij się, że **Kopiuj do katalogu wyjściowego** ustawiono **Kopiuj Jeśli nowszy**.

### <a name="configure-the-device-simulation-service"></a>Skonfiguruj usługę symulacji urządzenia

Aby ograniczyć liczbę symulowanych urządzeń łączących się z rozwiązaniem podczas testowania, należy skonfigurować usługę do uruchomienia jednym Chłodnica i urządzenia jednej ikony żarówki. Dane konfiguracji są przechowywane w wystąpieniu usługi Cosmos DB w grupie zasobów rozwiązania. Aby edytować dane konfiguracji, należy użyć **programu Cloud Explorer** widok w programie Visual Studio:

1. Aby otworzyć **programu Cloud Explorer** wyświetlić w programie Visual Studio, wybierz **widoku** a następnie **programu Cloud Explorer**.

1. Aby znaleźć w dokumentacji konfiguracji symulacji **wyszukiwanie zasobów** wprowadź **simualtions.1**.

1. Kliknij dwukrotnie **simulations.1** dokumentu, aby go otworzyć do edycji.

1. Wartości **danych**, zlokalizuj **DeviceModels** tablicy, która wygląda podobnie do następującego fragmentu kodu:

    ```json
    [{\"Id\":\"chiller-01\",\"Count\":1},{\"Id\":\"chiller-02\",\"Count\":1},{\"Id\":\"elevator-01\",\"Count\":1},{\"Id\":\"elevator-02\",\"Count\":1},{\"Id\":\"engine-01\",\"Count\":1},{\"Id\":\"engine-02\",\"Count\":1},{\"Id\":\"prototype-01\",\"Count\":1},{\"Id\":\"prototype-02\",\"Count\":1},{\"Id\":\"truck-01\",\"Count\":1},{\"Id\":\"truck-02\",\"Count\":1}]
    ```

1. Aby określić pojedynczy Chłodnica i jednego żarówki symulowanego urządzenia, należy zastąpić **DeviceModels** tablicy następującym kodem:

    ```json
    [{\"Id\":\"chiller-01\",\"Count\":1},{\"Id\":\"lightbulb-01\",\"Count\":1}]
    ```

    Zapisz zmiany **simulations.1** dokumentu.

> [!NOTE]
> Możesz również użyć Eksplorator danych Cosmos DB w witrynie Azure portal, do edycji **simulations.1** dokumentu.

### <a name="test-the-lightbulb-device-type-locally"></a>Typ urządzenia żarówki lokalnie testu

Teraz można przystąpić do testowania nowego typu symulowane żarówki, uruchamiając projekt symulacji urządzenia lokalnie.

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **WebService**, wybierz **debugowania** , a następnie wybierz **Uruchom nowe wystąpienie**.

1. Aby sprawdzić, czy dwa symulowane urządzenia są podłączone do usługi IoT Hub, otwórz Azure portal w przeglądarce.

1. Przejdź do Centrum IoT w grupie zasobów, który zawiera rozwiązania do zdalnego monitorowania.

1. W **monitorowanie** wybierz pozycję **metryki**. Następnie upewnij się, że liczba **połączone urządzenia** wynosi dwa:

    ![Liczba połączonych urządzeń](./media/iot-accelerators-remote-monitoring-test/connecteddevices.png)

1. W przeglądarce przejdź do **pulpit nawigacyjny** dla rozwiązania do zdalnego monitorowania. W panelu dane telemetryczne w **pulpit nawigacyjny**, wybierz opcję **temperatury**. Temperatura wszystkie symulowane urządzenia wyświetlane na wykresie:

    ![Danych telemetrycznych dotyczących temperatury](./media/iot-accelerators-remote-monitoring-test/telemetry.png)

Masz teraz symulacji urządzenia żarówki, które są uruchamiane lokalnie. Następnym krokiem jest, aby wdrożyć kod symulatora zaktualizowane do maszyny wirtualnej z systemem monitorowania zdalnego mikrousług na platformie Azure.

Przed kontynuowaniem można zatrzymać debugowanie symulacji urządzenia i projekty adapter magazynu w programie Visual Studio.

### <a name="deploy-the-updated-simulator-to-the-cloud"></a>Wdrożenie zaktualizowanego symulatora w chmurze

Mikrousługi w rozwiązaniu do zdalnego monitorowania są uruchamiane w kontenerach platformy docker. Kontenery są hostowane na maszynie wirtualnej rozwiązania na platformie Azure. W tej sekcji omówiono następujące zagadnienia:

* Utwórz nowy obraz platformy docker symulacji urządzenia.
* Przekaż obraz do repozytorium usługi docker hub.
* Zaimportować go do swojego rozwiązania maszyny wirtualnej.

W następujących krokach założono, że masz repozytorium o nazwie **żarówki** na Twoim koncie usługi Docker Hub.

1. W programie Visual Studio w **symulacji urządzenia** projektu, otwórz plik **solution\scripts\docker\build.cmd**.

1. Edytuj wiersz, który ustawia **DOCKER_IMAGE** zmienną środowiskową na nazwę repozytorium usługi Docker Hub:

    ```cmd
    SET DOCKER_IMAGE=your-docker-hub-acccount/lightbulb
    ```

    Zapisz zmianę.

1. W programie Visual Studio w **symulacji urządzenia** projektu, otwórz plik **solution\scripts\docker\publish.cmd**.

1. Edytuj wiersz, który ustawia **DOCKER_IMAGE** zmienną środowiskową na nazwę repozytorium usługi Docker Hub:

    ```cmd
    SET DOCKER_IMAGE=your-docker-hub-acccount/lightbulb
    ```

    Zapisz zmianę.

1. Otwórz wiersz polecenia jako administrator. Następnie przejdź do folderu **scripts\docker** w sklonowanym z **symulacji urządzenia** repozytorium GitHub.

1. Aby utworzyć obraz platformy docker, uruchom następujące polecenie:

    ```cmd
    build.cmd
    ```

1. Aby zalogować się do swojego konta usługi Docker Hub, uruchom następujące polecenie:

    ```cmd
    docker login
    ```

1. Aby przekazać nowy obraz do swojego konta usługi Docker Hub, uruchom następujące polecenie:

    ```cmd
    publish.cmd
    ```

1. Aby sprawdzić, czy przekazywania, przejdź do [ https://hub.docker.com/ ](https://hub.docker.com/). Zlokalizuj swoje **żarówki** repozytorium i wybierz polecenie **szczegóły**. Następnie wybierz **tagi**:

    ![Usługi docker hub](./media/iot-accelerators-remote-monitoring-test/dockerhub.png)

    Skrypty dodane **testowania** tag do obrazu.

1. Łączenie z maszyną wirtualną swoje rozwiązanie na platformie Azure za pomocą protokołu SSH. Następnie przejdź do **aplikacji** folder i Edytuj **docker-compose.yml** pliku:

    ```sh
    cd /app
    sudo nano docker-compose.yml
    ```

1. Edytuj wpis dla usługi symulacji urządzenia w celu używania obrazu platformy docker:

    ```yaml
    devicesimulation:
      image: {your docker ID}/lightbulb:testing
    ```

    Zapisz zmiany.

1. Aby uruchomić ponownie wszystkie usługi z nowymi ustawieniami, uruchom następujące polecenie:

    ```sh
    sudo ./start.sh
    ```

1. Aby sprawdzić plik dziennika z nowego kontenera symulacji urządzenia, uruchom następujące polecenie, aby znaleźć identyfikator kontenera:

    ```sh
    docker ps
    ```

    Następnie uruchom następujące polecenie, używając Identyfikatora kontenera:

    ```sh
    docker logs {container ID}
    ```

Ukończono kroki umożliwiające wdrożenie zaktualizowanej wersji usługi symulacji urządzeń do rozwiązania do zdalnego monitorowania.

W przeglądarce przejdź do **pulpit nawigacyjny** dla rozwiązania do zdalnego monitorowania. W panelu dane telemetryczne w **pulpit nawigacyjny**, wybierz opcję **temperatury**. Temperatura dwa symulowane urządzenia wyświetlane na wykresie:

![Danych telemetrycznych dotyczących temperatury](./media/iot-accelerators-remote-monitoring-test/telemetry.png)

Na **urządzeń** stronie może aprowizować wystąpień tego nowego typu:

![Wyświetl listę dostępnych symulacji](./media/iot-accelerators-remote-monitoring-test/devicesmodellist.png)

Możesz wyświetlić dane telemetryczne z symulowanych urządzeń:

![Wyświetlanie danych telemetrycznych z żarówka](./media/iot-accelerators-remote-monitoring-test/devicestelemetry.png)

Możesz wywołać **SwitchOn** i **SwitchOff** metody na urządzeniu:

![Wywoływanie metod żarówka](./media/iot-accelerators-remote-monitoring-test/devicesmethods.png)

## <a name="add-a-new-telemetry-type"></a>Dodaj nowy typ telemetrii

Ta sekcja opisuje sposób modyfikowania istniejącego typu symulowane urządzenie na potrzeby obsługi nowego typu danych telemetrycznych.

### <a name="locate-the-chiller-device-type-files"></a>Znajdź pliki Chłodnica typ urządzenia

Poniższe kroki pokazują, jak można znaleźć plików, które definiują wbudowane **Chłodnica** urządzenia:

1. Jeśli jeszcze tego nie zrobiono, użyj następującego polecenia, aby sklonować **urządzeń symulacji dotnet** repozytorium GitHub na komputer lokalny:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git
    ```

1. Dla każdego typu urządzenia jest plik modelu w formacie JSON i skojarzone skrypty w `data/devicemodels` folderu. Pliki, które definiują symulowane **Chłodnica** są typu urządzenia:

    * **data/devicemodels/chiller-01.json**
    * **data/devicemodels/scripts/chiller-01-state.js**

### <a name="specify-the-new-telemetry-type"></a>Określ nowy typ telemetrii

Poniższe kroki pokazują, jak dodać nową **temperatura wewnętrzna** typ **Chłodnica** typu urządzenia:

1. Otwórz **01.json Chłodnica** pliku.

1. Aktualizacja **SchemaVersion** wartości w następujący sposób:

    ```json
    "SchemaVersion": "1.1.0",
    ```

1. W **stan początkowy** Dodaj follwing dwie definicje:

    ```json
    "internal_temperature": 65.0,
    "internal_temperature_unit": "F",
    ```

1. W **Telemetrii** tablicy, dodaj następującą definicję:

    ```json
    {
      "Interval": "00:00:05",
      "MessageTemplate": "{\"internal_temperature\":${internal_temperature},\"internal_temperature_unit\":\"${internal_temperature_unit}\"}",
      "MessageSchema": {
        "Name": "chiller-internal-temperature;v1",
        "Format": "JSON",
        "Fields": {
          "temperature": "double",
          "temperature_unit": "text"
        }
      }
    },
    ```

1. Zapisz **01.json Chłodnica** pliku.

1. Otwórz **skryptów/Chłodnica-01-state.js** pliku.

1. Dodaj następujące pola do **stanu** zmiennej:

    ```js
    internal_temperature: 65.0,
    internal_temperature_unit: "F",
    ```

1. Dodaj następujący wiersz do **głównego** funkcji:

    ```js
    state.internal_temperature = vary(65, 2, 15, 125);
    ```

1. Zapisz **skryptów/Chłodnica-01-state.js** pliku.

### <a name="test-the-chiller-device-type"></a>Typ urządzenia Chłodnica testu

Aby przetestować zaktualizowane **Chłodnica** typu urządzenia, najpierw uruchom lokalną kopię **urządzeń symulacji dotnet** usługi, aby przetestować danemu typowi urządzenia zachowuje się zgodnie z oczekiwaniami. Gdy po przetestowaniu i debugować lokalnie typu zaktualizowanych urządzeniach, może ponownego kompilowania kontenera i ponowne wdrażanie **urządzeń symulacji dotnet** usługi na platformie Azure.

Po uruchomieniu **urządzeń symulacji dotnet** usługę lokalnie wysyła dane telemetryczne do rozwiązania do zdalnego monitorowania. Na **urządzeń** stronie może aprowizować wystąpień typu zaktualizowane.

Testowanie i debugowanie lokalnie zmiany, zobacz poprzednią sekcję [lokalnie typ urządzenia żarówki testu](#test-the-lightbulb-device-type-locally).

Aby wdrożyć usługi symulacji zaktualizowanych urządzeniach rozwiązania maszyny wirtualnej platformy Azure, zobacz poprzednią sekcję [wdrożenie zaktualizowanego symulatora w chmurze](#deploy-the-updated-simulator-to-the-cloud).

Na **urządzeń** stronie może aprowizować wystąpień typu zaktualizowane:

![Dodaj zaktualizowane Chłodnica](./media/iot-accelerators-remote-monitoring-test/devicesupdatedchiller.png)

Możesz wyświetlić nowy **temperatura wewnętrzna** dane telemetryczne z symulowanych urządzeń.

## <a name="next-steps"></a>Kolejne kroki

Ten samouczek pokazuje, jak do:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Tworzenie nowego typu urządzenia
> * Symulowanie urządzenia niestandardowe zachowanie
> * Dodawanie nowego typu urządzenia do pulpitu nawigacyjnego
> * Wyślij niestandardowych danych telemetrycznych z istniejącego typu urządzenia

Teraz masz pokazaliśmy, jak dostosować usługę symulacji urządzenia. Sugerowane następnym krokiem jest, aby dowiedzieć się, jak [Podłączanie urządzenia fizycznego do rozwiązania do zdalnego monitorowania](iot-accelerators-connecting-devices-node.md).

Aby uzyskać więcej informacji dla deweloperów o rozwiązaniu monitorowania zdalnego Zobacz:

* [Przewodnik informacyjny dla deweloperów](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Przewodnik po rozwiązywaniu problemów dla deweloperów](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->
