---
title: Konfigurowanie zasad replikacji dla odzyskiwania po awarii VMware za pomocą usługi Azure Site Recovery| Dokumenty firmy Microsoft
description: W tym artykule opisano sposób konfigurowania ustawień replikacji odzyskiwania po awarii VMware na platformie Azure za pomocą usługi Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: sutalasi
ms.openlocfilehash: 45921bdf802a649b7b802f44d2842a543e44f02b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257123"
---
# <a name="configure-and-manage-replication-policies-for-vmware-disaster-recovery"></a>Konfigurowanie zasad replikacji i zarządzanie nimi w przypadku odzyskiwania po awarii usługi VMware

W tym artykule opisano sposób konfigurowania zasad replikacji podczas replikowania maszyn wirtualnych VMware na platformie Azure przy użyciu [usługi Azure Site Recovery.](site-recovery-overview.md)

## <a name="create-a-policy"></a>Tworzenie zasad

1. Wybierz **pozycję Zarządzaj** > **infrastrukturą odzyskiwania witryn**.
2. W **obszarze Dla komputerów VMware i Fizyczne**wybierz pozycję Zasady **replikacji**.
3. Kliknij **pozycję +Zasady replikacji**i określ nazwę zasad.
4. W **progu RPO**należy określić limit RPO. Alerty są generowane, gdy replikacja ciągła przekracza ten limit.
5. W obszarze **Przechowywanie punktów odzyskiwania** określ w godzinach, jak długie będzie okno przechowywania dla każdego punktu odzyskiwania. Chronione maszyny można odzyskać do dowolnego punktu w tym oknie przechowywania. W przypadku maszyn replikowanych do magazynu w warstwie Premium jest obsługiwany czas przechowywania do 24 godzin. Do 72 godzin jest obsługiwany dla standardowej pamięci masowej.
6. W **aplikacji spójne częstotliwości migawki**, wybierz z listy rozwijanej, jak często (w godzinach) punkty odzyskiwania, które zawierają migawki spójne z aplikacją powinny być tworzone. Jeśli chcesz wyłączyć generowanie punktów spójności aplikacji, wybierz wartość "Off" w rozwijanej.
7. Kliknij przycisk **OK**. Tworzenie zasad powinno potrwać od 30 do 60 sekund.

Podczas tworzenia zasad replikacji automatycznie tworzona jest pasująca zasada replikacji powrotu po awarii z sufiksem "powrót po awarii". Po utworzeniu zasady można ją edytować, zaznaczając ją > **Edytuj ustawienia**.

## <a name="associate-a-configuration-server"></a>Kojarzenie serwera konfiguracji

Skojarz zasady replikacji z lokalnym serwerem konfiguracji.

1. Kliknij przycisk **Skojarz**i wybierz serwer konfiguracji.

    ![Kojarzenie serwera konfiguracji](./media/vmware-azure-set-up-replication/associate1.png)
2. Kliknij przycisk **OK**. Kojarzenie serwera konfiguracji powinno potrwać od 1 do 2 minut.

    ![Kojarzenie serwera konfiguracji](./media/vmware-azure-set-up-replication/associate2.png)

## <a name="edit-a-policy"></a>Edytowanie zasad

Zasady replikacji można zmodyfikować po jej utworzeniu.

- Zmiany w zasadach są stosowane do wszystkich komputerów korzystających z zasad.
- Jeśli chcesz skojarzyć maszyny replikowane z inną zasadą replikacji, należy wyłączyć i ponownie obsługiwać ochronę odpowiednich maszyn.

Edytuj zasady w następujący sposób:
1. Wybierz **pozycję Zarządzaj zasadami** > replikacji**infrastruktury** > odzyskiwania**lokacji**.
2. Wybierz zasady replikacji, które chcesz zmodyfikować.
3. Kliknij **pozycję Edytuj ustawienia**i w razie potrzeby zaktualizuj pola czasu przechowywania punktów punktu odzyskiwania/czas przechowywania punktów odzyskiwania/częstotliwość migawek spójnych z aplikacją.
4. Jeśli chcesz wyłączyć generowanie punktów spójności aplikacji, wybierz wartość "Wyłączone" w rozwijaniu **częstotliwości migawki spójnej z aplikacją**pola .
5. Kliknij przycisk **Zapisz**. Zasady powinny zostać zaktualizowane w ciągu 30 do 60 sekund.



## <a name="disassociate-or-delete-a-replication-policy"></a>Usuwanie zespocjał lub usuwanie zasad replikacji

1. Wybierz zasady replikacji.
    a. Aby odłączyć zasady od serwera konfiguracji, upewnij się, że żadne zreplikowanych maszyn nie używa zasad. Następnie kliknij przycisk **Rozłącz .**
    b. Aby usunąć zasady, upewnij się, że nie jest skojarzony z serwerem konfiguracji. Następnie kliknij przycisk **Usuń**. Usunięcie powinno potrwać 30-60 sekund.
2. Kliknij przycisk **OK**.
