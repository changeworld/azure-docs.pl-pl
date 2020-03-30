---
title: Szyfrowanie źródła aplikacji w stanie spoczynku
description: Szyfruj dane aplikacji w usłudze Azure Storage i wdrażaj ją jako plik pakietu.
ms.topic: article
ms.date: 03/06/2020
ms.openlocfilehash: 7e5e809fe8b670ae6ec5bfd15e54f9a8019e76d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408747"
---
# <a name="encryption-at-rest-using-customer-managed-keys"></a>Szyfrowanie w spoczynku przy użyciu kluczy zarządzanych przez klienta

Szyfrowanie danych aplikacji sieci web w stanie spoczynku wymaga konta usługi Azure Storage i usługi Azure Key Vault. Te usługi są używane podczas uruchamiania aplikacji z pakietu wdrażania.

  - [Usługa Azure Storage zapewnia szyfrowanie w spoczynku.](../storage/common/storage-service-encryption.md) Można użyć kluczy dostarczonych przez system lub własnych kluczy zarządzanych przez klienta. W tym miejscu dane aplikacji są przechowywane, gdy nie jest uruchomiony w aplikacji sieci web na platformie Azure.
  - [Uruchamianie z pakietu wdrażania](deploy-run-package.md) jest funkcją wdrażania usługi App Service. Umożliwia wdrożenie zawartości witryny z konta usługi Azure Storage przy użyciu adresu URL podpisu dostępu współdzielonego (SAS).
  - [Odwołania do magazynu kluczy](app-service-key-vault-references.md) są funkcją zabezpieczeń usługi App Service. Umożliwia importowanie wpisów tajnych w czasie wykonywania jako ustawienia aplikacji. Użyj tego do szyfrowania adresu URL sygnatury dostępu Współdzielonego konta usługi Azure Storage.

## <a name="set-up-encryption-at-rest"></a>Konfigurowanie szyfrowania w spoczynku

### <a name="create-an-azure-storage-account"></a>Tworzenie konta usługi Azure Storage

Najpierw [utwórz konto usługi Azure Storage](../storage/common/storage-account-create.md) i [zaszyfruj je za pomocą kluczy zarządzanych przez klienta.](../storage/common/encryption-customer-managed-keys.md) Po utworzeniu konta magazynu użyj [Eksploratora usługi Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md) do przekazywania plików pakietów.

Następnie użyj Eksploratora magazynu, aby [wygenerować sygnaturę współdzieloną](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#generate-a-sas-in-storage-explorer). 

> [!NOTE]
> Zapisz ten adres URL sygnatury dostępu Współdzielonego, jest on używany później, aby włączyć bezpieczny dostęp do pakietu wdrażania w czasie wykonywania.

### <a name="configure-running-from-a-package-from-your-storage-account"></a>Konfigurowanie uruchamiania z pakietu z konta magazynu
  
Po przekazaniu pliku do magazynu obiektów Blob i uzyskaniu `WEBSITE_RUN_FROM_PACKAGE` adresu URL sygnatury dostępu Współdzielonego dla pliku ustaw ustawienie aplikacji na adres URL sygnatury dostępu Współdzielonego. W poniższym przykładzie wykonuje to przy użyciu interfejsu wiersza polecenia platformy Azure:

```
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="<your-SAS-URL>"
```

Dodanie tego ustawienia aplikacji powoduje ponowne uruchomienie aplikacji sieci web. Po ponownym uruchomieniu aplikacji przejdź do niej i upewnij się, że aplikacja została poprawnie uruchomiona przy użyciu pakietu wdrażania. Jeśli aplikacja nie została poprawnie uruchomiony, zobacz [przewodnik Rozwiązywanie problemów z pakietem Uruchom z pakietu](deploy-run-package.md#troubleshooting).

### <a name="encrypt-the-application-setting-using-key-vault-references"></a>Szyfrowanie ustawień aplikacji przy użyciu odwołań do magazynu kluczy

Teraz możesz zastąpić wartość `WEBSITE_RUN_FROM_PACKAGE` ustawienia aplikacji odwołaniem do adresu URL zakodowanego w sygnale sygnatury dostępu Współdzielonego. Dzięki temu adres URL sygnatury dostępu Współdzielonego jest zaszyfrowany w magazynie kluczy, co zapewnia dodatkową warstwę zabezpieczeń.

1. Użyj następującego [`az keyvault create`](/cli/azure/keyvault#az-keyvault-create) polecenia, aby utworzyć wystąpienie magazynu kluczy.       

    ```azurecli    
    az keyvault create --name "Contoso-Vault" --resource-group <group-name> --location eastus    
    ```    

1. Postępuj zgodnie z [poniższymi instrukcjami, aby udzielić aplikacji dostępu](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) do magazynu kluczy:

1. Użyj następującego [`az keyvault secret set`](/cli/azure/keyvault/secret#az-keyvault-secret-set) polecenia, aby dodać zewnętrzny adres URL jako klucz tajny w magazynie kluczy:   

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ```    

1.  Użyj następującego [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) polecenia, `WEBSITE_RUN_FROM_PACKAGE` aby utworzyć ustawienie aplikacji z wartością jako odwołaniem do zewnętrznego adresu URL magazynu kluczy:

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    Będzie `<secret-version>` w danych wyjściowych `az keyvault secret set` poprzedniego polecenia.

Aktualizacja tego ustawienia aplikacji powoduje ponowne uruchomienie aplikacji sieci web. Po ponownym uruchomieniu aplikacji, przejdź do niej upewnij się, że został uruchomiony poprawnie przy użyciu odwołania Dojdarki kluczy.

## <a name="how-to-rotate-the-access-token"></a>Jak obrócić token dostępu

Najlepszym rozwiązaniem jest okresowe obracanie klucza Sygnatury dostępu Współdzielonego konta magazynu. Aby upewnić się, że aplikacja internetowa nie przypadkowo traci dostęp, należy również zaktualizować adres URL sygnatury dostępu Współdzielonego w magazynie kluczy.

1. Obróć klucz Sygnatury dostępu Współdzielonego, przechodząc do konta magazynu w witrynie Azure portal. W obszarze**Klawisze dostępu** **ustawienia** > kliknij ikonę, aby obrócić klucz Sygnatury dostępu Współdzielonego.

1. Skopiuj nowy adres URL sygnatury dostępu Współdzielonego i użyj następującego polecenia, aby ustawić zaktualizowany adres URL sygnatury dostępu Współdzielonego w magazynie kluczy:

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ``` 

1. Zaktualizuj odwołanie do magazynu kluczy w ustawieniach aplikacji do nowej wersji tajnej:

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    Będzie `<secret-version>` w danych wyjściowych `az keyvault secret set` poprzedniego polecenia.

## <a name="how-to-revoke-the-web-apps-data-access"></a>Jak odwołać dostęp do danych aplikacji sieci web

Istnieją dwie metody odwołania dostępu aplikacji sieci web do konta magazynu. 

### <a name="rotate-the-sas-key-for-the-azure-storage-account"></a>Obracanie klucza Sygnatury dostępu Współdzielonego dla konta usługi Azure Storage

Jeśli klucz Sygnatury dostępu Współdzielonego dla konta magazynu zostanie obrócony, aplikacja sieci web nie będzie już miała dostępu do konta magazynu, ale będzie nadal działać z ostatnią pobraną wersją pliku pakietu. Uruchom ponownie aplikację internetową, aby wyczyścić ostatnią pobraną wersję.

### <a name="remove-the-web-apps-access-to-key-vault"></a>Usuwanie dostępu aplikacji sieci web do usługi Key Vault

Możesz odwołać dostęp aplikacji sieci web do danych witryny, wyłączając dostęp aplikacji sieci web do usługi Key Vault. Aby to zrobić, usuń zasady dostępu dla tożsamości aplikacji sieci web. Jest to ta sama tożsamość utworzona wcześniej podczas konfigurowania odwołań do magazynu kluczy.

## <a name="summary"></a>Podsumowanie

Pliki aplikacji są teraz szyfrowane w spoczynku na koncie magazynu. Po uruchomieniu aplikacji sieci web pobiera adres URL sygnatury dostępu Współdzielonego z magazynu kluczy. Na koniec aplikacja sieci web ładuje pliki aplikacji z konta magazynu. 

Jeśli chcesz odwołać dostęp aplikacji sieci web do konta magazynu, możesz odwołać dostęp do magazynu kluczy lub obrócić klucze konta magazynu, co unieważnia adres URL sygnatury dostępu Współdzielonego.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="is-there-any-additional-charge-for-running-my-web-app-from-the-deployment-package"></a>Czy istnieje dodatkowa opłata za uruchomienie aplikacji sieci web z pakietu wdrożeniowego?

Tylko koszt skojarzony z kontem usługi Azure Storage i wszelkie odpowiednie opłaty wychodzące.

### <a name="how-does-running-from-the-deployment-package-affect-my-web-app"></a>Jak uruchamianie z pakietu wdrożeniowego wpływa na moją aplikację sieci web?

- Uruchamianie aplikacji z pakietu `wwwroot/` wdrażania sprawia, że tylko do odczytu. Aplikacja otrzymuje błąd podczas próby zapisania w tym katalogu.
- Formaty TAR i GZIP nie są obsługiwane.
- Ta funkcja nie jest zgodna z lokalną pamięcią podręczną.

## <a name="next-steps"></a>Następne kroki

- [Odwołania do magazynu kluczy dla usługi App Service](app-service-key-vault-references.md)
- [Szyfrowanie usługi Azure Storage dla danych w stanie spoczynku](../storage/common/storage-service-encryption.md)
