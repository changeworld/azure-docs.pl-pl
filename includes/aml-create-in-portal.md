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
ms.date: 05/21/2019
ms.openlocfilehash: a33eb98525ea857ee52ad2fffa5937207504909d
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67720547"
---
1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) przy użyciu poświadczeń subskrypcji platformy Azure, możesz użyć. 

   ![Azure Portal](./media/aml-create-in-portal/portal-dashboard-05-2019.png)

1. W lewym górnym rogu portalu, wybierz **Utwórz zasób**.

   ![Tworzenie zasobu w witrynie Azure Portal](./media/aml-create-in-portal/portal-create-a-resource-07-2019.png)

1. Na pasku wyszukiwania wprowadź **uczenia maszynowego**. Wybierz **obszaru roboczego usługi Machine Learning** wynik wyszukiwania.

   ![Wyszukiwania dla obszaru roboczego](./media/aml-create-in-portal/allservices-search.png)

1. W **obszar roboczy usługi uczenie Maszynowe** okienku wybierz **Utwórz** do rozpoczęcia.

    ![Przycisk Utwórz](./media/aml-create-in-portal/portal-create-button.png)

1. W **obszar roboczy usługi uczenie Maszynowe** okienku konfigurowanie obszaru roboczego.

   Pole|Opis
   ---|---
   Nazwa obszaru roboczego |Wprowadź unikatową nazwę, która identyfikuje obszaru roboczego. W tym przykładzie używamy **docs ws**. Nazwy muszą być unikatowe w obrębie grupy zasobów. Użyj nazwy, który jest łatwy do odwołania i odróżnienia jej od obszarów roboczych utworzonych przez innych użytkowników.  
   Subscription |Wybierz subskrypcję platformy Azure, której chcesz użyć.
   Resource group | Użyj istniejącej grupy zasobów w ramach subskrypcji, lub wprowadź nazwę, aby utworzyć nową grupę zasobów. Grupa zasobów zawiera powiązane zasoby rozwiązania platformy Azure. W tym przykładzie używamy **docs aml**. 
   Location | Wybierz lokalizację najbliżej Twoich użytkowników i zasobów danych. Ta lokalizacja jest tworzona dla obszaru roboczego.

1. Aby rozpocząć proces tworzenia, wybierz opcję **przeglądu + Utwórz**.

    ![Tworzenie obszaru roboczego](./media/aml-create-in-portal/workspace-create-main-tab.png)

1. Przejrzyj konfigurację obszaru roboczego. Jeśli jest on poprawny, wybierz opcję **Utwórz**. Może upłynąć kilka minut, aby utworzyć obszar roboczy.

1. Aby sprawdzić stan wdrożenia, wybierz ikonę powiadomienia **dzwonka**, na pasku narzędzi.

1. Po zakończeniu procesu pojawi się komunikat o powodzeniu wdrożenia. Jest również obecny w sekcji powiadomienia. Aby wyświetlić nowy obszar roboczy, wybierz **przejdź do zasobu**.

   ![Stan tworzenia obszaru roboczego](./media/aml-create-in-portal/notifications.png)
