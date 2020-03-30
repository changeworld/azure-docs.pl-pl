---
title: Powrót po awarii podczas odzyskiwania po awarii za pomocą usługi Azure Site Recovery | Dokumenty firmy Microsoft
description: Ten artykuł zawiera omówienie różnych typów powrotu po awarii i zastrzeżeń, które należy wziąć pod uwagę podczas niepowodzenia w środowisku lokalnym podczas odzyskiwania po awarii za pomocą usługi Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: raynew
ms.openlocfilehash: c0eaf28f9aeb4050fd35a6036a53e3e91d00f3eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281836"
---
# <a name="failback-of-vmware-vms-after-disaster-recovery-to-azure"></a>Powrót po awarii maszyn wirtualnych VMware po odzyskiwaniu po awarii na platformę Azure

Po przejściu po awarii na platformie Azure w ramach procesu odzyskiwania po awarii można wrócić po awarii do lokacji lokalnej. Istnieją dwa różne typy powrotu po awarii, które są możliwe w usłudze Azure Site Recovery: 

- Powrót po awarii do pierwotnej lokalizacji 
- Powrót po awarii do lokalizacji alternatywnej

Jeśli komputer wirtualny VMware uległ awarii, można przywrócić po awarii do tej samej lokalnej maszyny wirtualnej źródła, jeśli nadal istnieje. W tym scenariuszu tylko zmiany są replikowane z powrotem. Ten scenariusz jest znany jako **oryginalne odzyskiwanie lokalizacji**. Jeśli lokalna maszyna wirtualna nie istnieje, scenariusz jest **odzyskiwaniem lokalizacji alternatywnej**.

> [!NOTE]
> Powrót do oryginalnego serwera vCenter i Configuration można przywrócić tylko do wersji 10. Nie można wdrożyć nowego serwera konfiguracji i przywrócić go po awarii przy użyciu. Ponadto nie można dodać nowego centrum wirtualnego do istniejącego serwera konfiguracji i powrotu po awarii do nowego vCenter.

## <a name="original-location-recovery-olr"></a>Odzyskiwanie oryginalnej lokalizacji (OLR)
Jeśli zdecydujesz się wrócić po awarii do oryginalnej maszyny wirtualnej, muszą być spełnione następujące warunki:

* Jeśli maszyna wirtualna jest zarządzana przez serwer vCenter, host ESX głównego obiektu docelowego powinien mieć dostęp do magazynu danych maszyny wirtualnej.
* Jeśli maszyna wirtualna znajduje się na hoście ESX, ale nie jest zarządzana przez vCenter, dysk twardy maszyny wirtualnej musi znajdować się w magazynie danych, do który może uzyskać dostęp host głównego obiektu docelowego.
* Jeśli maszyna wirtualna znajduje się na hoście ESX i nie używa vCenter, należy ukończyć odnajdowanie hosta ESX głównego obiektu docelowego przed ponownej ochrony. Dotyczy to również serwerów fizycznych, które nie mają zastosowania.
* Można przywrócić po awarii do sieci wirtualnego obszaru magazynowania (vSAN) lub dysku opartego na mapowaniu nieprzetworzonego urządzenia (RDM), jeśli dyski już istnieją i są połączone z lokalną maszyną wirtualną.

> [!IMPORTANT]
> Ważne jest, aby włączyć disk.enableUUID= TRUE, tak aby podczas powrotu po awarii usługa Azure Site Recovery jest w stanie zidentyfikować oryginalny VMDK na maszynie wirtualnej, na której będą zapisywane oczekujące zmiany. Jeśli ta wartość nie jest ustawiona na WARTOŚĆ TRUE, usługa próbuje zidentyfikować odpowiednie lokalne VMDK na podstawie najlepszych starań. Jeśli nie zostanie znaleziony prawy zestaw VMDK, utworzy dodatkowy dysk, a dane zostanie na nim zapisane.

## <a name="alternate-location-recovery-alr"></a>Odzyskiwanie lokalizacji alternatywnej (ALR)
Jeśli lokalna maszyna wirtualna nie istnieje przed ponowneprzecięciem maszyny wirtualnej, scenariusz jest nazywany odzyskiwaniem lokalizacji alternatywnej. Przepływ pracy ponownego przetwolania lokalnej maszyny wirtualnej ponownie. Spowoduje to również pełne pobranie danych.

* Po powrocie do lokalizacji alternatywnej maszyna wirtualna jest odzyskiwana do tego samego hosta ESX, na którym wdrożono główny serwer docelowy. Magazyn danych, który jest używany do tworzenia dysku będzie ten sam magazyn danych, który został wybrany podczas ponownego przeceniania maszyny wirtualnej.
* Powrót po awarii można przywrócić tylko do systemu plików maszyny wirtualnej (VMFS) lub magazynu danych vSAN. Jeśli masz RDM, reprotect i powrót po awarii nie będzie działać.
* Reprotect obejmuje jeden duży początkowy transfer danych, po którym następuje zmiany. Ten proces istnieje, ponieważ maszyna wirtualna nie istnieje lokalnie. Pełne dane muszą być replikowane z powrotem. Ta reprotect zajmie również więcej czasu niż odzyskiwanie oryginalnej lokalizacji.
* Nie można przywrócić dysku opartego na serwerze RDM po awarii. Tylko nowe dyski maszyn wirtualnych (VMDKs) można tworzyć w magazynie danych VMFS/vSAN.

> [!NOTE]
> Komputer fizyczny, po przełączeniu awaryjnym na platformę Azure, można odzyskać po awarii tylko jako maszyny wirtualnej VMware. Wynika to z tego samego przepływu pracy co odzyskiwanie lokalizacji alternatywnej. Upewnij się, że odnajdujesz co najmniej jeden główny serwer docelowy i niezbędne hosty ESX/ESXi, do których musisz wrócić po awarii.

## <a name="next-steps"></a>Następne kroki

Wykonaj kroki, aby wykonać [operację powrotu po awarii](vmware-azure-failback.md).

