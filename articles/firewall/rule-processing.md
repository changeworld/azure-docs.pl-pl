---
title: Logika Azure przetwarzania reguły zapory
description: Więcej informacji na temat reguł zapory usługi Azure przetwarzanie logiki
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/27/2018
ms.author: victorh
ms.openlocfilehash: 542682037a932a2e3b08c71b38b64b2694ad40f3
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2018
ms.locfileid: "47228369"
---
# <a name="azure-firewall-rule-processing-logic"></a>Logika Azure przetwarzania reguły zapory
Zapora systemu Azure ma reguły translatora adresów Sieciowych, reguł sieci i reguły aplikacji. Reguły są przetwarzane zgodnie z typem reguły.


## <a name="network-rules-and-applications-rules"></a>Reguły sieciowych i reguł aplikacji 
Reguły sieciowych są zastosowane reguły pierwszy, a następnie aplikacji. Reguły są Trwa przerywanie wykonywania. Dlatego jeśli w zasadach sieci zostanie znalezione dopasowanie, reguł aplikacji nie są przetwarzane.  Jeśli nie zostanie odnaleziony odpowiednik reguł sieci i Protokół pakietów jest HTTP/HTTPS, pakiet jest następnie oceniany przez zasady aplikacji. Jeśli nadal nie zostanie znalezione dopasowanie, pakiet zostaną ocenione względem kolekcji reguł infrastruktury. Jeśli nadal nie ma dopasowania, pakiet zostanie odrzucona przez domyślny.

## <a name="nat-rules"></a>Reguły translatora adresów sieciowych
Połączenie przychodzące można włączyć, konfigurując przeznaczenia sieci adres tłumaczenia (DNAT), zgodnie z opisem w [samouczek: filtrowanie ruchu przychodzącego za pomocą DNAT zapory platformy Azure przy użyciu witryny Azure portal](tutorial-firewall-dnat.md). Najpierw stosowane są reguły DNAT. Jeśli zostanie znalezione dopasowanie, niejawne odpowiednią regułę sieci zezwalającą na ruch przetłumaczonych zostanie dodany. Aby zmienić to zachowanie, należy jawnie dodając kolekcji reguł sieci przy użyciu reguł odmowy, zgodne przetłumaczone ruchu. Brak reguł aplikacji są stosowane w przypadku tych połączeń.


## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [wdrażanie i konfigurowanie zapory usługi Azure](tutorial-firewall-deploy-portal.md).