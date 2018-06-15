---
title: Azure Cloud Services definicję schematu (plik csdef) | Dokumentacja firmy Microsoft
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
author: thraka
ms.author: adegeo
manager: timlt
ms.openlocfilehash: d5e086a952e18e477177634e5c197c27d4a5cc5f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34359020"
---
# <a name="azure-cloud-services-definition-schema-csdef-file"></a>Schemat definicji usług w chmurze Azure (plik csdef)
Plik definicji usługi definiuje modelu usług dla aplikacji. Plik zawiera definicje dla ról, które są dostępne dla usługi w chmurze, określa punkty końcowe usługi i określa ustawienia konfiguracji dla usługi. Wartości ustawienia konfiguracji są ustawione w pliku konfiguracji usługi w sposób opisany w [schemat konfiguracji usługi w chmurze (klasyczne)](http://msdn.microsoft.com/library/b1ae68cd-cc95-48cb-a4a4-da91dc708a35).

Domyślnie plik schematu konfiguracji diagnostyki Azure jest instalowany na `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` katalogu. Zastąp `<version>` z zainstalowaną wersją [zestawu Azure SDK](http://www.windowsazure.com/develop/downloads/).

Domyślne rozszerzenie pliku definicji usługi jest csdef.

## <a name="basic-service-definition-schema"></a>Podstawowa usługa definicji schematu
Plik definicji usługi musi zawierać jeden `ServiceDefinition` elementu. Definicja usługi musi zawierać co najmniej jedną rolę (`WebRole` lub `WorkerRole`) elementu. Nazwa może zawierać maksymalnie 25 role zdefiniowane w jednej definicji i można mieszać typów roli. Definicja usługi zawiera również opcjonalne `NetworkTrafficRules` element, który ogranicza role mogą komunikować się do określonych wewnętrznych punktów końcowych. Definicja usługi zawiera również opcjonalne `LoadBalancerProbes` zdefiniowany element, który zawiera klienta sondy kondycji punktów końcowych.

Podstawowy format pliku definicji usługi ma następującą składnię.

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
Schemat można znaleźć w następujących tematach:

- [LoadBalancerProbe — schemat](schema-csdef-loadbalancerprobe.md)
- [WebRole — schemat](schema-csdef-webrole.md)
- [WorkerRole — schemat](schema-csdef-workerrole.md)
- [NetworkTrafficRules — schemat](schema-csdef-networktrafficrules.md)

##  <a name="ServiceDefinition"></a> ServiceDefinition Element
`ServiceDefinition` Element jest elementem najwyższego poziomu w pliku definicji usługi.

W poniższej tabeli opisano atrybuty `ServiceDefinition` elementu.

| Atrybut               | Opis |
| ----------------------- | ----------- |
| name                    |Wymagany. Nazwa usługi. Nazwa musi być unikatowa w ramach konta usługi.|
| topologyChangeDiscovery | Opcjonalny. Określa typ powiadomienia o zmianie topologii. Możliwe wartości:<br /><br /> -   `Blast` -Wysyła aktualizacji tak szybko, jak to możliwe do wszystkich wystąpień ról. Po wybraniu opcji roli powinno być możliwe do obsługi aktualizacji topologii bez ponownego uruchamiania.<br />-   `UpgradeDomainWalk` — Wysyła aktualizacji do każdego wystąpienia roli sekwencyjnie poprzednie wystąpienie pomyślnie zatwierdzenia aktualizacji.|
| Atrybut schemaVersion           | Opcjonalny. Określa wersję schematu definicji usługi. Wersja schematu umożliwia programowi Visual Studio wybierz odpowiednie narzędzia zestawu SDK do użycia w celu weryfikacji schematu, jeśli zainstalowano więcej niż jedną wersję zestawu SDK side-by-side.|
| upgradeDomainCount      | Opcjonalny. Określa liczbę domen uaktualnienia, w których są przydzielane role w tej usłudze. Wystąpienia ról przypisuje się do domeny uaktualnienia, podczas wdrażania usługi. Aby uzyskać więcej informacji, zobacz [aktualizacji roli usługi w chmurze lub wdrożenia](cloud-services-how-to-manage-portal.md#update-a-cloud-service-role-or-deployment).<br /><br /> Można określić maksymalnie 20 domen uaktualnienia. Jeśli nie zostanie określony, domyślna liczba domen uaktualnienia wynosi 5.|