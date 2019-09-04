---
title: Rejestrowanie wystąpienia kontenera za pomocą dzienników usługi Azure Monitor
description: Dowiedz się, jak wysyłać dzienniki z usługi Azure Container Instances do dzienników Azure Monitor.
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: overview
ms.date: 09/02/2019
ms.author: danlep
ms.openlocfilehash: 1c4846414036e86d460d9abe0bd93e785e710395
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70258456"
---
# <a name="container-instance-logging-with-azure-monitor-logs"></a>Rejestrowanie wystąpienia kontenera za pomocą dzienników usługi Azure Monitor

Obszary robocze Log Analytics zapewniają scentralizowaną lokalizację do przechowywania i wykonywania zapytań dotyczących danych dzienników tylko z zasobów platformy Azure, ale także zasobów i zasobów lokalnych w innych chmurach. Azure Container Instances zawiera wbudowaną obsługę wysyłania dzienników i danych zdarzeń do dzienników Azure Monitor.

Aby wysłać dziennik grupy kontenerów i dane zdarzeń do dzienników Azure Monitor, należy określić identyfikator obszaru roboczego Log Analytics i klucz obszaru roboczego podczas tworzenia grupy kontenerów. W poniższych sekcjach opisano tworzenie grupy kontenerów z włączonym rejestrowaniem oraz dzienników wykonujących zapytania.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
> Obecnie można wysyłać tylko dane zdarzeń z wystąpień kontenera systemu Linux do Log Analytics.

## <a name="prerequisites"></a>Wymagania wstępne

Aby włączyć rejestrowanie w wystąpieniach kontenera, potrzebne są następujące elementy:

* [Obszar roboczy usługi Log Analytics](../azure-monitor/learn/quick-create-workspace.md)
* [Interfejs poziomu wywołania platformy Azure](/cli/azure/install-azure-cli) (lub usługa [Cloud Shell](/azure/cloud-shell/overview))

## <a name="get-log-analytics-credentials"></a>Uzyskiwanie poświadczeń usługi Log Analytics

Usługa Azure Container Instances wymaga uprawnień do wysyłania danych do obszaru roboczego usługi Log Analytics. Aby nadać to uprawnienie i włączyć rejestrowanie, należy podać identyfikator obszaru roboczego usługi Log Analytics i jeden z jego kluczy (podstawowy lub pomocniczy) podczas tworzenia grupy kontenerów.

Aby uzyskać identyfikator obszaru roboczego analizy dzienników i klucz podstawowy:

1. W witrynie Azure Portal przejdź do obszaru roboczego usługi Log Analytics
1. W obszarze **Ustawienia**wybierz pozycję **Ustawienia zaawansowane** .
1. Wybierz opcje **Połączone źródła** > **Serwery z systemem Windows** (lub **Serwery z systemem Linux** — identyfikator i klucze są takie same dla obu opcji)
1. Zwróć uwagę na następujące elementy:
   * **WORKSPACE ID** (IDENTYFIKATOR OBSZARU ROBOCZEGO)
   * **PRIMARY KEY** (KLUCZ PODSTAWOWY)

## <a name="create-container-group"></a>Tworzenie grupy kontenerów

Teraz, gdy masz identyfikator obszaru roboczego analizy dzienników i klucz podstawowy, możesz utworzyć grupę kontenerów z włączonym rejestrowaniem.

W poniższych przykładach pokazano dwa sposoby tworzenia grupy kontenerów [z pojedynczym][fluentd] postawionym kontenerem: za pomocą interfejsu wiersza polecenia platformy Azure i interfejsu wiersza polecenia platformy Azure przy użyciu szablonu YAML. Kontener Fluentd tworzy kilka wierszy danych wyjściowych w konfiguracji domyślnej. Ponieważ te dane wyjściowe są wysyłane do obszaru roboczego usługi Log Analytics, są przydatne do przedstawiania wyświetlania i wykonywania zapytań przez dzienniki.

### <a name="deploy-with-azure-cli"></a>Wdrażanie przy użyciu interfejsu wiersza polecenia platformy Azure

Aby wdrożyć za pomocą interfejsu wiersza polecenia platformy Azure `--log-analytics-workspace` , `--log-analytics-workspace-key` Określ parametry i w poleceniu [AZ Container Create][az-container-create] . Przed uruchomieniem następującego polecenia należy zastąpić te dwie wartości obszaru roboczego wartościami uzyskanymi w poprzednim kroku (i zaktualizować nazwę grupy zasobów).

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
apiVersion: 2018-10-01
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

Następnie wykonaj następujące polecenie, aby wdrożyć grupę kontenerów. Zamień `myResourceGroup` na grupę zasobów w ramach subskrypcji (lub najpierw utwórz grupę zasobów o nazwie "Grupa zasobów"):

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainergroup001 --file deploy-aci.yaml
```

Zaraz po wykonaniu polecenia powinna pojawić się odpowiedź z usługi Azure zawierająca szczegóły wdrożenia.

## <a name="view-logs"></a>Wyświetlanie dzienników

Po wdrożeniu grupy kontenerów, może upłynąć kilka minut (do 10), zanim pierwsze wpisy dziennika będą widoczne w witrynie Azure Portal. Aby wyświetlić dzienniki grupy kontenerów w `ContainerInstanceLog_CL` tabeli:

1. W witrynie Azure Portal przejdź do obszaru roboczego usługi Log Analytics
1. W obszarze **Ogólne**wybierz pozycję **dzienniki** .  
1. Wpisz następujące zapytanie:`ContainerInstanceLog_CL | limit 50`
1. Wybierz pozycję **Uruchom**

Należy zobaczyć kilka wyników wyświetlanych przez zapytanie. Jeśli pierwsze wyniki nie są widoczne, odczekaj kilka minut, a następnie wybierz przycisk **Uruchom** , aby ponownie uruchomić zapytanie. Domyślnie wpisy dziennika są wyświetlane w formacie **tabeli** . Następnie możesz rozwijać wiersze, aby wyświetlić zawartość poszczególnych wpisów dziennika.

![Wyniki przeszukiwania dzienników w witrynie Azure Portal][log-search-01]

## <a name="view-events"></a>Wyświetlanie zdarzeń

Możesz również wyświetlać zdarzenia dla wystąpień kontenera w Azure Portal. Zdarzenia obejmują godzinę utworzenia wystąpienia i momentu jego uruchomienia. Aby wyświetlić dane zdarzenia w `ContainerEvent_CL` tabeli:

1. W witrynie Azure Portal przejdź do obszaru roboczego usługi Log Analytics
1. W obszarze **Ogólne**wybierz pozycję **dzienniki** .  
1. Wpisz następujące zapytanie:`ContainerEvent_CL | limit 50`
1. Wybierz pozycję **Uruchom**

Należy zobaczyć kilka wyników wyświetlanych przez zapytanie. Jeśli pierwsze wyniki nie są widoczne, odczekaj kilka minut, a następnie wybierz przycisk **Uruchom** , aby ponownie uruchomić zapytanie. Domyślnie wpisy są wyświetlane w formacie **tabeli** . Następnie można rozwinąć wiersz, aby zobaczyć zawartość pojedynczego wpisu.

![Wyniki wyszukiwania zdarzeń w Azure Portal][log-search-02]

## <a name="query-container-logs"></a>Wykonywanie zapytań dla dzienników kontenerów

Dzienniki Azure Monitor zawierają obszerny [język zapytań][query_lang] służący do ściągania informacji z potencjalnie tysięcy wierszy danych wyjściowych dziennika.

Podstawowa struktura zapytania jest tabelą źródłową (w tym artykule lub `ContainerInstanceLog_CL` `ContainerEvent_CL`), po której następuje seria operatorów oddzielonych znakiem potoku (`|`). Można połączyć kilka operatorów, aby dostosować wyniki i wykonać funkcje zaawansowane.

Aby wyświetlić przykładowe wyniki zapytania, wklej następujące zapytanie do pola tekstowego zapytania, a następnie wybierz przycisk **Run (Uruchom** ), aby wykonać zapytanie. To zapytanie wyświetla wszystkie wpisy dziennika, których pole „Message” zawiera słowo "warn":

```query
ContainerInstanceLog_CL
| where Message contains "warn"
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
[log-search-02]: ./media/container-instances-log-analytics/portal-query-02.png

<!-- LINKS - External -->
[fluentd]: https://hub.docker.com/r/fluent/fluentd/
[query_lang]: https://aka.ms/LogAnalyticsLanguage

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create