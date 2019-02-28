---
title: Usługa Azure Monitor dla kontenerów — często zadawane pytania | Dokumentacja firmy Microsoft
description: Usługa Azure Monitor dla kontenerów to rozwiązanie, które monitoruje kondycję klastrów usługi AKS i wystąpień kontenera na platformie Azure. Ten artykuł zawiera odpowiedzi na często zadawane pytania.
services: azure-monitor
author: mgoedtel
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: magoedte
ms.openlocfilehash: 250bf6ede3a54b26b6e9c68850f8e7260020002e
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2019
ms.locfileid: "56960516"
---
# <a name="azure-monitor-for-containers-frequently-asked-questions"></a>Usługa Azure Monitor dla kontenerów — często zadawane pytania
Ta FAQ firmy Microsoft znajduje się lista często zadawane pytania dotyczące usługi Azure Monitor dla kontenerów. Jeśli masz dodatkowe pytania dotyczące rozwiązania, przejdź do strony [forum dyskusyjne](https://feedback.azure.com/forums/34192--general-feedback) i Publikuj swoje pytania. Gdy zadawane pytanie dodajemy go do tego artykułu tak, aby możliwe było szybkie i łatwe.

## <a name="i-am-unable-to-see-any-data-in-the-log-analytics-workspace-at-a-certain-time-everyday-how-do-i-resolve-this"></a>Nie mogę widoczne żadne dane, w obszarze roboczym usługi Log Analytics w określonym czasie codziennie. Jak rozwiązać ten problem? 

Osiągnięto limit wynoszący 500 MB domyślne lub dziennego limitu pozwala określić ilość dane mają być zbierane codziennie. Po spełnieniu limit na ten dzień gromadzenie danych zatrzymuje i wznawia działanie tylko na następny dzień. Aby przejrzeć wykorzystanie danych i zaktualizować do innej warstwy cenowej na podstawie wzorców Twojej oczekiwanego użycia, zobacz [dziennika danych użycia i kosztów](../platform/manage-cost-storage.md). 

## <a name="what-are-the-states-of-containers-specified-in-the-containerinventory-table"></a>Jakie są Państwa kontenerów określonych w tabeli ContainerInventory?
Tabela ContainerInventory zawiera informacje o kontenerach zatrzymana i uruchomiona. Tabela jest wypełniana przez przepływ pracy w ramach agenta, który bada platformy docker dla kontenerów (uruchomione i zatrzymane) i przekazuje te dane z obszaru roboczego usługi Log Analytics.
 
## <a name="how-do-i-resolve-errors-related-to-missing-subscription-registration-for-microsoftoperationsmanagement"></a>Jak rozwiązać błędy związane z **rejestracji subskrypcji Brak Microsoft.OperationsManagement**?
Aby naprawić błąd, zarejestruj dostawcę zasobów **Microsoft.OperationsManagement** w ramach subskrypcji, w którym definiowany jest obszar roboczy. Można znaleźć w dokumentacji dotyczącej sposobu wykonania tego [tutaj](../../azure-resource-manager/resource-manager-register-provider-errors.md).

## <a name="does-azure-monitor-for-containers-include-support-for-rbac-enabled-aks-clusters"></a>Usługi Azure Monitor dla kontenerów zawiera obsługę RBAC włączona klastrów AKS?
To rozwiązanie monitorowanie kontenera nie obsługuje funkcji RBAC, ale jest obsługiwany z usługą Azure Monitor dla kontenerów. Na stronie szczegółów rozwiązania nie mogą być wyświetlane właściwe informacje w blokach, które wyświetlają dane dotyczące tych klastrów.

## <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>Jak włączyć zbieranie dzienników dla kontenerów w przestrzeni nazw systemu kubernetes za pomocą narzędzia Helm?
Zbieranie dzienników z kontenerów w przestrzeni nazw kubernetes — system jest domyślnie wyłączona. Zbieranie danych dziennika można włączyć, ustawiając zmienną środowiskową na omsagent. Aby uzyskać więcej informacji, zobacz [usługi Azure Monitor dla kontenerów](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers) w witrynie github. 

## <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>Jak zaktualizować omsagent najnowszej wersji?
Aby dowiedzieć się, jak uaktualnić agenta, zobacz [zarządzanie agentami](container-insights-manage-agent.md).

## <a name="how-do-i-enable-multi-line-logging"></a>Jak włączyć rejestrowanie wielowierszowe?
Obecnie usługa Azure Monitor dla kontenerów nie obsługuje wielowierszowe rejestrowania, ale istnieją obejścia. Można skonfigurować wszystkie usługi, aby zapisać w formacie JSON, a następnie platformy Docker/Moby zapisze je jako jeden wiersz.

Na przykład można opakować dziennika jako obiekt JSON, jak pokazano w poniższym przykładzie dla przykładowej aplikacji node.js:

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

Tych danych będzie wyglądać jak w poniższym przykładzie w usłudze Azure Monitor dla dzienników po wykonaniu zapytania dotyczącego go:

```
LogEntry : ({“Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

Aby uzyskać szczegółowy widok ten problem, przejrzyj następujący kod [łącze github](https://github.com/moby/moby/issues/22920).

## <a name="how-do-i-resolve-azure-active-directory-errors-when-i-enable-live-logs"></a>Jak rozwiązać błędy usługi Azure Active Directory po włączeniu dzienniki na żywo? 
Może zostać wyświetlony następujący błąd: **Odpowiedź adres url określony w żądaniu nie pasuje do adresów URL odpowiedzi skonfigurowane dla aplikacji: "< identyfikator aplikacji\>"**. Rozwiązanie, aby rozwiązać problem, można znaleźć w artykule [sposób wyświetlania kontenera czasie rzeczywistym dzienniki z usługą Azure Monitor dla kontenerów](container-insights-live-logs.md#configure-aks-with-azure-active-directory). 

## <a name="next-steps"></a>Kolejne kroki
Aby rozpocząć monitorowanie klastra usługi AKS, zapoznaj się z [sposób dołączania Azure Monitor wykrywający sytuacje, kontenery](container-insights-onboard.md) , aby zrozumieć wymagania i dostępnych metod, aby włączyć monitorowanie. 