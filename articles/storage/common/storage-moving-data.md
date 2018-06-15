---
title: Przenoszenie dużych ilości danych do/z magazynu w chmurze na platformie Azure | Dokumentacja firmy Microsoft
description: Przegląd różnych metod przenoszenie danych do i z usługi Azure Storage.
services: storage
documentationcenter: ''
author: JarrettRenshaw
manager: msmets
editor: tysonn
ms.assetid: 5e3947a9-d99b-4108-9d57-3eb67c03e7ba
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2017
ms.author: jarrettr
ms.openlocfilehash: 980e4675c2d2e88716a3133abb027988aecd538f
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2018
ms.locfileid: "28984599"
---
# <a name="moving-data-to-and-from-azure-storage"></a>Przenoszenie danych do i z usługi Azure Storage
Jeśli chcesz przenieść lokalne na dane do magazynu Azure (lub odwrotnie), dostępne są na różne sposoby, w tym celu. Podejście, który jest najbardziej będzie zależeć od danego scenariusza. W tym artykule zapewnia szybki przegląd różnych scenariuszy i odpowiednich ofert dla każdej z nich.

## <a name="building-applications"></a>Tworzenie aplikacji
Jeśli tworzysz aplikację, do interfejsu API REST lub jednym z naszych wiele bibliotek klienta jest to dobry sposób na przenoszenie danych do i z usługi Magazyn Azure.

Usługa Azure Storage udostępnia rozbudowane biblioteki dla platformy .NET, iOS, Java, Android, Windows platformy Uniwersalnej, Xamarin, C++, Node.JS, PHP, Ruby i Python. Biblioteki klienta oferują zaawansowane możliwości, takie jak logika ponowień, rejestrowanie i przekazywanie równoległe. Możliwe jest również programowanie bezpośrednio przy użyciu interfejsu API REST, który może być wywoływany przez dowolny język programowania mający możliwość wysyłania żądań HTTP lub HTTPS.

Zobacz [Rozpoczynanie pracy z magazynem obiektów Blob Azure](../blobs/storage-dotnet-how-to-use-blobs.md) Aby dowiedzieć się więcej.

Ponadto oferujemy również [Biblioteka przenoszenia danych magazynu Azure](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement) czyli biblioteki przeznaczony dla wysokiej wydajności kopiowanie danych do i z platformy Azure. Zapoznaj się z naszym Biblioteka przenoszenia danych [dokumentacji](https://github.com/Azure/azure-storage-net-data-movement) Aby dowiedzieć się więcej. 

## <a name="quickly-viewinginteracting-with-your-data"></a>Szybkie wyświetlanie danych i interakcje z danymi
Jeśli chcesz w prosty sposób wyświetlania danych usługi Azure Storage przy jednoczesnym zachowaniu również możliwość przekazywania i pobierania danych, należy rozważyć przy użyciu Eksploratora usługi Storage platformy Azure.

Zapoznaj się z naszej listy [eksploratory usługi Storage Azure](../storage-explorers.md) Aby dowiedzieć się więcej.

## <a name="system-administration"></a>Administracja systemu
Jeśli wymagają lub potrafisz więcej narzędzia wiersza polecenia (np. administratorów), poniżej przedstawiono kilka opcji, należy wziąć pod uwagę:

### <a name="azcopy"></a>Narzędzie AzCopy
Narzędzie AzCopy to narzędzie wiersza polecenia, przeznaczony dla wysokiej wydajności kopiowanie danych do i z usługi Azure Storage. Można także skopiować dane w ramach konta magazynu lub od różnych kont magazynu. Narzędzie AzCopy jest dostępna w [Windows](storage-use-azcopy.md) i na [Linux](storage-use-azcopy-linux.md).

Zobacz [Transfer danych za pomocą wiersza polecenia Azcopy](storage-use-azcopy.md) lub [Transfer danych za pomocą narzędzia AzCopy w systemie Linux](storage-use-azcopy-linux.md) Aby dowiedzieć się więcej.

### <a name="azure-powershell"></a>Azure PowerShell
Azure PowerShell to moduł udostępniający polecenia cmdlet służące do zarządzania usługami na platformie Azure. Jest to język skryptów i powłoka wiersza polecenia oparta na zadaniach zaprojektowane pod kątem administrowania systemem.

Zobacz [przy użyciu programu Azure PowerShell z usługą Azure Storage](storage-powershell-guide-full.md) Aby dowiedzieć się więcej.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Interfejsu wiersza polecenia platformy Azure oferuje zestaw typu open source, obsługujący wiele platform polecenia do pracy z usługami Azure. Interfejs wiersza polecenia platformy Azure jest dostępna w systemach Windows, OS x i Linux.

Zobacz [przy użyciu wiersza polecenia platformy Azure z usługą Azure Storage](../storage-azure-cli.md) Aby dowiedzieć się więcej.

## <a name="moving-large-amounts-of-data-with-a-slow-network"></a>Przenoszenie dużych ilości danych przy użyciu wolnej sieci
Jest jednym z największych wyzwań skojarzone z przenoszenia dużych ilości danych podczas transferu. Jeśli chcesz pobrać dane z usługi Azure Storage bez obaw o koszty sieci lub pisania kodu, Import/Eksport Azure to odpowiednie rozwiązanie.

Zobacz [Import/Eksport Azure](../storage-import-export-service.md) Aby dowiedzieć się więcej.

## <a name="backing-up-your-data"></a>Tworzenie kopii zapasowej danych
Jeśli po prostu potrzebujesz kopii zapasowej danych do magazynu Azure, Azure Backup jest sposób, aby przejść. To wydajne rozwiązanie tworzenia kopii zapasowych danych lokalnych i maszyn wirtualnych platformy Azure.

Zobacz [kopia zapasowa Azure](../../backup/backup-introduction-to-azure-backup.md) Aby dowiedzieć się więcej.

## <a name="accessing-your-data-on-premises-and-from-the-cloud"></a>Uzyskiwanie dostępu do danych lokalnej i w chmurze
Jeśli potrzebujesz rozwiązania do uzyskiwania dostępu do danych lokalnej i w chmurze, następnie należy rozważyć przy użyciu platformy Azure cloud magazynu hybrydowego, StorSimple. To rozwiązanie składa się z urządzenia fizycznego StorSimple, że inteligentnie magazynów często używane dane na dyskach SSD, czasami używane dane na dyski twarde i nieaktywne/tworzenia kopii zapasowej/archiwizowanie danych w magazynie Azure.

Zobacz [StorSimple](../../storsimple/storsimple-overview.md) Aby dowiedzieć się więcej.

## <a name="recovering-your-data"></a>Odzyskiwanie danych
Gdy masz lokalne obciążeń i aplikacji, konieczne będzie rozwiązanie umożliwiający firmy kontynuować działanie w przypadku awarii. Usługa Azure Site Recovery obsługuje replikacji, trybu failover i odzyskiwania maszyn wirtualnych i serwerów fizycznych. Replikowane dane są przechowywane w usłudze Azure Storage, co eliminuje konieczność dodatkowego centrum danych na miejscu.

Zobacz [usługi Azure Site Recovery](../../site-recovery/site-recovery-overview.md) Aby dowiedzieć się więcej.
### <a name="moving-data-faq"></a>Przenoszenie danych często zadawane pytania:
## <a name="can-i-migrate-vhds-from-one-region-to-another-without-copying"></a>Można przeprowadzić migrację, wirtualne dyski twarde z jednego regionu na inny bez kopiowania?
Jedynym sposobem, aby skopiować wirtualne dyski twarde między region jest kopiowanie danych między kontami magazynu w każdym regionie. Narzędzia AZCopy można użyć w tym. Zobacz transferu danych za pomocą wiersza polecenia Azcopy Aby dowiedzieć się więcej. W przypadku bardzo dużych ilości danych można także Import/Eksport Azure. Zobacz [Import/Eksport Azure](https://docs.microsoft.com/azure/storage/storage-import-export-service) Aby dowiedzieć się więcej.
