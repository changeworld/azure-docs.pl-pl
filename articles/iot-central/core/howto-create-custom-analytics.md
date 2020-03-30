---
title: Rozszerzanie usługi Azure IoT Central o analizę niestandardową | Dokumenty firmy Microsoft
description: Jako deweloper rozwiązania skonfiguruj aplikację IoT Central, aby wykonywać niestandardowe analizy i wizualizacje. To rozwiązanie używa usługi Azure Databricks.
author: dominicbetts
ms.author: dobett
ms.date: 12/02/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 7c2c14a937b4ef55d0e5f71e7b20214428ecd68c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158201"
---
# <a name="extend-azure-iot-central-with-custom-analytics-using-azure-databricks"></a>Rozszerzanie usługi Azure IoT Central o niestandardowe analizy przy użyciu usługi Azure Databricks

W tym przewodniku przedstawiono, jako deweloper rozwiązania, jak rozszerzyć aplikację IoT Central o niestandardowe analizy i wizualizacje. W przykładzie użyto obszaru roboczego [usługi Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/) do analizowania strumienia telemetrii Centralnej IoT i generowania wizualizacji, takich jak [wykresy pól.](https://wikipedia.org/wiki/Box_plot)

Ten poradnik pokazuje, jak rozszerzyć IoT Central poza to, co może już zrobić z [wbudowanymi narzędziami analitycznymi.](./howto-create-custom-analytics.md)

W tym przewodniku instrukcja, dowiedzieć się, jak:

* Przesyłaj dane telemetryczne z aplikacji IoT Central przy użyciu *ciągłego eksportowania danych*.
* Tworzenie środowiska usługi Azure Databricks do analizowania i drukowania danych telemetrycznych urządzenia.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku, potrzebujesz aktywnej subskrypcji platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

### <a name="iot-central-application"></a>Aplikacja IoT Central

Utwórz aplikację IoT Central w witrynie sieci Web [menedżera aplikacji Usługi Azure IoT Central](https://aka.ms/iotcentral) z następującymi ustawieniami:

| Ustawienie | Wartość |
| ------- | ----- |
| Plan cenowy | Standardowa |
| Szablon aplikacji | Analityka w sklepie – monitorowanie stanu |
| Nazwa aplikacji | Zaakceptuj wartość domyślną lub wybierz własną nazwę |
| Adres URL | Zaakceptuj domyślny lub wybierz własny unikatowy prefiks adresu URL |
| Katalog | Dzierżawa usługi Azure Active Directory |
| Subskrypcja platformy Azure | Twoja subskrypcja platformy Azure |
| Region | Twój najbliższy region |

Przykłady i zrzuty ekranu w tym artykule używają regionu **Stany Zjednoczone.** Wybierz lokalizację blisko siebie i upewnij się, że wszystkie zasoby są tworzęne w tym samym regionie.

Ten szablon aplikacji zawiera dwa symulowane urządzenia termostatu, które wysyłają dane telemetryczne.

### <a name="resource-group"></a>Grupa zasobów

Użyj [witryny Azure Portal, aby utworzyć grupę zasobów](https://portal.azure.com/#create/Microsoft.ResourceGroup) o nazwie **IoTCentralAnalysis,** aby zawierać inne zasoby, które tworzysz. Utwórz zasoby platformy Azure w tej samej lokalizacji co aplikacja IoT Central.

### <a name="event-hubs-namespace"></a>Przestrzeń nazw usługi Event Hubs

Użyj [witryny Azure Portal, aby utworzyć obszar nazw Centrum zdarzeń](https://portal.azure.com/#create/Microsoft.EventHub) z następującymi ustawieniami:

| Ustawienie | Wartość |
| ------- | ----- |
| Nazwa    | Wybierz nazwę obszaru nazw |
| Warstwa cenowa | Podstawowa (Basic) |
| Subskrypcja | Twoja subskrypcja |
| Grupa zasobów | IoTCentralAnaliza |
| Lokalizacja | Wschodnie stany USA |
| Jednostki przepływności | 1 |

### <a name="azure-databricks-workspace"></a>Obszar roboczy usługi Azure Databricks

Użyj [witryny Azure portal, aby utworzyć usługę Azure Databricks](https://portal.azure.com/#create/Microsoft.Databricks) z następującymi ustawieniami:

| Ustawienie | Wartość |
| ------- | ----- |
| Nazwa obszaru roboczego    | Wybierz nazwę obszaru roboczego |
| Subskrypcja | Twoja subskrypcja |
| Grupa zasobów | IoTCentralAnaliza |
| Lokalizacja | Wschodnie stany USA |
| Warstwa cenowa | Standardowa |

Po utworzeniu wymaganych zasobów grupa zasobów **IoTCentralAnalysis** wygląda następująco:

![Grupa zasobów analizy Centralnej IoT](media/howto-create-custom-analytics/resource-group.png)

## <a name="create-an-event-hub"></a>Tworzenie centrum zdarzeń

Aplikację IoT Central można skonfigurować do ciągłego eksportowania danych telemetrycznych do centrum zdarzeń. W tej sekcji utworzysz Centrum zdarzeń, aby odbierać dane telemetryczne z aplikacji IoT Central. Centrum zdarzeń dostarcza dane telemetryczne do zadania usługi Stream Analytics do przetwarzania.

1. W witrynie Azure portal przejdź do obszaru nazw Centrum zdarzeń i wybierz **+ Centrum zdarzeń**.
1. Nazwij centrum zdarzeń **centralexport**i wybierz pozycję **Utwórz**.
1. Na liście centrów zdarzeń w obszarze nazw wybierz **opcję centralexport**. Następnie wybierz pozycję **Zasady dostępu współdzielonego**.
1. Wybierz pozycję **+ Dodaj**. Utwórz zasadę o nazwie **Nasłuchiwanie** za pomocą oświadczenia **nasłuchiwanie.**
1. Gdy zasada jest gotowa, zaznacz ją na liście, a następnie skopiuj wartość **klucza podstawowego ciągu połączenia.**
1. Zanotuj ten ciąg połączenia, użyj go później podczas konfigurowania notesu Databricks do odczytu z centrum zdarzeń.

Obszar nazw centrum zdarzeń wygląda następująco:

![Przestrzeń nazw usługi Event Hubs](media/howto-create-custom-analytics/event-hubs-namespace.png)

## <a name="configure-export-in-iot-central"></a>Konfigurowanie eksportu w centrum IoT

W witrynie sieci Web [menedżera aplikacji usługi Azure IoT Central](https://aka.ms/iotcentral) przejdź do aplikacji IoT Central utworzonej na podstawie szablonu Contoso. W tej sekcji można skonfigurować aplikację do przesyłania strumieniowego danych telemetrycznych z symulowanych urządzeń do centrum zdarzeń. Aby skonfigurować eksport:

1. Przejdź do strony **Eksportowanie danych,** wybierz pozycję **+ Nowy**, a następnie **centrum zdarzeń platformy Azure**.
1. Użyj następujących ustawień, aby skonfigurować eksport, a następnie wybierz pozycję **Zapisz:**

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa wyświetlana | Eksportowanie do centrów zdarzeń |
    | Enabled (Włączony) | Włączone |
    | Przestrzeń nazw usługi Event Hubs | Nazwa obszaru nazw Centrów zdarzeń |
    | Centrum zdarzeń | centralexport |
    | Miary | Włączone |
    | Urządzenia | Wyłączone |
    | Szablony urządzeń | Wyłączone |

![Konfiguracja eksportu danych](media/howto-create-custom-analytics/cde-configuration.png)

Przed kontynuowaniem poczekaj, aż stan eksportu **zostanie uruchomiony.**

## <a name="configure-databricks-workspace"></a>Konfigurowanie obszaru roboczego Databricks

W witrynie Azure portal przejdź do usługi Azure Databricks i wybierz pozycję **Uruchom obszar roboczy**. W przeglądarce zostanie otwarta nowa karta, która zaloguje Cię do obszaru roboczego.

### <a name="create-a-cluster"></a>Tworzenie klastra

Na stronie **Azure Databricks,** na liście typowych zadań, wybierz pozycję **Nowy klaster**.

Aby utworzyć klaster, użyj informacji z poniższej tabeli:

| Ustawienie | Wartość |
| ------- | ----- |
| Nazwa klastra | centralanaliza |
| Tryb klastra | Standardowa |
| Databricks Wersja środowiska wykonawczego | 5,5 LTS (Scala 2.11, Iskra 2.4.3) |
| Wersja Pythona | 3 |
| Włączanie skalowania automatycznego | Nie |
| Zakończenie po kilku minutach bezczynności | 30 |
| Typ pracownika | Standardowa_DS3_v2 |
| Pracowników | 1 |
| Typ sterownika | Tak samo jak pracownik |

Tworzenie klastra może potrwać kilka minut, poczekaj na ukończenie tworzenia klastra przed kontynuowaniem.

### <a name="install-libraries"></a>Instalowanie bibliotek

Na stronie **Klastry** poczekaj, aż stan klastra zostanie **uruchomiony**.

W poniższych krokach pokazano, jak zaimportować bibliotekę, której przykładowe potrzeby potrzebuje do klastra:

1. Na stronie **Klastry** poczekaj, aż stan klastra interaktywnego **centralanalizy** zostanie **uruchomiony**.

1. Wybierz klaster, a następnie wybierz kartę **Biblioteki.**

1. Na karcie **Biblioteki** wybierz pozycję **Zainstaluj nowy**.

1. Na stronie **Zainstaluj bibliotekę** wybierz pozycję **Maven** jako źródło biblioteki.

1. W polu **tekstowym Współrzędne** wprowadź następującą wartość:`com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`

1. Wybierz **pozycję Zainstaluj,** aby zainstalować bibliotekę w klastrze.

1. Stan biblioteki jest teraz **zainstalowany:**

    ![Zainstalowana biblioteka](media/howto-create-custom-analytics/cluster-libraries.png)

### <a name="import-a-databricks-notebook"></a>Importowanie notesu databricks

Aby zaimportować notes Databricks zawierający kod języka Python, należy wykonać następujące kroki w celu przeanalizowania i wizualizacji danych telemetrycznych i wizualizowanych centrum IoT:

1. Przejdź do strony **Obszar roboczy** w środowisku Databricks. Wybierz pozycję rozwijanej obok nazwy konta, a następnie wybierz pozycję **Importuj**.

1. Wybierz, aby zaimportować z adresu URL i wprowadź następujący adres:[https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true](https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true)

1. Aby zaimportować notes, wybierz polecenie **Importuj**.

1. Wybierz **obszar roboczy,** aby wyświetlić zaimportowany notes:

    ![Zaimportowany notes](media/howto-create-custom-analytics/import-notebook.png)

1. Edytuj kod w pierwszej komórce języka Python, aby dodać wcześniej zapisany ciąg połączenia Usługi event hubów:

    ```python
    from pyspark.sql.functions import *
    from pyspark.sql.types import *

    ###### Event Hub Connection strings ######
    telementryEventHubConfig = {
      'eventhubs.connectionString' : '{your Event Hubs connection string}'
    }
    ```

## <a name="run-analysis"></a>Uruchom analizę

Aby uruchomić analizę, należy dołączyć notes do klastra:

1. Wybierz **pozycję Odłączony,** a następnie wybierz klaster **centralanalizy.**
1. Jeśli klaster nie jest uruchomiony, uruchom go.
1. Aby uruchomić notes, wybierz przycisk uruchom.

W ostatniej komórce może pojawić się błąd. Jeśli tak, sprawdź, czy poprzednie komórki są uruchomione, poczekaj minutę, aż niektóre dane mają zostać zapisane w magazynie, a następnie uruchom ponownie ostatnią komórkę.

### <a name="view-smoothed-data"></a>Wyświetlanie wygładzonych danych

W notesie przewiń w dół do komórki 14, aby wyświetlić wykres średniej wilgotności stopniowej według typu urządzenia. Ten wykres jest stale aktualizowany w miarę nadejścia danych telemetrycznych przesyłania strumieniowego:

![Wygładzony wykres telemetryczny](media/howto-create-custom-analytics/telemetry-plot.png)

Rozmiar wykresu można zmienić w notesie.

### <a name="view-box-plots"></a>Wyświetlanie wykresów pól

W notesie przewiń w dół do komórki 20, aby wyświetlić [wykresy pól](https://en.wikipedia.org/wiki/Box_plot). Wykresy pól są oparte na danych statycznych, więc aby je zaktualizować, należy ponownie uruchomić komórkę:

![Wykresy skrzynkowe](media/howto-create-custom-analytics/box-plots.png)

Można zmienić rozmiar wykresów w notesie.

## <a name="tidy-up"></a>Posprząt

Aby uporządkować po tym instrukcje i uniknąć niepotrzebnych kosztów, usuń **IoTCentralAnalysis** grupy zasobów w witrynie Azure portal.

Aplikację IoT Central można usunąć ze strony **Zarządzanie** w aplikacji.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku instrukcja, dowiedziałeś się, jak:

* Przesyłaj dane telemetryczne z aplikacji IoT Central przy użyciu *ciągłego eksportowania danych*.
* Tworzenie środowiska usługi Azure Databricks do analizowania i drukowania danych telemetrycznych.

Teraz, gdy wiesz, jak tworzyć niestandardowe analizy, sugerowanym następnym krokiem jest nauczenie się [zarządzania aplikacją](howto-administer.md).
