---
title: Użyć niestandardowego sondy modułu równoważenia obciążenia do monitorowania stanu kondycji | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać niestandardowych sondy dla usługi równoważenia obciążenia Azure do monitorowania wystąpień za usługą równoważenia obciążenia
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
ms.date: 03/8/2018
ms.author: kumud
ms.openlocfilehash: 0aab72fdf48589a72707ae87f90af11f65f35088
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
ms.locfileid: "30176792"
---
# <a name="understand-load-balancer-probes"></a>Zrozumienie sondy modułu równoważenia obciążenia

Moduł równoważenia obciążenia Azure używa sondy kondycji w celu określenia, wystąpieniu puli wewnętrznej bazy danych, z którym powinien zostać wyświetlony nowy przepływów. Badania kondycji nie powiedzie się, usługi równoważenia obciążenia zatrzyma się wysyłanie nowych przepływów do odpowiedniego wystąpienia złej kondycji i nie dotyczy to jednak istniejących przepływów w tym wystąpieniu.  Gdy wszystkie wystąpienia puli zaplecza sondowania w dół, limit czasu we wszystkich wystąpieniach w puli zaplecza będzie wszystkich istniejących przepływów.

Role usługi w chmurze (role procesów roboczych oraz role sieci web) skorzystać agenta gościa w celu monitorowania sondowania. Sondy kondycji niestandardowych TCP lub HTTP musi być skonfigurowany, korzystając z maszyn wirtualnych za usługą równoważenia obciążenia.

## <a name="understand-probe-count-and-timeout"></a>Licznik sondy i limit czasu

Zachowanie sondowania zależy od:

* Liczba pomyślnych sond, umożliwiających wystąpienia oznaczone jako czas.
* Liczba nieudanych sond, powodujących wystąpienia oznaczone etykietą w dół.

Limit czasu i częstotliwość wartości SuccessFailCount określić, czy wystąpienie jest potwierdzone uruchomiona lub nie działa. W portalu Azure limit czasu wynosi dwa razy wartości częstotliwości.

Badania konfiguracji wszystkich wystąpień równoważeniem obciążenia dla punktu końcowego (czyli zestaw z równoważeniem obciążenia) muszą być takie same. Nie ma konfiguracji różnych sondowania dla każdego wystąpienia roli lub maszyny Wirtualnej w tej samej usługi hostowanej kombinacji danego punktu końcowego. Na przykład każde wystąpienie musi mieć identyczne porty lokalne i przekroczeń limitu czasu.

> [!IMPORTANT]
> Sondę modułu równoważenia obciążenia używa adresu IP 168.63.129.16. Ten publiczny adres IP umożliwia komunikację do zasobów wewnętrznych platformy dla bring-your — właścicielem — adresu IP scenariusz sieci wirtualnej platformy Azure. Wirtualny adres IP publicznego 168.63.129.16 jest używany we wszystkich regionach, a nie zmienia się. Firma Microsoft zaleca Zezwalaj ten adres IP w żadnych zasad zapory lokalnej. Go nie należy traktować jako zagrożenie bezpieczeństwa, ponieważ tylko wewnętrzny platformy Azure może pobierać wiadomości z tego adresu. Jeśli nie zezwolisz na ten adres IP w zasadach zapory, nieoczekiwane zachowanie w różnych scenariuszach. Zachowanie obejmuje Konfigurowanie tego samego zakresu adresów IP 168.63.129.16 i adresy IP duplikowania.

## <a name="learn-about-the-types-of-probes"></a>Informacje o typach sondy

### <a name="guest-agent-probe"></a>Sondowanie agenta gościa

Sondowanie agenta gościa jest dostępna dla usług Azure Cloud Services tylko. Agent gościa w ramach maszyny Wirtualnej korzysta z usługi równoważenia obciążenia. Następnie nasłuchuje i wysyła odpowiedź HTTP 200 OK tylko wtedy, gdy wystąpienie jest w stanie gotowe. (Innych stanów są zajęte, odtwarzanie lub zatrzymywanie).

Aby uzyskać więcej informacji, zobacz [Konfigurowanie pliku definicji usługi (csdef) dla sondy kondycji](https://msdn.microsoft.com/library/azure/ee758710.aspx) lub [Rozpoczynanie pracy przez utworzenie publiczny moduł równoważenia obciążenia dla usług w chmurze](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services).

### <a name="what-makes-a-guest-agent-probe-mark-an-instance-as-unhealthy"></a>Dzięki czemu sondowanie agenta gościa, Oznacz wystąpienia swój stan jako niezdrowy?

Jeśli agent gościa nie odpowiada HTTP 200 OK, usługi równoważenia obciążenia oznacza wystąpienia jako nie odpowiada. Następnie zatrzymuje wysyłania ruchu do tego wystąpienia. Moduł równoważenia obciążenia w dalszym ciągu polecenie ping z wystąpieniem. Jeśli agent gościa odpowie 200 protokołu HTTP, usługi równoważenia obciążenia wysyła ruch do tego wystąpienia ponownie.

Korzystając z roli sieci web, kodu witryny sieci Web zwykle działa w w3wp.exe, który nie jest monitorowane przez program Azure agenta sieci szkieletowej lub gościa. Agent gościa nie są zgłaszane błędy w w3wp.exe (na przykład odpowiedzi HTTP 500). W rezultacie usługi równoważenia obciążenia nie przyjmuje tego wystąpienia poza obrotu.

### <a name="http-custom-probe"></a>Niestandardowe badanie HTTP

Badanie niestandardowych HTTP zastąpienia domyślnej funkcji badania agenta gościa. Możesz utworzyć własne niestandardowej logiki do ustalenia stanu wystąpienia roli. Moduł równoważenia obciążenia sondy punktu końcowego co 15 s domyślnie. Wystąpienie jest uważana rotacji usługi równoważenia obciążenia, gdy odpowiada z 200 protokołu HTTP przed upływem limitu czasu. Limit czasu jest 31 sekund domyślnie.

Badanie niestandardowych HTTP może być przydatna do implementacji logiki można usunąć wystąpienia z obrotu usługi równoważenia obciążenia. Na przykład można zdecydować usunąć wystąpienia, jeśli jest ponad 90% zasobów Procesora i zwraca stan – 200. Jeśli masz role sieci web, które używają w3wp.exe, możesz również uzyskać automatyczne monitorowania witryny sieci Web. Błędy w kodzie witryny sieci Web zwracają stan – 200 sondę modułu równoważenia obciążenia.

> [!NOTE]
> Badanie niestandardowych HTTP obsługuje ścieżek względnych i tylko protokołu HTTP. Protokół HTTPS nie jest obsługiwana.

### <a name="what-makes-an-http-custom-probe-mark-an-instance-as-unhealthy"></a>Co sprawia, że badanie niestandardowych HTTP oznaczyć swój stan jako niezdrowy wystąpienia?

* Aplikacja HTTP zwraca kod odpowiedzi HTTP innych niż 200 (na przykład 403, 404 lub 500). To potwierdzenie pozytywne alerty chcesz przejąć wystąpienia aplikacji poza eksploatacją od razu.
* Serwer HTTP nie odpowiada po upływie limitu czasu. W zależności od tego, która jest ustawiona wartość limitu czasu wiele żądań sondowania może przejść bez odpowiedzi, przed sondy pobiera oznaczona jako nieuruchomiona (czyli przed SuccessFailCount sond są wysyłane).
* Serwer zamyka połączenie za pośrednictwem resetowania TCP.

### <a name="tcp-custom-probe"></a>Niestandardowe sondowaniem TCP

Niestandardowe sond TCP nawiązania połączenia, wykonując trójstopniowego z zdefiniowany port.

### <a name="what-makes-a-tcp-custom-probe-mark-an-instance-as-unhealthy"></a>Dzięki czemu niestandardowy sondowaniem TCP oznaczyć swój stan jako niezdrowy wystąpienia?

* Serwer protokołu TCP nie odpowiada po upływie limitu czasu. Podczas sondowania jest oznaczony jako nieuruchomiona zależy od liczby żądań zakończonych niepowodzeniem sondowania, skonfigurowane w taki sposób, aby przejść bez odpowiedzi przed oznaczeniem sondowania jako nie działa.
* Sonda odbiera TCP zresetować z wystąpienia roli.

Aby uzyskać więcej informacji na temat konfigurowania sondy kondycji protokołu HTTP lub sondowaniem TCP, zobacz [rozpocząć tworzenie publiczny moduł równoważenia obciążenia w Menedżerze zasobów przy użyciu programu PowerShell](load-balancer-get-started-internet-arm-ps.md).

## <a name="add-healthy-instances-back-into-the-load-balancer-rotation"></a>Dodaj dobrej kondycji wystąpień do obrotu usługi równoważenia obciążenia

TCP i HTTP są traktowane jako dobrej kondycji i Oznacz wystąpienia roli co w przypadku dobrej kondycji:

* Moduł równoważenia obciążenia pobiera sondę dodatnią rozruchu maszyny Wirtualnej po raz pierwszy.
* Numer SuccessFailCount (opisanym wcześniej) definiuje wartość pomyślne sond, które są wymagane w celu oznaczenia wystąpienia roli w dobrej kondycji. Jeśli usunięto wystąpienia roli, liczbę sond pomyślne, kolejne musi równa lub przekracza wartość SuccessFailCount, aby oznaczyć wystąpienia roli co działa.

> [!NOTE]
> Jeśli zmienia się stan wystąpienia roli, usługi równoważenia obciążenia już przed oczekuje umieszcza wystąpienia roli w dobrej kondycji. Czas oczekiwania dodatkowe chroni użytkownika i infrastruktura i jest zamierzone zasad.

## <a name="use-log-analytics-for-a-load-balancer"></a>Analiza dzienników użycia usługi równoważenia obciążenia

Można użyć [dziennika analizy](load-balancer-monitor-log.md) można sprawdzić na stan kondycji sondę modułu równoważenia obciążenia i liczba sondowania. Rejestrowanie może służyć z usługi Power BI lub usługi Azure Operational Insights do zapewnienia statystyki dotyczące stanu kondycji modułu równoważenia obciążenia.
