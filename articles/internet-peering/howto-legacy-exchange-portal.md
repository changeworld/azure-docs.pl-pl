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
ms.openlocfilehash: a40c7bbc9f37135814b7bba3396d368faf97a166
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775201"
---
# <a name="convert-a-legacy-exchange-peering-to-azure-resource-using-the-portal"></a>Konwertowanie starszej komunikacji równorzędnej z punktem wymiany na zasób platformy Azure przy użyciu portalu

W tym artykule opisano sposób konwertowania istniejącej starszej komunikacji równorzędnej programu Exchange na zasób platformy Azure przy użyciu portalu.

Jeśli wolisz, możesz ukończyć ten przewodnik za pomocą programu [PowerShell](howto-legacy-exchange-powershell.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem
* Przed rozpoczęciem konfiguracji zapoznaj [się z wymaganiami wstępnymi](prerequisites.md) i [instruktażem komunikacji równorzędnej programu Exchange.](walkthrough-exchange-all.md)

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>Konwertowanie starszej komunikacji równorzędnej z punktem wymiany na zasób platformy Azure

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Zaloguj się do portalu i wybierz subskrypcję
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-legacy-exchange-peering"></a><a name=create></a>Konwertowanie starszej komunikacji równorzędnej programu Exchange

Starsze połączenia komunikacji równorzędnej można konwertować przy użyciu **zasobu komunikacji równorzędnej.**

#### <a name="launch-resource-and-configure-basic-settings"></a>Uruchamianie zasobu i konfigurowanie podstawowych ustawień
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Konfigurowanie połączeń i przesyłanie
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration-legacy.md)]

### <a name="verify-exchange-peering"></a><a name=get></a>Weryfikowanie komunikacji równorzędnej programu Exchange
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="additional-resources"></a>Zasoby dodatkowe

Aby uzyskać więcej informacji, odwiedź [najczęściej zadawane pytania dotyczące komunikacji internet](faqs.md)

## <a name="next-steps"></a>Następne kroki

* [Tworzenie lub modyfikowanie komunikacji równorzędnej z punktem wymiany przy użyciu portalu](howto-exchange-portal.md)
