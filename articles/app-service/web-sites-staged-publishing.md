---
title: Konfigurowanie środowisk dla aplikacji sieci web w usłudze Azure App Service przejściowych | Dokumentacja firmy Microsoft
description: Dowiedz się, jak użyć przemieszczanego publikowania aplikacji sieci web w usłudze Azure App Service.
services: app-service
documentationcenter: ''
author: cephalin
writer: cephalin
manager: erikre
editor: mollybos
ms.assetid: e224fc4f-800d-469a-8d6a-72bcde612450
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: cephalin
ms.openlocfilehash: c02b7a74eea6973d6ccfbc1cc59d15bfd5cb5b77
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Konfigurowanie środowisk w usłudze Azure App Service przejściowych
<a name="Overview"></a>

Podczas wdrażania aplikację sieci web, aplikacji sieci web w systemie Linux, przenośne zaplecza i aplikacji interfejsu API [usługi aplikacji](http://go.microsoft.com/fwlink/?LinkId=529714), można wdrożyć na miejsce wdrożenia oddzielne zamiast domyślnego gniazda produkcyjnego podczas uruchamiania **standardowe** lub **Premium** warstwy planu usługi aplikacji. Miejsca wdrożenia są faktycznie na żywo aplikacji za pomocą ich własnych nazwy hostów. Elementy zawartości i konfiguracji aplikacji może być zamieniona między dwóch miejsc wdrożenia, w tym miejsca produkcji. Wdrażanie aplikacji w miejscu wdrożenia ma następujące zalety:

* Zmiany przemieszczania miejsce wdrożenia aplikacji może sprawdzać przed zamienienie go z miejscem produkcyjnym.
* Wdrażanie aplikacji na gnieździe najpierw i zamienienie go w środowisku produkcyjnym gwarantuje, że wszystkie wystąpienia gniazda są przygotowaniu miejsca przed wymieniane w środowisku produkcyjnym. Eliminuje to czas przestoju, podczas wdrażania aplikacji. Przekierowywanie ruchu jest łatwego i żadne żądania są usuwane w wyniku operacji wymiany. Ta całego przepływu pracy można zautomatyzować poprzez skonfigurowanie [automatycznej wymiany](#Auto-Swap) podczas weryfikacji przed wymiany nie jest wymagana.
* Po wymiany gniazda z wcześniej przygotowanych aplikacji ma poprzedniej aplikacji produkcyjnej. Jeśli zmiany miejscami do miejsca produkcji są niezgodne z oczekiwaniami, można wykonać tego samego wymiany od razu do pobrania "ostatniej znanej dobrej witryny" ponownie.

Każda warstwa planu usługi aplikacji obsługuje różne liczby miejsc wdrożenia. Aby dowiedzieć się, liczba gniazd obsługuje warstwy aplikacji, zobacz [limity usługi App](https://docs.microsoft.com/en-us/azure/azure-subscription-service-limits#app-service-limits).

* Gdy aplikacja ma wiele miejsc, nie można zmienić warstwy.
* Skalowanie jest niedostępna dla gniazda nieprodukcyjnych.
* Zarządzanie połączonego zasobu nie jest obsługiwane dla gniazda nieprodukcyjnych. W [Azure Portal](http://go.microsoft.com/fwlink/?LinkId=529715) , można uniknąć ten potencjalny wpływ na gnieździe produkcyjnym przenosząc tymczasowo miejsca nieprodukcyjnych z inną warstwą planu usługi aplikacji. Należy pamiętać, że gniazdo nieprodukcyjnych musi ponownie udostępnić tej samej warstwie gniazda produkcyjnego przed można wymienić dwóch miejsc.

<a name="Add"></a>

## <a name="add-a-deployment-slot"></a>Dodaj miejsce wdrożenia
Aplikacja musi być uruchomiona **standardowe** lub **Premium** warstwy w kolejności, aby włączyć wielu miejsc wdrożenia.

1. W [Azure Portal](https://portal.azure.com/), otwórz aplikacji [bloku zasobów](../azure-resource-manager/resource-group-portal.md#manage-resources).
2. Wybierz **miejsc wdrożenia** opcji, a następnie kliknij przycisk **Dodaj miejsce**.
   
    ![Dodaj nowe miejsce wdrożenia][QGAddNewDeploymentSlot]
   
   > [!NOTE]
   > Jeśli aplikacja nie jest już w **standardowe** lub **Premium** warstwy, zostanie wyświetlony komunikat informujący o obsługiwanych warstw umożliwiających publikowanie przemieszczane. W tym momencie masz możliwość wybrania **uaktualnienia** i przejdź do **skali** kartę aplikacji przed kontynuowaniem.
   > 
   > 
3. W **dodać gniazdo** bloku, nazwę miejsca i wybierz, czy Klonuj konfiguracji aplikacji z innego istniejącego miejsca wdrożenia. Kliknij znacznik wyboru, aby kontynuować.
   
    ![Źródło konfiguracji][ConfigurationSource1]
   
    Pierwsze dodać gniazdo, będziesz korzystać tylko z dwóch opcji: klonowania konfiguracji z miejsca domyślne w środowisku produkcyjnym lub w ogóle.
    Po utworzeniu kilka miejsc, można Klonuj konfiguracji z miejsca innego niż ten, w środowisku produkcyjnym:
   
    ![Konfiguracja źródła][MultipleConfigurationSources]
4. W bloku zasobów aplikacji, kliknij przycisk **miejsc wdrożenia**, następnie kliknij przycisk miejsce wdrożenia, aby otworzyć blok zasobów z miejsca, przy użyciu zestawu metryki i konfiguracji, tak jak każda inna aplikacja. Nazwa miejsca jest wyświetlany w górnej części bloku celu odnotowania, że miejsca wdrożenia.
   
    ![Tytuł miejsca wdrożenia][StagingTitle]
5. Kliknij adres URL aplikacji w bloku tego gniazda. Zwróć uwagę, miejsce wdrożenia ma własną nazwę hosta i jest również aktywnej aplikacji. Aby ograniczyć publiczny dostęp do miejsca wdrożenia, zobacz [aplikacji sieci Web usługi aplikacji — Blokuj dostęp w sieci web do miejsc wdrożenia nieprodukcyjnych](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/).

Brak zawartości po utworzeniu miejsca wdrożenia. Można wdrożyć do gniazda z gałęzi repozytorium różnych lub całkowicie innego repozytorium. Można również zmienić konfigurację tego gniazda. Użyj poświadczeń profilu lub wdrożenia publikowania skojarzone z miejscem wdrożenia aktualizacji zawartości.  Można na przykład [publikowanie do tego miejsca za pomocą narzędzia git](app-service-deploy-local-git.md).

<a name="AboutConfiguration"></a>

## <a name="configuration-for-deployment-slots"></a>Konfiguracja dla miejsc wdrożenia
Klonuj konfiguracji z innego miejsca wdrożenia, sklonowany konfiguracji po edycji. Ponadto niektóre elementy konfiguracji będzie śledzić zawartości między swap (nie gniazdo określonych), podczas gdy inne elementy konfiguracji, pozostanie w tym samym miejscu po swap (gniazdo określone). Konfiguracja ulegnie zmianie po zamienić miejsc przedstawiono.

**Ustawienia, które są zamienione**:

* Ustawienia ogólne — takie jak gniazda sieci Web 32/64-bitowych wersji framework
* Ustawienia aplikacji (może być skonfigurowana do osadzania miejsca)
* Parametry połączenia (można skonfigurować w celu osadzania miejsca)
* Mapowania procedur obsługi
* Ustawienia monitorowania i diagnostyki
* Zawartość zadań Webjob

**Ustawienia, które nie są zamienione**:

* Publikowanie punktów końcowych
* Niestandardowa nazwa domeny
* Certyfikaty SSL i powiązań
* Ustawienia skali
* Planiści zadań Webjob

Aby skonfigurować aplikację ustawienie lub parametry połączenia trzymać miejsca (nie miejscami), dostęp do **ustawienia aplikacji** bloku do określonego miejsca, następnie wybierz **ustawienie miejsca** pola konfiguracji elementy, które powinny trzymaj gniazda. Należy pamiętać, że oznaczenie element konfiguracji zgodnie z miejsca określonego powoduje ustanowienie tego elementu jako nie swappable wszystkich miejsc wdrożenia skojarzone z aplikacją.

![Ustawienia gniazda][SlotSettings]

<a name="Swap"></a>

## <a name="swap-deployment-slots"></a>Zamienić miejsc wdrażania 
Można zamienić miejsc wdrożenia w **omówienie** lub **miejsc wdrożenia** widok bloku zasobów aplikacji.

> [!IMPORTANT]
> Przed możesz zamienić aplikację z miejsce wdrożenia do środowiska produkcyjnego, upewnij się, że wszystkie z systemem innym niż miejsce określone ustawienia są skonfigurowane zgodnie z oczekiwaniami go w celu wymiany.
> 
> 

1. Aby zamienić miejsc wdrożenia, kliknij przycisk **wymiany** przycisku w pasku poleceń, aplikacji lub na pasku poleceń w miejscu wdrożenia.
   
    ![Przycisk wymiany][SwapButtonBar]

2. Upewnij się, czy wymiany źródłowych i docelowych wymiany są poprawnie ustawione. Zazwyczaj obiekt docelowy wymiany jest miejsca produkcji. Kliknij przycisk **OK** do ukończenia tej operacji. Po zakończeniu operacji, zostały zamieniono miejsca wdrożenia.

    ![Ukończ zamianę](./media/web-sites-staged-publishing/SwapImmediately.png)

    Dla **zamiany z podglądem** wymiany typu, zobacz [zamiany z podglądem (faza wielu wymiany)](#Multi-Phase).  

<a name="Multi-Phase"></a>

## <a name="swap-with-preview-multi-phase-swap"></a>Zamiana z podglądem (faza wielu wymiany)

Zamiany z podglądem lub wielu faza wymiany uprościć weryfikacji konfiguracji specyficznych dla miejsca elementów, takich jak parametry połączenia.
W przypadku obciążeń krytycznym chcesz zweryfikować których aplikacja działa zgodnie z oczekiwaniami, po zastosowaniu konfiguracji miejsca produkcyjnego i należy wykonać takie weryfikacji *przed* aplikacji jest zamieniane w środowisku produkcyjnym. Zamiany z podglądem jest, co jest potrzebne.

> [!NOTE]
> Zamiany z podglądem jest nieobsługiwana w aplikacjach sieci web w systemie Linux.

Jeśli używasz **zamiana z podglądem** opcji (zobacz [zamienić miejsc wdrożenia](#Swap)), usługi aplikacji — wykonuje następujące czynności:

- Przechowuje miejsca docelowego bez zmian, więc nie ma wpływu na istniejące obciążenia na gniazdo (np. produkcja).
- Dotyczy elementów konfiguracji z miejsca docelowego do miejsca źródłowego, w tym parametry połączenia specyficzne dla miejsca i ustawień aplikacji.
- Uruchamia ponownie procesów roboczych na miejsca źródłowego przy użyciu tych elementów konfiguracji wyżej.
- Po zakończeniu wymiany: Przenosi miejsca źródłowego wstępnie przygotowany warmed up do miejsca docelowego. Miejscem docelowym jest przenoszony do miejsca źródłowego, jak ręcznie wymiany.
- Jeśli anulujesz wymiany: ponowne zastosowanie elementy konfiguracji z miejsca źródłowego do miejsca źródłowego.

Można wyświetlić podgląd dokładnie jak aplikacja będzie się odbywać z konfiguracji z miejsca docelowego. Po ukończeniu sprawdzania poprawności zakończeniu wymiany w osobnym kroku. Ten krok ma dodatkową zaletę, który jest już przygotowaniu miejsca źródłowego z odpowiednią konfigurację, a klienci nie będzie działać z żadnych przestojów.  

Przykłady dla poleceń cmdlet programu Azure PowerShell, dostępna dla wielu faza wymiany znajdują się w poleceniach cmdlet programu PowerShell systemu Azure dla sekcji miejsc wdrożenia.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>Konfigurowanie automatycznej wymiany (MB)
Automatycznej wymiany usprawnia scenariuszy opracowywania oprogramowania miejscu stale wdrażania aplikacji za pomocą zero zimny start i przestojów dla klientów końcowych w aplikacji. Jeśli miejsce wdrożenia jest skonfigurowany do automatycznej wymiany do produkcji, zawsze wypychania aktualizacji kodu do tego miejsca, usługi aplikacji — automatycznie wymiany aplikacji w środowisku produkcyjnym po już ma przygotowaniu miejsca w miejscu.

> [!IMPORTANT]
> Po włączeniu automatycznej wymiany dla miejsca, upewnij się, że konfiguracja miejsca jest dokładnie konfiguracji przeznaczonych dla miejsca docelowego (zazwyczaj gniazda produkcyjnego).
> 
> 

> [!NOTE]
> Automatycznej wymiany nie jest obsługiwana w aplikacjach sieci web w systemie Linux.

Konfigurowanie automatycznej wymiany gnieździe jest bardzo proste. Wykonaj poniższe kroki:

1. W **miejsc wdrożenia**, a następnie wybierz gniazdo nieprodukcyjnych i wybierz **ustawienia aplikacji** w bloku zasobów z tego miejsca.  
   
    ![][Autoswap1]
2. Wybierz **na** dla **automatycznej wymiany**, wybierz gniazdo docelowy w **automatycznej wymiany miejsca**i kliknij przycisk **zapisać** na pasku poleceń. Upewnij się, że konfiguracja gniazda jest dokładnie konfiguracji przeznaczonych dla miejsca docelowego.
   
    **Powiadomienia** kartę będzie flash zielona **Powodzenie** po zakończeniu operacji.
   
    ![][Autoswap2]
   
   > [!NOTE]
   > Aby przetestować automatycznej wymiany dla aplikacji, najpierw wybrać gnieździe docelowym nieprodukcyjnych **automatycznej wymiany miejsca** zapoznać się z funkcji.  
   > 
   > 
3. Wykonanie kodu wypychania do tego miejsca wdrożenia. Automatycznej wymiany nastąpi po pewnym czasie i aktualizacji zostaną odzwierciedlone w adresie URL z miejsca docelowego.

<a name="Rollback"></a>

## <a name="to-rollback-a-production-app-after-swap"></a>Aby wycofać aplikacji produkcyjnej po wymiany
Jeśli wszystkie błędy są identyfikowane w środowisku produkcyjnym po zakończeniu wymiany gniazd, wycofywanie gniazdach do stanu przed wymiany natychmiast wymiany tego samego dwa gniazda.

<a name="Warm-up"></a>

## <a name="custom-warm-up-before-swap"></a>Niestandardowe rozgrzewania przed wymiany
Niektóre aplikacje mogą wymagać akcje niestandardowe rozgrzewania. `applicationInitialization` Element konfiguracji w pliku web.config można określić niestandardową inicjalizację akcje można wykonać, zanim żądanie zostanie odebrane. Operacja zamiany będzie oczekiwał na ten niestandardowe zwiększanie gotowości do ukończenia. Oto przykładowe fragment pliku web.config.

    <applicationInitialization>
        <add initializationPage="/" hostName="[app hostname]" />
        <add initializationPage="/Home/About" hostname="[app hostname]" />
    </applicationInitialization>

<a name="Delete"></a>

## <a name="to-delete-a-deployment-slot"></a>Aby usunąć miejsce wdrożenia
W bloku dla miejsca wdrożenia, otwórz blok miejsce wdrożenia, kliknij pozycję **omówienie** (domyślna strona) i kliknij przycisk **usunąć** na pasku poleceń.  

![Usuń miejsce wdrożenia][DeleteStagingSiteButton]

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="azure-powershell-cmdlets-for-deployment-slots"></a>Polecenia cmdlet programu PowerShell systemu Azure dla miejsc wdrożenia
Program Azure PowerShell jest moduł, który udostępnia polecenia cmdlet do zarządzania za pomocą środowiska Windows PowerShell, włącznie z obsługą zarządzania miejsc wdrożenia w usłudze Azure App Service.

* Aby uzyskać informacje na temat instalowania i konfigurowania programu Azure PowerShell, a na uwierzytelniania programu Azure PowerShell z subskrypcją platformy Azure, zobacz [jak instalowanie i konfigurowanie programu Microsoft Azure PowerShell](/powershell/azure/overview).  

- - -
### <a name="create-a-web-app"></a>Tworzenie aplikacji sieci Web
```
New-AzureRmWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

- - -
### <a name="create-a-deployment-slot"></a>Tworzenie miejsca wdrożenia
```
New-AzureRmWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

- - -
### <a name="initiate-a-swap-with-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-source-slot"></a>Zainicjuj zamiana z podglądem (faza wielu wymiany) i zastosować konfiguracji miejsca docelowego do miejsca źródłowego
```
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

- - -
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-source-slot-configuration"></a>Anuluj oczekująca zamiana (obszar wymiany z przeglądem) i przywracanie konfiguracji miejsca źródłowego
```
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

- - -
### <a name="swap-deployment-slots"></a>Zamienić miejsc wdrażania
```
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

- - -
### <a name="delete-deployment-slot"></a>Usuń miejsce wdrożenia
```
Remove-AzureRmResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

- - -
<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="azure-command-line-interface-azure-cli-commands-for-deployment-slots"></a>Azure polecenia interfejsu wiersza polecenia (Azure CLI) dla miejsc wdrożenia
Interfejsu wiersza polecenia Azure udostępnia polecenia i platform do pracy z platformą Azure, w tym obsługę zarządzania miejsc wdrożenia usługi aplikacji.

* Aby uzyskać instrukcje dotyczące instalowania i konfigurowania wiersza polecenia platformy Azure, w tym informacje na temat nawiązywania połączenia z subskrypcją platformy Azure, Azure CLI zobacz [Instalowanie i Konfigurowanie interfejsu wiersza polecenia Azure](../cli-install-nodejs.md).
* Aby wyświetlić listę dostępnych poleceń dla usługi Azure App Service w wiersza polecenia platformy Azure, należy wywołać `azure site -h`.

> [!NOTE] 
> Dla [Azure CLI 2.0](https://github.com/Azure/azure-cli) poleceń dla miejsc wdrożenia, zobacz [miejsce wdrożenia aplikacji sieci Web az](/cli/azure/webapp/deployment/slot).

- - -
### <a name="azure-site-list"></a>Lista witryn platformy Azure
Informacje o aplikacji w bieżącej subskrypcji, należy wywołać **listy witryn azure**, jak w poniższym przykładzie.

`azure site list webappslotstest`

- - -
### <a name="azure-site-create"></a>Tworzenie usługi Azure site
Aby utworzyć miejsce wdrożenia, należy wywołać **Tworzenie usługi azure site** i określ nazwę istniejącej aplikacji oraz nazwę gniazda, aby utworzyć, jak w poniższym przykładzie.

`azure site create webappslotstest --slot staging`

Aby włączyć kontrolę źródła dla nowego miejsca, za pomocą **— git** opcji, jak w poniższym przykładzie.

`azure site create --git webappslotstest --slot staging`

- - -
### <a name="azure-site-swap"></a>usługi Azure site wymiany (MB)
Aby wdrażania aktualizacji gniazdo aplikacji produkcyjnej, użyj **wymiany usługi azure site** polecenie, aby wykonać operację zamiany, jak w poniższym przykładzie. Aplikacji produkcyjnej nie będą występować dowolne czas przestoju, nie zostaną poddane zimny start.

`azure site swap webappslotstest`

- - -
### <a name="azure-site-delete"></a>Usuwanie witryny platformy Azure
Aby usunąć miejsce wdrożenia, który nie jest już potrzebny, należy użyć **usuwanie witryny azure** polecenia, jak w poniższym przykładzie.

`azure site delete webappslotstest --slot staging`

- - -
> [!NOTE]
> Zobacz działającą aplikację sieci Web. [Wypróbuj usługę App Service](https://azure.microsoft.com/try/app-service/) natychmiast i tej utworzyć początkową aplikację — bez karty kredytowej i bez zobowiązań.
> 
> 

## <a name="next-steps"></a>Następne kroki
[Azure App Service aplikacji sieci Web — Blokuj dostęp w sieci web do miejsc wdrożenia nieprodukcyjnych](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)
[wprowadzenie do usługi App Service w systemie Linux](../app-service/containers/app-service-linux-intro.md)
[bezpłatna wersja próbna programu Microsoft Azure](https://azure.microsoft.com/pricing/free-trial/)

<!-- IMAGES -->
[QGAddNewDeploymentSlot]:  ./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png
[AddNewDeploymentSlotDialog]: ./media/web-sites-staged-publishing/AddNewDeploymentSlotDialog.png
[ConfigurationSource1]: ./media/web-sites-staged-publishing/ConfigurationSource1.png
[MultipleConfigurationSources]: ./media/web-sites-staged-publishing/MultipleConfigurationSources.png
[SiteListWithStagedSite]: ./media/web-sites-staged-publishing/SiteListWithStagedSite.png
[StagingTitle]: ./media/web-sites-staged-publishing/StagingTitle.png
[SwapButtonBar]: ./media/web-sites-staged-publishing/SwapButtonBar.png
[SwapConfirmationDialog]:  ./media/web-sites-staged-publishing/SwapConfirmationDialog.png
[DeleteStagingSiteButton]: ./media/web-sites-staged-publishing/DeleteStagingSiteButton.png
[SwapDeploymentsDialog]: ./media/web-sites-staged-publishing/SwapDeploymentsDialog.png
[Autoswap1]: ./media/web-sites-staged-publishing/AutoSwap01.png
[Autoswap2]: ./media/web-sites-staged-publishing/AutoSwap02.png
[SlotSettings]: ./media/web-sites-staged-publishing/SlotSetting.png

