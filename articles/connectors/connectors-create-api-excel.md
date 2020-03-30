---
title: Zarządzanie danymi, arkuszami i tabelami w aplikacji Excel Online
description: Zarządzanie danymi w arkuszach i tabelach w aplikacji Excel Online dla Firm lub w aplikacji Excel Online dla usługi OneDrive przy użyciu aplikacji Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 08/23/2018
tags: connectors
ms.openlocfilehash: b1df1219590aa94331a4404b1315eb7231c59699
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445886"
---
# <a name="manage-excel-online-data-with-azure-logic-apps"></a>Zarządzanie danymi aplikacji Excel Online za pomocą aplikacji Azure Logic Apps

Za [pomocą aplikacji Azure Logic Apps](../logic-apps/logic-apps-overview.md) i łącznika usługi Excel Online dla [Firm](/connectors/excelonlinebusiness/) lub [łącznika aplikacji Excel Online dla usługi OneDrive](/connectors/excelonline/) można tworzyć zautomatyzowane zadania i przepływy pracy na podstawie danych w aplikacji Excel Online dla Firm lub w usłudze OneDrive. Ten łącznik udostępnia akcje ułatwiające pracę z danymi i zarządzanie arkuszami kalkulacyjnymi, na przykład:

* Tworzenie nowych arkuszy i tabel.
* Pobierz arkusze, tabele i wiersze i zarządzaj nimi.
* Dodaj pojedyncze wiersze i kolumny kluczy.

Następnie można użyć danych wyjściowych z tych akcji z akcjami dla innych usług. Jeśli na przykład używasz akcji, która tworzy arkusze co tydzień, możesz użyć innej akcji, która wysyła wiadomość e-mail z potwierdzeniem przy użyciu łącznika programu Office 365 Outlook.

Jeśli jesteś nowy w aplikacjach logiki, sprawdź [Co to jest usługa Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

> [!NOTE]
> Łączniki [aplikacji Excel Online dla Firm](/connectors/excelonlinebusiness/) i Aplikacji Excel Online dla usługi [OneDrive](/connectors/excelonline/) współpracują z aplikacjami logiki azure i różnią się od [łącznika programu Excel dla usługi PowerApps](/connectors/excel/).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* [Konto usługi Office 365](https://www.office.com/) dla konta służbowego lub osobistego konta Microsoft

  Dane programu Excel mogą istnieć jako plik csv (CSV) oddzielony przecinkami w folderze magazynu, na przykład w usłudze OneDrive. 
  Można również użyć tego samego pliku CSV ze [złączem pliku płaskiego](../logic-apps/logic-apps-enterprise-integration-flatfile.md).

* Podstawowa wiedza na temat [tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp do danych aplikacji Excel Online. Ten łącznik zawiera tylko akcje, więc aby uruchomić aplikację logiki, wybierz oddzielny wyzwalacz, na przykład wyzwalacz **cyklu.**

## <a name="add-excel-action"></a>Dodawanie akcji programu Excel

1. W [witrynie Azure portal](https://portal.azure.com)otwórz aplikację logiki w projektancie aplikacji logiki, jeśli nie jest jeszcze otwarty.

1. W obszarze wyzwalacza wybierz pozycję **Nowy krok**.

1. W polu wyszukiwania wpisz "excel" jako filtr. W obszarze listy akcje wybierz odpowiednią akcję.

   > [!NOTE]
   > Projektant aplikacji logiki nie można załadować tabel, które mają 100 lub więcej kolumn. Jeśli to możliwe, zmniejsz liczbę kolumn w wybranej tabeli, aby projektant mógł załadować tabelę.

1. Jeśli zostanie wyświetlony monit, zaloguj się do konta usługi Office 365.

   Poświadczenia autoryzują aplikację logiki do tworzenia połączenia z aplikacją Excel Online i uzyskiwania dostępu do danych.

1. Kontynuuj dostarczanie szczegółów niezbędnych do wybranej akcji i tworzenie przepływu pracy aplikacji logiki.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne, takie jak wyzwalacze, akcje i limity, zgodnie z opisem plików OpenAPI łącznika (dawniej Swagger), zobacz następujące strony odwołania do łącznika:

* [Excel Online dla Firm](/connectors/excelonlinebusiness/)
* [Aplikacja Excel Online dla usługi OneDrive](/connectors/excelonline/)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o innych [łącznikach aplikacji logiki](../connectors/apis-list.md)
