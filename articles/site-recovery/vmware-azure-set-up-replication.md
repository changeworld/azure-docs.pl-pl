---
title: Konfigurowanie i zarządzanie zasadami replikacji w przypadku replikacji maszyn wirtualnych VMware z usługą Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania ustawień replikacji dla replikacji maszyn wirtualnych VMware do platformy Azure z usługą Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: sutalasi
ms.openlocfilehash: 6a8e6e7c6fbdbcbf58557a0896e976a608164041
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/08/2018
ms.locfileid: "29812670"
---
# <a name="configure-and-manage-replication-policies-for-vmware-replication"></a>Konfigurowanie i zarządzanie zasadami replikacji w przypadku replikacji maszyn wirtualnych VMware
W tym artykule opisano sposób konfigurowania zasad replikacji, Jeśli replikujesz maszyny wirtualne VMware do platformy Azure, przy użyciu [usługi Azure Site Recovery](site-recovery-overview.md).


## <a name="create-a-policy"></a>Utwórz zasady

1. Wybierz pozycję **Zarządzaj** > **Infrastruktura usługi Site Recovery**.
2. W **dla VMware i fizycznej maszyny**, wybierz pozycję **zasady replikacji**. 
3. Kliknij przycisk **+ zasad replikacji**, a następnie określ nazwę zasady.
5. W polu **Wartość progowa celu punktu odzyskiwania** określ limit celu punktu odzyskiwania. Alerty są generowane, gdy ciągłej replikacji przekracza ten limit.
6. W obszarze **Przechowywanie punktów odzyskiwania** określ w godzinach, jak długie będzie okno przechowywania dla każdego punktu odzyskiwania. Chronione maszyny można odzyskać do dowolnego punktu w tym oknie przechowywania. W przypadku maszyn replikowanych do magazynu w warstwie Premium jest obsługiwany czas przechowywania do 24 godzin. Do 72 godzin jest obsługiwana dla magazynu w warstwie standardowa.
7. W obszarze **Częstotliwość wykonywania migawek na poziomie aplikacji** określ, jak często (w minutach) będą tworzone punkty odzyskiwania zawierające migawki spójne z aplikacjami.
8. Kliknij przycisk **OK**. Tworzenie zasad powinno potrwać od 30 do 60 sekund.

Po utworzeniu zasad replikacji pasujących zasad replikacji powrotu po awarii jest tworzony automatycznie, wraz z sufiksem "powrotu po awarii". Po utworzeniu zasad, można ją edytować, wybierając go > **Edytuj ustawienia**.

## <a name="associate-a-configuration-server"></a>Skojarz serwer konfiguracji 

Skojarz zasady replikacji z lokalnego serwera konfiguracji.

1. Kliknij przycisk **skojarzyć**i wybierz serwer konfiguracji.

    ![Skojarz serwer konfiguracji](./media/vmware-azure-set-up-replication/associate1.png)

2. Kliknij przycisk **OK**. Kojarzenie serwera konfiguracji powinno potrwać od 1 do 2 minut.

    ![Kojarzenie serwera konfiguracji](./media/vmware-azure-set-up-replication/associate2.png)


## <a name="disassociate-or-delete-a-replication-policy"></a>Usuń skojarzenie lub usuwanie zasady replikacji
1. Wybierz zasady replikacji.
    a. Aby usunąć skojarzenie zasad z serwera konfiguracji, upewnij się, czy nie są replikowane komputery korzystają z zasad. Następnie kliknij przycisk **Utwórz skojarzenie**.
    b. Aby usunąć zasady, upewnij się, że nie został skojarzony z serwerem konfiguracji. Następnie kliknij przycisk **usunąć**. Powinien upłynąć 30 – 60 sekund do usunięcia.
2. Kliknij przycisk **OK**.
