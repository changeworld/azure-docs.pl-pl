---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 05/22/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 65c1011e6e005c190d1ae5d51fdd009f66a20956
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70919719"
---
|**SKU**   | **Połączenia typu lokacja-lokacja/Połączenia między sieciami wirtualnymi<br>Tunele** | **Połączenia typu punkt-lokacja<br> Połączenia SSTP** | **P2S<br> połączenia IKEv2/OpenVPN** | **Test porównawczy<br>agregowanej przepływności** | **BGP** | **Strefa nadmiarowa** |
|---       | ---        | ---       | ---            | ---       | --- | --- |
|**Podstawowa** | Maksymalnie z 10    | Maksymalnie z 128  | Brak obsługi  | 100 Mb/s  | Brak obsługi| Nie |
|**VpnGw1**| Maksymalnie z 30*   | Maksymalnie z 128  | Maksymalnie z 250       | 650 Mb/s  | Obsługiwane | Nie |
|**VpnGw2**| Maksymalnie z 30*   | Maksymalnie z 128  | Maksymalnie z 500       | 1 Gb/s    | Obsługiwane | Nie |
|**VpnGw3**| Maksymalnie z 30*   | Maksymalnie z 128  | Maksymalnie z 1000      | 1,25 Gb/s | Obsługiwane | Nie |
|**VpnGw1AZ**| Maksymalnie z 30*   | Maksymalnie z 128  | Maksymalnie z 250       | 650 Mb/s  | Obsługiwane | Tak |
|**VpnGw2AZ**| Maksymalnie z 30*   | Maksymalnie z 128  | Maksymalnie z 500       | 1 Gb/s    | Obsługiwane | Tak |
|**VpnGw3AZ**| Maksymalnie z 30*   | Maksymalnie z 128  | Maksymalnie z 1000      | 1,25 Gb/s | Obsługiwane | Tak |

(*) Jeśli potrzebujesz więcej niż 30 tuneli sieci VPN S2S, skorzystaj z usługi [Virtual WAN](../articles/virtual-wan/virtual-wan-about.md).

* Te limity połączeń są niezależne. Przykładowo dla jednostki SKU VpnGw1 można mieć 128 połączeń SSTP, a oprócz tego 250 połączeń IKEv2.

* Informacje o cenach znajdują się na stronie [Cennik](https://azure.microsoft.com/pricing/details/vpn-gateway).

* Informacje na temat umowy SLA (Service Level Agreement) można znaleźć na stronie [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/).

* VpnGw1, VpnGw2, VpnGw3, VpnGw1AZ, VpnGw2AZ i VpnGw3AZ są obsługiwane przez bramy sieci VPN tylko przy użyciu modelu wdrażania Menedżer zasobów.

* Podstawowa jednostka SKU jest traktowana jako starsza jednostka SKU. Podstawowa jednostka SKU ma pewne ograniczenia w zakresie funkcji. Rozmiaru bramy, która korzysta z podstawowej jednostki SKU, nie można zmienić na jedną z nowych jednostek SKU bramy. Zamiast tego należy przejść na nową jednostkę SKU, co wiąże się z koniecznością usunięcia i ponownego utworzenia bramy sieci VPN. Zanim użyjesz podstawowej jednostki SKU sprawdź, czy potrzebna Ci funkcja jest obsługiwana.

* Test porównawczy agregowanej przepływności opiera się na pomiarach wielu tuneli zagregowanych za pośrednictwem jednej bramy. Test porównawczy agregowanej przepływności dla bramy sieci VPN bazuje na sumie wartości dla połączeń typu lokacja-lokacja i punkt-lokacja. **Posiadanie dużej liczby połączeń typu punkt-lokacja może negatywnie wpływać na połączenie typu lokacja-lokacja z powodu ograniczeń przepustowości.** Ze względu na warunki ruchu internetowego i zachowania aplikacji test porównawczy agregowanej przepływności nie pokazuje przepływności gwarantowanej.

* Aby pomóc naszym klientom zrozumieć względną wydajność jednostek SKU VpnGw, do mierzenia wydajności użyto publicznie dostępnych narzędzi iPerf i CTSTraffic. W poniższej tabeli przedstawiono wyniki testów wydajności przy użyciu różnych algorytmów. Jak widać, najlepszą wydajność uzyskuje się w przypadku użycia algorytmu GCMAES256 w przypadku szyfrowania i integralności protokołu IPsec. Średnia wydajność podczas korzystania z AES256 na potrzeby szyfrowania IPsec i SHA256 w celu zapewnienia integralności. W przypadku użycia DES3 na potrzeby szyfrowania IPsec i SHA256 w celu zapewnienia integralności mamy najniższą wydajność.

|**SKU**   | **Używane<br>algorytmy** | **Zaobserwowana przepływność<br>** | **Poobserwowane pakiety<br>na sekundę** |
|---       | ---                 | ---            | ---                    |
|**VpnGw1**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 650 Mb/s<br>500 Mb/s<br>120 MB/s   | 58 000<br>50,000<br>50,000|
|**VpnGw2**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1 Gb/s<br>500 Mb/s<br>120 MB/s | 90,000<br>80,000<br>55 000|
|**VpnGw3**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1,25 Gb/s<br>550 MB/s<br>120 MB/s | 105 000<br>90,000<br>60,000|
|**VpnGw1AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 650 Mb/s<br>500 Mb/s<br>120 MB/s   | 58 000<br>50,000<br>50,000|
|**VpnGw2AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1 Gb/s<br>500 Mb/s<br>120 MB/s | 90,000<br>80,000<br>55 000|
|**VpnGw3AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1,25 Gb/s<br>550 MB/s<br>120 MB/s | 105 000<br>90,000<br>60,000|
