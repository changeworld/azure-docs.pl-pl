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
ms.date: 06/18/2019
ms.custom: seodec18
ms.openlocfilehash: 824d24b97f192583a42192b3bb90eb1818e1aa18
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67272983"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Samouczek: Konfigurowanie środowiska wersji zapoznawczej usługi Azure Time Series Insights

Ten samouczek zawiera instrukcje tworzenia środowiska wersji zapoznawczej usługi Azure Time Series Insights dostępnej w modelu płatności zgodnie z rzeczywistym użyciem (PAYG). 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

* Tworzenie środowiska wersji zapoznawczej usługi Azure Time Series Insights.
* Łączenie środowiska wersji zapoznawczej usługi Azure Time Series Insights z centrum zdarzeń w usłudze Azure Event Hubs.
* Uruchamianie przykładowego akceleratora rozwiązań umożliwiającego strumieniowe przesyłanie danych do środowiska wersji zapoznawczej usługi Azure Time Series Insights.
* Wykonywanie podstawowej analizy danych.
* Definiowanie hierarchii oraz typu modelu szeregów czasowych i kojarzenie ich z wystąpieniami.

>[!TIP]
> [Akceleratory rozwiązań IoT](https://www.azureiotsolutions.com/Accelerators) udostępniają korporacyjnej wstępnie skonfigurowanych rozwiązań, które umożliwia przyspieszenie rozwoju niestandardowych rozwiązań IoT.

## <a name="create-a-device-simulation"></a>Tworzenie symulacji urządzeń

W tej sekcji utworzysz trzy symulowane urządzenia, które wysyłają dane do wystąpienia usługi Azure IoT Hub.

1. Przejdź do [strony akceleratorów rozwiązań Azure IoT](https://www.azureiotsolutions.com/Accelerators). Na stronie będzie widocznych kilka gotowych przykładów. Zaloguj się przy użyciu konta platformy Azure. Następnie wybierz pozycję **Symulacja urządzenia**.

   [![Strona akceleratorów rozwiązań w usłudze Azure IoT](media/v2-update-provision/device-one-accelerator.png)](media/v2-update-provision/device-one-accelerator.png#lightbox)

   Wybierz pozycję **Wypróbuj teraz**.

1. Na **rozwiązania tworzenia symulacji urządzenia** ustaw następujące parametry:

    | Parametr | Akcja |
    | --- | --- |
    | **Nazwa wdrożenia** | Wprowadź unikatową wartość dla nowej grupy zasobów. Znajdujące się na liście zasoby platformy Azure są tworzone i przypisywane do grupy zasobów. |
    | **Subskrypcja platformy Azure** | Wybierz subskrypcję, której użyto do utworzenia środowiska usługi Time Series Insights. |
    | **Lokalizacja platformy Azure** | Wybierz region, który został użyty do utworzenia środowiska usługi Time Series Insights. |
    | **Opcje wdrażania** | Wybierz **Aprowizować nowe Centrum IoT Hub**. |
 
    Wybierz **tworzenie rozwiązania**. Może potrwać do 20 minut do zakończenia wdrażania rozwiązania.

    [![Utwórz stronę rozwiązania symulacji urządzenia](media/v2-update-provision/device-two-create.png)](media/v2-update-provision/device-two-create.png#lightbox)

## <a name="create-a-time-series-insights-preview-payg-environment"></a>Tworzenie środowiska wersji zapoznawczej usługi Time Series Insights dostępnej w modelu PAYG

W tej sekcji opisano sposób tworzenia środowiska Azure czas Series Insights w wersji zapoznawczej i podłącz go do usługi IoT hub, utworzone za pomocą akceleratora rozwiązań IoT [witryny Azure portal](https://portal.azure.com/).

1. Zaloguj się do witryny Azure Portal przy użyciu konta subskrypcji.

1. Wybierz **Utwórz zasób** > **Internet of Things** > **usługi Time Series Insights**.

   [![Wybierz Internetu rzeczy, a następnie wybierz usługi Time Series Insights](media/v2-update-provision/payg-one-azure.png)](media/v2-update-provision/payg-one-azure.png#lightbox)

1. W **środowisko tworzenia Time Series Insights** okienku na **podstawy** kartę, ustaw następujące parametry:

    | Parametr | Akcja |
    | --- | ---|
    | **Nazwa środowiska** | Wprowadź unikatową nazwę dla środowiska Azure czas Series Insights w wersji zapoznawczej. |
    | **Subskrypcja** | Wprowadź subskrypcję, w którym chcesz utworzyć środowisko Azure czas Series Insights w wersji zapoznawczej. Najlepiej jest używać tej samej subskrypcji, jak w pozostałych zasobów IoT, które są tworzone przez symulator urządzenia. |
    | **Grupa zasobów** | Wybierz istniejącą grupę zasobów lub Utwórz nową grupę zasobów dla zasobu środowiska Azure czas Series Insights w wersji zapoznawczej. Grupa zasobów jest kontenerem zasobów platformy Azure. Najlepszym rozwiązaniem jest, aby użyć tej samej grupie zasobów co inne zasoby IoT, które są tworzone przez symulator urządzenia. |
    | **Location** | Wybierz region centrum danych w środowisku Azure czas Series Insights w wersji zapoznawczej. Aby uniknąć dodatkowych opóźnień, najlepiej utworzyć środowiska Azure czas Series Insights w wersji zapoznawczej w tym samym regionie, co inne zasoby IoT. |
    | **Warstwa** |  Wybierz **PAYG** (*płatność za rzeczywiste użycie*). Jest to jednostka SKU produktu Azure Time Series Insights w wersji zapoznawczej. |
    | **Identyfikator właściwości** | Wprowadź wartość, która jednoznacznie identyfikuje wystąpienie serii czasu. Wartość wprowadzoną w **identyfikator właściwości** pole jest niezmienny. Nie można zmienić go później. Na potrzeby tego samouczka wprowadź **iothub-— urządzenia — identyfikator połączenia**. Aby dowiedzieć się więcej o identyfikatorze serii czasu, zobacz [najlepsze rozwiązania dotyczące wybierania Identyfikatora serii czasu](./time-series-insights-update-how-to-id.md). |
    | **Nazwa konta magazynu** | Wprowadź globalnie unikatową nazwę dla nowego konta magazynu do utworzenia. |
   
   Wybierz opcję **Dalej: Źródło zdarzeń**.

   [![W okienku tworzenia środowiska usługi Time Series Insights](media/v2-update-provision/payg-two-create.png)](media/v2-update-provision/payg-two-create.png#lightbox)

1. Na **źródła zdarzeń** kartę, ustaw następujące parametry:

   | Parametr | Akcja |
   | --- | --- |
   | **Utworzyć źródło zdarzeń?** | Wybierz **tak**.|
   | **Nazwa** | Wprowadź unikatową wartość dla nazwy źródła zdarzeń. |
   | **Typ źródła** | Wybierz **usługi IoT Hub**. |
   | **Wybierz koncentrator** | Wybierz **wybierz istniejący**. |
   | **Subskrypcja** | Wybierz subskrypcję, która została użyta dla symulator urządzenia. |
   | **Nazwa centrum IoT** | Wybierz nazwę Centrum IoT utworzonego symulator urządzenia. |
   | **Zasady dostępu do centrum IoT Hub** | Wybierz pozycję **iothubowner**. |
   | **Grupy konsumentów usługi IoT Hub** | Wybierz pozycję **Nowa**, wpisz unikatową nazwę, a następnie wybierz polecenie **Dodaj**. Grupy konsumentów musi mieć unikatową wartość w wersji zapoznawczej Azure czas serii szczegółowych informacji. |
   | **Właściwość znacznika czasu** | Ta wartość jest używana do identyfikowania **sygnatura czasowa** właściwości usługi w przychodzących danych telemetrycznych. W tym samouczku pozostaw to pole puste. Ten symulator używa sygnatury czasowej pochodzącej z usługi IoT Hub, domyślnej wartości dla usługi Time Series Insights. |

   Wybierz pozycję **Przegląd + utwórz**.

   [![Konfigurowanie źródła zdarzeń](media/v2-update-provision/payg-five-event-source.png)](media/v2-update-provision/payg-five-event-source.png#lightbox)

1. Na **przeglądanie + tworzenie** kartę Przejrzyj wybrane opcje, a następnie wybierz **Utwórz**.

    [![Przeglądanie + Tworzenie strony, przy użyciu przycisku Utwórz](media/v2-update-provision/payg-six-review.png)](media/v2-update-provision/payg-six-review.png#lightbox)

    Można wyświetlić stan wdrożenia:

    [![Powiadomienie, że wdrożenie jest ukończone](media/v2-update-provision/payg-seven-deploy.png)](media/v2-update-provision/payg-seven-deploy.png#lightbox)

1. Masz dostęp do środowiska Azure czas Series Insights w wersji zapoznawczej, jeśli jesteś właścicielem dzierżawy. Aby to sprawdzić:

   1. Wyszukaj grupy zasobów, a następnie wybierz środowiska Azure czas Series Insights w wersji zapoznawczej:

      [![Wybrane środowisko](media/v2-update-provision/payg-eight-environment.png)](media/v2-update-provision/payg-eight-environment.png#lightbox)

   1. Na stronie Azure czas Series Insights w wersji zapoznawczej wybierz **zasady dostępu do danych**:

      [![Zasady dostępu do danych](media/v2-update-provision/payg-nine-data-access.png)](media/v2-update-provision/payg-nine-data-access.png#lightbox)

   1. Sprawdź, czy poświadczenia są wyświetlane:

      [![Poświadczenia listy](media/v2-update-provision/payg-ten-verify.png)](media/v2-update-provision/payg-ten-verify.png#lightbox)

   Jeśli poświadczenia nie są wyświetlane, należy przyznać sobie uprawnień do dostępu do środowiska. Aby dowiedzieć się więcej o ustawianiu uprawnień, zobacz [Grant data access (Przyznawanie dostępu do danych)](./time-series-insights-data-access.md).

## <a name="stream-data-into-your-environment"></a>Stream data do środowiska

1. Przejdź z powrotem do [stronie akceleratorów rozwiązania Azure IoT](https://www.azureiotsolutions.com/Accelerators). Zlokalizuj swoje rozwiązanie na pulpicie nawigacyjnym akcelerator rozwiązań. Następnie wybierz **Uruchom**:

    [![Uruchom rozwiązanie symulacji urządzenia](media/v2-update-provision/device-three-launch.png)](media/v2-update-provision/device-three-launch.png#lightbox)

1. Nastąpi przekierowanie do strony **Symulacja urządzeń Microsoft Azure IoT**. W prawym górnym rogu strony wybierz **nowe symulacji**.

    [![Strona symulacji w usłudze Azure IoT](media/v2-update-provision/device-four-iot-sim-page.png)](media/v2-update-provision/device-four-iot-sim-page.png#lightbox)

1. W **Instalatora symulacji** okienko, ustaw następujące parametry:

    | Parametr | Akcja |
    | --- | --- |
    | **Nazwa** | Wprowadź unikatową nazwę symulatora. |
    | **Opis** | Wprowadź definicję. |
    | **Czas trwania symulacji** | Wybierz opcję **Uruchamianie bez ograniczeń**. |
    | **Model urządzenia** | **Nazwa**: Wpisz **Chiller** (chłodnia). <br />**Ilość**: Wpisz **3**. |
    | **Docelowa usługa IoT Hub** | Wybierz pozycję **Użyj dostarczonej wstępnie usługi IoT Hub**. |

    [![Parametry można ustawić](media/v2-update-provision/device-five-params.png)](media/v2-update-provision/device-five-params.png#lightbox)

    Wybierz **Rozpocznij symulację**.

    Na pulpicie nawigacyjnym symulacji urządzenia należy pamiętać, informacje wyświetlane dla **aktywnych urządzeń** i **komunikatów na sekundę**.

    [![Pulpit nawigacyjny symulacji usługi Azure IoT](media/v2-update-provision/device-seven-dashboard.png)](media/v2-update-provision/device-seven-dashboard.png#lightbox)

## <a name="analyze-data-in-your-environment"></a>Analizowanie danych w środowisku

W tej sekcji przeprowadzimy podstawową analizę danych szeregów czasowych przy użyciu [eksploratora wersji zapoznawczej usługi Azure Time Series Insights](./time-series-insights-update-explorer.md).

1. Przejdź do eksploratora wersji zapoznawczej usługi Azure Time Series Insights, wybierając adres URL na stronie zasobu w witrynie [Azure Portal](https://portal.azure.com/).

    [![Adres URL explorer czasu Series Insights w wersji zapoznawczej](media/v2-update-provision/analyze-one-portal.png)](media/v2-update-provision/analyze-one-portal.png#lightbox)

1. W Eksploratorze, wybierz **wystąpienia serii czasu** węzeł, aby wyświetlić wszystkie wystąpienia Azure czas Series Insights w wersji zapoznawczej w środowisku.

    [![Lista wystąpień bez elementów nadrzędnych](media/v2-update-provision/analyze-two-unparented.png)](media/v2-update-provision/analyze-two-unparented.png#lightbox)

1. Wybierz pozycję pierwszego wystąpienia serii czasu. Następnie wybierz **ciśnienie**.

    [![Wybrane wystąpienie serii czasu za pomocą polecenia menu, aby pokazać średnie wykorzystanie](media/v2-update-provision/analyze-three-show-pressure.png)](media/v2-update-provision/analyze-three-show-pressure.png#lightbox)

    Pojawi się wykres serii czasu. Zmiana **interwał** do **15 s**.

    [![Czas serii wykresu](media/v2-update-provision/analyze-four-chart.png)](media/v2-update-provision/analyze-four-chart.png#lightbox)

1. Powtórz krok 3 z pozostałe dwa wystąpienia serii czasu. Możesz wyświetlić wszystkie wystąpienia serii czasu, jak pokazano na tym wykresie:

    [![Wykres wszystkie Szeregi czasowe](media/v2-update-provision/analyze-five-chart.png)](media/v2-update-provision/analyze-five-chart.png#lightbox)

1. W **przedział czasu** opcji pola, zmień zakres czasu, aby zobaczyć trendy serii czasu w ciągu ostatniej godziny:

    [![Ustaw zakres czasu do godziny](media/v2-update-provision/analyze-six-time.png)](media/v2-update-provision/analyze-six-time.png#lightbox)

## <a name="define-and-apply-a-model"></a>Definiowanie i stosowanie modelu

W tej sekcji zastosujesz model, aby ustrukturyzować dane. Do utworzenia modelu potrzebne są definicje typów, hierarchii i wystąpień. Aby dowiedzieć się więcej na temat modelowania danych, zobacz [modelu szeregów czasowych](./time-series-insights-update-tsm.md).

1. W eksploratorze wybierz kartę **Model**:

   [![Karta modelu w Eksploratorze](media/v2-update-provision/define-one-model.png)](media/v2-update-provision/define-one-model.png#lightbox)

1. Wybierz **Dodaj** Aby dodać typ:

   [![Przycisk Dodaj, dla typów](media/v2-update-provision/define-two-add.png)](media/v2-update-provision/define-two-add.png#lightbox)

1. Następnie zdefiniuj trzech zmiennych typu: *wykorzystanie*, *temperatury*, i *wilgotności*. W **Dodaj typ** okienko, ustaw następujące parametry:

    | Parametr | Akcja |
    | --- | ---|
    | **Nazwa** | Wpisz **Chiller** (chłodnia). |
    | **Opis** | Wpisz **This is a type definition of Chiller** (To jest definicja typu dla chłodni). |

   * Aby zdefiniować *wykorzystanie*w obszarze **zmienne**, ustaw następujące parametry:

     | Parametr | Akcja |
     | --- | ---|
     | **Nazwa** | Wpisz **Avg Pressure** (Ciśnienie średnie). |
     | **Wartość** | Wybierz opcję **pressure (Double)** . Może upłynąć kilka minut **wartość** wypełniona automatycznie po uruchomieniu Azure czas Series Insights w wersji zapoznawczej, odbierania zdarzeń. |
     | **Operacja agregacji** | Wybierz opcję **AVG** (średnia). |

      [![Opcje do definiowania ciśnienia](media/v2-update-provision/define-three-variable.png)](media/v2-update-provision/define-three-variable.png#lightbox)

      Aby dodać zmienną dalej, wybierz **Dodaj zmienną**.

   * Zdefiniuj *temperatury*:

     | Parametr | Akcja |
     | --- | ---|
     | **Nazwa** | Wpisz **Avg Temperature** (Temperatura średnia). |
     | **Wartość** | Wybierz opcję **temperature (Double)** . Może upłynąć kilka minut **wartość** wypełniona automatycznie po uruchomieniu Azure czas Series Insights w wersji zapoznawczej, odbierania zdarzeń. |
     | **Operacja agregacji** | Wybierz opcję **AVG** (średnia).|

      [![Opcje określania temperatury](media/v2-update-provision/define-four-avg.png)](media/v2-update-provision/define-four-avg.png#lightbox)

      Aby dodać zmienną dalej, wybierz **Dodaj zmienną**.

   * Zdefiniuj *wilgotności*:

      | | |
      | --- | ---|
      | **Nazwa** | Wprowadź **maksymalna wilgotność** |
      | **Wartość** | Wybierz opcję **humidity (Double)** . Może upłynąć kilka minut **wartość** wypełniona automatycznie po uruchomieniu Azure czas Series Insights w wersji zapoznawczej, odbierania zdarzeń. |
      | **Operacja agregacji** | Wybierz **MAX**.|

      [![Opcje określania temperatury](media/v2-update-provision/define-five-humidity.png)](media/v2-update-provision/define-five-humidity.png#lightbox)

    Wybierz pozycję **Utwórz**.

    Możesz zobaczyć typie, który został dodany:

    [![Informacje o typie dodano](media/v2-update-provision/define-six-type.png)](media/v2-update-provision/define-six-type.png#lightbox)

1. Następnym krokiem jest dodanie hierarchii. W obszarze **hierarchie**, wybierz opcję **Dodaj**:

    [![Karta Hierarchie za pomocą przycisku Dodaj](media/v2-update-provision/define-seven-hierarchy.png)](media/v2-update-provision/define-seven-hierarchy.png#lightbox)

1. W **Edytuj hierarchię** okienko, ustaw następujące parametry:

   | Parametr | Akcja |
   | --- | ---|
   | **Nazwa** | Wpisz **Location Hierarchy** (Hierarchia lokalizacji). |
   | **Level 1** | Wpisz **Country** (Kraj). |
   | **Level 2** | Wpisz **City** (Miasto). |
   | **Level 3** | Wpisz **Building** (Budynek). |

   Wybierz pozycję **Zapisz**.

    [![Pola hierarchii przy użyciu przycisku Utwórz](media/v2-update-provision/define-eight-add-hierarchy.png)](media/v2-update-provision/define-eight-add-hierarchy.png#lightbox)

   Zobaczysz utworzoną hierarchię:

    [![Informacje dotyczące hierarchii](media/v2-update-provision/define-nine-created.png)](media/v2-update-provision/define-nine-created.png#lightbox)

1. Wybierz **wystąpień**. Wybierz pierwsze wystąpienie, a następnie wybierz **Edytuj**:

    [![Wybierając przycisk edycji wystąpienia](media/v2-update-provision/define-ten-edit.png)](media/v2-update-provision/define-ten-edit.png#lightbox)

1. W **edytowania wystąpień** okienko, ustaw następujące parametry:

    | Parametr | Akcja |
    | --- | --- |
    | **Typ** | Wybierz pozycję **Chiller** (Chłodnia). |
    | **Opis** | Wpisz **Instance for Chiller-01.1** (Wystąpienie dla urządzenia Chiller-01.1). |
    | **Hierarchies** | Wybierz **hierarchii lokalizacji**. |
    | **Country** | Wpisz **USA**. |
    | **City** | Wpisz **Seattle**. |
    | **Building** | Wpisz **Space Needle**. |

    [![Pola wystąpienia z przyciskiem Zapisz](media/v2-update-provision/define-eleven-chiller.png)](media/v2-update-provision/define-eleven-chiller.png#lightbox)

   Wybierz pozycję **Zapisz**.

1. Powtórz poprzedni krok dla innych czujników. Zaktualizuj następujące wartości:

   * Dla chłodni Chiller 01.2:

     | Parametr | Akcja |
     | --- | --- |
     | **Typ** | Wybierz pozycję **Chiller** (Chłodnia). |
     | **Opis** | Wpisz **Instance for Chiller-01.2** (Wystąpienie dla urządzenia Chiller-01.2). |
     | **Hierarchies** | Wybierz **hierarchii lokalizacji**. |
     | **Country** | Wpisz **USA**. |
     | **City** | Wpisz **Seattle**. |
     | **Building** | Wpisz **Pacific Science Center**. |

   * Dla chłodni Chiller 01.3:

     | Parametr | Akcja |
     | --- | --- |
     | **Typ** | Wybierz pozycję **Chiller** (Chłodnia). |
     | **Opis** | Wprowadź **wystąpienie 01.3 Chłodnica**. |
     | **Hierarchies** | Wybierz **hierarchii lokalizacji**. |
     | **Country** | Wpisz **USA**. |
     | **City** | Wpisz **New York**. |
     | **Building** | Wpisz **Empire State Building**. |

1. Wybierz **analizy** kartę, a następnie odśwież stronę. W obszarze **hierarchii lokalizacji**, rozwiń wszystkie poziomy hierarchii, aby wyświetlić wystąpienia serii czasu:

   [![Na karcie analiza](media/v2-update-provision/define-twelve.png)](media/v2-update-provision/define-twelve.png#lightbox)

1. Aby poznać wystąpienia serii czasu w ciągu ostatniej godziny, należy zmienić **krótkie okresy** do **Ostatnia godzina**:

    [![Pole krótkie okresy z ostatniej godziny wybrana](media/v2-update-provision/define-thirteen-explore.png)](media/v2-update-provision/define-thirteen-explore.png#lightbox)

1. W obszarze **Centrum analizy pacyficznego**, wybierz wystąpienie serii czasu, a następnie wybierz **Pokaż wilgotność maksymalna**.

    [![Wystąpienia serii wybrana wartość czasu i wybór menu Pokaż maksymalna wilgotność](media/v2-update-provision/define-fourteen-show-max.png)](media/v2-update-provision/define-fourteen-show-max.png#lightbox)

1. Serie czasu dla **maksymalna wilgotność** o rozmiarze interwał **1 minutę** zostanie otwarty. Aby filtrować zakres, wybierz region. Aby analizować zdarzenia w przedziale czasu, kliknij prawym przyciskiem myszy wykres, a następnie wybierz **powiększenia**:

   [![Wybrany zakres przy użyciu polecenia powiększenia w menu skrótów](media/v2-update-provision/define-fifteen-filter.png)](media/v2-update-provision/define-fifteen-filter.png#lightbox)

1. Aby wyświetlić szczegóły zdarzenia, wybierz region, a następnie kliknij prawym przyciskiem myszy wykres:

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
> [Usługi Azure storage czasu Series Insights w wersji zapoznawczej i transferu danych przychodzących](./time-series-insights-update-storage-ingress.md)

Dowiedz się więcej o modelach szeregów czasowych:

> [!div class="nextstepaction"]
> [Modelowanie danych w usłudze Azure Time Series Insights w wersji zapoznawczej](./time-series-insights-update-tsm.md)
