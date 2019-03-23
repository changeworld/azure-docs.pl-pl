---
title: Wdrażanie — często zadawane pytania — usługa Azure App Service | Dokumentacja firmy Microsoft
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące wdrażania dla funkcji Web Apps w usłudze Azure App Service.
services: app-service\web
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 9d38e3caf3d835eaf73df90282157eed76c5ea25
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58350644"
---
# <a name="deployment-faqs-for-web-apps-in-azure"></a>Często zadawane pytania dotyczące wdrażania dla aplikacji sieci Web na platformie Azure

W tym artykule znajdują się odpowiedzi na często zadawane pytania (FAQ) dotyczących problemów z wdrażaniem dla [funkcji Web Apps w usłudze Azure App Service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-am-just-getting-started-with-app-service-web-apps-how-do-i-publish-my-code"></a>Czy mogę jestem początkującym użytkownikiem aplikacji sieci web usługi App Service. Jak opublikować mój kod?

Poniżej przedstawiono niektóre opcje publikowania kodu aplikacji sieci web:

*   Wdrażanie przy użyciu programu Visual Studio. Jeśli masz rozwiązanie programu Visual Studio, kliknij prawym przyciskiem myszy projekt aplikacji sieci web, a następnie wybierz **Publikuj**.
*   Wdrażanie przy użyciu klienta FTP. W witrynie Azure portal Pobierz profil publikowania dla aplikacji sieci web, który chcesz wdrożyć swój kod. Następnie przekaż pliki \site\wwwroot przy użyciu tych samych poświadczeń publikowania w profilu FTP.

Aby uzyskać więcej informacji, zobacz [wdrażanie aplikacji w usłudze App Service](deploy-local-git.md).

## <a name="i-see-an-error-message-when-i-try-to-deploy-from-visual-studio-how-do-i-resolve-this"></a>Widzę komunikat o błędzie podczas próby wdrożenia w programie Visual Studio. Jak rozwiązać ten problem?

Jeśli zobaczysz następujący komunikat, być może używasz starszej wersji zestawu SDK: "Wystąpił błąd podczas wdrażania zasobu"YourResourceName"w grupie zasobów"YourResourceGroup": MissingRegistrationForLocation: Subskrypcja nie jest zarejestrowana dla typu zasobu "składniki" w lokalizacji "Środkowe stany USA". Zarejestruj ponownie dla tego dostawcy. Aby uzyskać dostęp do tej lokalizacji." 

Aby rozwiązać ten problem, przeprowadź uaktualnienie do [najnowszy zestaw SDK](https://azure.microsoft.com/downloads/). Jeśli widzisz ten komunikat i masz najnowszy zestaw SDK, Prześlij żądanie pomocy technicznej.

## <a name="how-do-i-deploy-an-aspnet-application-from-visual-studio-to-app-service"></a>Jak wdrożyć aplikację ASP.NET w programie Visual Studio w usłudze App Service?
<a id="deployasp"></a>

Samouczek [tworzenie pierwszej aplikacji sieci web platformy ASP.NET na platformie Azure w ciągu pięciu minut](app-service-web-get-started-dotnet.md) dowiesz się, jak wdrożyć aplikację sieci web platformy ASP.NET w aplikacji sieci web w usłudze App Service przy użyciu programu Visual Studio 2017.

## <a name="what-are-the-different-types-of-deployment-credentials"></a>Jakie są różne rodzaje poświadczeń wdrożenia?

Usługa App Service obsługuje dwa rodzaje poświadczenia lokalne wdrożenie narzędzia Git i wdrażania protokołu FTP/S. Aby uzyskać więcej informacji o sposobie konfigurowania poświadczeń wdrożenia, zobacz [skonfiguruj poświadczenia wdrożenia dla usługi App Service](deploy-configure-credentials.md).

## <a name="what-is-the-file-or-directory-structure-of-my-app-service-web-app"></a>Co to jest plik lub katalog struktury aplikacji sieci web usługi App Service?

Aby uzyskać informacji na temat struktury plików aplikacji usługi app Service, zobacz [pliku struktury na platformie Azure](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure).

## <a name="how-do-i-resolve-ftp-error-550---there-is-not-enough-space-on-the-disk-when-i-try-to-ftp-my-files"></a>Jak rozwiązać, "Błąd FTP 550 - miejsca jest wystarczająca ilość miejsca na dysku" podczas próby FTP pliki?

Jeśli widzisz ten komunikat, istnieje prawdopodobieństwo, że zostały przekroczone przydział dysku w ramach planu usługi dla aplikacji sieci web. Może być konieczne skalowanie w górę do wyższego poziomu usługi zgodnie z potrzebami miejsca na dysku. Aby uzyskać więcej informacji na temat cen, plany i limity zasobów zobacz [cennik usługi App Service](https://azure.microsoft.com/pricing/details/app-service/).

## <a name="how-do-i-set-up-continuous-deployment-for-my-app-service-web-app"></a>Jak skonfigurować ciągłe wdrażanie dla mojej aplikacji sieci web usługi App Service?

Można skonfigurować ciągłe wdrażanie z kilka zasobów, w tym DevOps platformy Azure, usługi OneDrive, GitHub, Bitbucket, Dropbox i innych repozytoriów Git. Te opcje są dostępne w portalu. [Ciągłe wdrażanie w usłudze App Service](deploy-continuous-deployment.md) jest przydatne samouczek, który objaśnia, jak skonfigurować ciągłe wdrażanie.

## <a name="how-do-i-troubleshoot-issues-with-continuous-deployment-from-github-and-bitbucket"></a>Jak rozwiązywać problemy z ciągłym wdrażaniem z usługi GitHub i Bitbucket?

Aby uzyskać pomoc, badanie problemów z ciągłym wdrażaniem z usługi GitHub lub Bitbucket, zobacz [badanie ciągłe wdrażanie](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment).

## <a name="i-cant-ftp-to-my-site-and-publish-my-code-how-do-i-resolve-this"></a>Nie mogę FTP do mojej lokacji i publikowanie mój kod. Jak rozwiązać ten problem?

Aby rozwiązać problemy z FTP:

1. Sprawdź, czy wprowadzasz poprawną nazwę hosta i poświadczenia. Aby uzyskać szczegółowe informacje o różnych typach poświadczeń i sposobu ich używania, zobacz [poświadczenia wdrożenia](https://github.com/projectkudu/kudu/wiki/Deployment-credentials).
2. Upewnij się, że porty FTP nie są blokowane przez zaporę. Porty powinny mieć następujące ustawienia:
    * Port połączenia sterowania FTP: 21
    * Port połączenia danych FTP: 989, 10001-10300

## <a name="how-do-i-publish-my-code-to-app-service"></a>Jak opublikować mój kod w usłudze App Service?

Szybkiego startu platformy Azure jest przeznaczony do wdrażania aplikacji przy użyciu stos wdrożenia i wybranej metody. Aby użyć tego przewodnika Szybki Start w witrynie Azure portal, przejdź do usługi app service w obszarze **wdrożenia**, wybierz opcję **Szybki Start**.

## <a name="why-does-my-app-sometimes-restart-after-deployment-to-app-service"></a>Dlaczego moja aplikacja czasem ponownego uruchomienia po wdrożeniu do usługi App Service?

Aby dowiedzieć się więcej na temat okoliczności, w których wdrożenie aplikacji może spowodować ponowne uruchomienie komputera, zobacz [wdrożenia, a problemy w czasie wykonywania](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues#deployments-and-web-app-restarts"). Zgodnie z opisem w artykule usługi App Service wdraża pliki w folderze wwwroot. Nigdy nie bezpośrednio ponownego uruchomienia aplikacji.

## <a name="how-do-i-integrate-azure-devops-code-with-app-service"></a>Jak zintegrować kodu DevOps platformy Azure z usługą App Service?

Masz dwie opcje używania ciągłego wdrażania za pomocą DevOps platformy Azure:

*   Należy użyć projektu usługi Git. Połączenie za pomocą usługi App Service przy użyciu Centrum wdrażania.
*   Należy użyć projektu Team Foundation Version Control (TFVC). Wdrażanie za pomocą agenta kompilacji dla usługi App Service.

Wdrażanie ciągłe kodu dla obu tych opcji zależy od tego, istniejących przepływów pracy dla deweloperów i procedur ewidencjonowania. Więcej informacji można znaleźć w tych artykułach: 

*   [Wdrożenie ciągłego wdrażania aplikacji na witrynę sieci Web platformy Azure](https://www.visualstudio.com/docs/release/examples/azure/azure-web-apps-from-build-and-release-hubs)
*   [Konfigurowanie organizacji DevOps platformy Azure, dzięki czemu można wdrożyć w aplikacji sieci web](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)

## <a name="how-do-i-use-ftp-or-ftps-to-deploy-my-app-to-app-service"></a>Jak używać protokołu FTP i FTPS Aby wdrożyć aplikację w usłudze App Service?

Aby uzyskać informacje dotyczące wdrażania aplikacji sieci web w usłudze App Service za pomocą protokołu FTP i FTPS, zobacz [wdrażanie aplikacji w usłudze App Service przy użyciu protokołu FTP/S](deploy-ftp.md).
