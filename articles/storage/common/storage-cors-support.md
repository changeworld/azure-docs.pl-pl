---
title: Współużytkowanie zasobów między źródłami (CORS) do udostępniania Obsługa | Dokumentacja firmy Microsoft
description: Dowiedz się, jak włączyć obsługę mechanizmu CORS dla usług magazynu Microsoft Azure.
services: storage
documentationcenter: .net
author: cbrooksmsft
manager: carmonm
editor: tysonn
ms.assetid: a0229595-5b64-4898-b8d6-fa2625ea6887
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 2/22/2017
ms.author: cbrooks
ms.openlocfilehash: 8d189d3ec3e6081dd37b912824f287cd75f39b35
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="cross-origin-resource-sharing-cors-support-for-the-azure-storage-services"></a>Współużytkowanie zasobów między źródłami (CORS) obsługę usług magazynu Azure do udostępniania
Począwszy od wersji 2013-08-15, usług Azure storage obsługuje udostępniania zasobów między źródłami (CORS) dla usług obiektów Blob, tabeli, kolejki i plików. CORS jest funkcja HTTP, która umożliwia aplikacja sieci web w jednej domenie dostęp do zasobów w innej domenie. Przeglądarki sieci Web zaimplementować ograniczenia zabezpieczeń znany jako [zasad samego pochodzenia](http://www.w3.org/Security/wiki/Same_Origin_Policy) uniemożliwiający strony sieci web na podstawie wywoływania interfejsów API w innej domenie; Mechanizm CORS zapewnia bezpieczny sposób, aby umożliwić jednej domeny (domena pochodzenia) do wywoływania interfejsów API w innej domenie. Zobacz [specyfikacji CORS](http://www.w3.org/TR/cors/) szczegółowe informacje dotyczące mechanizmu CORS.

Można ustawić zasady CORS osobno dla każdej usługi magazynu, wywołując [ustawić właściwości usługi Blob](https://msdn.microsoft.com/library/hh452235.aspx), [ustawić właściwości usługi kolejki](https://msdn.microsoft.com/library/hh452232.aspx), i [ustawić właściwości usługi tabeli](https://msdn.microsoft.com/library/hh452240.aspx). Po ustawieniu zasad CORS dla usługi do określenia, czy jest dozwolone zgodnie z regułami, które określono obliczone jest prawidłowo uwierzytelnione żądania dotyczącego z usługą z innej domeny.

> [!NOTE]
> Należy pamiętać, że CORS nie jest mechanizmem uwierzytelniania. Wszystkie żądania dla zasobu magazynu po włączeniu CORS muszą mieć podpisu właściwe uwierzytelnienie lub musi być dokonana przed publicznego zasobów.
> 
> 

## <a name="understanding-cors-requests"></a>Opis żądań CORPS
Żądanie CORS z domeny pochodzenia może składać się z dwóch oddzielnych żądania:

* Żądania wstępnego, który sprawdza CORS ograniczenia narzucone przez usługę. Żądania wstępnego jest wymagany, chyba że ma metodę żądania [prosta metoda](http://www.w3.org/TR/cors/), co oznacza, GET, HEAD lub POST.
* Rzeczywiste żądanie, wprowadzone w odniesieniu do żądanego zasobu.

### <a name="preflight-request"></a>Żądania wstępnego
Zapytania żądania wstępnego ograniczenia CORS, które zostały utworzone przez właściciela konta usługi magazynu. Przeglądarki sieci web (lub agenta użytkownika) wysyła żądanie opcji, która zawiera nagłówki żądania, metody i pochodzenia domeny. Usługa magazynu oblicza zamierzonej operacji na podstawie zestawu wstępnie skonfigurowanych zasad CORS, określających, które domeny pochodzenia żądania metody i nagłówki żądania można określić rzeczywistego żądania względem zasobów magazynu.

Jeśli istnieje reguła CORS, który odpowiada na żądania wstępnego CORS jest włączone dla usługi, Usługa odpowiedzi z kodem stanu 200 (OK) i obejmuje wymagane nagłówki kontroli dostępu w odpowiedzi.

Jeśli żadna reguła CORS odpowiada żądania wstępnego CORS nie jest włączone dla usługi, usługa będzie odpowiadać z kodem stanu 403 (Dostęp zabroniony).

Jeśli opcje żądania nie zawiera wymagane nagłówki CORS (nagłówki pochodzenia i Access-Control-Request-Method), usługa będzie odpowiadać z kodem stanu 400 (nieprawidłowe żądanie).

Należy pamiętać, że żądania wstępnego jest obliczane z usługą (obiektów Blob, kolejki i tabeli) i nie w odniesieniu do żądanego zasobu. Właściciel konta najpierw włączyć mechanizm CORS w ramach właściwości usługi konta, aby żądanie powiodło się.

### <a name="actual-request"></a>Rzeczywistego żądania
Po zaakceptowaniu żądania wstępnego i odpowiedź zostanie zwrócona, przeglądarka wyśle rzeczywistego żądania względem zasobów pamięci masowej. Przeglądarka będzie odmawiał rzeczywiste żądania od razu, jeśli wstępnej żądanie zostanie odrzucone.

Rzeczywistego żądania jest traktowana jako normalne żądania dotyczącego usługi magazynu. Obecność nagłówka źródła wskazuje, że żądanie jest żądaniem mechanizmu CORS i usługa będzie sprawdzać z regułami dopasowywania CORS. Jeśli zostanie znaleziony dopasowanie, nagłówki kontroli dostępu są dodawane do odpowiedzi i wysyłane z powrotem do klienta. Jeśli nie, nie są zwracane nagłówki CORS Access-Control.

## <a name="enabling-cors-for-the-azure-storage-services"></a>Włączanie mechanizmu CORS dla usługi Azure Storage
Zasady CORS są ustawione na poziomie usługi, więc musisz włączyć lub wyłączyć CORS dla każdej usługi (obiektów Blob, kolejki i tabeli) oddzielnie. Domyślnie CORS jest wyłączone dla każdej usługi. Aby włączyć mechanizm CORS, należy ustawić właściwości odpowiednią usługę, za pomocą wersji 2013-08-15 lub nowszym i Dodaj zasady CORS do właściwości usługi. Aby uzyskać szczegółowe informacje o tym, jak włączyć lub wyłączyć CORS dla usługi oraz jak ustawić zasady CORS, zapoznaj się [ustawić właściwości usługi Blob](https://msdn.microsoft.com/library/hh452235.aspx), [ustawić właściwości usługi kolejki](https://msdn.microsoft.com/library/hh452232.aspx), i [ustawić właściwości usługi tabeli](https://msdn.microsoft.com/library/hh452240.aspx).

Poniżej przedstawiono przykładowe jednej zasady CORS określona za pomocą operacji ustawić właściwości usługi:

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

Poniżej opisano każdy element uwzględnione w regule CORS:

* **AllowedOrigins**: domeny pochodzenia, które są dozwolone żądania z usługą magazynu za pomocą mechanizmu CORS. Domeny pochodzenia jest domeny, z której pochodzi żądanie. Należy pamiętać, że punkt początkowy musi być identyczna z uwzględnieniem wielkości liter z źródło, które wieku użytkownik wysyła do usługi. Można również użyć znaku wieloznacznego "*" zezwalająca na wszystkie domeny pochodzenia na wysyłanie żądań za pośrednictwem mechanizmu CORS. W przykładzie przedstawionym powyżej domen [ http://www.contoso.com ](http://www.contoso.com) i [ http://www.fabrikam.com ](http://www.fabrikam.com) mogą wysyłać żądania usługi przy użyciu mechanizmu CORS.
* **AllowedMethods**: metody (zlecenia HTTP żądania), mogą używających dla żądania CORS dla domeny pochodzenia. W powyższym przykładzie są dozwolone tylko żądania PUT i GET.
* **AllowedHeaders**: nagłówki żądania, które domeny pochodzenia może określać na żądanie CORS. W powyższym przykładzie są dozwolone wszystkie nagłówki metadanych, począwszy od x-ms metadane x-ms-meta docelowego, a x-ms-meta-abc. Należy pamiętać, że symbol wieloznaczny "*" wskazuje, że wszystkie nagłówka, począwszy od określonego prefiksu jest dozwolone.
* **ExposedHeaders**: nagłówki odpowiedzi, które mogą być wysyłany w odpowiedzi na żądanie CORS i udostępniane przez przeglądarkę, aby wystawcy żądania. W powyższym przykładzie przeglądarki otrzymuje instrukcję uwidacznia żadnych począwszy nagłówka x-ms-meta.
* **MaxAgeInSeconds**: maksymalną ilość czasu przeglądarki powinien buforowania przez wstępnej opcje żądania.

Do usług Azure storage obsługuje Określanie nagłówków prefiksem dla obu **AllowedHeaders** i **ExposedHeaders** elementów. Aby umożliwić kategorii nagłówków, można określić Wspólny prefiks do tej kategorii. Na przykład określenie *x-ms-meta** jako nagłówek prefiksem Określa regułę, która będzie odpowiadała wszystkie nagłówki, które zaczynają się od x-ms-meta.

Zasad CORS obowiązują następujące ograniczenia:

* Można określić maksymalnie pięć zasad CORS dla usługi magazynowania (obiektu Blob, tabel i kolejek).
* Maksymalny rozmiar wszystkich ustawień specyfikacji CORS reguł na żądanie, z wyłączeniem tagi XML nie może przekraczać 2 KB.
* Długość nagłówku dozwolonych, narażonych nagłówka lub dozwolone pochodzenie nie może przekraczać 256 znaków.
* Dozwolone nagłówki i nagłówki narażonych mogą być:
  * Literał nagłówków, których nazwa nagłówka dokładne podano, takich jak **x-ms-meta przetwarzane**. Można określić maksymalnie 64 literału nagłówki dla żądania.
  * Nagłówki, w którym dostępne są prefiks nagłówka, takie jak prefiks ** x-ms-meta-danych ***. Określenie prefiksu w ten sposób pozwala lub ujawnia żadnych nagłówek, który rozpoczyna się od danego prefiksu. Można określić maksymalnie dwa nagłówki prefiksem na żądanie.
* Metody (lub zlecenia HTTP), określona w **AllowedMethods** elementu musi być zgodna z metod obsługiwanych przez usługę Azure storage interfejsów API. Obsługiwane metody to DELETE, GET, HEAD, scalania, POST, opcje i PUT.

## <a name="understanding-cors-rule-evaluation-logic"></a>Opis logiki oceny reguły CORS
Gdy usługa Magazyn odbiera żądanie dotyczące stanu wstępnego lub rzeczywistego, ocenia tego żądania na podstawie reguł CORS, które zostało ustanowione dla usługi za pomocą odpowiednich operacji ustawić właściwości usługi. Zasady CORS są oceniane w kolejności, w którym zostały ustawione w treści żądania operacji ustawić właściwości usługi.

Zasady CORS są oceniane następująco:

1. Najpierw jest sprawdzany domeny pochodzenia żądania na podstawie domen dla **AllowedOrigins** elementu. Jeśli domeny pochodzenia jest uwzględniony na liście, wszystkie domeny są dozwolone lub z symbolem wieloznacznym "*", następnie zasady oceny będzie kontynuowane. Jeśli nie dołączono domeny pochodzenia, żądanie kończy się niepowodzeniem.
2. Następnie — metoda (lub zlecenie HTTP) żądania jest porównywany z metod opisanych w **AllowedMethods** elementu. Jeśli metoda jest uwzględniony na liście, będzie kontynuowana oceny zasad; Jeśli nie, żądanie kończy się niepowodzeniem.
3. Jeśli żądanie zgodny z regułą w jego domenie pochodzenia i jej metodę, tej reguły zostanie wybrany do przetworzenia żądania i żadne dodatkowe reguły są oceniane. Zanim będzie możliwe żądania, jednak wszelkie nagłówki określony w żądaniu są porównywane z nagłówków na liście **AllowedHeaders** elementu. Nagłówki wysyłane są niezgodne dozwolone nagłówki, żądanie kończy się niepowodzeniem.

Ponieważ reguły są przetwarzane w kolejności, w jakiej występują w treści żądania, najlepszym rozwiązaniem jest określenie najbardziej restrykcyjne zasady dotyczące źródeł najpierw na liście, dzięki czemu są one oceniane najpierw. Określ reguły, które są mniej restrykcyjnych — na przykład Reguła zezwalająca na wszystkie pochodzenia — na końcu listy.

### <a name="example--cors-rules-evaluation"></a>Przykład — CORS reguł oceny
W poniższym przykładzie przedstawiono treści żądania częściowej operacji można ustawić zasady CORS usługi magazynu. Zobacz [ustawić właściwości usługi Blob](https://msdn.microsoft.com/library/hh452235.aspx), [ustawić właściwości usługi kolejki](https://msdn.microsoft.com/library/hh452232.aspx), i [ustawić właściwości usługi tabeli](https://msdn.microsoft.com/library/hh452240.aspx) szczegółowe informacje dotyczące konstruowania żądania.

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
| **— Metoda** |**Origin** |**Nagłówki żądania** |**Rule Match** |**Result** |
| **PUT** |http://www.contoso.com |x-ms-blob-content-type |Pierwsza reguła |Powodzenie |
| **GET** |http://www.contoso.com |x-ms-blob-content-type |Drugą regułę |Powodzenie |
| **GET** |http://www.contoso.com |x-ms-client-request-id |Drugą regułę |Niepowodzenie |

Pierwsze żądanie pasuje do pierwszej reguły — domeny pochodzenia odpowiada dozwolonych źródeł, metoda zgodna dozwolone metody i nagłówek odpowiada dozwolone nagłówki — i tak zakończy się pomyślnie.

Drugie żądanie nie pasuje pierwszej reguły, ponieważ metoda jest niezgodna z dozwolone metody. Jednak odpowiadać drugą regułę, dlatego próba powiedzie się.

Trzeci żądanie jest zgodne drugą regułę w jego domenie pochodzenia i metody, więc nie dodatkowe reguły są sprawdzane. Jednak *nagłówka x-ms-client żądania id* nie jest dozwolona przez drugą regułę, więc żądanie zakończy się niepowodzeniem, mimo iż semantyka trzeci reguły by pozwoliła go powiodło się.

> [!NOTE]
> Chociaż ten przykład przedstawia mniej restrykcyjnych reguł przed bardziej restrykcyjne jeden, zazwyczaj najlepszym rozwiązaniem jest pierwsza lista najbardziej restrykcyjne zasady.
> 
> 

## <a name="understanding-how-the-vary-header-is-set"></a>Opis konfiguracji nagłówka Vary
*Vary* nagłówek jest standardowy nagłówek HTTP/1.1 zawierający zestaw pól nagłówka żądania, które powiadomienia o kryteriach, które zostały wybrane przez serwer przetwarzania żądania agenta przeglądarki lub użytkownika. *Vary* nagłówka jest głównie używane do buforowania przez serwery proxy, przeglądarki i CDN, które umożliwia jak powinno być buforowane odpowiedzi. Aby uzyskać więcej informacji, zobacz specyfikację dla [nagłówka Vary](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).

Gdy przeglądarki lub innego agenta użytkownika będzie buforować odpowiedź z żądania CORS, domeny pochodzenia są buforowane jako dozwolone pochodzenie. Gdy druga domena wysyła sam żądanie zasobu magazynu, gdy pamięć podręczna jest aktywny, agent użytkownika pobiera domeny pochodzenia pamięci podręcznej. Drugiej domeny nie pasuje pamięci podręcznej domeny, więc żądanie zakończy się niepowodzeniem, gdy go w przeciwnym razie powiedzie się. W niektórych przypadkach usługi Azure Storage ustawia Nagłówek Vary **pochodzenia** Aby wysłać kolejne żądania CORS do usługi, gdy żądania domeny różni się od buforowanego pochodzenia agenta użytkownika.

Ustawia magazyn Azure *Vary* nagłówka do **pochodzenia** do rzeczywistego żądania GET/HEAD w następujących przypadkach:

* Jeśli żądanie danych pierwotnych dokładnie odpowiada dozwolone pochodzenie zdefiniowana przez regułę CORS. Być dokładnego dopasowania, zasada CORS nie może zawierać symbol wieloznaczny "*" znaków.
* Nie zasady dopasowania żądanie danych pierwotnych, ale CORS jest włączone dla usługi magazynowania.

W przypadku, jeśli żądanie GET/HEAD odpowiada CORS regułę, która zezwala na wszystkie pochodzenia odpowiedź wskazuje, że wszystkie źródła są dozwolone i pamięci podręcznej agenta użytkownika umożliwi kolejnych żądań wysyłanych z dowolnej domeny pochodzenia, gdy pamięć podręczna jest aktywny.

Należy pamiętać, że dla żądania za pomocą metod innych niż GET/HEAD, usług magazynu nie ustawi nagłówka Vary, ponieważ przez agentów użytkownika nie są buforowane odpowiedzi na te metody.

W poniższej tabeli przedstawiono, jak usługa Azure storage będzie odpowiadał na żądania GET/HEAD oparte na przypadkach opisanych powyżej:

| Żądanie | Ustawienia konta i wyniki oceny reguły |  |  | Odpowiedź |  |  |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| **Nagłówek Origin na żądanie** |**Zasady CORS określony dla tej usługi** |**Istnieje reguła dopasowywania, która umożliwia origins(*) wszystkie** |**Istnieje reguła dopasowywania pochodzenia dokładnego dopasowania** |**Odpowiedź zawiera Nagłówek Vary ustawioną źródła** |**Odpowiedź zawiera Access-Control-dozwolone-Origin: "*"** |**Odpowiedź zawiera Access-Control-widoczne-Headers** |
| Nie |Nie |Nie |Nie |Nie |Nie |Nie |
| Nie |Yes |Nie |Nie |Yes |Nie |Nie |
| Nie |Yes |Yes |Nie |Nie |Yes |Yes |
| Yes |Nie |Nie |Nie |Nie |Nie |Nie |
| Yes |Yes |Nie |Yes |Yes |Nie |Yes |
| Yes |Yes |Nie |Nie |Yes |Nie |Nie |
| Yes |Yes |Yes |Nie |Nie |Yes |Yes |

## <a name="billing-for-cors-requests"></a>Rozliczeń dla żądań CORS
Inspekcja pomyślnych żądań są rozliczane włączenie mechanizmu CORS dla usług magazynu dla Twojego konta (wywołując [ustawić właściwości usługi Blob](https://msdn.microsoft.com/library/hh452235.aspx), [ustawić właściwości usługi kolejki](https://msdn.microsoft.com/library/hh452232.aspx), lub [ustawić właściwości usługi tabeli](https://msdn.microsoft.com/library/hh452240.aspx)). Aby zminimalizować koszty, rozważ ustawienie **MaxAgeInSeconds** element z CORS reguł na dużą wartość tak, aby agent użytkownika buforuje żądania.

Niepomyślnych żądań wstępnych nie będą naliczane.

## <a name="next-steps"></a>Kolejne kroki
[Ustaw właściwości usługi Blob](https://msdn.microsoft.com/library/hh452235.aspx)

[Ustaw właściwości usługi kolejki](https://msdn.microsoft.com/library/hh452232.aspx)

[Ustaw właściwości usługi tabel](https://msdn.microsoft.com/library/hh452240.aspx)

[W3C Cross-Origin Resource Sharing specyfikacji](http://www.w3.org/TR/cors/)

