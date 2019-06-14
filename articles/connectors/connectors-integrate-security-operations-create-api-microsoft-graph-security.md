---
title: Integracja operacje zabezpieczeń z usługą Microsoft Graph zabezpieczenia — Azure Logic Apps
description: Zwiększyć możliwości ochrony, wykrywania i reagowania zagrożeń Twojej aplikacji, zarządzając operacje zabezpieczeń za pomocą programu Microsoft Graph Security i Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: preetikr
ms.author: preetikr
ms.reviewer: klam, estfan, LADocs
ms.topic: article
ms.date: 01/30/2019
tags: connectors
ms.openlocfilehash: 24963a35bc3e54b2d140bf4ed1d169b213bd9b2a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60448053"
---
# <a name="improve-threat-protection-by-integrating-security-operations-with-microsoft-graph-security--azure-logic-apps"></a>Poprawa ochrony przed zagrożeniami, integrując operacje zabezpieczeń za pomocą programu Microsoft Graph Security i Azure Logic Apps

Za pomocą [usługi Azure Logic Apps](../logic-apps/logic-apps-overview.md) i [Microsoft Graph Security](https://docs.microsoft.com/graph/security-concept-overview) łącznika, można zwiększyć, jak aplikacja wykryje, chroni i reaguje na zagrożenia przez tworzenie automatycznych przepływów pracy integracji firmy Microsoft zabezpieczenia produktów, usług i partnerów. Na przykład można utworzyć [elementów playbook w usłudze Azure Security Center](../security-center/security-center-playbooks.md) , monitorowanie i zarządzanie nią podmiotów zabezpieczeń programu Microsoft Graph, takich jak alerty. Poniżej przedstawiono kilka scenariuszy, obsługiwane przez łącznik programu Microsoft Graph zabezpieczeń:

* Otrzymuj alerty na podstawie zapytania lub według identyfikatorów alertów. Na przykład można pobrać listy, która zawiera alerty o wysokiej ważności.
* Aktualizowanie alertów. Na przykład można zaktualizować alertu przypisania, dodać komentarze do alertów lub oznaczyć alerty.
* Monitor sytuacje, gdy alerty są tworzone lub zmienione, tworząc [alert subskrypcje (elementów webhook)](https://docs.microsoft.com/graph/api/resources/webhooks).
* Zarządzaj swoimi subskrypcjami alertu. Na przykład można uzyskać aktywne subskrypcje, wydłużenia czasu wygaśnięcia subskrypcji lub usuwania subskrypcji.

Przepływ pracy aplikacji logiki można użyć działań, które uzyskiwanie odpowiedzi z łącznika programu Microsoft Graph zabezpieczeń, a następnie udostępnić te dane wyjściowe do innych działań w przepływie pracy. Może również mieć inne akcje przepływ pracy korzystający dane wyjściowe z akcji łącznika programu Microsoft Graph zabezpieczeń. Na przykład jeśli możesz otrzymywać alerty o wysokiej ważności, za pośrednictwem łącznika Microsoft Graph Security, możesz wysłać te alerty w wiadomościach e-mail przy użyciu łącznika programu Outlook. 

Aby dowiedzieć się więcej na temat zabezpieczeń programu Microsoft Graph, zobacz [omówienie interfejsu API programu Microsoft Graph zabezpieczeń](https://aka.ms/graphsecuritydocs). Jeśli dopiero zaczynasz pracę z usługi logic apps, zapoznaj się z [co to jest Azure Logic Apps?](../logic-apps/logic-apps-overview.md). Jeśli szukasz Microsoft Flow lub PowerApps, zobacz [co to jest przepływ?](https://flow.microsoft.com/) lub [co to jest rozwiązanie PowerApps?](https://powerapps.microsoft.com/)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). 

* Do korzystania z łącznika programu Microsoft Graph zabezpieczeń, konieczne jest posiadanie *jawnie danego* zgody administratora dzierżawy usługi Azure Active Directory (AD), który jest częścią programu [wymagania dotyczące programu Microsoft Graph zabezpieczeń uwierzytelniania ](https://aka.ms/graphsecurityauth). To wyrażenie zgody wymaga łącznika programu Microsoft Graph Security Identyfikatora aplikacji i nazwę, którą można znaleźć w [witryny Azure portal](https://portal.azure.com):

   | Właściwość | Wartość |
   |----------|-------|
   | **Nazwa aplikacji** | `MicrosoftGraphSecurityConnector` |
   | **Identyfikator aplikacji** | `c4829704-0edc-4c3d-a347-7c4a67586f3c` |
   |||

   Aby udzielić zgody dla łącznika, administratorem dzierżawy usługi Azure AD tych kroków można albo:

   * [Udzielić zgody administratora dzierżawy dla aplikacji usługi Azure AD](../active-directory/develop/v2-permissions-and-consent.md).

   * Przy pierwszym uruchomieniu aplikacji logiki, aplikację można zażądania zgody od administratora dzierżawy usługi Azure AD za pomocą [środowisko zgody aplikacji](../active-directory/develop/application-consent-experience.md).
   
* Podstawową wiedzę na temat o [sposób tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki gdzie, do którego chcesz uzyskać dostęp jednostek Microsoft Graph zabezpieczeń, takich jak alerty. Obecnie ten łącznik nie ma wyzwalaczy. Tak, aby użyć akcji zabezpieczeń programu Microsoft Graph, uruchom aplikację logiki z wyzwalaczem, na przykład, **cyklu** wyzwalacza.

## <a name="connect-to-microsoft-graph-security"></a>Łączenie z usługą Security programu Microsoft Graph 

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/)i Otwórz swoją aplikację logiki w Projektancie aplikacji logiki, jeśli nie otwarto już.

1. Dla aplikacji logiki puste Dodaj wyzwalacz i żądane przed dodaniem akcji programu Microsoft Graph Security akcje.

   —lub—

   Dla istniejących aplikacji logiki w ostatnim kroku, którym chcesz dodać akcję zabezpieczeń programu Microsoft Graph wybierz **nowy krok**.

   —lub—

   Aby dodać akcję między krokami, wskaźnik myszy nad strzałką znajdującą się między krokami. 
   Wybierz znak plus (+), który pojawia się, a następnie wybierz **Dodaj akcję**.

1. W polu wyszukiwania wprowadź "programu microsoft graph zabezpieczenia" jako filtr. Z listy akcji wybierz akcję, którą chcesz.

1. Zaloguj się przy użyciu poświadczeń zabezpieczeń programu Microsoft Graph.

1. Podaj odpowiednie szczegóły wybranej akcji i kontynuuj tworzenie przepływu pracy aplikacji logiki.

## <a name="add-actions"></a>Dodawanie akcji

Poniżej przedstawiono bardziej szczegółowe informacje o korzystaniu z różnych dostępnych akcji za pomocą łącznika programu Microsoft Graph zabezpieczeń.

### <a name="manage-alerts"></a>Zarządzanie alertami

Do filtrowania, sortowania, lub Uzyskaj najnowsze wyniki, podaj *tylko* [parametry zapytania ODATA obsługiwane przez program Microsoft Graph](https://docs.microsoft.com/graph/query-parameters). *Nie określaj* pełną podstawowy adres URL lub akcje HTTP, na przykład `https://graph.microsoft.com/v1.0/security/alerts`, lub `GET` lub `PATCH` operacji. Oto konkretnemu przykładowi, który zawiera parametry dla **Otrzymuj alerty** akcji, jeśli chcesz, aby listy przy użyciu alertów o wysokiej ważności:

`Filter alerts value as Severity eq 'high'`

Aby uzyskać więcej informacji na temat zapytania za pomocą tego łącznika, zobacz [dokumentację referencyjną alerty programu Microsoft Graph zabezpieczeń](https://docs.microsoft.com/graph/api/alert-list). Aby skompilować rozszerzonych środowisk wynika z tego łącznika, Dowiedz się więcej o [alerty właściwości schematu](https://docs.microsoft.com/graph/api/resources/alert) , łącznik obsługuje.

| Akcja | Opis |
|--------|-------------|
| **Otrzymuj alerty** | Pobierz alerty filtrowane na podstawie co najmniej jeden [alert właściwości](https://docs.microsoft.com/graph/api/resources/alert), na przykład: <p>`Provider eq 'Azure Security Center' or 'Palo Alto Networks'` | 
| **Uzyskiwanie alertu według Identyfikatora** | Pobierz określony alert, w oparciu o identyfikator alertu. | 
| **Aktualizowanie alertu** | Aktualizuj określonego alertu na podstawie identyfikatora alertu. <p>Aby upewnić się, Przekaż wymaganą, jak i można edytować właściwości w żądaniu, zobacz [edytowalnych właściwości alertów](https://docs.microsoft.com/graph/api/alert-update). Na przykład, aby przypisać alert do analityka zabezpieczeń, więc one można zbadać, możesz zaktualizować ten alert **przypisane do** właściwości. |
|||

### <a name="manage-alert-subscriptions"></a>Zarządzaj subskrypcjami alertu

Program Microsoft Graph obsługuje [ *subskrypcje*](https://docs.microsoft.com/graph/api/resources/subscription), lub [ *elementów webhook*](https://docs.microsoft.com/graph/api/resources/webhooks). Pobieranie, aktualizację, lub usuwania subskrypcji, podaj [parametry zapytania ODATA obsługiwane przez program Microsoft Graph](https://docs.microsoft.com/graph/query-parameters) do jednostki programu Microsoft Graph konstruowania i obejmują `security/alerts` następuje zapytania ODATA. 
*Nie dołączaj* podstawowy adres URL, na przykład `https://graph.microsoft.com/v1.0`. W tym przykładzie, zamiast tego użyj formatu:

`security/alerts?$filter=status eq 'New'`

| Akcja | Opis |
|--------|-------------|
| **Tworzenie subskrypcji** | [Utwórz subskrypcję](https://docs.microsoft.com/graph/api/subscription-post-subscriptions) który powiadamia o zmianach. Można filtrować tę subskrypcję do określonych typów alertów, które chcesz. Na przykład można utworzyć subskrypcję powiadomienia o alertach o wysokiej ważności. |
| **Pobierz aktywne subskrypcje** | [Pobiera subskrypcje niewygasłe](https://docs.microsoft.com/graph/api/subscription-list). | 
| **Aktualizowanie subskrypcji** | [Aktualizowanie subskrypcji](https://docs.microsoft.com/graph/api/subscription-update) , podając identyfikator subskrypcji. Na przykład, aby rozszerzyć swoją subskrypcję, można zaktualizować subskrypcji `expirationDateTime` właściwości. | 
| **Usuwanie subskrypcji** | [Usuwanie subskrypcji](https://docs.microsoft.com/graph/api/subscription-delete) , podając identyfikator subskrypcji. | 
||| 

## <a name="connector-reference"></a>Dokumentacja łączników

Szczegółowe informacje techniczne dotyczące wyzwalaczy, akcje i ograniczeń, które opisano przez standard OpenAPI łącznika (dawniej Swagger) opis, przejrzyj łącznika [strona referencyjna](https://aka.ms/graphsecurityconnectorreference).

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o innych [łączników Logic Apps](../connectors/apis-list.md)
