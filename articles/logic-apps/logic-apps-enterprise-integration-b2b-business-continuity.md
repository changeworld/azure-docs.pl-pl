---
title: Odzyskiwanie po awarii dla konta integracji B2B — usługi Azure Logic Apps | Dokumentacja firmy Microsoft
description: Odzyskiwanie po awarii B2B aplikacje logiki
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: jeconnoc
editor: ''
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 1882207207327214ee6519a98715d06a7e32ab93
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37045606"
---
# <a name="logic-apps-b2b-cross-region-disaster-recovery"></a>Odzyskiwanie po awarii między region B2B aplikacje logiki

Obciążeń B2B obejmują transakcje pieniężne jak zamówienia i faktury. Podczas zdarzenia po awarii jest krytyczne dla działalności Szybkie odzyskiwanie, aby spełnić SLA biznesowej uzgodnione z ich partnerów. W tym artykule przedstawiono sposób tworzenia planu ciągłości biznesowej B2B obciążeń. 

* Gotowość odzyskiwania po awarii 
* Przełączyć region pomocniczy podczas zdarzenia po awarii 
* Wrócić do regionu podstawowego po zdarzeniu po awarii

## <a name="disaster-recovery-readiness"></a>Gotowość odzyskiwania po awarii  

1. Określić region pomocniczy i utworzyć [konta integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) w regionie pomocniczym.

2. Dodaj partnerów, schematów i umów dotyczących przepływów wymagane wiadomości, którym stan uruchomienia musi zostać zreplikowane do regionu pomocniczego konta integracji.

   > [!TIP]
   > Upewnij się, że istnieje spójności w konwencji nazewnictwa artefaktu konta integracji w regionach. 

3. Aby stan uruchomienia ściąganie danych z regionu podstawowego, należy utworzyć aplikację logiki w regionie pomocniczym. 

   Ta aplikacja logiki powinny mieć *wyzwalacza* i *akcji*. 
   Wyzwalacz ma nawiązać regionie podstawowym konta integracji i akcji należy łączyć do regionu pomocniczego konta integracji. 
   Oparte na przedział czasu, wyzwalacz sonduje regionu podstawowego, uruchom tabeli stanu i pobiera nowe rekordy, jeśli istnieje. Aktualizuje je do regionu pomocniczego konta integracji. 
   Dzięki temu można uzyskać stanu działania przyrostowe od regionu podstawowego do regionu pomocniczego.

4. Ciągłość prowadzenia działalności biznesowej na koncie integracji usługa Logic Apps jest przeznaczona do obsługi oparte na protokołach B2B - X12, AS2 i EDIFACT. Aby uzyskać szczegółowy opis kroków, wybierz odpowiednie łącza.

5. Zaleca się za wdrażanie wszystkie zasoby w regionie podstawowym w regionie pomocniczym. 

   Zasoby regionu podstawowego obejmują bazy danych SQL Azure lub bazy danych rozwiązania Cosmos platformy Azure, Azure Service Bus i usługi Azure Event Hubs, używany do obsługi wiadomości, Azure API Management i funkcji usługi Azure Logic Apps w usłudze Azure App Service.   

6. Nawiąż połączenie z regionu podstawowego w regionie pomocniczym. Aby stan uruchomienia ściąganie danych z regionu podstawowego, należy utworzyć aplikację logiki w regionie pomocniczym. 

   Aplikację logiki ma wyzwalacz i akcji. 
   Wyzwalacz ma nawiązać konta integracji regionu podstawowego. 
   Akcja powinni połączyć się z regionu pomocniczego konta integracji. 
   Oparte na przedział czasu, wyzwalacz sonduje regionu podstawowego, uruchom tabeli stanu i pobiera nowe rekordy, jeśli istnieje. 
   Aktualizuje je do regionu pomocniczego konta integracji. 
   Ten proces pozwala uzyskać stanu działania przyrostowe w regionie podstawowym w regionie pomocniczym.

Ciągłość prowadzenia działalności biznesowej w ramach konta integracji usługa Logic Apps zapewnia obsługę oparte na i protokoły B2B X12, AS2, EDIFACT. Aby uzyskać szczegółowe instrukcje na temat używania X12 i AS2, zobacz [X12](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#x12) i [AS2](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md#as2) w tym artykule.

## <a name="fail-over-to-a-secondary-region-during-a-disaster-event"></a>Awaryjnie w regionie pomocniczym podczas zdarzenia po awarii

Podczas zdarzenie po awarii, gdy regionu podstawowego nie jest dostępna dla ciągłość prowadzenia działalności biznesowej, bezpośrednie ruchu w regionie pomocniczym. Pomaga regionu pomocniczego, a biznesowych do odzyskania funkcji szybko, aby spełnić RPO/RTO uzgodnione przez ich partnerów. Zmniejsza on również starań, aby przełączyć się z jednego regionu w innym regionie. 

Brak oczekiwanego opóźnienia podczas kopiowania numery kontroli z regionu podstawowego w regionie pomocniczym. Aby uniknąć wysyłania numery zduplikowane kontroli wygenerowanego partnerom podczas zdarzenia po awarii, zaleca się zwiększenie liczby kontroli w regionie pomocniczym umowy przy użyciu [poleceń cmdlet programu PowerShell](https://blogs.msdn.microsoft.com/david_burgs_blog/2017/03/09/fresh-of-the-press-new-azure-powershell-cmdlets-for-upcoming-x12-connector-disaster-recovery).

## <a name="fall-back-to-a-primary-region-post-disaster-event"></a>Wrócić do regionu podstawowego zdarzenia po awarii

Aby wrócić do regionu podstawowego, gdy jest ona dostępna, wykonaj następujące kroki:

1. Akceptować komunikatów z partnerami w regionie pomocniczym.  

2. Zwiększenie liczby wygenerowanego sterowania dla wszystkich umów regionu podstawowego przy użyciu [poleceń cmdlet programu PowerShell](https://blogs.msdn.microsoft.com/david_burgs_blog/2017/03/09/fresh-of-the-press-new-azure-powershell-cmdlets-for-upcoming-x12-connector-disaster-recovery).  

3. Bezpośrednie ruch z regionu pomocniczego do regionu podstawowego.

4. Sprawdź, czy włączono aplikacji logiki utworzona w regionie pomocniczym dla ściąganie stan uruchomienia z regionu podstawowego.

## <a name="x12"></a>X12 

Ciągłość prowadzenia działalności biznesowej dla EDI X 12 dokumenty są oparte na numery kontroli:

> [!TIP]
> Można również użyć [X12 szybki start szablonu](https://azure.microsoft.com/resources/templates/201-logic-app-b2b-disaster-recovery-replication/) do tworzenia aplikacji logiki. Tworzenie konta integracji podstawowe i pomocnicze są wymagania wstępne, aby użyć szablonu. Szablon ułatwia tworzenie aplikacji logiki dwóch dla kontroli odebranej liczby i osobne numery wygenerowanego kontroli. Odpowiednich wyzwalacze i akcje są tworzone w aplikacjach logiki, wyzwalacz łączenia się z podstawowej integracji konta i akcji na koncie dodatkowej integracji.

**Wymagania wstępne**

Aby włączyć odzyskiwanie po awarii dla wiadomości przychodzących, wybierz ustawienia sprawdzania duplikatów w X12 ustawienia odbierania umowy.

![Wybierz ustawienia sprawdzania duplikatów](./media/logic-apps-enterprise-integration-b2b-business-continuity/dupcheck.png)  

1. Utwórz [aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md) w regionie pomocniczym.    

2. Wyszukaj **X12**i wybierz **X12-modyfikacji numer formantu**.   

   ![Wyszukaj X12](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn1.png)

   Wyzwalacz monituje o nawiązać połączenie z kontem integracji. 
   Wyzwalacz powinny być połączone z kontem integracji regionu podstawowego.

3. Wpisz nazwę połączenia, wybierz użytkownika *regionie podstawowym konta integracji* na liście i wybierz polecenie **Utwórz**.   

   ![Nazwa konta integracji regionu podstawowego](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn2.png)

4. **DateTime można uruchomić synchronizacji numer kontroli** ustawienie jest opcjonalne. **Częstotliwość** może być ustawiony na **dzień**, **godzina**, **minutę**, lub **drugi** interwał.   

   ![Daty i godziny i częstotliwość](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

5. Wybierz **nowy krok** > **Dodaj akcję**.

   ![Nowy krok, Dodaj Action](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

6. Wyszukaj **X12**i wybierz **X12-dodania lub zaktualizowania numery kontroli**.   

   ![Dodaj lub zaktualizuj numery kontrolne](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn5.png)

7. Aby połączyć się z regionu pomocniczego konta integracji z akcji, wybierz **zmienić połączenie** > **Dodaj nowe połączenie** listę kont dostępnych integracji. Wpisz nazwę połączenia, wybierz użytkownika *regionu pomocniczego konta integracji* na liście i wybierz polecenie **Utwórz**. 

   ![Nazwa konta integracji regionu pomocniczego](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

8. Przejdź do nieprzetworzone dane wejściowe, klikając ikonę w prawym górnym rogu.

   ![Przełącz się do nieprzetworzone dane wejściowe](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12rawinputs.png)

9. Wybierz treść z selektor zawartości dynamicznej, a następnie zapisz aplikację logiki.

   ![Pola zawartości dynamicznej](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn7.png)

   Oparte na przedział czasu, wyzwalacz sonduje tabeli numerów kontroli regionu podstawowego odebranych i pobiera nowe rekordy. 
   Aktualizuje rekordy w regionie pomocniczym konta integracji. 
   Jeśli nie ma żadnych aktualizacji, stan wyzwalacza jest wyświetlany jako **pomijane**.   

   ![Kontrola numeru tabeli](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

Oparte na przedział czasu, stanu działania przyrostowe replikuje z regionu podstawowego w regionie pomocniczym. Podczas zdarzenie po awarii, gdy regionu podstawowego nie jest dostępna, bezpośrednie ruch do regionu pomocniczego dla ciągłość prowadzenia działalności biznesowej. 

## <a name="edifact"></a>EDIFACT 

Ciągłość prowadzenia działalności biznesowej dla dokumentów EDI EDIFACT opiera się na numery kontroli.

**Wymagania wstępne**

Aby włączyć odzyskiwanie po awarii dla wiadomości przychodzących, wybierz ustawienia sprawdzania duplikatów w umowie EDIFACT odbierania ustawień.

![Wybierz ustawienia sprawdzania duplikatów](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactdupcheck.png)  

1. Utwórz [aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md) w regionie pomocniczym.    

2. Wyszukaj **EDIFACT**i wybierz **EDIFACT - modyfikacji numer formantu**.

   ![Wyszukaj EDIFACT](./media/logic-apps-enterprise-integration-b2b-business-continuity/edifactcn1.png)

   Wyzwalacz monituje o nawiązać połączenie z kontem integracji. 
   Wyzwalacz powinny być połączone z kontem integracji regionu podstawowego. 

3. Wpisz nazwę połączenia, wybierz użytkownika *regionie podstawowym konta integracji* na liście i wybierz polecenie **Utwórz**.    

   ![Nazwa konta integracji regionu podstawowego](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN2.png)

4. **DateTime można uruchomić synchronizacji numer kontroli** ustawienie jest opcjonalne. **Częstotliwość** może być ustawiony na **dzień**, **godzina**, **minutę**, lub **drugi** interwał.    

   ![Daty i godziny i częstotliwość](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn3.png)

6. Wybierz **nowy krok** > **Dodaj akcję**.    

   ![Nowy krok, Dodaj Action](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn4.png)

7. Wyszukaj **EDIFACT**i wybierz **EDIFACT - dodania lub zaktualizowania numery kontroli**.   

   ![Dodaj lub zaktualizuj numery kontrolne](./media/logic-apps-enterprise-integration-b2b-business-continuity/EdifactChooseAction.png)

8. Aby połączyć się z regionu pomocniczego konta integracji z akcji, wybierz **zmienić połączenie** > **Dodaj nowe połączenie** listę kont dostępnych integracji. Wpisz nazwę połączenia, wybierz użytkownika *regionu pomocniczego konta integracji* na liście i wybierz polecenie **Utwórz**.

   ![Nazwa konta integracji regionu pomocniczego](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12cn6.png)

9. Przejdź do nieprzetworzone dane wejściowe, klikając ikonę w prawym górnym rogu.

   ![Przełącz się do nieprzetworzone dane wejściowe](./media/logic-apps-enterprise-integration-b2b-business-continuity/Edifactrawinputs.png)

10. Wybierz treść z selektor zawartości dynamicznej, a następnie zapisz aplikację logiki.   

   ![Pola zawartości dynamicznej](./media/logic-apps-enterprise-integration-b2b-business-continuity/X12CN7.png)

   Oparte na przedział czasu, wyzwalacz sonduje tabeli numerów kontroli regionu podstawowego odebranych i pobiera nowe rekordy.
   Aktualizuje rekordy do regionu pomocniczego konta integracji. 
   Jeśli nie ma żadnych aktualizacji, stan wyzwalacza jest wyświetlany jako **pomijane**.

   ![Kontrola numeru tabeli](./media/logic-apps-enterprise-integration-b2b-business-continuity/x12recevicedcn8.png)

Oparte na przedział czasu, stanu działania przyrostowe replikuje z regionu podstawowego w regionie pomocniczym. Podczas zdarzenie po awarii, gdy regionu podstawowego nie jest dostępna, bezpośrednie ruch do regionu pomocniczego dla ciągłość prowadzenia działalności biznesowej. 

## <a name="as2"></a>AS2 

Ciągłość prowadzenia działalności biznesowej dla dokumentów, które używają protokołu AS2 opiera się na identyfikator komunikatu i wartość Mikrofon.

> [!TIP]
> Można również użyć [szablonów szybki start AS2](https://github.com/Azure/azure-quickstart-templates/pull/3302) do tworzenia aplikacji logiki. Tworzenie konta integracji podstawowe i pomocnicze są wymagania wstępne, aby użyć szablonu. Szablon ułatwia tworzenie aplikacji logiki wyzwalacza i akcji. Aplikację logiki tworzy połączenie z wyzwalacza konta podstawowej integracji i akcji na koncie dodatkowej integracji.

1. Utwórz [aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md) w regionie pomocniczym.  

2. Wyszukaj **AS2**i wybierz **AS2 — wartość Micznych po utworzeniu**.   

   ![Wyszukaj AS2](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid1.png)

   Wyzwalacz monituje o nawiązać połączenie z kontem integracji. 
   Wyzwalacz powinny być połączone z kontem integracji regionu podstawowego. 
   
3. Wpisz nazwę połączenia, wybierz użytkownika *regionie podstawowym konta integracji* na liście i wybierz polecenie **Utwórz**.

   ![Nazwa konta integracji regionu podstawowego](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid2.png)

4. **DateTime można uruchomić synchronizacji wartość Micznych** ustawienie jest opcjonalne. **Częstotliwość** może być ustawiony na **dzień**, **godzina**, **minutę**, lub **drugi** interwał.   

   ![Daty i godziny i częstotliwość](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid3.png)

5. Wybierz **nowy krok** > **Dodaj akcję**.  

   ![Nowy krok, Dodaj Action](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid4.png)

6. Wyszukaj **AS2**i wybierz **AS2 - dodania lub zaktualizowania zawartości Micznych**.  

   ![Dodawanie Mikrofon lub aktualizacji](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid5.png)

7. Połącz się kontem dodatkowej integracji z akcji, wybierz **zmienić połączenie** > **Dodaj nowe połączenie** listę kont dostępnych integracji. Wpisz nazwę połączenia, wybierz użytkownika *regionu pomocniczego konta integracji* na liście i wybierz polecenie **Utwórz**.

   ![Nazwa konta integracji regionu pomocniczego](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid6.png)

8. Przejdź do nieprzetworzone dane wejściowe, klikając ikonę w prawym górnym rogu.

   ![Przełącz się do nieprzetworzone dane wejściowe](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2rawinputs.png)

9. Wybierz treść z selektor zawartości dynamicznej, a następnie zapisz aplikację logiki.   

   ![Zawartość dynamiczna](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid7.png)

   Oparte na przedział czasu, wyzwalacz sonduje tabeli regionu podstawowego i pobiera nowe rekordy. Aktualizuje je do regionu pomocniczego konta integracji. 
   Jeśli nie ma żadnych aktualizacji, stan wyzwalacza jest wyświetlany jako **pomijane**.  

   ![Tabela regionu podstawowego](./media/logic-apps-enterprise-integration-b2b-business-continuity/as2messageid8.png)

Oparte na przedział czasu, stanu działania przyrostowe replikuje z regionu podstawowego w regionie pomocniczym. Podczas zdarzenie po awarii, gdy regionu podstawowego nie jest dostępna, bezpośrednie ruch do regionu pomocniczego dla ciągłość prowadzenia działalności biznesowej. 

## <a name="next-steps"></a>Kolejne kroki

[Monitorowanie komunikatów B2B](logic-apps-monitor-b2b-message.md)

