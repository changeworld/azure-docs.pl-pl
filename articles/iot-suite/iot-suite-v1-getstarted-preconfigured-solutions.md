---
title: Wprowadzenie do wstępnie skonfigurowanych rozwiązań | Microsoft Docs
description: Wykonaj czynności opisane w tym samouczku, aby dowiedzieć się, jak wdrożyć wstępnie skonfigurowane rozwiązanie pakiet Azure IoT Suite.
services: ''
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 6ab38d1a-b564-469e-8a87-e597aa51d0f7
ms.service: iot-suite
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 12eb858f0be4220419300019497070a081e58249
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38723021"
---
# <a name="get-started-with-the-preconfigured-solutions"></a>Wprowadzenie do wstępnie skonfigurowanych rozwiązań

[Wstępnie skonfigurowane rozwiązania][lnk-preconfigured-solutions] pakietu Azure IoT Suite obejmują wiele usług Azure IoT, co pozwala dostarczać kompleksowe rozwiązania, które umożliwiają implementowanie typowych scenariuszy biznesowych IoT. Wstępnie skonfigurowane rozwiązanie do *monitorowania zdalnego* łączy i monitoruje urządzenia. Rozwiązanie to umożliwia analizowanie strumienia danych z urządzeń oraz poprawianie wyników biznesowych dzięki zautomatyzowaniu odpowiedzi procesów na ten strumień danych.

W tym samouczku przedstawiono sposób aprowizowania wstępnie skonfigurowanego rozwiązania do monitorowania zdalnego. Dostępny jest również opis podstawowych funkcji wstępnie skonfigurowanego rozwiązania. Dostęp do wielu z tych funkcji można uzyskać z *pulpitu nawigacyjnego* rozwiązania, który jest wdrażany jako część wstępnie skonfigurowanego rozwiązania:

![Pulpit nawigacyjny wstępnie skonfigurowanego rozwiązania do monitorowania zdalnego][img-dashboard]

Do wykonania kroków tego samouczka jest potrzebna aktywna subskrypcja platformy Azure.

> [!NOTE]
> Jeśli jej nie masz, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz artykuł [Bezpłatna wersja próbna platformy Azure][lnk_free_trial].

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

## <a name="scenario-overview"></a>Omówienie scenariusza

Podczas wdrażania wstępnie skonfigurowanego rozwiązania do monitorowania zdalnego jest ono wstępnie wypełniane zasobami, które umożliwiają wykonanie kroków opisanych w typowym scenariuszu zdalnego monitorowania. W tym scenariuszu kilka urządzeń podłączonych do rozwiązania zgłasza nieoczekiwane wartości temperatury. W poniższych sekcjach opisano sposób wykonania następujących czynności:

* Określenie urządzeń wysyłających nieoczekiwane wartości temperatury.
* Skonfigurowanie tych urządzeń do wysłania bardziej szczegółowych danych telemetrycznych.
* Rozwiązanie problemu przez aktualizację oprogramowania układowego na tych urządzeniach.
* Sprawdzenie, czy podjęte akcje spowodowały rozwiązanie problemu.

Kluczową cechą tego scenariusza jest to, że te wszystkie akcje można wykonać zdalnie z poziomu pulpitu nawigacyjnego rozwiązania. Nie jest konieczny fizyczny dostęp do urządzeń.

## <a name="view-the-solution-dashboard"></a>Wyświetlanie pulpitu nawigacyjnego

Pulpit nawigacyjny pozwala zarządzać wdrożonym rozwiązaniem. Można na przykład wyświetlać dane telemetryczne, dodawać urządzenia i konfigurować reguły.

1. Jeśli aprowizacja została ukończona, a na kafelku wstępnie skonfigurowanego rozwiązania jest wyświetlany stan **Gotowe**, wybierz pozycję **Uruchom**, aby otworzyć portal rozwiązania do monitorowania zdalnego na nowej karcie.

    ![Uruchamianie wstępnie skonfigurowanego rozwiązania][img-launch-solution]

1. Domyślnie w portalu rozwiązania jest wyświetlany *pulpit nawigacyjny*. Korzystając z menu po lewej stronie, można przejść do innych obszarów portalu rozwiązania.

    ![Pulpit nawigacyjny wstępnie skonfigurowanego rozwiązania do monitorowania zdalnego][img-menu]

Pulpit nawigacyjny udostępnia następujące informacje:

* Mapę zawierającą lokalizację każdego urządzenia połączonego z rozwiązaniem. Przy pierwszym uruchomieniu rozwiązania dostępnych jest 25 symulowanych urządzeń. Są one implementowane jakoAzure WebJobs, a wykreślanie informacji na mapie odbywa się za pomocą interfejsu API Map Bing. Zobacz [Często zadawane pytania][lnk-faq], aby dowiedzieć się, jak utworzyć dynamiczną mapę.
* Panel **Historia telemetrii**, na którym są wyświetlane niemal w czasie rzeczywistym dane telemetryczne dotyczące wilgotności i temperatury pochodzące z wybranego urządzenia oraz dane zagregowane, takie jak wilgotność maksymalna, minimalna i średnia.
* Panel **Historia alarmów**, na którym są wyświetlane ostatnie zdarzenia alarmów generowane w przypadku przekroczenia progu przez wartość telemetryczną. Wstępnie skonfigurowane rozwiązanie zawiera przykładowe alarmy, ale można też definiować własne.
* Panel **Zadania**, na którym są wyświetlane informacje o zaplanowanych zadaniach. Własne zadania można zaplanować na stronie **Zadania zarządzania**.

## <a name="view-alarms"></a>Wyświetlanie alarmów

Na panelu Historia alarmów widać, że pięć urządzeń zgłasza wyższe niż oczekiwano wartości telemetryczne.

![DO ZROBIENIA Historia alarmów na pulpicie nawigacyjnym rozwiązania][img-alarms]

> [!NOTE]
> Te alarmy są generowane przez regułę dołączoną do wstępnie skonfigurowanego rozwiązania. Ta reguła generuje alert, gdy wartość temperatury wysyłana przez urządzenie przekracza 60. Można zdefiniować własne reguły i akcje, wybierając pozycje [Reguły](#add-a-rule) i [Akcje](#add-an-action) w menu po lewej stronie.

## <a name="view-devices"></a>Wyświetlanie urządzeń

*Lista urządzeń* zawiera wszystkie zarejestrowane urządzenia należące do rozwiązania. Korzystając z listy urządzeń, można wyświetlać i edytować metadane urządzeń, dodawać lub usuwać urządzenia i wywoływać metody na urządzeniach. Urządzenia znajdujące się na liście można filtrować i sortować. Na liście urządzeń można również dostosować kolumny.

1. Wybierz pozycję **Urządzenia**, aby wyświetlić listę urządzeń dla tego rozwiązania.

   ![Widok listy urządzeń w portalu rozwiązania][img-devicelist]

1. Na liście początkowo jest widocznych 25 symulowanych urządzeń utworzonych w procesie aprowizacji. Do rozwiązania można dodać dodatkowe symulowane i fizyczne urządzenia.

1. Aby wyświetlić szczegóły dotyczące urządzenia, wybierz odpowiednią pozycję z listy urządzeń.

   ![Widok szczegółów dotyczących urządzenia w portalu rozwiązania][img-devicedetails]

Panel **Szczegóły urządzenia** zawiera sześć sekcji:

* Kolekcja linków umożliwiająca dostosowanie ikony urządzenia, wyłączenie urządzenia, dodanie reguły, wywołanie metody lub wysłanie polecenia. Porównanie poleceń (komunikatów wysyłanych z urządzenia do chmury) i metod (metod bezpośrednich) znajduje się w temacie [Wskazówki dotyczące komunikacji z chmury do urządzenia][lnk-c2d-guidance].
* Sekcja **Bliźniacza reprezentacja urządzenia — Tagi** umożliwia edytowanie wartości tagów dla urządzenia. Wartości tagów można wyświetlać na liście urządzeń i używać ich do filtrowania tej listy.
* Sekcja **Bliźniacza reprezentacja urządzenia —Żądane właściwości** umożliwia ustawianie wartości właściwości wysyłanych do urządzenia.
* Sekcja **Bliźniacza reprezentacja urządzenia — Zgłaszane właściwości** umożliwia wyświetlanie wartości właściwości wysyłanych z urządzenia.
* Sekcja **Właściwości urządzenia** zawiera informacje z rejestru tożsamości, takie jak identyfikator urządzenia i klucze uwierzytelniania.
* Sekcja **Ostatnie zadania** zawiera informacje o wszystkich zadaniach, które były ukierunkowane na to urządzenie.

## <a name="filter-the-device-list"></a>Filtrowanie listy urządzeń

Aby wyświetlić tylko te urządzenia, które wysyłają nieoczekiwane wartości temperatury, można użyć filtru. Wstępnie skonfigurowane rozwiązania do zdalnego monitorowania zawiera filtr **Urządzenia w niedobrej kondycji**, który umożliwia wyświetlenie urządzeń mających wartość średniej temperatury większą niż 60. Możesz również [utworzyć własne filtry](#add-a-filter).

1. Wybierz pozycję **Otwórz zapisany filtr**, aby wyświetlić listę dostępnych filtrów. Następnie wybierz pozycję **Urządzenia w niedobrej kondycji**, aby zastosować filtr:

    ![Wyświetlanie listy filtrów][img-unhealthy-filter]

1. Na liście urządzeń są teraz pokazywane tylko urządzenia z wartością średniej temperatury większą niż 60.

    ![Widok filtrowanej listy urządzeń przedstawiający urządzenia w niedobrej kondycji][img-filtered-unhealthy-list]

## <a name="update-desired-properties"></a>Aktualizowanie żądanych właściwości

Na tym etapie zidentyfikowano zestaw urządzeń, które mogą wymagać naprawy. Wygląda jednak na to, że 15-sekundowa częstotliwość próbkowania danych jest niewystarczająca, aby jednoznacznie określić przyczynę problemu. Częstotliwość próbkowania danych telemetrycznych zostanie więc zmieniona na pięć sekund, co zapewni więcej punktów danych w celu lepszego zdiagnozowania problemu. Tę zmianę konfiguracji można wypchnąć do urządzeń zdalnych za pośrednictwem portalu rozwiązania. Można wprowadzić jednorazową zmianę, ocenić jej skutki, a następnie podjąć odpowiednie działania zgodnie z otrzymanymi wynikami.

Wykonaj następujące kroki, aby uruchomić zadanie zmieniające żądaną właściwość **TelemetryInterval** dla uwzględnionych urządzeń. Po otrzymaniu przez urządzenia nowej wartości właściwości **TelemetryInterval** zmieniają one swoją konfigurację i zaczynają wysyłać dane telemetryczne co pięć sekund zamiast co 15 sekund:

1. Podczas wyświetlania na liście urządzeń w niedobrej kondycji wybierz pozycję **Harmonogram zadań**, a następnie pozycję **Edytuj bliźniaczą reprezentację urządzenia**.

1. Wywołaj zadanie **Zmiana interwału telemetrii**.

1. Zmień wartość **żądanej właściwości** o nazwie **desired.Config.TelemetryInterval** na pięć sekund.

1. Wybierz pozycję **Zaplanuj**.

    ![Zmiana wartości właściwości TelemetryInterval na pięć sekund][img-change-interval]

1. Postęp zadania można monitorować na stronie **Zadania zarządzania** w portalu.

> [!NOTE]
> Jeśli chcesz zmienić wartość żądanej właściwości dla konkretnego urządzenia, użyj sekcji **Żądane właściwości** na panelu **Szczegóły urządzenia**, zamiast uruchamiać zadanie.

To zadanie ustawia wartość żądanej właściwości **TelemetryInterval** w bliźniaczej reprezentacji urządzenia dla wszystkich urządzeń wybranych za pomocą filtru. Urządzenia pobierają tę wartość z bliźniaczej reprezentacji urządzenia i aktualizują swoje zachowanie. Kiedy urządzenie pobiera i przetwarza żądaną właściwość z bliźniaczej reprezentacji urządzenia, ustawia odpowiednią wartość zgłoszonej właściwości.

## <a name="invoke-methods"></a>Wywoływanie metod

Podczas wykonywania zadania na liście urządzeń w niedobrej kondycji można zauważyć, że wszystkie te urządzenia mają starą (wcześniejszą niż 1.6) wersję oprogramowania układowego.

![Wyświetlanie zgłaszanej wersji oprogramowania układowego urządzeń w niedobrej kondycji][img-old-firmware]

Ta wersja oprogramowania układowego może być podstawową przyczyną występowania nieoczekiwanych wartości temperatury, ponieważ wiadomo, że inne urządzenia w dobrej kondycji zostały ostatnio zaktualizowane do wersji 2.0. W celu zidentyfikowania wszystkich urządzeń ze starymi wersjami oprogramowania układowego można użyć wbudowanego filtru **Urządzenia ze starym oprogramowaniem układowym**. Z poziomu portalu można następnie zdalnie zaktualizować wszystkie urządzenia działające nadal pod kontrolą starych wersji oprogramowania układowego:

1. Wybierz pozycję **Otwórz zapisany filtr**, aby wyświetlić listę dostępnych filtrów. Następnie wybierz pozycję **Urządzenia ze starym oprogramowaniem układowym**, aby zastosować filtr:

    ![Wyświetlanie listy filtrów][img-old-filter]

1. Na liście urządzeń są teraz wyświetlane tylko urządzenia ze starymi wersjami oprogramowania układowego. Na liście znajduje się pięć urządzeń zidentyfikowanych przez filtr **Urządzenia w niedobrej kondycji** i trzy dodatkowe urządzenia:

    ![Widok filtrowanej listy urządzeń przedstawiający stare urządzenia][img-filtered-old-list]

1. Wybierz pozycję **Harmonogram zadań**, a następnie pozycję **Wywołaj metodę**.

1. W polu **Nazwa zadania** wpisz **Aktualizacja oprogramowania układowego do wersji 2.0**.

1. W polu **Metoda** wybierz pozycję **InitiateFirmwareUpdate**.

1. Ustaw parametr **FwPackageUri** na wartość **https://iotrmassets.blob.core.windows.net/firmwares/FW20.bin**.

1. Wybierz pozycję **Zaplanuj**. Domyślnie zadanie zostanie uruchomione natychmiast.

    ![Tworzenie zadania aktualizacji oprogramowania układowego wybranych urządzeń][img-method-update]

> [!NOTE]
> Aby wywołać metodę dla konkretnego urządzenia, należy wybrać pozycję **Metody** na panelu **Szczegóły urządzenia**, zamiast uruchamiać zadanie.

To zadanie wywołuje metodę bezpośrednią **InitiateFirmwareUpdate** na wszystkich urządzeniach wybranych za pomocą filtru. Urządzenia odpowiadają natychmiast do usługi IoT Hub, a następnie asynchronicznie inicjują proces aktualizacji oprogramowania układowego. Urządzenia dostarczają informacje o stanie procesu aktualizacji oprogramowania układowego za pomocą wartości zgłaszanych właściwości, jak pokazano w poniższym zrzucie ekranu. Wybierz ikonę **Odśwież**, aby zaktualizować informacje na liście urządzeń i zadań:

![Lista zadań z widoczną listą aktualizacji oprogramowania układowego w stanie uruchomionym][img-update-1]
![Lista urządzeń przedstawiająca stan aktualizacji oprogramowania układowego][img-update-2]
![Lista zadań z widoczną listą aktualizacji oprogramowania układowego w stanie ukończonym][img-update-3]

> [!NOTE]
> W środowisku produkcyjnym można zaplanować, aby zadania były uruchamiane w wyznaczonym oknie obsługi.

## <a name="scenario-review"></a>Przegląd scenariusza

W tym scenariuszu za pomocą historii alarmów na pulpicie nawigacyjnym oraz filtru zidentyfikowano potencjalny problem występujący na niektórych urządzeniach zdalnych. Następnie za pomocą filtru oraz zadania skonfigurowano zdalnie urządzenia, aby uzyskać większą ilość informacji ułatwiających zdiagnozowanie problemu. Na koniec użyto filtru i zadania w celu zaplanowania obsługi kwalifikujących się urządzeń. Po powrocie do pulpitu nawigacyjnego można sprawdzić, czy nie nadchodzą już żadne alarmy z urządzeń w rozwiązaniu. Korzystając z filtru, można się upewnić, czy oprogramowanie układowe jest aktualne na wszystkich urządzeniach w rozwiązaniu oraz czy nie ma żadnych urządzeń w niedobrej kondycji:

![Filtr przedstawiający, że wszystkie urządzenia mają aktualne oprogramowanie układowe][img-updated]

![Filtr przedstawiający, że wszystkie urządzenia są w dobrej kondycji][img-healthy]

## <a name="other-features"></a>Pozostałe funkcje

W poniższych sekcjach opisano niektóre dodatkowe funkcje wstępnie skonfigurowanego rozwiązania do monitorowania zdalnego, które nie zostały opisane w poprzednim scenariuszu.

### <a name="customize-columns"></a>Dostosowywanie kolumn

Informacje wyświetlane na liście urządzeń można dostosowywać. W tym celu należy wybrać pozycję **Edytor kolumn**. Istnieje możliwość dodawania i usuwania kolumn, w których są wyświetlane zgłaszane wartości właściwości i tagów. Można również zmieniać kolejność kolumn i ich nazwy:

   ![Przycisk Edytor kolumn na liście urządzeń][img-columneditor]

### <a name="customize-the-device-icon"></a>Dostosowywanie ikony urządzenia

Ikonę urządzenia wyświetlaną na liście urządzeń można dostosować na panelu **Szczegóły urządzenia** w następujący sposób:

1. Wybierz ikonę ołówka, aby otworzyć panel **Edytowanie obrazu** dla urządzenia:

   ![Otwieranie edytora obrazu dla urządzenia][img-startimageedit]

1. Przekaż nowy obraz lub użyj jednego z istniejących obrazów, a następnie wybierz przycisk **Zapisz**:

   ![Edytowanie edytora obrazu dla urządzenia][img-imageedit]

1. Wybrany obraz jest teraz wyświetlany w kolumnie **Ikona** dla urządzenia.

> [!NOTE]
> Obraz jest przechowywany w usłudze Blob Storage. Tag w bliźniaczej reprezentacji urządzenia zawiera link do obrazu w usłudze Blob Storage.

### <a name="add-a-device"></a>Dodawanie urządzenia

Podczas wdrażania wstępnie skonfigurowanego rozwiązania automatycznie aprowizujesz 25 przykładowych urządzeń widocznych na liście urządzeń. Są to *symulowane urządzenia* uruchamiane w zadaniu WebJob Azure. Symulowane urządzenia ułatwiają eksperymentowanie ze wstępnie skonfigurowanym rozwiązaniem bez konieczności wdrażania prawdziwych urządzeń fizycznych. Jeśli chcesz połączyć prawdziwe urządzenie z rozwiązaniem, zobacz samouczek [Łączenie urządzenia ze wstępnie skonfigurowanym rozwiązaniem do monitorowania zdalnego][lnk-connect-rm].

Poniższe kroki pokazują, jak dodać symulowane urządzenie do rozwiązania:

1. Wróć do listy urządzeń.

1. Aby dodać urządzenie, wybierz pozycję **+ Dodaj urządzenie** w lewym dolnym rogu.

   ![Dodawanie urządzenia do wstępnie skonfigurowanego rozwiązania][img-adddevice]

1. Wybierz pozycję **Dodaj nowe** na kafelku **Symulowane urządzenie**.

   ![Określanie szczegółów nowego urządzenia na pulpicie nawigacyjnym][img-addnew]

   Oprócz tworzenia nowych symulowanych urządzeń można również dodawać urządzenia fizyczne — wystarczy wybrać opcję utworzenia **niestandardowego urządzenia**. Aby dowiedzieć się więcej na temat łączenia fizycznych urządzeń z rozwiązaniem, zobacz artykuł [Łączenie urządzenia ze wstępnie skonfigurowanym rozwiązaniem Pakietu IoT służącym do monitorowania zdalnego][lnk-connect-rm].

1. Wybierz pozycję **Pozwól mi zdefiniować własny identyfikator urządzenia** i wprowadź unikatowy identyfikator urządzenia, np. **moje_urzadzenie_01**.

1. Wybierz pozycję **Utwórz**.

   ![Zapisywanie nowego urządzenia][img-definedevice]

1. W kroku 3 procedury **Dodawanie symulowanego urządzenia** wybierz pozycję **Gotowe**, aby powrócić do listy urządzeń.

1. Na liście urządzeń widać, że stan urządzenia ma wartość **Uruchomione**.

    ![Wyświetlanie nowego urządzenia na liście urządzeń][img-runningnew]

1. Na pulpicie nawigacyjnym można również wyświetlić symulowane dane telemetryczne pochodzące z nowego urządzenia:

    ![Wyświetlanie telemetrii z nowego urządzenia][img-runningnew-2]

### <a name="disable-and-delete-a-device"></a>Wyłączanie i usuwanie urządzenia

Można wyłączyć urządzenie, a następnie je usunąć:

![Wyłączanie i usuwanie urządzenia][img-disable]

### <a name="add-a-rule"></a>Dodawanie reguły

Dla nowo dodanego urządzenia nie zostały określone żadne reguły. W tej sekcji dodasz regułę, która powoduje uruchomienie alarmu, gdy temperatura zgłoszona przez nowe urządzenie przekroczy 47 stopni. Przed wykonaniem poniższych instrukcji zwróć uwagę na to, że zgodnie z historią telemetrii dla nowego urządzenia dostępną na pulpicie nawigacyjnym temperatura urządzenie nigdy nie przekracza 45 stopni.

1. Wróć do listy urządzeń.

1. Aby dodać regułę dla urządzenia, wybierz nowe urządzenie na **liście urządzeń**, a następnie wybierz pozycję **Dodaj regułę**.

1. Utwórz regułę, w której jest używane pole danych **Temperatura** i która generuje dane wyjściowe **AlarmTemp**, gdy temperatura przekroczy 47 stopni:

    ![Dodawanie reguły urządzenia][img-adddevicerule]

1. Aby zapisać zmiany, wybierz pozycję **Zapisz i wyświetl reguły**.

1. Wybierz pozycję **Polecenia** w okienku szczegółów nowego urządzenia.

    ![Dodawanie reguły urządzenia][img-adddevicerule2]

1. Wybierz pozycję **ChangeSetPointTemp** z listy poleceń i ustaw parametr **SetPointTemp** na wartość 45. Następnie wybierz pozycję **Wyślij polecenie**:

    ![Dodawanie reguły urządzenia][img-adddevicerule3]

1. Wróć do pulpitu nawigacyjnego. Po pewnym czasie w okienku **Historia alarmów** pojawi się nowy wpis, gdy temperatura urządzenia przekroczy wartość progową równą 47 stopni:

    ![Dodawanie reguły urządzenia][img-adddevicerule4]

1. Na stronie **Reguły** pulpitu nawigacyjnego można przeglądać i edytować wszystkie reguły.

    ![Wyświetlanie listy reguł urządzenia][img-rules]

1. Na stronie **Akcje** pulpitu nawigacyjnego można przeglądać i edytować wszystkie akcje, które mogą być wykonywane w odpowiedzi na działanie reguły:

    ![Wyświetlanie listy akcji urządzenia][img-actions]

> [!NOTE]
> Istnieje możliwość zdefiniowania akcji, które umożliwiają wysyłanie wiadomości e-mail lub SMS w odpowiedzi na działanie reguły albo pozwalają na integrację z systemem biznesowym za pośrednictwem [aplikacji logiki][lnk-logic-apps]. Aby uzyskać więcej informacji, zobacz [Łączenie aplikacji logiki ze wstępnie skonfigurowanym rozwiązaniem do zarządzania zdalnego pakietu Azure IoT Suite][lnk-logicapptutorial].

### <a name="manage-filters"></a>Zarządzanie filtrami

Na liście urządzeń można tworzyć, zapisywać i ponownie ładować filtry, co umożliwia wyświetlenie niestandardowej listy urządzeń podłączonych do centrum. Aby utworzyć filtr:

1. Wybierz ikonę edycji filtru nad listą urządzeń:

    ![Otwieranie edytora filtru][img-editfiltericon]

1. W **Edytorze filtrów** można dodawać pola, operatory i wartości, aby filtrować listę urządzeń. W celu doprecyzowania filtru można dodać wiele klauzul. Wybierz pozycję **Filtruj**, aby zastosować filtr:

    ![Tworzenie filtru][img-filtereditor]

1. W tym przykładzie lista jest filtrowana według producenta i numeru modelu:

    ![Filtrowana lista][img-filterelist]

1. Aby zapisać filtr pod niestandardową nazwą, wybierz ikonę **Zapisz jako**:

    ![Zapisywanie filtru][img-savefilter]

1. Aby ponownie zastosować uprzednio zapisany filtr, wybierz ikonę **Otwórz zapisany filtr**:

    ![Otwieranie filtru][img-openfilter]

Filtry można tworzyć na postawie identyfikatora urządzenia, stanu urządzenia, żądanych właściwości, zgłaszanych właściwości i tagów. Własne niestandardowe tagi można dodać do urządzenia w sekcji **Tagi** na panelu **Szczegóły urządzenia**. Można też uruchomić zadanie aktualizacji tagów na wielu urządzeniach.

> [!NOTE]
> W **Edytorze filtrów** można użyć **Widoku zaawansowanego**, aby móc bezpośrednio edytować tekst zapytania.

### <a name="commands"></a>Polecenia

Korzystając z panelu **Szczegóły urządzenia**, można wysłać polecenia do urządzenia. Gdy urządzenie jest uruchamiane po raz pierwszy, wysyła do rozwiązania informacje o obsługiwanych poleceniach. Aby poznać różnice między poleceniami i metodami, zobacz [Azure IoT Hub cloud-to-device options][lnk-c2d-guidance] (Opcje wysyłania z chmury do urządzenia w usłudze Azure IoT Hub).

1. Wybierz pozycję **Polecenia** na panelu **Szczegóły urządzenia** dla wybranego urządzenia:

   ![Polecenia urządzenia na pulpicie nawigacyjnym][img-devicecommands]

1. Wybierz pozycję **PingDevice** na liście poleceń.

1. Wybierz pozycję **Wyślij polecenie**.

1. Stan polecenia można zobaczyć w historii poleceń.

   ![Stan polecenia na pulpicie nawigacyjnym][img-pingcommand]

Rozwiązanie umożliwia śledzenie stanu wszystkich wysyłanych poleceń. Na początku wynik ma wartość **Oczekiwanie**. Gdy urządzenie zgłosi wykonanie polecenia, wynik jest ustawiany na **Powodzenie**.

## <a name="behind-the-scenes"></a>Za kulisami

Podczas wdrażania wstępnie skonfigurowanego rozwiązania jest tworzonych wiele zasobów w ramach wybranej subskrypcji platformy Azure. Można je wyświetlić w witrynie Azure [Portal][lnk-portal]. W procesie wdrażania jest tworzona **grupa zasobów** o nazwie odpowiadającej nazwie wstępnie skonfigurowanego rozwiązania:

![Wstępnie skonfigurowane rozwiązanie w portalu Azure][img-portal]

Aby wyświetlić ustawienia danego zasobu, wybierz go z listy w grupie zasobów.

Można również wyświetlić kod źródłowy wstępnie skonfigurowanego rozwiązania. Kod źródłowy wstępnie skonfigurowanego rozwiązania do monitorowania zdalnego znajduje się w repozytorium GitHub [azure-iot-remote-monitoring][lnk-rmgithub]:

* Folder **DeviceAdministration** zawiera kod źródłowy pulpitu nawigacyjnego.
* Folder **Simulator** zawiera kod źródłowy symulowanego urządzenia.
* Folder **EventProcessor** zawiera kod źródłowy procesu zaplecza, który obsługuje przychodzące dane telemetryczne.

Gdy wszystko będzie gotowe, możesz usunąć wstępnie skonfigurowane rozwiązanie z subskrypcji platformy Azure w witrynie [azureiotsuite.com][lnk-azureiotsuite]. Ta witryna umożliwia łatwe usunięcie wszystkich zasobów, które zostały aprowizowane po utworzeniu wstępnie skonfigurowanego rozwiązania.

> [!NOTE]
> Aby mieć pewność, że zostaną usunięte wszystkie elementy powiązane ze wstępnie skonfigurowanym rozwiązaniem, nie usuwaj grupy zasobów w portalu, ale usuń rozwiązanie w witrynie [azureiotsuite.com][lnk-azureiotsuite].

## <a name="next-steps"></a>Następne kroki

Teraz, kiedy zostało wdrożone wstępnie skonfigurowane rozwiązanie, które działa, możesz kontynuować poznawanie Pakietu IoT, czytając następujące artykuły:

* [Przewodnik po wstępnie skonfigurowanym rozwiązaniu monitorowania zdalnego][lnk-rm-walkthrough]
* [Łączenie urządzenia ze wstępnie skonfigurowanym rozwiązaniem do monitorowania zdalnego][lnk-connect-rm]
* [Uprawnienia w witrynie azureiotsuite.com][lnk-permissions]

[img-launch-solution]: media/iot-suite-v1-getstarted-preconfigured-solutions/launch.png
[img-dashboard]: media/iot-suite-v1-getstarted-preconfigured-solutions/dashboard.png
[img-menu]: media/iot-suite-v1-getstarted-preconfigured-solutions/menu.png
[img-devicelist]: media/iot-suite-v1-getstarted-preconfigured-solutions/devicelist.png
[img-alarms]: media/iot-suite-v1-getstarted-preconfigured-solutions/alarms.png
[img-devicedetails]: media/iot-suite-v1-getstarted-preconfigured-solutions/devicedetails.png
[img-devicecommands]: media/iot-suite-v1-getstarted-preconfigured-solutions/devicecommands.png
[img-pingcommand]: media/iot-suite-v1-getstarted-preconfigured-solutions/pingcommand.png
[img-adddevice]: media/iot-suite-v1-getstarted-preconfigured-solutions/adddevice.png
[img-addnew]: media/iot-suite-v1-getstarted-preconfigured-solutions/addnew.png
[img-definedevice]: media/iot-suite-v1-getstarted-preconfigured-solutions/definedevice.png
[img-runningnew]: media/iot-suite-v1-getstarted-preconfigured-solutions/runningnew.png
[img-runningnew-2]: media/iot-suite-v1-getstarted-preconfigured-solutions/runningnew2.png
[img-rules]: media/iot-suite-v1-getstarted-preconfigured-solutions/rules.png
[img-adddevicerule]: media/iot-suite-v1-getstarted-preconfigured-solutions/addrule.png
[img-adddevicerule2]: media/iot-suite-v1-getstarted-preconfigured-solutions/addrule2.png
[img-adddevicerule3]: media/iot-suite-v1-getstarted-preconfigured-solutions/addrule3.png
[img-adddevicerule4]: media/iot-suite-v1-getstarted-preconfigured-solutions/addrule4.png
[img-actions]: media/iot-suite-v1-getstarted-preconfigured-solutions/actions.png
[img-portal]: media/iot-suite-v1-getstarted-preconfigured-solutions/portal.png
[img-disable]: media/iot-suite-v1-getstarted-preconfigured-solutions/solutionportal_08.png
[img-columneditor]: media/iot-suite-v1-getstarted-preconfigured-solutions/columneditor.png
[img-startimageedit]: media/iot-suite-v1-getstarted-preconfigured-solutions/imagedit1.png
[img-imageedit]: media/iot-suite-v1-getstarted-preconfigured-solutions/imagedit2.png
[img-editfiltericon]: media/iot-suite-v1-getstarted-preconfigured-solutions/editfiltericon.png
[img-filtereditor]: media/iot-suite-v1-getstarted-preconfigured-solutions/filtereditor.png
[img-filterelist]: media/iot-suite-v1-getstarted-preconfigured-solutions/filteredlist.png
[img-savefilter]: media/iot-suite-v1-getstarted-preconfigured-solutions/savefilter.png
[img-openfilter]:  media/iot-suite-v1-getstarted-preconfigured-solutions/openfilter.png
[img-unhealthy-filter]: media/iot-suite-v1-getstarted-preconfigured-solutions/unhealthyfilter.png
[img-filtered-unhealthy-list]: media/iot-suite-v1-getstarted-preconfigured-solutions/unhealthylist.png
[img-change-interval]: media/iot-suite-v1-getstarted-preconfigured-solutions/changeinterval.png
[img-old-firmware]: media/iot-suite-v1-getstarted-preconfigured-solutions/noticeold.png
[img-old-filter]: media/iot-suite-v1-getstarted-preconfigured-solutions/oldfilter.png
[img-filtered-old-list]: media/iot-suite-v1-getstarted-preconfigured-solutions/oldlist.png
[img-method-update]: media/iot-suite-v1-getstarted-preconfigured-solutions/methodupdate.png
[img-update-1]: media/iot-suite-v1-getstarted-preconfigured-solutions/jobupdate1.png
[img-update-2]: media/iot-suite-v1-getstarted-preconfigured-solutions/jobupdate2.png
[img-update-3]: media/iot-suite-v1-getstarted-preconfigured-solutions/jobupdate3.png
[img-updated]: media/iot-suite-v1-getstarted-preconfigured-solutions/updated.png
[img-healthy]: media/iot-suite-v1-getstarted-preconfigured-solutions/healthy.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-v1-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-logic-apps]: https://azure.microsoft.com/documentation/services/app-service/logic/
[lnk-portal]: http://portal.azure.com/
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-logicapptutorial]: iot-suite-v1-logic-apps-tutorial.md
[lnk-rm-walkthrough]: iot-suite-v1-remote-monitoring-sample-walkthrough.md
[lnk-connect-rm]: iot-suite-v1-connecting-devices.md
[lnk-permissions]: iot-suite-v1-permissions.md
[lnk-c2d-guidance]: ../iot-hub/iot-hub-devguide-c2d-guidance.md
[lnk-faq]: iot-suite-v1-faq.md