---
title: Wprowadzenie do usługi Key Vault i połączonych w programie Visual Studio (projekty ASP.NET) | Dokumentacja firmy Microsoft
description: Użyj tego samouczka, aby dowiedzieć się, jak dodać obsługę usługi Key Vault do aplikacji sieci web programu ASP.NET lub ASP.NET Core.
services: key-vault
author: ghogen
manager: douge
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 04/15/2018
ms.author: ghogen
ms.openlocfilehash: 3ca62d47d8e7682c80985bf5409b8540382fbf45
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/18/2018
ms.locfileid: "42059604"
---
# <a name="get-started-with-key-vault-connected-service-in-visual-studio-aspnet-projects"></a>Wprowadzenie do usługi Key Vault i połączonych w programie Visual Studio (projekty ASP.NET)

> [!div class="op_single_selector"]
> - [Wprowadzenie](vs-key-vault-aspnet-get-started.md)
> - [Co się stało](vs-key-vault-aspnet-what-happened.md)

Ten artykuł zawiera dodatkowe wskazówki, po dodaniu usługi Key Vault do projektu programu ASP.NET MVC za pomocą **Dodaj usługi połączone** polecenia w programie Visual Studio. Jeśli jeszcze nie dodano usługę do projektu, możesz to zrobić w dowolnej chwili wykonując instrukcje podane w [Dodaj usługi Key Vault do aplikacji sieci web przy użyciu programu Visual Studio usług połączonych](vs-key-vault-add-connected-service.md).

Zobacz [co się stało z moim projektem platformy ASP.NET?](vs-key-vault-aspnet-core-what-happened.md) zmian wprowadzonych do projektu, podczas dodawania usługi połączonej.

## <a name="after-you-connect"></a>Po nawiązaniu połączenia

1. Dodaj wpis tajny w usłudze Key Vault na platformie Azure. Można nawiązać połączenia z odpowiednim miejscu w portalu, kliknij link, aby **Zarządzaj wpisami tajnymi przechowywanymi w tym magazynie kluczy**. Jeśli został zamknięty, strony lub projektu, możesz przejść do niego w [witryny Azure portal](https://portal.azure.com) , wybierając **wszystkich usług**w obszarze **zabezpieczeń**, wybierz **usługi Key Vault**, następnie wybierz nowo utworzoną w usłudze Key Vault.

   ![Przejdź do portalu](media/vs-key-vault-add-connected-service/manage-secrets-link.jpg)

1. W sekcji Key Vault dla klucza magazynu został utworzony, wybierz polecenie **wpisów tajnych**, następnie **Generuj/Import**.

   ![Wygeneruj/zaimportuj klucz tajny](media/vs-key-vault-add-connected-service/generate-secrets.jpg)

1. Wprowadź klucz tajny, taką jak **MySecret**i nadaj mu dowolną wartość ciągu, która jest testem, a następnie wybierz **Utwórz** przycisku.

   ![Utwórz klucz tajny](media/vs-key-vault-add-connected-service/create-a-secret.jpg)
 
1. (opcjonalnie) Wprowadź inny klucz tajny, ale tym razem umieścić go w kategorii za pomocą nazw go **wpisów tajnych — MySecret**. Ta składnia określa kategorię **wpisów tajnych** zawierający wpis tajny **MySecret**.

1. Zmodyfikuj plik web.config w następujący sposób. Klucze są symbolami zastępczymi, które zostaną zastąpione przez AzureKeyVault ConfigurationBuilder z wartościami wpisów tajnych w usłudze Key Vault.

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

1. W HomeController, w metodzie kontrolera informacje Dodaj następujące wiersze do pobierania klucza tajnego i zapisz go w obiekt ViewBag.
 
   ```csharp
            var secret = ConfigurationManager.AppSettings["MySecret"];
            var secret2 = ConfigurationManager.AppSettings["Secrets--MySecret"];
            ViewBag.Secret = $"Secret: {secret}";
            ViewBag.Secret2 = $"Secret2: {secret2}";
   ```

1. W widoku About.cshtml Dodaj następujące polecenie, aby wyświetlić wartość wpisu tajnego (tylko do celów testowych).

   ```csharp
      <h3>@ViewBag.Secret</h3>
      <h3>@ViewBag.Secret2</h3>
   ```

Gratulacje, teraz możliwe jest usługa Key Vault umożliwia dostęp do danych poufnych zabezpieczonym aplikacji sieci web.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, Usuń grupę zasobów. Spowoduje to usunięcie usługi Key Vault i pokrewne zasoby. Aby usunąć grupę zasobów za pośrednictwem portalu:

1. Wprowadź nazwę grupy zasobów w polu wyszukiwania w górnej części portalu. Gdy w wynikach wyszukiwania zobaczysz grupę zasobów używaną w tym przewodniku Szybki start, wybierz ją.
2. Wybierz pozycję **Usuń grupę zasobów**.
3. W polu **WPISZ NAZWĘ GRUPY ZASOBÓW:** wpisz nazwę grupy zasobów i wybierz pozycję **Usuń**.

# <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat tworzenia usługi Key Vault w [Key Vault przewodnik dewelopera](key-vault-developers-guide.md)