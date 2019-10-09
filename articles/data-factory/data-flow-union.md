---
title: Azure Data Factory mapowanie nowej gałęzi przepływu danych
description: Azure Data Factory mapowanie nowej gałęzi przepływu danych
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 35d5b2250cb5f2f5bd5b3a0073dc2e3c655ceccb
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029921"
---
# <a name="mapping-data-flow-union-transformation"></a>Mapowanie przekształceń Unii danych



Unia łączy wiele strumieni danych w jeden, wraz z Unią SQL dla tych strumieni jako nowe dane wyjściowe z transformacji Union. Cały schemat z każdego strumienia wejściowego zostanie połączony wewnątrz przepływu danych, bez konieczności posiadania klucza sprzężenia.

Można połączyć n-liczbowe strumienie w tabeli ustawień, wybierając ikonę "+" obok każdego skonfigurowanego wiersza, w tym zarówno dane źródłowe, jak i strumienie z istniejących transformacji w przepływie danych.

Unia ![transformacji Union](media/data-flow/union.png "")

W takim przypadku można połączyć różne metadane z wielu źródeł (w tym przykładzie trzy różne pliki źródłowe) i połączyć je w jeden strumień:

![Przekształcenie Unii — Omówienie](media/data-flow/union111.png "Unii 1")

Aby to osiągnąć, Dodaj dodatkowe wiersze w ustawieniach Unii, dołączając wszystkie źródła, które chcesz dodać. Nie ma potrzeby wspólnego wyszukiwania lub klucza sprzężenia:

(media/data-flow/unionsettings.png "Ustawienia Unii") dla ![ustawień transformacji Union]

Jeśli ustawisz transformację wybraną po Unii, będziesz mieć możliwość zmiany nazwy nakładających się pól lub pól, które nie zostały nazwane ze źródeł bez nagłówka. Kliknij pozycję "Zbadaj", aby zobaczyć kolumny łącz łączne z 132 w tym przykładzie z trzech różnych źródeł:

Unia ![końcowa przekształcenia Unii](media/data-flow/union333.png "3")

## <a name="name-and-position"></a>Nazwa i pozycja

Po wybraniu opcji "Sumuj według nazwy" Każda wartość kolumny zostanie połączona z odpowiednią kolumną z każdego źródła, z nowym połączonym schematem metadanych.

Jeśli wybierzesz pozycję "Union by position", każda wartość kolumny zostanie połączona do oryginalnej pozycji z każdego odpowiadającego źródła, co spowoduje powstanie nowego połączonego strumienia danych, w którym dane z każdego źródła są dodawane do tego samego strumienia:

(media/data-flow/unionoutput.png "Dane wyjściowe Unii") ![wyjściowej]Union

## <a name="next-steps"></a>Następne kroki

Eksploruj podobne przekształcenia, w tym [Join](data-flow-join.md) i [EXISTS](data-flow-exists.md).
