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
ms.openlocfilehash: 03674a51566ab89791725d1a51c7af12ed6949e5
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/12/2019
ms.locfileid: "68952891"
---
Aby odczytać dane w magazynie archiwalnym, należy najpierw zmienić warstwę obiektu blob na gorącą lub chłodną. Ten proces jest nazywany odpełnieniem i może trwać kilka godzin. Zalecamy duże rozmiary obiektów BLOB w celu uzyskania optymalnej wydajności. Ponowne wypełnianie kilku małych obiektów blob równocześnie może spowodować dalsze przedłużenie czasu. Obecnie istnieją dwa priorytety odnoszące się do wartości (wersja zapoznawcza) i Standard, które można ustawić za pośrednictwem opcjonalnej właściwości " *x-MS-dehydratacji-Priority"* w [ustawieniu warstwy obiektu BLOB](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) lub operacji [kopiowania obiektu BLOB](https://docs.microsoft.com/rest/api/storageservices/copy-blob) .

* **Priorytet standardowy**: Żądanie odtworzenia zostanie przetworzone w kolejności, w jakiej zostało odebrane, i może trwać do 15 godzin.
* **Wysoki priorytet (wersja zapoznawcza)** : Żądania ponownego wypełniania będą ustalane według priorytetów w przypadku żądań standardowych i mogą kończyć się w ciągu 1 godziny. Wysoki priorytet może potrwać dłużej niż 1 godzina, w zależności od rozmiaru obiektu BLOB i bieżącego zapotrzebowania. Priorytet żądań o wysokim priorytecie jest gwarantowany przez żądania standardowego priorytetu.

> [!NOTE]
> Priorytet standardowy to domyślna opcja uzupełniania dla archiwum. Wysoki priorytet to szybsza opcja, która będzie kosztowała więcej niż normalne uzupełnianie priorytetów i jest zazwyczaj zarezerwowana do użycia w sytuacjach awaryjnych przywracania danych.

Podczas ponownego wypełniania można sprawdzać właściwość obiektu blob *Stan archiwum*, aby upewnić się, że warstwa została zmieniona. Właściwość ta ma wartość „rehydrate-pending-to-hot” (ponowne wypełnianie w celu przejścia do warstwy gorącej) lub „rehydrate-pending-to-cool” (ponowne wypełnianie w celu przejścia do warstwy chłodnej) w zależności od warstwy docelowej. Po zakończeniu tego procesu właściwość obiektu blob „stan archiwum” jest usuwana, a wartość właściwości *Warstwa dostępu* odpowiada nowej warstwie Gorąca lub Chłodna.
