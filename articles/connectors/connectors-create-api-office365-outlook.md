---
title: Łączenie z usługą Office 365 Outlook — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Zarządzanie poczty e-mail, kontaktów i kalendarzy z interfejsów API REST usługi Office 365 i Azure Logic Apps
author: ecfan
manager: jeconnoc
ms.author: v-yiso
origin.date: 10/18/2016
ms.date: 09/03/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 52abf17e869216e65780129a7b48df79bd79f67a
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62105058"
---
# <a name="get-started-with-the-office-365-outlook-connector"></a>Rozpoczynanie pracy z łącznikiem usługi Office 365 Outlook
Łącznik usługi Office 365 Outlook umożliwia interakcję z programem Outlook w usłudze Office 365. Aby utworzyć, edytować, aktualizować kontakty i elementy kalendarza i również uzyskać, wysyłanie i Odpowiedz na wiadomość e-mail, należy użyć tego łącznika.

Za pomocą usługi Office 365 Outlook możesz:

* Tworzenie przepływu pracy przy użyciu funkcji poczty e-mail i kalendarza w usłudze Office 365. 
* Używanie wyzwalaczy do uruchomienia przepływu pracy po nowej wiadomości e-mail, gdy element kalendarza zostanie zaktualizowany i nie tylko.
* Aby wysłać wiadomość e-mail, należy utworzyć nowe zdarzenie w kalendarzu i nie tylko za pomocą akcji. Na przykład jeśli nowy obiekt w usłudze Salesforce (wyzwalacz), Wyślij wiadomość e-mail do usługi Office 365 Outlook (akcję). 

W tym artykule przedstawiono sposób korzystania z łącznika usługi Office 365 Outlook w aplikacji logiki, a także zawiera wyzwalacze i akcje.

> [!NOTE]
> Ta wersja artykułu ma zastosowanie do aplikacji logiki ogólnodostępnej (GA).
> 
> 

Aby dowiedzieć się więcej o usłudze Logic Apps, zobacz [co to są aplikacje logiki](../logic-apps/logic-apps-overview.md) i [tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-office-365"></a>Łączenie z usługą Office 365
Zanim aplikacja logiki może uzyskać dostęp do dowolnej usługi, należy najpierw utworzyć *połączenia* do usługi. Połączenie zapewnia łączność między aplikacją logiki i inną usługę. Na przykład, aby połączyć z usługą Office 365 Outlook, musisz najpierw z usługi Office 365 *połączenia*. Aby utworzyć połączenie, wprowadź poświadczenia, zwykle używanego do uzyskania dostępu do usługi, którą chcesz połączyć się z. Dlatego za pomocą usługi Office 365 Outlook, wprowadź poświadczenia konta usługi Office 365, aby utworzyć połączenie.

## <a name="create-the-connection"></a>Tworzenie połączenia
> [!INCLUDE [Steps to create a connection to Office 365](../../includes/connectors-create-api-office365-outlook.md)]
> 
> 

## <a name="use-a-trigger"></a>Użyj wyzwalacza
Wyzwalacz to zdarzenie, który może służyć do uruchamiania tego przepływu, zdefiniowane w aplikacji logiki. Wyzwalacze "sondować" Usługa interwał i częstotliwość, z którą chcesz. [Dowiedz się więcej na temat wyzwalaczy](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. W aplikacji logiki wpisz "office 365" w celu uzyskania listy z wyzwalaczy:  
   
    ![](./media/connectors-create-api-office365-outlook/office365-trigger.png)
2. Wybierz **Office 365 Outlook — gdy nadchodzące zdarzenie ma wkrótce się rozpocząć**. Jeśli połączenie już istnieje, wybierz kalendarz, z listy rozwijanej.
   
    ![](./media/connectors-create-api-office365-outlook/sample-calendar.png)
   
    Jeśli zostanie wyświetlony monit, aby zalogować się, wprowadź znak szczegóły, aby utworzyć połączenie. [Utwórz połączenie](connectors-create-api-office365-outlook.md#create-the-connection) w tym temacie przedstawiono kroki. 
   
   > [!NOTE]
   > W tym przykładzie aplikacja logiki jest uruchamiany, gdy zdarzenie w kalendarzu zostanie zaktualizowany. Aby zobaczyć wyniki tego wyzwalacza, Dodaj kolejną akcję, która wyśle do Ciebie wiadomość SMS. Na przykład dodać usługi Twilio *Wyślij wiadomość* akcji tego teksty gdy zdarzenie kalendarza jest uruchamiana w ciągu 15 minut. 
   > 
   > 
3. Wybierz **Edytuj** przycisk i ustaw **częstotliwość** i **interwał** wartości. Na przykład, jeśli chcesz, aby wyzwalacz, aby wykonać sondowanie co 15 minut, następnie ustawić **częstotliwość** do **minutę**i ustaw **interwał** do **15**. 
   
    ![](./media/connectors-create-api-office365-outlook/calendar-settings.png)
4. **Zapisz** zmiany (lewym górnym rogu paska narzędzi). Twoja aplikacja logiki jest zapisywana i może zostać automatycznie włączone.

## <a name="use-an-action"></a>Użyj akcji
Akcja jest operacją przeprowadzanych przez przepływ pracy zdefiniowane w aplikacji logiki. [Dowiedz się więcej o akcjach](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. Wybierz znak plus. Zostaną wyświetlone kilka opcji: **Dodaj akcję**, **Dodaj warunek**, lub jeden z **więcej** opcje.
   
    ![](./media/connectors-create-api-office365-outlook/add-action.png)
2. Wybierz **Dodaj akcję**.
3. W polu tekstowym wpisz "office 365" Aby uzyskać listę dostępnych akcji.
   
    ![](./media/connectors-create-api-office365-outlook/office365-actions.png) 
4. W tym przykładzie wybierz **Office 365 Outlook — Tworzenie kontaktu**. Jeśli połączenie już istnieje, wybierz **identyfikator folderu**, **imię**i inne właściwości:  
   
    ![](./media/connectors-create-api-office365-outlook/office365-sampleaction.png)
   
    Jeśli zostanie wyświetlony monit, aby uzyskać informacje o połączeniu, wprowadź szczegóły, aby utworzyć połączenie. [Utwórz połączenie](connectors-create-api-office365-outlook.md#create-the-connection) w tym temacie opisano te właściwości. 
   
   > [!NOTE]
   > W tym przykładzie zostanie utworzony nowy kontakt w usłudze Office 365 Outlook. Dane wyjściowe z kolejnego wyzwalacza umożliwia tworzenie kontaktu. Na przykład dodać SalesForce *po utworzeniu obiektu* wyzwalacza. Następnie dodaj usługi Office 365 Outlook *Utwórz kontakt* akcję, która korzysta z pól usługi SalesForce, aby utworzyć nowy kontakt w usłudze Office 365. 
   > 
   > 
5. **Zapisz** zmiany (lewym górnym rogu paska narzędzi). Twoja aplikacja logiki jest zapisywana i może zostać automatycznie włączone.

## <a name="connector-specific-details"></a>Szczegóły specyficzne dla łącznika

Wyświetlanie wszystkich wyzwalaczy i akcji zdefiniowanych w strukturze swagger i zobacz też jakiekolwiek ograniczenia w [szczegóły łącznika](/connectors/office365connector/). 

## <a name="next-steps"></a>Następne kroki
[Tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Zapoznaj się z innych dostępnych łączników w usłudze Logic Apps w naszym [listy interfejsów API](apis-list.md).

