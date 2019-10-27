---
title: Definiowanie nowego typu urządzenia IoT na platformie Azure IoT Central | Microsoft Docs
description: W tym samouczku pokazano, jak utworzyć nowy szablon urządzenia usługi Azure IoT w aplikacji IoT Central platformy Azure. Zdefiniuj dane telemetryczne, stan, właściwości i polecenia dla danego typu.
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 30013bd72ac23475fe0e7b5eda50d8ec43464bad
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72955984"
---
# <a name="tutorial-define-a-new-iot-device-type-in-your-azure-iot-central-application-preview-features"></a>Samouczek: Definiowanie nowego typu urządzenia IoT w aplikacji IoT Central platformy Azure (funkcje w wersji zapoznawczej)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Szablon urządzenia jest planem, który definiuje cechy i zachowania typu urządzenia, które nawiązuje połączenie z aplikacją IoT Central platformy Azure.

Na przykład Konstruktor może utworzyć szablon urządzenia dla połączonego wentylatora o następujących cechach:

- Wysyła dane telemetryczne dotyczące temperatury
- Wysyła Właściwość Location
- Wysyła zdarzenia błędów silnika wentylatorowego
- Wysyła stan operacyjny wentylatora
- Właściwość szybkość wentylatorów zapisywalnych
- Polecenie ponownego uruchamiania urządzenia
- Pulpit nawigacyjny, który zapewnia ogólny widok urządzenia

Z tego szablonu urządzenia operator może tworzyć i łączyć urządzenia wentylatorów w rzeczywistości. Wszystkie te wentylatory mają pomiary, właściwości i polecenia używane przez operatorów do monitorowania i zarządzania. Operatory korzystają z pulpitów nawigacyjnych i formularzy urządzeń w celu współdziałania z urządzeniami wentylatorów.

> [!NOTE]
> Tylko konstruktory i Administratorzy mogą tworzyć, edytować i usuwać szablony urządzeń. Każdy użytkownik może tworzyć urządzenia na stronie **urządzenia** z istniejących szablonów urządzeń.

[Plug and Play IoT](../../iot-pnp/overview-iot-plug-and-play.md) umożliwia IoT Central integrację urządzeń bez konieczności pisania kodu urządzenia osadzonego. Podstawą Plug and Play IoT jest schemat modelu możliwości urządzenia, który opisuje możliwości urządzeń. W aplikacji IoT Central w wersji zapoznawczej szablony urządzeń używają tych modeli możliwości urządzeń Plug and Play IoT.

Jako Konstruktor można utworzyć szablony urządzeń za pomocą kilku opcji:

- Zaprojektuj szablon urządzenia w IoT Central a następnie Zaimplementuj jego model możliwości urządzenia w kodzie urządzenia.
- Zaimportuj model możliwości urządzenia z [wykazu urządzeń z certyfikatem platformy Azure dla IoT](https://aka.ms/iotdevcat) , a następnie Dodaj wszystkie właściwości, dostosowania i pulpity nawigacyjne, których potrzebuje IoT Central aplikacji.
- Utwórz model możliwości urządzenia przy użyciu programu Visual Studio Code. Zaimplementuj swój kod urządzenia z modelu. Ręcznie zaimportuj model możliwości urządzenia do aplikacji IoT Central, a następnie Dodaj wszystkie właściwości, dostosowania i pulpity nawigacyjne, których potrzebuje aplikacja IoT Central.
- Utwórz model możliwości urządzenia przy użyciu programu Visual Studio Code. Zaimplementuj swój kod urządzenia z modelu, a następnie połącz rzeczywiste urządzenie z aplikacją IoT Central przy użyciu połączenia urządzenia po raz pierwszy. IoT Central odnajduje i importuje model możliwości urządzenia z repozytorium publicznego. Następnie można dodać wszystkie właściwości, dostosowania i pulpity nawigacyjne, których aplikacja IoT Central musi być szablonem urządzenia.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka potrzebna jest aplikacja usługi Azure IoT Central. Postępuj zgodnie z tym przewodnikiem Szybki Start, aby [utworzyć aplikację IoT Central platformy Azure](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="create-a-device-template-from-the-device-catalog"></a>Tworzenie szablonu urządzenia z poziomu katalogu urządzeń

Jako Konstruktor możesz szybko rozpocząć tworzenie rozwiązania przy użyciu urządzenia z certyfikatem usługi IoT Plug and Play, które jest wymienione w [wykazie urządzeń Azure IoT](https://catalog.azureiotsolutions.com/alldevices). IoT Central integruje się z wykazem urządzeń, aby umożliwić importowanie modelu możliwości urządzenia z dowolnego z certyfikowanych urządzeń Plug and Play IoT. Aby utworzyć szablon urządzenia na podstawie jednego z tych urządzeń w IoT Central:

1. Przejdź do strony **Szablony urządzeń** w aplikacji IoT Central.
1. Wybierz pozycję **+ Nowy**, a następnie wybierz dowolne z certyfikowanych urządzeń Plug and Play IoT z wykazu wymienionego poniżej. IoT Central tworzy szablon urządzenia w oparciu o ten model możliwości urządzenia.
1. Dodaj do szablonu urządzenia wszystkie właściwości, dostosowania lub widoki w chmurze.
1. Wybierz pozycję **Opublikuj** , aby opublikować ten szablon urządzenia, aby udostępnić go operatorom do wyświetlania i łączenia urządzeń.

## <a name="create-a-device-template-from-scratch"></a>Tworzenie szablonu urządzenia od podstaw

Szablon urządzenia zawiera:

- _Model możliwości urządzenia_ określający dane telemetryczne, właściwości i polecenia implementowane przez urządzenie. Te możliwości są zorganizowane w jeden lub więcej interfejsów.
- _Właściwości chmury_ definiujące informacje o urządzeniach, które IoT Central aplikacji. Na przykład właściwość chmury może rejestrować dane, które urządzenie było ostatnio serwisowane. Te informacje nigdy nie są udostępniane na urządzeniu.
- _Dostosowania_ pozwalają konstruktorowi zastępować niektóre definicje w modelu możliwości urządzenia. Na przykład Konstruktor może zastąpić nazwę właściwości urządzenia. Nazwy właściwości są wyświetlane na IoT Central pulpitów nawigacyjnych i formularzach.
- _Pulpity nawigacyjne i formularze_ umożliwiają konstruktorowi Tworzenie interfejsu użytkownika, który umożliwia operatorom monitorowanie i zarządzanie urządzeniami podłączonymi do aplikacji.

Aby utworzyć szablon urządzenia w IoT Central:

1. Przejdź do strony **Szablony urządzeń** w aplikacji IoT Central.
1. Wybierz pozycję **+ Nowy**, a następnie pozycję **niestandardowy**.
1. Wprowadź nazwę szablonu, na przykład **czujnik środowiska**.
1. Naciśnij klawisz **Enter**. IoT Central tworzy pusty szablon urządzenia.

## <a name="manage-a-device-template"></a>Zarządzanie szablonem urządzenia

Można zmienić nazwę szablonu lub usunąć go ze strony głównej szablonu.

Po dodaniu modelu możliwości urządzenia do szablonu można go opublikować. Nie można połączyć urządzenia na podstawie tego szablonu, aby operatory były widoczne na stronie **urządzenia** do momentu opublikowania szablonu.

## <a name="create-a-capability-model"></a>Tworzenie modelu możliwości

Aby utworzyć model możliwości urządzenia, możesz:

- Użyj IoT Central, aby utworzyć niestandardowy model od podstaw.
- Importuj model z pliku JSON. Konstruktor urządzeń mógł używać Visual Studio Code do tworzenia modelu możliwości urządzenia dla aplikacji.
- Wybierz jedno z urządzeń z katalogu urządzeń. Ta opcja Importuje model możliwości urządzenia Opublikowany przez producenta dla tego urządzenia. Model możliwości urządzenia zaimportowany jak ten jest automatycznie publikowany.

## <a name="manage-a-capability-model"></a>Zarządzanie modelem możliwości

Po utworzeniu modelu możliwości urządzenia można:

- Dodaj interfejsy do modelu. Model musi mieć co najmniej jeden interfejs.
- Edytuj metadane modelu, takie jak jego identyfikator, przestrzeń nazw i nazwa.
- Usuń model.

## <a name="create-an-interface"></a>Tworzenie interfejsu

Możliwość urządzenia musi mieć co najmniej jeden interfejs. Interfejs jest kolekcją możliwości wielokrotnego użytku.

Aby utworzyć interfejs:

1. Przejdź do modelu możliwości urządzenia i wybierz pozycję **+ Dodaj interfejs**.

1. Na stronie **Wybieranie interfejsu** można:

    - Utwórz niestandardowy interfejs od podstaw.
    - Importuj istniejący interfejs z pliku. Konstruktor urządzeń mógł używać Visual Studio Code do tworzenia interfejsu dla urządzenia.
    - Wybierz jeden z standardowych interfejsów, takich jak interfejs **informacji o urządzeniu** . Interfejsy standardowe określają możliwości typowe dla wielu urządzeń. Te standardowe interfejsy są publikowane przez Microsoft Azure IoT i nie mogą być poddane lub edytowane.

1. Po utworzeniu interfejsu wybierz opcję **Edytuj tożsamość** , aby zmienić nazwę wyświetlaną interfejsu.

1. Jeśli zdecydujesz się utworzyć interfejs niestandardowy od podstaw, możesz dodać możliwości urządzenia. Możliwości urządzenia to dane telemetryczne, właściwości i polecenia.

### <a name="telemetry"></a>Telemetria

Telemetrię jest strumieniem wartości wysyłanych z urządzenia, zazwyczaj z czujnika. Na przykład czujnik może zgłosić temperaturę otoczenia.

W poniższej tabeli przedstawiono ustawienia konfiguracji dla funkcji telemetrii:

| Pole | Opis |
| ----- | ----------- |
| Nazwa wyświetlana | Nazwa wyświetlana wartości telemetrii używanej na pulpitach nawigacyjnych i formularzach. |
| Nazwa | Nazwa pola w komunikacie telemetrii. IoT Central generuje wartość dla tego pola z nazwy wyświetlanej, ale w razie potrzeby można wybrać własną wartość. |
| Typ możliwości | Telemetrii. |
| Typ semantyczny | Typ semantyczny danych telemetrycznych, takich jak temperatura, stan lub zdarzenie. Wybór typu semantycznego określa, które z poniższych pól są dostępne. |
| Schemat | Typ danych telemetrii, taki jak Double, String lub Vector. Dostępne opcje są określane przez typ semantyczny. Schemat nie jest dostępny dla typów semantyki zdarzenia i stanu. |
| Ważność | Dostępne tylko dla typu semantycznego zdarzenia. **Błąd**, **informacje**lub **Ostrzeżenie**. |
| Wartości stanu | Dostępne tylko dla typu semantyki stanu. Zdefiniuj możliwe wartości stanu, z których każdy ma nazwę wyświetlaną, nazwę, typ wyliczenia i wartość. |
| Jednostka | Jednostka wartości telemetrii, np. **mph**, **%** lub **&deg;C**. |
| Jednostka wyświetlania | Jednostka wyświetlania do użycia na pulpitach nawigacyjnych i formularzach. |
| Komentarz | Wszelkie komentarze dotyczące funkcji telemetrii. |
| Opis | Opis możliwości telemetrii. |

### <a name="properties"></a>Właściwości

Właściwości reprezentują wartości w czasie. Na przykład urządzenie może użyć właściwości do raportowania temperatury docelowej, do której próbujesz uzyskać dostęp. Można ustawić właściwości do zapisu z IoT Central.

W poniższej tabeli przedstawiono ustawienia konfiguracji dla funkcji właściwości:

| Pole | Opis |
| ----- | ----------- |
| Nazwa wyświetlana | Nazwa wyświetlana wartości właściwości używanej na pulpitach nawigacyjnych i formularzach. |
| Nazwa | Nazwa właściwości. IoT Central generuje wartość dla tego pola z nazwy wyświetlanej, ale w razie potrzeby można wybrać własną wartość. |
| Typ możliwości | wartość. |
| Typ semantyczny | Typ semantyczny właściwości, takiej jak temperatura, stan lub zdarzenie. Wybór typu semantycznego określa, które z poniższych pól są dostępne. |
| Schemat | Typ danych właściwości, taki jak Double, String lub Vector. Dostępne opcje są określane przez typ semantyczny. Schemat nie jest dostępny dla typów semantyki zdarzenia i stanu. |
| Zapisywalny | Jeśli właściwość nie jest zapisywalna, urządzenie może raportować wartości właściwości do IoT Central. Jeśli właściwość jest zapisywalna, urządzenie może raportować wartości właściwości do IoT Central i IoT Central może wysyłać do urządzenia aktualizacje właściwości.
| Ważność | Dostępne tylko dla typu semantycznego zdarzenia. **Błąd**, **informacje**lub **Ostrzeżenie**. |
| Wartości stanu | Dostępne tylko dla typu semantyki stanu. Zdefiniuj możliwe wartości stanu, z których każdy ma nazwę wyświetlaną, nazwę, typ wyliczenia i wartość. |
| Jednostka | Jednostka dla wartości właściwości, takiej jak **mph**, **%** lub **&deg;C**. |
| Jednostka wyświetlania | Jednostka wyświetlania do użycia na pulpitach nawigacyjnych i formularzach. |
| Komentarz | Wszelkie komentarze dotyczące możliwości właściwości. |
| Opis | Opis możliwości właściwości. |

### <a name="commands"></a>Polecenia

Polecenia urządzenia można wywołać z IoT Central. Polecenia opcjonalne umożliwiają przekazywanie parametrów do urządzenia i odbieranie odpowiedzi z urządzenia. Można na przykład wywołać polecenie w celu ponownego uruchomienia urządzenia w ciągu 10 sekund.

W poniższej tabeli przedstawiono ustawienia konfiguracji dla funkcji polecenia:

| Pole | Opis |
| ----- | ----------- |
| Nazwa wyświetlana | Nazwa wyświetlana polecenia użyta na pulpitach nawigacyjnych i formularzach. |
| Nazwa | Nazwa polecenia. IoT Central generuje wartość dla tego pola z nazwy wyświetlanej, ale w razie potrzeby można wybrać własną wartość. |
| Typ możliwości | Polecenie |
| Polecenie | SynchronousExecutionType. |
| Komentarz | Wszelkie komentarze dotyczące funkcji polecenia. |
| Opis | Opis możliwości polecenia. |
| Prośba | Jeśli ta funkcja jest włączona, definicja parametru żądania, w tym: nazwa, nazwa wyświetlana, schemat, jednostka i jednostka wyświetlania. |
| Odpowiedź | Jeśli ta funkcja jest włączona, definicja odpowiedzi polecenia, w tym: nazwa, nazwa wyświetlana, schemat, jednostka i jednostka wyświetlania. |

## <a name="manage-an-interface"></a>Zarządzanie interfejsem

Pod warunkiem, że interfejs nie został opublikowany, można edytować możliwości zdefiniowane przez interfejs. Po opublikowaniu interfejsu należy utworzyć nową wersję szablonu urządzenia i wersję interfejsu, aby wprowadzić zmiany. Zmiany, które nie wymagają przechowywania wersji, takie jak nazwy wyświetlane lub jednostki, można wprowadzać w sekcji **Dostosowywanie** .

Możesz również wyeksportować interfejs jako plik JSON, jeśli chcesz użyć go ponownie w innym modelu możliwości.

## <a name="add-cloud-properties"></a>Dodawanie właściwości chmury

Właściwości chmury służą do przechowywania informacji o urządzeniach w IoT Central. Właściwości chmury nigdy nie są wysyłane do urządzenia. Można na przykład użyć właściwości chmury do przechowywania nazwy klienta, który zainstalował urządzenie, lub daty jego ostatniej usługi.

W poniższej tabeli przedstawiono ustawienia konfiguracji dla właściwości chmury:

| Pole | Opis |
| ----- | ----------- |
| Nazwa wyświetlana | Nazwa wyświetlana wartości właściwości chmury używanej na pulpitach nawigacyjnych i formularzach. |
| Nazwa | Nazwa właściwości chmury. IoT Central generuje wartość dla tego pola z nazwy wyświetlanej, ale w razie potrzeby można wybrać własną wartość. |
| Typ semantyczny | Typ semantyczny właściwości, takiej jak temperatura, stan lub zdarzenie. Wybór typu semantycznego określa, które z poniższych pól są dostępne. |
| Schemat | Typ danych właściwości chmury, taki jak Double, String lub Vector. Dostępne opcje są określane przez typ semantyczny. |

## <a name="add-customizations"></a>Dodawanie dostosowań

Dostosowań należy używać w przypadku konieczności modyfikacji zaimportowanego interfejsu lub dodania funkcji specyficznych dla IoT Central do funkcji. Można dostosować tylko pola, które nie łamią zgodności interfejsów. Możesz na przykład:

- Dostosuj nazwę wyświetlaną i jednostki możliwości.
- Dodaj domyślny kolor, który ma być używany, gdy wartość pojawia się na wykresie.
- Określ początkową, minimalną i maksymalną wartość właściwości.

Nie można dostosować nazwy możliwości ani typu możliwości. Jeśli istnieją zmiany, których nie można wprowadzić w sekcji **Dostosowywanie** , musisz zmienić wersję szablonu i interfejsu urządzenia, aby zmodyfikować tę możliwość.

### <a name="generate-default-views"></a>Generuj widoki domyślne

Generowanie widoków domyślnych jest szybkim sposobem na rozpoczęcie wizualizacji ważnych informacji o urządzeniu. Dla szablonu urządzenia zostanie wygenerowanych maksymalnie trzy widoki domyślne:

- **Polecenia** udostępniają widok z poleceniami urządzenia i umożliwiają operatorowi wysłanie ich do urządzenia.
- **Przegląd** udostępnia widok danych telemetrycznych urządzenia, wyświetlanie wykresów i metryk.
- Informacje **o** urządzeniu zawierają informacje o urządzeniach, które są wyświetlane w obszarze właściwości urządzenia.

Po wybraniu opcji **Generuj domyślne widoki** zobaczysz, że zostały one automatycznie dodane w sekcji **widoki** szablonu urządzenia.

## <a name="add-dashboards"></a>Dodaj pulpity nawigacyjne

Dodaj pulpity nawigacyjne do szablonu urządzenia, aby umożliwić operatorom wizualizację urządzenia za pomocą wykresów i metryk. Dla szablonu urządzenia można mieć wiele pulpitów nawigacyjnych.

Aby dodać pulpit nawigacyjny do szablonu urządzenia:

- Przejdź do szablonu urządzenia i wybierz pozycję **widoki**.
- Następnie wybierz **wizualizację urządzenia**.
- Wprowadź nazwę pulpitu nawigacyjnego w polu **Nazwa pulpitu nawigacyjnego**.
- Dodaj kafelki do pulpitu nawigacyjnego z listy kafelków statycznej, właściwości, chmury, telemetrii i poleceń. Przeciągnij i upuść kafelki, które chcesz dodać do pulpitu nawigacyjnego.
- Aby wykreolić wiele wartości telemetrii na pojedynczym kafelku wykresu, wybierz wartości telemetryczne, a następnie wybierz pozycję **Połącz**.
- Skonfiguruj wszystkie dodawane kafelki, aby dostosować sposób wyświetlania danych przez wybranie ikony koła zębatego lub wybranie przycisku **Zmień konfigurację** na kafelku wykresu.
- Rozmieść kafelki na pulpicie nawigacyjnym i zmień ich rozmiar.
- Zapisz zmiany.

### <a name="configure-preview-device-to-view-dashboard"></a>Konfigurowanie urządzenia w wersji zapoznawczej do wyświetlania pulpitu nawigacyjnego

Aby wyświetlić i przetestować pulpit nawigacyjny, możesz wybrać pozycję **Konfiguruj urządzenie w wersji zapoznawczej**, która umożliwia wyświetlenie pulpitu nawigacyjnego jako operatora widocznego po jego opublikowaniu. Ta opcja umożliwia zweryfikowanie, czy w widokach są wyświetlane poprawne dane. Możesz wybrać opcję Brak urządzenia w wersji zapoznawczej, rzeczywiste urządzenie testowe skonfigurowane dla szablonu urządzenia lub istniejące urządzenie w aplikacji przy użyciu identyfikatora urządzenia.

## <a name="add-forms"></a>Dodawanie formularzy

Dodaj formularze do szablonu urządzenia, aby umożliwić operatorom zarządzanie urządzeniem przez wyświetlanie i Ustawianie właściwości. Operatory mogą edytować tylko właściwości chmury i zapisywalne właściwości urządzeń. Dla szablonu urządzenia można mieć wiele formularzy.

Aby dodać formularz do szablonu urządzenia:

1. Przejdź do szablonu urządzenia i wybierz pozycję **widoki**.
1. Następnie wybierz pozycję **Edytowanie urządzenia i dane w chmurze**.
1. Wprowadź nazwę formularza w polu **nazwa formularza**.
1. Wybierz liczbę kolumn, które mają być używane do układania formularza.
1. Dodaj właściwości do istniejącej sekcji w formularzu lub wybierz pozycję Właściwości, a następnie wybierz pozycję **Dodaj sekcję**. Użyj sekcji, aby grupować właściwości w formularzu. Możesz dodać tytuł do sekcji.
1. Skonfiguruj każdą właściwość w formularzu, aby dostosować jej zachowanie.
1. Rozmieść właściwości w formularzu.
1. Zapisz zmiany.

## <a name="publish-a-device-template"></a>Publikowanie szablonu urządzenia

Aby można było połączyć urządzenie, które implementuje model możliwości urządzenia, należy opublikować szablon urządzenia.

Po opublikowaniu szablonu urządzenia można wprowadzać tylko ograniczone zmiany w modelu możliwości urządzenia. Aby zmodyfikować interfejs, należy [utworzyć i opublikować nową wersję](./howto-version-device-template-pnp.md).

Aby opublikować szablon urządzenia, przejdź do szablonu urządzenia i wybierz pozycję **Publikuj**.

Po opublikowaniu szablonu urządzenia operator może przejść do strony **urządzenia** i dodać rzeczywiste lub symulowane urządzenia, które używają szablonu urządzenia. Można nadal modyfikować i zapisywać szablon urządzenia w miarę wprowadzania zmian, jednak jeśli chcesz wypchnąć te zmiany do operatora w celu wyświetlenia na stronie **urządzenia** , musisz wybrać opcję **Publikuj** za każdym razem.

## <a name="define-a-new-iot-gateway-device-type-preview-features"></a>Definiowanie nowego typu urządzenia bramy IoT (funkcje w wersji zapoznawczej)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

W tym samouczku przedstawiono jako konstruktora, jak używać szablonu urządzenia bramy do definiowania nowego typu urządzenia Azure IoT w aplikacji IoT Central platformy Azure. 

W tej sekcji utworzysz szablon **inteligentnego kompilowania** urządzenia. Inteligentne urządzenie bramy do kompilowania:

* Wysyła dane telemetryczne, takie jak temperatury i zajętości.
* Reaguje na właściwości zapisywalne w chmurze, takie jak interwał wysyłania danych telemetrycznych.
* Reaguje na polecenia, takie jak resetowanie temperatury.
* Zezwala na relacje z innymi modelami możliwości urządzeń

### <a name="create-iot-device-templates"></a>Tworzenie szablonów urządzeń IoT

Utworzysz szablony urządzeń IoT. 

Kliknij pozycję Szablony urządzeń na lewym pasku nawigacyjnym, kliknij pozycję **+ Nowy**, wybierz kafelek **urządzenia IoT** i wybierz kafelek Czujnik zajętości, a następnie kliknij przycisk **Dalej: Dostosuj.**

![Urządzenie IoT](./media/tutorial-define-iot-device-type-pnp/gateway-downstream-new.png)

Zostanie wyświetlona strona przegląd. Kliknij przycisk **Utwórz** . 

![Urządzenie IoT](./media/tutorial-define-iot-device-type-pnp/gateway-downstream-review.png)

Utworzono nowy szablon urządzenia. 

![Urządzenie IoT](./media/tutorial-define-iot-device-type-pnp/occupancy-sensor.png)

Utworzysz szablon urządzenia dla czujnika S1. 

Kliknij pozycję Szablony urządzeń na lewym pasku nawigacyjnym, kliknij pozycję **+ Nowy**, wybierz kafelek **urządzenia IoT** i wybierz kafelek Czujnik zajętości, a następnie kliknij przycisk **Dalej: Dostosuj.**

![Urządzenie IoT](./media/tutorial-define-iot-device-type-pnp/s1-sensor.png)

Zostanie wyświetlona strona przegląd. Kliknij przycisk **Utwórz** . 

![Urządzenie podrzędne](./media/tutorial-define-iot-device-type-pnp/s1-review.png)

Utworzono nowy szablon urządzenia. 

![Urządzenie podrzędne](./media/tutorial-define-iot-device-type-pnp/s1-template.png)

## <a name="create-an-iot-gateway-device-template"></a>Tworzenie szablonu urządzenia bramy IoT

Można utworzyć szablon urządzenia bramy IoT. Urządzenie bramy będzie mieć relacje z urządzeniami podrzędnymi, które łączą się w IoT Central za pomocą urządzenia bramy. 

### <a name="downstream-device-relationships-with-gateway-device"></a>Relacje urządzeń podrzędnych z urządzeniem bramy

Urządzenia IoT mogą łączyć się z urządzeniem bramy usługi Azure IoT 

![Strona aplikacji centralnej](./media/tutorial-define-iot-device-type-pnp/gatewaypattern.png)

Jako Konstruktor można tworzyć i edytować szablony urządzeń bramy usługi Azure IoT w swojej aplikacji. Po opublikowaniu szablonu urządzenia można nawiązać połączenie z rzeczywistymi urządzeniami, które implementują szablon urządzenia.

### <a name="select-device-template-type"></a>Wybierz typ szablonu urządzenia 

Aby dodać nowy szablon urządzenia do aplikacji, przejdź do strony **Szablony urządzeń** . Aby to zrobić, wybierz kartę **Szablony urządzeń** w okienku po lewej stronie.

![Strona aplikacji centralnej](./media/tutorial-define-iot-device-type-pnp/devicetemplate.png)

Kliknij pozycję **+ Nowy** , aby rozpocząć tworzenie nowego szablonu urządzenia.

![Szablony urządzeń — nowe](./media/tutorial-define-iot-device-type-pnp/devicetemplatenew.png)

![Wybór szablonów urządzeń — Brama](./media/tutorial-define-iot-device-type-pnp/gateway-review.png)

Zobaczysz na stronie Wybór typu szablonu urządzenia. Wybierz pozycję **Azure IoT** kafelk i kliknij przycisk **Dalej: Dostosuj** przycisk u dołu

Zaznacz pole wyboru bramy i kliknij przycisk **Utwórz** . 

![Wybór szablonów urządzeń — Brama](./media/tutorial-define-iot-device-type-pnp/gateway-customize.png)

Zostanie wyświetlona strona przeglądu, a następnie kliknij pozycję **Utwórz** . 

![Szablon urządzenia — brama](./media/tutorial-define-iot-device-type-pnp/gateway-review.png)

Wprowadź nazwę szablonu bramy **inteligentny szablon bramy**. Kliknij pozycję kafelek **niestandardowy** .

Dodaj standardowe **Informacje o urządzeniu**interfejsu.

### <a name="add-relationships"></a>Dodaj relacje

Możesz dodać relacje podrzędne do modeli możliwości urządzeń dla urządzeń, które będą się łączyć z urządzeniem bramy.

Utwórz relacje z modelami możliwości urządzeń podrzędnych. Kliknij pozycję **Zapisz**

![Szablon urządzenia — brama](./media/tutorial-define-iot-device-type-pnp/gateway-occupancy-s1-rel.png)

### <a name="add-cloud-properties"></a>Dodawanie właściwości chmury

Szablon urządzenia może zawierać właściwości chmury. Właściwości chmury istnieją tylko w aplikacji IoT Central i nigdy nie są wysyłane do ani odbierane z urządzenia.

1. Wybierz pozycję **właściwości chmury** , a następnie **+ Dodaj właściwość chmury**. Skorzystaj z informacji podanych w poniższej tabeli, aby dodać właściwość chmury do szablonu urządzenia.

    | Nazwa wyświetlana      | Typ semantyczny | Schemat |
    | ----------------- | ------------- | ------ |
    | Data ostatniego serwisowania | Brak          | Data   |
    | Nazwa klienta     | Brak          | Ciąg |

2. Wybierz pozycję **Zapisz** , aby zapisać zmiany:

### <a name="add-customizations"></a>Dodawanie dostosowań

Użyj dostosowań, gdy musisz zmodyfikować interfejs lub dodać funkcje specyficzne dla IoT Central do możliwości, która nie wymaga użycia modelu możliwości urządzenia. Można dostosować pola, gdy model możliwości jest w stanie roboczym lub opublikowanym. Można dostosować tylko pola, które nie łamią zgodności interfejsów. Możesz na przykład:

- Dostosuj nazwę wyświetlaną i jednostki możliwości.
- Dodaj domyślny kolor, który ma być używany, gdy wartość pojawia się na wykresie.
- Określ początkową, minimalną i maksymalną wartość właściwości.

Nie można dostosować nazwy możliwości ani typu możliwości. Kliknij pozycję **Zapisz**

### <a name="create-views"></a>Tworzenie widoków

Jako Konstruktor można dostosować aplikację tak, aby wyświetlała odpowiednie informacje o urządzeniu czujnika środowiska z operatorem. Twoje dostosowania umożliwiają operatorowi zarządzanie urządzeniami czujnika środowiskowego podłączonymi do aplikacji. Można utworzyć dwa typy widoków dla operatora, który ma być używany do współpracy z urządzeniami:

* Formularze do wyświetlania i edytowania właściwości urządzenia i chmury.
* Pulpity nawigacyjne do wizualizacji urządzeń.

### <a name="generate-default-views"></a>Generuj widoki domyślne

W tym samouczku kliknij pozycję Generuj widoki domyślne. Przegląd & na temat pulpitów nawigacyjnych są generowane. 

## <a name="publish-device-template"></a>Publikowanie szablonu urządzenia

Aby można było utworzyć symulowany czujnik środowiska lub połączyć prawdziwy czujnik środowiska, należy opublikować szablon urządzenia.

Aby opublikować szablon urządzenia:

1. Przejdź do szablonu urządzenia ze strony **Szablony urządzeń** .

2. Wybierz pozycję **Publikuj**.

3. W oknie dialogowym **Publikowanie szablonu urządzenia** wybierz pozycję **Publikuj**:

Po opublikowaniu szablonu urządzenia jest on widoczny na stronie **urządzenia** i do operatora. W opublikowanym szablonie urządzenia nie można edytować modelu możliwości urządzenia bez tworzenia nowej wersji. Można jednak wykonywać aktualizacje właściwości, dostosowań i widoków w chmurze w opublikowanym szablonie urządzenia bez obsługi wersji. Po wprowadzeniu zmian wybierz pozycję **Publikuj** , aby wypchnąć te zmiany do operatora.

## <a name="create-gateway-simulated-device"></a>Tworzenie symulowanego urządzenia bramy

W Eksploratorze urządzeń Utwórz symulowaną inteligentną bramę do kompilowania. 

![Szablon urządzenia — brama](./media/tutorial-define-iot-device-type-pnp/smartbuildingdevice.png)

## <a name="create-downstream-simulated-devices"></a>Tworzenie symulowanych urządzeń podrzędnych

Z poziomu Eksploratora urządzeń Utwórz czujnik zajętości. 

![Szablon urządzenia — zajętość](./media/tutorial-define-iot-device-type-pnp/occupancydevice.png)

Z poziomu Eksploratora urządzeń Utwórz symulowany czujnik S1. 

![Szablon urządzenia — S1](./media/tutorial-define-iot-device-type-pnp/s1device.png)

## <a name="add-downstream-devices-relationships-to-gateway-device"></a>Dodaj relacje z urządzeniami podrzędnymi do urządzenia bramy

Wybierz czujnik S1 i czujnik zajętości, a następnie kliknij pozycję **Połącz z bramą**. 

![Szablon urządzenia — S1](./media/tutorial-define-iot-device-type-pnp/connecttogateway.png)

Wybierz pozycję Szablon urządzenia bramy, wystąpienie urządzenia bramy, a następnie kliknij pozycję **Dołącz**.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

* Tworzenie nowej bramy IoT jako szablonu urządzenia
* Utwórz właściwości chmury.
* Utwórz dostosowania.
* Zdefiniuj wizualizację dla danych telemetrycznych urządzenia.
* Dodaj relacje
* Opublikuj szablon urządzenia.

Oto sugerowany następny krok:

> [!div class="nextstepaction"]
> [Dodaj urządzenie brzegowe](tutorial-add-edge-as-leaf-device.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
