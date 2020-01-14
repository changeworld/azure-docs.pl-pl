---
title: Włączanie usługi komunikacji równorzędnej w bezpośredniej komunikacji równorzędnej przy użyciu portalu
titleSuffix: Azure
description: Włączanie usługi komunikacji równorzędnej w bezpośredniej komunikacji równorzędnej przy użyciu portalu
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: d6b67c42ef8a5ba5ae98894775d1f56cee39ba8b
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774980"
---
# <a name="enable-peering-service-on-a-direct-peering-using-the-portal"></a>Włączanie usługi komunikacji równorzędnej w bezpośredniej komunikacji równorzędnej przy użyciu portalu

W tym artykule opisano sposób włączania [usługi komunikacji równorzędnej](overview-peering-service.md) w bezpośredniej komunikacji równorzędnej przy użyciu portalu.

Jeśli wolisz, możesz ukończyć ten przewodnik przy użyciu programu [PowerShell](howto-peering-service-powershell.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem
* Zapoznaj się z [wymaganiami wstępnymi](prerequisites.md) przed rozpoczęciem konfiguracji.
* Wybierz bezpośrednią komunikację równorzędną w ramach subskrypcji, w której chcesz włączyć usługę komunikacji równorzędnej. Jeśli go nie masz, Przekształć starszą bezpośrednią komunikację równorzędną lub Utwórz nową bezpośrednią komunikację równorzędną.
    * Aby skonwertować starszą bezpośrednią komunikację równorzędną, postępuj zgodnie z instrukcjami w temacie [konwertowanie starszej bezpośredniej komunikacji równorzędnej na zasób platformy Azure przy użyciu portalu](howto-legacy-direct-portal.md).
    * Aby utworzyć nową bezpośrednią komunikację równorzędną, postępuj zgodnie z instrukcjami w temacie [Tworzenie lub modyfikowanie bezpośredniej komunikacji równorzędnej przy użyciu portalu](howto-direct-portal.md).

## <a name="enable-peering-service-on-a-direct-peering"></a>Włącz usługę komunikacji równorzędnej w bezpośredniej komunikacji równorzędnej

### <a name= get></a>Wyświetlanie bezpośredniej komunikacji równorzędnej
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

### <a name= get></a>Włącz bezpośrednią komunikację równorzędną dla usługi Komunikacja równorzędna

Po otwarciu bezpośredniej komunikacji równorzędnej w poprzednim kroku Włącz ją dla usługi komunikacji równorzędnej.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-portal.md)]

## <a name="modify-a-direct-peering-connection"></a>Modyfikowanie bezpośredniego połączenia komunikacji równorzędnej

Jeśli musisz zmodyfikować ustawienia połączenia, zapoznaj się z sekcją **Modyfikowanie bezpośredniej komunikacji równorzędnej** w temacie [Tworzenie lub modyfikowanie bezpośredniej komunikacji równorzędnej przy użyciu portalu](howto-direct-portal.md)

## <a name="next-steps"></a>Następne kroki

* [Tworzenie lub modyfikowanie komunikacji równorzędnej programu Exchange przy użyciu portalu](howto-exchange-portal.md)
* [Konwertowanie starszej komunikacji równorzędnej programu Exchange z zasobem platformy Azure przy użyciu portalu](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

Często zadawane pytania można znaleźć w temacie [Usługa Komunikacja równorzędna — często zadawane pytania](service-faqs.md).