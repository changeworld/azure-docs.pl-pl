---
title: Synchronizacja programu Azure pliku lokalnego ustawienia zapory i serwera proxy | Dokumentacja firmy Microsoft
description: Synchronizacja programu Azure pliku lokalnego konfiguracji sieci
services: storage
documentationcenter: ''
author: fauhse
manager: klaasl
editor: tamram
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: fauhse
ms.openlocfilehash: 81425c6ac4e463bd4242328206bd43ce78a1105a
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Azure ustawienia serwera proxy i zapory synchronizacji plików
Synchronizacja programu Azure pliku nawiązuje połączenie z serwerami lokalnymi do usługi pliki Azure, włączenie synchronizacji obejmujący wiele lokacji i w chmurze, Obsługa poziomów funkcji. W efekcie lokalnego serwera musi połączone z Internetem. Administrator IT musi określić ścieżkę najlepsze na serwerze, aby uzyskać dostęp do usług w chmurze Azure.

W tym artykule będzie zapewniają wgląd w określonych wymagań i opcji dostępnych dla pomyślnie i bezpieczna łączność między serwerem synchronizacji plików Azure.

## <a name="overview"></a>Przegląd
Synchronizacja programu Azure pliku działa jako usługa aranżacji między serwera z systemem Windows, udziału plików platformy Azure i kilka innych usług Azure, na synchronizowanie danych, zgodnie z opisem w grupie synchronizacji. Do celów synchronizacji plików Azure działał prawidłowo należy skonfigurować serwery do komunikowania się z następujących usług platformy Azure:

- Azure Storage
- Usługa Azure File Sync
- Azure Resource Manager
- Usługi uwierzytelniania

> [!Note]  
> Agent synchronizacji plików Azure w systemie Windows Server inicjuje wszystkie żądania do usługi, które powoduje tylko konieczności należy wziąć pod uwagę ruch wychodzący z punktu widzenia zapory w chmurze. <br /> Żadna usługa Azure inicjuje połączenie z agentem Azure synchronizacji plików.


## <a name="ports"></a>Porty
Synchronizacja programu Azure pliku przenosi dane plików i metadanych wyłącznie za pośrednictwem protokołu HTTPS i wymaga portu 443, można otworzyć wychodzących.
W związku z tym cały ruch jest szyfrowany.

## <a name="networks-and-special-connections-to-azure"></a>Sieci i specjalnych połączeń na platformie Azure
Agent synchronizacji plików Azure nie ma wymagań dotyczących kanałów specjalnych, takich jak [ExpressRoute](../../expressroute/expressroute-introduction.md), itp. na platformie Azure.

Synchronizacja programu Azure pliku będzie działać, za pomocą jakichkolwiek metod umożliwiająca reach na platformie Azure, automatycznie adaptacja do różnych właściwości sieci, takich jak przepustowość, czas oczekiwania, a także oferowanie administratorowi kontrolowanie szczegóły. Nie wszystkie funkcje są dostępne w tej chwili. Daj nam znać, jeśli chcesz skonfigurować określone zachowanie, za pomocą [UserVoice plików Azure](https://feedback.azure.com/forums/217298-storage?category_id=180670).

## <a name="proxy"></a>Serwer proxy
Synchronizacja programu Azure pliku obecnie obsługuje ustawienia serwera proxy dla komputera. Ustawienie serwera proxy jest niewidoczny z agentem Azure plik synchronizacji, ponieważ cały ruch serwera jest kierowany przez ten serwer proxy.

Ustawienia serwera proxy dla aplikacji są obecnie opracowywane i będą obsługiwane w przyszłej wersji agenta synchronizacji plików Azure. Pozwoli to konfiguracji serwera proxy dla ruchu Azure synchronizacji plików.

## <a name="firewall"></a>Zapora
Jak wspomniano w poprzedniej sekcji, port 443 musi być Otwórz ruchu wychodzącego. Na podstawie zasad w centrum danych, gałęzi lub regionu, dalsze ograniczanie ruchu za pośrednictwem tego portu do określonych domen może być konieczne lub wymagane.

W poniższej tabeli opisano domen wymagana dla komunikacji:

| Usługa | Domena | Sposób użycia |
|---------|----------------|------------------------------|
| **Azure Resource Manager** | https://management.azure.com | Każde wywołanie użytkownika (np. programu PowerShell) przechodzi przez ten adres URL, w tym wywołanie rejestracji serwera początkowego. |
| **Azure Active Directory** | https://login.windows.net | Wywołania usługi Azure Resource Manager muszą być wprowadzane przez uwierzytelnionego użytkownika. Zakończyła się powodzeniem, ten adres URL jest używany do uwierzytelniania użytkowników. |
| **Azure Active Directory** | https://graph.windows.net/ | W ramach wdrażania synchronizacja programu Azure pliku nazwy głównej usługi w subskrypcji usługi Azure Active Directory zostanie utworzona. Ten adres URL jest używany w tym. Tego podmiotu zabezpieczeń jest używany do delegowania minimalny zestaw praw do usługi Azure synchronizacji plików. Użytkownik wykonywania początkowej konfiguracji synchronizacji plików Azure musi być uwierzytelniony użytkownik z uprawnieniami właściciela subskrypcji. |
| **Azure Storage** | &ast;.core.windows.net | Gdy serwer pobierze plik, następnie serwer wykonuje tego więcej przenoszenia danych wydajnie po rozmowie bezpośrednio z udziału plików Azure w ramach konta magazynu. Serwer ma klucz sygnatury dostępu Współdzielonego, która zezwala na dostęp do udziału pliku docelowego. |
| **Synchronizacja plików na platformę Azure** | &ast;.one.microsoft.com | Po zarejestrowaniu serwera początkowego serwer odbiera regionalnych adres URL dla wystąpienia usługi synchronizacji plików Azure w tym regionie. Serwer może komunikować się bezpośrednio i efektywnie z wystąpieniem obsługi jego synchronizacji za pomocą adresu URL. |

> [!Important]
> Gdy zezwala na ruch do &ast;. one.microsoft.com, ruch do więcej niż tylko usługi synchronizacji jest możliwe z serwera. Istnieje wiele więcej usług firmy Microsoft są dostępne w poddomeny.

Jeśli &ast;. one.microsoft.com jest zbyt szerokie, zezwalając tylko jawne regionalnych wystąpienia usługi synchronizacji plików Azure można ograniczyć komunikacji z serwerem. Które wystąpienia, aby wybrać, zależy od obszaru magazynu usługi synchronizacji, do którego zostały wdrożone i zarejestrowane serwera. To region, który należy zezwolić na serwerze. Wkrótce będzie można więcej adresów URL, aby włączyć nowe funkcje ciągłości biznesowej. 

| Region | Azure synchronizacji plików regionalnych końcowego adresu URL |
|--------|---------------------------------------|
| Australia Wschodnia | https://kailani-aue.one.microsoft.com |
| Kanada Środkowa | https://kailani-cac.one.microsoft.com |
| Wschodnie stany USA | https://kailani1.one.microsoft.com |
| Azja Południowo-Wschodnia | https://kailani10.one.microsoft.com |
| Południowe Zjednoczone Królestwo | https://kailani-uks.one.microsoft.com |
| Europa Zachodnia | https://kailani6.one.microsoft.com |
| Zachodnie stany USA | https://kailani.one.microsoft.com |

> [!Important]
> W przypadku definiowania tych reguł zapory szczegółowe, zaglądaj tego dokumentu, a następnie zaktualizować reguł zapory w celu uniknięcia przerw w działaniu usługi z powodu nieaktualnych lub niekompletne listy adres URL w ustawieniach zapory.

## <a name="summary-and-risk-limitation"></a>Podsumowanie i ryzyko ograniczenia
Listy wcześniej w tym dokumencie zawierają synchronizacji plików Azure aktualnie komunikuje się z adresów URL. Zapory musi mieć możliwość zezwolić na ruch wychodzący do tych domen, a także odpowiedzi z nich. Firma Microsoft dokłada starań zachować tej listy aktualizacji.

Konfigurowanie domeny ograniczanie reguł zapory można miarę w celu poprawy bezpieczeństwa. Jeśli te konfiguracje zapory są używane, należy pamiętać, adresy URL dodawane i zmienić wraz z upływem czasu. W związku z tym jest to miara rozsądne do sprawdzenia w tabelach w tym dokumencie jako część procesu zarządzania zmiana z jednej wersji agenta synchronizacji plików Azure w wdrożeniu najnowsza wersja agenta testowego do drugiego. W ten sposób można upewnij się, że Zapora jest skonfigurowana zezwalająca na ruch do domen najnowszych agenta wymaga.

## <a name="next-steps"></a>Kolejne kroki
- [Planowanie wdrożenia synchronizacji plików Azure](storage-sync-files-planning.md)
- [Wdrażanie synchronizacji plików Azure (wersja zapoznawcza)](storage-sync-files-deployment-guide.md)
