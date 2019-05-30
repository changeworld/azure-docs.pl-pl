---
title: Magazyn klucz-wartość usługi Azure App Configuration | Microsoft Docs
description: Omówienie przechowywania danych konfiguracji w konfiguracji aplikacji platformy Azure
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.service: azure-app-configuration
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 04/19/2019
ms.author: yegu
ms.openlocfilehash: b25cc8c04aed8cd333ff4de5b12db6674323787d
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393603"
---
# <a name="keys-and-values"></a>Klucze i wartości

Konfiguracja aplikacji Azure przechowuje dane konfiguracji jako pary klucz wartość. Pary klucz wartość to proste, ale elastyczny sposób reprezentują różne rodzaje ustawień aplikacji, które deweloperzy, którzy znają.

## <a name="keys"></a>Klucze

Klucze służą jako nazwy dla par klucz wartość i są używane do przechowywania i pobierania odpowiednie wartości. Jest to powszechną praktyką w celu zorganizowania klucze hierarchicznej przestrzeni nazw przy użyciu znaku ogranicznika, takich jak `/` lub `:`. Należy użyć Konwencji, który jest najbardziej odpowiedni dla aplikacji. Usługa App Configuration traktuje klucze jako jedną całość. Go nie przeanalizować klucze, aby zorientować się, jak ich nazwy są skonstruowane ani wymuszać reguły na nich.

Użycie danych konfiguracji w ramach struktury aplikacji może określać określonych systemów nazewnictwa dla wartości klucza. Na przykład w języku Java Spring Cloud framework definiuje `Environment` zasoby, które określania ustawień dla aplikacji platformy Spring, aby zostać sparametryzowane przez zmienne, które obejmują *Nazwa aplikacji* i *profilu*. Klucze dla danych konfiguracji odnoszące się do Spring Cloud zaczynają się zwykle te dwa elementy, oddzielając ogranicznika.

Klucze przechowywane w usłudze App Configuration są opartymi na standardzie Unicode ciągami uwzględniającymi wielkość liter. W magazynie konfiguracji aplikacji klucze *app1* i *App1* są rozróżniane. Miej to na uwadze, korzystając z ustawień konfiguracji w aplikacji, ponieważ niektóre środowiska obsługi kluczy konfiguracji uwzględniania wielkości liter. Na przykład system konfiguracji platformy ASP.NET Core traktuje klucze jako ciągi bez uwzględniania wielkości liter. Aby uniknąć nieprzewidywalnych zachowania, gdy zapytanie konfiguracji aplikacji w aplikacji platformy ASP.NET Core, nie używaj kluczy, które różnią się jedynie wielkość liter.

Można użyć dowolnych znaków unicode w nazwach kluczy wprowadzane do konfiguracji aplikacji, z wyjątkiem `*`, `,`, i `\`. Następujące znaki są zastrzeżone. Jeśli potrzebujesz uwzględnić zastrzeżonego znaku, musisz wyjść z niego przy użyciu `\{Reserved Character}`. Obowiązuje limit łącznego rozmiaru 10 000 znaków w pary klucz wartość. Ten limit obejmuje wszystkie znaki w kluczu, a jego wartość i wszystkie skojarzone z opcjonalnych atrybutów. W ramach tego limitu możesz mieć wiele poziomów hierarchii dla kluczy.

### <a name="design-key-namespaces"></a>Projektowanie przestrzeni nazw kluczy

Istnieją dwie ogólne metody nadawania nazw kluczom używanym na potrzeby danych konfiguracji: płaska i hierarchiczna. Te metody są podobne, z punktu widzenia użycia aplikacji, ale hierarchiczne nazewnictwa oferuje następujące korzyści:

* Łatwość odczytywania. Zamiast jednego długo sekwencja znaków ograniczniki w hierarchiczne nazwa klucza pełnią miejsca do magazynowania w zdaniu. Zapewniają także naturalne przerwy między wyrazami.
* Łatwość zarządzania. Hierarchia nazw kluczy reprezentuje grupy logiczne danych konfiguracji.
* Łatwość użycia. Łatwiej jest napisać zapytanie, które dopasowuje klucze do wzorca w strukturze hierarchicznej i pobiera tylko część danych konfiguracji. Ponadto wiele struktur programowania nowszej mieć natywną obsługę danych hierarchicznych konfiguracji, takie, które mogą ułatwić aplikacji wykorzystujących konkretnych zestawów konfiguracji.

Klucze w usłudze App Configuration można organizować hierarchicznie na wiele sposobów. Traktować te klucze jak [identyfikatorów URI](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier). Każdy klucz hierarchicznej jest zasobem *ścieżki* składa się z co najmniej jednego składnika, które są łączone ze sobą za ograniczniki. Wybierz jakie znaków do użycia jako ogranicznika, dla jakich aplikacji, język programowania, lub potrzebuje framework. Wiele ograniczników na użytek różnych kluczy w konfiguracji aplikacji.

Poniżej przedstawiono kilka przykładów, jak można ustrukturyzować nazwy kluczy w hierarchię:

* Na podstawie usług składowych

        AppName:Service1:ApiEndpoint
        AppName:Service2:ApiEndpoint

* Na podstawie regionów wdrożenia

        AppName:Region1:DbEndpoint
        AppName:Region2:DbEndpoint

### <a name="label-keys"></a>Klucze etykiety

Wartości klucza w konfiguracji aplikacji mogą opcjonalnie mieć atrybut etykiety. Etykiety umożliwiają odróżnienie wartości klucza z tego samego klucza. Klucz *app1* z etykietami *A* i *B* formularzy dwa oddzielne klucze w magazynie konfiguracji aplikacji. Domyślnie etykiety wartość klucza jest pusta, lub `null`.

Etykieta zapewnia wygodny sposób utworzenia wariantów klucza. Zazwyczaj etykiet, które jest używane do określenia wielu środowisk na potrzeby tego samego klucza:

    Key = AppName:DbEndpoint & Label = Test
    Key = AppName:DbEndpoint & Label = Staging
    Key = AppName:DbEndpoint & Label = Production

### <a name="version-key-values"></a>Wartości klucza wersji

Konfiguracja aplikacji automatycznie nie wartości klucza wersji ich modyfikacji. Używanie etykiet jako sposób tworzenia wielu wersji wartość klucza. Na przykład można wpisać numer wersji aplikacji lub identyfikator zatwierdzenia usługi Git w etykiecie do identyfikowania wartości klucza skojarzone z kompilacji konkretnego oprogramowania.

Można użyć dowolnych znaków unicode w etykiecie, z wyjątkiem `*`, `,`, i `\`. Następujące znaki są zastrzeżone. Aby dołączyć zastrzeżonego znaku, musisz wyjść z niego przy użyciu `\{Reserved Character}`.

### <a name="query-key-values"></a>Wartości klucza zapytania

Każda wartość klucza jest unikatowo identyfikowana przez jego klucza oraz etykiety, które mogą być `null`. Konfiguracja sklepu z aplikacjami dla wartości klucza są zapytania, określając wzorca. Konfiguracja sklepu zwraca wszystkie wartości kluczy, które pasują do wzorca oraz odpowiadające im wartości i atrybutów. Użyj następujących wzorców klucza w wywołaniach interfejsu API REST konfiguracji aplikacji:

| Klucz | |
|---|---|
| Element `key` jest pomijany lub `key=*` | Pasuje do wszystkich kluczy |
| `key=abc` | Nazwa klucza dopasowania **abc** dokładnie |
| `key=abc*` | Pasuje do nazw kluczy, które zaczynają się od ciągu **abc** |
| `key=*abc` | Pasuje do nazw kluczy, które kończą się ciągiem **abc** |
| `key=*abc*` | Pasuje do nazw kluczy, które zawierają ciąg **abc** |
| `key=abc,xyz` | Dopasowania nazw kluczy **abc** lub **xyz**, maksymalnie pięć udostępnionych woluminów klastra |

Może zawierać również następujące wzorce etykiety:

| Label | |
|---|---|
| Element `label` jest pomijany lub `label=*` | Dopasowuje wszelkie etykiety, która zawiera `null` |
| `label=%00` | Dopasowuje `null` etykiety |
| `label=1.0.0` | Dokładnie pasuje do etykiety **1.0.0** |
| `label=1.0.*` | Pasuje do etykiet zaczynających się od **1.0.** |
| `label=*.0.0` | Pasuje do etykiet kończących się na **.0.0** |
| `label=*.0.*` | Pasuje do etykiet zawierających **.0.** |
| `label=%00,1.0.0` | Pasuje do etykiety `null` lub **1.0.1**, maksymalnie pięć udostępnionych woluminów klastra |

## <a name="values"></a>Wartości

Wartości przypisane do kluczy są również ciągami Unicode. W przypadku wartości można używać wszystkich znaków Unicode. Brak opcjonalne zdefiniowanych przez użytkownika typ zawartości skojarzone z każdą wartość. Użyj tego atrybutu do przechowywania informacji, na przykład schemat kodowania, o wartość, która pomaga aplikacjom poprawnie go przetworzyć.

Dane konfiguracji przechowywane w magazynie konfiguracji aplikacji, która zawiera wszystkie klucze i wartości, są szyfrowane podczas przechowywania i podczas przesyłania. Konfiguracja aplikacji nie jest rozwiązaniem zastąpienia dla usługi Azure Key Vault. Nie należy przechowywać w nim wpisów tajnych aplikacji.

## <a name="next-steps"></a>Kolejne kroki

* [Migawkę punktu w czasie](./concept-point-time-snapshot.md)  
* [Funkcja zarządzania](./concept-feature-management.md)  
