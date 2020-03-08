---
title: Rozwiązywanie problemów z łącznością NAT na platformie Azure Virtual Network
titleSuffix: Azure Virtual Network NAT troubleshooting
description: Rozwiązywanie problemów z Virtual Network translatora adresów sieciowych.
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
ms.date: 03/05/2020
ms.author: allensu
ms.openlocfilehash: c629b3425cd095a6ac9d305b5cd6de58ed9d572a
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78674330"
---
# <a name="troubleshoot-azure-virtual-network-nat-connectivity-problems"></a>Rozwiązywanie problemów z łącznością NAT na platformie Azure Virtual Network

Ten artykuł ułatwia administratorom diagnozowanie i rozwiązywanie problemów z łącznością podczas korzystania z Virtual Network translatora adresów sieciowych.

>[!NOTE] 
>Virtual Network translator adresów sieciowych jest w tej chwili dostępny jako publiczna wersja zapoznawcza. Jest ono obecnie dostępne tylko w ograniczonym zestawie [regionów](nat-overview.md#region-availability). Ta wersja zapoznawcza jest świadczona bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms).

## <a name="problems"></a>Problemy

* [Wyczerpanie adresów współlotowych](#snat-exhaustion)
* [Polecenie ping protokołu ICMP kończy się niepowodzeniem](#icmp-ping-is-failing)
* [Błędy łączności](#connectivity-failures)
* [Współistnienie IPv6](#ipv6-coexistence)

Aby rozwiązać te problemy, wykonaj kroki opisane w następnej sekcji.

## <a name="resolution"></a>Rozwiązanie

### <a name="snat-exhaustion"></a>Wyczerpanie adresów współlotowych

Pojedynczy [zasób bramy translatora adresów sieciowych](nat-gateway-resource.md) obsługuje od 64 000 do 1 000 000 współbieżnych przepływów.  Każdy adres IP zapewnia 64 000 portów do dostępnego spisu. Możesz użyć maksymalnie 16 adresów IP na zasób bramy NAT.  Mechanizm podstawcy adresów sieciowych został opisany [tutaj](nat-gateway-resource.md#source-network-address-translation) bardziej szczegółowo.

Często głównym powodem wyczerpania adresów w ramach ruchu przychodzącego jest model zapewniający sposób ustanawiania i zarządzania łącznością wychodzącą.  Uważnie przejrzyj tę sekcję.

#### <a name="steps"></a>Kroki

1. Zbadaj, w jaki sposób aplikacja tworzy łączność wychodzącą (na przykład przegląd kodu lub przechwycenie pakietu). 
2. Ustal, czy to działanie jest oczekiwane, czy też czy aplikacja jest błędna.  Skorzystaj z [metryk](nat-metrics.md) w Azure monitor, aby uzasadnić swoje ustalenia. Użyj kategorii "Niepowodzenie" dla metryki połączeń z podłączaniem adresów sieciowych.
3. Oceń, czy są obserwowane odpowiednie wzorce.
4. Oceń, czy wyczerpanie portów podadresów IP powinno być skorygowane przy użyciu dodatkowych adresów, przypisanych do zasobu bramy translatora adresów sieciowych.

#### <a name="design-patterns"></a>Wzorce projektowe

Zawsze korzystaj z zalet użycia połączenia i puli połączeń, jeśli to możliwe.  Wzorce te pozwolą uniknąć problemów z wyczerpaniem zasobów i powodować przewidywalne, niezawodne i skalowalne zachowanie. Elementy pierwotne dla tych wzorców można znaleźć w wielu bibliotekach i strukturach programistycznych.

_**Rozwiązanie:**_ Użyj odpowiednich wzorców

- Należy rozważyć [asynchroniczne wzorce sondowania](https://docs.microsoft.com/azure/architecture/patterns/async-request-reply) dla długotrwałych operacji w celu zwolnienia zasobów połączenia dla innych operacji.
- Przepływy długotrwałe (na przykład ponowne użycie połączeń TCP) powinny korzystać z utrzymywania aktywności protokołu TCP lub warstwy aplikacji, aby uniknąć przekroczenia limitu czasu w systemach pośrednich.
- [Wzorce ponowień](https://docs.microsoft.com/azure/architecture/patterns/retry) należy stosować, aby uniknąć agresywnych ponownych prób/serii podczas przejściowej awarii lub odzyskiwania po awarii.
Tworzenie nowego połączenia TCP dla każdej operacji HTTP (nazywanej również "połączeniami niepodzielnymi") jest antywzorcem.  Połączenia niepodzielne uniemożliwią aplikacji również skalowanie zasobów.  Zawsze potoku wiele operacji w ramach tego samego połączenia.  Twoja aplikacja będzie korzystać z szybkości transakcji i kosztów zasobów.  Gdy aplikacja korzysta z szyfrowania warstwy transportowej (na przykład TLS), istnieje znaczący koszt związany z przetwarzaniem nowych połączeń.  Zapoznaj się ze [wzorcami projektowymi chmury platformy Azure](https://docs.microsoft.com/azure/architecture/patterns/) , aby uzyskać dodatkowe wzorce najlepszych rozwiązań.

#### <a name="possible-mitigations"></a>Możliwe środki zaradcze

_**Rozwiązanie:**_ Skaluj łączność wychodzącą w następujący sposób:

| Scenariusz | Świadectwa |Środki zaradcze |
|---|---|---|
| Wystąpiła rywalizacja o porty i wyczerpanie portów przydziałów adresów sieciowych w okresach o wysokim poziomie użycia. | Kategoria "Niepowodzenie" dla [metryki](nat-metrics.md) połączeń protokołu wiązania w Azure monitor pokazuje przejściowe lub trwałe błędy w porównaniu z ilością czasu i dużym połączeniem.  | Ustal, czy można dodać dodatkowe zasoby publicznego adresu IP lub zasobów prefiksu publicznego adresu IP. Spowoduje to dodanie maksymalnie 16 adresów IP do bramy translatora adresów sieciowych. To dodanie spowoduje udostępnienie większej ilości zasobów dla dostępnych portów (64 000 na adres IP) i pozwala na dalsze skalowanie scenariusza.|
| Podano już 16 adresów IP, a nadal wystąpiły wyczerpanie portów. | Próba dodania dodatkowego adresu IP kończy się niepowodzeniem. Łączna liczba adresów IP z zasobów publicznych adresów IP lub zasobów prefiksu publicznego adresu IP przekracza całkowitą liczbę 16. | Dystrybuuj środowisko aplikacji w wielu podsieciach i podaj zasób bramy NAT dla każdej podsieci.  Ponownie Oceń wzorce projektu, aby zoptymalizować je w oparciu o wcześniejsze [wskazówki](#design-patterns). |

>[!NOTE]
>Ważne jest, aby zrozumieć, dlaczego następuje wyczerpanie adresów. Upewnij się, że używasz właściwych wzorców do skalowalnych i niezawodnych scenariuszy.  Dodanie więcej portów do scenariusza bez znajomości przyczyny powinno być ostatnim etapem. Jeśli nie rozumiesz, dlaczego scenariusz stosuje nacisk na spis portów IP, dodanie większej liczby portów do spisu przez dodanie większej liczby adresów IP spowoduje opóźnienie tego samego błędu wyczerpania, co w przypadku skalowania aplikacji.  Mogą być maskowane inne nieefektywność i antywzorce.

### <a name="icmp-ping-is-failing"></a>Polecenie ping protokołu ICMP kończy się niepowodzeniem

[Virtual Network translator adresów sieciowych](nat-overview.md) obsługuje protokoły UDP IPv4 i TCP. Protokół ICMP nie jest obsługiwany i oczekiwany jest błąd.  

_**Rozwiązanie:**_ Zamiast tego należy użyć testów połączeń TCP (na przykład "TCP ping") i testów warstwy aplikacji specyficznych dla protokołu UDP w celu zweryfikowania kompleksowej łączności.

W poniższej tabeli można użyć punktu wyjścia, dla którego narzędzia do uruchomienia testów.

| System operacyjny | Ogólny test połączenia TCP | Test warstwy aplikacji TCP | UDP |
|---|---|---|---|
| Linux | NC (ogólny test połączenia) | zwinięcie (test warstwy aplikacji TCP) | specyficzne dla aplikacji |
| System Windows | [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) | Wywołanie programu PowerShell [— żądanie WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) | specyficzne dla aplikacji |

### <a name="connectivity-failures"></a>Błędy łączności

Problemy z łącznością z [translatorem adresów sieciowych Virtual Network](nat-overview.md) mogą być spowodowane kilkoma różnymi problemami:

* przejściowe lub trwałe [wyczerpanie](#snat-exhaustion) elementu przekroczenia bramy TRANSLATORA adresów sieciowych,
* Błędy przejściowe w infrastrukturze platformy Azure, 
* przejściowe błędy w ścieżce między platformą Azure a publicznym miejscem docelowym Internetu, 
* Błędy przejściowe lub trwałe w publicznym miejscu docelowym Internetu.

Użyj narzędzi, takich jak następujące, aby sprawdzić poprawność łączności. [Polecenie ping protokołu ICMP nie jest obsługiwane](#icmp-ping-is-failing).

| System operacyjny | Ogólny test połączenia TCP | Test warstwy aplikacji TCP | UDP |
|---|---|---|---|
| Linux | NC (ogólny test połączenia) | zwinięcie (test warstwy aplikacji TCP) | specyficzne dla aplikacji |
| System Windows | [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) | Wywołanie programu PowerShell [— żądanie WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) | specyficzne dla aplikacji |

#### <a name="snat-exhaustion"></a>Wyczerpanie adresów współlotowych

Przejrzyj sekcję dotyczącą [wyczerpania adresów](#snat-exhaustion) w tym artykule.

#### <a name="azure-infrastructure"></a>Infrastruktura platformy Azure

Mimo że platforma Azure monitoruje i obsługuje jej infrastrukturę z dużą ostrożnością, przejściowe awarie mogą wystąpić, ponieważ nie ma żadnej gwarancji, że transmisje są bezstratne.  Użyj wzorców projektowych, które pozwalają na retransmisję SYN dla aplikacji TCP. Limity czasu połączenia są wystarczająco duże, aby zezwolić na ponowną transmisję protokołu TCP SYN w celu ograniczenia przejściowych wpływów spowodowanych przez utracony pakiet SYN.

_**Narzędzie**_

* Sprawdź, czy jest [wyczerpany wyczerpanie adresów](#snat-exhaustion).
* Parametr konfiguracji w stosie protokołu TCP kontrolujący zachowanie retransmisji SYN ma nazwę RTO ([limit czasu retransmisji](https://tools.ietf.org/html/rfc793)). Wartość RTO jest ustawiana, ale zazwyczaj jest domyślnie 1 sekunda lub wyższa, z możliwością wycofywania wykładniczego.  Jeśli limit czasu połączenia aplikacji jest za krótki (na przykład 1 s), można zobaczyć sporadyczne limity czasu połączenia.  Zwiększ limit czasu połączenia aplikacji.
* Jeśli obserwujesz dłużej, nieoczekiwane limity czasu przy użyciu domyślnych zachowań aplikacji, Otwórz przypadek pomocy technicznej w celu dalszego rozwiązywania problemów.

Nie zalecamy sztucznego zmniejszania limitu czasu połączenia TCP ani dostrajania parametru RTO.

#### <a name="public-internet-transit"></a>publiczne tranzyt internetowy

Prawdopodobieństwo błędów przejściowych wzrasta z dłuższą ścieżką do miejsca docelowego i większej liczby systemów pośrednich. Oczekuje się, że przejściowe błędy mogą zwiększyć częstotliwość w przypadku [infrastruktury platformy Azure](#azure-infrastructure). 

Postępuj zgodnie z tymi samymi wskazówkami jak w poprzedniej sekcji [infrastruktury platformy Azure](#azure-infrastructure) .

#### <a name="internet-endpoint"></a>Internetowy punkt końcowy

Powyższe sekcje zawierają uzupełnienie zagadnień związanych z internetowym punktem końcowym, z którym nawiązuje się komunikacja. Inne czynniki, które mogą mieć wpływ na łączność, są następujące:

* zarządzanie ruchem na stronie docelowej, w tym
- Ograniczanie szybkości interfejsu API nakładane po stronie docelowej
- Pomiarowe środki zaradcze DDoS lub transkształtowanie ruchu warstwy transportowej
* Zapora lub inne składniki w miejscu docelowym 

Zwykle przechwycenia pakietu w źródle oraz miejsce docelowe (jeśli są dostępne) są wymagane do określenia, co ma miejsce.

_**Narzędzie**_

* Sprawdź, czy jest [wyczerpany wyczerpanie adresów](#snat-exhaustion). 
* Sprawdź poprawność łączności z punktem końcowym w tym samym regionie lub w innym miejscu do porównania.  
* Jeśli tworzysz testy dotyczące dużej ilości pamięci lub transakcji, zbadaj, czy zmniejszenie szybkości zmniejsza liczbę błędów.
* Jeśli zmiana szybkości ma wpływ na liczbę błędów, sprawdź, czy osiągnięto limity szybkości interfejsu API lub inne ograniczenia na stronie docelowej.
* Jeśli badanie jest niejednoznaczne, Otwórz zgłoszenie do pomocy technicznej, aby uzyskać dalsze Rozwiązywanie problemów.

#### <a name="tcp-resets-received"></a>Odebrane resety TCP

Jeśli zaobserwujesz Resetowanie TCP (pakiety TCP RST) otrzymane na źródłowej maszynie wirtualnej, mogą one być generowane przez bramę translatora adresów sieciowych po stronie prywatnej dla przepływów, które nie są rozpoznawane jako w toku.  Jedną z możliwych przyczyn jest to, że połączenie TCP ma limit czasu bezczynności.  Limit czasu bezczynności można dostosować z 4 minut do 120 minut.

Resetowanie protokołu TCP nie jest generowane po stronie publicznej zasobów bramy translatora adresów sieciowych. Jeśli po stronie docelowej są odbierane resety TCP, są one generowane przez stos źródłowej maszyny wirtualnej, a nie zasób bramy translatora adresów sieciowych.

_**Narzędzie**_

* Przejrzyj zalecenia dotyczące [wzorców projektowych](#design-patterns) .  
* Aby uzyskać dalsze Rozwiązywanie problemów, w razie potrzeby należy otworzyć zgłoszenie do pomocy technicznej.

### <a name="ipv6-coexistence"></a>Współistnienie IPv6

[Virtual Network translator adresów sieciowych](nat-overview.md) obsługuje protokoły UDP IPv4 i TCP oraz wdrażanie w [podsieci z prefiksem IPv6 nie jest obsługiwane](nat-overview.md#limitations).

_**Rozwiązanie:**_ Wdróż bramę translatora adresów sieciowych w podsieci bez prefiksu IPv6.

Możesz wskazać zainteresowanie dodatkowymi możliwościami za pośrednictwem [Virtual Network translatora adresów sieciowych](https://aka.ms/natuservoice)w usłudze UserVoice.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [Virtual Network translatora adresów sieciowych](nat-overview.md)
* Informacje o [zasobie bramy translatora adresów sieciowych](nat-gateway-resource.md)
* Informacje o [metrykach i alertach dotyczących zasobów bramy translatora adresów sieciowych](nat-metrics.md).
* [Powiedz nam, co należy utworzyć obok Virtual Network translatora adresów sieciowych w usłudze UserVoice](https://aka.ms/natuservoice).

