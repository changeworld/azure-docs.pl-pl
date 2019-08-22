---
title: Definiowanie nowego typu urządzenia w usłudze Azure IoT Central | Microsoft Docs
description: W tym samouczku pokazano, jak utworzyć nowy typ urządzenia w aplikacji IoT Central platformy Azure. Zdefiniuj dane telemetryczne, stan, właściwości i polecenia dla danego typu.
author: dominicbetts
ms.author: dobett
ms.date: 08/06/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: a622aa85d1d0a9dcd5d5ad9b2b30e7a3120ea974
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878012"
---
# <a name="tutorial-define-a-new-device-type-in-your-azure-iot-central-application-preview-features"></a>Samouczek: Definiowanie nowego typu urządzenia w aplikacji IoT Central platformy Azure (funkcje w wersji zapoznawczej)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Ten samouczek przedstawia, jak konstruktor może użyć szablonu urządzenia do zdefiniowania nowego typu urządzenia w aplikacji usługi Microsoft Azure IoT Central. Szablon urządzenia definiuje możliwości urządzenia. Możliwości obejmują dane telemetryczne wysyłane przez urządzenie, właściwości urządzenia i polecenia, na które urządzenie reaguje.

W tym samouczku utworzysz szablon urządzenia **czujnika środowiska** . Urządzenie czujnika środowiska:

* Wysyła dane telemetryczne, takie jak temperatura.
* Raportuje właściwości specyficzne dla urządzenia, takie jak poziom jasności.
* Reaguje na polecenia, takie jak Włączanie i wyłączanie.
* Raporty ogólne właściwości urządzenia, takie jak wersja oprogramowania układowego i numer seryjny.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz nowy szablon urządzenia.
> * Importuj model możliwości urządzenia.
> * Utwórz właściwości chmury.
> * Zdefiniuj wizualizację dla danych telemetrycznych urządzenia.
> * Opublikuj szablon urządzenia.
> * Utwórz symulowane urządzenie dla szablonu urządzenia.
> * Wyświetl symulowane urządzenie.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka potrzebna jest aplikacja usługi Azure IoT Central. Jeśli ukończono przewodnik Szybki start [Tworzenie aplikacji usługi Azure IoT Central](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json), możesz użyć ponownie aplikacji utworzonej w jego ramach. W przeciwnym przypadku wykonaj następujące kroki, aby utworzyć pustą aplikację usługi Azure IoT Central:

1. Przejdź na stronę [Menedżer aplikacji](https://aka.ms/iotcentral) usługi Azure IoT Central.

1. Jeśli masz subskrypcję platformy Azure, zaloguj się przy użyciu poświadczeń, których używasz, aby uzyskać do niej dostęp. w przeciwnym razie Podpisz go przy użyciu konto Microsoft:

    ![Określanie konta organizacji](./media/tutorial-define-device-type-pnp/sign-in.png)

1. Aby rozpocząć tworzenie nowej aplikacji usługi Azure IoT Central, wybierz pozycję **Nowa aplikacja**.

Aby utworzyć nową aplikację usługi Azure IoT Central, która korzysta z funkcji w wersji zapoznawczej, w tym Plug and Play IoT:

1. Wybierz pozycję **Wersja próbna**. Nie potrzebujesz subskrypcji platformy Azure, aby utworzyć aplikację w wersji próbnej.

    Aby uzyskać więcej informacji na temat katalogów i subskrypcji, zapoznaj się z [przewodnikiem Szybki start dotyczącym tworzenia aplikacji](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

1. Wybierz pozycję **wersja**zapoznawcza.

1. Opcjonalnie wybierz przyjazną nazwę aplikacji, taką jak **Klimatyzatory Contoso**. Usługa Azure IoT Central wygeneruje unikatowy prefiks adresu URL. Ten prefiks adresu URL możesz zmienić na łatwiejszy do zapamiętania.

1. W przypadku tworzenia aplikacji w wersji próbnej należy podać szczegóły dotyczące kontaktu.

1. Wybierz pozycję **Utwórz**.

    ![Strona Tworzenie aplikacji usługi Azure IoT Central](./media/tutorial-define-device-type-pnp/iotcentralcreate.png)

    Aby uzyskać więcej informacji, zapoznaj się z [przewodnikiem Szybki start dotyczącym tworzenia aplikacji](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

Potrzebna jest również lokalna kopia pliku **EnvironmentalSensorInline. capabilitymodel. JSON** , który zawiera model możliwości urządzenia [IoT Plug and Play](https://aka.ms/iot-pnp-docs) . Możesz pobrać go z tego [miejsca](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json). Kliknij prawym przyciskiem myszy na stronie i wybierz polecenie **Zapisz jako**.

Po pobraniu pliku Otwórz go w edytorze tekstów i Zastąp oba wystąpienia `<YOUR_COMPANY_NAME_HERE>` nazwą. Używaj tylko znaków a-z, A-Z, 0-9 i znaku podkreślenia.

## <a name="create-a-template"></a>Tworzenie szablonu

Jako Konstruktor można tworzyć i edytować szablony urządzeń w aplikacji. Po opublikowaniu szablonu urządzenia można wygenerować symulowane urządzenie lub połączyć rzeczywiste urządzenia, które implementują szablon urządzenia. Symulowane urządzenia umożliwiają testowanie zachowania aplikacji przed połączeniem rzeczywistego urządzenia.

Aby dodać nowy szablon urządzenia do aplikacji, przejdź do strony **Szablony urządzeń** . Aby to zrobić, wybierz kartę **Szablony urządzeń** w menu nawigacji po lewej stronie.

![Strona Szablony urządzeń](./media/tutorial-define-device-type-pnp/devicedefinitions.png)

### <a name="add-a-device-capability-model"></a>Dodawanie modelu możliwości urządzenia

Istnieje kilka opcji tworzenia modelu możliwości urządzeń w IoT Central. Można utworzyć niestandardowy model od podstaw, zaimportować z pliku, wybrać pozycję z katalogu urządzeń lub połączyć urządzenie Plug and Play IoT za pomocą połączenia pierwszego urządzenia, gdzie model możliwości urządzenia został opublikowany w repozytorium publicznym. W tym samouczku zaimportujesz model możliwości urządzenia z pliku.

Poniższe kroki pokazują, jak zaimportować model możliwości urządzenia **czujnika środowiska** . Te urządzenia wysyłają dane telemetryczne, takie jak temperatura, do aplikacji:

1. Aby dodać nowy szablon urządzenia, wybierz pozycję **+ Nowy** na stronie **Szablony urządzeń** .

1. Wybierz pozycję niestandardowa z listy modeli możliwości urządzeń.

1. Wprowadź **czujnik środowiska** jako nazwę szablonu urządzenia.

1. Wybierz pozycję **Importuj model możliwości** , aby utworzyć nowy model możliwości urządzenia z pliku JSON. Przejdź do folderu, w którym zapisano plik **EnvironmentalSensorInline. capabilitymodel. JSON** na komputerze lokalnym. Wybierz plik **EnvironmentalSensorInline. capabilitymodel. JSON** , a następnie wybierz pozycję Importuj. Model możliwości czujnika środowiska zawiera informacje o **czujniku** i interfejsie **informacji o urządzeniu** :

    ![Model możliwości urządzenia czujnika środowiska](./media/tutorial-define-device-type-pnp/newdevicecapabilitymodel.png)

    Te interfejsy definiują możliwości urządzenia **czujnika środowiska** . Możliwości obejmują dane telemetryczne wysyłane przez urządzenie, właściwości urządzenia i polecenia, na które urządzenie reaguje.

### <a name="add-cloud-properties"></a>Dodawanie właściwości chmury

Szablon urządzenia może zawierać właściwości chmury. Właściwości chmury istnieją tylko w aplikacji IoT Central i nigdy nie są wysyłane do ani odbierane z urządzenia.

1. Wybierz pozycję **właściwości chmury** , a następnie **+ Dodaj właściwość chmury**. Skorzystaj z informacji podanych w poniższej tabeli, aby dodać właściwość chmury do szablonu urządzenia.

    | Nazwa wyświetlana      | Typ semantyczny | Schemat |
    | ----------------- | ------------- | ------ |
    | Data ostatniego serwisowania | Brak          | Date   |
    | Nazwa klienta     | Brak          | String |

1. Wybierz pozycję **Zapisz** , aby zapisać zmiany:

    ![Właściwości chmury](media/tutorial-define-device-type-pnp/cloudproperties.png)

### <a name="add-customizations"></a>Dodawanie dostosowań

Użyj dostosowań, gdy musisz zmodyfikować interfejs lub dodać funkcje specyficzne dla IoT Central do możliwości, która nie wymaga użycia modelu możliwości urządzenia. Można dostosować pola, gdy model możliwości jest w stanie roboczym lub opublikowanym. Można dostosować tylko pola, które nie łamią zgodności interfejsów. Można na przykład:

- Dostosuj nazwę wyświetlaną i jednostki możliwości.
- Dodaj domyślny kolor, który ma być używany, gdy wartość pojawia się na wykresie.
- Określ początkową, minimalną i maksymalną wartość właściwości.

Nie można dostosować nazwy możliwości ani typu możliwości.


## <a name="create-views"></a>Tworzenie widoków

Jako Konstruktor można dostosować aplikację tak, aby wyświetlała odpowiednie informacje o urządzeniu czujnika środowiska z operatorem. Twoje dostosowania umożliwiają operatorowi zarządzanie urządzeniami czujnika środowiskowego podłączonymi do aplikacji. Można utworzyć dwa typy widoków dla operatora, który ma być używany do współpracy z urządzeniami:

* Formularze do wyświetlania i edytowania właściwości urządzenia i chmury.
* Pulpity nawigacyjne do wizualizacji urządzeń.

### <a name="generate-default-views"></a>Generuj widoki domyślne

Generowanie widoków domyślnych jest szybkim sposobem na rozpoczęcie wizualizacji ważnych informacji o urządzeniu. Dla szablonu urządzenia można zdefiniować maksymalnie trzy widoki domyślne:

* Widok **poleceń** umożliwia operatorowi Wysyłanie poleceń do urządzenia.
* Widok **Przegląd** używa wykresów i metryk do wyświetlania danych telemetrycznych urządzenia.
* W widoku **informacje** są wyświetlane właściwości urządzenia.

Po wybraniu opcji **Generuj widoki domyślne**są one automatycznie dodawane do sekcji **widoki** szablonu urządzenia.

### <a name="configure-a-view-to-visualize-devices"></a>Konfigurowanie widoku do wizualizacji urządzeń

Pulpit nawigacyjny urządzenia umożliwia operatorowi wizualizowanie urządzenia przy użyciu wykresów i metryk. Jako konstruktor możesz zdefiniować informacje wyświetlane na pulpicie nawigacyjnym urządzenia. Można zdefiniować wiele pulpitów nawigacyjnych dla urządzeń. Aby utworzyć pulpit nawigacyjny w celu wizualizacji danych telemetrycznych czujnika środowiska, wybierz pozycję **widoki** , a następnie **Wizualizuj urządzenie**:

1. Wszystkie właściwości urządzenia, właściwości chmury, telemetrii i opcje statyczne są wymienione w panelu po prawej stronie. Można przeciągać i upuszczać dowolne z tych elementów w widoku. Przeciągnij wartość właściwości do widoku. Kafelek można skonfigurować przy użyciu ikony koła zębatego.

1. Aby dodać wykres, który przedstawia dane telemetryczne , wybierz wilgotność i **temperaturę**, a następnie wybierz pozycję **Połącz**. Aby wyświetlić ten wykres w dowolnym innym formacie, takim jak wykres kołowy lub wykres słupkowy, wybierz przycisk **Zmień wizualizację** w górnej części kafelka.

1. Wybierz pozycję **Zapisz** , aby zapisać swój widok:

Można dodać więcej kafelków, które pokazują inne właściwości lub wartości telemetrii. Możesz również dodać tekst statyczny, linki i obrazy. Aby przenieść lub zmienić rozmiar kafelka na pulpicie nawigacyjnym, przesuń wskaźnik myszy nad kafelek i przeciągnij kafelek do nowej lokalizacji lub zmień jego rozmiar.

### <a name="add-a-device-form"></a>Dodaj formularz urządzenia

Formularz urządzenia umożliwia operatorowi Edytowanie właściwości urządzenia z możliwością zapisu i właściwości chmury. Jako Konstruktor można definiować wiele formularzy i wybierać, które urządzenia i właściwości chmury mają być wyświetlane w każdym formularzu. Możesz również wyświetlić właściwości urządzenia tylko do odczytu w formularzu.

Aby utworzyć formularz do wyświetlania i edytowania właściwości czujnika środowiska:

1. Wybierz kafelek **Edytowanie urządzenia i danych w chmurze** .

1. Wprowadź nazwę formularza **Właściwości czujnika środowiska**.

1. Przeciągnij właściwości **Nazwa klienta** i **Data ostatniej usługi** do istniejącej sekcji w formularzu.

1. Wybierz **poziom jasności** i właściwości urządzenia **stanu urządzenia** . Następnie wybierz pozycję **Dodaj sekcję**. Edytuj tytuł sekcji jako **Właściwości czujnika**. Wybierz przycisk **Zastosuj**.

1. Wybierz właściwości **baterii**pozostałej, **model urządzenia**, **wersja oprogramowania układowego**, **producent**i **numer seryjny** urządzenia. Następnie wybierz pozycję **Dodaj sekcję**. Edytuj tytuł sekcji jako **Właściwości urządzenia**. Wybierz przycisk **Zastosuj**.

1. Wybierz pozycję **Zapisz** , aby zapisać swój widok.


## <a name="publish-device-template"></a>Publikowanie szablonu urządzenia

Aby można było utworzyć symulowany czujnik środowiska lub połączyć prawdziwy czujnik środowiska, należy opublikować szablon urządzenia.

Aby opublikować szablon urządzenia:

1. Przejdź do szablonu urządzenia ze strony **Szablony urządzeń** .

1. Wybierz **publikowania**.

1. W oknie dialogowym **Publikowanie szablonu urządzenia** wybierz pozycję **Publikuj**:

    ![Opublikowany model](media/tutorial-define-device-type-pnp/publishedmodel.png)

Po opublikowaniu szablonu urządzenia jest on widoczny na stronie **urządzenia** i do operatora. W opublikowanym szablonie urządzenia nie można edytować modelu możliwości urządzenia bez tworzenia nowej wersji. Można jednak wykonywać aktualizacje właściwości, dostosowań i widoków w chmurze w opublikowanym szablonie urządzenia bez obsługi wersji. Po wprowadzeniu zmian wybierz pozycję **Publikuj** , aby wypchnąć te zmiany do operatora.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

* Tworzenie nowego szablonu urządzenia
* Importuj model możliwości urządzenia.
* Utwórz właściwości chmury.
* Utwórz dostosowania.
* Zdefiniuj wizualizację dla danych telemetrycznych urządzenia.
* Opublikuj szablon urządzenia.

Teraz, po utworzeniu szablonu urządzenia w aplikacji IoT Central platformy Azure, Oto sugerowany następny krok:

> [!div class="nextstepaction"]
> [Dodaj urządzenie](tutorial-add-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
