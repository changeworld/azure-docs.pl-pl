---
title: Połączenie z kontem SFTP z usługi Azure Logic Apps | Dokumentacja firmy Microsoft
description: Automatyzowanie zadań i przepływów pracy, umożliwiające monitorowanie, tworzenie, zarządzanie, wysyłania i odbierania plików na serwerze SFTP przy użyciu usługi Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
tags: connectors
ms.date: 09/24/2018
ms.openlocfilehash: cfee4f06479d2504b88f4a5d5a0a2417154e3b03
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2018
ms.locfileid: "47065110"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-azure-logic-apps-and-sftp-ssh-connector"></a>Monitorowanie, tworzenie i zarządzanie plikami SFTP za pomocą łącznika usługi Azure Logic Apps i SFTP — SSH

Korzystając z usługi Azure Logic Apps i łącznika SFTP-SSH, można utworzyć zautomatyzowanym zadaniom i przepływy pracy monitorowania, tworzenie, wysyłanie i odbieranie plików za pośrednictwem Twojego konta na [SFTP](https://www.ssh.com/ssh/sftp/) serwerem oraz inne czynności, na przykład:

* Monitor, gdy pliki są dodane lub zmienione.
* Pobieranie, tworzenie, kopiowanie, Zmień nazwę, aktualizacji listy i Usuń pliki.
* Utwórz folder.
* Pobierz zawartość pliku i metadanych.
* Wyodrębnij archiwum do folderów.

Możesz użyć wyzwalaczy, które uzyskiwanie odpowiedzi z serwera SFTP oraz udostępnić dane wyjściowe do innych działań. Akcje w aplikacjach logic apps umożliwia wykonywanie zadań z plikami na serwerze SFTP. Mogą też istnieć inne akcje użyć danych wyjściowych z akcji SFTP. Na przykład jeśli regularnie możesz pobrać pliki z serwera SFTP, możesz wysłać pocztą e-mail informacje dotyczące tych plików i ich zawartości za pomocą łącznika usługi Office 365 Outlook lub łącznik usługi Outlook.com.
Jeśli dopiero zaczynasz pracę z usługi logic apps, zapoznaj się z [co to jest Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="sftp-ssh-versus-sftp"></a>SFTP-SSH i SFTP

Poniżej przedstawiono kilka podstawowych różnic między łącznika SFTP-SSH i [SFTP](../connectors/connectors-create-api-sftp.md) łącznika. Łącznik SFTP-SSH udostępnia te możliwości:

* Używa <a href="https://github.com/sshnet/SSH.NET" target="_blank"> **SSH.NET** </a> biblioteki, która jest biblioteką Secure Shell (SSH) typu open source dla platformy .NET.

* Zapewnia obsługę dużych plików do **1 GB**. Łącznik można Odczyt lub zapis plików znajdujących się w rozmiarze do 1 GB.

* Udostępnia **Utwórz folder** akcji, która tworzy folder w określonej ścieżce na serwerze SFTP.

* Udostępnia **zmiany nazwy pliku** akcji, która zmienia nazwę pliku na serwerze SFTP.

* Buforuje połączenia do serwera SFTP, który zapewnia lepszą wydajność i zmniejsza liczbę prób nawiązania połączenia na serwerze. 

  Możesz kontrolować czas trwania buforowania połączenia, konfigurując <a href="http://man.openbsd.org/sshd_config#ClientAliveInterval" target="_blank"> **ClientAliveInterval** </a> właściwości na serwerze SFTP. 

## <a name="how-trigger-polling-works"></a>Jak wyzwolić działania sondowania

Wyzwalacze SFTP-SSH działają przez sondowanie system plików SFTP i szukasz każdego pliku, który został zmieniony od czasu ostatniego sondowania. Niektóre narzędzia pozwalają zachować sygnaturę czasową, gdy zmieniają się pliki, więc w takich przypadkach należy wyłączyć tę funkcję dla wyzwalacza do pracy. Poniżej przedstawiono niektóre typowe ustawienia:

| Klient protokołu SFTP | Akcja | 
|-------------|--------| 
| Winscp | Preferencje → opcje... Edytuj transferu → →... Wyłącz → zachować sygnatury czasowej → |
| FileZilla | Wyłącz transferu → Zachowaj znacznikami czasu plików przeniesionych → | 
||| 

Jeśli wyzwalacz wykryje nowy plik, wyzwalacz sprawdzi, czy nowy plik jest pełny i niezapisane częściowo. Na przykład plik może mieć zmiany w toku, gdy wyzwalacz sprawdza, czy serwer plików. Aby uniknąć, zwracając częściowo napisane pliku, wyzwalacz — informacje o sygnaturę czasową dla pliku, który zawiera ostatnie zmiany, ale nie natychmiast przywrócić ten plik. Wyzwalacz zwraca plik tylko wtedy, gdy ponownie sondowania serwera. Czasami to zachowanie może powodować opóźnienia, która jest maksymalnie dwa razy tego wyzwalacza interwał sondowania. 

Podczas żądania zawartości pliku, wyzwalacz nie pobierać pliki o rozmiarze większym niż 50 MB. Aby pobrać pliki o rozmiarze większym niż 50 MB, należy korzystać z tego wzoru:

* Użyj wyzwalacz, który zwraca wartość właściwości pliku, taką jak **gdy plik jest dodawany lub modyfikowany (tylko właściwości)**. 
* Postępuj zgodnie z wyzwalacza z akcji, która odczytuje plik pełną, takich jak **Pobierz zawartość pliku przy użyciu ścieżki**.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>. 

* SFTP hosta adres i konta poświadczeń serwera, które Autoryzuj aplikację logiki, aby utworzyć połączenie i dostęp do tego konta SFTP.

  Skopiuj i Wklej pełną zawartość prywatny klucz SSH do **prywatny klucz SSH** właściwości, postępując zgodnie z wielowierszowym formacie. 
  Poniżej przedstawiono przykładowe kroki, pokazujące, jak zapewnić prywatny klucz SSH przy użyciu Notepad.exe:
    
  1. Otwórz plik klucza prywatnego SSH w Notepad.exe
  2. Na **Edytuj** menu, wybierz opcję **Zaznacz wszystko**.
  3. Wybierz **Edytuj** > **kopiowania**.
  4. Po utworzeniu połączenia w **prywatny klucz SSH** właściwości, Wklej klucz. Nie należy ręcznie ich edytować **prywatny klucz SSH** właściwości.

     Łącznik używa biblioteki SSH.NET, która obsługuje formatach klucza prywatnego SSH i tylko algorytmy MD5 odcisk palca:

     * RSA 
     * DSA

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