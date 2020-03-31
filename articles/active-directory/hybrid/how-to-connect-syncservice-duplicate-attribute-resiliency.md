---
title: Synchronizacja tożsamości i odporność atrybutów duplikatów | Dokumenty firmy Microsoft
description: Nowe zachowanie sposobu obsługi obiektów z konfliktami UPN lub ProxyAddress podczas synchronizacji katalogów przy użyciu usługi Azure AD Connect.
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
ms.openlocfilehash: 5585f0cd04dca4145f0322db9d625e35372b24b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78298347"
---
# <a name="identity-synchronization-and-duplicate-attribute-resiliency"></a>Synchronizacja tożsamości i odporność względem zduplikowanych atrybutów
Odporność zduplikowanych atrybutów to funkcja w usłudze Azure Active Directory, która wyeliminuje tarcie spowodowane konfliktami **UserPrincipalName** i SMTP **ProxyAddress** podczas uruchamiania jednego z narzędzi synchronizacji firmy Microsoft.

Te dwa atrybuty są zazwyczaj wymagane, aby być unikatowe dla wszystkich **użytkowników,** **grupowania**lub **kontaktu** obiektów w danej dzierżawy usługi Azure Active Directory.

> [!NOTE]
> Tylko użytkownicy mogą mieć sieci UPN.
> 
> 

Nowe zachowanie, które umożliwia ta funkcja znajduje się w części chmury potoku synchronizacji, dlatego jest niezależny od klienta i istotne dla dowolnego produktu synchronizacji firmy Microsoft, w tym usługi Azure AD Connect, DirSync i MIM + Connector. Ogólny termin "klient synchronizacji" jest używany w tym dokumencie do reprezentowania jednego z tych produktów.

## <a name="current-behavior"></a>Bieżące zachowanie
Jeśli istnieje próba aprowizowania nowego obiektu z nazwy UPN lub ProxyAddress wartość, która narusza to ograniczenie unikatowości, usługa Azure Active Directory blokuje ten obiekt z tworzone. Podobnie jeśli obiekt jest aktualizowany za pomocą nieużytej aktualizacjej wiązki ZJEDNOCZONEJ LUB adres proxy, aktualizacja nie powiedzie się. Próba inicjowania obsługi administracyjnej lub aktualizacji jest ponawiana przez klienta synchronizacji w każdym cyklu eksportu i nadal nie powiedzie się, dopóki konflikt nie zostanie rozwiązany. Wiadomość e-mail z raportem o błędzie jest generowana przy każdej próbie, a błąd jest rejestrowany przez klienta synchronizacji.

## <a name="behavior-with-duplicate-attribute-resiliency"></a>Zachowanie z odpornością zduplikowanych atrybutów
Zamiast całkowicie nie można aprowizować lub zaktualizować obiektu za pomocą zduplikowanego atrybutu, usługa Azure Active Directory "poddaje kwarantannie" zduplikowany atrybut, który naruszyłby ograniczenie unikatowości. Jeśli ten atrybut jest wymagany do inicjowania obsługi administracyjnej, takich jak UserPrincipalName, usługa przypisuje wartość zastępczą. Format tych wartości tymczasowych jest  
_**OriginalPrefix>+\<4DigitNumber>\@ \<InitialTenantDomain>.onmicrosoft.com . \<**_

Proces odporności atrybutów obsługuje tylko wartości UPN i SMTP **ProxyAddress.**

Jeśli atrybut nie jest wymagany, na przykład **ProxyAddress**, usługa Azure Active Directory po prostu poddaje kwarantannie atrybut konfliktu i przechodzi z utworzeniem lub aktualizacją obiektu.

Po quarantining atrybutu, informacje o konflikcie jest wysyłany w tym samym raporcie o błędzie wiadomości e-mail używane w starym zachowaniu. Jednak te informacje pojawiają się w raporcie o błędach tylko raz, gdy nastąpi kwarantanna, nie są nadal rejestrowane w przyszłych wiadomościach e-mail. Ponadto, ponieważ eksport dla tego obiektu zakończył się pomyślnie, klient synchronizacji nie rejestruje błędu i nie ponawia próby operacji tworzenia / aktualizacji po kolejnych cyklach synchronizacji.

Aby wesprzeć to zachowanie, do klas obiektów Użytkownik, Grupa i Kontakt dodano nowy atrybut:  
**DirSyncProvisioningErrors**

Jest to atrybut wielowartościowy, który jest używany do przechowywania sprzecznych atrybutów, które mogłyby naruszać ograniczenie unikatowości, jeśli zostaną dodane normalnie. Zadanie czasomierza w tle zostało włączone w usłudze Azure Active Directory, która uruchamia się co godzinę w celu wyszukywana konfliktów zduplikowanych atrybutów, które zostały rozwiązane, i automatycznie usuwa dane atrybuty z kwarantanny.

### <a name="enabling-duplicate-attribute-resiliency"></a>Włączanie odporności zduplikowanych atrybutów
Odporność zduplikowanych atrybutów będzie nowym zachowaniem domyślnym we wszystkich dzierżawach usługi Azure Active Directory. Będzie on domyślnie włączony dla wszystkich dzierżawców, którzy włączyli synchronizację po raz pierwszy 22 sierpnia 2016 r. lub później. Dzierżawcy, którzy włączyli synchronizację przed tą datą, będą mieli włączoną funkcję w partiach. To wdrożenie rozpocznie się we wrześniu 2016 r., a powiadomienie e-mail zostanie wysłane do kontaktu z powiadomieniem technicznym każdej dzierżawcy z określoną datą włączenia tej funkcji.

> [!NOTE]
> Po włączeniu odporności zduplikowanych atrybutów nie można go wyłączyć.

Aby sprawdzić, czy funkcja jest włączona dla dzierżawy, można to zrobić, pobierając najnowszą wersję modułu programu Azure Active Directory PowerShell i uruchamiając:

`Get-MsolDirSyncFeatures -Feature DuplicateUPNResiliency`

`Get-MsolDirSyncFeatures -Feature DuplicateProxyAddressResiliency`

> [!NOTE]
> Nie można już używać polecenia cmdlet Set-MsolDirSyncFeature, aby aktywnie włączyć funkcję funkcji zduplikowanej odporności atrybutów, zanim zostanie włączona dla dzierżawy. Aby móc przetestować tę funkcję, należy utworzyć nową dzierżawę usługi Azure Active Directory.

## <a name="identifying-objects-with-dirsyncprovisioningerrors"></a>Identyfikowanie obiektów z błędami DirSyncProvisioningErrors
Obecnie istnieją dwie metody identyfikowania obiektów, które mają te błędy z powodu konfliktów zduplikowanych właściwości, programu Azure Active Directory PowerShell i [centrum administracyjnego usługi Microsoft 365](https://admin.microsoft.com). W przyszłości planuje się rozszerzenie na dodatkowe raportowanie oparte na portalu.

### <a name="azure-active-directory-powershell"></a>Azure Active Directory PowerShell
W przypadku poleceń cmdlet programu PowerShell w tym temacie spełnione są następujące warunki:

* We wszystkich następujących poleceniach cmdlet rozróżniana jest wielkość liter.
* **—ErrorCategory PropertyConflict** zawsze musi być uwzględniony. Obecnie nie ma innych typów **kategorii błędów,** ale może to zostać rozszerzone w przyszłości.

Najpierw rozpocznij, uruchamiając **Connect-MsolService** i wprowadzając poświadczenia dla administratora dzierżawy.

Następnie użyj następujących poleceń cmdlet i operatorów, aby wyświetlić błędy na różne sposoby:

1. [Zobacz wszystkie](#see-all)
2. [Według typu właściwości](#by-property-type)
3. [Przez wartość powodującą konflikt](#by-conflicting-value)
4. [Korzystanie z wyszukiwania ciągów](#using-a-string-search)
5. Sortowane
6. [W ograniczonej ilości lub we wszystkich](#in-a-limited-quantity-or-all)

#### <a name="see-all"></a>Zobacz wszystko
Po połączeniu, aby wyświetlić ogólną listę błędów inicjowania obsługi administracyjnej atrybutów w przebiegu dzierżawy:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict`

Daje to wynik podobny do następującego:  
 ![Get-MsolDirSyncProvisioningError](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/1.png "Get-MsolDirSyncProvisioningError")  

#### <a name="by-property-type"></a>Według typu właściwości
Aby wyświetlić błędy według typu właściwości, dodaj flagę **-PropertyName** z **argumentem UserPrincipalName** lub **ProxyAddresses:**

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName UserPrincipalName`

Lub

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName ProxyAddresses`

#### <a name="by-conflicting-value"></a>Przez sprzeczną wartość
Aby zobaczyć błędy związane z określoną właściwością, dodaj flagę **-PropertyValue** (**-PropertyName** musi być również używana podczas dodawania tej flagi):

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyValue User@domain.com -PropertyName UserPrincipalName`

#### <a name="using-a-string-search"></a>Korzystanie z wyszukiwania ciągów
Aby wykonać wyszukiwanie w szerokim ciągu, użyj flagi **-SearchString.** Może to być używane niezależnie od wszystkich powyższych flag, z wyjątkiem **-ErrorCategory PropertyConflict**, który jest zawsze wymagany:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -SearchString User`

#### <a name="in-a-limited-quantity-or-all"></a>W ograniczonej ilości lub we wszystkich
1. **MaxResults \<Int>** może służyć do ograniczenia kwerendy do określonej liczby wartości.
2. **Wszystkie** mogą służyć do zapewnienia, że wszystkie wyniki są pobierane w przypadku, gdy istnieje duża liczba błędów.

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -MaxResults 5`

## <a name="microsoft-365-admin-center"></a>Centrum administracyjne platformy Microsoft 365
Błędy synchronizacji katalogów można wyświetlać w centrum administracyjnym usługi Microsoft 365. Raport w centrum administracyjnym usługi Microsoft 365 wyświetla tylko obiekty **użytkownika,** które mają te błędy. Nie pokazuje informacji o konfliktach między **grupami** i **kontaktami**.

![Aktywni użytkownicy](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/1234.png "Aktywni użytkownicy")

Aby uzyskać instrukcje dotyczące wyświetlania błędów synchronizacji katalogów w centrum administracyjnym usługi Microsoft 365, zobacz [Identyfikowanie błędów synchronizacji katalogów w usłudze Office 365](https://support.office.com/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067).

### <a name="identity-synchronization-error-report"></a>Raport o błędach synchronizacji tożsamości
Gdy obiekt z duplikatem konfliktu atrybutów jest obsługiwany z tym nowym zachowaniem powiadomienie jest zawarte w standardowej wiadomości e-mail raportu o błędach synchronizacji tożsamości, która jest wysyłana do kontaktu powiadomienia technicznego dla dzierżawy. Jednak istnieje istotna zmiana w tym zachowaniu. W przeszłości informacje o konflikcie zduplikowanych atrybutów były uwzględniane w każdym kolejnym raporcie o błędach, dopóki konflikt nie został rozwiązany. W przypadku tego nowego zachowania powiadomienie o błędzie dla danego konfliktu pojawia się tylko raz — w momencie kwarantanny atrybutu powodującego konflikt.

Oto przykład tego, jak wygląda powiadomienie e-mail dla konfliktu ProxyAddress:  
    ![Aktywni użytkownicy](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/6.png "Aktywni użytkownicy")  

## <a name="resolving-conflicts"></a>Rozwiązywanie konfliktów
Rozwiązywanie problemów strategii i rozwiązywania taktyk dla tych błędów nie powinny różnić się od sposobu zduplikowane błędy atrybutów były obsługiwane w przeszłości. Jedyną różnicą jest to, że zadanie czasomierza jest przeciągnięte przez dzierżawę po stronie usługi, aby automatycznie dodać dany atrybut do właściwego obiektu po rozwiązaniu konfliktu.

W poniższym artykule opisano różne strategie rozwiązywania problemów i rozwiązywania problemów: [Duplikaty lub nieprawidłowe atrybuty uniemożliwiają synchronizację katalogów w usłudze Office 365](https://support.microsoft.com/kb/2647098).

## <a name="known-issues"></a>Znane problemy
Żaden z tych znanych problemów powoduje utratę danych lub degradacji usługi. Niektóre z nich są estetyczne, inne powodują standardowe "*pre-resiliency*" zduplikowane błędy atrybutów, które mają być generowane zamiast quarantining atrybutu konfliktu, a inny powoduje, że niektóre błędy wymagają dodatkowych ręczne fix-up.

**Podstawowe zachowanie:**

1. Obiekty z określonymi konfiguracjami atrybutów nadal otrzymują błędy eksportu, w przeciwieństwie do duplikatów atrybutów poddawanych kwarantannie.  
   Przykład:
   
    a. Nowy użytkownik jest tworzony w contoso.com **\@** **\@contoso.com** u!
   
    b. Właściwości tego obiektu są w konflikcie z istniejącą grupą, gdzie proxyAddress jest **\@SMTP:Joe contoso.com**.
   
    d. Po wyeksportu błąd **konfliktu ProxyAddress** jest generowany zamiast konieczności kwarantanny atrybutów konfliktu. Operacja jest ponawiana przy każdym kolejnym cyklu synchronizacji, tak jak przed włączeniem funkcji odporności.
2. Jeśli dwie grupy są tworzone lokalnie z tym samym adresem SMTP, nie można aprowizować przy pierwszej próbie zduplikowanym błędem **proxy.** Jednak zduplikowana wartość jest poprawnie poddawana kwarantannie w następnym cyklu synchronizacji.

**Raport portalu pakietu Office**:

1. Szczegółowy komunikat o błędzie dla dwóch obiektów w zestawie konfliktów numerÓW 90 jest taki sam. Oznacza to, że obaj zmienili nazwę UPN / poddali kwarantannie, podczas gdy w rzeczywistości tylko jeden z nich miał jakiekolwiek dane zmienione.
2. Szczegółowy komunikat o błędzie konfliktu nazwy UPN pokazuje niewłaściwą nazwę wyświetlania użytkownika, którego nazwa upn została zmieniona/poddana kwarantannie. Przykład:
   
    a. **Użytkownik A** synchronizuje się najpierw z **upn\@= contoso.com użytkownika**.
   
    b. **Użytkownik B** próbuje zostać zsynchronizowany dalej z **upn\@= User contoso.com**.
   
    d. **Użytkownik B** Nazwa UPN została **zmieniona\@na User1234 contoso.onmicrosoft.com,** a **contoso.com\@użytkownika** jest dodawany do **DirSyncProvisioningErrors**.
   
    d. Komunikat o błędzie **dla użytkownika B** powinien wskazywać, że użytkownik **A** ma już **contoso.com użytkownika\@** jako nazwy UPN, ale pokazuje własną nazwa displayName użytkownika **B.**

**Raport o błędzie synchronizacji tożsamości:**

Łącze do *kroków dotyczących rozwiązywania tego problemu* jest niepoprawne:  
    ![Aktywni użytkownicy](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/6.png "Aktywni użytkownicy")  

Należy wskazać [https://aka.ms/duplicateattributeresiliency](https://aka.ms/duplicateattributeresiliency)na .

## <a name="see-also"></a>Zobacz też
* [Synchronizacja usługi Azure AD Connect](how-to-connect-sync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)
* [Identyfikowanie błędów synchronizacji katalogów w usłudze Office 365](https://support.office.com/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067)

