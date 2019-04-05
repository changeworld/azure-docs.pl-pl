---
title: Wizualizuj dane z Eksploratora danych Azure przy użyciu narzędzia Grafana
description: W tym instruktażu dowiesz się, jak skonfigurować Eksplorator danych platformy Azure jako źródło danych dla platformy Grafana, a następnie wizualizować dane z klastra próbki.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/05/2018
ms.openlocfilehash: 188cb310cfc13fe2fc41ba3e01deb01068c0184d
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2019
ms.locfileid: "59048320"
---
# <a name="visualize-data-from-azure-data-explorer-in-grafana"></a>Wizualizuj dane z Eksploratora danych platformy Azure w narzędzia Grafana

Grafana to platforma analityczna, która pozwala na zapytania i wizualizowanie danych, a następnie tworzyć i udostępniać pulpity nawigacyjne oparte na wizualizacji. Grafana zapewnia ono Eksploratora danych usługi Azure *wtyczki*, co umożliwia nawiązywanie połączenia i wizualizuj dane z Eksploratora danych usługi Azure. W tym artykule dowiesz się, jak skonfigurować Eksplorator danych platformy Azure jako źródło danych dla platformy Grafana, a następnie wizualizować dane z klastra próbki.

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebne są następujące polecenie, aby ukończyć ten sposób:

* [Grafana wersji 5.3.0 lub nowszej](https://docs.grafana.org/installation/) systemu operacyjnego

* [Wtyczki Eksploratora danych usługi Azure](https://grafana.com/plugins/grafana-azure-data-explorer-datasource/installation) dla narzędzia Grafana

* Klastra, który zawiera StormEvents przykładowych danych. Aby uzyskać więcej informacji, zobacz [Szybki Start: Tworzenie klastra Eksplorator danych platformy Azure i bazy danych](create-cluster-database-portal.md) i [pozyskiwanie danych przykładowych do Eksploratora danych usługi Azure](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="configure-the-data-source"></a>Konfigurowanie źródła danych

Możesz wykonać następujące kroki, aby skonfigurować Eksplorator danych platformy Azure jako źródło danych dla narzędzia Grafana. Omówimy następujące kroki, które bardziej szczegółowo w tej sekcji:

1. Tworzenie usługi Azure Active Directory (Azure AD) jednostki usługi. Nazwa główna usługi jest używana przez narzędzia Grafana dostęp do usługi Azure Eksploratora danych.

1. Dodaj nazwę główną usługi Azure AD do *osoby przeglądające* roli w bazie danych Azure Eksploratora danych.

1. Określ właściwości połączenia narzędzia Grafana na podstawie informacji z jednostki usługi Azure AD, a następnie przetestuj połączenie.

### <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

Można utworzyć jednostki w usłudze [witryny Azure portal](#azure-portal) lub za pomocą [wiersza polecenia platformy Azure](#azure-cli) środowisko wiersza polecenia. Niezależnie od tego, która metoda użyjesz, po utworzeniu, którego pobieranie wartości do czterech właściwości połączenia, których można używać w kolejnych krokach.

#### <a name="azure-portal"></a>Azure Portal

1. Aby utworzyć jednostkę usługi, postępuj zgodnie z instrukcjami [dokumentacja witryny Azure portal](/azure/active-directory/develop/howto-create-service-principal-portal).

    1. W [przypisywanie aplikacji do roli](/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) sekcji, należy przypisać rolę rodzaj **czytnika** do klastra, Eksplorator danych usługi Azure.

    1. W [pobieranie wartości do logowania](/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) sekcji, skopiuj wartości właściwości trzy omówione w krokach: **Identyfikator katalogu** (identyfikator dzierżawy), **identyfikator aplikacji**, i **hasło**.

1. W witrynie Azure portal wybierz **subskrypcje** następnie skopiuj identyfikator subskrypcji, w którym utworzono nazwę główną usługi.

    ![Identyfikator subskrypcji — portal](media/grafana/subscription-id-portal.png)

#### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

1. Tworzenie jednostki usługi. Ustaw odpowiedni zakres i Typ roli `reader`.

    ```azurecli
    az ad sp create-for-rbac --name "https://{UrlToYourGrafana}:{PortNumber}" --role "reader" \
                             --scopes /subscriptions/{SubID}/resourceGroups/{ResourceGroupName}
    ```

    Aby uzyskać więcej informacji, zobacz [Tworzenie jednostki usługi platformy Azure przy użyciu wiersza polecenia platformy Azure](/cli/azure/create-an-azure-service-principal-azure-cli).

1. Polecenie zwraca zestaw podobne do następujących wyników. Skopiuj wartości z trzech właściwości: **appID**, **hasło**, i **dzierżawy**.

    ```json
    {
      "appId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "displayName": "{UrlToYourGrafana}:{PortNumber}",
      "name": "https://{UrlToYourGrafana}:{PortNumber}",
      "password": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
    }
    ```

1. Zostanie wyświetlona lista subskrypcji.

    ```azurecli
    az account list --output table
    ```

    Skopiuj identyfikator odpowiednią subskrypcję.

    ![Identyfikator subskrypcji — interfejs wiersza polecenia](media/grafana/subscription-id-cli.png)

### <a name="add-the-service-principal-to-the-viewers-role"></a>Dodaj nazwę główną usługi do roli osoby przeglądające

Teraz, gdy masz nazwę główną usługi, możesz dodać go do *osoby przeglądające* roli w bazie danych Azure Eksploratora danych. Można wykonać tego zadania w obszarze **uprawnienia** w witrynie Azure portal lub w obszarze **zapytania** za pomocą polecenia zarządzania.

#### <a name="azure-portal---permissions"></a>Witryna Azure portal — uprawnienia

1. W witrynie Azure portal przejdź do klastra, Eksplorator danych usługi Azure.

1. W **Przegląd** sekcji, wybierz bazę danych z przykładowymi danymi StormEvents.

    ![Wybierz bazę danych](media/grafana/select-database.png)

1. Wybierz **uprawnienia** następnie **Dodaj**.

    ![Uprawnienia bazy danych](media/grafana/database-permissions.png)

1. W obszarze **Dodaj uprawnienia bazy danych**, wybierz opcję **podglądu** następnie rola **wybierz jednostki**.

    ![Dodaj uprawnienia bazy danych](media/grafana/add-permission.png)

1. Wyszukaj utworzonej jednostki usługi, (w przykładzie pokazano podmiot zabezpieczeń **mb grafana**). Wybierz jednostki, a następnie **wybierz**.

    ![Zarządzanie uprawnieniami w witrynie Azure portal](media/grafana/new-principals.png)

1. Wybierz pozycję **Zapisz**.

    ![Zarządzanie uprawnieniami w witrynie Azure portal](media/grafana/save-permission.png)

#### <a name="management-command---query"></a>Polecenia zarządzania — zapytania

1. W witrynie Azure portal przejdź do klastra usługi Azure Eksploratora danych i wybierz **zapytania**.

    ![Zapytanie](media/grafana/query.png)

1. Uruchom następujące polecenie w oknie zapytania. Użyj Identyfikatora aplikacji i identyfikator dzierżawy z witryny Azure portal lub interfejsu wiersza polecenia.

    ```kusto
    .add database {TestDatabase} viewers ('aadapp={ApplicationID};{TenantID}')
    ```

    Polecenie zwraca zestaw podobne do następujących wyników. W tym przykładzie pierwszy wiersz jest wierszem dla istniejącego użytkownika w bazie danych, a drugi wiersz jest dla jednostki usługi, który właśnie został dodany.

    ![Zestaw wyników](media/grafana/result-set.png)

### <a name="specify-properties-and-test-the-connection"></a>Określ właściwości i przetestuj połączenie

Przy użyciu jednostki usługi, przypisany do *osoby przeglądające* roli, możesz teraz określić właściwości w ramach wystąpienia platformy Grafana i przetestować połączenie do Eksploratora danych platformy Azure.

1. W Grafana, w menu po lewej stronie wybierz ikonę koła zębatego następnie **źródeł danych**.

    ![Źródła danych](media/grafana/data-sources.png)

1. Wybierz **Dodaj źródło danych**.

1. Na **źródeł danych / nowy** strony, wprowadź nazwę dla źródła danych, a następnie wybierz typ **Datasource Eksploratora danych usługi Azure**.

    ![Nazwa połączenia i typ](media/grafana/connection-name-type.png)

1. Wprowadź nazwę klastra, w https://{ClusterName formularza}. {Region}. kusto.windows.net. Wprowadź inne wartości z witryny Azure portal lub interfejsu wiersza polecenia. Zobacz tabelę poniżej poniższej ilustracji dla mapowania.

    ![Connection properties (Właściwości połączenia)](media/grafana/connection-properties.png)

    | Grafana UI | Azure Portal | Interfejs wiersza polecenia platformy Azure |
    | --- | --- | --- |
    | Identyfikator subskrypcji | IDENTYFIKATOR SUBSKRYPCJI | SubscriptionId |
    | Identyfikator dzierżawy | Identyfikator katalogu | dzierżawa |
    | Identyfikator klienta | Identyfikator aplikacji | appId |
    | Klucz tajny klienta | Hasło | hasło |
    | | | |

1. Wybierz **Zapisz i przetestuj**.

    Jeśli test wypadnie pomyślnie, przejdź do następnej sekcji. Jeśli napotkasz jakiekolwiek problemy, sprawdź wartości, które określiłeś w Grafana i przejrzyj poprzednie kroki.

## <a name="visualize-data"></a>Wizualizowanie danych

Po zakończeniu konfigurowania Eksplorator danych platformy Azure jako źródło danych dla narzędzia Grafana, nadszedł czas na wizualizacji danych. Przedstawimy przykład podstawowy, ale można zrobić dużo więcej. Zalecamy przyjrzenie [Pisanie zapytań w Eksploratorze danych platformy Azure](write-queries.md) przykłady innych zapytań w celu uruchomienia zestawu danych przykładowych.

1. W Grafana, w menu po lewej stronie wybierz ikonę znaku plus następnie **pulpit nawigacyjny**.

    ![Tworzenie pulpitu nawigacyjnego](media/grafana/create-dashboard.png)

1. W obszarze **Dodaj** zaznacz **wykresu**.

    ![Dodaj wykres](media/grafana/add-graph.png)

1. Na panelu wykresu wybierz **tytuł panelu** następnie **Edytuj**.

    ![Edytuj panelu](media/grafana/edit-panel.png)

1. W dolnej części panelu wybierz **źródła danych** następnie wybierz źródło danych, który został skonfigurowany.

    ![Wybieranie źródła danych](media/grafana/select-data-source.png)

1. W okienku zapytania, skopiuj następujące zapytanie, a następnie wybierz pozycję **Uruchom**. Zapytanie przedziałów liczbę zdarzeń wg dnia dla zestawu danych przykładowych.

    ```kusto
    StormEvents
    | summarize event_count=count() by bin(StartTime, 1d)
    ```

    ![Uruchamianie zapytania](media/grafana/run-query.png)

1. Wykres nie pokazuje żadnych wyników, ponieważ jej obejmuje domyślnie dane z ostatnich sześciu godzin. W górnym menu wybierz **ostatnie 6 godzin**.

    ![Ostatnie 6 godzin](media/grafana/last-six-hours.png)

1. Określ zakres niestandardowych, który obejmuje 2007 roku zawarte w naszym zestawie danych przykładowych StormEvents. Wybierz przycisk **Zastosuj**.

    ![Niestandardowy zakres dat](media/grafana/custom-date-range.png)

    Teraz wykres przedstawia dane z 2007 zasobnikach dzienny.

    ![Zakończono wykresu](media/grafana/finished-graph.png)

1. W górnym menu, wybierz opcję Zapisz ikony: ![Ikona zapisywania](media/grafana/save-icon.png).

## <a name="next-steps"></a>Kolejne kroki

[Pisanie zapytań w Eksploratorze danych platformy Azure](write-queries.md)

[Samouczek: Wizualizuj dane z Eksploratora danych usługi Azure w usłudze Power BI](visualize-power-bi.md)