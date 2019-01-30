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
ms.date: 01/09/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: 6c1750e6a523828400b3d06d4e1c22fb34f8273f
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247871"
---
# <a name="azure-stack-marketplace-overview"></a>Omówienie usługi Azure Stack w portalu Marketplace

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

W portalu Azure Marketplace stosu to zbiór usług, aplikacji i zasobów dostosowane do usługi Azure Stack. Zasoby obejmują sieci, maszyn wirtualnych, magazynu i inne. Użyj portalu Marketplace, aby tworzyć nowe zasoby i wdrażanie nowych aplikacji; Przeglądaj i wybierz elementy, które chcesz użyć. Aby użyć elementu portalu Marketplace, użytkownicy muszą subskrybować oferty, który przyznaje im dostępu do elementu.

Jako operatorów usługi Azure Stack możesz wybierać elementy, które można dodać (publikowanie) w portalu Marketplace. Można publikować elementy, takie jak bazy danych, App Services i tak dalej. Publikowanie sprawia, że ich widoczne dla wszystkich użytkowników. Możesz opublikować niestandardowe elementy, które tworzysz lub opublikować elementy z rosnącej [listy elementów w portalu Azure Marketplace](azure-stack-marketplace-azure-items.md). Podczas publikowania elementu portalu Marketplace, użytkownicy mogli je zobaczyć w ciągu pięciu minut.

> [!CAUTION]  
> Wszystkie artefakty elementu galerii, w tym obrazy i pliki w formacie JSON, są dostępne bez uwierzytelniania po ich udostępnienie w witrynie Azure Marketplace stosu. Więcej uwagi podczas publikowania elementów portalu marketplace niestandardowych można znaleźć [tworzenie i publikowanie elementu portalu Marketplace](azure-stack-create-and-publish-marketplace-item.md).

Aby otworzyć w portalu Marketplace w portalu wybierz opcję administrator **+ Utwórz zasób**.

![Portal Marketplace](media/azure-stack-publish-custom-marketplace-item/image1.png)

## <a name="marketplace-items"></a>Elementy portalu Marketplace

Element usługi Azure Stack w portalu Marketplace jest usługa, aplikacji lub zasobu, który użytkownicy można pobrać do użycia. Wszystkie elementy usługi Azure Stack w portalu Marketplace są widoczne dla wszystkich użytkowników, w tym elementy administracyjne, takie jak plany i oferty. Te elementy nie wymagają subskrypcji do widoku, ale to nie działa dla użytkowników.

Każdy element Marketplace ma:

* Szablon usługi Azure Resource Manager do aprowizacji zasobów.
* Metadane, takie jak ciągi, ikony i inne materiały marketingowe.
* Informacje o formatowaniu do wyświetlania elementu w portalu.

Każdy element opublikowanych w portalu Marketplace w formacie pakietu galerii platformy Azure (.azpkg). Dodaj oddzielnie, wdrażania lub środowisko uruchomieniowe zasobów (kod, pliki zip z oprogramowania i obrazów maszyn wirtualnych) do usługi Azure Stack nie jako część elementu portalu Marketplace.

Za pomocą wersji 1803 i nowsze usługi Azure Stack konwertuje obrazy plików rozrzedzonych podczas pobierania na platformie Azure lub podczas przekazywania obrazów niestandardowych. Ten proces powoduje dodanie czasu podczas dodawania obrazu, ale pozwala zaoszczędzić miejsce i przyspiesza wdrażanie tych obrazów. Konwersja ma zastosowanie tylko do nowych obrazów. Istniejących obrazów nie są zmieniane.

## <a name="next-steps"></a>Kolejne kroki

* [Pobieranie elementów portalu Marketplace](azure-stack-download-azure-marketplace-item.md)  
* [Tworzenie i publikowanie elementu portalu Marketplace](azure-stack-create-and-publish-marketplace-item.md)
