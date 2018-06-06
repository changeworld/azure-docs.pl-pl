---
title: 'Azure AD Connect: Rozwiązywanie problemów z błędami podczas synchronizacji | Dokumentacja firmy Microsoft'
description: Wyjaśniono, jak rozwiązywać problemy podczas synchronizacji z programem Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 2209d5ce-0a64-447b-be3a-6f06d47995f8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: c38187221e7cd4e3244199e713f41be0005eb024
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34801885"
---
# <a name="troubleshooting-errors-during-synchronization"></a>Rozwiązywanie problemów z błędami podczas synchronizacji
Podczas synchronizowania danych tożsamości z usługi Windows Server Active Directory (AD DS) do usługi Azure Active Directory (Azure AD), mogą wystąpić błędy. Ten artykuł zawiera omówienie różnych typów błędów synchronizacji, niektóre możliwe scenariusze, które powodują tych błędów i potencjalnych sposobów, aby naprawić błędy. Ten artykuł zawiera typowych błędów i może nie obejmować wszystkich możliwych błędów.

 W tym artykule przyjęto czytnik są zaznajomieni z podstawową [projektu usługi Azure AD i Azure AD Connect](active-directory-aadconnect-design-concepts.md).

Do najnowszej wersji programu Azure AD Connect \(sierpnia 2016 lub nowszego\), raport błędów synchronizacji jest dostępna w [Azure Portal](https://aka.ms/aadconnecthealth) jako część usługi Azure AD Connect Health dla synchronizacji.

Od 1 września 2016 [Azure Active Directory zduplikowany atrybut odporności](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md) funkcja zostanie włączona domyślnie dla wszystkich *nowe* usługi Azure Active Directory dzierżaw. Ta funkcja będzie można automatycznie włączona dla istniejących dzierżawców w ciągu przyszłych miesięcy.

Azure AD Connect wykonuje trzy rodzaje operacji z zapewnia synchronizację katalogów: Import, synchronizacji i eksportu. Błędy mogą mieć miejsce w wszystkie operacje. W tym artykule skupiono się głównie na błędy podczas eksportowania do usługi Azure AD.

## <a name="errors-during-export-to-azure-ad"></a>Błędy podczas eksportowania do usługi Azure AD
Następujące sekcja zawiera opis różnych typów błędów synchronizacji, które mogą wystąpić podczas operacji eksportowania do usługi Azure AD za pomocą łącznika usługi Azure AD. Ten łącznik można zidentyfikować przez format nazwy jest "contoso. *onmicrosoft.com*".
Błędy podczas eksportowania do usługi Azure AD wskazują, że operacja \(Dodawanie, aktualizowanie i usuwanie itp.\) próba przy użyciu usługi Azure AD Connect \(aparatu synchronizacji\) w usłudze Azure Active Directory nie powiodło się.

![Omówienie błędy eksportu](./media/active-directory-aadconnect-troubleshoot-sync-errors/Export_Errors_Overview_01.png)

## <a name="data-mismatch-errors"></a>Błędy niezgodność typów danych
### <a name="invalidsoftmatch"></a>InvalidSoftMatch
#### <a name="description"></a>Opis
* Gdy programu Azure AD Connect \(aparatu synchronizacji\) powoduje, że usługi Azure Active Directory, aby dodać lub zaktualizować obiektów, usługi Azure AD odpowiada na przychodzące za pomocą obiektu **sourceAnchor** atrybutu **nazwę immutableId** atrybutu obiektów w usłudze Azure AD. Nosi nazwę tego dopasowania **twardych odpowiada**.
* Podczas usługi Azure AD **nie znajdzie** dowolnego obiektu, który pasuje **nazwę immutableId** atrybutem **sourceAnchor** atrybutu przychodzące obiektu przed zainicjowaniem obsługi administracyjnej nowy obiekt, jego powraca do wyszukiwać dopasowania przy użyciu atrybutów ProxyAddresses i UserPrincipalName. Nosi nazwę tego dopasowania **nietrwałego odpowiada**. Słabe zgodne zaprojektowano w celu odpowiada obiektów znajduje się już w usłudze Azure AD (które biorą się w usłudze Azure AD) obiektami nowego jest dodać/zaktualizować podczas synchronizacji, które reprezentują tego samego jednostek (użytkowników, grupy) lokalnie.
* **InvalidSoftMatch** błąd występuje, gdy dopasowanie twardych nie znajdzie dowolnego zgodnego obiektu **i** nietrwałego dopasowania znajduje dopasowywania obiektu, ale ten obiekt ma wartość inną *nazwę immutableId* niż przychodzące obiektu *SourceAnchor*, sugerujące, że obiektu zgodne była synchronizowana z innym obiektem z na lokalnych usługi Active Directory.

Innymi słowy, aby nietrwałego dopasowania do pracy, ma być dopasowane soft z nie ma żadnej wartości dla *nazwę immutableId*. Jeśli dowolne obiekty z *nazwę immutableId* zestawu z wartością kończy się niepowodzeniem twardych dopasowanie, ale spełniające kryteria soft-match, Operacja spowodowałaby InvalidSoftMatch błąd synchronizacji.

Co najmniej dwa obiekty mają taką samą wartość następujące atrybuty są niedozwolone w Azure schemat usługi Active Directory. \(To nie jest kompletną listą.\)

* ProxyAddresses
* UserPrincipalName
* onPremisesSecurityIdentifier
* Identyfikator obiektu

> [!NOTE]
> [Azure AD odporności atrybut zduplikowany atrybut](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md) również jest rozwijane funkcji jako domyślne zachowanie usługi Azure Active Directory.  Dzięki usłudze Azure AD bardziej odporne w sposób obsługi zduplikowanych ProxyAddresses i UserPrincipalName atrybuty w środowiskach lokalnych AD zmniejszy liczba błędów synchronizacji zostały odebrane przez Azure AD Connect (a także innych klientów synchronizacji). Ta funkcja poprawia błędów dublowania. Więc dane nadal wymaga naprawy. Ale umożliwia inicjowanie obsługi administracyjnej nowych obiektów, które w przeciwnym razie jest blokowane zainicjowanie obsługi z powodu zduplikowanych wartości w usłudze Azure AD. Będzie to również zmniejszyć liczbę błędów synchronizacji zwracana do klienta synchronizacji.
> Ta funkcja jest włączona dla Twojej dzierżawy, nie będą widzieć błędy synchronizacji InvalidSoftMatch występujące podczas inicjowania obsługi administracyjnej nowych obiektów.
>
>

#### <a name="example-scenarios-for-invalidsoftmatch"></a>Przykładowe scenariusze dotyczące InvalidSoftMatch
1. Istnieją dwa lub więcej obiektów z taką samą wartość atrybutu ProxyAddresses w lokalnej usłudze Active Directory. Tylko jeden z nich jest wprowadzenie administracyjnie w usłudze Azure AD.
2. Dwa lub więcej obiektów z taką samą wartość atrybutu userPrincipalName istnieje w lokalnej usłudze Active Directory. Tylko jeden z nich jest wprowadzenie administracyjnie w usłudze Azure AD.
3. Obiekt został dodany w lokalnymi usługi Active Directory z taką samą wartość atrybutu ProxyAddresses, co istniejący obiekt w usłudze Azure Active Directory. Obiekt dodane lokalnie nie zainicjowano pobierania w usłudze Azure Active Directory.
4. Obiekt został dodany w lokalnie usługi Active Directory z taką samą wartość atrybutu userPrincipalName jak konto w usłudze Azure Active Directory. Obiekt nie zainicjowano pobierania w usłudze Azure Active Directory.
5. Zsynchronizowane konta została przeniesiona z lasu do lasu B. Azure AD Connect (aparatem synchronizacji) używał atrybut ObjectGUID do obliczenia SourceAnchor. Po przeniesieniu lasu wartość SourceAnchor różni się. Nowy obiekt (z lasem B) kończy się niepowodzeniem do synchronizacji z istniejącego obiektu w usłudze Azure AD.
6. Zsynchronizowanej obiektu został przypadkowo usunięty z lokalnie usługi Active Directory i nowy obiekt został utworzony w usłudze Active Directory dla tej samej jednostki (na przykład użytkownik) bez usuwania konta w usłudze Azure Active Directory. Nowe konto nie powiedzie się był zsynchronizowany z istniejącego obiektu usługi Azure AD.
7. Azure AD Connect została odinstalowania i ponownego zainstalowania. Podczas ponownej instalacji przez inny atrybut, została wybrana jako SourceAnchor. Wszystkie obiekty, które wcześniej były zsynchronizowane zatrzymać synchronizację z powodu błędu InvalidSoftMatch.

#### <a name="example-case"></a>Przykład przypadek:
1. **Robert Smith** jest zsynchronizowany użytkownika w usłudze Azure Active Directory z na lokalnych usługi Active Directory dla *contoso.com*
2. Robert Smith **UserPrincipalName** jest ustawiony jako **bobs@contoso.com**.
3. **"abcdefghijklmnopqrstuv =="** jest **SourceAnchor** obliczane przy użyciu usługi Azure AD Connect przy użyciu Bob Smith **objectGUID** z na lokalnej usługi Active Directory, który jest  **nazwę immutableId** Nowak Bob w usłudze Azure Active Directory.
4. Robert ma również następujące wartości **proxyAddresses** atrybutu:
   * SMTP: bobs@contoso.com
   * SMTP: bob.smith@contoso.com
   * **SMTP: bob@contoso.com**
5. Nowy użytkownik **Taylora Bob**, jest dodawany do lokalnymi usługi Active Directory.
6. Robert Taylora **UserPrincipalName** jest ustawiony jako **bobt@contoso.com**.
7. **"abcdefghijkl0123456789 ==" "** jest **sourceAnchor** obliczane przy użyciu usługi Azure AD Connect przy użyciu Taylora Bob **objectGUID** z na lokalnych usługi Active Directory. Bob Taylora obiektu nie ma jeszcze zsynchronizowane z usługą Azure Active Directory.
8. Taylora Roberta, który ma następujące wartości dla atrybutu proxyAddresses
   * SMTP: bobt@contoso.com
   * SMTP: bob.taylor@contoso.com
   * **SMTP: bob@contoso.com**
9. Podczas synchronizacji Azure AD Connect rozpozna Dodawanie Roberta Taylora w na lokalnej usługi Active Directory i poproś usługi Azure AD, aby wprowadzić te same zmiany.
10. Usługi Azure AD najpierw zostanie wykonana twarde dopasowania. Oznacza to, umożliwia wyszukiwanie, jeśli dowolny obiekt nazwę immutableId jest równa "abcdefghijkl0123456789 ==". Dopasowanie twardych zakończy się niepowodzeniem, zgodnie z żadnego innego obiektu w usłudze Azure AD nie odniesie tego nazwę immutableId.
11. Usługi Azure AD następnie będzie podejmować próby soft-match Bob Taylora. Oznacza to przeszukiwane są, jeśli dowolny obiekt proxyAddresses jest równa trzy wartości, w tym smtp: bob@contoso.com
12. Obiekt Bob Smith zgodne z kryteriami soft-match zostanie ustalone, usługi Azure AD. Ale ten obiekt zawiera wartość nazwę immutableId = "abcdefghijklmnopqrstuv ==". co oznacza ten obiekt został zsynchronizowany z innego obiektu z lokalnie usługi Active Directory. W związku z tym usługi Azure AD nie soft-match te obiekty i powoduje **InvalidSoftMatch** błąd synchronizacji.

#### <a name="how-to-fix-invalidsoftmatch-error"></a>Jak usunąć błąd InvalidSoftMatch
Najczęstszą przyczyną tego błędu InvalidSoftMatch jest dwa obiekty o różnych SourceAnchor \(nazwę immutableId\) mają taką samą wartość dla atrybutów ProxyAddresses i/lub UserPrincipalName, które są używane w procesie soft-match w usłudze Azure AD. Aby rozwiązać nieprawidłowy nietrwałego dopasowania

1. Zidentyfikuj zduplikowanych proxyAddresses, userPrincipalName lub innych wartość atrybutu, który powoduje błąd. Również zidentyfikowanie dwa \(lub więcej\) obiekty są zaangażowane w konflikt. Raport wygenerowany przez [Azure AD Connect Health dla synchronizacji](https://aka.ms/aadchsyncerrors) mogą ułatwić identyfikację dwóch obiektów.
2. Identyfikowanie obiektu, który powinien nadal mieć zduplikowanych wartości i obiektu, który nie powinien.
3. Usuń zduplikowane wartości z obiektu, który nie powinien mieć tę wartość. Należy wprowadzić zmiany w katalogu, w której pochodzi z obiektu. W niektórych przypadkach może być konieczne usunięcie jednego z tych obiektów w konflikcie.
4. Jeśli wprowadzono zmiany w lokalnymi AD umożliwiają zmianę synchronizacji Azure AD Connect.

Raporty o błędach synchronizacji w ramach usługi Azure AD Connect Health dla synchronizacji są aktualizowane co 30 minut i zawierać błędy z najnowszą próby synchronizacji.

> [!NOTE]
> Nazwę ImmutableId, zgodnie z definicją, nie należy zmieniać w okresie istnienia obiektu. Jeśli usługa Azure AD Connect nie zostało skonfigurowane z niektórych scenariuszy pamiętać z powyższej listy, może się okazać w sytuacji, gdy Azure AD Connect oblicza inną wartość SourceAnchor dla obiekt AD, który reprezentuje takie same jednostki (tego samego itp. dane kontaktowe/użytkownika/grupy), która ma istniejącego obiektu AD Azure, który chcesz nadal używać.
>
>

#### <a name="related-articles"></a>Pokrewne artykuły
* [Zduplikowane lub nieprawidłowe atrybuty zapobieganie synchronizacji katalogów w usłudze Office 365](https://support.microsoft.com/en-us/kb/2647098)

### <a name="objecttypemismatch"></a>ObjectTypeMismatch
#### <a name="description"></a>Opis
Jeśli usługi Azure AD próbuje soft odpowiada dwa obiekty, jest to możliwe, że dwa obiekty o różnych "typu obiektu" (na przykład użytkownikiem, grupą, skontaktuj się z itp.) mają takie same wartości dla atrybutów używany do wykonywania nietrwałego dopasowania. Jak duplikatów tych atrybutów jest niedozwolone w usłudze Azure AD, operacja może spowodować błąd synchronizacji "ObjectTypeMismatch".

#### <a name="example-scenarios-for-objecttypemismatch-error"></a>Przykładowe scenariusze dla błędu ObjectTypeMismatch
* Grupy zabezpieczeń włączoną obsługę poczty jest tworzony w usłudze Office 365. Administrator dodaje nowego użytkownika lub kontaktu w lokalnie AD (tj. nie jest zsynchronizowany z usługą Azure AD jeszcze) z tą samą wartością dla atrybutu ProxyAddresses jako grupa usługi Office 365.

#### <a name="example-case"></a>Przykład case
1. Administrator tworzy nową grupę zabezpieczeń z włączoną obsługę poczty w usłudze Office 365 dla działu podatku i udostępnia adres e-mail jako tax@contoso.com. Ta grupa jest przypisana wartość atrybutu ProxyAddresses **smtp: tax@contoso.com**
2. Nowy użytkownik nie przyłączy Contoso.com i konto jest tworzone dla użytkowników lokalnych z proxyAddress jako **smtp: tax@contoso.com**
3. Gdy Azure AD Connect będzie synchronizować nowe konto użytkownika, otrzyma błąd "ObjectTypeMismatch".

#### <a name="how-to-fix-objecttypemismatch-error"></a>Jak usunąć błąd ObjectTypeMismatch
Najczęstszą przyczyną tego błędu ObjectTypeMismatch jest dwa obiekty innego typu (użytkownikiem, grupą, skontaktuj się z itp.) mają taką samą wartość dla atrybutu ProxyAddresses. Aby rozwiązać ObjectTypeMismatch:

1. Identyfikowanie zduplikowanych proxyAddresses (lub inne atrybuty) wartość, która powoduje błąd. Również zidentyfikowanie dwa \(lub więcej\) obiekty są zaangażowane w konflikt. Raport wygenerowany przez [Azure AD Connect Health dla synchronizacji](https://aka.ms/aadchsyncerrors) mogą ułatwić identyfikację dwóch obiektów.
2. Identyfikowanie obiektu, który powinien nadal mieć zduplikowanych wartości i obiektu, który nie powinien.
3. Usuń zduplikowane wartości z obiektu, który nie powinien mieć tę wartość. Należy pamiętać, że należy wprowadzić zmianę w katalogu, w której pochodzi z obiektu. W niektórych przypadkach może być konieczne usunięcie jednego z tych obiektów w konflikcie.
4. Jeśli wprowadzono zmiany w lokalnymi AD umożliwiają zmianę synchronizacji Azure AD Connect. Raport o błędzie synchronizacji w ramach usługi Azure AD Connect Health dla synchronizacji aktualizowany co 30 minut i zawiera błędy z najnowszą próby synchronizacji.

## <a name="duplicate-attributes"></a>Duplikowanie atrybutów
### <a name="attributevaluemustbeunique"></a>AttributeValueMustBeUnique
#### <a name="description"></a>Opis
Co najmniej dwa obiekty mają taką samą wartość następujące atrybuty są niedozwolone w Azure schemat usługi Active Directory. To, że wymusza każdego obiektu w usłudze Azure AD mają unikatowe wartości tych atrybutów w podanym wystąpieniu.

* ProxyAddresses
* UserPrincipalName

Jeśli usługa Azure AD Connect podejmuje próbę dodania nowego obiektu lub zaktualizować istniejący obiekt z wartością powyżej atrybutów, która jest już przypisany do innego obiektu w usłudze Azure Active Directory, wykonanie tej operacji skutkuje "AttributeValueMustBeUnique" Błąd synchronizacji.

#### <a name="possible-scenarios"></a>Możliwe scenariusze:
1. Zduplikowane wartości jest przypisany do obiektu już zsynchronizowane, który powoduje konflikt z innym obiektem zsynchronizowane.

#### <a name="example-case"></a>Przykład przypadek:
1. **Robert Smith** jest zsynchronizowany użytkownika w usłudze Azure Active Directory z na lokalnych usługi Active Directory dla domeny contoso.com
2. Robert Smith **UserPrincipalName** lokalnie jest ustawiony jako **bobs@contoso.com**.
3. Robert ma również następujące wartości **proxyAddresses** atrybutu:
   * SMTP: bobs@contoso.com
   * SMTP: bob.smith@contoso.com
   * **SMTP: bob@contoso.com**
4. Nowy użytkownik **Taylora Bob**, jest dodawany do lokalnymi usługi Active Directory.
5. Robert Taylora **UserPrincipalName** jest ustawiony jako **bobt@contoso.com**.
6. **Robert Taylora** z następującymi wartościami dla **ProxyAddresses** atrybutu i. SMTP: bobt@contoso.com ii. SMTP: bob.taylor@contoso.com
7. Obiektu Taylora Roberta, który jest synchronizowany z usługą Azure AD pomyślnie.
8. Admin postanowiła zaktualizować Taylora Bob **ProxyAddresses** atrybutu przy użyciu następującej wartości: i. **SMTP: bob@contoso.com**
9. Usługi Azure AD będzie próbował zaktualizować Taylora Bob obiektu w usłudze Azure AD z powyższą wartość, ale operacja zakończy się niepowodzeniem jako że ProxyAddresses wartość jest już przypisany do Bob Smith błąd "AttributeValueMustBeUnique".

#### <a name="how-to-fix-attributevaluemustbeunique-error"></a>Jak usunąć błąd AttributeValueMustBeUnique
Najczęstszą przyczyną tego błędu AttributeValueMustBeUnique jest dwa obiekty o różnych SourceAnchor \(nazwę immutableId\) mają taką samą wartość dla atrybutów ProxyAddresses i/lub UserPrincipalName. Aby naprawić błąd AttributeValueMustBeUnique

1. Zidentyfikuj zduplikowanych proxyAddresses, userPrincipalName lub innych wartość atrybutu, który powoduje błąd. Również zidentyfikowanie dwa \(lub więcej\) obiekty są zaangażowane w konflikt. Raport wygenerowany przez [Azure AD Connect Health dla synchronizacji](https://aka.ms/aadchsyncerrors) mogą ułatwić identyfikację dwóch obiektów.
2. Identyfikowanie obiektu, który powinien nadal mieć zduplikowanych wartości i obiektu, który nie powinien.
3. Usuń zduplikowane wartości z obiektu, który nie powinien mieć tę wartość. Należy pamiętać, że należy wprowadzić zmianę w katalogu, w której pochodzi z obiektu. W niektórych przypadkach może być konieczne usunięcie jednego z tych obiektów w konflikcie.
4. Jeśli wprowadzono zmiany w lokalnymi AD pozwolić, aby zsynchronizować zmiany tego błędu można uzyskać stałej Azure AD Connect.

#### <a name="related-articles"></a>Pokrewne artykuły
-[Zduplikowane lub nieprawidłowe atrybuty zapobieganie synchronizacji katalogów w usłudze Office 365](https://support.microsoft.com/en-us/kb/2647098)

## <a name="data-validation-failures"></a>Błędy sprawdzania poprawności danych
### <a name="identitydatavalidationfailed"></a>IdentityDataValidationFailed
#### <a name="description"></a>Opis
Usługa Azure Active Directory wymusza różne ograniczenia danych przed zezwoleniem na danych, który będzie zapisany w katalogu. Ograniczenia te powodują upewnij się, że użytkownicy końcowi mogą uzyskiwać najlepsze doświadczenia podczas korzystania z aplikacji, które są zależne od tych danych.

#### <a name="scenarios"></a>Scenariusze
a. Wartość atrybutu UserPrincipalName jest nieprawidłowy lub nieobsługiwany znaków.
b. Atrybut UserPrincipalName nie jest zgodna z wymaganym formatem.

#### <a name="how-to-fix-identitydatavalidationfailed-error"></a>Jak usunąć błąd IdentityDataValidationFailed
a. Upewnij się, że atrybut userPrincipalName ma obsługiwane znaki i wymagany format.

#### <a name="related-articles"></a>Pokrewne artykuły
* [Przygotowanie do obsługi administracyjnej użytkowników przez proces synchronizacji katalogów w celu usługi Office 365](https://support.office.com/en-us/article/Prepare-to-provision-users-through-directory-synchronization-to-Office-365-01920974-9e6f-4331-a370-13aea4e82b3e)

### <a name="federateddomainchangeerror"></a>FederatedDomainChangeError
#### <a name="description"></a>Opis
W wyniku tego przypadku **"FederatedDomainChangeError"** błąd synchronizacji, gdy sufiks UserPrincipalName użytkownika zostanie zmieniona z jednej domeny federacyjnej do innej domeny federacyjnej.

#### <a name="scenarios"></a>Scenariusze
Synchronizowanych użytkowników sufiks UserPrincipalName została zmieniona z jednej domeny federacyjnej do innej domeny federacyjnej lokalnie. Na przykład *UserPrincipalName = bob@contoso.com*  została zmieniona na *UserPrincipalName = bob@fabrikam.com* .

#### <a name="example"></a>Przykład
1. Smith Roberta, konta dla domeny Contoso.com, pobiera dodany jako nowy użytkownik w usłudze Active Directory o właściwości UserPrincipalName bob@contoso.com
2. Robert przenosi do innego oddziału contoso.com o nazwie Fabrikam.com i jego UserPrincipalName jest zmieniana na bob@fabrikam.com
3. Domeny contoso.com jak również fabrikam.com są Sfederowanych domen w usłudze Azure Active Directory.
4. UserPrincipalName Roberta nie zostanie zaktualizowana i powoduje błąd synchronizacji "FederatedDomainChangeError".

#### <a name="how-to-fix"></a>Jak rozwiązać
Jeśli sufiks UserPrincipalName użytkownika została zaktualizowana z bob @**contoso.com** do bob @**fabrikam.com**, gdzie oba **contoso.com** i **fabrikam.com** są **Sfederowanych domen**, wykonaj następujące kroki, aby naprawić błąd synchronizacji

1. Zaktualizuj UserPrincipalName użytkownika w usłudze Azure AD z bob@contoso.com do bob@contoso.onmicrosoft.com. Można Użyj następującego polecenia programu PowerShell z modułu Azure AD PowerShell: `Set-MsolUserPrincipalName -UserPrincipalName bob@contoso.com -NewUserPrincipalName bob@contoso.onmicrosoft.com`
2. Zezwalaj na następny cykl synchronizacji próby synchronizacji. Czas synchronizacji zakończy się pomyślnie i spowoduje zaktualizowanie UserPrincipalName Roberta do bob@fabrikam.com zgodnie z oczekiwaniami.

#### <a name="related-articles"></a>Pokrewne artykuły
* [Zmiany nie są synchronizowane przez narzędzie do synchronizacji usługi Azure Active Directory po zmianie nazwy UPN konta użytkownika, aby użyć innej domeny federacyjnej](https://support.microsoft.com/en-us/help/2669550/changes-aren-t-synced-by-the-azure-active-directory-sync-tool-after-you-change-the-upn-of-a-user-account-to-use-a-different-federated-domain)

## <a name="largeobject"></a>LargeObject
### <a name="description"></a>Opis
Gdy atrybut przekracza dozwolony limit rozmiaru, limit długości lub limit liczby ustawione przez schemat usługi Active Directory Azure, Operacja synchronizacji powoduje **LargeObject** lub **ExceededAllowedLength** błąd synchronizacji. Zazwyczaj ten błąd występuje z następującymi atrybutami

* userCertificate
* userSMIMECertificate
* thumbnailPhoto
* proxyAddresses

### <a name="possible-scenarios"></a>Możliwe scenariusze
1. Atrybut certyfikatu użytkownika Roberta jest przechowywanie zbyt wiele certyfikatów przypisane do niego. Mogą one obejmować starsze, wygasłe certyfikaty. Twarde limit wynosi 15 certyfikatów. Aby uzyskać więcej informacji na temat sposobu obsługi błędów LargeObject z atrybutem certyfikatu użytkownika, przeczytaj artykuł [LargeObject obsługi błędów spowodowanych przez atrybut userCertificate](active-directory-aadconnectsync-largeobjecterror-usercertificate.md).
2. Atrybut userSMIMECertificate Roberta jest przechowywanie zbyt wiele certyfikatów przypisane do niego. Mogą one obejmować starsze, wygasłe certyfikaty. Twarde limit wynosi 15 certyfikatów.
3. ThumbnailPhoto Roberta ustawić w usłudze Active Directory jest zbyt duży, można synchronizować w usłudze Azure AD.
4. Podczas automatycznego wypełniania atrybutu ProxyAddresses w usłudze Active Directory obiekt ma zbyt wiele ProxyAddresses przypisane.

### <a name="how-to-fix"></a>Jak rozwiązać
1. Upewnij się, że atrybut przyczyną błędu jest w ramach dozwolone ograniczenia.

## <a name="related-links"></a>Powiązane linki
* [Znajdź obiekty usługi Active Directory w Centrum administracyjne usługi Active Directory](https://technet.microsoft.com/library/dd560661.aspx)
* [Jak wykonać zapytanie dotyczące usługi Azure Active Directory dla obiekt przy użyciu programu PowerShell usługi Azure Active Directory](https://msdn.microsoft.com/library/azure/jj151815.aspx)
