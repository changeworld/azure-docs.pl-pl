---
title: Integracja operacji zabezpieczeń i zarządzanie nimi & programie Microsoft Graph Security
description: Usprawnij ochronę, wykrywanie i reagowanie aplikacji za pomocą aplikacji Microsoft Graph Security & usługi Azure Logic Apps
services: logic-apps
ms.suite: integration
author: preetikr
ms.author: preetikr
ms.reviewer: v-ching, estfan, logicappspm
ms.topic: article
ms.date: 02/21/2020
tags: connectors
ms.openlocfilehash: b4f51b192d1a7c0ee14a769321793753e8217dea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598837"
---
# <a name="improve-threat-protection-by-integrating-security-operations-with-microsoft-graph-security--azure-logic-apps"></a>Zwiększ ochronę przed zagrożeniami, integrując operacje zabezpieczeń z aplikacjami Microsoft Graph Security & Azure Logic Apps

Dzięki [usłudze Azure Logic Apps](../logic-apps/logic-apps-overview.md) i łącznikowi zabezpieczeń programu Microsoft [Graph](https://docs.microsoft.com/graph/security-concept-overview) można poprawić sposób wykrywania, ochrony i reagowania na zagrożenia przez tworzenie zautomatyzowanych przepływów pracy służących do integracji produktów, usług i partnerów zabezpieczeń firmy Microsoft. Na przykład można utworzyć [podręczniki usługi Azure Security Center,](../security-center/security-center-playbooks.md) które monitorują i zarządzają encjami programu Microsoft Graph Security, takimi jak alerty. Oto kilka scenariuszy obsługiwanych przez łącznik programu Microsoft Graph Security:

* Otrzymuj alerty na podstawie zapytań lub identyfikatora alertu. Na przykład można uzyskać listę, która zawiera alerty o wysokiej ważności.

* Aktualizowanie alertów. Na przykład można aktualizować przypisania alertów, dodawać komentarze do alertów lub oznaczać alerty.

* Monitoruj, kiedy alerty są tworzone lub zmieniane przez tworzenie [subskrypcji alertów (elementów webhook).](https://docs.microsoft.com/graph/api/resources/webhooks)

* Zarządzanie subskrypcjami alertów. Na przykład można uzyskać aktywne subskrypcje, przedłużyć czas wygaśnięcia subskrypcji lub usunąć subskrypcje.

Przepływ pracy aplikacji logiki można użyć akcji, które otrzymują odpowiedzi z łącznika microsoft Graph Security i udostępnić dane wyjściowe do innych akcji w przepływie pracy. Można również mieć inne akcje w przepływie pracy, aby użyć danych wyjściowych z akcji łącznika zabezpieczeń programu Microsoft Graph. Na przykład jeśli otrzymasz alerty o wysokiej ważności za pośrednictwem łącznika microsoft Graph Security, możesz wysłać te alerty w wiadomości e-mail przy użyciu łącznika programu Outlook. 

Aby dowiedzieć się więcej o programie Microsoft Graph Security, zobacz [omówienie interfejsu API zabezpieczeń programu Microsoft Graph](https://aka.ms/graphsecuritydocs). Jeśli jesteś nowy w aplikacjach logiki, sprawdź [Co to jest usługa Azure Logic Apps?](../logic-apps/logic-apps-overview.md). Jeśli szukasz usługi Microsoft Flow lub PowerApps, zobacz [What is PowerApps?](https://powerapps.microsoft.com/) [Co to jest przepływ?](https://flow.microsoft.com/)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). 

* Aby korzystać z łącznika usługi Microsoft Graph Security, konieczne jest posiadanie *jawnie udzielonej* zgody administratora dzierżawy usługi Azure Active Directory (AD), co jest częścią [wymagań uwierzytelniania w usłudze Microsoft Graph Security](https://aka.ms/graphsecurityauth). Ta zgoda wymaga identyfikatora i nazwy aplikacji łącznika programu Microsoft Graph Security, który można również znaleźć w [witrynie Azure portal:](https://portal.azure.com)

  | Właściwość | Wartość |
  |----------|-------|
  | **Nazwa aplikacji** | `MicrosoftGraphSecurityConnector` |
  | **Identyfikator aplikacji** | `c4829704-0edc-4c3d-a347-7c4a67586f3c` |
  |||

  Aby udzielić zgody na łącznik, administrator dzierżawy usługi Azure AD może wykonać następujące kroki:

  * [Udzielić zgody administratora dzierżawy dla aplikacji usługi Azure AD](../active-directory/develop/v2-permissions-and-consent.md).

  * Przy pierwszym uruchomieniu aplikacji logiki aplikacja może zażądać zgody od administratora dzierżawy usługi Azure AD za pomocą [funkcji zgody dla aplikacji](../active-directory/develop/application-consent-experience.md).
   
* Podstawowa wiedza na temat [tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp do jednostek zabezpieczeń programu Microsoft Graph, takich jak alerty. Aby użyć wyzwalacza zabezpieczeń programu Microsoft Graph, potrzebna jest pusta aplikacja logiki. Aby użyć akcji zabezpieczeń programu Microsoft Graph, potrzebujesz aplikacji logiki, która rozpoczyna się od odpowiedniego wyzwalacza dla danego scenariusza.

## <a name="connect-to-microsoft-graph-security"></a>Łączenie się z programem Microsoft Graph Security 

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/)i otwórz aplikację logiki w logice App Designer, jeśli nie jest już otwarty.

1. W przypadku pustych aplikacji logiki przed dodaniem akcji zabezpieczeń programu Microsoft Graph należy dodać wyzwalacz i inne akcje.

   — lub —

   W przypadku istniejących aplikacji logiki w ostatnim kroku, w którym chcesz dodać akcję Microsoft Graph Security, wybierz pozycję **Nowy krok**.

   — lub —

   Aby dodać akcję między krokami, przesuń wskaźnik myszy na strzałkę między krokami. Zaznacz wyświetlony znak plus (+), a następnie wybierz pozycję **Dodaj akcję**.

1. W polu wyszukiwania wpisz "microsoft graph security" jako filtr. Z listy akcji wybierz odpowiednią akcję.

1. Zaloguj się przy użyciu poświadczeń programu Microsoft Graph Security.

1. Podaj szczegóły niezbędne do wybranej akcji i kontynuuj tworzenie przepływu pracy aplikacji logiki.

## <a name="add-triggers"></a>Dodawanie wyzwalaczy

W usłudze Azure Logic Apps każda aplikacja logiki musi zaczynać się od [wyzwalacza,](../logic-apps/logic-apps-overview.md#logic-app-concepts)który uruchamia się, gdy występuje określone zdarzenie lub gdy zostanie spełniony określony warunek. Za każdym razem, gdy wyzwalacz jest uruchamiany, aparat aplikacji logiki tworzy wystąpienie aplikacji logiki i uruchamia przepływ pracy aplikacji.

> [!NOTE] 
> Po uruchomieniu wyzwalacza wyzwalacz przetwarza wszystkie nowe alerty. Jeśli nie zostaną odebrane żadne alerty, uruchomienie wyzwalacza zostanie pominięte. Następna ankieta wyzwalacza odbywa się na podstawie interwału cyklu, który można określić we właściwościach wyzwalacza.

W tym przykładzie pokazano, jak można uruchomić przepływ pracy aplikacji logiki, gdy nowe alerty są wysyłane do aplikacji.

1.  W witrynie Azure portal lub visual studio utwórz pustą aplikację logiki, która otwiera projektanta aplikacji logiki. W tym przykładzie użyto witryny Azure portal.

1.  Na projektancie w polu wyszukiwania wprowadź "microsoft graph security" jako filtr. Z listy wyzwalaczy wybierz ten wyzwalacz: **We wszystkich nowych alertach**

1.  W wyzwalaczu podaj informacje o alertach, które chcesz monitorować. Aby uzyskać więcej właściwości, otwórz listę **Dodaj nowy parametr** i wybierz parametr, aby dodać tę właściwość do wyzwalacza.

   | Właściwość | Właściwość (JSON) | Wymagany | Typ | Opis |
   |----------|-----------------|----------|------|-------------|
   | **Interwał** | `interval` | Tak | Liczba całkowita | Dodatnia liczba całkowita, która opisuje, jak często przepływ pracy jest uruchamiany na podstawie częstotliwości. Oto minimalne i maksymalne odstępy czasu: <p><p>- Miesiąc: 1-16 miesięcy <br>- Dzień: 1-500 dni <br>- Godzina: 1-12 000 godzin <br>- Minuta: 1-72 000 minut <br>- Drugi: 1-9,999,999 sekundy <p>Na przykład, jeśli interwał wynosi 6, a częstotliwość to "Miesiąc", cykl jest co 6 miesięcy. |
   | **Częstotliwości** | `frequency` | Tak | Ciąg | Jednostka czasu dla cyklu: **Drugi**, **Minuta,** **Godzina,** **Dzień,** **Tydzień**lub **Miesiąc** |
   | **Strefa czasowa** | `timeZone` | Nie | Ciąg | Ma zastosowanie tylko wtedy, gdy określisz godzinę rozpoczęcia, ponieważ ten wyzwalacz nie akceptuje [przesunięcia UTC](https://en.wikipedia.org/wiki/UTC_offset). Wybierz strefę czasową, którą chcesz zastosować. |
   | **Godzina rozpoczęcia** | `startTime` | Nie | Ciąg | Podaj datę i godzinę rozpoczęcia w tym formacie: <p><p>YYYY-MM-DDThh:mm:ss po wybraniu strefy czasowej <p>— lub — <p>YYYY-MM-DDThh:mm:ssZ jeśli nie wybierzesz strefy czasowej <p>Na przykład jeśli chcesz 18 września 2017 o 14:00, a następnie określić "2017-09-18T14:00:00" i wybierz strefę czasową, taką jak Pacyfik czasu standardowego. Możesz też określić "2017-09-18T14:00:00Z" bez strefy czasowej. <p>**Uwaga:** Ten czas rozpoczęcia ma maksymalnie 49 lat w przyszłości i musi być zgodny ze [specyfikacją iso 8601 data time](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) w [formacie daty UTC,](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)ale bez [przesunięcia UTC](https://en.wikipedia.org/wiki/UTC_offset). Jeśli nie wybierzesz strefy czasowej, musisz dodać literę "Z" na końcu bez spacji. To "Z" odnosi się do równoważnego [czasu żeglarskiego](https://en.wikipedia.org/wiki/Nautical_time). <p>W przypadku prostych harmonogramów czas rozpoczęcia jest pierwszym wystąpieniem, podczas gdy w przypadku złożonych harmonogramów wyzwalacz nie uruchamia się wcześniej niż godzina rozpoczęcia. [*Jakie są sposoby używania daty i godziny rozpoczęcia?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   ||||||

1.  Po zakończeniu na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

1.  Teraz kontynuuj dodawanie jednej lub więcej akcji do aplikacji logiki dla zadań, które chcesz wykonać z wynikami wyzwalacza.

## <a name="add-actions"></a>Dodawanie akcji

Poniżej przedstawiono bardziej szczegółowe informacje na temat używania różnych akcji dostępnych za pomocą łącznika microsoft Graph Security.

### <a name="manage-alerts"></a>Zarządzanie alertami

Aby filtrować, sortować lub utrzymywać najnowsze wyniki, podaj *tylko* [parametry kwerendy ODATA obsługiwane przez program Microsoft Graph](https://docs.microsoft.com/graph/query-parameters). *Nie określaj* na przykład pełnego podstawowego adresu `https://graph.microsoft.com/v1.0/security/alerts`URL ani `GET` `PATCH` akcji HTTP ani operacji lub operacji. Oto konkretny przykład, który pokazuje parametry akcji **Pobierz alerty,** gdy chcesz mieć listę z alertami o wysokiej ważności:

`Filter alerts value as Severity eq 'high'`

Aby uzyskać więcej informacji na temat kwerend, których można używać z tym łącznikiem, zobacz [dokumentację alertów zabezpieczeń programu Microsoft Graph](https://docs.microsoft.com/graph/api/alert-list). Aby utworzyć ulepszone środowiska z tym łącznikiem, dowiedz się więcej o [alertach właściwości schematu,](https://docs.microsoft.com/graph/api/resources/alert) które obsługuje łącznik.

| Akcja | Opis |
|--------|-------------|
| **Otrzymuj alerty** | Pobierz alerty filtrowane na podstawie co najmniej jednej `Provider eq 'Azure Security Center' or 'Palo Alto Networks'` [właściwości alertu](https://docs.microsoft.com/graph/api/resources/alert), na przykład . | 
| **Otrzymuj alerty według identyfikatora** | Uzyskaj określony alert na podstawie identyfikatora alertu. | 
| **Alert aktualizacji** | Zaktualizuj określony alert na podstawie identyfikatora alertu. Aby upewnić się, że w żądaniu zostały wprowadzone wymagane i edytowalne właściwości, zobacz [edytowalne właściwości alertów](https://docs.microsoft.com/graph/api/alert-update). Na przykład, aby przypisać alert do analityka zabezpieczeń, aby mogli zbadać, można zaktualizować alert **przypisany do** właściwości. |
|||

### <a name="manage-alert-subscriptions"></a>Zarządzanie subskrypcjami alertów

Program Microsoft Graph obsługuje [*subskrypcje*](https://docs.microsoft.com/graph/api/resources/subscription)lub [*elementów webhook.*](https://docs.microsoft.com/graph/api/resources/webhooks) Aby uzyskać, zaktualizować lub usunąć subskrypcje, podaj [parametry kwerendy ODATA obsługiwane przez program Microsoft Graph](https://docs.microsoft.com/graph/query-parameters) do konstruowania jednostki programu Microsoft Graph i dołącz `security/alerts` do nich kwerendę ODATA. *Nie dołączaj* na przykład podstawowego `https://graph.microsoft.com/v1.0`adresu URL . Zamiast tego użyj formatu w tym przykładzie:

`security/alerts?$filter=status eq 'New'`

| Akcja | Opis |
|--------|-------------|
| **Tworzenie subskrypcji** | [Utwórz subskrypcję,](https://docs.microsoft.com/graph/api/subscription-post-subscriptions) która powiadomi Cię o wszelkich zmianach. Tę subskrypcję można filtrować pod kątem określonych typów alertów. Na przykład można utworzyć subskrypcję, która powiadamia o alerty o wysokiej ważności. |
| **Uzyskaj aktywne subskrypcje** | [Uzyskaj niewygasłe subskrypcje](https://docs.microsoft.com/graph/api/subscription-list). | 
| **Aktualizuj subskrypcję** | [Zaktualizuj subskrypcję,](https://docs.microsoft.com/graph/api/subscription-update) podając identyfikator subskrypcji. Na przykład, aby rozszerzyć subskrypcję, można `expirationDateTime` zaktualizować właściwość subskrypcji. | 
| **Usuwanie subskrypcji** | [Usuń subskrypcję,](https://docs.microsoft.com/graph/api/subscription-delete) podając identyfikator subskrypcji. | 
||| 

### <a name="manage-threat-intelligence-indicators"></a>Zarządzanie wskaźnikami analizy zagrożeń

Aby filtrować, sortować lub utrzymywać najnowsze wyniki, podaj *tylko* [parametry kwerendy ODATA obsługiwane przez program Microsoft Graph](https://docs.microsoft.com/graph/query-parameters). *Nie określaj* na przykład pełnego podstawowego adresu `https://graph.microsoft.com/beta/security/tiIndicators`URL ani `GET` `PATCH` akcji HTTP ani operacji lub operacji. Oto konkretny przykład, który pokazuje parametry get **tiIndicators** akcji, gdy chcesz `DDoS` listę, która ma typ zagrożenia:

`Filter threat intelligence indicator value as threatType eq 'DDoS'`

Aby uzyskać więcej informacji na temat kwerend, których można używać z tym łącznikiem, zobacz ["Opcjonalne parametry kwerendy" w dokumentacji referencyjnej analizy zagrożeń zabezpieczeń programu Microsoft Graph](https://docs.microsoft.com/graph/api/tiindicators-list?view=graph-rest-beta&tabs=http). Aby utworzyć ulepszone środowiska z tym łącznikiem, dowiedz się więcej o [wskaźniku analizy zagrożeń właściwości schematu,](https://docs.microsoft.com/graph/api/resources/tiindicator?view=graph-rest-beta) który obsługuje łącznik.

| Akcja | Opis |
|--------|-------------|
| **Uzyskaj wskaźniki analizy zagrożeń** | Pobierz tiIndicators filtrowane na podstawie jednej lub więcej [właściwości tiIndicator](https://docs.microsoft.com/graph/api/resources/tiindicator?view=graph-rest-beta), na przykład,`threatType eq 'MaliciousUrl' or 'DDoS'` |
| **Uzyskaj wskaźnik analizy zagrożeń według identyfikatora** | Pobierz określony tiIndicator na podstawie identyfikatora tiIndicator. | 
| **Tworzenie wskaźnika analizy zagrożeń** | Utwórz nowy tiIndicator, publikując w kolekcji tiIndicators. Aby upewnić się, że należy przekazać wymagane właściwości w żądaniu, zapoznaj się z [wymaganymi właściwościami do tworzenia tiIndicator](https://docs.microsoft.com/graph/api/tiindicators-post?view=graph-rest-beta&tabs=http). |
| **Przesyłanie wielu wskaźników analizy zagrożeń** | Utwórz wiele nowych tiIndicators przez zaksięgowanie kolekcji tiIndicators. Aby upewnić się, że należy przekazać wymagane właściwości w żądaniu, zapoznaj się z [wymaganymi właściwościami przesyłania wielu tiIndicators](https://docs.microsoft.com/graph/api/tiindicator-submittiindicators?view=graph-rest-beta&tabs=http). |
| **Aktualizowanie wskaźnika analizy zagrożeń** | Zaktualizuj określony identyfikator tiIndicator na podstawie identyfikatora tiIndicator. Aby upewnić się, że przekażesz wymagane i edytowalne właściwości w żądaniu, zobacz [edytowalne właściwości tiIndicator](https://docs.microsoft.com/graph/api/tiindicator-update?view=graph-rest-beta&tabs=http). Na przykład, aby zaktualizować akcję do zastosowania, jeśli wskaźnik jest dopasowany z poziomu narzędzia zabezpieczeń targetProduct, można zaktualizować właściwość **akcji** tiIndicatora. |
| **Aktualizowanie wielu wskaźników analizy zagrożeń** | Zaktualizuj wiele tiIndicators. Aby upewnić się, że przekazujesz wymagane właściwości w żądaniu, zapoznaj się z [wymaganymi właściwościami aktualizacji wielu tiIndicators](https://docs.microsoft.com/graph/api/tiindicator-updatetiindicators?view=graph-rest-beta&tabs=http). |
| **Usuwanie wskaźnika analizy zagrożeń według identyfikatora** | Usuń określony tiIndicator na podstawie identyfikatora tiIndicator. |
| **Usuwanie wielu wskaźników analizy zagrożeń według identyfikatorów** | Usuń wiele tiIndicators przez ich identyfikatory. Aby upewnić się, że należy przekazać wymagane właściwości w żądaniu, zapoznaj się z [wymaganymi właściwościami usuwania wielu identyfikatorów przez identyfikatory](https://docs.microsoft.com/graph/api/tiindicator-deletetiindicators?view=graph-rest-beta&tabs=http). |
| **Usuwanie wielu wskaźników analizy zagrożeń przez identyfikatory zewnętrzne** | Usuń wiele tiIndicators przez identyfikatory zewnętrzne. Aby upewnić się, że przekazujesz wymagane właściwości w żądaniu, zapoznaj się z [wymaganymi właściwościami usuwania wielu identyfikatorów tiIndicatorów przez zewnętrzne identyfikatory](https://docs.microsoft.com/graph/api/tiindicator-deletetiindicatorsbyexternalid?view=graph-rest-beta&tabs=http). |
|||

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne na temat wyzwalaczy, akcji i limitów, które są opisane przez opis OpenAPI łącznika (dawniej Swagger), przejrzyj [stronę referencyjną](https://aka.ms/graphsecurityconnectorreference)łącznika .

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o innych [łącznikach aplikacji logiki](../connectors/apis-list.md)
