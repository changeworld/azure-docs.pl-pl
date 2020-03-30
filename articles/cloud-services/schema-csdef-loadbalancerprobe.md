---
title: Schemat def. loadbalancerprobe usług w chmurze platformy Azure | Dokumenty firmy Microsoft
description: Zdefiniowany przez klienta LoadBalancerProbe jest sondą kondycji punktów końcowych w wystąpieniach roli. Łączy się z rolami sieci web lub procesu roboczego w pliku definicji usługi.
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 14
author: georgewallace
ms.author: tagore
ms.openlocfilehash: 6d0e84b6724d9df4162d4be3e06a9952087a53a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537350"
---
# <a name="azure-cloud-services-definition-loadbalancerprobe-schema"></a>Schemat definicji definicji usług w chmurze platformy Azure
Sonda modułu równoważenia obciążenia jest zdefiniowaną przez klienta sondą kondycji punktów końcowych i punktów końcowych UDP w wystąpieniach roli. Nie `LoadBalancerProbe` jest elementem autonomicznym; jest połączony z rolą sieci web lub roli procesu roboczego w pliku definicji usługi. A `LoadBalancerProbe` może być używany przez więcej niż jedną rolę.

Domyślnym rozszerzeniem pliku definicji usługi jest .csdef.

## <a name="the-function-of-a-load-balancer-probe"></a>Funkcja sondy modułu równoważenia obciążenia
Moduł równoważenia obciążenia platformy Azure jest odpowiedzialny za routing ruchu przychodzącego do wystąpień roli. Moduł równoważenia obciążenia określa, które wystąpienia mogą odbierać ruch, regularnie sondując każde wystąpienie w celu określenia kondycji tego wystąpienia. Moduł równoważenia obciążenia sonduje każde wystąpienie wiele razy na minutę. Istnieją dwie różne opcje zapewnienia kondycji wystąpienia modułowi równoważenia obciążenia — domyślna sonda modułu równoważenia obciążenia lub niestandardowa sonda modułu równoważenia obciążenia, która jest implementowana przez zdefiniowanie LoadBalancerProbe w pliku csdef.

Domyślna sonda modułu równoważenia obciążenia wykorzystuje agenta gościa wewnątrz maszyny wirtualnej, który nasłuchuje i odpowiada z odpowiedzią HTTP 200 OK tylko wtedy, gdy wystąpienie jest w stanie Gotowy (na przykład gdy wystąpienie nie znajduje się w stanach Zajęty, Recykling, Zatrzymanie itp.). Jeśli agent gościa nie odpowiada http 200 OK, Azure Load Balancer oznacza wystąpienie jako nie odpowiada i przestaje wysyłać ruch do tego wystąpienia. Moduł równoważenia obciążenia platformy Azure kontynuuje pingowanie wystąpienia, a jeśli agent gościa odpowiada za pomocą protokołu HTTP 200, moduł równoważenia obciążenia platformy Azure ponownie wysyła ruch do tego wystąpienia. Podczas korzystania z roli sieci Web kod witryny sieci Web zazwyczaj działa w w3wp.exe, który nie jest monitorowany przez sieć szkieletową platformy Azure lub agenta gościa, co oznacza błędy w w3wp.exe (np. Odpowiedzi HTTP 500) nie jest zgłaszane do agenta gościa i moduł równoważenia obciążenia nie wie, aby wziąć to wystąpienie z obrotu.

Niestandardowa sonda modułu równoważenia obciążenia zastępuje domyślną sondę agenta gościa i umożliwia utworzenie własnej logiki niestandardowej w celu określenia kondycji wystąpienia roli. Moduł równoważenia obciążenia regularnie sonduje punkt końcowy (domyślnie co 15 sekund), a wystąpienie jest uwzględniane podczas obracania, jeśli odpowiada tcp ack lub HTTP 200 w okresie limitu czasu (domyślnie 31 sekund). Może to być przydatne do zaimplementowania własnej logiki, aby usunąć wystąpienia z rotacji modułu równoważenia obciążenia, na przykład zwracając stan innych niż 200, jeśli wystąpienie jest powyżej 90% procesora CPU. W przypadku ról sieci web przy użyciu programu w3wp.exe oznacza to również automatyczne monitorowanie witryny sieci Web, ponieważ błędy w kodzie witryny zwracają stan inny niż 200 do sondy modułu równoważenia obciążenia. Jeśli nie zdefiniujesz LoadBalancerProbe w pliku csdef, zostanie użyte domyślne zachowanie modułu równoważenia obciążenia (zgodnie z wcześniejszym opisem).

Jeśli używasz niestandardowej sondy modułu równoważenia obciążenia, należy upewnić się, że logika bierze pod uwagę RoleEnvironment.OnStop metody. Podczas korzystania z domyślnej sondy modułu równoważenia obciążenia, wystąpienie jest wyjęte z obrotu przed OnStop wywoływane, ale sondy niestandardowego modułu równoważenia obciążenia może nadal zwracać 200 OK podczas OnStop zdarzenia. Jeśli używasz OnStop zdarzenia do czyszczenia pamięci podręcznej, zatrzymać usługi lub w inny sposób wprowadzania zmian, które mogą mieć wpływ na zachowanie środowiska wykonawczego usługi, a następnie należy upewnić się, że logika sondy niestandardowego modułu równoważenia obciążenia usuwa wystąpienie z obrotu.

## <a name="basic-service-definition-schema-for-a-load-balancer-probe"></a>Podstawowy schemat definicji usługi dla sondy równoważenia obciążenia
 Podstawowy format pliku definicji usługi zawierającego sondę modułu równoważenia obciążenia jest następujący.

```xml
<ServiceDefinition …>
   <LoadBalancerProbes>
      <LoadBalancerProbe name="<load-balancer-probe-name>" protocol="[http|tcp]" path="<uri-for-checking-health-status-of-vm>" port="<port-number>" intervalInSeconds="<interval-in-seconds>" timeoutInSeconds="<timeout-in-seconds>"/>
   </LoadBalancerProbes>
</ServiceDefinition>
```

## <a name="schema-elements"></a>Elementy schematu
Element `LoadBalancerProbes` pliku definicji usługi zawiera następujące elementy:

- [LoadBalancerProbes Element](#LoadBalancerProbes)
- [LoadBalancerProbe Element](#LoadBalancerProbe)

##  <a name="loadbalancerprobes-element"></a><a name="LoadBalancerProbes"></a>LoadBalancerProbes Element
Element `LoadBalancerProbes` opisuje kolekcję sondy modułu równoważenia obciążenia. Ten element jest elementem nadrzędnym [LoadBalancerProbe Element](#LoadBalancerProbe). 

##  <a name="loadbalancerprobe-element"></a><a name="LoadBalancerProbe"></a>LoadBalancerProbe Element
Element `LoadBalancerProbe` definiuje sondę kondycji dla modelu. Można zdefiniować wiele sond modułu równoważenia obciążenia. 

W poniższej tabeli `LoadBalancerProbe` opisano atrybuty elementu:

|Atrybut|Typ|Opis|
| ------------------- | -------- | -----------------|
| `name`              | `string` | Wymagany. Nazwa sondy modułu równoważenia obciążenia. Nazwa musi być unikatowa.|
| `protocol`          | `string` | Wymagany. Określa protokół punktu końcowego. Możliwe wartości to `http` lub `tcp`. Jeśli `tcp` jest określony, odebrane ACK jest wymagane dla sondy, aby zakończyć się pomyślnie. Jeśli `http` jest określony, odpowiedź 200 OK z określonego identyfikatora URI jest wymagana dla sondy, aby zakończyć się pomyślnie.|
| `path`              | `string` | Identyfikator URI używany do żądania stanu kondycji z maszyny Wirtualnej. `path`jest wymagana, `protocol` jeśli `http`jest ustawiona na . W przeciwnym razie nie jest dozwolone.<br /><br /> Nie ma żadnej wartości domyślnej.|
| `port`              | `integer` | Element opcjonalny. Port do komunikowania sondy. Jest to opcjonalne dla dowolnego punktu końcowego, ponieważ ten sam port będzie następnie używany dla sondy. Można skonfigurować inny port do ich sondowania, jak również. Możliwe wartości wahają się od 1 do 65535 włącznie.<br /><br /> Wartość domyślna jest ustawiana przez punkt końcowy.|
| `intervalInSeconds` | `integer` | Element opcjonalny. Interwał w sekundach, jak często sondować punkt końcowy dla stanu kondycji. Zazwyczaj interwał jest nieco mniej niż połowa przydzielonego limitu czasu (w sekundach), który umożliwia dwie pełne sondy przed podjęciem wystąpienia z obrotu.<br /><br /> Wartość domyślna to 15, minimalna wartość to 5.|
| `timeoutInSeconds`  | `integer` | Element opcjonalny. Limit czasu w sekundach, stosowane do sondy, gdzie żadna odpowiedź spowoduje zatrzymanie dalszego ruchu z dostarczania do punktu końcowego. Ta wartość umożliwia punkty końcowe, które mają być wyjęte z rotacji szybciej lub wolniej niż typowe czasy używane na platformie Azure (które są wartościami domyślnymi).<br /><br /> Wartość domyślna to 31, minimalna wartość to 11.|

## <a name="see-also"></a>Zobacz też
[Schemat definicji usługi w chmurze (klasycznej)](schema-csdef-file.md)