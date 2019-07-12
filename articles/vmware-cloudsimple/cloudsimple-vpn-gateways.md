---
title: Bramy sieci VPN w VMware Solution by CloudSimple - Azure
description: Dowiedz się więcej o CloudSimple site-to-site VPN i pojęcia dotyczące sieci VPN typu punkt lokacja
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2eae81f357904bd5034d7409ef42b681d1085930
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67695216"
---
# <a name="vpn-gateways-overview"></a>Przegląd bram sieci VPN

Bramy sieci VPN jest używany do wysyłania zaszyfrowanego ruchu sieciowego między siecią region CloudSimple w lokalizacji lokalnej lub komputera za pośrednictwem publicznej sieci Internet.  Każdy region może mieć tylko jedną bramę sieci VPN. Możesz jednak utworzyć wiele połączeń do tej samej bramy sieci VPN. W przypadku utworzenia wielu połączeń do tej samej bramy sieci VPN wszystkie tunele VPN współdzielą dostępną przepustowość bramy.

CloudSimple oferuje dwa typy bram sieci VPN:

* Brama sieci VPN typu lokacja lokacja
* Brama sieci VPN typu punkt lokacja

## <a name="site-to-site-vpn-gateway"></a>Bramy sieci VPN typu lokacja lokacja

Bramy sieci VPN typu lokacja lokacja służy do wysyłania zaszyfrowanego ruchu sieciowego między siecią region CloudSimple i lokalnym centrum danych. Umożliwia to połączenie umożliwia zdefiniowanie zakresu podsieci/CIDR komunikację między siecią lokalną i siecią region CloudSimple.

Brama VPN umożliwia korzystanie z usług ze środowiska lokalnego w chmurze prywatnej i usług w chmurze prywatnej, z siecią lokalną.  CloudSimple udostępnia serwer sieci VPN oparte na zasadach dla podczas nawiązywania połączenia z sieci lokalnej.

Przypadki użycia dla sieci VPN typu lokacja lokacja, obejmują:

* Dostępność serwera vCenter chmury prywatnej z dowolnej stacji roboczej w sieci lokalnej.
* Korzystanie z usługi Active Directory w środowisku lokalnym jako źródła tożsamości vCenter.
* Wygodne transfer szablony maszyn wirtualnych, obrazów ISO i innych plików z zasobów lokalnych do serwera vCenter chmury prywatnej.
* Dostępność obciążeń w chmurze prywatnej z sieci lokalnej.

![Topologii połączeń sieci VPN typu lokacja-lokacja](media/cloudsimple-site-to-site-vpn-connection.png)

> [!IMPORTANT]
> Należy określić ograniczenie wartości TCP MSS, w bajtach 1078 lub niższą. Lub jeśli Twoje urządzenia sieci VPN nie obsługują MSS obcinanie, można również ustawić rozmiar jednostki MTU w interfejsie tunelu na bajty 1118 zamiast tego. 

### <a name="cryptographic-parameters"></a>Parametry kryptograficzne

Połączenia sieci VPN typu lokacja lokacja używa następujących parametrów kryptograficznych domyślne nawiązać bezpiecznego połączenia.  Podczas tworzenia połączenia z lokalnym urządzeniem sieci VPN, należy użyć dowolnego z następujących parametrów obsługiwanych przez bramy sieci VPN w środowisku lokalnym.

#### <a name="phase-1-proposals"></a>Faza 1 propozycji

| Parametr | Wniosek 1 | Propozycja 2 | Propozycja 3 |
|-----------|------------|------------|------------|
| Wersja IKE | IKEv1 | IKEv1 | IKEv1 |
| Szyfrowanie | AES 128 | AES 256 | AES 256 |
| Algorytm wyznaczania wartości skrótu| SHA 256 | SHA 256 | SHA 1 |
| Grupa Diffie-Hellman (Grupa DH) | 2 | 2 | 2 |
| Okres istnienia | 28 800 sekund | 28 800 sekund | 28 800 sekund |
| Rozmiar danych | 4 GB | 4 GB | 4 GB |


#### <a name="phase-2-proposals"></a>Faza 2 propozycji 

| Parametr | Wniosek 1 | Propozycja 2 | Propozycja 3 |
|-----------|------------|------------|------------|
| Szyfrowanie | AES 128 | AES 256 | AES 256 |
| Algorytm wyznaczania wartości skrótu| SHA 256 | SHA 256 | SHA 1 |
| Doskonałe rozwiązanie do przodu tajemnicy grupę (PFS) | Brak | Brak | Brak |
| Okres istnienia | 1800 sekund | 1800 sekund | 1800 sekund |
| Rozmiar danych | 4 GB | 4 GB | 4 GB |

## <a name="point-to-site-vpn-gateway"></a>Brama sieci VPN typu punkt lokacja

Sieć VPN punkt lokacja służy wysyłać zaszyfrowany ruch sieciowy między siecią region CloudSimple i komputera klienckiego.  Sieci VPN typu punkt lokacja jest najprostszym sposobem na dostęp do sieci prywatnej chmury, w tym usługi chmury prywatnej programu vCenter i maszyn wirtualnych obsługujących obciążenia.  Połączenie sieci VPN typu punkt lokacja należy użyć, jeśli łączysz się Chmura prywatna zdalnie.

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie bramy sieci VPN](https://docs.azure.cloudsimple.com/vpn-gateway/)