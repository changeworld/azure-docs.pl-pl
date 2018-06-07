---
title: Symulacji urządzenia w rozwiązaniu monitorowania zdalnego - Azure | Dokumentacja firmy Microsoft
description: W tym samouczku przedstawiono sposób symulator urządzeń za pomocą zdalnego monitorowania akcelerator rozwiązań.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/15/2018
ms.topic: conceptual
ms.openlocfilehash: d8a528265acc3e0bee24da6c1b6130082815b9fd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34628263"
---
# <a name="create-a-new-simulated-device"></a>Utwórz nowe urządzenie symulowane

Ten samouczek przedstawia sposób dostosowywania mikrousługi symulator urządzeń w akcelerator rozwiązań monitorowania zdalnego. Aby pokazać możliwości symulator urządzeń dwa scenariusze w tym samouczku jest używany w aplikacji Contoso IoT.

Poniższe wideo przedstawia Omówienie opcji dostosowywania mikrousługi symulator urządzeń:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/How-to-customize-the-Remote-Monitoring-Preconfigured-Solution-for-Azure-IoT/Player]

W pierwszego scenariusza firma Contoso chce przetestować nowe urządzenie żarówka inteligentne. Aby wykonać testów, należy utworzyć nowe symulowane urządzenie o następującej charakterystyce:

*właściwości*

| Name (Nazwa)                     | Wartości                      |
| ------------------------ | --------------------------- |
| Kolor                    | White, Red, Blue            |
| Jasność               | 0 do 100                    |
| Szacowany pozostały okres | Odliczania w dół od 10 000 godzin |

*Telemetrii*

W poniższej tabeli przedstawiono dane żarówka raportów w chmurze jako strumień danych:

| Name (Nazwa)   | Wartości      |
| ------ | ----------- |
| Stan | "na" "wyłączone" |
| Temperatura | Stopnie F |
| online | wartość true, false |

> [!NOTE]
> **Online** wartość telemetrii jest obowiązkowa w przypadku wszystkich symulowane typów.

*Metody*

W poniższej tabeli przedstawiono akcje, które obsługuje nowe urządzenie:

| Name (Nazwa)        |
| ----------- |
| Przełącz   |
| Wyłącz  |

*Stan początkowy*

W poniższej tabeli przedstawiono początkowy stan urządzenia:

| Name (Nazwa)                     | Wartości |
| ------------------------ | -------|
| Kolor początkowy            | Biały  |
| Jasność początkowej       | 75     |
| Początkowa pozostały okres   | 10 000 |
| Stan początkowy telemetrii | "on"   |
| Temperatury początkowej telemetrii | 200   |

W drugi scenariusz, należy dodać nowy typ danych telemetrycznych do firmy Contoso jego istniejącą **Chłodnica** urządzenia.

W tym samouczku przedstawiono sposób symulator urządzeń za pomocą akcelerator rozwiązań monitorowania zdalnego:

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

>[!div class="checklist"]
> * Tworzenie nowego typu urządzenia
> * Symulowanie zachowanie urządzeń niestandardowych
> * Dodawanie nowego typu urządzenia do pulpitu nawigacyjnego
> * Wysyłać dane telemetryczne niestandardowych z istniejącym typem urządzenia

Poniższe wideo przedstawia przewodnik połączenie symulowanych i rzeczywistych urządzeń do monitorowania zdalnego rozwiązania:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Part-38-Customizing-Azure-IoT-Suite-solution-and-connect-a-real-device/Player]

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć tego samouczka, potrzebne są:

* Wdrożone wystąpienie rozwiązanie monitorowania zdalnego w ramach subskrypcji platformy Azure. Jeśli jeszcze tego nie wdrożono rozwiązanie monitorowania zdalnego jeszcze, należy wykonać [wdrażanie akcelerator rozwiązań monitorowania zdalnego](../iot-accelerators/iot-accelerators-remote-monitoring-deploy.md) samouczka.

* Program Visual Studio 2017. Jeśli nie masz programu Visual Studio 2017 r zainstalowany, możesz pobrać bezpłatną [Visual Studio Community](https://www.visualstudio.com/free-developer-offers/) edition.

* [Cloud Explorer dla programu Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVS15Preview) rozszerzenie programu Visual Studio.

* Konto na [Centrum Docker](https://hub.docker.com/). Możesz utworzyć konto bezpłatnej rozpocząć pracę.

* [Git](https://git-scm.com/downloads) zainstalowana na tym komputerze pulpitu.

## <a name="prepare-your-development-environment"></a>Przygotowywanie środowiska projektowego

Wykonaj następujące zadania w celu przygotowania środowiska deweloperskiego do dodawania nowych symulowane urządzenie do monitorowania zdalnego rozwiązania:

### <a name="configure-ssh-access-to-the-solution-virtual-machine-in-azure"></a>Konfigurowanie dostępu SSH z maszyną wirtualną rozwiązania na platformie Azure

Podczas tworzenia rozwiązania monitorowania zdalnego na [www.azureiotsolutions.com](https://www.azureiotsolutions.com), wybrana nazwa rozwiązania. Nazwa rozwiązania staje się nazwa grupy zasobów platformy Azure, która zawiera różne wdrożone zasoby używane przez rozwiązanie. W poniższych poleceniach zastosowano grupę zasobów o nazwie **Contoso-01**, należy zastąpić **Contoso-01** z nazwą grupy zasobów.

Następujące polecenia, użyj `az` polecenie [Azure CLI 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest). Zainstaluj na komputerze deweloperskim 2.0 interfejsu wiersza polecenia Azure, lub użyj [powłoki chmury](https://docs.microsoft.com/azure/cloud-shell/overview) w [portalu Azure](http://portal.azure.com). Azure CLI 2.0 jest wstępnie zainstalowane w powłoce chmury.

1. Aby sprawdzić nazwę grupy zasobów, która zawiera zasoby zdalne monitorowanie, uruchom następujące polecenie:

    ```sh
    az group list | grep "name"
    ```

    To polecenie wyświetla listę wszystkich grup zasobów w ramach subskrypcji. Lista powinna zawierać grupę zasobów o takiej samej nazwie jako rozwiązania monitorowania zdalnego.

1. Aby zasób grupy domyślnej grupy dla kolejnych poleceń, uruchom następujące polecenie za pomocą nazwę grupy zasobów, zamiast **Contoso-01**:

    ```sh
    az configure --defaults group=Contoso-01
    ```

1. Aby wyświetlić listę zasobów w grupie zasobów, uruchom następujące polecenie:

    ```sh
    az resource list -o table
    ```

    Zanotuj nazwy maszyny wirtualnej i grupy zabezpieczeń sieci. Te wartości można użyć w kolejnych krokach.

1. Aby włączyć dostęp SSH maszyny wirtualnej, uruchom następujące polecenie, używając nazwy sieciowej grupy zabezpieczeń z poprzedniego kroku:

    ```sh
    az network nsg rule create --name SSH --nsg-name YOUR-NETWORK-SECURITY-GROUP --priority 101 --destination-port-ranges 22 --access Allow --protocol TCP
    ```

    Aby wyświetlić listę reguł ruchu przychodzącego dla danej sieci, uruchom następujące polecenie:

    ```sh
    az network nsg rule list --nsg-name YOUR-NETWORK-SECURITY-GROUP -o table
    ```

1. Aby zmienić hasło, które znasz hasła maszyny wirtualnej, uruchom następujące polecenie. Użyj nazwy maszyny wirtualnej, który już wspomniano i wybrane hasło:

    ```sh
    az vm user update --name YOUR-VM-NAME --username azureuser --password YOUR-PASSWORD
    ```
1. Aby znaleźć adres IP maszyny wirtualnej, użyj następującego polecenia, a następnie zanotuj publicznego adresu IP:

    ```sh
    az vm list-ip-addresses --name YOUR-VM-NAME
    ```

1. Można teraz używanie protokołu SSH, aby połączyć się z maszyną wirtualną. `ssh` Polecenie jest wstępnie zainstalowane w powłoce chmury. Użyj publicznego adresu IP z poprzedniego kroku i po wyświetleniu monitu hasło skonfigurowane dla maszyny wirtualnej:

    ```sh
    ssh azureuser@public-ip-address
    ```

    Masz teraz dostęp do powłoki w maszynę wirtualną, która uruchamia kontenery Docker w rozwiązaniu monitorowania zdalnego. Aby wyświetlić uruchomionych kontenery, użyj następującego polecenia:

    ```sh
    docker ps
    ```

### <a name="find-the-service-connection-strings"></a>Znajdź parametry połączenia z usługą

W samouczku możesz pracować z rozwiązania Visual Studio, która łączy się z usługami rozwiązania Cosmos bazę danych i Centrum IoT rozwiązania. W następujących krokach przedstawiono jeden sposób na znalezienie połączenia wartości ciągów, które są potrzebne:

1. Aby znaleźć ciąg połączenia bazy danych rozwiązania Cosmos, uruchom następujące polecenie w sesji SSH podłączony do maszyny wirtualnej:

    ```sh
    sudo grep STORAGEADAPTER_DOCUMENTDB /app/env-vars
    ```

    Zanotuj parametry połączenia. W dalszej części samouczka możesz użyć tej wartości.

1. Aby znaleźć ciąg połączenia Centrum IoT, uruchom następujące polecenie w sesji SSH podłączony do maszyny wirtualnej:

    ```sh
    sudo grep IOTHUB_CONNSTRING /app/env-vars
    ```

    Zanotuj parametry połączenia. W dalszej części samouczka możesz użyć tej wartości.

> [!NOTE]
> Możesz również znaleźć te parametry połączenia w portalu Azure lub za pomocą `az` polecenia.

### <a name="stop-the-device-simulation-service-in-the-virtual-machine"></a>Zatrzymaj usługę symulacji urządzenia w maszynie wirtualnej

Po zmodyfikowaniu usługi symulacji urządzenia można uruchomić lokalnie, aby przetestować zmiany. Przed uruchomieniem usługi symulacji urządzenia lokalnie, musisz zatrzymać wystąpienie działające na maszynie wirtualnej w następujący sposób:

1. Aby znaleźć **w KONTENERZE o identyfikatorze** z **symulacji urządzenia** usługi, uruchom następujące polecenie w sesji SSH podłączony do maszyny wirtualnej:

    ```sh
    docker ps
    ```

    Zanotuj identyfikator kontenera **symulacji urządzenia** usługi.

1. Aby zatrzymać **symulacji urządzenia** kontenera, uruchom następujące polecenie:

    ```sh
    docker stop container-id-from-previous-step
    ```

### <a name="clone-the-github-repositories"></a>Klonowanie repozytoriów GitHub

W tym samouczku pracować z **symulacji urządzenia** i **adaptera magazynu** projektów programu Visual Studio. Można sklonować repozytoriach kodów źródłowych z usługi GitHub. Ten krok należy wykonać na komputerze deweloperskim lokalnego, gdzie masz zainstalowanego programu Visual Studio:

1. Otwórz wiersz polecenia i przejdź do folderu, w którym chcesz zapisać kopię **symulacji urządzenia** i **adaptera magazynu** repozytoriów GitHub.

1. Klonowanie wersja .NET **symulacji urządzenia** repozytorium, uruchom następujące polecenie:

    ```cmd
    git clone https://github.com/Azure/device-simulation-dotnet.git
    ```

    Usługi symulacji urządzenia w rozwiązaniu monitorowania zdalnego umożliwia zmianę typów wbudowanych symulowane urządzenie i aby utworzyć nowy symulowane typów urządzeń. Typy urządzeń niestandardowych służy do testowania zachowanie rozwiązanie monitorowania zdalnego przed nawiązaniem połączenia urządzenia fizycznego.

1. Klonowanie wersja .NET **adaptera magazynu** repozytorium, uruchom następujące polecenie:

    ```cmd
    git clone https://github.com/Azure/pcs-storage-adapter-dotnet.git
    ```

    Usługa symulacji urządzeń używa usługi karty magazynu się połączyć z usługą rozwiązania Cosmos bazy danych na platformie Azure. To rozwiązanie monitorowanie zdalnego przechowuje dane konfiguracji symulowane urządzenie w bazie danych DB rozwiązania Cosmos.

### <a name="run-the-storage-adapter-service-locally"></a>Uruchom usługę karty magazynu lokalnie

Usługa symulacji urządzeń używa usługi karty magazynu do łączenia z bazą danych DB rozwiązania Cosmos rozwiązania. Jeśli usługa symulacji urządzenia zostanie uruchomiony lokalnie, należy również uruchomić usługę karty magazynu lokalnie. Poniższe kroki pokazują, jak uruchomić usługę karty magazynu w programie Visual Studio:

1. W programie Visual Studio Otwórz **komputerów magazynu adapter.sln** plik rozwiązania w sieci lokalnej klonu **adaptera magazynu** repozytorium.

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **WebService** projektu, wybierz **właściwości**, a następnie wybierz pozycję **debugowania**.

1. W **zmiennych środowiskowych** sekcji, edytowanie wartości **komputerów\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING** zmienną jako połączenie DB rozwiązania Cosmos ciąg, który wcześniej zapisany. Następnie zapisz zmiany.

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **Usługa sieci Web** projektu, wybierz **debugowania**, a następnie wybierz pozycję **Start nowe wystąpienie**.

1. Usługa zacznie działać lokalnie i otwiera `http://localhost:9022/v1/status` w domyślnej przeglądarce. Sprawdź, czy **stan** jest wartość "OK: aktywności i dobrze."

1. Pozostaw karty Usługa magazynu działa lokalnie dopiero po ukończeniu samouczka.

Masz teraz wszystko w miejscu, a wszystko będzie gotowe rozpocząć dodawanie nowego typu symulowane urządzenie do rozwiązania monitorowania zdalnego.

## <a name="create-a-simulated-device-type"></a>Tworzenie typu symulowane urządzenie

Najprostszym sposobem tworzenia nowego typu urządzenia w usłudze symulacji urządzenia jest na skopiowaniu i zmodyfikowaniu istniejącego typu. Poniższe kroki pokazują sposób kopiowania wbudowane **Chłodnica** urządzenia, aby utworzyć nową **żarówka** urządzenia:

1. W programie Visual Studio Otwórz **simulation.sln urządzenia** plik rozwiązania w sieci lokalnej klonu **symulacji urządzenia** repozytorium.

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **SimulationAgent** projektu, wybierz **właściwości**, a następnie wybierz pozycję **debugowania**.

1. W **zmiennych środowiskowych** sekcji, edytowanie wartości **komputerów\_Centrum IOTHUB\_CONNSTRING** zmiennej jako parametry połączenia Centrum IoT zanotowany wcześniej. Następnie zapisz zmiany.

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **WebService** projektu, wybierz **właściwości**, a następnie wybierz pozycję **debugowania**.

1. W **zmiennych środowiskowych** sekcji, edytowanie wartości **komputerów\_Centrum IOTHUB\_CONNSTRING** zmiennej jako parametry połączenia Centrum IoT zanotowany wcześniej. Następnie zapisz zmiany.

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **symulacji urządzenia** rozwiązania i wybierz polecenie **Ustaw projekty startowe**. Wybierz **jednego projektu startowego** i wybierz **WebService**. Następnie kliknij przycisk **OK**.

1. Dla każdego typu urządzenia jest plikiem JSON modelu i skojarzone skrypty w **usług/data/devicemodels** folderu. W Eksploratorze rozwiązań, skopiuj **Chłodnica** pliki, aby utworzyć **żarówka** plików, jak pokazano w poniższej tabeli:

    | Element źródłowy                      | Element docelowy                   |
    | --------------------------- | ----------------------------- |
    | chiller-01.json             | lightbulb-01.json             |
    | scripts/chiller-01-state.js | scripts/lightbulb-01-state.js |
    | skrypty/ponowne uruchomienie komputera method.js    | skrypty/SwitchOn-method.js    |

### <a name="define-the-characteristics-of-the-new-device-type"></a>Zdefiniowanie nowego typu urządzenia

**01.json żarówka** plik definiuje właściwości typu, takich jak dane telemetryczne generuje i obsługuje metod. Następujące kroki aktualizacji **01.json żarówka** pliku, aby określić **żarówka** urządzenia:

1. W **01.json żarówka** pliku, zaktualizuj metadane urządzenia, jak pokazano w poniższy fragment kodu:

    ```json
    "SchemaVersion": "1.0.0",
    "Id": "lightbulb-01",
    "Version": "0.0.1",
    "Name": "Lightbulb",
    "Description": "Smart lightbulb device.",
    "Protocol": "MQTT",
    ```

1. W **01.json żarówka** plików, należy zaktualizować definicję symulacji pokazane na następujący fragment kodu:

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

1. W **01.json żarówka** plików, aktualizowanie właściwości typu urządzenia, jak pokazano w poniższy fragment kodu:

    ```json
    "Properties": {
      "Type": "Lightbulb",
      "Color": "White",
      "Brightness": 75,
      "EstimatedRemainingLife": 10000
    },
    ```

1. W **01.json żarówka** plików, aktualizację definicji telemetrii typu urządzenia, jak pokazano w poniższy fragment kodu:

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

1. W **01.json żarówka** plików, zaktualizować metody typu urządzenia, jak pokazano w poniższy fragment kodu:

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

1. Zapisz **01.json żarówka** pliku.

### <a name="simulate-custom-device-behavior"></a>Symulowanie zachowanie urządzeń niestandardowych

**Skryptów/żarówka-01-state.js** plik definiuje zachowanie symulacji **żarówka** typu. Następujące kroki aktualizacji **skryptów/żarówka-01-state.js** pliku, aby określić zachowanie **żarówka** urządzenia:

1. Edytowanie definicji stanu w **skryptów/żarówka-01-state.js** plików, jak pokazano w poniższy fragment kodu:

    ```js
    // Default state
    var state = {
      online: true,
      temperature: 200.0,
      temperature_unit: "F",
      status: "on"
    };
    ```

1. Dodaj **Przerzuć** działanie po **różnią się** funkcji z definicji następujących:

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

1. Edytuj **głównego** funkcji, aby zaimplementować zachowanie, jak pokazano w poniższy fragment kodu:

    ```js
    function main(context, previousState) {

      // Restore the global state before generating the new telemetry, so that
      // the telemetry can apply changes using the previous function state.
      restoreState(previousState);

      state.temperature = vary(200, 5, 150, 250);

      // Make this flip every so often
      state.status = flip(state.status);

      return state;
    }
    ```

1. Zapisz **skryptów/żarówka-01-state.js** pliku.

**Skryptów/SwitchOn-method.js** pliku implementuje **przełącznika na** metody w **żarówka** urządzenia. Następujące kroki aktualizacji **skryptów/SwitchOn-method.js** pliku:

1. Edytowanie definicji stanu w **skryptów/SwitchOn-method.js** plików, jak pokazano w poniższy fragment kodu:

    ```js
    var state = {
       status: "on"
    };
    ```

1. Aby przełączyć się na żarówka, należy edytować **głównego** działają w następujący sposób:

    ```js
    function main(context, previousState) {
        log("Executing lightbulb Switch On method.");
        state.status = "on";
        updateState(state);
    }
    ```

1. Zapisz **skryptów/SwitchOn-method.js** pliku.

1. Utwórz kopię **skryptów/SwitchOn-method.js** plik o nazwie **skryptów/SwitchOff-method.js**.

1. Aby wyłączyć żarówka, należy edytować **głównego** działać w **skryptów/SwitchOff-method.js** plików w następujący sposób:

    ```js
    function main(context, previousState) {
        log("Executing lightbulb Switch Off method.");
        state.status = "off";
        updateState(state);
    }
    ```

1. Zapisz **skryptów/SwitchOff-method.js** pliku.

1. W Eksploratorze rozwiązań wybierz każdego z czterech nowych plików z kolei. W **właściwości** okna dla każdego pliku, upewnij się, że **Kopiuj do katalogu wyjściowego** ustawiono **Kopiuj, jeśli nowszy**.

### <a name="configure-the-device-simulation-service"></a>Skonfiguruj usługę symulacji urządzenia

Aby ograniczyć liczbę symulowanego urządzenia, podłączane do rozwiązania podczas testowania, należy skonfigurować usługę do uruchomienia jednym Chłodnica i żarówka pojedynczego urządzenia. Dane konfiguracji są przechowywane w wystąpieniu bazy danych rozwiązania Cosmos w grupie zasobów tego rozwiązania. Aby edytować dane konfiguracji, należy użyć **Eksplorator chmury** widoku w programie Visual Studio:

1. Aby otworzyć **Eksplorator chmury** w programie Visual Studio wybierz pozycję **widoku** , a następnie **Eksplorator chmury**.

1. Można znaleźć w dokumentacji konfiguracji symulacji **wyszukiwanie zasobów** wprowadź **simualtions.1**.

1. Kliknij dwukrotnie **simulations.1** dokument, aby otworzyć do edycji.

1. Wartości **danych**, zlokalizuj **DeviceModels** tablicy, która wygląda podobnie poniższy fragment kodu:

    ```json
    [{\"Id\":\"chiller-01\",\"Count\":1},{\"Id\":\"chiller-02\",\"Count\":1},{\"Id\":\"elevator-01\",\"Count\":1},{\"Id\":\"elevator-02\",\"Count\":1},{\"Id\":\"engine-01\",\"Count\":1},{\"Id\":\"engine-02\",\"Count\":1},{\"Id\":\"prototype-01\",\"Count\":1},{\"Id\":\"prototype-02\",\"Count\":1},{\"Id\":\"truck-01\",\"Count\":1},{\"Id\":\"truck-02\",\"Count\":1}]
    ```

1. Aby określić pojedynczy Chłodnica i jednego żarówka symulowane urządzenie, należy zastąpić **DeviceModels** tablicy z następującym kodem:

    ```json
    [{\"Id\":\"chiller-01\",\"Count\":1},{\"Id\":\"lightbulb-01\",\"Count\":1}]
    ```

    Zapisz zmiany **simulations.1** dokumentu.

> [!NOTE]
> W Eksploratorze rozwiązania Cosmos DB danych można użyć w portalu Azure, aby edytować **simulations.1** dokumentu.

### <a name="test-the-lightbulb-device-type-locally"></a>Typ urządzenia żarówka lokalnie testu

Teraz można przystąpić do testowania nowego typu symulowane żarówka lokalnie uruchamiając projekt symulacji urządzenia.

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy **WebService**, wybierz **debugowania** , a następnie wybierz **Start nowe wystąpienie**.

1. Aby sprawdzić, czy dwa symulowanego urządzenia są podłączone do Centrum IoT, otwórz Azure portal w przeglądarce.

1. Przejdź do Centrum IoT w grupie zasobów, zawierającą rozwiązanie monitorowania zdalnego.

1. W **monitorowanie** wybierz **metryki**. Sprawdź, czy liczba **urządzeń podłączonych** jest dwa:

    ![Liczba połączonych urządzeń](./media/iot-accelerators-remote-monitoring-test/connecteddevices.png)

1. W przeglądarce przejdź do **pulpitu nawigacyjnego** rozwiązania do monitorowania zdalnego. W panelu dane telemetryczne w **pulpitu nawigacyjnego**, wybierz pozycję **temperatury**. Wyświetla temperatury dla wszystkich urządzeń symulowane na wykresie:

    ![Dane telemetryczne temperatury](./media/iot-accelerators-remote-monitoring-test/telemetry.png)

Masz teraz symulacji urządzenia żarówka uruchomionej na komputerze lokalnym. Następnym krokiem jest aby wdrożyć kod symulatora zaktualizowane do maszyny wirtualnej, która działa mikrousług monitorowania zdalnego w systemie Azure.

Przed kontynuowaniem, można zatrzymać debugowania symulacji urządzenia i projekty karty magazynu w programie Visual Studio.

### <a name="deploy-the-updated-simulator-to-the-cloud"></a>Zaktualizowano symulatora wdrażać w chmurze

Uruchom mikrousług w rozwiązaniu Monitorowanie zdalne w kontenerach docker. Kontenery znajdują się w maszynie wirtualnej rozwiązania na platformie Azure. W tej sekcji omówiono następujące zagadnienia:

* Utwórz nowy obraz docker symulacji urządzenia.
* Przekaż obraz do repozytorium Centrum docker.
* Zaimportować go do rozwiązania maszyny wirtualnej.

W następujących krokach założono, że masz repozytorium o nazwie **żarówka** na Twoim koncie Centrum Docker.

1. W programie Visual Studio w **symulacji urządzenia** projekt, otwórz plik **solution\scripts\docker\build.cmd**.

1. Edytuj wiersz, który ustawia **DOCKER_IMAGE** zmiennej środowiskowej do nazwy Centrum Docker repozytorium:

    ```cmd
    SET DOCKER_IMAGE=your-docker-hub-acccount/lightbulb
    ```

    Zapisz zmiany.

1. W programie Visual Studio w **symulacji urządzenia** projekt, otwórz plik **solution\scripts\docker\publish.cmd**.

1. Edytuj wiersz, który ustawia **DOCKER_IMAGE** zmiennej środowiskowej do nazwy Centrum Docker repozytorium:

    ```cmd
    SET DOCKER_IMAGE=your-docker-hub-acccount/lightbulb
    ```

    Zapisz zmiany.

1. Otwórz wiersz polecenia jako administrator. Następnie przejdź do folderu **scripts\docker** w Twojej klonu **symulacji urządzenia** repozytorium GitHub.

1. Aby utworzyć obraz docker, uruchom następujące polecenie:

    ```cmd
    build.cmd
    ```

1. Aby zalogować się do Centrum Docker konta, uruchom następujące polecenie:

    ```cmd
    docker login
    ```

1. Aby przekazać nowy obraz do Centrum Docker konta, uruchom następujące polecenie:

    ```cmd
    publish.cmd
    ```

1. Aby sprawdzić, przekazywania, przejdź do [ https://hub.docker.com/ ](https://hub.docker.com/). Znajdź użytkownika **żarówka** repozytorium i wybierz polecenie **szczegóły**. Następnie wybierz pozycję **tagi**:

    ![Centrum docker](./media/iot-accelerators-remote-monitoring-test/dockerhub.png)

    Skrypty dodane **testowania** tag obrazu.

1. Używanie protokołu SSH do nawiązania połączenia tego rozwiązania maszyny wirtualnej platformy Azure. Następnie przejdź do **aplikacji** folderu i edytowanie **docker compose.yaml** pliku:

    ```sh
    cd /app
    sudo nano docker-compose.yaml
    ```

1. Edytuj wpis dla usługi symulacji urządzeń do obrazu docker:

    ```yaml
    devicesimulation:
      image: {your docker ID}/lightbulb:testing
    ```

    Zapisz zmiany.

1. Ponowne uruchomienie wszystkich usług przy użyciu nowych ustawień, uruchom następujące polecenie:

    ```sh
    sudo ./start.sh
    ```

1. Aby sprawdzić plik dziennika z Twojego nowego kontenera symulacji urządzenia, uruchom następujące polecenie, aby znaleźć identyfikator kontenera:

    ```sh
    docker ps
    ```

    Następnie uruchom następujące polecenie, używając Identyfikatora kontenera:

    ```sh
    docker logs {container ID}
    ```

Teraz wykonano kroki, aby zainstalować zaktualizowaną wersję usługi symulacji urządzeń do monitorowania zdalnego rozwiązania.

W przeglądarce przejdź do **pulpitu nawigacyjnego** rozwiązania do monitorowania zdalnego. W panelu dane telemetryczne w **pulpitu nawigacyjnego**, wybierz pozycję **temperatury**. Wyświetla temperatury dwóch symulowane urządzeń na wykresie:

![Dane telemetryczne temperatury](./media/iot-accelerators-remote-monitoring-test/telemetry.png)

Na **urządzeń** strony, można udostępnić wystąpienia nowego typu:

![Umożliwia wyświetlenie listy dostępnych symulacje](./media/iot-accelerators-remote-monitoring-test/devicesmodellist.png)

Można wyświetlić dane telemetryczne z symulowane urządzenie:

![Dane telemetryczne wyświetleń żarówka](./media/iot-accelerators-remote-monitoring-test/devicestelemetry.png)

Możesz wywołać **SwitchOn** i **SwitchOff** metod na urządzeniu:

![Wywołanie metody żarówka](./media/iot-accelerators-remote-monitoring-test/devicesmethods.png)

## <a name="add-a-new-telemetry-type"></a>Dodaj nowy typ telemetrii

Ta sekcja opisuje sposób modyfikowania istniejącego typu symulowane urządzenie na potrzeby obsługi nowego typu danych telemetrycznych.

### <a name="locate-the-chiller-device-type-files"></a>Zlokalizuj pliki Chłodnica typ urządzenia

Następujących krokach przedstawiono sposób wyszukiwania plików, które definiują wbudowane **Chłodnica** urządzenia:

1. Jeśli nie zostało to jeszcze zrobione, użyj następującego polecenia, można sklonować **symulacji urządzenia** repozytorium GitHub na komputerze lokalnym:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git
    ```

1. Dla każdego typu urządzenia jest plikiem JSON modelu i skojarzone skrypty w `data/devicemodels` folderu. Plików, które definiują symulowane **Chłodnica** są typu urządzenia:

    * **data/devicemodels/chiller-01.json**
    * **data/devicemodels/scripts/chiller-01-state.js**

### <a name="specify-the-new-telemetry-type"></a>Określ nowy typ telemetrii

Następujących krokach przedstawiono sposób dodawania nowego **temperatury wewnętrznej** typ **Chłodnica** typu urządzenia:

1. Otwórz **01.json Chłodnica** pliku.

1. Aktualizacja **SchemaVersion** wartości w następujący sposób:

    ```json
    "SchemaVersion": "1.1.0",
    ```

1. W **stan początkowy** Dodaj definicje Oto dwa:

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

1. Dodaj następujące pola do **stanu** zmienną:

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

Aby przetestować zaktualizowane **Chłodnica** typu urządzenia, najpierw uruchom lokalną kopię **symulacji urządzenia** usługi, aby przetestować danego typu urządzenia działa zgodnie z oczekiwaniami. Po przetestowane i debugować danego typu urządzenia zaktualizowane lokalnie, należy odbudować kontenera i wdrożenie **symulacji urządzenia** usługi na platformie Azure.

Po uruchomieniu **symulacji urządzenia** usługi lokalnie, wysyła dane telemetryczne do monitorowania zdalnego rozwiązania. Na **urządzeń** strony, można udostępnić wystąpienia typu zaktualizowane.

Do testowania i debugowania zmiany lokalnie, zawiera Poprzednia sekcja [lokalnie typ urządzenia żarówka testu](#test-the-lightbulb-device-type-locally).

Aby wdrożyć usługi symulacji zaktualizowanych urządzeniach rozwiązania maszyny wirtualnej platformy Azure, zobacz poprzednią sekcję [wdrażać zaktualizowane symulatora w chmurze](#deploy-the-updated-simulator-to-the-cloud).

Na **urządzeń** strony, można udostępnić wystąpienia typu zaktualizowane:

![Dodaj Chłodnica zaktualizowane](./media/iot-accelerators-remote-monitoring-test/devicesupdatedchiller.png)

Możesz wyświetlić nowy **temperatury wewnętrznej** dane telemetryczne z symulowane urządzenie.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono należy jak do:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Tworzenie nowego typu urządzenia
> * Symulowanie zachowanie urządzeń niestandardowych
> * Dodawanie nowego typu urządzenia do pulpitu nawigacyjnego
> * Wysyłać dane telemetryczne niestandardowych z istniejącym typem urządzenia

Teraz uzyskanych jak dostosować usługę symulacji urządzenia. Sugerowane następnym krokiem jest, aby dowiedzieć się jak [fizyczne urządzenie podłączone do rozwiązania monitorowania zdalnego](iot-accelerators-connecting-devices-node.md).

Aby uzyskać więcej informacji o deweloperów o rozwiązaniu monitorowania zdalnego Zobacz:

* [Przewodnik informacyjny dla deweloperów](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Przewodnik po rozwiązywaniu problemów dla deweloperów](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->
