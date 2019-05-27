---
title: Odzyskiwanie po awarii dla konta integracji B2B — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Przygotowywanie do odzyskiwania po awarii między regionami w usłudze Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.date: 04/10/2017
ms.openlocfilehash: ac29ef7f0599cc41924ba1a5a00e46b0292e7e9b
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65967747"
---
# <a name="cross-region-disaster-recovery-for-b2b-integration-accounts-in-azure-logic-apps"></a>Odzyskiwanie po awarii między regionami dla konta integracji B2B w usłudze Azure Logic Apps

B2B obciążenia uwzględniają Transakcje pieniężne, takich jak zamówień i faktur. Podczas zdarzenia awarii, jest krytyczny dla działalności Szybkie odzyskiwanie, aby spełnić wymagania umów SLA biznesowej uzgodnione z partnerami. W tym artykule przedstawiono sposób tworzenia planu ciągłości biznesowej dla obciążeń B2B. 

* Gotowość do odzyskiwania po awarii 
* Failover do regionu pomocniczego podczas zdarzenia awarii 
* Rezerwowe po wystąpieniu zdarzenia po awarii do regionu podstawowego

## <a name="disaster-recovery-readiness"></a>Gotowość do odzyskiwania po awarii  

1. Określenie regionu pomocniczego i utworzenie [konta integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) w regionie pomocniczym.

2. Dodawanie partnerów, schematów i umów dotyczących przepływów komunikat wymagana, gdy stan uruchomienia musi być replikowane do regionu pomocniczego konta integracji.

   > [!TIP]
   > Upewnij się, że istnieje spójność w konwencji nazewnictwa artefaktów konta integracji między regionami. 

3. Aby ściągnąć stan uruchomienia z regionu podstawowego, należy utworzyć aplikację logiki w regionie pomocniczym. 

   Ta aplikacja logiki powinna mieć *wyzwalacza* i *akcji*. 
   Wyzwalacz powinien łączą się z konta integracji z regionu podstawowego, i akcji należy do regionu pomocniczego konta integracji. 
   Oparte na przedział czasu, wyzwalacz sonduje regionu podstawowego, uruchom tabela stanu i pobiera nowe rekordy, jeśli istnieje. Akcja aktualizuje je do regionu pomocniczego konta integracji. 
   Pozwala to uzyskać stan czasu wykonywania przyrostowych z regionu podstawowego do regionu pomocniczego.

4. Ciągłość biznesową na koncie integracji aplikacji logiki jest przeznaczony do obsługi oparte na protokołach B2B - X12, AS2 i EDIFACT. Aby uzyskać szczegółowy opis kroków, wybierz odpowiednie linki.

5. Zalecane jest zbyt wdrażania wszystkich zasobów w regionie podstawowym w regionie pomocniczym. 

   Region podstawowy obejmują usługi Azure SQL Database lub Azure Cosmos DB, Azure Service Bus i usługi Azure Event Hubs, używane do obsługi komunikatów usługi Azure API Management i funkcji usługi Azure Logic Apps w usłudze Azure App Service.   

6. Nawiąż połączenie z regionu podstawowego do regionu pomocniczego. Aby ściągnąć stan uruchomienia z regionu podstawowego, należy utworzyć aplikację logiki w regionie pomocniczym. 

   Aplikacja logiki powinien mieć wyzwalaczy i akcji. 
   Wyzwalacz powinien łączenie z kontem integracji regionu podstawowego. 
   Akcja powinna łączenie z kontem integracji w regionie pomocniczym. 
   Oparte na przedział czasu, wyzwalacz sonduje regionu podstawowego, uruchom tabela stanu i pobiera nowe rekordy, jeśli istnieje. 
   Akcja aktualizuje je do regionu pomocniczego konta integracji. 
   Ten proces pozwala uzyskać stan czasu wykonywania przyrostowych z regionu podstawowego do regionu pomocniczego.

Ciągłość biznesową na koncie integracji Logic Apps obsługuje na podstawie protokoły B2B X12, AS2 i EDIFACT. Aby uzyskać szczegółowe instrukcje na temat korzystania z X12 i AS2, zobacz [X12](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#x12) i [AS2](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#as2) w tym artykule.

## <a name="fail-over-to-a-secondary-region-during-a-disaster-event"></a>Failover do regionu pomocniczego podczas zdarzenia awarii

Podczas zdarzenia awarii, gdy region podstawowy jest niedostępny dla ciągłości działania, bezpośrednie kierowanie ruchu do regionu pomocniczego. Pomaga w regionie pomocniczym, a firm, aby odzyskać funkcje szybko, aby spełnić cel punktu odzyskiwania i/lub RTO uzgodnione przez ich partnerów. Zmniejsza on również działań mających na celu przełączyć się z jednego regionu do innego regionu. 

Brak oczekiwane opóźnienie, jednocześnie kopiując numerów kontrolnych z regionu podstawowego do regionu pomocniczego. Aby uniknąć wysyła numerów kontrolnych wygenerowanego zduplikowane partnerów podczas zdarzenia awarii, zaleca się zwiększenie numery kontrolne w ramach umów w regionie pomocniczym za pomocą [poleceń cmdlet programu PowerShell](https://docs.microsoft.com/powershell/module/azurerm.logicapp/set-azurermintegrationaccountgeneratedicn?view=azurermps-6.13.0).

## <a name="fall-back-to-a-primary-region-post-disaster-event"></a>Wracać do regionu podstawowego zdarzenia po awarii

Aby wrócić do regionu podstawowego gdy stanie się dostępna, wykonaj następujące kroki:

1. Zatrzymaj akceptowanie komunikatów oferowanych przez partnerów w regionie pomocniczym.  

2. Zwiększony wygenerowanego formantu dla wszystkich umów regionu podstawowego, za pomocą [poleceń cmdlet programu PowerShell](https://docs.microsoft.com/powershell/module/azurerm.logicapp/set-azurermintegrationaccountgeneratedicn?view=azurermps-6.13.0).  

3. Bezpośredni ruch z regionu pomocniczego do regionu podstawowego.

4. Sprawdź, czy włączono aplikację logiki, utworzone w regionie pomocniczym dla ściąganie stan uruchomienia z regionu podstawowego.

## <a name="x12"></a>X12 

Ciągłość działania w EDI X 12 dokumentów opiera się na numery kontrolne:

> [!TIP]
> Można również użyć [X12 szybki start szablonu](https://azure.microsoft.com/resources/templates/201-logic-app-b2b-disaster-recovery-replication/) do tworzenia aplikacji logiki. Tworzenie kont integracji podstawowe i pomocnicze są wymagania wstępne dotyczące korzystania z szablonu. Szablon ułatwia tworzenie dwóch logic apps, z których jeden dla numerów kontrolnych odebranych i drugi dla numerów kontrolnych wygenerowany. Odpowiednich wyzwalacze i akcje są tworzone w usłudze logic apps wyzwalacz łączenia się z konta integracji podstawowego i akcji do konta integracyjnego dodatkowej.

**Wymagania wstępne**

Aby włączyć odzyskiwanie po awarii dla wiadomości przychodzących, wybierz ustawienia sprawdzania duplikatów X12 ustawieniach odbierania umowy.

![Wybierz ustawienia sprawdzania duplikatów](./media/logic-apps-enterprise-integration-b2b-business-continuity/dupcheck.png)  

1. Tworzenie [aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md) w regionie pomocniczym.    

2. Wyszukiwanie **X12**i wybierz **X12 — po zmodyfikowaniu numeru kontrolnego**.   

   ![Wyszukaj X12](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn1.png)

   Wyzwalacz jest wyświetlany monit o nawiązać połączenie z kontem integracji. 
   Wyzwalacz powinien być połączony z kontem integracji regionu podstawowego.

3. Wpisz nazwę połączenia, wybierz swoje *konta integracji w regionie podstawowym* z listy i wybierz polecenie **Utwórz**.   

   ![Nazwa konta integracyjnego regionu podstawowego](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn2.png)

4. **Data i godzina rozpoczęcia synchronizacji numeru** ustawienie jest opcjonalne. **Częstotliwość** można ustawić **dzień**, **godzinę**, **minutę**, lub **drugi** w odstępie czasu.   

   ![Daty/godziny i częstotliwość](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

5. Wybierz **nowy krok** > **Dodaj akcję**.

   ![Nowy krok, Dodaj Action](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

6. Wyszukiwanie **X12**i wybierz **X12 — Dodawanie lub aktualizowanie numerów kontrolnych**.   

   ![Dodaj lub zaktualizuj numery kontrolne](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn5.png)

7. Aby połączyć konto integracji w regionie pomocniczym akcję, wybierz **Zmień połączenie** > **Dodaj nowe połączenie** listę kont integracji dostępne. Wpisz nazwę połączenia, wybierz swoje *konta integracji w regionie pomocniczym* z listy i wybierz polecenie **Utwórz**. 

   ![Nazwa konta integracji w regionie pomocniczym](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

8. Przełącz się do nieprzetworzone dane wejściowe, klikając ikonę w prawym górnym rogu.

   ![Przełącz się do nieprzetworzone dane wejściowe](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12rawinputs.png)

9. Wybieranie treści z selektora zawartości dynamicznej, a następnie zapisz aplikację logiki.

   ![Pola zawartości dynamicznej](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn7.png)

   Oparte na przedział czasu, wyzwalacz sonduje regionu podstawowego, odebrano kontroli Numerowanie tabeli i pobiera nowe rekordy. 
   Akcja aktualizuje rekordy w ramach konta integracji w regionie pomocniczym. 
   Jeśli są dostępne nie aktualizacje, stan wyzwalacza jest wyświetlany jako **pomijane**.   

   ![Tabela liczba kontroli](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

Oparte na przedział czasu, stan czasu wykonywania przyrostowych są replikowane z regionu podstawowego do regionu pomocniczego. Podczas zdarzenia awarii, gdy region podstawowy nie jest dostępna, bezpośredni ruch do regionu pomocniczego dla ciągłości działania. 

## <a name="edifact"></a>EDIFACT 

Ciągłość działania dla dokumentów EDIFACT integracji EDI opiera się na numery kontrolne.

**Wymagania wstępne**

Aby włączyć odzyskiwanie po awarii dla wiadomości przychodzących, wybierz ustawienia sprawdzania duplikatów w ustawieniach odbierania umowy EDIFACT.

![Wybierz ustawienia sprawdzania duplikatów](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactdupcheck.png)  

1. Tworzenie [aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md) w regionie pomocniczym.    

2. Wyszukiwanie **EDIFACT**i wybierz **EDIFACT — po zmodyfikowaniu numeru kontrolnego**.

   ![Wyszukaj EDIFACT](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactcn1.png)

   Wyzwalacz jest wyświetlany monit o nawiązać połączenie z kontem integracji. 
   Wyzwalacz powinien być połączony z kontem integracji regionu podstawowego. 

3. Wpisz nazwę połączenia, wybierz swoje *konta integracji w regionie podstawowym* z listy i wybierz polecenie **Utwórz**.    

   ![Nazwa konta integracyjnego regionu podstawowego](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN2.png)

4. **Data i godzina rozpoczęcia synchronizacji numeru** ustawienie jest opcjonalne. **Częstotliwość** można ustawić **dzień**, **godzinę**, **minutę**, lub **drugi** w odstępie czasu.    

   ![Daty/godziny i częstotliwość](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

6. Wybierz **nowy krok** > **Dodaj akcję**.    

   ![Nowy krok, Dodaj Action](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

7. Wyszukiwanie **EDIFACT**i wybierz **EDIFACT — Dodawanie lub aktualizowanie numerów kontrolnych**.   

   ![Dodaj lub zaktualizuj numery kontrolne](./media/logic-apps-enterprise-integration-b2b-business-continuity/EdifactChooseAction.png)

8. Aby połączyć konto integracji w regionie pomocniczym akcję, wybierz **Zmień połączenie** > **Dodaj nowe połączenie** listę kont integracji dostępne. Wpisz nazwę połączenia, wybierz swoje *konta integracji w regionie pomocniczym* z listy i wybierz polecenie **Utwórz**.

   ![Nazwa konta integracji w regionie pomocniczym](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

9. Przełącz się do nieprzetworzone dane wejściowe, klikając ikonę w prawym górnym rogu.

   ![Przełącz się do nieprzetworzone dane wejściowe](./media/logic-apps-enterprise-integration-b2b-business-continuity/Edifactrawinputs.png)

10. Wybieranie treści z selektora zawartości dynamicznej, a następnie zapisz aplikację logiki.   

   ![Pola zawartości dynamicznej](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN7.png)

   Oparte na przedział czasu, wyzwalacz sonduje regionu podstawowego, odebrano kontroli Numerowanie tabeli i pobiera nowe rekordy.
   Akcja aktualizacji rekordów do regionu pomocniczego konta integracji. 
   Jeśli są dostępne nie aktualizacje, stan wyzwalacza jest wyświetlany jako **pomijane**.

   ![Tabela liczba kontroli](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

Oparte na przedział czasu, stan czasu wykonywania przyrostowych są replikowane z regionu podstawowego do regionu pomocniczego. Podczas zdarzenia awarii, gdy region podstawowy nie jest dostępna, bezpośredni ruch do regionu pomocniczego dla ciągłości działania. 

## <a name="as2"></a>AS2 

Ciągłość działania w dokumentach, które używają protokołu AS2 opiera się na identyfikator komunikatu i wartości MIC.

> [!TIP]
> Można również użyć [szablonu szybkiego startu AS2](https://github.com/Azure/azure-quickstart-templates/pull/3302) do tworzenia aplikacji logiki. Tworzenie kont integracji podstawowe i pomocnicze są wymagania wstępne dotyczące korzystania z szablonu. Ten szablon pomaga utworzyć aplikację logiki, która zawiera wyzwalacz i akcja. Aplikacja logiki tworzy połączenie przy użyciu wyzwalacza do głównej łączniki i akcji do konta integracji dodatkowej.

1. Tworzenie [aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md) w regionie pomocniczym.  

2. Wyszukiwanie **AS2**i wybierz **utworzeniu AS2 - wartości podczas MIC**.   

   ![Wyszukaj AS2](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid1.png)

   Wyzwalacz jest wyświetlany monit o nawiązać połączenie z kontem integracji. 
   Wyzwalacz powinien być połączony z kontem integracji regionu podstawowego. 
   
3. Wpisz nazwę połączenia, wybierz swoje *konta integracji w regionie podstawowym* z listy i wybierz polecenie **Utwórz**.

   ![Nazwa konta integracyjnego regionu podstawowego](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid2.png)

4. **Data i godzina rozpoczęcia synchronizowania wartości wartości MIC** ustawienie jest opcjonalne. **Częstotliwość** można ustawić **dzień**, **godzinę**, **minutę**, lub **drugi** w odstępie czasu.   

   ![Daty/godziny i częstotliwość](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid3.png)

5. Wybierz **nowy krok** > **Dodaj akcję**.  

   ![Nowy krok, Dodaj Action](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid4.png)

6. Wyszukiwanie **AS2**i wybierz **AS2 - Dodaj lub zaktualizuj zawartość kontroli integralności uprawnień**.  

   ![MIC dodawania lub aktualizacji](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid5.png)

7. Aby łączenie z kontem integracji dodatkowych akcji, wybierz pozycję **Zmień połączenie** > **Dodaj nowe połączenie** listę kont integracji dostępne. Wpisz nazwę połączenia, wybierz swoje *konta integracji w regionie pomocniczym* z listy i wybierz polecenie **Utwórz**.

   ![Nazwa konta integracji w regionie pomocniczym](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid6.png)

8. Przełącz się do nieprzetworzone dane wejściowe, klikając ikonę w prawym górnym rogu.

   ![Przełącz się do nieprzetworzone dane wejściowe](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2rawinputs.png)

9. Wybieranie treści z selektora zawartości dynamicznej, a następnie zapisz aplikację logiki.   

   ![Zawartość dynamiczna](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid7.png)

   Oparte na przedział czasu, wyzwalacz sonduje tabeli region podstawowy i pobiera nowe rekordy. Akcja aktualizuje je do regionu pomocniczego konta integracji. 
   Jeśli są dostępne nie aktualizacje, stan wyzwalacza jest wyświetlany jako **pomijane**.  

   ![Tabela region podstawowy](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid8.png)

Oparte na przedział czasu, stan czasu wykonywania przyrostowe są replikowane z regionu podstawowego do regionu pomocniczego. Podczas zdarzenia awarii, gdy region podstawowy nie jest dostępna, bezpośredni ruch do regionu pomocniczego dla ciągłości działania. 

## <a name="next-steps"></a>Kolejne kroki

[Monitorowanie komunikatów B2B](logic-apps-monitor-b2b-message.md)

