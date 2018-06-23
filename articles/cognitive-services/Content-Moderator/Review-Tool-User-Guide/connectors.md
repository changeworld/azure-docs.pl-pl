---
title: Używać łączników w zawartości moderatora Azure dostępu do innych interfejsów API | Dokumentacja firmy Microsoft
description: Dowiedz się, jak uzyskać dostępu do innych interfejsów API dla przepływów pracy moderatora zawartości za pomocą łączników.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/22/2017
ms.author: sajagtap
ms.openlocfilehash: d8114457e7079ca8772cab830bd011dcddf372f5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347020"
---
# <a name="connectors"></a>Łączniki

Azure zawartości moderatora przepływów pracy można użyć innych interfejsów API, oprócz zawartości moderatora interfejsów API. Możesz uzyskać dostępu do innych interfejsów API przy użyciu łącznika w moderatora zawartości. Łącznik zawiera łącze do innych interfejsów API.

Moderatora zawartości zawiera te łączniki domyślne:

* Interfejs API rozpoznawania emocji
* Interfejs API rozpoznawania twarzy
* Usługi w chmurze PhotoDNA

![Zawartości moderatora dostępnych łączników.](images/connectors-1.png)

## <a name="verify-your-credentials"></a>Zweryfikować Twoich poświadczeń 

Przed zdefiniowaniem przepływu pracy upewnij się, że masz prawidłowe poświadczenia dla łącznika interfejsu API, którego chcesz używać:

1.  Narzędzie przeglądu pulpitu nawigacyjnego, wybierz **ustawienia** > **łączniki**.

  ![Zawartości moderatora wybierz łączników.](images/connectors-2.png)

2.  Wybierz **Edytuj** symbol obok łącznik, który chcesz zweryfikować poświadczeń.

  ![Moderatora zawartości wybierz symbol edycji](images/connectors-3.png)

3.  Zostanie wyświetlony klucz subskrypcji. Jeśli wprowadzisz zmiany, wybierz **zapisać** po zakończeniu.

  ![Strona zawartości moderatora Edytuj łączników](images/connectors-4-1.png)
 
## <a name="add-a-connector"></a>Dodawanie łącznika

1.  Przed dodaniem łącznik należy klucza subskrypcji. Narzędzie przeglądu pulpitu nawigacyjnego, wybierz **ustawienia** > **poświadczenia**. Zaznacz i skopiuj wartość, która znajduje się w **Ocp-subskrypcji — klucz administratora** pole.

2.  Wybierz **łączniki**. Wybierz jedną z dostępnych łączników, które są wyświetlane na narzędzie pulpitu nawigacyjnego przeglądu. Następnie wybierz opcję **Connect**. 

  ![Strona zawartości moderatora Dodawanie łącznika](images/connectors-5.png)

3.  W **Ocp-subskrypcji — klucz administratora** Wklej klucz, który został skopiowany. Następnie wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Kolejne kroki

* Używanie łączników do [Zdefiniuj niestandardowe przepływy pracy](workflows.md).
