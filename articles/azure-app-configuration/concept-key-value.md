---
title: Magazyn klucz-wartość usługi Azure App Configuration | Microsoft Docs
description: Przegląd sposobu przechowywania danych konfiguracji w usłudze Azure App Configuration
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
ms.openlocfilehash: 054de294c9edb0fe5b75da4ac7fd54ce987123de
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185220"
---
# <a name="keys-and-values"></a>Klucze i wartości

Konfiguracja aplikacji platformy Azure przechowuje dane konfiguracji jako pary klucz-wartość. Pary klucz-wartość są prostymi, ale elastycznymi sposobami reprezentowania różnych rodzajów ustawień aplikacji, z którymi deweloperzy znają.

## <a name="keys"></a>Klucze

Klucze służą jako nazwa par klucz-wartość i są używane do przechowywania i pobierania odpowiednich wartości. Typowym sposobem organizowania kluczy w hierarchiczną przestrzeń nazw przy użyciu ogranicznika znaków, takiego jak `/` lub `:`. Użyj Konwencji, która jest najbardziej odpowiednia dla Twojej aplikacji. Usługa App Configuration traktuje klucze jako jedną całość. Nie analizuje ona kluczy, aby ustalić, jak ich nazwy są strukturalne lub wymuszać każdą z nich regułę.

Użycie danych konfiguracji w ramach struktur aplikacji może dyktować określone schematy nazewnictwa dla wartości kluczy. Przykładowo struktura chmurowa w języku Java definiuje `Environment` zasobów, które dostarczają do aplikacji sprężynowej, aby były sparametryzowane przez zmienne, które zawierają nazwę i *profil* *aplikacji* . Klucze służące do wiosennych danych konfiguracyjnych związanych z chmurą zwykle zaczynają się od tych dwóch elementów oddzielonych ogranicznikami.

Klucze przechowywane w usłudze App Configuration są opartymi na standardzie Unicode ciągami uwzględniającymi wielkość liter. Klucze *APP1* i *APP1* są odrębne w magazynie konfiguracji aplikacji. Należy pamiętać o tym, gdy używasz ustawień konfiguracji w ramach aplikacji, ponieważ niektóre struktury obsługują bez uwzględniania wielkości liter w kluczach konfiguracji. Na przykład system konfiguracji platformy ASP.NET Core traktuje klucze jako ciągi bez uwzględniania wielkości liter. Aby uniknąć nieprzewidywalnych zachowań podczas wykonywania zapytania dotyczącego konfiguracji aplikacji w aplikacji ASP.NET Core, nie używaj kluczy, które różnią się tylko wielkością liter.

W nazwach kluczy wprowadzonych w konfiguracji aplikacji można używać dowolnego znaku Unicode, z wyjątkiem `*`, `,`i `\`. Te znaki są zarezerwowane. Jeśli konieczne jest dołączenie zastrzeżonego znaku, należy go wypróbować za pomocą `\{Reserved Character}`. W parze klucz-wartość istnieje łączny limit rozmiaru wynoszący 10 000 znaków. Ten limit obejmuje wszystkie znaki w kluczu, jego wartość i wszystkie skojarzone opcjonalne atrybuty. W ramach tego limitu możesz mieć wiele poziomów hierarchii dla kluczy.

### <a name="design-key-namespaces"></a>Przestrzenie nazw kluczy projektu

Istnieją dwie ogólne metody nadawania nazw kluczom używanym na potrzeby danych konfiguracji: płaska i hierarchiczna. Te metody są podobne do punktu widzenia użycia aplikacji, ale hierarchiczne nazewnictwo oferuje szereg korzyści:

* Łatwość odczytywania. Zamiast jednej długiej sekwencji znaków ograniczniki w funkcji nazwy klucza hierarchicznego jako spacje w zdaniu. Zapewniają także naturalne przerwy między wyrazami.
* Łatwość zarządzania. Hierarchia nazw kluczy reprezentuje grupy logiczne danych konfiguracji.
* Łatwość użycia. Łatwiej jest napisać zapytanie, które dopasowuje klucze do wzorca w strukturze hierarchicznej i pobiera tylko część danych konfiguracji. Ponadto wiele nowszych platform programistycznych ma natywną obsługę hierarchicznych danych konfiguracyjnych, dzięki czemu aplikacja może korzystać z określonych zestawów konfiguracji.

Klucze w usłudze App Configuration można organizować hierarchicznie na wiele sposobów. Należy traktować takie klucze jako [identyfikatory URI](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier). Każdy klucz hierarchiczny jest *ścieżką* zasobów składającą się z co najmniej jednego składnika, który jest połączony ze sobą ogranicznikami. Wybierz znak, który ma być używany jako ogranicznik w zależności od potrzeb aplikacji, języka programowania lub struktury. Użyj wielu ograniczników dla różnych kluczy w konfiguracji aplikacji.

Poniżej przedstawiono kilka przykładów, jak można ustrukturyzować nazwy kluczy w hierarchię:

* Na podstawie usług składowych

        AppName:Service1:ApiEndpoint
        AppName:Service2:ApiEndpoint

* Na podstawie regionów wdrożenia

        AppName:Region1:DbEndpoint
        AppName:Region2:DbEndpoint

### <a name="label-keys"></a>Klucze etykiet

Wartości kluczy w konfiguracji aplikacji mogą opcjonalnie mieć atrybut Label. Etykiety służą do odróżniania wartości klucza z tym samym kluczem. Key *APP1* *z etykietami a* i *B* tworzy dwa osobne klucze w magazynie konfiguracji aplikacji. Domyślnie etykieta wartości klucza jest pusta lub `null`.

Etykieta zapewnia wygodny sposób tworzenia wariantów klucza. Typowym zastosowaniem etykiet jest określenie wielu środowisk dla tego samego klucza:

    Key = AppName:DbEndpoint & Label = Test
    Key = AppName:DbEndpoint & Label = Staging
    Key = AppName:DbEndpoint & Label = Production

### <a name="version-key-values"></a>Wartości klucza wersji

Konfiguracja aplikacji nie ma wartości klucza wersji automatycznie, ponieważ są one modyfikowane. Użyj etykiet jako sposobu tworzenia wielu wersji wartości klucza. Na przykład możesz wprowadzić numer wersji aplikacji lub identyfikator zatwierdzenia usługi Git w etykietach, aby zidentyfikować wartości kluczy skojarzone z konkretną kompilacją oprogramowania.

Możesz użyć dowolnego znaku Unicode w etykietach, z wyjątkiem `*`, `,`i `\`. Te znaki są zarezerwowane. Aby dołączyć znak zarezerwowany, należy go wypróbować za pomocą `\{Reserved Character}`.

### <a name="query-key-values"></a>Wartości klucza zapytania

Każda wartość klucza jest unikatowo identyfikowana przez jego klucz oraz etykieta, która może być `null`. Należy wykonać zapytanie dotyczące magazynu konfiguracji aplikacji, aby uzyskać kluczowe wartości przez określenie wzorca. Magazyn konfiguracji aplikacji zwraca wszystkie wartości klucza, które pasują do wzorca i odpowiadające im wartości i atrybuty. Użyj następujących wzorców kluczy w wywołaniach interfejsu API REST do konfiguracji aplikacji:

| Klucz | |
|---|---|
| Element `key` jest pomijany lub `key=*` | Pasuje do wszystkich kluczy |
| `key=abc` | Dopasowuje dokładnie nazwę klucza **ABC** |
| `key=abc*` | Pasuje do nazw kluczy, które zaczynają się od ciągu **abc** |
| `key=*abc` | Pasuje do nazw kluczy, które kończą się ciągiem **abc** |
| `key=*abc*` | Pasuje do nazw kluczy, które zawierają ciąg **abc** |
| `key=abc,xyz` | Dopasowuje nazwy kluczy **ABC** lub **XYZ**, ograniczone do pięciu CSV |

Można też uwzględnić następujące wzorce etykiet:

| Label | |
|---|---|
| Element `label` jest pomijany lub `label=*` | Dopasowuje dowolną etykietę, która zawiera `null` |
| `label=%00` | Dopasowuje etykietę `null` |
| `label=1.0.0` | Dokładnie pasuje do etykiety **1.0.0** |
| `label=1.0.*` | Pasuje do etykiet zaczynających się od **1.0.** |
| `label=*.0.0` | Pasuje do etykiet kończących się na **.0.0** |
| `label=*.0.*` | Pasuje do etykiet zawierających **.0.** |
| `label=%00,1.0.0` | Dopasowuje etykiety `null` lub **1.0.0**, ograniczone do pięciu CSV |

## <a name="values"></a>Wartości

Wartości przypisane do kluczy są również ciągami Unicode. W przypadku wartości można używać wszystkich znaków Unicode. Istnieje opcjonalny, zdefiniowany przez użytkownika typ zawartości skojarzony z każdą wartością. Ten atrybut służy do przechowywania informacji, na przykład schematu kodowania, o wartości, która ułatwia aplikacji prawidłowe przetwarzanie.

Dane konfiguracyjne przechowywane w magazynie konfiguracji aplikacji, w tym wszystkie klucze i wartości, są szyfrowane w czasie spoczynku i podczas przesyłania. Konfiguracja aplikacji nie jest rozwiązaniem zastępczym dla Azure Key Vault. Nie przechowuj w niej wpisów tajnych aplikacji.

## <a name="next-steps"></a>Kolejne kroki

* [Migawka do punktu w czasie](./concept-point-time-snapshot.md)  
* [Zarządzanie funkcjami](./concept-feature-management.md)  
