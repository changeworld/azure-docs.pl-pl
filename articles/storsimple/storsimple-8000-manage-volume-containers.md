---
title: Zarządzanie kontenerami woluminów dla urządzeń z serii StorSimple 8000
description: W tym artykule wyjaśniono, jak można użyć storsimple usługi kontenerów woluminów usługi, aby dodać, zmodyfikować lub usunąć kontener woluminu.
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
ms.openlocfilehash: cbdad3c68848ce552811ee658bb29df74a6fad19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267679"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-volume-containers"></a>Use the StorSimple Device Manager service to manage StorSimple volume containers (Zarządzanie kontenerami woluminów StorSimple za pomocą usługi Menedżer urządzeń StorSimple)

## <a name="overview"></a>Omówienie
W tym samouczku wyjaśniono, jak używać usługi StorSimple Device Manager do tworzenia kontenerów woluminów StorSimple i zarządzania nimi.

Kontener woluminu na urządzeniu Microsoft Azure StorSimple zawiera co najmniej jeden wolumin, który współużytkuje ustawienia konta magazynu, szyfrowania i zużycia przepustowości. Urządzenie może mieć wiele kontenerów woluminów dla wszystkich woluminów. 

Kontener woluminu ma następujące atrybuty:

* **Woluminy** — warstwowe lub lokalnie przypięte woluminy StorSimple, które znajdują się w kontenerze woluminów. 
* **Szyfrowanie** — klucz szyfrowania, który można zdefiniować dla każdego kontenera woluminu. Ten klucz służy do szyfrowania danych, które są wysyłane z urządzenia StorSimple do chmury. Z kluczem wprowadzonym przez użytkownika jest używany klucz bitowy klasy wojskowej AES-256. Aby zabezpieczyć dane, zalecamy zawsze włączyć szyfrowanie magazynu w chmurze.
* **Konto magazynu** — konto magazynu platformy Azure, które jest używane do przechowywania danych. Wszystkie woluminy zamieszkałe w kontenerze woluminu współużytkują to konto magazynu. Można wybrać konto magazynu z istniejącej listy lub utworzyć nowe konto podczas tworzenia kontenera woluminu, a następnie określić poświadczenia dostępu dla tego konta.
* **Przepustowość chmury** — przepustowość zużywana przez urządzenie, gdy dane z urządzenia są wysyłane do chmury. Kontrolę przepustowości można wymusić, określając wartość od 1 Mb/s do 1000 Mb/s podczas tworzenia tego kontenera. Jeśli chcesz, aby urządzenie zużywało całą dostępną przepustowość, ustaw to pole na **Nieograniczone**. Można również utworzyć i zastosować szablon przepustowości, aby przydzielić przepustowość na podstawie harmonogramu.

Poniższe procedury wyjaśniają, jak używać **pojemników StorSimple Volume** do wykonania następujących typowych operacji:

* Dodawanie kontenera woluminów
* Modyfikowanie kontenera woluminu
* Usuwanie kontenera woluminu

## <a name="add-a-volume-container"></a>Dodawanie kontenera woluminów
Wykonaj następujące kroki, aby dodać kontener woluminu.

[!INCLUDE [storsimple-8000-add-volume-container](../../includes/storsimple-8000-create-volume-container.md)]

## <a name="modify-a-volume-container"></a>Modyfikowanie kontenera woluminu
Wykonaj następujące kroki, aby zmodyfikować kontener woluminu.

[!INCLUDE [storsimple-8000-modify-volume-container](../../includes/storsimple-8000-modify-volume-container.md)]

## <a name="delete-a-volume-container"></a>Usuwanie kontenera woluminu
Kontener woluminu ma woluminy w nim. Można go usunąć tylko wtedy, gdy wszystkie zawarte w nim woluminy zostaną najpierw usunięte. Wykonaj następujące kroki, aby usunąć kontener woluminu.

[!INCLUDE [storsimple-8000-delete-volume-container](../../includes/storsimple-8000-delete-volume-container.md)]

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [zarządzaniu woluminami StorSimple](storsimple-8000-manage-volumes-u2.md). 
* Dowiedz się więcej o [administrowaniu urządzeniem StorSimple za pomocą usługi StorSimple Device Manager.](storsimple-8000-manager-service-administration.md)

