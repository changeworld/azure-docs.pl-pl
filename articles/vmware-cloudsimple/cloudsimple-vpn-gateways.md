---
title: Rozwiązanie Azure VMware firmy CloudSimple — bramy sieci VPN
description: Dowiedz się więcej o pojęciach sieci VPN między lokacjami i sieci VPN typu "punkt-lokacja"
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 662fa342b3a18f726b418c496ff3fda937445301
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024861"
---
# <a name="vpn-gateways-overview"></a>Omówienie bram sieci VPN

Brama sieci VPN służy do wysyłania zaszyfrowanego ruchu między siecią regionu CloudSimple w lokalizacji lokalnej lub komputerem za pośrednictwem publicznego Internetu.  Każdy region może mieć jedną bramę sieci VPN, która może obsługiwać wiele połączeń. W przypadku utworzenia wielu połączeń do tej samej bramy sieci VPN wszystkie tunele VPN współdzielą dostępną przepustowość bramy.

CloudSimple udostępnia dwa rodzaje bram sieci VPN:

* Brama sieci VPN między lokacjami
* Brama sieci VPN typu punkt-lokacja

## <a name="site-to-site-vpn-gateway"></a>Brama sieci VPN między lokacjami

Brama sieci VPN między lokacjami jest używana do wysyłania zaszyfrowanego ruchu między siecią regionu CloudSimple a lokalnym centrum danych. To połączenie służy do definiowania zakresu podsieci/CIDR dla ruchu sieciowego między siecią lokalną a siecią regionu CloudSimple.

Brama sieci VPN umożliwia korzystanie z usług lokalnych w chmurze prywatnej i usług w chmurze prywatnej z sieci lokalnej.  CloudSimple udostępnia oparty na zasadach serwer sieci VPN do ustanawiania połączenia z sieci lokalnej.

Przypadki użycia sieci VPN typu lokacja lokacja:

* Dostępność usługi Private Cloud vCenter z dowolnej stacji roboczej w sieci lokalnej.
* Korzystanie z lokalnej usługi Active Directory jako źródła tożsamości vCenter.
* Wygodne przesyłanie szablonów maszyn wirtualnych, iso i innych plików z zasobów lokalnych do usługi Private Cloud vCenter.
* Dostępność obciążeń działających w chmurze prywatnej z sieci lokalnej.

![Topologia połączeń sieci VPN między lokacjami](media/cloudsimple-site-to-site-vpn-connection.png)

### <a name="cryptographic-parameters"></a>Parametry kryptograficzne

Połączenie sieci VPN między lokacjami używa następujących domyślnych parametrów kryptograficznych do ustanowienia bezpiecznego połączenia.  Podczas tworzenia połączenia z lokalnego urządzenia sieci VPN należy użyć dowolnego z następujących parametrów obsługiwanych przez lokalną bramę sieci VPN.

#### <a name="phase-1-proposals"></a>Wnioski fazy 1

| Parametr | Wniosek 1 | Wniosek 2 | Wniosek 3 |
|-----------|------------|------------|------------|
| Wersja IKE | IKEv1 | IKEv1 | IKEv1 |
| Szyfrowanie | AES 128 | AES 256 | AES 256 |
| Algorytm mieszania| Sha 256 | Sha 256 | Sha 1 (właso) |
| Grupa Diffie Hellman (Grupa DH) | 2 | 2 | 2 |
| Czas życia | 28 800 sekund | 28 800 sekund | 28 800 sekund |
| Rozmiar danych | 4 GB | 4 GB | 4 GB |

#### <a name="phase-2-proposals"></a>Wnioski dotyczące fazy 2

| Parametr | Wniosek 1 | Wniosek 2 | Wniosek 3 |
|-----------|------------|------------|------------|
| Szyfrowanie | AES 128 | AES 256 | AES 256 |
| Algorytm mieszania| Sha 256 | Sha 256 | Sha 1 (właso) |
| Perfect Forward Secrecy Group (grupa PFS) | Brak | Brak | Brak |
| Czas życia | 1800 sekund | 1800 sekund | 1800 sekund |
| Rozmiar danych | 4 GB | 4 GB | 4 GB |


> [!IMPORTANT]
> Ustaw TCP MSS Clamping na 1200 na urządzeniu VPN. Lub jeśli urządzenia sieci VPN nie obsługują zaciskania MSS, możesz alternatywnie ustawić jednostkę MTU w interfejsie tunelu na 1240 bajtów.

## <a name="point-to-site-vpn-gateway"></a>Brama sieci VPN typu punkt-lokacja

Sieć VPN typu punkt-lokacja służy do wysyłania zaszyfrowanego ruchu między siecią regionu CloudSimple a komputerem klienckim.  Sieć VPN typu punkt-lokacja to najprostszy sposób uzyskiwania dostępu do sieci prywatnej chmury, w tym do wirtualnych centrów wirtualnych private cloud i obciążeń.  Użyj łączności sieci VPN typu punkt-lokacja, jeśli łączysz się zdalnie z chmurą prywatną.

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie bramy sieci VPN](vpn-gateway.md)
