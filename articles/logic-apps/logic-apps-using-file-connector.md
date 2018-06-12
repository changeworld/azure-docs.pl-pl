---
title: Połączenie z systemami plików lokalnie - Azure Logic Apps | Dokumentacja firmy Microsoft
description: Nawiązywanie połączenia systemów plików lokalnych z przepływów pracy aplikacji logiki za pomocą bramy danych lokalnych i łącznika systemu plików
keywords: systemy plików lokalnie
services: logic-apps
author: derek1ee
manager: jeconnoc
documentationcenter: ''
ms.assetid: ''
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2017
ms.author: LADocs; deli
ms.openlocfilehash: 019b5fcd218ddd471c5f02d0332b8f5b5bf0edb3
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35300824"
---
# <a name="connect-to-on-premises-file-systems-from-logic-apps-with-the-file-system-connector"></a>Nawiązywanie połączenia systemów lokalnych plików z aplikacji logiki w usłudze łącznika systemu plików

Zarządzanie danymi i bezpieczny dostęp do zasobów lokalnych, aplikacje logiki można użyć bramy danych lokalnych. W tym artykule przedstawiono sposób podłączenia do systemu plików lokalnie przez ten scenariusz podstawowy przykład: skopiować plik, który jest przekazywany do usługi Dropbox do udziału plików, a następnie wyślij wiadomość e-mail.

## <a name="prerequisites"></a>Wymagania wstępne

* Pobierz najnowszą [bramy danych lokalnych](https://www.microsoft.com/download/details.aspx?id=53127).

* Zainstaluj i definiowania najnowszą bramę danych lokalnych, wersja 1.15.6150.1 lub nowszym. Aby uzyskać instrukcje, zobacz [połączenia ze źródłami danych lokalnie](http://aka.ms/logicapps-gateway). Przed kontynuowaniem tych kroków, należy zainstalować bramy na maszynie lokalnej.

* Podstawową wiedzę na temat o [sposób tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="add-trigger-and-actions-for-connecting-to-your-file-system"></a>Dodaj wyzwalacza oraz do nawiązywania połączenia z systemu plików

1. Tworzenia pustej aplikacji logiki. Dodawanie tego wyzwalacza jako pierwszy krok: **Dropbox — po utworzeniu pliku** 

2. W obszarze wyzwalacza, wybierz **+ następnego kroku** > **Dodaj akcję**. 

3. W polu wyszukiwania wprowadź "file system" jako filtr. Gdy pojawi się wszystkie akcje dla łącznika systemu plików, wybierz **System plików - Utwórz plik** akcji. 

   ![Wyszukiwanie plików łącznika](media/logic-apps-using-file-connector/search-file-connector.png)

4. Jeśli masz już połączenie do systemu plików, zostanie wyświetlony monit o utworzyć połączenie. 

5. Wybierz **Połącz za pośrednictwem bramy danych lokalnych**. Gdy pojawią się właściwości połączenia, należy skonfigurować połączenie, określona w tabeli.

   ![Skonfiguruj połączenie](media/logic-apps-using-file-connector/create-file.png)

   | Ustawienie | Opis |
   | ------- | ----------- |
   | **Folder główny** | Określ folder główny do systemu plików. Można określić lokalny folder na komputerze, na którym zainstalowano bramę danych lokalnych lub może to być udział sieciowy, który ma dostęp maszyna. <p>**Porada:** folder główny jest folderu nadrzędnego głównego, który jest używany dla ścieżek względnych dla wszystkich akcji związanych z pliku. | 
   | **Typ uwierzytelniania** | Typ uwierzytelniania, który jest używany przez system plików | 
   | **Nazwa użytkownika** | Podaj nazwy użytkownika {*domeny*\\*username*} zainstalowane wcześniej bramy. | 
   | **Hasło** | Podaj hasło dla zainstalowanych wcześniej bramy. | 
   | **Brama** | Wybierz wcześniej zainstalowane bramy. | 
   ||| 

6. Po podaniu szczegóły połączenia wybierz **Utwórz**. 

   Logic Apps konfiguruje i testuje połączenie, upewniając się, że połączenie działa poprawnie. 
   Jeśli połączenie jest skonfigurowane poprawnie, opcje są wyświetlane dla akcji, które wcześniej wybrano. 
   Łącznik systemu plików jest teraz gotowy do użycia.

7. Konfigurowanie **Utwórz plik** działania kopiowania plików z Dropbox do folderu głównego dla udziału plików w katalogu lokalnym.

   ![Utworzenie pliku](media/logic-apps-using-file-connector/create-file-filled.png)

8. Po wykonaniu tej akcji kopiowania pliku Dodaj akcję programu Outlook, która wysyła wiadomość e-mail, aby poinformować odpowiednich użytkowników o nowy plik. Wprowadź adresatów, tytuł i treść wiadomości e-mail. 

   W **zawartości dynamicznej** listy, można wybrać danych wyjściowych z łącznika plików, dodając więcej szczegółowych informacji do wiadomości e-mail.

   ![Wysłanie wiadomości e-mail](media/logic-apps-using-file-connector/send-email.png)

9. Zapisz aplikację logiki. Testowanie aplikacji, przekazując plik do skrzynki. Plik powinien pobrać skopiowane do udziału pliku lokalnego, a użytkownik powinien otrzymywać wiadomości e-mail dotyczące działania.

Gratulacje, masz teraz pracy aplikacji logiki, który może łączyć się z lokalnego systemu plików. 

Spróbuj eksploracji inne funkcje, które oferuje łącznika, na przykład:

- Utwórz plik
- Wyświetl listę plików w folderze
- Dołącz plik
- Usuń plik
- Pobierz zawartość pliku
- Pobierz zawartość pliku przy użyciu ścieżki
- Pobierz metadane pliku
- Pobierz metadane pliku przy użyciu ścieżki
- Wyświetl listę plików w folderze głównym
- Aktualizuj plik

## <a name="view-the-swagger"></a>Wyświetlanie struktury swagger

Zobacz [swagger szczegóły](/connectors/fileconnector/). 

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Aby pomóc w udoskonalaniu Azure Logic Apps i łącznikami, Zagłosuj lub Prześlij pomysłów na [witryny Azure Logic Apps User Voice](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

* [Nawiązywanie połączenia z danymi lokalnymi](../logic-apps/logic-apps-gateway-connection.md) 
* [Monitorowanie aplikacji logiki](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Integracji przedsiębiorstwa dla scenariusze B2B](../logic-apps/logic-apps-enterprise-integration-overview.md)
