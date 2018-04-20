---
title: 'Azure AD Connect: Zagadnienia dotyczące projektowania | Dokumentacja firmy Microsoft'
description: W tym temacie szczegółowo niektórych obszarach projektowania implementacji
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 4114a6c0-f96a-493c-be74-1153666ce6c9
ms.service: active-directory
ms.custom: azure-ad-connect
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 179a669e4c9567950d22ed76a693ec6ab7a2db8d
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2018
---
# <a name="azure-ad-connect-design-concepts"></a>Azure AD Connect: Zagadnienia dotyczące projektowania
Celem tego tematu jest do opisywania obszarów, które muszą być uważane za pośrednictwem podczas projektu implementacji programu Azure AD Connect. Ten temat jest nowości w niektórych obszarach i tych pojęć krótko opisano w innych tematach również.

## <a name="sourceanchor"></a>sourceAnchor
Atrybut sourceAnchor jest zdefiniowany jako *atrybutu niezmienialny w okresie istnienia obiektu*. Identyfikuje obiekt jako tego samego obiektu lokalnej i w usłudze Azure AD. Ten atrybut jest również nazywany **nazwę immutableId** i obie nazwy są używane wymienne.

Niezmienne, word, który jest "nie można zmienić", ważne jest, aby w tym temacie. Ponieważ wartość tego atrybutu nie można zmienić po ustawieniu, należy wybrać projekt, który obsługuje danego scenariusza.

Ten atrybut jest używany w następujących scenariuszach:

* Gdy nowy serwer aparatu synchronizacji jest skompilowany lub ponownie skompilowany od scenariusza odzyskiwania po awarii, ten atrybut łączy istniejące obiekty w usłudze Azure AD z obiektami lokalnymi.
* Po przełączeniu z tożsamością tylko na chmurze do modelu synchronizowanych tożsamości następnie ten atrybut umożliwia obiektów w istniejących obiektach "twardych match" w usłudze Azure AD z lokalnymi obiektami.
* Jeśli używasz federacyjnego, a następnie ten atrybut razem z **userPrincipalName** jest używany w oświadczeń do unikatowego identyfikowania użytkownika.

W tym temacie wspomniana sourceAnchor tylko w odniesieniu do użytkowników. Te same zasady dotyczą wszystkich typów obiektów, ale tego problemu jest zwykle problemem jest tylko dla użytkowników.

### <a name="selecting-a-good-sourceanchor-attribute"></a>Wybranie atrybutu sourceAnchor dobra
Wartość atrybutu, należy wykonać następujące reguły:

* Zawierać mniej niż 60 znaków
  * Kodowania i liczone jak znaki 3 znaki nie są a-z, A-Z i 0-9
* Zawiera znaki specjalne: &#92; ! # $ % & * + / = ? ^ &#96; { } | ~ < > ( ) ' ; : , [ ] " @ _
* Musi być globalnie unikatowa
* Musi być ciągiem, liczbą całkowitą lub danych binarnych
* Nie powinna być oparta na nazwę użytkownika, te zmiany
* Nie powinny być uwzględniana wielkość liter i uniknąć wartości, które mogą różnić się wielkością liter
* Powinien być przypisany podczas tworzenia obiektu

Jeśli wybrane sourceAnchor nie jest typu String, następnie Azure AD Connect Base64Encode wartość atrybutu, aby upewnić się, znaki specjalne są wyświetlane. Użycie innego serwera federacyjnego niż usługi AD FS, upewnij się, serwer może również Base64Encode atrybutu.

Atrybut sourceAnchor jest rozróżniana wielkość liter. Wartość "JanNowak" nie jest taka sama jak "JanNowak". Ale nie powinien mieć dwa różne obiekty z różnicą tylko wielkością liter.

Jeśli masz jednego lasu w infrastrukturze lokalnej, następnie należy użyć atrybutu jest **objectGUID**. Dotyczy to również atrybut używane w przypadku użycia ustawień ekspresowych w programie Azure AD Connect, a także atrybutu używane przez narzędzie DirSync.

Jeśli masz wiele lasów i nie przenoś użytkowników między lasami i domenami, następnie **objectGUID** jest właściwego atrybutu nawet w tym przypadku.

Jeśli użytkownicy są przenoszone między lasami i domenami, następnie należy znaleźć atrybut, który nie ulega zmianie lub mogą zostać przeniesione z użytkownikami podczas przenoszenia. Zalecanym podejściem jest wprowadzenie syntetycznych atrybutu. Atrybut, który można umieścić element, który wygląda na to odpowiednia może być identyfikatorem GUID. Podczas tworzenia obiektu nowego identyfikatora GUID jest tworzony i dołączana do użytkownika. Reguły niestandardowe synchronizacji można utworzyć na serwerze aparatu synchronizacji, aby utworzyć tę wartość na podstawie **objectGUID** i zaktualizuj w DODAJE wybranego atrybutu. Podczas przenoszenia obiektu, upewnij się, że również skopiuj zawartość tej wartości.

Innym rozwiązaniem jest wybierz istniejący atrybut, których znasz nie ulega zmianie. Często używane atrybuty obejmują **identyfikator pracownika**. Należy wziąć pod uwagę atrybut, który zawiera litery, aby upewnić się, że można zmienić bez możliwości przypadku (wielkie i małe litery), wartość atrybutu. Złe atrybuty, które nie powinny być używane obejmują tych atrybutów o tej nazwie użytkownika. Unieważnienie lub rozwodu Oczekiwano nazwy do zmiany, co nie jest dozwolone dla tego atrybutu. Dotyczy to również jedną z przyczyn Dlaczego atrybutów, takich jak **userPrincipalName**, **poczty**, i **targetAddress** nie są jeszcze można wybrać w Kreatorze instalacji Azure AD Connect. Te atrybuty również zawierać "@" znak, który jest niedozwolony w sourceAnchor.

### <a name="changing-the-sourceanchor-attribute"></a>Zmiana atrybutu sourceAnchor
Wartość atrybutu sourceAnchor nie można zmienić po utworzeniu obiektu w usłudze Azure AD i tożsamość jest zsynchronizowana.

Z tego powodu następujące ograniczenia są stosowane do programu Azure AD Connect:

* Atrybut sourceAnchor można ustawić tylko podczas instalacji początkowej. Jeśli uruchomisz Kreatora instalacji, ta opcja jest tylko do odczytu. Jeśli potrzebujesz zmienić to ustawienie, należy odinstalować i zainstalować ponownie.
* Zainstalowanie innego serwera Azure AD Connect, a następnie należy wybrać jako korzystał już z tego samego atrybutu sourceAnchor. Jeśli wcześniej została przy użyciu narzędzia DirSync i przenieść do programu Azure AD Connect, a następnie należy użyć **objectGUID** ponieważ jest używane przez narzędzie DirSync atrybutu.
* Jeśli wartość dla elementu sourceAnchor zostało zmienione po obiektu zostały wyeksportowane do usługi Azure AD, a następnie Azure AD Connect synchronizacji wygeneruje błąd i nie zezwala na zmiany w obiekt przed problem został rozwiązany i sourceAnchor zostanie zmieniona w katalogu źródłowym.

## <a name="using-msds-consistencyguid-as-sourceanchor"></a>Przy użyciu msDS-ConsistencyGuid jako sourceAnchor
Domyślnie program Azure AD Connect (wersja 1.1.486.0 i starszych) używa objectGUID jako atrybut sourceAnchor. Atrybut ObjectGUID jest generowanych przez system. Nie można określić jego wartości podczas tworzenia lokalnych obiektów usługi AD. Jak opisano w sekcji [sourceAnchor](#sourceanchor), istnieją scenariusze, w którym należy określić wartość sourceAnchor. Jeśli scenariusze są stosowane do użytkownika, należy użyć można skonfigurować atrybut AD (na przykład msDS-ConsistencyGuid) jako atrybut sourceAnchor.

Azure AD Connect (wersja 1.1.524.0 oraz po) ułatwia teraz użycie msDS-ConsistencyGuid jako atrybut sourceAnchor. Podczas korzystania z tej funkcji Azure AD Connect automatycznie konfiguruje reguły synchronizacji do:

1. Użyj msDS-ConsistencyGuid jako atrybut sourceAnchor obiektów użytkownika. Atrybut ObjectGUID jest używana do innych obiektów.

2. Dla żadnej podanej lokalnych użytkowników usługi AD obiektu, którego atrybut msDS-ConsistencyGuid nie jest wypełniony, Azure zapisy AD Connect, jego wartość objectGUID z powrotem do atrybutu msDS-ConsistencyGuid w lokalnej usłudze Active Directory. Po atrybutu msDS-ConsistencyGuid zostanie wypełnione, Azure AD Connect następnie eksportuje obiektu do usługi Azure AD.

>[!NOTE]
> Raz lokalnego obiektu usługi Active Directory jest importowany do usługi Azure AD Connect (który jest zaimportowany do przestrzeni łącznika usługi AD i w magazynie Metaverse), nie można już zmienić jej wartość sourceAnchor. Aby określić wartość sourceAnchor podane lokalnymi AD obiektów, skonfiguruj jego atrybut msDS-ConsistencyGuid przed ich zaimportowaniem do usługi Azure AD Connect.

### <a name="permission-required"></a>Wymagane uprawnienia
Ta funkcja działała konto usług AD DS, używane do synchronizowania z lokalną usługą Active Directory musi mieć przyznane uprawnienia zapisu do atrybutu msDS-ConsistencyGuid w lokalnej usłudze Active Directory.

### <a name="how-to-enable-the-consistencyguid-feature---new-installation"></a>Jak włączyć funkcję ConsistencyGuid — nowa instalacja
Użycie ConsistencyGuid jako sourceAnchor można włączyć podczas instalacji nowego. W tej sekcji omówiono zarówno Express, jak i niestandardowe instalacji w szczegółach.

  >[!NOTE]
  > Tylko nowsze wersje programu Azure AD Connect (1.1.524.0 oraz po) obsługuje korzystanie z ConsistencyGuid jako sourceAnchor podczas instalacji nowego.

### <a name="how-to-enable-the-consistencyguid-feature"></a>Jak włączyć funkcję ConsistencyGuid
Obecnie funkcję można włączyć tylko podczas nowej instalacji usługi Azure AD Connect.

#### <a name="express-installation"></a>Instalacja ekspresowa
Podczas instalacji programu Azure AD Connect z trybem Express, Kreator Azure AD Connect automatycznie określa najbardziej odpowiedni atrybut AD ma być używana jako atrybut sourceAnchor przy użyciu z następującą logiką:

* Po pierwsze Kreator Azure AD Connect wysyła zapytanie do dzierżawy usługi Azure AD można pobrać atrybutu AD używane jako atrybut sourceAnchor w poprzedniej instalacji usługi Azure AD Connect (jeśli istnieje). Jeśli te informacje są dostępne, Azure AD Connect używa tego samego atrybutu AD.

  >[!NOTE]
  > Tylko nowsze wersje programu Azure AD Connect (1.1.524.0 oraz po) magazynów informacje w dzierżawie usługi Azure AD ten atrybut sourceAnchor jest używany podczas instalacji. Starsze wersje programu Azure AD Connect, czy nie.

* Jeśli informacje o atrybut sourceAnchor jest niedostępna, Kreator sprawdza, czy stan atrybutu msDS-ConsistencyGuid w lokalnej usługi Active Directory. Jeśli ten atrybut nie jest skonfigurowana dla dowolnego obiektu w katalogu, kreator użyje msDS-ConsistencyGuid jako atrybut sourceAnchor. Jeśli ten atrybut został skonfigurowany w co najmniej jednego obiektu w katalogu, Kreator zawiera atrybut jest używany przez inne aplikacje i nie nadaje się jako atrybut sourceAnchor...

* W takim przypadku Kreator powraca do korzystania objectGUID jako atrybut sourceAnchor.

* Gdy atrybut sourceAnchor jest decyzję, kreator zapisuje informacje w dzierżawie usługi Azure AD. Informacje będą używane w przyszłości instalacji programu Azure AD Connect.

Po zakończeniu instalacji ekspresowej, Kreator informuje, atrybut, którego została pobrana jako atrybut zakotwiczenia źródła.

![Kreator informuje pobrane dla elementu sourceAnchor atrybutów AD](./media/active-directory-aadconnect-design-concepts/consistencyGuid-01.png)

#### <a name="custom-installation"></a>Instalacja niestandardowa
Podczas instalacji programu Azure AD Connect z trybem niestandardowe, podczas konfigurowania atrybutu sourceAnchor Kreatora programu Azure AD Connect będzie oferuje dwie opcje:

![Instalacja niestandardowa - sourceAnchor konfiguracji](./media/active-directory-aadconnect-design-concepts/consistencyGuid-02.png)

| Ustawienie | Opis |
| --- | --- |
| Pozwól, aby platforma Azure zarządzała zakotwiczeniem źródła | Wybierz tę opcję, jeśli chcesz, aby usługa Azure AD wybierała ten atrybut za Ciebie. Jeśli wybierzesz tę opcję, Kreator Azure AD Connect stosuje takie same [logiki wybór atrybutu sourceAnchor używane podczas instalacji ekspresowej](#express-installation). Podobnie jak Instalacja ekspresowa, Kreator informuje o atrybut, którego została pobrana jako atrybut zakotwiczenia źródła po zakończeniu instalacji niestandardowej. |
| Określony atrybut | Wybierz tę opcję, jeśli chcesz określić istniejący atrybut usługi AD jako atrybut sourceAnchor. |

### <a name="how-to-enable-the-consistencyguid-feature---existing-deployment"></a>Jak włączyć funkcję ConsistencyGuid - istniejącego wdrożenia
Jeśli masz istniejące wdrożenie usługi Azure AD Connect, za pomocą objectGUID jako atrybut zakotwiczenia źródła, możesz przełączyć go do zamiast ConsistencyGuid.

>[!NOTE]
> Tylko nowsze wersje programu Azure AD Connect (1.1.552.0 oraz po) obsługuje przełączania ObjectGuid ConsistencyGuid jako atrybut zakotwiczenia źródła.

Aby przełączyć się z objectGUID ConsistencyGuid jako atrybut zakotwiczenia źródła:

1. Uruchom Kreatora programu Azure AD Connect i kliknij przycisk **Konfiguruj** aby przejść do ekranu zadań.

2. Wybierz **Konfiguruj zakotwiczenia źródła** zadań opcję i kliknij przycisk **dalej**.

   ![Włącz ConsistencyGuid dla istniejącego wdrożenia — krok 2](./media/active-directory-aadconnect-design-concepts/consistencyguidexistingdeployment01.png)

3. Wprowadź swoje poświadczenia usługi Azure AD administratora, a następnie kliknij przycisk **dalej**.

4. Kreator Azure AD Connect analizuje stan atrybutu msDS-ConsistencyGuid w lokalnej usługi Active Directory. Jeśli ten atrybut nie jest skonfigurowana dla dowolnego obiektu w katalogu, Azure AD Connect stwierdza, że żadna inna aplikacja używa obecnie atrybut i jest bezpieczne używać go jako atrybut zakotwiczenia źródła. Kliknij przycisk **dalej** aby kontynuować.

   ![Włącz ConsistencyGuid dla istniejącego wdrożenia — krok 4](./media/active-directory-aadconnect-design-concepts/consistencyguidexistingdeployment02.png)

5. W **gotowe do konfiguracji** kliknij **Konfiguruj** aby zmiany konfiguracji.

   ![Włącz ConsistencyGuid dla istniejącego wdrożenia — krok 5](./media/active-directory-aadconnect-design-concepts/consistencyguidexistingdeployment03.png)

6. Po zakończeniu konfiguracji kreatora wskazuje, że ten atrybut msDS-ConsistencyGuid jest teraz używany jako atrybut zakotwiczenia źródła.

   ![Włącz ConsistencyGuid dla istniejącego wdrożenia — krok 6](./media/active-directory-aadconnect-design-concepts/consistencyguidexistingdeployment04.png)

Podczas analizy (krok 4) Jeśli ten atrybut jest skonfigurowany w co najmniej jednego obiektu w katalogu, Kreator stwierdza, ten atrybut jest używany przez inną aplikację i zwraca błąd, jak pokazano na poniższym diagramie. Ten błąd może również wystąpić, jeśli uprzednio włączono funkcję ConsistencyGuid na sieci podstawowej Azure AD Connect server i próbujesz wykonaj te same na serwerze tymczasowe.

![Włącz ConsistencyGuid dla istniejącego wdrożenia — błąd](./media/active-directory-aadconnect-design-concepts/consistencyguidexistingdeploymenterror.png)

 Jeśli masz pewność, że atrybut nie jest używany przez inne istniejące aplikacje, kod błędu można pominąć, uruchamiając Kreator Azure AD Connect z **/SkipLdapSearchcontact** określony. Aby to zrobić, uruchom następujące polecenie w wierszu polecenia:

```
"c:\Program Files\Microsoft Azure Active Directory Connect\AzureADConnect.exe" /SkipLdapSearch
```

### <a name="impact-on-ad-fs-or-third-party-federation-configuration"></a>Wpływ na usługi AD FS lub konfiguracji federacyjne innych firm
Jeśli używasz usługi Azure AD Connect do zarządzania lokalnymi wdrożenia usług AD FS, Azure AD Connect automatycznie aktualizuje reguł oświadczeń, aby użyć tego samego atrybutu AD jako sourceAnchor. Dzięki temu, że oświadczenia nazwę ImmutableID generowanych przez usługi AD FS jest zgodne z wartościami sourceAnchor wyeksportowane do usługi Azure AD.

Jeśli zarządzasz usług AD FS poza Azure AD Connect lub serwery federacyjne innych firm jest używany do uwierzytelniania, należy ręcznie zaktualizować reguły oświadczeń nazwę ImmutableID oświadczenia były spójne z wartościami sourceAnchor wyeksportowane do usługi Azure AD, zgodnie z opisem w sekcji artykule [reguł oświadczeń Modyfikuj AD FS](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-management#modclaims). Po zakończeniu instalacji, Kreator zwraca następujące ostrzeżenie:

![Konfiguracja federacyjnego innych firm](./media/active-directory-aadconnect-design-concepts/consistencyGuid-03.png)

### <a name="adding-new-directories-to-existing-deployment"></a>Dodawanie nowych katalogów do istniejącego wdrożenia
Załóżmy, że wdrożono Azure AD Connect z włączoną funkcją ConsistencyGuid, a teraz chcesz dodać innego katalogu do wdrożenia. Podczas próby dodania katalogu, Kreator Azure AD Connect sprawdza stan atrybutu mSDS-ConsistencyGuid w katalogu. Jeśli ten atrybut został skonfigurowany w co najmniej jednego obiektu w katalogu, Kreator stwierdza, ten atrybut jest używany przez inne aplikacje i zwraca błąd, jak pokazano na poniższym diagramie. Jeśli masz pewność, że ten atrybut nie jest używane przez istniejące aplikacje, należy skontaktować się z obsługą informacji dotyczących Pomiń błąd.

![Dodawanie nowych katalogów do istniejącego wdrożenia](./media/active-directory-aadconnect-design-concepts/consistencyGuid-04.png)

## <a name="azure-ad-sign-in"></a>Logowanie się w usłudze Azure AD
Podczas integracji katalogu lokalnego z usługą Azure AD, ważne jest, aby zrozumieć, jak ustawienia synchronizacji wpływa na sposób użytkownik zostanie uwierzytelniony. Usługi Azure AD używa userPrincipalName (UPN) w celu uwierzytelnienia użytkownika. Jednak po zsynchronizowaniu użytkowników, musisz wybrać atrybut służący do wartości userPrincipalName uważnie.

### <a name="choosing-the-attribute-for-userprincipalname"></a>Wybieranie atrybutu userPrincipalName
Podczas wybierania atrybutu zapewniające powinno wartość UPN do użycia w przypadku platformy Azure

* Wartości atrybutów odpowiada składni UPN (RFC 822), który znajduje się powinna mieć ona format username@domain
* Sufiks w wartości pasuje do jednej z zweryfikowanych domen niestandardowych w usłudze Azure AD

W ustawieniach ekspresowych zakładanego wybór dla atrybutu jest userPrincipalName. Jeśli atrybut userPrincipalName nie zawiera wartości chcesz użytkownikom logowanie do platformy Azure, a następnie należy wybrać **Instalacja niestandardowa**.

### <a name="custom-domain-state-and-upn"></a>Stan domeny niestandardowe i UPN
Koniecznie upewnij się, że jest zweryfikowanej domeny dla sufiks głównej nazwy użytkownika.

Jan jest użytkownikiem w domenie contoso.com. Ma Jan do użycia z lokalną nazwą UPN john@contoso.com logować się do platformy Azure po zsynchronizowaniu użytkowników do Twojej contoso.onmicrosoft.com katalogu usługi Azure AD. Aby to zrobić, należy dodać i zweryfikować domeny contoso.com jako domeny niestandardowej w usłudze Azure AD, przed rozpoczęciem synchronizowania użytkowników. Jeśli sufiks nazwy UPN John, na przykład contoso.com, nie odpowiada zweryfikowanej domeny w usłudze Azure AD, następnie usługi Azure AD zastępuje sufiks nazwy UPN contoso.onmicrosoft.com.

### <a name="non-routable-on-premises-domains-and-upn-for-azure-ad"></a>UPN dla usługi Azure AD i lokalnej obsługi routingu domen
Niektóre organizacje mają nierutowalny domen, takich jak contoso.local lub prostego domen jak contoso. Nie jest możliwe zweryfikować domeny bez obsługi routingu w usłudze Azure AD. Azure AD Connect można synchronizację zweryfikowanej domeny w usłudze Azure AD. Podczas tworzenia katalogu usługi Azure AD, tworzy domenę routingu, która staje się domyślną domenę usługi Azure AD na przykład contoso.onmicrosoft.com. W związku z tym staje się zweryfikowanie innej domeny routingu w takiej sytuacji, w przypadku, gdy nie chcesz synchronizować domyślnej domeny onmicrosoft.com.

Odczyt [Dodawanie niestandardowej nazwy domeny do usługi Azure Active Directory](../active-directory-domains-add-azure-portal.md) uzyskać więcej informacji dotyczących dodawania i weryfikowania domeny.

Wykrywa Azure AD Connect, jeśli są uruchomione w środowisku domeny bez obsługi routingu i będzie odpowiednio ostrzegać z wyprzedzeniem korzystanie z ustawień ekspresowych. Jeśli pracujesz w domenie bez obsługi routingu, następnie prawdopodobnie nazwy UPN użytkowników zbyt ma sufiksy bez obsługi routingu. Na przykład, jeśli używane do uruchamiania contoso.local, Azure AD Connect sugeruje można użyć ustawienia niestandardowe, a nie przy użyciu ustawień ekspresowych. Za pomocą ustawień niestandardowych, będą mogli określać atrybut, które mają być używane jako nazwy UPN do logowania do platformy Azure po zsynchronizowaniu użytkowników do usługi Azure AD.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej na temat [integrowania tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md).
