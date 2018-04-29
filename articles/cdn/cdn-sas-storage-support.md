---
title: Z sygnatury dostępu Współdzielonego przy użyciu usługi Azure CDN | Dokumentacja firmy Microsoft
description: Usługi Azure CDN obsługuje korzystanie z dostępu sygnatury dostępu Współdzielonego można przyznać ograniczony dostęp do magazynu prywatne kontenery.
services: cdn
documentationcenter: ''
author: dksimpson
manager: ''
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: v-deasim
ms.openlocfilehash: 09efd5cd54fbd05d85939b3ae08bfbb37e91058d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="using-azure-cdn-with-sas"></a>Z sygnatury dostępu Współdzielonego przy użyciu usługi Azure CDN

Po skonfigurowaniu konta magazynu dla usługi Azure Content Delivery Network (CDN) do użycia w celu buforowania zawartości domyślnie każdy użytkownik, który zna w adresach URL dla kontenerów magazynu można uzyskać dostępu do plików, które zostały przekazane. Aby chronić pliki na koncie magazynu, można ustawić dostęp kontenery magazynu z publicznej na prywatną. Jednak jeśli tak zrobisz, nie będzie dostępu do plików. 

Jeśli chcesz przyznać ograniczony dostęp do magazynu prywatne kontenery, można użyć funkcji dostępu sygnatury dostępu Współdzielonego konta magazynu Azure. Sygnatury dostępu Współdzielonego jest identyfikator URI, że przyznaje ograniczone prawa dostępu do zasobów usługi Azure Storage bez narażania klucz konta. Sygnatury dostępu Współdzielonego zapewnia klientom, którzy nie ufasz kluczem konta magazynu, ale do których chcesz delegować dostęp do niektórych zasobów konta magazynu. Rozkładając identyfikator URI sygnatury dostępu współdzielonego dla tych klientów, przyznano im dostęp do zasobu w określonym okresie czasu.
 
Z sygnatury dostępu Współdzielonego można określić różnych parametrów dostępu do obiektu blob, takie jak czas rozpoczęcia i wygaśnięcia, uprawnienia (odczyt/zapis) oraz zakresów adresów IP. W tym artykule opisano sposób użycia SAS w połączeniu z usługą Azure CDN. Aby uzyskać więcej informacji na temat sygnatury dostępu Współdzielonego, łącznie ze sposobem tworzenia go i parametru opcje, zobacz [używanie udostępnionych sygnatur dostępu (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).

## <a name="setting-up-azure-cdn-to-work-with-storage-sas"></a>Konfigurowanie usługi Azure CDN do pracy z magazynem SAS
Zaleca się trzech opcji korzystania z usługi Azure CDN przy użyciu sygnatury dostępu Współdzielonego. Wszystkie opcje założono, że utworzono już działającego SAS (patrz wymagania wstępne). 
 
### <a name="prerequisites"></a>Wymagania wstępne
Aby rozpocząć, Utwórz konto magazynu, a następnie Generowanie sygnatury dostępu Współdzielonego dla zawartości. Możesz wygenerować dwa rodzaje sygnatur dostępu przechowywanej: sygnatury dostępu Współdzielonego usługi lub kontem sygnatury dostępu Współdzielonego. Aby uzyskać więcej informacji, zobacz [rodzaje sygnatur dostępu współdzielonego](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#types-of-shared-access-signatures).

Po wygenerowaniu tokenu sygnatury dostępu Współdzielonego, uzyskać dostęp do pliku magazynu obiektów blob, dołączanie `?sv=<SAS token>` do adresu URL. Ten adres URL ma następujący format: 

`https://<account name>.blob.core.windows.net/<container>/<file>?sv=<SAS token>`
 
Na przykład:
 ```
https://democdnstorage1.blob.core.windows.net/container1/demo.jpg?sv=2017-04-17&ss=b&srt=co&sp=r&se=2038-01-02T21:30:49Z&st=2018-01-02T13:30:49Z&spr=https&sig=QehoetQFWUEd1lhU5iOMGrHBmE727xYAbKJl5ohSiWI%3D
```

Aby uzyskać więcej informacji na temat ustawienie parametrów, zobacz [zagadnienia dotyczące parametru SAS](#sas-parameter-considerations) i [parametrów podpisu dostępu współużytkowanego](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#shared-access-signature-parameters).

![Ustawienia sieci CDN SAS](./media/cdn-sas-storage-support/cdn-sas-settings.png)

### <a name="option-1-using-sas-with-pass-through-to-blob-storage-from-azure-cdn"></a>Opcja 1: Przy użyciu sygnatury dostępu Współdzielonego z przekazywanego do magazynu obiektów blob z usługi Azure CDN

Ta opcja jest najprostszą i korzysta z jednego tokenu sygnatury dostępu Współdzielonego, który jest przekazywany z usługi Azure CDN z serwerem źródłowym. Nie jest obsługiwany przez **Azure CDN from Verizon** i **Azure CDN from Akamai**. 
 
1. Wybierz punkt końcowy, wybierz pozycję **buforowanie reguły**, a następnie wybierz pozycję **Buforuj każdy unikatowy adres URL** z **buforowania ciągu kwerendy** listy.

    ![Reguły buforowania CDN](./media/cdn-sas-storage-support/cdn-caching-rules.png)

2. Po skonfigurowaniu SAS na koncie magazynu, należy użyć tokenu sygnatury dostępu Współdzielonego z CDN punktu końcowego i pochodzenia adresów URL serwera, dostępu do tego pliku. 
   
   Adres URL punktu końcowego usługi CDN wynikowy ma następujący format: `https://<endpoint hostname>.azureedge.net/<container>/<file>?sv=<SAS token>`

   Na przykład:   
   ```
   https://demoendpoint.azureedge.net/container1/demo.jpg/?sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   
3. Dostosowywanie czas trwania pamięci podręcznej, za pomocą reguł buforowania lub dodając `Cache-Control` nagłówki na serwerze źródłowym. Ponieważ usługa Azure CDN traktuje tokenu sygnatury dostępu Współdzielonego jako ciąg zapytania zwykły, jako najlepsze rozwiązanie należy skonfigurować czas buforowania, wygaśnięcia czasu wygaśnięcia SAS lub wcześniejsze. W przeciwnym razie jeśli plik jest buforowana przez dłuższy czas nie jest aktywne skojarzenia zabezpieczeń, to plik może być dostępny z serwera pochodzenia Azure CDN, po upływie czasu wygaśnięcia SAS. Jeśli ta sytuacja występuje, a chcesz uczynić plików w pamięci podręcznej jest niedostępny, należy wykonać operację przeczyszczania plik, aby usunąć go z pamięci podręcznej. Informacje o ustawianiu czas buforowania na Azure CDN, zobacz [kontroli usługi Azure CDN zachowanie buforowania z buforowaniem reguły](cdn-caching-rules.md).

### <a name="option-2-hidden-cdn-sas-token-using-a-rewrite-rule"></a>Opcja 2: Ukryta token CDN SAS przy użyciu reguły ponownego zapisywania
 
Ta opcja jest dostępna tylko w przypadku **Azure CDN Premium from Verizon** profilów. Po wybraniu tej opcji można zabezpieczyć magazynu obiektów blob na serwerze źródłowym. Możesz użyć tej opcji, jeśli nie ma potrzeby ograniczenia dostępu do określonego pliku, ale chcesz uniemożliwić użytkownikom dostęp do źródła magazynu bezpośrednio, aby skrócić czas odciążania usługi Azure CDN. Token sygnatury dostępu Współdzielonego, który jest nieznany dla użytkownika, jest wymagana dla wszystkich uzyskiwania dostępu do plików w określonym kontenerze serwera pochodzenia. Jednak ze względu na zasady ponowne zapisywanie adresów URL tokenu sygnatury dostępu Współdzielonego nie jest wymagana dla punktu końcowego CDN.
 
1. Użyj [aparatu reguł](cdn-rules-engine.md) do utworzenia reguły ponowne zapisywanie adresów URL. Nowe zasady potrwać około 90 minut propagacji.

   ![Przycisk Zarządzaj CDN](./media/cdn-sas-storage-support/cdn-manage-btn.png)

   ![Przycisk aparatu reguł CDN](./media/cdn-sas-storage-support/cdn-rules-engine-btn.png)

   Następująca reguła ponowne zapisywanie adresów URL przykładowych używa wzorzec wyrażenia regularnego z grupą przechwytywania i punktu końcowego o nazwie *storagedemo*:
   
   Źródło:   
   `(/test/.*)`
   
   Miejsce docelowe:   
   ```
   $1?sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```

   ![Ponowne zapisywanie adresów URL w sieci CDN reguły](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-2.png)

2. Po uaktywnieniu nową regułę każda osoba, która ma dostęp do plików w określonym kontenerze punkt końcowy CDN niezależnie od tego, czy są one w adresie URL przy użyciu tokenu sygnatury dostępu Współdzielonego. Oto format: `https://<endpoint hostname>.azureedge.net/<container>/<file>`
 
   Na przykład:   
   `https://demoendpoint.azureedge.net/container1/demo.jpg`
       

3. Dostosowywanie czas trwania pamięci podręcznej, za pomocą reguł buforowania lub dodając `Cache-Control` nagłówki na serwerze źródłowym. Ponieważ usługa Azure CDN traktuje tokenu sygnatury dostępu Współdzielonego jako ciąg zapytania zwykły, jako najlepsze rozwiązanie należy skonfigurować czas buforowania, wygaśnięcia czasu wygaśnięcia SAS lub wcześniejsze. W przeciwnym razie jeśli plik jest buforowana przez dłuższy czas nie jest aktywne skojarzenia zabezpieczeń, to plik może być dostępny z serwera pochodzenia Azure CDN, po upływie czasu wygaśnięcia SAS. Jeśli ta sytuacja występuje, a chcesz uczynić plików w pamięci podręcznej jest niedostępny, należy wykonać operację przeczyszczania plik, aby usunąć go z pamięci podręcznej. Informacje o ustawianiu czas buforowania na Azure CDN, zobacz [kontroli usługi Azure CDN zachowanie buforowania z buforowaniem reguły](cdn-caching-rules.md).

### <a name="option-3-using-cdn-security-token-authentication-with-a-rewrite-rule"></a>Opcja 3: Przy użyciu uwierzytelniania tokenu zabezpieczeń CDN przy użyciu reguły ponownego zapisywania

Aby używać uwierzytelniania tokenu zabezpieczeń usługi Azure CDN, musisz mieć **Azure CDN Premium from Verizon** profilu. Ta opcja jest bezpieczne i dostosowania. Dostęp klienta jest oparty na parametry zabezpieczeń, które można ustawić tokenu zabezpieczającego. Po utworzeniu i skonfigurować token zabezpieczający, będzie wymagana dla wszystkich adresów URL punktu końcowego CDN. Jednak ze względu na zasady ponowne zapisywanie adresów URL tokenu sygnatury dostępu Współdzielonego nie jest wymagana dla punktu końcowego CDN. Jeśli token sygnatury dostępu Współdzielonego później staje się nieprawidłowy, Azure CDN będzie nie będzie można ponownie sprawdź poprawność zawartości z serwera pochodzenia.

1. [Utwórz token zabezpieczeń usługi Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-token-auth#setting-up-token-authentication) i Aktywuj ją przy użyciu aparatu reguł dla punktu końcowego CDN oraz ścieżkę, gdzie użytkownicy mogą uzyskać dostępu do pliku.

   Adres URL punktu końcowego tokenu zabezpieczeń ma następujący format:   
   `https://<endpoint hostname>.azureedge.net/<container>/<file>?<security_token>`
 
   Na przykład:   
   ```
   https://demoendpoint.azureedge.net/container1/demo.jpg?a4fbc3710fd3449a7c99986bkquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
       
   Parametr opcje uwierzytelniania tokenu zabezpieczeń są inne niż opcje parametru tokenu sygnatury dostępu Współdzielonego. Jeśli chcesz użyć podczas tworzenia tokenu zabezpieczającego czas wygaśnięcia, powinien on ustawioną taką samą wartość jak czas wygaśnięcia tokenu sygnatury dostępu Współdzielonego. Daje to gwarancję, że czas wygaśnięcia jest atrybutem wartości prognozowanych. 
 
2. Użyj [aparatu reguł](cdn-rules-engine.md) Aby utworzyć regułę ponowne zapisywanie adresów URL, aby umożliwić dostęp do tokenu sygnatury dostępu Współdzielonego, do wszystkich obiektów blob w kontenerze. Nowe zasady potrwać około 90 minut propagacji.

   Następująca reguła ponowne zapisywanie adresów URL przykładowych używa wzorzec wyrażenia regularnego z grupą przechwytywania i punktu końcowego o nazwie *storagedemo*:
   
   Źródło:   
   `(/test/.*)`
   
   Miejsce docelowe:   
   ```
   $1&sv=2017-04-17&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```

   ![Ponowne zapisywanie adresów URL w sieci CDN reguły](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-3.png)

3. Odnów sygnatury dostępu Współdzielonego, upewnij się, aktualizowanie reguły ponowne zapisywanie adresów Url przy użyciu nowego tokenu sygnatury dostępu Współdzielonego. 

## <a name="sas-parameter-considerations"></a>Zagadnienia dotyczące parametru SAS

Ponieważ SAS parametry nie są widoczne dla usługi Azure CDN, Azure CDN nie można zmienić jego zachowania dotyczącego dostarczania na ich podstawie. Ograniczenia parametrów zdefiniowanych mają zastosowanie tylko w odpowiedzi na żądania, które usługi Azure CDN sprawia, że na serwerze źródłowym, nie dla żądania od klienta do usługi Azure CDN. Ta różnica jest istotnym elementem do rozważenia po ustawieniu parametrów sygnatury dostępu Współdzielonego. Jeśli te zaawansowane możliwości są wymagane i używasz [opcja 3](#option-3-using-cdn-security-token-authentication-with-a-rewrite-rule), ustaw odpowiednie ograniczenia w tokenie zabezpieczeń usługi Azure CDN.

| Nazwa parametru SAS | Opis |
| --- | --- |
| Uruchamianie | Czas, jaki można rozpocząć dostępu do pliku blob Azure CDN. Z powodu zegara pochylanie (nadejściu sygnał zegara w różnym czasie różnych składników), wybierz czas wcześniej 15 minut, jeśli chcesz zasobów, które mają być dostępne natychmiast. |
| End | Czas, po upływie którego Azure CDN nie można uzyskać dostępu do pliku obiektu blob. Wcześniej buforowanych plików na platformie Azure CDN są nadal dostępne. Aby kontrolować czas wygaśnięcia pliku, ustaw czas wygaśnięcia odpowiednie w tokenie zabezpieczeń usługi Azure CDN lub przeczyszczania elementu zawartości. |
| Dozwolonych adresów IP | Opcjonalny. Jeśli używasz **Azure CDN from Verizon**, ustaw ten parametr, do zakresów określonych w [Azure CDN from zakresów adresów IP serwera krawędzi Verizon](https://msdn.microsoft.com/library/mt757330.aspx). Jeśli używasz **Azure CDN from Akamai**, nie można ustawić parametru zakresy IP, ponieważ nie są statyczne adresy IP.|
| Dozwolone protokoły | Następujące protokoły dozwoloną dla żądania dotyczącego przy użyciu sygnatury dostępu Współdzielonego konta. Ustawienie HTTPS jest zalecane.|

## <a name="see-also"></a>Zobacz także
- [Przy użyciu sygnatury dostępu współdzielonego (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)
- [Udostępnione sygnatur dostępu, część 2: Tworzenie i sygnatury dostępu Współdzielonego za pomocą magazynu obiektów Blob](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2)
- [Zabezpieczanie zasobów Azure Content Delivery Network z tokenu uwierzytelniania](https://docs.microsoft.com/azure/cdn/cdn-token-auth)
