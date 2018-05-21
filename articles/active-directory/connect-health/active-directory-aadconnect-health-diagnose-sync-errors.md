---
title: Azure AD Connect Health - diagnozowanie zduplikowany atrybut błędy synchronizacji | Dokumentacja firmy Microsoft
description: W tym dokumencie opisano proces diagnozowanie błędów synchronizacji zduplikowany atrybut i popraw potencjalnych scenariuszy obiektu oddzielonego bezpośrednio z portalu Azure.
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
ms.openlocfilehash: 0a345fd3443fb33d6f5912c8a04677091e20ecc8
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="duplicate-attribute-sync-errors-diagnosis-and-remediation"></a>Zduplikowany atrybut synchronizacji błędy diagnostyki i korygowania 

## <a name="overview"></a>Przegląd
Biorąc krok dalej wyróżnienia błędy synchronizacji, usługi Azure Active Directory Connect Health wprowadzają środowisko samodzielne skorygowanie problemu, do rozwiązywania problemów, zduplikowany atrybut synchronizacji i usunąć obiekty oddzielone z usługi Azure AD. Najważniejszą korzyścią z funkcji diagnostyki:
- Podaj procedury diagnostycznej zawęzić zduplikowany atrybut synchronizacji błąd scenariuszy i wskazuje określonego rozwiązania
- Zastosuj poprawkę dedykowanych scenariuszy z usługi Azure AD, aby rozwiązać problem w jednym kliknięciem
- Nie uaktualnienia lub konfiguracji są wymagane do włączenia tej funkcji.
Przeczytaj więcej szczegółów na temat usługi Azure AD [zduplikowane odporności](https://aka.ms/dupattributeresdocs).

## <a name="problems"></a>Problemy
### <a name="common-scenario"></a>Typowy scenariusz
Gdy **QuarantinedAttributeValueMustBeUnique** i **AttributeValueMustBeUnique** błędy synchronizacji wystąpi, często, aby zobaczyć konflikt głównej nazwy użytkownika lub adresów serwerów Proxy w usłudze Azure AD. Błędy synchronizacji może rozwiązać, aktualizując powodujące konflikt obiektu źródłowego z lokalnej strony. Po zakończeniu synchronizacji następujący błąd synchronizacji zostanie rozwiązany. Na przykład na ilustracji poniżej oznacza dwóch użytkowników występuje konflikt ich UserPrincipalName jako *Joe.J@contoso.com*. Obiekty powodujące konflikt zostały poddane kwarantannie w usłudze Azure AD. 

![Diagnozowanie typowy scenariusz błędu synchronizacji](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixCommonCase.png)

### <a name="orphaned-object-scenario"></a>Scenariusz obiektu oddzielonego
Czasami może okaże się, że istniejącego użytkownika utraci zakotwiczenie źródła. Usuwanie obiektu źródłowego wystąpił w lokalnie AD, ale zmiany sygnału usunięcia nigdy nie otrzymała synchronizowane z usługi Azure AD. Może się zdarzyć z powodów, takich jak synchronizacja aparat problem lub przeprowadzenia migracji domeny. Podczas tego samego obiektu uzyskano przywrócić lub ponownie utworzyć, logicznie istniejącego użytkownika powinna być użytkownika do synchronizacji z zakotwiczenie źródła. Dla istniejącego użytkownika jako obiekt tylko w chmurze można również wyświetlić powodujące konflikt użytkownika synchronizowane z usługą Azure AD i nie można dopasować do istniejącego obiektu synchronizacji. Nie istnieje sposób bezpośredniego można ponownie zmapować zakotwiczenie źródła. Przeczytaj więcej na temat [KB istniejących](https://support.microsoft.com/help/2647098). Na przykład istniejący obiekt w usłudze Azure AD zachowuje licencji Jan. Nowo synchronizowany obiekt o zakotwiczenie źródła różnych Wystąpił stan zduplikowany atrybut w usłudze Azure AD. Zmiany z Jan w lokalnej usługi AD nie będzie można ma zostać zastosowany do oryginalnego użytkownika Jana (istniejący obiekt) w usłudze Azure AD.  

![Diagnozowanie scenariusz obiektu oddzielonego błędu synchronizacji](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixOrphanedCase.png)

## <a name="diagnostic-and-troubleshooting-steps-in-connect-health"></a>Diagnostyka i rozwiązywanie problemów z kroków Connect Health 
Diagnozowanie funkcji obsługuje obiekty użytkownika z następującymi atrybutami zduplikowane:

| Nazwa atrybutu | Typy błędów synchronizacji|
| ------------------ | -----------------|
| UserPrincipalName | QuarantinedAttributeValueMustBeUnique lub AttributeValueMustBeUnique | 
| ProxyAddresses | QuarantinedAttributeValueMustBeUnique lub AttributeValueMustBeUnique | 
| sipProxyAddress | AttributeValueMustBeUnique | 
| onPremiseSecurityIdentifier |  AttributeValueMustBeUnique |

>[!IMPORTANT]
> Wymaga on **administratora globalnego** uprawnienia lub **współautora** z ustawień RBAC, aby można było korzystać z tej funkcji. 
>

Następujące kroki w portalu Azure można zawęzić szczegóły błędu synchronizacji i dostarczanie rozwiązań do bardziej szczegółowych:

![Diagnozowanie synchronizacji kroki diagnozowanie błędów](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixSteps.png)

W portalu Azure można przejść przez kilka czynności, aby zidentyfikować konkretnych możliwych do skorygowania scenariuszy:  
1.  W kolumnie Stan diagnozowanie stan zostaną wyświetlone, jeśli istnieje potencjalne przepływów rozwiązywania problemów, aby zawęzić przypadek błędu i potencjalnie rozwiązać bezpośrednio z usługą Azure Active Directory.
| Stan | Co to znaczy? |
| ------------------ | -----------------|
| Nie uruchomiono | Ten proces diagnostyki nie ma odwiedzone. Zależy od wyniku diagnostyczne jest potencjalnie sposobem bezpośrednio usunąć błąd synchronizacji z portalu. |
| Wymagane jest ręczne wprowadzenie poprawki | Błąd nie pasuje do kryteriów Poprawka dostępna z portalu. Wielkość liter może być (1) obiektu powodującego konflikt, który typy nie są użytkownicy, (2) już prowadzący czynności diagnostycznych i nie rozwiązać rozpoznawania dostępna z portalu. W takim przypadku poprawkę z lokalnej strony nadal będzie jednego z rozwiązań. [Dowiedz się więcej na temat poprawka lokalnymi](https://support.microsoft.com/help/2647098) | 
| Oczekuje na synchronizację | Poprawka została zastosowana. Oczekiwanie na następny cykl synchronizacji wyczyścić błąd. |
>[!IMPORTANT]
> W kolumnie Stan diagnostycznych spowoduje zresetowanie po każdym cyklu synchronizacji. 
>

2.  Klikając **Diagnozuj** przycisk w bloku szczegóły błędu, należy odpowiedzieć na kilka pytań i identyfikuje szczegóły błędu synchronizacji. Udzielenie odpowiedzi na pytania pomoże zidentyfikować w przypadku scenariusza obiektu oddzielonego. 

3.  W przypadku **Zamknij** przycisk na końcu diagnostyki, oznacza to, jest dostępna z portalu na podstawie danego odpowiedzi nie szybkiej poprawki. Można znaleźć rozwiązania wyświetlany w ostatnim kroku. Poprawka z lokalnie będą nadal rozwiązania. Po kliknięciu przycisku Zamknij, można znaleźć stan bieżący błąd synchronizacji przełączy się **Ręczna poprawka wymagane**. Stan zachowują bieżącego cyklu synchronizacji.

4.  Po zidentyfikowaniu obiektu oddzielonego case można naprawić zduplikowane atrybuty błędy synchronizacji bezpośrednio z portalu. Polecenie **zastosować napraw** przycisku do wyzwalania procesu. Stan bieżący błąd synchronizacji zostanie zaktualizowana, aby być **oczekujące synchronizacji**.

5.  Po cyklu synchronizacji następujący błąd powinna zostać usunięta z listy.

## <a name="how-to-answer-the-diagnosis-questions"></a>Jak odpowiedzieć na pytania diagnostyki 
### <a name="does-the-user-exist-in-your-on-premises-active-directory"></a>Użytkownik istnieje w w lokalnej usłudze Active Directory?

Pytanie próbuje zidentyfikować obiekt źródłowy istniejącego użytkownika z lokalnej usługi Active Directory.  
1.  Sprawdź, czy usługi Active Directory zawiera obiekt z podanych UserPrincipalName. Jeśli nie, odpowiedzi na nie.
2.  Jeśli tak, sprawdź, czy obiekt jest nadal w zakresie do synchronizacji.  
  - Szukaj w obszarze łącznika usługi Azure AD według nazwy wyróżniającej.
  - Jeśli znaleziono obiekt o **dodać oczekujące** stanu, odpowiedzi na nie. Azure AD Connect nie jest w stanie nawiązać obiekt prawym obiektu usługi Active Directory.
  - Jeśli obiekt nie zostanie znaleziony, odpowiedź Tak.

> Biorąc na poniższym diagramie, na przykład pytanie jest próby ustalenia, czy *Jackson Jan* nadal istnieć w lokalnej usłudze Active Directory.
Dla **typowy scenariusz**, zarówno użytkownika *Johnson Jan* i *Jackson Jan* będzie znajdować się w lokalnej usługi Active Directory. Poddane kwarantannie obiekty są dwóch różnych użytkowników.

![Diagnozowanie typowy scenariusz błędu synchronizacji](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixCommonCase.png)

> Dla **scenariusza obiektu oddzielonego**, tylko jednego użytkownika — *Johnson Jan* będzie wyświetlany z lokalnej usługi Active Directory:

![Diagnozowanie scenariusz obiektu oddzielonego błędu synchronizacji](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixOrphanedCase.png)

### <a name="do-both-these-accounts-belong-to-the-same-user"></a>Czy oba te konta należą do tego samego użytkownika?
Pytanie jest sprawdzanie użytkownika przychodzącego powodujące konflikt i istniejącego obiektu użytkownika w usłudze Azure AD, aby sprawdzić, czy należą do tego samego użytkownika.  
1.  Obiektu powodujące konflikt nowo jest zsynchronizowane z usługą Azure Active Directory. Porównuje obiekt z jego:  
  - Nazwa wyświetlana
  - Nazwa główna użytkownika
  - Identyfikator obiektu
2.  Jeśli nie można porównywać, sprawdź, czy usługi Active Directory zawiera obiekty z podanych UserPrincipalNames. Odpowiedź nie, jeśli znajdują się zarówno.  

> W tym przypadku dwa obiekty należące do tego samego użytkownika *Johnson Jan*.

![Diagnozowanie scenariusz obiektu oddzielonego błędu synchronizacji](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixOrphanedCase.png)


## <a name="what-happened-after-fix-is-applied-for-orphaned-object-scenario"></a>Co się stało, po zastosowaniu poprawki dla obiektu oddzielonego scenariusza
Na podstawie odpowiedzi zgłoszono pytań, można wyświetlić **zastosować napraw** przycisku, gdy poprawka jest dostępna z usługi Azure AD. W takim przypadku na obiekt lokalny jest synchronizowana z usługą Azure nieoczekiwany obiekt usługi Active Directory. Dwa obiekty są mapowane za pomocą "Zakotwiczenie źródła". Zastosuj zmiany zostaną wykonane kroki takich jak:
- Zaktualizuj zakotwiczenie źródła do prawidłowy obiekt w usłudze Azure AD.
- Usuń powodujące konflikt obiektu w usłudze Azure AD, jeśli stanowi.

![Diagnozowanie błędów synchronizacji po poprawki](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixAfterFix.png)

>[!IMPORTANT]
> Napraw Zastosuj zmiany będzie dotyczyć tylko przypadków obiektu oddzielonego.
>

Po wykonaniu powyższych kroków użytkownik będzie mógł uzyskać dostęp do oryginalnego zasób, który jest łącze do istniejącego obiektu. **Diagnozowanie stan** wartości w widoku listy zostanie zaktualizowany w celu zapewnienia **oczekujące synchronizacji**. Po zakończeniu synchronizacji następujący błąd synchronizacji zostanie rozwiązany. Połącz kondycji już nie wyświetli błąd rozpoznać synchronizacji z widoku listy. 


## <a name="faq"></a>Często zadawane pytania
 -  Co się stało, jeśli nie można wykonać Zastosuj?  
W przypadku niepowodzenia wykonywania jest możliwych usługi Azure AD Connect jest uruchomiona Błąd eksportu w czasie. Odśwież stronę portalu i spróbuj ponownie po następujące synchronizacji. Cykl synchronizacji domyślny to 30 minut. 

 -  Co zrobić, jeśli **istniejący obiekt** powinien być obiekt do usunięcia?  
Jeśli istniejący obiekt powinien zostać usunięty w tym przypadku, proces nie obejmują zmiany zakotwiczenie źródła. Powinno być możliwe ustalenie z sieci lokalnej usługi AD.  

 -  Co to jest uprawnień dla użytkownika można było zastosować poprawkę?  
Globalny administrator lub współautora z ustawień RBAC ma uprawnienia dostępu do diagnostyki i proces rozwiązywania problemów.

 -  I trzeba config AAD Connect lub zaktualizuj program Azure AD Connect Health agent dla tej funkcji?  
Nie, proces diagnostyki jest pełną funkcją oparte na chmurze.

 -  Jeśli istniejący obiekt jest delikatny usunięty, proces Diagnozuj przywróci obiektów jako aktywnych ponownie?  
Nie, poprawka nie może zaktualizować atrybutu obiektu innego niż zakotwiczenie źródła. 
