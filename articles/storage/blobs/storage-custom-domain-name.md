---
title: Mapowanie domeny niestandardowej do punktu końcowego usługi Azure Blob Storage
titleSuffix: Azure Storage
description: Mapowanie domeny niestandardowej do magazynu obiektów Blob lub punktu końcowego sieci Web na koncie magazynu platformy Azure.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: normesta
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: 9d05677ec47851557594ef47499da653accad141
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370478"
---
# <a name="map-a-custom-domain-to-an-azure-blob-storage-endpoint"></a>Mapowanie domeny niestandardowej do punktu końcowego usługi Azure Blob Storage

Domenę niestandardową można mapować do punktu końcowego usługi obiektu blob lub statycznego punktu końcowego [witryny sieci Web.](storage-blob-static-website.md) 

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

> [!NOTE] 
> To mapowanie działa tylko dla poddomen (na przykład: `www.contoso.com`). Jeśli chcesz, aby twój punkt końcowy sieci web był `contoso.com`dostępny w domenie głównej (na przykład: ), musisz użyć usługi Azure CDN. Aby uzyskać wskazówki, zobacz [sekcję Mapowanie domeny niestandardowej z włączoną obsługą PROTOKOŁU HTTPS](#enable-https) w tym artykule. Ponieważ przejście do tej sekcji tego artykułu, aby włączyć domenę główną domeny niestandardowej, krok w tej sekcji włączania protokołu HTTPS jest opcjonalny. 

<a id="enable-http" />

## <a name="map-a-custom-domain-with-only-http-enabled"></a>Mapowanie domeny niestandardowej z włączoną tylko funkcją HTTP

Takie podejście jest łatwiejsze, ale umożliwia tylko dostęp http. Jeśli konto magazynu jest skonfigurowane tak, aby [wymagało bezpiecznego transferu](../common/storage-require-secure-transfer.md) za pośrednictwem protokołu HTTPS, należy włączyć dostęp HTTPS dla domeny niestandardowej. 

Aby włączyć dostęp do protokołu HTTPS, zobacz [sekcję Mapowanie domeny niestandardowej z włączoną obsługą PROTOKOŁU HTTPS](#enable-https) w tym artykule. 

<a id="map-a-domain" />

### <a name="map-a-custom-domain"></a>Mapowanie domeny niestandardowej

> [!IMPORTANT]
> Twoja domena niestandardowa będzie na krótko niedostępna dla użytkowników po zakończeniu konfiguracji. Jeśli domena obsługuje obecnie aplikację z umową dotyczącą poziomu usług (SLA), która wymaga zerowego przestoju, wykonaj kroki opisane w sekcji [Mapowanie domeny niestandardowej z zerowym przestojem](#zero-down-time) tego artykułu, aby upewnić się, że użytkownicy będą mogli uzyskiwać dostęp do domeny podczas mapowania DNS.

Jeśli nie masz obaw, że domena jest na krótko niedostępna dla użytkowników, wykonaj następujące kroki.

:heavy_check_mark: Krok 1: Uzyskaj nazwę hosta punktu końcowego magazynu.

:heavy_check_mark: Krok 2: Utwórz rekord nazwy kanonicznej (CNAME) u dostawcy domeny.

:heavy_check_mark: Krok 3: Zarejestruj domenę niestandardową na platformie Azure. 

:heavy_check_mark: Krok 4: Przetestuj domenę niestandardową.

<a id="endpoint" />

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>Krok 1: Pobierz nazwę hosta punktu końcowego magazynu 

Nazwa hosta to adres URL punktu końcowego magazynu bez identyfikatora protokołu i końcowego ukośnika. 

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do konta magazynu.

2. W okienku menu w obszarze **Ustawienia**wybierz pozycję **Właściwości**.  

3. Skopiuj wartość **punktu końcowego podstawowej usługi obiektów blob** lub **podstawowego statycznego punktu końcowego witryny sieci Web** do pliku tekstowego. 

4. Usuń identyfikator protokołu *(np.* HTTPS) i końcowe ukośnik z tego ciągu. Poniższa tabela zawiera przykłady.

   | Typ punktu końcowego |  endpoint | nazwa hosta |
   |------------|-----------------|-------------------|
   |usługa obiektu blob  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |statyczna witryna internetowa  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   Odłóż tę wartość na później.

<a id="create-cname-record" />

#### <a name="step-2-create-a-canonical-name-cname-record-with-your-domain-provider"></a>Krok 2: Tworzenie rekordu nazwy kanonicznej (CNAME) u dostawcy domeny

Utwórz rekord CNAME, aby wskazać nazwę hosta. Rekord CNAME jest typem rekordu DNS, który mapuje nazwę domeny źródłowej na nazwę domeny docelowej.

1. Zaloguj się w witrynie sieci Web rejestratora domen, a następnie przejdź do strony, aby zarządzać ustawieniami DNS.

   Stronę można znaleźć w sekcji o nazwie **Nazwa domeny,** **DNS**lub **Zarządzanie serwerem nazw**.

2. Znajdź sekcję do zarządzania rekordami CNAME. 

   Może być trzeba przejść do strony ustawień zaawansowanych i poszukać **CNAME**, **Alias**lub **Subdomeny**.

3. Utwórz rekord CNAME. W ramach tego rekordu podaj następujące elementy: 

   - Alias poddomeny, `www` `photos`taki jak lub . Subdomena jest wymagana, domeny główne nie są obsługiwane. 
      
   - Nazwa hosta uzyskana w [sekcji Pobierz nazwę hosta punktu końcowego magazynu](#endpoint) wcześniej w tym artykule. 

<a id="register" />

#### <a name="step-3-register-your-custom-domain-with-azure"></a>Krok 3: Zarejestruj swoją domenę niestandardową na platformie Azure

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do konta magazynu.

2. W okienku menu w obszarze **Usługa obiektów Blob**wybierz pozycję **Domena niestandardowa**.  

   ![opcja domeny niestandardowej](./media/storage-custom-domain-name/custom-domain-button.png "domena niestandardowa")

   Zostanie otwarte **okienko Domeny niestandardowej.**

3. W polu tekstowym **Nazwa domeny** wprowadź nazwę domeny niestandardowej, w tym poddomenę  
   
   Jeśli na przykład domena jest *contoso.com,* a alias poddomeny to *www*, wprowadź `www.contoso.com`. Jeśli twoim poddomeną `photos.contoso.com`są *zdjęcia*, wprowadź .

4. Aby zarejestrować domenę niestandardową, wybierz przycisk **Zapisz.**

   Po propagacji rekordu CNAME za pośrednictwem serwerów nazw domen (DNS) i jeśli użytkownicy mają odpowiednie uprawnienia, mogą wyświetlać dane obiektów blob przy użyciu domeny niestandardowej.

#### <a name="step-4-test-your-custom-domain"></a>Krok 4: Testowanie domeny niestandardowej

Aby potwierdzić, że domena niestandardowa jest mapowana do punktu końcowego usługi obiektu blob, należy utworzyć obiekt blob w kontenerze publicznym na koncie magazynu. Następnie w przeglądarce sieci Web dostęp do obiektu blob przy użyciu identyfikatora URI w następującym formacie:`http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Na przykład, aby uzyskać dostęp do formularza sieci web w kontenerze *myforms* w *photos.contoso.com* niestandardowej poddomeny, można użyć następującego identyfikatora URI:`http://photos.contoso.com/myforms/applicationform.htm`

<a id="zero-down-time" />

### <a name="map-a-custom-domain-with-zero-downtime"></a>Mapowanie domeny niestandardowej z zerowym przestojem

> [!NOTE]
> Jeśli nie masz obaw, że domena jest na krótko niedostępna dla użytkowników, rozważ wykonać czynności opisane w sekcji [Mapowanie domeny niestandardowej](#map-a-domain) w tym artykule. Jest to prostsze podejście z mniejszą liczbą kroków.  

Jeśli domena obsługuje obecnie aplikację z umową dotyczącą poziomu usług (SLA), która wymaga zerowego przestoju, wykonaj następujące kroki, aby upewnić się, że użytkownicy będą mogli uzyskać dostęp do domeny podczas mapowania DNS. 

:heavy_check_mark: Krok 1: Uzyskaj nazwę hosta punktu końcowego magazynu.

:heavy_check_mark: Krok 2: Utwórz rekord nazwy kanonicznej pośrednika (CNAME) u dostawcy domeny.

:heavy_check_mark: Krok 3: Wstępnie zarejestruj domenę niestandardową na platformie Azure.

:heavy_check_mark: Krok 4: Utwórz rekord CNAME u dostawcy domeny.

:heavy_check_mark: Krok 5: Przetestuj domenę niestandardową.

<a id="endpoint-2" />

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>Krok 1: Pobierz nazwę hosta punktu końcowego magazynu 

Nazwa hosta to adres URL punktu końcowego magazynu bez identyfikatora protokołu i końcowego ukośnika. 

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do konta magazynu.

2. W okienku menu w obszarze **Ustawienia**wybierz pozycję **Właściwości**.  

3. Skopiuj wartość **punktu końcowego podstawowej usługi obiektów blob** lub **podstawowego statycznego punktu końcowego witryny sieci Web** do pliku tekstowego. 

4. Usuń identyfikator protokołu *(np.* HTTPS) i końcowe ukośnik z tego ciągu. Poniższa tabela zawiera przykłady.

   | Typ punktu końcowego |  endpoint | nazwa hosta |
   |------------|-----------------|-------------------|
   |usługa obiektu blob  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |statyczna witryna internetowa  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   Odłóż tę wartość na później.

#### <a name="step-2-create-a-intermediary-canonical-name-cname-record-with-your-domain-provider"></a>Krok 2: Tworzenie rekordu nazwy kanonicznej pośrednika (CNAME) u dostawcy domeny

Utwórz tymczasowy rekord CNAME, aby wskazać nazwę hosta. Rekord CNAME jest typem rekordu DNS, który mapuje nazwę domeny źródłowej na nazwę domeny docelowej.

1. Zaloguj się w witrynie sieci Web rejestratora domen, a następnie przejdź do strony, aby zarządzać ustawieniami DNS.

   Stronę można znaleźć w sekcji o nazwie **Nazwa domeny,** **DNS**lub **Zarządzanie serwerem nazw**.

2. Znajdź sekcję do zarządzania rekordami CNAME. 

   Może być trzeba przejść do strony ustawień zaawansowanych i poszukać **CNAME**, **Alias**lub **Subdomeny**.

3. Utwórz rekord CNAME. W ramach tego rekordu podaj następujące elementy: 

   - Alias poddomeny, `www` `photos`taki jak lub . Subdomena jest wymagana, domeny główne nie są obsługiwane.

     Dodaj `asverify` poddomenę do aliasu. Na `asverify.www` przykład: `asverify.photos`lub .
       
   - Nazwa hosta uzyskana w [sekcji Pobierz nazwę hosta punktu końcowego magazynu](#endpoint) wcześniej w tym artykule. 

     Dodaj poddomenę `asverify` do nazwy hosta. Na przykład: `asverify.mystorageaccount.blob.core.windows.net`.

4. Aby zarejestrować domenę niestandardową, wybierz przycisk **Zapisz.**

   Jeśli rejestracja zakończy się pomyślnie, portal powiadomi Cię, że twoje konto magazynu zostało pomyślnie zaktualizowane. Twoja domena niestandardowa została zweryfikowana przez platformę Azure, ale ruch do twojej domeny nie jest jeszcze kierowany do twojego konta magazynu.

#### <a name="step-3-pre-register-your-custom-domain-with-azure"></a>Krok 3: Wstępnie zarejestruj domenę niestandardową na platformie Azure

Podczas wstępnej rejestracji domeny niestandardowej na platformie Azure zezwalasz platformie Azure na rozpoznawanie domeny niestandardowej bez konieczności modyfikowania rekordu DNS dla domeny. W ten sposób po zmodyfikowaniu rekordu DNS dla domeny zostanie on mapowany do punktu końcowego obiektu blob bez przestojów.

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do konta magazynu.

2. W okienku menu w obszarze **Usługa obiektów Blob**wybierz pozycję **Domena niestandardowa**.  

   ![opcja domeny niestandardowej](./media/storage-custom-domain-name/custom-domain-button.png "domena niestandardowa")

   Zostanie otwarte **okienko Domeny niestandardowej.**

3. W polu tekstowym **Nazwa domeny** wprowadź nazwę domeny niestandardowej, w tym poddomenę  
   
   Jeśli na przykład domena jest *contoso.com,* a alias poddomeny to *www*, wprowadź `www.contoso.com`. Jeśli twoim poddomeną `photos.contoso.com`są *zdjęcia*, wprowadź .

4. Zaznacz pole wyboru **Użyj pośredniej weryfikacji CNAME.**

5. Aby zarejestrować domenę niestandardową, wybierz przycisk **Zapisz.**
  
   Po propagacji rekordu CNAME za pośrednictwem serwerów nazw domen (DNS) i jeśli użytkownicy mają odpowiednie uprawnienia, mogą wyświetlać dane obiektów blob przy użyciu domeny niestandardowej.

#### <a name="step-4-create-a-cname-record-with-your-domain-provider"></a>Krok 4: Tworzenie rekordu CNAME u dostawcy domeny

Utwórz tymczasowy rekord CNAME, aby wskazać nazwę hosta.

1. Zaloguj się w witrynie sieci Web rejestratora domen, a następnie przejdź do strony, aby zarządzać ustawieniami DNS.

   Stronę można znaleźć w sekcji o nazwie **Nazwa domeny,** **DNS**lub **Zarządzanie serwerem nazw**.

2. Znajdź sekcję do zarządzania rekordami CNAME. 

   Może być trzeba przejść do strony ustawień zaawansowanych i poszukać **CNAME**, **Alias**lub **Subdomeny**.

3. Utwórz rekord CNAME. W ramach tego rekordu podaj następujące elementy: 

   - Alias poddomeny, `www` `photos`taki jak lub . Subdomena jest wymagana, domeny główne nie są obsługiwane.
      
   - Nazwa hosta uzyskana w [sekcji Pobierz nazwę hosta punktu końcowego magazynu](#endpoint-2) wcześniej w tym artykule. 

#### <a name="step-5-test-your-custom-domain"></a>Krok 5: Testowanie domeny niestandardowej

Aby potwierdzić, że domena niestandardowa jest mapowana do punktu końcowego usługi obiektu blob, należy utworzyć obiekt blob w kontenerze publicznym na koncie magazynu. Następnie w przeglądarce sieci Web dostęp do obiektu blob przy użyciu identyfikatora URI w następującym formacie:`http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Na przykład, aby uzyskać dostęp do formularza sieci web w kontenerze *myforms* w *photos.contoso.com* niestandardowej poddomeny, można użyć następującego identyfikatora URI:`http://photos.contoso.com/myforms/applicationform.htm`

### <a name="remove-a-custom-domain-mapping"></a>Usuwanie mapowania domeny niestandardowej

Aby usunąć mapowanie domeny niestandardowej, należy wyrejestrować domenę niestandardową. Użyj jednej z następujących procedur.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

Aby usunąć niestandardowe ustawienie domeny, wykonaj następujące czynności:

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do konta magazynu.

2. W okienku menu w obszarze **Usługa obiektów Blob**wybierz pozycję **Domena niestandardowa**.  
   Zostanie otwarte **okienko Domeny niestandardowej.**

3. Wyczyść zawartość pola tekstowego zawierającego niestandardową nazwę domeny.

4. Wybierz ikonę **Zapisz**.

Po pomyślnym usunięciu domeny niestandardowej zostanie wyświetlone powiadomienie portalu o pomyślnym zaktualizowaniu konta magazynu

#### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby usunąć niestandardową rejestrację domeny, użyj polecenia aktualizacji interfejsu wiersza`""`polecenia [az storage,](https://docs.microsoft.com/cli/azure/storage/account) a następnie określ pusty ciąg ( ) dla wartości argumentu. `--custom-domain`

* Format polecenia:

  ```azurecli
  az storage account update \
      --name <storage-account-name> \
      --resource-group <resource-group-name> \
      --custom-domain ""
  ```

* Przykład polecenia:

  ```azurecli
  az storage account update \
      --name mystorageaccount \
      --resource-group myresourcegroup \
      --custom-domain ""
  ```

#### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Aby usunąć niestandardową rejestrację domeny, użyj polecenia cmdlet Programu PowerShell [set-AzStorageAccount,](/powershell/module/az.storage/set-azstorageaccount) a następnie określ pusty ciąg (`""`) dla wartości argumentu. `-CustomDomainName`

* Format polecenia:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "<resource-group-name>" `
      -AccountName "<storage-account-name>" `
      -CustomDomainName ""
  ```

* Przykład polecenia:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "myresourcegroup" `
      -AccountName "mystorageaccount" `
      -CustomDomainName ""
  ```
---

<a id="enable-https" />

## <a name="map-a-custom-domain-with-https-enabled"></a>Mapowanie domeny niestandardowej z włączoną obsługą protokołu HTTPS

Takie podejście obejmuje więcej kroków, ale umożliwia dostęp https. 

Jeśli nie potrzebujesz użytkowników, aby uzyskać dostęp do obiektu blob lub zawartości sieci Web przy użyciu protokołu HTTPS, zobacz [sekcję Mapuj domenę niestandardową z tylko włączoną funkcją HTTP](#enable-http) w tym artykule. 

Aby zamapować domenę niestandardową i włączyć dostęp https, wykonaj następujące czynności:

1. Włącz [usługę Azure CDN](../../cdn/cdn-overview.md) w obiekcie blob lub punkcie końcowym sieci Web. 

   Aby uzyskać punkt końcowy magazynu obiektów blob, zobacz [integrowanie konta magazynu platformy Azure z usługą Azure CDN.](../../cdn/cdn-create-a-storage-account-with-cdn.md) 

   Aby uzyskać statyczny punkt końcowy witryny sieci Web, zobacz [Integrowanie statycznej witryny sieci Web z usługą Azure CDN](static-website-content-delivery-network.md).

2. [Mapowanie zawartości usługi Azure CDN do domeny niestandardowej](../../cdn/cdn-map-content-to-custom-domain.md).

3. [Włącz protokół HTTPS w domenie niestandardowej usługi Azure CDN](../../cdn/cdn-custom-ssl.md).

   > [!NOTE] 
   > Podczas aktualizowania statycznej witryny sieci Web należy wyczyścić zawartość w pamięci podręcznej na serwerach krawędzi sieci CDN, czyszcząc punkt końcowy sieci CDN. Aby uzyskać więcej informacji, zobacz [Przeczyszczanie punktu końcowego usługi Azure CDN](../../cdn/cdn-purge-endpoint.md).

4. (Opcjonalnie) Zapoznaj się z następującymi wskazówkami:

   * [Tokeny sygnatury dostępu współdzielonego (SAS) za pomocą usługi Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-storage-custom-domain-https#shared-access-signatures).

   * [Przekierowanie http-to-HTTPS za pomocą usługi Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-storage-custom-domain-https#http-to-https-redirection).

   * [Ceny i rozliczenia podczas korzystania z usługi Blob Storage z usługą Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-storage-custom-domain-https#http-to-https-redirection).

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o statycznym hostingu witryny w magazynie obiektów Blob platformy Azure](storage-blob-static-website.md)
