---
title: Szyfrowanie w spoczynku przy użyciu kluczy zarządzanych przez klienta
titleSuffix: Azure Cognitive Search
description: Uzupełnij szyfrowanie po stronie serwera za pośrednictwem indeksów i map synonimów w usłudze Azure Cognitive Search przy użyciu kluczy, które tworzysz i którymi zarządzasz w usłudze Azure Key Vault.
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: cb17fe24339ad618229b3456ece15c206f79bdb7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76899950"
---
# <a name="encryption-at-rest-of-content-in-azure-cognitive-search-using-customer-managed-keys-in-azure-key-vault"></a>Szyfrowanie zawartości w usłudze Azure Cognitive Search przy użyciu kluczy zarządzanych przez klienta w usłudze Azure Key Vault

Domyślnie usługa Azure Cognitive Search szyfruje zawartość indeksowaną w spoczynku za pomocą [kluczy zarządzanych przez usługę.](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest#data-encryption-models) Szyfrowanie domyślne można uzupełnić dodatkową warstwą szyfrowania przy użyciu kluczy utworzonych w usłudze Azure Key Vault i którymi zarządzasz. W tym artykule otrzymasz od kroków.

Szyfrowanie po stronie serwera jest obsługiwane przez integrację z [usługą Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview). Można utworzyć własne klucze szyfrowania i przechowywać je w magazynie kluczy lub można użyć interfejsów API usługi Azure Key Vault do generowania kluczy szyfrowania. Za pomocą usługi Azure Key Vault można również przeprowadzić inspekcję użycia kluczy. 

Szyfrowanie za pomocą kluczy zarządzanych przez klienta jest konfigurowane na poziomie indeksu lub mapy synonimów podczas tworzenia tych obiektów, a nie na poziomie usługi wyszukiwania. Nie można szyfrować zawartości, która już istnieje. 

Klucze nie muszą znajdować się w tym samym magazynie kluczy. Pojedyncza usługa wyszukiwania może obsługiwać wiele zaszyfrowanych indeksów lub map synonimów, z których każdy jest zaszyfrowany za pomocą własnych kluczy szyfrowania zarządzanych przez klienta przechowywanych w różnych magazynach kluczy.  W tej samej usłudze mogą być również indeksy i mapy synonimów, które nie są szyfrowane przy użyciu kluczy zarządzanych przez klienta. 

> [!IMPORTANT] 
> Ta funkcja jest dostępna w [interfejsie API REST w wersji 2019-05-06](https://docs.microsoft.com/rest/api/searchservice/) i [.NET SDK w wersji 8.0-preview](search-dotnet-sdk-migration-version-9.md). Obecnie nie ma pomocy technicznej, aby skonfigurować klucze szyfrowania zarządzane przez klienta w witrynie Azure portal. Usługa wyszukiwania musi zostać utworzona po styczniu 2019 r. i nie może być bezpłatną (udostępnioną) usługą.

## <a name="prerequisites"></a>Wymagania wstępne

W tym przykładzie są używane następujące usługi. 

+ [Utwórz usługę Azure Cognitive Search](search-create-service-portal.md) lub znajdź [istniejącą usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach bieżącej subskrypcji. 

+ [Utwórz zasób usługi Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault) lub znajdź istniejący magazyn w ramach subskrypcji.

+ [Narzędzie Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) lub [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) jest używane do wykonywania zadań konfiguracyjnych.

+ [Postman](search-get-started-postman.md), [Azure PowerShell](search-create-index-rest-api.md) i [Azure Cognitive Search SDK](https://aka.ms/search-sdk-preview) może służyć do wywoływania interfejsu API REST. Obecnie nie ma obsługi portalu dla szyfrowania zarządzanego przez klienta.

>[!Note]
> Ze względu na charakter szyfrowania za pomocą funkcji kluczy zarządzanych przez klienta usługa Azure Cognitive Search nie będzie mogła pobrać danych, jeśli klucz magazynu azure key zostanie usunięty. Aby zapobiec utracie danych spowodowanej przypadkowymi usunięciemi kluczy usługi Key Vault, **należy** włączyć ochronę przed usuwaniem i przeczyszczaniem w magazynie kluczy, zanim będzie można go używać. Aby uzyskać więcej informacji, zobacz [Azure Key Vault soft-delete](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete).   

## <a name="1---enable-key-recovery"></a>1 - Włącz odzyskiwanie kluczy

Po utworzeniu zasobu usługi Azure Key Vault włącz ochronę **usuwania nietrwałego** i **przeczyszczania** w wybranym magazynie kluczy, wykonując następujące polecenia programu PowerShell lub interfejsu wiersza polecenia platformy Azure:   

```powershell
$resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

```azurecli-interactive
az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
```

## <a name="2---create-a-new-key"></a>2 - Tworzenie nowego klucza

Jeśli używasz istniejącego klucza do szyfrowania zawartości usługi Azure Cognitive Search, pomiń ten krok.

1. [Zaloguj się do witryny Azure portal](https://portal.azure.com) i przejdź do pulpitu nawigacyjnego magazynu kluczy.

1. Wybierz ustawienie **Klawisze** z lewego okienka nawigacji i kliknij przycisk **+ Generuj/Importuj**.

1. W okienku **Tworzenie klucza** z listy **Opcji**wybierz metodę, której chcesz użyć do utworzenia klucza. Możesz **wygenerować** nowy klucz, **przekazać** istniejący klucz lub użyć **opcji Przywróć kopię zapasową,** aby wybrać kopię zapasową klucza.

1. Wprowadź **nazwę** klucza i opcjonalnie wybierz inne właściwości klucza.

1. Kliknij przycisk **Utwórz,** aby rozpocząć wdrożenie.

Zanotuj identyfikator klucza — składa się on z **wartości klucza Uri**, **nazwy klucza**i **wersji klucza.** Będą one potrzebne do zdefiniowania zaszyfrowanego indeksu w usłudze Azure Cognitive Search.
 
![Tworzenie nowego klucza przechowalni kluczy](./media/search-manage-encryption-keys/create-new-key-vault-key.png "Tworzenie nowego klucza przechowalni kluczy")

## <a name="3---create-a-service-identity"></a>3 - Tworzenie tożsamości usługi

Przypisanie tożsamości do usługi wyszukiwania umożliwia przyznanie usługi wyszukiwania uprawnień dostępu do usługi wyszukiwania usługi wyszukiwania. Usługa wyszukiwania użyje swojej tożsamości do uwierzytelniania za pomocą magazynu azure key.

Usługa Azure Cognitive Search obsługuje dwa sposoby przypisywania tożsamości: tożsamość zarządzaną lub zewnętrznie zarządzaną aplikację usługi Azure Active Directory. 

Jeśli to możliwe, użyj tożsamości zarządzanej. Jest to najprostszy sposób przypisywania tożsamości do usługi wyszukiwania i powinien działać w większości scenariuszy. Jeśli używasz wielu kluczy dla indeksów i map synonimów lub jeśli rozwiązanie znajduje się w architekturze rozproszonej, która dyskwalifikuje uwierzytelnianie oparte na tożsamości, użyj [zaawansowanego, zarządzanego zewnętrznie podejścia usługi Azure Active Directory](#aad-app) opisanego na końcu tego artykułu.

 Ogólnie rzecz biorąc tożsamość zarządzana umożliwia usługi wyszukiwania do uwierzytelniania w usłudze Azure Key Vault bez przechowywania poświadczeń w kodzie. Cykl życia tego typu tożsamości zarządzanej jest powiązany z cyklem życia usługi wyszukiwania, która może mieć tylko jedną tożsamość zarządzaną. [Dowiedz się więcej o tożsamościach zarządzanych](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

1. Aby utworzyć tożsamość zarządzaną, [zaloguj się do portaluAzure](https://portal.azure.com) i otwórz pulpit nawigacyjny usługi wyszukiwania. 

1. Kliknij **pozycję Tożsamość** w lewym okienku nawigacji, zmień jej stan **na Włączone**i kliknij przycisk **Zapisz**.

![Włączanie tożsamości zarządzanej](./media/search-enable-msi/enable-identity-portal.png "Włączanie tożsamości manged")

## <a name="4---grant-key-access-permissions"></a>4 - Udzielanie uprawnień dostępu do kluczy

Aby umożliwić usłudze wyszukiwania korzystanie z klucza Usługi Key Vault, musisz przyznać usłudze wyszukiwania pewne uprawnienia dostępu.

Uprawnienia dostępu mogą zostać odwołane w danym momencie. Po odwołaniu każdy indeks usługi wyszukiwania lub mapa synonimów, która używa tego magazynu kluczy, stanie się bezużyteczny. Przywrócenie uprawnień dostępu do magazynu kluczy w późniejszym czasie spowoduje przywrócenie dostępu do mapy index\synonym. Aby uzyskać więcej informacji, zobacz [Bezpieczny dostęp do magazynu kluczy](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).

1. [Zaloguj się do witryny Azure portal](https://portal.azure.com) i otwórz stronę przeglądu magazynu kluczy. 

1. Wybierz ustawienie **Zasady programu Access** z lewego okienka nawigacji i kliknij przycisk **+Dodaj nowy**.

   ![Dodawanie nowych zasad dostępu do magazynu kluczy](./media/search-manage-encryption-keys/add-new-key-vault-access-policy.png "Dodawanie nowych zasad dostępu do magazynu kluczy")

1. Kliknij **pozycję Wybierz podmiot i** wybierz usługę Azure Cognitive Search. Można go wyszukać według nazwy lub identyfikatora obiektu, który został wyświetlony po włączeniu tożsamości zarządzanej.

   ![Wybieranie głównego zobowiązanego zasad dostępu do magazynu kluczy](./media/search-manage-encryption-keys/select-key-vault-access-policy-principal.png "Wybieranie głównego zobowiązanego zasad dostępu do magazynu kluczy")

1. Kliknij **na Uprawnienia klucz** i *wybierz*get , *Unwrap Key* i *Wrap Key*. Za pomocą szablonu *Usługi Azure Data Lake Storage lub Usługi Azure Storage* można szybko wybrać wymagane uprawnienia.

   Usługa Azure Cognitive Search musi być przyznawana z [następującymi uprawnieniami dostępu:](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-operations)

   * *Pobierz* - umożliwia usłudze wyszukiwania pobieranie publicznych części klucza w magazynie kluczy
   * *Wrap Key* - umożliwia usłudze wyszukiwania używanie klucza do ochrony wewnętrznego klucza szyfrowania
   * *Rozpakuj klucz* - umożliwia usłudze wyszukiwania używanie klucza do odkręcania wewnętrznego klucza szyfrowania

   ![Wybieranie uprawnień klucza zasad dostępu do magazynu kluczy](./media/search-manage-encryption-keys/select-key-vault-access-policy-key-permissions.png "Wybieranie uprawnień klucza zasad dostępu do magazynu kluczy")

1. Kliknij **przycisk OK** i **zapisz** zmiany zasad dostępu.

> [!Important]
> Zaszyfrowana zawartość w usłudze Azure Cognitive Search jest skonfigurowana do używania określonego klucza usługi Azure Key Vault z określoną **wersją.** W przypadku zmiany klucza lub wersji indeks lub mapa synonimów musi zostać zaktualizowana, aby użyć nowego klucza\version **przed** usunięciem poprzedniego klucza\version. Nie w przeciwnym razie spowoduje, że indeks lub mapa synonimów nie będzie bezużyteczna, a zawartość nie będzie mogła zostać odszyfrowana po utracie dostępu do klucza.   

## <a name="5---encrypt-content"></a>5 - Szyfrowanie zawartości

Tworzenie indeksu lub mapy synonimów zaszyfrowanych za pomocą klucza zarządzanego przez klienta nie jest jeszcze możliwe przy użyciu witryny Azure portal. Użyj interfejsu API REST usługi Azure Cognitive Search, aby utworzyć taki indeks lub mapę synonimów.

Mapa indeksu i synonimu obsługuje nową właściwość szyfrowania najwyższego **poziomuKey** używana do określania klucza. 

Korzystając z **uri magazynu kluczy**, **nazwy klucza** i **wersji klucza** klucza magazynu kluczy, możemy utworzyć definicję **szyfrowaniaKey:**

```json
{
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
  }
}
```
> [!Note] 
> Żadne z tych szczegółów magazynu kluczy są uważane za tajne i można je łatwo pobrać, przeglądając odpowiednią stronę klucza usługi Azure Key Vault w witrynie Azure portal.

Jeśli używasz aplikacji AAD do uwierzytelniania usługi Key Vault zamiast tożsamości zarządzanej, dodaj **poświadczenia dostępu aplikacji** AAD do klucza szyfrowania: 
```json
{
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

## <a name="example-index-encryption"></a>Przykład: szyfrowanie indeksu
Szczegóły tworzenia nowego indeksu za pośrednictwem interfejsu API REST można znaleźć w [Create Index (Azure Cognitive Search REST API),](https://docs.microsoft.com/rest/api/searchservice/create-index)gdzie jedyną różnicą w tym miejscu jest określenie szczegółów klucza szyfrowania jako część definicji indeksu: 

```json
{
 "name": "hotels",  
 "fields": [
  {"name": "HotelId", "type": "Edm.String", "key": true, "filterable": true},
  {"name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false},
  {"name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene"},
  {"name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
  {"name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
  {"name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true},
  {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true},
  {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true},
 ],
 "encryptionKey": {
   "keyVaultUri": "https://demokeyvault.vault.azure.net",
   "keyVaultKeyName": "myEncryptionKey",
   "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
 }
}
```
Teraz można wysłać żądanie tworzenia indeksu, a następnie rozpocząć korzystanie z indeksu normalnie.

## <a name="example-synonym-map-encryption"></a>Przykład: Szyfrowanie mapy synonimów

Szczegóły tworzenia nowej mapy synonimów za pośrednictwem interfejsu API REST można znaleźć w [temacie Tworzenie mapy synonimów (interfejs API rest usługi Azure Cognitive Search),](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map)gdzie jedyną różnicą jest określenie szczegółów klucza szyfrowania jako części definicji mapy synonimów: 

```json
{   
  "name" : "synonymmap1",  
  "format" : "solr",  
  "synonyms" : "United States, United States of America, USA\n
  Washington, Wash. => WA",
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
  }
}
```
Teraz możesz wysłać żądanie utworzenia mapy synonimu, a następnie rozpocząć korzystanie z niego normalnie.

>[!Important] 
> Chociaż **szyfrowaniaKey** nie można dodać do istniejących indeksów usługi Azure Cognitive Search lub map synonimów, może być aktualizowany przez podanie różnych wartości dla dowolnego z trzech szczegółów magazynu kluczy (na przykład aktualizowanie wersji klucza). Podczas zmiany na nowy klucz usługi Key Vault lub nowej wersji klucza, każdy indeks usługi Azure Cognitive Search lub mapa synonimów, która używa klucza, musi najpierw zostać zaktualizowany, aby użyć nowego klucza\version **przed** usunięciem poprzedniego klucza\wersji. Nie można tego zrobić spowoduje, że indeks lub mapa synonimów nie będzie bezużyteczna, ponieważ nie będzie ona mogła odszyfrować zawartości po utracie dostępu do klucza.   
> Przywrócenie uprawnień dostępu do magazynu kluczy w późniejszym czasie przywróci dostęp do zawartości.

## <a name="advanced-use-an-externally-managed-azure-active-directory-application"></a><a name="aad-app"></a>Zaawansowane: korzystanie z zewnętrznie zarządzanej aplikacji usługi Azure Active Directory

Jeśli tożsamość zarządzana nie jest możliwa, można utworzyć aplikację usługi Azure Active Directory z podmiotem zabezpieczeń dla usługi Azure Cognitive Search. W szczególności tożsamość zarządzana nie jest opłacalna w następujących warunkach:

* Nie można bezpośrednio udzielić uprawnień dostępu do usługi wyszukiwania do magazynu kluczy (na przykład, jeśli usługa wyszukiwania znajduje się w innej dzierżawie usługi Active Directory niż usługa Azure Key Vault).

* Jedna usługa wyszukiwania jest wymagana do obsługi wielu zaszyfrowanych indeksów\map synonimów, z których każda używa innego klucza z innego magazynu kluczy, gdzie każdy magazyn kluczy musi używać **innej tożsamości** do uwierzytelniania. Jeśli używanie innej tożsamości do zarządzania różnymi magazynami kluczy nie jest wymaganiem, należy rozważyć użycie powyższej opcji tożsamości zarządzanej.  

Aby uwzględnić takie topologie, usługa Azure Cognitive Search obsługuje korzystanie z aplikacji usługi Azure Active Directory (AAD) do uwierzytelniania między usługą wyszukiwania a usługą Key Vault.    
Aby utworzyć aplikację usługi AAD w portalu:

1. [Utworzenie aplikacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application).

1. [Pobierz identyfikator aplikacji i klucz uwierzytelniania,](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) ponieważ będą wymagane do utworzenia zaszyfrowanego indeksu. Wartości, które należy podać, obejmują **identyfikator aplikacji** i **klucz uwierzytelniania**.

>[!Important]
> Decydując się na użycie aplikacji AAD uwierzytelniania zamiast tożsamości zarządzanej, należy wziąć pod uwagę fakt, że usługa Azure Cognitive Search nie jest autoryzowana do zarządzania aplikacją AAD w Twoim imieniu, a zarządzanie aplikacją AAD należy do użytkownika, na przykład okresowe klucza uwierzytelniania aplikacji.
> Podczas zmiany aplikacji AAD lub jej klucza uwierzytelniania, każdy indeks usługi Azure Cognitive Search lub mapa synonimów, która używa tej aplikacji, musi najpierw zostać zaktualizowana, aby użyć nowego identyfikatora aplikacji\klucza **przed** usunięciem poprzedniej aplikacji lub jej klucza autoryzacji, a przed cofnięciem dostępu do niego usługi Key Vault.
> Nie można tego zrobić spowoduje, że indeks lub mapa synonimów nie będzie bezużyteczna, ponieważ nie będzie ona mogła odszyfrować zawartości po utracie dostępu do klucza.   

## <a name="next-steps"></a>Następne kroki

Jeśli nie znasz architektury zabezpieczeń platformy Azure, zapoznaj się z [dokumentacją usługi Azure Security,](https://docs.microsoft.com/azure/security/)a w szczególności z tym artykułem:

> [!div class="nextstepaction"]
> [Szyfrowanie danych w spoczynku](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
