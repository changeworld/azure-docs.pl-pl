---
title: Konfigurowanie niestandardowej nazwy domeny dla konta usługi Azure Storage | Dokumentacja firmy Microsoft
description: Użyj witryny Azure portal, aby zamapować swoją własną nazwę kanoniczną (CNAME) do punktu końcowego sieci web lub obiektu Blob na koncie usługi Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/26/2018
ms.author: tamram
ms.component: blobs
ms.openlocfilehash: 5fd823e9105157f8292d5a9554850b0f4338a392
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2018
ms.locfileid: "39398856"
---
# <a name="configure-a-custom-domain-name-for-your-azure-storage-account"></a>Konfigurowanie niestandardowej nazwy domeny dla konta usługi Azure Storage

Można skonfigurować niestandardową domenę umożliwiającą uzyskiwanie dostępu do danych obiektów blob na koncie usługi Azure storage. Domyślny punkt końcowy usługi Blob storage jest `<storage-account-name>.blob.core.windows.net`. Możesz również użyć internetowego punktu końcowego wygenerowane jako część [funkcji statycznych witryn sieci Web (wersja zapoznawcza)](storage-blob-static-website.md). Jeśli zamapujesz domenę niestandardową i poddomeny, takich jak **www.contoso.com** punktu końcowego magazynu obiektów blob lub sieci web konto usługi Użytkownicy mogą następnie uzyskać dostęp do danych obiektów blob na koncie magazynu przy użyciu tej domeny.

> [!IMPORTANT]
> Usługa Azure Storage jeszcze natywnie nie obsługują protokołu HTTPS z zastosowaniem domen niestandardowych. Obecnie można [dostęp do obiektów blob z zastosowaniem domen niestandardowych przy użyciu protokołu HTTPS za pomocą usługi Azure CDN](storage-https-custom-domain-cdn.md).
>

> [!NOTE]  
> Konta magazynu obsługują obecnie tylko jedna nazwa domeny niestandardowej na jednym koncie. Oznacza to, że nie można zamapować niestandardową nazwę domeny do punktów końcowych usługi sieci web i obiektów blob.

W poniższej tabeli przedstawiono kilka przykładowych adresy URL obiektu blob danych znajdujących się na koncie magazynu o nazwie **mystorageaccount**. Domena niestandardowa zarejestrowany dla konta magazynu jest **www.contoso.com**:

| Typ zasobu | Domyślny adres URL | Adres URL domeny niestandardowej |
| --- | --- | --- | --- |
| Konto magazynu | http://mystorageaccount.blob.core.windows.net | http://www.contoso.com |
| Obiekt blob |http://mystorageaccount.blob.core.windows.net/mycontainer/myblob | http://www.contoso.com/mycontainer/myblob |
| Nadrzędny kontener | http://mystorageaccount.blob.core.windows.net/myblob lub http://mystorageaccount.blob.core.windows.net/$root/myblob| http://www.contoso.com/myblob lub http://www.contoso.com/$root/myblob |
| Sieć Web |  http://mystorageaccount. [zone].web.core.windows.net/$web/[indexdoc] lub http://mystorageaccount. [ Zone].Web.Core.Windows.NET/[indexdoc] lub http://mystorageaccount. [ Zone].Web.Core.Windows.NET/$Web lub http://mystorageaccount. [ Zone].Web.Core.Windows.NET/ | http://www.contoso.com/$web lub http://www.contoso.com/ lub http://www.contoso.com/$web / [indexdoc] lub http://www.contoso.com/[indexdoc] |

> [!NOTE]  
> Wszystkie przykłady dla punktu końcowego usługi Blob poniżej dotyczą również punkt końcowy usługi sieci web.

## <a name="direct-vs-intermediary-domain-mapping"></a>Bezpośrednie a mapowanie domeny pośrednie

Istnieją dwa sposoby, aby wskazywała domenę niestandardową do punktu końcowego obiektu blob dla konta magazynu: bezpośrednie CNAME mapowania i przy użyciu *asverify* pośrednie poddomeny.

### <a name="direct-cname-mapping"></a>Bezpośrednie mapowanie rekordu CNAME

Jest metoda pierwszy i najprostszy, aby utworzyć rekord nazwy kanonicznej (CNAME), który mapuje swoje niestandardowe domeny i poddomeny bezpośrednio do punktu końcowego obiektu blob. Rekord CNAME jest funkcją systemu DNS nazwy domeny, która mapuje domenę źródłową na domenę docelową. W takim przypadku domeny źródłowej jest własne niestandardowe domeny i poddomeny, na przykład *www.contoso.com*. Domena docelowa jest na przykład do punktu końcowego usługi Blob *mystorageaccount.blob.core.windows.net*.

Metoda bezpośrednia zostało omówione w [rejestrowania domeny niestandardowej](#register-a-custom-domain).

### <a name="intermediary-mapping-with-asverify"></a>Pośrednie mapowanie z *asverify*

Druga metoda używa również rekordów CNAME, ale najpierw stosuje poddomeny specjalne rozpoznawane przez platformę Azure, aby uniknąć przestoju: **asverify**.

Proces mapowania domeny niestandardowej na punkt końcowy obiektu blob może spowodować krótki przestój domeny, podczas gdy rejestrujesz je w [witryny Azure portal](https://portal.azure.com). Jeśli aktualnie obsługiwanych aplikacji za pomocą umowy poziomu usług (SLA) wymaga przestojów domenę niestandardową, a następnie można użyć platformy Azure *asverify* poddomeny jako etap pośredni rejestracji. Ten krok pośrednich gwarantuje, że użytkownicy będą mogli uzyskiwać dostęp do domeny, podczas gdy odbywa się mapowanie DNS.

Metoda pośrednie są omówione w [zarejestrować się przy użyciu domeny niestandardowej *asverify* poddomeny](#register-a-custom-domain-using-the-asverify-subdomain).

## <a name="register-a-custom-domain"></a>Zarejestruj domenę niestandardową
Użyj tej procedury, aby zarejestrować domenę niestandardową, jeśli ze względu na nie wątpliwości dotyczących domeny trwa krótko niedostępne dla użytkowników lub domenę niestandardową obecnie nie obsługuje aplikacji. Aby skonfigurować niestandardową nazwę DNS dla magazynu obiektów Blob platformy Azure, można użyć usługi Azure DNS. Aby uzyskać więcej informacji, zobacz [Use Azure DNS to provide custom domain settings for an Azure service](https://docs.microsoft.com/azure/dns/dns-custom-domain#blob-storage) (Korzystanie z usługi Azure DNS w celu udostępnienia niestandardowych ustawień domeny dla usługi platformy Azure).

Jeśli obecnie obsługuje aplikację, która nie może mieć żadnych przestojów domeny niestandardowej, wykonaj procedurę opisaną w [zarejestrować się przy użyciu domeny niestandardowej *asverify* poddomeny](#register-a-custom-domain-using-the-asverify-subdomain).

Aby skonfigurować niestandardową nazwę domeny, należy utworzyć nowy rekord CNAME w systemie DNS. Rekord CNAME określa alias dla nazwy domeny. W tym przypadku jest on mapowany adres domenę niestandardową do punktu końcowego magazynu obiektów Blob konta magazynu.

Zazwyczaj można zarządzać ustawieniami DNS domeny w witrynie sieci Web swojego rejestratora domen. Każdy rejestrator ma nieco inną metodę określania rekordu CNAME, ale koncepcja jest taka sama. Niektóre pakiety rejestracji domeny podstawowe nie oferują konfiguracji DNS, więc należy uaktualnić pakiet rejestracji domeny, aby można było utworzyć rekord CNAME.

1. Przejdź do swojego konta magazynu w [witryny Azure portal](https://portal.azure.com).
1. W obszarze **usługę BLOB SERVICE** w bloku menu, wybierz **domena niestandardowa** otworzyć *domena niestandardowa* bloku.
1. Zaloguj się do witryny sieci Web swojego rejestratora domen i przejdź do strony zarządzania DNS. Może ona być zlokalizowana w sekcjach, takich jak **Domain Name** (Nazwa domeny), **DNS** (System DNS) lub **Name Server Management** (Zarządzanie serwerem nazw).
1. Znajdź sekcję zarządzania rekordami CNAME. Może mieć przejść do strony ustawień zaawansowanych i poszukanie słów **CNAME**, **Alias**, lub **poddomen**.
1. Utwórz nowy rekord CNAME i podaj alias domeny podrzędnej, takie jak **www** lub **zdjęcia**. Następnie podaj nazwę hosta, czyli usługi punktu końcowego usługi Blob, w formacie **mystorageaccount.blob.core.windows.net** (gdzie *mystorageaccount* jest nazwą konta magazynu). Nazwa hosta do użycia, który pojawia się w elemencie #1 *domena niestandardowa* bloku [witryny Azure portal](https://portal.azure.com).
1. W polu tekstowym na *domena niestandardowa* bloku [witryny Azure portal](https://portal.azure.com), wprowadź nazwę domeny niestandardowej, w tym poddomenę. Na przykład, jeśli Twoja domena to **contoso.com** i jest Twój alias poddomeny **www**, wprowadź **www.contoso.com**. Jeśli Twoje domeny podrzędnej jest **zdjęcia**, wprowadź **photos.contoso.com**. Jest poddomeną *wymagane*.
1. Wybierz **Zapisz** na *domena niestandardowa* bloku, aby zarejestrować domenę niestandardową. Jeśli rejestracja zakończy się pomyślnie, zostanie wyświetlone powiadomienie portalu konta usługi storage została pomyślnie zaktualizowana.

Po wykonaniu propagacji nowy rekord CNAME za pośrednictwem systemu DNS użytkownicy mogą wyświetlać dane obiektów blob przy użyciu domenę niestandardową, tak długo, jak długo mają odpowiednie uprawnienia.

## <a name="register-a-custom-domain-using-the-asverify-subdomain"></a>Zarejestrować się przy użyciu domeny niestandardowej *asverify* poddomeny
Użyj tej procedury, aby zarejestrować niestandardowe domeny w przypadku domeny niestandardowej jest obecnie obsługi aplikacji w ramach umowy SLA, który wymaga, aby istnieć bez przestojów. Tworząc rekord CNAME, który wskazuje z poddomeny `asverify.<subdomain>.<customdomain>` do `asverify.<storageaccount>.blob.core.windows.net`, można wstępnie rejestrować domenę przy użyciu platformy Azure. Następnie możesz utworzyć drugiego rekordu CNAME, który wskazuje z poddomeny `<subdomain>.<customdomain>` do `<storageaccount>.blob.core.windows.net`, w tym momencie nastąpi przekierowanie do punktu końcowego usługi blob ruchu do domeny niestandardowej.

**Asverify** poddomeny jest poddomeną specjalne rozpoznawane przez platformę Azure. Przez poprzedzenie jej `asverify` do własnych poddomeny zezwolić platforma Azure mogła rozpoznać domenę niestandardową, bez konieczności modyfikacji rekordu DNS dla domeny. Po zmodyfikowaniu rekordu DNS dla domeny, będzie można zamapować na punkt końcowy obiektu blob bez przestojów.

1. Przejdź do swojego konta magazynu w [witryny Azure portal](https://portal.azure.com).
1. W obszarze **usługę BLOB SERVICE** w bloku menu, wybierz **domena niestandardowa** otworzyć *domena niestandardowa* bloku.
1. Zaloguj się do witryny sieci Web dostawcy usługi DNS, a następnie przejdź do strony zarządzania DNS. Może ona być zlokalizowana w sekcjach, takich jak **Domain Name** (Nazwa domeny), **DNS** (System DNS) lub **Name Server Management** (Zarządzanie serwerem nazw).
1. Znajdź sekcję zarządzania rekordami CNAME. Może mieć przejść do strony ustawień zaawansowanych i poszukanie słów **CNAME**, **Alias**, lub **poddomen**.
1. Utwórz nowy rekord CNAME i podaj alias domeny podrzędnej, która zawiera *asverify* poddomeny. Na przykład **asverify.www** lub **asverify.photos**. Następnie podaj nazwę hosta, czyli usługi punktu końcowego usługi Blob, w formacie **asverify.mystorageaccount.blob.core.windows.net** (gdzie **mystorageaccount** jest nazwą konta magazynu). Nazwa hosta do użycia, który pojawia się w elemencie #2 *domena niestandardowa* bloku [witryny Azure portal](https://portal.azure.com).
1. W polu tekstowym na *domena niestandardowa* bloku [witryny Azure portal](https://portal.azure.com), wprowadź nazwę domeny niestandardowej, w tym poddomenę. Nie dołączaj *asverify*. Na przykład, jeśli Twoja domena to **contoso.com** i jest Twój alias poddomeny **www**, wprowadź **www.contoso.com**. Jeśli Twoje domeny podrzędnej jest **zdjęcia**, wprowadź **photos.contoso.com**. Poddomeny jest wymagana.
1. Wybierz **Użyj pośredniej weryfikacji CNAME** pola wyboru.
1. Wybierz **Zapisz** na *domena niestandardowa* bloku, aby zarejestrować domenę niestandardową. Jeśli rejestracja zakończy się pomyślnie, zobaczysz powiadomienie portalu z informacją, że konto magazynu zostało pomyślnie zaktualizowane. W tym momencie domena niestandardowa została zweryfikowana przez platformę Azure, ale ruch do Twojej domeny nie jest jeszcze jest kierowany do swojego konta magazynu.
1. Wróć do witryny internetowej dostawcy usługi DNS i utwórz inny rekord CNAME mapujący poddomenę na punkt końcowy usługi Blob service. Na przykład Podaj poddomenę jako **www** lub **zdjęcia** (bez *asverify*) i nazwę hosta jako **mystorageaccount.blob.core.windows.net**  (gdzie **mystorageaccount** jest nazwą konta magazynu). Ten krok zakończeniu rejestracji domeny niestandardowej.
1. Na koniec można usunąć rekord CNAME został utworzony, zawierający **asverify** poddomeny, ponieważ było konieczne tylko jako etap pośrednie.

Po wykonaniu propagacji nowy rekord CNAME za pośrednictwem systemu DNS użytkownicy mogą wyświetlać dane obiektów blob przy użyciu domenę niestandardową, tak długo, jak długo mają odpowiednie uprawnienia.

## <a name="test-your-custom-domain"></a>Testowanie domeny niestandardowej

Aby upewnić się, że domeny niestandardowej jest faktycznie mapowany do punktu końcowego usługi Blob service, Utwórz obiekt blob w kontenerze publicznym na koncie magazynu. Następnie w przeglądarce sieci web, użyj identyfikatora URI w następującym formacie dostępu obiektu blob do:

`http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Na przykład, może użyć następujący identyfikator URI na dostęp do formularza sieci web w **myforms** kontenera w **photos.contoso.com** niestandardowej domeny podrzędnej:

`http://photos.contoso.com/myforms/applicationform.htm`

## <a name="deregister-a-custom-domain"></a>Wyrejestrować domenę niestandardową

Aby wyrejestrować domenę niestandardową do punktu końcowego usługi Blob storage, użyj jednej z poniższych procedur.

### <a name="azure-portal"></a>Azure Portal

W witrynie Azure portal, aby usunąć ustawienia domeny niestandardowej, wykonaj następujące czynności:

1. Przejdź do swojego konta magazynu w [witryny Azure portal](https://portal.azure.com).
1. W obszarze **usługę BLOB SERVICE** w bloku menu, wybierz **domena niestandardowa** otworzyć *domena niestandardowa* bloku.
1. Wyczyść zawartość pola tekstowego, zawierający niestandardową nazwę domeny.
1. Wybierz ikonę **Zapisz**.

Domena niestandardowa została usunięta pomyślnie, zobaczysz powiadomienie portalu z informacją, że konto magazynu zostało pomyślnie zaktualizowane.

### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0

Użyj [aktualizacja konta magazynu az](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_update) interfejsu wiersza polecenia polecenie i wprowadź pusty ciąg (`""`) dla `--custom-domain` wartość argumentu do usunięcia rejestracji domeny niestandardowej.

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

### <a name="powershell"></a>PowerShell

Użyj [Set-AzureRmStorageAccount](/powershell/module/azurerm.storage/set-azurermstorageaccount) polecenia cmdlet programu PowerShell i określ pusty ciąg (`""`) dla `-CustomDomainName` wartość argumentu do usunięcia rejestracji domeny niestandardowej.

* Format polecenia:

  ```powershell
  Set-AzureRmStorageAccount `
      -ResourceGroupName "<resource-group-name>" `
      -AccountName "<storage-account-name>" `
      -CustomDomainName ""
  ```

* Przykład polecenia:

  ```powershell
  Set-AzureRmStorageAccount `
      -ResourceGroupName "myresourcegroup" `
      -AccountName "mystorageaccount" `
      -CustomDomainName ""
  ```

## <a name="next-steps"></a>Kolejne kroki
* [Mapowanie domeny niestandardowej do punktu końcowego usługi Azure Content Delivery Network (CDN)](../../cdn/cdn-map-content-to-custom-domain.md)
* [Dostęp do obiektów blob z zastosowaniem domen niestandardowych przy użyciu protokołu HTTPS przy użyciu usługi Azure CDN](storage-https-custom-domain-cdn.md)
* [Hostowania statycznej witryny internetowej w usłudze Azure Blob Storage (wersja zapoznawcza)](storage-blob-static-website.md)
