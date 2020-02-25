---
title: Nie można utworzyć lub usunąć bazy danych lub tabeli na platformie Azure Eksplorator danych
description: W tym artykule opisano procedurę rozwiązywania problemów dotyczących tworzenia i usuwania baz danych i tabel w usłudze Azure Eksplorator danych.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 67e4c33498b05884fe667a7977ddb40e647ab4c8
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562398"
---
# <a name="troubleshoot-failure-to-create-or-delete-a-database-or-table-in-azure-data-explorer"></a>Rozwiązywanie problemów: niepowodzenie tworzenia lub usuwania bazy danych lub tabeli na platformie Azure Eksplorator danych

Na platformie Azure Eksplorator danych często pracujesz z bazami danych i tabelami. W tym artykule przedstawiono kroki rozwiązywania problemów, które mogą się pojawić.

## <a name="creating-a-database"></a>Tworzenie bazy danych

1. Upewnij się, że masz odpowiednie uprawnienia. Aby utworzyć bazę danych, musisz być członkiem roli *współautora* lub *właściciela* subskrypcji platformy Azure. Jeśli to konieczne, skontaktuj się z administratorem subskrypcji, aby móc dodać Cię do odpowiedniej roli.

1. Upewnij się, że nazwa bazy danych nie zawiera błędów sprawdzania poprawności nazwy. Nazwa musi być alfanumeryczna z maksymalną długością 260 znaków.

1. Upewnij się, że wartości przechowywania i buforowania bazy danych znajdują się w dozwolonych zakresach. Czas przechowywania musi należeć do zakresu od 1 do 36500 dni (100 lat). Buforowanie musi zawierać się w przedziale od 1 do maksymalnej wartości ustawionej do przechowywania.

## <a name="deleting-or-renaming-a-database"></a>Usuwanie bazy danych lub zmienianie jej nazwy

Upewnij się, że masz odpowiednie uprawnienia. Aby usunąć bazę danych lub zmienić jej nazwę, musisz być członkiem roli *współautora* lub *właściciela* subskrypcji platformy Azure. Jeśli to konieczne, skontaktuj się z administratorem subskrypcji, aby móc dodać Cię do odpowiedniej roli.

## <a name="creating-a-table"></a>Tworzenie tabeli

1. Upewnij się, że masz odpowiednie uprawnienia. Aby utworzyć tabelę, musisz być członkiem roli użytkownika *administrator bazy danych* lub *baza danych* w bazie danych lub *współautorem* lub rolą *właściciela* subskrypcji platformy Azure. W razie potrzeby należy skontaktować się z subskrypcją lub administratorem klastra, aby mogli dodać Cię do odpowiedniej roli.

    Aby uzyskać więcej informacji o uprawnieniach, zobacz [Zarządzanie uprawnieniami do bazy danych](manage-database-permissions.md).

1. Upewnij się, że tabela o tej samej nazwie już nie istnieje. Jeśli istnieje, możesz: utworzyć tabelę o innej nazwie. Zmień nazwę istniejącej tabeli (wymaga roli *administratora tabeli* ); lub Porzuć istniejącą tabelę (wymaga roli *administratora bazy danych* ). Użyj następujących poleceń.

    ```Kusto
    .drop table <TableName>

   .rename table <OldTableName> to <NewTableName>
    ```

## <a name="deleting-or-renaming-a-table"></a>Usuwanie lub zmiana nazwy tabeli

Upewnij się, że masz odpowiednie uprawnienia. Aby usunąć tabelę lub zmienić jej nazwę, musisz być członkiem roli administratora *bazy danych* lub *administratorem tabeli* w bazie danych. W razie potrzeby należy skontaktować się z subskrypcją lub administratorem klastra, aby mogli dodać Cię do odpowiedniej roli.

Aby uzyskać więcej informacji o uprawnieniach, zobacz [Zarządzanie uprawnieniami do bazy danych](manage-database-permissions.md).

## <a name="general-guidance"></a>Wskazówki ogólne

1. Sprawdź [pulpit nawigacyjny kondycji usługi platformy Azure](https://azure.microsoft.com/status/). Poszukaj stanu Eksplorator danych platformy Azure w regionie, w którym próbujesz korzystać z bazy danych lub tabeli.

    Jeśli stan nie jest **dobry** (zielony znacznik wyboru), spróbuj ponownie po zwiększeniu stanu.

1. Jeśli nadal potrzebujesz pomocy przy rozwiązywaniu problemu, Otwórz żądanie pomocy technicznej w [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

## <a name="next-steps"></a>Następne kroki

[Sprawdzanie kondycji klastra](check-cluster-health.md)