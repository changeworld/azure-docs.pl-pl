---
title: 'Wprowadź dysku D: dysku danych maszyny wirtualnej | Dokumentacja firmy Microsoft'
description: 'Zawiera opis sposobu zmiany litery dysku dla maszyny Wirtualnej z systemem Windows, dzięki czemu możesz użyć dysku D: jako dysku danych.'
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
ms.openlocfilehash: cfd46d5e9750a81d89ed6d3a79bcc9bffdc3d0dd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60844203"
---
# <a name="use-the-d-drive-as-a-data-drive-on-a-windows-vm"></a>Użycie dysku D: jako dysku danych na maszyny Wirtualnej z systemem Windows
Jeśli Twoja aplikacja potrzebuje do skorzystania z dysku D do przechowywania danych, wykonaj te instrukcje, aby użyć innej litery dysku dla dysków tymczasowych. Nigdy nie należy używać dysku tymczasowego do przechowywania danych, który chcesz zachować.

Jeżeli zmienisz rozmiar lub **Stop (Deallocate)** maszyny wirtualnej może spowodować umieszczania maszyny wirtualnej do nowych funkcji hypervisor. Planowanego lub nieplanowanego zdarzenia konserwacji mogą również wyzwalać to umieszczania. W tym scenariuszu dysk tymczasowy zostanie ponownie przypisane pierwszą dostępną literę dysku. Jeśli masz aplikację, która wymaga specjalnie dysku D:, należy wykonać następujące kroki, aby tymczasowo przenieść pagefile.sys, dołączyć nowy dysk danych i przypisać jej literę D i następnie przenieść pagefile.sys z powrotem do dysku tymczasowego. Po wykonaniu tych czynności, Azure nie wycofania D: Jeśli maszyna wirtualna przechodzi do innej funkcji hypervisor.

Aby uzyskać więcej informacji o używaniu platformy Azure na dysk tymczasowy, zobacz [opis dysku tymczasowego na Microsoft Azure Virtual Machines](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="attach-the-data-disk"></a>Dołączanie dysku danych
Najpierw należy dołączyć dysk danych do maszyny wirtualnej. Aby to zrobić za pomocą portalu, zobacz [jak dołączyć dysk danych zarządzanych w witrynie Azure portal](attach-managed-disk-portal.md).

## <a name="temporarily-move-pagefilesys-to-c-drive"></a>Tymczasowo przenieść pagefile.sys na dysku c.
1. Nawiąż połączenie z maszyną wirtualną. 
2. Kliknij prawym przyciskiem myszy **Start** menu, a następnie wybierz **systemu**.
3. W menu po lewej stronie wybierz **Zaawansowane ustawienia systemu**.
4. W **wydajności** zaznacz **ustawienia**.
5. Wybierz **zaawansowane** kartę.
6. W **pamięci wirtualnej** zaznacz **zmiany**.
7. Wybierz **C** dysku, a następnie kliknij przycisk **System zarządzany rozmiar** a następnie kliknij przycisk **ustaw**.
8. Wybierz **D** dysku, a następnie kliknij przycisk **plik stronicowania nie** a następnie kliknij przycisk **ustaw**.
9. Kliknij przycisk Zastosuj. Zostanie wyświetlone ostrzeżenie, czy komputer wymaga ponownego uruchomienia, aby zmiany zostały wprowadzone.
10. Uruchom ponownie maszynę wirtualną.

## <a name="change-the-drive-letters"></a>Zmień litery dysku
1. Po ponownym uruchomieniu maszyny Wirtualnej, zaloguj się ponownie do maszyny Wirtualnej.
2. Kliknij przycisk **Start** menu i wpisz **diskmgmt.msc** i naciśnij klawisz Enter. Rozpocznie się Zarządzanie dyskami.
3. Kliknij prawym przyciskiem myszy **D**, dysku magazynu tymczasowego, a następnie wybierz **Zmień literę dysku i ścieżki**.
4. W obszarze literę dysku, wybierz nowy dysk takich jak **T** a następnie kliknij przycisk **OK**. 
5. Kliknij prawym przyciskiem myszy na dysku danych, a następnie wybierz pozycję **Zmień literę dysku i ścieżki**.
6. W obszarze literę dysku, należy wybrać dysk **D** a następnie kliknij przycisk **OK**. 

## <a name="move-pagefilesys-back-to-the-temporary-storage-drive"></a>Wróć pagefile.sys dysku magazynu tymczasowego
1. Kliknij prawym przyciskiem myszy **Start** menu, a następnie wybierz **systemu**
2. W menu po lewej stronie wybierz **Zaawansowane ustawienia systemu**.
3. W **wydajności** zaznacz **ustawienia**.
4. Wybierz **zaawansowane** kartę.
5. W **pamięci wirtualnej** zaznacz **zmiany**.
6. Wybierz dysk systemu operacyjnego **C** i kliknij przycisk **plik stronicowania nie** a następnie kliknij przycisk **ustaw**.
7. Wybierz dysk magazynu tymczasowego **T** a następnie kliknij przycisk **System zarządzany rozmiar** a następnie kliknij przycisk **ustaw**.
8. Kliknij przycisk **Zastosuj**. Zostanie wyświetlone ostrzeżenie, czy komputer wymaga ponownego uruchomienia, aby zmiany zostały wprowadzone.
9. Uruchom ponownie maszynę wirtualną.

## <a name="next-steps"></a>Kolejne kroki
* Możesz zwiększyć dostępny magazyn do maszyny wirtualnej przez [dołączanie dysku danych dodatkowych](attach-managed-disk-portal.md).

