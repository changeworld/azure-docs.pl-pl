---
title: Omówienie łączników serwera Proxy aplikacji usługi Azure AD | Dokumentacja firmy Microsoft
description: Zawiera podstawowe informacje dotyczące łączników serwera Proxy aplikacji usługi Azure AD.
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: celested
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: a787e896016b3230d389b2ec140ae6c03477d875
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684093"
---
# <a name="understand-azure-ad-application-proxy-connectors"></a>Omówienie łączników serwera Proxy aplikacji usługi Azure AD

Łączniki są na tym, co sprawia, że serwer Proxy aplikacji usługi Azure AD to możliwe. Są one proste, łatwe do wdrożenia i obsługi i bardzo wydajne. W tym artykule omówiono, jakie łączniki są, jak to działa i sugestie dotyczące sposobu optymalizacji danego wdrożenia. 

## <a name="what-is-an-application-proxy-connector"></a>Co to jest łącznik serwera Proxy aplikacji?

Łączniki są uproszczone agentów, które znajdują się w środowisku lokalnym i ułatwienia połączenie wychodzące do usługi serwera Proxy aplikacji. Łączniki, musi być zainstalowany w systemie Windows Server, który ma dostęp do aplikacji zaplecza. Łączników można organizować w grupy łączników, z każdą grupą obsługi ruchu do określonych aplikacji.

## <a name="requirements-and-deployment"></a>Wymagania i wdrożenia

Aby pomyślnie wdrożyć serwer Proxy aplikacji, co najmniej jeden łącznik jest wymagany, ale zaleca się co najmniej dwóch pod kątem większej odporności. Łącznik można zainstalować na maszynie 2016 lub Windows Server 2012 R2. Łącznik musi komunikować się z usługą serwera Proxy aplikacji usługi i aplikacji lokalnych, które można opublikować. 

### <a name="windows-server"></a>Server systemu Windows
Wymagany serwer z systemem Windows Server 2012 R2 lub nowszym na której można zainstalować łącznik serwera Proxy aplikacji. Serwer musi łączyć się z usługi serwera Proxy aplikacji na platformie Azure i aplikacji lokalnych, które publikujesz.

Systemu windows server musi mieć protokół TLS 1.2, włączone, przed zainstalowaniem łącznika serwera Proxy aplikacji. Istniejące łączniki z wersjami starszymi niż 1.5.612.0 będą w dalszym ciągu działać we wcześniejszych wersjach TLS do odwołania. Aby włączyć protokół TLS 1.2:

1. Ustaw następujące klucze rejestru:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

2. Uruchom ponownie serwer


Aby uzyskać więcej informacji na temat wymagania sieciowe dotyczące serwera łącznika zobacz [Rozpoczynanie pracy z usługą serwera Proxy aplikacji i zainstalować łącznik](application-proxy-add-on-premises-application.md).

## <a name="maintenance"></a>Konserwacja
Łączniki i usługa zajmie się wszystkie zadania o wysokiej dostępności. Mogą one dodane lub usunięte dynamicznie. Każdorazowo, gdy nowe żądanie dociera jest kierowany do jednego z łączników, które jest obecnie dostępna. Łącznik jest tymczasowo niedostępny, nie reagować na ten ruch.

Łączniki są bezstanowe i nie mają konfiguracji danych na maszynie. Jedyne dane, które przechowują jest ustawienie łączenia usługi i jego certyfikat uwierzytelniania. Łączą się z usługą ściągania danych wymaganej konfiguracji i ich odświeżanie co kilka minut.

Łączniki wykonać sondowanie serwer, aby dowiedzieć się, czy dostępna jest nowsza wersja łącznika. Jeśli nie zostanie znalezione, łączniki, aktualizują się same.

Łączniki z komputera, na którym jest uruchomiona, można monitorować przy użyciu dziennika zdarzeń i liczniki wydajności. Lub można wyświetlić ich stan ze strony serwera Proxy aplikacji w witrynie Azure Portal:

 ![Łączników serwera Proxy aplikacji usługi Azure AD](./media/application-proxy-connectors/app-proxy-connectors.png)

Nie trzeba ręcznie usunąć łączniki, które nie są używane. Łącznik jest uruchomiona, pozostaje aktywna jako nawiąże połączenie z usługą. Nieużywane łączniki są oznaczone jako _nieaktywne_ i zostaną usunięte po upływie 10 dni braku aktywności. Jeśli chcesz odinstalować łącznik, jednak odinstaluj usługę łącznika i usługę aktualizacji z serwera. Uruchom ponownie komputer, aby całkowicie usunąć usługę.

## <a name="automatic-updates"></a>Automatyczne aktualizacje

Usługa Azure AD zapewnia automatyczne otrzymywanie aktualizacji dla wszystkich łączników, które można wdrożyć. Tak długo, jak usługa aktualizator łącznika serwera Proxy aplikacji jest uruchomiona, łączników są aktualizowane automatycznie. Jeśli nie widzisz usługi aktualizator łącznika na serwerze, należy [ponownej instalacji usługi łącznika](application-proxy-add-on-premises-application.md) na pobranie aktualizacji. 

Jeśli nie chcesz czekać na automatycznych aktualizacji na osiągnięcie łącznika, możesz zrobić ręcznego uaktualnienia. Przejdź do [strony pobierania łącznika](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download) na serwerze, gdzie Twój łącznik jest zlokalizowany i wybierz pozycję **Pobierz**. Ten proces dotyczącego uaktualnienie lokalnego łącznika. 

Dla dzierżawców dzięki wielu łącznikom aktualizacje automatyczne docelowe jeden łącznik w czasie w każdej grupie, aby uniknąć przestojów w danym środowisku. 

Może wystąpić Przestój, po zaktualizowaniu łącznika, jeśli:  
- Masz tylko jeden łącznik zaleca się instalowania drugi łącznik i [Tworzenie grupy łączników](application-proxy-connector-groups.md). Spowoduje to uniknąć przestoju i zapewnić wyższą dostępność.  
- Łącznik został środku transakcji, podczas rozpoczęcia aktualizacji. Mimo że początkowej transakcja zostanie utracony, przeglądarka automatycznie spróbuj ponownie wykonać operację lub możesz odświeżyć stronę. Gdy żądanie jest wysyłane ponownie, ruch jest kierowany do tworzenia kopii zapasowej łącznika.

Aby wyświetlić informacje o wersjach wcześniej wydanych i jakich zmian, ich zawierają, zobacz [Application Proxy — Historia wersji](application-proxy-release-version-history.md).

## <a name="creating-connector-groups"></a>Tworzenie grupy łączników

Grupy łączników umożliwiają przypisywanie określonych łączników do obsługi określonych aplikacji. Można grupować wiele łączników, a następnie przypisz każdej aplikacji do grupy. 

Grupy łączników ułatwiają zarządzanie dużych wdrożeń. Mogą również zwiększyć opóźnienie dla dzierżawy, które mają aplikacje hostowane w różnych regionach, ponieważ można utworzyć grupy oparte na lokalizacji łączników, aby obsługiwać tylko lokalne aplikacje. 

Aby dowiedzieć się więcej na temat grupy łączników, zobacz [Publikuj aplikacje w oddzielnych sieciach i miejsc za pomocą grupy łączników](application-proxy-connector-groups.md).

## <a name="capacity-planning"></a>Planowanie pojemności 

Należy się upewnić, że zaplanowano wystarczająca ilość miejsca między łączników, aby obsłużyć oczekiwanego natężenia ruchu. W ogólne, większej liczby użytkowników, do których masz większe maszynie należy. Poniżej znajduje się że tabeli, dając konspektu woluminu różnych komputerów może obsłużyć. Należy pamiętać, wszystkie opiera się na oczekiwanych transakcji na drugim (TPS), a nie przez użytkownika, ponieważ użycie wzorce różnią się i nie można używać do prognozowania obciążenia. Będzie również istnieć pewne różnice w oparciu o rozmiar odpowiedzi i czas odpowiedzi aplikacji wewnętrznej bazy danych — niższy TPS Max spowoduje większe rozmiary odpowiedzi i mniejsza czasów odpowiedzi. Zaleca się o dodatkowych maszyn, tak aby rozłożonego obciążenia między maszynami jest o 50%. Dodatkowa pojemność zapewni, że wysokiej dostępności i odporności.

|Rdzenie|Pamięć RAM|Oczekiwany czas oczekiwania (MS) — poziomie P99|Max TPS|
| ----- | ----- | ----- | ----- |
|2|8|325|586|
|4|16|320|1150|
|8|32|270|1190|
|16|64|245|1200*|

\* Ta maszyna używana niestandardową wartość ustawienia pozyskiwania niektóre domyślne limity połączenia po przekroczeniu .NET zalecanych ustawień. Firma Microsoft zaleca uruchamianie testu przy użyciu ustawień domyślnych, skontaktuj się z pomocą techniczną, aby ten limit, zmieniona dla Twojej dzierżawy.
 
>[!NOTE]
>Nie jest dużo różnicy w maksymalna TPS 4, 8 i 16 rdzeni maszyny. Główna różnica między tymi jest oczekiwane opóźnienie.  

## <a name="security-and-networking"></a>Zabezpieczenia i sieci

Łączników można zainstalować w dowolnym miejscu w sieci, która pozwala im wysyłać żądania do usługi serwera Proxy aplikacji. Co to jest ważne jest, czy komputer z uruchomioną łącznik również ma dostęp do aplikacji. Wewnątrz sieci firmowej lub na maszynie wirtualnej, która działa w chmurze, można zainstalować łączniki. Łączniki mogą być uruchamiane w sieci obwodowej, ale nie jest konieczne, ponieważ cały ruch jest wychodzący, dlatego sieci pozostają bezpieczne.

Łączniki tylko wysyłać żądania wychodzącego. Ruch wychodzący są wysyłane do usługi serwera Proxy aplikacji i do opublikowanych aplikacji. Nie trzeba otworzyć porty dla ruchu przychodzącego, ponieważ ruch odbywa się dwukierunkowo po ustanowieniu sesji. Ponadto nie należy skonfigurować dla ruchu przychodzącego dostęp za pośrednictwem zapory. 

Aby uzyskać więcej informacji o konfigurowaniu reguł zapory dla ruchu wychodzącego, zobacz [pracy przy użyciu istniejących serwerów proxy lokalnych](application-proxy-configure-connectors-with-proxy-servers.md).


## <a name="performance-and-scalability"></a>Wydajność i skalowalność

Skalowanie usługi Serwer Proxy aplikacji jest przejrzysta, jednak skala jest współczynnik dla łączników. Musisz mieć wystarczającej liczby łączników w celu obsługi szczytowego natężenia ruchu. Łączniki są bezstanowe, nie ma wpływu według liczby użytkowników lub sesji. Zamiast tego mogą odpowiadać na liczbę żądań i ich rozmiar ładunku. Za pomocą ruch internetowy standard średnia maszyna może obsługiwać kilka tysięcy żądań na sekundę. Określonej pojemności, zależy od właściwości dokładnie maszyny. 

Wydajność łącznika jest związana z procesora CPU i sieci. Wydajność procesora CPU jest potrzebny do protokołu SSL szyfrowania i odszyfrowywania, gdy sieć jest ważne, aby uzyskać szybkie łączności do aplikacji i usług online na platformie Azure.

Z kolei jest pamięci nie stanowi takiego problemu dla łączników. Usługi online zajmuje się znacznie przetwarzania danych, a także cały ruch nieuwierzytelnionych. Wszystko, co można zrobić w chmurze jest wykonywane w chmurze. 

Jeśli z jakiegokolwiek powodu, że łącznik lub maszyny staną się niedostępne, ruch zostanie uruchomione, przechodząc do innego łącznika w grupie. Ta elastyczność jest również, dlaczego firma Microsoft zaleca posiadanie wielu łączników.

Innym czynnikiem, który wpływa na wydajność jest jakość siecią między łączniki, w tym: 

* **Usługi online**: Wolne lub długim czasie oczekiwania połączenia z usługą serwera Proxy aplikacji na platformie Azure mają wpływ na wydajność łącznika. Aby uzyskać najlepszą wydajność należy połączyć Twojej organizacji na platformie Azure z usługą Express Route. W przeciwnym razie ma Twój zespół sieci, upewnij się, jak najbardziej wydajny obsługiwania połączenia z platformą Azure. 
* **Aplikacji zaplecza**: W niektórych przypadkach istnieją dodatkowe serwery proxy między łącznika i aplikacji zaplecza, które mogą spowalniać lub uniemożliwić połączenia. Aby rozwiązać ten scenariusz, otwórz przeglądarkę z serwerem łącznika i spróbuj uzyskać dostęp do aplikacji. Jeśli łączniki działające na platformie Azure, ale aplikacje są w środowisku lokalnym, proces może nie być oczekiwaniami użytkowników.
* **Kontrolery domeny**: Łączniki wykonać logowanie jednokrotne (SSO) przy użyciu ograniczonego delegowania protokołu Kerberos, mogą skontaktować się z kontrolerów domeny przed wysłaniem żądania do zaplecza. Łączniki mają pamięci podręcznej bilety protokołu Kerberos, ale w środowisku zajęty szybkość reakcji kontrolerów domeny może wpłynąć na wydajność. Ten problem jest częściej łączników, które działają na platformie Azure, jednak komunikować się z kontrolerów domeny, które są w środowisku lokalnym. 

Aby uzyskać więcej informacji o optymalizacji sieci, zobacz [zagadnienia dotyczące topologii sieci, korzystając z serwera Proxy usługi Azure Active Directory Application](application-proxy-network-topology.md).

## <a name="domain-joining"></a>Przyłączanie do domeny

Łączników można uruchomić na komputerze, który nie jest przyłączony do domeny. Jeśli chcesz logowania jednokrotnego (SSO) aplikacjom, które używają zintegrowanego Windows Authentication (Zintegrowane), należy jednak komputerze przyłączonym do domeny. W tym przypadku maszyny łącznika musi być przyłączony do domeny, które może wykonywać [Kerberos](https://web.mit.edu/kerberos) ograniczonego delegowania w imieniu użytkowników w opublikowanej aplikacji.

Łączników można również łączyć domen i lasów, które mają częściowej relacji zaufania lub kontrolerów domeny tylko do odczytu.

## <a name="connector-deployments-on-hardened-environments"></a>Łącznik wdrożeń w środowiskach ze wzmocnionymi zabezpieczeniami

Zazwyczaj wdrożenia łącznika jest proste, a nie wymaga specjalnej konfiguracji. Istnieją jednak pewne unikatowe warunki, które należy uwzględnić:

* Organizacje, które ograniczają ruch wychodzący musi [otworzyć wymagane porty](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).
* Maszyny zgodne ze standardem FIPS, może być konieczne zmienianie ich konfiguracji, aby zezwolić na procesy łącznika do generowania i przechowywania certyfikatu.
* Organizacje, które zablokowania ich środowisko oparte na procesy, które wysyłają żądania sieci, trzeba upewnić się, że obie te usługi łącznika są włączone, dostęp do wszystkich wymaganych portów i adresów IP.
* W niektórych przypadkach ruchu wychodzącego do przodu serwery proxy może przerwać uwierzytelnianie certyfikatu dwukierunkowe i spowodować, że komunikacja nie powiedzie się.

## <a name="connector-authentication"></a>Łącznik uwierzytelniania

Zapewnienie bezpiecznej usłudze, łączników do uwierzytelniania usługi, a usługa udostępnia do uwierzytelniania w kierunku łącznika. To uwierzytelnianie jest wykonywane przy użyciu certyfikatów klienta i serwera, gdy łączniki inicjują połączenia. Dzięki temu nazwa użytkownika i hasło administratora nie są przechowywane na maszynie łącznika.

Certyfikaty używane są specyficzne dla usługi serwera Proxy aplikacji. Tworzone podczas wstępnej rejestracji i są automatycznie odnawiane przez łączniki co kilka miesięcy. 

Jeśli łącznik nie jest połączony z usługą przez kilka miesięcy, jego certyfikatów może być nieaktualna. W tym przypadku odinstalowanie i ponowne instalowanie łącznika do rejestracji wyzwalaczy. Można uruchomić następujące polecenia środowiska PowerShell:

```
Import-module AppProxyPSModule
Register-AppProxyConnector
```

## <a name="under-the-hood"></a>Kulisy

Łączniki zależą od systemu Windows serwer Proxy aplikacji sieci Web, więc większość z tych samych narzędzi zarządzania, w tym Windows, dzienniki zdarzeń

 ![Zarządzanie dziennikami zdarzeń za pomocą Podglądu zdarzeń](./media/application-proxy-connectors/event-view-window.png)

i Windows, liczniki wydajności. 

 ![Dodawanie liczników do łącznika przy użyciu Monitora wydajności](./media/application-proxy-connectors/performance-monitor.png)

Łączniki mają zarówno administratora, jak i sesji dzienniki. Dzienniki administracyjne obejmują kluczowych zdarzeń i ich błędy. Dzienniki sesji obejmują wszystkie transakcje i ich szczegóły przetwarzania. 

Aby wyświetlić dzienniki, przejdź do podglądu zdarzeń, otwórz **widoku** menu, a następnie włączyć **Pokaż analityczne i debugowania dzienniki**. Następnie włącz je rozpocząć zbieranie zdarzeń. Te dzienniki nie są wyświetlane na serwerze Proxy aplikacji sieci Web w systemie Windows Server 2012 R2, jak łączniki są oparte na nowszą wersję.

Można sprawdzić stanu usługi, w oknie usług. Łącznik składa się z dwóch usług Windows: rzeczywiste łącznika i aktualizacji. Obie z nich należy uruchomić cały czas.

 ![AzureAD Services Local](./media/application-proxy-connectors/aad-connector-services.png)

## <a name="next-steps"></a>Kolejne kroki


* [Publikuj aplikacje w oddzielnych sieciach i miejsc za pomocą grupy łączników](application-proxy-connector-groups.md)
* [Praca z istniejących serwerów proxy w środowisku lokalnym](application-proxy-configure-connectors-with-proxy-servers.md)
* [Rozwiązywanie problemów z błędami serwera Proxy aplikacji i łączników](application-proxy-troubleshoot.md)
* [Jak dyskretnie zainstalować łącznik serwera Proxy aplikacji usługi Azure AD](application-proxy-register-connector-powershell.md)

