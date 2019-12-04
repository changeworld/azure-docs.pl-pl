---
title: Nawiązywanie połączenia z usługą OneDrive
description: Przekazywanie plików i zarządzanie nimi za pomocą interfejsów API REST usługi OneDrive i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/18/2016
tags: connectors
ms.openlocfilehash: 93528e257ab45644a79c58fbf600dca10317eb0b
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789689"
---
# <a name="get-started-with-the-onedrive-connector"></a>Wprowadzenie do łącznika usługi OneDrive

Połącz się z usługą OneDrive, aby zarządzać plikami, w tym przekazywaniem, pobieraniem, usuwaniem plików itp. Za pomocą usługi OneDrive można wykonywać następujące zadania:

* Kompilowanie przepływu pracy przez przechowywanie plików w usłudze OneDrive lub aktualizowanie istniejących plików w usłudze OneDrive. 
* Użyj wyzwalaczy, aby uruchomić przepływ pracy po utworzeniu lub zaktualizowaniu pliku w usłudze OneDrive.
* Użyj akcji, aby utworzyć plik, usunąć plik i nie tylko. Na przykład po odebraniu nowej wiadomości e-mail w usłudze Office 365 z załącznikiem (wyzwalaczem) Utwórz nowy plik w usłudze OneDrive (akcję).

W tym artykule pokazano, jak używać łącznika usługi OneDrive w aplikacji logiki, a także wyświetlać wyzwalacze i akcje.

Aby dowiedzieć się więcej na temat Logic Apps, zobacz [co to są aplikacje logiki](../logic-apps/logic-apps-overview.md) i [Utwórz aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-onedrive"></a>Nawiązywanie połączenia z usługą OneDrive

Zanim aplikacja logiki będzie mogła uzyskać dostęp do dowolnej usługi, należy najpierw utworzyć *połączenie* z usługą. Połączenie zapewnia łączność między aplikacją logiki a inną usługą. Na przykład, aby połączyć się z usługą OneDrive, najpierw musisz mieć *połączenie*z usługą OneDrive. Aby utworzyć połączenie, wprowadź poświadczenia, które zwykle są używane w celu uzyskania dostępu do usługi, z którą chcesz nawiązać połączenie. Aby utworzyć połączenie, w usłudze OneDrive wprowadź poświadczenia dla konta usługi OneDrive.

### <a name="create-the-connection"></a>Tworzenie połączenia

[!INCLUDE [Steps to create a connection to OneDrive](../../includes/connectors-create-api-onedrive.md)]

## <a name="use-a-trigger"></a>Użyj wyzwalacza

Wyzwalacz to zdarzenie, którego można użyć do uruchomienia przepływu pracy zdefiniowanego w aplikacji logiki. Wyzwala "sondowanie" usługi z interwałem i częstotliwością. [Dowiedz się więcej o wyzwalaczach](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. W aplikacji logiki wpisz "OneDrive", aby uzyskać listę wyzwalaczy:  

   ![](./media/connectors-create-api-onedrive/onedrive-1.png)

2. Wybierz **, kiedy plik zostanie zmodyfikowany**. Jeśli połączenie już istnieje, wybierz przycisk Pokaż selektor, aby wybrać folder.

   ![](./media/connectors-create-api-onedrive/sample-folder.png)

   Jeśli zostanie wyświetlony monit o zalogowanie się, wprowadź szczegóły logowania, aby utworzyć połączenie. [Utwórz połączenie](connectors-create-api-onedrive.md#create-the-connection) w tym artykule, aby uzyskać listę kroków.

   W tym przykładzie aplikacja logiki jest uruchamiana, gdy zostanie zaktualizowany plik w wybranym folderze. Aby wyświetlić wyniki tego wyzwalacza, Dodaj kolejną akcję, która wysyła wiadomość e-mail. Na przykład Dodaj akcję *Wyślij wiadomość e-mail* do programu Outlook w usłudze Office 365, gdy plik zostanie zaktualizowany.

3. Wybierz przycisk **Edytuj** i ustaw wartości parametrów **częstotliwość** i **Interwał** . Na przykład jeśli wyzwalacz ma sondować co 15 minut, należy ustawić **częstotliwość** na **minutę**i ustawić **Interwał** na **15**. 

   ![](./media/connectors-create-api-onedrive/trigger-properties.png)

4. **Zapisz** zmiany (w lewym górnym rogu paska narzędzi). Aplikacja logiki jest zapisywana i może być automatycznie włączona.

## <a name="use-an-action"></a>Korzystanie z akcji

Akcja jest operacją wykonywaną przez przepływ pracy zdefiniowany w aplikacji logiki. [Dowiedz się więcej o akcjach](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. Wybierz znak plus. Zobaczysz kilka opcji: **Dodaj akcję**, **Dodaj warunek**lub jedną z opcji **więcej** .

   ![](./media/connectors-create-api-onedrive/add-action.png)

2. Wybierz pozycję **Dodaj akcję**.

3. W polu tekstowym wpisz "OneDrive", aby wyświetlić listę wszystkich dostępnych akcji.

   ![](./media/connectors-create-api-onedrive/onedrive-actions.png) 

4. W naszym przykładzie wybierz pozycję **OneDrive — Utwórz plik**. Jeśli połączenie już istnieje, wybierz **ścieżkę folderu** w celu umieszczenia pliku, wprowadź **nazwę pliku**i wybierz żądaną **zawartość pliku** :  

   ![](./media/connectors-create-api-onedrive/sample-action.png)

   Jeśli zostanie wyświetlony monit o podanie informacji o połączeniu, wprowadź szczegóły, aby [utworzyć połączenie zgodnie z opisem](#create-the-connection) w tym temacie.

   W tym przykładzie utworzysz nowy plik w folderze usługi OneDrive. Możesz użyć danych wyjściowych z innego wyzwalacza, aby utworzyć plik usługi OneDrive. Na przykład Dodaj pakiet Office 365 Outlook *po nadejściu nowej wiadomości e-mail* . Następnie Dodaj akcję *Utwórz plik* w usłudze OneDrive, która używa pól załączniki i typ zawartości w instrukcji foreach, aby utworzyć nowy plik w usłudze OneDrive.

   ![](./media/connectors-create-api-onedrive/foreach-action.png)

5. **Zapisz** zmiany (w lewym górnym rogu paska narzędzi). Aplikacja logiki jest zapisywana i może być automatycznie włączona.

## <a name="connector-specific-details"></a>Szczegóły dotyczące łącznika

Wyświetlanie wszystkich wyzwalaczy i akcji zdefiniowanych w strukturze Swagger, a także wszystkich ograniczeń w [szczegółach łącznika](/connectors/onedriveconnector/).

## <a name="next-steps"></a>Następne kroki

* [Łączniki dla usługi Azure Logic Apps](apis-list.md)