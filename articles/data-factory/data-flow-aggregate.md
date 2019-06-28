---
title: Agreguj transformacji w mapowanie przepływu danych — usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak i agregowanie danych na dużą skalę w usłudze Azure Data Factory przy użyciu transformacji agregacji przepływu mapowanie danych.
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 21135b26d4bc840b3fcb091e675e5e6bd24d8548
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2019
ms.locfileid: "67312118"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>Przekształcenie agregacji w mapowanie przepływu danych 

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Przekształcenie agregacji jest, gdzie należy zdefiniować agregacji kolumny w strumienie danych. Konstruktor wyrażeń można zdefiniować różne typy agregacji, takich jak SUM, MIN, MAX i COUNT, które mogą być grupowane według istniejących lub obliczanych kolumn.

## <a name="group-by"></a>Grupuj według
Wybierz istniejącą kolumnę lub Utwórz nową kolumnę obliczaną do użytku jako grupa by — klauzula usługi agregacji. Aby użyć istniejącej kolumny, wybierz odpowiednie kolumny z listy rozwijanej. Aby utworzyć nową kolumnę obliczaną, umieść kursor nad klauzuli, a następnie kliknij przycisk "Kolumna obliczana". Spowoduje to otwarcie [Konstruktor wyrażeń przepływu danych](concepts-data-flow-expression-builder.md). Po utworzeniu kolumny obliczanej, wprowadź nazwę kolumny danych wyjściowych poniżej pola "Nazwa jako". Jeśli chcesz dodać dodatkowe grupy przez klauzulę, umieść kursor nad istniejącej klauzuli i kliknij przycisk "+".

![Przekształcenie agregacji grupy za pomocą ustawień](media/data-flow/agg.png "grupy przekształcenie agregacji za pomocą ustawień")

> [!NOTE]
> Group by — klauzula jest opcjonalny w przekształcenie agregacji.

## <a name="aggregate-column"></a>Agreguj kolumnę 
Wybierz kartę "Agregacji", aby zbudować wyrażenia agregacji. Można albo wybierz istniejącą kolumnę i zastąpienie wartości agregacji lub utworzyć nowe pole pod nową nazwą. Wyrażenie agregacji został wprowadzony w pole po prawej stronie obok nazwy selektora kolumn. Aby edytować wyrażenie, kliknij pole tekstowe, aby otworzyć Kreatora wyrażeń. Aby dodać dodatkowe agregacji, umieść kursor nad istniejących wyrażenie, a następnie kliknij przycisk "+" Aby utworzyć nową kolumnę agregacji lub [wzorzec kolumny](concepts-data-flow-column-pattern.md).

![Agreguj przekształcenie agregacji ustawienia](media/data-flow/agg2.png "ustawienia agregacji przekształcenie agregacji")

> [!NOTE]
> Każde wyrażenie agregacji musi zawierać co najmniej jedną funkcję agregacji.

> [!NOTE]
> W trybie debugowania Konstruktor wyrażeń nie może utworzyć podglądów danych za pomocą funkcji agregujących. Aby wyświetlić podgląd danych dla przekształceń agregacji, należy zamknąć Konstruktor wyrażeń i wyświetlanie danych za pomocą karty "Podgląd danych".

## <a name="next-steps"></a>Kolejne kroki

-Definiowanie okienkowych agregacji za pomocą [przekształcania okna](data-flow-window.md)