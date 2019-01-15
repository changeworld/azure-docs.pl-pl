---
title: Łączenie z innymi usługami podczas moderowanie zawartości — Content Moderator
titlesuffix: Azure Cognitive Services
description: Dowiedz się, jak dostęp do innych interfejsów API usługi Content Moderator przepływów pracy za pomocą łączników.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 99d8b3603278a9c6c432ca32a1d85e9abe34e1da
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2019
ms.locfileid: "54265587"
---
# <a name="connect-to-other-cognitive-services"></a>Łączenie z innymi usługami cognitive

Usługa Azure Content Moderator przepływów pracy można użyć innych interfejsów API, oprócz Content Moderator interfejsów API. Możesz uzyskać dostęp do innych interfejsów API przy użyciu łącznika w pakiecie Content Moderator. Łącznik zawiera łącze do innych interfejsów API.

Pakiet Content Moderator obejmuje te łączniki domyślne:

* Interfejs API rozpoznawania emocji
* Interfejs API rozpoznawania twarzy
* Usługa w chmurze PhotoDNA

![Content Moderator dostępne łączniki.](images/connectors-1.png)

## <a name="verify-your-credentials"></a>Weryfikowanie poświadczeń 

Przed zdefiniowaniem przepływu pracy, upewnij się, czy masz prawidłowe poświadczenia dla łącznika interfejsu API, którego chcesz używać:

1.  Narzędzie do przeglądu pulpitu nawigacyjnego, wybierz **ustawienia** > **łączników**.

  ![Content Moderator wybierz łączników.](images/connectors-2.png)

2.  Wybierz **Edytuj** symbol obok łącznik, który chcesz zweryfikować poświadczeń.

  ![Pakiet Content Moderator wybierz symbol edycji](images/connectors-3.png)

3.  Zostanie wyświetlony klucz subskrypcji. Jeśli wprowadzisz zmiany, wybierz **Zapisz** po zakończeniu.

  ![Strona zawartości łączników Edytuj moderatora](images/connectors-4-1.png)
 
## <a name="add-a-connector"></a>Dodaj łącznik

1.  Przed dodaniem łącznika, potrzebujesz klucza subskrypcji. Narzędzie do przeglądu pulpitu nawigacyjnego, wybierz **ustawienia** > **poświadczenia**. Zaznacz i skopiuj wartość, która znajduje się w **Ocp-Admin-Subscription-Key** pole.

2.  Wybierz **łączników**. Wybierz jedną z dostępnych łączników, które są wyświetlane na narzędzie do przeglądu pulpitu nawigacyjnego. Następnie wybierz **Connect**. 

  ![Strona zawartości Moderator Dodaj łącznik](images/connectors-5.png)

3.  W **Ocp-Admin-Subscription-Key** pole, Wklej klucz, który został skopiowany. Następnie wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak i używanie łączników do [Definiowanie niestandardowych przepływów pracy](workflows.md).
