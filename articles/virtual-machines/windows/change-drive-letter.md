---
title: 'Skonfigurować dysk D: dysku danych maszyny wirtualnej | Dokumentacja firmy Microsoft'
description: 'Zawiera opis sposobu zmiany liter dysków dla maszyny Wirtualnej systemu Windows tak, aby dysków D: jako dysk danych.'
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 0867a931-0055-4e31-8403-9b38a3eeb904
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/02/2018
ms.author: cynthn
ms.openlocfilehash: a4e29ce7ebb20753c3313e1148e1c91e853f49f5
ms.sourcegitcommit: 2e540e6acb953b1294d364f70aee73deaf047441
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/03/2018
ms.locfileid: "27577449"
---
# <a name="use-the-d-drive-as-a-data-drive-on-a-windows-vm"></a>Użyj dysku D: jako dysk z danymi na maszynie Wirtualnej systemu Windows
Jeśli aplikacja wymaga na potrzeby przechowywania danych na dysku D, wykonaj te instrukcje, aby użyć innej litery dysku na dysku tymczasowym. Nigdy nie używaj tymczasowych dysku do przechowywania danych, które należy zachować.

Jeśli po zmianie rozmiaru lub **Stop (Deallocate)** maszyny wirtualnej, może to wywołać umieszczania maszyny wirtualnej do nowej funkcji hypervisor. Planowanego lub nieplanowanego zdarzenia konserwacji może być również przyczyną tego rozmieszczenia. W tym scenariuszu dysku tymczasowym zostanie przydzielona do pierwszą literę dysku. Jeśli masz aplikację, która wymaga dysku D:, w szczególności należy wykonaj następujące kroki, aby tymczasowo przenieść pagefile.sys, dołączyć nowego dysku danych i przypisać jej literę D i następnie przenieść pagefile.sys z powrotem do tymczasowej stacji. Po wykonaniu tych czynności Azure nie wycofania D: Jeśli maszyna wirtualna zostanie przeniesiona do innej funkcji hypervisor.

Aby uzyskać więcej informacji o używaniu dysku tymczasowym Azure, zobacz [opis dysku tymczasowym na maszynach wirtualnych Azure firmy Microsoft](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="attach-the-data-disk"></a>Dołączenie dysku danych
Najpierw należy dołączyć dysku danych do maszyny wirtualnej. Aby to zrobić za pomocą portalu, zobacz [jak dołączyć dysk danych zarządzanych w portalu Azure](attach-managed-disk-portal.md).

## <a name="temporarily-move-pagefilesys-to-c-drive"></a>Tymczasowo przenieść pagefile.sys dysku c.
1. Połączenie z maszyną wirtualną. 
2. Kliknij prawym przyciskiem myszy **Start** menu i wybierz **systemu**.
3. W menu po lewej stronie wybierz **Zaawansowane ustawienia systemu**.
4. W **wydajności** zaznacz **ustawienia**.
5. Wybierz **zaawansowane** kartę.
6. W **pamięci wirtualnej** zaznacz **zmiany**.
7. Wybierz **C** dysk, a następnie kliknij przycisk **Rozmiar kontrolowany przez System** , a następnie kliknij przycisk **ustawić**.
8. Wybierz **D** dysk, a następnie kliknij przycisk **plik stronicowania nie** , a następnie kliknij przycisk **ustawić**.
9. Kliknij przycisk Zastosuj. Zostanie wyświetlone ostrzeżenie, że wymagane zmiany zostały uwzględnione, należy uruchomić ponownie komputera.
10. Uruchom ponownie maszynę wirtualną.

## <a name="change-the-drive-letters"></a>Zmień litery dysku
1. Po ponownym uruchomieniu maszyny Wirtualnej, zaloguj się ponownie do maszyny Wirtualnej.
2. Kliknij przycisk **Start** menu i typ **diskmgmt.msc** i naciśnij Enter. Rozpocznie się Zarządzanie dyskami.
3. Kliknij prawym przyciskiem myszy **D**, magazyn tymczasowy dysku, a następnie wybierz **Zmień literę dysku i ścieżki**.
4. W obszarze literę dysku, wybierz inny dysk takich jak **T** , a następnie kliknij przycisk **OK**. 
5. Kliknij prawym przyciskiem myszy na dysk z danymi, a następnie wybierz **Zmień literę dysku i ścieżki**.
6. W obszarze literę dysku, należy wybrać dysk **D** , a następnie kliknij przycisk **OK**. 

## <a name="move-pagefilesys-back-to-the-temporary-storage-drive"></a>Przenieść pagefile.sys z powrotem do dysku magazyn tymczasowy
1. Kliknij prawym przyciskiem myszy **Start** menu i wybierz **systemu**
2. W menu po lewej stronie wybierz **Zaawansowane ustawienia systemu**.
3. W **wydajności** zaznacz **ustawienia**.
4. Wybierz **zaawansowane** kartę.
5. W **pamięci wirtualnej** zaznacz **zmiany**.
6. Wybierz dysk systemu operacyjnego **C** i kliknij przycisk **plik stronicowania nie** , a następnie kliknij przycisk **ustawić**.
7. Wybierz dysk magazyn tymczasowy **T** , a następnie kliknij przycisk **Rozmiar kontrolowany przez System** , a następnie kliknij przycisk **ustawić**.
8. Kliknij przycisk **Zastosuj**. Zostanie wyświetlone ostrzeżenie, że wymagane zmiany zostały uwzględnione, należy uruchomić ponownie komputera.
9. Uruchom ponownie maszynę wirtualną.

## <a name="next-steps"></a>Kolejne kroki
* Dostępny magazyn może zwiększyć się z maszyną wirtualną przez [dołączenie dysku danych dodatkowych](attach-managed-disk-portal.md).

