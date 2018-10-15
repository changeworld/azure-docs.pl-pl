---
title: Za pomocą tagów w usłudze Azure Content Moderator | Dokumentacja firmy Microsoft
description: Pakiet Content Moderator obejmuje znaczników domyślnych i możesz utworzyć niestandardowe znaczniki dla moderowanie zawartości specyficzne dla Twojej firmy.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/25/2017
ms.author: sajagtap
ms.openlocfilehash: c462ff2937453f942db7fdd5b751f3356b6fe715
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2018
ms.locfileid: "49310083"
---
# <a name="about-tags"></a>Informacje o znacznikach #

Oprócz tagów dwóch domyślnych **isadult** (**a**) i **isracy** (**r**), niestandardowe tagi można utworzyć więcej docelowej skanowania. Znaczniki niestandardowe będą dostępne dla człowieka recenzentów do przypisania do obrazów lub tekstu.

## <a name="create-tags"></a>Utworzenie tagów ##

1.  Wybierz tagi z karty Ustawienia.

  ![Tagi moderowanie zawartości](images/tags-1.png)

2.  Wprowadź kod krótki dwuliterowych dla tagu.
3.  Wprowadź nazwę tagu. Zachowaj nazwę krótki i opisowy. Na przykład **isbullying**.
4.  Wprowadź opis.
5.  Kliknij pozycję Add (Dodaj).
6.  Po zakończeniu tworzenia tagów, kliknij przycisk Zapisz.

![Definiowanie tagów moderowanie zawartości](images/tags-2-define.png)

## <a name="using-custom-tags"></a>Za pomocą tagów niestandardowych ##

Etykiety niestandardowe są używane podczas przeglądu przez ludzi. Są wyświetlane w wersji zapoznawczej, a recenzenta zaznacza go, klikając ją.

![Za pomocą tagów moderowanie zawartości](images/tags-3-use.png)

Różne tagi dla różnych recenzji, można wyłączyć przez zaznaczenie lub usunięcie zaznaczenia jest widoczny.
 
![Wyłączanie tagów moderowanie zawartości](images/tags-4-disable.png)

Chociaż nie można usunąć tagów dwóch domyślnych **isadult** i **isracy**, możesz usunąć znaczniki niestandardowe, które zostały zdefiniowane. Kliknij przycisk Kosza obok znacznika, który chcesz usunąć.

![Usuwanie tagów moderowanie zawartości](images/tags-5-delete.png)

## <a name="next-steps"></a>Kolejne kroki ##

Aby dowiedzieć się, jak używać znaczników dla Moderowanie obrazów, zobacz [przeglądu moderowane obrazów](Review-Moderated-Images.md).
