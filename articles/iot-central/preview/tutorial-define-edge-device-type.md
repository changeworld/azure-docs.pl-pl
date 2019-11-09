---
title: Definiowanie nowego typu urządzenia Azure IoT Edge na platformie Azure IoT Central | Microsoft Docs
description: W tym samouczku pokazano, jak utworzyć za pomocą konstruktora nowe urządzenie Azure IoT Edge w aplikacji IoT Central platformy Azure. Zdefiniuj dane telemetryczne, stan, właściwości i polecenia dla danego typu.
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: c205b4dd4871ed53e32dce72f12cc2dcfb3baf41
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/09/2019
ms.locfileid: "73893053"
---
# <a name="tutorial-define-a-new-azure-iot-edge-device-type-in-your-azure-iot-central-application-preview-features"></a>Samouczek: Definiowanie nowego typu urządzenia Azure IoT Edge w aplikacji IoT Central platformy Azure (funkcje w wersji zapoznawczej)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

W tym samouczku pokazano, jak utworzyć konstruktora, jak używać szablonu urządzenia do definiowania nowego typu urządzenia Azure IoT Edge w aplikacji IoT Central platformy Azure. 

Aby zapoznać się z omówieniem Azure IoT Edge, [zapoznaj się z tym artykułem](overview-iot-central.md). 

Usługa Azure IoT Edge obejmuje trzy składniki:
* **Moduły IoT Edge** są kontenerami, w których działają usługi platformy Azure, usługi innych firm lub własny kod. Moduły są wdrażane na urządzeniach usługi IoT Edge i wykonywane lokalnie na tych urządzeniach.
* **Środowisko uruchomieniowe IoT Edge** działa na każdym urządzeniu IoT Edge i zarządza modułami wdrożonymi na poszczególnych urządzeniach.
* **Interfejs oparty na chmurze** umożliwia zdalne monitorowanie IoT Edge urządzeń i zarządzanie nimi. IoT Central będzie interfejsem chmury.

Urządzenie **Azure IoT Edge** może być urządzeniem bramy z urządzeniami podrzędnymi łączącymi się z urządzeniem Azure IoT Edge. Wzorce łączności z urządzeniami podrzędnymi zostaną omówione w tym samouczku.

**Szablon urządzenia** definiuje możliwości urządzenia & IoT Edge modułów. Możliwości obejmują dane telemetryczne, które są wysyłane przez moduł, właściwości modułu i polecenia, na które odpowiada moduł.

W tym samouczku utworzysz szablon urządzenia **czujnika środowiska** . Urządzenie czujnika środowiska:

* Wysyła dane telemetryczne, takie jak temperatura.
* Reaguje na właściwości zapisywalne w chmurze, takie jak interwał wysyłania danych telemetrycznych.
* Reaguje na polecenia, takie jak resetowanie temperatury.

Ponadto w tym samouczku utworzysz szablon urządzenia **bramy środowiska** . Urządzenie bramy środowiskowej:

* Wysyła dane telemetryczne, takie jak temperatura.
* Reaguje na właściwości zapisywalne w chmurze, takie jak interwał wysyłania danych telemetrycznych.
* Reaguje na polecenia, takie jak resetowanie temperatury.
* Zezwala na relacje z innymi modelami możliwości urządzeń


Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz nowy szablon urządzenia Azure IoT Edge.
> * Przekaż manifest wdrożenia.
> * Tworzenie możliwości, w tym danych telemetrycznych, właściwości i poleceń dla każdego modułu
> * Zdefiniuj wizualizację dla telemetrii modułu.
> * Dodawanie relacji do szablonów urządzeń podrzędnych
> * Opublikuj szablon urządzenia.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka potrzebna jest aplikacja usługi Azure IoT Central. Postępuj zgodnie z tym przewodnikiem Szybki Start, aby [utworzyć aplikację IoT Central platformy Azure](quick-deploy-iot-central.md).


## <a name="downstream-device-relationships-with-gateway--modules"></a>Relacje urządzeń podrzędnych z modułami & bramy

Urządzenia podrzędne mogą łączyć się z urządzeniem bramy Azure IoT Edge za pomocą modułu $edgeHub. To Azure IoT Edge urządzenie zostanie w nieprzezroczystej bramie w tym scenariuszu

![Strona aplikacji centralnej](./media/tutorial-define-edge-device-type/gateway-transparent.png)

Urządzenia podrzędne mogą łączyć się z urządzeniem bramy Azure IoT Edge za pomocą modułu niestandardowego. W scenariuszu poniżej urządzenia podrzędne są nawiązywane połączenia za pomocą niestandardowego modułu Modbus, a urządzenia podrzędne mogą łączyć się z urządzeniem bramy Azure IoT Edge za pomocą modułu $edgeHub.

![Strona aplikacji centralnej](./media/tutorial-define-edge-device-type/gateway-module.png)

Urządzenia podrzędne mogą łączyć się z urządzeniem bramy Azure IoT Edge za pomocą modułu niestandardowego. W poniższym scenariuszu urządzenia podrzędne nawiązują połączenie za pomocą modułu niestandardowego Modbus. 

![Strona aplikacji centralnej](./media/tutorial-define-edge-device-type/gateway-module-transparent.png)

Urządzenia podrzędne mogą łączyć się z urządzeniem bramy Azure IoT Edge przy użyciu wielu modułów niestandardowych. W scenariuszu poniżej urządzeń podrzędnych są nawiązywane połączenia za pomocą modułu Modbus Custom, moduł niestandardowy i urządzenia podrzędne z siecią mogą łączyć się z urządzeniem bramy Azure IoT Edge za pomocą modułu $edgeHub. 

![Strona aplikacji centralnej](./media/tutorial-define-edge-device-type/gateway-module2-transparent.png)


## <a name="create-a-template"></a>Tworzenie szablonu

Jako Konstruktor można tworzyć i edytować szablony urządzeń Azure IoT Edge w aplikacji. Po opublikowaniu szablonu urządzenia można nawiązać połączenie z rzeczywistymi urządzeniami, które implementują szablon urządzenia.

### <a name="select-device-template-type"></a>Wybierz typ szablonu urządzenia 

Aby dodać nowy szablon urządzenia do aplikacji, przejdź do strony **Szablony urządzeń** . Aby to zrobić, wybierz kartę **Szablony urządzeń** w okienku po lewej stronie.

![Strona aplikacji centralnej](./media/tutorial-define-edge-device-type/edgedevicetemplate.png)

Kliknij pozycję **+ Nowy** , aby rozpocząć tworzenie nowego szablonu urządzenia.

![Szablony urządzeń — nowe](./media/tutorial-define-edge-device-type/edgedevicetemplatenew.png)

Zobaczysz na stronie Wybór typu szablonu urządzenia. Wybierz kafelek **Azure IoT Edge** i kliknij przycisk **Dalej: Dostosuj** przycisk u dołu

![Wybór szablonów urządzeń — Azure IoT Edge](./media/tutorial-define-edge-device-type/selectiotedge.png)

### <a name="customize-device-template"></a>Dostosuj szablon urządzenia

Azure IoT Edge pozwala wdrożyć logikę biznesową i zarządzać nią w formie modułów. **Moduły Azure IoT Edge** są najmniejszą jednostką obliczeniową zarządzaną przez IoT Edge i mogą zawierać usługi platformy Azure (takie jak Azure Stream Analytics) lub własny kod specyficzny dla rozwiązania. Aby zrozumieć, w jaki sposób moduły są opracowywane, wdrażane i utrzymywane IoT Edge odczytywanie [modułów](../../iot-edge/iot-edge-modules.md).

Na wysokim poziomie manifest wdrożenia jest listą bliźniaczych reprezentacji modułów, które są skonfigurowane przy użyciu odpowiednich właściwości. Manifest wdrożenia informuje urządzenie IoT Edge (lub grupę urządzeń), które moduły zainstalować i jak je skonfigurować. Manifesty wdrożenia obejmują odpowiednie właściwości dla każdego sznurka modułu. IoT Edge urządzeń raportuje z powrotem raportowane właściwości dla każdego modułu.

Użyj Visual Studio Code, aby utworzyć manifest wdrożenia. Postępuj zgodnie z dokumentacją dotyczącą tworzenia [manifestu wdrożenia](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).

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

**Przekazywanie manifestu wdrażania Azure IoT Edge**

Kliknij przycisk **Przeglądaj** 

![Szablon urządzenia — Azure IoT Edge](./media/tutorial-define-edge-device-type/edgedevicetemplateuploadmanifest.png)

Jeśli planujesz utworzenie szablonu urządzenia bramy Azure IoT Edge upewnij się, że wybrano pole wyboru **urządzenie bramy z urządzeniami podrzędnymi**

![Szablon urządzenia — Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-upload.png)

Zostanie wyświetlone okno dialogowe wyboru pliku. Wybierz plik manifestu wdrożenia, a następnie kliknij przycisk **Otwórz** .

Plik manifestu wdrożenia zostanie sprawdzony pod kątem schematu. Po pomyślnej weryfikacji kliknij przycisk **Przegląd**

![Szablon urządzenia — Azure IoT Edge](./media/tutorial-define-edge-device-type/deploymentmanifestvalidate.png)

Poniżej znajduje się przepływ cyklu życia manifestu wdrożenia w IoT Central.

![Szablon urządzenia — Azure IoT Edge](./media/tutorial-define-edge-device-type/dmflow.png)

Zostanie wyświetlona strona przegląd zawierająca szczegóły manifestu wdrożenia. Lista modułów z manifestu wdrożenia zostanie wyświetlona na stronie Przegląd. W tym samouczku zobaczysz moduł SimulatedTemperatureSensor. Kliknij przycisk **Utwórz** .

![Szablon urządzenia — Azure IoT Edge](./media/tutorial-define-edge-device-type/edgedevicetemplatereview.png)

W przypadku wybrania urządzenia bramy zostanie wyświetlona strona przeglądu

![Szablon urządzenia — Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-review.png)


Utworzenie nowego pokrętła szablonu urządzenia spowoduje wyświetlenie tworzonego szablonu urządzenia w IoT Central.

Szablon urządzenia jest tworzony z modelami możliwości modułu. W tym samouczku zostanie wyświetlony model możliwości modułu SimulatedTemperatureSensor. 

Zmień tytuł szablonu urządzenia na szablon urządzenia czujnika środowiska.

![Szablon urządzenia — Azure IoT Edge](./media/tutorial-define-edge-device-type/edgedevicetemplatelanding.png)

Modelowanie urządzeń Azure IoT Edge Plug n Play jest wykonywane w następujący sposób
* Każdy szablon urządzenia Azure IoT Edge będzie miał **model możliwości urządzenia**
* Dla każdego modułu niestandardowego wymienionego w manifeście wdrożenia zostanie wygenerowany **model możliwości modułu**
* Zostanie ustanowiona **relacja** między każdym modelem możliwości modułu a modelem możliwości urządzenia
* Model możliwości modułu implementuje **interfejsy modułów**
* Każdy interfejs modułu zawiera
   - Telemetria
   - Właściwości
   - Polecenia

![Szablon urządzenia — Azure IoT Edge](./media/tutorial-define-edge-device-type/edgemodelling.png)

**Dodawanie możliwości do modelu możliwości modułu**

Oto przykładowe dane wyjściowe z modułu SimulatedTemperatureSensor
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

Dodaj możliwości do modułu SimulatedTemperatureSensor, który będzie odzwierciedlał powyższy kod JSON. 

* Kliknij pozycję **Zarządzaj** interfejsem modelu możliwości modułu SimulatedTemperatureSensor. Kliknij pozycję **Dodaj funkcję**. 

    ![Szablon urządzenia — Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplateaddcapability.png)
  
* Dodaj maszynę jako typ obiektu, ponieważ jest to typ złożony
  
    ![Szablon urządzenia — Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplatemachineobject.png)

    Kliknij przycisk **Definiuj**. W oknie podręcznym Zmień nazwę obiektu na Machine i Utwórz właściwości temperatura, ciśnienie i kliknij **Zastosuj**
  
    ![Szablon urządzenia — Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplatemachineattributes.png)
  
* Dodaj otoczenie jako typ obiektu, ponieważ jest to typ złożony

    Kliknij przycisk **Definiuj**. W oknie podręcznym Zmień nazwę obiektu na otoczenie i Utwórz właściwości temperatura, wilgotność i kliknij **Zastosuj**
  
    ![Szablon urządzenia — Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplateambientattributes.png)

  
* Dodaj timeCreated jako typ DateTime i kliknij przycisk **Zapisz** .
  
    ![Szablon urządzenia — Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplateallattributes.png)


### <a name="add-relationships"></a>Dodaj relacje

W przypadku wybrania urządzenia z systemem Azure IoT Edge jako urządzenie bramy można dodać relacje podrzędne do modeli możliwości urządzeń, które będą się łączyć z urządzeniem bramy.
  
  ![Szablon urządzenia — Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-add-relationship.png)

Relację można dodać na urządzeniu lub w module.
  
  ![Szablon urządzenia — Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-relationship-types.png)


Możesz wybrać model możliwości urządzenia podrzędnego lub wybrać pozycję gwiazdki. 
  
  ![Szablon urządzenia — Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-downstream-rel.png)

  Na potrzeby tego samouczka wybierzemy pozycję gwiazdki, co oznacza, że wszystkie relacje podrzędne będą dozwolone. Kliknij pozycję **Zapisz**

  ![Szablon urządzenia — Azure IoT Edge](./media/tutorial-define-edge-device-type/gateway-add-relationship-asterix.png)


### <a name="add-cloud-properties"></a>Dodawanie właściwości chmury

Szablon urządzenia może zawierać właściwości chmury. Właściwości chmury istnieją tylko w aplikacji IoT Central i nigdy nie są wysyłane do ani odbierane z urządzenia.

1. Wybierz pozycję **właściwości chmury** , a następnie **+ Dodaj właściwość chmury**. Skorzystaj z informacji podanych w poniższej tabeli, aby dodać właściwość chmury do szablonu urządzenia.

    | Nazwa wyświetlana      | Typ semantyczny | Schemat |
    | ----------------- | ------------- | ------ |
    | Data ostatniego serwisowania | Brak          | Date   |
    | Nazwa klienta     | Brak          | Ciąg |

2. Wybierz pozycję **Zapisz** , aby zapisać zmiany:

  
    ![Właściwości chmury — Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplatecloudproperties.png)

### <a name="add-customizations"></a>Dodawanie dostosowań

Użyj dostosowań, gdy musisz zmodyfikować interfejs lub dodać funkcje specyficzne dla IoT Central do możliwości, która nie wymaga użycia modelu możliwości urządzenia. Można dostosować pola, gdy model możliwości jest w stanie roboczym lub opublikowanym. Można dostosować tylko pola, które nie łamią zgodności interfejsów. Można na przykład:

- Dostosuj nazwę wyświetlaną i jednostki możliwości.
- Dodaj domyślny kolor, który ma być używany, gdy wartość pojawia się na wykresie.
- Określ początkową, minimalną i maksymalną wartość właściwości.

Nie można dostosować nazwy możliwości ani typu możliwości. Kliknij pozycję **Zapisz**
  
![Dostosowania — Azure IoT Edge](./media/tutorial-define-edge-device-type/edgetemplatecustomize.png)


### <a name="create-views"></a>Tworzenie widoków

Jako Konstruktor można dostosować aplikację tak, aby wyświetlała odpowiednie informacje o urządzeniu czujnika środowiska z operatorem. Twoje dostosowania umożliwiają operatorowi zarządzanie urządzeniami czujnika środowiskowego podłączonymi do aplikacji. Można utworzyć dwa typy widoków dla operatora, który ma być używany do współpracy z urządzeniami:

* Formularze do wyświetlania i edytowania właściwości urządzenia i chmury.
* Pulpity nawigacyjne do wizualizacji urządzeń.

### <a name="configure-a-view-to-visualize-devices"></a>Konfigurowanie widoku do wizualizacji urządzeń

Pulpit nawigacyjny urządzenia umożliwia operatorowi wizualizowanie urządzenia przy użyciu wykresów i metryk. Jako konstruktor możesz zdefiniować informacje wyświetlane na pulpicie nawigacyjnym urządzenia. Można zdefiniować wiele pulpitów nawigacyjnych dla urządzeń. Aby utworzyć pulpit nawigacyjny w celu wizualizacji danych telemetrycznych czujnika środowiska, wybierz pozycję **widoki** , a następnie **Wizualizuj urządzenie**:

  
![Widoki — Azure IoT Edge](./media/tutorial-define-edge-device-type/visualizingthedevice.png)


Dane telemetryczne otoczenia i Telemetria maszyn są obiektami złożonymi, aby utworzyć wykresy wykonaj następujące czynności

Przeciągnij telemetrię i wybierz pozycję wykres liniowy. 
  
![Widoki — Azure IoT Edge](./media/tutorial-define-edge-device-type/sensorambientchart.png)

Kliknij pozycję Konfiguruj ikonę i wybierz pozycję temperatura i wilgotność, aby wizualizować dane, a następnie kliknij przycisk **Aktualizuj konfigurację** . 
  
![Widoki — Azure IoT Edge](./media/tutorial-define-edge-device-type/sensorambienttelemetrychart.png)

Wybierz pozycję **Zapisz** , aby zapisać swój widok:

Można dodać więcej kafelków, które pokazują inne właściwości lub wartości telemetrii. Możesz również dodać tekst statyczny, linki i obrazy. Aby przenieść lub zmienić rozmiar kafelka na pulpicie nawigacyjnym, przesuń wskaźnik myszy nad kafelek i przeciągnij kafelek do nowej lokalizacji lub zmień jego rozmiar.
  
![Widoki — Azure IoT Edge](./media/tutorial-define-edge-device-type/viewsdashboard.png)

### <a name="add-a-device-form"></a>Dodaj formularz urządzenia

Formularz urządzenia umożliwia operatorowi Edytowanie właściwości urządzenia z możliwością zapisu i właściwości chmury. Jako Konstruktor można definiować wiele formularzy i wybierać, które urządzenia i właściwości chmury mają być wyświetlane w każdym formularzu. Możesz również wyświetlić właściwości urządzenia tylko do odczytu w formularzu.

Aby utworzyć formularz do wyświetlania i edytowania właściwości czujnika środowiska:

Przejdź do **widoku** w szablonie **czujnika środowiska** . Wybierz kafelek **Edytowanie urządzenia i danych w chmurze** , aby dodać nowy widok.
  
![Widoki — Azure IoT Edge](./media/tutorial-define-edge-device-type/editingdeviceandclouddata.png)

Wprowadź nazwę formularza **Właściwości czujnika środowiska**.

Przeciągnij właściwości **Nazwa klienta** i **Data ostatniej usługi** do istniejącej sekcji w formularzu.
  
![Widoki — Azure IoT Edge](./media/tutorial-define-edge-device-type/views-properties.png)

Wybierz pozycję **Zapisz** , aby zapisać swój widok.

### <a name="generate-default-views"></a>Generuj widoki domyślne

Generowanie funkcji widoków domyślnych nie jest obsługiwane w przypadku szablonów Azure IoT Edge 

## <a name="publish-device-template"></a>Publikowanie szablonu urządzenia

Aby można było utworzyć symulowany czujnik środowiska lub połączyć prawdziwy czujnik środowiska, należy opublikować szablon urządzenia.

Aby opublikować szablon urządzenia:

1. Przejdź do szablonu urządzenia ze strony **Szablony urządzeń** .

2. Wybierz pozycję **Publikuj**.
  
    ![Widoki — publikowanie](./media/tutorial-define-edge-device-type/edgetemplatepublish.png)

1. W oknie dialogowym **Publikowanie szablonu urządzenia** wybierz pozycję **Publikuj**:
  
    ![Widoki — publikowanie](./media/tutorial-define-edge-device-type/edgepublishtemplate.png)

Po opublikowaniu szablonu urządzenia jest on widoczny na stronie **urządzenia** i do operatora. W opublikowanym szablonie urządzenia nie można edytować modelu możliwości urządzenia bez tworzenia nowej wersji. Można jednak wykonywać aktualizacje właściwości, dostosowań i widoków w chmurze w opublikowanym szablonie urządzenia bez obsługi wersji. Po wprowadzeniu zmian wybierz pozycję **Publikuj** , aby wypchnąć te zmiany do operatora.
  
![Widoki — publikowanie](./media/tutorial-define-edge-device-type/publishedtemplate.png)

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

* Tworzenie nowej krawędzi jako szablonu urządzenia typu liść
* Generuj moduły na podstawie przekazanego manifestu wdrożenia
* Dodaj telemetrię typu złożonego i właściwości
* Utwórz właściwości chmury.
* Utwórz dostosowania.
* Zdefiniuj wizualizację dla danych telemetrycznych urządzenia.
* Opublikuj swój szablon urządzenia brzegowego.

Teraz, po utworzeniu szablonu urządzenia w aplikacji IoT Central platformy Azure, Oto sugerowany następny krok:

> [!div class="nextstepaction"]
> [Połącz urządzenie](./tutorial-connect-pnp-device.md)
