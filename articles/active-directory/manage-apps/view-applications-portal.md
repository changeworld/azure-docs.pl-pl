---
title: Szybki start — wyświetlanie aplikacji dzierżawy przy użyciu usługi Azure Active Directory
description: W tym przewodniku Szybki start użyj witryny Azure Portal, aby wyświetlić aplikacje w dzierżawie usługi Azure Active Directory (Azure AD).
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 04/09/2019
ms.author: mimart
ms.reviewer: arvinh
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d40e968bb7079d50e3fa18889ae996c9b59c90f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "74421823"
---
# <a name="quickstart-view-your-azure-active-directory-tenant-applications"></a>Szybki start: wyświetlanie aplikacji dzierżawy usługi Azure Active Directory

W tym przewodniku Szybki start opisano wyświetlanie aplikacji w dzierżawie usługi Azure Active Directory (Azure AD) przy użyciu witryny Azure Portal.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby zobaczyć wyniki, musisz mieć co najmniej jedną aplikację w dzierżawie usługi Azure AD. Aby dodać aplikację, zobacz przewodnik Szybki start [Dodawanie aplikacji](add-application-portal.md).

Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator globalny dzierżawy usługi Azure AD, administrator aplikacji w chmurze lub administrator aplikacji.

## <a name="find-the-list-of-tenant-applications"></a>Wyszukiwanie listy aplikacji dzierżawy

Aplikacje dzierżawy usługi Azure AD można wyświetlić w sekcji **Aplikacje dla przedsiębiorstw** witryny Azure Portal.

Aby znaleźć swoje aplikacje dzierżawy, wykonaj następujące czynności:

1. W **[witrynie Azure portal](https://portal.azure.com)** po lewej stronie panelu nawigacji wybierz pozycję **Azure Active Directory**.
1. W okienku **Usługi Active Directory wybierz** pozycję **Aplikacje przedsiębiorstwa**.
1. Z menu rozwijanego **Typ aplikacji** wybierz pozycję **Wszystkie aplikacje**i wybierz polecenie **Zastosuj**. Zostanie wyświetlona losowa próbka aplikacji dzierżawy.
1. Aby wyświetlić więcej aplikacji, wybierz **pozycję Załaduj więcej** u dołu listy. W zależności od liczby aplikacji w dzierżawie może być łatwiej [wyszukać konkretną aplikację](#search-for-a-tenant-application) zamiast przewijać listę.

## <a name="select-viewing-options"></a>Wybieranie opcje wyświetlania

Wybierz opcje w zależności od tego, czego szukasz.

1. Aplikacje można wyświetlać według **typu aplikacji,** **stanu aplikacji**i **widoczności aplikacji**.
1. W obszarze **Typ aplikacji** wybierz jedną z następujących opcji:

    - Opcja **Aplikacje dla przedsiębiorstw** wyświetla aplikacje firm innych niż Microsoft.
    - Opcja **Aplikacje firmy Microsoft** wyświetla aplikacji firmy Microsoft.
    - Opcja **Wszystkie aplikacje** wyświetla zarówno aplikacje firm innych niż Microsoft, jak i aplikacje firmy Microsoft.

1. W obszarze **Stan aplikacji** wybierz pozycję **Dowolne**, **Wyłączono** lub **Włączono**. Opcja **Dowolne** obejmuje zarówno aplikacje wyłączone, jak i włączone.
1. W obszarze **Widoczność aplikacji** wybierz pozycję **Dowolne** lub **Ukryte**. Opcja **Ukryte** pokazuje aplikacje, które znajdują się w dzierżawie, ale nie są widoczne dla użytkowników.
1. Po wybraniu żądanych opcji wybierz pozycję **Zastosuj**.

## <a name="search-for-a-tenant-application"></a>Wyszukiwanie aplikacji dzierżawy

Aby wyszukać określoną aplikację:

1. W menu **Typ aplikacji** wybierz polecenie **Wszystkie aplikacje**i wybierz polecenie **Zastosuj**.
1. Wprowadź nazwę aplikacji, którą chcesz znaleźć. Jeśli aplikacja została dodana do dzierżawy usługi Azure AD, pojawi się w wynikach wyszukiwania. W tym przykładzie pokazano, że GitHub nie został dodany do aplikacji dzierżawy.

    ![Przykład pokazuje, że aplikacja nie została dodana do dzierżawy](media/view-applications-portal/search-for-tenant-application.png)

1. Spróbuj wprowadzić kilka pierwszych liter nazwy aplikacji. W tym przykładzie pokazano wszystkie aplikacje, których nazwy zaczynają się od **Sales**.

    ![Przykład pokazuje wszystkie aplikacje rozpoczynające się od sprzedaży](media/view-applications-portal/search-by-prefix.png)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start dowiesz się, jak wyświetlić aplikacje w dzierżawie usługi Azure AD. Dowiedzialiście się, jak filtrować listę aplikacji według typu aplikacji, stanu i widoczności. Przedstawiono również sposób wyszukiwania określonej aplikacji.

Teraz, gdy znalazłeś aplikację, której szukasz, możesz kontynuować [dodawanie większej liczby aplikacji do dzierżawy.](add-application-portal.md) Można też wybrać aplikację do wyświetlania lub edytowania właściwości i opcji konfiguracji. Możesz na przykład skonfigurować logowanie jednokrotne.

> [!div class="nextstepaction"]
> [Konfigurowanie logowania jednokrotnego](configure-single-sign-on-non-gallery-applications.md)
