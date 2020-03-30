---
title: Często zadawane pytania dotyczące wdrażania — usługa Azure App Service | Dokumenty firmy Microsoft
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące wdrażania funkcji aplikacji sieci Web usługi Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 25f43051f11d42c4f8565039d3168f2ae162eb37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74671687"
---
# <a name="deployment-faqs-for-web-apps-in-azure"></a>Często zadawane pytania dotyczące aplikacji internetowych na platformie Azure

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące problemów z wdrażaniem [funkcji aplikacji sieci Web usługi Azure App Service.](https://azure.microsoft.com/services/app-service/web/)

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-am-just-getting-started-with-app-service-web-apps-how-do-i-publish-my-code"></a>Dopiero zaczynam pracę z aplikacjami internetowymi usługi App Service. Jak opublikować kod?

Oto kilka opcji publikowania kodu aplikacji sieci Web:

*   Wdrażanie przy użyciu programu Visual Studio. Jeśli masz rozwiązanie programu Visual Studio, kliknij prawym przyciskiem myszy projekt aplikacji sieci web, a następnie wybierz polecenie **Publikuj**.
*   Wdrażanie przy użyciu klienta FTP. W witrynie Azure portal pobierz profil publikowania dla aplikacji sieci web, do której chcesz wdrożyć kod. Następnie przekaż pliki do \site\wwwroot przy użyciu tych samych poświadczeń ftp profilu publikowania.

Aby uzyskać więcej informacji, zobacz [Wdrażanie aplikacji w usłudze App Service](deploy-local-git.md).

## <a name="i-see-an-error-message-when-i-try-to-deploy-from-visual-studio-how-do-i-resolve-this-error"></a>Podczas próby wdrożenia z programu Visual Studio jest wyświetlany komunikat o błędzie. Jak rozwiązać ten błąd?

Jeśli zostanie wyświetlony następujący komunikat, być może używasz starszej wersji SDK: "Błąd podczas wdrażania zasobu "YourResourceName" w grupie zasobów "YourResourceGroup": MissingRegistrationForLocation: Subskrypcja nie jest zarejestrowana dla "składniki" w lokalizacji "Central US". Ponownie zarejestruj się dla tego dostawcy, aby mieć dostęp do tej lokalizacji." 

Aby rozwiązać ten błąd, uaktualnij do [najnowszego sdk](https://azure.microsoft.com/downloads/). Jeśli widzisz ten komunikat i masz najnowszy sdk, prześlij prośbę o pomoc techniczną.

## <a name="how-do-i-deploy-an-aspnet-application-from-visual-studio-to-app-service"></a>Jak wdrożyć aplikację ASP.NET z programu Visual Studio do usługi App Service?
<a id="deployasp"></a>

Samouczek [Tworzenie pierwszej ASP.NET aplikacji sieci web na platformie Azure w ciągu pięciu minut](app-service-web-get-started-dotnet.md) pokazuje, jak wdrożyć ASP.NET aplikacji sieci web do aplikacji sieci web w usłudze App Service przy użyciu programu Visual Studio.

## <a name="what-are-the-different-types-of-deployment-credentials"></a>Jakie są różne typy poświadczeń wdrożenia?

Usługa App Service obsługuje dwa typy poświadczeń dla lokalnego wdrożenia git i wdrożenia FTP/S. Aby uzyskać więcej informacji na temat konfigurowania poświadczeń wdrożenia, zobacz [Konfigurowanie poświadczeń wdrażania usługi App Service](deploy-configure-credentials.md).

## <a name="what-is-the-file-or-directory-structure-of-my-app-service-web-app"></a>Jaka jest struktura plików lub katalogów mojej aplikacji sieci Web usługi App Service?

Aby uzyskać informacje na temat struktury plików aplikacji usługi App Service, zobacz [Struktura plików na platformie Azure](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure).

## <a name="how-do-i-resolve-ftp-error-550---there-is-not-enough-space-on-the-disk-when-i-try-to-ftp-my-files"></a>Jak rozwiązać problem "Ftp Error 550 - There is not enough space on the disk" podczas próby FTP moje pliki?

Jeśli widzisz ten komunikat, jest prawdopodobne, że jesteś uruchomiony do przydziału dysku w planie usługi dla aplikacji sieci web. Może być konieczne skalowanie w górę do wyższej warstwy usług w zależności od zapotrzebowania na miejsce na dysku. Aby uzyskać więcej informacji na temat planów cenowych i limitów zasobów, zobacz [Ceny usługi App Service](https://azure.microsoft.com/pricing/details/app-service/).

## <a name="how-do-i-set-up-continuous-deployment-for-my-app-service-web-app"></a>Jak skonfigurować ciągłe wdrażanie aplikacji sieci web usługi App Service?

Możesz skonfigurować ciągłe wdrażanie z kilku zasobów, w tym Azure DevOps, OneDrive, GitHub, Bitbucket, Dropbox i innych repozytoriów Git. Te opcje są dostępne w portalu. [Ciągłe wdrażanie usługi App Service](deploy-continuous-deployment.md) to pomocny samouczek, który wyjaśnia, jak skonfigurować ciągłe wdrażanie.

## <a name="how-do-i-troubleshoot-issues-with-continuous-deployment-from-github-and-bitbucket"></a>Jak rozwiązać problemy z ciągłym wdrażaniem z GitHub i Bitbucket?

Aby uzyskać pomoc w badaniu problemów z ciągłym wdrażaniem z GitHub lub Bitbucket, zobacz [Badanie ciągłego wdrażania.](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)

## <a name="i-cant-ftp-to-my-site-and-publish-my-code-how-do-i-resolve-this-issue"></a>Nie mogę FTP na mojej stronie i opublikować mój kod. Jak rozwiązać ten problem?

Aby rozwiązać problemy FTP:

1. Sprawdź, czy wprowadzasz poprawną nazwę hosta i poświadczenia. Aby uzyskać szczegółowe informacje na temat różnych typów poświadczeń i sposobu ich używania, zobacz [Poświadczenia wdrażania](https://github.com/projectkudu/kudu/wiki/Deployment-credentials).
2. Sprawdź, czy porty FTP nie są blokowane przez zaporę. Porty powinny mieć następujące ustawienia:
    * Port połączenia sterowania FTP: 21
    * Port połączenia danych FTP: 989, 10001-10300

## <a name="how-do-i-publish-my-code-to-app-service"></a>Jak opublikować kod w usłudze App Service?

Szybki start platformy Azure został zaprojektowany, aby ułatwić wdrażanie aplikacji przy użyciu stosu wdrażania i wybranej metody. Aby skorzystać z przewodnika Szybki start, w witrynie Azure portal przejdź do usługi aplikacji w obszarze **Wdrażanie**wybierz pozycję **Szybki start**.

## <a name="why-does-my-app-sometimes-restart-after-deployment-to-app-service"></a>Dlaczego moja aplikacja czasami uruchamia się ponownie po wdrożeniu w usłudze App Service?

Aby dowiedzieć się więcej o okolicznościach, w których wdrożenie aplikacji może spowodować ponowne uruchomienie, zobacz [Problemy z wdrażaniem a czasem wykonywania](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues#deployments-and-web-app-restarts"). Jak opisano w artykule, usługa App Service wdraża pliki w folderze wwwroot. Nigdy nie uruchamia bezpośrednio ponownie aplikacji.

## <a name="how-do-i-integrate-azure-devops-code-with-app-service"></a>Jak zintegrować kod usługi Azure DevOps z usługą App Service?

Dostępne są dwie opcje ciągłego wdrażania za pomocą usługi Azure DevOps:

*   Użyj projektu Git. Połącz się za pośrednictwem usługi App Service przy użyciu Centrum wdrażania.
*   Użyj projektu kontroli wersji team foundation (TFVC). Wdrażanie przy użyciu agenta kompilacji dla usługi App Service.

Ciągłe wdrażanie kodu dla obu tych opcji zależy od istniejących przepływów pracy dewelopera i procedur zaewidencjonowania. Więcej informacji można znaleźć w tych artykułach: 

*   [Wdrażanie ciągłego wdrażania aplikacji w witrynie sieci Web platformy Azure](https://www.visualstudio.com/docs/release/examples/azure/azure-web-apps-from-build-and-release-hubs)
*   [Konfigurowanie organizacji DevOps platformy Azure, aby mogła wdrażać w aplikacji sieci web](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)

## <a name="how-do-i-use-ftp-or-ftps-to-deploy-my-app-to-app-service"></a>Jak wdrożyć aplikację w usłudze App Service za pomocą protokołu FTP lub FTPS?

Aby uzyskać informacje dotyczące wdrażania aplikacji sieci Web w usłudze App Service za pomocą protokołu FTP lub FTPS, zobacz [Wdrażanie aplikacji w usłudze App Service przy użyciu protokołu FTP/S](deploy-ftp.md).
