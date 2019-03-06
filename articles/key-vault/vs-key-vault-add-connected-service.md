---
title: Dodaj obsługę usługi Key Vault do projektu programu ASP.NET przy użyciu programu Visual Studio — usługi Azure Key Vault | Dokumentacja firmy Microsoft
description: Użyj tego samouczka, aby dowiedzieć się, jak dodać obsługę usługi Key Vault do aplikacji sieci web programu ASP.NET lub ASP.NET Core.
services: key-vault
author: ghogen
manager: douge
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: ghogen
ms.openlocfilehash: 098958cd729f1c616a93bbb5264445cb6d877f8b
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2019
ms.locfileid: "57441988"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Dodawanie usługi Key Vault do aplikacji sieci web za pomocą programu Visual Studio podłączone usługi

W tym samouczku dowiesz się, jak łatwo dodać wszystko, czego potrzebujesz, aby rozpocząć korzystanie z usługi Azure Key Vault do zarządzania klucze tajne dla projektów sieci web w programie Visual Studio, czy używasz platformy ASP.NET Core lub dowolnego typu projektu programu ASP.NET. Korzystając z funkcji usług połączonych programu Visual Studio 2017, możesz mieć programu Visual Studio automatycznie dodać wszystkie pakiety NuGet i ustawienia konfiguracji, które musisz nawiązać połączenie z usługi Key Vault na platformie Azure. 

Aby uzyskać więcej informacji na temat zmian dzięki usługom połączone w projekcie do włączenia usługi Key Vault, zobacz [usługi Key Vault i połączone - co się stało z Moje ASP.NET 4.7.1 projektu](vs-key-vault-aspnet-what-happened.md) lub [usługi Key Vault i połączone - co się stało z Mój projekt platformy ASP.NET Core](vs-key-vault-aspnet-core-what-happened.md).

## <a name="prerequisites"></a>Wymagania wstępne

- **Subskrypcja platformy Azure**. Jeśli jej nie masz, możesz zarejestrować się w celu [utworzenia bezpłatnego konta](https://azure.microsoft.com/pricing/free-trial/).
- Program **Visual Studio 2017 w wersji 15.7** z zainstalowanym pakietem roboczym **Tworzenie aplikacji internetowych**. [Pobierz go teraz](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).
- ASP.NET (nie-rdzeniowe) wymaga platformy .NET Framework 4.7.1 narzędzi deweloperskich, które nie są instalowane domyślnie. Na ich instalację, uruchom Instalatora programu Visual Studio, wybierz polecenie **Modyfikuj**, a następnie wybierz **poszczególne składniki**, następnie po prawej stronie, rozwiń węzeł **ASP.NET i tworzenie aplikacji internetowych**i wybierz polecenie **narzędzi programistycznych platformy .NET Framework 4.7.1**.
- ASP.NET 4.7.1 lub Otwórz projekt sieci web platformy ASP.NET Core 2.0.

## <a name="add-key-vault-support-to-your-project"></a>Dodanie obsługi usługi Key Vault do projektu

1. W **Eksploratorze rozwiązań** wybierz pozycję **Dodaj** > **Usługa połączona**.
   Zostanie wyświetlona strona Usługa połączona zawierająca usługi, które możesz dodać do projektu.
1. W menu dostępnych usług wybierz **zabezpieczyć wpisy tajne przy użyciu usługi Azure Key Vault**.

   ![Wybierz pozycję "Bezpieczne wpisów tajnych za pomocą usługi Azure Key Vault"](media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

   Jeśli logowanie do programu Visual Studio zostało już wykonane i masz subskrypcję platformy Azure skojarzoną z kontem, zostanie wyświetlona strona z listą rozwijaną zawierającą Twoje subskrypcje. Upewnij się, że po zarejestrowaniu się w programie Visual Studio i że to konto po zarejestrowaniu się przy użyciu tego samego konta, której użyjesz dla Twojej subskrypcji platformy Azure.

1. Wybierz subskrypcję, której chcesz użyć, a następnie wybierz nowy lub istniejący magazyn kluczy, lub wybierz link edycji, aby zmodyfikować nazwy wygenerowanej automatycznie.

   ![Wybierz swoją subskrypcję](media/vs-key-vault-add-connected-service/KeyVaultConnectedService3.PNG)

1. Wpisz nazwę, którą chcesz używać usługi Key Vault.

   ![Zmień nazwę usługi Key Vault, a następnie wybierz grupę zasobów](media/vs-key-vault-add-connected-service/KeyVaultConnectedService-Edit.PNG)

1. Wybierz istniejącą grupę zasobów lub wybrać opcję utworzenia nowego z automatycznie wygenerowaną unikatową nazwę.  Jeśli chcesz utworzyć nową grupę o innej nazwie, możesz użyć [witryny Azure Portal](https://portal.azure.com), a następnie zamknij stronę i uruchom ponownie, aby ponownie załadować listy grup zasobów.
1. Wybierz region, w której chcesz utworzyć usługi Key Vault. Jeśli aplikacja sieci web jest hostowana na platformie Azure, wybierz region, który jest hostem aplikacji sieci web w celu uzyskania optymalnej wydajności.
1. Wybierz model cenowy. Aby uzyskać więcej informacji, zobacz [cenami Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).
1. Wybierz przycisk OK, aby zaakceptować opcje konfiguracji.
1. Wybierz **Dodaj** do utworzenia magazynu kluczy. Proces tworzenia może zakończyć się niepowodzeniem, jeśli wybrana nazwa, który został już użyty.  Jeśli tak się stanie, należy użyć **Edytuj** link, aby zmienić nazwę usługi Key Vault i spróbuj ponownie.

   ![Dodawanie usługi połączonej do projektu](media/vs-key-vault-add-connected-service/KeyVaultConnectedService4.PNG)

1. Teraz Dodaj klucz tajny w usłudze Key Vault na platformie Azure. Aby uzyskać we właściwym miejscu w portalu, kliknij link dla Zarządzaj kluczy tajnych przechowywanych w tym magazynie kluczy. Jeśli został zamknięty, strony lub projektu, możesz przejść do niego w [witryny Azure portal](https://portal.azure.com) , wybierając **wszystkich usług**w obszarze **zabezpieczeń**, wybierz **usługi Key Vault**, następnie wybierz utworzoną w usłudze Key Vault.

   ![Przejdź do portalu](media/vs-key-vault-add-connected-service/manage-secrets-link.jpg)

1. W sekcji Key Vault dla klucza magazynu został utworzony, wybierz polecenie **wpisów tajnych**, następnie **Generuj/Import**.

   ![Wygeneruj/zaimportuj klucz tajny](media/vs-key-vault-add-connected-service/generate-secrets.jpg)

1. Wprowadź klucz tajny, takie jak "MySecret" i nadaj mu dowolną wartość ciągu, która jest testem, a następnie wybierz **Utwórz** przycisku.

   ![Utwórz klucz tajny](media/vs-key-vault-add-connected-service/create-a-secret.jpg)

1. (opcjonalnie) Wprowadź inny klucz tajny, ale tym razem, umieścić go w kategorii, nadając mu nazwę "Wpisy tajne — MySecret". Ta składnia określa kategorię "Wpisy tajne", która zawiera wpis tajny "MySecret."
 
Teraz może uzyskać dostęp do wpisów tajnych w kodzie. Następne kroki są różne w zależności od tego, czy przy użyciu 4.7.1 platformy ASP.NET lub ASP.NET Core.

## <a name="access-your-secrets-in-code"></a>Dostęp do wpisów tajnych w kodzie

1. Zainstaluj te dwa pakiety nuget [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) i [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet biblioteki.

2. Otwórz plik Program.cs i zaktualizować kod następującym kodem: 
```
    public class Program
    {
        public static void Main(string[] args)
        {
            BuildWebHost(args).Run();
        }

        public static IWebHost BuildWebHost(string[] args) =>
           WebHost.CreateDefaultBuilder(args)
               .ConfigureAppConfiguration((ctx, builder) =>
               {
                   var keyVaultEndpoint = GetKeyVaultEndpoint();
                   if (!string.IsNullOrEmpty(keyVaultEndpoint))
                   {
                       var azureServiceTokenProvider = new AzureServiceTokenProvider();
                       var keyVaultClient = new KeyVaultClient(
                           new KeyVaultClient.AuthenticationCallback(
                               azureServiceTokenProvider.KeyVaultTokenCallback));
                       builder.AddAzureKeyVault(
                           keyVaultEndpoint, keyVaultClient, new DefaultKeyVaultSecretManager());
                   }
               }
            ).UseStartup<Startup>()
             .Build();

        private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
    }
```
3. Następnie otwórz plik About.cshtml.cs i Zapisz poniższy kod
    1. Zawiera odwołanie do Microsoft.Extensions.Configuration to, za pomocą instrukcji    
        ```
        using Microsoft.Extensions.Configuration
        ```
    2. Dodaj ten konstruktor
        ```
        public AboutModel(IConfiguration configuration)
        {
            _configuration = configuration;
        }
        ```
    3. Metoda OnGet aktualizacji. Zaktualizuj wartość symbolu zastępczego tutaj pokazane z nazwa wpisu tajnego, utworzony w powyższych poleceń
        ```
        public void OnGet()
        {
            //Message = "Your application description page.";
            Message = "My key val = " + _configuration["<YourSecretNameThatWasCreatedAbove>"];
        }
        ```

Uruchom aplikację lokalnie, przechodząc do strony. Należy pobrać wartość wpisu tajnego

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów nie jest już potrzebna, usuń ją. Spowoduje to usunięcie usługi Key Vault i pokrewne zasoby. Aby usunąć grupę zasobów za pośrednictwem portalu:

1. Wprowadź nazwę grupy zasobów w polu wyszukiwania w górnej części portalu. Gdy w wynikach wyszukiwania zobaczysz grupę zasobów używaną w tym przewodniku Szybki start, wybierz ją.
2. Wybierz pozycję **Usuń grupę zasobów**.
3. W polu **WPISZ NAZWĘ GRUPY ZASOBÓW:** wpisz nazwę grupy zasobów i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o programowaniu dla usługi Key Vault, zapoznając [Key Vault przewodnik dewelopera](key-vault-developers-guide.md)