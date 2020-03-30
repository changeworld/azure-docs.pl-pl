---
title: Konwertowanie starszej bezpośredniej komunikacji równorzędnej na zasób platformy Azure przy użyciu portalu
titleSuffix: Azure
description: Konwertowanie starszej bezpośredniej komunikacji równorzędnej na zasób platformy Azure przy użyciu portalu
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 9900414d38bd597d08a80d15e908228c06ce06ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775058"
---
# <a name="convert-a-legacy-direct-peering-to-azure-resource-using-the-portal"></a>Konwertowanie starszej bezpośredniej komunikacji równorzędnej na zasób platformy Azure przy użyciu portalu

W tym artykule opisano sposób konwertowania istniejącej starszej funkcji bezpośrednie komunikacji równorzędnej na zasób platformy Azure przy użyciu portalu.

Jeśli wolisz, możesz ukończyć ten przewodnik za pomocą programu [PowerShell](howto-legacy-direct-powershell.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem
* Przed rozpoczęciem konfiguracji zapoznaj [się z wymaganiami wstępnymi](prerequisites.md) i [bezpośrednią komunikacją równorzędnej.](walkthrough-direct-all.md)


## <a name="convert-legacy-direct-peering-to-azure-resource"></a>Konwertowanie starszego bezpośredniego równorzędnego zasobu platformy Azure

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Zaloguj się do portalu i wybierz subskrypcję
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-legacy-direct-peering"></a><a name=create></a>Konwertowanie starszej komunikacji bezpośredniej komunikacji równorzędnej

Starsze połączenia komunikacji równorzędnej można konwertować przy użyciu **zasobu komunikacji równorzędnej.**

#### <a name="launch-resource-and-configure-basic-settings"></a>Uruchamianie zasobu i konfigurowanie podstawowych ustawień
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Konfigurowanie połączeń i przesyłanie
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration-legacy.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Weryfikowanie komunikacji bezpośredniej komunikacji równorzędnej
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="additional-resources"></a>Zasoby dodatkowe

Aby uzyskać więcej informacji, odwiedź [najczęściej zadawane pytania dotyczące komunikacji internet](faqs.md)

## <a name="next-steps"></a>Następne kroki

* [Tworzenie lub modyfikowanie bezpośredniego komunikacji równorzędnej za pomocą portalu](howto-direct-portal.md).
