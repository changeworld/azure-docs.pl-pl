---
title: Łączenie z usługą Dropbox — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Przekazywanie plików i zarządzanie nimi za pomocą interfejsów API REST usługi Dropbox i Azure Logic Apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 07/15/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 256a0b34d5050e17abe5bb98ca0c13ab0b61787e
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43094442"
---
# <a name="get-started-with-the-dropbox-connector"></a>Rozpoczynanie pracy z łącznikiem usługi Dropbox
Połączyć z usługą Dropbox, aby zarządzać plikami. Można wykonywać różne akcje, takie jak przekazywanie, aktualizowanie, pobieranie i usuwać pliki w usłudze Dropbox.

Aby użyć [każdy łącznik](apis-list.md), najpierw musisz utworzyć aplikację logiki. Możesz rozpocząć od [teraz tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-dropbox"></a>Łączenie z usługą Dropbox
Zanim aplikacja logiki może uzyskać dostęp do dowolnej usługi, należy najpierw utworzyć *połączenia* do usługi. Połączenie zapewnia łączność między aplikacją logiki i inną usługę. Na przykład, aby nawiązać połączenie usługi Dropbox, należy najpierw Dropbox *połączenia*. Aby utworzyć połączenie, należałoby o podanie poświadczeń, zwykle używanego do uzyskania dostępu do usługi, którą chcesz nawiązać połączenie z. Tak w tym przykładzie Dropbox będziesz potrzebować poświadczenia do Twojego konta usługi Dropbox aby można było utworzyć połączenie do usługi Dropbox. 

### <a name="create-a-connection-to-dropbox"></a>Utwórz połączenie usługi Dropbox
> [!INCLUDE [Steps to create a connection to Dropbox](../../includes/connectors-create-api-dropbox.md)]
> 
> 

## <a name="use-a-dropbox-trigger"></a>Użyj wyzwalacza usługi Dropbox
Wyzwalacz to zdarzenie, który może służyć do uruchamiania tego przepływu, zdefiniowane w aplikacji logiki. [Dowiedz się więcej na temat wyzwalaczy](../logic-apps/logic-apps-overview.md#logic-app-concepts).

W tym przykładzie użyjemy **po utworzeniu pliku** wyzwalacza. W przypadku wystąpienia tego wyzwalacza będzie przez nas wywoływany **Pobierz zawartość pliku przy użyciu ścieżki** akcję dotyczącą usługi Dropbox. 

1. Wprowadź *dropbox* w polu wyszukiwania w Projektancie aplikacji logiki, a następnie zaznacz **Dropbox — po utworzeniu pliku** wyzwalacza.      
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-trigger.PNG)  
2. Wybierz folder, w którym chcesz ją śledzić tworzenia pliku. Wybierz... (identyfikowanych czerwonym prostokątem) i przejdź do folderu, wybieraniu dla wejściowych wyzwalacza.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-trigger-2.PNG)  

## <a name="use-a-dropbox-action"></a>Użyj akcję dotyczącą usługi Dropbox
Akcja jest operacją przeprowadzanych przez przepływ pracy zdefiniowane w aplikacji logiki. [Dowiedz się więcej o akcjach](../logic-apps/logic-apps-overview.md#logic-app-concepts).

Teraz, że wyzwalacz został dodany, wykonaj następujące kroki, aby dodać akcję, która pobierze zawartość nowego pliku.

1. Wybierz **+ nowy krok** Aby dodać akcję chcesz wykonać, gdy tworzony jest nowy plik.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action.PNG)
2. Wybierz **Dodaj akcję**. To otwiera, które pola wyszukiwania, w którym można wyszukać dowolną akcję należy podjąć.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-2.PNG)
3. Wprowadź *dropbox* wyszukiwania akcji związanych z usługi Dropbox.  
4. Wybierz **Dropbox — Pobierz zawartość pliku przy użyciu ścieżki** jako akcję do wykonania, gdy tworzony jest nowy plik w wybranym folderze usługi Dropbox. Zostanie otwarty blok sterowania akcji. Zostanie wyświetlony monit Autoryzuj aplikację logiki, aby uzyskać dostęp do konta usługi Dropbox, jeśli użytkownik nie zostało zrobione wcześniej.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-3.PNG)  
5. Wybierz... (znajdujący się w prawej części **ścieżka pliku** kontrolki) i przejdź do ścieżki pliku, czy chcesz użyć. Możesz też korzystać z **ścieżka pliku** tokenu, aby przyspieszyć tworzenie aplikacji usługi Logic Apps.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-4.PNG)  
6. Zapisz swoją pracę i Utwórz nowy plik w usłudze Dropbox, aby uaktywnić Twój przepływ pracy.  

## <a name="connector-specific-details"></a>Szczegóły specyficzne dla łącznika

Wyświetlanie wszystkich wyzwalaczy i akcji zdefiniowanych w strukturze swagger i zobacz też jakiekolwiek ograniczenia w [szczegóły łącznika](/connectors/dropbox/).

## <a name="more-connectors"></a>Więcej łączników
Wróć do [listy interfejsów API](apis-list.md).
