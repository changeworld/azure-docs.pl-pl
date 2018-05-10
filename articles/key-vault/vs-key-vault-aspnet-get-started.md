---
title: Wprowadzenie do magazynu klucz połączone usługi, w programie Visual Studio (projekty programu ASP.NET) | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: cd305801f10c899682aa6d751e48f30b6e8303fa
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="get-started-with-key-vault-connected-service-in-visual-studio-aspnet-projects"></a>Wprowadzenie do magazynu klucz połączone usługi, w programie Visual Studio (projekty programu ASP.NET)

> [!div class="op_single_selector"]
> - [Wprowadzenie](vs-key-vault-aspnet-get-started.md)
> - [Co się stało](vs-key-vault-aspnet-what-happened.md)

Ten artykuł zawiera dodatkowe wskazówki po dodaniu usługi Key Vault do projektu programu ASP.NET MVC za pomocą **dodać usług połączonych** polecenia w programie Visual Studio. Jeśli usługa nie jest już zostały dodane do projektu, możesz to zrobić w dowolnym momencie zgodnie z instrukcjami w [usługi Key Vault dodać do aplikacji sieci web za pomocą programu Visual Studio usługami](vs-key-vault-add-connected-service.md).

Zobacz [co się stało z projektu programu ASP.NET?](vs-key-vault-aspnet-core-what-happened.md) , aby zmiany wprowadzone do projektu podczas dodawania podłączonej usługi.

## <a name="after-you-connect"></a>Po nawiązaniu połączenia

1. Dodawanie klucza tajnego w magazynie kluczy w usłudze Azure. Aby uzyskać we właściwym miejscu w portalu, kliknij Link, aby **Zarządzanie kluczy tajnych przechowywane w tym magazynie kluczy**. Jeśli został zamknięty, strona lub projektu, można przejść do niego w [portalu Azure](https://portal.azure.com) , wybierając **wszystkie usługi**w obszarze **zabezpieczeń**, wybierz **Key Vault**, a następnie wybierz utworzony magazyn kluczy.

   ![Przejście do portalu](media/vs-key-vault-add-connected-service/manage-secrets-link.jpg)

1. Wybierz, w sekcji magazynu kluczy dla klucza magazynu utworzonego **kluczy tajnych**, następnie **Generuj/Import**.

   ![Generowanie/Import klucz tajny](media/vs-key-vault-add-connected-service/generate-secrets.jpg)

1. Wprowadź klucz tajny, na przykład **MySecret**i nadaj mu dowolną wartość ciągu, która jest testem, a następnie wybierz **Utwórz** przycisku.

   ![Utwórz klucz tajny](media/vs-key-vault-add-connected-service/create-a-secret.jpg)
 
1. (opcjonalnie) Wprowadź inny klucz tajny, ale w tej chwili przełączyć w kategorii przez niego **kluczy tajnych — MySecret**. Ta składnia określa kategorię **kluczy tajnych** zawierający klucz tajny **MySecret**.

1. Zmodyfikuj plik web.config w następujący sposób. Klucze są symbole zastępcze, które zostaną zastąpione AzureKeyVault ConfigurationBuilder z wartości kluczy tajnych w magazynie kluczy.

   ```xml
     <appSettings configBuilders="AzureKeyVault">
       <add key="webpages:Version" value="3.0.0.0" />
       <add key="webpages:Enabled" value="false" />
       <add key="ClientValidationEnabled" value="true" />
       <add key="UnobtrusiveJavaScriptEnabled" value="true" />
       <add key="MySecret" value="dummy1"/>
       <add key="Secrets--MySecret" value="dummy2"/>
     </appSettings>
   ```

1. W HomeController, w metodzie kontrolera informacje Dodaj następujące wiersze, aby pobrać klucz tajny i zapisz go w obiekt ViewBag.
 
   ```csharp
            var secret = ConfigurationManager.AppSettings["MySecret"];
            var secret2 = ConfigurationManager.AppSettings["Secrets--MySecret"];
            ViewBag.Secret = $"Secret: {secret}";
            ViewBag.Secret2 = $"Secret2: {secret2}";
   ```

1. W widoku About.cshtml Dodaj następujący kod do wyświetlenia wartości hasła (tylko do celów testowych).

   ```csharp
      <h3>@ViewBag.Secret</h3>
      <h3>@ViewBag.Secret2</h3>
   ```

Gratulacje, możliwe jest teraz aplikacji sieci web na potrzeby magazynu kluczy dostępu zabezpieczonym kluczy tajnych.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebny, Usuń grupę zasobów. Spowoduje to usunięcie magazyn kluczy i powiązanych zasobów. Aby usunąć grupę zasobów za pośrednictwem portalu:

1. Wprowadź nazwę grupy zasobów w polu wyszukiwania w górnej części portalu. Gdy w wynikach wyszukiwania zobaczysz grupę zasobów używaną w tym przewodniku Szybki start, wybierz ją.
2. Wybierz pozycję **Usuń grupę zasobów**.
3. W polu **WPISZ NAZWĘ GRUPY ZASOBÓW:** wpisz nazwę grupy zasobów i wybierz pozycję **Usuń**.

# <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat programowania z użyciem klucza magazynu w [klucza magazynu przewodnik dewelopera](key-vault-developers-guide.md)