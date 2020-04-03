---
title: Przechowywanie usług Azure Cosmos DB za pomocą usługi Key Vault umożliwia przechowywanie kluczy usługi Azure Cosmos DB i uzyskiwanie do nich dostępu
description: Użyj usługi Azure Key Vault do przechowywania i uzyskiwania dostępu do ciągu połączenia usługi Azure Cosmos DB, kluczy, punktów końcowych.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 225221635f978e3d70cec4ce7e9d78d6b100b4fd
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618770"
---
# <a name="secure-azure-cosmos-keys-using-azure-key-vault"></a>Zabezpieczanie kluczy usługi Azure Cosmos przy użyciu usługi Azure Key Vault 

>[!IMPORTANT]
> Zalecane rozwiązanie dostępu do kluczy usługi Azure Cosmos DB jest użycie [tożsamości zarządzanej przypisanej](managed-identity-based-authentication.md)przez system. Jeśli usługa nie może korzystać z zarządzanych tożsamości, użyj [rozwiązania opartego na cert](certificate-based-authentication.md). Jeśli zarówno rozwiązanie oparte na tożsamości zarządzanej, jak i rozwiązanie oparte na cert nie spełniają Twoich potrzeb, użyj poniższego rozwiązania magazynu kluczy.

Korzystając z usługi Azure Cosmos DB dla aplikacji, można uzyskać dostęp do bazy danych, kolekcje, dokumenty przy użyciu punktu końcowego i klucza w pliku konfiguracji aplikacji.  Jednak nie jest bezpieczne, aby umieścić klucze i adres URL bezpośrednio w kodzie aplikacji, ponieważ są one dostępne w formacie zwykłego tekstu dla wszystkich użytkowników. Należy zapewnić dostępność punktu końcowego i kluczy, ale za pomocą bezpiecznego mechanizmu. Usługa Azure Key Vault pomaga w bezpiecznym przechowywaniu wpisów tajnych aplikacji i zarządzaniu nimi.

Następujące kroki są wymagane do przechowywania i odczytywania kluczy dostępu usługi Azure Cosmos DB z usługi Key Vault:

* Tworzenie magazynu kluczy  
* Dodawanie kluczy dostępu usługi Azure Cosmos DB do magazynu kluczy  
* Tworzenie aplikacji sieci Web platformy Azure  
* Zarejestruj aplikację & udzielić uprawnień do odczytu magazynu kluczy  


## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/).  
2. Wybierz **pozycję Utwórz zasób > zabezpieczenia > przechowalnią kluczy**.  
3. W sekcji **Tworzenie magazynu kluczy** podaj następujące informacje:  
   * **Nazwa:** Podaj unikatową nazwę magazynu kluczy.  
   * **Subskrypcja:** Wybierz subskrypcję, której będziesz używać.  
   * W obszarze **Grupa zasobów** wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę grupy zasobów.  
   * W menu rozwijanym Lokalizacja wybierz lokalizację.  
   * Pozostaw inne opcje do ich ustawień domyślnych.  
4. Po podaniu powyższych informacje wybierz przycisk **Utwórz**.  

## <a name="add-azure-cosmos-db-access-keys-to-the-key-vault"></a>Dodaj klucze dostępu usługi Azure Cosmos DB do magazynu kluczy.
1. Przejdź do przechowalni kluczy utworzonej w poprzednim kroku, otwórz kartę **Wpisy tajne.**  
2. Wybierz **+Generuj/Zaimportuj**, 

   * Wybierz **opcję Ręcznie** dla opcji **przekazywania**.
   * Podaj **imię i nazwisko** swojego sekretu
   * Podaj ciąg połączenia konta usługi Cosmos DB w polu **Wartość.** Następnie wybierz pozycję **Utwórz**.

   ![Utwórz klucz tajny](./media/access-secrets-from-keyvault/create-a-secret.png)

4. Po utworzeniu klucza tajnego otwórz go i skopiuj identyfikator **Secret, który jest w następującym formacie. Użyjesz tego identyfikatora w następnej sekcji. 

   `https://<Key_Vault_Name>.vault.azure.net/secrets/<Secret _Name>/<ID>`

## <a name="create-an-azure-web-application"></a>Tworzenie aplikacji sieci Web platformy Azure

1. Utwórz aplikację internetową platformy Azure lub możesz pobrać aplikację z [repozytorium GitHub](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/Demo/keyvaultdemo). Jest to prosta aplikacja MVC.  

2. Rozpaj pobraną aplikację i otwórz plik **HomeController.cs.** Zaktualizuj tajny identyfikator w następującym wierszu:

   `var secret = await keyVaultClient.GetSecretAsync("<Your Key Vault’s secret identifier>")`

3. **Zapisz** plik, **Skompiluj** rozwiązanie.  
4. Następnie wdrożyć aplikację na platformie Azure. Kliknij prawym przyciskiem myszy na projekt i wybierz **polecenie publikuj**. Utwórz nowy profil usługi aplikacji (możesz nazwać aplikację WebAppKeyVault1) i wybierz pozycję **Publikuj**.   

5. Po wdrożeniu aplikacji. W witrynie Azure Portal przejdź do wdrożonej aplikacji sieci web i włącz **tożsamość usługi zarządzanej** tej aplikacji.  

   ![Tożsamość usługi zarządzanej](./media/access-secrets-from-keyvault/turn-on-managed-service-identity.png)

Jeśli uruchomisz aplikację teraz, zobaczysz następujący błąd, ponieważ nie udzieliłeś żadnych uprawnień do tej aplikacji w przechowalni kluczy.

![Aplikacja wdrożona bez dostępu](./media/access-secrets-from-keyvault/app-deployed-without-access.png)

## <a name="register-the-application--grant-permissions-to-read-the-key-vault"></a>Zarejestruj aplikację & udzielić uprawnień do odczytu magazynu kluczy

W tej sekcji można zarejestrować aplikację w usłudze Azure Active Directory i dać uprawnienia dla aplikacji do odczytu usługi Key Vault. 

1. Przejdź do witryny Azure portal, otwórz **magazyn kluczy** utworzony w poprzedniej sekcji.  

2. **Otwórz zasady programu Access**, wybierz **+Dodaj nowy** znajdź wdrożoną aplikację internetową, wybierz uprawnienia i wybierz przycisk **OK**.  

   ![Dodawanie zasad dostępu](./media/access-secrets-from-keyvault/add-access-policy.png)

Teraz, jeśli uruchomisz aplikację, możesz odczytać klucz tajny z usługi Key Vault.

![Aplikacja wdrożona z kluczem tajnym](./media/access-secrets-from-keyvault/app-deployed-with-access.png)
 
Podobnie można dodać użytkownika, aby uzyskać dostęp do magazynu kluczy. Należy dodać siebie do magazynu kluczy, wybierając **zasady dostępu,** a następnie udzielić wszystkich uprawnień potrzebnych do uruchomienia aplikacji z programu Visual Studio. Gdy ta aplikacja jest uruchomiona z pulpitu, zajmuje swoją tożsamość.

## <a name="next-steps"></a>Następne kroki

* Aby skonfigurować zaporę dla usługi Azure Cosmos DB zobacz artykuł [pomocy technicznej zapory.](firewall-support.md)
* Aby skonfigurować punkt końcowy usługi sieci wirtualnej, zobacz [bezpieczny dostęp przy użyciu punktu końcowego usługi sieci wirtualnej.](vnet-service-endpoint.md)
