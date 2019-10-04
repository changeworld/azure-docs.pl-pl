---
title: 'Samouczek: Konfigurowanie środowiska Azure Time Series Insights w wersji zapoznawczej | Microsoft Docs'
description: Dowiedz się, jak skonfigurować środowisko w wersji zapoznawczej Azure Time Series Insights.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 10/02/2019
ms.custom: seodec18
ms.openlocfilehash: e680652d490d75701780e2e3618a43451bfe2819
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71845101"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Samouczek: Konfigurowanie środowiska Azure Time Series Insights w wersji zapoznawczej

Ten samouczek przeprowadzi Cię przez proces tworzenia środowiska Azure Time Series Insights w wersji zapoznawczej płatność zgodnie z rzeczywistym użyciem. 

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Utwórz środowisko Azure Time Series Insights w wersji zapoznawczej.
> * Podłącz środowisko Azure Time Series Insights w wersji zapoznawczej do centrum zdarzeń w usłudze Azure Event Hubs.
> * Uruchom przykład akceleratora rozwiązania, aby przesłać strumieniowo dane do środowiska Azure Time Series Insights w wersji zapoznawczej.
> * Wykonaj podstawową analizę danych.
> * Zdefiniuj typ i hierarchię szeregów czasowych, a następnie skojarz ją z wystąpieniami.

>[!TIP]
> [Akceleratory rozwiązań IoT](https://www.azureiotsolutions.com/Accelerators) zapewniają wstępnie skonfigurowane rozwiązania klasy korporacyjnej, których można użyć do przyspieszenia tworzenia niestandardowych rozwiązań IoT.

Utwórz konto [bezpłatnej subskrypcji platformy Azure](https://azure.microsoft.com/free/) , jeśli jeszcze jej nie masz.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto logowania platformy Azure musi być również członkiem roli **właściciela** subskrypcji. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem przy użyciu kontroli dostępu opartej na rolach i Azure Portal](../role-based-access-control/role-assignments-portal.md).

## <a name="create-a-device-simulation"></a>Tworzenie symulacji urządzenia

W tej sekcji utworzysz trzy symulowane urządzenia, które wysyłają dane do wystąpienia usługi Azure IoT Hub.

1. Przejdź do [strony Akceleratory rozwiązań usługi Azure IoT](https://www.azureiotsolutions.com/Accelerators). Na stronie wyświetlane są kilka wstępnie skompilowanych przykładów. Zaloguj się przy użyciu konta platformy Azure. Następnie wybierz opcję **symulacja urządzenia**.

   [@no__t — Strona akceleratorów rozwiązań IoT 1Azure](media/v2-update-provision/device-one-accelerator.png)](media/v2-update-provision/device-one-accelerator.png#lightbox)

   Wybierz pozycję **Wypróbuj teraz**.

1. Na stronie **Tworzenie rozwiązania symulacji urządzenia** ustaw następujące parametry:

    | Parametr | Akcja |
    | --- | --- |
    | **Nazwa wdrożenia** | Wprowadź unikatową wartość nowej grupy zasobów. Wymienione zasoby platformy Azure są tworzone i przypisywane do grupy zasobów. |
    | **Subskrypcja platformy Azure** | Wybierz subskrypcję, która została użyta do utworzenia środowiska Time Series Insightsowego. |
    | **Lokalizacja platformy Azure** | Wybierz region, który został użyty do utworzenia środowiska Time Series Insightsowego. |
    | **Opcje wdrażania** | Wybierz pozycję **Udostępnij nowe IoT Hub**. |
 
    Wybierz pozycję **Utwórz rozwiązanie**. Ukończenie wdrażania rozwiązania może potrwać do 20 minut.

    [Strona rozwiązania symulacji urządzenia @no__t 1Create](media/v2-update-provision/device-two-create.png)](media/v2-update-provision/device-two-create.png#lightbox)

## <a name="create-a-preview-payg-environment"></a>Tworzenie środowiska w wersji zapoznawczej PAYG

W tej sekcji opisano sposób tworzenia środowiska Azure Time Series Insights w wersji zapoznawczej i łączenia go z usługą IoT Hub utworzoną przez Akcelerator rozwiązania IoT przy użyciu [Azure Portal](https://portal.azure.com/).

1. Zaloguj się do Azure Portal przy użyciu konta subskrypcji.

1. Wybierz pozycję **Utwórz zasób** > **Internet rzeczy** > **Time Series Insights**.

   [![Select Internet rzeczy, a następnie wybierz pozycję Time Series Insights](media/v2-update-provision/payg-one-azure.png)](media/v2-update-provision/payg-one-azure.png#lightbox)

1. W okienku **Tworzenie środowiska Time Series Insights** na karcie **podstawowe** ustaw następujące parametry:

    | Parametr | Akcja |
    | --- | ---|
    | **Nazwa środowiska** | Wprowadź unikatową nazwę środowiska Azure Time Series Insights w wersji zapoznawczej. |
    | **Ramach** | Wprowadź subskrypcję, w której chcesz utworzyć środowisko Azure Time Series Insights w wersji zapoznawczej. Najlepszym rozwiązaniem jest użycie tej samej subskrypcji jako reszty zasobów IoT, które są tworzone przez symulatora urządzeń. |
    | **Grupa zasobów** | Wybierz istniejącą grupę zasobów lub Utwórz nową grupę zasobów dla zasobu środowiska Azure Time Series Insights wersji zapoznawczej. Grupa zasobów to kontener zasobów platformy Azure. Najlepszym rozwiązaniem jest użycie tej samej grupy zasobów co inne zasoby IoT, które są tworzone przez symulatora urządzeń. |
    | **Lokalizacja** | Wybierz region centrum danych dla środowiska Azure Time Series Insights w wersji zapoznawczej. Aby uniknąć dodatkowego opóźnienia, najlepszym rozwiązaniem jest utworzenie środowiska Azure Time Series Insights w wersji zapoznawczej w tym samym regionie, w którym znajduje się inne zasoby IoT. |
    | **Warstwy** |  Wybierz pozycję **PAYG** (*płatność zgodnie z rzeczywistym*użyciem). Jest to jednostka SKU dla produktu Azure Time Series Insights w wersji zapoznawczej. |
    | **Identyfikator właściwości** | Wprowadź wartość, która jednoznacznie identyfikuje wystąpienie szeregów czasowych. Wartość wprowadzona w polu **Identyfikator właściwości** jest niezmienna. Nie można go zmienić później. Na potrzeby tego samouczka wprowadź **iothub-Connection-Device-ID**. Aby dowiedzieć się więcej na temat identyfikatora szeregów czasowych, zobacz [najlepsze rozwiązania dotyczące wybierania identyfikatora szeregów czasowych](./time-series-insights-update-how-to-id.md). |
    | **Nazwa konta magazynu** | Wprowadź globalnie unikatową nazwę nowego konta magazynu, które ma zostać utworzone. |
   
   Wybierz pozycję **Dalej: Źródło zdarzenia**.

   [![Pane do tworzenia środowiska Time Series Insights](media/v2-update-provision/payg-two-create.png)](media/v2-update-provision/payg-two-create.png#lightbox)

1. Na karcie **Źródło zdarzenia** ustaw następujące parametry:

   | Parametr | Akcja |
   | --- | --- |
   | **Utworzyć źródło zdarzenia?** | Wybierz pozycję **tak**.|
   | **Nazwa** | Wprowadź unikatową wartość dla nazwy źródła zdarzenia. |
   | **Typ źródła** | Wybierz **IoT Hub**. |
   | **Wybierz centrum** | Wybierz **pozycję Wybierz istniejące**. |
   | **Ramach** | Wybierz subskrypcję używaną dla symulatora urządzeń. |
   | **Nazwa IoT Hub** | Wybierz nazwę usługi IoT Hub utworzoną dla symulatora urządzeń. |
   | **Zasady dostępu IoT Hub** | Wybierz pozycję **iothubowner**. |
   | **Grupa konsumentów IoT Hub** | Wybierz pozycję **Nowy**, wprowadź unikatową nazwę, a następnie wybierz pozycję **Dodaj**. Grupa konsumentów musi być unikatowa w wersji zapoznawczej Azure Time Series Insights. |
   | **Właściwość timestamp** | Ta wartość służy do identyfikowania właściwości **timestamp** w przychodzących danych telemetrycznych. Na potrzeby tego samouczka pozostaw to pole puste. Ten symulator używa przychodzącej sygnatury czasowej z IoT Hub, który Time Series Insights wartością domyślną. |

   Wybierz pozycję **Recenzja + Utwórz**.

   [@no__t — 1Configure źródła zdarzeń](media/v2-update-provision/payg-five-event-source.png)](media/v2-update-provision/payg-five-event-source.png#lightbox)

1. Na karcie **Przegląd + tworzenie** Przejrzyj wybrane opcje, a następnie wybierz pozycję **Utwórz**.

    [![Review + Utwórz stronę z przyciskiem Utwórz](media/v2-update-provision/payg-six-review.png)](media/v2-update-provision/payg-six-review.png#lightbox)

    Możesz zobaczyć stan wdrożenia:

    [@no__t — 1Notification ukończenie wdrożenia](media/v2-update-provision/payg-seven-deploy.png)](media/v2-update-provision/payg-seven-deploy.png#lightbox)

1. Jeśli jesteś posiadaczem dzierżawy, masz dostęp do środowiska Azure Time Series Insights w wersji zapoznawczej. Aby upewnić się, że masz dostęp:

   1. Wyszukaj grupę zasobów, a następnie wybierz środowisko Azure Time Series Insights w wersji zapoznawczej:

      [@no__t — środowisko 1Selected](media/v2-update-provision/payg-eight-environment.png)](media/v2-update-provision/payg-eight-environment.png#lightbox)

   1. Na stronie Podgląd Azure Time Series Insights wybierz pozycję **zasady dostępu do danych**:

      [zasady dostępu @no__t 1Data](media/v2-update-provision/payg-nine-data-access.png)](media/v2-update-provision/payg-nine-data-access.png#lightbox)

   1. Sprawdź, czy na liście znajdują się Twoje poświadczenia:

      [poświadczenia @no__t 1Listed](media/v2-update-provision/payg-ten-verify.png)](media/v2-update-provision/payg-ten-verify.png#lightbox)

   Jeśli Twoje poświadczenia nie znajdują się na liście, musisz udzielić sobie uprawnień dostępu do środowiska. Aby dowiedzieć się więcej o ustawianiu uprawnień, Odczytaj pozycję [Udziel dostępu do danych](./time-series-insights-data-access.md).

## <a name="stream-data"></a>Przesyłanie strumieniowe danych

Teraz, gdy wdrożono środowisko Time Series Insights, przesyłaj strumieniowo dane do analizy.

1. Przejdź z powrotem do [strony akceleratorów rozwiązań usługi Azure IoT](https://www.azureiotsolutions.com/Accelerators). Znajdź swoje rozwiązanie na pulpicie nawigacyjnym akceleratora rozwiązania. Następnie wybierz pozycję **Uruchom**:

    [![Launch rozwiązanie do symulacji urządzeń](media/v2-update-provision/device-three-launch.png)](media/v2-update-provision/device-three-launch.png#lightbox)

1. Nastąpi przekierowanie do strony **symulacji urządzenia Microsoft Azure IoT** . W prawym górnym rogu strony wybierz pozycję **Nowa symulacja**.

    [Strona symulacji IoT @no__t 1Azure](media/v2-update-provision/device-four-iot-sim-page.png)](media/v2-update-provision/device-four-iot-sim-page.png#lightbox)

1. W okienku **Konfiguracja symulacji** ustaw następujące parametry:

    | Parametr | Akcja |
    | --- | --- |
    | **Nazwa** | Wprowadź unikatową nazwę symulatora. |
    | **Opis** | Wprowadź definicję. |
    | **Czas trwania symulacji** | Ustawiony do **uruchamiania na czas nieokreślony**. |
    | **Model urządzenia** | **Nazwa**: wprowadź **chłodzenie**. <br />**Kwota**: wprowadź wartość **3**. |
    | **IoT Hub docelowe** | Ustaw, aby **użyć wstępnie zainicjowanej IoT Hub**. |

    [![Parameters do ustawienia](media/v2-update-provision/device-five-params.png)](media/v2-update-provision/device-five-params.png#lightbox)

    Wybierz pozycję **Rozpocznij symulację**.

    Na pulpicie nawigacyjnym symulacji urządzenia Zwróć uwagę na informacje wyświetlane dla **aktywnych urządzeń** i **komunikatów na sekundę**.

    [Pulpit nawigacyjny symulacji IoT @no__t 1Azure](media/v2-update-provision/device-seven-dashboard.png)](media/v2-update-provision/device-seven-dashboard.png#lightbox)

## <a name="analyze-data"></a>Analizowanie danych

W tej sekcji przeprowadzasz podstawową analizę danych szeregów czasowych przy użyciu [eksploratora Azure Time Series Insights Preview](./time-series-insights-update-explorer.md).

1. Przejdź do Eksploratora Azure Time Series Insights w wersji zapoznawczej, wybierając adres URL ze strony zasobów w [Azure Portal](https://portal.azure.com/).

    [@no__t — adres URL Eksploratora 1The Time Series Insights w wersji zapoznawczej](media/v2-update-provision/analyze-one-portal.png)](media/v2-update-provision/analyze-one-portal.png#lightbox)

1. W Eksploratorze wybierz węzeł **wystąpienia szeregów czasowych** , aby zobaczyć wszystkie Azure Time Series Insights wystąpienia w wersji zapoznawczej w środowisku.

    [![List wystąpień nienadrzędnych](media/v2-update-provision/analyze-two-unparented.png)](media/v2-update-provision/analyze-two-unparented.png#lightbox)

1. Wybierz pierwsze wystąpienie szeregów czasowych. Następnie wybierz pozycję **Pokaż ciśnienie**.

    [![Selected wystąpienie szeregów czasowych z poleceniem menu w celu wyświetlenia średniego ciśnienia](media/v2-update-provision/analyze-three-show-pressure.png)](media/v2-update-provision/analyze-three-show-pressure.png#lightbox)

    Zostanie wyświetlony wykres szeregów czasowych. Zmień **Interwał** na **15s**.

    [wykres serii @no__t 1Time](media/v2-update-provision/analyze-four-chart.png)](media/v2-update-provision/analyze-four-chart.png#lightbox)

1. Powtórz krok 3 z innymi dwoma wystąpieniami szeregów czasowych. Można wyświetlić wszystkie wystąpienia szeregów czasowych, jak pokazano na tym wykresie:

    [![Chart dla wszystkich szeregów czasowych](media/v2-update-provision/analyze-five-chart.png)](media/v2-update-provision/analyze-five-chart.png#lightbox)

1. W polu opcja **przedziału** czasu zmodyfikuj zakres godzin, aby zobaczyć trendy szeregów czasowych w ciągu ostatniej godziny:

    [@no__t — 1Set zakres czasu na godzinę](media/v2-update-provision/analyze-six-time.png)](media/v2-update-provision/analyze-six-time.png#lightbox)

## <a name="define-and-apply-a-model"></a>Definiowanie i stosowanie modelu

W tej sekcji stosujesz model do struktury danych. Aby ukończyć model, należy zdefiniować typy, hierarchie i wystąpienia. Aby dowiedzieć się więcej na temat modelowania danych, zobacz [model szeregów czasowych](./time-series-insights-update-tsm.md).

1. W Eksploratorze wybierz kartę **model** :

   [Karta ![Model w Eksploratorze](media/v2-update-provision/define-one-model.png)](media/v2-update-provision/define-one-model.png#lightbox)

1. Wybierz pozycję **Dodaj** , aby dodać typ:

   [![The — przycisk dodawania dla typów](media/v2-update-provision/define-two-add.png)](media/v2-update-provision/define-two-add.png#lightbox)

1. Następnie należy zdefiniować trzy zmienne dla typu: *ciśnienie*, *temperatura*i *wilgotność*. W okienku **Dodawanie typu** ustaw następujące parametry:

    | Parametr | Akcja |
    | --- | ---|
    | **Nazwa** | Wprowadź **chłodner**. |
    | **Opis** | Wprowadź **to w definicji typu dla chłodzenia**. |

   * Aby zdefiniować *nacisk*, w obszarze **zmienne**ustaw następujące parametry:

     | Parametr | Akcja |
     | --- | ---|
     | **Nazwa** | Wprowadź **średnie ciśnienie**. |
     | **Wartość** | Wybierz pozycję **nacisk (podwójna)** . Automatyczne wypełnianie **wartości** może potrwać kilka minut po rozpoczęciu otrzymywania zdarzeń przez Azure Time Series Insights Preview. |
     | **Operacja agregacji** | Wybierz wartość **średnia**. |

      [![Selections do definiowania nacisku](media/v2-update-provision/define-three-variable.png)](media/v2-update-provision/define-three-variable.png#lightbox)

      Aby dodać następną zmienną, wybierz pozycję **Dodaj zmienną**.

   * Zdefiniuj *temperaturę*:

     | Parametr | Akcja |
     | --- | ---|
     | **Nazwa** | Wprowadź **średnią temperaturę**. |
     | **Wartość** | Wybierz pozycję **temperatura (Podwójna precyzja)** . Automatyczne wypełnianie **wartości** może potrwać kilka minut po rozpoczęciu otrzymywania zdarzeń przez Azure Time Series Insights Preview. |
     | **Operacja agregacji** | Wybierz wartość **średnia**.|

      [![Selections do definiowania temperatury](media/v2-update-provision/define-four-avg.png)](media/v2-update-provision/define-four-avg.png#lightbox)

      Aby dodać następną zmienną, wybierz pozycję **Dodaj zmienną**.

   * Zdefiniuj *wilgotność*:

      | | |
      | --- | ---|
      | **Nazwa** | Wprowadź **maksymalną wilgotność** |
      | **Wartość** | Wybierz **wilgotność (Double)** . Automatyczne wypełnianie **wartości** może potrwać kilka minut po rozpoczęciu otrzymywania zdarzeń przez Azure Time Series Insights Preview. |
      | **Operacja agregacji** | Wybierz pozycję **maks**.|

      [![Selections do definiowania temperatury](media/v2-update-provision/define-five-humidity.png)](media/v2-update-provision/define-five-humidity.png#lightbox)

    Wybierz pozycję **Utwórz**.

    Możesz zobaczyć dodany typ:

    [![Information o dodanym typie](media/v2-update-provision/define-six-type.png)](media/v2-update-provision/define-six-type.png#lightbox)

1. Następnym krokiem jest dodanie hierarchii. W obszarze **hierarchie**wybierz pozycję **Dodaj**:

    [Karta @no__t 1Hierarchies z przyciskiem Dodaj](media/v2-update-provision/define-seven-hierarchy.png)](media/v2-update-provision/define-seven-hierarchy.png#lightbox)

1. W okienku **Edytuj hierarchię** ustaw następujące parametry:

   | Parametr | Akcja |
   | --- | ---|
   | **Nazwa** | Wprowadź **hierarchię lokalizacji**. |
   | **Poziom 1** | Wprowadź **kraj**. |
   | **Poziom 2** | Wprowadź **miejscowość**. |
   | **Poziom 3** | Wprowadź **kompilację**. |

   Wybierz pozycję **Zapisz**.

    [pola @no__t 1Hierarchy z przyciskiem tworzenia](media/v2-update-provision/define-eight-add-hierarchy.png)](media/v2-update-provision/define-eight-add-hierarchy.png#lightbox)

   Można zobaczyć utworzoną hierarchię:

    [![Information o hierarchii](media/v2-update-provision/define-nine-created.png)](media/v2-update-provision/define-nine-created.png#lightbox)

1. Wybierz **wystąpienia**. Wybierz pierwsze wystąpienie, a następnie wybierz pozycję **Edytuj**:

    [@no__t — 1Selecting przycisku edycji dla wystąpienia](media/v2-update-provision/define-ten-edit.png)](media/v2-update-provision/define-ten-edit.png#lightbox)

1. W okienku **Edytuj wystąpienia** ustaw następujące parametry:

    | Parametr | Akcja |
    | --- | --- |
    | **Wprowadź** | Wybierz pozycję **chłodzenia**. |
    | **Opis** | Wprowadź **wystąpienie dla chłodzeniaer-01,1**. |
    | **Zlokalizowan** | Wybierz **hierarchię lokalizacji**. |
    | **Trzeciego** | Wprowadź **Stan USA**. |
    | **—** | Wprowadź **Seattle**. |
    | **Budowaniu** | Wprowadź **wskazówki dotyczące miejsca**. |

    [pola @no__t 1Instance za pomocą przycisku Zapisz](media/v2-update-provision/define-eleven-chiller.png)](media/v2-update-provision/define-eleven-chiller.png#lightbox)

   Wybierz pozycję **Zapisz**.

1. Powtórz poprzedni krok dla innych czujników. Zaktualizuj następujące wartości:

   * Dla chłodzenia 01,2:

     | Parametr | Akcja |
     | --- | --- |
     | **Wprowadź** | Wybierz pozycję **chłodzenia**. |
     | **Opis** | Wprowadź **wystąpienie dla chłodzeniaer-01,2**. |
     | **Zlokalizowan** | Wybierz **hierarchię lokalizacji**. |
     | **Trzeciego** | Wprowadź **Stan USA**. |
     | **—** | Wprowadź **Seattle**. |
     | **Budowaniu** | Wprowadź **centrum nauki pacyficznego**. |

   * Dla chłodzenia 01,3:

     | Parametr | Akcja |
     | --- | --- |
     | **Wprowadź** | Wybierz pozycję **chłodzenia**. |
     | **Opis** | Wprowadź **wystąpienie dla chłodzeniaer-01,3**. |
     | **Zlokalizowan** | Wybierz **hierarchię lokalizacji**. |
     | **Trzeciego** | Wprowadź **Stan USA**. |
     | **—** | Wprowadź **Nowy Jork**. |
     | **Budowaniu** | Wprowadź **kompilację stanu Empire**. |

1. Wybierz kartę **Analiza** , a następnie Odśwież stronę. W obszarze **Hierarchia lokalizacji**rozwiń węzeł wszystkie poziomy hierarchii, aby wyświetlić wystąpienia szeregów czasowych:

   [Karta @no__t — analiza 1The](media/v2-update-provision/define-twelve.png)](media/v2-update-provision/define-twelve.png#lightbox)

1. Aby poznać wystąpienia szeregów czasowych w ciągu ostatniej godziny, Zmień **szybkie czasy** na **ostatnią godzinę**:

    [![The szybkie godziny z wybraną ostatnią godziną](media/v2-update-provision/define-thirteen-explore.png)](media/v2-update-provision/define-thirteen-explore.png#lightbox)

1. W obszarze **centrum nauki pacyficznego**wybierz wystąpienie szeregów czasowych, a następnie wybierz pozycję **Pokaż maksymalną wilgotność**.

    [@no__t 1Selected — wystąpienie szeregów czasowych i wybór menu Pokaż maksymalną wilgotność](media/v2-update-provision/define-fourteen-show-max.png)](media/v2-update-provision/define-fourteen-show-max.png#lightbox)

1. Zostanie otwarta seria czasu dla **maksymalnej wilgotności** z rozmiarem interwału wynoszącym **1 minutę** . Aby odfiltrować zakres, wybierz region. Aby analizować zdarzenia w przedziale czasowym, kliknij wykres prawym przyciskiem myszy, a następnie wybierz polecenie **powiększenie**:

   [![Selected zakres z poleceniem zoom w menu skrótów](media/v2-update-provision/define-fifteen-filter.png)](media/v2-update-provision/define-fifteen-filter.png#lightbox)

1. Aby wyświetlić szczegóły zdarzenia, wybierz region, a następnie kliknij prawym przyciskiem myszy wykres:

   [![Detailed listę zdarzeń](media/v2-update-provision/define-eighteen.png)](media/v2-update-provision/define-eighteen.png#lightbox)


## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po ukończeniu samouczka Wyczyść utworzone zasoby:

1. W menu po lewej stronie w [Azure Portal](https://portal.azure.com)wybierz pozycję **wszystkie zasoby**, Znajdź Azure Time Series Insights grupę zasobów.
1. Usuń całą grupę zasobów (i wszystkie znajdujące się w niej zasoby), wybierając pozycję **Usuń** lub Usuń osobno każdy zasób.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób wykonywania tych instrukcji:  

> [!div class="checklist"]
> * Utwórz akcelerator symulacji urządzenia i użyj go.
> * Utwórz środowisko Azure Time Series Insights w wersji zapoznawczej PAYG.
> * Podłącz środowisko Azure Time Series Insights w wersji zapoznawczej do centrum zdarzeń.
> * Uruchom przykład akceleratora rozwiązania, aby przesłać strumieniowo dane do środowiska Azure Time Series Insights w wersji zapoznawczej.
> * Wykonaj podstawową analizę danych.
> * Zdefiniuj typ i hierarchię szeregów czasowych i skojarz je z wystąpieniami.

Teraz, gdy wiesz już, jak utworzyć własne środowisko Azure Time Series Insights w wersji zapoznawczej, Dowiedz się więcej na temat kluczowych pojęć w Azure Time Series Insights.

Przeczytaj o konfiguracji magazynu Azure Time Series Insights:

> [!div class="nextstepaction"]
> [Azure Time Series Insights w wersji zapoznawczej magazynu i transferu danych przychodzących](./time-series-insights-update-storage-ingress.md)

Dowiedz się więcej na temat modeli szeregów czasowych:

> [!div class="nextstepaction"]
> [Azure Time Series Insights w wersji zapoznawczej](./time-series-insights-update-tsm.md)
