---
title: Konfigurowanie środowisk przejściowych
description: Dowiedz się, jak wdrożyć aplikacje w nieprodukcyjnym gnieździe i automatycznie wysuwać się do produkcji. Zwiększ niezawodność i wyeliminuj przestoje aplikacji podczas wdrożeń.
ms.assetid: e224fc4f-800d-469a-8d6a-72bcde612450
ms.topic: article
ms.date: 03/04/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 21e025088e59c7f65f848b332ecb393b05918261
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78300875"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Konfigurowanie środowisk przejściowych w usłudze Azure App Service
<a name="Overview"></a>

Podczas wdrażania aplikacji sieci web, aplikacji sieci web w systemie Linux, mobile back end lub aplikacji interfejsu API do [usługi Azure App Service,](https://go.microsoft.com/fwlink/?LinkId=529714)można użyć oddzielnego gniazda wdrożenia zamiast domyślnego gniazda produkcyjnego, gdy używasz w warstwie planu **standardowej,** **premium**lub **izolowanej** usługi aplikacji. Miejsca wdrożenia są aplikacjami na żywo z własnymi nazwami hostów. Elementy zawartości aplikacji i konfiguracji można przełączać między dwoma gniazdami wdrażania, w tym miejscem produkcyjnym. 

Wdrażanie aplikacji w nieprodukcyjnym gnieździe ma następujące zalety:

* Przed zamianą go z gniazdem produkcyjnym można sprawdzić poprawność zmian w aplikacji w miejscu wdrożenia przejściowego.
* Najpierw wdrożenie aplikacji w gnieździe i zamiana jej w proces produkcyjny zapewnia, że wszystkie wystąpienia gniazda są rozgrzane przed zamianą na produkcję. Eliminuje to przestoje podczas wdrażania aplikacji. Przekierowanie ruchu jest bezproblemowe i żadne żądania nie są odrzucane z powodu operacji wymiany. Można zautomatyzować cały ten przepływ pracy, konfigurując [automatyczną zamianę,](#Auto-Swap) gdy sprawdzanie poprawności przed wymianą nie jest potrzebne.
* Po zamianie gniazdo z wcześniej zorganizowaną aplikacją ma teraz poprzednią aplikację produkcyjną. Jeśli zmiany zamienione w gniazdo produkcyjne nie są takie, jak się spodziewasz, możesz natychmiast wykonać tę samą zamianę, aby odzyskać "ostatnią znaną dobrą witrynę".

Każda warstwa planu usługi app service obsługuje inną liczbę gniazd wdrażania. Nie ma żadnych dodatkowych opłat za korzystanie z gniazd wdrażania. Aby dowiedzieć się, ile gniazd obsługuje warstwa aplikacji, zobacz [Limity usługi App Service](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits). 

Aby skalować aplikację do innej warstwy, upewnij się, że warstwa docelowa obsługuje liczbę gniazd, których aplikacja już używa. Na przykład jeśli aplikacja ma więcej niż pięć gniazd, nie można skalować go w dół do **warstwy standardowej,** ponieważ warstwa **Standardowa** obsługuje tylko pięć gniazd wdrażania. 

<a name="Add"></a>

## <a name="add-a-slot"></a>Dodawanie miejsca
Aplikacja musi być uruchomiona w warstwie **Standardowa,** **Premium**lub **Izolowana,** aby można było włączyć wiele gniazd wdrażania.


1. w [witrynie Azure portal](https://portal.azure.com/)wyszukaj i wybierz **usługi aplikacji** oraz wybierz aplikację. 
   
    ![Wyszukiwanie usług aplikacji](./media/web-sites-staged-publishing/search-for-app-services.png)
   

2. W lewym okienku wybierz pozycję **Sloty** > wdrażania**Dodaj miejsce**.
   
    ![Dodawanie nowego miejsca wdrożenia](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)
   
   > [!NOTE]
   > Jeśli aplikacja nie jest jeszcze w warstwie **Standardowa,** **Premium**lub **Izolowana,** zostanie wyświetlony komunikat wskazujący obsługiwane warstwy umożliwiające publikowanie etapowe. W tym momencie możesz wybrać **uaktualnienie** i przejść do karty **Skalowanie** aplikacji przed kontynuowaniem.
   > 

3. W oknie **dialogowym Dodawanie gniazda** nadaj slotowi nazwę i wybierz, czy chcesz sklonować konfigurację aplikacji z innego gniazda wdrożenia. Wybierz **pozycję Dodaj,** aby kontynuować.
   
    ![Źródło konfiguracji](./media/web-sites-staged-publishing/ConfigurationSource1.png)
   
    Konfigurację można sklonować z dowolnego istniejącego gniazda. Ustawienia, które mogą być klonowane obejmują ustawienia aplikacji, parametry połączenia, wersje struktury językowej, gniazda sieci web, wersję HTTP i bitowości platformy.

4. Po dodaniu gniazda wybierz pozycję **Zamknij,** aby zamknąć okno dialogowe. Nowe gniazdo jest teraz wyświetlane na stronie **Sloty wdrażania.** Domyślnie **natężenie ruchu %** jest ustawiona na 0 dla nowego gniazda, przy czym cały ruch klienta jest kierowany do gniazda produkcyjnego.

5. Wybierz nowe miejsce wdrożenia, aby otworzyć stronę zasobu tego gniazda.
   
    ![Tytuł gniazda wdrożenia](./media/web-sites-staged-publishing/StagingTitle.png)

    Miejsce tymczasowe ma stronę zarządzania, tak jak każda inna aplikacja usługi App Service. Można zmienić konfigurację gniazda. Nazwa gniazda jest wyświetlana w górnej części strony, aby przypomnieć, że wyświetlasz gniazdo wdrażania.

6. Wybierz adres URL aplikacji na stronie zasobu gniazda. Gniazdo wdrażania ma własną nazwę hosta i jest również aplikacją na żywo. Aby ograniczyć publiczny dostęp do gniazda wdrażania, zobacz [Ograniczenia IP usługi Azure App Service](app-service-ip-restrictions.md).

Nowe gniazdo wdrażania nie ma zawartości, nawet jeśli sklonujesz ustawienia z innego gniazda. Na przykład, można [opublikować do tego gniazda z Git](app-service-deploy-local-git.md). Można wdrożyć w gnieździe z innej gałęzi repozytorium lub innego repozytorium. 

<a name="AboutConfiguration"></a>

## <a name="what-happens-during-a-swap"></a>Co dzieje się podczas wymiany

### <a name="swap-operation-steps"></a>Kroki operacji wymiany

Podczas wymiany dwóch slotów (zwykle z miejsca przejściowego do gniazda produkcyjnego), Usługa App Service wykonuje następujące czynności, aby upewnić się, że gniazdo docelowe nie wystąpi w przestoju:

1. Zastosuj następujące ustawienia z gniazda docelowego (na przykład gniazda produkcyjnego) do wszystkich wystąpień gniazda źródłowego: 
    - [Ustawienia](#which-settings-are-swapped) aplikacji specyficzne dla gniazda i parametry połączenia, jeśli ma to zastosowanie.
    - Ciągłe ustawienia [wdrażania,](deploy-continuous-deployment.md) jeśli jest włączona.
    - Ustawienia [uwierzytelniania usługi app service,](overview-authentication-authorization.md) jeśli jest włączona.
    
    Każdy z tych przypadków wyzwolić wszystkie wystąpienia w gnieździe źródłowym, aby ponownie uruchomić. Podczas [wymiany z podglądem](#Multi-Phase)oznacza to koniec pierwszej fazy. Operacja wymiany jest wstrzymana i można sprawdzić, czy gniazdo źródłowe działa poprawnie z ustawieniami gniazda docelowego.

1. Poczekaj na każde wystąpienie w gnieździe źródłowym, aby zakończyć jego ponowne uruchomienie. Jeśli każde wystąpienie nie powiedzie się ponownie, operacja wymiany przywraca wszystkie zmiany do gniazda źródłowego i zatrzymuje operację.

1. Jeśli [lokalna pamięć podręczna](overview-local-cache.md) jest włączona, wyzwalanie inicjowania lokalnej pamięci podręcznej przez wykonanie żądania HTTP do katalogu głównego aplikacji ("/") w każdym wystąpieniu gniazda źródłowego. Poczekaj, aż każde wystąpienie zwróci dowolną odpowiedź HTTP. Inicjowanie lokalnej pamięci podręcznej powoduje ponowne uruchomienie każdego wystąpienia.

1. Jeśli [automatyczna zamiana](#Auto-Swap) jest włączona z [niestandardowym rozgrzewaniem,](#Warm-up) [wyzwalanie inicjacji aplikacji](https://docs.microsoft.com/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) przez wykonanie żądania HTTP do katalogu głównego aplikacji ("/") w każdym wystąpieniu gniazda źródłowego.

    Jeśli `applicationInitialization` nie jest określony, wyzwolić żądanie HTTP do katalogu głównego aplikacji gniazda źródłowego w każdym wystąpieniu. 
    
    Jeśli wystąpienie zwraca dowolną odpowiedź HTTP, jest uważane za rozgrzane.

1. Jeśli wszystkie wystąpienia na gnieździe źródłowym są rozgrzane pomyślnie, zamienić dwa gniazda, przełączając reguły routingu dla dwóch gniazd. Po tym kroku gniazdo docelowe (na przykład gniazdo produkcyjne) ma aplikację, która jest wcześniej rozgrzana w gnieździe źródłowym.

1. Teraz, gdy gniazdo źródłowe ma wcześniej aplikację pre-swap w gnieździe docelowym, wykonaj tę samą operację, stosując wszystkie ustawienia i ponownie uruchamiając wystąpienia.

W dowolnym momencie operacji wymiany wszystkie prace inicjowania zamienionych aplikacji odbywa się na gnieździe źródłowym. Miejsce docelowe pozostaje w trybie online, podczas gdy gniazdo źródłowe jest przygotowywane i rozgrzewane, niezależnie od tego, gdzie zamiana zakończy się powodzeniem lub niepowodzeniem. Aby zamienić miejsce przejściowe z miejscem produkcyjnym, upewnij się, że gniazdo produkcyjne jest zawsze miejscem docelowym. W ten sposób operacja wymiany nie wpływa na aplikację produkcyjną.

### <a name="which-settings-are-swapped"></a>Które ustawienia są zamienione?

[!INCLUDE [app-service-deployment-slots-settings](../../includes/app-service-deployment-slots-settings.md)]

Aby skonfigurować ustawienie aplikacji lub parametry połączenia, aby trzymać się określonego gniazda (nie zamienione), przejdź do **konfiguracji** strony dla tego gniazda. Dodaj lub edytuj ustawienie, a następnie wybierz **ustawienie gniazda wdrożenia**. Zaznaczenie tego pola wyboru informuje usługę App Service, że ustawienie nie można zamienić. 

![Ustawienie gniazda](./media/web-sites-staged-publishing/SlotSetting.png)

<a name="Swap"></a>

## <a name="swap-two-slots"></a>Wymiana dwóch gniazd 
Miejsca wdrożenia można zamienić na stronie **sloty wdrażania** aplikacji i na stronie **Przegląd.** Aby uzyskać szczegółowe informacje na temat wymiany automatów, zobacz [Co się dzieje podczas wymiany](#AboutConfiguration).

> [!IMPORTANT]
> Przed zamienić aplikację z gniazda wdrożenia do środowiska produkcyjnego, upewnij się, że produkcja jest gniazdo docelowe i że wszystkie ustawienia w gnieździe źródłowym są skonfigurowane dokładnie tak, jak chcesz mieć je w środowisku produkcyjnym.
> 
> 

Aby zamienić miejsca wdrożenia:

1. Przejdź do strony **Sloty wdrażania** aplikacji i wybierz pozycję **Zamienić**.
   
    ![Przycisk Zamiana](./media/web-sites-staged-publishing/SwapButtonBar.png)

    Okno dialogowe **Zamiana** pokazuje ustawienia w wybranych gniazdach źródłowych i docelowych, które zostaną zmienione.

2. Wybierz żądane miejsca **źródłowe** i **docelowe.** Zazwyczaj celem jest gniazdo produkcyjne. Ponadto wybierz opcje **Zmiany źródłowe** i **Zmiany docelowe** i sprawdź, czy zmiany konfiguracji są oczekiwane. Po zakończeniu możesz natychmiast zamienić szczeliny, wybierając **opcję Zamienić**.

    ![Kończenie zamiany](./media/web-sites-staged-publishing/SwapImmediately.png)

    Aby zobaczyć, jak gniazdo docelowe będzie działać z nowymi ustawieniami, zanim zamiana rzeczywiście nastąpi, nie **wybieraj swap**, ale postępuj zgodnie z instrukcjami w [Swap z podglądem](#Multi-Phase).

3. Po zakończeniu zamknij okno dialogowe, wybierając pozycję **Zamknij**.

Jeśli masz jakiekolwiek problemy, zobacz [Rozwiązywanie problemów z swapami](#troubleshoot-swaps).

<a name="Multi-Phase"></a>

### <a name="swap-with-preview-multi-phase-swap"></a>Zamiana z podglądem (wymiana wielofazowa)

Przed zamianą do środowiska produkcyjnego jako gniazdo docelowe, sprawdź, czy aplikacja działa z zamienionych ustawień. Gniazdo źródłowe jest również rozgrzane przed zakończeniem wymiany, co jest pożądane w przypadku aplikacji o znaczeniu krytycznym.

Podczas wykonywania wymiany z podglądem usługa App Service wykonuje tę samą [operację wymiany,](#AboutConfiguration) ale wstrzymuje się po pierwszym kroku. Następnie można zweryfikować wynik na miejscu przejściowym przed zakończeniem wymiany. 

Jeśli anulujesz swap, usługa App Service ponownie pojawi się ponownie elementy konfiguracji do gniazda źródłowego.

Aby zamienić się z podglądem:

1. Wykonaj kroki opisane w [obszarze Zamiana gniazd wdrażania,](#Swap) ale wybierz opcję **Wykonaj zamianę z podglądu**.

    ![Zamiana z podglądem](./media/web-sites-staged-publishing/SwapWithPreview.png)

    Okno dialogowe pokazuje, jak zmienia się konfiguracja w gnieździe źródłowym w fazie 1 oraz jak gniazdo źródłowe i docelowe zmienia się w fazie 2.

2. Gdy będziesz gotowy do rozpoczęcia wymiany, wybierz opcję **Rozpocznij wymianę**.

    Po zakończeniu fazy 1 zostanie wyświetlone powiadomienie w oknie dialogowym. Podgląd wymiany w gnieździe `https://<app_name>-<source-slot-name>.azurewebsites.net`źródłowym, przechodząc do . 

3. Gdy będziesz gotowy do zakończenia oczekującej wymiany, wybierz **opcję Zakończ wymianę** w akcji **Wymiany** i wybierz opcję **Zakończ wymianę.**

    Aby anulować oczekującą zamianę, wybierz **pozycję Anuluj zamianę.**

4. Po zakończeniu zamknij okno dialogowe, wybierając pozycję **Zamknij**.

Jeśli masz jakiekolwiek problemy, zobacz [Rozwiązywanie problemów z swapami](#troubleshoot-swaps).

Aby zautomatyzować wymianę wielofazową, zobacz [Automatyzacja z programem PowerShell](#automate-with-powershell).

<a name="Rollback"></a>

## <a name="roll-back-a-swap"></a>Wycofywanie wymiany
Jeśli w gnieździe docelowym (na przykład w miejscu produkcyjnym) po wymianie gniazda wystąpią błędy, przywróć miejsca do stanów przedwstępnym, zamieniając natychmiast te same dwa gniazda.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>Konfigurowanie zamiany automatycznej

> [!NOTE]
> Automatyczna zamiana nie jest obsługiwana w aplikacjach internetowych w systemie Linux.

Automatyczna wymiana usprawnia scenariusze usługi Azure DevOps, w których chcesz stale wdrażać aplikację przy zerowym zimnym uruchomieniu i zerowym przestoju dla klientów aplikacji. Gdy automatyczna zamiana jest włączona z gniazda do produkcji, za każdym razem, gdy wypychasz zmiany kodu do tego gniazda, usługa App Service automatycznie [zamienia aplikację w produkcję](#swap-operation-steps) po jej rozgrzaniu w źródle.

   > [!NOTE]
   > Przed skonfigurowaniem automatycznej wymiany dla gniazda produkcyjnego należy rozważyć przetestowanie automatycznej wymiany na nieprodukcyjnym gnieździe docelowym.
   > 

Aby skonfigurować automatyczną wymianę:

1. Przejdź do strony zasobów aplikacji. Wybierz **miejsca wdrożenia żądane** > *\<gniazdo źródłowe>*  > **ustawienia ogólne** **konfiguracji** > .
   
2. W przypadku **opcji Automatyczna zamiana włączono**opcję **Wł.** Następnie wybierz żądane miejsce docelowe dla **gniazda wdrażania automatycznej wymiany**i wybierz pozycję **Zapisz** na pasku poleceń. 
   
    ![Wybory do konfigurowania automatycznej wymiany](./media/web-sites-staged-publishing/AutoSwap02.png)

3. Wykonaj wypychanie kodu do gniazda źródłowego. Automatyczna zamiana następuje po krótkim czasie, a aktualizacja zostanie odzwierciedlona w adresie URL docelowego gniazda.

Jeśli masz jakiekolwiek problemy, zobacz [Rozwiązywanie problemów z swapami](#troubleshoot-swaps).

<a name="Warm-up"></a>

## <a name="specify-custom-warm-up"></a>Określanie niestandardowego rozgrzewania

Niektóre aplikacje mogą wymagać niestandardowych akcji rozgrzewania przed zamienić. Element `applicationInitialization` konfiguracji w web.config umożliwia określenie niestandardowych akcji inicjowania. [Operacja wymiany](#AboutConfiguration) czeka na zakończenie tej niestandardowej rozgrzewki przed zamianą z gniazdem docelowym. Oto przykładowy fragment web.config.

    <system.webServer>
        <applicationInitialization>
            <add initializationPage="/" hostName="[app hostname]" />
            <add initializationPage="/Home/About" hostName="[app hostname]" />
        </applicationInitialization>
    </system.webServer>

Aby uzyskać więcej informacji `applicationInitialization` na temat dostosowywania elementu, zobacz [Najczęściej błędy wymiany gniazd wdrażania i jak je naprawić](https://ruslany.net/2017/11/most-common-deployment-slot-swap-failures-and-how-to-fix-them/).

Zachowanie rozgrzewki można również dostosować za pomocą jednego lub obu następujących [ustawień aplikacji:](configure-common.md)

- `WEBSITE_SWAP_WARMUP_PING_PATH`: Ścieżka do pingu, aby rozgrzać witrynę. Dodaj to ustawienie aplikacji, określając ścieżkę niestandardową, która zaczyna się od ukośnika jako wartości. Może to być na przykład `/statuscheck`. Wartością domyślną jest `/`. 
- `WEBSITE_SWAP_WARMUP_PING_STATUSES`: Prawidłowe kody odpowiedzi HTTP dla operacji rozgrzewania. Dodaj to ustawienie aplikacji z oddzieloną przecinkami listą kodów HTTP. Przykładem `200,202` jest . Jeśli zwróconego kodu stanu nie ma na liście, operacje rozgrzewania i wymiany są zatrzymywane. Domyślnie wszystkie kody odpowiedzi są prawidłowe.

> [!NOTE]
> Element `<applicationInitialization>` konfiguracji jest częścią każdego uruchomienia aplikacji, podczas gdy dwa ustawienia zachowania rozgrzewania aplikacji dotyczą tylko swapów gniazda.

Jeśli masz jakiekolwiek problemy, zobacz [Rozwiązywanie problemów z swapami](#troubleshoot-swaps).

## <a name="monitor-a-swap"></a>Monitorowanie wymiany

Jeśli [operacja wymiany](#AboutConfiguration) zajmuje dużo czasu, można uzyskać informacje na temat operacji wymiany w [dzienniku aktywności](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

Na stronie zasobów aplikacji w portalu w lewym okienku wybierz pozycję **Dziennik aktywności**.

Operacja wymiany pojawia się w `Swap Web App Slots`kwerendzie dziennika jako . Można go rozwinąć i wybrać jedną z poddziałania lub błędy, aby wyświetlić szczegóły.

## <a name="route-traffic"></a>Ruch na trasie

Domyślnie wszystkie żądania klientów do produkcyjnego`http://<app_name>.azurewebsites.net`adresu URL aplikacji ( ) są kierowane do gniazda produkcyjnego. Część ruchu można skierować do innego gniazda. Ta funkcja jest przydatna, jeśli potrzebujesz opinii użytkowników na nową aktualizację, ale nie jesteś gotowy do wydania go do wersji produkcyjnej.

### <a name="route-production-traffic-automatically"></a>Automatyczne kierowanie ruchu produkcyjnego

Aby automatycznie rozsyłać ruch produkcyjny:

1. Przejdź do strony zasobów aplikacji i wybierz **pozycję Miejsca wdrożenia**.

2. W kolumnie **Ruch %** gniazda, do którego chcesz kierować, określ wartość procentową (od 0 do 100), aby reprezentować całkowity ruch, który ma być kierowany. Wybierz **pozycję Zapisz**.

    ![Ustawianie procentu ruchu](./media/web-sites-staged-publishing/RouteTraffic.png)

Po zapisaniu ustawienia określony procent klientów jest losowo kierowany do gniazda nieprodukcyjnego. 

Po klient jest automatycznie kierowane do określonego gniazda, jest "przypięty" do tego gniazda przez cały okres tej sesji klienta. W przeglądarce klienta możesz zobaczyć, do którego gniazda przypięta jest sesja, patrząc na `x-ms-routing-name` plik cookie w nagłówkach HTTP. Żądanie, które jest kierowane do gniazda "przejściowe" ma plik cookie `x-ms-routing-name=staging`. Żądanie, które jest kierowane do gniazda `x-ms-routing-name=self`produkcyjnego ma plik cookie .

   > [!NOTE]
   > Obok witryny Azure portal można [`az webapp traffic-routing set`](/cli/azure/webapp/traffic-routing#az-webapp-traffic-routing-set) również użyć polecenia w narzędziu wiersza polecenia platformy Azure, aby ustawić wartości procentowe routingu z narzędzi ciągłej integracji/ciągłego wdrażania, takich jak potoki DevOps lub inne systemy automatyzacji.
   > 

### <a name="route-production-traffic-manually"></a>Ręcznie rozsyłaj ruch produkcyjny

Oprócz automatycznego routingu ruchu usługa App Service może kierować żądania do określonego gniazda. Jest to przydatne, gdy chcesz, aby użytkownicy mogli wyrazić zgodę na aplikację w wersji beta lub zrezygnować z nich. Aby ręcznie rozsyłać `x-ms-routing-name` ruch produkcyjny, należy użyć parametru kwerendy.

Aby umożliwić użytkownikom rezygnację z aplikacji w wersji beta, możesz na przykład umieścić ten link na swojej stronie internetowej:

```HTML
<a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>
```

Ciąg `x-ms-routing-name=self` określa gniazdo produkcyjne. Po przeglądarce klienta uzyskuje dostęp do łącza, jest przekierowywane do gniazda produkcyjnego. Każde kolejne żądanie `x-ms-routing-name=self` ma plik cookie, który przypina sesję do gniazda produkcyjnego.

Aby umożliwić użytkownikom wybranie aplikacji w wersji beta, ustaw ten sam parametr zapytania na nazwę gniazda nieprodukcyjnego. Oto przykład:

```
<webappname>.azurewebsites.net/?x-ms-routing-name=staging
```

Domyślnie nowe gniazda mają regułę routingu `0%`, pokazaną na szaro. Po jawnym ustawieniu `0%` tej wartości (wyświetlanej w czarnym tekście) użytkownicy `x-ms-routing-name` mogą ręcznie uzyskać dostęp do miejsca przejściowego przy użyciu parametru kwerendy. Ale nie będą one kierowane do gniazda automatycznie, ponieważ procent routingu jest ustawiony na 0. Jest to zaawansowany scenariusz, w którym można "ukryć" miejsce tymczasowe przed publicznością, umożliwiając zespołom wewnętrznym testowanie zmian w gnieździe.

<a name="Delete"></a>

## <a name="delete-a-slot"></a>Usuwanie gniazda

Wyszukaj i wybierz aplikację. Wybierz miejsce **na sloty** > *\<wdrażania, aby usunąć>*  >  **Przegląd**. Wybierz **pozycję Usuń** na pasku poleceń.  

![Usuwanie miejsca wdrożenia](./media/web-sites-staged-publishing/DeleteStagingSiteButton.png)

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-powershell"></a>Automatyzacja przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure PowerShell to moduł, który zapewnia polecenia cmdlet do zarządzania platformą Azure za pośrednictwem programu Windows PowerShell, w tym obsługę zarządzania miejscami wdrażania w usłudze Azure App Service.

Aby uzyskać informacje dotyczące instalowania i konfigurowania programu Azure PowerShell oraz uwierzytelniania programu Azure PowerShell za pomocą subskrypcji platformy Azure, zobacz [Jak zainstalować i skonfigurować program Microsoft Azure PowerShell](/powershell/azure/overview).  

---
### <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej
```powershell
New-AzWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

---
### <a name="create-a-slot"></a>Tworzenie gniazda
```powershell
New-AzWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

---
### <a name="initiate-a-swap-with-a-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-the-source-slot"></a>Inicjowanie zamiany z podglądem (zamiana wielofazowa) i stosowanie konfiguracji gniazda docelowego do gniazda źródłowego
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. "production"]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

---
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-the-source-slot-configuration"></a>Anulowanie oczekującej wymiany (zamiana z recenzją) i przywrócenie konfiguracji gniazda źródłowego
```powershell
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

---
### <a name="swap-deployment-slots"></a>Zamiana miejsc wdrożenia
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. "production"]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>Monitorowanie zdarzeń wymiany w dzienniku aktywności
```powershell
Get-AzLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

---
### <a name="delete-a-slot"></a>Usuwanie gniazda
```powershell
Remove-AzResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

## <a name="automate-with-resource-manager-templates"></a>Automatyzacja za pomocą szablonów Menedżera zasobów

[Szablony usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/template-deployment-overview) są deklaratywnymi plikami JSON używanymi do automatyzacji wdrażania i konfiguracji zasobów platformy Azure. Aby zamienić gniazda przy użyciu szablonów Menedżera zasobów, należy ustawić dwie właściwości w *zasobach Microsoft.web/sites/slots* i *Microsoft.web/sites:*

- `buildVersion`: jest to właściwość string, która reprezentuje bieżącą wersję aplikacji wdrożonej w gnieździe. Na przykład: "v1", "1.0.0.1" lub "2019-09-20T11:53:25.2887393-07:00".
- `targetBuildVersion`: jest to właściwość string, która określa, co `buildVersion` gniazdo powinno mieć. Jeśli targetBuildVersion nie jest `buildVersion`równa bieżącej, spowoduje to uruchomienie operacji wymiany `buildVersion`przez znalezienie gniazda, które ma określony .

### <a name="example-resource-manager-template"></a>Przykładowy szablon Menedżera zasobów

Poniższy szablon Menedżera zasobów `buildVersion` zaktualizuje miejsce `targetBuildVersion` przejściowe i ustawi na miejscu produkcyjnym. Spowoduje to zamianę dwóch gniazd. Szablon zakłada, że masz już webapp utworzony z gniazda o nazwie "staging".

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "my_site_name": {
            "defaultValue": "SwapAPIDemo",
            "type": "String"
        },
        "sites_buildVersion": {
            "defaultValue": "v1",
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Web/sites/slots",
            "apiVersion": "2018-02-01",
            "name": "[concat(parameters('my_site_name'), '/staging')]",
            "location": "East US",
            "kind": "app",
            "properties": {
                "buildVersion": "[parameters('sites_buildVersion')]"
            }
        },
        {
            "type": "Microsoft.Web/sites",
            "apiVersion": "2018-02-01",
            "name": "[parameters('my_site_name')]",
            "location": "East US",
            "kind": "app",
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites/slots', parameters('my_site_name'), 'staging')]"
            ],
            "properties": {
                "targetBuildVersion": "[parameters('sites_buildVersion')]"
            }
        }        
    ]
}
```

Ten szablon Menedżera zasobów jest idempotentny, co oznacza, że może być wykonywany wielokrotnie i produkować ten sam stan szczelin. Po pierwszym wykonaniu, `targetBuildVersion` będzie `buildVersion`pasować do bieżącego , więc swap nie zostanie uruchomiony.

<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-the-cli"></a>Automatyzacja za pomocą interfejsu wiersza polecenia

W przypadku poleceń [interfejsu wiersza polecenia interfejsu wiersza](https://github.com/Azure/azure-cli) polecenia platformy Azure dla gniazd wdrażania zobacz [gniazdo wdrażania aplikacji az webapp](/cli/azure/webapp/deployment/slot).

## <a name="troubleshoot-swaps"></a>Rozwiązywanie problemów z swapami

Jeśli podczas wymiany [gniazda](#AboutConfiguration)wystąpi błąd, jest on zalogowany w *D:\home\LogFiles\eventlog.xml*. Jest również zalogowany w dzienniku błędów specyficznych dla aplikacji.

Oto kilka typowych błędów wymiany:

- Żądanie HTTP do katalogu głównego aplikacji jest zdąwiane. Operacja wymiany czeka na 90 sekund dla każdego żądania HTTP i ponawia ponowną próby do 5 razy. Jeśli limit czasu wszystkich ponownych prób jest przesunął, operacja wymiany jest zatrzymana.

- Inicjowanie lokalnej pamięci podręcznej może zakończyć się niepowodzeniem, gdy zawartość aplikacji przekroczy przydział dysku lokalnego określony dla lokalnej pamięci podręcznej. Aby uzyskać więcej informacji, zobacz [Omówienie lokalnej pamięci podręcznej](overview-local-cache.md).

- Podczas [niestandardowego rozgrzewania](#Warm-up)żądania HTTP są składane wewnętrznie (bez przechodzenia przez zewnętrzny adres URL). Mogą zakończyć się niepowodzeniem z niektórych reguł ponownego zapisywania adresów URL w *web.config*. Na przykład reguły przekierowywania nazw domen lub wymuszania protokołu HTTPS mogą uniemożliwić dotarcie do kodu aplikacji żądań rozgrzewania. Aby obejść ten problem, zmodyfikuj reguły ponownego zapisu, dodając następujące dwa warunki:

    ```xml
    <conditions>
      <add input="{WARMUP_REQUEST}" pattern="1" negate="true" />
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Bez niestandardowego rozgrzewania reguły przepisywania adresów URL nadal mogą blokować żądania HTTP. Aby obejść ten problem, zmodyfikuj reguły przepisywania, dodając następujący warunek:

    ```xml
    <conditions>
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Niektóre [reguły ograniczeń IP](app-service-ip-restrictions.md) mogą uniemożliwić operacji wymiany wysyłanie żądań HTTP do aplikacji. Zakresy adresów IPv4, które rozpoczynają się `10.` i `100.` są wewnętrzne dla twojego wdrożenia. Należy zezwolić im na łączenie się z aplikacją.

- Po zamiany gniazda, aplikacja może wystąpić nieoczekiwane ponowne uruchomienie. Dzieje się tak, ponieważ po zamianie konfiguracja wiązania nazwy hosta nie jest zsynchronizowana, co samo w sobie nie powoduje ponownego uruchomienia. Jednak niektóre podstawowe zdarzenia magazynu (takie jak przewijanych w tryb failover woluminu magazynu) mogą wykryć te rozbieżności i wymusić ponowne uruchomienie wszystkich procesów roboczych. Aby zminimalizować tego typu ponowne uruchomienie, ustaw [ `WEBSITE_ADD_SITENAME_BINDINGS_IN_APPHOST_CONFIG=1` ustawienie aplikacji](https://github.com/projectkudu/kudu/wiki/Configurable-settings#disable-the-generation-of-bindings-in-applicationhostconfig) na *wszystkich gniazdach*. Jednak to ustawienie aplikacji *nie* działa z aplikacjami Windows Communication Foundation (WCF).

## <a name="next-steps"></a>Następne kroki
[Blokowanie dostępu do nieprodukcyjnych slotów](app-service-ip-restrictions.md)
