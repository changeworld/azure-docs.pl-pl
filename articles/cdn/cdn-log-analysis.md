---
title: Analizować wzorce użycia usługi Azure CDN | Dokumentacja firmy Microsoft
description: W tym artykule opisano różne typy raportów analizy, które są dostępne dla produkty Azure CDN.
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: 95e18b3c-b987-46c2-baa8-a27a029e3076
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: rli; v-deasim
ms.openlocfilehash: 61fbe6e29df787048a9694138d3c9095f5cba76b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33764897"
---
# <a name="analyze-azure-cdn-usage-patterns"></a>Analizowanie wzorców użycia usługi Azure CDN

Po włączeniu sieci CDN w warstwie aplikacji monitorowania użycia sieci CDN w warstwie, Sprawdź kondycję firmy i rozwiązać potencjalne problemy. Usługi Azure CDN zapewnia te możliwości w następujący sposób: 

## <a name="core-analytics-via-azure-diagnostic-logs"></a>Podstawowa analiza za pomocą dzienników diagnostycznych platformy Azure

Podstawowa analiza jest dostępna dla punktów końcowych usługi CDN dla wszystkich warstw cenowych. Dzienniki diagnostyczne platformy Azure umożliwiają podstawowa analiza mają być eksportowane do usługi Azure storage, centra zdarzeń lub Analiza dzienników Azure. Analiza dzienników Azure oferuje rozwiązanie o wykresy użytkownika można skonfigurować i dostosować. Aby uzyskać więcej informacji na temat dzienników diagnostycznych platformy Azure, zobacz [Azure dzienników diagnostycznych](cdn-azure-diagnostic-logs.md).

## <a name="verizon-core-reports"></a>Verizon podstawowych raportów

Jako użytkownik usługi Azure CDN z **Azure CDN Standard from Verizon** lub **Azure CDN Premium from Verizon** profilu, raportów core Verizon można wyświetlić w portalu uzupełniającym Verizon. Verizon podstawowych raportów jest dostępny za pośrednictwem **Zarządzaj** opcji w portalu Azure i oferuje wiele widoków i wykresy. Aby uzyskać więcej informacji, zobacz [raporty Core from Verizon](cdn-analyze-usage-patterns.md).

## <a name="verizon-custom-reports"></a>Raporty niestandardowe Verizon

Jako użytkownik usługi Azure CDN z **Azure CDN Standard from Verizon** lub **Azure CDN Premium from Verizon** profilu, raportów niestandardowych Verizon można wyświetlić w portalu uzupełniającym Verizon. Verizon niestandardowych raportów jest dostępny za pośrednictwem **Zarządzaj** opcji w portalu Azure. Pokazuje stronę niestandardowych raportów Verizon liczba trafień lub danych transferu dla każdego krawędzi CName należących do profilu usługi Azure CDN. Dane można przedstawić w rozbiciu stanu odpowiedzi HTTP kod lub pamięci podręcznej w dowolnym czasie. Aby uzyskać więcej informacji, zobacz [raporty niestandardowe from Verizon](cdn-verizon-custom-reports.md).

## <a name="azure-cdn-premium-from-verizon-reports"></a>Azure CDN Premium from Verizon raportów

Z **Azure CDN Premium from Verizon**, można także przejść do następujących raportów:
   * [Zaawansowane raporty HTTP](cdn-advanced-http-reports.md)
   * [Statystyki w czasie rzeczywistym](cdn-real-time-stats.md)
   * [Wydajność węzła brzegowego](cdn-edge-performance.md)

