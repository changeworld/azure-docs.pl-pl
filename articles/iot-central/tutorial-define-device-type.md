---
title: Definiowanie nowego typu urządzenia w usłudze Azure IoT Central | Microsoft Docs
description: Ten samouczek przedstawia, jak konstruktor może zdefiniować nowy typ urządzenia w aplikacji usługi Azure IoT Central. Dla typu można zdefiniować telemetrię, stan, właściwości i ustawienia.
author: dominicbetts
ms.author: dobett
ms.date: 06/07/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: fd3ec276fdc95ad326d14a5b21e8ab793b154080
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69875778"
---
# <a name="tutorial-define-a-new-device-type-in-your-azure-iot-central-application"></a>Samouczek: definiowanie nowego typu urządzenia w aplikacji usługi Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

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

3. Aby rozpocząć tworzenie nowej aplikacji IoT Central platformy Azure, wybierz pozycję **Nowa aplikacja**:

    ![Strona Menedżer aplikacji usługi Azure IoT Central](./media/tutorial-define-device-type/iotcentralhome.png)

4. Aby utworzyć nową aplikację usługi Azure IoT Central:
    
   * Wybierz pozycję **Wersja próbna**. Do tworzenia aplikacji w wersji próbnej nie potrzebujesz subskrypcji platformy Azure.
    
      Aby uzyskać więcej informacji na temat katalogów i subskrypcji, zapoznaj się z [przewodnikiem Szybki start dotyczącym tworzenia aplikacji](quick-deploy-iot-central.md).
    
   * Wybierz pozycję **Aplikacja niestandardowa**.
    
   * Opcjonalnie wybierz przyjazną nazwę aplikacji, taką jak **Klimatyzatory Contoso**. Usługa Azure IoT Central wygeneruje unikatowy prefiks adresu URL. Ten prefiks adresu URL możesz zmienić na łatwiejszy do zapamiętania.
    
   * Wybierz pozycję **Utwórz**.

     ![Strona Tworzenie aplikacji usługi Azure IoT Central](./media/tutorial-define-device-type/iotcentralcreate.png)

     Aby uzyskać więcej informacji, zapoznaj się z [przewodnikiem Szybki start dotyczącym tworzenia aplikacji](quick-deploy-iot-central.md).

## <a name="create-a-device-template"></a>Tworzenie szablonu urządzenia

Jako konstruktor możesz tworzyć i edytować szablony urządzenia w aplikacji. Podczas tworzenia szablonu urządzenia usługa Azure IoT Central generuje symulowane urządzenie na podstawie szablonu. Symulowane urządzenie generuje dane telemetryczne, który umożliwiają testowanie zachowania aplikacji przed podłączeniem prawdziwego urządzenia.

Aby dodać nowy szablon urządzenia do aplikacji, musisz przejść na stronę **Szablony urządzeń**. Aby to zrobić, wybierz **Szablony urządzeń** w menu nawigacji po lewej stronie.

![Strona Szablony urządzeń](./media/tutorial-define-device-type/devicetemplates.png)

## <a name="add-a-device-template"></a>Dodawanie szablonu urządzenia

Poniższe kroki przedstawiają sposób tworzenia nowego szablonu urządzenia **Połączony klimatyzator** przeznaczonego dla urządzeń wysyłających do aplikacji dane telemetryczne dotyczące temperatury:

1. Na stronie **Szablony urządzeń** wybierz pozycję **+ Nowy**:

    ![Strona Szablony urządzeń, szablon tworzenia urządzenia](./media/tutorial-define-device-type/newtemplate.png)

2. Na stronie są wyświetlane szablony, spośród których można dokonać wyboru.

    ![Biblioteka szablonów urządzeń](./media/tutorial-define-device-type/devicetemplatelibrary.png)

3. Wybierz niestandardową, wprowadź wartość w polupołączono **środowisko powietrza** jako nazwę szablonu urządzenia, a następnie wybierz pozycję **Utwórz**. Istnieje możliwość przekazania obrazu urządzenia widocznego dla operatorów w eksploratorze urządzeń:

    ![Urządzenie niestandardowe](./media/tutorial-define-device-type/createcustomdevice.png)

4. Upewnij się, że dla szablonu urządzenia **Połączony klimatyzator** jest wyświetlona karta **Miary**, na której definiuje się telemetrię. Każdy definiowany szablon urządzenia ma oddzielne strony umożliwiające:

   * określenie _miar_ wysyłanych przez urządzenie, takich jak telemetria, zdarzenie i stan;

   * definiowanie _ustawień_ używanych do kontrolowania urządzenia;

   * definiowanie _właściwości_ będących metadanymi urządzenia;

   * definiowanie _poleceń_ do uruchamiania bezpośrednio na urządzeniu;

   * definiowanie _reguł_ skojarzonych z urządzeniem;

   * dostosowanie _pulpitu nawigacyjnego_ urządzenia dla operatorów.

     ![Miary klimatyzatora](./media/tutorial-define-device-type/airconmeasurements.png)

     > [!NOTE]
     > Aby zmienić nazwę szablonu urządzenia, wybierz nazwę szablonu w górnej części strony.

5. Aby dodać pomiar danych telemetrycznych temperatury, wybierz pozycję **+ nowy pomiar**. Następnie wybierz pozycję **Telemetria** jako typ miary:

    ![Miary połączonego klimatyzatora](./media/tutorial-define-device-type/airconmeasurementsnew.png)

6. Każdy typ telemetrii zdefiniowany dla szablonu urządzenia obejmuje [opcje konfiguracji](howto-set-up-template.md), takie jak:

   * opcje wyświetlania,

   * szczegółowe informacje o telemetrii,

   * parametry symulacji.

     Aby skonfigurować miarę telemetrii **Temperatura**, skorzystaj z informacji w poniższej tabeli:

     | Ustawienie              | Value         |
     | -------------------- | -----------   |
     | Nazwa wyświetlana         | Temperatura   |
     | Nazwa pola           | temperature   |
     | Jednostki                | P             |
     | Min.                  | 60            |
     | Maks.                  | 110           |
     | Miejsca dziesiętne       | 0             |

     Istnieje także możliwość wyboru koloru na potrzeby wyświetlania danych telemetrii. Aby zapisać definicję telemetrii, wybierz pozycję **Zapisz**:

     ![Konfigurowanie symulacji temperatury](./media/tutorial-define-device-type/temperaturesimulation.png)

7. Po krótkiej chwili na karcie **Miary** zostanie wyświetlony wykres danych telemetrii temperatury pochodzących z symulowanego połączonego klimatyzatora. Do zarządzania widocznością lub agregacją albo do edytowania definicji telemetrii użyj kontrolek:
 
    > [!NOTE]
    > W przypadku telemetrii **średnia** jest ustawiana jako agregacja domyślna. 

    ![Wyświetlenie symulacji temperatury](./media/tutorial-define-device-type/viewsimulation.png)

8. Istnieje także możliwość dostosowania wykresu przy użyciu kontrolek **Wiersz**, **Skumulowany** i **Edytuj zakres czasu**:

    ![Dostosowywanie wykresu](./media/tutorial-define-device-type/customizechart.png)

## <a name="add-an-event-measurement"></a>Dodawanie miary zdarzenia

Użyj zdarzeń, aby zdefiniować dane wysyłane przez urządzenie w momencie wystąpienia takiego zdarzenia, jak błąd lub awaria składnika. Usługa Azure IoT Central może symulować zdarzenia urządzenia, aby umożliwić testowanie zachowania Twojej aplikacji przed podłączeniem prawdziwego urządzenia. Miary zdarzenia dla szablonu urządzenia definiuje się w widoku **Miary**.

1. Aby dodać pomiar zdarzeń **błędów silnika wentylatorów** , wybierz pozycję **+ nowy pomiar**. Następnie wybierz pozycję **Zdarzenie** jako typ miary:

    ![Miary połączonego klimatyzatora](./media/tutorial-define-device-type/eventnew.png)

2. Każdy typ zdarzenia zdefiniowany dla szablonu urządzenia obejmuje [opcje konfiguracji](howto-set-up-template.md), takie jak:

   * Nazwa wyświetlana

   * Nazwa pola

   * Ważność

     Aby skonfigurować pozycję telemetrii **Błąd silnika wentylatora**, skorzystaj z informacji w poniższej tabeli:

     | Ustawienie              | Value             |
     | -------------------- | -----------       |
     | Nazwa wyświetlana         | Błąd silnika wentylatora   |
     | Nazwa pola           | fanmotorerr       |
     | severity             | Błąd             |

     Aby zapisać definicję zdarzenia, wybierz pozycję **Zapisz**:

     ![Konfigurowanie miary zdarzenia](./media/tutorial-define-device-type/eventconfiguration.png)

3. Po krótkiej chwili na karcie **Miary** zostanie wyświetlony wykres dla zdarzeń wygenerowanych losowo przez symulowany połączony klimatyzator. Do zarządzania widocznością lub edytowania definicji zdarzenia użyj kontrolek:

    ![Wyświetlanie symulacji zdarzenia](./media/tutorial-define-device-type/eventview.png)

1. Aby wyświetlić dodatkowe szczegóły dotyczące zdarzenia, wybierz zdarzenie na wykresie:

    ![Wyświetlanie szczegółów zdarzenia](./media/tutorial-define-device-type/eventviewdetail.png)

## <a name="define-a-state-measurement"></a>Definiowanie miary stanu

Stan służy do definiowania i wizualizowania stanu urządzenia lub jego składników w danym czasie. Usługa Azure IoT Central może symulować stan urządzenia, aby umożliwić testowanie zachowania Twojej aplikacji przed podłączeniem prawdziwego urządzenia. Miary stanu dla typu urządzenia definiuje się w widoku **Miary**.

1. Aby dodać pomiar stanu **trybu wentylatorów** , wybierz pozycję **+ nowy pomiar**. Następnie wybierz pozycję **Stan** jako typ miary:

    ![Miary stanu połączonego klimatyzatora](./media/tutorial-define-device-type/statenew.png)

2. Każdy typ stanu zdefiniowany dla szablonu urządzenia zawiera [opcje konfiguracji](howto-set-up-template.md), takie jak:

   * Nazwa wyświetlana

   * Nazwa pola

   * Wartości z opcjonalnymi etykietami

   * Kolor dla każdej wartości

     Aby skonfigurować stan **Tryb wentylatora**, skorzystaj z informacji w poniższej tabeli:

     | Ustawienie              | Value             |
     | -------------------- | -----------       |
     | Nazwa wyświetlana         | Tryb wentylatora          |
     | Nazwa pola           | fanmode           |
     | Value                | 1                 |
     | Wyświetlana etykieta        | Działa         |
     | Value                | 0                 |
     | Wyświetlana etykieta        | Zatrzymano           |

     Aby zapisać definicję pomiaru stanu, wybierz pozycję **Zapisz**:

     ![Konfigurowanie miary stanu](./media/tutorial-define-device-type/stateconfiguration.png)

3. Po krótkiej chwili na karcie **Miary** zostanie wyświetlony wykres stanów wygenerowanych losowo przez symulowany połączony klimatyzator. Do zarządzania widocznością lub edytowania definicji stanu użyj kontrolek:

    ![Widok symulacji stanu](./media/tutorial-define-device-type/stateview.png)

4. Jeśli w krótkim czasie urządzenie wyśle za dużo punktów danych, miara stanu będzie wyświetlana za pomocą innej wizualizacji. Wybierz wykres, aby wyświetlić wszystkie punkty danych w tym okresie, które są wyświetlane w kolejności chronologicznej. Zakres czasu można także zawęzić, aby wyświetlić miarę bardziej szczegółowo.

## <a name="settings-properties-and-commands"></a>Ustawienia, właściwości i polecenia

Ustawienia, właściwości oraz polecenia to różne wartości zdefiniowane w szablonie urządzenia i skojarzone z poszczególnymi urządzeniami:

* _Ustawienia_ umożliwiają wysyłanie danych konfiguracji do urządzenia z aplikacji. Na przykład operator może użyć ustawienia do zmiany interwału telemetrii urządzenia z dwóch na pięć sekund. Po zmianie ustawienia przez operatora ustawienie jest oznaczone jako oczekujące w interfejsie użytkownika, dopóki urządzenie nie odpowie za pomocą potwierdzenia.

* _Właściwości_ możesz użyć do definiowania metadanych skojarzonych z urządzeniem. Są dostępne dwie kategorie właściwości:
    
  * _Właściwości aplikacji_ umożliwiają rejestrowanie informacji o urządzeniu w aplikacji. Na przykład możesz użyć właściwości aplikacji do zarejestrowania lokalizacji urządzenia i daty ostatniego serwisowania. Właściwości te są przechowywane w aplikacji i nie są synchronizowane z urządzeniem. Operator może przypisać wartości do właściwości.

  * _Właściwości urządzenia_ umożliwiają urządzaniu wysyłanie wartości właściwości do aplikacji. Te właściwości może zmienić tylko urządzenie. Dla operatora właściwości urządzenia są tylko do odczytu. W tym scenariuszu z połączonym klimatyzatorem wersja oprogramowania układowego i numer seryjny urządzenia to właściwości urządzenia zgłoszone przez urządzenie.
    
    Aby uzyskać więcej informacji, zobacz [Właściwości](howto-set-up-template.md#properties) w instrukcjach konfigurowania szablonu urządzenia.

* _Polecenia_ służą do zdalnego zarządzania urządzeniem z poziomu aplikacji. Polecenia można uruchamiać z chmury bezpośrednio na urządzeniu, aby sterować urządzeniami. Na przykład operator może uruchamiać polecenia, takie jak reboot, aby natychmiast ponownie uruchomić urządzenie.

## <a name="use-settings"></a>Używanie ustawień

*Ustawienia* umożliwiają operatorowi wysyłanie danych konfiguracji do urządzenia. W tej sekcji do szablonu urządzenia **Połączony klimatyzator** jest dodawane ustawienie, które umożliwia operatorowi ustawienie temperatury docelowej połączonego klimatyzatora.

1. Przejdź do karty **Ustawienia** szablonu urządzenia **Połączony klimatyzator**.

2. Istnieje możliwość utworzenia ustawień różnych typów, takich jak tekst lub liczby. Wybierz pozycję **Liczba** , aby dodać do urządzenia ustawienie liczbowe.

3. Aby skonfigurować ustawienie **Ustaw temperaturę**, skorzystaj z informacji w poniższej tabeli:

    | Pole                | Value           |
    | -------------------- | -----------     |
    | Nazwa wyświetlana         | Ustaw temperaturę |
    | Nazwa pola           | setTemperature  |
    | Jednostka miary      | P               |
    | Miejsca dziesiętne       | 1               |
    | Wartość minimalna        | 20              |
    | Wartość maksymalna        | 200             |
    | Wartość początkowa        | 80              |
    | Opis          | Ustawianie temperatury docelowej dla klimatyzatora |

    Następnie wybierz pozycję **Zapisz**:

    ![Konfigurowanie ustawienia Ustaw temperaturę](./media/tutorial-define-device-type/configuresetting.png)

    > [!NOTE]
    > Gdy urządzenie potwierdzi zmianę ustawień, stan ustawienia zostanie zmieniony na **zsynchronizowane**.

4. Układ karty **Ustawienia** możesz dostosować, przenosząc i zmieniając rozmiar kafelków ustawień:

    ![Dostosowywanie układu ustawień](./media/tutorial-define-device-type/settingslayout.png)

## <a name="use-properties"></a>Korzystanie z właściwości

*Właściwości aplikacji* umożliwiają przechowywanie informacji o urządzeniu w aplikacji. W tej sekcji do szablonu urządzenia **Połączony klimatyzator** są dodawane właściwości aplikacji na potrzeby przechowywania lokalizacji urządzenia i daty ostatniego serwisowania. Te właściwości można edytować w aplikacji. Urządzenie zgłasza także takie właściwości, jak numer seryjny i wersja oprogramowania układowego, które są tylko do odczytu w aplikacji.

1. Przejdź do karty **Właściwości** szablonu urządzenia **Połączony klimatyzator**.

1. Istnieje możliwość utworzenia właściwości urządzenia różnych typów, takich jak tekst lub liczby. Aby dodać właściwość lokalizacji do szablonu urządzenia, wybierz pozycję **Lokalizacja**. Aby skonfigurować właściwość lokalizacji, skorzystaj z informacji w poniższej tabeli:

    | Pole                | Value                |
    | -------------------- | -------------------- |
    | Nazwa wyświetlana         | Location             |
    | Nazwa pola           | location             |
    | Wartość początkowa        | Seattle, WA          |
    | Opis          | Lokalizacja urządzenia      |

    Pozostaw wartości domyślne w pozostałych polach.

    ![Konfigurowanie właściwości urządzenia](./media/tutorial-define-device-type/configureproperties.png)

    Wybierz pozycję **Zapisz**.

1. Aby dodać właściwość daty ostatniego serwisowania do szablonu urządzenia, wybierz pozycję **Data**.

1. Aby skonfigurować właściwość daty ostatniego serwisowania, skorzystaj z informacji w poniższej tabeli:

    | Pole                | Value                   |
    | -------------------- | ----------------------- |
    | Nazwa wyświetlana         | Data ostatniego serwisowania       |
    | Nazwa pola           | serviceDate             |
    | Wartość początkowa        | 2019-01-01                |
    | Opis          | Ostatnio serwisowane           |

    ![Konfigurowanie właściwości urządzenia](./media/tutorial-define-device-type/configureproperties2.png)

    Wybierz pozycję **Zapisz**.

1. Układ karty **Właściwości** możesz dostosować, przenosząc i zmieniając rozmiar kafelków właściwości.

1. Aby dodać właściwość urządzenia, taką jak wersja oprogramowania układowego, do szablonu urządzenia, wybierz pozycję **Właściwość urządzenia**.

1. Aby skonfigurować wersję oprogramowania układowego, skorzystaj z informacji w poniższej tabeli:

    | Pole                | Value                   |
    | -------------------- | ----------------------- |
    | Nazwa wyświetlana         | Wersja oprogramowania układowego        |
    | Nazwa pola           | firmwareVersion         |
    | Typ danych            | text                    |
    | Opis          | Wersja oprogramowania układowego klimatyzatora |

    ![Skonfiguruj wersję oprogramowania układowego](./media/tutorial-define-device-type/configureproperties3.png)

    Wybierz pozycję **Zapisz**.

1. Aby dodać właściwość urządzenia, taką jak numer seryjny, do szablonu urządzenia, wybierz pozycję **Właściwość urządzenia**.

1. Aby skonfigurować numer seryjny, skorzystaj z informacji w poniższej tabeli:

    | Pole                | Value                   |
    | -------------------- | ----------------------- |
    | Nazwa wyświetlana         | Numer seryjny           |
    | Nazwa pola           | serialNumber            |
    | Typ danych            | text                    |
    | Opis          | Numer seryjny klimatyzatora  |

    ![Skonfiguruj numer seryjny](./media/tutorial-define-device-type/configureproperties4.png)

    Wybierz pozycję **Zapisz**.

    > [!NOTE]
    > Właściwości urządzenia są wysyłane z urządzenia do aplikacji. Wartości wersji oprogramowania układowego i numeru seryjnego zostaną zaktualizowane, gdy rzeczywiste urządzenie połączy się z usługą IoT Central.

## <a name="use-commands"></a>Korzystanie z poleceń

Za pomocą _poleceń_ można umożliwić operatorowi uruchamianie poleceń bezpośrednio na urządzeniu. W tej sekcji dodasz do szablonu urządzenia **Połączony klimatyzator** polecenie, które umożliwia operatorowi wysyłanie komunikatów do połączonego klimatyzatora.

1. Przejdź do karty **Polecenia** szablonu urządzenia **Połączony klimatyzator**, aby edytować szablon.

1. Wybierz pozycję **+ nowe polecenie** , aby dodać polecenie do urządzenia i zacznij konfigurować nowe polecenie.

1. Aby skonfigurować nowe polecenie, skorzystaj z informacji w poniższej tabeli:

    | Pole                | Value           |
    | -------------------- | -----------     |
    | Nazwa wyświetlana         | Polecenie echo    |
    | Nazwa pola           | echo            |
    | Domyślny limit czasu      | 30              |
    | Typ wyświetlany         | text            |
    | Opis          | Polecenie urządzenia  |  

    Możesz dodać dodatkowe dane wejściowe do polecenia, wybierając pozycję **+** dla **pól wejściowych**.

    ![Przygotowanie do dodania ustawienia](./media/tutorial-define-device-type/commandsecho1.png)

     Wybierz pozycję **Zapisz**.

1. Układ karty **Polecenia** możesz dostosować, przenosząc i zmieniając rozmiar kafelków poleceń.

## <a name="view-your-simulated-device"></a>Wyświetlanie symulowanego urządzenia

Po zdefiniowaniu Twojego szablonu urządzenia **Połączony klimatyzator** możesz dostosować jego **Pulpit nawigacyjny** tak, aby obejmował zdefiniowane miary, ustawienia i właściwości. Następnie możesz wyświetlić podgląd pulpitu nawigacyjnego jako operator:

1. Wybierz kartę **Pulpit nawigacyjny** szablonu urządzenia **Połączony klimatyzator**.

1. Wybierz pozycję **Wykres liniowy** , aby dodać składnik do **pulpitu nawigacyjnego**.

1. Skonfiguruj składnik **Wykres liniowy**, korzystając z informacji w poniższej tabeli:

    | Ustawienie      | Value       |
    | ------------ | ----------- |
    | Tytuł        | Temperatura |
    | Zakres czasu   | Ostatnie 30 minut |
    | Miary     | Temperatura (wybierz **widoczność** obok **temperatury**) |

    ![Ustawienia wykresu liniowego](./media/tutorial-define-device-type/linechartsettings.png)

    Następnie wybierz pozycję **Zapisz**.

1. Wybierz składnik **historia zdarzeń** , korzystając z informacji podanych w poniższej tabeli:

    | Ustawienie      | Value       |
    | ------------ | ----------- |
    | Tytuł        | Zdarzenia silnika wentylatora |
    | Zakres czasu   | Ostatnie 30 minut |
    | Miary     | Błąd silnika wentylatorów (wybierz **widoczność** obok **błędu wentylatoru**) |

    ![Ustawienia wykresu zdarzeń](./media/tutorial-define-device-type/dashboardeventchartsetting.png)

    Następnie wybierz pozycję **Zapisz**.

1. Skonfiguruj składnik **Historia stanu**, korzystając z informacji w poniższej tabeli:

    | Ustawienie      | Value       |
    | ------------ | ----------- |
    | Tytuł        | Tryb wentylatora |
    | Zakres czasu   | Ostatnie 30 minut |
    | Miary | Tryb wentylatoru (wybierz **widoczność** obok **trybu wentylator**) |

    ![Ustawienia wykresu liniowego](./media/tutorial-define-device-type/dashboardstatechartsetting.png)

    Następnie wybierz pozycję **Zapisz**.

1. Aby dodać ustawienia i właściwości urządzenia do pulpitu nawigacyjnego, wybierz pozycję **Ustawienia i właściwości**. Wybierz pozycję **Dodaj/Usuń** , aby dodać ustawienia lub właściwości, które chcesz zobaczyć na pulpicie nawigacyjnym.

1. Skonfiguruj składnik **Ustawienia i właściwości**, korzystając z informacji w poniższej tabeli:

    | Ustawienie                 | Value         |
    | ----------------------- | ------------- |
    | Tytuł                   | Właściwości urządzenia |
    | Ustawienia i właściwości | Ustaw temperaturę<br/>Numer seryjny<br/>Wersja oprogramowania układowego |

    Ustawienia i właściwości zdefiniowane wcześniej na stronach **Ustawienia i właściwości** są wyświetlone w polu **Dostępne kolumny**.

    ![Określanie ustawień właściwości temperatury](./media/tutorial-define-device-type/propertysettings4.png)

    Następnie wybierz pozycję **Zapisz**.

1. Dane symulowane dla Twojego połączonego klimatyzatora będą teraz widoczne na pulpicie nawigacyjnym. Możesz edytować kafelki i układ pulpitu nawigacyjnego:

    ![Wyświetl pulpit nawigacyjny](./media/tutorial-define-device-type/dashboard.png)

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

* [Konfigurowanie reguł i akcji dla urządzenia](tutorial-configure-rules.md)
* [Dostosowywanie widoków operatora](tutorial-customize-operator.md)