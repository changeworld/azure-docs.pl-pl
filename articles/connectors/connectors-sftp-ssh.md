---
title: Połączyć się z serwerem SFTP przy użyciu protokołu SSH — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Automatyzowanie zadań, umożliwiające monitorowanie, tworzenie, zarządzanie, wysyłania i odbierania plików na serwerze SFTP przy użyciu protokołu SSH i Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, LADocs
ms.topic: article
tags: connectors
ms.date: 01/15/2019
ms.openlocfilehash: 660d785baf12052bddf5206d8641116c9ac606aa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60537704"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-ssh-and-azure-logic-apps"></a>Monitorowanie, tworzenie i zarządzanie plikami SFTP przy użyciu protokołu SSH i Azure Logic Apps

Automatyzowanie zadań, które monitorowania, tworzenie, wysyłanie i odbieranie plików na [Secure File Transfer Protocol (SFTP)](https://www.ssh.com/ssh/sftp/) serwera za pomocą [Secure Shell (SSH)](https://www.ssh.com/ssh/protocol/) protokołu, możesz rozwijać i automatyzować integracji przepływy pracy za pomocą usługi Azure Logic Apps i łącznika SFTP-protokołu SSH. Protokół SFTP jest protokołem sieci, który zapewnia dostęp do plików, transfer plików i zarządzanie plikami w strumieniu żadnych wiarygodnych danych. Poniżej przedstawiono niektóre przykład zadania, które można zautomatyzować: 

* Monitor, gdy pliki są dodane lub zmienione.
* Pobieranie, tworzenie, kopiowanie, Zmień nazwę, aktualizacji listy i Usuń pliki.
* Tworzenie folderów.
* Pobierz zawartość pliku i metadanych.
* Wyodrębnij archiwum do folderów.

Możesz użyć wyzwalaczy, które monitorowania zdarzeń na serwerze SFTP i udostępnić dane wyjściowe innych działań. Możesz użyć akcji, które wykonywania różnych zadań na serwerze SFTP. Mogą też istnieć inne akcje w aplikacji logiki, użyć danych wyjściowych z akcji SFTP. Na przykład jeśli regularnie możesz pobrać pliki z serwera SFTP, możesz wysłać alerty e-mail dotyczące tych plików i ich zawartości za pomocą łącznika usługi Office 365 Outlook lub łącznik usługi Outlook.com.
Jeśli dopiero zaczynasz pracę z usługi logic apps, zapoznaj się z [co to jest Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="limits"></a>Limits

* Akcje SFTP-SSH może odczytać lub zapisywać pliki, które są *1 GB lub mniej* za zarządzanie danymi jako *50 MB fragmentów*, nie 1 GB elementy.

* Dla plików *większą niż 1 GB*, można użyć akcji [segmentu komunikat](../logic-apps/logic-apps-handle-large-messages.md). Obecnie usługa SFTP-SSH wyzwalaczy nie obsługują segmentu.

Więcej różnic, można przejrzeć [porównania SFTP-SSH i SFTP](#comparison) później w następnej sekcji.

<a name="comparison"></a>

## <a name="compare-sftp-ssh-versus-sftp"></a>Porównaj SFTP-SSH i SFTP

Poniżej przedstawiono inne podstawowe różnice między łącznik SFTP-SSH i łącznika SFTP, w którym łącznik SFTP-SSH ma te możliwości:

* Używa <a href="https://github.com/sshnet/SSH.NET" target="_blank"> **SSH.NET** </a> biblioteki, która to biblioteka Secure Shell (SSH) typu open source, która obsługuje platformy .NET.

  > [!NOTE]
  >
  > SFTP-SSH łącznik obsługuje *tylko* te klucze prywatne, formatów, algorytmów i odcisków palców:
  >
  > * **Formaty klucza prywatnego**: RSA (Rivest Shamir Adleman) oraz klucze DSA (algorytmu Digital Signature Algorithm) w formacie OpenSSH i ssh.com
  > * **Algorytmy szyfrowania**: EDE3-DES-CBC, DES-EDE3-CFB DES-CBC, AES-128-CBC, 192-AES-CBC i AES-256-CBC
  > * **Odcisk palca**: MD5

* Akcje może odczytać lub zapisać pliki *do 1 GB* w porównaniu do łącznika SFTP, ale obsługuje dane w 50 MB fragmentów, nie 1 GB fragmentów. W przypadku plików większych niż 1 GB, można również użyć akcji [segmentu komunikat](../logic-apps/logic-apps-handle-large-messages.md). Obecnie usługa SFTP-SSH wyzwalaczy nie obsługują segmentu.

* Udostępnia **Utwórz folder** akcji, która tworzy folder w określonej ścieżce na serwerze SFTP.

* Udostępnia **zmiany nazwy pliku** akcji, która zmienia nazwę pliku na serwerze SFTP.

* Pamięci podręczne połączenie z serwerem SFTP *przez maksymalnie godzinę*, który zapewnia lepszą wydajność i zmniejsza liczbę prób połączenia z serwerem. Aby ustawić czas trwania tego zachowania buforowania, Edytuj <a href="https://man.openbsd.org/sshd_config#ClientAliveInterval" target="_blank"> **ClientAliveInterval** </a> właściwości w konfiguracji SSH na serwerze SFTP.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>. 

* SFTP adres i konta poświadczeń serwera, które umożliwiają dostęp do tego konta SFTP aplikacji logiki. Należy również dostęp do klucza prywatnego SSH i hasło klucza prywatnego SSH. 

  > [!IMPORTANT]
  >
  > SFTP-SSH łącznik obsługuje *tylko* te formaty klucza prywatnego, algorytmów i odcisków palców:
  > 
  > * **Formaty klucza prywatnego**: RSA (Rivest Shamir Adleman) oraz klucze DSA (algorytmu Digital Signature Algorithm) w formacie OpenSSH i ssh.com
  > * **Algorytmy szyfrowania**: EDE3-DES-CBC, DES-EDE3-CFB DES-CBC, AES-128-CBC, 192-AES-CBC i AES-256-CBC
  > * **Odcisk palca**: MD5
  >
  > Podczas tworzenia aplikacji logiki, po dodaniu SFTP-SSH wyzwalacza lub akcji, które chcesz, musisz podać informacje o połączeniu na serwerze SFTP. 
  > Jeśli używasz klucza prywatnego SSH, upewnij się, że ***kopiowania*** klucza z protokołu SSH pliku klucza prywatnego, i ***Wklej*** tego klucza do szczegółów połączenia ***nie ręcznie wprowadzić lub edytować klucza***, co może spowodować połączenia nie powiedzie się. 
  > Aby uzyskać więcej informacji zobacz kolejnych krokach w tym artykule.

* Podstawową wiedzę na temat o [sposób tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp do konta SFTP. Można uruchomić przy użyciu protokołu SFTP-SSH wyzwalacz, [Tworzenie pustej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby użyć akcji SFTP-SSH, uruchom aplikację logiki za pomocą wyzwalacza innego, na przykład, **cyklu** wyzwalacza.

## <a name="connect-to-sftp-with-ssh"></a>Nawiązać połączenie SFTP przy użyciu protokołu SSH

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i Otwórz swoją aplikację logiki w Projektancie aplikacji logiki, jeśli nie otwarto już.

1. Puste logic apps, w polu wyszukiwania wprowadź "sftp ssh" jako filtr. W obszarze listy wyzwalaczy wybierz wyzwalacz, który ma. 

   — lub —

   Dla istniejących aplikacji logiki w ostatnim kroku, które chcesz dodać akcję, wybierz **nowy krok**. 
   W polu wyszukiwania wpisz "sftp ssh" jako filtr. 
   W obszarze listy akcji wybierz akcję, którą chcesz.

   Aby dodać akcję między krokami, wskaźnik myszy nad strzałką znajdującą się między krokami. 
   Wybierz znak plus (**+**) pojawia się, a następnie wybierz **Dodaj akcję**.

1. Podaj odpowiednie szczegóły połączenia.

   > [!IMPORTANT] 
   >
   > Po wprowadzeniu klucza prywatnego SSH w **prywatny klucz SSH** właściwość, należy wykonać następujące dodatkowe czynności, które pomagają, pamiętaj o podaniu wartości kompletny i poprawny dla tej właściwości. 
   > Nieprawidłowy klucz powoduje, że połączenie nie powiedzie się.
   
   Chociaż można używać dowolnego edytora tekstów, poniżej przedstawiono kroki próbki, które pokazują, jak poprawnie skopiuj i Wklej swój klucz przy użyciu Notepad.exe jako przykład.
    
   1. Otwórz pliku klucza prywatnego SSH w edytorze tekstów. 
   Następujące kroki, np. za pomocą Notatnika.

   1. W programie Notatnik firmy **Edytuj** menu, wybierz opcję **Zaznacz wszystko**.

   1. Wybierz **Edytuj** > **kopiowania**.

   1. Wklej w SFTP SSH wyzwalacza lub akcji został dodany, *pełną* klucza, zostały skopiowane do **prywatny klucz SSH** właściwość, która obsługuje wiele wierszy. 
   ***Upewnij się, że możesz wkleić*** klucza. ***Nie ręcznie wprowadzić lub edytować klucza***.

1. Po zakończeniu wprowadzania szczegóły połączenia, wybierz **Utwórz**.

1. Teraz podaj odpowiednie szczegóły wybranego wyzwalacza lub akcji i kontynuuj tworzenie przepływu pracy aplikacji logiki.

## <a name="trigger-limits"></a>Ograniczenia wyzwalacza

Wyzwalacze SFTP-SSH działają przez sondowanie system plików SFTP i szukasz każdego pliku, który został zmieniony od czasu ostatniego sondowania. Niektóre narzędzia pozwalają zachować sygnaturę czasową, gdy zmienią się pliki. W takich przypadkach należy wyłączyć tę funkcję, dzięki czemu można pracować wyzwalacza. Poniżej przedstawiono niektóre typowe ustawienia:

| Klient protokołu SFTP | Akcja | 
|-------------|--------| 
| Winscp | Przejdź do **opcje** > **preferencje** > **transferu** > **Edytuj**  >  **Zachować sygnatury czasowej** > **wyłączone** |
| FileZilla | Przejdź do **transferu** > **zachować znacznikami czasu plików przeniesionych** > **wyłączone** | 
||| 

Jeśli wyzwalacz wykryje nowy plik, wyzwalacz sprawdzi, czy nowy plik jest pełny i niezapisane częściowo. Na przykład plik może mieć zmiany w toku, gdy wyzwalacz sprawdza, czy serwer plików. Aby uniknąć, zwracając częściowo napisane pliku, wyzwalacz — informacje o sygnaturę czasową dla pliku, który zawiera ostatnie zmiany, ale nie natychmiast przywrócić ten plik. Wyzwalacz zwraca plik tylko wtedy, gdy ponownie sondowania serwera. Czasami to zachowanie może powodować opóźnienia, która jest maksymalnie dwa razy tego wyzwalacza interwał sondowania. 

Podczas żądania zawartości pliku, wyzwalaczy nie uzyskasz plików większych niż 50 MB. Aby pobrać pliki większe niż 50 MB, należy korzystać z tego wzoru: 

* Użyj wyzwalacz, który zwraca wartość właściwości pliku, taką jak **gdy plik jest dodawany lub modyfikowany (tylko właściwości)**.

* Postępuj zgodnie z wyzwalacza z akcji, która odczytuje plik pełną, takich jak **Pobierz zawartość pliku przy użyciu ścieżki**, i akcji, użyj [segmentu komunikat](../logic-apps/logic-apps-handle-large-messages.md).

## <a name="examples"></a>Przykłady

<a name="file-added-modified"></a>

### <a name="sftp---ssh-trigger-when-a-file-is-added-or-modified"></a>SFTP — wyzwalanie SSH: Po dodaniu lub zmodyfikowaniu pliku

Ten wyzwalacz jest uruchamiany przepływ pracy aplikacji logiki, gdy plik zostanie dodane lub zmienione na serwerze SFTP. Na przykład można dodać warunek, który sprawdza, czy zawartość pliku i pobiera zawartość, w oparciu o tego, czy zawartość spełnia określony warunek. Następnie można dodać akcję, która pobiera zawartość pliku i umieszcza tę zawartość w folderze na serwerze SFTP. 

**Przykład Enterprise**: Tego wyzwalacza można używać do monitorowania folderu SFTP dla nowych plików, które reprezentują zamówienia. Można następnie użyć akcji SFTP takich jak **Pobierz zawartość pliku** , Pobierz zawartość w kolejności do dalszego przetwarzania i przechowywania tej kolejności w bazie danych zamówień.

Podczas żądania zawartości pliku, wyzwalaczy nie uzyskasz plików większych niż 50 MB. Aby pobrać pliki większe niż 50 MB, należy korzystać z tego wzoru: 

* Użyj wyzwalacz, który zwraca wartość właściwości pliku, taką jak **gdy plik jest dodawany lub modyfikowany (tylko właściwości)**.

* Postępuj zgodnie z wyzwalacza z akcji, która odczytuje plik pełną, takich jak **Pobierz zawartość pliku przy użyciu ścieżki**, i akcji, użyj [segmentu komunikat](../logic-apps/logic-apps-handle-large-messages.md).

<a name="get-content"></a>

### <a name="sftp---ssh-action-get-content-using-path"></a>SFTP - SSH akcji: Pobierz zawartość przy użyciu ścieżki

Ta akcja pobiera zawartość z pliku na serwer SFTP. Na przykład można dodać wyzwalacza z poprzedniego przykładu i warunek, który musi spełniać zawartość pliku. Jeśli warunek jest spełniony, można uruchomić akcję, która pobiera zawartość. 

Podczas żądania zawartości pliku, wyzwalaczy nie uzyskasz plików większych niż 50 MB. Aby pobrać pliki większe niż 50 MB, należy korzystać z tego wzoru: 

* Użyj wyzwalacz, który zwraca wartość właściwości pliku, taką jak **gdy plik jest dodawany lub modyfikowany (tylko właściwości)**.

* Postępuj zgodnie z wyzwalacza z akcji, która odczytuje plik pełną, takich jak **Pobierz zawartość pliku przy użyciu ścieżki**, i akcji, użyj [segmentu komunikat](../logic-apps/logic-apps-handle-large-messages.md).

## <a name="connector-reference"></a>Dokumentacja łączników

Szczegółowe informacje techniczne dotyczące wyzwalaczy, akcje i ograniczeń, które opisano przez standard OpenAPI łącznika (dawniej Swagger) opis, przejrzyj łącznika [strona referencyjna](/connectors/sftpconnector/).

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o innych [łączników Logic Apps](../connectors/apis-list.md)
