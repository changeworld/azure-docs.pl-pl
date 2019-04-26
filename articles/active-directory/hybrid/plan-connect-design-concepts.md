---
title: 'Program Azure AD Connect: Projektowanie pojęcia | Dokumentacja firmy Microsoft'
description: Ten temat wyszczególnia niektórych obszarach projektowania wdrożenia
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
ms.openlocfilehash: 311ba489073805fdb034b435ab9e5e1ddc2c4e3c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60382289"
---
# <a name="azure-ad-connect-design-concepts"></a>Program Azure AD Connect: Zagadnienia dotyczące projektowania
Ten dokument ma na celu opisania obszary, które należy uważać za pośrednictwem podczas projektu implementacji programu Azure AD Connect. Ten dokument jest uzyskać szczegółowe informacje dotyczące określonych obszarów i te pojęcia krótko opisano w innych dokumentów.

## <a name="sourceanchor"></a>sourceAnchor
Atrybut sourceAnchor jest zdefiniowany jako *atrybut niezmienialny w okresie istnienia obiektu*. Jednoznacznie identyfikuje obiekt jako ten sam obiekt w środowisku lokalnym i w usłudze Azure AD. Ten atrybut jest również nazywany **immutableId** i dwie nazwy są używane wymienne.

Niezmienne, word, który jest "nie można zmienić", ważne jest, aby w tym dokumencie. Ponieważ wartość tego atrybutu nie można zmienić po jej ustawieniu, jest ważne, aby wybrać projekt, który obsługuje danego scenariusza.

Ten atrybut jest używany w następujących scenariuszach:

* Gdy nowy serwer aparatu synchronizacji jest wbudowane lub odbudować po scenariusza odzyskiwania po awarii, ten atrybut łączy istniejących obiektów w usłudze Azure AD za pomocą obiektów w środowisku lokalnym.
* Po przełączeniu z tożsamości oparte tylko na chmurze modelu tożsamości zsynchronizowane następnie ten atrybut pozwala obiektów "twardych dopasowany" istniejących obiektów w usłudze Azure AD z obiektami w środowisku lokalnym.
* Jeśli używasz Federacji, a następnie tego atrybutu w połączeniu z **userPrincipalName** jest używany w oświadczenie do unikatowego identyfikowania użytkownika.

W tym temacie opowiada sourceAnchor tylko w odniesieniu do użytkowników. Te same zasady mają zastosowanie do wszystkich typów obiektu, ale jest tylko dla użytkowników, że ten problem jest zazwyczaj problemem.

### <a name="selecting-a-good-sourceanchor-attribute"></a>Wybranie atrybutu sourceAnchor dobre
Wartość atrybutu musi postępuj zgodnie z następującymi zasadami:

* Mniej niż 60 znaków
  * Znaki, które nie były a – z, A-Z lub 0-9 kodowania i wliczane do 3 znaków
* Zawiera znaki specjalne: &#92; ! # $ % & * + / = ? ^ &#96; { } | ~ < > ( ) ' ; : , [ ] " \@ _
* Musi być unikatowa w skali globalnej
* Musi być ciąg, liczba całkowita lub dane binarne
* Nie powinna być oparta na nazwę użytkownika, ponieważ mogą one zmienić
* Nie powinny być uwzględniana wielkość liter i uniknąć wartości, które mogą się różnić w przypadku
* Powinien być przypisany, gdy obiekt zostanie utworzony.

Jeśli wybrany sourceAnchor nie jest typu ciąg, następnie usługa Azure AD Connect Base64Encode wartość atrybutu, aby upewnić się, żadnych znaków specjalnych, są wyświetlane. Jeśli używasz innego serwera federacyjnego niż usługi AD FS, upewnij się, serwer może również Base64Encode atrybutu.

Atrybut sourceAnchor jest uwzględniana wielkość liter. Wartość "Jankowalski" nie jest taka sama jak "Jankowalski". Ale nie powinny mieć dwa różne obiekty z różnicą tylko w przypadku.

W przypadku pojedynczego lasu w środowisku lokalnym, następnie należy użyć atrybutu jest **objectGUID**. Dotyczy to również atrybut używany przy użyciu ustawień ekspresowych w usłudze Azure AD Connect, a także atrybut używane przez narzędzie DirSync.

Jeśli masz wiele lasów i nie przenoś użytkowników między lasami i domenami, następnie **objectGUID** jest właściwego atrybutu nawet w takim przypadku.

Jeśli przeniesiesz użytkowników między lasami i domenami, a następnie należy znaleźć atrybut, który nie powoduje zmiany lub mogą zostać przeniesione z użytkownikami, podczas przenoszenia. Zalecanym podejściem jest wprowadzenie syntetycznych atrybutu. Atrybut, który można umieścić coś, wygląda na to odpowiednia może być identyfikatorem GUID. Podczas tworzenia obiektu nowy identyfikator GUID jest tworzony i sygnatury znajdują się na użytkownika. Reguły synchronizacji niestandardowe można tworzyć w serwer aparat synchronizacji, aby utworzyć tę wartość na podstawie **objectGUID** i aktualizowanie wybranego atrybutu w usłudze ADDS. Podczas przenoszenia obiektu, upewnij się, że także skopiować zawartość tej wartości.

Innym rozwiązaniem jest pobranie istniejącego atrybutu znanych nie zmienia się. Często używane atrybuty obejmują **employeeID**. Należy wziąć pod uwagę atrybut, który zawiera litery upewnij się, że można zmienić bez możliwości przypadek (wielkie i małe litery), wartość atrybutu. Nieprawidłowe atrybuty, które nie powinny być używane uwzględnić te atrybuty z nazwą użytkownika. W małżeństwa lub rozwodu nazwa powinien zmienić, co nie jest dozwolone dla tego atrybutu. Ta wartość jest także jednym z powodów dlaczego atrybuty takie jak **userPrincipalName**, **poczty**, i **targetAddress** nie są jeszcze można wybrać w instalacji programu Azure AD Connect Kreator. Zawierają również te atrybuty "\@" znak, który jest niedozwolony w sourceAnchor.

### <a name="changing-the-sourceanchor-attribute"></a>Zmiana atrybutu sourceAnchor
Nie można zmienić wartości atrybutu sourceAnchor, po utworzeniu obiektu w usłudze Azure AD i tożsamość jest zsynchronizowana.

Z tego powodu następujące ograniczenia mają zastosowanie do programu Azure AD Connect:

* Atrybut sourceAnchor można ustawić tylko podczas instalacji początkowej. Jeśli uruchomisz Kreatora instalacji, ta opcja jest tylko do odczytu. Jeśli potrzebujesz zmienić to ustawienie, należy odinstalować i ponownie zainstalować.
* Możesz zainstalować inny serwer program Azure AD Connect, musisz wybrać ten sam atrybut sourceAnchor wcześniej używane. Jeśli wcześniej została przy użyciu narzędzia DirSync i przenieść do usługi Azure AD Connect, a następnie należy użyć **objectGUID** ponieważ atrybut używany przez narzędzie DirSync.
* Jeśli wartość sourceAnchor zostało zmienione po obiekcie zostały wyeksportowane do usługi Azure AD, a następnie program Azure AD Connect sync zgłasza błąd i nie zezwala na jakichkolwiek kolejnych zmian na obiekt, zanim problem został rozwiązany i sourceAnchor zmodyfikowaniu w źródłowej Dyrektor ds. y.

## <a name="using-ms-ds-consistencyguid-as-sourceanchor"></a>Przy użyciu ms-DS-ConsistencyGuid jako sourceAnchor
Domyślnie program Azure AD Connect (wersja 1.1.486.0 i starsze) używa objectGUID jako atrybutu sourceAnchor. ObjectGUID jest generowany przez system. Nie można określić wartość podczas tworzenia w środowisku lokalnym obiektami usługi AD. Zgodnie z opisem w sekcji [sourceAnchor](#sourceanchor), istnieją scenariusze, w którym należy określić wartość sourceAnchor. Scenariusze mają zastosowanie do użytkownika, należy użyć konfigurowalne atrybutu usługi AD (na przykład, ms-DS-ConsistencyGuid) jako atrybut sourceAnchor.

Program Azure AD Connect (wersja w 1.1.524.0 oraz po) teraz ułatwia korzystanie z ms-DS-ConsistencyGuid jako atrybutu sourceAnchor. Podczas korzystania z tej funkcji Azure AD Connect automatycznie konfiguruje reguły synchronizacji:

1. Na użytek ms-DS-ConsistencyGuid jako atrybutu sourceAnchor obiektów użytkowników. ObjectGUID jest używana do innych obiektów.

2. Dla dowolnej podanej w środowisku lokalnym użytkownika AD obiektu, którego atrybut ms-DS-ConsistencyGuid nie jest wypełnione, Azure zapisów AD Connect, jego wartość objectGUID z powrotem do atrybutu ms-DS-ConsistencyGuid w usłudze Active Directory w środowisku lokalnym. Po wypełnieniu atrybutu ms-DS-ConsistencyGuid program Azure AD Connect eksportowany jest obiekt do usługi Azure AD.

>[!NOTE]
> Raz lokalnego obiektu usługi AD jest importowany do usługi Azure AD Connect (który jest importowany do obszaru łącznika usługi AD i odwzorowane w obiekcie Metaverse), nie można już zmienić wartością sourceAnchor. Aby określić wartość sourceAnchor dla podanej w środowisku lokalnym AD obiektu, skonfiguruj jego atrybut ms-DS-ConsistencyGuid, przed zaimportowaniem ich do usługi Azure AD Connect.

### <a name="permission-required"></a>Wymagane jest uprawnienie
Ta funkcja działała konto usług AD DS, używane do synchronizacji z usługą Active Directory w środowisku lokalnym musi otrzymać uprawnienia do zapisu do atrybutu ms-DS-ConsistencyGuid w usłudze Active Directory w środowisku lokalnym.

### <a name="how-to-enable-the-consistencyguid-feature---new-installation"></a>Jak włączyć funkcję ConsistencyGuid — nowa instalacja
Korzystanie z ConsistencyGuid jako sourceAnchor można włączyć podczas instalacji nowego. Ta sekcja obejmuje instalację zarówno Express, jak i niestandardowe, w szczegółach.

  >[!NOTE]
  > Tylko nowsze wersje programu Azure AD Connect (1.1.524.0 oraz po) obsługuje korzystanie z ConsistencyGuid jako sourceAnchor podczas instalacji nowego.

### <a name="how-to-enable-the-consistencyguid-feature"></a>Jak włączyć funkcję ConsistencyGuid
Obecnie tę funkcję można włączyć tylko podczas nowej instalacji usługi Azure AD Connect.

#### <a name="express-installation"></a>Instalacja ekspresowa
Instalując program Azure AD Connect z trybem Express, Kreator Azure AD Connect automatycznie określa najbardziej odpowiedniego atrybutu usługi AD do użycia jako atrybut sourceAnchor przy użyciu logiki poniższym:

* Po pierwsze Kreator Azure AD Connect wysyła zapytanie do dzierżawy usługi Azure AD do pobrania atrybutu usługi AD, używane jako atrybut sourceAnchor w poprzedniej instalacji program Azure AD Connect (jeśli istnieje). Jeśli ta informacja jest dostępna, program Azure AD Connect używa tego samego atrybutu usługi AD.

  >[!NOTE]
  > Tylko nowsze wersje programu Azure AD Connect (1.1.524.0 oraz po) używany do przechowywania informacji w dzierżawie usługi Azure AD o atrybut sourceAnchor, podczas instalacji. Nie są starsze wersje programu Azure AD Connect.

* Jeśli informacje o atrybut sourceAnchor jest niedostępna, Kreator sprawdza stan atrybutu ms-DS-ConsistencyGuid w usłudze Active Directory w środowisku lokalnym. Jeśli ten atrybut nie jest skonfigurowany dla dowolnego obiektu w katalogu, kreator używa ms-DS-ConsistencyGuid jako atrybutu sourceAnchor. Jeśli ten atrybut jest skonfigurowana na jeden lub więcej obiektów w katalogu, Kreator stwierdza, ten atrybut jest używany przez inne aplikacje, a nie nadaje się jako atrybut sourceAnchor...

* W takim przypadku Kreator powraca do korzystania objectGUID jako atrybutu sourceAnchor.

* Gdy atrybut sourceAnchor zostanie podjęta decyzja, kreator zapisuje informacje w dzierżawy usługi Azure AD. Informacje będą używane przez przyszłych instalację programu Azure AD Connect.

Po zakończeniu instalacji ekspresowej Kreator informuje użytkownika, który atrybut został wybrany jako atrybut zakotwiczenia źródła.

![Kreator informuje atrybutu usługi AD wybrany na potrzeby sourceAnchor](./media/plan-connect-design-concepts/consistencyGuid-01.png)

#### <a name="custom-installation"></a>Instalacja niestandardowa
Instalując program Azure AD Connect przy użyciu trybu niestandardowego kreatora programu Azure AD Connect oferuje dwie opcje podczas konfigurowania atrybut sourceAnchor:

![Instalacja niestandardowa - sourceAnchor konfiguracji](./media/plan-connect-design-concepts/consistencyGuid-02.png)

| Ustawienie | Opis |
| --- | --- |
| Pozwól, aby platforma Azure zarządzała zakotwiczeniem źródła | Wybierz tę opcję, jeśli chcesz, aby usługa Azure AD wybierała ten atrybut za Ciebie. Jeśli wybierzesz tę opcję, Kreator Azure AD Connect stosuje się takie same [logikę wyboru atrybutu sourceAnchor używany podczas instalacji ekspresowej](#express-installation). Podobnie jak Instalacja ekspresowa, Kreator informuje użytkownika, który atrybut został wybrany jako atrybut zakotwiczenia źródła po zakończeniu instalacji niestandardowej. |
| Określony atrybut | Wybierz tę opcję, jeśli chcesz określić istniejący atrybut usługi AD jako atrybut sourceAnchor. |

### <a name="how-to-enable-the-consistencyguid-feature---existing-deployment"></a>Jak włączyć funkcję ConsistencyGuid - istniejącego wdrożenia
Za pomocą ConsistencyGuid zamiast tego można zmienić istniejącego wdrożenia usługi Azure AD Connect, który używa objectGUID jako atrybutu zakotwiczenia źródła.

>[!NOTE]
> Tylko nowsze wersje programu Azure AD Connect (1.1.552.0 oraz po) obsługuje przełączania ObjectGuid ConsistencyGuid jako atrybutu zakotwiczenia źródła.

Aby przełączyć się z objectGUID ConsistencyGuid jako atrybutu zakotwiczenia źródła:

1. Uruchom Kreatora programu Azure AD Connect, a następnie kliknij przycisk **Konfiguruj** można przejść do ekranu zadania.

2. Wybierz **Konfiguruj zakotwiczenie źródła** zadań opcji, a następnie kliknij przycisk **dalej**.

   ![Włącz ConsistencyGuid dla istniejącego wdrożenia — krok 2](./media/plan-connect-design-concepts/consistencyguidexistingdeployment01.png)

3. Wprowadź swoje poświadczenia administratora usługi Azure AD, a następnie kliknij przycisk **dalej**.

4. Kreator Azure AD Connect analizuje stan atrybutu ms-DS-ConsistencyGuid w usłudze Active Directory w środowisku lokalnym. Jeśli ten atrybut nie jest skonfigurowany dla dowolnego obiektu w katalogu, program Azure AD Connect stwierdza, że żadna inna aplikacja używa obecnie atrybut i bezpiecznym rozwiązaniem jest użycie go jako atrybut zakotwiczenia źródła. Kliknij przycisk **dalej** aby kontynuować.

   ![Włącz ConsistencyGuid dla istniejącego wdrożenia — krok 4](./media/plan-connect-design-concepts/consistencyguidexistingdeployment02.png)

5. W **gotowe do konfiguracji** ekranu, kliknij przycisk **Konfiguruj** umożliwiają zmianę konfiguracji.

   ![Włącz ConsistencyGuid dla istniejącego wdrożenia — krok 5](./media/plan-connect-design-concepts/consistencyguidexistingdeployment03.png)

6. Po zakończeniu konfiguracji kreatora oznacza, że jest teraz używany tego ms-DS-ConsistencyGuid jako atrybutu zakotwiczenia źródła.

   ![Włącz ConsistencyGuid dla istniejącego wdrożenia — krok 6](./media/plan-connect-design-concepts/consistencyguidexistingdeployment04.png)

Podczas analizy (krok 4) Jeśli ten atrybut jest skonfigurowany na jeden lub więcej obiektów w katalogu, Kreator stwierdza, ten atrybut jest używany przez inną aplikację i zwraca błąd, jak pokazano na poniższym diagramie. Ten błąd może również wystąpić, jeśli włączono funkcję ConsistencyGuid na swojej głównej usługi Azure AD Connect serwer próbuje tym samym serwerze przejściowym.

![Włącz ConsistencyGuid dla istniejącego wdrożenia — błąd](./media/plan-connect-design-concepts/consistencyguidexistingdeploymenterror.png)

 Jeśli masz pewność, że atrybut nie jest używany przez inne istniejące aplikacje, można pominąć ten błąd, uruchamiając ponownie kreatora Azure AD Connect przy użyciu **/SkipLdapSearch** określony przełącznik. Aby to zrobić, uruchom następujące polecenie w wierszu polecenia:

```
"c:\Program Files\Microsoft Azure Active Directory Connect\AzureADConnect.exe" /SkipLdapSearch
```

### <a name="impact-on-ad-fs-or-third-party-federation-configuration"></a>Wpływ na usług AD FS lub Konfiguracja Federacji innej firmy
Jeśli używasz usługi Azure AD Connect do zarządzania lokalnego wdrożenia usług AD FS, Azure AD Connect automatycznie aktualizuje reguł oświadczeń, aby użyć tego samego atrybutu usługi AD jako sourceAnchor. Zapewnia to, że oświadczenia ImmutableID generowanych przez usługi AD FS jest zgodne z wartościami sourceAnchor wyeksportowane do usługi Azure AD.

Jeśli zarządzasz usług AD FS poza programem Azure AD Connect lub używasz serwery federacyjne innych firm do uwierzytelniania, należy ręcznie zaktualizować reguły oświadczeń na oświadczenia ImmutableID były zgodne z wartościami sourceAnchor wyeksportowane do usługi Azure AD, zgodnie z opisem w Artykuł sekcji [Modyfikuj usługi AD FS roszczenie reguł](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-management#modclaims). Po zakończeniu instalacji, Kreator wyświetli następujące ostrzeżenie:

![Konfiguracja Federacji innej firmy](./media/plan-connect-design-concepts/consistencyGuid-03.png)

### <a name="adding-new-directories-to-existing-deployment"></a>Dodawanie nowych katalogów do istniejącego wdrożenia
Załóżmy, że wdrożono program Azure AD Connect z włączoną funkcją ConsistencyGuid, a teraz chcesz dodać innego katalogu do wdrożenia. Podczas próby dodania katalogu, Kreator Azure AD Connect sprawdza stan atrybutu ms-DS-ConsistencyGuid w katalogu. Jeśli ten atrybut jest skonfigurowana na jeden lub więcej obiektów w katalogu, Kreator stwierdza, ten atrybut jest używany przez inne aplikacje i zwraca błąd, jak pokazano na poniższym diagramie. Jeśli masz pewność, że ten atrybut nie jest używane przez istniejące aplikacje, można pominąć ten błąd, uruchamiając ponownie kreatora Azure AD Connect przy użyciu **/SkipLdapSearch** przełącznika określony w opisany powyżej, lub skontaktuj się z działem Obsługa, aby uzyskać więcej informacji.

![Dodawanie nowych katalogów do istniejącego wdrożenia](./media/plan-connect-design-concepts/consistencyGuid-04.png)

## <a name="azure-ad-sign-in"></a>Logowanie się w usłudze Azure AD
Podczas integracji katalogu lokalnego z usługą Azure AD, ważne jest, aby zrozumieć, jak ustawienia synchronizacji wpływa na sposób użytkownik jest uwierzytelniany. Usługa Azure AD używa userPrincipalName (UPN) do uwierzytelnienia użytkownika. Jednak po zsynchronizowaniu użytkowników, możesz wybrać atrybut, który ma być używany dla wartości userPrincipalName ostrożnie.

### <a name="choosing-the-attribute-for-userprincipalname"></a>Wybieranie atrybut userPrincipalName
Podczas wybierania atrybut zapewniające wartość nazwy UPN do użycia w jednej platformy Azure należy upewnić się

* Format nazwy użytkownika powinny być atrybutu wartości odpowiadają Składnia nazwy UPN (RFC 822), która jest\@domeny
* Sufiks w wartościach pasuje do jednej z zweryfikowanym domenom niestandardowym w usłudze Azure AD

W ustawieniach ekspresowych zakładanego dla atrybutu to userPrincipalName. Jeśli atrybut userPrincipalName nie zawiera wartości mają użytkownikom logowanie do platformy Azure, a następnie należy wybrać **Instalacja niestandardowa**.

### <a name="custom-domain-state-and-upn"></a>Stan domeny niestandardowe i UPN
Należy upewnić się, że zweryfikowaną domeną sufiks głównej nazwy użytkownika.

John jest użytkownikiem w domenie contoso.com. Chcesz Jan używać Jan nazwy UPN w środowisku lokalnym\@domeny contoso.com Zaloguj się do platformy Azure, po zsynchronizowaniu użytkowników do usługi Azure AD directory contoso.onmicrosoft.com. Aby to zrobić, należy dodać i zweryfikować domeny contoso.com jako domeny niestandardowej w usłudze Azure AD przed rozpoczęciem synchronizowania użytkowników. Jeśli sufiks głównej nazwy użytkownika John, na przykład "contoso.com", nie jest zgodna zweryfikowanej domeny w usłudze Azure AD, następnie usługi Azure AD zastępuje sufiks nazwy UPN contoso.onmicrosoft.com.

### <a name="non-routable-on-premises-domains-and-upn-for-azure-ad"></a>Bez obsługi routingu w domenach lokalnych i nazwy UPN w usłudze Azure AD
Niektóre organizacje mają domen niepodlegających routingowi, np. contoso.local lub prostą nazwą domeny, np. contoso. Nie jest możliwe zweryfikować domenę bez obsługi routingu w usłudze Azure AD. Program Azure AD Connect można zsynchronizować do zweryfikowanej domeny w usłudze Azure AD. Podczas tworzenia katalogu usługi Azure AD tworzy Routing domeny, która stanie się domyślną domenę usługi Azure AD, np. contoso.onmicrosoft.com. W związku z tym staje się niezbędne do sprawdzenia innej domeny routingu w takiej sytuacji, w przypadku, gdy nie chcesz zsynchronizować z domyślnej domeny onmicrosoft.com.

Odczyt [Dodawanie niestandardowej nazwy domeny do usługi Azure Active Directory](../active-directory-domains-add-azure-portal.md) uzyskać więcej informacji dotyczących dodawania i weryfikowania domeny.

Program Azure AD Connect wykrywa, jeśli są uruchomione w środowisku domeny bez obsługi routingu i będzie odpowiednio ostrzega użytkownika z wyprzedzeniem, korzystając z ustawień ekspresowych. Jeśli pracujesz w domenie bez obsługi routingu, następnie prawdopodobnie nazwy UPN użytkowników, zbyt mieć sufiksy bez obsługi routingu. Na przykład, jeśli jest używane do uruchamiania contoso.local, program Azure AD Connect sugeruje przy użyciu ustawień niestandardowych, a nie przy użyciu ustawień ekspresowych. Za pomocą ustawień niestandardowych, jesteś w stanie określić atrybut, który powinien służyć jako głównej nazwy użytkownika do logowania się do platformy Azure, po zsynchronizowaniu użytkowników do usługi Azure AD.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
