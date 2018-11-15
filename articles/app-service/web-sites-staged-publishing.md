---
title: Konfigurowanie środowiska tymczasowego dla aplikacji sieci web w usłudze Azure App Service | Dokumentacja firmy Microsoft
description: Dowiedz się, jak korzystać z publikowania etapowego dla aplikacji sieci web w usłudze Azure App Service.
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
ms.openlocfilehash: b5a06cff653007568b4ab2b44624b6314413f8a6
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2018
ms.locfileid: "51636071"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Konfigurowanie środowisk przejściowych w usłudze Azure App Service
<a name="Overview"></a>

Podczas wdrażania Twojej aplikacji sieci web, aplikacji sieci web w systemie Linux, zaplecze dla aplikacji mobilnych i aplikacji interfejsu API [usługi App Service](https://go.microsoft.com/fwlink/?LinkId=529714), można wdrożyć do gniazda wdrażane pojedynczo, zamiast domyślnego miejscem produkcyjnym podczas pracy w **standardowa**, **Premium**, lub **izolowany** warstwę planu usługi App Service. Miejsca wdrożenia to rzeczywiście aktywne aplikacje z własnymi nazwami hosta. Elementy zawartości i konfiguracji aplikacji można wymieniać między 2 miejscami wdrożenia, w tym także miejscem produkcyjnym. Wdrażanie aplikacji w taki sposób, aby miejsce wdrożenia ma następujące zalety:

* Można sprawdzić poprawność zmian aplikacji w miejscu przejściowym wdrożenia, zanim zamienisz z miejscem produkcyjnym.
* Najpierw wdrażanie aplikacji w miejscu i zamianę go w środowisku produkcyjnym gwarantuje, że wszystkie wystąpienia elementu gniazda są przygotowaniu przed zamianą na środowisko produkcyjne. Pozwala to wyeliminować przestój w przypadku wdrażania aplikacji. Przekierowywanie ruchu jest bezproblemowe i żadne żądania nie są usuwane w wyniku operacji wymiany. Ta całego przepływu pracy można zautomatyzować, konfigurując [automatycznej wymiany](#Auto-Swap) podczas wymiany wstępnego sprawdzania poprawności nie jest potrzebna.
* Po zamiany gniazda z wcześniej przygotowaną aplikacji ma teraz poprzedniej aplikacji produkcyjnej. W przypadku zmiany zamienione w gnieździe produkcyjnym nie zgodnie z oczekiwaniami, można wykonać tego samego obszaru wymiany od razu, aby uzyskać "ostatniej znanej dobrej witryny" ponownie.

Każda warstwa planu usługi App Service obsługuje szereg różnych miejsc wdrożenia. Aby dowiedzieć się, liczba gniazd obsługuje warstwy aplikacji, zobacz [limity usługi App](https://docs.microsoft.com/azure/azure-subscription-service-limits#app-service-limits). Aby przeskalować swoją aplikację do innej warstwy, warstwy docelowej musi obsługiwać liczbę miejsc, w których aplikacja używa już. Na przykład, jeśli aplikacja ma więcej niż 5 gniazda, nie będzie można skalować w dół, aby **standardowa** warstwy, ponieważ **standardowa** warstwy obsługuje tylko 5 miejsc wdrożenia.

<a name="Add"></a>

## <a name="add-a-deployment-slot"></a>Dodaj miejsce wdrożenia
Aplikacja musi być uruchomiona w **standardowa**, **Premium**, lub **izolowany* warstwy w kolejności, aby włączyć wielu miejsc wdrożenia.

1. W [witryny Azure Portal](https://portal.azure.com/), otwórz aplikacji [bloku zasobów](../azure-resource-manager/resource-group-portal.md#manage-resources).
2. Wybierz **miejsc wdrożenia** opcji, a następnie kliknij przycisk **Dodaj miejsce**.
   
    ![Dodaj nowe miejsce wdrożenia][QGAddNewDeploymentSlot]
   
   > [!NOTE]
   > Jeśli aplikacja nie znajduje się już w **standardowa**, **Premium**, lub **izolowany* warstwy, otrzymasz komunikat informujący o obsługiwanych warstw, umożliwiające publikowanie etapowe. W tym momencie masz możliwość dokonania wyboru **uaktualnienia** i przejdź do **skalowania** kartę aplikacji przed kontynuowaniem.
   > 
   > 
3. W **Dodaj miejsce** bloku, nazwij gniazda, a następnie wybierz, czy chcesz sklonować konfiguracji aplikacji z innego istniejącego miejsca wdrożenia. Kliknij znacznik wyboru, aby kontynuować.
   
    ![Źródło konfiguracji][ConfigurationSource1]
   
    Po raz pierwszy, Dodaj miejsce, masz tylko jedną z dwóch opcji: klonowania konfiguracji z gniazda domyślne w środowisku produkcyjnym lub wcale.
    Po utworzeniu kilku miejsc, możliwe będzie Klonuj konfiguracji z gniazda innym niż w środowisku produkcyjnym:
   
    ![Konfiguracja źródła][MultipleConfigurationSources]
4. W bloku zasobów aplikacji, kliknij przycisk **miejsc wdrożenia**, następnie kliknij przycisk miejsce wdrożenia, aby otworzyć blok zasobu tego miejsca, przy użyciu zestawu metryk i konfiguracji, tak jak każdą inną aplikację. Nazwa miejsca jest wyświetlany w górnej części bloku w celu odnotowania, że miejsce wdrożenia.
   
    ![Tytuł miejsca wdrożenia][StagingTitle]
5. Kliknij adres URL aplikacji w bloku tego gniazda. Zwróć uwagę, miejsce wdrożenia ma swoje własne nazwy hosta i jest również działającej aplikacji. Aby ograniczyć dostęp publiczny do miejsca wdrożenia, zobacz [App Service Web Apps — blokuje dostęp w sieci web do miejsc wdrożenia nieprodukcyjnych](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/).

Brak zawartości po utworzeniu miejsca wdrożenia. Można wdrożyć do gniazda z gałęzi innym repozytorium lub całkowicie innego repozytorium. Można również zmienić konfigurację tego gniazda. Użyj poświadczeń wdrożenia lub w profilu publikowania skojarzone z miejsce wdrożenia aktualizacji zawartości.  Można na przykład [publikowania do tego miejsca przy użyciu narzędzia git](app-service-deploy-local-git.md).

<a name="AboutConfiguration"></a>

## <a name="which-settings-are-swapped"></a>Ustawienia, które zostały zamienione?
Podczas klonowania konfiguracji z innego miejsca wdrożenia sklonowanego konfiguracji jest edytowalny. Ponadto niektóre elementy konfiguracji będą postępuj zgodnie z zawartości między swap (nie gniazdo określonych), podczas gdy inne elementy konfiguracji pozostanie w tym samym miejscu po swap (gniazdo określone). Poniższej przedstawiono ustawienia, które zmieniają się podczas zamiany miejsca.

**Ustawienia, które zostały zamienione**:

* Ustawienia ogólne — takich jak gniazda sieci Web 32/64-bitowych wersji framework
* Ustawienia aplikacji (można skonfigurować w celu przypisane do miejsca)
* Parametry połączenia (można skonfigurować w celu przypisane do miejsca)
* Mapowania procedur obsługi
* Ustawienia monitorowania i diagnostyki
* Zawartość usługi WebJobs

**Ustawienia, które nie zostały zamienione**:

* Publikowanie punktów końcowych
* Niestandardowe nazwy domen
* Certyfikaty SSL i powiązań
* Ustawienia skalowania
* Planiści zadań Webjob

Aby skonfigurować aplikację ustawienie lub parametrów połączenia do przypisane do miejsca (nie zamienione), dostęp do **ustawienia aplikacji** bloku dla określonego miejsca, następnie wybierz pozycję **ustawienie miejsca** pole dla konfiguracji elementy, które powinny być przypisane do gniazda. Oznaczanie elementu konfiguracji jako miejsce określonych powoduje ustanowienie tego elementu jako nie swappable miejsc wdrożenia skojarzone z aplikacją.

![Definiowanie ustawień gniazda][SlotSettings]

<a name="Swap"></a>

## <a name="swap-deployment-slots"></a>Zamiana miejsc wdrożenia 
Można zamienić miejsc wdrożenia w **Przegląd** lub **miejsc wdrożenia** widoku bloku zasobów aplikacji.

> [!IMPORTANT]
> Przed możesz zamienić aplikację na podstawie miejsce wdrożenia do środowiska produkcyjnego, upewnij się, że wszystkie miejsca niż określone ustawienia są skonfigurowane zgodnie z oczekiwaniami go w celu wymiany.
> 
> 

1. Aby zamienić miejscami wdrożenia, kliknij przycisk **wymiany** przycisk na pasku poleceń, aplikacji lub na pasku poleceń w miejsce wdrożenia.
   
    ![Przycisk wymiany][SwapButtonBar]

2. Upewnij się, czy wymiany źródłowy i docelowy wymiany są ustawione poprawnie. Zazwyczaj obiekt docelowy wymiany jest miejscem produkcyjnym. Kliknij przycisk **OK** można ukończyć operacji. Po zakończeniu operacji, zostały zamieniono miejsca wdrożenia.

    ![Ukończ zamianę](./media/web-sites-staged-publishing/SwapImmediately.png)

    Aby uzyskać **wymiany z podglądem** typ zamiany, zobacz [wymiany z podglądem (obszar wymiany wielofazowych)](#Multi-Phase).  

<a name="Multi-Phase"></a>

## <a name="swap-with-preview-multi-phase-swap"></a>Zamiana z podglądem (wielofazowych wymiany)

Wymiany z podglądem lub wymiany wielofazowych można uproszczenie sprawdzania poprawności konfiguracji specyficznej dla miejsca elementy, takie jak parametry połączenia.
W przypadku obciążeń o kluczowym znaczeniu, którą chcesz zweryfikować, które aplikacja zachowuje się zgodnie z oczekiwaniami po zastosowaniu konfiguracji z miejscem produkcyjnym i należy wykonać takie weryfikacji *przed* aplikacji jest wymieniany w środowisku produkcyjnym. Wymiany z podglądem jest to, czego potrzebujesz.

> [!NOTE]
> Wymiany z podglądem nie jest obsługiwane w aplikacjach sieci web w systemie Linux.

Kiedy używasz **wymiany z podglądem** opcji (zobacz [zamiana miejsc wdrożenia](#Swap)), usługi App Service wykonuje następujące czynności:

- Zapewnia miejsce docelowe niezmieniony, więc nie ma wpływu na istniejące obciążenie na gniazdo (takich jak środowisko produkcyjne).
- Dotyczy elementów konfiguracji z miejsca docelowego w miejscu źródłowym, w tym parametry połączenia specyficzne dla miejsca i ustawień aplikacji.
- Powoduje ponowne uruchomienie procesów roboczych na miejsca źródłowego przy użyciu tych elementów konfiguracji wyżej.
- Po zakończeniu wymiany: Przenosi miejsca źródłowego wstępnej warmed up do miejsca docelowego. Miejsce docelowe jest przenoszony do miejsca źródłowego, jak ręczne wymiany.
- Gdy anulować zamianę: Przywrócenie elementów konfiguracji w miejscu źródłowym w miejscu źródłowym.

Możesz wyświetlić podgląd dokładnie, jak aplikacja będzie zachowywać z konfiguracją miejsca docelowego. Po zakończeniu walidacji możesz ukończyć wymiany stanowi oddzielny krok. Ten krok ma tę zaletę, że w miejscu źródłowym jest już przygotowaniu z żądaną konfiguracją, a klienci nie żadnej przerwy.  

Przykłady dla poleceń cmdlet programu Azure PowerShell, dostępna dla wymiany wielofazowych znajdują się w poleceniach cmdlet programu Azure PowerShell dla sekcji miejsc wdrożenia.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>Konfigurowanie automatycznej wymiany
Automatycznej wymiany usprawnia scenariuszy DevOps, które chcesz ciągłe wdrażanie aplikacji przy użyciu zero zimnego startu i braku przestojów dla klientów końcowych w aplikacji. Gdy miejsce wdrożenia jest skonfigurowana dla automatycznej wymiany w środowisku produkcyjnym, każdym wypchnięciu aktualizacji kodu w tym miejscu, usługa App Service automatycznie wymiany aplikacji w środowisku produkcyjnym po już ma przygotowaniu w miejscu.

> [!IMPORTANT]
> Po włączeniu automatycznej wymiany dla miejsca Upewnij się, że konfiguracja miejsca jest dokładnie konfiguracji przeznaczonych dla miejsca docelowego (zazwyczaj z miejscem produkcyjnym).
> 
> 

> [!NOTE]
> Automatycznej wymiany nie jest obsługiwane w aplikacjach sieci web w systemie Linux.

Konfigurowanie automatycznej wymiany gniazdo jest proste. Wykonaj następujące kroki:

1. W **miejsc wdrożenia**, wybierz miejsce spoza środowiska produkcyjnego, a wybierz **ustawienia aplikacji** w bloku zasobów tego gniazda.  
   
    ![][Autoswap1]
2. Wybierz **na** dla **automatycznej wymiany**, wybierz miejsce wybraną docelową w **gniazda wymiany automatycznej**i kliknij przycisk **Zapisz** na pasku poleceń. Upewnij się, że konfiguracja dla gniazda jest dokładnie konfiguracji przeznaczonych dla miejsca docelowego.
   
    **Powiadomienia** kartę miga zielona **Powodzenie** po zakończeniu operacji.
   
    ![][Autoswap2]
   
   > [!NOTE]
   > Aby przetestować wymiany automatycznej dla aplikacji, można najpierw zaznaczyć miejsca docelowego nieprodukcyjnych w **gniazda wymiany automatycznej** zapoznać się z tej funkcji.  
   > 
   > 
3. Wykonaj powiadomienie wypychane kod do tego miejsca wdrożenia. Automatycznej wymiany się dzieje po pewnym czasie, a aktualizacja jest odzwierciedlona pod adresem URL Twojej docelowym gnieździe.

<a name="Rollback"></a>

## <a name="roll-back-a-production-app-after-swap"></a>Wycofywanie aplikacji produkcyjnej po wymiany
Jeśli błędy są identyfikowane w środowisku produkcyjnym po zakończeniu zamiany, wycofać gniazd ich stany wstępne wymiany przez zamianę tego samego dwa gniazda natychmiast.

<a name="Warm-up"></a>

## <a name="custom-warm-up-before-swap"></a>Niestandardowe rozgrzewania przed wymiany
Niektóre aplikacje mogą wymagać akcje niestandardowe rozgrzewania. `applicationInitialization` Element konfiguracji w pliku web.config pozwala określić niestandardową inicjalizację akcje do wykonania, zanim żądanie zostanie odebrane. Operacja zamiany czeka, aż ten niestandardowy rozgrzewania zakończyć. Poniżej przedstawiono przykładowe fragment pliku web.config.

    <system.webServer>
        <applicationInitialization>
            <add initializationPage="/" hostName="[app hostname]" />
            <add initializationPage="/Home/About" hostname="[app hostname]" />
        </applicationInitialization>
    </system.webServer>

## <a name="monitor-swap-progress"></a>Monitoruj postęp wymiany

Czasami operacji zamiany zajmuje trochę czasu, takie jak kiedy aplikację, która jest wymieniany ma czas rozgrzewania długie. Można uzyskać więcej informacji na temat operacji wymiany w [dziennika aktywności](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) w [witryny Azure portal](https://portal.azure.com).

Na stronie aplikacji portalu, w obszarze nawigacji po lewej stronie, wybierz **dziennika aktywności**.

Operacja zamiany pojawia się w dzienniku kwerendy w postaci `Slotsswap`. Można rozwinąć i wybierz jedną z podrzędnych operacji lub błędy, aby wyświetlić szczegóły.

![Dziennik aktywności, aby zamiany](media/web-sites-staged-publishing/activity-log.png)

<a name="Delete"></a>

## <a name="delete-a-deployment-slot"></a>Usuń miejsce wdrożenia
W bloku dla miejsca wdrożenia, otwórz blok miejsce wdrożenia, kliknij przycisk **Przegląd** (domyślna strona) i kliknij przycisk **Usuń** na pasku poleceń.  

![Usuń miejsce wdrożenia][DeleteStagingSiteButton]

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-azure-powershell"></a>Automatyzacja zadań za pomocą programu Azure PowerShell

Azure PowerShell to moduł udostępniający polecenia cmdlet do zarządzania platformy Azure za pomocą programu Windows PowerShell, łącznie z obsługą zarządzania miejscami wdrożenia w usłudze Azure App Service.

* Aby uzyskać informacje na temat instalowania i konfigurowania programu Azure PowerShell, na temat uwierzytelniania programu Azure PowerShell z subskrypcją platformy Azure, zobacz [jak zainstalować i skonfigurować program Microsoft Azure PowerShell](/powershell/azure/overview).  

- - -
### <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej
```PowerShell
New-AzureRmWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

- - -
### <a name="create-a-deployment-slot"></a>Utwórz miejsce wdrożenia
```PowerShell
New-AzureRmWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

- - -
### <a name="initiate-a-swap-with-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-source-slot"></a>Zainicjuj wymiany z podglądem (obszar wymiany wielofazowych) i Zastosuj konfigurację miejsca docelowego do miejsca źródłowego
```PowerShell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

- - -
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-source-slot-configuration"></a>Anuluj oczekujące zamianę (obszar wymiany z przeglądem) i przywracanie konfiguracji miejsca źródłowego
```PowerShell
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

- - -
### <a name="swap-deployment-slots"></a>Zamiana miejsc wdrożenia
```PowerShell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>Monitorowanie wymiany zdarzeń w dzienniku aktywności
```PowerShell
Get-AzureRmLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

- - -
### <a name="delete-deployment-slot"></a>Usuń miejsce wdrożenia
```
Remove-AzureRmResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

- - -
<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-azure-cli"></a>Automatyzacja zadań za pomocą wiersza polecenia platformy Azure

Aby uzyskać [wiersza polecenia platformy Azure](https://github.com/Azure/azure-cli) poleceń dla miejsc wdrożenia, zobacz [miejsce wdrożenia aplikacji sieci Web az](/cli/azure/webapp/deployment/slot).

## <a name="next-steps"></a>Kolejne kroki
[Azure App Service Web App — blokuje dostęp w sieci web do miejsc wdrożenia nieprodukcyjnych](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)  
[Wprowadzenie do usługi App Service w systemie Linux](../app-service/containers/app-service-linux-intro.md)  
[Bezpłatna wersja próbna platformy Microsoft Azure](https://azure.microsoft.com/pricing/free-trial/)

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

