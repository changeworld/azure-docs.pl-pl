---
title: Zarządzaj kontenerami woluminów StorSimple na urządzeniu StorSimple 8000 series | Dokumentacja firmy Microsoft
description: Wyjaśnia, jak można użyć strony kontenery woluminów usługi Menedżer urządzeń StorSimple można dodać, zmodyfikować lub usunąć kontenera woluminów.
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
ms.openlocfilehash: 7e1a5ac2c2b734c77fc3dbe788206f8c75044953
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60724741"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-volume-containers"></a>Zarządzanie kontenerami woluminów StorSimple przy użyciu usługi Menedżer urządzeń StorSimple

## <a name="overview"></a>Omówienie
W tym samouczku wyjaśniono, jak za pomocą usługi Menedżer urządzeń StorSimple tworzenie i Zarządzanie kontenerami woluminów StorSimple.

Kontener woluminów na urządzeniu StorSimple systemu Azure firmy Microsoft zawiera jeden lub więcej woluminów, które współużytkują konta magazynu, szyfrowania i ustawienia zużycie przepustowości. Urządzenie może mieć wiele kontenerów woluminów dla jego woluminów. 

Kontener woluminów ma następujące atrybuty:

* **Woluminy** — warstwowe lub lokalnie przypięte woluminy StorSimple, które są zawarte w kontenerze woluminów. 
* **Szyfrowanie** — kluczem szyfrowania, które mogą być definiowane dla każdego kontenera woluminów. Ten klucz jest używany do szyfrowania danych, które są wysyłane z urządzenia StorSimple w chmurze. Z klasy korporacyjnej military AES-256-bitowym kluczem jest używany z kluczem wprowadzonych przez użytkownika. Aby zabezpieczyć swoje dane, zalecamy zawsze włączone szyfrowanie magazynu w chmurze.
* **Konto magazynu** — konto magazynu platformy Azure, który służy do przechowywania danych. Wszystkie woluminy znajdujące się w kontenerze woluminów udostępnić to konto magazynu. Wybierz konto magazynu z istniejącej listy lub Utwórz nowe konto, podczas tworzenia kontenera woluminów, a następnie określ poświadczenia dostępu dla tego konta.
* **Przepustowość w chmurze** — przepustowość, używane przez urządzenie, gdy dane z urządzenia są wysyłane do chmury. Za wymuszania kontroli przepustowości, określając wartość z zakresu od 1 do 1000 MB/s, podczas tworzenia tego kontenera. Jeśli chcesz, aby urządzenie korzystało z wszystkie dostępne przepustowości, Ustaw to pole **nieograniczone**. Można również tworzyć i Zastosuj szablon przepustowości, aby przydzielić przepustowość zgodnie z harmonogramem.

W poniższych procedurach opisano sposób użycia usługi StorSimple **kontenery woluminów** bloku, aby wykonać następujące typowe operacje:

* Dodawanie kontenera woluminów
* Modyfikuj kontener woluminu
* Usuwanie kontenera woluminów

## <a name="add-a-volume-container"></a>Dodawanie kontenera woluminów
Wykonaj poniższe kroki, aby dodać kontener woluminów.

[!INCLUDE [storsimple-8000-add-volume-container](../../includes/storsimple-8000-create-volume-container.md)]

## <a name="modify-a-volume-container"></a>Modyfikuj kontener woluminu
Wykonaj poniższe kroki, aby zmodyfikować kontener woluminów.

[!INCLUDE [storsimple-8000-modify-volume-container](../../includes/storsimple-8000-modify-volume-container.md)]

## <a name="delete-a-volume-container"></a>Usuwanie kontenera woluminów
Kontener woluminów obejmuje woluminy znajdujące się w nim. Można usunąć tylko wtedy, gdy wszystkie woluminy, które są zawarte w nim najpierw są usuwane. Wykonaj następujące czynności, można usunąć kontenera woluminów.

[!INCLUDE [storsimple-8000-delete-volume-container](../../includes/storsimple-8000-delete-volume-container.md)]

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [Zarządzanie woluminów StorSimple](storsimple-8000-manage-volumes-u2.md). 
* Dowiedz się więcej o [przy użyciu usługi Menedżer urządzeń StorSimple do administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).

