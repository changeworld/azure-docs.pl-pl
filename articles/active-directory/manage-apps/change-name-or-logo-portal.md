---
title: Zmiana nazwy lub logo aplikacji przedsiębiorstwa w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Jak zmienić nazwę lub logo aplikacji niestandardowych organizacji w usłudze Azure Active Directory
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: celested
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: dbebc528227ebb06bd66e31b802dd4fd618d6a99
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64718758"
---
# <a name="change-the-name-or-logo-of-an-enterprise-application-in-azure-active-directory"></a>Zmiana nazwy lub logo aplikacji przedsiębiorstwa w usłudze Azure Active Directory

To proste zmienić nazwę lub logo aplikacji niestandardowych organizacji w usłudze Azure Active Directory (Azure AD). Musi mieć odpowiednie uprawnienia, aby wprowadzić te zmiany, a musi być twórcą aplikacji niestandardowej.

## <a name="how-do-i-change-an-enterprise-applications-name-or-logo"></a>Jak zmienić nazwę lub logo aplikacji przedsiębiorstwa

1. Zaloguj się do [portalu Azure Active Directory](https://aad.portal.azure.com/) przy użyciu konta, które jest administratorem globalnym katalogu. **Centrum administracyjne usługi Azure Active Directory** zostanie wyświetlona strona.
2. W lewym okienku wybierz pozycję **Aplikacje dla przedsiębiorstw**. Zostanie wyświetlona lista aplikacji dla przedsiębiorstwa.
3. Wybierz aplikację. Zostanie wyświetlona strona Omówienie aplikacji.
4. W okienku przeglądu aplikacji w obszarze **Zarządzaj** nagłówka, wybierz **właściwości**. **Właściwości** zostanie wyświetlona strona.
5. Jeśli chcesz zmienić nazwę, wybierz opcję **nazwa** pole, wpisz nową nazwę i naciśnij klawisz **Enter**.
6. Jeśli chcesz zmienić logo, Znajdź **Logo** pola i wybierz ikonę folderu **wybierz plik** okno, które znajduje się poniżej bieżącego obrazu logo aplikacji.

   ![Wybierając polecenie Właściwości](./media/change-name-or-logo-portal/change-logo.png)

   W przeciwnym razie jeśli nie są zmiany logo, przejdź do kroku 8.
7. W selektorze plików wybierz plik, który ma jako nowe logo. Nazwa pliku jest wyświetlana w polu poniżej bieżącego obrazu logo.

   > [!NOTE]
   > Platforma Azure wymaga obraz logo, który ma być plikiem PNG i mają zastosowanie limity na szerokość, wysokość i rozmiaru pliku.
8. Wybierz pozycję **Zapisz**. Jeśli została wybrana opcja nowe logo **Logo** pola obrazu zmieniony, aby odzwierciedlić nowy plik logo.

## <a name="next-steps"></a>Kolejne kroki

* [Szybki start: Wyświetlanie grup i elementów członkowskich organizacji w usłudze Azure Active Directory](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](assign-user-or-group-access-portal.md)
* [Usuń przypisanie użytkownika lub grupy z aplikacji przedsiębiorstwa](remove-user-or-group-access-portal.md)
* [Wyłączanie logowania użytkowników dla aplikacji przedsiębiorstwa](disable-user-sign-in-portal.md)
