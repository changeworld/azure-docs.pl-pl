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
ms.date: 04/25/2019
ms.custom: seodec18
ms.openlocfilehash: f83063a88207f51f9d481447923fd8a8498692a2
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64713918"
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

   [![Strona akceleratorów rozwiązań w usłudze Azure IoT](media/v2-update-provision/device-one-accelerator.png)](media/v2-update-provision/device-one-accelerator.png#lightbox)

   Wybierz pozycję **Wypróbuj teraz**.

1. Wprowadź wymagane parametry na stronie **Utwórz rozwiązanie symulacji urządzeń**:

   | Parametr | Opis |
   | --- | --- |
   | **Nazwa rozwiązania** |    Wprowadź unikatową wartość na potrzeby utworzenia nowej grupy zasobów. Znajdujące się na liście zasoby platformy Azure są tworzone i przypisywane do grupy zasobów. |
   | **Subskrypcja** | Określ subskrypcję użytą do utworzenia środowiska usługi Time Series Insights. |
   | **Region** |   Określ region użyty do utworzenia środowiska usługi Time Series Insights. |
   | **Wdróż opcjonalne zasoby platformy Azure**    | Pozostaw zaznaczone pole wyboru IoT Hub, ponieważ symulowane urządzenia będą używały tego centrum do łączenia się i strumieniowania danych. |

   Następnie wybierz pozycję **Utwórz rozwiązanie**. Poczekaj 10–15 minut na wdrożenie rozwiązania.

   [![Utwórz stronę rozwiązania symulacji urządzenia](media/v2-update-provision/device-two-create.png)](media/v2-update-provision/device-two-create.png#lightbox)

1. Na pulpicie nawigacyjnym akceleratora rozwiązań kliknij przycisk **Uruchom**:

   [![Uruchom rozwiązanie symulacji urządzenia](media/v2-update-provision/device-three-launch.png)](media/v2-update-provision/device-three-launch.png#lightbox)

1. Nastąpi przekierowanie do strony **Symulacja urządzeń Microsoft Azure IoT**. Wybierz pozycję **+ Nowa symulacja** w prawym górnym rogu strony.

   [![Strona symulacji w usłudze Azure IoT](media/v2-update-provision/device-four-iot-sim-page.png)](media/v2-update-provision/device-four-iot-sim-page.png#lightbox)

1. Wypełnij wymagane parametry następującymi wartościami:

    [![Parametry, aby wypełnić](media/v2-update-provision/device-five-params.png)](media/v2-update-provision/device-five-params.png#lightbox)

    |||
    | --- | --- |
    | **Nazwa** | Wprowadź unikatową nazwę symulatora. |
    | **Opis** | Wprowadź definicję. |
    | **Czas trwania symulacji** | Wybierz opcję **Uruchamianie bez ograniczeń**. |
    | **Model urządzenia** | **Nazwa**: Wprowadź polecenie `Chiller`. </br>**Ilość**: Wprowadź polecenie `3`. |
    | **Docelowa usługa IoT Hub** | Wybierz pozycję **Użyj dostarczonej wstępnie usługi IoT Hub**. |

    Następnie wybierz pozycję **Rozpocznij symulację**.

1. Na pulpicie nawigacyjnym symulacji urządzeń sprawdź opcje **Aktywne urządzenia** i **Komunikaty na sekundę**.

    [![Pulpit nawigacyjny symulacji usługi Azure IoT](media/v2-update-provision/device-seven-dashboard.png)](media/v2-update-provision/device-seven-dashboard.png#lightbox)

## <a name="list-device-simulation-properties"></a>Wyświetlanie właściwości symulacji urządzeń

Do utworzenia środowiska usługi Azure Time Series Insights potrzebne są nazwy centrum IoT Hub, subskrypcji i grupy zasobów.

1. Przejdź do pulpitu nawigacyjnego akceleratora rozwiązań i zaloguj się przy użyciu konta tej samej subskrypcji platformy Azure. Znajdź symulację urządzeń utworzoną w poprzednich krokach.

1. Wybierz symulator urządzeń, a następnie wybierz przycisk **Uruchom**. Wybierz link **Portal zarządzania Microsoft Azure** z prawej strony.

    [![Symulator list](media/v2-update-provision/device-six-listings.png)](media/v2-update-provision/device-six-listings.png#lightbox)

1. Zanotuj nazwy centrum IoT Hub, subskrypcji i grupy zasobów.

    [![Witryna Azure portal](media/v2-update-provision/device-eight-portal.png)](media/v2-update-provision/device-eight-portal.png#lightbox)

## <a name="create-a-time-series-insights-preview-payg-environment"></a>Tworzenie środowiska wersji zapoznawczej usługi Time Series Insights dostępnej w modelu PAYG

W tej sekcji opisano, jak utworzyć środowisko wersji zapoznawczej usługi Azure Time Series Insights przy użyciu witryny [Azure Portal](https://portal.azure.com/).

1. Zaloguj się do witryny Azure Portal przy użyciu konta subskrypcji.

1. Wybierz pozycję **Utwórz zasób**.

1. Wybierz kategorię **Internet rzeczy**, a następnie wybierz pozycję **Time Series Insights**.

   [![Wybierz Internetu rzeczy, a następnie wybierz usługi Time Series Insights](media/v2-update-provision/payg-one-azure.png)](media/v2-update-provision/payg-one-azure.png#lightbox)

1. Wypełnij pola na stronie w następujący sposób:

   | | |
   | --- | ---|
   | **Nazwa środowiska** | Wybierz unikatową nazwę środowiska wersji zapoznawczej usługi Azure Time Series Insights. |
   | **Subskrypcja** | Wprowadź subskrypcję, w której chcesz utworzyć środowisko wersji zapoznawczej usługi Azure Time Series Insights. Najlepiej jest użyć tej samej subskrypcji, w której są przechowywane pozostałe zasoby IoT utworzone przez symulator urządzeń. |
   | **Grupa zasobów** | Grupa zasobów jest kontenerem zasobów platformy Azure. Dla zasobu środowiska wersji zapoznawczej usługi Azure Time Series Insights wybierz istniejącą grupę zasobów lub utwórz nową. Najlepiej jest użyć tej samej grupy zasobów, w której są przechowywane pozostałe zasoby IoT utworzone przez symulator urządzeń. |
   | **Lokalizacja** | Wybierz region centrum danych dla środowiska wersji zapoznawczej usługi Azure Time Series Insights. Aby uniknąć kosztów i opóźnień spowodowanych dodatkową przepustowością, najlepiej przechowywać środowisko wersji zapoznawczej usługi Azure Time Series Insights w tym samym regionie co inne zasoby IoT. |
   | **Warstwa** |  Wybierz opcję **PAYG**, czyli model płatności zgodnie z rzeczywistym użyciem. Jest to jednostka SKU produktu Azure Time Series Insights w wersji zapoznawczej. |
   | **Identyfikator właściwości** | Wprowadź wartość umożliwiającą jednoznaczną identyfikację szeregu czasowego. Pamiętaj, że później nie można zmienić wartości tego pola. W tym samouczku używane `iothub-connection-device-id`. Aby dowiedzieć się więcej o identyfikatorze szeregu czasowego, zobacz [How to choose a Time Series ID (Jak wybrać identyfikator szeregu czasowego)](./time-series-insights-update-how-to-id.md). |
   | **Nazwa konta magazynu** | Wprowadź globalnie unikatową nazwę konta magazynu, które ma zostać utworzone. |

   Następnie wybierz opcję **Dalej: Źródło zdarzeń**.

   [![Strona Tworzenie środowiska usługi Time Series Insights](media/v2-update-provision/payg-two-create.png)](media/v2-update-provision/payg-two-create.png#lightbox)

1. Na stronie źródła zdarzeń wypełnij pola w następujący sposób:

   | | |
   | --- | --- |
   | **Utworzyć źródło zdarzeń?** | Wprowadź polecenie `Yes`.|
   | **Nazwa** | Wprowadź unikatową wartość używaną jako nazwa źródła zdarzeń.|
   | **Typ źródła** | Wybierz **usługi IoT Hub**. |
   | **Wybrać centrum?** | Wybierz **wybierz istniejący**. |
   | **Subskrypcja** | Wybierz subskrypcję, której używana przez symulator urządzenia. |
   | **Nazwa centrum IoT** | Wybierz nazwę Centrum IoT utworzonego symulator urządzenia. |
   | **Zasady dostępu do centrum IoT Hub** | Wybierz pozycję **iothubowner**. |
   | **Grupa konsumentów centrum IoT** | Do korzystania z wersji zapoznawczej usługi Azure Time Series Insights potrzebna jest unikatowa grupa użytkowników. Wybierz pozycję **Nowa**, wpisz unikatową nazwę, a następnie wybierz polecenie **Dodaj**. |
   | **Właściwość znacznika czasu** | To pole umożliwia identyfikowanie właściwości sygnatury czasowej w przychodzących danych telemetrycznych. W tym samouczku nie należy wypełniać tego pola. Ten symulator używa sygnatury czasowej pochodzącej z usługi IoT Hub, domyślnej wartości dla usługi Time Series Insights.|

   Następnie wybierz pozycję **Przegląd + utwórz**.

   [![Konfigurowanie źródła zdarzeń](media/v2-update-provision/payg-five-event-source.png)](media/v2-update-provision/payg-five-event-source.png#lightbox)

1. Sprawdź zawartość wszystkich pól na stronie przeglądu i wybierz przycisk **Utwórz**.

   [![Przeglądanie + Tworzenie strony, przy użyciu przycisku Utwórz](media/v2-update-provision/payg-six-review.png)](media/v2-update-provision/payg-six-review.png#lightbox)

1. Będą wyświetlane informacje o stanie wdrożenia.

   [![Powiadomienie, że wdrożenie jest ukończone](media/v2-update-provision/payg-seven-deploy.png)](media/v2-update-provision/payg-seven-deploy.png#lightbox)

1. Jeśli jesteś właścicielem dzierżawy, uzyskasz dostęp do środowiska wersji zapoznawczej usługi Azure Time Series Insights. Aby to sprawdzić:

   a. Wyszukaj grupę zasobów i wybierz swoje środowisko wersji zapoznawczej usługi Azure Time Series Insights:

      [![Wybrane środowisko](media/v2-update-provision/payg-eight-environment.png)](media/v2-update-provision/payg-eight-environment.png#lightbox)

   b. Na stronie wersji zapoznawczej usługi Azure Time Series Insights przejdź do obszaru **Zasady dostępu do danych**.

     [![Zasady dostępu do danych](media/v2-update-provision/payg-nine-data-access.png)](media/v2-update-provision/payg-nine-data-access.png#lightbox)

   c. Sprawdź, czy widać Twoje poświadczenia.

     [![Poświadczenia listy](media/v2-update-provision/payg-ten-verify.png)](media/v2-update-provision/payg-ten-verify.png#lightbox)

   Jeśli poświadczenia nie są widoczne, musisz przyznać sobie uprawnienie dostępu do środowiska. Aby dowiedzieć się więcej o ustawianiu uprawnień, zobacz [Grant data access (Przyznawanie dostępu do danych)](./time-series-insights-data-access.md).

## <a name="analyze-data-in-your-environment"></a>Analizowanie danych w środowisku

W tej sekcji przeprowadzimy podstawową analizę danych szeregów czasowych przy użyciu [eksploratora wersji zapoznawczej usługi Azure Time Series Insights](./time-series-insights-update-explorer.md).

1. Przejdź do eksploratora wersji zapoznawczej usługi Azure Time Series Insights, wybierając adres URL na stronie zasobu w witrynie [Azure Portal](https://portal.azure.com/).

   [![Adres URL explorer czasu Series Insights w wersji zapoznawczej](media/v2-update-provision/analyze-one-portal.png)](media/v2-update-provision/analyze-one-portal.png#lightbox)

1. W Eksploratorze, wybierz **wystąpienia serii czasu** węzeł, aby wyświetlić wszystkie wystąpienia Azure czas Series Insights w wersji zapoznawczej w środowisku.

   [![Lista wystąpień bez elementów nadrzędnych](media/v2-update-provision/analyze-two-unparented.png)](media/v2-update-provision/analyze-two-unparented.png#lightbox)

1. W wyświetlonym szeregu czasowym wybierz pierwsze wystąpienie. Następnie wybierz pozycję **Show Avg pressure** (Wyświetl ciśnienie średnie).

   [![Wybrane wystąpienie za pomocą polecenia menu, aby pokazać średnie wykorzystanie](media/v2-update-provision/analyze-three-show-pressure.png)](media/v2-update-provision/analyze-three-show-pressure.png#lightbox)

   Wykres serii czasu powinna zostać wyświetlona po prawej stronie. Dostosuj **interwał** do `15s`.

   [![Czas serii wykresu](media/v2-update-provision/analyze-four-chart.png)](media/v2-update-provision/analyze-four-chart.png#lightbox)

1. Powtórz **krok 3** dla dwóch pozostałych szeregów czasowych. Następnie możesz wyświetlić wszystkie szeregi czasowe, jak na poniższym wykresie:

   [![Wykres wszystkie Szeregi czasowe](media/v2-update-provision/analyze-five-chart.png)](media/v2-update-provision/analyze-five-chart.png#lightbox)

1. Zmień zakres czasu, aby wyświetlić trendy szeregów czasowych w ciągu ostatniej godziny.

   a. Wybierz **przedział czasu** pole opcji:

      [![Ustaw zakres czasu do godziny](media/v2-update-provision/analyze-six-time.png)](media/v2-update-provision/analyze-six-time.png#lightbox)

## <a name="define-and-apply-a-model"></a>Definiowanie i stosowanie modelu

W tej sekcji zastosujesz model, aby ustrukturyzować dane. Do utworzenia modelu potrzebne są definicje typów, hierarchii i wystąpień. Aby dowiedzieć się więcej na temat modelowania danych, zobacz [Time Series Model (Model szeregów czasowych)](./time-series-insights-update-tsm.md).

1. W eksploratorze wybierz kartę **Model**:

   [![Karta modelu w Eksploratorze](media/v2-update-provision/define-one-model.png)](media/v2-update-provision/define-one-model.png#lightbox)

1. Wybierz pozycję **+ Dodaj**, aby dodać typ. Po prawej stronie zostanie otwarty edytor typów.

   [![Przycisk Dodaj, dla typów](media/v2-update-provision/define-two-add.png)](media/v2-update-provision/define-two-add.png#lightbox)

1. Zdefiniuj trzy zmienne dla typu: ciśnienie, temperaturę i wilgotność. Wprowadź następujące informacje:

   | | |
   | --- | ---|
   | **Nazwa** | Wprowadź polecenie `Chiller`. |
   | **Opis** | Wprowadź polecenie `This is a type definition of Chiller`. |

   * Teraz zdefiniuj parametr ciśnienia z trzema zmiennymi:

      | | |
      | --- | ---|
      | **Nazwa** | Wprowadź polecenie `Avg Pressure`. |
      | **Wartość** | Wybierz opcję **pressure (Double)**. Pamiętaj, że gdy wersja zapoznawcza usługi Azure Time Series Insights zacznie odbierać zdarzenia, wypełnienie tego pola może potrwać kilka minut. |
      | **Operacja agregacji** | Wybierz opcję **AVG** (średnia). |

      [![Opcje do definiowania ciśnienia](media/v2-update-provision/define-three-variable.png)](media/v2-update-provision/define-three-variable.png#lightbox)

      Kliknij pozycję **+ Dodaj zmienną**, aby dodać kolejną zmienną.

   * Definiowanie temperatury:

      | | |
      | --- | ---|
      | **Nazwa** | Wprowadź polecenie `Avg Temperature`. |
      | **Wartość** | Wybierz opcję **temperature (Double)**. Pamiętaj, że gdy wersja zapoznawcza usługi Azure Time Series Insights zacznie odbierać zdarzenia, wypełnienie tego pola może potrwać kilka minut. |
      | **Operacja agregacji** | Wybierz opcję **AVG** (średnia).|

      [![Opcje określania temperatury](media/v2-update-provision/define-four-avg.png)](media/v2-update-provision/define-four-avg.png#lightbox)

   * Zdefiniuj wilgotność:

      | | |
      | --- | ---|
      | **Nazwa** | Wprowadź wartość `Max Humidity` |
      | **Wartość** | Wybierz opcję **humidity (Double)**. Pamiętaj, że gdy wersja zapoznawcza usługi Azure Time Series Insights zacznie odbierać zdarzenia, wypełnienie tego pola może potrwać kilka minut. |
      | **Operacja agregacji** | Wybierz **MAX**.|

      [![Opcje określania temperatury](media/v2-update-provision/define-five-humidity.png)](media/v2-update-provision/define-five-humidity.png#lightbox)

   Następnie wybierz przycisk **Create** (Utwórz).

1. Zobaczysz dodany typ:

   [![Informacje o typie dodano](media/v2-update-provision/define-six-type.png)](media/v2-update-provision/define-six-type.png#lightbox)

1. Następnym krokiem jest dodanie hierarchii. W sekcji **Hierarchie** wybierz pozycję **+ Dodaj**:

   [![Karta Hierarchie za pomocą przycisku Dodaj](media/v2-update-provision/define-seven-hierarchy.png)](media/v2-update-provision/define-seven-hierarchy.png#lightbox)

1. Zdefiniuj hierarchię. Wypełnij pola w następujący sposób:

   | | |
   | --- | ---|
   | **Nazwa** | Wprowadź polecenie `Location Hierarchy`. |
   | **Level 1** | Wprowadź polecenie `Country`. |
   | **Level 2** | Wprowadź polecenie `City`. |
   | **Level 3** | Wprowadź polecenie `Building`. |

   Następnie wybierz przycisk **Create** (Utwórz).

   [![Pola hierarchii przy użyciu przycisku Utwórz](media/v2-update-provision/define-eight-add-hierarchy.png)](media/v2-update-provision/define-eight-add-hierarchy.png#lightbox)

1. Zobaczysz utworzoną hierarchię:

   [![Informacje dotyczące hierarchii](media/v2-update-provision/define-nine-created.png)](media/v2-update-provision/define-nine-created.png#lightbox)

1. Wybierz pozycję **Wystąpienia** po lewej stronie. Po wyświetleniu wystąpień wybierz pierwsze wystąpienie i wybierz pozycję **Edytuj**:

   [![Wybierając przycisk edycji wystąpienia](media/v2-update-provision/define-ten-edit.png)](media/v2-update-provision/define-ten-edit.png#lightbox)

1. Po prawej stronie pojawi się edytor tekstu. Dodaj następujące informacje:

   | | |
   | --- | --- |
   | **Typ** | Wybierz pozycję **Chiller** (Chłodnia). |
   | **Opis** | Wprowadź polecenie `Instance for Chiller-01.1`. |
   | **Hierarchies** | Wybierz **hierarchii lokalizacji**. |
   | **Country** | Wprowadź polecenie `USA`. |
   | **City** | Wprowadź polecenie `Seattle`. |
   | **Building** | Wprowadź polecenie `Space Needle`. |

    Następnie wybierz pozycję **Zapisz**.

   [![Pola wystąpienia przycisk zapisywania](media/v2-update-provision/define-eleven-chiller.png)](media/v2-update-provision/define-eleven-chiller.png#lightbox)

1. Powtórz poprzedni krok dla pozostałych czujników. Użyj następujących pól:

   * Dla chłodni Chiller 01.2:

     | | |
     | --- | --- |
     | **Typ** | Wybierz pozycję **Chiller** (Chłodnia). |
     | **Opis** | Wprowadź polecenie `Instance for Chiller-01.2`. |
     | **Hierarchies** | Wybierz **hierarchii lokalizacji**. |
     | **Country** | Wprowadź polecenie `USA`. |
     | **City** | Wprowadź polecenie `Seattle`. |
     | **Building** | Wprowadź polecenie `Pacific Science Center`. |

   * Dla chłodni Chiller 01.3:

     | | |
     | --- | --- |
     | **Typ** | Wybierz pozycję **Chiller** (Chłodnia). |
     | **Opis** | Wprowadź polecenie `Instance for Chiller-01.3`. |
     | **Hierarchies** | Wybierz **hierarchii lokalizacji**. |
     | **Country** | Wprowadź polecenie `USA`. |
     | **City** | Wprowadź polecenie `New York`. |
     | **Building** | Wprowadź polecenie `Empire State Building`. |

1. Przejdź do karty **Analiza**, a następnie odśwież stronę. Rozwiń wszystkie poziomy hierarchii, aby znaleźć szereg czasowy.

   [![Na karcie analiza](media/v2-update-provision/define-twelve.png)](media/v2-update-provision/define-twelve.png#lightbox)

1. Aby zobaczyć szereg czasowy z ostatniej godziny, zmień opcję w polu **Szybkie czasy** na **Ostatnia godzina**:

    [![Pole krótkie okresy z ostatniej godziny wybrana](media/v2-update-provision/define-thirteen-explore.png)](media/v2-update-provision/define-thirteen-explore.png#lightbox)

1. Wybierz szereg czasowy w obszarze **Pacific Science Center** i wybierz pozycję **Show Max Humidity** (Wyświetl wilgotność maksymalną).

    [![Szeregi czasowe wybranego z opcją menu Pokaż maksymalna wilgotność](media/v2-update-provision/define-fourteen-show-max.png)](media/v2-update-provision/define-fourteen-show-max.png#lightbox)

1. Serie czasu dla **maksymalna wilgotność** o rozmiarze interwał **1 minutę** zostanie otwarty. Aby odfiltrować zakres, wybierz region. Następnie kliknij prawym przyciskiem myszy i wybierz opcję **Powiększenie**, aby przeanalizować zdarzenia w przedziale czasowym:

   [![Wybrany zakres przy użyciu polecenia powiększenia w menu skrótów](media/v2-update-provision/define-fifteen-filter.png)](media/v2-update-provision/define-fifteen-filter.png#lightbox)

1. Możesz również wybrać region, a następnie kliknąć prawym przyciskiem myszy, aby wyświetlić szczegóły zdarzenia:

   [![Szczegółowa lista zdarzeń](media/v2-update-provision/define-eighteen.png)](media/v2-update-provision/define-eighteen.png#lightbox)

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:  

> [!div class="checklist"]
> * Tworzenie i używanie akceleratora symulacji urządzenia.
> * Tworzenie środowiska wersji zapoznawczej usługi Azure Time Series Insights dostępnej w modelu PAYG.
> * Łączenie środowiska wersji zapoznawczej usługi Azure Time Series Insights z centrum zdarzeń.
> * Uruchamianie przykładowego akceleratora rozwiązań umożliwiającego strumieniowe przesyłanie danych do środowiska wersji zapoznawczej usługi Azure Time Series Insights.
> * Wykonywanie podstawowej analizy danych.
> * Definiowanie hierarchii oraz typu modelu szeregów czasowych i kojarzenie ich z wystąpieniami.

Wiesz już, jak utworzyć własne środowisko wersji zapoznawczej usługi Azure Time Series Insights. Teraz możesz dowiedzieć się więcej na temat kluczowych pojęć związanych z usługą Azure Time Series Insights.

Zapoznaj się z konfiguracją magazynu usługi Azure Time Series Insights:

> [!div class="nextstepaction"]
> [Magazyn i ruch przychodzący usługi Azure Time Series Insights w wersji zapoznawczej](./time-series-insights-update-storage-ingress.md)

Dowiedz się więcej o modelach szeregów czasowych:

> [!div class="nextstepaction"]
> [Modelowanie danych w usłudze Azure Time Series Insights w wersji zapoznawczej](./time-series-insights-update-tsm.md)