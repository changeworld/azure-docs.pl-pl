---
title: Azure Cloud Services, definicji schematu (plik csdef) | Dokumentacja firmy Microsoft
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: b7735dbf-8e91-4d1b-89f7-2f17e9302469
caps.latest.revision: 42
author: jpconnock
ms.author: jeconnoc
manager: timlt
ms.openlocfilehash: 4e018af7df64c9ed8050a3c618cf2645d5509cdd
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58918505"
---
# <a name="azure-cloud-services-definition-schema-csdef-file"></a>Schemat definicji usług Azure Cloud Services (plik csdef)
Plik definicji usługi definiuje model usług dla aplikacji. Plik zawiera definicje ról, które są dostępne dla usługi w chmurze, określa punkty końcowe usługi i ustanawia ustawienia konfiguracji dla usługi. Wartości ustawienia konfiguracji są ustawiane w pliku konfiguracji usługi, zgodnie z opisem w [schematu konfiguracji usługi w chmurze (klasyczne)](/previous-versions/azure/reference/ee758710(v=azure.100)).

Domyślnie plik schematu konfiguracji diagnostyki platformy Azure jest zainstalowany na `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` katalogu. Zastąp `<version>` z zainstalowaną wersją programu [zestawu Azure SDK](https://www.windowsazure.com/develop/downloads/).

Domyślnym rozszerzeniem dla pliku definicji usługi jest csdef.

## <a name="basic-service-definition-schema"></a>Schemat definicji usług w warstwie podstawowa
Plik definicji usługi musi zawierać jeden `ServiceDefinition` elementu. Definicja usługi musi zawierać co najmniej jedną rolę (`WebRole` lub `WorkerRole`) elementu. Nazwa może zawierać maksymalnie 25 zdefiniowanych w jednej definicji ról i możesz mieszać typy ról. Definicja usługi zawiera również opcjonalny `NetworkTrafficRules` element, który ogranicza możliwość użycia role można przekazać do określonej wewnętrznych punktów końcowych. Definicja usługi zawiera również opcjonalny `LoadBalancerProbes` zdefiniowany element, który zawiera klienta sond kondycji punktów końcowych.

Podstawowy format pliku definicji usługi jest w następujący sposób.

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

## <a name="schema-definitions"></a>Definicje schematu
Schemat można znaleźć w następujących tematach:

- [LoadBalancerProbe — schemat](schema-csdef-loadbalancerprobe.md)
- [WebRole — schemat](schema-csdef-webrole.md)
- [WorkerRole — schemat](schema-csdef-workerrole.md)
- [NetworkTrafficRules — schemat](schema-csdef-networktrafficrules.md)

##  <a name="ServiceDefinition"></a> Element ServiceDefinition — Element
`ServiceDefinition` Element jest elementem najwyższego poziomu w pliku definicji usługi.

W poniższej tabeli opisano atrybuty `ServiceDefinition` elementu.

| Atrybut               | Opis |
| ----------------------- | ----------- |
| name                    |Wymagany. Nazwa usługi. Nazwa musi być unikatowa w ramach konta usługi.|
| topologyChangeDiscovery | Opcjonalny. Określa typ powiadomienia o zmianie topologii. Możliwe wartości:<br /><br /> -   `Blast` -Wysyła aktualizacji tak szybko, jak to możliwe do wszystkich wystąpień roli. Jeśli opcja jest wybrana, roli powinno być możliwe do obsługi aktualizacji topologii bez ponownego uruchamiania.<br />-   `UpgradeDomainWalk` — Wysyła je do każdego wystąpienia roli w sposób sekwencyjny poprzednie wystąpienie pomyślnie zatwierdzenia aktualizacji.|
| schemaVersion           | Opcjonalny. Określa wersję ze schematem definicji usługi. Wersja schematu umożliwia programowi Visual Studio wybierz odpowiednie narzędzia zestawu SDK na potrzeby sprawdzania poprawności schematu, jeśli jest zainstalowana więcej niż jedna wersja zestawu SDK side-by-side.|
| upgradeDomainCount      | Opcjonalny. Określa liczbę domen uaktualnienia, w których są przydzielane ról w tej usłudze. Wystąpienia roli są przydzielane do domeny uaktualnienia, gdy wdrażana jest usługa. Aby uzyskać więcej informacji, zobacz [zaktualizować rola usługi w chmurze lub wdrożenia](cloud-services-how-to-manage-portal.md#update-a-cloud-service-role-or-deployment).<br /><br /> Można określić maksymalnie 20 domen uaktualnienia. Jeśli nie zostanie określony, domyślna liczba domen uaktualnienia wynosi 5.|
