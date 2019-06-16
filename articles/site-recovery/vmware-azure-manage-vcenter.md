---
title: Zarządzanie serwerami vCenter VMware do odzyskiwania po awarii maszyn wirtualnych programu VMware do platformy Azure przy użyciu usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano jak dodać i zarządzać oprogramowaniem VMware vCenter do odzyskiwania po awarii maszyn wirtualnych programu VMware na platformę Azure za pomocą usługi Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: ramamill
ms.openlocfilehash: 9694c682f171ab715812b05fed2064c9bbcd36b3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60600369"
---
# <a name="manage-vmware-vcenter-server"></a>Zarządzaj serwerem VMware vCenter

W tym artykule omówiono różne operacje odzyskiwania lokacji, które mogą być wykonywane na serwerze VMware vCenter. Sprawdź [wymagania wstępne](vmware-physical-azure-support-matrix.md#replicated-machines) przed rozpoczęciem.


## <a name="set-up-an-account-for-automatic-discovery"></a>Konfigurowanie konta do automatycznego odnajdowania

Usługa Site Recovery wymaga dostępu do zasobów programu VMware, serwer przetwarzania w celu automatycznego wykrywania maszyn wirtualnych i trybu failover i powrotu po awarii maszyn wirtualnych. Utwórz konto dostępu w następujący sposób:

1. Zaloguj się na komputerze serwera konfiguracji.
2. Otwórz uruchomienia cspsconfigtool.exe przy użyciu skrótu na pulpicie.
3. Kliknij przycisk **Dodaj konto** na **Zarządzaj kontem** kartę.

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)
1. Podaj szczegóły konta, a następnie kliknij przycisk **OK** ją dodać.  Konto powinno mieć uprawnienia podsumowane w poniższej tabeli. 

Trwa około 15 minut, aby uzyskać informacje o koncie, które można synchronizować się z usługą Site Recovery.

### <a name="account-permissions"></a>Uprawnień konta

|**Zadanie** | **Konto** | **Uprawnienia** | **Szczegóły**|
|--- | --- | --- | ---|
|**Automatyczne odnajdowanie/migracji (bez powrotu po awarii)** | Potrzebujesz co najmniej użytkownik tylko do odczytu | Obiekt centrum danych –> propagacja do obiektu podrzędnego, rola = tylko do odczytu | Użytkownik przypisany na poziomie centrum danych, mający dostęp do wszystkich obiektów w centrum danych.<br/><br/> Aby ograniczyć dostęp, Przypisz **bez dostępu** roli **Propagacja do obiektu podrzędnego** obiektu do obiektów podrzędnych (hostów vSphere, magazynów danych, maszyny wirtualne i sieci).|
|**Replikacji i pracy awaryjnej** | Potrzebujesz co najmniej użytkownik tylko do odczytu| Obiekt centrum danych –> propagacja do obiektu podrzędnego, rola = tylko do odczytu | Użytkownik przypisany na poziomie centrum danych, mający dostęp do wszystkich obiektów w centrum danych.<br/><br/> Aby ograniczyć dostęp, Przypisz **bez dostępu** roli **Propagacja do obiektu podrzędnego** obiektu do obiektów podrzędnych (hostów vSphere, magazynów danych, maszyny wirtualne i sieci).<br/><br/> Przydatne do celów migracji, ale nie Pełna replikacja, tryb failover i powrotu po awarii.|
|**Replikacja/tryb failover/powrotu po awarii** | Zalecamy, aby utworzyć rolę (AzureSiteRecoveryRole) z wymaganymi uprawnieniami, a następnie przypisać rolę użytkownikowi lub grupie VMware | Data Center object –> Propagate to Child Object, role=AzureSiteRecoveryRole<br/><br/> Magazyn danych -> przydzielanie miejsca, przegląd magazynu danych, operacje na plikach niskiego poziomu, usuwanie pliku, aktualizowanie plików maszyn wirtualnych<br/><br/> Sieć -> przypisywanie sieci<br/><br/> Zasób -> przypisywanie maszyny wirtualnej do puli zasobów, migracja wyłączonej maszyny wirtualnej, migracja włączonej maszyny wirtualnej<br/><br/> Zadania -> tworzenie zadania, aktualizowanie zadania<br/><br/> Maszyna wirtualna -> konfiguracja<br/><br/> Maszyna wirtualna -> interakcja -> odpowiadanie na pytanie, połączenie z urządzeniem, konfigurowanie nośnika CD, konfigurowanie dyskietki, wyłączanie, włączanie, instalowanie narzędzi VMware<br/><br/> Maszyna wirtualna -> spis -> tworzenie, rejestrowanie, wyrejestrowywanie<br/><br/> Maszyna wirtualna -> aprowizowanie -> zezwalanie na pobieranie maszyny wirtualnej, zezwalanie na przekazywanie plików maszyny wirtualnej<br/><br/> Maszyna wirtualna -> migawki -> usuwanie migawek | Użytkownik przypisany na poziomie centrum danych, mający dostęp do wszystkich obiektów w centrum danych.<br/><br/> Aby ograniczyć dostęp, Przypisz **bez dostępu** roli **Propagacja do obiektu podrzędnego** obiektu do obiektów podrzędnych (hostów vSphere, magazynów danych, maszyny wirtualne i sieci).|


## <a name="add-vmware-server-to-the-vault"></a>Dodawanie serwera VMware w magazynie

1. W witrynie Azure portal Otwórz magazynu > **infrastruktura usługi Site Recovery** > **serwery konfiguracji**, a następnie otwórz na serwerze konfiguracji.
2. Na **szczegóły** kliknij **+ vCenter**.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]

## <a name="modify-credentials"></a>Zmodyfikowania poświadczeń

Zmień poświadczenia używane do łączenia z serwerem vCenter lub hoście ESXi w następujący sposób:

1. Zaloguj się do serwera konfiguracji, a następnie uruchom cspsconfigtool.exe z pulpitu.
2. Kliknij przycisk **Dodaj konto** na **Zarządzaj kontem** kartę.

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)
3. Podaj nowe szczegóły konta, a następnie kliknij przycisk **OK** ją dodać. Konto musi mieć uprawnienia wymienione [powyżej](#account-permissions).
4. W witrynie Azure portal Otwórz magazynu > **infrastruktura usługi Site Recovery** > **serwery konfiguracji**, a następnie otwórz na serwerze konfiguracji.
5. W **szczegóły** kliknij **Odśwież serwer**.
6. Po zakończeniu zadania odświeżania serwera wybierz serwer vCenter Server, aby otworzyć vCenter **Podsumowanie** strony.
7. Wybierz nowo dodane konto w **konto hostów programu vCenter server/vSphere** pola, a następnie kliknij przycisk **Zapisz**.

   ![modify-account](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>Usuwanie serwera vCenter

1. W witrynie Azure portal Otwórz magazynu > **infrastruktura usługi Site Recovery** > **serwery konfiguracji**, a następnie otwórz na serwerze konfiguracji.
2. Na **szczegóły** wybierz serwer vCenter.
3. Kliknij pozycję **Usuń** przycisku.

   ![delete-account](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

## <a name="modify-the-vcenter-ip-address-and-port"></a>Modyfikowanie adresu IP vCenter i portu

1. Zaloguj się do Portalu Azure.
2. Przejdź do **magazyn usługi Recovery Services** > **infrastruktura usługi Site Recovery** > **serwery konfiguracji**.
3. Kliknij na serwerze konfiguracji serwer vCenter jest przypisany do.
4. W **serwerów vCenter** sekcji, kliknij serwer vCenter, który chcesz zmodyfikować.
5. Na stronie podsumowania vCenter zaktualizuj adres IP i port serwer vCenter w odpowiednich polach, a następnie zapisz zmiany.

   ![add_ip_new_vcenter](media/vmware-azure-manage-vcenter/add-ip.png)

6. Aby zmiany zaczynają obowiązywać, poczekaj 15 minut lub [Odśwież serwer konfiguracji](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="migrate-all-protected-virtual-machines-to-a-new-vcenter"></a>Migrowanie wszystkich chronionych maszyn wirtualnych do nowego vCenter

Aby przeprowadzić migrację wszystkich maszyn wirtualnych na nowy serwer vCenter, nie należy dodawać inne konto vCenter. Może to prowadzić do zduplikowane wpisy. Wystarczy zaktualizować adres IP nowego vCenter:

1. Zaloguj się do Portalu Azure.
2. Przejdź do **magazyn usługi Recovery Services** > **infrastruktura usługi Site Recovery** > **serwery konfiguracji**.
3. Kliknij na serwerze konfiguracji stary serwer vCenter jest przypisany do.
4. W **serwerów vCenter** sekcji, kliknij serwer vCenter jest planowana migracja z.
5. Na stronie podsumowania vCenter, zaktualizuj adres IP nowy serwer vCenter w polu **vCenter server/vSphere hosta lub adres IP**. Zapisz zmiany.

Jak adres IP zostanie zaktualizowany, składniki usługi Site Recovery rozpocznie się odbieranie informacji odnajdywania maszyn wirtualnych z nowego serwera vCenter. To nie ma wpływu na trwającą replikację działania.

## <a name="migrate-few-protected-virtual-machines-to-a-new-vcenter"></a>Migrowanie kilka chronionych maszyn wirtualnych do nowego vCenter

> [!NOTE]
> Ta sekcja ma zastosowanie tylko podczas migrowania kilka chronionych maszyn wirtualnych do nowego vCenter. Jeśli chcesz, aby chronić nowy zestaw maszyn wirtualnych z programu vCenter nowe, [Dodaj nowe szczegóły vCenter do serwera konfiguracji](#add-vmware-server-to-the-vault) i rozpoczynać  **[Włącz ochronę](vmware-azure-tutorial.md#enable-replication)** .

Aby przenieść kilku maszyn wirtualnych do nowego vCenter:

1. [Dodaj nowe szczegóły vCenter do serwera konfiguracji](#add-vmware-server-to-the-vault).
2. [Wyłącz replikację maszyn wirtualnych](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) planujesz migrować.
3. Zakończenie migracji wybranych maszyn wirtualnych na nowy serwer vCenter.
4. Teraz, ochrona migrowanych maszyn wirtualnych za pomocą [wybierając nowy serwer vCenter, po włączeniu ochrony](vmware-azure-tutorial.md#enable-replication).

> [!TIP]
> Jeśli liczba migrowanych maszyn wirtualnych jest **wyższe** , liczby maszyn wirtualnych, przechowywane w starym vCenter zaktualizowanie adresu IP nowego vcenter, wykonując instrukcje podane w tym miejscu. Kilka maszyn wirtualnych, które mają zostać zachowane podczas stary serwer vCenter [Wyłącz replikację](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure); [Dodaj nowe szczegóły vCenter do serwera konfiguracji](#add-vmware-server-to-the-vault)i Rozpocznij  **[Włącz ochronę](vmware-azure-tutorial.md#enable-replication)** .

## <a name="frequently-asked-questions"></a>Często zadawane pytania

1. Jeśli chronione maszyny wirtualne są przenoszone z hosta ESXi, będzie ona wpływu na replikację?

    Nie, to nie ma wpływu na trwającą replikację. Jednak [upewnij się, że wdrażanie główny serwer docelowy o odpowiednich uprawnieniach](vmware-azure-reprotect.md#deploy-a-separate-master-target-server)

2. Co to są numery portów używane do komunikacji między vCenter i inne usługi Site Recovery składniki?

    Domyślny port to 443. Serwer konfiguracji będzie uzyskiwać dostęp do informacji vCenter/hostem vSphere, za pośrednictwem tego portu. Jeśli chcesz zaktualizować te informacje, kliknij przycisk [tutaj](#modify-the-vcenter-ip-address-and-port).
