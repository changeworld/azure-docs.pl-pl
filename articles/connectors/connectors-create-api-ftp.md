---
title: Łączenie się z serwerem FTP
description: Automatyzuj zadania i przepływy pracy, które tworzą, monitorują i zarządzają plikami na serwerze FTP przy użyciu aplikacji Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 12/15/2019
tags: connectors
ms.openlocfilehash: 94ca609d9dc070e6e2b4dc878ecd8dfaf9331ede
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77648176"
---
# <a name="create-monitor-and-manage-ftp-files-by-using-azure-logic-apps"></a>Tworzenie, monitorowanie i zarządzanie plikami FTP przy użyciu usługi Azure Logic Apps

Za pomocą usługi Azure Logic Apps i łącznika FTP można tworzyć zautomatyzowane zadania i przepływy pracy, które tworzą, monitorują, wysyłają i odbierają pliki za pośrednictwem konta na serwerze FTP, a także inne akcje, na przykład:

* Monitoruj, kiedy pliki są dodawane lub zmieniane.
* Pobierz, utwórz, skopiuj, aktualizuj, wystaw i usuń pliki.
* Pobierz zawartość pliku i metadane.
* Wyodrębnij archiwa do folderów.

Można użyć wyzwalaczy, które otrzymują odpowiedzi z serwera FTP i udostępniają dane wyjściowe innym działaniom. Do zarządzania plikami na serwerze FTP można używać akcji uruchamiania w aplikacjach logiki. Można również mieć inne akcje użyć danych wyjściowych z akcji FTP. Na przykład jeśli regularnie otrzymujesz pliki z serwera FTP, możesz wysyłać wiadomości e-mail dotyczące tych plików i ich zawartości za pomocą łącznika programu Office 365 Outlook lub łącznika Outlook.com. Jeśli jesteś nowy w aplikacjach logiki, sprawdź [Co to jest Usługa Azure Logic Apps](../logic-apps/logic-apps-overview.md).

## <a name="limitations"></a>Ograniczenia

* Złącze FTP obsługuje tylko jawne ftp przez SSL (FTPS) i nie jest kompatybilny z niejawnym FTPS.

* Domyślnie akcje FTP mogą odczytywać lub zapisywać pliki o *rozmiarze 50 MB lub mniejszym*. Aby obsłużyć pliki większe niż 50 MB, akcje FTP obsługują [fragmentowanie wiadomości](../logic-apps/logic-apps-handle-large-messages.md). Akcja **Pobierz zawartość pliku** niejawnie używa fragmentowania.

* Wyzwalacze FTP nie obsługują fragmentowania. Podczas żądania zawartości pliku wyzwalacze wybierają tylko pliki o rozmiarze 50 MB lub mniejszym. Aby uzyskać pliki większe niż 50 MB, wykonaj następujące schematy:

  * Użyj wyzwalacza FTP, który zwraca właściwości pliku, na przykład **Gdy plik jest dodawany lub modyfikowany (tylko właściwości).**

  * Postępuj zgodnie z wyzwalaczem z ftp **pobierz zawartość pliku** akcji, która odczytuje cały plik i niejawnie używa fragmentowania.

* Jeśli masz lokalny serwer FTP, należy rozważyć utworzenie [środowiska usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) lub przy użyciu [połączeń hybrydowych usługi Azure App Service,](../app-service/app-service-hybrid-connections.md)które umożliwiają dostęp do lokalnych źródeł danych bez korzystania z lokalnej bramy danych.

## <a name="how-ftp-triggers-work"></a>Jak działają wyzwalacze FTP

Wyzwalacze FTP działają, sondując system plików FTP i szukając dowolnego pliku, który został zmieniony od czasu ostatniej ankiety. Niektóre narzędzia umożliwiają zachowanie sygnatury czasowej po zmianie plików. W takich przypadkach należy wyłączyć tę funkcję, aby wyzwalacz mógł działać. Oto kilka typowych ustawień:

| Klient SFTP | Akcja |
|-------------|--------|
| Winscp | Przejdź do **opcji** > Preferencje**Transfer** > **Edytuj Zachowaj** > **sygnaturę** > **Preferences** > **czasą Wyłącz** |
| Filezilla | Przejdź do **funkcji Transfer** > **Zachowaj sygnatury czasowe przesłanych plików** > **Wyłącz** |
|||

Gdy wyzwalacz znajdzie nowy plik, wyzwalacz sprawdza, czy nowy plik jest ukończony, a nie częściowo zapisany. Na przykład plik może mieć zmiany w toku, gdy wyzwalacz sprawdza serwer plików. Aby uniknąć zwracania częściowo napisanego pliku, wyzwalacz odnotowuje sygnaturę czasową dla pliku, który ma ostatnie zmiany, ale nie zwraca natychmiast tego pliku. Wyzwalacz zwraca plik tylko podczas ponownego sondowania serwera. Czasami to zachowanie może spowodować opóźnienie, które jest maksymalnie dwa razy interwał sondowania wyzwalacza.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Adres serwera hosta FTP i poświadczenia konta

  Złącze FTP wymaga, aby serwer FTP był dostępny z Internetu i skonfigurowany do pracy w trybie *pasywnym.* Poświadczenia umożliwiają aplikacji logiki utworzenie połączenia i dostęp do konta FTP.

* Podstawowa wiedza na temat [tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp do konta FTP. Aby rozpocząć od wyzwalacza FTP, [utwórz pustą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby użyć akcji FTP, uruchom aplikację logiki z innym wyzwalaczem, na przykład **wyzwalaczem cyklu.**

## <a name="connect-to-ftp"></a>Łączenie się z ftp

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i otwórz aplikację logiki w logice App Designer.

1. W przypadku pustych aplikacji logiki `ftp` w polu wyszukiwania wprowadź jako filtr. Z listy **Wyzwalacze** wybierz odpowiedni wyzwalacz.

   — lub —

   W przypadku istniejących aplikacji logiki w ostatnim kroku, w którym chcesz dodać akcję, wybierz pozycję **Nowy krok**, a następnie wybierz pozycję **Dodaj akcję.** W polu wyszukiwania `ftp` wprowadź jako filtr. Z listy **Akcje** wybierz odpowiednią akcję.

   Aby dodać akcję między krokami, przesuń wskaźnik myszy na strzałkę między krokami. Wybierz wyświetlony znak**+** plus ( ), a następnie wybierz pozycję **Dodaj akcję**.

1. Podaj informacje o połączeniu i wybierz pozycję **Utwórz**.

1. Podaj informacje dotyczące wybranego wyzwalacza lub akcji i kontynuuj tworzenie przepływu pracy aplikacji logiki.

## <a name="examples"></a>Przykłady

<a name="file-added-modified"></a>

### <a name="add-ftp-trigger"></a>Dodaj wyzwalacz FTP

Wyzwalacz **Po dodaniu lub zmodyfikowaniu pliku (tylko właściwości)** uruchamia przepływ pracy aplikacji logiki, gdy wyzwalacz wykryje, że plik jest dodawany lub zmieniany na serwerze FTP. Na przykład można dodać warunek, który sprawdza zawartość pliku i decyduje, czy uzyskać tę zawartość, na podstawie tego, czy ta zawartość spełnia określony warunek. Na koniec można dodać akcję, która pobiera zawartość pliku i umieścić tę zawartość w innym folderze na serwerze SFTP.

Na przykład można użyć tego wyzwalacza do monitorowania folderu FTP dla nowych plików opisujących zamówienia klientów. Następnie można użyć akcji FTP, takiej jak **Pobierz metadane pliku,** aby uzyskać właściwości tego nowego pliku, a następnie użyć **polecenia Pobierz zawartość pliku,** aby uzyskać zawartość z tego pliku do dalszego przetwarzania i przechowywać tę kolejność w bazie danych zamówień.

Oto przykład, który pokazuje, jak używać **when plik jest dodawany lub modyfikowany (tylko właściwości)** wyzwalacz.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i otwórz aplikację logiki w logice App Designer, jeśli nie jest już otwarty.

1. W przypadku pustych aplikacji logiki `ftp` w polu wyszukiwania wprowadź jako filtr. W obszarze listy wyzwalaczy wybierz ten wyzwalacz: **Po dodaniu lub zmodyfikowaniu zapisu (tylko właściwości)**

   ![Znajdowanie i wybieranie wyzwalacza FTP](./media/connectors-create-api-ftp/select-ftp-trigger-logic-app.png)

1. Podaj niezbędne szczegóły połączenia, a następnie wybierz pozycję **Utwórz**.

   Domyślnie ten łącznik przenosi pliki w formacie tekstowym. Aby przenieść pliki w formacie binarnym, na przykład gdzie i kiedy jest używane kodowanie, wybierz **Binary Transport**.

   ![Tworzenie połączenia z serwerem FTP](./media/connectors-create-api-ftp/create-ftp-connection-trigger.png)

1. W polu **Folder** zaznacz ikonę folderu, aby wyświetlić listę. Aby znaleźć folder, który chcesz monitorować pod kątem nowych lub**>** edytowanych plików, wybierz strzałkę pod kątem prostym ( ), przejdź do tego folderu, a następnie wybierz folder.

   ![Znajdowanie i wybieranie folderu do monitorowania](./media/connectors-create-api-ftp/select-folder-ftp-trigger.png)

   Wybrany folder pojawi się w polu **Folder.**

   ![Wybrany folder pojawi się we właściwości "Folder"](./media/connectors-create-api-ftp/selected-folder-ftp-trigger.png)

1. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

Teraz, gdy aplikacja logiki ma wyzwalacz, dodaj akcje, które chcesz uruchomić, gdy aplikacja logiki znajdzie nowy lub edytowany plik. W tym przykładzie można dodać akcję FTP, która pobiera nową lub zaktualizowaną zawartość.

<a name="get-content"></a>

### <a name="add-ftp-action"></a>Dodaj akcję FTP

Akcja **Pobierz metadane pliku** pobiera właściwości pliku znajdującego się na serwerze FTP, a akcja Pobierz zawartość **pliku** pobiera zawartość pliku na podstawie informacji o tym pliku na serwerze FTP. Na przykład można dodać wyzwalacz z poprzedniego przykładu i tych akcji, aby uzyskać zawartość pliku po dodaniu lub edycji tego pliku.

1. W obszarze wyzwalacza lub innych akcji wybierz pozycję **Nowy krok**.

1. W polu wyszukiwania `ftp` wprowadź jako filtr. W obszarze listy akcji wybierz tę akcję: **Pobierz metadane pliku**

   ![Wybierz akcję "Pobierz metadane pliku"](./media/connectors-create-api-ftp/select-get-file-metadata-ftp-action.png)

1. Jeśli masz już połączenie z serwerem i kontem FTP, przejdź do następnego kroku. W przeciwnym razie podaj szczegóły niezbędne dla tego połączenia, a następnie wybierz pozycję **Utwórz**.

   ![Tworzenie połączenia z serwerem FTP](./media/connectors-create-api-ftp/create-ftp-connection-action.png)

1. Po **wyświetlenie akcji Pobierz metadane pliku** kliknij wewnątrz pola **Plik,** aby wyświetlić dynamiczną listę zawartości. Teraz można wybrać właściwości dla wyjść z poprzednich kroków. Na dynamicznej liście zawartości w obszarze **Pobierz metadane plików**wybierz **właściwość Lista identyfikatorów plików,** która odwołuje się do kolekcji, w której plik został dodany lub zaktualizowany.

   ![Znajdź i wybierz właściwość "Lista identyfikatorów plików"](./media/connectors-create-api-ftp/select-list-of-files-id-output.png)

   Właściwość **Lista identyfikatorów plików** jest teraz wyświetlana w polu **Plik.**

   ![Wybrana właściwość "Lista identyfikatorów plików"](./media/connectors-create-api-ftp/selected-list-file-ids-ftp-action.png)

1. Teraz dodaj tę akcję FTP: **Pobierz zawartość pliku**

   ![Znajdź i wybierz akcję "Pobierz zawartość pliku"](./media/connectors-create-api-ftp/select-get-file-content-ftp-action.png)

1. Po **wyświetlenie** akcji Pobierz zawartość pliku kliknij wewnątrz pola **Plik,** aby wyświetlić dynamiczną listę zawartości. Teraz można wybrać właściwości dla wyjść z poprzednich kroków. Na dynamicznej liście zawartości w obszarze **Pobierz metadane plików**wybierz właściwość **Id,** która odwołuje się do pliku, który został dodany lub zaktualizowany.

   ![Znajdź i wybierz właściwość "Id"](./media/connectors-create-api-ftp/get-file-content-id-output.png)

   Właściwość **Id** jest teraz wyświetlana w polu **Plik.**

   ![Wybrana właściwość "Id"](./media/connectors-create-api-ftp/selected-get-file-content-id-ftp-action.png)

1. Zapisz aplikację logiki.

## <a name="test-your-logic-app"></a>Testowanie aplikacji logiki

Aby sprawdzić, czy przepływ pracy zwraca oczekiwaną zawartość, dodaj inną akcję, która wysyła zawartość z przekazanego lub zaktualizowanego pliku.

1. W obszarze **Akcja Pobierz zawartość pliku** dodaj akcję, która może wysłać ci zawartość pliku. W tym przykładzie dodano akcję **Wyślij wiadomość e-mail** dla programu Office 365 Outlook.

   ![Dodawanie akcji do wysyłania wiadomości e-mail](./media/connectors-create-api-ftp/select-send-email-action.png)

1. Po wyświetlenie akcji podaj informacje i dołącz właściwości, które chcesz przetestować. Na przykład dołącz **właściwość Zawartość pliku,** która pojawia się na liście zawartości dynamicznej po wybraniu opcji **Zobacz więcej** w sekcji Pobierz zawartość **pliku.**

   ![Podaj informacje o akcji e-mail](./media/connectors-create-api-ftp/selected-send-email-action.png)

1. Zapisz aplikację logiki. Aby uruchomić i wyzwolić aplikację logiki, na pasku narzędzi wybierz pozycję **Uruchom**, a następnie dodaj plik do folderu FTP, który aplikacja logiki monitoruje teraz.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać więcej informacji technicznych na temat tego łącznika, takich jak wyzwalacze, akcje i limity opisane w pliku Swagger łącznika, zobacz [stronę odwołania łącznika](https://docs.microsoft.com/connectors/ftpconnector/).

> [!NOTE]
> W przypadku aplikacji logiki w [środowisku usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)wersja z etykietą ISE tego łącznika używa [limitów komunikatów ŚRODOWISKA ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o innych [łącznikach aplikacji logiki](../connectors/apis-list.md)
