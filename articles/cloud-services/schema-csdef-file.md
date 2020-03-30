---
title: Schemat definicji usług w chmurze platformy Azure (plik csdef) | Dokumenty firmy Microsoft
description: Plik definicji usługi (csdef) definiuje model usługi dla aplikacji, zawierający dostępne role, punkty końcowe i wartości konfiguracji dla usługi.
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 42
author: tgore03
ms.author: tagore
ms.openlocfilehash: dadb50bd0663f47e6a1bf3d58b5187c8b466964d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528374"
---
# <a name="azure-cloud-services-definition-schema-csdef-file"></a>Schemat definicji usług w chmurze platformy Azure (plik csdef)
Plik definicji usługi definiuje model usługi dla aplikacji. Plik zawiera definicje ról, które są dostępne dla usługi w chmurze, określa punkty końcowe usługi i ustanawia ustawienia konfiguracji usługi. Wartości ustawień konfiguracji są ustawiane w pliku konfiguracji usługi, zgodnie z opisem [schematu konfiguracji usługi w chmurze (klasycznej).](/previous-versions/azure/reference/ee758710(v=azure.100))

Domyślnie plik schematu konfiguracji diagnostyki platformy `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` Azure jest instalowany w katalogu. Zamień `<version>` na zainstalowaną wersję [zestawu SDK platformy Azure](https://www.windowsazure.com/develop/downloads/).

Domyślnym rozszerzeniem pliku definicji usługi jest .csdef.

## <a name="basic-service-definition-schema"></a>Schemat definicji usługi podstawowej
Plik definicji usługi `ServiceDefinition` musi zawierać jeden element. Definicja usługi musi zawierać co`WebRole` `WorkerRole`najmniej jeden element roli ( lub ). Może zawierać maksymalnie 25 ról zdefiniowanych w pojedynczej definicji i można mieszać typy ról. Definicja usługi zawiera `NetworkTrafficRules` również element opcjonalny, który ogranicza, które role mogą komunikować się z określonymi wewnętrznymi punktami końcowymi. Definicja usługi zawiera `LoadBalancerProbes` również element opcjonalny, który zawiera sondy kondycji zdefiniowane przez klienta punktów końcowych.

Podstawowy format pliku definicji usługi jest następujący.

```xml
<ServiceDefinition name="<service-name>" topologyChangeDiscovery="<change-type>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" upgradeDomainCount="<number-of-upgrade-domains>" schemaVersion="<version>">
  
  <LoadBalancerProbes>
         …
  </LoadBalancerProbes>
  
  <WebRole …>
         …
  </WebRole>
  
  <WorkerRole …>
         …
  </WorkerRole>
  
  <NetworkTrafficRules>
         …
  </NetworkTrafficRules>

</ServiceDefinition>
```

## <a name="schema-definitions"></a>Definicje schematów
W poniższych tematach opisano schemat:

- [LoadBalancerProbe — schemat](schema-csdef-loadbalancerprobe.md)
- [WebRole — schemat](schema-csdef-webrole.md)
- [WorkerRole — schemat](schema-csdef-workerrole.md)
- [NetworkTrafficRules — schemat](schema-csdef-networktrafficrules.md)

##  <a name="servicedefinition-element"></a><a name="ServiceDefinition"></a>Element zdefiniowanie usługi
Element `ServiceDefinition` jest elementem najwyższego poziomu pliku definicji usługi.

W poniższej tabeli `ServiceDefinition` opisano atrybuty elementu.

| Atrybut               | Opis |
| ----------------------- | ----------- |
| name                    |Wymagany. Nazwa usługi. Nazwa musi być unikatowa na koncie usługi.|
| topologiaZdjęcie zodkrywą | Element opcjonalny. Określa typ powiadomienia o zmianie topologii. Możliwe wartości:<br /><br /> -   `Blast`- Wysyła aktualizację tak szybko, jak to możliwe do wszystkich wystąpień roli. Jeśli wybierzesz opcję, rola powinna być w stanie obsłużyć aktualizację topologii bez ponownego uruchamiania.<br />-   `UpgradeDomainWalk`– Wysyła aktualizację do każdego wystąpienia roli w sposób sekwencyjny po poprzednim wystąpieniu pomyślnie zaakceptował aktualizację.|
| wersja schematu           | Element opcjonalny. Określa wersję schematu definicji usługi. Wersja schematu umożliwia visual studio, aby wybrać odpowiednie narzędzia zestawu SDK do użycia do sprawdzania poprawności schematu, jeśli więcej niż jedna wersja zestawu SDK jest zainstalowany obok siebie.|
| upgradeDomainCount      | Element opcjonalny. Określa liczbę domen uaktualnienia, w których przypisane są role w tej usłudze. Wystąpienia roli są przydzielane do domeny uaktualnienia po wdrożeniu usługi. Aby uzyskać więcej informacji, zobacz [Aktualizowanie roli lub wdrożenia usługi w chmurze](cloud-services-how-to-manage-portal.md#update-a-cloud-service-role-or-deployment), [Zarządzanie dostępnością maszyn wirtualnych](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) i [Co to jest model usługi w chmurze](https://docs.microsoft.com/azure/cloud-services/cloud-services-model-and-package).<br /><br /> Można określić maksymalnie 20 domen uaktualnienia. Jeśli nie zostanie określony, domyślna liczba domen uaktualnienia wynosi 5.|
