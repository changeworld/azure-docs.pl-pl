---
title: Bezpieczna usługa Azure Data Lake Analytics dla wielu użytkowników
description: Dowiedz się, jak skonfigurować wielu użytkowników do uruchamiania zadań w usłudze Azure Data Lake Analytics.
ms.service: data-lake-analytics
services: data-lake-analytics
author: matt1883
ms.author: mahi
ms.reviewer: jasonwhowell
ms.topic: conceptual
ms.date: 05/30/2018
ms.openlocfilehash: 9fbc94259d6fdfb6758204efd6e6f0a346dc58da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60813375"
---
# <a name="configure-user-access-to-job-information-to-job-information-in-azure-data-lake-analytics"></a>Konfigurowanie dostępu użytkownika do informacji o zadaniach do informacji o zadaniach w usłudze Azure Data Lake Analytics 

W usłudze Azure Data Lake Analytics można używać wielu kont użytkowników lub podmiotów zabezpieczeń usługi do uruchamiania zadań. 

Aby ci sami użytkownicy mogli zobaczyć szczegółowe informacje o pracy, użytkownicy muszą mieć możliwość odczytywania zawartości folderów zadań. Foldery zadań znajdują `/system/` się w katalogu. 

Jeśli niezbędne uprawnienia nie są skonfigurowane, użytkownik może zobaczyć błąd:`Graph data not available - You don't have permissions to access the graph data.` 

## <a name="configure-user-access-to-job-information"></a>Konfigurowanie dostępu użytkownika do informacji o zadaniach

Za pomocą **Kreatora dodawania użytkownika** można skonfigurować listy ACL w folderach. Aby uzyskać więcej informacji, zobacz [Dodawanie nowego użytkownika](data-lake-analytics-manage-use-portal.md#add-a-new-user).

Jeśli potrzebujesz bardziej szczegółowej kontroli lub chcesz skrypt uprawnienia, a następnie zabezpieczyć foldery w następujący sposób:

1. Udziel uprawnień **do wykonywania** (za pośrednictwem listy ACL dostępu) w folderze głównym:
   - /
   
2. Udziel uprawnień **do wykonywania** i **odczytu** (za pośrednictwem listy ACL dostępu i domyślnej listy ACL) w folderach zawierających foldery zadań. Na przykład dla określonego zadania, które zostało uruchomiony w dniu 25 maja 2018 r., te foldery muszą być dostępne:
   - /system
   - /system/jobservice
   - /system/jobservice/zadania
   - /system/jobservice/jobs/Usql
   - /system/jobservice/jobs/Usql/2018
   - /system/jobservice/jobs/Usql/2018/05
   - /system/jobservice/jobs/Usql/2018/05/25
   - /system/jobservice/jobs/Usql/2018/05/25/11
   - /system/jobservice/jobs/Usql/2018/05/25/11/01
   - /system/jobservice/jobs/Usql/2018/05/25/11/01/b074bd7a-1448-d879-9d75-f562b101bd3d

## <a name="next-steps"></a>Następne kroki
[Dodawanie nowego użytkownika](data-lake-analytics-manage-use-portal.md#add-a-new-user)
