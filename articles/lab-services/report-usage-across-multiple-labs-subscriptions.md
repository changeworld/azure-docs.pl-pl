---
title: Użycie usługi Azure DevTest Labs w wielu laboratoriach i subskrypcjach
description: Dowiedz się, jak raportować użycie usługi Azure DevTest Labs w wielu laboratoriach i subskrypcjach.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: takamath
ms.openlocfilehash: 912f510f6380c0ba1eb92b7c485091801123558e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169179"
---
# <a name="report-azure-devtest-labs-usage-across-multiple-labs-and-subscriptions"></a>Zgłaszanie użycia usługi Azure DevTest Labs w wielu laboratoriach i subskrypcjach

Większość dużych organizacji chce śledzić użycie zasobów, aby być bardziej skuteczne z tych zasobów poprzez wizualizację trendów i odstaczy w użyciu. Na podstawie użycia zasobów właściciele lub menedżerowie laboratorium mogą dostosować laboratoria, aby [zwiększyć zużycie zasobów i koszty.](https://docs.microsoft.com/azure/billing/billing-getting-started) W laboratoriach DevTest Azure można pobrać użycie zasobów na laboratorium, umożliwiając głębsze spojrzenie historyczne na wzorce użycia. Te wzorce użycia może pomóc w określić zmiany w celu zwiększenia wydajności. Większość przedsiębiorstw chce zarówno indywidualnego użycia w laboratorium, jak i ogólnego użycia w [wielu laboratoriach i subskrypcjach.](https://docs.microsoft.com/azure/architecture/cloud-adoption/decision-guides/subscriptions/) 

W tym artykule omówiono sposób obsługi informacji o użyciu zasobów w wielu laboratoriach i subskrypcjach.

![Raportowanie użycia](./media/report-usage-across-multiple-labs-subscriptions/report-usage.png)

## <a name="individual-lab-usage"></a>Indywidualne użycie w laboratorium

W tej sekcji omówiono sposób eksportowania użycia zasobów dla jednego laboratorium.

Aby można było wyeksportować użycie zasobów devtest labs, należy skonfigurować konto usługi Azure Storage, aby umożliwić przechowywanie różnych plików zawierających dane użycia. Istnieją dwa typowe sposoby wykonywania eksportu danych:

* [DevTest Labs REST API](https://docs.microsoft.com/rest/api/dtl/labs/exportresourceusage) 
* Moduł PowerShell Az.Resource [Invoke-AzResourceAction](https://docs.microsoft.com/powershell/module/az.resources/invoke-azresourceaction?view=azps-2.5.0&viewFallbackFrom=azps-2.3.2) z `exportResourceUsage`akcją , identyfikator zasobu laboratorium i niezbędne parametry. 

    Artykuł [eksportowania lub usuwania danych osobowych](personal-data-delete-export.md) zawiera przykładowy skrypt programu PowerShell ze szczegółowymi informacjami o eksportowanych danych. 

    > [!NOTE]
    > Parametr date nie zawiera sygnatury czasowej, więc dane zawierają wszystko od północy na podstawie strefy czasowej, w której znajduje się laboratorium.

Po zakończeniu eksportowania w magazynie obiektów blob znajduje się wiele plików CSV z różnymi informacjami o zasobach.
  
Obecnie istnieją dwa pliki CSV:

* *virtualmachines.csv* - zawiera informacje o maszynach wirtualnych w laboratorium
* *disks.csv* - zawiera informacje o różnych dyskach w laboratorium 

Pliki te są przechowywane w kontenerze obiektów blob *labresourceusage* pod nazwą laboratorium, Lab unikatowy identyfikator, data wykonania i pełne lub data rozpoczęcia, która została oparta na żądaniu eksportu. Przykładowa struktura obiektów blob może być:

* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD26-MM6-2019YYYY/full/virtualmachines.csv`
* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD-MM-YYYY/26-6-2019/20-6-2019<Start>DD-MM-YYYY/virtualmachines.csv`

## <a name="exporting-usage-for-all-labs"></a>Eksportowanie użycia dla wszystkich laboratoriów

Aby wyeksportować informacje o użytkowaniu dla wielu laboratoriów, należy rozważyć użycie 

* [usługi Azure Functions](https://docs.microsoft.com/azure/azure-functions/), dostępne w wielu językach, w tym w programie PowerShell lub 
* [Podręcznik azure automation](https://docs.microsoft.com/azure/automation/), użyj programu PowerShell, Python lub niestandardowego projektanta graficznego, aby napisać kod eksportu.

Korzystając z tych technologii, można wykonać eksport poszczególnych laboratoriów we wszystkich laboratoriach w określonym dniu i godzinie. 

Funkcja platformy Azure powinna wypychać dane do magazynu długoterminowego. Podczas eksportowania danych dla wielu laboratoriów eksport może zająć trochę czasu. Aby pomóc w wydajności i zmniejszyć możliwość powielania informacji, zalecamy wykonanie każdego laboratorium równolegle. Aby osiągnąć równoległość, uruchom usługi Azure Functions asynchronicznie. Skorzystaj również z wyzwalacza czasomierza oferowanego przez usługę Azure Functions.

## <a name="using-a-long-term-storage"></a>Korzystanie z długoterminowego przechowywania

Magazyn długoterminowy konsoliduje informacje eksportowe z różnych laboratoriów w jednym źródle danych. Inną zaletą korzystania z magazynu długoterminowego jest możliwość usunięcia plików z konta magazynu w celu zmniejszenia powielania i kosztów. 

Długoterminowe przechowywanie może służyć do manipulowania tekstem, na przykład: 

* dodawanie przyjaznych nazw
* tworzenie złożonych grup
* agregowania danych.

Niektóre typowe rozwiązania pamięci masowej to: [SQL Server](https://azure.microsoft.com/services/sql-database/), Azure [Data Lake](https://azure.microsoft.com/services/storage/data-lake-storage/)i [Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). Wybór długoterminowego rozwiązania pamięci masowej zależy od preferencji. Możesz rozważyć wybranie narzędzia w zależności od tego, co oferuje pod względem dostępności interakcji podczas wizualizacji danych.

## <a name="visualizing-data-and-gathering-insights"></a>Wizualizacja danych i zbieranie szczegółowych informacji

Użyj wybranego narzędzia do wizualizacji danych, aby połączyć się z magazynem długoterminowym, aby wyświetlić dane dotyczące użytkowania i zebrać szczegółowe informacje w celu zweryfikowania wydajności użycia. Na przykład [usługa Power BI](https://docs.microsoft.com/power-bi/power-bi-overview) może służyć do organizowania i wyświetlania danych użycia. 

Usługi [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) można używać do tworzenia zasobów, łączenia i zarządzania nimi w ramach jednego interfejsu lokalizacji. Jeśli potrzebna jest większa kontrola, pojedynczy zasób można utworzyć w ramach jednej grupy zasobów i zarządzać niezależnie od usługi Fabryka danych.  

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu systemu i przejściu danych do długoterminowego przechowywania następnym krokiem jest wyjęcie pytań, na które dane muszą odpowiedzieć. Przykład: 

-   Co to jest użycie rozmiaru maszyny Wirtualnej?

    Czy użytkownicy wybierają wysokowydajne (droższe) rozmiary maszyn wirtualnych?
-   Które obrazy z Marketplace są używane?

    Są niestandardowe obrazy najczęściej bazy maszyny Wirtualnej, powinny wspólne sklep z obrazami być zbudowany jak [Shared Image Gallery](../virtual-machines/windows/shared-image-galleries.md) lub fabryki [obrazu](image-factory-create.md).
-   Które niestandardowe obrazy są używane lub nie są używane?
