---
title: Migrowanie aplikacji do najnowszego schematu
description: Jak przeprowadzić migrację aplikacji logiki do najnowszej wersji schematu
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
ms.openlocfilehash: dc967c90f28b29a63a1f26d0aae86b6d769c1ccf
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74786932"
---
# <a name="migrate-logic-apps-to-latest-schema-version"></a>Migrowanie aplikacji logiki do najnowszej wersji schematu

Aby przenieść istniejące aplikacje logiki do najnowszego schematu, wykonaj następujące kroki: 

1. W [Azure Portal](https://portal.azure.com)Otwórz aplikację logiki w Projektancie aplikacji logiki.

2. W menu aplikacji logiki wybierz pozycję **Przegląd**. Na pasku narzędzi wybierz pozycję **Aktualizuj schemat**.

   > [!NOTE]
   > Po wybraniu opcji **Aktualizuj schemat**Azure Logic Apps automatycznie uruchamia kroki migracji i udostępnia dane wyjściowe kodu. Możesz użyć tych danych wyjściowych do zaktualizowania definicji aplikacji logiki. Należy jednak przestrzegać najlepszych rozwiązań zgodnie z opisem w sekcji **najlepsze rozwiązania** .

   ![Aktualizuj schemat](./media/connectors-schema-migration/update-schema.png)

   Zostanie wyświetlona strona aktualizowanie schematu z linkiem do dokumentu opisującego ulepszenia w nowym schemacie.

## <a name="best-practices"></a>Najlepsze praktyki

Poniżej przedstawiono niektóre najlepsze rozwiązania dotyczące migrowania aplikacji logiki do najnowszej wersji schematu:

* Skopiuj zmigrowany skrypt do nowej aplikacji logiki. Nie zastępuj starej wersji do momentu ukończenia testowania i upewnienia się, że migrowana aplikacja działa zgodnie z oczekiwaniami.

* Przetestuj aplikację logiki **przed** wprowadzeniem do środowiska produkcyjnego.

* Po zakończeniu migracji Zacznij aktualizować Aplikacje logiki, aby używać [zarządzanych interfejsów API](../connectors/apis-list.md) , jeśli jest to możliwe. Na przykład Zacznij korzystać z usługi Dropbox v2 wszędzie tam, gdzie używasz usługi DropBox v1.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [ręcznie migrować Aplikacje logiki](../logic-apps/logic-apps-schema-2015-08-01.md)
