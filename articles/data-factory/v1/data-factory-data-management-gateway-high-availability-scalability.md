---
title: Wysoką dostępność z brama zarządzania danymi w fabryce danych Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak można skalować w poziomie bramy zarządzania danymi, dodając więcej węzłów i skalowania w górę zwiększając liczbę współbieżnych zadań, które można uruchomić w węźle.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 2d636eb381710c5e8341e24af8cd157a627348ef
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37053840"
---
# <a name="data-management-gateway---high-availability-and-scalability-preview"></a>Brama zarządzania danymi - wysokiej dostępności i skalowalności (wersja zapoznawcza)
> [!NOTE]
> Ten artykuł dotyczy wersji 1 fabryki danych. Jeśli używasz bieżącą wersję usługi fabryka danych, zobacz [hosta samodzielnego środowiska uruchomieniowego integracji w](../create-self-hosted-integration-runtime.md). 


Ten artykuł pomaga skonfigurować bramę zarządzania danymi wysokiej dostępności i skalowalności rozwiązania / integracji.    

> [!NOTE]
> W tym artykule przyjęto założenie, że znasz już podstawy integrację środowiska uruchomieniowego (wcześniej brama zarządzania danymi). Jeśli nie masz, zobacz [brama zarządzania danymi](data-factory-data-management-gateway.md).

>**Ta funkcja podglądu oficjalnie jest obsługiwana na 2.12.xxxx.x wersja bramy zarządzania danymi i nowszym**. Upewnij się, że używasz wersji 2.12.xxxx.x lub nowszej. Pobierz najnowszą wersję bramy zarządzania danymi [tutaj](https://www.microsoft.com/download/details.aspx?id=39717).

## <a name="overview"></a>Przegląd
Można skojarzyć dane zarządzania bram, które są zainstalowane na wielu komputerach lokalnych z pojedynczą bramą logicznych z portalu. Te komputery są nazywane **węzłów**. Można mieć maksymalnie **czterech węzłów** skojarzone z bramą logiczne. Zalety mające wiele węzłów (lokalnymi maszynami z bramą zainstalowany) dla bramy logiczne to:  

- Poprawianie wydajności przenoszenia danych między lokalnymi i w chmurze magazynów danych.  
- Jeśli jeden z węzłów ulegnie awarii jakiegoś powodu inne węzły są nadal dostępne do przenoszenia danych. 
- Jeśli jeden z węzłów trzeba przełączone do trybu offline w celu przeprowadzenia konserwacji, pozostałe węzły są nadal dostępne do przenoszenia danych.

Można również skonfigurować liczbę **zadania przepływu danych równoczesnych** który można uruchomić w węźle na skalowanie w górę możliwość przenoszenia danych między lokalnymi i w chmurze magazynów danych. 

Przy użyciu portalu Azure, można monitorować stan tych węzłów, który pomoże Ci zdecydować, czy można dodać lub usunąć węzeł z logiczną bramy. 

## <a name="architecture"></a>Architektura 
Następujący diagram zawiera omówienie architektury skalowalność i dostępność funkcji brama zarządzania danymi: 

![Brama zarządzania danymi - wysokiej dostępności i skalowalności](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-high-availability-and-scalability.png)

A **logicznej bramy** jest bramy, Dodaj do fabryki danych w portalu Azure. Wcześniej można skojarzyć tylko jedną na lokalnej maszynie systemu Windows z zainstalowana brama logicznej bramy zarządzania danymi. To lokalna maszyna bramy jest nazywany węzłem. Teraz możesz skojarzyć do **czterech węzłów fizycznych** logicznej bramy. Nosi nazwę logiczną bramy z wielu węzłów **bramy wielowęzłowego**.  

Te węzły są **active**. Wszystkie one może przetworzyć zadania przepływu danych do przenoszenia danych między lokalnymi i w chmurze magazynów danych. Jednym z węzłów pełnić zarówno dyspozytora i proces roboczy. Inne węzły w grupach są węzłami procesów roboczych. A **dyspozytora** węzeł ściąga przepływ zadań/zadania danych z usługi w chmurze i wysyła je z węzłami procesu roboczego (w tym sam). A **procesu roboczego** węzeł wykonuje zadania przepływu danych do przenoszenia danych między lokalnymi i w chmurze magazynów danych. Wszystkie węzły są pracowników. Tylko jeden węzeł może być zarówno wysyłania i proces roboczy.    

Zazwyczaj można rozpocząć z jednym węzłem i **skalowanie** można dodać więcej węzłów zgodnie z istniejących węzłów jest przeciążony z ładowania przepływu danych. Możesz również **skalowanie w górę** możliwość przenoszenia danych węzła bramy, zwiększając liczbę współbieżnych zadań, które mogą być uruchamiane na węźle. Ta funkcja jest również dostępna przy użyciu bramy jednowęzłowej (nawet jeśli nie włączono funkcji skalowalność i dostępność). 

Brama z wielu węzłów przechowuje poświadczeń magazynu danych zsynchronizowane we wszystkich węzłach. Jeśli występuje problem łączności między węzłami, poświadczenia mogą być zsynchronizowane. Podczas ustawiania poświadczeń na lokalnym magazynem danych, który używa bramy zapisuje poświadczeń w węźle dyspozytora/proces roboczy. Węzeł dyspozytora synchronizacje z innych węzłów procesu roboczego. Ten proces jest nazywany **Poświadczenia synchronizacji**. Kanał komunikacyjny między węzłami może być **zaszyfrowanych** za pomocą publicznego certyfikatu SSL/TLS. 

## <a name="set-up-a-multi-node-gateway"></a>Skonfiguruj bramę wieloma węzłami.
W tej sekcji założono, że przeszły za pomocą następujących dwóch artykułów lub dobrze znany z założenia w tych artykułach: 

- [Brama zarządzania danymi](data-factory-data-management-gateway.md) — szczegółowy przegląd bramy.
- [Przenoszenie danych między lokalnymi i w chmurze magazyny danych](data-factory-move-data-between-onprem-and-cloud.md) — zawiera przewodnik krok po kroku instrukcje korzystania z jednego węzła przy użyciu bramy.  

> [!NOTE]
> Przed zainstalowaniem bramy zarządzania danymi na lokalnym komputerze z systemem Windows, zobacz wymagania wstępne wymienione w [główny artykuł](data-factory-data-management-gateway.md#prerequisites).

1. W [wskazówki](data-factory-move-data-between-onprem-and-cloud.md#create-gateway), podczas tworzenia logicznej bramy, Włącz **zapewniające wysoką dostępność i skalowalność** funkcji. 

    ![Brama zarządzania danymi - enable wysokiej dostępności i skalowalności](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-enable-high-availability-scalability.png)
2. W **Konfiguruj** albo użyj **Express Instalator** lub **Podręcznika instalacji** łącze, aby zainstalować bramę na pierwszym węźle (komputer Windows lokalnych).

    ![Brama zarządzania danymi - instalacji ekspresowej, czy ręcznie](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-express-manual-setup.png)

    > [!NOTE]
    > Jeśli używasz opcji instalacji ekspresowej, komunikacja między węzłami odbywa się bez szyfrowania. Nazwa węzła jest taka sama jak nazwa komputera. Użyj instalacji ręcznej, jeśli komunikacja węzeł węzeł musi być szyfrowane lub aby określić nazwę węzła wybranych przez użytkownika. Nie można później edytować nazwy węzła.
3. Jeśli wybierzesz **instalacji ekspresowej**
    1. Po pomyślnym zainstalowaniu bramy zostanie wyświetlony następujący komunikat:

        ![Brama zarządzania danymi — powodzenie instalacji ekspresowej](media/data-factory-data-management-gateway-high-availability-scalability/express-setup-success.png)
    2. Uruchom Menedżera konfiguracji zarządzania danymi dla bramy, wykonując [tych instrukcji](data-factory-data-management-gateway.md#configuration-manager). Zostanie wyświetlony nazwa bramy, nazwa węzła, stan, itp.

        ![Brama zarządzania danymi - Instalacja powiodła się](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)
4. Jeśli wybierzesz **instalacji ręcznej**:
    1. Aby pobrać pakiet instalacyjny z Microsoft Download Center, uruchom go, aby zainstalować bramę na tym komputerze.
    2. Użyj **klucz uwierzytelniania** z **Konfiguruj** strony, aby zarejestrować bramę.
    
        ![Brama zarządzania danymi - Instalacja powiodła się](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-authentication-key.png)
    3. W **nowy węzeł bramy** strony, musisz podać niestandardowy **nazwa** do węzła bramy. Domyślnie nazwa węzła jest taka sama jak nazwa komputera.    

        ![Brama zarządzania danymi — Określ nazwę](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-name.png)
    4. Na następnej stronie można wybrać opcję **włączenie szyfrowania komunikacji między węzłami**. Kliknij przycisk **Pomiń** umożliwia wyłączenie szyfrowania (ustawienie domyślne).

        ![Brama zarządzania danymi - Włącz szyfrowanie](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-node-encryption.png)  
    
        > [!NOTE]
        > Zmianę trybu szyfrowania jest obsługiwana tylko, gdy węzeł pojedyncza brama logicznej bramy. Aby zmienić tryb szyfrowania bramy ma wiele węzłów, wykonaj następujące kroki: Usuń wszystkie węzły z wyjątkiem jednego węzła, Zmień tryb szyfrowania, a następnie ponownie Dodaj węzły.
        > 
        > Zobacz [wymagania dotyczące certyfikatów protokołu TLS/SSL](#tlsssl-certificate-requirements) sekcja zawiera listę wymagań dotyczących korzystania z certyfikatu TLS/SSL. 
    5. Po pomyślnym zainstalowaniu bramy, kliknij przycisk Uruchom Menedżera konfiguracji:
    
        ![Instalacji ręcznej — Uruchom Menedżera konfiguracji](media/data-factory-data-management-gateway-high-availability-scalability/manual-setup-launch-configuration-manager.png)   
    6. Menedżer konfiguracji bramy zarządzania danymi zostanie wyświetlony w węźle (na lokalnej maszynie z systemem Windows), który wyświetla stan łączności, **nazwa bramy**, i **nazwa węzła**.  

        ![Brama zarządzania danymi - Instalacja powiodła się](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)

        > [!NOTE]
        > W przypadku udostępniania bramy na maszynie Wirtualnej platformy Azure, można użyć [tego szablonu usługi Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-mutiple-vms-with-data-management-gateway). Ten skrypt tworzy logicznej bramy, ustawia maszyn wirtualnych z zainstalowanym oprogramowaniem brama zarządzania danymi i rejestruje logicznej bramy. 
6. W portalu Azure, uruchom program **bramy** strony: 
    1. Na stronie głównej fabryki danych w portalu, kliknij przycisk **połączonych usług**.
    
        ![Strona główna fabryki danych](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-home-page.png)
    2. Wybierz **bramy** wyświetlić **bramy** strony:
    
        ![Strona główna fabryki danych](media/data-factory-data-management-gateway-high-availability-scalability/linked-services-gateway.png)
    4. Zostanie wyświetlony **bramy** strony:   

        ![Brama z jednego węzła widoku](media/data-factory-data-management-gateway-high-availability-scalability/gateway-first-node-portal-view.png) 
7. Kliknij przycisk **Dodaj węzeł** na pasku narzędzi, aby dodać węzeł do logicznego bramy. Jeśli planujesz użyć instalacji ekspresowej, należy wykonać ten krok z komputera lokalnego, który zostanie dodany jako węzeł do bramy. 

    ![Brama zarządzania danymi — Dodaj menu węzła](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)
8. Kroki są podobne do konfigurowania pierwszy węzeł. Interfejs użytkownika programu Configuration Manager umożliwia określenie nazwy węzła, po wybraniu opcji ręcznej instalacji: 

    ![Configuration Manager — instalacji drugiego bramy](media/data-factory-data-management-gateway-high-availability-scalability/install-second-gateway.png)
9. Po bramy jest pomyślnie zainstalowane w węźle, narzędzia programu Configuration Manager wyświetla następujący ekran:  

    ![Configuration Manager — instalacja bramy drugi powiodło się](media/data-factory-data-management-gateway-high-availability-scalability/second-gateway-installation-successful.png)
10. Po otwarciu **bramy** strony w portalu, można zobaczyć dwa węzły bramy teraz: 

    ![Brama z dwoma węzłami w portalu](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)
11. Aby usunąć węzeł bramy, kliknij przycisk **usunąć węzeł** na pasku narzędzi wybierz węzeł, który chcesz usunąć, a następnie kliknij przycisk **usunąć** z paska narzędzi. Ta akcja spowoduje usunięcie wybranego węzła z grupy. Należy pamiętać, że ta akcja nie powoduje odinstalowania oprogramowania bramy danych zarządzania z węzła (komputera lokalnego systemu Windows). Użyj **apletu Dodaj lub usuń programy** w Panelu sterowania na lokalnej, aby odinstalować bramę. Brama jest odinstalowywana z węzła, są automatycznie usuwane w portalu.   

## <a name="upgrade-an-existing-gateway"></a>Uaktualnij istniejącą bramę
Można uaktualnić istniejącą bramę do użycia funkcji wysokiej dostępności i skalowalności. Ta funkcja działa tylko w przypadku węzłów posiadających brama zarządzania danymi w wersji > = 2.12.xxxx. Można wyświetlić wersję bramy zarządzania danymi zainstalowany na komputerze w **pomocy** kartę z danych Menedżera konfiguracji bramy zarządzania. 

1. Zaktualizuj bramę na maszynie lokalnej do najnowszej wersji następuje pobierając i uruchamiania Instalatora MSI Instalatora pakietu z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). Zobacz [instalacji](data-factory-data-management-gateway.md#installation) sekcji, aby uzyskać szczegółowe informacje.  
2. Przejdź do portalu Azure. Uruchom **strony fabryki danych** Twojego fabryki danych. Kliknij Kafelek usługi połączonej, aby uruchomić **strony połączonych usług**. Wybierz bramę, aby uruchomić **strony bramy**. Kliknij i włączyć **funkcja w wersji zapoznawczej** jak pokazano na poniższej ilustracji: 

    ![Brama zarządzania danymi - enable funkcja w wersji zapoznawczej](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-existing-gateway-enable-high-availability.png)   
2. Po włączeniu funkcji wersji zapoznawczej w portalu, zamknij wszystkie strony. Otwórz ponownie **strony bramy** wyświetlić nowy podgląd interfejs użytkownika (UI).
 
    ![Brama zarządzania danymi - Włącz podgląd funkcji Powodzenie](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview-success.png)

    ![Brama zarządzania danymi - preview interfejsu użytkownika](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview.png)

    > [!NOTE]
    > Podczas uaktualniania Nazwa pierwszego węzła jest nazwa komputera. 
3. Teraz Dodaj węzeł. W **bramy** kliknij przycisk **Dodaj węzeł**.  

    ![Brama zarządzania danymi — Dodaj menu węzła](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)

    Wykonaj instrukcje z poprzedniej sekcji, aby skonfigurować w węźle. 

### <a name="installation-best-practices"></a>Najlepsze rozwiązania dotyczące instalacji

- Skonfiguruj planu zasilania na komputerze hosta dla bramy, dzięki czemu komputer nie hibernacji. Jeśli komputer hosta przejdzie w stan hibernacji, brama nie odpowiada na żądania danych.
- Utwórz kopię zapasową certyfikatu skojarzonego z bramą.
- Upewnij się, że wszystkie węzły są podobne konfiguracji (zalecane) doskonale wydajności. 
- Dodaj co najmniej dwa węzły, aby zapewnić wysoką dostępność.  

### <a name="tlsssl-certificate-requirements"></a>Wymagania dotyczące certyfikatów dla protokołu TLS/SSL
Poniżej przedstawiono wymagania dotyczące certyfikatu TLS/SSL, który służy do zabezpieczania komunikacji między integrację środowiska uruchomieniowego węzłów:

- Certyfikat musi być publicznie zaufany X509 certyfikatu w wersji 3. Firma Microsoft zaleca, aby używać certyfikatów wystawionych przez publiczny (niezależny) urząd certyfikacji (CA).
- Każdy węzeł środowiska uruchomieniowego integracji muszą ufać tego certyfikatu, komputer kliencki z systemem aplikacji Menedżera poświadczeń. 
> [!NOTE]
> Aplikacja Menedżer poświadczeń jest używany podczas bezpiecznego ustawiania poświadczeń z Kreatora kopiowania / portalu Azure. I może to być wypalane z dowolnego komputera w ramach tej samej sieci co lokalnego / prywatnej magazynu danych.
- Symbol wieloznaczny certyfikaty są obsługiwane. Jeśli nazwa FQDN to **node1.domain.contoso.com**, można użyć ***. domain.contoso.com** jako nazwa podmiotu certyfikatu.
- Certyfikaty sieci SAN nie jest zalecane, ponieważ będą używane tylko ostatni element alternatywnej nazwy podmiotu, a wszystkie pozostałe zostaną pominięte ze względu na bieżące ograniczenia. Na przykład mieć certyfikat SAN którego SAN są **node1.domain.contoso.com** i **node2.domain.contoso.com**, tego certyfikatu można używać tylko na komputerze, którego nazwa FQDN to **node2.domain.contoso.com**.
- Obsługuje wszystkie rozmiar klucza obsługiwana przez system Windows Server 2012 R2 dla certyfikatów SSL.
- Certyfikat przy użyciu CNG klucze nie są obsługiwane. Doesrted DoesDoes nie obsługuje certyfikatów, które używają kluczy CNG.

#### <a name="faq-when-would-i-not-enable-this-encryption"></a>Często zadawane pytania: Gdy I nie umożliwia szyfrowanie?
Włączenie szyfrowania, można dodać pewnych kosztów infrastruktury (będący właścicielem certyfikatu publicznego) dlatego może pominąć włączenie szyfrowania w poniższych przypadków:
- Gdy środowiska uruchomieniowego integracji jest uruchomiony w zaufanej sieci lub sieci z przezroczystego szyfrowania, takich jak IP/s. Ponieważ kanał komunikacji jest tylko ograniczony w zaufanej sieci, mogą nie być potrzebne dodatkowe szyfrowania.
- Podczas integracji środowiska uruchomieniowego nie działa w środowisku produkcyjnym. Może to pomóc w ograniczeniu kosztów certyfikatu TLS/SSL.


## <a name="monitor-a-multi-node-gateway"></a>Monitor bramy wieloma węzłami.
### <a name="multi-node-gateway-monitoring"></a>Monitorowanie bramy z wieloma węzłami.
W portalu Azure można wyświetlić migawki czasie niemal rzeczywistym wykorzystania zasobów (procesora CPU, pamięci, network(in/out) itp.) w każdym węźle wraz ze Stanami węzłów bramy. 

![Brama zarządzania danymi - monitorowania wielu węzła](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)

Można włączyć **ustawienia zaawansowane** w **bramy** stronę, aby zobaczyć zaawansowane metryk, takich jak **sieci**(We/Wy), **roli & stan poświadczeń**, co jest przydatne w debugowaniu problemów bramy i **równoczesnych zadań** (systemem / Limit) który można być zmodyfikowane / odpowiednio zmienione podczas dostrajania wydajności. Poniższa tabela zawiera opisy kolumn w **węzłów bramy** listy:  

Właściwość monitorowania | Opis
:------------------ | :---------- 
Name (Nazwa) | Nazwa bramy logiczne i skojarzone z tą bramą węzłów.  
Stan | Stan bramy logicznych i węzłów bramy. Przykład: Online/Offline/Limited/itp. Informacje o tych stanów znajdują się w temacie [stanu bramy](#gateway-status) sekcji. 
Wersja | Wyświetlana jest wersja bramy logicznych i każdy węzeł bramy. Wersja bramy logicznej jest określana na podstawie wersji Większość węzłów w grupie. Jeśli ma prawidłowo węzłów z różnych wersji w konfiguracji bramy logiczne, tylko węzły z tym samym numerem wersji funkcji logicznej bramy. Inne osoby włączony jest tryb ograniczony i należy ręcznie zaktualizować (tylko w przypadku automatycznej aktualizacji nie powiedzie się). 
Dostępna pamięć | Dostępna pamięć na węzeł bramy. Ta wartość jest blisko migawka w czasie rzeczywistym. 
Użycie procesora CPU | Użycie procesora CPU przez węzeł bramy. Ta wartość jest blisko migawka w czasie rzeczywistym. 
Sieć (We/Wy) | Użycie sieci węzeł bramy. Ta wartość jest blisko migawka w czasie rzeczywistym. 
Równoczesnych zadań (systemem / Limit) | Liczba zadań lub zadania uruchomione w każdym węźle. Ta wartość jest blisko migawka w czasie rzeczywistym. Limit oznacza maksymalny współbieżnych zadań dla każdego węzła. Ta wartość jest określona na podstawie rozmiaru maszyny. Można zwiększyć limit skalowanie w górę wykonywania zadań jednoczesnych w zaawansowanych scenariuszach, gdzie Procesora / pamięci / sieci jest używane w obszarze, ale limit czasu są działania. Ta funkcja jest również dostępna przy użyciu bramy jednowęzłowej (nawet jeśli nie włączono funkcji skalowalność i dostępność). Aby uzyskać więcej informacji, zobacz [skalować zagadnienia](#scale-considerations) sekcji. 
Rola | Istnieją dwa typy ról — dyspozytora i proces roboczy. Wszystkie węzły są pracowników, co oznacza, że ich wszystkich służy do wykonywania zadań. Istnieje tylko jeden węzeł dyspozytora, który służy do pobierania zadania/zadań z usługi w chmurze i wysyłania ich do węzłów innego procesu roboczego (w tym sam). 

![Brama zarządzania danymi - zaawansowane monitorowanie wielu węzła](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-advanced.png)

### <a name="gateway-status"></a>Stan bramy

W poniższej tabeli przedstawiono możliwe stany **węzeł bramy**: 

Stan  | Komentarz/scenariuszy
:------- | :------------------
Online | Węzeł jest podłączony do usługi fabryki danych.
Offline | Węzeł jest w trybie offline.
Uaktualnianie | Węzeł jest aktualizowane automatycznie.
Ograniczone | Z powodu problemu z łącznością. Może być z powodu problemu 8050 port HTTP, problem dotyczący łączności magistrali usługi lub problemu z synchronizacją poświadczeń. 
Nieaktywna | Węzeł znajduje się w różnych konfiguracji innych węzłów większość konfiguracji.<br/><br/> Węzeł może być nieaktywne, gdy nie można połączyć do innych węzłów. 


W poniższej tabeli przedstawiono możliwe stany **logicznej bramy**. Stan bramy zależy od stany węzłów bramy. 

Stan | Komentarze
:----- | :-------
Wymaga rejestracji | Żaden węzeł nie jest jeszcze zarejestrowana dla tej bramy logiczne
Online | Węzły bramy są w trybie online
Offline | Nie węzła w stan online.
Ograniczone | Nie wszystkie węzły w tej bramy są w dobrej kondycji. Ten stan jest ostrzeżenie, że niektóre węzeł może być wyłączony! <br/><br/>Może wynikać z problemu z synchronizacją poświadczeń w węźle dyspozytora/proces roboczy. 

### <a name="pipeline-activities-monitoring"></a>Potok / działania monitorowania
Azure portal udostępnia potoku monitorowania środowisko z poziomu szczegółów szczegółowego węzła. Na przykład przedstawia działania uruchomionego na wybór węzła. Te informacje mogą być pomocne w opis problemy z wydajnością w określonym węźle, co oznacza ze względu na ograniczenia przepustowości sieci. 

![Brama zarządzania danymi - monitorowania dla potoków wiele węzłów](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-pipelines.png)

![Brama zarządzania danymi — szczegóły potoku](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-pipeline-details.png)

## <a name="scale-considerations"></a>Zagadnienia dotyczące skali

### <a name="scale-out"></a>Skalowanie w poziomie
Gdy **jest za mało dostępnej pamięci** i **użycie procesora CPU jest wysokie**, dodanie nowego węzła pomaga skalowania obciążenia na komputerach. Jeśli działania kończą się niepowodzeniem z powodu przekroczenia limitu czasu lub bramy węzła w trybie offline, pomaga w przypadku dodawania węzła do bramy.
 
### <a name="scale-up"></a>Skalowanie w górę
Gdy ilość dostępnej pamięci i Procesora nie są używane również, ale pojemność bezczynności wynosi 0, należy skalowanie w górę zwiększając liczbę współbieżnych zadań, które można uruchomić w węźle. Można również skalować podczas działania są limit czasu, ponieważ brama jest przeciążona. Jak pokazano na poniższej ilustracji, można zwiększyć maksymalną pojemność dla węzła. Zalecamy podwojenie go do uruchomienia z.  

![Brama zarządzania danymi — zagadnienia dotyczące skali](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-scale-considerations.png)


## <a name="known-issuesbreaking-changes"></a>Znane problemy/najważniejszych zmian

- Obecnie może mieć maksymalnie cztery węzły fizyczne bramy dla jednej bramy logiczne. Jeśli potrzebujesz więcej niż czterech węzłów ze względu na wydajność, Wyślij wiadomość e-mail do [ DMGHelp@microsoft.com ](mailto:DMGHelp@microsoft.com).
- Nie można ponownie zarejestrować węzeł bramy z kluczem uwierzytelniania z inną bramę logicznej przejść z bieżącej logicznej bramy. Aby ponownie zarejestrować odinstalować bramę z węzła, zainstaluj ponownie bramę i zarejestrowanie go za pomocą klucza uwierzytelniania logicznej bramy. 
- Jeśli serwer proxy HTTP jest wymagane dla wszystkich węzłów bramy, ustawić serwera proxy w diahost.exe.config i diawp.exe.config i upewnij się, że wszystkie węzły mają taką samą diahost.exe.config i diawip.exe.config przy użyciu Menedżera serwera. Zobacz [Skonfiguruj ustawienia serwera proxy](data-factory-data-management-gateway.md#configure-proxy-server-settings) sekcji, aby uzyskać szczegółowe informacje. 
- Aby zmienić tryb szyfrowania komunikacji między węzłami w Menedżerze konfiguracji bramy, należy usunąć wszystkie węzły w portalu, z wyjątkiem jednego. Następnie należy dodać węzły wstecz po zmianie trybu szyfrowania.
- Użyj oficjalnego certyfikatu SSL, jeśli chcesz zaszyfrować kanał komunikacyjny między węzłami. Certyfikat z podpisem własnym może spowodować problemy z łącznością, zgodnie z tego samego certyfikatu może nie być uważany za zaufany liście urząd certyfikacji na innych komputerach. 
- Nie można zarejestrować węzeł bramy do logicznego bramy, gdy wersja węzła jest starsza niż wersja logicznej bramy. Usuń wszystkie węzły logicznej bramy z portalu, tak aby niższe node(downgrade) wersji można zarejestrować go. Po usunięciu wszystkich węzłów bramy logicznej ręcznie zainstaluj i Zarejestruj nowe węzły do tej bramy logiczne. Instalacja ekspresowa nie jest obsługiwana w tym przypadku.
- Aby zainstalować węzły do istniejącej bramy logiczne, które nadal korzysta z poświadczeń w chmurze, nie można użyć instalacji ekspresowej. Możesz sprawdzić, gdzie poświadczenia są przechowywane z Menedżera konfiguracji bramy na karcie Ustawienia.
- Aby zainstalować węzły do istniejącej bramy logiczne, które ma włączone szyfrowanie węzła do węzła, nie można użyć instalacji ekspresowej. Jak ustawianie trybu szyfrowania obejmuje ręczne dodanie certyfikaty, instalacja ekspresowa ma więcej opcji. 
- Dla kopiowania plików ze środowiska lokalnego, nie należy używać \\nazwy localhost ani C:\files już od hosta lokalnego lub na dysku lokalnym może nie być dostępny za pośrednictwem wszystkich węzłów. Zamiast tego należy użyć \\ServerName\files do określenia lokalizacji plików.


## <a name="rolling-back-from-the-preview"></a>Wycofywanie w wersji zapoznawczej 
Aby przywrócić w wersji zapoznawczej, należy usunąć wszystkie węzły oprócz jednego węzła. Nie ma znaczenia węzły, które można usunąć, ale upewnij się, że co najmniej jeden węzeł w logicznej bramy. Odinstalowanie bramy na maszynie lub przy użyciu portalu Azure, można usunąć węzła. W portalu Azure w **fabryki danych** kliknij połączone usługi, aby uruchomić **połączone usługi** strony. Wybierz bramę, aby uruchomić **bramy** strony. Na stronie bramy można zobaczyć węzły skojarzone z tą bramą. Strona służy do usuwania węzła z bramy.
 
Po usunięciu, kliknij przycisk **funkcji w wersji zapoznawczej** w tej samej stronie portalu Azure i wyłączenie funkcji podglądu. Zresetowaniu bramę do jednej bramy (ogólnej dostępności) GA węzła.


## <a name="next-steps"></a>Kolejne kroki
Następujące artykuły:
- [Brama zarządzania danymi](data-factory-data-management-gateway.md) — szczegółowy przegląd bramy.
- [Przenoszenie danych między lokalnymi i w chmurze magazyny danych](data-factory-move-data-between-onprem-and-cloud.md) — zawiera przewodnik krok po kroku instrukcje korzystania z jednego węzła przy użyciu bramy. 
