---
title: Szyfrowanie w spoczynku przy użyciu kluczy zarządzanych przez klienta w usłudze Azure Key Vault (wersja zapoznawcza) — usługa Azure Search
description: Szyfrowanie po stronie serwera dodatku indeksy i map synonimów w usłudze Azure Search przy użyciu kluczy, które umożliwiają tworzenie i zarządzanie nimi w usłudze Azure Key Vault.
author: NatiNimni
manager: jlembicz
ms.author: natinimn
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.custom: ''
ms.openlocfilehash: 9d2cd2a2f4b3143d58d0ef03d67de094ea03303e
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2019
ms.locfileid: "65523089"
---
# <a name="azure-search-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Szyfrowanie Azure Search przy użyciu kluczy zarządzanych przez klienta w usłudze Azure Key Vault

> [!Note]
> Szyfrowanie za pomocą kluczy zarządzanych przez klienta jest w wersji zapoznawczej i nie przeznaczonych do użycia w środowisku produkcyjnym. [Wersji interfejsu API REST 2019-05-06-Preview](search-api-preview.md) zapewnia tę funkcję. Można również użyć zestawu SDK platformy .NET w wersji 8.0-preview.
>
> Ta funkcja nie jest dostępna dla usług bezpłatnych. Należy użyć usługi wyszukiwania płatnych utworzone 2019-01-01. Brak obsługi portalu w tej chwili.

Domyślnie usługa Azure Search szyfruje zawartość użytkownika nieużywanych danych za pomocą [kluczy zarządzanych przez usługę](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest#data-encryption-models). Można je jednak uzupełnić domyślne szyfrowanie za pomocą warstwy dodatkowego szyfrowania przy użyciu kluczy, które umożliwiają tworzenie i zarządzanie nimi w usłudze Azure Key Vault. W tym artykule opisano kolejne kroki.

Szyfrowanie po stronie serwera jest obsługiwany dzięki integracji z usługą [usługi Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview). Można tworzyć własne klucze szyfrowania i przechowywać je w magazynie kluczy lub interfejsów API usługi Azure Key Vault umożliwia generowanie kluczy szyfrowania. Usługa Azure Key Vault, można również przeprowadzać inspekcję użycia klucza. 

Szyfrowanie za pomocą kluczy zarządzanych przez klienta jest skonfigurowana na poziomie mapy indeksu lub synonim te obiekty są tworzone, a nie na poziomie usługi wyszukiwania. Nie można zaszyfrować zawartość, która już istnieje. 

Możesz użyć różnych kluczy z różnych magazynów kluczy. Oznacza to, że usługa wyszukiwania pojedynczej może obsługiwać wiele map indexes\synonym zaszyfrowane, każdy zaszyfrowany, potencjalnie przy użyciu innego klucza zarządzanego przez klienta oraz maps indexes\synonym, które nie są szyfrowane przy użyciu kluczy zarządzanych przez klienta. 

## <a name="prerequisites"></a>Wymagania wstępne

Następujące usługi są używane w tym przykładzie. 

+ [Tworzenie usługi Azure Search](search-create-service-portal.md) lub [znaleźć istniejącej usługi](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach Twojej bieżącej subskrypcji. Umożliwia to bezpłatna usługa, w tym samouczku.

+ [Utwórz zasób usługi Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault) lub znaleźć istniejącego magazynu w ramach Twojej subskrypcji.

+ [Program Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) lub [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) jest używany dla zadań konfiguracji.

+ [Postman](search-fiddler.md), [programu Azure PowerShell](search-create-index-rest-api.md) i [zestawu SDK usługi Azure Search](https://aka.ms/search-sdk-preview) może służyć do wywołania interfejsu API REST w wersji zapoznawczej. Nie ma portalu lub zestawu .NET SDK Obsługa szyfrowania zarządzanego przez klienta w tej chwili.

## <a name="1---enable-key-recovery"></a>1 — Włączanie odzyskiwania kluczy

Ten krok jest opcjonalny, ale zdecydowanie zalecany. Po utworzeniu zasobu usługi Azure Key Vault, Włącz **usuwanie nietrwałe** i **przeczyścić ochrony** w wybranej usłudze Key vault, wykonując następujące polecenia programu PowerShell lub wiersza polecenia platformy Azure:   

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
> Ze względu na charakter szyfrowania za pomocą kluczy zarządzanych przez klienta funkcji Usługa Azure Search nie będzie można pobrać danych, usunięcie klucza usługi Azure Key vault. Aby uniknąć utraty danych spowodowanej przypadkowymi usunięciami klucza usługi Key Vault, zdecydowanie zaleca się Włącz usuwanie nietrwałe i przeczyścić ochrony na wybranego magazynu kluczy. Aby uzyskać więcej informacji, zobacz [usuwania nietrwałego w usłudze Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete).   

## <a name="2---create-a-new-key"></a>2 — Tworzenie nowego klucza

Jeśli używasz istniejącego klucza zawartości Azure Search, Pomiń ten krok.

1. [Zaloguj się do witryny Azure portal](https://portal.azure.com) i przejdź do pulpitu nawigacyjnego magazynu kluczy.

1. Wybierz **klucze** ustawienie z poziomu okienka nawigacji po lewej stronie, a następnie kliknij przycisk **+ Generuj/Import**.

1. W **Utwórz klucz** okienko z listy **opcje**, wybierz metodę, która ma zostać użyte do utworzenia klucza. Możesz **Generuj** nowy klucz **przekazywanie** istniejącego klucza, lub użyj **przywracanie kopii zapasowej** do wybierz kopię zapasową klucza.

1. Wprowadź **nazwa** związane z kluczem i opcjonalnie wybierz inne właściwości klucza.

1. Kliknij pozycję **Utwórz** przycisk, aby rozpocząć wdrażanie.

Zanotuj identyfikator klucza — składa się z **wartość identyfikatora Uri klucza**, **nazwa klucza**i **wersja klucza**. Należy je do definiowania indeksu zaszyfrowanej w usłudze Azure Search.
 
![Utwórz nowy klucz usługi key vault](./media/search-manage-encryption-keys/create-new-key-vault-key.png "Utwórz nowy klucz usługi key vault")

## <a name="3---create-a-service-identity"></a>3 — tworzenie tożsamości usługi

Przypisywanie tożsamości do usługi wyszukiwania umożliwia przyznawanie uprawnień dostępu do usługi Key Vault do usługi wyszukiwania. Usługa wyszukiwania użyje swojej tożsamości do uwierzytelniania za pomocą usługi Azure Key vault.

Usługa Azure Search obsługuje dwie metody przypisywania tożsamości: tożsamość zarządzaną lub zewnętrznie zarządzanych aplikacji usługi Azure Active Directory. 

Jeśli to możliwe Użyj tożsamość zarządzaną. Jest to najprostszy sposób przypisywania tożsamości do usługi wyszukiwania i powinna działać w większości scenariuszy. Jeśli używasz wielu kluczy dla indeksów i map synonimów, lub jeśli rozwiązanie jest w architekturze rozproszonej, która dyskwalifikuje uwierzytelniania opartego na tożsamości za pomocą zaawansowanych [zewnętrznie zarządzane usługi Azure Active Directory podejście](#aad-app)opisane na końcu tego artykułu.

 Ogólnie rzecz biorąc tożsamość zarządzaną umożliwia usługi wyszukiwania w celu uwierzytelniania w usłudze Azure Key Vault bez przechowywania poświadczeń w kodzie. Cykl życia tego typu tożsamości zarządzanej jest powiązany cyklu życia usługi wyszukiwania, który może mieć tylko jedna tożsamość zarządzana. [Dowiedz się więcej o tożsamości zarządzanych](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

1. Do tworzenia tożsamości zarządzanej [Zaloguj się w portalu toAzure](https://portal.azure.com) , a następnie otwórz pulpit nawigacyjny usługi wyszukiwania. 

1. Kliknij przycisk **tożsamości** w lewym okienku nawigacji, zmienić jego stan zostanie **na**i kliknij przycisk **Zapisz**.

![Włączanie tożsamości zarządzanej](./media/search-enable-msi/enable-identity-portal.png "włączyć tożsamości zarządzanych")

## <a name="4---grant-key-access-permissions"></a>4 — udzielanie uprawnień dostępu do kluczy

Aby włączyć usługi wyszukiwania do użycia tego klucza usługi Key Vault, musisz przyznać wyszukiwanie określonych uprawnień dostępu do usługi.

Uprawnienia dostępu można odwołać w dowolnym momencie. Gdy odwołany, staje się bezużyteczny dowolnego wyszukiwania indeksu lub synonim mapy usługi korzystającej z tego magazynu kluczy. Przywrócenie uprawnień dostępu do usługi Key vault w późniejszym czasie spowoduje przywrócenie index\synonym mapy dostępu. Aby uzyskać więcej informacji, zobacz [bezpieczny dostęp do magazynu kluczy](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).

1. [Zaloguj się do witryny Azure portal](https://portal.azure.com) , a następnie otwórz stronę przeglądu usługi key vault. 

1. Wybierz **zasady dostępu** ustawienie z poziomu okienka nawigacji po lewej stronie, a następnie kliknij przycisk **+ Dodaj nowe**.

   ![Dodaj nowe zasady dostępu magazynu kluczy](./media/search-manage-encryption-keys/add-new-key-vault-access-policy.png "Dodawanie nowych zasad dostępu magazynu kluczy")

1. Kliknij przycisk **Wybierz podmiot zabezpieczeń** i wybierz swoją usługę Azure Search. Można wyszukiwać go według nazwy lub Identyfikatora obiektu, który został wyświetlony po włączeniu tożsamości zarządzanej.

   ![Wybierz magazyn kluczy dostępu do zasady jednostki](./media/search-manage-encryption-keys/select-key-vault-access-policy-principal.png "jednostki zasad dostępu wybierz magazyn kluczy")

1. Kliknij pozycję **uprawnienia klucza** i wybierz *uzyskać*, *Odpakuj klucz* i *Opakuj klucz*. Możesz użyć *usługi Azure Data Lake Storage lub Azure Storage* szablon, aby szybko wybrać wymagane uprawnienia.

   Usługa Azure search musi mieć przyznane następującym [uprawnień dostępu](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-operations):

   * *Pobierz* — umożliwia usługi wyszukiwania pobrać publicznej części klucza w usłudze Key Vault
   * *Opakuj klucz* — umożliwia usługa wyszukiwania do użycia tego klucza do ochrony klucza szyfrowania wewnętrznego
   * *Odpakuj klucz* — umożliwia usługa wyszukiwania do użycia tego klucza do odkodowania klucza szyfrowania wewnętrznego

   ![Wybierz uprawnienia klucza zasad dostępu magazynu kluczy](./media/search-manage-encryption-keys/select-key-vault-access-policy-key-permissions.png "wybierz uprawnienia klucza zasad dostępu magazynu kluczy")

1. Kliknij przycisk **OK** i **Zapisz** zmian zasad dostępu.

> [!Important]
> Zaszyfrowanej zawartości w usłudze Azure search jest skonfigurowany do używania określonego klucza usługi Azure Key Vault z określonym **wersji**. Jeśli zmienisz klucza lub wersja indeksu lub synonim mapy należy zaktualizować tak, aby użyć nowych key\version **przed** usuwanie poprzedniej key\version. Kończy się niepowodzeniem, w tym celu spowoduje, że indeks lub synonim mapy uniemożliwiającym jego używanie, po użytkownik nie będzie można odszyfrować zawartość po utraty dostępu do kluczy.   

## <a name="5---encrypt-content"></a>5 — szyfrowanie zawartości

Tworzenie indeksu lub synonim mapy, zaszyfrowane za pomocą klucza zarządzanego przez klienta nie jest jeszcze możliwe przy użyciu witryny Azure portal. Azure Search REST API umożliwia utworzenie takiego indeksu lub synonim mapy.

Indeks i synonim mapowania obsługi nowej najwyższego poziomu **encryptionKey** właściwość używana do określenia klucza. 

Za pomocą **usługi key vault Uri**, **nazwa klucza** i **wersja klucza** o klucz usługi Key vault, możemy utworzyć **encryptionKey** definicji:

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
> Żadna z tych szczegółów magazynu kluczy są traktowane jako klucz tajny i można łatwo pobrać, przechodząc do odpowiednich strony klucza usługi Azure Key Vault w witrynie Azure portal.

Jeśli używasz aplikacji usługi AAD do uwierzytelniania usługi Key Vault, zamiast korzystać z tożsamości zarządzanej, dodaj aplikację AAD **poświadczenia dostępu** klucza szyfrowania: 
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
Szczegółowe informacje o utworzenie nowego indeksu za pomocą interfejsu API REST został odnaleziony w [Create Index (Azure Search Service interfejs API REST)](https://docs.microsoft.com/rest/api/searchservice/create-index), gdzie jedyną różnicą jest określanie szczegółów klucza szyfrowania jako część definicji indeksu: 

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
Możesz teraz wysyłać żądania utworzenia indeksu i rozpocząć korzystanie z indeksu normalnie.

## <a name="example-synonym-map-encryption"></a>Przykład: Szyfrowanie mapy synonimów

Szczegółowe informacje o tworzeniu nowej mapy synonimów za pośrednictwem interfejsu API REST, można znaleźć w folderze [tworzenia mapy synonimów (Azure Search Service interfejs API REST)](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map), gdzie jedyną różnicą jest określanie szczegółów klucza szyfrowania jako część definicji mapy synonimów: 

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
Można teraz wysyłać żądania utworzenia mapy synonimów, a następnie uruchom normalnie przy użyciu.

>[!Important] 
> Gdy **encryptionKey** nie można dodać do istniejącego indeksów usługi Azure Search lub mapy synonimów, może zostać zaktualizowany, podając różne wartości dla każdego z trzech szczegóły magazynu kluczy (na przykład aktualizowanie klucza wersji). Podczas zmiany na nowy klucz usługi Key Vault lub Nowa wersja klucza, najpierw należy zaktualizować dowolnej usługi Azure Search index lub synonim mapę, która używa klucza do użycia nowego key\version **przed** usuwanie poprzedniej key\version. Kończy się niepowodzeniem, w tym celu spowoduje, że indeks lub stanie się bezużyteczny, ponieważ nie będzie w stanie odszyfrować raz klucza dostępu do zawartości mapy synonimów zostaną utracone.   
> Przywrócenie uprawnień dostępu do usługi Key vault w późniejszym czasie spowoduje przywrócenie dostępu do zawartości.

## <a name="aad-app"></a> Zaawansowane: Użycie zewnętrznie zarządzanych aplikacji usługi Azure Active Directory

Jeśli tożsamość zarządzana nie jest możliwe, utworzeniem aplikacji usługi Azure Active Directory za pomocą zabezpieczeń podmiotu zabezpieczeń dla usługi Azure Search. W szczególności tożsamość zarządzana nie jest możliwego do użycia w tych warunkach:

* Nie można bezpośrednio udzielić wyszukiwania usługi uprawnienia dostępu do usługi Key vault (na przykład, jeśli usługa wyszukiwania jest w innej dzierżawie usługi Active Directory niż usługi Azure Key Vault).

* Usługa wyszukiwania pojedynczej jest wymagany do obsługi wielu map indexes\synonym zaszyfrowane, każdy przy użyciu innego klucza z innego magazynu kluczy, których należy użyć poszczególnych kluczy **inną tożsamość** do uwierzytelniania. Jeśli do zarządzania w różnych magazynach klucz przy użyciu innej tożsamości nie jest to wymagane, należy wziąć pod uwagę przy użyciu powyższych opcji tożsamość zarządzaną.  

Aby umożliwić takie topologii, Azure wyszukiwanie obsługuje uwierzytelniania między usługi wyszukiwania i usługi Key Vault przy użyciu aplikacji Azure Active Directory (AAD).    
Aby utworzyć aplikację AAD w portalu:

1. [Utworzenie aplikacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application).

1. [Pobierz aplikację Identyfikatora i klucza uwierzytelniania](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-application-id-and-authentication-key) jako będą wymagane do utworzenia zaszyfrowanego indeksu. Należy podać wartości obejmują **identyfikator aplikacji** i **klucz uwierzytelniania**.

>[!Important]
> Podejmując decyzję o na korzystanie z aplikacji usługi AAD, uwierzytelniania zamiast tożsamości zarządzanej, należy wziąć pod uwagę fakt, że usługa Azure Search nie ma uprawnień do zarządzania aplikacją usługi AAD w Twoim imieniu i jest maksymalnie umożliwia zarządzanie aplikacją usługi AAD, takie jak okresowe obrotu klucz uwierzytelniania aplikacji.
> Zmieniając aplikacja usługi AAD lub jej klucz uwierzytelniania, najpierw należy zaktualizować dowolnej usługi Azure Search index lub synonim mapę, która korzysta z tej aplikacji do korzystania z nowych aplikacji ID\key **przed** usunięcie poprzedniej aplikacji lub jej Uwierzytelnianie klucza, a przed odbieranie prawa dostępu usługi Key Vault do niego.
> Kończy się niepowodzeniem, w tym celu spowoduje, że indeks lub stanie się bezużyteczny, ponieważ nie będzie w stanie odszyfrować raz klucza dostępu do zawartości mapy synonimów zostaną utracone.   

## <a name="next-steps"></a>Kolejne kroki

Jeśli nie jesteś zaznajomiony z architekturą zabezpieczeń platformy Azure, zapoznaj się z [dokumentacja zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/security/)w szczególności, w tym artykule:

> [!div class="nextstepaction"]
> [Szyfrowanie danych w spoczynku](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)
