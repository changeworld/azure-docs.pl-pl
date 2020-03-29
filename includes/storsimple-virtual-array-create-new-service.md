---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: alkohli
ms.service: storage
ms.topic: include
ms.date: 09/15/2018
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: 4ba5c8b69776b39d8a6640744b0c24600f3a0d5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67183418"
---
#### <a name="to-create-a-new-service"></a>Aby utworzyć nową usługę

1.  Korzystając z poświadczeń konta Microsoft, zaloguj <https://portal.azure.com/>się do witryny Azure portal pod tym adresem URL: . Jeśli wdrażasz urządzenie w portalu rządowym, zaloguj się pod adresem:<https://portal.azure.us/>

2.  W portalu Azure kliknij pozycję **+ Utwórz magazyn** &gt; **magazynu** &gt; **StorSimple Virtual Series**.

    ![Tworzenie nowej usługi](./media/storsimple-virtual-array-create-new-service/createnewservice2.png) 

3.  W otwieranym bloku **Menedżera urządzeń StorSimple** wykonaj następujące czynności:

    1.  Podaj **unikatową nazwę zasobu** dla swojej usługi. Nazwa zasobu jest przyjazną nazwą, która może służyć do identyfikowania usługi. Nazwa może zawierać od 2 do 50 znaków, które mogą być literami, cyframi i łącznikami. Nazwa musi zaczynać i kończyć się literą lub cyfrą.

    2.  Wybierz opcję **Subskrypcja** z listy rozwijanej. Subskrypcja jest połączona z kontem rozliczeniowym. To pole nie jest widoczne, jeśli istnieje tylko jedna subskrypcja.

    3.  W przypadku **grupy zasobów**wybierz istniejącą lub utwórz nową grupę. Aby uzyskać więcej informacji, zobacz [Grupy zasobów na platformie Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/).

    4.  Wypełnij pole **Lokalizacja** dla usługi. Zobacz [regiony platformy Azure, aby](https://azure.microsoft.com/regions/#services) uzyskać więcej informacji o tym, które usługi są dostępne w którym regionie. Ogólnie rzecz biorąc wybierz **lokalizację** najbliższą regionowi geograficznemu, w którym chcesz wdrożyć urządzenie. Możesz także użyć następujących składników:

        -   Jeśli masz istniejące obciążenia na platformie Azure, które również zamierzasz wdrożyć za pomocą urządzenia StorSimple, zaleca się użycie tego centrum danych.

        -   Usługa StorSimple Device Manager i usługa Azure storage mogą znajdować się w dwóch oddzielnych lokalizacjach. W takim przypadku należy utworzyć konta usługi Menedżer urządzeń StorSimple i usługi Azure Storage oddzielnie. Aby utworzyć konto magazynu platformy Azure, przejdź do usługi Azure Storage w witrynie Azure portal i wykonaj kroki opisane w [sekcji Tworzenie konta magazynu.](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) Po utworzeniu konta dodaj je do usługi Menedżer urządzeń StorSimple, wykonując czynności w temacie [Configure a new storage account for the service](https://azure.microsoft.com/documentation/articles/storsimple-deployment-walkthrough/#configure-a-new-storage-account-for-the-service) (Konfigurowanie nowego konta magazynu dla usługi).

        -   W przypadku wdrażania urządzenia wirtualnego w portalu rządowym usługa StorSimple Device Manager jest dostępna w lokalizacjach us Iowa i US Virginia.

    5.  Wybierz **pozycję Utwórz nowe konto magazynu platformy Azure,** aby automatycznie utworzyć konto magazynu w usłudze. Określ **nazwę konta magazynu**. Jeśli potrzebujesz danych w innej lokalizacji, usuń zaznaczenie tego pola.

    6.  Zaznacz pole **Przypnij do pulpitu nawigacyjnego**, jeśli chcesz szybko łączyć się z tą usługą na pulpicie nawigacyjnym.

    7.  Kliknij pozycję **Utwórz**, aby utworzyć usługę Menedżer urządzeń StorSimple.

        ![Tworzenie nowej usługi](./media/storsimple-virtual-array-create-new-service/createnewservice4.png)  

Użytkownik jest kierowany na stronę docelową **Usługi.** Tworzenie usługi potrwa kilka minut. Po pomyślnym utworzeniu usługi użytkownik zostanie odpowiednio powiadomiony i stan usługi zmieni się na **Aktywny**.


