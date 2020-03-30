---
title: Szybki start — dodawanie symulowanego urządzenia do usługi Azure IoT Central
description: Ten przewodnik Szybki start pokazuje, jak utworzyć szablon urządzenia i dodać symulowane urządzenie do aplikacji IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 02/12/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 489bf81388c1bb889756d54957e791282054f0f7
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77169608"
---
# <a name="quickstart-add-a-simulated-device-to-your-iot-central-application"></a>Szybki start: dodawanie symulowanego urządzenia do aplikacji IoT Central

*Ten artykuł dotyczy operatorów, konstruktorów i administratorów.*

Szablon urządzenia definiuje możliwości urządzenia, które łączy się z aplikacją IoT Central. Możliwości obejmują dane telemetryczne, które urządzenie wysyła, właściwości urządzenia i polecenia, na które urządzenie odpowiada. Z szablonu urządzenia konstruktor lub operator może dodawać do aplikacji zarówno rzeczywiste, jak i symulowane urządzenia. Symulowane urządzenia są przydatne do testowania zachowania aplikacji IoT Central przed podłączeniem rzeczywistych urządzeń.

W tym przewodniku Szybki start należy dodać szablon urządzenia dla tablicy *MXChip IoT DevKit* (DevKit) i utworzyć symulowane urządzenie. Aby ukończyć ten szybki start, nie potrzebujesz prawdziwego urządzenia, pracujesz z symulacją urządzenia. Urządzenie DevKit:

* Wysyła dane telemetryczne, takie jak temperatura.
* Raportuje właściwości specyficzne dla urządzenia, takie jak poziom jasności.
* Odpowiada na polecenia, takie jak włączanie i wyłączanie.
* Raportuje ogólne właściwości urządzenia, takie jak wersja oprogramowania układowego i numer seryjny.

## <a name="prerequisites"></a>Wymagania wstępne

Ukończ szybki start [aplikacji Azure IoT Central,](./quick-deploy-iot-central.md) aby utworzyć aplikację IoT Central przy użyciu niestandardowego szablonu **aplikacji > niestandardowej.**

## <a name="create-a-template"></a>Tworzenie szablonu

Jako konstruktor możesz tworzyć i edytować szablony urządzeń w aplikacji IoT Central. Po opublikowaniu szablonu urządzenia można wygenerować symulowane urządzenie lub podłączyć rzeczywiste urządzenia z szablonu urządzenia. Symulowane urządzenia umożliwiają przetestowanie zachowania aplikacji przed podłączeniem rzeczywistego urządzenia.

Aby dodać nowy szablon urządzenia do aplikacji, wybierz kartę **Szablony urządzeń** w lewym okienku.

![Strona Szablony urządzeń](./media/quick-create-pnp-device/devicedefinitions.png)

Szablon urządzenia zawiera model możliwości urządzenia, który definiuje dane telemetryczne, które urządzenie wysyła, właściwości urządzenia i polecenia, na które urządzenie odpowiada.

### <a name="add-a-device-capability-model"></a>Dodawanie modelu możliwości urządzenia

Istnieje kilka opcji dodawania modelu możliwości urządzenia do aplikacji IoT Central. Można utworzyć model od podstaw, zaimportować model z pliku lub wybrać urządzenie z katalogu urządzeń. IoT Central obsługuje również podejście *oparte na urządzeniu,* w którym automatycznie importuje model z repozytorium, gdy urządzenie łączy się po raz pierwszy. W tym przewodniku Szybki start należy wybrać urządzenie z katalogu urządzeń, aby zaimportować jego model możliwości urządzenia.

W poniższych krokach pokazano, jak za pomocą katalogu urządzeń zaimportować model możliwości dla urządzenia **MXChip IoT DevKit.** Te urządzenia wysyłają dane telemetryczne, takie jak temperatura, do aplikacji:

1. Aby dodać nowy szablon **+** urządzenia, wybierz pozycję na stronie **Szablony urządzeń.**

1. Na stronie **Wybierz typ szablonu** przewiń w dół, aż znajdziesz kafelek **MXChip IoT DevKit.**

1. Zaznacz kafelek **MXChip IoT DevKit,** a następnie wybierz pozycję **Dalej: Dostosuj**.

1. Na stronie **Recenzja** wybierz pozycję **Utwórz**.

1. Po kilku sekundach możesz zobaczyć nowy szablon urządzenia:

    ![Szablon urządzenia MXChip IoT DevKit](./media/quick-create-pnp-device/devkit-template.png)

    Model funkcji MXChip IoT DevKit zawiera interfejsy, takie jak **mxchip_sensor**, **mxchip_settings**i **Informacje o urządzeniu.** Interfejsy definiują możliwości urządzenia MXChip IoT DevKit. Możliwości obejmują dane telemetryczne, które urządzenie wysyła, właściwości, które urządzenie zgłasza, oraz polecenia, na które urządzenie odpowiada.

### <a name="add-cloud-properties"></a>Dodawanie właściwości chmury

Szablon urządzenia może zawierać właściwości chmury. Właściwości chmury istnieją tylko w aplikacji IoT Central i nigdy nie są wysyłane do urządzenia ani ich odbierane.

1. Wybierz **właściwości chmury,** a następnie **+ Dodaj właściwość chmury**. Użyj informacji w poniższej tabeli, aby dodać dwie właściwości chmury do szablonu urządzenia:

    | Nazwa wyświetlana      | Typ semantyczny | Schemat |
    | ----------------- | ------------- | ------ |
    | Data ostatniego serwisowania | Brak          | Data   |
    | Nazwa klienta     | Brak          | Ciąg |

1. Wybierz **zapisz,** aby zapisać zmiany:

    ![Właściwości chmury](media/quick-create-pnp-device/cloudproperties.png)

## <a name="views"></a>Widoki

Jako konstruktor możesz dostosować aplikację, aby wyświetlić odpowiednie informacje o urządzeniu operatorowi. Dostosowania umożliwiają operatorowi zarządzanie urządzeniami podłączonymi do aplikacji. Można utworzyć dwa typy widoków dla operatora do interakcji z urządzeniami:

* Formularze do wyświetlania i edytowania właściwości urządzenia i chmury.
* Pulpity nawigacyjne do wizualizacji urządzeń, w tym danych telemetrycznych, które wysyłają.

### <a name="default-views"></a>Widoki domyślne

Widoki domyślne to szybki sposób na rozpoczęcie wizualizacji ważnych informacji o urządzeniu. Dla szablonu urządzenia mogą być generowane maksymalnie trzy widoki domyślne:

* Widok **Polecenia** umożliwia operatorowi wysyłanie poleceń do urządzenia.
* **Widok Przegląd** używa wykresów i metryk do wyświetlania danych telemetrycznych urządzenia.
* Widok **Informacje** wyświetla właściwości urządzenia.

Wybierz węzeł **Widoki** w szablonie urządzenia. Widać, że IoT Central wygenerował **przegląd** i widok **Informacje** dla Ciebie po dodaniu szablonu.

Aby dodać nowy formularz **Zarządzaj urządzeniem,** którego operator może używać do zarządzania urządzeniem:

1. Wybierz węzeł **Widoki,** a następnie wybierz urządzenie do edycji i kafelek **danych w chmurze,** aby dodać nowy widok.

1. Zmień nazwę formularza na **Zarządzanie urządzeniem**.

1. Wybierz właściwości chmury **Nazwa klienta** i Data **ostatniej usługi** oraz **właściwość Prędkość wentylatora.** Następnie wybierz pozycję **Dodaj sekcję**:

    ![Tworzenie nowego formularza](media/quick-create-pnp-device/new-form.png)

1. Wybierz **pozycję Zapisz,** aby zapisać nowy formularz.

## <a name="publish-device-template"></a>Publikowanie szablonu urządzenia

Przed utworzeniem symulowanego urządzenia lub podłączeniem rzeczywistego urządzenia należy opublikować szablon urządzenia. Chociaż IoT Central opublikował szablon podczas pierwszego utworzenia go, należy opublikować zaktualizowaną wersję.

Aby opublikować szablon urządzenia:

1. Przejdź do szablonu urządzenia na stronie **Szablony urządzeń.**

1. Wybierz **pozycję Publikuj**:

    ![Opublikowany model](media/quick-create-pnp-device/publishedmodel.png)

1. W oknie dialogowym **Publikowanie tego szablonu urządzenia w aplikacji** wybierz pozycję **Publikuj**. 

Po opublikowaniu szablonu urządzenia jest on widoczny na stronie **Urządzenia.** W opublikowanym szablonie urządzenia nie można edytować modelu możliwości urządzenia bez utworzenia nowej wersji. Można jednak wprowadzać aktualizacje właściwości, dostosowań i widoków w chmurze w opublikowanym szablonie urządzenia bez przechowywania wersji. Po wyrobieniu jakichkolwiek zmian wybierz **pozycję Publikuj,** aby wypchnąć te zmiany do operatora.

## <a name="add-a-simulated-device"></a>Dodawanie symulowanego urządzenia

Aby dodać symulowane urządzenie do aplikacji, należy użyć utworzonego szablonu urządzenia **MXChip IoT DevKit.**

1. Aby dodać nowe urządzenie jako operator, wybierz **pozycję Urządzenia** w lewym okienku. Na karcie **Urządzenia** są wyświetlane **wszystkie urządzenia** i szablon urządzenia **MXChip IoT DevKit.** Wybierz **MXChip IoT DevKit**.

1. Aby dodać symulowane urządzenie DevKit, wybierz opcję **+**. Użyj sugerowanego **identyfikatora urządzenia** lub wprowadź własny identyfikator **urządzenia**o małych literach . Można również wprowadzić nazwę nowego urządzenia. Upewnij się, że **przełącznik Symulowane** jest **włączone,** a następnie wybierz pozycję **Utwórz**.

    ![Symulowane urządzenie](./media/quick-create-pnp-device/simulated-device.png)

Teraz możesz wchodzić w interakcje z widokami utworzonymi przez konstruktora dla szablonu urządzenia przy użyciu symulowanych danych:

1. Wybierz symulowane urządzenie na stronie **Urządzenia.**

1. **Widok Przegląd** pokazuje wykres symulowanej telemetrii:

    ![Widok przeglądowy](./media/quick-create-pnp-device/simulated-telemetry.png)

1. Widok **Informacje** zawiera wartości właściwości, w tym właściwości chmury dodane do widoku.

1. Widok **Polecenia** umożliwia uruchamianie poleceń, takich jak **miganie** na urządzeniu.

1. Widok **Zarządzaj urządzeniami** to formularz utworzony dla operatora do zarządzania urządzeniem.

## <a name="use-a-simulated-device-to-improve-views"></a>Poprawianie widoków za pomocą symulowanego urządzenia

Po utworzeniu nowego symulowanego urządzenia konstruktor może użyć tego urządzenia, aby nadal ulepszać i korzystać z widoków szablonu urządzenia.

1. Wybierz **pozycję Szablony urządzeń** w lewym okienku i wybierz szablon **MXChip IoT DevKit.**

1. Wybierz dowolny z widoków, który chcesz edytować, lub utwórz nowy widok. Wybierz **pozycję Konfiguruj urządzenie podglądu,** a następnie **wybierz z uruchomionego urządzenia**. W tym miejscu można wybrać opcję braku urządzenia podglądu, rzeczywistego urządzenia skonfigurowanego do testowania lub istniejącego urządzenia dodanego do centrum IoT.

1. Wybierz symulowane urządzenie na liście. Następnie wybierz pozycję **Zastosuj**. Teraz możesz zobaczyć to samo symulowane urządzenie w widokach szablonu urządzenia. Ten widok jest przydatny w przypadku wykresów i innych wizualizacji.

    ![Konfigurowanie urządzenia podglądu](./media/quick-create-pnp-device/configure-preview.png)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start dowiesz się, jak utworzyć szablon urządzenia **MXChip IoT DevKit** i dodać symulowane urządzenie do aplikacji.

Aby dowiedzieć się więcej o monitorowaniu urządzeń podłączonych do aplikacji, przejdź do przewodnika Szybki start:

> [!div class="nextstepaction"]
> [Konfigurowanie reguł i akcji](./quick-configure-rules.md)
