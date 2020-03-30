---
title: Szybki start — dostęp & tworzenia nowej dzierżawy — Azure AD
description: Instrukcje umożliwiające znalezienie usługi Azure Active Directory oraz utworzenie nowej dzierżawy dla organizacji.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f29d103ce1be426fb0b5c462cc1d831fefe87b6
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80050009"
---
# <a name="quickstart-create-a-new-tenant-in-azure-active-directory"></a>Szybki start: tworzenie nowej dzierżawy w usłudze Azure Active Directory
Wszystkie zadania administracyjne, w tym tworzenie nowej dzierżawy dla swojej organizacji, możesz wykonywać przy użyciu portalu usługi Azure Active Directory (Azure AD). 

W tym przewodniku Szybki start dowiesz się, jak uzyskać dostęp do witryny Azure Portal i usługi Azure Active Directory oraz jak utworzyć podstawową dzierżawę dla organizacji.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="create-a-new-tenant-for-your-organization"></a>Tworzenie nowej dzierżawy dla organizacji
Po zalogowaniu się do witryny Azure Portal możesz utworzyć nową dzierżawę dla swojej organizacji. Nowa dzierżawa reprezentuje Twoją organizację i pomaga w zarządzaniu konkretnym wystąpieniem usług firmy Microsoft w chmurze na potrzeby użytkowników wewnętrznych i zewnętrznych.

### <a name="to-create-a-new-tenant"></a>Aby utworzyć nową dzierżawę

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/)organizacji .

1. Z menu Portalu platformy Azure wybierz polecenie **Utwórz zasób**.  

    ![Strona Tworzenia ponownego zaserwuj usługę Azure Active Directory](media/active-directory-access-create-new-tenant/azure-ad-portal.png)

1. Wybierz **pozycję Tożsamość**, a następnie wybierz pozycję **Usługa Azure Active Directory**.

    Zostanie wyświetlona strona **Tworzenie katalogu**.

    ![Strona tworzenia usługi Azure Active Directory](media/active-directory-access-create-new-tenant/azure-ad-create-new-tenant.png)

1.  Na stronie **Tworzenie katalogu** wprowadź następujące informacje:
    
    - Wpisz wartość _Contoso_ w polu **Nazwa organizacji**.

    - Wpisz wartość _Contoso_ w polu **Początkowa nazwa domeny**.

    - Pozostaw opcję _Stany Zjednoczone_ w polu **Kraj lub region**.

1. Wybierz **pozycję Utwórz**.

Twoja nowa dzierżawa zostanie utworzona i będzie miała domenę contoso.onmicrosoft.com.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Jeśli nie zamierzasz nadal korzystać z tej aplikacji, możesz usunąć dzierżawcę, wykonując następujące kroki:

- Upewnij się, że zalogowano się do katalogu, który chcesz usunąć za pomocą filtru **subskrypcja Katalog +** w witrynie Azure Portal i w razie potrzeby przełączysz się do katalogu docelowego.
- Wybierz usługę **Azure Active Directory**, a następnie na stronie **Contoso — przegląd** wybierz pozycję **Usuń katalog**.

    Dzierżawa zostanie usunięta wraz ze skojarzonymi informacjami.

    ![Strona Przegląd z wyróżnionym przyciskiem Usuń katalog](media/active-directory-access-create-new-tenant/azure-ad-delete-new-tenant.png)

## <a name="next-steps"></a>Następne kroki
- Zmiana lub dodawanie kolejnych nazw domen — zobacz [Jak dodać nazwę domeny niestandardowej do usługi Azure Active Directory](add-custom-domain.md)

- Dodawanie użytkowników — zobacz [Dodawanie lub usuwanie nowego użytkownika](add-users-azure-active-directory.md)

- Dodawanie grup i członków — zobacz [Tworzenie podstawowej grupy i dodawanie członków](active-directory-groups-create-azure-portal.md)

- Dowiedz się więcej o [dostępie opartym na rolach przy użyciu zarządzania tożsamościami uprzywilejowanymi](../../role-based-access-control/pim-azure-resource.md) i [dostępu warunkowego](../../role-based-access-control/conditional-access-azure-management.md) w celu zarządzania aplikacjami i zasobami organizacji.

- Uzyskaj informacje o usłudze Azure AD, w tym [podstawowe informacje o licencjonowaniu oraz informacje o terminologii i skojarzonych funkcjach](active-directory-whatis.md).
