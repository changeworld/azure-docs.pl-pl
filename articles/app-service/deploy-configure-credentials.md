---
title: Skonfiguruj poświadczenia wdrożenia — usłudze Azure App Service | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać poświadczenia wdrożenia usługi Azure App Service.
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnoc
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 03/10/2019
ms.author: cephalin;byvinyal
ms.custom: seodec18
ms.openlocfilehash: df874ab77c88f05b048b1f9d10873943b7bebf36
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60765485"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Skonfiguruj poświadczenia wdrożenia dla usługi Azure App Service
[Usługa Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) obsługuje dwa typy poświadczeń dla [lokalne wdrożenie narzędzia Git](deploy-local-git.md) i [wdrożenia protokołu FTP/S](deploy-ftp.md). Te poświadczenia nie są takie same, jak poświadczeń usługi Azure Active Directory.

* **Poświadczenia na poziomie użytkownika**: jeden zestaw poświadczeń dla całego konta platformy Azure. Może służyć do wdrożenia usługi App Service dla każdej aplikacji, w przypadku subskrypcji, z uprawnień dostępu do konta platformy Azure. Jest domyślny zestaw, który jest udostępniane w portalu graficznego interfejsu użytkownika (takie jak **Przegląd** i **właściwości** aplikacji [strony zasobu](../azure-resource-manager/manage-resources-portal.md#manage-resources)). Po użytkownik uzyska dostęp do aplikacji za pomocą kontroli dostępu opartej na rolach (RBAC) lub uprawnienia coadmin, użytkownik ten może korzystać swoich własnych poświadczeń na poziomie użytkownika, dopóki nie został odwołany dostęp. Nie udostępniaj tych poświadczeń z innymi użytkownikami platformy Azure.

* **Poświadczenia na poziomie aplikacji**: jeden zestaw poświadczeń dla każdej aplikacji. Może służyć do wdrożenia tej aplikacji tylko. Poświadczenia dla każdej aplikacji są generowane automatycznie podczas tworzenia aplikacji. Nie można skonfigurować ręcznie ale można je zresetować w dowolnym czasie. Użytkownikom można udzielić dostępu do poświadczeń na poziomie aplikacji za pośrednictwem (RBAC), ten użytkownik musi być współautorem lub nowszej na aplikację. Czytelnicy nie są dozwolone do publikowania i nie może uzyskiwać dostęp do tych poświadczeń.

## <a name="userscope"></a>Wartości i zresetować poświadczenia na poziomie użytkownika

Poświadczenia na poziomie użytkownika można skonfigurować w dowolnej aplikacji [strony zasobu](../azure-resource-manager/manage-resources-portal.md#manage-resources). Niezależnie od tego w aplikacji, które można skonfigurować te poświadczenia, ma to zastosowanie do wszystkich aplikacji, a także dla wszystkich subskrypcji na Twoim koncie platformy Azure. 

Aby skonfigurować swoje poświadczenia na poziomie użytkownika:

1. W [witryny Azure portal](https://portal.azure.com), w menu po lewej stronie kliknij **App Services** > **&lt;any_app >** > **wdrożenia Centrum** > **poświadczenia wdrożenia**.

    W portalu musi mieć co najmniej jedną aplikację, zanim dostęp do strony poświadczeń wdrożenia. Jednak w przypadku [wiersza polecenia platformy Azure](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set), można skonfigurować poświadczeń na poziomie użytkownika bez istniejącej aplikacji.

2. Kliknij przycisk **poświadczenia użytkownika**, skonfiguruj nazwę użytkownika i hasło, a następnie kliknij przycisk **poświadczenia zapisu**.

    ![](./media/app-service-deployment-credentials/deployment_credentials_configure.png)

Po ustawieniu poświadczenia wdrożenia można znaleźć *Git* nazwa użytkownika wdrożenia w swojej aplikacji **Przegląd**,

![](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

i *FTP* nazwa użytkownika wdrożenia w swojej aplikacji **właściwości**.

![](./media/app-service-deployment-credentials/deployment_credentials_properties.png)

> [!NOTE]
> Azure nie są wyświetlane hasło do wdrażania na poziomie użytkownika. Jeśli zapomnisz hasło, wykonując kroki opisane w tej sekcji można zresetować swoje poświadczenia.
>
>  

## <a name="use-user-level-credentials-with-ftpftps"></a>Poświadczenia na poziomie użytkownika za pomocą protokołu FTP/FTPS

Uwierzytelnianie FTP/FTPS punkt końcowy przy użyciu requirers poświadczenia na poziomie użytkownika o nazwę użytkownika w następującym formacie: `<app-name>\<user-name>`

Ponieważ poświadczenia na poziomie użytkownika są połączone z kontem użytkownika i określonego zasobu, nazwa użytkownika musi być w następującym formacie, aby skierować działania logowania do endpoint odpowiedniej aplikacji.

## <a name="appscope"></a>Pobierz i zresetować poświadczenia na poziomie aplikacji
Aby uzyskać poświadczeń na poziomie aplikacji:

1. W [witryny Azure portal](https://portal.azure.com), w menu po lewej stronie kliknij **App Services** > **&lt;any_app >** > **wdrożenia Centrum** > **poświadczenia wdrożenia**.

2. Kliknij przycisk **poświadczenia aplikacji**i kliknij przycisk **kopiowania** link, aby skopiować nazwy użytkownika i hasła.

    ![](./media/app-service-deployment-credentials/deployment_credentials_app_level.png)

Aby przywrócić poświadczenia poziomie aplikacji, kliknij przycisk **zresetować poświadczenia** w tym samym oknie dialogowym.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak te poświadczenia służą do wdrażania aplikacji na podstawie [lokalnego narzędzia Git](deploy-local-git.md) lub za pomocą [protokołu FTP/S](deploy-ftp.md).
