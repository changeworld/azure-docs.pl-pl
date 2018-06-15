---
title: Konfigurowanie źródła wdrożenia dla usługi aplikacji Azure stosu | Dokumentacja firmy Microsoft
description: Jak Administrator usługi można skonfigurować wdrożenie źródła (Git, GitHub, BitBucket, DropBox i OneDrive) dla usługi aplikacji Azure stosu
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: brenduns
ms.reviewer: anwestg
ms.openlocfilehash: 4ee333fcc50937679c4bc25b83c2d6aa389ba194
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34359598"
---
# <a name="configure-deployment-sources"></a>Konfigurowanie źródeł wdrożenia
*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*


Usługa aplikacji Azure stosu obsługuje wdrożenia na żądanie z wielu dostawców kontroli źródła. Ta funkcja umożliwia deweloperom aplikacji, wdrażanie bezpośrednio z ich repozytoria kontroli źródła. Jeśli użytkownik chce skonfigurować usługę aplikacji do nawiązania połączenia ich repozytoriów, operatorowi chmury najpierw należy skonfigurować integrację usługi aplikacji Azure stosu i dostawca kontroli źródła.  

Oprócz lokalnej Git obsługiwane są następujące dostawców kontroli źródła:

* GitHub
* BitBucket
* OneDrive
* DropBox

## <a name="view-deployment-sources-in-app-service-administration"></a>Widok źródła wdrożenia w administracji usługi aplikacji

1. Zaloguj się do portalu administracyjnego platformy Azure stosu (https://adminportal.local.azurestack.external) jako administratora usługi.
2. Przejdź do **dostawców zasobów** i wybierz **administrator dostawcy zasobów usługi aplikacji**.  ![Administrator dostawcy zasobów usługi aplikacji][1]
3. Kliknij przycisk **konfiguracji kontroli źródła**.  W tym miejscu zostanie wyświetlona lista wszystkich źródeł wdrożenia skonfigurowane.
    ![Konfiguracji kontroli źródła administrator dostawcy zasobów usługi aplikacji][2]

## <a name="configure-github"></a>Konfigurowanie usługi GitHub

Musisz mieć konto GitHub, aby zakończyć to zadanie. Możesz użyć konta organizacji, a nie konta osobistego.

1. Zaloguj się w witrynie GitHub, przejdź do https://www.github.com/settings/developers i kliknij przycisk **zarejestrować nową aplikację**.
    ![GitHub - zarejestrować nową aplikację][3]
2. Wprowadź **Nazwa aplikacji** na przykład - usługi aplikacji Azure stosu.
3. Wprowadź **adres URL strony głównej**. Adres URL strony głównej musi być adresem Portal Azure stosu. Na przykład https://portal.local.azurestack.external.
4. Wprowadź **opis aplikacji**.
5. Wprowadź **adresu URL wywołania zwrotnego autoryzacji**.  We wdrożeniu stosu Azure domyślny adres Url jest w formie https://portal.local.azurestack.external/TokenAuthorize, jeśli jest używane do uruchamiania zastępuje inną domenę domenę w poszukiwaniu local.azurestack.external
6. Kliknij przycisk **zarejestrować aplikację**.  Teraz zostanie wyświetlona z listą strony **identyfikator klienta** i **klucz tajny klienta** dla aplikacji.
    ![GitHub — rejestracja ukończona aplikacja][5]
7.  W oknie lub nową kartę przeglądarki zalogować się do portalu administracyjnego platformy Azure stosu (https://adminportal.local.azurestack.external) jako administratora usługi.
8.  Przejdź do **dostawców zasobów** i wybierz **administrator dostawcy zasobów usługi aplikacji**.
9. Kliknij przycisk **konfiguracji kontroli źródła**.
10. Skopiuj i Wklej **identyfikator klienta** i **klucz tajny klienta** w elemencie wejściowym odpowiednie pola GitHub.
11. Kliknij pozycję **Zapisz**.

## <a name="configure-bitbucket"></a>Skonfiguruj BitBucket

Musisz mieć konto BitBucket, aby zakończyć to zadanie. Możesz użyć konta organizacji, a nie konta osobistego.

1. Zaloguj się do BitBucket i przejdź do **integracji** przy użyciu tego konta.
    ![Pulpit nawigacyjny BitBucket — integracji][7]
2. Kliknij przycisk **OAuth** w obszarze zarządzania dostępem i **konsumenta Dodaj**.
    ![BitBucket dodać klienta OAuth][8]
3. Wprowadź **nazwa** dla użytkownika, na przykład usługi aplikacji Azure stosu.
4. Wprowadź **opis** dla aplikacji.
5. Wprowadź **adres URL wywołania zwrotnego**.  We wdrożeniu stosu Azure domyślny adres Url wywołania zwrotnego jest w formie https://portal.local.azurestack.external/TokenAuthorize, jeśli jest używane do uruchamiania zastępuje inną domenę domenę dla azurestack.local.  Adres Url musi występować po wielkich liter, wymienione w tym miejscu integracji BitBucket powiodło się.
6. Wprowadź **adres URL** — ten adres Url powinien być na przykład adres URL portalu Azure stosu https://portal.local.azurestack.external.
7. Wybierz **uprawnienia** wymagane:
    - **Repozytoria**: *odczytu*
    - **Elementów Webhook**: *odczytu i zapisu*
8. Kliknij pozycję **Zapisz**.  Zostanie wyświetlona nowa aplikacja, wraz z **klucza** i **klucz tajny** w obszarze **konsumentów OAuth**.
    ![Lista aplikacji BitBucket][9]
9.  W oknie lub nową kartę przeglądarki zalogować się do portalu administracyjnego platformy Azure stosu (https://adminportal.local.azurestack.external) jako administratora usługi.
10.  Przejdź do **dostawców zasobów** i wybierz **administrator dostawcy zasobów usługi aplikacji**.
11. Kliknij przycisk **konfiguracji kontroli źródła**.
12. Skopiuj i Wklej **klucza** do **identyfikator klienta** pole wprowadzania i **klucz tajny** do **klucz tajny klienta** pole wejściowe dla BitBucket.
13. Kliknij pozycję **Zapisz**.


## <a name="configure-onedrive"></a>Konfigurowanie usługi OneDrive

Musi mieć Account Microsoft połączone z kontem usługi OneDrive w celu wykonania tego zadania.  Możesz użyć konta organizacji, a nie konta osobistego.

> [!NOTE]
> OneDrive dla firm konta nie są obecnie obsługiwane.

1. Przejdź do https://apps.dev.microsoft.com/?referrer=https%3A%2F%2Fdev.onedrive.com%2Fapp-registration.htm i zaloguj się za pomocą Account Microsoft.
2. W obszarze **aplikacje**, kliknij przycisk **Dodaj aplikację**.
![Aplikacje usługi OneDrive][10]
3. Wprowadź **nazwa** dla nowej rejestracji aplikacji, wprowadź **usługi aplikacji Azure stosu**i kliknij przycisk **tworzenie aplikacji**
4. Następnym ekranie zawiera listę właściwości nowej aplikacji. Rekord **identyfikator aplikacji**.
![Właściwości aplikacji OneDrive][11]
5. W obszarze **klucze tajne aplikacji**, kliknij przycisk **wygenerować nowe hasło**. Zanotuj **nowe hasło generowane**. To jest klucz tajny aplikacji i nie jest pobieranie, po kliknięciu **OK** na tym etapie.
6. W obszarze **platform** kliknij **dodać platformy** i wybierz **Web**.
7. Wprowadź **identyfikator URI przekierowania**.  We wdrożeniu stosu Azure domyślny identyfikator URI przekierowania jest w formie https://portal.local.azurestack.external/TokenAuthorize, jeśli jest używane do uruchamiania zastępuje inną domenę domenę dla azurestack.local ![aplikacja usługi OneDrive — Dodaj platformy sieci Web][12]
8. Dodaj **uprawnienia Microsoft Graph** - **delegowane uprawnienia**
    - **Files.ReadWrite.AppFolder**
    - **User.Read**  
      ![Aplikacja usługi OneDrive — uprawnienia wykresu][13]
9. Kliknij pozycję **Zapisz**.
10.  W oknie lub nową kartę przeglądarki zalogować się do portalu administracyjnego platformy Azure stosu (https://adminportal.local.azurestack.external) jako administratora usługi.
11.  Przejdź do **dostawców zasobów** i wybierz **administrator dostawcy zasobów usługi aplikacji**.
12. Kliknij przycisk **konfiguracji kontroli źródła**.
13. Skopiuj i Wklej **identyfikator aplikacji** do **identyfikator klienta** pole wprowadzania i **hasło** do **klucz tajny klienta** pole wejściowe dla usługi OneDrive.
14. Kliknij pozycję **Zapisz**.

## <a name="configure-dropbox"></a>Konfigurowanie skrzynki

> [!NOTE]
> Musisz mieć konto usługi DropBox do zakończenia tego zadania.  Możesz użyć konta organizacji, a nie konta osobistego.

1. Przejdź do https://www.dropbox.com/developers/apps i zaloguj się za pomocą konta DropBox.
2. Kliknij przycisk **tworzenie aplikacji**.

    ![Aplikacje skrzynki][14]

3. Wybierz **DropBox interfejsu API**.
4. Ustaw poziom dostępu **aplikacji Folder**.
5. Wprowadź **nazwa** aplikacji.
![Rejestracja aplikacji Dropbox][15]
6. Kliknij przycisk **tworzenie aplikacji**.  Możesz teraz zobaczy stronę ustawienia dla aplikacji, w tym **klucz aplikacji** i **klucz tajny aplikacji**.
7. Sprawdź **nazwa folderu aplikacji** ustawiono **usługi aplikacji Azure stosu**.
8. Ustaw **identyfikator URI przekierowania 2 OAuth** i kliknij przycisk **Dodaj**.  We wdrożeniu stosu Azure domyślny identyfikator URI przekierowania jest w formie https://portal.local.azurestack.external/TokenAuthorize, jeśli jest używane do uruchamiania zastępuje inną domenę domenę dla azurestack.local.
![Konfiguracja aplikacji Dropbox][16]
9.  W oknie lub nową kartę przeglądarki zalogować się do portalu administracyjnego platformy Azure stosu (https://adminportal.local.azurestack.external) jako administratora usługi.
10.  Przejdź do **dostawców zasobów** i wybierz **administrator dostawcy zasobów usługi aplikacji**.
11. Kliknij przycisk **konfiguracji kontroli źródła**.
12. Skopiuj i Wklej **klucz aplikacji** do **identyfikator klienta** pole wprowadzania i **klucz tajny aplikacji** do **klucz tajny klienta** pole wejściowe dla DropBox.
13. Kliknij pozycję **Zapisz**.


<!--Image references-->
[1]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin.png
[2]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-source-control-configuration.png
[3]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-developer-applications.png
[4]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-register-a-new-oauth-application-populated.png
[5]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-register-a-new-oauth-application-complete.png
[6]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-roles-management-server-repair-all.png
[7]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-dashboard.png
[8]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-access-management-add-oauth-consumer.png
[9]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-access-management-add-oauth-consumer-complete.png
[10]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-applications.png
[11]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-registration.png
[12]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-platform.png
[13]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-graph-permissions.png
[14]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-applications.png
[15]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-application-registration.png
[16]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-application-configuration.png

## <a name="next-steps"></a>Kolejne kroki

Użytkownicy mogą teraz używać źródła wdrożenia dla elementów, takich jak [ciągłe wdrażanie](https://docs.microsoft.com/azure/app-service-web/app-service-continuous-deployment), [lokalnego wdrożenia Git](https://docs.microsoft.com/azure/app-service-web/app-service-deploy-local-git), i [chmury synchronizacji folderu](https://docs.microsoft.com/azure/app-service-web/app-service-deploy-content-sync).
