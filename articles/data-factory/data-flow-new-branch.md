---
title: Wiele gałęzi w przepływie danych mapowania
description: Replikowanie strumieni danych w przepływie danych mapowania z wieloma gałęziami
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/08/2020
ms.openlocfilehash: 71fb9f1ba9952be0e6b3910dd1079aa6d3c0482d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834504"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>Tworzenie nowej gałęzi w przepływie danych mapowania

Dodaj nową gałąź, aby wykonać wiele zestawów operacji i przekształceń względem tego samego strumienia danych. Dodawanie nowej gałęzi jest przydatne, gdy chcesz użyć tego samego źródła dla wielu pochłaniacze lub do samodzielnego łączenia danych razem.

Nową gałąź można dodać z listy transformacji podobne do innych przekształceń. **Nowa gałąź** będzie dostępna tylko jako akcja, gdy istnieje istniejąca transformacja po transformacji, którą próbujesz rozgałęzić.

![Dodawanie nowej gałęzi](media/data-flow/new-branch2.png "Dodawanie nowej gałęzi")

W poniższym przykładzie przepływ danych odczytuje dane podróży taksówką. Wymagane jest dane wyjściowe zagregowane zarówno przez dzień, jak i przez dostawcę. Zamiast tworzyć dwa oddzielne przepływy danych, które odczytują z tego samego źródła, można dodać nową gałąź. W ten sposób obie agregacje mogą być wykonywane jako część tego samego przepływu danych. 

![Dodawanie nowej gałęzi](media/data-flow/new-branch.png "Dodawanie nowej gałęzi")
