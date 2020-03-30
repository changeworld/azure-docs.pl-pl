---
title: Wyświetlanie danych na żywo (wersja zapoznawcza) za pomocą usługi Azure Monitor dla kontenerów | Dokumenty firmy Microsoft
description: W tym artykule opisano widok w czasie rzeczywistym dzienników, zdarzeń i metryk zasobników usługi Kubernetes bez użycia kubectl w usłudze Azure Monitor dla kontenerów.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 9e7c7a7b7bf276b3451cee1d289b8b07ac0f40ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79216538"
---
# <a name="how-to-view-kubernetes-logs-events-and-pod-metrics-in-real-time"></a>Jak wyświetlać dzienniki, zdarzenia i dane zasobników kubernetes w czasie rzeczywistym

Usługa Azure Monitor dla kontenerów zawiera funkcję Live Data (wersja zapoznawcza), która jest zaawansowaną funkcją diagnostyczną umożliwiającą bezpośredni dostęp do dzienników kontenerów usługi Azure Kubernetes (AKS) (stdout/stderror), zdarzeń i metryk zasobników. Udostępnia bezpośredni dostęp `kubectl logs -c`do `kubectl get` , `kubectl top pods`wydarzeń i . Okienko konsoli pokazuje dzienniki, zdarzenia i metryki generowane przez aparat kontenera, aby dodatkowo pomóc w rozwiązywaniu problemów w czasie rzeczywistym.

Ten artykuł zawiera szczegółowe omówienie i pomaga zrozumieć, jak korzystać z tej funkcji. 

>[!NOTE]
>Klastry AKS włączone jako [klastry prywatne](https://azure.microsoft.com/updates/aks-private-cluster/) nie są obsługiwane za pomocą tej funkcji. Ta funkcja polega na bezpośrednim uzyskiwaniu dostępu do interfejsu API kubernetes za pośrednictwem serwera proxy z przeglądarki. Włączenie zabezpieczeń sieciowych w celu zablokowania interfejsu API usługi Kubernetes z tego serwera proxy spowoduje zablokowanie tego ruchu. 

>[!NOTE]
>Ta funkcja jest dostępna we wszystkich regionach platformy Azure, w tym w chinach platformy Azure. Obecnie nie jest dostępna w usłudze Azure US Government.

Aby uzyskać pomoc dotyczącą konfigurowania lub rozwiązywania problemów z funkcją Live Data (wersja zapoznawcza), zapoznaj się z naszym [przewodnikiem po instalacji](container-insights-livedata-setup.md). Ta funkcja ma bezpośredni dostęp do interfejsu API kubernetes, a dodatkowe informacje na temat modelu uwierzytelniania można znaleźć [tutaj](https://kubernetes.io/docs/concepts/overview/kubernetes-api/). 

## <a name="live-data-preview-functionality-overview"></a>Omówienie funkcji funkcji live data (wersja zapoznawcza)

### <a name="search"></a>Wyszukiwanie

![Przykład filtru okienka konsoli danych aktywnych](./media/container-insights-livedata-overview/livedata-pane-filter-example.png)

Funkcja Live Data (wersja zapoznawcza) zawiera funkcję wyszukiwania. W polu **Wyszukiwanie** można filtrować wyniki, wpisując słowo kluczowe lub termin, a wszystkie pasujące wyniki są wyróżnione, aby umożliwić szybką weryfikację. Podczas przeglądania zdarzeń można dodatkowo ograniczyć wyniki za pomocą pigułki **Filtruj** znajdującej się po prawej stronie paska wyszukiwania. W zależności od wybranego zasobu pigułka zawiera listę zasobu, obszaru nazw lub klastra do wyboru.  

### <a name="scroll-lock-and-pause"></a>Blokada przewijania i pauza 

Aby zawiesić autoprzestrszt i kontrolować zachowanie okienka, co pozwala ręcznie przewijać nowe odczytywanie danych, można użyć opcji **Przewijania.** Aby ponownie włączyć autoscroll, wystarczy ponownie wybrać opcję **Przewijanie.** Możesz również wstrzymać pobieranie danych dziennika lub zdarzeń, wybierając opcję **Wstrzymaj,** a gdy będziesz gotowy do wznowienia, po prostu wybierz przycisk **Odtwórz**.  

![Okienko konsoli danych na żywo wstrzymuje widok na żywo](./media/container-insights-livedata-overview/livedata-pane-scroll-pause-example.png)

>[!IMPORTANT]
>Zalecamy tylko zawieszenie lub wstrzymanie automatycznego skalowania na krótki okres czasu podczas rozwiązywania problemu. Te żądania mogą mieć wpływ na dostępność i ograniczanie interfejsu API usługi Kubernetes w klastrze. 

>[!IMPORTANT]
>Żadne dane nie są przechowywane na stałe podczas pracy tej funkcji. Wszystkie informacje przechwycone podczas sesji są usuwane po zamknięciu przeglądarki lub odejściu od niej. Dane pozostają widoczne tylko do wizualizacji w pięciominutowym oknie funkcji metryki; wszystkie dane starsze niż pięć minut są również usuwane. Zapytania buforu danych na żywo (w wersji zapoznawczej) w rozsądnych limitach użycia pamięci (muszą być bardziej szczegółowe w tym miejscu, co jest uzasadnione?). 

## <a name="view-logs"></a>Wyświetlanie dzienników

Można wyświetlić dane dziennika w czasie rzeczywistym, ponieważ są one generowane przez aparat kontenerów z **widoków Węzły,** **Kontrolery**i **Kontenery.** Aby wyświetlić dane dziennika, wykonaj następujące kroki.

1. W witrynie Azure portal przejdź do grupy zasobów klastra AKS i wybierz zasób usługi AKS.

2. Na pulpicie nawigacyjnym klastra AKS w obszarze **Monitorowanie** po lewej stronie wybierz pozycję **Insights**. 

3. Wybierz kartę **Węzły,** **Kontrolery**lub **Kontenery.**

4. Zaznacz obiekt z siatki wydajności, a w okienku właściwości znajdującym się po prawej stronie wybierz opcję **Wyświetl dane na żywo (podgląd).** Jeśli klaster AKS jest skonfigurowany przy użyciu logowania jednokrotnego przy użyciu usługi Azure AD, zostanie wyświetlony monit o uwierzytelnienie przy pierwszym użyciu podczas tej sesji przeglądarki. Wybierz swoje konto i pełne uwierzytelnianie za pomocą platformy Azure.  

    >[!NOTE]
    >Podczas przeglądania danych z obszaru roboczego usługi Log Analytics, wybierając opcję **Wyświetl w analizie** z okienka właściwości, w wynikach wyszukiwania dziennika mogą być wyświetlane **węzły,** **zestawy demonów,** **zestawy replik,** **zadania,** **zadania cron**, **zasobniki**i **kontenery,** które mogą już nie istnieć. Próba wyszukiwania dzienników kontenera, który nie `kubectl` jest dostępny w spowoduje również niepowodzenie w tym miejscu. Przejrzyj funkcję [Wyświetl w analytics,](container-insights-log-search.md#search-logs-to-analyze-data) aby dowiedzieć się więcej o wyświetlaniu historycznych dzienników, zdarzeń i danych.  

Po pomyślnym uwierzytelnieniu okienko konsoli Live Data (wersja zapoznawcza) pojawi się poniżej siatki danych wydajności, w której można wyświetlać dane dziennika w strumieniu ciągłym. Jeśli wskaźnik stanu pobierania pokazuje zielony znacznik wyboru, który znajduje się po prawej stronie okienka, oznacza to, że dane mogą być pobierane i rozpoczyna przesyłanie strumieniowe do konsoli.  

![Opcja widoku danych widoku okienka właściwości węzła](./media/container-insights-livedata-overview/node-properties-pane.png)  

Tytuł okienka zawiera nazwę zasobnika, z nim zgrupowany kontener.

## <a name="view-events"></a>Wyświetlanie zdarzeń

Dane zdarzeń w czasie rzeczywistym można wyświetlać w czasie rzeczywistym, ponieważ są one generowane przez aparat kontenerów z widoku **Węzły,** **Kontrolery,** **Kontenery**i **Wdrożenia (podgląd)** po wybraniu kontenera, zasobnika, węzła, replicaset, zestawu demonów, zadania, cronjob lub wdrożenia. Aby wyświetlić zdarzenia, wykonaj następujące kroki.

1. W witrynie Azure portal przejdź do grupy zasobów klastra AKS i wybierz zasób usługi AKS.

2. Na pulpicie nawigacyjnym klastra AKS w obszarze **Monitorowanie** po lewej stronie wybierz pozycję **Insights**. 

3. Wybierz kartę **Węzły,** **Kontrolery,** **Kontenery**lub **Wdrożenia (podgląd).**

4. Zaznacz obiekt z siatki wydajności, a w okienku właściwości znajdującym się po prawej stronie wybierz opcję **Wyświetl dane na żywo (podgląd).** Jeśli klaster AKS jest skonfigurowany przy użyciu logowania jednokrotnego przy użyciu usługi Azure AD, zostanie wyświetlony monit o uwierzytelnienie przy pierwszym użyciu podczas tej sesji przeglądarki. Wybierz swoje konto i pełne uwierzytelnianie za pomocą platformy Azure.  

    >[!NOTE]
    >Podczas przeglądania danych z obszaru roboczego usługi Log Analytics, wybierając opcję **Wyświetl w analizie** z okienka właściwości, w wynikach wyszukiwania dziennika mogą być wyświetlane **węzły,** **zestawy demonów,** **zestawy replik,** **zadania,** **zadania cron**, **zasobniki**i **kontenery,** które mogą już nie istnieć. Próba wyszukiwania dzienników kontenera, który nie `kubectl` jest dostępny w spowoduje również niepowodzenie w tym miejscu. Przejrzyj funkcję [Wyświetl w analytics,](container-insights-log-search.md#search-logs-to-analyze-data) aby dowiedzieć się więcej o wyświetlaniu historycznych dzienników, zdarzeń i danych.  

Po pomyślnym uwierzytelnieniu okienko konsoli Live Data (wersja zapoznawcza) pojawi się poniżej siatki danych wydajności. Jeśli wskaźnik stanu pobierania pokazuje zielony znacznik wyboru, który znajduje się po prawej stronie okienka, oznacza to, że dane mogą być pobierane i rozpoczyna przesyłanie strumieniowe do konsoli. 
    
Jeśli wybrany obiekt był kontenerem, wybierz opcję **Zdarzenia** w okienku. Jeśli wybrano węzeł, zasobnik lub kontroler, wyświetlanie zdarzeń jest wybierane automatycznie. 

![Zdarzenia widoku okienka właściwości kontrolera](./media/container-insights-livedata-overview/controller-properties-live-event.png)  

Tytuł okienka zawiera nazwę zasobnika, z w który kontener jest zgrupowany.

### <a name="filter-events"></a>Filtrowanie zdarzeń 

Podczas przeglądania zdarzeń można dodatkowo ograniczyć wyniki za pomocą pigułki **Filtruj** znajdującej się po prawej stronie paska wyszukiwania. W zależności od wybranego zasobu pigułka zawiera listę zasobu, obszaru nazw lub klastra do wyboru.  

## <a name="view-metrics"></a>Wyświetlanie metryk 

Można wyświetlić dane metryki w czasie rzeczywistym, ponieważ są one generowane przez aparat kontenera z **widoku węzły** lub **kontrolery** tylko wtedy, gdy wybrano **zasobnika.** Aby wyświetlić metryki, wykonaj następujące kroki.

1. W witrynie Azure portal przejdź do grupy zasobów klastra AKS i wybierz zasób usługi AKS.

2. Na pulpicie nawigacyjnym klastra AKS w obszarze **Monitorowanie** po lewej stronie wybierz pozycję **Insights**. 

3. Wybierz kartę **Węzły** lub **Kontrolery.**

4. Wybierz obiekt **Pod** z siatki wydajności, a w okienku właściwości znajdującym się po prawej stronie wybierz opcję **Wyświetl dane na żywo (podgląd).** Jeśli klaster AKS jest skonfigurowany przy użyciu logowania jednokrotnego przy użyciu usługi Azure AD, zostanie wyświetlony monit o uwierzytelnienie przy pierwszym użyciu podczas tej sesji przeglądarki. Wybierz swoje konto i pełne uwierzytelnianie za pomocą platformy Azure.  

    >[!NOTE]
    >Podczas przeglądania danych z obszaru roboczego usługi Log Analytics, wybierając opcję **Wyświetl w analizie** z okienka właściwości, w wynikach wyszukiwania dziennika mogą być wyświetlane **węzły,** **zestawy demonów,** **zestawy replik,** **zadania,** **zadania cron**, **zasobniki**i **kontenery,** które mogą już nie istnieć. Próba wyszukiwania dzienników kontenera, który nie `kubectl` jest dostępny w spowoduje również niepowodzenie w tym miejscu. Przejrzyj funkcję [Wyświetl w analytics,](container-insights-log-search.md#search-logs-to-analyze-data) aby dowiedzieć się więcej o wyświetlaniu historycznych dzienników, zdarzeń i danych.  

Po pomyślnym uwierzytelnieniu okienko konsoli Live Data (wersja zapoznawcza) pojawi się poniżej siatki danych wydajności. Dane metryki są pobierane i rozpoczyna przesyłanie strumieniowe do konsoli do prezentacji na dwóch wykresach. Tytuł okienka zawiera nazwę zasobnika, z nim zgrupowany kontener.

![Wyświetl przykład metryk zasobników](./media/container-insights-livedata-overview/pod-properties-live-metrics.png)  

## <a name="next-steps"></a>Następne kroki

- Aby kontynuować naukę korzystania z usługi Azure Monitor i monitorować inne aspekty klastra usługi AKS, zobacz [Wyświetlanie kondycji usługi Azure Kubernetes.](container-insights-analyze.md)

- Wyświetlanie [przykładów zapytań dziennika,](container-insights-log-search.md#search-logs-to-analyze-data) aby wyświetlić wstępnie zdefiniowane kwerendy i przykłady do tworzenia alertów, wizualizacji lub wykonywania dalszej analizy klastrów.
