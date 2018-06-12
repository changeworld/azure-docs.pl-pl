---
title: Połączenie z systemami SAP - Azure Logic Apps | Dokumentacja firmy Microsoft
description: Jak uzyskać dostępu do zasobów i zarządzanie nimi SAP automatyzując przepływy pracy z usługą Azure Logic Apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 05/31/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, divswa, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: a9346092e0a24709a9888937effdf802bf1b09fb
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35300219"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Połączenie z systemami SAP z usługi Azure Logic Apps

W tym artykule przedstawiono uzyskać dostęp z zasobów SAP wewnątrz aplikacji logiki za pomocą łączników SAP serwerze aplikacji i wiadomości SAP. W ten sposób można zautomatyzować zadania, procesów i przepływy pracy, które zarządzanie przez tworzenie aplikacji logiki danych SAP i zasobów.

W tym przykładzie użyto aplikacji logiki, który można wywoływać z żądania HTTP. Aplikację logiki wysyła pośredniego dokumentu (IDoc) do serwera SAP, a następnie zwraca odpowiedź do obiektu żądającego, który wywołuje aplikację logiki.
Bieżący łączniki SAP ma działania, ale nie wyzwalaczy, w tym przykładzie użyto [wyzwalacza żądania HTTP](../connectors/connectors-native-reqres.md) jako pierwszy krok w przepływie pracy aplikacji logiki. Aby uzyskać informacje techniczne dotyczące łącznika programu SAP zobacz artykuły te odwołania: 

* <a href="https://docs.microsoft.com/connectors/sapapplicationserver/" target="blank">Łącznik aplikacji serwera SAP</a>
* <a href="https://docs.microsoft.com/connectors/sapmessageserver/" target="blank">Łącznik serwera komunikat SAP</a>

Jeśli nie masz jeszcze subskrypcji platformy Azure <a href="https://azure.microsoft.com/free/" target="_blank">Załóż bezpłatne konto platformy Azure</a>.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skorzystać z tego artykułu, potrzebne są następujące elementy:

* Aplikację logiki, z którym chcesz uzyskać dostęp do systemu SAP i wyzwalacz, który uruchamia przepływ pracy aplikacji logiki. Łączniki programu SAP aktualnie zawierają tylko akcje. Jeśli jesteś nowym użytkownikiem aplikacji logiki, przejrzyj [co to jest Azure Logic Apps](../logic-apps/logic-apps-overview.md) i [Szybki Start: tworzenie pierwszej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Twoje <a href="https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server" target="_blank">serwera aplikacji SAP</a> lub <a href="https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm" target="_blank">SAP komunikatów serwera</a>

* Pobierz i zainstaluj najnowszą [bramy danych lokalnych](https://www.microsoft.com/download/details.aspx?id=53127) na dowolnym komputerze lokalnym. Upewnij się, że skonfigurowanie bramy w portalu Azure przed kontynuowaniem. Brama pomoże Ci bezpiecznego dostępu do danych i zasoby są lokalnie. Aby uzyskać więcej informacji, zobacz [instalacji lokalnej bramy danych do usługi Azure Logic Apps](../logic-apps/logic-apps-gateway-install.md).

* Pobierz i zainstaluj najnowsze biblioteki klienta SAP, który jest obecnie <a href="https://softwaredownloads.sap.com/file/0020000000086282018" target="_blank">SAP łącznika (NCo) 3.0.20.0 dla programu Microsoft .NET Framework 4.0 i systemu Windows w 64-bitowym (x64)</a>, w tym samym komputerze co brama danych lokalnych. Zainstaluj tę wersję lub nowszym z tego względu:

  * Można stają się zakleszczone wcześniejszych wersji SAP NCo, gdy więcej niż jeden IDoc są wysyłane w tym samym czasie. 
  Ten warunek blokuje wszystkie nowsze wiadomości, które są wysyłane do miejsca docelowego SAP, powoduje wiadomości limitu czasu.

  * Brama danych lokalna działa tylko na 64-bitowym. 
  W przeciwnym razie błąd "zły obraz", ponieważ usługa hosta bramy danych nie obsługuje zestawów 32-bitowych.

  * Zarówno usługa hosta bramy danych, jak i Microsoft SAP Adapter korzystają z programu .NET Framework 4.5. NCo SAP dla programu .NET Framework 4.0 współpracuje z procesami, które używają środowiska uruchomieniowego .NET 4.0 i 4.7.1. 
  NCo SAP programu .NET Framework 2.0 współpracuje z procesami, które używają środowiska uruchomieniowego .NET 2.0 i 3.5 i już nie działa z najnowszą bramę danych lokalnych.

* Zawartość wiadomości wysyłanych do serwera SAP, takie jak przykładowy plik IDoc. Ta zawartość musi być w formacie XML i obejmują przestrzeń nazw dla akcji SAP, którego chcesz użyć.

<a name="add-trigger"></a>

## <a name="add-http-request-trigger"></a>Dodaj wyzwalacza żądania HTTP

W aplikacjach logiki platformy Azure, każda aplikacja logiki musi rozpoczynać się od [wyzwalacza](../logic-apps/logic-apps-overview.md#logic-app-concepts), które odbywa się uruchamiany, gdy określonego zdarzenia lub gdy zostanie spełniony określony warunek. Zawsze uruchamiany wyzwalacza aparat Logic Apps tworzy wystąpienie aplikacji logiki i uruchamiania aplikacji przepływu pracy.

W tym przykładzie zostanie tworzenie aplikacji logiki z punktem końcowym na platformie Azure tak, aby można było wysyłać *żądania HTTP POST* do aplikacji logiki. Odebrania te żądania HTTP, aplikację logiki wyzwalacza generowane i uruchamia następnego kroku w przepływie pracy.

1. W portalu Azure tworzenie aplikacji logiki puste, który zostanie otwarty w Projektancie aplikacji logiki. 

2. W polu wyszukiwania wprowadź "żądania http" jako filtr. Wybierz z listy wyzwalaczy, wyzwalacz: **żądania - zostanie odebrane żądanie HTTP podczas**

   ![Dodaj wyzwalacza żądania HTTP](./media/logic-apps-using-sap-connector/add-trigger.png)

3. Teraz zapisać aplikację logiki, aby wygenerować adresu URL punktu końcowego dla aplikacji logiki.
Na pasku narzędzi projektanta wybierz pozycję **Zapisz**. 

   Punkt końcowy adres URL jest teraz wyświetlany w wyzwalacz, na przykład:

   ![Generowania adresu URL dla punktu końcowego](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

<a name="add-action"></a>

## <a name="add-sap-action"></a>Dodaj akcję SAP

W aplikacjach logiki platformy Azure [akcji](../logic-apps/logic-apps-overview.md#logic-app-concepts) jest krokiem w przepływie pracy następujący wyzwalacz inną akcję. Jeśli nie zostały jeszcze dodane wyzwalacz do aplikacji logiki i chcesz dokonać w tym przykładzie [dodać wyzwalacza opisane w tej sekcji](#add-trigger).

1. W Projektancie aplikacji logiki w ramach wyzwalacza, wybierz **nowy krok** > **Dodaj akcję**.

   ![Dodawanie akcji](./media/logic-apps-using-sap-connector/add-action.png) 

2. W polu wyszukiwania wprowadź "serwerem sap" jako filtr. Z listy akcji wybierz akcję serwera SAP: 

   * **Serwer aplikacji SAP — Wyślij do programu SAP**
   * **Serwer komunikatów SAP — Wyślij do programu SAP**

   W tym przykładzie użyto tej akcji: **serwera aplikacji SAP — Wyślij do programu SAP**

   ![Wybierz "Serwera SAP aplikacji" lub "Serwerem SAP komunikat"](media/logic-apps-using-sap-connector/select-sap-action.png)

3. Jeśli zostanie wyświetlony monit o informacje dotyczące połączenia, należy utworzyć połączenie SAP teraz. W przeciwnym razie połączenie już istnieje, kontynuować do następnego kroku, możesz skonfigurować akcję SAP. 

   **Utwórz lokalne połączenie SAP**

   1. Dla **bram**, wybierz pozycję **Połącz za pośrednictwem bramy danych lokalnych** będą wyświetlane właściwości połączenia lokalnego.

   2. Podaj informacje o połączeniu dla serwera SAP. 
   Aby uzyskać **bramy** właściwości, wybierz bramę danych utworzonego w portalu Azure instalacji bramy, na przykład:

      **Serwer aplikacji SAP**

      ![Utwórz połączenie z serwerem aplikacji SAP](./media/logic-apps-using-sap-connector/create-SAP-app-server-connection.png)  

      **Serwer komunikatów SAP**

      ![Utwórz połączenie z serwerem SAP wiadomości](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png) 

   2. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

      Logic Apps konfiguruje i testuje połączenie, upewniając się, że połączenie działa poprawnie.

4. Teraz Znajdź i wybierz akcję z serwera SAP. 

   1. W **akcji SAP** wybierz ikonę folderu. 
   Z listy folderów Znajdź i wybierz akcję, która ma być używany. 

      W tym przykładzie wybiera **IDOC** kategorii akcji IDoc. 

      ![Znajdź i wybierz akcję IDoc](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

      Jeśli nie można odnaleźć akcji, którą chcesz, możesz ręcznie wprowadzić ścieżkę, na przykład:

      ![Ręcznie Podaj ścieżkę do akcji IDoc](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

      Aby uzyskać więcej informacji na temat operacji IDoc, zobacz [komunikatu schematów dla operacji IDOC](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)

   2. Kliknij wewnątrz **komunikat wejściowy** , dzięki czemu pojawi się lista zawartości dynamicznej. 
   W tej listy, w obszarze **zostanie odebrane żądanie HTTP podczas**, wybierz pozycję **treści** pola. 

      Ten krok obejmuje zawartość treści z wyzwalacz żądania HTTP i wysyła, że dane wyjściowe do serwera SAP.

      ![Wybierz pole "Treść"](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      Gdy wszystko będzie gotowe, akcję SAP wygląda w tym przykładzie:

      ![Zakończenie akcji SAP](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

6. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

<a name="add-response"></a>

## <a name="add-http-response-action"></a>Dodaj akcję odpowiedzi HTTP

Teraz Dodaj akcji odpowiedzi do przepływu pracy aplikacji logiki i zawierają dane wyjściowe z akcji SAP. W ten sposób aplikację logiki zwraca wyniki z serwera SAP do oryginalnego obiektu żądającego. 

1. W Projektancie aplikacji logiki w ramach działania programu SAP wybierz **nowy krok** > **Dodaj akcję**.

2. W polu wyszukiwania wpisz "odpowiedź" jako filtr. Wybierz z listy akcji, ta akcja: **żądanie - odpowiedź**

3. Kliknij wewnątrz **treści** , dzięki czemu pojawi się lista zawartości dynamicznej. Z tej listy w obszarze **Wyślij do programu SAP**, wybierz pozycję **treści** pola. 

   ![Zakończenie akcji SAP](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

4. Zapisz aplikację logiki. 

## <a name="test-your-logic-app"></a>Przetestuj aplikację logiki

1. Jeśli aplikację logiki nie jest jeszcze włączone, w menu aplikacji logiki, wybierz **omówienie**. Na pasku narzędzi wybierz **włączyć**. 

2. Na pasku narzędzi Projektanta aplikacji logiki, wybierz **Uruchom**. Ten krok zostanie uruchomiony ręcznie aplikacji logiki.

3. Wyzwalanie aplikację logiki, wysyłając żądanie HTTP POST na adres URL w wyzwalacz żądania HTTP, a obejmują zawartości w żądaniu wiadomości. Do wysłania żądania można użyć narzędzia takie jak [Postman](https://www.getpostman.com/apps). 

   W tym artykule żądania wysyła pliku IDoc, który musi być w formacie XML i obejmują przestrzeń nazw dla akcji SAP, którego używasz, na przykład: 

   ``` xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//620/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

4. Po wysłaniu żądania HTTP oczekiwania na odpowiedź z aplikacji logiki.

> [!NOTE]
> Aplikację logiki może upłynął limit czasu, jeśli wszystkie kroki wymagane do odpowiedzi nie zakończy się w [limit czasu żądania](./logic-apps-limits-and-config.md). Jeśli ten stan występuje, może pobrać zablokowane żądania. Aby ułatwić diagnozowanie problemów, Dowiedz się, jak [Sprawdź i Monitoruj aplikacje logiki](../logic-apps/logic-apps-monitor-your-logic-apps.md).

Gratulacje, został już utworzony aplikacji logiki, który może komunikować się z serwerem SAP. Teraz, kiedy zostały skonfigurowane połączenie programu SAP aplikacji logiki, można sprawdzić innych dostępnych akcji SAP, takie jak BAPI i RFC.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne dotyczące łącznika, zgodnie z opisem przez działania łączników programu Swagger plików zobacz artykuły te odwołania: 

* [Serwer aplikacji SAP](/connectors/sapapplicationserver/)
* [Serwer komunikatów SAP](/connectors/sapmessageserver/)

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

* [Połączenie z lokalnymi systemami](../logic-apps/logic-apps-gateway-connection.md) z aplikacji logiki
* Dowiedz się, jak sprawdzanie poprawności, przekształcania i inne operacje dotyczące komunikatów z [pakiet integracyjny dla przedsiębiorstw](../logic-apps/logic-apps-enterprise-integration-overview.md)
* Dowiedz się więcej o innych [łączniki Logic Apps](../connectors/apis-list.md)
