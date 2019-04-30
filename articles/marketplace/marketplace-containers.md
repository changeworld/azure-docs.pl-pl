---
title: Kontenery oferują publikowania Przewodnik po portalu Azure Marketplace
description: W tym artykule opisano wymagania aby opublikować kontenerów w witrynie Marketplace
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: ellacroi
manager: nunoc
ms.service: marketplace
ms.topic: article
ms.date: 07/09/2018
ms.author: ellacroi
ms.openlocfilehash: e81f2b42e725e50473c09622a486586fb6721ad5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60882184"
---
# <a name="containers-offer-publishing-guide"></a>Kontenery oferują Podręcznik publikowania

Oferuje kontener pomóc Ci publikować z obrazu kontenera z portalu Azure Marketplace. Użyj tego przewodnika, aby poznać wymagania dla tej oferty. 

Są to oferty transakcji, które są wdrożone i rozliczane za pośrednictwem portalu Marketplace. Wywołanie akcji, które użytkownik będzie widział jest "Pobierz teraz."

Gdy Twoje rozwiązanie jest inicjowana jak dla usługi kontenera platformy Azure na podstawie Kubernetes obraz kontenera Docker, należy użyć kontenera typu oferty.

>[!NOTE]
>Na przykład na podstawie Kubernetes usługi Azure container service, takich jak usługi Azure Kubernetes Service lub Azure Container Instances, wybór klientów platformy Azure na podstawie Kubernetes kontener środowiska uruchomieniowego.  

Firma Microsoft obsługuje obecnie bezpłatna i bring-your-own-license (BYOL) Modele licencjonowania.

## <a name="containers-offer"></a>Oferta kontenerów

| Wymaganie | Szczegóły |  
|:--- |:--- |  
| Rozliczeń oraz metod pomiarów | Albo bezpłatną pomoc techniczną lub model rozliczeń w ramach opcji BYOL. |  
| Obraz utworzony na podstawie pliku Dockerfile | Obrazy kontenerów muszą być oparte na specyfikacji obrazu platformy Docker i muszą być zbudowane z pliku Dockerfile.<ul> <li>Aby uzyskać więcej informacji na temat Tworzenie obrazów platformy docker, odwiedź sekcję użycie znajdujący się w [docs.docker.com/engine/reference/builder/#usage](https://docs.docker.com/engine/reference/builder/#usage).</li> </ul> |  
| Hosting w rejestru Azure container Registry | Obrazy kontenera musi być hostowany w repozytorium Azure Container Registry (ACR).<ul> <li>Aby uzyskać więcej informacji na temat pracy z rejestru Azure container Registry odwiedź tego przewodnika Szybki Start: Tworzenie rejestru kontenerów przy użyciu stronę witryny Azure portal znajduje się w [docs.microsoft.com/azure/container-registry/container-registry-get-started-portal](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal).</li> </ul> |  
| Obraz znakowania | Obrazy kontenera musi zawierać co najmniej 1 tag (maksymalna liczba tagów: 16).<ul> <li>Aby uzyskać więcej informacji na temat tagowanie obrazu stronę docker tag znajdujący się w [docs.docker.com/engine/reference/commandline/tag](https://docs.docker.com/engine/reference/commandline/tag).</li> </ul> |  

## <a name="next-steps"></a>Kolejne kroki

Jeśli użytkownik jeszcze tego nie zrobiono, 

- [Zarejestruj](https://azuremarketplace.microsoft.com/sell) w portalu marketplace.

Jeśli jesteś zarejestrowanym i tworzysz nową ofertę czy działa na podstawie istniejącego

- [Zaloguj się do portalu Cloud Partner](https://cloudpartner.azure.com) do utworzenia lub zakończyć oferty.
- Zobacz [kontenery](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-containers-offer) Aby uzyskać więcej informacji.
