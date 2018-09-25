---
title: Rozwiń przekształcania JSON przy użyciu usługi Azure Machine Learning Workbench
description: Dokument referencyjny dotyczący przekształcenia "Rozwiń JSON"
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc, reference
ms.topic: article
ms.date: 09/14/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 0a5cbca114b220686d656f93edb00a199e3cbeeb
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46989825"
---
# <a name="expand-json-transformation"></a>Rozwiń przekształcania JSON

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


**Rozwiń JSON** przekształcenie umożliwia użytkownikom rozszerzyć istniejącą kolumnę, która zawiera prawidłowy tekst JSON na wiele kolumn.

## <a name="how-to-perform-this-transformation"></a>Jak przeprowadzić takie przekształcenie

Wykonaj następujące kroki, aby wykonywać przekształcenia:
1. Wybierz kolumny źródłowej, która zawiera tekst JSON.
2. Wybierz **rozwiń JSON** z **przekształca** menu. Lub kliknij prawym przyciskiem myszy nagłówek kolumny źródłowej, a następnie wybierz pozycję **rozwiń JSON** z menu kontekstowego. 
3. Kliknij przycisk **OK**. 
 
Obok kolumny źródłowej są dodawane nowe kolumny. Te kolumny zawierają właściwości udoskonalanie hierarchii w tekście JSON. Właściwość klucza, jeśli jest obecny, służy do tworzenia nazwy odpowiadającej jej kolumny. Właściwości zagnieżdżone są zachowywane jako tekstu JSON tego użytkownika iteracyjne można rozwinąć lub odrzucić zgodnie z potrzebami.

## <a name="examples"></a>Przykłady

Kolumna źródłowa *klienta* podzielonego na dwie kolumny *Customer.Name* i *Customer.Phone*.

| Klient                                                | Customer.Name   | Customer.Phone |
|---------------------------------------------------------|-----------------|----------------|
| {"Name": "Carrie Dodson", "Phone": "123-4567-890"}   | Carrie Dodson   | 123-4567-890   |
| {"Name": "Richardson Robledo", "Phone": "456-7890-123"} | Robledo Richardson | 456-7890-123   |

