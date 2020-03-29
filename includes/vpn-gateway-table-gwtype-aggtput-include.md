---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/12/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1643b20c6c157c43e93967cef364e703dbf4478e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74828973"
---
|**Generowanie<br>bramy sieci VPN<br>** |**Numer jednostki magazynowej**   | **Połączenia typu lokacja-lokacja/Połączenia między sieciami wirtualnymi<br>Tunele** | **Połączenia typu punkt-lokacja<br> Połączenia SSTP** | **Połączenia P2S<br> IKEv2/OpenVPN** | **Test porównawczy<br>agregowanej przepływności** | **BGP** | **Strefa nadmiarowa** |
|---            |---         | ---        | ---       | ---            | ---       | ---       | ---|
|**Pokolenie 1**|**Podstawowa (Basic)**   | Maksymalnie z 10    | Maksymalnie z 128  | Nieobsługiwane  | 100 Mb/s  | Nieobsługiwane| Nie |
|**Pokolenie 1**|**VpnGw1**  | Maksymalnie z 30*   | Maksymalnie z 128  | Maksymalnie z 250       | 650 Mb/s  | Obsługiwane | Nie |
|**Pokolenie 1**|**VpnGw2**  | Maksymalnie z 30*   | Maksymalnie z 128  | Maksymalnie z 500       | 1 Gb/s    | Obsługiwane | Nie |
|**Pokolenie 1**|**VpnGw3**  | Maksymalnie z 30*   | Maksymalnie z 128  | Maksymalnie z 1000      | 1,25 Gb/s | Obsługiwane | Nie |
|**Pokolenie 1**|**VpnGw1AZ**| Maksymalnie z 30*   | Maksymalnie z 128  | Maksymalnie z 250       | 650 Mb/s  | Obsługiwane | Tak |
|**Pokolenie 1**|**VpnGw2AZ**| Maksymalnie z 30*   | Maksymalnie z 128  | Maksymalnie z 500       | 1 Gb/s    | Obsługiwane | Tak |
|**Pokolenie 1**|**VpnGw3AZ**| Maksymalnie z 30*   | Maksymalnie z 128  | Maksymalnie z 1000      | 1,25 Gb/s | Obsługiwane | Tak |
|        |            |            |           |                |           |           |     |
|**Pokolenie 2**|**VpnGw2**  | Maksymalnie z 30*   | Maksymalnie z 128  | Maksymalnie z 500       | 1,25 Gb/s | Obsługiwane | Nie |
|**Pokolenie 2**|**VpnGw3**  | Maksymalnie z 30*   | Maksymalnie z 128  | Maksymalnie z 1000      | 2,5 Gb/s  | Obsługiwane | Nie |
|**Pokolenie 2**|**VpnGw4**  | Maksymalnie z 30*   | Maksymalnie z 128  | Maksymalnie z 5000      | 5 Gb/s    | Obsługiwane | Nie |
|**Pokolenie 2**|**VpnGw5**  | Maksymalnie z 30*   | Maksymalnie z 128  | Maksymalnie z 10 000      | 10 Gb/s   | Obsługiwane | Nie |
|**Pokolenie 2**|**VpnGw2AZ**| Maksymalnie z 30*   | Maksymalnie z 128  | Maksymalnie z 500       | 1,25 Gb/s | Obsługiwane | Tak |
|**Pokolenie 2**|**VpnGw3AZ**| Maksymalnie z 30*   | Maksymalnie z 128  | Maksymalnie z 1000      | 2,5 Gb/s  | Obsługiwane | Tak |
|**Pokolenie 2**|**VpnGw4AZ**| Maksymalnie z 30*   | Maksymalnie z 128  | Maksymalnie z 5000      | 5 Gb/s    | Obsługiwane | Tak |
|**Pokolenie 2**|**VpnGw5AZ**| Maksymalnie z 30*   | Maksymalnie z 128  | Maksymalnie z 10 000      | 10 Gb/s   | Obsługiwane | Tak |

(*) Jeśli potrzebujesz więcej niż 30 tuneli sieci VPN S2S, skorzystaj z usługi [Virtual WAN](../articles/virtual-wan/virtual-wan-about.md).

* Zmiana rozmiaru jednostek SKU VpnGw jest dozwolona w ramach tej samej generacji, z wyjątkiem zmiany rozmiaru podstawowej jednostki SKU. Podstawowa jednostka SKU jest starszą jednostką SKU i ma ograniczenia funkcji. Aby przejść z podstawowej do innej jednostki SKU VpnGw, należy usunąć podstawową bramę sieci VPN sku i utworzyć nową bramę z żądaną kombinacją rozmiarów generacji i jednostki SKU.

* Te limity połączeń są niezależne. Przykładowo dla jednostki SKU VpnGw1 można mieć 128 połączeń SSTP, a oprócz tego 250 połączeń IKEv2.

* Informacje o cenach znajdują się na stronie [Cennik](https://azure.microsoft.com/pricing/details/vpn-gateway).

* Informacje na temat umowy SLA (Service Level Agreement) można znaleźć na stronie [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/).

* W jednym tunelu można osiągnąć przepustowość maksymalnie 1 Gb/s. Zagregowany wskaźnik przepływności w powyższej tabeli opiera się na pomiarach wielu tuneli zagregowanych za pośrednictwem jednej bramy. Test porównawczy agregowanej przepływności dla bramy sieci VPN bazuje na sumie wartości dla połączeń typu lokacja-lokacja i punkt-lokacja. **Posiadanie dużej liczby połączeń typu punkt-lokacja może negatywnie wpływać na połączenie typu lokacja-lokacja z powodu ograniczeń przepustowości.** Ze względu na warunki ruchu internetowego i zachowania aplikacji test porównawczy agregowanej przepływności nie pokazuje przepływności gwarantowanej.

Aby pomóc naszym klientom zrozumieć względną wydajność jednostek SKU przy użyciu różnych algorytmów, użyliśmy publicznie dostępnych narzędzi iPerf i CTSTraffic do pomiaru wydajności. W poniższej tabeli przedstawiono wyniki testów wydajności dla jednostek SKU generacji 1, VpnGw. Jak widać, najlepszą wydajność uzyskuje się, gdy użyliśmy algorytmu GCMAES256 zarówno do szyfrowania IPsec, jak i integralności. Uzyskaliśmy średnią wydajność podczas korzystania z AES256 dla szyfrowania IPsec i SHA256 dla integralności. Kiedy użyliśmy DES3 dla szyfrowania IPsec i SHA256 dla integralności, uzyskaliśmy najniższą wydajność.

|**Generowanie**|**Numer jednostki magazynowej**   | **Algorytmy<br>używane** | **Obserwowana<br>przepustowość** | **Obserwowane pakiety na sekundę<br>** |
|---           |---       | ---                 | ---            | ---                    |
|**Pokolenie 1**|**VpnGw1**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 650 Mb/s<br>500 Mb/s<br>120 Mb/s   | 58,000<br>50 000<br>50 000|
|**Pokolenie 1**|**VpnGw2**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1 Gb/s<br>500 Mb/s<br>120 Mb/s | 90 000<br>80 000<br>55,000|
|**Pokolenie 1**|**VpnGw3**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1,25 Gb/s<br>550 Mb/s<br>120 Mb/s | 105,000<br>90 000<br>60 000|
|**Pokolenie 1**|**VpnGw1AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 650 Mb/s<br>500 Mb/s<br>120 Mb/s   | 58,000<br>50 000<br>50 000|
|**Pokolenie 1**|**VpnGw2AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1 Gb/s<br>500 Mb/s<br>120 Mb/s | 90 000<br>80 000<br>55,000|
|**Pokolenie 1**|**VpnGw3AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1,25 Gb/s<br>550 Mb/s<br>120 Mb/s | 105,000<br>90 000<br>60 000|
