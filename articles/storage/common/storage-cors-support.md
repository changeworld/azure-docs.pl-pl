---
title: Cross-Origin Resource Sharing (CORS) w pomocy technicznej | Dokumentacja firmy Microsoft
description: Dowiedz się, jak włączyć obsługę mechanizmu CORS dla usług Microsoft Azure Storage.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65147686"
---
# <a name="cross-origin-resource-sharing-cors-support-for-the-azure-storage-services"></a>Cross-Origin Resource Sharing (CORS) obsługę usług Azure Storage
Począwszy od wersji 2013-08-15, usług Azure storage obsługuje udostępniania zasobów między źródłami (CORS) dla usług obiektów Blob, tabela, kolejka i pliku. CORS to funkcja protokołu HTTP, który umożliwia aplikacji sieci web uruchomionej w jednej domenie dostęp do zasobów w innej domenie. Przeglądarki sieci Web wdrażają ograniczenie bezpieczeństwa nazywane [zasadami tego samego źródła](https://www.w3.org/Security/wiki/Same_Origin_Policy) która zapobiega wywoływaniu interfejsów API w innej domenie; strony sieci web Mechanizm CORS zapewnia bezpieczną metodę umożliwiania jednej domenie (domenie źródłowej) wywoływania interfejsów API z innej domeny. Zobacz [specyfikacji CORS](https://www.w3.org/TR/cors/) szczegółowe informacje na mechanizmu CORS.

Można ustawić reguły CORS indywidualnie dla każdego z usługi magazynu, wywołując [ustawiania właściwości usługi obiektów Blob](https://msdn.microsoft.com/library/hh452235.aspx), [ustawiania właściwości usługi kolejki](https://msdn.microsoft.com/library/hh452232.aspx), i [ustawiania właściwości usługi tabeli](https://msdn.microsoft.com/library/hh452240.aspx). Po ustawieniu reguł CORS dla usługi, a następnie prawidłowo autoryzowanych żądania skierowanego do usługi z innej domeny zostanie ocenione w celu określenia, czy jest dozwolone zgodnie z regułami, które określono.

> [!NOTE]
> Należy pamiętać, że mechanizm CORS nie jest mechanizm uwierzytelniania. Wszelkie żądania skierowanego do zasobu magazynu po włączeniu CORS musi mieć podpis właściwe uwierzytelnienie lub musi zostać wykonane w stosunku do zasobu publicznego.
> 
> 

## <a name="understanding-cors-requests"></a>Opis żądania CORS
Żądanie CORS z domeny pochodzenia może składać się z dwóch osobnych żądań:

* Żądania wstępnego, który sprawdza CORS ograniczenia nałożone przez usługę. Żądania wstępnego jest wymagany, chyba że jest metoda żądania [prosta metoda](https://www.w3.org/TR/cors/), co oznacza, GET, HEAD lub POST.
* Rzeczywistego żądania, skierowanego do żądanego zasobu.

### <a name="preflight-request"></a>Żądania wstępnego
Zapytania żądania wstępnego CORS ograniczenia, które zostały utworzone przez właściciela konta usługi storage. Przeglądarka sieci web (lub agenta użytkownika) wysyła żądanie opcje, które obejmują nagłówki żądania, metody i pochodzenia domeny. Usługa storage oblicza zamierzonej operacji na podstawie zestawu wstępnie skonfigurowanych reguł CORS, określających, które domeny pochodzenia, metody żądania i nagłówków żądań, które mogą być określone dla rzeczywistego żądania względem zasobu magazynu.

Jeśli ma regułę CORS, który odpowiada na żądania wstępnego CORS jest włączone dla usługi, usługa odpowiada kodem stanu 200 (OK) i obejmuje wymagana nagłówki kontroli dostępu w odpowiedzi.

Jeśli nie włączono mechanizm CORS dla usługi lub nie ma mechanizmu CORS pasującej reguły żądania wstępnego, usługa będzie odpowiadać kod stanu 403 (zabronione).

Jeśli żądanie opcje nie zawiera wymaganego nagłówków CORS (nagłówki pochodzenia i Access-Control-Request-Method), usługa będzie odpowiadać kod stanu 400 (złe żądanie).

Należy zauważyć, że żądania wstępnego jest oceniane względem usługi (Blob, Queue oraz Table), a nie dla żądanego zasobu. Właściciel konta muszą mieć włączone mechanizmu CORS w ramach właściwości usługi konta, aby żądanie zakończyło się sukcesem.

### <a name="actual-request"></a>Rzeczywistego żądania
Po zaakceptowaniu żądania wstępnego i odpowiedź zostanie zwrócona, przeglądarka wyśle rzeczywistego żądania względem zasobu magazynu. Przeglądarka będzie odmawiał rzeczywiste żądanie natychmiast, jeśli wstępnego żądanie zostanie odrzucone.

Rzeczywistego żądania jest traktowany jak normalny żądanie względem usługi magazynu. Obecność nagłówka Origin wskazuje, że żądanie jest żądaniem mechanizmu CORS i usługa sprawdzi pasujących reguł CORS. Jeśli zostanie znalezione dopasowanie, nagłówki kontroli dostępu są dodawane do odpowiedzi i wysyłane z powrotem do klienta. Jeśli nie zostanie znalezione dopasowanie, nagłówki CORS kontroli dostępu nie są zwracane.

## <a name="enabling-cors-for-the-azure-storage-services"></a>Włączanie mechanizmu CORS dla usługi Azure Storage
Reguły CORS są ustawiane na poziomie usługi, dlatego musisz włączyć lub wyłączyć CORS dla każdej usługi (Blob, Queue oraz Table) oddzielnie. Domyślnie CORS jest wyłączona dla każdej usługi. Aby włączyć mechanizm CORS, należy ustawić właściwości odpowiednią usługę, za pomocą wersji 2013-08-15 lub nowszym i Dodaj reguły CORS do właściwości usługi. Aby uzyskać szczegółowe informacje o tym, jak włączyć lub wyłączyć CORS dla usługi oraz jak ustawić reguły CORS można znaleźć [ustawiania właściwości usługi obiektów Blob](https://msdn.microsoft.com/library/hh452235.aspx), [ustawiania właściwości usługi kolejki](https://msdn.microsoft.com/library/hh452232.aspx), i [zestawu usługi Table Service Właściwości](https://msdn.microsoft.com/library/hh452240.aspx).

Oto przykład jednej reguły CORS określona za pomocą operacji ustawiania właściwości usługi:

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

Poniżej opisano każdy element do reguły CORS:

* **AllowedOrigins**: Domeny pochodzenia, które są dozwolone, aby utworzyć żądanie względem usługi storage przy użyciu mechanizmu CORS. Domena pochodzenia jest domeny, z której pochodzi żądanie. Należy pamiętać, że punkt początkowy musi być uwzględniana wielkość liter dokładnych źródło, które wieku użytkownika wysyła do usługi. Można również użyć znaku wieloznacznego "*" Aby zezwolić na wszystkie domeny pochodzenia żądań za pośrednictwem mechanizmu CORS. W przykładzie powyżej protokołu http domeny:\//www.contoso.com i http: \/ /www.fabrikam.com mogą wysyłać żądania usługi przy użyciu mechanizmu CORS.
* **AllowedMethods**: Metody (polecenia żądań protokołu HTTP), które domena pochodzenia może używać do żądania CORS. W powyższym przykładzie są dozwolone tylko żądania PUT i GET.
* **AllowedHeaders**: Nagłówki żądania, które określić domenę pochodzenia dla żądania CORS. W powyższym przykładzie są dozwolone wszystkie nagłówki metadanych, począwszy od x-ms-meta-data, x-ms-meta-target i x-ms-meta-abc. Należy pamiętać, że symbol wieloznaczny "*" wskazuje, czy jest dozwolone dowolnego nagłówka rozpoczynający się od określonego prefiksu.
* **ExposedHeaders**: Nagłówki odpowiedzi, które mogą być wysyłany w odpowiedzi na żądanie CORS i ujawniane wydawcy żądania. W powyższym przykładzie przeglądarki jest zobowiązany do udostępnienia dowolnego nagłówka rozpoczynający się od x-ms-meta.
* **MaxAgeInSeconds**: Maksymalny czas że przeglądarki powinien buforować żądania wstępnego OPTIONS.

Do usług Azure storage obsługuje określanie nagłówki z prefiksami dla obu **AllowedHeaders** i **ExposedHeaders** elementów. Aby zezwolić na kategorię nagłówków, można określić Wspólny prefiks do tej kategorii. Na przykład określenie *x-ms-meta** zgodnie z prefiksem nagłówka ustanawia regułę, która będzie odpowiadała wszystkie nagłówki, które zaczynają się od x-ms-meta.

Poniższe ograniczenia mają zastosowanie do reguł CORS:

* Można określić maksymalnie pięć reguł CORS dla usługi storage (Blob, tabel i kolejek).
* Maksymalny rozmiar wszystkich ustawień reguł CORS dla żądania, z wyłączeniem tagi XML nie może przekraczać 2 KB.
* Długość dozwoloną nagłówek, nagłówek narażonych lub dozwolone pochodzenie nie może przekraczać 256 znaków.
* Dozwolone nagłówki i uwidocznione nagłówki mogą być:
  * Nagłówki literałów, gdzie nazwa nagłówka dokładnie zostanie podany, takich jak **x-ms-meta przetwarzane**. W żądaniu można określić maksymalnie 64 nagłówki literałów.
  * Prefiks nagłówków, w którym prefiks nagłówka zostanie podany, takich jak ** x-ms-meta-danych ***. Określenie prefiksu w ten sposób pozwala lub uwidacznia wszystkie nagłówek, który rozpoczyna się od danego prefiksu. W żądaniu można określić maksymalnie dwa nagłówki z prefiksami.
* Metody (lub zlecenia HTTP), określony w **AllowedMethods** element musi być zgodna z metodami obsługiwane przez interfejsy API usług Azure storage. Obsługiwane metody to DELETE, GET, HEAD, MERGE, POST, opcji i PUT.

## <a name="understanding-cors-rule-evaluation-logic"></a>Opis logiki oceny reguły CORS
Gdy Usługa magazynu odbiera żądania wstępnego lub rzeczywistego, ocenia tego żądania na podstawie reguł CORS, które zostało ustanowione dla usługi za pomocą odpowiednich operacji ustawiania właściwości usługi. Reguły CORS są obliczane w kolejności, w którym zostały ustawione w treści żądania operacji ustawiania właściwości usługi.

Reguły CORS są oceniane w następujący sposób:

1. Po pierwsze, domena pochodzenia żądania jest sprawdzana względem domeny, dla **AllowedOrigins** elementu. Jeśli domena pochodzenia jest uwzględniony na liście lub wszystkie domeny są dozwolone w przypadku symbol wieloznaczny "*", następnie reguły oceny kontynuowane. Jeśli domena pochodzenia nie jest uwzględniona, żądanie kończy się niepowodzeniem.
2. Następnie metoda (lub zlecenie HTTP) żądania jest porównywany z metod opisanych w **AllowedMethods** elementu. Jeśli metoda jest uwzględniony na liście, będzie kontynuowane oceny reguł; Jeśli nie, żądanie kończy się niepowodzeniem.
3. Jeśli żądanie jest zgodny z regułą, w jego domena pochodzenia i jej metodę, tej reguły jest wybrany do przetwarzania żądań i nie dodatkowe reguły są przetwarzane. Zanim będzie możliwe żądanie, jednak wszelkie nagłówki określony w żądaniu są porównywane z nagłówków na liście **AllowedHeaders** elementu. Nagłówki wysyłane są zgodne dozwolone nagłówki, żądanie kończy się niepowodzeniem.

Ponieważ reguły są przetwarzane w kolejności, w jakiej występują w treści żądania, najlepszych rozwiązaniach zaleca się określić najbardziej restrykcyjne zasady w odniesieniu do źródła najpierw na liście, dlatego, że są one obliczane jako pierwsze. Określ reguły, które są mniej restrykcyjny — na przykład Reguła zezwalająca na wszystkie pochodzenia — na końcu listy.

### <a name="example--cors-rules-evaluation"></a>Przykład — CORS reguły oceny
Treść żądania częściowe operacji do konfigurowania reguł CORS w przypadku usług storage można znaleźć w poniższym przykładzie. Zobacz [ustawiania właściwości usługi obiektów Blob](https://msdn.microsoft.com/library/hh452235.aspx), [ustawiania właściwości usługi kolejki](https://msdn.microsoft.com/library/hh452232.aspx), i [ustawiania właściwości usługi tabeli](https://msdn.microsoft.com/library/hh452240.aspx) szczegółowe informacje na temat tworzenia żądania.

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
| **— Metoda** |**Origin** |**Nagłówki żądań** |**Rule Match** |**wynik** |
| **PUT** |http:\//www.contoso.com |x-ms-blob-content-type |Pierwsza reguła |Powodzenie |
| **GET** |http:\//www.contoso.com |x-ms-blob-content-type |Druga reguła |Powodzenie |
| **GET** |http:\//www.contoso.com |x-ms-client-request-id |Druga reguła |Niepowodzenie |

Pierwsze żądanie pasuje pierwszym reguły — domena pochodzenia jest zgodna dozwolonych źródeł, metoda dopasowuje dozwolone metody i nagłówek odpowiada dozwolone nagłówki — i tak zakończy się pomyślnie.

Drugie żądanie nie pasuje pierwszej reguły, ponieważ metoda jest niezgodna z dozwolone metody. Jednak odpowiadać drugą regułę, dzięki czemu jego powodzenia.

Trzecie żądanie dopasowuje drugiej reguły w jego domena pochodzenia i metody, dzięki czemu żadne dalsze reguły są oceniane. Jednak *nagłówek x-ms klient request-id* nie jest dozwolona przez drugą regułę, więc żądanie zakończy się niepowodzeniem, mimo iż semantyka trzeci reguły by pozwoliła go zakończyło się sukcesem.

> [!NOTE]
> Chociaż ten przykład przedstawia mniej restrykcyjne zasady przed bardziej restrykcyjne jeden, ogólnie rzecz biorąc najlepszym rozwiązaniem jest pierwsza lista najbardziej restrykcyjne zasady.
> 
> 

## <a name="understanding-how-the-vary-header-is-set"></a>Informacje o konfiguracji nagłówka zależne
*Zależne* nagłówek jest standardowy nagłówek HTTP/1.1, zawierający zestaw pól nagłówka żądania, które powiadomienia agenta przeglądarki lub użytkowników o kryteria, które zostały wybrane przez serwer przetwarzania żądania. *Zależne* nagłówek jest używana głównie dla pamięci podręcznej przez serwery proxy, przeglądarki i usługi CDN, które go użyć do określenia, jak powinny być buforowane odpowiedzi. Aby uzyskać szczegółowe informacje, zobacz specyfikację [nagłówka Vary](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).

Gdy przeglądarki lub innego agenta użytkownika będzie buforować odpowiedź z żądania CORS, domena pochodzenia jest buforowany jako dozwolone pochodzenie. Gdy drugą domenę wysyła tego samego żądania dla zasobów magazynu, gdy pamięć podręczna jest aktywna, agent użytkownika pobiera domena pochodzenia pamięci podręcznej. Drugą domenę pasuje do pamięci podręcznej domeny, więc żądanie zakończy się niepowodzeniem, gdy w przeciwnym razie powiedzie. W niektórych przypadkach usługi Azure Storage ustawia nagłówek zależne **pochodzenia** nakazać agenta użytkownika, aby wysłać kolejne żądanie CORS do usługi, gdy żądanie domeny różni się od źródła pamięci podręcznej.

Usługa Azure Storage zestawy *zależne* nagłówka do **pochodzenia** dla rzeczywistego żądania GET/HEAD w następujących przypadkach:

* Gdy żądanie danych pierwotnych dokładnie odpowiada dozwolone pochodzenie zdefiniowana przez regułę CORS. Być dokładne dopasowanie, regułę CORS nie może zawierać symbolu wieloznacznego "*" znaków.
* Nie ma reguły dopasowania żądanie danych pierwotnych, ale włączono mechanizm CORS dla usługi storage.

W przypadku, gdy żądanie GET/HEAD spełnia regułę CORS, która zezwala na wszystkie pochodzenia odpowiedź wskazuje, że wszystkie źródła są dozwolone i pamięci podręcznej agenta użytkownika umożliwi kolejne żądania z domeny pochodzenia, gdy pamięć podręczna jest aktywny.

Należy pamiętać, że w przypadku żądań za pomocą metod innych niż GET/HEAD, usługi magazynu nie ustawia nagłówek zależne od czasu odpowiedzi na te metody nie są buforowane przez agentów użytkownika.

Poniższa tabela wskazuje, jak usługa Azure storage będą odpowiadać na żądania GET/HEAD oparte na przypadkach opisanych powyżej:

| Żądanie | Ustawienia konta i wyniki oceny reguły |  |  | Odpowiedź |  |  |
| --- | --- | --- | --- | --- | --- | --- |
| **Nagłówek źródła na żądanie** |**Reguły CORS określone dla tej usługi** |**Istnieje zgodną regułę, która zezwala na wszystkie origins(*)** |**Istnieje reguła pasujące źródło dokładne dopasowanie** |**Odpowiedź zawiera nagłówek zależne, ustaw punkt początkowy** |**Odpowiedź zawiera Access-Control-dozwolone-Origin: "*"** |**Odpowiedź zawiera Access-Control-udostępniane-Headers** |
| Nie |Nie |Nie |Nie |Nie |Nie |Nie |
| Nie |Yes |Nie |Nie |Yes |Nie |Nie |
| Nie |Yes |Yes |Nie |Nie |Yes |Yes |
| Yes |Nie |Nie |Nie |Nie |Nie |Nie |
| Yes |Yes |Nie |Yes |Yes |Nie |Yes |
| Yes |Yes |Nie |Nie |Yes |Nie |Nie |
| Yes |Yes |Yes |Nie |Nie |Yes |Tak |

## <a name="billing-for-cors-requests"></a>Okres rozliczeniowy żądania CORS
Inspekcja pomyślnych żądań, są naliczane, jeśli włączono mechanizm CORS dla dowolnej usługi magazynu dla konta usługi (przez wywołanie metody [ustawiania właściwości usługi obiektów Blob](https://msdn.microsoft.com/library/hh452235.aspx), [ustawiania właściwości usługi kolejki](https://msdn.microsoft.com/library/hh452232.aspx), lub [Ustawiać właściwości usługi tabeli](https://msdn.microsoft.com/library/hh452240.aspx)). Aby zminimalizować opłaty, rozważ ustawienie **atrybut MaxAgeInSeconds** element z własnym mechanizmem CORS reguł na dużą wartość tak, aby agent użytkownika buforuje żądania.

Niepomyślne żądania wstępnego nie będą naliczane.

## <a name="next-steps"></a>Kolejne kroki
[Ustaw właściwości usługi obiektów Blob](https://msdn.microsoft.com/library/hh452235.aspx)

[Ustaw właściwości usługi kolejki](https://msdn.microsoft.com/library/hh452232.aspx)

[Ustawianie właściwości usługi tabeli](https://msdn.microsoft.com/library/hh452240.aspx)

[W3C Cross-Origin Resource Sharing specyfikacji](https://www.w3.org/TR/cors/)

