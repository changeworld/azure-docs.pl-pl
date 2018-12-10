---
title: 'Samouczek: usługa Azure Time Series Insights (wersja zapoznawcza) | Microsoft Docs'
description: Poznaj usługę Azure Time Series Insights (wersja zapoznawcza)
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 11/26/2018
ms.openlocfilehash: ed25d03f7c592476b9284790ac12f9954661a42b
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52872309"
---
# <a name="azure-time-series-insights-preview-tutorial"></a>Samouczek: usługa Azure Time Series Insights (wersja zapoznawcza)

Ten samouczek przeprowadzi Cię przez proces tworzenia środowiska usługi Azure Time Series Insights (TSI) w wersji zapoznawczej, wypełnianego przy użyciu danych z urządzeń symulowanych. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

* Tworzenie środowiska usługi TSI (wersja zapoznawcza).
* Łączenie środowiska usługi TSI (wersja zapoznawcza) z centrum zdarzeń.
* Uruchamianie symulacji farmy wiatru w celu strumieniowego przesyłania danych do środowiska usługi TSI w wersji zapoznawczej.
* Wykonywanie podstawowej analizy danych.
* Definiowanie hierarchii oraz typu modelu szeregów czasowych i kojarzenie ich z wystąpieniami.

## <a name="create-a-time-series-insights-preview-environment"></a>Tworzenie środowiska usługi Time Series Insights (wersja zapoznawcza)

W tej sekcji opisano, jak utworzyć środowisko usługi Azure TSI (wersja zapoznawcza) przy użyciu witryny [Azure Portal](https://portal.azure.com/).

1. Zaloguj się do witryny Azure Portal przy użyciu konta subskrypcji
1. W menu u góry po lewej stronie wybierz pozycję **+ Utwórz zasób**.
1. Wybierz kategorię **Internet rzeczy**, a następnie wybierz **Time Series Insights**.

  ![samouczek-jeden][1]

1. Na stronie środowiska usługi Time Series Insights wypełnij wymagane parametry i kliknij pozycję **Dalej: Źródło zdarzenia**

  ![samouczek-dwa][2]

1. Na stronie  **Źródło zdarzenia** wypełnij wymagane parametry i kliknij pozycję **Przeglądanie + tworzenie**.

  ![samouczek-trzy][3]

1. Przejrzyj wszystkie szczegóły, a następnie kliknij pozycję **Utwórz**, aby rozpocząć aprowizowanie środowiska.

  ![samouczek-cztery][4]

1. Po pomyślnym ukończeniu wdrażania zostanie wyświetlone powiadomienie.

  ![samouczek-pięć][5]

## <a name="send-events-to-your-tsi-environment"></a>Wysyłanie zdarzeń do środowiska usługi TSI

W tej sekcji użyjesz symulatora wiatraka, aby wysyłać zdarzenia do środowiska usługi TSI za pośrednictwem centrum zdarzeń.

  1. W witrynie Azure Portal przejdź do zasobu centrum zdarzeń i połącz go ze środowiskiem usługi TSI. Dowiedz się, [jak połączyć zasób z istniejącym centrum zdarzeń](./time-series-insights-how-to-add-an-event-source-eventhub.md).

  1. Na stronie zasobu centrum zdarzeń wybierz pozycję **Zasady dostępu współużytkowanego**, a następnie pozycję **RootManageSharedAccessKey**. Skopiuj wyświetlany tam **klucz podstawowy parametrów połączenia** .

      ![samouczek-sześć][6]

  1. Przejdź do obszaru [https://tsiclientsample.azurewebsites.net/windFarmGen.html]( https://tsiclientsample.azurewebsites.net/windFarmGen.html) (Ustawienia — Integracje i usługi). Ta aplikacja internetowa symuluje wiatraki.
  1. W polu **Parametry połączenia centrum zdarzeń** wklej parametry połączenia skopiowane w kroku 3.

      ![samouczek-siedem][7]

  1. Kliknij pozycję **Kliknij, aby rozpocząć**, aby wypchnąć zdarzenia do Twojego centrum zdarzeń. Na tym etapie plik o nazwie `instances.json` zostanie pobrany na komputer. Zapisz ten plik, ponieważ będzie on potrzebny później.

  1. Wróć do centrum zdarzeń. Powinno być teraz widoczne, że nowe zdarzenia są odbierane przez centrum hub.d.

     ![samouczek-osiem][8]

## <a name="analyze-data-in-your-environment"></a>Analizowanie danych w środowisku

W tej sekcji przeprowadzisz podstawową analizę danych szeregów czasowych przy użyciu eksploratora aktualizacji usługi Time Series Insights.

  1. Przejdź do eksploratora aktualizacji usługi Time Series Insights, klikając adres URL na stronie zasobu w witrynie Azure Portal.

      ![samouczek-dziewięć][9]

  1. W eksploratorze kliknij węzły **Unparented Instances** (Wystąpienia bez elementów nadrzędnych), aby wyświetlić wszystkie wystąpienia szeregów czasowych w środowisku.

     ![samouczek-dziesięć][10]

  1. W tym samouczku przeanalizujemy dane przesłane w ciągu ostatniego dnia. W tym celu kliknij pozycję **Szybkie czasy** i wybierz opcję **Ostatnie 24 godziny**.

     ![samouczek-jedenaście][11]

  1. Wybierz pozycję **Sensor_0** (Czujnik_0) i wybierz polecenie **Show Avg Value** (Pokaż wartość średnią), aby zwizualizować dane wysyłane z tego wystąpienia szeregu czasowego.

     ![samouczek-dwanaście][12]

  1. W podobny sposób można utworzyć wykresy danych pochodzących z innych wystąpień szeregów czasowych, aby przeprowadzić podstawową analizę.

     ![samouczek-trzynaście][13]

## <a name="define-a-type--hierarchy"></a>Definiowanie typu i hierarchii 

W tej sekcji utworzysz typ i hierarchię i skojarzysz je z wystąpieniami szeregów czasowych. Przeczytaj więcej na temat [modeli szeregów czasowych](./time-series-insights-update-tsm.md).

  1. W eksploratorze kliknij kartę **Model** na pasku aplikacji.

     ![samouczek-czternaście][14]

  1. W sekcji Typy kliknij pozycję **+ Dodaj**. Dzięki temu będzie można utworzyć nowy typ modelu szeregu czasowego.

     ![samouczek-piętnaście][15]

  1. W edytorze typu wypełnij pola **Nazwa** i **Opis** oraz utwórz zmienne dla wartości **Średnia**, **Minimum** i **Maksimum**, jak pokazano poniżej. Kliknij pozycję **Utwórz**, aby zapisać typ.

     ![samouczek-szesnaście][16]

     ![samouczek-siedemnaście][17]

  1. W sekcji **Hierarchie** kliknij pozycję **+ Dodaj**. Dzięki temu będzie można utworzyć nową hierarchię modelu szeregu czasowego.

     ![samouczek-osiemnaście][18]

  1. W edytorze hierarchii wypełnij pole **Nazwa** i dodaj poziomy hierarchii, jak pokazano poniżej. Kliknij pozycję **Utwórz**, aby zapisać hierarchię.

     ![samouczek-dziewiętnaście][19]

     ![samouczek-dwadzieścia][20]

  1. W sekcji **Wystąpienia** wybierz wystąpienie i kliknij pozycję **Edytuj**. Dzięki temu z tym wystąpieniem będzie można skojarzyć typ i hierarchię.

     ![samouczek-dwadzieścia-jeden][21]

  1. W edytorze wystąpienia wybierz typ i hierarchię zdefiniowane w krokach 3 i 5 powyżej, tak jak pokazano.

     ![samouczek-dwadzieścia-dwa][22]

  1. Alternatywnie, aby zrobić to dla wszystkich wystąpień jednocześnie, można edytować plik `instances.json`, który został pobrany wcześniej. W tym pliku zastąp wszystkie pola **typeId** i **hierarchyId** za pomocą identyfikatora uzyskanego w krokach 3 i 5 powyżej.

  1. W sekcji **Wystąpienia** kliknij pozycję **Przekaż kod JSON** i przekaż edytowany plik `instances.json`, jak pokazano poniżej.

     ![samouczek-dwadzieścia-trzy][23]

  1. Przejdź do karty **Analiza** i odśwież przeglądarkę. Powinny zostać wyświetlone wszystkie wystąpienia skojarzone z typem i hierarchią zdefiniowanymi powyżej.

     ![samouczek-dwadzieścia-cztery][24]

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:  

* Tworzenie środowiska usługi TSI (wersja zapoznawcza).
* Łączenie środowiska usługi TSI (wersja zapoznawcza) z centrum zdarzeń.
* Uruchamianie symulacji farmy wiatru w celu strumieniowego przesyłania danych do środowiska usługi TSI (wersja zapoznawcza).
* Wykonywanie podstawowej analizy danych.
* Definiowanie hierarchii i typu modelu szeregów czasowych oraz kojarzenie ich z wystąpieniami.

Teraz, gdy wiesz już, jak utworzyć własne środowisko aktualizacji usługi TSI, dowiedz się więcej o najważniejszych pojęciach dotyczących usługi TSI.

Przeczytaj o konfiguracji magazynu usługi TSI:

> [!div class="nextstepaction"]
> [Magazyn i ruch przychodzący usługi Azure TSI (wersja zapoznawcza)](./time-series-insights-update-storage-ingress.md)

Dowiedz się więcej o modelach szeregów czasowych:

> [!div class="nextstepaction"]
> [Modelowania danych w usłudze Azure TSI (wersja zapoznawcza)](./time-series-insights-update-tsm.md)

<!-- Images -->
[1]: media/v2-update-provision/tutorial-one.png
[2]: media/v2-update-provision/tutorial-two.png
[3]: media/v2-update-provision/tutorial-three.png
[4]: media/v2-update-provision/tutorial-four.png
[5]: media/v2-update-provision/tutorial-five.png
[6]: media/v2-update-provision/tutorial-six.png
[7]: media/v2-update-provision/tutorial-seven.png
[8]: media/v2-update-provision/tutorial-eight.png
[9]: media/v2-update-provision/tutorial-nine.png
[10]: media/v2-update-provision/tutorial-ten.png
[11]: media/v2-update-provision/tutorial-eleven.png
[12]: media/v2-update-provision/tutorial-twelve.png
[13]: media/v2-update-provision/tutorial-thirteen.png
[14]: media/v2-update-provision/tutorial-fourteen.png
[15]: media/v2-update-provision/tutorial-fifteen.png
[16]: media/v2-update-provision/tutorial-sixteen.png
[17]: media/v2-update-provision/tutorial-seventeen.png
[18]: media/v2-update-provision/tutorial-eighteen.png
[19]: media/v2-update-provision/tutorial-nineteen.png
[20]: media/v2-update-provision/tutorial-twenty.png
[21]: media/v2-update-provision/tutorial-twenty-one.png
[22]: media/v2-update-provision/tutorial-twenty-two.png
[23]: media/v2-update-provision/tutorial-twenty-three.png
[24]: media/v2-update-provision/tutorial-twenty-four.png