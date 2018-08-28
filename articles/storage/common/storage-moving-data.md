---
title: Przenoszenie dużych ilości danych do/z magazynu w chmurze na platformie Azure | Dokumentacja firmy Microsoft
description: Omówienie różnych metod przenoszenie danych do i z usługi Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/26/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: 1df237a65a8b5312b20de19a99399b3a3dd075ff
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43049614"
---
# <a name="moving-data-to-and-from-azure-storage"></a>Przenoszenie danych do i z usługi Azure Storage
Jeśli chcesz przenieść lokalne dane do usługi Azure Storage (lub odwrotnie), istnieją na różne sposoby, aby to zrobić. Podejście, które najlepiej odpowiadający Ci będzie zależeć od danego scenariusza. Ten artykuł będzie zawiera krótkie omówienie różnych scenariuszy i odpowiednie oferty dla każdej z nich.

## <a name="building-applications"></a>Tworzenie aplikacji
Jeśli tworzysz aplikację, programowanie pod kątem interfejsu API REST lub jednej z naszych wiele bibliotek klienckich jest doskonałym sposobem na przenoszenie danych do i z usługi Azure Storage.

Usługa Azure Storage udostępnia rozbudowane biblioteki klienckie dla wielu popularnych języków, w tym .NET, Java, Android, Go, Xamarin, C++, Node.JS, PHP, Ruby, Python i z systemem iOS. Biblioteki klienta oferują zaawansowane możliwości, takie jak logika ponowień, rejestrowanie i przekazywanie równoległe. Możliwe jest również programowanie bezpośrednio przy użyciu interfejsu API REST, który może być wywoływany przez dowolny język programowania mający możliwość wysyłania żądań HTTP lub HTTPS.

Zobacz [Rozpoczynanie pracy z usługą Azure Blob Storage](../blobs/storage-dotnet-how-to-use-blobs.md) Aby dowiedzieć się więcej.

Ponadto oferujemy również [Biblioteka przenoszenia danych usługi Azure Storage](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement) co jest zaprojektowana z myślą o wysokiej wydajności kopiowania danych do i z platformy Azure o bibliotece. Zapoznaj się z naszym Biblioteka przenoszenia danych [dokumentacji](https://github.com/Azure/azure-storage-net-data-movement) Aby dowiedzieć się więcej. 

## <a name="quickly-viewinginteracting-with-your-data"></a>Szybkie wyświetlanie danych i interakcje z danymi
Jeśli chcesz, aby łatwo do wyświetlania danych usługi Azure Storage, a jednocześnie ma również możliwość przekazywania i pobierania danych, rozważ, za pomocą Eksploratora usługi Azure Storage.

Zapoznaj się z naszą listę [Eksploratory usługi Azure Storage](../storage-explorers.md) Aby dowiedzieć się więcej.

## <a name="system-administration"></a>Administracja systemu
Jeśli wymagają lub wolisz tworzyć za pomocą narzędzia wiersza polecenia (np. administratorów), poniżej przedstawiono kilka opcji, należy wziąć pod uwagę:

### <a name="azcopy"></a>Narzędzie AzCopy
AzCopy to narzędzie wiersza polecenia zapewniające o wysokiej wydajności kopiowania danych z usługi Azure Storage. Można także skopiować dane na koncie magazynu lub między różnych kont magazynu. Narzędzie AzCopy jest dostępny na [Windows](storage-use-azcopy.md) i [Linux](storage-use-azcopy-linux.md).

Zobacz [Transfer danych za pomocą wiersza polecenia Azcopy](storage-use-azcopy.md) lub [transferu danych za pomocą narzędzia AzCopy w systemie Linux](storage-use-azcopy-linux.md) Aby dowiedzieć się więcej.

### <a name="azure-powershell"></a>Azure PowerShell
Azure PowerShell to moduł udostępniający polecenia cmdlet służące do zarządzania usługami na platformie Azure. Jest to język skryptów i powłoka wiersza polecenia oparta na zadaniach zaprojektowane pod kątem administrowania systemem.

Zobacz [przy użyciu programu Azure PowerShell z usługą Azure Storage](storage-powershell-guide-full.md) Aby dowiedzieć się więcej.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Wiersza polecenia platformy Azure udostępnia zestaw typu "open source", międzyplatformowych poleceń do pracy z usługami platformy Azure. Wiersza polecenia platformy Azure jest dostępna w Windows, OS x i Linux.

Zobacz [przy użyciu wiersza polecenia platformy Azure z usługą Azure Storage](../storage-azure-cli.md) Aby dowiedzieć się więcej.

## <a name="moving-large-amounts-of-data-with-a-slow-network"></a>Przenoszenie dużych ilości danych przy użyciu wolnej sieci
Jednym z największych wyzwań, które są skojarzone z przenoszenie dużych ilości danych jest czas transferu. Jeśli chcesz pobrać dane z usługi Azure Storage bez konieczności martwienia się o koszty sieci i pisanie kodu, Azure Import/Export jest to odpowiednie rozwiązanie.

Zobacz [Azure Import/Export](../storage-import-export-service.md) Aby dowiedzieć się więcej.

## <a name="backing-up-your-data"></a>Tworzenie kopii zapasowej danych
Jeśli po prostu musisz wykonać kopię zapasową danych do usługi Azure Storage, usługa Azure Backup jest Zdajemy sobie. Jest to zaawansowane rozwiązanie do tworzenia kopii zapasowych danych lokalnych i maszyn wirtualnych platformy Azure.

Zobacz [kopia zapasowa Azure](../../backup/backup-introduction-to-azure-backup.md) Aby dowiedzieć się więcej.

## <a name="accessing-your-data-on-premises-and-from-the-cloud"></a>Uzyskiwanie dostępu do usługi danych w sieci lokalnej i w chmurze
Jeśli potrzebujesz rozwiązania do uzyskiwania dostępu do usługi danych w sieci lokalnej i w chmurze, następnie należy rozważyć za pomocą platformy Azure hybrydowego cloud storage rozwiązania StorSimple. To rozwiązanie składa się z fizycznego urządzenia StorSimple, aby inteligentnie Sklepy często używane dane na dyskach SSD, czasami używane dane na dyski twarde i nieaktywne/backup/używanych danych archiwalnych w usłudze Azure Storage.

Zobacz [StorSimple](../../storsimple/storsimple-overview.md) Aby dowiedzieć się więcej.

## <a name="recovering-your-data"></a>Odzyskiwanie danych
W przypadku obciążeń lokalnych i aplikacji, potrzebujesz rozwiązania, który umożliwia firmie kontynuować działanie w przypadku awarii. Usługa Azure Site Recovery obsługuje replikacji, trybu failover i odzyskiwania maszyn wirtualnych i serwerów fizycznych. Replikowane dane są przechowywane w usłudze Azure Storage, co pozwala wyeliminować konieczność stosowania dodatkowego lokalnego centrum danych.

Zobacz [usługi Azure Site Recovery](../../site-recovery/site-recovery-overview.md) Aby dowiedzieć się więcej.
### <a name="moving-data-faq"></a>Przenoszenie danych — często zadawane pytania:
## <a name="can-i-migrate-vhds-from-one-region-to-another-without-copying"></a>Czy można migrować wirtualnych dysków twardych z jednego regionu do innego bez kopiowania?
Jedynym sposobem, aby skopiować wirtualne dyski twarde w regionie jest kopiowanie danych między kontami magazynu w każdym regionie. Narzędzia AZCopy można użyć w tym. Zobacz transferu danych za pomocą narzędzia wiersza polecenia Azcopy Aby dowiedzieć się więcej. Bardzo duże ilości danych można również Azure Import/Export. Zobacz [Azure Import/Export](https://docs.microsoft.com/azure/storage/storage-import-export-service) Aby dowiedzieć się więcej.
