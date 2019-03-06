---
title: Magazyn klucz-wartość usługi Azure App Configuration | Microsoft Docs
description: Omówienie sposobu przechowywania danych konfiguracji w usłudze Azure App Configuration
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: f04067358b0b2bae745727a5dd7a1f5554f9f70e
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2019
ms.locfileid: "56884984"
---
# <a name="key-value-store"></a>Magazyn wartości klucza

Usługa Azure App Configuration przechowuje dane konfiguracji jako pary klucz-wartość — jest to prosty, a przy tym elastyczny sposób reprezentowania różnorodnych ustawień aplikacji, znany deweloperom.

## <a name="keys"></a>Klucze

Klucze służą jako nazwy dla par klucz-wartość i są używane do przechowywania i pobierania odpowiednich wartości. Powszechną praktyką jest organizowanie kluczy w hierarchicznej przestrzeni nazw przy użyciu ogranicznika znaku (takiego jak `/` lub `:`) na podstawie konwencji, która jest najbardziej odpowiednia dla danej aplikacji. Usługa App Configuration traktuje klucze jako jedną całość. Klucze nie są analizowane w celu ustalenia ich struktury i nie są na nich wymuszane żadne reguły.

Użycie magazynu konfiguracji w ramach struktury aplikacji może wymagać określonych schematów nazewnictwa dla par klucz-wartość. Na przykład struktura Spring Cloud języka Java definiuje zasoby `Environment`, które zawierają ustawienia dla aplikacji platformy Spring, tak, aby były parametryzowane za pomocą zmiennych, takich jak *nazwa aplikacji* i *profil*. Klucze dla danych konfiguracji związanych z platformą Spring Cloud zazwyczaj zaczynają się od tych dwóch elementów oddzielonych ogranicznikiem.

Klucze przechowywane w usłudze App Configuration są opartymi na standardzie Unicode ciągami uwzględniającymi wielkość liter. W magazynie konfiguracji aplikacji klucze *app1* i *App1* są rozróżniane. Należy o tym pamiętać, korzystając z ustawień konfiguracji w aplikacji, ponieważ niektóre struktury obsługują klucze konfiguracji bez uwzględniania wielkości liter. Na przykład system konfiguracji platformy ASP.NET Core traktuje klucze jako ciągi bez uwzględniania wielkości liter. Aby uniknąć nieprzewidywalnych zachowań podczas wykonywania zapytań względem usługi App Configuration w ramach aplikacji platformy ASP.NET Core, nie należy używać kluczy, które różnią się tylko wielkością liter.

W nazwach kluczy wprowadzanych do usługi App Configuration można używać dowolnych znaków Unicode z wyjątkiem znaków `*`, `,` i `\`, które są zastrzeżone. Jeśli chcesz uwzględnić znak zastrzeżony, musisz to zrobić w następujący sposób: `\{Reserved Character}`. Limit łącznego rozmiaru dla pary klucz-wartość wynosi 10K znaków. Obejmuje to wszystkie znaki w kluczu, w jego wartości i we wszystkich skojarzonych atrybutach opcjonalnych. W ramach tego limitu możesz mieć wiele poziomów hierarchii dla kluczy.

### <a name="designing-key-namespaces"></a>Projektowanie przestrzeni nazw kluczy

Istnieją dwie ogólne metody nadawania nazw kluczom używanym na potrzeby danych konfiguracji: płaska i hierarchiczna. Te metody są bardzo podobne z punktu widzenia użycia aplikacji, jednak ta druga metoda oferuje szereg korzyści:

* Łatwość odczytywania. Zamiast jednej długiej sekwencji znaków są ograniczniki, które w hierarchicznej nazwie klucza działają jak spacje w zdaniu i zapewniają naturalne przerwy między wyrazami.
* Łatwość zarządzania. Hierarchia nazw kluczy reprezentuje grupy logiczne danych konfiguracji.
* Łatwość użycia. Łatwiej jest napisać zapytanie, które dopasowuje klucze do wzorca w strukturze hierarchicznej i pobiera tylko część danych konfiguracji. Ponadto wiele nowszych środowisk programistycznych oferuje natywną obsługę hierarchicznych danych konfiguracji, dzięki czemu Twoja aplikacja może wykorzystać określone zestawy konfiguracji.

Klucze w usłudze App Configuration można organizować hierarchicznie na wiele sposobów. Takie klucze można traktować jako [identyfikatory URI](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier). Każdy klucz hierarchiczny jest *ścieżką* zasobu złożoną z jednego lub kilku składników połączonych za pomocą ograniczników. Możesz wybrać, jakiego znaku użyć jako ogranicznika, w zależności od tego, czego potrzebuje Twoja aplikacja, język programowania lub struktura. W usłudze App Configuration można użyć wielu ograniczników dla różnych kluczy.

Poniżej przedstawiono kilka przykładów, jak można ustrukturyzować nazwy kluczy w hierarchię:

* Na podstawie środowisk

        AppName:Test:DB:Endpoint
        AppName:Staging:DB:Endpoint
        AppName:Production:DB:Endpoint

* Na podstawie usług składowych

        AppName:Service1:Test:DB:Endpoint
        AppName:Service1:Staging:DB:Endpoint
        AppName:Service1:Production:DB:Endpoint
        AppName:Service2:Test:DB:Endpoint
        AppName:Service2:Staging:DB:Endpoint
        AppName:Service2:Production:DB:Endpoint

* Na podstawie regionów wdrożenia

        AppName:Production:Region1:DB:Endpoint
        AppName:Production:Region2:DB:Endpoint

### <a name="versioning-key-values"></a>Przechowywanie wersji par klucz-wartość

Pary klucz-wartość w usłudze App Configuration mogą opcjonalnie mieć atrybut **etykiety**. Etykiety umożliwiają rozróżnienie par klucz-wartość z tym samym kluczem. Klucz *app1* z etykietami *v1* i *v2* tworzy dwie oddzielne pary klucz-wartość w magazynie konfiguracji aplikacji. Domyślnie etykieta dla pary klucz-wartość jest pusta (lub równa `null`).

Usługa App Configuration nie tworzy wersji par klucz-wartość automatycznie, gdy są one modyfikowane. Przy użyciu etykiet można tworzyć wiele wersji pary klucz-wartość. W etykietach można na przykład umieścić numer wersji aplikacji lub identyfikator zatwierdzenia usługi Git, aby identyfikować pary klucz-wartość skojarzone z określoną kompilacją oprogramowania.

W etykietach można używać dowolnych znaków Unicode z wyjątkiem znaków `*`, `,` i `\`, które są zastrzeżone. Jeśli chcesz uwzględnić znak zastrzeżony, musisz to zrobić w następujący sposób: `\{Reserved Character}`.

### <a name="querying-key-values"></a>Wysyłanie zapytań względem par klucz-wartość

Każda para klucz-wartość jest unikatowo identyfikowana za pomocą klucza oraz etykiety, która może mieć wartość `null`. Aby wysłać zapytanie do magazynu konfiguracji aplikacji dotyczące par klucz-wartość, należy określić wzorzec. Magazyn konfiguracji aplikacji zwróci wszystkie pary klucz-wartość pasujące do wzorca wraz z odpowiadającymi im wartościami i atrybutami. W wywołaniach interfejsu API REST do usługi App Configuration możesz użyć następujących wzorców klucza:

| Klucz | |
|---|---|
| Element `key` jest pomijany lub `key=*` | Pasuje do wszystkich kluczy |
| `key=abc` | Dokładnie pasuje do nazwy klucza **abc**. |
| `key=abc*` | Pasuje do nazw kluczy, które zaczynają się od ciągu **abc** |
| `key=*abc` | Pasuje do nazw kluczy, które kończą się ciągiem **abc** |
| `key=*abc*` | Pasuje do nazw kluczy, które zawierają ciąg **abc** |
| `key=abc,xyz` | Pasuje do nazw kluczy **abc** lub **xyz**, z ograniczeniem do 5 plików CSV |

Możesz również uwzględnić następujące wzorce etykiety:

| Label | |
|---|---|
| Element `label` jest pomijany lub `label=*` | Pasuje do każdej etykiety, także równej `null` |
| `label=%00` | Pasuje do etykiety `null`. |
| `label=1.0.0` | Dokładnie pasuje do etykiety **1.0.0** |
| `label=1.0.*` | Pasuje do etykiet zaczynających się od **1.0.** |
| `label=*.0.0` | Pasuje do etykiet kończących się na **.0.0** |
| `label=*.0.*` | Pasuje do etykiet zawierających **.0.** |
| `label=%00,1.0.0` | Pasuje do etykiet `null` lub **1.0.1**, z ograniczeniem do 5 plików CSV |

## <a name="values"></a>Wartości

Wartości przypisane do kluczy są również ciągami Unicode. W przypadku wartości można używać wszystkich znaków Unicode. Istnieje opcjonalny, zdefiniowany przez użytkownika **typ zawartości** skojarzony z każdą wartością. Za pomocą tego atrybutu można przechowywać informacje (na przykład schemat kodowania) dotyczące wartości, które pomagają aplikacji w jej poprawnym przetworzeniu.

Dane konfiguracji przechowywane w magazynie konfiguracji aplikacji, w tym wszystkie klucze i wartości, są szyfrowane podczas przechowywania i podczas przesyłania. Niemniej jednak usługa App Configuration nie jest rozwiązaniem zastępującym usługę Azure Key Vault. Nie należy w niej przechowywać kluczy tajnych aplikacji.

## <a name="next-steps"></a>Następne kroki

* [Pojęcie: Migawka punktu w czasie](concept-point-time-snapshot.md)  
