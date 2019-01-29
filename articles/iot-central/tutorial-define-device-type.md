---
title: Definiowanie nowego typu urządzenia w usłudze Azure IoT Central | Microsoft Docs
description: Ten samouczek przedstawia, jak konstruktor może zdefiniować nowy typ urządzenia w aplikacji usługi Azure IoT Central. Dla typu można zdefiniować telemetrię, stan, właściwości i ustawienia.
author: dominicbetts
ms.author: dobett
ms.date: 10/30/2018
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 0aef8c5e055eb43576ea0ecc7ef8bf09d22d6ed3
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2019
ms.locfileid: "54412907"
---
# <a name="tutorial-define-a-new-device-type-in-your-azure-iot-central-application"></a>Samouczek: definiowanie nowego typu urządzenia w aplikacji usługi Azure IoT Central

Ten samouczek przedstawia, jak konstruktor może użyć szablonu urządzenia do zdefiniowania nowego typu urządzenia w aplikacji usługi Microsoft Azure IoT Central. Szablon urządzenia definiuje telemetrię, stan, właściwości i ustawienia dla danego typu urządzenia.

Aby umożliwić przetestowanie aplikacji przed podłączeniem rzeczywistego urządzenia, usługa IoT Central generuje symulowane urządzenie na podstawie szablonu urządzenia podczas jego tworzenia.

W tym samouczku utworzysz szablon urządzenia **Połączony klimatyzator**. Połączony klimatyzator:

* wysyła dane telemetryczne, takie jak temperatura i wilgotność;
* zgłasza stan, taki jak „włączony” lub „wyłączony”;
* ma właściwości urządzenia, takie jak wersja oprogramowania układowego i numer seryjny;
* ma ustawienia, takie jak temperatura docelowa.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie nowego szablonu urządzenia
> * Dodawanie telemetrii do urządzenia
> * Wyświetlanie symulowanych danych telemetrycznych
> * Definiowanie miary zdarzenia
> * Wyświetlanie symulowanych zdarzeń
> * Definiowanie miary stanu
> * Wyświetlanie symulowanego stanu
> * Korzystanie z ustawień i właściwości
> * Korzystanie z poleceń
> * Wyświetlanie symulowanego urządzenia na pulpicie nawigacyjnym

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka potrzebna jest aplikacja usługi Azure IoT Central. Jeśli ukończono przewodnik Szybki start [Tworzenie aplikacji usługi Azure IoT Central](quick-deploy-iot-central.md), możesz użyć ponownie aplikacji utworzonej w jego ramach. W przeciwnym przypadku wykonaj następujące kroki, aby utworzyć pustą aplikację usługi Azure IoT Central:

1. Przejdź na stronę [Menedżer aplikacji](https://aka.ms/iotcentral) usługi Azure IoT Central.

2. Podaj adres e-mail i hasło umożliwiające dostęp do subskrypcji platformy Azure:

   ![Określanie konta organizacji](./media/tutorial-define-device-type/sign-in.png)

3. Aby rozpocząć tworzenie nowej aplikacji usługi Azure IoT Central, wybierz pozycję **Nowa aplikacja**:

    ![Strona Menedżer aplikacji usługi Azure IoT Central](./media/tutorial-define-device-type/iotcentralhome.png)

4. Aby utworzyć nową aplikację usługi Azure IoT Central:
    
    * Wybierz pozycję **Wersja próbna**. Do tworzenia aplikacji w wersji próbnej nie potrzebujesz subskrypcji platformy Azure.
    
       Aby uzyskać więcej informacji na temat katalogów i subskrypcji, zapoznaj się z [przewodnikiem Szybki start dotyczącym tworzenia aplikacji](quick-deploy-iot-central.md).
    
    * Wybierz pozycję **Aplikacja niestandardowa**.
    
    * Opcjonalnie wybierz przyjazną nazwę aplikacji, taką jak **Klimatyzatory Contoso**. Usługa Azure IoT Central wygeneruje unikatowy prefiks adresu URL. Ten prefiks adresu URL możesz zmienić na łatwiejszy do zapamiętania.
    
    * Wybierz pozycję **Utwórz**.

    ![Strona Tworzenie aplikacji usługi Azure IoT Central](./media/tutorial-define-device-type/iotcentralcreate.png)

    Aby uzyskać więcej informacji, zapoznaj się z [przewodnikiem Szybki start dotyczącym tworzenia aplikacji](quick-deploy-iot-central.md).

## <a name="create-a-new-custom-device-template"></a>Tworzenie nowego niestandardowego szablonu urządzenia

Jako konstruktor możesz tworzyć i edytować szablony urządzenia w aplikacji. Podczas tworzenia szablonu urządzenia usługa Azure IoT Central generuje symulowane urządzenie na podstawie szablonu. Symulowane urządzenie generuje dane telemetryczne, który umożliwiają testowanie zachowania aplikacji przed podłączeniem prawdziwego urządzenia.

Aby dodać nowy szablon urządzenia do aplikacji, należy przejść na stronę **Konstruktor aplikacji**. W tym celu wybierz pozycję **Konstruktor aplikacji** z menu nawigacji po lewej stronie.

![Strona Konstruktor aplikacji](./media/tutorial-define-device-type/builderhome.png)

## <a name="add-a-device-and-define-telemetry"></a>Dodawanie urządzenia i definiowanie telemetrii

Poniższe kroki przedstawiają sposób tworzenia nowego szablonu urządzenia **Połączony klimatyzator** przeznaczonego dla urządzeń wysyłających do aplikacji dane telemetryczne dotyczące temperatury:

1. Na stronie **Konstruktor aplikacji** wybierz pozycję **Utwórz szablon urządzenia**:

    ![Strona Konstruktor aplikacji, Utwórz szablon urządzenia](./media/tutorial-define-device-type/builderhomedevices.png)

2. Na stronie **Szablony urządzenia** wybierz pozycję **Niestandardowy**. Szablon urządzenia **Niestandardowy** umożliwia zdefiniowanie wszystkich cech i zachowań połączonego klimatyzatora:

    ![Urządzenia](./media/tutorial-define-device-type/builderhomedevicescustom.png)

3. Na stronie **Nowy szablon urządzenia** podaj ciąg **Połączony klimatyzator** jako nazwę urządzenia, a następnie wybierz polecenie **Utwórz**. Istnieje możliwość przekazania obrazu urządzenia widocznego dla operatorów w eksploratorze urządzeń:

    ![Urządzenie niestandardowe](./media/tutorial-define-device-type/createcustomdevice.png)

4. Upewnij się, że dla szablonu urządzenia **Połączony klimatyzator** jest wyświetlona strona **Miary**, na której definiuje się telemetrię. Każdy szablon urządzenia ma oddzielne strony umożliwiające:

    * określenie miar wysyłanych przez urządzenie, takich jak telemetria, zdarzenie i stan;
    
    * definiowanie ustawień używanych do kontrolowania urządzenia;
    
    * definiowanie właściwości będących metadanymi urządzenia;

    * definiowanie poleceń do uruchamiania bezpośrednio na urządzeniu;
    
    * definiowanie reguł skojarzonych z urządzeniem;
    
    * dostosowanie pulpitu nawigacyjnego urządzenia dla operatorów.

    Zawsze podczas definiowania szablonu urządzenia wybierz najpierw pozycję **Edytuj szablon**, aby edytować szablon. Po zakończeniu wybierz pozycję **Gotowe**. 

    ![Miary klimatyzatora](./media/tutorial-define-device-type/airconmeasurements.png)

    > [!NOTE]
    > Aby zmienić nazwę urządzenia lub szablonu urządzenia, kliknij tekst w górnej części strony.

5. Aby dodać miarę telemetrii dla temperatury, wybierz pozycję **Nowa miara**. Następnie wybierz pozycję **Telemetria** jako typ miary:

    ![Miary połączonego klimatyzatora](./media/tutorial-define-device-type/airconmeasurementsnew.png)

6. Każdy typ telemetrii zdefiniowany dla szablonu urządzenia obejmuje [opcje konfiguracji](howto-set-up-template.md), takie jak:

    * opcje wyświetlania,

    * szczegółowe informacje o telemetrii,

    * parametry symulacji.

    Aby skonfigurować miarę telemetrii **Temperatura**, skorzystaj z informacji w poniższej tabeli:

    | Ustawienie              | Wartość         |
    | -------------------- | -----------   |
    | Nazwa wyświetlana         | Temperatura   |
    | Nazwa pola           | temperature   |
    | Jednostki                | F             |
    | Min.                  | 60            |
    | Maks.                  | 110           |
    | Miejsca dziesiętne       | 0             |

    Istnieje także możliwość wyboru koloru na potrzeby wyświetlania danych telemetrii. Aby zapisać definicję telemetrii, wybierz polecenie **Zapisz**:

    ![Konfigurowanie symulacji temperatury](./media/tutorial-define-device-type/temperaturesimulation.png)

7. Po krótkiej chwili na stronie **Miary** zostanie wyświetlony wykres danych telemetrii temperatury pochodzących z symulowanego połączonego klimatyzatora. Do zarządzania widocznością lub agregacją albo do edytowania definicji telemetrii użyj kontrolek:

    ![Wyświetlenie symulacji temperatury](./media/tutorial-define-device-type/viewsimulation.png)

8. Istnieje także możliwość dostosowania wykresu przy użyciu kontrolek **Wiersz**, **Skumulowany** i **Edytuj zakres czasu**:

    ![Dostosowywanie wykresu](./media/tutorial-define-device-type/customizechart.png)

## <a name="define-event-measurement"></a>Definiowanie miary zdarzenia

Zdarzenie umożliwia zdefiniowanie danych punktu w czasie wysyłanych przez urządzenie i oznaczających znaczące wydarzenie, takie jak błąd lub awaria składnika. Usługa Azure IoT Central może symulować zdarzenia urządzenia, podobnie jak miary telemetrii, aby umożliwić testowanie zachowania aplikacji przed podłączeniem prawdziwego urządzenia. Miary zdarzenia dla typu urządzenia definiuje się w widoku **Miary**.

1. Aby dodać miarę zdarzenia **Błąd silnika wentylatora**, wybierz pozycję **Nowa miara**. Następnie wybierz pozycję **Zdarzenie** jako typ miary:

    ![Miary połączonego klimatyzatora](./media/tutorial-define-device-type/eventnew.png)

2. Każdy typ zdarzenia zdefiniowany dla szablonu urządzenia obejmuje [opcje konfiguracji](howto-set-up-template.md), takie jak:

   * Nazwa wyświetlana

   * Nazwa pola

   * Ważność

    Aby skonfigurować pozycję telemetrii **Błąd silnika wentylatora**, skorzystaj z informacji w poniższej tabeli:

    | Ustawienie              | Wartość             |
    | -------------------- | -----------       |
    | Nazwa wyświetlana         | Błąd silnika wentylatora   |
    | Nazwa pola           | fanmotorerr       |
    | Ważność             | Błąd             |

    Aby zapisać definicję zdarzenia, wybierz polecenie **Zapisz**:

    ![Konfigurowanie miary zdarzenia](./media/tutorial-define-device-type/eventconfiguration.png)

3. Po krótkiej chwili na stronie **Miary** zostanie wyświetlony wykres dla zdarzeń wygenerowanych losowo przez symulowany połączony klimatyzator. Do zarządzania widocznością lub edytowania definicji zdarzenia użyj kontrolek:

    ![Wyświetlanie symulacji zdarzenia](./media/tutorial-define-device-type/eventview.png)

1. Aby wyświetlić dodatkowe szczegóły dotyczące zdarzenia, kliknij zdarzenie na wykresie:

    ![Wyświetlanie szczegółów zdarzenia](./media/tutorial-define-device-type/eventviewdetail.png)

## <a name="define-state-measurement"></a>Definiowanie miary stanu

Stan służy do definiowania i wizualizowania stanu urządzenia lub jego składników w danym okresie. Usługa Azure IoT Central może symulować stan urządzenia, podobnie jak miary telemetrii, aby umożliwić testowanie zachowania aplikacji przed podłączeniem prawdziwego urządzenia. Miary stanu dla typu urządzenia definiuje się w widoku **Miary**.

1. Aby dodać miarę **Tryb wentylatora**, wybierz pozycję **Nowa miara**. Następnie wybierz pozycję **Stan** jako typ miary:

    ![Miary stanu połączonego klimatyzatora](./media/tutorial-define-device-type/statenew.png)

2. Każdy typ stanu zdefiniowany dla szablonu urządzenia obejmuje [opcje konfiguracji](howto-set-up-template.md), takie jak:

   * Nazwa wyświetlana

   * Nazwa pola

   * Wartości z opcjonalnymi etykietami

   * Kolor dla każdej wartości

    Aby skonfigurować stan **Tryb wentylatora**, skorzystaj z informacji w poniższej tabeli:

    | Ustawienie              | Wartość             |
    | -------------------- | -----------       |
    | Nazwa wyświetlana         | Tryb wentylatora          |
    | Nazwa pola           | fanmode           |
    | Wartość                | 1                 |
    | Wyświetlana etykieta        | Działa         |
    | Wartość                | 0                 |
    | Wyświetlana etykieta        | Zatrzymano           |

    Aby zapisać definicję miary stanu, wybierz polecenie **Zapisz**:

    ![Konfigurowanie miary stanu](./media/tutorial-define-device-type/stateconfiguration.png)

3. Po krótkiej chwili na stronie **Miary** zostanie wyświetlony wykres stanów wygenerowanych losowo przez symulowany połączony klimatyzator. Do zarządzania widocznością lub edytowania definicji stanu użyj kontrolek:

    ![Widok symulacji stanu](./media/tutorial-define-device-type/stateview.png)

4. Jeśli w krótkim okresie urządzenie wyśle za dużo punktów danych, miara stanu będzie wyświetlana za pomocą innej wizualizacji, jak pokazano poniżej. Jeśli klikniesz wykres, wszystkie punkty danych w danym okresie zostaną wyświetlone w kolejności chronologicznej. Zakres czasu można także zawęzić, aby wyświetlić miarę bardziej szczegółowo.

    ![Wyświetlanie szczegółów stanu](./media/tutorial-define-device-type/stateviewdetail.png)

## <a name="settings-properties-and-commands"></a>Ustawienia, właściwości i polecenia

Ustawienia, właściwości oraz polecenia to różne wartości zdefiniowane w szablonie urządzenia i skojarzone z poszczególnymi urządzeniami:

* _Ustawienia_ umożliwiają wysyłanie danych konfiguracji do urządzenia z aplikacji. Na przykład operator może użyć ustawienia do zmiany interwału telemetrii urządzenia z dwóch na pięć sekund. Gdy operator zmieni ustawienie, jest ono oznaczone jako oczekujące w interfejsie użytkownika, dopóki urządzenie nie potwierdzi, że wykonało odpowiednie działanie dla zmiany ustawień.

* _Właściwości_ możesz użyć do definiowania metadanych skojarzonych z urządzeniem. Są dostępne dwie kategorie właściwości:
    
    * _Właściwości aplikacji_ umożliwiają rejestrowanie informacji o urządzeniu w aplikacji. Na przykład możesz użyć właściwości aplikacji do zarejestrowania lokalizacji urządzenia i daty ostatniego serwisowania. Właściwości są przechowywane w aplikacji i nie są synchronizowane z urządzeniem. Operator może przypisać wartości do właściwości.

    * _Właściwości urządzenia_ umożliwiają urządzaniu wysyłanie wartości właściwości do aplikacji. Te właściwości może zmienić tylko urządzenie. Dla operatora właściwości urządzenia są tylko do odczytu. W tym scenariuszu z połączonym klimatyzatorem wersja oprogramowania układowego i numer seryjny urządzenia to właściwości urządzenia zgłoszone przez urządzenie. 
    
    Aby uzyskać więcej informacji, zobacz [Właściwości][lnk-define-template] w instrukcjach konfigurowania szablonu urządzenia.

* _Polecenia_ służą do zdalnego zarządzania urządzeniem z poziomu aplikacji. Polecenia można uruchamiać z chmury bezpośrednio na urządzeniu, aby sterować urządzeniami. Na przykład operator może uruchamiać polecenia, takie jak reboot, aby natychmiast ponownie uruchomić urządzenie.

## <a name="use-settings"></a>Używanie ustawień

*Ustawienia* umożliwiają operatorowi wysyłanie danych konfiguracji do urządzenia. W tej sekcji do szablonu urządzenia **Połączony klimatyzator** jest dodawane ustawienie, które umożliwia operatorowi ustawienie temperatury docelowej połączonego klimatyzatora.

1. Przejdź do strony **Ustawienia** szablonu urządzenia **Połączony klimatyzator**:

    ![Przygotowanie do dodania ustawienia](./media/tutorial-define-device-type/deviceaddsetting.png)

    Istnieje możliwość utworzenia ustawień różnych typów, takich jak tekst lub liczby.

2. Wybierz pozycję **Liczba**, aby dodać ustawienie liczbowe dla urządzenia.

3. Aby skonfigurować ustawienie **Ustaw temperaturę**, skorzystaj z informacji w poniższej tabeli:

    | Pole                | Wartość           |
    | -------------------- | -----------     |
    | Nazwa wyświetlana         | Ustaw temperaturę |
    | Nazwa pola           | setTemperature  |
    | Jednostka miary      | F               |
    | Miejsca dziesiętne       | 1               |
    | Wartość minimalna        | 20              |
    | Wartość maksymalna        | 200             |
    | Wartość początkowa        | 80              |
    | Opis          | Ustawianie temperatury docelowej dla klimatyzatora |

    Następnie wybierz polecenie **Zapisz**:

    ![Konfigurowanie ustawienia Ustaw temperaturę](./media/tutorial-define-device-type/configuresetting.png)

    > [!NOTE]
    > Gdy urządzenie potwierdzi zmianę ustawień, stan ustawienia zostanie zmieniony na **zsynchronizowane**.

4. Układ strony **Ustawienia** możesz dostosować przez przeniesienie i zmianę rozmiaru kafelków ustawień:

    ![Dostosowywanie układu ustawień](./media/tutorial-define-device-type/settingslayout.png)

## <a name="use-properties"></a>Korzystanie z właściwości 

*Właściwości aplikacji* umożliwiają przechowywanie informacji o urządzeniu w aplikacji. W tej sekcji do szablonu urządzenia **Połączony klimatyzator** są dodawane właściwości aplikacji na potrzeby przechowywania lokalizacji urządzenia i daty ostatniego serwisowania. Należy zauważyć, że obie właściwości to edytowalne właściwości urządzenia. Dostępne są także właściwości urządzenia tylko do odczytu zgłaszane przez urządzenie, których nie można zmienić, takie jak numer seryjny urządzenia i wersja oprogramowania układowego.
 
1. Przejdź do strony **Właściwości** szablonu urządzenia **Połączony klimatyzator**:

    ![Przygotowanie do dodania właściwości](./media/tutorial-define-device-type/deviceaddproperty.png)

    Istnieje możliwość utworzenia właściwości urządzenia różnych typów, takich jak tekst lub liczby. Aby dodać właściwość lokalizacji do szablonu urządzenia, wybierz pozycję **Lokalizacja**.

1. Aby skonfigurować właściwość lokalizacji, skorzystaj z informacji w poniższej tabeli:

    | Pole                | Wartość                |
    | -------------------- | -------------------- |
    | Nazwa wyświetlana         | Lokalizacja             |
    | Nazwa pola           | location             |
    | Wartość początkowa        | Seattle, WA          |
    | Opis          | Lokalizacja urządzenia      |

    Pozostaw wartości domyślne w pozostałych polach.

    ![Konfigurowanie właściwości urządzenia](./media/tutorial-define-device-type/configureproperties.png)

    Wybierz pozycję **Zapisz**.

1. Aby dodać właściwość daty ostatniego serwisowania do szablonu urządzenia, wybierz pozycję **Data**.

1. Aby skonfigurować właściwość daty ostatniego serwisowania, skorzystaj z informacji w poniższej tabeli:

    | Pole                | Wartość                   |
    | -------------------- | ----------------------- |
    | Nazwa wyświetlana         | Data ostatniego serwisowania       |
    | Nazwa pola           | serviceDate             |
    | Wartość początkowa        | 2018-01-01                |
    | Opis          | Ostatnio serwisowane           |

    ![Konfigurowanie właściwości urządzenia](./media/tutorial-define-device-type/configureproperties2.png)

    Wybierz pozycję **Zapisz**.

5. Układ strony **Właściwości** możesz dostosować przez przeniesienie i zmianę rozmiaru kafelków właściwości:

    ![Dostosowanie układu właściwości](./media/tutorial-define-device-type/propertieslayout.png)

1. Aby dodać właściwość urządzenia, taką jak wersja oprogramowania układowego, do szablonu urządzenia, wybierz pozycję **Właściwość urządzenia**.

1.  Aby skonfigurować wersję oprogramowania układowego, skorzystaj z informacji w poniższej tabeli:

    | Pole                | Wartość                   |
    | -------------------- | ----------------------- |
    | Nazwa wyświetlana         | Wersja oprogramowania układowego        |
    | Nazwa pola           | firmwareVersion         |
    | Typ danych            | tekst                    |
    | Opis          | Wersja oprogramowania układowego klimatyzatora |

    ![Skonfiguruj wersję oprogramowania układowego](./media/tutorial-define-device-type/configureproperties3.png)
    
    Wybierz pozycję **Zapisz**.

1. Aby dodać właściwość urządzenia, taką jak numer seryjny, do szablonu urządzenia, wybierz pozycję **Właściwość urządzenia**.

1. Aby skonfigurować numer seryjny, skorzystaj z informacji w poniższej tabeli:

    | Pole                | Wartość                   |
    | -------------------- | ----------------------- |
    | Nazwa wyświetlana         | Numer seryjny           |
    | Nazwa pola           | serialNumber            |
    | Typ danych            | tekst                    |
    | Opis          | Numer seryjny klimatyzatora  |

    ![Skonfiguruj numer seryjny](./media/tutorial-define-device-type/configureproperties4.png)
    
    Wybierz pozycję **Zapisz**.
    
    > [!NOTE]
    > Właściwości urządzenia są wysyłane z urządzenia do aplikacji. Wartości wersji oprogramowania układowego i numeru seryjnego zostaną zaktualizowane, gdy rzeczywiste urządzenie połączy się z usługą IoT Central.

## <a name="use-commands"></a>Korzystanie z poleceń

Za pomocą _poleceń_ można umożliwić operatorowi uruchamianie poleceń bezpośrednio na urządzeniu. W tej sekcji dodasz do szablonu urządzenia **Połączony klimatyzator** polecenie, które umożliwia operatorowi wysyłanie komunikatów do połączonego klimatyzatora.

1. Przejdź do strony **Polecenia** szablonu urządzenia **Połączony klimatyzator**, aby edytować szablon. 

1. Kliknij pozycję **Nowe polecenie**, aby dodać polecenie do urządzenia i rozpocząć konfigurowanie nowego polecenia.

   Możesz tworzyć polecenia różnych typów, w zależności od wymagań. 

1. Aby skonfigurować nowe polecenie, skorzystaj z informacji w poniższej tabeli:

    | Pole                | Wartość           |
    | -------------------- | -----------     |
    | Nazwa wyświetlana         | Polecenie echo    |
    | Nazwa pola           | echo            |
    | Domyślny limit czasu      | 30              |
    | Typ wyświetlany         | tekst            |
    | Opis          | Polecenie urządzenia  |  

    Dodatkowe dane wejściowe polecenia możesz dodać, klikając znak **+** dla pozycji **Pola wejściowe**.

    ![Przygotowanie do dodania ustawienia](media/tutorial-define-device-type/commandsecho1.png)

     Wybierz pozycję **Zapisz**.

1. Układ strony **Polecenia** możesz dostosować przez przeniesienie i zmianę rozmiaru kafelków poleceń:

    ![Dostosowywanie układu ustawień](media/tutorial-define-device-type/commandstileresize1.png)

## <a name="view-your-simulated-device"></a>Wyświetlanie symulowanego urządzenia

Po zdefiniowaniu szablonu urządzenia **Połączony klimatyzator** możesz dostosować jego **pulpit nawigacyjny**, tak aby obejmował zdefiniowane miary, ustawienia i właściwości. Następnie możesz wyświetlić podgląd pulpitu nawigacyjnego jako operator:

1. Wybierz stronę **Pulpit nawigacyjny** dla szablonu urządzenia **Połączony klimatyzator**:

    ![Pulpity nawigacyjne połączonego klimatyzatora](./media/tutorial-define-device-type/aircondashboards.png)

1. Wybierz pozycję **Wykres liniowy**, aby dodać składnik do **pulpitu nawigacyjnego**:

    ![Składniki pulpitu nawigacyjnego](./media/tutorial-define-device-type/dashboardcomponents1.png)

1. Skonfiguruj składnik **Wykres liniowy**, korzystając z informacji w poniższej tabeli:

    | Ustawienie      | Wartość       |
    | ------------ | ----------- |
    | Tytuł        | Temperatura |
    | Zakres czasu   | Ostatnie 30 minut |
    | Miary     | temperatura (wybierz element **Widoczność** obok elementu **temperatura**) |

    ![Ustawienia wykresu liniowego](./media/tutorial-define-device-type/linechartsettings.png)

    Następnie wybierz polecenie **Zapisz**.

1. Skonfiguruj składnik **Historia zdarzeń**, korzystając z informacji w poniższej tabeli:

    | Ustawienie      | Wartość       |
    | ------------ | ----------- |
    | Tytuł        | Zdarzenia |
    | Zakres czasu   | Ostatnie 30 minut |
    | Miary     | Błąd silnika wentylatora (wybierz element **Widoczność** obok elementu **Błąd silnika wentylatora**) |

    ![Ustawienia wykresu liniowego](./media/tutorial-define-device-type/dashboardeventchartsetting.png)

    Następnie wybierz polecenie **Zapisz**.

1. Skonfiguruj składnik **Historia stanu**, korzystając z informacji w poniższej tabeli:

    | Ustawienie      | Wartość       |
    | ------------ | ----------- |
    | Tytuł        | Tryb wentylatora |
    | Zakres czasu   | Ostatnie 30 minut |
    | Miary | Tryb wentylatora (wybierz element **Widoczność** obok elementu **Tryb wentylatora**) |

    ![Ustawienia wykresu liniowego](./media/tutorial-define-device-type/dashboardstatechartsetting.png)

    Następnie wybierz polecenie **Zapisz**.

1. Aby dodać ustawienie Ustaw temperaturę do pulpitu nawigacyjnego, wybierz pozycję **Ustawienia i właściwości**. Kliknij pozycję **Dodaj/usuń**, aby dodać ustawienia lub właściwości, które mają być wyświetlane na pulpicie nawigacyjnym. 

    ![Składniki pulpitu nawigacyjnego](./media/tutorial-define-device-type/dashboardcomponents4.png)

1. Skonfiguruj składnik **Ustawienia i właściwości**, korzystając z informacji w poniższej tabeli:

    | Ustawienie                 | Wartość         |
    | ----------------------- | ------------- |
    | Tytuł                   | Ustaw temperaturę docelową |
    | Ustawienia i właściwości | Ustaw temperaturę |

    Ustawienia i właściwości zdefiniowane wcześniej na stronach Ustawienia i Właściwości są wyświetlone w polu Dostępne kolumny. 

    ![Określanie ustawień właściwości temperatury](./media/tutorial-define-device-type/propertysettings4.png)

    Następnie wybierz pozycję **OK**.

1. Aby dodać numer seryjny urządzenia do pulpitu nawigacyjnego, wybierz pozycję **Ustawienia i właściwości**:

    ![Składniki pulpitu nawigacyjnego](./media/tutorial-define-device-type/dashboardcomponents3.png)

1. Skonfiguruj składnik **Ustawienia i właściwości**, korzystając z informacji w poniższej tabeli:

    | Ustawienie                 | Wartość         |
    | ----------------------- | ------------- |
    | Tytuł                   | Numer seryjny |
    | Ustawienia i właściwości | Numer seryjny |

    ![Ustawienia właściwości numeru seryjnego](./media/tutorial-define-device-type/propertysettings5.png)

    Następnie wybierz pozycję **OK**.

1. Aby dodać wersję oprogramowania układowego do pulpitu nawigacyjnego, wybierz pozycję **Ustawienia i właściwości**:

    ![Składniki pulpitu nawigacyjnego](./media/tutorial-define-device-type/dashboardcomponents4.png)

1. Skonfiguruj składnik **Ustawienia i właściwości**, korzystając z informacji w poniższej tabeli:

    | Ustawienie                 | Wartość            |
    | ----------------------- | ---------------- |
    | Tytuł                   | Wersja oprogramowania układowego |
    | Ustawienia i właściwości | Wersja oprogramowania układowego |

    ![Ustawienia właściwości numeru seryjnego](./media/tutorial-define-device-type/propertysettings6.png)

    Następnie wybierz pozycję **OK**.

1. Aby wyświetlić pulpit nawigacyjny jako operator, wyłącz pozycję **Edytuj szablon** w prawej górnej części strony.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * Tworzenie nowego szablonu urządzenia
> * Dodawanie telemetrii do urządzenia
> * Wyświetlanie symulowanych danych telemetrycznych
> * Definiowanie zdarzeń urządzenia
> * Wyświetlanie symulowanych zdarzeń
> * Definiowanie stanu
> * Wyświetlanie symulowanego stanu
> * Korzystanie z ustawień i właściwości
> * Korzystanie z poleceń
> * Wyświetlanie symulowanego urządzenia na pulpicie nawigacyjnym

Teraz, po zdefiniowaniu szablonu urządzenia w aplikacji usługi Azure IoT Central, możesz wykonać następujące sugerowane kroki:

* [Konfigurowanie reguł i akcji dla urządzenia](tutorial-configure-rules.md)
* [Dostosowywanie widoków operatora](tutorial-customize-operator.md)

[lnk-define-template]: /azure/iot-central/howto-set-up-template#properties