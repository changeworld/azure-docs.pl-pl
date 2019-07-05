---
title: Nawiązywanie połączenia punkty końcowe REST z usługi Azure Logic Apps
description: Monitorowane punkty końcowe REST w zadaniach, procesów i przepływów pracy za pomocą usługi Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/05/2019
tags: connectors
ms.openlocfilehash: f0410ed7a98e4838e41407868cf26b5254811ae3
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541598"
---
# <a name="call-rest-endpoints-by-using-azure-logic-apps"></a>Wywoływanie punktów końcowych REST przy użyciu usługi Azure Logic Apps

Za pomocą [usługi Azure Logic Apps](../logic-apps/logic-apps-overview.md) i wbudowane protokołu HTTP + Swagger łącznika możesz zautomatyzować przepływy pracy, które regularnie wywołanie dowolnego punktu końcowego REST za pośrednictwem [plik struktury Swagger](https://swagger.io) przez tworzenie aplikacji logiki. HTTP + Swagger wyzwalacza i akcji, które działają w taki sam jak [wyzwalacza HTTP i akcji](connectors-native-http.md) jednak zapewnić lepsze doświadczenia w Projektancie aplikacji logiki, zapewniając strukturę interfejsu API i dane wyjściowe opisany przez plik struktury Swagger. Aby zaimplementować wyzwalacza sondowania, oparte na wzorcu sondowania opisaną w [Tworzenie niestandardowych interfejsów API można wywoływać z aplikacji logiki innych interfejsów API, usługami i systemami](../logic-apps/logic-apps-create-api-app.md#polling-triggers).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Adres URL pliku struktury Swagger, który opisuje docelowy punkt końcowy REST

  Zazwyczaj punktu końcowego REST musi spełniać te kryteria dla łącznika do pracy:

  * Plik struktury Swagger musi być hostowany na adres URL HTTPS, który jest publicznie dostępny.

  * Plik struktury Swagger muszą mieć [udostępniania zasobów między źródłami (CORS)](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) włączone.

  Aby odwołać plik struktury Swagger, który nie jest hostowana lub który nie spełnia wymagań dotyczących cross-origin bezpieczeństwa i, możesz [przekazać plik struktury Swagger do kontenera obiektów blob na koncie usługi Azure storage](#host-swagger), a więc włączyć mechanizm CORS dla tego konta magazynu Możesz odwołać się do pliku.

  Przykłady w tym temacie [Cognitive Services Face API](https://docs.microsoft.com/azure/cognitive-services/face/overview), co wymaga [usług Cognitive Services konta i klucza dostępu](../cognitive-services/cognitive-services-apis-create-account.md).

* Podstawową wiedzę na temat o [jak tworzyć aplikacje logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Jeśli dopiero zaczynasz pracę z usługi logic apps, zapoznaj się z [co to jest Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

* Aplikacja logiki, z którym ma zostać wywołana docelowy punkt końcowy. Na początek z protokołu HTTP + Swagger wyzwolić, [Tworzenie pustej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Do używają protokołu HTTP + Swagger akcji uruchomić swoją aplikację logiki z dowolnego wyzwalacza, który ma. W przykładzie wykorzystano HTTP + Swagger wyzwalacza w pierwszym kroku.

## <a name="add-an-http--swagger-trigger"></a>Dodawanie protokołu HTTP + Swagger wyzwalacza

Ten wyzwalacz wbudowanych wysyła żądanie HTTP do adresu URL dla pliku struktury Swagger, która opisuje interfejs API REST i zwraca odpowiedź, która zawiera zawartość tego pliku.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Otwórz swoje pustej aplikacji logiki w Projektancie aplikacji logiki.

1. W projektancie, w polu wyszukiwania wprowadź "swagger" jako filtr. Z **wyzwalaczy** listy wybierz **protokołu HTTP + Swagger** wyzwalacza.

   ![Wybierz opcję protokołu HTTP + Swagger wyzwalacza](./media/connectors-native-http-swagger/select-http-swagger-trigger.png)

1. W **adresu URL punktu KOŃCOWEGO struktury SWAGGER** pole, wprowadź adres URL pliku struktury Swagger, a następnie wybierz **dalej**.

   W tym przykładzie użyto programu Swagger adres URL, który znajduje się w regionie zachodnie stany USA [Cognitive Services Face API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236):

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Wprowadź adres URL punktu końcowego struktury Swagger](./media/connectors-native-http-swagger/http-swagger-trigger-parameters.png)

1. Podczas operacji opisany przez plik struktury Swagger w pokazie projektanta, należy wybrać operację, którego chcesz używać.

   ![Operacje w pliku struktury Swagger](./media/connectors-native-http-swagger/http-swagger-trigger-operations.png)

1. Podaj wartości dla wyzwalacza parametry, które różnią się w zależności od wybranej operacji, którą chcesz dołączyć wywołanie punktu końcowego. Skonfiguruj cyklu dla jak często ma wyzwalacz, aby wywołać punkt końcowy.

   Ten przykład zmienia nazwę wyzwalacza "HTTP + Swagger wyzwalacza: Twarzy — wykrywanie", aby ten krok ma bardziej opisowe nazwy.

   ![Szczegóły operacji](./media/connectors-native-http-swagger/http-swagger-trigger-operation-details.png)

1. Aby dodać inne dostępne parametry, otwórz **dodano nowy parametr** listy i wybierz parametry, które mają.

   Aby uzyskać więcej informacji na temat dostępnych typów uwierzytelniania dla protokołu HTTP + Swagger zobacz [uwierzytelniania HTTP wyzwalacze i akcje](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

1. Kontynuuj tworzenie przepływu pracy aplikacji logiki, za pomocą akcji, które są uruchamiane po aktywowaniu wyzwalacza.

1. Gdy skończysz, pamiętaj, aby zapisać aplikację logiki. Na pasku narzędzi Projektanta wybierz **Zapisz**.

## <a name="add-an-http--swagger-action"></a>Dodawanie protokołu HTTP + Swagger akcji

Ta akcja wbudowanych sprawia, że żądanie HTTP do adresu URL dla pliku struktury Swagger, która opisuje interfejs API REST i zwraca odpowiedź, która zawiera zawartość tego pliku.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Otwórz aplikację logiki w Projektancie aplikacji logiki.

1. W kroku, w której chcesz dodać protokołu HTTP + Swagger akcji wybierz **nowy krok**.

   Aby dodać akcję między krokami, wskaźnik myszy nad strzałką znajdującą się między krokami. Wybierz znak plus ( **+** ) pojawia się, a następnie wybierz **Dodaj akcję**.

1. W projektancie, w polu wyszukiwania wprowadź "swagger" jako filtr. Z **akcje** listy wybierz **protokołu HTTP + Swagger** akcji.

    ![Wybieranie protokołu HTTP + Swagger akcji](./media/connectors-native-http-swagger/select-http-swagger-action.png)

1. W **adresu URL punktu KOŃCOWEGO struktury SWAGGER** pole, wprowadź adres URL pliku struktury Swagger, a następnie wybierz **dalej**.

   W tym przykładzie użyto programu Swagger adres URL, który znajduje się w regionie zachodnie stany USA [Cognitive Services Face API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236):

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Wprowadź adres URL punktu końcowego struktury Swagger](./media/connectors-native-http-swagger/http-swagger-action-parameters.png)

1. Podczas operacji opisany przez plik struktury Swagger w pokazie projektanta, należy wybrać operację, którego chcesz używać.

   ![Operacje w pliku struktury Swagger](./media/connectors-native-http-swagger/http-swagger-action-operations.png)

1. W artości parametry akcji, które różnią się w zależności od wybranej operacji, którą chcesz dołączyć wywołanie punktu końcowego.

   W tym przykładzie nie ma parametrów, ale zmienia nazwę akcji "HTTP + Swagger akcji: Twarzy — ustalenie", aby ten krok ma bardziej opisowe nazwy.

   ![Szczegóły operacji](./media/connectors-native-http-swagger/http-swagger-action-operation-details.png)

1. Aby dodać inne dostępne parametry, otwórz **dodano nowy parametr** listy i wybierz parametry, które mają.

   Aby uzyskać więcej informacji na temat dostępnych typów uwierzytelniania dla protokołu HTTP + Swagger zobacz [uwierzytelniania HTTP wyzwalacze i akcje](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

1. Gdy skończysz, pamiętaj, aby zapisać aplikację logiki. Na pasku narzędzi Projektanta wybierz **Zapisz**.

<a name="host-swagger"></a>

## <a name="host-swagger-in-azure-storage"></a>Host Swagger w usłudze Azure Storage

Możesz odwoływać się plik struktury Swagger, który nie jest obsługiwana, lub który nie spełnia wymagań cross-origin dotyczących bezpieczeństwa i przez przekazanie tego pliku do kontenera obiektów blob na koncie usługi Azure storage i włączanie funkcji CORS dla tego konta magazynu. Do tworzenia, konfigurowania i przechowywania plików struktury Swagger w usłudze Azure Storage, wykonaj następujące kroki:

1. [Utwórz konto usługi Azure Storage](../storage/common/storage-create-storage-account.md).

1. Teraz można włączyć mechanizm CORS dla obiektu blob. Z menu konta magazynu wybierz **CORS**. Na **usługi Blob service** karcie, określ te wartości, a następnie wybierz **Zapisz**.

   | Właściwość | Wartość |
   |----------|-------|
   | **Dozwolone źródła** | `*` |
   | **Dozwolone metody** | `GET`, `HEAD`, `PUT` |
   | **Dozwolone nagłówki** | `*` |
   | **Uwidocznione nagłówki** | `*` |
   | **Maksymalny wiek** (w sekundach) | `200` |
   |||

   Mimo że w tym przykładzie użyto [witryny Azure portal](https://portal.azure.com), można użyć narzędzia, takie jak [Eksploratora usługi Azure Storage](https://storageexplorer.com/), albo automatycznie skonfigurować to ustawienie za pomocą tego przykładu [skryptprogramuPowerShell](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1).

1. [Utwórz kontener obiektów blob](../storage/blobs/storage-quickstart-blobs-portal.md). W kontenerze **Przegląd** okienku wybierz **Zmień poziom dostępu**. Z **poziom dostępu publicznego** listy wybierz **obiektów Blob (anonimowy dostęp do odczytu tylko dla obiektów blob.)** i wybierz **OK**.

1. [Przekaż plik struktury Swagger do kontenera obiektów blob](../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob), albo za pośrednictwem [witryny Azure portal](https://portal.azure.com) lub [Eksploratora usługi Azure Storage](https://storageexplorer.com/).

1. Aby odwoływać się do plików w kontenerze obiektów blob, użyj łącza HTTPS, który następuje po tym formacie, który jest rozróżniana wielkość liter:

   `https://<storage-account-name>.blob.core.windows.net/<blob-container-name>/<swagger-file-name>`

## <a name="connector-reference"></a>Dokumentacja łączników

Poniżej przedstawiono więcej informacji na temat danych wyjściowych z protokołu HTTP + Swagger wyzwalacza lub akcji. HTTP + Swagger wywołanie zwraca te informacje:

| Nazwa właściwości | Typ | Opis |
|---------------|------|-------------|
| Nagłówki | object | Nagłówki w żądaniu |
| Treść | object | Obiekt JSON | Obiekt z zawartości treści z żądania |
| Kod stanu: | int | Kod stanu z żądania |
|||

| Kod stanu | Opis |
|-------------|-------------|
| 200 | OK |
| 202 | Zaakceptowane |
| 400 | Nieprawidłowe żądanie |
| 401 | Brak autoryzacji |
| 403 | Zabroniony |
| 404 | Nie można odnaleźć |
| 500 | Wewnętrzny błąd serwera. Wystąpił nieznany błąd. |
|||

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o innych [łączników Logic Apps](../connectors/apis-list.md)