---
title: 'Samouczek: Konfigurowanie środowiska Azure Time Series Insights w wersji zapoznawczej'
description: 'Samouczek: informacje dotyczące konfigurowania środowiska w wersji zapoznawczej programu Azure Time Series Insights.'
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 11/11/2019
ms.custom: seodec18
ms.openlocfilehash: 49960ae91688b97a9d38a65e1b17fd277508b996
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74114576"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Samouczek: Konfigurowanie środowiska Azure Time Series Insights w wersji zapoznawczej

Ten samouczek zawiera instrukcje tworzenia środowiska wersji zapoznawczej usługi Azure Time Series Insights dostępnej w modelu płatności zgodnie z rzeczywistym użyciem (PAYG).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Tworzenie środowiska wersji zapoznawczej usługi Azure Time Series Insights.
> * Połącz środowisko Azure Time Series Insights w wersji zapoznawczej z IoT Hub.
> * Uruchamianie przykładowego akceleratora rozwiązań umożliwiającego strumieniowe przesyłanie danych do środowiska wersji zapoznawczej usługi Azure Time Series Insights.
> * Wykonywanie podstawowej analizy danych.
> * Definiowanie hierarchii oraz typu modelu szeregów czasowych i kojarzenie ich z wystąpieniami.
> * Użyj łącznika Power BI i Wizualizuj dane w Power BI.

>[!TIP]
> [Akceleratory rozwiązań IoT](https://www.azureiotsolutions.com/Accelerators) zapewniają wstępnie skonfigurowane rozwiązania klasy korporacyjnej, których można użyć do przyspieszenia tworzenia niestandardowych rozwiązań IoT.

Utwórz konto [bezpłatnej subskrypcji platformy Azure](https://azure.microsoft.com/free/) , jeśli jeszcze jej nie masz.

## <a name="prerequisites"></a>Wymagania wstępne

* Co najmniej musisz mieć rolę **współautor** dla subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem przy użyciu kontroli dostępu opartej na rolach i Azure Portal](../role-based-access-control/role-assignments-portal.md).

## <a name="create-a-device-simulation"></a>Tworzenie symulacji urządzeń

W tej sekcji utworzysz trzy symulowane urządzenia, które wysyłają dane do wystąpienia usługi Azure IoT Hub.

1. Przejdź do [strony akceleratorów rozwiązań Azure IoT](https://www.azureiotsolutions.com/Accelerators). Na stronie będzie widocznych kilka gotowych przykładów. Zaloguj się przy użyciu konta platformy Azure. Następnie wybierz pozycję **Symulacja urządzenia**.

   [Strona akceleratorów rozwiązań usługi Azure IoT ![](media/v2-update-provision/device-one-accelerator.png)](media/v2-update-provision/device-one-accelerator.png#lightbox)

1. Na następnej stronie wybierz pozycję **Wypróbuj teraz**.

   [Strona symulacji urządzenia ![](media/v2-update-provision/device-two-try.png)](media/v2-update-provision/device-two-try.png#lightbox)

1. Na stronie **Tworzenie rozwiązania symulacji urządzenia** ustaw następujące parametry:

    | Parametr | Akcja |
    | --- | --- |
    | **Nazwa wdrożenia** | Wprowadź unikatową wartość nowej grupy zasobów. Znajdujące się na liście zasoby platformy Azure są tworzone i przypisywane do grupy zasobów. |
    | **Subskrypcja platformy Azure** | Wybierz subskrypcję, w której zostanie utworzone środowisko Time Series Insights. |
    | **Lokalizacja platformy Azure** | Wybierz region, w którym ma być przechowywane środowisko Time Series Insights. Należy pamiętać, że symulator urządzeń jest oferowany tylko w ograniczonej liczbie regionów, w tym przypadku, jeśli nie widzisz żądanego regionu, możesz wybrać lokalizację wyłącznie dla tego samouczka, a następnie utworzyć nowe środowisko TSI, gdy wszystko będzie gotowe do przejścia na następną fazę usługi-b oarding.  |
    | **Opcje wdrażania** | Wybierz pozycję **Udostępnij nowe IoT Hub**. |

    1. Wybierz pozycję **Utwórz**.
    [Strona symulacji urządzenia ![](media/v2-update-provision/device-two-create.png)](media/v2-update-provision/device-two-create.png#lightbox)

1. Po około 20 minutach Akcelerator rozwiązania będzie gotowy.

    [Strona symulacji urządzenia ![](media/v2-update-provision/device-two-ready.png)](media/v2-update-provision/device-two-ready.png#lightbox)

## <a name="create-a-preview-payg-environment"></a>Tworzenie środowiska w wersji zapoznawczej PAYG

W tej sekcji opisano sposób tworzenia środowiska Azure Time Series Insights w wersji zapoznawczej i łączenia go z usługą IoT Hub utworzoną przez Akcelerator rozwiązania IoT przy użyciu [Azure Portal](https://portal.azure.com/).

1. Zaloguj się do witryny Azure Portal przy użyciu konta subskrypcji.

1. Wybierz pozycję **Utwórz zasób** > **Internet rzeczy** > **Time Series Insights**.

   [![wybierz pozycję Internet rzeczy, a następnie wybierz pozycję Time Series Insights](media/v2-update-provision/payg-one-azure.png)](media/v2-update-provision/payg-one-azure.png#lightbox)

1. W okienku **Tworzenie środowiska Time Series Insights** na karcie **podstawowe** ustaw następujące parametry:

    | Parametr | Akcja |
    | --- | ---|
    | **Nazwa środowiska** | Wprowadź unikatową nazwę środowiska Azure Time Series Insights w wersji zapoznawczej. |
    | **Subskrypcja** | Wprowadź subskrypcję, w której chcesz utworzyć środowisko Azure Time Series Insights w wersji zapoznawczej. Najlepszym rozwiązaniem jest użycie tej samej subskrypcji jako reszty zasobów IoT, które są tworzone przez symulatora urządzeń. |
    | **Grupa zasobów** | Wybierz istniejącą grupę zasobów lub Utwórz nową grupę zasobów dla zasobu środowiska Azure Time Series Insights wersji zapoznawczej. Grupa zasobów jest kontenerem zasobów platformy Azure. Najlepszym rozwiązaniem jest użycie tej samej grupy zasobów co inne zasoby IoT, które są tworzone przez symulatora urządzeń. |
    | **Lokalizacja** | Wybierz region centrum danych dla środowiska Azure Time Series Insights w wersji zapoznawczej. Aby uniknąć dodatkowego opóźnienia, najlepszym rozwiązaniem jest utworzenie środowiska Azure Time Series Insights w wersji zapoznawczej w tym samym regionie, w którym znajduje się centrum IoT utworzone w symulatorze urządzeń. |
    | **Warstwa** |  Wybierz pozycję **PAYG** (*płatność zgodnie z rzeczywistym*użyciem). Jest to jednostka SKU produktu Azure Time Series Insights w wersji zapoznawczej. |
    | **Identyfikator właściwości** | Wprowadź wartość, która jednoznacznie identyfikuje wystąpienie szeregów czasowych. Nie można później zmienić wartości wprowadzonej w polu **ID właściwości** . Na potrzeby tego samouczka wprowadź **iothub-Connection-Device-ID**. Aby dowiedzieć się więcej na temat identyfikatora szeregów czasowych, zobacz [najlepsze rozwiązania dotyczące wybierania identyfikatora szeregów czasowych](./time-series-insights-update-how-to-id.md). |
    | **Nazwa konta magazynu** | Wprowadź globalnie unikatową nazwę nowego konta magazynu.|
    |**Włącz sklep ciepły**|Wybierz pozycję **tak** , aby włączyć sklep ciepły.|
    |**Przechowywanie danych (w dniach)**|Wybierz opcję domyślną 7 dni. |

    Wybierz pozycję **Dalej: Źródło zdarzenia**.

   [![okienko do tworzenia środowiska Time Series Insights](media/v2-update-provision/payg-two-1-create.png)](media/v2-update-provision/payg-two-1-create.png#lightbox)
   [![okienku do tworzenia środowiska Time Series Insights](media/v2-update-provision/payg-two-2-create.png)](media/v2-update-provision/payg-two-2-create.png#lightbox)

1. Na karcie **Źródło zdarzenia** ustaw następujące parametry:

   | Parametr | Akcja |
   | --- | --- |
   | **Utworzyć źródło zdarzeń?** | Wybierz pozycję **Tak**.|
   | **Nazwa** | Wprowadź unikatową wartość dla nazwy źródła zdarzenia. |
   | **Typ źródła** | Wybierz **IoT Hub**. |
   | **Wybierz centrum** | Wybierz **pozycję Wybierz istniejące**. |
   | **Subskrypcja** | Wybierz subskrypcję używaną dla symulatora urządzeń. |
   | **Nazwa centrum IoT** | Wybierz nazwę usługi IoT Hub utworzoną dla symulatora urządzeń. |
   | **Zasady dostępu do centrum IoT Hub** | Wybierz pozycję **iothubowner**. |
   | **Grupa konsumentów IoT Hub** | Wybierz pozycję **Nowa**, wpisz unikatową nazwę, a następnie wybierz polecenie **Dodaj**. Grupa konsumentów musi być unikatowa w wersji zapoznawczej Azure Time Series Insights. |
   | **Właściwość znacznika czasu** | Ta wartość służy do identyfikowania właściwości **timestamp** w przychodzących danych telemetrycznych. Na potrzeby tego samouczka pozostaw to pole puste. Ten symulator używa sygnatury czasowej pochodzącej z usługi IoT Hub, domyślnej wartości dla usługi Time Series Insights. |

   Wybierz pozycję **Przegląd + utwórz**.

   [![skonfigurować źródło zdarzenia](media/v2-update-provision/payg-five-event-source.png)](media/v2-update-provision/payg-five-event-source.png#lightbox)

1. Wybierz pozycję **Utwórz**.

    [![przegląd + Tworzenie strony z przyciskiem Utwórz](media/v2-update-provision/payg-six-review.png)](media/v2-update-provision/payg-six-review.png#lightbox)

    Możesz zobaczyć stan wdrożenia:

    [![powiadomienia o ukończeniu wdrożenia](media/v2-update-provision/payg-seven-deploy.png)](media/v2-update-provision/payg-seven-deploy.png#lightbox)

1. Masz dostęp do środowiska w wersji zapoznawczej Azure Time Series Insights domyślnie, jeśli jesteś właścicielem subskrypcji platformy Azure. Sprawdź, czy masz dostęp:

   1. Wyszukaj grupę zasobów, a następnie wybierz nowo utworzone środowisko Azure Time Series Insights w wersji zapoznawczej. 
      [![wybrane środowisko](media/v2-update-provision/payg-eight-environment.png)](media/v2-update-provision/payg-eight-environment.png#lightbox)

   1. Na stronie Podgląd Azure Time Series Insights wybierz pozycję **zasady dostępu do danych**: [![zasady dostępu do danych](media/v2-update-provision/payg-nine-data-access.png)](media/v2-update-provision/payg-nine-data-access.png#lightbox)

   1. Sprawdź, czy na liście znajdują się Twoje poświadczenia:

      [![na liście poświadczeń](media/v2-update-provision/payg-ten-verify.png)](media/v2-update-provision/payg-ten-verify.png#lightbox)

   Jeśli Twoje poświadczenia nie znajdują się na liście, musisz udzielić sobie uprawnień dostępu do środowiska, wybierając pozycję Dodaj i wyszukuj poświadczenia. Aby dowiedzieć się więcej o ustawianiu uprawnień, zobacz [Grant data access (Przyznawanie dostępu do danych)](./time-series-insights-data-access.md).

## <a name="stream-data"></a>Strumieniowe przesyłanie danych

Teraz, gdy wdrożono środowisko Time Series Insights, Rozpocznij przesyłanie strumieniowe danych na potrzeby analizy.

1. Przejdź z powrotem do [strony akceleratorów rozwiązań usługi Azure IoT](https://www.azureiotsolutions.com/Accelerators). Znajdź swoje rozwiązanie na pulpicie nawigacyjnym akceleratora rozwiązań, a następnie wybierz pozycję **Uruchom**:

    [![uruchomić rozwiązanie do symulacji urządzeń](media/v2-update-provision/device-three-launch.png)](media/v2-update-provision/device-three-launch.png#lightbox)

1. Wybierz pozycję **Przejdź do akceleratora rozwiązania**.

    [![uruchomić rozwiązanie do symulacji urządzeń](media/v2-update-provision/device-accelerator.png)](media/v2-update-provision/device-accelerator.png#lightbox)

1. Nastąpi przekierowanie do strony **Symulacja urządzeń Microsoft Azure IoT**. W prawym górnym rogu strony wybierz pozycję **Nowa symulacja**.

    [Strona symulacji usługi Azure IoT ![](media/v2-update-provision/device-four-iot-sim-page.png)](media/v2-update-provision/device-four-iot-sim-page.png#lightbox)

1. W okienku **Konfiguracja symulacji** ustaw następujące parametry:

    | Parametr | Akcja |
    | --- | --- |
    | **Nazwa** | Wprowadź unikatową nazwę symulatora. |
    | **Opis** | Wprowadź definicję. |
    | **Czas trwania symulacji** | Wybierz opcję **Uruchamianie bez ograniczeń**. |
    | **Model urządzenia** | Kliknij pozycję + **Dodaj typ urządzenia** <br />**Nazwa**: wprowadź **windę**. <br />**Kwota**: wprowadź wartość **3**. <br /> Pozostaw pozostałe wartości domyślne |
    | **Docelowa usługa IoT Hub** | Wybierz pozycję **Użyj dostarczonej wstępnie usługi IoT Hub**. |

    [![parametry do ustawienia](media/v2-update-provision/device-five-params.png)](media/v2-update-provision/device-five-params.png#lightbox)

    Wybierz pozycję **Rozpocznij symulację**.

    Na pulpicie nawigacyjnym symulacji urządzenia będą widoczne **aktywne urządzenia** i **całkowita liczba komunikatów**.

    [Pulpit nawigacyjny symulacji usługi Azure IoT ![](media/v2-update-provision/device-seven-dashboard.png)](media/v2-update-provision/device-seven-dashboard.png#lightbox)

## <a name="analyze-data"></a>Analizuj dane

W tej sekcji przeprowadzimy podstawową analizę danych szeregów czasowych przy użyciu [eksploratora wersji zapoznawczej usługi Azure Time Series Insights](./time-series-insights-update-explorer.md).

1. Przejdź do eksploratora wersji zapoznawczej usługi Azure Time Series Insights, wybierając adres URL na stronie zasobu w witrynie [Azure Portal](https://portal.azure.com/).

    [![adres URL Eksploratora Time Series Insights Preview](media/v2-update-provision/analyze-one-portal.png)](media/v2-update-provision/analyze-one-portal.png#lightbox)

1. W Eksploratorze Time Series Insights zostanie wyświetlony pasek obejmujący górną część ekranu. Jest to Twój selektor dostępności. Upewnij się, że wybrano co najmniej dwa 2 mln i jeśli to konieczne, rozwiń przedział czasu, zaznaczając i przeciągając uchwyty selektora w lewo i w prawo.

1. Po lewej stronie będą widoczne **wystąpienia szeregów czasowych** .


    [![lista niewidocznych wystąpień](media/v2-update-provision/analyze-two-unparented.png)](media/v2-update-provision/analyze-two-unparented.png#lightbox)

1. Wybierz pierwsze wystąpienie szeregów czasowych. Następnie wybierz pozycję **Pokaż ciśnienie**.

    [![wybrane wystąpienie szeregów czasowych z poleceniem menu w celu wyświetlenia średniego ciśnienia](media/v2-update-provision/analyze-three-show-pressure.png)](media/v2-update-provision/analyze-three-show-pressure.png#lightbox)

    Zostanie wyświetlony wykres szeregów czasowych. Zmień **Interwał** na **30 s**.

    [![wykres szeregów czasowych](media/v2-update-provision/analyze-four-chart.png)](media/v2-update-provision/analyze-four-chart.png#lightbox)

1. Powtórz krok 3 z innymi dwoma wystąpieniami szeregów czasowych, aby wyświetlić wszystkie trzy, jak pokazano na tym wykresie:

    [![wykres dla wszystkich szeregów czasowych](media/v2-update-provision/analyze-five-chart.png)](media/v2-update-provision/analyze-five-chart.png#lightbox)

1. Wybierz selektor przedziałów czasu w prawym górnym rogu. W tym miejscu możesz wybrać określone czasy rozpoczęcia i zakończenia w milisekundach lub wybrać spośród wstępnie skonfigurowanych opcji, takich jak Ostatnia godzina. Możesz również zmienić domyślną strefę czasową.

    [![Ustaw zakres czasu na godzinę](media/v2-update-provision/analyze-six-time.png)](media/v2-update-provision/analyze-six-time.png#lightbox)

    Poniżej znajduje się przechwytywanie ekranu okienka wykresów po uruchomieniu symulacji przez godzinę:

    [![okienku wykresów](media/v2-update-provision/analyze-seven-time.png)](media/v2-update-provision/analyze-seven-time.png#lightbox)

## <a name="define-and-apply-a-model"></a>Definiowanie i stosowanie modelu

W tej sekcji zastosujesz model, aby ustrukturyzować dane. Do utworzenia modelu potrzebne są definicje typów, hierarchii i wystąpień. Aby dowiedzieć się więcej na temat modelowania danych, zobacz [model szeregów czasowych](./time-series-insights-update-tsm.md).

1. W eksploratorze wybierz kartę **Model**:

   [Karta ![model w Eksploratorze](media/v2-update-provision/define-one-model.png)](media/v2-update-provision/define-one-model.png#lightbox)

1. Na karcie **typy** wybierz pozycję **Dodaj**.

   [![przycisku Dodaj dla typów](media/v2-update-provision/define-two-add.png)](media/v2-update-provision/define-two-add.png#lightbox)

1. Wprowadź następujące parametry:

    | Parametr | Akcja |
    | --- | ---|
    | **Nazwa** | Wprowadź **windę** |
    | **Opis** | Wprowadź **tę definicję typu dla wind** |

    [![przycisku Dodaj dla typów](media/v2-update-provision/define-three-properties.png)](media/v2-update-provision/define-three-properties.png#lightbox)

1. Następnie wybierz kartę **zmienne** . [![wybierz kartę zmienne](media/v2-update-provision/define-four-variables.png)](media/v2-update-provision/define-four-variables.png#lightbox)

1. Wybierz pozycję **+ Dodaj zmienną** i wypełnij następujące wartości dla pierwszej zmiennej typu Wind. W sumie są tworzone trzy zmienne.

    | Parametr | Akcja |
    | --- | --- |
    | **Nazwa** | Wpisz **Avg Temperature** (Temperatura średnia). |
    | **Natur** | Wybierz wartość **numeryczną** |
    | **Wartość** | Wybierz jedną z ustawień predefiniowanych: wybierz pozycję **temperatura (Podwójna precyzja)** . <br /> Uwaga: Ta **wartość** może potrwać kilka minut, jeśli funkcja Azure Time Series Insights Preview zacznie otrzymywać zdarzenia.|
    | **Operacja agregacji** | Rozwiń **Opcje zaawansowane**. <br /> Wybierz opcję **AVG** (średnia). |

    [![wybór dla definiowania temperatury](media/v2-update-provision/define-five-variable.png)](media/v2-update-provision/define-five-variable.png#lightbox)

    Wybierz przycisk **Zastosuj**.

    Ponownie wybierz pozycję **+ Dodaj zmienną** i ustaw następujące wartości:

    | Parametr | Akcja |
    | --- | --- |
    | **Nazwa** | Wprowadź **średnie wibracje**. |
    | **Natur** | Wybierz wartość **numeryczną** |
    | **Wartość** | Wybierz spośród ustawień predefiniowanych: wybierz pozycję **drgania (Double)** . <br /> Uwaga: Ta **wartość** może potrwać kilka minut, jeśli funkcja Azure Time Series Insights Preview zacznie otrzymywać zdarzenia.|
    | **Operacja agregacji** | Rozwiń **Opcje zaawansowane**. <br /> Wybierz opcję **AVG** (średnia). |

    [![wybór dla definiowania drgań](media/v2-update-provision/define-five-vibration.png)](media/v2-update-provision/define-five-vibration.png#lightbox)

    Wybierz przycisk **Zastosuj**.

    Ponownie wybierz pozycję **+ Dodaj zmienną** i ustaw następujące wartości dla zmiennej trzeciej i końcowej:

    | Parametr | Akcja |
    | --- | --- |
    | **Nazwa** | Wprowadź **piętro**. |
    | **Natur** | Wybierz **kategorii** |
    | **Wartość** | Wybierz z ustawień predefiniowanych: wybierz pozycję **piętro (Double)** . <br /> Uwaga: Ta **wartość** może potrwać kilka minut, jeśli funkcja Azure Time Series Insights Preview zacznie otrzymywać zdarzenia.|
    | **Rodzaj** | <span style="text-decoration: underline">Etykiety</span>  - <span style="text-decoration: underline">wartości</span> <br /> Niższy: 1, 2, 3, 4 <br /> Środek: 5, 6, 7, 8, 9 <br /> Upper: 10, 11, 12, 13, 14, 15 |
    | **Kategoria domyślna** | Wprowadź **nieznany** |

    [![wybór dla definiowania drgań](media/v2-update-provision/define-five-floor.png)](media/v2-update-provision/define-five-floor.png#lightbox)

    Wybierz przycisk **Zastosuj**. Powinny pojawić się trzy utworzone zmienne:

    [![wybór dla definiowania drgań](media/v2-update-provision/define-five-floor2.png)](media/v2-update-provision/define-five-floor2.png#lightbox)

   Wybierz pozycję **Zapisz**. Zobaczysz utworzony **Typ** :

    [![wybór dla definiowania drgań](media/v2-update-provision/define-five-floor3.png)](media/v2-update-provision/define-five-floor3.png#lightbox)

1. Wybierz kartę **hierarchie** . Wybierz pozycję **+ Dodaj**.

    [Karta hierarchie ![z przyciskiem Dodaj](media/v2-update-provision/define-seven-hierarchy.png)](media/v2-update-provision/define-seven-hierarchy.png#lightbox)

1. W okienku **Edytuj hierarchię** ustaw następujące parametry:

   | Parametr | Akcja |
   | --- | ---|
   | **Nazwa** | Wpisz **Location Hierarchy** (Hierarchia lokalizacji). |
   |**Poziomy**| Wprowadź **kraj** jako nazwę pierwszego poziomu <br> Wybierz pozycję **+ Dodaj poziom** <br> Wprowadź **miasto** dla drugiego poziomu, a następnie wybierz pozycję **+ Dodaj poziom** <br> Wprowadź wartość **konstrukcyjną** jako nazwę trzeciego i końcowego poziomu |

   Wybierz pozycję **Zapisz**.

    [![pól hierarchii za pomocą przycisku tworzenia](media/v2-update-provision/define-eight-add-hierarchy.png)](media/v2-update-provision/define-eight-add-hierarchy.png#lightbox)

   Zobaczysz utworzoną hierarchię:

    [![informacje o hierarchii](media/v2-update-provision/define-nine-created.png)](media/v2-update-provision/define-nine-created.png#lightbox)

1. Przejdź do **wystąpień**. W obszarze **Akcje** po prawej stronie i wybierz ikonę ołówka, aby zmodyfikować pierwsze wystąpienie o następujące wartości:

    | Parametr | Akcja |
    | --- | --- |
    | **Typ** | Wybierz pozycję **windy**. |
    | **Nazwa** | Wprowadź **windę 1**|
    | **Opis** | Wprowadź **wystąpienie dla wind 1** |

    [![wybranie przycisku edycji dla wystąpienia](media/v2-update-provision/define-ten-edit.png)](media/v2-update-provision/define-ten-edit.png#lightbox)

    Przejdź do **pól wystąpienia** i wprowadź następujące elementy:

    | Parametr | Akcja |
    | --- | --- |
    | **Hierarchies** | Wybierz **hierarchię lokalizacji** |
    | **Country** | Wprowadź **Stan USA** |
    | **City** | Wprowadź **Seattle** |
    | **Building** | Wprowadź **wskazówkę miejsca** |

    [![wybranie przycisku edycji dla wystąpienia](media/v2-update-provision/define-ten2-edit.png)](media/v2-update-provision/define-ten2-edit.png#lightbox)

    Wybierz pozycję **Zapisz**.

1. Powtórz krok 8 z innymi dwoma wystąpieniami o następujących wartościach:

    <span style="text-decoration: underline;">Dla wind 2</span>:

    | Parametr | Akcja |
    | --- | --- |
    | **Typ** | Wybierz pozycję **windy**. |
    | **Nazwa** | Wprowadź **windę 2**|
    | **Opis** | Wprowadź **wystąpienie dla wind 2** |
    | **Hierarchies** | Wybierz **hierarchię lokalizacji** |
    | **Country** | Wprowadź **Stan USA** |
    | **City** | Wprowadź **Seattle** |
    | **Building** | Wprowadź **centrum nauki Pacyfiku** |

    <span style="text-decoration: underline;">Dla windy 3</span>:

    | Parametr | Akcja |
    | --- | --- |
    | **Typ** | Wybierz pozycję **windy**. |
    | **Nazwa** | Wprowadź **windę 3**|
    | **Opis** | Wprowadź **wystąpienie dla wind 3** |
    | **Hierarchies** | Wybierz **hierarchię lokalizacji** |
    | **Country** | Wprowadź **Stan USA** |
    | **City** | Wprowadź **Nowy Jork** |
    | **Building** | Wprowadź **kompilację stanu Empire** |

1. Przejdź z powrotem do karty **Analiza** , aby wyświetlić okienko wykresów. W obszarze **Hierarchia lokalizacji**rozwiń węzeł wszystkie poziomy hierarchii, aby wyświetlić wystąpienia szeregów czasowych:

   [![karcie analiza](media/v2-update-provision/define-twelve.png)](media/v2-update-provision/define-twelve.png#lightbox)

1. W obszarze **centrum nauki pacyficznego**wybierz pozycję przenośnik szeregów czasowych **2**, a następnie wybierz pozycję **Pokaż średnią temperaturę**.

    [![wystąpienie szeregów czasowych Pokaż średnią temperaturę](media/v2-update-provision/define-eleven-temperature.png)](media/v2-update-provision/define-eleven-temperature.png#lightbox)

1. Dla tego samego wystąpienia, **Wind 2**, wybierz pozycję **Pokaż piętro**.

    [![wystąpienie szeregów czasowych Pokaż podłogę](media/v2-update-provision/define-twelve-floor.png)](media/v2-update-provision/define-twelve-floor.png#lightbox)

    Za pomocą zmiennej kategorii można określić, jak długo winda zajmuje się górną, niższą i środkową podłogą.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po ukończeniu samouczka Wyczyść utworzone zasoby:

1. W menu po lewej stronie w [Azure Portal](https://portal.azure.com)wybierz pozycję **wszystkie zasoby**, Znajdź Azure Time Series Insights grupę zasobów.
1. Usuń całą grupę zasobów (i wszystkie znajdujące się w niej zasoby), wybierając pozycję **Usuń** lub Usuń osobno każdy zasób.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:  

* Tworzenie i używanie akceleratora symulacji urządzenia.
* Tworzenie środowiska wersji zapoznawczej usługi Azure Time Series Insights dostępnej w modelu PAYG.
* Podłącz środowisko Azure Time Series Insights w wersji zapoznawczej do centrum IoT Hub.
* Uruchamianie przykładowego akceleratora rozwiązań umożliwiającego strumieniowe przesyłanie danych do środowiska wersji zapoznawczej usługi Azure Time Series Insights.
* Wykonywanie podstawowej analizy danych.
* Definiowanie hierarchii oraz typu modelu szeregów czasowych i kojarzenie ich z wystąpieniami.

Wiesz już, jak utworzyć własne środowisko wersji zapoznawczej usługi Azure Time Series Insights. Teraz możesz dowiedzieć się więcej na temat kluczowych pojęć związanych z usługą Azure Time Series Insights.

Zapoznaj się z konfiguracją magazynu usługi Azure Time Series Insights:

> [!div class="nextstepaction"]
> [Usługi Azure storage czasu Series Insights w wersji zapoznawczej i transferu danych przychodzących](./time-series-insights-update-storage-ingress.md)

Dowiedz się więcej o modelach szeregów czasowych:

> [!div class="nextstepaction"]
> [Modelowanie danych w usłudze Azure Time Series Insights w wersji zapoznawczej](./time-series-insights-update-tsm.md)
