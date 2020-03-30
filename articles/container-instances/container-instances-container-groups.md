---
title: Wprowadzenie do grup kontenerów
description: Dowiedz się więcej o grupach kontenerów w wystąpieniach kontenerów platformy Azure, kolekcji wystąpień współużytkowania cyklu życia i zasobów, takich jak magazyn i sieć
ms.topic: article
ms.date: 11/01/2019
ms.custom: mvc
ms.openlocfilehash: 73781418321c3932bf3e0190b646dcd3bb178195
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247217"
---
# <a name="container-groups-in-azure-container-instances"></a>Grupy kontenerów w usłudze Azure Container Instances

Zasób najwyższego poziomu w wystąpieniach kontenera platformy Azure jest *grupą kontenerów.* W tym artykule opisano, jakie są grupy kontenerów i typy scenariuszy, które umożliwiają.

## <a name="what-is-a-container-group"></a>Co to jest grupa kontenerów?

Grupa kontenerów jest kolekcją kontenerów, które są zaplanowane na tym samym komputerze hosta. Kontenery w grupie kontenerów współużytkuje cykl życia, zasoby, sieć lokalną i woluminy magazynu. Jest podobny w koncepcji do *kapsuły* w [Kubernetes][kubernetes-pod].

Na poniższym diagramie przedstawiono przykład grupy kontenerów, która zawiera wiele kontenerów:

![Diagram grup kontenerów][container-groups-example]

W tym przykładowym wyekslii grupy kontenerów:

* Jest zaplanowana na jednym komputerze hosta.
* Jest przypisana etykieta nazwy DNS.
* Udostępnia jeden publiczny adres IP z jednym portem narażonym.
* Składa się z dwóch pojemników. Jeden kontener nasłuchuje na porcie 80, podczas gdy drugi nasłuchuje na porcie 5000.
* Zawiera dwa udziały plików platformy Azure jako instalacje woluminu, a każdy kontener montuje jeden z udziałów lokalnie.

> [!NOTE]
> Grupy wielu kontenerów obsługują obecnie tylko kontenery systemu Linux. W przypadku kontenerów systemu Windows wystąpienia kontenerów platformy Azure obsługują tylko wdrażanie wystąpienia pojedynczego kontenera. Podczas gdy pracujemy nad wprowadzeniem wszystkich funkcji do kontenerów systemu Windows, można znaleźć aktualne różnice platformy w [przeglądzie](container-instances-overview.md#linux-and-windows-containers)usługi .

## <a name="deployment"></a>wdrażania

Poniżej przedstawiono dwa typowe sposoby wdrażania grupy wielu kontenerów: użyj [szablonu Menedżera zasobów][resource-manager template] lub [pliku YAML][yaml-file]. Szablon Menedżera zasobów jest zalecane, gdy trzeba wdrożyć dodatkowe zasoby usługi platformy Azure (na przykład [udział usługi Azure Files)][azure-files]podczas wdrażania wystąpień kontenera. Ze względu na bardziej zwięzły charakter formatu YAML, plik YAML jest zalecany, gdy wdrożenie obejmuje tylko wystąpienia kontenera. Aby uzyskać szczegółowe informacje na temat właściwości, które można ustawić, zobacz [odwołanie do szablonu Menedżera zasobów](/azure/templates/microsoft.containerinstance/containergroups) lub dokumentację [referencyjną YAML.](container-instances-reference-yaml.md)

Aby zachować konfigurację grupy kontenerów, można wyeksportować konfigurację do pliku YAML za pomocą polecenia Azure CLI [az container export][az-container-export]. Eksportuj umożliwia przechowywanie konfiguracji grupy kontenerów w kontroli wersji dla "konfiguracji jako kodu". Możesz też użyć wyeksportowanego pliku jako punktu wyjścia podczas tworzenia nowej konfiguracji w yaml.



## <a name="resource-allocation"></a>Alokacja zasobów

Wystąpienia kontenera platformy Azure przydziela zasoby, takie jak procesory, pamięci i opcjonalnie [procesory GPU][gpus] (wersja zapoznawcza) do grupy wielu kontenerów, dodając [żądania zasobów][resource-requests] wystąpień w grupie. Biorąc zasobów procesora CPU jako przykład, jeśli utworzysz grupę kontenerów z dwóch wystąpień kontenera, każdy żądający 1 procesora CPU, a następnie grupa kontenerów jest przydzielana 2 procesory.

### <a name="resource-usage-by-container-instances"></a>Użycie zasobów przez wystąpienia kontenera

Każde wystąpienie kontenera w grupie jest przydzielane zasoby określone w jego żądania zasobów. Jednak maksymalna liczba zasobów używanych przez wystąpienie kontenera w grupie może być inna, jeśli skonfigurujesz jego opcjonalną właściwość [limitu zasobów.][resource-limits] Limit zasobów wystąpienia kontenera musi być większy lub równy właściwości żądania obowiązkowego [zasobu.][resource-requests]

* Jeśli nie określisz limitu zasobów, maksymalne użycie zasobów w wystąpieniu kontenera jest takie samo jak jego żądanie zasobów.

* Jeśli określisz limit dla wystąpienia kontenera, maksymalne użycie wystąpienia może być większe niż żądanie, do ustawionego limitu. Odpowiednio użycie zasobów przez inne wystąpienia kontenera w grupie może zmniejszyć. Maksymalny limit zasobów, który można ustawić dla wystąpienia kontenera, to łączna ilość zasobów przydzielonych grupie.
    
Na przykład w grupie z dwóch wystąpień kontenera każdy żądający 1 procesora CPU, jeden z kontenerów może uruchomić obciążenie, które wymaga więcej procesorów do uruchomienia niż inne.

W tym scenariuszu można ustawić limit zasobów 2 procesorów dla wystąpienia kontenera. Ta konfiguracja umożliwia wystąpieniu kontenera użycie maksymalnie 2 pełnych procesorów, jeśli są dostępne.

### <a name="minimum-and-maximum-allocation"></a>Minimalna i maksymalna alokacja

* Przydziel **co najmniej** 1 procesora CPU i 1 GB pamięci do grupy kontenerów. Poszczególne wystąpienia kontenerów w grupie mogą być aprowizacji z mniej niż 1 procesora CPU i 1 GB pamięci. 

* Aby uzyskać **maksymalną** ilość zasobów w grupie kontenerów, zobacz [dostępność zasobów][region-availability] dla wystąpień kontenerów platformy Azure w regionie wdrażania.

## <a name="networking"></a>Obsługa sieci

Grupy kontenerów mogą współużytkować zewnętrzny adres IP, co najmniej jeden port na tym adresie IP oraz etykietę DNS z w pełni kwalifikowaną nazwą domeny (FQDN). Aby umożliwić klientom zewnętrznym dotarcie do kontenera w grupie, należy udostępnić port na adres IP i z kontenera. Ponieważ kontenery w grupie współużytkuje obszar nazw portu, mapowanie portów nie jest obsługiwane. Adres IP grupy kontenerów i numer FQDN zostaną zwolnione po usunięciu grupy kontenerów. 

W ramach grupy kontenerów wystąpienia kontenerów mogą dotrzeć do siebie za pośrednictwem localhost na dowolnym porcie, nawet jeśli te porty nie są udostępniane zewnętrznie na adres IP grupy lub z kontenera.

Opcjonalnie wdrożyć grupy kontenerów w [sieci wirtualnej platformy Azure,][virtual-network] aby umożliwić kontenerom komunikować się bezpiecznie z innymi zasobami w sieci wirtualnej.

## <a name="storage"></a>Magazyn

Można określić woluminy zewnętrzne do zainstalowania w grupie kontenerów. Obsługiwane woluminy obejmują:
* [Udział plików platformy Azure][azure-files]
* [Wpis tajny][secret]
* [Pusty katalog][empty-directory]
* [Sklonowane git repo][volume-gitrepo]

Woluminy te można mapować na określone ścieżki w poszczególnych kontenerach w grupie. 

## <a name="common-scenarios"></a>Typowe scenariusze

Grupy wielu kontenerów są przydatne w przypadkach, gdy chcesz podzielić jedno zadanie funkcjonalne na niewielką liczbę obrazów kontenerów. Te obrazy mogą być następnie dostarczane przez różne zespoły i mają oddzielne wymagania dotyczące zasobów.

Przykładowe użycie może obejmować:

* Kontener obsługujący aplikację sieci web i kontener pobierający najnowszą zawartość z formantu źródła.
* Kontener aplikacji i kontener rejestrowania. Kontener rejestrowania zbiera dzienniki i metryki danych wyjściowych przez główną aplikację i zapisuje je do magazynu długoterminowego.
* Kontener aplikacji i kontener monitorowania. Kontener monitorowania okresowo sprawia, że żądanie do aplikacji, aby upewnić się, że jest uruchomiony i odpowiada poprawnie i podnosi alert, jeśli nie jest.
* Pojemnik front-end i kontener zaplecza. Front end może obsługiwać aplikacji sieci web, z zaplecza z uruchomić usługę do pobierania danych. 

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak wdrożyć grupę kontenerów z wieloma kontenerami za pomocą szablonu usługi Azure Resource Manager:

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
[secret]: container-instances-volume-secret.md
[volume-gitrepo]: container-instances-volume-gitrepo.md
[gpus]: container-instances-gpu.md
[empty-directory]: container-instances-volume-emptydir.md
[az-container-export]: /cli/azure/container#az-container-export
