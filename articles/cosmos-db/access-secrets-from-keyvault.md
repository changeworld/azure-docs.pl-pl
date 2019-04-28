---
title: Przechowywanie i dostęp do kluczy usługi Azure Cosmos DB za pomocą usługi Key Vault
description: Użyj usługi Azure Key Vault do przechowywania i dostępu do usługi Azure Cosmos DB parametry połączenia, kluczy i punktów końcowych.
author: rimman
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 36b0a2f18cf2917251a87405456980811af1bc3d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60894841"
---
# <a name="secure-azure-cosmos-keys-using-azure-key-vault"></a>Zabezpieczanie kluczy Azure Cosmos przy użyciu usługi Azure Key Vault 

W przypadku używania usługi Azure Cosmos DB dla aplikacji, możesz uzyskać dostęp do bazy danych, kolekcje, dokumenty, przy użyciu punktu końcowego i klucz w pliku konfiguracji aplikacji.  Jednak nie jest bezpieczne przełączyć klucze i adres URL bezpośrednio w kodzie aplikacji, ponieważ są one dostępne w formacie zwykłego tekstu dla wszystkich użytkowników. Chcesz upewnić się, że punkt końcowy i klucze są dostępne, ale za pomocą mechanizmu bezpiecznego. Jest to, gdzie usługi Azure Key Vault ułatwia bezpieczne przechowywanie i zarządzanie wpisami tajnymi aplikacji.

Poniższe kroki są wymagane do przechowywania i klucze dostępu usługi Azure Cosmos DB do odczytu z usługi Key Vault:

* Tworzenie magazynu kluczy  
* Dodawanie kluczy dostępu do usługi Azure Cosmos DB do usługi Key Vault  
* Tworzenie aplikacji sieci web platformy Azure  
* Rejestrowanie aplikacji i przyznaj uprawnienia do odczytu usługi Key Vault  


## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com/).  
2. Wybierz **Utwórz zasób > Zabezpieczenia > usługi Key Vault**.  
3. W sekcji **Tworzenie magazynu kluczy** podaj następujące informacje:  
   * **Nazwa:** Podaj unikatową nazwę usługi Key Vault.  
   * **Subskrypcja:** Wybierz subskrypcję, która będzie używana.  
   * W obszarze **Grupa zasobów** wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę grupy zasobów.  
   * W menu rozwijanym lokalizacji wybierz lokalizację.  
   * Pozostałych opcji Pozostaw ich wartości domyślne.  
4. Po podaniu powyższych informacje wybierz przycisk **Utwórz**.  

## <a name="add-azure-cosmos-db-access-keys-to-the-key-vault"></a>Dodawanie kluczy dostępu do usługi Azure Cosmos DB do usługi Key Vault.
1. Przejdź do utworzonego w poprzednim kroku, otwórz usługę Key Vault **wpisów tajnych** kartę.  
2. Wybierz **+ Wygeneruj/zaimportuj**, 

   * Wybierz **ręczne** dla **opcje przekazywania**.
   * Podaj **nazwa** dla klucz tajny
   * Podaj parametry połączenia konta usługi Cosmos DB do **wartość** pola. A następnie wybierz **Utwórz**.

   ![Utwórz klucz tajny](./media/access-secrets-from-keyvault/create-a-secret.png)

4. Po utworzeniu wpisu tajnego, otwórz go i skopiuj ** identyfikator wpisu tajnego, który znajduje się w następującym formacie. Użyjesz tego identyfikatora w następnej sekcji. 

   `https://<Key_Vault_Name>.vault.azure.net/secrets/<Secret _Name>/<ID>`

## <a name="create-an-azure-web-application"></a>Tworzenie aplikacji sieci web platformy Azure

1. Tworzenie aplikacji sieci web platformy Azure można również pobrać aplikację z [repozytorium GitHub](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/Demo/keyvaultdemo). Jest prostą aplikację MVC.  

2. Rozpakuj pobraną aplikację, a następnie otwórz **HomeController.cs** pliku. Zaktualizuj identyfikator wpisu tajnego w następującym wierszu:

   `var secret = await keyVaultClient.GetSecretAsync("<Your Key Vault’s secret identifier>")`

3. **Zapisz** pliku **kompilacji** rozwiązania.  
4. Następnie Wdróż aplikację na platformie Azure. Kliknij prawym przyciskiem myszy projekt i wybierz polecenie **publikowania**. Utwórz nowy profil usługi aplikacji (o nazwie aplikacji WebAppKeyVault1) i wybierz **Publikuj**.   

5. Po wdrożeniu aplikacji. W witrynie Azure portal, przejdź do aplikacji sieci web, która została wdrożona, a następnie Włącz **tożsamości usługi zarządzanej** tej aplikacji.  

   ![Tożsamość usługi zarządzanej](./media/access-secrets-from-keyvault/turn-on-managed-service-identity.png)

Jeśli uruchomisz aplikację teraz, zobaczysz następujący błąd, ponieważ nie mają żadnych uprawnień do tej aplikacji w usłudze Key Vault.

![Aplikacja wdrożona bez dostępu](./media/access-secrets-from-keyvault/app-deployed-without-access.png)

## <a name="register-the-application--grant-permissions-to-read-the-key-vault"></a>Rejestrowanie aplikacji i przyznaj uprawnienia do odczytu usługi Key Vault

W tej sekcji możesz zarejestrować aplikację w usłudze Azure Active Directory i nadanie uprawnień dla aplikacji na odczytywanie usługi Key Vault. 

1. Przejdź do witryny Azure portal, otwórz **usługi Key Vault** został utworzony w poprzedniej sekcji.  

2. Otwórz **zasady dostępu**, wybierz opcję **+ Dodaj nowy** możesz znaleźć aplikacji sieci web został wdrożony, wybierz uprawnienia i wybierz **OK**.  

   ![Dodawanie zasad dostępu](./media/access-secrets-from-keyvault/add-access-policy.png)

Teraz po uruchomieniu aplikacji, możesz przeczytać wpisu tajnego usługi Key Vault.

![Aplikacja wdrożona za pomocą klucza tajnego](./media/access-secrets-from-keyvault/app-deployed-with-access.png)
 
Podobnie można dodać użytkownika do uzyskania dostępu do klucza magazynu. Musisz samodzielnie dodać do usługi Key Vault, wybierając **zasady dostępu** a następnie przyznać wszystkie uprawnienia potrzebne do uruchomienia aplikacji w programie Visual studio. Ta aplikacja jest uruchomiona z pulpitu, zajmuje się Twoją tożsamość.

## <a name="next-steps"></a>Kolejne kroki

* Konfigurowanie zapory dla usługi Azure Cosmos DB, zobacz [zapory pomocy technicznej](firewall-support.md) artykułu.
* Aby skonfigurować punkt końcowy usługi sieci wirtualnej, zobacz [zabezpieczyć dostęp przy użyciu punktu końcowego usługi sieci wirtualnej](vnet-service-endpoint.md) artykułu.
