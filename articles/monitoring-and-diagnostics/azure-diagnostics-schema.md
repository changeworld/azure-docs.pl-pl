---
title: Azure Historia wersji schematu konfiguracji diagnostyki rozszerzenia
description: Właściwe dla zbierania liczników wydajności w maszynach wirtualnych platformy Azure, zestawy skalowania maszyny Wirtualnej, sieci szkieletowej usług i usługi w chmurze.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/16/2017
ms.author: robb
ms.component: diagnostic-extension
ms.openlocfilehash: 47fb598e9a0e722d51493fda1ff5180d4b022524
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35262202"
---
# <a name="azure-diagnostics-extension-configuration-schema-versions-and-history"></a>Azure wersji schematu konfiguracji rozszerzenia diagnostyki i historii
Indeksy tej strony wersji schematu rozszerzenia diagnostyki Azure dostarczana jako część zestawu SDK platformy Microsoft Azure.  

> [!NOTE]
> Rozszerzenie Azure Diagnostics jest składnik służący do zbierania liczników wydajności i innych danych statystycznych z:
> - Azure Virtual Machines 
> - Zestawy skali maszyn wirtualnych
> - Service Fabric 
> - Cloud Services 
> - Grupy zabezpieczeń sieci
> 
> Ta strona jest tylko istotne, jeśli używasz tych usług.

Rozszerzenia Azure Diagnostics jest używany z innymi produktami firmy Microsoft diagnostyki, takich jak Azure monitora, usługi Application Insights i analizy dzienników. Aby uzyskać więcej informacji, zobacz [omówienie narzędzi monitorowania Microsoft](monitoring-overview.md).

## <a name="azure-sdk-and-diagnostics-versions-shipping-chart"></a>Azure wersji zestawu SDK i informacji diagnostycznych wysyłania wykresu  

|Wersja zestawu SDK platformy Azure | Wersja rozszerzenia diagnostyki | Modelowanie|  
|------------------|-------------------------------|------|  
|1.x               |1.0                            |plug-in|  
|2.0 - 2.4         |1.0                            |plug-in|  
|2.5               |1.2                            |Rozszerzenia|  
|2.6               |1.3                            |"|  
|2.7               |1.4                            |"|  
|2.8               |1.5                            |"|  
|2.9               |1.6                            |"|
|2.96              |1.7                            |"|
|2.96              |1.8                            |"|
|2.96              |1.8.1                          |"|
|2.96              |1.9                            |"|



 Azure diagnostyki w wersji 1.0 najpierw dostarczoną w wtyczki model — co oznacza, że podczas instalowania zestawu Azure SDK uzyskano wersję diagnostyki Azure zostały wydane z nim.  

 Począwszy od zestawu SDK, 2.5 (Diagnostyka w wersji 1.2), Diagnostyka Azure poszło na model rozszerzenia. Narzędzia mogą korzystać z nowych funkcji tylko były dostępne w nowszej Azure SDK, ale usługi za pomocą diagnostyki Azure czy odebrania najnowszej wersji wysyłki bezpośrednio z platformy Azure. Na przykład każdy użytkownik nadal przy użyciu zestawu SDK, 2.5 będzie można ładowania najnowszej wersji, które są wyświetlane w poprzedniej tabeli, niezależnie od tego, jeśli używają nowsze funkcje.  

## <a name="schemas-index"></a>Indeks schematów  
Różne wersje diagnostyki Azure używają schematy innej konfiguracji. 

[Schemat konfiguracji diagnostyki 1.0](azure-diagnostics-schema-1dot0.md)  

[Schemat konfiguracji diagnostyki 1.2](azure-diagnostics-schema-1dot2.md)  

[Diagnostyka 1.3 i nowszym schemat konfiguracji](azure-diagnostics-schema-1dot3-and-later.md)  

## <a name="version-history"></a>Historia wersji


### <a name="diagnostics-extension-19"></a>Diagnostyka rozszerzenia 1.9 
Dodano obsługę Docker.


### <a name="diagnostics-extension-181"></a>Diagnostyka rozszerzenia 1.8.1 
Można określić tokenu sygnatury dostępu Współdzielonego, zamiast klucz konta magazynu w konfiguracji prywatnej. Jeśli zostanie podany SAS token, klucz konta magazynu jest ignorowana.


```json
{
    "storageAccountName": "diagstorageaccount",
    "storageAccountEndPoint": "https://core.windows.net",
    "storageAccountSasToken": "{sas token}",
    "SecondaryStorageAccounts": {
        "StorageAccount": [
            {
                "name": "secondarydiagstorageaccount",
                "endpoint": "https://core.windows.net",
                "sasToken": "{sas token}"
            }
        ]
    }
}
```

```xml
<PrivateConfig>
    <StorageAccount name="diagstorageaccount" endpoint="https://core.windows.net" sasToken="{sas token}" />
    <SecondaryStorageAccounts>
        <StorageAccount name="secondarydiagstorageaccount" endpoint="https://core.windows.net" sasToken="{sas token}" />
    </SecondaryStorageAccounts>
</PrivateConfig>
```


### <a name="diagnostics-extension-18"></a>Diagnostyka rozszerzenia 1.8 
Typ magazynu dodanych do PublicConfig. Może być StorageType *tabeli*, *obiektu Blob*, *TableAndBlob*. *Tabela* jest ustawieniem domyślnym.


```json
{
    "WadCfg": {
    },
    "StorageAccount": "diagstorageaccount",
    "StorageType": "TableAndBlob"
}
```

```xml
<PublicConfig>
    <WadCfg />
    <StorageAccount>diagstorageaccount</StorageAccount>
    <StorageType>TableAndBlob</StorageType>
</PublicConfig>
```


### <a name="diagnostics-extension-17"></a>Diagnostyka rozszerzenia 1.7 
Dodano możliwość trasy do Centrum EventHub.

### <a name="diagnostics-extension-15"></a>Rozszerzenia diagnostyki w wersji 1.5
Dodaje element wychwytywanie i umożliwia wysyłanie danych diagnostycznych [usługi Application Insights](../application-insights/app-insights-cloudservices.md) co ułatwia diagnozowanie problemów w aplikacji, a także poziom systemu i infrastruktury.

### <a name="azure-sdk-26-and-diagnostics-extension-13"></a>2.6 zestawu SDK i informacji diagnostycznych rozszerzenie Azure 1.3 
Usługi w chmurze projektów programu Visual Studio wprowadzono następujące zmiany. (Te zmiany również dotyczyć nowsze wersje zestawu SDK platformy Azure.)

* Lokalne emulatora obsługuje teraz diagnostyki. Ta zmiana oznacza, że można zbierać dane diagnostyczne i upewnij się, że aplikacja tworzy prawo śladów podczas tworzenia i testowania w programie Visual Studio. Parametry połączenia `UseDevelopmentStorage=true` umożliwia zbieranie danych diagnostycznych, gdy używasz projekt usługi w chmurze w programie Visual Studio przy użyciu emulatora magazynu Azure. Wszystkie dane diagnostyczne są zbierane w ramach konta magazynu (Programowanie magazynu).
* Parametry połączenia konta magazynu diagnostyki (Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString) znajduje się jeszcze raz w pliku konfiguracji (cscfg) usługi. W systemie Azure SDK 2.5 konto magazynu diagnostyki została określona w pliku diagnostics.wadcfgx.

Istnieją pewne ważne różnice między jak parametry połączenia działał w 2.4 zestawu SDK platformy Azure i starszych i jak działa w Azure SDK w wersji 2.6 lub nowszej.

* W 2.4 zestawu SDK platformy Azure i starszych ciąg połączenia był używany w czasie wykonywania w dodatku diagnostyki można pobrać informacji o koncie magazynu przesyłania dzienników diagnostycznych.
* W Azure SDK w wersji 2.6 lub nowszej Visual Studio używa diagnostyki parametry połączenia do konfigurowania rozszerzenia diagnostyki magazynu odpowiednie informacje o koncie podczas publikowania. Parametry połączenia umożliwia definiowanie różnych kont magazynu dla konfiguracji innej usługi, które Visual Studio będzie korzystać w przypadku publikowania. Jednak ponieważ wtyczki Diagnostyka nie jest już dostępny (po 2.5 zestawu SDK platformy Azure), plik .cscfg samodzielnie nie można włączyć rozszerzenia diagnostyki. Należy włączyć rozszerzenie z osobna za pomocą narzędzi, takich jak Visual Studio lub programu PowerShell.
* Aby uprościć proces konfigurowania rozszerzenia diagnostyki przy użyciu programu PowerShell, dane wyjściowe pakietu z programu Visual Studio zawiera publicznej konfiguracji XML dla rozszerzenia diagnostyki dla każdej roli. Visual Studio będzie korzystać diagnostyki parametry połączenia do wypełniania informacji o koncie magazynu, które są obecne w publicznej konfiguracji. Pliki konfiguracji publicznego są tworzone w folderze rozszerzenia i postępuj zgodnie ze wzorcem PaaSDiagnostics. <RoleName>. PubConfig.xml. Wszystkie wdrożenia na podstawie programu PowerShell można użyć tego wzorca do mapowania każdej konfiguracji do roli.
* Parametry połączenia w pliku .cscfg służy również w portalu Azure do uzyskiwania dostępu do danych diagnostycznych, może występować w **monitorowanie** kartę. Ciąg połączenia jest potrzebne do skonfigurowania usługi, aby wyświetlić pełne dane monitorowania w portalu.

#### <a name="migrating-projects-to-azure-sdk-26-and-later"></a>Migrowanie projektów Azure SDK w wersji 2.6 lub nowszego
Podczas migracji z 2.5 zestawu SDK platformy Azure do usługi Azure SDK w wersji 2.6 lub nowszej, jeśli masz konto magazynu diagnostyki określone w pliku .wadcfgx, następnie pozostanie on istnieje. Aby móc korzystać z elastyczność przy użyciu różnych kont magazynu dla konfiguracji innego magazynu, musisz ręcznie dodać parametry połączenia do projektu. W przypadku migrowania projektu z Azure SDK 2.4 lub jego starszej wersji 2.6 zestawu SDK platformy Azure, Diagnostyka parametry połączenia zostaną zachowane. Pamiętaj jednak, zmiany w sposób parametry połączenia są traktowane w Azure SDK w wersji 2.6 określone w poprzedniej sekcji.

#### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>Jak Visual Studio Określa konto magazynu diagnostyki
* Jeśli parametry połączenia diagnostyki jest określone w pliku .cscfg, Visual Studio używa go do konfigurowania rozszerzenia diagnostyki podczas publikowania i podczas generowania publicznej konfiguracji plików xml podczas pakowania.
* Jeśli ciąg połączenia diagnostyki nie została określona w pliku .cscfg, następnie Visual Studio powraca do przy użyciu konta magazynu określony w pliku .wadcfgx, aby skonfigurować rozszerzenie diagnostyki podczas publikowania i generowanie publicznej konfiguracji plików xml Podczas tworzenia pakietu.
* Diagnostyka parametry połączenia w pliku .cscfg mają pierwszeństwo przed konta magazynu w pliku .wadcfgx. Jeśli parametry połączenia diagnostyki jest określone w pliku .cscfg, Visual Studio użyty i ignoruje konta magazynu w .wadcfgx.

#### <a name="what-does-the-update-development-storage-connection-strings-checkbox-do"></a>Co to jest "Aktualizacja programowanie magazynu ciągów połączenia..." czy pole wyboru?
Pole wyboru **zaktualizować parametry połączenia magazynu programowanie dla diagnostyki i buforowanie z poświadczeniami konta magazynu Microsoft Azure podczas publikowania do systemu Microsoft Azure** udostępnia wygodny sposób zaktualizować rozwojem Parametry połączenia konta magazynu z kontem magazynu systemu Azure podczas publikowania określono.

Załóżmy na przykład, zaznacz to pole wyboru i określa parametry połączenia diagnostyki `UseDevelopmentStorage=true`. Podczas publikowania projektu na platformie Azure, programu Visual Studio automatycznie zaktualizuje diagnostyki parametry połączenia z kontem magazynu określone w Kreatorze publikowania. Jednak jeśli konto magazynu rzeczywista określono jako parametry połączenia diagnostyki, następnie to konto jest używana zamiast tego.

### <a name="diagnostics-functionality-differences-between-azure-sdk-24-and-earlier-and-azure-sdk-25-and-later"></a>Diagnostyka funkcji różnice między 2.4 zestawu SDK platformy Azure i wcześniej i Azure SDK, 2.5 lub nowszej
Jeśli uaktualniasz projektu z 2.4 zestawu SDK platformy Azure, Azure SDK 2.5 lub nowszej, musisz należy pamiętać następujące różnice funkcji diagnostyki.

* **Konfiguracyjne interfejsy API są przestarzałe** — trybu diagnostyki jest dostępna w 2.4 zestawu SDK platformy Azure i jego wcześniejsze wersje, ale jest przestarzała w wersji 2.5 zestawu SDK platformy Azure i nowszych. Jeśli konfiguracji diagnostyki jest obecnie zdefiniowane w kodzie, należy ponownie skonfigurować te ustawienia od podstaw w projekcie migrowane w kolejności diagnostyki, aby kontynuować pracę. Plik konfiguracji diagnostyki Azure SDK 2.4 jest diagnostics.wadcfg i diagnostics.wadcfgx dla 2.5 zestawu SDK platformy Azure i nowszych.
* **Diagnostyki dla aplikacji usługi w chmurze można skonfigurować tylko na poziomie roli, nie na poziomie wystąpienia.**
* **Za każdym razem, gdy wdrażanie aplikacji, zaktualizowano konfigurację diagnostyki** — może to powodować problemy z parzystością, jeśli zmiana konfiguracji diagnostyki z Eksploratora serwera, a następnie ponowne wdrożenie aplikacji.
* **W wersji 2.5 zestawu SDK platformy Azure i nowszych, zrzuty awaryjne są konfigurowane w pliku konfiguracji diagnostyki nie w kodzie** — Jeśli masz zrzuty awaryjne skonfigurowane w kodzie, musisz ręcznie przenieść konfiguracji z kodu do pliku konfiguracji, ponieważ zrzuty awaryjne nie są przenoszone podczas migracji do 2.6 zestawu SDK platformy Azure.

