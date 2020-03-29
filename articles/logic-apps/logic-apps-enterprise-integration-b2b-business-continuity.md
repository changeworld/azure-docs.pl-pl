---
title: Odzyskiwanie po awarii dla kont integracyjnych
description: Konfigurowanie kont integracji i artefaktów B2B za pomocą odzyskiwania po awarii między regionami w usłudze Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/10/2017
ms.openlocfilehash: 09b77862ad3379efeb8b3063a9d6c60b062ca2d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905127"
---
# <a name="set-up-cross-region-disaster-recovery-for-integration-accounts-in-azure-logic-apps"></a>Konfigurowanie odzyskiwania po awarii między regionami dla kont integracji w usłudze Azure Logic Apps

Obciążenia B2B obejmują transakcje pieniężne, takie jak zamówienia i faktury. Podczas katastrofy ważne jest, aby firma szybko odzyskała siły, aby spełnić umowy SLA na poziomie biznesowym uzgodnione ze swoimi partnerami. W tym artykule pokazano, jak utworzyć plan ciągłości działania dla obciążeń B2B. 

* Gotowość do odzyskiwania po awarii 
* Przejmuje się w przewodzą do regionu pomocniczego podczas wystąpienia awarii 
* Powrót do regionu podstawowego po zdarzeniu związanym z klęską żywiołową

## <a name="disaster-recovery-readiness"></a>Gotowość do odzyskiwania po awarii  

1. Zidentyfikuj region pomocniczy i utwórz [konto integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) w regionie pomocniczym.

2. Dodaj partnerów, schematy i umowy dla wymaganych przepływów komunikatów, w których stan uruchomienia musi zostać zreplikowany na konto integracji regionu pomocniczego.

   > [!TIP]
   > Upewnij się, że istnieje spójność w konwencji nazewnictwa artefaktu konta integracji w różnych regionach. 

3. Aby wyciągnąć stan uruchomienia z regionu podstawowego, utwórz aplikację logiki w regionie pomocniczym. 

   Ta aplikacja logiki powinna mieć *wyzwalacz* i *akcję*. 
   Wyzwalacz powinien łączyć się z kontem integracji regionu podstawowego, a akcja powinna łączyć się z kontem integracji regionu pomocniczego. 
   Na podstawie przedziału czasu wyzwalacz sonduje tabelę stanu uruchamiania regionu podstawowego i pobiera nowe rekordy, jeśli takie istnieją. Akcja aktualizuje je do konta integracji regionu pomocniczego. 
   Pomaga to uzyskać przyrostowy stan środowiska uruchomieniowego z regionu podstawowego do regionu pomocniczego.

4. Ciągłość działania na koncie integracji aplikacji logiki jest przeznaczony do obsługi na podstawie protokołów B2B - X12, AS2 i EDIFACT. Aby znaleźć szczegółowe kroki, wybierz odpowiednie łącza.

5. Zalecenie jest, aby wdrożyć wszystkie zasoby regionu podstawowego w regionie pomocniczym zbyt. 

   Zasoby regionu podstawowego obejmują usługę Azure SQL Database lub usługę Azure Cosmos DB, usługa Azure Service Bus i usługi Azure Event Hubs używane do obsługi wiadomości, usługi Azure API Management i funkcji Usługi Azure Logic Apps w usłudze Azure App Service.   

6. Ustanawianie połączenia z regionu podstawowego do regionu pomocniczego. Aby wyciągnąć stan uruchomienia z regionu podstawowego, utwórz aplikację logiki w regionie pomocniczym. 

   Aplikacja logiki powinna mieć wyzwalacz i akcję. 
   Wyzwalacz powinien łączyć się z kontem integracji regionu podstawowego. 
   Akcja powinna łączyć się z kontem integracji regionu pomocniczego. 
   Na podstawie przedziału czasu wyzwalacz sonduje tabelę stanu uruchamiania regionu podstawowego i pobiera nowe rekordy, jeśli takie istnieją. 
   Akcja aktualizuje je do dodatkowego konta integracji regionu. 
   Ten proces pomaga uzyskać przyrostowy stan środowiska uruchomieniowego z regionu podstawowego do regionu pomocniczego.

Ciągłość działania na koncie integracji aplikacji logiki zapewnia obsługę opartą na protokołach B2B X12, AS2 i EDIFACT. Aby uzyskać szczegółowe kroki dotyczące korzystania z X12 i AS2, zobacz [X12](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#x12) i [AS2](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#as2) w tym artykule.

## <a name="fail-over-to-a-secondary-region-during-a-disaster-event"></a>Przeładuj awaryjnie do regionu pomocniczego podczas zdarzenia katastrofy

Podczas wystąpienia awarii, gdy region podstawowy nie jest dostępny dla ciągłości biznesowej, ruch bezpośredni do regionu pomocniczego. Region pomocniczy pomaga przedsiębiorstwu szybko odzyskać funkcje, aby osiągnąć rpo/rto uzgodnione przez ich partnerów. Minimalizuje również wysiłki na rzecz awaryjnego działania z jednego regionu do drugiego regionu. 

Istnieje oczekiwane opóźnienie podczas kopiowania numerów kontrolnych z regionu podstawowego do regionu pomocniczego. Aby uniknąć wysyłania zduplikowanych wygenerowanych numerów kontrolnych do partnerów podczas wystąpienia awarii, zaleca się zwiększenie liczby formantów w umowach regionu pomocniczego przy użyciu [poleceń cmdlet programu PowerShell](https://docs.microsoft.com/powershell/module/azurerm.logicapp/set-azurermintegrationaccountgeneratedicn?view=azurermps-6.13.0).

## <a name="fall-back-to-a-primary-region-post-disaster-event"></a>Powrót do regionu podstawowego po katastrofie

Aby wrócić do regionu podstawowego, gdy jest on dostępny, wykonaj następujące kroki:

1. Przestań akceptować wiadomości od partnerów w regionie pomocniczym.  

2. Przyrost wygenerowane numery kontrolne dla wszystkich umów regionu podstawowego przy użyciu [poleceń cmdlet programu PowerShell](https://docs.microsoft.com/powershell/module/azurerm.logicapp/set-azurermintegrationaccountgeneratedicn?view=azurermps-6.13.0).  

3. Ruch bezpośredni z regionu pomocniczego do regionu podstawowego.

4. Sprawdź, czy aplikacja logiki utworzona w regionie pomocniczym do ściągania stanu uruchomienia z regionu podstawowego jest włączona.

## <a name="x12"></a>X12 

Ciągłość działania dokumentów EDI X12 opiera się na numerach kontrolnych:

> [!TIP]
> Za pomocą [szablonu szybkiego startu X12](https://azure.microsoft.com/resources/templates/201-logic-app-b2b-disaster-recovery-replication/) można również tworzyć aplikacje logiki. Tworzenie kont integracji podstawowej i pomocniczej są wymaganiami wstępnymi do użycia szablonu. Szablon pomaga utworzyć dwie aplikacje logiki, jedną dla odebranych numerów kontrolnych, a drugą dla wygenerowanych numerów kontrolnych. Odpowiednie wyzwalacze i akcje są tworzone w aplikacjach logiki, łącząc wyzwalacz z kontem integracji podstawowej i akcji do konta integracji pomocniczej.

**Wymagania wstępne**

Aby włączyć odzyskiwanie po awarii dla wiadomości przychodzących, wybierz zduplikowane ustawienia sprawdzania w ustawieniach odbierania umowy X12.

![Wybieranie ustawień sprawdzania duplikatów](./media/logic-apps-enterprise-integration-b2b-business-continuity/dupcheck.png)  

1. Tworzenie [aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md) w regionie pomocniczym.    

2. Wyszukaj na **X12**i wybierz **X12 - Po zmodyfikowaniu numeru kontrolnego**.   

   ![Szukaj X12](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn1.png)

   Wyzwalacz monituje o nawiązanie połączenia z kontem integracji. 
   Wyzwalacz powinien być połączony z kontem integracji regionu podstawowego.

3. Wprowadź nazwę połączenia, wybierz z listy *podstawowe konto integracji regionu* i wybierz pozycję **Utwórz**.   

   ![Nazwa konta integracji regionu podstawowego](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn2.png)

4. Ustawienie **synchronizacji numeru formantu DateTime to start** jest opcjonalne. **Częstotliwość** można ustawić na **Dzień,** **Godzina,** **Minuta**lub **Sekunda** z interwałem.   

   ![DateTime i częstotliwość](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

5. Wybierz **pozycję Nowy krok** > **Dodaj akcję**.

   ![Nowy krok, Dodaj akcję](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

6. Wyszukaj na **X12**i wybierz **X12 - Dodaj lub zaktualizuj numery kontrolne**.   

   ![Dodawanie lub aktualizowanie numerów kontrolnych](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn5.png)

7. Aby połączyć akcję z kontem integracji regionu pomocniczego, wybierz pozycję **Zmień połączenie** > **Dodaj nowe połączenie** dla listy dostępnych kont integracji. Wprowadź nazwę połączenia, wybierz konto *integracji regionu pomocniczego* z listy i wybierz pozycję **Utwórz**. 

   ![Nazwa konta integracji regionu pomocniczego](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

8. Przełącz się na nieprzetworzone wejścia, klikając ikonę w prawym górnym rogu.

   ![Przełączanie na nieprzetworzone wejścia](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12rawinputs.png)

9. Wybierz body z selektora zawartości dynamicznej i zapisz aplikację logiki.

   ![Dynamiczne pola zawartości](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn7.png)

   Na podstawie przedziału czasu wyzwalacz sonduje tabelę numerów kontrolnych regionu podstawowego i pobiera nowe rekordy. 
   Akcja aktualizuje rekordy na koncie integracji regionu pomocniczego. 
   Jeśli nie ma żadnych aktualizacji, stan wyzwalacza jest wyświetlany jako **Pominięty**.   

   ![Tabela numerów kontrolnych](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

Na podstawie interwału czasu przyrostowy stan środowiska uruchomieniowego jest replikowane z regionu podstawowego do regionu pomocniczego. Podczas wystąpienia awarii, gdy region podstawowy nie jest dostępny, przekieruj ruch do regionu pomocniczego w celu zapewnienia ciągłości biznesowej. 

## <a name="edifact"></a>EDIFACT 

Ciągłość działania dokumentów EDI EDIFACT opiera się na numerach kontrolnych.

**Wymagania wstępne**

Aby włączyć odzyskiwanie po awarii dla wiadomości przychodzących, wybierz zduplikowane ustawienia sprawdzania w ustawieniach odbioru umowy EDIFACT.

![Wybieranie ustawień sprawdzania duplikatów](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactdupcheck.png)  

1. Tworzenie [aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md) w regionie pomocniczym.    

2. Wyszukaj w **EDIFACT**i wybierz **EDIFACT - Po zmodyfikowaniu numeru kontrolnego.**

   ![Szukaj EDIFACT](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactcn1.png)

   Wyzwalacz monituje o nawiązanie połączenia z kontem integracji. 
   Wyzwalacz powinien być połączony z kontem integracji regionu podstawowego. 

3. Wprowadź nazwę połączenia, wybierz z listy *podstawowe konto integracji regionu* i wybierz pozycję **Utwórz**.    

   ![Nazwa konta integracji regionu podstawowego](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN2.png)

4. Ustawienie **synchronizacji numeru formantu DateTime to start** jest opcjonalne. **Częstotliwość** można ustawić na **Dzień,** **Godzina,** **Minuta**lub **Sekunda** z interwałem.    

   ![DateTime i częstotliwość](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

6. Wybierz **pozycję Nowy krok** > **Dodaj akcję**.    

   ![Nowy krok, Dodaj akcję](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

7. Wyszukaj w **EDIFACT**i wybierz **EDIFACT - Dodaj lub zaktualizuj numery kontrolne**.   

   ![Dodawanie lub aktualizowanie numerów kontrolnych](./media/logic-apps-enterprise-integration-b2b-business-continuity/EdifactChooseAction.png)

8. Aby połączyć akcję z kontem integracji regionu pomocniczego, wybierz pozycję **Zmień połączenie** > **Dodaj nowe połączenie** dla listy dostępnych kont integracji. Wprowadź nazwę połączenia, wybierz konto *integracji regionu pomocniczego* z listy i wybierz pozycję **Utwórz**.

   ![Nazwa konta integracji regionu pomocniczego](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

9. Przełącz się na nieprzetworzone wejścia, klikając ikonę w prawym górnym rogu.

   ![Przełączanie na nieprzetworzone wejścia](./media/logic-apps-enterprise-integration-b2b-business-continuity/Edifactrawinputs.png)

10. Wybierz body z selektora zawartości dynamicznej i zapisz aplikację logiki.   

   ![Dynamiczne pola zawartości](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN7.png)

   Na podstawie przedziału czasu wyzwalacz sonduje tabelę numerów kontrolnych regionu podstawowego i pobiera nowe rekordy.
   Akcja aktualizuje rekordy do konta integracji regionu pomocniczego. 
   Jeśli nie ma żadnych aktualizacji, stan wyzwalacza jest wyświetlany jako **Pominięty**.

   ![Tabela numerów kontrolnych](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

Na podstawie interwału czasu przyrostowy stan środowiska uruchomieniowego jest replikowane z regionu podstawowego do regionu pomocniczego. Podczas wystąpienia awarii, gdy region podstawowy nie jest dostępny, przekieruj ruch do regionu pomocniczego w celu zapewnienia ciągłości biznesowej. 

## <a name="as2"></a>AS2 

Ciągłość działania dokumentów korzystających z protokołu AS2 opiera się na identyfikatorze wiadomości i wartości MIC.

> [!TIP]
> Za pomocą [szablonu szybkiego startu AS2](https://github.com/Azure/azure-quickstart-templates/pull/3302) można również tworzyć aplikacje logiki. Tworzenie kont integracji podstawowej i pomocniczej są wymaganiami wstępnymi do użycia szablonu. Szablon pomaga utworzyć aplikację logiki, która ma wyzwalacz i akcję. Aplikacja logiki tworzy połączenie z wyzwalacza do konta integracji podstawowej i akcji do konta integracji pomocniczej.

1. Tworzenie [aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md) w regionie pomocniczym.  

2. Wyszukaj na **AS2**i wybierz **AS2 - Po utworzeniu wartości MIC**.   

   ![Szukaj AS2](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid1.png)

   Wyzwalacz monituje o nawiązanie połączenia z kontem integracji. 
   Wyzwalacz powinien być połączony z kontem integracji regionu podstawowego. 
   
3. Wprowadź nazwę połączenia, wybierz z listy *podstawowe konto integracji regionu* i wybierz pozycję **Utwórz**.

   ![Nazwa konta integracji regionu podstawowego](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid2.png)

4. **Ustawienie synchronizacji wartości mic DateTime** to start jest opcjonalne. **Częstotliwość** można ustawić na **Dzień,** **Godzina,** **Minuta**lub **Sekunda** z interwałem.   

   ![DateTime i częstotliwość](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid3.png)

5. Wybierz **pozycję Nowy krok** > **Dodaj akcję**.  

   ![Nowy krok, Dodaj akcję](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid4.png)

6. Wyszukaj w **AS2**i wybierz **AS2 - Dodaj lub zaktualizuj zawartość mic**.  

   ![Dodanie lub aktualizacja mic](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid5.png)

7. Aby połączyć akcję z kontem integracji pomocniczej, wybierz **pozycję Zmień połączenie** > **Dodaj nowe połączenie** dla listy dostępnych kont integracji. Wprowadź nazwę połączenia, wybierz konto *integracji regionu pomocniczego* z listy i wybierz pozycję **Utwórz**.

   ![Nazwa konta integracji regionu pomocniczego](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid6.png)

8. Przełącz się na nieprzetworzone wejścia, klikając ikonę w prawym górnym rogu.

   ![Przełączanie na nieprzetworzone wejścia](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2rawinputs.png)

9. Wybierz body z selektora zawartości dynamicznej i zapisz aplikację logiki.   

   ![Zawartość dynamiczna](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid7.png)

   Na podstawie przedziału czasu wyzwalacz sonduje tabelę regionu podstawowego i ściąga nowe rekordy. Akcja aktualizuje je do konta integracji regionu pomocniczego. 
   Jeśli nie ma żadnych aktualizacji, stan wyzwalacza jest wyświetlany jako **Pominięty**.  

   ![Tabela regionu podstawowego](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid8.png)

Na podstawie interwału czasu przyrostowy stan środowiska uruchomieniowego jest replikowane z regionu podstawowego do regionu pomocniczego. Podczas wystąpienia awarii, gdy region podstawowy nie jest dostępny, przekieruj ruch do regionu pomocniczego w celu zapewnienia ciągłości biznesowej. 

## <a name="next-steps"></a>Następne kroki

[Monitorowanie komunikatów B2B przy użyciu dzienników usługi Azure Monitor](../logic-apps/monitor-b2b-messages-log-analytics.md)

