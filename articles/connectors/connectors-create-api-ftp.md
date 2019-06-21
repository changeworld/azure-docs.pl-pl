---
title: Nawiązać połączenie z serwerem FTP — Azure Logic Apps
description: Tworzenie, monitorowanie i zarządzanie plikami na serwerze FTP za pomocą usługi Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, klam, LADocs
ms.topic: article
ms.date: 06/19/2019
tags: connectors
ms.openlocfilehash: 66f1d726dcfa1a077abbff0d9f028036db43cc25
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67293092"
---
# <a name="create-monitor-and-manage-ftp-files-by-using-azure-logic-apps"></a>Tworzenie, monitorowanie i zarządzanie plikami FTP za pomocą usługi Azure Logic Apps

Korzystając z usługi Azure Logic Apps i łącznik FTP można utworzyć automatycznych zadań i przepływów pracy, tworzenia, monitorowania, wysyłania i odbierania plików za pośrednictwem Twojego konta na serwerze FTP oraz inne czynności, na przykład:

* Monitor, gdy pliki są dodane lub zmienione.
* Pobieranie, tworzenie, kopiowanie, aktualizacji listy i Usuń pliki.
* Pobierz zawartość pliku i metadanych.
* Wyodrębnij archiwum do folderów.

Możesz użyć wyzwalaczy, które uzyskać odpowiedzi z serwera FTP i udostępnić dane wyjściowe innych działań. Uruchom akcje w aplikacjach logiki służy do zarządzania plikami na serwerze FTP. Mogą też istnieć inne akcje użyć danych wyjściowych z akcji FTP. Na przykład jeśli regularnie plików z serwera FTP, możesz wysłać pocztą e-mail informacje dotyczące tych plików i ich zawartości za pomocą łącznika usługi Office 365 Outlook lub łącznik usługi Outlook.com. Jeśli dopiero zaczynasz pracę z usługi logic apps, zapoznaj się z [co to jest Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="limits"></a>Limits

* Łącznik FTP obsługuje tylko jawne FTP over SSL (FTPS) i nie jest zgodny z niejawne protokołu FTPS.

* Domyślnie akcje FTP można odczytać lub zapisać pliki, które są *50 MB lub mniej*. Do obsługi plików większych niż 50 MB, FTP Obsługa akcji [segmentu komunikat](../logic-apps/logic-apps-handle-large-messages.md). **Pobierz zawartość pliku** Akcja niejawnie używa segmentu.

* Wyzwalacze FTP nie jest obsługiwane segmentu. Podczas żądania zawartości pliku, wyzwalaczy wybierz tylko te pliki, które są 50 MB lub mniej. Aby pobrać pliki większe niż 50 MB, należy korzystać z tego wzoru:

  * Użyj wyzwalacza FTP, który zwraca wartość właściwości pliku, taką jak **gdy plik jest dodawany lub modyfikowany (tylko właściwości)** .

  * Postępuj zgodnie z wyzwalacza przy użyciu protokołu FTP **Pobierz zawartość pliku** akcji, która odczytuje całego pliku i niejawnie wykorzystuje segmentu.

## <a name="how-ftp-triggers-work"></a>Jak FTP wyzwala pracy

FTP pracy wyzwalacze sondowania w systemie plików FTP i przejrzysz dla każdego pliku, który został zmieniony od czasu ostatniego sondowania. Niektóre narzędzia pozwalają zachować sygnaturę czasową, gdy zmienią się pliki. W takich przypadkach należy wyłączyć tę funkcję, dzięki czemu można pracować wyzwalacza. Poniżej przedstawiono niektóre typowe ustawienia:

| Klient protokołu SFTP | Akcja |
|-------------|--------|
| Winscp | Przejdź do **opcje** > **preferencje** > **transferu** > **Edytuj**  >  **Zachować sygnatury czasowej** > **wyłączone** |
| FileZilla | Przejdź do **transferu** > **zachować znacznikami czasu plików przeniesionych** > **wyłączone** |
|||

Jeśli wyzwalacz wykryje nowy plik, wyzwalacz sprawdzi, czy nowy plik jest pełny i niezapisane częściowo. Na przykład plik może mieć zmiany w toku, gdy wyzwalacz sprawdza, czy serwer plików. Aby uniknąć, zwracając częściowo napisane pliku, wyzwalacz — informacje o sygnaturę czasową dla pliku, który zawiera ostatnie zmiany, ale nie natychmiast przywrócić ten plik. Wyzwalacz zwraca plik tylko wtedy, gdy ponownie sondowania serwera. Czasami to zachowanie może powodować opóźnienia, która jest maksymalnie dwa razy tego wyzwalacza interwał sondowania.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* FTP hosta adres i konta poświadczeń serwera

  Łącznik FTP wymaga, że serwer FTP jest dostępny z Internetu i skonfiguruj działanie w *pasywnym* trybu. Poświadczenia umożliwiają aplikację logiki, Utwórz połączenie i uzyskać dostęp do konta użytkownika FTP.

* Podstawową wiedzę na temat o [sposób tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp do konta użytkownika FTP. Można uruchomić przy użyciu wyzwalacza usługi FTP [Tworzenie pustej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby użyć akcji FTP, uruchom aplikację logiki za pomocą wyzwalacza innego, na przykład, **cyklu** wyzwalacza.

## <a name="connect-to-ftp"></a>Nawiązać połączenie FTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i Otwórz swoją aplikację logiki w Projektancie aplikacji logiki, jeśli nie otwarto już.

1. Puste logic apps w polu wyszukiwania wprowadź "ftp" jako filtr. W obszarze listy wyzwalaczy wybierz wyzwalacz, który ma.

   —lub—

   Dla istniejących aplikacji logiki w ostatnim kroku, które chcesz dodać akcję, wybierz **nowy krok**, a następnie wybierz pozycję **Dodaj akcję**. W polu wyszukiwania wprowadź "ftp" jako filtr. W obszarze listy akcji wybierz akcję, którą chcesz.

   Aby dodać akcję między krokami, wskaźnik myszy nad strzałką znajdującą się między krokami. Wybierz znak plus ( **+** ) pojawia się i wybierz **Dodaj akcję**.

1. Podaj odpowiednie szczegóły połączenia, a następnie wybierz **Utwórz**.

1. Podaj odpowiednie szczegóły wybranego wyzwalacza lub akcji i kontynuuj tworzenie przepływu pracy aplikacji logiki.

## <a name="examples"></a>Przykłady

<a name="file-added-modified"></a>

### <a name="ftp-trigger-when-a-file-is-added-or-modified"></a>Wyzwalacz FTP: Po dodaniu lub zmodyfikowaniu pliku

Ten wyzwalacz jest uruchamiany przepływ pracy aplikacji logiki po wykryciu wyzwalacz, gdy plik zostanie dodane lub zmienione na serwerze FTP. Na przykład można dodać warunek, który sprawdza, czy zawartość pliku i decyduje o tym, czy można pobrać tej zawartości na podstawie tego, czy tę zawartość spełnia określony warunek. Na koniec możesz dodać akcję, która pobiera zawartość pliku i umieścić tę zawartość w folderze na serwerze SFTP.

**Przykład Enterprise**: Tego wyzwalacza można używać do monitorowania folderu FTP dla nowych plików, które opisują zamówienia. Można następnie użyć akcji FTP takich jak **Pobierz zawartość pliku**, dzięki czemu można uzyskać zawartość kolejności do dalszego przetwarzania i przechowywania tej kolejności w bazie danych zamówień.

Oto przykład pokazujący tego wyzwalacza: **Po dodaniu lub zmodyfikowaniu pliku**

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i Otwórz swoją aplikację logiki w Projektancie aplikacji logiki, jeśli nie otwarto już.

1. Puste logic apps w polu wyszukiwania wprowadź "ftp" jako filtr. W obszarze listy wyzwalaczy wybierz następujący wyzwalacz: **Gdy zachowanej jest dodawany lub modyfikowany — FTP**

   ![Znajdź i wybierz wyzwalacz FTP](./media/connectors-create-api-ftp/select-ftp-trigger.png)  

1. Podaj odpowiednie szczegóły połączenia, a następnie wybierz **Utwórz**.

   Domyślnie ten łącznik do transferu plików w formacie tekstowym. Do transferu plików w pliku binarnym formatowania, na przykład, gdzie i kiedy jest używane kodowanie, wybierz **Transport binarny**.

   ![Utwórz połączenie z serwerem FTP](./media/connectors-create-api-ftp/create-ftp-connection-trigger.png)  

1. Obok pozycji **folderu** , wybierz ikonę folderu, więc zostanie wyświetlona lista. Aby znaleźć folder, który chcesz monitorować nowe lub zmodyfikowane pliki, wybierz strzałkę pod kątem prostym ( **>** ), przejdź do folderu, a następnie wybierz folder.

   ![Znajdź i wybierz folder do monitorowania](./media/connectors-create-api-ftp/select-folder.png)  

   Wybranym folderze, który pojawia się w **folderu** pole.

   ![Wybrany folder](./media/connectors-create-api-ftp/selected-folder.png)  

Teraz, że Twoja aplikacja logiki ma wyzwalacz, należy dodać akcje, które chcesz uruchomić, gdy Twoja aplikacja logiki znajdzie nowe lub zmodyfikowane pliku. Na przykład możesz dodać akcję FTP, która pobiera zawartość nowe lub zaktualizowane.

<a name="get-content"></a>

### <a name="ftp-action-get-content"></a>Akcja FTP: Pobieranie zawartości

Ta akcja pobiera zawartość z pliku na serwerze FTP, gdy ten plik jest dodane lub zaktualizowane. Na przykład można dodać wyzwalacza z poprzedniego przykładu i akcji, która pobiera zawartość pliku, po dodaniu lub edytować tego pliku.

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

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o innych [łączników Logic Apps](../connectors/apis-list.md)
