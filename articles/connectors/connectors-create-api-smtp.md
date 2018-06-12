---
title: Łącznik SMTP w programie Azure Logic Apps | Dokumentacja firmy Microsoft
description: Tworzenie aplikacji logiki z usługi aplikacji Azure. Nawiązać SMTP do wysyłania wiadomości e-mail.
services: logic-apps
documentationcenter: .net,nodejs,java
author: ecfan
manager: jeconnoc
editor: ''
tags: connectors
ms.assetid: d4141c08-88d7-4e59-a757-c06d0dc74300
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/15/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 516110abc1786d99bc719d47d61475cdc2ebcc4b
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35296071"
---
# <a name="get-started-with-the-smtp-connector"></a>Rozpoczynanie pracy z łącznikiem SMTP
Nawiązać SMTP do wysyłania wiadomości e-mail.

Aby użyć [każdy łącznik](apis-list.md), należy najpierw utworzyć aplikację logiki. Możesz rozpocząć pracę przez [teraz tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-smtp"></a>Nawiązać SMTP
Zanim aplikację logiki można uzyskać dostęp do dowolnej usługi, należy najpierw utworzyć *połączenia* do usługi. A [połączenia](connectors-overview.md) udostępnia łączność między aplikacji logiki i innej usługi. Na przykład, aby nawiązać połączenie SMTP, należy najpierw SMTP *połączenia*. Aby utworzyć połączenie, wprowadź poświadczenia, zwykle używanego do uzyskania dostępu do usługi, gdy połączysz się. Tak w tym przykładzie SMTP, wprowadź poświadczenia do połączenia nazwa, adres serwera SMTP i dane logowania użytkownika, aby utworzyć połączenie SMTP.  

### <a name="create-a-connection-to-smtp"></a>Utwórz połączenie SMTP
> [!INCLUDE [Steps to create a connection to SMTP](../../includes/connectors-create-api-smtp.md)]
> 
> 

## <a name="use-an-smtp-trigger"></a>Użyj wyzwalacz SMTP
Wyzwalacz to zdarzenie służy do uruchomienia przepływu pracy zdefiniowanych w aplikacji logiki. [Dowiedz się więcej o wyzwalaczy](../logic-apps/logic-apps-overview.md#logic-app-concepts).

W tym przykładzie SMTP nie ma wyzwalacz własnych. Tak, użyj **Salesforce — po utworzeniu obiektu** wyzwalacza. Wyzwalacz aktywuje po utworzeniu nowego obiektu w usłudze Salesforce. Na przykład utworzono go tak, aby zawsze nowego potencjalnego klienta jest tworzone w usłudze Salesforce, *wysyłania wiadomości e-mail* akcja jest wykonywana za pomocą łącznika SMTP powiadomienie potencjalnego nowego klienta tworzona.

1. Wprowadź *salesforce* w polu wyszukiwania w Projektancie aplikacji logiki wybiorą **Salesforce — po utworzeniu obiektu** wyzwalacza.  
   ![](../../includes/media/connectors-create-api-salesforce/trigger-1.png)  
2. **Podczas tworzenia obiektu** formant jest wyświetlany.
   ![](../../includes/media/connectors-create-api-salesforce/trigger-2.png)  
3. Wybierz **typ obiektu** następnie wybierz *prowadzić* z listy obiektów. W tym kroku powoduje utworzenie wyzwalacz, który powiadamia aplikację logiki, zawsze, gdy nowy realizacji jest tworzony w usłudze Salesforce.  
   ![](../../includes/media/connectors-create-api-salesforce/trigger3.png)  
4. Wyzwalacz został utworzony.  
   ![](../../includes/media/connectors-create-api-salesforce/trigger-4.png)  

## <a name="use-an-smtp-action"></a>Za pomocą akcji SMTP
Akcja jest przeprowadzane przez przepływ pracy zdefiniowanych w aplikacji logiki operacji. [Dowiedz się więcej o akcjach](../logic-apps/logic-apps-overview.md#logic-app-concepts).

Teraz, wyzwalacz został dodany, użyj następujące kroki, aby dodać akcję SMTP, która występuje podczas tworzenia nowego potencjalnego klienta w usłudze Salesforce.

1. Wybierz **+ nowy krok** Aby dodać akcję chcesz wykonać po utworzeniu nowego potencjalnego klienta.  
   ![](../../includes/media/connectors-create-api-salesforce/trigger4.png)  
2. Wybierz **Dodaj akcję**. Służy to pole wyszukiwania, gdzie możesz wyszukać dowolną akcję użytkownik chce przejąć.  
   ![](../../includes/media/connectors-create-api-smtp/using-smtp-action-2.png)  
3. Wprowadź *smtp* do wyszukania akcji związanych z SMTP.  
4. Wybierz **SMTP — Wyślij wiadomość E-mail** jako akcję wykonywaną, gdy jest tworzony nowy potencjalnych klientów. Zostanie otwarty blok kontroli akcji. Należy ustanowić połączenie smtp w bloku projektanta, jeśli nie zostało zrobione to wcześniej.  
   ![](../../includes/media/connectors-create-api-smtp/smtp-2.png)    
5. Wprowadź informacje żądane poczty e-mail w **SMTP — Wyślij wiadomość E-mail** bloku.  
   ![](../../includes/media/connectors-create-api-smtp/using-smtp-action-4.PNG)  
6. Zapisz swoją pracę, aby aktywować przepływ pracy.  

## <a name="connector-specific-details"></a>Szczegóły dotyczące łącznika

Wyświetl wszystkie wyzwalacze i akcje zdefiniowane w swagger i zobacz też żadnych limitów w [szczegóły łącznika](/connectors/smtpconnector/).

## <a name="more-connectors"></a>Więcej łączników
Wróć do [listy interfejsów API](apis-list.md).