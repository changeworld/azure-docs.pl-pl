---
title: Samouczek dotyczący weryfikowania przekazywania danych z Azure Data Box Disk na konto magazynu | Microsoft Docs
description: Skorzystaj z tego samouczka, aby dowiedzieć się, jak zweryfikować dane przekazane z Azure Data Box Disk do konta usługi Azure Storage.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.localizationpriority: high
ms.date: 08/22/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: e36f009422307b3b70091775d2288ee710839172
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/23/2019
ms.locfileid: "70014181"
---
::: zone target="docs"

# <a name="tutorial-verify-data-upload-from-azure-data-box-disk"></a>Samouczek: Weryfikuj przekazywanie danych z Azure Data Box Disk

Jest to ostatni samouczek z serii: Wdrażanie urządzenia Azure Data Box Disk. W ramach tego samouczka nauczysz się:

> [!div class="checklist"]
> * Weryfikowanie przekazania danych na platformę Azure
> * Wymazywanie danych z urządzenia Data Box Disk

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że zostały wykonane kroki opisane w artykule [Samouczek: Zwróć Azure Data Box Disk](data-box-disk-deploy-picked-up.md).


## <a name="verify-data-upload-to-azure"></a>Weryfikowanie przekazania danych na platformę Azure

Po odebraniu dysków przez kuriera stan zamówienia w portalu zostanie zmieniony na **Pobrane**. Będzie też wyświetlany identyfikator śledzenia przesyłki.

![Dyski zostały pobrane](media/data-box-disk-deploy-picked-up/data-box-portal-pickedup.png)

Gdy firma Microsoft odbierze i zeskanuje dysk, stan zadania zmieni się na **Odebrane**. 

![Dyski zostały odebrane](media/data-box-disk-deploy-picked-up/data-box-portal-received.png)

Dane zostaną skopiowane automatycznie po podłączeniu dysków do serwera w centrum danych Azure. W zależności od rozmiaru danych operacja kopiowania może potrwać od kilku godzin do kilku dni. Postęp kopiowania możesz monitorować w portalu.

Po zakończeniu kopiowania danych stan zamówienia zmieni się na **Zakończone**.

![Kopiowanie danych zostało zakończone](media/data-box-disk-deploy-picked-up/data-box-portal-completed.png)

Jeśli kopia zakończy się z błędami, zobacz [Rozwiązywanie problemów](data-box-disk-troubleshoot-upload.md)z przekazywaniem.

Sprawdź, czy dane znajdują się na kontach magazynu, zanim usuniesz je ze źródła. Twoje dane mogą znajdować się w:

- Twoje konta usługi Azure Storage. Po skopiowaniu danych na urządzenie Data Box są one zależnie od typu przekazywane do jednej z poniższych ścieżek w ramach konta usługi Azure Storage.

  - W przypadku blokowych obiektów blob i stronicowych obiektów blob: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - W przypadku usługi Azure Files: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Możesz też przejść do swojego konta usługi Azure Storage w witrynie Azure Portal i nawigować z poziomu tej witryny.

- Grupy zasobów dysku zarządzanego. Podczas tworzenia dysków zarządzanych wirtualne dyski twarde są przekazywane jako stronicowe obiekty blob, a następnie konwertowane na dyski zarządzane. Dyski zarządzane są dołączone do grup zasobów określonych w momencie tworzenia zamówienia.

  - Jeśli kopiowanie do dysków zarządzanych na platformie Azure zakończyło się pomyślnie, możesz przejść do **szczegółów zamówienia** w Azure Portal i zanotować grupę zasobów określoną dla dysków zarządzanych.

      ![Wyświetl szczegóły zamówienia](media/data-box-disk-deploy-picked-up/order-details-resource-group.png)

    Przejdź do zanotowanej grupy zasobów i Znajdź dyski zarządzane.

      ![Grupa zasobów dla dysków zarządzanych](media/data-box-disk-deploy-picked-up/resource-group-attached-managed-disk.png)

  - W przypadku skopiowania pliku VHDX lub dynamicznego/różnicowego dysku VHD dysk VHDX/VHD zostanie przekazany do konta magazynu tymczasowego jako blokowy obiekt BLOB. Przejdź do swojego **konta magazynu tymczasowego > obiektów BLOB** , a następnie wybierz odpowiedni kontener — StandardSSD, StandardHDD lub PremiumSSD. Plik VHDX/VHD powinien być wyświetlany jako blokowe obiekty blob na koncie magazynu przemieszczania.

Aby sprawdzić, czy dane zostały przekazane na platformę Azure, wykonaj następujące czynności:

1. Przejdź do konta magazynu skojarzonego z zamówieniem dysku.
2. Przejdź do pozycji **Blob Service > Przeglądaj obiekty blob**. Zostanie wyświetlona lista kontenerów. Na koncie magazynu są tworzone kontenery o nazwach odpowiadających nazwom podfolderów utworzonych przez Ciebie w folderach *BlockBlob* i *PageBlob*.
    Jeśli nazwy folderów są niezgodne z konwencją nazewnictwa platformy Azure, przekazywanie danych na platformę Azure zakończy się niepowodzeniem.

4. Aby upewnić się, że cały zestaw danych został przekazany, użyj Eksploratora usługi Microsoft Azure Storage. Dołącz konto magazynu powiązane z zamówieniem dysków, a następnie sprawdź listę kontenerów obiektów blob. Wybierz kontener, kliknij pozycję **Więcej**, a następnie pozycję **Statystyka folderu**. W okienku **Działania** zostaną wyświetlone statystyki dotyczące tego folderu, w tym liczba i łączny rozmiar obiektów blob. Łączny rozmiar obiektów blob w bajtach powinien być taki sam, jak rozmiar zestawu danych.

    ![Statystyka folderu w Eksploratorze usługi Storage](media/data-box-disk-deploy-picked-up/folder-statistics-storage-explorer.png)

## <a name="erasure-of-data-from-data-box-disk"></a>Wymazywanie danych z urządzenia Data Box Disk

Po zakończeniu kopiowania i sprawdzeniu, czy dane znajdują się na koncie usługi Azure Storage, dyski są bezpiecznie wymazywane zgodnie ze standardem NIST.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono zagadnienia dotyczące urządzenia Azure Data Box Disk, takie jak:

> [!div class="checklist"]
> * Weryfikowanie przekazania danych na platformę Azure
> * Wymazywanie danych z urządzenia Data Box Disk


Przejdź do następnego tematu, aby zapoznać się z instrukcjami zarządzania usługą Data Box Disk w witrynie Azure Portal.

> [!div class="nextstepaction"]
> [Administrowanie usługą Data Box Disk w witrynie Azure Portal](./data-box-portal-ui-admin.md)

::: zone-end

::: zone target="chromeless"

# <a name="verify-data-upload-to-azure"></a>Weryfikowanie przekazania danych na platformę Azure

Po przekazaniu danych na platformę Azure Sprawdź, czy Twoje dane są na kontach magazynu przed usunięciem go ze źródła. Twoje dane mogą znajdować się w:

- Twoje konta usługi Azure Storage. Po skopiowaniu danych na urządzenie Data Box są one zależnie od typu przekazywane do jednej z poniższych ścieżek w ramach konta usługi Azure Storage.

    - **Dla blokowych obiektów blob i stronicowych obiektów BLOB**: https://< storage_account_name >. blob. Core.<containername>Windows. NET//Files/a.txt

    - **W przypadku Azure Files**: https://< storage_account_name >. plik. Core. Windows. NET<sharename>//Files/a.txt

    Możesz też przejść do swojego konta usługi Azure Storage w witrynie Azure Portal i nawigować z poziomu tej witryny.

- Grupy zasobów dysku zarządzanego. Podczas tworzenia dysków zarządzanych wirtualne dyski twarde są przekazywane jako stronicowe obiekty blob, a następnie konwertowane na dyski zarządzane. Dyski zarządzane są dołączone do grup zasobów określonych w momencie tworzenia zamówienia.

::: zone-end


