---
title: " Zarządzanie programu VMware vCenter Server w usłudze Azure Site Recovery | Dokumentacja firmy Microsoft"
description: W tym artykule opisano sposób dodawania i zarządzania nimi w usłudze Azure Site Recovery program VMware vCenter.
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: be415340da09043eccd361b0168bb304d8904bef
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2018
ms.locfileid: "29852306"
---
# <a name="manage-vmware-vcenter-servers"></a>Zarządzanie programu VMware vCenter Server 

W tym artykule opisano różne operacje odzyskiwania lokacji, które można wykonywać w programie VMware vCenter. Sprawdź [wymagania wstępne](vmware-physical-azure-support-matrix.md#replicated-machines) przed jego uruchomieniem.


## <a name="set-up-an-account-for-automatic-discovery"></a>Konfigurowanie konta automatycznego wykrywania

Odzyskiwanie lokacji musi mieć dostęp do programu VMware, serwera przetwarzania do automatycznego wykrywania maszyn wirtualnych i trybu failover i powrotu po awarii maszyn wirtualnych. Utwórz konto dostępu w następujący sposób:

1. Zaloguj się na komputerze serwera konfiguracji.
2. Otwórz uruchomienie cspsconfigtool.exe za pomocą skrótu na pulpicie.
3. Kliknij przycisk **Dodaj konto** na **Zarządzaj kontem** kartę.

  ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)
1. Podaj szczegóły konta, a następnie kliknij przycisk **OK** ją dodać.  Konto musi mieć uprawnienia podsumowane w poniższej tabeli. 

Trwa około 15 minut, aby uzyskać informacje o koncie, które można synchronizować się z usługą Site Recovery.

### <a name="account-permissions"></a>Uprawnienia dostępu dla konta

|**Zadanie podrzędne** | **Konto** | **Uprawnienia** | **Szczegóły**|
|--- | --- | --- | ---|
|**Automatyczne odnajdowanie/migracji (bez powrotu po awarii)** | Należy co najmniej użytkownika tylko do odczytu | Obiekt centrum danych –> propagacja do obiektu podrzędnego, rola = tylko do odczytu | Użytkownik przypisany na poziomie centrum danych, mający dostęp do wszystkich obiektów w centrum danych.<br/><br/> Aby ograniczyć dostęp, Przypisz **dostępu** roli z **propagowany do podrzędnego** obiektu do obiektów podrzędnych (hostami vSphere, datastores maszyn wirtualnych i sieci).|
|**Replikacji i pracy awaryjnej** | Należy co najmniej użytkownika tylko do odczytu| Obiekt centrum danych –> propagacja do obiektu podrzędnego, rola = tylko do odczytu | Użytkownik przypisany na poziomie centrum danych, mający dostęp do wszystkich obiektów w centrum danych.<br/><br/> Aby ograniczyć dostęp, Przypisz **dostępu** roli z **propagowany do podrzędnego** obiektu do obiektów podrzędnych (hostami vSphere, datastores maszyn wirtualnych i sieci).<br/><br/> Przydatna do celów migracji, ale nie pełnej replikacji, pracy awaryjnej, powrotu po awarii.|
|**Replikacja/pracy awaryjnej/powrotu po awarii** | Zalecamy, aby utworzyć rolę (AzureSiteRecoveryRole) z wymaganymi uprawnieniami, a następnie przypisać rolę użytkownikowi VMware lub grupy | Centrum danych obiektu –> propagowany do obiektu podrzędnego roli = AzureSiteRecoveryRole<br/><br/> Magazyn danych -> przydzielanie miejsca, przegląd magazynu danych, operacje na plikach niskiego poziomu, usuwanie pliku, aktualizowanie plików maszyn wirtualnych<br/><br/> Sieć -> przypisywanie sieci<br/><br/> Zasób -> przypisywanie maszyny wirtualnej do puli zasobów, migracja wyłączonej maszyny wirtualnej, migracja włączonej maszyny wirtualnej<br/><br/> Zadania -> tworzenie zadania, aktualizowanie zadania<br/><br/> Maszyna wirtualna -> konfiguracja<br/><br/> Maszyna wirtualna -> interakcja -> odpowiadanie na pytanie, połączenie z urządzeniem, konfigurowanie nośnika CD, konfigurowanie dyskietki, wyłączanie, włączanie, instalowanie narzędzi VMware<br/><br/> Maszyna wirtualna -> spis -> tworzenie, rejestrowanie, wyrejestrowywanie<br/><br/> Maszyna wirtualna -> aprowizowanie -> zezwalanie na pobieranie maszyny wirtualnej, zezwalanie na przekazywanie plików maszyny wirtualnej<br/><br/> Maszyna wirtualna -> migawki -> usuwanie migawek | Użytkownik przypisany na poziomie centrum danych, mający dostęp do wszystkich obiektów w centrum danych.<br/><br/> Aby ograniczyć dostęp, Przypisz **dostępu** roli z **propagowany do podrzędnego** obiektu do obiektów podrzędnych (hostami vSphere, datastores maszyn wirtualnych i sieci).|


## <a name="add-vmware-server-to-the-vault"></a>Dodawanie serwera VMware do magazynu

1. W portalu Azure, otwórz magazynu > **infrastruktura usługi Site Recovery** > **serwery konfiguracji**i otwórz serwera konfiguracji.
2. Na **szczegóły** kliknij przycisk **+ vCenter**.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]

## <a name="modify-credentials"></a>Zmodyfikowania poświadczeń

Zmień poświadczenia użyte do nawiązania połączenia z serwerem vCenter lub hosta ESXi w następujący sposób:

1. Zaloguj się do konfiguracji serwera, a następnie uruchom cspsconfigtool.exe z pulpitu.
2. Kliknij przycisk **Dodaj konto** na **Zarządzaj kontem** kartę.

  ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)
3. Podaj nowe szczegóły konta, a następnie kliknij przycisk **OK** ją dodać. Konto musi mieć uprawnienia wymienione [powyżej](#account-permissions).
4. W portalu Azure, Otwórz magazyn > **infrastruktura usługi Site Recovery** > **serwery konfiguracji**i otwórz serwera konfiguracji.
5. W **szczegóły** kliknij przycisk **odświeżania serwera**.
6. Po zakończeniu zadania odświeżania serwera wybierz programu vCenter Server, aby otworzyć vCenter **Podsumowanie** strony.
7. Wybierz konto nowo dodanego w **konta hosta serwera/vSphere vCenter** , a następnie kliknij przycisk **zapisać**.

    ![modify-account](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>Usuń serwer vCenter

1. W portalu Azure Otwórz magazynu > **infrastruktura usługi Site Recovery** > **serwery konfiguracji**i otwórz serwera konfiguracji.
2. Na **szczegóły** wybierz serwer vCenter.
3. Polecenie **usunąć** przycisku.

  ![delete-account](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

> [!NOTE]
Jeśli musisz zmienić adres IP vCenter, nazwa FQDN lub portu, należy usunąć serwera vCenter i dodaj go do portalu.
