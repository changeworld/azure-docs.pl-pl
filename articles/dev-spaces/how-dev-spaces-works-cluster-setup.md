---
title: Jak działa konfigurowanie klastra dla usługi Azure Dev Spaces
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: W tym artykule opisano, jak działa konfigurowanie klastra usługi Azure Kubernetes dla usługi Azure Dev Spaces
keywords: Miejsca deweloperów platformy Azure, przestrzenie deweloperów, platforma do dokowania, sieci Kubernetes, platforma Azure, usługa AKS, usługa Azure Kubernetes, kontenery
ms.openlocfilehash: 00f8262f3008ce9ba82726960f78d18395458a2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241727"
---
# <a name="how-setting-up-a-cluster-for-azure-dev-spaces-works"></a>Jak działa konfigurowanie klastra dla usługi Azure Dev Spaces

Usługa Azure Dev Spaces oferuje wiele sposobów szybkiego iteracji i debugowania aplikacji Kubernetes oraz współpracy z zespołem nad klastrem usługi Azure Kubernetes (AKS). Jednym ze sposobów jest włączenie usługi Azure Dev Spaces w klastrze AKS, dzięki czemu można [uruchamiać usługi bezpośrednio w klastrze][how-it-works-up] i korzystać z [dodatkowych funkcji sieciowych i routingu.][how-it-works-routing] W tym artykule opisano, co się dzieje podczas przygotowywania klastra i włączania usługi Azure Dev Spaces.

## <a name="prepare-your-aks-cluster"></a>Przygotowanie klastra AKS

Aby przygotować klaster usługi AKS dla obszarów deweloperskich, sprawdź, czy klaster usługi AKS znajduje się w regionie [obsługiwanym przez usługi Azure Dev Spaces][supported-regions] i używasz aplikacji Kubernetes 1.10.3 lub nowszej. Aby włączyć usługę Azure Dev Spaces w klastrze z witryny Azure portal, przejdź do klastra, kliknij pozycję *Miejsca deweloperów,* zmień *pozycję Użyj miejsca dewelopera* na *Tak*i kliknij przycisk *Zapisz*. Można również włączyć usługi Azure Dev Spaces `az aks use-dev-spaces`z interfejsu wiersza polecenia platformy Azure, uruchamiając program .

Na przykład konfigurowanie klastra AKS dla obszarów deweloperskich zobacz [szybki start rozwoju zespołu][quickstart-team].

Gdy usługa Azure Dev Spaces jest włączona w klastrze AKS, instaluje kontroler dla klastra. Kontroler znajduje się poza klastrem AKS. Napędza zachowanie i komunikację między narzędziami po stronie klienta a klastrem AKS. Po włączeniu można wchodzić w interakcje z kontrolerem za pomocą narzędzi po stronie klienta.

Kontroler wykonuje następujące akcje:

* Zarządza tworzeniem i zaznaczaniem przestrzeni deweloperów.
* Instaluje wykres Helm aplikacji i tworzy obiekty Kubernetes.
* Tworzy obraz kontenera aplikacji.
* Wdraża aplikację do usługi AKS.
* Wykonuje przyrostowe kompilacje i restartuje po zmianie kodu źródłowego.
* Zarządza dziennikami i śladami HTTP.
* Przekazuje stdout i stderr do narzędzi po stronie klienta.
* Konfiguruje routing dla aplikacji w obrębie przestrzeni, a także w przestrzeni nadrzędnej i podrzędnej.

Kontroler jest oddzielnym zasobem platformy Azure poza klastrem i wykonuje następujące czynności z zasobami w klastrze:

* Tworzy lub wyznacza obszar nazw Kubernetes do użycia jako obszar dewelopera.
* Usuwa wszystkie obszary nazw Kubernetes o nazwie *azds*, jeśli istnieje, i tworzy nowy.
* Wdraża konfigurację elementu webhook kubernetes.
* Wdraża serwer dostępu do elementu webhook.

Używa tej samej jednostki usługi, której klaster AKS używa do wykonywania wywołań usługi do innych składników usługi Azure Dev Spaces.

![Usługa Azure Dev Spaces przygotowuje klaster](media/how-dev-spaces-works/prepare-cluster.svg)

Aby można było korzystać z usługi Azure Dev Spaces, musi istnieć co najmniej jedno miejsce dewelopera. Usługa Azure Dev Spaces używa obszarów nazw usługi Kubernetes w klastrze AKS do tworzenia przestrzeni. Po zainstalowaniu kontrolera monituje o utworzenie nowego obszaru nazw Kubernetes lub wybranie istniejącego obszaru nazw, który będzie używany jako pierwszy obszar deweloperów. Domyślnie kontroler oferuje uaktualnienie istniejącego *domyślnego* obszaru nazw Kubernetes do pierwszego obszaru deweloperów.

Gdy obszar nazw jest wyznaczony jako obszar dewelopera, kontroler dodaje etykietę *azds.io/space=true* do tego obszaru nazw, aby zidentyfikować go jako obszar dewelopera. Początkowa przestrzeń deweloperska, którą tworzysz lub wyznaczasz, jest wybierana domyślnie po przygotowaniu klastra. Po wybraniu miejsca jest on używany przez usługę Azure Dev Spaces do tworzenia nowych obciążeń.

Narzędzia po stronie klienta umożliwiają tworzenie nowych przestrzeni deweloperskich i usuwanie istniejących przestrzeni deweloperskich. Ze względu na ograniczenie w umięśne, nie można usunąć *domyślnego* miejsca dewelopera. Kontroler usuwa również wszystkie istniejące obszary nazw Kubernetes o nazwie *azds,* aby uniknąć konfliktów z `azds` poleceniem używanym przez narzędzia po stronie klienta.

Serwer dostępu do łączy webhook kubernetes służy do wstrzykiwania zasobników z trzema kontenerami podczas wdrażania do instrumentacji: kontenera devspaces-proxy, kontenera devspaces-proxy-init i kontenera devspaces-build. **Wszystkie trzy z tych kontenerów są uruchamiane z dostępem administratora w klastrze AKS.** Używają również tej samej jednostki usługi, której klaster AKS używa do nawiązywać wywołania usługi do innych składników usługi Azure Dev Spaces.

![Serwer dostępu do witryny Azure Dev Spaces Kubernetes webhook](media/how-dev-spaces-works/kubernetes-webhook-admission-server.svg)

Kontener devspaces-proxy jest kontenerem sidecar, który obsługuje cały ruch TCP do i z kontenera aplikacji i pomaga w routingu. Kontener devspaces-proxy przekieruje przekierowania wiadomości HTTP, jeśli używane są określone spacje. Na przykład może pomóc w kierowaniu wiadomości HTTP między aplikacjami w przestrzeni nadrzędnej i podrzędnej. Cały ruch nie-HTTP przechodzi przez devspaces-proxy niezmodyfikowany. Kontener devspaces-proxy rejestruje również wszystkie przychodzące i wychodzące wiadomości HTTP i wysyła je do narzędzi po stronie klienta jako ślady. Te ślady mogą być następnie wyświetlane przez dewelopera, aby sprawdzić zachowanie aplikacji.

Devspaces-proxy-init kontener jest [kontenerem init,](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/) który dodaje dodatkowe reguły routingu na podstawie hierarchii przestrzeni do kontenera aplikacji. Dodaje reguły routingu, aktualizując konfigurację pliku */etc/resolv.conf* kontenera aplikacji i iptables przed jego uruchomieniem. Aktualizacje */etc/resolv.conf* umożliwiają rozpoznawanie usług DNS w przestrzeniach nadrzędnych. Aktualizacje konfiguracji iptables upewnij się, że cały ruch TCP do i z kontenera aplikacji są kierowane przez devspaces-proxy. Wszystkie aktualizacje z devspaces-proxy-init się oprócz reguł, które dodaje Kubernetes.

Devspaces-build kontener jest kontenerem init i ma kod źródłowy projektu i gniazdo Platformy Docker zainstalowany. Kod źródłowy projektu i dostęp do platformy Docker umożliwia kontener aplikacji, które mają być budowane bezpośrednio przez zasobnika.

> [!NOTE]
> Usługa Azure Dev Spaces używa tego samego węzła do utworzenia kontenera aplikacji i uruchomienia go. W rezultacie usługa Azure Dev Spaces nie potrzebuje zewnętrznego rejestru kontenerów do tworzenia i uruchamiania aplikacji.

Serwer dostępu do łączy webhook Kubernetes nasłuchuje wszelkich nowych zasobników, które są tworzone w klastrze AKS. Jeśli ten zasobnik jest wdrażany w dowolnym obszarze nazw z etykietą *azds.io/space=true,* wstrzykuje ten zasobnik z dodatkowymi kontenerami. Devspaces-build kontener jest wstrzykiwany tylko wtedy, gdy kontener aplikacji jest uruchamiany przy użyciu narzędzi po stronie klienta.

Po przygotowaniu klastra AKS można użyć narzędzia po stronie klienta, aby przygotować i uruchomić kod w przestrzeni deweloperskiej.

## <a name="client-side-tooling"></a>Narzędzia po stronie klienta

Narzędzia po stronie klienta umożliwiają użytkownikowi:
* Generowanie pliku dockerfile, wykresu helm i pliku konfiguracji usługi Azure Dev Spaces dla aplikacji.
* Tworzenie nadrzędnych i podrzędnych przestrzeni deweloperskich.
* Powiedz kontrolerowi, aby skompilować i uruchomić aplikację.

Gdy aplikacja jest uruchomiona, narzędzia po stronie klienta również:
* Odbiera i wyświetla stdout i stderr z aplikacji uruchomionej w AKS.
* Używa [port-forward,](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/) aby zezwolić na dostęp\/do aplikacji w sieci Web przy użyciu http: /localhost.
* Dołącza debuger do uruchomionej aplikacji w u.
* Synchronizuje kod źródłowy z miejscem deweloperskim po wykryciu zmiany dla kompilacji przyrostowych, co pozwala na szybką iterację.
* Umożliwia podłączenie komputera deweloperskiego bezpośrednio do klastra AKS.

Narzędzia po stronie klienta można użyć z wiersza `azds` polecenia jako część polecenia. Można również użyć narzędzia po stronie klienta z:

* Visual Studio Code przy użyciu [rozszerzenia Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds).
* Visual Studio z [programem Visual Studio Tools dla kubernetes](https://aka.ms/get-vsk8stools).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat korzystania z narzędzi po stronie klienta w celu przygotowania i uruchomienia kodu w obszarze deweloperskim, zobacz [Jak działa przygotowanie projektu dla usługi Azure Dev Spaces.][how-it-works-prep]

Aby rozpocząć korzystanie z usługi Azure Dev Spaces do tworzenia zespołów, zobacz tworzenie zespołu w przewodniku Szybki start [usługi Azure Dev Spaces.][quickstart-team]

[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[quickstart-team]: quickstart-team-development.md