---
title: Zarządzanie aktualizacjami w stosie Azure — omówienie | Dokumentacja firmy Microsoft
description: Więcej informacji na temat zarządzania aktualizacjami stosu Azure zintegrowanych systemów.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 9b0781f4-2cd5-4619-a9b1-59182b4a6e43
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: mabrigg
ms.openlocfilehash: 23b05909bda7785b45aeaeed0bd75a90de9ffe50
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/06/2018
ms.locfileid: "27620927"
---
# <a name="manage-updates-in-azure-stack-overview"></a>Zarządzanie aktualizacjami w stosie Azure — omówienie

*Dotyczy: Azure stosu zintegrowane systemy*

Firma Microsoft będzie udostępniać pakietów aktualizacji dla systemów stosu Azure zintegrowane w regularnych okresach, który będzie zazwyczaj dzielą czwarty wtorek każdego miesiąca, zaczynając od ogólnej dostępności. Poproś o ich procesu określonych powiadomień, aby zapewnić reach powiadomienia o aktualizacji organizacji OEM lub sprawdź, w tym miejscu w systemach Notes\Integrated Overview\Release informacje o wersji, aby uzyskać więcej informacji na temat określonych wersji.

Każde wydanie aktualizacji oprogramowania firmy Microsoft jest powiązane jako jedna aktualizacja pakietu. Jako operator stosu Azure można łatwo importować, instalacji i Monitoruj postęp instalacji tych aktualizacji pakietów z portalu administratora. 

Dostawca sprzętu producenta sprzętu (OEM) zostanie również zwolnić aktualizacji, na przykład aktualizacje sterowników i oprogramowania układowego. Te aktualizacje są dostarczane jako osobne pakiety przez producenta OEM komputera i są zarządzane oddzielnie z usługi Microsoft updates.

Aby zachować system w obszarze pomocy technicznej, należy dysponować stosu Azure aktualizowane na poziomie określonej wersji. Upewnij się, że należy sprawdzić [stosu Azure obsługi zasad](azure-stack-servicing-policy.md).

> [!NOTE]
> Nie można zastosować pakietów aktualizacji stosu Azure Azure stosu Development Kit. Pakiety aktualizacji są przeznaczone dla zintegrowanych systemów.

## <a name="the-update-resource-provider"></a>Dostawca zasobów aktualizacji

Stos Azure obejmuje dostawcy zasobów aktualizacji, który organizuje stosowania aktualizacji oprogramowania firmy Microsoft. Ten dostawca zasobów gwarantuje, że aktualizacje zostały zastosowane wszystkie hosty fizyczne, aplikacji usługi Service Fabric i środowisk uruchomieniowych i wszystkich maszyn wirtualnych infrastruktury i ich skojarzonych usług.

Jak zainstalować aktualizacje, można łatwo wyświetlić stan wysokiego poziomu jako miejsca docelowe proces aktualizacji różne podsystemy w stosie Azure (na przykład hostów fizycznych i maszyn wirtualnych infrastruktury).

## <a name="plan-for-updates"></a>Planowanie aktualizacji

Zalecamy powiadomienie użytkowników wszystkie operacje obsługi, i Zaplanuj konserwacji systemu windows podczas poza godzinami pracy możliwie. Operacje konserwacji może mieć wpływ na zarówno obciążeń dzierżawców i działania portalu.

## <a name="using-the-update-tile-to-manage-updates"></a>Zarządzanie aktualizacjami za pomocą kafelka aktualizacji
Zarządzanie aktualizacjami w portalu administratora jest prosty proces. Operator stosu Azure można przejść do aktualizacji kafelka na pulpicie nawigacyjnym, aby:

- Wyświetl ważne informacje, takie jak bieżącej wersji.
- Zainstaluj aktualizacje, a następnie monitorować postęp.
- Przejrzyj historię aktualizacji dla poprzednio zainstalowanych aktualizacji.
 
## <a name="determine-the-current-version"></a>Określić bieżącej wersji

Na kafelku aktualizacji pokazuje bieżącą wersję programu Azure stosu. Na kafelku aktualizacji można uzyskać przy użyciu jednej z następujących metod w portalu administratora:

- Na pulpicie nawigacyjnym, Wyświetl bieżącą wersję w **aktualizacji** kafelka.
 
   ![Kafelek aktualizacje na domyślnego pulpitu nawigacyjnego](./media/azure-stack-updates/image1.png)
 
- Na **zarządzania Region** kafelka, kliknij nazwę regionu. Wyświetl bieżącą wersję w **aktualizacji** kafelka.

## <a name="next-steps"></a>Kolejne kroki

- [Azure stos obsługi zasad](azure-stack-servicing-policy.md) 
- [Zarządzanie regionu Azure stosu](azure-stack-region-management.md)     


