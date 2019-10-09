---
title: Węzeł odwołania do przepływu danych mapowania Azure Data Factory
description: Data Factory przepływ danych doda węzeł odwołania dla sprzężeń, wyszukiwań, Unii
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 4ed17114cc0ce586c68c5b3e087acffdb82ea96c
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030494"
---
# <a name="mapping-data-flow-reference-node"></a>Mapowanie węzła odwołania przepływu danych



(media/data-flow/referencenode.png "Węzeł") odwołania ![węzła odwołania]

Węzeł odwołania jest automatycznie dodawany do kanwy, co oznacza, że węzeł, do którego jest dołączony, odwołuje się do innego istniejącego węzła na kanwie. Należy traktować węzeł odwołania jako wskaźnik lub odwołanie do innej transformacji przepływu danych.

Na przykład: w przypadku sprzęgania lub złożenia więcej niż jednego strumienia danych Kanwa przepływu danych może dodać węzeł odwołania, który odzwierciedla nazwę i ustawienia niepodstawowego strumienia przychodzącego.

Nie można przenieść ani usunąć węzła odwołania. Można jednak kliknąć w węźle, aby zmodyfikować ustawienia transformacji źródłowej.

Reguły interfejsu użytkownika, które określają, kiedy przepływ danych dodaje węzeł odniesienia, są oparte na dostępne miejsce i odstępy w pionie między wierszami.
