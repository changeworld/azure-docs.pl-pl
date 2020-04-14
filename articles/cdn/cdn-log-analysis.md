---
title: Analizowanie wzorców użycia usługi Azure CDN | Dokumenty firmy Microsoft
description: W tym artykule opisano różne typy raportów analizy dostępnych dla produktów usługi Azure CDN.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: 95e18b3c-b987-46c2-baa8-a27a029e3076
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: allensu
ms.openlocfilehash: c78dff9f5258023d90100c70bd244e8e0d016d6f
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81253615"
---
# <a name="analyze-azure-cdn-usage-patterns"></a>Analizowanie wzorców użycia usługi Azure CDN

Po włączeniu sieci CDN dla aplikacji można monitorować użycie sieci CDN, sprawdzać kondycję dostawy i rozwiązywać potencjalne problemy. Usługa Azure CDN udostępnia te funkcje w następujący sposób: 

## <a name="core-analytics-via-azure-diagnostic-logs"></a>Podstawowa analiza za pomocą dzienników diagnostycznych platformy Azure

Analiza podstawowa jest dostępna dla punktów końcowych usługi CDN dla wszystkich warstw cenowych. Dzienniki diagnostyki platformy Azure umożliwiają eksportowanie podstawowych analiz do magazynu platformy Azure, centrów zdarzeń lub dzienników usługi Azure Monitor. Dzienniki usługi Azure Monitor oferują rozwiązanie z wykresami, które można konfigurować i dostosowywać przez użytkownika. Aby uzyskać więcej informacji na temat dzienników diagnostycznych platformy Azure, zobacz [Dzienniki diagnostyczne platformy Azure](cdn-azure-diagnostic-logs.md).

## <a name="verizon-core-reports"></a>Podstawowe raporty Verizon

Jako użytkownik usługi Azure CDN z **standardem usługi Azure CDN firmy Verizon** lub **Azure CDN Premium z** profilu Verizon możesz przeglądać podstawowe raporty Verizon w portalu uzupełniającym Verizon. Raporty podstawowe Verizon są dostępne za pośrednictwem opcji **Zarządzaj** z witryny Azure portal i oferuje wiele wykresów i widoków. Aby uzyskać więcej informacji, zobacz [Podstawowe raporty firmy Verizon](cdn-analyze-usage-patterns.md).

## <a name="verizon-custom-reports"></a>Raporty niestandardowe Verizon

Jako użytkownik usługi Azure CDN z **standardem usługi Azure CDN firmy Verizon** lub **Azure CDN Premium z** profilu Verizon możesz wyświetlać niestandardowe raporty Verizon w portalu uzupełniającym Verizon. Raporty niestandardowe Verizon są dostępne za pośrednictwem opcji **Zarządzaj** w witrynie Azure portal. Strona Raporty niestandardowe Verizon pokazuje liczbę trafień lub danych przesłanych dla każdego cname krawędzi należących do profilu usługi Azure CDN. Dane mogą być pogrupowane według kodu odpowiedzi HTTP lub stanu pamięci podręcznej w dowolnym okresie czasu. Aby uzyskać więcej informacji, zobacz [Raporty niestandardowe firmy Verizon](cdn-verizon-custom-reports.md).

## <a name="azure-cdn-premium-from-verizon-reports"></a>Raporty usługi Azure CDN Premium z verizon

Dzięki **usłudze Azure CDN Premium firmy Verizon**można również uzyskać dostęp do następujących raportów:
   * [Zaawansowane raporty HTTP](cdn-advanced-http-reports.md)
   * [Statystyki w czasie rzeczywistym](cdn-real-time-stats.md)
   * [Wydajność węzła brzegowego](cdn-edge-performance.md)

