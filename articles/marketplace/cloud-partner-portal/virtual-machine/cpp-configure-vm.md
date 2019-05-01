---
title: Konfigurowanie maszyn wirtualnych hostowanych na platformie Azure firmy Microsoft dla witryny Azure Marketplace
description: Wyjaśnia, jak rozmiar, aktualizowanie i uogólnianie maszyny Wirtualnej hostowanej na platformie Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: pabutler
ms.openlocfilehash: 3d38efadfb8ad13d072056ef851be99d9540ad4b
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64938428"
---
# <a name="configure-the-azure-hosted-vm"></a>Konfigurowanie maszyny Wirtualnej hostowanej na platformie Azure

W tym artykule wyjaśniono, jak rozmiar, aktualizowanie i uogólnianie maszyny wirtualnej (VM), hostowanej na platformie Azure.  Te kroki są niezbędne w celu przygotowania jej do wdrożenia w portalu Azure Marketplace.


## <a name="sizing-the-vhds"></a>Zmiany rozmiaru wirtualnych dysków twardych

<!--TD: Check if the following assertion is true. I didn't understand the original content. -->
Jeśli wybrano maszyn wirtualnych, wstępnie skonfigurowane przy użyciu systemu operacyjnego (i opcjonalnie dodatkowych usług), a następnie zostały już pobrane standardowy rozmiar maszyny Wirtualnej platformy Azure, zgodnie z opisem w [jednostki SKU maszyny wirtualnej karcie](./cpp-skus-tab.md).  Uruchamianie rozwiązania z wstępnie skonfigurowanym systemem operacyjnym jest zalecanym podejściem.  Jednak w przypadku ręcznego instalowania systemu operacyjnego następnie musi rozmiaru głównej wirtualnego dysku twardego w obrazie maszyny Wirtualnej:

- Dla Windows, system operacyjny dysku VHD powinien zostać utworzony jako dysk o rozmiarze 127 128 GB wirtualnego dysku twardego w stałym formacie. 
- W przypadku systemu Linux ten wirtualny dysk twardy powinien zostać utworzony jako dysk o rozmiarze 30 – 50 GB wirtualnego dysku twardego w stałym formacie.

Jeśli rozmiar fizyczny jest mniejszy niż 127 128 GB, wirtualny dysk twardy powinien być rozrzedzony. Podstawowe obrazy Windows i program SQL Server, podano już spełniają te wymagania, więc nie zmieniaj format lub rozmiar wirtualnego dysku twardego. 

Dyski danych może być większy niż 1 TB. Podczas ustawiania ich rozmiar, należy pamiętać, że klienci nie mogą zmieniać rozmiarów wirtualnych dysków twardych w ramach obrazu po ich wdrożeniu. Twarde z danymi powinny zostać utworzone jako dyski VHD w stałym formacie. Również powinien być rozrzedzony. Dyski z danymi początkowo może być pusty ani zawierać dane.


## <a name="install-the-most-current-updates"></a>Zainstaluj najnowsze aktualizacje

Podstawowe obrazy systemu operacyjnego maszyn wirtualnych zawierają najnowsze aktualizacje wydane do dnia opublikowania. Przed opublikowaniem utworzonego wirtualnego dysku twardego systemu operacyjnego, upewnij się, aktualizacji systemu operacyjnego i wszystkich zainstalowanych usług za pomocą wszystkie najnowsze poprawki zabezpieczeń i konserwacji.

Windows Server 2016, można uruchomić **sprawdzać dostępność aktualizacji** polecenia.  W przeciwnym razie dla starszych wersji systemu Windows, zobacz [sposób uzyskiwania aktualizacji za pośrednictwem usługi Windows Update](https://support.microsoft.com/help/3067639/how-to-get-an-update-through-windows-update).  Windows update będzie automatycznie instalować aktualizacje najnowsze zabezpieczenia krytyczne i ważne.

Dystrybucje systemu Linux aktualizacje są często pobierane i instalowane za pomocą narzędzia wiersza polecenia lub graficznego narzędzia.  Na przykład, Ubuntu Linux udostępnia [polecenia apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) polecenia i [Menedżera aktualizacji](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html) narzędzia do aktualizowania systemu operacyjnego.


## <a name="perform-additional-security-checks"></a>Wykonaj dodatkowe kontrole zabezpieczeń

Powinno to Ty masz wysoki poziom zabezpieczeń dla obrazów rozwiązania w portalu Azure Marketplace.  Następujący artykuł zawiera listę kontrolną konfiguracji zabezpieczeń i procedur, aby pomóc w przypadku tego celu: [Zalecenia dotyczące zabezpieczeń dla obrazów z witryny Azure Marketplace](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images).  Niektóre z tych zaleceń są specyficzne dla obrazów opartych na systemie Linux, ale najbardziej zastosować do dowolnego obrazu maszyny Wirtualnej. 


## <a name="perform-custom-configuration-and-scheduled-tasks"></a>Wykonywanie niestandardowej konfiguracji i zaplanowanych zadań

Jeśli wymagane jest dodatkowe czynności konfiguracyjne, zalecanym podejściem jest używać zaplanowane zadanie, które jest uruchamiane podczas uruchamiania, aby wprowadzić zmiany końcowy do maszyny Wirtualnej, po jej wdrożeniu.  Ponadto należy wziąć pod uwagę następujące zalecenia:
- Jeśli jest to zadanie uruchamiane jednokrotnie, zaleca się, że zadanie usunięte po pomyślnym ukończeniu.
- Konfiguracje nie należy polegać na dyskach innych niż C i D, ponieważ tylko te dwa dyski, które są zawsze musi istnieć. Dysk C jest dysk systemu operacyjnego i dysk D to tymczasowy dysk lokalny.

Aby uzyskać więcej informacji na temat dostosowywania systemu Linux, zobacz [rozszerzenia maszyn wirtualnych i funkcji dla systemu Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux).


## <a name="generalize-the-image"></a>Uogólnianie obrazu

Wszystkie obrazy w portalu Azure Marketplace muszą być wielokrotnego użytku w ogólny sposób. Aby osiągnąć ten możliwość ponownego wykorzystania, system operacyjny dysku VHD musi być *uogólniony*, operacja, która usuwa wszystkie wystąpienia określonych identyfikatorów i sterowników oprogramowania z maszyny Wirtualnej.

### <a name="windows"></a>Windows

Dyski systemu operacyjnego Windows są uogólniony za pomocą [narzędzia sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview). Jeśli później zaktualizować lub zmienić konfigurację systemu operacyjnego, należy ponownie uruchomić program sysprep. 

> [!WARNING]
>  Ponieważ aktualizacje może być uruchamiane automatycznie, po uruchomieniu programu sysprep, należy wyłączyć maszynę Wirtualną, dopóki nie jest ona wdrożona.  Zamknięcie zapobiegnie kolejnych aktualizacji, możliwość dokonywania zmian danego wystąpienia wirtualnego dysku twardego systemu operacyjnego lub zainstalowane usługi.

Aby uzyskać więcej informacji na temat uruchamiania polecenia sysprep, zobacz [kroki, aby uogólnić wirtualnego dysku twardego](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#steps-to-generalize-a-vhd)

### <a name="linux"></a>Linux

Następujący dwuetapowy proces uogólniania maszyny Wirtualnej z systemem Linux i ponownie wdrożyć go jako oddzielne maszyny Wirtualnej.  Aby uzyskać więcej informacji, zobacz [Jak utworzyć obraz maszyny wirtualnej lub wirtualnego dysku twardego](../../../virtual-machines/linux/capture-image.md). 

#### <a name="remove-the-azure-linux-agent"></a>Usuń agenta systemu Linux platformy Azure
1.  Łączenie z maszyną wirtualną z systemem Linux przy użyciu klienta SSH.
2.  W oknie SSH wpisz następujące polecenie: <br/>
    `sudo waagent -deprovision+user`
3.  Typ `y` aby kontynuować. (Można dodać `-force` do poprzedniego polecenia uniknąć tego kroku potwierdzenia.)
4.  Po wykonaniu polecenia, wpisz `exit` zamknąć klienta SSH.

<!-- TD: I need to add meat and/or references to the following steps -->
#### <a name="capture-the-image"></a>Przechwycenie obrazu
1.  Przejdź do witryny Azure portal, wybierz grupę zasobów (RG) i cofnąć przydziału maszyny Wirtualnej.
2.  Wirtualnego dysku twardego jest teraz uogólniony, a następnie można utworzyć nową maszynę Wirtualną przy użyciu tego wirtualnego dysku twardego.


## <a name="create-one-or-more-copies"></a>Utwórz jedną lub więcej kopii

Tworzenie kopii maszyny Wirtualnej jest często przydatne w przypadku tworzenia kopii zapasowej, testowania, dostosowane pracy awaryjnej lub równoważenia obciążenia, oferują różne konfiguracje rozwiązania i tak dalej. Aby uzyskać informacji na temat zduplikowane i Pobierz podstawowy dysk VHD zapewnienie niezarządzanym klonowania zobacz:

- Maszyny Wirtualnej systemu Linux: [Pobieranie wirtualnego dysku twardego systemu Linux na platformie Azure](../../../virtual-machines/linux/download-vhd.md)
- Windows maszyny Wirtualnej: [Pobierz Windows wirtualnego dysku twardego z platformy Azure](../../../virtual-machines/windows/download-vhd.md)


## <a name="next-steps"></a>Kolejne kroki

Po skonfigurowaniu maszyny Wirtualnej można przystąpić do [wdrożyć maszynę wirtualną z wirtualnego dysku twardego](./cpp-deploy-vm-vhd.md).
