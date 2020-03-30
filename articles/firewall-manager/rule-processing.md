---
title: Logika przetwarzania reguł usługi Azure Firewall
description: Dowiedz się więcej o logice przetwarzania reguł zapory platformy Azure
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: victorh
ms.openlocfilehash: 74e58c316651a1604984ac14c70a3a65d46d6d9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73518205"
---
# <a name="azure-firewall-rule-processing-logic"></a>Logika przetwarzania reguł usługi Azure Firewall

Zapora azure ma reguły NAT, reguły sieciowe i reguły aplikacji. Reguły są przetwarzane zgodnie z typem reguły.

## <a name="network-rules-and-applications-rules"></a>Reguły sieci i reguły aplikacji

Najpierw stosowane są reguły sieciowe, a następnie reguły aplikacji. Zasady wygasają. Jeśli więc dopasowanie zostanie znalezione w regułach sieciowych, reguły aplikacji nie są przetwarzane.  Jeśli nie zostanie dopasowana reguła sieci i jeśli protokół pakietu to HTTP/HTTPS, pakiet zostanie oceniony przez reguły aplikacji. Jeśli nadal nie zostanie znaleziony nie znaleziono dopasowania, pakiet jest oceniany względem kolekcji reguł infrastruktury. Jeśli wciąż nie zostanie znalezione dopasowanie, pakiet zostanie domyślnie odrzucony.

## <a name="nat-rules"></a>Reguły translatora i sieci zuchwów

Łączność przychodzącą można włączyć, konfigurując translację adresów sieciowych (DNAT) zgodnie z opisem w [samouczku: Filtrowanie ruchu przychodzącego za pomocą usługi Azure Firewall DNAT przy użyciu witryny Azure portal](../firewall/tutorial-firewall-dnat.md). Najpierw stosuje się reguły DNAT. Jeśli zostanie znalezione dopasowanie, zostanie dodana niejawna odpowiednia reguła sieciowa zezwalana na przetłumaczony ruch. Aby przesłonić to zachowanie, jawnie dodaj kolekcję reguł sieci z regułami odmowy zgodnymi z przetłumaczonym ruchem. Dla tych połączeń nie są stosowane żadne reguły aplikacji.

## <a name="inherited-rules"></a>Reguły dziedziczone

Kolekcje reguł sieciowych dziedziczone z zasad nadrzędnych są zawsze traktowane priorytetowo powyżej kolekcji reguł sieciowych, które są zdefiniowane jako część nowej zasady. Ta sama logika ma również zastosowanie do kolekcji reguł aplikacji. Jednak kolekcje reguł sieciowych są zawsze przetwarzane przed kolekcjami reguł aplikacji, niezależnie od dziedziczenia.

Domyślnie zasady dziedziczy jego nadrzędnego trybu analizy zagrożeń zasad. Można to zastąpić, ustawiając tryb analizy zagrożeń na inną wartość na stronie ustawień zasad. Jest możliwe tylko do zastąpienia z bardziej rygorystyczne wartości. Na przykład jeśli zasady nadrzędne są ustawione tylko na *Alert,* możesz skonfigurować tę zasadę lokalną na *Alert i odmów,* ale nie można jej wyłączyć.

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej o usłudze Azure Firewall Manager Preview](overview.md)