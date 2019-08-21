---
title: Włącz połączenie przeglądarki na maszynach wirtualnych Azure DevTest Labs | Microsoft Docs
description: DevTest Labs teraz integrują się z usługą Azure bastionu, jako właściciel laboratorium można włączyć dostęp do wszystkich maszyn wirtualnych laboratorium za pomocą przeglądarki.
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
ms.date: 08/19/2019
ms.author: takamath
ms.openlocfilehash: 2ddc56c60c547bd4ce48d620a83fb79246762bfb
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642489"
---
# <a name="enable-browser-connection-on-lab-virtual-machines"></a>Włącz połączenie przeglądarki na maszynach wirtualnych laboratorium 

DevTest Labs integrują się z [usługą Azure bastionu](https://docs.microsoft.com/azure/bastion/), która umożliwia łączenie się z maszynami wirtualnymi za pomocą przeglądarki. Najpierw należy włączyć połączenie przeglądarki na maszynach wirtualnych laboratorium.

Jako właściciel laboratorium można włączyć dostęp do wszystkich maszyn wirtualnych laboratorium za pomocą przeglądarki. Nie jest potrzebny dodatkowy klient, agent ani oprogramowanie. Usługa Azure bastionu zapewnia bezpieczną i bezproblemową łączność protokołu RDP/SSH z maszynami wirtualnymi bezpośrednio w Azure Portal za pośrednictwem protokołu SSL. Po nawiązaniu połączenia za pośrednictwem usługi Azure bastionu maszyny wirtualne nie potrzebują publicznego adresu IP. Aby uzyskać więcej informacji, zobacz [co to jest usługa Azure bastionu?](../bastion/bastion-overview.md)

> [!NOTE]
> Włączenie połączenia przeglądarki na maszynach wirtualnych laboratorium jest w wersji zapoznawczej.

W tym artykule przedstawiono sposób włączania połączenia przeglądarki na maszynach wirtualnych laboratorium.

## <a name="prerequisites"></a>Wymagania wstępne 
Wdróż hosta bastionu w istniejącej sieci wirtualnej laboratorium **(lub)** Połącz laboratorium ze skonfigurowaną siecią wirtualną bastionu. 

Aby dowiedzieć się, jak wdrożyć hosta bastionu w sieci wirtualnej, zobacz [Tworzenie hosta usługi Azure bastionu (wersja zapoznawcza)](../bastion/bastion-create-host-portal.md). Podczas tworzenia hosta bastionu wybierz sieć wirtualną laboratorium. 

Aby dowiedzieć się, jak połączyć laboratorium ze skonfigurowaną siecią wirtualną bastionu, zobacz [Konfigurowanie sieci wirtualnej w Azure DevTest Labs](devtest-lab-configure-vnet.md). Wybierz sieć wirtualną, w której wdrożono hosta bastionu i **AzureBastionSubnet** . Poniżej przedstawiono szczegółowe czynności: 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz pozycję **wszystkie usługi** w menu nawigacji po lewej stronie. 
1. Wybierz z listy pozycję **DevTest Labs**. 
1. Z listy laboratoriów wybierz *laboratorium*. 

    > [!NOTE]
    > Usługa Azure bastionu jest obecnie dostępna w wersji zapoznawczej. Jest to ograniczone do następujących regionów: Zachodnie stany USA, Wschodnie stany USA, Europa Zachodnia, Południowo-środkowe stany USA, Australia Wschodnia i Japonia Wschodnia. Utwórz laboratorium w jednym z tych regionów, jeśli laboratorium nie znajduje się w jednym z nich. 
1. Wybierz pozycję **Konfiguracja i zasady** w sekcji **Ustawienia** w menu po lewej stronie. 
1. Wybierz pozycję **sieci wirtualne**.
1. Wybierz pozycję **Dodaj** z paska narzędzi. 
1. Wybierz **sieć wirtualną** , dla której wdrożono hosta bastionu. 
1. Wybierz podsieć: **AzureBastionSubnet**. 

    ![Subnet](./media/enable-browser-connection-lab-virtual-machines/subnet.png)
1. Wybierz opcję **Użyj podczas tworzenia maszyny wirtualnej** . 
1. Wybierz pozycję **Zapisz** na pasku narzędzi. 
1. Jeśli masz starą sieć wirtualną dla laboratorium, usuń ją, wybierając pozycję * *...*  i **Usuń**. 

## <a name="enable-browser-connection"></a>Włącz połączenie przeglądarki 

Po skonfigurowaniu bastionu sieci wirtualnej w laboratorium jako właściciela laboratorium można włączyć opcję Połącz z przeglądarką na maszynach wirtualnych laboratorium.

Aby włączyć program Browser Connect na maszynach wirtualnych laboratorium, wykonaj następujące kroki:

1. W Azure Portal przejdź do *laboratorium*.
1. Wybierz pozycję **Konfiguracja i zasady**.
1. W obszarze **Ustawienia**wybierz pozycję **przeglądarka Połącz (wersja zapoznawcza)** .

![Włącz połączenie przeglądarki](./media/enable-browser-connection-lab-virtual-machines/browser-connect.png)

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z następującym artykułem, aby dowiedzieć się, jak nawiązać połączenie z maszynami wirtualnymi przy użyciu przeglądarki: [Łączenie się z maszynami wirtualnymi za pomocą przeglądarki](connect-virtual-machine-through-browser.md)