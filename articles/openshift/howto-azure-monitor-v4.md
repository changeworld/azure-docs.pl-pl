---
title: Integracja z usługą Azure Monitor dla usługi Azure Red Hat OpenShift 4.3
description: Dowiedz się, jak włączyć usługę Azure Monitor w klastrze OpenShift Red Hat platformy Microsoft Azure.
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
ms.openlocfilehash: a784fc070400995c56d16a3bc264d589bcb1f64e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082850"
---
# <a name="azure-monitor-integration-for-azure-red-hat-openshift-43"></a>Integracja z usługą Azure Monitor dla usługi Azure Red Hat OpenShift 4.3

> [!IMPORTANT] 
> Należy pamiętać, że usługa Azure Red Hat OpenShift 4.3 jest obecnie dostępna tylko w prywatnej wersji zapoznawczej we wschodnich stanach USA. Akceptacja prywatnej wersji zapoznawczej odbywa się wyłącznie na zaproszenie. Przed podjęciem próby włączenia tej funkcji należy zarejestrować subskrypcję: [rejestracja prywatna w wersji zapoznawczej Azure Red Hat OpenShift](https://aka.ms/aro-preview-register)

> [!NOTE]
> Funkcje w wersji zapoznawczej są samoobsługowe i są dostępne w stanie dostępności i są wyłączone z umowy dotyczącej poziomu usług (SLA) i ograniczonej gwarancji. W związku z tym funkcje nie są przeznaczone do użytku produkcyjnego.

W tym artykule opisano, jak włączyć prywatną wersję zapoznawczą usługi Azure Monitor dla kontenerów dla klastrów OpenShift 4.3 hostowanych na prem lub w dowolnym środowisku chmury. Te same instrukcje mają również zastosowanie, aby włączyć monitorowanie klastrów Azure Red Hat OpenShift (ARO) 4.3.  

## <a name="prerequisites"></a>Wymagania wstępne

- [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Ster 3](https://helm.sh/docs/intro/install/)
- Dostęp do kubeconfig klastra kubernetes
- Dostęp do subskrypcji platformy Azure
- Dostęp do klastra OpenShift 4.3 w celu zainstalowania wykresu Steru usługi Azure Monitor dla kontenerów
- Minimalne uprawnienie roli RBAC współautora w ramach subskrypcji platformy Azure  
- Agent monitorowania wymaga następujących portów wychodzących — i domen do wysyłania danych monitorowania do wewnętrznej bazy danych usługi Azure Monitor (jeśli jest zablokowany przez serwer proxy/zapory):
  - *.ods.opinsights.azure.com 443
  - *.oms.opinsights.azure.com 443
  - *.blob.core.windows.net 443
  - dc.services.visualstudio.com 443

## <a name="onboarding"></a>Dołączanie

> [!TIP]
> Skrypt używa funkcji bash 4, więc upewnij się, że bash jest aktualny. Aktualną wersję możesz `bash --version`sprawdzić za pomocą programu .

### <a name="download-the-onboarding-script"></a>Pobieranie skryptu dołączania

```bash
curl -LO  https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/openshiftV4/onboarding_azuremonitor_for_containers.sh
```

Wykonaj następujący skrypt za pomocą azureSubscriptionId, obszar roboczy Region, clusterName i kontekst klastra Kubernetes.

```bash
bash onboarding_azuremonitor_for_containers.sh <azureSubscriptionId> <azureRegionforLogAnalyticsWorkspace> <clusterName> <kubeconfigContextNameOftheCluster>
```

Przykład:

```bash
 bash onboarding_azuremonitor_for_containers.sh 27ac26cf-a9f0-4908-b300-9a4e9a0fb205 eastus myocp42 admin 
```

## <a name="configure-agent-data-collection"></a>Konfigurowanie zbierania danych agenta

Domyślnie agent monitorowania zbiera {stdout; stderr} dzienniki kontenerów wszystkich kontenerów uruchomionych we wszystkich obszarach nazw z wyjątkiem kube-system.  Jeśli chcesz skonfigurować kolekcję dziennika kontenera specyficzną dla określonego obszaru nazw lub obszarów nazw, możesz odwołać się do [konfiguracji agenta usługi Container Insights](../azure-monitor/insights/container-insights-agent-config.md). W tym miejscu można skonfigurować agenta monitorowania z żądanymi ustawieniami zbierania danych za pomocą mapy konfiguracji.

## <a name="configure-scraping-of-prometheus-metrics"></a>Konfigurowanie skrobania metryk Prometeusza

Usługa Azure Monitor dla kontenerów zaciera metryki Prometheus i pozyskiwania do zaplecza usługi Azure Monitor. Zapoznaj się z [konfiguracją usługi Container Insights Prometheus,](../azure-monitor/insights/container-insights-prometheus-integration.md) aby uzyskać instrukcje dotyczące konfigurowania skrobania Prometheus.

Po pomyślnym dołączaniu przejdź do [monitorowania hybrydowego](https://aka.ms/azmon-containers-hybrid) i wybierz opcję Środowisko jako **"Wszystkie",** aby wyświetlić nowo wbudowany klaster OpenShift w wersji 4.

## <a name="disable-monitoring"></a>Wyłączanie monitorowania

Jeśli chcesz wyłączyć monitorowanie, możesz usunąć wykres Azure Monitor dla kontenerów helm przy użyciu następującego polecenia, aby zatrzymać zbieranie i pozyskiwanie danych monitorowania do usługi Azure Monitor dla kontenerów zaplecza.

``` bash
helm del azmon-containers-release-1
```

## <a name="update-monitoring"></a>Monitorowanie aktualizacji

Uruchom ponownie skrypt dołączania zgodnie z opisem w sekcji [Dołączanie](#onboarding) z tym samym parametrem, aby zaktualizować go do najnowszego wykresu Helm.

## <a name="after-successful-onboarding"></a>Po pomyślnym dołączaniu

Przejdź do [monitorowania hybrydowego,](https://aka.ms/azmon-containers-hybrid)a na karcie **Monitorowane klastry** możesz zobaczyć nowo włączona klaster OpenShift/ARO v4 ze stanem kondycji. W tym miejscu można uzyskać szczegółowe informacje, takie jak metryki, zapasy i dzienniki, klikając kolumnę **Klaster.**

## <a name="supported-features"></a>Obsługiwane funkcje

Aby uzyskać więcej informacji na temat obsługiwanych funkcji i funkcji, zobacz [Omówienie usługi Container Insights](../azure-monitor/insights/container-insights-overview.md).

Skontaktuj się askcoin@microsoft.com z nami za pośrednictwem informacji zwrotnych i pytań.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o monitorowaniu, zobacz:
- [Omówienie usługi Container Insights](../azure-monitor/insights/container-insights-overview.md)

- [Omówienie kwerendy dziennika](../azure-monitor/log-query/log-query-overview.md)
