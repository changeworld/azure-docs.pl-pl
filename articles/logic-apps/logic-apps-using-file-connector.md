---
title: Łączenie się z systemami plików w środowisku lokalnym
description: Automatyzacja zadań i przepływów pracy łączących się z lokalnymi systemami plików za pomocą łącznika systemu plików za pośrednictwem lokalnej bramy danych w usłudze Azure Logic Apps
services: logic-apps
ms.suite: integration
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan, logicappspm
ms.topic: article
ms.date: 01/13/2019
ms.openlocfilehash: ab17137f162b893b54942d870b07a36f87d1b71d
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115069"
---
# <a name="connect-to-on-premises-file-systems-with-azure-logic-apps"></a>Łączenie z lokalnymi systemami plików przy użyciu usługi Azure Logic Apps

Za pomocą usługi Azure Logic Apps i łącznika systemu plików można tworzyć zautomatyzowane zadania i przepływy pracy, które tworzą pliki i zarządzają nimi w lokalnym udziale plików, na przykład:

- Tworzenie, pobierz, dołącz, aktualizuj i usuń pliki.
- Lista plików w folderach lub folderach głównych.
- Pobierz zawartość pliku i metadane.

W tym artykule pokazano, jak można połączyć się z lokalnym systemem plików, zgodnie z opisem w tym przykładowym scenariuszu: skopiuj plik, który został przekazany do Dropbox do udziału plików, a następnie wyślij wiadomość e-mail. Aby bezpiecznie łączyć się i uzyskiwać dostęp do systemów lokalnych, aplikacje logiki używają [lokalnej bramy danych.](../logic-apps/logic-apps-gateway-connection.md) Jeśli jesteś nowy w aplikacjach logiki, sprawdź [Co to jest usługa Azure Logic Apps?](../logic-apps/logic-apps-overview.md). Aby uzyskać informacje techniczne dotyczące łącznika, zobacz [odwołanie do złącza systemu plików](/connectors/filesystem/).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Przed połączeniem aplikacji logiki z systemami lokalnymi, takimi jak serwer systemu plików, należy [zainstalować i skonfigurować lokalną bramę danych](../logic-apps/logic-apps-gateway-install.md). W ten sposób można określić, aby używać instalacji bramy podczas tworzenia połączenia systemu plików z aplikacji logiki.

* [Konto Dropbox](https://www.dropbox.com/), które możesz zarejestrować za darmo. Poświadczenia konta są niezbędne do utworzenia połączenia między aplikacją logiki a kontem Dropbox.

* Dostęp do komputera z systemem plików, którego chcesz użyć. Na przykład w przypadku instalowania bramy danych na tym samym komputerze co system plików potrzebne są poświadczenia konta dla tego komputera.

* Konto e-mail od dostawcy obsługiwanego przez aplikacje logiki, takie jak Office 365 Outlook, Outlook.com lub Gmail. W przypadku innych dostawców [przejrzyj tę listę łączników](https://docs.microsoft.com/connectors/). Ta aplikacja logiki korzysta z konta Office 365 Outlook. Jeśli korzystasz z innego konta e-mail, ogólne kroki będą takie same, ale interfejs użytkownika może się trochę różnić.

* Podstawowa wiedza na temat [tworzenia aplikacji logicznych](../logic-apps/quickstart-create-first-logic-app-workflow.md). W tym przykładzie potrzebna jest pusta aplikacja logiki.

## <a name="add-trigger"></a>Dodawanie wyzwalacza

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i otwórz aplikację logiki w logice App Designer, jeśli nie jest już otwarty.

1. W polu wyszukiwania wpisz "dropbox" jako filtr. Z listy wyzwalaczy wybierz ten wyzwalacz: **Po utworzeniu pliku**

   ![Wybierz wyzwalacz Dropbox](media/logic-apps-using-file-connector/select-dropbox-trigger.png)

1. Zaloguj się przy użyciu poświadczeń konta Dropbox i autoryzuj dostęp do danych Dropbox dla usługi Azure Logic Apps.

1. Podaj wymagane informacje dla wyzwalacza.

   ![Wyzwalacz Dropbox](media/logic-apps-using-file-connector/dropbox-trigger.png)

## <a name="add-actions"></a>Dodawanie akcji

1. W obszarze wyzwalacza wybierz pozycję **Następny krok**. W polu wyszukiwania wpisz "system plików" jako filtr. Z listy akcji wybierz tę akcję: **Utwórz plik**

   ![Znajdź łącznik systemu plików](media/logic-apps-using-file-connector/find-file-system-action.png)

1. Jeśli nie masz jeszcze połączenia z systemem plików, zostanie wyświetlony monit o utworzenie połączenia.

   ![Tworzenie połączenia](media/logic-apps-using-file-connector/file-system-connection.png)

   | Właściwość | Wymagany | Wartość | Opis |
   | -------- | -------- | ----- | ----------- |
   | **Nazwa połączenia** | Tak | <*nazwa połączenia*> | Nazwa połączenia, którą chcesz uzyskać |
   | **Folder główny** | Tak | <*nazwa folderu głównego*> | Na przykład folder główny systemu plików, jeśli zainstalowano lokalną bramę danych, taką jak folder lokalny na komputerze, na którym zainstalowana jest lokalna brama danych, lub folder udziału sieciowego, do którego komputer może uzyskać dostęp. <p>Na przykład: `\\PublicShare\\DropboxFiles` <p>Folder główny jest głównym folderem nadrzędnym, który jest używany dla ścieżek względnych dla wszystkich akcji związanych z plikami. |
   | **Typ uwierzytelniania** | Nie | <*typ auth*> | Typ uwierzytelniania używany przez system plików: **Windows** |
   | **Nazwę użytkownika** | Tak | <*domain*>\\<*nazwa użytkownika* domeny> | Nazwa użytkownika komputera, na którym masz system plików |
   | **Hasło** | Tak | <*twoje hasło*> | Hasło do komputera, na którym masz system plików |
   | **Bramy** | Tak | <*zainstalowana nazwa bramy*> | Nazwa wcześniej zainstalowanej bramy |
   |||||

1. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

   Aplikacja logiki konfiguruje i testuje połączenie, upewniając się, że połączenie działa poprawnie. Jeśli połączenie jest poprawnie skonfigurowane, zostaną wyświetlone opcje dla wcześniej wybranej akcji.

1. W akcji **Utwórz plik** podaj szczegółowe informacje dotyczące kopiowania plików z Dropbox do folderu głównego w lokalnym udziale plików. Aby dodać dane wyjściowe z poprzednich kroków, kliknij wewnątrz pól i wybierz z dostępnych pól, gdy pojawi się dynamiczna lista zawartości.

   ![Utwórz akcję pliku](media/logic-apps-using-file-connector/create-file-filled.png)

1. Teraz dodaj akcję programu Outlook, która wysyła wiadomość e-mail, aby odpowiedni użytkownicy wiedzieli o nowym pliku. Wprowadź adresatów, tytuł i treść wiadomości e-mail. Do testowania można użyć własnego adresu e-mail.

   ![Akcja Wyślij wiadomość e-mail](media/logic-apps-using-file-connector/send-email.png)

1. Zapisz aplikację logiki. Przetestuj aplikację, przesyłając plik do Dropbox.

   Aplikacja logiki powinna skopiować plik do lokalnego udziału plików i wysłać adresaci e-mail o skopiowanym pliku.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać więcej informacji technicznych na temat tego łącznika, takich jak wyzwalacze, akcje i limity opisane w pliku Swagger łącznika, zobacz [stronę odwołania łącznika](https://docs.microsoft.com/connectors/fileconnector/).

> [!NOTE]
> W przypadku aplikacji logiki w [środowisku usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)wersja z etykietą ISE tego łącznika używa [limitów komunikatów ŚRODOWISKA ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [łączyć się z danymi lokalnymi](../logic-apps/logic-apps-gateway-connection.md) 
* Dowiedz się więcej o innych [łącznikach aplikacji logiki](../connectors/apis-list.md)
