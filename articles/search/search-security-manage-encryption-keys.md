---
title: Szyfrowanie — w spoczynku przy użyciu kluczy zarządzanych przez klienta w Azure Key Vault (wersja zapoznawcza) — Azure Search
description: Uzupełniaj szyfrowanie po stronie serwera za pośrednictwem indeksów i map synonimów w Azure Search za pomocą kluczy tworzonych i zarządzanych w programie Azure Key Vault.
author: NatiNimni
manager: nitinme
ms.author: natinimn
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.custom: ''
ms.openlocfilehash: ce7a8af1416664a3a94b248c95203c8e775e805c
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70182396"
---
# <a name="azure-search-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Azure Search szyfrowanie przy użyciu kluczy zarządzanych przez klienta w programie Azure Key Vault

> [!Note]
> Szyfrowanie za pomocą kluczy zarządzanych przez klienta jest w wersji zapoznawczej i nie jest przeznaczone do użycia w środowisku produkcyjnym. [Interfejs API REST w wersji 2019-05-06 —](search-api-preview.md) wersja zapoznawcza zawiera tę funkcję. Można również użyć zestawu .NET SDK w wersji 8,0-Preview.
>
> Ta funkcja jest niedostępna w przypadku bezpłatnych usług. Musisz użyć usługi wyszukiwania do rozliczenia, która została utworzona w dniu lub po 2019-01-01. W tej chwili nie ma pomocy technicznej dla portalu.

Domyślnie program Azure Search szyfruje zawartość użytkownika w stanie spoczynku przy użyciu [kluczy zarządzanych przez usługę](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest#data-encryption-models). Można uzupełnić domyślne szyfrowanie za pomocą dodatkowej warstwy szyfrowania przy użyciu kluczy tworzonych i zarządzanych w Azure Key Vault. Ten artykuł przeprowadzi Cię przez kroki.

Szyfrowanie po stronie serwera jest obsługiwane przez integrację z [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview). Możesz tworzyć własne klucze szyfrowania i przechowywać je w magazynie kluczy lub używać interfejsów API Azure Key Vault do generowania kluczy szyfrowania. Za pomocą Azure Key Vault można również prześledzić użycie klucza. 

Szyfrowanie przy użyciu kluczy zarządzanych przez klienta jest konfigurowane na poziomie indeksu lub synonimu podczas tworzenia tych obiektów, a nie na poziomie usługi wyszukiwania. Nie można zaszyfrować zawartości, która już istnieje. 

Można użyć różnych kluczy z różnych magazynów kluczy. Oznacza to, że pojedyncza usługa wyszukiwania może obsługiwać wiele szyfrowanych map indexes\synonym, z których każdy zaszyfrował potencjalnie przy użyciu innego klucza zarządzanego przez klienta, wraz z mapami indexes\synonym, które nie są szyfrowane przy użyciu kluczy zarządzanych przez klienta. 

## <a name="prerequisites"></a>Wymagania wstępne

W tym przykładzie są używane następujące usługi. 

+ [Utwórz usługę Azure Search](search-create-service-portal.md) lub [Znajdź istniejącą usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach bieżącej subskrypcji. W tym samouczku możesz użyć bezpłatnej usługi.

+ [Utwórz zasób Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault) lub Znajdź istniejący magazyn w ramach subskrypcji.

+ [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) lub [interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) jest używany na potrzeby zadań konfiguracyjnych.

+ [](search-get-started-postman.md)Do wywoływania interfejsu API REST w wersji zapoznawczej można użyć programu Poster, [Azure POWERSHELL](search-create-index-rest-api.md) i [Azure Search SDK](https://aka.ms/search-sdk-preview) . W tej chwili nie ma portalu ani zestawu SDK platformy .NET dla szyfrowania zarządzanego przez klienta.

## <a name="1---enable-key-recovery"></a>1 — Włączanie odzyskiwania klucza

Ten krok jest opcjonalny, ale zdecydowanie zalecany. Po utworzeniu zasobu Azure Key Vault Włącz **trwałe usuwanie** i przeczyszczanie **ochrony** w wybranym magazynie kluczy przez wykonanie następujących poleceń programu PowerShell lub interfejsu wiersza polecenia platformy Azure:   

```powershell
$resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

```azurecli-interactive
az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
```

>[!Note]
> Ze względu na bardzo dużą naturę szyfrowania z kluczami zarządzanymi przez klienta Azure Search nie będzie można pobrać danych, jeśli klucz magazynu kluczy platformy Azure zostanie usunięty. Aby zapobiec utracie danych przez przypadkowe Key Vault usuwania kluczy, zdecydowanie zaleca się włączenie trwałego usuwania i przeczyszczania w wybranym magazynie kluczy. Aby uzyskać więcej informacji, zobacz [Azure Key Vault usuwania](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)nietrwałego.   

## <a name="2---create-a-new-key"></a>2 — Tworzenie nowego klucza

Jeśli używasz istniejącego klucza do szyfrowania Azure Search zawartości, Pomiń ten krok.

1. [Zaloguj się do Azure Portal](https://portal.azure.com) i przejdź do pulpitu nawigacyjnego magazynu kluczy.

1. Wybierz ustawienie **klucze** w lewym okienku nawigacji, a następnie kliknij pozycję **+ Generuj/Importuj**.

1. W okienku **Utwórz klucz** z listy **opcji**wybierz metodę, która ma zostać użyta do utworzenia klucza. Można **wygenerować** nowy klucz, **przekazać** istniejący klucz lub użyć **Przywróć kopię zapasową** , aby wybrać kopię zapasową klucza.

1. Wprowadź **nazwę** klucza i opcjonalnie wybierz inne właściwości klucza.

1. Kliknij przycisk **Utwórz** , aby rozpocząć wdrażanie.

Zanotuj identyfikator klucza — to jest składający się z **identyfikatora URI wartości klucza**, **nazwy klucza**i **wersji klucza**. Będą one potrzebne do zdefiniowania zaszyfrowanego indeksu w Azure Search.
 
![Utwórz nowy klucz magazynu kluczy](./media/search-manage-encryption-keys/create-new-key-vault-key.png "Utwórz nowy klucz magazynu kluczy")

## <a name="3---create-a-service-identity"></a>3 — Tworzenie tożsamości usługi

Przypisanie tożsamości do usługi wyszukiwania umożliwia udzielenie Key Vault uprawnień dostępu do usługi wyszukiwania. Twoja usługa wyszukiwania będzie używać swojej tożsamości do uwierzytelniania w usłudze Azure Key magazyn.

Azure Search obsługuje dwa sposoby przypisywania tożsamości: zarządzaną tożsamością lub zewnętrznie zarządzaną aplikacją Azure Active Directory. 

Jeśli to możliwe, Użyj tożsamości zarządzanej. Jest to najprostszy sposób przypisywania tożsamości do usługi wyszukiwania i działania w większości scenariuszy. Jeśli używasz wielu kluczy w przypadku indeksów i map synonimów lub jeśli Twoje rozwiązanie znajduje się w rozproszonej architekturze, która nie kwalifikuje się do uwierzytelniania opartego na tożsamościach, użyj zaawansowanego, [zarządzanego na zewnątrz podejścia Azure Active Directory](#aad-app) opisanego na końcu tego artykułu.

 Ogólnie rzecz biorąc, zarządzana tożsamość umożliwia usłudze wyszukiwania uwierzytelnianie Azure Key Vault bez zapisywania poświadczeń w kodzie. Cykl życia tego typu tożsamości zarządzanej jest powiązany z cyklem życia usługi wyszukiwania, która może mieć tylko jedną tożsamość zarządzaną. [Dowiedz się więcej o tożsamościach zarządzanych](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

1. Aby utworzyć zarządzaną tożsamość, [Zaloguj się do portalu toAzure](https://portal.azure.com) i Otwórz pulpit nawigacyjny usługi wyszukiwania. 

1. Kliknij pozycję **tożsamość** w lewym okienku nawigacji, zmień jej stan na **włączone**, a następnie kliknij przycisk **Zapisz**.

![Włącz zarządzaną tożsamość](./media/search-enable-msi/enable-identity-portal.png "Włączanie zarządzanej tożsamości")

## <a name="4---grant-key-access-permissions"></a>4 — przyznaj uprawnienia dostępu do klucza

Aby umożliwić usłudze wyszukiwania korzystanie z klucza Key Vault, należy przyznać usłudze wyszukiwania określone uprawnienia dostępu.

Uprawnienia dostępu można odwołać w dowolnym momencie. Po odwołaniu każdy indeks usługi wyszukiwania lub mapa synonimu, która używa tego magazynu kluczy, stanie się bezużyteczny. Przywrócenie uprawnień dostępu do magazynu kluczy w późniejszym czasie spowoduje przywrócenie dostępu do mapy index\synonym. Aby uzyskać więcej informacji, zobacz [bezpieczny dostęp do magazynu kluczy](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).

1. [Zaloguj się do Azure Portal](https://portal.azure.com) i Otwórz stronę omówienia magazynu kluczy. 

1. Wybierz ustawienie **zasady dostępu** w lewym okienku nawigacji, a następnie kliknij pozycję **+ Dodaj nowe**.

   ![Dodawanie nowych zasad dostępu do magazynu kluczy](./media/search-manage-encryption-keys/add-new-key-vault-access-policy.png "Dodawanie nowych zasad dostępu do magazynu kluczy")

1. Kliknij pozycję **Wybierz podmiot zabezpieczeń** i wybierz usługę Azure Search. Możesz wyszukać je według nazwy lub identyfikatora obiektu, który został wyświetlony po włączeniu tożsamości zarządzanej.

   ![Wybierz podmiot zabezpieczeń zasad dostępu magazynu kluczy](./media/search-manage-encryption-keys/select-key-vault-access-policy-principal.png "Wybierz podmiot zabezpieczeń zasad dostępu magazynu kluczy")

1. Kliknij pozycję **uprawnienia klucza** i wybierz pozycję *Pobierz*, *Odpakuj klucz* i *Zawijaj klucz*. Możesz użyć szablonu *Azure Data Lake Storage lub Azure Storage* , aby szybko wybrać wymagane uprawnienia.

   Usługa Azure Search musi mieć przyznane następujące [uprawnienia dostępu](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-operations):

   * *Get* — zezwala usłudze wyszukiwania na pobieranie publicznych części klucza w Key Vault
   * *Zawijanie klucza* — umożliwia usłudze wyszukiwania używanie klucza do ochrony wewnętrznego klucza szyfrowania
   * *Odpakuj klucz* — umożliwia usłudze wyszukiwania używanie klucza w celu odpakowania wewnętrznego klucza szyfrowania

   ![Wybieranie uprawnień klucza zasad dostępu do magazynu kluczy](./media/search-manage-encryption-keys/select-key-vault-access-policy-key-permissions.png "Wybieranie uprawnień klucza zasad dostępu do magazynu kluczy")

1. Kliknij przycisk **OK** i **Zapisz** zmiany zasad dostępu.

> [!Important]
> Zaszyfrowana zawartość w usłudze Azure Search jest skonfigurowana do używania określonego klucza Azure Key Vault z określoną **wersją**. W przypadku zmiany klucza lub wersji należy zaktualizować indeks lub mapę synonimu, aby korzystała z nowego key\version **przed** usunięciem poprzedniej key\version. Niewykonanie tej czynności spowoduje, że indeks lub mapa synonimu stanie się niezdatna, nie będzie można odszyfrować zawartości po utracie dostępu do klucza.   

## <a name="5---encrypt-content"></a>5 — Szyfruj zawartość

Tworzenie indeksu lub mapy synonimów zaszyfrowanej przy użyciu klucza zarządzanego przez klienta nie jest jeszcze możliwe przy użyciu Azure Portal. Użyj interfejsu API REST Azure Search, aby utworzyć taki indeks lub mapę synonimów.

Zarówno indeks, jak i mapa synonimu obsługują nową właściwość **EncryptionKey** najwyższego poziomu użytą do określenia klucza. 

Korzystając z **identyfikatora URI magazynu kluczy**i **nazwy** klucza i **wersji klucza** magazynu kluczy, możemy utworzyć definicję **EncryptionKey** :

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
> Żadna z tych informacji nie jest uważana za klucz tajny i można ją łatwo pobrać, przechodząc do odpowiedniej strony klucza Azure Key Vault w Azure Portal.

Jeśli używasz aplikacji usługi AAD do uwierzytelniania Key Vault, a nie przy użyciu tożsamości zarządzanej, Dodaj **poświadczenia dostępu** do aplikacji usługi AAD do klucza szyfrowania: 
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

## <a name="example-index-encryption"></a>Przykład: Szyfrowanie indeksu
Szczegóły dotyczące tworzenia nowego indeksu za pośrednictwem interfejsu API REST można znaleźć w artykule [Tworzenie indeksu (interfejs API REST usługi Azure Search Service)](https://docs.microsoft.com/rest/api/searchservice/create-index), gdzie jedyną różnicą jest określenie informacji o kluczu szyfrowania w ramach definicji indeksu: 

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
Teraz możesz wysłać żądanie utworzenia indeksu, a następnie zacząć używać indeksu normalnie.

## <a name="example-synonym-map-encryption"></a>Przykład: Szyfrowanie mapy synonimów

Szczegóły tworzenia nowej mapy synonimów za pośrednictwem interfejsu API REST można znaleźć na stronie [Tworzenie mapy synonimów (Azure Search interfejs API REST usługi)](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map), gdzie jedyną różnicą jest określenie informacji o kluczu szyfrowania w ramach definicji mapy synonimów: 

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
Teraz można wysłać żądanie utworzenia mapy synonimów, a następnie rozpocząć korzystanie z niej normalnie.

>[!Important] 
> Mimo że nie można dodać **EncryptionKey** do istniejących indeksów Azure Search lub mapowań synonimów, można je zaktualizować, podając różne wartości dla każdego z trzech szczegółów magazynu kluczy (na przykład aktualizacji wersji klucza). W przypadku zmiany na nowy klucz Key Vault lub nowej wersji klucza należy najpierw zaktualizować wszystkie indeksy Azure Search indeksu lub synonimu, które używają klucza, aby użyć nowego key\version **przed** usunięciem poprzedniej key\version. Niewykonanie tej czynności spowoduje renderowanie indeksu lub mapy synonimów, ponieważ nie będzie można odszyfrować zawartości po utracie dostępu do klucza.   
> Przywrócenie uprawnień dostępu do magazynu kluczy w późniejszym czasie spowoduje przywrócenie dostępu do zawartości.

## <a name="aad-app"></a>Doświadczonych Korzystanie z zarządzanej zewnętrznie aplikacji Azure Active Directory

Jeśli zarządzana tożsamość nie jest możliwa, można utworzyć aplikację Azure Active Directory z podmiotem zabezpieczeń dla usługi Azure Search. W tym przypadku tożsamość zarządzana nie jest wykonalna w następujących warunkach:

* Nie można bezpośrednio udzielić uprawnień dostępu do usługi wyszukiwania do magazynu kluczy (na przykład jeśli usługa wyszukiwania znajduje się w innej dzierżawie Active Directory niż Azure Key Vault).

* Pojedyncza usługa wyszukiwania jest wymagana do hostowania wielu szyfrowanych map indexes\synonym, z których każdy korzysta z innego klucza niż inny magazyn kluczy, gdzie każdy Magazyn kluczy musi używać **innej tożsamości** do uwierzytelniania. Jeśli użycie innej tożsamości do zarządzania różnymi magazynami kluczy nie jest wymagane, należy rozważyć użycie opcji tożsamości zarządzanej powyżej.  

Aby można było obsłużyć takie topologie, usługa Azure Search obsługuje aplikacje Azure Active Directory (AAD) do uwierzytelniania między usługą wyszukiwania i Key Vault.    
Aby utworzyć aplikację usługi AAD w portalu:

1. [Utworzenie aplikacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application).

1. [Pobierz identyfikator aplikacji i klucz uwierzytelniania](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) , ponieważ będą one wymagane do utworzenia zaszyfrowanego indeksu. Wartości należy podać **Identyfikator aplikacji** i **klucz uwierzytelniania**.

>[!Important]
> Przy podejmowaniu decyzji o użyciu uwierzytelniania w usłudze AAD zamiast tożsamości zarządzanej należy wziąć pod uwagę fakt, że Azure Search nie jest autoryzowany do zarządzania aplikacją usługi AAD w Twoim imieniu i umożliwia zarządzanie aplikacją usługi AAD, na przykład okresowe obroty klucz uwierzytelniania aplikacji.
> W przypadku zmiany aplikacji usługi AAD lub jej klucza uwierzytelniania należy najpierw zaktualizować dowolny indeks Azure Search lub mapę synonimów, która używa tej aplikacji, aby użyć nowej aplikacji ID\key **przed** usunięciem poprzedniej aplikacji lub jej klucza autoryzacji. Przed odwołaniem Key Vault do niego dostępu.
> Niewykonanie tej czynności spowoduje renderowanie indeksu lub mapy synonimów, ponieważ nie będzie można odszyfrować zawartości po utracie dostępu do klucza.   

## <a name="next-steps"></a>Następne kroki

Jeśli nie znasz architektury zabezpieczeń platformy Azure, zapoznaj się z [dokumentacją zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/security/), a w szczególności w tym artykule:

> [!div class="nextstepaction"]
> [Szyfrowanie danych w spoczynku](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
