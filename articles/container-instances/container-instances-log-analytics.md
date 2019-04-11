---
title: Rejestrowanie wystąpienia kontenera za pomocą dzienników usługi Azure Monitor
description: Dowiedz się, jak wysyłać dane wyjściowe kontenera (STDOUT i STDERR) do dzienników usługi Azure Monitor.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: overview
ms.date: 07/17/2018
ms.author: danlep
ms.openlocfilehash: 13f1fa92365c284ed10bd7c0a1b2fdefef50b29e
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2019
ms.locfileid: "56879717"
---
# <a name="container-instance-logging-with-azure-monitor-logs"></a>Rejestrowanie wystąpienia kontenera za pomocą dzienników usługi Azure Monitor

Obszary robocze usługi Log Analytics zapewniają centralną lokalizację do przechowywania i badania danych dziennika nie tylko z zasobów platformy Azure, ale również z zasobów lokalnych i zasobów w innych chmurach. Usługa Azure Container Instances obejmuje wbudowaną obsługę wysyłania danych do dzienników usługi Azure Monitor.

Aby wysłać dane wystąpienia kontenera do dzienników usługi Azure Monitor, należy utworzyć grupę kontenerów przy użyciu interfejsu wiersza polecenia platformy Azure (lub usługi Cloud Shell) i pliku YAML. W poniższych sekcjach opisano tworzenie grupy kontenerów z włączonym rejestrowaniem oraz dzienników wykonujących zapytania.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby włączyć rejestrowanie w wystąpieniach kontenera, potrzebne są następujące elementy:

* [Obszar roboczy usługi Log Analytics](../azure-monitor/learn/quick-create-workspace.md)
* [Interfejs poziomu wywołania platformy Azure](/cli/azure/install-azure-cli) (lub usługa [Cloud Shell](/azure/cloud-shell/overview))

## <a name="get-log-analytics-credentials"></a>Uzyskiwanie poświadczeń usługi Log Analytics

Usługa Azure Container Instances wymaga uprawnień do wysyłania danych do obszaru roboczego usługi Log Analytics. Aby nadać to uprawnienie i włączyć rejestrowanie, należy podać identyfikator obszaru roboczego usługi Log Analytics i jeden z jego kluczy (podstawowy lub pomocniczy) podczas tworzenia grupy kontenerów.

Aby uzyskać identyfikator obszaru roboczego analizy dzienników i klucz podstawowy:

1. W witrynie Azure Portal przejdź do obszaru roboczego usługi Log Analytics
1. W obszarze **USTAWIENIA** wybierz pozycję **Ustawienia zaawansowane**
1. Wybierz opcje **Połączone źródła** > **Serwery z systemem Windows** (lub **Serwery z systemem Linux** — identyfikator i klucze są takie same dla obu opcji)
1. Zwróć uwagę na następujące elementy:
   * **WORKSPACE ID** (IDENTYFIKATOR OBSZARU ROBOCZEGO)
   * **PRIMARY KEY** (KLUCZ PODSTAWOWY)

## <a name="create-container-group"></a>Tworzenie grupy kontenerów

Teraz, gdy masz identyfikator obszaru roboczego analizy dzienników i klucz podstawowy, możesz utworzyć grupę kontenerów z włączonym rejestrowaniem.

W poniższych przykładach pokazano dwa sposoby tworzenia grupy kontenerów z jednym kontenerem [fluentd][fluentd]: za pomocą interfejsu wiersza polecenia platformy Azure i interfejsu wiersza polecenia platformy Azure przy użyciu szablonu YAML. Kontener Fluentd tworzy kilka wierszy danych wyjściowych w konfiguracji domyślnej. Ponieważ te dane wyjściowe są wysyłane do obszaru roboczego usługi Log Analytics, są przydatne do przedstawiania wyświetlania i wykonywania zapytań przez dzienniki.

### <a name="deploy-with-azure-cli"></a>Wdrażanie przy użyciu interfejsu wiersza polecenia platformy Azure

Aby przeprowadzić wdrożenie przy użyciu wiersza polecenia platformy Azure, należy określić parametry `--log-analytics-workspace` i `--log-analytics-workspace-key` w poleceniu [az container create][az-container-create]. Przed uruchomieniem następującego polecenia należy zastąpić te dwie wartości obszaru roboczego wartościami uzyskanymi w poprzednim kroku (i zaktualizować nazwę grupy zasobów).

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainergroup001 \
    --image fluent/fluentd \
    --log-analytics-workspace <WORKSPACE_ID> \
    --log-analytics-workspace-key <WORKSPACE_KEY>
```

### <a name="deploy-with-yaml"></a>Wdrażanie za pomocą kodu YAML

Użyj tej metody, jeśli chcesz wdrożyć grupy kontenerów za pomocą kodu YAML. Poniższy kod YAML definiuje grupę kontenerów z jednym kontenerem. Skopiuj kod YAML do nowego pliku, a następnie zastąp parametry `LOG_ANALYTICS_WORKSPACE_ID` i `LOG_ANALYTICS_WORKSPACE_KEY` przy użyciu wartości uzyskanych w poprzednim kroku. Zapisz plik jako **deploy-aci.yaml**.

```yaml
apiVersion: 2018-06-01
location: eastus
name: mycontainergroup001
properties:
  containers:
  - name: mycontainer001
    properties:
      environmentVariables: []
      image: fluent/fluentd
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  osType: Linux
  restartPolicy: Always
  diagnostics:
    logAnalytics:
      workspaceId: LOG_ANALYTICS_WORKSPACE_ID
      workspaceKey: LOG_ANALYTICS_WORKSPACE_KEY
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Następnie uruchom następujące polecenie w celu wdrożenia grupy kontenerów. Zastąp element `myResourceGroup` grupą zasobów w Twojej subskrypcji (lub najpierw utwórz grupę zasobów o nazwie „myResourceGroup”):

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainergroup001 --file deploy-aci.yaml
```

Zaraz po wykonaniu polecenia powinna pojawić się odpowiedź z usługi Azure zawierająca szczegóły wdrożenia.

## <a name="view-logs-in-azure-monitor-logs"></a>Wyświetlanie dzienników w usłudze Azure Monitor

Po wdrożeniu grupy kontenerów, może upłynąć kilka minut (do 10), zanim pierwsze wpisy dziennika będą widoczne w witrynie Azure Portal. Aby wyświetlić dzienniki grupy kontenerów, otwórz obszar roboczy usługi Log Analytics, następnie:

1. W przeglądzie **Obszar roboczy OMS** wybierz pozycję **Wyszukiwanie w dziennikach**. Obszary robocze OMS są teraz nazywane obszarami roboczymi usługi Log Analytics.  
1. W obszarze **Kilka innych zapytań, które możesz wypróbować** wybierz link **Wszystkie zebrane dane**

Powinno pojawić się kilka wyników dla każdego zapytania `search *`. Jeśli na początku nie widać żadnych wyników, zaczekaj kilka minut, a następnie wybierz przycisk **RUN** (URUCHOM), aby wykonać zapytanie ponownie. Domyślnie wpisy dziennika są wyświetlane w widoku „Lista” — wybierz pozycję **Tabela**, aby zobaczyć wpisy dziennika w bardziej skróconym formacie. Następnie możesz rozwijać wiersze, aby wyświetlić zawartość poszczególnych wpisów dziennika.

![Wyniki przeszukiwania dzienników w witrynie Azure Portal][log-search-01]

## <a name="query-container-logs"></a>Wykonywanie zapytań dla dzienników kontenerów

Dzienniki usługi Azure Monitor obejmują rozszerzony [język zapytań][query_lang] do ściągania informacji nawet z tysięcy wierszy danych wyjściowych dziennika.

Agent rejestrowania usługi Azure Container Instances wysyła wpisy do tabeli `ContainerInstanceLog_CL` w obszarze roboczym usługi Log Analytics. Podstawową strukturę zapytania stanowi tabela źródłowa (`ContainerInstanceLog_CL`), po której następuje szereg operatorów oddzielonych znakiem potoku (`|`). Można połączyć kilka operatorów, aby dostosować wyniki i wykonać funkcje zaawansowane.

Aby wyświetlić przykładowe wyniki zapytania, wklej poniższe zapytanie w polu tekstowym zapytania (w obszarze „Pokaż starszą wersję konwertera języka”) i wybierz przycisk **RUN** (URUCHOM), aby wykonać zapytanie. To zapytanie wyświetla wszystkie wpisy dziennika, których pole „Message” zawiera słowo "warn":

```query
ContainerInstanceLog_CL
| where Message contains("warn")
```

Bardziej złożone zapytania są również obsługiwane. Na przykład to zapytanie wyświetla tylko wpisy dziennika dla grupy kontenerów "mycontainergroup001" wygenerowane w ciągu ostatniej godziny:

```query
ContainerInstanceLog_CL
| where (ContainerGroup_s == "mycontainergroup001")
| where (TimeGenerated > ago(1h))
```

## <a name="next-steps"></a>Następne kroki

### <a name="azure-monitor-logs"></a>Dzienniki usługi Azure Monitor

Aby uzyskać więcej informacji dotyczących wykonywania zapytań dla dzienników i konfigurowania alertów w dziennikach usługi Azure Monitor, zobacz:

* [Understanding log searches in Azure Monitor logs (Informacje o przeszukiwaniu dzienników w usłudze Azure Monitor)](../log-analytics/log-analytics-log-search.md)
* [Unified alerts in Azure Monitor](../azure-monitor/platform/alerts-overview.md) (Ujednolicone alerty na platformie Azure Monitor)


### <a name="monitor-container-cpu-and-memory"></a>Monitorowanie procesora i pamięci kontenera

Aby uzyskać informacje na temat monitorowania procesora wystąpienia i zasobów pamięci kontenera, zobacz temat:

* [Monitorowanie zasobów kontenerów w usłudze Azure Container Instances](container-instances-monitor.md).

<!-- IMAGES -->
[log-search-01]: ./media/container-instances-log-analytics/portal-query-01.png

<!-- LINKS - External -->
[fluentd]: https://hub.docker.com/r/fluent/fluentd/
[query_lang]: https://aka.ms/LogAnalyticsLanguage

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create