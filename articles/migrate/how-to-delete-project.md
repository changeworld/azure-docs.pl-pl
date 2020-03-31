---
title: Usuwanie projektu usługi Azure Migrate
description: W tym artykule opisano sposób tworzenia projektu migracji platformy Azure i dodawania narzędzia oceny/migracji.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 10/22/2019
ms.author: raynew
ms.openlocfilehash: 55842d36cddb2a7851ff5bd7002c20e9873158f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73512732"
---
# <a name="delete-an-azure-migrate-project"></a>Usuwanie projektu usługi Azure Migrate

W tym artykule opisano sposób usuwania projektu [migracji platformy Azure.](migrate-overview.md)


## <a name="before-you-start"></a>Przed rozpoczęciem

Przed usunięciem projektu:

- Po usunięciu projektu, projektu i odnalezione metadane komputera są usuwane.
- Jeśli do narzędzia Oceny serwera dołączono obszar roboczy usługi Log Analytics do analizy zależności, zdecyduj, czy chcesz usunąć obszar roboczy. 
    - Obszar roboczy nie jest automatycznie usuwany. Usuń go ręcznie.
    - Przed usunięciem sprawdź, do czego służy obszar roboczy. Ten sam obszar roboczy usługi Log Analytics może służyć do wielu scenariuszy.
    - Przed usunięciem projektu można znaleźć łącze do obszaru roboczego w **obszarze migracji platformy Azure — Usługi** > Migracji platformy**Azure — ocena serwera**w obszarze Obszar **roboczy systemu OMS**.
    - Aby usunąć obszar roboczy po usunięciu projektu, znajdź obszar roboczy w odpowiedniej grupie zasobów i postępuj zgodnie z [tymi instrukcjami](../azure-monitor/platform/delete-workspace.md).


## <a name="delete-a-project"></a>Usuwanie projektu


1. W witrynie Azure portal otwórz grupę zasobów, w której utworzono projekt.
2. Na stronie grupy zasobów wybierz pozycję **Pokaż ukryte typy**.
3. Wybierz projekt i skojarzone zasoby, które chcesz usunąć.
    - Typem zasobu dla projektów migracji platformy Azure jest **microsoft.Migrate/migrateprojects**.
    - W następnej sekcji przejrzyj zasoby utworzone do odnajdowania, oceny i migracji w projekcie migracji platformy Azure.
    - Jeśli grupa zasobów zawiera tylko projekt migracji platformy Azure, można usunąć całą grupę zasobów.
    - Jeśli chcesz usunąć projekt z poprzedniej wersji usługi Azure Migrate, kroki są takie same. Typem zasobu dla tych projektów jest **projekt migracji**.


## <a name="created-resources"></a>Utworzone zasoby

Te tabele podsumowują zasoby utworzone do odnajdowania, oceny i migracji w projekcie migracji platformy Azure.

> [!NOTE]
> Usuń magazyn kluczy z ostrożnością, ponieważ może zawierać klucze zabezpieczeń.

### <a name="vmwarephysical-server"></a>VMware/serwer fizyczny

**Zasobów** | **Typ**
--- | ---
"Nazwa urządzenia"kv | Magazyn kluczy
Strona "Nazwa urządzenia" | Witryny Microsoft.OffAzure/VMwareSites
"Nazwa projektu" | Microsoft.Migrate/migrateprojects Microsoft.Migrate/migrateprojects Microsoft.Migrate/migrateprojects Microsoft.
Projekt "ProjectName" | Microsoft.Migrate/assessmentProjects
"ProjectName"rsvault | Magazyn usługi Recovery Services
"ProjectName"-MigrateVault-* | Magazyn usługi Recovery Services
migrateappligwsa* | Konto magazynu
migrateapplilsa* | Konto magazynu
migrateapplicsa* | Konto magazynu
migrateapplikv* | Magazyn kluczy
migrateapplisbns16041 | Przestrzeń nazw usługi Service Bus

### <a name="hyper-v-vm"></a>Maszyna wirtualna funkcji Hyper-V 

**Zasobów** | **Typ**
--- | ---
"Nazwa projektu" | Microsoft.Migrate/migrateprojects Microsoft.Migrate/migrateprojects Microsoft.Migrate/migrateprojects Microsoft.
Projekt "ProjectName" | Microsoft.Migrate/assessmentProjects
HyperV*kv | Magazyn kluczy
HyperV*Witryna | Microsoft.OffAzure/HyperVSites
"ProjectName"-MigrateVault-* | Magazyn usługi Recovery Services


## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak dodać dodatkowe narzędzia [oceny](how-to-assess.md) i [migracji.](how-to-migrate.md) 
