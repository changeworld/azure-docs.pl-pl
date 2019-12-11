---
title: Samouczek — Definiowanie nowego typu urządzenia Azure IoT Edge na platformie Azure IoT Central
description: W tym samouczku pokazano, jak utworzyć za pomocą konstruktora nowe urządzenie Azure IoT Edge w aplikacji IoT Central platformy Azure. Zdefiniuj dane telemetryczne, stan, właściwości i polecenia dla danego typu.
author: rangv
ms.author: rangv
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 00ab92effbc5167d8bca3242e55d566c71209ac4
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74979091"
---
# <a name="tutorial-define-a-new-azure-iot-edge-device-type-in-your-azure-iot-central-application-preview-features"></a>Samouczek: Definiowanie nowego typu urządzenia Azure IoT Edge w aplikacji IoT Central platformy Azure (funkcje w wersji zapoznawczej)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

W tym samouczku pokazano, jak utworzyć konstruktora, jak używać szablonu urządzenia do definiowania nowego typu urządzenia Azure IoT Edge w aplikacji IoT Central platformy Azure. 

Aby zapoznać się z omówieniem, zobacz artykuł [co to jest usługa Azure IoT Central (funkcje w wersji zapoznawczej)?](overview-iot-central.md). 

IoT Edge składa się z trzech składników:
* **Moduły IoT Edge** są kontenerami, na których działają usługi platformy Azure, usługi partnerskie lub własny kod. Moduły są wdrażane na IoT Edge urządzeniach i uruchamiane lokalnie na tych urządzeniach.
* **Środowisko uruchomieniowe IoT Edge** działa na każdym urządzeniu IoT Edge i zarządza modułami wdrożonymi na poszczególnych urządzeniach.
* **Interfejs oparty na chmurze** umożliwia zdalne monitorowanie IoT Edge urządzeń i zarządzanie nimi. IoT Central jest interfejsem chmury.

Urządzenie **Azure IoT Edge** może być urządzeniem bramy z urządzeniami podrzędnymi łączącymi się z urządzeniem IoT Edge. Ten samouczek udostępnia więcej informacji na temat wzorców łączności urządzeń podrzędnych.

**Szablon urządzenia** definiuje możliwości urządzenia i moduły IoT Edge. Możliwości obejmują dane telemetryczne, które są wysyłane przez moduł, właściwości modułu i polecenia, na które odpowiada moduł.

W tym samouczku utworzysz szablon urządzenia czujnika środowiska. Urządzenie czujnika środowiska:

* Wysyła dane telemetryczne, takie jak temperatura.
* Reaguje na właściwości zapisywalne w chmurze, takie jak interwał wysyłania danych telemetrycznych.
* Reaguje na polecenia, takie jak resetowanie temperatury.

Ponadto w tym samouczku utworzysz szablon urządzenia bramy środowiska. Urządzenie bramy środowiskowej:

* Wysyła dane telemetryczne, takie jak temperatura.
* Reaguje na właściwości zapisywalne w chmurze, takie jak interwał wysyłania danych telemetrycznych.
* Reaguje na polecenia, takie jak resetowanie temperatury.
* Umożliwia relacje z innymi modelami możliwości urządzeń.


Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz nowy szablon urządzenia Azure IoT Edge.
> * Przekaż manifest wdrożenia.
> * Tworzenie funkcji, w tym telemetrii, właściwości i poleceń dla każdego modułu.
> * Zdefiniuj wizualizację dla telemetrii modułu.
> * Dodaj relacje do szablonów urządzeń podrzędnych.
> * Opublikuj szablon urządzenia.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, musisz [utworzyć aplikację usługi Azure IoT Central](quick-deploy-iot-central.md).


## <a name="downstream-device-relationships-with-a-gateway-and-modules"></a>Relacje urządzeń podrzędnych z bramą i modułami

Urządzenia podrzędne mogą łączyć się z urządzeniem bramy IoT Edge za pomocą modułu `$edgeHub`. To IoT Edge urządzenie zostanie w tym scenariuszu przezroczystą bramą.

![Diagram przezroczystej bramy](./media/tutorial-define-edge-device-type/gateway-transparent.png)

Urządzenia podrzędne mogą również łączyć się z urządzeniem bramy IoT Edge za pomocą modułu niestandardowego. W poniższym scenariuszu urządzenia podrzędne łączą się za pomocą modułu niestandardowego Modbus.

![Diagram połączenia modułu niestandardowego](./media/tutorial-define-edge-device-type/gateway-module.png)

Na poniższym diagramie przedstawiono połączenie z urządzeniem bramy IoT Edge za pomocą obu typów modułów (niestandardowych i `$edgeHub`).  

![Diagram łączenia za pośrednictwem obu modułów połączeń](./media/tutorial-define-edge-device-type/gateway-module-transparent.png)

Na koniec urządzenia podrzędne mogą łączyć się z urządzeniem bramy IoT Edge przy użyciu wielu modułów niestandardowych. Na poniższym diagramie przedstawiono urządzenia podrzędne łączące się za pomocą modułu niestandardowego Modbus, modułu niestandardowego dla beli i modułu `$edgeHub`. 

![Diagram łączenia za pośrednictwem wielu modułów niestandardowych](./media/tutorial-define-edge-device-type/gateway-module2-transparent.png)


## <a name="create-a-template"></a>Tworzenie szablonu

Jako Konstruktor można tworzyć i edytować szablony urządzeń IoT Edge w aplikacji. Po opublikowaniu szablonu urządzenia można nawiązać połączenie z rzeczywistymi urządzeniami, które implementują szablon urządzenia.

### <a name="select-device-template-type"></a>Wybierz typ szablonu urządzenia 

Aby dodać nowy szablon urządzenia do aplikacji, wybierz pozycję **Szablony urządzeń** w okienku po lewej stronie.

![Zrzut ekranu aplikacji w wersji zapoznawczej z wyróżnionymi szablonami urządzeń](./media/tutorial-define-edge-device-type/edgedevicetemplate.png)

Wybierz pozycję **+ Nowy** , aby rozpocząć tworzenie nowego szablonu urządzenia.

![Zrzut ekranu przedstawiający stronę szablony urządzeń z nowymi wyróżnionymi](./media/tutorial-define-edge-device-type/edgedevicetemplatenew.png)

Na stronie **Wybieranie typu szablonu** wybierz pozycję **Azure IoT Edge**, a następnie wybierz pozycję **Dalej: Dostosuj**.

![Zrzut ekranu przedstawiający stronę Wybieranie typu szablonu](./media/tutorial-define-edge-device-type/selectiotedge.png)

### <a name="customize-device-template"></a>Dostosuj szablon urządzenia

W IoT Edge można wdrożyć logikę biznesową w formie modułów i zarządzać nią. Moduły IoT Edge są najmniejszą jednostką obliczeniową zarządzaną przez IoT Edge i mogą zawierać usługi platformy Azure (takie jak Azure Stream Analytics) lub własny kod specyficzny dla rozwiązania. Aby zrozumieć, jak moduły są opracowywane, wdrażane i utrzymywane, zobacz [IoT Edge modules](../../iot-edge/iot-edge-modules.md).

Na wysokim poziomie manifest wdrażania znajduje się lista bliźniaczych reprezentacjach modułów, które są skonfigurowane przy użyciu ich żądane właściwości. Manifest wdrożenia informuje urządzenie IoT Edge (lub grupę urządzeń), które moduły zainstalować, oraz sposób ich konfiguracji. Manifesty wdrożenia obejmują odpowiednie właściwości dla każdego sznurka modułu. IoT Edge urządzeń raportuje z powrotem raportowane właściwości dla każdego modułu.

Użyj Visual Studio Code, aby utworzyć manifest wdrożenia. Aby dowiedzieć się więcej, zobacz [Azure IoT Edge Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).

Oto podstawowy manifest wdrożenia z jednym modułem, który ma być używany w tym samouczku. Skopiuj poniższy kod JSON i Zapisz go jako plik JSON. 

   ```JSON
   {
     "modulesContent": {
       "$edgeAgent": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "runtime": {
             "type": "docker",
             "settings": {
               "minDockerVersion": "v1.25",
               "loggingOptions": "",
               "registryCredentials": {}
             }
           },
           "systemModules": {
             "edgeAgent": {
               "type": "docker",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                 "createOptions": "{}"
               }
             },
             "edgeHub": {
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                 "createOptions": "{}"
               }
             }
           },
           "modules": {
             "SimulatedTemperatureSensor": {
               "version": "1.0",
               "type": "docker",
               "status": "running",
               "restartPolicy": "always",
               "settings": {
                 "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                 "createOptions": "{}"
               }
             }
           }
         }
       },
       "$edgeHub": {
         "properties.desired": {
           "schemaVersion": "1.0",
           "routes": {
               "route": "FROM /* INTO $upstream"
           },
           "storeAndForwardConfiguration": {
             "timeToLiveSecs": 7200
           }
         }
       },
       "SimulatedTemperatureSensor": {
         "properties.desired": {
              "SendData": true,
              "SendInterval": 10
         }
       }
     }
   }
   ```

#### <a name="upload-an-iot-edge-deployment-manifest"></a>Przekazywanie manifestu wdrażania IoT Edge

Na stronie **Dostosowywanie urządzenia** w obszarze **Przekaż Azure IoT Edge manifest wdrożenia**wybierz pozycję **Przeglądaj**. 

![Zrzut ekranu przedstawiający stronę Dostosowywanie urządzenia z wyróżnioną pozycją Przeglądaj](./media/tutorial-define-edge-device-type/edgedevicetemplateuploadmanifest.png)

Jeśli planujesz utworzenie szablonu urządzenia bramy IoT Edge, upewnij się, że wybrano opcję **urządzenie bramy z urządzeniami podrzędnymi**.

![Zrzut ekranu przedstawiający stronę Dostosowywanie urządzenia z zaznaczonym urządzeniem z bramą z urządzeniami podrzędnymi](./media/tutorial-define-edge-device-type/gateway-upload.png)

W oknie dialogowym Wybieranie pliku wybierz plik manifestu wdrożenia, a następnie wybierz pozycję **Otwórz**.

IoT Edge sprawdza poprawność pliku manifestu wdrożenia względem schematu. Jeśli sprawdzanie poprawności zakończy się pomyślnie, wybierz pozycję **Przegląd**.

![Zrzut ekranu przedstawiający stronę Dostosowywanie urządzenia z manifestem wdrożenia i wyróżnioną recenzję](./media/tutorial-define-edge-device-type/deploymentmanifestvalidate.png)

Poniższy schemat blokowy przedstawia cykl życia manifestu wdrożenia w IoT Central.

![Schemat blokowy cyklu życia manifestu wdrożenia](./media/tutorial-define-edge-device-type/dmflow.png)

Następnie zobaczysz stronę przeglądu z szczegółowymi informacjami dotyczącymi manifestu wdrażania. Ta strona zawiera listę modułów z manifestu wdrożenia. W tym samouczku należy zwrócić uwagę na to, że moduł `SimulatedTemperatureSensor` znajduje się na liście. Wybierz pozycję **Utwórz**.

![Zrzut ekranu strony przegląd z wyróżnionym modułem i utworzeniem](./media/tutorial-define-edge-device-type/edgedevicetemplatereview.png)

W przypadku wybrania urządzenia bramy zostanie wyświetlona następująca strona przeglądu.

![Zrzut ekranu strony przegląd z wyróżnioną bramą Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-review.png)


Tworzysz szablon urządzenia z modelami możliwości modułów. W tym samouczku utworzysz szablon urządzenia z modelem możliwości modułu `SimulatedTemperatureSensor`. 

Zmień tytuł szablonu urządzenia na **szablon urządzenia czujnika środowiska**.

![Zrzut ekranu szablonu urządzenia z wyróżnionym tytułem zaktualizowany](./media/tutorial-define-edge-device-type/edgedevicetemplatelanding.png)

Na urządzeniu IoT Edge model IoT Plug and Play w następujący sposób:
* Każdy szablon urządzenia IoT Edge ma model możliwości urządzenia.
* Dla każdego modułu niestandardowego wymienionego w manifeście wdrożenia jest generowany model możliwości modułu.
* Między każdym modelem możliwości modułu a modelem możliwości urządzenia jest ustanowiona relacja.
* Model możliwości modułu implementuje interfejsy modułów.
* Każdy interfejs modułu zawiera dane telemetryczne, właściwości i polecenia.

![Diagram modelowania IoT Edge](./media/tutorial-define-edge-device-type/edgemodelling.png)

#### <a name="add-capabilities-to-a-module-capability-model"></a>Dodawanie możliwości do modelu możliwości modułu

Oto przykładowe dane wyjściowe z modułu `SimulatedTemperatureSensor`:
```json
{

    "machine": {

        "temperature": 75.0,
        "pressure": 40.2
    },
    "ambient": {
        "temperature": 23.0,
        "humidity": 30.0
    },
    "timeCreated": ""
}
```

Można dodać możliwości do modułu `SimulatedTemperatureSensor`, który będzie odzwierciedlał poprzednie dane wyjściowe. 

1. Aby zarządzać interfejsem modelu możliwości modułu `SimulatedTemperatureSensor`, wybierz pozycję **zarządzaj** > **Dodaj funkcję**. 

    ![Zrzut ekranu szablonu czujnika środowiska z wyróżnioną opcją Dodaj funkcję](./media/tutorial-define-edge-device-type/edgetemplateaddcapability.png)
  
1. Dodaj maszynę jako typ obiektu.
  
    ![Zrzut ekranu przedstawiający stronę możliwości szablonu czujnika środowiska z wyróżnionym schematem](./media/tutorial-define-edge-device-type/edgetemplatemachineobject.png)

1. Wybierz pozycję **Definiuj**. W wyświetlonym oknie dialogowym Zmień nazwę obiektu na **Machine**. Utwórz właściwości temperatury i ciśnienia i wybierz pozycję **Zastosuj**.
  
    ![Zrzut ekranu przedstawiający okno dialogowe atrybutów z różnymi opcjami wyróżnionymi](./media/tutorial-define-edge-device-type/edgetemplatemachineattributes.png)
  
1. Dodaj otoczenie jako typ obiektu.

1. Wybierz pozycję **Definiuj**. W wyświetlonym oknie dialogowym Zmień nazwę obiektu na **otoczenia**. Utwórz właściwości temperatury i wilgotności i wybierz pozycję **Zastosuj**.
  
    ![Zrzut ekranu przedstawiający okno dialogowe atrybutów z różnymi opcjami wyróżnionymi](./media/tutorial-define-edge-device-type/edgetemplateambientattributes.png)

  
1. Dodaj `timeCreated` jako typ `DateTime` i wybierz pozycję **Zapisz**.
  
    ![Zrzut ekranu szablonu czujnika środowiska z wyróżnionym zapisem](./media/tutorial-define-edge-device-type/edgetemplateallattributes.png)


### <a name="add-relationships"></a>Dodaj relacje

Jeśli wybrano urządzenie IoT Edge jako urządzenie bramy, można dodać relacje podrzędne do modeli możliwości urządzeń, które mają być połączone z urządzeniem bramy.
  
  ![Zrzut ekranu szablonu bramy środowiska z wyróżnioną opcją Dodaj relację](./media/tutorial-define-edge-device-type/gateway-add-relationship.png)

Można dodać relację na urządzeniu lub w module.
  
  ![Zrzut ekranu szablonu bramy środowiska z wyróżnionymi relacjami na poziomie urządzenia i modułu](./media/tutorial-define-edge-device-type/gateway-relationship-types.png)


Można wybrać model możliwości urządzenia podrzędnego lub wybrać symbol gwiazdki. 
  
  ![Zrzut ekranu szablonu bramy środowiska z wyróżnionym elementem docelowym](./media/tutorial-define-edge-device-type/gateway-downstream-rel.png)

  Na potrzeby tego samouczka Wybierz gwiazdkę. Ta opcja umożliwia wszelką relację podrzędny. Następnie wybierz pozycję **Zapisz**.

  ![Zrzut ekranu szablonu bramy środowiska z wyróżnionym elementem docelowym](./media/tutorial-define-edge-device-type/gateway-add-relationship-asterix.png)


### <a name="add-cloud-properties"></a>Dodawanie właściwości chmury

Szablon urządzenia może zawierać właściwości chmury. Właściwości chmury istnieją tylko w aplikacji IoT Central i nigdy nie są wysyłane do ani odbierane z urządzenia.

1. Wybierz pozycję **właściwości chmury** >  **+ Dodaj właściwość chmury**. Skorzystaj z informacji podanych w poniższej tabeli, aby dodać właściwość chmury do szablonu urządzenia.

    | Nazwa wyświetlana      | Typ semantyczny | Schemat |
    | ----------------- | ------------- | ------ |
    | Data ostatniego serwisowania | Brak          | Data   |
    | Nazwa klienta     | Brak          | Ciąg |

2. Wybierz pozycję **Zapisz**.

  
    ![Zrzut ekranu szablonu czujnika środowiska z wyróżnionym zapisem](./media/tutorial-define-edge-device-type/edgetemplatecloudproperties.png)

### <a name="add-customizations"></a>Dodawanie dostosowań

Użyj opcji dostosowania, aby zmodyfikować interfejs lub dodać funkcje specyficzne dla IoT Central do możliwości, która nie wymaga użycia modelu możliwości urządzenia. Można dostosować pola, gdy model możliwości jest w stanie roboczym lub opublikowanym. Można dostosować tylko pola, które nie łamią zgodności interfejsów. Możesz na przykład:

- Dostosuj nazwę wyświetlaną i jednostki możliwości.
- Dodaj domyślny kolor, który ma być używany, gdy wartość pojawia się na wykresie.
- Określ początkową, minimalną i maksymalną wartość właściwości.

Nie można dostosować nazwy możliwości ani typu możliwości.

Po zakończeniu dostosowywania wybierz pozycję **Zapisz**.
  
![Zrzut ekranu szablonu czujnika środowiska — Dostosowywanie strony](./media/tutorial-define-edge-device-type/edgetemplatecustomize.png)


### <a name="create-views"></a>Tworzenie widoków

Jako Konstruktor można dostosować aplikację tak, aby wyświetlała odpowiednie informacje o urządzeniu czujnika środowiska z operatorem. Twoje dostosowania umożliwiają operatorowi zarządzanie urządzeniami czujnika środowiskowego podłączonymi do aplikacji. Można utworzyć dwa typy widoków dla operatora, który ma być używany do współpracy z urządzeniami:

* Formularze do wyświetlania i edytowania właściwości urządzenia i chmury.
* Pulpity nawigacyjne do wizualizacji urządzeń.

### <a name="configure-a-view-to-visualize-devices"></a>Konfigurowanie widoku do wizualizacji urządzeń

Pulpit nawigacyjny urządzenia umożliwia operatorowi wizualizację urządzenia za pomocą wykresów i metryk. Jako Konstruktor można określić, jakie informacje są wyświetlane na pulpicie nawigacyjnym urządzenia. Można zdefiniować wiele pulpitów nawigacyjnych dla urządzeń. Aby utworzyć pulpit nawigacyjny do wizualizacji danych telemetrycznych czujnika środowiska, wybierz pozycję **widoki** > **wizualizacji urządzenia**:

  
![Zrzut ekranu przedstawiający stronę widoków szablonu czujnika środowiska i wizualizację wyróżnionego urządzenia](./media/tutorial-define-edge-device-type/visualizingthedevice.png)


Dane telemetryczne otaczające i telemetrię są obiektami złożonymi. Aby utworzyć wykresy:

1. Przeciągnij **telemetrię**i wybierz pozycję **Wykres liniowy**. 
  
   ![Zrzut ekranu szablonu czujnika środowiska z wyróżnioną telemetrię i wykresem liniowym](./media/tutorial-define-edge-device-type/sensorambientchart.png)

1. Wybierz ikonę Konfiguruj. Wybierz pozycję **temperatura** i **wilgotność** , aby wizualizować dane, a następnie wybierz pozycję **Konfiguracja aktualizacji**. 
  
   ![Zrzut ekranu szablonu czujnika środowiska z różnymi opcjami wyróżnionymi](./media/tutorial-define-edge-device-type/sensorambienttelemetrychart.png)

1. Wybierz pozycję **Zapisz**.

Można dodać więcej kafelków, które pokazują inne właściwości lub wartości telemetrii. Możesz również dodać tekst statyczny, linki i obrazy. Aby przenieść lub zmienić rozmiar kafelka na pulpicie nawigacyjnym, przesuń wskaźnik myszy nad kafelek i przeciągnij kafelek do nowej lokalizacji lub zmień jego rozmiar.
  
![Zrzut ekranu przedstawiający widok pulpitu nawigacyjnego szablonu czujnika środowiska](./media/tutorial-define-edge-device-type/viewsdashboard.png)

### <a name="add-a-device-form"></a>Dodaj formularz urządzenia

Formularz urządzenia umożliwia operatorowi Edytowanie właściwości urządzenia z możliwością zapisu i właściwości chmury. Jako Konstruktor można definiować wiele formularzy i wybierać, które urządzenia i właściwości chmury mają być wyświetlane w każdym formularzu. Możesz również wyświetlić właściwości urządzenia tylko do odczytu w formularzu.

Aby utworzyć formularz do wyświetlania i edytowania właściwości czujnika środowiska:

1. W **szablonie czujnika środowiska**przejdź do pozycji **widoki**. Wybierz kafelek **Edytowanie urządzenia i danych w chmurze** , aby dodać nowy widok.
  
   ![Zrzut ekranu przedstawiający stronę widoków szablonu czujnika środowiska, z wyróżnioną pozycją Edycja i dane w chmurze](./media/tutorial-define-edge-device-type/editingdeviceandclouddata.png)

1. Wprowadź nazwę formularza **Właściwości czujnika środowiska**.

1. Przeciągnij właściwości **Nazwa klienta** i **Data ostatniej usługi** do istniejącej sekcji w formularzu.
  
   ![Zrzut ekranu przedstawiający stronę widoków szablonu czujnika środowiska z różnymi opcjami wyróżnionymi](./media/tutorial-define-edge-device-type/views-properties.png)

1. Wybierz pozycję **Zapisz**.

## <a name="publish-a-device-template"></a>Publikowanie szablonu urządzenia

Aby można było utworzyć symulowany czujnik środowiska lub połączyć prawdziwy czujnik środowiska, należy opublikować szablon urządzenia.

Aby opublikować szablon urządzenia:

1. Przejdź do szablonu urządzenia ze strony **Szablony urządzeń** .

2. Wybierz pozycję **Publikuj**.
  
    ![Zrzut ekranu szablonu czujnika środowiska z wyróżnioną pozycją Opublikuj](./media/tutorial-define-edge-device-type/edgetemplatepublish.png)

1. W oknie dialogowym **Publikowanie szablonu urządzenia** wybierz pozycję **Publikuj**.
  
    ![Zrzut ekranu przedstawiający okno dialogowe publikowanie szablonu urządzenia z wyróżnioną pozycją Opublikuj](./media/tutorial-define-edge-device-type/edgepublishtemplate.png)

Po opublikowaniu szablonu urządzenia jest on widoczny na stronie **urządzenia** i do operatora. W opublikowanym szablonie urządzenia nie można edytować modelu możliwości urządzenia bez tworzenia nowej wersji. Można jednak wykonywać aktualizacje właściwości, dostosowań i widoków w chmurze w opublikowanym szablonie urządzenia. Te aktualizacje nie powodują utworzenia nowej wersji. Po wprowadzeniu zmian wybierz pozycję **Publikuj** , aby wypchnąć te zmiany do operatora.
  
![Zrzut ekranu przedstawiający listę szablonów urządzeń opublikowanych szablonów](./media/tutorial-define-edge-device-type/publishedtemplate.png)

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

* Utwórz nową krawędź jako szablon urządzenia liścia.
* Generuj moduły na podstawie przekazanego manifestu wdrożenia.
* Dodaj telemetrię typu złożonego i właściwości.
* Utwórz właściwości chmury.
* Utwórz dostosowania.
* Zdefiniuj wizualizację dla danych telemetrycznych urządzenia.
* Opublikuj swój szablon urządzenia brzegowego.

Teraz, po utworzeniu szablonu urządzenia w aplikacji IoT Central platformy Azure, możesz to zrobić w następujący sposób:

> [!div class="nextstepaction"]
> [Połącz urządzenie](./tutorial-connect-pnp-device.md)
