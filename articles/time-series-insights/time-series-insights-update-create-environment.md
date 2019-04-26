---
title: 'Samouczek: konfigurowanie środowiska wersji zapoznawczej usługi Azure Time Series Insights | Microsoft Docs'
description: Dowiedz się, jak skonfigurować środowisko wersji zapoznawczej usługi Azure Time Series Insights.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 12/12/2018
ms.custom: seodec18
ms.openlocfilehash: 75ab3baf8638a387defd0e367b4a61c3746794f3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60544476"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Samouczek: Konfigurowanie środowiska wersji zapoznawczej usługi Azure Time Series Insights

Ten samouczek zawiera instrukcje tworzenia środowiska wersji zapoznawczej usługi Azure Time Series Insights dostępnej w modelu płatności zgodnie z rzeczywistym użyciem (PAYG). Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

* Tworzenie środowiska wersji zapoznawczej usługi Azure Time Series Insights.
* Łączenie środowiska wersji zapoznawczej usługi Azure Time Series Insights z centrum zdarzeń w usłudze Azure Event Hubs.
* Uruchamianie przykładowego akceleratora rozwiązań umożliwiającego strumieniowe przesyłanie danych do środowiska wersji zapoznawczej usługi Azure Time Series Insights.
* Wykonywanie podstawowej analizy danych.
* Definiowanie hierarchii oraz typu modelu szeregów czasowych i kojarzenie ich z wystąpieniami.

# <a name="create-a-device-simulation"></a>Tworzenie symulacji urządzeń

W tej sekcji utworzysz trzy symulowane urządzenia, które będą wysyłać dane do centrum IoT Hub.

1. Przejdź do [strony akceleratorów rozwiązań Azure IoT](https://www.azureiotsolutions.com/Accelerators). Na stronie będzie widocznych kilka gotowych przykładów. Zaloguj się przy użyciu konta platformy Azure. Następnie wybierz pozycję **Symulacja urządzenia**.

   ![Strona akceleratorów rozwiązań usługi Azure IoT][1]

   Wybierz pozycję **Wypróbuj teraz**.

1. Wprowadź wymagane parametry na stronie **Utwórz rozwiązanie symulacji urządzeń**:

   | Parametr | Opis |
   | --- | --- |
   | **Nazwa rozwiązania** |    Wprowadź unikatową wartość na potrzeby utworzenia nowej grupy zasobów. Znajdujące się na liście zasoby platformy Azure są tworzone i przypisywane do grupy zasobów. |
   | **Subskrypcja** | Określ subskrypcję użytą do utworzenia środowiska usługi Time Series Insights. |
   | **Region** |   Określ region użyty do utworzenia środowiska usługi Time Series Insights. |
   | **Wdróż opcjonalne zasoby platformy Azure**    | Pozostaw zaznaczone pole wyboru IoT Hub, ponieważ symulowane urządzenia będą używały tego centrum do łączenia się i strumieniowania danych. |

   Następnie wybierz pozycję **Utwórz rozwiązanie**. Poczekaj 10–15 minut na wdrożenie rozwiązania.

   ![Strona tworzenia rozwiązania do symulacji urządzeń][2]

1. Na pulpicie nawigacyjnym akceleratora rozwiązań kliknij przycisk **Uruchom**:

   ![Uruchamianie rozwiązania symulacji urządzeń][3]

1. Nastąpi przekierowanie do strony **Symulacja urządzeń Microsoft Azure IoT**. Wybierz pozycję **+ Nowa symulacja** w prawym górnym rogu strony.

   ![Strona symulacji w usłudze Azure IoT][4]

1.  Wypełnij wymagane parametry następującymi wartościami:

    ![Parametry do wypełnienia][5]

    |||
    | --- | --- |
    | **Nazwa** | Wprowadź unikatową nazwę symulatora. |
    | **Opis** | Wprowadź definicję. |
    | **Czas trwania symulacji** | Wybierz opcję **Uruchamianie bez ograniczeń**. |
    | **Model urządzenia** | **Nazwa**: Wpisz **Chiller** (chłodnia). </br>**Ilość**: Wpisz **3**. |
    | **Docelowa usługa IoT Hub** | Wybierz pozycję **Użyj dostarczonej wstępnie usługi IoT Hub**. |

    Następnie wybierz pozycję **Rozpocznij symulację**.

1. Na pulpicie nawigacyjnym symulacji urządzeń sprawdź opcje **Aktywne urządzenia** i **Komunikaty na sekundę**.

    ![Pulpit nawigacyjny symulacji w usłudze Azure IoT][6]

## <a name="list-device-simulation-properties"></a>Wyświetlanie właściwości symulacji urządzeń

Do utworzenia środowiska usługi Azure Time Series Insights potrzebne są nazwy centrum IoT Hub, subskrypcji i grupy zasobów.

1. Przejdź do pulpitu nawigacyjnego akceleratora rozwiązań i zaloguj się przy użyciu konta tej samej subskrypcji platformy Azure. Znajdź symulację urządzeń utworzoną w poprzednich krokach.

1. Wybierz symulator urządzeń, a następnie wybierz przycisk **Uruchom**. Wybierz link **Portal zarządzania Microsoft Azure** z prawej strony.

    ![Lista symulatorów][7]

1. Zanotuj nazwy centrum IoT Hub, subskrypcji i grupy zasobów.

    ![Azure Portal][8]

## <a name="create-a-time-series-insights-preview-payg-environment"></a>Tworzenie środowiska wersji zapoznawczej usługi Time Series Insights dostępnej w modelu PAYG

W tej sekcji opisano, jak utworzyć środowisko wersji zapoznawczej usługi Azure Time Series Insights przy użyciu witryny [Azure Portal](https://portal.azure.com/).

1. Zaloguj się do witryny Azure Portal przy użyciu konta subskrypcji.

1. Wybierz pozycję **Utwórz zasób**.

1. Wybierz kategorię **Internet rzeczy**, a następnie wybierz pozycję **Time Series Insights**.

   ![Wybierz pozycję Internet rzeczy, a następnie Time Series Insights][9]

1. Wypełnij pola na stronie w następujący sposób:

   | | |
   | --- | ---|
   | **Nazwa środowiska** | Wybierz unikatową nazwę środowiska wersji zapoznawczej usługi Azure Time Series Insights. |
   | **Subskrypcja** | Wprowadź subskrypcję, w której chcesz utworzyć środowisko wersji zapoznawczej usługi Azure Time Series Insights. Najlepiej jest użyć tej samej subskrypcji, w której są przechowywane pozostałe zasoby IoT utworzone przez symulator urządzeń. |
   | **Grupa zasobów** | Grupa zasobów jest kontenerem zasobów platformy Azure. Dla zasobu środowiska wersji zapoznawczej usługi Azure Time Series Insights wybierz istniejącą grupę zasobów lub utwórz nową. Najlepiej jest użyć tej samej grupy zasobów, w której są przechowywane pozostałe zasoby IoT utworzone przez symulator urządzeń. |
   | **Lokalizacja** | Wybierz region centrum danych dla środowiska wersji zapoznawczej usługi Azure Time Series Insights. Aby uniknąć kosztów i opóźnień spowodowanych dodatkową przepustowością, najlepiej przechowywać środowisko wersji zapoznawczej usługi Azure Time Series Insights w tym samym regionie co inne zasoby IoT. |
   | **Warstwa** |  Wybierz opcję **PAYG**, czyli model płatności zgodnie z rzeczywistym użyciem. Jest to jednostka SKU produktu Azure Time Series Insights w wersji zapoznawczej. |
   | **Identyfikator właściwości** | Wprowadź wartość umożliwiającą jednoznaczną identyfikację szeregu czasowego. Pamiętaj, że później nie można zmienić wartości tego pola. Na potrzeby tego samouczka użyj wartości **iothub-connection-device-id**. Aby dowiedzieć się więcej o identyfikatorze szeregu czasowego, zobacz [How to choose a Time Series ID (Jak wybrać identyfikator szeregu czasowego)](./time-series-insights-update-how-to-id.md). |
   | **Nazwa konta magazynu** | Wprowadź globalnie unikatową nazwę konta magazynu, które ma zostać utworzone. |

   Następnie wybierz opcję **Dalej: Źródło zdarzeń**.

   ![Strona tworzenia środowiska usługi Time Series Insights][10]

1. Na stronie źródła zdarzeń wypełnij pola w następujący sposób:

   | | |
   | --- | --- |
   | **Utworzyć źródło zdarzeń?** | Podaj wartość **Tak**.|
   | **Nazwa** | Wprowadź unikatową wartość używaną jako nazwa źródła zdarzeń.|
   | **Typ źródła** | Podaj wartość **IoT Hub**. |
   | **Wybrać centrum?** | Podaj wartość **Wybierz istniejące**. |
   | **Subskrypcja** | Wprowadź subskrypcję użytą dla symulatora urządzenia. |
   | **Nazwa centrum IoT** | Wprowadź nazwę centrum IoT Hub utworzonego dla symulatora urządzenia. |
   | **Zasady dostępu do centrum IoT Hub** | Wprowadź wartość **iothubowner**. |
   | **Grupa konsumentów centrum IoT** | Do korzystania z wersji zapoznawczej usługi Azure Time Series Insights potrzebna jest unikatowa grupa użytkowników. Wybierz pozycję **Nowa**, wpisz unikatową nazwę, a następnie wybierz polecenie **Dodaj**. |
   | **Właściwość znacznika czasu** | To pole umożliwia identyfikowanie właściwości sygnatury czasowej w przychodzących danych telemetrycznych. W tym samouczku nie należy wypełniać tego pola. Ten symulator używa sygnatury czasowej pochodzącej z usługi IoT Hub, domyślnej wartości dla usługi Time Series Insights.|

   Następnie wybierz pozycję **Przegląd + utwórz**.

   ![Strona konfigurowania źródła zdarzeń][13]

1. Sprawdź zawartość wszystkich pól na stronie przeglądu i wybierz przycisk **Utwórz**.

   ![Strona Przeglądanie + tworzenie z przyciskiem Utwórz][14]

1. Będą wyświetlane informacje o stanie wdrożenia.

   ![Powiadomienie o zakończeniu wdrażania][15]

1. Jeśli jesteś właścicielem dzierżawy, uzyskasz dostęp do środowiska wersji zapoznawczej usługi Azure Time Series Insights. Aby to sprawdzić:

   a. Wyszukaj grupę zasobów i wybierz swoje środowisko wersji zapoznawczej usługi Azure Time Series Insights:

      ![Wybrane środowisko][16]

   b. Na stronie wersji zapoznawczej usługi Azure Time Series Insights przejdź do obszaru **Zasady dostępu do danych**.

     ![Zasady dostępu do danych][17]

   c. Sprawdź, czy widać Twoje poświadczenia.

     ![Widoczne poświadczenia][18]

   Jeśli poświadczenia nie są widoczne, musisz przyznać sobie uprawnienie dostępu do środowiska. Aby dowiedzieć się więcej o ustawianiu uprawnień, zobacz [Grant data access (Przyznawanie dostępu do danych)](./time-series-insights-data-access.md).

## <a name="analyze-data-in-your-environment"></a>Analizowanie danych w środowisku

W tej sekcji przeprowadzimy podstawową analizę danych szeregów czasowych przy użyciu [eksploratora wersji zapoznawczej usługi Azure Time Series Insights](./time-series-insights-update-explorer.md).

1. Przejdź do eksploratora wersji zapoznawczej usługi Azure Time Series Insights, wybierając adres URL na stronie zasobu w witrynie [Azure Portal](https://portal.azure.com/).

   ![Adres URL eksploratora wersji zapoznawczej usługi Time Series Insights][19]

1. W eksploratorze wybierz węzły **Wystąpienia bez elementów nadrzędnych**, aby wyświetlić wszystkie wystąpienia usługi Azure Time Series Insights w wersji zapoznawczej w środowisku.

   ![Lista wystąpień bez elementów nadrzędnych][20]

1. W wyświetlonym szeregu czasowym wybierz pierwsze wystąpienie. Następnie wybierz pozycję **Show Avg pressure** (Wyświetl ciśnienie średnie).

   ![Wybrane wystąpienie z poleceniem menu umożliwiającym wyświetlenie ciśnienia średniego][21]

   Po prawej stronie powinien pojawić się wykres szeregu czasowego:

   ![Wykres szeregu czasowego][22]

1. Powtórz krok 3 dla dwóch pozostałych szeregów czasowych. Następnie możesz wyświetlić wszystkie szeregi czasowe, jak na poniższym wykresie:

   ![Wykres z wszystkimi szeregami czasowymi][23]

1. Zmień zakres czasu, aby wyświetlić trendy szeregów czasowych w ciągu ostatniej godziny. 

   a. Zaznacz pole opcji **Od**:

      ![Pole opcji Od][24]

   b. Zmień godzinę w polu, aby wyświetlić zdarzenia z ostatniej godziny:

      ![Dostosowywanie czasu][25]

1. Dzięki temu możesz porównać ciśnienie we wszystkich trzech urządzeniach w ciągu ostatniej godziny:

   ![Porównywanie danych z trzech urządzeń][26]

## <a name="define-and-apply-a-model"></a>Definiowanie i stosowanie modelu

W tej sekcji zastosujesz model, aby ustrukturyzować dane. Do utworzenia modelu potrzebne są definicje typów, hierarchii i wystąpień. Aby dowiedzieć się więcej na temat modelowania danych, zobacz [Time Series Model (Model szeregów czasowych)](./time-series-insights-update-tsm.md).

1. W eksploratorze wybierz kartę **Model**:

   ![Karta Model w eksploratorze][27]

1. Wybierz pozycję **+ Dodaj**, aby dodać typ. Po prawej stronie zostanie otwarty edytor typów.

   ![Przycisk Dodaj umożliwiający dodanie typu][28]

1. Zdefiniuj trzy zmienne dla typu: ciśnienie, temperaturę i wilgotność. Wprowadź następujące informacje:

   | | |
   | --- | ---|
   | **Nazwa** | Wpisz **Chiller** (chłodnia). |
   | **Opis** | Wpisz **This is a type definition of Chiller** (To jest definicja typu dla chłodni). |

   * Teraz zdefiniuj parametr ciśnienia z trzema zmiennymi:

      | | |
      | --- | ---|
      | **Nazwa** | Wpisz **Avg Pressure** (Ciśnienie średnie). |
      | **Wartość** | Wybierz opcję **pressure (Double)**. Pamiętaj, że gdy wersja zapoznawcza usługi Azure Time Series Insights zacznie odbierać zdarzenia, wypełnienie tego pola może potrwać kilka minut. |
      | **Operacja agregacji** | Wybierz opcję **AVG** (średnia). |

      ![Opcje umożliwiające zdefiniowanie parametru ciśnienia][29]

      Kliknij pozycję **+ Dodaj zmienną**, aby dodać kolejną zmienną.

   * Definiowanie temperatury:

      | | |
      | --- | ---|
      | **Nazwa** | Wpisz **Avg Temperature** (Temperatura średnia). |
      | **Wartość** | Wybierz opcję **temperature (Double)**. Pamiętaj, że gdy wersja zapoznawcza usługi Azure Time Series Insights zacznie odbierać zdarzenia, wypełnienie tego pola może potrwać kilka minut. |
      | **Operacja agregacji** | Wybierz opcję **AVG** (średnia).|

      ![Opcje umożliwiające zdefiniowanie parametru temperatury][30]

   * Zdefiniuj wilgotność:

      | | |
      | --- | ---|
      | **Nazwa** | Wpisz **Max Humidity** (Maksymalna wilgotność). |
      | **Wartość** | Wybierz opcję **humidity (Double)**. Pamiętaj, że gdy wersja zapoznawcza usługi Azure Time Series Insights zacznie odbierać zdarzenia, wypełnienie tego pola może potrwać kilka minut. |
      | **Operacja agregacji** | Wybierz **MAX**.|

      ![Opcje umożliwiające zdefiniowanie parametru temperatury][31]

   Następnie wybierz przycisk **Create** (Utwórz).

1. Zobaczysz dodany typ:

   ![Informacje na temat dodanego typu][32]

1. Następnym krokiem jest dodanie hierarchii. W sekcji **Hierarchie** wybierz pozycję **+ Dodaj**:

   ![Karta Hierarchie z przyciskiem Dodaj][33]

1. Zdefiniuj hierarchię. Wypełnij pola w następujący sposób:

   | | |
   | --- | ---|
   | **Nazwa** | Wpisz **Location Hierarchy** (Hierarchia lokalizacji). |
   | **Level 1** | Wpisz **Country** (Kraj). |
   | **Level 2** | Wpisz **City** (Miasto). |
   | **Level 3** | Wpisz **Building** (Budynek). |

   Następnie wybierz przycisk **Create** (Utwórz).

   ![Pola hierarchii i przycisk Utwórz][34]

1. Zobaczysz utworzoną hierarchię:

   ![Informacje o hierarchii][35]

1. Wybierz pozycję **Wystąpienia** po lewej stronie. Po wyświetleniu wystąpień wybierz pierwsze wystąpienie i wybierz pozycję **Edytuj**:

   ![Wybieranie przycisku Edytuj dla wystąpienia][36]

1. Po prawej stronie pojawi się edytor tekstu. Dodaj następujące informacje:

   | | |
   | --- | --- |
   | **Typ** | Wybierz pozycję **Chiller** (Chłodnia). |
   | **Opis** | Wpisz **Instance for Chiller-01.1** (Wystąpienie dla urządzenia Chiller-01.1). |
   | **Hierarchies** | Włącz hierarchię **Location Hierarchy** (Hierarchia lokalizacji). |
   | **Country** | Wpisz **USA**. |
   | **City** | Wpisz **Seattle**. |
   | **Building** | Wpisz **Space Needle**. |

    Następnie wybierz pozycję **Zapisz**.

   ![Pola wystąpienia i przycisk Zapisz][37]

1. Powtórz poprzedni krok dla pozostałych czujników. Użyj następujących pól:

   * Dla chłodni Chiller 01.2:

     | | |
     | --- | --- |
     | **Typ** | Wybierz pozycję **Chiller** (Chłodnia). |
     | **Opis** | Wpisz **Instance for Chiller-01.2** (Wystąpienie dla urządzenia Chiller-01.2). |
     | **Hierarchies** | Włącz hierarchię **Location Hierarchy** (Hierarchia lokalizacji). |
     | **Country** | Wpisz **USA**. |
     | **City** | Wpisz **Seattle**. |
     | **Building** | Wpisz **Pacific Science Center**. |

   * Dla chłodni Chiller 01.3:

     | | |
     | --- | --- |
     | **Typ** | Wybierz pozycję **Chiller** (Chłodnia). |
     | **Opis** | Wpisz **Instance for Chiller-01.1** (Wystąpienie dla urządzenia Chiller-01.1). |
     | **Hierarchies** | Włącz hierarchię **Location Hierarchy** (Hierarchia lokalizacji). |
     | **Country** | Wpisz **USA**. |
     | **City** | Wpisz **New York**. |
     | **Building** | Wpisz **Empire State Building**. |

1. Przejdź do karty **Analiza**, a następnie odśwież stronę. Rozwiń wszystkie poziomy hierarchii, aby znaleźć szereg czasowy.

   ![Wyświetlanie karty Analiza][38]

1. Aby zobaczyć szereg czasowy z ostatniej godziny, zmień opcję w polu **Szybkie czasy** na **Ostatnia godzina**:

   ![Pole Szybkie czasy z wybraną opcją Ostatnia godzina][39]

1. Wybierz szereg czasowy w obszarze **Pacific Science Center** i wybierz pozycję **Show Max Humidity** (Wyświetl wilgotność maksymalną).

   ![Wybrany szereg czasowy z pozycją menu umożliwiającą wyświetlanie wilgotności maksymalnej][40]

1. Zostanie wyświetlony szereg czasowy **Max Humidity** o wielkości interwału równej 1 minucie. Aby odfiltrować zakres, wybierz region. Następnie kliknij prawym przyciskiem myszy i wybierz opcję **Powiększenie**, aby przeanalizować zdarzenia w przedziale czasowym:

   ![Wybrany zakres i polecenie Powiększenie w menu skrótów][41]

1. Możesz również wybrać region, a następnie kliknąć prawym przyciskiem myszy, aby wyświetlić szczegóły zdarzenia:

   ![Szczegółowa lista zdarzeń][44]

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:  

* Tworzenie i używanie akceleratora symulacji urządzenia.
* Tworzenie środowiska wersji zapoznawczej usługi Azure Time Series Insights dostępnej w modelu PAYG.
* Łączenie środowiska wersji zapoznawczej usługi Azure Time Series Insights z centrum zdarzeń.
* Uruchamianie przykładowego akceleratora rozwiązań umożliwiającego strumieniowe przesyłanie danych do środowiska wersji zapoznawczej usługi Azure Time Series Insights.
* Wykonywanie podstawowej analizy danych.
* Definiowanie hierarchii oraz typu modelu szeregów czasowych i kojarzenie ich z wystąpieniami.

Wiesz już, jak utworzyć własne środowisko wersji zapoznawczej usługi Azure Time Series Insights. Teraz możesz dowiedzieć się więcej na temat kluczowych pojęć związanych z usługą Azure Time Series Insights.

Zapoznaj się z konfiguracją magazynu usługi Azure Time Series Insights:

> [!div class="nextstepaction"]
> [Magazyn i ruch przychodzący usługi Azure Time Series Insights w wersji zapoznawczej](./time-series-insights-update-storage-ingress.md)

Dowiedz się więcej o modelach szeregów czasowych:

> [!div class="nextstepaction"]
> [Modelowanie danych w usłudze Azure Time Series Insights w wersji zapoznawczej](./time-series-insights-update-tsm.md)

<!-- Images -->
[1]: media/v2-update-provision/device-one-accelerator.png
[2]: media/v2-update-provision/device-two-create.png
[3]: media/v2-update-provision/device-three-launch.png
[4]: media/v2-update-provision/device-four-iot-sim-page.png
[5]: media/v2-update-provision/device-five-params.png
[6]: media/v2-update-provision/device-seven-dashboard.png
[7]: media/v2-update-provision/device-six-listings.png
[8]: media/v2-update-provision/device-eight-portal.png

[9]: media/v2-update-provision/payg-one-azure.png
[10]: media/v2-update-provision/payg-two-create.png
[11]: media/v2-update-provision/payg-three-new.png
[12]: media/v2-update-provision/payg-four-add.png
[13]: media/v2-update-provision/payg-five-event-source.png
[14]: media/v2-update-provision/payg-six-review.png
[15]: media/v2-update-provision/payg-seven-deploy.png
[16]: media/v2-update-provision/payg-eight-environment.png
[17]: media/v2-update-provision/payg-nine-data-access.png
[18]: media/v2-update-provision/payg-ten-verify.png

[19]: media/v2-update-provision/analyze-one-portal.png
[20]: media/v2-update-provision/analyze-two-unparented.png
[21]: media/v2-update-provision/analyze-three-show-pressure.png
[22]: media/v2-update-provision/analyze-four-chart.png
[23]: media/v2-update-provision/analyze-five-chart.png
[24]: media/v2-update-provision/analyze-six-from.png
[25]: media/v2-update-provision/analyze-seven-change-from.png
[26]: media/v2-update-provision/analyze-eight-all.png

[27]: media/v2-update-provision/define-one-model.png
[28]: media/v2-update-provision/define-two-add.png
[29]: media/v2-update-provision/define-three-variable.png
[30]: media/v2-update-provision/define-four-avg.png
[31]: media/v2-update-provision/define-five-humidity.png
[32]: media/v2-update-provision/define-six-type.png
[33]: media/v2-update-provision/define-seven-hierarchy.png
[34]: media/v2-update-provision/define-eight-add-hierarchy.png
[35]: media/v2-update-provision/define-nine-created.png
[36]: media/v2-update-provision/define-ten-edit.png
[37]: media/v2-update-provision/define-eleven-chiller.png
[38]: media/v2-update-provision/define-twelve.png
[39]: media/v2-update-provision/define-thirteen-explore.png
[40]: media/v2-update-provision/define-fourteen-show-max.png
[41]: media/v2-update-provision/define-fifteen-filter.png
[42]: media/v2-update-provision/define-sixteen.png
[43]: media/v2-update-provision/define-seventeen.png
[44]: media/v2-update-provision/define-eighteen.png