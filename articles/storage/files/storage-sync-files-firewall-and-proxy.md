---
title: Ustawienia zapory lokalnej i serwera proxy usługi Azure File Sync | Dokumenty firmy Microsoft
description: Konfiguracja sieci lokalnej usługi Azure File Sync
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 7f398012edc25ba6a04e230fa8049e7264f857bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294522"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Ustawienia serwera proxy i zapory usługi Azure File Sync
Usługa Azure File Sync łączy serwery lokalne z usługą Azure Files, umożliwiając synchronizację wielu lokacji i tworzenie warstw w chmurze. W związku z tym serwer lokalny musi być połączony z Internetem. Administrator IT musi zdecydować najlepszą ścieżkę dostępu serwera do usług w chmurze platformy Azure.

Ten artykuł zapewni wgląd w określone wymagania i opcje dostępne do pomyślnego i bezpiecznego połączenia serwera z synchronizacją plików platformy Azure.

## <a name="overview"></a>Omówienie
Usługa Azure File Sync działa jako usługa aranżacji między systemem Windows Server, udziałem plików platformy Azure i kilkoma innymi usługami platformy Azure w celu synchronizacji danych zgodnie z opisem w grupie synchronizacji. Aby usługa Azure File Sync działała poprawnie, należy skonfigurować serwery do komunikowania się z następującymi usługami platformy Azure:

- Azure Storage
- Azure File Sync
- Azure Resource Manager
- Usługi uwierzytelniania

> [!Note]  
> Agent synchronizacji plików platformy Azure w systemie Windows Server inicjuje wszystkie żądania do usług w chmurze, co powoduje, że musi brać pod uwagę ruch wychodzący z punktu widzenia zapory. <br /> Żadna usługa platformy Azure nie inicjuje połączenia z agentem usługi Azure File Sync.

## <a name="ports"></a>Porty
Usługa Azure File Sync przenosi dane plików i metadane wyłącznie za pośrednictwem protokołu HTTPS i wymaga, aby port 443 był otwarty wychodzący.
W rezultacie cały ruch jest szyfrowany.

## <a name="networks-and-special-connections-to-azure"></a>Sieci i specjalne połączenia z platformą Azure
Agent usługi Azure File Sync nie ma żadnych wymagań dotyczących specjalnych kanałów, takich jak [ExpressRoute](../../expressroute/expressroute-introduction.md)itp.

Usługa Azure File Sync będzie działać za pomocą wszelkich dostępnych środków, które umożliwiają dostęp do platformy Azure, automatycznie dostosowując się do różnych cech sieci, takich jak przepustowość, opóźnienie, a także oferując kontrolę administratora w celu dostrajania. Nie wszystkie funkcje są obecnie dostępne. Jeśli chcesz skonfigurować określone zachowanie, poinformuj nas o tym za pośrednictwem [usługi Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage?category_id=180670).

## <a name="proxy"></a>Serwer proxy
Usługa Azure File Sync obsługuje ustawienia serwera proxy specyficzne dla aplikacji i całego komputera.

**Ustawienia serwera proxy specyficzne dla aplikacji** umożliwiają konfigurację serwera proxy specjalnie dla ruchu usługi Azure File Sync. Ustawienia serwera proxy specyficzne dla aplikacji są obsługiwane w wersji agenta 4.0.1.0 lub nowszej i mogą być konfigurowane podczas instalacji agenta lub przy użyciu polecenia cmdlet programu PowerShell Set-StorageSyncProxyConfiguration.

Polecenia programu PowerShell służące do konfigurowania ustawień serwera proxy specyficznych dla aplikacji:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Set-StorageSyncProxyConfiguration -Address <url> -Port <port number> -ProxyCredential <credentials>
```
**Ustawienia serwera proxy dla całego komputera** są przezroczyste dla agenta usługi Azure File Sync, ponieważ cały ruch serwera jest kierowany przez serwer proxy.

Aby skonfigurować ustawienia serwera proxy dla całego komputera, wykonaj poniższe czynności: 

1. Konfigurowanie ustawień serwera proxy dla aplikacji .NET 

   - Edytuj te dwa pliki:  
     C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config  
     C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config

   - Dodaj sekcję> <system.net w plikach machine.config (poniżej sekcji <system.serviceModel>).  Zmień 127.0.01:8888 na adres IP i port serwera proxy. 
     ```
      <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
          <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
      </system.net>
     ```

2. Ustawianie ustawień serwera proxy WinHTTP 

   - Uruchom następujące polecenie z wiersza polecenia z podwyższonym poziomem uprawnień lub programu PowerShell, aby wyświetlić istniejące ustawienie serwera proxy:   

     netsh winhttp pokaż serwer proxy

   - Uruchom następujące polecenie z wiersza polecenia z podwyższonym poziomem uprawnień lub programu PowerShell, aby ustawić ustawienie serwera proxy (zmień 127.0.01:8888 na adres IP i port serwera proxy):  

     netsh winhttp set proxy 127.0.0.1:8888

3. Uruchom ponownie usługę Agent synchronizacji magazynu, uruchamiając następujące polecenie z wiersza polecenia z podwyższonym poziomem uprawnień lub programu PowerShell: 

      net stop filesyncsvc

      Uwaga: Usługa Agent synchronizacji pamięci masowej (filesyncsvc) zostanie automatycznie uruchomiony po zatrzymaniu.

## <a name="firewall"></a>Zapora
Jak wspomniano w poprzedniej sekcji, port 443 musi być otwarty wychodzący. Na podstawie zasad w centrum danych, oddział lub region, dalsze ograniczenie ruchu przez ten port do określonych domen może być pożądane lub wymagane.

W poniższej tabeli opisano wymagane domeny do komunikacji:

| Usługa | Punkt końcowy chmury publicznej | Punkt końcowy platformy Azure dla instytucji rządowych | Sposób użycia |
|---------|----------------|---------------|------------------------------|
| **Azure Resource Manager** | `https://management.azure.com` | https://management.usgovcloudapi.net | Każde wywołanie użytkownika (takie jak program PowerShell) przechodzi do/za pośrednictwem tego adresu URL, w tym początkowego wywołania rejestracji serwera. |
| **Azure Active Directory** | https://login.windows.net<br>`https://login.microsoftonline.com` | https://login.microsoftonline.us | Wywołania usługi Azure Resource Manager muszą być wykonane przez uwierzytelnionego użytkownika. Aby odnieść sukces, ten adres URL jest używany do uwierzytelniania użytkowników. |
| **Azure Active Directory** | https://graph.microsoft.com/ | https://graph.microsoft.com/ | W ramach wdrażania usługi Azure File Sync zostanie utworzony podmiot usługi w usłudze Azure Active Directory subskrypcji. Ten adres URL jest używany do tego. Ten podmiot zabezpieczeń jest używany do delegowanowania minimalnego zestawu praw do usługi Azure File Sync. Użytkownik wykonujący początkową konfigurację usługi Azure File Sync musi być uwierzytelnionym użytkownikiem z uprawnieniami właściciela subskrypcji. |
| **Azure Storage** | &ast;core.windows.net .core.windows.net | &ast;core.usgovcloudapi.net .core.usgovcloudapi.net | Gdy serwer pobiera plik, serwer wykonuje ten ruch danych bardziej efektywnie podczas rozmowy bezpośrednio z udziałem plików platformy Azure na koncie magazynu. Serwer ma klucz Sygnatury dostępu Współdzielonego, który umożliwia tylko dostęp do docelowego udziału plików. |
| **Azure File Sync** | &ast;one.microsoft.com .one.microsoft.com<br>&ast;afs.azure.net . | &ast;afs.azure.us . | Po początkowej rejestracji serwera serwer otrzymuje regionalny adres URL dla wystąpienia usługi Azure File Sync w tym regionie. Serwer może używać adresu URL do komunikowania się bezpośrednio i skutecznie z wystąpieniem obsługującym jego synchronizację. |
| **Microsoft PKI** | https://www.microsoft.com/pki/mscorp/cps<br><http://ocsp.msocsp.com> | https://www.microsoft.com/pki/mscorp/cps<br><http://ocsp.msocsp.com> | Po zainstalowaniu agenta synchronizacji plików azure adres URL infrastruktury kluczy publicznych jest używany do pobierania certyfikatów pośrednich wymaganych do komunikowania się z usługą Azure File Sync i udziałem plików platformy Azure. Adres URL programu OCSP służy do sprawdzania stanu certyfikatu. |

> [!Important]
> Podczas zezwalania &ast;na ruch do .one.microsoft.com, ruch do więcej niż tylko usługi synchronizacji jest możliwe z serwera. Istnieje wiele innych usług firmy Microsoft dostępnych poddomenami.

Jeśli &ast;.one.microsoft.com jest zbyt szeroki, można ograniczyć komunikację serwera, zezwalając na komunikację tylko jawne wystąpienia regionalne usługi Azure Files Sync. To, które wystąpienia wybrać, zależy od regionu wdrożonej i zarejestrowanej usługi synchronizacji magazynu. Ten region jest nazywany "podstawowy adres URL punktu końcowego" w poniższej tabeli.

Ze względów ciągłości działania i odzyskiwania po awarii (BCDR) można określić udziały plików platformy Azure na koncie magazynu globalnie nadmiarowego (GRS). W takim przypadku udziały plików platformy Azure zostaną przejęte awaryjnie do sparowanego regionu w przypadku trwałej awarii regionalnej. Usługa Azure File Sync używa tych samych par regionalnych co magazyn. Jeśli więc używasz kont magazynu GRS, musisz włączyć dodatkowe adresy URL, aby umożliwić serwerowi rozmawianie z sparowanym regionem dla usługi Azure File Sync. Poniższa tabela nazywa ten "region sparowany". Ponadto istnieje adres URL profilu menedżera ruchu, który również musi być włączony. Zapewni to bezproblemową trasę ruchu sieciowego do sparowanego regionu w przypadku awarii i nazywa się "Odnajdowanie adresu URL" w poniższej tabeli.

| Chmura  | Region | Podstawowy adres URL punktu końcowego | Region sparowany | Adres URL odnajdywania |
|--------|--------|----------------------|---------------|---------------|
| Public |Australia Wschodnia | https:\//kailani-aue.one.microsoft.com | Australia Południowo-Wschodnia | https:\//tm-kailani-aue.one.microsoft.com |
| Public |Australia Południowo-Wschodnia | https:\//kailani-aus.one.microsoft.com | Australia Wschodnia | https:\//tm-kailani-aus.one.microsoft.com |
| Public | Brazylia Południowa | https:\//brazilsouth01.afs.azure.net | Południowo-środkowe stany USA | https:\//tm-brazilsouth01.afs.azure.net |
| Public | Kanada Środkowa | https:\//kailani-cac.one.microsoft.com | Kanada Wschodnia | https:\//tm-kailani-cac.one.microsoft.com |
| Public | Kanada Wschodnia | https:\//kailani-cae.one.microsoft.com | Kanada Środkowa | https:\//tm-kailani.cae.one.microsoft.com |
| Public | Indie Środkowe | https:\//kailani-cin.one.microsoft.com | Indie Południowe | https:\//tm-kailani-cin.one.microsoft.com |
| Public | Środkowe stany USA | https:\//kailani-cus.one.microsoft.com | Wschodnie stany USA 2 | https:\//tm-kailani-cus.one.microsoft.com |
| Public | Azja Wschodnia | https:\//kailani11.one.microsoft.com | Azja Południowo-Wschodnia | https:\//tm-kailani11.one.microsoft.com |
| Public | Wschodnie stany USA | https:\//kailani1.one.microsoft.com | Zachodnie stany USA | https:\//tm-kailani1.one.microsoft.com |
| Public | Wschodnie stany USA 2 | https:\//kailani-ess.one.microsoft.com | Środkowe stany USA | https:\//tm-kailani-ess.one.microsoft.com |
| Public | Japonia Wschodnia | https:\//japaneast01.afs.azure.net | Japonia Zachodnia | https:\//tm-japaneast01.afs.azure.net |
| Public | Japonia Zachodnia | https:\//japanwest01.afs.azure.net | Japonia Wschodnia | https:\//tm-japanwest01.afs.azure.net |
| Public | Korea Środkowa | https:\//koreacentral01.afs.azure.net/ | Korea Południowa | https:\//tm-koreacentral01.afs.azure.net/ |
| Public | Korea Południowa | https:\//koreasouth01.afs.azure.net/ | Korea Środkowa | https:\//tm-koreasouth01.afs.azure.net/ |
| Public | Północno-środkowe stany USA | https:\//northcentralus01.afs.azure.net | Południowo-środkowe stany USA | https:\//tm-northcentralus01.afs.azure.net |
| Public | Europa Północna | https:\//kailani7.one.microsoft.com | Europa Zachodnia | https:\//tm-kailani7.one.microsoft.com |
| Public | Południowo-środkowe stany USA | https:\//southcentralus01.afs.azure.net | Północno-środkowe stany USA | https:\//tm-southcentralus01.afs.azure.net |
| Public | Indie Południowe | https:\//kailani-sin.one.microsoft.com | Indie Środkowe | https:\//tm-kailani-sin.one.microsoft.com |
| Public | Azja Południowo-Wschodnia | https:\//kailani10.one.microsoft.com | Azja Wschodnia | https:\//tm-kailani10.one.microsoft.com |
| Public | Południowe Zjednoczone Królestwo | https:\//kailani-uks.one.microsoft.com | Zachodnie Zjednoczone Królestwo | https:\//tm-kailani-uks.one.microsoft.com |
| Public | Zachodnie Zjednoczone Królestwo | https:\//kailani-ukw.one.microsoft.com | Południowe Zjednoczone Królestwo | https:\//tm-kailani-ukw.one.microsoft.com |
| Public | Zachodnio-środkowe stany USA | https:\//westcentralus01.afs.azure.net | Zachodnie stany USA 2 | https:\//tm-westcentralus01.afs.azure.net |
| Public | Europa Zachodnia | https:\//kailani6.one.microsoft.com | Europa Północna | https:\//tm-kailani6.one.microsoft.com |
| Public | Zachodnie stany USA | https:\//kailani.one.microsoft.com | Wschodnie stany USA | https:\//tm-kailani.one.microsoft.com |
| Public | Zachodnie stany USA 2 | https:\//westus201.afs.azure.net | Zachodnio-środkowe stany USA | https:\//tm-westus201.afs.azure.net |
| Instytucje rządowe | US Gov Arizona | https:\//usgovarizona01.afs.azure.us | US Gov Teksas | https:\//tm-usgovarizona01.afs.azure.us |
| Instytucje rządowe | US Gov Teksas | https:\//usgovtexas01.afs.azure.us | US Gov Arizona | https:\//tm-usgovtexas01.afs.azure.us |

- Jeśli używasz lokalnie nadmiarowych (LRS) lub stref nadmiarowych (ZRS) kont magazynu, wystarczy włączyć adres URL wymieniony w obszarze "Podstawowy adres URL punktu końcowego".

- Jeśli używasz globalnie nadmiarowych (GRS) kont magazynu, włącz trzy adresy URL.

**Przykład:** Wdrożyć usługę synchronizacji `"West US"` magazynu i zarejestrować serwer z nim. Adresy URL umożliwiające serwerowi komunikowanie się w tym przypadku to:

> - https:\//kailani.one.microsoft.com (podstawowy punkt końcowy: Zachodnie stany USA)
> - https:\//kailani1.one.microsoft.com (sparowany region awaryjny: wschodnie stany USA)
> - https:\//tm-kailani.one.microsoft.com (adres URL odnajdywania regionu podstawowego)

### <a name="allow-list-for-azure-file-sync-ip-addresses"></a>Zezwalaj na listę adresów IP synchronizacji plików platformy Azure
Usługa Azure File Sync obsługuje używanie [tagów usługi,](../../virtual-network/service-tags-overview.md)które reprezentują grupę prefiksów adresów IP dla danej usługi platformy Azure. Za pomocą tagów usługi można tworzyć reguły zapory, które umożliwiają komunikację z usługą Azure File Sync. Tagiem usługi dla usługi `StorageSyncService`Azure File Sync jest .

Jeśli używasz usługi Azure File Sync na platformie Azure, możesz użyć nazwy tagu usługi bezpośrednio w sieciowej grupie zabezpieczeń, aby zezwolić na ruch. Aby dowiedzieć się więcej o tym, jak to zrobić, zobacz [Grupy zabezpieczeń sieci .](../../virtual-network/security-overview.md)

Jeśli używasz usługi Azure File Sync lokalnie, można użyć interfejsu API tagu usługi, aby uzyskać określone zakresy adresów IP dla listy dozwolonych zapory. Istnieją dwie metody uzyskiwania tych informacji:

- Bieżąca lista zakresów adresów IP dla wszystkich tagów usług platformy Azure obsługujących usługi są publikowane co tydzień w Centrum pobierania Firmy Microsoft w formie dokumentu JSON. Każda chmura platformy Azure ma swój własny dokument JSON z zakresami adresów IP odpowiednimi dla tej chmury:
    - [Publiczna platforma Azure](https://www.microsoft.com/download/details.aspx?id=56519)
    - [Azure rząd USA](https://www.microsoft.com/download/details.aspx?id=57063)
    - [Platforma Azure w Chinach](https://www.microsoft.com/download/details.aspx?id=57062)
    - [Azure (Niemcy)](https://www.microsoft.com/download/details.aspx?id=57064)
- Interfejs API odnajdowania tagów usługi (wersja zapoznawcza) umożliwia programowe pobieranie bieżącej listy tagów usługi. W wersji zapoznawczej interfejs API odnajdowania tagów usługi może zwracać informacje, które są mniej aktualne niż informacje zwrócone z dokumentów JSON opublikowanych w Centrum pobierania Microsoft. Powierzchni interfejsu API można używać na podstawie preferencji automatyzacji:
    - [INTERFEJS API ODPOCZYNKU](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
    - [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag)
    - [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/network#az-network-list-service-tags)

Ponieważ interfejs API odnajdowania tagów usługi nie jest aktualizowany tak często, jak dokumenty JSON opublikowane w Centrum pobierania Firmy Microsoft, zalecamy użycie dokumentu JSON do zaktualizowania lokalnej listy dozwolonych zapory. Można to zrobić w następujący sposób:

```PowerShell
# The specific region to get the IP address ranges for. Replace westus2 with the desired region code 
# from Get-AzLocation.
$region = "westus2"

# The service tag for Azure File Sync. Do not change unless you're adapting this
# script for another service.
$serviceTag = "StorageSyncService"

# Download date is the string matching the JSON document on the Download Center. 
$possibleDownloadDates = 0..7 | `
    ForEach-Object { [System.DateTime]::Now.AddDays($_ * -1).ToString("yyyyMMdd") }

# Verify the provided region
$validRegions = Get-AzLocation | `
    Where-Object { $_.Providers -contains "Microsoft.StorageSync" } | `
    Select-Object -ExpandProperty Location

if ($validRegions -notcontains $region) {
    Write-Error `
            -Message "The specified region $region is not available. Either Azure File Sync is not deployed there or the region does not exist." `
            -ErrorAction Stop
}

# Get the Azure cloud. This should automatically based on the context of 
# your Az PowerShell login, however if you manually need to populate, you can find
# the correct values using Get-AzEnvironment.
$azureCloud = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty Name

# Build the download URI
$downloadUris = @()
switch($azureCloud) {
    "AzureCloud" { 
        $downloadUris = $possibleDownloadDates | ForEach-Object {  
            "https://download.microsoft.com/download/7/1/D/71D86715-5596-4529-9B13-DA13A5DE5B63/ServiceTags_Public_$_.json"
        }
    }

    "AzureUSGovernment" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/6/4/D/64DB03BF-895B-4173-A8B1-BA4AD5D4DF22/ServiceTags_AzureGovernment_$_.json"
        }
    }

    "AzureChinaCloud" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/9/D/0/9D03B7E2-4B80-4BF3-9B91-DA8C7D3EE9F9/ServiceTags_China_$_.json"
        }
    }

    "AzureGermanCloud" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/0/7/6/076274AB-4B0B-4246-A422-4BAF1E03F974/ServiceTags_AzureGermany_$_.json"
        }
    }

    default {
        Write-Error -Message "Unrecognized Azure Cloud: $_" -ErrorAction Stop
    }
}

# Find most recent file
$found = $false 
foreach($downloadUri in $downloadUris) {
    try { $response = Invoke-WebRequest -Uri $downloadUri -UseBasicParsing } catch { }
    if ($response.StatusCode -eq 200) {
        $found = $true
        break
    }
}

if ($found) {
    # Get the raw JSON 
    $content = [System.Text.Encoding]::UTF8.GetString($response.Content)

    # Parse the JSON
    $serviceTags = ConvertFrom-Json -InputObject $content -Depth 100

    # Get the specific $ipAddressRanges
    $ipAddressRanges = $serviceTags | `
        Select-Object -ExpandProperty values | `
        Where-Object { $_.id -eq "$serviceTag.$region" } | `
        Select-Object -ExpandProperty properties | `
        Select-Object -ExpandProperty addressPrefixes
} else {
    # If the file cannot be found, that means there hasn't been an update in
    # more than a week. Please verify the download URIs are still accurate
    # by checking https://docs.microsoft.com/azure/virtual-network/service-tags-overview
    Write-Verbose -Message "JSON service tag file not found."
    return
}
```

Następnie można użyć zakresów adresów IP, `$ipAddressRanges` aby zaktualizować zaporę. Informacje na temat aktualizacji zapory można znaleźć w witrynie sieci Web zapory/urządzenia sieciowego.

## <a name="test-network-connectivity-to-service-endpoints"></a>Testowanie łączności sieciowej z punktami końcowymi usługi
Po zarejestrowaniu serwera w usłudze Azure File Sync polecenie cmdlet Test-StorageSyncNetworkConnectivity i ServerRegistration.exe może służyć do testowania komunikacji ze wszystkimi punktami końcowymi (adresami URL) specyficznymi dla tego serwera. To polecenie cmdlet może pomóc w rozwiązywaniu problemów, gdy niekompletna komunikacja uniemożliwia serwerowi pełną pracę z synchronizacją plików platformy Azure i może służyć do precyzyjnego dostrajania konfiguracji serwera proxy i zapory.

Aby uruchomić test łączności sieciowej, należy zainstalować agenta usługi Azure File Sync w wersji 9.1 lub nowszej i uruchomić następujące polecenia programu PowerShell:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Test-StorageSyncNetworkConnectivity
```

## <a name="summary-and-risk-limitation"></a>Podsumowanie i ograniczenie ryzyka
Listy wcześniej w tym dokumencie zawierają adresy URL, z których obecnie komunikuje się usługa Azure File Sync. Zapory muszą mieć możliwość zezwalania na ruch wychodzący do tych domen. Firma Microsoft dokłada wszelkich starań, aby ta lista była aktualizowana.

Konfigurowanie reguł zapory ograniczającej domeny może być środkiem służącym poprawie bezpieczeństwa. Jeśli te konfiguracje zapory są używane, należy pamiętać, że adresy URL zostaną dodane, a nawet mogą ulec zmianie w czasie. Okresowo sprawdzaj ten artykuł.

## <a name="next-steps"></a>Następne kroki
- [Planowanie wdrażania usługi Azure File Sync](storage-sync-files-planning.md)
- [Wdrażanie usługi Azure File Sync](storage-sync-files-deployment-guide.md)
- [Monitorowanie usługi Azure File Sync](storage-sync-files-monitoring.md)
