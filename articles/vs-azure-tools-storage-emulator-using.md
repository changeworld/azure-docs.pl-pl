---
title: Konfigurowanie i używanie emulatora magazynu za pomocą programu Visual Studio | Dokumenty firmy Microsoft
description: Konfigurowanie i używanie emulatora magazynu, narzędzie, które symuluje usługi magazynu obiektów Blob, Queue i Table storage dostępne na platformie Azure na lokalnym komputerze deweloperskim.
services: visual-studio-online
author: ghogen
manager: jillfra
assetId: c8e7996f-6027-4762-806e-614b93131867
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 8/17/2017
ms.author: ghogen
ms.openlocfilehash: a6f853924416cce2440ca15767044029b20e651f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75450726"
---
# <a name="configuring-and-using-the-storage-emulator-with-visual-studio"></a>Konfigurowanie i używanie emulatora usługi Storage za pomocą programu Visual Studio

[!INCLUDE [storage-try-azure-tools](../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Omówienie

Środowisko programistyczne azure SDK zawiera emulator magazynu, narzędzie, które symuluje usługi magazynu obiektów Blob, Queue i Table storage dostępne na platformie Azure na komputerze deweloperskim lokalnym. Jeśli budujesz usługę w chmurze, która wykorzystuje usługi magazynu platformy Azure lub zapisuje dowolną aplikację zewnętrzną, która wywołuje usługi magazynu, można przetestować kod lokalnie względem emulatora magazynu. Narzędzia platformy Azure dla programu Microsoft Visual Studio integrują zarządzanie emulatorem magazynu z programem Visual Studio. Narzędzia platformy Azure zainicjować bazy danych emulator magazynu przy pierwszym użyciu, uruchamia usługę emulator magazynu po uruchomieniu lub debugowania kodu z programu Visual Studio i zapewnia dostęp tylko do odczytu do danych emulatora magazynu za pośrednictwem Eksploratora usługi Azure Storage.

Aby uzyskać szczegółowe informacje na temat emulatora magazynu, w tym wymagania systemowe i instrukcje konfiguracji niestandardowej, zobacz [Używanie emulatora usługi Azure Storage do tworzenia i testowania.](storage/common/storage-use-emulator.md)

> [!NOTE]
> Istnieją pewne różnice w funkcjonalności między symulacją emulatora magazynu a usługami magazynu platformy Azure. Zobacz [różnice między emulatorem magazynu i usługami Azure Storage](storage/common/storage-use-emulator.md) w dokumentacji zestawu SDK platformy Azure, aby uzyskać informacje na temat określonych różnic.

## <a name="configuring-a-connection-string-for-the-storage-emulator"></a>Konfigurowanie ciągu połączenia dla emulatora magazynu

Aby uzyskać dostęp do emulatora magazynu z kodu w roli, należy skonfigurować parametry połączenia, który wskazuje emulator magazynu i które można później zmienić, aby wskazać konto magazynu platformy Azure. Parametry połączenia to ustawienie konfiguracji, które rola może odczytać w czasie wykonywania, aby połączyć się z kontem magazynu. Aby uzyskać więcej informacji na temat tworzenia ciągów połączeń, zobacz [Konfigurowanie ciągów połączeń usługi Azure Storage](/azure/storage/common/storage-configure-connection-string).

> [!NOTE]
> Odwołanie do konta emulatora magazynu z kodu można zwrócić przy użyciu **właściwości DevelopmentStorageAccount.** To podejście działa poprawnie, jeśli chcesz uzyskać dostęp do emulatora magazynu z kodu, ale jeśli planujesz opublikować aplikację na platformie Azure, musisz utworzyć ciąg połączenia, aby uzyskać dostęp do konta magazynu platformy Azure i zmodyfikować kod, aby użyć tego połączenia przed jego opublikowaniem. Jeśli często przełączasz się między kontem emulatora magazynu a kontem usługi Azure storage, parametry połączenia uprości ten proces.

## <a name="initializing-and-running-the-storage-emulator"></a>Inicjowanie i uruchamianie emulatora magazynu

Można określić, że po uruchomieniu lub debugowaniu usługi w programie Visual Studio program Visual Studio automatycznie uruchamia emulator magazynu. W Eksploratorze rozwiązań otwórz menu **skrótów**dla projektu **platformy Azure** i wybierz polecenie Właściwości . Na karcie **Rozwój** na liście **Rozpocznij emulator usługi Azure Storage** wybierz pozycję **True** (jeśli nie jest jeszcze ustawiona na tę wartość).  Niektóre typy projektów nie mają karty **Rozwoju.** W takim przypadku można włączyć lub wyłączyć uruchamianie emulatora `StartDevelopmentStorage` magazynu, ustawiając element w pliku projektu. Ustaw go na **True,** aby go włączyć, lub **False,** aby go wyłączyć.  Na przykład w projekcie usługi Azure Functions otwórz plik projektu do edycji i zmodyfikuj kod XML w następujący sposób:

```xml
  <PropertyGroup>
    <TargetFramework>netcoreapp2.1</TargetFramework>
    <AzureFunctionsVersion>v2</AzureFunctionsVersion>
    <StartDevelopmentStorage>True</StartDevelopmentStorage>
  </PropertyGroup>
```

Przy pierwszym uruchomieniu lub debugowaniu usługi z programu Visual Studio emulator magazynu uruchamia proces inicjowania. Ten proces rezerwuje porty lokalne dla emulatora magazynu i tworzy bazę danych emulatora magazynu. Po zakończeniu tego procesu nie trzeba uruchamiać ponownie, chyba że baza danych emulatora magazynu zostanie usunięta.

> [!NOTE]
> Począwszy od wersji z czerwca 2012 narzędzia azure emulator domyślnie uruchamia w SQL Express LocalDB. We wcześniejszych wersjach narzędzia azure emulator magazynu działa względem domyślnego wystąpienia programu SQL Express 2005 lub 2008, które należy zainstalować przed zainstalowaniem zestawu Azure SDK. Emulator magazynu można również uruchomić względem nazwanego wystąpienia programu SQL Express lub nazwanego lub domyślnego wystąpienia programu Microsoft SQL Server. Jeśli chcesz skonfigurować emulator magazynu do uruchamiania względem wystąpienia innego niż wystąpienie domyślne, zobacz [Użyj emulatora usługi Azure Storage do tworzenia i testowania.](storage/common/storage-use-emulator.md)

Emulator magazynu udostępnia interfejs użytkownika, aby wyświetlić stan usług magazynu lokalnego i uruchomić, zatrzymać i zresetować je. Po uruchomieniu usługi emulatora magazynu można wyświetlić interfejs użytkownika lub uruchomić lub zatrzymać usługę, klikając prawym przyciskiem myszy ikonę obszaru powiadomień dla emulatora platformy Microsoft Azure na pasku zadań systemu Windows.

## <a name="viewing-storage-emulator-data-in-server-explorer"></a>Wyświetlanie danych emulatora pamięci masowej w Eksploratorze serwera

Węzeł usługi Azure Storage w Eksploratorze serwera umożliwia wyświetlanie danych i zmienianie ustawień danych obiektów blob i tabeli na kontach magazynu, w tym emulator magazynu. Aby uzyskać więcej informacji, zobacz [Zarządzanie zasobami usługi Azure Blob Storage za pomocą Eksploratora magazynu.](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs)

