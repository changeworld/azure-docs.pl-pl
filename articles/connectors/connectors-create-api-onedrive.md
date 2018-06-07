---
title: Połączenie do usługi OneDrive — aplikacje logiki platformy Azure | Dokumentacja firmy Microsoft
description: Przekazywanie plików i zarządzania nimi z interfejsów API REST usługi OneDrive i usługi Azure Logic Apps
author: ecfan
manager: cfowler
ms.author: estfan
ms.date: 10/18/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 12df6efc21be30f1c42fa7db98823908ae0f5964
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34610059"
---
# <a name="get-started-with-the-onedrive-connector"></a>Rozpoczynanie pracy z łącznikiem usługi OneDrive
Połączenie do usługi OneDrive zarządzania plikami, w tym przekazywane, get, usuwanie plików i inne. 

Z usługą OneDrive możesz: 

* Tworzenie przepływu pracy przez zapisanie plików w usłudze OneDrive lub zaktualizować istniejące pliki w usłudze OneDrive. 
* Wyzwalacze są używane do uruchomienia przepływu pracy, gdy plik jest tworzony lub aktualizowany w aplikacji OneDrive.
* Użyj działania do utworzenia pliku, usuń plik i inne. Na przykład po odebraniu nowej usługi Office 365 wiadomości e-mail z załącznikiem (wyzwalacz) Utwórz nowy plik w usłudze OneDrive (działanie).

W tym artykule przedstawiono sposób korzystania z łącznika usługi OneDrive w aplikacji logiki, a także zawiera wyzwalacze i akcje.

Aby dowiedzieć się więcej na temat aplikacji logiki, zobacz [co to jest logic apps](../logic-apps/logic-apps-overview.md) i [tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-onedrive"></a>Połączenie do usługi OneDrive
Zanim aplikację logiki można uzyskać dostęp do dowolnej usługi, należy najpierw utworzyć *połączenia* do usługi. Połączenie stanowi połączenie między aplikacji logiki i innej usługi. Na przykład, aby nawiązać połączenie usługi OneDrive, należy najpierw OneDrive *połączenia*. Aby utworzyć połączenie, wprowadź poświadczenia, zwykle używanego do uzyskania dostępu do usługi, który chcesz połączyć się z. Tak z usługą OneDrive, wprowadź poświadczenia z kontem usługi OneDrive, aby utworzyć połączenie.

### <a name="create-the-connection"></a>Tworzenie połączenia
> [!INCLUDE [Steps to create a connection to OneDrive](../../includes/connectors-create-api-onedrive.md)]
> 
> 

## <a name="use-a-trigger"></a>Użyć wyzwalacza
Wyzwalacz to zdarzenie służy do uruchomienia przepływu pracy zdefiniowanych w aplikacji logiki. Wyzwalacze "sondować" Usługa interwału i częstotliwość. [Dowiedz się więcej o wyzwalaczy](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. W aplikacji logiki wpisz "onedrive" w celu uzyskania listy wyzwalaczy:  
   
    ![](./media/connectors-create-api-onedrive/onedrive-1.png)
2. Wybierz **modyfikacji pliku**. Jeśli połączenie już istnieje, następnie wybierz przycisk Pokaż selektora, aby wybrać folder.
   
    ![](./media/connectors-create-api-onedrive/sample-folder.png)
   
    Po pojawieniu się zalogować, wprowadź znak w szczegóły, aby utworzyć połączenie. [Utwórz połączenie](connectors-create-api-onedrive.md#create-the-connection) w tym artykule przedstawiono kroki. 
   
   > [!NOTE]
   > W tym przykładzie aplikacji logiki uruchamiane po pliku w folderze wybranej aktualizacji. Aby wyświetlić wyniki tego wyzwalacza, Dodaj inną akcję wysyłanej wiadomości e-mail. Na przykład dodać Office 365 Outlook *wysłać wiadomość e-mail* akcję, która wysyła pocztą e-mail, należy po zaktualizowaniu pliku. 

3. Wybierz **Edytuj** przycisk i ustaw **częstotliwość** i **interwał** wartości. Na przykład, jeśli chcesz wyzwalacza do sondowania co 15 minut, następnie ustawić **częstotliwość** do **minutę**i ustaw **interwał** do **15**. 
   
    ![](./media/connectors-create-api-onedrive/trigger-properties.png)
4. **Zapisz** zmiany (lewym górnym rogu paska narzędzi). Aplikację logiki jest zapisywana i automatycznie włączone.

## <a name="use-an-action"></a>Za pomocą akcji
Akcja jest przeprowadzane przez przepływ pracy zdefiniowanych w aplikacji logiki operacji. [Dowiedz się więcej o akcjach](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. Wybierz znak plus. Zobacz kilka opcji: **Dodaj akcję**, **Dodaj warunek**, lub jeden z **więcej** opcje.
   
    ![](./media/connectors-create-api-onedrive/add-action.png)
2. Wybierz **Dodaj akcję**.
3. W polu tekstowym wpisz "onedrive", aby uzyskać listę dostępnych akcji.
   
    ![](./media/connectors-create-api-onedrive/onedrive-actions.png) 
4. W tym przykładzie wybierz **OneDrive — Utwórz plik**. Jeśli połączenie już istnieje, następnie wybierz **ścieżka folderu** umieścić pliku, wprowadź **nazwę pliku**i wybierz polecenie **zawartość pliku** ma:  
   
    ![](./media/connectors-create-api-onedrive/sample-action.png)
   
    Jeśli zostanie wyświetlony monit o informacje dotyczące połączenia, wprowadź szczegóły, aby utworzyć połączenie. [Utwórz połączenie](connectors-create-api-onedrive.md#create-the-connection) w tym artykule opisano te właściwości. 
   
   > [!NOTE]
   > W tym przykładzie utworzymy nowy plik w folderze OneDrive. Dane wyjściowe z innego wyzwalacza służy do tworzenia plików usługi OneDrive. Na przykład dodać Office 365 Outlook *po odebraniu nowej wiadomości e-mail* wyzwalacza. Następnie dodaj OneDrive *Utwórz plik* akcję, która używa typu zawartości i załączników pola w obrębie ForEach do utworzenia nowego pliku w usłudze OneDrive. 
   > 
   > ![](./media/connectors-create-api-onedrive/foreach-action.png)

5. **Zapisz** zmiany (lewym górnym rogu paska narzędzi). Aplikację logiki jest zapisywana i automatycznie włączone.


## <a name="connector-specific-details"></a>Szczegóły dotyczące łącznika

Wyświetl wszystkie wyzwalacze i akcje zdefiniowane w swagger i zobacz też żadnych limitów w [szczegóły łącznika](/connectors/onedriveconnector/).

## <a name="more-connectors"></a>Więcej łączników
Wróć do [listy interfejsów API](apis-list.md).