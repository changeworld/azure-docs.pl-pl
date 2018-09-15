---
title: Publikowanie elementu portalu marketplace w niestandardowych w usłudze Azure Stack (operator chmury) | Dokumentacja firmy Microsoft
description: Jako operatorów usługi Azure Stack Dowiedz się, jak publikowanie elementu portalu marketplace w niestandardowych w usłudze Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 60871cbb-eed2-433c-a76d-d605c7aec06c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2018
ms.author: sethm
ms.reviewer: jeffgo
ms.openlocfilehash: 12310c088777d65bef211747806f942433857e40
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2018
ms.locfileid: "45632352"
---
# <a name="the-azure-stack-marketplace-overview"></a>Omówienie usługi Azure Stack w portalu Marketplace

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

Portal Marketplace to zbiór usług, aplikacji i zasobów dostosowane do usługi Azure Stack. Zasoby obejmują sieci, maszyn wirtualnych, magazynu i tak dalej. Użytkownicy przejdź tutaj, aby tworzyć nowe zasoby i wdrażanie nowych aplikacji. Go traktować jako zakupów katalog, w którym użytkownicy mogą przeglądać i wybierz elementy, które mają być użyte. Aby użyć elementu portalu Marketplace, użytkownicy muszą subskrybować oferty, który przyznaje im dostępu do elementu.

Jako operatorów usługi Azure Stack możesz wybierać elementy, które można dodać (publikowanie) w portalu Marketplace. Możesz opublikować elementów, takich jak bazy danych, App Services i tak dalej. Publikowanie sprawia, że ich widoczne dla wszystkich użytkowników. Możesz opublikować niestandardowe elementy, które tworzysz. Możesz również publikować elementy z rosnącej [listy elementów w portalu Azure Marketplace](azure-stack-marketplace-azure-items.md). Podczas publikowania elementu portalu Marketplace, użytkownicy mogli je zobaczyć w ciągu pięciu minut.

> [!Caution]  
> Wszystkie artefakty elementu galerii, znane jako obrazy i pliki w formacie json, są dostępne bez uwierzytelniania po ich udostępnienie w portalu marketplace usługi Azure Stack. Więcej uwagi podczas publikowania elementów portalu marketplace niestandardowych można znaleźć [tworzenie i publikowanie elementu portalu Marketplace](azure-stack-create-and-publish-marketplace-item.md).

Aby otworzyć w portalu Marketplace, w konsoli administracyjnej wybierz **+ Utwórz zasób**.

![](media/azure-stack-publish-custom-marketplace-item/image1.png)

## <a name="marketplace-items"></a>Elementy portalu Marketplace
Element usługi Azure Stack w portalu Marketplace jest usługa, aplikacji lub zasobu, który użytkownicy można pobrać do użycia. Wszystkie elementy usługi Azure Stack w portalu Marketplace są widoczne dla wszystkich użytkowników, w tym elementy administracyjne, takie jak plany i oferty. Te elementy nie wymagają subskrypcji do widoku, ale to nie działa dla użytkowników.

Każdy element Marketplace ma:

* Inicjowanie obsługi zasobu szablon usługi Azure Resource Manager
* Metadane, takie jak ciągi, ikony i inne materiały marketingowe
* Informacje o formatowaniu do wyświetlania elementu w portalu

Każdy element opublikowanych w portalu Marketplace w formacie pakietu galerii platformy Azure (.azpkg). Dodaj oddzielnie, wdrażania lub środowisko uruchomieniowe zasoby (na przykład kod, pliki zip z oprogramowania i obrazów maszyn wirtualnych) do usługi Azure Stack nie jako część elementu portalu Marketplace. 

Za pomocą wersji 1803 i nowsze usługi Azure Stack konwertuje obrazy plików rozrzedzonych podczas pobierania na platformie Azure lub podczas przekazywania obrazów niestandardowych. Ten proces powoduje dodanie czasu podczas dodawania obrazu, ale pozwala zaoszczędzić miejsce i przyspiesza wdrażanie tych obrazów. Konwersja ma zastosowanie tylko do nowych obrazów.  Istniejących obrazów nie są zmieniane. 

## <a name="next-steps"></a>Kolejne kroki
[Pobieranie elementów portalu Marketplace](azure-stack-download-azure-marketplace-item.md)  
[Tworzenie i publikowanie elementu portalu Marketplace](azure-stack-create-and-publish-marketplace-item.md)

