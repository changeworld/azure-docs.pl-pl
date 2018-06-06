---
title: Azure zalecenia usługi Advisor koszt | Dokumentacja firmy Microsoft
description: Optymalizowanie koszt wdrożeń platformy Azure przy użyciu klasyfikatora Azure.
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: ade6ef996c00c0c06d5b8e44815520e6e4ab7e9f
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34735871"
---
# <a name="advisor-cost-recommendations"></a>Zalecenia doradcy w zakresie koszt

Advisor pomaga zoptymalizować i zmniejszyć ogólną Azure wydatków, określając bezczynności i wyczerpaniu zasobów. Zalecenia można uzyskać koszt **koszt** na pulpicie nawigacyjnym usługi Advisor.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Optymalizacja maszyny wirtualnej spędzają na zmianę rozmiaru lub zamykanie niedostatecznie wystąpień 
Mimo że niektóre scenariusze aplikacji może spowodować niskiego poziomu wykorzystania zgodnie z projektem, można często zaoszczędzić, zarządzając rozmiaru i liczby maszyn wirtualnych. Klasyfikator monitoruje użycie maszyny wirtualnej w ciągu ostatnich 14 dni, a następnie identyfikuje niskiego wykorzystania maszyn wirtualnych. Maszyny wirtualne, których użycie procesora CPU wynosi 5 procent lub mniej i użycie sieci jest 7 MB lub mniej przez cztery lub więcej dni są traktowane jako niskiego wykorzystania maszyn wirtualnych.

Klasyfikator pokazuje szacowany koszt kontynuowania działania maszyny wirtualnej, dzięki czemu można zamknąć lub zmiany rozmiaru.

Jeśli chcesz mieć bardziej agresywną na określenie niedostatecznie maszyn wirtualnych, można dostosować średni reguły wykorzystanie Procesora na podstawie subskrypcji na.

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>Obniżenie kosztów przez wyeliminowanie nieudostępniany obwody usługi ExpressRoute
Klasyfikator identyfikuje obwody usługi ExpressRoute, które zostały w widoku stanu dostawcy *nieudostępniane* więcej niż jeden miesiąc i zaleca usunięcie obwodu, jeśli nie planuje udostępnić obwodu łączność Dostawca.

## <a name="buy-virtual-machine-reserved-instances-to-save-money-over-pay-as-you-go-costs"></a>Kup wystąpienia zarezerwowane maszyny wirtualnej, aby zaoszczędzić pieniądze w porównaniu z kosztami płatności zgodnie z rzeczywistym użyciem
Klasyfikator będzie Przejrzyj użycie maszyny wirtualnej w ciągu ostatnich 30 dni, aby ustalić, jeśli można zapisać pieniędzy po zakupie wystąpień zastrzeżone. Klasyfikator opisano regiony i rozmiary, gdzie potencjalnie masz większości oszczędności i wyświetli szacowany oszczędności zakupów wystąpień zastrzeżone. 

Zarezerwowane wystąpień wstępnie kupić podstawowej kosztów maszyn wirtualnych. Zniżki zostanie automatycznie zastosowana do nowej lub istniejącej maszyny wirtualne, które mają ten sam rozmiar i regionu jako swoich wystąpień zastrzeżone. [Dowiedz się więcej o zastrzeżonych wystąpień maszyn wirtualnych Azure.](https://azure.microsoft.com/pricing/reserved-vm-instances/)

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Jak uzyskać dostęp zalecenia koszt klasyfikatora Azure

1. Zaloguj się do [portalu Azure](https://portal.azure.com), a następnie otwórz [Advisor](https://aka.ms/azureadvisordashboard).

2.  Na pulpicie nawigacyjnym usługi Advisor, kliknij przycisk **koszt** kartę.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat zalecenia doradcy w zakresie, zobacz:
* [Wprowadzenie do usługi Advisor](advisor-overview.md)
* [Rozpoczęcie pracy](advisor-get-started.md)
* [Zalecenia doradcy w zakresie wydajności](advisor-cost-recommendations.md)
* [Zalecenia doradcy w zakresie wysokiej dostępności](advisor-cost-recommendations.md)
* [Zalecenia doradcy w zakresie zabezpieczeń](advisor-cost-recommendations.md)
