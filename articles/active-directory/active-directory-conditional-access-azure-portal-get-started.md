---
title: Rozpoczynanie pracy z dostępu warunkowego w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Testowanie dostępu warunkowego za pomocą warunku lokalizacji.
services: active-directory
keywords: dostęp warunkowy do aplikacji, dostęp warunkowy przy użyciu usługi Azure AD, bezpieczny dostęp do zasobów firmy, zasady dostępu warunkowego
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: cd701b8c6f015afc60b434317d0d7fef0560e9af
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34723708"
---
# <a name="get-started-with-conditional-access-in-azure-active-directory"></a>Rozpoczynanie pracy z dostępu warunkowego w usłudze Azure Active Directory

Dostęp warunkowy jest możliwość usługi Azure Active Directory, który umożliwia zdefiniowanie warunków, w których autoryzowani użytkownicy mają dostęp do Twojej aplikacji. 

Ten temat zawiera instrukcje dotyczące testowania dostępu warunkowego na podstawie warunku lokalizacji w danym środowisku.  


## <a name="scenario-description"></a>Opis scenariusza

Jednym z typowych wymagań w wielu organizacjach jest wymagane tylko uwierzytelnianie wieloskładnikowe dla dostępu do aplikacji, która nie jest wykonywane od firmowego intranetu. Z usługą Azure Active Directory można łatwo realizację tego celu, konfigurując zasady dostępu warunkowego na podstawie lokalizacji. W tym temacie przedstawiono szczegółowe instrukcje dotyczące konfigurowania pokrewnych zasad. Wykorzystanie zasad [zaufanych adresów IP](authentication/howto-mfa-mfasettings.md#trusted-ips) odróżnienie z firmowej prób dostępu do wszystkich innych lokalizacji w intranecie i.


## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym temacie założono, że znasz pojęcia opisane w [dostępu warunkowego w usłudze Azure Active Directory](active-directory-conditional-access-azure-portal.md).

Do przetestowania tego scenariusza, należy:

- Tworzenie użytkownika testowego 

- Przypisywanie licencji usługi Azure AD Premium do użytkownika testowego

- Konfigurowanie zarządzanych aplikacji i przypisać użytkownika testowego

- Skonfiguruj listę zaufanych adresów IP

Aby uzyskać więcej informacji na temat zaufanych adresów IP, zobacz [zaufanych adresów IP](authentication/howto-mfa-mfasettings.md#trusted-ips).


## <a name="policy-configuration-steps"></a>Kroki konfiguracji zasad

**Aby skonfigurować zasady dostępu warunkowego, należy wykonać:**

1. W portalu Azure na lewy pasek nawigacyjny, kliknij przycisk **usługi Azure Active Directory**. 

    ![Dostęp warunkowy](./media/active-directory-conditional-access-azure-portal-get-started/01.png)

2. Na **usługi Azure Active Directory** bloku, w **Zarządzaj** kliknij **dostępu warunkowego**.

    ![Dostęp warunkowy](./media/active-directory-conditional-access-azure-portal-get-started/02.png)
 
3. Na **dostępu warunkowego** bloku, aby otworzyć **nowy** bloku na pasku narzędzi u góry, kliknij przycisk **Dodaj**.

    ![Dostęp warunkowy](./media/active-directory-conditional-access-azure-portal-get-started/03.png)

4. Na **nowy** bloku, w **nazwa** tekstowym, wpisz nazwę zasady.

    ![Dostęp warunkowy](./media/active-directory-conditional-access-azure-portal-get-started/04.png)

5. W **przypisania** kliknij **użytkowników i grup**.

    ![Dostęp warunkowy](./media/active-directory-conditional-access-azure-portal-get-started/05.png)

6. Na **użytkowników i grup** blok, wykonaj następujące czynności:

    ![Dostęp warunkowy](./media/active-directory-conditional-access-azure-portal-get-started/06.png)

    a. Kliknij przycisk **Wybierz użytkowników i grupy**.

    b. Kliknij pozycję **Wybierz**.

    c. Na **wybierz** bloku, wybierz użytkownika testowego, a następnie kliknij przycisk **wybierz**.

    d. Na **użytkowników i grup** bloku, kliknij przycisk **gotowe**.

7. Na **nowy** bloku, aby otworzyć **aplikacji w chmurze** bloku, w **przypisania** kliknij **aplikacji w chmurze**.

    ![Dostęp warunkowy](./media/active-directory-conditional-access-azure-portal-get-started/07.png)

8. Na **aplikacji w chmurze** blok, wykonaj następujące czynności:

    ![Dostęp warunkowy](./media/active-directory-conditional-access-azure-portal-get-started/08.png)

    a. Kliknij przycisk **Wybierz aplikacje**.

    b. Kliknij pozycję **Wybierz**.

    c. Na **wybierz** bloku, wybierz aplikacji w chmurze, a następnie kliknij przycisk **wybierz**.

    d. Na **aplikacji w chmurze** bloku, kliknij przycisk **gotowe**.

9. Na **nowy** bloku, aby otworzyć **warunki** bloku, w **przypisania** kliknij **warunki**.

    ![Dostęp warunkowy](./media/active-directory-conditional-access-azure-portal-get-started/09.png)

10. Na **warunki** bloku, aby otworzyć **lokalizacje** bloku, kliknij przycisk **lokalizacje**.

    ![Dostęp warunkowy](./media/active-directory-conditional-access-azure-portal-get-started/10.png)

11. Na **lokalizacje** blok, wykonaj następujące czynności:

    ![Dostęp warunkowy](./media/active-directory-conditional-access-azure-portal-get-started/11.png)

    a. W obszarze **Konfiguruj**, kliknij przycisk **tak**.

    b. W obszarze **Include**, kliknij przycisk **wszystkie lokalizacje**.

    c. Kliknij przycisk **wykluczyć**, a następnie kliknij przycisk **wszystkich zaufanych adresów IP**.

    ![Dostęp warunkowy](./media/active-directory-conditional-access-azure-portal-get-started/12.png)

    d. Kliknij przycisk **Gotowe**.

12. Na **warunki** bloku, kliknij przycisk **gotowe**.

13. Na **nowy** bloku, aby otworzyć **Grant** bloku, w **formanty** kliknij **Grant**.

    ![Dostęp warunkowy](./media/active-directory-conditional-access-azure-portal-get-started/13.png)

14. Na **Grant** blok, wykonaj następujące czynności:

    ![Dostęp warunkowy](./media/active-directory-conditional-access-azure-portal-get-started/14.png)

    a. Wybierz **wymusić uwierzytelnianie wieloskładnikowe**.

    b. Kliknij pozycję **Wybierz**.

15. Na **nowy** bloku, w obszarze **Włącz zasady**, kliknij przycisk **na**.

    ![Dostęp warunkowy](./media/active-directory-conditional-access-azure-portal-get-started/15.png)

16. Na **nowy** bloku, kliknij przycisk **Utwórz**.


## <a name="testing-the-policy"></a>Testowanie zasad

Do testowania zasad, powinien dostęp aplikacji z urządzenia, które: 

1. Ma adres IP, który mieści się w sieci skonfigurowanych zaufanych adresów IP 

1. Ma adres IP, który nie znajduje się w sieci skonfigurowanych zaufanych adresów IP

Uwierzytelnianie wieloskładnikowe powinny być wymagana tylko podczas próby połączenia zostało utworzone z urządzenia, które nie znajduje się w sieci skonfigurowanych zaufanych adresów IP. 


## <a name="next-steps"></a>Kolejne kroki

Jeśli chcesz dowiedzieć się więcej na temat dostępu warunkowego, zobacz [dostępu warunkowego w usłudze Azure Active Directory](active-directory-conditional-access-azure-portal.md).

