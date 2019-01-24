---
title: Jak rozwiązywać problemy z logowania w raportach usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy z logowania w raportach usługi Azure Active Directory w witrynie Azure portal
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: f39557b21e8c54f565ddbb6764d697968d3bbee6
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54823771"
---
# <a name="how-to-troubleshoot-sign-in-errors-using-azure-active-directory-reports"></a>Instrukcje: Rozwiązywanie problemów z logowania w raportach usługi Azure Active Directory

[Raporcie logowań](concept-sign-ins.md) w usłudze Azure Active Directory (Azure AD) umożliwia znalezienie odpowiedzi na pytania dotyczące zarządzania dostępem do aplikacji w organizacji, w tym:

- Co to jest wzorzec logowania użytkownika?
- Ilu użytkowników zalogowało się w ciągu tygodnia?
- Jaki jest stan tych logowań?


Ponadto w raporcie logowań może również pomóc rozwiązać logowań dla użytkowników w Twojej organizacji. W tym przewodniku dowiesz się, jak izolować błąd logowania w raporcie logowań i użyj go, aby poznać główną przyczynę niepowodzenia.

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebne elementy:

* Dzierżawa usługi Azure AD z licencją premium (P1/P2). Aby uaktualnić swoją wersję usługi Azure Active Directory, zobacz [Wprowadzenie do usługi Azure Active Directory w wersji Premium](../fundamentals/active-directory-get-started-premium.md).
* Użytkownik mający rolę **administratora globalnego**, **administratora zabezpieczeń**, **czytelnika zabezpieczeń** lub **czytelnika raportów** dla dzierżawy. Ponadto każdy użytkownik może uzyskiwać dostęp do informacji o swoim logowaniu. 

## <a name="troubleshoot-sign-in-errors-using-the-sign-ins-report"></a>Rozwiązywanie problemów z logowania za pomocą funkcji Zgłoś logowania

1. Przejdź do witryny [Azure Portal](https://portal.azure.com) i wybierz swój katalog.
2. Wybierz pozycję **Azure Active Directory**, a następnie pozycję **Logowania** w sekcji **Monitorowanie**. 
3. Użyj filtrów podane, aby zawęzić awarii, albo według nazwy użytkownika lub identyfikatorem obiektu, nazwa aplikacji lub daty. Ponadto wybierz **błąd** z **stan** listy rozwijanej do wyświetlania tylko Nieudane logowania. 

    ![Filtrowanie wyników](./media/howto-troubleshoot-sign-in-errors/filters.png)
        
4. Zidentyfikuj Nieudane logowania, którą chcesz zbadać, a następnie wybierz ją. Spowoduje to otwarcie okna dodatkowe szczegóły o więcej informacji na temat logowania nie powiodło się. Zanotuj **logowania kod błędu:** i **Przyczyna niepowodzenia**. 

    ![Wybierz rekord](./media/howto-troubleshoot-sign-in-errors/sign-in-failures.png)
        
5. Można również znaleźć te informacje w **Rozwiązywanie problemów i pomoc techniczna** karta w oknie Szczegóły.

    ![Rozwiązywanie problemów i pomoc techniczna](./media/howto-troubleshoot-sign-in-errors/troubleshooting-and-support.png)

6. Przyczyna niepowodzenia opisuje błąd. Na przykład w powyższym scenariuszu Usługa Przyczyna niepowodzenia jest **nieprawidłowej nazwy użytkownika lub hasła lub nieprawidłowa nazwa użytkownika w środowisku lokalnym lub hasła**. Oznacza to, że użytkownik wprowadził niepoprawna nazwa użytkownika lub hasło, aby zalogować się do witryny Azure portal. Poprawka jest po prostu Zaloguj się ponownie, podając prawidłową nazwę użytkownika i hasło.

7. Dodatkowe informacje, w tym koncepcji korygowania, szukając kodu błędu, można uzyskać **50126** w tym przykładzie w [odwołania kody błędów logowania](reference-sign-ins-error-codes.md). 

8. Wszystkie inne nie powiedzie się, czy problem nadal występuje mimo podjęcia zalecany sposób postępowania, [Otwórz bilet pomocy technicznej](../fundamentals/active-directory-troubleshooting-support-howto.md) czynności opisane w **Rozwiązywanie problemów i pomoc techniczna** kartę. 

## <a name="next-steps"></a>Kolejne kroki

* [Odwoływać się do kodów błędów logowania](reference-sign-ins-error-codes.md)
* [Omówienie raportów logowania](concept-sign-ins.md)
