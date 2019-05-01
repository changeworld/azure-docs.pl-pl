---
title: Uzyskaj więcej danych, elementy lub rekordy z podziałem na strony — Azure Logic Apps
description: Skonfigurować podział na strony przekracza limit rozmiaru strony domyślnej akcji łącznika w usłudze Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 04/11/2019
ms.openlocfilehash: 2d1bcf2cf83fab106f79120c3caacc424f839836
ms.sourcegitcommit: daf6538427ea6effef898f2ee3d857e5fa2dccbc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/25/2019
ms.locfileid: "64476544"
---
# <a name="get-more-data-items-or-records-by-using-pagination-in-azure-logic-apps"></a>Uzyskaj więcej danych, elementy lub rekordy przy użyciu dzielenia na strony w usłudze Azure Logic Apps

Podczas pobierania danych, elementy lub rekordów za pomocą akcji łącznika w [usługi Azure Logic Apps](../logic-apps/logic-apps-overview.md), możesz otrzymać zestawów wyników tak dużych, akcja nie zwraca wszystkie wyniki w tym samym czasie. Za pomocą niektórych działań liczba wyników może przekraczać łącznika domyślny rozmiar strony. W tym przypadku akcja zwraca pierwszej strony wyników. Na przykład domyślny rozmiar strony dla łącznika programu SQL Server **Pobierz wiersze** akcji to 2048, ale może się różnić w zależności od innych ustawień.

Pewne działania pozwalają włączyć *dzielenia na strony* ustawienie, aby Twoja aplikacja logiki pobierać więcej wyników do limitu dzielenia na strony, ale zwracają te jako pojedynczy komunikat po zakończeniu działania. Korzystając z podziałem na strony, należy określić *próg* wartość, która jest docelowa liczba wyników akcji do zwrócenia. Akcja pobiera wyniki, aż do osiągnięcia określony próg. Jeśli łączna liczba elementów jest mniejsza niż określony próg, akcja pobiera wszystkie wyniki.

Włączenie tej opcji na stronach pobiera ustawienia podziału na strony wyników na podstawie rozmiaru strony łącznika. To zachowanie oznacza, że czasami może być otrzymasz więcej wyników niż określony próg. Na przykład w przypadku korzystania z programu SQL Server **Pobierz wiersze** akcji, która obsługuje podział na strony ustawienie:

* Akcja ma domyślny rozmiar strony jest 2048 rekordy na każdej stronie.
* Załóżmy, że masz 10 000 rekordów i określić 5000 rekordów jako minimum.
* Podział na strony pobiera strony rekordów, aby pobrać co najmniej określona wartość minimalna, akcja zwraca 6144 rekordów (3 stron x 2048 rekordów), nie 5000 rekordów.

Poniżej przedstawiono listę tylko niektórych łączników, których może przekroczyć domyślny rozmiar strony dla określonych akcji:

* [Azure Blob Storage](https://docs.microsoft.com/connectors/azureblob/)
* [Dynamics 365](https://docs.microsoft.com/connectors/dynamicscrmonline/)
* [Excel](https://docs.microsoft.com/connectors/excel/)
* [HTTP](https://docs.microsoft.com/azure/connectors/connectors-native-http)
* [IBM DB2](https://docs.microsoft.com/connectors/db2/)
* [Microsoft Teams](https://docs.microsoft.com/connectors/teams/)
* [Baza danych Oracle](https://docs.microsoft.com/connectors/oracle/)
* [Salesforce](https://docs.microsoft.com/connectors/salesforce/)
* [SharePoint](https://docs.microsoft.com/connectors/sharepointonline/)
* [SQL Server](https://docs.microsoft.com/connectors/sql/)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure [Załóż bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).

* Aplikacja logiki i akcji, której chcesz włączyć funkcję podziału na strony. Jeśli nie masz aplikacji logiki, zobacz [Szybki Start: Utwórz swoją pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="turn-on-pagination"></a>Włącz podział na strony

Aby ustalić, czy akcja obsługuje podział na strony w Projektancie aplikacji logiki, sprawdź ustawienia akcji **dzielenia na strony** ustawienie. W tym przykładzie pokazano, jak włączyć funkcję podziału na strony w programie SQL Server **Pobierz wiersze** akcji.

1. W akcji w prawym górnym rogu wybierz wielokropek (**...** ) i wybierz **ustawienia**.

   ![Otwórz ustawienia akcji](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings.png)

   Jeśli stronicowanie obsługiwane przez akcję, akcja pokazuje **dzielenia na strony** ustawienie.

1. Zmiana **dzielenia na strony** z **poza** do **na**. W **próg** właściwości, określ dla docelowej liczby wyników, które chcesz, aby akcja zwracająca wartość całkowitą.

   ![Określ minimalną liczbę wyników do zwrócenia](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings-pagination.png)

1. Gdy wszystko będzie gotowe, wybierz pozycję **gotowe**.

## <a name="workflow-definition---pagination"></a>Definicja przepływu pracy — podział na strony

Po włączeniu dzielenia na strony dla akcji, która obsługuje tę możliwość obejmuje definicję przepływu pracy aplikacji logiki `"paginationPolicy"` właściwości wraz z `"minimumItemCount"` właściwość ta akcja `"runtimeConfiguration"` właściwości, na przykład:

```json
"actions": {
   "HTTP": {
      "inputs": {
         "method": "GET",
         "uri": "https://www.testuri.com"
      },
      "runAfter": {},
      "runtimeConfiguration": {
         "paginationPolicy": {
            "minimumItemCount": 1000
         }
      },
      "type": "Http"
   }
},
```

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
