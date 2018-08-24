---
title: Łączenie z programem Excel Online — usługi Azure Logic Apps | Dokumentacja firmy Microsoft
description: Zarządzanie danymi za pomocą programu Excel Online interfejsów API REST i usługi Azure Logic Apps
ms.service: logic-apps
services: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.topic: article
ms.date: 08/23/2018
ms.openlocfilehash: 94960b95e6de30159ec34b3f97bb5119cac42c35
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/24/2018
ms.locfileid: "42818103"
---
# <a name="manage-excel-online-data-with-azure-logic-apps"></a>Zarządzanie danymi programu Excel Online z usługą Azure Logic Apps

Za pomocą usługi Azure Logic Apps i łącznika programu Excel Online możesz utworzyć automatycznych zadań i przepływów pracy na podstawie danych w usłudze Excel Online dla firm lub OneDrive. Przepływy pracy można wykonywać te akcje i inne osoby ze swoimi danymi, na przykład:

* Tworzenie nowych arkuszy i tabel.
* Pobierz i Zarządzaj arkuszy, tabele i wiersze.
* Dodaj pojedynczego wierszy i kolumn kluczy.

Możesz dołączyć akcje w aplikacjach logiki, korzystających z danych wyjściowych z akcji programu Excel Online. Ten łącznik udostępnia tylko akcje, aby uruchomić aplikację logiki, użyj oddzielnych wyzwalacza, takich jak **cyklu** wyzwalacza. Na przykład jeśli tworzysz arkuszy co tydzień, możesz wysłać pocztą e-mail informacje dotyczące tych nowych arkuszy, za pomocą łącznika usługi Office 365 Outlook.

Jeśli dopiero zaczynasz pracę z usługi logic apps, zapoznaj się z [co to jest Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

> [!NOTE]
> [Usłudze Excel Online dla firm](/connectors/excelonlinebusiness/) i [Excel Online, usłudze OneDrive](/connectors/excelonline/) łączniki działają z usługą Azure Logic Apps i różnią się od [łącznika programu Excel w usłudze PowerApps](/connectors/excel/).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>. 

* [Konta usługi Office 365](https://www.office.com/) dla konta służbowego lub osobistego konta Microsoft 

  Twoje dane programu Excel może istnieć w formacie wartości rozdzielanych przecinkami (CSV) w folderze pamięci masowej, na przykład w usłudze OneDrive. 
  Można również użyć tego samego pliku CSV przy użyciu [łącznik plików płaskiej](../logic-apps/logic-apps-enterprise-integration-flatfile.md).

* Podstawową wiedzę na temat o [sposób tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp do danych w usłudze Excel Online. Ten łącznik udostępnia tylko akcje, aby uruchomić aplikację logiki, wybierz oddzielne wyzwalacz, na przykład, **cyklu** wyzwalacza.

## <a name="add-excel-action"></a>Dodawanie akcji programu Excel

1. W [witryny Azure portal](https://portal.azure.com), Otwórz aplikację logiki w Projektancie aplikacji logiki, jeśli nie już otwarty.

1. W obszarze wyzwalacza wybierz **nowy krok**.

1. W polu wyszukiwania wprowadź "programu excel" jako filtr. W obszarze listy akcji wybierz akcję, którą chcesz.

1. W przypadku wyświetlenia monitu zaloguj się do konta usługi Office 365 wybierz **Zaloguj**. 

   Poświadczenia Autoryzuj aplikację logiki, aby utworzyć połączenie do programu Excel w trybie Online i uzyskiwać dostęp do danych.

1. Kontynuuj, podając wymagane szczegóły wybranej akcji i tworzenie przepływu pracy aplikacji logiki.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne, takie jak akcje i ograniczeń, opisane przez plików struktury Swagger łączników Zobacz te strony odwołania łącznika:

* [Program Excel w trybie Online dla firm](/connectors/excelonlinebusiness/) 
* [Excel Online do usługi OneDrive](/connectors/excelonline/) 

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o innych [łączników Logic Apps](../connectors/apis-list.md)
