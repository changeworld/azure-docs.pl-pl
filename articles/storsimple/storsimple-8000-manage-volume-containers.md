---
title: Zarządzanie kontenerów woluminu StorSimple na urządzeniu z serii StorSimple 8000 | Dokumentacja firmy Microsoft
description: W tym artykule wyjaśniono, jak Menedżer urządzeń StorSimple usługi woluminu kontenery strona służy do dodawania, modyfikowania i usuwania kontenera woluminów.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/19/2017
ms.author: alkohli
ms.openlocfilehash: 0f8e00d6d07224f56625482f339e612e68914be2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874843"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-volume-containers"></a>Zarządzanie kontenery woluminów StorSimple przy użyciu usługi Menedżer StorSimple urządzenia

## <a name="overview"></a>Omówienie
W tym samouczku wyjaśniono, jak korzystać z usługi Menedżera urządzeń StorSimple, tworzenie i zarządzanie nimi kontenery woluminów StorSimple.

Kontener woluminów na urządzeniu Microsoft Azure StorSimple zawiera jeden lub więcej woluminów, które mają konta magazynu, szyfrowania i ustawienia zużycie przepustowości. Urządzenie może mieć wiele kontenerów woluminu dla jego woluminów. 

Kontener woluminów obejmuje następujące atrybuty:

* **Woluminy** — warstwowy lub woluminów StorSimple, które znajdują się w kontenerze wolumin przypięty lokalnie. 
* **Szyfrowanie** — klucz szyfrowania, które mogą być definiowane dla każdego kontenera woluminów. Ten klucz służy do szyfrowania danych, które są wysyłane z urządzenia StorSimple w chmurze. Z klasy zbrojnych AES 256-bitowym kluczem jest używany z kluczem wprowadzonych przez użytkownika. Aby zabezpieczyć dane, zalecamy zawsze włączone szyfrowanie magazynu w chmurze.
* **Konto magazynu** — konta magazynu Azure, który jest używany do przechowywania danych. Wszystkie woluminy znajdującej się w kontenerze woluminów udostępnić to konto magazynu. Wybierz konto magazynu z istniejącej listy lub Utwórz nowe konto, podczas tworzenia kontenera woluminu, a następnie określ poświadczenia dostępu dla tego konta.
* **Chmury przepustowości** — przepustowości przez urządzenie po wysłaniu danych z urządzenia do chmury. Można wymusić kontroli przepustowości, określając wartość z zakresu od 1 MB/s do 1000 MB/s, podczas tworzenia tego kontenera. Jeśli chcesz, aby urządzenia pełną dostępną przepustowość, ustaw tego pola **nieograniczone**. Można również utworzyć i zastosować szablon przepustowości przydzielić przepustowość zgodnie z harmonogramem.

W poniższych procedurach opisano sposób użycia StorSimple **kontenery woluminów** bloku, aby ukończyć następujące typowe operacje:

* Dodaj kontener woluminów
* Modyfikowanie kontenera woluminów
* Usunięcie kontenera woluminów

## <a name="add-a-volume-container"></a>Dodaj kontener woluminów
Wykonaj poniższe kroki, aby dodać kontener woluminów.

[!INCLUDE [storsimple-8000-add-volume-container](../../includes/storsimple-8000-create-volume-container.md)]

## <a name="modify-a-volume-container"></a>Modyfikowanie kontenera woluminów
Wykonaj poniższe kroki, aby zmodyfikować kontener woluminów.

[!INCLUDE [storsimple-8000-modify-volume-container](../../includes/storsimple-8000-modify-volume-container.md)]

## <a name="delete-a-volume-container"></a>Usunięcie kontenera woluminów
Kontener woluminów obejmuje woluminy znajdujące się w nim. Można usunąć tylko wtedy, gdy wszystkie woluminy, które są zawarte w niej najpierw zostaną usunięte. Wykonaj poniższe kroki, aby usunąć kontener woluminów.

[!INCLUDE [storsimple-8000-delete-volume-container](../../includes/storsimple-8000-delete-volume-container.md)]

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [zarządzania woluminami StorSimple](storsimple-8000-manage-volumes-u2.md). 
* Dowiedz się więcej o [przy użyciu usługi Menedżer StorSimple urządzenia do administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).

