---
title: Azure Monitor for Containers — często zadawane pytania | Microsoft Docs
description: Azure Monitor for Containers to rozwiązanie monitorujące kondycję klastrów AKS i Container Instances na platformie Azure. Ten artykuł zawiera odpowiedzi na często zadawane pytania.
services: azure-monitor
author: mgoedtel
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 08/14/2019
ms.author: magoedte
ms.openlocfilehash: f8d763f8bb228a0d4d83a3776f818d59939b942d
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2019
ms.locfileid: "69559084"
---
# <a name="azure-monitor-for-containers-frequently-asked-questions"></a>Azure Monitor for Containers — często zadawane pytania

Ta firma Microsoft — często zadawane pytania dotyczące Azure Monitor kontenerów. Jeśli masz dodatkowe pytania dotyczące rozwiązania, przejdź do [forum dyskusyjnego](https://feedback.azure.com/forums/34192--general-feedback) i Opublikuj swoje pytania. Gdy zadawane pytanie dodajemy go do tego artykułu tak, aby możliwe było szybkie i łatwe.

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

Aby zapoznać się z szczegółowym opisem problemu, zapoznaj się z następującym linkiem w witrynie [GitHub](https://github.com/moby/moby/issues/22920).

## <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>Jak mogę rozwiązać problemy z usługą Azure AD po włączeniu dzienników na żywo? 

Może zostać wyświetlony następujący błąd: **Adres URL odpowiedzi określony w żądaniu jest niezgodny z adresami URL odpowiedzi skonfigurowanymi dla aplikacji: "< identyfikator\>aplikacji"** . Rozwiązanie, które pozwala rozwiązać ten problem, można znaleźć w artykule [jak wyświetlić dzienniki kontenerów w czasie rzeczywistym za pomocą Azure monitor dla kontenerów](container-insights-live-logs.md#configure-aks-with-azure-active-directory). 

## <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>Dlaczego nie mogę uaktualnić klastra po dołączeniu?

Jeśli po włączeniu Azure Monitor dla kontenerów dla klastra AKS zostanie usunięty obszar roboczy Log Analytics, do którego klaster wysłał swoje dane, podczas próby uaktualnienia klastra zakończy się niepowodzeniem. Aby obejść ten krok, należy wyłączyć monitorowanie, a następnie włączyć go ponownie, odwołując się do innego prawidłowego obszaru roboczego w ramach subskrypcji. Podczas próby ponownego wykonania uaktualnienia klastra należy przetworzyć i zakończyć pracę.  

## <a name="which-ports-and-domains-do-i-need-to-openwhitelist-for-the-agent"></a>Które porty i domeny muszę otworzyć/dozwolonych dla agenta?
- *.ods.opinsights.azure.com   443
- *.oms.opinsights.azure.com   443
- *.blob.core.windows.net      443
- dc.services.visualstudio.com 443
- *. microsoftonline.com 443
- *. monitoring.azure.com 443
- login.microsoftonline.com 443

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć monitorowanie klastra usługi AKS, zapoznaj się z [sposób dołączania Azure Monitor wykrywający sytuacje, kontenery](container-insights-onboard.md) , aby zrozumieć wymagania i dostępnych metod, aby włączyć monitorowanie. 
