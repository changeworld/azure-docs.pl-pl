---
title: Jak używać kluczy tworzenia i środowiska uruchomieniowego — usługa LUIS
titleSuffix: Azure Cognitive Services
description: Podczas pierwszego użycia języka rozumienia (LUIS), nie trzeba tworzyć klucza tworzenia. Gdy zamierzasz opublikować aplikację, a następnie użyj punktu końcowego środowiska uruchomieniowego, należy utworzyć i przypisać klucz środowiska wykonawczego do aplikacji.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: a5140da32eb6fce03131a42bfa90e71e64552431
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219974"
---
# <a name="using-authoring-and-runtime-resource-keys"></a>Korzystanie z kluczy zasobów tworzenia i środowiska wykonawczego

Zasoby tworzenia i środowiska wykonawczego zapewniają uwierzytelnianie aplikacji usługi LUIS i punktu końcowego przewidywania.

<a name="create-luis-service"></a>
<a name="create-language-understanding-endpoint-key-in-the-azure-portal"></a>

Po zalogowaniu się do portalu usługi LUIS można kontynuować:

* bezpłatny [klucz próbny](#trial-key) — zapewniający tworzenie i kilka zapytań dotyczących punktu końcowego przewidywania.
* zasobów tworzenia usługi Azure [LUIS.](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) 


<a name="starter-key"></a>

## <a name="sign-in-to-luis-portal-and-begin-authoring"></a>Zaloguj się do portalu usługi LUIS i rozpocznij tworzenie

1. Zaloguj się do [portalu usługi LUIS](https://www.luis.ai) i zaakceptuj warunki użytkowania.
1. Rozpocznij aplikację usługi LUIS, wybierając typ klucza tworzenia usługi LUIS, którego chcesz użyć: bezpłatny klucz próbny lub nowy klucz tworzenia usługi Azure LUIS. 

    ![Wybieranie typu zasobu tworzenia opisu języka](./media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Po zakończeniu procesu wyboru zasobów [utwórz nową aplikację](luis-how-to-start-new-app.md#create-new-app-in-luis). 

## <a name="trial-key"></a>Klucz wersji próbnej

Klucz próbny (rozrusznik) jest dla Ciebie. Jest on używany jako klucz uwierzytelniania do kwerendy środowiska uruchomieniowego punktu końcowego przewidywania, do 1000 zapytań miesięcznie. 

Jest to widoczne zarówno na stronie **Ustawienia użytkownika,** jak i na stronach **Zarządzanie zasobami platformy Azure >** w portalu usługi LUIS. 

Gdy będziesz gotowy do opublikowania punktu końcowego przewidywania, należy utworzyć i przypisać klucze środowiska wykonawczego tworzenia i przewidywania, aby zastąpić funkcjonalność klucza startowego. 

## <a name="create-resources-in-the-azure-portal"></a>Tworzenie zasobów w witrynie Azure portal

1. Użyj [tego łącza,](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) aby otworzyć witrynę Azure portal w celu tworzenia zasobów.
1. Wybierz **opcję Zarówno,** aby utworzyć klucz tworzenia i przewidywania punktu końcowego środowiska uruchomieniowego. 
1. Wprowadź informacje wymagane do utworzenia zasobu, a następnie wybierz pozycję **Utwórz,** aby zakończyć proces.

    ![Tworzenie zasobu opisu języka](./media/luis-how-to-azure-subscription/create-resource-in-azure.png)

    |Nazwa|Przeznaczenie|
    |--|--|
    |Nazwa zasobu| Wybrana nazwa niestandardowa, używana jako część adresu URL zapytań dotyczących tworzenia i przewidywania punktów końcowych.|
    |Nazwa subskrypcji| subskrypcji, która zostanie naliczona za zasób.|
    |Grupa zasobów| Niestandardowa nazwa grupy zasobów, którą wybierzesz lub utworzysz. Grupy zasobów umożliwiają grupowanie zasobów platformy Azure w celu uzyskania dostępu i zarządzania w tym samym regionie.|
    |Lokalizacja tworzenia|Region skojarzony z modelem.|
    |Warstwa cenowa tworzenia|Warstwa cenowa określa maksymalną transakcję na sekundę i miesiąc.|
    |Lokalizacja środowiska uruchomieniowego|Region skojarzony z opublikowanym czasem wykonywania punktu końcowego przewidywania.|
    |Warstwa cenowa środowiska uruchomieniowego|Warstwa cenowa określa maksymalną transakcję na sekundę i miesiąc.|

    Po utworzeniu obu zasobów przypisz zasoby w portalu usługi LUIS.

## <a name="create-resources-in-azure-cli"></a>Tworzenie zasobów w platformie Azure CLI

Użyj [interfejsu wiersza polecenia platformy Azure,](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) aby utworzyć każdy zasób indywidualnie. 

Zasób `kind`:

* Tworzenia:`LUIS.Authoring`
* Przewidywania:`LUIS` 

1. Zaloguj się do interfejsu wiersza polecenia platformy Azure:

    ```azurecli
    az login
    ```

    Spowoduje to otwarcie przeglądarki, aby umożliwić wybranie właściwego konta i uwierzytelnienie.

1. Utwórz **zasób tworzenia** `LUIS.Authoring`usługi `my-luis-authoring-resource` LUIS , w `my-resource-group` rodzaju `westus` , nazwany w _istniejącej_ grupie zasobów o nazwie dla regionu. 

    ```azurecli
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

1. Tworzenie **zasobu końcowego punktu końcowego prognozowania usługi LUIS**, w rodzaju `LUIS`, o nazwie `my-luis-prediction-resource` w _istniejącej_ grupie zasobów o nazwie `my-resource-group` dla regionu. `westus` Jeśli chcesz uzyskać wyższą przepustowość niż `F0` `S0`warstwa bezpłatna, zmień na . Dowiedz się więcej o [warstwach cenowych i przepływności](luis-boundaries.md#key-limits).

    ```azurecli
    az cognitiveservices account create -n my-luis-prediction-resource -g my-resource-group --kind LUIS --sku F0 -l westus --yes
    ```

    > [!Note] 
    > Te klucze nie są używane przez portal usługi LUIS, dopóki **nie** zostaną przypisane do portalu usługi LUIS w **zasobach platformy Azure Manage ->**.

## <a name="assign-an-authoring-resource-in-the-luis-portal-for-all-apps"></a>Przypisywanie zasobu autora w portalu usługi LUIS dla wszystkich aplikacji

Możesz przypisać zasób tworzenia dla pojedynczej aplikacji lub dla wszystkich aplikacji w usłudze LUIS. Poniższa procedura przypisuje wszystkie aplikacje do jednego zasobu tworzenia.

1. Zaloguj się do [portalu usługi LUIS](https://www.luis.ai).
1. Na górnym pasku nawigacyjnym po prawej stronie wybierz konto użytkownika, a następnie wybierz pozycję **Ustawienia**.
1. Na stronie **Ustawienia użytkownika** wybierz pozycję **Dodaj zasób autorski,** a następnie wybierz istniejący zasób autorski. Wybierz **pozycję Zapisz**. 

## <a name="assign-a-resource-to-an-app"></a>Przypisywanie zasobu do aplikacji

Można przypisać pojedynczy zasób, tworzenie lub przewidywanie środowiska wykonawczego punktu końcowego, do aplikacji z poniższą procedurą.

1. Zaloguj się do [portalu usługi LUIS,](https://www.luis.ai)a następnie wybierz aplikację z listy **Moje aplikacje.**
1. Przejdź do strony **Zarządzanie zasobami platformy Azure >.**

    ![Wybierz zarządzanie zasobami platformy Azure > w portalu usługi LUIS, aby przypisać zasób do aplikacji.](./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png)

1. Wybierz kartę Przewidywanie lub Zasoby autora, a następnie wybierz przycisk **Dodaj zasób przewidywania** lub **Dodaj zasoby autora.** 
1. Zaznacz pola w formularzu, aby znaleźć właściwy zasób, a następnie wybierz pozycję **Zapisz**.  

### <a name="assign-runtime-resource-without-using-luis-portal"></a>Przypisywanie zasobu środowiska uruchomieniowego bez korzystania z portalu usługi LUIS

Do celów automatyzacji, takich jak potok ciągłej integracji/ciągłego wdrażania, można zautomatyzować przypisanie zasobu środowiska uruchomieniowego usługi LUIS do aplikacji usługi LUIS. Aby to zrobić, należy wykonać następujące kroki:

1. Pobierz token usługi Azure Resource Manager z tej [witryny sieci Web](https://resources.azure.com/api/token?plaintext=true). Ten token wygasa, więc należy go natychmiast użyć. Żądanie zwraca token usługi Azure Resource Manager.

    ![Żądanie tokenu usługi Azure Resource Manager i odbieranie tokenu usługi Azure Resource Manager](./media/luis-manage-keys/get-arm-token.png)

1. Użyj tokenu, aby zażądać zasobów środowiska uruchomieniowego usługi LUIS w ramach subskrypcji, z [interfejsu API get usługi LUIS azure,](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c)do którego ma dostęp konto użytkownika. 

    Ten interfejs API POST wymaga następujących ustawień:

    |Nagłówek|Wartość|
    |--|--|
    |`Authorization`|Wartość `Authorization` jest `Bearer {token}`. Należy zauważyć, że wartość tokenu `Bearer` musi być poprzedzona wyrazem i spacją.| 
    |`Ocp-Apim-Subscription-Key`|Klucz do tworzenia.|

    Ten interfejs API zwraca tablicę obiektów JSON subskrypcji usługi LUIS, w tym identyfikator subskrypcji, grupę zasobów i nazwę zasobu, zwróconą jako nazwa konta. Znajdź jeden element w tablicy, który jest zasobem usługi LUIS do przypisania do aplikacji usługi LUIS. 

1. Przypisz token do zasobu usługi LUIS za pomocą [konta Azure Przypisywanie usługi LUIS do interfejsu](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) API aplikacji. 

    Ten interfejs API POST wymaga następujących ustawień:

    |Typ|Ustawienie|Wartość|
    |--|--|--|
    |Nagłówek|`Authorization`|Wartość `Authorization` jest `Bearer {token}`. Należy zauważyć, że wartość tokenu `Bearer` musi być poprzedzona wyrazem i spacją.|
    |Nagłówek|`Ocp-Apim-Subscription-Key`|Klucz do tworzenia.|
    |Nagłówek|`Content-type`|`application/json`|
    |Querystring|`appid`|Identyfikator aplikacji usługi LUIS. 
    |Treść||{"AzureSubscriptionId":"ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceGroup": "resourcegroup-2",<br>"AccountName": "luis-uswest-S0-2"}|

    Po pomyślnym zakończeniu tego interfejsu API zwraca stan 201 — utworzony. 

## <a name="unassign-resource"></a>Niepodpisywaj zasobu

1. Zaloguj się do [portalu usługi LUIS,](https://www.luis.ai)a następnie wybierz aplikację z listy **Moje aplikacje.**
1. Przejdź do strony **Zarządzanie zasobami platformy Azure >.**
1. Wybierz kartę Przewidywanie lub Tworzenie zasobów, a następnie wybierz przycisk **Cozbij przydział zasobu** dla zasobu. 

Po odsyłaniu zasobu nie jest on usuwany z platformy Azure. Jest on akces dołączona tylko z usługi LUIS. 

## <a name="reset-authoring-key"></a>Resetowanie klucza tworzenia

**W przypadku tworzenia aplikacji [migrowanych zasobów:](luis-migration-authoring.md) **jeśli klucz tworzenia jest zagrożony, zresetuj klucz w witrynie Azure portal na stronie **Klucze** dla tego zasobu autora. 

**W przypadku aplikacji, które nie zostały jeszcze zmigrowane:** klucz jest resetowany we wszystkich aplikacjach w portalu usługi LUIS. Jeśli autor aplikacji za pośrednictwem interfejsów API tworzenia, należy zmienić wartość Ocp-Apim-Subscription-Key do nowego klucza.

## <a name="regenerate-azure-key"></a>Ponowne generowanie klucza platformy Azure

Ponowne generowanie kluczy platformy Azure z witryny Azure portal, **na** keys strony.

## <a name="delete-account"></a>Usuń konto

Aby uzyskać informacje o tym, jakie dane są usuwane po usunięciu konta, zobacz [Przechowywanie i usuwanie danych.](luis-concept-data-storage.md#accounts)

## <a name="change-pricing-tier"></a>Zmiana warstwy cenowej

1.  Na [platformie Azure](https://portal.azure.com)znajdź subskrypcję usługi LUIS. Wybierz subskrypcję usługi LUIS.
    ![Znajdowanie subskrypcji usługi LUIS](./media/luis-usage-tiers/find.png)
1.  Wybierz **warstwę cenową,** aby wyświetlić dostępne warstwy cenowe. 
    ![Wyświetlanie warstw cenowych](./media/luis-usage-tiers/subscription.png)
1.  Wybierz warstwę cenową i wybierz **wybierz pozycję Wybierz,** aby zapisać zmianę. 
    ![Zmienianie warstwy płatności usługi LUIS](./media/luis-usage-tiers/plans.png)
1.  Po zakończeniu zmiany cen wyskakujące okno weryfikuje nową warstwę cenową. 
    ![Weryfikowanie warstwy płatności usługi LUIS](./media/luis-usage-tiers/updated.png)
1. Pamiętaj, aby [przypisać ten klucz punktu końcowego](#assign-a-resource-to-an-app) na stronie **Publikowanie** i używać go we wszystkich kwerendach punktu końcowego. 

## <a name="viewing-azure-resource-metrics"></a>Wyświetlanie metryk zasobów platformy Azure

### <a name="viewing-azure-resource-summary-usage"></a>Wyświetlanie użycia podsumowania zasobów platformy Azure
Można wyświetlić informacje o użyciu usługi LUIS na platformie Azure. Na stronie **Przegląd** znajdują się najnowsze informacje podsumowujące, w tym połączenia i błędy. Jeśli zostanie złożony żądanie punktu końcowego usługi LUIS, a następnie natychmiast obejrzeć **przegląd strony**, odczekać do pięciu minut na użycie, aby pokazać.

![Wyświetlanie użycia podsumowania](./media/luis-usage-tiers/overview.png)

### <a name="customizing-azure-resource-usage-charts"></a>Dostosowywanie wykresów użycia zasobów platformy Azure
Metryki zapewnia bardziej szczegółowy widok danych.

![Domyślne metryki](./media/luis-usage-tiers/metrics-default.png)

Można skonfigurować wykresy metryki dla okresu i typu metryki. 

![Metryki niestandardowe](./media/luis-usage-tiers/metrics-custom.png)

### <a name="total-transactions-threshold-alert"></a>Alert progu transakcji całkowitych
Jeśli chcesz wiedzieć, kiedy osiągnąłeś określony próg transakcji, na przykład 10 000 transakcji, możesz utworzyć alert. 

![Alerty domyślne](./media/luis-usage-tiers/alert-default.png)

Dodaj alert metryki dla **metryki łącznej liczby połączeń** dla określonego okresu. Dodaj adresy e-mail wszystkich osób, które powinny otrzymać alert. Dodaj element webhook dla wszystkich systemów, które powinny odbierać alert. Można również uruchomić aplikację logiki po wyzwoleniu alertu. 

## <a name="next-steps"></a>Następne kroki

* Dowiedz [się, jak używać wersji](luis-how-to-manage-versions.md) do kontrolowania cyklu życia aplikacji.
* Poznaj pojęcia, w tym [zasobów autora](luis-concept-keys.md#authoring-key) i [współautorów](luis-concept-keys.md#contributions-from-other-authors) tego zasobu.
* Dowiedz [się, jak tworzyć](luis-how-to-azure-subscription.md) zasoby tworzenia i środowiska wykonawczego
* Migrowanie do nowego [zasobu autora](luis-migration-authoring.md) 
