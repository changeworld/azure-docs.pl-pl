---
title: Migrowanie aplikacji do najnowszego schematu
description: Jak migrować definicje JSON przepływu pracy aplikacji logiki do najnowszej wersji języka definicji przepływu pracy
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
ms.openlocfilehash: cef0fcb990cd2c5c6583822d4dc4c6993c52eac2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75666792"
---
# <a name="migrate-logic-apps-to-latest-schema-version"></a>Migrowanie aplikacji logiki do najnowszej wersji schematu

Aby przenieść istniejące aplikacje logiki do najnowszego schematu, wykonaj następujące kroki: 

1. W [witrynie Azure portal](https://portal.azure.com)otwórz aplikację logiki w projektancie aplikacji logiki.

2. W menu aplikacji logiki wybierz polecenie **Przegląd**. Na pasku narzędzi wybierz pozycję **Aktualizuj schemat**.

   > [!NOTE]
   > Po **wybraniu opcji Aktualizuj schemat**usługa Azure Logic Apps automatycznie uruchamia kroki migracji i udostępnia dane wyjściowe kodu. Można użyć tego danych wyjściowych do aktualizowania definicji aplikacji logiki. Upewnij się jednak, że należy postępować zgodnie z najlepszymi rozwiązaniami opisanymi w poniższej sekcji **Najważniejsze wskazówki.**

   ![Aktualizuj schemat](./media/connectors-schema-migration/update-schema.png)

   Zostanie wyświetlona strona Aktualizuj schemat i zostanie wyświetlona łącze do dokumentu opisującego ulepszenia nowego schematu.

## <a name="best-practices"></a>Najlepsze rozwiązania

Oto kilka najlepszych rozwiązań dotyczących migracji aplikacji logiki do najnowszej wersji schematu:

* Skopiuj zmigrowany skrypt do nowej aplikacji logiki. Nie zastępuj starej wersji, dopóki nie zakończysz testów i nie potwierdź, że zmigrowana aplikacja działa zgodnie z oczekiwaniami.

* Przetestuj aplikację logiki **przed** wprowadzeniem do produkcji.

* Po zakończeniu migracji należy rozpocząć aktualizowanie aplikacji logiki, aby w miarę możliwości używać [zarządzanych interfejsów API.](../connectors/apis-list.md) Na przykład zacznij używać Dropbox v2 wszędzie tam, gdzie używasz DropBox v1.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [ręcznie migrować aplikacje logiki](../logic-apps/logic-apps-schema-2015-08-01.md)
