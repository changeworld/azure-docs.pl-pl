---
title: Łączenie z systemami SAP — Azure Logic Apps | Microsoft Docs
description: Jak uzyskać dostęp do zasobów SAP i zarządzać nimi przez Automatyzowanie przepływów pracy za pomocą Azure Logic Apps
author: ecfan
ms.author: estfan
ms.date: 05/31/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, divswa, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 31f6a3fc281b8dc309ddcd237246c870c85ae20b
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69971639"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Łączenie z systemami SAP z Azure Logic Apps

> [!NOTE]
> Dla tego łącznika SAP zaplanowano wycofanie. Użyj lub Przeprowadź migrację do [nowszego i bardziej zaawansowanego łącznika SAP](./logic-apps-using-sap-connector.md). 

W tym artykule pokazano, jak uzyskać dostęp do zasobów SAP z poziomu aplikacji logiki za pomocą łącznika serwera aplikacji SAP i serwera komunikatów SAP. Dzięki temu można zautomatyzować zadania, procesy i przepływy pracy służące do zarządzania danymi i zasobami SAP przez tworzenie aplikacji logiki.

W tym przykładzie zastosowano aplikację logiki, którą można wyzwolić za pomocą żądania HTTP. Aplikacja logiki wysyła dokument pośredni (IDoc) do serwera SAP i zwraca odpowiedź do żądającego, który wywołał aplikację logiki.
Bieżące łączniki SAP mają akcje, ale nie są wyzwalane, więc w tym przykładzie używa [wyzwalacza żądania HTTP](../connectors/connectors-native-reqres.md) jako pierwszego kroku w przepływie pracy aplikacji logiki. Aby uzyskać informacje techniczne dotyczące łącznika SAP, zobacz następujące artykuły referencyjne: 

* <a href="https://docs.microsoft.com/connectors/sap" target="blank">Łącznik serwera aplikacji SAP</a>
* <a href="https://docs.microsoft.com/connectors/sap/#send-message-to-sap" target="blank">Łącznik serwera komunikatów SAP</a>

Jeśli nie masz jeszcze subskrypcji platformy Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się, aby skorzystać z bezpłatnego konta platformy Azure</a>.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać czynności opisane w tym artykule, potrzebne są następujące elementy:

* Aplikacja logiki z lokalizacji, w której chcesz uzyskać dostęp do systemu SAP, i wyzwalacza, który uruchamia przepływ pracy aplikacji logiki. Łączniki SAP obecnie udostępniają tylko akcje. Jeśli jesteś nowym usługą Logic Apps, zapoznaj [się](../logic-apps/logic-apps-overview.md) z tematem [Azure Logic Apps i szybki start: Utwórz swoją pierwszą aplikację](../logic-apps/quickstart-create-first-logic-app-workflow.md)logiki.

* <a href="https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server" target="_blank">Serwer aplikacji SAP</a> lub <a href="https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm" target="_blank">serwer komunikatów SAP</a>

* Pobierz i zainstaluj najnowszą [lokalną bramę danych](https://www.microsoft.com/download/details.aspx?id=53127) na komputerze lokalnym. Przed kontynuowaniem upewnij się, że brama została skonfigurowana w Azure Portal. Brama pomaga w bezpiecznym dostępie do danych i zasobów lokalnych. Aby uzyskać więcej informacji, zobacz [Instalowanie lokalnej bramy danych dla Azure Logic Apps](../logic-apps/logic-apps-gateway-install.md).

* Pobierz i zainstaluj najnowszą bibliotekę klienta SAP, która obecnie jest <a href="https://softwaredownloads.sap.com/file/0020000000086282018" target="_blank">łącznikiem SAP (NCo) 3.0.20.0 dla Microsoft .NET Framework 4,0 i systemu Windows w wersji 64-bitowej (x64)</a>na tym samym komputerze, na którym znajduje się lokalna Brama danych. Zainstaluj tę wersję lub nowszą z następujących powodów:

  * Wcześniejsze wersje oprogramowania SAP NCo mogą stać się zakleszczeniami, gdy w tym samym czasie są wysyłane więcej niż jeden komunikat IDoc. 
  Ten warunek blokuje wszystkie późniejsze komunikaty wysyłane do miejsca docelowego SAP, co powoduje przekroczenie limitu czasu komunikatów.

  * Lokalna Brama danych działa tylko w systemach 64-bitowych. 
  W przeciwnym razie zostanie wyświetlony komunikat o błędzie "zły obraz", ponieważ usługa hosta bramy danych nie obsługuje zestawów 32-bitowych.

  * Zarówno usługa hosta bramy danych, jak i karta Microsoft SAP używają .NET Framework 4,5. Rozwiązanie SAP NCo dla .NET Framework 4,0 współpracuje z procesami, które korzystają z środowiska uruchomieniowego .NET 4,0 do 4.7.1. 
  System SAP NCo dla .NET Framework 2,0 współpracuje z procesami, które korzystają z programu .NET Runtime 2,0 do 3,5 i nie będą już działać z najnowszą lokalną bramą danych.

* Zawartość komunikatu, którą można wysłać do serwera SAP, na przykład przykładowego pliku IDoc. Ta zawartość musi być w formacie XML i zawierać przestrzeń nazw dla akcji SAP, która ma zostać użyta.

<a name="add-trigger"></a>

## <a name="add-http-request-trigger"></a>Dodawanie wyzwalacza żądania HTTP

W Azure Logic Apps każda aplikacja logiki musi rozpoczynać się od [wyzwalacza](../logic-apps/logic-apps-overview.md#logic-app-concepts), który jest uruchamiany w przypadku wystąpienia konkretnego zdarzenia lub spełnienia określonego warunku. Za każdym razem, gdy wyzwala wyzwalacz, aparat Logic Apps tworzy wystąpienie aplikacji logiki i uruchamia przepływ pracy aplikacji.

W tym przykładzie utworzysz aplikację logiki z punktem końcowym na platformie Azure, aby można było wysyłać *żądania HTTP Post* do aplikacji logiki. Gdy aplikacja logiki otrzymuje te żądania HTTP, wyzwalacz jest uruchamiany i uruchamia następny krok w przepływie pracy.

1. W Azure Portal Utwórz pustą aplikację logiki, która otwiera projektanta aplikacji logiki. 

2. W polu wyszukiwania wprowadź ciąg "żądanie HTTP" jako filtr. Z listy Wyzwalacze wybierz następujący wyzwalacz: **Żądanie — po odebraniu żądania HTTP**

   ![Dodawanie wyzwalacza żądania HTTP](./media/logic-apps-using-sap-connector-old/add-trigger.png)

3. Teraz Zapisz aplikację logiki, aby można było wygenerować adres URL punktu końcowego dla aplikacji logiki.
Na pasku narzędzi projektanta wybierz pozycję **Zapisz**. 

   Adres URL punktu końcowego jest teraz wyświetlany w wyzwalaczu, na przykład:

   ![Generuj adres URL dla punktu końcowego](./media/logic-apps-using-sap-connector-old/generate-http-endpoint-url.png)

<a name="add-action"></a>

## <a name="add-sap-action"></a>Dodaj akcję SAP

W Azure Logic Apps [Akcja](../logic-apps/logic-apps-overview.md#logic-app-concepts) to krok w przepływie pracy, który następuje po wyzwalaczu lub innej akcji. Jeśli wyzwalacz nie został jeszcze dodany do aplikacji logiki i chcesz postępować zgodnie z tym przykładem, [Dodaj wyzwalacz opisany w tej sekcji](#add-trigger).

1. W Projektancie aplikacji logiki w obszarze wyzwalacza wybierz pozycję **nowy krok** > **Dodaj akcję**.

   ![Dodaj akcję](./media/logic-apps-using-sap-connector-old/add-action.png) 

2. W polu wyszukiwania wprowadź ciąg "serwer SAP" jako filtr. Z listy Akcje wybierz akcję dla serwera SAP: 

   * **Serwer aplikacji SAP — wysyłanie do SAP**
   * **Serwer komunikatów SAP — wysyłanie do SAP**

   W tym przykładzie używa tej akcji: **Serwer aplikacji SAP — wysyłanie do SAP**

   ![Wybierz pozycję "serwer aplikacji SAP" lub "serwer komunikatów SAP"](media/logic-apps-using-sap-connector-old/select-sap-action.png)

3. Jeśli zostanie wyświetlony monit o podanie szczegółów połączenia, Utwórz połączenie SAP teraz. W przeciwnym razie, jeśli połączenie już istnieje, przejdź do następnego kroku, aby móc skonfigurować akcję SAP. 

   **Tworzenie lokalnego połączenia SAP**

   1. W przypadku **bram**wybierz opcję **Połącz za pośrednictwem lokalnej bramy danych** , aby wyświetlić właściwości połączenia lokalnego.

   2. Podaj informacje o połączeniu dla serwera SAP. 
   Dla właściwości **brama** Wybierz bramę danych utworzoną w Azure Portal na potrzeby instalacji bramy, na przykład:

      **Serwer aplikacji SAP**

      ![Utwórz połączenie z serwerem aplikacji SAP](./media/logic-apps-using-sap-connector-old/create-SAP-app-server-connection.png)  

      **Serwer komunikatów SAP**

      ![Utwórz połączenie z serwerem komunikatów SAP](media/logic-apps-using-sap-connector-old/create-SAP-message-server-connection.png) 

   2. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

      Logic Apps konfiguruje i testuje połączenie, upewniając się, że połączenie działa poprawnie.

4. Teraz Znajdź i wybierz akcję z serwera SAP. 

   1. W polu **Akcja SAP** wybierz ikonę folderu. 
   Z listy folder Znajdź i wybierz akcję, której chcesz użyć. 

      Ten przykład wybiera kategorię **IDOC** dla akcji IDOC. 

      ![Znajdź i wybierz akcję IDoc](./media/logic-apps-using-sap-connector-old/SAP-app-server-find-action.png)

      Jeśli nie możesz znaleźć żądanej akcji, możesz ręcznie wprowadzić ścieżkę, na przykład:

      ![Ręcznie podaj ścieżkę do akcji IDoc](./media/logic-apps-using-sap-connector-old/SAP-app-server-manually-enter-action.png)

      Aby uzyskać więcej informacji o operacjach IDoc, zobacz [schematy komunikatów dla operacji IDOC](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)

   2. Kliknij wewnątrz pola **komunikat wejściowy** , aby wyświetlić listę zawartości dynamicznej. 
   Na tej liście, w obszarze **po odebraniu żądania HTTP**, zaznacz pole **treść** . 

      Ten krok obejmuje zawartość treści z wyzwalacza żądania HTTP i wysyła te dane wyjściowe do serwera SAP.

      ![Wybierz pole "treść"](./media/logic-apps-using-sap-connector-old/SAP-app-server-action-select-body.png)

      Gdy skończysz, Twoja akcja SAP będzie wyglądać następująco:

      ![Ukończ akcję SAP](./media/logic-apps-using-sap-connector-old/SAP-app-server-complete-action.png)

6. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

<a name="add-response"></a>

## <a name="add-http-response-action"></a>Dodaj akcję odpowiedzi HTTP

Teraz Dodaj akcję odpowiedzi do przepływu pracy aplikacji logiki i Uwzględnij dane wyjściowe akcji SAP. Dzięki temu aplikacja logiki zwraca wyniki z serwera SAP do oryginalnego obiektu żądającego. 

1. W Projektancie aplikacji logiki w obszarze Akcja SAP wybierz pozycję **nowy krok** > **Dodaj akcję**.

2. W polu wyszukiwania wprowadź "odpowiedź" jako filtr. Z listy Akcje wybierz tę akcję: **Żądanie-odpowiedź**

3. Kliknij wewnątrz pola **treść** , aby wyświetlić listę zawartości dynamicznej. Z tej listy w obszarze **Wyślij do SAP**wybierz pole **treść** . 

   ![Ukończ akcję SAP](./media/logic-apps-using-sap-connector-old/select-sap-body-for-response-action.png)

4. Zapisz aplikację logiki. 

## <a name="test-your-logic-app"></a>Testowanie aplikacji logiki

1. Jeśli aplikacja logiki nie została już włączona, w menu aplikacji logiki wybierz pozycję **Przegląd**. Na pasku narzędzi wybierz pozycję **Włącz**. 

2. Na pasku narzędzi projektanta aplikacji logiki wybierz **Uruchom**. Ten krok polega na ręcznym uruchomieniu aplikacji logiki.

3. Wyzwól aplikację logiki, wysyłając żądanie HTTP POST do adresu URL w wyzwalaczu żądania HTTP i dołączając zawartość wiadomości do żądania. Aby wysłać żądanie, można użyć narzędzia, takiego jak Poster. [](https://www.getpostman.com/apps) 

   W tym artykule żądanie wysyła plik IDoc, który musi być w formacie XML i zawiera przestrzeń nazw dla używanej akcji SAP, na przykład: 

   ``` xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//620/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

4. Po wysłaniu żądania HTTP poczekaj na odpowiedź z aplikacji logiki.

> [!NOTE]
> Aplikacja logiki może przekroczyć limit czasu, jeśli wszystkie kroki wymagane do odpowiedzi nie zakończą się w ramach [limitu czasu żądania](./logic-apps-limits-and-config.md). W przypadku tego stanu żądania mogą zostać zablokowane. Aby ułatwić diagnozowanie problemów, Dowiedz się, jak można [sprawdzić i monitorować aplikacje logiki](../logic-apps/logic-apps-monitor-your-logic-apps.md).

Gratulacje, utworzono aplikację logiki, która może komunikować się z serwerem SAP. Teraz, po skonfigurowaniu połączenia SAP dla aplikacji logiki, można eksplorować inne dostępne akcje SAP, takie jak BAPI i RFC.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne na temat łącznika zgodnie z opisem w plikach struktury Swagger łączników, zobacz następujące artykuły referencyjne: 

* [Serwer aplikacji SAP](/connectors/sap)
* [Serwer komunikatów SAP](/connectors/sap/#send-message-to-sap)

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Następne kroki

* [Łączenie z systemami lokalnymi](../logic-apps/logic-apps-gateway-connection.md) z usługi Logic Apps
* Informacje dotyczące sprawdzania poprawności, przekształcania i innych operacji na komunikatach przy użyciu [pakiet integracyjny dla przedsiębiorstw](../logic-apps/logic-apps-enterprise-integration-overview.md)
* Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md)
