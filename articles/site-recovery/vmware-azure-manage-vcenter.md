---
title: Zarządzanie serwerami vCenter VMware w usłudze Azure Site Recovery
description: W tym artykule opisano sposób dodawania vCenter VMware vCenter i zarządzania nimi w celu odzyskiwania po awarii maszyn wirtualnych VMware na platformie Azure za pomocą usługi Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/24/2019
ms.author: ramamill
ms.openlocfilehash: 01aef3aca4f6967b1681bff9598c7dd7a24739cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257266"
---
# <a name="manage-vmware-vcenter-server"></a>Zarządzanie serwerem VMware vCenter Server

W tym artykule podsumowano akcje zarządzania na serwerze VMware vCenter Server w [usłudze Azure Site Recovery](site-recovery-overview.md).

## <a name="verify-prerequisites-for-vcenter-server"></a>Weryfikowanie wymagań wstępnych dla serwera vCenter

Wymagania wstępne dla serwerów vCenter i maszyn wirtualnych podczas odzyskiwania po awarii maszyn wirtualnych VMware na platformie Azure są wymienione w [macierzy pomocy technicznej](vmware-physical-azure-support-matrix.md#replicated-machines).

## <a name="set-up-an-account-for-automatic-discovery"></a>Konfigurowanie konta do automatycznego odnajdowania

Po skonfigurowaniu odzyskiwania po awarii dla lokalnych maszyn wirtualnych VMware, usługa Site Recovery wymaga dostępu do hosta vCenter Server/vSphere. Serwer procesów odzyskiwania lokacji może następnie automatycznie wykrywać maszyny wirtualne i w razie potrzeby je przeczować w razie potrzeby. Domyślnie serwer przetwarzania jest uruchamiany na serwerze konfiguracji Odzyskiwania lokacji. Dodaj konto serwera konfiguracji, aby połączyć się z hostem vCenter Server/vSphere w następujący sposób:

1. Zaloguj się do serwera konfiguracji.
1. Otwórz narzędzie serwera konfiguracji _(cspsconfigtool.exe)_ za pomocą skrótu pulpitu.
1. Na karcie **Zarządzanie kontem** kliknij pozycję **Dodaj konto**.

   ![konto dodatkowe](./media/vmware-azure-manage-vcenter/addaccount.png)

1. Podaj szczegóły konta i kliknij przycisk **OK,** aby je dodać. Konto powinno mieć uprawnienia podsumowane w tabeli uprawnień konta.

   > [!NOTE]
   > Synchronizowanie informacji o koncie z odzyskiwaniem witryny zajmuje około 15 minut.

### <a name="account-permissions"></a>Uprawnienia do konta

|**Zadanie** | **Konto** | **Uprawnienia** | **Szczegóły**|
|--- | --- | --- | ---|
|**Odnajdowanie/migracja maszyn wirtualnych (bez powrotu po awarii)** | Co najmniej konto użytkownika tylko do odczytu. | Obiekt centrum danych –> propagacja do obiektu podrzędnego, rola = tylko do odczytu | Użytkownik przypisany na poziomie centrum danych, mający dostęp do wszystkich obiektów w centrum danych.<br/><br/> Aby ograniczyć dostęp, przypisz rolę **Brak dostępu** z **propagate do** obiektu podrzędnego, do obiektów podrzędnych (hosty vSphere, magazyny danych, maszyny wirtualne i sieci).|
|**Replikacja/przewijenie awaryjne** | Co najmniej konto użytkownika tylko do odczytu. | Obiekt centrum danych –> propagacja do obiektu podrzędnego, rola = tylko do odczytu | Użytkownik przypisany na poziomie centrum danych, mający dostęp do wszystkich obiektów w centrum danych.<br/><br/> Aby ograniczyć dostęp, przypisz rolę **Brak dostępu** z **propagate do** obiektu podrzędnego do obiektów podrzędnych (hosty vSphere, magazyny danych, maszyny wirtualne i sieci).<br/><br/> Przydatne do celów migracji, ale nie pełnej replikacji, pracy awaryjnej, powrotu po awarii.|
|**Replikacja/pracy awaryjnej/po awarii** | Sugerujemy utworzenie roli (AzureSiteRecoveryRole) z wymaganymi uprawnieniami, a następnie przypisać rolę do użytkownika lub grupy VMware. | Obiekt Centrum danych – > Propagacji do obiektu podrzędnego, role=AzureSiteRecoveryRole<br/><br/> Magazyn danych -> przydzielanie miejsca, przegląd magazynu danych, operacje na plikach niskiego poziomu, usuwanie pliku, aktualizowanie plików maszyn wirtualnych<br/><br/> Sieć -> przypisywanie sieci<br/><br/> Zasób -> przypisywanie maszyny wirtualnej do puli zasobów, migracja wyłączonej maszyny wirtualnej, migracja włączonej maszyny wirtualnej<br/><br/> Zadania -> tworzenie zadania, aktualizowanie zadania<br/><br/> Maszyna wirtualna -> konfiguracja<br/><br/> Maszyna wirtualna -> interakcja -> odpowiadanie na pytanie, połączenie z urządzeniem, konfigurowanie nośnika CD, konfigurowanie dyskietki, wyłączanie, włączanie, instalowanie narzędzi VMware<br/><br/> Maszyna wirtualna -> spis -> tworzenie, rejestrowanie, wyrejestrowywanie<br/><br/> Maszyna wirtualna -> aprowizowanie -> zezwalanie na pobieranie maszyny wirtualnej, zezwalanie na przekazywanie plików maszyny wirtualnej<br/><br/> Maszyna wirtualna -> migawki -> usuwanie migawek | Użytkownik przypisany na poziomie centrum danych, mający dostęp do wszystkich obiektów w centrum danych.<br/><br/> Aby ograniczyć dostęp, przypisz rolę **Brak dostępu** z **propagate do** obiektu podrzędnego, do obiektów podrzędnych (hosty vSphere, magazyny danych, maszyny wirtualne i sieci).|

## <a name="add-vmware-server-to-the-vault"></a>Dodawanie serwera VMware do przechowalni

Podczas konfigurowania odzyskiwania po awarii dla lokalnych maszyn wirtualnych VMware należy dodać host vCenter Server/vSphere, na którym odnajdujesz maszyny wirtualne, do magazynu odzyskiwania witryny, w następujący sposób:

1. W magazynie > **Konfiguracja infrastruktury** > odzyskiwania**lokacji Severs**, otwórz serwer konfiguracji.
1. Na stronie **Szczegóły** kliknij pozycję **vCenter**.
1. W **obszarze Dodaj vCenter**określ przyjazną nazwę dla hosta vSphere lub serwera vCenter.
1. Określ adres IP lub nazwę FQDN serwera.
1. Pozostaw port ustawiony na 443, chyba że Twoje serwery VMware są skonfigurowane do nasłuchiwania żądań na innym porcie.
1. Wybierz konto używane do łączenia się z serwerem VMware vCenter lub vSphere ESXi. Następnie kliknij przycisk **OK**.

## <a name="modify-credentials"></a>Modyfikowanie poświadczeń

W razie potrzeby można zmodyfikować poświadczenia używane do łączenia się z hostem vCenter Server/vSphere w następujący sposób:

1. Zaloguj się do serwera konfiguracji.
1. Otwórz narzędzie serwera konfiguracji _(cspsconfigtool.exe)_ za pomocą skrótu pulpitu.
1. Kliknij **pozycję Dodaj konto** na karcie Zarządzanie **kontem.**

   ![konto dodatkowe](./media/vmware-azure-manage-vcenter/addaccount.png)

1. Podaj nowe szczegóły konta i kliknij przycisk **OK**. Konto potrzebuje uprawnień wymienionych w tabeli [uprawnień konta.](#account-permissions)
1. W magazynie > **Konfiguracja infrastruktury** > odzyskiwania**lokacji Severs**otwórz serwer konfiguracji.
1. W **2018**r. kliknij pozycję **Odśwież serwer**.
1. Po zakończeniu zadania Odświeżanie serwera wybierz serwer vCenter.
1. W **obszarze Podsumowanie**wybierz nowo dodane konto na **koncie hosta serwera vCenter/vSphere**i kliknij przycisk **Zapisz**.

   ![konto modyfikowania](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>Usuwanie serwera vCenter

1. W magazynie > **Konfiguracja infrastruktury** > odzyskiwania**lokacji Severs**otwórz serwer konfiguracji.
1. Na stronie **Szczegóły** wybierz serwer vCenter.
1. Kliknij przycisk **Usuń.**

   ![usuń konto](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

## <a name="modify-the-ip-address-and-port"></a>Modyfikowanie adresu IP i portu

Można zmodyfikować adres IP serwera vCenter lub porty używane do komunikacji między serwerem a usługą Site Recovery. Domyślnie usługa Site Recovery uzyskuje dostęp do informacji hosta vCenter Server/vSphere za pośrednictwem portu 443.

1. W magazynie > > **serwerów konfiguracji** **infrastruktury odzyskiwania lokacji**kliknij serwer konfiguracji, do którego jest dodawany serwer vCenter Server.
1. Na **serwerach vCenter**kliknij serwer vCenter, który chcesz zmodyfikować.
1. W **podsumowaniu**zaktualizuj adres IP i port oraz zapisz zmiany.

   ![add_ip_new_vcenter](media/vmware-azure-manage-vcenter/add-ip.png)

1. Aby zmiany weszły w życie, odczekaj 15 minut lub [odśwież serwer konfiguracji](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="migrate-all-vms-to-a-new-server"></a>Migrowanie wszystkich maszyn wirtualnych na nowy serwer

Jeśli chcesz przeprowadzić migrację wszystkich maszyn wirtualnych w celu użycia nowego serwera vCenter Server, wystarczy zaktualizować adres IP przypisany do serwera vCenter. Nie dodawaj innego konta VMware, ponieważ może to prowadzić do zduplikowanych wpisów. Zaktualizuj adres w następujący sposób:

1. W magazynie > > **serwerów konfiguracji** **infrastruktury odzyskiwania lokacji**kliknij serwer konfiguracji, do którego jest dodawany serwer vCenter Server.
1. W sekcji **serwery vCenter** kliknij serwer vCenter, z którego chcesz przeprowadzić migrację.
1. W **podsumowaniu**zaktualizuj adres IP do adresu nowego serwera vCenter i zapisz zmiany.
1. Po zaktualizowaniu adresu IP usługa Site Recovery rozpoczyna odbieranie informacji o odnajdowaniu maszyn wirtualnych z nowego serwera vCenter Server. Nie ma to wpływu na bieżące działania replikacji.

## <a name="migrate-a-few-vms-to-a-new-server"></a>Migrowanie kilku maszyn wirtualnych na nowy serwer

Jeśli chcesz przeprowadzić migrację tylko kilku replikujących maszyn wirtualnych na nowy serwer vCenter, wykonaj następujące czynności:

1. [Dodaj](#add-vmware-server-to-the-vault) nowy serwer vCenter do serwera konfiguracji.
1. [Wyłącz replikację](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) maszyn wirtualnych, które zostaną przeniesione na nowy serwer.
1. W VMware migruj maszyny wirtualne do nowego serwera vCenter.
1. Włącz ponownie [replikację](vmware-azure-tutorial.md#enable-replication) zmigrowanych maszyn wirtualnych, wybierając nowy serwer vCenter.

## <a name="migrate-most-vms-to-a-new-server"></a>Migrowanie większości maszyn wirtualnych na nowy serwer

Jeśli liczba maszyn wirtualnych, które mają zostać przeniesione na nowy serwer vCenter, jest wyższa niż liczba maszyn wirtualnych, które pozostaną na oryginalnym serwerze vCenter Server, wykonaj następujące czynności:

1. [Zaktualizuj adres IP](#modify-the-ip-address-and-port) przypisany do serwera vCenter w ustawieniach serwera konfiguracji na adres nowego serwera vCenter Server.
1. [Wyłącz replikację](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) dla kilku maszyn wirtualnych, które pozostają na starym serwerze.
1. [Dodaj stary serwer vCenter i](#add-vmware-server-to-the-vault) jego adres IP do serwera konfiguracji.
1. [Ponownie włącz replikację](vmware-azure-tutorial.md#enable-replication) maszyn wirtualnych, które pozostają na starym serwerze.

## <a name="next-steps"></a>Następne kroki

Jeśli masz jakiekolwiek problemy, zobacz [Rozwiązywanie problemów z błędami odnajdowania serwera vCenter](vmware-azure-troubleshoot-vcenter-discovery-failures.md).
