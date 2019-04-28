---
title: Łączenie z usługą OneDrive — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Przekazywanie plików i zarządzanie nimi za pomocą interfejsów API REST usługi OneDrive i Azure Logic Apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 10/18/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 840a010f8606387a250552d884621a96d0031f90
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62106231"
---
# <a name="get-started-with-the-onedrive-connector"></a>Rozpoczynanie pracy z łącznikiem usługi OneDrive
Łączenie z usługą OneDrive do zarządzania plikami, w tym przekazywane, pobieranie, usuwanie plików i inne. 

Z usługą OneDrive możesz: 

* Tworzenie przepływu pracy przez przechowywanie plików w usłudze OneDrive lub zaktualizować istniejące pliki w usłudze OneDrive. 
* Używanie wyzwalaczy do uruchomienia przepływu pracy, gdy plik jest tworzony lub aktualizowany w usłudze OneDrive.
* Utwórz plik, usuń plik i nie tylko za pomocą akcji. Na przykład po odebraniu nowej wiadomości e-mail usługi Office 365 z załącznikiem (wyzwalacz) Utwórz nowy plik w usłudze OneDrive (akcję).

W tym artykule przedstawiono sposób korzystania z łącznika usługi OneDrive w aplikacji logiki, a także zawiera wyzwalacze i akcje.

Aby dowiedzieć się więcej o usłudze Logic Apps, zobacz [co to są aplikacje logiki](../logic-apps/logic-apps-overview.md) i [tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-onedrive"></a>Łączenie z usługą OneDrive
Zanim aplikacja logiki może uzyskać dostęp do dowolnej usługi, należy najpierw utworzyć *połączenia* do usługi. Połączenie zapewnia łączność między aplikacją logiki i inną usługę. Na przykład, aby połączyć z usługą OneDrive, musisz najpierw usługi OneDrive *połączenia*. Aby utworzyć połączenie, wprowadź poświadczenia, zwykle używanego do uzyskania dostępu do usługi, którą chcesz połączyć się z. W związku z usługą OneDrive, wprowadź poświadczenia z kontem usługi OneDrive, aby utworzyć połączenie.

### <a name="create-the-connection"></a>Tworzenie połączenia
> [!INCLUDE [Steps to create a connection to OneDrive](../../includes/connectors-create-api-onedrive.md)]
> 
> 

## <a name="use-a-trigger"></a>Użyj wyzwalacza
Wyzwalacz to zdarzenie, który może służyć do uruchamiania tego przepływu, zdefiniowane w aplikacji logiki. Wyzwalacze "sondować" Usługa interwał i częstotliwość, z którą chcesz. [Dowiedz się więcej na temat wyzwalaczy](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. W aplikacji logiki wpisz "usługi onedrive" w celu uzyskania listy z wyzwalaczy:  
   
    ![](./media/connectors-create-api-onedrive/onedrive-1.png)
2. Wybierz **po zmodyfikowaniu pliku**. Jeśli połączenie już istnieje, wybierz przycisk Pokaż narzędzie wyboru, aby wybrać folder.
   
    ![](./media/connectors-create-api-onedrive/sample-folder.png)
   
    Jeśli zostanie wyświetlony monit, aby zalogować się, wprowadź znak szczegóły, aby utworzyć połączenie. [Utwórz połączenie](connectors-create-api-onedrive.md#create-the-connection) w tym artykule przedstawiono kroki. 
   
   > [!NOTE]
   > W tym przykładzie aplikacja logiki jest uruchamiana po pliku w folderze, który wybierzesz zostanie zaktualizowany. Aby zobaczyć wyniki tego wyzwalacza, Dodaj kolejną akcję, która spowoduje wysłanie wiadomości e-mail. Na przykład dodać usługi Office 365 Outlook *Wyślij wiadomość e-mail* akcję, która Ci wiadomości e-mail, gdy plik zostanie zaktualizowany. 

3. Wybierz **Edytuj** przycisk i ustaw **częstotliwość** i **interwał** wartości. Na przykład, jeśli chcesz, aby wyzwalacz, aby wykonać sondowanie co 15 minut, następnie ustawić **częstotliwość** do **minutę**i ustaw **interwał** do **15**. 
   
    ![](./media/connectors-create-api-onedrive/trigger-properties.png)
4. **Zapisz** zmiany (lewym górnym rogu paska narzędzi). Twoja aplikacja logiki jest zapisywana i może zostać automatycznie włączone.

## <a name="use-an-action"></a>Użyj akcji
Akcja jest operacją przeprowadzanych przez przepływ pracy zdefiniowane w aplikacji logiki. [Dowiedz się więcej o akcjach](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. Wybierz znak plus. Zostaną wyświetlone kilka opcji: **Dodaj akcję**, **Dodaj warunek**, lub jeden z **więcej** opcje.
   
    ![](./media/connectors-create-api-onedrive/add-action.png)
2. Wybierz **Dodaj akcję**.
3. W polu tekstowym wpisz "usługi onedrive", aby uzyskać listę dostępnych akcji.
   
    ![](./media/connectors-create-api-onedrive/onedrive-actions.png) 
4. W tym przykładzie wybierz **OneDrive — Utwórz plik**. Jeśli połączenie już istnieje, wybierz **ścieżka folderu** aby umieścić ten plik, wprowadź **nazwy pliku**i wybierz polecenie **zawartość pliku** chcesz:  
   
    ![](./media/connectors-create-api-onedrive/sample-action.png)
   
    Jeśli zostanie wyświetlony monit, aby uzyskać informacje o połączeniu, wprowadź szczegóły, aby utworzyć połączenie. [Utwórz połączenie](connectors-create-api-onedrive.md#create-the-connection) w tym artykule opisano te właściwości. 
   
   > [!NOTE]
   > W tym przykładzie utworzymy nowego pliku w folderze usługi OneDrive. Dane wyjściowe z kolejnego wyzwalacza służy do tworzenia plików usługi OneDrive. Na przykład dodać usługi Office 365 Outlook *po nadejściu nowej wiadomości e-mail* wyzwalacza. Następnie Dodaj usługę OneDrive *Utwórz plik* akcję, która korzysta z typu zawartości i załączników pól w pętli ForEach, aby utworzyć nowy plik w usłudze OneDrive. 
   > 
   > ![](./media/connectors-create-api-onedrive/foreach-action.png)

5. **Zapisz** zmiany (lewym górnym rogu paska narzędzi). Twoja aplikacja logiki jest zapisywana i może zostać automatycznie włączone.


## <a name="connector-specific-details"></a>Szczegóły specyficzne dla łącznika

Wyświetlanie wszystkich wyzwalaczy i akcji zdefiniowanych w strukturze swagger i zobacz też jakiekolwiek ograniczenia w [szczegóły łącznika](/connectors/onedriveconnector/).

## <a name="more-connectors"></a>Więcej łączników
Wróć do [listy interfejsów API](apis-list.md).