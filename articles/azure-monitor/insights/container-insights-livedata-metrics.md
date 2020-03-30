---
title: Wyświetlanie metryk w czasie rzeczywistym za pomocą usługi Azure Monitor dla kontenerów | Dokumenty firmy Microsoft
description: W tym artykule opisano widok metryk w czasie rzeczywistym bez użycia kubectl z usługą Azure Monitor dla kontenerów.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 4604635c985057ec0b7f49a0d1cca7111dfc8eec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79216584"
---
# <a name="how-to-view-metrics-in-real-time"></a>Jak wyświetlać dane w czasie rzeczywistym

Usługa Azure Monitor dla kontenerów Dane na żywo (wersja zapoznawcza) funkcja umożliwia wizualizację metryk dotyczących stanu węzła i zasobnika w klastrze w czasie rzeczywistym. Emuluje bezpośredni dostęp `kubectl top nodes`do `kubectl get pods –all-namespaces`, `kubectl get nodes` i poleceń do wywołania, analizowania i wizualizacji danych na wykresach wydajności, które są zawarte w tym insight. 

Ten artykuł zawiera szczegółowe omówienie i pomaga zrozumieć, jak korzystać z tej funkcji.  

>[!NOTE]
>Klastry AKS włączone jako [klastry prywatne](https://azure.microsoft.com/updates/aks-private-cluster/) nie są obsługiwane za pomocą tej funkcji. Ta funkcja polega na bezpośrednim uzyskiwaniu dostępu do interfejsu API kubernetes za pośrednictwem serwera proxy z przeglądarki. Włączenie zabezpieczeń sieciowych w celu zablokowania interfejsu API usługi Kubernetes z tego serwera proxy spowoduje zablokowanie tego ruchu. 

>[!NOTE]
>Ta funkcja jest dostępna we wszystkich regionach platformy Azure, w tym w chinach platformy Azure. Obecnie nie jest dostępna w usłudze Azure US Government.

Aby uzyskać pomoc dotyczącą konfigurowania lub rozwiązywania problemów z funkcją Live Data (wersja zapoznawcza), zapoznaj się z naszym [przewodnikiem po instalacji](container-insights-livedata-setup.md).

## <a name="how-it-works"></a>Jak to działa 

Funkcja Live Data (wersja zapoznawcza) umożliwia bezpośredni dostęp do interfejsu API usługi Kubernetes, a dodatkowe informacje o modelu uwierzytelniania można znaleźć [tutaj](https://kubernetes.io/docs/concepts/overview/kubernetes-api/). 

Ta funkcja wykonuje operację sondowania względem punktów końcowych `/api/v1/nodes`metryk (w tym , `/apis/metrics.k8s.io/v1beta1/nodes`i `/api/v1/pods`), która jest domyślnie co pięć sekund. Te dane są buforowane w przeglądarce i na wykresach na czterech wykresach wydajności uwzględnionych w usłudze Azure Monitor dla kontenerów na karcie **Klaster,** wybierając pozycję **Przejdź na żywo (wersja zapoznawcza)**. Każda kolejna ankieta jest przedstawiana na wykresie w toczącym się pięciominutowym oknie wizualizacji. 

![Opcja Przejdź na żywo w widoku klastra](./media/container-insights-livedata-metrics/cluster-view-go-live-example-01.png)

Interwał sondowania jest skonfigurowany z listy rozwijanej **Ustaw interwał,** co umożliwia ustawienie sondowania nowych danych co 1, 5, 15 i 30 sekund. 

![Interwał sondowania listy rozwijanej Przejdź na żywo](./media/container-insights-livedata-metrics/cluster-view-polling-interval-dropdown.png)

>[!IMPORTANT]
>Zaleca się ustawienie interwału sondowania na jedną sekundę podczas rozwiązywania problemu przez krótki okres czasu. Te żądania mogą mieć wpływ na dostępność i ograniczanie interfejsu API usługi Kubernetes w klastrze. Następnie ponownie skonfiguruj do dłuższego interwału sondowania. 

>[!IMPORTANT]
>Żadne dane nie są przechowywane na stałe podczas pracy tej funkcji. Wszystkie informacje przechwycone podczas tej sesji są natychmiast usuwane po zamknięciu przeglądarki lub przejściu z dala od funkcji. Dane pozostają tylko obecne do wizualizacji wewnątrz okna pięciu minut; wszystkie dane starsze niż pięć minut są również trwale usuwane.

Tych wykresów nie można przypiąć do ostatniego pulpitu nawigacyjnego platformy Azure, który był oglądany w trybie na żywo.

## <a name="metrics-captured"></a>Przechwycone dane

### <a name="node-cpu-utilization---node-memory-utilization-"></a>Wykorzystanie procesora CPU węzła % / wykorzystanie pamięci węzła % 

Te dwa wykresy wydajności map do `kubectl top nodes` odpowiednika wywoływania i przechwytywania wyników **CPU%** i **MEMORY%** kolumn do odpowiedniego wykresu. 

![Przykładowe wyniki węzłów górnych Kubectl](./media/container-insights-livedata-metrics/kubectl-top-nodes-example.png)

![Wykres procentu wykorzystania procesora CPU węzłów](./media/container-insights-livedata-metrics/cluster-view-node-cpu-util.png)

![Wykres procentu wykorzystania pamięci węzła](./media/container-insights-livedata-metrics/cluster-view-node-memory-util.png)

Obliczenia percentyla będą działać w większych klastrach, aby ułatwić identyfikację węzłów odstających w klastrze. Na przykład, aby zrozumieć, czy węzły są niedostateczone do celów skalowania w dół. Korzystając z **agregacji Min** można zobaczyć, które węzły mają niskie wykorzystanie w klastrze. Aby dokładniej zbadać, należy wybrać **nodes** kartę i sortować siatki według wykorzystania procesora CPU lub pamięci.

Pomaga to również zrozumieć, które węzły są wypychane do ich limitów i czy może być wymagane skalowanie w poziomie. Korzystanie z agregacji **Max** i **P95** może pomóc sprawdzić, czy istnieją węzły w klastrze z wysokim wykorzystaniem zasobów. W celu dalszego zbadania należy ponownie przełączyć się do **węzłów** kartę.

### <a name="node-count"></a>Liczba węzłów

Ten wykres wydajności jest mapowany `kubectl get nodes` na odpowiednik wywoływania i mapowania kolumny **STATUS** na wykres pogrupowany według typów stanu.

![Kubectl uzyskać przykładowe wyniki węzłów](./media/container-insights-livedata-metrics/kubectl-get-nodes-example.png)

![Wykres liczby węzłów](./media/container-insights-livedata-metrics/cluster-view-node-count-01.png)

Węzły są zgłaszane w stanie **Gotowe** lub **Nie gotowe.** Są one liczone (i tworzona jest całkowita liczba), a wyniki tych dwóch agregacji są na wykresie.
Na przykład, aby zrozumieć, jeśli węzły są w stanie awarii. Korzystając z agregacji **Niegotówka,** można szybko zobaczyć liczbę węzłów w klastrze obecnie w stanie **Nie gotowy.**

### <a name="active-pod-count"></a>Liczba aktywnych zasobników

Ten wykres wydajności mapuje do `kubectl get pods –all-namespaces` odpowiednika wywoływania i mapuje kolumnę **STATUS,** w ramach tej kolumny, która jest pogrupowana według typów stanu.

![Kubectl uzyskać przykładowe wyniki strąków](./media/container-insights-livedata-metrics/kubectl-get-pods-example.png)

![Wykres liczby zasobników węzłów](./media/container-insights-livedata-metrics/cluster-view-node-pod-count.png)

>[!NOTE]
>Nazwy stanu interpretowane `kubectl` przez może nie dokładnie odpowiadać na wykresie. 

## <a name="next-steps"></a>Następne kroki

Wyświetlanie [przykładów zapytań dziennika,](container-insights-log-search.md#search-logs-to-analyze-data) aby wyświetlić wstępnie zdefiniowane kwerendy i przykłady do tworzenia alertów, wizualizacji lub wykonywania dalszej analizy klastrów.
