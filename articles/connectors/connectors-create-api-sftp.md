---
title: Łączenie się z kontem SFTP — Azure Logic Apps
description: Automatyzowanie zadań i procesów, których monitorowanie, tworzenie, zarządzanie, wysyłania i odbierania plików na serwerze SFTP za pośrednictwem protokołu SSH przy użyciu usługi Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, klam, LADocs
ms.topic: article
ms.date: 06/18/2019
tags: connectors
ms.openlocfilehash: 5b80339e90947b7cc45b1edb5df00652a13291ac
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67293476"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-azure-logic-apps"></a>Monitorowanie, tworzenie i zarządzanie plikami SFTP przy użyciu usługi Azure Logic Apps

Automatyzowanie zadań, które monitorowania, tworzenie, wysyłanie i odbieranie plików na [Secure File Transfer Protocol (SFTP)](https://www.ssh.com/ssh/sftp/) serwera, możesz rozwijać i automatyzować przepływy pracy integracji przy użyciu usługi Azure Logic Apps i łącznika SFTP. Protokół SFTP jest protokołem sieci, który zapewnia dostęp do plików, transfer plików i zarządzanie plikami w strumieniu żadnych wiarygodnych danych. Poniżej przedstawiono niektóre przykład zadania, które można zautomatyzować:

* Monitor, gdy pliki są dodane lub zmienione.
* Pobieranie, tworzenie, kopiowanie, aktualizacji listy i Usuń pliki.
* Pobierz zawartość pliku i metadanych.
* Wyodrębnij archiwum do folderów.

Możesz użyć wyzwalaczy, które monitorowania zdarzeń na serwerze SFTP i udostępnić dane wyjściowe innych działań. Możesz użyć akcji, które wykonywania różnych zadań na serwerze SFTP. Mogą też istnieć inne akcje w aplikacji logiki, użyć danych wyjściowych z akcji SFTP. Na przykład jeśli regularnie możesz pobrać pliki z serwera SFTP, możesz wysłać alerty e-mail dotyczące tych plików i ich zawartości za pomocą łącznika usługi Office 365 Outlook lub łącznik usługi Outlook.com. Jeśli dopiero zaczynasz pracę z usługi logic apps, zapoznaj się z [co to jest Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="limits"></a>Limits

Łącznik SFTP obsługuje tylko pliki, które są *50 MB lub mniej* i nie obsługuje [segmentu komunikat](../logic-apps/logic-apps-handle-large-messages.md). W przypadku większych plików, użyj [łącznika SFTP-SSH](../connectors/connectors-sftp-ssh.md). Różnice między łącznika SFTP i łącznika SFTP-SSH, można przejrzeć [porównania SFTP-SSH i SFTP](../connectors/connectors-sftp-ssh.md#comparison) w artykule SFTP-protokołu SSH.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* SFTP adres i konta poświadczeń serwera, które umożliwiają dostęp do tego konta SFTP aplikacji logiki. Aby użyć [Secure Shell (SSH)](https://www.ssh.com/ssh/protocol/) protokołu, również muszą mieć dostęp do klucza prywatnego SSH i hasło klucza prywatnego SSH.

  > [!NOTE]
  >
  > Łącznik SFTP obsługuje formatach klucza prywatnego: OpenSSH, ssh.com i programu PuTTY
  >
  > Podczas tworzenia aplikacji logiki, po dodaniu SFTP wyzwalacz lub akcję, którą chcesz, musisz podać informacje o połączeniu na serwerze SFTP. 
  > Jeśli używasz klucza prywatnego SSH, upewnij się, że ***kopiowania*** klucza z protokołu SSH pliku klucza prywatnego, i ***Wklej*** tego klucza do szczegółów połączenia ***nie ręcznie wprowadzić lub edytować klucza***, co może spowodować połączenia nie powiedzie się. 
  > Aby uzyskać więcej informacji zobacz kolejnych krokach w tym artykule.

* Podstawową wiedzę na temat o [sposób tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp do konta SFTP. Można uruchomić z wyzwalaczem SFTP [Tworzenie pustej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby użyć akcji SFTP, uruchom aplikację logiki za pomocą wyzwalacza innego, na przykład, **cyklu** wyzwalacza.

## <a name="how-sftp-triggers-work"></a>Jak SFTP wyzwala pracy

SFTP pracy wyzwalacze sondowania w systemie plików SFTP i przejrzysz dla każdego pliku, który został zmieniony od czasu ostatniego sondowania. Niektóre narzędzia pozwalają zachować sygnaturę czasową, gdy zmienią się pliki. W takich przypadkach należy wyłączyć tę funkcję, dzięki czemu można pracować wyzwalacza. Poniżej przedstawiono niektóre typowe ustawienia:

| Klient protokołu SFTP | Akcja |
|-------------|--------|
| Winscp | Przejdź do **opcje** > **preferencje** > **transferu** > **Edytuj**  >  **Zachować sygnatury czasowej** > **wyłączone** |
| FileZilla | Przejdź do **transferu** > **zachować znacznikami czasu plików przeniesionych** > **wyłączone** |
|||

Jeśli wyzwalacz wykryje nowy plik, wyzwalacz sprawdzi, czy nowy plik jest pełny i niezapisane częściowo. Na przykład plik może mieć zmiany w toku, gdy wyzwalacz sprawdza, czy serwer plików. Aby uniknąć, zwracając częściowo napisane pliku, wyzwalacz — informacje o sygnaturę czasową dla pliku, który zawiera ostatnie zmiany, ale nie natychmiast przywrócić ten plik. Wyzwalacz zwraca plik tylko wtedy, gdy ponownie sondowania serwera. Czasami to zachowanie może powodować opóźnienia, która jest maksymalnie dwa razy tego wyzwalacza interwał sondowania.

## <a name="connect-to-sftp"></a>Nawiązać połączenie SFTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i Otwórz swoją aplikację logiki w Projektancie aplikacji logiki, jeśli nie otwarto już.

1. Puste logic apps w polu wyszukiwania wprowadź "sftp" jako filtr. W obszarze listy wyzwalaczy wybierz wyzwalacz, który ma.

   —lub—

   Dla istniejących aplikacji logiki w ostatnim kroku, które chcesz dodać akcję, wybierz **nowy krok**. W polu wyszukiwania wprowadź "sftp" jako filtr. W obszarze listy akcji wybierz akcję, którą chcesz.

   Aby dodać akcję między krokami, wskaźnik myszy nad strzałką znajdującą się między krokami. Wybierz znak plus ( **+** ) pojawia się, a następnie wybierz **Dodaj akcję**.

1. Podaj odpowiednie szczegóły połączenia.

   > [!IMPORTANT]
   >
   > Po wprowadzeniu klucza prywatnego SSH w **prywatny klucz SSH** właściwość, należy wykonać następujące dodatkowe czynności, które pomagają, pamiętaj o podaniu wartości kompletny i poprawny dla tej właściwości. 
   > Nieprawidłowy klucz powoduje, że połączenie nie powiedzie się.

   Chociaż można używać dowolnego edytora tekstów, poniżej przedstawiono kroki próbki, które pokazują, jak poprawnie skopiuj i Wklej swój klucz przy użyciu Notepad.exe jako przykład.

   1. Otwórz pliku klucza prywatnego SSH w edytorze tekstów. Następujące kroki, np. za pomocą Notatnika.

   1. W programie Notatnik **Edytuj** menu, wybierz opcję **Zaznacz wszystko**.

   1. Wybierz **Edytuj** > **kopiowania**.

   1. Wklej w SFTP wyzwalacza lub akcji został dodany, *pełną* klucza, zostały skopiowane do **prywatny klucz SSH** właściwość, która obsługuje wiele wierszy. ***Upewnij się, że możesz wkleić*** klucza. ***Nie ręcznie wprowadzić lub edytować klucza***.

1. Po zakończeniu wprowadzania szczegóły połączenia, wybierz **Utwórz**.

1. Podaj odpowiednie szczegóły wybranego wyzwalacza lub akcji i kontynuuj tworzenie przepływu pracy aplikacji logiki.

## <a name="examples"></a>Przykłady

<a name="file-add-modified"></a>

### <a name="sftp-trigger-when-a-file-is-added-or-modified"></a>Wyzwalacz protokołu SFTP: Po dodaniu lub zmodyfikowaniu pliku

Ten wyzwalacz jest uruchamiany przepływ pracy aplikacji logiki, gdy plik zostanie dodane lub zmienione na serwerze SFTP. Na przykład można dodać warunek, który sprawdza, czy zawartość pliku i pobiera zawartość, w oparciu o tego, czy zawartość spełnia określony warunek. Następnie można dodać akcję, która pobiera zawartość pliku i umieszcza tę zawartość w folderze na serwerze SFTP.

**Przykład Enterprise**: Tego wyzwalacza można używać do monitorowania folderu SFTP dla nowych plików, które reprezentują zamówienia. Można następnie użyć akcji SFTP takich jak **Pobierz zawartość pliku** , Pobierz zawartość w kolejności do dalszego przetwarzania i przechowywania tej kolejności w bazie danych zamówień.

<a name="get-content"></a>

### <a name="sftp-action-get-content"></a>Akcja SFTP: Pobieranie zawartości

Ta akcja pobiera zawartość z pliku na serwer SFTP. Na przykład można dodać wyzwalacza z poprzedniego przykładu i warunek, który musi spełniać zawartość pliku. Jeśli warunek jest spełniony, można uruchomić akcję, która pobiera zawartość.

## <a name="connector-reference"></a>Dokumentacja łączników

Szczegółowe informacje techniczne dotyczące wyzwalaczy, akcje i ograniczeń, które opisano przez standard OpenAPI łącznika (dawniej Swagger) opis, przejrzyj łącznika [strona referencyjna](/connectors/sftpconnector/).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o innych [łączników Logic Apps](../connectors/apis-list.md)
