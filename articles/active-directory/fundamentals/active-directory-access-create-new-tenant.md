---
title: Szybki start — uzyskiwanie dostępu do usługi Azure Active Directory i tworzenie nowej dzierżawy | Microsoft Docs
description: Instrukcje umożliwiające znalezienie usługi Azure Active Directory oraz utworzenie nowej dzierżawy dla organizacji.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: lizross
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fafa3974eb01b36015254307ba1a52a9bc221da
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59798642"
---
# <a name="quickstart-create-a-new-tenant-in-azure-active-directory"></a>Szybki start: Tworzenie nowej dzierżawy w usłudze Azure Active Directory
Wszystkie zadania administracyjne, w tym tworzenie nowej dzierżawy dla swojej organizacji, możesz wykonywać przy użyciu portalu usługi Azure Active Directory (Azure AD). 

W tym przewodniku Szybki start dowiesz się, jak uzyskać dostęp do witryny Azure Portal i usługi Azure Active Directory oraz jak utworzyć podstawową dzierżawę dla organizacji.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal
Zaloguj się do witryny [Azure Portal](https://portal.azure.com/) Twojej organizacji przy użyciu konta administratora globalnego.

![Azure portal ekranu, przy użyciu opcji usługi Azure AD](media/active-directory-access-create-new-tenant/azure-ad-portal.png)

## <a name="create-a-new-tenant-for-your-organization"></a>Tworzenie nowej dzierżawy dla organizacji
Po zalogowaniu się do witryny Azure Portal możesz utworzyć nową dzierżawę dla swojej organizacji. Nowa dzierżawa reprezentuje Twoją organizację i pomaga w zarządzaniu konkretnym wystąpieniem usług firmy Microsoft w chmurze na potrzeby użytkowników wewnętrznych i zewnętrznych.

### <a name="to-create-a-new-tenant"></a>Aby utworzyć nową dzierżawę
1. Wybierz pozycję **Utwórz zasób**, wybierz pozycję **Tożsamość**, a następnie wybierz pozycję **Azure Active Directory**.

    Zostanie wyświetlona strona **Tworzenie katalogu**.

    ![Strona tworzenia usługi Azure Active Directory](media/active-directory-access-create-new-tenant/azure-ad-create-new-tenant.png)

2.  Na stronie **Tworzenie katalogu** wprowadź następujące informacje:
    
    - Wpisz wartość _Contoso_ w polu **Nazwa organizacji**.

    - Wpisz wartość _Contoso_ w polu **Początkowa nazwa domeny**.

    - Pozostaw opcję _Stany Zjednoczone_ w polu **Kraj lub region**.

3. Wybierz pozycję **Utwórz**.

Twoja nowa dzierżawa zostanie utworzona i będzie miała domenę contoso.onmicrosoft.com.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Jeśli nie zamierzasz nadal korzystać z tej aplikacji, możesz usunąć dzierżawę, wykonując następujące czynności:

- Wybierz usługę **Azure Active Directory**, a następnie na stronie **Contoso — przegląd** wybierz pozycję **Usuń katalog**.

    Dzierżawa zostanie usunięta wraz ze skojarzonymi informacjami.

    ![Strona przeglądu, za pomocą przycisku Usuń wyróżniony w katalogu](media/active-directory-access-create-new-tenant/azure-ad-delete-new-tenant.png)

## <a name="next-steps"></a>Kolejne kroki
- Zmiana lub dodawanie kolejnych nazw domen — zobacz [Jak dodać nazwę domeny niestandardowej do usługi Azure Active Directory](add-custom-domain.md)

- Dodawanie użytkowników — zobacz [Dodawanie lub usuwanie nowego użytkownika](add-users-azure-active-directory.md)

- Dodawanie grup i członków — zobacz [Tworzenie podstawowej grupy i dodawanie członków](active-directory-groups-create-azure-portal.md)

- Dowiedz się więcej na temat [dostępu na podstawie ról przy użyciu usługi Privileged Identity Management](../../role-based-access-control/pim-azure-resource.md) oraz [dostępu warunkowego](../../role-based-access-control/conditional-access-azure-management.md), aby ułatwić zarządzanie dostępem do aplikacji i zasobów w organizacji.

- Uzyskaj informacje o usłudze Azure AD, w tym [podstawowe informacje o licencjonowaniu oraz informacje o terminologii i skojarzonych funkcjach](active-directory-whatis.md).
