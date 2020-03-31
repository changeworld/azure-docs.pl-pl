---
title: Tworzenie dzierżawy usługi Azure AD dla usługi Azure Red Hat OpenShift
description: Poniżej opisano, jak utworzyć dzierżawę usługi Azure Active Directory (Azure AD) do obsługi klastra OpenShift Red Hat platformy Microsoft Azure.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: ad03538cafcce9c1d660d0f2ac5eb3c6ae5f4f38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243694"
---
# <a name="create-an-azure-ad-tenant-for-azure-red-hat-openshift"></a>Tworzenie dzierżawy usługi Azure AD dla usługi Azure Red Hat OpenShift

Usługa Microsoft Azure Red Hat OpenShift wymaga [dzierżawy usługi Azure Active Directory (Azure AD),](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) w której można utworzyć klaster. *Dzierżawa* to dedykowane wystąpienie usługi Azure AD, które organizacja lub deweloper aplikacji otrzymuje podczas tworzenia relacji z firmą Microsoft, rejestrując się w usłudze Azure, microsoft intune lub usłudze Microsoft 365. Każda dzierżawa usługi Azure AD jest odrębna i oddzielona od innych dzierżaw usługi Azure AD i ma własne tożsamości robocze i szkolne oraz rejestracje aplikacji.

Jeśli nie masz jeszcze dzierżawy usługi Azure AD, postępuj zgodnie z tymi instrukcjami, aby go utworzyć.

## <a name="create-a-new-azure-ad-tenant"></a>Tworzenie nowej dzierżawy usługi Azure AD

Aby utworzyć dzierżawę:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) przy użyciu konta, które chcesz skojarzyć z klastrem Azure Red Hat OpenShift.
2. Otwórz [blok usługi Azure Active Directory,](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) aby utworzyć nową dzierżawę (z nazywaną również nową *usługą Azure Active Directory).*
3. Podaj **nazwę organizacji**.
4. Podaj **początkową nazwę domeny**. Będzie to *onmicrosoft.com* do niego dołączone. W tym miejscu można ponownie użyć wartości *nazwy organizacji.*
5. Wybierz kraj lub region, w którym zostanie utworzona dzierżawa.
6. Kliknij przycisk **Utwórz**.
7. Po utworzeniu dzierżawy usługi Azure AD wybierz **kliknij tutaj, aby zarządzać nowym łączem katalogu.** Twoja nowa nazwa dzierżawy powinna być wyświetlana w prawym górnym rogu witryny Azure portal:  

    ![Zrzut ekranu przedstawiający portal przedstawiający nazwę dzierżawy w prawym górnym rogu][tenantcallout]  

8. Zanotuj *identyfikator dzierżawy,* aby później określić, gdzie należy utworzyć klaster OpenShift usługi Azure Red Hat. W portalu powinien zostać wyświetlony blok przegląd usługi Azure Active Directory dla nowej dzierżawy. Wybierz **pozycję Właściwości** i skopiuj wartość identyfikatora **katalogu**. Będziemy odwoływać się `TENANT` do tej wartości, jak w [tworzenie narzędzia Azure Red Hat OpenShift klastra](tutorial-create-cluster.md) samouczek.

[tenantcallout]: ./media/howto-create-tenant/tenant-callout.png

## <a name="resources"></a>Resources

Zapoznaj się z [dokumentacją usługi Azure Active Directory,](https://docs.microsoft.com/azure/active-directory/) aby uzyskać więcej informacji na temat [dzierżawy usługi Azure AD.](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak utworzyć jednostkę usługi, wygenerować adres URL wywołania zwrotnego klucza tajnego klienta i uwierzytelniania oraz utworzyć nowego użytkownika usługi Active Directory do testowania aplikacji w klastrze Azure Red Hat OpenShift.

[Tworzenie użytkownika i obiektu aplikacji usługi Azure AD](howto-aad-app-configuration.md)
