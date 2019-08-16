---
title: Skonfiguruj niestandardową nazwę domeny dla konta usługi Azure Storage | Microsoft Docs
description: Użyj Azure Portal, aby zmapować własną nazwę kanoniczną (CNAME) do magazynu obiektów blob lub punktu końcowego sieci Web na koncie usługi Azure Storage.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: normesta
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: 2359befc05bff867a8f8b17943ed67d906ff4971
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2019
ms.locfileid: "69534343"
---
# <a name="configure-a-custom-domain-name-for-your-azure-storage-account"></a>Konfigurowanie niestandardowej nazwy domeny dla konta usługi Azure Storage

Można skonfigurować domenę niestandardową na potrzeby uzyskiwania dostępu do danych obiektów BLOB na koncie usługi Azure Storage. Domyślny punkt końcowy usługi Azure Blob Storage to  *\<Storage-account-name >. blob. Core. Windows. NET*. Można również użyć punktu końcowego sieci Web, który jest generowany w ramach [funkcji statyczne witryny sieci Web](storage-blob-static-website.md). Jeśli zamapujesz domenę niestandardową i poddomenę, taką jak *www\.contoso.com*, do punktu końcowego obiektu BLOB lub sieci Web dla konta magazynu, użytkownicy mogą korzystać z tej domeny w celu uzyskania dostępu do danych obiektów BLOB na koncie magazynu.

> [!IMPORTANT]
> Usługa Azure Storage nie obsługuje jeszcze natywnie protokołu HTTPS z domenami niestandardowymi. Obecnie można [używać Azure CDN do uzyskiwania dostępu do obiektów BLOB za pomocą domen niestandardowych za pośrednictwem protokołu HTTPS](storage-https-custom-domain-cdn.md).
> 
> 
> [!NOTE]
> Konta magazynu obsługują obecnie tylko jedną niestandardową nazwę domeny na konto. Nie można zmapować niestandardowej nazwy domeny do punktów końcowych usługi sieci Web i obiektów BLOB.
> 
> [!NOTE]
> Mapowanie działa tylko w przypadku poddomen (np. www\.contoso.com). Jeśli chcesz, aby punkt końcowy sieci Web był dostępny w domenie głównej (np. contoso.com), musisz [dodać domenę niestandardową do punktu końcowego Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-map-content-to-custom-domain).

W poniższej tabeli przedstawiono kilka przykładowych adresów URL dla danych obiektów blob, które znajdują się na koncie magazynu o nazwie *mojekontomagazynu*. Niestandardowa poddomena zarejestrowana dla konta magazynu to *contoso.com\.www*:

| Typ zasobu | Domyślny adres URL | Adres URL domeny niestandardowej |
| --- | --- | --- |
| Konto magazynu | http:\//mystorageaccount.blob.core.windows.net | http:\//www.contoso.com |
| Blob |http:\//mystorageaccount.blob.core.windows.net/mycontainer/myblob | http:\//www.contoso.com/mycontainer/myblob |
| Kontener główny | http:\//mystorageaccount.blob.Core.Windows.NET/myblob lub http:\//mystorageaccount.blob.Core.Windows.NET/$root/myblob | http:\//www.contoso.com/myblob lub http:\//www.contoso.com/$root/myblob |
| sieć Web |  http:\//mystorageaccount. [ Zone]. Web. Core. Windows. NET/$Web/[indexdoc] lub http:\//mystorageaccount. [ Zone]. Web. Core. Windows. NET/[indexdoc] lub http:\//mystorageaccount. [ Zone]. Web. Core. Windows. NET/$Web lub http:\//mystorageaccount. [ Strefa]. Web. Core. Windows. NET/ | http:\//www.contoso.com/$Web lub http:\//www.contoso.com/lub http:\//www.contoso.com/$Web/[indexdoc] lub http:\//www.contoso.com/[indexdoc] |

> [!NOTE]  
> Jak pokazano w poniższych sekcjach, wszystkie przykłady dla punktu końcowego usługi BLOB Service mają zastosowanie również do punktu końcowego usługi sieci Web.

## <a name="direct-vs-intermediary-cname-mapping"></a>Bezpośrednie a pośrednie mapowanie CNAME

Możesz wskazać domenę niestandardową poprzedzoną poddomeną (np. www\.contoso.com) do punktu końcowego obiektu BLOB dla konta magazynu na jeden z dwóch sposobów: 
* Użyj bezpośredniego mapowania CNAME.
* Użyj poddomeny *asverify* .

### <a name="direct-cname-mapping"></a>Bezpośrednie mapowanie CNAME

Pierwsza, najprostsza metoda polega na utworzeniu rekordu nazwy kanonicznej (CNAME), który mapuje domenę niestandardową i poddomenę bezpośrednio do punktu końcowego obiektu BLOB. Rekord CNAME jest funkcją systemu nazw domen (DNS), która mapuje domenę źródłową na domenę docelową. W naszym przykładzie domena źródłowa jest własną domeną niestandardową i poddomeną (na przykład*www\.contoso.com*). Domena docelowa to punkt końcowy usługi BLOB Service (na przykład*mystorageaccount.blob.Core.Windows.NET*).

Metoda bezpośrednia została omówiona w sekcji "Zarejestruj domenę niestandardową".

### <a name="intermediary-mapping-with-asverify"></a>Mapowanie pośrednie z *asverify*

Druga metoda używa również rekordów CNAME. Aby uniknąć przestoju, najpierw wykorzystuje specjalną poddomenę *asverify* , która jest rozpoznawana przez platformę Azure.

Mapowanie domeny niestandardowej do punktu końcowego obiektu BLOB może spowodować skrócenie okresu przestoju podczas rejestrowania domeny w [Azure Portal](https://portal.azure.com). Jeśli domena obsługuje obecnie aplikację z umową dotyczącą poziomu usług (SLA), która wymaga zero przestojów, użyj poddomeny *Asverify* platformy Azure jako etapu rejestracji pośredniej. Ten krok zapewnia, że użytkownicy będą mogli uzyskiwać dostęp do domeny podczas mapowania DNS.

Metoda pośrednika jest objęta rejestracją domeny niestandardowej przy użyciu poddomeny *asverify* .

## <a name="register-a-custom-domain"></a>Zarejestruj domenę niestandardową
Zarejestruj domenę, korzystając z procedury opisanej w tej sekcji, jeśli są spełnione następujące instrukcje:
* Nie masz żadnych danych, że domena jest krótko dostępna dla użytkowników.
* Domena niestandardowa nie obsługuje obecnie aplikacji. 

Za pomocą Azure DNS można skonfigurować niestandardową nazwę DNS magazynu obiektów blob platformy Azure. Aby uzyskać więcej informacji, zobacz [Use Azure DNS to provide custom domain settings for an Azure service](https://docs.microsoft.com/azure/dns/dns-custom-domain#blob-storage) (Korzystanie z usługi Azure DNS w celu udostępnienia niestandardowych ustawień domeny dla usługi platformy Azure).

Jeśli domena niestandardowa aktualnie obsługuje aplikację, która nie może mieć żadnego przestoju, użyj procedury opisanej w temacie Rejestrowanie domeny niestandardowej przy użyciu poddomeny *asverify* .

Aby skonfigurować niestandardową nazwę domeny, Utwórz nowy rekord CNAME w systemie DNS. Rekord CNAME określa alias dla nazwy domeny. W naszym przykładzie mapuje adres domeny niestandardowej na punkt końcowy magazynu obiektów BLOB Twojego konta magazynu.

Zazwyczaj można zarządzać ustawieniami DNS domeny w witrynie sieci Web rejestratora domen. Każdy Rejestrator ma podobną, ale nieco inną metodę określania rekordu CNAME, ale koncepcja jest taka sama. Ponieważ niektóre podstawowe pakiety rejestracji domeny nie oferują konfiguracji DNS, przed utworzeniem rekordu CNAME może być konieczna aktualizacja pakietu rejestracyjnego domeny.

1. W [Azure Portal](https://portal.azure.com)przejdź do konta magazynu.

1. W okienku menu w obszarze **Usługa BLOB**wybierz pozycję **domena**niestandardowa.  
   Zostanie otwarte okienko **domena** niestandardowa.

1. Zaloguj się do witryny sieci Web rejestratora domen, a następnie przejdź do strony, aby zarządzać systemem DNS.  
   Stronę można znaleźć w sekcji Nazwa **domeny**, **DNS**lub **Zarządzanie serwerem nazw**.

1. Znajdź sekcję zarządzania rekordami CNAME.  
   Może zajść potrzeba przejścia do strony ustawień zaawansowanych i poszukania **rekordu CNAME**, **aliasu**lub **poddomen**.

1. Utwórz nowy rekord CNAME, wprowadź alias subdomenowy, taki jak **www** lub **Photos** (poddomena jest wymagana, domeny główne nie są obsługiwane), a następnie podaj nazwę hosta.  
   Nazwa hosta jest punktem końcowym usługi BLOB Service. Jego format to  *\<mojekontomagazynu >. blob. Core. Windows. NET*, gdzie *mojekontomagazynu* to nazwa konta magazynu. Nazwa hosta do użycia pojawia się w #1 elementów okienka **domeny niestandardowej** w [Azure Portal](https://portal.azure.com). 

1. W okienku **domena** niestandardowa w polu tekstowym wprowadź nazwę domeny niestandardowej, w tym poddomenę.  
   Na przykład, jeśli Twoja domena to *contoso.com* , a alias domeny podrzędnej to *www*, **wprowadź\.contoso.com www**. Jeśli poddomeną są *Zdjęcia*, wprowadź **photos.contoso.com**.

1. Aby zarejestrować domenę niestandardową, wybierz pozycję **Zapisz**.  
   Jeśli rejestracja zakończyła się pomyślnie, w portalu zostanie wyświetlone powiadomienie o pomyślnym zaktualizowaniu konta magazynu.

Gdy nowy rekord CNAME zostanie rozpropagowany za pośrednictwem usługi DNS, jeśli użytkownicy mają odpowiednie uprawnienia, mogą wyświetlać dane obiektów BLOB przy użyciu domeny niestandardowej.

## <a name="register-a-custom-domain-by-using-the-asverify-subdomain"></a>Rejestrowanie domeny niestandardowej przy użyciu domeny podrzędnej *asverify*
Jeśli domena niestandardowa obecnie obsługuje aplikację z umową SLA wymagającą braku przestojów, zarejestruj domenę niestandardową, korzystając z procedury opisanej w tej sekcji. Przez utworzenie rekordu CNAME, który wskazuje z *asverify\< . poddomena >. żeelementcustomdomain\<>* *asverify.\< storageaccount >. blob. Core. Windows. NET*można wstępnie zarejestrować domenę na platformie Azure. Następnie można utworzyć drugi rekord CNAME, który wskazuje z  *\<poddomeny >.\< że element customdomain >* do  *\<storageaccount >. blob. Core. Windows. NET*, a następnie ruch do domeny niestandardowej jest kierowany do punktu końcowego obiektu BLOB.

Poddomeną *asverify* jest specjalna poddomena rozpoznawana przez platformę Azure. W zależności od tego, jak *asverify* się z własną poddomeną, pozwalamy platformie Azure rozpoznać swoją domenę niestandardową bez konieczności modyfikowania rekordu DNS dla domeny. Po zmodyfikowaniu rekordu DNS dla domeny zostanie on zmapowany do punktu końcowego obiektu BLOB bez przestoju.

1. W [Azure Portal](https://portal.azure.com)przejdź do konta magazynu.

1. W okienku menu w obszarze **Usługa BLOB**wybierz pozycję **domena**niestandardowa.  
   Zostanie otwarte okienko **domena** niestandardowa.

1. Zaloguj się do witryny sieci Web dostawcy DNS, a następnie przejdź do strony, aby zarządzać systemem DNS.  
   Stronę można znaleźć w sekcji Nazwa **domeny**, **DNS**lub **Zarządzanie serwerem nazw**.

1. Znajdź sekcję zarządzania rekordami CNAME.  
   Może zajść potrzeba przejścia do strony ustawień zaawansowanych i poszukania **rekordu CNAME**, **aliasu**lub **poddomen**.

1. Utwórz nowy rekord CNAME, podaj alias poddomen zawierający poddomenę *asverify* , taką jak **asverify. www** lub **asverify. Photos**, a następnie podaj nazwę hosta.  
   Nazwa hosta jest punktem końcowym usługi BLOB Service. Jego format to *asverify.\< mojekontomagazynu >. blob. Core. Windows. NET*, gdzie *mojekontomagazynu* to nazwa konta magazynu. Nazwa hosta do użycia pojawia się w #2 elementów okienka *domeny niestandardowej* w [Azure Portal](https://portal.azure.com).

1. W okienku **domena** niestandardowa w polu tekstowym wprowadź nazwę domeny niestandardowej, w tym poddomenę.  
   Nie dołączaj *asverify*. Na przykład, jeśli Twoja domena to *contoso.com* , a alias domeny podrzędnej to *www*, **wprowadź\.contoso.com www**. Jeśli poddomeną są *Zdjęcia*, wprowadź **photos.contoso.com**.

1. Zaznacz pole wyboru **Użyj pośredniego sprawdzania poprawności rekordu CNAME** .

1. Aby zarejestrować domenę niestandardową, wybierz pozycję **Zapisz**.  
   Jeśli rejestracja zakończyła się pomyślnie, w portalu zostanie wyświetlone powiadomienie o pomyślnym zaktualizowaniu konta magazynu. Domena niestandardowa została zweryfikowana przez platformę Azure, ale ruch do domeny nie jest jeszcze kierowany do konta magazynu.

1. Wróć do witryny sieci Web dostawcy DNS, a następnie utwórz inny rekord CNAME, który mapuje poddomenę na punkt końcowy usługi BLOB Service.  
   Na przykład określ poddomenę jako *www* lub *zdjęć* (bez *asverify*) i określ nazwę hosta jako  *\<mojekontomagazynu >. blob. Core. Windows. NET*, gdzie *mojekontomagazynu* to nazwa konto magazynu. W tym kroku zarejestrowano domenę niestandardową.

1. Na koniec można usunąć nowo utworzony rekord CNAME zawierający poddomenę *asverify* , która była wymagana tylko jako etap pośrednika.

Gdy nowy rekord CNAME zostanie rozpropagowany za pośrednictwem usługi DNS, jeśli użytkownicy mają odpowiednie uprawnienia, mogą wyświetlać dane obiektów BLOB przy użyciu domeny niestandardowej.

## <a name="test-your-custom-domain"></a>Przetestuj domenę niestandardową

Aby upewnić się, że domena niestandardowa została zamapowana na punkt końcowy usługi BLOB Service, Utwórz obiekt BLOB w kontenerze publicznym na koncie magazynu. Następnie w przeglądarce internetowej Uzyskuj dostęp do obiektu BLOB przy użyciu identyfikatora URI w następującym formacie:`http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Na przykład aby uzyskać dostęp do formularza sieci Web w kontenerze WebForms w niestandardowej poddomenie *photos.contoso.com* , można użyć następującego identyfikatora URI:`http://photos.contoso.com/myforms/applicationform.htm`

## <a name="deregister-a-custom-domain"></a>Wyrejestrowywanie domeny niestandardowej

Aby wyrejestrować domenę niestandardową dla punktu końcowego usługi BLOB Storage, należy użyć jednej z poniższych procedur.

### <a name="azure-portal"></a>Azure Portal

Aby usunąć ustawienie domeny niestandardowej, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com)przejdź do konta magazynu.

1. W okienku menu w obszarze **Usługa BLOB**wybierz pozycję **domena**niestandardowa.  
   Zostanie otwarte okienko **domena** niestandardowa.

1. Wyczyść zawartość pola tekstowego zawierającego swoją niestandardową nazwę domeny.

1. Wybierz ikonę **Zapisz**.

Po pomyślnym usunięciu domeny niestandardowej zobaczysz powiadomienie portalu, że konto magazynu zostało pomyślnie zaktualizowane.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby usunąć niestandardową rejestrację domeny, użyj polecenia [AZ Storage account Update](https://docs.microsoft.com/cli/azure/storage/account) CLI polecenie, a następnie określ pusty ciąg`""`() dla `--custom-domain` wartości argumentu.

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

Aby usunąć niestandardową rejestrację domeny, należy użyć polecenia cmdlet [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) programu PowerShell, a następnie określić`""`pusty ciąg ( `-CustomDomainName` ) dla wartości argumentu.

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

## <a name="next-steps"></a>Następne kroki
* [Mapowanie domeny niestandardowej do punktu końcowego usługi Azure Content Delivery Network (CDN)](../../cdn/cdn-map-content-to-custom-domain.md)
* [Używanie Azure CDN do uzyskiwania dostępu do obiektów BLOB za pomocą domen niestandardowych za pośrednictwem protokołu HTTPS](storage-https-custom-domain-cdn.md)
* [Hosting statycznej witryny sieci Web w usłudze Azure Blob Storage (wersja zapoznawcza)](storage-blob-static-website.md)
