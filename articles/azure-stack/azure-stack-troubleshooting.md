---
title: Rozwiązywanie problemów z usługi Microsoft Azure Stack | Dokumentacja firmy Microsoft
description: Usługa Azure Stack rozwiązywania.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: a20bea32-3705-45e8-9168-f198cfac51af
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.openlocfilehash: b6ec3283121a3403afb80ccad81f313decf16c88
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52957644"
---
# <a name="microsoft-azure-stack-troubleshooting"></a>Rozwiązywanie problemów z usługi Microsoft Azure Stack

Ten dokument zawiera wspólne informacje dotyczące rozwiązywania problemów dla usługi Azure Stack. 

> [!NOTE]
> Ponieważ usługi Azure Stack Technical Preview Development Kit (ASDK) jest oferowany jako środowisko oceny, nie istnieje żadne oficjalne pomocy technicznej z usług obsługi klienta firmy Microsoft. Jeśli występuje problem, upewnij się sprawdzić [Forum MSDN usługi Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) dalszą pomoc i informacje.  

Zalecenia dotyczące rozwiązywania problemów, które są opisane w tej sekcji są uzyskiwane z wielu źródeł i może lub nie może rozpoznać określonego problemu. Przykłady kodu są dostarczane w postaci jest i nie można zagwarantować oczekiwanych wyników. W tej sekcji podlega częste zmiany i aktualizacje zaimplementowanego ulepszenia produktu.

## <a name="deployment"></a>Wdrożenie
### <a name="deployment-failure"></a>Wdrożenie zakończyło się niepowodzeniem
Jeśli nastąpi awaria podczas instalacji należy ponownie uruchomić wdrożenie z krok zakończony niepowodzeniem przy użyciu opcję ponownego uruchamiania skryptu wdrożenia.  

### <a name="at-the-end-of-asdk-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>Po zakończeniu wdrożenia ASDK sesji programu PowerShell jest wciąż otwarty, a nie wyświetla żadnych danych wyjściowych.
To zachowanie jest prawdopodobnie tylko wynik domyślne zachowanie okno poleceń programu PowerShell, jeśli został wybrany. Deployment kit rozwoju zakończyła się pomyślnie, ale skrypt został wstrzymany, wybierając okno. Możesz sprawdzić, czy instalacja została ukończona, wyszukując słowo "Wybierz" pasek tytułu okna wiersza polecenia.  Naciśnij klawisz ESC, aby usunąć jej zaznaczenie, a powinien być wyświetlany komunikat o zakończeniu po nim.

## <a name="virtual-machines"></a>Maszyny wirtualne
### <a name="default-image-and-gallery-item"></a>Element domyślny obraz i galerii
Element obrazu i galerii systemu Windows Server, należy dodać przed wdrożeniem maszyn wirtualnych w usłudze Azure Stack.

### <a name="after-restarting-my-azure-stack-host-some-vms-may-not-automatically-start"></a>Po ponownym uruchomieniu mojej hosta usługi Azure Stack, niektóre maszyny wirtualne nie może automatycznie uruchomić.
Po ponownym uruchomieniu hosta, może być Zauważ, że nie są natychmiast dostępne usługi Azure Stack.  Jest to spowodowane Azure Stack [maszynach wirtualnych infrastruktury](../azure-stack/asdk/asdk-architecture.md#virtual-machine-roles) i dostawców zasobów zająć trochę czasu, aby sprawdzić spójność, ale ostatecznie rozpocznie się automatycznie.

Ponadto tej dzierżawy maszyn wirtualnych nie automatycznie uruchomiony po ponownym uruchomieniu hosta usługi Azure Stack development zestawu. Jest to znany problem i wymaga tylko kilku ręcznych kroków, aby przełączyć w tryb online:

1.  Na hoście usługi Azure Stack development kit start **Menedżera klastra trybu Failover** w Start Menu.
2.  Wybierz klaster **S Cluster.azurestack.local**.
3.  Wybierz **role**.
4.  Maszyny wirtualne dzierżawy są wyświetlane w *zapisane* stanu. Gdy są uruchomione wszystkie maszyny wirtualne infrastruktury, kliknij prawym przyciskiem myszy maszyny wirtualne dzierżawcy i wybierz **Start** Aby wznowić działanie maszyny Wirtualnej.

### <a name="i-have-deleted-some-virtual-machines-but-still-see-the-vhd-files-on-disk-is-this-behavior-expected"></a>Usunięto niektóre maszyny wirtualne, ale nadal Zobacz pliki wirtualnego dysku twardego na dysku. To zachowanie jest oczekiwane?
Tak, jest to oczekiwane zachowanie. Ponieważ została zaprojektowana w ten sposób:

* Jeśli usuniesz Maszynę wirtualną, wirtualne dyski twarde nie są usuwane. Dyski są oddzielne zasoby w grupie zasobów.
* Gdy konto magazynu zostaje usunięte, usunięcie jest widoczna od razu za pośrednictwem usługi Azure Resource Manager, ale dyski które może ona zawierać nadal są przechowywane w magazynie, dopóki działa wyrzucanie elementów bezużytecznych.

Jeśli widzisz "oddzielony" wirtualne dyski twarde, należy znać, jeśli są one częścią folderu dla konta magazynu, który został usunięty. Jeśli nie można usunąć konta magazynu, jest normalnym, że są one nadal istnieje.

Możesz dowiedzieć się więcej o konfigurowaniu przechowywania odzyskiwanie próg i na żądanie w [Zarządzanie kontami magazynu](azure-stack-manage-storage-accounts.md).

## <a name="storage"></a>Magazyn
### <a name="storage-reclamation"></a>Odzyskiwanie pamięci masowej
Może upłynąć do 14 godzin odzyskiwanego pojemności wyświetlani w portalu. Odzyskiwanie miejsca zależy od różnych czynników, w tym procent użycia plików pojemnika wewnętrznego w magazyn blokowych obiektów blob. W związku z tym w zależności od ilości danych zostanie usunięty, nie ma żadnej gwarancji ilości miejsca, które można odzyskać po uruchomieniu modułu zbierającego elementy bezużyteczne.

