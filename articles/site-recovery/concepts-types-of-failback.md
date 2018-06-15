---
title: Powrót po awarii w usłudze Azure Site Recovery | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera omówienie różnych typów powrotu po awarii i ostrzeżenia wziąć pod uwagę podczas niepowodzenie wstecz do lokalnego z usługą Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: guaravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: rajanki
ms.openlocfilehash: 372a7867b47960338d7a1bf7e646fb9fffbe72e1
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/08/2018
ms.locfileid: "29803556"
---
# <a name="overview-of-failback"></a>Omówienie powrotu po awarii

Po za pośrednictwem powiodły się do platformy Azure, może zakończyć się niepowodzeniem, powrót do lokacji lokalnej. Istnieją dwa różne typy powrotu po awarii, które są możliwe za pomocą usługi Azure Site Recovery: 

- Powrót po awarii do oryginalnej lokalizacji 
- Powrót po awarii do lokalizacji alternatywnej

Jeśli nie powiodło się na maszynie wirtualnej VMware, mogą nie do tej samej maszyny wirtualnej lokalnego źródła Jeśli nadal istnieje. W tym scenariuszu tylko zmiany są replikowane ponownie. Ten scenariusz jest nazywany **odzyskiwanie do oryginalnej lokalizacji**. Jeśli na lokalnej maszynie wirtualnej istnieje, jest scenariusz **odzyskiwanie do lokalizacji alternatywnej**.

> [!NOTE]
> Można tylko powrotu po awarii do oryginalnego vCenter i serwer konfiguracji. Nie można wdrożyć serwer konfiguracji i zakończyć się niepowodzeniem przy jego użyciu. Ponadto nie można dodać nowego vCenter do istniejącego serwera konfiguracji i powrotu po awarii do nowego vCenter.

## <a name="original-location-recovery-olr"></a>Odzyskiwanie do oryginalnej lokalizacji (OLR)
Jeśli zdecydujesz się na powrót po awarii do oryginalnego maszyny wirtualnej, muszą zostać spełnione następujące warunki:

* Jeśli maszyna wirtualna jest zarządzana przez serwer vCenter, hostów ESX główny cel powinien mieć dostęp do magazynu danych maszyny wirtualnej.
* Jeśli maszyna wirtualna znajduje się na hoście ESX, ale nie jest zarządzana przez vCenter, dysku twardego maszyny wirtualnej musi być w magazynu danych, dostępnym dla głównego celu hosta.
* Jeśli Twoja maszyna wirtualna znajduje się na hoście ESX i nie używa vCenter, następnie należy wykonać odnajdywanie hostów ESX głównego elementu docelowego przed możesz Włącz ponownie ochronę. Dotyczy to powrót fizycznych serwerów powrotu po awarii zbyt.
* Powrót do wirtualnej sieci SAN (vSAN) lub dysk, który oparty na urządzenia niesformatowanego mapowania (RDM), jeśli dyski już istnieją i są połączone z na lokalnej maszynie wirtualnej może zakończyć się niepowodzeniem.

> [!IMPORTANT]
> Należy włączyć disk.enableUUID= wartość TRUE, dzięki czemu podczas powrotu po awarii, usługa Azure Site Recovery jest w stanie zidentyfikować oryginalnego VMDK na maszynie wirtualnej, na którym zostanie zapisany oczekujące zmiany. Jeśli ta wartość nie jest równa mieć wartość PRAWDA, usługa próbuje zidentyfikować odpowiedniego VMDK lokalnymi w konkretnej sytuacji. Jeśli prawo VMDK nie zostaną znalezione, tworzy dodatkowy dysk i dane pobiera na który.

## <a name="alternate-location-recovery-alr"></a>Odzyskiwanie do lokalizacji alternatywnej (ALR)
Jeśli nie istnieje na lokalnej maszynie wirtualnej przed ponownej ochrony maszyny wirtualnej, scenariusz jest nazywany odzyskiwania do lokalizacji alternatywnej. Przepływ pracy ponownej ochrony ponownie tworzy na lokalnej maszynie wirtualnej. Spowoduje to również pobierania danych.

* Gdy użytkownik powrót po awarii do alternatywnej lokalizacji, maszyna wirtualna jest odzyskiwana do tego samego hosta ESX, na którym wdrożono główny serwer docelowy. Magazyn danych, który jest używany do tworzenia dysku będzie tego samego magazynu danych, które zostały wybrane podczas ponownej ochrony maszyny wirtualnej.
* Może się nie powieść kopii tylko do systemu plików maszyny wirtualnej (VMFS) lub sieci vSAN magazynu danych. Jeśli masz RDM ponownej ochrony i powrotu po awarii nie będzie działać.
* Ponownej ochrony obejmuje jedną początkowy transfer dużej ilości danych następuje zmiany. Ten proces nie istnieje, ponieważ maszyna wirtualna nie istnieje lokalnie. Pełnych danych ma być replikowane ponownie. Ta ponownej ochrony również zajmie więcej czasu niż odzyskiwanie do oryginalnej lokalizacji.
* Do dysków na podstawie RDM nie może zakończyć się niepowodzeniem. Na VMFS/vSAN magazyn danych można tworzyć tylko nowe dyski maszyny wirtualnej (VMDKs).

> [!NOTE]
> Można symulować komputera fizycznego, gdy Failover na platformie Azure, Utwórz kopię tylko jako maszyny wirtualnej VMware. Wynika z tego samego przepływu pracy jako odzyskiwanie do lokalizacji alternatywnej. Upewnij się, można odnaleźć co najmniej jeden główny serwer docelowy i niezbędne hostów ESX/ESXi, do których należy wykonaj powrót po awarii.

## <a name="next-steps"></a>Kolejne kroki

Wykonaj kroki do wykonania [operacja powrotu po awarii](vmware-azure-failback.md).

