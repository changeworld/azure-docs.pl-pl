---
title: Zarządzanie aktualizacjami w usłudze Azure Stack — omówienie | Dokumentacja firmy Microsoft
description: Więcej informacji na temat rozwiązania update management dla usługi Azure Stack, zintegrowanych systemów.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2019
ms.author: mabrigg
ms.lastreviewed: 01/22/2019
ms.openlocfilehash: 1ee8b11b131a40150431daa22011e868ab290e3a
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2019
ms.locfileid: "55250577"
---
# <a name="manage-updates-in-azure-stack-overview"></a>Zarządzanie aktualizacjami w usłudze Azure Stack — omówienie

*Dotyczy: Zintegrowane systemy usługi Azure Stack*

Pakiety aktualizacji firmy Microsoft dla zazwyczaj zintegrowane systemy usługi Azure Stack wersji wokół czwarty wtorek każdego miesiąca. Zadaj producent OEM dotyczące procesu ich powiadomień określonych w taki sposób, aby zapewnić powiadomienia o aktualizacji dotrzeć do Twojej organizacji. Możesz również sprawdzić w tej bibliotece dokumentacji w obszarze **Przegląd** > **informacje o wersji** informacji o wersjach, które są aktywne pomocy technicznej. 

Wszystkie wersje aktualizacji oprogramowania firmy Microsoft jest zawarte w pakiecie jako pakiet jedną aktualizację. Jako operatorów usługi Azure Stack można zaimportować, instalacji i Monitoruj postęp instalacji tych aktualizacji dla pakietów z portalu administratora. 

Z dostawcą sprzętu producenta sprzętu (OEM) będzie również aktualizacje, takich jak aktualizacje oprogramowania układowego i sterowników. Chociaż te aktualizacje są dostarczane jako oddzielne pakiety przez dostawcę sprzętu OEM, ich są importowane, zainstalowane i zarządzane te same pakiety aktualizacji sposób od firmy Microsoft, pakiety aktualizacji są importowane, zainstalowane i zarządzane.

Aby zapewnić systemu w ramach pomocy technicznej, należy dysponować zaktualizowany do poziomu określonej wersji w usłudze Azure Stack. Upewnij się, że przeglądu [obsługi zasad w usłudze Azure Stack](azure-stack-servicing-policy.md).

> [!NOTE]
> Pakiety aktualizacji usługi Azure Stack nie można zastosować do usługi Azure Stack Development Kit. Pakiety aktualizacji są przeznaczone dla systemów zintegrowanych. Aby uzyskać informacje, zobacz [ponownie wdrożyć ASDK](https://docs.microsoft.com/azure/azure-stack/asdk).

## <a name="the-update-resource-provider"></a>Dostawca zasobów aktualizacji

Usługa Azure Stack obejmuje dostawcy zasobów aktualizacji, która zarządza procesem stosowania aktualizacji oprogramowania firmy Microsoft. Tego dostawcę zasobów gwarantuje, że aktualizacje są stosowane na wszystkie hosty fizyczne, aplikacje usługi Service Fabric i środowisk uruchomieniowych oraz wszystkich maszyn wirtualnych infrastruktury i ich skojarzone usługi.

Jak zainstalować aktualizacje, można wyświetlić stan wysokiego poziomu jako elementy docelowe proces aktualizacji różnych podsystemów w usłudze Azure Stack (na przykład hostów fizycznych i maszyn wirtualnych infrastruktury).

## <a name="plan-for-updates"></a>Planowanie aktualizacji

Zdecydowanie zalecamy powiadomienie użytkowników dowolne operacje konserwacji oraz zaplanowanie konserwacji systemu windows podczas poza godzinami, jeśli jest to możliwe. Operacje konserwacji może mieć wpływ na obciążeniami dzierżawy i operacje w portalu.


- Przed rozpoczęciem instalacji tej aktualizacji należy uruchomić [AzureStack testu](azure-stack-diagnostic-test.md) z następującymi parametrami, aby zweryfikować stan usługi Azure Stack i rozwiązać wszelkie problemy z działaniem, znaleziono, w tym wszystkie ostrzeżenia i błędy. Również przejrzeć aktywne alerty i rozwiązywanie tych, które wymagają akcji.  

  ```PowerShell
  Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
  ``` 

## <a name="using-the-update-tile-to-manage-updates"></a>Za pomocą kafelka aktualizacji do zarządzania aktualizacjami
Zarządzanie aktualizacjami z portalu administratora. Jako operatorów usługi Azure Stack umożliwia Aktualizuj Kafelek na pulpicie nawigacyjnym, aby:

- Wyświetl ważne informacje, takie jak bieżąca wersja.
- Instalowanie aktualizacji, a następnie monitorować postęp.
- Przejrzyj historię aktualizacji dla wcześniej zainstalowanych aktualizacji.
 
## <a name="determine-the-current-version"></a>Określić bieżącej wersji

Aktualizuj Kafelek pokazuje bieżącą wersję usługi Azure Stack. Aktualizuj Kafelek można uzyskać, przy użyciu jednej z poniższych metod w portalu administratora:

- Na pulpicie nawigacyjnym wyświetlić bieżącą wersję **aktualizacji** kafelka.
 
   ![Aktualizacje kafelków na domyślny pulpit nawigacyjny](./media/azure-stack-updates/image1.png)
 
- Na **zarządzanie regionami** kafelków, kliknij nazwę regionu. Wyświetl bieżącą wersję **aktualizacji** kafelka.

## <a name="next-steps"></a>Kolejne kroki

- [Obsługa zasad z usługi Azure Stack](azure-stack-servicing-policy.md) 
- [Zarządzanie regionami w usłudze Azure Stack](azure-stack-region-management.md)     


