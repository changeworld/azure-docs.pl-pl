---
title: Rozwiązywanie problemów z Microsoft Azure stosu | Dokumentacja firmy Microsoft
description: Azure stosu Development Kit (ASDK) informacje dotyczące rozwiązywania problemów.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 6c715f07f75c9196b7cf2cc8659c6e541e1260da
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
ms.locfileid: "30167904"
---
# <a name="microsoft-azure-stack-development-kit-asdk-troubleshooting"></a>Rozwiązywanie problemów z Microsoft Azure stosu Development Kit (ASDK)
Ten dokument zawiera typowe informacje dotyczące rozwiązywania problemów dla ASDK. Jeśli występuje problem, który nie jest udokumentowany, upewnij się, że sprawdzanie [Forum MSDN stosu Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) dalszą pomoc i informacje.  

> [!IMPORTANT]
> Ponieważ ASDK środowiska do oceny, nie jest oficjalną obsługiwane oferowane za pośrednictwem usług obsługi klienta firmy Microsoft (CSS).

Zalecenia dotyczące rozwiązywania problemów, które zostały opisane w tej sekcji są uzyskiwane z wielu źródeł i może lub nie może rozpoznać określonego problemu. Przykłady kodu są dostarczane "w jakim jest" i nie można zagwarantować oczekiwanych rezultatów. W tej sekcji podlega częste zmiany i aktualizacje zaimplementowanego ulepszenia produktu.

## <a name="deployment"></a>Wdrożenie
### <a name="deployment-failure"></a>Niepowodzenie wdrożenia
Jeśli wystąpi błąd podczas instalacji, możesz ponownie uruchomić wdrażania z kroku nie powiodło się przy użyciu opcja ponownego uruchamiania skryptu wdrażania, jak w poniższym przykładzie:

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

### <a name="at-the-end-of-the-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>Po zakończeniu wdrożenia sesji programu PowerShell jest wciąż otwarty i nie wyświetla żadnych danych wyjściowych
To zachowanie jest prawdopodobnie tylko wynik domyślne zachowanie okno poleceń programu PowerShell, gdy została wybrana. Rozwój wdrożenie zestawu zakończyła się pomyślnie, ale skrypt został wstrzymany, wybierając okna. Możesz sprawdzić, czy instalacja została ukończona, wyszukując słowa "Wybierz" pasek tytułu okna wiersza polecenia. Naciśnij klawisz ESC, aby usunąć jej zaznaczenie, a powinien być wyświetlany komunikat o zakończeniu po nim.

## <a name="virtual-machines"></a>Maszyny wirtualne
### <a name="default-image-and-gallery-item"></a>Element domyślny obraz i galerii
Przed wdrożeniem maszyn wirtualnych w stosie Azure należy dodać element obrazu i galerii systemu Windows Server.

### <a name="after-restarting-my-azure-stack-host-some-vms-may-not-automatically-start"></a>Po ponownym uruchomieniu hosta Moje stosu Azure, niektóre maszyny wirtualne nie może automatycznie uruchomić.
Po jego ponownym uruchomieniu hosta, można zauważyć, że usługi Azure stosu nie są natychmiast dostępne. Jest to spowodowane stosu Azure [infrastruktury maszyn wirtualnych](asdk-architecture.md#virtual-machine-roles) i podejmij RPs niektóre czas na sprawdzanie spójności, ale ostatecznie rozpocznie się automatycznie.

Ponadto tej dzierżawy maszyn wirtualnych nie automatycznie uruchomić po ponownym uruchomieniu hosta zestawu programowanie stosu Azure. Jest to znany problem i wymaga kilku ręczne czynności w celu ich Przełącz do trybu online:

1.  Na hoście zestawu programowanie stosu Azure, należy uruchomić **Menedżera klastra trybu Failover** z Start Menu.
2.  Wybierz klaster **S Cluster.azurestack.local**.
3.  Wybierz **ról**.
4.  Maszyny wirtualne dzierżawy są wyświetlane w *zapisane* stanu. Po wszystkich maszyn wirtualnych infrastruktury są uruchomione, kliknij prawym przyciskiem myszy dzierżawione maszyny wirtualne i wybierz **Start** wznowienie maszyny Wirtualnej.

### <a name="i-have-deleted-some-virtual-machines-but-still-see-the-vhd-files-on-disk-is-this-behavior-expected"></a>Usunięto niektóre maszyny wirtualne, ale również widzieć plików VHD na dysk. Jest to zachowanie oczekiwane?
Tak, to zachowanie oczekiwane. Ponieważ została zaprojektowana w ten sposób:

* Po usunięciu maszyny Wirtualnej, wirtualne dyski twarde nie są usuwane. Dyski są oddzielne zasoby w grupie zasobów.
* Usunięcie konta magazynu pobiera usunięcia jest widoczna od razu za pośrednictwem usługi Azure Resource Manager, ale dyski, które może on zawierać nadal są przechowywane w magazynie do momentu uruchamia wyrzucanie elementów bezużytecznych.

Jeśli widzisz "oddzielony" wirtualne dyski twarde, należy znać, jeśli są one częścią folderu dla konta magazynu, który został usunięty. Jeśli konto magazynu nie została usunięta, jest normalne, że są one nadal istnieje.

Więcej o konfigurowaniu przechowywania odzyskiwanie próg i na żądanie w [Zarządzanie kontami magazynu](.\.\azure-stack-manage-storage-accounts.md).

## <a name="storage"></a>Magazyn
### <a name="storage-reclamation"></a>Odzyskiwanie pamięci masowej
Może upłynąć do 14 godzin regeneracji pojemność były wyświetlane w portalu. Odzyskiwanie miejsca zależy od różnych czynników, takich jak procent użycia plików wewnętrzny kontenera w magazynie obiektów blob bloku. W związku z tym w zależności od tego, jak dużo danych zostanie usunięty, nie ma żadnej gwarancji na ilość miejsca, które można odzyskać po uruchomieniu przez moduł Garbage Collector.

## <a name="next-steps"></a>Kolejne kroki
[Odwiedź forum pomocy technicznej platformy Azure stosu](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)

