---
title: 'Usługa Azure AD Connect: pojęcia dotyczące projektowania | Dokumenty firmy Microsoft'
description: W tym temacie opisano niektóre obszary projektowania implementacji
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 4114a6c0-f96a-493c-be74-1153666ce6c9
ms.service: active-directory
ms.custom: azure-ad-connect
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb41e14a7ecf41a2698a063c3067a98d8acf8f07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253886"
---
# <a name="azure-ad-connect-design-concepts"></a>Usługa Azure AD Connect: koncepcje projektu
Celem tego dokumentu jest opisanie obszarów, które muszą być przemyślane podczas projektowania implementacji usługi Azure AD Connect. Ten dokument jest głębokim zanurzeniem w niektórych obszarach i te koncepcje są krótko opisane w innych dokumentach, jak również.

## <a name="sourceanchor"></a>źródłoAnchor
Atrybut sourceAnchor jest definiowany jako *atrybut niezmienny w okresie istnienia obiektu*. Unikatowo identyfikuje obiekt jako ten sam obiekt w środowisku lokalnym i w usłudze Azure AD. Atrybut jest również nazywany **immutableId** i dwie nazwy są używane wymienne.

Słowo niezmienne, czyli "nie można zmienić", jest ważne dla tego dokumentu. Ponieważ wartość tego atrybutu nie można zmienić po jego ustawieniu, ważne jest, aby wybrać projekt, który obsługuje scenariusz.

Atrybut jest używany w następujących scenariuszach:

* Po zbudowaniu lub przebudowywanym nowym serwerze aparatu synchronizacji lub przebudowie po scenariuszu odzyskiwania po awarii ten atrybut łączy istniejące obiekty w usłudze Azure AD z obiektami lokalnymi.
* Jeśli przeniesiesz się z tożsamości tylko w chmurze do zsynchronizowanego modelu tożsamości, ten atrybut umożliwia obiektom "dopasowanie" istniejących obiektów w usłudze Azure AD z obiektami lokalnymi.
* Jeśli używasz federacji, a następnie ten atrybut wraz z **userPrincipalName** jest używany w oświadczeniu jednoznacznie zidentyfikować użytkownika.

Ten temat mówi tylko o sourceAnchor, ponieważ odnosi się do użytkowników. Te same reguły mają zastosowanie do wszystkich typów obiektów, ale to tylko dla użytkowników ten problem zwykle jest problemem.

### <a name="selecting-a-good-sourceanchor-attribute"></a>Wybieranie dobrego atrybutu sourceAnchor
Wartość atrybutu musi być zgodna z następującymi regułami:

* Mniej niż 60 znaków długości
  * Znaki nie są a-z, A-Z lub 0-9 są kodowane i liczone jako 3 znaki
* Nie zawierają znaku specjalnego: &#92; ! # $ % & * + / = ? ↑ &#96; { } | ~ < > ( ) " ; : , [ ] " \@ _
* Musi ona być unikatowa w skali globalnej
* Musi być ciągiem, całkowitej lub binarnej
* Nie powinny być oparte na nazwie użytkownika, ponieważ mogą one ulec zmianie
* Nie powinny być rozróżniane wielkości liter i unikać wartości, które mogą się różnić w zależności od wielkości liter
* Powinien być przypisany podczas tworzenia obiektu

Jeśli wybrany sourceAnchor nie jest typu ciąg, a następnie usługi Azure AD Connect Base64Encode wartość atrybutu, aby upewnić się, że nie znaki specjalne są wyświetlane. Jeśli używasz innego serwera federacyjnego niż usługa ADFS, upewnij się, że twój serwer może również kodować atrybut Base64Encode.

W atrybucie SourceAnchor jest rozróżniana wielkość liter. Wartość "JohnDoe" nie jest taka sama jak "johndoe". Ale nie powinieneś mieć dwóch różnych obiektów z tylko różnicą w przypadku.

Jeśli masz jeden las w środowisku lokalnym, a następnie atrybut, którego należy użyć jest **objectGUID**. Jest to również atrybut używany podczas korzystania z ustawień ekspresowych w usłudze Azure AD Connect, a także atrybut używany przez DirSync.

Jeśli masz wiele lasów i nie przenosisz użytkowników między lasami i domenami, **objectGUID** jest dobrym atrybutem do użycia nawet w tym przypadku.

Jeśli przeniesiesz użytkowników między lasami i domenami, musisz znaleźć atrybut, który nie zmienia się lub może zostać przeniesiony z użytkownikami podczas przenoszenia. Zalecane podejście jest wprowadzenie atrybutu syntetycznego. Odpowiedni byłby atrybut, który może zawierać coś, co wygląda jak identyfikator GUID. Podczas tworzenia obiektu tworzony jest nowy identyfikator GUID i stemplowany na użytkowniku. Można utworzyć niestandardową regułę synchronizacji na serwerze aparatu synchronizacji, aby utworzyć tę wartość na podstawie **objectGUID** i zaktualizować wybrany atrybut w programie ADDS. Podczas przenoszenia obiektu należy również skopiować zawartość tej wartości.

Innym rozwiązaniem jest wybranie istniejącego atrybutu, o czym wiesz, że się nie zmieni. Najczęściej używane atrybuty obejmują **employeeID**. Jeśli wziąć pod uwagę atrybut, który zawiera litery, upewnij się, że nie ma szans, że wielkość liter (wielkie litery a małe litery) może zmienić wartość atrybutu. Złe atrybuty, które nie powinny być używane obejmują te atrybuty z nazwą użytkownika. W małżeństwie lub rozwodzie oczekuje się zmiany nazwiska, co nie jest dozwolone dla tego atrybutu. Jest to również jeden z powodów, dla których atrybuty, takie jak **userPrincipalName**, **mail**i **targetAddress** nie są nawet możliwe do wybrania w kreatorze instalacji usługi Azure AD Connect. Te atrybuty zawierają\@również znak " ", który nie jest dozwolony w sourceAnchor.

### <a name="changing-the-sourceanchor-attribute"></a>Zmiana atrybutu sourceAnchor
Nie można zmienić wartości atrybutu sourceAnchor po utworzeniu obiektu w usłudze Azure AD i synchronizacji tożsamości.

Z tego powodu następujące ograniczenia mają zastosowanie do usługi Azure AD Connect:

* SourceAnchor atrybut można ustawić tylko podczas instalacji początkowej. Po ponownym uruchomienia kreatora instalacji ta opcja jest tylko do odczytu. Jeśli chcesz zmienić to ustawienie, należy odinstalować i ponownie zainstalować.
* Jeśli zainstalujesz inny serwer usługi Azure AD Connect, należy wybrać ten sam atrybut sourceAnchor, który był poprzednio używany. Jeśli wcześniej używasz DirSync i przenieść do usługi Azure AD Connect, następnie należy użyć **objectGUID,** ponieważ jest to atrybut używany przez DirSync.
* Jeśli wartość sourceAnchor zostanie zmieniona po wyeksportowanie obiektu do usługi Azure AD, synchronizacja usługi Azure AD Connect zgłasza błąd i nie zezwala na więcej zmian w tym obiekcie, zanim problem został rozwiązany, a sourceAnchor zostanie zmieniony z powrotem w źródle Katalogu.

## <a name="using-ms-ds-consistencyguid-as-sourceanchor"></a>Używanie ms-DS-ConsistencyGuid jako źródłaAnchor
Domyślnie usługa Azure AD Connect (wersja 1.1.486.0 i starsze) używa objectGUID jako sourceAnchor atrybut. ObjectGUID jest generowany przez system. Nie można określić jego wartości podczas tworzenia lokalnych obiektów usługi AD. Jak wyjaśniono w sekcji [sourceAnchor](#sourceanchor), istnieją scenariusze, w których należy określić sourceAnchor wartość. Jeśli scenariusze mają zastosowanie do Ciebie, należy użyć konfigurowalny atrybut AD (na przykład ms-DS-ConsistencyGuid) jako sourceAnchor atrybut.

Usługa Azure AD Connect (wersja 1.1.524.0 i później) ułatwia teraz korzystanie z ms-DS-ConsistencyGuid jako sourceAnchor atrybut. Korzystając z tej funkcji, usługa Azure AD Connect automatycznie konfiguruje reguły synchronizacji na:

1. Użyj ms-DS-ConsistencyGuid jako sourceAnchor atrybut dla user obiektów. ObjectGUID jest używany dla innych typów obiektów.

2. Dla każdego danego lokalnego obiektu użytkownika usługi AD, którego atrybut ms-DS-ConsistencyGuid nie jest wypełniony, usługa Azure AD Connect zapisuje swoją wartość objectGUID z powrotem do atrybutu ms-DS-ConsistencyGuid w lokalnej usłudze Active Directory. Po wypełnieniu atrybutu ms-DS-ConsistencyGuid usługa Azure AD Connect eksportuje obiekt do usługi Azure AD.

>[!NOTE]
> Po zaimportowaniu lokalnego obiektu usługi AD do usługi Azure AD Connect (czyli zaimportowane do obszaru łącznika usługi AD i rzutowane do metaverse), nie można już zmienić jego sourceAnchor wartość. Aby określić wartość sourceAnchor dla danego lokalnego obiektu USŁUGI AD, skonfiguruj jego atrybut ms-DS-ConsistencyGuid przed zaimportowaniem do usługi Azure AD Connect.

### <a name="permission-required"></a>Wymagane uprawnienie
Aby ta funkcja działała, konto usług AD DS używane do synchronizacji z lokalną usługą Active Directory musi mieć uprawnienia do zapisu do atrybutu ms-DS-ConsistencyGuid w lokalnej usłudze Active Directory.

### <a name="how-to-enable-the-consistencyguid-feature---new-installation"></a>Jak włączyć funkcję ConsistencyGuid - Nowa instalacja
Można włączyć użycie ConsistencyGuid jako sourceAnchor podczas nowej instalacji. Ta sekcja obejmuje zarówno instalacji ekspresowej i niestandardowej w szczegółach.

  >[!NOTE]
  > Tylko nowsze wersje usługi Azure AD Connect (1.1.524.0 i później) obsługują użycie ConsistencyGuid jako sourceAnchor podczas nowej instalacji.

### <a name="how-to-enable-the-consistencyguid-feature"></a>Jak włączyć funkcję ConsistencyGuid

#### <a name="express-installation"></a>Instalacja ekspresowa
Podczas instalowania usługi Azure AD Connect w trybie ekspresowym kreator usługi Azure AD Connect automatycznie określa najbardziej odpowiedni atrybut usługi AD do użycia jako atrybut sourceAnchor przy użyciu następującej logiki:

* Po pierwsze kreator usługi Azure AD Connect wysyła zapytanie do dzierżawy usługi Azure AD, aby pobrać atrybut usługi AD używany jako atrybut sourceAnchor w poprzedniej instalacji usługi Azure AD Connect (jeśli istnieje). Jeśli te informacje są dostępne, usługa Azure AD Connect używa tego samego atrybutu USŁUGI AD.

  >[!NOTE]
  > Tylko nowsze wersje usługi Azure AD Connect (1.1.524.0 i później) przechowują informacje w dzierżawie usługi Azure AD o źródłowymatrybuanchor używany podczas instalacji. Starsze wersje usługi Azure AD Connect nie.

* Jeśli informacje o używanym atrybucie sourceAnchor nie są dostępne, kreator sprawdza stan atrybutu ms-DS-ConsistencyGuid w lokalnej usłudze Active Directory. Jeśli atrybut nie jest skonfigurowany na żadnym obiekcie w katalogu, kreator używa ms-DS-ConsistencyGuid jako sourceAnchor atrybut. Jeśli atrybut jest skonfigurowany na jednym lub więcej obiektów w katalogu, kreator stwierdza, że atrybut jest używany przez inne aplikacje i nie jest odpowiedni jako sourceAnchor atrybut...

* W takim przypadku kreator powraca do korzystania objectGUID jako sourceAnchor atrybut.

* Po podjęciu decyzji o atrybucie sourceAnchor kreator przechowuje informacje w dzierżawie usługi Azure AD. Informacje będą używane przez przyszłą instalację usługi Azure AD Connect.

Po zakończeniu instalacji ekspresowej kreator informuje, który atrybut został wybrany jako atrybut Source Anchor.

![Kreator informuje atrybut AD pobrany dla sourceAnchor](./media/plan-connect-design-concepts/consistencyGuid-01.png)

#### <a name="custom-installation"></a>Instalacja niestandardowa
Podczas instalowania usługi Azure AD Connect w trybie niestandardowym kreator usługi Azure AD Connect udostępnia dwie opcje podczas konfigurowania atrybutu sourceAnchor:

![Instalacja niestandardowa — konfiguracja sourceAnchor](./media/plan-connect-design-concepts/consistencyGuid-02.png)

| Ustawienie | Opis |
| --- | --- |
| Pozwól, aby platforma Azure zarządzała zakotwiczeniem źródła | Wybierz tę opcję, jeśli chcesz, aby usługa Azure AD wybierała ten atrybut za Ciebie. Jeśli wybierzesz tę opcję, Kreator usługi Azure AD Connect zastosuje to samo [źródłoWącze logika wyboru atrybutu, która jest używana podczas instalacji ekspresowej.](#express-installation) Podobnie jak w instalacji ekspresowej, kreator informuje, który atrybut został wybrany jako atrybut Source Anchor po zakończeniu instalacji niestandardowej. |
| Określony atrybut | Wybierz tę opcję, jeśli chcesz określić istniejący atrybut usługi AD jako atrybut sourceAnchor. |

### <a name="how-to-enable-the-consistencyguid-feature---existing-deployment"></a>Jak włączyć funkcję ConsistencyGuid — istniejące wdrożenie
Jeśli masz istniejące wdrożenie usługi Azure AD Connect, które używa objectGUID jako atrybutu Zakotwiczenie źródła, możesz przełączyć go na przy użyciu ConsistencyGuid.

>[!NOTE]
> Tylko nowsze wersje usługi Azure AD Connect (1.1.552.0 i później) obsługują przełączanie z ObjectGuid na ConsistencyGuid jako atrybut Kotwica źródła.

Aby przełączyć się z objectGUID na ConsistencyGuid jako atrybut Source Anchor:

1. Uruchom kreatora usługi Azure AD Connect i kliknij przycisk **Konfiguruj,** aby przejść do ekranu Zadania.

2. Wybierz opcję Konfigurowanie zadania **Zakotwiczenie źródła** i kliknij przycisk **Dalej**.

   ![Włącz spójne guid dla istniejącego wdrożenia — krok 2](./media/plan-connect-design-concepts/consistencyguidexistingdeployment01.png)

3. Wprowadź poświadczenia administratora usługi Azure AD i kliknij przycisk **Dalej**.

4. Kreator usługi Azure AD Connect analizuje stan atrybutu ms-DS-ConsistencyGuid w lokalnej usłudze Active Directory. Jeśli atrybut nie jest skonfigurowany na żaden obiekt w katalogu, usługi Azure AD Connect stwierdza, że żadna inna aplikacja jest obecnie przy użyciu atrybutu i jest bezpieczny do użycia jako atrybut kotwicy źródła. Kliknij **przycisk Dalej,** aby kontynuować.

   ![Włącz spójne guid dla istniejącego wdrożenia — krok 4](./media/plan-connect-design-concepts/consistencyguidexistingdeployment02.png)

5. Na ekranie **Gotowe do skonfigurowania** kliknij przycisk **Konfiguruj,** aby wprowadzić zmianę konfiguracji.

   ![Włącz spójne guid dla istniejącego wdrożenia — krok 5](./media/plan-connect-design-concepts/consistencyguidexistingdeployment03.png)

6. Po zakończeniu konfiguracji kreator wskazuje, że ms-DS-ConsistencyGuid jest teraz używany jako atrybut Zakotwiczenie źródła.

   ![Włącz spójne guid dla istniejącego wdrożenia — krok 6](./media/plan-connect-design-concepts/consistencyguidexistingdeployment04.png)

Podczas analizy (krok 4), jeśli atrybut jest skonfigurowany na jeden lub więcej obiektów w katalogu, kreator stwierdza, że atrybut jest używany przez inną aplikację i zwraca błąd, jak pokazano na poniższym diagramie. Ten błąd może również wystąpić, jeśli wcześniej włączono funkcję ConsistencyGuid na podstawowym serwerze usługi Azure AD Connect i próbujesz zrobić to samo na serwerze przejściowym.

![Włącz spójne guid dla istniejącego wdrożenia - błąd](./media/plan-connect-design-concepts/consistencyguidexistingdeploymenterror.png)

 Jeśli masz pewność, że atrybut nie jest używany przez inne istniejące aplikacje, można pominąć błąd, uruchamiając ponownie kreatora usługi Azure AD Connect z przełącznikiem **/SkipLdapSearch** określonym. Aby to zrobić, uruchom następujące polecenie w wierszu polecenia:

```
"c:\Program Files\Microsoft Azure Active Directory Connect\AzureADConnect.exe" /SkipLdapSearch
```

### <a name="impact-on-ad-fs-or-third-party-federation-configuration"></a>Wpływ na usługi AD FS lub konfigurację federacji innych firm
Jeśli używasz usługi Azure AD Connect do zarządzania lokalnym wdrożeniem usług AD FS, usługa Azure AD Connect automatycznie aktualizuje reguły oświadczeń, aby używać tego samego atrybutu USŁUGI AD jako sourceAnchor. Gwarantuje to, że oświadczenie ImmutableID generowane przez usługę ADFS jest zgodne z wartościami sourceAnchor eksportowane do usługi Azure AD.

Jeśli zarządzasz usługami AD FS poza usługą Azure AD Connect lub używasz serwerów federacyjnych innych firm do uwierzytelniania, musisz ręcznie zaktualizować reguły oświadczeń dla oświadczenia ImmutableID, aby były zgodne ze źródłowymi wartościami usługi Ad eksportowane do usługi Azure AD, zgodnie z opisem w sekcji artykuł [zmodyfikuj reguły oświadczeń usług AD FS](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-management#modclaims). Po zakończeniu instalacji kreator zwraca następujące ostrzeżenie:

![Konfiguracja federacji innych firm](./media/plan-connect-design-concepts/consistencyGuid-03.png)

### <a name="adding-new-directories-to-existing-deployment"></a>Dodawanie nowych katalogów do istniejącego wdrożenia
Załóżmy, że wdrożono usługę Azure AD Connect z włączoną funkcją ConsistencyGuid, a teraz chcesz dodać inny katalog do wdrożenia. Podczas próby dodania katalogu kreator usługi Azure AD Connect sprawdza stan atrybutu ms-DS-ConsistencyGuid w katalogu. Jeśli atrybut jest skonfigurowany na jeden lub więcej obiektów w katalogu, kreator stwierdza, że atrybut jest używany przez inne aplikacje i zwraca błąd, jak pokazano na poniższym diagramie. Jeśli masz pewność, że atrybut nie jest używany przez istniejące aplikacje, można pominąć błąd, uruchamiając ponownie kreatora usługi Azure AD Connect z przełącznikiem **/SkipLdapSearch** określonym w sposób opisany powyżej lub skontaktuj się z pomocą techniczną, aby uzyskać więcej informacji.

![Dodawanie nowych katalogów do istniejącego wdrożenia](./media/plan-connect-design-concepts/consistencyGuid-04.png)

## <a name="azure-ad-sign-in"></a>Logowanie usługi Azure AD
Podczas integrowania katalogu lokalnego z usługą Azure AD, ważne jest, aby zrozumieć, jak ustawienia synchronizacji mogą wpływać na sposób uwierzytelniania użytkownika. Usługa Azure AD używa userPrincipalName (UPN) do uwierzytelniania użytkownika. Jednak podczas synchronizacji użytkowników, należy wybrać atrybut, który ma być używany dla wartości userPrincipalName starannie.

### <a name="choosing-the-attribute-for-userprincipalname"></a>Wybieranie atrybutu dla userPrincipalName
Wybierając atrybut dotyczący dostarczania wartości nazwy UPN, która ma być używana na platformie Azure, należy

* Wartości atrybutów są zgodne ze składnią nazwy UPN (RFC 822),\@która powinna mieć nazwę domeny formatu
* Sufiks w wartościach jest zgodny ze zweryfikowaną domeną niestandardową w usłudze Azure AD

W ustawieniach ekspresowych zakładanym wyborem dla atrybutu jest userPrincipalName. Jeśli atrybut userPrincipalName nie zawiera wartości, którą użytkownicy mają zalogować się na platformie Azure, należy wybrać **opcję Instalacja niestandardowa**.

### <a name="custom-domain-state-and-upn"></a>Stan domeny niestandardowej i nazwa UPN
Ważne jest, aby upewnić się, że istnieje zweryfikowana domena dla sufiksu nazwy UPN.

John jest użytkownikiem w contoso.com. Chcesz, aby Jan używał lokalnej sieci\@UPN john contoso.com do logowania się na platformie Azure po zsynchronizowaniu użytkowników z katalogiem usługi Azure AD contoso.onmicrosoft.com. Aby to zrobić, należy dodać i zweryfikować contoso.com jako domenę niestandardową w usłudze Azure AD, zanim będzie można rozpocząć synchronizację użytkowników. Jeśli sufiks nazwy UPN firmy John, na przykład contoso.com, nie jest zgodny ze zweryfikowaną domeną w usłudze Azure AD, usługa Azure AD zastępuje sufiks nazwy UPN contoso.onmicrosoft.com.

### <a name="non-routable-on-premises-domains-and-upn-for-azure-ad"></a>Domeny lokalne niepodjęwalne i numer UPN dla usługi Azure AD
Niektóre organizacje mają domeny nierutowalne, takie jak contoso.local, lub proste domeny pojedynczej etykiety, takie jak contoso. Nie można zweryfikować domeny nie rutowalne w usłudze Azure AD. Usługa Azure AD Connect może synchronizować tylko ze zweryfikowaną domeną w usłudze Azure AD. Podczas tworzenia katalogu usługi Azure AD tworzy domenę routingu, która staje się domeną domyślną dla usługi Azure AD, na przykład contoso.onmicrosoft.com. W związku z tym staje się konieczne, aby zweryfikować inne domeny rutable w takim scenariuszu w przypadku, gdy nie chcesz synchronizować z domyślną domeną onmicrosoft.com.

Przeczytaj artykuł [Dodaj niestandardową nazwę domeny do usługi Azure Active Directory, aby](../active-directory-domains-add-azure-portal.md) uzyskać więcej informacji na temat dodawania i weryfikowania domen.

Usługa Azure AD Connect wykrywa, czy korzystasz w środowisku domeny nie rutowalne i odpowiednio ostrzega przed przejściem do przodu z ustawieniami ekspresowymi. Jeśli działasz w domenie nierutowalnej, prawdopodobnie nazwa UPN użytkowników również ma sufiksy nierutowalne. Na przykład jeśli używasz w obszarze contoso.local, usługa Azure AD Connect sugeruje użycie ustawień niestandardowych, a nie przy użyciu ustawień ekspresowych. Za pomocą ustawień niestandardowych można określić atrybut, który powinien być używany jako nazwy UPN do logowania się na platformie Azure po zsynchronizowanie użytkowników z usługą Azure AD.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
