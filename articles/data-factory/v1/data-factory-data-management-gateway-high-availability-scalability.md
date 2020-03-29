---
title: Wysoka dostępność dzięki bramie zarządzania danymi w usłudze Azure Data Factory
description: W tym artykule wyjaśniono, jak można skalować w poziomie bramy zarządzania danymi, dodając więcej węzłów i skalować w górę, zwiększając liczbę równoczesnych zadań, które można uruchomić w węźle.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: anandsub
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 4ee89f4bba70bb5e81eef21247d556f65a2a1f16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065206"
---
# <a name="data-management-gateway---high-availability-and-scalability-preview"></a>Brama zarządzania danymi — wysoka dostępność i skalowalność (wersja zapoznawcza)
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [środowisko uruchomieniowe integracji hostowanego samodzielnie w pliku](../create-self-hosted-integration-runtime.md). 


Ten artykuł ułatwia konfigurowanie rozwiązania o wysokiej dostępności i skalowalności za pomocą bramy/integracji zarządzania danymi.    

> [!NOTE]
> W tym artykule założono, że znasz już podstawy środowiska wykonawczego integracji (Wcześniejsza brama zarządzania danymi). Jeśli nie, zobacz [Brama zarządzania danymi](data-factory-data-management-gateway.md).
> 
> **Ta funkcja podglądu jest oficjalnie obsługiwana w bramie zarządzania danymi w wersji 2.12.xxxx.x i wyższej**. Upewnij się, że używasz wersji 2.12.xxxx.x lub wyższej. Pobierz najnowszą wersję bramy zarządzania danymi [tutaj](https://www.microsoft.com/download/details.aspx?id=39717).

## <a name="overview"></a>Omówienie
Bramy zarządzania danymi, które są instalowane na wielu komputerach lokalnych, można skojarzyć z jedną bramą logiczną z portalu. Maszyny te są nazywane **węzłami**. Z bramą logiczną mogą być skojarzone maksymalnie **cztery węzły.** Korzyści z posiadania wielu węzłów (komputerów lokalnych z zainstalowaną bramą) dla bramy logicznej są następujące:  

- Zwiększ wydajność przenoszenia danych między magazynami danych w środowisku lokalnym i chmurowym.  
- Jeśli jeden z węzłów ulegnie upadkowi z jakiegoś powodu, inne węzły są nadal dostępne do przenoszenia danych. 
- Jeśli jeden z węzłów musi zostać przesuń do trybu offline w celu konserwacji, inne węzły są nadal dostępne do przenoszenia danych.

Można również skonfigurować liczbę **równoczesnych zadań przenoszenia danych,** które można uruchomić w węźle, aby zwiększyć możliwości przenoszenia danych między magazynami danych w chmurze i w chmurze. 

Za pomocą witryny Azure portal, można monitorować stan tych węzłów, co pomaga zdecydować, czy dodać lub usunąć węzeł z bramy logicznej. 

## <a name="architecture"></a>Architektura 
Poniższy diagram zawiera omówienie architektury funkcji skalowalności i dostępności bramy zarządzania danymi: 

![Brama zarządzania danymi — wysoka dostępność i skalowalność](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-high-availability-and-scalability.png)

**Brama logiczna** to brama dodana do fabryki danych w witrynie Azure portal. Wcześniej można skojarzyć tylko jeden komputer z systemem Windows w środowisku lokalnym z bramą zarządzania danymi zainstalowaną z bramą logiczną. Ten komputer bramy lokalnej jest nazywany węzłem. Teraz można skojarzyć maksymalnie **cztery węzły fizyczne** z bramą logiczną. Brama logiczna z wieloma węzłami jest nazywana **bramą wielowęzłową**.  

Wszystkie te węzły są **aktywne**. Wszystkie one mogą przetwarzać zadania przenoszenia danych, aby przenieść dane między magazynami danych lokalnych i chmurowych. Jeden z węzłów działa zarówno jako dyspozytor, jak i pracownik. Inne węzły w grupach są węzłami procesu roboczego. Węzeł **dyspozytora** pobiera zadania/zadania przenoszenia danych z usługi w chmurze i wysyła je do węzłów procesu roboczego (w tym do siebie). Węzeł **procesu roboczego** wykonuje zadania przenoszenia danych w celu przenoszenia danych między magazynami danych lokalnych i chmurowych. Wszystkie węzły są pracownikami. Tylko jeden węzeł może być zarówno wysyłki i procesu roboczego.    

Zazwyczaj można rozpocząć od jednego węzła i **skalować w poziomie,** aby dodać więcej węzłów, ponieważ istniejące węzły są przeciążone obciążeniem przenoszenia danych. Można również **skalować w górę** możliwości przenoszenia danych węzła bramy, zwiększając liczbę równoczesnych zadań, które mogą być uruchamiane w węźle. Ta funkcja jest również dostępna z bramą z jednym węzłem (nawet wtedy, gdy funkcja skalowalności i dostępności nie jest włączona). 

Brama z wieloma węzłami utrzymuje poświadczenia magazynu danych w synchronizacji we wszystkich węzłach. Jeśli występuje problem z łącznością węzeł-węzeł, poświadczenia mogą być niezsynchronizowane. Po ustawieniu poświadczeń lokalnego magazynu danych, który używa bramy, zapisuje poświadczenia w węźle dyspozytor/proces roboczy. Węzeł dyspozytora synchronizuje się z innymi węzłami procesu roboczego. Ten proces jest nazywany **synchronizacją poświadczeń**. Kanał komunikacji między węzłami może być **szyfrowany** przez publiczny certyfikat SSL/TLS. 

## <a name="set-up-a-multi-node-gateway"></a>Konfigurowanie bramy wielowęzłowej
W tej sekcji przyjęto założenie, że przeszedłeś przez następujące dwa artykuły lub zapoznałeś się z pojęciami w tych artykułach: 

- [Brama zarządzania danymi](data-factory-data-management-gateway.md) — zapewnia szczegółowy przegląd bramy.
- [Przenoszenie danych między magazynami danych w chmurze i w chmurze](data-factory-move-data-between-onprem-and-cloud.md) — zawiera przewodnik z instrukcjami krok po kroku dotyczącymi korzystania z bramy z jednym węzłem.  

> [!NOTE]
> Przed zainstalowaniem bramy zarządzania danymi na lokalnym komputerze z systemem Windows zobacz wymagania wstępne wymienione w [głównym artykule](data-factory-data-management-gateway.md#prerequisites).

1. W [instruktażu](data-factory-move-data-between-onprem-and-cloud.md#create-gateway), podczas tworzenia bramy logicznej, włącz funkcję **Wysoka dostępność & skalowalności.** 

    ![Brama zarządzania danymi — umożliwia wysoką dostępność i skalowalność](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-enable-high-availability-scalability.png)
2. Na stronie Konfigurowanie użyj łącza **Instalacja ekspresowa** lub **Express Setup** **Ręczna instalacja,** aby zainstalować bramę w pierwszym węźle (lokalnym komputerze z systemem Windows).

    ![Brama zarządzania danymi — konfiguracja ekspresowa lub ręczna](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-express-manual-setup.png)

    > [!NOTE]
    > Jeśli używasz opcji konfiguracji ekspresowej, komunikacja między węzłami odbywa się bez szyfrowania. Nazwa węzła jest taka sama jak nazwa komputera. Użyj ręcznej konfiguracji, jeśli komunikacja węzeł-węzeł musi być zaszyfrowana lub chcesz określić wybraną nazwę węzła. Nazwy węzłów nie mogą być edytowane później.
3. Jeśli wybierzesz **konfigurację ekspresową**
    1. Po pomyślnym zainstalowaniu bramy zostanie wyświetlony następujący komunikat:

        ![Brama zarządzania danymi — wyraźny sukces konfiguracji](media/data-factory-data-management-gateway-high-availability-scalability/express-setup-success.png)
    2. Uruchom program Data Management Configuration Manager dla bramy, postępując zgodnie z [tymi instrukcjami](data-factory-data-management-gateway.md#configuration-manager). Zobaczysz nazwę bramy, nazwę węzła, stan itp.

        ![Brama zarządzania danymi — instalacja powiodła się](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)
4. W przypadku **wybrania konfiguracji ręcznej:**
    1. Pobierz pakiet instalacyjny z Centrum pobierania Microsoft, uruchom go, aby zainstalować bramę na komputerze.
    2. Użyj **klucza uwierzytelniania** ze strony **Konfigurowanie,** aby zarejestrować bramę.
    
        ![Brama zarządzania danymi — instalacja powiodła się](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-authentication-key.png)
    3. Na stronie **Nowy węzeł bramy** można podać **niestandardową nazwę** węzła bramy. Domyślnie nazwa węzła jest taka sama jak nazwa komputera.    

        ![Brama zarządzania danymi — określa nazwę](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-name.png)
    4. Na następnej stronie można wybrać, czy **włączyć szyfrowanie komunikacji między węzłami**. Kliknij **przycisk Pomiń,** aby wyłączyć szyfrowanie (domyślnie).

        ![Brama zarządzania danymi — włącz szyfrowanie](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-node-encryption.png)  
    
        > [!NOTE]
        > Zmiana trybu szyfrowania jest obsługiwana tylko wtedy, gdy masz jeden węzeł bramy w bramie logicznej. Aby zmienić tryb szyfrowania, gdy brama ma wiele węzłów, wykonaj następujące czynności: usuń wszystkie węzły z wyjątkiem jednego węzła, zmień tryb szyfrowania, a następnie dodaj węzły ponownie.
        > 
        > Zobacz sekcję [Wymagania certyfikatów TLS/SSL,](#tlsssl-certificate-requirements) aby uzyskać listę wymagań dotyczących używania certyfikatu TLS/SSL. 
    5. Po pomyślnym zainstalowaniu bramy kliknij przycisk Uruchom program Menedżer konfiguracji:
    
        ![Ręczna konfiguracja — uruchamianie menedżera konfiguracji](media/data-factory-data-management-gateway-high-availability-scalability/manual-setup-launch-configuration-manager.png)   
    6. menedżer konfiguracji bramy zarządzania danymi w węźle (lokalnym komputerze z systemem Windows), który pokazuje stan łączności, **nazwę bramy**i **nazwę węzła**.  

        ![Brama zarządzania danymi — instalacja powiodła się](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)

        > [!NOTE]
        > Jeśli inicjujesz inicjowanie obsługi administracyjnej bramy na maszynie Wirtualnej platformy Azure, możesz użyć [tego szablonu usługi Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-mutiple-vms-with-data-management-gateway). Ten skrypt tworzy bramę logiczną, konfiguruje maszyny wirtualne z zainstalowanym oprogramowaniem Bramy zarządzania danymi i rejestruje je przy bramie logicznej. 
6. W witrynie Azure portal uruchom stronę **bramy:** 
    1. Na stronie głównej fabryki danych w portalu kliknij pozycję **Usługi połączone**.
    
        ![Strona główna fabryki danych](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-home-page.png)
    2. wybierz **bramę,** aby wyświetlić stronę **Bramy:**
    
        ![Strona główna fabryki danych](media/data-factory-data-management-gateway-high-availability-scalability/linked-services-gateway.png)
    4. Zostanie wyświetlona strona **Brama:**   

        ![Brama z widokiem pojedynczego węzła](media/data-factory-data-management-gateway-high-availability-scalability/gateway-first-node-portal-view.png) 
7. Kliknij **pozycję Dodaj węzeł** na pasku narzędzi, aby dodać węzeł do bramy logicznej. Jeśli planujesz używać konfiguracji ekspresowej, wykonaj ten krok z komputera lokalnego, który zostanie dodany jako węzeł do bramy. 

    ![Brama zarządzania danymi — menu dodaj węzeł](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)
8. Kroki są podobne do konfigurowania pierwszego węzła. Interfejs użytkownika programu Configuration Manager umożliwia ustawienie nazwy węzła, jeśli wybierzesz opcję instalacji ręcznej: 

    ![Menedżer konfiguracji — instalowanie drugiej bramy](media/data-factory-data-management-gateway-high-availability-scalability/install-second-gateway.png)
9. Po pomyślnym zainstalowaniu bramy w węźle narzędzie Menedżer konfiguracji wyświetla następujący ekran:  

    ![Menedżer konfiguracji — instalacja drugiej bramy powiodła się](media/data-factory-data-management-gateway-high-availability-scalability/second-gateway-installation-successful.png)
10. Jeśli otworzysz stronę **bramy** w portalu, zobaczysz teraz dwa węzły bramy: 

    ![Brama z dwoma węzłami w portalu](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)
11. Aby usunąć węzeł bramy, kliknij pozycję **Usuń węzeł** na pasku narzędzi, wybierz węzeł, który chcesz usunąć, a następnie kliknij przycisk **Usuń** z paska narzędzi. Ta akcja powoduje usunięcie zaznaczonego węzła z grupy. Należy zauważyć, że ta akcja nie powoduje odinstalowania oprogramowania bramy zarządzania danymi z węzła (lokalnego komputera z systemem Windows). Odinstaluj bramę za pomocą **funkcji Dodaj lub usuń programy** w Panelu sterowania w środowisku lokalnym. Po odinstalowaniu bramy z węzła jest ona automatycznie usuwana w portalu.   

## <a name="upgrade-an-existing-gateway"></a>Uaktualnianie istniejącej bramy
Można uaktualnić istniejącą bramę, aby korzystać z funkcji wysokiej dostępności i skalowalności. Ta funkcja działa tylko z węzłami, które mają bramę zarządzania danymi w wersji >= 2.12.xxxx. Wersję bramy zarządzania danymi zainstalowaną na komputerze można wyświetlić na karcie **Pomoc** programu Menedżer konfiguracji bramy zarządzania danymi. 

1. Zaktualizuj bramę na komputerze lokalnym do najnowszej wersji, wykonując następujące czynności, pobierając i uruchamiając pakiet instalacyjny MSI z [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=39717). Szczegółowe informacje można znaleźć w sekcji [instalacji.](data-factory-data-management-gateway.md#installation)  
2. Przejdź do witryny Azure Portal. Uruchom **stronę Fabryka danych** dla fabryki danych. Kliknij kafelek Usługi połączone, aby uruchomić **stronę usług połączonych**. Wybierz bramę, aby uruchomić **stronę bramy**. Kliknij i włącz **funkcję podglądu,** jak pokazano na poniższej ilustracji: 

    ![Brama zarządzania danymi — włącz funkcję podglądu](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-existing-gateway-enable-high-availability.png)   
2. Po włączeniu funkcji podglądu w portalu zamknij wszystkie strony. Otwórz **ponownie stronę bramy,** aby wyświetlić nowy interfejs użytkownika w wersji zapoznawczej(UI).
 
    ![Brama zarządzania danymi — włączanie powodzenia funkcji w wersji zapoznawczej](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview-success.png)

    ![Brama zarządzania danymi — podgląd interfejsu użytkownika](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview.png)

    > [!NOTE]
    > Podczas uaktualniania nazwa pierwszego węzła jest nazwą komputera. 
3. Teraz dodaj węzeł. Na stronie **Brama** kliknij pozycję **Dodaj węzeł**.  

    ![Brama zarządzania danymi — menu dodaj węzeł](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)

    Postępuj zgodnie z instrukcjami z poprzedniej sekcji, aby skonfigurować węzeł. 

### <a name="installation-best-practices"></a>Najlepsze rozwiązania dotyczące instalacji

- Skonfiguruj plan zasilania na komputerze-hoście bramy, aby maszyna nie hibernacji. Jeśli komputer-host hibernuje, brama nie odpowiada na żądania danych.
- Łącze zapasowe certyfikatu skojarzonego z bramą.
- Upewnij się, że wszystkie węzły mają podobną konfigurację (zalecane) dla idealnej wydajności. 
- Dodaj co najmniej dwa węzły, aby zapewnić wysoką dostępność.  

### <a name="tlsssl-certificate-requirements"></a>Wymagania dotyczące certyfikatów TLS/SSL
Poniżej przedstawiono wymagania dotyczące certyfikatu TLS/SSL używanego do zabezpieczania komunikacji między węzłami środowiska wykonawczego integracji:

- Certyfikat musi być publicznie zaufanym certyfikatem X509 v3. Zaleca się używanie certyfikatów wystawianych przez publiczny urząd certyfikacji (CA).
- Każdy węzeł środowiska uruchomieniowego integracji musi ufać temu certyfikatowi, a także maszynie klienckiej, na którym jest uruchomiona aplikacja menedżera poświadczeń. 
  > [!NOTE]
  > Aplikacja Menedżera poświadczeń jest używana podczas bezpiecznego ustawiania poświadczeń z Kreatora kopiowania/ usługi Azure Portal. I to może być zwolniony z dowolnego komputera w tej samej sieci, jak lokalnie / prywatny magazyn danych.
- Certyfikaty z symbolami wieloznaczowymi są obsługiwane. Jeśli nazwa FQDN jest **node1.domain.contoso.com**, można użyć ***.domain.contoso.com** jako nazwa podmiotu certyfikatu.
- Certyfikaty SIECI SAN nie są zalecane, ponieważ używany będzie tylko ostatni element alternatywnych nazw podmiotu, a wszystkie inne zostaną zignorowane z powodu bieżącego ograniczenia. Na przykład masz certyfikat SAN, którego san **są node1.domain.contoso.com** i **node2.domain.contoso.com,** można używać tylko na komputerze, którego FQDN jest **node2.domain.contoso.com**.
- Obsługuje dowolny rozmiar klucza obsługiwany przez system Windows Server 2012 R2 dla certyfikatów TLS/SSL.
- Certyfikat przy użyciu kluczy CNG nie są obsługiwane.

#### <a name="faq-when-would-i-not-enable-this-encryption"></a>Często zadawane pytania: Kiedy nie włączyć tego szyfrowania?
Włączenie szyfrowania może dodać określony koszt do infrastruktury (posiadanie certyfikatu publicznego), dlatego możesz pominąć włączenie szyfrowania w poniższych przypadkach:
- Gdy środowisko wykonawcze integracji jest uruchomione w zaufanej sieci lub w sieci z przezroczystym szyfrowaniem, takim jak IP/SEC. Ponieważ komunikacja kanału jest ograniczona tylko w zaufanej sieci, może nie być konieczne dodatkowe szyfrowanie.
- Gdy środowisko wykonawcze integracji nie jest uruchomiony w środowisku produkcyjnym. Może to pomóc w zmniejszeniu kosztów certyfikatu TLS/SSL.


## <a name="monitor-a-multi-node-gateway"></a>Monitorowanie bramy wielowęzłowej
### <a name="multi-node-gateway-monitoring"></a>Monitorowanie bramy wielowęzłowej
W witrynie Azure portal można wyświetlać migawkę w czasie zbliżonym do rzeczywistego wykorzystania zasobów (procesora CPU, pamięci, sieci(in/out) itp.) w każdym węźle wraz ze stanami węzłów bramy. 

![Brama zarządzania danymi — monitorowanie wielu węzłów](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)

Ustawienia **zaawansowane** na stronie **Bramy** można włączyć, aby wyświetlić zaawansowane metryki, takie jak **Network**(in/out), Role & **Credential Status**, co jest przydatne w debugowaniu problemów z bramą, i **równoczesnych zadań** (Running/ Limit), które mogą być odpowiednio modyfikowane/ zmieniane podczas dostrajania wydajności. Poniższa tabela zawiera opisy kolumn na liście **Węzły bramy:**  

Właściwość monitorowania | Opis
:------------------ | :---------- 
Nazwa | Nazwa bramy logicznej i węzłów skojarzonych z bramą.  
Stan | Stan bramy logicznej i węzłów bramy. Przykład: Online / Offline / Limited / itp. Aby uzyskać informacje o tych stanach, zobacz [sekcję stanu bramy.](#gateway-status) 
Wersja | Pokazuje wersję bramy logicznej i każdego węzła bramy. Wersja bramy logicznej jest określana na podstawie wersji większości węzłów w grupie. Jeśli w konfiguracji bramy logicznej znajdują się węzły z różnymi wersjami, poprawnie działają tylko węzły o tym samym numerze wersji co brama logiczna. Inne są w trybie ograniczonym i muszą być ręcznie aktualizowane (tylko w przypadku, gdy automatyczna aktualizacja nie powiedzie się). 
Dostępna pamięć | Dostępna pamięć w węźle bramy. Ta wartość jest migawka w czasie zbliżonym do rzeczywistego. 
Wykorzystanie procesora | Wykorzystanie procesora CPU węzła bramy. Ta wartość jest migawka w czasie zbliżonym do rzeczywistego. 
Sieć (wsuwanie/wyjęcie) | Wykorzystanie sieci węzła bramy. Ta wartość jest migawka w czasie zbliżonym do rzeczywistego. 
Równoczesne zadania (uruchamianie/ limit) | Liczba zadań lub zadań uruchomionych w każdym węźle. Ta wartość jest migawka w czasie zbliżonym do rzeczywistego. Limit oznacza maksymalną liczbę równoczesnych zadań dla każdego węzła. Ta wartość jest definiowana na podstawie rozmiaru maszyny. Można zwiększyć limit skalowania w górę równoczesnych zadań w zaawansowanych scenariuszach, gdzie procesor/pamięć/ sieć jest niedostateczone, ale działania są limit czasu. Ta funkcja jest również dostępna z bramą z jednym węzłem (nawet wtedy, gdy funkcja skalowalności i dostępności nie jest włączona). Aby uzyskać więcej informacji, zobacz [sekcję zagadnienia skali.](#scale-considerations) 
Rola | Istnieją dwa typy ról — dyspozytor i pracownik. Wszystkie węzły są pracownikami, co oznacza, że wszystkie mogą być używane do wykonywania zadań. Istnieje tylko jeden węzeł dyspozytora, który jest używany do ściągania zadań/zadań z usług w chmurze i wysyłania ich do różnych węzłów procesu roboczego (w tym do siebie). 

![Brama zarządzania danymi — zaawansowane monitorowanie wielu węzłów](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-advanced.png)

### <a name="gateway-status"></a>Stan bramy

Poniższa tabela zawiera możliwe stany **węzła bramy:** 

Stan  | Komentarze/scenariusze
:------- | :------------------
Online | Węzeł połączony z usługą Data Factory.
W trybie offline | Węzeł jest w trybie offline.
Uaktualnianie | Węzeł jest automatycznie aktualizowany.
Ograniczone | Ze względu na problem z łącznością. Może to być spowodowane problemem z portem HTTP 8050, problemem z łącznością magistrali usług lub problemem synchronizacji poświadczeń. 
Nieaktywne | Węzeł jest w konfiguracji różni się od konfiguracji innych węzłów większościowych.<br/><br/> Węzeł może być nieaktywny, gdy nie może połączyć się z innymi węzłami. 


Poniższa tabela zawiera możliwe stany **bramy logicznej**. Stan bramy zależy od stanów węzłów bramy. 

Stan | Komentarze
:----- | :-------
Wymaga rejestracji | Żaden węzeł nie jest jeszcze zarejestrowany w tej bramie logicznej
Online | Węzły bramy są w trybie online
W trybie offline | Brak węzła w stanie online.
Ograniczone | Nie wszystkie węzły w tej bramie są w dobrej kondycji. Ten stan jest ostrzeżenie, że niektóre węzły mogą być w dół! <br/><br/>Może to być spowodowane problemem synchronizacji poświadczeń w węźle dyspozytor/pracownik. 

### <a name="pipeline-activities-monitoring"></a>Monitorowanie rurociągów/ działań
Portal Azure zapewnia środowisko monitorowania potoku ze szczegółowymi szczegółami poziomu węzła. Na przykład pokazuje, które działania uruchomiono w którym węźle. Te informacje mogą być pomocne w zrozumieniu problemów z wydajnością w określonym węźle, na przykład z powodu ograniczania przepustowości sieci. 

![Brama zarządzania danymi — monitorowanie wielu węzłów dla potoków](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-pipelines.png)

![Brama zarządzania danymi — szczegóły potoku](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-pipeline-details.png)

## <a name="scale-considerations"></a>Zagadnienia dotyczące skali

### <a name="scale-out"></a>Skalowanie w poziomie
Gdy **dostępna pamięć jest niska,** a **użycie procesora CPU jest wysokie,** dodanie nowego węzła pomaga skalować w poziomie obciążenia na różnych komputerach. Jeśli działania nie popełniają się z powodu przeterminowania lub węzła bramy w trybie offline, pomaga po dodaniu węzła do bramy.
 
### <a name="scale-up"></a>Skalowanie w górę
Gdy dostępna pamięć i procesor CPU nie są dobrze wykorzystywane, ale pojemność bezczynności wynosi 0, należy zwiększyć skalę, zwiększając liczbę równoczesnych zadań, które można uruchomić w węźle. Można również skalować w górę, gdy działania są limit czasu, ponieważ brama jest przeciążony. Jak pokazano na poniższej ilustracji, można zwiększyć maksymalną pojemność węzła. Sugerujemy podwojenie go na początek.  

![Brama zarządzania danymi — zagadnienia dotyczące skali](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-scale-considerations.png)


## <a name="known-issuesbreaking-changes"></a>Znane problemy/istotne zmiany

- Obecnie można mieć maksymalnie cztery węzły bramy fizycznej dla pojedynczej bramy logicznej. Jeśli ze względu na wydajność potrzebujesz więcej niż [DMGHelp@microsoft.com](mailto:DMGHelp@microsoft.com)czterech węzłów, wyślij wiadomość e-mail na adres .
- Nie można ponownie zarejestrować węzła bramy za pomocą klucza uwierzytelniania z innej bramy logicznej, aby przełączyć się z bieżącej bramy logicznej. Aby ponownie zarejestrować, odinstaluj bramę z węzła, zainstaluj ponownie bramę i zarejestruj ją przy użyciu klucza uwierzytelniania dla innej bramy logicznej. 
- Jeśli serwer proxy HTTP jest wymagany dla wszystkich węzłów bramy, ustaw serwer proxy w diahost.exe.config i diawp.exe.config, a następnie użyj menedżera serwera, aby upewnić się, że wszystkie węzły mają takie same węzły diahost.exe.config i diawip.exe.config. Szczegółowe informacje można znaleźć w sekcji [Konfigurowanie ustawień serwera proxy.](data-factory-data-management-gateway.md#configure-proxy-server-settings) 
- Aby zmienić tryb szyfrowania komunikacji między węzłami w programie Gateway Configuration Manager, usuń wszystkie węzły w portalu z wyjątkiem jednego. Następnie dodaj węzły z powrotem po zmianie trybu szyfrowania.
- Użyj oficjalnego certyfikatu TLS, jeśli zdecydujesz się zaszyfrować kanał komunikacji węzeł-węzeł. Certyfikat z podpisem własnym może powodować problemy z łącznością, ponieważ ten sam certyfikat może nie być zaufany na liście instytucji certyfikujących na innych komputerach. 
- Nie można zarejestrować węzła bramy do bramy logicznej, gdy wersja węzła jest niższa niż wersja bramy logicznej. Usuń wszystkie węzły bramy logicznej z portalu, dzięki czemu można zarejestrować węzeł (downgrade) niższej wersji. Jeśli usuniesz wszystkie węzły bramy logicznej, ręcznie zainstaluj i zarejestruj nowe węzły do tej bramy logicznej. Konfiguracja ekspresowa nie jest obsługiwana w tym przypadku.
- Nie można użyć konfiguracji ekspresowej do instalowania węzłów do istniejącej bramy logicznej, która nadal używa poświadczeń w chmurze. Można sprawdzić, gdzie poświadczenia są przechowywane z Menedżera konfiguracji bramy na karcie Ustawienia.
- Nie można użyć konfiguracji ekspresowej do instalowania węzłów w istniejącej bramie logicznej, która ma włączone szyfrowanie węzeł-węzeł. Ponieważ ustawienie trybu szyfrowania polega na ręcznym dodawaniu certyfikatów, instalacja ekspresowa nie jest już opcją. 
- W przypadku kopii pliku ze środowiska lokalnego \\nie należy już używać localhost lub C:\files, ponieważ localhost lub dysk lokalny może nie być dostępny za pośrednictwem wszystkich węzłów. Zamiast tego \\należy użyć servername\files, aby określić lokalizację plików.


## <a name="rolling-back-from-the-preview"></a>Wycofywanie z wersji zapoznawczej 
Aby wycofać się z podglądu, usuń wszystkie węzły oprócz jednego węzła. Nie ma znaczenia, które węzły można usunąć, ale upewnij się, że masz co najmniej jeden węzeł w bramie logicznej. Węzeł można usunąć, odinstalowując bramę na komputerze lub korzystając z witryny Azure Portal. W witrynie Azure portal na stronie **Fabryka danych** kliknij pozycję Połączone usługi, aby uruchomić stronę **Usługi połączone.** Wybierz bramę, aby uruchomić stronę **Bramy.** Na stronie Brama można zobaczyć węzły skojarzone z bramą. Strona umożliwia usunięcie węzła z bramy.
 
Po usunięciu kliknij **funkcje podglądu** na tej samej stronie portalu platformy Azure i wyłącz funkcję podglądu. Masz zresetować bramę do jednego węzła GA (ogólna dostępność) bramy.


## <a name="next-steps"></a>Następne kroki
Przejrzyj następujące artykuły:
- [Brama zarządzania danymi](data-factory-data-management-gateway.md) — zapewnia szczegółowy przegląd bramy.
- [Przenoszenie danych między magazynami danych w chmurze i w chmurze](data-factory-move-data-between-onprem-and-cloud.md) — zawiera przewodnik z instrukcjami krok po kroku dotyczącymi korzystania z bramy z jednym węzłem. 
