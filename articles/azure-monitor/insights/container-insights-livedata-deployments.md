---
title: Wyświetlanie usługi Azure Monitor dla wdrożeń kontenerów (wersja zapoznawcza) | Dokumenty firmy Microsoft
description: W tym artykule opisano widok w czasie rzeczywistym wdrożenia kubernetes bez użycia kubectl w usłudze Azure Monitor dla kontenerów.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 7d0344851e1db8c014a1bb16b228a0c2f76444d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75404776"
---
# <a name="how-to-view-deployments-preview-in-real-time"></a>Jak wyświetlić wdrożenia (wersja zapoznawcza) w czasie rzeczywistym

W usłudze Azure Monitor dla kontenerów funkcja wyświetlania wdrożeń (wersja zapoznawcza) emuluje bezpośredni dostęp do obiektów wdrażania usługi Kubernetes w czasie rzeczywistym, ujawniając polecenia `kubeclt get deployments` i. `kubectl describe deployment {your deployment}` 

>[!NOTE]
>Klastry AKS włączone jako [klastry prywatne](https://azure.microsoft.com/updates/aks-private-cluster/) nie są obsługiwane za pomocą tej funkcji. Ta funkcja polega na bezpośrednim uzyskiwaniu dostępu do interfejsu API kubernetes za pośrednictwem serwera proxy z przeglądarki. Włączenie zabezpieczeń sieciowych w celu zablokowania interfejsu API usługi Kubernetes z tego serwera proxy spowoduje zablokowanie tego ruchu. 

>[!NOTE]
>Ta funkcja jest dostępna we wszystkich regionach platformy Azure, w tym w chinach platformy Azure. Obecnie nie jest dostępna w usłudze Azure US Government.

Aby dowiedzieć się więcej, zapoznaj się z dokumentacją programu Kubernetes dotyczącą [wdrożeń](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/). 

## <a name="how-it-works"></a>Jak to działa

Funkcja Live Data (wersja zapoznawcza) umożliwia bezpośredni dostęp do interfejsu API usługi Kubernetes, a dodatkowe informacje o modelu uwierzytelniania można znaleźć [tutaj](https://kubernetes.io/docs/concepts/overview/kubernetes-api/). 

Funkcja Wdrożenia (wersja zapoznawcza) wykonuje jednorazowe (odświeżalne) obciążenie `/apis/apps/v1/deployments`względem punktu końcowego wdrożeń . Umożliwia wybranie danego wdrożenia i załadowanie szczegółów opisu dla tego `/apis/apps/v1/namespaces/${nameSpace}/deployments/${deploymentName}`konkretnego wdrożenia względem punktu końcowego wdrożenia. 

Wybranie **opcji Odśwież** w lewym górnym rogu strony powoduje odświeżenie listy wdrożeniowej. Symuluje ponowne `kubectl` uruchomienie polecenia. 

>[!IMPORTANT]
>Żadne dane nie są przechowywane na stałe podczas pracy tej funkcji. Wszystkie informacje przechwycone podczas sesji są usuwane po zamknięciu przeglądarki lub odejściu od niej.  

>[!NOTE]
>Nie można przypiąć danych live data (w wersji zapoznawczej) z konsoli do pulpitu nawigacyjnego platformy Azure.

## <a name="deployments-describe"></a>Wdrożeń opisać

Aby wyświetlić opis szczegółów wdrożenia, które `kubectl describe deployment`jest równoważne , wykonaj następujące kroki.

1. W witrynie Azure portal przejdź do grupy zasobów klastra AKS i wybierz zasób usługi AKS.

2. Na pulpicie nawigacyjnym klastra AKS w obszarze **Monitorowanie** po lewej stronie wybierz pozycję **Insights**. 

3. Wybierz kartę **Wdrożenia (podgląd).**

    ![Widok wdrożenia w witrynie Azure portal](./media/container-insights-livedata-deployments/deployment-view.png)

Widok zawiera listę wszystkich uruchomionych wdrożeń wraz z obszarem nazw i `kubectl get deployments –all-namespaces`innymi szczegółowymi informacjami, emulując wykonanie polecenia . Wyniki można sortować, wybierając jedną z kolumn. 

![Szczegóły okienka właściwości wdrożenia](./media/container-insights-livedata-deployments/deployment-properties-pane-details.png)

Po wybraniu wdrożenia z listy okienko właściwości jest automatycznie wyświetlane po prawej stronie strony. Pokazuje informacje związane z wybranym wdrożeniem, które `kubectl describe deployment {deploymentName}`można wyświetlić po uruchomieniu polecenia . Być może zauważyłeś, że w opisie informacji brakuje pewnych szczegółów. Przede wszystkim brakuje **szablonu.** Wybranie karty **Raw** umożliwia przejście do szczegółów describe nieprzeswojonych.  

![Szczegóły nieprzetworzonego okienka właściwości wdrożeń](./media/container-insights-livedata-deployments/deployment-properties-pane-raw.png)

Podczas przeglądania szczegółów wdrożenia, można zobaczyć dzienniki kontenerów i zdarzenia w czasie rzeczywistym. Wybierz **opcję Pokaż konsolę na żywo,** a okienko konsoli Dane na żywo (wersja zapoznawcza) pojawi się poniżej siatki danych wdrożeń, w której można wyświetlać dane dziennika na żywo w strumieniu ciągłym. Jeśli wskaźnik stanu pobierania pokazuje zielony znacznik wyboru, który znajduje się po prawej stronie okienka, oznacza to, że dane mogą być pobierane i rozpoczyna przesyłanie strumieniowe do konsoli.

Można również filtrować według zdarzeń obszaru nazw lub poziomu klastra. Aby dowiedzieć się więcej o wyświetlaniu danych w czasie rzeczywistym w konsoli, zobacz [Wyświetlanie danych na żywo (w wersji zapoznawczej) za pomocą usługi Azure Monitor dla kontenerów](container-insights-livedata-overview.md). 

![Wdrożenia wyświetlą dane na żywo w konsoli](./media/container-insights-livedata-deployments/deployments-console-view-events.png)

## <a name="next-steps"></a>Następne kroki

- Aby kontynuować naukę korzystania z usługi Azure Monitor i monitorować inne aspekty klastra usługi AKS, zobacz [Wyświetlanie kondycji usługi Azure Kubernetes.](container-insights-analyze.md)

- Wyświetlanie [przykładów zapytań dziennika,](container-insights-log-search.md#search-logs-to-analyze-data) aby wyświetlić wstępnie zdefiniowane kwerendy i przykłady do tworzenia alertów, wizualizacji lub wykonywania dalszej analizy klastrów.
