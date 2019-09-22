---
title: Azure Container Instances grupy kontenerów
description: Informacje o tym, jak grupy wielokontenerowe działają w Azure Container Instances
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 03/20/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: cc9b11ba5fe0cd015d0879f28b9e85fb46b11955
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/22/2019
ms.locfileid: "71178581"
---
# <a name="container-groups-in-azure-container-instances"></a>Grupy kontenerów w Azure Container Instances

Zasób najwyższego poziomu w Azure Container Instances jest *grupą kontenerów*. W tym artykule opisano, co to są grupy kontenerów i jakie typy scenariuszy są włączane.

## <a name="how-a-container-group-works"></a>Jak działa Grupa kontenerów

Grupa kontenerów to kolekcja kontenerów, które są planowane na tym samym komputerze-hoście. Kontenery w grupie kontenerów współdzielą cykl życia, zasoby, sieć lokalną i woluminy magazynu. Jest to podobne w koncepcji w [Kubernetes][kubernetes-pod].

Na poniższym diagramie przedstawiono przykład grupy kontenerów zawierającej wiele kontenerów:

![Diagram grup kontenerów][container-groups-example]

Ta przykładowa Grupa kontenerów:

* Zaplanowano na pojedynczym komputerze-hoście.
* Ma przypisaną etykietę nazw DNS.
* Udostępnia jeden publiczny adres IP z jednym uwidocznionym portem.
* Składa się z dwóch kontenerów. Jeden kontener nasłuchuje na porcie 80, podczas gdy drugi nasłuchuje na porcie 5000.
* Obejmuje dwa udziały plików platformy Azure jako instalacje woluminów, a każdy kontener instaluje jeden z udziałów lokalnie.

> [!NOTE]
> Grupy wielokontenerowe obsługują obecnie tylko kontenery systemu Linux. W przypadku kontenerów systemu Windows Azure Container Instances obsługuje tylko wdrożenie jednego wystąpienia. Gdy pracujemy nad przełączeniem wszystkich funkcji do kontenerów systemu Windows, w przeglądzie usługi można znaleźć [](container-instances-overview.md#linux-and-windows-containers)bieżące różnice między platformami.

## <a name="deployment"></a>Wdrożenie

Poniżej przedstawiono dwa typowe sposoby wdrażania grupy wielokontenerowej: Użyj [szablonu Menedżer zasobów][resource-manager template] lub [pliku YAML][yaml-file]. Szablon Menedżer zasobów jest zalecany, gdy konieczne jest wdrożenie dodatkowych zasobów usługi platformy Azure (na przykład [udziału Azure Files][azure-files]) podczas wdrażania wystąpień kontenerów. Ze względu na bardziej zwięzły YAML formatu pliku YAML zaleca się, gdy wdrożenie obejmuje tylko wystąpienia kontenerów. Aby uzyskać szczegółowe informacje na temat właściwości, które można ustawić, zapoznaj się z dokumentacją dotyczącą [szablonu Menedżer zasobów](/azure/templates/microsoft.containerinstance/containergroups) lub dokumentacji [referencyjnej YAML](container-instances-reference-yaml.md) .

Aby zachować konfigurację grupy kontenerów, można wyeksportować konfigurację do pliku YAML przy użyciu polecenia [AZ Container Export][az-container-export]na platformie Azure. Eksport umożliwia przechowywanie konfiguracji grupy kontenerów w kontroli wersji dla "konfiguracji jako kodu". Można też użyć wyeksportowanego pliku jako punktu wyjścia podczas tworzenia nowej konfiguracji w programie YAML.



## <a name="resource-allocation"></a>Alokacja zasobów

Azure Container Instances przydzielać zasoby, takie jak procesory CPU, pamięć i opcjonalnie [procesory GPU][gpus] (wersja zapoznawcza) do grupy kontenerów przez dodanie [żądań zasobów][resource-requests] wystąpień w grupie. Jeśli na przykład utworzysz grupę kontenerów z dwoma wystąpieniami, każdy z nich żąda 1 procesora CPU, Grupa kontenerów zostanie przypisana 2 procesory CPU.

Maksymalna liczba zasobów dostępnych dla grupy kontenerów zależy od [regionu platformy Azure][region-availability] używanego do wdrożenia.

### <a name="container-resource-requests-and-limits"></a>Żądania i limity zasobów kontenera

* Domyślnie wystąpienia kontenerów w grupie udostępniają żądane zasoby grupy. W grupie z dwoma wystąpieniami żądające 1 procesora CPU Grupa jako całość ma dostęp do 2 procesorów CPU. Każde wystąpienie może korzystać z maksymalnie dwóch procesorów CPU, a wystąpienia mogą ulec konkurowaniu w przypadku zasobów procesora CPU, gdy są uruchomione.

* Aby ograniczyć użycie zasobów przez wystąpienie w grupie, opcjonalnie Ustaw [Limit zasobów][resource-limits] dla tego wystąpienia. W grupie zawierającej dwa wystąpienia żądające 1 procesora CPU jeden z kontenerów może wymagać od siebie więcej procesorów CPU.

  W tym scenariuszu można ustawić limit zasobów 0,5 CPU dla jednego wystąpienia i limit 2 procesorów CPU dla drugiego. Ta konfiguracja ogranicza użycie zasobów pierwszego kontenera do 0,5 procesora CPU, co pozwala drugiemu kontenerowi używać do pełnych 2 procesorów CPU, jeśli są dostępne.

Aby uzyskać więcej informacji, zobacz Właściwość [ResourceRequirements][resource-requirements] w interfejsie API REST grup kontenerów.

### <a name="minimum-and-maximum-allocation"></a>Minimalna i maksymalna alokacja

* Przydziel **co najmniej** 1 procesor CPU i 1 GB pamięci do grupy kontenerów. Można obsługiwać poszczególne wystąpienia kontenerów w grupie przy użyciu mniej niż 1 procesora CPU i 1 GB pamięci. 

* Aby uzyskać **maksymalną liczbę** zasobów w grupie kontenerów, zapoznaj się z tematem [dostępność zasobów][region-availability] dla Azure Container Instances w regionie wdrożenia.

## <a name="networking"></a>Networking

Grupy kontenerów udostępniają adresy IP i przestrzeń nazw portów na tym adresie IP. Aby umożliwić klientom zewnętrznym dotarcie do kontenera w grupie, należy uwidocznić port w adresie IP i z kontenera. Ponieważ kontenery w grupie współużytkują przestrzeń nazw portu, mapowanie portów nie jest obsługiwane. Kontenery w obrębie grupy mogą się łączyć ze sobą za pomocą hosta lokalnego na portach, które zostały uwidocznione, nawet jeśli te porty nie są udostępniane zewnętrznie na adresie IP grupy.

Opcjonalnie można wdrożyć grupy kontenerów w usłudze [Azure Virtual Network][virtual-network] (wersja zapoznawcza), aby umożliwić kontenerom bezpieczne komunikowanie się z innymi zasobami w sieci wirtualnej.

## <a name="storage"></a>Magazyn

Możesz określić woluminy zewnętrzne do zainstalowania w obrębie grupy kontenerów. Te woluminy można mapować na określone ścieżki w poszczególnych kontenerach w grupie.

## <a name="common-scenarios"></a>Typowe scenariusze

Grupy wielu kontenerów są przydatne w przypadkach, w których chcesz podzielić pojedyncze zadanie funkcjonalne na niewielką liczbę obrazów kontenerów. Te obrazy mogą następnie zostać dostarczone przez różne zespoły i mieć oddzielne wymagania dotyczące zasobów.

Przykładowe użycie może obejmować:

* Kontener obsługujący aplikację sieci Web i kontener ściągający najnowszą zawartość z kontroli źródła.
* Kontener aplikacji i kontener rejestrowania. Kontener rejestrowania zbiera dzienniki i metryki dane wyjściowe przez główną aplikację i zapisuje je do magazynu długoterminowego.
* Kontener aplikacji i kontener monitorowania. Kontener monitorujący okresowo wysyła żądanie do aplikacji, aby upewnić się, że działa i odpowiada poprawnie, i zgłasza alert, jeśli nie jest.
* Kontener frontonu i kontener zaplecza. Fronton może obtworzyć aplikację sieci Web przy użyciu zaplecza z uruchomioną usługą w celu pobierania danych. 

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak wdrożyć wielokontenerową grupę kontenerów z szablonem Azure Resource Manager:

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
