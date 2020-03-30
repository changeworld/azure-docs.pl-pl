---
title: Jak działa routing z usługą Azure Dev Spaces
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: W tym artykule opisano procesy, które zasilają usługi Azure Dev Spaces i jak działa routing
keywords: Miejsca deweloperów platformy Azure, przestrzenie deweloperów, platforma do dokowania, sieci Kubernetes, platforma Azure, usługa AKS, usługa Azure Kubernetes, kontenery
ms.openlocfilehash: e9bc1875c053335da6a8e2603406bcdb34a6dd04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241389"
---
# <a name="how-routing-works-with-azure-dev-spaces"></a>Jak działa routing z usługą Azure Dev Spaces

Usługa Azure Dev Spaces oferuje wiele sposobów szybkiego iteracji i debugowania aplikacji Kubernetes oraz współpracy z zespołem nad klastrem usługi Azure Kubernetes (AKS). Gdy projekt jest uruchomiony w przestrzeni deweloperów, usługa Azure Dev Spaces zapewnia dodatkowe możliwości sieci i routingu dla projektu.

W tym artykule opisano sposób działania routingu z przestrzeniami deweloperskimi.

## <a name="how-routing-works"></a>Jak działa routing

Przestrzeń deweloperska jest zbudowana na podstawie systemu AKS i używa tych [samych koncepcji sieciowych.](../aks/concepts-network.md) Usługa Azure Dev Spaces ma również scentralizowaną usługę *obsługi przychodzącej* i wdraża własny kontroler transferu danych przychodzących w klastrze AKS. Usługa *obsługi przychodzącej* monitoruje klastry AKS za pomocą przestrzeni deweloperskich i powiększa kontroler transferu danych przychodzących azure dev spaces w klastrze za pomocą obiektów transferu danych przychodzących do routingu do zasobników aplikacji. Devspaces-proxy kontenera w każdym `azds-route-as` zasobniku dodaje nagłówek HTTP dla ruchu HTTP do obszaru deweloperskiego na podstawie adresu URL. Na przykład żądanie do *http://azureuser.s.default.serviceA.fedcba09...azds.io* adresu URL otrzyma `azds-route-as: azureuser`nagłówek HTTP z . Devspaces-kontener serwera proxy nie `azds-route-as` doda nagłówka, jeśli jest już obecny.

Gdy żądanie HTTP jest składane do usługi spoza klastra, żądanie przechodzi do kontrolera transferu danych przychodzących. Kontroler transferu danych przychodzących kieruje żądanie bezpośrednio do odpowiedniego zasobnika na podstawie jego obiektów i reguł przychodzących. Devspaces-proxy kontenera w zasobniku odbiera `azds-route-as` żądanie, dodaje nagłówek na podstawie adresu URL, a następnie kieruje żądanie do kontenera aplikacji.

Gdy żądanie HTTP jest składane do usługi z innej usługi w klastrze, żądanie najpierw przechodzi przez kontener devspaces-proxy usługi wywołującej. Kontener devspaces-proxy analizuje żądanie HTTP i `azds-route-as` sprawdza nagłówek. Na podstawie nagłówka kontener devspaces-proxy wyszukuje adres IP usługi skojarzonej z wartością nagłówka. Jeśli zostanie znaleziony adres IP, devspaces-proxy kontenera przekieruje żądanie do tego adresu IP. Jeśli adres IP nie zostanie znaleziony, devspaces-proxy kontenera kieruje żądanie do kontenera aplikacji nadrzędnej.

Na przykład aplikacje *serviceA* i *serviceB* są wdrażane w nadrzędnym obszarze deweloperskim o nazwie *default*. *serviceA* opiera się na *serviceB* i sprawia, że wywołania HTTP do niego. Użytkownik platformy Azure tworzy przestrzeń deweloperską podrzędną na podstawie *domyślnej* przestrzeni o nazwie *azureuser*. Użytkownik platformy Azure wdraża również własną wersję *usługiA* w miejscu podrzędnym. W przypadku złożenia *http://azureuser.s.default.serviceA.fedcba09...azds.io*wniosku do:

![Routing usługi Azure Dev Spaces](media/how-dev-spaces-works/routing.svg)

1. Kontroler transferu danych przychodzących wyszukuje adres IP zasobnika skojarzonego z adresem URL, którym jest *serviceA.azureuser*.
1. Kontroler transferu danych przychodzących znajduje adres IP zasobnika w przestrzeni deweloperów użytkownika platformy Azure i kieruje żądanie do *zasobnika serviceA.azureuser.*
1. Devspaces-proxy kontener w *serviceA.azureuser* zasobnika odbiera `azds-route-as: azureuser` żądanie i dodaje jako nagłówek HTTP.
1. Kontener devspaces-proxy w *zasobniku serviceA.azureuser* kieruje żądanie do kontenera aplikacji *serviceA* w *zasobniku serviceA.azureuser.*
1. *ServiceA* aplikacji w *serviceA.azureuser* pod sprawia, że wywołanie *serviceB*. Aplikacja *serviceA* zawiera również kod, `azds-route-as` aby zachować istniejący nagłówek, który w tym przypadku jest `azds-route-as: azureuser`.
1. Devspaces-proxy kontenera w *serviceA.azureuser* zasobnika odbiera żądanie i wyszukuje adres `azds-route-as` IP *serviceB* na podstawie wartości nagłówka.
1. Kontener devspaces-proxy w *witrynie serviceA.azureuser* nie znajduje adresu IP dla *serviceB.azureuser*.
1. Kontener devspaces-proxy w *witrynie serviceA.azureuser* wyszukuje adres IP dla *serviceB* w przestrzeni nadrzędnej, czyli *serviceB.default*.
1. Devspaces-proxy kontener w *serviceA.azureuser* zasobnika znajduje adres IP dla *serviceB.default* i kieruje żądanie do *serviceB.default* pod.
1. Kontener devspaces-proxy w *serviceB.default* pod odbiera żądanie i kieruje żądanie do kontenera aplikacji *serviceB* w *serviceB.default* pod.
1. *ServiceB* aplikacji w *serviceB.default* zasobnika zwraca odpowiedź do *serviceA.azureuser* pod.
1. Kontener devspaces-proxy w *zasobniku serviceA.azureuser* odbiera odpowiedź i kieruje odpowiedź na kontener aplikacji *serviceA* w *zasobniku serviceA.azureuser.*
1. Aplikacja *serviceA* odbiera odpowiedź, a następnie zwraca własną odpowiedź.
1. Kontener devspaces-proxy w *zasobniku serviceA.azureuser* odbiera odpowiedź z kontenera aplikacji *serviceA* i kieruje odpowiedź do oryginalnego obiektu wywołującego poza klastrem.

Wszystkie inne ruchu TCP, który nie jest HTTP przechodzi przez kontrolera transferu danych przychodzących i devspaces-proxy kontenerów niezmodyfikowanych.

## <a name="sharing-a-dev-space"></a>Udostępnianie przestrzeni deweloperów

Podczas pracy z zespołem można [udostępnić przestrzeń dewelopera w całym zespole](how-to/share-dev-spaces.md) i utworzyć pochodne przestrzenie deweloperów. Miejsca dewelopera mogą być używane przez każdego, kto ma dostęp współautora do grupy zasobów obszaru dewelopera.

Można również utworzyć nową przestrzeń dewelopera, która pochodzi z innej przestrzeni deweloperów. Podczas tworzenia obszaru deweloperów pochodnych etykieta *azds.io/parent-space=PARENT-SPACE-NAME* jest dodawana do przestrzeni nazw obszaru dewelopera pochodnego. Ponadto wszystkie aplikacje z nadrzędnego obszaru dewelopera są współużytkowane z pochodną przestrzeni deweloperów. Jeśli wdrożysz zaktualizowaną wersję aplikacji do obszaru deweloperów pochodnych, będzie ona istnieć tylko w pochodnym obszarze deweloperskim, a nadrzędna przestrzeń deweloperska pozostanie nienaruszona. Może mieć maksymalnie trzy poziomy wypoziomów deweloperskich lub *przestrzeniach dziadków.*

Pochodna przestrzeń dewelopera będzie również inteligentnie kierować żądania między własnymi aplikacjami i aplikacjami udostępnionymi przez jego element nadrzędny. Routing działa przez próbę kierowania żądania do aplikacji w pochodnym przestrzeni dewelopera i powrót do udostępnionej aplikacji z nadrzędnego miejsca dewelopera. Routing powróci do udostępnionej aplikacji w przestrzeni nadrzędnej, jeśli aplikacja nie znajduje się w przestrzeni nadrzędnej.

Przykład:
* *Domyślnie* miejsca dewelopera ma aplikacje *serviceA* i *serviceB*.
* Dev space *azureuser* jest pochodną *domyślnej*.
* Zaktualizowana wersja *usługiA* jest wdrażana na *platformie azureuser*.

Podczas korzystania z *azureuser,* wszystkie żądania do *serviceA* będą kierowane do zaktualizowanej wersji w *azureuser*. Żądanie *serviceB* najpierw spróbuje być kierowane do *wersji azureuser* *serviceB*. Ponieważ nie istnieje, zostanie przekierowany do *domyślnej* wersji *serviceB*. Jeśli wersja *usługi azureuser* zostanie usunięta, wszystkie żądania do *serviceA* powrócą do korzystania z *domyślnej* wersji *serviceA*. *serviceA*

## <a name="next-steps"></a>Następne kroki

Aby zobaczyć kilka przykładów użycia routingu przez usługę Azure Dev Spaces w celu zapewnienia szybkiej iteracji i rozwoju, zobacz Jak działa łączenie komputera deweloperskiego z [przestrzenią deweloperską][how-it-works-connect], [Jak zdalnie debugowanie kodu za pomocą usługi Azure Dev Spaces działa,][how-it-works-remote-debugging]oraz [Akcje Usługi GitHub & usłudze Azure Kubernetes.][pr-flow]

Aby rozpocząć korzystanie z routingu z usługą Azure Dev Spaces do tworzenia zespołów, zobacz rozwój zespołu w przewodniku Szybki start [usługi Azure Dev Spaces.][quickstart-team]

[helm-upgrade]: https://helm.sh/docs/intro/using_helm/#helm-upgrade-and-helm-rollback-upgrading-a-release-and-recovering-on-failure
[how-it-works-connect]: how-dev-spaces-works-connect.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[pr-flow]: how-to/github-actions.md
[quickstart-team]: quickstart-team-development.md
[troubleshooting]: troubleshooting.md