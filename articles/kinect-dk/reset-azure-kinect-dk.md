---
title: Resetowanie zestawu Azure Kinect DK
description: Opis sposobu resetowania zestawu Azure Kinect DK do obrazu fabrycznego
author: Teresa-Motiv
ms.author: v-tea
ms.reviewer: jarrettr
ms.prod: kinect-dk
ms.date: 02/11/2020
ms.topic: how-to
keywords: kinect, reset
ms.custom:
- CI 113492
- CSSTroubleshooting
audience: ITPro
manager: dcscontentpm
ms.localizationpriority: high
ms.openlocfilehash: f851579712a9549c88f1ca652aea4f31457da132
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201902"
---
# <a name="reset-azure-kinect-dk"></a>Resetowanie zestawu Azure Kinect DK

Może wystąpić sytuacja, w której musisz zresetować zestaw Azure Kinect DK z powrotem do jego obrazu fabrycznego (na przykład jeśli aktualizacja oprogramowania układowego nie została poprawnie zainstalowana).

1. Wyłącz zasilanie zestawu Azure Kinect DK. Aby to zrobić, odłącz kabel USB i przewód zasilający.
  ![Rysunek, na którym pokazano lokalizację śruby zakrywającej przycisk resetowania.](media/reset-azure-kinect-dk-diagram.png)
1. Aby znaleźć przycisk resetowania, wykręć śrubę znajdującą się w blokadzie mocowania statywu.
1. Podłącz ponownie przewód zasilający.
1. Wsuń do pustego otworu po śrubie w blokadzie mocowania statywu koniec rozgiętego spinacza do papieru.
1. Za pomocą spinacza delikatnie naciśnij i przytrzymaj przycisk resetowania.
1. Przytrzymując naciśnięty przycisk resetowania, ponownie podłącz kabel USB.
1. Po około 3 sekundach światło lampki wskaźnika zasilania zmieni się na bursztynowe. Po zmianie stanu lampki zwolnij przycisk resetowania.  
   
   Po zwolnieniu przycisku resetowania lampka wskaźnika zasilania pulsuje światłem białym i bursztynowym podczas resetowania urządzenia. 
1. Poczekaj, aż światło lampki wskaźnika zasilania zmieni się na ciągłe białe.
1. Włóż na miejsce i dokręć śrubę osłaniającą przycisk resetowania w blokadzie mocowania statywu.
1. Użyj narzędzia Azure Kinect Viewer, aby sprawdzić, czy oprogramowanie układowe zostało zresetowane. W tym celu uruchom narzędzie [Azure Kinect Viewer](azure-kinect-viewer.md), a następnie wybierz pozycję **Device firmware version info** (Informacje o wersji oprogramowania układowego urządzenia), aby zobaczyć wersję oprogramowania układowego zainstalowanego na urządzeniu Azure Kinect DK.

Zawsze należy się upewnić, że na urządzeniu jest zainstalowane najnowsze oprogramowanie układowe. Aby uzyskać najnowszą wersję oprogramowania układowego, użyj narzędzia Azure Kinect Firmware Tool. Aby uzyskać więcej informacji na temat sposobu sprawdzania stanu oprogramowania układowego, zobacz [Sprawdzanie wersji oprogramowania układowego urządzenia](azure-kinect-firmware-tool.md#check-device-firmware-version).

## <a name="related-topics"></a>Powiązane tematy

- [Informacje o zestawie Azure Kinect DK](about-azure-kinect-dk.md)
- [Konfigurowanie zestawu Azure Kinect DK](set-up-azure-kinect-dk.md)
- [Dane techniczne sprzętu zestawu Azure Kinect DK: Środowisko pracy](hardware-specification.md#operating-environment)
- [Narzędzie Azure Kinect Firmware Tool](azure-kinect-firmware-tool.md)
- [Azure Kinect Viewer](azure-kinect-viewer.md)
- [Synchronizacja na wielu urządzeniach Azure Kinect DK](multi-camera-sync.md)
