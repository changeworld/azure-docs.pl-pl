---
title: Usługa Application Insights umożliwia monitorowanie usługi Azure Kubernetes Service (AKS) lub innych aplikacji hostowanych przez usługę Kubernetes — Azure Monitor | Dokumenty firmy Microsoft
description: Usługa Azure Monitor używa technologii siatki usług, Istio, w klastrze kubernetes, aby zapewnić monitorowanie aplikacji dla dowolnej aplikacji hostowanej przez kubernetes. Dzięki temu można zbierać dane telemetryczne usługi Application Insights dotyczące żądań przychodzących i wychodzących do i z zasobników uruchomionych w klastrze.
ms.topic: conceptual
author: tokaplan
ms.author: alkaplan
ms.date: 04/25/2019
ms.openlocfilehash: 56a0cb66f5b54c817067970ab369d7ca471a1696
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132344"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes-hosted-applications"></a>Zero monitorowania aplikacji oprzyrządowania dla aplikacji hostowanych kubernetes

> [!IMPORTANT]
> Ta funkcja jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Usługa Azure Monitor korzysta teraz z technologii siatki usług w klastrze usługi Kubernetes, aby zapewnić monitorowanie aplikacji po wyjęciu z pudełka dla dowolnej aplikacji hostowanego przez usługę Kubernetes. Z domyślnymi funkcjami application insight, takimi jak [Mapa aplikacji](../../azure-monitor/app/app-map.md) do modelowania zależności, [Strumień metryk](../../azure-monitor/app/live-stream.md) na żywo do monitorowania w czasie rzeczywistym, zaawansowane wizualizacje z [domyślnym pulpitem nawigacyjnym,](../../azure-monitor/app/overview-dashboard.md) [Eksplorator metryk](../../azure-monitor/platform/metrics-getting-started.md)i [Skoroszyty](../../azure-monitor/app/usage-workbooks.md). Ta funkcja pomoże użytkownikom wykryć wąskie gardła wydajności i punkty aktywne awarii we wszystkich swoich obciążeniach kubernetes w wybranym obszarze nazw usługi Kubernetes. Korzystając z istniejących inwestycji w siatkę usług za pomocą technologii takich jak Istio, usługa Azure Monitor umożliwia automatyczne monitorowanie aplikacji bez żadnych modyfikacji kodu aplikacji.

> [!NOTE]
> Jest to jeden z wielu sposobów, aby wykonać monitorowanie aplikacji w u kubernetes.Można również instrumentować dowolną aplikację hostowane w usłudze Kubernetes przy użyciu [SDK usługi Application Insights](../../azure-monitor/azure-monitor-app-hub.yml) bez konieczności siatki usługi. Aby monitorować kubernetes bez instrumentowania aplikacji za pomocą SDK można użyć poniższej metody.

## <a name="prerequisites"></a>Wymagania wstępne

- [Klaster Kubernetes](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads).
- Dostęp konsoli do klastra do uruchomienia *kubectl*.
- [Zasób wgląd w aplikacje](create-new-resource.md)
- Posiadaj siatkę serwisową. Jeśli klaster nie ma wdrożonego programu Istio, możesz dowiedzieć się, jak [zainstalować i używać programu Istio w usłudze Azure Kubernetes.](https://docs.microsoft.com/azure/aks/istio-install)

## <a name="capabilities"></a>Możliwości

Korzystając z monitorowania aplikacji oprzyrządowania zero dla aplikacji hostowanych przez kubernetes, można użyć:

- [Mapa aplikacji](../../azure-monitor/app/app-map.md)
- [Metryki transmisji na żywo](../../azure-monitor/app/live-stream.md)
- [Pulpity nawigacyjne](../../azure-monitor/app/overview-dashboard.md)
- [Eksplorator metryk](../../azure-monitor/platform/metrics-getting-started.md)
- [Śledzenie rozproszone](../../azure-monitor/app/distributed-tracing.md)
- [Kompleksowe monitorowanie transakcji](../../azure-monitor/learn/tutorial-performance.md#identify-slow-server-operations)

## <a name="installation-steps"></a>Kroki instalacji

Aby włączyć rozwiązanie, będziemy wykonywać następujące kroki:
- Wdrażanie aplikacji (jeśli nie jest już wdrożone).
- Upewnij się, że aplikacja jest częścią siatki serwisowej.
- Obserwuj zebrane dane telemetryczne.

### <a name="configure-your-app-to-work-with-a-service-mesh"></a>Konfigurowanie aplikacji do pracy z siatką usługi

Istio obsługuje dwa sposoby [oprzyrządowania kapsuły.](https://istio.io/docs/setup/kubernetes/additional-setup/sidecar-injection/)
W większości przypadków najłatwiej jest oznaczyć obszar nazw Kubernetes zawierający aplikację etykietą *istio-injection:*

```console
kubectl label namespace <my-app-namespace> istio-injection=enabled
```

> [!NOTE]
> Ponieważ siatka serwisowa podnosi dane z sieci, nie możemy przechwycić zaszyfrowanego ruchu. W przypadku ruchu, który nie opuszcza klastra, użyj niezaszyfrowanego protokołu (na przykład HTTP). W przypadku ruchu zewnętrznego, który musi być zaszyfrowany, należy rozważyć [skonfigurowanie zakończenia TLS](https://kubernetes.io/docs/concepts/services-networking/ingress/#tls) na kontrolerze transferu danych przychodzących.

Aplikacje działające poza siatką usług nie są zagrożone.

### <a name="deploy-your-application"></a>Wdrażanie aplikacji

- Wdrażanie aplikacji w obszarze *nazw my-app-namespace.* Jeśli aplikacja jest już wdrożona, a następnie metoda automatycznego wtrysku bocznego opisana powyżej, należy odtworzyć zasobników, aby upewnić się, że Istio wstrzykuje swój sidecar; inicjować aktualizację stopniową lub usuwać poszczególne zasobniki i czekać na ich odtworzenie.
- Upewnij się, że aplikacja jest zgodna z [wymaganiami istio](https://istio.io/docs/setup/kubernetes/prepare/requirements/).

### <a name="deploy-zero-instrumentation-application-monitoring-for-kubernetes-hosted-apps"></a>Wdrażanie monitorowania aplikacji o zerowej oprzyrządowaniu dla aplikacji hostowanych przez kubernetes

1. Pobierz i wyodrębnij wersję [ *karty usługi Application Insights* ](https://github.com/Microsoft/Application-Insights-Istio-Adapter/releases/).
2. Przejdź do */src/kubernetes/* wewnątrz folderu wydania.
3. Edycja *aplikacji-insights-istio-mixer-adapter-deployment.yaml*
    - edytować wartość zmiennej środowiskowej *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY,* aby zawierać klucz instrumentacji zasobu usługi Application Insights w witrynie Azure Portal, aby zawierać dane telemetryczne.
    - W razie potrzeby należy edytować wartość *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* zmiennej środowiskowej, aby zawierała oddzieloną przecinkami listę obszarów nazw, dla których chcesz włączyć monitorowanie. Pozostaw puste miejsce, aby monitorować wszystkie obszary nazw.
4. Zastosuj *każdy* plik YAML znaleziony pod *src/kubernetes/* uruchamiając następujące (nadal musisz znajdować się wewnątrz */src/kubernetes/*):

   ```console
   kubectl apply -f .
   ```

### <a name="verify-deployment"></a>Weryfikowanie wdrożenia

- Upewnij się, że karta usługi Application Insights została wdrożona:

  ```console
  kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
  ```
> [!NOTE]
> W niektórych przypadkach wymagane jest dostrajanie. Aby uwzględnić lub wykluczyć dane telemetryczne dla pojedynczej zasobnika z zebranych, użyj *etykiety appinsights/monitoring.enabled* w tej zasobniku. Będzie to miało pierwszeństwo przed całą konfiguracją opartą na przestrzeni nazw. Ustaw *appinsights/monitoring.enabled* *true,* aby uwzględnić zasobnik i *false,* aby go wykluczyć.

### <a name="view-application-insights-telemetry"></a>Wyświetlanie danych telemetrycznych usługi Application Insights

- Wygeneruj przykładowe żądanie dla aplikacji, aby potwierdzić, że monitorowanie działa poprawnie.
- W ciągu 3-5 minut należy rozpocząć wyświetlanie danych telemetrycznych w witrynie Azure portal. Pamiętaj, aby sprawdzić sekcję *Mapy aplikacji* zasobu usługi Application Insights w portalu.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Poniżej znajduje się przepływ rozwiązywania problemów do użycia, gdy dane telemetryczne nie są wyświetlane w witrynie Azure portal zgodnie z oczekiwaniami.

1. Upewnij się, że aplikacja jest pod obciążeniem i wysyła/odbiera żądania w zwykły HTTP. Ponieważ dane telemetryczne są unoszone z sieci, zaszyfrowany ruch nie jest obsługiwany. Jeśli nie ma żadnych żądań przychodzących lub wychodzących, nie będzie żadnych danych telemetrycznych albo.
2. Upewnij się, że właściwy klucz przyrządzowania znajduje się w *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* zmiennej środowiskowej w *application-insights-istio-mixer-adapter-deployment.yaml*. Klucz instrumentacji znajduje się na karcie *Przegląd* zasobu usługi Application Insights w witrynie Azure portal.
3. Upewnij się, że w ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES zmiennej środowiskowej *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* w *pliku application-insights-istio-mixer-adapter-deployment.yaml*znajduje się poprawna przestrzeń nazw kubernetes. Pozostaw puste miejsce, aby monitorować wszystkie obszary nazw.
4. Upewnij się, że strąki aplikacji zostały wstrzyknięte przez firmę Istio. Sprawdź, czy boczna rzesza Istio istnieje na każdej kapsułce.

   ```console
   kubectl describe pod -n <my-app-namespace> <my-app-pod-name>
   ```
   Sprawdź, czy istnieje kontener o nazwie *istio-proxy* uruchomiony w zasobniku.

5. Wyświetlanie śladów karty usługi Application Insights.

   ```console
   kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
   kubectl logs -n istio-system application-insights-istio-mixer-adapter-<fill in from previous command output>
   ```

   Liczba odebranych elementów telemetrycznych jest aktualizowana raz na minutę. Jeśli nie rośnie minuta po minucie — nie jest wysyłana do karty przez Istio.
   Poszukaj błędów w dzienniku.
6. Jeśli ustalono, że *aplikacja Insight dla karty Kubernetes* nie jest podawany telemetrii, sprawdź Istio's Mixer dzienniki, aby dowiedzieć się, dlaczego nie jest wysyłanie danych do karty:

   ```console
   kubectl get pods -n istio-system -l "istio=mixer,app=telemetry"
   kubectl logs -n istio-system istio-telemetry-<fill in from previous command output> -c mixer
   ```
   Poszukaj błędów, szczególnie odnoszących się do komunikacji z *adapterem applicationinsightsadapter.*

## <a name="faq"></a>Najczęściej zadawane pytania

Aby uzyskać najnowsze informacje na temat postępów w tym projekcie, odwiedź [kartę Usługi Application Insights dla programu GitHub projektu Istio Mixer.](https://github.com/Microsoft/Application-Insights-Istio-Adapter/blob/master/SETUP.md#faq)

## <a name="uninstall"></a>Dezinstalacja

Aby odinstalować produkt, dla *każdego* pliku YAML znalezionego pod *src/kubernetes/* run:

```console
kubectl delete -f <filename.yaml>
```


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o tym, jak usługa Azure Monitor i kontenery współpracują ze sobą, odwiedź [witrynę Azure Monitor w celu zapoznania się z omówieniem kontenerów](../../azure-monitor/insights/container-insights-overview.md)
