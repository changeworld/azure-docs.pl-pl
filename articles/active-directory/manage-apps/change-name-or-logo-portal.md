---
title: Zmienianie nazwy lub logo aplikacji przedsiębiorstwa w usłudze Azure AD
description: Jak zmienić nazwę lub logo niestandardowej aplikacji przedsiębiorstwa w usłudze Azure Active Directory
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
ms.openlocfilehash: d23849df75d1ab239eb269b462abb21df196e7e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79138505"
---
# <a name="change-the-name-or-logo-of-an-enterprise-application-in-azure-active-directory"></a>Zmienianie nazwy lub logo aplikacji przedsiębiorstwa w usłudze Azure Active Directory

Łatwo jest zmienić nazwę lub logo niestandardowej aplikacji przedsiębiorstwa w usłudze Azure Active Directory (Azure AD). Musisz mieć odpowiednie uprawnienia do wprowadzania tych zmian i musisz być twórcą aplikacji niestandardowej.

## <a name="how-do-i-change-an-enterprise-applications-name-or-logo"></a>Jak zmienić nazwę lub logo aplikacji przedsiębiorstwa?

1. Zaloguj się do [portalu usługi Azure Active Directory](https://aad.portal.azure.com/) przy za pomocą konta, które jest administratorem globalnym dla katalogu. Zostanie wyświetlona strona **centrum administracyjnego usługi Azure Active Directory.**
2. W lewym okienku wybierz pozycję **Aplikacje dla przedsiębiorstw**. Pojawi się lista aplikacji dla przedsiębiorstw.
3. Wybierz aplikację. Zostanie wyświetlona strona przeglądu aplikacji.
4. W okienku przeglądu aplikacji w obszarze **Nagłówek Zarządzanie** wybierz pozycję **Właściwości**. Zostanie wyświetlona strona **Właściwości.**
5. Jeśli chcesz zmienić nazwę, zaznacz pole **Nazwa,** wpisz nową nazwę i naciśnij **klawisz Enter**.
6. Jeśli chcesz zmienić logo, znajdź pole **Logo** i wybierz ikonę folderu obok pola **Wybierz plik,** który znajduje się poniżej bieżącego obrazu logo aplikacji.

   ![Wybieranie polecenia właściwości](./media/change-name-or-logo-portal/change-logo.png)

   W przeciwnym razie, jeśli nie zmieniasz logo, przejdź do kroku 8.
7. W selektorze plików wybierz plik, który chcesz wybrać jako nowe logo. Nazwa pliku pojawia się w polu poniżej bieżącego obrazu logo.

   > [!NOTE]
   > Platforma Azure wymaga, aby obraz logo był plikiem PNG i stosuje limity szerokości, wysokości i rozmiaru pliku. Niestandardowe logo musi mieć dokładnie &times; rozmiar 215 215 pikseli i być w formacie PNG. Zaleca się używanie jednolitego kolorowego tła bez przezroczystości w logo aplikacji, aby były najlepiej wyświetlane użytkownikom.
8. Wybierz **pozycję Zapisz**. Jeśli wybierzesz nowe logo, obraz pola **Logo** zmieni się, aby odzwierciedlić nowy plik logo.

## <a name="next-steps"></a>Następne kroki

* [Szybki start: wyświetlanie grup i członków w usłudze Azure Active Directory w danej organizacji](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](assign-user-or-group-access-portal.md)
* [Usuwanie przypisania użytkownika lub grupy z aplikacji przedsiębiorstwa](remove-user-or-group-access-portal.md)
* [Wyłączanie logów użytkowników dla aplikacji dla przedsiębiorstwa](disable-user-sign-in-portal.md)
