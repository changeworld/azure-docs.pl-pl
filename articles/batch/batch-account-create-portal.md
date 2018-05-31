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
ms.date: 05/15/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 83d97d9ed9c51d59500115c4ee3896d471024999
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34359761"
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

2. Kliknij kolejno opcje **Nowy** > **Obliczenia** > **Usługa Batch**.

    ![Usługa Batch w witrynie Marketplace][marketplace_portal]

3. Wprowadź ustawienia **Nowe konto usługi Batch**. Zobacz następujące szczegóły.

    ![Tworzenie konta usługi Batch][account_portal]

    a. **Nazwa konta**: wybrana nazwa musi być unikatowa w obrębie regionu świadczenia usługi Azure, w którym konto zostanie utworzone (zobacz **Lokalizacja** poniżej). Nazwa konta może zawierać tylko małe litery lub cyfry i musi mieć od 3 do 24 znaków.

    b. **Subskrypcja**: subskrypcja, w której ma zostać utworzone konto usługi Batch. Jeśli masz tylko jedną subskrypcję, jest ona wybrana domyślnie.

    d. **Grupa zasobów**: wybierz istniejącą grupę zasobów dla nowego konta usługi Batch. Opcjonalnie można utworzyć nową grupę zasobów.

    d. **Lokalizacja**: region świadczenia usługi Azure, w którym ma zostać utworzone konto usługi Batch. Tylko regiony obsługiwane przez subskrypcję i grupę zasobów są wyświetlane jako opcje.

    e. **Konto magazynu** (opcjonalne): konto usługi Azure Storage skojarzone z kontem usługi Batch. Jest to zalecane w przypadku większości kont usługi Batch. Opis opcji konta magazynu w usłudze Batch można znaleźć w temacie [Omówienie funkcji usługi Batch](batch-api-basics.md#azure-storage-account). W portalu wybierz istniejące konto magazynu lub opcjonalnie utwórz nowe.

      ![Tworzenie konta magazynu][storage_account]

    f. **Tryb alokacji puli**: w przypadku większości scenariuszy zaakceptuj wartość domyślną **Usługa Batch**.

4. Kliknij przycisk **Utwórz**, aby utworzyć konto.



## <a name="view-batch-account-properties"></a>Wyświetlanie właściwości konta usługi Batch
Po utworzeniu konta kliknij je, aby uzyskać dostęp do jego ustawień i właściwości. Menu po lewej stronie zapewnia dostęp do wszystkich ustawień i właściwości konta.

![Strona konta usługi Batch w witrynie Azure Portal][account_blade]

* **Nazwa, adres URL i klucze konta usługi Batch**: aby po utworzeniu aplikacji za pomocą [interfejsów API usługi Batch](batch-apis-tools.md#azure-accounts-for-batch-development) uzyskać dostęp do zasobów usługi Batch, potrzebujesz adresu URL i klucza konta. (Usługa Batch obsługuje też uwierzytelnianie za pomocą usługi Azure Active Directory).

    Aby wyświetlić informacje dostępu do konta usługi Batch, kliknij pozycję **Klucze**.

    ![Klucze konta usługi Batch w witrynie Azure Portal][account_keys]

* Aby wyświetlić nazwę i klucze konta magazynu skojarzone z kontem usługi Batch, kliknij pozycję **Konto magazynu**.

* Aby wyświetlić limity przydziału zasobów dotyczące konta usługi Batch, kliknij pozycję **Limity przydziału**. Aby uzyskać szczegółowe informacje, zobacz [Limity przydziału i limity usługi Batch](batch-quota-limit.md).


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

1. W witrynie [Azure Portal][azure_portal] kliknij pozycję **Nowy** > **Bezpieczeństwo** > **Key Vault**.

2. Na stronie **Tworzenie magazynu Key Vault** wprowadź nazwę magazynu Key Vault i utwórz grupę zasobów w wymaganym regionie konta usługi Batch. Pozostaw wartości domyślne pozostałych ustawień, a następnie kliknij przycisk **Utwórz**.

Podczas tworzenia konta usługi Batch w trybie subskrypcji użytkownika użyj grupy zasobów dla magazynu kluczy, określ **subskrypcję użytkownika** jako tryb alokacji puli, a następnie wybierz magazyn kluczy.

## <a name="other-batch-account-management-options"></a>Inne opcje zarządzania kontem usługi Batch
Poza korzystaniem z witryny Azure Portal można tworzyć konta usługi Batch i zarządzać nimi za pomocą narzędzi, takich jak:

* [Polecenia cmdlet programu PowerShell usługi Batch](batch-powershell-cmdlets-get-started.md)
* [Interfejs wiersza polecenia platformy Azure](batch-cli-get-started.md)
* [Batch Management .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>Następne kroki
* Aby dowiedzieć się więcej o zasadach działania i funkcjach usługi Batch, zobacz temat [Omówienie funkcji usługi Batch](batch-api-basics.md). W artykule omówiono podstawowe zasoby usługi Batch, takie jak pule, węzły obliczeniowe, zadania i podzadania, oraz opisano funkcje dla obciążeń zasobów obliczeniowych na dużą skalę.
* Poznaj podstawy tworzenia aplikacji wykorzystujących usługę Batch za pomocą biblioteki klienta [Batch .NET](batch-dotnet-get-started.md) lub języka [Python](batch-python-tutorial.md). Niniejsze artykuły wprowadzające zawierają omówienie działającej aplikacji, która korzysta z usługi Batch do wykonywania obciążenia na wielu węzłach obliczeniowych i stosuje usługę Azure Storage do tymczasowego przechowywania i pobierania pliku obciążenia.

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[marketplace_portal]: ./media/batch-account-create-portal/marketplace-batch.png
[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch-account-portal.png
[account_keys]: ./media/batch-account-create-portal/batch-account-keys.png
[account_url]: ./media/batch-account-create-portal/account_url.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[subscription_access]: ./media/batch-account-create-portal/subscription_iam.png
[add_permission]: ./media/batch-account-create-portal/add_permission.png

