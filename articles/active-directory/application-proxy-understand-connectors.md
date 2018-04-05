---
title: Zrozumienie łączniki serwera Proxy aplikacji usługi Azure AD | Dokumentacja firmy Microsoft
description: Zawiera podstawowe informacje dotyczące serwera Proxy aplikacji usługi Azure AD łączników.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2017
ms.author: billmath
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: fe8d5c40249431be60dc8844adf7efa1b8e87c5f
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="understand-azure-ad-application-proxy-connectors"></a>Zrozumienie łączniki serwera Proxy aplikacji usługi Azure AD

Łączniki są co umożliwiają serwera Proxy aplikacji usługi Azure AD. Są one proste, łatwa do wdrożenia i konserwacji i bardzo zaawansowane. W tym artykule omówiono, jakie łączników są, jak działają i sugestie dotyczące sposobu optymalizacji wdrożenia. 

## <a name="what-is-an-application-proxy-connector"></a>Co to jest łącznik serwera Proxy aplikacji?

Łączniki są lekkie agentów, które znajdują się lokalnie i umożliwiać wychodzące połączenie z usługą serwera Proxy aplikacji. Łączniki, musi być zainstalowany w systemie Windows Server, który ma dostęp do aplikacji zaplecza. Łączniki można organizować w grupy łącznika, z każdą grupą obsługi ruchu do określonych aplikacji. Równoważenie obciążenia łączniki automatycznie, a może pomóc w celu zoptymalizowania strukturę sieci. 

## <a name="requirements-and-deployment"></a>Wymagania i wdrożenia

Aby pomyślnie wdrożyć serwer Proxy aplikacji, potrzebujesz co najmniej jeden łącznik, ale zaleca się co najmniej dwóch dla większej odporności. Zainstaluj łącznik na maszynie 2016 lub Windows Server 2012 R2. Łącznik musi być w stanie nawiązać połączenia z usługi Serwer Proxy aplikacji, a także aplikacji lokalnych, które można opublikować. 

Aby uzyskać więcej informacji o wymaganiach dotyczących sieci na serwerze łącznika, zobacz [Rozpoczynanie pracy z serwera Proxy aplikacji i zainstalować łącznik](active-directory-application-proxy-enable.md).

## <a name="maintenance"></a>Konserwacja
Łączniki i usługę automatyzującą wszystkie zadania wysokiej dostępności. Można można dodać lub usunąć dynamicznie. Zawsze, gdy nowe żądanie dociera jest kierowany do jednego z łączników, które jest obecnie dostępna. Jeśli łącznik jest tymczasowo niedostępny, nie odpowiada na ten ruch.

Łączniki są bezstanowych i nie mają konfiguracji danych na tym komputerze. Tylko dane, które przechowują jest ustawienia dla połączenia usługi oraz jego certyfikat uwierzytelniania. Łączą się z usługą ściąganie wszystkich danych konfiguracji i go odświeżyć co kilka minut.

Łączniki również sprawdzają, czy na serwerze, aby dowiedzieć się, czy dostępna jest nowsza wersja łącznika. Jeśli został znaleziony, łączniki aktualizacji samodzielnie.

Łączniki z komputera, na którym jest uruchomiona, można monitorować przy użyciu dziennika zdarzeń i liczniki wydajności. Lub można wyświetlić ich stan ze strony serwera Proxy aplikacji w portalu Azure:

 ![Łączniki serwera Proxy aplikacji AzureAD](./media/application-proxy-understand-connectors/app-proxy-connectors.png)

Nie trzeba ręcznie usunąć łączniki, które nie są używane. Łącznik jest uruchomiona, pozostaje aktywne zgodnie z jego łączy się z usługą. Nieużywane łączników są oznaczone jako _nieaktywne_ i zostaną usunięte po upływie 10 dni nieaktywności. Jeśli chcesz odinstalować łącznik, jednak odinstalowanie zarówno usługę łącznika i usługę aktualizacji z serwera. Uruchom ponownie komputer, aby całkowicie usunąć usługę.

## <a name="automatic-updates"></a>Automatyczne aktualizacje

Usługa Azure AD zapewnia aktualizacje automatyczne dla wszystkich łączników, które można wdrożyć. Tak długo, jak usługa aktualizator łącznika serwera Proxy aplikacji jest uruchomiona, łączników są aktualizowane automatycznie. Jeśli nie widzisz usługi aktualizator łącznika na serwerze, należy [ponowne instalowanie łącznika programu](active-directory-application-proxy-enable.md) na pobranie aktualizacji. 

Jeśli nie chcesz czekać na automatycznych aktualizacji przejdzie do Twojego łącznika można przeprowadzić uaktualnienie ręczne. Przejdź do [stronę pobierania łącznika](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download) na serwerze, gdzie Twojego łącznika jest zlokalizowany i wybierz pozycję **Pobierz**. Ten proces dotyczącego uaktualnienia dla lokalnego łącznika. 

W przypadku dzierżaw z wiele łączników aktualizacje automatyczne docelowe jeden łącznik w czasie w każdej grupie, aby uniknąć przestoju w danym środowisku. 

Przestój mogą wystąpić podczas aktualizacji z łącznika, jeśli:  
- Masz tylko jeden łącznik. Aby uniknąć tego Przestój i zwiększenia wysokiej dostępności, zaleca się zainstalować drugi łącznik i [Utwórz grupę łącznika](active-directory-application-proxy-connectors-azure-portal.md).  
- Po rozpoczęciu aktualizacji łącznika był w trakcie wykonywania transakcji. Chociaż nie zostały utracone początkowej transakcji, przeglądarka automatycznie spróbuj ponownie wykonać operację lub odświeżyć stronę. Jeśli żądanie jest wysyłane ponownie, ruch jest kierowany do kopii zapasowej łącznika.

## <a name="creating-connector-groups"></a>Tworzenie grup łącznika

Łącznik grupy umożliwiają przypisywanie określonych łączniki do obsługi określonych aplikacji. Można zgrupować wiele łączników, a następnie przypisz każdej aplikacji do grupy. 

Łącznika grupy ułatwiają zarządzanie dużych wdrożeniach. Poprawiają opóźnienia również w przypadku dzierżaw mających aplikacji obsługiwanych w różnych regionach, ponieważ możesz tworzyć grupy na podstawie lokalizacji łącznika do obsługi tylko lokalne aplikacje. 

Aby dowiedzieć się więcej o grupach łącznika, zobacz [publikowania aplikacji w odrębnych sieci i lokalizacje przy użyciu łącznika grup](active-directory-application-proxy-connectors-azure-portal.md).

## <a name="capacity-planning"></a>Planowanie pojemności 

Podczas łączników zostanie automatycznie równoważyć obciążenie spowodowane w obrębie grupy łącznika, jest również należy się upewnić, że zaplanowano wystarczającą wydajność między łączników, aby obsłużyć odebranej oczekiwanego natężenia ruchu. Na ogół więcej użytkowników, których można mieć większe maszyny, które mają być. Poniżej znajduje się, że może obsłużyć tabeli nadanie konspektu woluminu różnych maszyn. Należy pamiętać, jest on wszystkie oparty na oczekiwany transakcji na drugim (TPS), a nie przez użytkownika, ponieważ użycie wzorce różnią się i nie można używać do prognozowania obciążenia.  Należy również zauważyć, że będzie pewne różnice, zależnie od rozmiaru odpowiedzi i czas odpowiedzi aplikacji zaplecza - o większych rozmiarach odpowiedzi i mniejsza czas reakcji spowoduje niższe TPS Max.

|Rdzenie|Pamięć RAM|Oczekiwany czas oczekiwania (MS)-P99|Max TPS|
| ----- | ----- | ----- | ----- |
|2|8|325|586|
|4|16|320|1150|
|8|32|270|1190|
|16|64|245|1200*|
\* Ten komputer ma limit połączenia 800. W przypadku wszystkich innych maszyn użyliśmy domyślny limit 200 połączenia.
 
>[!NOTE]
>Nie jest znacznie różnicy w maksymalną TPS 4, 8 i 16 maszyny podstawowej. Główną różnicą między tymi jest oczekiwany czas oczekiwania.  

## <a name="security-and-networking"></a>Zabezpieczenia i sieci

Łączniki można zainstalować dowolne miejsce w sieci, która pozwala na ich do wysyłania żądań do serwera Proxy aplikacji usługi. Ważne jest czy komputer z uruchomionym łącznik również ma dostęp do aplikacji. Wewnątrz sieci firmowej lub na maszynie wirtualnej, która działa w chmurze, można zainstalować łączniki. Łączniki mogą być uruchamiane w strefą zdemilitaryzowaną (DMZ), ale nie jest konieczne, ponieważ cały ruch jest wychodzący, dlatego sieci pozostają bezpieczne.

Łączniki tylko wysyłać żądania wychodzącego. Ruch wychodzący jest wysyłane do serwera Proxy aplikacji usługi i do opublikowanych aplikacji. Nie trzeba otworzyć porty dla ruchu przychodzącego, ponieważ ruch przepływa obu kierunkach po ustanowieniu sesji. Nie trzeba ustawić zrównoważenia obciążenia między łączniki lub skonfigurować dostęp przychodzących na zaporach. 

Aby uzyskać więcej informacji o konfigurowaniu wychodzących reguł zapory, zobacz [pracy przy użyciu istniejących lokalnych serwerów proxy](application-proxy-working-with-proxy-servers.md).

Użyj [Azure AD aplikacji serwera Proxy łącznika porty testu narzędzia](https://aadap-portcheck.connectorporttest.msappproxy.net/) można zweryfikować, że Twoje łącznika może nawiązać połączenie usługi Serwer Proxy aplikacji. Co najmniej upewnij się, że region środkowe stany USA i najbliższego region jest wszystkich zielony zaznaczeń. Ponadto więcej zaznaczeń zielony oznacza większą elastyczność. 

## <a name="performance-and-scalability"></a>Wydajność i skalowalność

Skali usługi Serwer Proxy aplikacji jest niewidoczny, ale skala jest współczynnik dla łączników. Musisz mieć za mało łączniki do obsługi ruchu godzinami szczytu. Nie trzeba jednak skonfigurowania równoważenia obciążenia, ponieważ wszystkie łączniki w obrębie grupy łącznika automatycznie równoważyć obciążenie.

Ponieważ łączniki są bezstanowych, są one nie dotyczy to według liczby użytkowników lub sesji. Zamiast tego odpowiadają one na liczbę żądań i ich rozmiar ładunku. W przypadku ruchu standardowe sieci web średni maszyny może obsługiwać kilka tysięcy żądań na sekundę. Określonej pojemności zależy od właściwości dokładne maszyny. 

Wydajność łącznika jest związana z procesora CPU i sieci. Wydajność Procesora jest wymagany dla protokołu SSL szyfrowania i odszyfrowywania, gdy sieć jest ważne uzyskać szybki łączności do aplikacji i usług online na platformie Azure.

Natomiast w pamięci jest mniejsza problemu dla łączników. Usługi online odpowiada on za dużo przetwarzania, a także cały ruch nieuwierzytelnione. Wszystko, co można zrobić w chmurze odbywa się w chmurze. 

Równoważenie obciążenia między odbywa się łączniki grupy danego łącznika. Jak odmianą okrężnego do określenia, które łącznika w grupie służy poszczególnych żądań. Po wybraniu łącznika, firma Microsoft zachowuje koligacji sesję tego użytkownika i aplikacji w czasie trwania sesji. Jeśli z jakiegokolwiek powodu ten łącznik lub maszyny staną się niedostępne, ruch zostanie uruchomione, przechodząc do innego łącznika w grupie. Ta odporności jest również, dlaczego zaleca się wiele łączników.

Innym czynnikiem wpływającym na wydajność jest jakość sieci między łączników, w tym: 

* **Usługi online**: wolne lub dużymi opóźnieniami połączenia z serwerem proxy aplikacji usługi w Azure wpływ wydajność łącznika. Aby uzyskać najlepszą wydajność należy połączyć z organizacji na platformie Azure za pomocą Express Route. W przeciwnym razie ma zespołem sieci, upewnij się, jak najbardziej wydajny obsługi połączeń na platformie Azure. 
* **Aplikacjami wewnętrznej bazy danych**: W niektórych przypadkach są dodatkowe serwery proxy między łącznika i aplikacje wewnętrznej bazy danych, które mogą spowalniać lub uniemożliwiać połączenia. Aby rozwiązać ten scenariusz, otwórz przeglądarkę z serwerem łącznika i spróbuj uzyskać dostęp do aplikacji. Jeśli łączników jest uruchamiany na platformie Azure, ale aplikacje są lokalne, środowisko użytkownika może nie być spodziewać się użytkownicy.
* **Kontrolery domeny**: Jeśli łączniki wykonać logowanie Jednokrotne przy użyciu ograniczone delegowanie protokołu Kerberos, ich skontaktuj się z kontrolerami domeny przed wysłaniem żądania do wewnętrznej bazy danych. Łączniki mają pamięci podręcznej biletów Kerberos, ale w środowisku zajęty czas odpowiedzi kontrolerów domeny może wpłynąć na wydajność. Ten problem nie zostanie częściej łączników, uruchom na platformie Azure, które komunikują się z kontrolerami domeny z lokalnymi. 

Aby uzyskać więcej informacji o optymalizacji sieci, zobacz [zagadnienia dotyczące topologii sieci, używając serwera Proxy usługi Azure Active Directory aplikacji](application-proxy-network-topology-considerations.md).

## <a name="domain-joining"></a>Przyłączanie do domeny

Łączniki można uruchomić na komputerze, który nie jest przyłączony do domeny. Jeśli chcesz rejestracji jednokrotnej (SSO) do aplikacji używających zintegrowanego uwierzytelniania systemu Windows (IWA), jednak maszynie przyłączone do domeny. W takim przypadku łącznik maszyny musi być przyłączony do domeny, która może przeprowadzać [Kerberos](https://web.mit.edu/kerberos) delegowania ograniczonego w imieniu użytkowników dla opublikowanych aplikacji.

Łączniki mogą być również częścią domen i lasów, które są w częściowej relacji zaufania, lub do kontrolerów domeny tylko do odczytu.

## <a name="connector-deployments-on-hardened-environments"></a>Łącznik wdrożeń w środowiskach ze wzmocnionymi zabezpieczeniami

Zazwyczaj wdrażanie łącznika jest proste i nie wymaga specjalnej konfiguracji. Istnieją jednak niektóre unikatowe warunki, które należy wziąć pod uwagę:

* Organizacje, które ograniczyć ruch wychodzący musi [otworzyć porty wymagane](active-directory-application-proxy-enable.md#open-your-ports).
* Może być konieczne maszyny zgodne ze standardem FIPS można zmienić ich konfiguracji umożliwia procesów łącznika do generowania i przechowywania certyfikatu.
* Organizacje, które zablokowanie środowisku oparta na procesach, które wysyłania żądań sieci należy się, że obie te usługi łącznika jest włączony dostęp do wszystkich wymagane porty i adresy IP.
* W niektórych przypadkach wychodzącego do przodu serwery proxy mogą przerwanie dwukierunkowe certyfikatu uwierzytelniania i spowodować, że komunikacja się nie powieść.

## <a name="connector-authentication"></a>Łącznik uwierzytelniania

Do obsługi bezpiecznego, trzeba uwierzytelniania kierunku usługi łączników, a usługa ma uwierzytelnianie kierunku łącznika. To uwierzytelnianie jest wykonywane przy użyciu certyfikatów klienta i serwera, gdy łączniki inicjują połączenia. Dzięki temu nazwa użytkownika i hasło administratora nie są przechowywane na komputerze łącznika.

Certyfikaty używane są specyficzne dla usługi serwera Proxy aplikacji. Tworzone podczas wstępnej i są automatycznie odnawiane przez łączniki co kilka miesięcy. 

Jeśli łącznik nie jest połączony z usługą przez kilka miesięcy, swoje certyfikaty mogą być nieaktualne. W takim przypadku odinstalowanie i ponowne zainstalowanie łącznika w celu rejestracji wyzwalaczy. Można uruchomić następujące polecenia programu PowerShell:

```
Import-module AppProxyPSModule
Register-AppProxyConnector
```

## <a name="under-the-hood"></a>Kulisy

Łączniki są oparte na Windows serwer Proxy aplikacji sieci Web, więc większość tego samego narzędzia do zarządzania tym dzienniki zdarzeń systemu Windows

 ![Zarządzanie dziennikami zdarzeń za pomocą Podglądu zdarzeń](./media/application-proxy-understand-connectors/event-view-window.png)

i liczników wydajności systemu Windows. 

 ![Dodawanie liczników do łącznika z Monitora wydajności](./media/application-proxy-understand-connectors/performance-monitor.png)

Łączniki ma zarówno administratora, jak i sesji dzienników. Dzienniki administracyjne obejmują zdarzenia kluczy i ich błędy. Dzienniki sesji obejmują wszystkie transakcje i ich szczegóły przetwarzania. 

Aby wyświetlić dzienniki, przejdź do podglądu zdarzeń, otwórz **widoku** menu, a następnie włączyć **wyświetlanie analityczne i debugowania dzienniki**. Następnie włącz je, aby zbieranie zdarzeń. Te dzienniki nie występują na liście serwera Proxy aplikacji sieci Web w systemie Windows Server 2012 R2, jak łączniki są oparte na nowszą wersję.

Można zbadać stanu usługi w oknie usługi. Łącznik obejmuje dwie usługi systemu Windows: rzeczywiste łącznika i aktualizacji. Obie z nich należy uruchomić cały czas.

 ![AzureAD Services Local](./media/application-proxy-understand-connectors/aad-connector-services.png)

## <a name="next-steps"></a>Kolejne kroki


* [Publikowanie aplikacji w odrębnych sieci i lokalizacje przy użyciu grup łącznika](active-directory-application-proxy-connectors-azure-portal.md)
* [Praca z istniejącym lokalnych serwerów proxy](application-proxy-working-with-proxy-servers.md)
* [Rozwiązywanie problemów z serwera Proxy aplikacji i łącznika](active-directory-application-proxy-troubleshoot.md)
* [Jak zainstalować łącznik serwera Proxy aplikacji Azure AD w trybie dyskretnym](active-directory-application-proxy-silent-installation.md)

