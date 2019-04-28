---
title: Konfigurowanie i używanie emulatora usługi Storage z programem Visual Studio | Dokumentacja firmy Microsoft
description: Konfigurowanie i używanie emulatora usługi Storage z programem Visual Studio
services: visual-studio-online
author: ghogen
manager: douge
assetId: c8e7996f-6027-4762-806e-614b93131867
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 8/17/2017
ms.author: ghogen
ms.openlocfilehash: 39e2071a62d6a1f6ee050f862856815048e50430
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128300"
---
# <a name="configuring-and-using-the-storage-emulator-with-visual-studio"></a>Konfigurowanie i używanie emulatora usługi Storage z programem Visual Studio
[!INCLUDE [storage-try-azure-tools](../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Omówienie
Środowisko projektowe zestawu Azure SDK zawiera w emulatorze magazynu, narzędzia, która symuluje obiektów Blob, kolejek i tabel magazynu usług dostępnych na platformie Azure na lokalnej maszynie do programowania. Jeśli jesteś tworzenia usługi w chmurze, która używa usług Azure storage lub pisania aplikacji zewnętrznej, która wywołuje usług magazynu, można przetestować kod lokalnie z emulatora magazynu. Azure Tools dla programu Microsoft Visual Studio zintegrować Zarządzanie emulatora magazynu w programie Visual Studio. Narzędzia systemu Azure zainicjować bazy danych emulatora magazynu przy pierwszym użyciu uruchamia usługę emulatora magazynu, podczas uruchamiania lub debugowania kodu w programie Visual Studio i zapewnia dostęp tylko do odczytu do danych emulatora magazynu za pomocą Eksploratora usługi Azure Storage.

Aby uzyskać szczegółowe informacje w emulatorze magazynu, takie jak wymagania systemowe i konfiguracji niestandardowej, zobacz [korzystanie z emulatora magazynu Azure do tworzenia i testowania](storage/common/storage-use-emulator.md).

> [!NOTE]
> Istnieją pewne różnice w funkcjonalności między symulacji emulatora magazynu i usług Azure storage. Zobacz [różnice między emulatora magazynu i usług Azure Storage](storage/common/storage-use-emulator.md) w dokumentacji zestawu Azure SDK dla informacji na temat konkretne różnice.
> 
> 

## <a name="configuring-a-connection-string-for-the-storage-emulator"></a>Konfigurowanie parametrów połączenia dla emulatora magazynu
Aby uzyskać dostęp z emulatora magazynu, z kodu w ramach roli, należy skonfigurować parametry połączenia, który wskazuje na emulator magazynu i można to zmienić później, aby wskazać konto usługi Azure storage. Parametry połączenia to ustawienie konfiguracji, który może być odczytany przez Twoją rolę w czasie wykonywania, aby nawiązać połączenie z kontem magazynu. Aby uzyskać więcej informacji na temat tworzenia parametrów połączenia, zobacz [Konfigurowanie usługi Azure Storage, parametry połączenia](/azure/storage/common/storage-configure-connection-string).

> [!NOTE]
> Może zwrócić odwołanie do konta emulatora magazynu w kodzie za pomocą **DevelopmentStorageAccount** właściwości. Ta metoda działa prawidłowo, jeśli chcesz uzyskać dostęp do emulatora magazynu w kodzie, ale jeśli zamierzasz opublikować aplikację na platformie Azure, musisz utworzyć parametry połączenia można uzyskać dostępu do konta usługi Azure storage i modyfikować kodu, aby użyć tego połączenia ciąg przed jego opublikowaniem. Aby przełączyć się między kontem emulatora magazynu, a konto usługi Azure storage często, ciąg połączenia upraszcza ten proces.
> 
> 

## <a name="initializing-and-running-the-storage-emulator"></a>Inicjowanie i uruchamianie emulatora magazynu
Można określić, że podczas uruchamiania lub debugowania usługi w programie Visual Studio, Visual Studio powoduje automatyczne uruchomienie emulatora magazynu. W Eksploratorze rozwiązań Otwórz menu skrótów dla swojej **Azure** projektu, a następnie wybierz **właściwości**. Na **rozwoju** na karcie **Uruchom Emulator usługi Azure Storage** wybierz **True** (Jeśli nie jest już ustawione do tej wartości).

Podczas pierwszego uruchamiania lub debugowania usługi w programie Visual Studio emulator magazynu uruchamia proces inicjowania. Ten proces rezerwy portów lokalnych dla emulatora magazynu i tworzy bazę danych z emulatora magazynu. Po zakończeniu tego procesu nie trzeba uruchomić ponownie, chyba że zostanie usunięty emulatora bazy danych magazynu.

> [!NOTE]
> Począwszy od czerwca 2012 wersję narzędzi platformy Azure, emulator magazynu działa domyślnie, SQL Express LocalDB. We wcześniejszych wersjach programu Azure Tools, emulator magazynu jest uruchamiana dla domyślnego wystąpienia programu SQL Express 2005 lub 2008, które należy zainstalować przed zainstalowaniem zestawu Azure SDK. Można również uruchomić emulator magazynu przed nazwane wystąpienie programu SQL Express lub nazwane i domyślne wystąpienie programu Microsoft SQL Server. Jeśli potrzebujesz skonfigurować emulator magazynu, aby uruchamiać instancji innych niż domyślne wystąpienie, zobacz [korzystanie z emulatora magazynu Azure do tworzenia i testowania](storage/common/storage-use-emulator.md).
> 
> 

Emulator magazynu zapewnia interfejs użytkownika, aby wyświetlić stan usług magazynu lokalnego i można uruchomić, zatrzymać, a następnie je zresetować. Po rozpoczęciu usługa emulatora magazynu możesz wyświetlać interfejsu użytkownika lub uruchom lub Zatrzymaj usługę przez kliknięcie prawym przyciskiem myszy ikonę w obszarze powiadomień emulatorze Microsoft Azure, na pasku zadań Windows.

## <a name="viewing-storage-emulator-data-in-server-explorer"></a>Wyświetlanie danych emulatora magazynu, w Eksploratorze serwera
Węzła usługi Azure Storage w Eksploratorze serwera umożliwia wyświetlanie danych i zmienić ustawienia dla obiektów blob oraz tabel danych na kontach usługi storage, łącznie z emulatora magazynu. Zobacz [zasoby zarządzania usługi Azure Blob Storage za pomocą Eksploratora usługi Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs) Aby uzyskać więcej informacji.

