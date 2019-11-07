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
ms.date: 10/04/2019
ms.openlocfilehash: c1f4b3d60c0b8af96d851ce54c2aac57c76335a7
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73633718"
---
1. Zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu poświadczeń dla subskrypcji platformy Azure. 

1. W lewym górnym rogu Azure Portal wybierz pozycję **+ Utwórz zasób**.

      ![Tworzenie nowego zasobu](media/aml-create-in-portal/create-workspace.gif)

1. Użyj paska wyszukiwania, aby znaleźć **Machine Learning**.

1. Wybierz **Machine Learning**.

1. W okienku **Machine Learning** wybierz pozycję **Utwórz** , aby rozpocząć.

1. Podaj następujące informacje, aby skonfigurować nowy obszar roboczy:

   Pole|Opis 
   ---|---
   Nazwa obszaru roboczego |Wprowadź unikatową nazwę identyfikującą obszar roboczy. W tym przykładzie używamy **dokumentów-WS**. Nazwy muszą być unikatowe w ramach grupy zasobów. Użyj nazwy, która jest łatwa do odzyskania i odróżniania od obszarów roboczych utworzonych przez inne osoby.  
   Subskrypcja |Wybierz subskrypcję platformy Azure, której chcesz użyć.
   Grupa zasobów | Użyj istniejącej grupy zasobów w subskrypcji lub wprowadź nazwę, aby utworzyć nową grupę zasobów. Grupa zasobów zawiera powiązane zasoby dla rozwiązania platformy Azure. W tym przykładzie używane są **dokumenty-AML**. 
   Lokalizacja | Wybierz lokalizację znajdującą się najbliżej użytkowników i zasoby danych, aby utworzyć obszar roboczy.
   Wersja obszaru roboczego | Wybierz pozycję **Enterprise**.  Ten samouczek wymaga użycia wersji Enterprise Edition.  Wersja Enterprise Edition jest dostępna w wersji zapoznawczej i obecnie nie dodaje żadnych dodatkowych kosztów.  

1. Po zakończeniu konfigurowania obszaru roboczego wybierz pozycję **Utwórz**. 

   > [!Warning] 
   > Tworzenie obszaru roboczego w chmurze może potrwać kilka minut.

   Po zakończeniu procesu zostanie wyświetlony komunikat o powodzeniu wdrożenia. 
 
 1. Aby wyświetlić nowy obszar roboczy, wybierz pozycję **Przejdź do zasobu**.

