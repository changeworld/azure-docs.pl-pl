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
ms.date: 01/04/2019
ms.author: sethm
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: bc73e80acddaac8676b4cb47c0b9ced2467cdc44
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2019
ms.locfileid: "55245460"
---
# <a name="manage-key-vault-in-azure-stack-by-using-the-portal"></a>Zarządzanie usługi Key Vault w usłudze Azure Stack przy użyciu portalu

Key Vault w usłudze Azure Stack można zarządzać za pomocą portalu usługi Azure Stack. W tym artykule opisano, jak tworzyć i zarządzać nimi magazynu kluczy w usłudze Azure Stack.

## <a name="prerequisites"></a>Wymagania wstępne

Należy subskrybować ofertę, która obejmuje usługę Azure Key Vault.

## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

1. Zaloguj się do [portal użytkowników](https://portal.local.azurestack.external).

2. Na pulpicie nawigacyjnym wybierz **+ Utwórz zasób**, następnie **bezpieczeństwo i Obsługa tożsamości**, następnie **usługi Key Vault**.

    ![Ekranu usługi Key Vault](media/azure-stack-key-vault-manage-portal/image1.png)

3. W **Tworzenie magazynu Key Vault** okienku przypisać **nazwa** dla magazynu. Nazwy magazynu może zawierać tylko znaki alfanumeryczne i znak łącznika (-). Nie należy ich zaczynać się cyfrą.

4. Wybierz **subskrypcji** z listy dostępnych subskrypcji. Wszystkie subskrypcje, które udostępniają usługi Key Vault są wyświetlane na liście rozwijanej.

5. Wybierz istniejącą **grupy zasobów**, lub utworzyć nowy.

6. Wybierz **warstwa cenowa**. Magazyny kluczy w usłudze Azure Stack Development Kit (ASDK), obsługa **standardowa** tylko jednostki SKU.

7. Wybierz jedną z istniejących **zasady dostępu** lub utworzyć nowy. Zasady dostępu umożliwia przyznawanie uprawnień dla użytkownika, aplikacji lub grupy zabezpieczeń wykonywać operacje, w tym magazynie.

8. Opcjonalnie wybierz opcję **zasad dostępu do wersji Advanced** umożliwiające dostęp do funkcji. Na przykład: maszyny wirtualne (VM) wdrożenia usługi Resource Manager dla wdrożenia szablonu, a dostęp do usługi Azure Disk Encryption dla szyfrowania woluminu.

9. Po skonfigurowaniu ustawień, wybierz **OK**, a następnie wybierz pozycję **Utwórz**. Spowoduje to uruchomienie wdrożenia usługi key vault.

## <a name="manage-keys-and-secrets"></a>Zarządzanie kluczami i wpisami tajnymi

Po utworzeniu magazynu, należy użyć poniższej procedury do tworzenia i obsługi kluczy i wpisów tajnych w magazynie.

### <a name="create-a-key"></a>Utwórz klucz

1. Zaloguj się do [portal użytkowników](https://portal.local.azurestack.external).

2. Na pulpicie nawigacyjnym wybierz **wszystkie zasoby**, wybierz magazyn kluczy, który został utworzony wcześniej, a następnie wybierz **klucze** kafelka.

3. W **klucze** okienku wybierz **Dodaj**.

4. W **Utwórz klucz** okienko z listy **opcje**, wybierz metodę, która ma zostać użyte do utworzenia klucza. Możesz **Generuj** nowy klucz **przekazywanie** istniejącego klucza, lub użyj **przywracanie kopii zapasowej** do wybierz kopię zapasową klucza.

5. Wprowadź **nazwa** związane z kluczem. Nazwa klucza może zawierać tylko znaki alfanumeryczne i znak łącznika (-).

6. Opcjonalnie skonfiguruj **czy ustawić datę aktywacji** i **Ustaw datę wygaśnięcia** wartości dla klucza.

7. Wybierz **Utwórz** rozpocząć wdrażanie.

Po pomyślnym utworzeniu klucza, możesz wybrać go w obszarze **klucze** i wyświetlić lub zmodyfikować jego właściwości. Sekcja właściwości zawiera **identyfikatora klucza**, który jest jednolite zasobów identyfikator (URI) używanego przez zewnętrznym aplikacjom dostępu do tego klucza. Aby ograniczyć liczbę operacji dla tego klucza, należy skonfigurować ustawienia w obszarze **dozwolone operacje**.

![Identyfikator URI klucza](media/azure-stack-key-vault-manage-portal/image4.png)

### <a name="create-a-secret"></a>Utwórz klucz tajny

1. Zaloguj się do [portal użytkowników](https://portal.local.azurestack.external).

2. Na pulpicie nawigacyjnym wybierz **wszystkie zasoby**, wybierz magazyn kluczy, który został utworzony wcześniej, a następnie wybierz **wpisów tajnych** kafelka.

3. W obszarze **wpisów tajnych**, wybierz opcję **Dodaj**.

4. W obszarze **Utwórz klucz tajny**, z listy **opcje przekazywania**, wybierz jedną z opcji, z którym chcesz utworzyć klucz tajny. Można utworzyć wpisu tajnego **ręcznie** po wprowadzeniu wartości dla klucza tajnego lub przesłać **certyfikatu** z komputera lokalnego.

5. Wprowadź **nazwa** dla klucza tajnego. Nazwa wpisu tajnego może zawierać tylko znaki alfanumeryczne i znak łącznika (-).

6. Opcjonalnie można określić **typ zawartości**i skonfigurować wartości **czy ustawić datę aktywacji** i **Ustaw datę wygaśnięcia** dla klucza tajnego.

7. Wybierz **Utwórz** rozpocząć wdrażanie.

Po pomyślnym utworzeniu wpisu tajnego, możesz wybrać go w obszarze **wpisów tajnych** i wyświetlić lub zmodyfikować jego właściwości. **Identyfikator wpisu tajnego** jest identyfikator URI, który umożliwia dostęp do tego wpisu tajnego zewnętrznych aplikacji.

![Identyfikator URI klucza tajnego](media/azure-stack-key-vault-manage-portal/image5.png)

## <a name="next-steps"></a>Kolejne kroki

* [Wdrażanie maszyny Wirtualnej przez pobieranie hasła przechowywane w usłudze Key Vault](azure-stack-key-vault-deploy-vm-with-secret.md)
* [Wdrażanie maszyny Wirtualnej przy użyciu certyfikatu przechowywanego w usłudze Key Vault](azure-stack-key-vault-push-secret-into-vm.md)
