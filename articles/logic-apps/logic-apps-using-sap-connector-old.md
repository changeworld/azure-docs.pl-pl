---
title: Łączenie z systemami SAP — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Uzyskiwanie dostępu i zarządzania zasobami SAP dzięki automatyzacji przepływów pracy z usługą Azure Logic Apps
author: ecfan
ms.author: estfan
ms.date: 05/31/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, divswa, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: d677c0eae9c92f90783ed4ebd95a528b34c872ec
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60847484"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Łączenie z systemami SAP z usługi Azure Logic Apps

> [!NOTE]
> Ten łącznik SAP jest zaplanowane do wycofania z użycia. Użyj lub Migrowanie do [łącznika systemu SAP nowszych i bardziej zaawansowanych](./logic-apps-using-sap-connector.md). 

Ten artykuł pokazuje, jak można pobrać zasobów SAP z wewnątrz aplikacji logiki za pomocą łączników serwera aplikacji SAP i SAP Message Server. W ten sposób można zautomatyzować zadania, procesów i przepływów pracy, które Zarządzaj danymi SAP i zasoby, tworząc aplikacje logiki.

W tym przykładzie użyto aplikacji logiki, który można wywoływać za pomocą żądania HTTP. Aplikacja logiki wysyła pośrednie dokument (IDoc) do serwera SAP, a następnie zwraca odpowiedź do osoby zgłaszającej żądanie, która wywołuje aplikację logiki.
Bieżący łączniki SAP ma działania, ale nie wyzwalaczy, więc w tym przykładzie użyto [wyzwalacza żądania HTTP](../connectors/connectors-native-reqres.md) jako pierwszy krok w przepływie pracy aplikacji logiki. Aby uzyskać informacje techniczne dotyczące łącznika SAP zobacz następujące artykuły odwołania: 

* <a href="https://docs.microsoft.com/connectors/sapapplicationserver/" target="blank">Łącznik serwera aplikacji SAP</a>
* <a href="https://docs.microsoft.com/connectors/sapmessageserver/" target="blank">Serwer komunikatów łącznika systemu SAP</a>

Jeśli nie masz jeszcze subskrypcji platformy Azure <a href="https://azure.microsoft.com/free/" target="_blank">Załóż bezpłatne konto platformy Azure</a>.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skorzystać z tego artykułu, potrzebne są następujące elementy:

* Aplikacja logiki, z którym chcesz uzyskać dostęp do systemu SAP i wyzwalacza uruchamiającego przepływ pracy aplikacji logiki. Łączniki SAP zapewniają obecnie tylko akcje. Jeśli dopiero zaczynasz pracę z usługi logic apps, zapoznaj się z [co to jest Azure Logic Apps](../logic-apps/logic-apps-overview.md) i [Szybki Start: Utwórz swoją pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Twoje <a href="https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server" target="_blank">serwera aplikacji SAP</a> lub <a href="https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm" target="_blank">SAP Message Server</a>

* Pobierz i zainstaluj najnowszą wersję [lokalnej bramy danych](https://www.microsoft.com/download/details.aspx?id=53127) na każdym komputerze w środowisku lokalnym. Upewnij się, że możesz skonfigurować bramę w witrynie Azure portal przed kontynuowaniem. Bramy umożliwia bezpieczny dostęp do danych i zasoby są w środowisku lokalnym. Aby uzyskać więcej informacji, zobacz [instalacji lokalnej bramy danych usługi Azure Logic Apps](../logic-apps/logic-apps-gateway-install.md).

* Pobierz i zainstaluj najnowsze biblioteki klienta SAP, która jest obecnie <a href="https://softwaredownloads.sap.com/file/0020000000086282018" target="_blank">łącznika systemu SAP (NCo) 3.0.20.0 dla programu Microsoft .NET Framework 4.0 i Windows 64-bitowy (x64)</a>, w tym samym komputerze co lokalna brama danych. Zainstaluj tę wersję lub nowszym z tych powodów:

  * Wcześniejszych wersjach SAP NCo mogą stać się zakleszczone więcej niż jeden IDoc komunikaty wysłane w tym samym czasie. 
  Ten warunek blokuje wszystkie nowsze wiadomości, które są wysyłane do miejsca docelowego SAP, powodując wiadomości, które przekraczają limit czasu.

  * Lokalna brama danych działa tylko w systemach 64-bitowych. 
  W przeciwnym razie otrzymasz błąd "Nieprawidłowy obraz" ponieważ usługa hosta bramy danych nie obsługuje zestawów 32-bitowych.

  * Zarówno usługa host bramy danych, jak i Microsoft SAP Adapter korzystają z .NET Framework 4.5. NCo SAP dla programu .NET Framework 4.0 współpracuje z procesami, które używają środowiska uruchomieniowego .NET 4.0 i 4.7.1. 
  NCo SAP dla platformy .NET Framework 2.0 współpracuje z procesami, które używają środowiska uruchomieniowego .NET 2.0 i 3.5 i nie będzie działać z najnowszą lokalnej bramy danych.

* Zawartość wiadomości wysyłanych do serwera SAP, np. przykładowego pliku IDoc. Ta zawartość musi być w formacie XML i zawierać przestrzeń nazw dla akcji SAP, którego chcesz użyć.

<a name="add-trigger"></a>

## <a name="add-http-request-trigger"></a>Dodawanie wyzwalacza żądania HTTP

W usłudze Azure Logic Apps, każda aplikacja logiki musi rozpoczynać się [wyzwalacza](../logic-apps/logic-apps-overview.md#logic-app-concepts), który jest aktywowany wystąpienia, gdy zajdzie określone zdarzenie lub po spełnieniu określonego warunku. Każdym aktywowaniu wyzwalacza aparat usługi Logic Apps tworzy wystąpienie aplikacji logiki i uruchamiania przepływu pracy Twojej aplikacji.

W tym przykładzie utworzysz aplikację logiki z punktem końcowym usługi na platformie Azure tak, aby można było wysyłać *żądania HTTP POST* do aplikacji logiki. Gdy Twoja aplikacja logiki odbiera tych żądań HTTP, aktywuje się i uruchamia następny krok w przepływie pracy.

1. W witrynie Azure portal utworzyć pustej aplikacji logiki, który zostanie otwarty projektant aplikacji logiki. 

2. W polu wyszukiwania wpisz "żądanie http" jako filtr. Z listy wyzwalaczy wybierz następujący wyzwalacz: **Żądania — po odebraniu żądania HTTP**

   ![Dodawanie wyzwalacza żądania HTTP](./media/logic-apps-using-sap-connector-old/add-trigger.png)

3. Teraz Zapisz aplikację logiki, dzięki czemu można wygenerować adresu URL punktu końcowego dla aplikacji logiki.
Na pasku narzędzi projektanta wybierz pozycję **Zapisz**. 

   Punkt końcowy adres URL jest teraz wyświetlany w wyzwalacza, na przykład:

   ![Generowanie adresu URL punktu końcowego](./media/logic-apps-using-sap-connector-old/generate-http-endpoint-url.png)

<a name="add-action"></a>

## <a name="add-sap-action"></a>Dodawanie akcji SAP

W usłudze Azure Logic Apps [akcji](../logic-apps/logic-apps-overview.md#logic-app-concepts) jest to krok w przepływie pracy, występującego wyzwalacza lub innej akcji. Jeśli nie zostały jeszcze dodane wyzwalacza do aplikacji logiki, a chcesz kontynuować ten przykład [Dodaj wyzwalacz, opisane w tej sekcji](#add-trigger).

1. W Projektancie aplikacji logiki w obszarze wyzwalacza wybierz **nowy krok** > **Dodaj akcję**.

   ![Dodawanie akcji](./media/logic-apps-using-sap-connector-old/add-action.png) 

2. W polu wyszukiwania wprowadź "serwer sap" jako filtr. Z listy akcji wybierz akcję dla serwera SAP: 

   * **Serwer aplikacji SAP — Wyślij SAP**
   * **Serwer komunikatów SAP — Wyślij SAP**

   W tym przykładzie użyto tej akcji: **Serwer aplikacji SAP — Wyślij SAP**

   ![Wybierz opcję "Serwer aplikacji SAP" lub "Serwer SAP komunikat"](media/logic-apps-using-sap-connector-old/select-sap-action.png)

3. Jeśli zostanie wyświetlony monit, aby uzyskać informacje dotyczące połączenia, należy utworzyć połączenie SAP teraz. W przeciwnym razie istnieje już połączenie, przejdź do następnego kroku, dzięki czemu możesz skonfigurować akcję SAP. 

   **Utwórz połączenie SAP w środowisku lokalnym**

   1. Dla **bram**, wybierz opcję **Połącz za pośrednictwem lokalnej bramy danych** tak, aby wyświetlane właściwości połączenia lokalnego.

   2. Podaj informacje o połączeniu dla serwera SAP. 
   Aby uzyskać **bramy** właściwości, wybierz bramę danych, utworzonego w witrynie Azure portal dla instalacji bramy, na przykład:

      **Serwer aplikacji SAP**

      ![Utwórz połączenie z serwerem aplikacji SAP](./media/logic-apps-using-sap-connector-old/create-SAP-app-server-connection.png)  

      **SAP Message Server**

      ![Utwórz połączenie z serwerem SAP wiadomości](media/logic-apps-using-sap-connector-old/create-SAP-message-server-connection.png) 

   2. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

      Usługa Logic Apps, konfiguruje i testuje połączenie, upewniając się, że połączenie działa poprawnie.

4. Teraz Znajdź i wybierz akcję z serwera SAP. 

   1. W **akcji SAP** , wybierz ikonę folderu. 
   Z listy folderów Znajdź i wybierz akcję, którą chcesz użyć. 

      W tym przykładzie wybiera **IDOC** kategorii dla akcji IDoc. 

      ![Znajdowanie i wybieranie akcji IDoc](./media/logic-apps-using-sap-connector-old/SAP-app-server-find-action.png)

      Jeśli nie możesz znaleźć akcję, którą chcesz, możesz ręcznie wprowadzić ścieżkę, na przykład:

      ![Ręcznie Podaj ścieżkę do akcji IDoc](./media/logic-apps-using-sap-connector-old/SAP-app-server-manually-enter-action.png)

      Aby uzyskać więcej informacji na temat operacji IDoc zobacz [komunikatu schematów dla operacji IDOC](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)

   2. Kliknij wewnątrz **komunikat wejściowy** pola tak, aby wyświetlić listę zawartości dynamicznej. 
   W tym listy, w obszarze **zostanie odebrane żądanie po HTTP**, wybierz opcję **treści** pola. 

      Ten krok obejmuje treść z wyzwalacza żądania HTTP i wysyła go do serwera SAP.

      ![Zaznacz pole "Treść"](./media/logic-apps-using-sap-connector-old/SAP-app-server-action-select-body.png)

      Gdy skończysz, Twoja Akcja SAP będzie wyglądać następująco:

      ![Zakończenie akcji SAP](./media/logic-apps-using-sap-connector-old/SAP-app-server-complete-action.png)

6. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

<a name="add-response"></a>

## <a name="add-http-response-action"></a>Dodawanie akcji odpowiedzi HTTP

Teraz Dodaj akcję odpowiedzi do przepływu pracy aplikacji logiki i zawierają dane wyjściowe z akcji SAP. W ten sposób Twoja aplikacja logiki zwraca wyniki z serwera SAP do oryginalnego obiektu żądającego. 

1. W Projektancie aplikacji logiki w obszarze akcji SAP wybierz **nowy krok** > **Dodaj akcję**.

2. W polu wyszukiwania wprowadź "response" jako filtr. Z listy akcji wybierz następującą akcję: **Żądanie - odpowiedź**

3. Kliknij wewnątrz **treści** pola tak, aby wyświetlić listę zawartości dynamicznej. Z tej listy w obszarze **wysyłać SAP**, wybierz opcję **treści** pola. 

   ![Zakończenie akcji SAP](./media/logic-apps-using-sap-connector-old/select-sap-body-for-response-action.png)

4. Zapisz aplikację logiki. 

## <a name="test-your-logic-app"></a>Przetestuj aplikację logiki

1. Jeśli Twoja aplikacja logiki nie zostało jeszcze włączone, w menu aplikacji logiki, wybierz opcję **Przegląd**. Na pasku narzędzi wybierz **Włącz**. 

2. Na pasku narzędzi Projektanta aplikacji logiki wybierz **Uruchom**. Ten krok spowoduje ręczne uruchomienie aplikacji logiki.

3. Wyzwolenie aplikacji logiki, wysyłając żądanie HTTP POST do adresu URL w wyzwalaczu żądania HTTP, a zawierają wiadomości zawartości z żądaniem. Wyślij żądanie, można użyć narzędzia takie jak [Postman](https://www.getpostman.com/apps). 

   W tym artykule żądania wysyła plik IDoc, który musi być w formacie XML i zawierać przestrzeń nazw dla akcji SAP, którego używasz, na przykład: 

   ``` xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//620/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

4. Po wysłaniu żądania HTTP, oczekiwania na odpowiedź z aplikacji logiki.

> [!NOTE]
> Twoja aplikacja logiki może upłynąć limit czasu, jeśli nie zostają ukończone wszystkie kroki, które są wymagane dla odpowiedzi w ramach [limit czasu żądania](./logic-apps-limits-and-config.md). Jeśli ten stan występuje, zablokowane żądania. Aby ułatwić diagnozowanie problemów, Dowiedz się, jak [Sprawdź i monitorowanie aplikacji logiki](../logic-apps/logic-apps-monitor-your-logic-apps.md).

Gratulacje, utworzono aplikację logiki, który może komunikować się z serwerem SAP. Teraz, gdy skonfigurowano połączenie usługi SAP dla aplikacji logiki, możesz eksplorować inne dostępne akcje SAP, takie jak BAPI i RFC.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne dotyczące łącznika, zgodnie z opisem w plików struktury Swagger łączników programu zobacz następujące artykuły odwołania: 

* [Serwer aplikacji SAP](/connectors/sapapplicationserver/)
* [SAP Message Server](/connectors/sapmessageserver/)

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

* [Nawiązać połączenie z lokalnymi systemami](../logic-apps/logic-apps-gateway-connection.md) z aplikacji logiki
* Dowiedz się, jak sprawdzanie poprawności, przekształcania i inne operacje dotyczące wiadomości z [pakiet integracyjny dla przedsiębiorstw](../logic-apps/logic-apps-enterprise-integration-overview.md)
* Dowiedz się więcej o innych [łączników Logic Apps](../connectors/apis-list.md)
