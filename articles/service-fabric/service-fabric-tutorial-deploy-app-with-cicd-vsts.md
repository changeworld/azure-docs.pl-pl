---
title: Wdrażanie aplikacji usługi Azure Service Fabric przy użyciu ciągłej integracji (usługa Team Services) | Microsoft Docs
description: W ramach tego samouczka nauczysz się konfigurować ciągłą integrację i ciągłe wdrażanie aplikacji usługi Service Fabric za pomocą usługi Visual Studio Team Services.  Wdrażanie aplikacji w klastrze usługi Service Fabric na platformie Azure.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/13/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 543b75fbc1e080d13654df06cf36874e3833e851
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37018404"
---
# <a name="tutorial-deploy-an-application-with-cicd-to-a-service-fabric-cluster"></a>Samouczek: wdrażanie aplikacji przy użyciu ciągłej integracji/ciągłego wdrażania w klastrze usługi Service Fabric
Niniejszy samouczek jest czwartą częścią serii i zawiera opis sposobu konfiguracji ciągłej integracji i ciągłego wdrażania aplikacji usługi Service Fabric za pomocą usługi Visual Studio Team Services.  Wymagana jest istniejąca aplikacja usługi Service Fabric. Na potrzeby tego samouczka za przykład posłużyła aplikacja utworzona w temacie [Tworzenie aplikacji .NET](service-fabric-tutorial-create-dotnet-app.md).

Część trzecia serii zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie kontroli źródła do projektu
> * Tworzenie definicji kompilacji w usłudze Team Services
> * Tworzenie definicji wydania w usłudze Team Services
> * Automatyczne wdrażanie i uaktualnianie aplikacji

Ta seria samouczków zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * [Kompilowanie aplikacji .NET Service Fabric](service-fabric-tutorial-create-dotnet-app.md)
> * [Wdrażanie aplikacji w klastrze zdalnym](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Dodawanie punktu końcowego HTTPS do usługi frontonu platformy ASP.NET Core](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * Konfigurowanie procesu ciągłej integracji/ciągłego wdrażania za pomocą usługi Visual Studio Team Services
> * [Konfigurowanie monitorowania i diagnostyki dla aplikacji](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem tego samouczka:
- Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Zainstaluj program Visual Studio 2017](https://www.visualstudio.com/), a następnie zainstaluj obciążenia **Programowanie na platformie Azure** i **Tworzenie aplikacji na platformie ASP.NET i tworzenie aplikacji internetowych**.
- [Zainstaluj zestaw SDK usługi Service Fabric.](service-fabric-get-started.md)
- Utwórz klaster usługi Service Fabric z systemem Windows na platformie Azure, na przykład [postępując zgodnie z tym samouczkiem](service-fabric-tutorial-create-vnet-and-windows-cluster.md).
- Utwórz [konto usługi Team Services](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services).

## <a name="download-the-voting-sample-application"></a>Pobieranie przykładowej aplikacji do głosowania
Jeśli nie skompilowano przykładowej aplikacji do głosowania w [pierwszej części tej serii samouczków](service-fabric-tutorial-create-dotnet-app.md), można ją pobrać. W oknie polecenia uruchom następujące polecenie, aby sklonować przykładowe repozytorium aplikacji na komputer lokalny.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="prepare-a-publish-profile"></a>Przygotowywanie profilu publikowania
Po [utworzeniu aplikacji](service-fabric-tutorial-create-dotnet-app.md) i [wdrożeniu jej na platformie Azure](service-fabric-tutorial-deploy-app-to-party-cluster.md) wszystko jest gotowe do skonfigurowania ciągłej integracji.  Najpierw przygotuj profil publikowania w aplikacji na potrzeby procesu wdrażania wykonywanego w ramach usługi Team Services.  Profil publikowania należy skonfigurować pod kątem klastra, który został wcześniej utworzony.  Uruchom program Visual Studio i otwórz istniejący projekt aplikacji usługi Service Fabric.  W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy aplikację i wybierz polecenie **Opublikuj...**.

Wybierz profil docelowy w ramach projektu aplikacji na potrzeby przepływu pracy ciągłej integracji, na przykład chmurę.  Określ punkt końcowy połączenia klastra.  Zaznacz pole wyboru **Uaktualnij aplikację**, aby uaktualnić aplikację dla każdego wdrożenia w usłudze Team Services.  Kliknij hiperlink **Zapisz**, aby zapisać ustawienia w profilu publikowania, a następnie kliknij przycisk **Anuluj** w celu zamknięcia okna dialogowego.  

![Profil wypychania][publish-app-profile]

## <a name="share-your-visual-studio-solution-to-a-new-team-services-git-repo"></a>Udostępnianie rozwiązania programu Visual Studio w nowym repozytorium Git w usłudze Team Services
Udostępnij pliki źródłowe aplikacji w projekcie zespołowym usługi Team Services, aby umożliwić generowanie kompilacji.  

Utwórz nowe lokalne repozytorium Git dla projektu, wybierając pozycję **Dodaj do kontroli źródła** -> **Git** na pasku stanu w prawym dolnym rogu programu Visual Studio. 

W widoku **Wypychanie** w programie **Team Explorer** wybierz przycisk **Opublikuj repozytorium Git** w obszarze **Wypychanie do usługi Visual Studio Team Services**.

![Wypychanie repozytorium Git][push-git-repo]

Sprawdź swój adres e-mail i z listy rozwijanej **Domena usługi Team Services** wybierz swoje konto. Wprowadź nazwę repozytorium i wybierz polecenie **Publikuj repozytorium**.

![Wypychanie repozytorium Git][publish-code]

Opublikowanie repozytorium powoduje utworzenie nowego projektu zespołowego na Twoim koncie o takiej samej nazwie co lokalne repozytorium. Aby utworzyć repozytorium w istniejącym projekcie zespołowym, kliknij polecenie **Zaawansowane** obok nazwy **Repozytorium**, a następnie wybierz projekt zespołowy. Aby wyświetlić kod w Internecie, wybierz polecenie **Wyświetl w Internecie**.

## <a name="configure-continuous-delivery-with-vsts"></a>Konfigurowanie ciągłego dostarczania za pomocą usługi VSTS
Definicja kompilacji usługi Team Services zawiera opis przepływu pracy, który składa się z zestawu kroków kompilacji wykonywanych sekwencyjnie. Utwórz definicję kompilacji, która spowoduje wygenerowanie pakietu aplikacji usługi Service Fabric i innych artefaktów, na potrzeby wdrożenia w klastrze usługi Service Fabric. Dowiedz się więcej na temat [definicji kompilacji usługi Team Services](https://www.visualstudio.com/docs/build/define/create). 

Definicja wydania usługi Team Services opisuje przepływ pracy, który wdraża pakiet aplikacji w klastrze. Jednoczesne użycie definicji kompilacji i definicji wydania powoduje wykonanie całego przepływu pracy, zaczynając od plików źródłowych, a kończąc na aplikacji uruchomionej w klastrze. Dowiedz się więcej na temat[definicji wydania](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition) usługi Team Services.

### <a name="create-a-build-definition"></a>Tworzenie definicji kompilacji
Otwórz przeglądarkę internetową i przejdź do nowego projektu zespołowego na stronie: [https://&lt;myaccount&gt;.visualstudio.com/Voting/Voting%20Team/_git/Voting](https://myaccount.visualstudio.com/Voting/Voting%20Team/_git/Voting). 

Wybierz kartę **Kompilowanie i wydawanie**, potem pozycję **Kompilacje**, a następnie pozycję **+ Nowa definicja**.  W obszarze **Wybieranie szablonu** wybierz szablon **Aplikacja usługi Azure Service Fabric** i kliknij przycisk **Zastosuj**. 

![Wybieranie szablonu kompilacji][select-build-template] 

W obszarze **Zadania** wprowadź wyrażenie „Hostowany program VS2017” jako **Kolejkę agentów**. 

![Wybieranie zadań][save-and-queue]

W obszarze **Wyzwalacze** włącz ciągłą integrację, ustawiając **Stan wyzwalacza**.  Wybierz pozycję **Zapisz i dodaj do kolejki**, aby ręcznie uruchomić kompilację.  

![Wybieranie wyzwalaczy][save-and-queue2]

Kompilacje można również wyzwalać w ramach procesu wypychania lub ewidencjonowania. Aby sprawdzić postęp kompilacji, przejdź do karty **Kompilacje**.  Po upewnieniu się, że kompilacja jest wykonywana prawidłowo, określ definicję wydania, która wdraża aplikację w klastrze. 

### <a name="create-a-release-definition"></a>Tworzenie definicji wydania  

Wybierz kartę **Kompilowanie i wydawanie**, potem pozycję **Wydania**, a następnie pozycję **+ Nowa definicja**.  W obszarze **Wybieranie szablonu** wybierz z listy szablon **Wdrożenie usługi Azure Service Fabric**, a następnie przycisk **Zastosuj**.  

![Wybieranie szablonu wydania][select-release-template]

Wybierz pozycję **Zadania**->**Środowisko 1**, a następnie pozycję **+Nowe**, aby dodać nowe połączenie klastra.

![Dodawanie połączenia klastra][add-cluster-connection]

W widoku **Dodawanie nowego połączenia z usługą Service Fabric** wybierz uwierzytelnianie **Na podstawie certyfikatu** lub **Azure Active Directory**.  Określ nazwę połączenia dla klastra „mysftestcluster” i następujący punkt końcowy klastra „tcp://mysftestcluster.southcentralus.cloudapp.azure.com:19000” (lub punkt końcowy klastra, w którym przeprowadzane jest wdrożenie). 

W przypadku uwierzytelniania opartego na certyfikatach dodaj **Odcisk palca certyfikatu serwera** dla certyfikatu serwera, którego użyto do utworzenia klastra.  W obszarze **Certyfikat klienta** dodaj plik certyfikatu klienta zakodowany przy użyciu kodowania base-64. Zobacz pomoc podręczną dla tego pola, aby uzyskać informacje dotyczące sposobu uzyskania reprezentacji certyfikatu w kodowaniu base-64. Dodaj również **Hasło** certyfikatu.  Jeśli nie masz osobnego certyfikatu klienta, możesz użyć certyfikatu klastra lub serwera. 

W przypadku poświadczeń usługi Azure Active Directory dodaj **Odcisk palca certyfikatu serwera** dla certyfikatu serwera użytego do utworzenia klastra oraz poświadczenia w polach **Nazwa użytkownika** i **Hasło**, których chcesz użyć w celu nawiązania połączenia z klastrem. 

Kliknij przycisk **Dodaj**, aby zapisać połączenie klastra.

Następnie dodaj artefakt kompilacji do potoku, aby umożliwić definicji wydania odnalezienie danych wyjściowych kompilacji. Wybierz pozycję **Potok**, a następnie **Artefakty**->**+ Dodaj**.  W obszarze **Źródło (definicja kompilacji)** wybierz wcześniej utworzoną definicję kompilacji.  Kliknij przycisk **Dodaj**, aby zapisać artefakt kompilacji.

![Dodawanie artefaktu][add-artifact]

Włącz wyzwalacz ciągłego wdrażania, aby umożliwić automatyczne tworzenie wydania po zakończeniu kompilacji. Kliknij ikonę pioruna w artefakcie, włącz wyzwalacz, a następnie kliknij przycisk **Zapisz** w celu zapisania definicji wydania.

![Włączanie wyzwalacza][enable-trigger]

Wybierz pozycję **+Wydanie** -> **Tworzenie wydania** -> **Utwórz**, aby ręcznie utworzyć wydanie.  Sprawdź, czy wdrożenie zakończyło się pomyślnie, a aplikacja została uruchomiona w klastrze.  Otwórz przeglądarkę i przejdź pod adres [http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/](http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/).  Zwróć uwagę na wersję aplikacji. W tym przykładzie jest to „1.0.0.20170616.3”. 

## <a name="commit-and-push-changes-trigger-a-release"></a>Zatwierdzanie i wypychanie zmian, wyzwalanie wydania
Aby upewnić się, że potok ciągłej integracji działa prawidłowo, zaewidencjonuj jakieś zmiany kodu w usłudze Team Services.    

Podczas pisania kodu zmiany są automatycznie śledzone przez program Visual Studio. Zatwierdź zmiany w lokalnym repozytorium Git, wybierając ikonę oczekujących zmian (![Oczekujące][pending]) na pasku stanu w prawym dolnym rogu.

W widoku **Zmiany** programu Team Explorer dodaj komunikat opisujący aktualizację i zatwierdź wprowadzone zmiany.

![Zatwierdź wszystko][changes]

Wybierz ikonę paska stanu nieopublikowanych zmian (![Nieopublikowane zmiany][unpublished-changes]) lub widok synchronizacji w programie Team Explorer. Wybierz pozycję **Wypchnij**, aby zaktualizować kod w usłudze Team Services lub na serwerze TFS.

![Wypychanie zmian][push]

Wypychanie zmian do usługi Team Services automatycznie wyzwala kompilację.  Po pomyślnym zakończeniu definicji kompilacji automatycznie tworzone jest wydanie, które rozpoczyna uaktualnianie aplikacji w klastrze.

Aby sprawdzić postęp kompilacji, przejdź do karty **Kompilacje** modułu **Team Explorer** w programie Visual Studio.  Po upewnieniu się, że kompilacja jest wykonywana prawidłowo, określ definicję wydania, która wdraża aplikację w klastrze.

Sprawdź, czy wdrożenie zakończyło się pomyślnie, a aplikacja została uruchomiona w klastrze.  Otwórz przeglądarkę i przejdź pod adres [http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/](http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/).  Zwróć uwagę na wersję aplikacji. W tym przykładzie jest to „1.0.0.20170815.3”.

![Service Fabric Explorer][sfx1]

## <a name="update-the-application"></a>Aktualizowanie aplikacji
Wprowadź zmiany w kodzie aplikacji.  Zapisz i zatwierdź zmiany, postępując zgodnie z poprzednimi krokami.

Po rozpoczęciu uaktualnienia aplikacji możesz obserwować jego postęp w narzędziu Service Fabric Explorer:

![Service Fabric Explorer][sfx2]

Uaktualnianie aplikacji może potrwać kilka minut. Po zakończeniu uaktualniania aplikacja zostanie uruchomiona w następnej wersji.  W tym przykładzie „1.0.0.20170815.4”.

![Service Fabric Explorer][sfx3]

## <a name="next-steps"></a>Następne kroki
W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie kontroli źródła do projektu
> * Tworzenie definicji kompilacji
> * Tworzenie definicji wydania
> * Automatyczne wdrażanie i uaktualnianie aplikacji

Przejdź do następnego samouczka:
> [!div class="nextstepaction"]
> [Konfigurowanie monitorowania i diagnostyki dla aplikacji](service-fabric-tutorial-monitoring-aspnet.md) 


<!-- Image References -->
[publish-app-profile]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishAppProfile.png
[push-git-repo]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishGitRepo.png
[publish-code]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishCode.png
[select-build-template]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectBuildTemplate.png
[save-and-queue]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SaveAndQueue.png
[save-and-queue2]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SaveAndQueue2.png
[select-release-template]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectReleaseTemplate.png
[set-continuous-integration]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SetContinuousIntegration.png
[add-cluster-connection]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddClusterConnection.png
[add-artifact]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddArtifact.png
[enable-trigger]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/EnableTrigger.png
[sfx1]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX1.png
[sfx2]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX2.png
[sfx3]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX3.png
[pending]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Pending.png
[changes]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Changes.png
[unpublished-changes]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/UnpublishedChanges.png
[push]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Push.png
[continuous-delivery-with-VSTS]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/VSTS-Dialog.png
[new-service-endpoint]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpoint.png
[new-service-endpoint-dialog]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpointDialog.png
