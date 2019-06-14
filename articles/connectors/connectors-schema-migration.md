---
title: Migrowanie aplikacji ze schematem najnowsze — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Jak przeprowadzić migrację aplikacji logiki do najnowszej wersji schematu
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.assetid: 3e177e49-fd69-43e9-9b9b-218abb250c31
ms.topic: article
ms.date: 08/25/2018
ms.openlocfilehash: bf27739bd42106550c18e3bbc27a1ff8b3770747
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60447161"
---
# <a name="migrate-logic-apps-to-latest-schema-version"></a>Migracji aplikacji logiki do najnowszej wersji schematu

Aby przenieść istniejących aplikacji logiki do najnowszej schematu, wykonaj następujące kroki: 

1. W [witryny Azure portal](https://portal.azure.com), Otwórz aplikację logiki w Projektancie aplikacji logiki.

2. W menu aplikacji logiki wybierz **Przegląd**. Na pasku narzędzi wybierz **aktualizacja schematu**.

   > [!NOTE]
   > Po wybraniu **aktualizacja schematu**, Azure Logic Apps, automatycznie uruchamia kroków migracji i udostępnia kod wyjściowy. Te dane wyjściowe służy do aktualizowania definicji aplikacji logiki. Jednak należy się upewnić, stosowanie najlepszych rozwiązań, zgodnie z opisem w następujących **najlepsze praktyki** sekcji.

   ![Aktualizacja schematu](./media/connectors-schema-migration/update-schema.png)

   Strona aktualizacja schematu pojawia się i wyświetla łącze do dokumentu, który opisuje ulepszeń w nowym schemacie.

## <a name="best-practices"></a>Najlepsze praktyki

Poniżej przedstawiono najlepsze rozwiązania dotyczące migracji aplikacji logiki do najnowszej wersji schematu:

* Skopiuj skrypt zmigrowane do nowej aplikacji logiki. Nie zastępuj starej wersji, do momentu ukończenia, testowania i upewnij się, czy zmigrowane aplikacja działa zgodnie z oczekiwaniami.

* Przetestuj aplikację logiki **przed** umieszczenie w środowisku produkcyjnym.

* Po zakończeniu migracji Rozpocznij aktualizowanie aplikacji logiki, aby użyć [zarządzane interfejsy API](../connectors/apis-list.md) gdzie to możliwe. Na przykład zacząć używać interfejsu Dropbox 2 wszędzie, gdzie użycie DropBox 1.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak [ręcznie migrować aplikacje logiki](../logic-apps/logic-apps-schema-2015-08-01.md)
