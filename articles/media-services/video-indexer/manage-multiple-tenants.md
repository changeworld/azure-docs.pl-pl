---
title: Zarządzanie wieloma dzierżawcami za pomocą indeksatora wideo — Azure
description: W tym artykule sugeruje różne opcje integracji do zarządzania wieloma dzierżawami za pomocą indeksatora wideo.
services: media-services
documentationcenter: ''
author: ika-microsoft
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 05/15/2019
ms.author: ikbarmen
ms.openlocfilehash: 18f2cf3daa281400151ba223e1735e7138d97e8e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76990508"
---
# <a name="manage-multiple-tenants"></a>Zarządzanie wieloma dzierżawami

W tym artykule omówiono różne opcje zarządzania wieloma dzierżawami za pomocą indeksatora wideo. Wybierz metodę, która jest najbardziej odpowiednia dla danego scenariusza:

* Konto indeksatora wideo na dzierżawę
* Konto indeksatora pojedynczego wideo dla wszystkich dzierżawców
* Subskrypcja platformy Azure na dzierżawę

## <a name="video-indexer-account-per-tenant"></a>Konto indeksatora wideo na dzierżawę

Podczas korzystania z tej architektury konto indeksatora wideo jest tworzony dla każdej dzierżawy. Dzierżawcy mają pełną izolację w warstwie trwałe i obliczeniowej.  

![Konto indeksatora wideo na dzierżawę](./media/manage-multiple-tenants/video-indexer-account-per-tenant.png)

### <a name="considerations"></a>Zagadnienia do rozważenia

* Klienci nie udostępniają kont magazynu (chyba że klient ręcznie skonfigurowany).
* Klienci nie współużytkują jednostki obliczeniowe (jednostki zarezerwowane) i nie mają wpływu na czasy zadań przetwarzania między sobą.
* Dzierżawę można łatwo usunąć z systemu, usuwając konto indeksatora wideo.
* Nie ma możliwości udostępniania modeli niestandardowych między dzierżawami.

    Upewnij się, że nie ma wymagań biznesowych do udostępniania modeli niestandardowych.
* Trudniejsze do zarządzania ze względu na wiele kont indeksatora wideo (i skojarzone usługi Media Services) na dzierżawę.

> [!TIP]
> Utwórz administratora dla swojego systemu w [portalu deweloperskim indeksatora](https://api-portal.videoindexer.ai/) wideo i użyj interfejsu API autoryzacji, aby zapewnić dzierżawcom odpowiedni [token dostępu do konta.](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Account-Access-Token)

## <a name="single-video-indexer-account-for-all-users"></a>Pojedyncze konto indeksatora wideo dla wszystkich użytkowników

Podczas korzystania z tej architektury, klient jest odpowiedzialny za izolację dzierżawców. Wszyscy dzierżawcy muszą używać jednego konta indeksatora wideo z jednym kontem usługi Azure Media Service. Podczas przekazywania, wyszukiwania lub usuwania zawartości klient będzie musiał filtrować odpowiednie wyniki dla tej dzierżawy.

![Pojedyncze konto indeksatora wideo dla wszystkich użytkowników](./media/manage-multiple-tenants/single-video-indexer-account-for-all-users.png)

Za pomocą tej opcji modele dostosowywania (Osoba, Język i marki) mogą być współużytkowane lub izolowane między dzierżawcami przez filtrowanie modeli według dzierżawy.

Podczas [przesyłania filmów](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?)można określić inny atrybut partycji na dzierżawę. Pozwoli to na izolację w [interfejsie API wyszukiwania](https://api-portal.videoindexer.ai/docs/services/operations/operations/Search-videos?). Określając atrybut partycji w interfejsie API wyszukiwania, otrzymasz tylko wyniki określonej partycji. 

### <a name="considerations"></a>Zagadnienia do rozważenia

* Możliwość udostępniania zawartości i dostosowywania modeli między dzierżawami.
* Jedna dzierżawa wpływa na wydajność innych dzierżaw.
* Klient musi zbudować złożoną warstwę zarządzania na podstawie indeksatora wideo.

> [!TIP]
> Atrybut [priorytetu](upload-index-videos.md) służy do określania priorytetów zadań dzierżawców.

## <a name="azure-subscription-per-tenant"></a>Subskrypcja platformy Azure na dzierżawę 

Podczas korzystania z tej architektury, każda dzierżawa będzie mieć własną subskrypcję platformy Azure. Dla każdego użytkownika utworzysz nowe konto indeksatora wideo w subskrypcji dzierżawy.

![Subskrypcja platformy Azure na dzierżawę](./media/manage-multiple-tenants/azure-subscription-per-tenant.png)

### <a name="considerations"></a>Zagadnienia do rozważenia

* Jest to jedyna opcja, która umożliwia rozdzielanie rozliczeń.
* Ta integracja ma więcej kosztów administracyjnych niż konto indeksatora wideo na dzierżawę. Jeśli fakturowanie nie jest wymagane, zaleca się użycie jednej z innych opcji opisanych w tym artykule.

## <a name="next-steps"></a>Następne kroki

[Przegląd](video-indexer-overview.md)
