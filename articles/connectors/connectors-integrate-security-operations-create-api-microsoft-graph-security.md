---
title: Integrowanie operacji zabezpieczeń i zarządzanie nimi — Azure Logic Apps & Microsoft Graph zabezpieczenia
description: Zwiększ ochronę przed zagrożeniami, wykrywanie i reagowanie aplikacji, korzystając z Microsoft Graph & zabezpieczeń Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: preetikr
ms.author: preetikr
ms.reviewer: klam, estfan, LADocs
ms.topic: article
ms.date: 01/30/2019
tags: connectors
ms.openlocfilehash: 845f57d84f49bdd964cc6f61790faff093f59466
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72679094"
---
# <a name="improve-threat-protection-by-integrating-security-operations-with-microsoft-graph-security--azure-logic-apps"></a>Poprawa ochrony przed zagrożeniami przez integrację operacji zabezpieczeń z Microsoft Graph & zabezpieczeń Azure Logic Apps

Za pomocą [Azure Logic Apps](../logic-apps/logic-apps-overview.md) i łącznika [zabezpieczeń Microsoft Graph](https://docs.microsoft.com/graph/security-concept-overview) można ulepszyć, w jaki sposób aplikacja wykrywa, chroni i reaguje na zagrożenia przez Tworzenie zautomatyzowanych przepływów pracy na potrzeby integracji produktów, usług i partnerów zabezpieczeń firmy Microsoft. Można na przykład utworzyć [Azure Security Center elementy PlayBook](../security-center/security-center-playbooks.md) , które monitorują Microsoft Graph jednostki zabezpieczeń i zarządzają nimi, na przykład alerty. Poniżej przedstawiono niektóre scenariusze obsługiwane przez łącznik zabezpieczeń Microsoft Graph:

* Otrzymuj alerty na podstawie zapytań lub identyfikatora alertu. Możesz na przykład uzyskać listę zawierającą alerty o wysokiej ważności.
* Aktualizowanie alertów. Można na przykład zaktualizować przypisania alertów, dodać komentarze do alertów lub alerty tagów.
* Monitoruj, kiedy alerty są tworzone lub zmieniane, tworząc [subskrypcje alertów (webhook)](https://docs.microsoft.com/graph/api/resources/webhooks).
* Zarządzaj subskrypcjami alertów. Możesz na przykład uzyskać aktywne subskrypcje, zwiększyć czas wygaśnięcia subskrypcji lub usunąć subskrypcje.

Przepływ pracy aplikacji logiki może korzystać z akcji, które pobierają odpowiedzi z łącznika zabezpieczeń Microsoft Graph i udostępniają te dane wyjściowe innym akcjom w przepływie pracy. Możesz również mieć inne akcje w przepływie pracy, używając danych wyjściowych akcji łącznika zabezpieczeń Microsoft Graph. Jeśli na przykład alerty o wysokiej ważności są uzyskiwane za pośrednictwem łącznika zabezpieczeń Microsoft Graph, można wysłać te alerty w wiadomości e-mail przy użyciu łącznika programu Outlook. 

Aby dowiedzieć się więcej o zabezpieczeniach Microsoft Graph, zobacz [Omówienie interfejsu API zabezpieczeń Microsoft Graph](https://aka.ms/graphsecuritydocs). Jeśli jesteś nowym sposobem logiki aplikacji, zapoznaj [się z tematem Azure Logic Apps?](../logic-apps/logic-apps-overview.md). Jeśli szukasz Microsoft Flow lub PowerApps, zobacz [co to jest przepływ?](https://flow.microsoft.com/) lub [co to jest usługa powerapps?](https://powerapps.microsoft.com/)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). 

* Aby korzystać z łącznika zabezpieczeń Microsoft Graph, musisz mieć *jawnie daną* zgodę administratora dzierżawy Azure Active Directory (AD), która jest częścią [Microsoft Graph wymagania dotyczące uwierzytelniania zabezpieczeń](https://aka.ms/graphsecurityauth). Ta zgoda wymaga identyfikatora aplikacji i nazwy łącznika zabezpieczeń Microsoft Graph, który można również znaleźć w [Azure Portal](https://portal.azure.com):

   | Właściwość | Wartość |
   |----------|-------|
   | **Nazwa aplikacji** | `MicrosoftGraphSecurityConnector` |
   | **Identyfikator aplikacji** | `c4829704-0edc-4c3d-a347-7c4a67586f3c` |
   |||

   Aby przyznać zgodę na łącznik, Administrator dzierżawy usługi Azure AD może wykonać następujące czynności:

   * [Udziel zgody administratora dzierżawy na aplikacje usługi Azure AD](../active-directory/develop/v2-permissions-and-consent.md).

   * Podczas pierwszego uruchomienia aplikacji logiki aplikacja może zażądać zgody od administratora dzierżawy usługi Azure AD za pośrednictwem narzędzia do [wyrażania zgody na korzystanie z aplikacji](../active-directory/develop/application-consent-experience.md).
   
* Podstawowa wiedza [na temat tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, do której chcesz uzyskać dostęp do Microsoft Graph jednostek zabezpieczeń, takich jak alerty. Obecnie ten łącznik nie ma wyzwalaczy. Aby użyć akcji zabezpieczeń Microsoft Graph, uruchom aplikację logiki z wyzwalaczem, na przykład wyzwalaczem **cyklu** .

## <a name="connect-to-microsoft-graph-security"></a>Łączenie z zabezpieczeniami Microsoft Graph 

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [Azure Portal](https://portal.azure.com/)i Otwórz aplikację logiki w Projektancie aplikacji logiki, jeśli nie jest jeszcze otwarta.

1. W przypadku pustych aplikacji logiki Dodaj wyzwalacz i wszelkie inne żądane akcje przed dodaniem akcji zabezpieczeń Microsoft Graph.

   — lub —

   W przypadku istniejących aplikacji logiki w ostatnim kroku, w którym chcesz dodać akcję zabezpieczeń Microsoft Graph, wybierz pozycję **nowy krok**.

   — lub —

   Aby dodać akcję między krokami, przesuń wskaźnik myszy nad strzałkę między krokami. 
   Wybierz wyświetlony znak plus (+), a następnie wybierz pozycję **Dodaj akcję**.

1. W polu wyszukiwania wprowadź "zabezpieczenia programu Microsoft Graph" jako filtr. Z listy Akcje wybierz żądaną akcję.

1. Zaloguj się przy użyciu poświadczeń zabezpieczeń Microsoft Graph.

1. Podaj niezbędne szczegóły wybranej akcji i Kontynuuj tworzenie przepływu pracy aplikacji logiki.

## <a name="add-actions"></a>Dodaj akcje

Poniżej przedstawiono bardziej szczegółowe informacje dotyczące korzystania z różnych akcji dostępnych w ramach łącznika zabezpieczeń Microsoft Graph.

### <a name="manage-alerts"></a>Zarządzanie alertami

Aby odfiltrować, posortować lub pobrać najnowsze wyniki, podaj *tylko* [parametry zapytania ODATA obsługiwane przez Microsoft Graph](https://docs.microsoft.com/graph/query-parameters). *Nie określaj* kompletnego podstawowego adresu URL lub akcji http, na przykład `https://graph.microsoft.com/v1.0/security/alerts`, lub operacji `GET` lub `PATCH`. Oto przykład, w którym przedstawiono parametry akcji **Pobierz alerty** , gdy chcesz uzyskać listę z alertami o wysokiej ważności:

`Filter alerts value as Severity eq 'high'`

Aby uzyskać więcej informacji na temat zapytań, których można użyć z tym łącznikiem, zobacz [dokumentację dotyczącą alertów zabezpieczeń Microsoft Graph](https://docs.microsoft.com/graph/api/alert-list). Aby tworzyć udoskonalone środowiska przy użyciu tego łącznika, Dowiedz się więcej o [właściwościach schematu](https://docs.microsoft.com/graph/api/resources/alert) , które obsługuje łącznik.

| Działanie | Opis |
|--------|-------------|
| **Pobierz alerty** | Uzyskuj filtrowanie alertów na podstawie jednej lub kilku [właściwości alertu](https://docs.microsoft.com/graph/api/resources/alert), na przykład: <p>`Provider eq 'Azure Security Center' or 'Palo Alto Networks'` | 
| **Pobierz Alert według identyfikatora** | Pobierz konkretny alert na podstawie identyfikatora alertu. | 
| **Aktualizowanie alertu** | Aktualizowanie określonego alertu na podstawie identyfikatora alertu. <p>Aby upewnić się, że wymagane i edytowalne właściwości zostały przekazane do żądania, zobacz [edytowalne właściwości alertów](https://docs.microsoft.com/graph/api/alert-update). Na przykład, aby przypisać alert do analityka zabezpieczeń, aby umożliwić badanie, można zaktualizować właściwość **przypisane do** alertu. |
|||

### <a name="manage-alert-subscriptions"></a>Zarządzanie subskrypcjami alertów

Microsoft Graph obsługuje [*subskrypcje*](https://docs.microsoft.com/graph/api/resources/subscription)lub elementy [*webhook*](https://docs.microsoft.com/graph/api/resources/webhooks). Aby uzyskać, zaktualizować lub usunąć subskrypcje, podaj [parametry zapytania OData obsługiwane przez Microsoft Graph](https://docs.microsoft.com/graph/query-parameters) do konstrukcji Microsoft Graph Entity i Dołącz `security/alerts`, a następnie zapytanie OData. 
*Nie dołączaj* podstawowego adresu URL, na przykład `https://graph.microsoft.com/v1.0`. Zamiast tego należy użyć formatu w tym przykładzie:

`security/alerts?$filter=status eq 'New'`

| Działanie | Opis |
|--------|-------------|
| **Tworzenie subskrypcji** | [Utwórz subskrypcję](https://docs.microsoft.com/graph/api/subscription-post-subscriptions) , która powiadamia o wszelkich zmianach. Możesz odfiltrować subskrypcję dla określonych typów alertów. Można na przykład utworzyć subskrypcję, która powiadamia o alertach o wysokiej ważności. |
| **Pobierz aktywne subskrypcje** | [Uzyskaj niewygasłe subskrypcje](https://docs.microsoft.com/graph/api/subscription-list). | 
| **Aktualizowanie subskrypcji** | [Zaktualizuj subskrypcję](https://docs.microsoft.com/graph/api/subscription-update) , podając Identyfikator subskrypcji. Na przykład aby zwiększyć subskrypcję, możesz zaktualizować Właściwość `expirationDateTime` subskrypcji. | 
| **Usuń subskrypcję** | [Usuń subskrypcję](https://docs.microsoft.com/graph/api/subscription-delete) , podając Identyfikator subskrypcji. | 
||| 

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne na temat wyzwalaczy, akcji i limitów, które są opisane w opisie OpenAPI łącznika (dawniej Swagger), przejrzyj [stronę odwołania](https://aka.ms/graphsecurityconnectorreference)łącznika.

## <a name="get-support"></a>Uzyskaj pomoc techniczną

Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md)
