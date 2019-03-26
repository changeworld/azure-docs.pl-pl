---
title: Tożsamość synchronizacji i odporności zduplikowanego atrybutu | Dokumentacja firmy Microsoft
description: Nowe zachowanie sposób obsługi obiektów przy użyciu nazwy UPN lub ProxyAddress konfliktów podczas synchronizacji katalogów za pomocą usługi Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 537a92b7-7a84-4c89-88b0-9bce0eacd931
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a65af5a5ea0629b617c4e736d8c110cbb9aa540c
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58438305"
---
# <a name="identity-synchronization-and-duplicate-attribute-resiliency"></a>Synchronizacja tożsamości i odporność względem zduplikowanych atrybutów
Odporność na zduplikowane atrybuty to funkcja usługi Azure Active Directory, która zostanie całkowicie wyeliminować zajmowania się przyczyną **UserPrincipalName** i **ProxyAddress** powoduje konflikt podczas uruchamiania jednego z firmy Microsoft narzędzia do synchronizacji.

Te atrybuty są zazwyczaj musi być unikatowa we wszystkich **użytkownika**, **grupy**, lub **skontaktuj się z pomocą** obiektów w danej dzierżawie usługi Azure Active Directory.

> [!NOTE]
> Tylko użytkownicy mogą mieć nazwy UPN.
> 
> 

Nowe zachowanie, który umożliwia ta funkcja jest w chmurze części procesu synchronizacji, w związku z tym klient niezależny i istotne dla każdego produktu synchronizacji firmy Microsoft, w tym Azure AD Connect, narzędzia DirSync i MIM + łącznika. Ogólny termin określający "klienta synchronizacji" jest używany w tym dokumencie do reprezentowania któregokolwiek z tych produktów.

## <a name="current-behavior"></a>Aktualny efekt
W przypadku próby aprowizacji nowego obiektu przy użyciu nazwy UPN lub ProxyAddress wartość, która narusza to ograniczenie unikatowości usługi Azure Active Directory blokuje tego obiektu z tworzona. Podobnie jeśli obiekt jest aktualizowany przy użyciu nazwy UPN lub ProxyAddress nie jest unikatowa, aktualizacja nie powiedzie się. Próba inicjowania obsługi administracyjnej lub aktualizacja zostanie ponowiony przez klienta synchronizacji, podczas każdego cyklu eksportu, a zakończy się niepowodzeniem, dopóki nie zostanie rozwiązany konflikt. Wiadomość e-mail raportów błąd jest generowany po każdej próbie i jest rejestrowany błąd przez klienta synchronizacji.

## <a name="behavior-with-duplicate-attribute-resiliency"></a>Zachowanie przy użyciu odporność na zduplikowane atrybuty
Zamiast całkowicie przełączenia do aprowizowania lub aktualizowania obiektu za pomocą zduplikowany atrybut, usługi Azure Active Directory "poddaje" zduplikowany atrybut, który mógłby naruszyć ograniczenie unikatowości. Jeśli ten atrybut jest wymagany do obsługi administracyjnej, np. UserPrincipalName, usługa przypisuje wartość symbolu zastępczego. Format wartości tymczasowej  
“***\<OriginalPrefix>+\<4DigitNumber>\@\<InitialTenantDomain>.onmicrosoft.com***”.  
Jeśli ten atrybut nie jest wymagane, takie jak **ProxyAddress**, usługi Azure Active Directory, po prostu poddaje atrybut konfliktu i kontynuuje tworzenie obiektów lub aktualizacji.

Po poddawania atrybutu, informacje dotyczące konfliktu są wysyłane w ten sam błąd raportu wiadomości e-mail używany w stare zachowanie. Te informacje pojawia się tylko do raportów o błędach jeden raz w sytuacji informującą o kwarantannie nie nadal mają być rejestrowane w przyszłych wiadomości e-mail. Ponadto, ponieważ Eksport dla tego obiektu zakończyła się pomyślnie, klienta synchronizacji nie rejestruje błąd i nie ponawia próby tworzenia / operacja przy kolejnej synchronizacji cykli aktualizacji.

Do obsługi tego zachowania został dodany nowy atrybut do klas obiektów użytkownika, grupy i skontaktuj się z pomocą:  
**DirSyncProvisioningErrors**

To jest wielowartościowy atrybut, który jest używany do przechowywania powodujące konflikt atrybutów, które będzie narusza ograniczenie unikatowości, powinny one zostać dodane normalnie. Zadanie czasomierza w tle została włączona w usłudze Azure Active Directory, który jest uruchamiany co godzinę, aby wyszukać zduplikowany atrybut konflikty, które zostały rozwiązane i automatycznie usuwa atrybutów w danym z kwarantanny.

### <a name="enabling-duplicate-attribute-resiliency"></a>Włączanie odporność na zduplikowane atrybuty
Odporność na zduplikowane atrybuty będą nowe zachowanie domyślne we wszystkich dzierżawach usługi Azure Active Directory. Będzie ona na domyślnie dla wszystkich dzierżaw, w których włączono synchronizację po raz pierwszy w 22 sierpnia 2016 lub nowszym. Dzierżawcy włączona synchronizacja przed tą datą będzie funkcja jest włączona w partiach. To wdrożenie rozpocznie się we wrześniu 2016 i powiadomienie e-mail zostanie wysłane do kontaktu powiadomienie techniczne każdego dzierżawcy z określonej daty, kiedy funkcja zostanie włączona.

> [!NOTE]
> Po włączeniu odporność na zduplikowane atrybuty nie można wyłączyć.

Aby sprawdzić, jeśli ta funkcja jest włączona dla Twojej dzierżawy, możesz to zrobić przez najnowszą wersję modułu programu PowerShell usługi Azure Active Directory i uruchomienia:

`Get-MsolDirSyncFeatures -Feature DuplicateUPNResiliency`

`Get-MsolDirSyncFeatures -Feature DuplicateProxyAddressResiliency`

> [!NOTE]
> Polecenia cmdlet Set-MsolDirSyncFeature umożliwia nie jest już aktywnie włączyć funkcję odporność na zduplikowane atrybuty, zanim jest włączona dla Twojej dzierżawy. Aby można było przetestować funkcję, należy utworzyć nową dzierżawę usługi Azure Active Directory.

## <a name="identifying-objects-with-dirsyncprovisioningerrors"></a>Identyfikowania obiektów z DirSyncProvisioningErrors
Obecnie istnieją dwie metody, aby zidentyfikować obiekty, które mają te błędy z powodu konfliktów zduplikowaną właściwość, programu PowerShell usługi Azure Active Directory i [Centrum administracyjnego usługi Microsoft 365](https://admin.microsoft.com). Planujemy rozszerzenie dodatkowe portalu na podstawie raportowania w przyszłości.

### <a name="azure-active-directory-powershell"></a>Azure Active Directory PowerShell
Dla poleceń cmdlet programu PowerShell w tym temacie są spełnione następujące warunki:

* Wszystkie z następujących poleceń cmdlet są z uwzględnieniem wielkości liter.
* **— ErrorCategory PropertyConflict** zawsze musi być uwzględniony. Obecnie nie istnieją żadne inne typy **ErrorCategory**, ale może to zostać rozszerzona w przyszłości.

Po pierwsze, Rozpoczynanie pracy przez uruchomienie **Connect-MsolService** i wprowadzić poświadczenia administratora dzierżawy.

Następnie użyj następujących poleceń cmdlet i operatory, aby wyświetlić błędy na różne sposoby:

1. [Zobacz wszystko](#see-all)
2. [Według typu właściwości](#by-property-type)
3. [Według wartości powodujące konflikt](#by-conflicting-value)
4. [Za pomocą wyszukiwania ciągów](#using-a-string-search)
5. Sortowane
6. [Ograniczona ilość lub wszystkie](#in-a-limited-quantity-or-all)

#### <a name="see-all"></a>Zobacz wszystkie
Po nawiązaniu połączenia umożliwia wyświetlenie listy ogólnego atrybut obsługi błędów w dzierżawie Uruchom:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict`

To daje wyniki podobne do następującego:  
 ![Get-MsolDirSyncProvisioningError](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/1.png "Get-MsolDirSyncProvisioningError")  

#### <a name="by-property-type"></a>Według typu właściwości
Aby sprawdzić błędy według typu właściwości, Dodaj **- PropertyName** flaga z **UserPrincipalName** lub **ProxyAddresses** argumentu:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName UserPrincipalName`

Lub

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName ProxyAddresses`

#### <a name="by-conflicting-value"></a>Według wartości powodujące konflikt
Aby wyświetlić błędy dotyczące konkretnej właściwości, Dodaj **- PropertyValue** flagi (**- PropertyName** musi być również używana podczas dodawania tej flagi):

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyValue User@domain.com -PropertyName UserPrincipalName`

#### <a name="using-a-string-search"></a>Za pomocą wyszukiwania ciągów
Celu użyj wyszukiwania szerokiego ciągu **- Ciągwyszukiwania** flagi. Może to służyć niezależnie od wszystkich powyższych flagi, z wyjątkiem produktów **- ErrorCategory PropertyConflict**, który jest zawsze wymagane:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -SearchString User`

#### <a name="in-a-limited-quantity-or-all"></a>Ograniczona ilość lub wszystkie
1. **MaxResults \<Int >** może służyć do ograniczania zapytanie do określonej liczby wartości.
2. **Wszystkie** może służyć do zapewnienia wszystkie wyniki są pobierane w przypadku, że istnieje duża liczba błędów.

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -MaxResults 5`

## <a name="microsoft-365-admin-center"></a>Centrum administracyjne usługi Microsoft 365
Błędy synchronizacji katalogu można wyświetlić w Centrum administracyjnym usługi Microsoft 365. Raport w administratora usługi Microsoft 365 Centrum przedstawia tylko **użytkownika** obiektów, które mają te błędy. Nie są wyświetlane informacje na temat konflikty między **grup** i **kontakty**.

![Aktywni użytkownicy](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/1234.png "aktywni użytkownicy")

Aby uzyskać instrukcje dotyczące sposobu wyświetlania błędów synchronizacji katalogu w Centrum administracyjnym usługi Microsoft 365, zobacz [identyfikowanie błędów synchronizacji katalogu w usłudze Office 365](https://support.office.com/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067).

### <a name="identity-synchronization-error-report"></a>Raport o błędach synchronizacji tożsamości
Gdy odbywa się obiekt powodujący konflikt zduplikowany atrybut z tym działaniem nowe powiadomienie znajduje się w standardowych raport o błędach synchronizacji tożsamości wiadomości e-mail są wysyłane do powiadomienie techniczne skontaktuj się z pomocą dla dzierżawy. Istnieje jednak ważne zmiany w tym zachowaniu. W przeszłości informacji o konflikcie zduplikowany atrybut będzie uwzględniane w raporcie każdy kolejny błąd, aż konflikt został rozwiązany. Z tym zachowaniem nowe powiadomienia o błędzie dla danej konfliktu występować tylko raz — w momencie atrybutem sprzecznych jest objęte kwarantanną.

Oto jak wygląda powiadomienie e-mail dla konfliktu ProxyAddress przykład:  
    ![Aktywni użytkownicy](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/6.png "aktywni użytkownicy")  

## <a name="resolving-conflicts"></a>Rozwiązywanie konfliktów
Rozwiązywanie problemów z taktyka strategii i rozpoznawanie błędów powinny różnić się od sposób błędy zduplikowanych atrybutów są obsługiwane w przeszłości. Jedyna różnica polega na tym, że zadanie czasomierza wrzucając za pomocą dzierżawy po stronie usługi do automatycznego dodawania atrybutów w danym do odpowiedniego obiektu po rozwiązaniu konfliktu.

Następujący artykuł zawiera opis różnych strategii rozwiązywania problemów i rozwiązania. [Zduplikowana lub nieprawidłowa atrybuty zapobieganie synchronizacji katalogów w usłudze Office 365](https://support.microsoft.com/kb/2647098).

## <a name="known-issues"></a>Znane problemy
Żadna z tych znanych problemów powoduje spadek utratę lub usługi danych. Niektóre z nich są estetycznych, inne powodować standard "*wstępne odporności*" błędy zduplikowanych atrybutów zostanie wygenerowany zamiast poddawania atrybut konflikt, a druga powoduje, że pewne błędy wymagać dodatkowych ręcznego konfigurowania.

**Podstawowe zachowanie:**

1. Obiekty z konfiguracjami określonych atrybutów w dalszym ciągu otrzymywać komunikaty o błędach eksportu zamiast zduplikowanych atrybutów, które są objęte kwarantanną.  
   Na przykład:
   
    a. Nowy użytkownik jest tworzony w AD za pomocą nazwy UPN **Jan\@contoso.com** i ProxyAddress **smtp:Joe\@contoso.com**
   
    b. Właściwości tego obiektu powodują konflikt z istniejącą grupą, gdzie jest ProxyAddress **SMTP:Joe\@contoso.com**.
   
    c. Podczas eksportu **konflikt ProxyAddress** zamiast atrybutów konflikt poddane kwarantannie, zostanie zgłoszony błąd. Jest próba powtórzenia operacji podczas każdego cyklu kolejnej synchronizacji, ponieważ miałoby to miejsce przed włączeniem funkcji odporności.
2. Jeśli dwie grupy są tworzone w środowisku lokalnym za pomocą tego samego adresu SMTP, zawiedzie aprowizację przy pierwszej próbie ze zduplikowanymi standardowa **ProxyAddress** błędu. Jednak zduplikowaną wartość jest objęte kwarantanną prawidłowo po następnym cyklu synchronizacji.

**Raport portalu Office**:

1. Szczegółowy komunikat o błędzie dla dwóch obiektów w zestawie konflikt nazwy UPN jest taka sama. Oznacza to, że oba miały, ich nazwy UPN zmienione / poddane kwarantannie, gdy w rzeczywistości tylko jeden z nich ma wszelkie zmiany danych.
2. Szczegółowy komunikat o błędzie dla konflikt nazwy UPN zawiera nieprawidłową wartość displayName dla użytkownika, który miał ich nazwy UPN, zmienić kwarantannie. Na przykład:
   
    a. **Użytkownik A** synchronizuje się najpierw w **nazwy UPN użytkownika =\@contoso.com**.
   
    b. **Użytkownik B** podejmowana jest próba zostanie on zsynchronizowany się dalej z **nazwy UPN użytkownika =\@contoso.com**.
   
    c. **Użytkownik B** nazwy UPN jest zmieniana na **User1234\@contoso.onmicrosoft.com** i **użytkownika\@contoso.com** jest dodawany do **DirSyncProvisioningErrors** .
   
    d. Komunikat o błędzie dla **użytkownikowi B** powinny wskazywać, że **użytkownik A** ma już **użytkownika\@contoso.com** jak pokazano na nazwy UPN, ale **przez użytkownika B** własnych displayName.

**Raport o błędach synchronizacji tożsamości**:

Link do *instrukcje dotyczące sposobu rozwiązania tego problemu* jest niepoprawny:  
    ![Aktywni użytkownicy](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/6.png "aktywni użytkownicy")  

Powinien wskazywać [ https://aka.ms/duplicateattributeresiliency ](https://aka.ms/duplicateattributeresiliency).

## <a name="see-also"></a>Zobacz także
* [Synchronizacja programu Azure AD Connect](how-to-connect-sync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)
* [Identyfikowanie błędów synchronizacji katalogu w usłudze Office 365](https://support.office.com/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067)

