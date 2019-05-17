---
title: Zarządzanie wieloma dzierżawami przy użyciu Video Indexer — Azure
description: W tym artykule sugeruje integracji różne opcje zarządzania wielu dzierżaw za pomocą indeksatora wideo.
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
ms.openlocfilehash: 2919e021d6b70ce82a6ff6b1d1972dd89de95104
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65799485"
---
# <a name="manage-multiple-tenants"></a>Zarządzanie wieloma dzierżawami

W tym artykule omówiono różne opcje do zarządzania wieloma dzierżawami za pomocą indeksatora wideo. Wybierz metodę, która jest najbardziej odpowiednie dla danego scenariusza:

* Wideo konta indeksatora na dzierżawę
* Konto usługi Video Indexer jednego dla wszystkich dzierżaw
* Subskrypcja platformy Azure dla dzierżawy

## <a name="video-indexer-account-per-tenant"></a>Wideo konta indeksatora na dzierżawę

Korzystając z tej architektury, konto usługi Video Indexer jest tworzony dla każdej dzierżawy. Dzierżawcy mają pełną izolację w trwałych i obliczeniowych warstwy.  

![Wideo konta indeksatora na dzierżawę](./media/manage-multiple-tenants/video-indexer-account-per-tenant.png)

### <a name="considerations"></a>Zagadnienia do rozważenia

* Klienci nie należy udostępniać konta magazynu (chyba że ręcznie skonfigurowane przez klienta).
* Klienci nie mają obliczeniowych (jednostek zarezerwowanych) i nie wpływać na czas zadania przetwarzania od siebie.
* Można łatwo usunąć dzierżawę z systemu, usuwając konto usługi Video Indexer.
* Istnieje możliwość udostępniania niestandardowe modele między dzierżawami.

    Upewnij się, że nie jest wymagane Udostępnianie niestandardowych modeli biznesowych.
* Trudniejsze do zarządzania z powodu wielu Video Indexer (i skojarzone usługi Media Services) konta w dzierżawie.

> [!TIP]
> Utwórz konto administratora systemu w [portalu dla deweloperów indeksatora wideo](https://api-portal.videoindexer.ai/) i zapewnić odpowiedni dzierżawcom za pomocą interfejsu API autoryzacji [tokenu dostępu do konta](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token).

## <a name="single-video-indexer-account-for-all-users"></a>Konto usługi Video Indexer jednego dla wszystkich użytkowników

Korzystając z tej architektury, klient jest odpowiedzialny za izolacji dzierżawców. Wszystkich dzierżaw musi używać jednego konta indeksatora wideo za pomocą jednego konta usługi Azure Media Service. Podczas przekazywania, wyszukiwania lub usuwania zawartości, klient będzie musiał filtrować wyniki właściwe dla tej dzierżawy.

![Konto usługi Video Indexer jednego dla wszystkich użytkowników](./media/manage-multiple-tenants/single-video-indexer-account-for-all-users.png)

Po wybraniu tej opcji dostosowywania modeli (osoby, języka i marek) można udostępnione lub izolowany między dzierżawami, filtrując modele przez dzierżawcę.

Gdy [przekazywanie filmów wideo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?), można określić atrybutu innej partycji w dzierżawie. Dzięki temu będzie izolacji w [interfejsu API wyszukiwania](https://api-portal.videoindexer.ai/docs/services/operations/operations/Search-videos?). Przez określenie atrybutu partycji w interfejsie API wyszukiwania zostanie wyświetlony tylko wyniki określonej partycji. 

### <a name="considerations"></a>Zagadnienia do rozważenia

* Możliwość udostępniania zawartości i dostosowywania modeli między dzierżawami.
* Jednej dzierżawy ma wpływ na wydajność innych dzierżaw.
* Klient potrzebuje do tworzenia złożonych zarządzania warstwą Video Indexer.

> [!TIP]
> Możesz użyć [priorytet](upload-index-videos.md) atrybutu zadaniom dzierżaw.

## <a name="azure-subscription-per-tenant"></a>Subskrypcja platformy Azure dla dzierżawy 

Korzystając z tej architektury, każda dzierżawa ma własnej subskrypcji platformy Azure. Dla każdego użytkownika utworzysz nowe konto usługi Video Indexer w ramach subskrypcji dzierżawy.

![Subskrypcja platformy Azure dla dzierżawy](./media/manage-multiple-tenants/azure-subscription-per-tenant.png)

### <a name="considerations"></a>Zagadnienia do rozważenia

* Jest to jedyna opcja, która umożliwia rozdzielenie rozliczeń.
* Integracja ta ma większe koszty zarządzania niż konto usługi Video Indexer na dzierżawę. Karta nie jest to wymagane, zalecane jest aby użyć jednego z innych opcji, które opisano w tym artykule.

## <a name="next-steps"></a>Kolejne kroki

[Omówienie](video-indexer-overview.md)
