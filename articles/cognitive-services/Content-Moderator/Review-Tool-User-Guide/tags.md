---
title: Przy użyciu tagów w zawartości moderatora Azure | Dokumentacja firmy Microsoft
description: Moderatora zawartości zawiera znaczników domyślnych i mogą tworzyć niestandardowe tagi dla moderowanie zawartości specyficzne dla firmy.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/25/2017
ms.author: sajagtap
ms.openlocfilehash: add4c685c07c63944ae89f48a47ac78df28c1623
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346965"
---
# <a name="about-tags"></a>Tagi — informacje #

Oprócz dwóch domyślnych tagi (— isadult i r — isracy) można utworzyć znaczniki niestandardowe do bardziej docelowe skanowania. Znaczniki niestandardowe będą dostępne dla człowieka recenzentów do przypisania do obrazów lub tekstu.

## <a name="create-tags"></a>Tworzenie znaczników ##

1.  Wybierz tagi na karcie Ustawienia.

  ![Tagi łagodzenia zawartości](images/tags-1.png)

2.  Wprowadź kod krótkich dwuliterowych dla tagu.
3.  Wprowadź nazwę dla tagu. Zachowaj nazwę krótki i opisowy. Na przykład "isNudity".
4.  Wprowadź opis.
5.  Kliknij pozycję Add (Dodaj).
6.  Po zakończeniu tworzenia znaczniki, kliknij przycisk Zapisz.

![Definiowanie tagów łagodzenia zawartości](images/tags-2-define.png)

## <a name="using-custom-tags"></a>Przy użyciu niestandardowych tagów ##

Znaczniki niestandardowe są używane podczas przeglądu człowieka. Są one wyświetlane na podglądzie, a osoba dokonująca przeglądu zaznacza go, klikając go.

![Przy użyciu tagów łagodzenia zawartości](images/tags-3-use.png)

Tagi różne dla różnych recenzji, można wyłączyć przez zaznaczenie lub usunięcie zaznaczenia jest widoczny.
 
![Wyłączanie tagów łagodzenia zawartości](images/tags-4-disable.png)

Gdy nie można usunąć dwa znaczniki domyślne, isadult i isracy, należy usunąć wszystkie znaczniki niestandardowe, które zostały określone. Kliknij przycisk może odzyskiwanie obok tagu, który chcesz usunąć.

![Usuwanie tagów łagodzenia zawartości](images/tags-5-delete.png)

## <a name="next-steps"></a>Kolejne kroki ##

Informacje na temat użycia znaczników podczas łagodzenia obrazu, zobacz [sposób przeglądania moderowane obrazów](Review-Moderated-Images.md).
