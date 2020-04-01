---
title: Samouczek rotacji pojedynczego użytkownika/hasła
description: Użyj tego samouczka do automatyzacji rotacji pojedynczego użytkownika/ hasła
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239371"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-with-single-userpassword-authentication"></a>Automatyzacja rotacji klucza tajnego dla zasobów za pomocą uwierzytelniania pojedynczego użytkownika/hasła

Chociaż najlepszym sposobem uwierzytelniania w usługach platformy Azure jest przy użyciu [tożsamości zarządzanej,](managed-identity.md)istnieją pewne scenariusze, w których nie jest to opcja. W takich przypadkach używane są klucze dostępu lub wpisy tajne. Klucze dostępu lub wpisy tajne powinny być okresowo obracane.

W tym samouczku pokazano, jak zautomatyzować okresowy obrót wpisów tajnych dla baz danych i usług z uwierzytelniania pojedynczego użytkownika/hasła. W szczególności w tym scenariuszu obraca hasła serwera SQL przechowywane w magazynie kluczy przy użyciu funkcji wyzwalane przez powiadomienie usługi Event Grid:

![Diagram obrotu](./media/rotate1.png)

1. Trzydzieści dni przed datą wygaśnięcia klucza magazyn kluczy publikuje zdarzenie "bliski wygaśnięcia" w uszmia zdarzenia.
1. Usługa Event Grid sprawdza subskrypcje zdarzeń i za pomocą http post wywołuje punkt końcowy aplikacji funkcji subskrybowany do tego zdarzenia.
1. Aplikacja funkcji odbiera tajne informacje, generuje nowe losowe hasło i tworzy nową wersję klucza tajnego z nowym hasłem w Key Vault.
1. Aplikacja funkcji aktualizuje SQL z nowym hasłem.

> [!NOTE]
> Może istnieć opóźnienie między krokiem 3 i 4 i w tym czasie klucz tajny w magazynie kluczy nie będzie prawidłowy do uwierzytelniania w języku SQL. W przypadku awarii w dowolnym z kroków Siatki zdarzeń ponawia ponawianie prób przez 2 godziny.

## <a name="setup"></a>Konfiguracja

## <a name="create-a-key-vault-and-sql-server"></a>Tworzenie magazynu kluczy i serwera SQL

Zanim zaczniemy, musimy utworzyć magazyn kluczy, utworzyć serwer SQL i bazę danych oraz zapisać hasło administratora programu SQL Server w programie Key Vault.

W tym samouczku użyto wstępnie utworzonego szablonu usługi Azure Resource Manager do tworzenia składników. Cały kod można znaleźć tutaj: [Przykład szablonu tajnego obrotu .](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/arm-templates)

1. Kliknij łącze wdrażania szablonu platformy Azure:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Finitial-setup%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. W przypadku opcji "Grupa zasobów" wybierz opcję "Utwórz nowy" i nadaj jej nazwę "simplerotation".
1. Wybierz "Kup".

    ![Tworzenie nowej grupy zasobów](./media/rotate2.png)

Po wykonaniu tych kroków będziesz mieć magazyn kluczy, serwer SQL i bazę danych SQL. Można to sprawdzić w terminalu interfejsu wiersza polecenia platformy Azure, uruchamiając:

```azurecli
az resource list -o table
```

Wyniki będą wyglądać coś takiego:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv             simplerotation             eastus      Microsoft.KeyVault/vaults
simplerotation-sql            simplerotation             eastus      Microsoft.Sql/servers
simplerotation-sql/master     simplerotation             eastus      Microsoft.Sql/servers/databases
```

## <a name="create-function-app"></a>Utwórz aplikację funkcji

Utwórz aplikację funkcji z tożsamością zarządzaną przez system, a także z dodatkowymi wymaganymi składnikami: 

Aplikacja funkcyjna wymaga poniżej składników i konfiguracji:
- Plan usługi App Service
- Konto magazynu
- Zasady dostępu do wpisów tajnych w ucho.

1. Kliknij łącze wdrażania szablonu platformy Azure:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Ffunction-app%2Fazuredeploy.json" target="_blank"><img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. W przypadku opcji "Grupa zasobów" wybierz opcję "simplerotation".
1. Wybierz "Kup".

   ![Ekran zakupu](./media/rotate3.png)

Po wykonaniu powyższych kroków będziesz mieć konto magazynu, farmę serwerów i aplikację funkcji.  Można to sprawdzić w terminalu interfejsu wiersza polecenia platformy Azure, uruchamiając:

```azurecli
az resource list -o table
```

Wyniki będą wyglądać coś takiego:

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

Aby uzyskać informacje dotyczące tworzenia aplikacji funkcji i uzyskiwania dostępu do usługi Key Vault przy użyciu funkcji, zobacz [Tworzenie aplikacji funkcji z portalu Azure](../azure-functions/functions-create-function-app-portal.md) i [zapewnianie uwierzytelniania usługi Key Vault za pomocą tożsamości zarządzanej](managed-identity.md)

### <a name="rotation-function-and-deployment"></a>Funkcja rotacji i wdrażanie
Funkcja używa zdarzenia jako wyzwalacza i wykonuje obrót tajnego aktualizowania usługi Key Vault i bazy danych SQL.

#### <a name="function-event-trigger-handler"></a>Program obsługi wyzwalacza zdarzeń funkcji

Poniżej funkcja odczytuje dane zdarzeń i wykonuje logikę obrotu

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

#### <a name="secret-rotation-logic"></a>Logika rotacji tajnej
Ta metoda rotacji odczytuje informacje o bazie danych z klucza tajnego, tworzy nową wersję klucza tajnego i aktualizuje bazę danych o nowy klucz tajny.

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
Cały kod można znaleźć tutaj:https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/rotation-function

#### <a name="function-deployment"></a>Wdrażanie funkcji

1. Pobierz funkcję aplikacji zip pliku:https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-fn.zip

1. Przekaż plik simplerotationsample-fn.zip do usługi Azure Cloud Shell.
 
1. Użyj polecenia poniżej interfejsu wiersza polecenia, aby wdrożyć plik zip do aplikacji funkcyjnej:

```azurecli
az functionapp deployment source config-zip -g simplerotation -n simplerotation-fn --src /home/{firstname e.g jack}/simplerotationsample-fn.zip
```
![Ekran zakupu](./media/rotate4.png)

Po wdrożeniu należy zauważyć dwie funkcje w ramach simplerotation-fn:

![Azure Cloud Shell](./media/rotate5.png)

### <a name="add-event-subscription-for-secretnearexpiry-event"></a>Dodaj subskrypcję wydarzenia dla zdarzenia "SecretNearExpiry"

Skopiuj aplikację funkcji eventgrid_extension klucz.

![Azure Cloud Shell](./media/rotate6.png)

![Testowanie i weryfikowanie](./media/rotate7.png)

Użyj skopiowanego klucza rozszerzenia eventgrid i identyfikatora subskrypcji w poniższym poleceniu, aby utworzyć subskrypcję siatki zdarzeń dla zdarzeń SecretNearExpiry.

```azurecli
az eventgrid event-subscription create --name simplerotation-eventsubscription --source-resource-id "/subscriptions/<subscription-id>/resourceGroups/simplerotation/providers/Microsoft.KeyVault/vaults/simplerotation-kv" --endpoint "https://simplerotation-fn.azurewebsites.net/runtime/webhooks/EventGrid?functionName=SimpleRotation&code=<extension-key>" --endpoint-type WebHook --included-event-types "Microsoft.KeyVault.SecretNearExpiry"
```

### <a name="add-secret-to-key-vault"></a>Dodawanie klucza tajnego do magazynu kluczy
Ustaw zasady dostępu, aby nadać użytkownikom uprawnienia "zarządzanie wpisami tajnymi".

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name simplerotation-kv --secret-permissions set delete get list
```

Teraz utwórz nowy klucz tajny ze znacznikami zawierającymi źródło danych bazy danych SQL i identyfikator użytkownika, z datą wygaśnięcia ustawioną na jutro.

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name sqluser --vault-name simplerotation-kv --value "Simple123" --tags "UserID=azureuser" "DataSource=simplerotation-sql.database.windows.net" --expires $tomorrowDate
```

Utworzenie klucza tajnego z krótką datą wygaśnięcia natychmiast opublikuje zdarzenie SecretNearExpiry, które z kolei wyzwoli funkcję obracania klucza tajnego.

### <a name="test-and-verify"></a>Testowanie i weryfikowanie
Po kilku minutach, sqluser secret powinien automatycznie obracać.

Aby zweryfikować weryfikację tajnych obrotów, przejdź do usługi Key Vault > Secrets

![Testowanie i weryfikowanie](./media/rotate8.png)

Otwórz klucz tajny "sqluser" i wyświetl oryginalną i obróconą wersję

![Testowanie i weryfikowanie](./media/rotate9.png)

## <a name="create-web-app"></a>Tworzenie aplikacji internetowej

Aby zweryfikować poświadczenia SQL, utwórz aplikację sieci web. Ta aplikacja sieci web otrzyma klucz tajny z magazynu kluczy, wyodrębni informacje o bazie danych SQL i poświadczenia z klucza tajnego i przetestuje połączenie z sql.

Aplikacja internetowa wymaga poniżej składników i konfiguracji:
- Aplikacja Web z tożsamością zarządzaną systemem
- Uzyskiwanie dostępu do zasad dostępu do wpisów tajnych w programie Key Vault przy użyciu tożsamości zarządzanej w aplikacji Sieci Web

1. Kliknij łącze wdrażania szablonu platformy Azure:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Fweb-app%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Wybierz grupę zasobów **simplerotation**
1. Kliknij przycisk Kup

### <a name="deploy-web-app"></a>Wdrażanie aplikacji internetowej

Kod źródłowy aplikacji sieci web, który można znaleźć w https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/test-webapp przypadku wdrożenia aplikacji sieci web, wykonaj następujące czynności:

1. Pobierz plik zip aplikacji funkcji zhttps://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-app.zip
1. Przekaż plik `simplerotationsample-app.zip` do usługi Azure Cloud Shell.
1. Użyj tego polecenia interfejsu wiersza polecenia platformy Azure, aby wdrożyć plik zip w aplikacji funkcji:

   ```azurecli
   az webapp deployment source config-zip -g simplerotation -n simplerotation-app --src /home/{firstname e.g jack}/simplerotationsample-app.zip
   ```

#### <a name="open-web-application"></a>Otwórz aplikację internetową

Przejdź do wdrożonej aplikacji i kliknij "URL":
 
![Testowanie i weryfikowanie](./media/rotate10.png)

Wygenerowana wartość tajnego powinna być wyświetlana z połączoną z bazą danych jako true.

## <a name="learn-more"></a>Więcej informacji:

- Omówienie: [Monitorowanie magazynu kluczy za pomocą usługi Azure Event Grid (wersja zapoznawcza)](event-grid-overview.md)
- Jak: [Odbieranie wiadomości e-mail po zmianie klucza tajnego magazynu](event-grid-logicapps.md)
- [Schemat zdarzeń usługi Azure Event Grid dla usługi Azure Key Vault (wersja zapoznawcza)](../event-grid/event-schema-key-vault.md)
