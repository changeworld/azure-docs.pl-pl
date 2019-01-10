---
title: Usługa Azure Stack magazynu poświadczeń jednostki usługi w usłudze Key Vault | Dokumentacja firmy Microsoft
description: Dowiedz się, jak usługa Key Vault przechowuje poświadczenia nazwy głównej usługi w usłudze Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2019
ms.author: sethm
ms.openlocfilehash: 570c1adc2f4615e78cbe5656c13b0e22b863baf7
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2019
ms.locfileid: "54192288"
---
# <a name="store-service-principal-credentials-in-key-vault"></a>Store poświadczenia nazwy głównej usługi w usłudze Key Vault

Opracowywanie aplikacji w usłudze Azure Stack zwykle wymaga tworzenia jednostki usługi i przy użyciu tych poświadczeń do uwierzytelniania przed wdrożeniem. Jednak często przechowywane poświadczenia jednostki usługi są utraty. W tym artykule opisano, jak można utworzyć jednostkę usługi, a następnie zapisz wartości w usłudze Azure Key Vault do nowszych pobierania.

Aby uzyskać więcej informacji na temat usługi Key Vault, zobacz [w tym artykule](azure-stack-key-vault-intro.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja z ofertą, która obejmuje usługę Azure Key Vault.
- Program PowerShell jest skonfigurowana do użycia z usługą Azure Stack.

## <a name="key-vault-in-azure-stack"></a>Magazyn kluczy w usłudze Azure Stack

Key Vault w usłudze Azure Stack ułatwia ochrony kluczy kryptograficznych i wpisów tajnych, które aplikacje i usługi w chmurze Użyj. Za pomocą usługi Key Vault, umożliwia ona szyfrowanie kluczy i wpisów tajnych.

Aby utworzyć magazyn kluczy, wykonaj następujące kroki:

1. Zaloguj się do portalu usługi Azure Stack.

2. Na pulpicie nawigacyjnym wybierz **+ Utwórz zasób**, następnie **bezpieczeństwo i Obsługa tożsamości**, a następnie wybierz **usługi Key Vault.**

   ![Tworzenie magazynu kluczy](media/azure-stack-key-vault-store-credentials/create-key-vault.png)

3. W **Tworzenie magazynu Key Vault** okienku przypisać **nazwa** dla magazynu. Nazwy magazynu może zawierać tylko znaki alfanumeryczne i znak łącznika (-). Nie należy ich zaczynać się cyfrą.

4. Wybierz subskrypcję z listy dostępnych subskrypcji.

5. Wybierz istniejącą grupę zasobów lub Utwórz nową.

6. Wybierz warstwę cenową.

7. Wybierz jedną z istniejących zasad dostępu, lub Utwórz nową. Zasady dostępu można przyznać uprawnienia dla użytkownika, aplikacji lub grupy zabezpieczeń wykonywać operacje, w tym magazynie.

8. Opcjonalnie wybierz zasady dostępu zaawansowane, aby umożliwić dostęp do funkcji.

9. Po skonfigurowaniu ustawień, wybierz **OK**, a następnie wybierz pozycję **Utwórz**. Rozpocznie się wdrożenie usługi key vault.

## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

1. Zaloguj się do konta platformy Azure za pośrednictwem witryny Azure portal.

2. Wybierz **usługi Azure Active Directory**, następnie **rejestracje aplikacji**, następnie **Dodaj**.

3. Podaj nazwę i adres URL aplikacji. Wybierz opcję **aplikacji sieci Web / interfejs API** lub **natywnych** dla typu aplikacji, którą chcesz utworzyć. Po ustawieniu wartości, wybierz **Utwórz**.

4. Wybierz **usługi Active Directory**, następnie **rejestracje aplikacji**i wybierz swoją aplikację.

5. Skopiuj **identyfikator aplikacji** i zapisz go w kodzie aplikacji. Te aplikacje w aplikacji przykładowych **identyfikator klienta** przy odwoływaniu się do **identyfikator aplikacji**.

6. Aby wygenerować klucz uwierzytelniania, wybierz pozycję **Klucze**.

7. Podaj opis klucz i wartość typu duration.

8. Wybierz pozycję **Zapisz**.

9. Kopiuj **klucz** stanie się dostępne po kliknięciu przycisku **Zapisz**.

## <a name="store-the-service-principal-inside-key-vault"></a>Store nazwy głównej usługi wewnątrz usługi Key Vault

1. Zaloguj się do portalu użytkownika dla usługi Azure Stack, a następnie wybierz magazyn kluczy, który został utworzony wcześniej, a następnie wybierz **klucz tajny** kafelka.

2. W **klucz tajny** okienku wybierz **Generuj/Import**.

3. W **Utwórz klucz tajny** okienko z listy wybierz opcje **ręczne**.

4. Wprowadź **identyfikator aplikacji** kopiowane z jednostki usługi jako nazwę klucza. Nazwa klucza może zawierać tylko znaki alfanumeryczne i znak łącznika (-).

5. Wklej wartość klucza jednostki do usługi **wartość** kartę.

6. Wybierz **nazwy głównej usługi** dla **typ zawartości**.

7. Ustaw **Data aktywacji** i **datę wygaśnięcia** wartości dla klucza.

8. Wybierz **Utwórz** rozpocząć wdrażanie.

Po pomyślnym utworzeniu wpisu tajnego, informacji o jednostce usługi będą tam przechowywane. Możesz wybrać go w dowolnym momencie w obszarze **wpisów tajnych**i wyświetlić lub zmodyfikować jego właściwości. Sekcja właściwości zawiera identyfikator wpisu tajnego, który jest jednolite zasobów identyfikator (URI) używanego przez zewnętrznym aplikacjom na dostęp do tego wpisu tajnego.

## <a name="next-steps"></a>Kolejne kroki

- [Użycie jednostki usługi](azure-stack-create-service-principals.md)
- [Zarządzanie usługi Key Vault w usłudze Azure Stack w portalu](azure-stack-key-vault-manage-portal.md)  
- [Zarządzanie usługi Key Vault w usłudze Azure Stack przy użyciu programu PowerShell](azure-stack-key-vault-manage-powershell.md)