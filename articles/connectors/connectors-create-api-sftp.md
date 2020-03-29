---
title: Łączenie się z kontem SFTP
description: Automatyzuj zadania i procesy monitoruj, tworzą, zarządzają, wysyłają i odbierają pliki dla serwera SFTP za pomocą usługi SSH przy użyciu usługi Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.reviewer: divswa, klam, logicappspm
ms.topic: article
ms.date: 11/01/2019
tags: connectors
ms.openlocfilehash: d0da98070fa8da5403677e1a67bda75456c74d80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789277"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-azure-logic-apps"></a>Monitorowanie i tworzenie plików SFTP oraz zarządzanie nimi za pomocą usługi Azure Logic Apps

> [!IMPORTANT]
> Użyj [złącza SFTP-SSH,](../connectors/connectors-sftp-ssh.md) ponieważ złącze SFTP jest przestarzałe. Nie można już wybierać wyzwalaczy i akcji SFTP w projektancie aplikacji logiki.

Aby zautomatyzować zadania monitorujące, tworzące, wysyłające i odbierające pliki na serwerze [protokołu SFTP (Secure File Transfer Protocol),](https://www.ssh.com/ssh/sftp/) można tworzyć i automatyzować przepływy pracy integracji przy użyciu aplikacji Azure Logic Apps i łącznika SFTP. SFTP to protokół sieciowy, który zapewnia dostęp do plików, transfer plików i zarządzanie plikami za pośrednictwem dowolnego niezawodnego strumienia danych. Oto kilka przykładowych zadań, które można zautomatyzować:

* Monitoruj, kiedy pliki są dodawane lub zmieniane.
* Pobierz, utwórz, skopiuj, aktualizuj, wystaw i usuń pliki.
* Pobierz zawartość pliku i metadane.
* Wyodrębnij archiwa do folderów.

Można użyć wyzwalaczy, które monitorują zdarzenia na serwerze SFTP i udostępniają dane wyjściowe innym działaniom. Można użyć akcji, które wykonują różne zadania na serwerze SFTP. Można również mieć inne akcje w aplikacji logiki używać danych wyjściowych z akcji SFTP. Na przykład jeśli regularnie pobierasz pliki z serwera SFTP, możesz wysyłać alerty e-mail dotyczące tych plików i ich zawartości za pomocą łącznika programu Office 365 Outlook lub łącznika Outlook.com. Jeśli jesteś nowy w aplikacjach logiki, sprawdź [Co to jest usługa Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="limits"></a>Limity

Łącznik SFTP obsługuje tylko pliki, które mają *rozmiar 50 MB lub mniej* i nie obsługują [fragmentowania wiadomości.](../logic-apps/logic-apps-handle-large-messages.md) W przypadku większych plików należy użyć [złącza SFTP-SSH](../connectors/connectors-sftp-ssh.md). Różnice między złączem SFTP a złączem SFTP-SSH można znaleźć w artykule [Porównaj SFTP-SSH i SFTP](../connectors/connectors-sftp-ssh.md#comparison) w artykule SFTP-SSH.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Adres serwera SFTP i poświadczenia konta, które umożliwiają aplikacji logiki dostęp do konta SFTP. Aby korzystać z protokołu [Secure Shell (SSH),](https://www.ssh.com/ssh/protocol/) potrzebny jest również dostęp do klucza prywatnego SSH i hasła klucza prywatnego SSH.

  > [!NOTE]
  >
  > Złącze SFTP obsługuje te formaty kluczy prywatnych: OpenSSH, ssh.com i PuTTY
  >
  > Podczas tworzenia aplikacji logiki po dodaniu wyzwalacza sftp lub akcji, które chcesz, musisz podać informacje o połączeniu dla serwera SFTP. 
  > Jeśli używasz klucza prywatnego SSH, upewnij się, że ***skopiować*** klucz z pliku klucza prywatnego SSH i ***wkleić*** ten klucz do szczegółów połączenia, ***Nie należy ręcznie wprowadzać lub edytować klucz***, co może spowodować niepowodzenie połączenia. 
  > Aby uzyskać więcej informacji, zobacz dalsze kroki w tym artykule.

* Podstawowa wiedza na temat [tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp do konta SFTP. Aby rozpocząć od wyzwalacza SFTP, [utwórz pustą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby użyć akcji SFTP, uruchom aplikację logiki z innym wyzwalaczem, na przykład **wyzwalaczem cyklu.**

## <a name="how-sftp-triggers-work"></a>Jak działają wyzwalacze SFTP

Wyzwalacze SFTP działają, sondując system plików SFTP i szukając dowolnego pliku, który został zmieniony od czasu ostatniej ankiety. Niektóre narzędzia umożliwiają zachowanie sygnatury czasowej po zmianie plików. W takich przypadkach należy wyłączyć tę funkcję, aby wyzwalacz mógł działać. Oto kilka typowych ustawień:

| Klient SFTP | Akcja |
|-------------|--------|
| Winscp | Przejdź do **opcji** > Preferencje**Transfer** > **Edytuj Zachowaj** > **sygnaturę** > **Preferences** > **czasą Wyłącz** |
| Filezilla | Przejdź do **funkcji Transfer** > **Zachowaj sygnatury czasowe przesłanych plików** > **Wyłącz** |
|||

Gdy wyzwalacz znajdzie nowy plik, wyzwalacz sprawdza, czy nowy plik jest ukończony, a nie częściowo zapisany. Na przykład plik może mieć zmiany w toku, gdy wyzwalacz sprawdza serwer plików. Aby uniknąć zwracania częściowo napisanego pliku, wyzwalacz odnotowuje sygnaturę czasową dla pliku, który ma ostatnie zmiany, ale nie zwraca natychmiast tego pliku. Wyzwalacz zwraca plik tylko podczas ponownego sondowania serwera. Czasami to zachowanie może spowodować opóźnienie, które jest maksymalnie dwa razy interwał sondowania wyzwalacza.

## <a name="connect-to-sftp"></a>Łączenie się z SFTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i otwórz aplikację logiki w logice App Designer, jeśli nie jest już otwarty.

1. W przypadku pustych aplikacji logiki w polu wyszukiwania wprowadź "sftp" jako filtr. W obszarze listy wyzwalaczy wybierz odpowiedni wyzwalacz.

   — lub —

   W przypadku istniejących aplikacji logiki w ostatnim kroku, w którym chcesz dodać akcję, wybierz pozycję **Nowy krok**. W polu wyszukiwania wpisz "sftp" jako filtr. W obszarze listy akcje wybierz odpowiednią akcję.

   Aby dodać akcję między krokami, przesuń wskaźnik myszy na strzałkę między krokami. Wybierz wyświetlony znak**+** plus ( ), a następnie wybierz pozycję **Dodaj akcję**.

1. Podaj szczegóły niezbędne do połączenia.

   > [!IMPORTANT]
   >
   > Po wprowadzeniu klucza prywatnego SSH we właściwości **klucza prywatnego SSH** wykonaj następujące dodatkowe kroki, które pomagają upewnić się, że podasz pełną i prawidłową wartość dla tej właściwości. 
   > Nieprawidłowy klucz powoduje, że połączenie nie powiedzie się.

   Chociaż można użyć dowolnego edytora tekstu, oto przykładowe kroki, które pokazują, jak poprawnie skopiować i wkleić klucz przy użyciu Notepad.exe jako przykład.

   1. Otwórz plik klucza prywatnego SSH w edytorze tekstu. W tych krokach użyto Notatnika jako przykładu.

   1. W menu **Edycja** notatnika wybierz polecenie **Zaznacz wszystko**.

   1. Wybierz **pozycję Edytuj** > **kopię**.

   1. W dodanym wyzwalaczu SFTP wklej *cały* klucz skopiowany do właściwości **klucza prywatnego SSH,** która obsługuje wiele wierszy. ***Upewnij się, że wklejasz*** klucz. ***Nie wprowadzaj ręcznie ani nie edytuj klawisza***.

1. Po zakończeniu wprowadzania szczegółów połączenia wybierz pozycję **Utwórz**.

1. Podaj szczegóły niezbędne do wybranego wyzwalacza lub akcji i kontynuuj tworzenie przepływu pracy aplikacji logiki.

## <a name="examples"></a>Przykłady

<a name="file-add-modified"></a>

### <a name="sftp-trigger-when-a-file-is-added-or-modified"></a>Wyzwalacz SFTP: po dodaniu lub zmodyfikowaniu pliku

Ten wyzwalacz uruchamia przepływ pracy aplikacji logiki po dodaniu lub zmianie pliku na serwerze SFTP. Na przykład można dodać warunek, który sprawdza zawartość pliku i pobiera zawartość na podstawie tego, czy zawartość spełnia określony warunek. Następnie można dodać akcję pobiera zawartość pliku i umieszcza ją w folderze na serwerze SFTP.

**Przykład przedsiębiorstwa:** Ten wyzwalacz służy do monitorowania folderu SFTP pod kątem nowych plików, które reprezentują zamówienia klientów. Następnie można użyć akcji SFTP, takich jak **Pobierz zawartość pliku,** aby uzyskać zawartość zamówienia do dalszego przetwarzania i przechowywać to zamówienie w bazie danych zamówień.

<a name="get-content"></a>

### <a name="sftp-action-get-content"></a>Akcja SFTP: pobierz zawartość

Ta akcja pobiera zawartość z pliku na serwerze SFTP. Na przykład można dodać wyzwalacz z poprzedniego przykładu i warunek, który musi spełniać zawartość pliku. Jeśli warunek jest spełniony, można uruchomić akcję, która pobiera zawartość.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne na temat wyzwalaczy, akcji i limitów, które są opisane przez opis OpenAPI łącznika (dawniej Swagger), przejrzyj [stronę referencyjną](/connectors/sftpconnector/)łącznika .

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o innych [łącznikach aplikacji logiki](../connectors/apis-list.md)
