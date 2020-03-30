---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: ed061234da27716bfcdaf1cc065568908108f90a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597865"
---
Wszystkie dane przechowywane w usłudze Azure Files są szyfrowane w spoczynku przy użyciu szyfrowania usługi magazynu platformy Azure (SSE). Szyfrowanie usługi magazynowania działa podobnie do funkcji BitLocker w systemie Windows: dane są szyfrowane poniżej poziomu systemu plików. Ponieważ dane są szyfrowane w systemie plików udziału plików platformy Azure, ponieważ jest zakodowany na dysku, nie trzeba mieć dostępu do klucza źródłowego na kliencie do odczytu lub zapisu do udziału plików platformy Azure.

Domyślnie dane przechowywane w plikach Azure są szyfrowane za pomocą kluczy zarządzanych przez firmę Microsoft. Dzięki kluczom zarządzanym przez firmę Microsoft firma Microsoft przechowuje klucze do szyfrowania/odszyfrowywania danych i jest odpowiedzialna za ich regularne obracanie. Można również wybrać zarządzanie własnymi kluczami, co daje kontrolę nad procesem obracania. Jeśli zdecydujesz się zaszyfrować udziały plików za pomocą kluczy zarządzanych przez klienta, usługa Azure Files jest autoryzowana do uzyskiwania dostępu do kluczy w celu spełnienia żądań odczytu i zapisu od klientów. Za pomocą kluczy zarządzanych przez klienta można odwołać tę autoryzację w dowolnym momencie, ale oznacza to, że udział plików platformy Azure nie będzie już dostępny za pośrednictwem protokołu SMB lub interfejsu API FileREST.

Usługa Azure Files używa tego samego schematu szyfrowania, co inne usługi magazynu platformy Azure, takie jak magazyn obiektów blob platformy Azure. Aby dowiedzieć się więcej o szyfrowaniu usługi azure storage (SSE), zobacz [Szyfrowanie magazynu platformy Azure w celu uzyskania danych w spoczynku.](../articles/storage/common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)