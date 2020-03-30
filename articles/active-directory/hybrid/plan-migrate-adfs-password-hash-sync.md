---
title: 'Usługa Azure AD Connect: migracja z federacji do usługi PHS dla usługi Azure AD | Dokumenty firmy Microsoft'
description: Ten artykuł zawiera informacje dotyczące przenoszenia środowiska tożsamości hybrydowej z federacji do synchronizacji skrótów haseł.
services: active-directory
author: billmath
manager: daveba
ms.reviewer: martincoetzer
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 05/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b621c9cbc35d0e9956f6648d870102affd84c24f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76028394"
---
# <a name="migrate-from-federation-to-password-hash-synchronization-for-azure-active-directory"></a>Migrowanie z synchronizacji haszyszu z federacją do hasła dla usługi Azure Active Directory

W tym artykule opisano sposób przenoszenia domen organizacji z usług federacyjnych Active Directory (AD FS) do synchronizacji skrótów haseł.

> [!NOTE]
> Zmiana metody uwierzytelniania wymaga planowania, testowania i potencjalnie przestojów. [Wdrożenie etapowe](how-to-connect-staged-rollout.md) zapewnia alternatywny sposób testowania i stopniowej migracji z federacji do uwierzytelniania w chmurze przy użyciu synchronizacji skrótów haseł.

## <a name="prerequisites-for-migrating-to-password-hash-synchronization"></a>Wymagania wstępne dotyczące migracji do synchronizacji skrótów haseł

Następujące wymagania wstępne są wymagane do migracji z korzystania z usług AD FS do przy użyciu synchronizacji skrótu hasła.

### <a name="update-azure-ad-connect"></a>Aktualizowanie usługi Azure AD Connect

Co najmniej, aby pomyślnie wykonać kroki migracji do synchronizacji skrótów haseł, powinien mieć [usługi Azure AD połączyć](https://www.microsoft.com/download/details.aspx?id=47594) 1.1.819.0. Ta wersja zawiera istotne zmiany w sposobie konwersji logowania jest wykonywana i skraca całkowity czas migracji z federacji do uwierzytelniania w chmurze z potencjalnie godzin do minut.


> [!IMPORTANT]
> W przestarzałej dokumentacji, narzędziach i blogach można przeczytać, że konwersja użytkownika jest wymagana podczas konwersji domen z tożsamości federacyjnej na tożsamość zarządzaną. *Konwersja użytkowników* nie jest już wymagana. Firma Microsoft pracuje nad aktualizacją dokumentacji i narzędzi w celu odzwierciedlenia tej zmiany.

Aby zaktualizować usługę Azure AD Connect, wykonaj kroki opisane w [usłudze Azure AD Connect: Uaktualnienie do najnowszej wersji](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version).

### <a name="password-hash-synchronization-required-permissions"></a>Wymagane uprawnienia do synchronizacji skrótu hasła

Usługę Azure AD Connect można skonfigurować przy użyciu ustawień ekspresowych lub instalacji niestandardowej. Jeśli użyto opcji instalacji niestandardowej, wymagane uprawnienia do synchronizacji [skrótów](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-accounts-permissions) haseł mogą nie być na miejscu.

Konto usługi Usług domenowych Active Directory usługi ad (AD DS) usługi Azure AD Connect wymaga następujących uprawnień do synchronizowania skrótów haseł:

* Replikowanie zmian w katalogu
* Wszystkie zmiany katalogu replikacji

Teraz jest dobry moment, aby sprawdzić, czy te uprawnienia są w miejscu dla wszystkich domen w lesie.

### <a name="plan-the-migration-method"></a>Planowanie metody migracji

Można wybrać jedną z dwóch metod migracji z zarządzania tożsamościami federacyjnych do synchronizacji skrótów haseł i bezproblemowego logowania jednokrotnego(SSO). Używana metoda zależy od oryginalnego skonfigurowania wystąpienia usług AD FS.

* **Azure AD Connect**. Jeśli pierwotnie skonfigurowano usługi AD FS przy użyciu usługi Azure AD Connect, *należy* zmienić synchronizację skrótów haseł przy użyciu kreatora usługi Azure AD Connect.

   Usługa Azure AD Connect automatycznie uruchamia polecenie cmdlet **Set-MsolDomainAuthentication** po zmianie metody logowania użytkownika. Usługa Azure AD Connect automatycznie unfederates wszystkie zweryfikowane federacyjne domeny w dzierżawie usługi Azure AD.

   > [!NOTE]
   > Obecnie jeśli pierwotnie użyto usługi Azure AD Connect do skonfigurowania usług AD FS, nie można uniknąć unfederating wszystkich domen w dzierżawie po zmianie logowania użytkownika do synchronizacji skrótu hasła. ‎
* **Usługa Azure AD Connect z programem PowerShell**. Tej metody można użyć tylko wtedy, gdy pierwotnie nie skonfigurowano usług AD FS przy użyciu usługi Azure AD Connect. W przypadku tej opcji nadal należy zmienić metodę logowania użytkownika za pomocą kreatora Usługi Azure AD Connect. Podstawową różnicą w tej opcji jest to, że kreator nie uruchamia automatycznie polecenia cmdlet **Set-MsolDomainAuthentication.** Dzięki tej opcji masz pełną kontrolę nad tym, które domeny są konwertowane i w jakiej kolejności.

Aby zrozumieć, której metody należy użyć, wykonaj kroki opisane w poniższych sekcjach.

#### <a name="verify-current-user-sign-in-settings"></a>Weryfikowanie bieżących ustawień logowania użytkownika

Aby zweryfikować bieżące ustawienia logowania użytkownika:

1. Zaloguj się do [portalu usługi Azure AD](https://aad.portal.azure.com/) przy użyciu konta administratora globalnego.
2. W sekcji **Logowanie użytkownika sprawdź** następujące ustawienia:
   * **Federacja** jest **ustawiona**na Włączone .
   * **Bezproblemowe logowanie jednokrotne** jest ustawione na **Wyłączone**.
   * **Uwierzytelnianie przekazywane** jest ustawione na **Wyłączone**.

   ![Zrzut ekranu przedstawiający ustawienia w sekcji logowania użytkownika usługi Azure AD Connect](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image1.png)

#### <a name="verify-the-azure-ad-connect-configuration"></a>Weryfikowanie konfiguracji usługi Azure AD Connect

1. Na serwerze usługi Azure AD Connect otwórz usługę Azure AD Connect. Wybierz pozycję **Konfiguruj**.
2. Na stronie **Zadania dodatkowe** wybierz pozycję **Wyświetl bieżącą konfigurację**, a następnie wybierz pozycję **Dalej**.<br />

   ![Zrzut ekranu przedstawiający opcję Wyświetl bieżącą konfigurację wybraną na stronie Zadania dodatkowe](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image2.png)<br />
3. Na stronie **Przejrzyj rozwiązanie** zwróć uwagę na stan **synchronizacji skrótu hasła.**<br /> 

   * Jeśli **synchronizacja skrótu hasła** jest ustawiona na **Wyłączona,** wykonaj kroki opisane w tym artykule, aby ją włączyć.
   * Jeśli **synchronizacja skrótu hasła** jest **ustawiona na Włączone,** można pominąć **sekcję Krok 1: Włącz synchronizację skrótów haseł** w tym artykule.
4. Na stronie **Przejrzyj rozwiązanie** przewiń do **usługi federacyjnej Active Directory (AD FS)**.<br />

   * Jeśli konfiguracja usług AD FS pojawi się w tej sekcji, można bezpiecznie założyć, że usługi AD FS został pierwotnie skonfigurowany przy użyciu usługi Azure AD Connect. Domeny można przekonwertować z tożsamości federacyjnej na tożsamość zarządzaną przy użyciu opcji logowania użytkownika usługi Azure AD Connect **Change.** Proces jest szczegółowo opisany w sekcji **Opcja A: Przełączanie z synchronizacji skrótów federacyjnych na synchronizację skrótów haseł przy użyciu usługi Azure AD Connect**.
   * Jeśli usług AD FS nie ma na liście w bieżących ustawieniach, należy ręcznie przekonwertować domeny z tożsamości federacyjnej na tożsamość zarządzaną przy użyciu programu PowerShell. Aby uzyskać więcej informacji na temat tego procesu, zobacz sekcję **Opcja B: Przełączanie z synchronizacji skrótów federacji na synchronizację haszyszu haseł przy użyciu usługi Azure AD Connect i programu PowerShell**.

### <a name="document-current-federation-settings"></a>Dokumentowanie bieżących ustawień federacji

Aby znaleźć bieżące ustawienia federacji, uruchom polecenie cmdlet **Get-MsolDomainFederationSettings:**

``` PowerShell
Get-MsolDomainFederationSettings -DomainName YourDomain.extention | fl *
```

Przykład:

``` PowerShell
Get-MsolDomainFederationSettings -DomainName Contoso.com | fl *
```

Sprawdź wszystkie ustawienia, które mogły zostać dostosowane do dokumentacji projektu i wdrożenia federacji. W szczególności poszukaj dostosowań w **PreferredAuthenticationProtocol**, **SupportsMfa**i **PromptLoginBehavior**.

Więcej informacji można znaleźć w tych artykułach:

* [Obsługa parametrów usługi AD FS prompt=login](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-prompt-login)
* [Set-MsolDomainAuthentication](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainauthentication?view=azureadps-1.0)

> [!NOTE]
> Jeśli **SupportsMfa** jest ustawiona na **True**, używasz lokalnego rozwiązania uwierzytelniania wieloskładnikowego, aby wstrzyknąć wyzwanie drugiego czynnika do przepływu uwierzytelniania użytkownika. Ta konfiguracja nie działa już w scenariuszach uwierzytelniania usługi Azure AD po przekonwertowaniu tej domeny z uwierzytelnienia federacyjnego na uwierzytelnianie zarządzane. Po wyłączeniu federacji należy zerwać relację z lokalną federacją, która obejmuje lokalne karty usługi MFA. 
>
> Zamiast tego użyj usługi opartej na uwierzytelniania wieloskładnikowego azure do wykonywania tej samej funkcji. Przed kontynuowaniem należy dokładnie ocenić wymagania dotyczące uwierzytelniania wieloskładnikowego. Przed przekonwertowanie domen, upewnij się, że rozumiesz, jak korzystać z usługi Azure uwierzytelnianie wieloskładnikowe, implikacje licencjonowania i procesu rejestracji użytkownika.

#### <a name="back-up-federation-settings"></a>Łań 17:1

Chociaż podczas procesów opisanych w tym artykule nie wprowadzono żadnych zmian w innych jednostkach uzależnień w farmie usług AD FS, zaleca się wykonanie bieżącej prawidłowej kopii zapasowej farmy usług AD FS, z której można przywrócić. Bieżącą prawidłową kopię zapasową można utworzyć za pomocą bezpłatnego [narzędzia szybkiego przywracania usług](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool)Microsoft AD FS . Za pomocą tego narzędzia można utworzyć kopię zapasową usług AD FS oraz przywrócić istniejącą farmę lub utworzyć nową farmę.

Jeśli użytkownik nie chce korzystać z narzędzia szybkiego przywracania usług AD FS, należy co najmniej wyeksportować zaufanie jednostki uzależnionej platformy tożsamości usługi Microsoft Office 365 i wszystkie dodane niestandardowe reguły oświadczeń. Można wyeksportować reguły zaufania jednostki uzależnione i skojarzone z nimi oświadczenia przy użyciu następującego przykładu programu PowerShell:

``` PowerShell
(Get-AdfsRelyingPartyTrust -Name "Microsoft Office 365 Identity Platform") | Export-CliXML "C:\temp\O365-RelyingPartyTrust.xml"
```

## <a name="deployment-considerations-and-using-ad-fs"></a>Zagadnienia dotyczące wdrażania i korzystania z usług AD FS

W tej sekcji opisano zagadnienia dotyczące wdrażania i szczegółowe informacje dotyczące korzystania z usług AD FS.

### <a name="current-ad-fs-use"></a>Bieżące użycie usług AD FS

Przed konwersją z tożsamości federacyjnej na tożsamość zarządzana przyjrzyj się uważnie, jak obecnie używasz usług AD FS dla usługi Azure AD, usługi Office 365 i innych aplikacji (relacji zaufania jednostki uzależnionej). W szczególności należy wziąć pod uwagę scenariusze, które są opisane w poniższej tabeli:

| Jeśli użytkownik | Następnie |
|-|-|
| Planujesz nadal używać usług AD FS z innymi aplikacjami (innymi niż usługi Azure AD i Office 365). | Po przekonwertowaniu domen użyjesz zarówno usług AD FS, jak i usługi Azure AD. Należy wziąć pod uwagę środowisko użytkownika. W niektórych scenariuszach użytkownicy mogą być zobowiązani do uwierzytelnienia dwa razy: raz do usługi Azure AD (gdzie użytkownik uzyskuje dostęp do drugiego użytkownika do innych aplikacji, takich jak Office 365) i ponownie dla wszystkich aplikacji, które są nadal powiązane z usługami AD FS jako zaufanie jednostki uzależnianej. |
| Wystąpienie usług AD FS jest mocno dostosowane i opiera się na określonych ustawieniach dostosowywania w pliku onload.js (na przykład, jeśli zmienisz środowisko logowania, tak aby użytkownicy używali tylko formatu **SamAccountName** dla swojej nazwy użytkownika zamiast głównej nazwy użytkownika (UPN) lub organizacja mocno oznaczyła środowisko logowania). Nie można zduplikować pliku onload.js w usłudze Azure AD. | Przed kontynuowaniem należy sprawdzić, czy usługa Azure AD może spełniać bieżące wymagania dotyczące dostosowywania. Aby uzyskać więcej informacji i uzyskać wskazówki, zobacz sekcje dotyczące znakowania usług AD FS i dostosowywania usług AD FS.|
| Za pomocą usług AD FS można blokować wcześniejsze wersje klientów uwierzytelniania.| Rozważ zastąpienie formantów usług AD FS, które blokują wcześniejsze wersje klientów uwierzytelniania przy użyciu kombinacji [formantów dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions) i [reguł dostępu klienta usługi Exchange Online](https://aka.ms/EXOCAR). |
| Wymaganie od użytkowników uwierzytelniania wieloskładnikowego w przypadku uwierzytelniania wieloskładnikowego w środowisku lokalnym, gdy użytkownicy uwierzytelniają się w u usług AD FS.| W domenie tożsamości zarządzanej nie można wstrzyknąć wyzwanie uwierzytelniania wieloskładnikowego za pośrednictwem lokalnego rozwiązania uwierzytelniania wieloskładnikowego do przepływu uwierzytelniania. Jednak po przekonwertowaniu domeny można użyć usługi Azure Multi-Factor Authentication do uwierzytelniania wieloskładnikowego.<br /><br /> Jeśli użytkownicy nie korzystają obecnie z uwierzytelniania wieloskładnikowego platformy Azure, wymagany jest jednorazowy krok rejestracji użytkownika. Należy przygotować się do planowanej rejestracji i przekazać je użytkownikom. |
| Obecnie używasz zasad kontroli dostępu (reguł AuthZ) w usług AD FS do kontrolowania dostępu do usługi Office 365.| Rozważ zastąpienie zasad równoważnymi [zasadami dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) usługi Azure AD i [regułami dostępu klienta usługi Exchange Online.](https://aka.ms/EXOCAR)|

### <a name="common-ad-fs-customizations"></a>Typowe dostosowania usług AD FS

W tej sekcji opisano typowe dostosowania usług AD FS.

#### <a name="insidecorporatenetwork-claim"></a>InsideCorporateSuferowanie sieciowe

Ad FS wystawia **insidecorporateNetwork** oświadczenia, jeśli użytkownik, który jest uwierzytelnianie znajduje się wewnątrz sieci firmowej. To oświadczenie można następnie przekazać do usługi Azure AD. Oświadczenie służy do pomijania uwierzytelniania wieloskładnikowego na podstawie lokalizacji sieciowej użytkownika. Aby dowiedzieć się, jak ustalić, czy ta funkcja jest obecnie włączona w systemach AD FS, zobacz [Zaufane adresy IP dla użytkowników federowanych](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud).

**Oświadczenie InsideCorporateNetwork** nie jest dostępne po przekonwertowaniu domen na synchronizację skrótów haseł. Aby zastąpić tę funkcję, można użyć [nazwanych lokalizacji w usłudze Azure AD.](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations)

Po skonfigurowaniu nazwanych lokalizacji należy zaktualizować wszystkie zasady dostępu warunkowego skonfigurowane tak, aby uwzględniały lub wykluczały sieć **Wszystkie zaufane lokalizacje** lub zaufane odpowiedzi **USŁUGI MFA wartości,** aby odzwierciedlić nowe nazwane lokalizacje.

Aby uzyskać więcej informacji o warunku **Lokalizacja** w programie Dostęp [warunkowy, zobacz Lokalizacje dostępu warunkowego usługi Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations).

#### <a name="hybrid-azure-ad-joined-devices"></a>Urządzenia z usługą Azure AD połączone hybrydowymi

Po dołączeniu urządzenia do usługi Azure AD można utworzyć reguły dostępu warunkowego, które wymuszają, że urządzenia spełniają standardy dostępu dla zabezpieczeń i zgodności. Ponadto użytkownicy mogą logować się do urządzenia przy użyciu konta służbowego lub służbowego zamiast konta osobistego. Korzystając z hybrydowych urządzeń przyłączonych do usługi Azure AD, możesz dołączyć do urządzeń przyłączonych do domeny usługi Active Directory do usługi Azure AD. Być może skonfigurowano środowisko federacyjne do korzystania z tej funkcji.

Aby upewnić się, że sprzężenie hybrydowe będzie nadal działać dla wszystkich urządzeń przyłączonych do domeny po przekonwertowaniu domen na synchronizację skrótów haseł, dla klientów systemu Windows 10 należy użyć opcji urządzenia usługi Azure AD Connect do synchronizacji komputera usługi Active Directory kont na platformie Azure AD. 

W przypadku kont komputerów z systemami Windows 8 i Windows 7 sprzężenie hybrydowe używa bezproblemowego logowania jednokrotnego do rejestrowania komputera w usłudze Azure AD. Nie musisz synchronizować kont komputerów z systemem Windows 8 i Windows 7, tak jak w przypadku urządzeń z systemem Windows 10. Należy jednak wdrożyć zaktualizowany plik workplacejoin.exe (za pośrednictwem pliku msi) na klientach systemu Windows 8 i Windows 7, aby mogli się zarejestrować przy użyciu bezproblemowego logowania jednokrotnego. [Pobierz plik msi](https://www.microsoft.com/download/details.aspx?id=53554).

Aby uzyskać więcej informacji, zobacz [Konfigurowanie hybrydowych urządzeń przyłączonych do usługi Azure AD](https://docs.microsoft.com/azure/active-directory/device-management-hybrid-azuread-joined-devices-setup).

#### <a name="branding"></a>Znakowanie

Jeśli twoja organizacja [dostosowała strony logowania](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization) usług AD FS do wyświetlania informacji, które są bardziej istotne dla organizacji, rozważ wprowadzenie podobnych [dostosowań do strony logowania usługi Azure AD](https://docs.microsoft.com/azure/active-directory/customize-branding).

Chociaż podobne dostosowania są dostępne, po konwersji należy spodziewać się pewnych zmian wizualnych na stronach logowania. Można podać użytkownikom informacje o oczekiwanych zmianach w komunikacji.

> [!NOTE]
> Znakowanie organizacji jest dostępne tylko wtedy, gdy zakupujesz licencję Premium lub Basic dla usługi Azure Active Directory lub jeśli masz licencję usługi Office 365.

## <a name="plan-deployment-and-support"></a>Planowanie wdrażania i wsparcia

Wykonaj zadania opisane w tej sekcji, aby ułatwić planowanie wdrażania i pomocy technicznej.

### <a name="plan-the-maintenance-window"></a>Zaplanuj okno konserwacji

Chociaż proces konwersji domeny jest stosunkowo szybki, usługa Azure AD może nadal wysyłać niektóre żądania uwierzytelniania do serwerów usług AD FS przez maksymalnie cztery godziny po zakończeniu konwersji domeny. W tym oknie cztery godziny i w zależności od różnych pamięci podręcznych po stronie usługi usługi Azure AD może nie akceptować tych uwierzytelniania. Użytkownicy mogą otrzymać błąd. Użytkownik nadal może pomyślnie uwierzytelniać się względem usług AD FS, ale usługa Azure AD nie akceptuje już wystawionego tokenu użytkownika, ponieważ zaufanie tej federacji zostało teraz usunięte.

Dotyczy to tylko użytkowników, którzy uzyskują dostęp do usług za pośrednictwem przeglądarki internetowej w tym oknie po konwersji, zanim zostanie wyczyszczona pamięć podręczna po stronie usługi. Nie oczekuje się, że mają to wpływ starsi klienci (Exchange ActiveSync, Outlook 2010/2013), ponieważ usługa Exchange Online przechowuje pamięć podręczną swoich poświadczeń przez określony czas. Pamięć podręczna służy do dyskretnego ponownego uwierzytelnienia użytkownika. Użytkownik nie musi zwracać się do usług AD FS. Poświadczenia przechowywane na urządzeniu dla tych klientów są używane do dyskretnego ponownego uwierzytelnienia się po tym buforowane jest wyczyszczone. Użytkownicy nie powinni otrzymywać żadnych monitów o hasło w wyniku procesu konwersji domeny. 

Nowi klienci uwierzytelniania (Office 2016 i Office 2013, iOS i Android apps) używają prawidłowego tokenu odświeżania, aby uzyskać nowe tokeny dostępu dla ciągłego dostępu do zasobów zamiast zwracać do usług AD FS. Klienci ci są odporni na wszelkie monity o hasło wynikające z procesu konwersji domeny. Klienci będą nadal działać bez dodatkowej konfiguracji.

> [!IMPORTANT]
> Nie zamykaj środowiska usług AD FS ani nie usuwaj zaufania jednostki uzależnianej usługi Office 365, dopóki nie zweryfikujesz, że wszyscy użytkownicy mogą pomyślnie uwierzytelniać się przy użyciu uwierzytelniania w chmurze.

### <a name="plan-for-rollback"></a>Planowanie wycofywania

Jeśli napotkasz poważny problem, którego nie możesz szybko rozwiązać, możesz zdecydować się na wycofanie rozwiązania do federacji. Należy zaplanować, co należy zrobić, jeśli wdrożenie nie zostanie wdrożone zgodnie z oczekiwaniami. Jeśli konwersja domeny lub użytkowników nie powiedzie się podczas wdrażania lub jeśli trzeba przywrócić do federacji, należy zrozumieć, jak ograniczyć wszelkie awarie i zmniejszyć wpływ na użytkowników.

#### <a name="to-roll-back"></a>Aby wycofać

Aby zaplanować wycofywanie, sprawdź dokumentację projektu federacji i wdrożenia, aby uzyskać szczegółowe informacje na temat wdrożenia. Proces ten powinien obejmować następujące zadania:

* Konwertowanie domen zarządzanych na domeny federacyjne przy użyciu polecenia cmdlet **Convert-MSOLDomainToFederated.**
* W razie potrzeby konfigurowanie reguł oświadczeń dodatkowych.

### <a name="plan-communications"></a>Planowanie komunikacji

Ważną częścią planowania wdrażania i pomocy technicznej jest zapewnienie, że użytkownicy są proaktywnie informowani o nadchodzących zmianach. Użytkownicy powinni wiedzieć z wyprzedzeniem, czego mogą doświadczyć i co jest od nich wymagane. 

Po wdrożeniu zarówno synchronizacji skrótów haseł, jak i bezproblemowego logowania jednokrotnego środowisko logowania użytkownika w celu uzyskania dostępu do usługi Office 365 i innych zasobów uwierzytelnionych za pomocą zmian usługi Azure AD. Użytkownicy spoza sieci widzą tylko stronę logowania usługi Azure AD. Ci użytkownicy nie są przekierowywani do strony opartej na formularzach, która jest prezentowana przez zewnętrzne serwery proxy aplikacji sieci Web.

Uwzględnij w strategii komunikacji następujące elementy:

* Powiadamiaj użytkowników o nadchodzących i wydanych funkcjach za pomocą:
   * Poczta e-mail i inne kanały komunikacji wewnętrznej.
   * Wizualizacje, takie jak plakaty.
   * Komunikacji wykonawczej, na żywo lub innej.
* Określ, kto i kto i kiedy będzie wysyłał komunikację.

## <a name="implement-your-solution"></a>Zaimplementuj swoje rozwiązanie

Zaplanowałeś rozwiązanie. Teraz możesz go zaimplementować. Implementacja obejmuje następujące składniki:

* Włączanie synchronizacji skrótów haseł.
* Przygotowanie do bezproblemowego logowania jednokrotnego.
* Zmiana metody logowania na synchronizację skrótów haseł i włączanie bezproblemowego logowania jednokrotnego.

### <a name="step-1-enable-password-hash-synchronization"></a>Krok 1: Włącz synchronizację skrótów haseł

Pierwszym krokiem do zaimplementowania tego rozwiązania jest włączenie synchronizacji skrótów haseł przy użyciu kreatora usługi Azure AD Connect. Synchronizacja skrótów haseł to opcjonalna funkcja, którą można włączyć w środowiskach korzystających z federacji. Nie ma wpływu na przepływ uwierzytelniania. W takim przypadku usługa Azure AD Connect rozpocznie synchronizowanie skrótów haseł bez wpływu na użytkowników, którzy logują się przy użyciu federacji.

Z tego powodu zaleca się wykonanie tego kroku jako zadania przygotowania na długo przed zmianą metody logowania domeny. Następnie będziesz mieć wystarczająco dużo czasu, aby sprawdzić, czy synchronizacja skrótów haseł działa poprawnie.

Aby włączyć synchronizację skrótów haseł:

1. Na serwerze usługi Azure AD Connect otwórz kreatora usługi Azure AD Connect, a następnie wybierz pozycję **Konfiguruj**.
2. Wybierz **pozycję Dostosuj opcje synchronizacji**, a następnie wybierz pozycję **Dalej**.
3. Na stronie **Połącz z usługą Azure AD** wprowadź nazwę użytkownika i hasło konta administratora globalnego.
4. Na stronie **Łączenie katalogów** wybierz pozycję **Dalej**.
5. Na stronie **Filtrowanie domeny i wy. organizacyjnej** wybierz pozycję **Dalej**.
6. Na stronie **Funkcje opcjonalne** wybierz pozycję **Synchronizacja haseł**, a następnie wybierz pozycję **Dalej**.
 
   ![Zrzut ekranu przedstawiający opcję synchronizacji haseł wybraną na stronie Funkcje opcjonalne](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image6.png)<br />
7. **Wybierz pozycję Dalej** na pozostałych stronach. Na ostatniej stronie wybierz pozycję **Konfiguruj**.
8. Usługa Azure AD Connect rozpoczyna synchronizację skrótów haseł przy następnej synchronizacji.

Po włączeniu synchronizacji skrótów haseł skróty haseł dla wszystkich użytkowników w zakresie synchronizacji usługi Azure AD Connect są ponownie zapisywane i zapisywane w usłudze Azure AD. W zależności od liczby użytkowników ta operacja może potrwać kilka minut lub kilka godzin.

Do celów planowania należy oszacować, że około 20 000 użytkowników jest przetwarzanych w ciągu 1 godziny.

Aby sprawdzić, czy synchronizacja skrótów haseł działa poprawnie, wykonaj zadanie **rozwiązywania problemów** w kreatorze usługi Azure AD Connect:

1. Otwórz nową sesję programu Windows PowerShell na serwerze usługi Azure AD Connect przy użyciu opcji Uruchom jako administrator.
2. Uruchom `Set-ExecutionPolicy RemoteSigned` `Set-ExecutionPolicy Unrestricted`lub .
3. Uruchom kreatora usługi Azure AD Connect.
4. Przejdź do strony **Dodatkowe zadania,** wybierz pozycję **Rozwiązywanie problemów,** a następnie wybierz pozycję **Dalej**.
5. Na stronie **Rozwiązywanie problemów** wybierz pozycję **Uruchom,** aby uruchomić menu rozwiązywania problemów w programie PowerShell.
6. W menu głównym wybierz pozycję **Rozwiązywanie problemów z synchronizacją skrótów haseł**.
7. W podmenu wybierz **opcję Synchronizacja skrótów haseł w ogóle nie działa**.

Aby rozwiązać problemy, zobacz [Rozwiązywanie problemów z synchronizacją skrótów haseł z synchronizacją usługi Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization).

### <a name="step-2-prepare-for-seamless-sso"></a>Krok 2: Przygotuj się na bezproblemowe logowanie jednokrotne

Aby urządzenia używały bezproblemowego logowania jednokrotnego, należy dodać adres URL usługi Azure AD do ustawień strefy intranetu użytkowników przy użyciu zasad grupy w usłudze Active Directory.

Domyślnie przeglądarki internetowe automatycznie obliczają prawidłową strefę, internetową lub intranet, z adresu URL. Na przykład **http:\/\/contoso/** mapuje do strefy intranetowej i **http:\/\/intranet.contoso.com** map do strefy internetowej (ponieważ adres URL zawiera kropkę). Przeglądarki wysyłają bilety Protokołu Kerberos do punktu końcowego chmury, takiego jak adres URL usługi Azure AD, tylko wtedy, gdy jawnie dodać adres URL do strefy intranetu przeglądarki.

Wykonaj kroki, aby [wprowadzić](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start) wymagane zmiany w urządzeniach.

> [!IMPORTANT]
> Wprowadzenie tej zmiany nie powoduje zmiany sposobu logowania użytkowników do usługi Azure AD. Jednak przed kontynuowaniem należy zastosować tę konfigurację do wszystkich urządzeń. Użytkownicy, którzy logują się na urządzeniach, które nie otrzymały tej konfiguracji, są po prostu zobowiązani do wprowadzenia nazwy użytkownika i hasła, aby zalogować się do usługi Azure AD.

### <a name="step-3-change-the-sign-in-method-to-password-hash-synchronization-and-enable-seamless-sso"></a>Krok 3: Zmień metodę logowania na synchronizację skrótów haseł i włącz bezproblemowe logowanie jednokrotne

Dostępne są dwie opcje zmiany metody logowania na synchronizację skrótów haseł i włączanie bezproblemowego logowania jednokrotnego.

#### <a name="option-a-switch-from-federation-to-password-hash-synchronization-by-using-azure-ad-connect"></a>Opcja A: Przełączanie z synchronizacji skrótów z federacją na hasło przy użyciu usługi Azure AD Connect

Tej metody należy użyć, jeśli początkowo skonfigurowano środowisko usług AD FS przy użyciu usługi Azure AD Connect. Tej metody nie można użyć, jeśli *środowisko* usług AD FS nie zostało pierwotnie skonfigurowane przy użyciu usługi Azure AD Connect.

Najpierw zmień metodę logowania:

1. Na serwerze usługi Azure AD Connect otwórz kreatora usługi Azure AD Connect.
2. Wybierz **pozycję Zmień logowanie użytkownika,** a następnie wybierz pozycję **Dalej**. 

   ![Zrzut ekranu przedstawiający opcję Zmienianie logowania użytkownika na stronie Zadania dodatkowe](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image7.png)<br />
3. Na stronie **Połącz z usługą Azure AD** wprowadź nazwę użytkownika i hasło konta administratora globalnego.
4. Na stronie **Logowanie użytkownika** wybierz **przycisk synchronizacji skrótu hasła**. Upewnij się, że zaznacz pole wyboru **Nie konwertuj kont użytkowników.** Opcja jest przestarzała. Wybierz **pozycję Włącz logowanie jednokrotne,** a następnie wybierz pozycję **Dalej**.

   ![Zrzut ekranu przedstawiający stronę Włącz logowanie jednokrotne](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image8.png)<br />

   > [!NOTE]
   > Począwszy od usługi Azure AD Connect w wersji 1.1.880.0, pole wyboru **Bezproblemowe logowanie jednokrotne** jest zaznaczone domyślnie.

   > [!IMPORTANT]
   > Można bezpiecznie zignorować ostrzeżenia, które wskazują, że konwersja użytkownika i pełna synchronizacja skrótów haseł są wymagane kroki do konwersji z federacji na uwierzytelnianie w chmurze. Należy pamiętać, że te kroki nie są już wymagane. Jeśli nadal widzisz te ostrzeżenia, upewnij się, że używasz najnowszej wersji usługi Azure AD Connect i że używasz najnowszej wersji tego przewodnika. Aby uzyskać więcej informacji, zobacz sekcję [Aktualizowanie usługi Azure AD Connect](#update-azure-ad-connect).

5. Na stronie **Włączanie logowania jednokrotnego** wprowadź poświadczenia konta Administratora domeny, a następnie wybierz pozycję **Dalej**.

   ![Zrzut ekranu przedstawiający stronę Włącz logowanie jednokrotne](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image9.png)<br />

   > [!NOTE]
   > Poświadczenia konta administratora domeny są wymagane, aby włączyć bezproblemowe logowanie jednokrotne. Proces kończy następujące akcje, które wymagają tych uprawnień podwyższonego poziomu. Poświadczenia konta administratora domeny nie są przechowywane w usłudze Azure AD Connect ani w usłudze Azure AD. Poświadczenia konta administratora domeny są używane tylko do włączania tej funkcji. Poświadczenia są odrzucane po pomyślnym zakończeniu procesu.
   >
   > 1. Konto komputera o nazwie AZUREADSSOACC (który reprezentuje usługę Azure AD) jest tworzony w lokalnym wystąpieniu usługi Active Directory.
   > 2. Klucz odszyfrowywania konta komputera Kerberos jest bezpiecznie współużytkowane z usługą Azure AD.
   > 3. Dwie nazwy główne usługi Kerberos (SPN) są tworzone w celu reprezentowania dwóch adresów URL, które są używane podczas logowania usługi Azure AD.

6. Na stronie **Gotowe do skonfigurowania** upewnij się, że pole wyboru **Rozpocznij proces synchronizacji po zakończeniu konfiguracji jest zaznaczone.** Następnie wybierz pozycję **Konfiguruj**.

      ![Zrzut ekranu przedstawiający stronę Gotowe do skonfigurowania](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image10.png)<br />

   > [!IMPORTANT]
   > W tym momencie wszystkie domeny federacyjne zmienią się w uwierzytelnianie zarządzane. Synchronizacja skrótów haseł to nowa metoda uwierzytelniania.

7. W portalu usługi Azure AD wybierz pozycję **Azure Active Directory** > **Azure AD Connect**.
8. Sprawdź następujące ustawienia:
   * **Federacja** jest ustawiona na **Wyłączona**.
   * **Bezproblemowe logowanie jednokrotne** jest ustawione na **Włączone**.
   * **Synchronizacja hasła** jest **ustawiona**na Włączone .<br /> 

   ![Zrzut ekranu przedstawiający ustawienia w sekcji Logowanie użytkownika](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image11.png)<br />

Przejdź do [pozycji Testowanie i kolejne kroki](#testing-and-next-steps).

   > [!IMPORTANT]
   > Pomiń sekcję **Opcja B: Przełączanie z federacji na synchronizację skrótów haseł przy użyciu usługi Azure AD Connect i programu PowerShell**. Kroki opisane w tej sekcji nie mają zastosowania, jeśli wybrano opcję A, aby zmienić metodę logowania na synchronizację skrótu hasła i włączyć bezproblemowe logowanie jednokrotne.

#### <a name="option-b-switch-from-federation-to-password-hash-synchronization-using-azure-ad-connect-and-powershell"></a>Opcja B: Przełączanie z synchronizacji skrótów haseł na hasło przy użyciu usługi Azure AD Connect i programu PowerShell

Użyj tej opcji, jeśli początkowo nie skonfigurowano domen federacyjnej przy użyciu usługi Azure AD Connect. Podczas tego procesu można włączyć bezproblemowe logowanie jednokrotne i przełączyć domeny z federacyjne do zarządzane.

1. Na serwerze usługi Azure AD Connect otwórz kreatora usługi Azure AD Connect.
2. Wybierz **pozycję Zmień logowanie użytkownika,** a następnie wybierz pozycję **Dalej**.
3. Na stronie **Połącz z usługą Azure AD** wprowadź nazwę użytkownika i hasło dla konta administratora globalnego.
4. Na stronie **Logowanie użytkownika** wybierz przycisk **synchronizacji skrótu hasła.** Wybierz **pozycję Włącz logowanie jednokrotne,** a następnie wybierz pozycję **Dalej**.

   Przed włączeniem synchronizacji skrótów haseł: ![Zrzut ekranu przedstawiający opcję Nie konfiguruj na stronie logowania użytkownika](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image12.png)<br />

   Po włączeniu synchronizacji skrótów haseł: ![zrzut ekranu przedstawiający nowe opcje na stronie Logowania użytkownika](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image13.png)<br />
   
   > [!NOTE]
   > Począwszy od usługi Azure AD Connect w wersji 1.1.880.0, pole wyboru **Bezproblemowe logowanie jednokrotne** jest zaznaczone domyślnie.

5. Na stronie **Włączanie logowania jednokrotnego** wprowadź poświadczenia konta administratora domeny, a następnie wybierz pozycję **Dalej**.

   > [!NOTE]
   > Poświadczenia konta administratora domeny są wymagane, aby włączyć bezproblemowe logowanie jednokrotne. Proces kończy następujące akcje, które wymagają tych uprawnień podwyższonego poziomu. Poświadczenia konta administratora domeny nie są przechowywane w usłudze Azure AD Connect ani w usłudze Azure AD. Poświadczenia konta administratora domeny są używane tylko do włączania tej funkcji. Poświadczenia są odrzucane po pomyślnym zakończeniu procesu.
   >
   > 1. Konto komputera o nazwie AZUREADSSOACC (który reprezentuje usługę Azure AD) jest tworzony w lokalnym wystąpieniu usługi Active Directory.
   > 2. Klucz odszyfrowywania konta komputera Kerberos jest bezpiecznie współużytkowane z usługą Azure AD.
   > 3. Dwie nazwy główne usługi Kerberos (SPN) są tworzone w celu reprezentowania dwóch adresów URL, które są używane podczas logowania usługi Azure AD.

6. Na stronie **Gotowe do skonfigurowania** upewnij się, że pole wyboru **Rozpocznij proces synchronizacji po zakończeniu konfiguracji jest zaznaczone.** Następnie wybierz pozycję **Konfiguruj**.

   ![Zrzut ekranu przedstawiający przycisk Konfiguruj na stronie Gotowe do skonfigurowania](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image15.png)<br />
   Po wybraniu przycisku **Konfiguruj bezproblemowe** logowanie jednokrotne jest skonfigurowane zgodnie z powyższym w poprzednim kroku. Konfiguracja synchronizacji skrótu hasła nie jest modyfikowana, ponieważ została włączona wcześniej.

   > [!IMPORTANT]
   > W tej chwili nie wprowadzono żadnych zmian w sposobie logowania użytkowników.

7. W portalu usługi Azure AD sprawdź następujące ustawienia:
   * **Federacja** jest **ustawiona**na Włączone .
   * **Bezproblemowe logowanie jednokrotne** jest ustawione na **Włączone**.
   * **Synchronizacja hasła** jest **ustawiona**na Włączone .

   ![Zrzut ekranu przedstawiający ustawienia w sekcji Logowanie użytkownika](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image16.png)

#### <a name="convert-domains-from-federated-to-managed"></a>Konwertowanie domen z federacyjnego na zarządzane

W tym momencie federacja jest nadal włączona i działa dla Twoich domen. Aby kontynuować wdrażanie, każda domena musi zostać przekonwertowana z federacyjnego na zarządzane, aby wymusić uwierzytelnianie użytkownika za pomocą synchronizacji skrótów haseł.

> [!IMPORTANT]
> Nie musisz konwertować wszystkich domen w tym samym czasie. Możesz rozpocząć od domeny testowej w dzierżawie produkcyjnej lub rozpocząć od domeny, która ma najniższą liczbę użytkowników.

Zakończ konwersję przy użyciu modułu programu Azure AD PowerShell:

1. W programie PowerShell zaloguj się do usługi Azure AD przy użyciu konta administratora globalnego.
2. Aby przekonwertować pierwszą domenę, uruchom następujące polecenie:

   ``` PowerShell
   Set-MsolDomainAuthentication -Authentication Managed -DomainName <domain name>
   ```

3. W portalu usługi Azure AD wybierz pozycję **Azure Active Directory** > **Azure AD Connect**.
4. Sprawdź, czy domena została przekonwertowana na zarządzane, uruchamiając następujące polecenie:

   ``` PowerShell
   Get-MsolDomain -DomainName <domain name>
   ```

## <a name="testing-and-next-steps"></a>Testowanie i kolejne kroki

Wykonaj następujące zadania, aby zweryfikować synchronizację skrótów haseł i zakończyć proces konwersji.

### <a name="test-authentication-by-using-password-hash-synchronization"></a>Testowanie uwierzytelniania przy użyciu synchronizacji skrótów haseł 

Gdy dzierżawa używała tożsamości federacyjnej, użytkownicy zostali przekierowani ze strony logowania usługi Azure AD do środowiska usług AD FS. Teraz, gdy dzierżawa jest skonfigurowana do używania synchronizacji skrótów haseł zamiast uwierzytelniania federacyjnego, użytkownicy nie są przekierowywani do usług AD FS. Zamiast tego użytkownicy logują się bezpośrednio na stronie logowania usługi Azure AD.

Aby przetestować synchronizację skrótów haseł:

1. Otwórz program Internet Explorer w trybie InPrivate, aby bezproblemowe logowanie jednokrotne nie loguje się automatycznie.
2. Przejdź do strony logowania do usługi Office[https://portal.office.com](https://portal.office.com/)365 ( ).
3. Wprowadź nazwy UPN użytkownika, a następnie wybierz pozycję **Dalej**. Upewnij się, że wprowadzono nazwy UPN użytkownika hybrydowego, który został zsynchronizowany z lokalnego wystąpienia usługi Active Directory i który wcześniej korzystał z uwierzytelniania federacyjnego. Zostanie wyświetlona strona, na której zostanie wprowadzona nazwa użytkownika i hasło:

   ![Zrzut ekranu przedstawiający stronę logowania, na której wprowadzasz nazwę użytkownika](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image18.png)

   ![Zrzut ekranu przedstawiający stronę logowania, na której wprowadzono hasło](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image19.png)

4. Po wprowadzeniu hasła i wybraniu opcji **Zaloguj się**zostaniesz przekierowany do portalu usługi Office 365.

   ![Zrzut ekranu przedstawiający portal usługi Office 365](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image20.png)


### <a name="test-seamless-sso"></a>Test bezszwowego logowania jednokrotnego

1. Zaloguj się do komputera przyłączonego do domeny, który jest połączony z siecią firmową.
2. W programie Internet Explorer lub Chrome przejdź do jednego z następujących adresów URL (zastąp domenę "contoso"):

   * https:\/\/myapps.microsoft.com/contoso.com
   * https:\/\/myapps.microsoft.com/contoso.onmicrosoft.com

   Użytkownik jest krótko przekierowywane do strony logowania usługi Azure AD, który zawiera komunikat "Próbuje się zalogować." Użytkownik nie jest monitowany o nazwę użytkownika lub hasło.<br />

   ![Zrzut ekranu przedstawiający stronę logowania i komunikat usługi Azure AD](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image21.png)<br />
3. Użytkownik jest przekierowywał i został pomyślnie zalogowany do panelu dostępu:

   > [!NOTE]
   > Bezproblemowe logowanie jednokrotne działa w usługach Office 365, które obsługują wskazówki dotyczące domeny (na przykład myapps.microsoft.com/contoso.com). Obecnie portal usługi Office 365 (portal.office.com) nie obsługuje wskazówek dotyczących domeny. Użytkownicy muszą wprowadzić upn. Po wprowadzeniu nazwy UPN bezproblemowe logowanie jednokrotne pobiera bilet Protokołu Kerberos w imieniu użytkownika. Użytkownik jest zalogowany bez wprowadzania hasła.

   > [!TIP]
   > Rozważ wdrożenie [sprzężenia hybrydowego usługi Azure AD w systemie Windows 10,](https://docs.microsoft.com/azure/active-directory/device-management-introduction) aby uzyskać lepsze środowisko aplikacji SSO.

### <a name="remove-the-relying-party-trust"></a>Usuwanie zaufania jednostki uzależniającej

Po sprawdzeniu, czy wszyscy użytkownicy i klienci są pomyślnie uwierzytelniania za pośrednictwem usługi Azure AD, można bezpiecznie usunąć zaufanie jednostki uzależniającej usługi Office 365.

Jeśli nie używasz usług AD FS do innych celów (to znaczy dla innych relacji zaufania jednostki uzależniającej), w tym momencie można bezpiecznie zlikwidować usługi AD FS.

### <a name="rollback"></a>Wycofywania

Jeśli odkryjesz poważny problem i nie możesz go szybko rozwiązać, możesz wycofać rozwiązanie do federacji.

Szczegółowe informacje na temat wdrażania można znaleźć w dokumentacji projektu i wdrożenia federacji. Proces ten powinien obejmować następujące zadania:

* Konwertuj domeny zarządzane na uwierzytelnianie federacyjne przy użyciu polecenia cmdlet **Convert-MSOLDomainToFederated.**
* W razie potrzeby skonfiguruj reguły oświadczeń dodatkowych.

### <a name="sync-userprincipalname-updates"></a>Synchronizowanie aktualizacji userPrincipalName

W przeszłości aktualizacje atrybutu **UserPrincipalName,** który korzysta z usługi synchronizacji ze środowiska lokalnego, są blokowane, chyba że oba te warunki są spełnione:

* Użytkownik znajduje się w zarządzanej (niefederowanej) domenie tożsamości.
* Użytkownikowi nie przypisano licencji.

Aby dowiedzieć się, jak zweryfikować lub włączyć tę funkcję, zobacz [Synchronizowanie aktualizacji userPrincipalName](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsyncservice-features).

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Zespół pomocy technicznej powinien zrozumieć, jak rozwiązać wszelkie problemy z uwierzytelnianiem, które pojawiają się podczas lub po zmianie z federacji na zarządzane. Skorzystaj z poniższej dokumentacji rozwiązywania problemów, aby pomóc zespołowi pomocy technicznej zapoznać się z typowymi krokami rozwiązywania problemów i odpowiednimi akcjami, które mogą pomóc w wyizolowaniu i rozwiązaniu problemu.

[Rozwiązywanie problemów z synchronizacją haszyszu haseł usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization)

[Rozwiązywanie problemów z bezproblemowym logowaniem jednokrotnym usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sso)

## <a name="roll-over-the-seamless-sso-kerberos-decryption-key"></a>Przejedrowuj bezszwowy klucz odszyfrowywania protokołu SSO Kerberos

Ważne jest, aby często przewracać klucz odszyfrowywania protokołu Kerberos konta komputera AZUREADSSOACC (który reprezentuje usługę Azure AD). Konto komputera AZUREADSSOACC jest tworzone w lokalnym lesie usługi Active Directory. Zdecydowanie zaleca się przewracanie klucza odszyfrowywania protokołu Kerberos co najmniej co 30 dni, aby dostosować się do sposobu, w jaki członkowie domeny usługi Active Directory przesyłają zmiany hasła. Nie ma skojarzonego urządzenia dołączonego do obiektu konta komputera AZUREADSSOACC, więc należy wykonać najazd ręcznie.

Inicjowanie narzucania bezproblemowego klucza odszyfrowywania protokołu SSO Kerberos na serwerze lokalnym z uruchomionym usługą Azure AD Connect.

Aby uzyskać więcej informacji, zobacz [Jak przerzucić klucz odszyfrowywania protokołu Kerberos na koncie komputera AZUREADSSOACC?](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-faq).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [pojęciach projektowych usługi Azure AD Connect](plan-connect-design-concepts.md).
* Wybierz [odpowiednie uwierzytelnianie](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn).
* Dowiedz się więcej o [obsługiwanych topologiach](plan-connect-design-concepts.md).
