---
title: Azure File Sync lokalne ustawienia zapory i serwera proxy | Microsoft Docs
description: Azure File Sync konfigurację sieci lokalnej
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 93681813c12f0df99909c849e57153e7a64c78fb
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299315"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Ustawienia serwera proxy i zapory usługi Azure File Sync
Azure File Sync nawiązuje połączenie z serwerami lokalnymi w celu Azure Files, włączając synchronizację z obsługą wielolokacją i funkcjami obsługi warstw w chmurze. W związku z tym serwer lokalny musi być połączony z Internetem. Administrator IT musi zdecydować najlepszą ścieżkę serwera, aby uzyskać dostęp do usług Azure Cloud Services.

W tym artykule zawarto szczegółowe informacje o określonych wymaganiach i opcjach dostępnych w celu pomyślnego i bezpiecznego połączenia Twojego serwera z Azure File Sync.

## <a name="overview"></a>Omówienie
Azure File Sync pełni rolę usługi aranżacji między serwerem systemu Windows, udziałem plików platformy Azure i kilkoma innymi usługami platformy Azure, aby synchronizować dane zgodnie z opisem w grupie synchronizacji. Aby program Azure File Sync działał prawidłowo, należy skonfigurować serwery do komunikowania się z następującymi usługami platformy Azure:

- Azure Storage
- Azure File Sync
- Azure Resource Manager
- Usługi uwierzytelniania

> [!Note]  
> Agent Azure File Sync w systemie Windows Server inicjuje wszystkie żądania do usług w chmurze, które w wyniku uwzględniają tylko ruch wychodzący z perspektywy zapory. <br /> Żadna usługa platformy Azure nie inicjuje połączenia z agentem Azure File Sync.

## <a name="ports"></a>Porty
Azure File Sync przenosi dane plików i metadane wyłącznie za pośrednictwem protokołu HTTPS i wymaga otwartego portu 443.
W wyniku tego cały ruch jest szyfrowany.

## <a name="networks-and-special-connections-to-azure"></a>Sieci i specjalne połączenia z platformą Azure
Agent Azure File Sync nie ma wymagań dotyczących specjalnych kanałów, takich jak [ExpressRoute](../../expressroute/expressroute-introduction.md)itp., na platformę Azure.

Azure File Sync będzie działała za pośrednictwem dowolnych środków, które umożliwiają dostęp do platformy Azure, automatyczne dostosowanie do różnych właściwości sieci, takich jak przepustowość, opóźnienie, a także oferowanie kontroli administracyjnej w celu dostosowania. W tej chwili nie wszystkie funkcje są dostępne. Jeśli chcesz skonfigurować określone zachowanie, daj nam znać za pośrednictwem [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage?category_id=180670).

## <a name="proxy"></a>Serwer proxy
Azure File Sync obsługuje ustawienia serwera proxy specyficzne dla aplikacji i komputera.

**Ustawienia serwera proxy specyficzne dla aplikacji** umożliwiają konfigurowanie serwera proxy przeznaczonego dla Azure File Sync ruchu sieciowego. Ustawienia serwera proxy specyficzne dla aplikacji są obsługiwane przez agenta w wersji 4.0.1.0 lub nowszej i można je skonfigurować podczas instalacji agenta lub przy użyciu polecenia cmdlet Set-StorageSyncProxyConfiguration programu PowerShell.

Polecenia programu PowerShell służące do konfigurowania ustawień serwera proxy specyficznych dla aplikacji:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Set-StorageSyncProxyConfiguration -Address <url> -Port <port number> -ProxyCredential <credentials>
```
**Ustawienia serwera proxy dla maszyn** są niewidoczne dla agenta Azure File Sync, ponieważ cały ruch serwera jest kierowany przez serwer proxy.

Aby skonfigurować ustawienia serwera proxy dla całej maszyny, wykonaj następujące czynności: 

1. Konfigurowanie ustawień serwera proxy dla aplikacji platformy .NET 

   - Edytuj te dwa pliki:  
     C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config  
     C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config

   - Dodaj sekcję < System. net > w plikach Machine. config (poniżej sekcji < System. serviceModel >).  Zmień 127.0.01:8888 na adres IP i port serwera proxy. 
     ```
      <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
          <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
      </system.net>
     ```

2. Ustawianie ustawień serwera proxy WinHTTP 

   - Uruchom następujące polecenie w wierszu polecenia z podwyższonym poziomem uprawnień lub programie PowerShell, aby wyświetlić istniejące ustawienie serwera proxy:   

     netsh WinHTTP show proxy

   - Uruchom następujące polecenie w wierszu polecenia z podwyższonym poziomem uprawnień lub w programie PowerShell, aby ustawić ustawienie serwera proxy (Zmień 127.0.01:8888 na adres IP i port serwera proxy):  

     netsh WinHTTP Set proxy 127.0.0.1:8888

3. Uruchom ponownie usługę agenta synchronizacji magazynu, uruchamiając następujące polecenie w wierszu polecenia z podwyższonym poziomem uprawnień lub programie PowerShell: 

      polecenie net stop filesyncsvc

      Uwaga: usługa agenta synchronizacji magazynu (filesyncsvc) zostanie uruchomiona ponownie po zatrzymaniu.

## <a name="firewall"></a>Zapora
Jak wspomniano w poprzedniej sekcji, port 443 musi być otwarty dla ruchu wychodzącego. W oparciu o zasady w centrum danych, rozgałęzieniu lub regionie, może być konieczne lub wymagane dalsze ograniczenie ruchu przez ten port do określonych domen.

W poniższej tabeli opisano wymagane domeny do komunikacji:

| NDES | Punkt końcowy chmury publicznej | Azure Government punkt końcowy | Sposób użycia |
|---------|----------------|---------------|------------------------------|
| **Azure Resource Manager** | https://management.azure.com | https://management.usgovcloudapi.net | Każde wywołanie użytkownika (na przykład program PowerShell) przechodzi do/za pomocą tego adresu URL, łącznie z początkowym wywołaniem rejestracji serwera. |
| **Azure Active Directory** | https://login.windows.net<br>https://login.microsoftonline.com | https://login.microsoftonline.us | Wywołania Azure Resource Manager muszą być wykonywane przez uwierzytelnionego użytkownika. Aby pomyślnie, ten adres URL jest używany do uwierzytelniania użytkowników. |
| **Azure Active Directory** | https://graph.windows.net/ | https://graph.windows.net/ | W ramach wdrażania Azure File Sync zostanie utworzona jednostka usługi w Azure Active Directory subskrypcji. Ten adres URL jest używany dla tego programu. Ten podmiot zabezpieczeń jest używany do delegowania minimalnego zestawu praw do usługi Azure File Sync. Użytkownik wykonujący początkową konfigurację Azure File Sync musi być uwierzytelnionym użytkownikiem z uprawnieniami właściciela subskrypcji. |
| **Azure Storage** | &ast;. core.windows.net | &ast;. core.usgovcloudapi.net | Gdy serwer pobiera plik, serwer przeprowadzi bardziej wydajne przenoszenie danych podczas rozmowy bezpośrednio z udziałem plików platformy Azure na koncie magazynu. Serwer ma klucz sygnatury dostępu współdzielonego, który zezwala tylko na dostęp do udziału plików. |
| **Azure File Sync** | &ast;. one.microsoft.com<br>&ast;. afs.azure.net | &ast;. afs.azure.us | Po początkowej rejestracji serwera serwer otrzymuje regionalny adres URL dla wystąpienia usługi Azure File Sync w tym regionie. Serwer może używać adresu URL do bezpośredniego i wydajnego komunikowania się z wystąpieniem obsługującym jego synchronizację. |
| **Infrastruktura PKI firmy Microsoft** | https://www.microsoft.com/pki/mscorp/cps<br><http://ocsp.msocsp.com> | https://www.microsoft.com/pki/mscorp/cps<br><http://ocsp.msocsp.com> | Po zainstalowaniu agenta Azure File Sync, adres URL infrastruktury PKI jest używany do pobierania certyfikatów pośrednich wymaganych do komunikowania się z usługą Azure File Sync i udziałem plików platformy Azure. Adres URL protokołu OCSP służy do sprawdzania stanu certyfikatu. |

> [!Important]
> Gdy zezwalasz na ruch do &ast;. one.microsoft.com, możliwe jest, że ruch do więcej niż tylko usługi synchronizacji jest możliwy z serwera. W poddomenach poddomen jest dostępnych wiele usług firmy Microsoft.

Jeśli &ast;. one.microsoft.com jest zbyt szeroki, można ograniczyć komunikację serwera, zezwalając na komunikację tylko z jawnymi wystąpieniami regionalnymi usługi synchronizacji Azure Files. Jakie wystąpienia są zależne od regionu usługi synchronizacji magazynu wdrożonej i zarejestrowanej na serwerze. Ten region jest nazywany "podstawowym adresem URL punktu końcowego" w poniższej tabeli.

Ze względu na ciągłość działania i odzyskiwanie po awarii (BCDR), możesz określić udziały plików platformy Azure na koncie magazynu globalnie nadmiarowy (GRS). W takim przypadku udziały plików platformy Azure zostaną przełączone w tryb failover do sparowanego regionu w przypadku długotrwałej awarii regionalnej. Azure File Sync używa tych samych regionalnych par jako magazynu. W przypadku korzystania z kont magazynu GRS należy włączyć dodatkowe adresy URL, aby umożliwić serwerowi komunikowanie się z sparowanym regionem Azure File Sync. Poniższa tabela wywołuje ten "sparowany region". Ponadto istnieje adres URL profilu Menedżera ruchu, który musi zostać włączony. Dzięki temu ruch sieciowy może być bezproblemowo kierowany do sparowanego regionu w przypadku przełączenia w tryb failover i nosi nazwę "adres URL odnajdywania" w poniższej tabeli.

| Chmura  | Region | Podstawowy adres URL punktu końcowego | Region sparowany | Adres URL odnajdywania |
|--------|--------|----------------------|---------------|---------------|
| Publiczne |Australia Wschodnia | https:\//kailani-aue.one.microsoft.com | Australia Południowo-Wschodnia | https:\//tm-kailani-aue.one.microsoft.com |
| Publiczne |Australia Południowo-Wschodnia | https:\//kailani-aus.one.microsoft.com | Australia Wschodnia | https:\//tm-kailani-aus.one.microsoft.com |
| Publiczne | Brazylia Południowa | https:\//brazilsouth01.afs.azure.net | Południowo-środkowe stany USA | https:\//tm-brazilsouth01.afs.azure.net |
| Publiczne | Kanada Środkowa | https:\//kailani-cac.one.microsoft.com | Kanada Wschodnia | https:\//tm-kailani-cac.one.microsoft.com |
| Publiczne | Kanada Wschodnia | https:\//kailani-cae.one.microsoft.com | Kanada Środkowa | https:\//tm-kailani.cae.one.microsoft.com |
| Publiczne | Indie Środkowe | https:\//kailani-cin.one.microsoft.com | Indie Południowe | https:\//tm-kailani-cin.one.microsoft.com |
| Publiczne | Środkowe stany USA | https:\//kailani-cus.one.microsoft.com | Wschodnie stany USA 2 | https:\//tm-kailani-cus.one.microsoft.com |
| Publiczne | Azja Wschodnia | https:\//kailani11.one.microsoft.com | Azja Południowo-Wschodnia | https:\//tm-kailani11.one.microsoft.com |
| Publiczne | Wschodnie stany USA | https:\//kailani1.one.microsoft.com | Zachodnie stany USA | https:\//tm-kailani1.one.microsoft.com |
| Publiczne | Wschodnie stany USA 2 | https:\//kailani-ess.one.microsoft.com | Środkowe stany USA | https:\//tm-kailani-ess.one.microsoft.com |
| Publiczne | Japonia Wschodnia | https:\//japaneast01.afs.azure.net | Japonia Zachodnia | https:\//tm-japaneast01.afs.azure.net |
| Publiczne | Japonia Zachodnia | https:\//japanwest01.afs.azure.net | Japonia Wschodnia | https:\//tm-japanwest01.afs.azure.net |
| Publiczne | Korea Środkowa | https:\//koreacentral01.afs.azure.net/ | Korea Południowa | https:\//tm-koreacentral01.afs.azure.net/ |
| Publiczne | Korea Południowa | https:\//koreasouth01.afs.azure.net/ | Korea Środkowa | https:\//tm-koreasouth01.afs.azure.net/ |
| Publiczne | Północno-środkowe stany USA | https:\//northcentralus01.afs.azure.net | Południowo-środkowe stany USA | https:\//tm-northcentralus01.afs.azure.net |
| Publiczne | Europa Północna | https:\//kailani7.one.microsoft.com | Europa Zachodnia | https:\//tm-kailani7.one.microsoft.com |
| Publiczne | Południowo-środkowe stany USA | https:\//southcentralus01.afs.azure.net | Północno-środkowe stany USA | https:\//tm-southcentralus01.afs.azure.net |
| Publiczne | Indie Południowe | https:\//kailani-sin.one.microsoft.com | Indie Środkowe | https:\//tm-kailani-sin.one.microsoft.com |
| Publiczne | Azja Południowo-Wschodnia | https:\//kailani10.one.microsoft.com | Azja Wschodnia | https:\//tm-kailani10.one.microsoft.com |
| Publiczne | Południowe Zjednoczone Królestwo | https:\//kailani-uks.one.microsoft.com | Zachodnie Zjednoczone Królestwo | https:\//tm-kailani-uks.one.microsoft.com |
| Publiczne | Zachodnie Zjednoczone Królestwo | https:\//kailani-ukw.one.microsoft.com | Południowe Zjednoczone Królestwo | https:\//tm-kailani-ukw.one.microsoft.com |
| Publiczne | Zachodnio-środkowe stany USA | https:\//westcentralus01.afs.azure.net | Zachodnie stany USA 2 | https:\//tm-westcentralus01.afs.azure.net |
| Publiczne | Europa Zachodnia | https:\//kailani6.one.microsoft.com | Europa Północna | https:\//tm-kailani6.one.microsoft.com |
| Publiczne | Zachodnie stany USA | https:\//kailani.one.microsoft.com | Wschodnie stany USA | https:\//tm-kailani.one.microsoft.com |
| Publiczne | Zachodnie stany USA 2 | https:\//westus201.afs.azure.net | Zachodnio-środkowe stany USA | https:\//tm-westus201.afs.azure.net |
| Instytucje rządowe | US Gov Arizona | https:\//usgovarizona01.afs.azure.us | US Gov Teksas | https:\//tm-usgovarizona01.afs.azure.us |
| Instytucje rządowe | US Gov Teksas | https:\//usgovtexas01.afs.azure.us | US Gov Arizona | https:\//tm-usgovtexas01.afs.azure.us |

- W przypadku korzystania z kont magazynu lokalnie nadmiarowego (LRS) lub strefy nadmiarowe (ZRS) należy włączyć tylko adres URL wymieniony w obszarze "podstawowy adres URL punktu końcowego".

- Jeśli używasz kont magazynu GRS (Globally nadmiarowe), Włącz trzy adresy URL.

**Przykład:** Należy wdrożyć usługę synchronizacji magazynu w `"West US"` i zarejestrować swój serwer. Adresy URL umożliwiające serwerowi komunikowanie się w tym przypadku są następujące:

> - https:\//kailani.one.microsoft.com (podstawowy punkt końcowy: zachodnie stany USA)
> - https:\//kailani1.one.microsoft.com (sparowany region w trybie failover: Wschodnie stany USA)
> - https:\//tm-kailani.one.microsoft.com (adres URL odnajdywania regionu podstawowego)

### <a name="allow-list-for-azure-file-sync-ip-addresses"></a>Lista dozwolonych adresów IP Azure File Sync
Jeśli Zapora lokalna wymaga dodania określonych adresów IP do listy dozwolonych, aby połączyć się z Azure File Sync, można dodać następujące zakresy adresów IP na podstawie regionów, z którymi nawiązujesz połączenie.

| Region | Zakresy adresów IP |
|--------|-------------------|
| Środkowe stany USA | 52.176.149.179/32, 20.37.157.80/29 |
| Wschodnie stany USA 2 | 40.123.47.110/32, 20.41.5.144/29 |
| Wschodnie stany USA | 104.41.148.238/32, 20.42.4.248/29 |
| Północno-środkowe stany USA | 65.52.62.167/32, 40.80.188.24/29 |
| Południowo-środkowe stany USA | 104.210.219.252/32, 13.73.248.112/29 |
| Zachodnie stany USA 2 | 52.183.27.204/32, 20.42.131.224/29 |
| Zachodnio-środkowe stany USA | 52.161.25.233/32, 52.150.139.104/29 |
| Zachodnie stany USA | 40.112.150.67/32, 40.82.253.192/29 |
| Kanada Środkowa | 52.228.42.41/32, 52.228.81.248/29 |
| Kanada Wschodnia | 52.235.36.119/32, 40.89.17.232/29 |
| Brazylia Południowa | 191.237.253.115/32, 191.235.225.216/29 |
| Europa Północna | 40.113.94.67/32, 20.38.85.152/29 |
| Europa Zachodnia | 104.40.191.8/32, 20.50.1.0/29 |
| Francja Środkowa | 52.143.166.54/32, 20.43.42.8/29 |
| Francja Południowa | 52.136.131.99/32, 51.105.88.248/29 |
| Południowe Zjednoczone Królestwo | 51.140.67.72/32, 51.104.25.224/29 |
| Zachodnie Zjednoczone Królestwo | 51.140.202.34/32, 51.137.161.240/29 |
| Szwajcaria Północna | 51.107.48.224/29 |
| Szwajcaria Zachodnia | 51.107.144.216/29 |
| Norwegia Zachodnia | 51.120.224.216/29 |
| Norwegia Wschodnia | 51.120.40.224/29 |
| Azja Wschodnia | 23.102.225.54/32, 20.189.108.56/29 |
| Azja Południowo-Wschodnia | 13.76.81.46/32, 20.43.131.40/29 |
| Australia Środkowa | 20.37.224.216/29 |
| Australia Środkowa 2 | 20.36.120.216/29 |
| Australia Wschodnia | 13.75.153.240/32, 20.37.195.96/29 |
| Australia Południowo-Wschodnia | 13.70.176.196/32, 20.42.227.128/29 |
| Indie Południowe | 104.211.231.18/32, 20.41.193.160/29 |
| Indie Zachodnie | 52.136.48.216/29 |
| Japonia Wschodnia | 104.41.161.113/32, 20.43.66.0/29 |
| Japonia Zachodnia | 23.100.106.151/32, 40.80.57.192/29 |
| Korea Środkowa | 52.231.67.75/32, 20.41.65.184/29 |
| Korea Południowa | 52.231.159.38/32, 40.80.169.176/29 |
| US DoD (region wschodni) | 20.140.72.152/29 |
| US Gov Arizona | 20.140.64.152/29 |
| US Gov Arizona | 52.244.75.224/32, 52.244.79.140/32 |
| US Gov Iowa | 52.244.79.140/32, 52.244.75.224/32 |
| US Gov Teksas | 52.238.166.107/32, 52.238.79.29/32 |
| US Gov Wirginia | 13.72.17.152/32, 52.227.153.92/32 |
| Północna Republika Południowej Afryki | 102.133.175.72/32 |
| Zachodnia Republika Południowej Afryki | 102.133.75.173/32, 102.133.56.128/29, 20.140.48.216/29 |
| Środkowy Zjednoczone Emiraty Arabskie | 20.45.71.151/32, 20.37.64.216/29, 20.140.48.216/29 |
| Północne Zjednoczone Emiraty Arabskie | 40.123.216.130/32, 20.38.136.224/29, 20.140.56.136/29 |

## <a name="test-network-connectivity-to-service-endpoints"></a>Testowanie łączności sieciowej z punktami końcowymi usługi
Po zarejestrowaniu serwera w usłudze Azure File Sync polecenie cmdlet Test-StorageSyncNetworkConnectivity i ServerRegistration. exe może służyć do testowania komunikacji ze wszystkimi punktami końcowymi (URL) specyficznymi dla tego serwera. To polecenie cmdlet może pomóc w rozwiązywaniu problemów w przypadku niekompletnej komunikacji uniemożliwia serwerowi pełną pracę z Azure File Sync i może służyć do precyzyjnego dostrajania konfiguracji serwera proxy i zapory.

Aby uruchomić test łączności sieciowej, Zainstaluj agenta Azure File Sync w wersji 9,1 lub nowszej i uruchom następujące polecenia programu PowerShell:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Test-StorageSyncNetworkConnectivity
```

## <a name="summary-and-risk-limitation"></a>Podsumowanie i ograniczenie ryzyka
Listy znajdujące się wcześniej w tym dokumencie zawierają adresy URL, Azure File Sync obecnie komunikują się z usługą. Zapory muszą mieć możliwość zezwalania na ruch wychodzący do tych domen. Firma Microsoft dokłada starań, aby zachować tę listę.

Skonfigurowanie ograniczania dostępu do domeny reguły zapory może być miarą w celu zwiększenia bezpieczeństwa. Jeśli są używane te konfiguracje zapory, należy pamiętać, że adresy URL zostaną dodane i nawet mogą ulec zmianie w czasie. Sprawdzaj okresowo ten artykuł.

## <a name="next-steps"></a>Następne kroki
- [Planowanie wdrażania usługi Azure File Sync](storage-sync-files-planning.md)
- [Wdrażanie usługi Azure File Sync](storage-sync-files-deployment-guide.md)
- [Monitorowanie usługi Azure File Sync](storage-sync-files-monitoring.md)
