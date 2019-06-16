---
title: Łączenie się z magazynem obiektów blob platformy Azure — Azure Logic Apps
description: Tworzenie i zarządzanie obiektami BLOB w usłudze Azure storage z usługą Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 05/21/2018
tags: connectors
ms.openlocfilehash: ea3e97db9ec560306788943d92a7670025f38bdc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60958642"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-with-azure-logic-apps"></a>Tworzenie i zarządzanie obiektami BLOB w usłudze Azure blob storage z usługą Azure Logic Apps

W tym artykule pokazano, jak uzyskać dostęp i zarządzanie plikami przechowywane jako obiekty BLOB na koncie magazynu platformy Azure z wewnątrz aplikacji logiki za pomocą łącznika usługi Azure Blob Storage. Dzięki temu można tworzyć aplikacje logiki, które automatyzują zadania i przepływów pracy na potrzeby zarządzania plikami. Na przykład można tworzyć aplikacje logiki, które tworzenie, pobieranie, aktualizowanie i usuwanie plików w ramach konta magazynu.

Załóżmy, że masz narzędzie, które zostanie zaktualizowany w witrynie sieci Web platformy Azure. który działa jako wyzwalacz aplikacji logiki. Gdy to zdarzenie występuje, może mieć swoją aplikację logiki, zaktualizować niektóre pliki w kontenerze magazynu obiektów blob, czyli akcję w aplikacji logiki.

> [!NOTE]
> Usługa Logic Apps nie obsługuje bezpośredniego połączenia z konta usługi Azure storage za pośrednictwem zapór. Aby uzyskać dostęp do tych kont magazynu, użyj tutaj jedną z opcji:
>
> * Tworzenie [środowisko usługi integracji](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), które mogą łączyć się z zasobami w sieci wirtualnej platformy Azure.
>
> * Jeśli używasz już usługi API Management, można użyć tej usługi, w tym scenariuszu. Aby uzyskać więcej informacji, zobacz [Architektura integracji przedsiębiorstw proste](https://aka.ms/aisarch).

Jeśli dopiero zaczynasz pracę z usługi logic apps, zapoznaj się z [co to jest Azure Logic Apps](../logic-apps/logic-apps-overview.md) i [Szybki Start: Utwórz swoją pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Aby uzyskać informacje techniczne dotyczące łącznika, zobacz <a href="https://docs.microsoft.com/connectors/azureblobconnector/" target="blank">dokumentacja łączników usługi Azure Blob Storage</a>.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>.

* [Konta magazynu platformy Azure i kontener magazynu](../storage/blobs/storage-quickstart-blobs-portal.md)

* Aplikacja logiki gdzie potrzebujesz dostępu do konta magazynu obiektów blob platformy Azure. Aby uruchomić aplikację logiki z wyzwalaczem usługi Azure Blob Storage, musisz mieć [pusta aplikacja logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-trigger"></a>

## <a name="add-blob-storage-trigger"></a>Dodaj wyzwalacz usługi blob storage

W usłudze Azure Logic Apps, każda aplikacja logiki musi rozpoczynać się [wyzwalacza](../logic-apps/logic-apps-overview.md#logic-app-concepts), który jest aktywowany wystąpienia, gdy zajdzie określone zdarzenie lub po spełnieniu określonego warunku. Każdym aktywowaniu wyzwalacza aparat usługi Logic Apps tworzy wystąpienie aplikacji logiki i uruchamiania przepływu pracy Twojej aplikacji.

Ten przykład przedstawia, jak można uruchomić przepływu pracy aplikacji logiki za pomocą **usługi Azure Blob Storage — gdy obiekt blob jest dodawany lub modyfikowany (tylko właściwości)** wyzwalacza, gdy właściwości obiektu blob zostanie dodane lub zaktualizowane w kontenerze magazynu. 

1. W witrynie Azure portal lub programu Visual Studio należy utworzyć pustej aplikacji logiki, który zostanie otwarty projektant aplikacji logiki. W tym przykładzie użyto portalu Azure.

2. W polu wyszukiwania wprowadź "obiektów blob platformy azure" jako filtr. Z listy wyzwalaczy wybierz wyzwalacz, który ma.

   W tym przykładzie użyto tego wyzwalacza: **Usługa Azure Blob Storage — gdy obiekt blob jest dodawany lub modyfikowany (tylko właściwości)**

   ![Wybierz wyzwalacz](./media/connectors-create-api-azureblobstorage/azure-blob-trigger.png)

3. Jeśli zostanie wyświetlony monit, aby uzyskać informacje dotyczące połączenia, [utworzyć połączenia magazynu obiektów blob. teraz](#create-connection). Lub, jeśli istnieje już połączenie, podaj informacje niezbędne dla wyzwalacza.

   W tym przykładzie Wybierz kontener oraz folder, który chcesz monitorować.

   1. W **kontenera** wybierz ikonę folderu.

   2. Na liście folderów, wybierz nawias po prawej stronie ( **>** ), a następnie przejdź do momentu Znajdź i wybierz folder ma.

      ![Wybierz folder](./media/connectors-create-api-azureblobstorage/trigger-select-folder.png)

   3. Wybierz interwał i częstotliwość jak często ma wyzwalacz, aby sprawdzić folder zmian.

4. Gdy wszystko będzie gotowe, na pasku narzędzi Projektanta wybierz pozycję **Zapisz**.

5. Teraz kontynuowana, dodając jedną lub więcej akcji do aplikacji logiki dla zadań, które mają być wykonywane z wynikami wyzwalacza.

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>Dodawanie akcji do magazynu obiektów blob

W usłudze Azure Logic Apps [akcji](../logic-apps/logic-apps-overview.md#logic-app-concepts) jest to krok w przepływie pracy, występującego wyzwalacza lub innej akcji. W tym przykładzie aplikacja logiki rozpoczyna się od [wyzwalacz cykliczny](../connectors/connectors-native-recurrence.md).

1. W witrynie Azure portal lub programu Visual Studio Otwórz aplikację logiki w Projektancie aplikacji logiki. W tym przykładzie użyto portalu Azure.

2. W Projektancie aplikacji logiki w obszarze wyzwalacza lub akcji, wybierz **nowy krok** > **Dodaj akcję**.

   ![Dodawanie akcji](./media/connectors-create-api-azureblobstorage/add-action.png) 

   Aby dodać akcję między krokami istniejących, myszą strzałkę nawiązującego połączenie. 
   Wybierz znak plus ( **+** ) pojawia się, a następnie wybierz **Dodaj akcję**.

3. W polu wyszukiwania wprowadź "obiektów blob platformy azure" jako filtr. Z listy akcji wybierz akcję, którą chcesz.

   W tym przykładzie użyto tej akcji: **Usługa Azure Blob Storage — Pobierz zawartość obiektu blob**

   ![Wybierz akcję](./media/connectors-create-api-azureblobstorage/azure-blob-action.png) 

4. Jeśli zostanie wyświetlony monit, aby uzyskać informacje dotyczące połączenia, [utworzyć połączenie usługi Azure Blob Storage teraz](#create-connection). Lub, jeśli istnieje już połączenie, podaj informacje niezbędne do działania.

   W tym przykładzie wybierz żądany plik.

   1. Z **Blob** wybierz ikonę folderu.
  
      ![Wybierz folder](./media/connectors-create-api-azureblobstorage/action-select-folder.png)

   2. Znajdź i zaznacz plik, który ma zależności od obiektu blob **identyfikator** numer. Możesz znaleźć tę wartość **identyfikator** w metadanych obiektu blob, który jest zwracany przez opisany wcześniej obiekt blob wyzwalacza magazynu.

5. Gdy wszystko będzie gotowe, na pasku narzędzi Projektanta wybierz pozycję **Zapisz**.
Aby przetestować aplikację logiki, upewnij się, że wybrany folder zawiera obiekt blob.

W tym przykładzie pobiera tylko zawartość obiektu blob. Aby wyświetlić jego zawartość, Dodawanie kolejnej akcji, która tworzy plik z obiektu blob przy użyciu innego łącznika. Na przykład Dodaj akcję usługi OneDrive, która tworzy plik, w oparciu o zawartość obiektu blob.

<a name="create-connection"></a>

## <a name="connect-to-storage-account"></a>Łączenie się z kontem magazynu

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to Azure blob storage](../../includes/connectors-create-api-azureblobstorage.md)]

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne, takich jak wyzwalacze, akcje i ograniczeń, zgodnie z opisem w otwartych interfejsów API łącznika (dawniej Swagger) plików, zobacz [strona referencyjna łącznika](/connectors/azureblobconnector/).

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o innych [łączników Logic Apps](../connectors/apis-list.md)
