---
title: Przewodnik publikowania ofert kontenerów dla portalu Azure Marketplace
description: W tym artykule opisano wymagania dotyczące publikowania kontenerów w portalu Marketplace
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: dsindona
ms.openlocfilehash: 882d428d7a8a9b56408aaa68964fa1e36c7cc7c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80277310"
---
# <a name="containers-offer-publishing-guide"></a>Przewodnik publikowania oferty kontenerów

Oferty kontenerów ułatwią publikowanie obrazu kontenera w portalu Azure Marketplace. Skorzystaj z tego przewodnika, aby zrozumieć wymagania dotyczące tej oferty. 

Są to oferty transakcji, które są wdrażane i rozliczane za pośrednictwem portalu Marketplace. Wywołanie działania, które użytkownik widzi jest "Get It Now".

Użyj typu oferty kontenera, gdy rozwiązanie jest obraz kontenera platformy Docker aprowizowana jako usługa kontenera platformy Azure oparta na usłudze Platformy Azure opartej na usłudze Kubernetes.

>[!NOTE]
>Na przykład usługa kontenera platformy Azure oparta na usłudze platformy Azure oparta na usłudze platformy Kubernetes, taka jak usługa Azure Kubernetes Service lub Wystąpienia kontenerów platformy Azure, wybór klientów platformy Azure dla środowiska wykonawczego kontenera opartego na usłudze Kubernetes.  

Firma Microsoft obsługuje obecnie modele licencjonowania bezpłatnej i własnej licencji (BYOL).

## <a name="containers-offer"></a>Oferta kontenerów

| Wymaganie | Szczegóły |  
|:--- |:--- |  
| Rozliczenia i pomiary | Obsługa bezpłatnego lub byolowego modelu rozliczeniowego. |  
| Obraz zbudowany z pliku Dockerfile | Obrazy kontenerów muszą być oparte na specyfikacji obrazu platformy Docker i muszą być zbudowane z pliku Dockerfile.<ul> <li>Aby uzyskać więcej informacji na temat tworzenia obrazów dok [docs.docker.com/engine/reference/builder/#usage.](https://docs.docker.com/engine/reference/builder/#usage)</li> </ul> |  
| Hosting w ACR | Obrazy kontenerów muszą być hostowane w repozytorium rejestru kontenerów platformy Azure (ACR).<ul> <li>Aby uzyskać więcej informacji na temat pracy z usługą ACR, odwiedź przewodnik Szybki start: Tworzenie rejestru kontenerów przy użyciu strony portalu Azure znajdującej się w [docs.microsoft.com/azure/container-registry/container-registry-get-started-portal](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal).</li> </ul> |  
| Tagowanie obrazów | Obrazy kontenerów muszą zawierać co najmniej 1 znacznik (maksymalna liczba tagów: 16).<ul> <li>Aby uzyskać więcej informacji na temat oznaczania obrazu, odwiedź stronę tagu docker znajdującą się w [docs.docker.com/engine/reference/commandline/tag](https://docs.docker.com/engine/reference/commandline/tag).</li> </ul> |  

## <a name="next-steps"></a>Następne kroki

Jeśli jeszcze tego nie zrobiłeś, 

- [Zarejestruj się](https://azuremarketplace.microsoft.com/sell) na rynku.

Jeśli jesteś zarejestrowany i tworzysz nową ofertę lub pracujesz nad istniejącą,

- [Zaloguj się do portalu cloud partner,](https://cloudpartner.azure.com) aby utworzyć lub uzupełnić ofertę.
- Zobacz [kontenery,](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-containers-offer) aby uzyskać więcej informacji.
