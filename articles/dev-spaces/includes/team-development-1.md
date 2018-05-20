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
ms.openlocfilehash: 96a749c0cb59759e9294f52bd4f631d7fdc2275f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
Do tej pory działała kodu aplikacji tak, jakby był tylko używająca w aplikacji. W tej sekcji dowiesz się, jak spacje deweloperów Azure upraszcza programowanie zespołowe:
* Włącz zespół deweloperów do pracy w tym samym środowisku programistycznym.
* Obsługuje wszystkich deweloperów iteracja na kodzie w izolacji i bez obawy złamanie innych użytkowników.
* Testowanie kodu na całej trasie, przed zatwierdzania kodu bez konieczności tworzenia mocks lub symulować zależności.

## <a name="challenges-with-developing-microservices"></a>Problemy z opracowywanie mikrousług
Przykładowej aplikacji nie jest bardzo skomplikowane, w tej chwili. Jednak w rozwoju rzeczywistych wyzwania wyłonić szybko dodać więcej usług i rozwoju zespół deweloperów.

Obraz użytkownika pracy z usługą, która współdziała z wielu innych usług.

- Może stać się wypadku do uruchomienia wszystkich lokalnie na rozwój rozwiązań. Komputerze deweloperskim może nie mieć wystarczającej ilości zasobów, aby uruchomić całej aplikacji. Lub, być może aplikacja ma punkty końcowe, które muszą być publicznie dostępny (na przykład aplikację odpowiada elementu webhook z aplikacji SaaS).

- Będzie można uruchamiać tylko zależne od usług, ale oznacza to, że konieczne będzie wiedzieć pełne zamknięcie zależności (na przykład zależności zależności). Lub jest to kwestia nie otrzymuje informacji o tym jak skompilować i uruchomić zależności, ponieważ nie działa na nich.
- Niektórzy deweloperzy odwołać się do symulowania lub mocking się wiele zależności usługi. Takie podejście może pomóc czasami, ale zarządzanie tymi mocks może szybko przybrać wysiłków programowanie. Plus, takie podejście prowadzi do deweloperów środowiska wyszukiwania bardzo różnią się w środowisku produkcyjnym i subtelnych błędów można pełzanie.
- Wynika, że dowolnego typu end-to-end testowania czynności staje się trudny. Testowanie integracji tylko realistycznie możliwe po zatwierdzania, co oznacza, że Zobacz problemów później w cyklu tworzenia.

![](../media/common/microservices-challenges.png)


## <a name="work-in-a-shared-development-environment"></a>Praca w środowisku projektowym udostępnionego
Spacjami deweloperów platformy Azure, możesz skonfigurować *udostępnionego* środowisko projektowe na platformie Azure. Każdy deweloper może skupić się na ich części aplikacji i wielokrotnie powtarzane można programować *wstępnie zatwierdzisz kod* w środowisku, które zawiera już wszystkie usługi i które ich scenariusze są zależne od zasobów w chmurze. Zależności są zawsze aktualne i deweloperów pracuje w taki sposób, który odzwierciedla produkcji.

## <a name="work-in-your-own-space"></a>Praca w swoim obszarze
Opracuj kodu dla usługi, a przed możesz przystąpić do sprawdzenia w kodzie często nie będzie w dobrym stanie. Nadal wielokrotnie powtarzane Cię, kształtowania, przetestowaniu i wykorzystywanie rozwiązania. Spacje deweloperów platformy Azure wprowadza pojęcie **miejsca**, który umożliwia pracę w izolacji i bez obawy złamanie członków zespołu.

> [!Note]
> Aby kontynuować, zamknij wszystkie okna kodzie VS dla obu usług, a następnie uruchom `azds up -d` w poszczególnych folderów głównych usługi. (Jest to ograniczenie wersja zapoznawcza).

Spójrzmy bliższe spojrzenie na którym usługi są aktualnie uruchomione. Uruchom `azds list` polecenie, aby zobaczyć dane wyjściowe podobne do następujących:

```
Name         Space     Chart              Ports   Updated     Access Points
-----------  --------  -----------------  ------  ----------  -------------------------
mywebapi     default  mywebapi-0.1.0     80/TCP  2m ago     <not attached>
webfrontend  default  webfrontend-0.1.0  80/TCP  1m ago     https://webfrontend-contosodev.1234abcdef.westeurope.aksapp.io
```

Kolumna miejsca pokazuje, że obie usługi są uruchomione w miejscu o nazwie `default`. Każdy, kto otwiera publiczny adres URL i przechodzi do aplikacji sieci web wywoła ścieżkę kodu uprzednio zapisanego uruchamiana za pomocą obu usług. Teraz załóżmy, że chcesz kontynuować tworzenie `mywebapi`. Jak można wprowadzać zmiany kodu i je przetestować i nie przerywają pracy inni deweloperzy, którzy używają środowiska deweloperskiego? W tym celu należy skonfigurować własne miejsca.

## <a name="create-a-space"></a>Utwórz obszar
Do uruchomienia własnej wersji `mywebapi` w miejscu innym niż `default`, można utworzyć własne miejsce za pomocą następującego polecenia:

``` 
azds space create --name scott
```

W powyższym przykładzie tak, aby zidentyfikować Moje partnerom, które jest miejsce pracy w mojej nazwy nowe miejsce wcześniej używanych, ale można go wywoływać niczego, takich jak i elastyczna o znaczeniu, takie jak "sprint4" lub "demo."

Uruchom `azds space list` polecenie, aby wyświetlić listę wszystkich spacji w środowisku testowym. Znak gwiazdki (*) widoczna obok aktualnie zaznaczonego miejsca. W Twoim przypadku miejsca o nazwie "scott" wybrano automatycznie podczas jej tworzenia. W dowolnym momencie można wybrać innej przestrzeni `azds space select` polecenia.
