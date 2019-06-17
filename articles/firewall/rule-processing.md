---
title: Logika Azure przetwarzania reguły zapory
description: Więcej informacji na temat reguł zapory usługi Azure przetwarzanie logiki
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/27/2018
ms.author: victorh
ms.openlocfilehash: 12d86793c0d75413559aad77c558c4adb7ac91af
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64681587"
---
# <a name="azure-firewall-rule-processing-logic"></a>Logika Azure przetwarzania reguły zapory
Zapora systemu Azure ma reguły translatora adresów Sieciowych, reguł sieci i reguły aplikacji. Reguły są przetwarzane zgodnie z typem reguły.


## <a name="network-rules-and-applications-rules"></a>Reguły sieciowych i reguł aplikacji 
Reguły sieciowych są zastosowane reguły pierwszy, a następnie aplikacji. Reguły są Trwa przerywanie wykonywania. Dlatego jeśli w zasadach sieci zostanie znalezione dopasowanie, reguł aplikacji nie są przetwarzane.  Jeśli nie zostanie dopasowana reguła sieci i jeśli protokół pakietu to HTTP/HTTPS, pakiet zostanie oceniony przez reguły aplikacji. Jeśli nadal nie zostanie znalezione dopasowanie, pakiet zostaną ocenione względem kolekcji reguł infrastruktury. Jeśli wciąż nie zostanie znalezione dopasowanie, pakiet zostanie domyślnie odrzucony.

## <a name="nat-rules"></a>Reguły translatora adresów sieciowych
Połączenie przychodzące można włączyć, konfigurując przeznaczenia sieci adres tłumaczenia (DNAT), zgodnie z opisem w [samouczka: Filtrowanie ruchu przychodzącego za pomocą DNAT zapory platformy Azure przy użyciu witryny Azure portal](tutorial-firewall-dnat.md). Najpierw stosowane są reguły DNAT. Jeśli zostanie znalezione dopasowanie, niejawne odpowiednią regułę sieci zezwalającą na ruch przetłumaczonych zostanie dodany. Aby przesłonić to zachowanie, jawnie dodaj kolekcję reguł sieci z regułami odmowy zgodnymi z przetłumaczonym ruchem. Brak reguł aplikacji są stosowane w przypadku tych połączeń.


## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [wdrażanie i konfigurowanie zapory usługi Azure](tutorial-firewall-deploy-portal.md).