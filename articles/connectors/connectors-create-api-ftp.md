---
title: Nawiązywanie połączenia z serwerem FTP — Azure Logic Apps
description: Tworzenie, monitorowanie i zarządzanie plikami na serwerze FTP za pomocą Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.reviewer: divswa, klam, LADocs
ms.topic: conceptual
ms.date: 06/19/2019
tags: connectors
ms.openlocfilehash: ac6ae1a3b00a4e7568bd7967105f202fbf2e4f9b
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74547494"
---
# <a name="create-monitor-and-manage-ftp-files-by-using-azure-logic-apps"></a>Tworzenie i monitorowanie plików FTP oraz zarządzanie nimi za pomocą Azure Logic Apps

Za pomocą Azure Logic Apps i łącznika FTP można tworzyć automatyczne zadania i przepływy pracy, które tworzą, monitorują, wysyłają i odbierają pliki przy użyciu konta na serwerze FTP oraz z innymi akcjami, na przykład:

* Monitoruj, gdy pliki są dodawane lub zmieniane.
* Pobieranie, tworzenie, kopiowanie, aktualizowanie, wyświetlanie i usuwanie plików.
* Pobieranie zawartości i metadanych pliku.
* Wyodrębnij archiwa do folderów.

Można użyć wyzwalaczy, które odbierają odpowiedzi z serwera FTP i udostępniają dane wyjściowe innym akcjom. W aplikacjach logiki można używać akcji Run do zarządzania plikami na serwerze FTP. Możesz również mieć inne akcje, które używają danych wyjściowych z akcji FTP. Na przykład w przypadku regularnego pobierania plików z serwera FTP można wysyłać wiadomości e-mail dotyczące tych plików i ich zawartości przy użyciu łącznika programu Office 365 Outlook lub łącznika Outlook.com. Jeśli jesteś nowym sposobem logiki aplikacji, zapoznaj [się z tematem Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="limits"></a>Limity

* Łącznik FTP obsługuje tylko jawne FTP za pośrednictwem protokołu SSL (FTPS) i nie jest zgodny z niejawnym FTPS.

* Domyślnie akcje FTP mogą odczytywać lub zapisywać pliki, które są *50 MB lub mniejsze*. Aby obsługiwać pliki o rozmiarze większym niż 50 MB, operacje FTP obsługują [fragmenty komunikatów](../logic-apps/logic-apps-handle-large-messages.md). Akcja **Pobierz zawartość pliku** niejawnie używa podziału.

* Wyzwalacze FTP nie obsługują fragmentacji. Podczas żądania zawartości pliku wyzwalane są tylko pliki o rozmiarze 50 MB lub mniejszej. Aby uzyskać pliki o rozmiarze większym niż 50 MB, wykonaj następujące czynności:

  * Użyj wyzwalacza FTP, który zwraca właściwości pliku, na przykład **gdy plik jest dodawany lub modyfikowany (tylko właściwości)** .

  * Śledź wyzwalacz z akcją **Pobierz zawartość pliku** FTP, która odczytuje kompletny plik i niejawnie używa podziału.

## <a name="how-ftp-triggers-work"></a>Jak działają wyzwalacze FTP

Wyzwalacze FTP działają przez sondowanie systemu plików FTP i wyszukiwanie wszelkich plików, które zostały zmienione od czasu ostatniego sondowania. Niektóre narzędzia pozwalają zachować sygnaturę czasową, gdy pliki zmienią się. W takich przypadkach należy wyłączyć tę funkcję, aby wyzwalacz mógł funkcjonować. Poniżej przedstawiono niektóre typowe ustawienia:

| Klient SFTP | Działanie |
|-------------|--------|
| WinSCP | Przejdź do **opcji opcje** > **preferencje** > **transfer** > **edycja** > **Zachowaj sygnaturę czasową** > **Wyłącz** |
| FileZilla | Przejdź do obszaru **Transfer** > **zachować sygnatury czasowe transferowanych plików** > **wyłączyć** |
|||

Gdy wyzwalacz odnajdzie nowy plik, wyzwalacz sprawdza, czy nowy plik jest zakończony i nie jest częściowo zapisany. Na przykład plik może mieć zmiany w toku, gdy wyzwalacz sprawdza serwer plików. Aby uniknąć powrotu częściowo zapisywanego pliku, wyzwalacz odnotowuje sygnaturę czasową dla pliku, który ma ostatnio wprowadzone zmiany, ale nie zwraca natychmiast tego pliku. Wyzwalacz zwraca plik tylko wtedy, gdy ponownie sonduje serwer. Czasami takie zachowanie może spowodować opóźnienie, który jest maksymalnie dwa razy interwał sondowania wyzwalacza.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Adres serwera hosta FTP i poświadczenia konta

  Łącznik FTP wymaga, aby serwer FTP był dostępny z Internetu i skonfigurowany do działania w trybie *pasywnym* . Twoje poświadczenia pozwalają aplikacji logiki utworzyć połączenie i uzyskać dostęp do konta FTP.

* Podstawowa wiedza [na temat tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp do konta FTP. Aby rozpocząć pracę z wyzwalaczem FTP, [Utwórz pustą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby użyć akcji FTP, uruchom aplikację logiki z innym wyzwalaczem, na przykład wyzwalaczem **cyklu** .

## <a name="connect-to-ftp"></a>Nawiązywanie połączenia z serwerem FTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [Azure Portal](https://portal.azure.com)i Otwórz aplikację logiki w Projektancie aplikacji logiki, jeśli nie jest jeszcze otwarta.

1. W przypadku pustych aplikacji logiki w polu wyszukiwania wprowadź ciąg "FTP" jako filtr. Na liście Wyzwalacze wybierz wyzwalacz, który chcesz.

   — lub —

   W przypadku istniejących aplikacji logiki w ostatnim kroku, w którym chcesz dodać akcję, wybierz pozycję **nowy krok**, a następnie wybierz pozycję **Dodaj akcję**. W polu wyszukiwania wprowadź ciąg "FTP" jako filtr. Na liście Akcje wybierz żądaną akcję.

   Aby dodać akcję między krokami, przesuń wskaźnik myszy nad strzałkę między krokami. Wybierz wyświetlony znak plus ( **+** ), a następnie wybierz pozycję **Dodaj akcję**.

1. Podaj niezbędne szczegóły dotyczące połączenia, a następnie wybierz pozycję **Utwórz**.

1. Podaj niezbędne szczegóły wybranego wyzwalacza lub akcji i Kontynuuj tworzenie przepływu pracy aplikacji logiki.

## <a name="examples"></a>Przykłady

<a name="file-added-modified"></a>

### <a name="ftp-trigger-when-a-file-is-added-or-modified"></a>Wyzwalacz FTP: po dodaniu lub zmodyfikowaniu pliku

Ten wyzwalacz uruchamia przepływ pracy aplikacji logiki, gdy wyzwalacz wykryje, gdy plik zostanie dodany lub zmieniony na serwerze FTP. Na przykład można dodać warunek, który sprawdza zawartość pliku i decyduje o tym, czy zawartość jest zgodna z określonym warunkiem. Na koniec można dodać akcję, która pobiera zawartość pliku i umieścić tę zawartość w folderze na serwerze SFTP.

**Przykład przedsiębiorstwa**: ten wyzwalacz służy do monitorowania folderu FTP dla nowych plików, które opisują zamówienia klientów. Następnie możesz użyć akcji FTP, takiej jak **pobieranie zawartości pliku**, aby można było uzyskać zawartość zamówienia do dalszej obróbki i przechowywać ją w bazie danych zamówień.

Oto przykład, który pokazuje ten wyzwalacz: **po dodaniu lub zmodyfikowaniu pliku**

1. Zaloguj się do [Azure Portal](https://portal.azure.com)i Otwórz aplikację logiki w Projektancie aplikacji logiki, jeśli nie jest jeszcze otwarta.

1. W przypadku pustych aplikacji logiki w polu wyszukiwania wprowadź ciąg "FTP" jako filtr. Na liście Wyzwalacze wybierz ten wyzwalacz: **po dodaniu lub zmodyfikowaniu elementu FTP**

   ![Znajdź i wybierz wyzwalacz FTP](./media/connectors-create-api-ftp/select-ftp-trigger.png)  

1. Podaj niezbędne szczegóły dotyczące połączenia, a następnie wybierz pozycję **Utwórz**.

   Domyślnie ten łącznik przenosi pliki w formacie tekstowym. Aby przenieść pliki w formacie binarnym, na przykład gdzie i kiedy jest używane kodowanie, wybierz opcję **transport binarny**.

   ![Utwórz połączenie z serwerem FTP](./media/connectors-create-api-ftp/create-ftp-connection-trigger.png)  

1. Obok pola **folder** wybierz ikonę folderu, aby wyświetlić listę. Aby znaleźć folder, który ma być monitorowany dla nowych lub edytowanych plików, wybierz strzałkę w prawo kąt ( **>** ), przejdź do tego folderu, a następnie wybierz folder.

   ![Znajdź i wybierz folder do monitorowania](./media/connectors-create-api-ftp/select-folder.png)  

   Wybrany folder zostanie wyświetlony w polu **folder** .

   ![Wybrany folder](./media/connectors-create-api-ftp/selected-folder.png)  

Teraz, gdy aplikacja logiki ma wyzwalacz, Dodaj akcje, które chcesz uruchomić, gdy aplikacja logiki odnajdzie nowy lub zmodyfikowany plik. Na potrzeby tego przykładu można dodać akcję FTP, która pobiera nową lub zaktualizowaną zawartość.

<a name="get-content"></a>

### <a name="ftp-action-get-content"></a>Akcja FTP: pobieranie zawartości

Ta akcja pobiera zawartość z pliku na serwerze FTP w przypadku dodania lub zaktualizowania tego pliku. Na przykład można dodać wyzwalacz z poprzedniego przykładu i akcję, która pobiera zawartość pliku po dodaniu lub edytowaniu tego pliku.

Oto przykład, który pokazuje tę akcję: **Pobierz zawartość**

1. W obszarze wyzwalacza lub innych akcji wybierz pozycję **nowy krok**.

1. W polu wyszukiwania wprowadź ciąg "FTP" jako filtr. Na liście Akcje wybierz tę akcję: **Pobierz zawartość pliku — FTP**

   ![Wybierz akcję FTP](./media/connectors-create-api-ftp/select-ftp-action.png)  

1. Jeśli masz już połączenie z serwerem FTP i kontem, przejdź do następnego kroku. W przeciwnym razie podaj niezbędne szczegóły tego połączenia, a następnie wybierz pozycję **Utwórz**.

   ![Utwórz połączenie z serwerem FTP](./media/connectors-create-api-ftp/create-ftp-connection-action.png)

1. Gdy zostanie otwarta Akcja **Pobierz zawartość pliku** , kliknij wewnątrz pola **plik** , aby wyświetlić listę zawartości dynamicznej. Teraz można wybrać właściwości danych wyjściowych z poprzednich kroków. Z listy zawartość dynamiczna wybierz właściwość **zawartość pliku** , która ma zawartość dla dodanego lub zaktualizowanego pliku.  

   ![Znajdź i wybierz plik](./media/connectors-create-api-ftp/ftp-action-get-file-content.png)

   Właściwość **zawartość pliku** zostanie teraz wyświetlona w polu **plik** .

   ![Wybrana Właściwość "zawartość pliku"](./media/connectors-create-api-ftp/ftp-action-selected-file-content-property.png)

1. Zapisz aplikację logiki. Aby przetestować przepływ pracy, Dodaj plik do folderu FTP, który teraz monitoruje aplikację logiki.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne na temat wyzwalaczy, akcji i limitów, które są opisane w opisie OpenAPI łącznika (dawniej Swagger), przejrzyj [stronę odwołania łącznika](/connectors/ftpconnector/).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md)