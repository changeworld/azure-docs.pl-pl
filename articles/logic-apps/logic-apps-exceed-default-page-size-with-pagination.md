---
title: Uzyskaj więcej elementów lub rekordów z podziałem na strony
description: Konfigurowanie podziałów na strony, aby przekraczać domyślny limit rozmiaru strony dla akcji łącznika w usłudze Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 04/11/2019
ms.openlocfilehash: 75d9660eb35b5d7ddc644d177c11ae489e2853dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792108"
---
# <a name="get-more-data-items-or-records-by-using-pagination-in-azure-logic-apps"></a>Uzyskaj więcej danych, elementów lub rekordów przy użyciu obsługi strony na strony w usłudze Azure Logic Apps

Podczas pobierania danych, elementów lub rekordów przy użyciu akcji łącznika w [usłudze Azure Logic Apps,](../logic-apps/logic-apps-overview.md)można uzyskać zestawy wyników tak duże, że akcja nie zwraca wszystkie wyniki w tym samym czasie. W przypadku niektórych akcji liczba wyników może przekraczać domyślny rozmiar strony łącznika. W takim przypadku akcja zwraca tylko pierwszą stronę wyników. Na przykład domyślny rozmiar strony dla akcji **Pobierz wiersze** łącznika programu SQL Server to 2048, ale może się różnić w zależności od innych ustawień.

Niektóre akcje umożliwiają włączenie ustawienia podziałki na *strony,* dzięki czemu aplikacja logiki może pobrać więcej wyników do limitu podziałów na strony, ale zwraca te wyniki jako pojedynczy komunikat po zakończeniu akcji. Korzystając z podziałania na strony, należy określić wartość *progową,* która jest docelową liczbą wyników, które mają zostać zwracane. Akcja pobiera wyniki aż do osiągnięcia określonego progu. Gdy całkowita liczba elementów jest mniejsza niż określony próg, akcja pobiera wszystkie wyniki.

Włączenie ustawienia podziałki na strony powoduje pobranie stron wyników na podstawie rozmiaru strony łącznika. To zachowanie oznacza, że czasami można uzyskać więcej wyników niż określony próg. Na przykład podczas korzystania z akcji SQL Server **Pobierz wiersze,** która obsługuje ustawienie podziałania na strony:

* Domyślny rozmiar strony akcji to 2048 rekordów na stronę.
* Załóżmy, że masz 10 000 rekordów i określisz 5000 rekordów jako minimum.
* Podział na strony pobiera strony rekordów, więc aby uzyskać co najmniej określone minimum, akcja zwraca 6144 rekordów (3 strony x 2048 rekordów), a nie 5000 rekordów.

Oto lista z tylko niektórymi łącznikami, na której można przekroczyć domyślny rozmiar strony dla określonych akcji:

* [Magazyn obiektów blob platformy Azure](https://docs.microsoft.com/connectors/azureblob/)
* [Dynamics 365](https://docs.microsoft.com/connectors/dynamicscrmonline/)
* [Programu excel](https://docs.microsoft.com/connectors/excel/)
* [Protokół HTTP](https://docs.microsoft.com/azure/connectors/connectors-native-http)
* [IBM DB2](https://docs.microsoft.com/connectors/db2/)
* [Microsoft Teams](https://docs.microsoft.com/connectors/teams/)
* [Baza danych Oracle](https://docs.microsoft.com/connectors/oracle/)
* [Salesforce](https://docs.microsoft.com/connectors/salesforce/)
* [Program SharePoint](https://docs.microsoft.com/connectors/sharepointonline/)
* [SQL Server](https://docs.microsoft.com/connectors/sql/)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, [zarejestruj się, aby uzyskać bezpłatne konto platformy Azure.](https://azure.microsoft.com/free/)

* Aplikacja logiki i akcja, w której chcesz włączyć podział na strony. Jeśli nie masz aplikacji logiki, zobacz [Szybki start: Tworzenie pierwszej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="turn-on-pagination"></a>Włączanie podziałania na strony

Aby ustalić, czy akcja obsługuje podział na strony w Logic App Designer, sprawdź ustawienia akcji dla ustawienia **podział na strony.** W tym przykładzie pokazano, jak włączyć podział na strony w akcji **Pobierz wiersze** programu SQL Server.

1. W prawym górnym rogu akcji wybierz przycisk elipsy (**...**) i wybierz pozycję **Ustawienia**.

   ![Otwieranie ustawień akcji](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings.png)

   Jeśli akcja obsługuje podział na strony, akcja pokazuje ustawienie **Podział na strony.**

1. Zmień ustawienie **Podział na strony** z **Wyłączone** **na Włączone**. We właściwości **Próg** określ wartość całkowitą dla docelowej liczby wyników, które mają zostać zwracane.

   ![Określ minimalną liczbę wyników do zwrócenia](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings-pagination.png)

1. Gdy wszystko będzie gotowe, wybierz pozycję **Gotowe**.

## <a name="workflow-definition---pagination"></a>Definicja przepływu pracy — podział na strony

Po włączeniu podziałania na strony dla akcji, która obsługuje tę funkcję, `"paginationPolicy"` definicja przepływu `"minimumItemCount"` pracy aplikacji logiki zawiera właściwość wraz z właściwością we `"runtimeConfiguration"` właściwości tej akcji, na przykład:

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
