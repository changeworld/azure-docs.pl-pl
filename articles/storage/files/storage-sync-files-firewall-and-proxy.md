---
title: Usługa Azure File Sync w środowisku lokalnym ustawień zapory i serwera proxy | Dokumentacja firmy Microsoft
description: Usługa Azure File Sync konfiguracji sieci lokalnej
services: storage
documentationcenter: ''
author: fauhse
manager: aungoo
editor: tamram
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: fauhse
ms.openlocfilehash: 7d86082abb6412072af44a6b2d794bcf536fa18d
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2018
ms.locfileid: "37342730"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Ustawienia serwera proxy i zapory usługi Azure File Sync
Usługa Azure File Sync łączy swoje lokalne serwery do usługi pliki Azure, włączenie synchronizacji obejmujące wiele lokacji i obsługi warstw w funkcji w chmurze. W efekcie na serwerze lokalnym musi połączony z Internetem. Administrator IT musi określić najlepszą ścieżkę dla serwera uzyskać dostęp do usług Azure cloud services.

Ten artykuł będzie zapewniają wgląd w określonych wymagań i opcji umożliwiających pomyślnie i bezpiecznie połączyć swój serwer usługi Azure File Sync.

> [!Important]
> Usługa Azure File Sync nie obsługuje jeszcze zapory i sieci wirtualne dla konta magazynu. 

## <a name="overview"></a>Przegląd
Usługa Azure File Sync działa jako usługa aranżacji między serwera z systemem Windows, udziału plików platformy Azure i kilka innych usług platformy Azure, na synchronizowanie danych, zgodnie z opisem w grupie synchronizacji. Dla usługi Azure File Sync działała prawidłowo należy skonfigurować serwery do komunikowania się z następujących usług platformy Azure:

- Azure Storage
- Usługa Azure File Sync
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

Ustawienia serwera proxy dla komputera są niewidoczne dla agenta usługi Azure File Sync, ponieważ cały ruch serwera odbywa się za pośrednictwem serwera proxy.

Ustawienia serwera proxy dla aplikacji umożliwia skonfigurowanie serwera proxy dla usługi Azure File Sync ruchu. Ustawienia proxy specyficzny dla aplikacji są obsługiwane w wersji agenta 3.0.12.0 lub nowszej, a także można skonfigurować podczas instalacji agenta lub za pomocą polecenia cmdlet programu PowerShell Set-StorageSyncProxyConfiguration.

Polecenia programu PowerShell, aby skonfigurować ustawienia specyficzne dla aplikacji serwera proxy:
```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Set-StorageSyncProxyConfiguration -Address <url> -Port <port number> -ProxyCredential <credentials>
```

## <a name="firewall"></a>Zapora
Jak wspomniano w poprzedniej sekcji, port 443 wymaga Otwórz ruchu wychodzącego. Na podstawie zasad w swoim centrum danych, gałęzi lub regionie, dalsze ograniczanie ruchu za pośrednictwem tego portu do określonych domen może być konieczne lub wymagane.

W poniższej tabeli opisano domen wymagana dla komunikacji:

| Usługa | Domena | Sposób użycia |
|---------|----------------|------------------------------|
| **Azure Resource Manager** | https://management.azure.com | Każde wywołanie użytkownika (na przykład programu PowerShell) przechodzi przez ten adres URL, w tym wywołanie rejestracji serwera początkowego. |
| **Azure Active Directory** | https://login.windows.net | Wywołania usługi Azure Resource Manager muszą być przekazywane uwierzytelnionego użytkownika. Została wykonana pomyślnie, ten adres URL jest używany do uwierzytelniania użytkowników. |
| **Azure Active Directory** | https://graph.windows.net/ | W ramach wdrażania usługi Azure File Sync nazwy głównej usługi dla subskrypcji usługi Azure Active Directory zostanie utworzona. Ten adres URL jest używany w tym. Ta jednostka służy delegowania minimalny zestaw praw do usługi Azure File Sync. Użytkownik wykonujący początkowej konfiguracji usługi Azure File Sync musi być uwierzytelniony użytkownik z uprawnieniami właściciela subskrypcji. |
| **Azure Storage** | &ast;.core.windows.net | Gdy serwer pobiera plik, następnie serwera wykonuje tego przepływu danych bardziej wydajnie w przypadku bezpośrednio do udziału plików platformy Azure na koncie magazynu. Serwer ma klucz sygnatury dostępu Współdzielonego, który zezwala tylko na dostęp do udziału plików docelowych. |
| **Usługi Azure File Sync** | &ast;.one.microsoft.com | Po zarejestrowaniu serwera początkowego serwer odebrał regionalnego adresu URL dla wystąpienia usługi Azure File Sync, w tym regionie. Serwer może komunikować się bezpośrednio i efektywnie z wystąpieniem, Obsługa synchronizację za pomocą adresu URL. |

> [!Important]
> Gdy zezwala na ruch do &ast;. one.microsoft.com, ruch do więcej niż tylko usługa synchronizacji jest możliwe z serwera. Istnieje wiele więcej usług firmy Microsoft są dostępne w obszarze domen podrzędnych.

Jeśli &ast;. one.microsoft.com jest zbyt szerokie, można ograniczyć komunikacji z serwerem, zezwalając tylko jawne regionalnych wystąpień usługi Azure Files Sync. Które wystąpienia, aby wybrać, zależy od regionu usługi synchronizacji magazynu, do którego mają wdrożeniu i zarejestrowaniu serwera. To region, który należy zezwolić na serwerze. Wkrótce nastąpi większej liczby adresów URL, aby włączyć nowe funkcje zachowywania ciągłości biznesowej. 

| Region | URL regionalnych punktu końcowego w usłudze Azure File Sync |
|--------|---------------------------------------|
| Australia Wschodnia | https://kailani-aue.one.microsoft.com |
| Kanada Środkowa | https://kailani-cac.one.microsoft.com |
| Wschodnie stany USA | https://kailani1.one.microsoft.com |
| Azja Południowo-Wschodnia | https://kailani10.one.microsoft.com |
| Południowe Zjednoczone Królestwo | https://kailani-uks.one.microsoft.com |
| Europa Zachodnia | https://kailani6.one.microsoft.com |
| Zachodnie stany USA | https://kailani.one.microsoft.com |

> [!Important]
> Jeśli zdefiniujesz tych reguł zapory szczegółowe, sprawdź często w tym dokumencie i zaktualizuj reguły zapory w celu uniknięcia przerw w świadczeniu usługi z powodu nieaktualnych lub niekompletne ofert adresu URL w ustawieniach zapory.

## <a name="summary-and-risk-limitation"></a>Podsumowanie i ryzyko ograniczenia
Listy we wcześniejszej części tego dokumentu zawiera adresy URL, usługa Azure File Sync aktualnie komunikuje się z usługą. Aby zezwolić na ruch wychodzący do tych domen, jak również odpowiedzi z nich musi umożliwiać zapory. Firma Microsoft dokłada starań zachować to lista została zaktualizowana.

Konfigurowanie domeny ograniczenie reguł zapory można miarę w celu zwiększenia bezpieczeństwa. Jeśli używane są te konfiguracje zapory, należy pamiętać, adresy URL dodawane i zmienić wraz z upływem czasu. Dlatego jest miarą rozsądne do sprawdzenia tabele w tym dokumencie jako części procesu zarządzania zmiana z jednej wersji agenta usługi Azure File Sync na inny we wdrożeniu testu najnowszą wersję agenta. W ten sposób można upewnić się, czy Zapora jest skonfigurowana, aby zezwolić na ruch do domen najnowszych agenta wymaga.

## <a name="next-steps"></a>Kolejne kroki
- [Planowanie wdrożenia usługi Azure File Sync](storage-sync-files-planning.md)
- [Wdrażanie usługi Azure File Sync (wersja zapoznawcza)](storage-sync-files-deployment-guide.md)
