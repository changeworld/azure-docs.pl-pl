---
title: Dodaj obsługę usługi Key Vault do projektu programu ASP.NET przy użyciu programu Visual Studio | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: 9f0cc6ee06042948442aace05d56fcffa3742a8d
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Dodaj magazyn kluczy do aplikacji sieci web za pomocą programu Visual Studio usługami

Z tego samouczka dowiesz się, jak łatwo dodać wszystkie elementy potrzebne do uruchomienia zarządzanie za pomocą usługi Azure Key Vault tajnych dla projektów sieci web w programie Visual Studio, czy używasz platformy ASP.NET Core lub dowolnego typu projektu programu ASP.NET. Korzystając z funkcji usług połączonych w Visual Studio 2017, może mieć Visual Studio automatyczne dodawanie pakietów NuGet i ustawień konfiguracji, potrzebne do nawiązania połączenia usługi Key Vault na platformie Azure. 

Aby uzyskać więcej informacji na temat zmian, dzięki usługom połączone w projekcie do włączenia usługi Key Vault, zobacz [klucza magazynu połączone usługi - co się stało z mojej ASP.NET 4.7.1 projektu](vs-key-vault-aspnet-what-happened.md) lub [klucza magazynu połączone usługi - co się stało z projektu platformy ASP.NET Core](vs-key-vault-aspnet-core-what-happened.md).

## <a name="prerequisites"></a>Wymagania wstępne

- **Subskrypcja platformy Azure**. Jeśli jej nie masz, możesz zarejestrować się w celu [utworzenia bezpłatnego konta](https://azure.microsoft.com/pricing/free-trial/).
- **Visual Studio 2017 wersji 15.7** z **projektowanie witryn sieci Web** obciążenia zainstalowane. [Pobierz teraz](https://aka.ms/vsdownload).
- Dla platformy ASP.NET (nie podstawowych) należy .NET Framework 4.7.1 narzędziami programistycznymi, które nie są instalowane domyślnie. Mają być instalowane, należy uruchomić Instalator programu Visual Studio, wybierz **Modyfikuj**, a następnie wybierz pozycję **pojedynczych składników**, następnie po prawej stronie, rozwiń węzeł **ASP.NET i sieć web development**i wybierz polecenie **narzędzia do programowania .NET Framework 4.7.1**.
- ASP.NET 4.7.1 lub Otwórz projekt sieci web platformy ASP.NET Core 2.0.

## <a name="add-key-vault-support-to-your-project"></a>Dodaj obsługę usługi Key Vault do projektu

1. W **Eksploratora rozwiązań**, wybierz **Dodaj** > **podłączonej usługi**.
   Z usług, które można dodać do projektu zostanie wyświetlona strona połączonych usług.
1. W menu dostępnych usług wybierz **bezpiecznego hasła z usługą Azure Key Vault**.

   ![Wybierz pozycję "Bezpiecznych haseł z usługi Azure Key Vault"](media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

   Jeśli po zalogowaniu do Visual Studio i mieć subskrypcję platformy Azure skojarzonych z Twoim kontem, zostanie wyświetlona strona z listy rozwijanej z subskrypcji.
1. Wybierz subskrypcję, której chcesz użyć, a następnie wybierz nowy lub istniejący magazyn kluczy, lub łącza edycji, aby zmodyfikować nazwy wygenerowanej automatycznie.

   ![Wybierz swoją subskrypcję](media/vs-key-vault-add-connected-service/KeyVaultConnectedService3.PNG)

1. Wpisz nazwę, którą chcesz użyć dla usługi Key Vault.

   ![Zmień nazwę magazynu kluczy i wybierz grupę zasobów](media/vs-key-vault-add-connected-service/KeyVaultConnectedService-Edit.PNG)

1. Wybierz istniejącą grupę zasobów, lub utworzyć nową z użyciem nazwy wygenerowanej automatycznie unqiue.  Jeśli chcesz utworzyć nową grupę o innej nazwie, możesz użyć [Azure Portal](https://portal.azure.com), a następnie zamknij stronę i uruchom ponownie, aby ponownie załadować listy grup zasobów.
1. Wybierz region, w którym ma zostać utworzony magazyn kluczy. Jeśli aplikacja sieci web jest hostowana na platformie Azure, wybierz region, który jest hostem aplikacji sieci web w celu uzyskania optymalnej wydajności.
1. Wybierz modelu cenowego. Aby uzyskać więcej informacji, zobacz [klucza magazynu cennik](https://azure.microsoft.com/pricing/details/key-vault/).
1. Kliknij przycisk OK, aby zaakceptować opcje konfiguracji.
1. Wybierz **Dodaj** do utworzenia magazynu kluczy. Proces tworzenia może zakończyć się niepowodzeniem, jeśli wybrana nazwa, który został już użyty.  Jeśli tak się stanie, należy użyć **Edytuj** łącze, aby zmienić nazwę magazynu kluczy i spróbuj ponownie.

   ![Trwa dodawanie podłączonej usługi do projektu](media/vs-key-vault-add-connected-service/KeyVaultConnectedService4.PNG)

1. Teraz Dodaj klucz tajny w magazynie kluczy na platformie Azure. Aby uzyskać we właściwym miejscu w portalu, kliknij łącze dla kluczy tajnych Zarządzaj przechowywane w tym magazynie kluczy. Jeśli został zamknięty, strona lub projektu, można przejść do niego w [portalu Azure](https://portal.azure.com) , wybierając **wszystkie usługi**w obszarze **zabezpieczeń**, wybierz **Key Vault**, a następnie wybierz utworzony magazyn kluczy.

   ![Przejście do portalu](media/vs-key-vault-add-connected-service/manage-secrets-link.jpg)

1. Wybierz, w sekcji magazynu kluczy dla klucza magazynu utworzonego **kluczy tajnych**, następnie **Generuj/Import**.

   ![Generowanie/Import klucz tajny](media/vs-key-vault-add-connected-service/generate-secrets.jpg)

1. Wprowadź klucz tajny, takie jak "MySecret" i nadaj mu dowolną wartość ciągu, która jest testem, a następnie wybierz **Utwórz** przycisku.

   ![Utwórz klucz tajny](media/vs-key-vault-add-connected-service/create-a-secret.jpg)

1. (opcjonalnie) Wprowadź inny klucz tajny, ale tym razem umieszcza je w kategorii, nadając "Kluczy tajnych--MySecret". Ta składnia określa kategorię "Kluczy tajnych", która zawiera klucz tajny "MySecret."
 
Teraz możesz uzyskać dostęp tajnych w kodzie. Następne kroki są różne w zależności od tego, czy używasz ASP.NET 4.7.1 lub ASP.NET Core.

## <a name="access-your-secrets-in-code-aspnet-core-projects"></a>Dostęp do tajnych w kodzie (projektów platformy ASP.NET Core)

1. W programie Visual Studio w projekcie platformy ASP.NET Core, teraz możesz odwoływać się tych kluczy tajnych przy użyciu następujących wyrażeń w kodzie:
 
   ```csharp
      config["MySecret"] // Access a secret without a section
      config["Secrets:MySecret"] // Access a secret in a section
      config.GetSection("Secrets")["MySecret"] // Get the configuration section and access a secret in it.
   ```

1. Na stronie .cshtml powiedzieć About.cshtml, Dodaj @inject dyrektywy w górnej części pliku, aby skonfigurować zmienną można uzyskać dostępu do konfiguracji usługi Key Vault.

   ```cshtml
      @inject Microsoft.Extensions.Configuration.IConfiguration config
   ```

1. Jako testu można potwierdzić wartość klucz tajny jest dostępna, wyświetlając go w jednym stron. Użyj @config można odwoływać się do zmiennej konfiguracji.
 
   ```cshtml
      <p> @config["MySecret"] </p>
      <p> @config.GetSection("Secrets")["MySecret"] </p>
      <p> @config["Secrets:MySecret"] </p>
   ```

1. Tworzenie i uruchomienia aplikacji sieci web, przejdź do strony informacje i widoczna wartość "secret".

## <a name="access-your-secrets-in-code-aspnet-471-projects"></a>Dostęp do tajnych w kodzie (ASP.NET 4.7.1 projekty)

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

Gratulacje, teraz potwierdzeniu aplikacji sieci web za pomocą usługi Key Vault dostęp do kluczy tajnych bezpiecznie przechowywane.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebny, Usuń grupę zasobów. Spowoduje to usunięcie magazyn kluczy i powiązanych zasobów. Aby usunąć grupę zasobów za pośrednictwem portalu:

1. Wprowadź nazwę grupy zasobów w polu wyszukiwania w górnej części portalu. Gdy w wynikach wyszukiwania zobaczysz grupę zasobów używaną w tym przewodniku Szybki start, wybierz ją.
2. Wybierz pozycję **Usuń grupę zasobów**.
3. W polu **WPISZ NAZWĘ GRUPY ZASOBÓW:** wpisz nazwę grupy zasobów i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o programowanie Key Vault, odczytując [klucza magazynu przewodnik dewelopera](key-vault-developers-guide.md)