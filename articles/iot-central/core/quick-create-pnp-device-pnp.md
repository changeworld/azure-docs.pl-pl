---
title: Dodawanie symulowanego urządzenia do usługi Azure IoT Central | Microsoft Docs
description: Utwórz szablon urządzenia i Dodaj symulowane urządzenie do aplikacji IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 10/24/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 29d40556cb2335abf894c7fc6c0df7a6b7fbde7e
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72958147"
---
# <a name="quickstart-add-a-simulated-device-to-your-iot-central-application-preview-features"></a>Szybki Start: Dodawanie symulowanego urządzenia do aplikacji IoT Central (funkcje w wersji zapoznawczej)

*Ten artykuł dotyczy operatorów, konstruktorów i administratorów.*

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Szablon urządzenia definiuje możliwości urządzenia, które nawiązują połączenie z aplikacją IoT Central. Możliwości obejmują dane telemetryczne wysyłane przez urządzenie, właściwości urządzenia i polecenia, na które urządzenie reaguje. Z szablonu urządzenia Konstruktor lub operator mogą dodawać do aplikacji zarówno rzeczywiste, jak i symulowane urządzenia. Symulowane urządzenia są przydatne do testowania zachowania aplikacji IoT Central przed połączeniem rzeczywistych urządzeń.

W tym przewodniku szybki start utworzysz szablon urządzenia **czujnika środowiska** i dodasz symulowane urządzenie. Urządzenie czujnika środowiska:

* Wysyła dane telemetryczne, takie jak temperatura.
* Raportuje właściwości specyficzne dla urządzenia, takie jak poziom jasności.
* Reaguje na polecenia, takie jak Włączanie i wyłączanie.
* Raporty ogólne właściwości urządzenia, takie jak wersja oprogramowania układowego i numer seryjny.

## <a name="prerequisites"></a>Wymagania wstępne

Ukończ Przewodnik Szybki Start dotyczący [tworzenia aplikacji IoT Central platformy Azure (funkcje w wersji zapoznawczej)](./quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) , aby utworzyć aplikację IoT Central przy użyciu szablonu **aplikacji niestandardowy > aplikacji w wersji zapoznawczej** .

Potrzebna jest również lokalna kopia pliku **EnvironmentalSensorInline. capabilitymodel. JSON** , który zawiera model możliwości urządzenia [IoT Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) . Możesz pobrać go z tego [miejsca](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json). Kliknij prawym przyciskiem myszy na stronie i wybierz polecenie **Zapisz jako**.

Po pobraniu pliku Otwórz go w edytorze tekstów i Zastąp dwa wystąpienia `<YOUR_COMPANY_NAME_HERE>` nazwą. Używaj tylko znaków a-z, A-Z, 0-9 i znaku podkreślenia.

## <a name="create-a-template"></a>Tworzenie szablonu

Jako Konstruktor można tworzyć i edytować szablony urządzeń w aplikacji. Po opublikowaniu szablonu urządzenia można wygenerować symulowane urządzenie lub połączyć rzeczywiste urządzenia, które implementują szablon urządzenia. Symulowane urządzenia umożliwiają testowanie zachowania aplikacji przed połączeniem rzeczywistego urządzenia.

Aby dodać nowy szablon urządzenia do aplikacji, przejdź do strony **Szablony urządzeń** . W tym celu wybierz kartę **Szablony urządzeń** w lewym okienku.

![Strona Szablony urządzeń](./media/quick-create-pnp-device-pnp/devicedefinitions.png)

### <a name="add-a-device-capability-model"></a>Dodawanie modelu możliwości urządzenia

Istnieje kilka opcji tworzenia modelu możliwości urządzeń w IoT Central. Można utworzyć niestandardowy model od podstaw, zaimportować z pliku, wybrać pozycję z katalogu urządzeń lub połączyć urządzenie Plug and Play IoT za pomocą połączenia pierwszego urządzenia, gdzie model możliwości urządzenia został opublikowany w repozytorium publicznym. W tym samouczku zaimportujesz model możliwości urządzenia z pliku.

Poniższe kroki pokazują, jak zaimportować model możliwości urządzenia **czujnika środowiska** . Te urządzenia wysyłają dane telemetryczne, takie jak temperatura, do aplikacji:

1. Aby dodać nowy szablon urządzenia, wybierz pozycję **+** na stronie **Szablony urządzeń** .

1. Wybierz pozycję **urządzenie IoT** z listy niestandardowych szablonów urządzeń, wybierz pozycję **Dalej: Dostosuj**, a następnie wybierz kolejno pozycje **Dalej: przegląd**, a następnie wybierz pozycję **Utwórz**.

1. Wprowadź **czujnik środowiska** jako nazwę szablonu urządzenia.

1. Wybierz pozycję **Importuj model możliwości** , aby utworzyć nowy model możliwości urządzenia z pliku JSON. Przejdź do folderu, w którym zapisano plik **EnvironmentalSensorInline. capabilitymodel. JSON** na komputerze lokalnym. Wybierz plik **EnvironmentalSensorInline. capabilitymodel. JSON** , a następnie wybierz pozycję **Otwórz**. Model możliwości czujnika środowiska zawiera informacje o **czujniku** i interfejsie **informacji o urządzeniu** :

    ![Model możliwości urządzenia czujnika środowiska](./media/quick-create-pnp-device-pnp/newdevicecapabilitymodel.png)

    Te interfejsy definiują możliwości urządzenia **czujnika środowiska** . Możliwości obejmują dane telemetryczne wysyłane przez urządzenie, właściwości urządzenia i polecenia, na które urządzenie reaguje.

### <a name="add-cloud-properties"></a>Dodawanie właściwości chmury

Szablon urządzenia może zawierać właściwości chmury. Właściwości chmury istnieją tylko w aplikacji IoT Central i nigdy nie są wysyłane do ani odbierane z urządzenia.

1. Wybierz pozycję **właściwości chmury** , a następnie **+ Dodaj właściwość chmury**. Skorzystaj z informacji podanych w poniższej tabeli, aby dodać właściwość chmury do szablonu urządzenia.

    | Nazwa wyświetlana      | Typ semantyczny | Schemat |
    | ----------------- | ------------- | ------ |
    | Data ostatniego serwisowania | Brak          | Data   |
    | Nazwa klienta     | Brak          | Ciąg |

1. Wybierz pozycję **Zapisz** , aby zapisać zmiany:

    ![Właściwości chmury](media/quick-create-pnp-device-pnp/cloudproperties.png)

## <a name="create-views"></a>Tworzenie widoków

Jako Konstruktor można dostosować aplikację tak, aby wyświetlała odpowiednie informacje o urządzeniu czujnika środowiska z operatorem. Twoje dostosowania umożliwiają operatorowi zarządzanie urządzeniami czujnika środowiskowego podłączonymi do aplikacji. Można utworzyć dwa typy widoków dla operatora, który ma być używany do współpracy z urządzeniami:

* Formularze do wyświetlania i edytowania właściwości urządzenia i chmury.
* Pulpity nawigacyjne do wizualizacji urządzeń.

### <a name="generate-default-views"></a>Generuj widoki domyślne

Generowanie widoków domyślnych jest szybkim sposobem na rozpoczęcie wizualizacji ważnych informacji o urządzeniu. Dla szablonu urządzenia można zdefiniować maksymalnie trzy widoki domyślne:

* Widok **poleceń** umożliwia operatorowi Wysyłanie poleceń do urządzenia.
* Widok **Przegląd** używa wykresów i metryk do wyświetlania danych telemetrycznych urządzenia.
* W widoku **informacje** są wyświetlane właściwości urządzenia.

Wybierz pozycję **widoki** , a następnie opcję **Wygeneruj widoki domyślne**.

### <a name="configure-a-view-to-visualize-devices"></a>Konfigurowanie widoku do wizualizacji urządzeń

Pulpit nawigacyjny urządzenia umożliwia operatorowi wizualizowanie urządzenia przy użyciu wykresów i metryk. Jako konstruktor możesz zdefiniować informacje wyświetlane na pulpicie nawigacyjnym urządzenia. Można zdefiniować wiele pulpitów nawigacyjnych dla urządzeń. Aby utworzyć pulpit nawigacyjny w celu wizualizacji danych telemetrycznych czujnika środowiska, wybierz pozycję **widoki** , a następnie **Wizualizuj urządzenie**:

1. Wszystkie właściwości urządzenia, właściwości chmury, telemetrii i opcje statyczne są wymienione w obszarze **Właściwości**. Można przeciągać i upuszczać dowolne z tych elementów w widoku. Przeciągnij Właściwość **poziom jasności** do widoku. Kafelek można skonfigurować przy użyciu ikony koła zębatego.

1. Aby dodać wykres, który przedstawia dane telemetryczne, wybierz **wilgotność** i **temperaturę**, a następnie wybierz pozycję **Połącz**. Aby wyświetlić ten wykres w innym formacie, na przykład na wykresie kołowym lub wykresie słupkowym, wybierz przycisk **Zmień wizualizację** w górnej części kafelka.

1. Wybierz pozycję **Zapisz** , aby zapisać swój widok:

Można dodać więcej kafelków, które pokazują inne właściwości lub wartości telemetrii. Możesz również dodać tekst statyczny, linki i obrazy. Aby przenieść lub zmienić rozmiar kafelka na pulpicie nawigacyjnym, przesuń wskaźnik myszy nad kafelek i przeciągnij kafelek do nowej lokalizacji lub zmień jego rozmiar.

### <a name="add-a-device-form"></a>Dodaj formularz urządzenia

Formularz urządzenia umożliwia operatorowi Edytowanie właściwości urządzenia z możliwością zapisu i właściwości chmury. Jako Konstruktor można definiować wiele formularzy i wybierać, które urządzenia i właściwości chmury mają być wyświetlane w każdym formularzu. Możesz również wyświetlić właściwości urządzenia tylko do odczytu w formularzu.

Aby utworzyć formularz do wyświetlania i edytowania właściwości czujnika środowiska:

1. Przejdź do **widoku** w szablonie **czujnika środowiska** . Wybierz kafelek **Edytowanie urządzenia i danych w chmurze** , aby dodać nowy widok.

1. Wprowadź nazwę formularza **Właściwości czujnika środowiska**.

1. Przeciągnij właściwości **Nazwa klienta** i **Data ostatniej usługi** do istniejącej sekcji w formularzu.

1. Wybierz **poziom jasności** i właściwości urządzenia **stanu urządzenia** . Następnie wybierz pozycję **Dodaj sekcję**. Edytuj tytuł sekcji jako **Właściwości czujnika**. Wybierz przycisk **Zastosuj**.

1. Wybierz właściwości urządzenia **model**, **wersja oprogramowania**, **producent**i **producent procesora** . Następnie wybierz pozycję **Dodaj sekcję**. Edytuj tytuł sekcji jako **Właściwości urządzenia**. Wybierz przycisk **Zastosuj**.

1. Wybierz pozycję **Zapisz** , aby zapisać swój widok.

## <a name="publish-device-template"></a>Publikowanie szablonu urządzenia

Aby można było utworzyć symulowany czujnik środowiska lub połączyć prawdziwy czujnik środowiska, należy opublikować szablon urządzenia.

Aby opublikować szablon urządzenia:

1. Przejdź do szablonu urządzenia ze strony **Szablony urządzeń** .

1. Wybierz pozycję **Publikuj**.

1. W oknie dialogowym **Publikowanie szablonu urządzenia** wybierz pozycję **Publikuj**:

    ![Opublikowany model](media/quick-create-pnp-device-pnp/publishedmodel.png)

Po opublikowaniu szablonu urządzenia jest on widoczny na stronie **urządzenia** i do operatora. W opublikowanym szablonie urządzenia nie można edytować modelu możliwości urządzenia bez tworzenia nowej wersji. Można jednak wykonywać aktualizacje właściwości, dostosowań i widoków w chmurze w opublikowanym szablonie urządzenia bez obsługi wersji. Po wprowadzeniu zmian wybierz pozycję **Publikuj** , aby wypchnąć te zmiany do operatora.

## <a name="add-a-simulated-device"></a>Dodawanie symulowanego urządzenia

Aby dodać symulowane urządzenie do aplikacji, należy użyć utworzonego szablonu urządzenia **czujnika środowiska** .

1. Aby dodać nowe urządzenie jako operator, wybierz pozycję **urządzenia** w lewym okienku. Karta **urządzenia** przedstawia **wszystkie urządzenia** i szablon urządzenia **czujnika środowiska** . Wybierz **czujnik środowiska**.

1. Aby dodać symulowane urządzenie czujnika środowiska, wybierz pozycję **+ Nowy**. Użyj sugerowanego **identyfikatora urządzenia** lub wprowadź swój własny **Identyfikator**w postaci małych liter. Możesz również wprowadzić nazwę nowego urządzenia. Przełącz **symulowany** przełącznik na wartość **włączone** , a następnie wybierz pozycję **Utwórz**.

    ![Symulowane urządzenie](./media/quick-create-pnp-device-pnp/simulated-device.png)

Teraz można korzystać z widoków, które zostały utworzone przez konstruktora dla szablonu urządzenia przy użyciu symulowanych danych.

## <a name="use-a-simulated-device-to-improve-views"></a>Ulepszanie widoków przy użyciu symulowanego urządzenia

Po utworzeniu nowego symulowanego urządzenia, Konstruktor może używać tego urządzenia do dalszego ulepszania i kompilowania widoków dla szablonu urządzenia.

1. W lewym okienku wybierz pozycję **Szablony urządzeń** , a następnie wybierz szablon **czujnika środowiska** .

1. Wybierz dowolne z widoków, które chcesz edytować, lub Utwórz nowy widok. Kliknij pozycję **Konfiguruj Podgląd urządzenia**, a następnie **Wybierz pozycję z uruchomionego urządzenia**. W tym miejscu możesz wybrać opcję braku urządzenia do wyświetlania wersji zapoznawczej przy użyciu rzeczywistego urządzenia, które można skonfigurować do testowania, lub z istniejącego urządzenia dodanego do IoT Central.

1. Na liście Wybierz swoje symulowane urządzenie. Następnie wybierz pozycję **Zastosuj**. Teraz można zobaczyć to samo symulowane urządzenie w widokach szablonów urządzeń. Ten widok jest przydatny w przypadku wykresów i innych wizualizacji.

    ![Konfigurowanie urządzenia w wersji zapoznawczej](./media/quick-create-pnp-device-pnp/configure-preview.png)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia szablonu urządzenia **czujnika środowiska** i dodawania symulowanego urządzenia do aplikacji.

Aby dowiedzieć się więcej o monitorowaniu urządzeń połączonych z aplikacją, przejdź do przewodnika Szybki Start:

> [!div class="nextstepaction"]
> [Konfigurowanie reguł i akcji](./quick-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
