---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: ee2c1fd1fc1cad07b14a2c99318be20be30db9c5
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423595"
---
Jako minimum zalecamy używanie co najmniej 30 obrazów na tag w początkowym zestawie szkoleniowym. Warto również zebrać kilka dodatkowych obrazów w celu przetestowania modelu po jego przeszkoleniu.

Aby efektywnie szkolić model, użyj obrazów z odmianą wizualną. Wybierz obrazy, które różnią się w zależności od:
* kąt kamery
* oświetlenia
* tle
* styl wizualizacji
* osoby/zgrupowane elementy
* size
* type

Ponadto upewnij się, że wszystkie obrazy szkoleniowe spełniają następujące kryteria:
* Format jpg, PNG lub BMP
* nie więcej niż 6MB w rozmiarze (4 MB dla obrazów predykcyjnych)
* nie mniej niż 256 pikseli na najkrótszej krawędzi; Każdy obraz krótszy niż ten zostanie automatycznie przeskalowany w górę przez Custom Vision Service