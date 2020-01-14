---
title: Przewodnik po partnerze usługi Komunikacja równorzędna
titleSuffix: Azure
description: Przewodnik po partnerze usługi Komunikacja równorzędna
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 28aa9f921de64fcc838935cf65b64a63280ddf16
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774629"
---
# <a name="peering-service-partner-walkthrough"></a>Przewodnik po partnerze usługi Komunikacja równorzędna

W tej sekcji opisano kroki, które dostawca musi wykonać, aby włączyć bezpośrednią komunikację równorzędną dla usługi komunikacji równorzędnej.

## <a name="create-direct-peering-connection-for-peering-service"></a>Utwórz bezpośrednie połączenie komunikacji równorzędnej dla usługi Komunikacja równorzędna
Dostawcy usług mogą rozszerzyć ich zasięg geograficzny przez utworzenie nowej bezpośredniej komunikacji równorzędnej obsługującej usługę komunikacji równorzędnej. Aby to osiągnąć,
1. Zostań partnerem usługi komunikacji równorzędnej, jeśli nie została jeszcze
1. Postępuj zgodnie z instrukcjami, aby [utworzyć lub zmodyfikować bezpośrednią komunikację równorzędną przy użyciu portalu](howto-direct-portal.md). Upewnij się, że spełnia ono wymagania dotyczące wysokiej dostępności.
1. Następnie wykonaj kroki, aby [włączyć usługę komunikacji równorzędnej w bezpośredniej komunikacji równorzędnej przy użyciu portalu](howto-peering-service-portal.md).

## <a name="use-legacy-direct-peering-conection-for-peering-service"></a>Użyj starszej bezpośredniej komunikacji równorzędnej połączeń dla usługi komunikacji równorzędnej
Jeśli masz starszą bezpośrednią komunikację równorzędną, która ma być używana do obsługi komunikacji równorzędnej,
1. Jeśli nie jest, Zostań partnerem usługi komunikacji równorzędnej.
1. Postępuj zgodnie z instrukcjami, aby [skonwertować starszą bezpośrednią komunikację równorzędną do zasobu platformy Azure przy użyciu portalu](howto-legacy-direct-portal.md). W razie potrzeby Zamów dodatkowe obwody w celu spełnienia wymagań dotyczących wysokiej dostępności.
1. Następnie wykonaj kroki, aby [włączyć usługę komunikacji równorzędnej w bezpośredniej komunikacji równorzędnej przy użyciu portalu](howto-peering-service-portal.md).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [zasadach komunikacji równorzędnej](https://peering.azurewebsites.net/peering).
* Aby dowiedzieć się więcej o krokach konfigurowania bezpośredniej komunikacji równorzędnej z firmą Microsoft, postępuj zgodnie ze wskazówkami dotyczącymi [komunikacji równorzędnej](walkthrough-direct-all.md)
* Aby poznać kroki konfigurowania komunikacji równorzędnej programu Exchange z firmą Microsoft, postępuj zgodnie ze wskazówkami dotyczącymi [komunikacji równorzędnej programu Exchange](walkthrough-exchange-all.md).