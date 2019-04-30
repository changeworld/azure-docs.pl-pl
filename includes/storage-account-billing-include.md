---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/19/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: dfb76a14f7e177211e5e8891005544e20f19d3f3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61483652"
---
Opłaty są naliczane na podstawie użycia konta magazynu w usłudze Azure Storage. Wszystkie obiekty w koncie magazynu są rozliczane wspólnie jako grupa. 

Koszty magazynowania są obliczane na podstawie następujących czynników: regionu/lokalizacji, typu konta, Warstwa dostępu, pojemności magazynu, schemacie replikacji, transakcje magazynowe i wyjście danych.

* **Region** odwołuje się do regionu geograficznego, w którym opiera się Twoje konto.
* **Typ konta** odwołuje się do typu konta magazynu, którego używasz. 
* **Warstwa dostępu** odwołuje się do wzorca użycia danych określony dla konta usługi Blob storage lub ogólnego przeznaczenia w wersji 2.
* Magazyn **pojemności** odnosi się do ilości przydziału konta usługi storage jest używany do przechowywania danych.
* **Replikacja** określa liczbę kopii danych są obsługiwane w tym samym czasie i ich lokalizacje.
* **Transakcje** odnoszą się do wszystkich operacji odczytu i zapisu do usługi Azure Storage.
* **Wyjście danych** odwołuje się do wszelkie dane przesyłane poza region platformy Azure. Jeśli do danych na koncie magazynu uzyskuje się dostęp za pośrednictwem aplikacji, która nie działa w tym samym regionie, naliczane są opłaty za wyjście danych. Aby dowiedzieć się, jak przy użyciu grup zasobów do grupy danych i usług, w tym samym regionie, ograniczenie opłat za ruch wychodzący, zobacz [co to jest grupa zasobów platformy Azure?](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/azure-resource-access#what-is-an-azure-resource-group). 

Strona [Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/) zawiera szczegółowe informacje o cenach w zależności od typu konta, pojemności magazynu, replikacji i transakcji. Artykuł [Szczegóły cennika transferów danych](https://azure.microsoft.com/pricing/details/data-transfers/) zawiera szczegółowe informacje o cenach za wyjście danych. Artykuł [Kalkulator cen usługi Azure Storage](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) zawiera informacje, które ułatwią szacowanie kosztów.

