---
title: Konfigurowanie odzyskiwania po awarii maszyny Wirtualnej platformy Azure w regionie pomocniczym za pomocą usługi Azure Site Recovery
description: Szybko skonfiguruj odzyskiwanie po awarii w innym regionie platformy Azure dla maszyny Wirtualnej platformy Azure przy użyciu usługi Azure Site Recovery.
ms.topic: quickstart
ms.date: 03/27/2020
ms.custom: mvc
ms.openlocfilehash: e26c2a1f24a88dc979f4ec68de65afc618740c00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371858"
---
# <a name="quickstart-set-up-disaster-recovery-to-a-secondary-azure-region-for-an-azure-vm"></a>Szybki start: konfigurowanie odzyskiwania po awarii w pomocniczym regionie platformy Azure dla maszyny Wirtualnej platformy Azure

Usługa [Azure Site Recovery](site-recovery-overview.md) przyczynia się do ciągłości biznesowej i odzyskiwania po awarii (BCDR), utrzymując aplikacje biznesowe w trybie online podczas planowanych i nieplanowanych awarii. Usługa Site Recovery zarządza odzyskiwaniem po awarii na komputerach lokalnych i maszynach wirtualnych platformy Azure (VM), w tym replikacją, trybem failover i odzyskiwaniem.

W tym przewodniku Szybki start opisano sposób konfigurowania odzyskiwania po awarii dla maszyny Wirtualnej platformy Azure przez replikowanie jej do pomocniczego regionu platformy Azure. Ogólnie rzecz biorąc ustawienia domyślne są używane do włączania replikacji.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, potrzebujesz subskrypcji platformy Azure i maszyny Wirtualnej.

- Jeśli nie masz konta platformy Azure z aktywną subskrypcją, możesz [utworzyć konto za darmo.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Zaleca się maszynę wirtualną z co najmniej 1 GB pamięci RAM. [Dowiedz się więcej](/azure/virtual-machines/windows/quick-create-portal) o tworzeniu maszyny Wirtualnej.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do [Portalu Azure](https://portal.azure.com).

## <a name="enable-replication-for-the-azure-vm"></a>Włączanie replikacji maszyny wirtualnej platformy Azure

Poniższe kroki umożliwiają replikację maszyny Wirtualnej do lokalizacji dodatkowej.

1. W witrynie Azure portal z menu **Domowe** > **maszyny wirtualne** wybierz maszynę wirtualną do replikacji.
1. W **obszarze Operacje** wybierz opcję Odzyskiwanie po **awarii**.
1. W regionie Podstawowe informacje **docelowe** > **Target region**wybierz region docelowy.
1. Aby wyświetlić ustawienia replikacji, wybierz **pozycję Recenzja + Rozpocznij replikację**. Jeśli chcesz zmienić ustawienia domyślne, wybierz opcję **Ustawienia zaawansowane**.
1. Aby uruchomić zadanie umożliwiające replikację maszyny Wirtualnej, wybierz **opcję Rozpocznij replikację**.

   :::image type="content" source="media/azure-to-azure-quickstart/enable-replication1.png" alt-text="Włącz replikację.":::

## <a name="verify-settings"></a>Weryfikowanie ustawień

Po zakończeniu zadania replikacji można sprawdzić stan replikacji, zmodyfikować ustawienia replikacji i przetestować wdrożenie.

1. W menu portalu platformy Azure wybierz maszyny **wirtualne** i wybierz zreplikowaną maszynę wirtualną.
1. W **obszarze Operacje** wybierz opcję Odzyskiwanie po **awarii**.
1. Aby wyświetlić szczegóły replikacji z **przeglądu,** wybierz **opcję Podstawowe**programy . Więcej szczegółów przedstawiono w pliku **Zdrowie i stan**, **Gotowość do pracy awaryjnej**oraz na mapie **widoku Infrastruktura.**

   :::image type="content" source="media/azure-to-azure-quickstart/replication-status.png" alt-text="Stan replikacji.":::

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby zatrzymać replikację maszyny Wirtualnej w regionie podstawowym, należy wyłączyć replikację:

- Ustawienia replikacji źródła są automatycznie czyszczone.
- Rozszerzenie odzyskiwania lokacji zainstalowane na maszynie wirtualnej podczas replikacji nie jest usuwane.
- Płatności usługi Site Recovery dla maszyny Wirtualnej są zatrzymane.

Aby wyłączyć replikację, wykonaj następujące czynności:

1. W menu portalu platformy Azure wybierz maszyny **wirtualne** i wybierz zreplikowaną maszynę wirtualną.
1. W **obszarze Operacje** wybierz opcję Odzyskiwanie po **awarii**.
1. Z **przeglądu**wybierz pozycję **Wyłącz replikację**.
1. Aby odinstalować rozszerzenie Odzyskiwania witryny, przejdź do**rozszerzenia** **ustawień maszyny** > Wirtualnej .

   :::image type="content" source="media/azure-to-azure-quickstart/disable2-replication.png" alt-text="Wyłącz replikację.":::

## <a name="next-steps"></a>Następne kroki

W tym samouczku Szybki start zreplikowano pojedynczą maszynę wirtualną w regionie pomocniczym. Następnie skonfiguruj replikację dla wielu maszyn wirtualnych platformy Azure.

> [!div class="nextstepaction"]
> [Konfigurowanie odzyskiwania po awarii dla maszyn wirtualnych platformy Azure](azure-to-azure-tutorial-enable-replication.md)
