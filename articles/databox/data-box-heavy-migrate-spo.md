---
title: Użycie Azure Data Box mocno do migrowania zawartości udziału plików do usługi SharePoint Online | Dokumentacja firmy Microsoft
description: Użyj tego samouczka, aby dowiedzieć się, jak migrować zawartość udziału plików do udostępniania punktu Online przy użyciu usługi duże pole danych platformy Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: d74539ec1de8f503b0d0e423adf6273d1422fed5
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592331"
---
# <a name="use-the-azure-data-box-heavy-to-migrate-your-file-share-content-to-sharepoint-online"></a>Użycie duże pole danych platformy Azure do migrowania zawartości udziału plików do usługi SharePoint Online

Użyj swoje pole danych Azure przy dużych i narzędzia do migracji programu SharePoint (SPMT), aby łatwo przeprowadzić migrację zawartości udziału plików do usługi SharePoint Online i OneDrive. Za pomocą duże pole danych, można usunąć zależności w sieci (rozległej WAN) rozległej łącze do transferu danych.

Microsoft Azure Data Box jest to usługa, która umożliwia zamówić urządzenie z portalu Microsoft Azure. Możesz następnie skopiować terabajtów danych z serwerów, na urządzeniu. Po wysłania go do firmy Microsoft, Twoje dane są kopiowane do platformy Azure. W zależności od rozmiaru danych, które zamierzasz przenieść wybrania:

- [Dysku Data Box](https://docs.microsoft.com/azure/databox/data-box-disk-overview) z 35 TB pojemności do wykorzystania na zamówienie zlecenie dla małych i średnich zestawów danych.
- [Urządzenie Data Box](https://docs.microsoft.com/azure/databox/data-box-overview) o 80 TB pojemności do wykorzystania na urządzenie pod kątem nośnik do dużych zestawów danych.
- [Duże pole danych](https://docs.microsoft.com/azure/databox/data-box-heavy-overview) z 770 TB pojemności do wykorzystania na każdym urządzeniu na potrzeby dużych zestawów danych.

W tym artykule szczegółowo opowiada o tym, jak używać duże pole danych do migracji swojej zawartości udziału plików do usługi SharePoint Online.

## <a name="requirements-and-costs"></a>Wymagania i kosztów

### <a name="for-data-box-heavy"></a>Aby uzyskać duże pole danych

- Duże pole danych jest dostępna tylko dla Enterprise Agreement (EA), dostawca rozwiązań w chmurze (CSP), lub dostęp sponsorowany Microsoft Azure oferuje. Jeśli Twoja subskrypcja nie kwalifikuje się do dowolnego z powyższych typów, skontaktuj się z pomocą techniczną firmy Microsoft Support Uaktualnij swoją subskrypcję lub zobacz [ceny subskrypcji platformy Azure](https://azure.microsoft.com/pricing/).
- Brak opłat do użycia duże pole danych. Upewnij się zapoznać się z [duże pole danych ceny](https://azure.microsoft.com/pricing/details/databox/heavy/).


### <a name="for-sharepoint-online"></a>Do usługi SharePoint Online

- Przegląd [minimalne wymagania dla programu SharePoint narzędzia do migracji (SPMT)](https://docs.microsoft.com/sharepointmigration/how-to-use-the-sharepoint-migration-tool).

## <a name="workflow-overview"></a>Omówienie przepływu pracy

Ten przepływ pracy należy wykonać czynności opisane na duże pole danych platformy Azure, a także SharePoint online.
Następujące kroki odnoszą się do Twojej duże pole danych platformy Azure.

1. Duże pole danych na platformie Azure zamówienia.
2. Odbieranie i skonfigurować urządzenie.
3. Kopiowanie danych z pliku lokalnego udostępniać do folderu dla usługi Azure Files na urządzeniu.
4. Po zakończeniu kopiowania odesłanie urządzenia ponownie zgodnie z instrukcjami.
5. Poczekaj, aż dane całkowicie przekazać na platformę Azure.

Następujące kroki odnoszą się do Share Point online.

6. Tworzenie maszyny Wirtualnej w witrynie Azure portal i zainstalować ten udział plików platformy Azure na nim.
7. Zainstaluj narzędzie SPMT na maszynie Wirtualnej platformy Azure.
8. Uruchom narzędzie SPMT przy użyciu udziału plików platformy Azure jako *źródła*.
9. Wykonaj kroki końcowego narzędzia.
10. Sprawdź i potwierdzili swoje dane.

## <a name="use-data-box-heavy-to-copy-data"></a>Użyj duże pole danych do kopiowania danych

Wykonaj następujące kroki, aby skopiować dane do swojej duże pole danych.

1. [Kolejność swoje duże pole danych](data-box-heavy-deploy-ordered.md).
2. Po otrzymaniu swoje dane pole bardzo duże, [Konfigurowanie duże pole danych](data-box-heavy-deploy-set-up.md). Będziesz Podłączanie kabli i konfiguracji obu węzłów na urządzeniu.
3. [Kopiowanie danych do platformy Azure Data Box duże](data-box-heavy-deploy-copy-data.md). Podczas kopiowania, upewnij się, że:

    - Użyj tylko *AzureFile* folderu w danych pole mocno do kopiowania danych. Jest to spowodowane dane znajdą się w udziale plików platformy Azure, nie w przypadku blokowych obiektów blob i stronicowe obiekty BLOB.
    - Skopiuj pliki do folderu, w ramach *AzureFile* folderu. Podfolder w *AzureFile* folder tworzy udział plików. Pliki kopiowana bezpośrednio do *AzureFile* folderu zakończy się niepowodzeniem i są przekazywane jako blokowe obiekty BLOB. Jest to udział plików, który zainstaluje się na maszynie Wirtualnej w następnym kroku.
    - Kopiuj dane do obu węzłach swoje duże pole danych.
3. Uruchom [przygotowywanie do wysłania](data-box-heavy-deploy-picked-up.md#prepare-to-ship) na urządzeniu. Pomyślny przygotowywanie do wysłania zapewnia było pomyślnie przekazać plików na platformie Azure.
4. [Zwrot urządzenia](data-box-heavy-deploy-picked-up.md#ship-data-box-heavy-back).
5. [Przekazywanie danych do platformy Azure Sprawdź](data-box-heavy-deploy-picked-up.md#verify-data-upload-to-azure).

## <a name="use-spmt-to-migrate-data"></a>Użyj SPMT do migracji danych

Po otrzymaniu potwierdzenia od zespołu danych platformy Azure, który Twoja kopia danych została zakończona, można teraz kontynuować migrację danych do usługi SharePoint Online.

Aby uzyskać najlepszą wydajność i łączność firma Microsoft zaleca tworzenia maszyny wirtualnej (maszyny Wirtualnej platformy Azure).

1. Zaloguj się do witryny Azure portal, a następnie [Utwórz maszynę wirtualną](../virtual-machines/windows/quick-create-portal.md).
2. [Instalowanie udziału plików platformy Azure na maszynie Wirtualnej](../storage/files/storage-how-to-use-files-windows.md#mount-the-azure-file-share-with-file-explorer).
3. [Pobierz narzędzie do migracji do programu SharePoint](https://spmtreleasescus.blob.core.windows.net/install/default.htm) i zainstaluj go na maszynie Wirtualnej platformy Azure.
4. Uruchom narzędzie do migracji programu SharePoint. Kliknij przycisk **Zaloguj** i wprowadź nazwę użytkownika usługi Office 365 i hasła.
5. Po wyświetleniu monitu **gdzie są Twoje dane?** , wybierz opcję **udziału plików**. Wprowadź ścieżkę do udziału plików platformy Azure, w którym znajduje się dane.
6. Postępuj zgodnie z pozostałymi instrukcjami jako normalny, łącznie z lokalizacji docelowej. Aby uzyskać więcej informacji, przejdź do [sposób użycia narzędzia migracji SharePoint](https://docs.microsoft.com/sharepointmigration/how-to-use-the-sharepoint-migration-tool).

> [!IMPORTANT]
> - Szybkość jaką dane są pozyskiwane do usługi SharePoint Online ma wpływ kilka czynników, niezależnie od tego, jeśli już masz dane na platformie Azure. Zrozumienie tych czynników może pomóc w planowaniu i zmaksymalizować wydajność migracji.  Aby uzyskać więcej informacji, przejdź do [migracji usługi SharePoint Online i OneDrive szybkość](/sharepointmigration/sharepoint-online-and-onedrive-migration-speed).
> - Istnieje ryzyko utraty istniejących uprawnień do plików, podczas migracji danych do usługi SharePoint Online. Można również utracić niektóre metadane, takie jak *utworzone przez* i *Data modyfikacji przez*.

## <a name="next-steps"></a>Kolejne kroki

[Kolejność swoje duże pole danych](./data-box-heavy-deploy-ordered.md)