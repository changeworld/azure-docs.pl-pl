---
title: Usuwanie aplikacji z usługi Azure Active Directory
description: Dowiedz się, jak usunąć aplikację z usługi Azure Active Directory (Azure AD).
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0038dcc10aafa191121b2797f68d66a3e32b3fa7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60298764"
---
# <a name="quickstart-remove-an-application-from-azure-active-directory"></a>Szybki start: Usuwanie aplikacji z usługi Azure Active Directory

Deweloperzy w przedsiębiorstwach oraz dostawcy oprogramowania jako usługi (SaaS), którzy zarejestrowali aplikacje w usłudze Azure Active Directory (Azure AD), mogą usunąć rejestracje aplikacji z dzierżawy usługi Azure AD.

W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:

* [Usuwanie aplikacji utworzonej przez Twoją organizację](#removing-an-application-authored-by-your-organization)
* [Usuwanie aplikacji z wieloma dzierżawami autoryzowanej przez inną organizację](#removing-a-multi-tenant-application-authorized-by-another-organization)

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, potrzebna jest dzierżawa usługi Azure AD, w której zarejestrowano aplikacje.

* Jeśli nie masz jeszcze dzierżawy, [dowiedz się, jak ją uzyskać](quickstart-create-new-tenant.md).
* Aby dowiedzieć się, w jaki sposób dodać i zarejestrować aplikacje w swojej dzierżawie, zobacz [Add an application to Azure AD (Dodawanie aplikacji do usługi Azure AD)](quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="removing-an-application-authored-by-your-organization"></a>Usuwanie aplikacji utworzonej przez Twoją organizację

Aplikacje zarejestrowane przez Twoją organizację są wyświetlane w obszarze filtru **Moje aplikacje** na stronie głównej **Rejestracje aplikacji** dzierżawy. Są to aplikacje, które zarejestrowano ręcznie za pośrednictwem portalu Azure Portal lub programistycznie za pośrednictwem programu PowerShell lub interfejsu API programu Microsoft Graph. Dokładniej mówiąc, te aplikacje są reprezentowane zarówno przez obiekt aplikacji, jak i obiekt główny usługi w dzierżawie. Aby uzyskać więcej informacji na temat tych obiektów, zobacz [Application objects and service principal objects (Obiekty aplikacji i obiekty główne usługi)](app-objects-and-service-principals.md).

### <a name="to-remove-a-single-tenant-application-from-your-directory"></a>Aby usunąć aplikację z jedną dzierżawą z katalogu

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Jeśli Twoje konto umożliwia dostęp do więcej niż jednej dzierżawy, wybierz konto w prawym górnym rogu, a następnie ustaw sesję portalu na odpowiednią dzierżawę usługi Azure AD.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory**, wybierz pozycję **Rejestracje aplikacji**, a następnie znajdź i wybierz aplikację, którą chcesz skonfigurować.
    Nastąpi przekierowanie do głównej strony rejestracji aplikacji, która otwiera stronę **Ustawienia** aplikacji.
1. Na stronie głównej rejestracji aplikacji wybierz pozycję **Usuń**.
1. Wybierz pozycję **Tak**, aby potwierdzić, że chcesz usunąć aplikację.

### <a name="to-remove-a-multi-tenant-application-from-its-home-directory"></a>Aby usunąć aplikację z wieloma dzierżawami z katalogu macierzystego

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Jeśli Twoje konto umożliwia dostęp do więcej niż jednej dzierżawy, wybierz konto w prawym górnym rogu, a następnie ustaw sesję portalu na odpowiednią dzierżawę usługi Azure AD.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory**, wybierz pozycję **Rejestracje aplikacji**, a następnie znajdź i wybierz aplikację, którą chcesz skonfigurować.
    Nastąpi przekierowanie do głównej strony rejestracji aplikacji, która otwiera stronę **Ustawienia** aplikacji.
1. Na stronie **Ustawienia** wybierz pozycję **Właściwości** i ustaw przełącznik **Z wieloma dzierżawami** w pozycji **Nie**, aby najpierw zmienić aplikację na aplikację z jedną dzierżawą, a następnie wybierz polecenie **Zapisz**.
    Obiekty główne usługi aplikacji pozostają w dzierżawach wszystkich organizacji, które wyraziły na to zgodę.
1. Wybierz polecenie **Usuń** na stronie głównej rejestracji aplikacji.
1. Wybierz pozycję **Tak**, aby potwierdzić, że chcesz usunąć aplikację.

## <a name="removing-a-multi-tenant-application-authorized-by-another-organization"></a>Usuwanie aplikacji z wieloma dzierżawami autoryzowanej przez inną organizację

Podzestaw aplikacji, który jest wyświetlany w obszarze filtru **Wszystkie aplikacje** (oprócz rejestracji **Moje aplikacje**) na stronie głównej **Rejestracje aplikacji** dzierżawy, to aplikacje z wieloma dzierżawami.

Pod względem technicznym aplikacje z wieloma dzierżawami należą do innej dzierżawy i zostały zarejestrowane w Twojej dzierżawie po udzielenia zgody. Dokładniej mówiąc, są reprezentowane tylko przez obiekt główny usługi w dzierżawie, bez odpowiadającego mu obiektu aplikacji. Aby uzyskać więcej informacji na temat różnic między obiektami aplikacji a jednostkami usługi, zobacz [Application and service principal objects in Azure AD (Obiekty aplikacji i obiekty główne usługi w usłudze Azure AD)](app-objects-and-service-principals.md).

Aby usunąć dostęp aplikacji z wieloma dzierżawami do Twojego katalogu (po wcześniejszym udzieleniu zgody), administrator firmy musi usunąć jednostkę usługi aplikacji. Administrator mający uprawnienia administratora globalnego może usunąć dostęp w witrynie Azure Portal lub za pośrednictwem [poleceń cmdlet programu PowerShell usługi Azure AD](https://go.microsoft.com/fwlink/?LinkId=294151).

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat innych powiązanych podręczników Szybki start dotyczących zarządzania aplikacjami korzystającymi z punktu końcowego usługi Azure AD w wersji 1.0:

- [Add an application to Azure AD (Dodawanie aplikacji do usługi Azure AD)](quickstart-v1-integrate-apps-with-azure-ad.md)
- [Update an application in Azure AD (Aktualizowanie aplikacji w usłudze Azure AD)](quickstart-v1-update-azure-ad-app.md)
