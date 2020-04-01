---
title: 'Samouczek: Konfigurowanie środowiska w wersji zapoznawczej — usługa Azure Time Series Insights | Dokumenty firmy Microsoft'
description: 'Samouczek: Dowiedz się, jak skonfigurować środowisko w usłudze Azure Time Series Insights Preview.'
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 02/20/2020
ms.custom: seodec18
ms.openlocfilehash: af15a7366fd07cecb376ff76ad383f784202a887
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77526822"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Samouczek: Konfigurowanie środowiska usługi Azure Time Series Insights Preview

W tym samouczku można przejść przez proces tworzenia środowiska *payg* usługi Azure Time Series Insights Preview (PAYG).

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Tworzenie środowiska wersji zapoznawczej usługi Azure Time Series Insights.
> * Połącz środowisko usługi Azure Time Series Insights Preview z centrum IoT Hub.
> * Uruchamianie przykładowego akceleratora rozwiązań umożliwiającego strumieniowe przesyłanie danych do środowiska wersji zapoznawczej usługi Azure Time Series Insights.
> * Wykonywanie podstawowej analizy danych.
> * Definiowanie hierarchii oraz typu modelu szeregów czasowych i kojarzenie ich z wystąpieniami.
> * Użyj łącznika usługi Power BI i wizualizuj dane w usłudze Power BI.

>[!TIP]
> [Akceleratory rozwiązań IoT](https://www.azureiotsolutions.com/Accelerators) zapewniają wstępnie skonfigurowane rozwiązania klasy korporacyjnej, których można użyć do przyspieszenia opracowywania niestandardowych rozwiązań IoT.

Zarejestruj się, aby uzyskać [bezpłatną subskrypcję platformy Azure,](https://azure.microsoft.com/free/) jeśli jeszcze jej nie masz.

## <a name="prerequisites"></a>Wymagania wstępne

* Co najmniej musisz mieć rolę **współautora** dla subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem przy użyciu kontroli dostępu opartej na rolach i witryny Azure portal](../role-based-access-control/role-assignments-portal.md).

## <a name="create-a-device-simulation"></a>Tworzenie symulacji urządzeń

W tej sekcji utworzysz trzy symulowane urządzenia, które wysyłają dane do wystąpienia usługi Azure IoT Hub.

1. Przejdź do [strony akceleratorów rozwiązań Azure IoT](https://www.azureiotsolutions.com/Accelerators). Na stronie będzie widocznych kilka gotowych przykładów. Zaloguj się przy użyciu konta platformy Azure. Następnie wybierz pozycję **Symulacja urządzenia**.

   [![Strona akceleratorów rozwiązań IoT platformy Azure.](media/v2-update-provision/iot-solution-accelerators-landing-page.png)](media/v2-update-provision/iot-solution-accelerators-landing-page.png#lightbox)

1. Na następnej stronie wybierz pozycję **Wypróbuj teraz**. Następnie wprowadź wymagane parametry na stronie **Tworzenie rozwiązania symulacji urządzeń.**

   Parametr|Opis
   ---|---
   **Nazwa wdrożenia** | Ta unikatowa wartość jest używana do tworzenia nowej grupy zasobów. Znajdujące się na liście zasoby platformy Azure są tworzone i przypisywane do grupy zasobów.
   **Subskrypcja platformy Azure** | Określ tę samą subskrypcję, która została użyta do utworzenia środowiska usługi Time Series Insights w poprzedniej sekcji.
   **Opcje wdrażania** | Wybierz **pozycję Aprowizuj nowy Centrum IoT,** aby utworzyć nowy centrum IoT specyficzne dla tego samouczka.
   **Lokalizacja platformy Azure** | Określ ten sam region, który został użyty do utworzenia środowiska usługi Time Series Insights w poprzedniej sekcji.

   Po zakończeniu wybierz pozycję **Utwórz,** aby aprowizować zasoby platformy Azure rozwiązania. Ukończenie tego procesu może potrwać do 20 minut.

   [![Aprowizuj rozwiązanie symulacji urządzenia.](media/v2-update-provision/iot-solution-accelerators-configuration.png)](media/v2-update-provision/iot-solution-accelerators-configuration.png#lightbox)

1. Po zakończeniu inicjowania obsługi administracyjnej pojawią się dwa powiadomienia informujące o przeniesieniu stanu wdrożenia z **inicjowania obsługi administracyjnej** do **gotowości.** 

   >[!IMPORTANT]
   > Nie wprowadzaj jeszcze akceleratora rozwiązań! Zachowaj tę stronę internetową otwartą, ponieważ powrócisz do niej później.

   [![Kompletne aprowizacji rozwiązań symulacji urządzeń.](media/v2-update-provision/iot-solution-accelerator-ready.png)](media/v2-update-provision/iot-solution-accelerator-ready.png#lightbox)

1. Teraz sprawdź nowo utworzone zasoby w witrynie Azure portal. Na **stronie Grupy zasobów** zwróć uwagę, że nowa grupa zasobów została utworzona przy użyciu **nazwy rozwiązania** podanej w ostatnim kroku. Zanotuj zasoby utworzone dla symulacji urządzenia.

   [![Zasoby symulacji urządzeń.](media/v2-update-provision/tsi-device-sim-solution-resources.png)](media/v2-update-provision/tsi-device-sim-solution-resources.png#lightbox)

## <a name="create-a-preview-payg-environment"></a>Tworzenie środowiska PAYG w wersji zapoznawczej

W tej sekcji opisano sposób tworzenia środowiska usługi Azure Time Series Insights Preview i łączenia go z centrum IoT utworzone przez Akcelerator rozwiązań IoT przy użyciu [witryny Azure portal.](https://portal.azure.com/)

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta subskrypcji platformy Azure. 
1. W menu u góry po lewej stronie wybierz pozycję **+ Utwórz zasób**. 
1. Wybierz kategorię **Internet rzeczy**, a następnie wybierz pozycję **Time Series Insights**. 

   [![Wybierz zasób środowiska usługi Time Series Insights.](media/v2-update-provision/tsi-create-new-environment.png)](media/v2-update-provision/tsi-create-new-environment.png#lightbox)

1. W okienku **środowiska Tworzenie usługi Time Series Insights** na karcie **Podstawy** ustaw następujące parametry:

    | Parametr | Akcja |
    | --- | ---|
    | **Nazwa środowiska** | Wprowadź unikatową nazwę środowiska usługi Azure Time Series Insights Preview. |
    | **Subskrypcja** | Wprowadź subskrypcję, w której chcesz utworzyć środowisko usługi Azure Time Series Insights Preview. Najlepszym rozwiązaniem jest użycie tej samej subskrypcji, co pozostałe zasoby IoT, które są tworzone przez symulator urządzenia. |
    | **Grupa zasobów** | Wybierz istniejącą grupę zasobów lub utwórz nową grupę zasobów dla zasobu środowiska usługi Azure Time Series Insights Preview. Grupa zasobów jest kontenerem zasobów platformy Azure. Najlepszym rozwiązaniem jest użycie tej samej grupy zasobów, co inne zasoby IoT, które są tworzone przez symulator urządzenia. |
    | **Lokalizacja** | Wybierz region centrum danych dla środowiska usługi Azure Time Series Insights Preview. Aby uniknąć dodatkowych opóźnień, najlepiej utworzyć środowisko usługi Azure Time Series Insights Preview w tym samym regionie co centrum IoT utworzone przez symulator urządzenia. |
    | **Warstwa** |  Wybierz **PAYG** (*płatność zgodnie z rzeczywistymi oczekiwaniami).* Jest to jednostka SKU produktu Azure Time Series Insights w wersji zapoznawczej. |
    | **Nazwa właściwości** | Wprowadź wartość, która jednoznacznie identyfikuje wystąpienie szeregów czasowych. Nie można później zmienić wartości wprowadzonej w polu **Identyfikator właściwości.** W tym samouczku wprowadź ***iothub-connection-device-id***. Aby dowiedzieć się więcej o identyfikatorze szeregów czasowych, zapoznaj się [z najlepszymi rozwiązaniami dotyczącymi wybierania identyfikatora szeregów czasowych.](./time-series-insights-update-how-to-id.md) |
    | **Nazwa konta magazynu** | Wprowadź unikatową globalnie nazwę nowego konta magazynu.|
    |**Włącz ciepły magazyn**|Wybierz **opcję Tak,** aby włączyć ciepły magazyn. Możesz wrócić później i włączyć to ustawienie. |
    |**Przechowywanie danych (w dniach)**|Wybierz domyślną opcję 7 dni. |

    Wybierz **dalej: Źródło zdarzenia**.

   [![Nowa konfiguracja środowiska usługi Time Series Insights.](media/v2-update-provision/tsi-environment-configuration.png)](media/v2-update-provision/tsi-environment-configuration.png#lightbox)

   [![Skonfiguruj identyfikator szeregów czasowych dla środowiska.](media/v2-update-provision/tsi-time-series-id-selection.png)](media/v2-update-provision/tsi-time-series-id-selection.png#lightbox)

1. Na karcie **Źródło zdarzenia** ustaw następujące parametry:

   | Parametr | Akcja |
   | --- | --- |
   | **Czy utworzyć źródło zdarzeń?** | Wybierz **pozycję Tak**.|
   | **Nazwa** | Wprowadź unikatową wartość nazwy źródła zdarzenia. |
   | **Typ źródła** | Wybierz **Centrum IoT**. |
   | **Wybieranie koncentratora** | Wybierz **pozycję Wybierz istniejący**plik . |
   | **Subskrypcja** | Wybierz subskrypcję używaną dla symulatora urządzenia. |
   | **Nazwa Centrum IoT** | Wybierz nazwę centrum IoT utworzone dla symulatora urządzenia. |
   | **Zasady dostępu do centrum IoT Hub** | Wybierz pozycję **iothubowner**. |
   | **Grupa odbiorców usługi IoT Hub** | Wybierz **pozycję Nowy**, wprowadź unikatową nazwę, a następnie wybierz pozycję + **Dodaj**. Grupa odbiorców musi być unikatową wartością w usłudze Azure Time Series Insights Preview. |
   | **Właściwość znacznika czasu** | Ta wartość służy do identyfikowania właściwości **sygnatury czasowej** w przychodzących danych telemetrycznych. W tym samouczku pozostaw to pole puste. Ten symulator używa sygnatury czasowej pochodzącej z usługi IoT Hub, domyślnej wartości dla usługi Time Series Insights. |

   Wybierz **pozycję Recenzja + Utwórz**.

   [![Skonfiguruj utworzone centrum IoT jako źródło zdarzeń.](media/v2-update-provision/tsi-configure-event-source.png)](media/v2-update-provision/tsi-configure-event-source.png#lightbox)

1. Wybierz **pozycję Utwórz**.

    [![Recenzja + Utwórz stronę za pomocą przycisku Utwórz.](media/v2-update-provision/tsi-environment-confirmation.png)](media/v2-update-provision/tsi-environment-confirmation.png#lightbox)

    Możesz przejrzeć stan wdrożenia:

    [![Powiadomienie o zakończeniu wdrażania.](media/v2-update-provision/tsi-deployment-notification.png)](media/v2-update-provision/tsi-deployment-notification.png#lightbox)

1. Masz domyślnie dostęp do środowiska usługi Azure Time Series Preview, jeśli jesteś właścicielem subskrypcji platformy Azure. Sprawdź, czy masz dostęp:

   1. Wyszukaj grupę zasobów, a następnie wybierz nowo utworzone środowisko usługi Azure Time Series Insights Preview. 

      [![Wybierz i wyświetl swoje środowisko.](media/v2-update-provision/verify-tsi-resource-in-group.png)](media/v2-update-provision/verify-tsi-resource-in-group.png#lightbox)

   1. Na stronie Usługi Azure Time Series Insights Preview wybierz pozycję **Zasady dostępu do danych:**

      [![Weryfikuj zasady dostępu do danych.](media/v2-update-provision/tsi-data-access-panel.png)](media/v2-update-provision/tsi-data-access-panel.png#lightbox)

   1. Sprawdź, czy poświadczenia są wymienione:

      Jeśli poświadczeń nie ma na liście, należy udzielić sobie uprawnień dostępu do środowiska, wybierając pozycję Dodaj i wyszukując poświadczenia. Aby dowiedzieć się więcej o ustawianiu uprawnień, zobacz [Grant data access (Przyznawanie dostępu do danych)](./time-series-insights-data-access.md).

## <a name="stream-data"></a>Strumieniowe przesyłanie danych

Teraz, gdy wdrożono środowisko usługi Time Series Insights, rozpocznij przesyłanie strumieniowe danych do analizy.

1. Wróć do swojego [Pulpitu nawigacyjnego akceleratorów rozwiązania](https://www.azureiotsolutions.com/Accelerators#dashboard). Zaloguj się ponownie, jeśli to konieczne, przy użyciu tego samego konta platformy Azure, którego używasz w tym samouczku. Wybierz "Rozwiązanie urządzenia", a następnie **przejdź do akceleratora rozwiązań,** aby uruchomić wdrożone rozwiązanie.

   [![Pulpit nawigacyjny akceleratorów rozwiązań.](media/v2-update-provision/iot-solution-accelerator-ready.png)](media/v2-update-provision/iot-solution-accelerator-ready.png#lightbox)

1. Aplikacja internetowa symulacji urządzenia rozpoczyna się od monitowania o udzielenie aplikacji sieci web **logowania użytkownika i przeczytanie** uprawnień do profilu. To uprawnienie umożliwia aplikacji pobranie informacji o profilu użytkownika niezbędnych do obsługi funkcjonowania aplikacji.

   [![Zgoda aplikacji internetowej symulacji urządzenia.](media/v2-update-provision/sawa-signin-consent.png)](media/v2-update-provision/sawa-signin-consent.png#lightbox)

1. Wybierz **+ Nowa symulacja**. Po **załadowaniu** strony ustawienia symulacji wprowadź wymagane parametry.

    | Parametr | Akcja |
    | --- | --- |
    | **Nazwa** | Wprowadź unikatową nazwę symulatora. |
    | **Opis** | Wprowadź definicję. |
    | **Czas trwania symulacji** | Wybierz opcję **Uruchamianie bez ograniczeń**. |
    | **Model urządzenia** | Kliknij + **Dodaj typ urządzenia** <br />**Nazwa**: Wprowadź **windę**. <br />**Kwota**: Wprowadź **3**. <br /> Pozostaw pozostałe wartości domyślne |
    | **Docelowa usługa IoT Hub** | Wybierz pozycję **Użyj dostarczonej wstępnie usługi IoT Hub**. |

    [![Skonfiguruj parametry i uruchom.](media/v2-update-provision/tsi-launch-solution-accelerator.png)](media/v2-update-provision/tsi-launch-solution-accelerator.png#lightbox)

    Wybierz **start symulacji**.

    Na pulpicie nawigacyjnym symulacji urządzenia wyświetlane są **aktywne urządzenia** i **komunikaty Razem.**

    [![Pulpit nawigacyjny symulacji usługi Azure IoT.](media/v2-update-provision/tsi-see-active-devices-and-messages.png)](media/v2-update-provision/tsi-see-active-devices-and-messages.png#lightbox)

## <a name="analyze-data"></a>Analizowanie danych

W tej sekcji przeprowadzimy podstawową analizę danych szeregów czasowych przy użyciu [eksploratora wersji zapoznawczej usługi Azure Time Series Insights](./time-series-insights-update-explorer.md).

1. Przejdź do eksploratora wersji zapoznawczej usługi Azure Time Series Insights, wybierając adres URL na stronie zasobu w witrynie [Azure Portal](https://portal.azure.com/).

    [![Adres URL Eksploratora wglądu w statystyki szeregów czasowych.](media/v2-update-provision/tsi-select-explorer-url.png)](media/v2-update-provision/tsi-select-explorer-url.png#lightbox)

1. W Eksploratorze usługi Time Series Insights pojawi się pasek obejmujący górną część ekranu. To jest twój selektor dostępności. Upewnij się, że wybrano co najmniej dwa 2 m, a w razie potrzeby rozwiń przedział czasu, zaznaczając i przeciągając uchwyty selektora w lewo i w prawo.

1. **Wystąpienia szeregów czasowych** będą wyświetlane po lewej stronie.

    [![Lista wystąpień nieparzeszonych.](media/v2-update-provision/tsi-explorer-unparented-instances.png)](media/v2-update-provision/tsi-explorer-unparented-instances.png#lightbox)

1. Wybierz wystąpienie serii po raz pierwszy. Następnie wybierz pozycję **Pokaż temperaturę**.

    [![Wybrane wystąpienie szeregów czasowych z poleceniem menu, aby pokazać średnią temperaturę.](media/v2-update-provision/select-instance-and-temperature.png)](media/v2-update-provision/select-instance-and-temperature.png#lightbox)

    Pojawi się wykres szeregów czasowych. Zmień **interwał** na **30s**.

1. Powtórz poprzedni krok z pozostałymi dwoma wystąpieniami szeregów czasowych, aby wyświetlać wszystkie trzy, jak pokazano na tym wykresie:

    [![Wykres dla wszystkich szeregów czasowych.](media/v2-update-provision/tsi-explorer-add-three-instances.png)](media/v2-update-provision/tsi-explorer-add-three-instances.png#lightbox)

1. Wybierz selektor przedziału czasu w prawym górnym rogu. W tym miejscu można wybrać określone godziny rozpoczęcia i zakończenia w dół do milisekundy lub wybrać jedną z wstępnie skonfigurowanych opcji, takich jak **Ostatnie 30 minut**. Można również zmienić domyślną strefę czasową.

    [![Ustaw zakres czasu na ostatnie 30 minut.](media/v2-update-provision/tsi-explorer-thirty-minute-time-range.png)](media/v2-update-provision/tsi-explorer-thirty-minute-time-range.png#lightbox)

    Postęp akceleratora rozwiązań w ciągu **ostatnich 30 minut** jest teraz wyświetlany w Eksploratorze Usługi Time Series Insights.

## <a name="define-and-apply-a-model"></a>Definiowanie i stosowanie modelu

W tej sekcji zastosujesz model, aby ustrukturyzować dane. Do utworzenia modelu potrzebne są definicje typów, hierarchii i wystąpień. Aby dowiedzieć się więcej o modelowaniu danych, przeczytaj [artykuł Model szeregów czasowych](./time-series-insights-update-tsm.md).

1. W eksploratorze wybierz kartę **Model**:

   [![Wyświetl kartę Model w eksploratorze.](media/v2-update-provision/tsi-select-model-view.png)](media/v2-update-provision/tsi-select-model-view.png#lightbox)

   Na karcie **Typy** wybierz pozycję **+ Dodaj**.

1. Wprowadź następujące parametry:

    | Parametr | Akcja |
    | --- | ---|
    | **Nazwa** | Wejdź do **windy** |
    | **Opis** | Enter **Jest to definicja typu dla windy** |

1. Następnie wybierz kartę **Zmienne.** 

   Wybierz **+ Dodaj zmienną** i wypełnij następujące wartości dla pierwszej zmiennej typu Winda. W sumie zostaną zasiądą trzy zmienne.

    | Parametr | Akcja |
    | --- | --- |
    | **Nazwa** | Wpisz **Avg Temperature** (Temperatura średnia). |
    | **Rodzaju** | Wybierz **numeryczne** |
    | **Wartość** | Wybierz z ustawień predefiniowanych: Wybierz **temperaturę (Podwójna)**. <br /> Uwaga: Może upłynąć kilka minut, aby **wartość** została automatycznie wypełniona po rozpoczęciu odbierania zdarzeń przez usługę Azure Time Series Insights Preview.|
    | **Operacja agregacji** | Rozwiń **opcje zaawansowane**. <br /> Wybierz opcję **AVG** (średnia). |

    Wybierz przycisk **Zastosuj**. Następnie **+ Dodaj zmienną** ponownie i ustaw następujące wartości:

    | Parametr | Akcja |
    | --- | --- |
    | **Nazwa** | Wprowadź **śrę wibracji**. |
    | **Rodzaju** | Wybierz **numeryczne** |
    | **Wartość** | Wybierz z ustawień predefiniowanych: Wybierz **wibracje (Podwójne)**. <br /> Uwaga: Może upłynąć kilka minut, aby **wartość** została automatycznie wypełniona po rozpoczęciu odbierania zdarzeń przez usługę Azure Time Series Insights Preview.|
    | **Operacja agregacji** | Rozwiń **opcje zaawansowane**. <br /> Wybierz opcję **AVG** (średnia). |

    Wybierz przycisk **Zastosuj**. Następnie **+ Dodaj zmienną** ponownie i ustaw następujące wartości dla trzeciej i ostatniej zmiennej:

    | Parametr | Akcja |
    | --- | --- |
    | **Nazwa** | Wprowadź **podłogę**. |
    | **Rodzaju** | Wybierz **kategoryczny** |
    | **Wartość** | Wybierz z ustawień predefiniowanych: Wybierz **Podłogę (Podwójna)**. <br /> Uwaga: Może upłynąć kilka minut, aby **wartość** została automatycznie wypełniona po rozpoczęciu odbierania zdarzeń przez usługę Azure Time Series Insights Preview.|
    | **Kategorie** | <span style="text-decoration: underline">Label </span>   -  <span style="text-decoration: underline">Wartości</span> etykiet <br /> Niższy: 1,2,3,4 <br /> Środek: 5,6,7,8,9 <br /> Górna: 10,11,12,13,14,15 |
    | **Kategoria domyślna** | Wprowadź **nieznany** |

    [![Dodaj zmienne typu.](media/v2-update-provision/tsi-add-type-variables.png)](media/v2-update-provision/tsi-add-type-variables.png#lightbox)

    Wybierz przycisk **Zastosuj**.

1. Wybierz **pozycję Zapisz**. Tworzone i wyświetlane są trzy zmienne.

    [![Po dodaniu typu przejrzyj go w widoku Modelu.](media/v2-update-provision/tsi-add-type-and-view.png)](media/v2-update-provision/tsi-add-type-and-view.png#lightbox)

1. Wybierz kartę **Hierarchie.** Następnie wybierz pozycję **+ Dodaj**.
   
   W okienku **Edytowanie hierarchii** ustaw następujące parametry:

   | Parametr | Akcja |
   | --- | ---|
   | **Nazwa** | Wpisz **Location Hierarchy** (Hierarchia lokalizacji). |
   |**Poziomy**| Wprowadź **kraj** jako nazwę pierwszego poziomu <br> Wybierz **+ Dodaj poziom** <br> Wprowadź **miasto** dla drugiego poziomu, a następnie wybierz **+ Dodaj poziom** <br> Wprowadź **budynek** jako nazwę trzeciego i ostatniego poziomu |

   Wybierz **pozycję Zapisz**.

   [![Wyświetlanie nowej hierarchii w widoku Modelu.](media/v2-update-provision/tsi-add-hierarchy-and-view.png)](media/v2-update-provision/tsi-add-hierarchy-and-view.png#lightbox)

1. Przejdź do **pozycji Wystąpienia**. W **obszarze Akcje** po prawej stronie wybierz ikonę ołówka, aby edytować pierwsze wystąpienie z następującymi wartościami:

    | Parametr | Akcja |
    | --- | --- |
    | **Typ** | Wybierz **windę**. |
    | **Nazwa** | Wejdź **do windy 1**|
    | **Opis** | Wprowadź **wystąpienie windy 1** |

    Przejdź do **pól wystąpień** i wprowadź następujące wartości:

    | Parametr | Akcja |
    | --- | --- |
    | **Hierarchies** | Wybieranie **hierarchii lokalizacji** |
    | **Country** | Wejdź do **USA** |
    | **Miasta** | Wprowadź **Seattle** |
    | **Building** | Wprowadź **igłę kosmiczną** |

    Wybierz **pozycję Zapisz**.

1. Powtórz poprzedni krok z pozostałymi dwoma wystąpieniami, używając następujących wartości:

    **Dla windy 2:**

    | Parametr | Akcja |
    | --- | --- |
    | **Typ** | Wybierz **windę**. |
    | **Nazwa** | Wejdź **do windy 2**|
    | **Opis** | Wprowadź **wystąpienie windy 2** |
    | **Hierarchies** | Wybieranie **hierarchii lokalizacji** |
    | **Country** | Wejdź do **USA** |
    | **Miasta** | Wprowadź **Seattle** |
    | **Building** | Wejdź do **Centrum Nauki Pacyfiku** |

    **Dla windy 3:**

    | Parametr | Akcja |
    | --- | --- |
    | **Typ** | Wybierz **windę**. |
    | **Nazwa** | Wejdź **do windy 3**|
    | **Opis** | Wprowadź **wystąpienie windy 3** |
    | **Hierarchies** | Wybieranie **hierarchii lokalizacji** |
    | **Country** | Wejdź do **USA** |
    | **Miasta** | Wjazd Do **Nowego Jorku** |
    | **Building** | Wejdź do **Empire State Building** |

    [![Wyświetl zaktualizowane wystąpienia.](media/v2-update-provision/iot-solution-accelerator-instances.png)](media/v2-update-provision/iot-solution-accelerator-instances.png#lightbox)

1. Przejdź wstecz do karty **Analizowanie,** aby wyświetlić okienko wykresów. W obszarze **Hierarchia lokalizacji**rozwiń wszystkie poziomy hierarchii, aby wyświetlić wystąpienia szeregów czasowych:

    [![Służy do wyświetlania wszystkich hierarchii w widoku wykresu.](media/v2-update-provision/iot-solution-accelerator-view-hierarchies.png)](media/v2-update-provision/iot-solution-accelerator-view-hierarchies.png#lightbox)

1. W obszarze **Centrum nauki pacyfiku**wybierz **windę instancji szeregów czasowych 2**, a następnie wybierz pozycję **Pokaż średnią temperaturę**.

1. W tym samym **wystąpieniu, Winda 2**, wybierz **Pokaż podłogę**.

    Za pomocą zmiennej kategorycznej możesz określić, ile czasu winda spędziła na wyższych, dolnych i środkowych piętrach.

    [![Wizualizuj windę 2 z hierarchią i danymi.](media/v2-update-provision/iot-solution-accelerator-elevator-two.png)](media/v2-update-provision/iot-solution-accelerator-elevator-two.png#lightbox)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu samouczka posprzątaj utworzone zasoby:

1. Z lewego menu w [witrynie Azure portal](https://portal.azure.com)wybierz **pozycję Wszystkie zasoby**— znajdź grupę zasobów usługi Azure Time Series Insights.
1. Usuń całą grupę zasobów (i wszystkie zasoby w niej zawarte), wybierając **pozycję Usuń** lub usuń każdy zasób indywidualnie.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:  

* Tworzenie i używanie akceleratora symulacji urządzenia.
* Tworzenie środowiska wersji zapoznawczej usługi Azure Time Series Insights dostępnej w modelu PAYG.
* Połącz środowisko usługi Azure Time Series Insights Preview z centrum iot.
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
