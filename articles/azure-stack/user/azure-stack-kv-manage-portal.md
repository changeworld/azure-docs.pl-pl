---
title: Zarządzanie usługi Key Vault w usłudze Azure Stack przy użyciu portalu | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzać usługi Key Vault w usłudze Azure Stack przy użyciu portalu
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: D4300668-461F-45F6-BF3B-33B502C39D17
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2018
ms.author: sethm
ms.openlocfilehash: 51c04a567ff953c4e84930e3feae448f78627683
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "44713939"
---
# <a name="manage-key-vault-in-azure-stack-by-using-the-portal"></a>Zarządzanie usługi Key Vault w usłudze Azure Stack przy użyciu portalu

Key Vault w usłudze Azure Stack można zarządzać za pomocą portalu usługi Azure Stack. Ten artykuł zawiera wprowadzenie do tworzenia i obsługi magazynu kluczy w usłudze Azure Stack.

## <a name="prerequisites"></a>Wymagania wstępne

Należy subskrybować ofertę, która obejmuje usługę Azure Key Vault.

## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

1. Zaloguj się do [portal użytkowników](https://portal.local.azurestack.external).

2. Na pulpicie nawigacyjnym wybierz **+ Utwórz zasób** > **bezpieczeństwo i Obsługa tożsamości** > **usługi Key Vault**.

    ![Ekranu usługi Key Vault](media/azure-stack-kv-manage-portal/image1.png)

3. W **Tworzenie magazynu Key Vault** okienku przypisać **nazwa** dla magazynu. Nazwy magazynu może zawierać wyłącznie znaki alfanumeryczne i znak specjalny łącznika (-). Nie mogą zaczynać się cyfrą.

4. Wybierz **subskrypcji** z listy dostępnych subskrypcji. Wszystkie subskrypcje, które udostępniają usługi Key Vault są wyświetlane na liście rozwijanej.

5. Wybierz istniejącą **grupę zasobów** lub utwórz nową.

6. Wybierz **warstwa cenowa**.
    >[!NOTE]
    > Klucz magazynów w obsłudze usługi Azure Stack Development Kit **standardowa** tylko jednostki SKU.

7. Wybierz jedną z istniejących **zasady dostępu** lub utworzyć nowy. Zasady dostępu umożliwia przyznawanie uprawnień dla użytkownika, aplikacji lub grupy zabezpieczeń wykonywać operacje, w tym magazynie.

8. Opcjonalnie wybierz opcję **zasad dostępu do wersji Advanced** umożliwiające dostęp do funkcji. Na przykład: maszyny wirtualne (VM) wdrożenia usługi Resource Manager dla wdrożenia szablonu, a dostęp do usługi Azure Disk Encryption dla szyfrowania woluminu.

9. Po skonfigurowaniu ustawień, wybierz **OK**, a następnie wybierz pozycję **Utwórz**. Spowoduje to uruchomienie wdrożenia usługi key vault.

## <a name="manage-keys-and-secrets"></a>Zarządzanie kluczami i wpisami tajnymi

Po utworzeniu magazynu, należy użyć następujące kroki, tworzenie i zarządzanie kluczami i wpisami tajnymi w ramach magazynu.

### <a name="create-a-key"></a>Utwórz klucz

1. Zaloguj się do [portal użytkowników](https://portal.local.azurestack.external).

2. Na pulpicie nawigacyjnym wybierz **wszystkie zasoby**, wybierz magazyn kluczy, który został utworzony wcześniej, a następnie wybierz **klucze** kafelka.

3. W **klucze** okienku wybierz **Dodaj**.

4. W **Utwórz klucz** okienko z listy **opcje**, wybierz metodę, która ma zostać użyte do utworzenia klucza. Możesz **Generuj** nowy klucz **przekazywanie** istniejącego klucza, lub użyj **przywracanie kopii zapasowej** do wybierz kopię zapasową klucza.

5. Wprowadź **nazwa** związane z kluczem. Nazwa klucza może zawierać tylko znaki alfanumeryczne i znaki specjalne łącznika (-).

6. Opcjonalnie skonfiguruj **czy ustawić datę aktywacji** i **Ustaw datę wygaśnięcia** wartości dla klucza.

7. Wybierz **Utwórz** rozpocząć wdrażanie.

Po pomyślnym utworzeniu klucza, możesz wybrać go w obszarze **klucze** i wyświetlić lub zmodyfikować jego właściwości. Sekcja właściwości zawiera **identyfikatora klucza**, który jest jednolite zasobów identyfikator (URI) używanego przez zewnętrznym aplikacjom dostępu do tego klucza. Aby ograniczyć liczbę operacji dla tego klucza, należy skonfigurować ustawienia w obszarze **dozwolone operacje**.

![Identyfikator URI klucza](media/azure-stack-kv-manage-portal/image4.png)

### <a name="create-a-secret"></a>Utwórz klucz tajny

1. Zaloguj się do [portal użytkowników](https://portal.local.azurestack.external).
2. Na pulpicie nawigacyjnym wybierz **wszystkie zasoby**, wybierz magazyn kluczy, który został utworzony wcześniej, a następnie wybierz **wpisów tajnych** kafelka.

3. W obszarze **wpisów tajnych**, wybierz opcję **Dodaj**.

4. W obszarze **Utwórz klucz tajny**, z listy **opcje przekazywania**, wybierz jedną z opcji za pomocą którego chcesz utworzyć klucz tajny. Można utworzyć wpisu tajnego **ręcznie** po wprowadzeniu wartości dla klucza tajnego lub przesłać **certyfikatu** z komputera lokalnego.

5. Wprowadź **nazwa** dla klucza tajnego. Nazwa wpisu tajnego może zawierać tylko znaki alfanumeryczne i znaki specjalne łącznika (-).

6. Opcjonalnie można określić **typ zawartości**i skonfigurować wartości **czy ustawić datę aktywacji** i **Ustaw datę wygaśnięcia** dla klucza tajnego.

7. Wybierz **Utwórz** rozpocząć wdrażanie.

Po pomyślnym utworzeniu wpisu tajnego, możesz wybrać go w obszarze **wpisów tajnych** i wyświetlić lub zmodyfikować jego właściwości. **Identyfikator wpisu tajnego** jest identyfikator URI, który umożliwia dostęp do tego wpisu tajnego zewnętrznych aplikacji.

![Identyfikator URI klucza tajnego](media/azure-stack-kv-manage-portal/image5.png)

## <a name="next-steps"></a>Kolejne kroki

* [Wdrażanie maszyny Wirtualnej przez pobieranie hasła przechowywane w usłudze Key Vault](azure-stack-kv-deploy-vm-with-secret.md)
* [Wdrażanie maszyny Wirtualnej przy użyciu certyfikatu przechowywanego w usłudze Key Vault](azure-stack-kv-push-secret-into-vm.md)
