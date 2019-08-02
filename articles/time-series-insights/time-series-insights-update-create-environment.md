---
title: 'Samouczek: konfigurowanie środowiska wersji zapoznawczej usługi Azure Time Series Insights | Microsoft Docs'
description: Dowiedz się, jak skonfigurować środowisko wersji zapoznawczej usługi Azure Time Series Insights.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 07/29/2019
ms.custom: seodec18
ms.openlocfilehash: 998471d99a785eeff39ef7c99e60e1d9b49e0d7a
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68725831"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Samouczek: Konfigurowanie środowiska wersji zapoznawczej usługi Azure Time Series Insights

Ten samouczek zawiera instrukcje tworzenia środowiska wersji zapoznawczej usługi Azure Time Series Insights dostępnej w modelu płatności zgodnie z rzeczywistym użyciem (PAYG). 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie środowiska wersji zapoznawczej usługi Azure Time Series Insights.
> * Łączenie środowiska wersji zapoznawczej usługi Azure Time Series Insights z centrum zdarzeń w usłudze Azure Event Hubs.
> * Uruchamianie przykładowego akceleratora rozwiązań umożliwiającego strumieniowe przesyłanie danych do środowiska wersji zapoznawczej usługi Azure Time Series Insights.
> * Wykonywanie podstawowej analizy danych.
> * Definiowanie hierarchii oraz typu modelu szeregów czasowych i kojarzenie ich z wystąpieniami.

>[!TIP]
> [Akceleratory rozwiązań IoT](https://www.azureiotsolutions.com/Accelerators) zapewniają wstępnie skonfigurowane rozwiązania klasy korporacyjnej, których można użyć do przyspieszenia tworzenia niestandardowych rozwiązań IoT.

Utwórz konto bezpłatnej [subskrypcji platformy Azure](https://azure.microsoft.com/free/) , jeśli jeszcze jej nie masz.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto logowania platformy Azure musi być również członkiem roli **właściciela** subskrypcji. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem przy użyciu kontroli dostępu opartej na rolach i Azure Portal](../role-based-access-control/role-assignments-portal.md).

## <a name="create-a-device-simulation"></a>Tworzenie symulacji urządzeń

W tej sekcji utworzysz trzy symulowane urządzenia, które wysyłają dane do wystąpienia usługi Azure IoT Hub.

1. Przejdź do [strony akceleratorów rozwiązań Azure IoT](https://www.azureiotsolutions.com/Accelerators). Na stronie będzie widocznych kilka gotowych przykładów. Zaloguj się przy użyciu konta platformy Azure. Następnie wybierz pozycję **Symulacja urządzenia**.

   [![Strona akceleratorów rozwiązań usługi Azure IoT](media/v2-update-provision/device-one-accelerator.png)](media/v2-update-provision/device-one-accelerator.png#lightbox)

   Wybierz pozycję **Wypróbuj teraz**.

1. Na stronie **Tworzenie rozwiązania symulacji urządzenia** ustaw następujące parametry:

    | Parametr | Action |
    | --- | --- |
    | **Nazwa wdrożenia** | Wprowadź unikatową wartość nowej grupy zasobów. Znajdujące się na liście zasoby platformy Azure są tworzone i przypisywane do grupy zasobów. |
    | **Subskrypcja platformy Azure** | Wybierz subskrypcję, która została użyta do utworzenia środowiska Time Series Insightsowego. |
    | **Lokalizacja platformy Azure** | Wybierz region, który został użyty do utworzenia środowiska Time Series Insightsowego. |
    | **Opcje wdrażania** | Wybierz pozycję **Udostępnij nowe IoT Hub**. |
 
    Wybierz pozycję **Utwórz rozwiązanie**. Ukończenie wdrażania rozwiązania może potrwać do 20 minut.

    [![Strona tworzenia rozwiązania symulacji urządzenia](media/v2-update-provision/device-two-create.png)](media/v2-update-provision/device-two-create.png#lightbox)

## <a name="create-a-preview-payg-environment"></a>Tworzenie środowiska w wersji zapoznawczej PAYG

W tej sekcji opisano sposób tworzenia środowiska Azure Time Series Insights w wersji zapoznawczej i łączenia go z usługą IoT Hub utworzoną przez Akcelerator rozwiązania IoT przy użyciu [Azure Portal](https://portal.azure.com/).

1. Zaloguj się do witryny Azure Portal przy użyciu konta subskrypcji.

1. Wybierz pozycję **Utwórz zasób** > **Internet rzeczy** > **Time Series Insights**.

   [![Wybierz pozycję Internet rzeczy, a następnie wybierz pozycję Time Series Insights](media/v2-update-provision/payg-one-azure.png)](media/v2-update-provision/payg-one-azure.png#lightbox)

1. W okienku **Tworzenie środowiska Time Series Insights** na karcie **podstawowe** ustaw następujące parametry:

    | Parametr | Action |
    | --- | ---|
    | **Nazwa środowiska** | Wprowadź unikatową nazwę środowiska Azure Time Series Insights w wersji zapoznawczej. |
    | **Subskrypcja** | Wprowadź subskrypcję, w której chcesz utworzyć środowisko Azure Time Series Insights w wersji zapoznawczej. Najlepszym rozwiązaniem jest użycie tej samej subskrypcji jako reszty zasobów IoT, które są tworzone przez symulatora urządzeń. |
    | **Grupa zasobów** | Wybierz istniejącą grupę zasobów lub Utwórz nową grupę zasobów dla zasobu środowiska Azure Time Series Insights wersji zapoznawczej. Grupa zasobów jest kontenerem zasobów platformy Azure. Najlepszym rozwiązaniem jest użycie tej samej grupy zasobów co inne zasoby IoT, które są tworzone przez symulatora urządzeń. |
    | **Location** | Wybierz region centrum danych dla środowiska Azure Time Series Insights w wersji zapoznawczej. Aby uniknąć dodatkowego opóźnienia, najlepszym rozwiązaniem jest utworzenie środowiska Azure Time Series Insights w wersji zapoznawczej w tym samym regionie, w którym znajduje się inne zasoby IoT. |
    | **Warstwa** |  Wybierz pozycję **PAYG** (*płatność zgodnie z rzeczywistym*użyciem). Jest to jednostka SKU produktu Azure Time Series Insights w wersji zapoznawczej. |
    | **Identyfikator właściwości** | Wprowadź wartość, która jednoznacznie identyfikuje wystąpienie szeregów czasowych. Wartość wprowadzona w polu **Identyfikator właściwości** jest niezmienna. Nie można go zmienić później. Na potrzeby tego samouczka wprowadź **iothub-Connection-Device-ID**. Aby dowiedzieć się więcej na temat identyfikatora szeregów czasowych, zobacz [najlepsze rozwiązania dotyczące wybierania identyfikatora szeregów czasowych](./time-series-insights-update-how-to-id.md). |
    | **Nazwa konta magazynu** | Wprowadź globalnie unikatową nazwę nowego konta magazynu, które ma zostać utworzone. |
   
   Wybierz opcję **Dalej: Źródło zdarzeń**.

   [![Okienko do tworzenia środowiska Time Series Insights](media/v2-update-provision/payg-two-create.png)](media/v2-update-provision/payg-two-create.png#lightbox)

1. Na karcie **Źródło zdarzenia** ustaw następujące parametry:

   | Parametr | Action |
   | --- | --- |
   | **Utworzyć źródło zdarzeń?** | Wybierz pozycję **tak**.|
   | **Nazwa** | Wprowadź unikatową wartość dla nazwy źródła zdarzenia. |
   | **Typ źródła** | Wybierz **IoT Hub**. |
   | **Wybierz centrum** | Wybierz **pozycję Wybierz istniejące**. |
   | **Subskrypcja** | Wybierz subskrypcję używaną dla symulatora urządzeń. |
   | **Nazwa centrum IoT** | Wybierz nazwę usługi IoT Hub utworzoną dla symulatora urządzeń. |
   | **Zasady dostępu do centrum IoT Hub** | Wybierz pozycję **iothubowner**. |
   | **Grupa konsumentów IoT Hub** | Wybierz pozycję **Nowa**, wpisz unikatową nazwę, a następnie wybierz polecenie **Dodaj**. Grupa konsumentów musi być unikatowa w wersji zapoznawczej Azure Time Series Insights. |
   | **Właściwość znacznika czasu** | Ta wartość służy do identyfikowania właściwości **timestamp** w przychodzących danych telemetrycznych. Na potrzeby tego samouczka pozostaw to pole puste. Ten symulator używa sygnatury czasowej pochodzącej z usługi IoT Hub, domyślnej wartości dla usługi Time Series Insights. |

   Wybierz pozycję **Przegląd + utwórz**.

   [![Konfigurowanie źródła zdarzeń](media/v2-update-provision/payg-five-event-source.png)](media/v2-update-provision/payg-five-event-source.png#lightbox)

1. Na karcie **Przegląd + tworzenie** Przejrzyj wybrane opcje, a następnie wybierz pozycję **Utwórz**.

    [![Przegląd + Tworzenie strony, za pomocą przycisku Utwórz](media/v2-update-provision/payg-six-review.png)](media/v2-update-provision/payg-six-review.png#lightbox)

    Możesz zobaczyć stan wdrożenia:

    [![Powiadomienie o ukończeniu wdrożenia](media/v2-update-provision/payg-seven-deploy.png)](media/v2-update-provision/payg-seven-deploy.png#lightbox)

1. Jeśli jesteś posiadaczem dzierżawy, masz dostęp do środowiska Azure Time Series Insights w wersji zapoznawczej. Aby to sprawdzić:

   1. Wyszukaj grupę zasobów, a następnie wybierz środowisko Azure Time Series Insights w wersji zapoznawczej:

      [![Wybrane środowisko](media/v2-update-provision/payg-eight-environment.png)](media/v2-update-provision/payg-eight-environment.png#lightbox)

   1. Na stronie Podgląd Azure Time Series Insights wybierz pozycję **zasady dostępu do danych**:

      [![Zasady dostępu do danych](media/v2-update-provision/payg-nine-data-access.png)](media/v2-update-provision/payg-nine-data-access.png#lightbox)

   1. Sprawdź, czy na liście znajdują się Twoje poświadczenia:

      [![Podane poświadczenia](media/v2-update-provision/payg-ten-verify.png)](media/v2-update-provision/payg-ten-verify.png#lightbox)

   Jeśli Twoje poświadczenia nie znajdują się na liście, musisz udzielić sobie uprawnień dostępu do środowiska. Aby dowiedzieć się więcej o ustawianiu uprawnień, zobacz [Grant data access (Przyznawanie dostępu do danych)](./time-series-insights-data-access.md).

## <a name="stream-data"></a>Przesyłanie strumieniowe danych

Teraz, gdy wdrożono środowisko Time Series Insights, przesyłaj strumieniowo dane do analizy.

1. Przejdź z powrotem do [strony akceleratorów rozwiązań usługi Azure IoT](https://www.azureiotsolutions.com/Accelerators). Znajdź swoje rozwiązanie na pulpicie nawigacyjnym akceleratora rozwiązania. Następnie wybierz pozycję **Uruchom**:

    [![Uruchamianie rozwiązania do symulacji urządzenia](media/v2-update-provision/device-three-launch.png)](media/v2-update-provision/device-three-launch.png#lightbox)

1. Nastąpi przekierowanie do strony **Symulacja urządzeń Microsoft Azure IoT**. W prawym górnym rogu strony wybierz pozycję **Nowa symulacja**.

    [![Strona symulacji usługi Azure IoT](media/v2-update-provision/device-four-iot-sim-page.png)](media/v2-update-provision/device-four-iot-sim-page.png#lightbox)

1. W okienku **Konfiguracja symulacji** ustaw następujące parametry:

    | Parametr | Action |
    | --- | --- |
    | **Nazwa** | Wprowadź unikatową nazwę symulatora. |
    | **Opis** | Wprowadź definicję. |
    | **Czas trwania symulacji** | Wybierz opcję **Uruchamianie bez ograniczeń**. |
    | **Model urządzenia** | **Nazwa**: Wpisz **Chiller** (chłodnia). <br />**Ilość**: Wpisz **3**. |
    | **Docelowa usługa IoT Hub** | Wybierz pozycję **Użyj dostarczonej wstępnie usługi IoT Hub**. |

    [![Parametry do ustawienia](media/v2-update-provision/device-five-params.png)](media/v2-update-provision/device-five-params.png#lightbox)

    Wybierz pozycję **Rozpocznij symulację**.

    Na pulpicie nawigacyjnym symulacji urządzenia Zwróć uwagę na informacje wyświetlane dla **aktywnych urządzeń** i **komunikatów na sekundę**.

    [![Pulpit nawigacyjny symulacji usługi Azure IoT](media/v2-update-provision/device-seven-dashboard.png)](media/v2-update-provision/device-seven-dashboard.png#lightbox)

## <a name="analyze-data"></a>Analizowanie danych

W tej sekcji przeprowadzimy podstawową analizę danych szeregów czasowych przy użyciu [eksploratora wersji zapoznawczej usługi Azure Time Series Insights](./time-series-insights-update-explorer.md).

1. Przejdź do eksploratora wersji zapoznawczej usługi Azure Time Series Insights, wybierając adres URL na stronie zasobu w witrynie [Azure Portal](https://portal.azure.com/).

    [![Adres URL Eksploratora Time Series Insights w wersji zapoznawczej](media/v2-update-provision/analyze-one-portal.png)](media/v2-update-provision/analyze-one-portal.png#lightbox)

1. W Eksploratorze wybierz węzeł **wystąpienia szeregów czasowych** , aby zobaczyć wszystkie Azure Time Series Insights wystąpienia w wersji zapoznawczej w środowisku.

    [![Lista nienadrzędnych wystąpień](media/v2-update-provision/analyze-two-unparented.png)](media/v2-update-provision/analyze-two-unparented.png#lightbox)

1. Wybierz pierwsze wystąpienie szeregów czasowych. Następnie wybierz pozycję **Pokaż ciśnienie**.

    [![Wybrane wystąpienie szeregów czasowych z poleceniem menu do wyświetlania średniego ciśnienia](media/v2-update-provision/analyze-three-show-pressure.png)](media/v2-update-provision/analyze-three-show-pressure.png#lightbox)

    Zostanie wyświetlony wykres szeregów czasowych. Zmień **Interwał** na **15s**.

    [![Wykres szeregów czasowych](media/v2-update-provision/analyze-four-chart.png)](media/v2-update-provision/analyze-four-chart.png#lightbox)

1. Powtórz krok 3 z innymi dwoma wystąpieniami szeregów czasowych. Można wyświetlić wszystkie wystąpienia szeregów czasowych, jak pokazano na tym wykresie:

    [![Wykres dla wszystkich szeregów czasowych](media/v2-update-provision/analyze-five-chart.png)](media/v2-update-provision/analyze-five-chart.png#lightbox)

1. W polu opcja przedziału czasu zmodyfikuj zakres godzin, aby zobaczyć trendy szeregów czasowych w ciągu ostatniej godziny:

    [![Ustaw zakres czasu na godzinę](media/v2-update-provision/analyze-six-time.png)](media/v2-update-provision/analyze-six-time.png#lightbox)

## <a name="define-and-apply-a-model"></a>Definiowanie i stosowanie modelu

W tej sekcji zastosujesz model, aby ustrukturyzować dane. Do utworzenia modelu potrzebne są definicje typów, hierarchii i wystąpień. Aby dowiedzieć się więcej na temat modelowania danych, zobacz [model szeregów czasowych](./time-series-insights-update-tsm.md).

1. W eksploratorze wybierz kartę **Model**:

   [![Karta model w Eksploratorze](media/v2-update-provision/define-one-model.png)](media/v2-update-provision/define-one-model.png#lightbox)

1. Wybierz pozycję **Dodaj** , aby dodać typ:

   [![Przycisk Dodaj dla typów](media/v2-update-provision/define-two-add.png)](media/v2-update-provision/define-two-add.png#lightbox)

1. Następnie należy zdefiniować trzy zmienne dla typu: *ciśnienie*, *temperatura*i wilgotność. W okienku **Dodawanie typu** ustaw następujące parametry:

    | Parametr | Action |
    | --- | ---|
    | **Nazwa** | Wpisz **Chiller** (chłodnia). |
    | **Opis** | Wpisz **This is a type definition of Chiller** (To jest definicja typu dla chłodni). |

   * Aby zdefiniować *nacisk*, w obszarze **zmienne**ustaw następujące parametry:

     | Parametr | Action |
     | --- | ---|
     | **Nazwa** | Wpisz **Avg Pressure** (Ciśnienie średnie). |
     | **Wartość** | Wybierz opcję **pressure (Double)** . Automatyczne wypełnianie **wartości** może potrwać kilka minut po rozpoczęciu otrzymywania zdarzeń przez Azure Time Series Insights Preview. |
     | **Operacja agregacji** | Wybierz opcję **AVG** (średnia). |

      [![Opcje definiowania nacisku](media/v2-update-provision/define-three-variable.png)](media/v2-update-provision/define-three-variable.png#lightbox)

      Aby dodać następną zmienną, wybierz pozycję **Dodaj zmienną**.

   * Zdefiniuj *temperaturę*:

     | Parametr | Action |
     | --- | ---|
     | **Nazwa** | Wpisz **Avg Temperature** (Temperatura średnia). |
     | **Wartość** | Wybierz opcję **temperature (Double)** . Automatyczne wypełnianie **wartości** może potrwać kilka minut po rozpoczęciu otrzymywania zdarzeń przez Azure Time Series Insights Preview. |
     | **Operacja agregacji** | Wybierz opcję **AVG** (średnia).|

      [![Wybory dla definiowania temperatury](media/v2-update-provision/define-four-avg.png)](media/v2-update-provision/define-four-avg.png#lightbox)

      Aby dodać następną zmienną, wybierz pozycję **Dodaj zmienną**.

   * Zdefiniuj *wilgotność*:

      | | |
      | --- | ---|
      | **Nazwa** | Wprowadź **maksymalną wilgotność** |
      | **Wartość** | Wybierz opcję **humidity (Double)** . Automatyczne wypełnianie **wartości** może potrwać kilka minut po rozpoczęciu otrzymywania zdarzeń przez Azure Time Series Insights Preview. |
      | **Operacja agregacji** | Wybierz **MAX**.|

      [![Wybory dla definiowania temperatury](media/v2-update-provision/define-five-humidity.png)](media/v2-update-provision/define-five-humidity.png#lightbox)

    Wybierz pozycję **Utwórz**.

    Możesz zobaczyć dodany typ:

    [![Informacje o dodanym typie](media/v2-update-provision/define-six-type.png)](media/v2-update-provision/define-six-type.png#lightbox)

1. Następnym krokiem jest dodanie hierarchii. Wobszarze hierarchie wybierz pozycję **Dodaj**:

    [![Karta hierarchie z przyciskiem Dodaj](media/v2-update-provision/define-seven-hierarchy.png)](media/v2-update-provision/define-seven-hierarchy.png#lightbox)

1. W okienku **Edytuj hierarchię** ustaw następujące parametry:

   | Parametr | Action |
   | --- | ---|
   | **Nazwa** | Wpisz **Location Hierarchy** (Hierarchia lokalizacji). |
   | **Level 1** | Wpisz **Country** (Kraj). |
   | **Level 2** | Wpisz **City** (Miasto). |
   | **Level 3** | Wpisz **Building** (Budynek). |

   Wybierz pozycję **Zapisz**.

    [![Pola hierarchii z przyciskiem tworzenia](media/v2-update-provision/define-eight-add-hierarchy.png)](media/v2-update-provision/define-eight-add-hierarchy.png#lightbox)

   Zobaczysz utworzoną hierarchię:

    [![Informacje o hierarchii](media/v2-update-provision/define-nine-created.png)](media/v2-update-provision/define-nine-created.png#lightbox)

1. Wybierz **wystąpienia**. Wybierz pierwsze wystąpienie, a następnie wybierz pozycję **Edytuj**:

    [![Wybieranie przycisku Edytuj dla wystąpienia](media/v2-update-provision/define-ten-edit.png)](media/v2-update-provision/define-ten-edit.png#lightbox)

1. W okienku **Edytuj wystąpienia** ustaw następujące parametry:

    | Parametr | Action |
    | --- | --- |
    | **Typ** | Wybierz pozycję **Chiller** (Chłodnia). |
    | **Opis** | Wpisz **Instance for Chiller-01.1** (Wystąpienie dla urządzenia Chiller-01.1). |
    | **Hierarchies** | Wybierz **hierarchię lokalizacji**. |
    | **Country** | Wpisz **USA**. |
    | **City** | Wpisz **Seattle**. |
    | **Building** | Wpisz **Space Needle**. |

    [![Pola wystąpienia z przyciskiem Zapisz](media/v2-update-provision/define-eleven-chiller.png)](media/v2-update-provision/define-eleven-chiller.png#lightbox)

   Wybierz pozycję **Zapisz**.

1. Powtórz poprzedni krok dla innych czujników. Zaktualizuj następujące wartości:

   * Dla chłodni Chiller 01.2:

     | Parametr | Action |
     | --- | --- |
     | **Typ** | Wybierz pozycję **Chiller** (Chłodnia). |
     | **Opis** | Wpisz **Instance for Chiller-01.2** (Wystąpienie dla urządzenia Chiller-01.2). |
     | **Hierarchies** | Wybierz **hierarchię lokalizacji**. |
     | **Country** | Wpisz **USA**. |
     | **City** | Wpisz **Seattle**. |
     | **Building** | Wpisz **Pacific Science Center**. |

   * Dla chłodni Chiller 01.3:

     | Parametr | Action |
     | --- | --- |
     | **Typ** | Wybierz pozycję **Chiller** (Chłodnia). |
     | **Opis** | Wprowadź **wystąpienie dla chłodzeniaer-01,3**. |
     | **Hierarchies** | Wybierz **hierarchię lokalizacji**. |
     | **Country** | Wpisz **USA**. |
     | **City** | Wpisz **New York**. |
     | **Building** | Wpisz **Empire State Building**. |

1. Wybierz kartę **Analiza** , a następnie Odśwież stronę. W obszarze **Hierarchia lokalizacji**rozwiń węzeł wszystkie poziomy hierarchii, aby wyświetlić wystąpienia szeregów czasowych:

   [![Karta Analiza](media/v2-update-provision/define-twelve.png)](media/v2-update-provision/define-twelve.png#lightbox)

1. Aby poznać wystąpienia szeregów czasowych w ciągu ostatniej godziny, Zmień **szybkie czasy** na **ostatnią godzinę**:

    [![Pole szybkie czasy z wybraną ostatnią godziną](media/v2-update-provision/define-thirteen-explore.png)](media/v2-update-provision/define-thirteen-explore.png#lightbox)

1. W obszarze **centrum nauki pacyficznego**wybierz wystąpienie szeregów czasowych, a następnie wybierz pozycję **Pokaż maksymalną wilgotność**.

    [![Wybrane wystąpienie szeregów czasowych i wybór menu Pokaż maksymalną wilgotność](media/v2-update-provision/define-fourteen-show-max.png)](media/v2-update-provision/define-fourteen-show-max.png#lightbox)

1. Zostanie otwarta seria czasu dla **maksymalnej wilgotności** z rozmiarem interwału wynoszącym **1 minutę** . Aby odfiltrować zakres, wybierz region. Aby analizować zdarzenia w przedziale czasowym, kliknij wykres prawym przyciskiem myszy, a następnie wybierz polecenie **powiększenie**:

   [![Zaznaczony zakres z poleceniem zoom w menu skrótów](media/v2-update-provision/define-fifteen-filter.png)](media/v2-update-provision/define-fifteen-filter.png#lightbox)

1. Aby wyświetlić szczegóły zdarzenia, wybierz region, a następnie kliknij prawym przyciskiem myszy wykres:

   [![Szczegółowa lista zdarzeń](media/v2-update-provision/define-eighteen.png)](media/v2-update-provision/define-eighteen.png#lightbox)


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po ukończeniu samouczka Wyczyść utworzone zasoby:

1. W menu po lewej stronie w [Azure Portal](https://portal.azure.com)wybierz pozycję **wszystkie zasoby**, Znajdź Azure Time Series Insights grupę zasobów.
1. Usuń całą grupę zasobów (i wszystkie znajdujące się w niej zasoby), wybierając pozycję **Usuń** lub Usuń osobno każdy zasób.

## <a name="next-steps"></a>Następne kroki

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
> [Usługi Azure storage czasu Series Insights w wersji zapoznawczej i transferu danych przychodzących](./time-series-insights-update-storage-ingress.md)

Dowiedz się więcej o modelach szeregów czasowych:

> [!div class="nextstepaction"]
> [Modelowanie danych w usłudze Azure Time Series Insights w wersji zapoznawczej](./time-series-insights-update-tsm.md)
