---
title: Nawiązywanie połączenia z pakietem Office 365 Outlook
description: Zarządzanie pocztą e-mail, kontaktami i kalendarzami przy użyciu interfejsów API REST pakietu Office 365 i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/18/2016
tags: connectors
ms.openlocfilehash: 858366947fe21a20d6f112fc51899d1533a36472
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789622"
---
# <a name="get-started-with-the-office-365-outlook-connector"></a>Wprowadzenie do łącznika programu Outlook pakietu Office 365
Łącznik Office 365 Outlook umożliwia interakcję z programem Outlook w pakiecie Office 365. Użyj tego łącznika, aby tworzyć, edytować i aktualizować kontakty i elementy kalendarza, a także uzyskiwać, wysyłać i odpowiadać na wiadomości e-mail.

Za pomocą pakietu Office 365 Outlook:

* Kompilowanie przepływu pracy za pomocą funkcji poczty e-mail i kalendarza w pakiecie Office 365. 
* Użyj wyzwalaczy, aby uruchomić przepływ pracy w przypadku nowej wiadomości e-mail, gdy element kalendarza zostanie zaktualizowany i nie tylko.
* Użyj akcji do wysyłania wiadomości e-mail, tworzenia nowego zdarzenia kalendarza i innych. Na przykład jeśli w usłudze Salesforce (wyzwalacz) istnieje nowy obiekt, Wyślij wiadomość e-mail do programu Office 365 Outlook (akcję). 

W tym artykule pokazano, jak używać łącznika Office 365 Outlook w aplikacji logiki, a także wyświetlać wyzwalacze i akcje.

> [!NOTE]
> Ta wersja artykułu ma zastosowanie do Logic Apps ogólnej dostępności.
> 
> 

Aby dowiedzieć się więcej na temat Logic Apps, zobacz [co to są aplikacje logiki](../logic-apps/logic-apps-overview.md) i [Utwórz aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-office-365"></a>Łączenie z usługą Office 365
Zanim aplikacja logiki będzie mogła uzyskać dostęp do dowolnej usługi, należy najpierw utworzyć *połączenie* z usługą. Połączenie zapewnia łączność między aplikacją logiki a inną usługą. Na przykład, aby nawiązać połączenie z pakietem Office 365 Outlook, najpierw musisz mieć *połączenie*z pakietem Office 365. Aby utworzyć połączenie, wprowadź poświadczenia, które zwykle są używane w celu uzyskania dostępu do usługi, z którą chcesz nawiązać połączenie. Tak jak w przypadku pakietu Office 365 Outlook, wprowadź poświadczenia do konta pakietu Office 365, aby utworzyć połączenie.

## <a name="create-the-connection"></a>Tworzenie połączenia
> [!INCLUDE [Steps to create a connection to Office 365](../../includes/connectors-create-api-office365-outlook.md)]
> 
> 

## <a name="use-a-trigger"></a>Użyj wyzwalacza
Wyzwalacz to zdarzenie, którego można użyć do uruchomienia przepływu pracy zdefiniowanego w aplikacji logiki. Wyzwala "sondowanie" usługi z interwałem i częstotliwością. [Dowiedz się więcej o wyzwalaczach](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. W aplikacji logiki wpisz "Office 365", aby uzyskać listę wyzwalaczy:  
   
    ![](./media/connectors-create-api-office365-outlook/office365-trigger.png)
2. Wybierz pozycję **Office 365 Outlook — gdy wkrótce zostanie uruchomione nadchodzące wydarzenie**. Jeśli połączenie już istnieje, wybierz kalendarz z listy rozwijanej.
   
    ![](./media/connectors-create-api-office365-outlook/sample-calendar.png)
   
    Jeśli zostanie wyświetlony monit o zalogowanie się, wprowadź szczegóły logowania, aby utworzyć połączenie. [Utwórz połączenie](connectors-create-api-office365-outlook.md#create-the-connection) w tym temacie, aby uzyskać listę kroków. 
   
   > [!NOTE]
   > W tym przykładzie aplikacja logiki jest uruchamiana po zaktualizowaniu zdarzenia kalendarza. Aby wyświetlić wyniki tego wyzwalacza, Dodaj kolejną akcję, która wysyła wiadomość SMS. Na przykład Dodaj akcję Twilio *Wyślij wiadomość* , która zawiera tekst, gdy zdarzenie kalendarza zostanie uruchomione w ciągu 15 minut. 
   > 
   > 
3. Wybierz przycisk **Edytuj** i ustaw wartości parametrów **częstotliwość** i **Interwał** . Na przykład jeśli wyzwalacz ma sondować co 15 minut, należy ustawić **częstotliwość** na **minutę**i ustawić **Interwał** na **15**. 
   
    ![](./media/connectors-create-api-office365-outlook/calendar-settings.png)
4. **Zapisz** zmiany (w lewym górnym rogu paska narzędzi). Aplikacja logiki jest zapisywana i może być automatycznie włączona.

## <a name="use-an-action"></a>Korzystanie z akcji
Akcja jest operacją wykonywaną przez przepływ pracy zdefiniowany w aplikacji logiki. [Dowiedz się więcej o akcjach](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. Wybierz znak plus. Zobaczysz kilka opcji: **Dodaj akcję**, **Dodaj warunek**lub jedną z opcji **więcej** .
   
    ![](./media/connectors-create-api-office365-outlook/add-action.png)
2. Wybierz pozycję **Dodaj akcję**.
3. W polu tekstowym wpisz "Office 365", aby uzyskać listę wszystkich dostępnych akcji.
   
    ![](./media/connectors-create-api-office365-outlook/office365-actions.png) 
4. W naszym przykładzie wybierz pozycję **Office 365 Outlook — utwórz kontakt**. Jeśli połączenie już istnieje, wybierz **Identyfikator folderu**, **podaną nazwę**i inne właściwości:  
   
    ![](./media/connectors-create-api-office365-outlook/office365-sampleaction.png)
   
    Jeśli zostanie wyświetlony monit o podanie informacji o połączeniu, wprowadź szczegóły, aby utworzyć połączenie. [Utwórz połączenie](connectors-create-api-office365-outlook.md#create-the-connection) w tym temacie opisuje te właściwości. 
   
   > [!NOTE]
   > W tym przykładzie tworzymy nowy kontakt w pakiecie Office 365 Outlook. Możesz użyć danych wyjściowych z innego wyzwalacza, aby utworzyć kontakt. Na przykład Dodaj aplikację SalesForce, *gdy zostanie utworzony wyzwalacz obiektu* . Następnie Dodaj akcję *Utwórz kontakt* programu Outlook w usłudze Office 365, która używa pól usługi Salesforce do utworzenia nowego kontaktu w pakiecie Office 365. 
   > 
   > 
5. **Zapisz** zmiany (w lewym górnym rogu paska narzędzi). Aplikacja logiki jest zapisywana i może być automatycznie włączona.

## <a name="connector-specific-details"></a>Szczegóły dotyczące łącznika

Wyświetlanie wszystkich wyzwalaczy i akcji zdefiniowanych w strukturze Swagger, a także wszystkich ograniczeń w [szczegółach łącznika](/connectors/office365connector/). 

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md)