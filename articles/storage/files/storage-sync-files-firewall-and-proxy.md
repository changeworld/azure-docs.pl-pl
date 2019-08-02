---
title: Azure File Sync lokalne ustawienia zapory i serwera proxy | Microsoft Docs
description: Azure File Sync konfigurację sieci lokalnej
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 69f4c767b9fc1da90db021ffb3eb8704983ca69b
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699304"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Ustawienia serwera proxy i zapory usługi Azure File Sync
Azure File Sync nawiązuje połączenie z serwerami lokalnymi w celu Azure Files, włączając synchronizację z obsługą wielolokacją i funkcjami obsługi warstw w chmurze. W związku z tym serwer lokalny musi być połączony z Internetem. Administrator IT musi zdecydować najlepszą ścieżkę serwera, aby uzyskać dostęp do usług Azure Cloud Services.

W tym artykule zawarto szczegółowe informacje o określonych wymaganiach i opcjach dostępnych w celu pomyślnego i bezpiecznego połączenia Twojego serwera z Azure File Sync.

## <a name="overview"></a>Przegląd
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

      Uwaga: Po zatrzymaniu usługa Agent synchronizacji magazynu (filesyncsvc) zostanie uruchomiona ponownie.

## <a name="firewall"></a>Zapora
Jak wspomniano w poprzedniej sekcji, port 443 musi być otwarty dla ruchu wychodzącego. W oparciu o zasady w centrum danych, rozgałęzieniu lub regionie, może być konieczne lub wymagane dalsze ograniczenie ruchu przez ten port do określonych domen.

W poniższej tabeli opisano wymagane domeny do komunikacji:

| Usługa | Punkt końcowy chmury publicznej | Azure Government punkt końcowy | Użycie |
|---------|----------------|---------------|------------------------------|
| **Azure Resource Manager** | https://management.azure.com | https://management.usgovcloudapi.net | Każde wywołanie użytkownika (na przykład program PowerShell) przechodzi do/za pomocą tego adresu URL, łącznie z początkowym wywołaniem rejestracji serwera. |
| **Azure Active Directory** | https://login.windows.net | https://login.microsoftonline.us | Wywołania Azure Resource Manager muszą być wykonywane przez uwierzytelnionego użytkownika. Aby pomyślnie, ten adres URL jest używany do uwierzytelniania użytkowników. |
| **Azure Active Directory** | https://graph.windows.net/ | https://graph.windows.net/ | W ramach wdrażania Azure File Sync zostanie utworzona jednostka usługi w Azure Active Directory subskrypcji. Ten adres URL jest używany dla tego programu. Ten podmiot zabezpieczeń jest używany do delegowania minimalnego zestawu praw do usługi Azure File Sync. Użytkownik wykonujący początkową konfigurację Azure File Sync musi być uwierzytelnionym użytkownikiem z uprawnieniami właściciela subskrypcji. |
| **Azure Storage** | &ast;.core.windows.net | &ast;.core.usgovcloudapi.net | Gdy serwer pobiera plik, serwer przeprowadzi bardziej wydajne przenoszenie danych podczas rozmowy bezpośrednio z udziałem plików platformy Azure na koncie magazynu. Serwer ma klucz sygnatury dostępu współdzielonego, który zezwala tylko na dostęp do udziału plików. |
| **Azure File Sync** | &ast;.one.microsoft.com | &ast;. afs.azure.us | Po początkowej rejestracji serwera serwer otrzymuje regionalny adres URL dla wystąpienia usługi Azure File Sync w tym regionie. Serwer może używać adresu URL do bezpośredniego i wydajnego komunikowania się z wystąpieniem obsługującym jego synchronizację. |
| **Infrastruktura PKI firmy Microsoft** | `https://www.microsoft.com/pki/mscorp`<br /><http://ocsp.msocsp.com> | `https://www.microsoft.com/pki/mscorp`<br /><http://ocsp.msocsp.com> | Po zainstalowaniu agenta Azure File Sync, adres URL infrastruktury PKI jest używany do pobierania certyfikatów pośrednich wymaganych do komunikowania się z usługą Azure File Sync i udziałem plików platformy Azure. Adres URL protokołu OCSP służy do sprawdzania stanu certyfikatu. |

> [!Important]
> W przypadku zezwolenia na &ast;ruch do. one.Microsoft.com ruch do więcej niż tylko usługi synchronizacji jest możliwy z serwera. W poddomenach poddomen jest dostępnych wiele usług firmy Microsoft.

Jeśli &ast;one.Microsoft.com jest zbyt szeroki, można ograniczyć komunikację serwera, zezwalając na komunikację tylko z jawnymi wystąpieniami regionalnymi usługi synchronizacji Azure Files. Jakie wystąpienia są zależne od regionu usługi synchronizacji magazynu wdrożonej i zarejestrowanej na serwerze. Ten region jest nazywany "podstawowym adresem URL punktu końcowego" w poniższej tabeli.

Ze względu na ciągłość działania i odzyskiwanie po awarii (BCDR), możesz określić udziały plików platformy Azure na koncie magazynu globalnie nadmiarowy (GRS). W takim przypadku udziały plików platformy Azure zostaną przełączone w tryb failover do sparowanego regionu w przypadku długotrwałej awarii regionalnej. Azure File Sync używa tych samych regionalnych par jako magazynu. W przypadku korzystania z kont magazynu GRS należy włączyć dodatkowe adresy URL, aby umożliwić serwerowi komunikowanie się z sparowanym regionem Azure File Sync. Poniższa tabela wywołuje ten "sparowany region". Ponadto istnieje adres URL profilu Menedżera ruchu, który musi zostać włączony. Dzięki temu ruch sieciowy może być bezproblemowo kierowany do sparowanego regionu w przypadku przełączenia w tryb failover i nosi nazwę "adres URL odnajdywania" w poniższej tabeli.

| Chmura  | Region | Podstawowy adres URL punktu końcowego | Region sparowany | Adres URL odnajdywania |
|--------|--------|----------------------|---------------|---------------|
| Public |Australia Wschodnia | https:\//Kailani-Aue.one.Microsoft.com | Australia Południowo-Wschodnia | https:\//TM-Kailani-Aue.one.Microsoft.com |
| Public |Australia Południowo-Wschodnia | https:\//Kailani-aus.one.Microsoft.com | Australia Wschodnia | https:\//TM-Kailani-aus.one.Microsoft.com |
| Public | Brazylia Południowa | https:\//brazilsouth01.AFS.Azure.NET | Środkowo-południowe stany USA | https:\//TM-brazilsouth01.AFS.Azure.NET |
| Public | Kanada Środkowa | https:\//Kailani-CAC.one.Microsoft.com | Kanada Wschodnia | https:\//TM-Kailani-CAC.one.Microsoft.com |
| Public | Kanada Wschodnia | https:\//Kailani-CAE.one.Microsoft.com | Kanada Środkowa | https:\//TM-Kailani.CAE.one.Microsoft.com |
| Public | Indie Środkowe | https:\//Kailani-CIN.one.Microsoft.com | Indie Południowe | https:\//TM-Kailani-CIN.one.Microsoft.com |
| Public | Środkowe stany USA | https:\//Kailani-CUS.one.Microsoft.com | Wschodnie stany USA 2 | https:\//TM-Kailani-CUS.one.Microsoft.com |
| Public | Azja Wschodnia | https:\//kailani11.one.Microsoft.com | Azja Południowo-Wschodnia | https:\//TM-kailani11.one.Microsoft.com |
| Public | East US | https:\//kailani1.one.Microsoft.com | Zachodnie stany USA | https:\//TM-kailani1.one.Microsoft.com |
| Public | Wschodnie stany USA 2 | https:\//Kailani-ESS.one.Microsoft.com | Środkowe stany USA | https:\//TM-Kailani-ESS.one.Microsoft.com |
| Public | Japonia Wschodnia | https:\//japaneast01.afs.azure.net | Japonia Zachodnia | https:\//TM-japaneast01.AFS.Azure.NET |
| Public | Japonia Zachodnia | https:\//japanwest01.afs.azure.net | Japonia Wschodnia | https:\//TM-japanwest01.AFS.Azure.NET |
| Public | Korea Środkowa | https:\//koreacentral01.afs.azure.net/ | Korea Południowa | https:\//tm-koreacentral01.afs.azure.net/ |
| Public | Korea Południowa | https:\//koreasouth01.afs.azure.net/ | Korea Środkowa | https:\//tm-koreasouth01.afs.azure.net/ |
| Public | Środkowo-północne stany USA | https:\//northcentralus01.AFS.Azure.NET | Środkowo-południowe stany USA | https:\//TM-northcentralus01.AFS.Azure.NET |
| Public | Europa Północna | https:\//kailani7.one.Microsoft.com | Europa Zachodnia | https:\//TM-kailani7.one.Microsoft.com |
| Public | Środkowo-południowe stany USA | https:\//southcentralus01.AFS.Azure.NET | Środkowo-północne stany USA | https:\//TM-southcentralus01.AFS.Azure.NET |
| Public | Indie Południowe | https:\//Kailani-Sin.one.Microsoft.com | Indie Środkowe | https:\//TM-Kailani-Sin.one.Microsoft.com |
| Public | Azja Południowo-Wschodnia | https:\//kailani10.one.Microsoft.com | Azja Wschodnia | https:\//TM-kailani10.one.Microsoft.com |
| Public | Południowe Zjednoczone Królestwo | https:\//Kailani-UKS.one.Microsoft.com | Zachodnie Zjednoczone Królestwo | https:\//TM-Kailani-UKS.one.Microsoft.com |
| Public | Zachodnie Zjednoczone Królestwo | https:\//Kailani-UKW.one.Microsoft.com | Południowe Zjednoczone Królestwo | https:\//TM-Kailani-UKW.one.Microsoft.com |
| Public | Środkowo-zachodnie stany USA | https:\//westcentralus01.afs.azure.net | Zachodnie stany USA 2 | https:\//tm-westcentralus01.afs.azure.net |
| Public | Europa Zachodnia | https:\//kailani6.one.Microsoft.com | Europa Północna | https:\//TM-kailani6.one.Microsoft.com |
| Public | Zachodnie stany USA | https:\//kailani.one.microsoft.com | East US | https:\//TM-Kailani.one.Microsoft.com |
| Public | Zachodnie stany USA 2 | https:\//westus201.afs.azure.net | Środkowo-zachodnie stany USA | https:\//tm-westus201.afs.azure.net |
| Instytucje rządowe | Administracja USA — Arizona | https:\//usgovarizona01.afs.azure.us | Administracja USA — Teksas | https:\//TM-usgovarizona01.AFS.Azure.us |
| Instytucje rządowe | Administracja USA — Teksas | https:\//usgovtexas01.AFS.Azure.us | Administracja USA — Arizona | https:\//TM-usgovtexas01.AFS.Azure.us |

- W przypadku korzystania z kont magazynu lokalnie nadmiarowego (LRS) lub strefy nadmiarowe (ZRS) należy włączyć tylko adres URL wymieniony w obszarze "podstawowy adres URL punktu końcowego".

- Jeśli używasz kont magazynu GRS (Globally nadmiarowe), Włącz trzy adresy URL.

**Przykład:** Należy wdrożyć usługę synchronizacji magazynu w `"West US"` systemie i zarejestrować serwer. Adresy URL umożliwiające serwerowi komunikowanie się w tym przypadku są następujące:

> - https:\//Kailani.one.Microsoft.com (podstawowy punkt końcowy: Zachodnie stany USA)
> - https:\//kailani1.one.Microsoft.com (sparowany region w trybie failover: Wschodnie stany USA)
> - https:\//TM-Kailani.one.Microsoft.com (adres URL odnajdywania regionu podstawowego)

## <a name="summary-and-risk-limitation"></a>Podsumowanie i ograniczenie ryzyka
Listy znajdujące się wcześniej w tym dokumencie zawierają adresy URL, Azure File Sync obecnie komunikują się z usługą. Zapory muszą mieć możliwość zezwalania na ruch wychodzący do tych domen. Firma Microsoft dokłada starań, aby zachować tę listę.

Skonfigurowanie ograniczania dostępu do domeny reguły zapory może być miarą w celu zwiększenia bezpieczeństwa. Jeśli są używane te konfiguracje zapory, należy pamiętać, że adresy URL zostaną dodane i nawet mogą ulec zmianie w czasie. Sprawdzaj okresowo ten artykuł.

## <a name="next-steps"></a>Następne kroki
- [Planowanie wdrożenia usługi Azure File Sync](storage-sync-files-planning.md)
- [Wdrażanie usługi Azure File Sync](storage-sync-files-deployment-guide.md)
- [Monitorowanie usługi Azure File Sync](storage-sync-files-monitoring.md)
