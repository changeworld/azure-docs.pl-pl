---
title: Łączenie się z magazynem obiektów blob platformy Azure - Azure Logic Apps | Dokumentacja firmy Microsoft
description: Tworzenie i zarządzanie nimi obiekty BLOB w magazynie Azure z usługą Azure Logic Apps
author: ecfan
manager: cfowler
ms.author: estfan
ms.date: 05/21/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 15d737cd85f70717bfdf15dfb3d179f977b63c72
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "34723436"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-with-azure-logic-apps"></a>Tworzenie i zarządzanie nimi obiekty BLOB w magazynie obiektów blob platformy Azure z usługą Azure Logic Apps

W tym artykule przedstawiono sposób dostępu i zarządzanie plikami przechowywane jako obiekty BLOB na koncie magazynu platformy Azure z wewnątrz aplikacji logiki z łącznika usługi Azure Blob Storage. W ten sposób można utworzyć aplikacji logiki, które automatyzują zadania i przepływy pracy dotyczące zarządzania plikami. Na przykład można tworzyć aplikacje logiki, które tworzenia, get, aktualizacji i usuwania plików na Twoim koncie magazynu.

Załóżmy, że masz narzędzie, które zostanie zaktualizowany w witrynie sieci web platformy Azure. który działa jako wyzwalacz aplikacji logiki. W przypadku tego zdarzenia może mieć aplikację logiki zaktualizować niektóre pliki w kontenerze obiektu blob magazynu, czyli akcji w aplikacji logiki. 

Jeśli nie masz subskrypcji platformy Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>. Jeśli jesteś nowym użytkownikiem aplikacji logiki, przejrzyj [co to jest Azure Logic Apps](../logic-apps/logic-apps-overview.md) i [Szybki Start: tworzenie pierwszej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Aby uzyskać informacje techniczne dotyczące łącznika, zobacz <a href="https://docs.microsoft.com/connectors/azureblobconnector/" target="blank">odwołanie łącznika usługi Azure Blob Storage</a>.

## <a name="prerequisites"></a>Wymagania wstępne

* [Konto magazynu Azure i kontener magazynu](../storage/blobs/storage-quickstart-blobs-portal.md)

* Aplikację logiki, gdy potrzebny jest dostęp do konta magazynu obiektów blob platformy Azure. Aby uruchomić aplikację logiki z wyzwalaczem magazynu obiektów Blob Azure, należy [aplikacji logiki puste](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

<a name="add-trigger"></a>

## <a name="add-blob-storage-trigger"></a>Dodaj wyzwalacza magazynu obiektów blob

W aplikacjach logiki platformy Azure, każda aplikacja logiki musi rozpoczynać się od [wyzwalacza](../logic-apps/logic-apps-overview.md#logic-app-concepts), które odbywa się uruchamiany, gdy określonego zdarzenia lub gdy zostanie spełniony określony warunek. Zawsze uruchamiany wyzwalacza aparat Logic Apps tworzy wystąpienie aplikacji logiki i uruchamiania aplikacji przepływu pracy.

W tym przykładzie pokazano, jak można uruchomić przepływu pracy aplikacji logiki z **magazynu obiektów Blob Azure — obiekt blob jest dodawana lub modyfikacji (tylko właściwości)** wyzwalacza, gdy właściwości obiektu blob pobiera dodane lub zaktualizowane w kontenerze z magazynu. 

1. W portalu Azure lub programu Visual Studio tworzenie aplikacji logiki puste, który otwiera projektanta aplikacji logiki. W przykładzie użyto portalu Azure.

2. W polu wyszukiwania wprowadź "obiektów blob platformy azure" jako filtr. Na liście wyzwalaczy zaznacz wyzwalacz, który ma.

   W tym przykładzie użyto tego wyzwalacza: **magazynu obiektów Blob Azure — obiekt blob jest dodawana lub modyfikacji (tylko właściwości)**

   ![Wybierz wyzwalacz](./media/connectors-create-api-azureblobstorage/azure-blob-trigger.png)

3. Jeśli zostanie wyświetlony monit o szczegóły połączenia [utworzyć teraz połączenia magazynu obiektów blob](#create-connection). Lub, jeśli połączenie już istnieje, podaj informacje niezbędne do wyzwalacza.

   Na przykład wybierz kontenera i folderu, który chcesz monitorować.

   1. W **kontenera** wybierz ikonę folderu.

   2. Na liście folderu, wybierz prawa nawias ( **>** ), a następnie przejdź do momentu Znajdź i wybierz folder, w którym chcesz. 

      ![Wybierz folder](./media/connectors-create-api-azureblobstorage/trigger-select-folder.png)

   3. Wybierz interwał i częstotliwość Częstotliwość trigger, aby sprawdzić folder zmian.

4. Gdy wszystko będzie gotowe, na pasku narzędzi projektanta, wybierz pozycję **zapisać**.

5. Teraz kontynuować dodawanie co najmniej jednej akcji do aplikacji logiki dla zadań, które chcesz wykonać z wynikami wyzwalacza.

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>Dodaj akcję magazynu obiektów blob

W aplikacjach logiki platformy Azure [akcji](../logic-apps/logic-apps-overview.md#logic-app-concepts) jest krokiem w przepływie pracy następujący wyzwalacz inną akcję. Na przykład aplikację logiki rozpoczyna się od [wyzwalacza cyklu](../connectors/connectors-native-recurrence.md).

1. W portalu Azure lub programu Visual Studio Otwórz aplikację logiki w Projektancie aplikacji logiki. W przykładzie użyto portalu Azure.

2. W Projektancie aplikacji logiki w ramach wyzwalacza lub akcji, wybierz **nowy krok** > **Dodaj akcję**.

   ![Dodawanie akcji](./media/connectors-create-api-azureblobstorage/add-action.png) 

   Aby dodać akcję między krokami istniejących, myszą strzałkę nawiązującego połączenie. 
   Wybierz znak plus (**+**) zostanie wyświetlony, a następnie wybierz **Dodaj akcję**.

3. W polu wyszukiwania wprowadź "obiektów blob platformy azure" jako filtr. Z listy akcji wybierz akcję, którą chcesz.

   W tym przykładzie użyto tej akcji: **magazynu obiektów Blob Azure - pobrania zawartości obiektu blob**

   ![Wybierz akcję](./media/connectors-create-api-azureblobstorage/azure-blob-action.png) 

4. Jeśli zostanie wyświetlony monit o szczegóły połączenia [tworzenia połączenia magazynu obiektów Blob Azure teraz](#create-connection). Lub, jeśli połączenie już istnieje, podaj informacje niezbędne do działania. 

   Na przykład wybierz plik, który ma.

   1. Z **obiektu Blob** wybierz ikonę folderu.
  
      ![Wybierz folder](./media/connectors-create-api-azureblobstorage/action-select-folder.png)

   2. Znajdź i wybierz plik, który ma w zależności od obiektu blob **identyfikator** numer. Można je znaleźć **identyfikator** numer w metadanych obiektu blob, które jest zwracany przez wyzwalacz opisany wcześniej obiektu blob magazynu.

5. Gdy wszystko będzie gotowe, na pasku narzędzi projektanta, wybierz pozycję **zapisać**.
Aby przetestować aplikację logiki, upewnij się, że wybrany folder zawiera obiektu blob.

W tym przykładzie pobiera tylko zawartość obiektu blob. Aby wyświetlić zawartość, Dodaj inną akcję, która tworzy plik z obiektu blob przy użyciu innego łącznika. Na przykład dodać akcję OneDrive, która tworzy plik na podstawie zawartości obiektu blob.

<a name="create-connection"></a>

## <a name="connect-to-storage-account"></a>Połącz z kontem magazynu

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to Azure blob storage](../../includes/connectors-create-api-azureblobstorage.md)]

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne, takie jak wyzwalacze, akcje i limity, zgodnie z opisem w pliku programu Swagger łącznika, zobacz [strony odwołanie łącznika](/connectors/azureblobconnector/). 

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o innych [łączniki Logic Apps](../connectors/apis-list.md)
