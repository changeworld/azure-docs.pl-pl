---
title: Zabezpieczanie usługi Azure Data Lake Analytics dla wielu użytkowników
description: Dowiedz się, jak skonfigurować wielu użytkownikom na uruchamianie zadań w usłudze Azure Data Lake Analytics.
ms.service: data-lake-analytics
services: data-lake-analytics
author: matt1883
ms.author: mahi
ms.reviewer: jasonwhowell
ms.topic: conceptual
ms.date: 05/30/2018
ms.openlocfilehash: c6b86e25602f36896855d2593952609904396879
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43051588"
---
# <a name="configure-user-access-to-job-information-to-job-information-in-azure-data-lake-analytics"></a>Konfigurowanie dostępu użytkowników, aby informacje o zadaniu, aby informacje o zadaniu w usłudze Azure Data Lake Analytics 

W usłudze Azure Data Lake Analytics można użyć wielu kont użytkowników lub jednostki usługi do uruchamiania zadań. 

Aby dla tych tych samych użytkowników wyświetlić informacje szczegółowe zadania użytkownicy muszą mieć możliwość odczytu zawartości folderów zadania. Foldery zadania znajdują się w `/system/` katalogu. 

Jeśli nie skonfigurowano wymaganych uprawnień, użytkownik może zostać wyświetlony błąd: `Graph data not available - You don't have permissions to access the graph data.` 

## <a name="configure-user-access-to-job-information"></a>Konfigurowanie dostępu użytkowników, aby informacje o zadaniu

Możesz użyć **Kreatora dodawania użytkownika** do skonfigurowania listy ACL na foldery. Aby uzyskać więcej informacji, zobacz [dodać nowego użytkownika](data-lake-analytics-manage-use-portal.md#add-a-new-user).

Jeśli potrzebujesz większej ilości szczegółową kontrolę ani konieczności skryptu uprawnień, następnie zabezpieczyć foldery w następujący sposób:

1. Udziel **wykonania** uprawnień (za pośrednictwem dostępu do listy ACL) w folderze głównym:
   - /
   
2. Udziel **wykonania** i **odczytu** uprawnień folderów, które zawierają foldery zadania (za pośrednictwem listy ACL dostępu i domyślnej listy ACL). Na przykład dotyczących określonego zadania uruchomione na 25 maja 2018 r. tych folderów muszą być dostępne:
   - / System
   - / system/jobservice
   - /System/jobservice/Jobs
   - /System/jobservice/Jobs/Usql
   - /System/jobservice/Jobs/Usql/2018
   - /System/jobservice/Jobs/Usql/2018/05
   - /System/jobservice/Jobs/Usql/2018/05/25
   - /System/jobservice/Jobs/Usql/2018/05/25/11
   - /System/jobservice/Jobs/Usql/2018/05/25/11/01
   - System/jobservice/zadania/Usql/2018/05/25/11/01/b074bd7a-1448-d879-9d75-f562b101bd3d

## <a name="next-steps"></a>Kolejne kroki
[Dodawanie nowego użytkownika](data-lake-analytics-manage-use-portal.md#add-a-new-user)
