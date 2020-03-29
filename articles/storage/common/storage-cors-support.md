---
title: Obsługa udostępniania zasobów między źródłami (CORS) | Dokumenty firmy Microsoft
description: Dowiedz się, jak włączyć obsługę cors dla usług Microsoft Azure Storage Services.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 2/22/2017
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: bb296db0d97382deac984369704777de5d5cb362
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65147686"
---
# <a name="cross-origin-resource-sharing-cors-support-for-the-azure-storage-services"></a>Obsługa udostępniania zasobów między źródłami (CORS) dla usług usługi Azure Storage
Począwszy od wersji 2013-08-15 usługi magazynu platformy Azure obsługują udostępnianie zasobów między źródłami (CORS) dla usług obiektów blob, table, queue i file. CORS to funkcja HTTP, która umożliwia aplikacji sieci web działającej w jednej domenie dostęp do zasobów w innej domenie. Przeglądarki sieci Web implementują ograniczenie zabezpieczeń znane jako [zasady tego samego źródła,](https://www.w3.org/Security/wiki/Same_Origin_Policy) które uniemożliwia stronie sieci Web wywoływanie interfejsów API w innej domenie; Usługa CORS zapewnia bezpieczny sposób zezwalania jednej domenie (domenie pochodzenia) na wywoływanie interfejsów API w innej domenie. Szczegółowe informacje na temat CORS można znaleźć w [specyfikacji CORS.](https://www.w3.org/TR/cors/)

Reguły CORS można ustawić indywidualnie dla każdej z usług magazynu, wywołując [ustaw właściwości usługi obiektów blob,](https://msdn.microsoft.com/library/hh452235.aspx)ustaw właściwości usługi [kolejki](https://msdn.microsoft.com/library/hh452232.aspx)i Ustaw właściwości [usługi tabeli](https://msdn.microsoft.com/library/hh452240.aspx). Po ustawieniu reguł CORS dla usługi zostanie ocenione poprawnie autoryzowane żądanie złożone przeciwko usłudze z innej domeny w celu ustalenia, czy jest dozwolone zgodnie z określonymi regułami.

> [!NOTE]
> Należy zauważyć, że cors nie jest mechanizm uwierzytelniania. Każde żądanie złożone względem zasobu magazynu, gdy funkcja CORS jest włączona, musi mieć odpowiedni podpis uwierzytelniania lub musi być wykonane względem zasobu publicznego.
> 
> 

## <a name="understanding-cors-requests"></a>Opis żądań CORS
Żądanie CORS z domeny pochodzenia może składać się z dwóch oddzielnych żądań:

* Żądanie inspekcji wstępnej, które wysyła zapytanie do ograniczeń CORS nałożonych przez usługę. Żądanie inspekcji wstępnej jest wymagane, chyba że metoda żądania jest [prostą metodą](https://www.w3.org/TR/cors/), czyli GET, HEAD lub POST.
* Rzeczywiste żądanie, wykonane względem żądanego zasobu.

### <a name="preflight-request"></a>Żądanie inspekcji wstępnej
Żądanie inspekcji wstępnej wysyła zapytanie do ograniczeń CORS, które zostały ustanowione dla usługi magazynowania przez właściciela konta. Przeglądarka internetowa (lub inny agent użytkownika) wysyła żądanie OPTIONS, które zawiera nagłówki żądań, metodę i domenę pochodzenia. Usługa magazynu ocenia zamierzoną operację na podstawie wstępnie skonfigurowanego zestawu reguł CORS, które określają, które domeny pochodzenia, metody żądań i nagłówki żądań mogą być określone w rzeczywistym żądaniu względem zasobu magazynu.

Jeśli usługa CORS jest włączona dla usługi i istnieje reguła CORS, która pasuje do żądania inspekcji wstępnej, usługa odpowiada kodem stanu 200 (OK) i zawiera wymagane nagłówki kontroli dostępu w odpowiedzi.

Jeśli cors nie jest włączona dla usługi lub żadna reguła CORS nie pasuje do żądania inspekcji wstępnej, usługa odpowie kodem stanu 403 (Zabronione).

Jeśli żądanie OPTIONS nie zawiera wymaganych nagłówków CORS (nagłówki Origin i Access-Control-Request-Method), usługa odpowie kodem stanu 400 (Złe żądanie).

Należy zauważyć, że żądanie inspekcji wstępnej jest oceniane względem usługi (blob, queue i table), a nie względem żądanego zasobu. Właściciel konta musi włączyć cors jako część właściwości usługi konta, aby żądanie zakończyło się pomyślnie.

### <a name="actual-request"></a>Rzeczywiste żądanie
Po zaakceptowaniu żądania inspekcji wstępnej i powrocie odpowiedzi przeglądarka wyśle rzeczywiste żądanie do zasobu magazynu. Przeglądarka natychmiast odrzuci rzeczywiste żądanie, jeśli żądanie inspekcji wstępnej zostanie odrzucone.

Rzeczywiste żądanie jest traktowane jako normalne żądanie względem usługi magazynu. Obecność nagłówka Origin wskazuje, że żądanie jest żądaniem CORS, a usługa sprawdzi pasujące reguły CORS. Jeśli zostanie znaleziony dopasowania, nagłówki kontroli dostępu są dodawane do odpowiedzi i wysyłane z powrotem do klienta. Jeśli dopasowanie nie zostanie znalezione, nagłówki kontroli dostępu CORS nie są zwracane.

## <a name="enabling-cors-for-the-azure-storage-services"></a>Włączanie usługi CORS dla usług Azure Storage
Reguły CORS są ustawiane na poziomie usługi, więc należy włączyć lub wyłączyć cors dla każdej usługi (Blob, Queue i Table) oddzielnie. Domyślnie mechanizm CORS jest wyłączony dla każdej usługi. Aby włączyć cors, należy ustawić odpowiednie właściwości usługi przy użyciu wersji 2013-08-15 lub nowszej i dodać reguły CORS do właściwości usługi. Szczegółowe informacje na temat włączania lub wyłączania usługi CORS dla usługi oraz ustawiania reguł [Set Queue Service Properties](https://msdn.microsoft.com/library/hh452232.aspx)CORS można znaleźć [Set Table Service Properties](https://msdn.microsoft.com/library/hh452240.aspx) [w dz.](https://msdn.microsoft.com/library/hh452235.aspx)

Oto przykład pojedynczej reguły CORS, określonej za pomocą operacji Ustaw właściwości usługi:

```xml
<Cors>    
    <CorsRule>
        <AllowedOrigins>http://www.contoso.com, http://www.fabrikam.com</AllowedOrigins>
        <AllowedMethods>PUT,GET</AllowedMethods>
        <AllowedHeaders>x-ms-meta-data*,x-ms-meta-target*,x-ms-meta-abc</AllowedHeaders>
        <ExposedHeaders>x-ms-meta-*</ExposedHeaders>
        <MaxAgeInSeconds>200</MaxAgeInSeconds>
    </CorsRule>
<Cors>
```

Każdy element zawarty w regule CORS jest opisany poniżej:

* **AllowedOrigins:** domeny pochodzenia, które mogą składać żądanie względem usługi magazynu za pośrednictwem usługi CORS. Domena pochodzenia jest domeną, z której pochodzi żądanie. Należy zauważyć, że pochodzenie musi być dokładne dopasowanie rozróżnianej wielkości liter ze źródłem, który wiek użytkownika wysyła do usługi. Można również użyć symbolu wieloznacznego "*", aby umożliwić wszystkim domenom pochodzenia składanie żądań za pośrednictwem usługi CORS. W powyższym przykładzie domeny\/http: /www.contoso.com i\/http: /www.fabrikam.com mogą żądać względem usługi przy użyciu usługi CORS.
* **AllowedMethods**: Metody (zlecenia żądań HTTP), które domeny pochodzenia może używać dla żądania CORS. W powyższym przykładzie są dozwolone jedynie żądania PUT i GET.
* **AllowedHeaders**: Nagłówki żądań, które domena pochodzenia może określić w żądaniu CORS. W powyższym przykładzie dozwolone są wszystkie nagłówki metadanych, począwszy od x-ms-meta-data, x-ms-meta-target i x-ms-meta-abc. Należy zauważyć, że symbol wieloznaczny "*" wskazuje, że każdy nagłówek rozpoczynający się od określonego prefiksu jest dozwolony.
* **ExposedHeaders**: Nagłówki odpowiedzi, które mogą być wysyłane w odpowiedzi na żądanie CORS i udostępniane przez przeglądarkę do wystawcy żądania. W powyższym przykładzie przeglądarka jest instruowana, aby udostępnić dowolny nagłówek zaczynając od x-ms-meta.
* **MaxAgeInSeconds**: Maksymalny czas, przez który przeglądarka powinna buforować żądanie opcji inspekcji wstępnej.

Usługi magazynu platformy Azure obsługują określanie wstępnie zdefiniowanych nagłówków dla **elementów AllowedHeaders** i **ExposedHeaders.** Aby zezwolić na kategorię nagłówków, można określić wspólny prefiks tej kategorii. Na przykład określenie *x-ms-meta** jako nagłówka z prefiksem ustanawia regułę, która będzie zgodna ze wszystkimi nagłówkami, które zaczynają się od x-ms-meta.

Do reguł CORS obowiązują następujące ograniczenia:

* Można określić maksymalnie pięć reguł CORS na usługę magazynu (blob, tabela i kolejka).
* Maksymalny rozmiar wszystkich ustawień reguł CORS w żądaniu, z wyłączeniem znaczników XML, nie powinien przekraczać 2 KB.
* Długość dozwolonego nagłówka, narażonego nagłówka lub dozwolonego źródła pochodzenia nie powinna przekraczać 256 znaków.
* Dozwolone nagłówki i widoczne nagłówki mogą być:
  * Nagłówki dosłowne, w których podana jest dokładna nazwa nagłówka, takie jak **x-ms-meta-processed**. W żądaniu można określić maksymalnie 64 nagłówki literału.
  * Wstępnie uszekione nagłówki, w których znajduje się prefiks nagłówka, taki jak **x-ms-meta-data***. Określenie prefiksu w ten sposób umożliwia lub udostępnia dowolny nagłówek, który zaczyna się od danego prefiksu. W żądaniu można określić maksymalnie dwa wstępnie zdefiniowane nagłówki.
* Metody (lub zlecenia HTTP) określone w **AllowedMethods** element musi być zgodna z metod obsługiwanych przez interfejsy API usługi magazynu platformy Azure. Obsługiwane metody to DELETE, GET, HEAD, MERGE, POST, OPTIONS i PUT.

## <a name="understanding-cors-rule-evaluation-logic"></a>Opis logiki oceny reguł CORS
Gdy usługa magazynu odbiera dane z inspekcji wstępnej lub rzeczywiste żądanie, ocenia to żądanie na podstawie reguł CORS ustanowionych dla usługi za pośrednictwem odpowiedniej operacji Ustaw właściwości usługi. Reguły CORS są oceniane w kolejności, w jakiej zostały ustawione w treści żądania operacji Ustaw właściwości usługi.

Reguły CORS są oceniane w następujący sposób:

1. Po pierwsze domena pochodzenia żądania jest sprawdzana względem domen wymienionych dla **elementu AllowedOrigins.** Jeśli domena pochodzenia znajduje się na liście lub wszystkie domeny są dozwolone ze znakiem wieloznacznym "*", następnie postępujszych ocen reguł. Jeśli domena pochodzenia nie jest uwzględniona, żądanie kończy się niepowodzeniem.
2. Następnie metoda (lub zlecenie HTTP) żądania jest sprawdzana względem metod wymienionych w **AllowedMethods** elementu. Jeśli metoda jest uwzględniona w wykazie, następnie postępuj oceny reguł; jeśli nie, żądanie nie powiedzie się.
3. Jeśli żądanie pasuje do reguły w domenie pochodzenia i jej metody, ta reguła jest wybierana do przetwarzania żądania i nie są oceniane żadne dalsze reguły. Zanim żądanie zakończy się pomyślnie, jednak wszystkie nagłówki określone w żądaniu są sprawdzane względem nagłówków wymienionych w **AllowedHeaders** elementu. Jeśli wysłane nagłówki nie są zgodne z dozwolonymi nagłówkami, żądanie kończy się niepowodzeniem.

Ponieważ reguły są przetwarzane w kolejności, w jakiej są obecne w treści żądania, najlepsze rozwiązania zalecają określenie najbardziej restrykcyjnych reguł w odniesieniu do pochodzenia jako pierwsze na liście, tak aby były one oceniane jako pierwsze. Określ reguły, które są mniej restrykcyjne — na przykład reguła zezwalana na wszystkie źródła — na końcu listy.

### <a name="example--cors-rules-evaluation"></a>Przykład – ocena zasad CORS
W poniższym przykładzie pokazano częściową treść żądania dla operacji, aby ustawić reguły CORS dla usług magazynu. Zobacz [Ustawianie właściwości usługi obiektów blob,](https://msdn.microsoft.com/library/hh452235.aspx) [Ustawianie właściwości usługi kolejki](https://msdn.microsoft.com/library/hh452232.aspx)i [Ustawianie właściwości usługi tabel, aby](https://msdn.microsoft.com/library/hh452240.aspx) uzyskać szczegółowe informacje na temat konstruowania żądania.

```xml
<Cors>
    <CorsRule>
        <AllowedOrigins>http://www.contoso.com</AllowedOrigins>
        <AllowedMethods>PUT,HEAD</AllowedMethods>
        <MaxAgeInSeconds>5</MaxAgeInSeconds>
        <ExposedHeaders>x-ms-*</ExposedHeaders>
        <AllowedHeaders>x-ms-blob-content-type, x-ms-blob-content-disposition</AllowedHeaders>
    </CorsRule>
    <CorsRule>
        <AllowedOrigins>*</AllowedOrigins>
        <AllowedMethods>PUT,GET</AllowedMethods>
        <MaxAgeInSeconds>5</MaxAgeInSeconds>
        <ExposedHeaders>x-ms-*</ExposedHeaders>
        <AllowedHeaders>x-ms-blob-content-type, x-ms-blob-content-disposition</AllowedHeaders>
    </CorsRule>
    <CorsRule>
        <AllowedOrigins>http://www.contoso.com</AllowedOrigins>
        <AllowedMethods>GET</AllowedMethods>
        <MaxAgeInSeconds>5</MaxAgeInSeconds>
        <ExposedHeaders>x-ms-*</ExposedHeaders>
        <AllowedHeaders>x-ms-client-request-id</AllowedHeaders>
    </CorsRule>
</Cors>
```

Następnie należy wziąć pod uwagę następujące żądania CORS:

| Żądanie |  |  | Odpowiedź |  |
| --- | --- | --- | --- | --- |
| **Metoda** |**Origin** |**Nagłówki żądań** |**Dopasowanie reguły** |**Wynik** |
| **Umieścić** |http:\//www.contoso.com |x-ms-blob-content-type |Pierwsza reguła |Powodzenie |
| **Pobierz** |http:\//www.contoso.com |x-ms-blob-content-type |Druga zasada |Powodzenie |
| **Pobierz** |http:\//www.contoso.com |x-ms-client-request-id |Druga zasada |Niepowodzenie |

Pierwsze żądanie pasuje do pierwszej reguły — domena pochodzenia pasuje do dozwolonych początków, metoda jest zgodna z dozwolonymi metodami, a nagłówek jest zgodny z dozwolonymi nagłówkami — i tak się powiedzie.

Drugie żądanie nie jest zgodne z pierwszą regułą, ponieważ metoda nie jest zgodna z dozwolonymi metodami. Pasuje jednak do drugiej reguły, więc się udaje.

Trzecie żądanie jest zgodne z drugą regułą w domenie i metodzie pochodzenia, więc nie są oceniane żadne dalsze reguły. Jednak *nagłówek x-ms-client-request-id* nie jest dozwolony przez drugą regułę, więc żądanie kończy się niepowodzeniem, pomimo faktu, że semantyka trzeciej reguły pozwoliłaby mu się pomyślnie.

> [!NOTE]
> Chociaż w tym przykładzie pokazano mniej restrykcyjną regułę przed bardziej restrykcyjną, ogólnie rzecz biorąc, najlepszym rozwiązaniem jest najpierw wymienić najbardziej restrykcyjne reguły.
> 
> 

## <a name="understanding-how-the-vary-header-is-set"></a>Opis ustawiania nagłówka Vary
Nagłówek *Vary* to standardowy nagłówek HTTP/1.1 składający się z zestawu pól nagłówka żądania, które informują przeglądarkę lub agenta użytkownika o kryteriach wybranych przez serwer do przetworzenia żądania. *Nagłówek Vary* jest używany głównie do buforowania przez serwery proxy, przeglądarki i sieci CDN, które używają go do określenia sposobu buforowania odpowiedzi. Szczegółowe informacje można znaleźć w specyfikacji [nagłówka Vary](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).

Gdy przeglądarka lub inny agent użytkownika buforuje odpowiedź z żądania CORS, domena pochodzenia jest buforowana jako dozwolone źródło. Gdy druga domena wystawia to samo żądanie dla zasobu magazynu, gdy pamięć podręczna jest aktywna, agent użytkownika pobiera buforowaną domenę pochodzenia. Druga domena nie pasuje do buforowanej domeny, więc żądanie kończy się niepowodzeniem, gdy w przeciwnym razie zakończy się pomyślnie. W niektórych przypadkach usługa Azure Storage ustawia vary nagłówka do **Pochodzenia,** aby poinstruować agenta użytkownika, aby wysłać kolejne żądanie CORS do usługi, gdy żądana domena różni się od źródła buforowanego.

Usługa Azure Storage ustawia nagłówek *Vary* na **Origin** dla rzeczywistych żądań GET/HEAD w następujących przypadkach:

* Gdy początek żądania dokładnie odpowiada dozwolonemu pochodzeniu zdefiniowanemu przez regułę CORS. Aby być dokładnym dopasowaniem, reguła CORS może nie zawierać symbolu wieloznacznego " * ' znak.
* Nie ma reguły pasującej do początku żądania, ale usługa CORS jest włączona dla usługi magazynu.

W przypadku, gdy żądanie GET/HEAD pasuje do reguły CORS, która zezwala na wszystkie źródła, odpowiedź wskazuje, że wszystkie źródła są dozwolone, a pamięć podręczna agenta użytkownika zezwala na kolejne żądania z dowolnej domeny pochodzenia, gdy pamięć podręczna jest aktywna.

Należy zauważyć, że w przypadku żądań przy użyciu metod innych niż GET/HEAD usługi magazynu nie ustawią nagłówka Vary, ponieważ odpowiedzi na te metody nie są buforowane przez agentów użytkowników.

Poniższa tabela wskazuje, jak usługa Azure Storage będzie odpowiadać na żądania GET/HEAD na podstawie wcześniej wymienionych przypadków:

| Żądanie | Ustawienie konta i wynik oceny reguły |  |  | Odpowiedź |  |  |
| --- | --- | --- | --- | --- | --- | --- |
| **Nagłówek pochodzenia obecny na żądanie** |**Reguły(-y) CORS określone dla tej usługi** |**Istnieje reguła dopasowywania, która umożliwia wszystkie źródła(*)** |**Istnieje reguła dopasowywania dla dokładnego dopasowania pochodzenia** |**Odpowiedź zawiera nagłówek Vary ustawiony na Początek** |**Odpowiedź obejmuje access-control-allowed-Origin: "*"** |**Odpowiedź zawiera nagłówki z kontrolą dostępu** |
| Nie |Nie |Nie |Nie |Nie |Nie |Nie |
| Nie |Tak |Nie |Nie |Tak |Nie |Nie |
| Nie |Tak |Tak |Nie |Nie |Tak |Tak |
| Tak |Nie |Nie |Nie |Nie |Nie |Nie |
| Tak |Tak |Nie |Tak |Tak |Nie |Tak |
| Tak |Tak |Nie |Nie |Tak |Nie |Nie |
| Tak |Tak |Tak |Nie |Nie |Tak |Tak |

## <a name="billing-for-cors-requests"></a>Rozliczenia za żądania CORS
Pomyślne żądania inspekcji wstępnej są naliczane, jeśli włączono usługę CORS dla dowolnej z usług magazynowania dla swojego konta (wywołując [ustaw właściwości usługi obiektów blob,](https://msdn.microsoft.com/library/hh452235.aspx)Ustaw właściwości usługi [kolejki](https://msdn.microsoft.com/library/hh452232.aspx)lub Ustaw właściwości [usługi tabeli).](https://msdn.microsoft.com/library/hh452240.aspx) Aby zminimalizować opłaty, należy rozważyć ustawienie **MaxAgeInSeconds** element w regułach CORS do dużej wartości, tak aby agent użytkownika buforuje żądanie.

Nieudane żądania inspekcji wstępnej nie będą naliczane.

## <a name="next-steps"></a>Następne kroki
[Ustawianie właściwości usługi obiektów blob](https://msdn.microsoft.com/library/hh452235.aspx)

[Ustawianie właściwości usługi kolejki](https://msdn.microsoft.com/library/hh452232.aspx)

[Ustawianie właściwości usługi tabel](https://msdn.microsoft.com/library/hh452240.aspx)

[Specyfikacja udostępniania zasobów W3C Cross-Origin](https://www.w3.org/TR/cors/)

