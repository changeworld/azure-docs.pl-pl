---
title: Grupy kontenerów w usłudze Azure Container Instances
description: Zrozumieć, jak wiele kontenerów, grup pracy w usłudze Azure Container Instances
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 03/20/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: f4bbea8acd447a731cf5c56f9876baf9183735ea
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2019
ms.locfileid: "59005532"
---
# <a name="container-groups-in-azure-container-instances"></a>Grupy kontenerów w usłudze Azure Container Instances

Zasób najwyższego poziomu w usłudze Azure Container Instances jest *grupy kontenerów*. W tym artykule opisano, co to są grupy kontenerów oraz typy scenariusze, które umożliwiają one.

## <a name="how-a-container-group-works"></a>Jak działa Grupa kontenerów

Grupy kontenerów to zbiór kontenerów, które są planowane na tym samym komputerze hosta. Kontenery w grupie kontenerów udostępniać cykl życia, zasobów, sieci lokalnej i woluminy magazynu. Jest podobna do *pod* w [Kubernetes][kubernetes-pod].

Na poniższym diagramie przedstawiono przykład grupy kontenerów, która zawiera wiele kontenerów:

![Diagram grupy kontenerów][container-groups-example]

Ten przykład grupy kontenerów:

* Zaplanowano jednego komputera-hosta.
* Jest przypisana etykieta nazwy DNS.
* Przedstawia jeden publiczny adres IP, za pomocą jednej ujawnionych portów.
* Składa się z dwóch kontenerów. Jeden kontener nasłuchuje na porcie 80, podczas innych nasłuchuje na porcie 5000.
* Zawiera dwie udziałów plików platformy Azure jako woluminu instaluje i każdego kontenera na komputerze instalująca jeden z udziałów lokalnie.

> [!NOTE]
> Grup wielu kontenerów obsługuje obecnie tylko kontenerów systemu Linux. Kontenery Windows Azure Container Instances obsługuje tylko wdrożenie pojedynczego wystąpienia. Podczas gdy pracujemy, aby udostępnić wszystkie funkcje dostępne w kontenerach Windows, bieżące różnice dotyczące platform w usłudze można znaleźć [Przegląd](container-instances-overview.md#linux-and-windows-containers).

## <a name="deployment"></a>Wdrożenie

Poniżej przedstawiono dwa podstawowe sposoby wdrażania grupy wielu kontenerów: Użyj [szablonu usługi Resource Manager] [ resource-manager template] lub [pliku YAML][yaml-file]. Szablon usługi Resource Manager jest zalecane, gdy należy wdrożyć zasoby dodatkowe usługi platformy Azure (na przykład [udostępniać usługi Azure Files][azure-files]) podczas wdrażania wystąpień kontenerów. Ze względu na charakter bardziej zwięzły widok formacie YAML pliku YAML jest zalecane, gdy Twoje wdrożenie obejmuje tylko wystąpienia kontenera.

Aby zachować konfigurację grupy kontenerów, możesz wyeksportować konfigurację do pliku YAML, za pomocą polecenia wiersza polecenia platformy Azure [az container eksportu][az-container-export]. Eksportowanie służy do przechowywania konfiguracji grupy kontenerów w systemie kontroli wersji dla "konfiguracji jako kodu". Możesz też użyć wyeksportowanego pliku jako punktu wyjścia podczas tworzenia nowej konfiguracji w YAML.

## <a name="resource-allocation"></a>Alokacja zasobów

Usługa Azure Container Instances przydziela zasoby, takie jak procesory, pamięć i opcjonalnie [procesorów GPU] [ gpus] (wersja zapoznawcza) do grupy kontenerów przez dodanie [żądania zasobu] [ resource-requests] wystąpień w grupie. Biorąc zasobów procesora CPU na przykład, jeśli tworzysz grupę kontenerów z dwóch wystąpień, każdego żądania 1 procesor CPU, a następnie grupy kontenerów jest przydzielany 2 procesory CPU.

Maksymalna zasoby dostępne dla grupy kontenerów są zależne od [regionu świadczenia usługi Azure] [ region-availability] używaną na potrzeby wdrożenia.

### <a name="container-resource-requests-and-limits"></a>Żądania zasobów kontenera i limity

* Domyślnie wystąpień kontenerów w grupie Udostępnij żądanych zasobów grupy. W grupie przy użyciu dwóch wystąpień każdego żądania 1 procesor CPU, grupa jako całość ma dostęp do 2 procesory CPU. Każde wystąpienie można wykorzystać maksymalnie 2 procesory CPU i wystąpienia mogą konkurować o zasoby procesora CPU, gdy są one uruchomione.

* Aby ograniczyć użycie zasobów przez wystąpienie w grupie, opcjonalnie ustawić [limit zasobów] [ resource-limits] dla tego wystąpienia. W grupie z dwoma wystąpieniami żądanie 1 procesor CPU, jeden z kontenerów może wymagać większej liczby procesorów CPU niż drugi.

  W tym scenariuszu można ustawić limit zasobów wynosi 0,5 CPU dla jednego wystąpienia, a limit wynoszący 2 procesory CPU dla drugiego. Ta konfiguracja ogranicza użycie zasobów pierwszego kontenera 0,5 CPU, co drugi kontener, aby zużyć w pełnej 2 procesory CPU, jeśli jest dostępny.

Aby uzyskać więcej informacji, zobacz [ResourceRequirements] [ resource-requirements] właściwość w kontenerze grup interfejsu API REST.

### <a name="minimum-and-maximum-allocation"></a>Minimalna i maksymalna alokacja

* Przydziel **minimalne** 1 procesora CPU i 1 GB pamięci do grupy kontenerów. Pojedynczy kontener wystąpień w obrębie grupy mogą być udostępniane z mniej niż 1 procesora CPU i 1 GB pamięci. 

* Aby uzyskać **maksymalną** zasoby w grupie kontenerów, zobacz [dostępności zasobów] [aci-— dostępność regionów] dla usługi Azure Container Instances w regionie wdrażania.

## <a name="networking"></a>Networking

Grupy kontenerów udostępnić adres IP i port przestrzeni nazw na ten adres IP. Aby włączyć klientów zewnętrznych dotrzeć do kontenera w grupie, należy udostępnić portu, adresu IP i z kontenera. Ponieważ kontenery w obrębie grupy współdzielą portu przestrzeni nazw, mapowanie portów nie jest obsługiwane. Kontenery w obrębie grupy docierać do siebie nawzajem za pośrednictwem hosta lokalnego na portach, które mogą mieć dostępne, nawet wtedy, gdy te porty nie są widoczne zewnętrznie na adres IP grupy.

Opcjonalnie wdrażają grup kontenerów do [sieci wirtualnej platformy Azure] [ virtual-network] (wersja zapoznawcza), aby umożliwić kontenerów do bezpiecznego komunikowania się z innymi zasobami w sieci wirtualnej.

## <a name="storage"></a>Magazyn

Można określić zewnętrzne woluminów należy zainstalować w ramach grupy kontenerów. Woluminy można mapować do określonej ścieżki w ramach poszczególnych kontenerów w grupie.

## <a name="common-scenarios"></a>Typowe scenariusze

Grup wielu kontenerów są przydatne w sytuacjach, w którym chcesz podzielić funkcjonalności pojedynczego zadania na niewielką liczbę obrazów kontenerów. Obrazy te mogą być następnie dostarczane przez różne zespoły i mają wymagania osobnego zasobu.

Przykład użycia mogą obejmować:

* Kontener obsługujący aplikację sieci web i kontener pobierać najnowszą zawartość z kontroli źródła.
* Kontener aplikacji i kontenerów rejestrowania. Kontener rejestrowania zbiera dane wyjściowe dzienników i metryk przez główne aplikacji i zapisuje je do magazynu długoterminowego.
* Kontener aplikacji i monitorowania kontenerów. Monitorowania kontenera okresowo wysyła żądanie do aplikacji, aby upewnić się, że jest uruchomiona i odpowiada poprawnie i zgłasza alert, jeśli nie jest.
* Kontener frontonu i zaplecza kontenera. Fronton może służyć aplikacji sieci web z zapleczem usługi do pobierania danych. 

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak wdrożyć grupę kontenerów z obsługą wielu kontenerów przy użyciu szablonu usługi Azure Resource Manager:

> [!div class="nextstepaction"]
> [Wdrażanie grupy kontenerów][resource-manager template]

<!-- IMAGES -->
[container-groups-example]: ./media/container-instances-container-groups/container-groups-example.png

<!-- LINKS - External -->
[dcos-pod]: https://dcos.io/docs/1.10/deploying-services/pods/
[kubernetes-pod]: https://kubernetes.io/docs/concepts/workloads/pods/pod/

<!-- LINKS - Internal -->
[resource-manager template]: container-instances-multi-container-group.md
[yaml-file]: container-instances-multi-container-yaml.md
[region-availability]: container-instances-region-availability.md
[resource-requests]: /rest/api/container-instances/containergroups/createorupdate#resourcerequests
[resource-limits]: /rest/api/container-instances/containergroups/createorupdate#resourcelimits
[resource-requirements]: /rest/api/container-instances/containergroups/createorupdate#resourcerequirements
[azure-files]: container-instances-volume-azure-files.md
[virtual-network]: container-instances-vnet.md
[gpus]: container-instances-gpu.md
[az-container-export]: /cli/azure/container#az-container-export
