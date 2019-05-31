---
title: Powrót po awarii podczas odzyskiwania po awarii przy użyciu usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie różnych typów powrotu po awarii i zastrzeżenia wziąć pod uwagę podczas przechodzenia wstecz do serwera lokalnego, podczas odzyskiwania po awarii przy użyciu usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 05/19/2019
ms.author: raynew
ms.openlocfilehash: 1e5dc91018df822c72381e4a162c5af5d74ed83c
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399476"
---
# <a name="failback-after-disaster-recovery-of-vmware-vms"></a>Powrót po awarii po odzyskaniu po awarii maszyn wirtualnych programu VMware

Po użytkownik nie powiodło za pośrednictwem platformy Azure jako część procesu odzyskiwania po awarii, może zakończyć się niepowodzeniem, wróć do witryny w środowisku lokalnym. Istnieją dwa różne typy powrotu po awarii, które można wykonać za pomocą usługi Azure Site Recovery: 

- Powrót po awarii do oryginalnej lokalizacji 
- Powrót po awarii do lokalizacji alternatywnej

W przypadku niepowodzenia przełączania maszyny wirtualnej programu VMware mogą powrotu po awarii do tej samej maszyny wirtualnej w środowisku lokalnym źródła Jeśli nadal istnieje. W tym scenariuszu tylko zmiany są replikowane z powrotem. Ten scenariusz jest nazywany **odzyskiwanie do oryginalnej lokalizacji**. Jeśli maszyna wirtualna w środowisku lokalnym nie istnieje, jest scenariusz **odzyskiwanie do lokalizacji alternatywnej**.

> [!NOTE]
> Można tylko powrotu po awarii do oryginalnej vCenter i serwerem konfiguracji. Nie można wdrożyć nowy serwer konfiguracji i zakończyć się niepowodzeniem, ponownie za pomocą. Ponadto nie można dodać nowego vCenter do istniejącego serwera konfiguracji i powrotu po awarii na nowy serwer vCenter.

## <a name="original-location-recovery-olr"></a>Odzyskiwanie do oryginalnej lokalizacji (OLR)
Jeśli chcesz powrócić po awarii do oryginalnej maszyny wirtualnej muszą zostać spełnione następujące warunki:

* Jeśli maszyna wirtualna jest zarządzana przez serwer vCenter, host ESX głównego elementu docelowego powinien mieć dostęp do magazynu danych maszyny wirtualnej.
* Jeśli maszyna wirtualna znajduje się na hoście ESX, ale nie jest zarządzane przez program vCenter, dysk twardy maszyny wirtualnej musi być w magazynie danych, które mogą uzyskiwać dostęp do głównego elementu docelowego hosta.
* Jeśli maszyna wirtualna znajduje się na hoście ESX, a nie używa serwera vCenter, następnie należy najpierw ukończyć odnajdowanie hosta ESX głównego elementu docelowego przed można ponownie włączyć ochronę. Ma to zastosowanie w przypadku zbyt przechodzenia powrotu po awarii fizycznych serwerów.
* Powrót do wirtualnej sieci SAN (vSAN) lub dysk, który oparty na urządzenie niesformatowane mapping (RDM), jeśli dyski już istnieją i są połączone z lokalnej maszyny wirtualnej może zakończyć się niepowodzeniem.

> [!IMPORTANT]
> Należy włączyć disk.enableUUID= wartość TRUE, aby usługa Azure Site Recovery podczas powrotu po awarii, jest w stanie zidentyfikować oryginalny dysk VMDK na maszynie wirtualnej, do której zostanie zapisany oczekujących zmian. Jeśli ta wartość nie jest równa mieć wartość PRAWDA, następnie usługa próbuje zidentyfikować odpowiedni VMDK w środowisku lokalnym na optymalne rozwiązanie. Jeśli odpowiednie VMDK nie zostanie znaleziony, tworzy dodatkowy dysk i napisane się, że dane.

## <a name="alternate-location-recovery-alr"></a>Odzyskiwanie do lokalizacji alternatywnej (ALR)
Jeśli nie ma maszyny wirtualnej w środowisku lokalnym przed ponowne włączanie ochrony maszyny wirtualnej, scenariusz jest nazywany odzyskiwania do lokalizacji alternatywnej. Ponowne włączanie ochrony przepływ pracy umożliwia utworzenie maszyny wirtualnej w środowisku lokalnym ponownie. Spowoduje to również pobierania pełnych danych.

* Po powrocie po awarii do innej lokalizacji, maszyna wirtualna jest odzyskiwana do tego samego hosta ESX, na którym została wdrożona na głównym serwerze docelowym. Magazyn danych, który jest używany do tworzenia dysku będzie tego samego magazynu danych, który został wybrany podczas ponowne włączanie ochrony maszyny wirtualnej.
* Może zakończyć się niepowodzeniem z powrotem tylko w systemie plików maszyny wirtualnej (VMFS) lub magazyn danych sieci vSAN. W przypadku RDM ponowne włączanie ochrony i powrotu po awarii nie będzie działać.
* Ponowne włączanie ochrony obejmuje jedną początkowy transfer dużej ilości danych następuje zmiany. Ten proces istnieje, ponieważ maszyna wirtualna nie istnieje w środowisku lokalnym. Kompletne dane, musi być replikowane z powrotem. To ponowne objęcie ochroną także zajmie więcej czasu niż odzyskiwanie do oryginalnej lokalizacji.
* Do dysków z systemem od RDM nie może zakończyć się niepowodzeniem. W magazynie danych VMFS/sieci vSAN można tworzyć tylko nowe dyski maszyny wirtualnej (Vmdk).

> [!NOTE]
> Fizyczny komputer, gdy przełączone w tryb failover Azure, można utworzyć kopię tylko jako maszynę wirtualną VMware. Wynika to tego samego przepływu pracy jako odzyskiwanie do lokalizacji alternatywnej. Upewnij się, można wykryć co najmniej jeden główny serwer docelowy i niezbędne hostów ESX/ESXi, do których należy wykonać powrotu po awarii.

## <a name="next-steps"></a>Kolejne kroki

Postępuj zgodnie z instrukcjami, aby wykonać [operacji powrotu po awarii](vmware-azure-failback.md).

