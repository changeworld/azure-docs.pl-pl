---
title: Zabezpieczanie usługi Azure Data Lake Analytics dla wielu użytkowników
description: Informacje o sposobie konfigurowania wielu użytkownikom na uruchamianie zadań w usłudze Azure Data Lake Analytics.
ms.service: data-lake-analytics
services: data-lake-analytics
author: matt1883
ms.author: mahi
manager: kfile
editor: jasonwhowell
ms.topic: conceptual
ms.date: 05/30/2018
ms.openlocfilehash: 1d92e6d0e619584dedcbc9a66450c25dd1ac8b75
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34701423"
---
# <a name="configure-user-access-to-job-information-to-job-information-in-azure-data-lake-analytics"></a>Konfigurowanie dostępu użytkowników do informacji zadania, aby informacje o zadaniu w usłudze Azure Data Lake Analytics 

W usłudze Azure Data Lake Analytics można użyć wielu kont użytkowników lub nazwy główne usług do uruchomienia zadań. 

Aby tych samym użytkowników wyświetlić informacje szczegółowe zadania użytkownicy muszą mieć możliwość odczytu zawartości folderów zadania. Foldery zadania znajdują się w `/system/` katalogu. 

Jeśli nie skonfigurowano odpowiednie uprawnienia, użytkownik może zostać wyświetlony błąd: `Graph data not available - You don't have permissions to access the graph data.` 

## <a name="configure-user-access-to-job-information"></a>Konfigurowanie dostępu użytkownika do informacji o zadania

Można użyć **Kreatora dodawania użytkownika** skonfigurować listy kontroli dostępu w folderach. Aby uzyskać więcej informacji, zobacz [dodać nowego użytkownika](data-lake-analytics-manage-use-portal.md#add-a-new-user).

Jeśli potrzebujesz więcej kontrolę lub konieczność skryptu, następnie zabezpieczenia folderów w następujący sposób:

1. Udziel **wykonanie** uprawnień (za pośrednictwem dostępu ACL) w folderze głównym:
   - /
   
2. Udziel **wykonania** i **odczytu** uprawnienia (za pomocą dostępu ACL i domyślnej listy ACL) w folderach, które zawierają foldery zadania. Na przykład dla określonego zadania uruchomionego na 25 maja 2018 tych folderów muszą być dostępne:
   - odzyskiwanie
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
[Dodaj nowego użytkownika](data-lake-analytics-manage-use-portal.md#add-a-new-user)
