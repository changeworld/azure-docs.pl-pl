---
title: Konfigurowanie i używanie emulatora magazynu z programem Visual Studio | Dokumentacja firmy Microsoft
description: Konfigurowanie i używanie emulatora magazynu z programem Visual Studio
services: visual-studio-online
author: ghogen
manager: douge
assetId: c8e7996f-6027-4762-806e-614b93131867
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 8/17/2017
ms.author: ghogen
ms.openlocfilehash: c502d5e0869d35ded5c3ba7e790da0558d219e0e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="configuring-and-using-the-storage-emulator-with-visual-studio"></a>Konfigurowanie i używanie emulatora magazynu z programem Visual Studio
[!INCLUDE [storage-try-azure-tools](../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Przegląd
Środowisko projektowe zestawu Azure SDK zawiera emulator magazynu, narzędzia, która symuluje obiektów Blob, kolejki i tabeli magazynu usługi dostępnej na platformie Azure na komputerze deweloperskim lokalnego. W przypadku tworzenia usługi w chmurze, która jest stosowana do usług Azure storage lub zapisywanie zewnętrznej aplikacji, która wywołuje usług magazynu, należy przetestować Twój kod lokalnie emulatora magazynu. Azure Tools dla programu Microsoft Visual Studio zintegrować Zarządzanie emulator magazynu programu Visual Studio. Narzędzia Azure Zainicjuj bazę danych emulatora magazynu przy pierwszym użyciu, uruchamia usługę emulatora magazynu, podczas uruchamiania lub debugowania kodu w programie Visual Studio i umożliwia dostęp tylko do odczytu do danych emulatora magazynu za pomocą Eksploratora magazynu Azure.

Aby uzyskać szczegółowe informacje w emulatorze magazynu, w tym wymagania systemowe i instrukcje dotyczące konfigurowania niestandardowych, zobacz [użyć emulatora magazynu Azure do programowania i testowania](storage/common/storage-use-emulator.md).

> [!NOTE]
> Istnieją pewne różnice w działaniu między Symulacja emulatora magazynu i usług Azure storage. Zobacz [różnice między emulatora magazynu i usług magazynu Azure](storage/common/storage-use-emulator.md) w dokumentacji zestawu SDK usługi Azure poznać konkretne różnice.
> 
> 

## <a name="configuring-a-connection-string-for-the-storage-emulator"></a>Konfigurowanie parametrów połączenia dla emulatora magazynu
Dostęp do emulatora magazynu z kodu w ramach roli, należy skonfigurować parametry połączenia wskazującego emulator magazynu, a później można to zmienić wskaż konto magazynu platformy Azure. Ciąg połączenia jest ustawienie konfiguracji, który może być odczytany roli użytkownika w czasie wykonywania, aby nawiązać połączenie z konta magazynu. Aby uzyskać więcej informacji o sposobie tworzenia parametrów połączenia, zobacz [Konfigurowanie aplikacji Azure](https://msdn.microsoft.com/library/azure/2da5d6ce-f74d-45a9-bf6b-b3a60c5ef74e#BK_SettingsPage).

> [!NOTE]
> Odwołanie do konta emulatora magazynu w kodzie można zwrócić przy użyciu **DevelopmentStorageAccount** właściwości. Ta metoda działa prawidłowo, jeśli chcesz uzyskać dostęp emulator magazynu w kodzie, ale jeśli planujesz opublikować aplikację na platformie Azure, musisz utworzyć parametry połączenia dostępu do konta magazynu Azure i zmodyfikuj kod, aby używał tego połączenia ciąg przed opublikowaniem. Aby przełączyć między konto emulatora magazynu i konto magazynu platformy Azure często, ciąg połączenia upraszcza ten proces.
> 
> 

## <a name="initializing-and-running-the-storage-emulator"></a>Inicjowanie i uruchomiony emulator magazynu
Można określić, że podczas uruchamiania lub debugowania w programie Visual Studio usługi Visual Studio automatycznie uruchamia emulatora magazynu. W Eksploratorze rozwiązań Otwórz menu skrótów dla Twojego **Azure** projekt i wybierz pozycję **właściwości**. Na **programowanie** karcie **uruchomić emulatora magazynu Azure** wybierz **True** (Jeśli nie jest już ustawiona na tę wartość).

Podczas pierwszego uruchamiania lub debugowania usługi z programu Visual Studio emulator magazynu uruchamia proces inicjowania. Ten proces rezerwuje portów lokalnych dla emulatora magazynu i tworzy bazy danych emulatora magazynu. Po zakończeniu tego procesu nie musiał ponownie uruchomić, chyba że bazy danych emulator magazynu jest usuwany.

> [!NOTE]
> Począwszy od wersji czerwca 2012 narzędzi Azure, emulator magazynu działa domyślnie w SQL Express LocalDB. We wcześniejszych wersjach narzędzi Azure, emulator magazynu jest uruchamiana dla domyślnego wystąpienia programu SQL Express 2005 lub 2008, które należy zainstalować przed można zainstalować zestaw Azure SDK. Można również uruchomić emulatora magazynu nazwanego wystąpienia programu SQL Express lub nazwanym lub domyślnego wystąpienia programu Microsoft SQL Server. Jeśli konieczne będzie skonfigurowanie emulator magazynu, aby uruchomić dla wystąpienia innych niż domyślne wystąpienie, zobacz [użyć emulatora magazynu Azure do programowania i testowania](storage/common/storage-use-emulator.md).
> 
> 

Emulator magazynu udostępnia interfejs użytkownika, aby wyświetlić stan usługi Magazyn lokalny i uruchomić, zatrzymać, a ich ponownie. Po rozpoczęciu usługę emulatora magazynu można wyświetlić interfejs użytkownika lub rozpocząć lub zatrzymać usługi przez kliknięcie prawym przyciskiem myszy ikonę w obszarze powiadomień Microsoft Azure Emulator na pasku zadań systemu Windows.

## <a name="viewing-storage-emulator-data-in-server-explorer"></a>Wyświetlanie danych emulatora magazynu w Eksploratorze serwera
Węzeł magazynu Azure w Eksploratorze serwera umożliwia przeglądanie danych i zmienianie ustawień danych obiektów blob i tabeli na kontach magazynu, w tym emulatora magazynu. Zobacz [zasobów Zarządzanie magazynu obiektów Blob Azure przy użyciu Eksploratora usługi Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs) Aby uzyskać więcej informacji.

