---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 07/31/2019
ms.openlocfilehash: 0a497ce506e1aa3b0f9afc47bf1ab8382c9c0405
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/12/2019
ms.locfileid: "68951441"
---
1. Zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu poświadczeń dla używanej subskrypcji platformy Azure. 

1. W lewym górnym rogu Azure Portal wybierz pozycję **Utwórz zasób**.

1. Użyj paska wyszukiwania, aby znaleźć **obszar roboczy usługi Machine Learning**.

1. Wybierz **obszar roboczy usługi Machine Learning**.

1. W okienku **obszaru roboczego usługi ml** wybierz pozycję **Utwórz** , aby rozpocząć.

1. Skonfiguruj nowy obszar roboczy, podając nazwę obszaru roboczego, subskrypcję, grupę zasobów i lokalizację.

    ![Tworzenie obszaru roboczego](./media/aml-create-in-portal/workspace-create-main-tab.png)

   Pole|Opis 
   ---|---
   Nazwa obszaru roboczego |Wprowadź unikatową nazwę identyfikującą obszar roboczy. W tym przykładzie używamy **dokumentów-WS**. Nazwy muszą być unikatowe w ramach grupy zasobów. Użyj nazwy, która jest łatwa do odzyskania i odróżniania od obszarów roboczych utworzonych przez inne osoby.  
   Subscription |Wybierz subskrypcję platformy Azure, której chcesz użyć.
   Resource group | Użyj istniejącej grupy zasobów w subskrypcji lub wprowadź nazwę, aby utworzyć nową grupę zasobów. Grupa zasobów zawiera powiązane zasoby dla rozwiązania platformy Azure. W tym przykładzie używane są **dokumenty-AML**. 
   Location | Wybierz lokalizację znajdującą się najbliżej użytkowników i zasoby danych, aby utworzyć obszar roboczy.

1. Po zakończeniu konfigurowania obszaru roboczego wybierz pozycję **Utwórz**. 

   Utworzenie obszaru roboczego może potrwać kilka chwil.

   Po zakończeniu procesu zostanie wyświetlony komunikat o powodzeniu wdrożenia. Znajduje się również w sekcji powiadomienia. Aby wyświetlić nowy obszar roboczy, wybierz pozycję **Przejdź do zasobu**.

   ![Stan tworzenia obszaru roboczego](./media/aml-create-in-portal/notifications.png)

1. Jeśli kod odwołuje się do tego obszaru roboczego ze środowiska lokalnego, wybierz pozycję **Pobierz plik config. JSON** z sekcji **Przegląd** w obszarze roboczym.  

   ![Pobierz plik config.json](./media/aml-create-in-portal/configure.png)

    Umieść ten plik JSON w strukturze katalogów zawierającej skrypty języka Python lub notesy Jupyter. Może znajdować się w tym samym katalogu, podkatalogu o nazwie *. Azure*lub w katalogu nadrzędnym.

   Jeśli będziesz korzystać z [maszyny wirtualnej notesu](../articles/machine-learning/service/tutorial-1st-experiment-sdk-setup.md#azure) , nie musisz pobierać tego pliku, zostanie ona dodana do poprawnego katalogu na maszynie wirtualnej.
