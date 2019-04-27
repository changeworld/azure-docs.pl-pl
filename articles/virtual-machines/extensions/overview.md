---
title: Rozszerzenia maszyny wirtualnej platformy Azure i funkcje | Dokumentacja firmy Microsoft
description: Dowiedz się, czym są rozszerzenia maszyny Wirtualnej platformy Azure i jak z nich korzystać w usłudze Azure virtual machines
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
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
ms.author: roiyz
ms.openlocfilehash: a35cba0ab7df80596ba1403765980809635c0249
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60618000"
---
# <a name="azure-virtual-machine-extensions-and-features"></a>Rozszerzenia maszyny wirtualnej platformy Azure i funkcji
Rozszerzenia maszyn wirtualnych (VM) to małych aplikacji, które zapewniają po wdrożeniu konfiguracji oraz zadania automatyzacji na maszynach wirtualnych platformy Azure, możesz użyć istniejących obrazów i następnie dostosuj je, jako część wdrożenia, uzyskiwanie działalność niestandardowe Tworzenie obrazu.

Platforma Azure obsługuje wiele rozszerzeń, które należeć do zakresu od konfiguracji maszyny Wirtualnej, monitorowania, zabezpieczeń i aplikacji narzędzie. Wydawcy przełączenia aplikacji, a następnie ująć je w rozszerzeniu i uprościć instalację, dzięki czemu jest wszystko, co należy zrobić, podaj wymagane parametry. 

 Ma duże wybór pierwszy i rozszerzenia innych firm, jeśli aplikacja w repozytorium rozszerzeń nie istnieje, a następnie można użyć rozszerzenia niestandardowego skryptu i konfigurowanie maszyny Wirtualnej przy użyciu własnych skryptów i poleceń.

Przykłady kluczowych scenariuszy, które rozszerzenia są używane do:
* Konfiguracja maszyny Wirtualnej można użyć programu Powershell DSC (Desired State Configuration), Chef, Puppet i rozszerzenia skryptu niestandardowego do instalowania agentów konfiguracji maszyn wirtualnych i konfigurowania maszyny Wirtualnej. 
* Pliki audio i wideo produktów, takich jak firmy Symantec, firmy ESET.
* Narzędzie luk w zabezpieczeniach maszyn wirtualnych, takich jak rozwiązania firmy Qualys Rapid7, HPE.
* Maszyna wirtualna i narzędzi, takich jak DynaTrace, usługi Azure Network Watcher, Site24x7 i rozwiązania Stackify monitorowania aplikacji.

Rozszerzenia dołączone nowe wdrożenie maszyny Wirtualnej. Na przykład można być częścią większej wdrażania, konfigurowania aplikacji na obsługę administracyjną maszyny Wirtualnej, lub uruchamiać wszystkie obsługiwane rozszerzenia obsługiwane systemy po wdrożeniu.

## <a name="how-can-i-find-what-extensions-are-available"></a>Jak znaleźć, jakie rozszerzenia są dostępne?
Dostępnych rozszerzeń można wyświetlić w bloku maszyny Wirtualnej w portalu w obszarze rozszerzenia, jest to po prostu niewielkie, aby uzyskać pełną listę, użyj narzędzi interfejsu wiersza polecenia, zobacz [odnajdywania rozszerzeń maszyn wirtualnych dla systemu Linux](features-linux.md) i [ Odnajdywanie rozszerzeń maszyn wirtualnych dla Windows](features-windows.md).

## <a name="how-can-i-install-an-extension"></a>Jak zainstalować rozszerzenie?
Rozszerzenia maszyn wirtualnych platformy Azure mogą być zarządzane przy użyciu wiersza polecenia platformy Azure, programu Azure PowerShell, szablony usługi Azure Resource Manager i witryny Azure portal. Aby wypróbować rozszerzenia, możesz można przejdź do witryny Azure portal, wybierz opcję rozszerzenia niestandardowego skryptu, a następnie przekazać za pomocą polecenia / skrypt i uruchomić rozszerzenia.

Jeśli chcesz samo rozszerzenie, które zostały dodane w portalu przez szablon interfejsu wiersza polecenia lub usługi Resource Manager znajdują się inne rozszerzenie dokumentacji, takie jak [Windows niestandardowego rozszerzenia skryptu](custom-script-windows.md) i [Linux niestandardowego rozszerzenia skryptu](custom-script-linux.md).

## <a name="how-do-i-manage-extension-application-lifecycle"></a>Jak zarządzać cyklem życia aplikacji rozszerzenia?
Nie trzeba połączyć się bezpośrednio, aby zainstalować lub usunąć rozszerzenia maszyny Wirtualnej. Cykl życia aplikacji rozszerzenie platformy Azure zarządzanego poza maszyny Wirtualnej i zintegrowane z platformą Azure, możesz także uzyskać zintegrowane stan rozszerzenia.

## <a name="anything-else-i-should-be-thinking-about-for-extensions"></a>Czymkolwiek I powinny być zastanawiasz się rozszerzeń?
Rozszerzenia instalowanie aplikacji, np. wszystkie aplikacje, które istnieją pewne wymagania dotyczące rozszerzeń znajduje się lista obsługiwanych Windows i systemów operacyjnych Linux i musisz mieć zainstalowanych agentów maszyny Wirtualnej platformy Azure. Niektóre poszczególnych aplikacji rozszerzenia maszyny Wirtualnej może mieć własne wymagania wstępne środowiska, takich jak dostęp do punktu końcowego.

## <a name="next-steps"></a>Kolejne kroki
* Aby uzyskać więcej informacji na temat działania agenta systemu Linux i rozszerzenia, zobacz [rozszerzeń maszyny Wirtualnej platformy Azure i funkcji dla systemu Linux](features-linux.md).
* Aby uzyskać więcej informacji na temat działania agenta gościa Windows i rozszerzenia zobacz [rozszerzeń maszyny Wirtualnej platformy Azure i funkcji dla Windows](features-windows.md).  
* Aby zainstalować agenta gościa Windows, zobacz [omówienie agenta maszyny wirtualnej Windows Azure](agent-windows.md).  
* Aby zainstalować agenta systemu Linux, zobacz [omówienie agenta maszyny wirtualnej systemu Linux platformy Azure](agent-linux.md).  

