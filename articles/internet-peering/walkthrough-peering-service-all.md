---
title: Przewodnik partnera usługi Peering Service
titleSuffix: Azure
description: Przewodnik partnera usługi Peering Service
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: d626d98613bd5d988b599d0980c885d7f73bb958
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720252"
---
# <a name="peering-service-partner-walkthrough"></a>Przewodnik partnera usługi Peering Service

W tej sekcji opisano kroki, które dostawca musi wykonać, aby włączyć bezpośrednią komunikację równorzędną dla usługi komunikacji równorzędnej.

## <a name="create-direct-peering-connection-for-peering-service"></a>Tworzenie bezpośredniego połączenia komunikacji równorzędnej dla usługi komunikacji równorzędnej
Dostawcy usług mogą rozszerzyć swój zasięg geograficzny, tworząc nowe bezpośrednie komunikacji równorzędnej, które obsługują usługę komunikacji równorzędnej. Aby to osiągnąć,
1. Zostań partnerem usługi komunikacji równorzędnej, jeśli jeszcze nie
1. Postępuj zgodnie z instrukcjami, aby [utworzyć lub zmodyfikować bezpośrednie komunikacji równorzędnej za pomocą portalu](howto-direct-portal.md). Upewnij się, że spełnia wymagania o wysokiej dostępności.
1. Następnie wykonaj kroki, aby [włączyć usługę komunikacji równorzędnej w bezpośredniej komunikacji równorzędnej za pomocą portalu](howto-peering-service-portal.md).

## <a name="use-legacy-direct-peering-connection-for-peering-service"></a>Używanie starszego bezpośredniego połączenia komunikacji równorzędnej dla usługi komunikacji równorzędnej
Jeśli masz starszą komunikację równorzędną bezpośrednią, której chcesz użyć do obsługi usługi komunikacji równorzędnej,
1. Zostań partnerem usługi komunikacji równorzędnej, jeśli jeszcze nie.
1. Postępuj zgodnie z instrukcjami, aby [przekonwertować starszą bezpośrednią komunikację równorzędną na zasób platformy Azure za pomocą portalu](howto-legacy-direct-portal.md). W razie potrzeby zamów dodatkowe obwody, aby spełnić wymagania dotyczące wysokiej dostępności.
1. Następnie wykonaj kroki, aby [włączyć usługę komunikacji równorzędnej w bezpośredniej komunikacji równorzędnej za pomocą portalu](howto-peering-service-portal.md).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [zasadach komunikacji równorzędnej](https://peering.azurewebsites.net/peering).
* Aby dowiedzieć się więcej o krokach konfigurowania komunikacji bezpośredniej z firmą Microsoft, postępuj zgodnie z [wskazówki dotyczące komunikacji równorzędnej direct](walkthrough-direct-all.md).
* Aby dowiedzieć się więcej o krokach konfigurowania komunikacji równorzędnej programu Exchange z firmą Microsoft, postępuj zgodnie z instrukcją dotyczącą [komunikacji równorzędnej programu Exchange](walkthrough-exchange-all.md).