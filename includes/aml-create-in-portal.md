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
ms.date: 09/24/2018
ms.openlocfilehash: 57fd69542a5d92b9afd1e003d8b94c1ebb64953e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65031790"
---
1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) przy użyciu poświadczeń subskrypcji platformy Azure, możesz użyć. 

   ![Azure Portal](./media/aml-create-in-portal/portal-dashboard.png)

1. W lewym górnym rogu portalu, wybierz **Utwórz zasób**.

   ![Tworzenie zasobu w witrynie Azure Portal](./media/aml-create-in-portal/portal-create-a-resource.png)

1. Na pasku wyszukiwania wprowadź **uczenia maszynowego**. Wybierz **obszaru roboczego usługi Machine Learning** wynik wyszukiwania.

   ![Wyszukiwania dla obszaru roboczego](./media/aml-create-in-portal/allservices-search.png)

1. W **obszar roboczy usługi uczenie Maszynowe** okienko, przewiń do dołu i wybierz pozycję **Utwórz** do rozpoczęcia.

   ![Przycisk Utwórz](./media/aml-create-in-portal/portal-create-button.png)

1. W **obszar roboczy usługi uczenie Maszynowe** okienku konfigurowanie obszaru roboczego.

   Pole|Opis
   ---|---
   Nazwa obszaru roboczego |Wprowadź unikatową nazwę, która identyfikuje obszaru roboczego. W tym przykładzie używamy **docs ws**. Nazwy muszą być unikatowe w obrębie grupy zasobów. Użyj nazwy, który jest łatwy do odwołania i odróżnienia jej od obszarów roboczych utworzonych przez innych użytkowników.  
   Subskrypcja |Wybierz subskrypcję platformy Azure, której chcesz użyć.
   Grupa zasobów | Użyj istniejącej grupy zasobów w ramach subskrypcji, lub wprowadź nazwę, aby utworzyć nową grupę zasobów. Grupa zasobów to kontener, który zawiera powiązane zasoby dla rozwiązania platformy Azure. W tym przykładzie używamy **docs aml**. 
   Lokalizacja | Wybierz lokalizację najbliżej Twoich użytkowników i zasobów danych. Ta lokalizacja jest tworzona dla obszaru roboczego.

   ![Tworzenie obszaru roboczego](./media/aml-create-in-portal/workspace-create.png)

1. Aby rozpocząć proces tworzenia, wybierz opcję **Utwórz**. Może upłynąć kilka minut, aby utworzyć obszar roboczy.

1. Aby sprawdzić stan wdrożenia, wybierz ikonę powiadomienia **dzwonka**, na pasku narzędzi.

1. Po zakończeniu procesu pojawi się komunikat o powodzeniu wdrożenia. Jest również obecny w sekcji powiadomienia. Aby wyświetlić nowy obszar roboczy, wybierz **przejdź do zasobu**.

   ![Stan tworzenia obszaru roboczego](./media/aml-create-in-portal/notifications.png)
