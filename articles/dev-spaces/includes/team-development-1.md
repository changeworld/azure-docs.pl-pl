---
title: Plik dyrektywy include
description: Plik dyrektywy include
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: 23b5373f4986c4a3d113baebe9e04ce65b9a9df0
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39062903"
---
Do tej pory kod aplikacji był uruchamiany tak, jakby nad aplikacją pracował tylko jeden deweloper. W tej sekcji dowiesz się, jak usługa Azure Dev Spaces upraszcza programowanie zespołowe:
* Pozwól zespołowi deweloperów pracować w tym samym środowisku: w zależności od potrzeb w udostępnionej przestrzeni deweloperskiej lub w odrębnych przestrzeniach deweloperskich.
* Środowisko umożliwia każdemu deweloperowi iterację na jego kodzie w izolacji i bez obaw o przeszkadzanie pozostałym.
* Przed zatwierdzeniem kodu można go kompleksowo przetestować bez konieczności tworzenia makiet czy symulowania zależności.

### <a name="challenges-with-developing-microservices"></a>Problemy z tworzeniem mikrousług
W tej chwili przykładowa aplikacja nie jest zbyt skomplikowana. Jednak w rzeczywistym środowisku deweloperskim w miarę dodawania kolejnych usług i rozwoju zespołu programistycznego szybko pojawiają się problemy.

Wyobraź sobie, że pracujesz nad usługą, która współpracuje z dziesiątkami innych usług.

- Uruchomienie ich wszystkich lokalnie na potrzeby programowania może okazać się nierealne. Twoja maszyna deweloperska może nie mieć wystarczającej ilości zasobów, aby uruchomić całą aplikację. A może aplikacja ma punkty końcowe, które muszą być publicznie osiągalne (na przykład aplikacja reaguje na element webhook z aplikacji SaaS).

- Możesz podjąć próbę uruchomienia tylko usług, od których zależysz, ale oznacza to, że musisz znać pełny zestaw zależności (na przykład zależności zależności). Być może jest to po prostu kwestia niewiedzy, jak kompilować i uruchamiać zależności, ponieważ jeszcze nie były one przez Ciebie używane.
- Niektórzy deweloperzy uciekają się do symulowania lub budowania makiet wielu zależności swoich usług. Czasami takie podejście może pomóc, ale zarządzanie tymi makietami może szybko zabrać czas potrzebny na programowanie. Ponadto przestrzeń deweloperska będzie wówczas bardzo różniła się od produkcyjnej, co może doprowadzić do pojawienia się drobnych błędów.
- Z tego względu dowolny kompleksowy test staje się trudny. Testowanie integracji realnie może nastąpić po zatwierdzeniu, co oznacza, że problemy pojawią się na dalszych etapach cyklu programowania.

![](../media/common/microservices-challenges.png)


### <a name="work-in-a-shared-dev-space"></a>Praca w udostępnionej przestrzeni deweloperskiej
W usłudze Azure Dev Spaces możesz skonfigurować *udostępnione* przestrzenie deweloperskie na platformie Azure. Każdy deweloper może skupić się wyłącznie na swojej części aplikacji oraz iteracyjnie tworzyć *wstępnie zatwierdzony kod* w przestrzeni deweloperskiej zawierającej już wszystkie pozostałe usługi i zasoby w chmurze, od których zależą jego scenariusze. Zależności są zawsze aktualne, a deweloperzy pracują w sposób odzwierciedlający środowisko produkcyjne.

### <a name="work-in-your-own-space"></a>Praca we własnym obszarze
Kiedy tworzysz kod dla swojej usługi i nie jest on jeszcze gotowy do zaewidencjonowania, często znajduje się on w niezbyt dobrym stanie. Wciąż w sposób iteracyjny go kształtujesz, testujesz i eksperymentujesz z rozwiązaniami. Usługa Azure Dev Spaces wprowadza pojęcie **miejsca**, które umożliwia pracę w izolacji i bez obaw o przeszkadzanie członkom zespołu.

> [!Note]
> Zanim przejdziesz dalej, zamknij wszystkie okna programu VS Code dla obu usług, a następnie uruchom polecenie `azds up -d` w każdym z folderów głównych usługi. (Jest to ograniczenie wersji zapoznawczej).

Przyjrzyjmy się, gdzie obecnie są uruchamiane usługi. Uruchom polecenie `azds list-up`. Zobaczysz dane wyjściowe podobne do poniższych:

```
Name         Space     Chart              Ports   Updated     Access Points
-----------  --------  -----------------  ------  ----------  -------------------------
mywebapi     default  mywebapi-0.1.0     80/TCP  2m ago     <not attached>
webfrontend  default  webfrontend-0.1.0  80/TCP  1m ago     http://webfrontend-contosodev.1234abcdef.eastus.aksapp.io
```

Kolumna Przestrzeń pokazuje, że obie usługi są uruchomione w przestrzeni o nazwie `default`. Każda osoba otwierająca publiczny adres URL i przechodząca do aplikacji internetowej wywoła napisaną przez Ciebie wcześniej ścieżkę kodu, która uruchomi obie usługi. Teraz załóżmy, że chcesz kontynuować tworzenie aplikacji `mywebapi`. Jak możesz wprowadzać zmiany kodu i testować je, aby nie przerywać pracy innych deweloperów, którzy używają środowiska deweloperskiego? W tym celu skonfigurujesz własne miejsce.

### <a name="create-a-dev-space"></a>Tworzenie przestrzeni deweloperskiej
Aby uruchomić własną wersję aplikacji `mywebapi` w przestrzeni innej niż `default`, możesz utworzyć własną przestrzeń za pomocą następującego polecenia:

``` 
azds space select --name scott
```

Po wyświetleniu monitu wybierz opcję `default` jako **nadrzędną przestrzeń deweloperską**. Oznacza to, że nasza nowa przestrzeń, `default/scott`, będzie wywodzić się z przestrzeni `default`. Wkrótce zobaczymy, jak pomoże nam to podczas testowania. 

W powyższym przykładzie została użyta moja nazwa nowej przestrzeni, która jest łatwa do zidentyfikowania dla moich partnerów pracujących w tym samym miejscu, ale możesz nazwać ją w dowolny sposób, który coś oznacza, na przykład „sprint4” czy „demo”.

Uruchom polecenie `azds space list`, aby wyświetlić listę wszystkich przestrzeni w środowisku deweloperskim. Obok aktualnie wybranej przestrzeni jest wyświetlana gwiazdka (*). W Twoim przypadku przestrzeń o nazwie „default/scott” została wybrana automatycznie po jej utworzeniu. W dowolnym momencie możesz wybrać inną przestrzeń, używając polecenia `azds space select`.
