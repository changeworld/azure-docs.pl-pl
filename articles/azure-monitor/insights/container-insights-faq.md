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
ms.date: 04/17/2019
ms.author: magoedte
ms.openlocfilehash: 70712b68cc26a461f702850dbf6064b65b4ff130
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "59999726"
---
# <a name="azure-monitor-for-containers-frequently-asked-questions"></a>Usługa Azure Monitor dla kontenerów — często zadawane pytania

Ta FAQ firmy Microsoft znajduje się lista często zadawane pytania dotyczące usługi Azure Monitor dla kontenerów. Jeśli masz dodatkowe pytania dotyczące rozwiązania, przejdź do strony [forum dyskusyjne](https://feedback.azure.com/forums/34192--general-feedback) i Publikuj swoje pytania. Gdy zadawane pytanie dodajemy go do tego artykułu tak, aby możliwe było szybkie i łatwe.

## <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>Dlaczego nie widzę danych w obszarze roboczym usługi Log Analytics?

Jeśli nie widać żadnych danych w obszarze roboczym usługi Log Analytics na codziennie czasu, możliwe, że osiągnięto domyślny limit 500 MB lub dziennego limitu pozwala określić ilość dane mają być zbierane codziennie. Po spełnieniu limit na ten dzień gromadzenie danych zatrzymuje i wznawia działanie tylko na następny dzień. Aby przejrzeć wykorzystanie danych i zaktualizować do innej warstwy cenowej na podstawie wzorców Twojej oczekiwanego użycia, zobacz [dziennika danych użycia i kosztów](../platform/manage-cost-storage.md). 

## <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>Jakie są Państwa kontenera określonych w tabeli ContainerInventory?

Tabela ContainerInventory zawiera informacje o kontenerach zatrzymana i uruchomiona. Tabela jest wypełniana przez przepływ pracy w ramach agenta, który bada platformy docker dla kontenerów (uruchomione i zatrzymane) i przekazuje te dane z obszaru roboczego usługi Log Analytics.
 
## <a name="how-do-i-resolve-missing-subscription-registration-error"></a>Jak rozwiązać **rejestracji subskrypcji Brak** błąd?

Jeśli zostanie wyświetlony błąd **rejestracji subskrypcji Brak Microsoft.OperationsManagement**, można je było rozwiązać przez rejestrowanie dostawcy zasobów **Microsoft.OperationsManagement** w Subskrypcja, w którym definiowany jest obszar roboczy. Można znaleźć w dokumentacji dotyczącej sposobu wykonania tego [tutaj](../../azure-resource-manager/resource-manager-register-provider-errors.md).

## <a name="is-there-support-for-rbac-enabled-aks-clusters"></a>Jest obsługa RBAC włączona klastrów AKS?

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

## <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>Jak rozwiązać błędy usługi Azure AD, po włączeniu dzienniki na żywo? 

Może zostać wyświetlony następujący błąd: **Odpowiedź adres url określony w żądaniu nie pasuje do adresów URL odpowiedzi skonfigurowane dla aplikacji: "< identyfikator aplikacji\>"**. Rozwiązanie, aby rozwiązać problem, można znaleźć w artykule [sposób wyświetlania kontenera czasie rzeczywistym dzienniki z usługą Azure Monitor dla kontenerów](container-insights-live-logs.md#configure-aks-with-azure-active-directory). 

## <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>Dlaczego nie można uaktualnić klastra po dołączeniu?

Po włączeniu usługi Azure Monitor dla kontenerów do klastra usługi AKS w przypadku usunięcia obszaru roboczego usługi Log Analytics klastra wysyłała dane, podczas próby uaktualnienia klastra zakończy się niepowodzeniem. Aby obejść ten problem, należy wyłączyć monitorowanie a następnie włączyć ją ponownie odwołujące się do innego obszaru roboczego prawidłowy w ramach subskrypcji. Podczas próby ponownie uaktualnienie klastra powinien przetworzyć i ukończone pomyślnie.  

## <a name="next-steps"></a>Kolejne kroki

Aby rozpocząć monitorowanie klastra usługi AKS, zapoznaj się z [sposób dołączania Azure Monitor wykrywający sytuacje, kontenery](container-insights-onboard.md) , aby zrozumieć wymagania i dostępnych metod, aby włączyć monitorowanie. 