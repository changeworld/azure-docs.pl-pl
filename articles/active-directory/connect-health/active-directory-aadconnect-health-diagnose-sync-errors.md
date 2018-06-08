---
title: Azure AD Connect Health - diagnozowanie zduplikowany atrybut błędy synchronizacji | Dokumentacja firmy Microsoft
description: W tym dokumencie opisano proces diagnostyki błędów synchronizacji zduplikowany atrybut i potencjalne rozwiązania scenariuszy obiektu oddzielonego bezpośrednio z portalu Azure.
services: active-directory
documentationcenter: ''
author: zhiweiw
manager: maheshu
editor: billmath
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: zhiweiw
ms.openlocfilehash: 4a6e0924492c26c9bad4ed0af207ad9764c3cc5c
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34831901"
---
# <a name="diagnose-and-remediate-duplicated-attribute-sync-errors"></a>Zdiagnozować i rozwiązać błędy synchronizacji zduplikowany atrybut.

## <a name="overview"></a>Przegląd
Biorąc dalej jeden krok, aby wyróżnić błędy synchronizacji, usługi Azure Active Directory (Azure AD) Connect Health wprowadza samodzielne skorygowanie problemu. Rozwiązuje zduplikowany atrybut błędy synchronizacji, a poprawki obiektów, które są oddzielone od usługi Azure AD.
Funkcja diagnostyki ma następujące zalety:
- Zapewnia procedury diagnostycznej, który zawęża błędy synchronizacji zduplikowany atrybut. I udostępnia określonych poprawki.
- Ma to zastosowanie poprawkę dla dedykowanych scenariuszy z usługi Azure AD, aby rozwiązać problem w jednym kroku.
- Nie uaktualnienia lub konfiguracji są wymagane do włączenia tej funkcji.
Aby uzyskać więcej informacji na temat usługi Azure AD, zobacz [tożsamości synchronizacji i zduplikowany atrybut odporności](https://aka.ms/dupattributeresdocs).

## <a name="problems"></a>Problemy
### <a name="a-common-scenario"></a>Typowy scenariusz
Gdy **QuarantinedAttributeValueMustBeUnique** i **AttributeValueMustBeUnique** błędy synchronizacji wystąpi, często, aby wyświetlić **UserPrincipalName** lub **Adresów serwerów proxy** konflikt w usłudze Azure AD. Błędy synchronizacji może rozwiązać, aktualizując powodujące konflikt obiektu źródłowego z lokalnymi strony. Błąd synchronizacji zostanie rozwiązany po następnej synchronizacji. Na przykład ten obraz oznacza, że dwóch użytkowników ma konfliktu ich **UserPrincipalName**. Oba są **Joe.J@contoso.com**. Obiekty powodujące konflikt zostały poddane kwarantannie w usłudze Azure AD.

![Diagnozowanie typowy scenariusz błędu synchronizacji](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixCommonCase.png)

### <a name="orphaned-object-scenario"></a>Scenariusz obiektu oddzielonego
Czasami może się okazać, że istniejącego użytkownika utraci **zakotwiczenie źródła**. Usuwanie obiektu źródłowego wystąpiło w lokalnej usłudze Active Directory. Jednak zmiany sygnału usunięcia nigdy nie otrzymała synchronizowane z usługi Azure AD. Ta utrata odbywa się powodów, takich jak błędy aparatu synchronizacji lub migracji domeny. Jeśli ten sam obiekt pobiera przywrócić lub ponownie utworzyć logicznie, istniejącego użytkownika powinny mieć użytkownika do synchronizowania z **zakotwiczenie źródła**. 

Istniejącego użytkownika jest obiektem tylko w chmurze, możesz również sprawdzić powodujące konflikt użytkownika synchronizowane z usługą Azure AD. Nie można dopasować użytkownika do synchronizacji do istniejącego obiektu. Nie istnieje sposób bezpośredniego można ponownie zmapować **zakotwiczenie źródła**. Zobacz więcej informacji [istniejące bazy wiedzy knowledge base](https://support.microsoft.com/help/2647098). 

Na przykład istniejący obiekt w usłudze Azure AD zachowuje licencji Jan. Nowo synchronizowany obiekt z innym **zakotwiczenie źródła** odbywa się w stanie zduplikowany atrybut w usłudze Azure AD. Zmiany dotyczące Jan w lokalnej usłudze Active Directory nie można zastosować Jana oryginalnego użytkownika (istniejący obiekt) w usłudze Azure AD.  

![Diagnozowanie scenariusz obiektu oddzielonego błędu synchronizacji](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixOrphanedCase.png)

## <a name="diagnostic-and-troubleshooting-steps-in-connect-health"></a>Diagnostyka i rozwiązywanie problemów z kroków Connect Health 
Funkcja Diagnozuj obsługuje obiekty użytkownika z następującymi atrybutami zduplikowane:

| Nazwa atrybutu | Typy błędów synchronizacji|
| ------------------ | -----------------|
| UserPrincipalName | QuarantinedAttributeValueMustBeUnique lub AttributeValueMustBeUnique | 
| ProxyAddresses | QuarantinedAttributeValueMustBeUnique lub AttributeValueMustBeUnique | 
| sipProxyAddress | AttributeValueMustBeUnique | 
| onPremiseSecurityIdentifier |  AttributeValueMustBeUnique |

>[!IMPORTANT]
> Dostępu do tej funkcji **administratora globalnego** uprawnień, lub **współautora** wymagane jest uprawnienie z ustawień RBAC.
>

Wykonaj kroki w portalu Azure, aby zawęzić szczegóły błędu synchronizacji i dostarczanie rozwiązań do bardziej szczegółowych:

![Kroki diagnozowania błędu synchronizacji](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixSteps.png)

W portalu Azure należy wykonać kilka czynności, aby zidentyfikować konkretnych możliwych do skorygowania scenariuszy:  
1.  Sprawdź **diagnozowanie stan** kolumny. Stan pokazuje, czy jest możliwe sposób, aby naprawić błąd synchronizacji bezpośrednio z usługą Azure Active Directory. Innymi słowy, rozwiązywania problemów przepływu umożliwiającą można zawęzić przypadek błędu i potencjalnie rozwiązać ten problem.
| Stan | Co to znaczy? |
| ------------------ | -----------------|
| Nie uruchomiono | Ten proces diagnostyki nie zostało to jeszcze odwiedzone. W zależności od wyniku diagnostyczne jest potencjalnych sposób, aby naprawić błąd synchronizacji bezpośrednio z portalu. |
| Wymagane jest ręczne wprowadzenie poprawki | Błąd nie mieści się kryteria dostępne poprawki z portalu. Albo konfliktu typów obiektu nie są użytkownicy, lub już nawiązaniem czynności diagnostycznych i rozwiązanie poprawka była dostępna z portalu. W drugim przypadku poprawkę z lokalnymi strony jest nadal jednym z rozwiązań. [Dowiedz się więcej na temat poprawki lokalnymi](https://support.microsoft.com/help/2647098). | 
| Oczekuje na synchronizację | Poprawka została zastosowana. Portalu oczekuje na następny cykl synchronizacji wyczyścić błąd. |
  >[!IMPORTANT]
  > W kolumnie Stan diagnostycznych spowoduje zresetowanie po każdym cyklu synchronizacji. 
  >

2.  Wybierz **Diagnozuj** przycisku w obszarze szczegółów błędu. Należy odpowiedzieć na kilka pytań i zidentyfikować szczegóły błędu synchronizacji. Odpowiedzi na pytania identyfikowania przypadków obiektu oddzielonego.

3.  Jeśli **Zamknij** na końcu diagnostyki, pojawi się przycisk nie nie szybkiej poprawki dostępnej w portalu na podstawie Twojej odpowiedzi. Można znaleźć rozwiązania wyświetlany w ostatnim kroku. Poprawki z lokalnymi są nadal rozwiązania. Wybierz **Zamknij** przycisku. Stan bieżący błąd synchronizacji zmienia się na **Ręczna poprawka wymagane**. Stan pozostaje bieżącego cyklu synchronizacji.

4.  Po zidentyfikowaniu obiektu oddzielonego case można naprawić zduplikowane atrybuty błędy synchronizacji bezpośrednio z portalu. Aby wyzwolić procesu, wybierz **zastosować napraw** przycisku. Stan bieżący aktualizacji błąd synchronizacji do **oczekujące synchronizacji**.

5.  Po następnym cyklu synchronizacji błąd powinna zostać usunięta z listy.

## <a name="how-to-answer-the-diagnosis-questions"></a>Jak odpowiedzieć na pytania diagnostyki 
### <a name="does-the-user-exist-in-your-on-premises-active-directory"></a>Użytkownik istnieje w lokalnej usługi Active Directory?

To pytanie próbuje zidentyfikować obiekt źródłowy istniejącego użytkownika z lokalnej usługi Active Directory.  
1.  Sprawdź, czy usługi Azure Active Directory zawiera obiekt z dostarczonych **UserPrincipalName**. Jeśli nie, odpowiedzi **nr**.
2.  Jeśli tak, sprawdź, czy obiekt jest nadal w zakresie do synchronizowania.  
  - Wyszukaj w przestrzeni łącznika usługi Azure AD przy użyciu nazwy domeny.
  - Jeśli obiekt znajduje się w **oczekujące dodać** stanu, odpowiedź **nr**. Azure AD Connect nie może połączyć się z obiektu do obiektu bezpośrednio w usłudze Azure AD.
  - Jeśli obiekt nie zostanie odnaleziony, należy odpowiedzieć **tak**.

W tych przykładach pytanie podejmie próbę identyfikacji czy **Jackson Jan** nadal istnieje w lokalnej usłudze Active Directory.
Dla **typowy scenariusz**, użytkownicy **Johnson Jan** i **Jackson Jan** znajdują się w lokalnej usłudze Active Directory. Poddane kwarantannie obiekty są dwóch różnych użytkowników.

![Diagnozowanie typowy scenariusz błędu synchronizacji](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixCommonCase.png)

Dla **scenariusza obiektu oddzielonego**, tylko jednego użytkownika **Johnson Jan** znajduje się w lokalnej usłudze Active Directory:

![Diagnozowanie obiektu oddzielonego błąd synchronizacji * istnieje użytkownik * scenariusz](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixOrphanedCase.png)

### <a name="do-both-of-these-accounts-belong-to-the-same-user"></a>Czy oba te konta należy do tego samego użytkownika?
To pytanie sprawdza użytkownika przychodzącego powodujące konflikt i istniejącego obiektu użytkownika w usłudze Azure AD, aby zobaczyć, jeśli należą do tego samego użytkownika.  
1.  Obiekt powodujące konflikt nowo jest synchronizowane z usługi Azure Active Directory. Porównanie obiektów atrybuty:  
  - Nazwa wyświetlana
  - Nazwa główna użytkownika
  - Identyfikator obiektu
2.  Jeśli usługi Azure AD nie może porównać je, należy sprawdzić, czy usługi Active Directory ma obiektów z dostarczonych **UserPrincipalNames**. Odpowiedzi **nr** Jeśli okaże się jednocześnie.

W poniższym przykładzie dwa obiekty należą do tego samego użytkownika **Johnson Jan**.

![Diagnozowanie obiektu oddzielonego błąd synchronizacji * tego samego użytkownika * scenariusz](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixOrphanedCase.png)


## <a name="what-happens-after-the-fix-is-applied-in-the-orphaned-object-scenario"></a>Co się stanie po zastosowaniu tej poprawki w scenariuszu obiektu oddzielonego
Zobaczysz na podstawie odpowiedzi na pytania w poprzednim, **zastosować napraw** przycisku, gdy poprawka jest dostępna z usługi Azure AD. W takim przypadku obiekt lokalnego jest synchronizowany z platformy Azure nieoczekiwany obiekt usługi Active Directory. Dwa obiekty są mapowane za pomocą **zakotwiczenie źródła**. **Zastosować napraw** zmiana zostanie te lub podobne kroki:
1. Aktualizacje **zakotwiczenie źródła** na prawidłowy obiekt w usłudze Azure AD.
2. Usuwa obiekt powodujące konflikt w usłudze Azure AD, jeśli jest obecny.

![Diagnozowanie błędów synchronizacji po poprawki](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixAfterFix.png)

>[!IMPORTANT]
> **Zastosować napraw** dotyczy jedynie w przypadku obiektu oddzielonego.
>

Po wykonaniu powyższych kroków użytkownik ma dostęp oryginalnego zasób, który jest łączem do istniejącego obiektu. **Diagnozowanie stan** wartości w widoku listy aktualizacji do **oczekujące synchronizacji**. Błąd synchronizacji zostanie rozwiązany po następnej synchronizacji. Connect będzie kondycji nie dłuższa Pokaż błąd synchronizacji rozwiązane w widoku listy.


## <a name="faq"></a>Często zadawane pytania
**PYTANIE** Co się stanie w przypadku wykonywania **zastosować napraw** zakończy się niepowodzeniem?  
**ODPOWIEDŹ** Jeśli wykonanie nie powiedzie się, istnieje możliwość, że Azure AD Connect działa Błąd eksportu. Odśwież stronę portalu i spróbuj ponownie po następnej synchronizacji. Cykl synchronizacji domyślny to 30 minut. 


**PYTANIE** Co zrobić, jeśli **istniejący obiekt** powinien być obiekt do usunięcia?  
**ODPOWIEDŹ** Jeśli **istniejący obiekt** powinny zostać usunięte, proces nie wymaga zmiany **zakotwiczenie źródła**. Zazwyczaj można poprawić z lokalnej usługi Active Directory. 


**PYTANIE** Jakie uprawnienia czy użytkownik musi zastosować poprawkę?  
**ODPOWIEDŹ** **Administrator globalny**, lub **współautora** od ustawień RBAC, ma uprawnienia umożliwiające dostęp dane diagnostyczne i proces rozwiązywania problemów.


**PYTANIE** Należy ponownie skonfigurować Azure AD Connect lub zaktualizuj agenta programu Azure AD Connect Health dla tej funkcji?  
**ODPOWIEDŹ** Nie, proces diagnostyki jest pełną funkcją oparte na chmurze.


**PYTANIE** Jeśli istniejący obiekt jest delikatny usunięte, będzie proces diagnostyki uaktywnić obiekt ponownie?  
**ODPOWIEDŹ** Nie, poprawki nie zaktualizować atrybutów obiektu innych niż **zakotwiczenie źródła**.
