---
title: Usługa Azure File Sync w środowisku lokalnym ustawień zapory i serwera proxy | Dokumentacja firmy Microsoft
description: Usługa Azure File Sync konfiguracji sieci lokalnej
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 06/24/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: fdbbea2d32762d2d4030ec3a10826595dadd371c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449829"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Ustawienia serwera proxy i zapory usługi Azure File Sync
Usługa Azure File Sync łączy swoje lokalne serwery do usługi pliki Azure, włączenie synchronizacji obejmujące wiele lokacji i obsługi warstw w funkcji w chmurze. W efekcie na serwerze lokalnym musi połączony z Internetem. Administrator IT musi określić najlepszą ścieżkę dla serwera uzyskać dostęp do usług Azure cloud services.

Ten artykuł będzie zapewniają wgląd w określonych wymagań i opcji umożliwiających pomyślnie i bezpiecznie połączyć swój serwer usługi Azure File Sync.

> [!Important]
> Usługa Azure File Sync nie obsługuje jeszcze zapory i sieci wirtualne dla konta magazynu.

## <a name="overview"></a>Przegląd
Usługa Azure File Sync działa jako usługa aranżacji między serwera z systemem Windows, udziału plików platformy Azure i kilka innych usług platformy Azure, na synchronizowanie danych, zgodnie z opisem w grupie synchronizacji. Dla usługi Azure File Sync działała prawidłowo należy skonfigurować serwery do komunikowania się z następujących usług platformy Azure:

- Azure Storage
- Azure File Sync
- Azure Resource Manager
- Usługi uwierzytelniania

> [!Note]  
> Agent usługi Azure File Sync w systemie Windows Server inicjuje wszystkie żądania do usługi, co powoduje tylko konieczności należy wziąć pod uwagę ruch wychodzący z punktu widzenia zapory w chmurze. <br /> Nie usługi platformy Azure inicjuje połączenie z agentem usługi Azure File Sync.

## <a name="ports"></a>Porty
Usługa Azure File Sync przenosi dane plików i metadanych wyłącznie za pośrednictwem protokołu HTTPS i wymaga portu 443, aby otworzyć w ruchu wychodzącego.
W rezultacie cały ruch jest szyfrowany.

## <a name="networks-and-special-connections-to-azure"></a>Sieci i specjalnych połączenia z platformą Azure
Agent usługi Azure File Sync nie ma wymagań dotyczących kanałów specjalnych, takich jak [ExpressRoute](../../expressroute/expressroute-introduction.md), itp. na platformie Azure.

Usługa Azure File Sync będą działać przez wszystkie środki, dzięki czemu zasięg na platformę Azure, automatycznie dostosowania do różnych charakterystyk sieci, takich jak przepustowość, opóźnienie, a także oferuje kontrolę administracyjną do precyzyjnego dostosowywania. Nie wszystkie funkcje są dostępne w tej chwili. Jeśli chcesz skonfigurować określone zachowanie, Daj nam znać za pośrednictwem [UserVoice plików Azure](https://feedback.azure.com/forums/217298-storage?category_id=180670).

## <a name="proxy"></a>Serwer proxy
Usługa Azure File Sync wspiera ustawienia serwera proxy dla aplikacji i komputera.

**Ustawienia specyficzne dla aplikacji serwera proxy** zezwala na konfigurację serwera proxy dla usługi Azure File Sync ruchu. Ustawienia specyficzne dla aplikacji serwera proxy są obsługiwane w wersji agenta 4.0.1.0 lub nowszej, a także można skonfigurować podczas instalacji agenta lub za pomocą polecenia cmdlet programu PowerShell Set-StorageSyncProxyConfiguration.

Polecenia programu PowerShell, aby skonfigurować ustawienia specyficzne dla aplikacji serwera proxy:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Set-StorageSyncProxyConfiguration -Address <url> -Port <port number> -ProxyCredential <credentials>
```
**Ustawienia serwera proxy dla komputera** są niewidoczne dla agenta usługi Azure File Sync, ponieważ cały ruch serwera odbywa się za pośrednictwem serwera proxy.

Aby skonfigurować ustawienia serwera proxy dla komputera, wykonaj następujące czynności: 

1. Skonfiguruj ustawienia serwera proxy dla aplikacji .NET 

   - Edytuj te dwa pliki:  
     C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config  
     C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config

   - Dodaj sekcję < system.net > Machine.config — pliki (poniżej w sekcji < system.serviceModel >).  Zmień 127.0.01:8888 na adres IP i port serwera proxy. 
     ```
      <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
          <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
      </system.net>
     ```

2. Ustaw ustawienia serwera proxy WinHTTP 

   - Uruchom następujące polecenie z wiersza polecenia z podwyższonym poziomem uprawnień lub programu PowerShell, aby wyświetlić istniejące ustawienia serwera proxy:   

     netsh winhttp show proxy

   - Uruchom następujące polecenie z wiersza polecenia z podwyższonym poziomem uprawnień lub programu PowerShell, aby ustawić ustawienia serwera proxy (Zmień 127.0.01:8888 na adres IP i port serwera proxy):  

     netsh winhttp Ustaw 127.0.0.1:8888 serwera proxy

3. Uruchom ponownie usługę agenta synchronizacji magazynu, uruchamiając następujące polecenie z wiersza polecenia z podwyższonym poziomem uprawnień lub programu PowerShell: 

      polecenie net stop filesyncsvc

      Uwaga: Automatycznie uruchamiana będzie Usługa agenta synchronizacji magazynu (filesyncsvc) po zatrzymaniu.

## <a name="firewall"></a>Zapora
Jak wspomniano w poprzedniej sekcji, port 443 wymaga Otwórz ruchu wychodzącego. Na podstawie zasad w swoim centrum danych, gałęzi lub regionie, dalsze ograniczanie ruchu za pośrednictwem tego portu do określonych domen może być konieczne lub wymagane.

W poniższej tabeli opisano domen wymagana dla komunikacji:

| Usługa | Punkt końcowy w chmurze publicznej | Punkt końcowy platformy Azure dla instytucji rządowych | Sposób użycia |
|---------|----------------|---------------|------------------------------|
| **Azure Resource Manager** | https://management.azure.com | https://management.usgovcloudapi.net | Każde wywołanie użytkownika (na przykład programu PowerShell) przechodzi przez ten adres URL, w tym wywołanie rejestracji serwera początkowego. |
| **Azure Active Directory** | https://login.windows.net | https://login.microsoftonline.us | Wywołania usługi Azure Resource Manager muszą być przekazywane uwierzytelnionego użytkownika. Została wykonana pomyślnie, ten adres URL jest używany do uwierzytelniania użytkowników. |
| **Azure Active Directory** | https://graph.windows.net/ | https://graph.windows.net/ | W ramach wdrażania usługi Azure File Sync nazwy głównej usługi dla subskrypcji usługi Azure Active Directory zostanie utworzona. Ten adres URL jest używany w tym. Ta jednostka służy delegowania minimalny zestaw praw do usługi Azure File Sync. Użytkownik wykonujący początkowej konfiguracji usługi Azure File Sync musi być uwierzytelniony użytkownik z uprawnieniami właściciela subskrypcji. |
| **Azure Storage** | &ast;.core.windows.net | &ast;.core.usgovcloudapi.net | Gdy serwer pobiera plik, następnie serwera wykonuje tego przepływu danych bardziej wydajnie w przypadku bezpośrednio do udziału plików platformy Azure na koncie magazynu. Serwer ma klucz sygnatury dostępu Współdzielonego, który zezwala tylko na dostęp do udziału plików docelowych. |
| **Azure File Sync** | &ast;.one.microsoft.com | &ast;.afs.azure.us | Po zarejestrowaniu serwera początkowego serwer odebrał regionalnego adresu URL dla wystąpienia usługi Azure File Sync, w tym regionie. Serwer może komunikować się bezpośrednio i efektywnie z wystąpieniem, Obsługa synchronizację za pomocą adresu URL. |
| **Microsoft PKI** | `https://www.microsoft.com/pki/mscorp`<br /><http://ocsp.msocsp.com> | `https://www.microsoft.com/pki/mscorp`<br /><http://ocsp.msocsp.com> | Po zainstalowaniu agenta usługi Azure File Sync adres URL infrastruktury kluczy publicznych jest używany do pobrania certyfikatów pośrednich wymagany do komunikowania się z usługą Azure File Sync i udziału plików platformy Azure. Adres URL protokołu OCSP służy do sprawdzania stanu certyfikatu. |

> [!Important]
> Gdy zezwala na ruch do &ast;. one.microsoft.com, ruch do więcej niż tylko usługa synchronizacji jest możliwe z serwera. Istnieje wiele więcej usług firmy Microsoft są dostępne w obszarze domen podrzędnych.

Jeśli &ast;. one.microsoft.com jest zbyt szerokie, można ograniczyć komunikacji z serwerem, umożliwiając komunikację tylko jawne regionalnych wystąpień usługi Azure Files Sync. Które wystąpienia, aby wybrać zależy od regionu Usługa synchronizacji magazynu, które mają wdrożone i zarejestrowane serwerowi. Ten region określa się mianem "adres URL podstawowego punktu końcowego" w tabeli poniżej.

Ciągłość prowadzenia działalności biznesowej po awarii (BCDR) odzyskiwania ze względu na i określono udziałów usługi plików platformy Azure na koncie magazynu globalnie nadmiarowy (GRS). Jeśli tak jest rzeczywiście, następnie udziałów plików platformy Azure zakończy się niepowodzeniem na sparowanym regionie w przypadku długotrwałej awarii regionalnej. Usługa Azure File Sync używa tej samej pary regionalnej jako magazyn. Dlatego jeśli używasz konta magazynu GRS, musisz włączyć dodatkowe adresy URL umożliwić serwerowi komunikować się sparowanym regionie dla usługi Azure File Sync. W poniższej tabeli wywołuje to "region Paired". Ponadto jest ruch adres URL profilu Menedżera, który musi być także włączona. Pozwoli to zagwarantować ruchu sieciowego można bezproblemowo ponownie kierowane do sparowanym regionie w przypadku pracy awaryjnej i nosi nazwę "Adres URL odnajdywania usługi" w tabeli poniżej.

| Chmura  | Region | Adres URL podstawowego punktu końcowego | Region sparowany | Adres URL odnajdywania |
|--------|--------|----------------------|---------------|---------------|
| Public |Australia Wschodnia | https://kailani-aue.one.microsoft.com | Australia Południowo-Wschodnia | https://tm-kailani-aue.one.microsoft.com |
| Public |Australia Południowo-Wschodnia | https://kailani-aus.one.microsoft.com | Australia Wschodnia | https://tm-kailani-aus.one.microsoft.com |
| Public | Brazylia Południowa | https://brazilsouth01.afs.azure.net | Środkowo-południowe stany USA | https://tm-brazilsouth01.afs.azure.net |
| Public | Kanada Środkowa | https://kailani-cac.one.microsoft.com | Kanada Wschodnia | https://tm-kailani-cac.one.microsoft.com |
| Public | Kanada Wschodnia | https://kailani-cae.one.microsoft.com | Kanada Środkowa | https://tm-kailani.cae.one.microsoft.com |
| Public | Indie Środkowe | https://kailani-cin.one.microsoft.com | Indie Południowe | https://tm-kailani-cin.one.microsoft.com |
| Public | Środkowe stany USA | https://kailani-cus.one.microsoft.com | Wschodnie stany USA 2 | https://tm-kailani-cus.one.microsoft.com |
| Public | Azja Wschodnia | https://kailani11.one.microsoft.com | Azja Południowo-Wschodnia | https://tm-kailani11.one.microsoft.com |
| Public | East US | https://kailani1.one.microsoft.com | Zachodnie stany USA | https://tm-kailani1.one.microsoft.com |
| Public | Wschodnie stany USA 2 | https://kailani-ess.one.microsoft.com | Środkowe stany USA | https://tm-kailani-ess.one.microsoft.com |
| Public | Japonia Wschodnia | https://japaneast01.afs.azure.net | Japonia Zachodnia | https://tm-japaneast01.afs.azure.net |
| Public | Japonia Zachodnia | https://japanwest01.afs.azure.net | Japonia Wschodnia | https://tm-japanwest01.afs.azure.net |
| Public | Korea Środkowa | https://koreacentral01.afs.azure.net/ | Korea Południowa | https://tm-koreacentral01.afs.azure.net/ |
| Public | Korea Południowa | https://koreasouth01.afs.azure.net/ | Korea Środkowa | https://tm-koreasouth01.afs.azure.net/ |
| Public | Środkowo-północne stany USA | https://northcentralus01.afs.azure.net | Środkowo-południowe stany USA | https://tm-northcentralus01.afs.azure.net |
| Public | Europa Północna | https://kailani7.one.microsoft.com | Europa Zachodnia | https://tm-kailani7.one.microsoft.com |
| Public | Środkowo-południowe stany USA | https://southcentralus01.afs.azure.net | Środkowo-północne stany USA | https://tm-southcentralus01.afs.azure.net |
| Public | Indie Południowe | https://kailani-sin.one.microsoft.com | Indie Środkowe | https://tm-kailani-sin.one.microsoft.com |
| Public | Azja Południowo-Wschodnia | https://kailani10.one.microsoft.com | Azja Wschodnia | https://tm-kailani10.one.microsoft.com |
| Public | Południowe Zjednoczone Królestwo | https://kailani-uks.one.microsoft.com | Zachodnie Zjednoczone Królestwo | https://tm-kailani-uks.one.microsoft.com |
| Public | Zachodnie Zjednoczone Królestwo | https://kailani-ukw.one.microsoft.com | Południowe Zjednoczone Królestwo | https://tm-kailani-ukw.one.microsoft.com |
| Public | Środkowo-zachodnie stany USA | https://westcentralus01.afs.azure.net | Zachodnie stany USA 2 | https://tm-westcentralus01.afs.azure.net |
| Public | Europa Zachodnia | https://kailani6.one.microsoft.com | Europa Północna | https://tm-kailani6.one.microsoft.com |
| Public | Zachodnie stany USA | https://kailani.one.microsoft.com | East US | https://tm-kailani.one.microsoft.com |
| Public | Zachodnie stany USA 2 | https://westus201.afs.azure.net | Środkowo-zachodnie stany USA | https://tm-westus201.afs.azure.net |
| Instytucje rządowe | Administracja USA — Arizona | https://usgovarizona01.afs.azure.us | Administracja USA — Teksas | https://tm-usgovarizona01.afs.azure.us |
| Instytucje rządowe | Administracja USA — Teksas | https://usgovtexas01.afs.azure.us | Administracja USA — Arizona | https://tm-usgovtexas01.afs.azure.us |

- Jeśli używasz lokalnie nadmiarowy (LRS) lub strefy nadmiarowego (ZRS) konta magazynu, wystarczy włączyć adresu URL podanego w polu "adres URL podstawowego punktu końcowego".

- Jeśli używasz konta usługi storage globalnie nadmiarowy (GRS), należy włączyć trzy adresy URL.

**Przykład:** Wdrażanie usługi synchronizacji magazynu w `"West US"` i zarejestrowanie serwera. Adresy URL, aby umożliwić serwera komunikowanie się w tym przypadku to:

> - https://kailani.one.microsoft.com (podstawowego punktu końcowego: Zachodnie stany USA)
> - https://kailani1.one.microsoft.com (sparowanym regionie trybu failover: Wschodnie stany USA)
> - https://tm-kailani.one.microsoft.com (adres URL odnajdywania regionu podstawowego)

## <a name="summary-and-risk-limitation"></a>Podsumowanie i ryzyko ograniczenia
Listy we wcześniejszej części tego dokumentu zawiera adresy URL, usługa Azure File Sync aktualnie komunikuje się z usługą. Zapory muszą mieć możliwość zezwalają na ruch wychodzący do tych domen. Firma Microsoft dokłada starań zachować to lista została zaktualizowana.

Konfigurowanie domeny ograniczenie reguł zapory można miarę w celu zwiększenia bezpieczeństwa. Jeśli używane są te konfiguracje zapory, należy pamiętać, że adresy URL zostaną dodane, a nawet może zmienić wraz z upływem czasu. Sprawdzaj okresowo, w tym artykule.

## <a name="next-steps"></a>Kolejne kroki
- [Planowanie wdrożenia usługi Azure File Sync](storage-sync-files-planning.md)
- [Wdrażanie usługi Azure File Sync](storage-sync-files-deployment-guide.md)
- [Monitorowanie usługi Azure File Sync](storage-sync-files-monitoring.md)
