---
title: Przenoszenie zawartości udziału plików do usługi SharePoint Online za pomocą usługi Azure Data Box Heavy
description: Użyj tego samouczka, aby dowiedzieć się, jak przeprowadzić migrację zawartości udziału plików do usługi Share Point Online przy użyciu narzędzia Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/18/2019
ms.author: alkohli
ms.openlocfilehash: f97ea17551d4415f7ed6371853172cfde30fe4b6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77560052"
---
# <a name="use-the-azure-data-box-heavy-to-migrate-your-file-share-content-to-sharepoint-online"></a>Migracja zawartości udziału plików do usługi SharePoint Online za pomocą narzędzia Azure Data Box Heavy

Użyj narzędzia Azure Data Box Heavy i narzędzia migracji programu SharePoint (SPMT), aby łatwo migrować zawartość udziału plików do usługi SharePoint Online i usługi OneDrive. Korzystając z funkcji Data Box Heavy, można usunąć zależność od łącza sieci rozległej (WAN) w celu przesłania danych.

Microsoft Azure Data Box to usługa, która umożliwia zamawianie urządzenia z witryny Microsoft Azure portal. Następnie można skopiować terabajty danych z serwerów do urządzenia. Po wysłaniu go z powrotem do firmy Microsoft, dane są kopiowane na platformę Azure. W zależności od rozmiaru danych, które zamierzasz przenieść, możesz wybrać spośród:

- [Dysk skrzynki danych](https://docs.microsoft.com/azure/databox/data-box-disk-overview) z pojemnością użytkową 35 TB na zamówienie dla małych i średnich zestawów danych.
- [Data Box](https://docs.microsoft.com/azure/databox/data-box-overview) z 80-TB pojemności użytkowej na urządzenie dla średnich i dużych zestawów danych.
- [Data Box Heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-overview) z pojemnością użytkową 770 TB na urządzenie dla dużych zestawów danych.

W tym artykule opisano, jak za pomocą narzędzia Data Box Heavy do migracji zawartości udziału plików do usługi SharePoint Online.

## <a name="requirements-and-costs"></a>Wymagania i koszty

### <a name="for-data-box-heavy"></a>Usługa Data Box Heavy

- Data Box Heavy jest dostępna tylko w przypadku umów Enterprise Agreement (EA), dostawcy rozwiązań w chmurze (CSP) lub oferty sponsoringu platformy Azure. Jeśli subskrypcja nie wchodzi w skład któregokolwiek z powyższych typów, skontaktuj się z pomocą techniczną firmy Microsoft, aby uaktualnić subskrypcję lub zobacz [Ceny subskrypcji platformy Azure](https://azure.microsoft.com/pricing/).
- Korzystanie z Data Box Heavy jest płatne. Zapoznaj się z [cennikiem Data Box Heavy](https://azure.microsoft.com/pricing/details/databox/heavy/).


### <a name="for-sharepoint-online"></a>Dla usługi SharePoint Online

- Przejrzyj [minimalne wymagania narzędzia migracji programu SharePoint (SPMT).](https://docs.microsoft.com/sharepointmigration/how-to-use-the-sharepoint-migration-tool)

## <a name="workflow-overview"></a>Omówienie przepływu pracy

Ten przepływ pracy wymaga wykonania kroków w usłudze Azure Data Box Heavy oraz w usłudze SharePoint Online.
Poniższe kroki odnoszą się do usługi Azure Data Box Heavy.

1. Zamów azure data box heavy.
2. Odbierz i skonfiguruj urządzenie.
3. Skopiuj dane z lokalnego udziału plików do folderu usługi Azure Files na urządzeniu.
4. Po zakończeniu kopiowania należy wysłać urządzenie z powrotem zgodnie z instrukcjami.
5. Poczekaj, aż dane całkowicie przekażą dane na platformę Azure.

Poniższe kroki dotyczą usługi SharePoint Online.

6. Utwórz maszynę wirtualną w witrynie Azure portal i zainstaluj na niej udział plików platformy Azure.
7. Zainstaluj narzędzie SPMT na maszynie Wirtualnej platformy Azure.
8. Uruchom narzędzie SPMT przy użyciu udziału plików platformy Azure jako *źródła*.
9. Wykonaj ostatnie kroki narzędzia.
10. Zweryfikuj i potwierdź swoje dane.

## <a name="use-data-box-heavy-to-copy-data"></a>Kopiowanie danych za pomocą funkcji Data Box Heavy

Aby skopiować dane do urządzenia Data Box Heavy, należy wykonać następujące czynności.

1. [Zamów swój Data Box Heavy](data-box-heavy-deploy-ordered.md).
2. Po otrzymaniu pliku Data Box Heavy [należy skonfigurować duży zestaw danych](data-box-heavy-deploy-set-up.md). Kabel i skonfigurować oba węzły na urządzeniu.
3. [Kopiowanie danych do usługi Azure Data Box Heavy](data-box-heavy-deploy-copy-data.md). Podczas kopiowania upewnij się, że:

    - Kopiowanie danych należy używać tylko folderu *StorageAccountName_AzFile* w polu danych Gruby. Dzieje się tak, ponieważ chcesz, aby dane kończyły się w udziale plików platformy Azure, a nie w blokowych obiektach blob lub stronicowych obiektach blob.
    - Kopiowanie plików do folderu *StorageAccountName_AzFile.* Podfolder w *folderze StorageAccountName_AzFile* tworzy udział plików. Pliki kopiowane bezpośrednio do *folderu StorageAccountName_AzFile* nie powiodą się i są przekazywane jako blokowe obiekty blob. Jest to udział plików, który zostanie zamontowany na maszynie wirtualnej w następnym kroku.
    - Skopiuj dane do obu węzłów data box heavy.
3. Uruchom [przygotuj się do wysyłki](data-box-heavy-deploy-picked-up.md#prepare-to-ship) na urządzeniu. Pomyślne przygotowanie do wysyłki zapewnia pomyślne przekazywanie plików na platformę Azure.
4. [Zwróć urządzenie](data-box-heavy-deploy-picked-up.md#ship-data-box-heavy-back).
5. [Sprawdź przekazywanie danych na platformę Azure](data-box-heavy-deploy-picked-up.md#verify-data-upload-to-azure).

## <a name="use-spmt-to-migrate-data"></a>Migrowanie danych za pomocą funkcji SPMT

Po otrzymaniu potwierdzenia od zespołu danych platformy Azure, że kopia danych została ukończona, przejdź do migracji danych do usługi SharePoint Online.

Aby uzyskać najlepszą wydajność i łączność, zaleca się utworzenie maszyny wirtualnej platformy Azure (VM).

1. Zaloguj się do witryny Azure portal, a następnie [utwórz maszynę wirtualną](../virtual-machines/windows/quick-create-portal.md).
2. [Zainstaluj udział plików platformy Azure na maszynie Wirtualnej](../storage/files/storage-how-to-use-files-windows.md#mount-the-azure-file-share-with-file-explorer).
3. [Pobierz narzędzie Migracji programu SharePoint](https://spmtreleasescus.blob.core.windows.net/install/default.htm) i zainstaluj je na maszynie wirtualnej platformy Azure.
4. Uruchom narzędzie migracji programu SharePoint. Kliknij **pozycję Zaloguj się** i wprowadź nazwę użytkownika i hasło usługi Office 365.
5. Po wyświetleniu **monitu Gdzie są twoje dane?**, wybierz **opcję Udział plików**. Wprowadź ścieżkę do udziału plików platformy Azure, w którym znajdują się dane.
6. Postępuj zgodnie z pozostałymi monitami w zwykły sposób, łącznie z lokalizacją docelową. Aby uzyskać więcej informacji, zobacz [Jak korzystać z narzędzia migracji programu SharePoint](https://docs.microsoft.com/sharepointmigration/how-to-use-the-sharepoint-migration-tool).

> [!IMPORTANT]
> - Szybkość, z jaką dane są pogoń za pośrednictwem usługi SharePoint Online, zależy od kilku czynników, niezależnie od tego, czy masz dane już na platformie Azure. Zrozumienie tych czynników pomoże Ci zaplanować i zmaksymalizować efektywność migracji.  Aby uzyskać więcej informacji, przejdź do [usługi SharePoint Online i szybkość migracji usługi OneDrive](/sharepointmigration/sharepoint-online-and-onedrive-migration-speed).
> - Istnieje ryzyko utraty istniejących uprawnień do plików podczas migracji danych do usługi SharePoint Online. Możesz również utracić pewne metadane, takie jak *Utworzone przez* i *Data zmodyfikowana przez*.

## <a name="next-steps"></a>Następne kroki

[Zamów swój Box Heavy](./data-box-heavy-deploy-ordered.md)