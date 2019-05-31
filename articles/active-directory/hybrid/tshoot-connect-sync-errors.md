---
title: 'Program Azure AD Connect: Rozwiązywanie problemów z błędami występującymi podczas synchronizacji | Dokumentacja firmy Microsoft'
description: Wyjaśnia, jak rozwiązywać problemy wystąpiły błędy podczas synchronizacji z programem Azure AD Connect.
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
ms.openlocfilehash: f63aebb9a9bbefe84ac36b92cd69e0d93de0ab76
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2019
ms.locfileid: "66298755"
---
# <a name="troubleshooting-errors-during-synchronization"></a>Rozwiązywanie problemów z błędami występującymi podczas synchronizacji
Podczas synchronizowania danych tożsamości z usługi Windows Server Active Directory (AD DS) do usługi Azure Active Directory (Azure AD), mogą wystąpić błędy. Ten artykuł zawiera omówienie różnych typów błędów synchronizacji, niektóre z możliwych scenariuszy, które powodują tych błędów i potencjalne sposoby naprawienia błędy. W tym artykule zawiera z najczęściej popełnianymi typami błędów i może nie obejmować wszystkich możliwych błędów.

 W tym artykule założono, czytelnik jest zapoznać się z bazowego [projektowania pojęcia dotyczące usługi Azure AD i Azure AD Connect](plan-connect-design-concepts.md).

Za pomocą najnowszej wersji programu Azure AD Connect \(sierpnia 2016 lub nowszego\), raport błędów synchronizacji jest dostępny w [witryny Azure portal](https://aka.ms/aadconnecthealth) jako część usługi Azure AD Connect Health do celów synchronizacji.

Od 1 września 2016 [usługi Azure Active Directory zduplikowany atrybut odporności](how-to-connect-syncservice-duplicate-attribute-resiliency.md) funkcja zostanie włączona domyślnie dla wszystkich *nowe* usługi Azure Active Directory dzierżaw. Ta funkcja zostanie automatycznie włączona w przypadku istniejących dzierżaw w ciągu najbliższych miesięcy.

Program Azure AD Connect wykonuje trzy typy operacji z katalogów, który zapewnia synchronizację: Importowania, synchronizacji i eksportowania. Błędy mogą być wykonywane we wszystkich operacjach. Ten artykuł koncentruje się głównie na błędy podczas eksportowania do usługi Azure AD.

## <a name="errors-during-export-to-azure-ad"></a>Błędy podczas eksportowania do usługi Azure AD
Tej sekcji opisano różne typy błędów synchronizacji, które mogą wystąpić podczas operacji eksportowania do usługi Azure AD za pomocą łącznika usługi Azure AD. Ten łącznik można zidentyfikować przez format nazwy jest "contoso. *onmicrosoft.com*".
Błędy podczas eksportowania do usługi Azure AD wskazują, że operacja \(Dodawanie, aktualizowanie i usuwanie itp.\) próba przy użyciu usługi Azure AD Connect \(aparatu synchronizacji\) usługi Azure Active Directory nie powiodło się.

![Omówienie błędów eksportu](./media/tshoot-connect-sync-errors/Export_Errors_Overview_01.png)

## <a name="data-mismatch-errors"></a>Błędy niezgodności danych
### <a name="invalidsoftmatch"></a>InvalidSoftMatch
#### <a name="description"></a>Opis
* Gdy programu Azure AD Connect \(aparatu synchronizacji\) powoduje, że usługi Azure Active Directory, aby dodać lub zaktualizować obiekty, usługi Azure AD pasuje do przychodzącego przy użyciu obiektu **sourceAnchor** atrybutu **immutableId**  atrybutów obiektów w usłudze Azure AD. To dopasowanie jest nazywany **twardych dopasowania**.
* Gdy usługi Azure AD **nie znajdzie** dowolnego obiektu, który jest zgodny **immutableId** atrybutem **sourceAnchor** atrybut obiektu przychodzących przed zainicjowaniem obsługi administracyjnej nowej obiekt, nastąpi powrót do użycia atrybutów ProxyAddresses i UserPrincipalName w celu znalezienia dopasowania. To dopasowanie jest nazywany **nietrwałego dopasowania**. Elastyczne dopasowania jest przeznaczony do dopasowania obiektów, które są już istnieje w usłudze Azure AD (biorą się w usłudze Azure AD) przy użyciu nowych obiektów jest dodać/zaktualizować podczas synchronizacji, które reprezentują tej samej jednostki (użytkowników, grup) w środowisku lokalnym.
* **InvalidSoftMatch** błąd występuje, gdy twardych dopasowanie nie znajdzie każdego pasującego obiektu **i** dopasowanie słabe umożliwia znalezienie pasującego obiektu, ale ten obiekt ma inną wartość *immutableId* niż przychodzące obiektu *SourceAnchor*, sugerujące, że pasującego obiektu była synchronizowana z innym obiektem z na lokalne usługi Active Directory.

Oznacza to, aby dopasowanie słabe do pracy, obiekt jest dopasowany nietrwałego za pomocą nie mają żadnej wartości dla *immutableId*. Jeśli dowolny obiekt z *immutableId* zestawu z wartością kończy się niepowodzeniem twardych dopasowanie, ale spełniające kryteria miękkiego, w wyniku operacji InvalidSoftMatch błąd synchronizacji.

Schemat usługi Active Directory systemu Azure nie zezwala na dwóch lub więcej obiektów, które mają taką samą wartość z następujących atrybutów. \(Nie jest kompletną listą.\)

* ProxyAddresses
* UserPrincipalName
* onPremisesSecurityIdentifier
* ObjectId

> [!NOTE]
> [Odporność platformy Azure AD atrybut zduplikowany atrybut](how-to-connect-syncservice-duplicate-attribute-resiliency.md) funkcji jest również udostępniana jako zachowanie domyślne usługi Azure Active Directory.  Zmniejsza to liczbę wszystkich błędów synchronizacji widzianych przez program Azure AD Connect (a także innych klientów synchronizacji), wprowadzając usługi Azure AD bardziej odporne na błędy w taki sposób, obsługuje on zduplikowanych atrybutów ProxyAddresses i UserPrincipalName atrybuty w środowiska lokalne usługi AD. Ta funkcja nie naprawić błędy dublowania. Dlatego dane nadal musi mieć stałą. Jednak umożliwia inicjowanie obsługi nowych obiektów, które w przeciwnym razie jest zablokowany aprowizowana z powodu zduplikowane wartości w usłudze Azure AD. Spowoduje to także zmniejszyć liczbę błędów synchronizacji zwracana do klienta synchronizacji.
> Ta funkcja jest włączona dla Twojej dzierżawy, nie będą widzieć błędów synchronizacji InvalidSoftMatch występujące podczas inicjowania obsługi administracyjnej nowych obiektów.
>
>

#### <a name="example-scenarios-for-invalidsoftmatch"></a>Przykładowe scenariusze dotyczące InvalidSoftMatch
1. Istnieją dwa lub więcej obiektów z taką samą wartość dla atrybutu ProxyAddresses usługi w usłudze Active Directory w środowisku lokalnym. Tylko jedna jest wprowadzenie aprowizowane w usłudze Azure AD.
2. Dwa lub więcej obiektów z taką samą wartość dla atrybutu userPrincipalName istnieje w usłudze Active Directory w środowisku lokalnym. Tylko jedna jest wprowadzenie aprowizowane w usłudze Azure AD.
3. Obiekt został dodany w w lokalnej usługi Active Directory o tej samej wartości atrybutu ProxyAddresses, jak w przypadku istniejącego obiektu w usłudze Azure Active Directory. Obiekt dodany w środowisku lokalnym jest wprowadzenie nieudostępniane w usłudze Azure Active Directory.
4. Obiekt został dodany w lokalne usługi Active Directory z taką samą wartość atrybutu userPrincipalName jak konto w usłudze Azure Active Directory. Obiekt nie jest wprowadzenie aprowizowane w usłudze Azure Active Directory.
5. Zsynchronizowane konta został przeniesiony z lasem A do B. lasu w usłudze Azure AD Connect (aparatem synchronizacji) została przy użyciu atrybutu ObjectGUID do obliczenia SourceAnchor. Po przeniesieniu lasu wartością SourceAnchor różni się. Nowy obiekt (z lasem B) kończy się niepowodzeniem do synchronizacji z istniejącego obiektu w usłudze Azure AD.
6. Obiekt zsynchronizowanych przypadkowo została usunięta z lokalnego usługi Active Directory i nowy obiekt został utworzony w usłudze Active Directory dla tej samej jednostki (na przykład użytkownik) bez usuwania konta usługi Azure Active Directory. Nowe konto kończy się niepowodzeniem, można zsynchronizować z istniejących obiektów usługi Azure AD.
7. Program Azure AD Connect została odinstalowania i ponownego zainstalowania. Podczas ponownej instalacji przez inny atrybut została wybrana jako SourceAnchor. Wszystkie obiekty, które było wcześniej synchronizowany zatrzymana, synchronizacja z powodu błędu InvalidSoftMatch.

#### <a name="example-case"></a>Przykład w przypadku:
1. **Bob Smith** jest synchronizowanych użytkowników w usłudze Azure Active Directory z na lokalne usługi Active Directory dla *contoso.com*
2. Bob Smith **UserPrincipalName** jest ustawiony jako **Komputer1\@contoso.com**.
3. **"abcdefghijklmnopqrstuv =="** jest **SourceAnchor** obliczana na podstawie usługi Azure AD Connect przy użyciu Bob Smith **objectGUID** z lokalnej usługi Active Directory, czyli  **wartość immutableId** Smith Bob w usłudze Azure Active Directory.
4. Bob ma również następujące wartości dla **proxyAddresses** atrybutu:
   * smtp: bobs@contoso.com
   * smtp: bob.smith@contoso.com
   * **SMTP: Robert\@contoso.com**
5. Nowy użytkownik **Taylora Bob**, zostanie dodany do lokalnej usłudze Active Directory.
6. Bob Taylora **UserPrincipalName** jest ustawiony jako **bobt\@contoso.com**.
7. **"abcdefghijkl0123456789 ==" "** jest **sourceAnchor** obliczana na podstawie usługi Azure AD Connect przy użyciu Taylora Bob **objectGUID** z na lokalne usługi Active Directory. Bob Taylora obiekt ma nie jest zsynchronizowane z usługą Azure Active Directory jeszcze.
8. Bob Taylora ma następujące wartości dla atrybutu proxyAddresses
   * smtp: bobt@contoso.com
   * smtp: bob.taylor@contoso.com
   * **SMTP: Robert\@contoso.com**
9. Podczas synchronizacji Azure AD Connect rozpozna dodanie Taylora Bob w lokalne usługi Active Directory i przekażą usługi Azure AD, aby wprowadzić tę samą zmianę.
10. Usługa Azure AD wykona najpierw twardych dopasowania. Oznacza to, wyszukiwanie będzie, jeśli dowolny obiekt wartość immutableId jest równa "abcdefghijkl0123456789 ==". Twarde dopasowanie zakończy się niepowodzeniem, żadnego innego obiektu w usłudze Azure AD będą dostępne dla tego immutableId.
11. Usługa Azure AD podejmie próbę Taylora Bob soft-match. Oznacza to, że zostanie wyszukany w przypadku dowolnego obiektu za pomocą proxyAddresses równa trzech wartości, w tym smtp: bob@contoso.com
12. Usługa Azure AD znajduje się obiekt Bob Smith, aby pasował do kryteriów miękkiego. Ale ten obiekt zawiera wartość immutableId = "abcdefghijklmnopqrstuv ==". co oznacza ten obiekt został zsynchronizowany z innego obiektu z lokalnego usługi Active Directory. W związku z tym, usługi Azure AD nie soft-match tych obiektów i ich wyniki w **InvalidSoftMatch** błąd synchronizacji.

#### <a name="how-to-fix-invalidsoftmatch-error"></a>Jak rozwiązać błąd InvalidSoftMatch
Najbardziej typową przyczyną błędu InvalidSoftMatch to dwa obiekty o różnych SourceAnchor \(immutableId\) mają taką samą wartość dla atrybutów ProxyAddresses lub UserPrincipalName, które są używane podczas miękkiego przetwarzanie w usłudze Azure AD. Aby naprawić nieprawidłową dopasowanie słabe

1. Zidentyfikuj zduplikowane proxyAddresses, userPrincipalName lub inną wartość atrybutu, który jest przyczyną błędu. Także identyfikować, które dwa \(co najmniej\) obiekty są zaangażowane w konflikt. Raport wygenerowany przez [Azure AD Connect Health do celów synchronizacji](https://aka.ms/aadchsyncerrors) może pomóc w zidentyfikowaniu dwóch obiektów.
2. Zidentyfikuj obiektu, który powinna w dalszym ciągu ma zduplikowane wartości i obiektu, który nie powinien.
3. Usuń zduplikowane wartości z obiektu, który nie powinien mieć tę wartość. Należy wprowadzić zmiany w katalogu, w której pochodzi z obiektu. W niektórych przypadkach może być konieczne Usuń jeden z obiektów w konflikcie.
4. Jeśli wprowadzono zmiany w lokalnych AD Poinformuj program Azure AD Connect zsynchronizować zmiany.

Raporty o błędach synchronizacji w usłudze Azure AD Connect Health do celów synchronizacji są aktualizowane co 30 minut i zawierać błędy z ostatnia próba synchronizacji.

> [!NOTE]
> Wartość ImmutableId, zgodnie z definicją, nie należy zmieniać w okresie istnienia obiektu. Jeśli program Azure AD Connect nie został skonfigurowany z niektórych scenariuszy należy pamiętać, na liście powyżej, może wystąpić w sytuacji, w którym program Azure AD Connect oblicza inną wartość SourceAnchor dla obiektu AD, która reprezentuje tej samej jednostki (tej samej grupy użytkowników / / Skontaktuj się z pomocą itp.) zawierający istniejących obiektów usługi Azure AD, którą chcesz nadal używać.
>
>

#### <a name="related-articles"></a>Powiązane artykuły
* [Zduplikowana lub nieprawidłowa atrybuty zapobieganie synchronizacji katalogów w usłudze Office 365](https://support.microsoft.com/kb/2647098)

### <a name="objecttypemismatch"></a>ObjectTypeMismatch
#### <a name="description"></a>Opis
Gdy usługa Azure AD próbuje nietrwałe dopasować dwa obiekty, jest to możliwe, że dwa obiekty o różnych "typu obiektu" (na przykład użytkownika, grupy itp. Skontaktuj się z pomocą) mają takie same wartości dla atrybutów używanych do wykonywania dopasowanie słabe. Jak duplikowanie tych atrybutów nie jest dozwolona w usłudze Azure AD, operacja może spowodować błąd synchronizacji "ObjectTypeMismatch".

#### <a name="example-scenarios-for-objecttypemismatch-error"></a>Przykładowe scenariusze dotyczące ObjectTypeMismatch błąd
* Grupy zabezpieczeń obsługujące pocztę jest tworzony w usłudze Office 365. Administrator dodaje nowego użytkownika lub kontakt w lokalna Usługa AD (który nie jest zsynchronizowany z usługą Azure AD jeszcze) z taką samą wartość dla atrybutu ProxyAddresses jak w przypadku grupy usługi Office 365.

#### <a name="example-case"></a>Przykład case
1. Administrator tworzy nową grupę zabezpieczeń obsługujące pocztę w usłudze Office 365 dla działu podatku i przypisuje mu adres e-mail jako tax@contoso.com. Ta grupa jest przypisana wartość atrybutu ProxyAddresses **smtp: podatku\@contoso.com**
2. Nowy użytkownik nie przyłączy Contoso.com i utworzeniu konta dla użytkownika w środowisku lokalnym za pomocą proxyAddress jako **smtp: podatku\@contoso.com**
3. Gdy program Azure AD Connect będzie synchronizować nowe konto użytkownika, otrzyma błąd "ObjectTypeMismatch".

#### <a name="how-to-fix-objecttypemismatch-error"></a>Jak rozwiązać błąd ObjectTypeMismatch
Najbardziej typową przyczyną błędu ObjectTypeMismatch czy dwa obiekty innego typu (użytkownikiem, grupą, skontaktuj się z pomocą itp.) mają taką samą wartość atrybutu ProxyAddresses. Aby można było naprawić ObjectTypeMismatch:

1. Identyfikowanie zduplikowanych proxyAddresses (lub innego atrybutu) wartość, która jest przyczyną błędu. Także identyfikować, które dwa \(co najmniej\) obiekty są zaangażowane w konflikt. Raport wygenerowany przez [Azure AD Connect Health do celów synchronizacji](https://aka.ms/aadchsyncerrors) może pomóc w zidentyfikowaniu dwóch obiektów.
2. Zidentyfikuj obiektu, który powinna w dalszym ciągu ma zduplikowane wartości i obiektu, który nie powinien.
3. Usuń zduplikowane wartości z obiektu, który nie powinien mieć tę wartość. Należy pamiętać, że należy wprowadzić zmianę w katalogu, w której pochodzi z obiektu. W niektórych przypadkach może być konieczne Usuń jeden z obiektów w konflikcie.
4. Jeśli wprowadzono zmiany w lokalnych AD Poinformuj program Azure AD Connect zsynchronizować zmiany. Raport o błędach synchronizacji w usłudze Azure AD Connect Health do celów synchronizacji jest aktualizowany co 30 minut i zawiera błędy z ostatnia próba synchronizacji.

## <a name="duplicate-attributes"></a>Zduplikowane atrybuty
### <a name="attributevaluemustbeunique"></a>AttributeValueMustBeUnique
#### <a name="description"></a>Opis
Schemat usługi Active Directory systemu Azure nie zezwala na dwóch lub więcej obiektów, które mają taką samą wartość z następujących atrybutów. To, że każdy obiekt w usłudze Azure AD musi mieć unikatową wartość tych atrybutów w podanym wystąpieniu.

* ProxyAddresses
* UserPrincipalName

Jeśli program Azure AD Connect próbuje dodać nowy obiekt lub zaktualizować istniejący obiekt z wartością dla powyższych atrybutów, która jest już przypisany do innego obiektu w usłudze Azure Active Directory, wykonanie tej operacji skutkuje "AttributeValueMustBeUnique" Błąd synchronizacji.

#### <a name="possible-scenarios"></a>Możliwe scenariusze:
1. Zduplikowane wartości jest przypisane do już zsynchronizowane obiektu, który powoduje konflikt z innym obiektem zsynchronizowane.

#### <a name="example-case"></a>Przykład w przypadku:
1. **Bob Smith** jest synchronizowanych użytkowników w usłudze Azure Active Directory z na lokalne usługi Active Directory dla domeny contoso.com
2. Bob Smith **UserPrincipalName** w środowisku lokalnym jest ustawiony jako **Komputer1\@contoso.com**.
3. Bob ma również następujące wartości dla **proxyAddresses** atrybutu:
   * smtp: bobs@contoso.com
   * smtp: bob.smith@contoso.com
   * **SMTP: Robert\@contoso.com**
4. Nowy użytkownik **Taylora Bob**, zostanie dodany do lokalnej usłudze Active Directory.
5. Bob Taylora **UserPrincipalName** jest ustawiony jako **bobt\@contoso.com**.
6. **Bob Taylora** ma następujące wartości dla **ProxyAddresses** atrybutu i. smtp: bobt@contoso.com ii. smtp: bob.taylor@contoso.com
7. Obiekt Taylora Bob jest synchronizowany z usługą Azure AD pomyślnie.
8. Administrator zdecydowała się zaktualizować Taylora Bob **ProxyAddresses** atrybutu z następującą wartością: czy mogę. **SMTP: Robert\@contoso.com**
9. Usługi Azure AD będzie próbował zaktualizować Taylora Bob obiektu w usłudze Azure AD z powyższą wartość, ale ta operacja zakończy się niepowodzeniem jako że wartość ProxyAddresses jest już przypisana do Bob Smith zostaje błąd "AttributeValueMustBeUnique".

#### <a name="how-to-fix-attributevaluemustbeunique-error"></a>Jak rozwiązać błąd AttributeValueMustBeUnique
Najbardziej typową przyczyną błędu AttributeValueMustBeUnique to dwa obiekty o różnych SourceAnchor \(immutableId\) mają taką samą wartość dla atrybutów ProxyAddresses lub UserPrincipalName. Aby naprawić błąd AttributeValueMustBeUnique

1. Zidentyfikuj zduplikowane proxyAddresses i userPrincipalName inną wartość atrybutu, który jest przyczyną błędu. Także identyfikować, które dwa \(co najmniej\) obiekty są zaangażowane w konflikt. Raport wygenerowany przez [Azure AD Connect Health do celów synchronizacji](https://aka.ms/aadchsyncerrors) może pomóc w zidentyfikowaniu dwóch obiektów.
2. Zidentyfikuj obiektu, który powinna w dalszym ciągu ma zduplikowane wartości i obiektu, który nie powinien.
3. Usuń zduplikowane wartości z obiektu, który nie powinien mieć tę wartość. Należy pamiętać, że należy wprowadzić zmianę w katalogu, w której pochodzi z obiektu. W niektórych przypadkach może być konieczne Usuń jeden z obiektów w konflikcie.
4. Jeśli wprowadzono zmiany w lokalnych AD umożliwiają program Azure AD Connect synchronizować zmiany błąd naprawione.

#### <a name="related-articles"></a>Powiązane artykuły
-[Zduplikowana lub nieprawidłowa atrybuty zapobieganie synchronizacji katalogów w usłudze Office 365](https://support.microsoft.com/kb/2647098)

## <a name="data-validation-failures"></a>Błędy sprawdzania poprawności danych
### <a name="identitydatavalidationfailed"></a>IdentityDataValidationFailed
#### <a name="description"></a>Opis
Usługa Azure Active Directory wymusza różnych ograniczeń dotyczących danych przed zezwoleniem na te dane są zapisywane w katalogu. Te ograniczenia dotyczą upewnij się, że użytkownicy końcowi uzyskują najlepsze doświadczenia podczas korzystania z aplikacji, które są zależne od tych danych.

#### <a name="scenarios"></a>Scenariusze
a. Wartość atrybutu UserPrincipalName ma nieprawidłowy/nieobsługiwany znaki.
b. Atrybut UserPrincipalName nie jest zgodna z wymaganym formatem.

#### <a name="how-to-fix-identitydatavalidationfailed-error"></a>Jak rozwiązać błąd IdentityDataValidationFailed
a. Upewnij się, że atrybut userPrincipalName jest obsługiwane znaki i wymagany format.

#### <a name="related-articles"></a>Powiązane artykuły
* [Przygotowanie do inicjowania obsługi użytkowników za pomocą synchronizacji katalogów usługi Office 365](https://support.office.com/article/Prepare-to-provision-users-through-directory-synchronization-to-Office-365-01920974-9e6f-4331-a370-13aea4e82b3e)

### <a name="federateddomainchangeerror"></a>FederatedDomainChangeError
#### <a name="description"></a>Opis
Ten przypadek skutkuje **"FederatedDomainChangeError"** błąd synchronizacji, gdy sufiks domeny atrybutu UserPrincipalName użytkownika zostanie zmieniona z jednej domeny federacyjnej do innej domeny federacyjnej.

#### <a name="scenarios"></a>Scenariusze
Synchronizacja użytkownika sufiks UserPrincipalName został zmieniony z jednej domeny federacyjnej do innej domeny federacyjnej w środowisku lokalnym. Na przykład *UserPrincipalName = bob\@contoso.com* została zmieniona na *UserPrincipalName = bob\@fabrikam.com*.

#### <a name="example"></a>Przykład
1. Bob Smith, konto dla domeny Contoso.com, pobiera dodane jako nowy użytkownik w usłudze Active Directory za pomocą właściwości UserPrincipalName bob@contoso.com
2. Bob zostanie przeniesiony do innego oddziału contoso.com o nazwie Fabrikam.com i ich UserPrincipalName jest zmieniana na bob@fabrikam.com
3. Domeny contoso.com i fabrikam.com są domen federacyjnych w usłudze Azure Active Directory.
4. UserPrincipalName przez Boba zostaje zaktualizowana i powoduje błąd synchronizacji "FederatedDomainChangeError".

#### <a name="how-to-fix"></a>Jak naprawić
Jeśli sufiks UserPrincipalName użytkownika została zaktualizowana z bob @**contoso.com** do bob\@**fabrikam.com**, gdzie oba **contoso.com** i  **Fabrikam.com** są **domen federacyjnych**, następnie wykonaj poniższe kroki, aby naprawić błąd synchronizacji

1. Aktualizuj UserPrincipalName użytkownika w usłudze Azure AD z bob@contoso.com do bob@contoso.onmicrosoft.com. Za pomocą następującego polecenia programu PowerShell przy użyciu modułu Azure AD PowerShell: `Set-MsolUserPrincipalName -UserPrincipalName bob@contoso.com -NewUserPrincipalName bob@contoso.onmicrosoft.com`
2. Zezwalaj na następny cykl synchronizacji próby synchronizacji. Wykonanie synchronizacji czasu zakończy się pomyślnie i zaktualizuje UserPrincipalName Roberta do bob@fabrikam.com zgodnie z oczekiwaniami.

#### <a name="related-articles"></a>Powiązane artykuły
* [Zmiany nie są synchronizowane za pomocą narzędzia Azure Active Directory Sync po zmianie nazwy UPN konta użytkownika do użycia z innej domeny federacyjnej](https://support.microsoft.com/help/2669550/changes-aren-t-synced-by-the-azure-active-directory-sync-tool-after-you-change-the-upn-of-a-user-account-to-use-a-different-federated-domain)

## <a name="largeobject"></a>LargeObject
### <a name="description"></a>Opis
Gdy atrybut przekroczy dozwolony limit rozmiaru, długości lub liczby ustawiony przez schemat usługi Active Directory platformy Azure, wynikiem operacji synchronizacji **LargeObject** lub **ExceededAllowedLength**błąd synchronizacji. Zazwyczaj ten błąd występuje w przypadku następujących atrybutów

* userCertificate
* userSMIMECertificate
* thumbnailPhoto
* proxyAddresses

### <a name="possible-scenarios"></a>Możliwe scenariusze
1. Atrybut userCertificate przez Boba zapisuje zbyt wiele certyfikatów, przypisany do niego. Mogą one zawierać starsze, wygasłych certyfikatów. Stały limit jest 15 certyfikatów. Więcej informacji na temat sposobu obsługi błędów LargeObject za pomocą atrybutu userCertificate można znaleźć w artykule [błędów obsługi LargeObject spowodowanych przez atrybut userCertificate](tshoot-connect-largeobjecterror-usercertificate.md).
2. Atrybut userSMIMECertificate przez Boba zapisuje zbyt wiele certyfikatów, przypisany do niego. Mogą one zawierać starsze, wygasłych certyfikatów. Stały limit jest 15 certyfikatów.
3. Thumbnailphoto usługa Roberta, ustaw w usłudze Active Directory jest zbyt duża, można synchronizować w usłudze Azure AD.
4. Podczas automatycznego wypełniania parametrów atrybutu ProxyAddresses usługi w usłudze Active Directory obiekt ma zbyt wiele ProxyAddresses przypisane.

### <a name="how-to-fix"></a>Jak naprawić
1. Upewnij się, że atrybut powoduje błąd w ramach ograniczenia dozwolone.

## <a name="existing-admin-role-conflict"></a>Istniejące konflikt roli administratora

### <a name="description"></a>Opis
**Istniejącego konfliktu roli administratora** ma miejsce w obiekcie użytkownika podczas synchronizacji, gdy ma ten obiekt użytkownika:

- uprawnienia administracyjne i
- tym samym UserPrincipalName jako istniejący obiekt usługi Azure AD

Program Azure AD Connect nie może dopasowanie słabe obiektu użytkownika z lokalnej usługi AD za pomocą obiektu użytkownika w usłudze Azure AD, która ma przypisane do niego rola administracyjna.  Aby uzyskać więcej informacji, zobacz [populacji UserPrincipalName usługi Azure AD](plan-connect-userprincipalname.md)

![Istniejącego administratora](media/tshoot-connect-sync-errors/existingadmin.png)


### <a name="how-to-fix"></a>Jak naprawić
Aby rozwiązać tego problemu, wykonaj, jedną z następujących czynności:


- Zmień właściwości UserPrincipalName na wartość, która nie pasuje do użytkownika administratora w usłudze Azure AD — które spowoduje utworzenie nowego użytkownika w usłudze Azure AD za pomocą dopasowywania UserPrincipalName
- Usuń rolę administracyjną użytkownika administratora w usłudze Azure AD, co umożliwi nietrwałego zgodność między obiektu użytkownika w środowisku lokalnym i do istniejącego obiektu użytkownika w usłudze Azure AD.

>[!NOTE]
>Można przypisać roli administracyjnej do istniejącego obiektu użytkownika ponownie po ukończeniu nietrwałego zgodność między obiektu użytkownika w środowisku lokalnym i obiektów użytkownika usługi Azure AD.

## <a name="related-links"></a>Linki pokrewne
* [Znajdź obiekty usługi Active Directory w Centrum administracyjne usługi Active Directory](https://technet.microsoft.com/library/dd560661.aspx)
* [Tworzenie zapytań względem usługi Azure Active Directory obiektu przy użyciu programu PowerShell usługi Azure Active Directory](https://msdn.microsoft.com/library/azure/jj151815.aspx)
