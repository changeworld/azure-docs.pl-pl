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
ms.date: 02/18/2019
ms.author: magoedte
ms.openlocfilehash: 27a191bb62ae59aa154167a22c99d3e699f3eb5a
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/19/2019
ms.locfileid: "56418624"
---
# <a name="azure-monitor-for-containers-frequently-asked-questions"></a>Usługa Azure Monitor dla kontenerów — często zadawane pytania
Ta FAQ firmy Microsoft znajduje się lista często zadawane pytania dotyczące usługi Azure Monitor dla kontenerów. Jeśli masz dodatkowe pytania dotyczące rozwiązania, przejdź do strony [forum dyskusyjne](https://feedback.azure.com/forums/34192--general-feedback) i Publikuj swoje pytania. Gdy zadawane pytanie dodajemy go do tego artykułu tak, aby możliwe było szybkie i łatwe.

## <a name="why-am-i-not-seeing-data-if-log-analytics-workspace-is-configured-with-the-free-pricing-tier"></a>Dlaczego nie widzę danych jeśli obszar roboczy usługi Log Analytics jest skonfigurowany przy użyciu warstwy cenowej bezpłatna? 

Może osiągnięto limit wynoszący 500 MB domyślną lub określić dzienny limit, aby kontrolować poziom dane mają być zbierane codziennie. Aby sprawdzić użycie i zarządzaj nim danych, zobacz [dziennika danych użycia i kosztów](../platform/manage-cost-storage.md). 

## <a name="what-are-the-states-of-containers-specified-in-the-containerinventory-table"></a>Jakie są Państwa kontenerów określonych w tabeli ContainerInventory?
Tabela ContainerInventory zawiera informacje o kontenerach zatrzymana i uruchomiona. Tabela jest wypełniana przez przepływ pracy w ramach agenta, który bada platformy docker dla kontenerów (uruchomione i zatrzymane) i przekazuje te dane z obszaru roboczego usługi Log Analytics.
 
## <a name="how-do-i-resolve-errors-related-to-missing-subscription-registration-for-microsoftoperationsmanagement"></a>Jak rozwiązać błędy związane z **rejestracji subskrypcji Brak Microsoft.OperationsManagement**?
Aby naprawić błąd, zarejestruj dostawcę zasobów **Microsoft.OperationsManagement** w ramach subskrypcji, w którym definiowany jest obszar roboczy. Można znaleźć w dokumentacji dotyczącej sposobu wykonania tego [tutaj](../../azure-resource-manager/resource-manager-register-provider-errors.md).

## <a name="does-azure-monitor-for-containers-include-support-for-rbac-enabled-aks-clusters"></a>Usługi Azure Monitor dla kontenerów zawiera obsługę RBAC włączona klastrów AKS?
RBAC włączone AKS klastrów nie są obecnie obsługiwane przez to rozwiązanie. Na stronie szczegółów rozwiązania nie mogą być wyświetlane właściwe informacje w blokach, które wyświetlają dane dotyczące tych klastrów.

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

Tych danych będzie wyglądać podobnie do poniższego w usłudze Azure Monitor dla dzienników po wykonaniu zapytania dotyczącego go:

```
LogEntry : ({“Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

Aby uzyskać szczegółowy widok ten problem, przejrzyj następujący kod [łącze github](https://github.com/moby/moby/issues/22920).

## <a name="how-do-i-resolve-azure-active-directory-errors-when-i-enable-live-logs"></a>Jak rozwiązać błędy usługi Azure Active Directory po włączeniu dzienniki na żywo? 
Może zostać wyświetlony następujący błąd: **Odpowiedź adres url określony w żądaniu jest niezgodna z adresów URL odpowiedzi skonfigurowane dla aplikacji: '60b4dec7-5a69-4165-a211-12c40b5c0435'**. Rozwiązanie tego problemu można znaleźć w artykule [sposób wyświetlania kontenera czasie rzeczywistym dzienniki z usługą Azure Monitor dla kontenerów](container-insights-live-logs.md#configure-aks-with-azure-active-directory). 

## <a name="next-steps"></a>Kolejne kroki
Aby rozpocząć monitorowanie klastra usługi AKS, zapoznaj się z [sposób dołączania Azure Monitor wykrywający sytuacje, kontenery](container-insights-onboard.md) , aby zrozumieć wymagania i dostępnych metod, aby włączyć monitorowanie. 