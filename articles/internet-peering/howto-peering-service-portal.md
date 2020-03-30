---
title: Włączanie usługi Peering Service w bezpośredniej komunikacji równorzędnej przy użyciu portalu
titleSuffix: Azure
description: Włączanie usługi Peering Service w bezpośredniej komunikacji równorzędnej przy użyciu portalu
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: article
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: 87a65826a338f4098ae24c33939ea7f9d4475e36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129971"
---
# <a name="enable-peering-service-on-a-direct-peering-using-the-portal"></a>Włączanie usługi Peering Service w bezpośredniej komunikacji równorzędnej przy użyciu portalu

W tym artykule opisano, jak włączyć [usługę komunikacji równorzędnej](overview-peering-service.md) w bezpośredniej komunikacji równorzędnej przy użyciu portalu.

Jeśli wolisz, możesz ukończyć ten przewodnik za pomocą programu [PowerShell](howto-peering-service-powershell.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem
* Przejrzyj [wymagania wstępne](prerequisites.md) przed rozpoczęciem konfiguracji.
* Wybierz bezpośrednią komunikację równorzędną w subskrypcji, na której chcesz włączyć usługę komunikacji równorzędnej. Jeśli go nie masz, przekonwertuj starszą bezpośrednią komunikację równorzędną lub utwórz nową bezpośrednią komunikację równorzędną.
    * Aby przekonwertować starszą komunikację równorzędną bezpośrednią, postępuj zgodnie z instrukcjami w [obszarze Konwertuj starsze bezpośrednie komunikacji równorzędnej do zasobu platformy Azure za pomocą portalu](howto-legacy-direct-portal.md).
    * Aby utworzyć nową komunikację równorzędną bezpośrednią, postępuj zgodnie z instrukcjami w [pliku Create lub zmodyfikuj bezpośrednie komunikacji równorzędnej za pomocą portalu](howto-direct-portal.md).

## <a name="enable-peering-service-on-a-direct-peering"></a>Włączanie usługi Peering Service w bezpośredniej komunikacji równorzędnej

### <a name="view-direct-peering"></a><a name= get></a>Wyświetl bezpośrednią komunikację równorzędnych
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>Włączanie usługi komunikacji równorzędnej bezpośredniej dla komunikacji równorzędnej

Po otwarciu direct komunikacji równorzędnej w poprzednim kroku, włącz go dla usługi komunikacji równorzędnej.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-portal.md)]

## <a name="modify-a-direct-peering-connection"></a>Modyfikowanie bezpośredniego połączenia równorzędnego

Jeśli chcesz zmodyfikować ustawienia połączenia, zapoznaj się **z sekcją Modyfikowanie bezpośredniej komunikacji równorzędnej** w [obszarze Tworzenie lub modyfikowanie komunikacji równorzędnej za pomocą portalu](howto-direct-portal.md)

## <a name="next-steps"></a>Następne kroki

* [Tworzenie lub modyfikowanie komunikacji równorzędnej programu Exchange za pomocą portalu](howto-exchange-portal.md)
* [Konwertowanie starszej komunikacji równorzędnej z punktem wymiany na zasób platformy Azure przy użyciu portalu](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

Aby zapoznać się z często zadawanymi pytaniami, zobacz [Często zadawane pytania dotyczące usługi komunikacji równorzędnej.](service-faqs.md)