---
title: Konfigurowanie usługi Azure Time Series Insights w wersji zapoznawczej — samouczek dotyczący konfigurowania środowiska wersji zapoznawczej usługi Azure Time Series Insights | Microsoft Docs
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
ms.openlocfilehash: 9ad957d6378b1279f1ca51939eb4802b0ce7d78f
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/13/2018
ms.locfileid: "53322622"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Samouczek: Konfigurowanie środowiska wersji zapoznawczej usługi Azure Time Series Insights

Ten samouczek zawiera instrukcje tworzenia środowiska wersji zapoznawczej usługi Azure Time Series Insights dostępnej w modelu płatności zgodnie z rzeczywistym użyciem (PAYG). Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

* Tworzenie środowiska wersji zapoznawczej usługi Azure Time Series Insights.
* Łączenie środowiska wersji zapoznawczej usługi Azure Time Series Insights z centrum zdarzeń w usłudze Azure Event Hubs.
* Uruchamianie symulacji farmy wiatrowej umożliwiającej strumieniowe przesyłanie danych do środowiska wersji zapoznawczej usługi Azure Time Series Insights.
* Wykonywanie podstawowej analizy danych.
* Definiowanie hierarchii oraz typu modelu szeregów czasowych i kojarzenie ich z wystąpieniami.

# <a name="create-a-device-simulation"></a>Tworzenie symulacji urządzeń

W tej sekcji utworzymy trzy symulowane urządzenia, które będą wysyłać dane do centrum IoT Hub.

1. Przejdź do [strony głównej akceleratorów rozwiązań Azure IoT](https://www.azureiotsolutions.com/Accelerators). Strona główna akceleratorów rozwiązań Azure IoT zawiera kilka gotowych przykładów. Zaloguj się przy użyciu konta platformy Azure. Następnie wybierz pozycję **Symulacja urządzenia**.

   ![Strona główna akceleratorów rozwiązań Azure IoT][1]

   Na koniec kliknij pozycję **Wypróbuj teraz**.

1. Wprowadź wymagane parametry na stronie rozwiązania **Tworzenie symulacji urządzenia**:

   | Parametr | Opis |
   | --- | --- |
   | Nazwa rozwiązania |    Unikatowa wartość służąca do tworzenia nowej grupy zasobów. Wyświetlone zasoby platformy Azure | są tworzone i przypisywane do grupy zasobów. |
   | Subskrypcja | Wpisz subskrypcję użytą do utworzenia środowiska usługi TSI. |
   | Region |   Wpisz region użyty do utworzenia środowiska usługi TSI. |
   | Wdróż opcjonalne zasoby platformy Azure    | Pozostaw zaznaczone pole wyboru IoT Hub, ponieważ symulowane urządzenia będą używały tego centrum do łączenia się i strumieniowania danych. |

   Po wprowadzeniu wymaganych parametrów kliknij pozycję **Utwórz rozwiązanie**. Poczekaj około 10–15 minut na wdrożenie rozwiązania.

   ![Tworzenie rozwiązania symulacji urządzeń][2]

1. Na **pulpicie nawigacyjnym akceleratora rozwiązania** kliknij przycisk **Uruchom**:

   ![Uruchamianie rozwiązania symulacji urządzeń][3]

1. Nastąpi przekierowanie do strony **symulacji urządzeń Microsoft Azure IoT**. Kliknij przycisk **+ Nowa symulacja** widoczny w prawym górnym rogu ekranu.

   ![Strona symulacji w usłudze Azure IoT][4]

1.  Wypełnij wymagane parametry następującymi wartościami:

    ![Parametry do wypełnienia][5]

    |||
    | --- | --- |
    | **Nazwa** | Wprowadź unikatową nazwę symulatora |
    | **Opis** | Wprowadź definicję |
    | **Czas trwania symulacji** | Ustaw wartość `Run indefinitely` |
    | **Model urządzenia** | **Nazwa**: Wprowadź `Chiller` **Ilość**: Wprowadź `3` |
    | **Docelowa usługa IoT Hub** | Ustaw wartość `Use pre-provisioned IoT Hub` |

    Po wprowadzeniu wymaganych parametrów kliknij pozycję **Rozpocznij symulację**.

1. Na pulpicie nawigacyjnym symulacji urządzeń sprawdź opcje **Aktywne urządzenia** i **Komunikaty na sekundę**.

    ![Pulpit nawigacyjny symulacji w usłudze Azure IoT][6]

## <a name="list-device-simulation-properties"></a>Wyświetlanie właściwości symulacji urządzeń

Do utworzenia środowiska usługi Azure Time Series Insights potrzebne będą nazwy centrum IoT Hub, subskrypcji i grupy zasobów.

1. Przejdź do **pulpitu nawigacyjnego akceleratora rozwiązań** i zaloguj się przy użyciu tego samego konta platformy Azure. Znajdź symulację urządzeń utworzoną w poprzednich krokach.

1. Kliknij symulator urządzeń, a następnie kliknij przycisk **Uruchom**. Kliknij link **Portal zarządzania Azure** wyświetlany po prawej stronie.

    ![Lista symulatorów][7]

1. Zanotuj nazwy centrum IoT Hub, subskrypcji i grupy zasobów.

    ![Azure Portal][8]

## <a name="create-a-time-series-insights-preview-payg-environment"></a>Tworzenie środowiska wersji zapoznawczej usługi Time Series Insights dostępnej w modelu PAYG

W tej sekcji opisano, jak utworzyć środowisko wersji zapoznawczej usługi Azure Time Series Insights przy użyciu witryny [Azure Portal](https://portal.azure.com/).

1. Zaloguj się do witryny Azure Portal przy użyciu konta subskrypcji.

1. Wybierz pozycję **Utwórz zasób**.

1. Wybierz kategorię **Internet rzeczy**, a następnie wybierz pozycję **Time Series Insights**.

   ![Wybieranie pozycji Utwórz zasób, Internet rzeczy i Time Series Insights][9]

1. Wypełnij pola na stronie w następujący sposób:

   | | |
   | --- | ---|
   | **Nazwa środowiska** | Wybierz unikatową nazwę środowiska wersji zapoznawczej usługi Azure Time Series Insights. |
   | **Subskrypcja** | Wprowadź subskrypcję, w której chcesz utworzyć środowisko wersji zapoznawczej usługi Azure Time Series Insights. Najlepiej jest użyć tej samej subskrypcji, w której są przechowywane pozostałe zasoby IoT utworzone przez symulatora urządzeń. |
   | **Grupa zasobów** | Grupa zasobów jest kontenerem zasobów platformy Azure. Dla zasobu środowiska wersji zapoznawczej usługi Azure Time Series Insights wybierz istniejącą grupę zasobów lub utwórz nową. Najlepiej jest użyć tej samej grupy zasobów, w której są przechowywane pozostałe zasoby IoT utworzone przez symulatora urządzeń. |
   | **Lokalizacja** | Wybierz region centrum danych dla środowiska wersji zapoznawczej usługi Azure Time Series Insights. Aby uniknąć kosztów i opóźnień spowodowanych dodatkową przepustowością, najlepiej przechowywać środowisko wersji zapoznawczej usługi Azure Time Series Insights w tym samym regionie co inne zasoby IoT. |
   | **Warstwa** |  Wybierz opcję `PAYG`, czyli model płatności zgodnie z rzeczywistym użyciem. Jest to jednostka SKU produktu Azure Time Series Insights w wersji zapoznawczej. |
   | **Identyfikator właściwości** | Umożliwia jednoznaczną identyfikację szeregu czasowego. Należy pamiętać, że później nie można zmienić tego pola. W tym samouczku pole to ma wartość `iothub-connection-device-id`. Aby dowiedzieć się więcej o identyfikatorze szeregu czasowego, zobacz [How to choose a Time Series ID (Jak wybrać identyfikator szeregu czasowego)](./time-series-insights-update-how-to-id.md). |
   | **Nazwa konta magazynu** | Wprowadź globalnie unikatową nazwę konta magazynu, które ma zostać utworzone. |

   Po wypełnieniu powyższych pól kliknij przycisk **Dalej: Źródło zdarzeń**.

   ![Klikanie przycisku Dalej: Źródło zdarzeń][10]

1. Na stronie wypełnij pola w następujący sposób:

   | | |
   | --- | --- |
   | **Utworzyć źródło zdarzeń?** | Wprowadź wartość `Yes`|
   | **Nazwa** | Wymaga unikatowej wartości, która jest używana jako nazwa źródła zdarzeń.|
   | **Typ źródła** | Wprowadź wartość `IoT Hub` |
   | **Wybrać centrum?** | Wprowadź wartość `Select Existing` |
   | **Subskrypcja** | Wprowadź subskrypcję użytą dla symulatora urządzenia. |
   | **Nazwa centrum IoT** | Wprowadź nazwę centrum IoT Hub utworzonego dla symulatora urządzenia. |
   | **Zasady dostępu do centrum IoT Hub** | Wprowadź wartość `iothubowner` |
   | **Grupa konsumentów centrum IoT** | Do korzystania z wersji zapoznawczej usługi Azure Time Series Insights potrzebna jest unikatowa grupa użytkowników. |
   | **Sygnatura czasowa** | To pole umożliwia identyfikowanie właściwości sygnatury czasowej w przychodzących danych telemetrycznych. W tym samouczku nie należy wypełniać tego pola. Ten symulator używa sygnatury czasowej pochodzącej z usługi IoT Hub, domyślnej wartości dla usługi Time Series Insights.|

   Aby utworzyć unikatową grupę użytkowników:

   1. Kliknij przycisk **Nowy** obok pola **Grupa konsumentów centrum IoT**:

      ![Klikanie przycisku Dalej: Źródło zdarzeń][11]

   1. Nadaj grupie użytkowników unikatową nazwę, a następnie kliknij przycisk **Dodaj**:

      ![Kliknij pozycję Dodaj.][12]

   Po wypełnieniu powyższych pól kliknij przycisk **Przejrzyj + utwórz**.

      ![Przejrzyj i utwórz][13]

1. Sprawdź zawartość wszystkich pól na stronie przeglądu i kliknij przycisk **Utwórz**.

   ![Przycisk Utwórz][14]

1. Będą wyświetlane informacje o stanie wdrożenia.

   ![Zakończono wdrożenie][15]

1. Jeśli jesteś właścicielem dzierżawy, masz dostęp do środowiska szeregu czasowego. Aby to sprawdzić:

   * Przejdź do nowo utworzonego środowiska wersji zapoznawczej usługi Azure Time Series Insights. W tym celu wyszukaj grupę zasobów. Następnie kliknij swoje środowisko szeregu czasowego:

      ![Zakończono wdrożenie][16]

   * Na stronie wersji zapoznawczej usługi Azure Time Series Insights przejdź do obszaru **Zasady dostępu do danych**.

     ![Zasady dostępu do danych][17]

   * Sprawdź, czy widać Twoje poświadczenia.

     ![Weryfikowanie poświadczeń][18]

   Jeśli poświadczenia nie są widoczne, musisz przyznać sobie uprawnienie dostępu do środowiska. Aby dowiedzieć się więcej o ustawianiu uprawnień, zobacz [Przyznawanie dostępu do danych](./time-series-insights-data-access.md).

## <a name="analyze-data-in-your-environment"></a>Analizowanie danych w środowisku

W tej sekcji przeprowadzimy podstawową analizę danych szeregów czasowych przy użyciu [eksploratora wersji zapoznawczej usługi Azure Time Series Insights](./time-series-insights-update-explorer.md).

1. Przejdź do eksploratora wersji zapoznawczej usługi Azure Time Series Insights, klikając adres URL na stronie zasobu w witrynie [Azure Portal](https://portal.azure.com/).

   ![Adres URL eksploratora usługi Time Series Insights][19]

1. W eksploratorze wybierz węzły **Wystąpienia bez elementów nadrzędnych**, aby wyświetlić wszystkie wystąpienia usługi Azure Time Series Insights w wersji zapoznawczej w środowisku.

   ![Lista wystąpień bez elementów nadrzędnych][20]

1. W wyświetlonym szeregu czasowym kliknij pierwsze wystąpienie. Następnie kliknij pozycję **Show Avg pressure** (Wyświetl ciśnienie średnie).

   ![Wyświetlanie ciśnienia średniego][21]

1. Po prawej stronie powinien pojawić się wykres szeregu czasowego:

   ![Wykres szeregu czasowego][22]

1. Powtórz **krok 3** dla dwóch pozostałych szeregów czasowych. Spowoduje to wyświetlenie wszystkich szeregów czasowych, jak pokazano poniżej:

   ![Wykres z wszystkimi szeregami czasowymi][23]

1. Zmień **zakres czasu**, aby wyświetlić trendy szeregów czasowych w ciągu ostatniej godziny. Wybierz pole opcji **Od**, jak pokazano poniżej:

   ![Wybieranie opcji Od][24]

1. Zmień godzinę w polu opcji **Od**, aby wyświetlić zdarzenia z ostatniej godziny:

   ![Wybieranie opcji Od][25]

1. Dzięki temu możesz porównać ciśnienie we wszystkich trzech urządzeniach w ciągu ostatniej godziny:

   ![Wybieranie opcji Od][26]

## <a name="define-and-apply-a-model"></a>Definiowanie i stosowanie modelu

W tej sekcji zastosujemy model, aby ustrukturyzować dane. Do utworzenia modelu potrzebne będą definicje typów, hierarchii i wystąpień. Aby dowiedzieć się więcej na temat modelowania danych, zobacz [Modele szeregów czasowych](./time-series-insights-update-tsm.md).

1. W eksploratorze wybierz kartę **Model**:

   ![Wybieranie karty modelu][27]

1. Następnie kliknij pozycję **+ Dodaj**, aby dodać typ. Po prawej stronie zostanie otwarty edytor typów.

   ![Kliknij pozycję Dodaj.][28]

1. Następnie zdefiniuj trzy zmienne — Pressure, Temperature i Humidity — w typie. Wprowadź następujące pola:

   | | |
   | --- | ---|
   | **Nazwa** | Wprowadź wartość `Chiller` |
   | **Opis** | Wprowadź wartość `This is a type definition of Chiller` |

   * Teraz zdefiniuj element Pressure z trzema zmiennymi:

      | | |
      | --- | ---|
      | **Nazwa** | Wprowadź wartość `Avg Pressure` |
      | **Wartość** | Wybierz opcję **pressure (Double)**. Pamiętaj, że gdy usługa Azure Time Series Insights zacznie odbierać zdarzenia, wypełnienia tego pola może potrwać kilka minut |
      | **Operacja agregacji** | Wybierz pozycję `AVG` |

      ![Dodawanie zmiennej][29]

      Kliknij pozycję **+ Zmienna**, aby dodać kolejną zmienną.

   * Teraz zdefiniujemy zmienną Temperature:

      | | |
      | --- | ---|
      | **Nazwa** | Wprowadź wartość `Avg Temperature` |
      | **Wartość** | Wybierz opcję **temperature (Double)**. Pamiętaj, że gdy usługa Azure Time Series Insights zacznie odbierać zdarzenia, wypełnienia tego pola może potrwać kilka minut |
      | **Operacja agregacji** | Wybierz pozycję `AVG`|

      ![Definiowanie temperatury][30]

   * Teraz zdefiniujemy zmienną Humidity:

      | | |
      | --- | ---|
      | **Nazwa** | Wprowadź wartość `Max Humidity` |
      | **Wartość** | Wybierz opcję **humidity (Double)**. Pamiętaj, że gdy usługa Azure Time Series Insights zacznie odbierać zdarzenia, wypełnienia tego pola może potrwać kilka minut |
      | **Operacja agregacji** | Wybierz pozycję `MAX`|

      ![Definiowanie temperatury][31]

   Po zdefiniowaniu zmiennych kliknij przycisk **Utwórz**.

1. Zobaczysz dodany typ:

   ![Wyświetlanie dodanego typu][32]

1. Następny krok polega na dodaniu hierarchii. W sekcji **Hierarchie** wybierz pozycję **+ Dodaj**, aby utworzyć nową hierarchię:

   ![Dodawanie hierarchii][33]

1. Zdefiniuj hierarchię. Wprowadź następujące pola:

   | | |
   | --- | ---|
   | **Nazwa** | Wprowadź wartość `Location Hierarchy` |
   | **Level 1** | Wprowadź wartość `Country` |
   | **Level 2** | Wprowadź wartość `City` |
   | **Level 3** | Wprowadź wartość `Building` |

   Po wypełnieniu powyższych pól kliknij pozycję **Utwórz**.

   ![Definiowanie hierarchii][34]

1. Zobaczysz utworzoną hierarchię:

   ![Wyświetlanie hierarchii][35]

1. Po zdefiniowaniu hierarchii kliknij przycisk **Wystąpienia** po lewej stronie. Po wyświetleniu wystąpień kliknij pierwsze wystąpienie i wybierz pozycję **Edytuj**:

   ![Edytowanie wystąpienia][36]

1. Po prawej stronie pojawi się edytor tekstu. Dodaj następujące pola:

   | | |
   | --- | --- |
   | **Typ** | Wybierz pozycję `Chiller` |
   | **Opis** | Wprowadź wartość `Instance for Chiller-01.1` |
   | **Hierarchies** | Włącz opcję `Location Hierarchy` |
   | **Country** | Wprowadź wartość `USA` |
   | **City** | Wprowadź wartość `Seattle` |
   | **Building** | Wprowadź wartość `Space Needle` |

    Po wypełnieniu powyższych pól kliknij przycisk **Zapisz**.

   ![Zapisywanie chłodni][37]

1. Powtórz poprzedni krok dla pozostałych czujników. Użyj następujących pól:

   * Dla chłodni Chiller 01.2:

     | | |
     | --- | --- |
     | **Typ** | Wybierz pozycję `Chiller` |
     | **Opis** | Wprowadź wartość `Instance for Chiller-01.2` |
     | **Hierarchies** | Włącz opcję `Location Hierarchy` |
     | **Country** | Wprowadź wartość `USA` |
     | **City** | Wprowadź wartość `Seattle` |
     | **Building** | Wprowadź wartość `Pacific Science Center` |

   * Dla chłodni Chiller 01.3:

     | | |
     | --- | --- |
     | **Typ** | Wybierz pozycję `Chiller` |
     | **Opis** | Wprowadź wartość `Instance for Chiller-01.1` |
     | **Hierarchies** | Włącz opcję `Location Hierarchy` |
     | **Country** | Wprowadź wartość `USA` |
     | **City** | Wprowadź wartość `New York` |
     | **Building** | Wprowadź wartość `Empire State Building` |

1. Przejdź do karty **Analiza**, a następnie odśwież stronę. Rozwiń wszystkie poziomy hierarchii, aby znaleźć szereg czasowy.

   ![Wyświetlanie karty analiz][38]

1. Aby zobaczyć szereg czasowy z ostatniej godziny, zmień wartość opcji **Szybkie czasy** na ostatnią godzinę:

   ![Przeglądanie ostatniej godziny][39]

1. Kliknij szereg czasowy w obszarze **Pacific Science Center** i kliknij pozycję **Show Max Humidity** (Wyświetl wilgotność maksymalną).

   ![Wyświetlanie wilgotności maksymalnej][40]

1. Zostanie wyświetlony szereg czasowy **Max Humidity** o wielkości interwału równej jednej minucie. Aby odfiltrować zakres, kliknij lewym przyciskiem myszy region. Następnie kliknij prawym przyciskiem myszy i powiększ obszar, aby przeanalizować zdarzenia w przedziale czasowym:

   ![Wyświetlanie, filtrowanie i powiększanie][41]

   ![Wyświetlanie, filtrowanie i powiększanie][42]

1. Możesz również kliknąć lewym przyciskiem myszy region, a następnie kliknąć prawym przyciskiem myszy, aby wyświetlić szczegóły zdarzenia:

   ![Wyświetlanie, filtrowanie i powiększanie][43]

   ![Wyświetlanie, filtrowanie i powiększanie][44]

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:  

* Tworzenie i używanie akceleratora symulacji urządzenia.
* Tworzenie środowiska wersji zapoznawczej usługi Azure Time Series Insights dostępnej w modelu PAYG.
* Łączenie środowiska wersji zapoznawczej usługi Azure Time Series Insights z centrum zdarzeń.
* Uruchamianie symulacji farmy wiatrowej umożliwiającej strumieniowe przesyłanie danych do środowiska wersji zapoznawczej usługi Azure Time Series Insights.
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
[6]: media/v2-update-provision/device-six-listings.png
[7]: media/v2-update-provision/device-seven-dashboard.png
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