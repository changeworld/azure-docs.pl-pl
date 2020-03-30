---
title: Niepowodzenie tworzenia lub usuwania bazy danych lub tabeli w Eksploratorze danych platformy Azure
description: W tym artykule opisano kroki rozwiązywania problemów z tworzeniem i usuwaniem baz danych i tabel w Eksploratorze danych platformy Azure.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 67e4c33498b05884fe667a7977ddb40e647ab4c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562398"
---
# <a name="troubleshoot-failure-to-create-or-delete-a-database-or-table-in-azure-data-explorer"></a>Rozwiązywanie problemów: nie można utworzyć lub usunąć bazy danych lub tabeli w Eksploratorze danych platformy Azure

W Eksploratorze danych platformy Azure regularnie pracujesz z bazami danych i tabelami. Ten artykuł zawiera kroki rozwiązywania problemów, które mogą pojawić się.

## <a name="creating-a-database"></a>Tworzenie bazy danych

1. Upewnij się, że masz odpowiednie uprawnienia. Aby utworzyć bazę danych, musisz być członkiem roli *współautora* lub *właściciela* dla subskrypcji platformy Azure. W razie potrzeby należy współpracować z administratorem subskrypcji, aby można go było dodać do odpowiedniej roli.

1. Upewnij się, że nie ma błędów sprawdzania poprawności nazwy dla nazwy bazy danych. Nazwa musi być alfanumeryczna, o maksymalnej długości 260 znaków.

1. Upewnij się, że wartości przechowywania i buforowania bazy danych znajdują się w dopuszczalnych zakresach. Zatrzymanie musi wynosić od 1 do 36500 dni (100 lat). Buforowanie musi zawierać się między 1 a maksymalną wartością ustawioną dla przechowywania.

## <a name="deleting-or-renaming-a-database"></a>Usuwanie lub zmienianie nazwy bazy danych

Upewnij się, że masz odpowiednie uprawnienia. Aby usunąć lub zmienić nazwę bazy danych, musisz być członkiem roli *współautora* lub *właściciela* dla subskrypcji platformy Azure. W razie potrzeby należy współpracować z administratorem subskrypcji, aby można go było dodać do odpowiedniej roli.

## <a name="creating-a-table"></a>Tworzenie tabeli

1. Upewnij się, że masz odpowiednie uprawnienia. Aby utworzyć tabelę, musisz być członkiem roli *administratora bazy danych* lub *użytkownika bazy danych* w bazie danych lub roli *współautora* lub *właściciela* dla subskrypcji platformy Azure. W razie potrzeby należy pracować z administratorem subskrypcji lub klastra, aby można było dodać Cię do odpowiedniej roli.

    Aby uzyskać więcej informacji o uprawnieniach, zobacz [Zarządzanie uprawnieniami do bazy danych](manage-database-permissions.md).

1. Upewnij się, że tabela o tej samej nazwie jeszcze nie istnieje. Jeśli istnieje, możesz: Utwórz tabelę o innej nazwie; zmień nazwę istniejącej tabeli (wymaga roli *administratora tabeli);* lub upuść istniejącą tabelę (wymaga roli *administratora bazy danych).* Użyj następujących poleceń.

    ```Kusto
    .drop table <TableName>

   .rename table <OldTableName> to <NewTableName>
    ```

## <a name="deleting-or-renaming-a-table"></a>Usuwanie lub zmienianie nazwy tabeli

Upewnij się, że masz odpowiednie uprawnienia. Aby usunąć lub zmienić nazwę tabeli, musisz być członkiem roli *administratora bazy danych* lub *administratora tabeli* w bazie danych. W razie potrzeby należy pracować z administratorem subskrypcji lub klastra, aby można było dodać Cię do odpowiedniej roli.

Aby uzyskać więcej informacji o uprawnieniach, zobacz [Zarządzanie uprawnieniami do bazy danych](manage-database-permissions.md).

## <a name="general-guidance"></a>Wskazówki ogólne

1. Sprawdź [pulpit nawigacyjny kondycji usługi platformy Azure](https://azure.microsoft.com/status/). Poszukaj stanu Eksploratora danych platformy Azure w regionie, w którym próbujesz pracować z bazą danych lub tabelą.

    Jeśli stan nie jest **dobry** (zielony znacznik wyboru), spróbuj ponownie po poprawie stanu.

1. Jeśli nadal potrzebujesz pomocy w rozwiązaniu problemu, otwórz żądanie pomocy technicznej w [witrynie Azure portal.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

## <a name="next-steps"></a>Następne kroki

[Sprawdzanie kondycji klastra](check-cluster-health.md)