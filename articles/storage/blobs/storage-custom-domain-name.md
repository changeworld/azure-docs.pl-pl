---
title: Konfigurowanie niestandardowej nazwy domeny dla punktu końcowego magazynu obiektów Blob platformy Azure | Dokumentacja firmy Microsoft
description: Użyj portalu Azure, aby zamapować własną nazwę kanoniczną (CNAME) do punktu końcowego magazynu obiektów Blob na koncie magazynu Azure.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 05/25/2017
ms.author: tamram
ms.openlocfilehash: 2b776e8f40f6972a60f933b0104312b119439f38
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/24/2018
ms.locfileid: "29559931"
---
# <a name="configure-a-custom-domain-name-for-your-blob-storage-endpoint"></a>Konfigurowanie niestandardowej nazwy domeny dla punktu końcowego usługi Blob Storage

Można skonfigurować niestandardową domenę na potrzeby uzyskiwania dostępu do danych obiektów blob na koncie magazynu Azure. Domyślny punkt końcowy dla magazynu obiektów Blob jest `<storage-account-name>.blob.core.windows.net`. Jeśli mapujesz niestandardowych domen i poddomen, takich jak **www.contoso.com** do punktu końcowego obiektu blob magazynu konta użytkowników można następnie uzyskać dostęp do danych obiektów blob na koncie magazynu przy użyciu tej domeny.

> [!IMPORTANT]
> Usługa Azure Storage jeszcze natywnie obsługuje HTTPS z domen niestandardowych. Obecnie można [umożliwia dostęp do obiektów blob z domenami niestandardowymi za pośrednictwem protokołu HTTPS Azure CDN](storage-https-custom-domain-cdn.md).
>

W poniższej tabeli przedstawiono kilka przykładowych adresy URL obiektu blob danych znajdujących się na koncie magazynu o nazwie **mojekontomagazynu**. Zarejestrowany domeny niestandardowej dla konta magazynu jest **www.contoso.com**:

| Typ zasobu | Domyślny adres URL | Adres URL domeny niestandardowej |
| --- | --- | --- |
| Konto magazynu | http://mystorageaccount.blob.core.windows.net | http://www.contoso.com |
| Obiekt blob |http://mystorageaccount.blob.core.windows.net/mycontainer/myblob | http://www.contoso.com/mycontainer/myblob |
| Nadrzędny kontener | http://mystorageaccount.blob.core.windows.net/myblob or http://mystorageaccount.blob.core.windows.net/$root/myblob| http://www.contoso.com/myblob lub http://www.contoso.com/$ głównego/mojblob |

## <a name="direct-vs-intermediary-domain-mapping"></a>Bezpośrednie a mapowanie pośredniczące domeny

Istnieją dwa sposoby, aby wskazywała domenę niestandardową punktu końcowego konta magazynu obiektów blob: bezpośrednie CNAME mapping, a za pomocą *asverify* pośredniczące poddomeny.

### <a name="direct-cname-mapping"></a>Bezpośrednie mapowanie CNAME

Metoda pierwszy i najprostszym, jest do utworzenia rekordu nazwę kanoniczną (CNAME), który mapuje Twoje niestandardowe domen i poddomen bezpośrednio do punktu końcowego obiektu blob. Rekord CNAME jest funkcją systemu (nazw domen DNS) nazwa domeny, która mapuje domeny źródłowej do domeny docelowej. W takim przypadku domeny źródłowej jest własnych niestandardowych domen i poddomen, na przykład *www.contoso.com*. Domena docelowego jest punkt końcowy usługi Blob, na przykład *mystorageaccount.blob.core.windows.net*.

Metoda bezpośrednia jest objęte [zarejestrować niestandardową domenę](#register-a-custom-domain).

### <a name="intermediary-mapping-with-asverify"></a>Mapowanie pośredniczące *asverify*

Druga metoda używa również rekordy CNAME, ale najpierw wykorzystuje specjalne poddomenę rozpoznawane przez platformę Azure, aby uniknąć przestoju: **asverify**.

Proces mapowania domeny niestandardowej do punktu końcowego obiektu blob może spowodować przez krótki czas przestoju dla domeny podczas rejestrowania w [portalu Azure](https://portal.azure.com). Jeśli aktualnie obsługiwanych aplikacji przy użyciu umowy poziomu usług (SLA) wymaga przestojów domenę niestandardową, a następnie platformy Azure można użyć *asverify* poddomeny jako etap pośredni rejestracji. Ten krok pośredniego gwarantuje, że użytkownicy będą mogli uzyskać dostępu do domeny, podczas mapowania DNS ma miejsce.

Pośredniczące metoda została opisana w [zarejestrować domeny niestandardowej przy użyciu *asverify* poddomeny](#register-a-custom-domain-using-the-asverify-subdomain).

## <a name="register-a-custom-domain"></a>Zarejestruj domeny niestandardowej
Użyj tej procedury, aby zarejestrować domeny niestandardowej, jeśli nie pytań dotyczących trwa krótko niedostępne dla użytkowników domeny lub domeny niestandardowej nie jest obecnie hostuje aplikację. Aby skonfigurować niestandardową nazwę DNS dla magazynu obiektów Blob platformy Azure, można użyć usługi Azure DNS. Aby uzyskać więcej informacji, zobacz [użycia usługi Azure DNS, aby określić ustawienia domeny niestandardowej dla usługi Azure](https://docs.microsoft.com/azure/dns/dns-custom-domain#blob-storage).

W przypadku domeny niestandardowej jest obecnie obsługi aplikacji, która nie może mieć żadnych przestojów, postępuj zgodnie z procedurą przedstawioną w [zarejestrować domeny niestandardowej przy użyciu *asverify* poddomeny](#register-a-custom-domain-using-the-asverify-subdomain).

Aby skonfigurować niestandardową nazwę domeny, należy utworzyć nowy rekord CNAME w systemie DNS. Rekord CNAME określa aliasu dla nazwy domeny. W takim przypadku mapuje adres domeny niestandardowej do punktu końcowego magazynu obiektów Blob dla konta magazynu.

Zazwyczaj można zarządzać ustawieniami DNS domeny w witrynie rejestratora domen. Każdy rejestrator ma podobne, lecz nieco inne metody określania rekord CNAME, ale koncepcja jest taka sama. Niektóre pakiety rejestracji domena podstawowa nie oferują konfiguracji DNS, może być konieczne uaktualnienie pakietu rejestracji domeny, aby można było utworzyć rekord CNAME.

1. Przejdź do swojego konta magazynu w [portalu Azure](https://portal.azure.com).
1. W obszarze **usługa BLOB** w bloku menu wybierz **domeny niestandardowe** otworzyć *domeny niestandardowe* bloku.
1. Zaloguj się w witrynie rejestratora domen i przejdź do strony zarządzania DNS. Może ona być zlokalizowana w sekcjach, takich jak **Domain Name** (Nazwa domeny), **DNS** (System DNS) lub **Name Server Management** (Zarządzanie serwerem nazw).
1. Znajdź sekcję zarządzania rekordami CNAME. Może być konieczne przejdź do strony ustawień zaawansowanych i poszukaj słowa **CNAME**, **Alias**, lub **poddomen**.
1. Utwórz nowy rekord CNAME i podaj alias poddomeny, takie jak **www** lub **zdjęć**. Następnie podaj nazwę hosta, który jest punkt końcowy usługi Blob, w formacie **mystorageaccount.blob.core.windows.net** (gdzie *mojekontomagazynu* jest nazwą konta magazynu). Nazwa hosta, aby użyć pojawia się w elemencie #1 *domeny niestandardowe* bloku w [portalu Azure](https://portal.azure.com).
1. W polu tekstowym na *domeny niestandardowe* bloku w [portalu Azure](https://portal.azure.com), wprowadź nazwę domeny niestandardowej, łącznie z poddomeny. Na przykład, jeśli Twoja domena to **contoso.com** i aliasu poddomeny **www**, wprowadź **www.contoso.com**. Jeśli Twoje domeny podrzędnej jest **zdjęć**, wprowadź **photos.contoso.com**. Jest poddomeną *wymagane*.
1. Wybierz **zapisać** na *domeny niestandardowe* bloku, aby zarejestrować domeny niestandardowej. Jeśli rejestracja zakończy się pomyślnie, zostanie wyświetlone powiadomienie portalu konta magazynu zostało pomyślnie zaktualizowane.

Po Twoje nowy rekord CNAME zakończeniem propagacji przy użyciu systemu DNS, użytkownicy mogą wyświetlać dane obiektów blob przy użyciu domenę niestandardową, tak długo, jak długo mają odpowiednie uprawnienia.

## <a name="register-a-custom-domain-using-the-asverify-subdomain"></a>Zarejestruj domeny niestandardowej przy użyciu *asverify* poddomeny
Ta procedura umożliwia rejestrowanie niestandardowe domeny w przypadku domeny niestandardowej jest obecnie obsługi aplikacji z umowy dotyczącej poziomu usług, które wymaga, aby nie istnieć bez przestojów. Przez utworzenie rekordu CNAME, który wskazuje z `asverify.<subdomain>.<customdomain>` do `asverify.<storageaccount>.blob.core.windows.net`, można wstępnie zarejestrować domeny z platformy Azure. Następnie możesz utworzyć drugi CNAME, który wskazuje z `<subdomain>.<customdomain>` do `<storageaccount>.blob.core.windows.net`, w którym ruch do domeny niestandardowej nastąpi przekierowanie do punktu końcowego obiektu blob.

**Asverify** poddomeny jest specjalne poddomenę rozpoznawane przez platformę Azure. Przez dołączanie `asverify` do własnych poddomeny, należy zezwolić na działanie Azure, aby rozpoznać domeny niestandardowej bez modyfikowania rekordów DNS dla domeny. Podczas modyfikowania rekordów DNS dla domeny, zostanie on zamapowany do punktu końcowego obiektu blob bez przestojów.

1. Przejdź do swojego konta magazynu w [portalu Azure](https://portal.azure.com).
1. W obszarze **usługa BLOB** w bloku menu wybierz **domeny niestandardowe** otworzyć *domeny niestandardowe* bloku.
1. Zaloguj się do witryny sieci Web dostawcy usługi DNS i przejdź do strony zarządzania DNS. Może ona być zlokalizowana w sekcjach, takich jak **Domain Name** (Nazwa domeny), **DNS** (System DNS) lub **Name Server Management** (Zarządzanie serwerem nazw).
1. Znajdź sekcję zarządzania rekordami CNAME. Może być konieczne przejdź do strony ustawień zaawansowanych i poszukaj słowa **CNAME**, **Alias**, lub **poddomen**.
1. Utwórz nowy rekord CNAME i podaj alias poddomeny, która obejmuje *asverify* poddomeny. Na przykład **asverify.www** lub **asverify.photos**. Następnie podaj nazwę hosta, który jest punkt końcowy usługi Blob, w formacie **asverify.mystorageaccount.blob.core.windows.net** (gdzie **mojekontomagazynu** jest nazwą konta magazynu). Nazwa hosta, aby użyć pojawia się w elemencie #2 *domeny niestandardowe* bloku w [portalu Azure](https://portal.azure.com).
1. W polu tekstowym na *domeny niestandardowe* bloku w [portalu Azure](https://portal.azure.com), wprowadź nazwę domeny niestandardowej, łącznie z poddomeny. Nie dołączaj *asverify*. Na przykład, jeśli Twoja domena to **contoso.com** i aliasu poddomeny **www**, wprowadź **www.contoso.com**. Jeśli Twoje domeny podrzędnej jest **zdjęć**, wprowadź **photos.contoso.com**. Poddomeny jest wymagana.
1. Wybierz **Użyj pośredniej weryfikacji CNAME** wyboru.
1. Wybierz **zapisać** na *domeny niestandardowe* bloku, aby zarejestrować domeny niestandardowej. Jeśli rejestracja zakończy się pomyślnie, zobaczysz portalu powiadomienie, że Twoje konto magazynu zostało pomyślnie zaktualizowane. W tym momencie domeny niestandardowej została zweryfikowana przez platformę Azure, ale nie ruch do Twojej domeny jest jeszcze rozsyłane do konta magazynu.
1. Wróć do witryny sieci Web dostawcy usługi DNS i utwórz inny rekord CNAME mapujący z poddomeny na punkt końcowy usługi Blob. Na przykład określić poddomeny jako **www** lub **zdjęć** (bez *asverify*) i nazwy hosta jako **mystorageaccount.blob.core.windows.net** (gdzie **mojekontomagazynu** jest nazwą konta magazynu). W ramach tego kroku zakończeniu rejestracji domeny niestandardowej.
1. Na koniec możesz usunąć rekord CNAME utworzonego zawierający **asverify** poddomeny, ponieważ było konieczne tylko jako etap pośredniczące.

Po Twoje nowy rekord CNAME zakończeniem propagacji przy użyciu systemu DNS, użytkownicy mogą wyświetlać dane obiektów blob przy użyciu domenę niestandardową, tak długo, jak długo mają odpowiednie uprawnienia.

## <a name="test-your-custom-domain"></a>Testowanie domenę niestandardową

Aby upewnić się, że domeny niestandardowej w rzeczywistości jest mapowany na punkt końcowy usługi Blob, należy utworzyć obiektu blob w kontenerze publicznym w ramach konta magazynu. Następnie w przeglądarce sieci web za pomocą identyfikatora URI w następującym formacie dostęp do obiektu blob:

`http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Na przykład można na przykład następujący identyfikator URI do formularza sieci web w **myforms** kontenera w **photos.contoso.com** poddomeny niestandardowych:

`http://photos.contoso.com/myforms/applicationform.htm`

## <a name="deregister-a-custom-domain"></a>Wyrejestrowania domeny niestandardowej

Do wyrejestrowania domeny niestandardowej dla punktu końcowego magazynu obiektów Blob, użyj jednej z następujących procedur.

### <a name="azure-portal"></a>Azure Portal

Wykonaj poniższe czynności w portalu Azure, aby usunąć ustawienia niestandardowe domeny:

1. Przejdź do swojego konta magazynu w [portalu Azure](https://portal.azure.com).
1. W obszarze **usługa BLOB** w bloku menu wybierz **domeny niestandardowe** otworzyć *domeny niestandardowe* bloku.
1. Czyszczenie zawartości pola tekstowego zawierającego niestandardową nazwę domeny.
1. Wybierz ikonę **Zapisz**.

Domena niestandardowa została pomyślnie usunął, zobaczysz portalu powiadomienie, że Twoje konto magazynu zostało pomyślnie zaktualizowane.

### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0

Użyj [aktualizacja konta magazynu az](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_update) CLI polecenia i określić ciąg pusty (`""`) dla `--custom-domain` wartość argumentu, aby usunąć rejestrację domeny niestandardowej.

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

Użyj [Set-AzureRmStorageAccount](/powershell/module/azurerm.storage/set-azurermstorageaccount) polecenia cmdlet programu PowerShell i określ ciąg pusty (`""`) dla `-CustomDomainName` wartość argumentu, aby usunąć rejestrację domeny niestandardowej.

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
* [Zamapować niestandardową domenę na punkt końcowy Azure sieci dostarczania zawartości (CDN)](../../cdn/cdn-map-content-to-custom-domain.md)
* [Dostęp do obiektów blob z domenami niestandardowymi za pośrednictwem protokołu HTTPS przy użyciu usługi Azure CDN](storage-https-custom-domain-cdn.md)
