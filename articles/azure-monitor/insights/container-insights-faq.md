---
title: Azure Monitor for Containers — często zadawane pytania | Microsoft Docs
description: Azure Monitor for Containers to rozwiązanie monitorujące kondycję klastrów AKS i Container Instances na platformie Azure. Ten artykuł zawiera odpowiedzi na często zadawane pytania.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 0984de51221c506bb1824e4dcfd93eef56453a4d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75405079"
---
# <a name="azure-monitor-for-containers-frequently-asked-questions"></a>Azure Monitor for Containers — często zadawane pytania

Ta firma Microsoft — często zadawane pytania dotyczące Azure Monitor kontenerów. Jeśli masz dodatkowe pytania dotyczące rozwiązania, przejdź do [forum dyskusyjnego](https://feedback.azure.com/forums/34192--general-feedback) i Opublikuj swoje pytania. Gdy zadawane pytanie dodajemy go do tego artykułu tak, aby możliwe było szybkie i łatwe.

## <a name="i-dont-see-image-and-name-property-values-populated-when-i-query-the-containerlog-table"></a>Nie widzę wartości właściwości obraz i nazwa wypełniane podczas wykonywania zapytania względem tabeli ContainerLog.

W przypadku agenta w wersji ciprod12042019 i nowszych domyślnie te dwie właściwości nie są wypełniane dla każdego wiersza dziennika, aby zminimalizować koszty związane z zbieranymi danymi dzienników. Istnieją dwie opcje zapytania do tabeli zawierającej te właściwości z ich wartościami:

### <a name="option-1"></a>Opcja 1 

Dołącz inne tabele, aby uwzględnić te wartości właściwości w wynikach.

Zmodyfikuj zapytania, aby uwzględnić właściwości obrazu i ImageTag z tabeli ```ContainerInventory``` przez dołączenie do właściwości ContainerID. Możesz dołączyć Właściwość Name (jak wcześniej pojawiła się w tabeli ```ContainerLog```) z pola ContaineName tabeli KubepodInventory, dołączając Właściwość ContainerID. Jest to zalecana opcja.

Poniższy przykład to przykładowe zapytanie szczegółowe objaśniające, jak uzyskać te wartości pól za pomocą sprzężeń.

```
//lets say we are querying an hour worth of logs
let startTime = ago(1h);
let endTime = now();
//below gets the latest Image & ImageTag for every containerID, during the time window
let ContainerInv = ContainerInventory | where TimeGenerated >= startTime and TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID, Image, ImageTag | project-away TimeGenerated | project ContainerID1=ContainerID, Image1=Image ,ImageTag1=ImageTag;
//below gets the latest Name for every containerID, during the time window
let KubePodInv  = KubePodInventory | where ContainerID != "" | where TimeGenerated >= startTime | where TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID2 = ContainerID, Name1=ContainerName | project ContainerID2 , Name1;
//now join the above 2 to get a 'jointed table' that has name, image & imagetag. Outer left is safer in-case there are no kubepod records are if they are latent
let ContainerData = ContainerInv | join kind=leftouter (KubePodInv) on $left.ContainerID1 == $right.ContainerID2;
//now join ContainerLog table with the 'jointed table' above and project-away redundant fields/columns and rename columns that were re-written
//Outer left is safer so you dont lose logs even if we cannot find container metadata for loglines (due to latency, time skew between data types etc...)
ContainerLog
| where TimeGenerated >= startTime and TimeGenerated < endTime 
| join kind= leftouter (
   ContainerData
) on $left.ContainerID == $right.ContainerID2 | project-away ContainerID1, ContainerID2, Name, Image, ImageTag | project-rename Name = Name1, Image=Image1, ImageTag=ImageTag1 

```

### <a name="option-2"></a>Opcja 2

Ponownie włącz kolekcję dla tych właściwości dla każdego wiersza dziennika kontenera.

Jeśli pierwsza opcja jest niewygodna ze względu na zmiany zapytania, można ponownie włączyć zbieranie tych pól, włączając ustawienie ```log_collection_settings.enrich_container_logs``` na mapie konfiguracji agenta zgodnie z opisem w [ustawieniach konfiguracji zbierania danych](./container-insights-agent-config.md).

> [!NOTE]
> Druga opcja nie jest zalecana w przypadku dużych klastrów, które mają więcej niż 50 węzłów, ponieważ generuje ona wywołania serwera interfejsu API z każdego węzła > w klastrze w celu przeprowadzenia tego wzbogacania. Ta opcja umożliwia również zwiększenie rozmiaru danych dla każdej zebranej linii dziennika.

## <a name="can-i-view-metrics-collected-in-grafana"></a>Czy można wyświetlić metryki zebrane w Grafana?

Azure Monitor dla kontenerów obsługuje wyświetlanie metryk przechowywanych w obszarze roboczym Log Analytics na pulpitach nawigacyjnych Grafana. Podano szablon, który można pobrać z [repozytorium pulpitu nawigacyjnego](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) Grafana, aby rozpocząć pracę i zapoznać się z pomocą techniczną, aby dowiedzieć się, jak wykonywać zapytania dotyczące dodatkowych danych z monitorowanych klastrów w celu wizualizowania niestandardowych pulpitów nawigacyjnych Grafana. 

## <a name="can-i-monitor-my-aks-engine-cluster-with-azure-monitor-for-containers"></a>Czy mogę monitorować klaster AKS-Engine z Azure Monitor dla kontenerów?

Azure Monitor dla kontenerów obsługuje monitorowanie obciążeń kontenerów wdrożonych na platformie AKS (wcześniej znanych jako klastry ACS) hostowanych w systemie Azure. Aby uzyskać szczegółowe informacje i zapoznać się z omówieniem kroków wymaganych do włączenia monitorowania dla tego scenariusza, zobacz [używanie Azure monitor dla kontenerów dla aparatu AKS](https://github.com/microsoft/OMS-docker/tree/aks-engine).

## <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>Dlaczego nie widzę danych w obszarze roboczym Log Analytics?

Jeśli nie widzisz żadnych danych w obszarze roboczym Log Analytics w określonym czasie codziennie, być może osiągnięto domyślny limit 500 MB lub dzienne zakończenie określony w celu kontrolowania ilości danych do zebrania dziennie. Gdy limit zostanie osiągnięty przez dzień, zbieranie danych zostanie zatrzymane i wznowione tylko w następnym dniu. Aby sprawdzić użycie danych i zaktualizować do innej warstwy cenowej na podstawie przewidywanych wzorców użycia, zobacz temat [użycie i koszt danych dziennika](../platform/manage-cost-storage.md). 

## <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>Jakie Stany kontenerów określono w tabeli ContainerInventory?

Tabela ContainerInventory zawiera informacje o zatrzymanych i uruchomionych kontenerach. Tabela jest wypełniana przez przepływ pracy wewnątrz agenta, który wysyła zapytanie do platformy Docker dla wszystkich kontenerów (uruchomionych i zatrzymanych) oraz przekazuje te dane do obszaru roboczego Log Analytics.
 
## <a name="how-do-i-resolve-missing-subscription-registration-error"></a>Jak mogę rozwiązać *brakującego błędu rejestracji subskrypcji* ?

Jeśli wystąpi błąd **braku rejestracji subskrypcji dla Microsoft. OperationsManagement**, możesz rozwiązać ten problem, rejestrując dostawcę zasobów **Microsoft. OperationsManagement** w subskrypcji, w której zdefiniowano obszar roboczy. Dokumentację dotyczącą sposobu wykonywania tej czynności można znaleźć [tutaj](../../azure-resource-manager/resource-manager-register-provider-errors.md).

## <a name="is-there-support-for-rbac-enabled-aks-clusters"></a>Czy obsługiwane są klastry AKS z włączoną funkcją RBAC?

Rozwiązanie do monitorowania kontenerów nie obsługuje RBAC, ale jest obsługiwane w przypadku kontenerów Azure Monitor. Na stronie Szczegóły rozwiązania mogą nie być wyświetlane odpowiednie informacje w blokach, które pokazują dane dla tych klastrów.

## <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>Jak mogę włączyć zbieranie dzienników dla kontenerów w przestrzeni nazw polecenia-system za pomocą Helm?

Kolekcja dzienników z kontenerów w przestrzeni nazw polecenia-system jest domyślnie wyłączona. Zbieranie dzienników można włączyć przez ustawienie zmiennej środowiskowej w omsagent. Aby uzyskać więcej informacji, zobacz stronę [Azure monitor for Containers w](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers) serwisie GitHub. 

## <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>Jak mogę zaktualizować omsagent do najnowszej wydanej wersji?

Aby dowiedzieć się, jak uaktualnić agenta, zobacz [Zarządzanie agentem](container-insights-manage-agent.md).

## <a name="how-do-i-enable-multi-line-logging"></a>Jak mogę włączyć rejestrowanie wielowierszowe?

Obecnie Azure Monitor dla kontenerów nie obsługuje rejestrowania wielowierszowego, ale są dostępne obejścia. Można skonfigurować wszystkie usługi do zapisu w formacie JSON, a następnie Docker/Moby zapisze je jako jeden wiersz.

Na przykład możesz otoczyć dziennik jako obiekt JSON, jak pokazano w poniższym przykładzie dla przykładowej aplikacji node. js:

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

Te dane będą wyglądać podobnie do poniższego przykładu w Azure Monitor dla dzienników podczas wykonywania zapytania dotyczącego:

```
LogEntry : ({“Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

Aby zapoznać się z szczegółowym opisem problemu, zapoznaj się z następującym [linkiem](https://github.com/moby/moby/issues/22920)w witrynie GitHub.

## <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>Jak mogę rozwiązać problemy z usługą Azure AD po włączeniu dzienników na żywo? 

Może zostać wyświetlony następujący błąd: **adres URL odpowiedzi określony w żądaniu nie pasuje do adresów URL odpowiedzi skonfigurowanych dla aplikacji: "< identyfikator aplikacji\>"** . Rozwiązanie, które ma zostać rozwiązane, można znaleźć w artykule [jak wyświetlać dane kontenerów w czasie rzeczywistym za pomocą Azure monitor dla kontenerów](container-insights-livedata-setup.md#configure-ad-integrated-authentication). 

## <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>Dlaczego nie mogę uaktualnić klastra po dołączeniu?

Jeśli po włączeniu Azure Monitor dla kontenerów dla klastra AKS zostanie usunięty obszar roboczy Log Analytics, do którego klaster wysłał swoje dane, podczas próby uaktualnienia klastra zakończy się niepowodzeniem. Aby obejść ten krok, należy wyłączyć monitorowanie, a następnie włączyć go ponownie, odwołując się do innego prawidłowego obszaru roboczego w ramach subskrypcji. Podczas próby ponownego wykonania uaktualnienia klastra należy przetworzyć i zakończyć pracę.  

## <a name="which-ports-and-domains-do-i-need-to-openwhitelist-for-the-agent"></a>Które porty i domeny muszę otworzyć/dozwolonych dla agenta?

Zapoznaj się z [wymaganiami dotyczącymi zapory sieciowej](container-insights-onboard.md#network-firewall-requirements) dotyczącymi informacji o konfiguracji serwera proxy i zapory wymaganych przez agenta kontenerów z platformą Azure oraz chmurą platformy Azure dla instytucji rządowych i platformy Azure.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć monitorowanie klastra usługi AKS, zapoznaj się z [sposób dołączania Azure Monitor wykrywający sytuacje, kontenery](container-insights-onboard.md) , aby zrozumieć wymagania i dostępnych metod, aby włączyć monitorowanie. 
