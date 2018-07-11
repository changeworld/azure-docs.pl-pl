---
title: " Zarządzanie programu VMware vCenter Server w usłudze Azure Site Recovery | Dokumentacja firmy Microsoft"
description: W tym artykule opisano jak dodać i zarządzać oprogramowaniem VMware vCenter w usłudze Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.devlang: na
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: ramamill
ms.openlocfilehash: 6f3edf8e5d7a6fda1795991ac0a21cc316c29414
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2018
ms.locfileid: "37950448"
---
# <a name="manage-vmware-vcenter-servers"></a>Zarządzanie serwerami programu VMware vCenter 

W tym artykule omówiono różne operacje odzyskiwania lokacji, które mogą być wykonywane na serwerze VMware vCenter. Sprawdź [wymagania wstępne](vmware-physical-azure-support-matrix.md#replicated-machines) przed rozpoczęciem.


## <a name="set-up-an-account-for-automatic-discovery"></a>Konfigurowanie konta do automatycznego odnajdowania

Usługa Site Recovery wymaga dostępu do zasobów programu VMware, serwer przetwarzania w celu automatycznego wykrywania maszyn wirtualnych i trybu failover i powrotu po awarii maszyn wirtualnych. Utwórz konto dostępu w następujący sposób:

1. Zaloguj się na komputerze serwera konfiguracji.
2. Otwórz uruchomienia cspsconfigtool.exe przy użyciu skrótu na pulpicie.
3. Kliknij przycisk **Dodaj konto** na **Zarządzaj kontem** kartę.

  ![Dodaj konto](./media/vmware-azure-manage-vcenter/addaccount.png)
1. Podaj szczegóły konta, a następnie kliknij przycisk **OK** ją dodać.  Konto powinno mieć uprawnienia podsumowane w poniższej tabeli. 

Trwa około 15 minut, aby uzyskać informacje o koncie, które można synchronizować się z usługą Site Recovery.

### <a name="account-permissions"></a>Uprawnień konta

|**Zadanie podrzędne** | **Konto** | **Uprawnienia** | **Szczegóły**|
|--- | --- | --- | ---|
|**Automatyczne odnajdowanie/migracji (bez powrotu po awarii)** | Potrzebujesz co najmniej użytkownik tylko do odczytu | Obiekt centrum danych –> propagacja do obiektu podrzędnego, rola = tylko do odczytu | Użytkownik przypisany na poziomie centrum danych, mający dostęp do wszystkich obiektów w centrum danych.<br/><br/> Aby ograniczyć dostęp, Przypisz **bez dostępu** roli **Propagacja do obiektu podrzędnego** obiektu do obiektów podrzędnych (hostów vSphere, magazynów danych, maszyny wirtualne i sieci).|
|**Replikacji i pracy awaryjnej** | Potrzebujesz co najmniej użytkownik tylko do odczytu| Obiekt centrum danych –> propagacja do obiektu podrzędnego, rola = tylko do odczytu | Użytkownik przypisany na poziomie centrum danych, mający dostęp do wszystkich obiektów w centrum danych.<br/><br/> Aby ograniczyć dostęp, Przypisz **bez dostępu** roli **Propagacja do obiektu podrzędnego** obiektu do obiektów podrzędnych (hostów vSphere, magazynów danych, maszyny wirtualne i sieci).<br/><br/> Przydatne do celów migracji, ale nie Pełna replikacja, tryb failover i powrotu po awarii.|
|**Replikacja/tryb failover/powrotu po awarii** | Zalecamy, aby utworzyć rolę (AzureSiteRecoveryRole) z wymaganymi uprawnieniami, a następnie przypisać rolę użytkownikowi lub grupie VMware | Obiekt centrum danych -> Propagacja do obiektu podrzędnego, rola = AzureSiteRecoveryRole<br/><br/> Magazyn danych -> przydzielanie miejsca, przegląd magazynu danych, operacje na plikach niskiego poziomu, usuwanie pliku, aktualizowanie plików maszyn wirtualnych<br/><br/> Sieć -> przypisywanie sieci<br/><br/> Zasób -> przypisywanie maszyny wirtualnej do puli zasobów, migracja wyłączonej maszyny wirtualnej, migracja włączonej maszyny wirtualnej<br/><br/> Zadania -> tworzenie zadania, aktualizowanie zadania<br/><br/> Maszyna wirtualna -> konfiguracja<br/><br/> Maszyna wirtualna -> interakcja -> odpowiadanie na pytanie, połączenie z urządzeniem, konfigurowanie nośnika CD, konfigurowanie dyskietki, wyłączanie, włączanie, instalowanie narzędzi VMware<br/><br/> Maszyna wirtualna -> spis -> tworzenie, rejestrowanie, wyrejestrowywanie<br/><br/> Maszyna wirtualna -> aprowizowanie -> zezwalanie na pobieranie maszyny wirtualnej, zezwalanie na przekazywanie plików maszyny wirtualnej<br/><br/> Maszyna wirtualna -> migawki -> usuwanie migawek | Użytkownik przypisany na poziomie centrum danych, mający dostęp do wszystkich obiektów w centrum danych.<br/><br/> Aby ograniczyć dostęp, Przypisz **bez dostępu** roli **Propagacja do obiektu podrzędnego** obiektu do obiektów podrzędnych (hostów vSphere, magazynów danych, maszyny wirtualne i sieci).|


## <a name="add-vmware-server-to-the-vault"></a>Dodawanie serwera VMware w magazynie

1. W witrynie Azure portal Otwórz magazynu > **infrastruktura usługi Site Recovery** > **serwery konfiguracji**, a następnie otwórz na serwerze konfiguracji.
2. Na **szczegóły** kliknij **+ vCenter**.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]

## <a name="modify-credentials"></a>Zmodyfikowania poświadczeń

Zmień poświadczenia używane do łączenia z serwerem vCenter lub hoście ESXi w następujący sposób:

1. Zaloguj się do serwera konfiguracji, a następnie uruchom cspsconfigtool.exe z pulpitu.
2. Kliknij przycisk **Dodaj konto** na **Zarządzaj kontem** kartę.

  ![Dodaj konto](./media/vmware-azure-manage-vcenter/addaccount.png)
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

> [!NOTE]
Jeśli trzeba zmodyfikować vCenter adresu IP, nazwy FQDN lub portu, musisz usunąć serwer vCenter i dodaj go ponownie do portalu.
