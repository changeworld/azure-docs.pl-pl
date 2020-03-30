---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/09/2019
ms.service: storage
ms.subservice: blobs
ms.topic: include
ms.reviewer: hux
ms.custom: include file
ms.openlocfilehash: 81ffc87ce97a936e693c59bca6caf721cb8599cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75780248"
---
Aby odczytać dane w magazynie archiwalnym, należy najpierw zmienić warstwę obiektu blob na gorącą lub chłodną. Proces ten jest znany jako nawodnienie i może potrwać wiele godzin. Zalecamy duże rozmiary obiektów blob dla optymalnej wydajności nawodnienia. Ponowne wypełnianie kilku małych obiektów blob równocześnie może spowodować dalsze przedłużenie czasu. Obecnie istnieją dwa priorytety ponownego nawodnienia, Wysoki (wersja zapoznawcza) i Standard, które można ustawić za pomocą opcjonalnej właściwości *x-ms-rehydrate-priority* w operacji [Set Blob Tier](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) lub [Copy Blob.](https://docs.microsoft.com/rest/api/storageservices/copy-blob)

* **Standardowy priorytet**: Wniosek o nawodnienie zostanie rozpatrzony w kolejności, w jakiej został otrzymany i może potrwać do 15 godzin.
* **Wysoki priorytet (wersja zapoznawcza)**: Żądanie nawadniania będzie traktowane priorytetowo w stosunku do żądań standardowych i może zakończyć się w mniej niż 1 godzinę. Wysoki priorytet może trwać dłużej niż 1 godzinę, w zależności od rozmiaru obiektu blob i bieżącego zapotrzebowania. Żądania o wysokim priorytecie są gwarantowane jako priorytetowe w stosunku do żądań priorytetu standardowego.

> [!NOTE]
> Standardowy priorytet jest domyślną opcją nawodnienia dla archiwum. Wysoki priorytet to szybsza opcja, która będzie kosztować więcej niż standardowe nawodnienie priorytetowe i jest zwykle zarezerwowana do użytku w sytuacjach przywracania danych w sytuacjach awaryjnych.

Po zainicjowaniu żądania ponownego nawodnienia nie można go anulować. Podczas ponownego wypełniania można sprawdzać właściwość obiektu blob *Stan archiwum*, aby upewnić się, że warstwa została zmieniona. Właściwość ta ma wartość „rehydrate-pending-to-hot” (ponowne wypełnianie w celu przejścia do warstwy gorącej) lub „rehydrate-pending-to-cool” (ponowne wypełnianie w celu przejścia do warstwy chłodnej) w zależności od warstwy docelowej. Po zakończeniu tego procesu właściwość obiektu blob „stan archiwum” jest usuwana, a wartość właściwości *Warstwa dostępu* odpowiada nowej warstwie Gorąca lub Chłodna.
