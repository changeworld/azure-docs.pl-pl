---
title: Uzyskiwanie dostępu do plików i zarządzanie nimi w usłudze Microsoft OneDrive
description: Przekazywanie plików w usłudze OneDrive i zarządzanie nimi przez tworzenie zautomatyzowanych przepływów pracy w usłudze Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/18/2016
tags: connectors
ms.openlocfilehash: edfbf090c3409d583cda6fd2c9957c37be5dfb7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75378436"
---
# <a name="access-and-manage-files-in-onedrive-connector-by-using-azure-logic-apps"></a>Uzyskiwanie dostępu do plików w usłudze OneDrive i zarządzanie nimi przy użyciu aplikacji Azure Logic Apps

Za pomocą [usługi Azure Logic Apps](../logic-apps/logic-apps-overview.md) i [łącznika usługi OneDrive](/connectors/onedriveconnector/)można tworzyć zautomatyzowane zadania i przepływy pracy w celu zarządzania plikami, w tym przekazywanie, pobieranie, usuwanie plików i inne. Za pomocą usługi OneDrive można wykonywać następujące zadania:

* Tworzenie przepływu pracy przez przechowywanie plików w usłudze OneDrive lub aktualizowanie istniejących plików w usłudze OneDrive. 
* Uruchamianie przepływu pracy po utworzeniu lub zaktualizowaniu pliku w usłudze OneDrive umożliwia uruchamianie przepływu pracy.
* Akcje służy do tworzenia pliku, usuwania pliku i nie tylko. Na przykład po odebraniu nowej wiadomości e-mail usługi Office 365 z załącznikiem (wyzwalaczem) utwórz nowy plik w usłudze OneDrive (akcja).

W tym artykule pokazano, jak używać łącznika usługi OneDrive w aplikacji logiki, a także wyświetla listę wyzwalaczy i akcji.

Aby dowiedzieć się więcej o aplikacjach logiki, zobacz [Co to są aplikacje logiki](../logic-apps/logic-apps-overview.md) i [utwórz aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-onedrive"></a>Łączenie z usługą OneDrive

Zanim aplikacja logiki będzie mogła uzyskać dostęp do dowolnej usługi, należy najpierw utworzyć *połączenie z* usługą. Połączenie zapewnia łączność między aplikacją logiki a inną usługą. Na przykład, aby połączyć się z usługą OneDrive, najpierw potrzebujesz *połączenia*z usługą OneDrive . Aby utworzyć połączenie, wprowadź poświadczenia, których zwykle używasz do uzyskiwania dostępu do usługi, z którą chcesz się połączyć. Tak więc w usłudze OneDrive wprowadź poświadczenia do konta usługi OneDrive, aby utworzyć połączenie.

### <a name="create-the-connection"></a>Tworzenie połączenia

[!INCLUDE [Steps to create a connection to OneDrive](../../includes/connectors-create-api-onedrive.md)]

## <a name="use-a-trigger"></a>Używanie wyzwalacza

Wyzwalacz jest zdarzeniem, którego można użyć do uruchomienia przepływu pracy zdefiniowanego w aplikacji logiki. Wyzwala "sondować" usługę w żądanym przedziale i częstotliwości. [Dowiedz się więcej o wyzwalaczach](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. W projektancie aplikacji `onedrive` logiki wpisz, aby uzyskać listę wyzwalaczy:  

   ![](./media/connectors-create-api-onedrive/onedrive-1.png)

2. Wybierz **opcję Po zmodyfikowaniu pliku**. Jeśli połączenie już istnieje, wybierz przycisk Pokaż selektor, aby wybrać folder.

   ![](./media/connectors-create-api-onedrive/sample-folder.png)

   Jeśli zostanie wyświetlony monit o zalogowanie się, wprowadź szczegóły logowania, aby utworzyć połączenie. [Utwórz połączenie w](connectors-create-api-onedrive.md#create-the-connection) tym artykule zawiera listę kroków.

   W tym przykładzie aplikacja logiki jest uruchamiana, gdy plik w folderze, który wybierzesz, zostanie zaktualizowany. Aby wyświetlić wyniki tego wyzwalacza, dodaj kolejną akcję, która wysyła wiadomość e-mail. Na przykład dodaj akcję *Wysyłania wiadomości e-mail* w programie Office 365 Outlook, która wysyła wiadomość e-mail po zaktualizowaniu pliku.

3. Wybierz przycisk **Edytuj** i ustaw wartości **częstotliwość** i **interwał.** Na przykład, jeśli chcesz, aby wyzwalacz sondować co 15 minut, a następnie ustawić **częstotliwość** **minut**, i ustawić **interwał** na **15**. 

   ![](./media/connectors-create-api-onedrive/trigger-properties.png)

4. **Zapisz** zmiany (lewy górny róg paska narzędzi). Aplikacja logiki jest zapisywana i może być automatycznie włączona.

## <a name="use-an-action"></a>Korzystanie z akcji

Akcja jest operacją przeprowadzaną przez przepływ pracy zdefiniowany w aplikacji logiki. [Dowiedz się więcej o akcjach](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. Wybierz znak plus. Dostępnych jest kilka opcji: **Dodaj akcję**, **Dodaj warunek**lub jedną z opcji **Więcej.**

   ![](./media/connectors-create-api-onedrive/add-action.png)

2. Wybierz **pozycję Dodaj akcję**.

3. W polu wyszukiwania `onedrive` wpisz, aby uzyskać listę wszystkich dostępnych akcji.

   ![](./media/connectors-create-api-onedrive/onedrive-actions.png) 

4. W naszym przykładzie wybierz pozycję **OneDrive — Tworzenie pliku**. Jeśli połączenie już istnieje, wybierz **ścieżkę folderu,** aby umieścić plik, wprowadź **nazwę pliku**i wybierz odpowiednią zawartość **pliku:**  

   ![](./media/connectors-create-api-onedrive/sample-action.png)

   Jeśli zostanie wyświetlony monit o podanie informacji o połączeniu, wprowadź szczegóły, aby [utworzyć połączenie zgodnie z opisem](#create-the-connection) w tym temacie.

   W tym przykładzie utworzysz nowy plik w folderze usługi OneDrive. Można użyć danych wyjściowych z innego wyzwalacza, aby utworzyć plik usługi OneDrive. Na przykład dodaj program Outlook usługi Office 365 Po odebraniu nowego wyzwalacza *wiadomości e-mail.* Następnie dodaj akcję *tworzenia pliku* usługi OneDrive, która używa pól Załączniki i Typ zawartości w forEach, aby utworzyć nowy plik w usłudze OneDrive.

   ![](./media/connectors-create-api-onedrive/foreach-action.png)

5. **Zapisz** zmiany (lewy górny róg paska narzędzi). Aplikacja logiki jest zapisywana i może być automatycznie włączona.

## <a name="connector-specific-details"></a>Szczegóły specyficzne dla łącznika

Wyświetl wszystkie wyzwalacze i akcje zdefiniowane w swagger, a także zobacz wszelkie limity w [szczegółach łącznika](/connectors/onedriveconnector/).

## <a name="next-steps"></a>Następne kroki

* [Łączniki dla usługi Azure Logic Apps](apis-list.md)