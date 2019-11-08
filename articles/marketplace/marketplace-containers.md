---
title: Przewodnik publikowania oferty kontenerów dla portalu Azure Marketplace
description: W tym artykule opisano wymagania dotyczące publikowania kontenerów w portalu Marketplace
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: ellacroi
manager: nunoc
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 07/09/2018
ms.author: ellacroi
ms.openlocfilehash: ffdc6b22209c1d334ac7b75c7079e755a7405154
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822990"
---
# <a name="containers-offer-publishing-guide"></a>Przewodnik publikowania oferty kontenerów

Kontener oferuje pomoc w publikowaniu obrazu kontenera w witrynie Azure Marketplace. Skorzystaj z tego przewodnika, aby poznać wymagania dotyczące tej oferty. 

Są to oferty transakcji, które są wdrażane i rozliczane za pomocą witryny Marketplace. Wywołanie akcji, którą widzi użytkownik, to "Pobierz teraz".

Użyj typu oferty kontenera, gdy rozwiązanie jest obrazem kontenera Docker, który został zainicjowany jako usługa kontenera platformy Azure oparta na Kubernetes.

>[!NOTE]
>Na przykład oparta na Kubernetes usługa Azure Container Service, taka jak Azure Kubernetes Service lub Azure Container Instances, wybór klientów platformy Azure dla środowiska uruchomieniowego kontenera opartego na Kubernetes.  

Firma Microsoft obsługuje obecnie bezpłatne modele licencjonowania (BYOL).

## <a name="containers-offer"></a>Oferta kontenerów

| Wymaganie | Szczegóły |  
|:--- |:--- |  
| Rozliczenia i pomiary | Obsługuj model rozliczeń bezpłatny lub BYOL. |  
| Obraz skompilowany z pliku dockerfile | Obrazy kontenerów muszą być oparte na specyfikacji obrazu platformy Docker i muszą być zbudowane z pliku dockerfile.<ul> <li>Aby uzyskać więcej informacji na temat tworzenia obrazów platformy Docker, odwiedź sekcję użycie znajdującą się w witrynie [docs.docker.com/engine/reference/builder/#usage](https://docs.docker.com/engine/reference/builder/#usage).</li> </ul> |  
| Hosting w ACR | Obrazy kontenerów muszą być hostowane w repozytorium Azure Container Registry (ACR).<ul> <li>Aby uzyskać więcej informacji na temat pracy z usługą ACR, odwiedź stronę szybki start: tworzenie rejestru kontenerów za pomocą strony Azure Portal znajdującej się w witrynie [docs.Microsoft.com/Azure/Container-Registry/Container-Registry-Get-Started-Portal](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal).</li> </ul> |  
| Tagowanie obrazu | Obrazy kontenerów muszą zawierać co najmniej 1 tag (maksymalna liczba tagów: 16).<ul> <li>Aby uzyskać więcej informacji na temat tagowania obrazu, odwiedź stronę tag platformy Docker znajdującą się pod adresem [docs.Docker.com/Engine/Reference/CommandLine/tag](https://docs.docker.com/engine/reference/commandline/tag).</li> </ul> |  

## <a name="next-steps"></a>Następne kroki

Jeśli jeszcze tego nie zrobiono, 

- [Zarejestruj](https://azuremarketplace.microsoft.com/sell) się w portalu Marketplace.

Jeśli jesteś zarejestrowanym użytkownikiem i tworzysz nową ofertę lub pracujesz nad istniejącym,

- [Zaloguj się do Portal Cloud partner](https://cloudpartner.azure.com) , aby utworzyć lub zakończyć swoją ofertę.
- Aby uzyskać więcej informacji, zobacz [kontenery](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-containers-offer) .
