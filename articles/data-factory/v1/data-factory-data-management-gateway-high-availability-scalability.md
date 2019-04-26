---
title: Wysoka dostępność bramy zarządzania danymi w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: W tym artykule wyjaśniono, jak można skalować w poziomie bramy zarządzania danymi, dodając więcej węzłów i skalowania w górę, zwiększając liczbę współbieżnych zadań, które można uruchomić w węźle.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 08e7341bfd1c384e41e6d3f1bd7810552899849a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60488950"
---
# <a name="data-management-gateway---high-availability-and-scalability-preview"></a>Brama zarządzania danymi — wysokiej dostępności i skalowalności (wersja zapoznawcza)
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [może być samodzielnie hostowane środowisko IR w](../create-self-hosted-integration-runtime.md). 


Ten artykuł pomoże Ci skonfigurować rozwiązania wysokiej dostępności i skalowalności przy użyciu bramy zarządzania danymi / integracji.    

> [!NOTE]
> W tym artykule założono, że znasz już podstawy środowiska Integration Runtime (wcześniej brama zarządzania danymi). Jeśli nie masz, zobacz [bramy zarządzania danymi](data-factory-data-management-gateway.md).
> 
> **Tę funkcję wersji zapoznawczej oficjalnie jest obsługiwana w 2.12.xxxx.x wersji bramy zarządzania danymi i nowszych**. Upewnij się, że używasz wersji 2.12.xxxx.x lub nowszej. Pobierz najnowszą wersję bramy zarządzania danymi [tutaj](https://www.microsoft.com/download/details.aspx?id=39717).

## <a name="overview"></a>Omówienie
Możesz skojarzyć bramy zarządzania danych, które są zainstalowane na wielu komputerach w środowisku lokalnym za pomocą jednej logicznej bramy w portalu. Te maszyny są nazywane **węzłów**. Możesz mieć maksymalnie **czterech węzłów** skojarzone z logicznej bramy. Zalety o wielu węzłach (maszynach lokalnych z zainstalowaną bramę) dla bramy sieci logiczne to:  

- Poprawianie wydajności przenoszenia danych między lokalizacją lokalną i chmurą magazynów danych.  
- Jeśli jeden z węzłów ulegnie awarii jakiegoś powodu, inne węzły są nadal dostępne do przenoszenia danych. 
- Jeśli jeden z węzłów potrzeba przełączenia w tryb offline w celu przeprowadzenia konserwacji, inne węzły są nadal dostępne do przenoszenia danych.

Możesz również skonfigurować liczbę **zadania przepływu danych współbieżnych** uruchomioną w węźle, aby skalować w górę możliwości przenoszenia danych między lokalizacją lokalną i chmurą magazynów danych. 

W witrynie Azure portal, można monitorować stan tych węzłów, które ułatwi Ci zdecydować, czy chcesz dodać lub usunąć węzeł z logicznej bramy. 

## <a name="architecture"></a>Architektura 
Na poniższym diagramie przedstawiono omówienie architektury, skalowalności i dostępności funkcji bramy zarządzania danymi: 

![Brama zarządzania danymi — wysokiej dostępności i skalowalności](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-high-availability-and-scalability.png)

A **logicznej bramy** jest bramą możesz dodać do fabryki danych w witrynie Azure portal. Wcześniej można było skojarzyć tylko jedną maszynę Windows w środowisku lokalnym za pomocą bramę zarządzania danymi zainstalowaną z logicznej bramy. To lokalne maszynie bramy, jest nazywana węzłem. Teraz można skojarzyć maksymalnie **cztery węzły fizyczne** logicznej bramy. Nosi nazwę logicznej bramy z wielu węzłów **bram z wieloma węzłami**.  

Te węzły są **active**. Wszystkie one mogą przetwarzać zadania przepływu danych do przenoszenia danych między lokalizacją lokalną i chmurą magazynów danych. Jednym z węzłów pełnić rolę zarówno w przypadku dispatcher, jak i procesu roboczego. Inne węzły w grupach są węzły procesu roboczego. A **dyspozytora** węzeł ściąga dane przepływu zadań/zadania w usłudze w chmurze i wysyła je do węzłów procesu roboczego (w tym sam). A **procesu roboczego** węzeł wykonuje zadania przepływu danych do przenoszenia danych między lokalizacją lokalną i chmurą magazynów danych. Wszystkie węzły są pracowników. Tylko jeden węzeł może być zarówno wysyłki, jak i proces roboczy.    

Zazwyczaj można rozpocząć z jednym węzłem i **skalowanie w poziomie** dodanie kolejnych węzłów jako przytłoczeni istniejących węzłów z obciążeniem przenoszenia danych. Możesz również **skalowanie w górę** możliwość przenoszenia danych, węzła bramy, zwiększając liczbę współbieżnych zadań, które mogą być uruchamiane w węźle. Ta funkcja jest również dostępna za pośrednictwem bramy jednowęzłowej (nawet jeśli nie włączono funkcji skalowalności i dostępności). 

Bramy z wielu węzłów przechowuje poświadczenia magazynu danych zsynchronizowane we wszystkich węzłach. Jeśli występuje problem z łącznością między węzłami, poświadczenia może nie być zsynchronizowany. Podczas ustawiania poświadczeń dla lokalnego magazynu danych, który używa bramy zapisuje poświadczenia na węzeł dyspozytora/procesu roboczego. Węzeł dyspozytora synchronizuje się z innych węzłów procesu roboczego. Ten proces jest nazywany **Poświadczenia synchronizacji**. Kanał komunikacyjny między węzłami może być **zaszyfrowanych** przy użyciu publicznego certyfikatu SSL/TLS. 

## <a name="set-up-a-multi-node-gateway"></a>Konfigurowanie bramy wielowęzłowego
W tej sekcji założono, że wykonano za pomocą następujących dwóch artykułach lub powszechnie znane pojęcia w następujących artykułach: 

- [Brama zarządzania danymi](data-factory-data-management-gateway.md) — zawiera szczegółowe omówienie bramy.
- [Przenoszenie danych między lokalizacją lokalną i chmurą magazyny danych](data-factory-move-data-between-onprem-and-cloud.md) — zawiera przewodnik z instrukcjami krok po kroku z jednego węzła przy użyciu bramy.  

> [!NOTE]
> Przed zainstalowaniem bramy zarządzania danymi na komputerze Windows w środowisku lokalnym, zobacz wymagania wstępne wymienione we [główny artykuł](data-factory-data-management-gateway.md#prerequisites).

1. W [wskazówki](data-factory-move-data-between-onprem-and-cloud.md#create-gateway), podczas tworzenia logicznej bramy, należy włączyć **wysokiej dostępności i skalowalności** funkcji. 

    ![Brama zarządzania danymi — Włączanie wysokiej dostępności i skalowalności](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-enable-high-availability-scalability.png)
2. W **Konfiguruj** strony, należy użyć albo **instalacji ekspresowej** lub **Instalacja ręczna** łącze, aby zainstalować bramę na pierwszym węźle (maszynie Windows w środowisku lokalnym).

    ![Brama zarządzania danymi — instalacji ekspresowej, czy ręcznie](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-express-manual-setup.png)

    > [!NOTE]
    > Jeśli używasz opcji instalacji ekspresowej, komunikacji między węzłami odbywa się bez szyfrowania. Nazwa węzła jest taka sama jak nazwa komputera. Użyj instalacji ręcznej, jeśli komunikacji między węzłami, musi być zaszyfrowane lub, aby określić nazwę węzła. Nie można później edytować nazwy węzłów.
3. Jeśli wybierzesz **instalacji ekspresowej**
    1. Po pomyślnym zainstalowaniu bramy zostanie wyświetlony następujący komunikat:

        ![Brama zarządzania danymi — powodzenie instalacji ekspresowej](media/data-factory-data-management-gateway-high-availability-scalability/express-setup-success.png)
    2. Uruchom danych zarządzania Configuration Manager dla bramy, postępując zgodnie z [w instrukcjach](data-factory-data-management-gateway.md#configuration-manager). Zobaczysz nazwę bramy, nazwa węzła, stan, itp.

        ![Brama zarządzania danymi — instalacja zakończyła się pomyślnie](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)
4. Jeśli wybierzesz **instalacji ręcznej**:
    1. Pobierz pakiet instalacyjny z Microsoft Download Center, uruchom go, aby zainstalować bramę na tym komputerze.
    2. Użyj **klucz uwierzytelniania** z **Konfiguruj** strony, aby zarejestrować bramę.
    
        ![Brama zarządzania danymi — instalacja zakończyła się pomyślnie](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-authentication-key.png)
    3. W **nowy węzeł bramy** strony, można podać niestandardowy **nazwa** do węzła bramy. Domyślnie nazwa węzła jest taka sama jak nazwa komputera.    

        ![Brama zarządzania danymi — Określ nazwę](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-name.png)
    4. Na następnej stronie można wybrać opcję **włączyć szyfrowanie komunikacji między węzłami**. Kliknij przycisk **Pomiń** umożliwia wyłączenie szyfrowania (ustawienie domyślne).

        ![Brama zarządzania danymi — Włączanie szyfrowania](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-node-encryption.png)  
    
        > [!NOTE]
        > Zmiana trybu szyfrowania jest obsługiwane tylko w przypadku węzła pojedynczą bramą w logicznej bramy. Aby zmienić tryb szyfrowania bramy ma wiele węzłów, wykonaj następujące czynności: Usuń wszystkie węzły z wyjątkiem jednego węzła, zmienić trybu szyfrowania, a następnie ponownie Dodaj węzły.
        > 
        > Zobacz [wymagania certyfikatu TLS/SSL](#tlsssl-certificate-requirements) sekcja zawiera listę wymagań dotyczących korzystania z certyfikatu TLS/SSL. 
    5. Po pomyślnym zainstalowaniu bramy, kliknij przycisk Uruchom program Configuration Manager:
    
        ![Instalację ręczną — Menedżer konfiguracji uruchamiania](media/data-factory-data-management-gateway-high-availability-scalability/manual-setup-launch-configuration-manager.png)   
    6. Zobacz Menedżera konfiguracji bramy zarządzania danych na węzła (komputer Windows w środowisku lokalnym,), który wyświetla stan łączności, **nazwę bramy**, i **nazwa węzła**.  

        ![Brama zarządzania danymi — instalacja zakończyła się pomyślnie](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)

        > [!NOTE]
        > W przypadku udostępniania bramę na Maszynie wirtualnej platformy Azure, można użyć [tego szablonu usługi Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-mutiple-vms-with-data-management-gateway). Ten skrypt tworzy logicznej bramy, konfiguruje maszyny wirtualne z zainstalowanym oprogramowaniem bramy zarządzania danymi i rejestruje je za pomocą logicznej bramy. 
6. W witrynie Azure portal, należy uruchomić **bramy** strony: 
    1. Na stronie głównej fabryki danych w portalu, kliknij **połączone usługi**.
    
        ![Strona główna fabryki danych](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-home-page.png)
    2. Wybierz **bramy** się **bramy** strony:
    
        ![Strona główna fabryki danych](media/data-factory-data-management-gateway-high-availability-scalability/linked-services-gateway.png)
    4. Zostanie wyświetlony **bramy** strony:   

        ![Bramy przy użyciu widoku pojedynczego węzła](media/data-factory-data-management-gateway-high-availability-scalability/gateway-first-node-portal-view.png) 
7. Kliknij przycisk **Dodaj węzeł** na pasku narzędzi, aby dodać węzeł do logicznej bramy. Jeśli planujesz użyć instalacji ekspresowej, ten krok należy wykonać z komputera lokalnego, który zostanie dodany jako węzeł do bramy. 

    ![Brama zarządzania danymi — Dodaj węzeł menu](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)
8. Kroki są podobne do konfigurowania pierwszego węzła. Interfejs użytkownika programu Configuration Manager pozwala ustawić na nazwę węzła, jeśli wybierzesz opcję instalacji ręcznej: 

    ![Configuration Manager — druga brama instalacji](media/data-factory-data-management-gateway-high-availability-scalability/install-second-gateway.png)
9. Po zainstalowaniu bramy jest pomyślnie w węźle, narzędzie programu Configuration Manager wyświetla następujący ekran:  

    ![Configuration Manager — instalacja druga brama pomyślne](media/data-factory-data-management-gateway-high-availability-scalability/second-gateway-installation-successful.png)
10. Jeśli otworzysz **bramy** strony w portalu, możesz zobaczyć dwa węzły bramy teraz: 

    ![Bramy z dwoma węzłami w portalu](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)
11. Aby usunąć węzeł bramy, kliknij przycisk **Usuń węzeł** na pasku narzędzi, wybierz węzeł, który chcesz usunąć, a następnie kliknij przycisk **Usuń** na pasku narzędzi. Ta akcja spowoduje usunięcie wybranego węzła z grupy. Należy pamiętać, że ta akcja nie powoduje odinstalowania oprogramowania do zarządzania bramy danych z węzła (komputer Windows w środowisku lokalnym). Użyj **apletu Dodaj lub usuń programy** w Panelu sterowania na lokalne, aby odinstalować bramę. Po odinstalowaniu bramy z węzła, zostanie on automatycznie usunięty w portalu.   

## <a name="upgrade-an-existing-gateway"></a>Uaktualnianie istniejącej bramy
Można uaktualnić istniejącą bramę do użycia funkcję o wysokiej dostępności i skalowalności. Ta funkcja działa tylko w przypadku węzły, które mają bramę zarządzania danymi w wersji > = 2.12.xxxx. Możesz zobaczyć wersję bramę zarządzania danymi zainstalowaną na komputerze w **pomocy** kartę z danych Menedżera konfiguracji bramy zarządzania. 

1. Zaktualizuj bramę na maszynie lokalnej do najnowszej wersji, wykonując, pobierając i uruchamiania Instalatora MSI Instalatora pakietu na podstawie [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). Zobacz [instalacji](data-factory-data-management-gateway.md#installation) sekcji, aby uzyskać szczegółowe informacje.  
2. Przejdź do witryny Azure portal. Uruchom **strony usługi Data Factory** fabryki danych. Kliknij Kafelek usługi połączonej, aby uruchomić **strony połączone usługi**. Wybierz bramy Aby uruchomić **stronie bramy**. Kliknij przycisk, a następnie Włącz **funkcja w wersji zapoznawczej** jak pokazano na poniższej ilustracji: 

    ![Brama zarządzania danymi — Włączanie funkcji w wersji zapoznawczej](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-existing-gateway-enable-high-availability.png)   
2. Po włączeniu funkcja w wersji zapoznawczej w portalu, zamknij wszystkie strony. Otwórz ponownie **stronie bramy** się nowej wersji zapoznawczej interfejsu użytkownika (UI).
 
    ![Brama zarządzania danymi — Włączanie wersji zapoznawczej funkcji sukces](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview-success.png)

    ![Brama zarządzania danymi — wersja zapoznawcza interfejsu użytkownika](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview.png)

    > [!NOTE]
    > Podczas uaktualniania Nazwa pierwszego węzła jest nazwa komputera. 
3. Teraz Dodaj węzeł. W **bramy** kliknij **Dodaj węzeł**.  

    ![Brama zarządzania danymi — Dodaj węzeł menu](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)

    Postępuj zgodnie instrukcjami w poprzedniej sekcji, aby skonfigurować w węźle. 

### <a name="installation-best-practices"></a>Najlepsze rozwiązania dotyczące instalacji

- Na maszynie hosta dla bramy należy skonfigurować plan zasilania, tak aby maszyny bez hibernacji. Jeśli komputer hosta przechodzi w stan hibernacji, brama nie odpowiada na żądania danych.
- Utwórz kopię zapasową certyfikatu skojarzonego z tą bramą.
- Upewnij się, że wszystkie węzły są podobnej konfiguracji (zalecane), wydajność idealnym rozwiązaniem. 
- Dodaj co najmniej dwa węzły, aby zapewnić wysoką dostępność.  

### <a name="tlsssl-certificate-requirements"></a>Wymagania certyfikatu TLS/SSL
Poniżej przedstawiono wymagania dotyczące certyfikatu TLS/SSL, używany do zabezpieczania komunikacji między integration runtime węzłów:

- Certyfikat musi być publicznie zaufany X509 certyfikatu v3. Firma Microsoft zaleca użycie certyfikatów wystawionych przez publiczny (innej firmy) urząd certyfikacji (CA).
- Ten certyfikat, a także komputer klienta, na którym jest uruchomiony aplikacji Menedżer poświadczeń, muszą ufać każdej węzeł środowiska integration runtime. 
  > [!NOTE]
  > Aplikacji Menedżer poświadczeń jest używany podczas tworzenia bezpiecznego dostępu do poświadczeń za pomocą Kreatora kopiowania / witryny Azure Portal. I może to być aktywowany z dowolnego komputera w ramach tej samej sieci lokalnej / prywatnego magazynu danych.
- Certyfikaty symbole wieloznaczne są obsługiwane. Jeśli nazwa FQDN jest **node1.domain.contoso.com**, możesz użyć ***. domain.contoso.com** jako nazwę podmiotu certyfikatu.
- Certyfikaty SAN nie są zalecane, ponieważ tylko ostatni element nazwy alternatywnej podmiotu, który będzie używany, a wszystkie pozostałe zostaną zignorowane ze względu na bieżące ograniczenia. Na przykład mieć certyfikat sieci SAN, w których SAN są **node1.domain.contoso.com** i **node2.domain.contoso.com**, możesz użyć wyłącznie tego certyfikatu na komputerze, którego nazwa FQDN jest **node2.domain.contoso.com**.
- Obsługuje wszystkie rozmiar klucza obsługiwana przez system Windows Server 2012 R2 dla certyfikatów SSL.
- Certyfikat przy użyciu CNG klucze nie są obsługiwane.

#### <a name="faq-when-would-i-not-enable-this-encryption"></a>CZĘSTO ZADAWANE PYTANIA: Jeśli będzie nie włączenie szyfrowania?
Włączanie szyfrowania można dodać pewnych kosztów do infrastruktury (będącej właścicielem certyfikatu publicznego) dlatego możesz pominąć włączania szyfrowania w poniższych przypadków:
- Gdy środowisko integration runtime jest uruchomiona na zaufanej sieci lub sieci o przezroczyste szyfrowanie, takich jak IP/s. Ponieważ ta komunikacja kanał jest tylko ograniczony w zaufanej sieci, mogą nie być potrzebne dodatkowe szyfrowanie.
- Gdy środowisko integration runtime nie jest uruchomiony w środowisku produkcyjnym. Może to pomóc w zmniejszeniu kosztów certyfikatu TLS/SSL.


## <a name="monitor-a-multi-node-gateway"></a>Monitor bramy wielowęzłowego
### <a name="multi-node-gateway-monitoring"></a>Monitorowania bram z wieloma węzłami
W witrynie Azure portal można wyświetlić migawki niemal w czasie rzeczywistym wykorzystania zasobów (procesor CPU, pamięci, network(in/out) itp.) w każdym węźle, wraz ze stanów węzłów bramy. 

![Brama zarządzania danymi — monitorowanie wielu węzłów](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)

Możesz włączyć **ustawienia zaawansowane** w **bramy** stronę, aby wyświetlić zaawansowane metryki, takie jak **sieci**(We/Wy), **rola & stan poświadczeń**, co jest przydatne podczas debugowania problemów bramy i **równoczesnych zadań** (uruchomione / Limit) który można być zmodyfikowane / zmienione w związku z tym podczas dostrajania wydajności. W poniższej tabeli przedstawiono opisy kolumn w **węzły bramy** listy:  

Właściwość monitorowania | Opis
:------------------ | :---------- 
Name (Nazwa) | Nazwa logicznej bramy i węzłów skojarzone z tą bramą.  
Stan | Stan logicznej bramy i węzłów bramy. Przykład: Online/Offline/Limited/itd. Aby uzyskać informacji na temat tych stanów, zobacz [stan bramy](#gateway-status) sekcji. 
Wersja | Wyświetla wersję logicznej bramy i każdy węzeł bramy. Wersja logicznej bramy jest określany na podstawie wersji Większość węzłów w grupie. W przypadku węzłów z różnymi wersjami w Instalatorze logicznej bramy tylko węzły numerem wersji funkcji logicznej bramy prawidłowo. Innym jest tryb ograniczony i należy ręcznie zaktualizować (tylko w przypadku, gdy aktualizacje automatyczne nie powiedzie się). 
Dostępna pamięć | Dostępna pamięć na węzeł bramy. Ta wartość jest niemal w czasie rzeczywistym migawki. 
Użycie procesora CPU | Użycie procesora CPU węzeł bramy. Ta wartość jest niemal w czasie rzeczywistym migawki. 
Sieć (We/Wy) | Wykorzystanie sieci węzła bramy. Ta wartość jest niemal w czasie rzeczywistym migawki. 
Równoczesne zadania (uruchomione / Limit) | Numer zadania lub podzadania uruchomione w każdym węźle. Ta wartość jest niemal w czasie rzeczywistym migawki. Limit oznacza maksymalna liczba równoczesnych zadań dla każdego węzła. Ta wartość jest określona na podstawie rozmiaru maszyny. Możesz zwiększyć limit, aby skalować w górę do wykonywania zadań jednoczesnych w zaawansowanych scenariuszach, gdzie Procesora / pamięci / sieć jest niewykorzystywanych, ale działania przekraczają limit. Ta funkcja jest również dostępna za pośrednictwem bramy jednowęzłowej (nawet jeśli nie włączono funkcji skalowalności i dostępności). Aby uzyskać więcej informacji, zobacz [skalowanie zagadnienia](#scale-considerations) sekcji. 
Rola | Istnieją dwa typy ról — dyspozytora i proces roboczy. Wszystkie węzły są pracowników, co oznacza, że ich można wszystkie służyć do wykonywania zadań. Istnieje tylko jeden węzeł dyspozytora, który jest używany do pobierania zadania/zadań z usług cloud services i wysyłać je do innego procesu roboczego węzłów (w tym sam). 

![Brama zarządzania danymi — zaawansowane monitorowanie wielu węzłów](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-advanced.png)

### <a name="gateway-status"></a>Stan bramy

W poniższej tabeli przedstawiono możliwe stany **węzeł bramy**: 

Stan  | Komentarze/scenariusze
:------- | :------------------
Online | Węzeł jest połączony do usługi Data Factory.
Offline | Węzeł jest w trybie offline.
Uaktualnianie | Węzeł jest aktualizowane automatycznie.
Ograniczone | Ze względu na problem z łącznością. Może być spowodowane problemem 8050 portu HTTP, problem łączności z usługą Service bus lub problemu z synchronizacją poświadczeń. 
Nieaktywna | Węzeł znajduje się w konfiguracji różni się od konfiguracji innych większości węzłów.<br/><br/> Węzeł może być nieaktywne, gdy nie można nawiązać z innych węzłów. 


W poniższej tabeli przedstawiono możliwe stany **logicznej bramy**. Stan bramy zależy od stany węzłów bramy. 

Stan | Komentarze
:----- | :-------
Wymaga rejestracji | Żaden węzeł nie został jeszcze zarejestrowany na tym logicznej bramy
Online | Węzły bramy są w trybie online
Offline | Nie węzła w stan online.
Ograniczone | Nie wszystkie węzły w tę bramę są w dobrej kondycji. Ten stan jest ostrzeżenie, że może to być jednego z węzłów! <br/><br/>Może wynikać z problemu z synchronizacją poświadczeń na węzeł dyspozytora/procesu roboczego. 

### <a name="pipeline-activities-monitoring"></a>Potok / działania monitorowania
Azure portal udostępnia środowisko zawierające szczegóły poziomu węzła szczegółowe monitorowanie potoku. Na przykład pokazuje, uruchomiono działania, na który węzeł. Te informacje może być przydatne, aby zrozumieć problemy z wydajnością dla określonego węzła, na przykład ze względu na ograniczenia przepustowości sieci. 

![Brama zarządzania danymi — monitorowania potoków wielu węzłów](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-pipelines.png)

![Brama zarządzania danymi — szczegóły potoku](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-pipeline-details.png)

## <a name="scale-considerations"></a>Zagadnienia dotyczące skalowania

### <a name="scale-out"></a>Skalowanie w poziomie
Gdy **mało dostępnej pamięci** i **użycie procesora CPU jest wysokie**, dodanie nowego węzła ułatwia skalowanie obciążenia między maszynami. Jeśli działania kończą się niepowodzeniem z powodu przekroczenia limitu czasu lub bramy węzeł jest w trybie offline, ułatwi to, jeśli dodasz węzeł do bramy.
 
### <a name="scale-up"></a>Skalowanie w górę
Gdy ilość dostępnej pamięci i procesora CPU nie mogą być wykorzystane dobrze, ale bezczynną dyspozycyjność wynosi 0, należy skalowanie w górę, zwiększając liczbę współbieżnych zadań, które można uruchomić w węźle. Możesz również skalować w górę, gdy działania przekraczają limit, ponieważ brama jest przeciążona. Jak pokazano na poniższej ilustracji, można zwiększyć maksymalną pojemność dla węzła. Zaleca się ona zaczynać podwojenie.  

![Brama zarządzania danymi — zagadnienia dotyczące skalowania](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-scale-considerations.png)


## <a name="known-issuesbreaking-changes"></a>Znane problemy dotyczące/zmiany

- Obecnie możesz mieć maksymalnie cztery węzły fizyczne bramy dla jednej logicznej bramy. Jeśli potrzebujesz więcej niż cztery węzły ze względu na wydajność, Wyślij wiadomość e-mail do [ DMGHelp@microsoft.com ](mailto:DMGHelp@microsoft.com).
- Nie można ponownie zarejestrować węzeł bramy przy użyciu klucza uwierzytelniania podanego w innym logicznej bramy, aby przełączyć się z bieżącym logicznej bramy. Aby ponownie zarejestrować, odinstalować bramę z węzła, ponownie zainstaluj bramę i zarejestruj je przy użyciu klucza uwierzytelniania dla logicznej bramy. 
- Jeśli serwer proxy HTTP jest wymagane dla wszystkich węzłów bramy, serwer proxy w ustawach diahost.exe.config i diawp.exe.config i upewnij się, że wszystkie węzły mają te same diahost.exe.config i diawip.exe.config przy użyciu Menedżera serwera. Zobacz [Skonfiguruj ustawienia serwera proxy](data-factory-data-management-gateway.md#configure-proxy-server-settings) sekcji, aby uzyskać szczegółowe informacje. 
- Aby zmienić trybu szyfrowania komunikacji między węzłami w Menedżerze konfiguracji bramy, należy usunąć wszystkie węzły w portalu, z wyjątkiem jednego. Następnie dodaj węzły ponownie po zmianie trybu szyfrowania.
- Użyj oficjalnych certyfikatów SSL, jeśli chcesz zaszyfrować kanał komunikacyjny między węzłami. Certyfikat z podpisem własnym może spowodować problemy z łącznością, zgodnie z tego samego certyfikatu nie może być zaufany liście urząd certyfikacji na innych komputerach. 
- Nie można zarejestrować węzła bramy, aby logicznej bramy, gdy wersja węzła jest niższa niż wersja logicznej bramy. Usuń wszystkie węzły, logiczne bramy z portalu, tak, aby zarejestrować niższe node(downgrade) wersji go. Jeśli usuniesz wszystkie węzły, logiczne bramy, ręcznie zainstaluj i Zarejestruj nowych węzłów do tej bramy logiczne. Instalacja ekspresowa produktu nie jest obsługiwana w tym przypadku.
- Instalacja ekspresowa produktu nie można użyć do zainstalowania węzły do istniejącej bramy logiczne, które nadal korzysta z poświadczeń w chmurze. Możesz sprawdzić, gdzie poświadczenia są przechowywane z Menedżera konfiguracji bramy na karcie Ustawienia.
- Instalacja ekspresowa produktu nie można użyć do zainstalowania węzły do istniejącej bramy logiczne, które ma włączone szyfrowanie węzła do węzła. Jako ustawienie trybu szyfrowania obejmuje ręczne dodanie certyfikatów, instalacja ekspresowa jest ma więcej opcji. 
- Dla kopiowania plików ze środowiska lokalnego, nie należy używać \\nazwy localhost ani C:\files już od localhost lub dysk lokalny może nie być dostępne za pośrednictwem wszystkich węzłów. Zamiast tego należy użyć \\ServerName\files do określenia lokalizacji plików.


## <a name="rolling-back-from-the-preview"></a>Wycofywanie z wersji zapoznawczej 
Aby przywrócić w wersji zapoznawczej, należy usunąć wszystkie węzły oprócz jednego węzła. Nie ma znaczenia węzły, które można usunąć, ale upewnij się, że masz co najmniej jeden węzeł w logicznej bramy. Można usunąć węzła, odinstalowując bramy na komputerze lub przy użyciu witryny Azure portal. W witrynie Azure portal w **usługi Data Factory** kliknij połączone usługi, aby uruchomić **połączonych usług** strony. Wybierz bramy Aby uruchomić **bramy** strony. Na stronie bramy widać węzłów skojarzone z tą bramą. Strona służy do usuwania węzła z bramy.
 
Po usunięciu, kliknij przycisk **funkcje w wersji zapoznawczej** w tej samej stronie portalu Azure, a następnie wyłącz funkcję w wersji zapoznawczej. Masz zresetowaniem bramy do jednego węzła GA (ogólnie) bramy.


## <a name="next-steps"></a>Kolejne kroki
Sprawdź następujące artykuły:
- [Brama zarządzania danymi](data-factory-data-management-gateway.md) — zawiera szczegółowe omówienie bramy.
- [Przenoszenie danych między lokalizacją lokalną i chmurą magazyny danych](data-factory-move-data-between-onprem-and-cloud.md) — zawiera przewodnik z instrukcjami krok po kroku z jednego węzła przy użyciu bramy. 
