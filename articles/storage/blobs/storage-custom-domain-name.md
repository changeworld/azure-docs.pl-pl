---
title: Konfigurowanie niestandardowej nazwy domeny dla konta usługi Azure storage | Dokumentacja firmy Microsoft
description: Użyj witryny Azure portal, aby zamapować swoją własną nazwę kanoniczną (CNAME) do obiektu Blob magazynu lub sieci web punktu końcowego na koncie usługi Azure storage.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 06/26/2018
ms.author: normesta
ms.reviewer: seguler
ms.subservice: blobs
ms.openlocfilehash: 4f6776a5f15cf391f3a65aceb6e9e783d87a2078
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65148927"
---
# <a name="configure-a-custom-domain-name-for-your-azure-storage-account"></a>Konfigurowanie niestandardowej nazwy domeny dla konta usługi Azure storage

Można skonfigurować niestandardową domenę umożliwiającą uzyskiwanie dostępu do danych obiektów blob na koncie usługi Azure storage. Domyślny punkt końcowy usługi Azure Blob storage jest  *\<nazwa konta magazynu >. blob.core.windows.net*. Można również użyć internetowego punktu końcowego, który jest generowany jako część [funkcji statycznych witryn internetowych](storage-blob-static-website.md). Jeśli takie jak mapować domenę niestandardową i poddomeny, *www\.contoso.com*, do punktu końcowego sieci web lub obiektu blob dla konta usługi storage użytkownicy mogą korzystania z tej domeny do dostępu do danych obiektów blob na koncie magazynu.

> [!IMPORTANT]
> Usługa Azure Storage jeszcze natywnie nie obsługują protokołu HTTPS z zastosowaniem domen niestandardowych. Obecnie można [użycia usługi Azure CDN, dostęp do obiektów blob za pomocą domen niestandardowych przy użyciu protokołu HTTPS](storage-https-custom-domain-cdn.md).
> 
> 
> [!NOTE]
> Konta magazynu obsługują obecnie tylko jedna nazwa domeny niestandardowej na jednym koncie. Nie można zamapować niestandardową nazwę domeny do punktów końcowych usługi sieci web i obiektów blob.
> 
> [!NOTE]
> Mapowanie działać tylko przez poddomen (np. www\.contoso.com). Jeśli chcesz mieć dostępny punkt końcowy usługi sieci web w domenie katalogu głównego (np. contoso.com), a następnie trzeba [używanie usługi Azure CDN z zastosowaniem domen niestandardowych](storage-https-custom-domain-cdn.md)

W poniższej tabeli przedstawiono kilka przykładowych adresy URL danych obiektów blob, który znajduje się na koncie magazynu o nazwie *mystorageaccount*. Niestandardowe domeny podrzędnej, który jest zarejestrowany dla konta magazynu jest *www\.contoso.com*:

| Typ zasobu | Domyślny adres URL | Adres URL domeny niestandardowej |
| --- | --- | --- |
| Konto magazynu | http://mystorageaccount.blob.core.windows.net | http://www.contoso.com |
| Obiekt blob |http://mystorageaccount.blob.core.windows.net/mycontainer/myblob | http://www.contoso.com/mycontainer/myblob |
| Nadrzędny kontener | http://mystorageaccount.blob.core.windows.net/myblob lub http://mystorageaccount.blob.core.windows.net/$root/myblob| http://www.contoso.com/myblob lub http://www.contoso.com/$root/myblob |
| Sieć Web |  http://mystorageaccount. [zone].web.core.windows.net/$web/[indexdoc] lub http://mystorageaccount. [ Zone].Web.Core.Windows.NET/[indexdoc] lub http://mystorageaccount. [ Zone].Web.Core.Windows.NET/$Web lub http://mystorageaccount. [ Zone].Web.Core.Windows.NET/ | http://www.contoso.com/$web lub http://www.contoso.com/ lub http://www.contoso.com/$web / [indexdoc] lub http://www.contoso.com/[indexdoc] |

> [!NOTE]  
> Jak pokazano w poniższych sekcjach, wszystkie przykłady dla punktu końcowego usługi blob dotyczą również punkt końcowy usługi sieci web.

## <a name="direct-vs-intermediary-cname-mapping"></a>Bezpośrednie i pośrednie mapowanie rekordu CNAME

Można wskazać prefiksem poddomeny domeny niestandardowej (np. www\.contoso.com) do punktu końcowego obiektu blob dla konta magazynu w jednym z dwóch sposobów: 
* Użyj bezpośrednie mapowanie rekordu CNAME.
* Użyj *asverify* pośrednie poddomeny.

### <a name="direct-cname-mapping"></a>Bezpośrednie mapowanie rekordu CNAME

Jest metoda pierwszy i najprostszy, aby utworzyć rekord nazwy kanonicznej (CNAME), który mapuje swoje niestandardowe domeny i poddomeny bezpośrednio do punktu końcowego obiektu blob. Rekord CNAME jest funkcją systemu DNS nazwy domeny, która mapuje domenę źródłową na domenę docelową. W tym przykładzie domena źródłowa jest własne niestandardowe domeny i poddomeny (*www\.contoso.com*, na przykład). Domena docelowa jest punkt końcowy usługi blob service (*mystorageaccount.blob.core.windows.net*, na przykład).

Metoda bezpośrednia zostało opisane w sekcji "Register domenę niestandardową".

### <a name="intermediary-mapping-with-asverify"></a>Pośrednie mapowanie z *asverify*

Druga metoda używa także rekordy CNAME. Aby uniknąć przestoju, natomiast najpierw zatrudnia specjalne poddomeny *asverify* który jest rozpoznawany przez platformę Azure.

Mapowanie domeny niestandardowej na punkt końcowy obiektu blob może spowodować krótki przestój w przypadku rejestrowania domeny w [witryny Azure portal](https://portal.azure.com). Jeśli domena obsługuje obecnie aplikacji za pomocą umowy poziomu usług (SLA) wymaga przestojów, skorzystaj z Kalkulatora *asverify* poddomeny jako etap pośredni rejestracji. Ten krok zapewnia, że użytkownicy mają dostęp do domeny, podczas mapowania DNS.

Metoda pośrednie zostało omówione w domenie niestandardowej należy zarejestrować przy użyciu *asverify* poddomeny.

## <a name="register-a-custom-domain"></a>Zarejestruj domenę niestandardową
Zarejestruj domeny za pomocą procedury w tej sekcji, jeśli następujące instrukcje zastosowania:
* Jesteś unconcerned, że domena jest krótko niedostępne dla użytkowników.
* Domena niestandardowa nie jest obecnie obsługuje aplikację. 

Aby skonfigurować niestandardową nazwę DNS dla magazynu obiektów Blob platformy Azure, można użyć usługi Azure DNS. Aby uzyskać więcej informacji, zobacz [Use Azure DNS to provide custom domain settings for an Azure service](https://docs.microsoft.com/azure/dns/dns-custom-domain#blob-storage) (Korzystanie z usługi Azure DNS w celu udostępnienia niestandardowych ustawień domeny dla usługi platformy Azure).

Jeśli domenę niestandardową obecnie obsługuje aplikację, która nie może mieć żadnych przestojów, użyj procedury w rejestrze domenę niestandardową przy użyciu *asverify* poddomeny.

Aby skonfigurować niestandardową nazwę domeny, należy utworzyć nowy rekord CNAME w systemie DNS. Rekord CNAME określa alias dla nazwy domeny. W naszym przykładzie jest on mapowany adres domenę niestandardową do punktu końcowego konta magazynu Blob storage.

Zazwyczaj można zarządzać ustawień DNS domeny w witrynie sieci Web swojego rejestratora domen. Każdy rejestrator ma nieco inną metodę określania rekordu CNAME, ale koncepcja jest taka sama. Ponieważ niektóre pakiety rejestracji podstawowe domeny nie ma możliwości konfiguracji serwera DNS, może być konieczne uaktualnienie pakietu rejestracji domeny, aby można było utworzyć rekord CNAME.

1. W [witryny Azure portal](https://portal.azure.com), przejdź do swojego konta magazynu.

1. W okienku menu w obszarze **usługę Blob Service**, wybierz opcję **domena niestandardowa**.  
   **Domena niestandardowa** zostanie otwarte okienko.

1. Zaloguj się do witryny sieci Web swojego rejestratora domen, a następnie przejdź do strony zarządzania DNS.  
   Strony można znaleźć w sekcji o nazwie **nazwy domeny**, **DNS**, lub **Zarządzanie serwerem nazw**.

1. Znajdź sekcję zarządzania rekordami CNAME.  
   Może być konieczne przejście do strony ustawień zaawansowanych i poszukaj **CNAME**, **Alias**, lub **poddomen**.

1. Utwórz nowy rekord CNAME, wprowadź alias poddomeny, takie jak **www** lub **zdjęcia** (poddomeny jest wymagany, domeny katalogu głównego nie są obsługiwane), a następnie podaj nazwę hosta.  
   Nazwa hosta jest punkt końcowy usługi blob service. Jego format jest  *\<mystorageaccount >. blob.core.windows.net*, gdzie *mystorageaccount* jest nazwą konta magazynu. Nazwa hosta do użycia, który pojawia się w elemencie #1 **domena niestandardowa** okienka [witryny Azure portal](https://portal.azure.com). 

1. W **domena niestandardowa** okienko, w polu tekstowym wprowadź nazwę domeny niestandardowej, w tym poddomenę.  
   Na przykład, jeśli Twoja domena to *contoso.com* i jest Twój alias poddomeny *www*, wprowadź **www\.contoso.com**. Jeśli Twoje domeny podrzędnej jest *zdjęcia*, wprowadź **photos.contoso.com**.

1. Aby zarejestrować domenę niestandardową, zaznacz opcję **Zapisz**.  
   Jeśli rejestracja zakończy się pomyślnie, portalu powiadamia, konto magazynu zostało pomyślnie zaktualizowane.

Po nowy rekord CNAME jest propagowany za pośrednictwem systemu DNS, jeśli użytkownicy mają odpowiednie uprawnienia, mogą wyświetlać dane obiektów blob przy użyciu domeny niestandardowej.

## <a name="register-a-custom-domain-by-using-the-asverify-subdomain"></a>Zarejestruj niestandardową domenę przy użyciu *asverify* poddomeny
Jeśli domenę niestandardową obecnie obsługuje aplikacji w ramach umowy SLA, która wymaga istnienia bez przestojów, zarejestrować domenę niestandardową, wykonując procedurę podaną w tej sekcji. Tworząc rekord CNAME, który wskazuje z poddomeny *asverify.\< Poddomena >. \<customdomain >* do *asverify.\< Konto magazynu >. blob.core.windows.net*, można wstępnie rejestrować domenę przy użyciu platformy Azure. Następnie możesz utworzyć drugiego rekordu CNAME, który wskazuje z poddomeny  *\<poddomena >.\< customdomain >* do  *\<storageaccount >. blob.core.windows.net*, i kierowani ruchu do domeny niestandardowej do punktu końcowego usługi blob.

*Asverify* poddomeny jest poddomeną specjalne rozpoznawane przez platformę Azure. Przez poprzedzenie jej *asverify* do własnych poddomeny zezwolić platforma Azure mogła rozpoznać domenę niestandardową, bez konieczności modyfikowania rekordu DNS dla domeny. Po zmodyfikowaniu rekordu DNS dla domeny, będzie można zamapować na punkt końcowy obiektu blob bez przestojów.

1. W [witryny Azure portal](https://portal.azure.com), przejdź do swojego konta magazynu.

1. W okienku menu w obszarze **usługę Blob Service**, wybierz opcję **domena niestandardowa**.  
   **Domena niestandardowa** zostanie otwarte okienko.

1. Zaloguj się do witryny sieci Web dostawcy usługi DNS, a następnie przejdź do strony zarządzania DNS.  
   Strony można znaleźć w sekcji o nazwie **nazwy domeny**, **DNS**, lub **Zarządzanie serwerem nazw**.

1. Znajdź sekcję zarządzania rekordami CNAME.  
   Może być konieczne przejście do strony ustawień zaawansowanych i poszukaj **CNAME**, **Alias**, lub **poddomen**.

1. Utwórz nowy rekord CNAME, podaj alias domeny podrzędnej, która obejmuje *asverify* poddomeny, takie jak **asverify.www** lub **asverify.photos**, a następnie podaj nazwę hosta.  
   Nazwa hosta jest punkt końcowy usługi blob service. Jego format jest *asverify.\< mystorageaccount >. blob.core.windows.net*, gdzie *mystorageaccount* jest nazwą konta magazynu. Nazwa hosta do użycia, który pojawia się w elemencie #2 *domena niestandardowa* okienka [witryny Azure portal](https://portal.azure.com).

1. W **domena niestandardowa** okienko, w polu tekstowym wprowadź nazwę domeny niestandardowej, w tym poddomenę.  
   Nie dołączaj *asverify*. Na przykład, jeśli Twoja domena to *contoso.com* i jest Twój alias poddomeny *www*, wprowadź **www\.contoso.com**. Jeśli Twoje domeny podrzędnej jest *zdjęcia*, wprowadź **photos.contoso.com**.

1. Wybierz **Użyj pośredniej weryfikacji CNAME** pole wyboru.

1. Aby zarejestrować domenę niestandardową, zaznacz opcję **Zapisz**.  
   Jeśli rejestracja zakończy się pomyślnie, portalu powiadamia, konto magazynu zostało pomyślnie zaktualizowane. Domena niestandardowa została zweryfikowana przez platformę Azure, ale ruch do Twojej domeny nie jest jeszcze jest kierowany do swojego konta magazynu.

1. Wróć do witryny internetowej dostawcy usługi DNS, a następnie utwórz inny rekord CNAME mapujący poddomenę na punkt końcowy usługi blob service.  
   Na przykład Podaj poddomenę jako *www* lub *zdjęcia* (bez *asverify*) i określ nazwę hosta jako  *\<mystorageaccount >. blob.core.windows.net*, gdzie *mystorageaccount* jest nazwą konta magazynu. Ten krok zakończeniu rejestracji domeny niestandardowej.

1. Na koniec można usunąć nowo utworzony rekord CNAME, który zawiera *asverify* poddomeny, które były wymagane tylko jako etap pośrednie.

Po nowy rekord CNAME jest propagowany za pośrednictwem systemu DNS, jeśli użytkownicy mają odpowiednie uprawnienia, mogą wyświetlać dane obiektów blob przy użyciu domeny niestandardowej.

## <a name="test-your-custom-domain"></a>Testowanie domeny niestandardowej

Aby upewnić się, że Twoja domena niestandardowa została zamapowana na punkt końcowy usługi blob service, Utwórz obiekt blob w kontenerze publicznym na koncie magazynu. Następnie w przeglądarce sieci web dostęp do obiektu blob przy użyciu identyfikatora URI w następującym formacie: `http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Na przykład, aby przejść do formularza sieci web w *myforms* kontenera w *photos.contoso.com* poddomeny niestandardowych, można na przykład następujący identyfikator URI: `http://photos.contoso.com/myforms/applicationform.htm`

## <a name="deregister-a-custom-domain"></a>Wyrejestrować domenę niestandardową

Aby wyrejestrować domenę niestandardową do punktu końcowego usługi Blob storage, użyj jednej z poniższych procedur.

### <a name="azure-portal"></a>Azure Portal

Aby usunąć ustawienia domeny niestandardowej, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com), przejdź do swojego konta magazynu.

1. W okienku menu w obszarze **usługę Blob Service**, wybierz opcję **domena niestandardowa**.  
   **Domena niestandardowa** zostanie otwarte okienko.

1. Wyczyść zawartość pola tekstowego, który zawiera niestandardową nazwę domeny.

1. Wybierz ikonę **Zapisz**.

Po domena niestandardowa została usunięta pomyślnie, zostanie wyświetlone powiadomienie portalu konta usługi storage została pomyślnie zaktualizowana.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby usunąć rejestracji domeny niestandardowej, należy użyć [aktualizacja konta magazynu az](https://docs.microsoft.com/cli/azure/storage/account) interfejsu wiersza polecenia polecenie, a następnie określ pusty ciąg (`""`) dla `--custom-domain` wartość argumentu.

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

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Aby usunąć rejestracji domeny niestandardowej, należy użyć [AzStorageAccount zestaw](/powershell/module/az.storage/set-azstorageaccount) polecenia cmdlet programu PowerShell, a następnie określ pusty ciąg (`""`) dla `-CustomDomainName` wartość argumentu.

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

## <a name="next-steps"></a>Kolejne kroki
* [Mapowanie domeny niestandardowej do punktu końcowego usługi Azure Content Delivery Network (CDN)](../../cdn/cdn-map-content-to-custom-domain.md)
* [Używanie usługi Azure CDN dostęp do obiektów blob za pomocą domen niestandardowych przy użyciu protokołu HTTPS](storage-https-custom-domain-cdn.md)
* [Hostowania statycznej witryny internetowej w usłudze Azure Blob storage (wersja zapoznawcza)](storage-blob-static-website.md)
