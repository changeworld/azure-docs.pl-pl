---
title: Użyj niestandardowej sondy modułu równoważenia obciążenia do monitorowania stanu kondycji | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać niestandardowe sondy modułu równoważenia obciążenia Azure do monitorowania wystąpień za modułem równoważenia obciążenia
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2018
ms.author: kumud
ms.openlocfilehash: 69991a0b805b5502fc96fab4ce902b3d8bc77baf
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39056362"
---
# <a name="understand-load-balancer-probes"></a>Opis sond modułu równoważenia obciążenia

Usługa Azure Load Balancer używa sondy kondycji, aby określić, które wystąpienie puli serwerów zaplecza powinien zostać wyświetlony nowych przepływów.   Za pomocą sondy kondycji do wykrywania awarii aplikacji na wystąpienie wewnętrznej bazy danych.  Odpowiedź sondy kondycji aplikacji umożliwia również zasygnalizowania do modułu równoważenia obciążenia, czy kontynuować wysyłanie nowych przepływów lub zatrzymać wysyłanie nowych przepływów do wystąpienia wewnętrznej bazy danych, aby zarządzać obciążenia lub planowanych przestojów.

Sondy kondycji określają, czy nowych przepływów są ustanowione do wystąpień zaplecza w dobrej kondycji. W przypadku awarii sondę kondycji modułu równoważenia obciążenia zatrzymuje wysyłanie nowych przepływów do odpowiednich wystąpień złej kondycji.  Ustanowionych połączeń TCP nadal po niepowodzeniu sondy kondycji.  Istniejące przepływy UDP zostanie przesunięty w złej kondycji wystąpieniu do innego wystąpienia w puli zaplecza.

W przypadku awarii wszystkich sondy dla puli zaplecza, podstawowe usługi równoważenia obciążenia spowoduje przerwanie działania wszystkie istniejące przepływy TCP do puli zaplecza, standardowego modułu równoważenia obciążenia będzie pozwalać na ustanowionych przepływy TCP, aby kontynuować; nie nowych przepływów będą wysyłane do puli zaplecza.

Role usługi w chmurze (role procesu roboczego i role sieci web) używać agenta gościa na potrzeby monitorowania sondowania. Musi być skonfigurowany protokół TCP lub HTTP niestandardowe sondy kondycji, gdy używasz usługi w chmurze przy użyciu maszyn wirtualnych IaaS za modułem równoważenia obciążenia.

## <a name="understand-probe-count-and-timeout"></a>Liczba sondowania i limit czasu

Zachowanie sondy zależy od:

* Liczba pomyślnych sond, zezwalających na wystąpienie, które oznaczone jako czas.
* Liczba nieudanych sond, powodujące wystąpienie, które oznaczone jako w dół.

Limit czasu i częstotliwość wartości SuccessFailCount ustalić, czy wystąpienie jest potwierdzone, że jest uruchomiona lub nie działa. W witrynie Azure portal limit czasu jest równa dwa razy wartości częstotliwości.

Konfiguracja sondy wszystkich wystąpień ze zrównoważonym obciążeniem dla punktu końcowego (oznacza to, — zestawu o zrównoważonym obciążeniu) musi być taka sama. Nie może mieć konfigurację sondowania różne dla każdego wystąpienia roli lub maszyny Wirtualnej w tej samej usłudze hostowanej dla kombinacji konkretny punkt końcowy. Na przykład każde wystąpienie musi mieć identyczny porty lokalne i limity czasu.

> [!IMPORTANT]
> Sonda modułu równoważenia obciążenia korzysta z adresu IP 168.63.129.16. Ten publiczny adres IP usprawnia komunikację do zasobów wewnętrznych platformy dla bring-your właścicielem — adres IP scenariusz sieci wirtualnych platformy Azure. Wirtualny adres IP publicznego 168.63.129.16 jest używany we wszystkich regionach, a nie zmienia się. Zaleca się zezwolić na ten adres IP w żadnych zasadach zapory lokalnej. Go nie należy rozważyć zagrożenie bezpieczeństwa, ponieważ tylko wewnętrzne platformy Azure można źródła wiadomości z tego adresu. Jeśli w zasadach zapory nie zezwalaj na ten adres IP, wystąpi nieoczekiwane zachowanie w różnych scenariuszach. Zachowanie obejmuje Konfigurowanie tego samego zakresu adresów IP z adresu 168.63.129.16 i duplikowanie IP adresów.

## <a name="learn-about-the-types-of-probes"></a>Informacje o typach sondy

### <a name="guest-agent-probe"></a>Sondowanie agenta gościa

Sondowanie agenta gościa jest dostępna dla usług Azure Cloud Services tylko. Moduł równoważenia obciążenia korzysta z agenta gościa wewnątrz maszyny Wirtualnej. Następnie odbiera i odpowiada za pomocą odpowiedź HTTP 200 OK, tylko wtedy, gdy wystąpienie jest w stanie gotowe. (Inne stany są zajęte, odtwarzanie lub zatrzymywania).

Aby uzyskać więcej informacji, zobacz [Konfigurowanie pliku definicji usługi (csdef) dla sondy kondycji](https://msdn.microsoft.com/library/azure/ee758710.aspx) lub [zacznij od utworzenia publicznego modułu równoważenia obciążenia dla usług w chmurze](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services).

### <a name="what-makes-a-guest-agent-probe-mark-an-instance-as-unhealthy"></a>Co sprawia, że sondowanie agenta gościa, Oznacz wystąpienia o złej kondycji?

Jeśli agent gościa nie odpowiada przy użyciu protokołu HTTP 200 OK, moduł równoważenia obciążenia oznacza wystąpienie jako nie odpowiada. Następnie zatrzymuje, ruch wysyłany do tego wystąpienia. Moduł równoważenia obciążenia w dalszym ciągu polecenia ping wystąpienia. Jeśli agent gościa odpowiada za pomocą protokołu HTTP 200, moduł równoważenia obciążenia wysyła ruch do tego wystąpienia ponownie.

Korzystając z roli sieci web, kod witryny sieci Web jest zwykle działa w w3wp.exe, który nie jest monitorowane przez usługę Azure Service fabric lub gościa agenta. Błędy w w3wp.exe (na przykład odpowiedzi HTTP 500) nie są zgłaszane do agenta gościa. W związku z tym moduł równoważenia obciążenia nie przyjmuje tego wystąpienia z rotacji.

### <a name="http-custom-probe"></a>Niestandardowe sondy HTTP

Niestandardowej sondy HTTP zastąpienia domyślnej funkcji badania agenta gościa. Możesz utworzyć własną logikę niestandardową można określić kondycji wystąpienia roli. Moduł równoważenia obciążenia sondy punktu końcowego co 15 sekund domyślnie. Wystąpienie jest uważany w rotacji modułu równoważenia obciążenia, gdy odpowiada za pomocą protokołu HTTP 200 przed upływem limitu czasu. Limit czasu jest 31 sekund domyślnie.

Niestandardowej sondy HTTP może być przydatne, jeśli chcesz zaimplementować logikę do usuwania wystąpień z rotacji modułu równoważenia obciążenia. Na przykład można zdecydować usunąć wystąpienie, jeśli jest ponad 90% Procesora i zwraca stan – 200. W przypadku ról sieci web, które używają w3wp.exe uzyskasz także automatyczne monitorowanie witryny sieci Web. Błędy w kodzie witryny sieci Web zwrócenia stanu – 200 do sondy modułu równoważenia obciążenia.

> [!NOTE]
> Niestandardowej sondy HTTP obsługuje ścieżki względne i tylko protokół HTTP. Protokół HTTPS nie jest obsługiwane.

### <a name="what-makes-an-http-custom-probe-mark-an-instance-as-unhealthy"></a>Co sprawia, że niestandardowej sondy HTTP oznaczyć wystąpienia o złej kondycji?

* Aplikacja HTTP zwraca kod odpowiedzi HTTP inne niż 200 (na przykład, 403, 404 lub 500). Tej pozytywnych potwierdzenia powiadomi o konieczności natychmiast podjąć wystąpienie aplikacji z usługi.
* Serwer HTTP nie odpowiada po upływie limitu czasu. W zależności od wartość limitu czasu, który jest ustawiony, wielokrotne żądania sondowania mogą zostać przekazane bez odpowiedzi, zanim sondy zostanie oznaczone jako nieuruchomiona (czyli przed SuccessFailCount sondy są wysyłane).
* Serwer zamyka połączenie za pośrednictwem resetowania TCP.

### <a name="tcp-custom-probe"></a>Niestandardową sondę TCP

Niestandardowe sondy TCP zainicjować połączenie, wykonując trójstopniowego z zdefiniowany port.

### <a name="what-makes-a-tcp-custom-probe-mark-an-instance-as-unhealthy"></a>Co sprawia, że niestandardową sondę TCP oznaczyć wystąpienia o złej kondycji?

* Serwer protokołu TCP nie odpowiada po upływie limitu czasu. Podczas sondowania jest oznaczony jako nieuruchomiona zależy od liczby żądań sondy nie powiodło się, które zostały skonfigurowane do bez odpowiedzi zanim oznaczysz sondy jako nie jest uruchomiona.
* Sonda odbiera TCP zresetować z wystąpienia roli.

Aby uzyskać więcej informacji o sposobie konfigurowania sondę kondycji HTTP lub sonda TCP, zobacz [wprowadzenie do tworzenia publicznego modułu równoważenia obciążenia w usłudze Resource Manager przy użyciu programu PowerShell](load-balancer-get-started-internet-arm-ps.md).

## <a name="add-healthy-instances-back-into-the-load-balancer-rotation"></a>Dodaj dobrej kondycji wystąpień do rotacji modułu równoważenia obciążenia

Sondy protokołu TCP i HTTP są traktowane jako zdrowych i Oznacz wystąpienia roli, co działa prawidłowo, gdy:

* Moduł równoważenia obciążenia pobiera sondę dodatnią rozruchu maszyny Wirtualnej po raz pierwszy.
* Numer SuccessFailCount (opisanym wcześniej) definiuje wartość pomyślne sond, które są wymagane do oznaczenia wystąpienia roli jako w dobrej kondycji. Jeśli wystąpienie roli zostało usunięte, liczbę pomyślnych, kolejne sond musisz równa lub przekracza wartość SuccessFailCount do oznaczenia wystąpienia roli, co działa.

> [!NOTE]
> Jeśli zmienia się kondycję wystąpienia roli, usługi równoważenia obciążenia już przed oczekuje umieszcza wystąpienia roli w dobrej kondycji. Czas oczekiwania dodatkowe chroni użytkownika i infrastruktura i zamierzone zasady.

## <a name="use-log-analytics-for-a-load-balancer"></a>Używanie programu log analytics dla usługi load balancer

Możesz użyć [dziennika analizy](load-balancer-monitor-log.md) można sprawdzić stanu zdrowia sondy modułu równoważenia obciążenia i sondowania count. Rejestrowanie może służyć za pomocą usługi Power BI lub usługi Azure Operational Insights umożliwia statystyki dotyczące stanu kondycji modułu równoważenia obciążenia.
