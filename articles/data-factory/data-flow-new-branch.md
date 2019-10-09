---
title: Azure Data Factory mapowanie nowej gałęzi przepływu danych
description: Azure Data Factory mapowanie nowej gałęzi przepływu danych
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 3f443396627a4bbaba2a3eeb0a3ac05cc1597c85
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029291"
---
# <a name="azure-data-factory-mapping-data-flow-new-branch-transformation"></a>Azure Data Factory mapowanie nowej gałęzi przepływu danych



(media/data-flow/menu.png "Menu") ![opcji gałęzi]

Rozgałęzienie zajmie bieżący strumień danych w przepływie danych i zreplikuje je do innego strumienia. Użyj nowej gałęzi, aby wykonać wiele zestawów operacji i transformacji dla tego samego strumienia danych.

Przykład: przepływ danych ma transformację źródłową z wybranym zestawem kolumn i konwersji typów danych. Następnie należy umieścić kolumnę pochodną bezpośrednio po tym źródle. W kolumnie pochodnej utworzysz nowe pole, które łączy imię i nazwisko, aby utworzyć nowe pole "imię i nazwisko".

Można traktować ten nowy strumień z zestawem przekształceń i ujściam w jednym wierszu i użyć nowej gałęzi, aby utworzyć kopię tego strumienia, w którym można przekształcić te same dane z innym zestawem transformacji. Przez transformacje skopiowane dane w oddzielnym rozgałęzieniu można następnie odejść te dane do osobnej lokalizacji.

> [!NOTE]
> "Nowa gałąź" będzie wyświetlana tylko jako akcja w menu + przekształcenia, gdy istnieje kolejna transformacja, która następuje po bieżącej lokalizacji, w której próbujesz utworzyć gałąź. oznacza to, że na końcu nie zostanie wyświetlona opcja "Nowa gałąź", dopóki nie dodasz kolejnej transformacji po zaznaczeniu

Gałąź ![rozgałęzienia](media/data-flow/branch2.png "2")
