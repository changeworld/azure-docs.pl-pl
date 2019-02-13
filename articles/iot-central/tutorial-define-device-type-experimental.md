---
title: Definiowanie nowego typu urządzenia w usłudze Azure IoT Central | Microsoft Docs
description: Ten samouczek przedstawia, jak konstruktor może zdefiniować nowy typ urządzenia w aplikacji usługi Azure IoT Central. Dla typu można zdefiniować telemetrię, stan, właściwości i ustawienia.
author: dominicbetts
ms.author: dobett
ms.date: 01/28/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 2506137e03e8677827bb1e2a3914ee10ae24f368
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55810120"
---
# <a name="tutorial-define-a-new-device-type-in-your-azure-iot-central-application-new-ui-design"></a>Samouczek: definiowanie nowego typu urządzenia w aplikacji usługi Azure IoT Central (nowy projekt interfejsu użytkownika)

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

[!INCLUDE [iot-central-experimental-note](../../includes/iot-central-experimental-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka potrzebna jest aplikacja usługi Azure IoT Central. Jeśli ukończono przewodnik Szybki start [Tworzenie aplikacji usługi Azure IoT Central](quick-deploy-iot-central-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json), możesz użyć ponownie aplikacji utworzonej w jego ramach. W przeciwnym przypadku wykonaj następujące kroki, aby utworzyć pustą aplikację usługi Azure IoT Central:

1. Przejdź na stronę [Menedżer aplikacji](https://aka.ms/iotcentral) usługi Azure IoT Central.

2. Podaj adres e-mail i hasło umożliwiające dostęp do subskrypcji platformy Azure:

    ![Określanie konta organizacji](./media/tutorial-define-device-type-experimental/sign-in.png)

3. Aby rozpocząć tworzenie nowej aplikacji usługi Azure IoT Central, kliknij pozycję **Nowa aplikacja**:

    ![Strona Menedżer aplikacji usługi Azure IoT Central](./media/tutorial-define-device-type-experimental/iotcentralhome.png)

4. Aby utworzyć nową aplikację usługi Azure IoT Central:
    
    * Wybierz pozycję **Wersja próbna**. Do tworzenia aplikacji w wersji próbnej nie potrzebujesz subskrypcji platformy Azure.
    
       Aby uzyskać więcej informacji na temat katalogów i subskrypcji, zapoznaj się z [przewodnikiem Szybki start dotyczącym tworzenia aplikacji](quick-deploy-iot-central-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).
    
    * Wybierz pozycję **Aplikacja niestandardowa**.
    
    * Opcjonalnie wybierz przyjazną nazwę aplikacji, taką jak **Klimatyzatory Contoso**. Usługa Azure IoT Central wygeneruje unikatowy prefiks adresu URL. Ten prefiks adresu URL możesz zmienić na łatwiejszy do zapamiętania.
    
    * Kliknij pozycję **Utwórz**.

    ![Strona Tworzenie aplikacji usługi Azure IoT Central](./media/tutorial-define-device-type-experimental/iotcentralcreate.png)

    Aby uzyskać więcej informacji, zapoznaj się z [przewodnikiem Szybki start dotyczącym tworzenia aplikacji](quick-deploy-iot-central-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

## <a name="create-a-new-custom-device-template"></a>Tworzenie nowego niestandardowego szablonu urządzenia

Jako konstruktor możesz tworzyć i edytować szablony urządzenia w aplikacji. Podczas tworzenia szablonu urządzenia usługa Azure IoT Central generuje symulowane urządzenie na podstawie szablonu. Symulowane urządzenie generuje dane telemetryczne, który umożliwiają testowanie zachowania aplikacji przed podłączeniem prawdziwego urządzenia.

Aby dodać nowy szablon urządzenia do aplikacji, musisz przejść na stronę **Szablony urządzeń**. W tym celu kliknij pozycję **Szablony urządzeń** w menu nawigacji po lewej stronie.

![Strona Szablony urządzeń](./media/tutorial-define-device-type-experimental/devicetemplates.png)

## <a name="add-a-device-template"></a>Dodawanie szablonu urządzenia

Poniższe kroki przedstawiają sposób tworzenia nowego szablonu urządzenia **Połączony klimatyzator** przeznaczonego dla urządzeń wysyłających do aplikacji dane telemetryczne dotyczące temperatury:

1. Na stronie **Szablony urządzeń** kliknij **+ Nowy**:

    ![Strona Szablony urządzeń, szablon tworzenia urządzenia](./media/tutorial-define-device-type-experimental/newtemplate.png)

3. Na stronie **Niestandardowy szablon urządzenia** wprowadź **Połączony klimatyzator** jako nazwę urządzenia, a następnie kliknij polecenie **Utwórz**. Istnieje możliwość przekazania obrazu urządzenia widocznego dla operatorów w eksploratorze urządzeń:

    ![Urządzenie niestandardowe](./media/tutorial-define-device-type-experimental/createcustomdevice.png)

4. Upewnij się, że dla szablonu urządzenia **Połączony klimatyzator** jest wyświetlona karta **Miary**, na której definiuje się telemetrię. Każdy definiowany szablon urządzenia ma oddzielne strony umożliwiające:

    * określenie _miar_ wysyłanych przez urządzenie, takich jak telemetria, zdarzenie i stan;

    * definiowanie _ustawień_ używanych do kontrolowania urządzenia;

    * definiowanie _właściwości_ będących metadanymi urządzenia;

    * definiowanie _poleceń_ do uruchamiania bezpośrednio na urządzeniu;

    * definiowanie _reguł_ skojarzonych z urządzeniem;

    * dostosowanie _pulpitu nawigacyjnego_ urządzenia dla operatorów.

    ![Miary klimatyzatora](./media/tutorial-define-device-type-experimental/airconmeasurements.png)

    > [!NOTE]
    > Aby zmienić nazwę szablonu urządzenia, kliknij nazwę szablonu w górnej części strony.

5. Aby dodać miarę telemetrii dla temperatury, kliknij pozycję **+ Nowa miara**. Następnie wybierz pozycję **Telemetria** jako typ miary:

    ![Miary połączonego klimatyzatora](./media/tutorial-define-device-type-experimental/airconmeasurementsnew.png)

6. Każdy typ telemetrii zdefiniowany dla szablonu urządzenia obejmuje [opcje konfiguracji](howto-set-up-template-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json), takie jak:

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

    Istnieje także możliwość wyboru koloru na potrzeby wyświetlania danych telemetrii. Aby zapisać definicję telemetrii, kliknij polecenie **Zapisz**:

    ![Konfigurowanie symulacji temperatury](./media/tutorial-define-device-type-experimental/temperaturesimulation.png)

7. Po krótkiej chwili na karcie **Miary** zostanie wyświetlony wykres danych telemetrii temperatury pochodzących z symulowanego połączonego klimatyzatora. Do zarządzania widocznością lub agregacją albo do edytowania definicji telemetrii użyj kontrolek:

    ![Wyświetlenie symulacji temperatury](./media/tutorial-define-device-type-experimental/viewsimulation.png)

8. Istnieje także możliwość dostosowania wykresu przy użyciu kontrolek **Wiersz**, **Skumulowany** i **Edytuj zakres czasu**:

    ![Dostosowywanie wykresu](./media/tutorial-define-device-type-experimental/customizechart.png)

## <a name="add-an-event-measurement"></a>Dodawanie miary zdarzenia

Użyj zdarzeń, aby zdefiniować dane wysyłane przez urządzenie w momencie wystąpienia takiego zdarzenia, jak błąd lub awaria składnika. Usługa Azure IoT Central może symulować zdarzenia urządzenia, aby umożliwić testowanie zachowania Twojej aplikacji przed podłączeniem prawdziwego urządzenia. Miary zdarzenia dla szablonu urządzenia definiuje się w widoku **Miary**.

1. Aby dodać miarę zdarzenia **Błąd silnika wentylatora**, kliknij pozycję **+ Nowa miara**. Następnie wybierz pozycję **Zdarzenie** jako typ miary:

    ![Miary połączonego klimatyzatora](./media/tutorial-define-device-type-experimental/eventnew.png)

2. Każdy typ zdarzenia zdefiniowany dla szablonu urządzenia obejmuje [opcje konfiguracji](howto-set-up-template-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json), takie jak:

   * Nazwa wyświetlana

   * Nazwa pola

   * Ważność

    Aby skonfigurować pozycję telemetrii **Błąd silnika wentylatora**, skorzystaj z informacji w poniższej tabeli:

    | Ustawienie              | Wartość             |
    | -------------------- | -----------       |
    | Nazwa wyświetlana         | Błąd silnika wentylatora   |
    | Nazwa pola           | fanmotorerr       |
    | Ważność             | Błąd             |

    Aby zapisać definicję zdarzenia, kliknij polecenie **Zapisz**:

    ![Konfigurowanie miary zdarzenia](./media/tutorial-define-device-type-experimental/eventconfiguration.png)

3. Po krótkiej chwili na karcie **Miary** zostanie wyświetlony wykres dla zdarzeń wygenerowanych losowo przez symulowany połączony klimatyzator. Do zarządzania widocznością lub edytowania definicji zdarzenia użyj kontrolek:

    ![Wyświetlanie symulacji zdarzenia](./media/tutorial-define-device-type-experimental/eventview.png)

1. Aby wyświetlić dodatkowe szczegóły dotyczące zdarzenia, kliknij zdarzenie na wykresie:

    ![Wyświetlanie szczegółów zdarzenia](./media/tutorial-define-device-type-experimental/eventviewdetail.png)

## <a name="define-a-state-measurement"></a>Definiowanie miary stanu

Stan służy do definiowania i wizualizowania stanu urządzenia lub jego składników w danym czasie. Usługa Azure IoT Central może symulować stan urządzenia, aby umożliwić testowanie zachowania Twojej aplikacji przed podłączeniem prawdziwego urządzenia. Miary stanu dla typu urządzenia definiuje się w widoku **Miary**.

1. Aby dodać miarę stanu **Tryb wentylator**, kliknij pozycję **+ Nowa miara**. Następnie wybierz pozycję **Stan** jako typ miary:

    ![Miary stanu połączonego klimatyzatora](./media/tutorial-define-device-type-experimental/statenew.png)

2. Każdy typ stanu zdefiniowany dla szablonu urządzenia zawiera [opcje konfiguracji](howto-set-up-template-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json), takie jak:

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

    Aby zapisać definicję miary stanu, kliknij polecenie **Zapisz**:

    ![Konfigurowanie miary stanu](./media/tutorial-define-device-type-experimental/stateconfiguration.png)

3. Po krótkiej chwili na karcie **Miary** zostanie wyświetlony wykres stanów wygenerowanych losowo przez symulowany połączony klimatyzator. Do zarządzania widocznością lub edytowania definicji stanu użyj kontrolek:

    ![Widok symulacji stanu](./media/tutorial-define-device-type-experimental/stateview.png)

4. Jeśli w krótkim czasie urządzenie wyśle za dużo punktów danych, miara stanu będzie wyświetlana za pomocą innej wizualizacji. Kliknij wykres, aby zobaczyć wszystkie punkty danych w danym czasie wyświetlone w kolejności chronologicznej. Zakres czasu można także zawęzić, aby wyświetlić miarę bardziej szczegółowo.

## <a name="settings-properties-and-commands"></a>Ustawienia, właściwości i polecenia

Ustawienia, właściwości oraz polecenia to różne wartości zdefiniowane w szablonie urządzenia i skojarzone z poszczególnymi urządzeniami:

* _Ustawienia_ umożliwiają wysyłanie danych konfiguracji do urządzenia z aplikacji. Na przykład operator może użyć ustawienia do zmiany interwału telemetrii urządzenia z dwóch na pięć sekund. Po zmianie ustawienia przez operatora ustawienie jest oznaczone jako oczekujące w interfejsie użytkownika, dopóki urządzenie nie odpowie za pomocą potwierdzenia.

* _Właściwości_ możesz użyć do definiowania metadanych skojarzonych z urządzeniem. Są dostępne dwie kategorie właściwości:
    
    * _Właściwości aplikacji_ umożliwiają rejestrowanie informacji o urządzeniu w aplikacji. Na przykład możesz użyć właściwości aplikacji do zarejestrowania lokalizacji urządzenia i daty ostatniego serwisowania. Właściwości te są przechowywane w aplikacji i nie są synchronizowane z urządzeniem. Operator może przypisać wartości do właściwości.

    * _Właściwości urządzenia_ umożliwiają urządzaniu wysyłanie wartości właściwości do aplikacji. Te właściwości może zmienić tylko urządzenie. Dla operatora właściwości urządzenia są tylko do odczytu. W tym scenariuszu z połączonym klimatyzatorem wersja oprogramowania układowego i numer seryjny urządzenia to właściwości urządzenia zgłoszone przez urządzenie.
    
    Aby uzyskać więcej informacji, zobacz [Właściwości](howto-set-up-template-experimental.md#properties) w instrukcjach konfigurowania szablonu urządzenia.

* _Polecenia_ służą do zdalnego zarządzania urządzeniem z poziomu aplikacji. Polecenia można uruchamiać z chmury bezpośrednio na urządzeniu, aby sterować urządzeniami. Na przykład operator może uruchamiać polecenia, takie jak reboot, aby natychmiast ponownie uruchomić urządzenie.

## <a name="use-settings"></a>Używanie ustawień

*Ustawienia* umożliwiają operatorowi wysyłanie danych konfiguracji do urządzenia. W tej sekcji do szablonu urządzenia **Połączony klimatyzator** jest dodawane ustawienie, które umożliwia operatorowi ustawienie temperatury docelowej połączonego klimatyzatora.

1. Przejdź do karty **Ustawienia** szablonu urządzenia **Połączony klimatyzator**.

2. Istnieje możliwość utworzenia ustawień różnych typów, takich jak tekst lub liczby. Kliknij pozycję **Liczba**, aby dodać ustawienie liczbowe dla urządzenia.

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

    Następnie kliknij przycisk **Save** (Zapisz):

    ![Konfigurowanie ustawienia Ustaw temperaturę](./media/tutorial-define-device-type-experimental/configuresetting.png)

    > [!NOTE]
    > Gdy urządzenie potwierdzi zmianę ustawień, stan ustawienia zostanie zmieniony na **zsynchronizowane**.

4. Układ karty **Ustawienia** możesz dostosować, przenosząc i zmieniając rozmiar kafelków ustawień:

    ![Dostosowywanie układu ustawień](./media/tutorial-define-device-type-experimental/settingslayout.png)

## <a name="use-properties"></a>Korzystanie z właściwości

*Właściwości aplikacji* umożliwiają przechowywanie informacji o urządzeniu w aplikacji. W tej sekcji do szablonu urządzenia **Połączony klimatyzator** są dodawane właściwości aplikacji na potrzeby przechowywania lokalizacji urządzenia i daty ostatniego serwisowania. Te właściwości można edytować w aplikacji. Urządzenie zgłasza także takie właściwości, jak numer seryjny i wersja oprogramowania układowego, które są tylko do odczytu w aplikacji.

1. Przejdź do karty **Właściwości** szablonu urządzenia **Połączony klimatyzator**.

1. Istnieje możliwość utworzenia właściwości urządzenia różnych typów, takich jak tekst lub liczby. Aby dodać właściwość lokalizacji do szablonu urządzenia, wybierz pozycję **Lokalizacja**. Aby skonfigurować właściwość lokalizacji, skorzystaj z informacji w poniższej tabeli:

    | Pole                | Wartość                |
    | -------------------- | -------------------- |
    | Nazwa wyświetlana         | Lokalizacja             |
    | Nazwa pola           | location             |
    | Wartość początkowa        | Seattle, WA          |
    | Opis          | Lokalizacja urządzenia      |

    Pozostaw wartości domyślne w pozostałych polach.

    ![Konfigurowanie właściwości urządzenia](./media/tutorial-define-device-type-experimental/configureproperties.png)

    Kliknij pozycję **Zapisz**.

1. Aby dodać właściwość daty ostatniego serwisowania do szablonu urządzenia, wybierz pozycję **Data**.

1. Aby skonfigurować właściwość daty ostatniego serwisowania, skorzystaj z informacji w poniższej tabeli:

    | Pole                | Wartość                   |
    | -------------------- | ----------------------- |
    | Nazwa wyświetlana         | Data ostatniego serwisowania       |
    | Nazwa pola           | serviceDate             |
    | Wartość początkowa        | 2019-01-01                |
    | Opis          | Ostatnio serwisowane           |

    ![Konfigurowanie właściwości urządzenia](./media/tutorial-define-device-type-experimental/configureproperties2.png)

    Kliknij pozycję **Zapisz**.

1. Układ karty **Właściwości** możesz dostosować, przenosząc i zmieniając rozmiar kafelków właściwości.

1. Aby dodać właściwość urządzenia, taką jak wersja oprogramowania układowego, do szablonu urządzenia, wybierz pozycję **Właściwość urządzenia**.

1. Aby skonfigurować wersję oprogramowania układowego, skorzystaj z informacji w poniższej tabeli:

    | Pole                | Wartość                   |
    | -------------------- | ----------------------- |
    | Nazwa wyświetlana         | Wersja oprogramowania układowego        |
    | Nazwa pola           | firmwareVersion         |
    | Typ danych            | tekst                    |
    | Opis          | Wersja oprogramowania układowego klimatyzatora |

    ![Skonfiguruj wersję oprogramowania układowego](./media/tutorial-define-device-type-experimental/configureproperties3.png)

    Kliknij pozycję **Zapisz**.

1. Aby dodać właściwość urządzenia, taką jak numer seryjny, do szablonu urządzenia, wybierz pozycję **Właściwość urządzenia**.

1. Aby skonfigurować numer seryjny, skorzystaj z informacji w poniższej tabeli:

    | Pole                | Wartość                   |
    | -------------------- | ----------------------- |
    | Nazwa wyświetlana         | Numer seryjny           |
    | Nazwa pola           | serialNumber            |
    | Typ danych            | tekst                    |
    | Opis          | Numer seryjny klimatyzatora  |

    ![Skonfiguruj numer seryjny](./media/tutorial-define-device-type-experimental/configureproperties4.png)

    Kliknij pozycję **Zapisz**.

    > [!NOTE]
    > Właściwości urządzenia są wysyłane z urządzenia do aplikacji. Wartości wersji oprogramowania układowego i numeru seryjnego zostaną zaktualizowane, gdy rzeczywiste urządzenie połączy się z usługą IoT Central.

## <a name="use-commands"></a>Korzystanie z poleceń

Za pomocą _poleceń_ można umożliwić operatorowi uruchamianie poleceń bezpośrednio na urządzeniu. W tej sekcji dodasz do szablonu urządzenia **Połączony klimatyzator** polecenie, które umożliwia operatorowi wysyłanie komunikatów do połączonego klimatyzatora.

1. Przejdź do karty **Polecenia** szablonu urządzenia **Połączony klimatyzator**, aby edytować szablon.

1. Kliknij pozycję **+ Nowe polecenie**, aby dodać polecenie do urządzenia i rozpocząć konfigurowanie nowego polecenia.

1. Aby skonfigurować nowe polecenie, skorzystaj z informacji w poniższej tabeli:

    | Pole                | Wartość           |
    | -------------------- | -----------     |
    | Nazwa wyświetlana         | Polecenie echo    |
    | Nazwa pola           | echo            |
    | Domyślny limit czasu      | 30              |
    | Typ wyświetlany         | tekst            |
    | Opis          | Polecenie urządzenia  |  

    Dodatkowe dane wejściowe polecenia możesz dodać, klikając znak **+** dla pozycji **Pola wejściowe**.

    ![Przygotowanie do dodania ustawienia](./media/tutorial-define-device-type-experimental/commandsecho1.png)

     Kliknij pozycję **Zapisz**.

1. Układ karty **Polecenia** możesz dostosować, przenosząc i zmieniając rozmiar kafelków poleceń.

## <a name="view-your-simulated-device"></a>Wyświetlanie symulowanego urządzenia

Po zdefiniowaniu Twojego szablonu urządzenia **Połączony klimatyzator** możesz dostosować jego **Pulpit nawigacyjny** tak, aby obejmował zdefiniowane miary, ustawienia i właściwości. Następnie możesz wyświetlić podgląd pulpitu nawigacyjnego jako operator:

1. Wybierz kartę **Pulpit nawigacyjny** szablonu urządzenia **Połączony klimatyzator**.

1. Kliknij pozycję **Wykres liniowy**, aby dodać składnik do **pulpitu nawigacyjnego**.

1. Skonfiguruj składnik **Wykres liniowy**, korzystając z informacji w poniższej tabeli:

    | Ustawienie      | Wartość       |
    | ------------ | ----------- |
    | Tytuł        | Temperatura |
    | Zakres czasu   | Ostatnie 30 minut |
    | Miary     | Temperatura (kliknij element **Widoczność** obok elementu **Temperatura**) |

    ![Ustawienia wykresu liniowego](./media/tutorial-define-device-type-experimental/linechartsettings.png)

    Następnie kliknij przycisk **Save** (Zapisz).

1. Kliknij składnik **Historia zdarzeń**, korzystając z informacji w poniższej tabeli:

    | Ustawienie      | Wartość       |
    | ------------ | ----------- |
    | Tytuł        | Zdarzenia silnika wentylatora |
    | Zakres czasu   | Ostatnie 30 minut |
    | Miary     | Błąd silnika wentylatora (kliknij element **Widoczność** obok elementu **Błąd silnika wentylatora**) |

    ![Ustawienia wykresu liniowego](./media/tutorial-define-device-type-experimental/dashboardeventchartsetting.png)

    Następnie kliknij przycisk **Save** (Zapisz).

1. Skonfiguruj składnik **Historia stanu**, korzystając z informacji w poniższej tabeli:

    | Ustawienie      | Wartość       |
    | ------------ | ----------- |
    | Tytuł        | Tryb wentylatora |
    | Zakres czasu   | Ostatnie 30 minut |
    | Miary | Tryb wentylatora (kliknij element **Widoczność** obok elementu **Tryb wentylatora**) |

    ![Ustawienia wykresu liniowego](./media/tutorial-define-device-type-experimental/dashboardstatechartsetting.png)

    Następnie kliknij przycisk **Save** (Zapisz).

1. Aby dodać ustawienia i właściwości urządzenia do pulpitu nawigacyjnego, wybierz pozycję **Ustawienia i właściwości**. Kliknij pozycję **Dodaj/usuń**, aby dodać ustawienia lub właściwości, które mają być wyświetlane na pulpicie nawigacyjnym.

1. Skonfiguruj składnik **Ustawienia i właściwości**, korzystając z informacji w poniższej tabeli:

    | Ustawienie                 | Wartość         |
    | ----------------------- | ------------- |
    | Tytuł                   | Właściwości urządzenia |
    | Ustawienia i właściwości | Ustaw temperaturę<br/>Numer seryjny<br/>Wersja oprogramowania układowego |

    Ustawienia i właściwości zdefiniowane wcześniej na stronach **Ustawienia i właściwości** są wyświetlone w polu **Dostępne kolumny**.

    ![Określanie ustawień właściwości temperatury](./media/tutorial-define-device-type-experimental/propertysettings4.png)

    Następnie kliknij przycisk **Save** (Zapisz).

1. Dane symulowane dla Twojego połączonego klimatyzatora będą teraz widoczne na pulpicie nawigacyjnym. Możesz edytować kafelki i układ pulpitu nawigacyjnego:

    ![Wyświetlanie pulpitu nawigacyjnego](./media/tutorial-define-device-type-experimental/dashboard.png)

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

Teraz, po zdefiniowaniu szablonu urządzenia w swojej aplikacji usługi Azure IoT Central, możesz wykonać następujące sugerowane kroki:

* [Konfigurowanie reguł i akcji dla urządzenia](tutorial-configure-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
* [Dostosowywanie widoków operatora](tutorial-customize-operator-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)