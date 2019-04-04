---
title: Wyświetlanie aplikacji dzierżawy — Azure Active Directory | Microsoft Docs
description: Wyświetlanie aplikacji w dzierżawie usługi Azure Active Directory (Azure AD) przy użyciu witryny Azure Portal.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/25/2018
ms.author: celested
ms.reviewer: arvinh
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ca57746d1b13d2020f78d3c527e7d0c7a444769
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2019
ms.locfileid: "58918403"
---
# <a name="view-your-azure-active-directory-tenant-applications"></a>Wyświetlanie aplikacji dzierżawy usługi Azure Active Directory

W tym przewodniku Szybki start opisano wyświetlanie aplikacji w dzierżawie usługi Azure Active Directory (Azure AD) przy użyciu witryny Azure Portal.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby zobaczyć wyniki, musisz mieć co najmniej jedną aplikację w dzierżawie usługi Azure AD. Aby dodać aplikację, zobacz przewodnik Szybki start [Dodawanie aplikacji](add-application-portal.md).

Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator globalny dzierżawy usługi Azure AD, administrator aplikacji w chmurze lub administrator aplikacji.

## <a name="find-the-list-of-tenant-applications"></a>Wyszukiwanie listy aplikacji dzierżawy

Aplikacje dzierżawy usługi Azure AD można wyświetlić w sekcji **Aplikacje dla przedsiębiorstw** witryny Azure Portal.

Aby znaleźć swoje aplikacje dzierżawy, wykonaj następujące czynności:

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie wybierz **usługi Azure Active Directory**. 

2. W **usługi Azure Active Directory** okienku wybierz **aplikacje dla przedsiębiorstw**. 

3. Z **typ aplikacji** menu rozwijanego wybierz opcję **wszystkie aplikacje**i wybierz polecenie **Zastosuj**. Zostanie wyświetlona losowa próbka aplikacji dzierżawy.
   
4. Aby wyświetlić więcej aplikacji, wybierz **Załaduj więcej** w dolnej części listy. W zależności od liczby aplikacji w dzierżawie może być łatwiej [wyszukać konkretną aplikację](#search-for-a-tenant-application) zamiast przewijać listę.

## <a name="select-viewing-options"></a>Wybieranie opcje wyświetlania

Wybierz opcje zgodnie z czego szukasz.

1. Można przejrzeć aplikacje przez **typ aplikacji**, **stan aplikacji**, i **widoczność aplikacji**. 

2. W obszarze **Typ aplikacji** wybierz jedną z następujących opcji:

    - Opcja **Aplikacje dla przedsiębiorstw** wyświetla aplikacje firm innych niż Microsoft.
    - Opcja **Aplikacje firmy Microsoft** wyświetla aplikacji firmy Microsoft.
    - Opcja **Wszystkie aplikacje** wyświetla zarówno aplikacje firm innych niż Microsoft, jak i aplikacje firmy Microsoft.

3. W obszarze **Stan aplikacji** wybierz pozycję **Dowolne**, **Wyłączono** lub **Włączono**. Opcja **Dowolne** obejmuje zarówno aplikacje wyłączone, jak i włączone.

4. W obszarze **Widoczność aplikacji** wybierz pozycję **Dowolne** lub **Ukryte**. **Ukryty** opcja umożliwia pokazanie aplikacje, które są w dzierżawie, ale nie są widoczne dla użytkowników.

5. Po wybraniu wybrane opcje, wybierz **Zastosuj**.
 

## <a name="search-for-a-tenant-application"></a>Wyszukiwanie aplikacji dzierżawy

Aby wyszukać określoną aplikację:

1. W **typ aplikacji** menu, wybierz opcję **wszystkie aplikacje**i wybierz polecenie **Zastosuj**.

2. Wprowadź nazwę aplikacji, którą chcesz znaleźć. Jeśli aplikacja została dodana do dzierżawy usługi Azure AD, zostanie wyświetlony w wynikach wyszukiwania. Ten przykład pokazuje, że GitHub nie został dodany do aplikacji dzierżawy.

    ![Wyszukiwanie aplikacji](media/view-applications-portal/search-for-tenant-application.png)

3. Spróbuj wprowadzić kilka pierwszych liter nazwy aplikacji. W tym przykładzie pokazano wszystkie aplikacje, których nazwy zaczynają się od **Sales**.

    ![Wyszukiwanie przy użyciu prefiksu](media/view-applications-portal/search-by-prefix.png)

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki Start przedstawiono sposób wyświetlania aplikacji w dzierżawie usługi Azure AD. Pokazaliśmy ci, jak filtrować listę aplikacji według typu aplikacji, stanu i widoczności. Przedstawiono również sposób wyszukiwania określonej aplikacji.

Teraz, gdy Ci się znaleźć aplikacji zostały znalezione dla można kontynuować [Dodawanie większej liczby aplikacji z dzierżawą](add-application-portal.md). Alternatywnie można wybrać aplikację, aby wyświetlić lub edytować właściwości i opcje konfiguracji. Możesz na przykład skonfigurować logowanie jednokrotne. 

> [!div class="nextstepaction"]
> [Konfigurowanie logowania jednokrotnego](configure-single-sign-on-portal.md)


