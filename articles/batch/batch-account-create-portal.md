---
title: Tworzenie konta usługi Batch w witrynie Azure Portal | Microsoft Docs
description: Dowiedz się, jak utworzyć konto usługi Azure Batch w portalu Azure w celu równoległego uruchamiania dużych obciążeń w chmurze
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 3fbae545-245f-4c66-aee2-e25d7d5d36db
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/14/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b1c8c981c0b1c3b599d2dd737b680390a52888eb
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Tworzenie konta usługi Batch w witrynie Azure Portal

> [!div class="op_single_selector"]
> * [Azure Portal](batch-account-create-portal.md)
> * [Batch Management .NET](batch-management-dotnet.md)
>
>

Dowiedz się, jak utworzyć konto usługi Azure Batch w witrynie [Azure Portal][azure_portal] i wybrać właściwości konta pasujące do scenariusza obliczeniowego. Dowiedz się, gdzie znaleźć ważne właściwości konta, takie jak klucze dostępu i adresy URL konta.

Ogólne informacje o kontach usługi Batch i scenariuszach można znaleźć w [omówieniu funkcji](batch-api-basics.md).



## <a name="create-a-batch-account"></a>Tworzenie konta usługi Batch

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

1. Zaloguj się w witrynie [Azure Portal][azure_portal].
2. Kliknij polecenie **Utwórz zasób** i w witrynie Marketplace wyszukaj ciąg **Usługa Batch**.

    ![Usługa Batch w witrynie Marketplace][marketplace_portal]
3. Wybierz pozycję **Usługa Batch**, kliknij przycisk **Utwórz** i wprowadź ustawienia **Nowe konto usługi Batch**. Zobacz następujące szczegóły.

    ![Tworzenie konta usługi Batch][account_portal]

    a. **Nazwa konta**: wybrana nazwa musi być unikatowa w obrębie regionu świadczenia usługi Azure, w którym konto zostanie utworzone (zobacz **Lokalizacja** poniżej). Nazwa konta może zawierać tylko małe litery lub cyfry i musi mieć od 3 do 24 znaków.

    b. **Subskrypcja**: subskrypcja, w której ma zostać utworzone konto usługi Batch. Jeśli masz tylko jedną subskrypcję, jest ona wybrana domyślnie.

    d. **Tryb alokacji puli**: jeśli to ustawienie jest wyświetlane, zaakceptuj wartość domyślną **Usługa Batch**.

    d. **Grupa zasobów**: wybierz istniejącą grupę zasobów dla nowego konta usługi Batch. Opcjonalnie można utworzyć nową grupę zasobów.

    d. **Lokalizacja**: region świadczenia usługi Azure, w którym ma zostać utworzone konto usługi Batch. Tylko regiony obsługiwane przez subskrypcję i grupę zasobów są wyświetlane jako opcje.

    e. **Konto magazynu** (opcjonalne): konto usługi Azure Storage skojarzone z kontem usługi Batch. Jest to zalecane w przypadku większości kont usługi Batch. Szczegółowe informacje można znaleźć w sekcji [Połączone konto usługi Azure Storage](#linked-azure-storage-account) w dalszej części tego artykułu.

4. Kliknij przycisk **Utwórz**, aby utworzyć konto.



## <a name="view-batch-account-properties"></a>Wyświetlanie właściwości konta usługi Batch
Po utworzeniu konta kliknij je, aby uzyskać dostęp do jego ustawień i właściwości. Menu po lewej stronie zapewnia dostęp do wszystkich ustawień i właściwości konta.

![Strona konta usługi Batch w witrynie Azure Portal][account_blade]

* **Adres URL konta usługi Batch**: aby po utworzeniu aplikacji za pomocą [interfejsów API usługi Batch](batch-apis-tools.md#azure-accounts-for-batch-development) uzyskać dostęp do zasobów usługi Batch, potrzebujesz adresu URL konta. Adres URL konta usługi Batch ma następujący format:

    `https://<account_name>.<region>.batch.azure.com`

![Adres URL konta usługi Batch w portalu][account_url]

* **Klucze dostępu**: do uwierzytelniania dostępu do konta usługi Batch z aplikacji możesz użyć klucza dostępu do konta. (Usługa Batch obsługuje też uwierzytelnianie za pomocą usługi Azure Active Directory).

    Aby wyświetlić lub ponownie wygenerować klucze dostępu, wybierz pozycję **Klucze**.

    ![Klucze konta usługi Batch w witrynie Azure Portal][account_keys]

[!INCLUDE [batch-pricing-include](../../includes/batch-pricing-include.md)]

## <a name="linked-azure-storage-account"></a>Połączone konto usługi Azure Storage

Konto usługi Azure Storage możesz połączyć z kontem usługi Batch, co jest przydatne w wielu scenariuszach. Funkcja [pakietów aplikacji](batch-application-packages.md) usługi Batch korzysta z usługi Azure Blob Storage, podobnie jak biblioteka [.NET Batch File Conventions](batch-task-output.md). Te opcjonalne funkcje pomagają we wdrażaniu aplikacji uruchamianych przez zadania usługi Batch oraz utrwalaniu wytwarzanych przez nich danych.

Opis opcji konta magazynu w usłudze Batch można znaleźć w temacie [Omówienie funkcji usługi Batch](batch-api-basics.md#azure-storage-account).

![Tworzenie konta magazynu][storage_account]

> [!NOTE]
> Należy zachować ostrożność przy ponownym generowaniu kluczy dostępu połączonego konta usługi Storage. Wygeneruj ponownie tylko jeden klucz konta usługi Storage i kliknij przycisk **Zsynchronizuj klucze** na stronie połączonego konta usługi Storage. Poczekaj 5 minut, aby umożliwić propagację kluczy do węzłów obliczeniowych w swoich pulach, a następnie w razie potrzeby ponownie wygeneruj i zsynchronizuj drugi klucz. Jeśli w tym samym czasie zostaną ponownie wygenerowane oba klucze, węzły obliczeniowe nie będą mogły zsynchronizować żadnego z nich i w ten sposób utracą dostęp do konta usługi Storage.
>
>

![Ponowne generowanie kluczy konta magazynu][4]

## <a name="additional-configuration-for-user-subscription-mode"></a>Dodatkowa konfiguracja dla trybu subskrypcji użytkownika

Jeśli wybrano tworzenie konta usługi Batch w trybie subskrypcji użytkownika, należy wykonać następujące dodatkowe kroki przed utworzeniem konta.

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>Umożliwia usłudze Azure Batch dostęp do subskrypcji (jednorazowa operacja)
Podczas tworzenia pierwszego konta usługi Batch w trybie subskrypcji użytkownika należy zarejestrować subskrypcję w usłudze Batch. (Jeśli wcześniej zostało to już zrobione, przejdź do następnej sekcji).

1. Zaloguj się w witrynie [Azure Portal][azure_portal].

2. Kliknij pozycję **Więcej usług** > **Subskrypcje**, a następnie kliknij subskrypcję, której chcesz użyć dla konta usługi Batch.

3. Na stronie **Subskrypcja** kliknij pozycję **Kontrola dostępu (IAM)** > **Dodaj**.

    ![Kontrola dostępu do subskrypcji][subscription_access]

4. Na stronie **Dodawanie uprawnień** wybierz rolę **Współautor** i wyszukaj interfejs API usługi Batch. Wyszukuj następujące ciągi, aż znajdziesz odpowiedni interfejs API:
    1. **MicrosoftAzureBatch**.
    2. **Microsoft Azure Batch**. W przypadku nowszych dzierżaw usługi Azure AD może być używana ta nazwa.
    3. **ddbf3205-c6bd-46ae-8127-60eb93363864** to identyfikator interfejsu API usługi Batch. 

5. Po odnalezieniu interfejsu API usługi Batch wybierz jego pozycję i kliknij przycisk **Zapisz**.

    ![Dodawanie uprawnień usługi Batch][add_permission]

### <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy
W trybie subskrypcji użytkownika wymagana jest usługa Azure Key Vault należąca do tej samej grupy zasobów, co konto usługi Batch, które ma zostać utworzone. Upewnij się, że grupa zasobów znajduje się w regionie, gdzie usługa Batch jest [dostępna](https://azure.microsoft.com/regions/services/) i który obsługuje Twoja subskrypcja.

1. W witrynie [Azure Portal][azure_portal] kliknij pozycję **Nowy** > **Bezpieczeństwo i obsługa tożsamości** > **Key Vault**.

2. Na stronie **Tworzenie magazynu Key Vault** wprowadź nazwę magazynu Key Vault i utwórz grupę zasobów w wymaganym regionie konta usługi Batch. Pozostaw wartości domyślne pozostałych ustawień, a następnie kliknij przycisk **Utwórz**.




## <a name="batch-service-quotas-and-limits"></a>Limity przydziału i limity usługi Batch
Podobnie jak w przypadku subskrypcji i innych usług platformy Azure, do kont usługi Batch mają zastosowanie określone [limity przydziału i limity](batch-quota-limit.md). Bieżące limity przydziału dla konta usługi Batch są wyświetlane w pozycji **Limity przydziału**.

![Limity przydziału konta usługi Batch w witrynie Azure Portal][quotas]



Ponadto wiele z tych limitów przydziału można powiększyć przy użyciu bezpłatnego żądania obsługi przesłanego w witrynie Azure Portal. Szczegóły dotyczące żądań zwiększenia limitów przydziału zamieszczono w artykule [Quotas and limits for the Azure Batch service](batch-quota-limit.md) (Limity przydziału i limity dla usługi Azure Batch).

## <a name="other-batch-account-management-options"></a>Inne opcje zarządzania kontem usługi Batch
Poza korzystaniem z witryny Azure Portal można tworzyć konta usługi Batch i zarządzać nimi przy użyciu następujących opcji:

* [Polecenia cmdlet programu PowerShell usługi Batch](batch-powershell-cmdlets-get-started.md)
* [Interfejs wiersza polecenia platformy Azure](batch-cli-get-started.md)
* [Batch Management .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>Następne kroki
* Aby dowiedzieć się więcej o zasadach działania i funkcjach usługi Batch, zobacz temat [Omówienie funkcji usługi Batch](batch-api-basics.md). W artykule omówiono podstawowe zasoby usługi Batch, takie jak pule, węzły obliczeniowe, zadania i podzadania, oraz opisano funkcje dla obciążeń zasobów obliczeniowych na dużą skalę.
* Poznaj podstawy tworzenia aplikacji wykorzystujących usługę Batch za pomocą biblioteki klienta [Batch .NET](batch-dotnet-get-started.md) lub języka [Python](batch-python-tutorial.md). Niniejsze artykuły wprowadzające zawierają omówienie działającej aplikacji, która korzysta z usługi Batch do wykonywania obciążenia na wielu węzłach obliczeniowych i stosuje usługę Azure Storage do tymczasowego przechowywania i pobierania pliku obciążenia.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[4]: ./media/batch-account-create-portal/batch_acct_04.png "Ponowne generowanie kluczy konta magazynu"
[marketplace_portal]: ./media/batch-account-create-portal/marketplace_batch.PNG
[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch_acct_portal.png
[account_keys]: ./media/batch-account-create-portal/account_keys.PNG
[account_url]: ./media/batch-account-create-portal/account_url.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[quotas]: ./media/batch-account-create-portal/quotas.png
[subscription_access]: ./media/batch-account-create-portal/subscription_iam.png
[add_permission]: ./media/batch-account-create-portal/add_permission.png

