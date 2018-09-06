---
title: Dodaj obsługę usługi Key Vault do projektu programu ASP.NET przy użyciu programu Visual Studio | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: d2ab34b3737ec00e4adc464f6d2255203fb6ae08
ms.sourcegitcommit: 3d0295a939c07bf9f0b38ebd37ac8461af8d461f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2018
ms.locfileid: "43840623"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Dodawanie usługi Key Vault do aplikacji sieci web za pomocą programu Visual Studio podłączone usługi

W tym samouczku dowiesz się, jak łatwo dodać wszystko, czego potrzebujesz, aby rozpocząć korzystanie z usługi Azure Key Vault do zarządzania klucze tajne dla projektów sieci web w programie Visual Studio, czy używasz platformy ASP.NET Core lub dowolnego typu projektu programu ASP.NET. Korzystając z funkcji usług połączonych programu Visual Studio 2017, możesz mieć programu Visual Studio automatycznie dodać wszystkie pakiety NuGet i ustawienia konfiguracji, które musisz nawiązać połączenie z usługi Key Vault na platformie Azure. 

Aby uzyskać więcej informacji na temat zmian dzięki usługom połączone w projekcie do włączenia usługi Key Vault, zobacz [usługi Key Vault i połączone - co się stało z Moje ASP.NET 4.7.1 projektu](vs-key-vault-aspnet-what-happened.md) lub [usługi Key Vault i połączone - co się stało z Mój projekt platformy ASP.NET Core](vs-key-vault-aspnet-core-what-happened.md).

## <a name="prerequisites"></a>Wymagania wstępne

- **Subskrypcja platformy Azure**. Jeśli jej nie masz, możesz zarejestrować się w celu [utworzenia bezpłatnego konta](https://azure.microsoft.com/pricing/free-trial/).
- **Visual Studio 2017 w wersji 15.7** z **programowania dla sieci Web** zainstalowanym obciążeniem. [Pobierz teraz](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).
- ASP.NET (nie-rdzeniowe) wymaga platformy .NET Framework 4.7.1 narzędzi deweloperskich, które nie są instalowane domyślnie. Na ich instalację, uruchom Instalatora programu Visual Studio, wybierz polecenie **Modyfikuj**, a następnie wybierz **poszczególne składniki**, następnie po prawej stronie, rozwiń węzeł **ASP.NET i tworzenie aplikacji internetowych**i wybierz polecenie **narzędzi programistycznych platformy .NET Framework 4.7.1**.
- ASP.NET 4.7.1 lub Otwórz projekt sieci web platformy ASP.NET Core 2.0.

## <a name="add-key-vault-support-to-your-project"></a>Dodanie obsługi usługi Key Vault do projektu

1. W **Eksploratora rozwiązań**, wybierz **Dodaj** > **podłączoną usługę**.
   Na stronie usługi połączonej pojawia się z usługami, które można dodać do projektu.
1. W menu dostępnych usług wybierz **zabezpieczyć wpisy tajne przy użyciu usługi Azure Key Vault**.

   ![Wybierz pozycję "Bezpieczne wpisów tajnych za pomocą usługi Azure Key Vault"](media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

   Jeśli po zalogowaniu się do programu Visual Studio i subskrypcji platformy Azure skojarzony z Twoim kontem, zostanie wyświetlona strona, która za pomocą listy rozwijanej z subskrypcjami.
1. Wybierz subskrypcję, której chcesz użyć, a następnie wybierz nowy lub istniejący magazyn kluczy, lub wybierz link edycji, aby zmodyfikować nazwy wygenerowanej automatycznie.

   ![Wybierz swoją subskrypcję](media/vs-key-vault-add-connected-service/KeyVaultConnectedService3.PNG)

1. Wpisz nazwę, którą chcesz używać usługi Key Vault.

   ![Zmień nazwę usługi Key Vault, a następnie wybierz grupę zasobów](media/vs-key-vault-add-connected-service/KeyVaultConnectedService-Edit.PNG)

1. Wybierz istniejącą grupę zasobów lub wybrać opcję utworzenia nowego z automatycznie wygenerowanego unikatowa nazwa.  Jeśli chcesz utworzyć nową grupę o innej nazwie, możesz użyć [witryny Azure Portal](https://portal.azure.com), a następnie zamknij stronę i uruchom ponownie, aby ponownie załadować listy grup zasobów.
1. Wybierz region, w której chcesz utworzyć usługi Key Vault. Jeśli aplikacja sieci web jest hostowana na platformie Azure, wybierz region, który jest hostem aplikacji sieci web w celu uzyskania optymalnej wydajności.
1. Wybierz model cenowy. Aby uzyskać więcej informacji, zobacz [cenami Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).
1. Wybierz przycisk OK, aby zaakceptować opcje konfiguracji.
1. Wybierz **Dodaj** do utworzenia magazynu kluczy. Proces tworzenia może zakończyć się niepowodzeniem, jeśli wybrana nazwa, który został już użyty.  Jeśli tak się stanie, należy użyć **Edytuj** link, aby zmienić nazwę usługi Key Vault i spróbuj ponownie.

   ![Dodawanie usługi połączonej do projektu](media/vs-key-vault-add-connected-service/KeyVaultConnectedService4.PNG)

1. Teraz Dodaj klucz tajny w usłudze Key Vault na platformie Azure. Aby uzyskać we właściwym miejscu w portalu, kliknij link dla Zarządzaj kluczy tajnych przechowywanych w tym magazynie kluczy. Jeśli został zamknięty, strony lub projektu, możesz przejść do niego w [witryny Azure portal](https://portal.azure.com) , wybierając **wszystkich usług**w obszarze **zabezpieczeń**, wybierz **usługi Key Vault**, następnie wybierz nowo utworzoną w usłudze Key Vault.

   ![Przejdź do portalu](media/vs-key-vault-add-connected-service/manage-secrets-link.jpg)

1. W sekcji Key Vault dla klucza magazynu został utworzony, wybierz polecenie **wpisów tajnych**, następnie **Generuj/Import**.

   ![Wygeneruj/zaimportuj klucz tajny](media/vs-key-vault-add-connected-service/generate-secrets.jpg)

1. Wprowadź klucz tajny, takie jak "MySecret" i nadaj mu dowolną wartość ciągu, która jest testem, a następnie wybierz **Utwórz** przycisku.

   ![Utwórz klucz tajny](media/vs-key-vault-add-connected-service/create-a-secret.jpg)

1. (opcjonalnie) Wprowadź inny klucz tajny, ale tym razem, umieścić go w kategorii, nadając mu nazwę "Wpisy tajne — MySecret". Ta składnia określa kategorię "Wpisy tajne", która zawiera wpis tajny "MySecret."
 
Teraz może uzyskać dostęp do wpisów tajnych w kodzie. Następne kroki są różne w zależności od tego, czy przy użyciu 4.7.1 platformy ASP.NET lub ASP.NET Core.

## <a name="access-your-secrets-in-code-aspnet-core-projects"></a>Dostęp do wpisów tajnych w kodzie (projektów ASP.NET Core)

Połączenie do usługi Key Vault jest ustawiana przy uruchamianiu przez klasę, która implementuje [Microsoft.AspNetCore.Hosting.IHostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup?view=aspnetcore-2.1) korzystanie z rozszerzania zachowanie podczas uruchamiania, który jest opisany w sposób [zwiększanie możliwości aplikacji z zewnętrznej zestaw w programie ASP.NET Core za pomocą interfejsu IHostingStartup](/aspnet/core/fundamentals/host/platform-specific-configuration). Klasa początkowa korzysta z dwóch zmiennych środowiskowych, które zawierają informacje o połączeniu usługi Key Vault: ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED, ustaw na true, a ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT, Ustaw klucz Adres URL magazynu. Te są dodawane do pliku launchsettings.json podczas uruchamiania za pośrednictwem **Dodaj podłączoną usługę** procesu.

Dostęp do wpisów tajnych:

1. W programie Visual Studio w projekcie platformy ASP.NET Core teraz możesz odwoływać się tych kluczy tajnych przy użyciu następujących wyrażeń w kodzie:
 
   ```csharp
      config["MySecret"] // Access a secret without a section
      config["Secrets:MySecret"] // Access a secret in a section
      config.GetSection("Secrets")["MySecret"] // Get the configuration section and access a secret in it.
   ```

1. Na stronie cshtml, powiedz About.cshtml, Dodaj @inject dyrektywę w górnej części pliku, aby zdefiniować zmienną możesz uzyskiwać dostęp do konfiguracji usługi Key Vault.

   ```cshtml
      @inject Microsoft.Extensions.Configuration.IConfiguration config
   ```

1. Jako test można potwierdzić, czy wartość wpisu tajnego jest dostępna, wyświetlając na jedną ze stron. Użyj @config można odwoływać się do zmiennej konfiguracji.
 
   ```cshtml
      <p> @config["MySecret"] </p>
      <p> @config.GetSection("Secrets")["MySecret"] </p>
      <p> @config["Secrets:MySecret"] </p>
   ```

1. Tworzenie i uruchamianie aplikacji sieci web, przejdź do strony informacje i zobaczyć wartość "wpis tajny".

## <a name="access-your-secrets-in-code-aspnet-471-projects"></a>Dostęp do wpisów tajnych w kodzie (ASP.NET 4.7.1 projektów)

Połączenie do usługi Key Vault jest ustawiana przez klasy ConfigurationBuilder, korzystając z informacji, który został dodany do pliku web.config, po uruchomieniu za pośrednictwem **Dodaj podłączoną usługę** procesu.

Dostęp do wpisów tajnych:

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

Gratulacje, teraz potwierdzeniu, że Twoja aplikacja sieci web umożliwia usługi Key Vault dostęp do danych poufnych w zabezpieczonym.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie są już potrzebne, Usuń grupę zasobów. Spowoduje to usunięcie usługi Key Vault i pokrewne zasoby. Aby usunąć grupę zasobów za pośrednictwem portalu:

1. Wprowadź nazwę grupy zasobów w polu wyszukiwania w górnej części portalu. Gdy w wynikach wyszukiwania zobaczysz grupę zasobów używaną w tym przewodniku Szybki start, wybierz ją.
2. Wybierz pozycję **Usuń grupę zasobów**.
3. W polu **WPISZ NAZWĘ GRUPY ZASOBÓW:** wpisz nazwę grupy zasobów i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o programowaniu dla usługi Key Vault, zapoznając [Key Vault przewodnik dewelopera](key-vault-developers-guide.md)