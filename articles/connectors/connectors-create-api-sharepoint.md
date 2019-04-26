---
title: Łączenie z programem SharePoint z usługi Azure Logic Apps | Dokumentacja firmy Microsoft
description: Automatyzowanie zadań i przepływów pracy, które monitorują i zarządzanie zasobami w usłudze SharePoint Online lub SharePoint Server w środowisku lokalnym za pomocą usługi Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: e0ec3149-507a-409d-8e7b-d5fbded006ce
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: e636b2bb08477e6c56c6ae41f08983fc5bfa2a9b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60450747"
---
# <a name="monitor-and-manage-sharepoint-resources-with-azure-logic-apps"></a>Monitorowanie i zarządzanie zasobami programu SharePoint za pomocą usługi Azure Logic Apps

Korzystając z usługi Azure Logic Apps i łączniku programu SharePoint można utworzyć zautomatyzowanym zadaniom i przepływów pracy, które monitorowanie i zarządzanie nimi zasobów, takich jak pliki, foldery, listy, elementy, osoby, i tak dalej w usłudze SharePoint Online lub w programie SharePoint Server w środowisku lokalnym, na przykład:

* Monitor, gdy pliki lub elementy zostaną utworzone, zmieniony lub usunięty.
* Tworzenie, pobieranie, aktualizacji lub usuwania elementów.
* Dodawanie, pobieranie lub usuwaj załączniki. Pobierz zawartość z załącznikami.
* Tworzenie, kopiowanie, aktualizacji lub usuwania plików. 
* Aktualizuj właściwości pliku. Pobieranie zawartości, metadanych lub właściwości pliku.
* Lista lub wyodrębniania folderów.
* Pobieranie listy lub widoki z listy.
* Ustaw stan zatwierdzenia zawartości.
* Rozwiąż osób.
* Wysyłanie żądania HTTP do programu SharePoint.
* Pobierz wartości jednostki.

Możesz użyć wyzwalaczy, które uzyskiwanie odpowiedzi z poziomu programu SharePoint i udostępnić dane wyjściowe innych działań. Akcje w aplikacjach logiki służy do wykonywania zadań w programie SharePoint. Mogą też istnieć inne akcje użyć danych wyjściowych z akcji programu SharePoint. Na przykład jeśli regularnie możesz pobrać pliki z programu SharePoint, możesz wysłać komunikaty do Twojego zespołu za pomocą łącznika usługi Slack.
Jeśli dopiero zaczynasz pracę z usługi logic apps, zapoznaj się z [co to jest Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>. 

* Poświadczenia użytkownika i adres witryny programu SharePoint

  Poświadczenia Autoryzuj aplikację logiki, aby utworzyć połączenie i dostęp do konta programu SharePoint. 

* Aplikacje logiki można nawiązać połączenie systemów lokalnych, takich jak program SharePoint Server, musisz najpierw [zainstalować i skonfigurować lokalną bramę danych](../logic-apps/logic-apps-gateway-install.md). W ten sposób można określić instalację bramy są używane podczas tworzenia połączenia z serwerem programu SharePoint dla twojej aplikacji logiki.

* Podstawową wiedzę na temat o [sposób tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp do konta programu SharePoint. Zaczynać się od wyzwalacza programu SharePoint, [Tworzenie pustej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby użyć działania programu SharePoint, uruchomić swoją aplikację logiki z wyzwalaczem, takich jak Salesforce wyzwalacza, jeśli masz konto usługi Salesforce.

  Na przykład, można uruchomić aplikację logiki z **gdy rekord zostanie utworzony** wyzwalacza usługi Salesforce. 
  Wyzwalacza każdorazowo, utworzony nowy rekord zostanie zaktualizowany w usłudze Salesforce. 
  Następnie można wykonać tego wyzwalacza z programem SharePoint **Utwórz plik** akcji. Dzięki temu, gdy tworzony jest nowy rekord, aplikacja logiki tworzy plik w programie SharePoint informacje na temat tego nowego rekordu.

## <a name="connect-to-sharepoint"></a>Łączenie z programem SharePoint

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i Otwórz swoją aplikację logiki w Projektancie aplikacji logiki, jeśli nie otwarto już.

1. Puste logic apps w polu wyszukiwania wprowadź "sharepoint" jako filtr. W obszarze listy wyzwalaczy wybierz wyzwalacz, który ma. 

   — lub —

   Dla istniejących aplikacji logiki w ostatnim kroku, które chcesz dodać akcję programu SharePoint wybierz **nowy krok**. 
   W polu wyszukiwania wprowadź "sharepoint" jako filtr. 
   W obszarze listy akcji wybierz akcję, którą chcesz.

   Aby dodać akcję między krokami, wskaźnik myszy nad strzałką znajdującą się między krokami. 
   Wybierz znak plus (**+**) pojawia się, a następnie wybierz **Dodaj akcję**.

1. Po wyświetleniu monitu zaloguj się w podaj niezbędne informacje o połączeniu. Jeśli używasz programu SharePoint Server, upewnij się, możesz wybrać **Połącz za pośrednictwem lokalnej bramy danych**. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

1. Podaj odpowiednie szczegóły wybranego wyzwalacza lub akcji i kontynuuj tworzenie przepływu pracy aplikacji logiki.

## <a name="connector-reference"></a>Dokumentacja łączników

Szczegółowe informacje techniczne dotyczące wyzwalaczy, akcje i ograniczeń, które opisano przez standard OpenAPI łącznika (dawniej Swagger) opis, przejrzyj łącznika [strona referencyjna](/connectors/sharepoint/).

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o innych [łączników Logic Apps](../connectors/apis-list.md)
