---
title: Nawiązywanie połączenia z serwerem SFTP przy użyciu protokołu SSH-Azure Logic Apps
description: Automatyzowanie zadań, które monitorują, tworzą, zarządzają, wysyłają i odbierają pliki dla serwera SFTP przy użyciu protokołu SSH i Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, klam, LADocs
ms.topic: article
ms.date: 06/18/2019
tags: connectors
ms.openlocfilehash: a48ba0d2d691314a1ca7c91ac7ae27b62fbb379b
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825237"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-ssh-and-azure-logic-apps"></a>Monitorowanie i tworzenie plików SFTP oraz zarządzanie nimi za pomocą protokołów SSH i Azure Logic Apps

Aby zautomatyzować zadania, które monitorują, tworzą, wysyłają i odbierają pliki na serwerze [bezpiecznym protokół transferu plików (SFTP)](https://www.ssh.com/ssh/sftp/) przy użyciu protokołu [Secure Shell (SSH)](https://www.ssh.com/ssh/protocol/) , można tworzyć i automatyzować przepływy pracy integracji przy użyciu Azure Logic Apps i SFTP-SSH Łącznik. SFTP to protokół sieciowy, który zapewnia dostęp do plików, transfer plików i zarządzanie plikami za pośrednictwem dowolnego niezawodnego strumienia danych. Poniżej przedstawiono kilka przykładowych zadań, które można zautomatyzować:

* Monitoruj, gdy pliki są dodawane lub zmieniane.
* Pobieranie, tworzenie, kopiowanie, zmienianie nazwy, aktualizowanie, wyświetlanie i usuwanie plików.
* Tworzenie folderów.
* Pobieranie zawartości i metadanych pliku.
* Wyodrębnij archiwa do folderów.

Można użyć wyzwalaczy, które monitorują zdarzenia na serwerze SFTP i udostępniają dane wyjściowe dla innych akcji. Możesz użyć akcji, które wykonują różne zadania na serwerze SFTP. Możesz również mieć inne akcje w aplikacji logiki, używając danych wyjściowych z akcji SFTP. Na przykład w przypadku regularnego pobierania plików z serwera SFTP można wysyłać alerty e-mail dotyczące tych plików i ich zawartości przy użyciu łącznika programu Office 365 Outlook lub łącznika Outlook.com. Jeśli jesteś nowym sposobem logiki aplikacji, zapoznaj [się z tematem Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

Aby uzyskać różnice między łącznikiem protokołu SFTP-SSH a łącznikiem SFTP, zapoznaj się z sekcją [porównanie protokołu SFTP-SSH i SFTP](#comparison) w dalszej części tego tematu.

## <a name="limits"></a>Limity

* Domyślnie działania protokołu SFTP-SSH mogą odczytywać lub zapisywać pliki, które są *1 GB lub mniejsze* , ale tylko w przypadku fragmentów *15 MB* naraz. Aby obsłużyć pliki o rozmiarze większym niż 15 MB, operacje SFTP-SSH obsługują [fragmenty komunikatów](../logic-apps/logic-apps-handle-large-messages.md), z wyjątkiem akcji Kopiuj plik, która może obsługiwać tylko 15 MB plików. Akcja **Pobierz zawartość pliku** niejawnie używa fragmentacji komunikatów.

* Protokół SFTP-SSH nie obsługuje fragmentów. Podczas żądania zawartości pliku wyzwalane są tylko pliki o rozmiarze 15 MB lub mniejszej. Aby uzyskać pliki o rozmiarze większym niż 15 MB, użyj tego wzorca:

  * Użyj wyzwalacza SFTP-SSH, który zwraca właściwości pliku, na przykład **gdy plik jest dodawany lub modyfikowany (tylko właściwości)** .

  * Śledź wyzwalacz z akcją **Pobierz zawartość pliku** SFTP-SSH, która odczytuje kompletny plik i niejawnie używa fragmenty komunikatów.

<a name="comparison"></a>

## <a name="compare-sftp-ssh-versus-sftp"></a>Porównanie protokołów SFTP-SSH i SFTP

Poniżej przedstawiono inne kluczowe różnice między łącznikiem SFTP-SSH a łącznikiem SFTP, gdzie łącznik SFTP-SSH ma następujące możliwości:

* Używa [biblioteki SSH.NET](https://github.com/sshnet/SSH.NET), która jest biblioteką Secure Shell (SSH), która obsługuje platformę .NET.

* Domyślnie działania protokołu SFTP-SSH mogą odczytywać lub zapisywać pliki, które są *1 GB lub mniejsze* , ale tylko w przypadku fragmentów *15 MB* naraz. Aby obsłużyć pliki o rozmiarze większym niż 15 MB, w akcjach SFTP-SSH można używać [fragmentów komunikatów](../logic-apps/logic-apps-handle-large-messages.md). Do przekazywania dużych plików wymagane są również uprawnienia do odczytu i zapisu. Jednak akcja Kopiuj plik obsługuje tylko 15 MB plików, ponieważ ta akcja nie obsługuje fragmentacji komunikatów. Protokół SFTP-SSH nie obsługuje fragmentów.

* Udostępnia akcję **Utwórz folder** , która tworzy folder w określonej ścieżce na serwerze SFTP.

* Udostępnia akcję **zmiany nazwy pliku** , która zmienia nazwę pliku na serwerze SFTP.

* Buforuje połączenie z serwerem SFTP *przez maksymalnie 1 godzinę*, co zwiększa wydajność i zmniejsza liczbę prób nawiązania połączenia z serwerem. Aby ustawić czas trwania tego zachowania buforowania, Edytuj Właściwość [**ClientAliveInterval**](https://man.openbsd.org/sshd_config#ClientAliveInterval) w konfiguracji SSH na serwerze SFTP.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Adres serwera SFTP i poświadczenia konta, które umożliwiają aplikacji logiki dostęp do Twojego konta SFTP. Wymagany jest również dostęp do prywatnego klucza SSH oraz hasła prywatnego klucza SSH. Aby można było używać fragmentów podczas przekazywania dużych plików, wymagane są uprawnienia do odczytu i zapisu.

  > [!IMPORTANT]
  >
  > Łącznik SFTP-SSH obsługuje *tylko* te formaty kluczy prywatnych, algorytmy i odciski palców:
  >
  > * **Formaty kluczy prywatnych**: klucze RSA (Rivest Shamir Adleman) i DSA (algorytm podpisywania cyfrowego) w formatach OpenSSH i SSH.com. Jeśli klucz prywatny jest w formacie. PPK), najpierw [przekonwertuj klucz na format pliku OpenSSH (PEM)](#convert-to-openssh).
  >
  > * **Algorytmy szyfrowania**: des-EDE3-CBC, des-EDE3-CFB, des-CBC, aes-128-CBC, AES-192-CBC i AES-256-CBC
  >
  > * **Odcisk palca**: MD5
  >
  > Po dodaniu wyzwalacza SFTP-SSH lub akcji do aplikacji logiki należy podać informacje o połączeniu dla serwera SFTP. Po podaniu klucza prywatnego SSH dla tego połączenia ***nie wprowadzaj ręcznie ani nie edytuj klucza***, co może spowodować niepowodzenie połączenia. Zamiast tego należy ***skopiować klucz*** z pliku prywatnego klucza SSH i ***wkleić*** go do szczegółów połączenia. 
  > Aby uzyskać więcej informacji, zobacz sekcję [łączenie się](#connect) z PROTOKOŁem SSH w dalszej części tego artykułu.

* Podstawowa wiedza [na temat tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp do konta SFTP. Aby rozpocząć pracę z wyzwalaczem SFTP-SSH, [Utwórz pustą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby użyć akcji SFTP-SSH, uruchom aplikację logiki z innym wyzwalaczem, na przykład wyzwalaczem **cyklu** .

## <a name="how-sftp-ssh-triggers-work"></a>Jak działają wyzwalacze SFTP-SSH

Protokół SFTP-SSH wyzwalacze działają przez sondowanie systemu plików SFTP i wyszukiwanie plików, które zostały zmienione od czasu ostatniego sondowania. Niektóre narzędzia pozwalają zachować sygnaturę czasową, gdy pliki zmienią się. W takich przypadkach należy wyłączyć tę funkcję, aby wyzwalacz mógł funkcjonować. Poniżej przedstawiono niektóre typowe ustawienia:

| Klient SFTP | Akcja |
|-------------|--------|
| WinSCP | Przejdź do **opcji opcje** > **preferencje** > **transfer** > **edycja** > **Zachowaj sygnaturę czasową** > **Wyłącz** |
| FileZilla | Przejdź do obszaru **Transfer** > **zachować sygnatury czasowe transferowanych plików** > **wyłączyć** |
|||

Gdy wyzwalacz odnajdzie nowy plik, wyzwalacz sprawdza, czy nowy plik jest zakończony i nie jest częściowo zapisany. Na przykład plik może mieć zmiany w toku, gdy wyzwalacz sprawdza serwer plików. Aby uniknąć powrotu częściowo zapisywanego pliku, wyzwalacz odnotowuje sygnaturę czasową dla pliku, który ma ostatnio wprowadzone zmiany, ale nie zwraca natychmiast tego pliku. Wyzwalacz zwraca plik tylko wtedy, gdy ponownie sonduje serwer. Czasami takie zachowanie może spowodować opóźnienie, który jest maksymalnie dwa razy interwał sondowania wyzwalacza.

<a name="convert-to-openssh"></a>

## <a name="convert-putty-based-key-to-openssh"></a>Konwertuj wyskakujące klucze na OpenSSH

Jeśli klucz prywatny jest w formacie pobierania, który używa rozszerzenia nazwy pliku. ppk (podano klucz prywatny), najpierw przekonwertuj klucz do formatu OpenSSH, który używa rozszerzenia nazwy pliku PEM (Privacy Enhanced Mail).

### <a name="unix-based-os"></a>System operacyjny oparty na systemie UNIX

1. Jeśli w systemie nie zainstalowano narzędzi do prezentacji, zrób to teraz, na przykład:

   `sudo apt-get install -y putty`

1. Uruchom to polecenie, które tworzy plik, którego można użyć z łącznikiem SFTP-SSH:

   `puttygen <path-to-private-key-file-in-PuTTY-format> -O private-openssh -o <path-to-private-key-file-in-OpenSSH-format>`

   Na przykład:

   `puttygen /tmp/sftp/my-private-key-putty.ppk -O private-openssh -o /tmp/sftp/my-private-key-openssh.pem`

### <a name="windows-os"></a>System operacyjny Windows

1. Jeśli jeszcze tego nie zrobiono, [Pobierz najnowsze narzędzie do generowania generatora (PuTTYgen. exe)](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html), a następnie uruchom narzędzie.

1. Na tym ekranie wybierz pozycję **Załaduj**.

   ![Wybierz pozycję "Załaduj"](./media/connectors-sftp-ssh/puttygen-load.png)

1. Przejdź do pliku klucza prywatnego w formacie podano i wybierz pozycję **Otwórz**.

1. Z menu **konwersje** wybierz pozycję **Eksportuj klucz OpenSSH**.

   ![Wybierz pozycję "Eksportuj klucz OpenSSH"](./media/connectors-sftp-ssh/export-openssh-key.png)

1. Zapisz plik klucza prywatnego z rozszerzeniem nazwy pliku `.pem`.

<a name="connect"></a>

## <a name="connect-to-sftp-with-ssh"></a>Nawiązywanie połączenia z protokołem SFTP przy użyciu protokołu SSH

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [Azure Portal](https://portal.azure.com)i Otwórz aplikację logiki w Projektancie aplikacji logiki, jeśli nie jest jeszcze otwarta.

1. W przypadku pustych aplikacji logiki w polu wyszukiwania wprowadź ciąg "SFTP SSH" jako filtr. Na liście Wyzwalacze wybierz wyzwalacz, który chcesz.

   — lub —

   W przypadku istniejących aplikacji logiki w ostatnim kroku, w którym chcesz dodać akcję, wybierz pozycję **nowy krok**. W polu wyszukiwania wprowadź ciąg "SFTP SSH" jako filtr. Na liście Akcje wybierz żądaną akcję.

   Aby dodać akcję między krokami, przesuń wskaźnik myszy nad strzałkę między krokami. Wybierz wyświetlony znak plus ( **+** ), a następnie wybierz pozycję **Dodaj akcję**.

1. Podaj niezbędne szczegóły dotyczące połączenia.

   > [!IMPORTANT]
   >
   > Po wprowadzeniu klucza prywatnego SSH we właściwości **prywatnego klucza SSH** wykonaj te dodatkowe kroki, aby upewnić się, że podajesz pełną i poprawną wartość dla tej właściwości. Nieprawidłowy klucz powoduje niepowodzenie połączenia.

   Mimo że można użyć dowolnego edytora tekstu, poniżej przedstawiono przykładowe kroki pokazujące sposób prawidłowego kopiowania i wklejania klucza przy użyciu programu Notepad. exe jako przykładu.

   1. Otwórz plik klucza prywatnego SSH w edytorze tekstu. W tych krokach użyto Notatnika jako przykładu.

   1. W menu **Edycja** Notatnik wybierz pozycję **Zaznacz wszystko**.

   1. Wybierz pozycję **edytuj** > **Kopiuj**.

   1. W wyzwalaczu SFTP-SSH lub akcji, który został dodany, wklej *pełny* klucz skopiowany do właściwości **prywatnego klucza SSH** , która obsługuje wiele wierszy.  ***Upewnij się, że wkleisz*** klucz. ***Nie wprowadzaj ręcznie ani nie edytuj klucza***.

1. Po zakończeniu wprowadzania szczegółów połączenia wybierz pozycję **Utwórz**.

1. Podaj teraz niezbędne szczegóły wybranego wyzwalacza lub akcji i Kontynuuj tworzenie przepływu pracy aplikacji logiki.

## <a name="examples"></a>Przykłady

<a name="file-added-modified"></a>

### <a name="sftp---ssh-trigger-when-a-file-is-added-or-modified"></a>Wyzwalacz SFTP-SSH: po dodaniu lub zmodyfikowaniu pliku

Ten wyzwalacz uruchamia przepływ pracy aplikacji logiki, gdy na serwerze SFTP zostanie dodany lub zmieniony plik. Na przykład można dodać warunek, który sprawdza zawartość pliku i pobiera zawartość w zależności od tego, czy zawartość spełnia określony warunek. Następnie można dodać akcję, która pobiera zawartość pliku i umieszcza tę zawartość w folderze na serwerze SFTP.

**Przykład przedsiębiorstwa**: ten wyzwalacz służy do monitorowania folderu SFTP dla nowych plików reprezentujących zamówienia klienta. Następnie można użyć akcji SFTP, takiej jak **pobieranie zawartości pliku** , aby uzyskać zawartość zamówienia do dalszej obróbki i przechowywać ją w bazie danych zamówień.

<a name="get-content"></a>

### <a name="sftp---ssh-action-get-content-using-path"></a>Działanie SFTP-SSH: pobieranie zawartości przy użyciu ścieżki

Ta akcja pobiera zawartość z pliku na serwerze SFTP. Na przykład można dodać wyzwalacz z poprzedniego przykładu i warunek, który musi spełniać zawartość pliku. Jeśli warunek ma wartość true, Akcja, która pobiera zawartość, może zostać uruchomiona.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne na temat wyzwalaczy, akcji i limitów, które są opisane w opisie OpenAPI łącznika (dawniej Swagger), przejrzyj [stronę odwołania](/connectors/sftpconnector/)łącznika.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md)
