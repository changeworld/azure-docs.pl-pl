---
title: Łączenie z systemami plików w środowisku lokalnym — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Automatyzowanie zadań i przepływów pracy, które łączą się z systemami plików w środowisku lokalnym za pomocą łącznika systemu plików za pośrednictwem lokalnej bramy danych w usłudze Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan, LADocs
ms.topic: article
ms.date: 08/25/2018
ms.openlocfilehash: 0c30ffec58b1542fa80cf0c9873a0e6df8641104
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2018
ms.locfileid: "50232549"
---
# <a name="connect-to-on-premises-file-systems-with-azure-logic-apps"></a>Nawiązać połączenie z lokalnymi systemami plików za pomocą usługi Azure Logic Apps

Za pomocą łącznika systemu plików i usługi Azure Logic Apps, można tworzyć zautomatyzowane zadania i udostępnić przepływów pracy, które umożliwiają tworzenie i zarządzanie plikami w pliku w środowisku lokalnym, na przykład:  

- Tworzenie, pobieranie, Dołącz, aktualizacji i usuwania plików
- Wyświetl listę plików w folderach lub folderów głównych.
- Pobierz zawartość pliku i metadanych.

W tym artykule pokazano, jak połączyć do lokalnego systemu plików zgodnie z opisem w tym przykładowym scenariuszu: Skopiuj plik, który zostanie przekazany do usługi Dropbox do udziału plików, a następnie wyślij wiadomość e-mail. Do nawiązania bezpiecznego połączenia i dostęp do systemów lokalnych, użyj aplikacji logiki [lokalnej bramy danych](../logic-apps/logic-apps-gateway-connection.md). Jeśli dopiero zaczynasz pracę z usługi logic apps, zapoznaj się z [co to jest Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>. 

* Zanim będzie można połączyć aplikacji logiki do systemów lokalnych, takim jak serwer systemu plików, należy [zainstalować i skonfigurować lokalną bramę danych](../logic-apps/logic-apps-gateway-install.md). W ten sposób można określić, aby użyć instalacji bramy, podczas tworzenia połączenia systemu plików z aplikacji logiki.

* A [konta Drobox](https://www.dropbox.com/) i poświadczenia użytkownika

  Poświadczenia Autoryzuj aplikację logiki, aby utworzyć połączenie i dostęp do tego konta Drobox. 

* Podstawową wiedzę na temat o [jak tworzyć aplikacje logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). W tym przykładzie należy pustej aplikacji logiki.

## <a name="add-trigger"></a>Dodawanie wyzwalacza

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i Otwórz swoją aplikację logiki w Projektancie aplikacji logiki, jeśli nie otwarto już.

1. W polu wyszukiwania wprowadź "dropbox" jako filtr. Z listy wyzwalaczy wybierz następujący wyzwalacz: **po utworzeniu pliku** 

   ![Wybieranie wyzwalacza usługi Dropbox](media/logic-apps-using-file-connector/select-dropbox-trigger.png)

1. Zaloguj się przy użyciu poświadczeń konta usługi Dropbox i autoryzować dostęp do danych usługi Dropbox do usługi Azure Logic Apps. 

1. Podaj wymagane informacje dotyczące wyzwalacza.

   ![Wyzwalacz usługi Dropbox](media/logic-apps-using-file-connector/dropbox-trigger.png)

## <a name="add-actions"></a>Dodawanie akcji

1. W obszarze wyzwalacza wybierz **następny krok**. W polu wyszukiwania wprowadź "file system" jako filtr. Z listy akcji wybierz następującą akcję: **Tworzenie pliku — System plików**

   ![Znajdź łącznika systemu plików](media/logic-apps-using-file-connector/find-file-system-action.png)

1. Jeśli nie masz jeszcze połączenia w systemie plików, zostanie wyświetlony monit utworzyć połączenie.

   ![Tworzenie połączenia](media/logic-apps-using-file-connector/file-system-connection.png)

   | Właściwość | Wymagane | Wartość | Opis | 
   | -------- | -------- | ----- | ----------- | 
   | **Nazwa połączenia** | Yes | <*Nazwa połączenia*> | Nazwę, która ma połączenia | 
   | **Folder główny** | Yes | <*Nazwa folderu głównego*> | Folder główny systemu plików, takich jak lokalnego folderu na komputerze z zainstalowanym lokalnej bramy danych lub folderu do udziału sieciowego, w których komputer może uzyskać dostęp. <p>Na przykład: `\\PublicShare\\DropboxFiles` <p>Folder główny jest folder nadrzędny głównego, który jest używany dla ścieżek względnych dla wszystkich działań związanych z plikami. | 
   | **Typ uwierzytelniania** | Nie | <*Typ uwierzytelnienia*> | Typ uwierzytelniania, który używa systemu plików, na przykład **Windows** | 
   | **Nazwa użytkownika** | Yes | <*domeny*>\\<*nazwy użytkownika*> | Nazwa użytkownika dla zainstalowanych wcześniej data gateway | 
   | **Hasło** | Yes | <*— hasło*> | Hasło dla zainstalowanych wcześniej data gateway | 
   | **Bramy** | Yes | <*zainstalowane gateway-name*> | Nazwa dla uprzednio zainstalowanej bramy | 
   ||| 

1. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**. 

   Usługa Logic Apps, konfiguruje i testuje połączenie, upewniając się, że połączenie działa poprawnie. 
   Jeśli połączenie jest skonfigurowane prawidłowo, opcje są wyświetlane dla akcji, która została wybrana wcześniej. 

1. W **Utwórz plik** akcji, podaj szczegóły kopiowania plików z usługi Dropbox do folderu głównego w udziale plików lokalnych. Aby dodać dane wyjściowe z poprzednich kroków, kliknij wewnątrz pola wyboru, a następnie wybierz z dostępnych pól, gdy pojawi się lista zawartości dynamicznej.

   ![Utwórz akcję pliku](media/logic-apps-using-file-connector/create-file-filled.png)

1. Teraz Dodaj akcję programu Outlook, która wysyła wiadomość e-mail, aby odpowiednie użytkownicy wiedzieli o nowy plik. Wprowadź odbiorców, tytuł i treść wiadomości e-mail. W przypadku testowania można użyć własnego adresu e-mail.

   ![Akcję wysyłania wiadomości e-mail](media/logic-apps-using-file-connector/send-email.png)

1. Zapisz aplikację logiki. Przetestuj swoją aplikację, przekazując plik do usługi Dropbox. 

   Twoja aplikacja logiki należy skopiować plik do udziału plików w środowisku lokalnym i Wyślij wiadomość e-mail adresatów o skopiowanego pliku.

## <a name="connector-reference"></a>Dokumentacja łączników

Szczegółowe informacje techniczne dotyczące wyzwalaczy, akcje i ograniczeń, które opisano przez standard OpenAPI łącznika (dawniej Swagger) opis, przejrzyj łącznika [strona referencyjna](/connectors/fileconnector/).

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Aby pomóc w ulepszaniu usługi Azure Logic Apps i łączniki, przesyłaj pomysły lub Głosuj na [witryny usługi Azure Logic Apps User Voice](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak [nawiązać połączenie z danymi lokalnymi](../logic-apps/logic-apps-gateway-connection.md) 
* Dowiedz się więcej o innych [łączników Logic Apps](../connectors/apis-list.md)
