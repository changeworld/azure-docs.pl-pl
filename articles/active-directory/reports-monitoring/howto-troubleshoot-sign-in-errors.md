---
title: Jak rozwiązywać problemy z raportami o błędach logowania | Dokumenty firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy z błędami logowania przy użyciu raportów usługi Azure Active Directory w witrynie Azure portal
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec5fe7f62e8537a7f687202d365eb37d43b48b78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74008058"
---
# <a name="how-to-troubleshoot-sign-in-errors-using-azure-active-directory-reports"></a>Jak: rozwiązywanie problemów z błędami logowania przy użyciu raportów usługi Azure Active Directory

[Raport logowania w usłudze](concept-sign-ins.md) Azure Active Directory (Azure AD) umożliwia znajdowanie odpowiedzi na pytania dotyczące zarządzania dostępem do aplikacji w organizacji, w tym:

- Co to jest wzorzec logowania użytkownika?
- Ilu użytkowników zalogowało się w ciągu tygodnia?
- Jaki jest stan tych logowań?


Ponadto raport logowania może również pomóc w rozwiązywaniu problemów z błędami logowania dla użytkowników w organizacji. W tym przewodniku dowiesz się, jak wyizolować błąd logowania w raporcie logowania i użyć go do zrozumienia głównej przyczyny błędu.

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebne elementy:

* Dzierżawa usługi Azure AD z licencją premium (P1/P2). Aby uaktualnić swoją wersję usługi Azure Active Directory, zobacz [Wprowadzenie do usługi Azure Active Directory w wersji Premium](../fundamentals/active-directory-get-started-premium.md).
* Użytkownik, który jest administratorem **globalnym,** **administratorem zabezpieczeń,** **czytnikiem zabezpieczeń**lub **funkcją czytnika raportów** dla dzierżawy. Ponadto każdy użytkownik może uzyskiwać dostęp do informacji o swoim logowaniu. 

## <a name="troubleshoot-sign-in-errors-using-the-sign-ins-report"></a>Rozwiązywanie problemów z błędami logowania przy użyciu raportu logowania

1. Przejdź do witryny [Azure Portal](https://portal.azure.com) i wybierz swój katalog.
2. Wybierz pozycję **Azure Active Directory**, a następnie pozycję **Logowania** w sekcji **Monitorowanie**. 
3. Użyj podanych filtrów, aby zawęzić błąd przez identyfikator nazwy użytkownika lub obiektu, nazwę aplikacji lub datę. Ponadto wybierz **błąd** z listy rozwijanej **Stan,** aby wyświetlić tylko nieudane logowania. 

    ![Filtrowanie wyników](./media/howto-troubleshoot-sign-in-errors/filters.png)
        
4. Zidentyfikuj nieudane logowanie, które chcesz zbadać. Wybierz go, aby otworzyć okno dodatkowych szczegółów z więcej informacji na temat nieudanego logowania. Zanotuj **kod błędu logowania** i **przyczynę błędu**. 

    ![Wybierz rekord](./media/howto-troubleshoot-sign-in-errors/sign-in-failures.png)
        
5. Informacje te można również znaleźć na karcie **Rozwiązywanie problemów i pomoc techniczna** w oknie szczegółów.

    ![Rozwiązywanie problemów i pomoc techniczna](./media/howto-troubleshoot-sign-in-errors/troubleshooting-and-support.png)

6. Przyczyna awarii opisuje błąd. Na przykład w powyższym scenariuszu przyczyną błędu jest **Nieprawidłowa nazwa użytkownika lub hasło lub Nieprawidłowa lokalna nazwa użytkownika lub hasło**. Poprawka polega na tym, aby po prostu zalogować się ponownie przy użyciu poprawnej nazwy użytkownika i hasła.

7. Dodatkowe informacje, w tym pomysły na rozwiązanie, można uzyskać, wyszukując kod błędu **50126** w tym przykładzie, w [odwołaniu do kodów błędów logowania](reference-sign-ins-error-codes.md). 

8. Jeśli wszystko inne nie powiedzie się lub problem będzie się powtarzał pomimo podjęcia zalecanego przebiegu działania, [otwórz bilet pomocy technicznej,](../fundamentals/active-directory-troubleshooting-support-howto.md) wykonując kroki opisane w karcie **Rozwiązywanie problemów i pomoc techniczna.** 

## <a name="next-steps"></a>Następne kroki

* [Odwołanie do kodów błędów logowania](reference-sign-ins-error-codes.md)
* [Omówienie raportu logowania](concept-sign-ins.md)
