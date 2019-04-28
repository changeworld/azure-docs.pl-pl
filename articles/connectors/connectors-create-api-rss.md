---
title: Nawiązywanie połączenia źródła danych RSS z usługi Azure Logic Apps | Dokumentacja firmy Microsoft
description: Automatyzowanie zadań i przepływów pracy, które monitorują i zarządzać kanałów informacyjnych RSS przy użyciu usługi Azure Logic Apps
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
ms.topic: article
ms.assetid: a10a6277-ed29-4e68-a881-ccdad6fd0ad8
tags: connectors
ms.date: 08/24/2018
ms.openlocfilehash: 01573871700bbeeb653ce3efdbf6c6aca88fd454
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62104888"
---
# <a name="manage-rss-feeds-by-using-azure-logic-apps"></a>Zarządzanie źródłami danych RSS przy użyciu usługi Azure Logic Apps

Za pomocą usługi Azure Logic Apps i łącznik RSS można utworzyć automatycznych zadań i przepływów pracy na potrzeby RSS dowolnego źródła danych, na przykład:

* Monitorował opublikowaniu elementów kanału informacyjnego RSS.
* Wyświetl listę wszystkich elementów kanału informacyjnego RSS.

Funkcja RSS (Rich Site podsumowanie) jest określana skrótem naprawdę proste syndykacji to popularny format do syndykacji w sieci web i służy do publikowania często aktualizowanej zawartości, takie jak wpisy w blogu i nagłówków wiadomości. Wielu wydawców zawartości zapewniają, że źródła danych RSS, dzięki czemu użytkownicy mogą subskrybować tę zawartość. 

Możesz użyć wyzwalacz kanału informacyjnego RSS, pobiera odpowiedzi z kanału informacyjnego RSS, która udostępnia dane wyjściowe do innych działań. Akcja RSS w aplikacjach logiki służy do wykonywania zadań z kanałem informacyjnym RSS. Jeśli dopiero zaczynasz pracę z usługi logic apps, zapoznaj się z [co to jest Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>. 

* Adres URL dla źródła danych RSS

* Podstawową wiedzę na temat o [sposób tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp RSS do źródła danych. Chcesz rozpocząć od wyzwalacz RSS [Tworzenie pustej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby użyć akcji RSS, uruchom aplikację logiki za pomocą wyzwalacza innego, na przykład, **cyklu** wyzwalacza.

## <a name="connect-to-an-rss-feed"></a>Nawiązać połączenie z kanału informacyjnego RSS

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i Otwórz swoją aplikację logiki w Projektancie aplikacji logiki, jeśli nie otwarto już.

1. Wybierz ścieżkę: 

   * W przypadku aplikacji logiki puste w polu wyszukiwania wprowadź "ciąg rss" jako filtr. W obszarze listy wyzwalaczy wybierz wyzwalacz, który ma. 

     — lub —

   * Dla istniejących aplikacji logiki w ramach kroku, w której chcesz dodać akcję, wybierz **nowy krok**. W polu wyszukiwania jako filtr wprowadź „rss”. W obszarze listy akcji wybierz akcję, którą chcesz.

1. Podaj odpowiednie szczegóły wybranego wyzwalacza lub akcji i kontynuuj tworzenie przepływu pracy aplikacji logiki.

## <a name="connector-reference"></a>Dokumentacja łączników

Szczegółowe informacje techniczne dotyczące wyzwalaczy, akcje i ograniczeń, które opisano przez standard OpenAPI łącznika (dawniej Swagger) opis, przejrzyj łącznika [strona referencyjna](/connectors/rss/).

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o innych [łączników Logic Apps](../connectors/apis-list.md)