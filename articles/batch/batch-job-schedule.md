---
title: Planowanie zadań
description: Zarządzanie zadaniami służy do planowania zadań.
services: batch
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 63d9d4f1-8521-4bbb-b95a-c4cad73692d3
ms.service: batch
ms.topic: article
ms.workload: big-compute
ms.date: 02/20/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 55ea8fb4cc0e65deaa89d718c4a46513716dcf54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672435"
---
# <a name="schedule-jobs-for-efficiency"></a>Planowanie zadań pod kątem wydajności

Planowanie zadań wsadowych umożliwia nadanie priorytetu zadańom, które mają być uruchamiane jako pierwsze, biorąc pod uwagę zadania, które mają zależności od innych zadań. Planując zadania, można upewnić się, że używasz najmniejszej ilości zasobów. Węzły mogą być likwidowane, gdy nie są potrzebne, zadania, które są zależne od innych zadań są obracane w sam raz optymalizacji przepływów pracy. Uruchamia się tylko jedno zadanie naraz. Nowy nie rozpocznie się, dopóki poprzedni nie zostanie ukończony. Zadanie można ustawić na autouzupełnienie. 

## <a name="benefit-of-job-scheduling"></a>Korzyści z planowania zadań

Zaletą planowania zadań jest to, że można określić harmonogram tworzenia zadań. Zadania zaplanowane przy użyciu zadania menedżera zadań są skojarzone z zadaniem. Zadanie menedżera zadań utworzy zadania dla zadania. Aby to zrobić, zadanie menedżera zadań musi uwierzytelnić się przy użyciu konta usługi Batch. Użyj tokenu dostępu AZ_BATCH_AUTHENTICATION_TOKEN. Token umożliwi dostęp do pozostałej części zadania. 

## <a name="use-the-portal-to-schedule-a-job"></a>Planowanie zadania za pomocą portalu

   1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/).

   2. Wybierz konto usługi Batch, na które chcesz zaplanować zadania.

   3. Wybierz **pozycję Dodaj,** aby utworzyć nowy harmonogram zadań i wypełnić **formularz Podstawowy**.



![Planowanie zadania][1]

**Identyfikator harmonogramu**zadań: unikatowy identyfikator dla tego harmonogramu zadań.

**Nazwa wyświetlana:** Nazwa wyświetlana zadania nie musi być unikatowa, ale ma maksymalną długość 1024 znaków.

**Nie należy uruchamiać, dopóki:** Określa najwcześniejszą godzinę uruchomienia zadania. Jeśli nie ustawisz tego, harmonogram stanie się gotowy do natychmiastowego uruchomienia zadań.

**Nie uruchamiaj po:** Żadne zadania nie są uruchamiane po godzinie ustawionej w tym miejscu. Jeśli nie określisz czasu, tworzysz harmonogram zadań cyklicznych, który pozostaje aktywny, dopóki nie zostanie jawnie zakończony.

**Interwał cyklu:** Można określić czas między zadaniami. W zaplanowanym czasie może być tylko jedno zadanie, więc jeśli nadszedł czas, aby utworzyć nowe zadanie zgodnie z harmonogramem zadań, ale poprzednie zadanie jest nadal uruchomione, usługa wsadowa nie utworzy nowego zadania do czasu zakończenia poprzedniego zadania.  

**Okno Start:** W tym miejscu należy określić przedział czasu, począwszy od czasu, w której harmonogram wskazuje, że zadanie powinno zostać utworzone, aż do jego ukończenia. Jeśli bieżące zadanie nie zostanie ukończone w oknie, następne zadanie nie zostanie uruchomiony.

U dołu formularza podstawowego określą pulę, na której ma być uruchamiane zadanie. Aby znaleźć informacje o identyfikatorze puli, wybierz pozycję **Aktualizuj**. 

![Określ pulę][2]


**Identyfikator puli:** Zidentyfikuj pulę, na której uruchomisz zadanie.

**Zadanie konfiguracji zadania**: Wybierz **opcję Aktualizuj,** aby nadać nazwę zadaniu Menedżera zadań, a także zadania związane z przygotowaniem i zwolnieniem zadań, jeśli są używane.

**Priorytet:** Nadaj zadaniu priorytet.

**Maksymalny czas zegara ściennego:** Ustaw maksymalny czas, przez jaki zadanie może być uruchamiane. Jeśli nie zostanie ukończona w ramach czasowych, partia kończy zadanie. Jeśli nie ustawisz tego, nie ma limitu czasu dla zadania.

**Maksymalna liczba ponownych prób:** Określ, ile razy zadanie może zostać ponowione maksymalnie czterokrotnie. To nie jest taka sama, jak liczba ponownych prób wywołanie interfejsu API może mieć.

**Po wykonaniu wszystkich zadań**: Domyślnie nie ma akcji.

**Gdy zadanie nie powiedzie się**: Wartość domyślna nie jest żadna akcja. Zadanie kończy się niepowodzeniem, jeśli liczba ponownych prób jest wyczerpana lub wystąpił błąd podczas uruchamiania zadania. 

Po **wybraniu opcji Zapisz**, jeśli przejdziesz do **Harmonogramy zadań** w lewej nawigacji, możesz śledzić wykonanie zadania, wybierając informacje o **wykonaniu**.


## <a name="for-more-information"></a>Więcej informacji

Aby zarządzać zadaniem przy użyciu interfejsu wiersza polecenia platformy Azure, zobacz [harmonogram zadań wsadowych az](https://docs.microsoft.com/cli/azure/batch/job-schedule?view=azure-cli-latest).

## <a name="next-steps"></a>Następne kroki

[Tworzenie zależności zadań w celu uruchamiania zadań zależnych od innych zadań](batch-task-dependencies.md).





[1]: ./media/batch-job-schedule/add_job_schedule-02.png
[2]: ./media/batch-job-schedule/add_job_schedule-03.png


