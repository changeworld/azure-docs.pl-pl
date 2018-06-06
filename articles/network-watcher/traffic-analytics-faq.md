---
title: Analiza ruchu Azure — często zadawane pytania | Dokumentacja firmy Microsoft
description: Odpowiedzi na niektóre często zadawane pytania dotyczące analizy ruchu.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2018
ms.author: jdial
ms.openlocfilehash: f7e456c76dcf67a40777e32b100b900b859e210e
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34736800"
---
# <a name="traffic-analytics-frequently-asked-questions"></a>Analiza ruchu — często zadawane pytania

1.  Jakie są wymagania wstępne do użycia analizy ruchu?

    Analiza ruchu wymagane są następujące wymagania wstępne:

    - Subskrypcja obserwatora sieciowego włączony
    - Dzienniki przepływu NSG włączona dla grupy NSG, który chcesz monitorować
    - Konto usługi Azure Storage do przechowywania raw flog dzienniki
    - Obszar roboczy analizy dzienników (OMS), z odczytu i zapisu
    - Konto musi mieć przypisaną z jednego z następujących uprawnień na poziomie subskrypcji:
    
            All permissions *
            All Read permissions */read
            All network permissions Microsoft.Network/*
            All network read permissions Microsoft.Network/*/read

    Lub Twoje konto musi mieć przypisaną o następującej wszystkie akcje na poziomie subskrypcji: 

        - Microsoft.Network/applicationGateways/read
        - Microsoft.Network/connections/read
        - Microsoft.Network/loadBalancers/read 
        - Microsoft.Network/localNetworkGateways/read 
        - Microsoft.Network/networkInterfaces/read 
        - Microsoft.Network/networkSecurityGroups/read 
        - Microsoft.Network/publicIPAddresses/read
        - Microsoft.Network/routeTables/read
        - Microsoft.Network/virtualNetworkGateways/read 
        - Microsoft.Network/virtualNetworks/read

2.  Regiony platformy Azure są dostępne w ruchu analytics?

    W wersji zapoznawczej, można użyć analizy ruchu dla grup NSG w następujących **obsługiwane regiony**: zachodnie centralnej nam wschodnie stany USA, wschodnie stany USA 2, północno-środkowe stany, południowo-środkowe stany, środkowe stany USA, zachodnie stany USA, zachodnie stany USA 2, Europa Zachodnia, Europa Północna , Wielka Brytania Zachodnia, Wielka Brytania Południowa, Australia Wschodnia i Australia południowo-wschodnia. Obszar roboczy analizy dzienników musi istnieć w zachodnie centralnej nam wschodnie stany USA, Europa Zachodnia, Południowo-Wschodnia Australia albo region Wielka Brytania Południowa.

3.  Można włączyć przepływ grup NSG dzienniki znajdować się w różnych regionach niż obszar Mój obszar roboczy OMS?

    Yes

4.  Można skonfigurować wiele grup NSG w obrębie jednego obszaru roboczego?

    Yes

5.  Czy można używać z istniejącym obszarem roboczym pakietu OMS?

    Tak, jeśli wybierz istniejący obszar roboczy, upewnij się, że został już zmigrowany do nowego języka zapytań. Jeśli nie chcesz, aby uaktualnić obszaru roboczego; Musisz utworzyć nowy. Aby uzyskać więcej informacji dotyczących nowego języka zapytań, zobacz [Analiza dzienników Azure uaktualnienia do nowego wyszukiwania dziennika](../log-analytics/log-analytics-log-search-upgrade.md).

6.  Można moim koncie magazynu Azure w ramach jednej subskrypcji i Mój obszar roboczy OMS znajdować się w innej subskrypcji?

    Yes

7.  Czy mogę przechowywać nowych dzienników innego konta magazynu w innej subskrypcji

    Nie. Nieprzetworzone dzienniki może przechowywać żadnych koncie magazynu, jeśli grupa NSG jest włączona dzienniki przepływu, jednak zarówno konto magazynu i raw dzienniki muszą być w tej samej subskrypcji i regionu.

8.  Jeśli wyświetlany jest komunikat o błędzie "Nie została odnaleziona" podczas konfigurowania grupy NSG do analizy ruchu, jak można rozwiązać go?

    Wybierz na liście pytania 2 obsługiwanym regionie. W przypadku wybrania z systemem innym niż obsługiwany region komunikat o błędzie "Nie została odnaleziona".

9.  W obszarze dzienniki przepływu NSG, otrzymuję NSG stan jako "Nie można załadować", co zrobić dalej?

    Dostawca elemencie Microsoft.Insights musi być zarejestrowana do przepływu rejestrowania działała poprawnie. Jeśli nie masz pewności, czy dostawca elemencie Microsoft.Insights jest zarejestrowany lub nie dla Twojej subskrypcji, Zastąp *xxxxx-xxxxx-xxxxxx-xxxx* w następujące polecenie, a następnie uruchom następujące polecenia ze środowiska PowerShell:

    ```powershell-interactive
    **Select-AzureRmSubscription** -SubscriptionId xxxxx-xxxxx-xxxxxx-xxxx
    **Register-AzureRmResourceProvider** -ProviderNamespace Microsoft.Insights
    ```

10. Konfigurowanie rozwiązania zostało. Dlaczego nie widzę żadnych czynności na pulpicie nawigacyjnym?

    Pulpit nawigacyjny może potrwać maksymalnie 30 minutach pojawiają się po raz pierwszy. Rozwiązanie musi najpierw agregacji wystarczającej ilości danych dla niej pochodzić wgląd w istotne dane, zanim wszystkie raporty są generowane. 

11.  Jeśli wyświetlany jest następujący komunikat: "nie znaleziono żadnych danych w tym obszarze roboczym dla wybranego okresu. Spróbuj zmienić okres lub wybierz inny obszar roboczy", jak I usunąć go?

        Spróbuj następujących opcji:
        - Spróbuj zmienić interwał czasu na górnym pasku.
        - Wybierz inny obszar roboczy analizy dzienników na górnym pasku
        - Spróbuj uzyskiwanie dostępu do analizy ruchu po 30 minutach, jeśli niedawno została włączona
    
        Jeśli problemy będą się powtarzać, Zgłoś problemy w [forum głos użytkownika](https://feedback.azure.com/forums/217313-networking?category_id=195844).

12.  Jeśli wyświetlany jest następujący komunikat: "1) Twoje NSG analizowanie przepływać dzienników po raz pierwszy. Ten proces może potrwać 20-30 minut. Sprawdź ponownie za jakiś czas. 2) Jeśli nie działa powyższy krok i obszaru roboczego znajduje się w bezpłatnej wersji produktu, a następnie sprawdź wykorzystanie sieci obszaru roboczego można zweryfikować za pośrednictwem przydziału, w przeciwnym wypadku zapoznaj na często zadawane pytania dla dalszych informacji", jak I usunąć go?

        Ten błąd może pojawić się z następujących powodów:
        - Analiza ruchu może ostatnio włączone i może nie jeszcze ma łączy wystarczającej ilości danych dla niej pochodzić wgląd w istotne dane.
        - Obszar roboczy OMS jest bezpłatna jednostki SKU i jego naruszenie limitów przydziału. W takim przypadku może być konieczne użycie obszaru roboczego w jednostce SKU o większej pojemności.
    
        Jeśli problemy będą się powtarzać, Zgłoś problemy w [forum głos użytkownika](https://feedback.azure.com/forums/217313-networking?category_id=195844).
    
13.  Jeśli wyświetlany jest następujący komunikat: "wygląda na to mamy danych zasobów (topologii) i żadne informacje przepływów. W tym samym czasie, kliknij tutaj, aby wyświetlić dane zasobów i dotyczą — często zadawane pytania, aby uzyskać więcej informacji", jak go usunąć?

        Pojawia się informacje o zasobach na pulpicie nawigacyjnym; jednak nie statystyki związane z przepływem są obecne. Dane mogą nie być obecny ze względu na nie przepływów komunikacji między zasobami. Poczekaj 60 min i ponownie sprawdzić stan. Jeśli wiesz, że przepływy komunikacji między zasobami istnieje, a następnie budzić zastrzeżenia co w [forum głos użytkownika](https://feedback.azure.com/forums/217313-networking?category_id=195844).

14. Można skonfigurować analytics ruchu przy użyciu programu PowerShell lub szablonu usługi Azure Resource Manager?

    Nie, analizy ruchu może być konfigurowany tylko przy użyciu portalu Azure.

15.  Jak kosztuje analizy ruchu

        Analizy ruchu taryfowych wzmocnienia zmniejszenie dzienniki i zapisywania rozszerzonych dzienniki w obszarze roboczym analizy dzienników. W wersji zapoznawczej, analizy ruchu jest nie rozliczane wzmocnienia zmniejszenie dzienniki, jednak przechowywania danych w obszarze roboczym podlega rozliczeń stawkami opublikowanych. Tej odpowiedzi zostanie zaktualizowany po udostępnieniu ceny analizy ruchu.

16.  Jak można przejść za pomocą klawiatury w widoku mapy geograficznie?

        Strona mapy geograficznie zawiera dwóch głównych sekcji:
    
        - **Transparent**: transparent umieszczone w górnej części mapy geograficznie oferuje możliwość Wybierz Filtry dystrybucji ruchu za pomocą przycisków, takich jak Deployment/No wdrożenia/Active/Inactive/ruch Analytics włączone/ruch Analytics nie włączono/ruchu z krajów/Benign/złośliwym kodem/dozwolone szkodliwy ruch, a informacje legendy. Na wybór przycisków zdefiniowanych na mapie, na przykład jeśli użytkownik wybierze przycisk filtru "Active" w obszarze transparentu, mapy wyróżnia "Active" centrów danych w danym wdrożeniu, a następnie jest stosowany filtr odpowiednich.
        - **Mapa**: sekcja mapy umieszczone pod nagłówkiem zawiera Dystrybucja ruchu między centrach danych platformy Azure i krajów.
    
        **Nawigacji klawiatury na transparencie**
    
        - Domyślnie zaznaczenie na stronie mapy geograficznie transparentu jest filtru przycisk "Azure kontrolery domeny".
        - Aby przejść do innego przycisku filtrów, można użyć `Tab` lub `Right arrow` klawisz, aby przejść dalej. Aby przejść do tyłu, użyj `Shift+Tab` lub `Left arrow` klucza. Pierwszeństwo kierunku nawigacji do przodu jest od lewej do prawej, a następnie góry do dołu.
        - Naciśnij klawisz `Enter` lub `Down` klawisz, aby zastosować wybrany filtr. Na podstawie wyboru filtru i wdrażania, są wyróżnione jednego lub wielu węzłów w sekcji mapy.
            - Aby przełączyć między **transparent** i **mapy**, naciśnij klawisz `Ctrl+F6`.
        
        **Nawigacji klawiatury na mapie**
    
        - Po wybrać dowolny filtr na banerze i naciśnięciu `Ctrl+F6`, fokus do jednego z węzłów wyróżnione (**centrum danych Azure** lub **Kraj/Region**) w widoku mapy.
        - Aby przejść do innych wyróżnione węzły na mapie można użyć `Tab` lub `Right arrow` kluczy dla ruch do przodu i `Shift+Tab` lub `Left arrow` klucza dla ruchu z poprzednimi wersjami.
        - Aby wybrać wyróżnione węzły na mapie, użyj `Enter` lub `Down arrow` klucza.
        - Wyboru tych węzłów, fokus do **informacji przybornika** dla węzła. Domyślnie fokus do zamkniętego przycisk **informacji przybornika**. Aby umożliwić dalsze nawigację **pole** przeglądać, `Right` i `Left arrow` kluczy, aby przejść do przodu i do tyłu, odpowiednio. Naciśnięcie przycisku `Enter` ma sam efekt co wybierając przycisk ukierunkowanych na **informacji przybornika**.
        - Naciśnięcie przycisku `Tab` podczas, gdy fokus jest ustawiony na **informacji przybornika**, fokusu do punktów końcowych na tym samym kontynencie jako wybranego węzła. Można użyć `Right` i `Left arrow` klucze do nawigowania tymi punktami końcowymi.
        - Aby przejść do innego przepływu punkty końcowe/kontynentów i klastra, należy użyć `Tab` dla ruch do przodu i `Shift+Tab` dla ruchu z poprzednimi wersjami.
        - Gdy fokus jest ustawiony na `Continent clusters`, użyj `Enter` lub `Down` klawisze strzałek, aby wyróżnić punkty końcowe wewnątrz kontynencie klastra. Do nawigowania punktów końcowych i przycisku Zamknij w polu informacje kontynencie klastra, użyj `Right` lub `Left arrow` klucza do przeniesienia do przodu i do tyłu, odpowiednio. W dowolnym punkcie końcowym, można użyć `Shift+L` przejść do linii połączenia z wybranego węzła do punktu końcowego. Naciśnięcie przycisku `Shift+L` ponownie powoduje przejście do wybranego punktu końcowego.
        
        Na każdym etapie:
    
        - `ESC` Zwija zaznaczenie rozwinięte.
        - `UP Arrow` Klucza wykonuje ta sama akcja co `ESC`. `Down arrow` Klucza wykonuje ta sama akcja co `Enter`.
        - Użyj `Shift+Plus` Aby powiększyć, i `Shift+Minus` Aby pomniejszyć.
