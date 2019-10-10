---
title: Przekształcenie zmiany wiersza Azure Data Factory mapowania przepływu danych
description: Jak zaktualizować obiekt docelowy bazy danych przy użyciu Azure Data Factory mapowanie zmian w przekształceniu wierszy
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: e2cd69d5977b8ad1d9be2a71a006579fe3abfd23
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69971255"
---
# <a name="azure-data-factory-alter-row-transformation"></a>Azure Data Factory zmodyfikować przekształcenia wierszy

Użyj przekształcenia ALTER Row, aby ustawić zasady wstawiania, usuwania, aktualizacji i upsert w wierszach. Możesz dodać warunki "jeden do wielu" jako wyrażenia. Warunki te należy określić w kolejności priorytetu, ponieważ każdy wiersz zostanie oznaczony przy użyciu zasad odpowiadających pierwszemu wyrażeniu. Każdy z tych warunków może spowodować Wstawianie, aktualizowanie, usuwanie lub upserted wierszy (lub wierszy). Polecenie ALTER Row może generować do bazy danych zarówno akcje DDL & DML.

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Zmień ustawienia wiersza](media/data-flow/alter-row1.png "Zmień ustawienia wiersza")

> [!NOTE]
> Przekształcenia ALTER Row będą działać tylko w ujściach bazy danych w przepływie danych. Akcje przypisane do wierszy (INSERT, Update, DELETE, upsert) nie będą wykonywane podczas sesji debugowania. Należy dodać zadanie wykonywania przepływu danych do potoku i użyć debugowania potoku lub wyzwalaczy w celu wymuszenia zasad ALTER Row w tabelach bazy danych.

## <a name="indicate-a-default-row-policy"></a>Wskazywanie domyślnych zasad wiersza

Tworzenie przekształcenia ALTER Row i Określanie zasad wiersza z warunkiem `true()`. Każdy wiersz, który nie spełnia żadnych wcześniej zdefiniowanych wyrażeń, zostanie oznaczony dla określonych zasad wiersza. Domyślnie każdy wiersz, który nie spełnia żadnego wyrażenia warunkowego, zostanie oznaczony jako dla `Insert`.

![Zmień wiersz o jedną zasadę](media/data-flow/alter-row4.png "Zmień wiersz o jedną zasadę")

> [!NOTE]
> Aby oznaczyć wszystkie wiersze z jedną zasadą, można utworzyć warunek dla tych zasad i określić warunek jako `true()`.

## <a name="view-policies"></a>Wyświetl zasady

Włącz tryb debugowania przepływu danych, aby wyświetlić wyniki zasad ALTER Row w okienku Podgląd danych. Wykonanie polecenia ALTER Row w trybie debugowania przepływu danych nie spowoduje utworzenia akcji DDL ani DML względem obiektu docelowego. Aby te akcje były wykonywane, należy wykonać przepływ danych wewnątrz działania wykonywania przepływu danych w ramach potoku.

![Zasady ALTER Row](media/data-flow/alter-row3.png "Zasady ALTER Row")

Pozwoli to sprawdzić i wyświetlić stan każdego wiersza w oparciu o Twoje warunki. Istnieje ikona reprezentuje dla każdej akcji INSERT, Update, DELETE i Upsert, która będzie miała miejsce w przepływie danych, wskazująca, która akcja będzie wykonywana po wykonaniu przepływu danych w potoku.

## <a name="sink-settings"></a>Ustawienia ujścia

Aby zmiana wiersza działała, musisz mieć typ ujścia bazy danych. W ustawieniach ujścia należy ustawić wszystkie akcje odpowiadające warunkom zmiany wiersza, które mają być dozwolone.

![Zmień ujścia wierszy](media/data-flow/alter-row2.png "Zmień ujścia wierszy")

Domyślnym zachowaniem w przepływie danych ADF z ujściami bazy danych jest wstawianie wierszy. Jeśli chcesz zezwolić na aktualizacje, upserts i usunięcia, należy również zaznaczyć te pola w zlewie, aby zezwolić na wykonywanie akcji.

> [!NOTE]
> Jeśli Wstaw, aktualizacje lub upserts modyfikują schemat tabeli docelowej w ujścia, przepływ danych zakończy się niepowodzeniem. Aby zmodyfikować schemat docelowy w bazie danych, należy wybrać opcję "Utwórz ponownie tabelę" w zlewie. Spowoduje to usunięcie i ponowne utworzenie tabeli przy użyciu nowej definicji schematu.

## <a name="next-steps"></a>Następne kroki

Po przekształceniu zmiany wiersza możesz chcieć [zaujścia danych do docelowego magazynu danych](data-flow-sink.md).
