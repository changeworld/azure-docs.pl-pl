---
title: Azure Cloud Services Def. LoadBalancerProbe Schema | Microsoft Docs
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: 113374a8-8072-4994-9d99-de391a91e6ea
caps.latest.revision: 14
author: jpconnock
ms.author: jeconnoc
manager: timlt
ms.openlocfilehash: de365de7bf93c0a612f102b3ec2b25c79d1c3d18
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60613864"
---
# <a name="azure-cloud-services-definition-loadbalancerprobe-schema"></a>Azure Cloud Services, definicja loadbalancerprobe — schemat
Sonda modułu równoważenia obciążenia jest klient sondy kondycji zdefiniowanych punktów końcowych protokołu UDP i punktów końcowych w wystąpieniach roli. `LoadBalancerProbe` Nie jest elementem autonomiczny; jest połączona z roli sieci web lub roli procesu roboczego w pliku definicji usługi. A `LoadBalancerProbe` mogą być używane przez więcej niż jednej roli.

Domyślnym rozszerzeniem dla pliku definicji usługi jest csdef.

## <a name="the-function-of-a-load-balancer-probe"></a>Funkcja sondy modułu równoważenia obciążenia
Równoważenie obciążenia Azure jest odpowiedzialny za routing ruchu przychodzącego do wystąpień roli. Moduł równoważenia obciążenia określa, które wystąpienia mogą odbierać ruch regularnie sondowanie każde wystąpienie, aby można było określić kondycji tego wystąpienia. Moduł równoważenia obciążenia, sondy każde wystąpienie wiele razy na minutę. Dostępne są dwie opcje zapewniające kondycji wystąpień do modułu równoważenia obciążenia — domyślna sonda modułu równoważenia obciążenia, lub sondy modułu równoważenia obciążenia niestandardowego, który jest implementowany przez definiowanie loadbalancerprobe — w pliku csdef.

Sonda modułu równoważenia obciążenia domyślne korzysta z agenta gościa na maszynie wirtualnej, która odbiera i odpowiada za pomocą odpowiedź HTTP 200 OK, tylko wtedy, gdy wystąpienie jest w stanie gotowości (np. gdy wystąpienie nie jest w stanie zajęta, odtwarzania, zatrzymywanie, stanów itd.). Jeśli Agent gościa nie odpowiada przy użyciu protokołu HTTP 200 OK, usługi Azure Load Balancer oznacza wystąpienie odpowiada i przestaje wysyłać ruch do tego wystąpienia. Usługi Azure Load Balancer w dalszym ciągu polecenia ping wystąpienia i, jeśli Agent gościa odpowiada za pomocą protokołu HTTP 200, usługi Azure Load Balancer wysyła ruch do tego wystąpienia ponownie. Korzystając z roli sieci web kodu witryny sieci Web jest zwykle działa w w3wp.exe, który nie jest monitorowany przez sieci szkieletowej platformy Azure lub agenta gościa, co oznacza błędów w3wp.exe (np.) Odpowiedzi HTTP 500) nie jest zgłaszany do agenta gościa i obciążenia równoważenia nie wie, do wykonania tego wystąpienia z rotacji.

Sonda modułu równoważenia obciążenia niestandardowe zastąpienia domyślnej funkcji badania agenta gościa i pozwala na tworzenie własnej logiki niestandardowej można określić kondycji wystąpienia roli. Moduł równoważenia obciążenia regularnie sondy punktu końcowego (co 15 sekund, domyślnie), a wystąpienie jest w brana pod uwagę obrotu, gdy odpowiada za pomocą potwierdzenia TCP lub HTTP 200 przed upływem limitu czasu (domyślnie 31 sekund). Może to być przydatne zaimplementowanie własnej logiki, można usunąć wystąpienia z rotacji modułu równoważenia obciążenia, na przykład, jeśli wystąpienie jest ponad 90% Procesora, zwracając stanu – 200. Dla ról sieci web przy użyciu w3wp.exe, oznacza to również uzyskasz automatyczne monitorowanie witryny sieci Web, ponieważ błędów w kodzie witryny sieci Web zwrócenia stanu – 200 do sondy modułu równoważenia obciążenia. Jeśli loadbalancerprobe — nie zostaną zdefiniowane w pliku csdef, a następnie domyślne zachowanie usługi równoważenia obciążenia (jak opisano wcześniej) ma być używany.

Jeśli używasz sondy modułu równoważenia obciążenia niestandardowych, upewnij się, że logika uwzględnia metoda RoleEnvironment.OnStop. Korzystając z usługi równoważenia obciążenia domyślnej funkcji badania, wystąpienie jest wykluczana z rotacji przed OnStop wywoływana, ale sondy modułu równoważenia obciążenia niestandardowe mogą w dalszym ciągu zwrócić 200 OK podczas zdarzenia OnStop. Jeśli używasz zdarzeń OnStop wyczyścić pamięć podręczną, zatrzymanie usługi lub w przeciwnym razie wprowadzania zmian, które mogą wpłynąć na działanie usługi, należy upewnić się, że logika sondy modułu równoważenia obciążenia niestandardowego spowoduje usunięcie wystąpienia z obrotu.

## <a name="basic-service-definition-schema-for-a-load-balancer-probe"></a>Schemat definicji usług w warstwie podstawowa dla sondy modułu równoważenia obciążenia
 Podstawowy format pliku definicji usługi, zawierający sondy modułu równoważenia obciążenia jest w następujący sposób.

```xml
<ServiceDefinition …>
   <LoadBalancerProbes>
      <LoadBalancerProbe name="<load-balancer-probe-name>" protocol="[http|tcp]" path="<uri-for-checking-health-status-of-vm>" port="<port-number>" intervalInSeconds="<interval-in-seconds>" timeoutInSeconds="<timeout-in-seconds>"/>
   </LoadBalancerProbes>
</ServiceDefinition>
```

## <a name="schema-elements"></a>Elementy schematu
`LoadBalancerProbes` Element pliku definicji usługi obejmuje następujące elementy:

- [LoadBalancerProbes Element](#LoadBalancerProbes)
- [LoadBalancerProbe Element](#LoadBalancerProbe)

##  <a name="LoadBalancerProbes"></a> LoadBalancerProbes Element
`LoadBalancerProbes` Element opisuje kolekcję sondy modułu równoważenia obciążenia. Ten element jest elementem nadrzędnym [loadbalancerprobe — Element](#LoadBalancerProbe). 

##  <a name="LoadBalancerProbe"></a> LoadBalancerProbe Element
`LoadBalancerProbe` Element definiuje sondy kondycji dla modelu. Można zdefiniować wiele sondy modułu równoważenia obciążenia. 

W poniższej tabeli opisano atrybuty `LoadBalancerProbe` elementu:

|Atrybut|Type|Opis|
| ------------------- | -------- | -----------------|
| `name`              | `string` | Wymagany. Nazwa sondy modułu równoważenia obciążenia. Nazwa musi być unikatowa.|
| `protocol`          | `string` | Wymagany. Określa protokół punktu końcowego. Możliwe wartości to `http` lub `tcp`. Jeśli `tcp` określono ACK odebrane jest wymagany dla sondy zakończy się powodzeniem. Jeśli `http` jest określona, odpowiedź 200 OK z określonego identyfikatora URI jest wymagany dla sondy zakończy się powodzeniem.|
| `path`              | `string` | Identyfikator URI używany do żądania stanu kondycji z maszyny Wirtualnej. `path` jest wymagany, jeśli `protocol` ustawiono `http`. W przeciwnym razie jest niedozwolona.<br /><br /> Brak wartości domyślnej.|
| `port`              | `integer` | Opcjonalny. Port sondy przekazywania. Jest to opcjonalne dla dowolnego punktu końcowego, ponieważ tego samego portu zostaną następnie użyte dla sondy. Można skonfigurować inny port dla ich badania, jak również. Możliwe wartości z zakresu od 1 do 65535 (włącznie).<br /><br /> Wartość domyślna jest ustawiana przez punkt końcowy.|
| `intervalInSeconds` | `integer` | Opcjonalny. Interwał w sekundach, jak często sondy stanu kondycji punktu końcowego. Zazwyczaj interwału jest nieco mniej niż połowy przydzielony limit czasu (w sekundach) umożliwiający dwa pełne sondy przed przełączeniem wystąpienia z rotacji.<br /><br /> Wartość domyślna to 15, wartość minimalna wynosi 5.|
| `timeoutInSeconds`  | `integer` | Opcjonalny. Limit czasu w sekundach, są stosowane do badania, w których brak odpowiedzi spowoduje zatrzymanie dalej ruch z są dostarczane do punktu końcowego. Ta wartość umożliwia punktów końcowych do wykonania z rotacji szybszy lub wolniejszy niż typowy czas, w którym używane na platformie Azure (które są wartości domyślne).<br /><br /> Wartość domyślna to 31, 11 wartość minimalna.|

## <a name="see-also"></a>Zobacz też
[Chmury usługi (model klasyczny) definicji schematu](schema-csdef-file.md)