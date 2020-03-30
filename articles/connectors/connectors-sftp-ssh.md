---
title: Łączenie się z serwerem SFTP za pomocą SSH
description: Automatyzuj zadania monitoruj, tworzą, zarządzają, wysyłają i odbierają pliki dla serwera SFTP przy użyciu aplikacji SSH i Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.reviewer: estfan, klam, logicappspm
ms.topic: article
ms.date: 03/7/2020
tags: connectors
ms.openlocfilehash: d4ab7425c967d3a176c0a576d0be38ece1701b8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128411"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-ssh-and-azure-logic-apps"></a>Monitorowanie, tworzenie i zarządzanie plikami SFTP przy użyciu aplikacji SSH i Azure Logic Apps

Aby zautomatyzować zadania monitorujące, tworzące, wysyłające i odbierające pliki na serwerze [protokołu SFTP (Secure File Transfer Protocol)](https://www.ssh.com/ssh/sftp/) przy użyciu protokołu [Secure Shell (SSH),](https://www.ssh.com/ssh/protocol/) można tworzyć i automatyzować przepływy pracy integracji przy użyciu usługi Azure Logic Apps i łącznika SFTP-SSH. SFTP to protokół sieciowy, który zapewnia dostęp do plików, transfer plików i zarządzanie plikami za pośrednictwem dowolnego niezawodnego strumienia danych. Oto kilka przykładowych zadań, które można zautomatyzować:

* Monitoruj, kiedy pliki są dodawane lub zmieniane.
* Pobierz, utwórz, skopiuj, zmień nazwę, aktualizuj, wystaw i usuń pliki.
* Tworzenie folderów.
* Pobierz zawartość pliku i metadane.
* Wyodrębnij archiwa do folderów.

Można użyć wyzwalaczy, które monitorują zdarzenia na serwerze SFTP i udostępniają dane wyjściowe innym działaniom. Można użyć akcji, które wykonują różne zadania na serwerze SFTP. Można również mieć inne akcje w aplikacji logiki używać danych wyjściowych z akcji SFTP. Na przykład jeśli regularnie pobierasz pliki z serwera SFTP, możesz wysyłać alerty e-mail dotyczące tych plików i ich zawartości za pomocą łącznika programu Office 365 Outlook lub łącznika Outlook.com. Jeśli jesteś nowy w aplikacjach logiki, sprawdź [Co to jest usługa Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

Aby uzyskać różnice między złączem SFTP-SSH a złączem SFTP, przejrzyj sekcję [Porównaj SFTP-SSH i SFTP](#comparison) w dalszej części tego tematu.

## <a name="limits"></a>Limity

* Akcje SFTP-SSH [obsługujące fragmentowanie](../logic-apps/logic-apps-handle-large-messages.md) mogą obsługiwać pliki o masie do 1 GB, podczas gdy akcje SFTP-SSH, które nie obsługują fragmentowania, mogą obsługiwać pliki o przesiąknieniu do 50 MB. Mimo że domyślny rozmiar fragmentu wynosi 15 MB, ten rozmiar może dynamicznie zmieniać się, zaczynając od 5 MB i stopniowo zwiększając się do maksymalnego 50 MB, na podstawie takich czynników, jak opóźnienie sieci, czas odpowiedzi serwera i tak dalej.

  > [!NOTE]
  > W przypadku aplikacji logiki w [środowisku usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)wersja z etykietą ISE tego łącznika używa [limitów komunikatów ŚRODOWISKA ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

  Można zastąpić to zachowanie adaptacyjne po [określeniu stałego rozmiaru fragmentu,](#change-chunk-size) którego chcesz użyć. Ten rozmiar może wynosić od 5 MB do 50 MB. Załóżmy na przykład, że masz plik o rozmiarze 45 MB i sieć, która może obsługiwać ten rozmiar pliku bez opóźnień. Adaptacyjne fragmentowanie powoduje kilka wywołań, a raczej jedno wywołanie. Aby zmniejszyć liczbę połączeń, możesz spróbować użyć fragmentu o rozmiarze 50 MB. W innym scenariuszu, jeśli aplikacja logiki jest limit czasu, na przykład podczas korzystania z 15 MB fragmentów, można spróbować zmniejszyć rozmiar do 5 MB.

  Rozmiar fragmentu jest skojarzony z połączeniem, co oznacza, że można użyć tego samego połączenia dla akcji, które obsługują fragmentowanie, a następnie dla akcji, które nie obsługują fragmentowania. W takim przypadku rozmiar fragmentu dla akcji, które nie obsługują fragmentowania waha się od 5 MB do 50 MB. W tej tabeli przedstawiono, które akcje SFTP-SSH obsługują fragmentowanie:

  | Akcja | Obsługa fragmentów | Zastąd wystają na obsługę rozmiaru fragmentu |
  |--------|------------------|-----------------------------|
  | **Kopiowanie pliku** | Nie | Nie dotyczy |
  | **Utwórz plik** | Tak | Tak |
  | **Tworzenie folderu** | Nie dotyczy | Nie dotyczy |
  | **Usuń plik** | Nie dotyczy | Nie dotyczy |
  | **Wyodrębnianie archiwum do folderu** | Nie dotyczy | Nie dotyczy |
  | **Pobierz zawartość pliku** | Tak | Tak |
  | **Pobierz zawartość pliku przy użyciu ścieżki** | Tak | Tak |
  | **Pobierz metadane pliku** | Nie dotyczy | Nie dotyczy |
  | **Pobierz metadane pliku przy użyciu ścieżki** | Nie dotyczy | Nie dotyczy |
  | **Lista plików w folderze** | Nie dotyczy | Nie dotyczy |
  | **Zmień nazwę pliku** | Nie dotyczy | Nie dotyczy |
  | **Aktualizuj plik** | Nie | Nie dotyczy |
  ||||

* Wyzwalacze SFTP-SSH nie obsługują fragmentowania wiadomości. Podczas żądania zawartości pliku wyzwalacze wybierają tylko pliki o rozmiarze 15 MB lub mniejszym. Aby uzyskać pliki większe niż 15 MB, wykonaj ten wzorzec:

  1. Użyj wyzwalacza SFTP-SSH, który zwraca tylko właściwości pliku, na przykład **Gdy plik jest dodawany lub modyfikowany (tylko właściwości).**

  1. Postępuj zgodnie z wyzwalaczem z SFTP-SSH **Pobierz akcję zawartości pliku,** który odczytuje kompletny plik i niejawnie używa fragmentowania wiadomości.

<a name="comparison"></a>

## <a name="compare-sftp-ssh-versus-sftp"></a>Porównanie SFTP-SSH z SFTP

Oto inne kluczowe różnice między złączem SFTP-SSH a złączem SFTP, gdzie złącze SFTP-SSH ma następujące możliwości:

* Używa [biblioteki SSH.NET](https://github.com/sshnet/SSH.NET), która jest biblioteką secure shell typu open source (SSH), która obsługuje .NET.

* Udostępnia akcję **Utwórz folder,** która tworzy folder przy określonej ścieżce na serwerze SFTP.

* Udostępnia akcję **Zmień nazwę pliku,** która zmienia nazwę pliku na serwerze SFTP.

* Buforuje połączenie z serwerem SFTP *przez maksymalnie 1 godzinę,* co zwiększa wydajność i zmniejsza liczbę prób nawiązania połączenia z serwerem. Aby ustawić czas trwania tego zachowania buforowania, edytuj [**ClientAliveInterval**](https://man.openbsd.org/sshd_config#ClientAliveInterval) właściwości w konfiguracji SSH na serwerze SFTP.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Adres serwera SFTP i poświadczenia konta, które umożliwiają aplikacji logiki dostęp do konta SFTP. Potrzebny jest również dostęp do klucza prywatnego SSH i hasła klucza prywatnego SSH. Aby używać fragmentowania podczas przekazywania dużych plików, potrzebujesz uprawnień do odczytu i zapisu dla folderu głównego na serwerze SFTP. W przeciwnym razie pojawia się błąd "401 Unauthorized".

  > [!IMPORTANT]
  >
  > Złącze SFTP-SSH obsługuje *tylko* te formaty kluczy prywatnych, algorytmy i odciski palców:
  >
  > * **Klucze prywatne: klucze**RSA (Rivest Shamir Adleman) i DSA (Digital Signature Algorithm) zarówno w formatach OpenSSH, jak i ssh.com. Jeśli twój klucz prywatny jest w formacie pliku PuTTY (.ppk), najpierw [przekonwertuj klucz na format pliku OpenSSH (pem).](#convert-to-openssh)
  >
  > * **Algorytmy szyfrowania:** DES-EDE3-CBC, DES-EDE3-CFB, DES-CBC, AES-128-CBC, AES-192-CBC i AES-256-CBC
  >
  > * **Odcisk palca**: MD5
  >
  > Po dodaniu wyzwalacza SFTP-SSH lub akcji, które chcesz do aplikacji logiki, należy podać informacje o połączeniu dla serwera SFTP. Po podaniu klucza prywatnego SSH dla tego połączenia ***nie należy ręcznie wprowadzać ani edytować klucza,*** co może spowodować niepowodzenie połączenia. Zamiast tego upewnij się, że ***klucz został skopiowany*** z pliku klucza prywatnego SSH i ***wklej*** ten klucz do szczegółów połączenia. 
  > Aby uzyskać więcej informacji, zobacz [Połącz z SFTP z SSH](#connect) sekcji w dalszej części tego artykułu.

* Podstawowa wiedza na temat [tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp do konta SFTP. Aby rozpocząć od wyzwalacza SFTP-SSH, [utwórz pustą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby użyć akcji SFTP-SSH, uruchom aplikację logiki z innym wyzwalaczem, na przykład **wyzwalaczem cyklu.**

## <a name="how-sftp-ssh-triggers-work"></a>Jak działają wyzwalacze SFTP-SSH

Wyzwalacze SFTP-SSH działają, sondując system plików SFTP i szukając dowolnego pliku, który został zmieniony od czasu ostatniej ankiety. Niektóre narzędzia umożliwiają zachowanie sygnatury czasowej po zmianie plików. W takich przypadkach należy wyłączyć tę funkcję, aby wyzwalacz mógł działać. Oto kilka typowych ustawień:

| Klient SFTP | Akcja |
|-------------|--------|
| Winscp | Przejdź do **opcji** > Preferencje**Transfer** > **Edytuj Zachowaj** > **sygnaturę** > **Preferences** > **czasą Wyłącz** |
| Filezilla | Przejdź do **funkcji Transfer** > **Zachowaj sygnatury czasowe przesłanych plików** > **Wyłącz** |
|||

Gdy wyzwalacz znajdzie nowy plik, wyzwalacz sprawdza, czy nowy plik jest ukończony, a nie częściowo zapisany. Na przykład plik może mieć zmiany w toku, gdy wyzwalacz sprawdza serwer plików. Aby uniknąć zwracania częściowo napisanego pliku, wyzwalacz odnotowuje sygnaturę czasową dla pliku, który ma ostatnie zmiany, ale nie zwraca natychmiast tego pliku. Wyzwalacz zwraca plik tylko podczas ponownego sondowania serwera. Czasami to zachowanie może spowodować opóźnienie, które jest maksymalnie dwa razy interwał sondowania wyzwalacza.

<a name="convert-to-openssh"></a>

## <a name="convert-putty-based-key-to-openssh"></a>Konwertuj klucz oparty na putty na OpenSSH

Jeśli twój klucz prywatny jest w formacie PuTTY, który używa rozszerzenia nazwy pliku .ppk (PuTTY Private Key), najpierw przekonwertuj klucz do formatu OpenSSH, który używa rozszerzenia nazwy pliku .pem (Privacy Enhanced Mail).

### <a name="unix-based-os"></a>System operacyjny oparty na uniksach

1. Jeśli narzędzia PuTTY nie są jeszcze zainstalowane w systemie, zrób to teraz, na przykład:

   `sudo apt-get install -y putty`

1. Uruchom to polecenie, które tworzy plik, którego można użyć ze łącznikiem SFTP-SSH:

   `puttygen <path-to-private-key-file-in-PuTTY-format> -O private-openssh -o <path-to-private-key-file-in-OpenSSH-format>`

   Przykład:

   `puttygen /tmp/sftp/my-private-key-putty.ppk -O private-openssh -o /tmp/sftp/my-private-key-openssh.pem`

### <a name="windows-os"></a>System operacyjny Windows

1. Jeśli jeszcze tego nie zrobiłeś, [pobierz najnowsze narzędzie PuTTY Generator (puttygen.exe),](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)a następnie uruchom narzędzie.

1. Na tym ekranie wybierz pozycję **Załaduj**.

   ![Wybierz "Załaduj"](./media/connectors-sftp-ssh/puttygen-load.png)

1. Przejdź do pliku klucza prywatnego w formacie PuTTY i wybierz pozycję **Otwórz**.

1. Z menu **Konwersje** wybierz **polecenie Eksportuj klucz OpenSSH**.

   ![Wybierz "Eksportuj klucz OpenSSH"](./media/connectors-sftp-ssh/export-openssh-key.png)

1. Zapisz plik klucza `.pem` prywatnego z rozszerzeniem nazwy pliku.

<a name="connect"></a>

## <a name="connect-to-sftp-with-ssh"></a>Łączenie się z SFTP za pomocą SSH

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i otwórz aplikację logiki w logice App Designer, jeśli nie jest już otwarty.

1. W przypadku pustych aplikacji logiki `sftp ssh` w polu wyszukiwania wprowadź jako filtr. W obszarze listy wyzwalaczy wybierz odpowiedni wyzwalacz.

   — lub —

   W przypadku istniejących aplikacji logiki w ostatnim kroku, w którym chcesz dodać akcję, wybierz pozycję **Nowy krok**. W polu wyszukiwania `sftp ssh` wprowadź jako filtr. W obszarze listy akcje wybierz odpowiednią akcję.

   Aby dodać akcję między krokami, przesuń wskaźnik myszy na strzałkę między krokami. Wybierz wyświetlony znak**+** plus ( ), a następnie wybierz pozycję **Dodaj akcję**.

1. Podaj szczegóły niezbędne do połączenia.

   > [!IMPORTANT]
   >
   > Po wprowadzeniu klucza prywatnego SSH we właściwości **klucza prywatnego SSH** wykonaj następujące dodatkowe kroki, które pomagają upewnić się, że podasz pełną i prawidłową wartość dla tej właściwości. Nieprawidłowy klucz powoduje, że połączenie nie powiedzie się.

   Chociaż można użyć dowolnego edytora tekstu, oto przykładowe kroki, które pokazują, jak poprawnie skopiować i wkleić klucz przy użyciu Notepad.exe jako przykład.

   1. Otwórz plik klucza prywatnego SSH w edytorze tekstu. W tych krokach użyto Notatnika jako przykładu.

   1. W menu **Edycja** notatnika wybierz polecenie **Zaznacz wszystko**.

   1. Wybierz **pozycję Edytuj** > **kopię**.

   1. W dodanym wyzwalaczu SFTP-SSH wklej *klucz kompletny* skopiowany do właściwości **klucza prywatnego SSH,** która obsługuje wiele wierszy.  ***Upewnij się, że wklejasz*** klucz. ***Nie wprowadzaj ręcznie ani nie edytuj klawisza***.

1. Po zakończeniu wprowadzania szczegółów połączenia wybierz pozycję **Utwórz**.

1. Teraz podaj szczegóły niezbędne dla wybranego wyzwalacza lub akcji i kontynuuj tworzenie przepływu pracy aplikacji logiki.

<a name="change-chunk-size"></a>

## <a name="override-chunk-size"></a>Zastąd wysyłkaj rozmiar fragmentu

Aby zastąpić domyślne zachowanie adaptacyjne, którego używa fragmentowanie, można określić stały rozmiar fragmentu od 5 MB do 50 MB.

1. W prawym górnym rogu akcji wybierz przycisk elipsy (**...**), a następnie wybierz pozycję **Ustawienia**.

   ![Otwórz ustawienia SFTP-SSH](./media/connectors-sftp-ssh/sftp-ssh-connector-setttings.png)

1. W obszarze **Transfer zawartości**we właściwości **Rozmiar fragmentu** `5` wprowadź `50`wartość całkowitą od do , na przykład: 

   ![Określ rozmiar fragmentu, który ma być używany](./media/connectors-sftp-ssh/specify-chunk-size-override-default.png)

1. Po zakończeniu wybierz pozycję **Gotowe**.

## <a name="examples"></a>Przykłady

<a name="file-added-modified"></a>

### <a name="sftp---ssh-trigger-when-a-file-is-added-or-modified"></a>SFTP - Wyzwalacz SSH: Po dodaniu lub zmodyfikowaniu pliku

Ten wyzwalacz uruchamia przepływ pracy aplikacji logiki po dodaniu lub zmianie pliku na serwerze SFTP. Na przykład można dodać warunek, który sprawdza zawartość pliku i pobiera zawartość na podstawie tego, czy zawartość spełnia określony warunek. Następnie można dodać akcję pobiera zawartość pliku i umieszcza ją w folderze na serwerze SFTP.

**Przykład przedsiębiorstwa:** Ten wyzwalacz służy do monitorowania folderu SFTP pod kątem nowych plików, które reprezentują zamówienia klientów. Następnie można użyć akcji SFTP, takich jak **Pobierz zawartość pliku,** aby uzyskać zawartość zamówienia do dalszego przetwarzania i przechowywać to zamówienie w bazie danych zamówień.

<a name="get-content"></a>

### <a name="sftp---ssh-action-get-content-using-path"></a>SFTP - Akcja SSH: Pobierz zawartość przy użyciu ścieżki

Ta akcja pobiera zawartość z pliku na serwerze SFTP. Na przykład można dodać wyzwalacz z poprzedniego przykładu i warunek, który musi spełniać zawartość pliku. Jeśli warunek jest spełniony, można uruchomić akcję, która pobiera zawartość.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać więcej informacji technicznych na temat tego łącznika, takich jak wyzwalacze, akcje i limity opisane w pliku Swagger łącznika, zobacz [stronę odwołania łącznika](https://docs.microsoft.com/connectors/sftpwithssh/).

> [!NOTE]
> W przypadku aplikacji logiki w [środowisku usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)wersja z etykietą ISE tego łącznika używa [limitów komunikatów ŚRODOWISKA ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o innych [łącznikach aplikacji logiki](../connectors/apis-list.md)
