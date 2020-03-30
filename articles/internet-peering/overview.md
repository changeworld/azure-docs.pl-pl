---
title: Konfigurowanie komunikacji równorzędnej w firmie Microsoft
titleSuffix: Azure
description: Omówienie komunikacji równorzędnej
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: overview
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 576bc3e37711851acd7d6c7ac811a10e40080710
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75908917"
---
# <a name="internet-peering-overview"></a>Omówienie komunikacji równorzędnej w Internecie

Komunikacja równorzędna to połączenie między globalną siecią firmy Microsoft (AS8075) a siecią użytkownika w celu wymiany ruchu internetowego z/do usług online firmy Microsoft i usług Microsoft Azure. Przewoźnicy lub usługodawcy mogą zażądać połączenia z firmą Microsoft w dowolnej z naszych lokalizacji brzegowych. Każde żądanie jest sprawdzane przez firmę Microsoft, aby upewnić się, że jest zgodne z naszymi zasadami komunikacji równorzędnej. Komunikację równorzędną można skonfigurować w sieci Firmy Microsoft na dwa sposoby:

* **Bezpośrednia komunikacja równorzędna:**

    Komunikacja równorzędna jest nawiązywalna za pośrednictwem bezpośrednich połączeń fizycznych między siecią Microsoft w witrynie Microsoft Edge a siecią. Sesje Protokołu BGP są konfigurowane w tych połączeniach zgodnie z naszymi zasadami routingu i przy użyciu wstępnie wynegocjowanej umowy. Jest to również określane jako PNI.

* **Komunikacja równorzędna programu Exchange:**

    Odnosi się to do standardowych publicznych połączeń równorzędnych w wymianach internetowych (IX). Fizyczne połączenia między siecią Firmy Microsoft a siecią są za pośrednictwem sieci szkieletowej przełączników obsługiwanych przez IX. Sesje BGP są konfigurowane przy użyciu miejsca IP dostarczonego przez IX.

## <a name="benefits-of-peering-with-microsoft"></a>Korzyści z komunikacji równorzędnej z firmą Microsoft
* Obniż koszty transportu, dostarczając ruch firmy Microsoft za pomocą komunikacji równorzędnej z firmą Microsoft.
* Zwiększ wydajność klientów, zmniejszając przeskoki sieciowe i opóźnienia w sieci Microsoft Edge.
* Chroń ruch klientów przed awariami w sieci lub sieci dostawcy transportu, korzystając z komunikacji z firmą Microsoft w nadmiarowych lokalizacjach.
* Poznaj metryki wydajności dotyczące połączeń komunikacji równorzędnej i wykorzystaj szczegółowe informacje do rozwiązywania problemów z siecią.

## <a name="benefits-of-using-azure-to-set-up-peering"></a>Korzyści z używania platformy Azure do konfigurowania komunikacji równorzędnej

Możesz poprosić o komunikację równorzędnych z firmą Microsoft przy użyciu programu Azure PowerShell lub portalu. Konfiguracja komunikacji równorzędnej w ten sposób jest zarządzana jako zasób platformy Azure i zapewnia następujące korzyści:
* Uproszczone i zautomatyzowane kroki konfigurowania komunikacji równorzędnej z firmą Microsoft i zarządzania nimi.
* Szybki i łatwy sposób, aby wyświetlić i zarządzać wszystkimi peerings w jednym miejscu.
* Śledź dane o stanie i przepustowości dla wszystkich połączeń.
* Możesz użyć tej samej subskrypcji, aby uzyskać dostęp do usług w chmurze azure.

Jeśli masz już ustalone komunikacji równorzędnej z firmą Microsoft, są one określane jako **starsze peerings**. Możesz zarządzać takimi elementami komunikacji równorzędnej, jak zasób platformy Azure, aby skorzystać z powyższych korzyści. Aby przesłać nowe żądanie komunikacji równorzędnej lub przekonwertować starsze komunikacji równorzędnej do zasobu platformy Azure, wykonaj łącza w sekcji **Następne kroki** poniżej.

## <a name="peering-policy"></a>Zasady komunikacji równorzędnej
Firma Microsoft ma selektywne, ale ogólnie otwarte zasady komunikacji równorzędnej. Elementy równorzędne są wybierane na podstawie wydajności, możliwości i w przypadku obopólnych korzyści i podlegają pewnym wymogom technicznym, handlowym i prawnym. Aby uzyskać szczegółowe informacje, zobacz [zasady komunikacji równorzędnej](policy.md).

## <a name="faq"></a>Najczęściej zadawane pytania
Aby uzyskać często zadawane pytania dotyczące komunikacji równorzędnej, zobacz [Peering internet — często zadawane pytania](faqs.md).

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o krokach konfigurowania komunikacji bezpośredniej z firmą Microsoft, postępuj zgodnie z [wskazówki dotyczące komunikacji równorzędnej](walkthrough-direct-all.md)
* Aby dowiedzieć się więcej o krokach konfigurowania komunikacji równorzędnej programu Exchange z firmą Microsoft, postępuj zgodnie z [wskazówki dotyczące komunikacji równorzędnej programu Exchange](walkthrough-exchange-all.md)
* Poznaj inne kluczowe [możliwości sieciowe](https://docs.microsoft.com/azure/networking/networking-overview) platformy Azure.
