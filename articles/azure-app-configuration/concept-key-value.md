---
title: Opis magazynu kluczy i wartości konfiguracji aplikacji platformy Azure
description: Dowiedz się, jak dane konfiguracji są przechowywane w konfiguracji aplikacji platformy Azure.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/19/2020
ms.openlocfilehash: 0b83a35d912c97ae25bc2d69d076e8eae8ca490f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523608"
---
# <a name="keys-and-values"></a>Klawisze i wartości

Konfiguracja aplikacji platformy Azure przechowuje dane konfiguracji jako pary klucz-wartość. Pary klucz-wartość to prosta i elastyczna reprezentacja ustawień aplikacji używanych przez deweloperów.

## <a name="keys"></a>Klucze

Klucze służą jako identyfikatory par klucz-wartość i są używane do przechowywania i pobierania odpowiednich wartości. Powszechną praktyką jest organizowanie kluczy w hierarchicznym obszarze nazw przy `/` użyciu `:`ogranicznika znaków, takiego jak lub . Użyj konwencji najlepiej nadaje się do aplikacji. Usługa App Configuration traktuje klucze jako jedną całość. Nie analizuje kluczy, aby dowiedzieć się, jak ich nazwy są skonstruowane lub wymusić dowolną regułę na nich.

Oto dwa przykłady nazw kluczy uporządkowanych w hierarchii:

* Na podstawie usług składowych

        AppName:Service1:ApiEndpoint
        AppName:Service2:ApiEndpoint

* Na podstawie regionów wdrożenia

        AppName:Region1:DbEndpoint
        AppName:Region2:DbEndpoint

Użycie danych konfiguracyjnych w ramach aplikacji może dyktować określone schematy nazewnictwa dla wartości kluczy. Na przykład platforma Java Spring Cloud `Environment` definiuje zasoby, które dostarczają ustawienia do aplikacji Spring.  Są one parametryzowane przez zmienne, które zawierają *nazwę aplikacji* i *profil*. Klucze danych konfiguracyjnych związanych z wiosenną chmurą zazwyczaj rozpoczynają się od tych dwóch elementów oddzielonych ogranicznikiem.

Klucze przechowywane w usłudze App Configuration są opartymi na standardzie Unicode ciągami uwzględniającymi wielkość liter. Klucze *app1* i *App1* są różne w sklepie konfiguracji aplikacji. Należy o tym pamiętać podczas korzystania z ustawień konfiguracji w aplikacji, ponieważ niektóre struktury obsługi kluczy konfiguracji bez uwzględniania wielkości liter. Nie zaleca się używania case do rozróżniania klawiszy.

Można użyć dowolnego znaku unicode w `*` `,`nazwach `\`kluczy z wyjątkiem , i .  Jeśli chcesz dołączyć jedną z tych zastrzeżonych `\{Reserved Character}`znaków, ucieknij go za pomocą programu . 

Istnieje łączny limit rozmiaru 10 KB na parze klucz-wartość. Ten limit obejmuje wszystkie znaki w kluczu, jego wartość i wszystkie skojarzone atrybuty opcjonalne. W ramach tego limitu możesz mieć wiele poziomów hierarchii dla kluczy.

### <a name="design-key-namespaces"></a>Projektowanie kluczowych obszarów nazw

Istnieją dwie ogólne metody nadawania nazw kluczom używanym na potrzeby danych konfiguracji: płaska i hierarchiczna. Te metody są podobne z punktu widzenia użycia aplikacji, ale hierarchiczne nazewnictwo oferuje szereg zalet:

* Łatwość odczytywania. Ograniczniki w hierarchicznej nazwie klucza działają jako spacje w zdaniu. Zapewniają one również naturalne przerwy między słowami.
* Łatwość zarządzania. Hierarchia nazw kluczy reprezentuje grupy logiczne danych konfiguracji.
* Łatwość użycia. Łatwiej jest napisać zapytanie, które dopasowuje klucze do wzorca w strukturze hierarchicznej i pobiera tylko część danych konfiguracji. Ponadto wiele nowszych platform programowania mają natywną obsługę danych konfiguracji hierarchicznej, dzięki czemu aplikacja może korzystać z określonych zestawów konfiguracji.

Klucze w usłudze App Configuration można organizować hierarchicznie na wiele sposobów. Pomyśl o takich kluczy jak [URI](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier). Każdy klucz hierarchiczny jest *ścieżką* zasobu składającą się z jednego lub więcej składników, które są połączone ze sobą przez ograniczniki. Wybierz znak, który ma być używany jako ogranicznik na podstawie potrzeb aplikacji, języka programowania lub struktury. Użyj wielu ograniczników dla różnych kluczy w konfiguracji aplikacji.

### <a name="label-keys"></a>Klawisze etykiet

Wartości klucza w konfiguracji aplikacji mogą opcjonalnie mieć atrybut etykiety. Etykiety służą do rozróżniania wartości kluczy za pomocą tego samego klucza. Aplikacja *klucz1* z etykietami *A* i *B* tworzy dwa oddzielne klucze w magazynie konfiguracji aplikacji. Domyślnie wartość klucza nie ma etykiety. Aby jawnie odwoływać się do `\0` wartości klucza bez `%00`etykiety, użyj (adres URL zakodowany jako ).

Etykieta zapewnia wygodny sposób tworzenia wariantów klucza. Typowym zastosowaniem etykiet jest określenie wielu środowisk dla tego samego klucza:

    Key = AppName:DbEndpoint & Label = Test
    Key = AppName:DbEndpoint & Label = Staging
    Key = AppName:DbEndpoint & Label = Production

### <a name="version-key-values"></a>Wartości klucza wersji

Konfiguracja aplikacji nie jest automatycznie wersją wartości klucza. Etykiety można używać jako sposobu tworzenia wielu wersji wartości klucza. Na przykład można wprowadzić numer wersji aplikacji lub identyfikator zatwierdzenia Git w etykietach, aby zidentyfikować wartości kluczy skojarzone z określoną kompilacją oprogramowania.

Można użyć dowolnego znaku unicode `*`w `,`etykietach z wyjątkiem , i `\`. Znaki te są zastrzeżone. Aby dołączyć znak zastrzeżony, należy `\{Reserved Character}`go uniknąć za pomocą programu .

### <a name="query-key-values"></a>Wartości klucza kwerendy

Każda wartość klucza jest jednoznacznie identyfikowana przez `null`jego klucz oraz etykietę, która może być . Kwerenda w magazynie konfiguracji aplikacji dla wartości kluczy, określając wzorzec. Sklep konfiguracji aplikacji zwraca wszystkie wartości kluczy, które pasują do wzorca i odpowiadających im wartości i atrybutów. Użyj następujących wzorców kluczowych w wywołaniach interfejsu API REST do konfiguracji aplikacji:

| Klucz | |
|---|---|
| Element `key` jest pomijany lub `key=*` | Pasuje do wszystkich kluczy |
| `key=abc` | Dokładnie dopasowuje nazwę **klucza abc** |
| `key=abc*` | Pasuje do nazw kluczy, które zaczynają się od ciągu **abc** |
| `key=abc,xyz` | Dopasowuje nazwy kluczy **abc** lub **xyz**. Ograniczone do pięciu csv |

Można również dołączyć następujące wzorce etykiet:

| Label | |
|---|---|
| Element `label` jest pomijany lub `label=*` | Dopasowuje dowolną etykietę, która zawiera`null` |
| `label=%00` | Etykieta Dopasowuje `null` |
| `label=1.0.0` | Dokładnie pasuje do etykiety **1.0.0** |
| `label=1.0.*` | Pasuje do etykiet zaczynających się od **1.0.** |
| `label=%00,1.0.0` | Dopasowuje etykiety `null` lub **1.0.0**, ograniczone do pięciu CSV |

## <a name="values"></a>Wartości

Wartości przypisane do kluczy są również ciągami Unicode. W przypadku wartości można używać wszystkich znaków Unicode. Istnieje opcjonalny, zdefiniowany przez użytkownika typ zawartości skojarzony z każdą wartością. Ten atrybut służy do przechowywania informacji o wartości, która pomaga aplikacji w prawidłowym przetwarzaniu.

Dane konfiguracyjne przechowywane w magazynie konfiguracji aplikacji są szyfrowane w spoczynku i podczas przesyłania. Klucze nie są szyfrowane w spoczynku. Konfiguracja aplikacji nie jest rozwiązaniem zastępczym dla usługi Azure Key Vault. Nie przechowuj w nim wpisów tajnych aplikacji.

## <a name="next-steps"></a>Następne kroki

* [Migawka w czasie](./concept-point-time-snapshot.md)  
* [Zarządzanie funkcjami](./concept-feature-management.md)  
