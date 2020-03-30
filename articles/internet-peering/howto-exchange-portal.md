---
title: Tworzenie lub modyfikowanie komunikacji równorzędnej z punktem wymiany przy użyciu portalu
titleSuffix: Azure
description: Tworzenie lub modyfikowanie komunikacji równorzędnej z punktem wymiany przy użyciu portalu
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 2c186decf68d167ab2c5ab7696c2dfb51d77a071
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774577"
---
# <a name="create-or-modify-an-exchange-peering-using-the-portal"></a>Tworzenie lub modyfikowanie komunikacji równorzędnej z punktem wymiany przy użyciu portalu

W tym artykule opisano sposób tworzenia komunikacji równorzędnej programu Microsoft Exchange przy użyciu portalu. W tym artykule pokazano również, jak sprawdzić stan zasobu, zaktualizować go lub usunąć i anulować jego aprowizję.

Jeśli wolisz, możesz ukończyć ten przewodnik za pomocą programu [PowerShell](howto-exchange-powershell.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem
* Przed rozpoczęciem konfiguracji zapoznaj [się z wymaganiami wstępnymi](prerequisites.md) i [instruktażem komunikacji równorzędnej programu Exchange.](walkthrough-exchange-all.md)
* Jeśli masz już komunikację równorzędną programu Exchange z firmą Microsoft, która nie jest konwertowana na zasoby platformy Azure, zapoznaj się [z temat Convert a legacy Exchange peering to Azure resource using the portal](howto-legacy-exchange-portal.md)

## <a name="create-and-provision-an-exchange-peering"></a>Tworzenie i inicjowanie obsługi administracyjnej komunikacji równorzędnej programu Exchange

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Zaloguj się do portalu i wybierz subskrypcję
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Tworzenie komunikacji równorzędnej programu Exchange

Nowe żądanie komunikacji równorzędnej można utworzyć przy użyciu **zasobu komunikacji równorzędnej.**

#### <a name="launch-resource-and-configure-basic-settings"></a>Uruchamianie zasobu i konfigurowanie podstawowych ustawień
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Konfigurowanie połączeń i przesyłanie
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration.md)]

### <a name="verify-an-exchange-peering"></a><a name=get></a>Weryfikowanie komunikacji równorzędnej programu Exchange
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>Modyfikowanie komunikacji równorzędnej programu Exchange
[!INCLUDE [peering-exchange-modify-portal](./includes/exchange-portal-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name="delete"></a>Anulowanie obsługi administracyjnej komunikacji równorzędnej programu Exchange
[!INCLUDE [peering-exchange-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Następne kroki

* [Tworzenie lub modyfikowanie bezpośredniej komunikacji równorzędnej przy użyciu portalu](howto-direct-portal.md)
* [Konwertowanie starszej bezpośredniej komunikacji równorzędnej na zasób platformy Azure przy użyciu portalu](howto-legacy-direct-portal.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

Aby uzyskać więcej informacji, odwiedź [najczęściej zadawane pytania dotyczące komunikacji internet](faqs.md)
