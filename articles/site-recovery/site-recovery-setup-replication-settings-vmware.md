---
title: Konfigurowanie ustawień replikacji dla usługi Azure Site Recovery | Microsoft Docs
description: Opis sposobu wdrożenia usługi Site Recovery w celu zaaranżowania replikacji, pracy w trybie failover i odzyskiwania maszyn wirtualnych funkcji Hyper-V w chmurach VMM do platformy Azure.
services: site-recovery
documentationcenter: ''
author: sujayt
manager: rochakm
editor: rayne-wiselman
ms.assetid: 8e7d868e-00f3-4e8b-9a9e-f23365abf6ac
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 02/22/2018
ms.author: sutalasi
ms.openlocfilehash: 0a567f31bf1991d4c2a95468d2abc31c51a878f3
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2018
ms.locfileid: "29767428"
---
# <a name="manage-replication-policy-for-vmware-to-azure"></a>Zarządzanie zasadami replikacji oprogramowania VMware na platformie Azure


## <a name="create-a-replication-policy"></a>Tworzenie zasad replikacji

1. Wybierz pozycję **Zarządzaj** > **Infrastruktura usługi Site Recovery**.
2. Wybierz pozycję **Zasady replikacji** w sekcji **Oprogramowanie VMware i maszyny fizyczne**.
3. Wybierz pozycję **+Zasady replikacji**.

    ![Tworzenie zasad replikacji](./media/site-recovery-setup-replication-settings-vmware/createpolicy.png)

4. Wprowadź nazwę zasad.

5. W polu **Wartość progowa celu punktu odzyskiwania** określ limit celu punktu odzyskiwania. Przekroczenie tego limitu przez replikację ciągłą będzie powodować generowanie alertów.
6. W obszarze **Przechowywanie punktów odzyskiwania** określ w godzinach, jak długie będzie okno przechowywania dla każdego punktu odzyskiwania. Chronione maszyny można odzyskać do dowolnego punktu w tym oknie przechowywania.

    > [!NOTE]
    > W przypadku maszyn replikowanych do magazynu w warstwie Premium jest obsługiwany czas przechowywania do 24 godzin. W przypadku maszyn replikowanych do magazynu w warstwie Standardowa jest obsługiwany czas przechowywania do 72 godzin.

    > [!NOTE]
    > Zasady replikacji na potrzeby powrotu po awarii są tworzone automatycznie.

7. W obszarze **Częstotliwość wykonywania migawek na poziomie aplikacji** określ, jak często (w minutach) będą tworzone punkty odzyskiwania zawierające migawki spójne z aplikacjami.

8. Kliknij przycisk **OK**. Tworzenie zasad powinno potrwać od 30 do 60 sekund.

![Generowanie zasad replikacji](./media/site-recovery-setup-replication-settings-vmware/Creating-Policy.png)

## <a name="associate-a-configuration-server-with-a-replication-policy"></a>Kojarzenie serwera konfiguracji z zasadami replikacji
1. Wybierz zasady replikacji, z którymi chcesz skojarzyć serwer konfiguracji.
2. Kliknij pozycję **Skojarz**.
![Kojarzenie serwera konfiguracji](./media/site-recovery-setup-replication-settings-vmware/Associate-CS-1.PNG)

3. Z listy serwerów wybierz serwer konfiguracji.
4. Kliknij przycisk **OK**. Kojarzenie serwera konfiguracji powinno potrwać od 1 do 2 minut.

![Kojarzenie serwera konfiguracji](./media/site-recovery-setup-replication-settings-vmware/Associate-CS-2.png)

## <a name="edit-a-replication-policy"></a>Edytowanie zasad replikacji
1. Wybierz zasady replikacji, dla których chcesz edytować ustawienia replikacji.
![Edytowanie zasad replikacji](./media/site-recovery-setup-replication-settings-vmware/Select-Policy.png)

2. Kliknij pozycję **Edytuj ustawienia**.
![Edytowanie ustawień zasad replikacji](./media/site-recovery-setup-replication-settings-vmware/Edit-Policy.png)

3. Zmień ustawienia zgodnie z Twoimi wymaganiami.
4. Kliknij pozycję **Zapisz**. Zapisywanie zasad powinno potrwać od 2 do 5 minut w zależności od tego, ile maszyn wirtualnych korzysta z tych zasad replikacji.

![Zapisywanie zasad replikacji](./media/site-recovery-setup-replication-settings-vmware/Save-Policy.png)

## <a name="dissociate-a-configuration-server-from-a-replication-policy"></a>Usuwanie skojarzenia serwera konfiguracji z zasad replikacji
1. Wybierz zasady replikacji, z którymi chcesz skojarzyć serwer konfiguracji.
2. Kliknij pozycję **Usuń skojarzenie**.
3. Z listy serwerów wybierz serwer konfiguracji.
4. Kliknij przycisk **OK**. Usuwanie skojarzenia serwera konfiguracji powinno potrwać od 1 do 2 minut.

    > [!NOTE]
    > Nie można usunąć skojarzenia serwera konfiguracji, jeśli co najmniej jeden zreplikowany element korzysta z zasad. Przed usunięciem skojarzenia serwera konfiguracji upewnij się, że żadne zreplikowane elementy nie korzystają z zasad.

## <a name="delete-a-replication-policy"></a>Usuwanie zasad replikacji

1. Wybierz zasady replikacji, które chcesz usunąć.
2. Kliknij polecenie **Usuń**. Usuwanie zasad powinno potrwać od 30 do 60 sekund.

    > [!NOTE]
    > Nie można usunąć zasad replikacji, jeśli jest z nimi skojarzony co najmniej jeden serwer konfiguracji. Przed usunięciem zasad upewnij się, że żadne zreplikowane elementy nie korzystają z zasad, i usuń wszystkie skojarzone serwery konfiguracji.
