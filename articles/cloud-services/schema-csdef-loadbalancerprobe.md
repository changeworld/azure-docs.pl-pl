---
title: Domyślna usługi w chmurze Azure Schemat LoadBalancerProbe | Dokumentacja firmy Microsoft
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
author: thraka
ms.author: adegeo
manager: timlt
ms.openlocfilehash: 6cd56c9b04fc4657cedf845e7f111005a8dee183
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34360057"
---
# <a name="azure-cloud-services-definition-loadbalancerprobe-schema"></a>Definicja schematu LoadBalancerProbe usług w chmurze Azure
Sondę modułu równoważenia obciążenia jest klienta badanie kondycji zdefiniowanych punktów końcowych protokołu UDP i punktów końcowych w wystąpieniach ról. `LoadBalancerProbe` Nie jest elementem autonomiczny; jest połączona z rolą sieci web lub roli proces roboczy w pliku definicji usługi. A `LoadBalancerProbe` mogą być używane przez więcej niż jednej roli.

Domyślne rozszerzenie pliku definicji usługi jest csdef.

## <a name="the-function-of-a-load-balancer-probe"></a>Funkcja sondę modułu równoważenia obciążenia
Moduł równoważenia obciążenia Azure jest odpowiedzialny za routingu ruchu przychodzącego do wystąpienia roli. Usługi równoważenia obciążenia określa, które wystąpienia mogą odbierać dane za regularnie każde wystąpienie w celu ustalenia kondycji danego wystąpienia. Moduł równoważenia obciążenia sondy każde wystąpienie wiele razy na minutę. Dostępne są dwie opcje dostarczanie wystąpienia kondycji modułu równoważenia obciążenia — domyślną sondę modułu równoważenia obciążenia, lub sondy modułu równoważenia obciążenia niestandardowego, który jest implementowany przez definiowanie LoadBalancerProbe w pliku csdef.

Agent gościa na maszynie wirtualnej, która wykrywa i wysyła odpowiedź HTTP 200 OK tylko wtedy, gdy wystąpienie jest w stanie gotowe (na przykład jeśli wystąpienie nie jest zajęty, odtwarzanie, zatrzymywania, stany itp.) korzysta z domyślnej sondy modułu równoważenia obciążenia. Jeśli Agent gościa nie odpowiada HTTP 200 OK, usługi równoważenia obciążenia Azure oznacza wystąpienia jako odpowiadać i zatrzymuje wysyłania ruchu do tego wystąpienia. Usługi równoważenia obciążenia Azure w dalszym ciągu polecenie ping z wystąpieniem, a jeśli Agent gościa odpowie 200 protokołu HTTP, usługi równoważenia obciążenia Azure wysyła ruchu wystąpienia ponownie. Korzystając z roli sieci web kodu witryny sieci Web zwykle działa w w3wp.exe, który nie jest monitorowane przez sieci szkieletowej Azure lub agenta gościa, co oznacza błędów w w3wp.exe (np.) Odpowiedzi HTTP 500) nie został zgłoszony do agenta gościa i obciążenia modułu równoważenia nie zna do wykonania danego wystąpienia poza obrotu.

Sondę modułu równoważenia obciążenia niestandardowych zastępuje domyślną sondę agenta gościa i umożliwia utworzenie niestandardowej logiki do ustalenia stanu wystąpienia roli. Moduł równoważenia obciążenia regularnie sond punktu końcowego (co 15 sekund, domyślnie) i wystąpienie jest wziąć pod uwagę obrót, gdy odpowiada potwierdzenia TCP lub HTTP 200 przed upływem limitu czasu (domyślnie 31 sekund). Może to być przydatne do implementacji logiki można usunąć wystąpienia z obrotu usługi równoważenia obciążenia, na przykład zwracanie stanu – 200, jeśli wystąpienie jest ponad 90% zasobów Procesora. Dla ról sieci web przy użyciu w3wp.exe, oznacza to też, możesz uzyskać automatyczne monitorowania witryny sieci Web, ponieważ błędów w kodzie witryny sieci Web zwraca stan – 200 do sondę modułu równoważenia obciążenia. Jeśli LoadBalancerProbe nie zostaną zdefiniowane w piku .csdef, a następnie jest domyślne zachowanie usługi równoważenia obciążenia (opisanej powyżej) można użyć.

Jeśli używasz sondę modułu równoważenia obciążenia niestandardowych musi upewnij się, że logika uwzględnia metody RoleEnvironment.OnStop. Podczas korzystania z domyślnej sondy modułu równoważenia obciążenia, wystąpienie jest wykluczony z cyklu przed OnStop wywoływana, ale sondę modułu równoważenia obciążenia niestandardowe mogą w dalszym ciągu zwrócić 200 OK podczas zdarzenia OnStop. Jeśli używasz zdarzeń OnStop czyszczenie pamięci podręcznej, Zatrzymaj usługę lub w przeciwnym razie wprowadzania zmian, które mogą wpłynąć na działanie usługi, należy upewnić się, że logika sondy modułu równoważenia obciążenia niestandardowych usuwa wystąpienie z obrotu.

## <a name="basic-service-definition-schema-for-a-load-balancer-probe"></a>Podstawowa usługa definicji schematu dla sondę modułu równoważenia obciążenia
 Podstawowy format pliku definicji usługi zawierającego sondę modułu równoważenia obciążenia ma następującą składnię.

```xml
<ServiceDefinition …>
   <LoadBalancerProbes>
      <LoadBalancerProbe name="<load-balancer-probe-name>" protocol="[http|tcp]" path="<uri-for-checking-health-status-of-vm>" port="<port-number>" intervalInSeconds="<interval-in-seconds>" timeoutInSeconds="<timeout-in-seconds>"/>
   </LoadBalancerProbes>
</ServiceDefinition>
```

## <a name="schema-elements"></a>Elementy schematu
`LoadBalancerProbes` Element pliku definicji usługi obejmują następujące elementy:

- [LoadBalancerProbes Element](#LoadBalancerProbes)
- [LoadBalancerProbe Element](#LoadBalancerProbe)

##  <a name="LoadBalancerProbes"></a> LoadBalancerProbes Element
`LoadBalancerProbes` Element opisuje kolekcję sondy modułu równoważenia obciążenia. Ten element jest elementem nadrzędnym [LoadBalancerProbe elementu](#LoadBalancerProbe). 

##  <a name="LoadBalancerProbe"></a> LoadBalancerProbe Element
`LoadBalancerProbe` Element definiuje sondy kondycji dla modelu. Można zdefiniować wiele sondy modułu równoważenia obciążenia. 

W poniższej tabeli opisano atrybuty `LoadBalancerProbe` elementu:

|Atrybut|Typ|Opis|
| ------------------- | -------- | -----------------|
| `name`              | `string` | Wymagany. Nazwa sondę modułu równoważenia obciążenia. Nazwa musi być unikatowa.|
| `protocol`          | `string` | Wymagany. Określa protokół punktu końcowego. Możliwe wartości to `http` lub `tcp`. Jeśli `tcp` określono potwierdzenia odebranego jest wymagany dla sondy powiódł się. Jeśli `http` określono odpowiedź 200 OK z określonego identyfikatora URI jest wymagany dla sondy powiódł się.|
| `path`              | `string` | Identyfikator URI używany do żądania stanu kondycji z maszyny Wirtualnej. `path` jest wymagany, jeśli `protocol` ma ustawioną wartość `http`. W przeciwnym razie jest niedozwolona.<br /><br /> Brak wartości domyślnej.|
| `port`              | `integer` | Opcjonalny. Port sondy przekazywania. Jest to opcjonalne dla dowolnego punktu końcowego, ponieważ ten sam port zostanie następnie użyte sondy. Można skonfigurować inny port dla ich sondowanie, jak również. Możliwe wartości należą do zakresu od 1 do 65535 włącznie.<br /><br /> Wartość domyślna jest ustawiana przez punkt końcowy.|
| `intervalInSeconds` | `integer` | Opcjonalny. Interwał w sekundach częstotliwość do sondowania punktu końcowego na stan kondycji. Interwał jest zazwyczaj nieco poniżej połowy przydzielony limit czasu (w sekundach) umożliwiający dwóch sond pełne przed zmianą wystąpienia poza obrotu.<br /><br /> Wartość domyślna to 15, wartość minimalna wynosi 5.|
| `timeoutInSeconds`  | `integer` | Opcjonalny. Limit czasu w sekundach, są stosowane do sondowania, w którym brak odpowiedzi spowoduje zatrzymanie dalej ruch z są dostarczane do punktu końcowego. Ta wartość umożliwia punkty końcowe podejmowane poza obrotu szybciej lub wolniej niż razy typowe używane na platformie Azure (które są ustawienia domyślne).<br /><br /> Wartość domyślna to 31, 11 wartość minimalna.|

## <a name="see-also"></a>Zobacz też
[Schematu definicji (klasyczny) usługi w chmurze](schema-csdef-file.md)