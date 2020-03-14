---
title: Jak używać kluczy tworzenia i środowiska uruchomieniowego — LUIS
titleSuffix: Azure Cognitive Services
description: Przy pierwszym użyciu Language Understanding (LUIS) nie trzeba tworzyć klucza tworzenia. Jeśli zamierzasz opublikować aplikację, użyj punktu końcowego środowiska uruchomieniowego, aby utworzyć aplikację i przypisać do niej klucz środowiska uruchomieniowego.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79219974"
---
# <a name="using-authoring-and-runtime-resource-keys"></a>Korzystanie z kluczy zasobów tworzenia i środowiska uruchomieniowego

Zasoby tworzenia i środowiska uruchomieniowego zapewniają uwierzytelnianie w aplikacji LUIS i punkcie końcowym przewidywania.

<a name="create-luis-service"></a>
<a name="create-language-understanding-endpoint-key-in-the-azure-portal"></a>

Po zalogowaniu się do portalu LUIS możesz kontynuować:

* bezpłatny [klucz wersji próbnej](#trial-key) — udostępniający tworzenie i kilka zapytań dotyczących punktów końcowych przewidywania.
* zasób [tworzenia Luis](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) platformy Azure. 


<a name="starter-key"></a>

## <a name="sign-in-to-luis-portal-and-begin-authoring"></a>Zaloguj się do portalu LUIS i zacznij tworzenie

1. Zaloguj się do [portalu Luis](https://www.luis.ai) i zaakceptuj warunki użytkowania.
1. Rozpocznij swoją aplikację LUIS, wybierając typ klucza tworzenia LUIS, który ma być używany: bezpłatny klucz wersji próbnej lub nowy klucz tworzenia usługi Azure LUIS. 

    ![Wybierz typ zasobu Language Understanding tworzenia](./media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Po zakończeniu procesu wyboru zasobów [Utwórz nową aplikację](luis-how-to-start-new-app.md#create-new-app-in-luis). 

## <a name="trial-key"></a>Klucz wersji próbnej

Zostanie udostępniony klucz wersji próbnej (Starter). Służy jako klucz uwierzytelniania do wysyłania zapytań do środowiska uruchomieniowego punktu końcowego przewidywania, do 1000 zapytań miesięcznie. 

Jest ona widoczna na stronie **Ustawienia użytkownika** oraz na stronach **Zarządzanie > zasobów platformy Azure** w portalu Luis. 

Gdy wszystko będzie gotowe do opublikowania punktu końcowego przewidywania, Utwórz i przypisz klucze środowiska uruchomieniowego tworzenia i przewidywania, aby zastąpić funkcję klucza początkowego. 

## <a name="create-resources-in-the-azure-portal"></a>Tworzenie zasobów w Azure Portal

1. Użyj [tego linku](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) , aby otworzyć Azure Portal pod kątem tworzenia zasobów.
1. Wybierz **oba te** elementy, aby utworzyć klucz środowiska uruchomieniowego usługi tworzenia i prognozowania. 
1. Wprowadź informacje wymagane do utworzenia zasobu, a następnie wybierz pozycję **Utwórz** , aby zakończyć proces.

    ![Tworzenie zasobu interpretacji języka](./media/luis-how-to-azure-subscription/create-resource-in-azure.png)

    |Name (Nazwa)|Przeznaczenie|
    |--|--|
    |Nazwa zasobu| Wybrana nazwa niestandardowa, używana jako część adresu URL dla zapytań dotyczących tworzenia i przewidywania punktów końcowych.|
    |Nazwa subskrypcji| subskrypcja, która będzie rozliczana za zasób.|
    |Grupa zasobów| Nazwa niestandardowej grupy zasobów, która została wybrana lub utworzona. Grupy zasobów umożliwiają grupowanie zasobów platformy Azure w celu uzyskania dostępu i zarządzania w tym samym regionie.|
    |Lokalizacja autorstwa|Region skojarzony z Twoim modelem.|
    |Tworzenie warstwy cenowej|Warstwa cenowa określa maksymalną liczbę transakcji na sekundę i miesiąc.|
    |Lokalizacja środowiska uruchomieniowego|Region skojarzony z opublikowanym środowiskiem uruchomieniowym przewidywanego punktu końcowego.|
    |Warstwa cenowa środowiska uruchomieniowego|Warstwa cenowa określa maksymalną liczbę transakcji na sekundę i miesiąc.|

    Po utworzeniu obu zasobów Przypisz zasoby w portalu LUIS.

## <a name="create-resources-in-azure-cli"></a>Tworzenie zasobów w interfejsie wiersza polecenia platformy Azure

Utwórz poszczególne zasoby przy użyciu [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) . 

`kind`zasobów:

* Tworzenie: `LUIS.Authoring`
* Przewidywanie: `LUIS` 

1. Zaloguj się do interfejsu wiersza polecenia platformy Azure:

    ```azurecli
    az login
    ```

    Spowoduje to otwarcie przeglądarki w celu umożliwienia wybrania odpowiedniego konta i zapewnienia uwierzytelniania.

1. Utwórz `LUIS.Authoring`**zasób tworzenia Luis**o nazwie `my-luis-authoring-resource` w _istniejącej_ grupie zasobów o nazwie `my-resource-group` dla regionu `westus`. 

    ```azurecli
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

1. Utwórz `LUIS`**zasób punktu końcowego przewidywania Luis**o nazwie `my-luis-prediction-resource` w _istniejącej_ grupie zasobów o nazwie `my-resource-group` dla regionu `westus`. Jeśli potrzebujesz wyższej przepływności niż warstwa Bezpłatna, Zmień `F0` na `S0`. Dowiedz się więcej o [warstwach cenowych i przepływności](luis-boundaries.md#key-limits).

    ```azurecli
    az cognitiveservices account create -n my-luis-prediction-resource -g my-resource-group --kind LUIS --sku F0 -l westus --yes
    ```

    > [!Note] 
    > Te klucze nie są używane przez portal LUIS, dopóki **nie** zostaną przypisane do portalu Luis w **zasobach platformy Azure Zarządzanie >** .

## <a name="assign-an-authoring-resource-in-the-luis-portal-for-all-apps"></a>Przypisywanie zasobów autorstwa w portalu LUIS dla wszystkich aplikacji

Można przypisać zasób tworzenia dla jednej aplikacji lub dla wszystkich aplikacji w LUIS. Poniższa procedura przypisuje wszystkie aplikacje do pojedynczego zasobu tworzenia.

1. Zaloguj się do [portalu Luis](https://www.luis.ai).
1. Na górnym pasku nawigacyjnym po prawej stronie wybierz konto użytkownika, a następnie wybierz pozycję **Ustawienia**.
1. Na stronie **Ustawienia użytkownika** wybierz pozycję **Dodaj zasób tworzenia** , a następnie wybierz istniejący zasób tworzenia. Wybierz pozycję **Zapisz**. 

## <a name="assign-a-resource-to-an-app"></a>Przypisywanie zasobu do aplikacji

Do aplikacji można przypisać pojedynczy zasób, tworzenie lub przewidywanie środowiska uruchomieniowego punktu końcowego.

1. Zaloguj się do [portalu Luis](https://www.luis.ai), a następnie wybierz aplikację z listy **Moje aplikacje** .
1. Przejdź do strony **zarządzaj > zasobami platformy Azure** .

    ![Wybierz pozycję Zarządzaj > zasobów platformy Azure w portalu LUIS, aby przypisać zasób do aplikacji.](./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png)

1. Wybierz kartę zasób przewidywania lub tworzenia, a następnie wybierz przycisk **Dodaj zasób predykcyjny** lub **Dodaj zasób tworzenia zasobów** . 
1. Wybierz pola w formularzu, aby znaleźć poprawny zasób, a następnie wybierz pozycję **Zapisz**.  

### <a name="assign-runtime-resource-without-using-luis-portal"></a>Przypisywanie zasobu środowiska uruchomieniowego bez użycia portalu LUIS

W celach automatyzacji, takich jak potok ciągłej integracji/ciągłego wdrażania, można zautomatyzować przypisanie zasobu środowiska uruchomieniowego LUIS do aplikacji LUIS. Aby to zrobić, należy wykonać następujące czynności:

1. Pobierz token Azure Resource Manager z tej [witryny sieci Web](https://resources.azure.com/api/token?plaintext=true). Ten token wygaśnie więc natychmiast zastosować go. Żądanie zwraca tokenu usługi Azure Resource Manager.

    ![Token Azure Resource Manager żądania i Odbierz token Azure Resource Manager](./media/luis-manage-keys/get-arm-token.png)

1. Użyj tokenu, aby zażądać zasobów środowiska uruchomieniowego LUIS w różnych subskrypcjach, za pomocą [interfejsu API kont platformy Azure Get Luis](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c), do którego Twoje konto użytkownika ma dostęp. 

    Ten interfejs API WPIS wymaga następujących ustawień:

    |Nagłówek|Wartość|
    |--|--|
    |`Authorization`|Wartość `Authorization` jest `Bearer {token}`. Zwróć uwagę, że wartość tokenu musi być poprzedzona słowem `Bearer` i spacją.| 
    |`Ocp-Apim-Subscription-Key`|Twój klucz tworzenia.|

    Ten interfejs API zwraca tablicę obiektów JSON subskrypcji usługi LUIS, m.in. identyfikator subskrypcji, grupy zasobów i nazwę zasobu, zwracane jako nazwy konta. Znajdź jeden element w tablicy, która jest zasobem usługi LUIS do przypisania do aplikacji usługi LUIS. 

1. Przypisz token do zasobu LUIS za pomocą [konta platformy Azure Luis do](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) interfejsu API aplikacji. 

    Ten interfejs API WPIS wymaga następujących ustawień:

    |Typ|Ustawienie|Wartość|
    |--|--|--|
    |Nagłówek|`Authorization`|Wartość `Authorization` jest `Bearer {token}`. Zwróć uwagę, że wartość tokenu musi być poprzedzona słowem `Bearer` i spacją.|
    |Nagłówek|`Ocp-Apim-Subscription-Key`|Twój klucz tworzenia.|
    |Nagłówek|`Content-type`|`application/json`|
    |Ciąg zapytania|`appid`|Identyfikator aplikacji usługi LUIS. 
    |Treść||{"AzureSubscriptionId": "ddda2925-af7f-4b05-9ba1-2155c5fe8a8e"<br>"ResourceGroup": "Grupa zasobów-2"<br>"Nazwa konta": "Usługa luis-uswest-S0-2"}|

    Po pomyślnym ten interfejs API zwraca 201 - stan utworzone. 

## <a name="unassign-resource"></a>Cofanie przypisania zasobów

1. Zaloguj się do [portalu Luis](https://www.luis.ai), a następnie wybierz aplikację z listy **Moje aplikacje** .
1. Przejdź do strony **zarządzaj > zasobami platformy Azure** .
1. Wybierz kartę zasób przewidywania lub tworzenia, a następnie wybierz przycisk **Cofnij przypisanie zasobu** dla zasobu. 

Po odpisaniu zasobu nie jest on usuwany z platformy Azure. Jest on tylko została odłączona od usługi LUIS. 

## <a name="reset-authoring-key"></a>Resetuj klucz tworzenia pakietów administracyjnych

**W przypadku [tworzenia aplikacji migrowanych zasobów](luis-migration-authoring.md)** : Jeśli klucz autorstwa został naruszony, zresetuj klucz w Azure Portal na stronie **klucze** dla tego zasobu tworzenia. 

W **przypadku aplikacji, które nie zostały jeszcze zmigrowane**: klucz jest resetowany we wszystkich aplikacjach w portalu Luis. W przypadku tworzenia aplikacji za pośrednictwem interfejsów API tworzenia należy zmienić wartość parametru OCP-APIM-Subscription-Key na nowy klucz.

## <a name="regenerate-azure-key"></a>Wygeneruj ponownie klucz platformy Azure

Wygeneruj ponownie klucze platformy Azure z Azure Portal na stronie **klucze** .

## <a name="delete-account"></a>Usuń konto

Zobacz [magazynowanie i usuwanie danych,](luis-concept-data-storage.md#accounts) Aby uzyskać informacje o tym, jakie dane są usuwane po usunięciu konta.

## <a name="change-pricing-tier"></a>Zmiana warstwy cenowej

1.  Na [platformie Azure](https://portal.azure.com)Znajdź swoją subskrypcję usługi Luis. Wybierz subskrypcję usługi LUIS.
    ![znaleźć subskrypcję usługi LUIS](./media/luis-usage-tiers/find.png)
1.  Wybierz pozycję **warstwa cenowa** , aby wyświetlić dostępne warstwy cenowe. 
    ![wyświetlić warstwy cenowe](./media/luis-usage-tiers/subscription.png)
1.  Wybierz warstwę cenową, a następnie wybierz pozycję **Wybierz** , aby zapisać zmiany. 
    ![zmienić warstwę płatności LUIS](./media/luis-usage-tiers/plans.png)
1.  Po zakończeniu zmianą cen okno podręczne sprawdza nowej warstwy cenowej. 
    ![zweryfikować warstwy płatności LUIS](./media/luis-usage-tiers/updated.png)
1. Należy pamiętać o [przypisaniu tego klucza punktu końcowego](#assign-a-resource-to-an-app) na stronie **publikowania** i używania go we wszystkich zapytaniach punktów końcowych. 

## <a name="viewing-azure-resource-metrics"></a>Wyświetlanie metryk zasobów platformy Azure

### <a name="viewing-azure-resource-summary-usage"></a>Wyświetlanie użycia podsumowania zasobów platformy Azure
Możesz wyświetlić informacje dotyczące użycia usługi LUIS na platformie Azure. Na stronie **Przegląd** są wyświetlane najnowsze informacje podsumowujące, w tym wywołania i błędy. Jeśli wprowadzisz żądanie punktu końcowego LUIS, natychmiast Obejrzyj **stronę przeglądu**, aby umożliwić wyświetlenie użycia do 5 minut.

![Wyświetlanie podsumowania użycia](./media/luis-usage-tiers/overview.png)

### <a name="customizing-azure-resource-usage-charts"></a>Dostosowywanie wykresów użycia zasobów platformy Azure
Metryki zapewnia bardziej szczegółowy wgląd w dane.

![Domyślne metryki](./media/luis-usage-tiers/metrics-default.png)

Wykresy metryk można skonfigurować w przedziale czasu i typ metryki. 

![Metryki niestandardowe](./media/luis-usage-tiers/metrics-custom.png)

### <a name="total-transactions-threshold-alert"></a>Łączna liczba transakcji próg alertu
Jeśli chcesz dowiedzieć się, gdy został osiągnięty limit określonego progu transakcji, na przykład 10 000 transakcji, można utworzyć alertu. 

![Domyślne alerty](./media/luis-usage-tiers/alert-default.png)

Dodaj alert dotyczący metryki **łącznej liczby wywołań** przez określony czas. Dodaj adresy e-mail wszystkich osób, które powinien zostać wyświetlony alert. Dodaj elementy webhook dla wszystkich systemów, które powinny otrzymać alert. Można również uruchomić aplikację logiki, gdy zostanie wyzwolony alert. 

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, [jak za pomocą wersji](luis-how-to-manage-versions.md) sterować cyklem życia aplikacji.
* Zapoznaj się z pojęciami dotyczącymi [zasobów tworzenia](luis-concept-keys.md#authoring-key) i [współautorów](luis-concept-keys.md#contributions-from-other-authors) tego zasobu.
* Dowiedz się [, jak tworzyć zasoby dotyczące](luis-how-to-azure-subscription.md) tworzenia i wykonywania
* Migrowanie do nowego [zasobu tworzenia](luis-migration-authoring.md) 
