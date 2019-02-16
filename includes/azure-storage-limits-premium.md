---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 01/22/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 3d100ec08b7b6d70366e605daf9c67edc6ab3bf3
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2019
ms.locfileid: "56331072"
---
Kont usługi Premium storage ma następujące cele skalowalności:

| Łączna liczba kont pojemności | Całkowitej przepustowości dla konta magazynu lokalnie nadmiarowego |
| --- | --- | 
| Pojemność dysku: 35 TB <br>Pojemność migawki: 10 TB | Się do 50 gigabity na sekundę dla ruchu przychodzącego<sup>1</sup> + wychodzącego<sup>2</sup> |

<sup>1</sup> wszystkich danych (żądań), które są wysyłane do konta magazynu

<sup>2</sup> wszystkich danych (żądań), które są odbierane z konta magazynu

Jeśli używasz kont usługi premium storage dla dysków niezarządzanych aplikacji przekracza cele skalowalności z jednego konta magazynu, można migrować do usługi managed disks. Jeśli nie chcesz przeprowadzić migrację do usługi managed disks, tworzenie aplikacji na używanie wielu kont magazynu. Następnie podzielić dane na tych kontach magazynu. Na przykład jeśli chcesz dołączyć dyski 51 – TB na wielu maszynach wirtualnych, rozkładają się je na dwa konta magazynu. 35 TB jest limit dla konta magazynu premium jednego. Upewnij się, że konto magazynu premium pojedynczego nigdy nie ma więcej niż 35 TB aprowizowanego dysku.