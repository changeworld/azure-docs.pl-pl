---
title: Wdrażanie aplikacji w usłudze Azure App Service przy użyciu FTP/S | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wdrożyć aplikację w usłudze Azure App Service przy użyciu FTP i FTPS.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/06/2016
ms.author: cephalin;dariac
ms.openlocfilehash: 561f317cd7afd740b83709efc8a75ed515626192
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>Wdrażanie aplikacji w usłudze Azure App Service przy użyciu FTP/S

W tym artykule przedstawiono sposób użycia FTP i FTPS, aby wdrożyć aplikację sieci web, zaplecza aplikacji mobilnej lub aplikacji interfejsu API [usłudze Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714).

Punkt końcowy FTP/S dla aplikacji jest już aktywna. Konfiguracja nie jest niezbędne do obsługi wdrożenia FTP/S.

<a name="step1"></a>
## <a name="step-1-set-deployment-credentials"></a>Krok 1: Konfigurowanie poświadczeń wdrożenia

Aby uzyskać dostęp do serwera FTP dla aplikacji, najpierw poświadczenia wdrożenia. 

Aby skonfigurować lub zresetować poświadczenia wdrażania, zobacz [poświadczenia wdrożenia usługi aplikacji Azure](app-service-deployment-credentials.md). W tym samouczku przedstawiono na używanie poświadczeń na poziomie użytkownika.

## <a name="step-2-get-ftp-connection-information"></a>Krok 2: Pobieranie informacji o połączeniu FTP

1. W [portalu Azure](https://portal.azure.com), otwórz aplikacji [zasobu strony](../azure-resource-manager/resource-group-portal.md#manage-resources).
2. Wybierz **omówienie** na lewym pasku nawigacyjnym, a następnie zanotuj wartości **użytkownika serwera FTP/wdrożenia**, **nazwa hosta FTP**, i **nazwy hosta FTPS**. 

    ![Informacje o połączeniu FTP](./media/app-service-deploy-ftp/FTP-Connection-Info.PNG)

    > [!NOTE]
    > Do zapewnienia prawidłowego kontekstu serwera FTP **użytkownika serwera FTP/wdrożenia** wartość wyświetlana w portalu Azure zawiera nazwę aplikacji.
    > Te same informacje można znaleźć po wybraniu **właściwości** na lewym pasku nawigacyjnym. 
    >
    > Ponadto hasła wdrożenia nigdy nie jest wyświetlana. Jeśli użytkownik zapomni hasła wdrożenia, wróć do [krok 1](#step1) i zresetuj hasło wdrożenia.
    >
    >

## <a name="step-3-deploy-files-to-azure"></a>Krok 3: Wdrażanie plików na platformie Azure

1. Z tego klienta FTP (na przykład [programu Visual Studio](https://www.visualstudio.com/vs/community/) lub [FileZilla](https://filezilla-project.org/download.php?type=client)), Użyj zebranych do nawiązania połączenia aplikacji informacje o połączeniu.
3. Kopiowanie plików i ich struktury katalogów odpowiednich do [ **/lokacji/wwwroot** katalogu](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) na platformie Azure (lub **/lokacji/wwwroot/App_Data/zadania/** katalogu dla zadań Webjob).
4. Przejdź do adresu URL aplikacji, aby sprawdzić, czy aplikacja działa prawidłowo. 

> [!NOTE] 
> W odróżnieniu od [wdrożenia oparte na Git](app-service-deploy-local-git.md), wdrożenie FTP nie obsługuje następujących automatyzacji wdrażania: 
>
> - Przywraca zależności (na przykład automatyzacji NuGet, NPM, PIP i kompozytor)
> - Kompilacja plików binarnych .NET
> - Generowanie pliku Web.config (w tym miejscu jest [przykład Node.js](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps))
> 
> Generowanie te niezbędne pliki ręcznie na komputerze lokalnym, a następnie wdrożyć je razem z aplikacji.
>
>

## <a name="enforce-ftps"></a>Wymuszanie FTPS

Aby zwiększyć bezpieczeństwo należy zezwalać FTP przy użyciu protokołu SSL tylko. Jeśli nie używasz serwera FTP wdrożenia można również wyłączyć zarówno FTP i FTPS.

Na stronie zasobów aplikacji w programie [portalu Azure](https://portal.azure.com), wybierz pozycję **ustawień aplikacji** na lewym pasku nawigacyjnym.

Aby wyłączyć niezaszyfrowane FTP, wybierz pozycję **FTPS tylko**. Aby całkowicie wyłączyć zarówno FTP i FTPS, wybierz pozycję **wyłączyć**. Gdy skończysz, kliknij przycisk **zapisać**.

![Wyłącz FTP/S](./media/app-service-deploy-ftp/disable-ftp.png)

## <a name="troubleshoot-ftp-deployment"></a>Rozwiązywanie problemów z wdrażaniem FTP

- [Jak rozwiązywać problemy z serwera FTP wdrożenia?](#how-can-i-troubleshoot-ftp-deployment)
- [I jest niemożliwe FTP i Publikuj kod. Jak można rozwiązać ten problem?](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
- [Jak można podłączyć do usługi FTP w usłudze Azure App Service za pośrednictwem w trybie pasywnym?](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)

### <a name="how-can-i-troubleshoot-ftp-deployment"></a>Jak rozwiązywać problemy z serwera FTP wdrożenia?

Pierwszym krokiem Rozwiązywanie problemów z wdrażaniem FTP jest izolowanie problem wdrażania, problem aplikacji środowiska wykonawczego.

Problem wdrażania zazwyczaj wynikiem nie plików lub nieprawidłowe pliki wdrożonych aplikacji. Może zostać zlikwidowane poprzez badanie wdrożenia FTP lub wybranie ścieżki alternatywnej rozmieszczania (na przykład kontroli źródła).

Problem aplikacji środowiska wykonawczego zazwyczaj wynikiem prawidłowego zestawu plików wdrożonych aplikacji, ale zachowanie aplikacji niepoprawne. Może zostać zlikwidowane poprzez koncentrujących się na zachowanie kodu w czasie wykonywania i badania ścieżki określony błąd.

Aby określić problem wdrożenia lub w czasie wykonywania, zobacz [wdrożenia, a problemy środowiska uruchomieniowego](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues).

 
### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>I jest niemożliwe FTP i Publikuj kod. Jak można rozwiązać ten problem?
Sprawdź, czy wprowadzono poprawną nazwę hosta i [poświadczenia](#step-1--set-deployment-credentials). Należy także sprawdzić, czy następujące porty FTP na tym komputerze nie są blokowane przez zaporę:

- Port połączenia sterowania FTP: 21
- Port połączenia danych FTP: 989, 10001 10300
 
### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>Jak można podłączyć do usługi FTP w usłudze Azure App Service za pośrednictwem w trybie pasywnym?
Usługa aplikacji Azure obsługuje nawiązywaniu połączeń za pośrednictwem trybu zarówno aktywnych i pasywnych. W trybie pasywnym jest preferowana, ponieważ maszynach wdrożenia są zwykle za zaporą (w systemie operacyjnym lub jako część pakietu domu lub sieci firmowej). Zobacz [przykład dokumentacji WinSCP](https://winscp.net/docs/ui_login_connection). 

## <a name="next-steps"></a>Kolejne kroki

W przypadku bardziej zaawansowanych scenariuszy wdrażania, spróbuj [wdrażanie na platformie Azure za pomocą narzędzia Git](app-service-deploy-local-git.md). Wdrożenie oparte na Git na platformie Azure umożliwia kontroli wersji, Przywracanie pakietu MSBuild i więcej.

## <a name="more-resources"></a>Więcej zasobów

* [Poświadczenia wdrożenia usługi aplikacji Azure](app-service-deploy-ftp.md)
