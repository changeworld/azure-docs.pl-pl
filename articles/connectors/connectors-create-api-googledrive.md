---
title: Łączenie z dyskiem Google — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Tworzenie i zarządzanie plikami za pomocą interfejsów API REST dysk Google i Azure Logic Apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 11/07/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 43bd5248f1bb80c71a85935c585deac6152be78b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62105093"
---
# <a name="get-started-with-the-google-drive-connector"></a>Rozpoczynanie pracy z łącznikiem usługi dysk Google
Łączenie z dyskiem Google, aby utworzyć pliki, Pobierz wiersze i inne. Z dyskiem Google możesz wykonywać następujące czynności: 

* Tworzenie przepływu biznesowych na podstawie danych, który jest pobierany z usługi wyszukiwania. 
* Za pomocą akcji do wyszukania obrazy, wiadomości i nie tylko. Te akcje uzyskać odpowiedzi, a następnie udostępnić dane wyjściowe do innych działań. Na przykład można wyszukać film wideo, a następnie za pomocą usługi Twitter, Opublikuj wideo do serwisu Twitter.

Możesz rozpocząć od teraz tworzenie aplikacji logiki, zobacz [tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-the-connection-to-google-drive"></a>Utwórz połączenie do usługi dysk Google
Po dodaniu tego łącznika do aplikacji logiki, należy autoryzować logic apps, aby nawiązać połączenie z Twoim dysku Google.

> [!INCLUDE [Steps to create a connection to googledrive](../../includes/connectors-create-api-googledrive.md)]
> 
> 

Po utworzeniu połączenia, możesz wprowadzić właściwości usługi dysk Google, takie jak nazwa pliku lub ścieżki folderu. 

## <a name="connector-specific-details"></a>Szczegóły specyficzne dla łącznika

Wyświetlanie wszystkich wyzwalaczy i akcji zdefiniowanych w strukturze swagger i zobacz też jakiekolwiek ograniczenia w [szczegóły łącznika](/connectors/googledrive/).

## <a name="more-connectors"></a>Więcej łączników
Wróć do [listy interfejsów API](apis-list.md).
