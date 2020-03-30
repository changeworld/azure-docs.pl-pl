---
title: 'Usługa Azure AD Connect: rozwiązywanie problemów z błędami podczas synchronizacji | Dokumenty firmy Microsoft'
description: W tym artykule wyjaśniono, jak rozwiązywać problemy z błędami napotkanymi podczas synchronizacji z usługą Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 2209d5ce-0a64-447b-be3a-6f06d47995f8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 745ddcc95bb91e61478307265aec1ac8a7ebba54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609200"
---
# <a name="troubleshooting-errors-during-synchronization"></a>Rozwiązywanie problemów z błędami podczas synchronizacji
Błędy mogą wystąpić, gdy dane tożsamości są synchronizowane z usługi Windows Server Active Directory (AD DS) do usługi Azure Active Directory (Azure AD). Ten artykuł zawiera omówienie różnych typów błędów synchronizacji, niektóre z możliwych scenariuszy, które powodują te błędy i potencjalne sposoby naprawienia błędów. Ten artykuł zawiera typowe typy błędów i może nie obejmować wszystkich możliwych błędów.

 W tym artykule założono, że czytelnik jest zaznajomiony z [podstawowymi pojęciami projektowymi usługi Azure AD i usługi Azure AD Connect.](plan-connect-design-concepts.md)

W najnowszej wersji usługi \(Azure AD Connect w\)sierpniu 2016 r. lub nowszej raport o błędach synchronizacji jest dostępny w [witrynie Azure portal](https://aka.ms/aadconnecthealth) w ramach usługi Azure AD Connect Health do synchronizacji.

Od 1 września 2016 r. funkcja [odporności zduplikowanych atrybutów usługi Azure Active Directory](how-to-connect-syncservice-duplicate-attribute-resiliency.md) będzie domyślnie włączona dla wszystkich *nowych* dzierżaw usługi Azure Active Directory. Ta funkcja zostanie automatycznie włączona dla istniejących dzierżawców w nadchodzących miesiącach.

Usługa Azure AD Connect wykonuje trzy typy operacji z katalogów, które przechowuje w synchronizacji: Import, Synchronizacja i Eksport. Błędy mogą mieć miejsce we wszystkich operacjach. W tym artykule koncentruje się głównie na błędach podczas eksportowania do usługi Azure AD.

## <a name="errors-during-export-to-azure-ad"></a>Błędy podczas eksportowania do usługi Azure AD
W poniższej sekcji opisano różne typy błędów synchronizacji, które mogą wystąpić podczas operacji eksportowania do usługi Azure AD przy użyciu łącznika usługi Azure AD. Ten łącznik można zidentyfikować za pomocą formatu nazwy "contoso. *onmicrosoft.com*".
Błędy podczas eksportowania do usługi \(Azure AD wskazują,\) że operacja dodawania,\) aktualizowania, usuwania \(itp.

![Omówienie błędów eksportu](./media/tshoot-connect-sync-errors/Export_Errors_Overview_01.png)

## <a name="data-mismatch-errors"></a>Błędy niezgodności danych
### <a name="invalidsoftmatch"></a>InvalidSoftMatch (Nieprawidłowy
#### <a name="description"></a>Opis
* Gdy\) aparat \(synchronizacji usługi Azure AD Connect instruuje usługę Azure Active Directory o doliczaniu lub aktualizowaniu obiektów, usługa Azure AD dopasowuje obiekt przychodzący przy użyciu **atrybutu sourceAnchor** do atrybutu **immutableId** obiektów w usłudze Azure AD. Ten mecz nazywa się **Hard Match**.
* Gdy usługa Azure AD **nie znajdzie** żadnego obiektu, który pasuje do atrybutu **immutableId** z **atrybutem sourceAnchor** obiektu przychodzącego, przed inicjowania obsługi administracyjnej nowego obiektu, powraca do korzystania z atrybutów ProxyAddresses i UserPrincipalName, aby znaleźć dopasowanie. Ten mecz jest nazywany **meczem miękkim.** Dopasowanie miękkie jest przeznaczony do dopasowania obiektów już obecnych w usłudze Azure AD (które są pozyskiwane w usłudze Azure AD) z nowych obiektów dodawanych/aktualizowanych podczas synchronizacji, które reprezentują tę samą jednostkę (użytkowników, grup) w środowisku lokalnym.
* **Błąd InvalidSoftMatch** występuje, gdy dopasowanie twarde nie znajduje żadnego pasującego obiektu **i** dopasowania programowego, ale ten obiekt ma inną wartość *immutableId* niż *sourceanchor*obiektu przychodzącego, co sugeruje, że pasujący obiekt został zsynchronizowany z innym obiektem z lokalnej usługi Active Directory.

Innymi słowy, aby dopasowanie miękkie działało, obiekt, z którego ma być dopasowany do miękkości, nie powinien mieć żadnej wartości dla *pliku immutableId*. Jeśli dowolny obiekt z *immutableId* zestaw z wartością kończy się niepowodzeniem, ale spełnia kryteria dopasowania miękkiego, operacja spowoduje błąd synchronizacji InvalidSoftMatch.

Schemat usługi Azure Active Directory nie zezwala na to, aby dwa lub więcej obiektów miało taką samą wartość następujących atrybutów. \(Nie jest to wyczerpująca lista.\)

* ProxyAddresses
* UserPrincipalName
* onPremisesSecurityIdentifier
* ObjectId

> [!NOTE]
> Funkcja [odporności zduplikowanych atrybutów usługi Azure AD](how-to-connect-syncservice-duplicate-attribute-resiliency.md) jest również wdrażana jako domyślne zachowanie usługi Azure Active Directory.  Zmniejszy to liczbę błędów synchronizacji widzianych przez usługę Azure AD Connect (a także innych klientów synchronizacji), dzięki czemu usługa Azure AD będzie bardziej odporna na sposób obsługi zduplikowanych adresów proxy i atrybutów UserPrincipalName obecnych w lokalnych środowiskach usługi AD. Ta funkcja nie rozwiązuje błędów duplikacji. Tak więc dane nadal muszą zostać naprawione. Ale umożliwia inicjowanie obsługi administracyjnej nowych obiektów, które w przeciwnym razie są blokowane z powodu zduplikowanych wartości w usłudze Azure AD. Zmniejszy to również liczbę błędów synchronizacji zwracanych do klienta synchronizacji.
> Jeśli ta funkcja jest włączona dla dzierżawy, nie zostaną wyświetlone błędy synchronizacji InvalidSoftMatch widoczne podczas inicjowania obsługi administracyjnej nowych obiektów.
>
>

#### <a name="example-scenarios-for-invalidsoftmatch"></a>Przykładowe scenariusze dla InvalidSoftMatch
1. Dwa lub więcej obiektów o tej samej wartości dla atrybutu ProxyAddresses istnieje w lokalnej usłudze Active Directory. Tylko jeden jest coraz aprowizacji w usłudze Azure AD.
2. Dwa lub więcej obiektów o tej samej wartości dla atrybutu userPrincipalName istnieje w lokalnej usłudze Active Directory. Tylko jeden jest coraz aprowizacji w usłudze Azure AD.
3. Obiekt został dodany w lokalnej usłudze Active Directory o tej samej wartości atrybutu ProxyAddresses, co w przypadku istniejącego obiektu w usłudze Azure Active Directory. Obiekt dodany lokalnie nie jest coraz aprowizacji w usłudze Azure Active Directory.
4. Obiekt został dodany w lokalnej usłudze Active Directory o takiej samej wartości atrybutu userPrincipalName, jak w usłudze Azure Active Directory. Obiekt nie jest coraz aprowizacji w usłudze Azure Active Directory.
5. Zsynchronizowane konto zostało przeniesione z lasu A do lasu B. Usługa Azure AD Connect (aparat synchronizacji) używała atrybutu ObjectGUID do obliczania funkcji SourceAnchor. Po przeniesieniu lasu wartość SourceAnchor jest inna. Nowy obiekt (z lasu B) nie można zsynchronizować z istniejącym obiektem w usłudze Azure AD.
6. Zsynchronizowany obiekt został przypadkowo usunięty z lokalnej usługi Active Directory i w usłudze Active Directory został utworzony nowy obiekt dla tej samej jednostki (takiej jak użytkownik) bez usuwania konta w usłudze Azure Active Directory. Nowe konto nie można zsynchronizować z istniejącym obiektem usługi Azure AD.
7. Usługa Azure AD Connect została odinstalowana i ponownie zainstalowana. Podczas ponownej instalacji został wybrany inny atrybut jako SourceAnchor. Wszystkie obiekty, które wcześniej zostały zsynchronizowane, przestały synchronizować z błędem InvalidSoftMatch.

#### <a name="example-case"></a>Przykładowy przypadek:
1. **Robert Smith** jest zsynchronizowanym użytkownikiem w usłudze Azure Active Directory z lokalnej usługi Active Directory *contoso.com*
2. Bob Smith's **UserPrincipalName** jest ustawiony jako **\@bobs contoso.com**.
3. **"abcdefghijklmnopqrstuv=="** to **sourceanchor** obliczany przez usługę Azure AD Connect przy użyciu **obiektuGUID** Roberta Smitha z lokalnej usługi Active Directory, która jest **immutableId** dla Roberta Smitha w usłudze Azure Active Directory.
4. Bob ma również następujące wartości dla **atrybutu proxyAddresses:**
   * Smtp:bobs@contoso.com
   * Smtp:bob.smith@contoso.com
   * **smtp:\@bob contoso.com**
5. Nowy użytkownik, **Bob Taylor,** jest dodawany do lokalnej usługi Active Directory.
6. Użytkownik Boba **TayloraName** jest ustawiony jako **\@bobt contoso.com**.
7. **"abcdefghijkl0123456789==""** to **źródłoAnchor** obliczany przez usługę Azure AD Connect przy użyciu **objectGUID** Roberta Taylora z lokalnej usługi Active Directory. Obiekt Roberta Taylora NIE został jeszcze zsynchronizowany z usługą Azure Active Directory.
8. Bob Taylor ma następujące wartości dla atrybutu proxyAddresses
   * Smtp:bobt@contoso.com
   * Smtp:bob.taylor@contoso.com
   * **smtp:\@bob contoso.com**
9. Podczas synchronizacji usługa Azure AD Connect rozpozna dodanie Roberta Taylora w lokalnej usłudze Active Directory i poprosi usługę Azure AD o dokonanie tej samej zmiany.
10. Usługa Azure AD najpierw wykona trudne dopasowanie. Oznacza to, że będzie wyszukiwać, jeśli istnieje dowolny obiekt z immutableId równa "abcdefghijkl0123456789==". Hard Match zakończy się niepowodzeniem, ponieważ żaden inny obiekt w usłudze Azure AD nie będzie miał tego identyfikatora niezmiennego.
11. Usługa Azure AD będzie następnie próbować soft-match Bob Taylor. Oznacza to, że będzie wyszukiwać, jeśli istnieje dowolny obiekt z proxyAddresses równa trzech wartości, w tym smtp:bob@contoso.com
12. Usługa Azure AD znajdzie obiekt Roberta Smitha, aby dopasować się do kryteriów dopasowania nietrwale. Ale ten obiekt ma wartość niezmienneId = "abcdefghijklmnopqrstuv==". który wskazuje, że ten obiekt został zsynchronizowany z innego obiektu z lokalnej usługi Active Directory. W związku z tym usługi Azure AD nie można dopasować do tych obiektów i powoduje błąd synchronizacji **InvalidSoftMatch.**

#### <a name="how-to-fix-invalidsoftmatch-error"></a>Jak naprawić błąd InvalidSoftMatch
Najczęstszą przyczyną błędu InvalidSoftMatch są dwa obiekty o \(różnych SourceAnchor immutableId\) mają taką samą wartość dla atrybutów ProxyAddresses i/lub UserPrincipalName, które są używane podczas procesu dopasowania programowego w usłudze Azure AD. Aby naprawić nieprawidłowe dopasowanie

1. Zidentyfikuj zduplikowane proxyAddresses, userPrincipalName lub inną wartość atrybutu, która powoduje błąd. Również określić, które dwa \(lub więcej\) obiektów są zaangażowane w konflikt. Raport wygenerowany przez [usługę Azure AD Connect Health do synchronizacji](https://aka.ms/aadchsyncerrors) może pomóc w zidentyfikowaniu dwóch obiektów.
2. Określ, który obiekt powinien nadal mieć zduplikowaną wartość, a który nie powinien.
3. Usuń zduplikowaną wartość z obiektu, który NIE powinien mieć tej wartości. Należy wprowadzić zmiany w katalogu, z którego pochodzi obiekt. W niektórych przypadkach może być konieczne usunięcie jednego z obiektów w konflikcie.
4. Jeśli wy została wyeksja lokalna usługa AD, niech usługa Azure AD Connect synchronizuje zmiany.

Raporty o błędach synchronizacji w usłudze Azure AD Connect Health do synchronizacji są aktualizowane co 30 minut i zawierają błędy z ostatniej próby synchronizacji.

> [!NOTE]
> ImmutableId, z definicji, nie powinny się zmieniać w okresie istnienia obiektu. Jeśli usługa Azure AD Connect nie została skonfigurowana z niektórymi scenariuszami na uwadze z powyższej listy, może skończyć się w sytuacji, gdy usługa Azure AD Connect oblicza inną wartość SourceAnchor dla obiektu USŁUGI AD, który reprezentuje tę samą jednostkę (ten sam użytkownik/grupa/kontakt itp.), która ma istniejący obiekt usługi Azure AD, który chcesz kontynuować.
>
>

#### <a name="related-articles"></a>Powiązane artykuły
* [Zduplikowane lub nieprawidłowe atrybuty uniemożliwiają synchronizację katalogów w usłudze Office 365](https://support.microsoft.com/kb/2647098)

### <a name="objecttypemismatch"></a>ObjectTypeMismatch (Typ obiektuMismatch)
#### <a name="description"></a>Opis
Gdy usługa Azure AD próbuje wyrównać dwa obiekty, jest możliwe, że dwa obiekty o różnym "typie obiektu" (takich jak Użytkownik, Grupa, Kontakt itp.) mają takie same wartości dla atrybutów używanych do wykonywania dopasowania programowego. Ponieważ powielanie tych atrybutów nie jest dozwolone w usłudze Azure AD, operacja może spowodować błąd synchronizacji "ObjectTypeMismatch".

#### <a name="example-scenarios-for-objecttypemismatch-error"></a>Przykładowe scenariusze dla błędu ObjectTypeMismatch
* W usłudze Office 365 zostanie utworzona grupa zabezpieczeń z włączoną obsługą poczty. Administrator dodaje nowego użytkownika lub kontakt w lokalnej usłudze AD (która nie jest jeszcze zsynchronizowana z usługą Azure AD) z taką samą wartością dla atrybutu ProxyAddresses, jak w przypadku grupy usługi Office 365.

#### <a name="example-case"></a>Przykładowa sprawa
1. Administrator tworzy nową grupę zabezpieczeń z włączoną pocztą w usłudze Office tax@contoso.com365 dla działu podatkowego i podaje adres e-mail jako . Tej grupie jest przypisywana wartość atrybutu ProxyAddresses **smtp:\@tax contoso.com**
2. Nowy użytkownik dołącza do Contoso.com i konto jest tworzone dla użytkownika lokalnie z proxyAddress jako **smtp:\@podatek contoso.com**
3. Gdy usługa Azure AD Connect zsynchronizuje nowe konto użytkownika, zostanie wyświetlony błąd "ObjectTypeMismatch".

#### <a name="how-to-fix-objecttypemismatch-error"></a>Jak naprawić błąd ObjectTypeMismatch
Najczęstszą przyczyną błędu ObjectTypeMismatch są dwa obiekty innego typu (Użytkownik, Grupa, Kontakt itp.) mają taką samą wartość dla atrybutu ProxyAddresses. Aby naprawić ObjectTypeMismatch:

1. Zidentyfikuj zduplikowaną wartość proxyAddresses (lub inny atrybut), który powoduje błąd. Również określić, które dwa \(lub więcej\) obiektów są zaangażowane w konflikt. Raport wygenerowany przez [usługę Azure AD Connect Health do synchronizacji](https://aka.ms/aadchsyncerrors) może pomóc w zidentyfikowaniu dwóch obiektów.
2. Określ, który obiekt powinien nadal mieć zduplikowaną wartość, a który nie powinien.
3. Usuń zduplikowaną wartość z obiektu, który NIE powinien mieć tej wartości. Należy zauważyć, że należy wprowadzić zmiany w katalogu, z którego pochodzi obiekt. W niektórych przypadkach może być konieczne usunięcie jednego z obiektów w konflikcie.
4. Jeśli wy została wyeksja lokalna usługa AD, niech usługa Azure AD Connect synchronizuje zmiany. Raport o błędach synchronizacji w usłudze Azure AD Connect Health dla synchronizacji jest aktualizowany co 30 minut i zawiera błędy z ostatniej próby synchronizacji.

## <a name="duplicate-attributes"></a>Zduplikowane atrybuty
### <a name="attributevaluemustbeunique"></a>AtrybutValueMustBeUnique
#### <a name="description"></a>Opis
Schemat usługi Azure Active Directory nie zezwala na to, aby dwa lub więcej obiektów miało taką samą wartość następujących atrybutów. To jest każdy obiekt w usłudze Azure AD jest zmuszony mieć unikatową wartość tych atrybutów w danym wystąpieniu.

* ProxyAddresses
* UserPrincipalName

Jeśli usługa Azure AD Connect próbuje dodać nowy obiekt lub zaktualizować istniejący obiekt o wartości dla powyższych atrybutów, który jest już przypisany do innego obiektu w usłudze Azure Active Directory, operacja powoduje błąd synchronizacji "AttributeValueMustBeUnique".

#### <a name="possible-scenarios"></a>Możliwe scenariusze:
1. Zduplikowana wartość jest przypisywana do już zsynchronizowanego obiektu, który powoduje konflikt z innym zsynchronizowanym obiektem.

#### <a name="example-case"></a>Przykładowy przypadek:
1. **Robert Smith** jest zsynchronizowanym użytkownikiem w usłudze Azure Active Directory z lokalnej usługi Active Directory contoso.com
2. Użytkownik Boba **SmithaName w lokalu** jest ustawiony jako **bobs\@contoso.com**.
3. Bob ma również następujące wartości dla **atrybutu proxyAddresses:**
   * Smtp:bobs@contoso.com
   * Smtp:bob.smith@contoso.com
   * **smtp:\@bob contoso.com**
4. Nowy użytkownik, **Bob Taylor,** jest dodawany do lokalnej usługi Active Directory.
5. Użytkownik Boba **TayloraName** jest ustawiony jako **\@bobt contoso.com**.
6. **Bob Taylor** ma następujące wartości dla **atrybutu ProxyAddresses** i. smtp: bobt@contoso.com ii. Smtp:bob.taylor@contoso.com
7. Obiekt Roberta Taylora jest pomyślnie synchronizowany z usługą Azure AD.
8. Administrator postanowił zaktualizować atrybut **ProxyAddresses Boba** Taylora o następującej wartości: i. **smtp:\@bob contoso.com**
9. Usługa Azure AD będzie próbować zaktualizować obiekt Roberta Taylora w usłudze Azure AD z powyższą wartością, ale ta operacja zakończy się niepowodzeniem, ponieważ ta wartość ProxyAddresses jest już przypisana do programu Bob Smith, co powoduje błąd "AttributeValueMustBeUnique".

#### <a name="how-to-fix-attributevaluemustbeunique-error"></a>Jak naprawić błąd AttributeValueMustBeUnique
Najczęstszą przyczyną attributeValueMustBeUnique błąd jest dwa obiekty z \(różnych SourceAnchor immutableId\) mają taką samą wartość dla ProxyAddresses i/lub UserPrincipalName atrybutów. Aby naprawić błąd AttributeValueMustBeUnique

1. Zidentyfikuj zduplikowane proxyAddresses, userPrincipalName lub inną wartość atrybutu, która powoduje błąd. Również określić, które dwa \(lub więcej\) obiektów są zaangażowane w konflikt. Raport wygenerowany przez [usługę Azure AD Connect Health do synchronizacji](https://aka.ms/aadchsyncerrors) może pomóc w zidentyfikowaniu dwóch obiektów.
2. Określ, który obiekt powinien nadal mieć zduplikowaną wartość, a który nie powinien.
3. Usuń zduplikowaną wartość z obiektu, który NIE powinien mieć tej wartości. Należy zauważyć, że należy wprowadzić zmiany w katalogu, z którego pochodzi obiekt. W niektórych przypadkach może być konieczne usunięcie jednego z obiektów w konflikcie.
4. Jeśli w lokalnej usłudze AD została wyeksja lokalna, usługa Azure AD Connect zsynchronizowana ze zmianą błędu została naprawiona.

#### <a name="related-articles"></a>Powiązane artykuły
-[Zduplikowane lub nieprawidłowe atrybuty uniemożliwiają synchronizację katalogów w usłudze Office 365](https://support.microsoft.com/kb/2647098)

## <a name="data-validation-failures"></a>Błędy sprawdzania poprawności danych
### <a name="identitydatavalidationfailed"></a>IdentityDataValidationFailed
#### <a name="description"></a>Opis
Usługa Azure Active Directory wymusza różne ograniczenia dotyczące samych danych przed zezwoleniem na zapisanie tych danych w katalogu. Ograniczenia te mają na celu zapewnienie, że użytkownicy końcowi uzyskać najlepsze możliwe środowisko podczas korzystania z aplikacji, które zależą od tych danych.

#### <a name="scenarios"></a>Scenariusze
a. Wartość atrybutu UserPrincipalName ma nieprawidłowe/nieobsługiwały znaki.
b. UserPrincipalName atrybut nie jest zgodny z wymaganym formatem.

#### <a name="how-to-fix-identitydatavalidationfailed-error"></a>Jak naprawić błąd IdentityDataValidationFailed
a. Upewnij się, że atrybut userPrincipalName obsługuje znaki i wymagany format.

#### <a name="related-articles"></a>Powiązane artykuły
* [Przygotowanie do aprowizowania użytkowników za pomocą synchronizacji katalogów do usługi Office 365](https://support.office.com/article/Prepare-to-provision-users-through-directory-synchronization-to-Office-365-01920974-9e6f-4331-a370-13aea4e82b3e)

### <a name="federateddomainchangeerror"></a>FederatedDomainChangeError
#### <a name="description"></a>Opis
Ten przypadek powoduje błąd synchronizacji **"FederatedDomainChangeError",** gdy sufiks userPrincipalName użytkownika zostanie zmieniony z jednej domeny federacyjnej do innej domeny federacyjnej.

#### <a name="scenarios"></a>Scenariusze
Dla zsynchronizowanego użytkownika sufiks UserPrincipalName został zmieniony z jednej domeny federacyjnej na inną domenę federacyjnej w środowisku lokalnym. Na przykład *UserPrincipalName\@= bob contoso.com* został zmieniony na *UserPrincipalName = bob\@fabrikam.com*.

#### <a name="example"></a>Przykład
1. Bob Smith, konto dla Contoso.com, zostanie dodany jako nowy użytkownik w usłudze Active Directory z UserPrincipalNamebob@contoso.com
2. Bob przenosi się do innego działu Contoso.com o nazwie Fabrikam.com, a ich UserPrincipalName zostaje zmieniona nabob@fabrikam.com
3. Zarówno contoso.com, jak i fabrikam.com domeny są domenami federatywnymi za pomocą usługi Azure Active Directory.
4. Użytkownik RobertaPrincipalName nie jest aktualizowany i powoduje błąd synchronizacji "FederatedDomainChangeError".

#### <a name="how-to-fix"></a>Jak rozwiązać problem
Jeśli sufiks UserPrincipalName użytkownika został zaktualizowany**contoso.com** z bob@ contoso.com\@do**fabrikam.com,** gdzie zarówno **contoso.com,** jak i **fabrikam.com** są **domenami federalizowanymi,** wykonaj następujące kroki, aby naprawić błąd synchronizacji

1. Zaktualizuj user's UserPrincipalName w usłudze Azure AD z bob@contoso.com do bob@contoso.onmicrosoft.com. Za pomocą modułu Programu Azure AD PowerShell można użyć następującego polecenia programu PowerShell:`Set-MsolUserPrincipalName -UserPrincipalName bob@contoso.com -NewUserPrincipalName bob@contoso.onmicrosoft.com`
2. Zezwól na następny cykl synchronizacji, aby podjąć próbę synchronizacji. Tym razem synchronizacja zakończy się pomyślnie i zaktualizuje UserPrincipalName Bob do bob@fabrikam.com zgodnie z oczekiwaniami.

#### <a name="related-articles"></a>Powiązane artykuły
* [Zmiany nie są synchronizowane przez narzędzie Synchronizacja usługi Azure Active Directory po zmianie nazwy UPN konta użytkownika w celu użycia innej domeny federacyjnej](https://support.microsoft.com/help/2669550/changes-aren-t-synced-by-the-azure-active-directory-sync-tool-after-you-change-the-upn-of-a-user-account-to-use-a-different-federated-domain)

## <a name="largeobject"></a>Dużyobiekt
### <a name="description"></a>Opis
Gdy atrybut przekracza dozwolony limit rozmiaru, limit długości lub limit liczby ustawiony przez schemat usługi Azure Active Directory, operacja synchronizacji powoduje błąd synchronizacji **LargeObject** lub **ExceededAllowedLength.** Zazwyczaj ten błąd występuje dla następujących atrybutów

* userCertificate
* userSMIMECertificate
* thumbnailPhoto
* proxyAddresses

### <a name="possible-scenarios"></a>Możliwe scenariusze
1. Atrybut Certyfikatu użytkownika Roberta jest przechowywanie zbyt wielu certyfikatów przypisanych do Roberta. Mogą to być starsze, wygasłe certyfikaty. Limit twardy to 15 certyfikatów. Aby uzyskać więcej informacji na temat obsługi błędów LargeObject z atrybutem userCertificate, zapoznaj się z [artykułem Obsługa błędów LargeObject spowodowanych przez atrybut userCertificate](tshoot-connect-largeobjecterror-usercertificate.md).
2. Atrybut Certyfikatu użytkownika RobertaSMIMECertificate jest przechowywanie zbyt wielu certyfikatów przypisanych do Roberta. Mogą to być starsze, wygasłe certyfikaty. Limit twardy to 15 certyfikatów.
3. Miniatura RobertaDojednak w usłudze Active Directory jest zbyt duża, aby można ją było zsynchronizować w usłudze Azure AD.
4. Podczas automatycznego zaludnienia atrybutu ProxyAddresses w usłudze Active Directory obiekt ma przypisaną zbyt wiele adresów proxy.

### <a name="how-to-fix"></a>Jak rozwiązać problem
1. Upewnij się, że atrybut powodujący błąd mieści się w dozwolonym ograniczeniu.

## <a name="existing-admin-role-conflict"></a>Istniejący konflikt ról administratora

### <a name="description"></a>Opis
**Istniejący konflikt ról administratora** wystąpi na obiekcie użytkownika podczas synchronizacji, gdy ten obiekt użytkownika ma:

- administracyjne i
- ta sama nazwa użytkownika jako istniejący obiekt usługi Azure AD

Usługa Azure AD Connect nie może być miękka dopasować obiekt użytkownika z lokalnej usługi AD do obiektu użytkownika w usłudze Azure AD, który ma przypisaną rolę administracyjną.  Aby uzyskać więcej informacji, zobacz [Populacja usługi Azure AD UserPrincipalName](plan-connect-userprincipalname.md)

![Istniejący administrator](media/tshoot-connect-sync-errors/existingadmin.png)


### <a name="how-to-fix"></a>Jak rozwiązać problem
Aby rozwiązać ten problem, wykonaj następujące czynności:

1. Usuń konto usługi Azure AD (właściciela) ze wszystkich ról administratora. 
2. **Twarde usuwanie** obiektu poddanego kwarantannie w chmurze. 
3. Następny cykl synchronizacji zajmie się miękkim dopasowaniem użytkownika lokalnego do konta w chmurze (ponieważ użytkownik chmury nie jest już globalną ga). 
4. Przywróć członkostwo roli właściciela. 

>[!NOTE]
>Można przypisać rolę administracyjną do istniejącego obiektu użytkownika ponownie po zakończeniu dopasowania programowego między lokalnym obiektem użytkownika a obiektem użytkownika usługi Azure AD.

## <a name="related-links"></a>Powiązane linki
* [Lokalizowanie obiektów usługi Active Directory w Centrum administracyjnym usługi Active Directory](https://technet.microsoft.com/library/dd560661.aspx)
* [Jak wysyłać zapytania do usługi Azure Active Directory dla obiektu przy użyciu programu Azure Active Directory PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx)
