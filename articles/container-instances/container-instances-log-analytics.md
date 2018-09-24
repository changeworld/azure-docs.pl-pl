---
title: Rejestrowanie wystąpienia kontenera za pomocą usługi Azure Log Analytics
description: Dowiedz się, jak wysyłać dane wyjściowe kontenera (STDOUT i STDERR) do usługi Azure Log Analytics.
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: overview
ms.date: 07/17/2018
ms.author: marsma
ms.openlocfilehash: 9d967cc7509ef0d558327da2299b0478b62de430
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46960723"
---
# <a name="container-instance-logging-with-azure-log-analytics"></a>Rejestrowanie wystąpienia kontenera za pomocą usługi Azure Log Analytics

Obszary robocze usługi Log Analytics zapewniają centralną lokalizację do przechowywania i badania danych dziennika nie tylko z zasobów platformy Azure, ale również z zasobów lokalnych i zasobów w innych chmurach. Usługa Azure Container Instances obejmuje wbudowaną obsługę wysyłania danych do usługi Log Analytics.

Aby wysłać dane wystąpienia kontenera do usługi Log Analytics, należy utworzyć grupę kontenerów przy użyciu interfejsu wiersza polecenia platformy Azure (lub usługi Cloud Shell) i pliku YAML. W poniższych sekcjach opisano tworzenie grupy kontenerów z włączonym rejestrowaniem oraz dzienników wykonujących zapytania.

## <a name="prerequisites"></a>Wymagania wstępne

Aby włączyć rejestrowanie w wystąpieniach kontenera, potrzebne są następujące elementy:

* [Obszar roboczy usługi Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md)
* [Interfejs poziomu wywołania platformy Azure](/cli/azure/install-azure-cli) (lub usługa [Cloud Shell](/azure/cloud-shell/overview))

## <a name="get-log-analytics-credentials"></a>Uzyskiwanie poświadczeń usługi Log Analytics

Usługa Azure Container Instances wymaga uprawnień do wysyłania danych do obszaru roboczego usługi Log Analytics. Aby nadać to uprawnienie i włączyć rejestrowanie, należy podać identyfikator obszaru roboczego usługi Log Analytics i jeden z jego kluczy (podstawowy lub pomocniczy) podczas tworzenia grupy kontenerów.

Aby uzyskać identyfikator obszaru roboczego usługi Log Analytics i klucz podstawowy:

1. W witrynie Azure Portal przejdź do obszaru roboczego usługi Log Analytics
1. W obszarze **USTAWIENIA** wybierz pozycję **Ustawienia zaawansowane**
1. Wybierz opcje **Połączone źródła** > **Serwery z systemem Windows** (lub **Serwery z systemem Linux** — identyfikator i klucze są takie same dla obu opcji)
1. Zwróć uwagę na następujące elementy:
   * **WORKSPACE ID** (IDENTYFIKATOR OBSZARU ROBOCZEGO)
   * **PRIMARY KEY** (KLUCZ PODSTAWOWY)

## <a name="create-container-group"></a>Tworzenie grupy kontenerów

Teraz, gdy masz identyfikator obszaru roboczego usługi Log Analytics i klucz podstawowy, możesz utworzyć grupę kontenerów z włączonym rejestrowaniem.

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

## <a name="view-logs-in-log-analytics"></a>Wyświetlanie dzienników w usłudze Log Analytics

Po wdrożeniu grupy kontenerów, może upłynąć kilka minut (do 10), zanim pierwsze wpisy dziennika będą widoczne w witrynie Azure Portal. Aby wyświetlić dzienniki grupy kontenerów, otwórz obszar roboczy usługi Log Analytics, następnie:

1. W omówieniu **Obszar roboczy OMS** wybierz pozycję **Wyszukiwanie dziennika**
1. W obszarze **Kilka innych zapytań, które możesz wypróbować** wybierz link **Wszystkie zebrane dane**

Powinno pojawić się kilka wyników dla każdego zapytania `search *`. Jeśli na początku nie widać żadnych wyników, zaczekaj kilka minut, a następnie wybierz przycisk **RUN** (URUCHOM), aby wykonać zapytanie ponownie. Domyślnie wpisy dziennika są wyświetlane w widoku „Lista” — wybierz pozycję **Tabela**, aby zobaczyć wpisy dziennika w bardziej skróconym formacie. Następnie możesz rozwijać wiersze, aby wyświetlić zawartość poszczególnych wpisów dziennika.

![Wyniki przeszukiwania dzienników w witrynie Azure Portal][log-search-01]

## <a name="query-container-logs"></a>Wykonywanie zapytań dla dzienników kontenerów

Usługa Log Analytics obejmuje rozszerzony [język zapytań][query_lang] do ściągania informacji nawet z tysięcy wierszy danych wyjściowych dziennika.

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

### <a name="log-analytics"></a>Log Analytics

Aby uzyskać więcej informacji dotyczących wykonywania zapytań dla dzienników i konfigurowania alertów w usłudze Azure Log Analytics, zobacz:

* [Understanding log searches in Log Analytics](../log-analytics/log-analytics-log-search.md) (Interpretacja przeszukiwania dzienników w usłudze Log Analytics)
* [Unified alerts in Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md) (Ujednolicone alerty na platformie Azure Monitor)

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