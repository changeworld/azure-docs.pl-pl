---
title: Azure Data Factory mapowanie nowej gałęzi przepływu danych
description: Azure Data Factory mapowanie nowej gałęzi przepływu danych
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: de8cb74d788e3ca7599f226e4204c4b09112e70c
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387220"
---
# <a name="azure-data-factory-mapping-data-flow-new-branch-transformation"></a>Azure Data Factory mapowanie nowej gałęzi przepływu danych



![Opcje gałęzi](media/data-flow/menu.png "DodajMenu")

Rozgałęzienie zajmie bieżący strumień danych w przepływie danych i zreplikuje je do innego strumienia. Użyj nowej gałęzi, aby wykonać wiele zestawów operacji i transformacji dla tego samego strumienia danych.

Przykład: przepływ danych ma transformację źródłową z wybranym zestawem kolumn i konwersji typów danych. Następnie należy umieścić kolumnę pochodną bezpośrednio po tym źródle. W kolumnie pochodnej utworzysz nowe pole, które łączy imię i nazwisko, aby utworzyć nowe pole "imię i nazwisko".

Można traktować ten nowy strumień z zestawem przekształceń i ujściam w jednym wierszu i użyć nowej gałęzi, aby utworzyć kopię tego strumienia, w którym można przekształcić te same dane z innym zestawem transformacji. Przez transformacje skopiowane dane w oddzielnym rozgałęzieniu można następnie odejść te dane do osobnej lokalizacji.

> [!NOTE]
> "Nowa gałąź" będzie wyświetlana tylko jako akcja w menu + przekształcenia, gdy istnieje kolejna transformacja, która następuje po bieżącej lokalizacji, w której próbujesz utworzyć gałąź. oznacza to, że na końcu nie zostanie wyświetlona opcja "Nowa gałąź", dopóki nie dodasz kolejnej transformacji po zaznaczeniu

![Gałęzi](media/data-flow/branch2.png "Gałąź 2")
