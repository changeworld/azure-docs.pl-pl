---
title: Tworzenie lub modyfikowanie bezpośredniej komunikacji równorzędnej przy użyciu portalu
titleSuffix: Azure
description: Tworzenie lub modyfikowanie bezpośredniej komunikacji równorzędnej przy użyciu portalu
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 67a4944082d3ebc14fd564eedee0310afe6e3ff5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775331"
---
# <a name="create-or-modify-a-direct-peering-using-the-portal"></a>Tworzenie lub modyfikowanie bezpośredniej komunikacji równorzędnej przy użyciu portalu

W tym artykule opisano sposób tworzenia komunikacji równorzędnej microsoft direct przy użyciu portalu. W tym artykule pokazano również, jak sprawdzić stan zasobu, zaktualizować go lub usunąć i anulować jego aprowizję.

Jeśli wolisz, możesz ukończyć ten przewodnik za pomocą programu [PowerShell](howto-direct-powershell.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem
* Przed rozpoczęciem konfiguracji zapoznaj [się z wymaganiami wstępnymi](prerequisites.md) i [bezpośrednią komunikacją równorzędnej.](walkthrough-direct-all.md)
* W przypadku, gdy masz już bezpośrednią komunikację równorzędną z firmą Microsoft, która nie jest konwertowana na zasoby platformy Azure, zapoznaj się [z poleceniem Konwertuj starsze bezpośrednie komunikacji równorzędnej do zasobu Azure przy użyciu portalu](howto-legacy-direct-portal.md)

## <a name="create-and-provision-a-direct-peering"></a>Tworzenie i inicjowanie obsługi administracyjnej komunikacji równorzędnej

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Zaloguj się do portalu i wybierz subskrypcję
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Tworzenie komunikacji równorzędnej bezpośredniej

Nowe żądanie komunikacji równorzędnej można utworzyć przy użyciu **zasobu komunikacji równorzędnej.**

#### <a name="launch-resource-and-configure-basic-settings"></a>Uruchamianie zasobu i konfigurowanie podstawowych ustawień
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Konfigurowanie połączeń i przesyłanie
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Weryfikowanie komunikacji bezpośredniej komunikacji równorzędnej
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>Modyfikowanie komunikacji równorzędnej bezpośredniej
[!INCLUDE [peering-direct-modify-portal](./includes/direct-portal-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>Anulowanie obsługi administracyjnej funkcji bezpośrednie równorzędne
[!INCLUDE [peering-direct-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Następne kroki

* [Tworzenie lub modyfikowanie komunikacji równorzędnej programu Exchange za pomocą portalu](howto-exchange-portal.md).
* [Konwertuj starszą komunikację równorzędną programu Exchange na zasób platformy Azure przy użyciu portalu](howto-legacy-exchange-portal.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

Aby uzyskać więcej informacji, odwiedź [najczęściej zadawane pytania dotyczące komunikacji internet](faqs.md)
