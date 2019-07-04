---
title: Najlepsze rozwiązania dotyczące usługi Azure Cache dla usługi Redis
description: Dowiedz się, jak na potrzeby pamięci podręcznej Azure redis Cache skutecznie przy zastosowaniu tych najlepszych rozwiązań.
services: cache
documentationcenter: na
author: joncole
manager: jhubbard
editor: tysonn
ms.assetid: 3e4905e3-89e3-47f7-8cfb-12caf1c6e50e
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 06/21/2019
ms.author: joncole
ms.openlocfilehash: bdc75033e0aa2e401a511789728feef3248d46ad
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67452464"
---
# <a name="best-practices-for-azure-cache-for-redis"></a>Najlepsze rozwiązania dotyczące usługi Azure Cache dla usługi Redis 
Korzystając z tych najlepszych rozwiązań, może pomóc zmaksymalizować wydajność i oszczędność kosztów podczas korzystania z usługi Azure cache dla wystąpienia usługi Redis.

## <a name="configuration-and-concepts"></a>Konfiguracja i pojęcia
 * **Dla systemów produkcyjnych przy użyciu warstwy standardowa lub Premium.**  Warstwa podstawowa to system jeden węzeł nie replikacji danych i objęta umową SLA. Ponadto można użyć co najmniej C1 pamięci podręcznej.  Pamięci podręcznych C0 są przeznaczone dla scenariuszy deweloperskich lub testowych proste, ponieważ ma współdzielony rdzeń procesora CPU, mała ilość pamięci i są podatne na "hałaśliwym sąsiadem".

 * **Należy pamiętać, że Redis jest magazyn danych w pamięci.**  [W tym artykule](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) opisano sytuacje, w których może wystąpić utrata danych.

 * **Tworzenie systemu w taki sposób, że może obsługiwać połączenia blips** [ze względu na instalowaniu poprawek i trybu failover](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md).

 * **Konfigurowanie usługi [zastrzeżone maxmemory ustawienie](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) można zwiększyć szybkość reakcji systemu** warunkach dużego wykorzystania pamięci.  To ustawienie jest szczególnie ważne w przypadku zapisu duże obciążenia, lub jeśli większe wartości (100 KB lub więcej) są przechowywane w pamięci podręcznej Redis.  Czy mogę będzie zaleca się, rozpoczynając od 10% rozmiaru pamięci podręcznej, a następnie zwiększyć w przypadku obciążeń procesów. Zobacz [pewne zagadnienia](cache-how-to-troubleshoot.md#considerations-for-memory-reservations) podczas wybierania wartości.

 * **Redis Cache działa najlepiej z mniejszej wartości**, dlatego należy rozważyć siekanie większe danych na wiele kluczy.  W [tej dyskusji Redis](https://stackoverflow.com/questions/55517224/what-is-the-ideal-value-size-range-for-redis-is-100kb-too-large/), pewne zagadnienia są wyświetlane, należy rozważyć.  Odczyt [w tym artykule](cache-how-to-troubleshoot.md#large-requestresponse-size) dotyczącej problemu przykład, która może być spowodowany przez dużych wartościach.

 * **Znajdź wystąpienie pamięci podręcznej i aplikacji, w tym samym regionie.**  Łączenie z pamięcią podręczną w innym regionie może znacznie zwiększyć opóźnienie i ograniczyć niezawodności.  Chociaż można połączyć się z spoza platformy Azure, nie zalecane *zwłaszcza w przypadku korzystania z usługi Redis jako pamięci podręcznej*.  Jeśli używasz usługi Redis jako tylko magazyn klucz/wartość, opóźnienie może nie być podstawową kwestią. 

 * **Ponowne użycie połączenia** -tworzenia nowych połączeń jest kosztowne i zwiększa opóźnienia, więc ponowne używanie połączeń, jak to możliwe. Jeśli zdecydujesz się utworzyć nowe połączenia, upewnij się zamknąć starych połączeń, przed zwolnieniem (nawet w przypadku języków pamięci zarządzanej, takich jak .NET lub Java).

 * **Konfigurowanie biblioteki klienta do użycia *limit czasu połączenia* co najmniej 15 sekund**, co daje systemowi czas na łączenie nawet warunkach procesora CPU wyższa.  Mała wartość limitu czasu nie gwarantuje, że w tym czasie zostanie nawiązane połączenie.  Jeśli coś niewłaściwego zbliża się (dużej liczbie klientów procesora CPU, procesor CPU serwera o wysokiej i tak dalej), a następnie wartość limitu czasu połączenia krótki spowoduje, że próba połączenia nie powiodła się. To zachowanie często sprawia, że gorsza zły sytuacji.  Zamiast pomagając krótszy przekroczeń limitu czasu pogłębić problemu przez wymuszenie ponownego uruchomienia procesu próby nawiązania ponownego połączenia, systemu, co może prowadzić do *connect -> niepowodzenie -> spróbuj ponownie* pętli. Ogólnie zaleca się pozostawienie połączenia limitu czasu na 15 sekund lub nowszej. Lepiej umożliwić próba połączenia powiedzie się po 15 lub 20 sekund nie jest ona szybko się nie powieść tylko po to, aby spróbować ponownie. Pętla ponawiania prób może spowodować swoje awarii do ostatniej dłużej niż Jeśli po prostu Pozwól systemowi wykonać już wstępnie.  
     > [!NOTE]
     > Niniejsze wskazówki są specyficzne dla *próba połączenia* i nie jest powiązany z czasu chce czekać na *operacji* takich jak GET lub SET, aby zakończyć.
 

 * **Unikaj kosztownych poleceń** -redis niektóre operacje, takie jak [polecenia KLUCZE](https://redis.io/commands/keys), są *bardzo* kosztowne i należy ich unikać.  Aby uzyskać więcej informacji, zobacz [niektóre zagadnienia dotyczące, wokół kosztownych poleceń](cache-how-to-troubleshoot.md#expensive-commands)


 
## <a name="memory-management"></a>Zarządzanie pamięcią
Istnieje kilka kwestii związanych z użycia pamięci w ramach Twojego wystąpienia serwera Redis, które warto wziąć pod uwagę.  Poniżej przedstawiono kilka:

 * **Wybierz [zasady eksmisji](https://redis.io/topics/lru-cache) działający dla swojej aplikacji.**  Domyślne zasady usługi Azure redis Cache jest *volatile lru*, co oznacza, że tylko klucze, które mają czasu wygaśnięcia wartość zestawu będą kwalifikować się do wykluczenia.  Jeśli nie klucze mają wartości TTL, system nie będzie Wyklucz żadnych kluczy.  Jeśli chcesz, aby system, aby zezwolić na dowolny klawisz, aby zostać wykluczony, jeżeli duże wykorzystanie pamięci, a następnie chcesz wziąć pod uwagę *allkeys lru* zasad.

 * **Ustaw wartość dla wygaśnięcia na klucze.**  Spowoduje to usunięcie kluczy z wyprzedzeniem, zamiast czekać, aż, do wykorzystania pamięci.  Podczas wykluczania zaczną działać z powodu dużego wykorzystania pamięci, może to spowodować dodatkowego obciążenia na serwerze.  Aby uzyskać więcej informacji, zobacz dokumentację dla [Wygaś](https://redis.io/commands/expire) i [ExpireAt](https://redis.io/commands/expireat) poleceń.
 
## <a name="client-library-specific-guidance"></a>Dokładne wskazówki biblioteki klienta
 * [StackExchange.Redis (.NET)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-stackexchange-redis-md)
 * [Java — które klient powinien używać?](https://gist.github.com/warrenzhu25/1beb02a09b6afd41dff2c27c53918ce7#file-azure-redis-java-best-practices-md)
 * [Sałaty (Java)](https://gist.github.com/warrenzhu25/181ccac7fa70411f7eb72aff23aa8a6a#file-azure-redis-lettuce-best-practices-md)
 * [Jedis (Java)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-java-jedis-md)
 * [Node.js](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-node-js-md)
 * [PHP](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-php-md)
 * [Dostawca stanu sesji Asp.Net](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-session-state-provider-md)


## <a name="when-is-it-safe-to-retry"></a>Gdy jest bezpieczne spróbować ponownie?
Niestety jest łatwa odpowiedzi.  Każda aplikacja musi zdecydować, jakie operacje mogą być ponawiane i które nie mogą.  Każda operacja ma inne wymagania i zależności między klucza.  Oto kilka rzeczy, które może wziąć pod uwagę:

 * Mimo, że magazyn Redis działa prawidłowo wymaganą do uruchomienia polecenia, można uzyskać błędów po stronie klienta.  Na przykład:
     - Limity czasu to pojęcie po stronie klienta.  Jeśli operacja dotarła na serwer, serwerze uruchom polecenie, nawet wtedy, gdy klient zrezygnuje oczekiwania.  
     - Po wystąpieniu błędu połączenia gniazda, nie jest możliwe, należy wiedzieć, jeśli operacja faktycznie uruchomiła się na serwerze.  Na przykład błąd połączenia może się zdarzyć, po przetworzeniu żądania przez serwer, ale przed odpowiedź jest odbierana przez klienta.
 *  Jak Moja aplikacja react Jeśli przypadkowo dwa razy tę samą operację?  Na przykład co zrobić, jeśli I zwiększyć liczbę całkowitą dwukrotnie zamiast tylko raz?  Moja aplikacja zapisuje do tego samego klucza w wielu miejscach?  Co zrobić, jeśli Moja logikę ponawiania próby zastępuje wartość przy jakaś część mojej aplikacji?

Jeśli chcesz przetestować sposób działania swojego kodu w warunkach błędu, należy wziąć pod uwagę przy użyciu [ponowne uruchomienie funkcji](cache-administration.md#reboot). Dzięki temu można zobaczyć, jak połączenie blips wpływać na aplikację.

## <a name="performance-testing"></a>Testowanie wydajności
 * **Uruchom przy użyciu `redis-benchmark.exe`**  aby można było uzyskać pewne pojęcie dla możliwe przepływności/opóźnienie przed zapisem wydajności własne testy.  Dokumentacja testu porównawczego usługi redis może być [tutaj](http://redis.io/topics/benchmarks).  Należy pamiętać, tej pamięci podręcznej redis testów porównawczych nie obsługuje protokołu SSL, dlatego musisz [włączyć port bez protokołu SSL za pośrednictwem portalu](cache-configure.md#access-ports) przed uruchomieniem testu.  [Windows zgodnej wersji redis benchmark.exe można znaleźć tutaj](https://github.com/MSOpenTech/redis/releases)
 * Powinny być używane do testowania maszyny Wirtualnej klienta **w tym samym regionie** jako wystąpienia pamięci podręcznej Redis.
 * **Firma Microsoft zaleca używanie serię maszyn wirtualnych Dv2** klienta, zgodnie z ich sprzętu lepiej i zapewnią najlepsze rezultaty.
 * Upewnić się, że klient ma maszyny Wirtualnej, możesz użyć **co najmniej taką ilość obliczeń i przepustowość* jako pamięć podręczna poddawana testom. 
 * **Włącz VRSS** na komputerze klienckim, jeśli użytkownik pracuje na Windows.  [Zobacz tutaj, aby uzyskać szczegółowe informacje](https://technet.microsoft.com/library/dn383582(v=ws.11).aspx).  Przykładowy skrypt programu powershell:
     >Program PowerShell - ExecutionPolicy Unrestricted Enable-NetAdapterRSS-Name (Get-NetAdapter). Nazwa 
     
 * **Należy rozważyć użycie wystąpienia usługi Redis warstwy Premium**.  Te rozmiary pamięci podręcznej będzie miało lepsze opóźnienia sieci i przepustowość, ponieważ są one uruchamiane na sprzęcie lepsze dla procesora CPU i sieci.
 
     > [!NOTE]
     > Nasze wyniki zaobserwowanego są [opublikowane w tym miejscu](cache-faq.md#azure-cache-for-redis-performance) użytkownikowi.   Ponadto należy pamiętać, że SSL/TLS dodaje pewien narzut, dzięki czemu mogą uzyskać różne wartości opóźnienia i/lub przepływność Jeśli używasz szyfrowanie transportu.
 
### <a name="redis-benchmark-examples"></a>Przykłady testu porównawczego usługi redis
**Testowanie wstępne instalacji**: Przygotuje wystąpienie pamięci podręcznej przy użyciu danych wymaganych do opóźnienia i przepływności, testowanie poleceń wymienionych poniżej.
> redis benchmark.exe - h yourcache.redis.cache.windows.net - yourAccesskey -t ustaw n - 10 - d 1024 

**Aby przetestować opóźnienie**: To polecenie przetestuje żądań GET, przy użyciu 1 ładunku k.
> redis benchmark.exe - h yourcache.redis.cache.windows.net - yourAccesskey -t Pobierz 1024 – P 50 -c -d 4

**Aby przetestować przepływność:** Ta metoda korzysta żądania GET potokowe przy użyciu 1 k ładunku.
> redis benchmark.exe - h yourcache.redis.cache.windows.net - yourAccesskey -t pobieranie -n 1000000 - d 1024 - P 50 - c 50
