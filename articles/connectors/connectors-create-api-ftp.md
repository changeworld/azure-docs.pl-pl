---
title: Nawiązać połączenie z serwerem FTP — Azure Logic Apps
description: Tworzenie, monitorowanie i zarządzanie plikami na serwerze FTP za pomocą usługi Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, LADocs
ms.topic: article
ms.date: 10/15/2018
tags: connectors
ms.openlocfilehash: e5aeaa707c7a839483484c524e982204d6fe055c
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2019
ms.locfileid: "58576330"
---
# <a name="create-monitor-and-manage-ftp-files-by-using-azure-logic-apps"></a>Tworzenie, monitorowanie i zarządzanie plikami FTP za pomocą usługi Azure Logic Apps

Korzystając z usługi Azure Logic Apps i łącznik FTP można utworzyć automatycznych zadań i przepływów pracy, tworzenia, monitorowania, wysyłania i odbierania plików za pośrednictwem Twojego konta na serwerze FTP oraz inne czynności, na przykład:

* Monitor, gdy pliki są dodane lub zmienione.
* Pobieranie, tworzenie, kopiowanie, aktualizacji listy i Usuń pliki.
* Pobierz zawartość pliku i metadanych.
* Wyodrębnij archiwum do folderów.

Możesz użyć wyzwalaczy, które uzyskać odpowiedzi z serwera FTP i udostępnić dane wyjściowe innych działań. Uruchom akcje w aplikacjach logiki służy do zarządzania plikami na serwerze FTP. Mogą też istnieć inne akcje użyć danych wyjściowych z akcji FTP. Na przykład jeśli regularnie plików z serwera FTP, możesz wysłać pocztą e-mail informacje dotyczące tych plików i ich zawartości za pomocą łącznika usługi Office 365 Outlook lub łącznik usługi Outlook.com. Jeśli dopiero zaczynasz pracę z usługi logic apps, zapoznaj się z [co to jest Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="limits"></a>Limity

* Akcje FTP obsługuje tylko pliki, które są *50 MB lub mniej* chyba że używasz [segmentu komunikat](../logic-apps/logic-apps-handle-large-messages.md), umożliwiają który przekroczenia tego limitu. Obecnie usługa wyzwalaczy FTP nie obsługują segmentu.

* Łącznik FTP obsługuje tylko jawne FTP over SSL (FTPS) i nie jest zgodny z niejawne protokołu FTPS.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>. 

* FTP hosta adres i konta poświadczeń serwera

  Łącznik FTP wymaga, że serwer FTP jest dostępny z Internetu i skonfiguruj działanie w *pasywnym* trybu. Poświadczenia umożliwiają aplikację logiki, Utwórz połączenie i uzyskać dostęp do konta użytkownika FTP.

* Podstawową wiedzę na temat o [sposób tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp do konta użytkownika FTP. Można uruchomić przy użyciu wyzwalacza usługi FTP [Tworzenie pustej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby użyć akcji FTP, uruchom aplikację logiki za pomocą wyzwalacza innego, na przykład, **cyklu** wyzwalacza.

## <a name="connect-to-ftp"></a>Nawiązać połączenie FTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i Otwórz swoją aplikację logiki w Projektancie aplikacji logiki, jeśli nie otwarto już.

1. Puste logic apps w polu wyszukiwania wprowadź "ftp" jako filtr. W obszarze listy wyzwalaczy wybierz wyzwalacz, który ma.

   — lub —

   Dla istniejących aplikacji logiki w ostatnim kroku, które chcesz dodać akcję, wybierz **nowy krok**, a następnie wybierz pozycję **Dodaj akcję**. 
   W polu wyszukiwania wprowadź "ftp" jako filtr. 
   W obszarze listy akcji wybierz akcję, którą chcesz.

   Aby dodać akcję między krokami, wskaźnik myszy nad strzałką znajdującą się między krokami. 
   Wybierz znak plus (**+**) pojawia się, a następnie wybierz **Dodaj akcję**.

1. Podaj odpowiednie szczegóły połączenia, a następnie wybierz **Utwórz**.

1. Podaj odpowiednie szczegóły wybranego wyzwalacza lub akcji i kontynuuj tworzenie przepływu pracy aplikacji logiki.

Podczas żądania zawartości pliku, wyzwalacz nie przyniesie oczekiwanych plików większych niż 50 MB. Aby pobrać pliki większe niż 50 MB, należy korzystać z tego wzoru:

* Użyj wyzwalacz, który zwraca wartość właściwości pliku, taką jak **gdy plik jest dodawany lub modyfikowany (tylko właściwości)**.

* Postępuj zgodnie z wyzwalacza z akcji, która odczytuje plik pełną, takich jak **Pobierz zawartość pliku przy użyciu ścieżki**, i akcji, użyj [segmentu komunikat](../logic-apps/logic-apps-handle-large-messages.md).

## <a name="examples"></a>Przykłady

<a name="file-added-modified"></a>

### <a name="ftp-trigger-when-a-file-is-added-or-modified"></a>Wyzwalacz FTP: Po dodaniu lub zmodyfikowaniu pliku

Ten wyzwalacz jest uruchamiany przepływ pracy aplikacji logiki po wykryciu wyzwalacz, gdy plik zostanie dodane lub zmienione na serwerze FTP. Na przykład można dodać warunek, który sprawdza, czy zawartość pliku i decyduje o tym, czy można pobrać tej zawartości na podstawie tego, czy tę zawartość spełnia określony warunek. Na koniec możesz dodać akcję, która pobiera zawartość pliku i umieścić tę zawartość w folderze na serwerze SFTP. 

**Przykład Enterprise**: Tego wyzwalacza można używać do monitorowania folderu FTP dla nowych plików, które opisują zamówienia. Można następnie użyć akcji FTP takich jak **Pobierz zawartość pliku**, dzięki czemu można uzyskać zawartość kolejności do dalszego przetwarzania i przechowywania tej kolejności w bazie danych zamówień.

Podczas żądania zawartości pliku, wyzwalaczy nie można pobrać plików większych niż 50 MB. Aby pobrać pliki większe niż 50 MB, należy korzystać z tego wzoru: 

* Użyj wyzwalacz, który zwraca wartość właściwości pliku, taką jak **gdy plik jest dodawany lub modyfikowany (tylko właściwości)**.

* Postępuj zgodnie z wyzwalacza z akcji, która odczytuje plik pełną, takich jak **Pobierz zawartość pliku przy użyciu ścieżki**, i akcji, użyj [segmentu komunikat](../logic-apps/logic-apps-handle-large-messages.md).

Aplikacja logiki prawidłowe i funkcjonalności wymaga wyzwalacza i co najmniej jedną akcję. Dlatego upewnij się, że akcja zostanie dodana po dodaniu wyzwalacza.

Oto przykład pokazujący tego wyzwalacza: **Po dodaniu lub zmodyfikowaniu pliku**

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i Otwórz swoją aplikację logiki w Projektancie aplikacji logiki, jeśli nie otwarto już.

1. Puste logic apps w polu wyszukiwania wprowadź "ftp" jako filtr. W obszarze listy wyzwalaczy wybierz następujący wyzwalacz: **Gdy zachowanej jest dodawany lub modyfikowany — FTP**

   ![Znajdź i wybierz wyzwalacz FTP](./media/connectors-create-api-ftp/select-ftp-trigger.png)  

1. Podaj odpowiednie szczegóły połączenia, a następnie wybierz **Utwórz**.

   Domyślnie ten łącznik do transferu plików w formacie tekstowym. 
   Do transferu plików w pliku binarnym formatowania, na przykład, gdzie i kiedy jest używane kodowanie, wybierz **Transport binarny**.

   ![Utwórz połączenie z serwerem FTP](./media/connectors-create-api-ftp/create-ftp-connection-trigger.png)  

1. Obok pozycji **folderu** , wybierz ikonę folderu, więc zostanie wyświetlona lista. Aby znaleźć folder, który chcesz monitorować nowe lub zmodyfikowane pliki, wybierz strzałkę pod kątem prostym (**>**), przejdź do folderu, a następnie wybierz folder.

   ![Znajdź i wybierz folder do monitorowania](./media/connectors-create-api-ftp/select-folder.png)  

   Wybranym folderze, który pojawia się w **folderu** pole.

   ![Wybrany folder](./media/connectors-create-api-ftp/selected-folder.png)  

Teraz, że Twoja aplikacja logiki ma wyzwalacz, należy dodać akcje, które chcesz uruchomić, gdy Twoja aplikacja logiki znajdzie nowe lub zmodyfikowane pliku. Na przykład możesz dodać akcję FTP, która pobiera zawartość nowe lub zaktualizowane.

<a name="get-content"></a>

### <a name="ftp-action-get-content"></a>Akcja FTP: Pobieranie zawartości

Ta akcja pobiera zawartość z pliku na serwerze FTP, gdy ten plik jest dodane lub zaktualizowane. Na przykład można dodać wyzwalacza z poprzedniego przykładu i akcji, która pobiera zawartość pliku, po dodaniu lub edytować tego pliku. 

Podczas żądania zawartości pliku, wyzwalaczy nie można pobrać plików większych niż 50 MB. Aby pobrać pliki większe niż 50 MB, należy korzystać z tego wzoru: 

* Użyj wyzwalacz, który zwraca wartość właściwości pliku, taką jak **gdy plik jest dodawany lub modyfikowany (tylko właściwości)**.

* Postępuj zgodnie z wyzwalacza z akcji, która odczytuje plik pełną, takich jak **Pobierz zawartość pliku przy użyciu ścieżki**, i akcji, użyj [segmentu komunikat](../logic-apps/logic-apps-handle-large-messages.md).

Oto przykład, który pokazuje tę akcję: **Pobieranie zawartości**

1. W obszarze wyzwalacza lub innych akcji, wybierz opcję **nowy krok**. 

1. W polu wyszukiwania wprowadź "ftp" jako filtr. W obszarze listy akcji wybierz następującą akcję: **Pobierz zawartość pliku — FTP**

   ![Wybierz akcję, FTP](./media/connectors-create-api-ftp/select-ftp-action.png)  

1. Jeśli masz już połączenie z serwerem FTP a konta, przejdź do następnego kroku. W przeciwnym razie, wprowadź wymagane szczegóły dla tego połączenia, a następnie wybierz **Utwórz**. 

   ![Utwórz połączenie z serwerem FTP](./media/connectors-create-api-ftp/create-ftp-connection-action.png)

1. Po **Pobierz zawartość pliku** akcja zostanie otwarta, kliknij wewnątrz **pliku** pola tak, aby wyświetlić listę zawartości dynamicznej. Teraz można wybrać właściwości dane wyjściowe z poprzednich kroków. Wybierz z listy zawartości dynamicznej **zawartość pliku** właściwość, która ma zawartość pliku dodane lub zaktualizowane.  

   ![Znajdź i wybierz plik](./media/connectors-create-api-ftp/ftp-action-get-file-content.png)

   **Zawartość pliku** właściwość pojawia się teraz w **pliku** pole.

   ![Wybrana właściwość "Zawartość pliku"](./media/connectors-create-api-ftp/ftp-action-selected-file-content-property.png)

1. Zapisz aplikację logiki. Aby przetestować przepływ pracy, należy dodać plik do folderu FTP, który teraz monitoruje aplikację logiki.

## <a name="connector-reference"></a>Dokumentacja łączników

Szczegółowe informacje techniczne dotyczące wyzwalaczy, akcje i limity, który opisano przez łącznika interfejsu OpenAPI (dawniej Swagger) opis, przejrzyj [strona referencyjna łącznika](/connectors/ftpconnector/).

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o innych [łączników Logic Apps](../connectors/apis-list.md)
