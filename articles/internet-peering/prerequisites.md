---
title: Wymagania wstępne dotyczące konfigurowania komunikacji równorzędnej z firmą Microsoft
titleSuffix: Azure
description: Wymagania wstępne dotyczące konfigurowania komunikacji równorzędnej z firmą Microsoft
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: conceptual
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 3c820a7be561aeef9b7e50fd0ac0cf4dee721af8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775409"
---
# <a name="prerequisites-to-set-up-peering-with-microsoft"></a>Wymagania wstępne dotyczące konfigurowania komunikacji równorzędnej z firmą Microsoft

Upewnij się, że poniższe wymagania wstępne są spełnione, zanim zażądasz nowego elementu równorzędnego lub przekonwertujesz starszą komunikację równorzędną na zasób platformy Azure.

## <a name="azure-related-prerequisites"></a>Wymagania wstępne związane z platformą Azure
* **Konto Platformy Microsoft Azure:** Jeśli nie masz konta Platformy Microsoft Azure, utwórz [konto Platformy Microsoft Azure](https://azure.microsoft.com/free). Prawidłowa i aktywna subskrypcja platformy Microsoft Azure jest wymagana do skonfigurowania komunikacji równorzędnej, ponieważ komunikacja równorzędna jest modelowana jako zasoby w ramach subskrypcji platformy Azure. Ważne jest, aby pamiętać, że:
    * Typy zasobów platformy Azure używane do konfigurowania komunikacji równorzędnej są zawsze bezpłatne produkty platformy Azure, to znaczy, nie są naliczane opłaty za tworzenie konta platformy Azure lub tworzenie subskrypcji lub uzyskiwanie dostępu do zasobów platformy Azure **PeerAsn** i **peering** skonfigurować komunikacji równorzędnej. Nie należy tego mylić z umową komunikacji równorzędnej dla bezpośredniego komunikacji równorzędnej między użytkownikiem a firmą Microsoft, której warunki są wyraźnie omawiane z naszym zespołem komunikacji równorzędnej. Jeśli masz jakiekolwiek pytania dotyczące, skontaktuj się z firmą Microsoft w [komunikacji równorzędnej.](mailto:peering@microsoft.com)
    * Tej samej subskrypcji platformy Azure można użyć, aby uzyskać dostęp do innych produktów platformy Azure lub usług w chmurze, które mogą być bezpłatne lub płatne. Po dodaniu dostępu do płatnego produktu poniesiesz opłaty.
    * Jeśli tworzysz nowe konto i/lub subskrypcję platformy Azure, możesz kwalifikować się do bezpłatnego kredytu na platformę Azure w okresie próbnym, który możesz wykorzystać do wypróbowania usług w chmurze Azure. Jeśli chcesz, odwiedź [konto platformy Microsoft Azure,](https://azure.microsoft.com/free) aby uzyskać więcej informacji.

* **Skojarzyć peer ASN:** Przed złożeniem wniosku o komunikację równorzędnej należy najpierw skojarzyć numer ASN i informacje kontaktowe z subskrypcją. Postępuj zgodnie z instrukcjami zawartymi w [skojarzeń elementów równorzędnych ASN do subskrypcji platformy Azure](howto-subscription-association-powershell.md).

## <a name="other-prerequisites"></a>Inne warunki wstępne
* **Profil PeeringDB:** Oczekuje się, że elementy równorzędne będą miały pełny i aktualny profil w [peeringdb](https://www.peeringdb.com). Używamy tych informacji w naszym systemie rejestracji, aby zweryfikować dane peera, takie jak informacje NOC, techniczne informacje kontaktowe i ich obecność w obiektach komunikacji równorzędnej itp.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie lub modyfikowanie bezpośredniego komunikacji równorzędnej za pomocą portalu](howto-direct-portal.md).
* [Konwertowanie starszej bezpośredniej komunikacji równorzędnej na zasób platformy Azure przy użyciu portalu](howto-legacy-direct-portal.md)
* [Tworzenie lub modyfikowanie komunikacji równorzędnej programu Exchange za pomocą portalu](howto-exchange-portal.md)
* [Konwertowanie starszej komunikacji równorzędnej z punktem wymiany na zasób platformy Azure przy użyciu portalu](howto-legacy-exchange-portal.md)