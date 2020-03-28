---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: ce67062f83ad10703dfb1f0ee1f3741e698fb450
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70130104"
---
Co najmniej zalecamy użycie co najmniej 30 obrazów na znacznik w zestawie szkolenia wstępnego. Warto również zebrać kilka dodatkowych obrazów, aby przetestować model, gdy zostanie przeszkolony.

Aby skutecznie trenować model, użyj obrazów o różnorodności wizualnej. Wybierz obrazy, które różnią się w zależności od:
* kąt kamery
* Oświetlenie
* tło
* styl wizualny
* indywidualne/zgrupowane przedmioty
* size
* type

Ponadto upewnij się, że wszystkie obrazy treningowe spełniają następujące kryteria:
* format .jpg, png, .bmp lub .gif
* nie większy niż 6 MB (4 MB dla obrazów przewidywania)
* nie mniej niż 256 pikseli na najkrótszej krawędzi; obrazy krótsze niż te zostaną automatycznie przeskalowane przez usługę Custom Vision Service
