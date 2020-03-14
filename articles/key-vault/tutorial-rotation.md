---
title: Samouczek rotacji pojedynczego użytkownika/hasła
description: Ten samouczek służy do automatyzowania rotacji pojedynczego użytkownika/hasła
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 01/26/2020
ms.author: mbaldwin
ms.openlocfilehash: 890932f7c0e46a2c9c0b6e1cf1461e4d7d25b409
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79239371"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-with-single-userpassword-authentication"></a>Automatyzacja rotacji tajnych zasobów za pomocą uwierzytelniania pojedynczego użytkownika/hasła

Chociaż najlepszym sposobem na uwierzytelnianie w usługach Azure jest użycie [tożsamości zarządzanej](managed-identity.md), istnieją pewne scenariusze, w których nie jest to opcja. W takich przypadkach są używane klucze dostępu lub wpisy tajne. Klucze dostępu lub wpisy tajne powinny być okresowo obracane.

W tym samouczku pokazano, jak zautomatyzować okresowe rotacje wpisów tajnych dla baz danych i usług przy użyciu uwierzytelniania pojedynczego użytkownika/hasła. W tym scenariuszu są obracane hasła programu SQL Server przechowywane w magazynie kluczy przy użyciu funkcji wyzwalanej przez Event Grid powiadomienia:

![Diagram obrotu](./media/rotate1.png)

1. Trzydzieści dni przed datą wygaśnięcia wpisu tajnego Key Vault Opublikuj zdarzenie "niedługo wygasnąć", aby Event Grid.
1. Event Grid sprawdza subskrypcje zdarzeń i przy użyciu protokołu HTTP Post wywołuje punkt końcowy aplikacja funkcji subskrybowany do tego zdarzenia.
1. Aplikacja funkcji otrzymuje informacje o kluczu tajnym, generuje nowe hasło losowe i tworzy nową wersję dla wpisu tajnego z nowym hasłem w Key Vault.
1. Aplikacja funkcji aktualizuje SQL przy użyciu nowego hasła.

> [!NOTE]
> Może istnieć opóźnienie między krokami 3 i 4 i w tym okresie tajnym w Key Vault nie będzie prawidłowym uwierzytelnianiem w programie SQL Server. W przypadku awarii w którymkolwiek z kroków Event Grid ponownych prób przez 2 godziny.

## <a name="setup"></a>Konfigurowanie

## <a name="create-a-key-vault-and-sql-server"></a>Tworzenie magazynu kluczy i programu SQL Server

Przed rozpoczęciem należy utworzyć Key Vault, utworzyć SQL Server i bazę danych oraz przechowywać SQL Server hasło administratora w Key Vault.

Ten samouczek używa wstępnie utworzonego szablonu Azure Resource Manager do tworzenia składników programu. Cały kod można znaleźć tutaj: [przykład podstawowego szablonu rotacji Secret](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/arm-templates).

1. Kliknij link wdrożenie szablonu platformy Azure:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Finitial-setup%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. W polu "Grupa zasobów" Wybierz pozycję "Utwórz nową" i nadaj jej nazwę "simplerotation".
1. Wybierz pozycję "Kup".

    ![Utwórz nową grupę zasobów](./media/rotate2.png)

Po wykonaniu tych kroków będziesz dysponować magazynem kluczy, programem SQL Server i bazą danych SQL. Można to sprawdzić w terminalu interfejsu wiersza polecenia platformy Azure, uruchamiając:

```azurecli
az resource list -o table
```

Wyniki będą wyglądać następująco:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv             simplerotation             eastus      Microsoft.KeyVault/vaults
simplerotation-sql            simplerotation             eastus      Microsoft.Sql/servers
simplerotation-sql/master     simplerotation             eastus      Microsoft.Sql/servers/databases
```

## <a name="create-function-app"></a>Utwórz aplikacja funkcji

Utwórz aplikacja funkcji z tożsamością zarządzaną przez system, a także dodatkowymi wymaganymi składnikami: 

Aplikacja funkcji wymaga następującej części składników i konfiguracji:
- Plan usługi App Service
- Konto magazynu
- Zasady dostępu w celu uzyskiwania dostępu do wpisów tajnych w Key Vault przy użyciu tożsamości zarządzanej aplikacja funkcji

1. Kliknij link wdrożenie szablonu platformy Azure:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Ffunction-app%2Fazuredeploy.json" target="_blank"><img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. W obszarze "Grupa zasobów" Wybierz pozycję "simplerotation".
1. Wybierz pozycję "Kup".

   ![Ekran kupowania](./media/rotate3.png)

Po wykonaniu powyższych kroków będziesz mieć konto magazynu, farmę serwerów i aplikacja funkcji.  Można to sprawdzić w terminalu interfejsu wiersza polecenia platformy Azure, uruchamiając:

```azurecli
az resource list -o table
```

Wyniki będą wyglądać następująco:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv             simplerotation             eastus      Microsoft.KeyVault/vaults
simplerotation-sql            simplerotation             eastus      Microsoft.Sql/servers
simplerotation-sql/master     simplerotation             eastus      Microsoft.Sql/servers/databases
simplerotationstrg            simplerotation             eastus      Microsoft.Storage/storageAccounts
simplerotation-plan           simplerotation             eastus      Microsoft.Web/serverFarms
simplerotation-fn             simplerotation             eastus      Microsoft.Web/sites
```

Aby uzyskać informacje dotyczące sposobu tworzenia aplikacja funkcji i używania tożsamości zarządzanej w celu uzyskania dostępu do Key Vault, zobacz [Tworzenie aplikacji funkcji na podstawie Azure Portal](../azure-functions/functions-create-function-app-portal.md) i [udostępnianie Key Vault uwierzytelniania przy użyciu tożsamości zarządzanej](managed-identity.md)

### <a name="rotation-function-and-deployment"></a>Funkcja rotacji i wdrażanie
Funkcja używa zdarzenia jako wyzwalacza i przeprowadzenia rotacji Key Vault i bazy danych SQL.

#### <a name="function-event-trigger-handler"></a>Procedura obsługi wyzwalacza zdarzeń funkcji

Funkcja poniżej odczytuje dane zdarzenia i wykonuje logikę rotacji

```csharp
public static class SimpleRotationEventHandler
{
    [FunctionName("SimpleRotation")]
       public static void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
       {
            log.LogInformation("C# Event trigger function processed a request.");
            var secretName = eventGridEvent.Subject;
            var secretVersion = Regex.Match(eventGridEvent.Data.ToString(), "Version\":\"([a-z0-9]*)").Groups[1].ToString();
            var keyVaultName = Regex.Match(eventGridEvent.Topic, ".vaults.(.*)").Groups[1].ToString();
            log.LogInformation($"Key Vault Name: {keyVaultName}");
            log.LogInformation($"Secret Name: {secretName}");
            log.LogInformation($"Secret Version: {secretVersion}");

            SeretRotator.RotateSecret(log, secretName, secretVersion, keyVaultName);
        }
}
```

#### <a name="secret-rotation-logic"></a>Logika rotacji wpisów tajnych
Ta metoda rotacji odczytuje informacje o bazie danych z wpisu tajnego, tworzy nową wersję wpisu tajnego i aktualizuje bazę danych przy użyciu nowego klucza tajnego.

```csharp
public class SecretRotator
    {
       private const string UserIdTagName = "UserID";
       private const string DataSourceTagName = "DataSource";
       private const int SecretExpirationDays = 31;

    public static void RotateSecret(ILogger log, string secretName, string secretVersion, string keyVaultName)
    {
           //Retrieve Current Secret
           var kvUri = "https://" + keyVaultName + ".vault.azure.net";
           var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
           KeyVaultSecret secret = client.GetSecret(secretName, secretVersion);
           log.LogInformation("Secret Info Retrieved");
        
           //Retrieve Secret Info
           var userId = secret.Properties.Tags.ContainsKey(UserIdTagName) ?  
                        secret.Properties.Tags[UserIdTagName] : "";
           var datasource = secret.Properties.Tags.ContainsKey(DataSourceTagName) ? 
                            secret.Properties.Tags[DataSourceTagName] : "";
           log.LogInformation($"Data Source Name: {datasource}");
           log.LogInformation($"User Id Name: {userId}");
        
           //create new password
           var randomPassword = CreateRandomPassword();
           log.LogInformation("New Password Generated");
        
           //Check db connection using existing secret
           CheckServiceConnection(secret);
           log.LogInformation("Service Connection Validated");
                    
           //Create new secret with generated password
           CreateNewSecretVersion(client, secret, randomPassword);
           log.LogInformation("New Secret Version Generated");
        
           //Update db password
           UpdateServicePassword(secret, randomPassword);
           log.LogInformation("Password Changed");
           log.LogInformation($"Secret Rotated Succesffuly");
    }
}
```
Cały kod można znaleźć tutaj: https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/rotation-function

#### <a name="function-deployment"></a>Wdrożenie funkcji

1. Pobierz plik zip aplikacji funkcji: https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-fn.zip

1. Przekaż plik simplerotationsample-Fn. zip do Azure Cloud Shell.
 
1. Użyj poniższego polecenia CLI, aby wdrożyć plik zip w aplikacji funkcji:

```azurecli
az functionapp deployment source config-zip -g simplerotation -n simplerotation-fn --src /home/{firstname e.g jack}/simplerotationsample-fn.zip
```
![Ekran kupowania](./media/rotate4.png)

Po wdrożeniu należy zauważyć dwie funkcje w obszarze simplerotation-fn:

![Azure Cloud Shell](./media/rotate5.png)

### <a name="add-event-subscription-for-secretnearexpiry-event"></a>Dodaj subskrypcję zdarzeń dla zdarzenia "SecretNearExpiry"

Skopiuj klucz eventgrid_extension aplikacji funkcji.

![Azure Cloud Shell](./media/rotate6.png)

![Testowanie i weryfikowanie](./media/rotate7.png)

Użyj skopiowanego klucza rozszerzenia eventgrid oraz identyfikatora subskrypcji w poniższym poleceniu, aby utworzyć subskrypcję usługi Event Grid dla zdarzeń SecretNearExpiry.

```azurecli
az eventgrid event-subscription create --name simplerotation-eventsubscription --source-resource-id "/subscriptions/<subscription-id>/resourceGroups/simplerotation/providers/Microsoft.KeyVault/vaults/simplerotation-kv" --endpoint "https://simplerotation-fn.azurewebsites.net/runtime/webhooks/EventGrid?functionName=SimpleRotation&code=<extension-key>" --endpoint-type WebHook --included-event-types "Microsoft.KeyVault.SecretNearExpiry"
```

### <a name="add-secret-to-key-vault"></a>Dodaj klucz tajny do Key Vault
Ustaw zasady dostępu, aby nadać użytkownikom uprawnienia do zarządzania wpisami tajnymi.

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name simplerotation-kv --secret-permissions set delete get list
```

Teraz Utwórz nowy wpis tajny z tagami zawierającymi źródło danych SQL i identyfikator użytkownika z datą wygaśnięcia ustawioną na jutro.

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name sqluser --vault-name simplerotation-kv --value "Simple123" --tags "UserID=azureuser" "DataSource=simplerotation-sql.database.windows.net" --expires $tomorrowDate
```

Tworzenie wpisu tajnego z krótką datą wygaśnięcia spowoduje natychmiastowe opublikowanie zdarzenia SecretNearExpiry, co spowoduje, że funkcja zostanie wyłączona w celu obrócenia klucza tajnego.

### <a name="test-and-verify"></a>Testowanie i weryfikowanie
Po kilku minutach klucz tajny sqluser powinien automatycznie zostać obrócony.

Aby sprawdzić weryfikację rotacji wpisów tajnych, przejdź do Key Vault > wpisy tajne

![Testowanie i weryfikowanie](./media/rotate8.png)

Otwórz klucz tajny "sqluser" i Wyświetl oryginalną i obróconą wersję

![Testowanie i weryfikowanie](./media/rotate9.png)

## <a name="create-web-app"></a>Tworzenie aplikacji internetowej

Aby zweryfikować poświadczenia SQL, Utwórz aplikację sieci Web. Ta aplikacja sieci Web pobierze klucz tajny z magazynu kluczy, wyodrębni informacje o bazie danych SQL i poświadczenia z klucza tajnego i przetestuje połączenie z programem SQL.

Aplikacja sieci Web wymaga następującej części składników i konfiguracji:
- Aplikacja internetowa z tożsamością zarządzaną przez system
- Zasady dostępu w celu uzyskiwania dostępu do wpisów tajnych w Key Vault przy użyciu tożsamości zarządzanej aplikacji sieci Web

1. Kliknij link wdrożenie szablonu platformy Azure:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Fweb-app%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Wybierz grupę zasobów **simplerotation**
1. Kliknij pozycję Kup

### <a name="deploy-web-app"></a>Wdróż aplikację sieci Web

Kod źródłowy aplikacji sieci Web, którą można znaleźć w https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/test-webapp na potrzeby wdrożenia aplikacji sieci Web, wykonaj następujące czynności:

1. Pobierz plik zip aplikacji funkcji z https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-app.zip
1. Przekaż `simplerotationsample-app.zip` pliku do Azure Cloud Shell.
1. Użyj tego polecenia platformy Azure w celu wdrożenia pliku zip w aplikacji funkcji:

   ```azurecli
   az webapp deployment source config-zip -g simplerotation -n simplerotation-app --src /home/{firstname e.g jack}/simplerotationsample-app.zip
   ```

#### <a name="open-web-application"></a>Otwórz aplikację sieci Web

Przejdź do wdrożonej aplikacji i kliknij pozycję "URL":
 
![Testowanie i weryfikowanie](./media/rotate10.png)

Wygenerowana wartość wpisu tajnego powinna być wyświetlana z bazą danych połączoną jako true.

## <a name="learn-more"></a>Więcej informacji:

- Przegląd: [monitorowanie Key Vault z Azure Event Grid (wersja zapoznawcza)](event-grid-overview.md)
- Instrukcje: [otrzymywanie wiadomości e-mail po zmianie wpisu tajnego magazynu kluczy](event-grid-logicapps.md)
- [Schemat zdarzeń Azure Event Grid dla Azure Key Vault (wersja zapoznawcza)](../event-grid/event-schema-key-vault.md)
