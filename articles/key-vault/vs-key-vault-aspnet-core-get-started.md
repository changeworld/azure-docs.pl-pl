---
title: Wprowadzenie do magazynu klucz połączone usługi, w programie Visual Studio (projekty platformy ASP.NET Core) | Dokumentacja firmy Microsoft
description: Użyj tego samouczka, aby dowiedzieć się, jak dodać obsługę usługi Key Vault w aplikacji sieci web ASP.NET lub ASP.NET Core.
services: key-vault
author: ghogen
manager: douge
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 04/15/2018
ms.author: ghogen
ms.openlocfilehash: 64d1a404eac955f47308f01edd56b3d008e250a0
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="get-started-with-key-vault-connected-service-in-visual-studio-aspnet-core-projects"></a>Wprowadzenie do magazynu klucz połączone usługi, w programie Visual Studio (projekty platformy ASP.NET Core)

> [!div class="op_single_selector"]
> - [Wprowadzenie](vs-key-vault-aspnet-core-get-started.md)
> - [Co się stało](vs-key-vault-aspnet-core-what-happened.md)

Ten artykuł zawiera dodatkowe wskazówki po dodaniu usługi Key Vault do projektu platformy ASP.NET Core za pomocą **dodać usług połączonych** polecenia w programie Visual Studio. Jeśli usługa nie jest już zostały dodane do projektu, możesz to zrobić w dowolnym momencie zgodnie z instrukcjami w [usługi Key Vault dodać do aplikacji sieci web za pomocą programu Visual Studio usługami](vs-key-vault-add-connected-service.md).

Zobacz [co się stało z projektu platformy ASP.NET Core?](vs-key-vault-aspnet-core-what-happened.md) , aby zmiany wprowadzone do projektu podczas dodawania podłączonej usługi.

## <a name="after-you-connect"></a>Po nawiązaniu połączenia

1. Dodawanie klucza tajnego w magazynie kluczy w usłudze Azure. Aby uzyskać we właściwym miejscu w portalu, kliknij łącze dla kluczy tajnych Zarządzaj przechowywane w tym magazynie kluczy. Jeśli został zamknięty, strona lub projektu, można przejść do niego w [portalu Azure](https://portal.azure.com) , wybierając **wszystkie usługi**w obszarze **zabezpieczeń**, wybierz **Key Vault**, a następnie wybierz utworzony magazyn kluczy.

   ![Przejście do portalu](media/vs-key-vault-add-connected-service/manage-secrets-link.jpg)

1. Wybierz, w sekcji magazynu kluczy dla klucza magazynu utworzonego **kluczy tajnych**, następnie **Generuj/Import**.

   ![Generowanie/Import klucz tajny](media/vs-key-vault-add-connected-service/generate-secrets.jpg)

1. Wprowadź klucz tajny, takie jak "MySecret" i nadaj mu dowolną wartość ciągu, która jest testem, a następnie wybierz **Utwórz** przycisku.

   ![Utwórz klucz tajny](media/vs-key-vault-add-connected-service/create-a-secret.jpg)
 
1. (opcjonalnie) Wprowadź inny klucz tajny, ale w tej chwili przełączyć w kategorii przez niego **kluczy tajnych — MySecret**. Ta składnia określa kategorię **kluczy tajnych** zawierający klucz tajny **MySecret.**
1. W projekcie programu Visual Studio teraz możesz odwoływać się tych kluczy tajnych przy użyciu następujących wyrażeń w kodzie:
 
   ```csharp
      config["MySecret"] // Access a secret without a section
      config["Secrets:MySecret"] // Access a secret in a section
      config.GetSection("Secrets")["MySecret"] // Get the configuration section and access a secret in it.
   ```

Gratulacje, możliwe jest teraz aplikacji sieci web na potrzeby magazynu kluczy dostępu zabezpieczonym kluczy tajnych.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebny, Usuń grupę zasobów. Spowoduje to usunięcie magazyn kluczy i powiązanych zasobów. Aby usunąć grupę zasobów za pośrednictwem portalu:

1. Wprowadź nazwę grupy zasobów w polu wyszukiwania w górnej części portalu. Gdy w wynikach wyszukiwania zobaczysz grupę zasobów używaną w tym przewodniku Szybki start, wybierz ją.
2. Wybierz pozycję **Usuń grupę zasobów**.
3. W polu **WPISZ NAZWĘ GRUPY ZASOBÓW:** wpisz nazwę grupy zasobów i wybierz pozycję **Usuń**.

# <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat programowania z użyciem klucza magazynu w [klucza magazynu przewodnik dewelopera](key-vault-developers-guide.md)