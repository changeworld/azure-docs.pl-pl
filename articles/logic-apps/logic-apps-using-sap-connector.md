---
title: Łączenie się z systemami SAP
description: Uzyskiwanie dostępu do zasobów SAP i zarządzanie nimi przez automatyzację przepływów pracy za pomocą aplikacji Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 08/30/2019
tags: connectors
ms.openlocfilehash: 39ab222f64d964e95b16e043c9cdeccd8170ace3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651019"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Łączenie z systemami SAP z usługi Azure Logic Apps

> [!IMPORTANT]
> Wcześniejsze łączniki serwera aplikacji SAP i serwera komunikatów SAP są przestarzałe 29 lutego 2020 r. Bieżące złącze SAP konsoliduje te poprzednie złącza SAP, dzięki czemu nie trzeba zmieniać typu połączenia, jest w pełni zgodne z poprzednimi łącznikami, zapewnia wiele dodatkowych możliwości i kontynuuje korzystanie z biblioteki łączników SAP .Net ( SAP NCo).
>
> W przypadku aplikacji logiki, które używają starszych łączników, należy [przeprowadzić migrację do najnowszego łącznika](#migrate) przed datą wycofania. W przeciwnym razie te aplikacje logiki wystąpią błędy wykonywania i nie będzie można wysyłać wiadomości do systemu SAP.

W tym artykule pokazano, jak można uzyskać dostęp do lokalnych zasobów SAP z wewnątrz aplikacji logiki przy użyciu łącznika SAP. Łącznik współpracuje z klasycznymi wersjami SAP, takimi jak systemy R/3 i ECC lokalnie. Łącznik umożliwia również integrację z nowszymi systemami SAP opartymi na systemie SAP opartymi na systemie SAP, takimi jak S/4 HANA, niezależnie od tego, czy są hostowane lokalnie, czy w chmurze. Łącznik SAP obsługuje integrację wiadomości lub danych do i z systemów opartych na SAP NetWeaver za pośrednictwem dokumentu pośredniego (IDoc), interfejsu programowania aplikacji biznesowych (BAPI) lub wywołania funkcji zdalnych (RFC).

Łącznik SAP używa [biblioteki łącznika SAP .NET (NCo)](https://support.sap.com/en/product/connectors/msnet.html) i udostępnia następujące akcje:

* **Wyślij wiadomość do SAP:** Wyślij IDoc przez tRFC, wywołaj funkcje BAPI za pomocą RFC lub zadzwoń do RFC/tRFC w systemach SAP.
* **Po odebraniu wiadomości z SAP:** Receive IDoc przez tRFC, wywołaj funkcje BAPI za pomocą tRFC lub wywołaj RFC/tRFC w systemach SAP.
* **Generowanie schematów:** Generowanie schematów dla artefaktów SAP dla IDoc, BAPI lub RFC.

W przypadku tych operacji łącznik SAP obsługuje uwierzytelnianie podstawowe za pośrednictwem nazw użytkowników i haseł. Złącze obsługuje również [bezpieczną komunikację sieciową (SNC).](https://help.sap.com/doc/saphelp_nw70/7.0.31/e6/56f466e99a11d1a5b00000e835363f/content.htm?no_cache=true) SNC może być używany do logowania jednokrotnego SAP NetWeaver (SSO) lub dodatkowych funkcji zabezpieczeń zapewnianych przez zewnętrzny produkt zabezpieczeń.

Łącznik SAP integruje się z lokalnymi systemami SAP za pośrednictwem [lokalnej bramy danych.](../logic-apps/logic-apps-gateway-connection.md) W scenariuszach wysyłania, na przykład, gdy wiadomość jest wysyłana z aplikacji logiki do systemu SAP, brama danych działa jako klient RFC i przekazuje żądania odebrane z aplikacji logiki do sap. Podobnie w scenariuszach odbierania brama danych działa jako serwer RFC, który odbiera żądania od sap i przekazuje je do aplikacji logiki.

W tym artykule pokazano, jak utworzyć przykładowe aplikacje logiki, które integrują się z SAP podczas obejmujące wcześniej opisane scenariusze integracji. W przypadku aplikacji logiki, które używają starszych łączników SAP, w tym artykule pokazano, jak migrować aplikacje logiki do najnowszego łącznika SAP.

<a name="pre-reqs"></a>

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać ten artykuł, potrzebujesz następujących elementów:

* Subskrypcja platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, [zarejestruj się, aby uzyskać bezpłatne konto platformy Azure.](https://azure.microsoft.com/free/)

* Aplikacja logiki, z której chcesz uzyskać dostęp do systemu SAP i wyzwalacz, który uruchamia przepływ pracy aplikacji logiki. Jeśli jesteś nowy w aplikacjach logiki, zobacz [Co to jest usługa Azure Logic Apps?](../logic-apps/logic-apps-overview.md) i Szybki [start: Tworzenie pierwszej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* [Serwer aplikacji SAP](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) lub serwer [komunikatów SAP](https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm).

* Pobierz i zainstaluj najnowszą [lokalną bramę danych na](https://www.microsoft.com/download/details.aspx?id=53127) dowolnym komputerze lokalnym. Przed kontynuowaniem upewnij się, że brama została skonfigurowana w witrynie Azure portal. Brama ułatwia bezpieczny dostęp do lokalnych danych i zasobów. Aby uzyskać więcej informacji, zobacz [Instalowanie lokalnej bramy danych dla usługi Azure Logic Apps.](../logic-apps/logic-apps-gateway-install.md)

* Jeśli używasz SNC z SSO, upewnij się, że brama jest uruchomiona jako użytkownik, który jest mapowany względem użytkownika SAP. Aby zmienić konto domyślne, wybierz pozycję **Zmień konto**i wprowadź poświadczenia użytkownika.

  ![Zmienianie konta bramy](./media/logic-apps-using-sap-connector/gateway-account.png)

* Jeśli włączysz usługę SNC za pomocą zewnętrznego produktu zabezpieczającego, skopiuj bibliotekę SNC lub pliki na tym samym komputerze, na którym jest zainstalowana brama. Niektóre przykłady produktów SNC to [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), Kerberos i NTLM.

* Pobierz i zainstaluj najnowszą bibliotekę klienta SAP, która jest obecnie [SAP Connector (NCo 3.0) dla firmy Microsoft .NET 3.0.22.0 skompilowany z .NET Framework 4.0 - Windows 64-bit (x64)](https://softwaredownloads.sap.com/file/0020000001000932019), na tym samym komputerze co lokalna brama danych. Zainstaluj tę wersję lub później z następujących powodów:

  * Wcześniejsze wersje SAP NCo mogą stać się zakleszczone, gdy w tym samym czasie zostanie wysłana więcej niż jedna wiadomość IDoc. Ten warunek blokuje wszystkie późniejsze wiadomości, które są wysyłane do miejsca docelowego SAP, co powoduje, że komunikaty do przesunięcia czasu.
  
  * Lokalna brama danych działa tylko w systemach 64-bitowych. W przeciwnym razie zostanie wyświetlony błąd "zły obraz", ponieważ usługa hosta bramy danych nie obsługuje zestawów 32-bitowych.
  
  * Zarówno usługa hosta bramy danych, jak i karta Microsoft SAP karty używać programu .NET Framework 4.5. Sap NCo dla platformy .NET Framework 4.0 działa z procesami, które używają środowiska uruchomieniowego .NET 4.0 do 4.7.1. Sap NCo for .NET Framework 2.0 współpracuje z procesami, które używają środowiska uruchomieniowego .NET od 2.0 do 3.5, ale nie współpracuje już z najnowszą lokalną bramą danych.

* Zawartość wiadomości, którą można wysłać do serwera SAP, na przykładowy plik IDoc, musi być w formacie XML i zawierać obszar nazw dla akcji SAP, której chcesz użyć.

<a name="migrate"></a>

## <a name="migrate-to-current-connector"></a>Migracja do bieżącego łącznika

1. Jeśli jeszcze tego nie zrobiono, zaktualizuj [lokalną bramę danych,](https://www.microsoft.com/download/details.aspx?id=53127) aby mieć najnowszą wersję. Aby uzyskać więcej informacji, zobacz [Instalowanie lokalnej bramy danych dla usługi Azure Logic Apps.](../logic-apps/logic-apps-gateway-install.md)

1. W aplikacji logiki, która używa starszego łącznika SAP, usuń akcję **Wyślij do SAP.**

1. Z najnowszego łącznika SAP dodaj akcję **Wyślij wiadomość do SAP.** Zanim będzie można użyć tej akcji, ponownie stwórz połączenie z systemem SAP.

1. Po zakończeniu zapisz aplikację logiki.

<a name="add-trigger"></a>

## <a name="send-message-to-sap"></a>Wyślij wiadomość do SAP

W tym przykładzie użyto aplikacji logiki, którą można wyzwolić za pomocą żądania HTTP. Aplikacja logiki wysyła IDoc do serwera SAP i zwraca odpowiedź do żądacza, który nazwał aplikację logiki.

### <a name="add-an-http-request-trigger"></a>Dodawanie wyzwalacza żądania HTTP

W usłudze Azure Logic Apps każda aplikacja logiki musi zaczynać się od [wyzwalacza,](../logic-apps/logic-apps-overview.md#logic-app-concepts)który uruchamia się, gdy występuje określone zdarzenie lub gdy zostanie spełniony określony warunek. Za każdym razem, gdy wyzwalacz jest uruchamiany, aparat aplikacji logiki tworzy wystąpienie aplikacji logiki i uruchamia przepływ pracy aplikacji.

W tym przykładzie utworzysz aplikację logiki z punktem końcowym na platformie Azure, dzięki czemu można wysyłać *żądania HTTP POST* do aplikacji logiki. Gdy aplikacja logiki odbiera te żądania HTTP, wyzwalacz uruchamia i uruchamia następny krok w przepływie pracy.

1. W [witrynie Azure portal](https://portal.azure.com)utwórz pustą aplikację logiki, która otwiera projektanta aplikacji logiki.

1. W polu wyszukiwania `http request` wprowadź jako filtr. Z listy **Wyzwalacze** wybierz pozycję **Po odebraniu żądania HTTP**.

   ![Dodaj wyzwalacz żądania HTTP](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. Teraz zapisz aplikację logiki, dzięki czemu można wygenerować adres URL punktu końcowego dla aplikacji logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

   Adres URL punktu końcowego pojawia się teraz w wyzwalaczu, na przykład:

   ![Generowanie adresu URL dla punktu końcowego](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

<a name="add-action"></a>

### <a name="add-an-sap-action"></a>Dodawanie akcji SAP

W usłudze Azure Logic Apps [akcja](../logic-apps/logic-apps-overview.md#logic-app-concepts) jest krokiem w przepływie pracy, który następuje wyzwalacz lub innej akcji. Jeśli nie dodano jeszcze wyzwalacza do aplikacji logiki i chcesz wykonać ten przykład, [dodaj wyzwalacz opisany w tej sekcji](#add-trigger).

1. W projektancie aplikacji logiki w obszarze wyzwalacz wybierz pozycję **Nowy krok**.

   ![Dodawanie nowego kroku do aplikacji logiki](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. W polu wyszukiwania `sap` wprowadź jako filtr. Z listy **Akcje** wybierz pozycję **Wyślij wiadomość do sap**.
  
   ![Wybierz akcję "Wyślij wiadomość do SAP"](media/logic-apps-using-sap-connector/select-sap-send-action.png)

   Można też wybrać kartę **Przedsiębiorstwo** i wybrać akcję SAP.

   ![Wybierz akcję "Wyślij wiadomość do SAP" z karty Przedsiębiorstwo](media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

1. Jeśli połączenie już istnieje, przejdź do następnego kroku, aby skonfigurować akcję SAP. Jeśli jednak zostanie wyświetlony monit o podanie szczegółów połączenia, podaj informacje, aby teraz utworzyć połączenie z lokalnym serwerem SAP.

   1. Podaj nazwę połączenia.

   1. W sekcji **Brama danych** w obszarze **Subskrypcja**najpierw wybierz subskrypcję platformy Azure dla zasobu bramy utworzonego w witrynie Azure portal dla instalacji bramy. 
   
   1. W obszarze **Brama połączenia**wybierz zasób bramy.

   1. Kontynuuj dostarczanie informacji o połączeniu. W przypadku właściwości **Typ logowania** wykonaj krok na podstawie tego, czy właściwość jest **ustawiona** na Serwer aplikacji lub **Grupa:**
   
      * W przypadku **serwera aplikacji**wymagane są te właściwości, które zwykle wydają się opcjonalne:

        ![Tworzenie połączenia z serwerem aplikacji SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * W przypadku **grupy**wymagane są te właściwości, które zwykle wydają się opcjonalne:

        ![Tworzenie połączenia z serwerem komunikatów SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      Domyślnie silne wpisywanie jest używane do sprawdzania nieprawidłowych wartości przez wykonanie sprawdzania poprawności XML względem schematu. To zachowanie może pomóc wykryć problemy wcześniej. Opcja **Bezpieczne wpisywanie** jest dostępna dla zgodności z powrotem i sprawdza tylko długość ciągu. Dowiedz się więcej o [opcji Bezpieczne pisanie](#safe-typing).

   1. Po zakończeniu wybierz pozycję **Utwórz**.

      Logic Apps konfiguruje i testuje połączenie, aby upewnić się, że połączenie działa poprawnie.

1. Teraz znajdź i wybierz akcję z serwera SAP.

   1. W polu **Akcja SAP** wybierz ikonę folderu. Z listy plików znajdź i wybierz komunikat SAP, którego chcesz użyć. Aby nawigować po liście, użyj strzałek.

      W tym przykładzie wybiera IDoc z typem **Zamówienia.**

      ![Znajdowanie i wybieranie akcji IDoc](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

      Jeśli nie możesz znaleźć żądanej akcji, możesz ręcznie wprowadzić ścieżkę, na przykład:

      ![Ręcznie podaj ścieżkę do akcji IDoc](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

      > [!TIP]
      > Podaj wartość **akcji SAP** za pośrednictwem edytora wyrażeń. W ten sposób można użyć tej samej akcji dla różnych typów wiadomości.

      Aby uzyskać więcej informacji na temat operacji IDoc, zobacz [Schematy komunikatów dla operacji IDOC](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

   1. Kliknij wewnątrz pola **Komunikat wprowadzania,** aby wyświetlić dynamiczną listę zawartości. Z tej listy w obszarze **Po odebraniu żądania HTTP**wybierz pole **Treść.**

      Ten krok obejmuje zawartość treści z wyzwalacza żądania HTTP i wysyła to dane wyjściowe do serwera SAP.

      ![Wybierz właściwość "Body" z wyzwalacza](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      Po zakończeniu akcja SAP wygląda następująco:

      ![Ukończ akcję SAP](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

1. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

<a name="add-response"></a>

### <a name="add-an-http-response-action"></a>Dodawanie akcji odpowiedzi HTTP

Teraz dodaj akcję odpowiedzi do przepływu pracy aplikacji logiki i dołącz dane wyjściowe z akcji SAP. W ten sposób aplikacja logiki zwraca wyniki z serwera SAP do oryginalnego żądacza.

1. W projektancie aplikacji logiki w ramach akcji SAP wybierz pozycję **Nowy krok**.

1. W polu wyszukiwania `response` wprowadź jako filtr. Z listy **Akcje** wybierz pozycję **Odpowiedź**.

1. Kliknij wewnątrz pola **Treść,** aby wyświetlić dynamiczną listę zawartości. Z tej listy w obszarze **Wyślij wiadomość do SAP**wybierz pole **Treść.**

   ![Ukończ akcję SAP](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

1. Zapisz aplikację logiki.

### <a name="test-your-logic-app"></a>Testowanie aplikacji logiki

1. Jeśli aplikacja logiki nie jest jeszcze włączona, w menu aplikacji logiki wybierz pozycję **Przegląd**. Na pasku narzędzi wybierz pozycję **Włącz**.

1. Na pasku narzędzi projektanta wybierz pozycję **Uruchom**. Ten krok ręcznie uruchamia aplikację logiki.

1. Wyzwalanie aplikacji logiki przez wysłanie żądania HTTP POST do adresu URL w wyzwalaczu żądania HTTP.
Dołącz treść wiadomości do swojego żądania. Do wysłania żądania można użyć narzędzia, takiego jak [Listonosz](https://www.getpostman.com/apps).

   W tym artykule żądanie wysyła plik IDoc, który musi być w formacie XML i zawierać obszar nazw dla akcji SAP, której używasz, na przykład:

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/2/ORDERS05//720/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

1. Po wysłaniu żądania HTTP poczekaj na odpowiedź z aplikacji logiki.

   > [!NOTE]
   > Aplikacja logiki może przekroczyć limit czasu, jeśli wszystkie kroki wymagane dla odpowiedzi nie zakończą się w [terminie żądania.](./logic-apps-limits-and-config.md) Jeśli ten warunek się stanie, żądania mogą zostać zablokowane. Aby ułatwić diagnozowanie problemów, dowiedz się, jak [sprawdzać i monitorować aplikacje logiki.](../logic-apps/monitor-logic-apps.md)

Utworzono aplikację logiki, która może komunikować się z serwerem SAP. Teraz, gdy masz skonfigurowane połączenie SAP dla aplikacji logiki, można eksplorować inne dostępne akcje SAP, takie jak BAPI i RFC.

<a name="receive-from-sap"></a>

## <a name="receive-message-from-sap"></a>Odbieranie wiadomości od sap

W tym przykładzie użyto aplikacji logiki, która wyzwala, gdy aplikacja odbiera komunikat z systemu SAP.

### <a name="add-an-sap-trigger"></a>Dodawanie wyzwalacza SAP

1. W witrynie Azure portal utwórz pustą aplikację logiki, która otwiera projektanta aplikacji logiki.

1. W polu wyszukiwania `sap` wprowadź jako filtr. Z listy **Wyzwalacze** wybierz pozycję **Po odebraniu wiadomości z sap**.

   ![Dodaj wyzwalacz SAP](./media/logic-apps-using-sap-connector/add-sap-trigger-logic-app.png)

   Możesz też wybrać kartę **Przedsiębiorstwo,** a następnie wybrać wyzwalacz:

   ![Karta Dodawanie wyzwalacza SAP z karty Przedsiębiorstwo](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

1. Jeśli połączenie już istnieje, przejdź do następnego kroku, aby skonfigurować akcję SAP. Jeśli jednak zostanie wyświetlony monit o podanie szczegółów połączenia, podaj informacje, aby teraz utworzyć połączenie z lokalnym serwerem SAP.

   1. Podaj nazwę połączenia.

   1. W sekcji **Brama danych** w obszarze **Subskrypcja**najpierw wybierz subskrypcję platformy Azure dla zasobu bramy utworzonego w witrynie Azure portal dla instalacji bramy. 

   1. W obszarze **Brama połączenia**wybierz zasób bramy.

   1. Kontynuuj dostarczanie informacji o połączeniu. W przypadku właściwości **Typ logowania** wykonaj krok na podstawie tego, czy właściwość jest **ustawiona** na Serwer aplikacji lub **Grupa:**

      * W przypadku **serwera aplikacji**wymagane są te właściwości, które zwykle wydają się opcjonalne:

        ![Tworzenie połączenia z serwerem aplikacji SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * W przypadku **grupy**wymagane są te właściwości, które zwykle wydają się opcjonalne:

        ![Tworzenie połączenia z serwerem komunikatów SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

      Domyślnie silne wpisywanie jest używane do sprawdzania nieprawidłowych wartości przez wykonanie sprawdzania poprawności XML względem schematu. To zachowanie może pomóc wykryć problemy wcześniej. Opcja **Bezpieczne wpisywanie** jest dostępna dla zgodności z powrotem i sprawdza tylko długość ciągu. Dowiedz się więcej o [opcji Bezpieczne pisanie](#safe-typing).

   1. Po zakończeniu wybierz pozycję **Utwórz**.

      Logic Apps konfiguruje i testuje połączenie, aby upewnić się, że połączenie działa poprawnie.

1. Podaj wymagane parametry na podstawie konfiguracji systemu SAP.

   Opcjonalnie można podać jedną lub więcej akcji SAP. Ta lista akcji określa komunikaty, które wyzwalacz odbiera z serwera SAP za pośrednictwem bramy danych. Pusta lista określa, że wyzwalacz odbiera wszystkie komunikaty. Jeśli lista zawiera więcej niż jedną wiadomość, wyzwalacz odbiera tylko komunikaty określone na liście. Wszystkie inne wiadomości wysyłane z serwera SAP są odrzucane przez bramę.

   Możesz wybrać akcję SAP z selektora plików:

   ![Dodawanie akcji SAP do aplikacji logiki](media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

   Możesz też ręcznie określić akcję:

   ![Ręczne wprowadzanie akcji SAP](media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png)

   Oto przykład, który pokazuje, jak akcja pojawia się po skonfigurowaniu wyzwalacza, aby otrzymać więcej niż jedną wiadomość.

   ![Przykład wyzwalania, który odbiera wiele wiadomości](media/logic-apps-using-sap-connector/example-trigger.png)

   Aby uzyskać więcej informacji na temat akcji SAP, zobacz [Schematy wiadomości dla operacji IDOC](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)

1. Teraz zapisz aplikację logiki, dzięki czemu można rozpocząć odbieranie wiadomości z systemu SAP. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

Aplikacja logiki jest teraz gotowa do odbierania wiadomości z systemu SAP.

> [!NOTE]
> Wyzwalacz SAP nie jest wyzwalaczem sondowania, ale jest wyzwalaczem opartym na interfejsie webhook. Wyzwalacz jest wywoływany z bramy tylko wtedy, gdy istnieje komunikat, więc nie jest konieczne sondowanie.

### <a name="test-your-logic-app"></a>Testowanie aplikacji logiki

1. Aby wyzwolić aplikację logiki, wyślij wiadomość z systemu SAP.

1. W menu aplikacji logiki wybierz pozycję **Przegląd**. Przejrzyj **historię przebiegów** dla wszystkich nowych przebiegów dla aplikacji logiki.

1. Otwórz najnowsze uruchomienie, które pokazuje komunikat wysłany z systemu SAP w sekcji wyjścia wyzwalacza.

## <a name="receive-idoc-packets-from-sap"></a>Odbieranie pakietów IDOC z sap

Można skonfigurować sap do [wysyłania IDOC w pakietach,](https://help.sap.com/viewer/8f3819b0c24149b5959ab31070b64058/7.4.16/en-US/4ab38886549a6d8ce10000000a42189c.html)które są partiami lub grupami IDOC. Aby odbierać pakiety IDOC, łącznik SAP, a w szczególności wyzwalacz, nie wymaga dodatkowej konfiguracji. Jednak aby przetworzyć każdy element w pakiecie IDOC po odebraniu pakietu przez wyzwalacz, konieczne są dodatkowe kroki, aby podzielić pakiet na poszczególne iDOC.

Oto przykład, który pokazuje, jak wyodrębnić poszczególne IDOC z pakietu za pomocą [ `xpath()` funkcji:](./workflow-definition-language-functions-reference.md#xpath)

1. Przed rozpoczęciem potrzebujesz aplikacji logiki z wyzwalaczem SAP. Jeśli nie masz jeszcze tej aplikacji logiki, wykonaj poprzednie kroki opisane w tym temacie, aby [skonfigurować aplikację logiki z wyzwalaczem SAP](#receive-from-sap).

   Przykład:

   ![Dodawanie wyzwalacza SAP do aplikacji logiki](./media/logic-apps-using-sap-connector/first-step-trigger.png)

1. Pobierz główny obszar nazw z identyfikatora XML IDOC, który aplikacja logiki otrzymuje od SAP. Aby wyodrębnić ten obszar nazw z dokumentu XML, dodaj krok, który tworzy `xpath()` zmienną ciągu lokalnego i przechowuje ten obszar nazw przy użyciu wyrażenia:

   `xpath(xml(triggerBody()?['Content']), 'namespace-uri(/*)')`

   ![Pobierz główny obszar nazw z IDOC](./media/logic-apps-using-sap-connector/get-namespace.png)

1. Aby wyodrębnić indywidualny identyfikator IDOC, dodaj krok, który tworzy zmienną `xpath()` tablicową i przechowuje kolekcję IDOC przy użyciu innego wyrażenia:

   `xpath(xml(triggerBody()?['Content']), '/*[local-name()="Receive"]/*[local-name()="idocData"]')`

   ![Pobierz tablicę elementów](./media/logic-apps-using-sap-connector/get-array.png)

   Zmienna tablicowa udostępnia każdemu IDOC dla aplikacji logiki do przetwarzania indywidualnie przez wyliczenie nad kolekcją. W tym przykładzie aplikacja logiki przenosi każdy IDOC do serwera SFTP przy użyciu pętli:

   ![Wysyłanie IDOC do serwera SFTP](./media/logic-apps-using-sap-connector/loop-batch.png)

   Każdy IDOC musi zawierać główny obszar nazw, co jest powodem, dla którego zawartość pliku jest zawijana wewnątrz elementu wraz z głównym obszarem `<Receive></Receive` nazw przed wysłaniem IDOC do aplikacji podrzędnej lub serwera SFTP w tym przypadku.

Szablon szybkiego startu dla tego wzorca można użyć, wybierając ten szablon w projektancie aplikacji logiki podczas tworzenia nowej aplikacji logiki.

![Wybieranie szablonu aplikacji logiki wsadowego](./media/logic-apps-using-sap-connector/select-batch-logic-app-template.png)

## <a name="generate-schemas-for-artifacts-in-sap"></a>Generowanie schematów artefaktów w systemie SAP

W tym przykładzie użyto aplikacji logiki, którą można wyzwolić za pomocą żądania HTTP. Akcja SAP wysyła żądanie do systemu SAP w celu wygenerowania schematów dla określonych identyfikatorów IDoc i BAPI. Schematy, które zwracają w odpowiedzi są przekazywane do konta integracji przy użyciu łącznika usługi Azure Resource Manager.

### <a name="add-an-http-request-trigger"></a>Dodawanie wyzwalacza żądania HTTP

1. W witrynie Azure portal utwórz pustą aplikację logiki, która otwiera projektanta aplikacji logiki.

1. W polu wyszukiwania `http request` wprowadź jako filtr. Z listy **Wyzwalacze** wybierz pozycję **Po odebraniu żądania HTTP**.

   ![Dodaj wyzwalacz żądania HTTP](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. Teraz zapisz aplikację logiki, dzięki czemu można wygenerować adres URL punktu końcowego dla aplikacji logiki.
Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

   Adres URL punktu końcowego pojawia się teraz w wyzwalaczu, na przykład:

   ![Generowanie adresu URL dla punktu końcowego](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-an-sap-action-to-generate-schemas"></a>Dodawanie akcji SAP w celu wygenerowania schematów

1. W projektancie aplikacji logiki w obszarze wyzwalacz wybierz pozycję **Nowy krok**.

   ![Dodawanie nowego kroku do aplikacji logiki](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. W polu wyszukiwania `sap` wprowadź jako filtr. Z listy **Akcje** wybierz pozycję **Generuj schematy**.
  
   ![Dodaj akcję "Generuj schematy" do aplikacji logiki](media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   Można też wybrać kartę **Przedsiębiorstwo** i wybrać akcję SAP.

   ![Wybierz akcję wysyłania SAP z karty Przedsiębiorstwo](media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

1. Jeśli połączenie już istnieje, przejdź do następnego kroku, aby skonfigurować akcję SAP. Jeśli jednak zostanie wyświetlony monit o podanie szczegółów połączenia, podaj informacje, aby teraz utworzyć połączenie z lokalnym serwerem SAP.

   1. Podaj nazwę połączenia.

   1. W sekcji **Brama danych** w obszarze **Subskrypcja**najpierw wybierz subskrypcję platformy Azure dla zasobu bramy utworzonego w witrynie Azure portal dla instalacji bramy. 
   
   1. W obszarze **Brama połączenia**wybierz zasób bramy.

   1. Kontynuuj dostarczanie informacji o połączeniu. W przypadku właściwości **Typ logowania** wykonaj krok na podstawie tego, czy właściwość jest **ustawiona** na Serwer aplikacji lub **Grupa:**
   
      * W przypadku **serwera aplikacji**wymagane są te właściwości, które zwykle wydają się opcjonalne:

        ![Tworzenie połączenia z serwerem aplikacji SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * W przypadku **grupy**wymagane są te właściwości, które zwykle wydają się opcjonalne:

        ![Tworzenie połączenia z serwerem komunikatów SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      Domyślnie silne wpisywanie jest używane do sprawdzania nieprawidłowych wartości przez wykonanie sprawdzania poprawności XML względem schematu. To zachowanie może pomóc wykryć problemy wcześniej. Opcja **Bezpieczne wpisywanie** jest dostępna dla zgodności z powrotem i sprawdza tylko długość ciągu. Dowiedz się więcej o [opcji Bezpieczne pisanie](#safe-typing).

   1. Po zakończeniu wybierz pozycję **Utwórz**.

      Logic Apps konfiguruje i testuje połączenie, aby upewnić się, że połączenie działa poprawnie.

1. Podaj ścieżkę do artefaktu, dla którego chcesz wygenerować schemat.

   Akcję SAP można wybrać z selektora plików:

   ![Wybierz akcję SAP](media/logic-apps-using-sap-connector/select-SAP-action-schema-generator.png)  

   Można też ręcznie wprowadzić czynność:

   ![Ręczne wprowadzanie akcji SAP](media/logic-apps-using-sap-connector/manual-enter-SAP-action-schema-generator.png)

   Aby wygenerować schematy dla więcej niż jednego artefaktu, podaj szczegóły akcji SAP dla każdego artefaktu, na przykład:

   ![Wybierz pozycję Dodaj nowy element](media/logic-apps-using-sap-connector/schema-generator-array-pick.png)

   ![Pokaż dwa elementy](media/logic-apps-using-sap-connector/schema-generator-example.png)

   Aby uzyskać więcej informacji na temat akcji SAP, zobacz [Schematy komunikatów dla operacji IDOC](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

1. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

### <a name="test-your-logic-app"></a>Testowanie aplikacji logiki

1. Na pasku narzędzi projektanta wybierz pozycję **Uruchom,** aby wyzwolić uruchomienie aplikacji logiki.

1. Otwórz przebieg i sprawdź dane wyjściowe dla akcji **Generowanie schematów.**

   Dane wyjściowe pokazują wygenerowane schematy dla określonej listy komunikatów.

### <a name="upload-schemas-to-an-integration-account"></a>Przekazywanie schematów na konto integracji

Opcjonalnie można pobrać lub przechowywać wygenerowane schematy w repozytoriach, takich jak obiekt blob, magazyn lub konto integracji. Konta integracji zapewniają pierwszorzędne środowisko z innymi akcjami XML, więc w tym przykładzie pokazano, jak przekazać schematy do konta integracji dla tej samej aplikacji logiki przy użyciu łącznika usługi Azure Resource Manager.

1. W projektancie aplikacji logiki w obszarze wyzwalacz wybierz pozycję **Nowy krok**.

1. W polu wyszukiwania `Resource Manager` wprowadź jako filtr. Wybierz **pozycję Utwórz lub zaktualizuj zasób**.

   ![Wybierz akcję usługi Azure Resource Manager](media/logic-apps-using-sap-connector/select-azure-resource-manager-action.png)

1. Wprowadź szczegóły akcji, w tym subskrypcję platformy Azure, grupę zasobów platformy Azure i konto integracji. Aby dodać tokeny SAP do pól, kliknij wewnątrz pól dla tych pól i wybierz z wyświetlona dynamiczna lista zawartości.

   1. Otwórz listę **Dodaj nowy parametr** i wybierz pola **Lokalizacja** i **Właściwości.**

   1. Podaj szczegółowe informacje dotyczące tych nowych pól, jak pokazano w tym przykładzie.

      ![Wprowadzanie szczegółów akcji usługi Azure Resource Manager](media/logic-apps-using-sap-connector/azure-resource-manager-action.png)

   Akcja **SCHEMATÓW GENERUJĄC** SAP generuje schematy jako kolekcję, więc projektant automatycznie dodaje **For each** pętli do akcji. Oto przykład, który pokazuje, jak ta akcja jest wyświetlana:

   ![Akcja usługi Azure Resource Manager z pętlą "dla każdej"](media/logic-apps-using-sap-connector/azure-resource-manager-action-foreach.png)

   > [!NOTE]
   > Schematy używają formatu zakodowanego base64. Aby przekazać schematy do konta integracji, muszą być `base64ToString()` dekodowane przy użyciu funkcji. Oto przykład, który pokazuje kod `"properties"` dla elementu:
   >
   > ```json
   > "properties": {
   >    "Content": "@base64ToString(items('For_each')?['Content'])",
   >    "ContentType": "application/xml",
   >    "SchemaType": "Xml"
   > }
   > ```

1. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

### <a name="test-your-logic-app"></a>Testowanie aplikacji logiki

1. Na pasku narzędzi projektanta wybierz pozycję **Uruchom,** aby ręcznie wyzwolić aplikację logiki.

1. Po pomyślnym uruchomieniu przejdź do konta integracji i sprawdź, czy istnieją wygenerowane schematy.

## <a name="enable-secure-network-communications"></a>Włącz bezpieczną komunikację sieciową

Przed rozpoczęciem upewnij się, że spełniasz wcześniej wymienione [wymagania wstępne:](#pre-reqs)

* Lokalna brama danych jest zainstalowana na komputerze, który znajduje się w tej samej sieci co system SAP.
* W przypadku aplikacji SSO brama jest uruchomiona jako użytkownik, który jest mapowany na użytkownika SAP.
* Biblioteka SNC, która udostępnia dodatkowe funkcje zabezpieczeń, jest zainstalowana na tym samym komputerze co brama danych. Niektóre przykłady obejmują [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), Kerberos i NTLM.

   Aby włączyć funkcję SNC dla żądań do lub z systemu SAP, zaznacz pole wyboru **Użyj SNC** w połączeniu SAP i podaj następujące właściwości:

   ![Konfigurowanie sap SNC w połączeniu](media/logic-apps-using-sap-connector/configure-sapsnc.png)

   | Właściwość | Opis |
   |----------| ------------|
   | **Ścieżka biblioteki SNC** | Nazwa biblioteki SNC lub ścieżka względem lokalizacji instalacji NCo lub ścieżki bezwzględnej. Przykładami `sapsnc.dll` są `.\security\sapsnc.dll` `c:\security\sapsnc.dll`lub . |
   | **SNC SSO** | Podczas łączenia za pośrednictwem snc, tożsamość SNC jest zwykle używany do uwierzytelniania wywołującego. Inną opcją jest zastąpienie, aby informacje o użytkowniku i haśle mogły być używane do uwierzytelniania obiektu wywołującego, ale wiersz jest nadal szyfrowany. |
   | **SNC Moje imię** | W większości przypadków można pominąć tę właściwość. Zainstalowane rozwiązanie SNC zwykle zna własną nazwę SNC. Tylko dla rozwiązań, które obsługują wiele tożsamości, może być konieczne określenie tożsamości, która ma być używana dla tego określonego miejsca docelowego lub serwera. |
   | **Nazwa partnera SNC** | Nazwa zaplecza SNC. |
   | **Jakość ochrony SNC** | Jakość usług, które mają być wykorzystywane do komunikacji SNC tego konkretnego miejsca docelowego lub serwera. Wartość domyślna jest definiowana przez system zaplecza. Maksymalna wartość jest definiowana przez produkt zabezpieczający używany dla SNC. |
   |||

   > [!NOTE]
   > Nie ustawiaj zmiennych środowiskowych SNC_LIB i SNC_LIB_64 na komputerze, na którym znajduje się brama danych i biblioteka SNC. Jeśli jest ustawiona, mają pierwszeństwo przed wartością biblioteki SNC przekazywanych przez łącznik.

<a name="safe-typing"></a>

## <a name="safe-typing"></a>Bezpieczne pisanie

Domyślnie podczas tworzenia połączenia SAP silne wpisywanie jest używane do sprawdzania nieprawidłowych wartości przez wykonanie sprawdzania poprawności XML względem schematu. To zachowanie może pomóc wykryć problemy wcześniej. Opcja **Bezpieczne wpisywanie** jest dostępna dla zgodności z powrotem i sprawdza tylko długość ciągu. Jeśli wybierzesz **bezpieczne wpisywanie,** typ DATS i typ TIMS w SAP są traktowane `xs:date` `xs:time`jako `xmlns:xs="http://www.w3.org/2001/XMLSchema"`ciągi, a nie jako ich odpowiedniki XML, i , gdzie . Bezpieczne wpisywanie wpływa na zachowanie dla wszystkich generowania schematu, wiadomość wysyłania zarówno dla ładunku "zostały wysłane" i "odebrano" odpowiedź i wyzwalacz. 

Gdy używane jest silne wpisywanie **(bezpieczne wpisywanie** nie jest włączone), schemat mapuje typy DATS i TIMS na bardziej proste typy XML:

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true" type="xs:date"/>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true" type="xs:time"/>
```

Podczas wysyłania wiadomości przy użyciu silnego pisania odpowiedź DATS i TIMS jest zgodna z pasującym formatem typu XML:

```xml
<DATE>9999-12-31</DATE>
<TIME>23:59:59</TIME>
```

Gdy opcja **Bezpieczne pisanie** jest włączona, schemat mapuje typy DATS i TIMS na pola ciągu XML tylko z ograniczeniami długości, na przykład:

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="8" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="6" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
```

Gdy wiadomości są wysyłane z włączoną funkcją **bezpiecznego pisania,** odpowiedź DATS i TIMS wygląda następująco:

```xml
<DATE>99991231</DATE>
<TIME>235959</TIME>
```

## <a name="advanced-scenarios"></a>Scenariusze zaawansowane

### <a name="confirm-transaction-explicitly"></a>Jawnie potwierdzaj transakcję

Podczas wysyłania transakcji do sap z aplikacji logiki ta wymiana odbywa się w dwóch krokach, jak opisano w dokumencie SAP, [Programy serwera RFC transakcyjnych](https://help.sap.com/doc/saphelp_nwpi71/7.1/en-US/22/042ad7488911d189490000e829fbbd/content.htm?no_cache=true). Domyślnie akcja **Wyślij do SAP** obsługuje zarówno kroki dla transferu funkcji, jak i potwierdzenia transakcji w jednym wywołaniu. Łącznik SAP umożliwia oddzielenie tych kroków. Można wysłać IDOC i zamiast automatycznie potwierdzić transakcję, można użyć jawnej akcji **Potwierdzanie identyfikatora transakcji.**

Ta możliwość oddzielenia potwierdzenia identyfikatora transakcji jest przydatna, gdy nie chcesz duplikować transakcji w sap, na przykład w scenariuszach, w których mogą wystąpić błędy z przyczyn, takich jak problemy z siecią. Po potwierdzeniu identyfikatora transakcji oddzielnie, transakcja jest zakończona tylko jeden raz w systemie SAP.

Oto przykład, który pokazuje ten wzorzec:

1. Utwórz pustą aplikację logiki i dodaj wyzwalacz HTTP.

1. W łączniku SAP dodaj akcję **Wyślij IDOC.** Podaj szczegółowe informacje dotyczące iDOC wysyłanego do systemu SAP.

1. Aby jawnie potwierdzić identyfikator transakcji w osobnym kroku, w polu **Potwierdź identyfikator TID** wybierz pozycję **Nie**. W przypadku opcjonalnego pola **identyfikatora transakcji** identyfikator GUID można ręcznie określić wartość lub automatycznie wygenerować i zwrócić ten identyfikator GUID w odpowiedzi z akcji Wyślij IDOC.

   ![Właściwości akcji Wyślij IDOC](./media/logic-apps-using-sap-connector/send-idoc-action-details.png)

1. Aby jawnie potwierdzić identyfikator transakcji, dodaj akcję **Potwierdź identyfikator transakcji.** Kliknij wewnątrz pola **Identyfikator transakcji,** aby wyświetlić dynamiczną listę zawartości. Z tej listy wybierz wartość **identyfikatora transakcji,** która jest zwracana z akcji **Wyślij IDOC.**

   ![Potwierdź akcję identyfikatora transakcji](./media/logic-apps-using-sap-connector/explicit-transaction-id.png)

   Po uruchomieniu tego kroku bieżąca transakcja jest oznaczona jako ukończona na obu końcach, po stronie łącznika SAP i po stronie systemu SAP.

## <a name="known-issues-and-limitations"></a>Znane problemy i ograniczenia

Oto obecnie znane problemy i ograniczenia dotyczące łącznika SAP:

* Wyzwalacz SAP nie obsługuje klastrów bramy danych. W niektórych przypadkach pracy awaryjnej węzeł bramy danych, który komunikuje się z systemem SAP może różnić się od aktywnego węzła, co powoduje nieoczekiwane zachowanie. W przypadku scenariuszy wysyłania klastry bramy danych są obsługiwane.

* Łącznik SAP obecnie nie obsługuje ciągów routera SAP. Lokalna brama danych musi istnieć w tej samej sieci LAN co system SAP, który chcesz połączyć.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać więcej informacji technicznych na temat tego łącznika, takich jak wyzwalacze, akcje i limity opisane w pliku Swagger łącznika, zobacz [stronę odwołania łącznika](https://docs.microsoft.com/connectors/sap/).

> [!NOTE]
> W przypadku aplikacji logiki w [środowisku usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)wersja z etykietą ISE tego łącznika używa [limitów komunikatów ŚRODOWISKA ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Następne kroki

* [Połącz się](../logic-apps/logic-apps-gateway-connection.md) z systemami lokalnymi z usługi Azure Logic Apps.
* Dowiedz się, jak sprawdzać poprawność, przekształcać i używać innych operacji związanych z wiadomościami za pomocą [pakietu Enterprise Integration Pack.](../logic-apps/logic-apps-enterprise-integration-overview.md)
* Dowiedz się więcej o innych [łącznikach aplikacji logiki](../connectors/apis-list.md).
