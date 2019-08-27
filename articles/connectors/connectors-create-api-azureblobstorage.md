---
title: Nawiązywanie połączenia z usługą Azure Blob Storage — Azure Logic Apps
description: Tworzenie obiektów BLOB w usłudze Azure Storage i zarządzanie nimi za pomocą Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 06/20/2019
tags: connectors
ms.openlocfilehash: d57ea1a881980203b1c8f216239b27b64f0d71cd
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051052"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-with-azure-logic-apps"></a>Tworzenie obiektów BLOB w usłudze Azure Blob Storage i zarządzanie nimi za pomocą Azure Logic Apps

W tym artykule pokazano, jak można uzyskać dostęp do plików przechowywanych jako obiekty blob w ramach konta usługi Azure Storage i zarządzać nimi z poziomu aplikacji logiki za pomocą łącznika usługi Azure Blob Storage. Dzięki temu można tworzyć aplikacje logiki, które automatyzują zadania i przepływy pracy związane z zarządzaniem plikami. Można na przykład tworzyć aplikacje logiki, które tworzą, pobierają, aktualizują i usuwają pliki na koncie magazynu.

Załóżmy, że masz narzędzie, które jest aktualizowane w witrynie sieci Web systemu Azure. który działa jako wyzwalacz aplikacji logiki. Po wystąpieniu tego zdarzenia aplikacja logiki może aktualizować jakiś plik w kontenerze magazynu obiektów blob, który jest akcją w aplikacji logiki.

> [!NOTE]
> Logic Apps nie obsługuje bezpośredniego łączenia się z kontami usługi Azure Storage za pomocą zapór. Aby uzyskać dostęp do tych kont magazynu, użyj jednej z następujących opcji:
>
> * Utwórz [środowisko usługi integracji](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), które może łączyć się z zasobami w sieci wirtualnej platformy Azure.
>
> * Jeśli używasz już API Management, możesz użyć tej usługi w tym scenariuszu. Aby uzyskać więcej informacji, zobacz [prosta architektura integracji przedsiębiorstwa](https://aka.ms/aisarch).

Jeśli jesteś nowym usługą Logic Apps, zapoznaj [się](../logic-apps/logic-apps-overview.md) z tematem [Azure Logic Apps i szybki start: Utwórz swoją pierwszą aplikację](../logic-apps/quickstart-create-first-logic-app-workflow.md)logiki. Informacje techniczne dotyczące konkretnego łącznika można znaleźć w [dokumentacji łącznika usługi Azure Blob Storage](/connectors/azureblobconnector/).

## <a name="limits"></a>Limity

* Domyślnie akcje Blob Storage platformy Azure mogą odczytywać lub zapisywać pliki o *rozmiarze 50 MB lub mniejszym*. Do obsługi plików o rozmiarze większym niż 50 MB, ale nawet do 1024 MB, działania Blob Storage platformy Azure obsługują [fragmenty komunikatów](../logic-apps/logic-apps-handle-large-messages.md). Akcja **Pobierz zawartość obiektu BLOB** niejawnie używa podziału.

* Wyzwalacze usługi Azure Blob Storage nie obsługują rozdzielenie. Podczas żądania zawartości pliku wyzwalane są tylko pliki o rozmiarze 50 MB lub mniejszej. Aby uzyskać pliki o rozmiarze większym niż 50 MB, wykonaj następujące czynności:

  * Użyj wyzwalacza Blob Storage platformy Azure, który zwraca właściwości pliku, na przykład **gdy obiekt BLOB jest dodawany lub modyfikowany (tylko właściwości)** .

  * Obserwuj wyzwalacz z akcją **Pobierz zawartość obiektu Blob** BLOB Storage platformy Azure, która odczytuje kompletny plik i niejawnie używa podziału.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* [Kontener konta i magazynu platformy Azure](../storage/blobs/storage-quickstart-blobs-portal.md)

* Aplikacja logiki, do której potrzebny jest dostęp do konta usługi Azure Blob Storage. Aby uruchomić aplikację logiki przy użyciu wyzwalacza usługi Azure Blob Storage, musisz mieć [pustą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-trigger"></a>

## <a name="add-blob-storage-trigger"></a>Dodaj wyzwalacz magazynu obiektów BLOB

W Azure Logic Apps każda aplikacja logiki musi rozpoczynać się od [wyzwalacza](../logic-apps/logic-apps-overview.md#logic-app-concepts), który jest uruchamiany w przypadku wystąpienia konkretnego zdarzenia lub spełnienia określonego warunku. Za każdym razem, gdy wyzwala wyzwalacz, aparat Logic Apps tworzy wystąpienie aplikacji logiki i uruchamia przepływ pracy aplikacji.

Ten przykład pokazuje, jak uruchomić przepływ pracy aplikacji logiki przy użyciu, **kiedy obiekt BLOB jest dodawany lub modyfikowany (tylko właściwości)** wyzwalacz, gdy właściwości obiektu BLOB zostaną dodane lub zaktualizowane w kontenerze magazynu.

1. W [Azure Portal](https://portal.azure.com) lub Visual Studio Utwórz pustą aplikację logiki, która otwiera projektanta aplikacji logiki. Ten przykład używa Azure Portal.

2. W polu wyszukiwania wprowadź ciąg "Azure Blob" jako filtr. Z listy Wyzwalacze wybierz wyzwalacz, który chcesz.

   W tym przykładzie używa tego wyzwalacza: **Gdy obiekt BLOB jest dodawany lub modyfikowany (tylko właściwości)**

   ![Wybierz wyzwalacz](./media/connectors-create-api-azureblobstorage/azure-blob-trigger.png)

3. Jeśli zostanie wyświetlony monit o podanie szczegółów połączenia, [Utwórz teraz połączenie usługi BLOB Storage](#create-connection). Lub, jeśli połączenie już istnieje, podaj niezbędne informacje dla wyzwalacza.

   Na potrzeby tego przykładu wybierz kontener i folder, który chcesz monitorować.

   1. W polu **kontener** wybierz ikonę folderu.

   2. Na liście folder wybierz nawias ostry ( **>** ), a następnie Przeglądaj do momentu znalezienia i wybrania żądanego folderu.

      ![Wybierz folder](./media/connectors-create-api-azureblobstorage/trigger-select-folder.png)

   3. Wybierz interwał i częstotliwość sprawdzania przez wyzwalacza zmian w folderze.

4. Gdy skończysz, na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

5. Teraz Kontynuuj dodawanie co najmniej jednej akcji do aplikacji logiki w celu wykonywania zadań, które chcesz wykonać z wynikami wyzwalacza.

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>Dodaj akcję magazynu obiektów BLOB

W Azure Logic Apps [Akcja](../logic-apps/logic-apps-overview.md#logic-app-concepts) to krok w przepływie pracy, który następuje po wyzwalaczu lub innej akcji. W tym przykładzie aplikacja logiki rozpoczyna się od wyzwalacza [cyklu](../connectors/connectors-native-recurrence.md).

1. W [Azure Portal](https://portal.azure.com) lub Visual Studio Otwórz aplikację logiki w Projektancie aplikacji logiki. Ten przykład używa Azure Portal.

2. W Projektancie aplikacji logiki w obszarze wyzwalacza lub akcji wybierz pozycję **nowy krok**.

   ![Dodaj akcję](./media/connectors-create-api-azureblobstorage/add-action.png) 

   Aby dodać akcję między istniejącymi krokami, przesuń wskaźnik myszy na strzałkę łączącą. Wybierz wyświetlony znak plus ( **+** ), a następnie wybierz pozycję **Dodaj akcję**.

3. W polu wyszukiwania wprowadź ciąg "Azure Blob" jako filtr. Z listy Akcje wybierz żądaną akcję.

   W tym przykładzie używa tej akcji: **Pobierz zawartość obiektu BLOB**

   ![Wybierz akcję](./media/connectors-create-api-azureblobstorage/azure-blob-action.png)

4. Jeśli zostanie wyświetlony monit o podanie szczegółów połączenia, [Utwórz teraz połączenie BLOB Storage platformy Azure](#create-connection).
Lub, jeśli połączenie już istnieje, podaj niezbędne informacje dla tej akcji.

   Na potrzeby tego przykładu wybierz odpowiedni plik.

   1. W polu **obiekt BLOB** wybierz ikonę folderu.
  
      ![Wybierz folder](./media/connectors-create-api-azureblobstorage/action-select-folder.png)

   2. Znajdź i wybierz odpowiedni plik na podstawie numeru identyfikacyjnego obiektu BLOB . Ten numer identyfikacyjny można znaleźć w metadanych obiektu BLOB, które są zwracane przez poprzednio opisany wyzwalacz magazynu obiektów BLOB.

5. Gdy skończysz, na pasku narzędzi projektanta wybierz pozycję **Zapisz**.
Aby przetestować aplikację logiki, upewnij się, że wybrany folder zawiera obiekt BLOB.

W tym przykładzie pobierana jest tylko zawartość obiektu BLOB. Aby wyświetlić zawartość, Dodaj kolejną akcję, która tworzy plik z obiektem BLOB przy użyciu innego łącznika. Na przykład Dodaj akcję OneDrive, która tworzy plik na podstawie zawartości obiektu BLOB.

<a name="create-connection"></a>

## <a name="connect-to-storage-account"></a>Połącz z kontem magazynu

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to Azure blob storage](../../includes/connectors-create-api-azureblobstorage.md)]

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne, takie jak wyzwalacze, akcje i limity, zgodnie z opisem w pliku Open API (dawniej Swagger) łącznika, zobacz [stronę odniesienia łącznika](/connectors/azureblobconnector/).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md)
