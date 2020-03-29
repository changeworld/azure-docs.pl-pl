---
title: Łączenie się z punktami końcowymi REST z usługi Azure Logic Apps
description: Monitorowanie punktów końcowych REST w zautomatyzowanych zadaniach, procesach i przepływach pracy przy użyciu aplikacji Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/01/2019
tags: connectors
ms.openlocfilehash: b34fdc36bd0b1ce294a92b2ae8fa5da01568e5a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74787373"
---
# <a name="call-rest-endpoints-by-using-azure-logic-apps"></a>Wywoływanie punktów końcowych REST przy użyciu aplikacji Azure Logic Apps

Dzięki [usłudze Azure Logic Apps](../logic-apps/logic-apps-overview.md) i wbudowanemu łącznikowi HTTP + Swagger można zautomatyzować przepływy pracy, które regularnie wywołują dowolny punkt końcowy REST za pośrednictwem pliku [Swagger,](https://swagger.io) tworząc aplikacje logiki. Wyzwalacz i akcja HTTP + Swagger działają tak samo jak [wyzwalacz i akcja HTTP,](connectors-native-http.md) ale zapewniają lepsze środowisko w projektancie aplikacji logiki, udostępniając strukturę interfejsu API i dane wyjściowe opisane przez plik Swagger. Aby zaimplementować wyzwalacz sondowania, postępuj zgodnie z wzorcem sondowania opisanym w [polu Tworzenie niestandardowych interfejsów API w celu wywoływania innych interfejsów API, usług i systemów z aplikacji logiki.](../logic-apps/logic-apps-create-api-app.md#polling-triggers)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Adres URL pliku Swagger (not OpenAPI), który opisuje docelowy punkt końcowy REST

  Zazwyczaj punkt końcowy REST musi spełniać te kryteria, aby łącznik działał:

  * Plik Swagger musi być hostowany na publicznie dostępnym adresie URL HTTPS.

  * Plik Swagger musi mieć włączone [udostępnianie zasobów cross-origin (CORS).](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)

  Aby odwołać się do pliku Swagger, który nie jest obsługiwany lub który nie spełnia wymagań zabezpieczeń i między źródłami, można [przekazać plik Swagger do kontenera obiektu blob na koncie magazynu platformy Azure](#host-swagger)i włączyć usługę CORS na tym koncie magazynu, dzięki czemu można odwoływać się do pliku.

  W przykładach w tym temacie użyto [interfejsu API twarzy usług Cognitive Services,](https://docs.microsoft.com/azure/cognitive-services/face/overview)który wymaga konta usług Cognitive Services i [klucza dostępu](../cognitive-services/cognitive-services-apis-create-account.md).

* Podstawowa wiedza na temat [tworzenia aplikacji logicznych](../logic-apps/quickstart-create-first-logic-app-workflow.md). Jeśli jesteś nowy w aplikacjach logiki, sprawdź [Co to jest usługa Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

* Aplikacja logiki, z której chcesz wywołać docelowy punkt końcowy. Aby rozpocząć od wyzwalacza HTTP + Swagger, [utwórz pustą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby użyć akcji HTTP + Swagger, uruchom aplikację logiki z dowolnym wyzwalaczem, który chcesz. W tym przykładzie użyto wyzwalacza HTTP + Swagger jako pierwszego kroku.

## <a name="add-an-http--swagger-trigger"></a>Dodawanie wyzwalacza HTTP + Swagger

Ten wbudowany wyzwalacz wysyła żądanie HTTP do adresu URL dla pliku Swagger, który opisuje interfejs API REST i zwraca odpowiedź zawierającą zawartość tego pliku.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Otwórz pustą aplikację logiki w logice App Designer.

1. Na projektancie w polu wyszukiwania wpisz "swagger" jako filtr. Z listy **Wyzwalacze** wybierz wyzwalacz **HTTP + Swagger.**

   ![Wybierz spust HTTP + Swagger](./media/connectors-native-http-swagger/select-http-swagger-trigger.png)

1. W polu **Adres URL punktu końcowego SWAGGER** wprowadź adres URL pliku Swagger i wybierz przycisk **Dalej**.

   W tym przykładzie użyto adresu URL Swagger, który znajduje się w regionie Zachodnie stany USA dla [interfejsu API twarzy usług Cognitive Services:](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Wprowadź adres URL punktu końcowego Swaggera](./media/connectors-native-http-swagger/http-swagger-trigger-parameters.png)

1. Gdy projektant pokazuje operacje opisane przez plik Swagger, wybierz operację, której chcesz użyć.

   ![Operacje w pliku Swagger](./media/connectors-native-http-swagger/http-swagger-trigger-operations.png)

1. Podaj wartości parametrów wyzwalacza, które różnią się w zależności od wybranej operacji, które mają zostać uwzględnione w wywołaniu punktu końcowego. Skonfiguruj cykl, aby określić, jak często wyzwalacz ma wywoływać punkt końcowy.

   W tym przykładzie zmienia nazwę wyzwalacza na "HTTP + Swagger trigger: Face - Detect", dzięki czemu krok ma bardziej opisową nazwę.

   ![Szczegóły operacji](./media/connectors-native-http-swagger/http-swagger-trigger-operation-details.png)

1. Aby dodać inne dostępne parametry, otwórz listę **Dodaj nowy parametr** i wybierz żądane parametry.

   Aby uzyskać więcej informacji na temat typów uwierzytelniania dostępnych dla protokołu HTTP + Swagger, zobacz [Dodawanie uwierzytelniania do połączeń wychodzących](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Kontynuuj tworzenie przepływu pracy aplikacji logiki z akcji, które są uruchamiane po uruchomieniu wyzwalacza.

1. Po zakończeniu pamiętaj, aby zapisać aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

## <a name="add-an-http--swagger-action"></a>Dodawanie akcji HTTP + Swagger

Ta wbudowana akcja sprawia, że żądanie HTTP do adresu URL dla pliku Swagger, który opisuje interfejs API REST i zwraca odpowiedź, która zawiera zawartość tego pliku.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com). Otwórz aplikację logiki w logic app designer.

1. W obszarze kroku, w którym chcesz dodać akcję HTTP + Swagger, wybierz pozycję **Nowy krok**.

   Aby dodać akcję między krokami, przesuń wskaźnik myszy na strzałkę między krokami. Wybierz wyświetlony znak**+** plus ( ), a następnie wybierz pozycję **Dodaj akcję**.

1. Na projektancie w polu wyszukiwania wpisz "swagger" jako filtr. Z listy **Akcje** wybierz akcję **HTTP + Swagger.**

    ![Wybierz akcję HTTP + Swagger](./media/connectors-native-http-swagger/select-http-swagger-action.png)

1. W polu **Adres URL punktu końcowego SWAGGER** wprowadź adres URL pliku Swagger i wybierz przycisk **Dalej**.

   W tym przykładzie użyto adresu URL Swagger, który znajduje się w regionie Zachodnie stany USA dla [interfejsu API twarzy usług Cognitive Services:](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Wprowadź adres URL punktu końcowego Swaggera](./media/connectors-native-http-swagger/http-swagger-action-parameters.png)

1. Gdy projektant pokazuje operacje opisane przez plik Swagger, wybierz operację, której chcesz użyć.

   ![Operacje w pliku Swagger](./media/connectors-native-http-swagger/http-swagger-action-operations.png)

1. Podaj wartości parametrów akcji, które różnią się w zależności od wybranej operacji, które mają zostać uwzględnione w wywołaniu punktu końcowego.

   W tym przykładzie nie ma parametrów, ale zmienia nazwę akcji na "HTTP + Swagger action: Face - Identify", dzięki czemu krok ma bardziej opisową nazwę.

   ![Szczegóły operacji](./media/connectors-native-http-swagger/http-swagger-action-operation-details.png)

1. Aby dodać inne dostępne parametry, otwórz listę **Dodaj nowy parametr** i wybierz żądane parametry.

   Aby uzyskać więcej informacji na temat typów uwierzytelniania dostępnych dla protokołu HTTP + Swagger, zobacz [Dodawanie uwierzytelniania do połączeń wychodzących](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Po zakończeniu pamiętaj, aby zapisać aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

<a name="host-swagger"></a>

## <a name="host-swagger-in-azure-storage"></a>Usługa Swagger hosta w usłudze Azure Storage

Można odwołać się do pliku Swagger, który nie jest obsługiwany lub który nie spełnia wymagań zabezpieczeń i cross-origin, przekazując ten plik do kontenera obiektów blob na koncie magazynu platformy Azure i włączając cors na tym koncie magazynu. Aby utworzyć, skonfigurować i przechowywać pliki Swagger w usłudze Azure Storage, wykonaj następujące kroki:

1. [Utwórz konto magazynu platformy Azure](../storage/common/storage-create-storage-account.md).

1. Teraz włącz CORS dla obiektu blob. W menu konta pamięci masowej wybierz pozycję **CORS**. Na karcie **Usługa obiektów blob** określ te wartości, a następnie wybierz pozycję **Zapisz**.

   | Właściwość | Wartość |
   |----------|-------|
   | **Dozwolone źródła** | `*` |
   | **Dozwolone metody** | `GET`, `HEAD`, `PUT` |
   | **Dozwolone nagłówki** | `*` |
   | **Uwidocznione nagłówki** | `*` |
   | **Maksymalny wiek** (w sekundach) | `200` |
   |||

   Chociaż w tym przykładzie użyto [witryny Azure portal,](https://portal.azure.com)można użyć narzędzia, takiego jak [Eksplorator usługi Azure Storage,](https://storageexplorer.com/)lub automatycznie skonfigurować to ustawienie przy użyciu tego przykładowego [skryptu programu PowerShell](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1).

1. [Tworzenie kontenera obiektów blob](../storage/blobs/storage-quickstart-blobs-portal.md). W okienku **Przegląd** kontenera wybierz pozycję **Zmień poziom dostępu**. Z listy **Poziom dostępu publicznego** wybierz pozycję **Blob (anonimowy dostęp do odczytu tylko dla obiektów blob)** i wybierz przycisk **OK**.

1. [Przekaż plik Swagger do kontenera obiektów blob](../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob)za pośrednictwem [witryny Azure portal](https://portal.azure.com) lub [Eksploratora usługi Azure Storage.](https://storageexplorer.com/)

1. Aby odwołać się do pliku w kontenerze obiektów blob, należy użyć łącza HTTPS, który następuje po tym formacie, który jest rozróżniana wielkość liter:

   `https://<storage-account-name>.blob.core.windows.net/<blob-container-name>/<swagger-file-name>`

## <a name="connector-reference"></a>Dokumentacja łączników

Oto więcej informacji na temat wyjść z wyzwalacza lub akcji HTTP + Swagger. Wywołanie HTTP + Swagger zwraca te informacje:

| Nazwa właściwości | Typ | Opis |
|---------------|------|-------------|
| Nagłówki | obiekt | Nagłówki z żądania |
| body | obiekt | Obiekt JSON | Obiekt z zawartością treści z żądania |
| kod stanu | int | Kod stanu z żądania |
|||

| Kod stanu | Opis |
|-------------|-------------|
| 200 | OK |
| 202 | Zaakceptowane |
| 400 | Zła prośba |
| 401 | Brak autoryzacji |
| 403 | Forbidden |
| 404 | Nie znaleziono |
| 500 | Wewnętrzny błąd serwera. Wystąpił nieznany błąd. |
|||

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o innych [łącznikach aplikacji logiki](../connectors/apis-list.md)
