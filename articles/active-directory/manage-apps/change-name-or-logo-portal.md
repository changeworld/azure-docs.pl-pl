---
title: Zmiana nazwy lub logo aplikacji dla przedsiębiorstw w usłudze Azure AD
description: Jak zmienić nazwę lub logo niestandardowej aplikacji dla przedsiębiorstw w Azure Active Directory
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3dfe0f8788275dd4403b6c9cad99a8eb09e479b
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74274711"
---
# <a name="change-the-name-or-logo-of-an-enterprise-application-in-azure-active-directory"></a>Zmień nazwę lub logo aplikacji przedsiębiorstwa w Azure Active Directory

Można łatwo zmienić nazwę lub logo niestandardowej aplikacji dla przedsiębiorstw w Azure Active Directory (Azure AD). Musisz mieć odpowiednie uprawnienia, aby wprowadzić te zmiany, i musisz być twórcą aplikacji niestandardowej.

## <a name="how-do-i-change-an-enterprise-applications-name-or-logo"></a>Jak mogę zmienić nazwę lub logo aplikacji firmowej?

1. Zaloguj się do [portalu Azure Active Directory](https://aad.portal.azure.com/) przy użyciu konta, które jest administratorem globalnym katalogu. Zostanie wyświetlona strona **Centrum administracyjne Azure Active Directory** .
2. W lewym okienku wybierz pozycję **Aplikacje dla przedsiębiorstw**. Zostanie wyświetlona lista aplikacji przedsiębiorstwa.
3. Wybierz aplikację. Zostanie wyświetlona strona przegląd aplikacji.
4. W okienku Przegląd aplikacji w obszarze **Zarządzanie** wybierz pozycję **Właściwości**. Zostanie wyświetlona strona **Właściwości** .
5. Jeśli chcesz zmienić nazwę, zaznacz pole **Nazwa** , wpisz nową nazwę i naciśnij klawisz **Enter**.
6. Jeśli chcesz zmienić logo, Znajdź pole **logo** i wybierz ikonę folderu obok pola **Wybierz plik** , który znajduje się poniżej bieżącego obrazu logo aplikacji.

   ![Wybieranie polecenia właściwości](./media/change-name-or-logo-portal/change-logo.png)

   W przeciwnym razie, jeśli nie zmienisz logo, przejdź do kroku 8.
7. W selektorze plików wybierz plik, który chcesz utworzyć jako nowe logo. Nazwa pliku zostanie wyświetlona w polu poniżej obrazu bieżącego logo.

   > [!NOTE]
   > Platforma Azure wymaga, aby obraz logo był plikiem PNG i ma ograniczenia dotyczące szerokości, wysokości i rozmiaru pliku.
8. Wybierz pozycję **Zapisz**. Jeśli wybrano nowe logo, obraz pola **logo** zostanie zmieniony w celu odzwierciedlenia nowego pliku logo.

## <a name="next-steps"></a>Następne kroki

* [Szybki Start: wyświetlanie grup i członków organizacji w Azure Active Directory](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Przypisywanie użytkownika lub grupy do aplikacji dla przedsiębiorstw](assign-user-or-group-access-portal.md)
* [Usuwanie przypisania użytkownika lub grupy z aplikacji dla przedsiębiorstw](remove-user-or-group-access-portal.md)
* [Wyłączanie logowania użytkownika dla aplikacji dla przedsiębiorstw](disable-user-sign-in-portal.md)
