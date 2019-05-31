---
title: Tworzenie dzierżawy usługi Azure AD dla usługi Azure Red Hat OpenShift | Dokumentacja firmy Microsoft
description: Oto jak utworzyć dzierżawę usługi Azure Active Directory (Azure AD) do hostowania klastra usługi Microsoft Azure Red Hat OpenShift.
author: jimzim
ms.author: jzim
ms.service: container-service
manager: jeconnoc
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: 560cdcf8a99a486c7f5177b675cff327c6fb6a41
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306460"
---
# <a name="create-an-azure-ad-tenant-for-azure-red-hat-openshift"></a>Tworzenie dzierżawy usługi Azure AD dla usługi Azure Red Hat OpenShift

Wymaga programu Microsoft Azure Red Hat OpenShift [usługi Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) dzierżawy, w której chcesz utworzyć klaster. A *dzierżawy* to dedykowane wystąpienie usługi Azure AD dla deweloperów aplikacji lub organizacja otrzymuje podczas tworzenia relacji z firmą Microsoft przez zarejestrowanie się do platformy Azure, Microsoft Intune i Microsoft 365. Każda dzierżawa usługi Azure AD jest odrębna i oddzielonym od innych usługi Azure AD dzierżawy i ma swoją własną robocze i school tożsamości i rejestracje aplikacji.

Jeśli nie masz już dzierżawę usługi Azure AD, wykonaj te instrukcje, aby go utworzyć.

## <a name="create-a-new-azure-ad-tenant"></a>Tworzenie nowej dzierżawy usługi Azure AD

Aby utworzyć dzierżawę:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) przy użyciu konta chcesz skojarzyć z klastrem usługi Azure Red Hat OpenShift.
2. Otwórz [bloku Azure Active Directory](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) do utworzenia nowej dzierżawy (znany także jako nową *usługi Azure Active Directory*).
3. Podaj **nazwa organizacji**.
4. Podaj **początkowa nazwa domeny**. Będzie to miało *onmicrosoft.com* dołączone do niego. Można użyć ponownie wartość *nazwa organizacji* tutaj.
5. Wybierz kraj lub region, w którym zostanie utworzona dzierżawa.
6. Kliknij pozycję **Utwórz**.
7. Po utworzeniu dzierżawy usługi Azure AD wybierz **kliknij tutaj, aby zarządzać nowym katalogiem** łącza. Nazwa nowej dzierżawy powinna być wyświetlana w prawym górnym rogu witryny Azure portal:  

    ![Zrzut ekranu przedstawiający portal przedstawiający nazwę dzierżawy w prawym górnym rogu][tenantcallout]  

8. Zanotuj *identyfikator dzierżawy* Aby później można określić miejsce utworzyć klaster usługi Azure Red Hat OpenShift. W portalu powinien zostać wyświetlony blok Przegląd usługi Azure Active Directory dla nowej dzierżawy. Wybierz **właściwości** i skopiuj wartość dla Twojego **identyfikator katalogu**. Firma Microsoft będzie odnosił się do tej wartości jako `TENANT` w [Tworzenie klastra usługi Azure Red Hat OpenShift](tutorial-create-cluster.md) samouczka.

[tenantcallout]: ./media/howto-create-tenant/tenant-callout.png

## <a name="resources"></a>Zasoby

Zapoznaj się z [dokumentacji usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) uzyskać więcej informacji dotyczących [dzierżaw usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak utworzyć jednostkę usługi, wygenerować klienta wpisu tajnego i uwierzytelnianie adresów URL wywołania zwrotnego i utworzenie nowego użytkownika usługi Active Directory do testowania aplikacji w klastrze Azure Red Hat OpenShift.

[Utwórz obiekt aplikacji usługi Azure AD i użytkownika](howto-aad-app-configuration.md)