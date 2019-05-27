---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/15/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 6882c46ec0e4925c42de86c87225e9509c84df84
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66159850"
---
## <a name="set-up-your-development-environment"></a>Konfigurowanie środowiska projektowego
Następnie skonfiguruj środowisko projektowe w programie Visual Studio, aby przygotować się do wypróbowania przykładów kodu zawartych w tym przewodniku.

### <a name="create-a-windows-console-application-project"></a>Utwórz projekt aplikacji konsoli dla systemu Windows
W programie Visual Studio utwórz nową aplikację konsoli dla systemu Windows. W poniższej procedurze pokazano, jak utworzyć aplikację konsolową w programie Visual Studio 2017. Procedura jest podobna w innych wersjach programu Visual Studio.

1. Wybierz kolejno pozycje **Plik** > **Nowy** > **Projekt**.
2. Wybierz kolejno pozycje **Zainstalowane** > **Szablony** > **Visual C#** > **Klasyczny pulpit systemu Windows**.
3. Wybierz pozycję **Aplikacja konsoli (.NET Framework)**.
4. Wprowadź nazwę aplikacji w polu **Nazwa**.
5. Kliknij przycisk **OK**.

![Zrzut ekranu przedstawiający okno dialogowe nowego projektu w programie Visual Studio](./media/storage-development-environment-include/storage-development-environment-include-1.png)

Wszystkie przykłady kodu w tym samouczku można dodać do metody `Main()` w pliku `Program.cs` aplikacji konsolowej.

Biblioteki klienta usługi Azure Storage można używać w aplikacjach .NET dowolnego typu , m.in. usługach w chmurze lub aplikacjach internetowych platformy Azure, aplikacjach pulpitu i aplikacjach mobilnych. W tym przewodniku dla uproszczenia przedstawiono aplikację konsolową.

### <a name="use-nuget-to-install-the-required-packages"></a>Użycie pakietu NuGet w celu zainstalowania wymaganych pakietów
Istnieją dwa pakiety, które trzeba przywołać w projekcie, aby ukończyć ten samouczek:

* [Biblioteka klienta usługi Microsoft Azure Storage dla platformy .NET](https://www.nuget.org/packages/WindowsAzure.Storage/): ten pakiet zapewnia dostęp programowy do zasobów danych na koncie magazynu.
* [Biblioteka programu Microsoft Azure Configuration Manager dla środowiska .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/): ten pakiet zawiera klasę do analizowania parametrów połączenia w pliku konfiguracji, niezależnie od tego, gdzie została uruchomiona aplikacja.

Pakiet NuGet służy do pobrania obu pakietów. Wykonaj następujące kroki:

1. Kliknij projekt prawym przyciskiem myszy w **Eksploratorze rozwiązań** i wybierz polecenie **Zarządzaj pakietami NuGet**.
2. Wyszukaj w trybie online ciąg „WindowsAzure.Storage”, a następnie wybierz pozycję **Zainstaluj**, aby zainstalować Bibliotekę klienta usługi Storage oraz jej zależności.
3. Wyszukaj w trybie online ciąg „WindowsAzure.ConfigurationManager” i wybierz pozycję **Zainstaluj**, aby zainstalować program Azure Configuration Manager.

> [!NOTE]
> Pakiet Biblioteki klienta usługi Storage znajduje się również w [zestawie SDK Azure dla środowiska .NET](https://azure.microsoft.com/downloads/). Firma Microsoft zaleca jednak również zainstalowanie Biblioteki klienta usługi Storage z pakietu NuGet, aby zagwarantować posiadanie najnowszej wersji biblioteki klienta.
> 
> Zależności ODataLib w Bibliotece klienta usługi Storage dla platformy .NET są rozwiązywane za pomocą pakietów ODataLib dostępnych w usłudze NuGet, a nie za pośrednictwem usług WCF Data Services. Biblioteki ODataLib można pobrać bezpośrednio lub użyć odwołań w projekcie kodu za pośrednictwem pakietu NuGet. Określone pakiety ODataLib używane przez Bibliotekę klienta usługi Storage to [OData](http://nuget.org/packages/Microsoft.Data.OData/), [Edm](http://nuget.org/packages/Microsoft.Data.Edm/) i [Spatial](http://nuget.org/packages/System.Spatial/). Ze względu na to, że biblioteki te są używane przez klasy usługi Azure Table Storage, są one wymaganymi zależnościami do programowania za pomocą Biblioteki klienta usługi Storage.
> 
> 

### <a name="determine-your-target-environment"></a>Określanie środowiska docelowego
W przypadku uruchamiania przykładów w tym przewodniku istnieją dwie opcje środowiska:

* Można uruchomić kod dla konta usługi Azure Storage w chmurze. 
* Można uruchomić kod dla emulatora usługi Azure Storage. Emulator magazynu jest lokalnym środowiskiem, które emuluje konto usługi Azure Storage w chmurze. Emulator jest bezpłatną opcją do testowania i debugowania kodu, gdy aplikacja jest w fazie projektowania. Emulator używa dobrze znanego konta i klucza. Aby uzyskać więcej informacji, zobacz [Używanie emulatora usługi Azure Storage do programowania i testowania](../articles/storage/common/storage-use-emulator.md).

Jeśli obiektem docelowym jest konto magazynu w chmurze, skopiuj podstawowy klucz dostępu dla konta magazynu z witryny Azure Portal. Aby uzyskać więcej informacji, zobacz temat [Klucze dostępu](../articles/storage/common/storage-account-manage.md#access-keys).

> [!NOTE]
> Na obiekt docelowy można wybrać emulator magazynu, aby uniknąć ponoszenia kosztów związanych z usługą Azure Storage. Jednak jeśli wybranym obiektem docelowym będzie konto usługi Azure Storage w chmurze, koszty związane z wykonaniem instrukcji w tym samouczku będą niewielkie.
> 
> 

### <a name="configure-your-storage-connection-string"></a>Konfigurowanie parametrów połączenia magazynu
Biblioteka klienta usługi Storage platformy Azure dla środowiska .NET obsługuje korzystanie z parametrów połączenia magazynu w celu konfiguracji punktów końcowych i poświadczeń w celu uzyskania dostępu do usług Storage. Najlepiej przechowywać parametry połączenia magazynu w pliku konfiguracji. 

Aby uzyskać więcej informacji dotyczących parametrów połączenia, zobacz [Konfigurowanie parametrów połączenia z usługą Azure Storage](../articles/storage/common/storage-configure-connection-string.md).

> [!NOTE]
> Klucz konta magazynu jest podobny do hasła głównego konta magazynu. Zawsze chroń klucz konta magazynu. Nie udostępniaj go innym użytkownikom, nie koduj go trwale ani nie zapisuj w zwykłym pliku tekstowym, do którego mają dostęp inne osoby. Wygeneruj ponownie klucz za pośrednictwem witryny Azure Portal, jeśli uważasz, że jego zabezpieczenia mogły zostać naruszone.
> 
> 

Aby skonfigurować parametry połączenia, otwórz plik `app.config` z Eksploratora rozwiązań w programie Visual Studio. Dodaj zawartość elementu `<appSettings>` widocznego poniżej. Zastąp ciąg `account-name` nazwą konta magazynu, a ciąg `account-key` kluczem dostępu do konta:

```xml
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key" />
    </appSettings>
</configuration>
```

Na przykład ustawienie konfiguracji może wyglądać mniej więcej tak:

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=GMuzNHjlB3S9itqZJHHCnRkrokLkcSyW7yK9BRbGp0ENePunLPwBgpxV1Z/pVo9zpem/2xSHXkMqTHHLcx8XRA==" />
```

Jeśli obiektem docelowym ma być emulator magazynu, możesz użyć skrótu klawiaturowego, który mapuje dobrze znaną nazwę konta i klucz. W takim przypadku ustawienie parametrów połączenia wygląda następująco:

```xml
<add key="StorageConnectionString" value="UseDevelopmentStorage=true;" />
```

