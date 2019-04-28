---
title: Plik dyrektywy include
description: Plik dyrektywy include
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: include
manager: yuvalm
ms.openlocfilehash: 40c1be20df845b975c023616e38cbb932c985735
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60368258"
---
# <a name="team-development-with-azure-dev-spaces"></a>Programowanie zespołowe w usłudze Azure Dev Spaces

Z tego samouczka dowiesz się, jak zespół deweloperów może równocześnie współpracować w tym samym klastrze Kubernetes przy użyciu usługi Dev Spaces.

## <a name="learn-about-team-development"></a>Więcej informacji na temat programowania zespołowego
Do tej pory kod aplikacji był uruchamiany tak, jakby nad aplikacją pracował tylko jeden deweloper. W tej sekcji dowiesz się, jak usługa Azure Dev Spaces upraszcza programowanie zespołowe:
* Pozwól zespołowi deweloperów pracować w tym samym środowisku: w zależności od potrzeb w udostępnionej przestrzeni deweloperskiej lub w odrębnych przestrzeniach deweloperskich.
* Środowisko umożliwia każdemu deweloperowi iterację na jego kodzie w izolacji i bez obaw o przeszkadzanie pozostałym.
* Przed zatwierdzeniem kodu można go kompleksowo przetestować bez konieczności tworzenia makiet czy symulowania zależności.

### <a name="challenges-with-developing-microservices"></a>Problemy z tworzeniem mikrousług
W tej chwili przykładowa aplikacja nie jest zbyt skomplikowana. Jednak w rzeczywistym środowisku deweloperskim w miarę dodawania kolejnych usług i rozwoju zespołu programistycznego szybko pojawiają się problemy. Uruchomienie ich wszystkich lokalnie na potrzeby programowania może okazać się nierealne.

* Maszyna deweloperska może nie mieć wystarczających zasobów, aby uruchamiać jednocześnie wszystkie potrzebne usługi.
* W przypadku niektórych usług może być wymagana dostępność publiczna. Na przykład w usłudze może być wymagany punkt końcowy, który odpowiada na element webhook.
* Jeśli chcesz uruchomić podzestaw usług, musisz znać pełną hierarchię zależności między wszystkimi swoimi usługami. Określenie tej hierarchii może być trudne, szczególnie, jeśli liczba usług się zwiększa.
* Niektórzy deweloperzy uciekają się do symulowania lub budowania makiet wielu zależności swoich usług. Takie podejście może pomóc, jednak zarządzanie tymi makietami może szybko zwiększyć koszt programowania. Ponadto takie podejście prowadzi do tego, że środowisko deweloperskie jest inne, niż środowisko produkcyjne, co może prowadzić do występowania drobnych błędów.
* Z tego względu przeprowadzenie jakiegokolwiek testu integracji staje się trudne. Testowanie integracji realnie może nastąpić po zatwierdzeniu, co oznacza, że problemy pojawią się na dalszych etapach cyklu programowania.

    ![](../articles/dev-spaces/media/common/microservices-challenges.png)

### <a name="work-in-a-shared-dev-space"></a>Praca w udostępnionej przestrzeni deweloperskiej
W usłudze Azure Dev Spaces możesz skonfigurować *udostępnione* przestrzenie deweloperskie na platformie Azure. Każdy deweloper może skupić się wyłącznie na swojej części aplikacji oraz iteracyjnie tworzyć *wstępnie zatwierdzony kod* w przestrzeni deweloperskiej zawierającej już wszystkie pozostałe usługi i zasoby w chmurze, od których zależą jego scenariusze. Zależności są zawsze aktualne, a deweloperzy pracują w sposób odzwierciedlający środowisko produkcyjne.

### <a name="work-in-your-own-space"></a>Praca we własnym obszarze
Kiedy tworzysz kod dla swojej usługi i nie jest on jeszcze gotowy do zaewidencjonowania, często znajduje się on w niezbyt dobrym stanie. Wciąż w sposób iteracyjny go kształtujesz, testujesz i eksperymentujesz z rozwiązaniami. Usługa Azure Dev Spaces wprowadza pojęcie **miejsca**, które umożliwia pracę w izolacji i bez obaw o przeszkadzanie członkom zespołu.

## <a name="use-dev-spaces-for-team-development"></a>Programowanie zespołowe za pomocą usługi Dev Spaces
Przedstawmy te pomysły za pomocą konkretnego przykładu, używając naszej przykładowej aplikacji *webfrontend* -> *mywebapi*. Wyobraźmy sobie scenariusz, w którym programista Scott musi wprowadzić zmianę w usłudze *mywebapi* — i *tylko* w tej usłudze. Usługa *webfrontend* nie musi być zmieniana w ramach aktualizacji Scotta.

_Bez_ używania usługi Dev Spaces Scott miałby kilka sposobów na opracowanie i przetestowanie swojej aktualizacji, jednak żaden z nich nie jest idealny:
* Uruchomienie WSZYSTKICH składników w środowisku lokalnym. Wymaga to bardziej zaawansowanej maszyny deweloperskiej z zainstalowaną platformą Docker i potencjalnie z rozwiązaniem MiniKube.
* Uruchomienie WSZYSTKICH składników w izolowanej przestrzeni nazw w klastrze Kubernetes. Ponieważ usługa *webfrontend* nie zmienia się, jest to marnowanie zasobów klastra.
* Uruchomienie TYLKO usługi *mywebapi* i wykonanie ręcznych wywołań REST w celu testowania. Nie powoduje to testowania całego przepływu, od początku do końca.
* Dodanie do usługi *webfrontend* skoncentrowanego kodu, który umożliwia deweloperowi wysyłanie żądań do innego wystąpienia usługi *mywebapi*. Komplikuje to działanie usługi *webfrontend*.

### <a name="set-up-your-baseline"></a>Konfigurowanie punktu odniesienia
Najpierw musimy wdrożyć punkt odniesienia naszych usług. To wdrożenie będzie reprezentować „ostatni znany dobry” punkt, aby można było łatwo porównać zachowanie kodu lokalnego z wersją zaewidencjonowaną. Następnie, na podstawie tego punktu odniesienia, utworzymy przestrzeń podrzędną, aby umożliwić testowanie zmian w usłudze *mywebapi* w kontekście większej aplikacji.

1. Sklonuj [przykładową aplikację usługi Dev Spaces](https://github.com/Azure/dev-spaces): `git clone https://github.com/Azure/dev-spaces && cd dev-spaces`
1. Wyewidencjonuj gałąź zdalną *azds_updates*: `git checkout -b azds_updates origin/azds_updates`
1. Wybierz przestrzeń _dev_: `azds space select --name dev`. Po wyświetleniu monitu o wybranie nadrzędnej przestrzeni deweloperskiej wybierz pozycję _\<none\>_.
1. Przejdź do katalogu _mywebapi_, a następnie wykonaj: `azds up -d`
1. Przejdź do katalogu _webfrontend_, a następnie wykonaj: `azds up -d`
1. Wykonaj `azds list-uris`, aby wyświetlić publiczny punkt końcowy usługi _webfrontend_

> [!TIP]
> Powyższe kroki pozwalają ręcznie skonfigurować punkt odniesienia, jednak zalecamy, aby zespoły używały ciągłej integracji/ciągłego wdrażania w celu automatycznego aktualizowania punktu odniesienia zgodnie z zatwierdzonym kodem.
>
> Zapoznaj się z naszym [przewodnikiem konfigurowania ciągłej integracji/ciągłego wdrażania za pomocą usługi Azure DevOps](../articles/dev-spaces/how-to/setup-cicd.md), aby utworzyć przepływ pracy podobny do przedstawionego na poniższym diagramie.
>
> ![Przykładowy diagram ciągłej integracji/ciągłego wdrażania](../articles/dev-spaces/media/common/ci-cd-complex.png)

W tym momencie punkt odniesienia powinien działać. Uruchom polecenie `azds list-up --all`. Zobaczysz dane wyjściowe podobne do poniższych:

```
$ azds list-up --all

Name                          DevSpace  Type     Updated  Status
----------------------------  --------  -------  -------  -------
mywebapi                      dev       Service  3m ago   Running
mywebapi-56c8f45d9-zs4mw      dev       Pod      3m ago   Running
webfrontend                   dev       Service  1m ago   Running
webfrontend-6b6ddbb98f-fgvnc  dev       Pod      1m ago   Running
```

Kolumna DevSpace pokazuje, że obie usługi zostały uruchomione w przestrzeni o nazwie _dev_. Każda osoba otwierająca publiczny adres URL i przechodząca do aplikacji internetowej wywoła zaewidencjonowaną ścieżkę kodu, która zostanie uruchomiona w obydwu usługach. Teraz załóżmy, że chcesz kontynuować tworzenie aplikacji _mywebapi_. Jak możesz wprowadzać zmiany kodu i testować je, aby nie przerywać pracy innych deweloperów, którzy używają środowiska deweloperskiego? W tym celu skonfigurujesz własne miejsce.

### <a name="create-a-dev-space"></a>Tworzenie przestrzeni deweloperskiej
Aby uruchomić własną wersję aplikacji _mywebapi_ w przestrzeni innej niż _dev_, możesz utworzyć własną przestrzeń za pomocą następującego polecenia:

```cmd
azds space select --name scott
```

Po wyświetleniu monitu wybierz opcję _dev_ jako **nadrzędną przestrzeń deweloperską**. Oznacza to, że nasza nowa przestrzeń, _dev/scott_, będzie pochodzić z przestrzeni _dev_. Wkrótce zobaczymy, jak pomoże nam to podczas testowania.

W naszym hipotetycznym wprowadzeniu używaliśmy nazwy _scott_ dla nowej przestrzeni, aby współpracownicy mogli zidentyfikować pracującą w niej osobę. Można jej jednak nadać dowolną nazwę i zastosować elastyczne podejście do jej znaczenia, używając nazw, takich jak _sprint4_ lub _demo_. W każdej sytuacji opcja _dev_ służy jako punkt odniesienia dla wszystkich deweloperów nad elementem tej aplikacji:

![](../articles/dev-spaces/media/common/ci-cd-space-setup.png)

Uruchom polecenie `azds space list`, aby wyświetlić listę wszystkich przestrzeni w środowisku deweloperskim. Kolumna _Wybrane_ wskazuje, która przestrzeń została aktualnie wybrana (true/false). W Twoim przypadku przestrzeń o nazwie _dev/scott_ została automatycznie wybrana po utworzeniu. W dowolnym momencie możesz wybrać inną przestrzeń, używając polecenia `azds space select`.

Zobaczmy, jak to działa.