---
title: Tożsamość synchronizacji i zduplikowany atrybut odporności | Dokumentacja firmy Microsoft
description: Nowe zachowanie sposób obsługi obiektów z konfliktami nazwy UPN lub ProxyAddress podczas synchronizacji katalogów za pomocą usługi Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 537a92b7-7a84-4c89-88b0-9bce0eacd931
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.component: hybrid
ms.author: markvi
ms.openlocfilehash: cfed9d32e919cc3c1b7b9c2a6ea5ddb31f2a8fb9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34593212"
---
# <a name="identity-synchronization-and-duplicate-attribute-resiliency"></a>Synchronizacja tożsamości i odporność względem zduplikowanych atrybutów
Zduplikowany atrybut odporności to funkcja usługi Azure Active Directory, która wyeliminuje tarcie spowodowane **UserPrincipalName** i **ProxyAddress** powoduje konflikt podczas uruchamiania narzędzi synchronizacji firmy Microsoft.

Te atrybuty są zazwyczaj muszą być unikatowe dla wszystkich **użytkownika**, **grupy**, lub **skontaktuj się z** obiektów w danej dzierżawy usługi Azure Active Directory.

> [!NOTE]
> Tylko użytkownicy mają nazwy UPN.
> 
> 

Ta funkcja umożliwia zachowanie nowych chmury część procesu synchronizacji, w związku z tym jest klient niezależny i zastosowanie w przypadku każdego produktu synchronizacji firmy Microsoft, w tym Azure AD Connect, narzędzia DirSync i MIM + łącznika. Ogólny termin "client synchronizacji" jest używana w tym dokumencie do reprezentowania któregokolwiek z tych produktów.

## <a name="current-behavior"></a>Aktualny efekt
W przypadku próby udostępnienia nowy obiekt z wartością nazwy UPN lub ProxyAddress, który narusza ograniczenie unikatowości. Ta usługa Azure Active Directory blokuje ten obiekt, utworzenie. Podobnie jeśli nazwy UPN lub ProxyAddress nieunikatowy jest aktualizowany obiekt, aktualizacja nie powiedzie się. Próba inicjowania obsługi administracyjnej lub aktualizacji próba zostanie ponowiona przez klienta synchronizacji po każdym cyklu eksportu, a zakończy się niepowodzeniem, dopóki nie zostanie rozwiązany. Wiadomość e-mail raport błąd jest generowany po każdej próbie i jest rejestrowany błąd przez klienta synchronizacji.

## <a name="behavior-with-duplicate-attribute-resiliency"></a>Zachowanie w przypadku odporności zduplikowany atrybut
Zamiast całkowicie brakiem obsługi administracyjnej lub zaktualizować zduplikowany atrybut obiektu, usługi Azure Active Directory "poddaje kwarantannie" zduplikowany atrybut, który mógłby naruszyć ograniczenie unikatowości. Jeśli ten atrybut jest wymagany do inicjowania obsługi, takie jak UserPrincipalName, usługa przypisuje wartość symbolu zastępczego. Format wartości tymczasowej  
"***<OriginalPrefix>+ < 4DigitNumber > @<InitialTenantDomain>. onmicrosoft.com***".  
Jeśli ten atrybut nie jest wymagana, takich jak **ProxyAddress**, Azure Active Directory po prostu poddaje kwarantannie atrybutu konflikt i kontynuuje tworzenie obiektów lub aktualizacji.

Po poddawania atrybutu, informacje o konflikt są wysyłane w tym samym błąd raportu wiadomości e-mail używane w starym zachowanie. Jednak te informacje jest wyświetlany tylko w raporcie o błędzie jeden raz, w przypadku kwarantanny nie nadal mają być rejestrowane w przyszłości wiadomości e-mail. Ponadto ponieważ eksportu dla tego obiektu zakończyła się pomyślnie, klient synchronizacji nie rejestruje błąd i nie ponownego tworzenia / aktualizacji operacji podczas kolejnej synchronizacji cykle.

Do obsługi tego zachowania został dodany nowy atrybut do użytkowników, grup i skontaktuj się z klas obiektów:  
**DirSyncProvisioningErrors**

To jest wielowartościowy atrybut, który jest używany do przechowywania powodujące konflikt atrybutów, które będzie narusza ograniczenie unikatowości powinny one zostać dodane normalnie. Zadanie czasomierza w tle została włączona w usłudze Azure Active Directory, która jest uruchamiana co godzinę, aby wyszukać zduplikowany atrybut konfliktów, które zostały rozwiązane i automatycznie usuwa zagrożona atrybutów z kwarantanny.

### <a name="enabling-duplicate-attribute-resiliency"></a>Włączaniu odporności zduplikowany atrybut
Zduplikowany atrybut odporności będą używane nowe zachowanie domyślne we wszystkich dzierżaw usługi Azure Active Directory. Na będzie domyślnie dla wszystkich dzierżawców, które włączony synchronizacja po raz pierwszy 22 sierpnia 2016 lub nowszy. Dzierżawcy włączony synchronizacja przed tą datą ma włączenie w partiach tej funkcji. Wdrożenia rozpocznie się we wrześniu 2016 r, a powiadomienie e-mail będą wysyłane do każdego dzierżawcy techniczne powiadomień kontakt z określonej daty, gdy funkcja zostanie włączona.

> [!NOTE]
> Gdy włączono zduplikowany atrybut odporności nie można wyłączyć.

Aby sprawdzić, czy ta funkcja jest włączona dla Twojej dzierżawy, możesz to zrobić przez pobranie najnowszej wersji modułu programu PowerShell usługi Azure Active Directory i systemem:

`Get-MsolDirSyncFeatures -Feature DuplicateUPNResiliency`

`Get-MsolDirSyncFeatures -Feature DuplicateProxyAddressResiliency`

> [!NOTE]
> Polecenia cmdlet Set-MsolDirSyncFeature umożliwia już z wyprzedzeniem włączyć funkcję zduplikowany atrybut odporności przed jest włączona dla Twojej dzierżawy. Aby móc przetestować funkcję, należy utworzyć nową dzierżawę usługi Azure Active Directory.

## <a name="identifying-objects-with-dirsyncprovisioningerrors"></a>Identyfikowania obiektów z DirSyncProvisioningErrors
Dostępne są obecnie dwie metody do identyfikowania obiektów, które mają te błędy z powodu konfliktów zduplikowanych właściwości, programu PowerShell usługi Azure Active Directory i portalu administracyjnego usługi Office 365. Brak planów rozszerzenie dodatkowe portalu na podstawie raportowania w przyszłości.

### <a name="azure-active-directory-powershell"></a>Azure Active Directory PowerShell
Dla polecenia cmdlet programu PowerShell w tym temacie są spełnione następujące warunki:

* Wszystkie następujące polecenia cmdlet jest uwzględniana wielkość liter.
* **— ErrorCategory PropertyConflict** zawsze musi być uwzględniony. Nie ma obecnie żadnych innych typów, z **ErrorCategory**, ale może to zostać rozszerzona w przyszłości.

Po pierwsze, Rozpoczynanie pracy przez uruchomienie **Connect MsolService** i wprowadzić poświadczenia administratora dzierżawy.

Następnie za pomocą następujących poleceń cmdlet i operatory wyświetlanie błędów na różne sposoby:

1. [Wyświetl wszystkie](#see-all)
2. [Według typu właściwości](#by-property-type)
3. [Wartości powodujące konflikt](#by-conflicting-value)
4. [Za pomocą wyszukiwania ciągu](#using-a-string-search)
5. [Sortowane](#sorted)
6. [Ograniczona ilość lub wszystkie](#in-a-limited-quantity-or-all)

#### <a name="see-all"></a>Zobacz wszystkie
Po nawiązaniu połączenia, aby wyświetlić ogólną listę atrybutów inicjowania obsługi błędów w dzierżawie Uruchom:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict`

Daje to wyniki podobne do poniższych:  
 ![Get-MsolDirSyncProvisioningError](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/1.png "Get-MsolDirSyncProvisioningError")  

#### <a name="by-property-type"></a>Według typu właściwości
Aby sprawdzić błędy według typu właściwości, Dodaj **- PropertyName** flaga z **UserPrincipalName** lub **ProxyAddresses** argumentu:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName UserPrincipalName`

Lub

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName ProxyAddresses`

#### <a name="by-conflicting-value"></a>Wartości powodujące konflikt
Aby sprawdzić błędy dotyczące konkretnej właściwości, Dodaj **- PropertyValue** flagi (**- PropertyName** musi być również używane podczas dodawania tej flagi):

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyValue User@domain.com -PropertyName UserPrincipalName`

#### <a name="using-a-string-search"></a>Za pomocą wyszukiwania ciągu
Robić, użyj wyszukiwania szeroki ciąg **parametru - Wyszukiwany_ciąg** flagi. Może być używany niezależnie od wszystkich powyższych flag, z wyjątkiem produktów **- ErrorCategory PropertyConflict**, który jest zawsze wymagane:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -SearchString User`

#### <a name="in-a-limited-quantity-or-all"></a>Ograniczona ilość lub wszystkie
1. **MaxResults <Int>**  pozwala ograniczyć zapytanie do określonej liczby wartości.
2. **Wszystkie** może służyć do zapewnienia w przypadku dużej liczby błędów istnieje pobierane są wszystkie wyniki.

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -MaxResults 5`

## <a name="office-365-admin-portal"></a>Portal administracyjny usługi Office 365
Błędy synchronizacji katalogu można wyświetlić w Centrum administracyjnym usługi Office 365. Raport w portalu usługi Office 365 są wyświetlane tylko **użytkownika** obiektów, które mają te błędy. Nie są wyświetlane informacje o konfliktach między **grup** i **kontaktów**.

![Aktywni użytkownicy](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/1234.png "aktywni użytkownicy")

Aby uzyskać instrukcje dotyczące sposobu wyświetlania błędów synchronizacji katalogu w Centrum administracyjnym usługi Office 365, zobacz [zidentyfikować błędy synchronizacji katalogu w usłudze Office 365](https://support.office.com/en-us/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067).

### <a name="identity-synchronization-error-report"></a>Raport o błędzie synchronizacji tożsamości
Gdy obiekt z konfliktem zduplikowany atrybut jest obsługiwany z to nowe zachowanie powiadomienie jest dołączany do standardowych wiadomości e-mail raport o błędzie synchronizacji tożsamości wysyłanym do powiadomień technicznych skontaktuj się z dzierżawy. Istnieje jednak ważne zmiany w tym zachowaniu. W przeszłości informacji na temat konflikt zduplikowany atrybut będzie uwzględnione w raporcie każdej kolejny błąd, dopóki konflikt został rozwiązany. Z to nowe zachowanie powiadomienie o błędzie dla danego konfliktu wystąpić tylko raz — w momencie atrybut powodujące konflikt zostanie poddane kwarantannie.

Oto przykład wygląd powiadomienia e-mail dla konfliktu ProxyAddress:  
    ![Aktywni użytkownicy](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/6.png "aktywni użytkownicy")  

## <a name="resolving-conflicts"></a>Rozwiązywanie konfliktów
Rozwiązywanie problemów z taktyk strategii i rozwiązania błędów powinny różnić się od sposób w przeszłości były obsługi błędów zduplikowany atrybut. Jedyna różnica polega na tym, że zadanie czasomierza wachlarzy za pomocą dzierżawy na stronie usługi automatycznie Dodaj atrybut zagrożona do prawidłowego obiektu, gdy konflikt został rozwiązany.

Artykule przedstawiono różne strategie rozwiązywania problemów i rozwiązania: [zduplikowane lub nieprawidłowe atrybuty zapobieganie synchronizacji katalogów w usłudze Office 365](https://support.microsoft.com/kb/2647098).

## <a name="known-issues"></a>Znane problemy
Żadna z tych znanych problemów powoduje spadek utracie lub usługi danych. Niektóre z nich są estetycznych, inne spowodować standardu "*wstępne odporności*" błędy zduplikowany atrybut zostanie wygenerowany zamiast poddawania atrybutu konflikt, a druga spowoduje, że niektóre błędy wymagać bardzo ręcznego konfigurowania.

**Zachowanie rdzeni:**

1. Obiekty z określonym atrybutem konfiguracje w dalszym ciągu otrzymywać błędy eksportu przeciwieństwie zduplikowane atrybuty kwarantanną.  
   Na przykład:
   
    a. Nowy użytkownik jest tworzony w usłudze AD za pomocą nazwy UPN **Joe@contoso.com** i ProxyAddress **smtp:Joe@contoso.com**
   
    b. Właściwości tego obiektu powodują konflikt z istniejącą grupę, gdzie jest ProxyAddress **SMTP:Joe@contoso.com**.
   
    c. Podczas eksportu **konflikt ProxyAddress** zamiast konflikt atrybuty poddane kwarantannie, zostanie zgłoszony błąd. Jak miałoby to miejsce przed włączeniem funkcji ochrony na każdym cyklu kolejnej synchronizacji jest ponowić próbę operacji.
2. Jeśli dwie grupy są tworzone lokalne z tego samego adresu SMTP, zawiedzie świadczeniem przy pierwszej próbie ze standardowych duplikatem **ProxyAddress** błędu. Jednak zduplikowaną wartość prawidłowo zostanie poddane kwarantannie przy następnym cyklu synchronizacji.

**Raport portalu Office**:

1. Szczegółowy komunikat o błędzie dla dwóch obiektów w zestawie konflikt nazwy UPN są takie same. Oznacza to, że oba miały ich nazwy UPN zmienić / poddane kwarantannie, gdy w rzeczywistości tylko jeden z nich ma wszystkie dane zmienione.
2. Szczegółowy komunikat o błędzie dla konflikt nazwy UPN zawiera nieprawidłową nazwą wyświetlaną dla użytkownika, który miał ich nazwy UPN zmienić kwarantannie. Na przykład:
   
    a. **Użytkownik A** synchronizuje pierwszy z **UPN = User@contoso.com** .
   
    b. **Użytkownik B** próbował można zsynchronizować się dalej z **UPN = User@contoso.com** .
   
    c. **Użytkownik B** głównej nazwy użytkownika jest zmieniana na **User1234@contoso.onmicrosoft.com** i **User@contoso.com** jest dodawany do **DirSyncProvisioningErrors**.
   
    d. Komunikat o błędzie dla **użytkownika B** powinny wskazywać, że **użytkownika A** ma już **User@contoso.com** jako nazwy UPN, ale zawiera **użytkownika B** własnych Nazwa wyświetlana.

**Raport o błędzie synchronizacji tożsamości**:

Łącze do *kroki dotyczące sposobu rozwiązania tego problemu* jest nieprawidłowy:  
    ![Aktywni użytkownicy](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/6.png "aktywni użytkownicy")  

Powinny wskazywać [ https://aka.ms/duplicateattributeresiliency ](https://aka.ms/duplicateattributeresiliency).

## <a name="see-also"></a>Zobacz także
* [Synchronizacja programu Azure AD Connect](active-directory-aadconnectsync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md)
* [Zidentyfikuj błędy synchronizacji katalogu w usłudze Office 365](https://support.office.com/en-us/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067)

