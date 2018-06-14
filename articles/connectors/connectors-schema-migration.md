---
title: Przeprowadzanie migracji aplikacji logiki do wersji schematu 2015-08-01-preview | Microsoft Docs
description: Możesz łatwo przeprowadzić migrację aplikacji logiki do najnowszej wersji schematu. Wystarczy wykonać poniższe czynności.
services: logic-apps
documentationcenter: ''
author: MSFTMAN
manager: erikre
editor: ''
tags: connectors
ms.assetid: 3e177e49-fd69-43e9-9b9b-218abb250c31
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/23/2016
ms.author: deonhe
ms.openlocfilehash: a5a73a9f124e5339b61dbc49021444a208a471f0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "22707128"
---
# <a name="how-to-migrate-logic-apps-to-schema-version-2015-08-01-preview"></a>Przeprowadzanie migracji aplikacji logiki do wersji schematu 2015-08-01-preview
Aby zmienić schemat istniejących aplikacji logiki na nowy, wykonaj następujące czynności:  

1. Otwórz aplikację logiki w witrynie Azure Portal.  
2. Kliknij pozycję Aktualizuj schemat:
   
   ![Ikona interfejsu API][step1]   
   Zostanie wyświetlona strona Aktualizacja schematu z linkiem do dokumentu zawierającego szczegółowe informacje dotyczące ulepszeń w nowym schemacie: ![Ikona interfejsu API][step2]

> [!NOTE]
> Po wybraniu pozycji **Aktualizuj schemat** firma Microsoft automatycznie wykonuje kroki migracji i przekazuje użytkownikowi kod wyjściowy. Korzystając z tych informacji, możesz zaktualizować definicję, jednak należy przestrzegać dobrych praktyk kodowania, takich jak te opisane w sekcji **Najlepsze rozwiązania** poniżej.
> 
> 

## <a name="best-practices-when-migrating-your-logic-apps-to-the-latest-schema-version"></a>Najlepsze rozwiązania w celu przeprowadzania migracji aplikacji logiki do najnowszej wersji schematu:
* Skopiuj zmigrowany skrypt do nowej aplikacji logiki — nie zastępuj starego skryptu do chwili, gdy zakończysz testy i potwierdzisz, że zmigrowana aplikacja działa zgodnie z oczekiwaniami.
* Przetestuj aplikację logiki **przed** użyciem jej w środowisku produkcyjnym.
* Po zakończeniu migracji rozpocznij aktualizowanie aplikacji logiki, aby korzystały z [zarządzanych interfejsów API](apis-list.md), jeżeli jest to możliwe. Na przykład możesz zacząć używać interfejsu DropBox 2 tam, gdzie jest używany interfejs DropBox 1.

## <a name="whats-next"></a>Co dalej
* [Dowiedz się, jak ręcznie migrować aplikacje logiki](../logic-apps/logic-apps-schema-2015-08-01.md)

<!--Icon references-->
[step1]: ./media/connectors-schema-migration/migrateschema1.png
[step2]: ./media/connectors-schema-migration/migrateschema2.png






