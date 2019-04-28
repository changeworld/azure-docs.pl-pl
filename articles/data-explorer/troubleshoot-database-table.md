---
title: Błąd tworzenia lub usuwania bazy danych lub tabeli w Eksploratorze danych platformy Azure
description: W tym artykule opisano kroki tworzenia i usuwania z bazy danych i tabele w Eksploratorze danych platformy Azure.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 0d221138914230d5455dc0addbe08cdaaed36a0b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60826258"
---
# <a name="troubleshoot-failure-to-create-or-delete-a-database-or-table-in-azure-data-explorer"></a>Rozwiązywanie problemów: Błąd tworzenia lub usuwania bazy danych lub tabeli w Eksploratorze danych platformy Azure

W Eksploratorze danych platformy Azure regularnie pracy z bazami danych i tabel. Ten artykuł zawiera procedury rozwiązywania problemów, które są włączone.

## <a name="creating-a-database"></a>Tworzenie bazy danych

1. Upewnij się, że masz odpowiednie uprawnienia. Aby utworzyć bazę danych, musisz być członkiem *Współautor* lub *właściciela* roli w subskrypcji platformy Azure. Jeśli to konieczne, działają z Twoim administratorem subskrypcji, dzięki czemu może Cię dodać do odpowiedniej roli.

1. Upewnij się, że nie istnieją żadne błędy sprawdzania poprawności nazwy dla nazwy bazy danych. Nazwa musi być litery, cyfry, o maksymalnej długości 260 znaków.

1. Upewnij się, że okres przechowywania bazy danych i buforowania wartości w ramach zakres Dozw. Przechowywanie musi wynosić od 1 do 36500 dni (100 lat). Pamięć podręczna musi być między 1 a maksymalną wartość do przechowywania danych.

## <a name="deleting-or-renaming-a-database"></a>Usuwanie lub zmiana nazwy bazy danych

Upewnij się, że masz odpowiednie uprawnienia. Aby usunąć lub zmienić nazwę bazy danych, musisz być członkiem *Współautor* lub *właściciela* roli w subskrypcji platformy Azure. Jeśli to konieczne, działają z Twoim administratorem subskrypcji, dzięki czemu może Cię dodać do odpowiedniej roli.

## <a name="creating-a-table"></a>Tworzenie tabeli

1. Upewnij się, że masz odpowiednie uprawnienia. Aby utworzyć tabelę, musisz być członkiem *administrator bazy danych* lub *użytkownika bazy danych* roli w bazie danych lub *Współautor* lub *właściciela* Rola dla subskrypcji platformy Azure. Jeśli to konieczne, działają w ramach subskrypcji lub administrator klastrów, dzięki czemu może Cię dodać do odpowiedniej roli.

    Aby uzyskać więcej informacji o uprawnieniach, zobacz [Zarządzanie uprawnieniami do bazy danych](manage-database-permissions.md).

1. Upewnij się, czy tabeli o takiej samej nazwie jeszcze nie istnieje. Jeśli istnieje, a następnie możesz: Tworzenie tabeli o innej nazwie; Zmień nazwę istniejącej tabeli (wymaga *administratora tabeli* roli); lub porzucić istniejącej tabeli (wymaga *administrator bazy danych* roli). Użyj następujących poleceń.

    ```Kusto
    .drop table <TableName>

   .rename table <OldTableName> to <NewTableName>
    ```

## <a name="deleting-or-renaming-a-table"></a>Usuwanie lub zmiana nazwy tabeli

Upewnij się, że masz odpowiednie uprawnienia. Aby usunąć lub zmienić nazwę tabeli, musisz być członkiem *administrator bazy danych* lub *administratora tabeli* roli w bazie danych. Jeśli to konieczne, działają w ramach subskrypcji lub administrator klastrów, dzięki czemu może Cię dodać do odpowiedniej roli.

Aby uzyskać więcej informacji o uprawnieniach, zobacz [Zarządzanie uprawnieniami do bazy danych](manage-database-permissions.md).

## <a name="general-guidance"></a>Wskazówki ogólne

1. Sprawdź [pulpit nawigacyjny kondycji usługi platformy Azure](https://azure.microsoft.com/status/). Odszukaj stan Eksploratora danych usługi Azure w regionie, w którym próbujesz się pracy z bazy danych lub tabeli.

    Jeśli stan jest **dobre** (zielony znacznik wyboru), spróbuj ponownie po zwiększa stan.

1. Jeśli nadal potrzebujesz pomocy przy rozwiązywaniu problemu, otwórz żądanie pomocy technicznej w [witryny Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

## <a name="next-steps"></a>Kolejne kroki

[Sprawdzanie kondycji klastra](check-cluster-health.md)