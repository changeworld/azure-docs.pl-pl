---
title: Wizualizuj dane z usługi Azure Eksplorator danych przy użyciu Kibana
description: W tym artykule dowiesz się, jak skonfigurować Eksplorator danych platformy Azure jako źródło danych dla Kibana
author: orspod
ms.author: orspodek
ms.reviewer: guregini
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 30d74f36c6462d1fba039595d2ed6fe722b742e8
ms.sourcegitcommit: d322d0a9d9479dbd473eae239c43707ac2c77a77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2020
ms.locfileid: "79164815"
---
# <a name="visualize-data-from-azure-data-explorer-in-kibana-with-the-k2bridge-open-source-connector"></a>Wizualizuj dane z usługi Azure Eksplorator danych w Kibana za pomocą łącznika programu K2Bridge Open Source

K2Bridge (Kibana-Kusto Bridge) umożliwia korzystanie z platformy Azure Eksplorator danych jako źródła danych i wizualizację tych danych w Kibana. K2Bridge to aplikacja kontenera typu [Open Source](https://github.com/microsoft/K2Bridge) , która działa jako serwer proxy między wystąpieniem Kibana i klastrem Eksplorator danych platformy Azure. W tym artykule opisano sposób tworzenia tego połączenia przy użyciu programu K2Bridge.

K2Bridge tłumaczy zapytania Kibana do języka zapytań Kusto (KQL) i wysyła wyniki Eksplorator danych platformy Azure z powrotem do Kibana. 

   ![wykres](media/k2bridge/k2bridge-chart.png)

K2Bridge obsługuje kartę odnajdywania Kibana, w której można:
* Wyszukiwanie i Eksplorowanie danych
* Filtrowanie wyników
* Dodawanie lub usuwanie pól w siatce wyników
* Wyświetl zawartość rekordu
* Zapisz i Udostępnij wyszukiwania

Na poniższej ilustracji przedstawiono wystąpienie Kibana powiązane z platformą Azure Eksplorator danych przez K2Bridge. Środowisko użytkownika w Kibana nie jest zmieniane.

   [![Strona Kibana](media/k2bridge/k2bridge-kibana-page.png)](media/k2bridge/k2bridge-kibana-page.png#lightbox)

## <a name="prerequisites"></a>Wymagania wstępne

Aby móc wizualizować dane z usługi Azure Eksplorator danych w Kibana, przygotuj następujące elementy:

* [Helm v3](https://github.com/helm/helm#install), Menedżer pakietów Kubernetes
* Klaster usługi Azure Kubernetes Service (AKS) lub inny klaster Kubernetes (wersja 1,14 do wersji 1,16 zostały przetestowane i zweryfikowane). Jeśli potrzebujesz klastra AKS, zobacz Wdrażanie klastra AKS [przy użyciu interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough) lub [Korzystanie z Azure Portal](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal)
* [Klaster Eksplorator danych platformy Azure](create-cluster-database-portal.md), w tym:
    * Adres URL klastra usługi Azure Eksplorator danych 
    * Nazwa bazy danych
    
* Jednostka usługi Azure AD uprawniona do wyświetlania danych w usłudze Azure Eksplorator danych, w tym:
    * Identyfikator klienta 
    * Wpis tajny klienta

    Zalecana jest jednostka usługi z uprawnieniem "Podgląd". Nie zaleca się używania wyższych uprawnień.

    * [Ustaw uprawnienia do wyświetlania klastra dla jednostki usługi Azure AD](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions#manage-permissions-in-the-azure-portal).

    Aby uzyskać więcej informacji na temat nazwy głównej usługi Azure AD, zobacz [Tworzenie jednostki usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application).

## <a name="run-k2bridge-on-azure-kubernetes-service-aks"></a>Uruchom K2Bridge w usłudze Azure Kubernetes Service (AKS)

Domyślnie wykres K2Bridges's Helm odwołuje się do publicznie dostępnego obrazu znajdującego się w Container Registry firmy Microsoft (MCR). MCR nie wymaga żadnych poświadczeń ani nie działa.

1. Pobierz wymagane wykresy Helm.

1. Dodaj zależność Elasticsearch do Helm. 
    Przyczyna zależności Elasticsearch polega na tym, że K2Bridge używa wewnętrznego małego wystąpienia Elasticsearch do obsługi żądań metadanych (takich jak wzorce indeksów i zapisane zapytania). Żadne dane biznesowe nie są zapisywane w tym wystąpieniu wewnętrznym i można je traktować jako szczegóły implementacji. 

    1. Aby dodać zależność Elasticsearch do Helm:

        ```bash
        helm repo add elastic https://helm.elastic.co
        helm repo update
        ```

    1. Aby uzyskać wykres K2Bridge z katalogu wykresów repozytorium GitHub:
        1. Sklonuj repozytorium z usługi [GitHub](https://github.com/microsoft/K2Bridge).
        1. Przejdź do katalogu głównego repozytorium K2Bridges.
        1. Uruchom:

            ```bash
            helm dependency update charts/k2bridge
            ```

1. Wdróż K2Bridge:

    1. Ustaw zmienne z prawidłowymi wartościami dla danego środowiska:

        ```bash
        ADX_URL=[YOUR_ADX_CLUSTER_URL] #For example, https://mycluster.westeurope.kusto.windows.net
        ADX_DATABASE=[YOUR_ADX_DATABASE_NAME]
        ADX_CLIENT_ID=[SERVICE_PRINCIPAL_CLIENT_ID]
        ADX_CLIENT_SECRET=[SERVICE_PRINCIPAL_CLIENT_SECRET]
        ADX_TENANT_ID=[SERVICE_PRINCIPAL_TENANT_ID]
        ```

    1. Obowiązkowe Włącz telemetrię Application Insights platformy Azure. 
        Jeśli używasz usługi Azure Application Insights po raz pierwszy, należy najpierw [utworzyć zasób Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource). Należy [skopiować klucz Instrumentacji](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#copy-the-instrumentation-key) do zmiennej: 

        ```bash
        APPLICATION_INSIGHTS_KEY=[INSTRUMENTATION_KEY]
        COLLECT_TELEMETRY=true
        ```

    1. <a name="install-k2bridge-chart"></a>Zainstaluj wykres K2Bridge:

        ```bash
        helm install k2bridge charts/k2bridge -n k2bridge --set image.repository=$REPOSITORY_NAME/$CONTAINER_NAME --set settings.adxClusterUrl="$ADX_URL" --set settings.adxDefaultDatabaseName="$ADX_DATABASE" --set settings.aadClientId="$ADX_CLIENT_ID" --set settings.aadClientSecret="$ADX_CLIENT_SECRET" --set settings.aadTenantId="$ADX_TENANT_ID" [--set image.tag=latest] [--set privateRegistry="$IMAGE_PULL_SECRET_NAME"] [--set settings.collectTelemetry=$COLLECT_TELEMETRY]
        ```

        W obszarze [Konfiguracja](https://github.com/microsoft/K2Bridge/blob/master/docs/configuration.md) można znaleźć pełny zestaw opcji konfiguracji.

    1. W danych wyjściowych polecenia zostanie zaproponowane następne polecenie Helm do wdrożenia Kibana. Opcjonalnie Uruchom polecenie:

        ```bash
        helm install kibana elastic/kibana -n k2bridge --set image=docker.elastic.co/kibana/kibana-oss --set imageTag=6.8.5 --set elasticsearchHosts=http://k2bridge:8080
        ```
    1. Używanie przekazywania portów w celu uzyskania dostępu do Kibana na hoście lokalnym: 

        ```bash
        kubectl port-forward service/kibana-kibana 5601 --namespace k2bridge
        ```
    1. Połącz się z usługą Kibana, przechodząc do http://127.0.0.1:5601.

    1. Udostępnienie użytkownikom końcowym Kibana. Istnieje wiele metod, które należy wykonać. Metoda używana w dużym stopniu zależy od przypadku użycia.

        Na przykład:

        Uwidacznianie usługi jako usługi równoważenia obciążenia. Aby to zrobić, Dodaj następujący parametr do polecenia instalacji K2Bridge Helm ([powyżej](#install-k2bridge-chart)):

        `--set service.type=LoadBalancer`
    
        Następnie uruchom:

           ```bash
           kubectl get service -w -n k2bridge
           ```   
        Dane wyjściowe powinny wyglądać następująco: 

        ```bash
        NAME            TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
        kibana-kibana   LoadBalancer   xx.xx.xx.xx   <pending>      5601:30128/TCP   4m24s
        ```
        Następnie można użyć wygenerowanego zewnętrznego adresu IP, który zostanie wyświetlony, a następnie użyć go do uzyskania dostępu do Kibana przez otwarcie przeglądarki w celu: `\<EXTERNAL-IP>:5601`.

1. Skonfiguruj wzorce indeksów w celu uzyskania dostępu do danych:  
W nowym wystąpieniu Kibana:
     1. Otwórz Kibana.
     1. Przejdź do zarządzania.
     1. Wybierz pozycję **wzorce indeksów**. 
     1. Utwórz wzorzec indeksu.
Nazwa indeksu musi dokładnie pasować do nazwy tabeli lub funkcji bez gwiazdki. Odpowiedni wiersz można skopiować z listy.

> [!Note]
> Aby uruchomić na innych dostawców Kubernetes, Zmień wartość Elasticsearch storageClassName w `values.yaml`, aby dopasować ją do sugerowanej przez dostawcę.

## <a name="visualize-data"></a>Wizualizacja danych

Gdy usługa Azure Eksplorator danych jest skonfigurowana jako źródło danych dla Kibana, można użyć Kibana do eksplorowania danych. 

1. W Kibana, w menu po lewej stronie wybierz kartę **odnajdywanie** .

1. Z listy rozwijanej po lewej stronie Wybierz wzorzec indeksu (w tym przypadku tabelę Eksplorator danych platformy Azure), która definiuje źródło danych, które chcesz zbadać.
    
   ![Wybierz wzorzec indeksu](media/k2bridge/k2bridge-select-an-index-pattern.png)

1. Jeśli dane zawierają pole filtru czasu, można określić zakres czasu. W prawym górnym rogu strony Ustaw filtr czasu. Domyślnie odnajdywanie pokazuje dane z ostatnich 15 minut.

   ![Filtr czasu](media/k2bridge/k2bridge-time-filter.png)
    
1. W tabeli wyników przedstawiono pierwsze 500 rekordów. Można rozwinąć dokument, aby przeanalizować jego dane pól w formatach JSON lub tabeli.

   ![Rozwiń rekord](media/k2bridge/k2bridge-expand-record.png)

1. Domyślnie tabela wyniki zawiera kolumny dla dokumentu _source i pola czas (jeśli istnieje). Można wybrać konkretne kolumny, które mają zostać dodane do tabeli wyników, wybierając pozycję **Dodaj** obok nazwy pola na lewym pasku bocznym.

   ![Określone kolumny](media/k2bridge/k2bridge-specific-columns.png)
    
1. Na pasku zapytania można wyszukiwać dane według:
    * Wprowadzanie terminu wyszukiwania
    * Użycie składni zapytań Lucene. 
    Na przykład:
        * Wyszukaj ciąg "Error", aby znaleźć wszystkie rekordy, które zawierają tę wartość. 
        * Wyszukaj ciąg "status: 200", aby uzyskać wszystkie rekordy z wartością stanu równą 200. 
    * Używanie operatorów logicznych (i, nie)
    * Używanie symboli wieloznacznych (gwiazdka "\*" lub znak zapytania "?") Na przykład:
        * `"destination_city: L*"` kwerendy będzie pasować do rekordów, w których wartość miejscowości docelowej rozpoczyna się od "l" (K2Bridge nie jest rozróżniana wielkość liter).

    ![Uruchamianie zapytania](media/k2bridge/k2bridge-run-query.png)
    
    > [!Tip]
    > W [wyszukiwaniu](https://github.com/microsoft/K2Bridge/blob/master/docs/searching.md)można znaleźć więcej reguł wyszukiwania i logiki.

1. Aby filtrować wyniki wyszukiwania, użyj **listy pól** na prawym pasku bocznym strony. 
    Lista pól to miejsce, w którym można zobaczyć:
    * Pięć najważniejszych wartości pola
    * Liczba rekordów zawierających pole
    * Procent rekordów zawierających każdą wartość. 
    
    >[!Tip]
    > Użyj ikony lupy (+), aby znaleźć wszystkie rekordy z określoną wartością.
    
    ![Lista pól](media/k2bridge/k2bridge-field-list.png)
   
    Możesz również filtrować wyniki przy użyciu ikony lupy (+) w widoku tabeli wyników każdego rekordu w tabeli wyników.
    
     ![Lista tabel](media/k2bridge/k2bridge-table-list.png)
    
1. Wybierz opcję, aby **zapisać** lub **udostępnić** wyszukiwanie.

     ![Zapisz wyszukiwanie](media/k2bridge/k2bridge-save-search.png)
