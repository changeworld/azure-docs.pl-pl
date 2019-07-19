---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/26/2018
ms.author: jeking
ms.custom: include file
ms.openlocfilehash: 3aa5310589101fa66fd70cc8d5449fbef80f02fa
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68286324"
---
Magazyn Geograficznie nadmiarowy (GRS) został zaprojektowany z założenia, że co najmniej 99.99999999999999% (16 9) trwałości obiektów w danym roku przez replikowanie danych do regionu pomocniczego, który znajduje się w setki kilometrów od regionu podstawowego. Jeśli konto magazynu ma włączone GRS, dane są trwałe nawet w przypadku pełnej awarii regionalnej lub awarii, w której region podstawowy nie jest możliwy do odzyskania.

W przypadku wybrania opcji GRS dostępne są dwie powiązane opcje do wyboru:

* GRS replikuje dane do innego centrum danych w regionie pomocniczym, ale dane te są dostępne tylko do odczytu, jeśli firma Microsoft zainicjuje tryb failover z regionu podstawowego do pomocniczego. 
* Magazyn Geograficznie nadmiarowy do odczytu (RA-GRS) jest oparty na GRS. RA-GRS replikuje dane do innego centrum danych w regionie pomocniczym, a także udostępnia opcję odczytu z regionu pomocniczego. W przypadku usługi RA-GRS można odczytać z regionu pomocniczego, niezależnie od tego, czy firma Microsoft inicjuje przejście w tryb failover z regionu podstawowego do pomocniczego. 

W przypadku konta magazynu z włączonym GRS lub RA-GRS wszystkie dane są najpierw replikowane przy użyciu magazynu lokalnie nadmiarowego (LRS). Aktualizacja jest najpierw zatwierdzana do lokalizacji podstawowej i replikowana przy użyciu LRS. Aktualizacja jest następnie replikowana asynchronicznie do regionu pomocniczego za pomocą GRS. Gdy dane są zapisywane w lokalizacji dodatkowej, są również replikowane w tej lokalizacji przy użyciu LRS. 

Zarówno region podstawowy, jak i pomocniczy zarządzają replikami w osobnych domenach błędów i uaktualniania domen w ramach jednostki skalowania magazynu. Jednostka skali magazynu jest podstawową jednostką replikacji w centrum danych. Replikacja na tym poziomie jest zapewniana przez LRS; Aby uzyskać więcej informacji, [zobacz Magazyn lokalnie nadmiarowy (LRS): Low-cost data redundancy for Azure Storage](../articles/storage/common/storage-redundancy-lrs.md) (Magazyn lokalnie nadmiarowy: nadmiarowość danych o niskich kosztach dla usługi Azure Storage).

Przed wybraniem opcji replikacji należy pamiętać o następujących kwestiach:

* Magazyn strefowo nadmiarowy (ZRS) zapewnia wysoką dostępność z replikacją synchroniczną i może być lepszym wyborem w przypadku niektórych scenariuszy niż GRS lub RA-GRS. Aby uzyskać więcej informacji na temat ZRS, zobacz [ZRS](../articles/storage/common/storage-redundancy-zrs.md).
* Replikacja asynchroniczna polega na opóźnieniu od momentu zapisania danych w regionie podstawowym, gdy zostanie ono zreplikowane do regionu pomocniczego. W przypadku awarii regionalnej zmiany, które nie zostały jeszcze zreplikowane do regionu pomocniczego, mogą zostać utracone, jeśli nie można odzyskać tych danych z regionu podstawowego.
* W programie GRS replika nie jest dostępna do odczytu lub zapisu, chyba że firma Microsoft zainicjuje przejście w tryb failover do regionu pomocniczego. W przypadku przejścia w tryb failover będziesz mieć dostęp do odczytu i zapisu do tych danych po zakończeniu pracy w trybie failover. Aby uzyskać więcej informacji, zobacz [wskazówki dotyczące odzyskiwania po awarii](../articles/storage/common/storage-disaster-recovery-guidance.md).
* Jeśli aplikacja wymaga odczytania z regionu pomocniczego, Włącz RA-GRS.
