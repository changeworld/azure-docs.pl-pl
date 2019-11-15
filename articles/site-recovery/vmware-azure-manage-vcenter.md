---
title: Zarządzanie serwerami VMware vCenter w Azure Site Recovery
description: W tym artykule opisano, jak dodawać i zarządzać programem VMware vCenter na potrzeby odzyskiwania po awarii maszyn wirtualnych VMware na platformie Azure przy użyciu Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: ramamill
ms.openlocfilehash: 8f339103f67f37d10999ef43fa57a6eb27b60f37
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083975"
---
# <a name="manage-vmware-vcenter-server"></a>Zarządzanie programem VMware vCenter Server

W tym artykule omówiono różne operacje Site Recovery, które można wykonać w oprogramowaniu VMware vCenter. Przed rozpoczęciem Sprawdź [wymagania wstępne](vmware-physical-azure-support-matrix.md#replicated-machines) .


## <a name="set-up-an-account-for-automatic-discovery"></a>Konfigurowanie konta do automatycznego odnajdywania

Site Recovery musi mieć dostęp do programu VMware, aby serwer przetwarzania automatycznie wykrywał maszyny wirtualne oraz w celu przełączenia w tryb failover i powrotu po awarii maszyn wirtualnych. Utwórz konto, aby uzyskać dostęp w następujący sposób:

1. Zaloguj się na komputerze serwera konfiguracji.
2. Otwórz plik Uruchom cspsconfigtool. exe przy użyciu skrótu na pulpicie.
3. Kliknij pozycję **Dodaj konto** na karcie **Zarządzanie kontem** .

   ![Dodaj konto](./media/vmware-azure-manage-vcenter/addaccount.png)
1. Podaj szczegóły konta, a następnie kliknij przycisk **OK** , aby go dodać.  Konto powinno mieć uprawnienia podsumowane w poniższej tabeli. 

Synchronizacja informacji o koncie z usługą Site Recovery trwa około 15 minut.

### <a name="account-permissions"></a>Uprawnienia konta

|**Zadanie** | **Konto** | **Uprawnienia** | **Szczegóły**|
|--- | --- | --- | ---|
|**Automatyczne odnajdywanie/Migrowanie (bez powrotu po awarii)** | Potrzebujesz co najmniej użytkownika tylko do odczytu | Obiekt centrum danych –> propagacja do obiektu podrzędnego, rola = tylko do odczytu | Użytkownik przypisany na poziomie centrum danych, mający dostęp do wszystkich obiektów w centrum danych.<br/><br/> Aby ograniczyć dostęp, przypisz rolę **Brak dostępu** z obiektem **Propaguj do obiektu podrzędnego** do obiektów podrzędnych (hostów vSphere, magazynów danych, maszyn wirtualnych i sieci).|
|**Replikacja/tryb failover** | Potrzebujesz co najmniej użytkownika tylko do odczytu| Obiekt centrum danych –> propagacja do obiektu podrzędnego, rola = tylko do odczytu | Użytkownik przypisany na poziomie centrum danych, mający dostęp do wszystkich obiektów w centrum danych.<br/><br/> Aby ograniczyć dostęp, przypisz rolę **Brak dostępu** z obiektem **Propaguj do obiektu podrzędnego** do obiektów podrzędnych (hostów vSphere, magazynów danych, maszyn wirtualnych i sieci).<br/><br/> Przydatne do migracji, ale nie pełna replikacja, tryb failover i powrót po awarii.|
|**Replikacja/tryb failover/powrót po awarii** | Sugerujemy utworzenie roli (AzureSiteRecoveryRole) z wymaganymi uprawnieniami, a następnie przypisanie roli do użytkownika lub grupy programu VMware | Obiekt centrum danych — > propagowania do obiektu podrzędnego, rola = AzureSiteRecoveryRole<br/><br/> Magazyn danych -> przydzielanie miejsca, przegląd magazynu danych, operacje na plikach niskiego poziomu, usuwanie pliku, aktualizowanie plików maszyn wirtualnych<br/><br/> Sieć -> przypisywanie sieci<br/><br/> Zasób -> przypisywanie maszyny wirtualnej do puli zasobów, migracja wyłączonej maszyny wirtualnej, migracja włączonej maszyny wirtualnej<br/><br/> Zadania -> tworzenie zadania, aktualizowanie zadania<br/><br/> Maszyna wirtualna -> konfiguracja<br/><br/> Maszyna wirtualna -> interakcja -> odpowiadanie na pytanie, połączenie z urządzeniem, konfigurowanie nośnika CD, konfigurowanie dyskietki, wyłączanie, włączanie, instalowanie narzędzi VMware<br/><br/> Maszyna wirtualna -> spis -> tworzenie, rejestrowanie, wyrejestrowywanie<br/><br/> Maszyna wirtualna -> aprowizowanie -> zezwalanie na pobieranie maszyny wirtualnej, zezwalanie na przekazywanie plików maszyny wirtualnej<br/><br/> Maszyna wirtualna -> migawki -> usuwanie migawek | Użytkownik przypisany na poziomie centrum danych, mający dostęp do wszystkich obiektów w centrum danych.<br/><br/> Aby ograniczyć dostęp, przypisz rolę **Brak dostępu** z obiektem **Propaguj do obiektu podrzędnego** do obiektów podrzędnych (hostów vSphere, magazynów danych, maszyn wirtualnych i sieci).|


## <a name="add-vmware-server-to-the-vault"></a>Dodawanie serwera VMware do magazynu

1. W Azure Portal otwórz magazyn > **Site Recovery infrastruktury** > **konfiguracjami**i Otwórz serwer konfiguracji.
2. Na stronie **szczegółów** kliknij pozycję **+ vCenter**.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]

## <a name="modify-credentials"></a>Modyfikuj poświadczenia

Zmodyfikuj poświadczenia używane do nawiązania połączenia z serwerem vCenter lub hostem ESXi w następujący sposób:

1. Zaloguj się na serwerze konfiguracji i uruchom program cspsconfigtool. exe z pulpitu.
2. Kliknij pozycję **Dodaj konto** na karcie **Zarządzanie kontem** .

   ![Dodaj konto](./media/vmware-azure-manage-vcenter/addaccount.png)
3. Podaj szczegóły nowego konta, a następnie kliknij przycisk **OK** , aby go dodać. Konto powinno mieć uprawnienia wymienione [powyżej](#account-permissions).
4. Na Azure Portal otwórz magazyn > **Site Recovery infrastruktury** > **Configurations**i Otwórz serwer konfiguracji.
5. Na stronie **szczegółów** kliknij przycisk **Odśwież serwer**.
6. Po zakończeniu zadania odświeżania serwera wybierz vCenter Server, aby otworzyć stronę **podsumowania** programu vCenter.
7. W polu **konto hosta programu vCenter Server/vSphere** wybierz nowo dodane konto, a następnie kliknij przycisk **Zapisz**.

   ![modify-account](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>Usuwanie serwera vCenter

1. W Azure Portal otwórz magazyn > **Site Recovery infrastruktury** > **konfiguracjami**i Otwórz serwer konfiguracji.
2. Na stronie **szczegóły** wybierz serwer vCenter.
3. Kliknij przycisk **Usuń** .

   ![delete-account](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

## <a name="modify-the-vcenter-ip-address-and-port"></a>Modyfikowanie adresu IP i portu programu vCenter

1. Zaloguj się do Portalu Azure.
2. Przejdź do **magazynu Recovery Services** > **Site Recovery infrastruktury** > **serwery konfiguracji**.
3. Kliknij serwer konfiguracji, do którego zostanie przypisany program vCenter.
4. W sekcji **serwery vCenter** kliknij program vCenter, który chcesz zmodyfikować.
5. Na stronie Podsumowanie programu vCenter zaktualizuj adres IP i port vCenter w odpowiednich polach, a następnie Zapisz zmiany.

   ![add_ip_new_vcenter](media/vmware-azure-manage-vcenter/add-ip.png)

6. Aby zmiany zaczęły obowiązywać, poczekaj 15 minut lub [Odśwież serwer konfiguracji](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="migrate-all-protected-virtual-machines-to-a-new-vcenter"></a>Migruj wszystkie chronione maszyny wirtualne do nowego programu vCenter

Aby przeprowadzić migrację wszystkich maszyn wirtualnych do nowego programu vCenter, nie dodawaj kolejnego konta vCenter. Może to prowadzić do duplikowania wpisów. Po prostu zaktualizuj adres IP nowego programu vCenter:

1. Zaloguj się do Portalu Azure.
2. Przejdź do **magazynu Recovery Services** > **Site Recovery infrastruktury** > **serwery konfiguracji**.
3. Kliknij serwer konfiguracji, do którego jest przypisany stary program vCenter.
4. W sekcji **serwery vCenter** kliknij program vCenter, z którego planujesz przeprowadzić migrację.
5. Na stronie Podsumowanie programu vCenter zaktualizuj adres IP nowego serwera vCenter w polu **Nazwa hosta lub adres IP vSphere**. Zapisz zmiany.

Po zaktualizowaniu adresu IP składniki Site Recovery będą rozpoczynać otrzymywanie informacji o odnajdywaniu maszyn wirtualnych z nowego programu vCenter. Nie będzie to miało wpływu na bieżące działania związane z replikacją.

## <a name="migrate-few-protected-virtual-machines-to-a-new-vcenter"></a>Migrowanie kilku chronionych maszyn wirtualnych do nowego programu vCenter

> [!NOTE]
> Ta sekcja ma zastosowanie tylko w przypadku migrowania kilku chronionych maszyn wirtualnych do nowego programu vCenter. Jeśli chcesz chronić nowy zestaw maszyn wirtualnych z nowego programu vCenter, [Dodaj nowe szczegóły programu vCenter do serwera konfiguracji](#add-vmware-server-to-the-vault) i zacznij od **[włączenia ochrony](vmware-azure-tutorial.md#enable-replication)** .

Aby przenieść kilka maszyn wirtualnych do nowego programu vCenter:

1. [Dodaj nowe szczegóły programu vCenter do serwera konfiguracji](#add-vmware-server-to-the-vault).
2. [Wyłącz replikację maszyn wirtualnych](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) , które chcesz zmigrować.
3. Zakończ migrację wybranych maszyn wirtualnych do nowego programu vCenter.
4. Teraz Chroń zmigrowane maszyny wirtualne, [wybierając nowy program vCenter po włączeniu ochrony](vmware-azure-tutorial.md#enable-replication).

> [!TIP]
> Jeśli liczba migrowanych maszyn wirtualnych jest **większa** niż liczba maszyn wirtualnych przechowywanych w starym serwerze vCenter, zaktualizuj adres IP nowego programu vCenter przy użyciu instrukcji podanej w tym miejscu. W przypadku kilku maszyn wirtualnych, które są przechowywane w starym serwerze vCenter, [Wyłącz replikację](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure); [Dodaj nowe szczegóły programu vCenter do serwera konfiguracji](#add-vmware-server-to-the-vault)i uruchom **[opcję Włącz ochronę](vmware-azure-tutorial.md#enable-replication)** .

## <a name="frequently-asked-questions"></a>Często zadawane pytania

1. Jeśli chronione maszyny wirtualne są przenoszone z jednego hosta ESXi do innego, wpłynie to na replikację?

    Nie, nie będzie to miało wpływu na bieżącą replikację. Pamiętaj jednak, [Aby wdrożyć główny serwer docelowy z odpowiednimi uprawnieniami](vmware-azure-reprotect.md#deploy-a-separate-master-target-server)

2. Jakie numery portów są używane do komunikacji między programem vCenter i innymi składnikami Site Recovery?

    Domyślnym portem jest 443. Serwer konfiguracji będzie miał dostęp do informacji o hoście vCenter/vSphere za poorednictwem tego portu. Jeśli chcesz zaktualizować te informacje, kliknij [tutaj](#modify-the-vcenter-ip-address-and-port).
