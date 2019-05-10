---
title: Usługa Azure Monitor — Zero instrumentacji aplikacji, monitorowanie usługi Kubernetes aplikacji hostowanych | Dokumentacja firmy Microsoft
description: Zero instrumentacji aplikacji, monitorowanie dla aplikacji hostowanej platformy Kubernetes jest rozwiązania do monitorowania, która pozwala na zbieranie danych telemetrycznych usługi Application Insights odnoszących się do żądań przychodzących i wychodzących do i z zasobników działającym w klastrze Kubernetes Korzystanie z technologii siatki usługi o nazwie Istio.
services: application-insights
author: tokaplan
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: alkaplan
ms.openlocfilehash: 42b81ec0fa01841791a5b2651d1c1189db5e27ff
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65408206"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes-hosted-apps"></a>Aplikacji hostowanych w monitorowania zero instrumentacji aplikacji dla rozwiązania Kubernetes

> [!IMPORTANT]
> Ta funkcja jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Usługa Azure Monitor korzysta teraz tech siatki usługi w klastrze Kubernetes zapewnienie poza pole monitorowania aplikacji dla dowolnej aplikacji hostowanej platformy Kubernetes. Za pomocą domyślnej funkcji Application Insights, takich jak [mapy aplikacji](../../azure-monitor/app/app-map.md) do modelowania zależności, [Live Stream metryki](../../azure-monitor/app/live-stream.md) monitorowania w czasie rzeczywistym, zaawansowane wizualizacje z [domyślne pulpit nawigacyjny](../../azure-monitor/app/overview-dashboard.md), [Eksploratora metryk](../../azure-monitor/platform/metrics-getting-started.md), i [skoroszyty](../../azure-monitor/app/usage-workbooks.md). Ta funkcja może pomóc wąskich gardeł wydajności dodatkowych użytkowników i punktów aktywnych błędów dla wszystkich obciążeń Kubernetes w wybranej przestrzeni nazw Kubernetes. Wykorzystując istniejące inwestycje siatki usługi przy użyciu technologii, takich jak Istio, usługi Azure Monitor umożliwia monitorowanie aplikacji Instrumentacji automatycznie bez żadnych modyfikacji kodu aplikacji.

> [!NOTE]
> Jest to jedna z wielu sposobów wykonania monitorowania aplikacji na platformie Kubernetes. Można również Instrumentacji dowolnej aplikacji hostowanej w usłudze Kubernetes za pomocą [zestawu SDK usługi Application Insights](../../azure-monitor/azure-monitor-app-hub.md) bez konieczności siatki usługi. Aby monitorować usługę Kubernetes bez instrumentacji aplikacji za pomocą zestawu SDK, można użyć poniższych metod.

## <a name="prerequisites"></a>Wymagania wstępne

- A [klastra Kubernetes](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads).
- Dostęp do klastra, aby uruchomić konsolę *kubectl*.
- [Zasobu Application Insights](create-new-resource.md)
- Zawierać oczek usługi. Jeśli klaster nie ma Istio wdrożony, możesz dowiedzieć się jak [zainstalować i używać Istio w usłudze Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/istio-install).

## <a name="capabilities"></a>Możliwości

Za pomocą zero instrumentacji aplikacji, monitorowanie usługi Kubernetes hostowanej aplikacji, można użyć:

- [Mapa aplikacji](../../azure-monitor/app/app-map.md)
- [Stream metryki na żywo](../../azure-monitor/app/live-stream.md)
- [Pulpity nawigacyjne](../../azure-monitor/app/overview-dashboard.md)
- [Eksplorator metryk](../../azure-monitor/platform/metrics-getting-started.md)
- [Rozproszone śledzenie](../../azure-monitor/app/distributed-tracing.md)
- [Monitorowanie transakcji end-to-end](../../azure-monitor/learn/tutorial-performance.md#identify-slow-server-operations)

## <a name="installation-steps"></a>Kroki instalacji

Aby włączyć rozwiązanie, firma Microsoft będzie wykonywać następujące czynności:
- Wdróż aplikację (jeśli jeszcze nie wdrożono).
- Upewnij się, że aplikacja jest częścią siatki usługi.
- Obserwuj zebranych danych telemetrycznych.

### <a name="configure-your-app-to-work-with-a-service-mesh"></a>Konfigurowanie aplikacji do pracy z siatki usługi

Istio obsługuje dwa sposoby [Instrumentacji zasobnik](https://istio.io/docs/setup/kubernetes/additional-setup/sidecar-injection/).
W większości przypadków jest najłatwiejsza do oznaczania przestrzeni nazw Kubernetes zawierającego aplikacji za pomocą *istio iniekcji* etykiety:

```console
kubectl label namespace <my-app-namespace> istio-injection=enabled
```

> [!NOTE]
> Ponieważ usługa siatki windy danych wyłączone podczas transmisji, firma Microsoft nie może przechwycić zaszyfrowany ruch sieciowy. W przypadku ruchu, który nie pozostawi klastra użyj nieszyfrowanego protokołu (na przykład HTTP). W przypadku ruchu zewnętrznego, który musi być szyfrowana, rozważ [Konfigurowanie kończenia żądań SSL](https://kubernetes.io/docs/concepts/services-networking/ingress/#tls) u kontrolera danych przychodzących.

Nie wpływa na aplikacje działające poza siatki usługi.

### <a name="deploy-your-application"></a>Wdrażanie aplikacji

- Wdrażanie aplikacji w usłudze *nazw my app* przestrzeni nazw. Jeśli aplikacja została już wdrożona, i wykonaniu powyższej metody iniekcji przyczepki automatycznego, należy ponownie utworzyć zasobników, aby upewnić się, że Istio wprowadza jego przyczepki; inicjować aktualizacje stopniowe lub usunąć poszczególne zasobników i poczekać na ich do odtworzenia.
- Upewnij się, aplikacja jest zgodna z [wymagania Istio](https://istio.io/docs/setup/kubernetes/prepare/requirements/).

### <a name="deploy-zero-instrumentation-application-monitoring-for-kubernetes-hosted-apps"></a>Wdrażanie aplikacji instrumentacji, zero monitorowania aplikacji hostowanej platformy Kubernetes

1. Pobierz i Wyodrębnij [ *adapter usługi Application Insights* wersji](https://github.com/Microsoft/Application-Insights-Istio-Adapter/releases/).
2. Przejdź do */src/kubernetes/* znajdujące się w folderze wersji.
3. Edit *application-insights-istio-mixer-adapter-deployment.yaml*
    - Edytuj wartość *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* zmienna środowiskowa zawiera klucz Instrumentacji zasobu usługi Application Insights w witrynie Azure portal, aby zawierała dane telemetryczne.
    - Jeśli to konieczne, należy edytować wartość *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* zmiennej środowiskowej, aby zawierać rozdzielaną przecinkami listę przestrzeni nazw, dla którego chcesz włączyć monitorowanie. Pozostaw pustą wartość, aby monitorować wszystkie obszary nazw.
4. Zastosuj *co* znajdującym się plik YAML *src/kubernetes/* , uruchamiając następujące (użytkownik musi być wewnątrz */src/kubernetes/*):

   ```console
   kubectl apply -f .
   ```

### <a name="verify-deployment"></a>Weryfikowanie wdrożenia

- Upewnij się, że adapter usługi Application Insights została wdrożona:

  ```console
  kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
  ```
> [!NOTE]
> W niektórych przypadkach dostrajanie dostrajania jest wymagana. Aby dołączyć lub wykluczyć dane telemetryczne dotyczące poszczególnych zasobników z są zbierane, należy użyć *appinsights/monitoring.enabled* etykietę, na którym następnie. Spowoduje to mają pierwszeństwo przed wszystkie konfiguracje oparte na przestrzeni nazw. Ustaw *appinsights/monitoring.enabled* do *true* obejmujący zasobnik, a *false* celu wykluczenia go.

### <a name="view-application-insights-telemetry"></a>Wyświetlanie danych telemetrycznych z usługi Application Insights

- Generowanie przykładowe żądanie twoją aplikację, aby upewnić się, że monitorowanie działa prawidłowo.
- W ciągu 3 – 5 minut powinny zacząć się wyświetlać dane telemetryczne są wyświetlane w witrynie Azure portal. Pamiętaj sprawdzić *mapy aplikacji* części zasobu usługi Application Insights w portalu.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Poniżej rozwiązywania problemów z przepływem do użycia podczas telemetrii nie pojawia się w witrynie Azure portal jako oczekuje.

1. Upewnij się, aplikacja jest obciążony i jest wysyłanie i odbieranie żądań w zwykłego protokołu HTTP. Ponieważ dane telemetryczne zostało zniesione wyłączone podczas transmisji, zaszyfrowany ruch sieciowy nie jest obsługiwane. Jeśli brak żądań przychodzących lub wychodzących, nie będzie żadnych danych telemetrycznych albo.
2. Upewnij się, jest podawany jako klucz Instrumentacji poprawne *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* zmiennej środowiskowej w *application-insights-istio-mixer-adapter-deployment.yaml*. Klucz Instrumentacji znajduje się na *Przegląd* kartę zasób usługi Application Insights w witrynie Azure portal.
3. Upewnij się, poprawną przestrzeń nazw Kubernetes znajduje się w *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* zmiennej środowiskowej w *application-insights-istio-mixer-adapter-deployment.yaml*. Pozostaw pustą wartość, aby monitorować wszystkie obszary nazw.
4. Upewnij się, że zasobników aplikacji zostało przyczepki wprowadzony przez Istio. Sprawdź, czy przyczepki firmy Istio istnieje na każdym pod.

   ```console
   kubectl describe pod -n <my-app-namespace> <my-app-pod-name>
   ```
   Sprawdź, czy kontener o nazwie *istio proxy* systemem zasobnik.

5. Wyświetlanie śladów adapter usługi Application Insights.

   ```console
   kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
   kubectl logs -n istio-system application-insights-istio-mixer-adapter-<fill in from previous command output>
   ```

   Liczba elementów odebrane dane telemetryczne są aktualizowane raz na minutę. Jeśli go nie rosnąć minutę minuta — Brak telemetrii jest wysyłany do karty przez Istio.
   Zwróć uwagę na wszelkie błędy w dzienniku.
6. Jeśli ustalono, *Application Insights dla platformy Kubernetes* adapter nie jest on podawany telemetrii, sprawdź dzienniki Mixer Istio firmy, aby ustalić, dlaczego nie wysyła dane do karty sieciowej:

   ```console
   kubectl get pods -n istio-system -l "istio=mixer,app=telemetry"
   kubectl logs -n istio-system istio-telemetry-<fill in from previous command output> -c mixer
   ```
   Znajdź błędy, szczególnie odnoszących się do komunikacji z *applicationinsightsadapter* karty.

## <a name="faq"></a>Często zadawane pytania

Aby uzyskać najnowsze informacje dla postępu w ramach tego projektu, odwiedź stronę [adapter usługi Application Insights dla usługi Mixer Istio projektu GitHub](https://github.com/Microsoft/Application-Insights-Istio-Adapter/blob/master/SETUP.md#faq).

## <a name="uninstall"></a>Odinstaluj

Do odinstalowania produktu, aby uzyskać *co* znajdującym się plik YAML *src/kubernetes/* Uruchom:

```console
kubectl delete -f <filename.yaml>
```


## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat usługi Azure Monitor kontenery współdziałanie i odwiedź stronę [usługi Azure Monitor, aby uzyskać przegląd kontenerów](../../azure-monitor/insights/container-insights-overview.md)