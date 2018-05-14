---
title: Często zadawane pytania dotyczące wdrożenia dla aplikacji sieci web platformy Azure | Dokumentacja firmy Microsoft
description: Odpowiedzi na często zadawane pytania dotyczące wdrażania dla funkcji aplikacji sieci Web usługi aplikacji Azure.
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
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 54c99cc9dde6a9706c75401dd75b98f8f5de7cec
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
---
# <a name="deployment-faqs-for-web-apps-in-azure"></a>Często zadawane pytania dotyczące wdrażania aplikacji sieci Web na platformie Azure

Ten artykuł zawiera odpowiedzi na często zadawane pytania (FAQ) o problemy z wdrażaniem [funkcja Web Apps usługi Azure App Service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-am-just-getting-started-with-app-service-web-apps-how-do-i-publish-my-code"></a>Jestem I dopiero rozpoczynasz pracę z aplikacjami sieci web usługi aplikacji. Jak opublikować mój kod?

Poniżej przedstawiono niektóre opcje publikowania kodu aplikacji sieci web:

*   Wdrażanie przy użyciu programu Visual Studio. Jeśli masz rozwiązania Visual Studio, kliknij prawym przyciskiem myszy projekt aplikacji sieci web, a następnie wybierz **publikowania**.
*   Wdrażanie przy użyciu klienta FTP. W portalu Azure Pobierz profil publikowania dla aplikacji sieci web, który chcesz wdrożyć swój kod. Następnie przekazać pliki do \site\wwwroot przy użyciu tych samych poświadczeń FTP profilu publikowania.

Aby uzyskać więcej informacji, zobacz [wdrażanie aplikacji w usłudze App Service](app-service-deploy-local-git.md).

## <a name="i-see-an-error-message-when-i-try-to-deploy-from-visual-studio-how-do-i-resolve-this"></a>Podczas próby wdrożenia w programie Visual Studio, zobacz się komunikat o błędzie. Jak rozwiązać ten problem?

Jeśli zostanie wyświetlony następujący komunikat, może być używasz starszej wersji zestawu SDK: "Wystąpił błąd podczas wdrażania zasobu"YourResourceName"w grupie zasobów"YourResourceGroup": MissingRegistrationForLocation: subskrypcja nie jest zarejestrowana dla Typ zasobu "składniki" w lokalizacji środkowe stany USA. Zarejestruj ponownie dla tego dostawcy Aby uzyskać dostęp do tej lokalizacji." 

Aby rozwiązać ten problem, Uaktualnij do [najnowszej wersji zestawu SDK](https://azure.microsoft.com/downloads/). Jeśli ten komunikat zostanie wyświetlony, a masz najnowszej wersji zestawu SDK, należy przesłać żądanie obsługi.

## <a name="how-do-i-deploy-an-aspnet-application-from-visual-studio-to-app-service"></a>Jak wdrożyć aplikację ASP.NET w programie Visual Studio do usługi App Service?
<a id="deployasp"></a>

Samouczek [tworzenie pierwszej aplikacji sieci web platformy ASP.NET na platformie Azure w ciągu pięciu minut](app-service-web-get-started-dotnet.md) pokazuje, jak wdrożyć aplikację sieci web platformy ASP.NET w aplikacji sieci web w usłudze App Service przy użyciu programu Visual Studio 2017 r.

## <a name="what-are-the-different-types-of-deployment-credentials"></a>Co to są różnego rodzaju poświadczenia wdrożenia?

Usługi aplikacji obsługuje dwa typy poświadczeń dla lokalnych wdrożeniem Git i FTP/S. Aby uzyskać więcej informacji na temat konfigurowania poświadczeń wdrożenia, zobacz [Konfigurowanie poświadczeń wdrożenia dla aplikacji usługi](app-service-deployment-credentials.md).

## <a name="what-is-the-file-or-directory-structure-of-my-app-service-web-app"></a>Co to jest plik lub katalog struktury Moja aplikacja sieci web usługi aplikacji?

Aby uzyskać informacje o strukturze plików aplikację usługi aplikacji, zobacz [pliku struktura w systemie Azure](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure).

## <a name="how-do-i-resolve-ftp-error-550---there-is-not-enough-space-on-the-disk-when-i-try-to-ftp-my-files"></a>Jak rozwiązać, "Błąd FTP 550 - tam nie jest za mało miejsca na dysku" podczas próby FTP pliki?

Jeśli ten komunikat zostanie wyświetlony, istnieje prawdopodobieństwo, że uruchamiasz w przydział dysku w planie usługi aplikacji sieci web. Konieczne może być skalowanie w górę do wyższego poziomu usługi na podstawie Twoich potrzeb miejsca na dysku. Aby uzyskać więcej informacji na temat cen planów i limity zasobów, zobacz [cennik usługi aplikacji](https://azure.microsoft.com/pricing/details/app-service/).

## <a name="how-do-i-set-up-continuous-deployment-for-my-app-service-web-app"></a>Jak skonfigurować ciągłe wdrażanie dla mojej aplikacji sieci web usługi aplikacji?

Można skonfigurować ciągłego wdrażania od wielu zasobów, w tym Visual Studio Team Services, usługi OneDrive, GitHub, Bitbucket, Dropbox i innych repozytoriów narzędzia Git. Te opcje są dostępne w portalu. [Ciągłe wdrażanie w usłudze App Service](app-service-continuous-deployment.md) jest przydatne samouczek, który objaśnia, jak można skonfigurować ciągłego wdrażania.

## <a name="how-do-i-troubleshoot-issues-with-continuous-deployment-from-github-and-bitbucket"></a>Jak rozwiązać problemy z ciągłego wdrażania od GitHub i Bitbucket?

Aby uzyskać pomoc do badania problemów dotyczących ciągłego wdrażania od usługi GitHub lub Bitbucket, [badanie ciągłe wdrażanie](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment).

## <a name="i-cant-ftp-to-my-site-and-publish-my-code-how-do-i-resolve-this"></a>Nie mogę FTP do witryny i Publikuj kod. Jak rozwiązać ten problem?

Aby rozwiązać problemy z FTP:

1. Upewnij się, że wprowadzasz poprawną nazwę hosta i poświadczeń. Aby uzyskać szczegółowe informacje o różnych typach poświadczeń i sposobu ich używania, zobacz [poświadczenia wdrażania](https://github.com/projectkudu/kudu/wiki/Deployment-credentials).
2. Upewnij się, że porty FTP nie są blokowane przez zaporę. Porty powinny mieć następujące ustawienia:
    * Port połączenia sterowania FTP: 21
    * Port połączenia danych FTP: 989, 10001 10300

## <a name="how-do-i-publish-my-code-to-app-service"></a>Jak opublikować mój kod w usłudze App Service?

Szybki Start Azure jest przeznaczony do wdrażania aplikacji przy użyciu wdrożenia stosu i użyciu wybranej metody. Aby użyć opcji szybkiego startu w portalu Azure, przejdź do **ustawienia** > **wdrażania aplikacji**.

## <a name="why-does-my-app-sometimes-restart-after-deployment-to-app-service"></a>Dlaczego moja aplikacja czasami ponownie po wdrożenia w usłudze App Service?

Aby dowiedzieć się więcej na temat okoliczności, w których wdrożenia aplikacji może spowodować ponowne uruchomienie, zobacz [wdrożenia, a problemy środowiska uruchomieniowego](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues#deployments-and-web-app-restarts"). Zgodnie z opisem w artykule, usługi aplikacji — wdraża pliki w folderze wwwroot. Nigdy nie bezpośrednio ponownego uruchomienia aplikacji.

## <a name="how-do-i-integrate-visual-studio-team-services-code-with-app-service"></a>Jak zintegrować kodu programu Visual Studio Team Services z usługi aplikacji?

Masz dwie opcje związane z Visual Studio Team Services za pomocą ciągłego wdrażania:

*   Użyj projektu Git. Połączenie za pomocą usługi aplikacji przy użyciu opcji wdrażania dla tego repozytorium.
*   Użyj projektu kontroli wersji typu Team Foundation (TFVC). Wdrażanie przy użyciu agenta kompilacji dla aplikacji usługi.

Kod ciągłego wdrażania dla obu tych opcji zależy od istniejących projektanta przepływów pracy i procedur zaewidencjonowania. Więcej informacji można znaleźć w tych artykułach: 

*   [Implementowanie ciągłego wdrażania aplikacji do witryny sieci Web platformy Azure](https://www.visualstudio.com/docs/release/examples/azure/azure-web-apps-from-build-and-release-hubs)
*   [Konfigurowanie konta usługi Visual Studio Team Services, można wdrożyć w aplikacji sieci web](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)

## <a name="how-do-i-use-ftp-or-ftps-to-deploy-my-app-to-app-service"></a>Jak używać FTP i FTPS można wdrożyć mojej aplikacji z usługi aplikacji?

Aby dowiedzieć się, jak przy użyciu FTP i FTPS, aby wdrożyć aplikację sieci web w usłudze App Service, zobacz [wdrażanie aplikacji w usłudze App Service przy użyciu FTP/S](app-service-deploy-ftp.md).
