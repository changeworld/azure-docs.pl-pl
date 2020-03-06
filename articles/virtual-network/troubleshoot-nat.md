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
ms.date: 03/04/2020
ms.author: allensu
ms.openlocfilehash: d56cd3b3d286d69a51d8cc14eb8020343cf7295a
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302988"
---
# <a name="troubleshoot-azure-virtual-network-nat-connectivity-problems"></a>Rozwiązywanie problemów z łącznością NAT na platformie Azure Virtual Network

Ten artykuł ułatwia administratorom diagnozowanie i rozwiązywanie problemów z łącznością podczas korzystania z Virtual Network translatora adresów sieciowych.

>[!NOTE] 
>Virtual Network translator adresów sieciowych jest w tej chwili dostępny jako publiczna wersja zapoznawcza. Jest ono obecnie dostępne tylko w ograniczonym zestawie [regionów](nat-overview.md#region-availability). Ta wersja zapoznawcza jest świadczona bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms).

## <a name="problems"></a>Problemy

- [Wyczerpanie](#snat-exhaustion).
- [Polecenie ping protokołu ICMP kończy się niepowodzeniem](#icmp-ping-is-failing).

Aby rozwiązać te problemy, wykonaj kroki opisane w następnej sekcji.

## <a name="resolution"></a>Rozwiązanie

### <a name="snat-exhaustion"></a>Wyczerpanie adresów współlotowych

Pojedynczy [zasób bramy translatora adresów sieciowych](nat-gateway-resource.md) obsługuje od 64 000 do 1 000 000 współbieżnych przepływów.  Każdy adres IP zapewnia 64 000 portów do dostępnego spisu. Możesz użyć maksymalnie 16 adresów IP na zasób bramy NAT.  Mechanizm podstawcy adresów sieciowych został opisany [tutaj](nat-gateway-resource.md#source-network-address-translation) bardziej szczegółowo.

#### <a name="steps"></a>Kroki:

1. Zbadaj, w jaki sposób aplikacja tworzy łączność wychodzącą (na przykład przegląd kodu lub przechwycenie pakietu). 
2. Ustal, czy to działanie jest oczekiwane, czy też czy aplikacja jest błędna.  Skorzystaj z [metryk](nat-metrics.md) w Azure monitor, aby uzasadnić swoje ustalenia.
3. Oceń, czy są obserwowane odpowiednie wzorce.
4. Oceń, czy wyczerpanie portów podadresów IP powinno być skorygowane przy użyciu dodatkowych adresów, przypisanych do zasobu bramy translatora adresów sieciowych.

#### <a name="design-pattern"></a>Wzorzec projektowy:

Zawsze korzystaj z zalet użycia połączenia i puli połączeń, jeśli to możliwe.  Ten wzorzec pozwala uniknąć problemów z wyczerpaniem zasobów i spowodować przewidywalne zachowanie. Elementy pierwotne dla tych wzorców można znaleźć w wielu bibliotekach i strukturach programistycznych.
- Należy rozważyć [asynchroniczne wzorce sondowania](https://docs.microsoft.com/azure/architecture/patterns/async-request-reply) dla długotrwałych operacji w celu zwolnienia zasobów połączenia dla innych operacji.
- Przepływy długotrwałe (na przykład ponowne użycie połączeń TCP) powinny korzystać z utrzymywania aktywności protokołu TCP lub warstwy aplikacji, aby uniknąć przekroczenia limitu czasu w systemach pośrednich.
- [Wzorce ponowień](https://docs.microsoft.com/azure/architecture/patterns/retry) należy stosować, aby uniknąć agresywnych ponownych prób/serii podczas przejściowej awarii lub odzyskiwania po awarii.
Tworzenie nowego połączenia TCP dla każdej operacji HTTP (nazywanej również "połączeniami niepodzielnymi") jest antywzorcem.  Połączenia niepodzielne uniemożliwią aplikacji również skalowanie zasobów.  Zawsze potoku wiele operacji w ramach tego samego połączenia.  Twoja aplikacja będzie korzystać z szybkości transakcji i kosztów zasobów.  Gdy aplikacja korzysta z szyfrowania warstwy transportowej (na przykład TLS), istnieje znaczący koszt związany z przetwarzaniem nowych połączeń.  Zapoznaj się ze [wzorcami projektowymi chmury platformy Azure](https://docs.microsoft.com/azure/architecture/patterns/) , aby uzyskać dodatkowe wzorce najlepszych rozwiązań.

#### <a name="mitigations"></a>Środki zaradcze

Łączność wychodzącą można skalować w następujący sposób:

| Scenariusz | Środki zaradcze |
|---|---|
| Wystąpiła rywalizacja o porty i wyczerpanie portów przydziałów adresów sieciowych w okresach o wysokim poziomie użycia. | Ustal, czy można dodać dodatkowe zasoby publicznego adresu IP lub zasobów prefiksu publicznego adresu IP. Spowoduje to dodanie maksymalnie 16 adresów IP do bramy translatora adresów sieciowych. To dodanie spowoduje udostępnienie większej ilości zasobów dla dostępnych portów (64 000 na adres IP) i pozwala na dalsze skalowanie scenariusza.|
| Podano już 16 adresów IP, a nadal wystąpiły wyczerpanie portów. | Dystrybuuj środowisko aplikacji w wielu podsieciach i podaj zasób bramy NAT dla każdej podsieci. |

>[!NOTE]
>Ważne jest, aby zrozumieć, dlaczego następuje wyczerpanie adresów. Upewnij się, że używasz właściwych wzorców do skalowalnych i niezawodnych scenariuszy.  Dodanie więcej portów do scenariusza bez znajomości przyczyny powinno być ostatnim etapem. Jeśli nie rozumiesz, dlaczego scenariusz stosuje nacisk na spis portów IP, dodanie większej liczby portów do spisu przez dodanie większej liczby adresów IP spowoduje opóźnienie tego samego błędu wyczerpania, co w przypadku skalowania aplikacji.  Mogą być maskowane inne nieefektywność i antywzorce.

### <a name="icmp-ping-is-failing"></a>Polecenie ping protokołu ICMP kończy się niepowodzeniem

[Virtual Network translator adresów sieciowych](nat-overview.md) obsługuje protokoły UDP IPv4 i TCP. Protokół ICMP nie jest obsługiwany i oczekiwany jest błąd.  Zamiast tego należy użyć testów połączeń TCP (na przykład "TCP ping") i testów warstwy aplikacji specyficznych dla protokołu UDP w celu zweryfikowania kompleksowej łączności.

W poniższej tabeli można użyć punktu wyjścia, dla którego narzędzia do uruchomienia testów.

| System operacyjny | Ogólny test połączenia TCP | Test warstwy aplikacji TCP | UDP |
|---|---|---|---|
| Linux | NC (ogólny test połączenia) | zwinięcie (test warstwy aplikacji TCP) | specyficzne dla aplikacji |
| System Windows | [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) | Wywołanie programu PowerShell [— żądanie WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) | specyficzne dla aplikacji |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [Virtual Network translatora adresów sieciowych](nat-overview.md)
- Informacje o [zasobie bramy translatora adresów sieciowych](nat-gateway-resource.md)
- Informacje o [metrykach i alertach dotyczących zasobów bramy translatora adresów sieciowych](nat-metrics.md).
