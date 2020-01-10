---
title: Wiele gałęzi w mapowaniu przepływu danych
description: Replikowanie strumieni danych w mapowaniu przepływu danych z wieloma gałęziami
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/08/2020
ms.openlocfilehash: 71fb9f1ba9952be0e6b3910dd1079aa6d3c0482d
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834504"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>Tworzenie nowej gałęzi w mapowaniu przepływu danych

Dodaj nową gałąź, aby wykonać wiele zestawów operacji i transformacji dla tego samego strumienia danych. Dodawanie nowej gałęzi jest przydatne, gdy chcesz użyć tego samego źródła dla wielu zleweń lub do samodzielnego łączenia danych.

Nową gałąź można dodać z listy transformacji podobnej do innych transformacji. **Nowa gałąź** będzie dostępna tylko jako akcja, gdy istnieje istniejąca transformacja po transformacji, którą próbujesz rozgałęzić.

![Dodawanie nowej gałęzi](media/data-flow/new-branch2.png "Dodawanie nowej gałęzi")

W poniższym przykładzie przepływ danych odczytuje dane podróży z taksówką. Dane wyjściowe zagregowane według dnia i dostawcy są wymagane. Zamiast tworzyć dwa oddzielne przepływy danych odczytane z tego samego źródła, można dodać nową gałąź. W ten sposób obie agregacje mogą być wykonywane w ramach tego samego przepływu danych. 

![Dodawanie nowej gałęzi](media/data-flow/new-branch.png "Dodawanie nowej gałęzi")
