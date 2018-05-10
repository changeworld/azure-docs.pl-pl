---
title: Rozszerzenia maszyny wirtualnej platformy Azure i funkcje | Dokumentacja firmy Microsoft
description: Dowiedz się, jakie anre rozszerzenia maszyny Wirtualnej Azure jak używać ich z maszynami wirtualnymi Azure
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: danis
ms.openlocfilehash: 01178995dbf9203082a6250ef256522bc1101e57
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="azure-virtual-machine-extensions-and-features"></a>Rozszerzenia maszyny wirtualnej platformy Azure i funkcje
Rozszerzenia maszyny wirtualnej platformy Azure (VM) są małe aplikacji, które mają po wdrożeniu i automatyzację zadań na maszynach wirtualnych Azure, możesz użyć istniejących obrazów i następnie dostosuj je w ramach wdrożeń, przekroczono biznesowe niestandardowe Tworzenie obrazu.

Platforma Azure obsługuje wiele rozszerzeń, które należeć do zakresu od konfiguracji maszyny Wirtualnej, monitorowania, zabezpieczeń i aplikacji narzędzie. Wydawców przełączenia aplikacji, następnie należy ująć w rozszerzenie i uprościć instalacji, więc wszystko, co należy zrobić to zapewnić obowiązkowe parametry. 

 Jest duże wybór pierwszej i rozszerzeń innych firm, jeśli aplikacja w repozytorium rozszerzeń nie istnieje, a następnie można użyć rozszerzenia niestandardowego skryptu i konfigurowanie maszyny Wirtualnej o własne skrypty i polecenia.

Przykłady kluczowych scenariuszy, które rozszerzenia są używane do:
* Konfiguracja maszyny Wirtualnej, można użyć DSC środowiska Powershell (konfiguracji żądanego stanu), Chef, Puppet i niestandardowego rozszerzenia skryptu do instalowania agentów konfiguracji maszyny Wirtualnej i konfigurowanie maszyny Wirtualnej. 
* Produkty antywirusowe, takich jak firmy Symantec, RESETUJ.
* Narzędzie luki w zabezpieczeniach maszyny Wirtualnej, takie jak Qualys Rapid7, HPE.
* Maszyna wirtualna i narzędzi, takich jak DynaTrace, obserwatora sieciowego Azure Site24x7 i Stackify monitorowania aplikacji.

Rozszerzenia dołączone nowe wdrożenie maszyny Wirtualnej. Przykładowo można być częścią większej wdrażania, konfigurowania aplikacji na obsługę administracyjną maszyn wirtualnych, lub uruchomić dla dowolnego rozszerzenia obsługiwanych obsługiwane systemy po wdrożeniu.

## <a name="how-can-i-find-what-extensions-are-available"></a>Jak znaleźć, jakie rozszerzenia są dostępne?
Można wyświetlić dostępne rozszerzenia w bloku maszyny Wirtualnej w portalu, w obszarze rozszerzeń, to po prostu niewielkie, aby uzyskać pełną listę, można użyć narzędzi interfejsu wiersza polecenia, zobacz [wykrywania rozszerzeń maszyny Wirtualnej dla systemu Linux](features-linux.md) i [ Odnajdywanie rozszerzeń maszyny Wirtualnej w systemie Windows](features-windows.md).

## <a name="how-can-i-install-an-extension"></a>Jak można zainstalować rozszerzenia?
Rozszerzenia maszyny Wirtualnej platformy Azure mogą być zarządzane przy użyciu 2.0 interfejsu wiersza polecenia Azure, programu Azure PowerShell, szablony usługi Azure Resource Manager i portalu Azure. Aby wypróbować rozszerzenie, należy można przejdź do portalu Azure wybierz niestandardowe rozszerzenie skryptu, następnie przekazać w poleceniu / skryptu i uruchomić rozszerzenia.

Jeśli chcesz samo rozszerzenie zostanie dodany do portalu za pomocą interfejsu wiersza polecenia lub Menedżera zasobów szablonu, zobacz dokumentację inne rozszerzenie, takich jak [niestandardowe rozszerzenie skryptu Windows](custom-script-windows.md) i [Linux niestandardowe rozszerzenie skryptu](custom-script-linux.md).

## <a name="how-do-i-manage-extension-application-lifecycle"></a>Sposób zarządzania cyklem życia aplikacji rozszerzenie?
Nie należy do nawiązania połączenia z maszyną Wirtualną bezpośrednio do instalacji i usuwania rozszerzenia. Cykl życia aplikacji rozszerzenie Azure zarządzanego poza maszyny Wirtualnej i zintegrowane z platformą Azure, również uzyskać zintegrowanego stanu rozszerzenia.

## <a name="anything-else-i-should-be-thinking-about-for-extensions"></a>Cokolwiek innego I powinien pomyśleć o dla rozszerzeń?
Instalowanie rozszerzenia aplikacji, takich jak wszystkie aplikacje są niektóre wymagania dla rozszerzeń znajduje się lista obsługiwanych systemu Windows i systemów operacyjnych Linux i muszą zostać zainstalowani agenci maszyny Wirtualnej platformy Azure. Niektóre poszczególne aplikacje rozszerzenia maszyny Wirtualnej może mieć własne wymagania wstępne środowiska, takich jak dostęp do punktu końcowego.

## <a name="next-steps"></a>Kolejne kroki
* Aby uzyskać więcej informacji na temat działania agenta systemu Linux i rozszerzeń, zobacz [rozszerzenia maszyny Wirtualnej platformy Azure i funkcje w systemie Linux](features-linux.md).
* Aby uzyskać więcej informacji na temat działania agenta gościa z systemem Windows i rozszerzeń, zobacz [rozszerzenia maszyny Wirtualnej platformy Azure i funkcje w systemie Linux](features-windows.md).  
* Aby zainstalować agenta gościa z systemem Windows, temacie [omówienie agenta maszyny wirtualnej systemu Windows Azure ](agent-windows.md).  
* Aby zainstalować agenta systemu Linux, zobacz [omówienie agenta maszyny wirtualnej systemu Linux Azure ](agent-linux.md).  

