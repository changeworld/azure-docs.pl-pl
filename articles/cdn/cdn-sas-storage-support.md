---
title: Za pomocą sygnatury dostępu Współdzielonego przy użyciu usługi Azure CDN | Dokumentacja firmy Microsoft
description: Usługa Azure CDN obsługuje korzystanie z sygnatury dostępu współdzielonego (SAS) przyznawanie ograniczonego dostępu do prywatnego magazynu kontenerów.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2018
ms.author: magattus
ms.openlocfilehash: 7edf0a9f8d4eb4c01b6d80fd82a1061b6cbb1e35
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60324156"
---
# <a name="using-azure-cdn-with-sas"></a>Usługa Azure CDN przy użyciu sygnatury dostępu Współdzielonego

Po skonfigurowaniu konta magazynu dla usługi Azure zawartości Delivery Network (CDN) do użycia w celu buforowania zawartości domyślnie każdy, kto zna adresy URL dla kontenerów magazynu można uzyskać dostęp do plików, które zostało przesłane. Aby chronić pliki w ramach konta magazynu, możesz ustawić dostęp do kontenerów magazynów z publicznej na prywatną. Jednak jeśli tak zrobisz, nie będzie można uzyskać dostęp do plików. 

Jeśli chcesz przyznać ograniczony dostęp do prywatnych kontenerów magazynu, możesz użyć funkcji sygnatury dostępu współdzielonego konta usługi Azure Storage. Sygnatura dostępu współdzielonego to identyfikator URI, który zapewnia ograniczone prawa dostępu do zasobów usługi Azure Storage bez ujawniania klucza konta. Możesz podać sygnatury dostępu Współdzielonego dla klientów, którzy nie ufasz, za pomocą klucza konta magazynu, ale do którego chcesz delegować dostęp do niektórych zasobów konta magazynu. Dzięki rozłożeniu identyfikatora URI sygnatury dostępu współdzielonego dla tych klientów, należy przyznać im dostęp do zasobu przez określony czas.
 
Za pomocą sygnatury dostępu Współdzielonego można zdefiniować różnych parametrów do dostępu do obiektu blob, takie jak czas rozpoczęcia i wygaśnięcia, uprawnienia (odczyt/zapis) i zakresy adresów IP. W tym artykule opisano sposób używania sygnatur dostępu Współdzielonego w połączeniu z usługą Azure CDN. Aby uzyskać więcej informacji na temat sygnatury dostępu Współdzielonego, w tym sposobu tworzenia go i jego opcji parametrów, zobacz [Using shared access signatures (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).

## <a name="setting-up-azure-cdn-to-work-with-storage-sas"></a>Konfigurowanie wysokiej dostępności treści Azure, do pracy z magazynem SAS
Poniższe trzy opcje są zalecane w przypadku przy użyciu sygnatury dostępu Współdzielonego z usługą Azure CDN. Wszystkie opcje przyjęto założenie, że utworzono już działającego sygnatury dostępu Współdzielonego (patrz wymagania wstępne). 
 
### <a name="prerequisites"></a>Wymagania wstępne
Aby rozpocząć, Utwórz konto magazynu, a następnie wygenerować sygnaturę dostępu Współdzielonego dla swojego elementu zawartości. Możesz wygenerować dwa rodzaje sygnatur dostępu przechowywane: sygnatury dostępu Współdzielonego usługi lub sygnatury dostępu Współdzielonego konta. Aby uzyskać więcej informacji, zobacz [rodzaje sygnatur dostępu współdzielonego](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#types-of-shared-access-signatures).

Po wygenerowaniu tokenu sygnatury dostępu Współdzielonego można uzyskiwać dostęp z pliku magazynu obiektów blob, dodając `?sv=<SAS token>` do adresu URL. Ten adres URL ma następujący format: 

`https://<account name>.blob.core.windows.net/<container>/<file>?sv=<SAS token>`
 
Na przykład:
 ```
https://democdnstorage1.blob.core.windows.net/container1/demo.jpg?sv=2017-07-29&ss=b&srt=co&sp=r&se=2038-01-02T21:30:49Z&st=2018-01-02T13:30:49Z&spr=https&sig=QehoetQFWUEd1lhU5iOMGrHBmE727xYAbKJl5ohSiWI%3D
```

Aby uzyskać więcej informacji na temat parametrów ustawień, zobacz [zagadnienia dotyczące parametru sygnatury dostępu Współdzielonego](#sas-parameter-considerations) i [parametrów sygnatury dostępu współdzielonego](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#shared-access-signature-parameters).

![Ustawienia sygnatury dostępu Współdzielonego usługi CDN](./media/cdn-sas-storage-support/cdn-sas-settings.png)

### <a name="option-1-using-sas-with-pass-through-to-blob-storage-from-azure-cdn"></a>Opcja 1: Sygnatury dostępu Współdzielonego używania z przy użyciu przekazywanego do magazynu obiektów blob usługi Azure CDN

Ta opcja jest najprostszy i korzysta z jednego tokenu sygnatury dostępu Współdzielonego, który jest przekazywany przez usługę Azure CDN do serwera pochodzenia.
 
1. Wybierz punkt końcowy, wybierz pozycję **reguły buforowania**, a następnie wybierz **Buforuj każdy unikatowy adres URL** z **buforowanie ciągu zapytania** listy.

    ![Reguły buforowania sieci CDN](./media/cdn-sas-storage-support/cdn-caching-rules.png)

2. Po skonfigurowaniu sygnatury dostępu Współdzielonego dla konta magazynu, należy użyć tokenu sygnatury dostępu Współdzielonego z sieci CDN punktu końcowego i pochodzenia adresów URL serwera, dostępu do tego pliku. 
   
   Wynikowy adres URL punktu końcowego usługi CDN ma następujący format: `https://<endpoint hostname>.azureedge.net/<container>/<file>?sv=<SAS token>`

   Na przykład:   
   ```
   https://demoendpoint.azureedge.net/container1/demo.jpg/?sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   
3. Dostosuj czas trwania pamięci podręcznej przy użyciu reguł buforowania lub dodając `Cache-Control` nagłówków na serwerze źródłowym. Ponieważ usługa Azure CDN traktuje token sygnatury dostępu Współdzielonego jako ciąg zapytania zwykły, najlepszym rozwiązaniem należy skonfigurować czas trwania buforowania, która wygaśnie po w lub przed upływem terminu ważności sygnatury dostępu Współdzielonego. W przeciwnym razie jeśli plik jest buforowana przez dłuższy czas niż sygnatury dostępu Współdzielonego jest aktywna, to plik może być dostępny z serwera pochodzenia sieć CDN systemu Azure, po upłynięciu czasu wygaśnięcia sygnatury dostępu Współdzielonego. Jeśli taka sytuacja ma miejsce, a chcesz wprowadzić plików w pamięci podręcznej jest niedostępny, należy wykonać operację przeczyszczania, plik, aby wyczyścić go z pamięci podręcznej. Aby dowiedzieć się, jak ustawienie czas trwania pamięci podręcznej w usłudze Azure CDN, zobacz [kontroli Azure działanie buforowania usługi CDN przy użyciu reguł buforowania](cdn-caching-rules.md).

### <a name="option-2-hidden-cdn-sas-token-using-a-rewrite-rule"></a>Opcja 2: Ukryte tokenu sygnatury dostępu Współdzielonego usługi CDN przy użyciu reguły ponownego zapisywania
 
Ta opcja jest dostępna tylko w przypadku **Azure CDN Premium from Verizon** profilów. Po wybraniu tej opcji można zabezpieczyć magazynu obiektów blob na serwerze źródłowym. Można użyć tej opcji, jeśli nie ma potrzeby ograniczenia dostępu do określonego pliku, ale chcesz uniemożliwić użytkownikom uzyskiwanie dostępu do źródła magazynu bezpośrednio do skrócenia czasu odciążania sieć CDN systemu Azure. Token sygnatury dostępu Współdzielonego, który jest nieznany do użytkownika, jest wymagana dla każdego, kto uzyskiwania dostępu do plików w określonym kontenerze serwera pochodzenia. Jednak ze względu na reguły ponownego zapisywania adresów URL tokenu sygnatury dostępu Współdzielonego nie jest wymagany w punkcie końcowym usługi CDN.
 
1. Użyj [aparat reguł](cdn-rules-engine.md) można utworzyć regułę ponownego zapisywania adresów URL. Nowe reguły podjąć propagowane do 4 godzin.

   ![Przycisk zarządzania sieci CDN](./media/cdn-sas-storage-support/cdn-manage-btn.png)

   ![Przycisk aparatu reguł sieci CDN](./media/cdn-sas-storage-support/cdn-rules-engine-btn.png)

   Poniższe reguły ponownego zapisywania adresów URL przykładowe za pomocą wzorca wyrażenia regularnego grupa przechwytywania i punktu końcowego o nazwie *sasstoragedemo*:
   
   Źródło:   
   `(container1\/.*)`
   
   Miejsce docelowe:   
   ```
   $1?sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   ![Ponowne zapisywanie adresów URL usługi CDN reguły - lewej](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
   ![reguły ponownego zapisywania adresów URL usługi CDN - prawej](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-4.png)

2. Po nową regułę stanie się aktywny, każdy użytkownik ma dostęp do plików w określonym kontenerze punktu końcowego usługi CDN, niezależnie od tego, czy przy użyciu tokenu sygnatury dostępu Współdzielonego w adresie URL. Oto format: `https://<endpoint hostname>.azureedge.net/<container>/<file>`
 
   Na przykład:   
   `https://sasstoragedemo.azureedge.net/container1/demo.jpg`
       

3. Dostosuj czas trwania pamięci podręcznej przy użyciu reguł buforowania lub dodając `Cache-Control` nagłówków na serwerze źródłowym. Ponieważ usługa Azure CDN traktuje token sygnatury dostępu Współdzielonego jako ciąg zapytania zwykły, najlepszym rozwiązaniem należy skonfigurować czas trwania buforowania, która wygaśnie po w lub przed upływem terminu ważności sygnatury dostępu Współdzielonego. W przeciwnym razie jeśli plik jest buforowana przez dłuższy czas niż sygnatury dostępu Współdzielonego jest aktywna, to plik może być dostępny z serwera pochodzenia sieć CDN systemu Azure, po upłynięciu czasu wygaśnięcia sygnatury dostępu Współdzielonego. Jeśli taka sytuacja ma miejsce, a chcesz wprowadzić plików w pamięci podręcznej jest niedostępny, należy wykonać operację przeczyszczania, plik, aby wyczyścić go z pamięci podręcznej. Aby dowiedzieć się, jak ustawienie czas trwania pamięci podręcznej w usłudze Azure CDN, zobacz [kontroli Azure działanie buforowania usługi CDN przy użyciu reguł buforowania](cdn-caching-rules.md).

### <a name="option-3-using-cdn-security-token-authentication-with-a-rewrite-rule"></a>Opcja 3: Przy użyciu uwierzytelniania tokenu zabezpieczeń usługi CDN przy użyciu reguły ponownego zapisywania

Aby użyć uwierzytelniania tokenu zabezpieczeń usługi Azure CDN, musisz mieć **Azure CDN Premium from Verizon** profilu. Ta opcja jest bezpieczne i możliwe do dostosowania. Dostęp klienta jest oparty na parametry zabezpieczeń, które możesz ustawić dla tokenu zabezpieczającego. Po utworzeniu i skonfigurować token zabezpieczający, będzie wymagana na wszystkie adresy URL punktu końcowego usługi CDN. Jednak ze względu na reguły ponownego zapisywania adresów URL tokenu sygnatury dostępu Współdzielonego nie jest wymagany w punkcie końcowym usługi CDN. Jeśli token sygnatury dostępu Współdzielonego później staje się nieprawidłowy, usługi Azure CDN będzie już można przechowywać zawartość z serwera pochodzenia.

1. [Tworzenie tokenu zabezpieczeń usługi Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-token-auth#setting-up-token-authentication) i aktywować go przy użyciu aparatu reguł dla punktu końcowego usługi CDN i ścieżkę, gdzie użytkownicy mogą uzyskać dostępu do pliku.

   Adres URL punktu końcowego tokenu zabezpieczeń ma następujący format:   
   `https://<endpoint hostname>.azureedge.net/<container>/<file>?<security_token>`
 
   Na przykład:   
   ```
   https://sasstoragedemo.azureedge.net/container1/demo.jpg?a4fbc3710fd3449a7c99986bkquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
       
   Opcje parametrów dla uwierzytelniania przy użyciu tokenu zabezpieczeń różnią się od opcji parametrów dla tokenu sygnatury dostępu Współdzielonego. Jeśli zdecydujesz się użyć podczas tworzenia tokenu zabezpieczającego czas wygaśnięcia, jest on ustawiany na taką samą wartość jak czas wygaśnięcia tokenu sygnatury dostępu Współdzielonego. Daje to gwarancję, że czas wygaśnięcia jest przewidywalne. 
 
2. Użyj [aparat reguł](cdn-rules-engine.md) można utworzyć regułę ponownego zapisywania adresów URL, aby umożliwić dostęp do tokenu sygnatury dostępu Współdzielonego do wszystkich obiektów blob w kontenerze. Nowe reguły podjąć propagowane do 4 godzin.

   Poniższe reguły ponownego zapisywania adresów URL przykładowe za pomocą wzorca wyrażenia regularnego grupa przechwytywania i punktu końcowego o nazwie *sasstoragedemo*:
   
   Źródło:   
   `(container1\/.*)`
   
   Miejsce docelowe:   
   ```
   $1&sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   ![Ponowne zapisywanie adresów URL usługi CDN reguły - lewej](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
   ![reguły ponownego zapisywania adresów URL usługi CDN - prawej](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-4.png)

3. Jeśli odnowisz sygnatury dostępu Współdzielonego upewnij się, zaktualizować reguły ponownego zapisywania adresów Url przy użyciu nowego tokenu sygnatury dostępu Współdzielonego. 

## <a name="sas-parameter-considerations"></a>Zagadnienia dotyczące parametru sygnatury dostępu Współdzielonego

Ponieważ sygnatury dostępu Współdzielonego parametry nie są widoczne dla usługi Azure CDN, sieć CDN systemu Azure nie można zmienić jego zachowanie dostarczania na ich podstawie. Ograniczenia zdefiniowany parametr stosuje się tylko na żądania, które sprawia, że usługa Azure CDN do serwera pochodzenia dla żądania od klienta do usługi Azure CDN. Różnica w tym ważne jest, aby wziąć pod uwagę podczas ustawiania parametrów sygnatury dostępu Współdzielonego. Jeśli te zaawansowane możliwości są wymagane, a używasz [opcja 3](#option-3-using-cdn-security-token-authentication-with-a-rewrite-rule), ustaw odpowiednie ograniczenia w tokenie zabezpieczającym sieć CDN systemu Azure.

| Nazwa parametru sygnatury dostępu Współdzielonego | Opis |
| --- | --- |
| Uruchamianie | Czas rozpoczęcia przez sieć CDN systemu Azure na dostęp do pliku obiektu blob. Z powodu zegara pochylanie (po odebraniu sygnału zegara w różnym czasie dla różnych składników), wybierz czas 15 minut wcześniej, jeśli chcesz, aby zasobów, które mają być dostępne natychmiast. |
| Koniec | Czas, po upływie którego usługa Azure CDN nie jest już dostęp do pliku obiektu blob. Wcześniej buforowanych plików w usłudze Azure CDN, są nadal dostępne. Aby kontrolować czas wygaśnięcia pliku, ustaw czas wygaśnięcia odpowiednie w tokenie zabezpieczeń usługi Azure CDN lub przeczyszczania elementu zawartości. |
| Dozwolone adresy IP | Opcjonalny. Jeśli używasz **Azure CDN from Verizon**, ten parametr zostanie ustawiony na zakresy zdefiniowane w [Azure CDN from Verizon zakresy adresów IP serwerów brzegowych](/azure/cdn/cdn-pop-list-api). Jeśli używasz **Azure CDN from Akamai**, nie można ustawić parametru zakresów adresów IP, ponieważ nie są statyczne adresy IP.|
| Dozwolone protokoły | Rozróżniany, dozwolone dla żądań wykonywanych przy użyciu sygnatury dostępu Współdzielonego konta. Ustawienie HTTPS jest zalecane.|

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat sygnatury dostępu Współdzielonego zobacz następujące artykuły:
- [Używanie sygnatury dostępu współdzielonego (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)
- [Udostępnione sygnatur dostępu, część 2: Tworzenie i używanie sygnatury dostępu Współdzielonego z usługą Blob storage](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2)

Aby uzyskać więcej informacji na temat konfigurowania uwierzytelniania tokenu, zobacz [zasobów Zabezpieczanie usługi Azure Content Delivery Network, za pomocą tokenu uwierzytelniania](https://docs.microsoft.com/azure/cdn/cdn-token-auth).
