---
title: Mapowanie węzeł odniesienia przepływu danych w usłudze Azure Data Factory
description: Przepływ danych fabryki danych spowoduje dodanie węzła odniesienia sprzężenia, wyszukiwania, Unii
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 641375c2b848957ffc0f5ad092a28460d91b6690
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56213429"
---
# <a name="azure-data-factory-mapping-data-flow-reference-node"></a>Mapowanie węzeł odniesienia przepływu danych w usłudze Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Węzeł odwołania](media/data-flow/referencenode.png "węzeł odniesienia")

Węzeł odniesienia jest automatycznie dodawany do kanwy, aby oznaczają, że węzeł, który jest dołączony do odwołuje się do innego istniejącego węzła na kanwie. Pomyśl o węźle odwołania w postaci wskaźnik lub odwołanie do innego Przekształcanie przepływu danych.

Na przykład: Jeśli dołączysz lub Unii więcej niż jeden strumień danych, Kanwa przepływ danych może dodać węzeł odniesienia, która odzwierciedla nazwę i ustawienia innego niż podstawowy przychodzącego strumienia.

Węzeł odwołania nie przeniesiona lub usunięta. Kliknięcie pozwala modyfikować ustawienia przekształcenie pochodzące z węzłem.

Reguły interfejsu użytkownika, które kontrolują, gdy przepływ danych dodaje węzeł odniesienia są oparte na dostępne miejsce i pionowe odstępy między wierszami.
