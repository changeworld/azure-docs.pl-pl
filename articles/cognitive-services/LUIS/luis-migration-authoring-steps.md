---
title: Migrowanie do zasobu autora platformy Azure
titleSuffix: Azure Cognitive Services
description: Migrowanie do zasobu tworzenia platformy Azure.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: diberry
ms.openlocfilehash: f5197ae79670e4543c58224a33838706edae6218
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78194646"
---
# <a name="steps-to-migrate-to-the-azure-authoring-resource"></a>Kroki migracji do zasobu tworzenia platformy Azure

Z portalu language understanding (LUIS) należy przeprowadzić migrację wszystkich posiadanych aplikacji w celu użycia zasobu tworzenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

* **Opcjonalnie**utwórz kopię zapasową aplikacji z listy aplikacji portalu usługi LUIS, eksportując każdą aplikację lub korzystając z interfejsu [API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)eksportu .
* **Opcjonalnie**zapisz listę współpracowników każdej aplikacji. Wszyscy współpracownicy mogą wysyłać wiadomość e-mail w ramach procesu migracji.
* **Wymagane**, musisz mieć [subskrypcję platformy Azure](https://azure.microsoft.com/free/). Część procesu subskrypcji wymaga informacji rozliczeniowych. Jednak można użyć warstwy cenowej Free (F0) podczas korzystania z usługi LUIS. W miarę wzrostu użycia może się okazać, że potrzebujesz warstwy płatnej.

Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się](https://azure.microsoft.com/free/).

## <a name="access-the-migration-process"></a>Dostęp do procesu migracji

Co tydzień jest wyświetlany monit o migrację aplikacji. Można anulować to okno bez migracji. Jeśli chcesz przeprowadzić migrację przed następnym zaplanowanym okresem, możesz rozpocząć proces migracji z ikony **platformy Azure** na górnym pasku narzędzi w portalu usługi LUIS.

> [!div class="mx-imgBorder"]
> ![Ikona Migracji](./media/migrate-authoring-key/migration-button.png)

## <a name="app-owner-begins-the-migration-process"></a>Właściciel aplikacji rozpoczyna proces migracji

Proces migracji jest dostępny, jeśli jesteś właścicielem wszystkich aplikacji usługi LUIS.

1. Zaloguj się do [portalu usługi LUIS](https://www.luis.ai) i zaakceptuj warunki użytkowania.
1. Wyskakujące okno migracji umożliwia kontynuowanie migracji lub migrację później. Wybierz pozycję **Migruj teraz**. Jeśli zdecydujesz się przeprowadzić migrację później, masz 9 miesięcy na migrację do nowego klucza tworzenia na platformie Azure.

    ![Pierwsze okno podręczne w procesie migracji wybierz pozycję Migruj teraz.](./media/migrate-authoring-key/migrate-now.png)

1. Opcjonalnie, jeśli którakolwiek z twoich aplikacji ma współpracowników, zostanie wyświetlony monit o **wysłanie wiadomości e-mail** z powiadomieniem o migracji. Jest to opcjonalny krok.

    Po migracji konta na platformę Azure aplikacje nie będą już dostępne dla współpracowników.

    Dla każdego współpracownika i aplikacji domyślna aplikacja poczty e-mail zostanie otwarta z lekko sformatowaną wiadomością e-mail. Możesz edytować wiadomość e-mail przed jej wysłaniem.

    Szablon wiadomości e-mail zawiera dokładny identyfikator aplikacji i nazwę aplikacji.

    ```html
    Dear Sir/Madam,

    I will be migrating my LUIS account to Azure. Consequently, you will no longer have access to the following app:

    App Id: <app-ID-omitted>
    App name: Human Resources

    Thank you
    ```

1. Wybierz, aby utworzyć zasób tworzenia usługi LUIS, wybierając opcję użycia istniejącego zasobu autora lub utworzenia nowego zasobu autora.

    > [!div class="mx-imgBorder"]
    > ![Tworzenie zasobu autora](./media/migrate-authoring-key/choose-existing-authoring-resource.png)

1. W następnym oknie wprowadź informacje o kluczu zasobu. Po wprowadzeniu informacji wybierz pozycję **Utwórz zasób**. Możesz mieć 10 bezpłatnych zasobów do tworzenia na region, na subskrypcję.

    ![Tworzenie zasobu autora](./media/migrate-authoring-key/choose-authoring-resource-form.png)

    Podczas **tworzenia nowego zasobu autora**podaj następujące informacje:

    * **Nazwa zasobu** — wybrana nazwa niestandardowa, używana jako część adresu URL zapytań dotyczących tworzenia i przewidywania punktów końcowych.
    * **Dzierżawa** — dzierżawa, z nią skojarzona jest subskrypcja platformy Azure.
    * **Nazwa subskrypcji** — subskrypcja, która będzie naliczona za zasób.
    * **Grupa zasobów** — niestandardowa nazwa grupy zasobów, którą wybierzesz lub utworzysz. Grupy zasobów umożliwiają grupowanie zasobów platformy Azure w celu uzyskania dostępu i zarządzania.
    * **Lokalizacja** — wybór lokalizacji jest oparty na wyborze **grupy zasobów.**
    * **Warstwa cenowa** — warstwa cenowa określa maksymalną transakcję na sekundę i miesiąc.

1. Sprawdź poprawność zasobu autora i **migruj teraz**.

    ![Tworzenie zasobu autora](./media/migrate-authoring-key/choose-authoring-resource-and-migrate.png)

1. Po utworzeniu zasobu autora wyświetlany jest komunikat o powodach. Wybierz **przycisk Zamknij,** aby zamknąć okno podręczne.

    ![Zasób tworzenia został pomyślnie utworzony.](./media/migrate-authoring-key/migration-success.png)

    Lista **Moje aplikacje** pokazuje aplikacje migrowane do nowego zasobu autora.

    Nie musisz znać klucza zasobu autora, aby kontynuować edycję aplikacji w portalu usługi LUIS. Jeśli planujesz programowo edytować aplikacje, potrzebujesz wartości klucza tworzenia. Te wartości są wyświetlane na **stronie Zarządzanie zasobami platformy Azure >** w portalu usługi LUIS i są również dostępne w witrynie Azure portal na stronie **Klucze** zasobu.

1. Przed wejściem do aplikacji wybierz subskrypcję i zasób tworzenia usługi LUIS, aby wyświetlić aplikacje, które można tworzyć.

    ![Wybierz zasób tworzenia subskrypcji i usługi LUIS, aby wyświetlić aplikacje, które możesz autoryzować.](./media/migrate-authoring-key/app-list-by-subscription-and-resource.png)


## <a name="app-contributor-begins-the-migration-process"></a>Współautor aplikacji rozpoczyna proces migracji

Wykonaj te same kroki, co właściciel aplikacji w celu migracji. Proces tworzy nowy zasób `LUIS.Authoring`autora w rodzaju .

Musisz przeprowadzić migrację konta, aby dodać je jako współautora do zmigrowanych aplikacji należących do innych osób.

## <a name="after-the-migration-process-add-contributors-to-your-authoring-resource"></a>Po zakończeniu procesu migracji dodaj współautorów do zasobu autora

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

Dowiedz [się, jak dodawać współautorów](luis-how-to-collaborate.md).

## <a name="troubleshooting-errors-with-the-migration-process"></a>Rozwiązywanie problemów z błędami w procesie migracji

Jeśli podczas `MissingSubscriptionRegistration` procesu migracji zostanie wyświetlony błąd w portalu usługi LUIS z czerwonym paskiem powiadomień, utwórz zasób usługi Cognitive Service w [witrynie Azure portal](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) lub [interfejs wiersza polecenia platformy Azure.](luis-how-to-azure-subscription.md#create-resources-in-azure-cli) Dowiedz się więcej o [przyczynach tego błędu](../../azure-resource-manager/templates/error-register-resource-provider.md#cause).

## <a name="next-steps"></a>Następne kroki


* Przeglądanie [pojęć](luis-concept-keys.md) dotyczących tworzenia i kluczy środowiska uruchomieniowego
* Przejrzyj [sposób przypisywania kluczy](luis-how-to-azure-subscription.md) i dodawania [współautorów](luis-how-to-collaborate.md)
