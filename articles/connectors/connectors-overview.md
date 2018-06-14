---
title: Omówienie łączników aplikacji logiki | Dokumentacja firmy Microsoft
description: Omówienie łączniki, które mogą być używane w aplikacji logiki
services: ''
documentationcenter: ''
author: jeffhollan
manager: erikre
editor: ''
tags: connectors
ms.assetid: ca8dab2e-9b69-4b1e-865d-1facd9f0cdac
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/15/2016
ms.author: jehollan
ms.openlocfilehash: 6f7d8f99bfa09847c01831a06efa8b94c1c0a89a
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2018
ms.locfileid: "27961656"
---
# <a name="using-connectors-in-a-logic-app"></a>Używanie łączników w aplikacji logiki
Łączniki zapewniają szybki dostęp do zdarzeń, danych i działania usług, protokołów i platform.  Pełną listę łączników, które obsługuje Logic Apps można [można znaleźć tutaj](apis-list.md).  Łączniki mogą być używane jako wyzwalacz lub akcji w aplikacji logiki i może wymagać skonfigurowany *połączenia* do użycia (na przykład: autoryzowanie konta w usłudze Twitter lub post w Twoim imieniu).

## <a name="basics"></a>Podstawy
Łączniki są można uzyskać dostępu do aplikacji logiki do integracji z innymi usługami, takich jak Dynamics Azure Salesforce, w ramach usług hostowanych [i więcej](apis-list.md).  Są wdrażane i zarządzane przez firmę Microsoft, więc można tworzyć przepływy pracy integracji z skali, przepływności i podjąć obsługę zabezpieczeń.  Możesz dodać łącznika do aplikacji logiki wyszukiwanie i wybierając akcję łącznik lub wyzwolenia w obszarze **Pokaż Microsoft zarządzanych interfejsów API**.

![Menu Akcja wybierania wyzwalacza][1]

Każdy łącznik akcji lub wyzwalacza będzie mieć jego zbiór właściwości, aby skonfigurować.  Kliknij przyciski informacje, aby dowiedzieć się więcej na temat akcji lub odwołać dokumentacji [więcej](apis-list.md).

Jeśli chcesz zintegrować z usługą lub interfejsu API, które nie są jeszcze łącznika można rozszerzać logiki aplikacji za pośrednictwem [niestandardowy łącznik](../logic-apps/logic-apps-create-api-app.md) lub po prostu Wywołaj bezpośrednio do usługi za pośrednictwem protokołu, takich jak HTTP.

## <a name="triggers"></a>Wyzwalacze
Niektóre łączniki ma wyzwalacz, co oznacza zdarzenia z tego łącznika spowoduje uruchomienie aplikacji logiki i przekazywanie danych w ramach wyzwalacza.  Wyzwalacz zawsze jest pierwszym etapem aplikacji logiki.  Wyzwalacze popularnych obejmują operacje takie jak:

* Cyklu — uruchamiane co godzinę
* Po odebraniu żądania HTTP
* Gdy element zostanie dodany do kolejki
* Po odebraniu wiadomości e-mail

Niektóre wyzwalacze będą uruchamiane natychmiast, które zdarzenie odbywa się w ramach powiadomienia do aplikacji logiki i inne muszą interwał cyklu skonfigurowane na częstotliwość aplikacji logiki będzie Sprawdź, czy usługa zdarzenia (maksymalnie co 15 sekund).  

Po odebraniu zdarzenia uruchomią aplikację logiki, uruchomić i rozpocznie działań w przepływie pracy.  Ponadto można uzyskać dostępu do żadnych danych z wyzwalacza w całym przepływie pracy (na przykład wyzwalacza "na nowy tweet" przekazuje tweet do uruchomienia).

## <a name="actions"></a>Akcje
Większość łączniki mają jeden lub wiele działań, które mogą być wykonywane w ramach przepływu pracy.  Akcje są wszystkie kroki, które stanie po procesie zostało uruchomione z wyzwalacza.  Aby dodać działanie, kliknij **nowy krok** przycisk i wyszukaj łącznik, którego chcesz użyć.  Raz wybrane (i po konfigurowanie wszelkich [połączeń](#connections) które mogą być wymagane) zobaczysz karty akcji można skonfigurować.  Wybierz dane z poprzednich kroków, klikając na dowolnym tokenów dla danych wyjściowych lub wprowadź w dowolnej innej konfiguracji zgodnie z potrzebami.

![Konfigurowanie akcji łącznika][2]

## <a name="connections"></a>Połączenia
Większość łączników wymagają skonfigurowania *połączenia* zanim będzie możliwe użycie łącznika.  A *połączenia* żadnych potrzeba dostępu łącznika do konfiguracji połączenia lub logowania.  Łączniki, które korzysta z protokołu OAuth i tworzyć połączenia oznacza zalogowaniem się do usługi (np. Office 365, Salesforce lub GitHub), gdzie tokenu dostępu mogą być szyfrowane i bezpiecznie przechowywane w magazynie Azure tajny.  Inne łączniki (na przykład FTP i SQL) wymagają połączenie, zawierający konfigurację, takie jak adres serwera, nazwę użytkownika i hasło.  Te dane konfiguracyjne połączenia są również są szyfrowane i bezpiecznie zmagazynowane.  Połączenia będą mogli uzyskać dostępu do usługi dla tak długo, jak w usłudze.  Dla połączeń usługi Azure Active Directory OAuth (takich jak usługi Office 365 i Dynamics) możemy w dalszym ciągu odświeżać token dostępu przez nieograniczony czas.  Inne usługi może wprowadzić limity na jak długo możemy użyć tokenu bez Trwa odświeżanie.  Ogólnie rzecz biorąc pewne akcje, takie jak zmiana haseł spowoduje unieważnienie wszystkich tokenów dostępu.  

Połączenia można wyświetlać i zarządzać na platformie Azure, klikając **Przeglądaj** i wybierając **połączenia interfejsu API**.  Z zasobów połączenia interfejsu API można wyświetlać, edytować, zaktualizować lub ponownie autoryzować połączeń, które zostały utworzone.

## <a name="next-steps"></a>Następne kroki
* [Tworzenie pierwszej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Dowiedz się wspólne używa i przykładowe aplikacje logiki](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Rozpoczynanie pracy z enterprise integracji wyzwalacze i akcje](../logic-apps/logic-apps-enterprise-integration-overview.md)

<!--Image References -->
[1]: ./media/connectors-overview/addAction.png
[2]: ./media/connectors-overview/configureAction.png
