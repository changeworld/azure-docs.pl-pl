---
title: "Rozwiń węzeł przekształcania JSON przy użyciu usługi Azure Machine Learning Workbench"
description: "W dokumencie referencyjnym transformacji \"Rozwiń JSON\""
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, reference
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: 21de94d2d0d3cc12aabcb8e9e8b0eec39b0a2710
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2018
---
# <a name="expand-json-transformation"></a>Rozwiń węzeł przekształcania JSON
**Rozwiń JSON** przekształcenia umożliwia użytkownikom rozwiń istniejąca kolumna zawiera nieprawidłowy tekst JSON do wielu kolumn.

## <a name="how-to-perform-this-transformation"></a>Jak wykonać tego przekształcenia

Wykonaj następujące kroki do wykonania tej transformacji:
1. Wybierz kolumny źródłowej, która zawiera tekst JSON.
2. Wybierz **rozwiń JSON** z **przekształca** menu. Lub kliknij prawym przyciskiem myszy nagłówek kolumny źródłowej i wybierz **rozwiń JSON** z menu kontekstowego. 
3. Kliknij przycisk **OK**. 
 
Obok kolumny źródłowej są dodawane nowe kolumny. Te kolumny zawierają właściwości z jednego poziomu hierarchii w tekście JSON. Klucz właściwości, jeśli jest obecny, służy do tworzenia nazwa odpowiedniej kolumny. Właściwości zagnieżdżone są zachowywane jako tekstu JSON użytkownika wielokrotnie powtarzane można rozwinąć lub odrzucić zgodnie z potrzebami.

## <a name="examples"></a>Przykłady

Columnn źródła *klienta* jest rozwinięta w dwóch kolumnach *Customer.Name* i *Customer.Phone*.

| Klient                                                | Customer.Name   | Customer.Phone |
|---------------------------------------------------------|-----------------|----------------|
| {"Name": "Carrie Dodson", "Phone": "123-4567-890"}   | Carrie Dodson   | 123-4567-890   |
| {"Name": "Leonard Robledo", "Phone": "456-7890-123"} | Leonard Robledo | 456-7890-123   |

