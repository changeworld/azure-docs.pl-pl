---
title: Program Azure AD Connect Health — diagnozowanie błędów synchronizacji atrybut zduplikowany | Dokumentacja firmy Microsoft
description: W tym dokumencie opisano proces diagnozowania błędów synchronizacji zduplikowanym atrybutem i potencjalnych awarii scenariuszy obiektu oddzielonego bezpośrednio w witrynie Azure portal.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: maheshu
editor: billmath
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: zhiweiw
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbdeef7c591221756ad206bf2f3dd78ac3d26c4f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60349985"
---
# <a name="diagnose-and-remediate-duplicated-attribute-sync-errors"></a>Zdiagnozować i rozwiązać błędy synchronizacji zduplikowanym atrybutem

## <a name="overview"></a>Omówienie
Biorąc o krok dalej, aby wyróżnić błędy synchronizacji, usługi Azure Active Directory (Azure AD) Connect Health wprowadza samodzielne rozwiązanie problemu. Ona rozwiązuje błędy synchronizacji zduplikowanym atrybutem i poprawki obiektów, które są oddzielone od usługi Azure AD.
Funkcja diagnostyki ma następujące korzyści:
- Zapewnia procedury diagnostycznej, który zawęża zduplikowanym atrybutem błędy synchronizacji. A także zapewnia określone poprawki.
- Ma to zastosowanie poprawki dla dedykowanych scenariuszy z usługi Azure AD, aby rozwiązać problem w jednym kroku.
- Nie uaktualnienia lub konfiguracja jest wymagana, aby włączyć tę funkcję.
Aby uzyskać więcej informacji na temat usługi Azure AD, zobacz [tożsamości synchronizacji i odporności zduplikowanego atrybutu](how-to-connect-syncservice-duplicate-attribute-resiliency.md).

## <a name="problems"></a>Problemy
### <a name="a-common-scenario"></a>Typowy scenariusz
Gdy **QuarantinedAttributeValueMustBeUnique** i **AttributeValueMustBeUnique** wystąpi błędy synchronizacji, jest częściej można zobaczyć **UserPrincipalName** lub **Adresy serwerów proxy** konflikt w usłudze Azure AD. Błędy synchronizacji może rozwiązać, aktualizując powodujące konflikt obiektu źródłowego ze strony w środowisku lokalnym. Błąd synchronizacji zostanie rozwiązany po następnej synchronizacji. Na przykład, ten obraz informuje, że dwóch użytkowników w przypadku konfliktu ich **UserPrincipalName**. Oba są **Joe.J\@contoso.com**. Obiekty powodujące konflikt, zostały poddane kwarantannie w usłudze Azure AD.

![Diagnozowanie scenariusz typowych błędów synchronizacji](./media/how-to-connect-health-diagnose-sync-errors/IIdFixCommonCase.png)

### <a name="orphaned-object-scenario"></a>Scenariusz obiektu oddzielonego
Czasami może się okazać, że traci istniejącego użytkownika **zakotwiczenie źródła**. Usunięcie obiektu źródłowego wydarzyło się w usłudze Active Directory w środowisku lokalnym. Ale zmiana usunięcia sygnału stało się nigdy nie synchronizować z usługą Azure AD. Ta utrata odbywa się powodów, takich jak problemy z synchronizacją aparat lub migracji domeny. Podczas tego samego obiektu pobiera przywrócić lub ponownie utworzyć logicznie istniejącego użytkownika powinna być użytkownika do synchronizowania z **zakotwiczenie źródła**. 

W przypadku istniejącego użytkownika jest obiektem tylko w chmurze, również widać powodujące konflikt użytkownika synchronizowane z usługą Azure AD. Nie można dopasować użytkownika do synchronizacji do istniejącego obiektu. Nie ma bezpośredniego sposobu ponownie zmapować **zakotwiczenie źródła**. Zobacz więcej informacji o [istniejącej bazie wiedzy knowledge base](https://support.microsoft.com/help/2647098). 

Na przykład istniejący obiekt w usłudze Azure AD zachowuje licencji Jan. Obiekt nowo zsynchronizowany z innym **zakotwiczenie źródła** występuje w stanie zduplikowanym atrybutem w usłudze Azure AD. Zmiany dotyczące Jan w usłudze Active Directory w środowisku lokalnym nie zostanie zastosowany do użytkownika oryginalnego Jana (istniejący obiekt) w usłudze Azure AD.  

![Diagnozowanie scenariusz obiektu oddzielonego błąd synchronizacji](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)

## <a name="diagnostic-and-troubleshooting-steps-in-connect-health"></a>Diagnostyka i rozwiązywanie problemów w programie Connect Health 
Funkcja diagnozowanie obsługuje obiekty użytkownika z następującymi atrybutami zduplikowane:

| Nazwa atrybutu | Typy błędów synchronizacji|
| ------------------ | -----------------|
| UserPrincipalName | QuarantinedAttributeValueMustBeUnique lub AttributeValueMustBeUnique | 
| ProxyAddresses | QuarantinedAttributeValueMustBeUnique lub AttributeValueMustBeUnique | 
| SipProxyAddress | AttributeValueMustBeUnique | 
| OnPremiseSecurityIdentifier |  AttributeValueMustBeUnique |

>[!IMPORTANT]
> Aby skorzystać z tej funkcji **administratora globalnego** uprawnienia, lub **Współautor** wymagane jest uprawnienie z ustawień kontroli RBAC usługi.
>

Postępuj zgodnie z instrukcjami w witrynie Azure portal, aby zawęzić szczegóły błędu synchronizacji i podaj bardziej specyficznych rozwiązań:

![Kroki diagnozy błędu synchronizacji](./media/how-to-connect-health-diagnose-sync-errors/IIdFixSteps.png)

W witrynie Azure portal należy wykonać kilka kroków, aby zidentyfikować konkretne scenariusze naprawić:  
1.  Sprawdź **diagnozowania stanu** kolumny. Stan pokazuje, czy istnieje możliwy sposób naprawić błąd synchronizacji bezpośrednio z usługi Azure Active Directory. Innymi słowy, rozwiązywania problemów z przepływem istnieje, który można zawęzić przypadki błędów i potencjalnie go naprawić.

| Stan | Co to oznacza? |
| ------------------ | -----------------|
| Nie uruchomiono | Jeszcze nie odwiedził tego procesu diagnostyki. W zależności od wyniki diagnostyki istnieje potencjalne sposób, aby naprawić błąd synchronizacji bezpośrednio z portalu. |
| Wymagane jest ręczne wprowadzenie poprawki | Ten błąd nie mieści się kryteria dostępne poprawki z poziomu portalu. Albo wybierane powodujące konflikt nie użytkowników, lub już przeszło czynności diagnostycznych i podejmowania żadnych działań poprawka była dostępna z poziomu portalu. W tym ostatnim przypadku poprawkę ze strony w środowisku lokalnym jest nadal jedno z rozwiązań. [Dowiedz się więcej na temat poprawek lokalnych](https://support.microsoft.com/help/2647098). | 
| Oczekiwanie na synchronizację | Poprawka została zastosowana. Portal czeka na następny cykl synchronizacji wyczyścić błąd. |

  >[!IMPORTANT]
  > W kolumnie Stan diagnostyczne zostaną zresetowane po każdym cyklu synchronizacji. 
  >

1. Wybierz **diagnozowanie** przycisku w obszarze Szczegóły błędu. Będzie Odpowiedz na kilka pytań, a następnie Zidentyfikuj informacje o błędzie synchronizacji. Odpowiedzi na pytania pomagać w identyfikacji przypadek obiektu oddzielonego.

1. Jeśli **Zamknij** przycisk pojawia się na końcu diagnostyki, Brak dostępnej nie szybkiej poprawki z poziomu portalu na podstawie Twojej odpowiedzi. Zapoznaj się z rozwiązaniem wyświetlane w ostatnim kroku. Poprawki ze środowiska lokalnego są nadal rozwiązania. Wybierz **Zamknij** przycisku. Stan bieżący błąd synchronizacji zmienia się na **wymagane jest ręczne wprowadzenie poprawki**. Stan pozostaje podczas bieżącego cyklu synchronizacji.

1. Po identyfikacji przypadek obiektu oddzielonego można naprawić zduplikowane atrybuty błędy synchronizacji bezpośrednio z portalu. Aby wyzwolić ten proces, wybierz **Zastosuj poprawkę** przycisku. Stan bieżącej aktualizacji błąd synchronizacji do **oczekująca synchronizacja**.

1. Po następnym cyklu synchronizacji błąd powinny zostać usunięte z listy.

## <a name="how-to-answer-the-diagnosis-questions"></a>Jak odpowiedzieć na pytania diagnostyki 
### <a name="does-the-user-exist-in-your-on-premises-active-directory"></a>Czy użytkownik istnieje w usłudze Active Directory w środowisku lokalnym?

To pytanie podejmie próbę identyfikacji źródła obiektu istniejącego użytkownika z usługi Active Directory w środowisku lokalnym.  
1. Sprawdź, czy usługi Azure Active Directory zawiera obiekt z podanych **UserPrincipalName**. Jeśli nie, odpowiedz **nie**.
2. Jeśli tak jest, sprawdź, czy obiekt jest nadal w zakresie do synchronizacji.  
   - Wyszukiwanie w obszarze łącznika usługi Azure AD przy użyciu nazwę Wyróżniającą.
   - Jeśli obiekt znajduje się w **oczekiwanie na dodanie** stanu, odpowiedź **nie**. Program Azure AD Connect nie może połączyć się z obiektu do obiektu bezpośrednio w usłudze Azure AD.
   - Jeśli obiekt nie zostanie znaleziona, odpowiedz **tak**.

W tych przykładach pytanie próbuje zidentyfikować czy **Jackson Jan** nadal istnieje w usłudze Active Directory w środowisku lokalnym.
Dla **typowy scenariusz**, użytkownicy **Johnson Jan** i **Jackson Jan** znajdują się w usłudze Active Directory w środowisku lokalnym. Poddane kwarantannie obiekty są dwóch różnych użytkowników.

![Diagnozowanie scenariusz typowych błędów synchronizacji](./media/how-to-connect-health-diagnose-sync-errors/IIdFixCommonCase.png)

Aby uzyskać **scenariusza obiektu oddzielonego**, tylko jednego użytkownika **Johnson Jan** znajduje się w usłudze Active Directory w środowisku lokalnym:

![Diagnozowanie obiektu oddzielonego błąd synchronizacji * istnieje użytkownik * scenariusza](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)

### <a name="do-both-of-these-accounts-belong-to-the-same-user"></a>Czy obie te konta należą do tego samego użytkownika?
To pytanie sprawdza użytkownika przychodzącego powodujących konflikt i do istniejącego obiektu użytkownika w usłudze Azure AD, aby zobaczyć, jeśli należą do tego samego użytkownika.  
1. Obiekt powodujący konflikt nowo jest synchronizowana z usługą Azure Active Directory. Porównaj atrybuty obiektów:  
   - Nazwa wyświetlana
   - Nazwa główna użytkownika
   - Identyfikator obiektu
2. W przypadku niepowodzenia usługi Azure AD w celu porównania ich Sprawdź, czy usługi Active Directory zawiera obiekty z podanych **userPrincipalName**. Odpowiedź **nie** Jeśli okaże się jednocześnie.

W poniższym przykładzie dwa obiekty należą do tego samego użytkownika **Johnson Jan**.

![Diagnozowanie obiektu oddzielonego błąd synchronizacji * ten sam scenariusz użytkownika *](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)


## <a name="what-happens-after-the-fix-is-applied-in-the-orphaned-object-scenario"></a>Co się stanie po zastosowaniu poprawki w scenariuszu obiektu oddzielonego
Na podstawie odpowiedzi na pytania poprzedni, zostanie wyświetlony **Zastosuj poprawkę** przycisku poprawka jest dostępna z usługi Azure AD. W tym przypadku obiekt lokalny jest synchronizowany z platformy Azure nieoczekiwany obiektu usługi AD. Oba obiekty są zamapowane przy użyciu **zakotwiczenie źródła**. **Zastosuj poprawkę** zmiana ta zajmuje te lub podobne kroki:
1. Aktualizacje **zakotwiczenie źródła** poprawny obiekt w usłudze Azure AD.
2. Usuwa obiekt powodujący konflikt w usłudze Azure AD, jeśli jest obecny.

![Diagnozowanie błędów synchronizacji po poprawki](./media/how-to-connect-health-diagnose-sync-errors/IIdFixAfterFix.png)

>[!IMPORTANT]
> **Zastosuj poprawkę** zmiana ma zastosowanie tylko do obiektu oddzielonego przypadków.
>

Po wykonaniu poprzednich kroków użytkownik ma dostęp oryginalny zasób, który stanowi link do istniejącego obiektu. **Diagnozowania stanu** wartości w widoku listy aktualizacji do **oczekiwanie na synchronizację**. Błąd synchronizacji zostanie rozwiązany po następnej synchronizacji. Connect będzie kondycji nie dłużej Pokaż błąd synchronizacji rozwiązane w widoku listy.

## <a name="failures-and-error-messages"></a>Błędy i komunikaty o błędach
**Użytkownik z atrybutem sprzecznych jest nietrwale usunięte w usłudze Azure Active Directory. Upewnij się, że użytkownik jest trudna usunięty przed ponownych prób.**  
Powinny być czyszczone użytkownika z atrybutem sprzecznych w usłudze Azure AD, przed zainstalowaniem poprawki. Zapoznaj się z [jak usunąć użytkownika trwale w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-restore) przed ponowną próbą poprawki. Użytkownik będzie również automatycznie usunięte stałe po upływie 30 dni w stanie usunięty nietrwale. 

**Trwa aktualizowanie zakotwiczenie źródła do użytkowników w dzierżawie usługi w chmurze nie jest obsługiwane.**  
Użytkowników w chmurze w usłudze Azure AD nie powinna mieć zakotwiczenie źródła. Trwa aktualizowanie zakotwiczenia źródła nie jest obsługiwana w tym przypadku. Ręczne wprowadzenie poprawki z jest wymagana w środowisku lokalnym. 

## <a name="faq"></a>Często zadawane pytania
**PYTANIE** Co się stanie w przypadku wykonywania **Zastosuj poprawkę** zakończy się niepowodzeniem?  
**ODPOWIEDŹ** Jeśli wykonanie nie powiedzie się, istnieje możliwość, że program Azure AD Connect działa na błąd eksportowania. Odśwież stronę portalu, a następnie ponów próbę po następnej synchronizacji. Cykl synchronizacji domyślny to 30 minut. 


**PYTANIE** Co zrobić, jeśli **istniejący obiekt** powinien być obiekt do usunięcia?  
**ODPOWIEDŹ** Jeśli **istniejący obiekt** powinny być usunięte, proces nie wymaga zmiany **zakotwiczenie źródła**. Zwykle możesz ją naprawić z usługi Active Directory w środowisku lokalnym. 


**PYTANIE** Jakich uprawnień czy użytkownikowi należy zastosować poprawkę?  
**ODPOWIEDŹ** **Administrator globalny**, lub **Współautor** z ustawień kontroli RBAC usługi ma uprawnienia dostępu do diagnostyki i rozwiązywania problemów z procesem.


**PYTANIE** Czy mam skonfigurować program Azure AD Connect lub zaktualizować agenta programu Azure AD Connect Health dla tej funkcji?  
**ODPOWIEDŹ** Nie, proces diagnostyki jest pełną funkcją oparte na chmurze.


**PYTANIE** Jeśli istniejący obiekt jest nietrwale usunięte, będzie procesu diagnostyki uaktywnić obiekt ponownie?  
**ODPOWIEDŹ** Nie, poprawki nie będzie aktualizować atrybuty obiektu innego niż **zakotwiczenie źródła**.
