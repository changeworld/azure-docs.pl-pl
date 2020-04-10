---
title: Samouczek — tworzenie aplikacji analitycznej w sklepie w usłudze Azure IoT Central
description: W tym samouczku pokazano, jak utworzyć aplikację sieci sprzedaży analizy w sklepie w centrum IoT. Utworzysz go, dostosujesz i dodasz urządzenia czujników.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: timlt
author: timlt
ms.date: 11/12/2019
ms.openlocfilehash: 50dd6038a8642f13cea7840fff723a5cf12ce2dd
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "81000252"
---
# <a name="tutorial-create-an-in-store-analytics-application-in-azure-iot-central"></a>Samouczek: Tworzenie aplikacji analitycznej w sklepie w usłudze Azure IoT Central



W samouczku pokazano, jak utworzyć aplikację analityczną usługi Azure IoT Central w sklepie. Przykładowa aplikacja jest dla sklepu detalicznego. Jest to rozwiązanie wspólnej potrzeby biznesowej monitorowania i dostosowywania się do obłożenia i warunków środowiskowych.

Przykładowa aplikacja, którą tworzysz, zawiera trzy rzeczywiste urządzenia: bramę Rigado Cascade 500 i dwa czujniki RuuviTag. Samouczek pokazuje również, jak używać symulowanego czujnika obłożenia zawartego w szablonie aplikacji do celów testowych. Brama Rigado C500 służy jako centrum komunikacyjne w aplikacji. Komunikuje się z czujnikami w sklepie i zarządza ich połączeniami z chmurą. RuuviTag to czujnik środowiskowy, który zapewnia dane telemetryczne, w tym temperaturę, wilgotność i ciśnienie. Symulowany czujnik obłożenia umożliwia śledzenie ruchu i obecności w obszarach kasowych sklepu. 

Ten samouczek zawiera wskazówki dotyczące podłączania urządzeń Rigado i RuuviTag do aplikacji. Jeśli masz inną bramę i czujniki, nadal możesz wykonać kroki, aby utworzyć aplikację. Samouczek pokazuje również, jak utworzyć symulowane czujniki RuuviTag. Symulowane czujniki umożliwiają tworzenie aplikacji, jeśli nie masz prawdziwych urządzeń. 

Rozwiązanie do kasy i monitorowania stanu jest rozwijane w trzech częściach:

* Tworzenie aplikacji i podłączanie urządzeń w celu monitorowania warunków
* Dostosowywanie pulpitu nawigacyjnego w celu umożliwienia operatorom monitorowania urządzeń i zarządzania nimi
* Konfigurowanie eksportu danych w celu umożliwienia menedżerom sklepów uruchamiania analiz i wizualizacji szczegółowych informacji

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Użyj szablonu analizy centrali Azure IoT **w sklepie — wyewidencjonowanie,** aby utworzyć aplikację sklepu detalicznego
> * Dostosowywanie ustawień aplikacji
> * Tworzenie i dostosowywanie szablonów urządzeń IoT
> * Łączenie urządzeń z aplikacją
> * Dodawanie reguł i akcji w celu monitorowania warunków

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć tę serię samouczków, potrzebujesz:
* Zalecana jest subskrypcja platformy Azure. Opcjonalnie możesz skorzystać z bezpłatnego 7-dniowego okresu próbnego. Jeśli nie masz subskrypcji platformy Azure, możesz ją utworzyć na [stronie rejestracji platformy Azure.](https://aka.ms/createazuresubscription)
* Dostęp do urządzenia bramy i dwóch czujników środowiskowych (opcjonalnie można użyć symulowanych urządzeń, jak opisano w samouczku)
* Szablony urządzeń dla używanych urządzeń (szablony są dostępne dla wszystkich urządzeń używanych w samouczku)

## <a name="create-an-application"></a>Tworzenie aplikacji
W tej sekcji utworzysz nową aplikację Azure IoT Central na podstawie szablonu. Użyjesz tej aplikacji w całej serii samouczków, aby utworzyć kompletne rozwiązanie.

Aby utworzyć nową aplikację usługi Azure IoT Central:

1. Przejdź do witryny [menedżera aplikacji usługi Azure IoT Central.](https://aka.ms/iotcentral)

1. Jeśli masz subskrypcję platformy Azure, zaloguj się przy użyciu poświadczeń, których używasz do uzyskiwania do niej dostępu, w przeciwnym razie zaloguj się przy użyciu konta Microsoft:

    ![Określanie konta organizacji](./media/tutorial-in-store-analytics-create-app/sign-in.png)

1. Aby rozpocząć tworzenie nowej aplikacji usługi Azure IoT Central, wybierz pozycję **Nowa aplikacja**.

1. Wybierz **pozycję Handel detaliczny**.  Na stronie sieci sprzedaży jest wyświetlanych kilka szablonów aplikacji sieci sprzedaży.

Aby utworzyć nową aplikację do realizacji transakcji analizy w sklepie:  

1. Wybierz szablon aplikacji analizy w sklepie — **wyewidencjonuj.** Ten szablon zawiera szablony urządzeń dla wszystkich urządzeń używanych w samouczku z wyjątkiem czujników RuuviTag. Szablon zawiera również pulpit nawigacyjny operatora do monitorowania realizacji transakcji i warunków środowiskowych oraz stanu urządzenia. 

1. Opcjonalnie wybierz przyjazną **nazwę aplikacji**. Ta aplikacja jest oparta na fikcyjnym sklepie detalicznym o nazwie Contoso. Samouczek używa **nazwy aplikacji** *Contoso checkout*. Szablon aplikacji jest oparty na fikcyjnej firmie Northwind. W tym samouczku użyj Contoso, aby dowiedzieć się, jak dostosować aplikację.

    > [!NOTE]
    > Jeśli używasz przyjaznej **nazwy aplikacji,** nadal musisz użyć unikatowej wartości **dla adresu URL**aplikacji .

1. Jeśli masz subskrypcję platformy Azure, wprowadź *katalog, subskrypcję platformy Azure i region*. Jeśli nie masz subskrypcji, możesz włączyć **7-dniową bezpłatną wersję próbną** i wypełnić wymagane informacje kontaktowe.  

    Aby uzyskać więcej informacji na temat katalogów i subskrypcji, zapoznaj się z [przewodnikiem Szybki start dotyczącym tworzenia aplikacji](../core/quick-deploy-iot-central.md).

1. Wybierz **pozycję Utwórz**.

    ![Strona Tworzenie aplikacji usługi Azure IoT Central](./media/tutorial-in-store-analytics-create-app/preview-application-template.png)

    ![Informacje rozliczeniowe azure IoT Central Tworzenie aplikacji](./media/tutorial-in-store-analytics-create-app/preview-application-template-billinginfo.png)

## <a name="customize-application-settings"></a>Dostosowywanie ustawień aplikacji

Jako konstruktor można zmienić kilka ustawień, aby dostosować środowisko użytkownika w aplikacji. W tej sekcji wybierz wstępnie zdefiniowany motyw aplikacji. Opcjonalnie dowiesz się, jak utworzyć motyw niestandardowy i zaktualizować obraz aplikacji. Motyw niestandardowy umożliwia ustawienie kolorów przeglądarki aplikacji, ikony przeglądarki i logo aplikacji, które pojawia się w nagłówku.

Aby wybrać wstępnie zdefiniowany motyw aplikacji:

1. Wybierz **pozycję Ustawienia** na nagłówku.

    ![Ustawienia aplikacji Azure IoT Central](./media/tutorial-in-store-analytics-create-app/settings-icon.png)

2. Wybierz nowy **motyw**.

3. Wybierz **pozycję Zapisz**.

Zamiast używać wstępnie zdefiniowanego motywu, można utworzyć motyw niestandardowy. Jeśli chcesz użyć zestawu przykładowych obrazów, aby dostosować aplikację i ukończyć samouczek, pobierz [przykładowe obrazy contoso](https://github.com/Azure-Samples/iot-central-docs-samples/tree/master/retail).

Aby utworzyć motyw niestandardowy:

1. Rozwiń lewe okienko, jeśli nie zostało jeszcze rozwinięte.

    ![Lewe okienko usługi Azure IoT Central](./media/tutorial-in-store-analytics-create-app/dashboard-expand.png)

1. Wybierz **pozycję Administracja > Dostosuj aplikację**.

1. Użyj przycisku **Zmień,** aby wybrać obraz do przesłania jako **logo Aplikacji**. Opcjonalnie należy określić wartość **tekstu alt logo**. 

1. Użyj przycisku **Zmień,** aby wybrać obraz **ikony przeglądarki,** który pojawi się na kartach przeglądarki.

1. Opcjonalnie zastąp **domyślne kolory przeglądarki,** dodając kody kolorów szesnastkowych HTML. W przypadku **nagłówka**dodaj *#008575*.  Dla **akcentu**dodaj *#A1F3EA*. 

1. Wybierz **pozycję Zapisz**. 

    ![Niestandardowe logo usługi Azure IoT Central](./media/tutorial-in-store-analytics-create-app/select-application-logo.png)

    Po zapisaniu aplikacja aktualizuje kolory przeglądarki, logo w nagłówku i ikonę przeglądarki. 

    ![Zaktualizowane ustawienia aplikacji usługi Azure IoT Central](./media/tutorial-in-store-analytics-create-app/saved-application-settings.png)

Aby zaktualizować obraz aplikacji:

1. Wybierz **pozycję Administracja > ustawienia aplikacji**.

1. Użyj przycisku **Wybierz obraz,** aby wybrać obraz do przesłania jako obraz aplikacji. Ten obraz pojawi się na kafelku aplikacji na stronie **Moje aplikacje** menedżera aplikacji IoT Central.

1. Wybierz **pozycję Zapisz**.

1. Opcjonalnie przejdź do widoku **Moje aplikacje** w witrynie sieci Web [menedżera aplikacji Usługi Azure IoT Central.](https://aka.ms/iotcentral) Kafelek aplikacji wyświetla zaktualizowany obraz aplikacji.

    ![Dostosowywanie obrazu aplikacji usługi Azure IoT Central](./media/tutorial-in-store-analytics-create-app/customize-application-image.png)

## <a name="create-device-templates"></a>Tworzenie szablonów urządzeń
Jako konstruktor można tworzyć szablony urządzeń, które umożliwiają operatorom aplikacji konfigurowanie urządzeń i zarządzanie nimi. Szablon można utworzyć, tworząc szablon niestandardowy, importując istniejący plik szablonu lub importując szablon z katalogu urządzeń Usługi Azure IoT. Po utworzeniu i dostosowaniu szablonu urządzenia użyj go do podłączenia rzeczywistych urządzeń do aplikacji. Opcjonalnie użyj szablonu urządzenia do generowania symulowanych urządzeń do testowania.

Szablon aplikacji **analizy w sklepie — usługa kasa** zawiera szablony urządzeń dla kilku urządzeń.  Istnieją szablony urządzeń dla dwóch z trzech urządzeń używanych w aplikacji. Szablon urządzenia RuuviTag nie jest uwzględniony w szablonie aplikacji analizy w sklepie — **wyewidencjonowanie.** W tej sekcji dodajesz szablon urządzenia dla czujników RuuviTag do aplikacji.

Aby dodać szablon urządzenia RuuviTag do aplikacji:

1. Wybierz **pozycję Szablony urządzeń** w lewym okienku.

1. Wybierz **+ Nowy,** aby utworzyć nowy szablon urządzenia.

1. Znajdź i wybierz szablon urządzenia **czujnika RuuviTag** w katalogu urządzeń Usługi Azure IoT. 

1. **Wybierz dalej: Dostosuj**.

    ![Szablon urządzenia czujnika Azure IoT Central RuuviTag](./media/tutorial-in-store-analytics-create-app/ruuvitag-device-template.png)

1. Wybierz **pozycję Utwórz**. Aplikacja dodaje szablon urządzenia RuuviTag.

1. Wybierz **pozycję Szablony urządzeń** w lewym okienku. Strona wyświetla wszystkie szablony urządzeń zawarte w szablonie aplikacji i szablon urządzenia RuuviTag, który właśnie dodał.

    ![Szablon urządzenia czujnika Azure IoT Central RuuviTag](./media/tutorial-in-store-analytics-create-app/device-templates-list.png)

## <a name="customize-device-templates"></a>Dostosowywanie szablonów urządzeń
Szablony urządzeń w aplikacji można dostosować na trzy sposoby. Najpierw można dostosować natywne wbudowane interfejsy w urządzeniach, zmieniając możliwości urządzenia. Na przykład za pomocą czujnika temperatury można zmienić szczegóły, takie jak nazwa wyświetlana interfejsu temperatury, typ danych, jednostki miary oraz minimalne i maksymalne zakresy robocze. 

Po drugie, dostosuj szablony urządzeń, dodając właściwości chmury. Właściwości chmury nie są częścią wbudowanych funkcji urządzenia. Właściwości chmury to dane niestandardowe tworzone, przechowywane i kojarzone z urządzeniami przez aplikację Azure IoT Central. Przykładem właściwości chmury może być obliczona wartość lub metadane, takie jak lokalizacja, którą chcesz skojarzyć z zestawem urządzeń. 

Po trzecie, dostosuj szablony urządzeń, tworząc widoki niestandardowe. Widoki umożliwiają operatorom wizualizację danych telemetrycznych i metadanych urządzeń, takich jak metryki urządzenia i kondycja.

W tym miejscu można użyć dwóch pierwszych metod, aby dostosować szablon urządzenia dla czujników RuuviTag. Aby uzyskać informacje na temat tworzenia widoków dla czujników, zobacz Dodawanie symulowanego urządzenia do szybkiego startu [aplikacji IoT Central.](../core/quick-create-simulated-device.md)

Aby dostosować wbudowane interfejsy szablonu urządzenia RuuviTag:

1. Wybierz **pozycję Szablony urządzeń** w lewym okienku. 

1. Wybierz szablon dla czujników RuuviTag. 

1. Ukryj lewe okienko. Widok podsumowania szablonu wyświetla możliwości urządzenia.

    ![Widok podsumowania szablonu urządzenia Azure IoT Central RuuviTag](./media/tutorial-in-store-analytics-create-app/ruuvitag-device-summary-view.png)

1. Wybierz **pozycję Dostosuj** w menu szablonu urządzenia RuuviTag. 

1. Przewiń listę możliwości i `humidity` znajdź typ telemetrii. Jest to element wiersza z edytowalną **wartością nazwy wyświetlanej** *wilgotności*.

W poniższych krokach można `humidity` dostosować typ telemetrii dla czujników RuuviTag. Opcjonalnie dostosuj niektóre inne typy danych telemetrycznych.

W `humidity` przypadku typu telemetrii należy wprowadzić następujące zmiany:

1. Wybierz formant **Rozwiń,** aby rozwinąć szczegóły schematu dla wiersza.

1. Zaktualizuj wartość **Nazwa wyświetlana** z *wilgotności* do wartości niestandardowej, takiej jak *wilgotność względna*.

1. Zmień opcję **Typ semantyczny** z *Brak* na *Wilgotność*.  Opcjonalnie ustaw wartości schematu dla typu telemetrii wilgotności w widoku rozwiniętego schematu. Ustawienia schematu umożliwiają tworzenie szczegółowych wymagań dotyczących sprawdzania poprawności danych, które monitoruje czujniki. Na przykład można ustawić minimalne i maksymalne wartości zakresu pracy dla danego interfejsu.

1. Wybierz przycisk **Zapisz**, aby zapisać zmiany.

    ![Dostosowywanie szablonu urządzenia Azure IoT Central RuuviTag](./media/tutorial-in-store-analytics-create-app/ruuvitag-device-template-customize.png)

Aby dodać właściwość chmury do szablonu urządzenia w aplikacji:

1. Wybierz **właściwości chmury** w menu szablonu urządzenia RuuviTag.

1. Wybierz **pozycję Dodaj właściwość chmury**. 

Określ następujące wartości, aby utworzyć właściwość niestandardową do przechowywania lokalizacji każdego urządzenia:

1. Wprowadź wartość *Lokalizacja* **dla nazwy wyświetlanej**. Ta wartość jest automatycznie kopiowana do pola **Nazwa,** które jest przyjazną nazwą właściwości. Można użyć skopiowanej wartości lub jej zmienić.

1. Wybierz *ciąg* w menu rozwijanym **Schemat.** Typ ciągu umożliwia skojarzenie ciągu nazwy lokalizacji z dowolnym urządzeniem na podstawie szablonu. Na przykład można skojarzyć obszar w sklepie z każdym urządzeniem. Opcjonalnie można ustawić **typ semantyczny** właściwości na *Lokalizacja,* a to automatycznie ustawia **schemat** na *Geopoint*. Umożliwia powiązanie współrzędnych GPS z urządzeniem. 

1. Ustaw **minimalną długość** na *2*. 

1. Ustaw **przycinanie odstępu** na **Włączone**.

1. Wybierz **pozycję Zapisz,** aby zapisać niestandardową właściwość chmury.

    ![Dostosowywanie szablonu urządzenia Azure IoT Central RuuviTag](./media/tutorial-in-store-analytics-create-app/ruuvitag-device-template-cloud-property.png)

1. Wybierz pozycję **Publikuj**. 

    Publikowanie szablonu urządzenia sprawia, że jest widoczny dla operatorów aplikacji. Po opublikowaniu szablonu użyj go do generowania symulowanych urządzeń do testowania lub do łączenia rzeczywistych urządzeń z aplikacją. Jeśli masz już urządzenia podłączone do aplikacji, opublikowanie dostosowanego szablonu wypycha zmiany na urządzeniach.

## <a name="add-devices"></a>Dodawanie urządzeń
Po utworzeniu i dostosowaniu szablonów urządzeń nadszedł czas, aby dodać urządzenia. 

W tym samouczku do utworzenia aplikacji użyto następującego zestawu rzeczywistych i symulowanych urządzeń:
- Prawdziwa brama Rigado C500
- Dwa prawdziwe czujniki RuuviTag
- Symulowany czujnik **obłożenia.** Symulowany czujnik znajduje się w szablonie aplikacji, więc nie trzeba go tworzyć. 

> [!NOTE]
> Jeśli nie masz prawdziwych urządzeń, nadal możesz ukończyć ten samouczek, tworząc symulowane czujniki RuuviTag. Poniższe wskazówki obejmują kroki tworzenia symulowanego ruuvitagu. Nie trzeba tworzyć symulowanej bramy.

Wykonaj kroki opisane w następujących dwóch artykułach, aby podłączyć prawdziwą bramę Rigado i czujniki RuuviTag. Po zakończeniu wróć do tego samouczka. Ponieważ już utworzono szablony urządzeń w tym samouczku, nie trzeba tworzyć ich ponownie w poniższym zestawie wskazówek.

- Aby połączyć bramę Rigado, zobacz [Łączenie rigado cascade 500 z aplikacją Azure IoT Central](../core/howto-connect-rigado-cascade-500.md?toc=/azure/iot-central/retail/toc.json&bc=/azure/iot-central/retail/breadcrumb/toc.json).
- Aby połączyć czujniki RuuviTag, zobacz [Łączenie czujnika RuuviTag z aplikacją Azure IoT Central](../core/howto-connect-ruuvi.md?toc=/azure/iot-central/retail/toc.json&bc=/azure/iot-central/retail/breadcrumb/toc.json). Można również użyć tych wskazówek do utworzenia dwóch symulowanych czujników, jeśli to konieczne.

## <a name="add-rules-and-actions"></a>Dodawanie reguł i akcji
W ramach korzystania z czujników w aplikacji Azure IoT Central do monitorowania warunków można utworzyć reguły do uruchamiania akcji, gdy spełnione są określone warunki. Reguła jest skojarzona z szablonem urządzenia i co najmniej jednym urządzeniem i zawiera warunki, które muszą być spełnione na podstawie danych telemetrycznych lub zdarzeń. Reguła ma również jedną lub więcej skojarzonych akcji. Akcje mogą obejmować wysyłanie powiadomień e-mail lub wyzwalanie akcji elementu webhook w celu wysyłania danych do innych usług. Szablon aplikacji analizy w sklepie — **wyewidencjonowanie** zawiera niektóre wstępnie zdefiniowane reguły dla urządzeń w aplikacji.

W tej sekcji utworzysz nową regułę, która sprawdza maksymalny poziom wilgotności względnej na podstawie danych telemetrycznych czujnika RuuviTag. Dodaj akcję do reguły, tak aby jeśli wilgotność przekracza maksimum, aplikacja wysyła wiadomości e-mail. 

Aby utworzyć regułę: 

1. Rozwiń lewe okienko.

1. Wybierz **reguły**.

1. Wybierz **+ Nowy**.

1. Wprowadź *poziom wilgotności* jako nazwę reguły. 

1. Wybierz szablon urządzenia RuuviTag w **zakresach**. Zdefiniowana reguła będzie stosowana do wszystkich czujników opartych na tym szablonie. Opcjonalnie można utworzyć filtr, który zastosuje regułę tylko do zdefiniowanego podzbioru czujników. 

1. Wybierz `Relative humidity` jako **telemetrię**. Jest to funkcja urządzenia dostosowana w poprzednim kroku.

1. Wybierz `Is greater than` jako **operator**. 

1. Wprowadź typowy górny zakres poziomu wilgotności w pomieszczeniu dla środowiska jako **wartość**. Na przykład wprowadź *65*. Ustawiłeś warunek reguły, który występuje, gdy wilgotność względna w dowolnym rzeczywistym lub symulowanym czujniku RuuviTag przekracza tę wartość. Może być konieczne dostosowanie wartości w górę lub w dół w zależności od normalnego zakresu wilgotności w środowisku.  

   ![Warunki reguły dodawania usługi Azure IoT Central](./media/tutorial-in-store-analytics-create-app/rules-add-conditions.png)

Aby dodać akcję do reguły:

1. Wybierz **+ E-mail**. 

1. Wprowadź *powiadomienie o wysokiej wilgotności* jako przyjazną nazwę **wyświetlaną** akcji. 

1. Wprowadź adres e-mail skojarzony z kontem w **obszarze Do**. Jeśli używasz innego adresu e-mail, adres, którego używasz, musi być dla użytkownika, który został dodany do aplikacji. Użytkownik musi również zalogować się i wylogować co najmniej raz.

1. Opcjonalnie wprowadź notatkę, która ma być uwzględnina w tekście wiadomości e-mail.

1. Wybierz **pozycję Gotowe,** aby ukończyć akcję.

   ![Usługa Azure IoT Central dodaje akcje do reguł](./media/tutorial-in-store-analytics-create-app/rules-add-action.png)

1. Wybierz **pozycję Zapisz,** aby zapisać i aktywować nową regułę. 

    W ciągu kilku minut określone konto e-mail powinno zacząć otrzymywać wiadomości e-mail. Aplikacja wysyła wiadomości e-mail za każdym razem, gdy czujnik wskazuje, że poziom wilgotności przekroczył wartość w Twoim stanie.

## <a name="next-steps"></a>Następne kroki
W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

* Użyj szablonu analizy centrali Azure IoT **w sklepie — wyewidencjonowanie,** aby utworzyć aplikację sklepu detalicznego
* Dostosowywanie ustawień aplikacji
* Tworzenie i dostosowywanie szablonów urządzeń IoT
* Łączenie urządzeń z aplikacją
* Dodawanie reguł i akcji w celu monitorowania warunków

Teraz, po utworzeniu aplikacji do monitorowania stanu Usługi Azure IoT Central, oto sugerowany następny krok:

> [!div class="nextstepaction"]
> [Dostosowywanie pulpitu nawigacyjnego operatora](./tutorial-in-store-analytics-customize-dashboard.md)
