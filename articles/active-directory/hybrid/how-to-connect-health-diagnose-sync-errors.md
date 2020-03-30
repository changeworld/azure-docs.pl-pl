---
title: Kondycja usługi Azure AD Connect — diagnozowanie błędów synchronizacji zduplikowanych atrybutów | Dokumenty firmy Microsoft
description: W tym dokumencie opisano proces diagnozowania zduplikowanych błędów synchronizacji atrybutów i potencjalną poprawkę scenariuszy osieroconych obiektów bezpośrednio z witryny Azure portal.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: maheshu
editor: billmath
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48ed9abf3e088e2581a3dd81b7c89e6b99da3ceb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76897199"
---
# <a name="diagnose-and-remediate-duplicated-attribute-sync-errors"></a>Diagnozowanie i naprawianie błędów synchronizacji zduplikowanego atrybutu

## <a name="overview"></a>Omówienie
O krok dalej, aby wyróżnić błędy synchronizacji, usługa Azure Active Directory (Azure AD) Connect Health wprowadza samoobsługowe korygowanie. Rozwiązuje błędy synchronizacji zduplikowanych atrybutów i naprawia obiekty, które są oddzielone od usługi Azure AD.
Funkcja diagnozy ma następujące zalety:
- Zapewnia procedurę diagnostyczną, która zawęża błędy synchronizacji zduplikowanych atrybutów. I daje konkretne poprawki.
- Stosuje poprawkę dla dedykowanych scenariuszy z usługi Azure AD, aby rozwiązać błąd w jednym kroku.
- Aby włączyć tę funkcję, nie jest wymagane uaktualnienie ani konfiguracja.
Aby uzyskać więcej informacji na temat usługi Azure AD, zobacz [Synchronizacja tożsamości i odporność atrybutów duplikatów](how-to-connect-syncservice-duplicate-attribute-resiliency.md).

## <a name="problems"></a>Problemy
### <a name="a-common-scenario"></a>Typowy scenariusz
Gdy **quarantinedAttributeValueMustBeUnique** i **AttributeValueMustBeUnique** błędy synchronizacji się zdarzyć, często, aby zobaczyć **UserPrincipalName** lub **adresy proxy** konflikt w usłudze Azure AD. Błędy synchronizacji można rozwiązać, aktualizując obiekt źródłowy powodujący konflikt z lokalnego boku. Błąd synchronizacji zostanie rozwiązany po następnej synchronizacji. Na przykład ten obraz wskazuje, że dwóch użytkowników ma konflikt ich **UserPrincipalName**. Oba są **Joe.J\@contoso.com**. Obiekty powodujące konflikt są poddawane kwarantannie w usłudze Azure AD.

![Diagnozowanie typowy scenariusz błędu synchronizacji](./media/how-to-connect-health-diagnose-sync-errors/IIdFixCommonCase.png)

### <a name="orphaned-object-scenario"></a>Scenariusz osieroconego obiektu
Czasami może się okazać, że istniejący użytkownik traci **źródło kotwicy**. Usunięcie obiektu źródłowego miało miejsce w lokalnej usłudze Active Directory. Ale zmiana sygnału usuwania nigdy nie została zsynchronizowana z usługą Azure AD. Ta utrata dzieje się z powodów, takich jak problemy z aparatem synchronizacji lub migracji domeny. Gdy ten sam obiekt zostanie przywrócony lub odtworzony, logicznie, istniejący użytkownik powinien być użytkownikiem do synchronizacji z **kotwicy źródłowej**. 

Gdy istniejący użytkownik jest obiektem tylko w chmurze, można również zobaczyć użytkownika powodującego konflikt zsynchronizowany z usługą Azure AD. Nie można dopasować użytkownika w synchronizacji z istniejącym obiektem. Nie ma bezpośredniego sposobu na ponowne mapowanie **kotwicy źródłowej**. Zobacz więcej informacji o [istniejącej bazie wiedzy](https://support.microsoft.com/help/2647098). 

Na przykład istniejący obiekt w usłudze Azure AD zachowuje licencję Joe. Nowo zsynchronizowany obiekt z inną **kotwicą źródła** występuje w stanie zduplikowanego atrybutu w usłudze Azure AD. Zmiany dla Joe w lokalnej usłudze Active Directory nie zostaną zastosowane do oryginalnego użytkownika Joe (istniejący obiekt) w usłudze Azure AD.  

![Diagnozowanie scenariusza osieroconych obiektów o błędzie synchronizacji](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)

## <a name="diagnostic-and-troubleshooting-steps-in-connect-health"></a>Kroki diagnostyczne i rozwiązywanie problemów w trybie Connect Health 
Funkcja diagnozowania obsługuje obiekty użytkownika z następującymi zduplikowanymi atrybutami:

| Nazwa atrybutu | Typy błędów synchronizacji|
| ------------------ | -----------------|
| UserPrincipalName | QuarantinedAttributeValueMustBeUnique lub AttributeValueMustBeUnique | 
| ProxyAddresses | QuarantinedAttributeValueMustBeUnique lub AttributeValueMustBeUnique | 
| SipProxyAddress (SipProxyAddress) | AtrybutValueMustBeUnique | 
| OnPremiseSecurityIdentifier |  AtrybutValueMustBeUnique |

>[!IMPORTANT]
> Aby uzyskać dostęp do tej funkcji, wymagane jest **uprawnienie Administrator globalny** lub **Uprawnienie współautora** z ustawień RBAC.
>

Wykonaj kroki z witryny Azure Portal, aby zawęzić szczegóły błędu synchronizacji i zapewnić bardziej szczegółowe rozwiązania:

![Kroki diagnostyki błędów synchronizacji](./media/how-to-connect-health-diagnose-sync-errors/IIdFixSteps.png)

W witrynie Azure portal należy wykonać kilka kroków w celu zidentyfikowania określonych scenariuszy, które można naprawić:  
1.  Sprawdź kolumnę **Diagnozuj stan.** Stan pokazuje, czy istnieje możliwy sposób naprawienia błędu synchronizacji bezpośrednio z usługi Azure Active Directory. Innymi słowy istnieje przepływ rozwiązywania problemów, który można zawęzić przypadek błędu i potencjalnie go naprawić.

| Stan | Co to oznacza? |
| ------------------ | -----------------|
| Nie rozpoczęto | Nie odwiedziłeś tego procesu diagnozy. W zależności od wyniku diagnostycznego istnieje potencjalny sposób naprawienia błędu synchronizacji bezpośrednio z portalu. |
| Wymagana poprawka ręczna | Błąd nie pasuje do kryteriów dostępnych poprawek z portalu. Typy obiektów powodujące konflikt nie są użytkownikami lub już przeszedłeś kroki diagnostyczne i nie było dostępne żadne rozwiązanie poprawki z portalu. W tym ostatnim przypadku poprawka od strony lokalnej jest nadal jednym z rozwiązań. [Dowiedz się więcej o poprawkach lokalnych](https://support.microsoft.com/help/2647098). | 
| Oczekująca synchronizacja | Zastosowano poprawkę. Portal oczekuje na następny cykl synchronizacji, aby usunąć błąd. |

  >[!IMPORTANT]
  > Kolumna stanu diagnostyki zostanie zresetowana po każdym cyklu synchronizacji. 
  >

1. Wybierz przycisk **Diagnozuj** pod szczegółami błędu. Odpowiesz na kilka pytań i zidentyfikujesz szczegóły błędu synchronizacji. Odpowiedzi na pytania pomagają zidentyfikować przypadek osieroconego obiektu.

1. Jeśli przycisk **Zamknij** pojawi się na końcu diagnostyki, nie ma szybkiej poprawki dostępnej w portalu na podstawie odpowiedzi. Zapoznaj się z rozwiązaniem pokazanym w ostatnim kroku. Poprawki z lokalnych są nadal rozwiązania. Wybierz przycisk **Zamknij.** Stan bieżącego błędu synchronizacji przełącza się na **Ręczna poprawka wymagana**. Stan pozostaje podczas bieżącego cyklu synchronizacji.

1. Po zidentyfikowaniu sprawy osieroconego obiektu można naprawić błędy synchronizacji zduplikowanych atrybutów bezpośrednio z portalu. Aby wyzwolić proces, wybierz przycisk **Zastosuj poprawkę.** Stan bieżącego błędu synchronizacji jest aktualizowany **na synchronizację oczekującą**.

1. Po następnym cyklu synchronizacji błąd powinien zostać usunięty z listy.

## <a name="how-to-answer-the-diagnosis-questions"></a>Jak odpowiedzieć na pytania diagnostyczne 
### <a name="does-the-user-exist-in-your-on-premises-active-directory"></a>Czy użytkownik istnieje w lokalnej usłudze Active Directory?

To pytanie próbuje zidentyfikować obiekt źródłowy istniejącego użytkownika z lokalnej usługi Active Directory.  
1. Sprawdź, czy usługa Azure Active Directory ma obiekt o podanej **aplikacji UserPrincipalName**. Jeśli nie, **odpowiedz nie**.
2. Jeśli tak, sprawdź, czy obiekt jest nadal w zakresie synchronizacji.  
   - Wyszukiwanie w przestrzeni łącznika usługi Azure AD przy użyciu sieci owej.
   - Jeśli obiekt zostanie znaleziony w stanie **Oczekujące na dodanie,** **odpowiedz nie**. Usługa Azure AD Connect nie może połączyć obiektu z właściwym obiektem usługi Azure AD.
   - Jeśli obiekt nie został znaleziony, odpowiedz **tak**.

W tych przykładach pytanie próbuje zidentyfikować, czy **Joe Jackson** nadal istnieje w lokalnej usłudze Active Directory.
W **typowym scenariuszu**zarówno użytkownicy **Joe Johnson,** jak i **Joe Jackson** są obecni w lokalnej usłudze Active Directory. Obiekty poddane kwarantannie to dwaj różni użytkownicy.

![Diagnozowanie typowy scenariusz błędu synchronizacji](./media/how-to-connect-health-diagnose-sync-errors/IIdFixCommonCase.png)

W **przypadku scenariusza osieroconego obiektu**w lokalnej usłudze Active Directory występuje tylko jeden użytkownik **Joe Johnson:**

![Diagnozowanie błędu synchronizacji osieroconego obiektu *czy użytkownik istnieje* scenariusz](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)

### <a name="do-both-of-these-accounts-belong-to-the-same-user"></a>Czy oba te konta należą do tego samego użytkownika?
To pytanie sprawdza przychodzącego użytkownika powodującego konflikt i istniejący obiekt użytkownika w usłudze Azure AD, aby sprawdzić, czy należą one do tego samego użytkownika.  
1. Obiekt powodujący konflikt jest nowo synchronizowany z usługą Azure Active Directory. Porównaj atrybuty obiektów:  
   - Nazwa wyświetlana
   - Nazwa główna użytkownika
   - Identyfikator obiektu
2. Jeśli usługa Azure AD nie może ich porównać, sprawdź, czy usługa Active Directory ma obiekty o **podanych nazwach UserPrincipal.** Odpowiedź **nie,** jeśli znajdziesz oba.

W poniższym przykładzie dwa obiekty należą do tego samego użytkownika **Joe Johnson**.

![Diagnozowanie błędu synchronizacji osieroconego obiektu *ten sam użytkownik* scenariusz](./media/how-to-connect-health-diagnose-sync-errors/IIdFixOrphanedCase.png)


## <a name="what-happens-after-the-fix-is-applied-in-the-orphaned-object-scenario"></a>Co się stanie po zastosowaniu poprawki w scenariuszu osieroconego obiektu
Na podstawie odpowiedzi na powyższe pytania zobaczysz przycisk **Zastosuj poprawkę,** gdy istnieje poprawka dostępna w usłudze Azure AD. W takim przypadku obiekt lokalny jest synchronizowany z nieoczekiwanym obiektem usługi Azure AD. Oba obiekty są mapowane za pomocą **zakotwiczenia źródła**. Zmiana **Zastosuj poprawkę** wykonuje następujące lub podobne kroki:
1. Aktualizuje **kotwicę źródłową** do właściwego obiektu w usłudze Azure AD.
2. Usuwa obiekt powodujący konflikt w usłudze Azure AD, jeśli jest obecny.

![Diagnozowanie błędu synchronizacji po poprawce](./media/how-to-connect-health-diagnose-sync-errors/IIdFixAfterFix.png)

>[!IMPORTANT]
> Zmiana **Zastosuj poprawkę** dotyczy tylko osieroconych przypadków obiektów.
>

Po powyższych krokach użytkownik może uzyskać dostęp do oryginalnego zasobu, który jest łączem do istniejącego obiektu. Wartość **Diagnozuj stan** w widoku listy jest aktualizowana **w widoku oczekującym na synchronizację**. Błąd synchronizacji zostanie rozwiązany po następnej synchronizacji. Funkcja Connect Health nie będzie już wyświetlać błędu synchronizacji rozwiązał w widoku listy.

## <a name="failures-and-error-messages"></a>Błędy i komunikaty o błędach
**Użytkownik z atrybutem powodującym konflikt jest nietrwale usunięty w usłudze Azure Active Directory. Upewnij się, że użytkownik jest mocno usunięty przed ponowieniem próby.**  
Użytkownik z atrybutem powodującym konflikt w usłudze Azure AD powinien zostać wyczyszczony przed zastosowaniem poprawki. Sprawdź, [jak usunąć użytkownika na stałe w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-restore) przed ponowieniem próby poprawki. Użytkownik zostanie również automatycznie usunięty na stałe po 30 dniach w stanie nieuiszczona. 

**Aktualizowanie źródła kotwicy do użytkownika w chmurze w dzierżawie nie jest obsługiwane.**  
Użytkownik oparty na chmurze w usłudze Azure AD nie powinien mieć kotwicy źródłowej. Aktualizacja źródła zakotwiczenia nie jest obsługiwana w tym przypadku. Ręczna poprawka jest wymagana z lokalnego. 

## <a name="faq"></a>Najczęściej zadawane pytania
**PYTANIE** Co się stanie, jeśli wykonanie **poprawki zastosuj** nie powiedzie się?  
**A.** Jeśli wykonanie nie powiedzie się, możliwe, że usługa Azure AD Connect jest uruchomiony błąd eksportu. Odśwież stronę portalu i ponów próbę po następnej synchronizacji. Domyślny cykl synchronizacji wynosi 30 minut. 


**PYTANIE** Co zrobić, jeśli **istniejący obiekt** powinien być obiektem do usunięcia?  
**A.** Jeśli **istniejący obiekt** powinien zostać usunięty, proces nie wiąże się ze zmianą **source anchor**. Zazwyczaj można go naprawić z lokalnej usługi Active Directory. 


**PYTANIE** Jakie uprawnienia ma użytkownik do zastosowania poprawki?  
**A.** **Administrator globalny**lub **współautor** z ustawień RBAC ma uprawnienia dostępu do procesu diagnostyki i rozwiązywania problemów.


**PYTANIE** Czy muszę skonfigurować usługę Azure AD Connect lub zaktualizować agenta usługi Azure AD Connect Health dla tej funkcji?  
**A.** Nie, proces diagnozowania jest kompletną funkcją opartą na chmurze.


**PYTANIE** Jeśli istniejący obiekt zostanie usunięty nietrwale, czy proces diagnozowania spowoduje, że obiekt ponownie stanie się aktywny?  
**A.** Nie, poprawka nie zaktualizuje atrybutów obiektów innych niż **Source Anchor**.
