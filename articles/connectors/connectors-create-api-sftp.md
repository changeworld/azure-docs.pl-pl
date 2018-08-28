---
title: Połączenie z kontem SFTP z usługi Azure Logic Apps | Dokumentacja firmy Microsoft
description: Automatyzowanie zadań i przepływów pracy, umożliwiające monitorowanie, tworzenie, zarządzanie, wysyłania i odbierania plików na serwerze SFTP przy użyciu usługi Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: 697eb8b0-4a66-40c7-be7b-6aa6b131c7ad
ms.topic: article
tags: connectors
ms.date: 08/24/2018
ms.openlocfilehash: 8f430477883543aa8f87eb3fb0fb49ab31e2d723
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43042042"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-azure-logic-apps"></a>Monitorowanie, tworzenie i zarządzanie plikami SFTP przy użyciu usługi Azure Logic Apps

Korzystając z usługi Azure Logic Apps i łącznika SFTP, można utworzyć zautomatyzowanym zadaniom i przepływy pracy monitorowania, tworzenie, wysyłanie i odbieranie plików za pośrednictwem Twojego konta na [SFTP](https://www.ssh.com/ssh/sftp/) serwerem oraz inne czynności, na przykład:

* Monitor, gdy pliki są dodane lub zmienione.
* Pobieranie, tworzenie, kopiowanie, aktualizacji listy i Usuń pliki.
* Pobierz zawartość pliku i metadanych.
* Wyodrębnij archiwum do folderów.

Możesz użyć wyzwalaczy, które uzyskiwanie odpowiedzi z serwera SFTP oraz udostępnić dane wyjściowe do innych działań. Akcje w aplikacjach logic apps umożliwia wykonywanie zadań z plikami na serwerze SFTP. Mogą też istnieć inne akcje użyć danych wyjściowych z akcji SFTP. Na przykład jeśli regularnie możesz pobrać pliki z serwera SFTP, możesz wysłać pocztą e-mail informacje dotyczące tych plików i ich zawartości za pomocą łącznika usługi Office 365 Outlook lub łącznik usługi Outlook.com.
Jeśli dopiero zaczynasz pracę z usługi logic apps, zapoznaj się z [co to jest Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>. 

* SFTP hosta adres i konta poświadczeń serwera

   Poświadczenia Autoryzuj aplikację logiki, aby utworzyć połączenie i dostęp do tego konta SFTP.

* Podstawową wiedzę na temat o [sposób tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp do konta SFTP. Można uruchomić z wyzwalaczem SFTP [Tworzenie pustej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby użyć akcji SFTP, uruchom aplikację logiki za pomocą wyzwalacza innego, na przykład, **cyklu** wyzwalacza.

## <a name="connect-to-sftp"></a>Nawiązać połączenie SFTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i Otwórz swoją aplikację logiki w Projektancie aplikacji logiki, jeśli nie otwarto już.

1. Puste logic apps w polu wyszukiwania wprowadź "sftp" jako filtr. W obszarze listy wyzwalaczy wybierz wyzwalacz, który ma. 

   — lub —

   Dla istniejących aplikacji logiki w ostatnim kroku, które chcesz dodać akcję, wybierz **nowy krok**. 
   W polu wyszukiwania wprowadź "sftp" jako filtr. 
   W obszarze listy akcji wybierz akcję, którą chcesz.

   Aby dodać akcję między krokami, wskaźnik myszy nad strzałką znajdującą się między krokami. 
   Wybierz znak plus (**+**) pojawia się, a następnie wybierz **Dodaj akcję**.

1. Podaj odpowiednie szczegóły połączenia, a następnie wybierz **Utwórz**.

1. Podaj odpowiednie szczegóły wybranego wyzwalacza lub akcji i kontynuuj tworzenie przepływu pracy aplikacji logiki.

## <a name="examples"></a>Przykłady

### <a name="sftp-trigger-when-a-file-is-added-or-modified"></a>Wyzwalacz protokołu SFTP: po dodaniu lub zmodyfikowaniu pliku

Ten wyzwalacz jest uruchamiany przepływ pracy aplikacji logiki po wykryciu wyzwalacz, gdy plik zostanie dodane lub zmienione na serwerze SFTP. Na przykład można dodać warunek, który sprawdza, czy zawartość pliku i decyduje o tym, czy można pobrać tej zawartości na podstawie tego, czy tę zawartość spełnia określony warunek. Na koniec możesz dodać akcję, która pobiera zawartość pliku i umieścić tę zawartość w folderze na serwerze SFTP. 

**Przykład Enterprise**: tego wyzwalacza można używać do monitorowania folderu SFTP dla nowych plików, które reprezentują zamówienia. Można następnie użyć akcji SFTP takich jak **Pobierz zawartość pliku**, dzięki czemu można uzyskać zawartość kolejności do dalszego przetwarzania i przechowywania tej kolejności w bazie danych zamówień.

### <a name="sftp-action-get-content"></a>Akcja SFTP: pobieranie zawartości

Ta akcja pobiera zawartość z pliku na serwer SFTP. Na przykład można dodać wyzwalacza z poprzedniego przykładu i warunek, który musi spełniać zawartość pliku. Jeśli warunek jest spełniony, można uruchomić akcję, która pobiera zawartość. 

## <a name="connector-reference"></a>Dokumentacja łączników

Szczegółowe informacje techniczne dotyczące wyzwalaczy, akcje i ograniczeń, które opisano przez standard OpenAPI łącznika (dawniej Swagger) opis, przejrzyj łącznika [strona referencyjna](/connectors/sftpconnector/).

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o innych [łączników Logic Apps](../connectors/apis-list.md)