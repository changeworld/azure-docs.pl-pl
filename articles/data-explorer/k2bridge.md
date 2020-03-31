---
title: Wizualizuj dane z eksploratora danych platformy Azure za pomocą programu Kibana
description: W tym artykule dowiesz się, jak skonfigurować Eksploratora danych platformy Azure jako źródło danych dla kibana
author: orspod
ms.author: orspodek
ms.reviewer: guregini
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: fac9c78607e50dca384670bf4cc08b50f723312b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065607"
---
# <a name="visualize-data-from-azure-data-explorer-in-kibana-with-the-k2bridge-open-source-connector"></a>Wizualizuj dane z usługi Azure Data Explorer w kibanie za pomocą łącznika typu open source K2Bridge

K2Bridge (Kibana-Kusto Bridge) umożliwia korzystanie z Usługi Azure Data Explorer jako źródła danych i wizualizowanie tych danych w Kibana. K2Bridge to aplikacja konteneryzowana [typu open source,](https://github.com/microsoft/K2Bridge) która działa jako serwer proxy między wystąpieniem Kibana a klastrem usługi Azure Data Explorer. W tym artykule opisano sposób tworzenia tego połączenia za pomocą aplikacji K2Bridge.

K2Bridge tłumaczy zapytania Kibana na Język zapytania Kusto (KQL) i wysyła wyniki Usługi Azure Data Explorer z powrotem do Kibana. 

   ![wykres](media/k2bridge/k2bridge-chart.png)

K2Bridge obsługuje kartę Odkryj Kibana, gdzie można:
* Wyszukiwanie i eksplorowanie danych
* Filtrowanie wyników
* Dodawanie lub usuwanie pól w siatce wyników
* Wyświetlanie zawartości rekordu
* Zapisywanie i udostępnianie wyszukiwań

Poniższy obraz ekspozycyjny przedstawia wystąpienie Kibana powiązane z Eksploratorem danych platformy Azure przez K2Bridge. Środowisko użytkownika w Kibana pozostaje bez zmian.

   [![Strona Kibana](media/k2bridge/k2bridge-kibana-page.png)](media/k2bridge/k2bridge-kibana-page.png#lightbox)

## <a name="prerequisites"></a>Wymagania wstępne

Zanim będzie można wizualizować dane z usługi Azure Data Explorer w programie Kibana, przygotuj następujące elementy:

* [Helm V3](https://github.com/helm/helm#install), menedżer pakietów Kubernetes
* Klaster usługi Azure Kubernetes (AKS) lub dowolny inny klaster kubernetes (wersja 1.14 do wersja 1.16 zostały przetestowane i zweryfikowane). Jeśli potrzebujesz klastra AKS, zobacz Wdrażanie klastra AKS [przy użyciu interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough) lub za pomocą portalu [Azure](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal)
* [Klaster Eksploratora danych platformy Azure](create-cluster-database-portal.md), w tym:
    * Adres URL klastra usługi Azure Data Explorer 
    * Nazwa bazy danych
    
* Podmiot usługi Azure AD autoryzowany do wyświetlania danych w Eksploratorze danych platformy Azure, w tym:
    * Identyfikator klienta 
    * Tajemnica klienta

    Zaleca się jednostkę usługi z uprawnieniem "Viewer". Nie zaleca się używania wyższych uprawnień.

    * [Ustaw uprawnienia do widoku klastra dla jednostki usługi Azure AD](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions#manage-permissions-in-the-azure-portal).

    Aby uzyskać więcej informacji na temat jednostki usługi Azure AD, zobacz [Tworzenie jednostki usługi Azure AD.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application)

## <a name="run-k2bridge-on-azure-kubernetes-service-aks"></a>Uruchamianie aplikacji K2Bridge w usłudze Azure Kubernetes (AKS)

Domyślnie wykres Helm firmy K2Bridges odwołuje się do publicznie dostępnego obrazu znajdującego się w rejestrze kontenerów firmy Microsoft (MCR). McR nie wymaga żadnych poświadczeń i działa out-of-the-box.

1. Pobierz wymagane wykresy Helm.

1. Dodaj zależność Elasticsearch do helmu. 
    Powodem zależności Elasticsearch jest to, że K2Bridge używa wewnętrznego małego wystąpienia Elasticsearch do obsługi żądań związanych z metadanymi (takich jak wzorce indeksu i zapisane zapytania). Żadne dane biznesowe nie są zapisywane w tym wystąpieniu wewnętrznym i można je uznać za szczegóły implementacji. 

    1. Aby dodać zależność Elasticsearch do helma:

        ```bash
        helm repo add elastic https://helm.elastic.co
        helm repo update
        ```

    1. Aby uzyskać wykres K2Bridge spod katalogu wykresów repozytorium GitHub:
        1. Sklonuj repozytorium z [GitHub](https://github.com/microsoft/K2Bridge).
        1. Przejdź do katalogu repozytorium głównego K2Bridges.
        1. Uruchomienie:

            ```bash
            helm dependency update charts/k2bridge
            ```

1. Wdrażanie K2Bridge:

    1. Ustaw zmienne z odpowiednimi wartościami dla środowiska:

        ```bash
        ADX_URL=[YOUR_ADX_CLUSTER_URL] #For example, https://mycluster.westeurope.kusto.windows.net
        ADX_DATABASE=[YOUR_ADX_DATABASE_NAME]
        ADX_CLIENT_ID=[SERVICE_PRINCIPAL_CLIENT_ID]
        ADX_CLIENT_SECRET=[SERVICE_PRINCIPAL_CLIENT_SECRET]
        ADX_TENANT_ID=[SERVICE_PRINCIPAL_TENANT_ID]
        ```

    1. (Opcjonalnie) Włącz dane telemetryczne usługi Azure Application Insights. 
        Jeśli jest to pierwszy raz, gdy używasz usługi Azure Application Insights, należy najpierw [utworzyć zasób usługi Application Insights.](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) Należy [skopiować klucz instrumentacji](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#copy-the-instrumentation-key) do zmiennej: 

        ```bash
        APPLICATION_INSIGHTS_KEY=[INSTRUMENTATION_KEY]
        COLLECT_TELEMETRY=true
        ```

    1. <a name="install-k2bridge-chart"></a>Zainstaluj wykres K2Bridge:

        ```bash
        helm install k2bridge charts/k2bridge -n k2bridge --set image.repository=$REPOSITORY_NAME/$CONTAINER_NAME --set settings.adxClusterUrl="$ADX_URL" --set settings.adxDefaultDatabaseName="$ADX_DATABASE" --set settings.aadClientId="$ADX_CLIENT_ID" --set settings.aadClientSecret="$ADX_CLIENT_SECRET" --set settings.aadTenantId="$ADX_TENANT_ID" [--set image.tag=latest] [--set privateRegistry="$IMAGE_PULL_SECRET_NAME"] [--set settings.collectTelemetry=$COLLECT_TELEMETRY]
        ```

        W [konfiguracji](https://github.com/microsoft/K2Bridge/blob/master/docs/configuration.md) można znaleźć kompletny zestaw opcji konfiguracji.

    1. Dane wyjściowe polecenia zasugerują następne polecenie Helm do uruchomienia w celu wdrożenia Kibana. Opcjonalnie uruchom:

        ```bash
        helm install kibana elastic/kibana -n k2bridge --set image=docker.elastic.co/kibana/kibana-oss --set imageTag=6.8.5 --set elasticsearchHosts=http://k2bridge:8080
        ```
        
    1. Użyj przekierowania portów, aby uzyskać dostęp do Kibana na localhost: 

        ```bash
        kubectl port-forward service/kibana-kibana 5601 --namespace k2bridge
        ```
        
    1. Połącz się z Kibana, przeglądając do http://127.0.0.1:5601.

    1. Uwidacznia kibana użytkownikom końcowym. Istnieje wiele metod, aby to zrobić. Metoda, której używasz, w dużej mierze zależy od przypadku użycia.

        Przykład:

        Uwidaczniać usługę jako usługę LoadBalancer. Aby to zrobić, `--set service.type=LoadBalancer` dodaj parametr do polecenia instalacji K2Bridge Helm ([powyżej](#install-k2bridge-chart)).        
    
        Następnie uruchom polecenie:
        
        ```bash
        kubectl get service -w -n k2bridge
        ```
        
        Dane wyjściowe powinny wyglądać następująco: 

        ```bash
        NAME            TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
        kibana-kibana   LoadBalancer   xx.xx.xx.xx   <pending>      5601:30128/TCP   4m24s
        ```
 
        Następnie można użyć wygenerowanego zewnętrznego adresu IP, który się pojawi, i `<EXTERNAL-IP>:5601`użyć go, aby uzyskać dostęp do Kibana, otwierając przeglądarkę na .

1. Skonfiguruj wzorce indeksów, aby uzyskać dostęp do danych:  
W nowym wystąpieniu Kibana:
     1. Otwórz Kibana.
     1. Przejdź do pozycji Zarządzanie.
     1. Wybierz **opcję Wzorce indeksu**. 
     1. Utwórz wzorzec indeksu.
Nazwa indeksu musi dokładnie odpowiadać nazwie tabeli lub nazwy funkcji bez gwiazdki. Odpowiedni wiersz można skopiować z listy.

> [!Note]
> Aby uruchomić na innych dostawców Kubernetes, należy zmienić Elasticsearch storageClassName w, `values.yaml` aby dopasować jeden sugerowane przez dostawcę.

## <a name="visualize-data"></a>Wizualizowanie danych

Gdy Usługa Azure Data Explorer jest skonfigurowana jako źródło danych dla kibana, można użyć Kibana do eksplorowania danych. 

1. W kibana, w menu po lewej stronie, wybierz **discover** kartę.

1. Z lewej listy rozwijanej wybierz wzorzec indeksu (w tym przypadku tabelę Eksploratora danych platformy Azure), która definiuje źródło danych, które chcesz eksplorować.
    
   ![Wybieranie wzorca indeksu](media/k2bridge/k2bridge-select-an-index-pattern.png)

1. Jeśli dane mają pole filtru czasu, można określić zakres czasu. W prawym górnym rogu strony ustaw filtr czasu. Domyślnie discover pokazuje dane z ostatnich 15 minut.

   ![Filtr czasu](media/k2bridge/k2bridge-time-filter.png)
    
1. Tabela wyników zawiera pierwsze 500 rekordów. Można rozwinąć dokument, aby sprawdzić jego dane pola w formacie JSON lub tabeli.

   ![Rozwijanie rekordu](media/k2bridge/k2bridge-expand-record.png)

1. Domyślnie tabela wyników zawiera kolumny dla _source dokumentu i pola czasu (jeśli istnieje). Można wybrać określone kolumny, które mają zostać dodane do tabeli wyników, wybierając **dodaj** obok nazwy pola na lewym pasku bocznym.

   ![Określone kolumny](media/k2bridge/k2bridge-specific-columns.png)
    
1. Na pasku zapytania można przeszukiwać dane według:
    * Wprowadzanie wyszukiwanego terminu
    * Korzystanie ze składni kwerendy lucene. 
    Przykład:
        * Wyszukaj "błąd", aby znaleźć wszystkie rekordy, które zawierają tę wartość. 
        * Wyszukaj "stan: 200", aby uzyskać wszystkie rekordy o wartości statusu 200. 
    * Korzystanie z operatorów logicznych (AND, OR, NOT)
    * Używanie symboli wieloznacznych \* (gwiazdka " " lub znaku zapytania "?") Na przykład:
        * Kwerenda `"destination_city: L*"` będzie zgodna rekordy, w których wartość miasta docelowego zaczyna się od "l" (K2Bridge nie jest rozróżniana wielkość liter).

    ![Uruchamianie zapytania](media/k2bridge/k2bridge-run-query.png)
    
    > [!Tip]
    > W [wyszukiwaniu](https://github.com/microsoft/K2Bridge/blob/master/docs/searching.md)można znaleźć więcej reguł wyszukiwania i logiki.

1. Aby filtrować wyniki wyszukiwania, użyj **listy pól** na prawym pasku bocznym strony. 
    Lista pól jest miejscem, w którym można zobaczyć:
    * Pięć pierwszych wartości pola
    * Liczba rekordów zawierających to pole
    * Procent rekordów zawierających każdą wartość. 
    
    >[!Tip]
    > Użyj ikony lupy (+), aby znaleźć wszystkie rekordy o określonej wartości.
    
    ![Lista pól](media/k2bridge/k2bridge-field-list.png)
   
    Wyniki można również filtrować za pomocą ikony lupy (+) w widoku formatu tabeli wyników każdego rekordu w tabeli wyników.
    
     ![Lista tabel](media/k2bridge/k2bridge-table-list.png)
    
1. Wybierz opcję **Zapisz** lub **Udostępnij** wyszukiwanie.

     ![Zapisz wyszukiwanie](media/k2bridge/k2bridge-save-search.png)
