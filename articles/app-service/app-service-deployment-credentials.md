---
title: Poświadczenia wdrożenia usługi Azure App Service | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać poświadczenia wdrożenia usługi Azure App Service.
services: app-service
documentationcenter: ''
author: dariagrigoriu
manager: erikre
editor: mollybos
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 01/05/2016
ms.author: dariagrigoriu
ms.openlocfilehash: a17260770f0b2e0a73585ce4108bd5625ac22229
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39436152"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Skonfiguruj poświadczenia wdrożenia dla usługi Azure App Service
[Usługa Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) obsługuje dwa typy poświadczeń dla [lokalne wdrożenie narzędzia Git](app-service-deploy-local-git.md) i [wdrożenia protokołu FTP/S](app-service-deploy-ftp.md). Nie są takie same, jak poświadczeń usługi Azure Active Directory.

* **Poświadczenia na poziomie użytkownika**: jeden zestaw poświadczeń dla całego konta platformy Azure. Może służyć do wdrożenia usługi App Service dla każdej aplikacji, w przypadku subskrypcji, z uprawnień dostępu do konta platformy Azure. Są to domyślny zestaw poświadczeń, skonfigurowanym w **App Services** > **&lt;nazwa_aplikacji >** > **poświadczenia wdrożenia**. Jest to opcja domyślna zestawu, który jest udostępniane w portalu graficznego interfejsu użytkownika (takie jak **Przegląd** i **właściwości** Twojej aplikacji [strony zasobu](../azure-resource-manager/resource-group-portal.md#manage-resources)).

    > [!NOTE]
    > Podczas możesz delegować dostęp do zasobów platformy Azure za pomocą kontroli dostępu na podstawie ról (RBAC) lub uprawnień współadministratora, każdy użytkownik platformy Azure, który uzyskuje dostęp do aplikacji można użyć jego osobistych poświadczeń użytkownika, dopóki nie został odwołany dostęp. Te poświadczenia wdrożenia powinna nie można udostępnić innym użytkownikom usługi Azure.
    >
    >

* **Poświadczenia na poziomie aplikacji**: jeden zestaw poświadczeń dla każdej aplikacji. Może służyć do wdrożenia tej aplikacji tylko. Poświadczenia dla każdej aplikacji jest generowany automatycznie podczas tworzenia aplikacji i znajduje się w aplikacji profilu publikowania. Nie można ręcznie skonfigurować poświadczenia, ale można je zresetować dowolnym dla aplikacji.

    > [!NOTE]
    > Aby dać innej osobie dostępu do tych poświadczeń za pośrednictwem roli na podstawie kontroli dostępu (RBAC), należy wprowadzić je współautora lub nowszego w aplikacji sieci Web. Czytelnicy nie są dozwolone do publikowania i dlatego nie może uzyskiwać dostęp do tych poświadczeń.
    >
    >

## <a name="userscope"></a>Wartości i zresetować poświadczenia na poziomie użytkownika

Poświadczenia na poziomie użytkownika można skonfigurować w dowolnej aplikacji [strony zasobu](../azure-resource-manager/resource-group-portal.md#manage-resources). Niezależnie od tego w aplikacji, które można skonfigurować te poświadczenia, ma to zastosowanie do wszystkich aplikacji, a także dla wszystkich subskrypcji na Twoim koncie platformy Azure. 

Aby skonfigurować swoje poświadczenia na poziomie użytkownika:

1. W [witryny Azure portal](https://portal.azure.com), kliknij opcję usługi App Service >  **&lt;any_app >** > **poświadczenia wdrożenia**.

    > [!NOTE]
    > W portalu musi mieć co najmniej jedną aplikację, zanim dostęp do strony poświadczeń wdrożenia. Jednak w przypadku [wiersza polecenia platformy Azure](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set), można skonfigurować poświadczeń na poziomie użytkownika bez istniejącej aplikacji.

2. Skonfiguruj nazwę użytkownika i hasło, a następnie kliknij przycisk **Zapisz**.

    ![](./media/app-service-deployment-credentials/deployment_credentials_configure.png)

Po ustawieniu poświadczenia wdrożenia można znaleźć *Git* nazwa użytkownika wdrożenia w swojej aplikacji **Przegląd**,

![](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

i *FTP* nazwa użytkownika wdrożenia w swojej aplikacji **właściwości**.

![](./media/app-service-deployment-credentials/deployment_credentials_properties.png)

> [!NOTE]
> Azure nie są wyświetlane hasło do wdrażania na poziomie użytkownika. Jeśli nie pamiętasz hasła, nie można go pobrać. Jednak możesz zresetować swoje poświadczenia, wykonując kroki opisane w tej sekcji.
>
>  

## <a name="appscope"></a>Pobierz i zresetować poświadczenia na poziomie aplikacji
Dla każdej aplikacji w usłudze App Service, jej poświadczenia na poziomie aplikacji są przechowywane w pliku XML profilu publikowania.

Aby uzyskać poświadczeń na poziomie aplikacji:

1. W [witryny Azure portal](https://portal.azure.com), kliknij opcję usługi App Service >  **&lt;any_app >** > **Przegląd**.

2. Kliknij przycisk **... Więcej** > **Pobierz profil publikowania**, i uruchamia pobierania. Plik ustawień publikacji.

    ![](./media/app-service-deployment-credentials/publish_profile_get.png)

3. Otwórz. Plik PublishSettings i Znajdź `<publishProfile>` tag z atrybutem `publishMethod="FTP"`. Następnie Pobierz jej `userName` i `password` atrybutów.
Są to poświadczenia poziomie aplikacji.

    ![](./media/app-service-deployment-credentials/publish_profile_editor.png)

    Podobnie poświadczenia na poziomie użytkownika, wartość FTP username wdrożenia jest w formacie `<app_name>\<username>`, a nazwa użytkownika wdrożenia Git jest po prostu `<username>` bez poprzedzającego `<app_name>\`.

Aby zresetować poświadczeń na poziomie aplikacji:

1. W [witryny Azure portal](https://portal.azure.com), kliknij opcję usługi App Service >  **&lt;any_app >** > **Przegląd**.

2. Kliknij przycisk **... Więcej** > **Resetowanie profilu publikowania**. Kliknij przycisk **tak** o potwierdzenie resetowania.

    Akcja resetowania unieważnia dowolne pobrane wcześniej. Pliki PublishSettings.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak te poświadczenia służą do wdrażania aplikacji na podstawie [lokalnego narzędzia Git](app-service-deploy-local-git.md) lub za pomocą [protokołu FTP/S](app-service-deploy-ftp.md).
