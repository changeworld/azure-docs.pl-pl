---
title: Zmiana rozmiaru puli pojemności lub woluminu dla plików NetApp platformy Azure | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób zmiany rozmiaru puli pojemności lub woluminu.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: b-juche
ms.openlocfilehash: c58ceef57b984f46b86bb2a8577c53b75082b78b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65794613"
---
# <a name="resize-a-capacity-pool-or-a-volume"></a>Zmienianie rozmiaru puli pojemności lub woluminu
W razie potrzeby można zmienić rozmiar puli pojemności lub woluminu. 

## <a name="resize-the-capacity-pool"></a>Ponowne rozmiary puli pojemności 

Rozmiar puli pojemności można zmienić w przyrostach lub ubytkach 1-TiB. Jednak rozmiar puli pojemności nie może być mniejszy niż 4 TiB. Zmiana rozmiaru puli pojemności zmienia pojemność zakupionej usługi Azure NetApp Files.

1. W bloku Zarządzanie kontem NetApp kliknij pulę pojemności, którą chcesz zmienić. 
2. Kliknij prawym przyciskiem myszy nazwę puli pojemności lub kliknij "..." na końcu wiersza puli pojemności, aby wyświetlić menu kontekstowe. 
3. Użyj opcji menu kontekstowego, aby zmienić rozmiar lub usunąć pulę pojemności.

## <a name="resize-a-volume"></a>Ponowne rozmiary woluminu

W razie potrzeby można zmienić rozmiar woluminu. Użycie pojemności woluminu jest liczone jako użycie aprowizowanej pojemności puli.

1. W bloku Zarządzanie kontem NetApp kliknij pozycję **Woluminy**. 
2. Kliknij prawym przyciskiem myszy nazwę woluminu, którego rozmiar chcesz zmienić, lub kliknij polecenie "..." na końcu wiersza woluminu, aby wyświetlić menu kontekstowe.
3. Użyj opcji menu kontekstowego, aby zmienić rozmiar lub usunąć wolumin.

