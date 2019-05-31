---
title: Wymagania wstępne maszyny wirtualnej dla systemu Microsoft Azure | Portal Azure Marketplace
description: Lista wymagań wstępnych dotyczących publikowanie oferty maszyny Wirtualnej w portalu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 03/13/2019
ms.author: pabutler
ms.openlocfilehash: 258d21eae5af50b5dc0bed6887618e2999cae45a
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/28/2019
ms.locfileid: "66257387"
---
# <a name="virtual-machine-prerequisites"></a>Wymagania wstępne maszyny wirtualnej

W tym artykule wymieniono zarówno techniczne i oferty maszyny Wirtualnej, aby opublikować wymagań biznesowych, które muszą spełnić, przed [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/).  Jeśli nie zostało to jeszcze zrobione, zapoznaj się z [Podręcznik publikowania oferty maszyny wirtualnej](../../marketplace-virtual-machines.md).


## <a name="technical-requirements"></a>Wymagania techniczne

Techniczne wymagania wstępne dotyczące publikowania rozwiązania maszyny wirtualnej (VM) są oczywiste:

- Musisz mieć aktywne konto platformy Azure. Jeśli nie masz, możesz zarejestrować się się w [witryny Microsoft Azure](https://azure.microsoft.com).  
- Konieczne jest posiadanie środowisko skonfigurowane do obsługi tworzenia maszyny Wirtualnej systemu Linux lub Windows.  Aby uzyskać więcej informacji zobacz skojarzone witrynie dokumentacji maszyn wirtualnych:
    - [Dokumentacja dotycząca maszyn wirtualnych z systemem Linux](https://docs.microsoft.com/azure/virtual-machines/linux/)
    - [Dokumentacja maszyny wirtualnej Windows](https://docs.microsoft.com/azure/virtual-machines/windows/)


## <a name="business-requirements"></a>Wymagania biznesowe

Wymagania biznesowe obejmują procedurach umowne stosowane w UE oraz prawne zobowiązań: 

<!-- TD: Aren't most of these business requirements common to all AMP offerings?  If yes, then move to higher level, perhaps to the AMP section "Become a Cloud Marketplace Publisher" -->
<!-- TD: Need references for remaining docs/business reqs!-->

- Musi być zarejestrowany wydawcy chmury w witrynie Marketplace.  Jeśli nie zostały jeszcze zarejestrowane, postępuj zgodnie z instrukcjami w artykule [stają się wydawcy w witrynie Marketplace chmury](https://docs.microsoft.com/azure/marketplace/become-publisher).

    > [!NOTE]
    > Należy używać tego samego konta Microsoft Developer Center rejestrowania się na [portalu Cloud Partner](https://cloudpartner.azure.com).
    > Powinna mieć tylko jedno konto Microsoft, dla ofert portalu Azure Marketplace. Nie powinien być specyficzne dla poszczególnych usług lub oferty.
    
- Siedziba firmy (lub firmy od niej zależnej) musi znajdować się w sprzedaży z kraj/region obsługiwanym przez Portal Azure Marketplace.  Aby uzyskać bieżącą listę tych krajów/regionów, zobacz [Microsoft Azure Marketplace — zasady udziału](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
- Musi być licencjonowany produkt w sposób, który jest zgodny z modelami rozliczeń obsługiwanym przez Portal Azure Marketplace.  Aby uzyskać więcej informacji, zobacz [opcje rozliczeń w witrynie Azure Marketplace](https://docs.microsoft.com/azure/marketplace/billing-options-azure-marketplace). 
- Odpowiedzialność za udostępnianie pomocy technicznej dla klientów w sposób rozsądny z komercyjnego punktu widzenia. Może to mieć wolne, płatną lub za pośrednictwem metody społeczności.
- Jesteś odpowiedzialny za Licencjonowanie oprogramowania oraz wszystkie zależności oprogramowania innych firm.
- Należy podać zawartość, która spełnia kryteria za ofertę użytkownika był wyświetlany w portalu Azure Marketplace i w witrynie Azure portal. <!-- TD: Meaning/links? -->
- Musisz wyrazić zgodę na warunki [Microsoft Azure Marketplace — zasady udziału](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) oraz Umowie wydawcy portalu.
- Muszą być zgodne z [Microsoft Azure warunkami korzystania z witryny](https://azure.microsoft.com/support/legal/website-terms-of-use/), [zasady zachowania poufności informacji firmy Microsoft](https://privacy.microsoft.com/privacystatement), i [umowę programu certyfikat platformy Azure Microsoft](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/).


## <a name="next-steps"></a>Kolejne kroki

Po zostały spełnione następujące wymagania wstępne, możesz [Tworzenie oferty maszyny Wirtualnej](./cpp-create-offer.md).
