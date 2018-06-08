---
title: Zainstalować tajny woluminu w wystąpień kontenera platformy Azure
description: Dowiedz się, jak zainstalować tajny wolumin do przechowywania poufnych informacji, aby uzyskać dostęp przez swoich wystąpień kontenera
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 02/08/2018
ms.author: marsma
ms.openlocfilehash: a030582c885dd0a5680bd23046ea2a9c0329701a
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34830075"
---
# <a name="mount-a-secret-volume-in-azure-container-instances"></a>Zainstalować tajny woluminu w wystąpień kontenera platformy Azure

Dowiedz się, jak zainstalować *klucz tajny* woluminu w swoich wystąpień kontenera do przechowywania i pobierania informacji poufnych przez kontenery w Twoich grupach kontenera.

> [!NOTE]
> Instalowanie *klucz tajny* wolumin jest obecnie ograniczone do kontenerów systemu Linux. Dowiedz się, jak przekazać zmiennych środowiskowych bezpiecznych kontenerów w systemach Windows i Linux w [Ustaw zmienne środowiskowe](container-instances-environment-variables.md). Podczas gdy pracujemy, aby udostępnić wszystkie funkcje na potrzeby kontenerów systemu Windows, bieżące różnice dotyczące platform możesz znaleźć w temacie [Limity przydziałów i dostępność regionów dla usługi Azure Container Instances](container-instances-quotas.md).

## <a name="secret-volume"></a>tajny woluminu

Można użyć *klucz tajny* woluminie, aby zapewnić poufne informacje do kontenerów w grupie kontenera. *Klucz tajny* wolumin przechowuje tajnych określona w plikach w woluminie, którego kontenery w grupie kontener może uzyskać dostęp. Przy użyciu kluczy tajnych w *klucz tajny* woluminu, można uniknąć umieszczenie poufnych danych, takie jak klucze SSH lub poświadczenia bazy danych w kodzie aplikacji.

Wszystkie *klucz tajny* woluminów bazują na [tmpfs][tmpfs], filesystem kopii pamięci RAM; ich zawartość nigdy nie są zapisywane w pamięci trwałej.

## <a name="mount-a-secret-volume"></a>Zainstalować tajny woluminu

Aby zainstalować *klucz tajny* woluminu w wystąpieniu kontenera, należy wdrożyć przy użyciu [szablonu usługi Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups).

Najpierw należy wypełnić `volumes` tablicy w grupie kontenera `properties` sekcji szablonu. Następnie dla każdego kontenera w grupie kontenera, w którym chcesz zainstalować *klucz tajny* woluminu, wypełnij `volumeMounts` tablicy w `properties` sekcja definicji kontenera.

Na przykład następujący szablon usługi Resource Manager tworzy grupę kontenera składające się z jeden kontener. Kontener instalacji *klucz tajny* wolumin składającego się z dwóch kluczy tajnych algorytmem Base64.

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-secret.json -->
[!code-json[volume-secret](~/azure-docs-json-samples/container-instances/aci-deploy-volume-secret.json)]

Aby zapoznać się przykładem kontenera wystąpienia wdrożenia z szablonem usługi Azure Resource Manager, zobacz [wdrożenia kontenera wielu grup wystąpień kontenera Azure](container-instances-multi-container-group.md).

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak zainstalować inne typy woluminu w wystąpień kontenera platformy Azure:

* [Instalowanie udziału plików na platformę Azure w wystąpień kontenera platformy Azure](container-instances-volume-azure-files.md)
* [Zainstalować emptyDir woluminu w wystąpień kontenera platformy Azure](container-instances-volume-emptydir.md)
* [Zainstalować wolumin gitRepo w wystąpień kontenera platformy Azure](container-instances-volume-gitrepo.md)

<!-- LINKS - External -->
[tmpfs]: https://wikipedia.org/wiki/Tmpfs