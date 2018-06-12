---
title: Nawiązać Dropbox — aplikacje logiki platformy Azure | Dokumentacja firmy Microsoft
description: Przekazywanie plików i zarządzania nimi z interfejsów API REST usługi Dropbox i usługi Azure Logic Apps
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
ms.openlocfilehash: 77203788a6329ed4c5b58419fbcf48a48da91b30
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295051"
---
# <a name="get-started-with-the-dropbox-connector"></a>Rozpoczynanie pracy z łącznikiem usługi Dropbox
Podłącz do skrzynki do zarządzania plikami. Można wykonywać różne akcje, takie jak przekazywanie, zaktualizować, Pobierz i usuwania plików skrzynki.

Aby użyć [każdy łącznik](apis-list.md), należy najpierw utworzyć aplikację logiki. Możesz rozpocząć pracę przez [teraz tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-dropbox"></a>Nawiązać skrzynki
Zanim aplikację logiki można uzyskać dostęp do dowolnej usługi, należy najpierw utworzyć *połączenia* do usługi. Połączenie stanowi połączenie między aplikacji logiki i innej usługi. Na przykład, aby nawiązać połączenie usługi Dropbox, należy najpierw Dropbox *połączenia*. Aby utworzyć połączenie, musisz podać poświadczenia, które zwykle jest używana do uzyskania dostępu do usługi, który chcesz połączyć się z. Tak w tym przykładzie skrzynki konieczne będzie poświadczenia konta Dropbox, aby utworzyć połączenie usługi Dropbox. [Dowiedz się więcej na temat połączenia]()

### <a name="create-a-connection-to-dropbox"></a>Utwórz połączenie usługi Dropbox
> [!INCLUDE [Steps to create a connection to Dropbox](../../includes/connectors-create-api-dropbox.md)]
> 
> 

## <a name="use-a-dropbox-trigger"></a>Użyj wyzwalacz skrzynki
Wyzwalacz to zdarzenie służy do uruchomienia przepływu pracy zdefiniowanych w aplikacji logiki. [Dowiedz się więcej o wyzwalaczy](../logic-apps/logic-apps-overview.md#logic-app-concepts).

W tym przykładzie używamy **podczas tworzenia pliku** wyzwalacza. W przypadku wystąpienia tego wyzwalacza będzie nazywamy **pobieranie plików zawartości przy użyciu ścieżki** skrzynki akcji. 

1. Wprowadź *dropbox* w polu wyszukiwania w Projektancie aplikacji logiki, następnie wybierz **Dropbox — po utworzeniu pliku** wyzwalacza.      
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-trigger.PNG)  
2. Wybierz folder, w którym mają być śledzone tworzenia pliku. Wybierz... (określone w czerwonym prostokątem), a następnie przejdź do folderu, który chcesz wybrać w odniesieniu do danych wejściowych wyzwalacza.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-trigger-2.PNG)  

## <a name="use-a-dropbox-action"></a>Za pomocą akcji skrzynki
Akcja jest przeprowadzane przez przepływ pracy zdefiniowanych w aplikacji logiki operacji. [Dowiedz się więcej o akcjach](../logic-apps/logic-apps-overview.md#logic-app-concepts).

Teraz, wyzwalacz został dodany, wykonaj następujące kroki, aby dodać akcję, która zostanie wyświetlony nowy plik zawartości.

1. Wybierz **+ nowy krok** Aby dodać akcję chcesz wykonać, gdy zostanie utworzony nowy plik.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action.PNG)
2. Wybierz **Dodaj akcję**. Służy to pole wyszukiwania, gdzie możesz wyszukać dowolną akcję użytkownik chce przejąć.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-2.PNG)
3. Wprowadź *dropbox* do wyszukania akcji związanych z Dropbox.  
4. Wybierz **Dropbox - pobrania zawartości pliku przy użyciu ścieżki** jako akcję wykonywaną, gdy nowy plik jest tworzony w wybranym folderze Dropbox. Zostanie otwarty blok kontroli akcji. Pojawi się monit o autoryzowanie aplikację logiki, aby uzyskać dostęp do konta Dropbox, jeśli nie zostało zrobione to wcześniej.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-3.PNG)  
5. Wybierz... (znajdujący się w prawej części **ścieżka pliku** sterowania) i przejdź do ścieżki pliku chcesz użyć. Lub użyj **ścieżka pliku** tokenu, aby przyspieszyć tworzenie aplikacji sieci logiczne.  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-4.PNG)  
6. Zapisz swoją pracę i Utwórz nowy plik w Dropbox, aby aktywować przepływ pracy.  

## <a name="connector-specific-details"></a>Szczegóły dotyczące łącznika

Wyświetl wszystkie wyzwalacze i akcje zdefiniowane w swagger i zobacz też żadnych limitów w [szczegóły łącznika](/connectors/dropbox/).

## <a name="more-connectors"></a>Więcej łączników
Wróć do [listy interfejsów API](apis-list.md).