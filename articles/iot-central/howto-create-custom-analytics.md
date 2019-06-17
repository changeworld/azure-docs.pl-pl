---
title: Rozszerzanie usługi Azure IoT Central przy użyciu niestandardowych analytics | Dokumentacja firmy Microsoft
description: Jako deweloper rozwiązania należy skonfigurować aplikację IoT Central celu niestandardowe analizy i wizualizacje. To rozwiązanie korzysta z usługi Azure Databricks.
author: dominicbetts
ms.author: dobett
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: e039e2b8d9c183b5bfee1bee47e4addc4c873bf7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66743443"
---
# <a name="extend-azure-iot-central-with-custom-analytics"></a>Rozszerzanie usługi Azure IoT Central przy użyciu niestandardowych analizy

Ten poradnik pokazuje, deweloper rozwiązania, jak rozszerzyć aplikację IoT Central, za pomocą niestandardowych analizy i wizualizacji. W przykładzie użyto [usługi Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/) obszaru roboczego do analizowania strumień danych telemetrycznych IoT Central, jak i do wygenerowania wizualizacji, takich jak [polu powierzchni](https://wikipedia.org/wiki/Box_plot).

Ten poradnik pokazuje jak rozszerzyć IoT Central poza jej już czynności przy użyciu [narzędzia wbudowanym narzędziom analitycznym](howto-create-analytics.md).

W tym przewodniku dowiesz się, jak:

* Stream dane telemetryczne z usługi IoT Central aplikacji za pomocą *ciągły Eksport danych*.
* Tworzenie środowiska usługi Azure Databricks do analizowania i wykreślania danych telemetrycznych z urządzenia.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku z instrukcjami, konieczne jest aktywna subskrypcja platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

### <a name="iot-central-application"></a>Aplikacja IoT Central

Tworzenie aplikacji IoT Central [usługi Azure IoT Central — aplikacje](https://aka.ms/iotcentral) strony z następującymi ustawieniami:

| Ustawienie | Wartość |
| ------- | ----- |
| Plan płatności | Płatność zgodnie z rzeczywistym użyciem |
| Szablon aplikacji | Przykład dotyczący firmy Contoso |
| Nazwa aplikacji | Zaakceptuj wartości domyślne lub wybierz swoją własną nazwę |
| Adres URL | Zaakceptuj wartości domyślne lub wybierz własny unikatowy prefiks adresu URL |
| Katalog | Dzierżawy usługi Azure Active Directory |
| Subskrypcja platformy Azure | Twoja subskrypcja platformy Azure |
| Region | Wschodnie stany USA |

Przykłady i zrzuty ekranu, w tym artykule korzystają **wschodnie stany USA** regionu. Wybierz bliską lokalizację i upewnij się, że wszystkie zasoby zostały utworzone w tym samym regionie.

### <a name="resource-group"></a>Grupa zasobów

Użyj [witryny Azure portal, aby utworzyć grupę zasobów](https://portal.azure.com/#create/Microsoft.ResourceGroup) o nazwie **IoTCentralAnalysis** zawiera inne zasoby, które tworzysz. Tworzenie zasobów platformy Azure w tej samej lokalizacji co aplikację IoT Central.

### <a name="event-hubs-namespace"></a>Przestrzeń nazw usługi Event Hubs

Użyj [witryny Azure portal do utworzenia przestrzeni nazw usługi Event Hubs](https://portal.azure.com/#create/Microsoft.EventHub) z następującymi ustawieniami:

| Ustawienie | Wartość |
| ------- | ----- |
| Name (Nazwa)    | Wybierz nazwę przestrzeni nazw |
| Warstwa cenowa | Podstawowa |
| Subskrypcja | Twoja subskrypcja |
| Grupa zasobów | IoTCentralAnalysis |
| Lokalizacja | Wschodnie stany USA |
| Jednostki przepływności | 1 |

### <a name="azure-databricks-workspace"></a>Obszar roboczy usługi Azure Databricks

Użyj [witryny Azure portal do utworzenia usługi Azure Databricks](https://portal.azure.com/#create/Microsoft.Databricks) z następującymi ustawieniami:

| Ustawienie | Wartość |
| ------- | ----- |
| Nazwa obszaru roboczego    | Wybierz nazwę obszaru roboczego |
| Subskrypcja | Twoja subskrypcja |
| Grupa zasobów | IoTCentralAnalysis |
| Lokalizacja | Wschodnie stany USA |
| Warstwa cenowa | Standardowa (Standard) |

Po utworzeniu wymaganych zasobów usługi **IoTCentralAnalysis** grupy zasobów wygląda jak poniższy zrzut ekranu:

![Grupa zasobów analizy IoT Central](media/howto-create-custom-analytics/resource-group.png)

## <a name="create-an-event-hub"></a>Tworzenie centrum zdarzeń

Można skonfigurować aplikację IoT Central ciągły Eksport danych telemetrycznych do Centrum zdarzeń. W tej sekcji utworzysz Centrum zdarzeń w taki sposób, aby odbierać dane telemetryczne z aplikacji IoT Central. Centrum zdarzeń zapewnia dane telemetryczne do zadania usługi Stream Analytics do przetworzenia.

1. W witrynie Azure portal przejdź do przestrzeni nazw usługi Event Hubs, a następnie wybierz pozycję **+ Centrum zdarzeń**.
1. Nazwa Centrum zdarzeń **centralexport**i wybierz **Utwórz**.
1. Na liście centrów zdarzeń w przestrzeni nazw wybierz **centralexport**. Następnie wybierz **zasady dostępu współdzielonego**.
1. Wybierz pozycję **+ Dodaj**. Tworzenie zasad o nazwie **nasłuchiwania** z **nasłuchiwania** oświadczenia.
1. Gdy zasady są gotowe, wybierz z listy, a następnie skopiuj **parametry połączenia — klucz podstawowy** wartość.
1. Zanotuj te parametry połączenia, można jej użyć później podczas konfigurowania notesu usługi Databricks można odczytać z Centrum zdarzeń.

Przestrzeń nazw usługi Event Hubs będzie wyglądać jak poniższy zrzut ekranu:

![Przestrzeń nazw usługi Event Hubs](media/howto-create-custom-analytics/event-hubs-namespace.png)

## <a name="configure-export-in-iot-central"></a>Skonfiguruj Eksport w IoT Central

Przejdź do [aplikacji IoT Central](https://aka.ms/iotcentral) utworzonym na podstawie szablonu firmy Contoso. W tej sekcji skonfigurujesz aplikację do przesyłania danych telemetrycznych z jego symulowanych urządzeń do Centrum zdarzeń. Aby skonfigurować eksportu:

1. Przejdź do **ciągły Eksport danych** wybierz opcję **+ nowy**, a następnie **usługi Azure Event Hubs**.
1. Użyj następujących ustawień, aby skonfigurować eksportu, a następnie wybierz **Zapisz**:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa wyświetlana | Eksportowanie do usługi Event Hubs |
    | Enabled (Włączony) | Włączone |
    | Przestrzeń nazw usługi Event Hubs | Nazwa przestrzeni nazw usługi Event Hubs |
    | Centrum zdarzeń | centralexport |
    | Miary | Włączone |
    | Urządzenia | Wyłączone |
    | Szablony urządzeń | Wyłączone |

![Konfiguracji eksportu ciągłego danych](media/howto-create-custom-analytics/cde-configuration.png)

Zaczekaj, aż stan eksportu **systemem** przed kontynuowaniem.

## <a name="configure-databricks-workspace"></a>Konfigurowanie obszaru roboczego usługi Databricks

W witrynie Azure portal przejdź do usługi Azure Databricks, a następnie wybierz pozycję **Uruchom obszar roboczy**. Nowa karta zostanie otwarta w przeglądarce, a użytkownik loguje się do swojego obszaru roboczego.

### <a name="create-a-cluster"></a>Tworzenie klastra

Na **usługi Azure Databricks** stronie w obszarze lista typowych zadań, wybierz opcję **nowy klaster**.

Aby utworzyć klaster, użyj informacji w poniższej tabeli:

| Ustawienie | Wartość |
| ------- | ----- |
| Nazwa klastra | centralanalysis |
| Tryb klastra | Standardowa (Standard) |
| Wersja środowiska uruchomieniowego usługi Databricks | 5.3 (Scala 2.11, Spark 2.4.0) |
| Wersja języka Python | 3 |
| Włączanie skalowania automatycznego | Nie |
| Zakończ po minutach braku aktywności | 30 |
| Typ procesu roboczego | Standardowa_DS3_v2 |
| Procesy robocze | 1 |
| Typ sterownika | Takie same jak proces roboczy |

Tworzenie klastra może potrwać kilka minut, poczekaj na ukończenie przed kontynuowaniem tworzenia klastra.

### <a name="install-libraries"></a>Instalowanie bibliotek

Na **klastrów** strony, poczekaj, aż stan klastra jest **systemem**.

Poniższe kroki pokazują, jak importować biblioteki przykładu należy do klastra:

1. Na **klastrów** strony, poczekaj, aż stan **centralanalysis** interaktywne klaster jest **systemem**.

1. Wybierz klaster, a następnie wybierz **bibliotek** kartę.

1. Na **bibliotek** kartę, wybrać **zainstalować nowy**.

1. Na **zainstalować biblioteki** wybierz **Maven** jako źródło biblioteki.

1. W **służy do koordynowania** polu tekstowym wprowadź następującą wartość: `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`

1. Wybierz **zainstalować** Aby zainstalować bibliotekę w klastrze.

1. Stan biblioteki jest teraz **zainstalowane**:

    ![Zainstalowanie biblioteki](media/howto-create-custom-analytics/cluster-libraries.png)

### <a name="import-a-databricks-notebook"></a>Importowanie notesu usługi Databricks

Aby zaimportować notesu usługi Databricks, który zawiera kod języka Python, aby analizować i wizualizować dane telemetryczne IoT Central, wykonaj następujące kroki:

1. Przejdź do **obszaru roboczego** strony w Twoim środowisku usługi Databricks. Wybierz listę rozwijaną obok swojej nazwy konta, a następnie wybierz **importu**.

1. Wybierz zaimportować z adresu URL i wprowadź następujący adres: [https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true](https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true)

1. Aby zaimportować Notes, wybierz opcję **zaimportować**.

1. Wybierz **obszaru roboczego** Aby wyświetlić zaimportowane Notes:

    ![Zaimportowane notesu](media/howto-create-custom-analytics/import-notebook.png)

1. Edytowanie kodu w pierwszej komórki języka Python, aby dodać parametry połączenia usługi Event Hubs, które zostały zapisane wcześniej:

    ```python
    from pyspark.sql.functions import *
    from pyspark.sql.types import *

    ###### Event Hub Connection strings ######
    telementryEventHubConfig = {
      'eventhubs.connectionString' : '{your Event Hubs connection string}'
    }
    ```

## <a name="run-analysis"></a>Przeprowadzanie analizy

Aby uruchomić analizę, należy dołączyć Notes do klastra:

1. Wybierz **Detached** , a następnie wybierz **centralanalysis** klastra.
1. Jeśli klaster nie jest uruchomiona, uruchom ją.
1. Aby uruchomić Notes, kliknij przycisk Uruchom.

Może zostać wyświetlony błąd w ostatniej komórki. Jeśli tak, sprawdź, czy poprzedniej komórki są uruchomione, zaczekaj chwilę niektóre dane są zapisywane do magazynu i ponownie uruchom ostatniej komórki.

### <a name="view-smoothed-data"></a>Dane widoku wygładzone

W notesie przewiń w dół do komórki 14, aby wyświetlić wykres średnia wilgotność stopniowe według typu urządzenia. Ten wykres stale aktualizuje jako nadejściu przesyłania strumieniowego danych telemetrycznych:

![Wygładzone kreślenia telemetrii](media/howto-create-custom-analytics/telemetry-plot.png)

Można zmienić rozmiar wykresu w notesie.

### <a name="view-box-plots"></a>Okno widoku wykresy

W notesie, przewiń w dół do komórki 20, aby zobaczyć [polu powierzchni](https://en.wikipedia.org/wiki/Box_plot). Powierzchnie pola są oparte na danych statycznych, aby je zaktualizować należy ponownie uruchomić komórkę:

![Pola geograficzne](media/howto-create-custom-analytics/box-plots.png)

Można zmienić rozmiar powierzchni w notesie.

## <a name="tidy-up"></a>Uporządkować dane

Aby uporządkować dane po instruktaż i uniknąć niepotrzebnych kosztów, Usuń **IoTCentralAnalysis** grupy zasobów w witrynie Azure portal.

Możesz usunąć aplikację IoT Central **zarządzania** strony w aplikacji.

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku przedstawiono sposób:

* Stream dane telemetryczne z usługi IoT Central aplikacji za pomocą *ciągły Eksport danych*.
* Tworzenie środowiska usługi Azure Databricks do analizowania i wykreślania dane telemetryczne.

Skoro już wiesz, jak utworzyć niestandardowe analizy, sugerowane następnym krokiem jest Dowiedz się, jak [Wizualizuj i Analizuj dane usługi Azure IoT Central, na pulpicie nawigacyjnym usługi Power BI](howto-connect-powerbi.md).
