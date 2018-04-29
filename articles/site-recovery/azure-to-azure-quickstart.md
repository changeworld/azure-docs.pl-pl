---
title: Replikowanie maszyny wirtualnej platformy Azure w innym regionie platformy Azure (wersja zapoznawcza)
description: W tym przewodniku Szybki start podano kroki wymagane do zreplikowania maszyny wirtualnej platformy Azure z jednego regionu platformy Azure w innym regionie.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: quickstart
ms.date: 04/08/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: a317d54b56f72373d99af35b806cb231c2ef962e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="replicate-an-azure-vm-to-another-azure-region-preview"></a>Replikowanie maszyny wirtualnej platformy Azure w innym regionie platformy Azure (wersja zapoznawcza)

Usługa [Azure Site Recovery](site-recovery-overview.md) stanowi część strategii związanej z ciągłością biznesową i odzyskiwaniem po awarii przez zapewnienie niezawodnego działania aplikacji biznesowych podczas planowanych i nieplanowanych przestojów. Usługa Site Recovery zarządza odzyskiwaniem po awarii maszyn lokalnych i maszyn wirtualnych platformy Azure, a także organizuje to odzyskiwanie. Obejmuje to replikację, przechodzenie w tryb failover i odzyskiwanie.

W tym samouczku Szybki start opisano sposób replikowania maszyny wirtualnej platformy Azure w innym regionie platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do witryny Azure Portal na stronie http://portal.azure.com.

## <a name="enable-replication-for-the-azure-vm"></a>Włączanie replikacji maszyny wirtualnej platformy Azure

1. W witrynie Azure Portal kliknij pozycję **Maszyny wirtualne** i wybierz tę, którą chcesz zreplikować.

2. W obszarze **Ustawienia** kliknij pozycję **Odzyskiwanie po awarii (wersja zapoznawcza)**.
3. W obszarze **Konfigurowanie odzyskiwania po awarii** > **Region docelowy** wybierz region docelowy, w którym maszyna będzie replikowana.
4. W przypadku tego przewodnika Szybki Start należy zaakceptować ustawienia domyślne.
5. Kliknij pozycję **Włącz replikację**. Spowoduje to uruchomienie zadania włączającego replikację dla maszyny wirtualnej.

    ![włączanie replikacji](media/azure-to-azure-quickstart/enable-replication1.png)



## <a name="verify-settings"></a>Weryfikowanie ustawień

Po zakończeniu zadania replikacji można sprawdzić stan replikacji, zmodyfikować ustawienia replikacji i przetestować wdrożenie.

1. W menu maszyny wirtualnej kliknij przycisk **Odzyskiwanie po awarii (wersja zapoznawcza)**.
2. Można sprawdzić kondycję replikacji, utworzone punkty odzyskiwania oraz regiony źródłowy i docelowy na mapie.

   ![Stan replikacji](media/azure-to-azure-quickstart/replication-status.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Replikowanie maszyny wirtualnej w regionie podstawowym jest zatrzymywane w przypadku wyłączenia dla niej replikacji:

- Ustawienia replikacji źródła są automatycznie czyszczone.
- Zatrzymywane jest też rozliczanie usługi Site Recovery dla danej maszyny wirtualnej.

Aby zatrzymać replikację:

1. Wybierz maszynę wirtualną.
2. W obszarze **Odzyskiwanie po awarii (wersja zapoznawcza)** kliknij pozycję **Więcej**.
3. Kliknij pozycję **Wyłącz replikację**.

   ![Wyłączanie replikacji](media/azure-to-azure-quickstart/disable2-replication.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku Szybki start zreplikowano pojedynczą maszynę wirtualną w regionie pomocniczym.

> [!div class="nextstepaction"]
> [Konfigurowanie odzyskiwania po awarii dla maszyn wirtualnych platformy Azure](azure-to-azure-tutorial-enable-replication.md)
