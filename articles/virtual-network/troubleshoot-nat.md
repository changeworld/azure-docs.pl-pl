---
title: Rozwiązywanie problemów z łącznością TRANSLATOR SIECI WIRTUALNEJ platformy Azure
titleSuffix: Azure Virtual Network
description: Rozwiązywanie problemów z translatorem adresów sieci wirtualnych.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
Customer intent: As an IT administrator, I want to troubleshoot Virtual Network NAT.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2020
ms.author: allensu
ms.openlocfilehash: 4a273801290a0a5833ebd83983a8b6b0ad856b45
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79408488"
---
# <a name="troubleshoot-azure-virtual-network-nat-connectivity"></a>Rozwiązywanie problemów z łącznością TRANSLATOR SIECI WIRTUALNEJ platformy Azure

Ten artykuł pomaga administratorom diagnozować i rozwiązywać problemy z łącznością podczas korzystania z translatora adresów sieci wirtualnych.

## <a name="problems"></a>Problemy

* [Wyczerpanie SNAT](#snat-exhaustion)
* [Ping ICMP nie działa](#icmp-ping-is-failing)
* [Awarie łączności](#connectivity-failures)
* [Współistnienie IPv6](#ipv6-coexistence)

Aby rozwiązać te problemy, wykonaj kroki opisane w poniższej sekcji.

## <a name="resolution"></a>Rozwiązanie

### <a name="snat-exhaustion"></a>Wyczerpanie SNAT

Pojedynczy [zasób bramy TRANSLATOR](nat-gateway-resource.md) obsługuje od 64 000 do 1 miliona równoczesnych przepływów.  Każdy adres IP udostępnia 64 000 portów SNAT do dostępnego magazynu. Na zasób bramy TRANSLATORA można użyć maksymalnie 16 adresów IP.  Mechanizm SNAT jest opisany [tutaj](nat-gateway-resource.md#source-network-address-translation) bardziej szczegółowo.

Często główną przyczyną wyczerpania SNAT jest anty-wzorzec dla jak połączenia wychodzącego jest ustanawiana i zarządzane.  Uważnie przejrzyj tę sekcję.

#### <a name="steps"></a>Kroki

1. Sprawdź, jak aplikacja tworzy łączność wychodzącą (na przykład przegląd kodu lub przechwytywanie pakietów). 
2. Określ, czy to działanie jest oczekiwane zachowanie lub czy aplikacja jest niewłaściwie.  Użyj [metryki](nat-metrics.md) w usłudze Azure Monitor, aby uzasadnić swoje ustalenia. Użyj kategorii "Niepowodzenie" dla metryki Połączenia SNAT.
3. Oceń, czy są przestrzegane odpowiednie wzorce.
4. Oceń, czy należy ograniczyć wyczerpanie portów SNAT za pomocą dodatkowych adresów IP przypisanych do zasobu bramy TRANSLATORA.

#### <a name="design-patterns"></a>Wzorce projektowe

Zawsze korzystaj z ponownego użycia połączenia i buforowania połączeń, gdy tylko jest to możliwe.  Te wzorce pozwoli uniknąć problemów wyczerpania zasobów i spowodować przewidywalne zachowanie. Egoisje dla tych wzorców można znaleźć w wielu bibliotekach programistycznych i strukturach.

_**Rozwiązanie:**_ Używanie odpowiednich wzorców i najlepszych praktyk

- Żądania niepodzielne (jedno żądanie na połączenie) są złym wyborem projektu. Takie anty-wzorek ogranicza skalę, zmniejsza wydajność i zmniejsza niezawodność. Zamiast tego ponownie użyć połączeń HTTP/S, aby zmniejszyć liczbę połączeń i skojarzonych portów SNAT. Skala aplikacji wzrośnie, a wydajność wzrośnie dzięki zmniejszeniu uzgadniania, obciążeniem i kosztom operacji kryptograficznej podczas korzystania z protokołu TLS.
- System DNS może wprowadzać wiele pojedynczych przepływów na woluminie, gdy klient nie buforuje wyniku rozpoznawania nazw DNS. Użyj buforowania.
- Przepływy UDP (na przykład wyszukiwania DNS) przydzielają porty SNAT na czas trwania limitu czasu bezczynności. Im dłuższy limit czasu bezczynnego, tym większe ciśnienie w portach SNAT. Użyj krótkiego limitu czasu bezczynnego (na przykład 4 minuty).
- Użyj pul połączeń, aby kształtować wolumin połączenia.
- Nigdy nie po cichu porzucić przepływ TCP i polegać na czasomierze TCP do czyszczenia przepływu. Spowoduje to pozostawienie stanu przydzielonego w systemach pośrednich i punktach końcowych oraz spowoduje, że porty będą niedostępne dla innych połączeń. Może to wywołać błędy aplikacji i wyczerpanie SNAT. 
- Wartości czasomierza zamknięcia TCP nie powinny być zmieniane bez wiedzy ekspertów na temat wpływu. Podczas TCP odzyska, wydajność aplikacji może mieć negatywny wpływ, gdy punkty końcowe połączenia mają niedopasowane oczekiwania. Chęć zmiany czasomierzy jest zwykle oznaką podstawowego problemu projektowego. Zapoznaj się z poniższymi zaleceniami.

Często wyczerpanie SNAT może być również wzmacniane za pomocą innych wzorców anty-wzorów w podstawowej aplikacji. Przejrzyj te dodatkowe wzorce i najlepsze rozwiązania, aby poprawić skalę i niezawodność usługi.

- Należy wziąć pod uwagę [wzorce sondowania asynchronii dla](https://docs.microsoft.com/azure/architecture/patterns/async-request-reply) długotrwałych operacji, aby zwolnić zasoby połączenia dla innych operacji.
- Przepływy długotrwałe (na przykład ponownie używane połączenia TCP) powinny używać funkcji tcp keepalives lub keepalives warstwy aplikacji, aby uniknąć limitu czasu systemów pośrednich. Zwiększenie limitu czasu bezczynności jest ostatecznością i może nie rozwiązać główną przyczynę. Długi limit czasu może spowodować błędy niskiej szybkości po upływie limitu czasu i wprowadzić opóźnienie i niepotrzebne błędy.
- Wdzięku [ponawiania wzorców](https://docs.microsoft.com/azure/architecture/patterns/retry) powinny być używane w celu uniknięcia agresywnych ponownych prób/wybuchów podczas przejściowej awarii lub odzyskiwania awarii.
Tworzenie nowego połączenia TCP dla każdej operacji HTTP (znanej również jako "połączenia niepodzielne") jest wzorcem.  Połączenia atomowe uniemożliwią aplikacji skalowanie dobrze i odpadów zasobów.  Zawsze potok wiele operacji do tego samego połączenia.  Aplikacja będzie korzystać z szybkości transakcji i kosztów zasobów.  Gdy aplikacja używa szyfrowania warstwy transportu (na przykład TLS), istnieje znaczne koszty związane z przetwarzaniem nowych połączeń.  Przejrzyj [wzorce projektowania usługi Azure Cloud, aby](https://docs.microsoft.com/azure/architecture/patterns/) uzyskać dodatkowe wzorce najlepszych rozwiązań.

#### <a name="additional-possible-mitigations"></a>Dodatkowe możliwe środki zaradcze

_**Rozwiązanie:**_ Skaluj łączność wychodzącą w następujący sposób:

| Scenariusz | Dowody |Środki zaradcze |
|---|---|---|
| Występują rywalizacji dla portów SNAT i wyczerpania portów SNAT w okresach wysokiego użycia. | Kategoria "Nie powiodło się" dla [metryki](nat-metrics.md) Połączenia SNAT w usłudze Azure Monitor pokazuje przejściowe lub trwałe błędy w czasie i dużej głośności połączenia.  | Określ, czy można dodać dodatkowe zasoby publicznego adresu IP lub publiczne zasoby prefiksu IP. To dodanie pozwoli na maksymalnie 16 adresów IP w sumie do bramy NAT. Ten dodatek zapewni więcej zapasów dla dostępnych portów SNAT (64 000 na adres IP) i pozwoli na dalsze skalowanie scenariusza.|
| Masz już podane 16 adresów IP i nadal występują wyczerpanie portu SNAT. | Próba dodania dodatkowego adresu IP kończy się niepowodzeniem. Łączna liczba adresów IP z publicznych zasobów adresów IP lub publicznych zasobów prefiksu IP przekracza łącznie 16. | Rozsiewaj środowisko aplikacji w wielu podsieciach i udostępniaj zasób bramy TRANSLATORA dla każdej podsieci.  Ponownie ocenić wzór(y) projektu, aby zoptymalizować na podstawie powyższych [wskazówek](#design-patterns). |

>[!NOTE]
>Ważne jest, aby zrozumieć, dlaczego występuje wyczerpanie SNAT. Upewnij się, że używasz odpowiednich wzorców do skalowalnych i niezawodnych scenariuszy.  Dodawanie większej liczby portów SNAT do scenariusza bez zrozumienia przyczyny popytu powinno być ostatecznością. Jeśli nie rozumiesz, dlaczego scenariusz jest zastosowanie nacisku na zapasy portów SNAT, dodanie więcej portów SNAT do zapasów przez dodanie większej liczby adresów IP tylko opóźni ten sam błąd wyczerpania, jak skale aplikacji.  Możesz maskować inne nieefektywności i anty-wzory.

### <a name="icmp-ping-is-failing"></a>Ping ICMP nie działa

[Translator adresów sieci wirtualnej](nat-overview.md) obsługuje protokoły IPv4 UDP i TCP. ICMP nie jest obsługiwany i oczekuje się, że zakończy się niepowodzeniem.  

_**Rozwiązanie:**_ Zamiast tego należy użyć testów połączeń TCP (na przykład "PING TCP") i testów warstwy aplikacji specyficznych dla protokołu UDP, aby sprawdzić poprawność łączności typu end-to-end.

W poniższej tabeli można użyć punktu wyjścia, dla którego narzędzia do rozpoczęcia testów.

| System operacyjny | Ogólny test połączenia TCP | Test warstwy aplikacji TCP | UDP |
|---|---|---|---|
| Linux | nc (test połączenia ogólnego) | curl (test warstwy aplikacji TCP) | specyficzne dla aplikacji |
| Windows | [PsPing (PsPing)](https://docs.microsoft.com/sysinternals/downloads/psping) | [Odwołanie do wywołania w programie PowerShell-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) | specyficzne dla aplikacji |

### <a name="connectivity-failures"></a>Awarie łączności

Problemy z łącznością z [translatorem adresów sieci wirtualnych](nat-overview.md) mogą być spowodowane kilkoma różnymi problemami:

* przejściowe lub trwałe [wyczerpanie bramy](#snat-exhaustion) NAT,
* przejściowe awarie w infrastrukturze platformy Azure, 
* przejściowe błędy na ścieżce między platformą Azure a publicznym miejscem docelowym internetu, 
* przejściowych lub uporczywych awarii w publicznym miejscu docelowym Internetu.

Użyj narzędzi, takich jak następujące do łączności sprawdzania poprawności. [Ping ICMP nie jest obsługiwany](#icmp-ping-is-failing).

| System operacyjny | Ogólny test połączenia TCP | Test warstwy aplikacji TCP | UDP |
|---|---|---|---|
| Linux | nc (test połączenia ogólnego) | curl (test warstwy aplikacji TCP) | specyficzne dla aplikacji |
| Windows | [PsPing (PsPing)](https://docs.microsoft.com/sysinternals/downloads/psping) | [Odwołanie do wywołania w programie PowerShell-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) | specyficzne dla aplikacji |

#### <a name="snat-exhaustion"></a>Wyczerpanie SNAT

Przejrzyj sekcję na [wyczerpanie SNAT](#snat-exhaustion) w tym artykule.

#### <a name="azure-infrastructure"></a>Infrastruktura platformy Azure

Platforma Azure monitoruje i obsługuje swoją infrastrukturę z wielką starannością. Mogą wystąpić przejściowe awarie, nie ma gwarancji, że transmisje są bezstratne.  Użyj wzorców projektowych, które umożliwiają retransmisję SYN dla aplikacji TCP. Użyj limitów czasu połączenia wystarczająco dużych, aby umożliwić retransmisję TCP SYN w celu zmniejszenia przejściowych skutków spowodowanych utratą pakietu SYN.

_**Rozwiązanie:**_

* Sprawdź [wyczerpanie SNAT](#snat-exhaustion).
* Parametr konfiguracji w stosie TCP sterujący zachowaniem retransmisji SYN nosi nazwę RTO ([Limit czasu retransmisji](https://tools.ietf.org/html/rfc793)). Wartość RTO jest regulowana, ale zazwyczaj domyślnie 1 sekunda lub wyższa z wykładniczym wycofywania.  Jeśli limit czasu połączenia aplikacji jest zbyt krótki (na przykład 1 sekunda), mogą być widoczne sporadyczne limity czasu połączenia.  Zwiększ limit czasu połączenia aplikacji.
* Jeśli zauważysz dłuższe, nieoczekiwane limity czasu z domyślnymi zachowaniami aplikacji, otwórz przypadek pomocy technicznej w celu dalszego rozwiązywania problemów.

Nie zaleca się sztucznie zmniejszania limitu czasu połączenia TCP lub dostrajania parametru RTO.

#### <a name="public-internet-transit"></a>Publiczny tranzyt przez Internet

Prawdopodobieństwo awarii przejściowych zwiększa się wraz z dłuższą ścieżką do miejsca docelowego i bardziej pośrednimi systemami. Oczekuje się, że przejściowe awarie mogą zwiększyć częstotliwość w [infrastrukturze platformy Azure.](#azure-infrastructure) 

Postępuj zgodnie z tymi samymi wskazówkami, co poprzednia sekcja [infrastruktury platformy Azure.](#azure-infrastructure)

#### <a name="internet-endpoint"></a>Internetowy punkt końcowy

Poprzednie sekcje mają zastosowanie wraz z punktem końcowym Internetu, z którymi jest ustanawiana komunikacja. Inne czynniki, które mogą mieć wpływ na sukces łączności to:

* zarządzania ruchem po stronie docelowej, w tym
- Ograniczenie szybkości API nałożone przez stronę docelową
- Ograniczenia ddos wolumetryczne lub kształtowanie ruchu warstwy transportowej
* zapory sieciowej lub innych składników w miejscu docelowym 

Zazwyczaj przechwytywanie pakietów u źródła i miejsce docelowe (jeśli jest dostępne) są wymagane do określenia, co się dzieje.

_**Rozwiązanie:**_

* Sprawdź [wyczerpanie SNAT](#snat-exhaustion). 
* Sprawdź poprawność łączności z punktem końcowym w tym samym regionie lub w innym miejscu do porównania.  
* Jeśli tworzysz testowanie dużej ilości lub szybkości transakcji, zbadaj, czy zmniejszenie szybkości zmniejsza występowanie błędów.
* Jeśli zmiana szybkości wpływa na szybkość błędów, sprawdź, czy mogły zostać osiągnięte limity szybkości interfejsu API lub inne ograniczenia po stronie docelowej.
* Jeśli dochodzenie jest niejednoznaczne, otwórz sprawę pomocy technicznej w celu dalszego rozwiązywania problemów.

#### <a name="tcp-resets-received"></a>Odebrano resetowanie TCP

Brama NAT generuje resetowanie protokołu TCP na źródłowej maszynie wirtualnej dla ruchu, który nie jest rozpoznawany jako w toku.

Jednym z możliwych powodów jest to, że połączenie TCP ma limit czasu bezczynnego.  Limit czasu bezczynnego można regulować z 4 minut do maksymalnie 120 minut.

Resety TCP nie są generowane po stronie publicznej zasobów bramy NAT. Resetowanie protokołu TCP po stronie docelowej jest generowane przez źródłowe maszyny Wirtualnej, a nie przez zasób bramy NAT.

_**Rozwiązanie:**_

* Przejrzyj zalecenia dotyczące [wzorców projektowych.](#design-patterns)  
* Otwórz przypadek pomocy technicznej, aby w razie potrzeby rozwiązać dalsze problemy.

### <a name="ipv6-coexistence"></a>Współistnienie IPv6

[Translator adresów sieci wirtualnej](nat-overview.md) obsługuje protokoły Protokołu IPv4 UDP i TCP, a wdrażanie w [podsieci z prefiksem IPv6 nie jest obsługiwane.](nat-overview.md#limitations)

_**Rozwiązanie:**_ Wdrażanie bramy TRANSLATORa w podsieci bez prefiksu IPv6.

Można wskazać zainteresowanie dodatkowymi możliwościami za pośrednictwem [virtual network NAT UserVoice](https://aka.ms/natuservoice).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [nat sieci wirtualnej](nat-overview.md)
* Dowiedz się ab Smażyć [zasób bramy NAT](nat-gateway-resource.md)
* Dowiedz się więcej o [metrykach i alertach dotyczących zasobów bramy TRANSLATORA](nat-metrics.md).
* [Powiedz nam, co zbudować dalej dla Virtual Network NAT w UserVoice](https://aka.ms/natuservoice).

