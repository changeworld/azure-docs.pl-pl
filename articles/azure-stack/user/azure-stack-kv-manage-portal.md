---
title: Zarządzanie Key Vault w stosie Azure przy użyciu portalu | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzać Key Vault w stosie Azure przy użyciu portalu
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: D4300668-461F-45F6-BF3B-33B502C39D17
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2018
ms.author: mabrigg
ms.openlocfilehash: e387e365b2de3412c23c995939f75400a03eeb80
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
---
# <a name="manage-key-vault-in-azure-stack-by-using-the-portal"></a>Zarządzanie Key Vault w stosie Azure przy użyciu portalu

Magazyn kluczy Azure stosu można zarządzać za pomocą portalu Azure stosu. W tym artykule opisano, jak rozpocząć tworzenie i zarządzanie nimi magazynu kluczy Azure stosu.

## <a name="prerequisites"></a>Wymagania wstępne

Należy zasubskrybować ofertę, która obejmuje usługę Azure Key Vault.

## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

1. Zaloguj się do [portal użytkowników](https://portal.local.azurestack.external).

2. Z poziomu pulpitu nawigacyjnego, wybierz **nowy** > **bezpieczeństwo i Obsługa tożsamości** > **Key Vault**.

    ![Magazyn kluczy ekranu](media/azure-stack-kv-manage-portal/image1.png)

3. W **utworzyć magazyn kluczy** okienku przypisać **nazwa** dla magazynu. Nazwy magazynu może zawierać tylko znaki alfanumeryczne i znak specjalny łącznik (-). Ich nie powinien zaczynać się liczbą.

4. Wybierz **subskrypcji** z listy dostępnych subskrypcji. Wszystkie subskrypcje, które oferują usługi Key Vault są wyświetlane na liście rozwijanej.

5. Wybierz istniejący **grupy zasobów** lub Utwórz nową.

6. Wybierz **warstwa cenowa**.
    >[!NOTE]
    > Klucz magazynów w obsłudze Azure stosu Development Kit **standardowe** tylko jednostki SKU.

7. Wybierz jedną z istniejących **zasady dostępu** lub Utwórz nową. Zasady dostępu umożliwia udzielić uprawnień dla użytkownika, aplikacji lub grupy zabezpieczeń w celu wykonania operacji w tym magazynie.

8. Opcjonalnie wybierz **zasad dostępu zaawansowane** umożliwiające dostęp do funkcji. Na przykład: maszynach wirtualnych (VM) do wdrożenia, Menedżera zasobów dla wdrożenia szablonu, a dostęp do szyfrowania dysków Azure dla woluminu.

9. Po skonfigurowaniu ustawień, wybierz **OK**, a następnie wybierz **Utwórz**. Spowoduje to uruchomienie wdrożenia magazynu kluczy.

## <a name="manage-keys-and-secrets"></a>Zarządzanie kluczy i kluczy tajnych

Po utworzeniu magazynu następujące kroki umożliwiają tworzenie i zarządzanie nimi kluczy i kluczy tajnych w magazynie.

### <a name="create-a-key"></a>Utwórz klucz

1. Zaloguj się do [portal użytkowników](https://portal.local.azurestack.external).

2. Z poziomu pulpitu nawigacyjnego, wybierz **wszystkie zasoby**, wybierz magazyn kluczy, który został utworzony wcześniej, a następnie wybierz **klucze** kafelka.

3. W **klucze** okienku wybierz **Dodaj**.

4. W **Utwórz klucz** okienko z listy **opcje**, wybierz metodę, która ma być używana do tworzenia klucza. Możesz **Generuj** nowy klucz **przekazać** istniejącego klucza, lub użyj **przywracanie kopii zapasowej** Wybierz kopię zapasową klucza.

5. Wprowadź **nazwa** klucza. Nazwa klucza może zawierać tylko znaki alfanumeryczne i znak specjalny łącznik (-).

6. Opcjonalnie możesz skonfigurować **ustawienie daty aktywacji** i **Ustawianie daty wygaśnięcia** wartości dla klucza.

7. Wybierz **Utwórz** do wdrożenia.

Po pomyślnym utworzeniu klucza, możesz wybrać je w obszarze **klucze** i wyświetlić lub zmodyfikować jego właściwości. Sekcja właściwości zawiera **identyfikator klucza**, która jest zasobów identyfikator URI (Uniform) korzystające z aplikacjami zewnętrznymi dostępu do tego klucza. Aby ograniczyć liczbę operacji do tego klucza, należy skonfigurować ustawienia w obszarze **dozwolone operacje**.

![Identyfikator URI klucza](media/azure-stack-kv-manage-portal/image4.png)

### <a name="create-a-secret"></a>Utwórz klucz tajny

1. Zaloguj się do [portal użytkowników](https://portal.local.azurestack.external).
2. Z poziomu pulpitu nawigacyjnego, wybierz **wszystkie zasoby**, wybierz magazyn kluczy, który został utworzony wcześniej, a następnie wybierz **kluczy tajnych** kafelka.

3. W obszarze **kluczy tajnych**, wybierz pozycję **Dodaj**.

4. W obszarze **Utwórz klucz tajny**, z listy **opcje przekazywania**, wybierz jedną z opcji, według której ma zostać utworzony klucz tajny. Można utworzyć klucza tajnego **ręcznie** po wprowadzeniu wartości dla klucza tajnego lub przekazywania **certyfikatu** z komputera lokalnego.

5. Wprowadź **nazwa** dla klucza tajnego. Nazwa klucza tajnego może zawierać tylko znaki alfanumeryczne i znak specjalny łącznik (-).

6. Opcjonalnie określ **typ zawartości**i skonfigurować wartości **ustawienie daty aktywacji** i **Ustawianie daty wygaśnięcia** dla klucza tajnego.

7. Wybierz **Utwórz** do wdrożenia.

Po pomyślnym utworzeniu klucz tajny, możesz wybrać je w obszarze **kluczy tajnych** i wyświetlić lub zmodyfikować jego właściwości. **Identyfikator klucza tajnego** jest identyfikatorem URI, który umożliwia dostęp do tego klucza tajnego aplikacji zewnętrznych.

![Identyfikator URI klucza tajnego](media/azure-stack-kv-manage-portal/image5.png)

## <a name="next-steps"></a>Kolejne kroki

* [Wdrożenie maszyny Wirtualnej przez pobranie hasła przechowywane w magazynie kluczy](azure-stack-kv-deploy-vm-with-secret.md)
* [Wdrożenie maszyny Wirtualnej z certyfikatem przechowywane w magazynie kluczy](azure-stack-kv-push-secret-into-vm.md)
