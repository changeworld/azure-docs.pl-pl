---
title: Omówienie usługi Azure Container Instances
description: Informacje dotyczące usługi Azure Container Instances
services: container-instances
author: seanmck
manager: jeconnoc
ms.service: container-instances
ms.topic: overview
ms.date: 07/19/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 953d1dfd633f2fee52a2e6d197c6f32e7ab053f7
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2018
ms.locfileid: "39160454"
---
# <a name="azure-container-instances"></a>Azure Container Instances

Kontenery stają się preferowanym sposobem tworzenia pakietów aplikacji w chmurze, ich wdrażania oraz zarządzania nimi. Usługa Azure Container Instances to najszybszy i najprostszy sposób uruchomienia kontenera na platformie Azure, bez konieczności zarządzania jakimikolwiek maszynami wirtualnymi ani korzystania z usługi wyższego poziomu.

Usługa Azure Container Instances to doskonałe rozwiązanie dla wszystkich scenariuszy, które może działać w kontenerach izolowanych, w tym w przypadku prostych aplikacji, automatyzacji zadań i zadań kompilacji. W przypadku scenariuszy, w których potrzebujesz pełnej orkiestracji kontenerów, w tym opcji odnajdowania usług w wielu kontenerach, automatycznego skalowania i skoordynowanych uaktualnień aplikacji, zalecamy skorzystanie z usługi [Azure Kubernetes Service](../aks/index.yml) (AKS).

## <a name="fast-startup-times"></a>Krótki czas uruchamiania

Kontenery oferują znaczące korzyści związane z uruchamianiem w porównaniu do maszyn wirtualnych. Usługa Azure Container Instances umożliwia uruchamianie kontenera na platformie Azure w ciągu kilku sekund bez konieczności aprowizowania maszyn wirtualnych i zarządzania nimi.

## <a name="public-ip-connectivity-and-dns-name"></a>Łączność przy użyciu publicznych adresów IP i nazwa DNS

Usługa Azure Container Instances umożliwia uwidacznianie kontenerów bezpośrednio w Internecie za pomocą adresu IP i w pełni kwalifikowanej nazwy domeny (FQDN, fully qualified domain name). Podczas tworzenia wystąpienia kontenera możesz określić niestandardową etykietę nazwy DNS, dzięki czemu aplikacja będzie dostępna pod adresem *etykieta_niestandardowa*.*region_świadczenia_usługi_Azure*.azurecontainer.io.

## <a name="hypervisor-level-security"></a>Zabezpieczenia na poziomie funkcji hypervisor

W przeszłości kontenery oferowały zarządzanie zasobami i izolację zależności aplikacji, ale nie były wystarczająco odporne na użycie wielu obcych dzierżaw. Usługa Azure Container Instances gwarantuje, że aplikacja jest izolowana w kontenerze w takim samym stopniu, w jakim byłaby na maszynie wirtualnej.

## <a name="custom-sizes"></a>Rozmiary niestandardowe

Kontenery są przeważnie optymalizowane do uruchamiania tylko jednej aplikacji, ale szczegółowe potrzeby poszczególnych aplikacji mogą się bardzo różnić. Usługa Azure Container Instances zapewnia optymalne wykorzystanie, zezwalając na korzystanie z dokładnych specyfikacji rdzeni procesora i pamięci. Opłaty są naliczane zgodnie z zapotrzebowaniem i za sekundę, dlatego można skutecznie dostosować wydatki w oparciu o potrzeby.

## <a name="persistent-storage"></a>Magazyn trwały

Aby umożliwić pobieranie i utrwalanie stanu przy użyciu usługi Azure Container Instances, firma Microsoft oferuje bezpośrednie [instalowanie udziałów usługi Azure Files](container-instances-mounting-azure-files-volume.md).

## <a name="linux-and-windows-containers"></a>Kontenery systemów Linux i Windows

Usługa Azure Container Instances umożliwia planowanie kontenerów systemów Windows i Linux przy użyciu tego samego interfejsu API. Podczas tworzenia [grup kontenerów](container-instances-container-groups.md) po prostu określ typ systemu operacyjnego.

Niektóre funkcje są obecnie ograniczone do kontenerów systemu Linux. Podczas gdy pracujemy, aby udostępnić równoważność funkcji na potrzeby kontenerów systemu Windows, bieżące różnice dotyczące platform możesz znaleźć w temacie [Limity przydziałów i dostępność regionów dla usługi Azure Container Instances](container-instances-quotas.md).

Usługa Azure Container Instances obsługuje obrazy systemu Windows na podstawie wersji kanału Long-Term Servicing Channel (LTSC). Obrazy systemu Windows z wersjami kanału Semi-Annual Channel (SAC), takimi jak 1709 i 1803, nie są obsługiwane.

## <a name="co-scheduled-groups"></a>Grupy planowane wspólnie

Usługa Azure Container Instances obsługuje planowanie [grup wielu kontenerów](container-instances-container-groups.md), które współużytkują maszynę hosta, sieć lokalną, magazyn i cykl życia. Dzięki temu można łączyć główny kontener aplikacji z innymi kontenerami pełniącymi rolę pomocniczą, np. przyczepkami rejestrowania.

## <a name="next-steps"></a>Następne kroki

Spróbuj wdrożyć kontener na platformie Azure za pomocą jednego polecenia, korzystając z naszego przewodnika Szybki start:

> [!div class="nextstepaction"]
> [Azure Container Instances — Szybki start](container-instances-quickstart.md)
