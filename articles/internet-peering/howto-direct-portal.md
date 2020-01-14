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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775331"
---
# <a name="create-or-modify-a-direct-peering-using-the-portal"></a>Tworzenie lub modyfikowanie bezpośredniej komunikacji równorzędnej przy użyciu portalu

W tym artykule opisano sposób tworzenia bezpośredniej komunikacji równorzędnej firmy Microsoft przy użyciu portalu. W tym artykule przedstawiono również sposób sprawdzania stanu zasobu, aktualizowania go lub usuwania i anulowania aprowizacji.

Jeśli wolisz, możesz ukończyć ten przewodnik przy użyciu programu [PowerShell](howto-direct-powershell.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem
* Przejrzyj [wymagania wstępne](prerequisites.md) i [bezpośredni Przewodnik komunikacji równorzędnej](walkthrough-direct-all.md) przed rozpoczęciem konfiguracji.
* Jeśli masz już bezpośrednią komunikację równorzędną z firmą Microsoft, która nie jest konwertowana na zasoby platformy Azure, zapoznaj się z tematem [konwertowanie starszej bezpośredniej komunikacji równorzędnej na zasób platformy Azure przy użyciu portalu](howto-legacy-direct-portal.md)

## <a name="create-and-provision-a-direct-peering"></a>Tworzenie i obsługa bezpośredniej komunikacji równorzędnej

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Zaloguj się do portalu i wybierz swoją subskrypcję
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name=create></a>Tworzenie bezpośredniej komunikacji równorzędnej

Nowe żądanie komunikacji równorzędnej można utworzyć przy użyciu zasobu **komunikacji równorzędnej** .

#### <a name="launch-resource-and-configure-basic-settings"></a>Uruchom zasób i skonfiguruj ustawienia podstawowe
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Konfigurowanie połączeń i przesyłanie
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration.md)]

### <a name=get></a>Weryfikowanie bezpośredniej komunikacji równorzędnej
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="modify"></a>Modyfikowanie bezpośredniej komunikacji równorzędnej
[!INCLUDE [peering-direct-modify-portal](./includes/direct-portal-modify.md)]

## <a name="delete"></a>Anulowanie aprowizacji bezpośredniej komunikacji równorzędnej
[!INCLUDE [peering-direct-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Następne kroki

* [Tworzenie lub modyfikowanie komunikacji równorzędnej programu Exchange przy użyciu portalu](howto-exchange-portal.md).
* [Przekonwertuj starszą komunikację równorzędną programu Exchange do zasobu platformy Azure przy użyciu portalu](howto-legacy-exchange-portal.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

Aby uzyskać więcej informacji, zobacz [często zadawane pytania dotyczące internetowej komunikacji równorzędnej](faqs.md)
