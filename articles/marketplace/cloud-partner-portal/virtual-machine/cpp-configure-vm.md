---
title: Konfigurowanie maszyny wirtualnej hostowanej przez platformę Microsoft Azure dla portalu Azure Marketplace
description: W tym artykule wyjaśniono, jak rozmiar, aktualizacja i uogólnianie maszyny Wirtualnej hostowane na platformie Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: dsindona
ms.openlocfilehash: f20beced7d977668d12c06375ceb8a2554c6d335
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273430"
---
# <a name="configure-the-azure-hosted-vm"></a>Konfigurowanie maszyny wirtualnej hostowanego na platformie Azure

> [!IMPORTANT]
> Od 13 kwietnia 2020 r. rozpoczniemy przenoszenie zarządzania ofertami maszyny wirtualnej platformy Azure do centrum partnerskiego. Po migracji utworzysz oferty i zarządzasz nimi w Centrum partnerów. Postępuj zgodnie z instrukcjami w [Tworzenie zasobów technicznych maszyny wirtualnej platformy Azure](https://aka.ms/AzureVMTechAsset) do zarządzania zmigrowanych ofert.

W tym artykule wyjaśniono, jak rozmiar, zaktualizować i uogólniać maszyny wirtualnej (VM) hostowane na platformie Azure.  Te kroki są niezbędne do przygotowania maszyny Wirtualnej do wdrożenia w portalu Azure Marketplace.

## <a name="sizing-the-vhds"></a>Zmiana rozmiaru VHD

<!--TD: Check if the following assertion is true. I didn't understand the original content. -->
Jeśli wybrano jedną z maszyn wirtualnych wstępnie skonfigurowaną z systemem operacyjnym (i opcjonalnie dodatkowymi usługami), to już wybrałeś standardowy rozmiar maszyny wirtualnej platformy Azure, zgodnie z opisem na [karcie Jednostki SKU maszyny wirtualnej](./cpp-skus-tab.md).  Rozpoczęcie rozwiązania za pomocą wstępnie skonfigurowanego systemu operacyjnego jest zalecanym rozwiązaniem.  Jeśli jednak system operacyjny jest instalowyny ręcznie, na obrazie maszyny Wirtualnej należy rozmiar podstawowego dysku twardego:

- W przypadku systemu Windows system operacyjny VHD powinien być tworzony jako dysk VHD o stałej masie 127-128 GB. 
- W przypadku systemu Linux ten dysk VHD powinien być tworzony jako dysk VHD o stałym formacie 30-50 GB.

Jeśli rozmiar fizyczny jest mniejszy niż 127-128 GB, dysk VHD powinien być rozrzedzony. Podstawowe obrazy systemu Windows i programu SQL Server dostarczone już spełniają te wymagania, więc nie należy zmieniać formatu ani rozmiaru uzyskanego dysku VHD. 

Dyski danych mogą mieć rozmiar nawet 1 TB. Decydując się na ich rozmiar, należy pamiętać, że klienci nie można zmienić rozmiar VHD w obrazie w momencie wdrożenia. Dyski VHD dysku danych powinny być tworzone jako dyski VHD o stałym formacie. Powinny być również rzadkie. Dyski danych mogą początkowo być puste lub zawierać dane.


## <a name="install-the-most-current-updates"></a>Instalowanie najbardziej aktualnych aktualizacji

Podstawowe obrazy maszyn wirtualnych systemu operacyjnego zawierają najnowsze aktualizacje do daty ich opublikowania. Przed opublikowaniem utworzonego systemu operacyjnego VHD upewnij się, że zaktualizuj system operacyjny i wszystkie zainstalowane usługi za pomocą wszystkich najnowszych poprawek zabezpieczeń i konserwacji.

W systemie Windows Server 2016 uruchom polecenie **Sprawdź aktualizacje.**  W przeciwnym razie w przypadku starszych wersji systemu Windows zobacz [Jak uzyskać aktualizację za pośrednictwem witryny Windows Update](https://support.microsoft.com/help/3067639/how-to-get-an-update-through-windows-update).  Usługa Windows Update automatycznie zainstaluje najnowsze krytyczne i ważne aktualizacje zabezpieczeń.

W przypadku dystrybucji systemu Linux aktualizacje są często pobierane i instalowane za pomocą narzędzia wiersza polecenia lub narzędzia graficznego.  Na przykład Ubuntu Linux udostępnia polecenie [apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) i narzędzie [Menedżera aktualizacji](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html) do aktualizowania systemu operacyjnego.


## <a name="perform-additional-security-checks"></a>Wykonywanie dodatkowych kontroli bezpieczeństwa

Należy zachować wysoki poziom zabezpieczeń dla obrazów rozwiązań w portalu Azure Marketplace.  Poniższy artykuł zawiera listę kontrolną konfiguracji zabezpieczeń i procedur ułatwiających osiągnięcie tego celu: [Zalecenia dotyczące zabezpieczeń dla obrazów portalu Azure Marketplace Images](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images).  Niektóre z tych zaleceń są specyficzne dla obrazów opartych na systemie Linux, ale większość dotyczy dowolnego obrazu maszyny Wirtualnej. 


## <a name="perform-custom-configuration-and-scheduled-tasks"></a>Wykonywanie niestandardowych konfiguracji i zaplanowanych zadań

Jeśli potrzebna jest dodatkowa konfiguracja, zalecanym podejściem jest użycie zaplanowanego zadania, które jest uruchamiane podczas uruchamiania, aby wprowadzić ostateczne zmiany na maszynie Wirtualnej po jej wdrożeniu.  Należy również wziąć pod uwagę następujące zalecenia:
- Jeśli jest to zadanie jednorazowe, zaleca się usunięcie samego zadania po pomyślnym zakończeniu.
- Konfiguracje nie powinny polegać na dyskach innych niż C lub D, ponieważ tylko te dwa dyski, które są zawsze gwarantowane istnieć. Dysk C jest dyskiem systemu operacyjnego, a dysk D jest tymczasowym dyskiem lokalnym.

Aby uzyskać więcej informacji na temat dostosowań systemu Linux, zobacz [Rozszerzenia i funkcje maszyn wirtualnych dla systemu Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux).


## <a name="generalize-the-image"></a>Uogólnianie obrazu

Wszystkie obrazy w portalu Azure Marketplace muszą być wielokrotnegoużyć w sposób ogólny. Aby osiągnąć tę możliwość ponownego użycia, system operacyjny VHD musi być *uogólniony,* operacja, która usuwa wszystkie identyfikatory specyficzne dla wystąpienia i sterowniki oprogramowania z maszyny Wirtualnej.

### <a name="windows"></a>Windows

Dyski systemu operacyjnego Windows są uogólnione za pomocą [narzędzia sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview). Jeśli następnie zaktualizujesz lub ponownie skonfigurujesz system operacyjny, należy ponownie uruchomić sysprep. 

> [!WARNING]
>  Ponieważ aktualizacje mogą działać automatycznie, po uruchomieniu sysprep, należy wyłączyć maszynę wirtualną, dopóki nie zostanie wdrożona.  To zamknięcie pozwoli uniknąć kolejnych aktualizacji od wprowadzania zmian specyficznych dla wystąpienia do systemu operacyjnego VHD lub zainstalowanych usług.

Aby uzyskać więcej informacji na temat uruchamiania sysprep, zobacz [Kroki, aby uogólnić dysk VHD](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep)

### <a name="linux"></a>Linux

Poniższy dwuetapowy proces uogólnia maszynę wirtualną z systemem Linux i ponownie wdroży ją jako oddzielną maszynę wirtualną. Te dwa kroki są tylko podstawowymi elementami tego procesu. Aby uzyskać więcej informacji na temat tych dwóch kroków i dlaczego należy je wykonać, zobacz [Jak utworzyć obraz maszyny wirtualnej lub dysku VHD](../../../virtual-machines/linux/capture-image.md). Na potrzeby tworzenia dysku twardego dla oferty portalu Azure Marketplace można zatrzymać po osiągnięciu sekcji "Tworzenie maszyny wirtualnej z przechwyconego obrazu".

#### <a name="remove-the-azure-linux-agent"></a>Usuwanie agenta systemu Azure Linux
1.  Połącz się z maszyną wirtualną z systemem Linux za pomocą klienta SSH.
2.  W oknie SSH wpisz następujące polecenie: <br/>
    `sudo waagent -deprovision+user`
3.  Wpisz, `y` aby kontynuować. (Parametr można `-force` dodać do poprzedniego polecenia, aby uniknąć tego kroku potwierdzenia).
4.  Po zakończeniu polecenia wpisz, `exit` aby zamknąć klienta SSH.

<!-- TD: I need to add meat and/or references to the following steps -->
#### <a name="capture-the-image"></a>Przechwytywanie obrazu
1.  Przejdź do witryny Azure portal, wybierz grupę zasobów (RG) i oddzielić maszynę wirtualną.
2.  Dysk VHD jest teraz uogólniony i można utworzyć nową maszynę wirtualną za pomocą tego dysku wirtualnego.


## <a name="create-one-or-more-copies"></a>Tworzenie jednej lub więcej kopii

Tworzenie kopii maszyny Wirtualnej jest często przydatne do tworzenia kopii zapasowych, testowania, dostosowywania w trybie fail-over lub równoważenia obciążenia, aby zaoferować różne konfiguracje rozwiązania i tak dalej. Aby uzyskać informacje na temat duplikowania i pobierania podstawowego dysku VHD, aby stworzyć klon niezarządzany, zobacz:

- Maszyna wirtualna z systemem Linux: [pobieranie dysku VHD z systemem Linux z platformy Azure](../../../virtual-machines/linux/download-vhd.md)
- Maszyna wirtualna systemu Windows: [pobieranie dysku VHD systemu Windows z platformy Azure](../../../virtual-machines/windows/download-vhd.md)


## <a name="next-steps"></a>Następne kroki

Po uogólnianiu maszyny Wirtualnej, został cofnięty przydział i utworzono obraz maszyny wirtualnej, można przystąpić do [wdrożenia maszyny wirtualnej z wirtualnego dysku twardego](./cpp-deploy-vm-vhd.md).
